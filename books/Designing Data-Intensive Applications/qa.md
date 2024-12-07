# Questions & Answers

Below are two sets of questions and answers for *Designing Data-Intensive Applications* by *Martin Kleppmann*. The first set tests the understanding of the book’s core principles beyond just definitions. The second set focuses requires deeper reasoning, nuanced trade-offs and the ability to apply concepts in complex and real-world scenarios.

## Medium-Difficulty

**1. Q:** Why might a system choose snapshot isolation over serializability for transactional workloads?  
**A:** Snapshot isolation often offers a good balance between performance and safety. It prevents many common anomalies (e.g., dirty reads, non-repeatable reads, phantom reads) without the overhead of full serializability. While it doesn’t guarantee perfect isolation (e.g., it still allows write skew), it typically performs better and scales more easily than a fully serializable system.

**2. Q:** How does using a columnar storage format in a data warehouse improve query performance compared to a row-oriented store?  
**A:** Columnar formats enable more efficient compression and minimize I/O by allowing queries to read only the relevant columns. This reduces data transfer, leverages vectorized processing, and improves analytical query performance, especially for aggregate computations over large datasets.

**3. Q:** In what scenario would log-structured storage be less beneficial than B-tree-based storage?  
**A:** If your workload consists predominantly of random reads and updates to small subsets of data, and there’s less emphasis on high write throughput, B-trees might be more efficient. B-trees allow in-place updates and can quickly access individual records without needing frequent compaction and segment merging, making them simpler when writes are not a bottleneck.

**4. Q:** What trade-offs come with relying on eventual consistency rather than strong consistency in a distributed system?  
**A:** Eventual consistency improves availability and fault tolerance under network partitions and failures, and can reduce latency. However, it complicates application logic since clients may see stale data. Developers must handle merging conflicts and reason about asynchronous updates, increasing complexity in the application layer.

**5. Q:** Why is schema evolution critical, and how do binary schema-driven formats like Avro or Protocol Buffers help?  
**A:** Systems and requirements change over time. Evolving schemas without downtime is essential for maintainability and development velocity. Binary schema-driven formats define explicit compatibility rules, making it possible for new consumers to read old data and old consumers to read new data, allowing rolling upgrades and safer deployments.

**6. Q:** How can monotonic reads consistency simplify the user experience in a replicated database?  
**A:** Monotonic reads ensure that once a user has seen a certain state, they won’t see a previous state on subsequent queries. This avoids confusion where a user’s subsequent view of data “goes backward in time.” While not as strong as linearizability, it improves user experience and reasoning about system behavior.

**7. Q:** What is a common method to achieve exactly-once semantics in stream processing systems?  
**A:** Techniques include using idempotent writes on sinks, checkpointing the consumer’s read offsets, and employing transactional mechanisms (e.g., transactional writes to output systems and state stores). Tools like Kafka Streams or Flink rely on these methods to ensure that when a failure occurs and data is replayed, the end state remains correct without duplicates.

**8. Q:** How does range-based (key-range) partitioning help with certain queries, and what is a potential downside?  
**A:** Range partitioning keeps related keys (e.g., lexicographically close) on the same partition, enabling efficient range queries and scans. However, if certain key ranges receive disproportionate traffic, it can create hotspots, leading to uneven load and reduced overall cluster performance.

**9. Q:** What role does rolling upgrade strategy play in large-scale distributed systems?  
**A:** Rolling upgrades allow gradual deployment of new versions across a cluster, minimizing downtime and risk. If issues arise, only a portion of the cluster is affected, making rollback easier. This strategy improves system maintainability and reliability over time.

**10. Q:** Why might you choose a multi-leader replication approach, and what complexities does it introduce?  
**A:** Multi-leader replication can improve write throughput and availability since multiple nodes can accept writes. However, it complicates conflict resolution, introduces the possibility of conflicting concurrent writes, and may require custom logic or automated conflict handling to reconcile divergent replicas.

---

## Hard-Difficulty

**1. Q:** Explain how you might design a system to provide linearizable reads on a globally distributed database, and discuss the performance implications.  
**A:** Achieving linearizability often involves coordination protocols (e.g., consensus) or reading from a primary leader and waiting for up-to-date metadata. Globally distributed systems add significant latency since enforcing a single global order of operations across continents requires round-trip communication and possibly quorum reads. The performance impact is higher latency per request, increased operational complexity, and potential throughput reduction.

**2. Q:** In what situations would you prefer a leaderless replication strategy over consensus-based leader election, and how would you mitigate the risks of eventual consistency?  
**A:** Leaderless replication can simplify cluster management and improve fault tolerance in highly unreliable networks. It can also boost availability under partitions. To mitigate eventual consistency risks, you could adopt read-repair, hinted handoff, version vectors to track causality, and client-side conflict resolution strategies. Additionally, adjusting read/write quorums and using CRDTs (Conflict-Free Replicated Data Types) can ensure convergence on consistent states.

**3. Q:** How can a distributed transaction engine achieve serializable isolation without resorting to two-phase locking or waiting for global consensus on every operation?  
**A:** Techniques like Serializable Snapshot Isolation (SSI) or deterministic database execution orders can achieve serializability. SSI tracks potential conflicts and aborts problematic transactions at commit time rather than blocking them upfront. Deterministic execution orders predefine transaction sequences, eliminating contention at runtime. These approaches avoid constant global coordination while still providing strong guarantees.

**4. Q:** Describe a scenario where partitioning secondary indexes separately from the primary data might be essential, and detail how you’d handle updates and queries efficiently.  
**A:** If queries frequently filter by a secondary attribute that doesn’t align with the primary key’s partitioning, separating the index allows targeted lookups for that attribute. To handle efficient updates, you’d route index updates to the partition responsible for that attribute value. Precomputing references or using a distributed query engine that can scatter/gather from index partitions speeds up queries. Careful batching, asynchronous index updates, and caching can mitigate overhead.

**5. Q:** How do consensus protocols (like Raft or Paxos) interact with data partitioning and sharding, and what are best practices for scaling consensus-based coordination?  
**A:** Each shard or partition can elect its own leader via consensus. To scale, you limit the scope of each consensus group to a subset of data. Best practices include:
- Using hierarchical consensus, where a master metadata service manages shard assignments and each shard uses local consensus.  
- Minimizing the number of nodes in each consensus group for efficiency.  
- Using load-based partitioning to keep groups small and manageable.  
- Employing rebalancing strategies that move shards but keep consensus stable.

**6. Q:** Compare the complexity and performance trade-offs of implementing a global transaction across multiple partitions using two-phase commit vs. employing an event-sourced, streaming approach.  
**A:**
- **Two-Phase Commit (2PC):** Ensures atomic commits but adds a synchronous coordination step and blocking if the coordinator fails. It’s simpler to reason about ACID semantics but can slow down global transactions.  
- **Event-Sourced / Streaming:** Instead of blocking commits, changes are appended to logs and downstream consumers derive the final consistent state. This avoids synchronous blocking at commit time but requires more complex eventual consistency handling, replay logic, and careful schema evolution to maintain correctness.

**7. Q:** In designing a system with both batch and stream processing components, how would you ensure that reprocessing historical data leads to consistent results with minimal downtime?  
**A:** You’d store immutable input data, maintain versioned code and schemas, and rely on deterministic transformations. By replaying historical event logs with updated logic, you can recompute derived state to fix errors. Techniques like the Lambda or Kappa architecture ensure minimal downtime: the batch layer can produce a correct snapshot, while the streaming layer continuously refines it. Using idempotent writes and “exactly-once” semantics helps ensure consistent end states.

**8. Q:** Discuss how integrating a distributed consensus system (like ZooKeeper or etcd) can simplify leader election, coordination, and membership changes in a complex data pipeline, and the trade-offs this introduces.  
**A:** Integrating consensus-based coordination services simplifies building reliable services that depend on shared configuration, naming, or leader election. This reduces custom algorithm complexity and ensures a known correctness guarantee. However, it introduces operational overhead, a single point of coordination that must remain highly available, and possible bottlenecks. There’s also a learning curve and increased complexity in deployment and management.

**9. Q:** If you must provide linearizable reads in a geo-distributed database, how could you minimize latency while still guaranteeing correctness?  
**A:** One approach is to use local leader proxies or read replicas that track the latest known commit timestamp from the leader and wait only if the replica is behind. Techniques such as lease-based protocols, hybrid logical clocks, or loosely synchronized clocks combined with read repair ensure correctness. Advanced protocols like Spanner’s TrueTime can provide external synchronization bounds, allowing reads to be linearizable with bounded staleness while minimizing waiting periods.

**10. Q:** What strategies can a system use to gracefully degrade and maintain partial functionality under severe network partitions or rolling outages, and what are the long-term consistency implications?  
**A:** Systems can serve stale reads, degrade to lower isolation levels, return approximate results, or switch to local write buffering until the network recovers. Techniques like CRDTs ensure eventual convergence without centralized coordination. However, these strategies imply that long-term consistency might be delayed, conflict resolution might produce unexpected merges, and clients must handle temporary anomalies. Over time, once connectivity is restored, the system must reconcile divergent states to achieve a consistent view.
