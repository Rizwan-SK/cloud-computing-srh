# ADR 001: Lightweight Lambda for the SHP05 platform

## Status
Accepted

Date: 2026-11-23

## Context
Shipper: **SHP05 Vertex Auto Parts**, a just-in-time car-parts supplier. Parts go by sea, and by air when a production line is about to stop.

Targets this decision affects:
- **Freshness:** 2 minutes
- **Reliability:** 65% on time on every ocean lane, 95% on every air lane
- **Budget:** $50 per month

Evidence from `docs/metrics.md` (October 2026, on time = at most 24 hours late):
- 3 of 4 ocean lanes miss 65%: SGSIN → BEANR 44.4%, NLRTM → USHOU 50.0%, CNNGB → DEHAM 60.0%.
- Air lane DXB → FRA misses 95% at 93.3%. The other two air lanes meet it.
- On the three failing ocean lanes, Atlas (ATL) was late on 6 of 6 shipments and Horizon (HRZ) was on time on 7 of 7.

The platform has to answer two different questions:
1. **Live:** is a shipment late right now, so someone can act before a line stops? This needs the 2-minute freshness.
2. **Historical:** which carriers keep missing the target over weeks, so procurement can renegotiate or move volume? This needs months of bookings and schedules, and a daily refresh is enough.

## Options considered
1. **Lambda (batch layer + speed layer):** daily files go through Cloud Storage into curated BigQuery tables, and shipment events go through Pub/Sub into a live BigQuery table. Each layer is queried separately.
2. **Kappa (one streaming pipeline):** all data, including history, flows through one event log, and reports are recomputed by replaying it. Replay needs a log that keeps history for a long time, which means Kafka or an always-on stream job. SHP05's history also arrives as daily files, so it would first have to be turned into a stream.

## Decision
We chose **Lambda, in a lightweight form**, because the two questions have different needs:
- The 2-minute freshness target applies to delay alerts, and only a speed layer can meet it.
- The carrier evidence needs months of history, which a daily batch layer handles cheaply. Kappa would force us to replay it.
- Kappa's replayable log needs always-on brokers or workers, which the $50 budget can't carry.

Compared with the reference platform:
- **Speed layer:** Pub/Sub → BigQuery subscription → BigQuery live → Ops view and alert. No Kafka and no Dataflow streaming job.
- **Batch layer:** Cloud Storage → scheduled BigQuery SQL → BigQuery curated → Exec view. No Dataproc.

## Consequences
- **Cost:** low, roughly a few dollars to low tens of dollars a month (Pub/Sub, BigQuery storage and queries, Cloud Storage), against the $50 budget. This is an estimate to check against the pricing calculator.
- **Freshness:** about 2 minutes or better on the live path, since a BigQuery subscription writes events directly. The Ops view must read the live table directly, because a cached dashboard could break the target.
- **Complexity:** two paths to build and monitor, plus a dead-letter topic and an alert. The "on time" rule (arrival at most 24 hours after plan) is defined once, in a shared view, and used by both layers.
- **What gets harder:** keeping live and batch numbers consistent, and handling duplicate or late events without distorting the live view. If event volume or budget grows, we would revisit Kappa.

## Cost estimate (assumes about 2 events/s, 400 bytes each, about 2 GB a month)
| Box | Estimate per month |
|---|---|
| Pub/Sub topic and dead-letter | $0 (inside the 10 GiB free tier) |
| BigQuery subscription | $0.10 to $0.25 ($50 per TiB) |
| BigQuery live storage | $0 (inside the 10 GiB free tier) |
| Cloud Storage raw bucket | under $0.05 |
| Scheduled SQL and curated tables | $0 (inside the 1 TiB free query tier) |
| Ops view alert | $0 to $1 |
| Dashboard queries | $0 if partitioned and filtered, about $30 if it rescans an unpartitioned table every refresh |
| **Total** | **about $1 to $3**, fits the $50 budget |

Prices from Google Cloud pricing pages for BigQuery and Pub/Sub, to be checked for europe-west3 in the pricing calculator. This is an estimate. The main risk is dashboard query volume, so the live table is partitioned by event time and the Ops view reads a small summary view. The crossed-out Kafka, Dataflow streaming and Dataproc would each bill hourly and could exceed the budget alone.
