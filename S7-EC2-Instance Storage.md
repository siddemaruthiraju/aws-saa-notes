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

