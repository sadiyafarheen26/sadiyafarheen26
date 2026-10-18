# 🏗️ Modern Data Engineering Architectures & Pipeline Patterns on Google Cloud

## 🎯 The Challenge / Scenario
Modern organizations struggle to unify massive volumes of heterogeneous data scattered across on-premises databases, SaaS tools, and cloud storage into reliable analytical stores. Engineering teams must evaluate trade-offs between compute overhead, schema flexibility, latency, and costs when designing data ingestion systems. This project examines enterprise-grade data engineering patterns—ranging from database migration and change data capture (CDC) to EL, ELT, and ETL processing architectures automated through cloud-native orchestration.

---

## 🧰 Tech Stack & Skills Learned
* **Storage & Warehousing:** Google Cloud Storage (GCS), BigQuery
* **Processing & Transformation:** Cloud Dataflow (Apache Beam), Cloud Dataproc (Managed Spark/Hadoop)
* **Replication & Ingestion:** Database Migration Service (DMS), Datastream (Serverless Change Data Capture), Cloud Pub/Sub
* **Orchestration & Workflow Automation:** Cloud Composer (Managed Apache Airflow), Cloud Scheduler
* **Architecture Paradigms:** Extract & Load (EL), Extract-Load-Transform (ELT), Extract-Transform-Load (ETL), Batch vs. Stream processing

---

## 🚀 Key Milestones & Architecture

```
                    ┌────────────────────────┐
                    │  Sources (OLTP / Logs) │
                    └───────────┬────────────┘
                                │
        ┌───────────────────────┼──────────────────────┐
        ▼ (EL Pattern)          ▼ (ETL Pattern)        ▼ (ELT Pattern)
┌──────────────┐        ┌──────────────┐       ┌──────────────┐
│ Cloud Storage│        │Cloud Dataflow│       │ Cloud Storage│
│ (Raw Lake)   │        │(Apache Beam) │       │ (Raw Staging)│
└───────┬──────┘        └───────┬──────┘       └───────┬──────┘
        │                       │                      │
        │ (Bulk Load)           │ (Stream/Pre-process) │ (Direct Load)
        ▼                       ▼                      ▼
┌─────────────────────────────────────────────────────────────┐
│                      Google BigQuery                        │
│            (Analytical Warehouse & SQL Engine)              │
└─────────────────────────────────────────────────────────────┘
                                ▲
                                │ (Orchestration & Triggers)
                    ┌───────────┴────────────┐
                    │     Cloud Composer     │
                    │    (Apache Airflow)    │
                    └────────────────────────┘
```

### 1. Ingestion Strategy & Database Replication
* **What was configured:** Designed data migration and continuous ingestion pipelines using Datastream and Database Migration Service (DMS).
* **How it works:** Leveraged Change Data Capture (CDC) to stream transaction logs from operational databases (e.g., MySQL, PostgreSQL) directly into Cloud Storage or BigQuery with minimal impact on source transactional systems.
* **Impact & Validation:** Enabled real-time operational reporting without relying on high-impact periodic batch dumps.

### 2. Multi-Pattern Data Pipeline Design (EL, ELT, ETL)
* **What was configured:** Evaluated and implemented pipelines across three core enterprise integration patterns:
  * **EL (Extract & Load):** Ingesting raw unstructured/semi-structured files straight into Cloud Storage for archival and raw data lake consumption.
  * **ELT (Extract, Load, Transform):** Ingesting high-velocity structured logs directly into BigQuery, pushing the computational transformation workload onto BigQuery's scalable SQL engine.
  * **ETL (Extract, Transform, Load):** Processing sensitive or malformed data through Cloud Dataflow (Apache Beam) to clean, mask PII, and aggregate data in-flight before persisting to target warehouses.
* **Impact & Validation:** Established clear selection criteria for compute choices based on regulatory constraints, budget limitations, and query response times.

### 3. Workflow Automation & Pipeline Orchestration
* **What was configured:** End-to-end task automation utilizing Cloud Composer (managed Apache Airflow) and event-driven triggers.
* **How it works:** Programmed Directed Acyclic Graphs (DAGs) to monitor incoming files in Cloud Storage buckets, trigger downstream Dataflow and BigQuery SQL transformation jobs, and alert on pipeline failures.
* **Impact & Validation:** Eliminated manual operational runs, enforced pipeline idempotency, and ensured data freshness SLAs were consistently met.

---

## 📊 Business Logic / Rules Implemented

### Pipeline Pattern Selection Matrix

| Pattern | Target Workload | Primary GCP Services | Key Engineering Trade-Off |
| :--- | :--- | :--- | :--- |
| **EL** | Data archiving, raw data lake landing | GCS, BigQuery transfer service | Lowest ingestion compute cost; raw data requires later transformation. |
| **ELT** | Structured/semi-structured analytics with fast turnaround | BigQuery, Cloud Storage | Maximizes warehouse compute power via SQL; storage and query costs scale with data scale. |
| **ETL** | Complex transformations, PII sanitization, streaming metrics | Dataflow (Beam), Dataproc (Spark) | Requires upfront pipeline engineering and compute costs, but yields production-ready, clean data. |

---

## 🔑 Key Takeaways & Best Practices
* **Decouple Storage and Compute:** Persisting immutable, raw source data in Cloud Storage protects against pipeline regressions, enabling downstream re-processing without source re-extraction.
* **Default to ELT for Warehousing:** Modern analytical databases like BigQuery execute aggregate and window transformations significantly faster and cheaper than custom middleware for structured schemas.
* **Isolate Sensitive Data Early:** Where compliance rules (e.g., GDPR, HIPAA) mandate data masking, adopt the **ETL** pattern to sanitize records via Dataflow prior to hitting analytical lakes or shared storage.
* **Build for Idempotency:** Production DAGs orchestrated through Airflow/Cloud Composer must support backfilling and retries without creating duplicate records in analytical tables.