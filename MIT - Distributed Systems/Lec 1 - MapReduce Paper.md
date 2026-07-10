#system-design #distributed-systems #big-data #batch-processing

## Overview

- **Definition**: A programming model for processing massive datasets across horizontally scaled, distributed clusters.
- **Origin**: Outlined in a 2004 Google Whitepaper.
- **Core Problem Solved**: Moving terabytes of data across a network for centralized processing is too slow. MapReduce solves this by sending the _code_ to the _data_.

## Core Principles

- **Data Locality**: Map functions operate on the data exactly where it lives on the disk (e.g., in a Distributed File System like HDFS) rather than transferring it over the network.
- **Fault Tolerance**: Designed for unreliable commodity hardware. If a machine or network partition fails, the central controller simply re-runs the failed task on a different node.
- **Idempotency**: For fault tolerance to safely restart tasks, the Map and Reduce functions must be idempotent (repeating the function multiple times yields the exact same result).

## The MapReduce Pipeline

![[MapReduce Architecture.png]]

### 1. Input & Splitting

- Data resides in a distributed file system.
- It is split into smaller, replicated chunks across hundreds or thousands of machines.
- A central controller tracks these chunks and assigns workers.

### 2. Map Phase

- **Action**: Transforms unstructured data (like raw logs or text) into a standard format.
- **Output**: Generates intermediary `(Key, Value)` pairs.
- Operates entirely in parallel across nodes without cross-communication.

### 3. Sort & Shuffle Phase

- **Sort**: On the mapping nodes, the `(Key, Value)` pairs are sorted by Key.
- **Shuffle**: Uses a hash of the Key to partition and route all identical keys to the exact same Reducer node.
- **Why Sorting is Mandatory**: If keys weren't sorted, the Reducer would have to hold every key in memory because it wouldn't know when it had received the "last" value for a specific key. Sorting allows the Reducer to perform an $O(n)$ merge join and immediately flush a completed key to disk once a new key sequence starts, preventing massive memory overhead.

### 4. Reduce Phase

- **Action**: Receives grouped outputs (e.g., `Key: [Value1, Value2, Value3]`).
- **Output**: Compresses/aggregates the list of values into a single, final meaningful value.

### 5. Materialize

- The final reduced values are written to disk and saved back into the distributed file system.

## Classic Example: Word Count

![[MapReduce Example.png]]

Given two files: "this is an apple" and "apple is red in color".

1. **Map**: Each node counts occurrences locally.
    - Node A -> `(apple, 1)`, `(this, 1)`, `(is, 1)`
    - Node B -> `(apple, 1)`, `(is, 1)`, `(red, 1)`
2. **Shuffle**: Routes all identical keys to specific reducers.
    - Reducer 1 receives -> `apple: [1, 1]`
3. **Reduce**: Aggregates the array into a single value.
    - Output -> `(apple, 2)`

## Advanced Implementations

- **Job Chaining**: Since a MapReduce job simply maps data on disk to different data on disk, complex operations are achieved by chaining multiple MapReduce jobs together sequentially. 

- **Performance Constraint (Key Sorting)**: Key sorting is not an option; it is _mandatory_. Without sorting, a reducer has to hold every key and all its associated values in memory because it cannot know when it has seen the 'last' instance. Sorting allows an $O(n)$ merge join and immediate data flushing to disk when a key sequence changes, reducing memory overhead to near zero.

## Key Examples from Real World

- **YouTube Metadata Extraction (Daily Batch Job)**: Analyzing terabytes of metadata across millions of distributed videos (engagement rate, likes, comment counts, etc.) to generate global reports, such as a report on the "Total Likes on All Videos containing 'Cat'." Each map function could produce `(cat_video, 1,452 likes)` and the reducer would aggregate them into `(cat_videos, 14,520,321 total likes)`.

- **Daily Log Analysis**: Analyzing daily distributed web server logs across thousands of nodes (e.g., count how many unique IPs accessed the site yesterday). A chain of MapReduce jobs could be used, with the first job creating `(IP, 1)` and the second counting them.
