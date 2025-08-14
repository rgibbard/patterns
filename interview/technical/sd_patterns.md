# 🏗️ System Design Patterns: Data Engineering

A modular guide to designing scalable, reliable, and maintainable data systems. Includes recognition cues, design patterns, tradeoffs, and annotated templates.



## 🧱 Batch vs Streaming Architecture

**Recognize When:**
- “Design a data pipeline for real-time analytics”
- “How would you process large volumes of historical data?”

**Design Pattern:**

| Use Case     | Batch                          | Streaming                        |
|--------------|--------------------------------|----------------------------------|
| Latency      | Minutes to hours               | Seconds to milliseconds          |
| Tools        | Spark, Airflow, dbt            | Kafka, Flink, Spark Streaming    |
| Storage      | Data Lake, Warehouse           | Kafka topics, real-time stores   |
| Complexity   | Lower operational overhead     | Higher coordination and tuning   |

**Tradeoffs:**
- Streaming offers freshness but increases complexity  
- Batch is simpler and cost-effective for non-real-time needs


## 🧮 Data Modeling & Storage

**Recognize When:**
- “How would you store user activity logs?”
- “Design a schema for a multi-tenant analytics platform”

**Design Pattern:**
- Dimensional Modeling (Star/Snowflake)
- Wide Tables for denormalized access
- Hybrid: Semi-denormalized with only essential dimension tables
- Partitioning & Clustering for performance
- Lakehouse Architecture (Delta, Iceberg, Hudi)

**Tradeoffs:**
- Normalized models reduce redundancy but increase join cost  
- Denormalized models improve performance but risk duplication


## 🔄 ETL vs ELT

**Recognize When:**
- “Design a pipeline to ingest and transform data from multiple sources”

**Design Pattern:**

| Approach | ETL (Transform before Load) | ELT (Transform after Load) |
|----------|-----------------------------|----------------------------|
| Tools    | Informatica, Talend, custom scripts | dbt, Spark SQL, BigQuery |
| Flexibility | Lower (fixed schema upfront) | Higher (schema-on-read)   |
| Storage Cost | Lower (pre-cleaned data) | Higher (raw data retained) |

**Tradeoffs:**
- ELT is preferred in modern cloud-native setups  
- ETL may be better for compliance or legacy systems


## 🧠 Orchestration & Dependency Management

**Recognize When:**
- “How do you ensure pipeline reliability?”
- “Design a system that handles task dependencies and retries”

**Design Pattern:**
- DAGs (Airflow, Prefect)
- Event-Driven Triggers (Kafka, AWS Lambda)
- Retry & Alerting (Exponential backoff, DLQs)

**Tradeoffs:**
- DAGs offer visibility but can become brittle  
- Event-driven systems scale better but are harder to debug


## 🛡️ Data Quality & Observability

**Recognize When:**
- “How do you detect and handle bad data?”
- “Design a system to monitor pipeline health”

**Design Pattern:**
- Validation Checks (nulls, ranges, schema drift)
- Data Contracts (schema enforcement)
- Monitoring Tools (Great Expectations, Monte Carlo)

**Tradeoffs:**
- Strict validation prevents bad data but may block ingestion  
- Loose validation allows flexibility but risks silent corruption


## 🧮 Scalability & Cost Optimization

**Recognize When:**
- “Design a pipeline that handles billions of events per day”
- “How do you optimize for cost in cloud data processing?”

**Design Pattern:**
- Partitioning & Bucketing
- Caching & Materialized Views
- Auto-scaling & Spot Instances
- Columnar Storage (Parquet, ORC)

**Tradeoffs:**
- Aggressive optimization may reduce flexibility  
- Over-partitioning can hurt performance


## 🏢 Multi-Tenant Architecture

**Recognize When:**
- “Design a platform for multiple clients or teams”
- “How do you isolate data and compute across tenants?”

**Design Pattern:**
- Namespace Isolation (schemas, folders, buckets)
- Row-Level Security or Column Masking
- Metadata tagging for tenant lineage
- Resource Quotas and Usage Tracking

**Tradeoffs:**
- Full isolation improves security but increases cost  
- Shared infrastructure reduces overhead but risks noisy neighbors


## 🧬 Data Lineage & Governance

**Recognize When:**
- “How do you track where data came from?”
- “Design a system for auditing and compliance”

**Design Pattern:**
- Lineage Tracking Tools (OpenLineage, Marquez, Unity Catalog)
- Metadata Propagation via Orchestration
- Versioning & Audit Logs
- Column-Level Lineage for sensitive fields

**Tradeoffs:**
- Deep lineage improves trust but adds metadata overhead  
- Shallow lineage is easier but less informative


## 🚨 Real-Time Alerting Systems

**Recognize When:**
- “How do you detect pipeline failures or anomalies in real time?”

**Design Pattern:**
- Metric Thresholds (latency, volume, error rate)
- Anomaly Detection (Z-score, time-series models)
- Alert Routing (PagerDuty, Slack, email)
- Dead Letter Queues for failed events

**Tradeoffs:**
- Real-time alerts reduce downtime but risk false positives  
- Batch alerts are simpler but slower to respond


## 🧰 Preparation Tips

### 🧠 1. Master Core Patterns
- Internalize tradeoffs between batch vs streaming, ETL vs ELT, and modeling strategies
- Use this cheatsheet to map patterns to use cases quickly

### 📚 2. Build a Design Prompt Bank
- Collect 10–15 common system design questions
- Tag each with relevant patterns (e.g., orchestration + lineage)

### 🗣️ 3. Practice Out Loud
- Use whiteboard or markdown to sketch architecture
- Explain decisions, tradeoffs, and failure modes clearly

### 🧭 4. Prioritize Constraints
- Always ask about scale, latency, SLAs, and data freshness
- Tailor your design to meet specific business goals

### 🧱 5. Layer Your Design
- Start with high-level architecture, then drill into ingestion, storage, processing, and observability
- Use modular blocks: ingestion → transformation → storage → access → monitoring

### 🔍 6. Annotate Your Diagrams
- Label components with purpose, tools, and failure handling
- Highlight bottlenecks, retries, and lineage paths

### 🧑‍🏫 7. Teach It Back
- Practice explaining your design to a junior engineer or non-technical stakeholder
- Focus on clarity, modularity, and real-world applicability

<br>

# 🧭 System Design Promps

A set of reusable prompts and annotated response templates


## 🔄 Prompt: Design a Real-Time Analytics Pipeline

**Use Cases:** Clickstream, IoT, fraud detection  
**Patterns:** Streaming architecture, ELT, observability

**Response Template:**
- Ingestion: Kafka → Spark Structured Streaming  
- Transformation: Stateless + windowed aggregations  
- Storage: Real-time store (e.g., Druid, Pinot)  
- Access: Dashboard or API  
- Monitoring: Lag metrics, schema drift alerts  
- Tradeoffs: Low latency vs complexity, schema evolution


## 🧬 Prompt: Design a Data Lineage System

**Use Cases:** Compliance, auditing, debugging  
**Patterns:** Metadata propagation, orchestration, governance

**Response Template:**
- Capture lineage via Airflow DAGs or Spark jobs  
- Store metadata in Marquez or Unity Catalog  
- Expose lineage via UI or API  
- Version datasets and transformations  
- Tradeoffs: Metadata overhead vs auditability


## 🏢 Prompt: Design a Multi-Tenant Data Platform

**Use Cases:** SaaS analytics, internal teams  
**Patterns:** Namespace isolation, quotas, row-level security

**Response Template:**
- Isolate tenants via schema or bucket naming  
- Enforce access via IAM + row-level policies  
- Track usage via metadata tagging  
- Monitor noisy neighbor impact  
- Tradeoffs: Isolation vs cost, shared infra vs governance


## 🛡️ Prompt: Design a Data Quality Framework

**Use Cases:** Ingestion validation, schema enforcement  
**Patterns:** Contracts, validation checks, alerting

**Response Template:**
- Define expectations via Great Expectations or dbt tests  
- Validate on ingestion and transformation  
- Route failures to DLQ or quarantine zone  
- Alert via Slack or PagerDuty  
- Tradeoffs: Strict checks vs ingestion continuity


## 🧮 Prompt: Design a Cost-Optimized Batch Pipeline

**Use Cases:** Daily reports, historical backfills  
**Patterns:** Partitioning, columnar formats, spot instances

**Response Template:**
- Ingest via Airflow → Spark  
- Store in Parquet with partitioning  
- Use spot instances and auto-scaling  
- Materialize views for frequent queries  
- Tradeoffs: Cost vs flexibility, latency vs throughput


## 📐 Capacity Estimation Template

Use this template to estimate system load, throughput, and scaling requirements. Ideal for sizing ingestion pipelines, APIs, databases, and storage layers.

### 🧾 1. Traffic Profile

**Inputs:**
- Daily Active Users (DAU): `e.g., 1M`
- Peak Concurrent Users: `e.g., 50K`
- Requests per User per Day: `e.g., 20`
- Avg Payload Size: `e.g., 2 KB`

**Derived Metrics:**
- Total Requests/day = `DAU × Requests/user` → `20M req/day`
- Peak QPS = `Concurrent Users × Ops/user ÷ 60 sec` → `~8K QPS`
- Bandwidth/day = `Total Requests × Payload Size` → `~40 GB/day`


### ⚙️ 2. Storage Estimation

**Inputs:**
- Event Size: `e.g., 1 KB`
- Retention Period: `e.g., 30 days`
- Daily Volume: `e.g., 100M events/day`

**Derived Metrics:**
- Daily Storage = `100M × 1 KB` → `~100 GB/day`
- Monthly Storage = `100 GB × 30` → `~3 TB`
- Add replication factor (e.g., ×3) → `~9 TB`


### 🚀 3. Throughput & Scaling

**Inputs:**
- Target Latency: `e.g., <100 ms`
- Processing Time per Event: `e.g., 10 ms`
- Parallelism: `e.g., 100 threads`

**Derived Metrics:**
- Max Throughput = `Threads × (1 / Processing Time)` → `~10K events/sec`
- Required Partitions (Kafka) = `Daily Volume ÷ Partition Throughput ÷ Seconds/day`  
  → `100M ÷ 10MB/s ÷ 86,400` → `~115 partitions`


### 🛡️ 4. Reliability & Redundancy

**Checklist:**
- ✅ Replication factor (e.g., Kafka, HDFS)
- ✅ Retry strategy (exponential backoff, DLQ)
- ✅ SLA targets (e.g., 99.9% uptime)
- ✅ Alerting thresholds (latency, error rate)


### 🧠 5. Interview Framing Tips

- Always clarify: **read/write ratio**, **peak vs average**, **latency vs throughput**
- Use round numbers for quick math (e.g., 1K req/sec = ~86M/day)
- Justify tradeoffs: cost vs performance, complexity vs reliability
- Layer your estimates: ingestion → processing → storage → access

<br>

# ⚙️ Latency & Throughput Values (Back-of-the-Envelope)

A quick-access guide to average response times, bandwidth, and throughput across common system components. Use these estimates for capacity planning, bottleneck analysis, and interview design tradeoffs.


## 🕒 Latency Estimates

| Operation                          | Avg Latency     | Notes                                                  |
|------------------------------------|-----------------|--------------------------------------------------------|
| L1 Cache Access                    | ~0.5 ns         | CPU-level, extremely fast                              |
| RAM Access                         | ~100 ns         | Memory lookup                                          |
| SSD Read                           | ~100 µs         | NVMe faster than SATA                                  |
| HDD Read                           | ~10 ms          | Mechanical disk                                        |
| Local Disk I/O (SSD)               | ~0.1–1 ms       | Depends on block size and queue depth                  |
| Network (Same Region)              | ~0.5–1 ms       | Intra-AZ or VPC                                        |
| Network (Cross Region)             | ~50–100 ms      | Depends on geography                                   |
| RDBMS Insert (Single Row)          | ~0.1–10 ms      | Depends on indexing, constraints, logging, and ACID    |
| RDBMS Insert (No Index/ACID)       | ~0.1–1 ms       | Minimal overhead; often used in ingestion pipelines    |
| RDBMS Query (Indexed)              | ~1–10 ms        | Simple SELECT with index                               |
| RDBMS Query (Join/Aggregate)       | ~10–100 ms      | Depends on data size and indexing                      |
| RDBMS Fetch (Indexed Row)          | ~0.1–1 ms       | Point lookup via primary/unique index                  |
| RDBMS Fetch (Range Scan)           | ~1–10 ms        | Depends on index selectivity and row count             |
| Redis GET/SET                      | ~0.2–1 ms       | In-memory cache                                        |
| Memcached GET                      | ~0.2–0.5 ms     | Slightly faster than Redis due to simpler protocol     |
| Kafka Publish/Consume              | ~2–10 ms        | Depends on batch size and partitioning                 |
| API Gateway + Lambda               | ~10–100 ms      | Cold start adds latency                                |
| HTTP Request (Internet)            | ~100–500 ms     | Includes DNS, TLS, routing                             |
| DynamoDB Read (Strongly Consistent)| ~1–10 ms        | Latency varies by partition size and access pattern    |
| DynamoDB Write                     | ~5–20 ms        | Includes replication and consistency overhead          |
| gRPC Call (Intra-region)           | ~0.5–5 ms       | Binary protocol, lower overhead than HTTP              |
| TLS Handshake                      | ~10–100 ms      | One-time cost per connection; reused in keep-alive     |
| Container Startup (Warm)           | ~50–200 ms      | Depends on image size and runtime                      |
| Container Startup (Cold)           | ~500 ms–5 sec   | Includes image pull and init; varies by orchestration  |

## 🌍 Network Latency by Distance (Round-Trip Estimates)

| Distance           | One-Way Delay | Est. RTT (Real-World)  | Notes                                           |
|--------------------|---------------|------------------------|-------------------------------------------------|
| 1 mile             | ~8 µs         | ~20–50 µs              | Negligible; dominated by processing overhead    |
| 100 miles          | ~0.8 ms       | ~2–5 ms                | Intra-region, low hop count                     |
| 500 miles          | ~4 ms         | ~10–20 ms              | Regional routing, moderate congestion           |
| 1,000 miles        | ~8 ms         | ~20–40 ms              | Cross-country RTT (e.g., NY ↔ TX)               |
| 3,000 miles        | ~24 ms        | ~50–80 ms              | Coast-to-coast RTT (e.g., NY ↔ SF)              |
| 6,000+ miles       | ~48 ms        | ~100–150 ms            | Transatlantic or transpacific RTT               |
| 10,000+ miles      | ~80 ms        | ~150–250 ms            | Global RTT (e.g., US ↔ Australia)               |

**Formula (approx):**  
- **Propagation delay** ≈ `Distance / Speed of signal`  
- Fiber optic speed ≈ `200,000 km/s` → ~5 µs/km or ~8 µs/mile  
- Real-world latency includes routing, queuing, NAT, congestion, and retransmissions


## 📦 Throughput & Bandwidth Estimates

| Component                          | Throughput        | Notes                                                |
|------------------------------------|-------------------|------------------------------------------------------|
| Gigabit Ethernet                   | ~125 MB/s         | Theoretical max                                      |
| NVMe SSD                           | ~2–5 GB/s         | Sequential read/write                                |
| HDD                                | ~100 MB/s         | Sequential access                                    |
| Kafka Broker                       | ~100 MB/s–1 GB/s  | Depends on partitioning and replication              |
| S3 Upload/Download                 | ~50–100 MB/s      | Per connection, can parallelize                      |
| RDBMS Insert (Single Row)          | ~1–10K rows/sec   | Varies by indexing, transaction size                 |
| RDBMS Batched Inserts (100–1K rows)| ~10K–100K rows/sec| Reduces round-trips and commit cost                  |
| RDBMS Bulk Insert (COPY, bcp, etc) | ~100K–1M rows/min | Engine-optimized; bypasses SQL layer                 |
| RDBMS Bulk Fetch (Indexed)         | ~10K–100K rows/sec| Depends on query plan, buffer size, and network      |
| RDBMS Bulk Insert (Batch)          | ~10K–1M rows/min  | Batch size and transaction tuning matter             |
| Spark Job (Clustered)              | ~100 MB/s–1 GB/s  | Depends on parallelism and I/O                       |
| Redis Ops                          | ~100K–500K ops/sec| In-memory, single-threaded                           |
| Redis Pub/Sub                      | ~100K msgs/sec    | Depends on message size and subscriber count         |
| Memcached Ops                      | ~200K–1M ops/sec  | Lightweight protocol, single-threaded                |
| DynamoDB Ops                       | ~1K–10K ops/sec   | Scales with partition key distribution               |
| API Gateway                        | ~10K–100K req/min | Scales horizontally                                  |
| Lambda Concurrency (Default Limit) | ~1K concurrent    | Soft limit; can be raised                            |
| gRPC Streaming                     | ~10–100 MB/s      | Efficient for binary payloads over persistent conns. |
| SQS Message Throughput             | ~100–300 msg/sec  | Per queue; can scale with batching and parallelism   |
| WebSocket Broadcast                | ~10K–100K clients | Depends on server and message frequency              |


## 📐 Capacity Planning Tips

- **Rule of Thumb:**  
  - 1 GB RAM ≈ 1M small objects (e.g., Redis keys)  
  - 1 Kafka partition ≈ 10 MB/s throughput  
  - 1 Lambda cold start ≈ 100–500 ms latency  
  - 1 RDS vCPU ≈ 100–500 queries/sec (simple SELECT)

- **Estimate Load:**  
  - Requests/sec × Avg Payload Size = Bandwidth  
  - Events/day ÷ Batch size = Processing frequency  
  - Concurrent users × Ops/user = Peak throughput

- **Scaling Strategy:**  
  - Use horizontal scaling for stateless services  
  - Use partitioning/sharding for stateful stores  
  - Cache hot paths to reduce latency and load


## 🧠 Interview Use Cases

- “Estimate how many Kafka partitions you'd need for 1 TB/day ingestion”  
- “How would you scale a Redis cache for 1M QPS?”  
- “Design a system to serve 10K concurrent users with sub-100ms latency”
