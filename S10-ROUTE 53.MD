\# Section 10: Route 53



\## DNS Basics



\*\*DNS (Domain Name System)\*\* — translates human-friendly hostnames into IP addresses that machines use.



Example: `www.google.com` → `172.217.18.36`



DNS is the backbone of the internet and uses a \*\*hierarchical naming structure\*\*:

`.com` → `example.com` → `www.example.com`



\### DNS Terminology



| Term | Description |

|---|---|

| Domain Registrar | Amazon Route 53, GoDaddy, etc. |

| DNS Records | A, AAAA, CNAME, NS, etc. |

| Zone File | Contains DNS records |

| Name Server | Resolves DNS queries (Authoritative) |

| Top Level Domain (TLD) | `.com`, `.us`, `.in`, `.gov`, `.org` — Non-Authoritative |

| Second Level Domain (SLD) | e.g. `amazon.com` |



\### URL Structure



&#x20;   http://api.www.example.com

&#x20;                 |    |    |

&#x20;                 |    |    +-- SLD (Second Level Domain)

&#x20;                 |    +------- TLD (Top Level Domain)

&#x20;                 +------------ Sub Domain

&#x20;                 

&#x20;                 www.example.com = FQDN (Fully Qualified Domain Name)



\- \*\*Protocol:\*\* `http`

\- \*\*Sub Domain:\*\* `www`

\- \*\*SLD:\*\* `example`

\- \*\*TLD:\*\* `com`

\- \*\*FQDN:\*\* `www.example.com`



\---



\## Hosted Zones



A \*\*container for records\*\* that define how to route traffic to a domain and its subdomains.



> Example: You \*can't\* create a hosted zone for `www.amazon.com`, but you \*can\* create one for `example.com`.



\- \*\*NS\*\* = Name Servers for the hosted zone

\- \*\*Public Hosted Zones\*\* — contains records specifying how to route traffic on the internet (public domain names)

\- \*\*Private Hosted Zones\*\* — contains records specifying how you route traffic within one or more VPCs (private domain names)



\*\*Use cases:\*\*

1\. Public: `company.com` — public-facing application

2\. Private: internal app within one or more VPCs (private domain names)



\*\*Cost:\*\* $0.50/month per hosted zone



\---



\## Route 53 — Records



Records define how you want to route traffic for a domain.



\*\*Each record contains:\*\*

\- \*\*Domain/subdomain name\*\* — e.g. `example.com`

\- \*\*Record type\*\* — e.g. A or AAAA

\- \*\*Value\*\* — e.g. `12.34.56.78`

\- \*\*Routing policy\*\* — how Route 53 responds to queries

\- \*\*TTL\*\* — amount of time the record is cached at DNS resolvers



\### Record Types Route 53 Supports



\- \*\*Must know:\*\* A / AAAA / CNAME / NS

\- \*\*Advanced:\*\* CAA / DS / MX / NAPTR / PTR / SOA

\- \*\*Other:\*\* TXT / SRF / SRV



\### Record Type Details



\- \*\*A\*\* — maps a hostname to an IPv4 address

\- \*\*AAAA\*\* — maps a hostname to an IPv6 address

\- \*\*CNAME\*\* — maps a hostname to another hostname

&#x20; - Target must have an A or AAAA record

&#x20; - \*\*Cannot\*\* create a CNAME record for the top node of a DNS namespace (\*\*Zone Apex\*\*) — e.g. can't create for `example.com`, only for `www.example.com`



\---



\## Amazon Route 53



A highly available, scalable, \*\*fully managed, Authoritative DNS\*\*.



\- \*\*Authoritative\*\* = the customer (you) can update the DNS records

\- Route 53 is also a \*\*Domain Registrar\*\*

\- Ability to check the \*\*health\*\* of your resources

\- The \*\*only AWS service\*\* which provides a \*\*100% availability SLA\*\*



\*\*Why "53"?\*\* — References the traditional DNS port (port 53)



\### Architecture Example



&#x20;   Client → Route 53 (example.com) → AWS Cloud → EC2 Instance (Public IP: 54.12.33.44 / 54.22.33.44)


## Creating Your First Record

- Route 53 → Hosted Zones → your domain (e.g. `stephanemthetutor.com` / your own domain name)
- **Create Record**
  - Record name — Record type — Value
    - Example: `11.22.33.44`
  - TTL: `300`
  - Routing Policy: **Simple Routing**
- Record gets created

### AWS CloudShell — DNS Testing

- `nslookup` → if "command not found":
    sudo yum install -y bind-utils
- Test lookup:
    nslookup test.stephanemthetutor.com
- Or use:
    dig

## Route 53 — EC2 Setup (Multi-Region Failover Demo)

- Create an EC2 instance
  - Same AMI
  - No Key Pair
  - New Security Group
  - Advanced settings → User data → paste script
- Launch instance
- Create another EC2 instance in a different region with the same setup
- Launch a 3rd instance in another region
- Create a Load Balancer in the first region with a new target group
- Check the IP address & DNS of the LB, copy and save it — will be used further

## Route 53 — Records TTL (Time To Live)

**Flow:**
1. Client sends DNS request → `my-app.example.com?`
2. Route 53 responds → `A 12.34.56.78 (with TTL)`
3. Client caches the result for the TTL duration of the record
4. Client sends HTTP request → Web Server
5. Web Server sends HTTP response back to client

**High TTL** (e.g. 24 hr)
- Less traffic on Route 53
- Possibly outdated records

**Low TTL** (e.g. 60 sec)
- More traffic on Route 53 ($$)
- Records are outdated for less time
- Easy to change records

> Except for **Alias records**, TTL is mandatory for each DNS record.

- Create a record with a name and any one instance IP, and set TTL
- Run this in CloudShell — you can see it there

## CNAME vs Alias

### CNAME
- Points a hostname to any other hostname
  - Example: `app.mydomain.com → blabla.anything.com`
- Only works for **non-root domain**

### Alias
- Points a hostname to an AWS resource
- Works for **both root domain and non-root domain**
- Free of charge
- Native health check

## Records

- Maps a hostname to an AWS resource
- DNS extension
- Automatically recognizes changes in a resource's IP address
- Unlike CNAME, it can be used at the **top node of a DNS namespace** (root domain)
- Alias record is always of type **A / AAAA** for AWS resources
- Can't set TTL (auto-set)

### Alias Record Targets
- ELB
- CloudFront distribution
- S3 websites
- VPC Interface Endpoints
- Global Accelerator accelerator
- Route 53 record in the same hosted zone
- API Gateway
- Elastic Beanstalk environments

> You **can't** set an Alias record for an EC2 DNS name.

## Route 53 — Routing Policies (Overview)

- Route 53 responds to DNS queries — it does **not** route any traffic, it only responds to the DNS queries.
- Route 53 supports:
  - Simple
  - Weighted
  - Failover
  - Latency based
  - Geolocation
  - Multi-Value Answer
  - Geoproximity (using Route 53 Traffic Flow feature)

### Simple Routing
- Can't be used with health checks
- **Single Value:** client queries `foo.example.com` → Route 53 returns `A 11.22.33.44`
- **Multiple Value:** client queries `foo.example.com` → Route 53 returns multiple A records (e.g. `11.22.33.44`, `55.66.77.88`, `88.11.22.33`) — client picks one

### Weighted Routing
- Assign weight for HC (works with health checks)
- Controls the % of requests that go to each specific resource
Traffic (%) = Weight for a specific record / Sum of all weights for all records

- Weights don't need to sum up to 100
- DNS records must have the same name & type
- **Use cases:** load balancing between regions/application, testing new application versions
- Assign a weight of 0 to a record to stop sending traffic to a resource
- If all records have weight 0 → all are returned equally (random)

**Example:** 70% / 20% / 10% split across three resources based on weight (70/20/10)

### Latency-Based Routing
- Can be used with health checks
- Redirects to the resource that has the least latency to us
- Super helpful when latency for users is a priority
- Latency is based on traffic between users and AWS Regions
- Germany users may be directed to the US if that's the lowest latency zone worldwide

### Failover Routing (Active–Passive)
- Requires a **mandatory** health check on the primary
- Flow: Client → DNS Request → Route 53 → Failover check
  - If primary EC2 instance is healthy → routes to Primary
  - If primary fails health check → routes to Secondary EC2 instance

### Geolocation Routing
- Different from latency-based!
- Routing is based on the **user's location**
- Specify location by continent, country, or US state (if overlapping, most precise location is selected)
- Should create a "Default" record (in case of no match on location)
- **Use cases:** website localization, restrict content distribution, load balancing
- Can be associated with health checks

### Geoproximity Routing
- Routes traffic to your resources based on the geographic location of users & resources
- Ability to shift more traffic to resources based on a defined **bias**
- Specify bias:
  - To expand (1 to 99) → more traffic
  - To shrink (-1 to -99) → less traffic
- Resources can be:
  - AWS resources (specify AWS region)
  - Non-AWS resources (specify latitude & longitude)
- Must use **Route 53 Traffic Flow** (advanced) to use this feature

### IP-Based Routing
- Routing is based on client's IP address
- You provide a list of CIDRs for your clients & corresponding endpoints/locations
- Use case: optimize network costs, performance

### Multi-Value Routing
- Used when routing traffic to multiple resources
- Route 53 returns multiple values/resources
- Can be associated with health checks
- Up to 8 healthy records are returned for each multi-value query
- Multi-Value is **not** a substitute for having an ELB

## Domain Registrar vs DNS Service

- You buy or register a domain name from a **Domain Registrar**
- Domain Registrar usually provides you with a DNS service to manage your DNS records
- But you can use another DNS service to manage your DNS records instead
Domain Registrar (e.g. GoDaddy) ──manages DNS records──> Route 53

## Route 53 — Health Checks

- HTTP health checks are only for **public** resources
- Health Check → Automated DNS failover
  1. HC monitors an endpoint — is Global, will check
  2. HC monitors other health checks — in "Single HC" combination
  3. HC monitors CloudWatch Alarms (full control)
- Health checks are integrated with CloudWatch metrics

### Health Checks — Hands On
Route 53
↓
Health Checks
↓
Create HC
↓

Name
Resource: Endpoints / Calculated HC / CloudWatch Alarm
IP address / Path
Created HC

## Route 53 — Hybrid DNS

- By default, Route 53 Resolver automatically:
  - Resolves local domain names for EC2
  - Resolves records in Private Hosted Zones
  - Resolves public name servers
- **Hybrid DNS** — resolving DNS queries between VPC (Route 53 Resolver) and network (other DNS resolvers)
- Network can be:
  - VPC itself / peered VPC
  - On-premises network (via AWS VPN)

### Resolver Endpoints

- **Inbound Endpoints** — allows your on-prem DNS resolvers to resolve domain names for AWS resources & records in Private Hosted Zones
- **Outbound Endpoints** — Route 53 Resolver forwards DNS queries to your on-prem DNS resolvers






