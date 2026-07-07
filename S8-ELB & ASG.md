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
