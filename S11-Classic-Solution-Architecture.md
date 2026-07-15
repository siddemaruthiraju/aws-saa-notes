\# Section 11: Classic Solutions Architecture Discussions



\## Overview

Solutions architects think through many case studies to build architecture intuition. Sample case studies covered:

\- WhatsTheTime.com

\- MyClothes.com

\- MyWordPress.com

\- Instantiating applications quickly

\- Elastic Beanstalk



\---



\## Case Study: WhatsTheTime.com (Stateless Web App)



\*\*Requirements:\*\*

\- Simple purpose: tell the user what time it is

\- Doesn't need a database

\- Should be able to start small and tolerate some downtime initially

\- Must be able to fully scale later with no downtime



\*\*Evolution of the architecture:\*\*

1\. \*\*Single EC2 instance\*\* with an Elastic IP — simplest setup, some downtime acceptable while scaling vertically (upgrading instance type).

2\. \*\*Scaling vertically\*\* — resize the EC2 instance for more capacity; brief downtime during resize while upgrading to a bigger instance.

3\. \*\*Scaling horizontally with high availability\*\* — Elastic/public IP → multiple EC2 instances behind the setup, eventually evolving toward ALB + ASG for full scale with no downtime.



\*\*Key takeaway:\*\* This app is a good example of a \*stateless\* architecture — no session or user data needs to persist on a specific instance, so any instance can serve any request. This makes horizontal scaling straightforward.



\---



\## Developer Problems on AWS

Common pain points developers face when deploying applications:

\- Managing infrastructure

\- Deploying code

\- Configuring databases, load balancers, etc.

\- Scaling concerns



\*\*Most common web architecture pattern:\*\* ALB + ASG (Application Load Balancer + Auto Scaling Group)



\*\*What developers actually want:\*\* just to write code and have it run — portably and consistently across different applications and environments, without managing the underlying infrastructure themselves.



\---



\## Elastic Beanstalk — Overview



\- A \*\*developer-centric view\*\* of deploying an application on AWS.

\- Bundles together all the components needed to run an app: EC2, ASG, ELB, RDS, etc.

\- It is a \*\*managed service\*\*:

&#x20; - Automatically handles capacity provisioning, load balancing, scaling, application health monitoring, and instance configuration.

&#x20; - You still have \*\*full control\*\* over the configuration if you need to customize it.

\- \*\*Pricing:\*\* Beanstalk itself is free — you only pay for the underlying resources (EC2, RDS, ELB, etc.) it provisions.



\### Beanstalk Components

\- \*\*Application\*\* — a collection of Elastic Beanstalk components (environments, versions, configs).

\- \*\*Application Version\*\* — an iteration of your application's code.

\- \*\*Environment\*\* — a collection of AWS resources running one application version at a time.

&#x20; - \*\*Tiers:\*\* Web Server environment tier, and Worker environment tier.

\- You can create multiple environments (e.g., dev, test, prod) from the same application.



\### Deployment Flow

`Create Application` → `Upload Version` → `Launch Environment` → `Manage Environment`

\- Uploading a new version and redeploying it into an existing environment = \*\*deploy new version\*\* (loops back into the same environment).



\### Deployment Modes

1\. \*\*Single instance\*\* — one EC2 instance running the app, with an Elastic IP, and an RDS instance directly attached (RDS acts as master). Great for dev/test environments.

2\. \*\*High availability with Load Balancer\*\* — Multi-AZ setup:

&#x20;  - ALB sits in front, distributing traffic across AZ1 and AZ2.

&#x20;  - Each AZ has an ASG-managed EC2 instance behind the ALB.

&#x20;  - RDS is set up as Master (in one AZ) with a Standby replica (in the other AZ) for high availability.

&#x20;  - Good for production workloads.



\### Supported Platforms

\- Go

\- Java SE

\- Java with Tomcat

\- .NET Core on Linux

\- .NET on Windows Server

\- Node.js

\- PHP

\- Python

\- Ruby

\- Packer builder

\- Single container Docker

\- Multi-container Docker

\- Pre-configured Docker



\---



\## Beanstalk Hands-On: Creating an Environment



\*\*Steps:\*\*

1\. Go to Elastic Beanstalk console → Create Elastic Beanstalk application.

2\. \*\*Configure environment:\*\*

&#x20;  - Environment tier: Web server environment

&#x20;  - Application Info → Name

&#x20;  - Environment Info → Environment name

&#x20;  - Platform → Node.js

&#x20;  - Application Code → Sample application

&#x20;  - Presets → Single instance

3\. \*\*Configure service access:\*\*

&#x20;  - Service role → Create a new role using IAM

&#x20;  - EC2 instance profile → Create a new role

&#x20;  - EC2 Key pair → Skip

4\. Steps 3, 4, 5 (Networking/DB, Instance traffic and scaling, Updates/monitoring) → optional, skip for this lab

5\. \*\*Review\*\* → Submit

6\. Once submitted, your Beanstalk environment gets created.

7\. \*\*After creation:\*\*

&#x20;  - Go to CloudFormation → you can see the complete stack Beanstalk created behind the scenes.

&#x20;  - Check EC2 console → you can see the complete EC2 instance(s) Beanstalk provisioned.

&#x20;  - You can upload and deploy a new version of your application anytime from the Beanstalk console.



\*\*Note:\*\* CloudFormation stacks created by Beanstalk are prefixed `awseb-` (not your application name) — search for that prefix if the stack isn't showing up, and double check you're in the correct AWS region.

