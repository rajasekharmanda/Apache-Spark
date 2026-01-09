## **Apache Spark: The Mental Model**

This repository captures a **system-level understanding of Apache Spark** — how it actually plans, optimizes, and executes work in real data engineering pipelines.

Please Refer [Apache Spark - The Mental Model](ApacheSpark-TheMentalModel.md)

Instead of focusing on APIs or syntax, the content builds a **mental model** for Spark:

- how data is represented (RDD, DataFrame, Dataset)
- how Spark plans work lazily
- how jobs, stages, tasks, and partitions are created
- why joins and shuffles dominate performance
- how caching changes execution graphs
- how Spark fits into batch and streaming pipelines
- how the lakehouse pattern shapes modern architectures

The goal is to help engineers **reason about Spark behavior before writing code**, so performance, correctness, and design decisions are intentional — not accidental.

This repository is suited for:
- data engineers who want to move beyond “writing Spark jobs”
- backend engineers working with data platforms
- architects designing batch + streaming pipelines
- anyone who wants to understand _why_ Spark behaves the way it does

No vendor lock-in.  
No framework churn.  
Just the execution model that Spark actually follows.