# Architecture: Coastline Fashion (SHP06)

Adapted from the reference platform (`architecture/`). Coastline's targets: **freshness 30 minutes**, reliability **60% ocean / 90% air**, budget **$25 per month**, focus **cost**.

- **LIVE** = must meet the 30-minute freshness target.
- **BATCH** = runs on a schedule.
- ✗ grey dashed boxes = in the reference platform, but removed for this customer.

```mermaid
flowchart LR
  subgraph speed["Speed layer · LIVE (≤ 30 min)"]
    ev["Shipment events<br/>ocean + air carriers<br/>LIVE"] --> ps["Pub/Sub<br/>one topic, ocean + air<br/>LIVE"]
    ps --> live["BigQuery live tables<br/>via Pub/Sub BigQuery subscription<br/>LIVE"]
    live --> ops["Ops view<br/>where is it now? refresh every 15 min<br/>LIVE"]
    kafka["✗ Kafka on GKE: removed<br/>always-on cluster, over budget"]:::cut
  end
  subgraph batch["Batch layer · BATCH (daily)"]
    files["Rate + schedule files<br/>daily from carriers<br/>BATCH"] --> gcs["Cloud Storage raw zone<br/>BATCH"]
    gcs --> load["bq load, scheduled daily<br/>BATCH"]
    load --> cur["BigQuery curated tables<br/>BATCH"]
    cur --> exec["Exec view<br/>spend per lane<br/>BATCH"]
    cur --> rel["NEW: lane reliability report<br/>on-time % vs 60/90% target<br/>flags SGSIN→BEANR<br/>BATCH"]
    dp["✗ Dataproc (Spark): removed<br/>data too small, bq load is enough"]:::cut
  end
  live -. "same BigQuery = serving layer" .- cur
  classDef cut fill:#eee,stroke:#999,stroke-dasharray: 5 5,color:#999
```

## Changes from the reference platform

| Box | Change | Why (Coastline) |
|---|---|---|
| Kafka on GKE (ocean) | **Removed**: ocean and air events both go through Pub/Sub | An always-on Kubernetes cluster alone would use up most or all of a $25 budget; 30-minute freshness doesn't need it. |
| Dataflow (streaming) | **Replaced** by a Pub/Sub → BigQuery subscription | No always-on workers; events are written straight to a table. Add Dataflow only if we need event-time windows later. |
| Dataproc (Spark) | **Removed**; keep `bq load` | About 2 MB of files a month. Spark adds a cluster to run and pay for with no benefit at this size. |
| Ops view | **Refresh every 15 min**, not continuously | Meets 30 minutes with slack, and cuts dashboard query cost. |
| Lane reliability report | **Added** | Coastline's reliability target is per lane; SGSIN → BEANR already misses it (`docs/metrics.md`). |
| Databricks ML | **Later / optional** | Not needed for any of Coastline's three targets yet. |
| Underneath (Terraform, IAM, monitoring) | Kept | Needed for any design. |
