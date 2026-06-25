\# S6 - EC2 Solutions Architect Associate Level Notes



\## EC2 Placement Groups

We can specify the EC2 placement strategy using Placement Groups.



\### 1. Cluster Placement Group

\- All EC2 instances in same rack, same AZ

\- Low latency, 10 Gbps network

\- Con: if the rack fails, all instances fail at the same time

\- Use for: HPC, big data jobs that need fast network

\- Big risk: data jobs that nodes that need low latency and high network



\### 2. Spread Placement Group

\- Spread instances across different underlying hardware

\- Can span across multiple AZ in one region

\- Reduced risk of simultaneous failure

\- EC2 instances are on different physical hardware

\- Con: Limited to 7 instances per AZ per placement group

\- Use for: Application that needs to maximize high availability

\- Critical applications where each instance must be isolated from failure



\### 3. Partition Placement Group

\- Up to 7 partitions per AZ

\- Can span across multiple AZ in one region

\- Up to 100s of EC2 instances

\- Instances in a partition do not share racks with instances in other partitions

\- A partition failure can affect many EC2 instances but not other partitions

\- EC2 instances get access to partition information as metadata

\- Use for: HDFS, HBase, Cassandra, Kafka



\### Placement Group Creation (Hands On)

\- EC2 → scroll down → Network \& Security → Placement Group

\- Click Create a Placement Group

\- Give a name tag → Select strategy → Create it

\- We can find this when we create an EC2 instance

\- In Advanced Settings we can find placement group option



\## Elastic Network Interfaces (ENI)

\- Logical component in a VPC that represents a virtual network card

\- ENI have the following attributes:

&#x20; - Primary Private IPv4, one or more secondary IPv4

&#x20; - One Elastic IP (IPv4) per private IPv4

&#x20; - One Public IPv4

&#x20; - One or more Security Groups

&#x20; - A MAC address

\- You can create ENI independently and attach them on the fly (move them)

&#x20; on EC2 instances for failover

\- Bound to a specific Availability Zone (AZ)



\### ENI Hands On

\- Create 2 EC2 instances

\- By default there will be 2 Network Interfaces

\- Create a new network interface with any name

\- Attach it to any instance so you can see 2 Network Interfaces

\- We can detach and add to other instances also

\- If we delete instance the Network Interface will automatically delete

\- But not the one which we have created



\## EC2 Hibernate

\- We know we can Stop and Terminate instances

\- Stop: the data on disk (EBS) is kept intact in the next start

\- Terminate: any EBS volume (root) also set up to be destroyed is lost



\### What happens on first start:

\- OS boots and EC2 User Data script is run



\### Following starts:

\- OS boots up

\- Then your application starts, caches get warmed up and that can take time



\### Introducing EC2 Hibernate:

\- The in-memory (RAM) state is preserved

\- The instance boot is much faster (OS not stopped/restarted)

\- Under the hood: the RAM state is written to a file in the root EBS volume

\- The root EBS volume must be encrypted



\### Use cases:

\- Long running processing

\- Saving the RAM state

\- Services that take time to initialize



\### EC2 Hibernate - Good to Know:

\- Supported instances: C3, C4, C5, I3, M3, M4, R3, R4, T2, T3

\- Instance RAM size: must be less than 150 GB

\- Instance size: not supported for bare metal instances

\- AMI: Amazon Linux 2, Linux AMI, Ubuntu, RHEL, CentOS and Windows

\- Root Volume: must be EBS, encrypted, not instance store and large

\- Available for On-Demand, Reserved and Spot instances

\- An instance cannot be hibernated more than 60 days



\### EC2 Hibernate Hands On:

\- Create an EC2 instance

\- Advanced Settings → Enable Hibernate

\- Storage: Enable Encrypted, KMS Key = Default

\- Then launch EC2 instance

\- Connect to EC2 Instance Connect

\- Check the uptime (greater than 1 min)

\- Now go to Instance State → click on Hibernate Instance

\- The instance is stopped

\- Then again start the instance

\- Open and connect to Instance Connect

\- Now run uptime in it

\- Then it shows like 3 min — where it has stopped it continues from there

