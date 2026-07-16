

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





