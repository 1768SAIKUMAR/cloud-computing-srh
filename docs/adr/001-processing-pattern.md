# ADR 001: Lambda with a thin, serverless speed layer

## Status
Proposed

Date: 2026-11-20

## Context
Launch customer: **SHP06 Coastline Fashion**, a fashion brand with thin margins (focus: cost). It ships mostly by sea and flies only fast-selling items.

Targets:
- **Freshness:** the ops view may be at most **30 minutes** behind reality.
- **Reliability:** **60% on time** on every ocean lane, **90%** on every air lane.
- **Budget:** **$25 per month** for cloud resources.

Evidence (`docs/metrics.md`, October 2026): 6 of 7 lanes meet the target. **SGSIN → BEANR misses it: 50% on time, about 50 hours average delay**, with the late shipments on ATL and ORS. Ocean lanes average 1–2 days late, air lanes under 3 hours.

The data arrives in two shapes: **rate and schedule files once a day**, and **shipment events all day**. Ops needs the events within 30 minutes to re-plan stock when a ship slips; the reliability and spend reports only need daily data. Total data is small (about 2 MB of files per month).

## Options considered
1. **Lambda (reference platform as is):** daily batch layer for files, speed layer with Kafka on GKE (ocean) and Pub/Sub (air) into a streaming Dataflow job, meeting in BigQuery. Fresh and complete, but an always-on GKE cluster and streaming Dataflow workers cost more than the whole $25 budget, and the logic for delay lives in two places.
2. **Kappa:** everything is an event in one durable log; one streaming job computes everything, and we replay the log to rebuild. One codebase, but rate files would have to be turned into events, the log needs long retention, and the job runs all the time: the most expensive option for a 30-minute target.
3. **Lambda with a thin, serverless speed layer:** keep the daily batch layer (files → Cloud Storage → `bq load` → curated tables). For events, use Pub/Sub with a BigQuery subscription that writes each event straight into a live table, with no Kafka and no always-on Dataflow job. Both paths meet in BigQuery.
4. **Batch only (micro-batch every 15 minutes):** cheapest, and 15 minutes meets 30, but events would wait in files between runs and a failed run could break the freshness target.

## Decision
We chose **Option 3: Lambda with a thin, serverless speed layer**, because:
- Coastline's sources are **mixed** (daily files and live events), which is the case Lambda fits (slide 11).
- **30 minutes is not real-time.** Seconds-level streaming (Kafka, streaming Dataflow) is over-built for it; a Pub/Sub → BigQuery subscription delivers within seconds anyway.
- The customer's focus is **cost**, and nothing in this design runs all the time except Pub/Sub, which is billed by data volume.
- The reliability target is a **batch metric** (on-time % per lane per month), so the batch layer is where it belongs.

## Consequences
- **Cost:** storage and queries for a few MB stay inside BigQuery's and Cloud Storage's free tiers or close to it; Pub/Sub is billed by volume, which is small. Expected well under $25 per month. To be checked box by box with the Google Cloud pricing calculator (stretch).
- **Freshness:** events reach the live table within seconds of arriving in Pub/Sub; the ops view refreshes every 15 minutes, leaving 15 minutes of slack against the 30-minute target for failures and late events.
- **Complexity:** two paths to keep consistent: "delay" must be computed the same way in the live table and in the curated tables (one shared SQL definition, `delay_per_lane.sql`).
- **What gets harder:**
  - No event-time windows or de-duplication in the speed layer. If carriers' events arrive hours late or twice, we'd need Dataflow (with event-time windows and watermarks, session 7), which brings back always-on cost.
  - Replaying history is harder than in Kappa: we rely on the raw bucket and Pub/Sub retention, not a long-lived log.
  - If Coastline later asks for seconds-level freshness, this ADR is replaced.
- **Follow-up:** the reliability report must flag **SGSIN → BEANR**. Moving its bookings from ATL to KST or BLC is a business decision for Coastline, but the platform has to show the evidence.
