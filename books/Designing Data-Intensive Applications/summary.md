# Summary

This summary distills key concepts from *Martin Kleppmann*’s *Designing Data-Intensive Applications* into a structured set of notes. It is tailored for mid to senior Engineers who want to deepen their understanding of modern data architectures, reliability strategies, scaling and distributed data processing. The goal is to provide a go-to resource to refresh foundational knowledge or identify areas for further exploration. The style is designed to be read comfortably within 15 minutes.

## Part I: Foundations of Data Systems

### Core System Qualities
- **Functional Requirements:** Define what the system should do.
- **Nonfunctional Requirements:** Define general properties (e.g., reliability, scalability, maintainability).
- **Reliability:** Systems must handle faults (hardware, software, human error) gracefully.
- **Scalability:** Keep performance good as load increases.
- **Maintainability:** Make it easier for teams to evolve, debug, and operate systems over time.

### Data Models and Query Languages
- **Relational Model:** Mature, structured schemas; supports joins and complex queries.
- **NoSQL Models:**
  - **Document Stores:** Flexible schemas; good for hierarchical data with rare cross-document relationships.
  - **Graph Databases:** Excellent for deeply interconnected data and relationship-oriented queries.
- **Schema Enforcement:**
  - **Explicit (On Write):** Strict and predictable schema validation.
  - **Implicit (On Read):** Flexible and adaptable to changing requirements.

### Storage and Retrieval
- **OLTP (Online Transaction Processing):** Many small, indexed queries; focus on quick lookups of individual records.
- **Data Warehousing & Analytics:** Column-oriented storage optimized for scanning large datasets efficiently.
- **Storage Engines:**
  - **B-Tree / Update-in-Place:** Traditional indexing, good for random lookups.
  - **LSM-Trees / Log-Structured:** Turn random writes into sequential writes for higher throughput.

### Encoding and Evolution
- **Schema Evolution:** Plan for forward/backward compatibility. Use rolling upgrades for safer, no-downtime deployments.
- **Encoding Formats:**
  - **Textual (JSON, XML):** Flexible but may lack strict typing.
  - **Binary (Avro, Protobuf):** Compact, efficient, and schema-driven with built-in compatibility semantics.
- **Dataflow Modes:**
  - **Databases:** Encode on write, decode on read.
  - **APIs (RPC/REST):** Clients and servers encode/decode requests and responses.
  - **Message Passing:** Producers encode, consumers decode messages asynchronously.

---

## Part II: Distributed Data

### Replication
- **Motivations:** High availability, reduced latency, and scalability for reads.
- **Replication Topologies:**
  - **Single-Leader:** Simplifies reasoning but risks bottlenecks and stale reads on followers.
  - **Multi-Leader:** Better write availability and geo-distribution, but requires conflict resolution.
  - **Leaderless:** High fault tolerance at the cost of weaker consistency guarantees.
- **Consistency Models:**
  - **Read-After-Write:** Ensures users see their own updates immediately.
  - **Monotonic Reads:** Prevents “time travel” in read results.
  - **Consistent Prefix Reads:** Users never see causally “out-of-order” data.

### Partitioning
- **Why Partition:** Scale beyond what a single machine can handle.
- **Partitioning Strategies:**
  - **Key-Range Partitioning:** Preserves order, enabling efficient range queries but risks hotspots.
  - **Hash Partitioning:** Distributes load evenly, but complicates range queries.
- **Index Partitioning:**
  - **Local (Document-Partitioned) Indexes:** Store indexes with data; simple writes but scatter/gather reads.
  - **Global (Term-Partitioned) Indexes:** Separate indexing partitions; simpler reads, more complex writes.
- **Rebalancing:** Move partitions when adding/removing nodes to maintain balanced load.

### Transactions
- **Benefits:** Simplify error handling by abstracting concurrency, retries, and partial failures.
- **Isolation Levels:**
  - **Read Committed:** Prevents dirty reads but allows some anomalies.
  - **Snapshot Isolation:** Good balance; prevents many anomalies but not fully serializable.
  - **Serializable:** The gold standard for correctness; appears as if transactions run sequentially.
- **Implementing Serializability:**
  - **2PL (Two-Phase Locking):** Traditional but can impact performance.
  - **Serializable Snapshot Isolation (SSI):** Optimistic; aborts conflicting transactions at commit time.

### The Trouble with Distributed Systems
- **Partial Failures:** Network delays, dropped packets, and node pauses are normal.
- **Unreliable Clocks & Timeouts:** Don’t rely on synchronized clocks; use timeouts as best-effort failure detectors.
- **Fault Tolerance:** Use quorums, consensus protocols, and careful retry logic to handle failures gracefully.

### Consistency and Consensus
- **Linearizability:** Simple mental model (like a single-threaded variable) but expensive due to coordination.
- **Causality:** Weaker consistency that models cause-and-effect relationships without total ordering.
- **Consensus (Paxos, Raft):** Needed for leader election, atomic commits, and linearizable operations in a distributed setting.
- **Trade-Offs:** Stronger consistency often means higher latency and complexity.

---

## Part III: Derived Data

### Batch Processing
- **Functional, Immutable Approach:** Input → Transformation → Output. Use composable tools (like MapReduce and successors).
- **Fault Tolerance in Batch:** Frequent checkpoints and retries ensure deterministic outputs despite failures.
- **Join Strategies:** Sort-merge or hash-based joins for large-scale batch analytics.

### Stream Processing
- **Unbounded Data:** Continuous streams of events rather than fixed-size datasets.
- **Event Logs & Message Brokers:** Kafka-like logs enable replay and state recomputation.  
- **Event Sourcing & Change Data Capture (CDC):** Model database changes as streams to build derived views in real-time.
- **Time & Ordering:** Handle late arrivals, event-time vs. processing-time differences carefully.
- **Exactly-Once Semantics:** Achieved via checkpoints, idempotent operations, and transactions.

### The Future of Data Systems
- **Loose Coupling via Data Flows:** Asynchronous transformations increase fault tolerance and scalability.
- **Reprocessing & Schema Evolution:** Fix code, re-run transformations, and easily evolve schemas over time.
- **Ethical Considerations:** Data-intensive systems have real-world impact—engineer responsibly and ethically.

---

# Key Takeaways
- **No One-Size-Fits-All Solution:** Different workloads call for different technologies and architectures. Understanding the fundamental trade-offs is more crucial than memorizing best practices for a particular tool.
- **Think in Terms of Guarantees and Failures:** System design should start from what you need to guarantee—consistency, durability, fault tolerance—then pick the components and configurations that achieve those guarantees.
- **Embrace Evolution:** Data systems change constantly. Good design accommodates evolving schemas, scaling workloads, new hardware platforms, and shifting requirements.
- **Modeling Matters:** The data model chosen influences complexity, scalability, and how easily developers can reason about the system.
- **Ethics and Responsibility:** Data-intensive systems shape real-world outcomes. Consider privacy, fairness and potential harm when designing these systems. Make responsible choices to maintain trust and serve the broader interests of society.