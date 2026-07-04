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



