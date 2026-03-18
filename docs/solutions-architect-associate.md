---
title: AWS Solutions Architect Associate (SAA-C03)
nav_order: 2
---

# AWS Solutions Architect Associate (SAA-C03)

Notes for the AWS Solutions Architect Associate certification exam.

## Table of Contents

- [Questions](#questions)
- [References](#references)

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

## References

- [AWS Solutions Architect Associate - Official Exam Guide](https://aws.amazon.com/certification/certified-solutions-architect-associate/)
