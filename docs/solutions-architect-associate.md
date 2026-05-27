---
title: AWS Solutions Architect Associate (SAA-C03)
nav_order: 2
---

# AWS Solutions Architect Associate (SAA-C03)

Notes for the AWS Solutions Architect Associate certification exam.

## Table of Contents

- [General Concepts](#aws-solutions-architect-associate---general-concepts)
  - [AWS Infrastructure](#aws-infrastructure)
- [Questions](#questions)
- [References](#references)

## AWS Solutions Architect Associate - General Concepts

### AWS Infrastructure

AWS infrastructure is organized to help you build effective applications
with high availability, low latency, and consistent performance globally.

**AWS Local Zones:**

- Extend AWS infrastructure closer to specific geographic areas
- **Low latency**: Places compute, storage, and database services near
  end users in metropolitan areas
- **Local data processing**: Run latency-sensitive applications with
  data processed close to the source
- **Consistent AWS experience**: Use the same AWS APIs, tools, and
  infrastructure as in standard Regions
- Use case: Media and entertainment, real-time gaming, live streaming,
  and latency-sensitive applications near specific cities

**Edge Locations:**

- Points of presence distributed globally used by services like
  Amazon CloudFront and Route 53
- **Caching of data**: Store copies of content closer to end users
  to reduce origin requests
- **Fast delivery of content**: Serve static and dynamic content
  with minimal latency via CloudFront CDN
- **Better user experience**: Reduce round-trip time for users
  regardless of their geographic location
- Use case: Content distribution, DNS resolution, DDoS mitigation
  via AWS Shield

**Key Differences:**

| Feature | Local Zones | Edge Locations |
| --- | --- | --- |
| Purpose | Run full compute/storage workloads near users | Cache and deliver content near users |
| Services | EC2, EBS, RDS, ECS, and more | CloudFront, Route 53, AWS Shield |
| Connectivity | Extension of parent Region VPC | Distributed globally, not tied to a Region |
| Use case | Low-latency app processing | Content delivery and DNS |

### AWS Data Migration Tools

Two categories: **Online** (network-based) and **Offline** (physical device).

**Online:**

- **AWS Storage Gateway** — Sync files with SMB, NFS, and iSCSI from on-premises to AWS
- **AWS DataSync** — Sync files from on-premises file storage to AWS (automated, scheduled transfers)
- **AWS Transfer Family** — Transfer files into/out of Amazon S3 or EFS using SFTP, FTP, or FTPS

**Offline:**

- **AWS Snow Family** — Move terabytes to petabytes of data physically to AWS using secure, rugged devices (Snowcone, Snowball, Snowmobile)

| Tool | Type | Use Case |
| --- | --- | --- |
| Storage Gateway | Online | Hybrid on-premises to AWS file sync |
| DataSync | Online | Automated bulk data transfer to AWS |
| Transfer Family | Online | SFTP/FTP file transfers to S3 or EFS |
| Snow Family | Offline | Large-scale physical data migration |

### Amazon S3 Storage Classes

S3 offers multiple storage classes ranging from high-cost frequent-access to low-cost infrequent-access:

| Storage Class | Use Case | Retrieval |
| --- | --- | --- |
| **S3 Standard** | Frequently accessed data | Milliseconds |
| **S3 Standard-IA** | Infrequently accessed, requires rapid access | Milliseconds |
| **S3 Intelligent-Tiering** | Unknown or changing access patterns — auto-moves objects between tiers | Milliseconds |
| **S3 One Zone-IA** | Infrequent access, single AZ, lower cost | Milliseconds |
| **S3 Glacier Instant Retrieval** | Archival data accessed once per quarter | Milliseconds |
| **S3 Glacier Flexible Retrieval** | Archival data accessed 1-2 times per year | Minutes to hours |
| **S3 Glacier Deep Archive** | Long-term retention, accessed rarely | Up to 12 hours |

- Cost decreases from Standard → Deep Archive
- Retrieval time increases from Standard → Deep Archive
- S3 Intelligent-Tiering automatically moves data between access tiers based on usage patterns with no retrieval fees

## Questions

### Q1: Most cost-effective way to transfer 60 TB monthly to on-premises

A company that processes satellite images has an application that runs on AWS. The company stores the images in an Amazon S3 bucket. For compliance reasons, the company must replicate all data once a month to an on-premises location. The average amount of data that the company needs to transfer is 60 TB.

What is the MOST cost-effective way to transfer this data?

- A. Export the data monthly from the existing S3 bucket to an AWS Snowball Edge Storage Optimized device. Ship the device to the on-premises location. Transfer the data. Return the device a week later.
- B. Use a new S3 bucket with S3 Standard-IA storage. Use an AWS Storage Gateway File Gateway to transfer the data from the new S3 bucket to the on-premises location. Delete the images from the new S3 bucket after the transfer.
- C. Use S3 bucket replication to copy all objects to a new S3 bucket with S3 Standard-IA storage. Use Amazon S3 to transfer the data from the new S3 bucket to the on-premises location. Delete the images from the new S3 bucket after the transfer.
- D. Create an Amazon CloudFront distribution for the objects in the existing S3 bucket. Download the objects from CloudFront to the on-premises location. Delete the objects from the distribution after the transfer.

**Answer: A**

Snowball Edge Storage Optimized is designed for large-scale offline data transfers. At 60 TB/month, shipping a physical device is far cheaper than internet egress costs. Options B, C, and D all require transferring data over the internet, which would incur high AWS data transfer (egress) charges at that volume — making them significantly more expensive.

### Q2: Service to manage vulnerability scans in EC2 and ECR

A company is investigating services to manage vulnerability scans in Amazon EC2 instances and container images that the company stores in Amazon Elastic Container Registry (Amazon ECR). The service should identify potential software vulnerabilities and categorize the severity of the vulnerabilities.

Which AWS service will meet these requirements?

- A. Amazon GuardDuty
- B. Patch Manager, a capability of AWS Systems Manager
- C. Amazon Inspector
- D. AWS Config

**Answer: C**

Amazon Inspector is purpose-built for vulnerability scanning of EC2 instances and container images in ECR. It automatically discovers workloads and continuously scans for software vulnerabilities, assigning severity scores. GuardDuty is for threat detection (suspicious activity), not vulnerability scanning. Patch Manager handles OS patching but doesn't categorize software vulnerability severity. AWS Config tracks resource configuration compliance, not software vulnerabilities.

### Q3: DR strategy with 5-minute RTO and minimal running capacity

A company is designing a disaster recovery (DR) architecture for an important application on AWS. The company has determined that the recovery time objective (RTO) is 5 minutes with a minimal running instance capacity to support the application in the AWS DR site. The company needs to minimize costs for the DR architecture.

Which DR strategy will meet these requirements?

- A. Warm standby
- B. Pilot light
- C. Multi-site active-active
- D. Backup and restore

**Answer: A**

Warm standby keeps a scaled-down but fully functional environment running at all times, enabling recovery within minutes (meets the 5-minute RTO). Pilot light only keeps core components running (e.g., database) and requires additional time to provision and start app servers, making it harder to meet a strict 5-minute RTO. Multi-site active-active meets the RTO but is the most expensive option (full duplicate environment). Backup and restore has the longest RTO (hours) and lowest cost but cannot meet a 5-minute RTO.

### Q4: Most cost-effective storage for MariaDB on RDS with 40 GiB and 1,000 IOPS

A company is transitioning its Amazon EC2 based MariaDB database to Amazon RDS. The company has already identified a database instance type that will meet the company's CPU and memory requirements. The database must provide at least 40 GiB of storage capacity and 1,000 IOPS.

Which storage configuration for the Amazon RDS for MariaDB instance is MOST cost-effective?

- A. Provision 350 GiB of magnetic storage for the RDS instance.
- B. Provision 50 GiB of General Purpose SSD (gp3) storage for the RDS instance.
- C. Provision 334 GiB of General Purpose SSD (gp2) storage for the RDS instance.
- D. Provision 50 GiB of Provisioned IOPS storage with 1,000 IOPS for the RDS instance.

**Answer: B**

gp3 includes 3,000 IOPS at no additional cost independent of volume size, so 50 GiB of gp3 easily covers the 1,000 IOPS requirement at minimal cost. Magnetic storage (A) does not support configurable IOPS. gp2 (C) ties IOPS to volume size at 3 IOPS/GB, requiring 334 GiB to reach 1,000 IOPS — far more storage than needed, making it more expensive. Provisioned IOPS (D) would meet the requirement but costs more than gp3 for this workload.

### Q5: Database for key-value chat app with high read rate and microsecond latency

A company is developing a chat application that will be deployed on AWS. The application stores the messages by using a key-value data model. Groups of users typically read the messages multiple times. A solutions architect must select a database solution that will scale for a high rate of reads and will deliver messages with microsecond latency.

Which database solution will meet these requirements?

- A. Amazon Aurora with Aurora Replicas
- B. Amazon DynamoDB with DynamoDB Accelerator (DAX)
- C. Amazon Aurora with Amazon ElastiCache for Memcached
- D. Amazon Neptune with Amazon ElastiCache for Memcached

**Answer: B**

DynamoDB is a NoSQL database that natively supports key-value records and scales for high read rates. DAX is an in-memory cache for DynamoDB that delivers response times in microseconds. Aurora (A, C) is a relational database — not a key-value store — and cannot consistently achieve microsecond latency. Neptune (D) is a graph database optimized for highly connected data, not simple key-value workloads.

### Q6: Secure RDS instances from internet access, allow only app tier on specific port (Select TWO)

A company has strict data protection requirements. A solutions architect must configure security for a VPC to ensure that backend Amazon RDS DB instances cannot be accessed from the internet. The solutions architect must ensure that the DB instances are accessible from the application tier over a specified port only.

Which actions should the solutions architect take to meet these requirements? (Select TWO.)

- A. Specify a DB subnet group that contains only private subnets for the DB instances.
- B. Attach an elastic network interface with a private IPv4 address to each DB instance.
- C. Configure AWS Shield with the VPC. Update the route tables for the subnets that the DB instances use.
- D. Configure an AWS Direct Connect connection on the database port between the application tier and the backend.
- E. Add an inbound rule to the database security group that allows requests from the security group of the application tier over the database port. Remove other inbound rules.

**Answer: A, E**

Placing DB instances in a DB subnet group with only private subnets ensures internet traffic cannot reach them (no internet gateway route). Adding a security group inbound rule that allows only the application tier's security group on the specific port enforces port-level access control. Elastic network interfaces (B) are logical networking components and don't restrict internet access. AWS Shield (C) protects against DDoS attacks and cannot be used as a routing target. Direct Connect (D) is for dedicated connectivity from on-premises to AWS, not for internal VPC tier-to-tier communication.

### Q7: EBS volume type supporting up to 20,000 IOPS

A company is deploying a new database on a new Amazon EC2 instance. The workload of this database requires a single Amazon Elastic Block Store (Amazon EBS) volume that can support up to 20,000 IOPS.

Which type of EBS volume meets this requirement?

- A. Throughput Optimized HDD
- B. Provisioned IOPS SSD
- C. General Purpose SSD
- D. Cold HDD

**Answer: B**

Provisioned IOPS SSD (io1/io2) supports up to 64,000 IOPS per volume, easily covering the 20,000 IOPS requirement. Throughput Optimized HDD (A) is limited to 500 IOPS. General Purpose SSD (C) is limited to 16,000 IOPS — insufficient for this workload. Cold HDD (D) is designed for cold, infrequently accessed workloads and measures performance in throughput, not IOPS.

### Q8: Most cost-effective storage for graphic rendering with 400 GiB temp data and 40,000 IOPS

A media company is designing a new application for graphic rendering. The application requires up to 400 GB of storage for temporary data that is discarded after the frames are rendered. The application requires approximately 40,000 random IOPS to perform the rendering.

What is the MOST cost-effective storage option for this rendering application?

- A. A storage optimized Amazon EC2 instance with instance store storage.
- B. A storage optimized Amazon EC2 instance with a Provisioned IOPS SSD (io1 or io2) Amazon Elastic Block Store (Amazon EBS) volume.
- C. A burstable Amazon EC2 instance with a Throughput Optimized HDD (st1) Amazon Elastic Block Store (Amazon EBS) volume.
- D. A burstable Amazon EC2 instance with Amazon S3 storage over a VPC endpoint.

**Answer: A**

Storage optimized instances with instance store are designed for workloads requiring high, random IOPS on local storage (tens of thousands of low-latency IOPS), and the instance store has no additional cost. Since the data is temporary and discarded after rendering, persistence is not needed — making EBS unnecessary. Provisioned IOPS SSD (B) can meet the IOPS requirement but adds EBS cost on top of the instance cost, making it less cost-effective. Throughput Optimized HDD (C) maximizes throughput, not random IOPS, and cannot meet the requirement. S3 (D) is object storage, not suitable for high-speed random read/write scratch space.

### Q9: Reliable S3 upload for 20-30 GB log files over intermittent network

A company has an on-premises application that exports log files about users of a website. The log files range from 20 GB to 30 GB in size. A solutions architect has created an Amazon S3 bucket to store the files. The files will be uploaded directly from the application. The network connection experiences intermittent failures, and the upload sometimes fails. The solutions architect must design a solution that resolves this issue. The solution must minimize operational overhead.

Which solution will meet these requirements?

- A. Enable S3 Transfer Acceleration.
- B. Copy the files to an Amazon EC2 instance in the closest AWS Region. Use S3 Lifecycle policies to copy the log files to Amazon S3.
- C. Use multipart upload to Amazon S3.
- D. Upload the files to two AWS Regions simultaneously. Enable two-way Cross-Region Replication between the two Regions.

**Answer: C**

Multipart upload splits large files into parts that are uploaded independently. If any part fails due to a network interruption, only that part needs to be retransmitted — not the entire file. This directly resolves intermittent failure issues with minimal operational overhead. S3 Transfer Acceleration (A) speeds up uploads via edge locations but doesn't handle partial failures. S3 Lifecycle policies (B) cannot transfer files from EC2 block storage to S3. Uploading to two Regions simultaneously (D) adds unnecessary operational overhead and cost.

### Q10: Most cost-effective solution for always-available OLTP app with unpredictable traffic

A company is deploying a new application that will consist of an application layer and an online transaction processing (OLTP) relational database. The application must be available at all times. However, the application will have unpredictable traffic patterns. The company wants to pay the minimum for compute costs during these idle periods.

Which solution will meet these requirements MOST cost effectively?

- A. Run the application on Amazon EC2 instances by using a burstable instance type. Use Amazon Redshift for the database.
- B. Deploy the application and a MySQL database to Amazon EC2 instances by using AWS CloudFormation. Delete the instances at the beginning of the idle periods.
- C. Deploy the application on Amazon EC2 instances in an Auto Scaling group behind an Application Load Balancer. Use Amazon RDS for MySQL for the database.
- D. Run the application in containers with Amazon Elastic Container Service (Amazon ECS) on AWS Fargate. Use Amazon Aurora Serverless for the database.

**Answer: D**

Fargate incurs minimal costs when the application is idle (no running containers = no compute charges), and Aurora Serverless also scales to zero compute when idle. This combination minimizes costs during unpredictable low-traffic periods while remaining available. EC2 burstable instances (A) still incur costs when idle, and Redshift is an OLAP data warehouse, not suited for OLTP. Deleting instances via CloudFormation (B) violates the always-available requirement. EC2 with Auto Scaling + RDS (C) keeps at least one instance and database running at all times, not minimizing idle costs.

### Q11: Ensure in-flight requests complete before instances are terminated during scale-in

A reporting application runs on Amazon EC2 instances behind an Application Load Balancer. The instances run in an Amazon EC2 Auto Scaling group across multiple Availability Zones. For complex reports, the application can take up to 15 minutes to respond to a request. A solutions architect is concerned that users will receive HTTP 5xx errors if a report request is in process during a scale-in event.

What should the solutions architect do to ensure that user requests will be completed before instances are terminated?

- A. Enable sticky sessions (session affinity) for the target group of the instances.
- B. Increase the instance size in the Application Load Balancer target group.
- C. Increase the cooldown period for the Auto Scaling group to a greater amount of time than the time required for the longest running responses.
- D. Increase the deregistration delay timeout for the target group of the instances to greater than 900 seconds.

**Answer: D**

The ALB deregistration delay (connection draining) controls how long the load balancer waits before completing deregistration of an instance, allowing in-flight requests to finish. Since reports take up to 15 minutes (900 seconds), setting the deregistration delay above 900 seconds ensures requests complete before the instance is terminated. Sticky sessions (A) would fail during scale-in since the instance being removed would become unhealthy and the ALB would stop routing to it. Increasing instance size (B) speeds up processing but doesn't prevent termination mid-request. Cooldown periods (C) control time between scaling activities, not how long the ALB waits for in-flight requests to complete.

### Q12: Improve high availability for two EC2 instances behind a NLB in a single AZ

An application runs on two Amazon EC2 instances behind a Network Load Balancer. The EC2 instances are in a single Availability Zone.

What should a solutions architect do to make this architecture more highly available?

- A. Create a new VPC with two new EC2 instances in the same Availability Zone as the original EC2 instances. Create a VPC peering connection between the two VPCs.
- B. Replace the Network Load Balancer with an Application Load Balancer that is configured with the EC2 instances in an Auto Scaling group.
- C. Configure Amazon Route 53 to perform health checks on the EC2 instances behind the Network Load Balancer. Add a failover routing policy.
- D. Place the EC2 instances in an Auto Scaling group that extends across multiple Availability Zones. Designate the Auto Scaling group as the target of the Network Load Balancer.

**Answer: D**

Extending the Auto Scaling group across multiple AZs ensures the application survives an AZ failure and automatically adds capacity when needed. VPC peering (A) provides connectivity but the instances remain in a single AZ, offering no HA improvement. Replacing the NLB with an ALB (B) doesn't improve availability since both load balancers are inherently highly available — the problem is the EC2 instances being in one AZ. Route 53 failover routing (C) requires a secondary destination, which isn't specified, and the instances still remain in a single AZ.

### Q13: Implement a pilot light DR strategy for a self-contained on-premises application

A company asks a solutions architect to implement a pilot light disaster recovery (DR) strategy for an existing on-premises application. The application is self-contained and does not need to access any databases.

Which solution will implement a pilot light DR strategy?

- A. Back up the on-premises application, configuration, and data to an Amazon S3 bucket. When the on-premises application fails, build a new hosting environment on AWS and restore the application from the information stored in the S3 bucket.
- B. Recreate the application hosting environment on AWS by using Amazon EC2 instances and stop the EC2 instances. When the on-premises application fails, start the stopped EC2 instances and direct 100% of application traffic to the EC2 instances running in the AWS Cloud.
- C. Recreate the application hosting environment on AWS by using Amazon EC2 instances. Direct 10% of application traffic to the EC2 instances running in the AWS Cloud. When the on-premises application fails, direct 100% of application traffic to the EC2 instances running in the AWS Cloud.
- D. Back up the on-premises application, configuration, and data to an Amazon S3 bucket. When the on-premises application fails, rebuild the on-premises hosting environment and restore the application from the information stored in the S3 bucket.

**Answer: B**

Pilot light pre-provisions the environment in AWS but keeps it turned off (or at minimal scale), activating it only during DR failover. Stopping EC2 instances means resources are ready but incur no compute costs until needed — RPO and RTO are typically in the tens of minutes. Options A and D are backup and restore strategies (highest RTO, lowest cost). Option C is a warm standby strategy, which keeps a portion of traffic running continuously and is more expensive than pilot light.

### Q14: Reduce costs for an app that runs randomly a few times a day with 10-min max runtime

A company has an application that runs on a large general purpose Amazon EC2 instance type that is part of an EC2 Auto Scaling group. The company wants to reduce future costs associated with this application. After the company reviews metrics and logs in Amazon CloudWatch, the company notices that this application runs randomly a couple of times a day to retrieve and manage data. According to CloudWatch, the maximum runtime for each request is 10 minutes, the memory use is 4 GB, and the instances are always in the running state.

Which solution will reduce costs the MOST?

- A. Deploy the application on a large burstable EC2 instance.
- B. Refactor the application code to run as an AWS Lambda function.
- C. Containerize the application by using Amazon Elastic Kubernetes Service (Amazon EKS). Host the container on EC2 instances.
- D. Use AWS Instance Scheduler to start and stop the instances based on the runtimes in the logs.

**Answer: B**

Lambda charges only for actual execution time and scales automatically — since the app runs randomly a few times a day for up to 10 minutes with 4 GB memory, Lambda eliminates the cost of keeping EC2 instances running 24/7. Burstable EC2 (A) still runs continuously, providing no cost reduction. EKS on EC2 (C) still requires instances in the running state. Instance Scheduler (D) is unsuitable for unpredictable runtimes and charges by the hour rather than exact execution time, potentially leaving the app unavailable when a request arrives.

### Q15: Command to retrieve EC2 instance metadata on Linux

A company needs to look up configuration details about how a Linux-based Amazon EC2 instance was launched.

Which command should a solutions architect run on the EC2 instance to gather the system metadata?

- A. `curl http://169.254.169.254/latest/meta-data/`
- B. `curl http://localhost/latest/meta-data/`
- C. `curl http://254.169.254.169/latest/meta-data/`
- D. `curl http://192.168.0.1/latest/meta-data/`

**Answer: A**

EC2 instance metadata is only accessible via the link-local address `169.254.169.254`. Using `localhost` (B) resolves to `127.0.0.1`, which does not serve instance metadata. Option C has the IP octets in the wrong order. Option D uses a private network range (`192.168.x.x`), which does not serve instance metadata.

### Q16: Grant security team cross-account read access following best practices

A company uses one AWS account to run production workloads. The company has a separate AWS account for its security team. During periodic audits, the security team needs to view specific account settings and resource configurations in the AWS account that runs production workloads. A solutions architect must provide the required access to the security team by designing a solution that follows AWS security best practices.

Which solution will meet these requirements?

- A. Create an IAM user for each security team member in the production account. Attach a permissions policy that provides the permissions required by the security team to each user.
- B. Create an IAM role in the production account. Attach a permissions policy that provides the permissions required by the security team. Add the security team account to the trust policy.
- C. Create a new IAM user in the production account. Assign administrative privileges to the user. Allow the security team to use this account to log in to the systems they need to review.
- D. Create an IAM user for each security team member in the production account. Attach a permissions policy to a new IAM group. Assign the security team members to the group.

**Answer: B**

Creating a cross-account IAM role with a trust policy that allows the security team's account to assume it is the AWS best practice for granting cross-account access. It follows least-privilege principles and avoids creating long-term credentials. Options A and D create IAM users in the production account for external team members, which does not follow best practices (long-term credentials, no cross-account role delegation). Option C grants administrative privileges, violating the principle of least privilege.

### Q17: Most cost-effective storage for rarely accessed data retained 5 years, accessible within 2 hours

A company needs to maintain data records for a minimum of 5 years. The data is rarely accessed after it is stored. The data must be accessible within 2 hours.

Which solution will meet these requirements MOST cost-effectively?

- A. Store the data in an Amazon Elastic File System (Amazon EFS) file system. Access the data by using AWS Direct Connect.
- B. Store the data in an Amazon Elastic Block Store (Amazon EBS) volume. Take snapshots. Store the snapshots in an Amazon S3 bucket.
- C. Store the data in an Amazon S3 bucket. Use an S3 Lifecycle policy to move the data to S3 Standard-Infrequent Access (S3 Standard-IA).
- D. Store the data in an Amazon S3 bucket. Use an S3 Lifecycle policy to move the data to S3 Glacier Instant Retrieval.

**Answer: D**

S3 Glacier Instant Retrieval is purpose-built for long-term archival of rarely accessed data and provides millisecond retrieval (well within the 2-hour requirement) at the lowest cost among the viable options. EFS + Direct Connect (A) is not cost-effective without a file system requirement. EBS snapshots on S3 (B) add unnecessary cost by requiring EBS in addition to S3. S3 Standard-IA (C) is cheaper than Standard but more expensive than Glacier for long-term archival of rarely accessed data.

### Q18: Components required to build a site-to-site VPN connection to AWS (Select TWO)

Which components are required to build a site-to-site VPN connection to AWS? (Select TWO.)

- A. An internet gateway
- B. A NAT gateway
- C. A customer gateway
- D. Amazon API Gateway
- E. A virtual private gateway

**Answer: C, E**

A virtual private gateway is attached to the VPC on the AWS side to terminate the VPN connection. A customer gateway is the device (physical or software) on the customer's on-premises side that is configured to establish the VPN tunnel. An internet gateway (A) allows general internet traffic into/out of a VPC but is not part of an encrypted VPN connection. A NAT gateway (B) enables private EC2 instances to reach the internet but cannot create a VPN connection. Amazon API Gateway (D) is a managed API service unrelated to VPN connectivity.

### Q19: Centralized auth, authorization, and user management for a mobile app

A company is developing a new mobile version of its popular web application in the AWS Cloud. The mobile app must be accessible to internal and external users. The mobile app must handle authorization, authentication, and user management from one central source.

Which solution meets these requirements?

- A. IAM roles
- B. IAM users and groups
- C. Amazon Cognito user pools
- D. AWS Security Token Service (AWS STS)

**Answer: C**

Amazon Cognito provides authentication, authorization, and user management for web and mobile apps from a single service. Users can sign in directly with a username and password or through a trusted third party. IAM roles (A) define permissions for AWS entities but do not control application-level access. IAM users and groups (B) control access to AWS services, not to applications. AWS STS (D) issues temporary security credentials for AWS resource access but does not manage application user authentication.

### Q20: Protect CloudFront + ALB + EC2 app against XSS and SQL injection

A company runs its website on Amazon EC2 instances behind an Application Load Balancer that is configured as the origin for an Amazon CloudFront distribution. The company wants to protect against cross-site scripting and SQL injection attacks.

Which approach should a solutions architect recommend to meet these requirements?

- A. Enable AWS Shield Advanced. List the CloudFront distribution as a protected resource.
- B. Define an AWS Shield Advanced policy in AWS Firewall Manager to block cross-site scripting and SQL injection attacks.
- C. Deploy AWS Firewall Manager on the EC2 instances. Create conditions and rules that block cross-site scripting and SQL injection attacks.
- D. Set up AWS WAF on the CloudFront distribution. Use conditions and rules that block cross-site scripting and SQL injection attacks.

**Answer: D**

AWS WAF can detect and block SQL injection and cross-site scripting (XSS) attacks using managed rules and custom conditions. Attaching it to the CloudFront distribution inspects traffic at the edge before it reaches the origin. Shield Advanced (A, B) protects against DDoS attacks but does not protect against XSS or SQL injection. Firewall Manager (C) is a managed service for centrally configuring WAF and Shield policies — it is not deployed on EC2 instances.

### Q21: Modes available in the Storage Gateway appliance (Select THREE)

Which of the following are modes available in the Storage Gateway appliance?

(Select THREE.)

- A. Internet Gateway
- B. Tape Gateway
- C. Volume Gateway
- D. Virtual Gateway
- E. Amazon S3 File Gateway
- F. NAT Gateway

**Answer: B, C, E**

AWS Storage Gateway has three modes: **Tape Gateway** (virtual tape library backed by S3/Glacier for backup workflows), **Volume Gateway** (iSCSI block storage volumes backed by S3, in cached or stored mode), and **Amazon S3 File Gateway** (stores files as objects in S3 via NFS/SMB). Internet Gateway (A) and NAT Gateway (F) are VPC networking components, not Storage Gateway modes. Virtual Gateway (D) does not exist as an AWS service.

### Q22: AWS service to run SQL queries directly on data in S3

Which AWS service lets you run SQL queries directly on data stored in Amazon S3 without loading it into a database?

- A. Amazon Redshift
- B. Amazon Athena
- C. AWS Glue
- D. Amazon QuickSight

**Answer: B**

Amazon Athena is a serverless interactive query service that lets you run SQL queries directly on data in S3 with no data loading or ETL required — you pay only per query. Redshift (A) is a data warehouse that requires data to be loaded into it. AWS Glue (C) is an ETL service for data preparation and transformation, not query execution. Amazon QuickSight (D) is a business intelligence and visualization tool, not a query engine for raw S3 data.

### Q23: Auto Scaling strategy to maintain 75% CPU with unpredictable traffic

You have an application with unpredictable traffic patterns that runs on at least two instances. You want the CPU utilization to stay at about 75 percent. Which Amazon EC2 Auto Scaling strategy should you choose?

- A. Scheduled
- B. Predictive
- C. Dynamic
- D. Manual

**Answer: C**

Dynamic scaling adjusts capacity in real time based on current demand using a target tracking policy — you simply set the desired metric (e.g., CPU at 75%) and Auto Scaling continuously adds or removes instances to maintain that target. It is the right choice for unpredictable traffic. Scheduled scaling (A) triggers at predefined times and requires knowing when traffic spikes occur. Predictive scaling (B) uses ML to forecast traffic but requires historical patterns — not suitable for truly unpredictable workloads. Manual scaling (D) requires human intervention and cannot react automatically to traffic changes.

### Q24: Use cases for AWS CloudTrail data (Select TWO)

Which of the following are use cases for AWS CloudTrail data? (Select TWO.)

- A. Provide real-time observability of AWS resources.
- B. Store log data as a record of account usage.
- C. Log events for a particular service or application.
- D. Capture root login failures.
- E. Collect metric data measuring CPU utilization.

**Answer: B, D**

CloudTrail records API calls and account activity across AWS services, providing a history of actions taken — making it the right tool for audit trails (B) and detecting security events like root login failures (D). Real-time observability (A) is provided by Amazon CloudWatch. Application-level event logging (C) is handled by CloudWatch Logs. CPU utilization metrics (E) are collected by CloudWatch Metrics, not CloudTrail.

### Q25: Make long-running report generation more responsive behind ALB

A web application runs on Amazon EC2 instances behind an Application Load Balancer (ALB). The application allows users to create custom reports of historical weather data. Generating a report can take up to 5 minutes. These long-running requests use many of the available incoming connections, making the system unresponsive to other users.

How can a solutions architect make the system more responsive?

- A. Use Amazon SQS with AWS Lambda to generate reports.
- B. Increase the idle timeout on the ALB to 5 minutes.
- C. Update the client-side application code to increase its request timeout to 5 minutes.
- D. Publish the reports to Amazon S3 and use Amazon CloudFront for downloading to the user.

**Answer: A**

Offloading report generation to SQS + Lambda decouples the long-running work from the web tier, freeing EC2 connections for other users. Increasing the ALB idle timeout (B) prevents premature disconnects but doesn't reduce connection consumption. Updating client-side timeouts (C) prevents client-side failures but does nothing to free server resources. Publishing to S3 + CloudFront (D) helps with delivery but doesn't solve the problem of long-running requests consuming connections during generation.

### Q26: Shared storage for 10–50 EC2 instances reading and writing to the same 50 GB folder

A solutions architect is designing an elastic application that will have between 10 and 50 Amazon EC2 concurrent instances running, depending on the load. Each instance must mount storage that will read and write to the same 50 GB folder.

Which storage type meets the requirements?

- A. Amazon S3
- B. Amazon Elastic File System (Amazon EFS)
- C. Amazon Elastic Block Store (Amazon EBS) volumes
- D. Amazon EC2 instance store

**Answer: B**

EFS is a fully managed, shared file system that can be mounted simultaneously by thousands of EC2 instances across multiple AZs using NFS. S3 (A) is object storage and cannot be natively mounted as a file system. EBS (C) volumes attach to a single EC2 instance (Multi-Attach is limited to io1/io2 and up to 16 instances with a cluster-aware file system). Instance store (D) is ephemeral local storage tied to a single instance.

### Q27: Most secure method for ECS task on EC2 to publish messages to SQS

A company has an application running as a service in Amazon Elastic Container Service (Amazon ECS) using the Amazon EC2 launch type. The application code makes AWS API calls to publish messages to Amazon Simple Queue Service (Amazon SQS).

What is the MOST secure method of giving the application permission to publish messages to Amazon SQS?

- A. Use AWS Identity and Access Management (IAM) to grant SQS permissions to the role used by the launch configuration for the Auto Scaling group of the ECS cluster.
- B. Create a new IAM user with SQS permissions. Then update the task definition to declare the access key ID and secret access key as environment variables.
- C. Create a new IAM role with SQS permissions. Then update the task definition to use this role for the task role setting.
- D. Update the security group used by the ECS cluster to allow access to Amazon SQS.

**Answer: C**

ECS task roles (taskRoleArn) provide fine-grained, per-task IAM permissions — only the specific task gets SQS access. Granting permissions via the instance role (A) gives ALL containers on the EC2 instance SQS access, violating least privilege. IAM user credentials as environment variables (B) uses long-term credentials, which is insecure and harder to rotate. Security groups (D) operate at the network layer and cannot control AWS API permissions.

### Q28: Immediate solution for viral traffic spike on an on-premises media site

A company runs an online media site, hosted on-premises. An employee posted a product review that contained videos and pictures. The review went viral, and the company needs to handle the resulting spike in website traffic.

What action would provide an immediate solution?

- A. Redesign the website to use Amazon API Gateway, and use AWS Lambda to deliver content.
- B. Add server instances using Amazon EC2 and use Amazon Route 53 with a failover routing policy.
- C. Serve the images and videos using an Amazon CloudFront distribution created using the media site as the origin.
- D. Use Amazon ElastiCache for Redis for caching and reducing the load requests from the origin.

**Answer: C**

CloudFront can be set up quickly with the existing on-premises site as the origin, immediately caching and distributing static media content (images and videos) at edge locations worldwide. Redesigning with API Gateway + Lambda (A) requires significant development — not immediate. Adding EC2 + Route 53 failover (B) requires provisioning and configuration time. ElastiCache (D) is an in-memory cache within AWS, not designed to front an on-premises website for external users.

### Q29: Self-managed service for governance evaluations and remediations across AWS Organizations

A company's cloud operations team wants to standardize resource remediation. The company wants to provide a standard set of governance evaluations and remediations to all member accounts in its organization in AWS Organizations.

Which self-managed AWS service can the company use to meet these requirements with the LEAST amount of operational effort?

- A. AWS Security Hub compliance standards
- B. AWS Config conformance packs
- C. AWS CloudTrail
- D. AWS Trusted Advisor

**Answer: A**

AWS Security Hub compliance standards provide automated, continuous security checks against industry frameworks (CIS, AWS Foundational Best Practices) across all member accounts in AWS Organizations with minimal setup. Security Hub integrates with AWS Organizations natively for centralized management. Config conformance packs (B) provide evaluations and remediation but require more operational effort to configure and deploy. CloudTrail (C) provides audit logging, not governance evaluations or remediation. Trusted Advisor (D) offers best-practice recommendations but does not provide standardized remediations.

### Q30: Most secure way for EC2 in private subnet to access SNS with PII data

An application launched on Amazon EC2 instances needs to publish personally identifiable information (PII) about customers using Amazon Simple Notification Service (Amazon SNS). The application is launched in private subnets within an Amazon VPC.

What is the MOST secure way to allow the application to access service endpoints in the same AWS Region?

- A. Use an internet gateway.
- B. Use AWS PrivateLink.
- C. Use a NAT gateway.
- D. Use a proxy instance.

**Answer: B**

AWS PrivateLink (VPC interface endpoints) allows private connectivity to AWS services like SNS without leaving the AWS network. Traffic never traverses the public internet, which is critical when handling PII. An internet gateway (A) would require the instances to be in public subnets and expose traffic to the internet. A NAT gateway (C) routes traffic through the public internet, which is less secure for PII. A proxy instance (D) adds operational overhead and still routes traffic through the internet.

### Q31: Optimize Auto Scaling costs for highly variable demand without impacting performance

A company hosts its website on AWS. To address the highly variable demand, the company has implemented Amazon EC2 Auto Scaling. Management is concerned that the company is over-provisioning its infrastructure, especially at the front end of the three-tier application. A solutions architect needs to ensure costs are optimized without impacting performance.

What should the solutions architect do to accomplish this?

- A. Use Auto Scaling with Reserved Instances.
- B. Use Auto Scaling with a scheduled scaling policy.
- C. Use Auto Scaling with the suspend-resume feature.
- D. Use Auto Scaling with a target tracking scaling policy.

**Answer: D**

Target tracking scaling automatically adjusts capacity to maintain a specific metric (e.g., CPU utilization at a target percentage), scaling in when demand drops and scaling out when demand increases. This directly addresses over-provisioning by closely matching capacity to actual demand. Reserved Instances (A) reduce per-instance cost but don't address capacity optimization. Scheduled scaling (B) requires predictable demand patterns and cannot handle highly variable traffic. Suspend-resume (C) pauses scaling actions entirely, which would impact availability.

### Q32: Improve security for EC2 running website and AD controller, minimize admin overhead

A company is performing an AWS Well-Architected Framework review of an existing workload deployed on AWS. The review identified a public-facing website running on the same Amazon EC2 instance as a Microsoft Active Directory domain controller that was installed recently to support other AWS services. A solutions architect needs to recommend a new design that would improve the security of the architecture and minimize the administrative demand on IT staff.

What should the solutions architect recommend?

- A. Use AWS Managed Microsoft AD to create a managed Active Directory. Uninstall Active Directory on the current EC2 instance.
- B. Create EC2 instances in the same subnet and reinstall Active Directory on a separate EC2 instance. Uninstall Active Directory on the current EC2 instance.
- C. Use AWS Directory Service to create an Active Directory Connector to the Active Directory controller running on the current EC2 instance.
- D. Configure AWS IAM Identity Center (AWS Single Sign-On) with Security Assertion Markup Language (SAML) 2.0 federation with the current Active Directory controller. Modify the EC2 instance's security group to deny public access to Active Directory.

**Answer: A**

AWS Managed Microsoft AD fully separates the Active Directory workload from the public-facing website and offloads AD management to AWS, addressing both security (separation of concerns) and administrative overhead (fully managed service). Reinstalling AD on a separate EC2 instance (B) improves security through separation but does not reduce admin effort — AD is still self-managed. An AD Connector (C) requires an existing directory to connect to and doesn't replace the AD on the instance. IAM Identity Center + security group (D) keeps AD on the same instance as the website, which doesn't resolve the fundamental architectural issue.

### Q33: Most secure way to store an API key for AWS Lambda accessing a third-party service

An application running on AWS Lambda requires an API key to access a third-party service. The key must be stored securely with audited access to the Lambda function only.

What is the MOST secure way to store the key?

- A. As an object in Amazon S3
- B. As a secure string in AWS Systems Manager Parameter Store
- C. Inside a file on an Amazon EBS volume attached to the Lambda function
- D. Inside a secrets file stored on Amazon EFS

**Answer: B**

AWS Systems Manager Parameter Store SecureString parameters encrypt values using AWS KMS and integrate with IAM for fine-grained access control. Access to the parameter can be restricted to the Lambda function's execution role and all access is logged via CloudTrail for auditing. S3 (A) can store encrypted objects but lacks the native integration and access auditing purpose-built for secrets. EBS volumes (C) cannot be attached to Lambda functions. EFS (D) can be mounted by Lambda but storing secrets as files lacks built-in encryption management and fine-grained auditing.

### Q34: SQS configuration to prioritize paid users over free users for photo processing

An online photo application lets users upload photos and perform image editing operations. The application is built on Amazon EC2 instances and offers two classes of service: free and paid. Photos submitted by paid users are processed before those submitted by free users. Photos are uploaded to Amazon S3 and the job information is sent to Amazon SQS.

Which configuration should a solutions architect recommend?

- A. Use one SQS FIFO queue. Assign a higher priority to the photos from paid users so they are processed first.
- B. Use two SQS FIFO queues: one for paid users and one for free users. Set the free queue to use short polling and the paid queue to use long polling.
- C. Use two SQS standard queues: one for paid users and one for free users. Configure the application on the Amazon EC2 instances to prioritize polling for the paid queue over the free queue.
- D. Use one SQS standard queue. Set the visibility timeout of the photos from paid users to zero. Configure the application on the Amazon EC2 instances to prioritize visibility settings so photos from paid users are processed first.

**Answer: B**

Using two separate FIFO queues ensures ordered processing within each tier. Long polling on the paid queue means the application retrieves messages immediately when available, while short polling on the free queue introduces deliberate delays — effectively prioritizing paid users. SQS does not support native message priority (A is invalid). Two standard queues with application-level prioritization (C) could work but standard queues don't guarantee ordering, potentially processing free user photos out of order. Manipulating visibility timeout (D) does not create a reliable prioritization mechanism and adds unnecessary complexity.

### Q35: Document storage on EC2 across multi-AZ with immediate retrieval

A company is building a document storage application on AWS. The application runs on Amazon EC2 instances in multiple Availability Zones. The company requires the document store to be highly available. The documents need to be available to all EC2 instances hosting the application and returned immediately when requested multiple times per month. The lead engineer has configured the application to use Amazon Elastic Block Store (Amazon EBS) to store the documents, but is willing to consider other options to meet the availability requirements.

What should a solutions architect recommend?

- A. Snapshot the EBS volumes regularly and build new volumes from those snapshots in additional Availability Zones.
- B. Use Amazon EBS for the EC2 instance root volumes. Configure the application to build the document store on Amazon S3.
- C. Use Amazon EBS for the EC2 instance root volumes. Configure the application to build the document store on Amazon S3 Glacier Flexible Retrieval.
- D. Use at least three Provisioned IOPS EBS volumes for EC2 instances. Mount the volumes to the EC2 instances in a RAID 1 configuration.

**Answer: B**

Amazon S3 provides 99.999999999% (11 nines) durability, is accessible from any AZ or Region, and returns objects immediately — meeting all requirements. EBS snapshots (A) add complexity and lag since volumes must be rebuilt in each AZ. S3 Glacier Flexible Retrieval (C) has retrieval times of minutes to hours, not immediate. RAID 1 with EBS (D) provides redundancy within a single instance but does not make documents available across AZs.

### Q36: Most cost-effective way to improve UDP audio streaming performance for North American users

A company has a well-architected application that streams audio data by using UDP in the AWS Cloud. The company hosts the application in the eu-central-1 Region. The company plans to offer services to North American users. A solutions architect must improve application network performance for the North American users.

Which of the following is the MOST cost-effective solution?

- A. Create an AWS Global Accelerator standard accelerator with an endpoint group in eu-central-1.
- B. Use AWS CloudFormation to deploy additional application infrastructure in the us-east-1 Region and the us-west-1 Region.
- C. Create an Amazon CloudFront distribution and use the North America (United States, Mexico, Canada) and Europe and Israel price classes.
- D. Configure the application to use an Amazon Route 53 latency-based routing policy.

**Answer: A**

AWS Global Accelerator routes UDP and TCP traffic through the AWS global backbone network, providing consistent low-latency performance without duplicating infrastructure. It is the most cost-effective because only an accelerator layer is added. Deploying in US regions (B) requires full infrastructure duplication — significantly more expensive. CloudFront (C) does not support UDP traffic. Route 53 latency-based routing (D) directs users to the nearest endpoint, but with only one endpoint in eu-central-1, it provides no benefit.

### Q37: Auto Scaling default termination policy behavior during scale-in across two AZs

An environment has an Auto Scaling group across two Availability Zones referred to as AZ-a and AZ-b. AZ-a has four Amazon EC2 instances, and AZ-b has three EC2 instances. The Auto Scaling group uses a default termination policy. None of the instances are protected from a scale-in event.

How will Auto Scaling proceed if there is a scale-in event?

- A. Auto Scaling selects an instance to terminate randomly.
- B. Auto Scaling terminates the instance with the oldest launch configuration of all instances.
- C. Auto Scaling selects the Availability Zone with four EC2 instances, and then continues to evaluate.
- D. Auto Scaling terminates the instance with the closest next billing hour of all instances.

**Answer: C**

The default termination policy first selects the AZ with the most instances to maintain balance across AZs (AZ-a with 4 instances). Then within that AZ, it evaluates further: selecting the instance with the oldest launch configuration/template, then the one closest to the next billing hour, and finally randomly if still tied. It does not select randomly across all AZs (A), nor does it skip the AZ-balancing step to evaluate launch configuration globally (B) or billing hour globally (D).

### Q38: Fix S3 bucket policy error missing required element

An administrator wants to apply a resource-based policy to the S3 bucket named "iam-policy-testbucket" to restrict access and to allow accounts to only write objects to the bucket. When the administrator tries to apply the following policy to the "iam-policy-testbucket" bucket, the S3 bucket presents an error.

`{ "Version": "2012-10-17", "Id": "Policy1646946718956", "Statement": [ { "Sid": "Stmt1646946717210", "Effect": "Allow", "Action": "s3:PutObject", "Resource": "arn:aws:s3:::iam-policy-testbucket/*" } ] }`

How can the administrator correct the policy to resolve the error and successfully apply the policy?

- A. Change the Action element from s3:PutObject to s3:*.
- B. Remove the Resource element because it is unnecessary for resource-based policies.
- C. Change the Resource element to NotResource.
- D. Add a Principal element to the policy to declare which accounts have access.

**Answer: D**

Resource-based policies (such as S3 bucket policies) require a Principal element to specify who is granted or denied access. Without it, the policy is invalid and S3 rejects it. Changing the Action to s3:* (A) broadens permissions but doesn't fix the missing Principal. Removing the Resource element (B) is incorrect — Resource is required in bucket policies. Changing to NotResource (C) inverts the resource scope but doesn't address the missing Principal.

### Q39: Collect 50,000 page clicks per second and process sequentially per user

A company's website receives 50,000 requests each second. The company wants to use multiple applications to analyze the navigation patterns of the website users so that the experience can be personalized.

Which AWS service or feature should a solutions architect use to collect page clicks for the website and process them sequentially for each user?

- A. Amazon Kinesis Data Streams
- B. Amazon Simple Queue Service (Amazon SQS) standard queue
- C. Amazon Simple Queue Service (Amazon SQS) FIFO queue
- D. AWS CloudTrail

**Answer: A**

Kinesis Data Streams handles massive throughput (tens of thousands of records per second) and uses partition keys to guarantee sequential processing within each shard — ideal for per-user ordering. SQS standard queues (B) do not guarantee message ordering. SQS FIFO queues (C) guarantee ordering but are limited to 300 transactions per second (3,000 with batching), far below the 50,000 requirement. CloudTrail (D) logs AWS API calls, not website clickstream data.

### Q40: Prevent direct linking to S3 bucket assets for a hosted website

A company is designing a website that will be hosted on Amazon S3.

How should users be prevented from linking directly to the assets in the S3 bucket?

- A. Create a static website, then update the bucket policy to require users to access the resources with the static website URL.
- B. Create an Amazon CloudFront distribution with an origin access control (OAC) and update the bucket policy to grant permission to the OAC only.
- C. Create a static website, then configure an Amazon Route 53 record set with an alias pointing to the static website. Provide this URL to users.
- D. Create an Amazon CloudFront distribution with an AWS WAF web ACL that permits access to the origin server through the distribution only.

**Answer: B**

CloudFront with an origin access control (OAC) restricts S3 bucket access so that only the CloudFront distribution can read objects. The bucket policy explicitly denies all access except from the OAC, preventing users from bypassing CloudFront with direct S3 URLs. Updating the bucket policy for the static website URL (A) does not prevent direct S3 object access. A Route 53 alias (C) provides a friendly URL but doesn't restrict direct bucket access. A WAF web ACL (D) filters incoming requests at the CloudFront level but does not prevent direct S3 URL access.

### Q41: Private subnet access to DynamoDB without leaving the AWS network

An application running in a private subnet accesses an Amazon DynamoDB table. The data cannot leave the AWS network to meet security requirements.

How should this requirement be met?

- A. Configure a network ACL on DynamoDB to limit traffic to the private subnet.
- B. Enable DynamoDB encryption at rest using an AWS Key Management Service (AWS KMS) key.
- C. Add a NAT gateway and configure the route table on the private subnet.
- D. Create a VPC endpoint for DynamoDB and configure the endpoint policy.

**Answer: D**

A VPC gateway endpoint for DynamoDB keeps all traffic between the private subnet and DynamoDB within the AWS network — no internet traversal. Network ACLs (A) cannot be applied directly to DynamoDB, which is a managed service outside the VPC. Encryption at rest (B) protects stored data but does not control network routing. A NAT gateway (C) routes traffic through the public internet, which violates the requirement that data cannot leave the AWS network.

### Q42: Private connectivity to AWS services without public internet, most operationally efficient

A solutions architect is designing a secure cloud-based application that uses Amazon EC2 instances within a VPC. The application uses other supported AWS services within the same Region. The network traffic between the instances and AWS services must remain private and must not travel across the public internet.

Which service or resource will meet the security requirement with the MOST operational efficiency?

- A. Internet gateway
- B. NAT gateway
- C. VPC endpoint
- D. AWS Direct Connect

**Answer: C**

VPC endpoints (gateway and interface) provide private connectivity between a VPC and supported AWS services entirely within the AWS network, with minimal setup and no infrastructure to manage. An internet gateway (A) enables public internet access, violating the private traffic requirement. A NAT gateway (B) routes traffic through the public internet. AWS Direct Connect (D) provides dedicated private connectivity from on-premises to AWS but is unnecessary and expensive for VPC-to-service communication within the same Region.

### Q43: Backup website with contact info when primary EC2 site is down

A company runs a website on Amazon EC2 instances behind an ELB Application Load Balancer. Amazon Route 53 is used for the DNS. The company wants to set up a backup website with a message including a phone number and email address that users can reach if the primary website is down.

How should the company deploy this solution?

- A. Use Amazon S3 website hosting for the backup website and a Route 53 failover routing policy.
- B. Use Amazon S3 website hosting for the backup website and a Route 53 latency routing policy.
- C. Deploy the application in another AWS Region and use ELB health checks for failover routing.
- D. Deploy the application in another AWS Region and use server-side redirection on the primary website.

**Answer: A**

S3 static website hosting is ideal for a simple backup page (static HTML with contact info) — it's highly available, low cost, and requires no servers. Route 53 failover routing automatically directs traffic to the S3 backup when the primary ALB health check fails. Latency routing (B) routes based on lowest latency, not health status — it won't failover when the primary is down. Deploying a full application in another Region (C) is overkill for a simple static message page. Server-side redirection (D) requires the primary website to be running, which defeats the purpose of a backup when it's down.

### Q44: SQS dead-letter queue for failed microservice messages

A solutions architect is redesigning a monolithic application to be a loosely coupled application composed of two microservices: Microservice A and Microservice B.

Microservice A places messages in a main Amazon Simple Queue Service (Amazon SQS) queue for Microservice B to consume. When Microservice B fails to process a message after four retries, the message needs to be removed from the queue and stored for further investigation.

What should the solutions architect do to meet these requirements?

- A. Create an SQS dead-letter queue. Microservice B adds failed messages to that queue after it receives and fails to process the message four times.
- B. Create an SQS dead-letter queue. Configure the dead-letter queue to deliver messages to the dead-letter queue after the message has been received four times.
- C. Create an SQS queue for failed messages. Microservice A adds failed messages to that queue after Microservice B receives and fails to process the message four times.
- D. Create an SQS queue for failed messages. Configure the SQS queue for failed messages to pull messages from the main SQS queue after the original message has been received four times.

**Answer: B**

An SQS dead-letter queue (DLQ) with a redrive policy configured on the main queue automatically moves messages that exceed the maxReceiveCount (set to 4) to the DLQ. This is a native SQS feature that requires no application code changes. Option A puts the burden on Microservice B to manually route messages, adding complexity. Options C and D describe non-standard patterns — SQS queues don't pull from other queues, and having Microservice A track Microservice B's failures breaks the decoupled architecture.

### Q45: Storage for high random IOPS with consistent performance and persistence

A solutions architect is designing a database solution that must support a high rate of random disk reads and writes. It must provide consistent performance, and requires long-term persistence.

Which storage solution meets these requirements?

- A. An Amazon Elastic Block Store (Amazon EBS) Provisioned IOPS volume
- B. An Amazon Elastic Block Store (Amazon EBS) General Purpose volume
- C. An Amazon Elastic Block Store (Amazon EBS) magnetic volume
- D. An Amazon EC2 instance store

**Answer: A**

EBS Provisioned IOPS (io1/io2) volumes are designed for I/O-intensive workloads that require consistently high random read/write performance, making them ideal for databases. General Purpose (gp2/gp3) volumes (B) provide burstable performance but cannot guarantee sustained high IOPS. Magnetic volumes (C) offer the lowest performance tier and are not suitable for high IOPS workloads. EC2 instance store (D) provides high I/O but is ephemeral — data is lost when the instance stops or terminates, failing the persistence requirement.

### Q46: Caching strategy that ensures cache is never stale on writes

A company has a three-tier architecture solution in which an application writes to a relational database. Because of frequent requests, the company wants to cache data whenever the application writes data to the database. The company's priority is to minimize latency for data retrieval and to ensure that data in the cache is never stale.

Which caching strategy should the company use to meet these requirements?

- A. Amazon ElastiCache with write-through
- B. Amazon DynamoDB Accelerator (DAX)
- C. Amazon ElastiCache with lazy loading
- D. Amazon Simple Queue Service (Amazon SQS)

**Answer: A**

Write-through caching updates the cache simultaneously whenever data is written to the database, ensuring the cache is always in sync and never stale. This directly meets the requirement. Lazy loading (C) only populates the cache on read misses, meaning stale data can be served until the cache entry expires — violating the "never stale" requirement. DAX (B) is a caching layer specifically for DynamoDB, not relational databases. SQS (D) is a messaging service, not a caching solution.

### Q47: Waiting list service that preserves customer order

A restaurant reservation application needs to access a waiting list. When a customer tries to reserve a table, and none are available, the customer application will put the user on the waiting list, and the application will notify the customer when a table becomes free. The waiting list must preserve the order in which customers were added to the waiting list.

Which service should the solutions architect recommend to store this waiting list?

- A. Amazon Simple Notification Service (Amazon SNS)
- B. AWS Step Functions invoking AWS Lambda functions
- C. A FIFO queue in Amazon Simple Queue Service (Amazon SQS)
- D. A standard queue in Amazon Simple Queue Service (Amazon SQS)

**Answer: C**

An SQS FIFO (First-In-First-Out) queue guarantees that messages are processed in exactly the order they were sent, which is essential for a waiting list where position order matters. A standard SQS queue (D) provides best-effort ordering but does not guarantee strict order, so customers could be served out of turn. SNS (A) is a pub/sub notification service, not a queue for ordered message storage. Step Functions (B) is a workflow orchestration service, not designed for ordered queue management.

### Q48: Resources that still generate costs when EC2 instances are stopped (Select TWO)

A company stops a cluster of Amazon EC2 instances over a weekend. The costs decrease, but they do not drop to zero.

Which resources could still be generating costs? (Select TWO.)

- A. Elastic IP addresses
- B. Data transfer out
- C. Regional data transfers
- D. Amazon Elastic Block Store (Amazon EBS) volumes
- E. AWS Auto Scaling

**Answer: A, D**

Elastic IP addresses (A) incur charges when they are allocated but not associated with a running instance — stopped instances count as unassociated. EBS volumes (D) continue to incur storage charges regardless of whether the attached EC2 instance is running or stopped. Data transfer out (B) and regional data transfers (C) only occur when instances are actively sending data, which doesn't happen when they're stopped. Auto Scaling (E) is a free service — you only pay for the underlying resources it manages.

### Q49: Secure file downloads reducing web server load, users access only their own files

An application provides a feature that allows users to securely download private and personal files. The web server is currently overwhelmed with serving files for download. A solutions architect must find a more effective solution to reduce the web server load and cost, and must allow users to download only their own files.

Which solution meets all requirements?

- A. Store the files securely on Amazon S3 and have the application generate an Amazon S3 presigned URL for the user to download.
- B. Store the files in an encrypted Amazon Elastic Block Store (Amazon EBS) volume, and use a separate set of servers to serve the downloads.
- C. Have the application encrypt the files and store them in the local Amazon EC2 instance store prior to serving them up for download.
- D. Create an Amazon CloudFront distribution to distribute and cache the files.

**Answer: A**

S3 presigned URLs offload file serving from the web server to S3 (reducing load and cost) while ensuring security — each URL is time-limited and scoped to a specific object, so users can only download their own files. EBS with separate servers (B) doesn't reduce infrastructure burden — it just shifts it. EC2 instance store (C) is ephemeral and doesn't reduce web server load. CloudFront (D) caches and distributes content but doesn't inherently restrict users to only their own files without additional authorization logic.

### Q50: Resolving EC2 QUOTA EXCEEDED error with Auto Scaling

A company has decided to use AWS to achieve high availability. The company's architecture consists of an Application Load Balancer in front of an Auto Scaling group that consists of Amazon EC2 instances. The company uses Amazon CloudWatch metrics and alarms to monitor the architecture. A solutions architect notices that the company is not able to launch some instances. The solutions architect finds the following message: EC2 QUOTA EXCEEDED.

How can the solutions architect ensure that the company is able to launch all the EC2 instances correctly?

- A. Modify the Auto Scaling group to raise the maximum number of instances that the company can launch.
- B. Use Service Quotas to request an increase to the number of EC2 instances that the company can launch.
- C. Recreate the Auto Scaling group to ensure the Auto Scaling group is connected to the Application Load Balancer.
- D. Modify the CloudWatch metric that the company monitors to launch the instances.

**Answer: B**

The "EC2 QUOTA EXCEEDED" error indicates the AWS account has hit its service quota (limit) for the number of EC2 instances in the Region. The fix is to request a quota increase through AWS Service Quotas. Modifying the Auto Scaling group max (A) changes the desired capacity ceiling but doesn't address the account-level quota. Recreating the Auto Scaling group (C) doesn't affect service quotas. Modifying CloudWatch metrics (D) changes monitoring, not instance launch capacity.

### Q51: Improving Athena query performance on large CSV dataset (Select TWO)

A company wants to measure the effectiveness of its recent marketing campaigns. The company performs batch processing on .csv files of sales data and stores the results in an Amazon S3 bucket once every hour. The S3 bucket contains petabytes of objects. The company runs one-time queries in Amazon Athena to determine which products are most popular on a particular date for a particular region. Queries sometimes fail or take longer than expected to finish running.

Which actions should a solutions architect take to improve the query performance and reliability? (Select TWO.)

- A. Reduce the S3 object sizes to less than 128 MB.
- B. Partition the data by date and region in Amazon S3.
- C. Store the files as large, single objects in Amazon S3.
- D. Use Amazon Kinesis Data Analytics to run the queries as part of the batch processing operation.
- E. Use an AWS Glue extract, transform, and load (ETL) process to convert the .csv files into Apache Parquet format.

**Answer: B, E**

Partitioning data by date and region (B) allows Athena to scan only the relevant subset of data instead of the entire petabyte-scale dataset, drastically reducing query time and cost. Converting CSV to Apache Parquet (E) provides columnar storage with compression, which Athena reads far more efficiently than row-based CSV — reducing data scanned and improving performance. Reducing object sizes (A) doesn't address the core issue of scanning too much data. Storing as large single objects (C) would actually worsen performance. Kinesis Data Analytics (D) is for real-time streaming analytics, not one-time batch queries.

### Q52: Decoupling click-tracking infrastructure for fastest SQL access to new data

A media company has an application that tracks user clicks on its websites and performs analytics to provide near-real-time recommendations. The application has a fleet of Amazon EC2 instances that receive data from the websites and send the data to an Amazon RDS DB instance for long-term retention. Another fleet of EC2 instances handles the portion of the application that is continuously checking changes in the database and running SQL queries to provide recommendations.

Management has requested a redesign to decouple the infrastructure. The solution must ensure that data analysts are writing SQL to analyze the new data only. No data can be lost during the deployment.

What should a solutions architect recommend to meet these requirements and to provide the FASTEST access to the user activity?

- A. Use Amazon Kinesis Data Streams to capture the data from the websites, Kinesis Data Firehose to persist the data on Amazon S3, and Amazon Athena to query the data.
- B. Use Amazon Kinesis Data Streams to capture the data from the websites, Kinesis Data Analytics to query the data, and Kinesis Data Firehose to persist the data on Amazon S3.
- C. Use Amazon Simple Queue Service (Amazon SQS) to capture the data from the websites, keep the fleet of EC2 instances, and change to a bigger instance type in the Auto Scaling group configuration.
- D. Use Amazon Simple Notification Service (Amazon SNS) to receive data from the websites and proxy the messages to AWS Lambda functions that run the queries and persist the data. Change Amazon RDS to Amazon Aurora Serverless to persist the data.

**Answer: B**

Kinesis Data Streams captures click data in real time with no data loss. Kinesis Data Analytics allows running SQL queries directly on the streaming data, providing the fastest access to new user activity — analysts see data as it arrives rather than waiting for it to land in storage. Kinesis Data Firehose handles persistence to S3 for long-term storage. Option A uses Athena, which queries data at rest in S3 — slower since data must first be delivered and stored. Option C doesn't decouple the architecture. Option D introduces unnecessary complexity with SNS and Lambda for what is fundamentally a streaming analytics use case.

### Q53: Ensuring high availability for S3-Lambda-DynamoDB-RDS application

A team has an application that detects when new objects are uploaded into an Amazon S3 bucket. The uploads invoke an AWS Lambda function to write object metadata into an Amazon DynamoDB table and an Amazon RDS for PostgreSQL database.

Which action should the team take to ensure high availability?

- A. Enable Cross-Region Replication in the S3 bucket.
- B. Create a Lambda function for each Availability Zone the application is deployed in.
- C. Enable Multi-AZ on the RDS for PostgreSQL database.
- D. Create a DynamoDB stream for the DynamoDB table.

**Answer: C**

RDS for PostgreSQL is the only component in this architecture that is not highly available by default. S3 is inherently highly available (99.99%), Lambda automatically runs across multiple AZs, and DynamoDB replicates across three AZs by default. Enabling Multi-AZ on RDS creates a standby replica in a different AZ with automatic failover, eliminating the single point of failure. Cross-Region Replication (A) addresses disaster recovery, not in-Region high availability. Creating per-AZ Lambda functions (B) is unnecessary since Lambda is already multi-AZ. DynamoDB Streams (D) enables change data capture but doesn't improve availability.

### Q54: Audio version of product manual with custom pronunciations, least operational overhead

A company wants to create an audio version of its product manual. The product manual contains custom product names and abbreviations. The product manual is divided into sections.

Which solution will meet these requirements with the LEAST operational overhead?

- A. Use Amazon Polly. Build custom lexicons for the product names and abbreviations. Use the StartSpeechSynthesisTask API operation for each section of the product manual.
- B. Use Amazon Polly. Build custom Speech Synthesis Markup Language (SSML) for the product names and abbreviations. Use the StartDocumentTextDetection API operation for each section of the product manual.
- C. Use Amazon Textract. Build custom Speech Synthesis Markup Language (SSML) for the product names and abbreviations. Use the StartDocumentTextDetection API operation for each section of the product manual.
- D. Use Amazon Textract. Build custom lexicons for the product names. Use the StartTranscriptionJob API operation for each section of the product manual.

**Answer: A**

Amazon Polly is the AWS text-to-speech service, and custom lexicons allow you to define how custom product names and abbreviations are pronounced. StartSpeechSynthesisTask is the correct Polly API for generating speech output. Option B incorrectly pairs Polly with StartDocumentTextDetection, which is an Amazon Textract API for extracting text from images. Option C uses Textract, which extracts text from documents — not a speech service. Option D uses Textract with StartTranscriptionJob, which belongs to Amazon Transcribe (speech-to-text), the opposite direction.

### Q55: Package tracking with variable traffic, searchable by multiple IDs, auto-expire old data

A company is creating a three-tier web application consisting of a web server, an application server, and a database server. The application will track GPS coordinates of packages as they are being delivered. The application will update the database every 0.5 seconds.

The tracking will need to be read as fast as possible for users to check the status of their packages. Only a few packages might be tracked on some days, whereas millions of packages might be tracked on other days. Tracking will need to be searchable by tracking ID, customer ID, and order ID. Orders older than 1 month no longer need to be tracked.

What should a solutions architect recommend to accomplish this with minimal total cost of ownership?

- A. Use Amazon DynamoDB. Activate Auto Scaling for the DynamoDB table. Schedule an automatic deletion script for items older than 1 month.
- B. Use Amazon DynamoDB with global secondary indexes. Activate Auto Scaling for the DynamoDB table and the global secondary indexes. Turn on TTL for the DynamoDB table.
- C. Use an Amazon RDS On-Demand Instance with Provisioned IOPS (PIOPS). Configure Amazon CloudWatch alarms to send notifications when PIOPS are exceeded. Increase and decrease PIOPS as needed.
- D. Use an Amazon RDS Reserved Instance with Provisioned IOPS (PIOPS). Configure Amazon CloudWatch alarms to send notifications when PIOPS are exceeded. Increase and decrease PIOPS as needed.

**Answer: B**

DynamoDB with Auto Scaling handles the highly variable traffic pattern (few to millions of packages) cost-effectively by scaling capacity up and down automatically. Global secondary indexes enable efficient searching by tracking ID, customer ID, and order ID. TTL (Time to Live) automatically deletes items older than 1 month with no additional cost or operational overhead — unlike option A which requires maintaining a separate deletion script. RDS options (C, D) are more expensive, don't scale as elastically for variable workloads, and require more operational management.

### Q56: Multi-Region disaster recovery with 30-minute RTO on a budget

A solutions architect is responsible for a new highly available three-tier architecture on AWS. An Application Load Balancer distributes traffic to two different Availability Zones with an auto scaling group that consists of Amazon EC2 instances and a Multi-AZ Amazon RDS DB instance. The solutions architect must recommend a multi-Region recovery plan with a recovery time objective (RTO) of 30 minutes. Because of budget constraints, the solutions architect cannot recommend a plan that replicates the entire architecture. The recovery plan should not use the secondary Region unless necessary.

Which disaster recovery strategy will meet these requirements?

- A. Backup and restore
- B. Multi-site active/active
- C. Pilot light
- D. Warm standby

**Answer: C**

Pilot light keeps only the most critical core elements running in the secondary Region (such as a cross-Region RDS read replica), while compute resources remain off until needed. This meets the 30-minute RTO — when disaster strikes, the read replica is promoted and compute resources are launched from pre-configured AMIs and templates. It satisfies the budget constraint since only the database replica incurs ongoing cost, not the full architecture. Backup and restore (A) has an RTO of hours, too slow. Multi-site active/active (B) replicates the entire architecture in both Regions, violating the budget constraint. Warm standby (D) runs a scaled-down but fully functional copy, which is more expensive than pilot light and closer to replicating the full architecture.

### Q57: Reducing EBS io2 costs without downtime (Select TWO)

Cost Explorer is showing charges higher than expected for Amazon Elastic Block Store (Amazon EBS) volumes connected to application servers in a production account. A significant portion of the charges from Amazon EBS are from volumes that were created as Provisioned IOPS SSD (io2) volume types. Controlling costs is the highest priority for this application.

Which steps should the user take to analyze and reduce the EBS costs without incurring any application downtime? (Select TWO.)

- A. Use the Amazon EC2 ModifyInstanceAttribute action to enable EBS optimization on the application server instances.
- B. Use the Amazon CloudWatch GetMetricData action to evaluate the read/write operations and read/write bytes of each volume.
- C. Use the Amazon EC2 ModifyVolume action to reduce the size of the underutilized io2 volumes.
- D. Use the Amazon EC2 ModifyVolume action to change the volume type of the underutilized io2 volumes to General Purpose SSD (gp3).
- E. Use an Amazon S3 PutBucketPolicy action to migrate existing volume snapshots to Amazon S3 Glacier Flexible Retrieval.

**Answer: B, D**

First, you need to analyze actual volume usage with CloudWatch metrics (B) to identify which io2 volumes are underutilized — you can't optimize what you haven't measured. Then, for underutilized volumes, use ModifyVolume (D) to change them from expensive io2 to cost-effective gp3, which can be done live without downtime. Enabling EBS optimization (A) doesn't reduce costs — it improves network throughput to EBS. Reducing volume size (C) is not supported by ModifyVolume (you can only increase size). Migrating snapshots via S3 PutBucketPolicy (E) is not a valid operation — EBS snapshots are managed by AWS and cannot be moved to Glacier through S3 bucket policies.

### Q58: Most secure way to grant Lambda access to DynamoDB

A solutions architect is designing a new workload in which an AWS Lambda function will access an Amazon DynamoDB table.

What is the MOST secure means of granting the Lambda function access to the DynamoDB table?

- A. Create an IAM role with the necessary permissions to access the DynamoDB table. Assign the role to the Lambda function.
- B. Create a DynamoDB user name and password and give them to the developer to use in the Lambda function.
- C. Create an IAM user, and create access and secret keys for the user. Give the user the necessary permissions to access the DynamoDB table. Have the developer use these keys to access the resources.
- D. Create an IAM role allowing access from AWS Lambda. Assign the role to the DynamoDB table.

**Answer: A**

IAM roles are the most secure way to grant Lambda access to AWS resources. The Lambda service automatically assumes the execution role and receives temporary credentials — no long-term secrets to manage or risk leaking. DynamoDB doesn't have its own user/password system (B), so that option is invalid. IAM users with access keys (C) require embedding long-term credentials in code, which is a security anti-pattern. Option D incorrectly assigns the role to the DynamoDB table — roles are attached to the compute resource (Lambda), not to the data resource.

### Q59: Mobile app with MFA, minimal build time and maintenance

A company designs a mobile app for its customers to upload photos to a website. The app needs a secure login with multi-factor authentication (MFA). The company wants to limit the initial build time and the maintenance of the solution.

Which solution should a solutions architect recommend to meet these requirements?

- A. Use Amazon Cognito Identity with SMS-based MFA.
- B. Edit IAM policies to require MFA for all users.
- C. Federate IAM against the corporate Active Directory that requires MFA.
- D. Use Amazon API Gateway and require server-side encryption (SSE) for photos.

**Answer: A**

Amazon Cognito provides fully managed user authentication for mobile and web apps with built-in MFA support (SMS, TOTP). It minimizes build time since authentication, user management, and MFA are all handled by the service — no custom auth infrastructure needed. IAM MFA (B) is designed for AWS console and API access, not for mobile app end users. Federating with Active Directory (C) adds complexity and is designed for enterprise/corporate users, not consumer-facing mobile apps. API Gateway with SSE (D) addresses data encryption, not user authentication or MFA.

### Q60: Database for gaming app with unstructured data, millions of users, least operational support

A database architect is designing an online gaming application that uses a simple, unstructured data format. The database must have the ability to store user information and to track the progress of each user. The database must have the ability to scale to millions of users throughout the week.

Which database service will meet these requirements with the LEAST amount of operational support?

- A. Amazon RDS Multi-AZ
- B. Amazon Neptune
- C. Amazon DynamoDB
- D. Amazon Aurora

**Answer: C**

DynamoDB is a fully managed NoSQL database that handles unstructured/semi-structured data natively, scales seamlessly to millions of users with on-demand capacity, and requires virtually no operational support — no patching, no provisioning, no cluster management. RDS Multi-AZ (A) and Aurora (D) are relational databases that require schema design and more operational management, and are better suited for structured data with complex relationships. Neptune (B) is a graph database optimized for highly connected datasets, which is overkill for simple user progress tracking.

### Q61: Shared storage for ECS tasks across AZs with highest throughput

A solutions architect is designing a solution that involves orchestrating a series of Amazon Elastic Container Service (Amazon ECS) tasks. The tasks run on an ECS cluster that uses Amazon EC2 instances across multiple Availability Zones. The output and state data for all tasks must be stored. The amount of data that each task outputs is approximately 10 MB, and hundreds of tasks can run at a time. As old outputs are archived and deleted, the storage size will not exceed 1 TB.

Which storage solution should the solutions architect recommend to meet these requirements with the HIGHEST throughput?

- A. An Amazon DynamoDB table that is accessible by all ECS cluster instances
- B. An Amazon Elastic Block Store (Amazon EBS) volume that is mounted to the ECS cluster instances
- C. An Amazon Elastic File System (Amazon EFS) file system with Bursting Throughput mode
- D. An Amazon Elastic File System (Amazon EFS) file system with Provisioned Throughput mode

**Answer: D**

EFS with Provisioned Throughput mode allows you to specify throughput independent of storage size, guaranteeing high throughput regardless of how much data is stored. With Bursting Throughput mode (C), throughput scales with storage size — at 1 TB, baseline throughput is only ~50 MiB/s, which may not be sufficient for hundreds of concurrent tasks. EBS (B) cannot be shared across instances in different AZs — each volume attaches to a single instance. DynamoDB (A) is a database service, not optimized for storing 10 MB file outputs from tasks.

### Q62: Highly resilient AWS Direct Connect configuration

A solutions architect is designing a hybrid application using the AWS Cloud. The network between the on-premises data center and AWS will use an AWS Direct Connect (DX) connection. The application connectivity between AWS and the on-premises data center must be highly resilient.

Which DX configuration should be implemented to meet these requirements?

- A. Configure a DX connection with a VPN on top of it.
- B. Configure a DX connection using the most reliable DX partner.
- C. Configure multiple virtual interfaces on top of a DX connection.
- D. Configure DX connections at multiple DX locations.

**Answer: D**

Multiple DX connections at different physical DX locations provides true high resiliency by eliminating single points of failure — if one location experiences an outage, traffic fails over to the other. A VPN on top of DX (A) adds encryption but doesn't improve resiliency since it still depends on a single DX connection. Using a reliable DX partner (B) is still a single connection and single point of failure. Multiple virtual interfaces on one DX connection (C) share the same physical link, so a failure of that link takes down all VIFs.

### Q63: Decoupling food ordering app and making it scalable

A company built a food ordering application that captures user data and stores it for future analysis. The application's static front-end is deployed on an Amazon EC2 instance. The front-end application sends the requests to the backend application running on a separate EC2 instance. The backend application then stores the data in Amazon RDS.

What should a solutions architect do to decouple the architecture and make it scalable?

- A. Use Amazon S3 to serve the static front-end application, which sends requests to Amazon EC2 to run the backend application. The backend application will process and store the data in Amazon RDS.
- B. Use Amazon S3 to serve the static front-end application and write requests to an Amazon Simple Notification Service (Amazon SNS) topic. Subscribe the backend Amazon EC2 instance HTTPS endpoint to the topic, and process and store the data in Amazon RDS.
- C. Use an EC2 instance to serve the static front-end application and write requests to an Amazon SQS queue. Place the backend instances in an Auto Scaling group, and scale based on the queue depth to process and store the data in Amazon RDS.
- D. Use Amazon S3 to serve the static front-end application and send requests to Amazon API Gateway, which writes the requests to an Amazon SQS queue. Place the backend instances in an Auto Scaling group, and scale based on the queue length to process and store the data in Amazon RDS.

**Answer: D**

This architecture fully decouples every tier: S3 hosts the static frontend (no EC2 needed), API Gateway provides a managed REST API endpoint, SQS queues requests for asynchronous processing, and Auto Scaling adjusts backend capacity based on queue depth. Option A still has a tightly coupled backend with no scaling. Option B uses SNS which is push-based and doesn't buffer requests — if the backend is overwhelmed, messages are lost. Option C keeps the frontend on EC2, missing the opportunity to decouple it with S3. Only D decouples all layers and enables horizontal scaling.

## References

- [AWS Solutions Architect Associate - Official Exam Guide](https://aws.amazon.com/certification/certified-solutions-architect-associate/)
