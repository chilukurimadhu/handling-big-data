Project Notes: Loading 0.5TB Data into PostgreSQL

Client Requirements :
Data Volume: 0.5TB, chunked into 1GB files.

Cloud Restrictions: No use of public cloud platforms (Azure, AWS, Google).

Database Requirement: Must use a free and open-source database.

Performance: The solution must support fast inserts, updates, and queries.

Proposed Solution

Virtual Machine Setup:

Requested the client to provision a high-performance VM (non-public cloud hosting) with the following specs:
64 cores CPU.
256GB RAM.
Transferred the 1GB file chunks to the VM for processing.

Processing with Apache Spark:

Installed and configured Apache Spark on the VM for distributed data processing.
Used PySpark to:
Read and process the 1GB files.
Perform transformations efficiently on large files, leveraging Spark's in-memory capabilities.
Database Selection:

Chose PostgreSQL, an open-source, free database, to meet the client's requirement.
Configured PostgreSQL for optimal performance to handle large-scale data inserts and updates.
PostgreSQL Performance Tuning

Connection Management:

ALTER SYSTEM SET max_connections = 100;
Memory Allocation:



ALTER SYSTEM SET shared_buffers = '64GB';          -- For caching data.

ALTER SYSTEM SET work_mem = '256MB';               -- For individual query memory.

ALTER SYSTEM SET maintenance_work_mem = '1800MB';  -- For maintenance tasks like VACUUM.

ALTER SYSTEM SET effective_cache_size = '195GB';   -- For query planner optimizations.

Parallel Processing:


ALTER SYSTEM SET max_worker_processes = 24;

ALTER SYSTEM SET max_parallel_workers = 16;

ALTER SYSTEM SET max_parallel_workers_per_gather = 8;

ALTER SYSTEM SET max_parallel_maintenance_workers = 4;

Write-Ahead Logging (WAL) Configuration:

ALTER SYSTEM SET wal_buffers = '16MB';

ALTER SYSTEM SET synchronous_commit = OFF;         -- For faster bulk inserts (can be reverted later).

IO Optimization:


ALTER SYSTEM SET effective_io_concurrency = 200;   -- Set higher for SSDs.

Database Maintenance:

Performed VACUUM ANALYZE to optimize query performance:


VACUUM ANALYZE;

Additional Queries and Operations

Data Cleanup:

DELETE FROM public.master_usa WHERE linkedinurl NOT LIKE 'https%';

Data Verification:


SELECT * 
FROM public.master_usa 
WHERE pemail IS NOT NULL AND record_action = 'U';

Challenges and Solutions
Large File Transformations:

Leveraged Spark’s distributed computing to handle transformations on the 1GB chunks efficiently.

Optimizing PostgreSQL for High Performance:

Tuned PostgreSQL settings for high-concurrency inserts and updates.

Used asynchronous commit and batch processing for faster bulk inserts.

Outcome
Successfully loaded 0.5TB of data into PostgreSQL.
Achieved efficient querying and updates with optimized PostgreSQL configurations.
Maintained the client’s security requirements by avoiding public cloud solutions.
