# Week 1 Revision — EC2 Fundamentals, SAA Level & EBS/EFS

**Date:** 2026-06-29 (Day 11 of 118)  
**Sections Covered:** S5, S6, S7  
**Type:** Consolidation day — no new lectures

---

## S5 — EC2 Fundamentals

### Instance Types
- **General Purpose (t3, m5):** balanced compute/memory/network
- **Compute Optimized (c5):** batch, ML inference, gaming servers
- **Memory Optimized (r5, x1):** in-memory DBs, big data processing
- **Storage Optimized (i3, d3):** high IOPS local storage workloads
- **GPU (p3, g4):** ML training, video rendering
- Naming convention: `m5.2xlarge` → family=m, generation=5, size=2xlarge

### Purchasing Options
| Option | Use Case | Discount |
|---|---|---|
| On-Demand | Short-term, unpredictable | None |
| Reserved (1/3yr) | Steady-state workloads | Up to 72% |
| Convertible Reserved | Flexibility to change family/OS | Up to 66% |
| Savings Plans | Flexible, commit to $/hr | Up to 72% |
| Spot | Fault-tolerant batch, stateless | Up to 90% |
| Dedicated Host | Compliance, per-socket licensing | Variable |

### Key Concepts
- **User Data:** Bootstrap script, runs once at first launch as root
- **Placement Groups:**
  - Cluster → low latency, same AZ, same rack (risk: single rack failure)
  - Spread → max 7 instances/AZ, fault isolation
  - Partition → large distributed workloads (Hadoop, Kafka, Cassandra)
- **ENI (Elastic Network Interface):** can attach/detach, stays in same AZ, used for failover
- **Hibernate:** RAM saved to encrypted EBS root, max 60 days, faster resume

---

## S6 — EC2 SAA Level

### Security Groups
- Stateful firewall — inbound rules auto-allow return traffic
- Rules are ALLOW only (no deny rules)
- Can reference other Security Group IDs (not just CIDR)
- One instance can have multiple SGs; one SG can attach to multiple instances

### IP Types
| Type | Behaviour |
|---|---|
| Public IP | Changes on stop/start |
| Private IP | Static within VPC, persists |
| Elastic IP | Static public IP — billed when not attached to running instance |

### IAM Roles for EC2
- Always use IAM roles instead of hardcoding access keys
- Role attached at launch or after — credentials rotated automatically by AWS
- Lab: `DemoRoleForEC2` with `IAMReadOnlyAccess`

### Instance Metadata
- URL: `http://169.254.169.254/latest/meta-data/`
- IMDSv1: no auth required
- IMDSv2: session-oriented, requires token — more secure (exam prefers IMDSv2)

### EC2 Nitro
- Next-gen hypervisor platform
- Higher EBS IOPS ceiling: 64k (vs 32k on older instances)
- Better network performance
- Most modern instance types run on Nitro

### vCPU Optimization
- 1 vCPU = 1 thread on 1 core
- Can reduce vCPU count or disable hyperthreading at launch
- Useful for per-core licensed software (e.g. Windows Server, Oracle DB)

---

## S7 — EBS & EFS

### EBS Volume Types
| Type | IOPS | Use Case |
|---|---|---|
| gp2 | 3 IOPS/GB, max 16k (tied to size) | General workloads |
| gp3 | 3000 baseline, max 16k (independent) | Preferred general SSD |
| io1 / io2 | Up to 64k (io2 Block Express: 256k) | High-perf DBs |
| st1 | Throughput up to 500 MB/s | Big data, Hadoop |
| sc1 | Throughput up to 250 MB/s | Cold data, cheapest |

> HDD types (st1, sc1) **cannot** be used as boot volumes

### EBS Key Facts
- Network drive — can detach and re-attach (same AZ only)
- Snapshots: incremental, stored in S3, can copy across regions
- Encryption: at rest + in transit + snapshots all encrypted; minimal latency impact
- **Multi-Attach:** io1/io2 only, same AZ, up to 16 instances, app must handle concurrent writes

### EC2 Instance Store
- Physically attached to host — not a network drive
- Extremely fast (millions of IOPS)
- **Ephemeral** — data lost on stop, terminate, or hardware failure
- Good for: buffer, cache, temporary data, scratch space

### EFS — Elastic File System
- Managed NFS (Network File System)
- **Linux only** — Windows EC2 cannot mount EFS
- Multi-AZ, multi-instance simultaneously
- Scales automatically — no capacity planning needed
- ~3× more expensive than gp2 EBS

### EFS Storage Classes
- **Standard:** frequent access
- **EFS-IA (Infrequent Access):** lower storage cost, retrieval fee applies
- Lifecycle policy moves files automatically between classes

### EFS Performance & Throughput Modes
- **General Purpose:** default, low latency
- **Max I/O:** higher throughput, higher latency (big data, parallel workloads)
- **Bursting throughput:** scales with file system size
- **Provisioned throughput:** set independently of size
- **Elastic throughput:** auto-scales (recommended default)

---

## Storage Comparison

| Feature | EBS | EFS | Instance Store |
|---|---|---|---|
| Type | Block | File (NFS) | Block |
| Persistence | Yes | Yes | No (ephemeral) |
| Multi-instance | io1/io2 only | Yes | No |
| Multi-AZ | No (per AZ) | Yes | No |
| OS support | Linux + Windows | Linux only | Linux + Windows |
| Speed | High | Moderate | Highest |
| Cost | Moderate | High | Free (included) |

---

## Common Exam Traps

- `st1`/`sc1` HDD → **cannot boot** from these volumes
- EBS volume must be in the **same AZ** as the EC2 instance to attach
- Spot Instances → **2-minute warning** before interruption; not for critical workloads
- Elastic IP → **billed when NOT attached** to a running instance
- Security Groups are **stateful** → no need to create both inbound and outbound rules for same connection
- Hibernate requires **encrypted root EBS** volume + max **60 days**
- EFS is **Linux only** — never mount on Windows EC2
- IMDSv2 is **more secure** than v1 — exam prefers IMDSv2 for new deployments
- gp3 is **preferred over gp2** — better cost/performance, IOPS independent of size

---

*Consolidated on Day 11 — no zero days.*
