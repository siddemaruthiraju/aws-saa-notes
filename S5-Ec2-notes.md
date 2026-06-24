# S5 - EC2 Fundamentals Notes

Date: 23 June 2026

## Key Concepts Learned Today

* EC2 (Elastic Compute Cloud) is AWS's virtual server service — it allows you to rent virtual machines in the cloud instead of buying physical hardware.
* EC2 Instance Types: T = General Purpose (balanced CPU/memory), C = Compute Optimized (high CPU tasks), R = Memory Optimized (databases, caching), I = Storage Optimized (high I/O operations).
* Security Groups act as a virtual firewall for EC2 instances — they control inbound and outbound traffic using rules based on port, protocol, and source IP.
* Key Pairs are used to securely SSH into EC2 instances — AWS stores the public key, you keep the private key (.pem file). Never share the private key.
* EC2 Pricing Models: On-Demand (pay per hour, no commitment), Reserved (1-3 year commitment, up to 72% cheaper), Spot (bid for unused capacity, cheapest but can be interrupted), Dedicated Host (physical server dedicated to you, most expensive).



\# S5 - EC2 Fundamentals Notes



\## Launching an EC2 Instance

\- Name + tags

\- Application - Amazon Machine Image (AMI) - Select OS

\- Instance type

\- Key pair

\- Network settings

\- Configure storage

\- Advanced details

\- Summary → Launch instance



\## EC2 User Data

\- Launching our first updated servers

\- Our web server is launched using EC2 User Data

\- EC2 web app comes when app does

\- Learn how to Stop / Start / Terminate our instance

\- Every time we stop and start the public IP changes



\## EC2 Instance Types Basics

\- Example: m5.2xlarge

&#x20; - m = instance class

&#x20; - 5 = generation (AWS improves over time)

&#x20; - 2xlarge = size within the instance class



\### Instance Type Categories:

\- General Purpose

\- Compute Optimized

\- Memory Optimized

\- Accelerated Computing

\- Storage Optimized

\- HPC Optimized

\- Instance Features

\- Measuring Instance Performance



\## Security Groups

\- Fundamental of network security in AWS

\- Control how traffic is allowed into or out of EC2 instances

\- SG contains only allow rules

\- Can be referenced by IP or by SG

\- Acts as a firewall on EC2 instance



\### They regulate:

\- Access to ports

\- Authorised IP ranges - IPv4 and IPv6

\- Control of inbound network (from internet to instance)

\- Control of outbound network (from instance to others)



\### Key Points:

\- Can be attached to multiple instances

\- Locked down to a region/VPC combination

\- Lives "outside" the EC2 - if traffic is blocked the EC2 won't see it

\- Good to maintain one separate SG for SSH access

\- If application not accessible (timeout) → issue with SG

\- If application gives "connection refused" → app not running or wrong port (NOT SG issue)

\- All inbound traffic is blocked by default

\- All outbound traffic is authorised by default



\### EXAM TRICK - Error Types:

\- \*\*Timeout\*\* → Security Group is blocking the traffic

\- \*\*Connection Refused\*\* → Application issue or wrong port, SG is fine



\### Security Group Rules Example:

| Type | Protocol | Port Range | Source |

|------|----------|------------|--------|

| HTTP | TCP | 80 | 0.0.0.0/0 |

| SSH | TCP | 22 | 122.149.196.85/32 |

| Custom TCP | TCP | 4567 | 0.0.0.0/0 |



\## Classic Ports to Know

\- 22 = SSH (Secure Shell) - log into a Linux instance

\- 21 = FTP (File Transfer Protocol) - upload files into a file share

\- 22 = SFTP (Secure File Transfer) - upload file using SSH

\- 80 = HTTP - access unsecured websites

\- 443 = HTTPS - access secured websites

\- 3389 = RDP (Remote Desktop Protocol) - log into a Windows instance

