Below you can find five mini project ideas that you can implement to gain hands-on experience and apply concepts  from *Designing Data-Intensive Applications*. Each project includes tool suggestions and strategies for running on your local machine.

---

### 1. Distributed Key-Value Store with Replication and Fault Tolerance

**Concepts Applied:** Data modeling, replication strategies, fault tolerance, consensus.

**Description:**  
Create a simplified distributed key-value store on your local machine, simulating multiple nodes as separate processes or containers. Start with a single-leader replication mode: one node is the leader, accepting writes and replicating to followers. Then implement failover by integrating a consensus algorithm (like Raft) to elect a new leader when the current one fails.

**Tools & Libraries:**
- **Language:** Go or Java are common choices.
- **Raft Implementations:**  
  - Go: [etcd/raft](https://github.com/etcd-io/etcd/tree/main/raft) or [HashiCorp raft](https://github.com/hashicorp/raft)  
  - Java: [Atomix](https://atomix.io/)  
- **Process Simulation:** Use Docker Compose or multiple local processes on different ports.
- **Testing:** `curl` or a simple HTTP client for GET/SET operations.

**Local Feasibility:**
- All nodes run locally as separate processes.
- State stored in local files.
- Leader failure simulated by stopping a process.

---

### 2. Event-Sourced Application with Stream Processing and Schema Evolution

**Concepts Applied:** Event sourcing, schema evolution, backward/forward compatibility, stream processing.

**Description:**  
Build an event-sourced app, e.g., a simple order management system. Store all changes as immutable events in Kafka. Use Kafka Streams or Apache Flink (in local mode) to build a materialized view. Introduce schema evolution by using Avro and a local Schema Registry. Update your schema and ensure old and new consumers continue to work seamlessly.

**Tools & Libraries:**
- **Event Log:** [Apache Kafka](https://kafka.apache.org/) (run locally via Docker Compose)
- **Stream Processing:**  
  - Kafka Streams (runs in the application JVM)
  - [Apache Flink](https://nightlies.apache.org/flink/) in local mode
- **Schema Management:** [Confluent Schema Registry](https://www.confluent.io/product/confluent-platform/schema-registry/)
- **Serialization:** Avro (with Avro libraries for Java/Scala)

**Local Feasibility:**
- All services (Kafka, Schema Registry) run locally.
- Schema updates tested by deploying updated consumers and producers.

---

### 3. Batch and Streaming ETL Pipeline for a Data Warehouse

**Concepts Applied:** Batch processing, streaming ingestion, columnar storage, data modeling for analytics.

**Description:**  
Set up a pipeline that ingests CSV/JSON files from a local directory and processes them via Apache Spark in local mode to produce clean, aggregated datasets. Store results in a columnar format (Parquet). Add a streaming component: read incremental updates from Kafka and apply them using Spark Structured Streaming. Then query the final Parquet datasets using DuckDB or Trino locally.

**Tools & Libraries:**
- **Batch Processing:** [Apache Spark](https://spark.apache.org/) in local mode
- **Streaming:** Spark Structured Streaming + Kafka input source
- **Data Formats:** Parquet for columnar storage
- **Querying:** [DuckDB](https://duckdb.org/) or [Trino](https://trino.io/) in a local container

**Local Feasibility:**
- Spark in local mode (no cluster needed).
- Kafka and Trino via Docker Compose.
- Parquet files on local filesystem.

---

### 4. Consistency and Isolation-Level Demonstration with a Transactional Store

**Concepts Applied:** Transaction isolation levels, concurrency control, serialization methods.

**Description:**  
Implement a simple in-memory store that supports transactions with different isolation levels: read committed, repeatable read, and serializable. Use threads or goroutines to simulate concurrent operations. Show anomalies like dirty reads, lost updates, and phantom reads at lower isolation levels and demonstrate how stricter isolation prevents them.

**Tools & Libraries:**
- **Language:** Python, Go, or Java
- **Testing Frameworks:** `pytest` (Python), `JUnit` (Java), built-in testing in Go

**Local Feasibility:**
- Entirely in-memory.
- No external dependencies.
- Simple concurrency tests on a single machine.

---

### 5. Geographically Distributed Deployment Simulation

**Concepts Applied:** Geo-replication, latency simulation, fault tolerance, high availability.

**Description:**  
Simulate a geo-distributed system by running multiple service instances (from Project 1 or another store) in Docker Compose. Use `tc` (traffic control) or Docker network settings to add latency between containers, emulating distance. Test synchronous vs. asynchronous replication and tune quorum sizes to see their effect on latency and consistency.

**Tools & Libraries:**
- **Multi-Region Simulation:** Multiple Docker containers
- **Latency Injection:**  
  - Linux: `tc` command  
  - On Mac/Windows: Docker network settings, or tools like `comcast`
- **Metrics & Observability:** Prometheus/Grafana (both runnable locally)

**Local Feasibility:**
- All instances run as containers on one machine.
- Artificial latency introduced locally.
- No extra hardware required.
