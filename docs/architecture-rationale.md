# Design Drivers

### Volumes & Peak Rates:

Processing up to 5,000 events per second during peak hours (about 50 GB of raw data per day) with the ability to scale horizontally when launching gaming events

### Latency Classes:

The requirements for data freshness are divided into two classes: operational dashboards for stability monitoring (up to 5 minutes) and batch financial/product reports (daily)

### Reliability & Compliance:

Guarantee the accuracy of financial calculations, the protection of personal data (PII) of players and full compliance with privacy standards (GDPR)

### Budget & Constraints:

Cost optimization by using cloud-based object storage instead of permanent relational database clusters for raw layers

# Ingestion

### Requirement:

Reliable collection of a continuous stream of game events without data loss and flexible reception of batch webhooks from app stores

### Decision:

The hybrid scheme of ingestion. Game events are sent via the API Gateway to the Apache Kafka message broker, which provides buffering and sharding by player_id. Payment webhooks and backups are received through a dedicated listener service

### Trade-off:

The increased operational complexity of Kafka cluster administration is offset by fault tolerance and storage protection from load surges

### Rejected Alternative:

Direct recording of client events from games to a relational database was rejected due to the risk of database crashes during peak loads and the high cost of locks

# Object Storage

### Requirement:

Cheap, long-lasting storage of large amounts of data with support for separation into logical zones (medallion architecture) and the ability to isolate personal information

### Decision:

Using AWS S3 divided into zones: Bronze (raw JSON logs), Silver (cleaned, deduplicated data with masked PII) and Gold (final analytical aggregates)

### Trade-off:

Lack of native transactional isolation "out of the box" (solved by using tabular formats like Delta Lake / Iceberg in the following stages)

# Storage and Compute Separation

### Requirement:

The ability to independently scale storage capacity and computing power depending on current loads

### Decision:

Complete separation of permanent storage in S3 from computing clusters (Stream Processing Engine and Batch Processing Engine). Calculations can be run, scaled to zero, or expanded on demand

### Trade-off:

Increased network traffic (I/O) between storage and computing nodes when performing heavy joins

# Scale-Out Choices

### Requirement:

The ability of the platform to cope with peaks of activity (for example, update releases) without degradation of response time

### Decision:

Horizontal scaling of Kafka (partitioning by the player_id key), scaling of Apache Flink/Spark workers, as well as the use of a distributed MPP engine (ClickHouse) for analytical queries

### Trade-off:

The risk of "hot keys" for popular players or top event items, requiring a competent selection of partitioning keys

# Processing and Serving

### Requirement:

Fast delivery of ready-made data storefronts to consumers without overloading the raw layers

### Decision:

Streaming processing (Flink) for live data and batch processing (Spark) for daily storefronts. Data from the Gold zone is provided through a distributed SQL engine (ClickHouse) and a caching semantic layer for dashboards

# Governance, Privacy, and Access Control

### Requirement:

Protection of confidential player data (IP addresses, mail, payment tokens) and compliance with the principle of least privilege

### Decision:

Early masking and pseudonymization of PII data during the transition from the Bronze to Silver zone. Implementing a role-based access model (RBAC) and maintaining a Lineage-based metadata catalog

### Trade-off:

Restricting analysts' direct access to raw data (Bronze), requiring prior clearance and masking procedures

# Operations and Cost

### Requirement:

Reliable pipeline automation, fault monitoring, and cloud cost control

### Decision:

Orchestration of all tasks using Apache Airflow with configured Data Quality Gates, automatic crash retries, and alerts for the maintenance team
