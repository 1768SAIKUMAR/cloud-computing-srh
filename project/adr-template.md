# Architecture decision record (ADR) template

An ADR records one design decision: what you chose, what else you considered, and why. Keep each one under a page. When a decision changes later, don't edit the old ADR: write a new one that replaces it, and set the old one's status to "Replaced by ADR 00N".

Store your ADRs in your fork as `docs/adr/001-<slug>.md`, `docs/adr/002-<slug>.md`, and so on, where the slug is a few words in lower case with hyphens.

Copy everything between the lines below into a new file.

---

```markdown
# ADR 00N: <decision in a few words>

## Status
Proposed | Accepted | Replaced by ADR 00N

Date: YYYY-MM-DD

## Context
Your shipper and the targets this decision affects (freshness, reliability, budget).
What problem are you solving, and what evidence do you have (metrics, numbers, constraints)?

## Options considered
1. **Option A:** one or two lines on what it is.
2. **Option B:** ...
3. (more if you really considered them)

## Decision
We chose **Option X**, because ...

## Consequences
- **Cost:** what it costs per month, roughly, against the budget.
- **Freshness:** how fresh the data will be, against the target.
- **Complexity:** what you now have to build, run and monitor.
- **What gets harder:** what this choice makes more difficult later.
```

---

## Worked example

This one is about file formats, not your session 3 decision: use it to see the level of detail, not the answer.

```markdown
# ADR 002: Store raw files as Parquet, not CSV

## Status
Accepted

Date: 2026-11-18

## Context
Shipper: Greenleaf Foods (budget $30 per month). The generator writes one CSV per
day per dataset into the raw bucket. Analysts query the raw files directly from
BigQuery as external tables, and every query scans whole files.

## Options considered
1. **Keep CSV:** human-readable, no conversion step, but every query reads every column.
2. **Convert to Parquet:** columnar and compressed; queries read only the columns they use.
3. **Load everything into native BigQuery tables and drop the raw files:** fast to query, but no raw copy to reload from.

## Decision
We chose **Parquet**, with a small daily conversion job, because the raw files are
queried far more often than they are written, and keeping a raw copy lets us reload.

## Consequences
- **Cost:** storage drops, and queries scan fewer bytes; the conversion job adds a few
  cents a day.
- **Freshness:** a new day's file is queryable after the conversion runs, not as soon as it lands.
- **Complexity:** one more job to schedule and monitor.
- **What gets harder:** you can no longer open a raw file in a text editor to check it.
```
