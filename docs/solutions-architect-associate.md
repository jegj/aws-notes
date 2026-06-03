---
title: AWS Solutions Architect Associate (SAA-C03)
nav_order: 2
---

# AWS Solutions Architect Associate (SAA-C03)

Notes for the AWS Solutions Architect Associate certification exam.

## Table of Contents

- [General Concepts](#aws-solutions-architect-associate---general-concepts)
  - [AWS Infrastructure](#aws-infrastructure)
  - [AWS Data Migration Tools](#aws-data-migration-tools)
  - [Amazon S3 Storage Classes](#amazon-s3-storage-classes)
  - [VPC Endpoints](#vpc-endpoints)
  - [VPC Peering](#vpc-peering)
  - [Hybrid Networking (AWS Site-to-Site VPN)](#hybrid-networking-aws-site-to-site-vpn)
  - [AWS Transit Gateway](#aws-transit-gateway)
  - [Data Ingestion Patterns](#data-ingestion-patterns)
  - [Backup & Recovery](#backup--recovery)
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

### VPC Endpoints

A VPC endpoint lets resources in a VPC privately connect to supported AWS services without an internet gateway, NAT device, VPN connection, or Direct Connect. Traffic stays on the AWS network and never traverses the public internet. There are two types:

- **Gateway endpoint** — A target you add to your route tables for traffic destined to **Amazon S3** and **Amazon DynamoDB** (the only two services supported). There is no additional charge. You control access with endpoint policies and route table entries.
- **Interface endpoint (AWS PrivateLink)** — An elastic network interface (ENI) with a private IP address in your subnet that serves as the entry point for traffic to a supported service. It is used for most AWS services (and your own/third-party services exposed via PrivateLink). Interface endpoints are billed per hour and per GB of data processed.

Quick comparison:

| | Gateway endpoint | Interface endpoint |
| --- | --- | --- |
| **Supported services** | S3 and DynamoDB only | Most AWS services + PrivateLink services |
| **Mechanism** | Route table entry | ENI with private IP |
| **Cost** | Free | Per hour + per GB |

![VPC endpoints — gateway and interface endpoints](https://github.com/user-attachments/assets/dfe3ee2e-23db-47c4-8a26-828099c3dec6)

### VPC Peering

A VPC peering connection is a **one-to-one** networking relationship between two VPCs that lets you route traffic between them using private IP addresses. The VPCs can be in different accounts and different Regions (inter-Region peering).

- With peering you can **bypass the internet gateway/virtual private gateway**, get **highly available** connections (redundant and AWS-managed), **avoid bandwidth bottlenecks** (inter-Region traffic is encrypted and stays on the AWS backbone), and use **private IP addresses** to route traffic.
- **Limitations:** there is a limit on active and pending connections per VPC; you can have only **one** peering connection between the same two VPCs; the maximum transmission unit (MTU) across a connection is **1,500 bytes**.
- Peering is **non-transitive** — if VPC A peers with B and B peers with C, A cannot reach C through B. A direct A–C peering connection is required.
- A common pattern is a **shared services VPC** that many application VPCs peer with. For connecting every VPC to every other VPC you need a **full mesh** of one-to-one connections, which grows quadratically and increases points of failure and monitoring overhead — for many VPCs, AWS Transit Gateway is preferred.

![VPC peering — route tables, non-transitive peering, and full mesh](https://github.com/user-attachments/assets/430cabb5-7bca-4f19-b0b3-bb91f3514868)

### Hybrid Networking (AWS Site-to-Site VPN)

An AWS Site-to-Site VPN connection offers two encrypted VPN tunnels between a **virtual private gateway** (or a **transit gateway**) on the AWS side and a **customer gateway** on the on-premises side. It has two endpoints:

- A **virtual private gateway** is the VPN concentrator on the AWS side of the connection.
- A **customer gateway** is a resource you create in AWS that represents the physical or software device on your on-premises network; you provide its public IP and configuration.
- Routing can be **static** or **dynamic**. Dynamic routing uses **Border Gateway Protocol (BGP)** so routes are advertised automatically.

For consistent, dedicated bandwidth between on-premises and AWS, **AWS Direct Connect** is preferred over a Site-to-Site VPN (which runs over the public internet).

![AWS Site-to-Site VPN — customer gateway and virtual private gateway endpoints](https://github.com/user-attachments/assets/7869c9e5-0034-4d95-b7c2-de7df160d91f)

### AWS Transit Gateway

AWS Transit Gateway acts as a **hub** that controls how traffic is routed among all connected networks, which act as **spokes**. This hub-and-spoke model simplifies management and reduces operational cost because each network connects only to the transit gateway rather than to every other network. Any new VPC connected to the transit gateway is automatically reachable by every other connected network — scaling cleanly to thousands of VPCs and on-premises networks.

- **Components:** a transit gateway is made up of **attachments** and **route tables**. An *attachment* is a source and destination of packets; you can attach a **VPC**, **VPN connection**, **Direct Connect gateway**, **Transit Gateway Connect**, or a **peering connection** (resources must be in the same Region as the transit gateway).
- **Setup / sharing:** a transit gateway works across AWS accounts. You can share it with other accounts using **AWS Resource Access Manager (RAM)**; the other account owner can then attach their VPCs, and either account can delete the attachment.
- Unlike VPC peering, transit gateway routing is **transitive**, so connected networks can reach each other through the hub.

![AWS Transit Gateway — hub-and-spoke model and components](https://github.com/user-attachments/assets/cfad4f48-d224-4930-88f1-5f39bb2ac50b)

### Data Ingestion Patterns

Data ingestion patterns are commonly grouped by whether the data sources share the same format/engine (**homogeneous**) or differ (**heterogeneous**):

- **Homogeneous** — sources and targets use the same data engine/format. Typical services:
  - **Amazon EMR** — big data processing (Spark, Hadoop)
  - **Amazon Athena** — serverless SQL queries over S3
  - **Amazon RDS** — relational databases
  - **Amazon S3** — object storage / data lake
- **Heterogeneous** — combining and transforming data from differing sources. Typical services:
  - **Amazon Redshift** — data warehousing/analytics
  - **Amazon Kinesis** — real-time streaming ingestion
  - **Amazon RDS** — relational databases
  - **Amazon S3** — object storage / data lake
  - **AWS Glue** — serverless ETL to discover, transform, and load data

In practice, streaming data is captured with Kinesis, landed in an S3 data lake, transformed with Glue or EMR, queried with Athena, and analyzed in Redshift.

![Data ingestion patterns — homogeneous vs heterogeneous](https://github.com/user-attachments/assets/8711f4c1-950d-4d9f-9fb7-269897fc86a0)

### Backup & Recovery

Backup and recovery planning is driven by two key metrics and a set of disaster recovery (DR) strategies that trade cost against speed of recovery.

#### Recovery Point Objective (RPO) and Recovery Time Objective (RTO)

- **Recovery Point Objective (RPO)** is the acceptable amount of **data loss measured in time**. It answers *"how much data can we afford to lose?"* — i.e., how far back the last usable backup must be. Example: if a disaster occurs at 1:00 p.m. and the RPO is 12 hours, the system must recover all data that was in the system before 1:00 a.m. (a 12-hour data loss), so backups must run at least every 12 hours.
- **Recovery Time Objective (RTO)** is the acceptable amount of **time to restore service** after a disruption, as defined by the operational level agreement (OLA). It answers *"how quickly must we be back online?"* Example: if a disaster occurs at 1:00 p.m. and the RTO is 1 hour, the DR process must restore the business to the acceptable service level by 2:00 p.m.

![RPO and RTO measured against the moment of disaster](https://github.com/user-attachments/assets/8373672b-7fbc-4a43-9bb3-c54d841b1474)

#### Disaster Recovery Strategies

Ordered from lowest cost / slowest recovery to highest cost / fastest recovery:

- **Backup and restore** — Backups are created in the same Region as the source and also copied to another Region, giving protection from a Region-wide disaster. It is the cheapest option but has the **highest RTO**, since the environment must be provisioned and restored from backups before service resumes.
- **Pilot light** — Core data is **live** (data stores and databases are kept up to date via replication to the recovery Region), but the services are **off**. Basic infrastructure such as Elastic Load Balancing and Amazon EC2 Auto Scaling is in place, with the compute/functional elements switched "off" until failover, when they are started and scaled up.
- **Warm standby (fully working low-capacity standby)** — A fully working deployment runs continuously at **reduced capacity** that can handle requests but not full production traffic. During failover the infrastructure must scale up to meet production needs. Faster RTO than pilot light at higher cost.
- **Multi-site active/active** — Two or more Regions **actively serve requests**. Failover consists of re-routing requests away from a failed Region; data is replicated across Regions and is actively used to read/write in each. This gives the **lowest RTO** at the **highest cost**, using routing patterns (latency-based, geolocation, or weighted) to direct traffic.

![Disaster recovery strategies — backup and restore, pilot light, warm standby, and multi-site active/active](https://github.com/user-attachments/assets/43ae5d22-102d-4827-bed8-095e250c9712)

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

### Q64: Cost-effective Aurora dev cluster for intermittent use

A company wants to deploy an additional Amazon Aurora MySQL DB cluster for development purposes. This cluster will be used several times a week for a few minutes upon request to debug production query issues. The company wants to keep overhead low for this resource.

Which solution meets the company's requirements MOST cost-effectively?

- A. Purchase a Reserved Instance for the DB instances.
- B. Run the DB instances on Aurora Serverless.
- C. Create a stop/start schedule for the DB instances.
- D. Create an AWS Lambda function to stop DB instances if there are no active connections.

**Answer: B**

Aurora Serverless automatically starts, scales, and pauses based on demand — you pay only for the capacity consumed while the cluster is active. This is ideal for an intermittent, on-request workload used only a few minutes a week, and it requires no manual management. Reserved Instances (A) are cost-effective only for steady-state, always-on workloads. A stop/start schedule (C) doesn't fit because usage is on-request and unpredictable, not on a fixed schedule. A custom Lambda function (D) adds operational overhead that Aurora Serverless eliminates natively.

### Q65: S3 storage class for logs with unpredictable access patterns

A solutions architect at an ecommerce company wants to store application log data using Amazon S3. The solutions architect is unsure how frequently the logs will be accessed or which logs will be accessed the most. The company wants to keep costs as low as possible by using the appropriate S3 storage class.

Which S3 storage class should be implemented to meet these requirements?

- A. S3 Glacier Flexible Retrieval (formerly S3 Glacier)
- B. S3 Intelligent-Tiering
- C. S3 Standard-Infrequent Access (S3 Standard-IA)
- D. S3 One Zone-Infrequent Access (S3 One Zone-IA)

**Answer: B**

S3 Intelligent-Tiering automatically moves objects between frequent and infrequent access tiers based on changing access patterns, optimizing cost without performance impact or operational overhead. It is purpose-built for exactly this scenario — unknown or unpredictable access patterns. Glacier (A) is for archival with retrieval delays. Standard-IA (C) and One Zone-IA (D) assume you already know the data is infrequently accessed and charge retrieval fees, which is risky when access patterns are unknown.

### Q66: Overlooked cost factor for stopped EC2 data warehouse

A company runs an application on three very large Amazon EC2 instances in a single Availability Zone in the us-east-1 Region. Multiple 16 TB Amazon Elastic Block Store (Amazon EBS) volumes are attached to each EC2 instance. The operations team uses an AWS Lambda script triggered by a schedule-based Amazon EventBridge rule to stop the instances on evenings and weekends, and start the instances on weekday mornings.

Before deploying the solution, the company used the public AWS pricing documentation to estimate the overall costs of running this data warehouse solution 5 days a week for 10 hours a day. When looking at monthly Cost Explorer charges for this new account, the overall charges are higher than the estimate.

What is the MOST likely cost factor that the company overlooked?

- A. EC2 data transfer charges between the instances are much higher than expected.
- B. EC2 and EBS rates are higher in us-east-1 than most other AWS Regions.
- C. The Lambda charges to stop and start the instances are much higher than expected.
- D. The company is being billed for the EBS storage on nights and weekends.

**Answer: D**

EBS volumes continue to incur storage charges 24/7 regardless of whether the attached EC2 instances are running or stopped. With multiple 16 TB volumes per instance across three instances, that's a substantial ongoing cost that persists during evenings and weekends — easily overlooked when estimating based only on EC2 running hours. Data transfer between instances (A) is unlikely to be significant in this scenario. us-east-1 (B) is one of the cheapest AWS Regions, not more expensive. Lambda charges (C) for a simple stop/start script are negligible and covered by the free tier.

### Q67: Improving Site-to-Site VPN throughput

A company is using AWS Site-to-Site VPN connections for secure connectivity to its AWS Cloud resources from on-premises locations. Due to an increase in traffic across the VPN connections to the Amazon EC2 instances, users are experiencing slower VPN connectivity. The network team reports that the internet connection used for the VPN has additional unused throughput.

Which solution will improve the VPN throughput?

- A. Implement multiple customer gateways for the same network.
- B. Configure a virtual private gateway with equal cost multipath routing and multiple channels.
- C. Use a transit gateway with equal cost multipath routing and add additional VPN tunnels.
- D. Increase the number of tunnels in the VPN configuration.

**Answer: C**

A single Site-to-Site VPN tunnel is limited to ~1.25 Gbps of throughput. To scale beyond that, a transit gateway supports Equal Cost Multipath (ECMP) routing, which aggregates bandwidth across multiple VPN tunnels — distributing traffic and increasing total throughput. A virtual private gateway (B) does not support ECMP for aggregating VPN bandwidth. Multiple customer gateways (A) alone don't aggregate throughput. Simply adding tunnels to a VPN connection (D) doesn't help because, without ECMP on a transit gateway, the second tunnel serves as failover/redundancy, not aggregation.

### Q68: Serverless processing of S3 location uploads with auto-scaling

A user is designing a new service that receives location updates from 3,600 rental cars every hour. The cars upload their location to an Amazon S3 bucket. Each location must be checked for distance from the original rental location.

Which services will process the updates and automatically scale?

- A. Amazon EC2 and Amazon Elastic Block Store (Amazon EBS)
- B. Amazon Data Firehose and Amazon S3
- C. Amazon Elastic Container Service (Amazon ECS) and Amazon RDS
- D. Amazon S3 events and AWS Lambda

**Answer: D**

S3 event notifications automatically trigger an AWS Lambda function whenever a car uploads its location to the bucket. Lambda processes each update (calculating distance from the original location) and scales automatically to handle any number of concurrent uploads — no servers to manage. EC2 with EBS (A) requires manual scaling configuration. Data Firehose (B) is for streaming data delivery, not running custom processing logic. ECS with RDS (C) requires provisioning and managing container infrastructure and a database, adding unnecessary overhead.

### Q69: Applying least privilege to security group rules between tiers

A company is reviewing a recent migration of a three-tier application to a VPC. The security team discovers that the principle of least privilege is not being applied to Amazon EC2 security group ingress and egress rules between the application tiers.

What should a solutions architect do to correct this issue?

- A. Create security group rules using the instance ID as the source or destination.
- B. Create security group rules using the security group ID as the source or destination.
- C. Create security group rules using the VPC CIDR blocks as the source or destination.
- D. Create security group rules using the subnet CIDR blocks as the source or destination.

**Answer: B**

Referencing another security group's ID as the source or destination enforces least privilege — only instances belonging to that specific security group can communicate, regardless of their IP addresses, and it automatically adapts as instances are added or removed. Instance IDs (A) cannot be used as a source/destination in security group rules. VPC CIDR blocks (C) allow the entire VPC, and subnet CIDR blocks (D) allow an entire subnet — both are far too broad and violate the principle of least privilege.

### Q70: Querying an S3 data lake with infrequent SQL queries at minimal cost

A company is developing a data lake solution in Amazon S3 to analyze large-scale datasets. The solution makes infrequent SQL queries only. In addition, the company wants to minimize infrastructure costs.

Which AWS service should be used to meet these requirements?

- A. Amazon Athena
- B. Amazon Redshift Spectrum
- C. Amazon RDS for PostgreSQL
- D. Amazon Aurora

**Answer: A**

Amazon Athena is a serverless, interactive query service that runs standard SQL directly against data in S3. You pay only per query based on the amount of data scanned — with no infrastructure to provision or manage — making it ideal for infrequent queries at minimal cost. Redshift Spectrum (B) requires a running Redshift cluster, incurring ongoing infrastructure cost. RDS for PostgreSQL (C) and Aurora (D) require loading data into a managed database that runs continuously, which is unnecessary cost and effort for infrequent ad-hoc queries on a data lake.

### Q71: Cost-effective storage for rarely accessed documents that must stay immediately available

A company has a three-tier web application on AWS for document storage and retrieval. The application stores documents on a shared NFS volume and references documents by using a Multi-AZ deployment of an Amazon RDS for MySQL DB instance. The document metadata is consulted regularly. The documents are not accessed more than one time a year, but they must remain immediately available. A solutions architect needs to optimize the workload and implement application modifications.

Which solution will meet these requirements MOST cost-effectively?

- A. Use an Amazon FSx for Lustre shared volume for document storage. Use a Multi-AZ deployment of an RDS for MySQL DB instance to keep document metadata.
- B. Use an Amazon S3 bucket with the S3 Glacier Deep Archive storage class for document storage. Use an Amazon DynamoDB table to keep document metadata.
- C. Use an Amazon S3 bucket with the S3 Standard-Infrequent Access (S3 Standard-IA) storage class for document storage. Use an Amazon DynamoDB table to keep document metadata.
- D. Use an Amazon Elastic File System (Amazon EFS) file system with the EFS Standard-Infrequent Access (EFS Standard-IA) storage class for document storage. Use a Multi-AZ deployment of an RDS for MySQL DB instance to keep document metadata.

**Answer: C**

S3 Standard-IA is designed for data accessed infrequently (such as once a year) while still providing immediate, millisecond access — meeting the "must remain immediately available" requirement at a lower storage cost than S3 Standard. DynamoDB is a cost-effective, fully managed store for the regularly consulted metadata. Glacier Deep Archive (B) is the cheapest storage but has retrieval times of hours, violating the immediate-availability requirement. FSx for Lustre (A) is a high-performance file system for HPC, far too expensive for archival. EFS Standard-IA (D) costs more than S3 Standard-IA, and keeping RDS Multi-AZ for simple metadata is more expensive than DynamoDB.

### Q72: Reducing NAT gateway costs for EMR jobs accessing S3

An application team has started using Amazon EMR to run batch jobs, using datasets located in Amazon S3. During the initial testing of the workload, a solutions architect notices that the account is starting to accrue NAT gateway data processing costs.

How can the team optimize the cost of the workload?

- A. Detach the NAT gateway from the subnet where the Amazon EMR clusters are running.
- B. Replace the NAT gateway with a customer gateway.
- C. Replace the NAT gateway with an S3 VPC endpoint.
- D. Configure a network ACL on the subnets where the Amazon EMR clusters are running to open access to Amazon S3.

**Answer: C**

A gateway VPC endpoint for S3 routes traffic between the EMR clusters and S3 directly over the AWS network, bypassing the NAT gateway entirely — and gateway endpoints incur no data processing charges. This eliminates the NAT data processing costs while preserving connectivity. Detaching the NAT gateway (A) would break the EMR clusters' other internet-bound traffic. A customer gateway (B) is a VPN component, irrelevant here. A network ACL (D) controls traffic permissions but doesn't change routing, so traffic would still flow through the NAT gateway and accrue charges.

### Q73: Securing a web tier and private database tier without disrupting running apps

A company hosts a popular web application. The web application connects to a database running in a private VPC subnet. The web servers must be accessible only to customers on an SSL connection. The Amazon RDS for MySQL database server must be accessible only from the web servers.

How should a solutions architect design a solution to meet the requirements without impacting running applications?

- A. Create a network ACL on the web server's subnet, allow HTTPS inbound and MySQL outbound. Place both database and web servers on the same subnet.
- B. Open an HTTPS port on the security group for web servers and set the source to 0.0.0.0/0. Open the MySQL port on the database security group and attach it to the MySQL instance. Set the source to web server security group.
- C. Create a network ACL on the web server's subnet; allow HTTPS inbound, and specify the source as 0.0.0.0/0. Create a network ACL on a database subnet, allow MySQL port inbound for web servers, and deny all outbound traffic.
- D. Open the MySQL port on the security group for web servers and set the source to 0.0.0.0/0. Open the HTTPS port on the database security group and attach it to the MySQL instance. Set the source to web server security group.

**Answer: B**

Security groups are stateful and can be applied without disrupting running applications. Opening HTTPS (443) with source 0.0.0.0/0 on the web server security group allows customers to reach the web tier over SSL. Setting the database security group's MySQL port (3306) source to the web server security group enforces least privilege — only the web servers can reach the database. Network ACLs (A, C) are stateless, harder to manage for stateful traffic, and option A's placement of the database in the web subnet undermines isolation. Option D inverts the ports/services incorrectly (MySQL open to the internet, HTTPS on the database).

### Q74: Minimizing throughput impact of a slow-running API endpoint

A company has a web application that makes requests to a backend API service. The API service runs on Amazon EC2 instances accessed behind an Elastic Load Balancer.

Most backend API service endpoint calls finish very quickly, but one endpoint that makes calls to create objects in an external service takes a long time to complete. These long-running calls are causing client timeouts and increasing overall system latency.

What should be done to minimize the system throughput impact of the slow-running endpoint?

- A. Change the EC2 instance size to increase memory and compute capacity.
- B. Use Amazon Simple Queue Service (Amazon SQS) to offload the long-running requests for asynchronous processing by separate workers.
- C. Increase the load balancer idle timeout to allow the long-running requests to complete.
- D. Use Amazon ElastiCache for Redis to cache responses from the external service.

**Answer: B**

Offloading the long-running object-creation requests to an SQS queue decouples them from the synchronous request path. Separate worker processes consume the queue and call the external service asynchronously, so slow calls no longer block resources or cause client timeouts — preserving overall system throughput. Increasing instance size (A) doesn't fix the blocking behavior. Increasing the load balancer idle timeout (C) just makes clients wait longer instead of eliminating the bottleneck. ElastiCache (D) caches read responses, but these calls create objects (writes), so caching doesn't apply.

### Q75: Least-privilege IAM policy for a Lambda function accessing one DynamoDB table

A company has implemented one of its microservices on AWS Lambda that accesses an Amazon DynamoDB table named "Books". A solutions architect is designing an IAM policy to be attached to the Lambda function's IAM role giving it access to put, update, and delete items in the "Books" table. The IAM policy must prevent the function from performing any other actions on the "Books" table and any other table.

Which IAM policy would fulfill these needs and provide the LEAST privileged access?

- A. Allow only the `dynamodb:PutItem`, `dynamodb:UpdateItem`, and `dynamodb:DeleteItem` actions on the resource `arn:aws:dynamodb:us-west-2:123456789012:table/Books`.
- B. Allow the `dynamodb:PutItem`, `dynamodb:UpdateItem`, and `dynamodb:DeleteItem` actions on the resource `arn:aws:dynamodb:us-west-2:123456789012:table/*` (all tables).
- C. Allow the action `dynamodb:*` (all DynamoDB actions) on the resource `arn:aws:dynamodb:us-west-2:123456789012:table/Books`.
- D. Allow `dynamodb:*` on the `Books` table, plus a separate statement that denies `dynamodb:*` on `*`.

**Answer: A**

Granting exactly the three required actions (PutItem, UpdateItem, DeleteItem) scoped to the specific `table/Books` resource ARN is the definition of least privilege — nothing more is allowed. Option B uses a wildcard resource (`table/*`), granting access to all tables. Option C allows every DynamoDB action (`dynamodb:*`) on the Books table, far more than the three needed operations. Option D both over-grants (all actions on Books) and creates a conflicting/over-broad explicit Deny that would also block the intended write actions.

### Q76: Relational database with multi-Region DR, 1-second RPO and 1-minute RTO

A company needs to implement a relational database with a multi-Region disaster recovery Recovery Point Objective (RPO) of 1 second and a Recovery Time Objective (RTO) of 1 minute.

Which AWS solution can achieve this?

- A. Amazon Aurora Global Database
- B. Amazon DynamoDB global tables
- C. Amazon RDS for MySQL with Multi-AZ turned on
- D. Amazon RDS for MySQL with a cross-Region snapshot copy

**Answer: A**

Aurora Global Database replicates data across Regions with typical latency under 1 second (meeting the 1-second RPO) and supports promoting a secondary Region in under a minute (meeting the 1-minute RTO) — and it is a relational database. DynamoDB global tables (B) meet the RPO/RTO but are NoSQL, not relational. RDS Multi-AZ (C) provides high availability within a single Region, not multi-Region DR. Cross-Region snapshot copy (D) has an RPO/RTO measured in hours, far exceeding the requirements.

### Q77: Privately exposing an application to thousands of consumer accounts (PHI)

A company wants to create an application that will transmit protected health information (PHI) to thousands of service consumers in different AWS accounts. The application servers will sit in private VPC subnets. The routing for the application must be fault tolerant.

What should be done to meet these requirements?

- A. Create a VPC endpoint service and grant permissions to specific service consumers to create a connection.
- B. Create a virtual private gateway connection between each pair of service provider VPCs and service consumer VPCs.
- C. Create an internal Application Load Balancer in the service provider VPC and put application servers behind it.
- D. Create a proxy server in the service provider VPC to route requests from service consumers to the application servers.

**Answer: A**

AWS PrivateLink (a VPC endpoint service) allows a provider to expose an application privately to thousands of consumers across different AWS accounts. Traffic stays entirely on the AWS network — never traversing the public internet — which is ideal for sensitive PHI data. Backed by a Network Load Balancer spanning multiple Availability Zones, it is inherently fault tolerant, and access is granted per consumer. Virtual private gateways (B) are VPN connections that don't scale to thousands of accounts. An internal ALB (C) is not reachable from other accounts. A custom proxy server (D) adds operational overhead and is not inherently fault tolerant.

### Q78: Modernizing a hospital's database write process to lower operational overhead (Select TWO)

A hospital is migrating from another cloud provider to AWS and wants to modernize as they migrate. The hospital has containerized applications that run on tablets. During spikes caused by increases in patient visits, the communications from the applications to the central database occasionally fail. As a result, the hospital currently has the applications try to write to the central database once. If that write fails, the application writes to a dedicated application PostgreSQL database run by the hospital IT team on premises. Each of the PostgreSQL databases then sends batch information to the central database.

The hospital is asking for recommendations on migrating or refactoring the database write process to lower operational overhead.

What should the solutions architect recommend? (Select TWO.)

- A. Migrate the containerized applications to AWS Fargate.
- B. Migrate the local databases to Amazon Aurora Serverless PostgreSQL-Compatible Edition.
- C. Migrate the PostgreSQL databases to an Amazon RDS instance with a read replica that replaces each of the local databases.
- D. Refactor the applications to use an Amazon Simple Queue Service (Amazon SQS) and eliminate the local PostgreSQL databases.
- E. Refactor the central database to add an Amazon ElastiCache lazy loading cache in front of the database.

**Answer: A, D**

Migrating the containerized applications to AWS Fargate (A) provides a serverless container platform with no infrastructure to manage, modernizing the apps and reducing operational overhead. Refactoring to use SQS (D) durably buffers writes during patient-visit spikes — messages are never lost and are processed to the central database at a sustainable rate, which eliminates the need for the on-premises PostgreSQL workaround databases entirely. Migrating the local databases to Aurora Serverless (B) or RDS with read replicas (C) keeps the unnecessary intermediate databases instead of removing them. ElastiCache (E) is a read cache and does nothing to solve failing writes.

### Q79: Preventing lost DynamoDB transactions during flash sales

A company is using Amazon DynamoDB with provisioned throughput for the inventory database tier of its ecommerce website. During flash sales, customers experience periods of time when the database cannot handle the high number of transactions taking place. This causes the company to lose transactions. During normal periods, the database performs appropriately.

Which solution solves the performance problem the company faces?

- A. Switch DynamoDB to on-demand mode during flash sales.
- B. Implement DynamoDB Accelerator (DAX).
- C. Use Amazon Kinesis to queue transactions for processing to DynamoDB.
- D. Use Amazon Simple Queue Service (Amazon SQS) to queue transactions to DynamoDB.

**Answer: D**

Placing an SQS queue in front of DynamoDB decouples the rate of incoming transactions from the database's write capacity. During a flash sale, transactions accumulate durably in the queue and are written to DynamoDB at a sustainable rate, so no transactions are lost. Switching to on-demand mode (A) can still throttle on sudden extreme spikes and is limited to one mode change per 24 hours, making it awkward to toggle for sales. DAX (B) is a read-through cache that doesn't help with write throughput. Kinesis (C) is designed for streaming analytics, adding unnecessary complexity for simple transaction buffering.

### Q80: Secure SSH access to private web servers via a bastion host (Select TWO)

A solutions architect needs to allow developers to have SSH connectivity to web servers. The requirements are as follows:

- Limit access to users originating from the corporate network.
- Web servers cannot have SSH access directly from the internet.
- Web servers reside in a private subnet.

Which combination of steps must the architect complete to meet these requirements? (Select TWO.)

- A. Create a bastion host that authenticates users against the corporate directory.
- B. Create a bastion host with security group rules that only allow traffic from the corporate network.
- C. Attach an IAM role to the bastion host with relevant permissions.
- D. Configure the web servers' security group to allow SSH traffic from a bastion host.
- E. Deny all SSH traffic from the corporate network in the inbound network ACL.

**Answer: B, D**

A bastion host (jump box) in a public subnet with a security group that only allows SSH from the corporate network (B) ensures access originates only from the corporate network and not the open internet. Configuring the private web servers' security group to allow SSH only from the bastion's security group (D) prevents any direct SSH from the internet while permitting developers to reach them through the bastion. Authenticating against the corporate directory (A) is not required by the stated requirements. IAM roles (C) grant AWS API permissions, not SSH access. Denying SSH from the corporate network in a NACL (E) directly contradicts the requirement.

### Q81: Encrypting S3 objects at rest without managing keys, while controlling key access

A company is planning to use Amazon S3 to store images uploaded by its users. The images must be encrypted at rest in Amazon S3. The company does not want to spend time managing and rotating the keys, but it does want to control who can access those keys.

What should a solutions architect use to accomplish this?

- A. Server-Side Encryption with encryption keys stored in an S3 bucket
- B. Server-Side Encryption with Customer-Provided Keys (SSE-C)
- C. Server-Side Encryption with encryption keys stored in AWS Systems Manager Parameter Store
- D. Server-Side Encryption with AWS KMS-Managed Keys (SSE-KMS)

**Answer: D**

SSE-KMS encrypts objects at rest using keys managed by AWS Key Management Service, which handles key storage and automatic rotation — so the company doesn't manage or rotate keys — while KMS key policies and IAM give the company fine-grained control over who can use the keys. SSE-C (B) requires the customer to provide and manage their own keys, the opposite of the requirement. Options A and C describe storing keys in S3 or Parameter Store, which are not valid S3 server-side encryption mechanisms.

### Q82: Internet access for private EC2 instances that is fault tolerant per Availability Zone

An application runs on Amazon EC2 instances in multiple Availability Zones behind an Application Load Balancer. The load balancer is in public subnets. The EC2 instances are in private subnets and must not be accessible from the internet. The EC2 instances must call external services on the internet. Each Availability Zone must be able to call the external services, regardless of the status of the other Availability Zones.

How should these requirements be met?

- A. Create a NAT gateway attached to the VPC. Add a route to the gateway that connects to each private subnet route table.
- B. Configure an internet gateway. Add a route to the gateway that connects to each private subnet route table.
- C. Create a NAT instance in the private subnet of each Availability Zone. Update the route tables for each private subnet to direct internet-bound traffic to the NAT instance.
- D. Create a NAT gateway in each Availability Zone. Update the route tables for each private subnet to direct internet-bound traffic to the NAT gateway.

**Answer: D**

Deploying a NAT gateway in each Availability Zone makes outbound internet access fault tolerant per AZ — if one AZ (or its NAT gateway) fails, the other AZs continue to reach external services independently. NAT gateways allow outbound traffic while keeping the private instances unreachable from the internet. A single NAT gateway (A) is a single point of failure across AZs. An internet gateway in private subnet route tables (B) would expose the instances to inbound internet traffic, violating the requirement. NAT instances (C) belong in public subnets, are self-managed, and are less resilient than NAT gateways.

### Q83: Reducing AWS Lambda cost by adjusting memory allocation (Select TWO)

A development team is deploying a new product on AWS and is using AWS Lambda as part of the deployment. The team allocates 512 MB of memory for one of the Lambda functions. With this memory allocation, the function is completed in 2 minutes. The function runs millions of times monthly, and the development team is concerned about cost. The team conducts tests to see how different Lambda memory allocations affect the cost of the function.

Which steps will reduce the Lambda costs for the product? (Select TWO.)

- A. Increase the memory allocation for this Lambda function to 1,024 MB if this change causes the run time of each function to be less than 1 minute.
- B. Increase the memory allocation for this Lambda function to 1,024 MB if this change causes the run time of each function to be less than 90 seconds.
- C. Reduce the memory allocation for this Lambda function to 256 MB if this change causes the run time of each function to be less than 4 minutes.
- D. Increase the memory allocation for this Lambda function to 2,048 MB if this change causes the run time of each function to be less than 1 minute.
- E. Reduce the memory allocation for this Lambda function to 256 MB if this change causes the run time of each function to be less than 5 minutes.

**Answer: A, C**

Lambda billing is based on GB-seconds (memory × duration). The baseline is 512 MB × 120 s = 61,440 MB-seconds. Option A (1,024 MB × under 60 s) yields under 61,440 MB-seconds — cheaper. Option C (256 MB × under 240 s) also yields under 61,440 MB-seconds — cheaper. Option B (1,024 MB × 90 s = 92,160 MB-seconds) and option D (2,048 MB × 60 s = 122,880 MB-seconds) both cost more. Option E (256 MB × 300 s = 76,800 MB-seconds) also costs more than the baseline.

### Q84: Migrating a desktop relational app to a highly available solution with least disruption

During a review of business applications, a solutions architect identifies a critical application with a relational database that was built by a business user and is running on the user's desktop. To reduce the risk of a business interruption, the solutions architect wants to migrate the application to a highly available, multi-tiered solution in AWS.

What should the solutions architect do to accomplish this with the LEAST amount of disruption to the business?

- A. Create an import package of the application code for upload to AWS Lambda, and include a function to create another Lambda function to migrate data into an Amazon RDS database.
- B. Create an image of the user's desktop and migrate it to Amazon EC2 using VM Import. Place the EC2 instance in an Auto Scaling group.
- C. Pre-stage new Amazon EC2 instances running the application code on AWS behind an Application Load Balancer and an Amazon RDS Multi-AZ DB instance.
- D. Use AWS Database Migration Service (AWS DMS) to migrate the backend database to an Amazon RDS Multi-AZ DB instance. Migrate the application code to AWS Elastic Beanstalk.

**Answer: D**

AWS DMS migrates the relational database to a highly available RDS Multi-AZ instance with minimal downtime, while AWS Elastic Beanstalk hosts the application code in a managed, automatically scalable, multi-tiered environment — together delivering high availability with the least disruption. Lambda (A) would require significant refactoring of a desktop application. Imaging the desktop to a single EC2 instance (B) is just a lift-and-shift that doesn't create a proper multi-tier, highly available architecture and leaves the database embedded in the image. Pre-staging EC2 with hand-deployed code (C) requires manually re-platforming the application, which is more disruptive and error-prone than the managed DMS + Elastic Beanstalk approach.

### Q85: Cost-optimizing an underutilized Aurora cluster used intermittently for reads

A solutions architect finds that an Amazon Aurora cluster with On-Demand Instance pricing is being underutilized for a blog application. The application is used only for a few minutes several times each day for reads.

What should a solutions architect do to optimize utilization MOST cost-effectively?

- A. Turn on Auto Scaling on the original Aurora database.
- B. Refactor the blog application to use Aurora parallel query.
- C. Convert the original Aurora database to an Aurora global database.
- D. Convert the original Aurora database to Amazon Aurora Serverless.

**Answer: D**

Aurora Serverless automatically scales capacity up and down based on demand and pauses when idle, so the company pays only for the capacity consumed during the brief periods of use — ideal for a blog accessed only a few minutes several times a day. Auto Scaling (A) adds read replicas but the always-on base instance continues to incur On-Demand charges during idle periods. Aurora parallel query (B) accelerates analytic queries but doesn't reduce idle cost. An Aurora global database (C) adds cross-Region replication, increasing cost rather than reducing it.

### Q86: Modernizing a self-managed PostgreSQL hospital app to reduce operational overhead (Select TWO)

A hospital is migrating from another cloud provider to AWS and wants to modernize as they migrate. The hospital has containerized applications that run on Amazon EC2 instances. During spikes caused by increases in patient visits, the communications from the applications to the central database increase substantially. As a result, the hospital currently has the applications try to write to the central database one at a time. If that write fails, the application retries on a dedicated self-managed PostgreSQL database for the hospital IT team on premises. Each of the PostgreSQL databases then sends batch information back to the central database.

The hospital is asking for recommendations on migrating or refactoring the database while process to lower operational overhead.

What should the solutions architect recommend? (Select TWO.)

- A. Migrate the containerized applications to AWS Fargate.
- B. Migrate the local databases to Amazon Aurora Serverless PostgreSQL-Compatible Edition.
- C. Migrate the PostgreSQL databases to an Amazon RDS instance with a read replica that replaces each of the local databases.
- D. Refactor the application to use an Amazon Simple Queue Service (Amazon SQS) queue instead of the local PostgreSQL databases.
- E. Refactor the local database to use an Amazon ElastiCache lazy loading cache in front of the database.

**Answer: A, B**

Moving the containers to AWS Fargate removes the need to provision and manage EC2 instances, cutting operational overhead. Aurora Serverless PostgreSQL-Compatible Edition automatically scales database capacity up and down to absorb the spikes during patient-visit increases, so the team no longer manages self-managed PostgreSQL servers. Replacing the local databases with RDS read replicas (C) still requires managing instances and read replicas can't accept writes. Refactoring to SQS (D) changes application behavior but doesn't reduce the database operational burden. An ElastiCache cache (E) speeds reads but the team would still self-manage the underlying PostgreSQL database.

### Q87: Most cost-effective compute for a short, low-memory prediction process

A prediction process requires access to a trained model that is stored in an Amazon S3 bucket. The process takes a few seconds to process an image and make a prediction. The process is not overly resource-intensive, does not require any specialized hardware, and takes less than 512 MB of memory to run.

What is the MOST cost-effective compute solution for this use case?

- A. Amazon Elastic Container Service (Amazon ECS)
- B. Amazon EC2 Spot Instances
- C. AWS Elastic Beanstalk
- D. AWS Lambda functions

**Answer: D**

Lambda runs code without provisioning or managing servers and charges only for the compute time consumed, with no charge when idle — ideal for a short (seconds), low-memory (<512 MB), non-specialized task. ECS (A), Beanstalk (C), and Spot Instances (B) all rely on EC2 instances that incur cost while running and add management overhead, making them more expensive for this lightweight, intermittent workload. Spot Instances are also unsuitable as a service backend because capacity is not guaranteed.

### Q88: Most cost-effective storage for archived audit data retrievable next business day

A company is using an Amazon S3 bucket to store archived data for audits. The company needs long-term storage for the data. The data is rarely accessed and must be available for retrieval the next business day.

After a quarterly review, the company wants to reduce the storage cost for the S3 bucket. A solutions architect must recommend the most cost-effective solution to store the archived data.

Which solution will meet these requirements?

- A. Store the data on an Amazon EC2 instance that uses Amazon Elastic Block Store (Amazon EBS).
- B. Store the data in S3 Glacier Flexible Retrieval.
- C. Use an S3 Lifecycle configuration rule to move the data to S3 Standard-Infrequent Access (S3 Standard-IA).
- D. Store the data in another S3 bucket in a different AWS Region.

**Answer: B**

S3 Glacier Flexible Retrieval is built for long-term archival of rarely accessed data and its standard retrieval (3–5 hours) easily satisfies a next-business-day requirement at a much lower storage cost than the other options. EBS on EC2 (A) is expensive block storage that must stay running. S3 Standard-IA (C) costs more than Glacier for archival data that is essentially never read. Copying to another Region (D) increases cost and addresses replication, not archival savings.

### Q89: Automating EBS snapshot lifecycle with least effort (Select TWO)

After reviewing the cost optimization checks in AWS Trusted Advisor, a team finds that it has 10,000 Amazon Elastic Block Store (Amazon EBS) snapshots in its account that are more than 30 days old. The team has determined that it needs to implement better governance for the lifecycle of its resources.

Which actions should the team take to automate the lifecycle management of the EBS snapshots with the LEAST effort? (Select TWO.)

- A. Create and schedule a backup plan with AWS Backup.
- B. Copy the EBS snapshots to Amazon S3, and then create lifecycle configurations in the S3 bucket.
- C. Use Amazon Data Lifecycle Manager.
- D. Use a scheduled event in Amazon EventBridge and invoke AWS Step Functions to manage the snapshots.
- E. Schedule and run backups in AWS Systems Manager.

**Answer: A, C**

Amazon Data Lifecycle Manager is purpose-built to automate creation, retention, and deletion of EBS snapshots through simple policies. AWS Backup centrally automates and schedules backup/retention lifecycles across services including EBS. Both require minimal effort. Copying snapshots to S3 (B) is manual and S3 lifecycle rules don't act on EBS snapshots. EventBridge + Step Functions (D) and Systems Manager scripting (E) require building and maintaining custom automation — far more effort.

### Q90: Lifecycle policy for images accessed for 30 days then rarely, with millisecond access

A company's application gives users the ability to upload image files to an Amazon S3 bucket. These files are accessed frequently for the first 30 days. After 30 days, these files are rarely accessed. However, the files need to be durably stored and available within milliseconds upon request. A solutions architect needs to configure a lifecycle policy that minimizes the overall cost while meeting the application requirements.

Which solution will meet these requirements?

- A. Configure a lifecycle policy to move the files to the S3 Standard-Infrequent Access (S3 Standard-IA) storage class after 30 days.
- B. Configure a lifecycle policy to move the files to the S3 Glacier Flexible Retrieval storage class after 30 days.
- C. Configure a lifecycle policy to move the files to the S3 Glacier Deep Archive storage class after 30 days.
- D. Configure a lifecycle policy to move the files to the S3 One Zone-Infrequent Access (S3 One Zone-IA) storage class after 30 days.

**Answer: A**

S3 Standard-IA delivers millisecond retrieval and stores data redundantly across multiple Availability Zones at a lower cost than S3 Standard — perfect for infrequently accessed files that still must be returned within milliseconds. Glacier Flexible Retrieval (B) and Glacier Deep Archive (C) require minutes to hours for retrieval, violating the millisecond requirement. S3 One Zone-IA (D) gives millisecond access but stores data in a single AZ, reducing durability against AZ loss.

### Q91: Charging departments for a shared analytics environment with least effort

A company is deploying an environment for a new data processing application. The application will be frequently accessed by 20 different departments across the globe seeking to run analytics. The company plans to charge each department for the cost of that department's access.

Which solution will meet these requirements with the LEAST effort?

- A. Amazon Aurora with global databases. Each department will query a database in a different Region, and the Region is tagged in the billing console.
- B. Amazon RDS for PostgreSQL, with read replicas for each department. Each department will query the read replica tagged for their department in the billing console.
- C. Amazon Redshift, with clusters set up for each department. Each department will query the cluster tagged for their department in the billing console.
- D. Amazon Athena with workgroups set up for each department. Each department will query through the workgroup tagged for their department in the billing console.

**Answer: D**

Amazon Athena lets you query data directly in Amazon S3, and workgroups are purpose-built to separate users/teams and track and allocate query costs per group with minimal setup. Aurora global databases (A) and RDS read replicas (B) are built for transactional workloads, not analytics, and spinning up per-department copies adds cost and effort. Redshift clusters per department (C) duplicate infrastructure and add significant cost and management overhead.

### Q92: Removing EC2 Spot Instances after a demonstration

A company used Amazon EC2 Spot Instances for a demonstration that is now complete. A solutions architect must remove the Spot Instances to stop them from incurring cost.

What should the solutions architect do to meet this requirement?

- A. Cancel the Spot request. Terminate the Spot Instances.
- B. Cancel the Spot request only.
- C. Terminate the Spot Instances only.
- D. Terminate the Spot Instances. Cancel the Spot request.

**Answer: A**

To stop Spot Instances from incurring cost you must first cancel the Spot request so it cannot launch replacements, and then terminate the running Spot Instances. Canceling the request only (B) leaves running instances incurring cost. Terminating instances only (C), or terminating before canceling (D), can cause the still-active request to launch new instances to maintain target capacity. The correct order is cancel the request, then terminate the instances.

### Q93: Database tier requiring full OS control and high availability

A financial services company is migrating its multi-tier web application to AWS. The application architecture consists of a fleet of web servers, application servers, and an Oracle database. The company must have full control over the database's underlying operating system. The database must be highly available.

Which approach should a solutions architect use for the database tier to meet these requirements?

- A. Migrate the database to an Amazon RDS for Oracle DB Single-AZ DB instance.
- B. Migrate the database to an Amazon RDS for Oracle Multi-AZ DB instance.
- C. Migrate to Amazon EC2 instances in two Availability Zones. Install Oracle and configure the instances to operate as a cluster.
- D. Migrate to Amazon EC2 instances in a single Availability Zone. Install Oracle and configure the instances to operate as a cluster.

**Answer: C**

Full control over the underlying operating system rules out Amazon RDS, which is a managed service that does not grant OS access — eliminating options A and B. Running Oracle on self-managed EC2 instances gives the company OS-level control, and deploying across two Availability Zones in a cluster provides high availability. A single-AZ deployment (D) cannot survive an Availability Zone failure, so it does not meet the high-availability requirement.

### Q94: DR strategy with seconds RPO, minutes RTO, and a scaled-down running environment

A solutions architect must create a disaster recovery (DR) solution for a company's business-critical applications. The DR site must reside in a different AWS Region than the primary site. The solution requires a recovery point objective (RPO) in seconds and a recovery time objective (RTO) in minutes. The solution also requires the deployment of a completely functional but scaled-down version of the applications.

Which DR strategy will meet these requirements?

- A. Multi-site active-active
- B. Backup and restore
- C. Pilot light
- D. Warm standby

**Answer: D**

Warm standby keeps a fully functional but scaled-down copy of the environment always running in the DR Region, enabling an RPO in seconds and an RTO in minutes by simply scaling up when failover occurs. Multi-site active-active (A) also meets the RTO/RPO but runs a full-scale environment, which is more than "scaled-down" and the most expensive. Backup and restore (B) has RTO/RPO in hours. Pilot light (C) keeps only core services running (not a "completely functional" version), so bringing up the full application takes longer.

### Q95: Highly available relational database when OS-level permission is required

A company requires operating system permission on a relational database server.

What should a solutions architect suggest as a configuration for a highly available database architecture?

- A. Multiple Amazon EC2 instances in a database replication configuration that uses two Availability Zones
- B. A database installed on a single Amazon EC2 instance in an Availability Zone
- C. Amazon RDS in a Multi-AZ configuration with Provisioned IOPS
- D. Multiple Amazon EC2 instances in a replication configuration that uses a placement group

**Answer: A**

The requirement for operating system permission rules out Amazon RDS, which is fully managed and does not grant OS access — eliminating C. High availability requires spanning two Availability Zones, so a single instance (B) cannot survive an AZ failure. A placement group (D) clusters instances within a single AZ to reduce latency, which actually reduces availability. Running the database on EC2 instances replicated across two Availability Zones gives both OS control and high availability.

### Q96: Highly available MySQL database that maximizes ease of management

A company is deploying a production portal application on AWS. The database tier runs on a MySQL database. The company requires a highly available database solution that maximizes ease of management.

How can the company meet these requirements?

- A. Deploy the database on multiple Amazon EC2 instances that are backed by Amazon Elastic Block Store (Amazon EBS) across multiple Availability Zones. Schedule periodic EBS snapshots.
- B. Use Amazon RDS with a Single-AZ deployment. Schedule periodic database snapshots.
- C. Use Amazon RDS with a Multi-AZ deployment. Schedule periodic database snapshots.
- D. Use Amazon DynamoDB with a DynamoDB Accelerator (DAX) cluster. Create periodic on-demand backups.

**Answer: C**

Amazon RDS is a managed service that maximizes ease of management, and a Multi-AZ deployment provides high availability by automatically failing over to a standby in another Availability Zone. Self-managing MySQL on EC2 (A) maximizes operational effort, the opposite of the requirement. RDS Single-AZ (B) is easy to manage but not highly available. DynamoDB (D) is a NoSQL database and is not compatible with the existing MySQL (relational) workload.

### Q97: Resilient, highly scalable architecture for a fast-growing gaming application

A gaming company is experiencing exponential growth. On multiple occasions, customers have been unable to access resources. To keep up with the increased demand, management is considering deploying a cloud-based solution. The company wants a solution that can match the on-premises resilience of multiple data centers and is robust enough to withstand the increased growth in usage.

Which configuration should a solutions architect implement to meet these requirements?

- A. A VPC configured with an ELB Network Load Balancer that targets an Amazon EC2 Auto Scaling group consisting of Amazon EC2 instances that span two Availability Zones.
- B. A VPC configured with an ELB Application Load Balancer that targets an Amazon EC2 Auto Scaling group consisting of Amazon EC2 instances across two Availability Zones.
- C. Multiple Amazon EC2 instances that are configured within peered VPCs across two Availability Zones.
- D. A VPC configured with an ELB Application Load Balancer that targets an Amazon EC2 Auto Scaling group consisting of Amazon EC2 instances that span two AWS Regions.

**Answer: B**

An Application Load Balancer fronting an EC2 Auto Scaling group across two Availability Zones provides both resilience (multiple AZs mirror multiple on-premises data centers) and elastic scaling to absorb growth. A Network Load Balancer (A) is for extreme TCP/UDP performance and is less suited to this web-facing application. Peered VPCs (C) add complexity without an autoscaling/load-balancing front end. An ALB cannot span two AWS Regions (D) — load balancers are Regional — so that option is not valid.

### Q98: Decoupling service that processes requests in the order received

A company is migrating its on-premises application to AWS and refactoring the application's design. The design will consist of frontend Amazon EC2 instances that receive requests, backend EC2 instances that process the requests, and a message-queuing service that addresses decoupling the application. A solutions architect has been informed that a key aspect of the application is that requests are processed in the order in which they are received.

Which AWS service should the solutions architect use to decouple the application?

- A. Amazon Simple Queue Service (Amazon SQS) FIFO queue
- B. Amazon Simple Queue Service (Amazon SQS) standard queue
- C. Amazon Simple Notification Service (Amazon SNS)
- D. Amazon Kinesis

**Answer: A**

An SQS FIFO (first-in-first-out) queue guarantees that messages are processed exactly once and strictly in the order they are received, which is the key requirement. An SQS standard queue (B) offers best-effort ordering only. SNS (C) is a pub/sub notification service, not an ordered work queue. Kinesis (D) maintains order within a shard but is built for streaming/analytics, not simple request decoupling between application tiers.

### Q99: Cost-effective, highly available data store protected from accidental deletion

A solutions architect must create a data store location that will be able to handle different file formats of unknown sizes. The data must be highly available and protected from being accidentally deleted.

What solution meets the requirements and is the MOST cost-effective?

- A. Deploy an Amazon S3 bucket and activate Cross-Region Replication.
- B. Deploy an Amazon DynamoDB table and activate global tables.
- C. Deploy a database by using Amazon RDS and configure a Multi-AZ deployment for that database.
- D. Deploy an Amazon S3 bucket and enable object versioning.

**Answer: D**

Amazon S3 is object storage ideal for arbitrary file formats of any size and is highly durable and available across multiple Availability Zones by default. Enabling object versioning protects against accidental deletion or overwrite at minimal extra cost. Cross-Region Replication (A) adds cost mainly for regional resilience, which isn't required here. DynamoDB (B) and RDS (C) are structured databases unsuited to storing arbitrary files and cost more for this use case.

### Q100: Centrally automating SSL/TLS certificate deployment and management

A SysOps administrator wants to automate the deployment of new SSL/TLS certificates to web servers. The administrator wants a centralized way to keep track of and manage the deployed certificates.

Which AWS service can the administrator use to meet these requirements?

- A. AWS Key Management Service (AWS KMS)
- B. Run Command, a capability of AWS Systems Manager
- C. AWS Certificate Manager (ACM)
- D. Parameter Store, a capability of AWS Systems Manager

**Answer: C**

AWS Certificate Manager provisions, manages, deploys, and automatically renews public and private SSL/TLS certificates from a central place, removing the manual work of purchasing, uploading, and renewing them. KMS (A) manages encryption keys, not SSL/TLS certificates. Run Command (B) can push a one-time deployment but does not provide ongoing certificate management. Parameter Store (D) stores configuration data and secrets but cannot manage SSL/TLS certificates.

### Q101: Identifying the destination of malicious network traffic from an EC2 instance

A solutions architect notices an abnormal amount of network traffic coming from an Amazon EC2 instance. The traffic is determined to be malicious, and the destination needs to be determined.

What tool can the solutions architect use to identify the destination of the malicious network traffic?

- A. Turn on AWS CloudTrail and filter the logs.
- B. Turn on VPC Flow Logs and filter the logs.
- C. Consult the AWS Health Dashboard.
- D. Filter the logs from Amazon CloudWatch.

**Answer: B**

VPC Flow Logs capture information about the IP traffic going to and from network interfaces in a VPC, including source and destination IP addresses and ports — exactly what is needed to identify where the malicious traffic is going. CloudTrail (A) records API calls, not network packet flows. The AWS Health Dashboard (C) reports AWS service health. CloudWatch (D) collects metrics and application logs but does not, by itself, record network traffic destinations.

### Q102: Securing a three-tier VPC architecture with HTTPS-only web access (Select TWO)

A solutions architect needs to design a secure environment for AWS resources that are being deployed to Amazon EC2 instances in a VPC. The solution should support a three-tier architecture that consists of web servers, application servers, and a database cluster. The VPC needs to allow resources in the web tier to be accessible from the internet over only the HTTPS protocol.

Which combination of actions would meet these requirements? (Select TWO.)

- A. Attach an Amazon API Gateway to the VPC. Create private subnets for the web, application, and database tiers.
- B. Attach an internet gateway to the VPC. Create public subnets for the web tier. Create private subnets for the application and database tiers.
- C. Attach a virtual private gateway to the VPC. Create public subnets for the web and application tiers. Create private subnets for the database tier.
- D. Create a web server security group that allows all traffic from the internet. Create an application server security group that allows requests from only the Amazon API Gateway on the application port. Create a database cluster security group that allows requests from the application security group on the database port only.
- E. Create a web server security group that allows HTTPS requests from the internet. Create an application server security group that allows requests from the web security group only. Create a database cluster security group that allows requests from the application security group on the database port only.

**Answer: B, E**

An internet gateway with public subnets for the web tier and private subnets for the application and database tiers correctly isolates the back-end tiers from direct internet access. Security groups that allow only HTTPS from the internet to the web tier, only web-tier traffic to the application tier, and only application-tier traffic to the database enforce least-privilege flow between tiers. Option D allows all traffic to the web tier (not HTTPS-only). A virtual private gateway (C) is for VPN connectivity, not internet access, and placing the application tier in public subnets reduces security. API Gateway (A) does not provide the EC2 three-tier network isolation described.

### Q103: Minimizing data transfer cost for a global static website on S3

A company will host a static website within an Amazon S3 bucket. The website will serve millions of users globally, and the company wants to minimize data transfer costs.

What should a solutions architect do to ensure costs are kept to a minimum?

- A. Implement an AWS Auto Scaling group for the website to ensure it grows with use.
- B. Use Cross-Region Replication to copy the website to an additional S3 bucket in a different AWS Region.
- C. Move the website to large compute-optimized Amazon EC2 instances with on-demand pricing.
- D. Create an Amazon CloudFront distribution, with the S3 bucket as the origin server.

**Answer: D**

Amazon CloudFront caches the static content at edge locations close to users worldwide, reducing the volume of data served directly from S3 and lowering data transfer costs while improving performance. Auto Scaling (A) and EC2 instances (C) don't apply to static content served from S3 and add cost. Cross-Region Replication (B) duplicates storage and adds replication cost without reducing data transfer for global users.

### Q104: Cost-effective secure login to EC2 instances in a private subnet

A company is developing an application that runs on Amazon EC2 instances in a private subnet. The EC2 instances use a NAT gateway to access the internet. A solutions architect must provide a secure option so that developers can log in to the instances.

Which solution meets these requirements MOST cost-effectively?

- A. Configure AWS Systems Manager Session Manager for the EC2 instances to activate login.
- B. Configure a bastion host in a public subnet to log in to the EC2 instances in a private subnet.
- C. Use the existing NAT gateway to log in to the EC2 instances in a private subnet.
- D. Configure AWS Site-to-Site VPN to log in directly to the EC2 instances.

**Answer: A**

AWS Systems Manager Session Manager provides secure, auditable shell access to private EC2 instances without opening inbound ports or running extra infrastructure, and there is no additional charge for the service — making it the most cost-effective and secure option. A bastion host (B) requires a continuously running EC2 instance and incurs cost. A NAT gateway (C) only enables outbound internet access and cannot be used for inbound login. Site-to-Site VPN (D) is for connecting networks and adds cost and complexity not warranted here.

### Q105: Cost-effective microservices with no infrastructure management, scaling, and DDoS protection

A startup company is looking for a solution to cost-effectively run and access microservices without the operational overhead of managing infrastructure. The solution needs to be able to scale quickly to accommodate rapid changes in the volume of requests and protect against common DDoS attacks.

What is the MOST cost-effective solution that meets these requirements?

- A. Run the microservices in containers using AWS Elastic Beanstalk.
- B. Run the microservices in AWS Lambda behind an Amazon API Gateway.
- C. Run the microservices on Amazon EC2 instances in an Auto Scaling group.
- D. Run the microservices in containers using Amazon Elastic Container Service (Amazon ECS) backed by EC2 instances.

**Answer: B**

AWS Lambda removes infrastructure management entirely and scales automatically with request volume, while you pay only for execution time. Fronting it with Amazon API Gateway provides a managed entry point that integrates with AWS Shield Standard for common DDoS protection. Elastic Beanstalk (A), EC2 Auto Scaling (C), and ECS on EC2 (D) all rely on EC2 instances that incur cost while idle and require more operational management, making them less cost-effective for spiky microservice traffic.

### Q106: Shared file storage for Windows servers integrated with Active Directory

A data processing facility wants to move a group of Microsoft Windows servers to the AWS Cloud. These servers require access to a shared file system that can integrate with the facility's existing Active Directory (AD) infrastructure for file and folder permissions. The solution needs to provide seamless support for shared files with AD users and groups located on premises. The solution must be highly available. The chosen solution should provide added security by supporting encryption at rest and in transit. Which storage solution would meet these requirements?

- A. An AWS S3 File Gateway joined to the existing AD domain.
- B. An Amazon FSx for Windows File Server file system joined to the existing AD domain.
- C. An Amazon Elastic File System (Amazon EFS) file system joined to an AWS managed AD domain.
- D. An Amazon S3 bucket mounted on Amazon EC2 instances in multiple Availability Zones running a Windows server.

**Answer: B**

Amazon FSx for Windows File Server provides a fully managed native Windows SMB file system that integrates directly with Active Directory for file and folder permissions, supports Multi-AZ high availability, and offers encryption at rest and in transit. S3 File Gateway (A) presents S3 as a file share but is not a native Windows file system with AD-based NTFS permissions. EFS (C) supports only Linux/NFS, not Windows SMB. Mounting an S3 bucket on EC2 (D) is not a native shared Windows file system and lacks AD-integrated file permissions.

### Q107: API Gateway features for privacy, scale, and external customer security (Select THREE)

A new application is going to be reviewed by the architecture board. The main concerns about required API implementation are as follows:

- Privacy of internal information
- Millions of concurrent calls
- Security for external customers

Which of the following Amazon API Gateway features alleviate these concerns? (Select THREE.)

- A. Private endpoint
- B. Distributed denial of service (DDoS) protection and throttling
- C. AWS Lambda
- D. Network Address Translation (NAT)
- E. API Gateway cache
- F. Edge optimized

**Answer: A, B, F**

A private endpoint keeps internal APIs reachable only from within the VPC, protecting the privacy of internal information. DDoS protection and throttling let API Gateway absorb and rate-limit millions of concurrent calls. An edge-optimized endpoint routes requests through the Amazon CloudFront network, improving security and latency for geographically distributed external customers. AWS Lambda (C) is a compute backend, not an API Gateway feature. NAT (D) is a networking construct, not an API Gateway feature. API Gateway cache (E) improves performance/cost but doesn't directly address these three stated concerns.

### Q108: Fastest way to migrate large data given limited VPN bandwidth and a 3-week deadline

You need to move a large amount of data from your data center to AWS in the next 3 weeks. You have a virtual private network (VPN) currently set up that provides 100 Mbps of bandwidth. Current saturation on the VPN is 75 percent, making it an untenable solution for your transfer. Which AWS service would be the FASTEST option for migrating the data within the 3-week timeframe?

- A. AWS Snow Family products
- B. AWS Storage Gateway
- C. AWS WAF
- D. Amazon CloudFront

**Answer: A**

With the VPN already 75 percent saturated, transferring a large data set over the network within three weeks is impractical. AWS Snow Family devices let you physically ship the data to AWS, making them the fastest option under limited bandwidth. Storage Gateway (B) still relies on the constrained network link. AWS WAF (C) is a web application firewall and CloudFront (D) is a content delivery network — neither is a bulk data-migration service.

### Q109: Route 53 routing policy to split traffic 80/20 across two Regions

A network engineer wants to route 80 percent of web traffic to the ap-southeast-2 Region. The remaining 20 percent of traffic will be directed to the eu-west-1 Region. Which Route 53 routing policy is the best choice for this use case?

- A. Geoproximity routing
- B. Weighted routing
- C. Geolocation routing
- D. Simple routing
- E. Multivalue answer routing

**Answer: B**

Weighted routing lets you assign relative weights to records so traffic is distributed by percentage — for example, 80 percent to one Region and 20 percent to another. Geoproximity (A) and geolocation (C) route based on the user's physical or geographic location, not fixed percentages. Simple routing (D) returns a single record with no traffic splitting. Multivalue answer routing (E) returns multiple healthy records at random but does not enforce specific percentage splits.

### Q110: Best service for system metrics, dashboards, and alert notifications

You are migrating from an on-premises data center to AWS. You need a monitoring system with system metrics such as CPU usage, access logs, dashboards, and alert notifications. In addition, you need to monitor AWS account-specific failures and notifications. Which service is BEST for this use case?

- A. Amazon CloudWatch
- B. AWS CloudTrail
- C. AWS Health
- D. AWS Health Dashboard

**Answer: A**

Amazon CloudWatch collects system metrics (such as CPU usage), ingests logs, builds dashboards, and sends alert notifications through alarms — covering the core monitoring requirements. It can also receive AWS account events to surface failures and notifications. CloudTrail (B) records API activity for auditing, not metrics/dashboards. AWS Health (C) and the AWS Health Dashboard (D) report on service/account health events only and don't provide system metrics or custom dashboards.

### Q111: First resource to review when an uploaded image isn't processed in a serverless lab

A student is running the Build Serverless Architecture lab and is testing their architecture with a .jpeg file. Reviewing the web folder on the Amazon Simple Storage Service (Amazon S3) bucket, the student notices there is no .jpeg file. Which AWS resource should the student review first to solve this?

- A. AWS Lambda Web Function
- B. Amazon Simple Notification Service (Amazon SNS) topic
- C. AWS Lambda Mobile Function
- D. Amazon Simple Queue Service (Amazon SQS) queue

**Answer: A**

In this serverless image-processing architecture, the Lambda Web Function is responsible for processing the uploaded image and writing the result (the .jpeg) into the web folder. If the file is missing, the Lambda Web Function is the first place to investigate for errors. SNS (B) and SQS (D) handle messaging/decoupling, not the file transformation, and the Lambda Mobile Function (C) is part of a different path in the lab.

### Q112: Efficiently restricting services across hundreds of accounts in many departments

Your organization has hundreds of accounts across many departments and needs to apply a restriction on specific services. Which of the following is an efficient way to implement the administration requirements?

- A. Assign AWS accounts to individual Organizational Units (OUs) in AWS Organizations and apply an Identity and Access Management (IAM) Policy to each OU.
- B. Assign the AWS accounts to Organizational Units (OUs) based on their departments in AWS Organizations and apply a service control policy at the OU level.
- C. Assign AWS accounts to Organizational Units (OUs) based on their departments in AWS Organizations and apply an Identity and Access Management (IAM) Policy at the root.
- D. Assign AWS accounts to individual Organizational Units (OUs) in AWS Organizations and apply a service control policy to each OU.

**Answer: B**

Grouping accounts into OUs by department and attaching a service control policy (SCP) at the OU level lets you restrict specific services for whole groups of accounts at once — the most efficient approach for hundreds of accounts. SCPs (not IAM policies) are the AWS Organizations mechanism for restricting services across accounts, which eliminates A and C. Applying SCPs to individual OUs per account (D) defeats the efficiency of grouping by department.

### Q113: Required VPC components for a public web tier and a private database tier (Select TWO)

You are a Solutions Architect designing the architecture of the virtual private clouds (VPCs) for a web application using Amazon Elastic Compute Cloud (Amazon EC2) instances. The application consists of a web tier and a database tier. The web tier needs to be accessible to internet based clients; however, the database tier needs to be accessible to the web tier instances only. Which of the following are required to make this solution work and follow best practices? (Select TWO.)

- A. Create an internet gateway and attach it to the VPC.
- B. Create a Security Group for the web tier and a Security Group for the database tier.
- C. Create a public and private subnet for the web tier and another public and private subnet for the database tier.
- D. Create a NAT gateway and attach it to a public subnet.
- E. Create one VPC for the web tier and one VPC for the database tier.

**Answer: A, B**

An internet gateway attached to the VPC is required so internet-based clients can reach the public web tier. Separate security groups for the web and database tiers let you allow internet (HTTPS) traffic to the web tier while restricting the database tier to accept traffic only from the web tier's security group. A NAT gateway (D) provides outbound internet access for private instances, which isn't the requirement here. Splitting tiers across separate VPCs (E) adds unnecessary complexity, and the subnet layout in C is not the minimal required pair compared with the gateway and security groups.

### Q114: Best services to ingest and process clickstream data for a recommendation engine (Select TWO)

An ecommerce website wants to process clickstream from their visitors and needs to store stream data while processing through a recommendation engine. What are the BEST AWS services for this use case? (Select TWO.)

- A. AWS Auto Scaling
- B. Amazon Kinesis Data Streams
- C. Amazon Kinesis Data Analytics
- D. AWS Elastic Beanstalk

**Answer: B, C**

Amazon Kinesis Data Streams ingests and durably stores high-volume, real-time clickstream data. Amazon Kinesis Data Analytics processes that streaming data in real time so it can feed a recommendation engine. AWS Auto Scaling (A) adjusts compute capacity but doesn't ingest or process streams. Elastic Beanstalk (D) deploys and runs applications but is not a streaming data ingestion/processing service.

### Q115: Factors to consider when picking an AWS Region (Select TWO)

What are the factors to consider when picking an AWS Region? (Select TWO.)

- A. Latency to end users
- B. Local data regulations
- C. Operating system requirements
- D. Support for hybrid networking
- E. Programming language of your application

**Answer: A, B**

Latency to end users drives Region choice because placing workloads closer to users reduces round-trip time. Local data regulations (data sovereignty/compliance) can legally require data to stay within a specific country or jurisdiction, dictating which Region you must use. Operating system requirements (C), hybrid networking support (D), and the application's programming language (E) are available across all Regions and are not differentiating factors when selecting one. (Service availability and pricing are also real factors but are not among the listed options.)

### Q116: Scalable connectivity for hundreds of VPCs with an uncertain future count

Your organization has grown very quickly and has applications deployed using hundreds of isolated virtual private clouds (VPCs). Now there is a need for connectivity between many VPCs. The company is uncertain how many future VPCs will be connected and is concerned about scalability of the chosen solution. As the Solutions Architect, which solution would you advise for them?

- A. AWS Site-to-Site VPN
- B. AWS Direct Connect
- C. VPC peering connections
- D. AWS Transit Gateway

**Answer: D**

AWS Transit Gateway acts as a central hub that connects thousands of VPCs (and on-premises networks) through a single gateway, scaling cleanly as new VPCs are added and supporting transitive routing. VPC peering (C) is non-transitive and requires a full mesh — the number of connections grows quadratically, which does not scale to hundreds of VPCs. Site-to-Site VPN (A) and Direct Connect (B) are for connecting on-premises networks to AWS, not for scalable VPC-to-VPC connectivity.

### Q117: Benefits of using the AWS CDK with AWS CloudFormation (Select TWO)

Which of the following are benefits of using AWS Cloud Development Kit (AWS CDK) with AWS CloudFormation? (Select TWO.)

- A. Components are limited to a single user.
- B. Developers can use common programming languages.
- C. Bulk discounts are automatically applied to resource usage.
- D. Using AWS Cloud Development Kit (AWS CDK) does not require an AWS account or credentials.
- E. Developers can call preconfigured resources with proven defaults.

**Answer: B, E**

The AWS CDK lets developers define infrastructure using familiar programming languages (TypeScript, Python, Java, C#, Go) instead of writing raw CloudFormation templates. It also provides constructs — preconfigured, reusable components with sensible defaults and AWS best practices baked in. CDK components can be shared, not limited to a single user (A). CDK does not change pricing or apply bulk discounts (C). Like any tool that deploys to AWS, it still requires an AWS account and credentials (D).

### Q118: Recommended service to deploy containers with limited operations knowledge

For customers that want to deploy containers on AWS, but do not have enough operations or deployment strategy knowledge, which service should you recommend?

- A. Amazon Elastic Compute Cloud (Amazon EC2)
- B. AWS Fargate
- C. Amazon Elastic Kubernetes Service (Amazon EKS)
- D. Amazon Elastic Container Service (Amazon ECS)

**Answer: B**

AWS Fargate is a serverless compute engine for containers that removes the need to provision, patch, and manage the underlying servers or capacity — ideal for teams without deep operations or deployment expertise. Running containers on EC2 (A) requires managing the instances yourself. EKS (C) and ECS (D) are orchestration services, but with the EC2 launch type you still manage the cluster infrastructure; Fargate abstracts that away entirely.

### Q119: Consolidating backups across EC2, EFS, and RDS into one place

You have decided to consolidate backup in your organization. You currently have Amazon Elastic Compute Cloud (Amazon EC2), Amazon Elastic File System (Amazon EFS), and Amazon Relational Database Service (Amazon RDS) services running in your environment. What AWS service can you use to consolidate all backups to one place?

- A. AWS Backup
- B. AWS Elastic Beanstalk
- C. AWS S3
- D. AWS Secrets Manager

**Answer: A**

AWS Backup is a fully managed service that centralizes and automates backups across AWS services — including EC2, EFS, and RDS — from a single console with unified backup policies. Elastic Beanstalk (B) deploys and runs applications, not backups. S3 (C) is object storage and would require building custom backup logic per service. Secrets Manager (D) stores and rotates secrets, not backups.

### Q120: Disaster recovery strategy that keeps only part of the stack running

While working on your disaster recovery plan (DR) you have decided to create a backup stack on AWS. Unfortunately, you need to keep some of the stack running but do not have the budget to keep the entire stack running. What type of recovery strategy is this?

- A. Backup and restore
- B. Multi-site active/active
- C. Pilot light
- D. AWS Snowball Edge Compute Optimized

**Answer: C**

Pilot light keeps the core, critical components of the stack running (such as the database) while the rest stays switched off until needed — matching the requirement to keep only part of the stack running on a limited budget. Backup and restore (A) keeps nothing running and rebuilds from backups (highest RTO). Multi-site active/active (B) keeps the entire stack running in parallel (highest cost). Snowball Edge Compute Optimized (D) is a data-transfer/edge-compute device, not a DR strategy.

### Q121: Compute purchase option with a commitment that is flexible across instance families

Which compute purchase option includes a 1-year or 3-year commitment and is also flexible to use across Amazon Elastic Compute Cloud (Amazon EC2) instance families?

- A. EC2 Instance Savings Plans
- B. On-Demand Instances
- C. EC2 Spot Instances
- D. Compute Savings Plans

**Answer: D**

Compute Savings Plans offer a 1-year or 3-year commitment and the most flexibility — discounts apply automatically across any instance family, size, Region, OS, and tenancy, and even to Fargate and Lambda. EC2 Instance Savings Plans (A) also require a commitment but are locked to a specific instance family in a chosen Region. On-Demand (B) has no commitment and no discount. Spot Instances (C) have no commitment and can be interrupted by AWS.

### Q122: Shared file system for two Linux applications in different Availability Zones

You have two Linux applications in different Availability Zones that must share a common file system. Which of the following is the best solution for this use case?

- A. AWS Storage Gateway
- B. Amazon FSx for Windows File Server
- C. Amazon Elastic File System (Amazon EFS)
- D. Amazon Simple Storage Service (Amazon S3)

**Answer: C**

Amazon EFS is a fully managed NFS file system for Linux that can be mounted concurrently by instances across multiple Availability Zones, making it ideal for sharing a common file system between the two applications. FSx for Windows File Server (B) serves Windows/SMB workloads, not Linux. S3 (D) is object storage and cannot be natively mounted as a shared file system. Storage Gateway (A) is for hybrid on-premises-to-AWS storage integration, not multi-AZ shared file access within AWS.

### Q123: Cost-effective scaling for a video processing workload

A company has developed an application that processes photos and videos. When users upload files, a job processes them. The job can take up to 1 hour to process a long video. The company uses Amazon EC2 On-Demand instances to serve web servers and processing jobs. The processing layer must be able to scale, and during peak hours the systems are at full capacity. What should a solutions architect do so that the application will process all the jobs in the MOST cost-effective manner?

- A. Use a larger instance size in the Auto Scaling groups of the web layer and the processing layer.
- B. Use Spot Instances for the Auto Scaling groups of the web layer and the processing layer.
- C. Use an Amazon Simple Queue Service (Amazon SQS) standard queue between the web layer and the processing layer. Use a custom queue metric to scale the Auto Scaling group in the processing layer.
- D. Use AWS Lambda functions instead of EC2 instances and Auto Scaling groups. Increase the service quota so that sufficient concurrent functions can run at the same time.

**Answer: C**

An SQS queue decouples the web layer from the processing layer so that jobs are buffered and never lost during peak demand. Scaling the processing Auto Scaling group on a custom queue-depth metric (such as the number of messages waiting) adds capacity only when there is a backlog and removes it when the queue drains, which is the most cost-effective fit. A larger instance size (A) raises cost continuously without addressing the bursty pattern. Spot Instances (B) can be reclaimed mid-job, which is risky for tasks that take up to an hour. Lambda (D) has a 15-minute execution limit, so it cannot run a 1-hour job.

### Q124: Combining solutions to ingest spiky IoT sensor data into a data lake (Select TWO)

A company is building a distributed application that sends sensor IoT data — including weather conditions and wind speed from wind turbines — to the AWS Cloud for further processing. The data is spiky and the application must be able to scale. The streaming data must be stored in a key-value database, then sent to a centralized data lake where it can be transformed, analyzed, and combined with diverse datasets to derive insights. Which combination of solutions accomplishes this with the LEAST operational overhead? (Select TWO.)

- A. Configure Amazon Kinesis to deliver streaming data to an Amazon S3 data lake.
- B. Use Amazon DocumentDB to store the IoT sensor data.
- C. Write AWS Lambda functions to deliver streaming data to Amazon S3.
- D. Use Amazon DynamoDB to store the IoT sensor data and turn on Kinesis Data Streams.
- E. Use Amazon Kinesis to deliver streaming data to Amazon Redshift and turn on Amazon Redshift Spectrum.

**Answer: A, D**

DynamoDB is a fully managed, serverless key-value database that scales seamlessly with spiky traffic, and DynamoDB Streams/Kinesis Data Streams capture changes for downstream processing (D). Amazon Kinesis Data Firehose can then deliver the streaming data directly into an S3 data lake with no servers to manage (A). Writing custom Lambda functions (C) adds operational overhead compared with Kinesis's managed delivery. DocumentDB (B) is a document database, not the required key-value store, and isn't serverless. Redshift with Spectrum (E) is a data warehouse, not the S3 data lake described, and adds cluster management overhead.

### Q125: Member accounts accessing a shared services VPC with the least operational overhead

A large international company has a management account in AWS Organizations and hundreds of VPCs across member accounts. A shared services VPC hosts an application that all the member accounts must reach. Communication among all the VPCs should be allowed. How can the member accounts access the shared services VPC with the LEAST operational overhead?

- A. Create an Application Load Balancer with a target of the private IP address of the shared services VPC. Add a Certificate Authority Authorization (CAA) record for the ALB to Amazon Route 53. Point all requests for shared services in the VPC routing tables to that record.
- B. Create a peering connection between each of the VPCs and the shared services VPC.
- C. Create a Network Load Balancer across the Availability Zones in the shared services VPC. Create service consumer roles in IAM, and set endpoint connection acceptance to automatically accept. Create consumer endpoints in each division VPC and point to the Network Load Balancer.
- D. Create a VPN connection between each of the VPCs and the shared services VPC.

**Answer: C**

A VPC endpoint service (AWS PrivateLink) backed by a Network Load Balancer lets the shared services VPC expose its application privately to hundreds of consumer VPCs across accounts, with auto-accepted endpoint connections to minimize manual approvals. This scales cleanly and keeps traffic on the AWS network. VPC peering (B) is non-transitive and requires a full mesh that grows quadratically — unmanageable for hundreds of VPCs. A VPN connection per VPC (D) has the same scaling and operational problem. The ALB/CAA approach (A) is not how cross-VPC private connectivity works (CAA records relate to certificate issuance, not routing).

### Q126: Choosing connectivity for consistent bandwidth in a hybrid cloud

Your organization is beginning a journey to the cloud and initially will implement a hybrid cloud infrastructure. The primary concern is consistency of bandwidth between the on-premises resources and AWS resources. Which option would you recommend for connectivity?

- A. Virtual Private Cloud (VPC) Peering Connections
- B. AWS Transit Gateway
- C. AWS Direct Connect
- D. AWS Site-to-Site VPN

**Answer: C**

AWS Direct Connect provides a dedicated, private physical connection between on-premises and AWS, delivering consistent, predictable bandwidth and latency — exactly the concern stated. A Site-to-Site VPN (D) runs over the public internet, so bandwidth and latency vary. VPC Peering (A) connects VPCs to each other, not on-premises to AWS. Transit Gateway (B) is a routing hub for many VPCs and VPNs; it does not by itself provide the dedicated, consistent bandwidth link to on-premises.

### Q127: Disaster recovery model with an RTO in minutes at the lowest cost

Which disaster recovery model offers an RTO in minutes at the lowest cost?

- A. Fully working low-capacity standby
- B. Pilot light
- C. Backup and restore
- D. Multi-site active/active

**Answer: B**

Pilot light keeps only the core elements (such as a replicated database) running while the rest of the environment is pre-provisioned but switched off, so it can be scaled up within minutes during failover — giving an RTO in minutes at low cost. A fully working low-capacity standby (warm standby, A) runs continuously and costs more. Backup and restore (C) is the cheapest but has the slowest RTO (hours). Multi-site active/active (D) gives the lowest RTO but is the most expensive.

### Q128: Metric that defines how often data must be backed up

Which metric defines how often data must be backed up?

- A. RTO
- B. RPO
- C. Available storage
- D. Amount of data

**Answer: B**

Recovery Point Objective (RPO) is the maximum acceptable amount of data loss measured in time, which directly determines how frequently backups must be taken. Recovery Time Objective (RTO, A) defines how quickly a system must be restored after a disruption, not backup frequency. Available storage (C) and amount of data (D) are capacity considerations, not recovery metrics.

### Q129: Features of AWS Backup (Select THREE)

Which of the following are features of AWS Backup? (Select THREE.)

- A. Encrypted backups
- B. Works across every AWS service
- C. Works across multiple services
- D. Automated failover to read replicas
- E. Incremental backups
- F. Automated machine conversion

**Answer: A, C, E**

AWS Backup centralizes and automates data protection: it encrypts backups (A) using AWS KMS, works across multiple supported AWS services such as EBS, EFS, RDS, DynamoDB, and Storage Gateway (C), and performs incremental backups so only changed data is stored after the first full backup (E). It does not work across *every* AWS service (B) — only supported ones. Automated failover to read replicas (D) is an RDS high-availability feature, not a backup feature. Automated machine conversion (F) relates to migration tooling, not AWS Backup.

### Q130: Making an existing RDS DB instance highly available with minimal RTO

What is the best way to make an existing Amazon RDS DB instance highly available and minimize your RTO?

- A. Run a secondary copy of your DB instance in another Region.
- B. Run a Multi-AZ DB instance in the same Region.
- C. Create a read replica in another Region.
- D. Create a read replica in the same Region.

**Answer: B**

A Multi-AZ deployment maintains a synchronous standby replica in a different Availability Zone within the same Region and automatically fails over to it, providing high availability with an RTO of about a minute or two. A secondary copy in another Region (A) is a disaster recovery pattern with higher RTO and complexity. Read replicas (C, D) are for scaling read traffic and replicate asynchronously; promoting one is a manual process that does not provide automatic failover.

### Q131: Querying DynamoDB attributes that are not key columns

An existing application built on Amazon DynamoDB needs to query attributes that are not defined in key columns. Which of the following will accommodate the change most efficiently?

- A. No change is required since you can query any column on DynamoDB.
- B. Build a local secondary index.
- C. Build a global secondary index.
- D. Create another DynamoDB table, define the required keys, and then copy the data to the new table.

**Answer: C**

A global secondary index (GSI) lets you query a table using an alternate partition key (and optional sort key) that differs from the base table's keys, which is exactly what's needed to query non-key attributes efficiently on an existing table. DynamoDB cannot efficiently query arbitrary attributes without an index (A is false). A local secondary index (B) must share the same partition key as the base table and can only be created at table creation time, so it can't be added to an existing table. Creating a new table and copying data (D) is far more operational effort than adding a GSI.

### Q132: Static public IP that persists across stop and start

You are deploying an application into an AWS virtual private cloud (VPC). Internet hosts must connect to a static public IP address that is persistent across stop and start. Which of the following options should be used?

- A. An Elastic IP address
- B. A public IP address
- C. A private IP address
- D. A NAT gateway

**Answer: A**

An Elastic IP address is a static public IPv4 address that you allocate to your account and associate with an instance; it remains the same across stops and starts, satisfying the persistent-address requirement. A default public IP address (B) is released and reassigned when an instance is stopped and started, so it is not persistent. A private IP address (C) is not reachable from internet hosts. A NAT gateway (D) enables outbound internet access for private instances; it is not a static inbound address for the application.

### Q133: What a connection to a transit gateway is called

What is a connection to a transit gateway called?

- A. Virtual Private Network (VPN)
- B. Attachment
- C. Route
- D. Virtual Private Cloud (VPC)

**Answer: B**

In AWS Transit Gateway terminology, each connection from a VPC, VPN, Direct Connect gateway, or peered transit gateway is called an *attachment*. A VPN (A) is one type of resource you attach, not the generic term for the connection. A route (C) is an entry in a route table that directs traffic, not the connection itself. A VPC (D) is a resource that you attach to the transit gateway via an attachment.

### Q134: Components of an AWS Site-to-Site VPN connection (Select TWO)

What are the components of an AWS Site-to-Site VPN connection? (Select TWO.)

- A. Customer gateway device
- B. Interface endpoint
- C. Virtual private gateway
- D. Virtual Private Cloud (VPC) peering connection
- E. Gateway endpoint

**Answer: A, C**

A Site-to-Site VPN connects your on-premises network to a VPC using a customer gateway device on the on-premises side (A) and a virtual private gateway (or transit gateway) on the AWS side (C) to terminate the encrypted tunnels. Interface endpoints (B) and gateway endpoints (E) are VPC endpoint types for privately reaching AWS services, unrelated to VPN. A VPC peering connection (D) links two VPCs and is not part of a Site-to-Site VPN.

### Q135: True statements about VPC peering connections (Select TWO)

What is TRUE about Virtual Private Cloud (VPC) peering connections? (Select TWO.)

- A. Connections are one-to-many.
- B. Connections are one-to-one.
- C. Connections require a transit gateway.
- D. Connections can span accounts.
- E. Connections are transitive.

**Answer: B, D**

A VPC peering connection is a one-to-one link between exactly two VPCs (B), and the two VPCs can belong to different AWS accounts — and even different Regions (D). Peering is not one-to-many (A); each pair needs its own connection. It does not require a transit gateway (C) — peering is a direct relationship. Peering is non-transitive (E), so traffic cannot flow through an intermediate VPC to reach a third one.

### Q136: SQS queue type that provides at-least-once delivery

Which type of Amazon Simple Queue Service (Amazon SQS) queue provides at-least-once delivery?

- A. First in, first out (FIFO) queue
- B. Standard queue
- C. Dead-letter queue
- D. Long polling

**Answer: B**

Standard queues guarantee at-least-once delivery, which means a message may occasionally be delivered more than once, in exchange for nearly unlimited throughput and best-effort ordering. FIFO queues (A) provide exactly-once processing and strict ordering, not at-least-once. A dead-letter queue (C) is a destination for messages that can't be processed successfully, not a delivery model. Long polling (D) is a message-retrieval technique, not a queue type.

### Q137: Advantage of long polling over short polling in SQS

What is an advantage of long polling compared to short polling while using Amazon Simple Queue Service (Amazon SQS)?

- A. Long polling provides an immediate response from a ReceiveMessage call.
- B. Long polling is more stable when using a single thread to poll multiple queues.
- C. Long polling reduces the cost of using Amazon SQS by reducing the number of empty responses and false empty responses.
- D. Long polling reduces cost by only sampling a subset of Amazon SQS servers.

**Answer: C**

Long polling waits until a message is available (or the timeout expires) before responding, which eliminates most empty responses and avoids false empty responses by querying all SQS servers. Fewer empty `ReceiveMessage` calls mean lower request costs. An immediate response (A) describes short polling, not long polling. Long polling is not about single-thread stability (B). Sampling only a subset of servers (D) describes short polling, which is what causes false empty responses.

### Q138: A feature of Amazon Simple Notification Service (Amazon SNS)

What is a feature of Amazon Simple Notification Service (Amazon SNS)?

- A. Amazon SNS exchanges messages through a polling model.
- B. Amazon SNS can send messages to distributed components of applications without requiring each component to be concurrently available.
- C. Amazon SNS can push messages to multiple subscribers.
- D. Amazon SNS keeps messages persistent.

**Answer: C**

Amazon SNS is a publish/subscribe (pub/sub) service that pushes messages to multiple subscribers (such as SQS queues, Lambda functions, HTTP endpoints, and email) at once. It uses a push model, not polling (A) — polling describes SQS. Decoupling components that don't need to be concurrently available (B) and keeping messages persistent in a queue (D) describe Amazon SQS, not SNS.

### Q139: Potential benefits of an Amazon CloudFront distribution (Select TWO)

What are the potential benefits of implementing an Amazon CloudFront distribution? (Select TWO.)

- A. Increased application security
- B. Two global static IP addresses
- C. Automatic redundancy for all application content
- D. Reduced latency for access to application content
- E. On-premises data caching

**Answer: A, D**

CloudFront caches content at edge locations close to users, reducing latency for accessing application content (D), and it increases application security by integrating with AWS WAF, AWS Shield, and HTTPS/TLS at the edge (A). Two global static IP addresses (B) is a feature of AWS Global Accelerator, not CloudFront. CloudFront caches content but does not provide automatic redundancy for *all* application content (C). It caches at AWS edge locations, not on-premises (E).

### Q140: Benefit of AWS Outposts servers versus an Outposts rack

What is a benefit of choosing AWS Outposts servers when compared to an Outposts rack?

- A. More AWS services are available on Outposts servers
- B. Pool compute and storage capacity between multiple Outposts servers
- C. A smaller-sized device can be placed in your own rack
- D. You don't need to host your Outposts servers in a data center

**Answer: C**

Outposts servers are individual 1U or 2U devices that fit into your existing rack, making them ideal for smaller spaces such as retail stores or branch offices where a full Outposts rack won't fit (C). Outposts racks actually support more AWS services and larger capacity (A is a rack benefit). Only racks let you pool compute and storage across multiple units (B). Both form factors still require a suitable on-premises facility with power, cooling, and network (D is false).

### Q141: Preventing duplicate processing of records from a queue

An API receives a high volume of sensor data. The data is written to a queue before being processed to produce trend analysis and forecasting reports. With the current architecture, some data records are being received and processed more than once. How can a solutions architect modify the architecture to ensure that duplicate records are not processed?

- A. Configure the API to send the records to Amazon Kinesis Data Streams.
- B. Configure the API to send the records to Amazon Kinesis Data Firehose.
- C. Configure the API to send the records to Amazon Simple Notification Service (Amazon SNS).
- D. Configure the API to send the records to an Amazon Simple Queue Service (Amazon SQS) FIFO queue.

**Answer: D**

An SQS FIFO queue provides exactly-once processing and deduplication (using a deduplication ID within a 5-minute interval), which guarantees that duplicate records are not processed. SQS standard queues, Kinesis Data Streams (A), and Kinesis Data Firehose (B) all provide at-least-once delivery, so consumers can see duplicates. SNS (C) is a pub/sub push service that also delivers at least once and does not deduplicate.

### Q142: DNS failover to a static website hosted on Amazon S3

Which AWS service can you implement to create a DNS failover to a static website hosted on Amazon S3?

**Answer: Amazon Route 53 with the failover routing policy**

Route 53 failover routing uses health checks on the primary endpoint and automatically redirects DNS queries to a secondary resource — such as a static website hosted in an S3 bucket — when the primary becomes unhealthy. This provides DNS-level failover for high availability.

### Q143: S3 storage class for cold data

What Amazon S3 storage class would you choose to store cold data?

**Answer: Amazon S3 Glacier**

S3 Glacier storage classes are purpose-built for cold (rarely accessed) archival data at the lowest cost. Depending on retrieval needs you choose Glacier Instant Retrieval, Glacier Flexible Retrieval, or Glacier Deep Archive — all far cheaper than S3 Standard for data that is seldom accessed.

### Q144: Parallel file system for frequently accessed data

What storage solution would you choose if you needed a parallel file system to store frequently accessed data?

**Answer: Amazon FSx for Lustre**

Amazon FSx for Lustre is a fully managed, high-performance parallel file system designed for compute-intensive workloads (HPC, machine learning, media processing) that need fast access to frequently used data with high throughput and low latency.

### Q145: Synchronous data replication across Availability Zones for Amazon RDS

If you need to design and implement synchronous data replication across Availability Zones for your Amazon RDS instances, what is the best solution?

**Answer: Multi-AZ deployment**

An RDS Multi-AZ deployment synchronously replicates data to a standby instance in a different Availability Zone and automatically fails over to it, providing high availability and durability within a Region.

### Q146: Asynchronous data replication to an Amazon RDS instance in another Region

If you need to design and implement asynchronous data replication to another Amazon RDS instance in another AWS Region, what is the best solution?

**Answer: Add a read replica (cross-Region read replica)**

RDS read replicas use asynchronous replication and can be created in another AWS Region. They offload read traffic and can be promoted to a standalone database, supporting cross-Region scaling and disaster recovery.

### Q147: Relational database for an RTO of 60 seconds and RPO of 1 second

You need to create a disaster recovery plan for your relational database with an RTO of 60 seconds and an RPO of 1 second. What Amazon RDS database would be the best choice?

**Answer: Amazon Aurora Global Database**

Amazon Aurora Global Database spans a single Aurora database across multiple AWS Regions using storage-based replication with typical latency of less than 1 second (meeting the 1-second RPO), and it can promote a secondary Region in well under a minute (meeting the 60-second RTO). This makes it ideal for low-RPO, low-RTO cross-Region disaster recovery of relational data.

## References

- [AWS Solutions Architect Associate - Official Exam Guide](https://aws.amazon.com/certification/certified-solutions-architect-associate/)
