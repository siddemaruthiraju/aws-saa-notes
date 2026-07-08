\# Section 8 — High Availability \& Scalability (ELB \& ASG)



\## Scalability \& High Availability



\- \*\*Scalability\*\* means an application/system can handle greater load by adapting.

\- Two kinds of scalability:

&#x20; - \*\*Vertical Scalability\*\*

&#x20; - \*\*Horizontal Scalability\*\* (= elasticity)

\- Scalability is linked to, but different from, High Availability.

\- To understand the distinction, it helps to think of a call center as an example.



\---



\## Vertical Scalability



\- Vertical = increasing the \*\*size\*\* of the instance (scale up / down).

\- Example: your application runs on a `t2.micro`. Scaling it vertically means running it on a `t2.large` instead.

\- Vertical scalability is very common for \*\*non-distributed systems\*\*, such as a database.

\- `RDS` and `ElastiCache` are services that can scale vertically.

\- There's usually a limit to how much you can vertically scale (hardware limit).

\- Range example:

&#x20; - From: `t2.nano` — 0.5 GB RAM, 1 vCPU

&#x20; - To: `u-12tb1.metal` — 12.3 TB RAM, 448 vCPUs



\## Horizontal Scalability



\- Horizontal = increasing the \*\*number\*\* of instances/systems for your application (scale out / in).

\- Horizontal scaling implies \*\*distributed systems\*\*.

\- Very common for web applications and modern applications.

\- Easy to scale horizontally thanks to cloud offerings such as Amazon EC2.

\- Achieved via:

&#x20; - Auto Scaling Group (ASG)

&#x20; - Load Balancer



\## High Availability



\- Usually goes hand in hand with horizontal scaling.

\- High Availability means running your application/system in \*\*at least 2 data centers\*\* (AZs) to survive a data center loss.

\- Can be \*\*passive\*\* (e.g., RDS Multi-AZ).

\- Can be \*\*active\*\* (e.g., horizontal scaling across AZs).



\---



\## High Availability \& Scalability for EC2



\- \*\*High Availability\*\* — run instances for the same application across multiple AZs:

&#x20; - Auto Scaling Group: multi-AZ

&#x20; - Load Balancer: multi-AZ



\---



\## Elastic Load Balancing (ELB)



\- \*\*Load Balancers\*\* are servers that forward traffic to multiple downstream servers (e.g., EC2 instances).



\### Why use a Load Balancer



\- Spread load across multiple downstream instances.

\- Expose a single point of access (DNS) to your application.

\- Seamlessly handle failures of downstream instances.

\- Do regular health checks on your instances.

\- Provide SSL termination (HTTPS) for your websites.

\- Enforce stickiness with cookies.

\- High availability across zones.

\- Separate public traffic from private traffic.



\### ELB (Elastic Load Balancing)



\- ELB is a \*\*managed load balancer\*\*.

\- AWS guarantees it will be working.

\- AWS takes care of upgrades, maintenance, and high availability.

\- AWS provides only a few configuration knobs.

\- It costs less to set up your own load balancer, but it takes a lot more effort on your end.

\- It is integrated with many AWS offerings/services:

&#x20; - EC2, EC2 Auto Scaling Group, Amazon ECS

&#x20; - AWS Certificate Manager (ACM), CloudWatch

&#x20; - Route 53, AWS WAF, AWS Global Accelerator


---

## Health Checks

- Health checks are crucial for load balancers.
- It is a way for a load balancer to know if an instance is available to reply to requests.
- The health check is done on a port and a route (`/health` is common).
- If the response is not `200 (OK)`, then the instance is deemed **unhealthy**.

Example health check configuration:
- **Protocol:** HTTP
- **Port:** 4567
- **Endpoint:** `/health`

---

## Types of Load Balancer on AWS

AWS has 4 kinds of managed Load Balancers:

1. **Classic Load Balancer (CLB)** — 2009
   - Supports: HTTP, HTTPS, TCP, SSL

2. **Application Load Balancer (ALB)** — 2016 (new generation)
   - Supports: HTTP, HTTPS, WebSocket

3. **Network Load Balancer (NLB)** — 2017 (new generation)
   - Supports: TCP, TLS (secure TCP), UDP

4. **Gateway Load Balancer (GWLB)** — 2020
   - Operates at Layer 3 (Network Layer) — IP Protocol

### Notes

- It's generally better to use the newer generation load balancers (ALB, NLB, GWLB) as they provide more features.
- Load Balancers can be set up as:
  - **Internal** (private)
  - **External** (public)
- ELBs use **Security Groups** to control inbound/outbound traffic.


# Section 8 — Elastic Load Balancing (ELB) Notes

## Application Load Balancer (ALB) — Overview
- ALB operates at Layer 7 (HTTP/HTTPS)
- Load balances across multiple targets in a target group, and can load
  balance to multiple applications on the same machine (e.g. containers)
- Supports HTTP/2 and WebSocket
- Supports redirects (e.g. HTTP to HTTPS)
- Routing tables can route to different target groups based on:
  - Path in URL
  - Hostname in URL
  - Query string / headers
- Great fit for microservices and container-based applications
  (e.g. Docker, Amazon ECS)
- Has a port mapping feature to redirect to a dynamic port in ECS
- In comparison, Classic Load Balancer needs multiple CLBs per application
  (ALB needs just one, routing to many target groups)

## Target Groups
- Can point to: EC2 instances (HTTP), ECS tasks (HTTP), Lambda functions
  (HTTP request is translated into a JSON event)
- IP addresses used as targets must be private IPs
- ALB can route to multiple target groups
- Health checks are performed at the target group level

## ALB — Hands-On Steps
1. Create 2 EC2 instances
   - Name the instances
   - Use EC2 user data (fundamental code) in the instance summary
   - Launch instances
   - Check that the private IP is working
2. Launch the Load Balancer
   - Choose Application Load Balancer
   - Name it (e.g. Demo-ALB)
   - Set to Internet-facing, IPv4
   - Network mapping: deploy in all Availability Zones/locations
   - Create a new Security Group
     - Name it (e.g. Demo-SG-load-balancer)
     - Description: "Allows HTTP into ALB"
     - Inbound rule: HTTP — Anywhere
3. Create a Target Group
   - Target type: Instances
   - Group name (e.g. Demo-tg-alb)
   - Protocol: HTTP
   - Configure health checks
   - Next → Register targets → select both instances
   - Target group created
4. Back in the Load Balancer setup
   - Select that target group
   - Listeners and routing: HTTP, port 80
   - Create the Load Balancer
5. Once created, copy the LB's DNS name and test it in browser
   - You'll see "Hello World" but the IP keeps changing on refresh
     (proof it's load balancing across instances)

## Listener Rules (Advanced)
- Go to Load Balancers → your LB → select → HTTP:80 listener → Listener Rules
- Add Rule:
  - Name it
  - Conditions: Path, or Host
  - Action: Return fixed response (e.g. 404 Not Found, custom error)
  - Set rule priority: 1 = highest, 5 = lowest
  - Create rule
- Copy DNS name + add the path/error condition to the URL to see the
  custom message you configured

## Query String / Parameter-Based Routing
- One ALB can route to multiple target groups based on query parameters:
  - Target Group 1: AWS EC2-based
  - Target Group 2: On-premises, private IP target

## Good to Know
- Fixed hostname — app servers don't see the client's IP directly
- The true client IP is inserted in the header:
  X-Forwarded-For, X-Forwarded-Port, X-Forwarded-Proto
- Flow: Client IP (e.g. 12.34.56.78) <-> Load Balancer IP (private IP)
  <-> EC2 instance — this is "connection termination" at the LB

---

# Network Load Balancer (NLB)
- Operates at Layer 4 (TCP/UDP)
- Forwards TCP and UDP traffic to your instances
- Handles millions of requests per second
- Ultra-low latency
- Has ONE static IP per Availability Zone, and supports assigning an
  Elastic IP (useful for whitelisting by IP)
- Used for extreme performance TCP/UDP use cases
- NLB (v2): Layer 4, TCP-based traffic
  - Routes based on TCP rules to a target group for a web/user application
    (with health checks)
  - Can also route HTTP traffic to a target group for a search application
    (with health checks)

## NLB — Target Groups
- Targets: EC2 instances, IP addresses (must be private IPs)
- Same concept as ALB target groups, but at Layer 4

## NLB — Hands-On Steps
1. Create the NLB — same process as ALB up to creating a new Security Group
2. Create a new Target Group
   - Protocol: TCP, port 80
   - Add health check settings
   - Add timing intervals
3. After creating, check the DNS name — it will NOT work yet
4. Configure the Security Group of the instances to allow TCP & HTTP
   traffic from the Load Balancer
5. Then the DNS name will work correctly

## NLB Hands-On — Part 2 (Failover Test)
- Stop one instance and check the target group — you'll see the health
  check reflect the unhealthy target
- You can then see only 1 IP address responding
- When you start the instance again, you'll see traffic switching
  between both addresses (both healthy again)
- You can check via the instance directly AND via the load balancer —
  going through the Load Balancer is more secure
- To lock it down: go to the instance's Security Group
  - Change inbound rule: delete the open HTTP rule
  - Add new rule: HTTP — source = the Load Balancer's Security Group
  - Now only the Load Balancer can reach the instance directly

---

# Gateway Load Balancer (GWLB)
- Deploys, scales, and manages a fleet of 3rd-party network virtual
  appliances in AWS
  - Examples: firewalls, Intrusion Detection & Prevention Systems (IDS/IPS),
    Deep Packet Inspection systems, payload manipulation tools
- Operates at Layer 3 (Network layer) — works with IP packets
- Combines two functions:
  - Transparent network gateway — single entry/exit point for all traffic
  - Load balancer — distributes traffic across your fleet of virtual
    appliances
- Uses the GENEVE protocol on port 6081

## GWLB — Target Groups
- Targets: EC2 instances
- IP addresses used as targets must be private IPs


# Section 8: Elastic Load Balancing & Auto Scaling Groups

## ELB Types – Cross-Zone Load Balancing (Default Settings)

- **Application Load Balancer (ALB)**
  - Cross-zone load balancing enabled by default
  - No charges for inter-AZ data transfer
- **Network Load Balancer (NLB) & Gateway Load Balancer (GWLB)**
  - Cross-zone load balancing disabled by default
  - You pay charges ($) for inter-AZ data transfer if enabled
- **Classic Load Balancer (CLB)**
  - Cross-zone load balancing disabled by default
  - No charges for inter-AZ data transfer if enabled

### Hands-on: Enable Cross-Zone Load Balancing
1. Create a Load Balancer
2. Go to it → **Attributes** tab
3. Click **Edit**
4. Turn on **Cross Zone Load Balancing**
5. **Save changes**

## Sticky Sessions (Session Affinity)

- If possible, implement stickiness so the same client is always redirected to the same instance behind a load balancer
- Works for: Classic LB, Application LB, Network LB
- The cookie used for stickiness has an expiration date you control
- Use case: make sure the user doesn't lose session data
- ⚠️ Enabling stickiness may bring imbalance to the load over the backend EC2 instances

### Cookie Names

**Application-based cookies**
- **Custom cookie**
  - Generated by the target (application)
  - Can include any custom attributes required by the application
  - Cookie name must be specified individually for each target group
  - ❌ Don't use the reserved names: `AWSALB`, `AWSALBAPP`, `AWSALBTG`
- **Application cookie**
  - Generated by the load balancer
  - Cookie name is `AWSALBAPP`

**Duration-based cookie**
- Cookie generated by the load balancer
- Cookie name:
  - `AWSALB` → for ALB
  - `AWSELB` → for CLB

### Hands-on: Create a Target Group
1. Create a target group
2. Select the target group
3. **Actions** → **Edit target group attributes**
4. Configure **Target selection configuration**
5. Enable **Stickiness**
6. **Save changes**

### Cross-Zone Load Balancing (recap)
- With cross-zone load balancing, each load balancer node distributes traffic evenly across all registered instances, in all Availability Zones

## SSL/TLS – Basics

- An SSL certificate allows traffic between your clients and your load balancer to be encrypted in transit (in-flight encryption)
- **SSL** = Secure Sockets Layer, used to encrypt connections
- **TLS** = Transport Layer Security, a newer version of SSL
- Nowadays, TLS certificates are mainly used, but people still refer to it as "SSL"
- Public SSL certificates are issued by Certificate Authorities (CA): Comodo, Symantec, GoDaddy, GlobalSign, DigiCert, Let's Encrypt, etc.
- SSL certificates have an expiry date (you set) and must be renewed

### Load Balancer – SSL Certificate Flow
User --HTTPS (encrypted, over WWW)--> Load Balancer --HTTP (over private VPC)--> EC2 Instance

- The load balancer uses an X.509 certificate (SSL/TLS server certificate)
- Manage certificates using **ACM** (AWS Certificate Manager)
- Alternatively, you can create/upload your own certificate

## SSL – Server Name Indication (SNI)

- SNI solves the problem of loading multiple SSL certificates onto one web server
- It's a newer protocol that requires the client to indicate the hostname of the target server in the initial SSL handshake
- The server then finds the correct certificate, or returns a default one
- ✅ Only works for **ALB & NLB**, and **CloudFront**
- ❌ Does not work for **CLB** (older generation)

### Hands-on
1. Load balancers → select LB
2. **Add listener**
3. Add certificate

## Connection Draining / Deregistration Delay

Same feature, different naming:

| Load Balancer | Feature Name |
|---|---|
| CLB | Connection Draining |
| ALB / NLB | Deregistration Delay |

- Time to complete "in-flight requests" while the instance is de-registering or unhealthy
- Stops sending new requests to the EC2 instance that is de-registering
- Value range: 1 – 3600 sec (default: 300 sec)
- Can be disabled (set value to 0)
- Set to a low value if your requests are short

---

# Auto Scaling Groups (ASG)

## Why ASG?

- In real life, load on your websites/applications can change
- In the cloud, you can create and get rid of servers very quickly
- **Goal of an ASG**: scale out (add EC2 instances) to match increased load, and scale in (remove EC2 instances) to match decreased load
- Ensure we have a minimum and maximum number of EC2 instances running
- Automatically register new instances to a load balancer
- Re-create an EC2 instance in case a previous one is terminated (e.g., if unhealthy)
- ASGs themselves are free — you only pay for the underlying EC2 instances

- **Min capacity**: minimum number of instances
- **Desired capacity**: current target number of instances (scales as needed)
- **Max capacity**: maximum number of instances allowed

## ASG with Load Balancer
Users --> Elastic Load Balancer --> Auto Scaling Group --> [EC2] [EC2] [EC2]


- ELB can check the health of your EC2 instances (and ASG replaces unhealthy ones)

## ASG Attributes

- A **Launch Template** (older "Launch Configurations" are deprecated)
  - AMI + Instance type
  - EC2 User Data
  - EBS Volumes
  - Security Group
  - SSH Key Pair
  - IAM Roles for your EC2 instances
  - Network + Subnets information
  - Load Balancer information
  - Min / Max size, Initial (desired) capacity

## ASG – Scaling Based on CloudWatch Alarms

- It's possible to scale an ASG based on CloudWatch alarms
- An alarm monitors a metric (e.g., Average CPU)
- Metrics such as Average CPU are computed for the overall ASG instances
- Based on the alarm, we can create:
  - **Scale-out policies (+)** — add instances
  - **Scale-in policies (−)** — remove instances

## ASG Hands-on — Creating an Auto Scaling Group

1. **EC2 → Auto Scaling Groups → Create**
2. **Name**
3. **Launch template** → Create a template
   - Same as a normal instance launch template, just add User Data under "Advanced details"
4. **Choose instance launch options**
   - Reset to launch template (if needed)
   - Network → select all AZs
5. **Integrate with other services**
   - Attach to an existing load balancer → **Attach**
   - VPC → No (use default)
   - Health check → **Turn on ELB health checks**
6. **Configure group size & scaling**
7. **Create Auto Scaling group**

### Verify it's Working
1. Check the instance is running
2. Go to **Target Groups → Targets** (may initially show unhealthy)
3. Change **Desired capacity** to 2
4. Click **Refresh** — you should see 2 instance IPs appear/change

---

## ASG — Scaling Policies

- **Dynamic Scaling**
  - **Target Tracking Scaling**
    - Simple to set up
    - Example: keep average ASG CPU at around 40%
  - **Simple / Step Scaling**
    - When a CloudWatch alarm is triggered (e.g., CPU > 70%) → add 2 units
    - When CloudWatch alarm triggered (e.g., CPU < 30%) → remove units
- **Scheduled Scaling**
  - Anticipate a scaling action based on known usage patterns
  - Example: increase min capacity to 10 at 5 PM on Fridays
- **Predictive Scaling**
  - Continuously forecasts load and schedules scaling ahead of time

### Good Metrics to Scale On
- **CPU Utilization** — average CPU utilization across your instances
- **Request Count Per Target** — ensures the number of requests per EC2 instance is stable
- **Average Network In/Out** — useful if your app is network-bound
- **Any custom metric** that you push using CloudWatch

## Scaling Cooldowns

- After a scaling activity happens, you enter the **cooldown period** (default: 300 sec)
- During the cooldown period, the ASG will not launch or terminate additional instances
- Use a ready-to-use AMI to reduce configuration time in order to serve requests faster and reduce the cooldown period

### Hands-on: Create a Scaling Policy
1. **EC2 → Auto Scaling Groups → your group → select it**
2. **Automatic scaling** tab
3. Choose:
   - Scheduled actions
   - Predictive scaling policies
   - Dynamic scaling policy
     - Simple scaling
     - Step scaling
     - Target tracking

### Hands-on: Create a Target Tracking Policy
1. **Policy type**
2. **Name**
3. **Target value**
4. **Create Policy**

### Verify the Policy is Working
1. Change desired capacity in ASG details (Min / Max = e.g. 3)
2. Connect to the instance (via EC2 Instance Connect)
3. Use **stress** commands to stress the CPU
4. Check the **Activity** tab — CPU usage should show high
5. Select the policy and watch it in **CloudWatch Alarms** — alarms should trigger, launching new instances