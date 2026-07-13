\# Amazon RDS (Relational Database Service)



\## Overview



\- \*\*RDS = Relational Database Service\*\*

\- It's a \*\*managed DB service\*\* for databases that use SQL as a query language

\- Allows you to create databases in the cloud that are managed by AWS

\- Supported engines:

&#x20; - Postgres

&#x20; - MySQL

&#x20; - MariaDB

&#x20; - Oracle

&#x20; - Microsoft SQL Server

&#x20; - IBM DB2

&#x20; - Aurora (AWS proprietary database)



\### Advantage of using RDS vs deploying DB on EC2



\- RDS is a \*\*managed service\*\*:

&#x20; - Automated provisioning, OS patching

&#x20; - Continuous backup and restore to specific timestamp (Point in Time Restore)

&#x20; - Monitoring dashboards

&#x20; - Read replicas for improved read performance

&#x20; - Multi-AZ setup for DR (Disaster Recovery)

&#x20; - Maintenance windows for upgrades

&#x20; - Scaling capabilities (vertical and horizontal)

&#x20; - Storage backed by EBS

\- \*\*But\*\* you can't SSH into your RDS instances (it's fully managed)



\---



\## Amazon RDS — Hands On



1\. Go to \*\*Aurora and RDS\*\* → \*\*Databases\*\*

2\. \*\*Create a database\*\* → choose \*\*Standard Create\*\* (full configuration)

3\. Select engine: \*\*MySQL\*\*

4\. Under templates: choose \*\*Free Tier\*\*

5\. Settings:

&#x20;  - Self managed credentials

&#x20;  - Set a master password

6\. Click \*\*Create database\*\*

7\. Database creation takes some time (goes through creating → backing-up → modifying → available)

8\. Copy the \*\*server address (endpoint)\*\*

9\. Go to \*\*SQLElectron\*\* → download (if not already installed) → \*\*add the connection manually\*\*



\### Common connection issues



If you have difficulty connecting:

\- Check \*\*Public access\*\* is allowed (enabled) on the DB instance

\- Check the \*\*Security Group\*\* allows your IP on port 3306



\### Once connected



\- Write some SQL queries in SQLElectron

\- Back to the AWS Console:

&#x20; - \*\*Actions\*\* → \*\*Create read replica\*\* → just view it (don't need to complete)

&#x20; - Go to \*\*Monitoring\*\* tab → you can watch all the metrics there

\- Finally, \*\*delete your DB\*\* (to avoid ongoing charges)



\---



\## RDS — From Single-AZ to Multi-AZ



\- This is a \*\*zero-downtime operation\*\* (no need to stop the DB)

\- Just click \*\*Modify\*\* on the database

\- What happens internally:

&#x20; 1. A \*\*snapshot\*\* is taken

&#x20; 2. A \*\*new DB is restored\*\* from the snapshot in a new AZ

&#x20; 3. \*\*Synchronization\*\* is established between the two databases (original RDS DB instance and the new standby DB)





\## RDS Multi-AZ (Disaster Recovery)



\- \*\*Sync (synchronous) replication\*\*

\- \*\*One DNS name\*\* — automatic app failover to standby

\- Increases availability

\- Failover happens in case of:

&#x20; - Loss of AZ

&#x20; - Loss of network

&#x20; - Instance or storage failure

\- \*\*No manual intervention\*\* needed in applications

\- \*\*Not used for scaling\*\* (it's purely for DR, not for handling more read traffic)



> \*\*NOTE:\*\* Read Replicas can be set up as Multi-AZ for Disaster Recovery (DR)



\---



\## RDS Read Replicas — for Read Scalability



\- Up to \*\*15 read replicas\*\*

\- Can be within AZ, cross-AZ, or \*\*cross-Region\*\*

\- Replication is \*\*ASYNC\*\*, so reads are \*\*eventually consistent\*\*

\- Replicas can be \*\*promoted to their own standalone DB\*\*

\- Applications must \*\*update the connection string\*\* to leverage read replicas



\### Use cases



\- You have a production DB that is taking on normal load

\- You want to run a reporting application to run some analytics

\- You can create a read replica to run the new workload there

\- The production application is \*\*unaffected\*\*

\- Read replicas are used for \*\*SELECT (read)\*\* statements only — \*\*not\*\* INSERT, UPDATE, DELETE



\### Network cost



\- In AWS, there's a \*\*network cost\*\* when data goes from one AZ to another

\- For RDS read replicas \*\*within the same region\*\*, you \*\*don't pay\*\* that fee



| Scenario | Cost |

|---|---|

| us-east-1a → us-east-1b (same region) | Free |

| us-east-1a → eu-west-1 (cross region) | Paid ($$$) |



\---



\## RDS — Storage Auto Scaling



\- Helps you increase storage on your RDS DB instance \*\*dynamically\*\*

\- When RDS detects you are running out of free database storage, it \*\*scales automatically\*\*

\- Avoid manually scaling your database storage

\- You have to set a \*\*Max Storage Threshold\*\* (max limit of DB storage)



\### Automatically modifies storage if:



\- Free storage is \*\*less than 10%\*\* of allocated storage

\- Low storage lasts at least \*\*5 minutes\*\*

\- \*\*6 hours\*\* have passed since last modification



\- Useful for applications with \*\*unpredictable workloads\*\*

\- Supports \*\*all RDS database engines\*\*



\### Network cost



\- In AWS, there's a \*\*network cost\*\* when data goes from one AZ to another

\- For RDS read replicas \*\*within the same region\*\*, you \*\*don't pay\*\* that fee



| Scenario | Cost |

|---|---|

| us-east-1a → us-east-1b (same region) | Free |

| us-east-1a → eu-west-1 (cross region) | Paid ($$$) |



\---



\## RDS — Storage Auto Scaling



\- Helps you increase storage on your RDS DB instance \*\*dynamically\*\*

\- When RDS detects you are running out of free database storage, it \*\*scales automatically\*\*

\- Avoid manually scaling your database storage

\- You have to set a \*\*Max Storage Threshold\*\* (max limit of DB storage)



\### Automatically modifies storage if:



\- Free storage is \*\*less than 10%\*\* of allocated storage

\- Low storage lasts at least \*\*5 minutes\*\*

\- \*\*6 hours\*\* have passed since last modification



\- Useful for applications with \*\*unpredictable workloads\*\*

\- Supports \*\*all RDS database engines\*\*


# AWS Database+aurora 

## 1. Amazon RDS & RDS Custom
### RDS vs. RDS Custom
* **Standard RDS:** The entire database instance and underlying Operating System (OS) are completely managed by AWS.
* **RDS Custom:** * Available for managed **Oracle** and **Microsoft SQL Server** databases.
  * Automates setup, operations, and scaling, but grants you **full administrative/root access** to the underlying OS and database.
  * Allows you to configure custom settings, install patches, enable native features, and access the underlying EC2 instance via SSH or AWS Systems Manager Session Manager.
  * *Tip:* It is recommended to deactivate automation mode before performing customization or manual modifications, and always take a DB snapshot beforehand.

### RDS Backups
* **Automated Backups:**
  * Daily full backups of the database.
  * Transaction logs are backed up by RDS every **5 minutes**.
  * Gives you the ability to restore to any point in time (Point-in-Time Recovery) with the oldest backup being from 5 minutes ago.
  * Retention period: **1 to 35 days**. Setting retention to `0` disables automated backups.
* **Manual DB Snapshots:**
  * Manually triggered by the user.
  * Retention persists as long as you want (not deleted automatically).
* **Cost Trick:** On a stopped RDS database, you **still pay for storage**. If you plan on stopping a database for a long time, it is more cost-effective to take a snapshot, delete the instance, and restore it later when needed.

### RDS & Aurora Security
* **At-Rest Encryption:** Database master and replicas encryption uses **AWS KMS** (must be defined at launch time). If the master is not encrypted, the read replicas cannot be encrypted. To encrypt an unencrypted database, you must take a DB snapshot, copy it to an encrypted snapshot, and restore it as encrypted.
* **In-Transit Encryption:** TLS is ready by default using the AWS TLS root certificates client-side.
* **IAM Authentication:** You can use IAM roles to connect to your database instead of utilizing standard username/password combinations.
* **Security Groups:** Control network access to your RDS / Aurora DB instances.
* **Access Restraints:** No SSH is available except when utilizing RDS Custom.
* **Audit Logs:** Can be enabled and sent directly to Amazon CloudWatch Logs for longer retention.

### RDS & Aurora Restore Options
* **Basic Restore:** Restoring an RDS/Aurora backup or snapshot always creates a completely **new** database instance.
* **Restoring MySQL RDS from S3:** 1. Create a backup of your on-premises database.
  2. Store the backup file in Amazon S3 (Object Storage).
  3. Restore the backup file directly onto a new RDS instance running MySQL.
* **Restoring MySQL Aurora Cluster from S3:**
  1. Create a backup of your on-premises database using **Percona XtraBackup**.
  2. Store the backup files in Amazon S3.
  3. Restore the backup files onto a new Amazon Aurora cluster running MySQL.

---

## 2. Amazon Aurora
### Core Architecture & Features
* **Proprietary Technology:** AWS cloud-optimized, closed-source relational database engine.
* **Compatibility:** Fully supports **PostgreSQL** and **MySQL** (your drivers and application code will work without modifications).
* **Performance:** Claims up to **5x** performance improvement over standard MySQL on RDS, and **3x** over standard PostgreSQL on RDS.
* **Auto-Expanding Storage:** Storage automatically grows in increments of **10 GiB**, scaling seamlessly up to **256 TB**.
* **Database Migrations:** Requires no to little code changes (same OS, SQL engine, and server client driver). The same application can be used after migration of your DB using **AWS SCT (Schema Conversion Tool) & DMS (Database Migration Service)**.

### High Availability & Read Scaling
* **Data Replication:** Maintains **6 copies of your data across 3 Availability Zones (AZs)**.
  * **4 copies out of 6** are required for Write quorum.
  * **3 copies out of 6** are required for Read quorum.
* **Self-Healing:** Uses peer-to-peer replication to automatically fix degraded storage blocks. Storage is striped across hundreds of volumes.
* **Instance Scaling:** * Only **one** Aurora instance acts as the Writer (Master) handling all writes.
  * Up to **15 Aurora Read Replicas** can be provisioned to serve read workloads.
  * Replication process is fast with sub-10ms replica lag (much faster than standard MySQL engines).
  * Automated failover to a read replica happens in **less than 30 seconds**. Failover is nearly instantaneous and HA native.
* **Cost Factor:** Aurora generally costs roughly 20% more than standard RDS, but offers significantly higher efficiency and scalability.

### Aurora Clustering & Endpoints
* **Writer Endpoint:** Points directly to the Master instance to handle write operations.
* **Reader Endpoint:** Provides connection load balancing across all available Read Replicas to scale read volumes.
* **Custom Endpoints:**
  * Allows you to define a specific subset of Aurora instances as a custom endpoint.
  * Useful for isolating specific workloads (e.g., running heavy analytical queries on dedicated replicas).
  * *Note:* The general Reader Endpoint is typically not used for those specific client connections once custom endpoints are established.
* **Aurora Database Cloning:**
  * Creates a new Aurora DB cluster from an existing one using a **Copy-on-Write** protocol.
  * Initially, the new cluster shares the same physical data volumes as the original cluster, making it extremely fast and cost-efficient (no physical data copying happens at creation).
  * Additional storage is allocated and copied over only when updates/mutations are made to the new cluster data.
  * Ideal and highly useful for quickly creating a "Staging database" from a "Production database" without impacting the performance of the production cluster.
  * It is much faster than running a standard snapshot and restore.
* **Aurora Backtrack:** Allows you to restore data to any exact point in time rewinding the DB backwards without utilizing standard snapshot restores.

### Aurora Serverless
* Provides automated database instantiation, auto-scaling, and high availability (Multi-AZ) based on actual application usage.
* Ideal for infrequent, intermittent, or highly unpredictable workloads.
* No capacity planning required; priced on a **pay-per-second** basis, optimizing costs for variable workloads.

### Global Aurora
* **Cross-Region Read Replicas:** Simple to set up and highly useful for global disaster recovery.
* **Aurora Global Database (Recommended):**
  * Consists of 1 Primary Region (Read/Write) and up to **10 Secondary (Read-Only) Regions**.
  * Replication lag between regions is typically **less than 1 second**.
  * Supports up to **16 Read Replicas per secondary region**, helping decrease latency globally.
  * Promoting a secondary region during disaster recovery features an RTO of **< 1 minute**.

### Aurora Machine Learning
* Enables SQL-based machine learning predictions directly within your application queries.
* Simple, optimized, and secure integration between Aurora and AWS ML services.
* **Supported Services:**
  * **Amazon SageMaker:** Use with any custom ML model.
  * **Amazon Comprehend:** Used for built-in sentiment analysis.
* **Use Cases:** Fraud detection, ad targeting, sentiment analysis, product recommendations. (Requires no prior ML experience from the developer).

### Babelfish for Aurora PostgreSQL
* Enables Aurora PostgreSQL to natively understand commands targeted for **Microsoft SQL Server (T-SQL)**.
* Allows legacy MS SQL Server-based applications to work directly with Aurora PostgreSQL with minimal to no modification.

---

## 3. Amazon RDS Proxy
* A fully managed, highly available database proxy for RDS and Aurora.
* **Connection Pooling:** Allows applications to pool and share database connections, improving database efficiency by reducing the stress on DB resources (e.g., CPU, RAM) and minimizing open connections and timeouts.
* **Serverless Scale:** Great for serverless architectures (like AWS Lambda) that scale up rapidly.
* **Failover Optimization:** Reduces RDS and Aurora failover times by up to **66%**.
* **Supported Engines:** Supports RDS (MySQL, PostgreSQL, MariaDB, MS SQL Server) and Aurora (MySQL, PostgreSQL).
* **Code Modification:** Requires no code changes for most applications (uses the same database drivers).
* **Security & Access Control:**
  * Enforces IAM Authentication for database connections.
  * Securely stores application credentials in **AWS Secrets Manager**.
  * **RDS Proxy is never publicly accessible**; it must be accessed strictly from within your VPC.

---

## 4. Amazon ElastiCache
### Overview
* The same way RDS is used to get a managed relational database, ElastiCache is used to get a managed **Redis** or **Memcached** deployment.
* Caches are in-memory databases featuring really high performance and low latency.
* Helps reduce the load off of databases for read-intensive workloads.
* Helps make your application stateless by handling session or OS maintenance/state.
* AWS takes care of OS maintenance, patching, optimizations, setup, and configurations.

### Solution Architectures & Caching Patterns
* **Cache-Aside / DB-Cache Architecture:**
  * Application queries ElastiCache first. 
  * **Cache Hit:** Data is retrieved directly from ElastiCache.
  * **Cache Miss:** If data is not available, the application gets it from RDS and writes/stores it into ElastiCache so it's available next time.
  * Helps relieve heavy query loads off RDS.
  * *Requirement:* Cache must have an **invalidation strategy** to make sure only the most current data is used in there.
  * *Trade-off:* Using ElastiCache involves heavy application code changes.
* **Lazy Loading:** * All read data is cached; data can become stale in cache if not managed properly.
* **Write-Through:**
  * Adds or updates data in the cache when written to a DB (results in no stale data).
* **Session Store:**
  * Stores temporary session data in a cache (frequently utilizing **TTL features**).
  * **Workflow:** User logs onto any of the application instances -> The application writes the session data into ElastiCache -> The user hits another instance of our application -> The instance retrieves the data from ElastiCache, and the user is already logged in.

### ElastiCache: Redis vs. Memcached
#### REDIS (Like a Database)
* Supports **Multi-AZ deployments with Auto-Failover**.
* Uses **Read Replicas** to scale reads and ensure High Availability.
* Data durability features using **AOF (Append Only File) Persistence** and Backup/Restore capabilities.
* Advanced data structures supported (e.g., Sets, Hashes, and **Sorted Sets**).
  * *Gaming Leaderboards use-case:* Gaming leaderboards are computationally complex. Redis Sorted Sets guarantee both uniqueness and element ordering. Each time a new element is added, it is ranked in real-time, then added in the correct order.

#### MEMCACHED (Pure Cache)
* Multi-node architecture optimized for **partitioning data (Sharding)**.
* **No High Availability** (No built-in replication capabilities).
* Strictly **Non-Persistent**.
* Backup & restore features are limited to Serverless configurations.
* Uses a **multi-threaded architecture**.

### ElastiCache Security
* **Connectivity & Network Type:** IPv4 support. Requires creating a new Subnet Group by defining a VPC ID and selecting the subnets where it is created.
* **IAM Authentication:** ElastiCache supports IAM Authentication for Redis deployments.
* **API Level Security:** IAM policies on ElastiCache are only used for **AWS API-level security** (e.g., control who can create/modify a cluster).
* **Redis AUTH:** * You can set a **"Password / Token"** when you create a Redis cluster.
  * This is an extra level of security for your cache on top of regular Security Groups.
  * Supports SSL for in-flight encryption.
* **Memcached Security:** Supports **SASL-based authentication** (advanced).

---

## 5. Network, Ports & Reference Data

### Important Protocols & Default Ports
| Service / Protocol | Default Port Number |
| :--- | :--- |
| **FTP** | 21 |
| **SSH** | 22 |
| **SFTP** | 22 |
| **HTTP** | 80 |
| **HTTPS** | 443 |

### AWS RDS & Aurora Default Data Ports
| Database Engine | Default Port Number |
| :--- | :--- |
| **PostgreSQL / Aurora (PostgreSQL)** | 5432 |
| **MySQL / Aurora (MySQL)** | 3306 |
| **MariaDB** | 3306 |
| **Oracle RDS** | 1521 |
| **MSSQL Server (Microsoft)** | 1433 |
  

cd ~/aws-saa-notes && cat > section09-rds-aurora-recap.md << 'EOF'
# Section 9 Recap - RDS, Aurora & ElastiCache

## RDS Core
- Managed relational DB service (MySQL, PostgreSQL, MariaDB, Oracle, SQL Server)
- Automated backups, patching, Multi-AZ for HA (standby, not for scaling reads)
- Read Replicas: for read scaling, async replication, can be cross-region
- Storage auto-scaling available

## Encrypting an Existing RDS Instance
- Cannot enable encryption directly on an unencrypted running instance
- Process: snapshot the unencrypted DB -> copy the snapshot with encryption enabled -> restore new encrypted instance from that snapshot -> switch app connections over

## Aurora vs Standard RDS
- Aurora: AWS proprietary, MySQL/PostgreSQL compatible, storage auto-scales up to 128TB
- 6 copies of data across 3 AZs, self-healing storage
- Aurora Replicas (up to 15) vs MySQL replicas (up to 5) - Aurora replicas have lower replication lag
- Aurora has own failover priority tiers; faster failover than standard RDS Multi-AZ
- Aurora Serverless: on-demand autoscaling for unpredictable workloads

## RDS Proxy
- Sits between app and RDS/Aurora, pools and shares DB connections
- Reduces connection overhead (useful for Lambda spiky connections)
- Improves failover time (handles it, app doesn't need to reconnect manually)
- Requires IAM authentication, enforced SSL
- Never publicly accessible - must be accessed from within VPC

## ElastiCache
- In-memory caching (Redis or Memcached) to reduce DB load
- Redis: persistence, replication, Multi-AZ, pub/sub
- Memcached: simple, multi-threaded, no persistence

- reviewing existing S9 material, resuming S10 lectures next session




