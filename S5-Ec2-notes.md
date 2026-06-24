# S5 - EC2 Fundamentals Notes
Date: 23 June 2026

## Key Concepts Learned Today

- EC2 (Elastic Compute Cloud) is AWS's virtual server service — it allows you to rent virtual machines in the cloud instead of buying physical hardware.

- EC2 Instance Types: T = General Purpose (balanced CPU/memory), C = Compute Optimized (high CPU tasks), R = Memory Optimized (databases, caching), I = Storage Optimized (high I/O operations).

- Security Groups act as a virtual firewall for EC2 instances — they control inbound and outbound traffic using rules based on port, protocol, and source IP.

- Key Pairs are used to securely SSH into EC2 instances — AWS stores the public key, you keep the private key (.pem file). Never share the private key.

- EC2 Pricing Models: On-Demand (pay per hour, no commitment), Reserved (1-3 year commitment, up to 72% cheaper), Spot (bid for unused capacity, cheapest but can be interrupted), Dedicated Host (physical server dedicated to you, most expensive).