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





