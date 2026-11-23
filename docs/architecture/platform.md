# Platform architecture for SHP05 Vertex Auto Parts

Orange = live (must meet 2-minute freshness). Blue = batch (scheduled). Red dashed = crossed out.

```mermaid
flowchart LR
  subgraph Speed["Speed layer (LIVE, freshness 2 min)"]
    direction LR
    S1["Shipment events<br/>ocean + air"]:::live --> S2["Pub/Sub"]:::live
    S2 -.->|failed messages| S2D["Dead-letter topic<br/>ADDED"]:::live
    S2 --> S3["BigQuery subscription<br/>replaces Dataflow"]:::live
    S3 --> S4[("BigQuery live")]:::live
    S4 --> S5["Ops view<br/>air delays, at-risk shipments"]:::live
    S5 --> S6["Alert to procurement<br/>ADDED"]:::live
    SX1["Kafka"]:::crossed
    SX2["Dataflow streaming job"]:::crossed
  end

  subgraph Batch["Batch layer (daily)"]
    direction LR
    B1["Rate files"]:::batch --> B2["Cloud Storage<br/>raw bucket"]:::batch
    B2 --> B3["Scheduled BigQuery SQL<br/>replaces Dataproc"]:::batch
    B3 --> B4[("BigQuery curated")]:::batch
    B4 --> B5["Exec view<br/>delay per lane, savings"]:::batch
    BX1["Dataproc cluster"]:::crossed
  end

  classDef live fill:#e07b2e,stroke:#8a4a14,color:#000;
  classDef batch fill:#2f6fdb,stroke:#173f85,color:#fff;
  classDef crossed fill:#fff,stroke:#c00,stroke-dasharray:5 5,color:#c00;
```

## Decisions
- **Live:** events, Pub/Sub, BigQuery subscription, BigQuery live, Ops view, alert. The 2-minute freshness target and a possible line stop justify it.
- **Batch:** rate files, Cloud Storage, scheduled SQL, curated tables, Exec view. They change daily and feed negotiations, not alarms.
- **Crossed out:** Kafka, the Dataflow streaming job and the Dataproc cluster. Each is an always-on cost the $50 budget can't carry at this data volume.
- **Added:** dead-letter topic and alert.
- **Reliability:** the targets are 95% on air and 65% on ocean. The Ops view tracks both, but only air delays trigger an immediate alert. Ocean is reviewed daily in the Exec view.