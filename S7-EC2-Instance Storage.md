\# EBS Volume (Elastic Block Store)



\## What is EBS?



\- EBS Volume is a \*\*network drive\*\* you can attach to your instances while they run

\- Allows your instances to \*\*persist data\*\*, even after their termination

\- Can only be \*\*mounted to one instance at a time\*\* (at the CCP level)

\- \*\*Bound to a specific Availability Zone\*\* (AZ)

\- Analogy: Think of it as a \*\*"network USB stick"\*\*



\## EBS Volume — Key Properties



\- It is a \*\*network drive\*\* (not a physical drive)

&#x20; - Uses the network to communicate with the instance → may have a \*\*bit of latency\*\*

&#x20; - Can be \*\*detached from one EC2 instance and attached to another quickly\*\*

\- Has a \*\*provisioned capacity\*\* (size in GBs and IOPS)

&#x20; - You are \*\*billed for all provisioned capacity\*\*

&#x20; - You can \*\*increase the capacity\*\* of the drive over time



\## EBS — Availability Zone Constraint



\- EBS Volume is \*\*locked to an Availability Zone\*\*

&#x20; - Example: A volume in `us-east-1a` \*\*cannot\*\* be attached to `us-east-1b`

\- To move a volume across AZs → you need to \*\*snapshot it first\*\*



\---



\## EBS — Delete on Termination Attribute



\- Controls the \*\*EBS behaviour when an EC2 instance terminates\*\*

\- \*\*By default:\*\*

&#x20; - Root EBS Volume → \*\*deleted\*\* (attribute enabled ✅)

&#x20; - Any other attached EBS Volume → \*\*NOT deleted\*\* (attribute disabled ❌)

\- This can be controlled via the \*\*AWS Console\*\* or \*\*AWS CLI\*\*

\- \*\*Use case:\*\* Preserve the root volume when an instance is terminated

## EBS — Extra Points

- EBS volumes are **locked to one AZ** — to move to another AZ, take a snapshot and restore it there
- Snapshots are stored in **S3** (managed by AWS, you don't see the bucket)
- **EBS Snapshot Archive** — move snapshots to a cheaper archive tier, takes 24–72 hrs to restore
- **Recycle Bin for Snapshots** — accidentally deleted snapshots can be recovered if retention rule is set
- **Fast Snapshot Restore (FSR)** — eliminates latency on first use of a restored snapshot (costs extra)
- EBS volumes can be **increased in size** but never decreased
- **GP2 vs GP3** — GP3 you set IOPS and throughput independently; GP2 IOPS is tied to size (3 IOPS/GB)
- **io1/io2** — for databases needing high IOPS (up to 64,000 IOPS)
- **Multi-Attach** — io1/io2 only, attach same volume to multiple EC2s in same AZ
- When an EC2 is terminated, **root EBS is deleted by default** but additional volumes are not

# Section 7: EC2 Instance Storage

## Lecture 56 – EBS Overview

- **EBS (Elastic Block Store)** is a network drive you can attach to your EC2 instances while they run
- Allows instances to persist data even after termination
- **Bound to a specific Availability Zone** — an EBS in us-east-1a cannot be attached to us-east-1b
- To move across AZs, you must first snapshot it

### EBS – Delete on Termination

- By default, the **root EBS volume is deleted** when the instance is terminated
- Any **additional EBS volumes are NOT deleted** by default
- You can control this during instance creation under **Storage → Delete on Termination → set to No**

---

## Lecture 57 – EBS Hands On

1. Launch an EC2 instance
2. Go to **Instance → Storage** — there you can see the volume already created by default
3. Go to **Volumes** → then **Create a Volume**
4. Then **attach** it to your instance and a new volume
5. **Remember:** Instance and new volume should be in the **same region/AZ**

---

## Lecture 58 – EBS Snapshots

- Make a **backup (snapshot)** of your EBS volume at a point in time
- **Not necessary to detach** the volume before taking a snapshot, but **recommended**
- Can **copy snapshots across AZs or Regions**

### Diagram

US-EAST-1A                          US-EAST-1B
[EC2] → [EBS 50GB] → [Snapshot] ──restore──→ [EBS 50GB]

---

## Lecture 59 – EBS Snapshots Hands On

### Creating a Snapshot
1. Go to **Volumes** → select volume → **Actions → Create Snapshot**
2. Go to **Snapshots** → **Actions → Copy Snapshot** (select a region to copy to)

### Snapshot Features

#### 1. EBS Snapshot Archive
- Move a snapshot to an **"archive tier"** that is **75% cheaper**
- Takes **24 to 72 hours** to restore from the archive

#### 2. Recycle Bin for EBS Snapshots
- Set up **retention rules** to retain deleted snapshots so you can recover them after accidental deletion
- Specify retention from **1 day to 1 year**

#### Recycle Bin – Hands On
1. Go to **Snapshots → Actions → Create Snapshot Volume**
2. After creation, go to **Volumes → click on Recycle Bin**
3. **Retention Settings → EBS Snapshots**
4. Add days → Unlock → **Create retention rule**
5. Then go to **Snapshots → delete it**
6. Go to **Recycle Bin → In Resources**
7. Find your deleted snapshot → click on it → **Recover**
8. It will be restored back to Snapshots

#### 3. Fast Snapshot Restore (FSR)
- **Force full initialization** of a snapshot to have **no latency on first use**
- Costs extra **($$$)**

---

## Lecture 60 – AMI Overview

- **AMI = Amazon Machine Image**
- AMI is a **customization of an EC2 instance**
- You add your own: software, configuration, operating system, monitoring
- **Faster boot / configuration time** because all software is **pre-packaged**
- AMIs are **built for a specific region** (but can be copied across regions)

### You Can Launch EC2 Instances From:
| AMI Type | Description |
|---|---|
| **Public AMI** | AWS provided |
| **Your own AMI** | You make and maintain them yourself |
| **AWS Marketplace AMI** | An AMI someone else made (and potentially sells) |

### AMI Process (from an EC2 instance)
1. **Start** an EC2 instance and customize it
2. **Stop** the instance (for data integrity)
3. **Build an AMI** — this will also create EBS snapshots
4. **Launch instances** from other AMIs

### Diagram

US-EAST-1A                              US-EAST-1B
[EC2] ──Create AMI──→ [AMI] ──Launch from AMI──→ [EC2]

---

## Lecture 61 – AMI Hands On

1. **Create an EC2 instance** (with user data from last time — paste EC2 user data)
2. **Launch the instance**
3. Check **Public IP address** — it works
4. Go to **EC2 → Actions → Create Image**
5. Go to **AMI** — your image is created (takes some time for initialization)
6. Go to **EC2 → Launch Instance → My AMIs → Select** your AMI
7. Copy the **EC2 user data** from the previous instance and paste it in user data
8. **Launch instance**
9. Go to instance → copy **Public IP** → paste in URL
10. **It works!**


# EC2 Instance Store

## What is it?
- EBS volumes are **network drives** with good but **"limited" performance**.
- If you need **high performance hardware disk**, use an **EC2 Instance Store** instead.

## Key Characteristic
- **Better I/O performance** than EBS volumes.
- Physically attached to the host hardware running the instance (not network-attached).

## Important: Ephemeral Storage
- Instance Store loses its data **if the instance is stopped** (it's **ephemeral**).
- Data is **only available** for the lifetime of the instance.

## Best Use Cases
- Buffer / cache / scratch data
- Temporary content
- Data that gets replicated or backed up elsewhere

## Risks
- **Risk of data loss** if the underlying hardware fails.
- **Backups and replication are YOUR responsibility** — AWS does not handle this for you.

---
### Quick Comparison: EBS vs Instance Store
| Feature | EBS | Instance Store |
|---|---|---|
| Performance | Good (network drive) | Higher (physically attached) |
| Persistence | Persists after stop | Lost on stop (ephemeral) |
| Use case | General purpose, durable storage | High-performance temp/cache data |
| Backup responsibility | Snapshots available | Fully on you |


# Section 7: EC2 Instance Storage — EBS & EFS

## EBS Volume Types

**6 types**, requiring different use cases:
- **gp2 / gp3 (SSD)**: General purpose SSD — balances price and performance for a wide variety of workloads
- **io1 / io2 Block Express (SSD)**: High performance SSD for mission-critical, low-latency or high-throughput workloads
- **st1 (HDD)**: Low-cost HDD volume designed for frequently accessed, throughput-intensive workloads
- **sc1 (HDD)**: Lowest-cost HDD volume designed for less frequently accessed workloads

EBS volumes are characterized by **size, IOPS (I/O operations per sec), and throughput**.

> When in doubt, always consult the AWS documentation — that's good practice.

Only **gp2/gp3** and **io1/io2 Block Express** can be used as **boot volumes**.

---

### General Purpose SSD (gp2/gp3)

- Cost-effective storage, low latency
- Use cases: system boot volumes, virtual desktops, dev & test environments
- Size range: **1 GiB – 16 TiB**

**gp3:**
- Baseline: **3,000 IOPS** + **125 MiB/s** throughput
- Can increase IOPS up to **16,000** and throughput up to **1,000 MiB/s** independently of size

**gp2:**
- Size and IOPS are linked — **3 IOPS per GiB**
- Small gp2 volumes can burst IOPS to **3,000**
- Max IOPS: **16,000** (reached at 5,334 GiB)

---

### Provisioned IOPS (PIOPS) SSD — io1/io2

- For critical business applications needing **sustained IOPS performance**
- For applications needing **more than 16,000 IOPS**
- Great for database workloads (sensitive to storage performance & consistency)

**io1** (4 GiB – 16 TiB):
- Max PIOPS: **64,000** for Nitro EC2 instances, **32,000** for other instance types
- Can increase PIOPS independently from storage size

**io2 Block Express** (4 GiB – 64 TiB):
- Sub-millisecond latency
- Max PIOPS: **256,000** with an IOPS:GiB ratio of **1,000:1**
- Supports EBS Multi-Attach

---

### Hard Disk Drives (HDD)

- Cannot be used as a boot volume
- Size range: **125 GiB – 16 TiB**

**Throughput Optimized HDD (st1):**
- Low-cost HDD volume designed for frequently accessed, throughput-intensive workloads
- Use cases: big data, data warehouses, log processing
- Max throughput: **500 MiB/s** — Max IOPS: **500**

**Cold HDD (sc1):**
- Lowest-cost HDD volume for infrequently accessed data
- For scenarios where lowest cost is important
- Max throughput: **250 MiB/s** — Max IOPS: **250**

---

## EBS Multi-Attach — io1/io2 family only

- Attach the **same EBS volume** to multiple EC2 instances **within the same AZ**
- Each instance has full read & write permission to the high-performance volume
- Use case: achieve higher application availability in clustered Linux applications (e.g., Teradata)
- Applications must manage concurrent write operations
- Up to **16 EC2 instances** at a time
- Must use a file system that's cluster-aware (not XFS, EXT4, etc. — those aren't cluster-aware)

---

## EBS Encryption

- When you create an encrypted EBS volume:
  - Data **at rest** is encrypted inside the volume
  - All data **in flight** moving between the instance and the volume is encrypted
  - All **snapshots** are encrypted
  - All **volumes created from the snapshot** are encrypted
- Encryption & decryption are handled **transparently** — you have nothing to do
- Encryption has **minimal impact on latency**
- EBS Encryption leverages keys from **KMS (AES-256)**
- Copying an **unencrypted snapshot** allows encryption
- Snapshots of encrypted volumes are encrypted

### Encrypting an Unencrypted EBS Volume
1. Create an EBS snapshot of the volume (unencrypted)
2. Encrypt the EBS snapshot (using copy)
3. Create a new EBS volume from the snapshot (the volume will also be encrypted)
4. Attach the encrypted volume to the original instance

---

## Amazon EFS — Elastic File System

- Managed **NFS (Network File System)** that can be mounted on many EC2 instances
- Works with EC2 instances in **Multi-AZ**
- Highly available, scalable, but **expensive** (~3x gp2 pricing) — pay per use

us-east-1a      us-east-1b      us-east-1c
  [Instance]      [Instance]      [Instance]
       \               |               /
        \______________|______________/
                Security Group

- Use cases: content management, web serving, data sharing, WordPress
- Uses **NFSv4.1** protocol
- Uses Security Group to control access to EFS
- Compatible with Linux-based AMI (not Windows)
- Encryption at rest using KMS
- **POSIX file system** (~Linux) with a standard file API
- File system **scales automatically**, pay-per-use, no capacity planning

### EFS Performance & Storage Classes

**EFS Scale:**
- Supports thousands of concurrent NFS clients, **10 GB+/s** throughput
- Grows to **petabyte-scale** network file system automatically

**Performance Mode** (set at EFS creation time):
- **General Purpose (default)**: latency-sensitive use cases (web server, CMS, etc.)
- **Max I/O**: higher latency/throughput, highly parallel (big data, media processing)

**Throughput Mode:**
- **Bursting**: 1 TB = 50 MiB/s baseline + burst up to 100 MiB/s
- **Provisioned**: Set your throughput regardless of storage size (e.g., 1 GiB/s for 1 TB storage)
- **Elastic**: Automatically scales throughput up or down based on your workload
  - Up to **3 GiB/s** for reads, **1 GiB/s** for writes
  - Used for unpredictable workloads

### EFS Storage Classes

- **Storage Tiers** (lifecycle management feature — move file after N days)
- **Standard**: for frequently accessed files
- **Infrequent Access (EFS-IA)**: lower cost to store, cost to retrieve files
- **Archive**: rarely accessed data (few times each year), **~50% cheaper**
- Implement **lifecycle policies** to move files between storage tiers automatically

### EFS Availability & Durability

- **Standard (Multi-AZ)**: great for production
- **One Zone**: single AZ, great for dev/backup — backup enabled by default, compatible with IA (EFS One Zone IA)
- **Over 90% in cost savings** when combining One Zone + IA


# EBS vs EFS (Elastic File System)

## EBS Volumes — Quick Recap
- An EBS volume can only be attached to one instance at a time (except Multi-Attach io1/io2 volumes).
- EBS volumes are locked at the Availability Zone (AZ) level.
- gp2: IOPS increase as disk size increases.
- gp3 & io1: IOPS can be increased independently of disk size.

### Migrating an EBS Volume Across AZs
1. Take a snapshot of the volume.
2. Restore the snapshot into a new volume in the target AZ.

Note: EBS backups use IO and you shouldn't run them while your application is handling a lot of traffic.

- Root EBS volumes of an instance are terminated by default when the EC2 instance is terminated (this behavior can be disabled).

Diagram:
AZ 1                          AZ 2
[EC2] --- EBS                 [EC2] --- EBS
   |                                       |
Snapshot ---> EBS Snapshot <--- Restore ---

---

## Elastic File System (EFS)
- Managed NFS (Network File System) that can be mounted on hundreds of EC2 instances across multiple AZs.
- Common use case: EFS works well with shared website files (e.g., WordPress).
- Only compatible with Linux instances (POSIX filesystem).
- EFS has a higher price point than EBS.
- Can leverage Storage Tiers for significant cost savings — over 90% in cost savings possible (e.g., moving infrequently accessed files to Infrequent Access tier).

### Remember: EFS vs EBS vs Instance Store
| Storage | Attach Scope | AZ Scope | OS Support |
|---|---|---|---|
| Instance Store | Single instance | Single AZ | Any |
| EBS | Single instance (Multi-Attach = exception) | Single AZ | Any |
| EFS | Hundreds of instances | Across multiple AZs | Linux only |

Architecture:
AZ 1                          AZ 2
[Linux EC2]                   [Linux EC2]
     |                              |
EFS Mount Target             EFS Mount Target
     |                              |
     -------------- EFS -------------

---

## Amazon EFS — Hands-On Lab

### 1. Create the File System
1. Go to Amazon EFS console.
2. Click Create a file system.
3. Select VPC.
4. Choose Customize (instead of quick create).
5. File system type: Regional or One Zone.
6. Enable automatic backups.
7. Lifecycle management — configure as needed.
8. Performance settings:
   - Throughput mode: Enhanced or Elastic
   - Additional settings: General Purpose
9. Network access: select the VPC.
10. Under Security Groups: create one SG, and add that SG group to it.
11. File system policy — optional.
12. Review and Create.
13. Your Amazon EFS is created.

### 2. Create EC2 Instances
1. Create an EC2 instance, configure all settings, add subnet.
2. Under File system: Add EFS, select the EFS file system.
3. Launch the instance.
4. Create a second instance — same setup, but with a different subnet/region (AZ).
5. Launch it.

### 3. Check EFS Network Access
- Go to the EFS file system → Network tab.
- You can see all the Availability Zones and mount targets (Zone 1A, etc.), and their SGs — these SGs are auto-created and attached to EFS.

### 4. Test Cross-Instance File Sharing

On Instance A, run:
ls /mnt/efs/fs1/
sudo su
echo "hello world" > /mnt/efs/fs1/hello.txt
cat /mnt/efs/fs1/hello.txt
Output: hello world

On Instance B, run:
ls /mnt/efs/fs1/
Output: hello.txt
cat /mnt/efs/fs1/hello.txt
Output: hello world

Result: Both instances share the same EFS file system — a file written from Instance A is instantly visible and readable from Instance B, confirming EFS's cross-AZ, multi-instance shared storage.

---





