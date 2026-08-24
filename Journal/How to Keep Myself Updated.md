## 📚 Books & Papers to Read #books 

-  **Database Internals:** _A Deep Dive into How Distributed Data Systems Work_ by Alex Petrov
- **Designing Data-Intensive Applications (DDIA)** by Martin Kleppmann
- **In Search of an Understandable Consensus Algorithm** _(The Raft Paper)_
- **The Pragmatic Programmer**
- **A Philosophy of Software Design**
- **Fundamentals of Software Architecture**
- **Software Architecture: The Hard Parts**
- **Software Engineering at Google: Lessons Learned from Programming Over Time**
- **Site Reliability Engineering: How Google Runs Production Systems**
- **System Design Interview: An insider's guide**

##  📓 Courses To Take

- [MIT Distributed Systems](https://pdos.csail.mit.edu/6.824/schedule.html)
- [CMU Intro to Database Systems](https://db.cs.cmu.edu/courses/)
- [Applied Distributed Database](https://github.com/pingcap/talent-plan)
- [Database Seminars](https://db.cs.cmu.edu/seminars/)
- [Hash Table Internals](https://www.youtube.com/playlist?list=PLsdq-3Z1EPT2UnueESBLReaVSLIo_BuAc)
- [Observability](https://learnobservability.com/) or [Observability with Grafana](https://www.udemy.com/course/grafana-prometheus-loki-alloy-tempo/?couponCode=25BBPMXNVD35)
- [Computer Networks](https://youtube.com/playlist?list=PLd1s-PEC5Pio&si=XsEPmrpmqp029dEG)
- 


## 🛠️ Project 1: The LSM-Tree Key-Value Engine 

> **Project Definition:**
> 
> Build a raw, disk-based Key-Value database engine from scratch in a systems programming language (e.g., Go or Rust), strictly implementing an LSM-Tree architecture. The engine must include an in-memory memtable, a Write-Ahead Log (WAL) for durability, background flushing mechanisms to create immutable SSTables, and a basic background compaction routine to merge SSTables.

### Execution & Instrumentation

- **RFC/Design Document:** Define the binary format for the WAL and the SSTable disk layout. Detail the concurrency model, explicitly outlining how the background compaction thread will safely acquire file locks and merge data without blocking incoming read and write requests from clients.
- **Implementation via Vertical Slicing:**
    
    -  **Slice 1:** Implement in-memory writes and reads (Memtable operations only).
    -  **Slice 2:** Integrate the WAL to ensure crash recovery works when the server restarts.
    - **Slice 3:** Implement the capacity threshold that flushes the Memtable to a single SSTable on disk.
    -  **Slice 4:** Implement multi-SSTable reads and the background mergesort compaction logic.
- **Deployment:** Wrap the custom storage engine in a lightweight HTTP API. Dockerize the application. Use Terraform to provision a VPC, an Application Load Balancer, and an ECS Fargate cluster. Deploy the container, ensuring the storage engine mounts an AWS Elastic File System (EFS) volume to provide durable, stateful disk storage across container restarts.
## 🌐 Project 2: The Distributed Raft Cluster

> **Project Definition:**
> 
> Evolve the single-node LSM-Tree Key-Value store into a fault-tolerant, distributed cluster. Implement the Raft consensus algorithm entirely from scratch to manage the strict replication of the Write-Ahead Log (WAL) across a minimum of three independent nodes over a network.

### Execution & Instrumentation

- **Implementation:** Build the randomized leader election loop and the `AppendEntries` replication logic. Refactor the previous codebase aggressively. If the storage engine logic has become deeply entangled with the HTTP routing, use Fowler's refactoring techniques to extract a clean networking interface, allowing nodes to communicate via RPCs.
- **Observability Integration:** Instrument the codebase exhaustively with the OpenTelemetry SDK.
    
    - Create a dedicated span for every incoming client request, tracking it as it is appended to the leader's log, transmitted over the network, and replicated to the followers.
    - Expose Prometheus metrics for internal Raft states, including the current term, the node's current role (leader, follower, or candidate), and the commit index.
- **Deployment:** Use Terraform to dramatically update the ECS Fargate configuration. Deploy three completely separate instances of the container into different subnets. Deploy a Prometheus container configured to scrape the OTLP metrics endpoint, alongside a Grafana container. Build a comprehensive dashboard that visually indicates which node is the current leader and tracks the replication latency across the cluster.