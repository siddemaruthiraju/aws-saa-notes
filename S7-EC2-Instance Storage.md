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

