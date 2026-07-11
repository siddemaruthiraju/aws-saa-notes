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



