# S4 - IAM (Identity and Access Management) Notes

- IAM is a global service — not region-specific. Users, groups, and roles 
  created here apply across all AWS regions.

- IAM Users = individual people. IAM Groups = collection of users. 
  Always assign permissions to groups, not directly to users.

- IAM Policies = JSON documents that define permissions. 
  Attached to users, groups, or roles to allow or deny actions on AWS resources.

- IAM Roles = used by AWS services (like EC2, Lambda) to perform actions 
  on your behalf — without storing credentials in code.

- IAM Best Practices: never use root account for daily tasks, 
  enable MFA on root account, follow least privilege principle — 
  give only the permissions needed, nothing more.