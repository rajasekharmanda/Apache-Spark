## **Topic 10 - Spark in real Data Engineering pipelines (batch + streaming)**

> Spark is rarely alone in the wild.  It lives inside **pipelines**—scheduled, monitored, and judged by SLAs.

Spark is **rarely the whole pipeline**.
Spark is usually:
- the **heavy compute engine**
- sitting **between storage and serving**
- doing work that is too big, too slow, or too complex for simple tools
---
![](images/10.ApacheSparkindatapipelinesarchitecture.png)
---
## Real-world ETL pipeline layers (mental model)

A typical modern data pipeline has **four layers**:
1. **Ingestion** (databases, files, object storage, ...)
2. **Processing / Transform (Spark lives here)** (cleaning, joins, aggregations, enrichment, ...)
3. **Storage**
4. **Serving / Consumption** (data warehouse, lakehouse, downstream apps, ...)

Spark plugs into **processing**, sometimes touching ingestion, sometimes touching serving — but it is not everything.

---
### Where Spark usually runs

Spark jobs are typically:
- scheduled
- parameterized
- repeatable

They’re often orchestrated by:
- Apache Airflow
- cron (simple but savage)
- managed schedulers

Spark doesn’t decide _when_ it runs.  
It decides _how_ the data moves once it does.

---
## Spark in **Batch pipelines** (the classic use)

### What batch Spark is used for

Batch Spark is used for:
- daily ETL jobs
- backfills
- recomputations
- heavy joins
- historical analytics
- data is large
- latency can be minutes or hours
- transformations are complex
- joins are unavoidable

Strengths:
- stable
- predictable
- scalable
- easy to reason about

Typical batch inputs:
- S3 / ADLS / GCS
- HDFS
- Historical Kafka dumps
- Daily/hourly files

Typical batch outputs:
- Parquet / Delta / Iceberg tables
- Aggregated fact tables
- Feature tables
- Data warehouse loads

```scss
Raw data (files / logs / CDC)
        ↓
Ingestion (Kafka Connect / Airflow jobs)
        ↓
Spark batch job
  - clean
  - dedupe
  - join
  - enrich
  - aggregate
        ↓
Curated tables (lakehouse)
        ↓
BI / ML / downstream services
```

Where Spark adds value:
- multi-table joins
- heavy aggregations
- schema evolution handling
- historical backfills
- reprocessing months/years of data

Batch Spark is about **correctness + scale**, not immediacy.

---
## Spark in **Streaming pipelines** (where confusion starts)

Spark Streaming (Structured Streaming) is **not** “real-time” in the strict sense.

It is:
- **micro-batch processing**
- with continuous execution semantics
- trading latency for reliability and exactly-once guarantees

Latency is usually:
- seconds
- sometimes tens of seconds
- almost never milliseconds

Streaming Spark is used when:
- data arrives continuously
- logic is stateful
- joins and aggregations matter
- correctness > raw speed

Typical streaming inputs:
- Kafka
- Event Hubs

Typical streaming outputs:
- Delta / Iceberg tables
- Aggregated metrics
- Downstream Kafka topics

```scss
Events (Kafka / Event Hubs)
        ↓
Spark Structured Streaming
  - parse
  - validate
  - enrich
  - window
  - aggregate
  - manage state
        ↓
Curated streaming tables
        ↓
Dashboards / alerts / ML features

```

Where Spark adds value::
- stateful processing
- windowed aggregations
- stream–stream joins
- stream–batch joins
- exactly-once semantics

Spark streaming is about **continuous correctness**, not ultra-low latency.

---
## The batch + streaming convergence (this is important)

Modern pipelines **do not separate batch and streaming mentally** anymore.

Spark enables a **unified model**:
- Same code
- Same transformations
- Same sinks
- Different execution mode

This is the lakehouse idea in practice.

Example:
- Streaming job writes hourly aggregates
- Batch job backfills the same table
- Same schema
- Same logic
- Same downstream consumers
---
### The lakehouse pattern (modern default)
> The lakehouse is an architecture that combines the openness and scale of a data lake with the reliability and governance of a data warehouse.

Modern stacks look like:
- raw data lands in object storage
- Spark transforms it
- data stored as optimized columnar formats
- queried by analytics engines

Spark:
- writes clean, structured data
- enforces schemas
- handles backfills gracefully

This is why Spark pairs so well with data lakes.

```scss

Sources (apps, logs, CDC, events)
              ↓
Ingestion (batch + streaming)
              ↓
Bronze tables (raw, append-only)
              ↓
Silver tables (cleaned, deduped)
              ↓
Gold tables (aggregated, business-ready)
              ↓
BI / ML / APIs

```

## The mental model that actually sticks

Here it is — the one worth remembering:

> **The lakehouse is not a tool.  
> It’s a contract between storage and compute.**