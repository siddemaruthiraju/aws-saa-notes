

\# Section 12: Amazon S3 Introduction 



\## What is S3

\- Amazon S3 (Simple Storage Service) is object storage — "infinitely scaling" storage, one of the main building block services of AWS.

\- Common use cases: backup \& storage, disaster recovery, archiving, hybrid cloud storage, application hosting, media hosting, data lakes, static website hosting, software delivery.



\## Buckets

\- S3 stores objects (files) in \*\*buckets\*\* (folders).

\- Bucket names must be \*\*globally unique\*\* across all AWS accounts (not just your account/region) — DNS-compliant naming.

\- Buckets are defined at the \*\*region\*\* level.

\- Naming rules: no uppercase, no underscores, 3–63 chars, must start with lowercase letter or number, can't be formatted as an IP address.



\## Objects

\- Objects have a \*\*key\*\* — the key is the FULL path of the file: `s3://bucket-name/folder1/folder2/file.txt`

\- The key is prefix + object name; there's no real "folder" concept, it's just naming convention (key structure).

\- Object values = content of the body, max size = \*\*5TB\*\*.

\- Upload in a single PUT = max \*\*5GB\*\*; above 5GB must use \*\*multi-part upload\*\*.

\- Objects can have metadata (key/value pairs), tags (up to 10, useful for security/lifecycle), and a version ID (if versioning enabled).



\## Security — Bucket Policy

\- Bucket policies are \*\*JSON-based\*\* resource policies (Resource, Effect, Action, Principal).

\- Used to grant public access, force encryption on upload, grant cross-account access.

\- To make objects public: bucket policy explicitly allowing `s3:GetObject` to `Principal: "\*"`.



\## Static Website Hosting

\- S3 can host static websites (HTML/CSS/JS — no server-side code).

\- Website URL format differs by region; must enable "Static Website Hosting" and make bucket/objects public via bucket policy.

\- If 403/404 errors appear, it's usually a bucket policy misconfiguration blocking public read.



\## Versioning

\- Enabled at the \*\*bucket level\*\*.

\- Same key overwrite → new version ID; old versions retained.

\- Best practice: always enable — protects against unintended deletes/overwrites, easy rollback.

\- Note: objects that existed \*before\* versioning was enabled have version "null".



\## Replication (CRR / SRR)

\- \*\*CRR\*\* = Cross-Region Replication, \*\*SRR\*\* = Same-Region Replication.

\- Requires versioning enabled on both source and destination buckets.

\- Replication is \*\*asynchronous\*\* — near-real-time but not instant.

\- Must set up correct IAM permissions for S3 to replicate.

\- After enabling, only NEW objects are replicated by default (not retroactive, unless using S3 Batch Replication).


# S3 Storage Classes — Quick Reference

## Overview
S3 offers multiple storage classes, trading off availability, retrieval time, and cost. Objects can transition between classes via lifecycle rules.

## Classes (high to low cost)
- **S3 Standard** — high durability, availability, performance. General purpose, frequently accessed data.
- **S3 Intelligent-Tiering** — auto-moves objects between tiers based on access patterns; no retrieval fees.
- **S3 Standard-IA (Infrequent Access)** — lower cost than Standard, but has a retrieval fee. For data accessed less often but needs fast access when needed.
- **S3 One Zone-IA** — same as Standard-IA but stored in a single AZ (not multi-AZ) — cheaper, less resilient (lose the AZ, lose the data).
- **S3 Glacier Instant Retrieval** — archive storage, milliseconds retrieval, for rarely accessed data.
- **S3 Glacier Flexible Retrieval** — retrieval takes minutes to hours (expedited/standard/bulk options).
- **S3 Glacier Deep Archive** — cheapest, retrieval takes hours (12-48h) — long-term archive.
- **S3 Express One Zone** — newest, single-AZ, very low latency (single-digit ms) for latency-sensitive workloads.

## Key exam point
Moving objects between classes = **lifecycle rules**. Can transition automatically based on object age.


# S3 Security — Quick Reference

## Access Control Layers
- **IAM Policies** — attached to users/roles, control what actions they can take on S3.
- **Bucket Policies** — attached to the bucket itself (resource-based), JSON format, can grant cross-account or public access.
- **Object ACLs** (legacy, often disabled) — finer-grained, per-object permissions. Bucket ACLs also exist (rarely used now).

## Bucket Policy Basics
- JSON document with Effect (Allow/Deny), Principal (who), Action (e.g., s3:GetObject), Resource (which bucket/objects).
- To block ALL public access regardless of other settings → **S3 Block Public Access** settings at bucket/account level (safety switch, overrides policies).

## Encryption
- **SSE-S3** — Amazon manages the keys, encryption at rest (AES-256).
- **SSE-KMS** — uses AWS KMS to manage keys, more control + audit trail via CloudTrail.
- **SSE-C** — customer provides their own encryption key, AWS doesn't store it.
- **Client-side encryption** — encrypt before uploading, S3 just stores encrypted blob.

## Key exam point
Bucket Policy vs IAM Policy — if principal is in the SAME account, either works. For cross-account or public access, use Bucket Policy.


# S3 Website Hosting & Versioning — Quick Reference

## Static Website Hosting
- Enable "Static Website Hosting" in bucket properties, specify index document (e.g., index.html) and optional error document.
- Bucket + objects must be made public via bucket policy (s3:GetObject to Principal: "*").
- Website endpoint URL format is region-specific, different from the regular S3 object URL.
- Common gotcha: 403 Forbidden usually means bucket policy isn't allowing public read, not a website-config issue.

## Versioning Recap
- Enabled per-bucket, not per-object.
- Once enabled, can be Suspended but never fully "disabled" (existing versions remain).
- Deleting a versioned object doesn't actually delete it — adds a "delete marker"; the object is recoverable by removing the marker.
- Useful for Project 1 (S3 static site) — enabling versioning protects against accidental overwrites while building/testing the site.



# Section 12: Amazon S3 - Introduction

## Overview
- One of the main building blocks of AWS
- Advertised as **"infinitely scaling"** storage
- Many websites use Amazon S3 as a backbone
- Many AWS services integrate with Amazon S3 as well

## Use Cases
- Backup & storage
- Disaster recovery
- Archive
- Hybrid cloud storage
- Application hosting
- Media hosting
- Data lakes & big data analytics
- Software delivery
- Static website hosting

## Amazon S3 Buckets
- Allows people to store objects (files) in buckets (directories)
- Buckets are defined at the **region level**
- S3 looks like a global service, but buckets are created in a specific region

### Bucket Naming
- Bucket names must be **globally unique** across all AWS accounts (shared global namespace)
- S3 Access Points have their own regional namespace, allowing reuse of the same access point name across different regions

### Naming Constraints
- No uppercase letters, no underscores
- Not formatted as an IP address
- Must start with a lowercase letter or number
- Must **NOT** start with the prefix `xn--`
- Must **NOT** end with the suffix `-s3alias`

## Amazon S3 Objects
- Objects (files) have a **key**
- The key is the **full path** of the object
  - Example: `s3://my-bucket/my-file.txt`
  - Example with prefix: `s3://my-bucket/my-folder1/another-folder1/my-file.txt`
- The key = prefix + object name

### Key Concepts
- There is **no real concept of "directories"** within buckets — just keys with very long names that contain slashes (`/`)
- Object **value** = the content of the body (the actual file data)
- **Max object size** = 5TB
- If uploading a file larger than **5GB**, you must use **Multi-Part Upload**

### Object Metadata & Extras
- **Metadata**: list of text key/value pairs — system or user metadata
- **Tags**: Unicode key/value pairs (up to 10) — useful for security and lifecycle rules
- **Version ID**: assigned if versioning is enabled on the bucket



