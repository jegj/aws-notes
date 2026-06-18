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
  - [Hybrid Networking (Site-to-Site VPN & Direct Connect)](#hybrid-networking-site-to-site-vpn--direct-connect)
  - [AWS Transit Gateway](#aws-transit-gateway)
  - [Data Ingestion Patterns](#data-ingestion-patterns)
  - [Backup & Recovery](#backup--recovery)
  - [DynamoDB Global Tables](#dynamodb-global-tables)
  - [Route 53 Routing Policies](#route-53-routing-policies)
  - [AWS Organizations, OUs & Service Control Policies (SCPs)](#aws-organizations-ous--service-control-policies-scps)
  - [Internet Gateway vs. NAT Gateway](#internet-gateway-vs-nat-gateway)
  - [EC2 Instance Savings Plans vs. Compute Savings Plans](#ec2-instance-savings-plans-vs-compute-savings-plans)
  - [Caching Strategies (Lazy Loading & Write-Through)](#caching-strategies-lazy-loading--write-through)
  - [CloudFront Price Classes](#cloudfront-price-classes)
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

### Hybrid Networking (Site-to-Site VPN & Direct Connect)

#### AWS Site-to-Site VPN

An AWS Site-to-Site VPN connection offers two encrypted VPN tunnels between a **virtual private gateway** (or a **transit gateway**) on the AWS side and a **customer gateway** on the on-premises side. It has two endpoints:

- A **virtual private gateway** is the VPN concentrator on the AWS side of the connection.
- A **customer gateway** is a resource you create in AWS that represents the physical or software device on your on-premises network; you provide its public IP and configuration.
- Routing can be **static** or **dynamic**. Dynamic routing uses **Border Gateway Protocol (BGP)** so routes are advertised automatically.
- **Throughput:** a single VPN tunnel is capped at **~1.25 Gbps**. Adding tunnels to one VPN connection only provides **failover**, not more bandwidth. To scale throughput, attach the VPNs to an **AWS Transit Gateway** with **Equal Cost Multipath (ECMP)** routing, which aggregates bandwidth across multiple tunnels — a virtual private gateway cannot do this.

For consistent, dedicated bandwidth between on-premises and AWS, **AWS Direct Connect** is preferred over a Site-to-Site VPN (which runs over the public internet).

> Note: Both Site-to-Site VPN and Direct Connect (via a transit VIF) can attach to an **AWS Transit Gateway** to reach many VPCs through a single hub — see [AWS Transit Gateway](#aws-transit-gateway).

![AWS Site-to-Site VPN — customer gateway and virtual private gateway endpoints](https://github.com/user-attachments/assets/7869c9e5-0034-4d95-b7c2-de7df160d91f)

#### AWS Direct Connect

AWS Direct Connect (DX) is a **dedicated, private physical connection** between your on-premises data center and AWS, giving consistent bandwidth and lower, more predictable latency than a VPN over the public internet.

- A **virtual interface (VIF)** runs on top of a DX connection: **private VIF** (reach a VPC), **public VIF** (reach public AWS services), **transit VIF** (reach a transit gateway).
- DX traffic is **not encrypted** on its own — add a **Site-to-Site VPN over DX** when you need encryption.

**Resiliency** — multiple VIFs share one physical link, so true high availability needs multiple *connections*, ideally at *multiple locations*:

| Resiliency | Setup |
| --- | --- |
| Low (dev) | Single DX connection at one DX location |
| High | Connections at **multiple DX locations** |
| Maximum | Multiple DX locations, each with connections to separate devices |
| Backup | A Site-to-Site VPN as failover for the DX connection |

> Exam tip (Q62): highest resiliency comes from connections at **multiple DX locations** — not extra VIFs on one link (shared physical failure) or a VPN on a single DX (still one connection).

### AWS Transit Gateway

AWS Transit Gateway acts as a **hub** that controls how traffic is routed among all connected networks, which act as **spokes**. This hub-and-spoke model simplifies management and reduces operational cost because each network connects only to the transit gateway rather than to every other network. Any new VPC connected to the transit gateway is automatically reachable by every other connected network — scaling cleanly to thousands of VPCs and on-premises networks.

- **Components:** a transit gateway is made up of **attachments** and **route tables**. An *attachment* is a source and destination of packets; you can attach a **VPC**, **VPN connection**, **Direct Connect gateway**, **Transit Gateway Connect**, or a **peering connection** (resources must be in the same Region as the transit gateway).
- **Setup / sharing:** a transit gateway works across AWS accounts. You can share it with other accounts using **AWS Resource Access Manager (RAM)**; the other account owner can then attach their VPCs, and either account can delete the attachment.
- Unlike VPC peering, transit gateway routing is **transitive**, so connected networks can reach each other through the hub.
- **VPN bandwidth aggregation:** a transit gateway supports **Equal Cost Multipath (ECMP)** routing, which combines the throughput of multiple Site-to-Site VPN tunnels (each capped at ~1.25 Gbps) to scale hybrid connectivity beyond a single tunnel — something a virtual private gateway cannot do (see [Hybrid Networking](#hybrid-networking-site-to-site-vpn--direct-connect)).

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

### DynamoDB Global Tables

Amazon DynamoDB global tables provide a **fully managed, multi-Region, multi-active** database. DynamoDB automatically replicates your table to other AWS Regions you choose, and every replica can serve both **reads and writes** (active-active).

- **Multi-active replication:** applications read and write to the **nearest Region**, lowering latency for globally distributed users. Changes propagate to the other replicas asynchronously, typically within **a second**.
- **High availability / disaster recovery:** because data lives in multiple Regions, the table survives a full Region outage with an **RTO and RPO close to zero** — failover is just routing traffic to a healthy Region.
- **Conflict resolution:** concurrent writes to the same item in different Regions are resolved with a **last-writer-wins** policy (based on timestamp).
- **Requirements:** each table must have **DynamoDB Streams enabled** and identical table definitions (keys, indexes) across Regions.

**When to use:** globally distributed apps needing low-latency local access, or relational/NoSQL workloads that require Region-level resilience without building your own replication.

> Exam tip: if a question asks for a database with low-latency access across multiple Regions and/or multi-Region disaster recovery for DynamoDB, the answer is **global tables**.

### Route 53 Routing Policies

Amazon Route 53 is AWS's managed DNS service. A **routing policy** decides how Route 53 answers DNS queries for a record. Pick the policy by *what drives the decision* — percentage, location, latency, health, or nothing.

| Policy | Use it when you want to… |
| --- | --- |
| **Simple** | Return a single record for a domain (no special logic). |
| **Weighted** | Split traffic by **percentage** across resources (e.g., 80/20) — useful for A/B testing or blue-green and canary releases. |
| **Latency-based** | Send users to the Region that gives them the **lowest latency**. |
| **Failover** | Route to a primary resource and **fail over** to a standby when a health check fails (active-passive). |
| **Geolocation** | Route based on the **user's location** (continent/country/state) — for content localization or compliance. |
| **Geoproximity** | Route based on the **geographic distance** between users and resources, with an adjustable **bias** to shift traffic. |
| **Multivalue answer** | Return **multiple healthy records** (up to 8) at random — DNS-level health checking and simple load spreading. |

- **Health checks** can be attached to most policies (notably failover and multivalue) so unhealthy endpoints are removed from responses.
- **Weighted vs. percentage routing:** only **weighted** routing enforces a fixed split like 80/20. Geolocation/geoproximity route by *where users are*, not by a percentage.
- **Geolocation vs. geoproximity:** geolocation matches the user's mapped location; geoproximity is distance-based and lets you bias traffic toward or away from a Region.

> Exam tip (Q106): to split traffic by a fixed percentage across Regions (e.g., 80% / 20%), choose **weighted routing** — geolocation and geoproximity route by location, not percentage.

### AWS Organizations, OUs & Service Control Policies (SCPs)

**AWS Organizations** lets you centrally manage and govern many AWS accounts. Accounts are grouped into a hierarchy of **Organizational Units (OUs)**, and **Service Control Policies (SCPs)** set the maximum permissions (guardrails) for those accounts.

- **Organization structure:** a **management account** sits at the **root**, under which you create **OUs** (which can nest) and place member accounts — typically grouped by **department, environment, or function**.
- **Service Control Policies (SCPs):** policies that define the **permission boundary** for accounts. An SCP attached to an OU applies to **every account in that OU** (and nested OUs), so you restrict a service for a whole group in one place.
- **SCPs vs. IAM policies — key distinction:**
  - **SCPs** set the **maximum** allowed permissions across whole accounts/OUs; they **never grant** access on their own.
  - **IAM policies** grant permissions to individual users/roles **within** an account.
  - Effective permissions = the **intersection** of SCPs and IAM policies. An action must be allowed by both.
- **Other benefits:** consolidated billing, and central management of services like AWS CloudTrail, Config, and Backup across the organization.

> Exam tip (Q109): to restrict specific services across hundreds of accounts efficiently, group accounts into **OUs by department** and attach a **service control policy at the OU level** — not IAM policies (which work inside a single account) and not one SCP per account (which defeats grouping).

### Internet Gateway vs. NAT Gateway

Both connect a VPC to the internet, but they serve opposite directions of traffic.

| | **Internet Gateway (IGW)** | **NAT Gateway** |
| --- | --- | --- |
| **Purpose** | Lets resources in **public subnets** send and receive traffic **to/from the internet** (inbound + outbound) | Lets resources in **private subnets** make **outbound-only** connections to the internet |
| **Direction** | Bidirectional (can be reached from the internet) | Outbound only — the internet **cannot initiate** connections back |
| **Attached to** | The **VPC** (one per VPC) | A **public subnet**, with an Elastic IP |
| **Typical use** | Public web/ALB tier that clients reach directly | Private app/DB tier that needs updates, patches, or to call external APIs while staying unreachable |

- A subnet is "**public**" when its route table sends `0.0.0.0/0` to an **internet gateway**; it's "**private**" when it has no such route (or routes `0.0.0.0/0` to a **NAT gateway**).
- The NAT gateway itself lives in a **public subnet** (so its own route to the internet is via the IGW) and is referenced by the **private** subnets' route tables.
- For AZ-fault-tolerant outbound access, deploy **one NAT gateway per Availability Zone**.

> Exam tip (Q110): to let internet clients reach a public web tier you need an **internet gateway** attached to the VPC. A **NAT gateway** is only for giving *private* instances **outbound** internet access — it does not make them reachable from the internet.

### EC2 Instance Savings Plans vs. Compute Savings Plans

**Savings Plans** give you discounted pricing (up to ~72% vs. On-Demand) in exchange for a commitment to a consistent amount of compute usage (measured in **$/hour**) for a **1-year or 3-year** term. There are two flavors, and the trade-off is **flexibility vs. discount**.

| | **EC2 Instance Savings Plans** | **Compute Savings Plans** |
| --- | --- | --- |
| **Commitment** | 1 or 3 years | 1 or 3 years |
| **Discount** | **Highest** (similar to Standard Reserved Instances) | Slightly lower, but still large |
| **Locked to** | A specific **instance family** in a chosen **Region** (e.g., M5 in us-east-1) | Nothing — applies automatically |
| **Flexible across** | Instance **size**, OS, and tenancy *within that family/Region* | **Any** instance family, size, Region, OS, and tenancy |
| **Also covers** | EC2 only | EC2 **plus AWS Fargate and AWS Lambda** |

- Choose **EC2 Instance Savings Plans** when your workload is **stable and predictable** on a known instance family/Region and you want the **deepest discount**.
- Choose **Compute Savings Plans** when you want **maximum flexibility** — you may change instance families/Regions or run containers/serverless — accepting a slightly smaller discount.
- Compared with **Reserved Instances**, Savings Plans are simpler (commit to a dollar amount, not specific instance reservations).

> Exam tip (Q118): a commitment (1 or 3 years) that stays **flexible across instance families** points to **Compute Savings Plans**. If it's locked to one family/Region for a bigger discount, that's an **EC2 Instance Savings Plan**.

### Caching Strategies (Lazy Loading & Write-Through)

A **cache** stores frequently accessed data in fast in-memory storage (typically **Amazon ElastiCache** — Redis or Memcached) to reduce latency and offload the backing database. The exam focuses on **how** the cache is kept in sync with the database, which comes down to two core strategies (often combined).

**Lazy Loading (cache-aside):** data is loaded into the cache only when it is requested.

1. Application requests data → checks the cache first.
2. **Cache hit** → return the cached value (fast).
3. **Cache miss** → read from the database, write it into the cache, then return it.

- **Pros:** only requested data is cached (no wasted memory); resilient — a cache node failure just causes more misses, not data loss.
- **Cons:** a cache miss costs **three trips** (cache → DB → cache), adding latency on the first read; data can become **stale**, since the cache isn't updated when the DB changes.

**Write-Through:** the cache is updated **at the same time** the database is written.

1. Application writes data → writes to the database **and** the cache together.
2. Reads always hit a populated, up-to-date cache.

- **Pros:** data in the cache is **never stale** and reads are always fast.
- **Cons:** **write penalty** — every write touches two stores; **cache churn** — data is cached even if it's never read, wasting memory; on a new node, data is missing until it's written again.

| | **Lazy Loading** | **Write-Through** |
| --- | --- | --- |
| **Cache populated on** | Read (cache miss) | Write |
| **Data freshness** | Can be **stale** | **Always current** |
| **Memory usage** | Only requested data | All written data (can be wasteful) |
| **Penalty** | Slow first read (3 trips) | Slower writes |
| **Best for** | Read-heavy workloads where some staleness is OK | Workloads that must never serve stale data |

**Combining them + TTL:** real systems often use **both** — write-through to keep hot data fresh, lazy loading to backfill what's missing. Add a **TTL (Time to Live)** on cache entries to bound staleness: entries expire automatically, forcing a fresh DB read. TTL is the common fix for lazy loading's staleness problem.

**Related caching services (exam context):**

- **Amazon ElastiCache** — managed Redis/Memcached; you implement lazy loading or write-through in your app code. Use it to cache **relational database** (RDS/Aurora) query results and session data.
- **Amazon DynamoDB Accelerator (DAX)** — a fully managed, write-through in-memory cache **specifically for DynamoDB**, delivering microsecond reads. Don't pick DAX for relational databases.
- **Amazon CloudFront** — edge caching for **static/dynamic web content**, not database query results.

> Exam tip (Q46): "cache must **never be stale**" on writes → **ElastiCache with write-through**. "Reduce DB load for a read-heavy app" → **lazy loading** (often + TTL). "Cache for **DynamoDB** with microsecond latency" → **DAX**.

### CloudFront Price Classes

A **price class** controls **which edge locations** CloudFront uses to serve your content, trading off global performance for lower cost. Edge locations are grouped by region, and some regions (e.g. South America, Australia, India) cost more — picking a smaller price class excludes the pricier ones.

| Price Class | Edge locations used | Cost |
| --- | --- | --- |
| **Price Class 100** | North America + Europe | Cheapest |
| **Price Class 200** | NA + Europe + Asia, Middle East, Africa | Medium |
| **Price Class All** | All locations worldwide (incl. South America, Australia) | Most expensive (best global performance) |

**Key behavior:**

- Users **outside** the selected regions are **still served** — CloudFront routes them to the **nearest included** edge location, so they get higher latency, not an error.
- You keep all CloudFront features (caching, HTTPS, etc.) — only the **geographic footprint** changes.
- **When to use Price Class 100:** most users are in North America / Europe and you want to **cut costs**, accepting slightly higher latency for the small percentage of users elsewhere.

> Don't confuse with **Geo-Restriction**: a price class is a **cost/performance** control and never blocks anyone; Geo-Restriction is an **access control** that actually blocks users by country.

## Questions

### Q1: Most cost-effective way to transfer 60 TB monthly to on-premises

A company that processes satellite images has an application that runs on AWS. The company stores the images in an Amazon S3 bucket. For compliance reasons, the company must replicate all data once a month to an on-premises location. The average amount of data that the company needs to transfer is 60 TB.

What is the MOST cost-effective way to transfer this data?

- A. Export the data monthly from the existing S3 bucket to an AWS Snowball Edge Storage Optimized device. Ship the device to the on-premises location. Transfer the data. Return the device a week later.
- B. Use a new S3 bucket with S3 Standard-IA storage. Use an AWS Storage Gateway File Gateway to transfer the data from the new S3 bucket to the on-premises location. Delete the images from the new S3 bucket after the transfer.
- C. Use S3 bucket replication to copy all objects to a new S3 bucket with S3 Standard-IA storage. Use Amazon S3 to transfer the data from the new S3 bucket to the on-premises location. Delete the images from the new S3 bucket after the transfer.
- D. Create an Amazon CloudFront distribution for the objects in the existing S3 bucket. Download the objects from CloudFront to the on-premises location. Delete the objects from the distribution after the transfer.

<details>
<summary>Show answer</summary>

**Answer: A**

Snowball Edge Storage Optimized is designed for large-scale offline data transfers. At 60 TB/month, shipping a physical device is far cheaper than internet egress costs. Options B, C, and D all require transferring data over the internet, which would incur high AWS data transfer (egress) charges at that volume — making them significantly more expensive.
</details>

### Q2: Service to manage vulnerability scans in EC2 and ECR

A company is investigating services to manage vulnerability scans in Amazon EC2 instances and container images that the company stores in Amazon Elastic Container Registry (Amazon ECR). The service should identify potential software vulnerabilities and categorize the severity of the vulnerabilities.

Which AWS service will meet these requirements?

- A. Amazon GuardDuty
- B. Patch Manager, a capability of AWS Systems Manager
- C. Amazon Inspector
- D. AWS Config

<details>
<summary>Show answer</summary>

**Answer: C**

Amazon Inspector is purpose-built for vulnerability scanning of EC2 instances and container images in ECR. It automatically discovers workloads and continuously scans for software vulnerabilities, assigning severity scores. GuardDuty is for threat detection (suspicious activity), not vulnerability scanning. Patch Manager handles OS patching but doesn't categorize software vulnerability severity. AWS Config tracks resource configuration compliance, not software vulnerabilities.
</details>

### Q3: DR strategy with 5-minute RTO and minimal running capacity

A company is designing a disaster recovery (DR) architecture for an important application on AWS. The company has determined that the recovery time objective (RTO) is 5 minutes with a minimal running instance capacity to support the application in the AWS DR site. The company needs to minimize costs for the DR architecture.

Which DR strategy will meet these requirements?

- A. Warm standby
- B. Pilot light
- C. Multi-site active-active
- D. Backup and restore

<details>
<summary>Show answer</summary>

**Answer: A**

Warm standby keeps a scaled-down but fully functional environment running at all times, enabling recovery within minutes (meets the 5-minute RTO). Pilot light only keeps core components running (e.g., database) and requires additional time to provision and start app servers, making it harder to meet a strict 5-minute RTO. Multi-site active-active meets the RTO but is the most expensive option (full duplicate environment). Backup and restore has the longest RTO (hours) and lowest cost but cannot meet a 5-minute RTO.
</details>

### Q4: Most cost-effective storage for MariaDB on RDS with 40 GiB and 1,000 IOPS

A company is transitioning its Amazon EC2 based MariaDB database to Amazon RDS. The company has already identified a database instance type that will meet the company's CPU and memory requirements. The database must provide at least 40 GiB of storage capacity and 1,000 IOPS.

Which storage configuration for the Amazon RDS for MariaDB instance is MOST cost-effective?

- A. Provision 350 GiB of magnetic storage for the RDS instance.
- B. Provision 50 GiB of General Purpose SSD (gp3) storage for the RDS instance.
- C. Provision 334 GiB of General Purpose SSD (gp2) storage for the RDS instance.
- D. Provision 50 GiB of Provisioned IOPS storage with 1,000 IOPS for the RDS instance.

<details>
<summary>Show answer</summary>

**Answer: B**

gp3 includes 3,000 IOPS at no additional cost independent of volume size, so 50 GiB of gp3 easily covers the 1,000 IOPS requirement at minimal cost. Magnetic storage (A) does not support configurable IOPS. gp2 (C) ties IOPS to volume size at 3 IOPS/GB, requiring 334 GiB to reach 1,000 IOPS — far more storage than needed, making it more expensive. Provisioned IOPS (D) would meet the requirement but costs more than gp3 for this workload.
</details>

### Q5: Database for key-value chat app with high read rate and microsecond latency

A company is developing a chat application that will be deployed on AWS. The application stores the messages by using a key-value data model. Groups of users typically read the messages multiple times. A solutions architect must select a database solution that will scale for a high rate of reads and will deliver messages with microsecond latency.

Which database solution will meet these requirements?

- A. Amazon Aurora with Aurora Replicas
- B. Amazon DynamoDB with DynamoDB Accelerator (DAX)
- C. Amazon Aurora with Amazon ElastiCache for Memcached
- D. Amazon Neptune with Amazon ElastiCache for Memcached

<details>
<summary>Show answer</summary>

**Answer: B**

DynamoDB is a NoSQL database that natively supports key-value records and scales for high read rates. DAX is an in-memory cache for DynamoDB that delivers response times in microseconds. Aurora (A, C) is a relational database — not a key-value store — and cannot consistently achieve microsecond latency. Neptune (D) is a graph database optimized for highly connected data, not simple key-value workloads.
</details>

### Q6: Secure RDS instances from internet access, allow only app tier on specific port (Select TWO)

A company has strict data protection requirements. A solutions architect must configure security for a VPC to ensure that backend Amazon RDS DB instances cannot be accessed from the internet. The solutions architect must ensure that the DB instances are accessible from the application tier over a specified port only.

Which actions should the solutions architect take to meet these requirements? (Select TWO.)

- A. Specify a DB subnet group that contains only private subnets for the DB instances.
- B. Attach an elastic network interface with a private IPv4 address to each DB instance.
- C. Configure AWS Shield with the VPC. Update the route tables for the subnets that the DB instances use.
- D. Configure an AWS Direct Connect connection on the database port between the application tier and the backend.
- E. Add an inbound rule to the database security group that allows requests from the security group of the application tier over the database port. Remove other inbound rules.

<details>
<summary>Show answer</summary>

**Answer: A, E**

Placing DB instances in a DB subnet group with only private subnets ensures internet traffic cannot reach them (no internet gateway route). Adding a security group inbound rule that allows only the application tier's security group on the specific port enforces port-level access control. Elastic network interfaces (B) are logical networking components and don't restrict internet access. AWS Shield (C) protects against DDoS attacks and cannot be used as a routing target. Direct Connect (D) is for dedicated connectivity from on-premises to AWS, not for internal VPC tier-to-tier communication.
</details>

### Q7: EBS volume type supporting up to 20,000 IOPS

A company is deploying a new database on a new Amazon EC2 instance. The workload of this database requires a single Amazon Elastic Block Store (Amazon EBS) volume that can support up to 20,000 IOPS.

Which type of EBS volume meets this requirement?

- A. Throughput Optimized HDD
- B. Provisioned IOPS SSD
- C. General Purpose SSD
- D. Cold HDD

<details>
<summary>Show answer</summary>

**Answer: B**

Provisioned IOPS SSD (io1/io2) supports up to 64,000 IOPS per volume, easily covering the 20,000 IOPS requirement. Throughput Optimized HDD (A) is limited to 500 IOPS. General Purpose SSD (C) is limited to 16,000 IOPS — insufficient for this workload. Cold HDD (D) is designed for cold, infrequently accessed workloads and measures performance in throughput, not IOPS.
</details>

### Q8: Most cost-effective storage for graphic rendering with 400 GiB temp data and 40,000 IOPS

A media company is designing a new application for graphic rendering. The application requires up to 400 GB of storage for temporary data that is discarded after the frames are rendered. The application requires approximately 40,000 random IOPS to perform the rendering.

What is the MOST cost-effective storage option for this rendering application?

- A. A storage optimized Amazon EC2 instance with instance store storage.
- B. A storage optimized Amazon EC2 instance with a Provisioned IOPS SSD (io1 or io2) Amazon Elastic Block Store (Amazon EBS) volume.
- C. A burstable Amazon EC2 instance with a Throughput Optimized HDD (st1) Amazon Elastic Block Store (Amazon EBS) volume.
- D. A burstable Amazon EC2 instance with Amazon S3 storage over a VPC endpoint.

<details>
<summary>Show answer</summary>

**Answer: A**

Storage optimized instances with instance store are designed for workloads requiring high, random IOPS on local storage (tens of thousands of low-latency IOPS), and the instance store has no additional cost. Since the data is temporary and discarded after rendering, persistence is not needed — making EBS unnecessary. Provisioned IOPS SSD (B) can meet the IOPS requirement but adds EBS cost on top of the instance cost, making it less cost-effective. Throughput Optimized HDD (C) maximizes throughput, not random IOPS, and cannot meet the requirement. S3 (D) is object storage, not suitable for high-speed random read/write scratch space.
</details>

### Q9: Reliable S3 upload for 20-30 GB log files over intermittent network

A company has an on-premises application that exports log files about users of a website. The log files range from 20 GB to 30 GB in size. A solutions architect has created an Amazon S3 bucket to store the files. The files will be uploaded directly from the application. The network connection experiences intermittent failures, and the upload sometimes fails. The solutions architect must design a solution that resolves this issue. The solution must minimize operational overhead.

Which solution will meet these requirements?

- A. Enable S3 Transfer Acceleration.
- B. Copy the files to an Amazon EC2 instance in the closest AWS Region. Use S3 Lifecycle policies to copy the log files to Amazon S3.
- C. Use multipart upload to Amazon S3.
- D. Upload the files to two AWS Regions simultaneously. Enable two-way Cross-Region Replication between the two Regions.

<details>
<summary>Show answer</summary>

**Answer: C**

Multipart upload splits large files into parts that are uploaded independently. If any part fails due to a network interruption, only that part needs to be retransmitted — not the entire file. This directly resolves intermittent failure issues with minimal operational overhead. S3 Transfer Acceleration (A) speeds up uploads via edge locations but doesn't handle partial failures. S3 Lifecycle policies (B) cannot transfer files from EC2 block storage to S3. Uploading to two Regions simultaneously (D) adds unnecessary operational overhead and cost.
</details>

### Q10: Most cost-effective solution for always-available OLTP app with unpredictable traffic

A company is deploying a new application that will consist of an application layer and an online transaction processing (OLTP) relational database. The application must be available at all times. However, the application will have unpredictable traffic patterns. The company wants to pay the minimum for compute costs during these idle periods.

Which solution will meet these requirements MOST cost effectively?

- A. Run the application on Amazon EC2 instances by using a burstable instance type. Use Amazon Redshift for the database.
- B. Deploy the application and a MySQL database to Amazon EC2 instances by using AWS CloudFormation. Delete the instances at the beginning of the idle periods.
- C. Deploy the application on Amazon EC2 instances in an Auto Scaling group behind an Application Load Balancer. Use Amazon RDS for MySQL for the database.
- D. Run the application in containers with Amazon Elastic Container Service (Amazon ECS) on AWS Fargate. Use Amazon Aurora Serverless for the database.

<details>
<summary>Show answer</summary>

**Answer: D**

Fargate incurs minimal costs when the application is idle (no running containers = no compute charges), and Aurora Serverless also scales to zero compute when idle. This combination minimizes costs during unpredictable low-traffic periods while remaining available. EC2 burstable instances (A) still incur costs when idle, and Redshift is an OLAP data warehouse, not suited for OLTP. Deleting instances via CloudFormation (B) violates the always-available requirement. EC2 with Auto Scaling + RDS (C) keeps at least one instance and database running at all times, not minimizing idle costs.
</details>

### Q11: Ensure in-flight requests complete before instances are terminated during scale-in

A reporting application runs on Amazon EC2 instances behind an Application Load Balancer. The instances run in an Amazon EC2 Auto Scaling group across multiple Availability Zones. For complex reports, the application can take up to 15 minutes to respond to a request. A solutions architect is concerned that users will receive HTTP 5xx errors if a report request is in process during a scale-in event.

What should the solutions architect do to ensure that user requests will be completed before instances are terminated?

- A. Enable sticky sessions (session affinity) for the target group of the instances.
- B. Increase the instance size in the Application Load Balancer target group.
- C. Increase the cooldown period for the Auto Scaling group to a greater amount of time than the time required for the longest running responses.
- D. Increase the deregistration delay timeout for the target group of the instances to greater than 900 seconds.

<details>
<summary>Show answer</summary>

**Answer: D**

The ALB deregistration delay (connection draining) controls how long the load balancer waits before completing deregistration of an instance, allowing in-flight requests to finish. Since reports take up to 15 minutes (900 seconds), setting the deregistration delay above 900 seconds ensures requests complete before the instance is terminated. Sticky sessions (A) would fail during scale-in since the instance being removed would become unhealthy and the ALB would stop routing to it. Increasing instance size (B) speeds up processing but doesn't prevent termination mid-request. Cooldown periods (C) control time between scaling activities, not how long the ALB waits for in-flight requests to complete.
</details>

### Q12: Improve high availability for two EC2 instances behind a NLB in a single AZ

An application runs on two Amazon EC2 instances behind a Network Load Balancer. The EC2 instances are in a single Availability Zone.

What should a solutions architect do to make this architecture more highly available?

- A. Create a new VPC with two new EC2 instances in the same Availability Zone as the original EC2 instances. Create a VPC peering connection between the two VPCs.
- B. Replace the Network Load Balancer with an Application Load Balancer that is configured with the EC2 instances in an Auto Scaling group.
- C. Configure Amazon Route 53 to perform health checks on the EC2 instances behind the Network Load Balancer. Add a failover routing policy.
- D. Place the EC2 instances in an Auto Scaling group that extends across multiple Availability Zones. Designate the Auto Scaling group as the target of the Network Load Balancer.

<details>
<summary>Show answer</summary>

**Answer: D**

Extending the Auto Scaling group across multiple AZs ensures the application survives an AZ failure and automatically adds capacity when needed. VPC peering (A) provides connectivity but the instances remain in a single AZ, offering no HA improvement. Replacing the NLB with an ALB (B) doesn't improve availability since both load balancers are inherently highly available — the problem is the EC2 instances being in one AZ. Route 53 failover routing (C) requires a secondary destination, which isn't specified, and the instances still remain in a single AZ.
</details>

### Q13: Implement a pilot light DR strategy for a self-contained on-premises application

A company asks a solutions architect to implement a pilot light disaster recovery (DR) strategy for an existing on-premises application. The application is self-contained and does not need to access any databases.

Which solution will implement a pilot light DR strategy?

- A. Back up the on-premises application, configuration, and data to an Amazon S3 bucket. When the on-premises application fails, build a new hosting environment on AWS and restore the application from the information stored in the S3 bucket.
- B. Recreate the application hosting environment on AWS by using Amazon EC2 instances and stop the EC2 instances. When the on-premises application fails, start the stopped EC2 instances and direct 100% of application traffic to the EC2 instances running in the AWS Cloud.
- C. Recreate the application hosting environment on AWS by using Amazon EC2 instances. Direct 10% of application traffic to the EC2 instances running in the AWS Cloud. When the on-premises application fails, direct 100% of application traffic to the EC2 instances running in the AWS Cloud.
- D. Back up the on-premises application, configuration, and data to an Amazon S3 bucket. When the on-premises application fails, rebuild the on-premises hosting environment and restore the application from the information stored in the S3 bucket.

<details>
<summary>Show answer</summary>

**Answer: B**

Pilot light pre-provisions the environment in AWS but keeps it turned off (or at minimal scale), activating it only during DR failover. Stopping EC2 instances means resources are ready but incur no compute costs until needed — RPO and RTO are typically in the tens of minutes. Options A and D are backup and restore strategies (highest RTO, lowest cost). Option C is a warm standby strategy, which keeps a portion of traffic running continuously and is more expensive than pilot light.
</details>

### Q14: Reduce costs for an app that runs randomly a few times a day with 10-min max runtime

A company has an application that runs on a large general purpose Amazon EC2 instance type that is part of an EC2 Auto Scaling group. The company wants to reduce future costs associated with this application. After the company reviews metrics and logs in Amazon CloudWatch, the company notices that this application runs randomly a couple of times a day to retrieve and manage data. According to CloudWatch, the maximum runtime for each request is 10 minutes, the memory use is 4 GB, and the instances are always in the running state.

Which solution will reduce costs the MOST?

- A. Deploy the application on a large burstable EC2 instance.
- B. Refactor the application code to run as an AWS Lambda function.
- C. Containerize the application by using Amazon Elastic Kubernetes Service (Amazon EKS). Host the container on EC2 instances.
- D. Use AWS Instance Scheduler to start and stop the instances based on the runtimes in the logs.

<details>
<summary>Show answer</summary>

**Answer: B**

Lambda charges only for actual execution time and scales automatically — since the app runs randomly a few times a day for up to 10 minutes with 4 GB memory, Lambda eliminates the cost of keeping EC2 instances running 24/7. Burstable EC2 (A) still runs continuously, providing no cost reduction. EKS on EC2 (C) still requires instances in the running state. Instance Scheduler (D) is unsuitable for unpredictable runtimes and charges by the hour rather than exact execution time, potentially leaving the app unavailable when a request arrives.
</details>

### Q15: Command to retrieve EC2 instance metadata on Linux

A company needs to look up configuration details about how a Linux-based Amazon EC2 instance was launched.

Which command should a solutions architect run on the EC2 instance to gather the system metadata?

- A. `curl http://169.254.169.254/latest/meta-data/`
- B. `curl http://localhost/latest/meta-data/`
- C. `curl http://254.169.254.169/latest/meta-data/`
- D. `curl http://192.168.0.1/latest/meta-data/`

<details>
<summary>Show answer</summary>

**Answer: A**

EC2 instance metadata is only accessible via the link-local address `169.254.169.254`. Using `localhost` (B) resolves to `127.0.0.1`, which does not serve instance metadata. Option C has the IP octets in the wrong order. Option D uses a private network range (`192.168.x.x`), which does not serve instance metadata.
</details>

### Q16: Grant security team cross-account read access following best practices

A company uses one AWS account to run production workloads. The company has a separate AWS account for its security team. During periodic audits, the security team needs to view specific account settings and resource configurations in the AWS account that runs production workloads. A solutions architect must provide the required access to the security team by designing a solution that follows AWS security best practices.

Which solution will meet these requirements?

- A. Create an IAM user for each security team member in the production account. Attach a permissions policy that provides the permissions required by the security team to each user.
- B. Create an IAM role in the production account. Attach a permissions policy that provides the permissions required by the security team. Add the security team account to the trust policy.
- C. Create a new IAM user in the production account. Assign administrative privileges to the user. Allow the security team to use this account to log in to the systems they need to review.
- D. Create an IAM user for each security team member in the production account. Attach a permissions policy to a new IAM group. Assign the security team members to the group.

<details>
<summary>Show answer</summary>

**Answer: B**

Creating a cross-account IAM role with a trust policy that allows the security team's account to assume it is the AWS best practice for granting cross-account access. It follows least-privilege principles and avoids creating long-term credentials. Options A and D create IAM users in the production account for external team members, which does not follow best practices (long-term credentials, no cross-account role delegation). Option C grants administrative privileges, violating the principle of least privilege.
</details>

### Q17: Most cost-effective storage for rarely accessed data retained 5 years, accessible within 2 hours

A company needs to maintain data records for a minimum of 5 years. The data is rarely accessed after it is stored. The data must be accessible within 2 hours.

Which solution will meet these requirements MOST cost-effectively?

- A. Store the data in an Amazon Elastic File System (Amazon EFS) file system. Access the data by using AWS Direct Connect.
- B. Store the data in an Amazon Elastic Block Store (Amazon EBS) volume. Take snapshots. Store the snapshots in an Amazon S3 bucket.
- C. Store the data in an Amazon S3 bucket. Use an S3 Lifecycle policy to move the data to S3 Standard-Infrequent Access (S3 Standard-IA).
- D. Store the data in an Amazon S3 bucket. Use an S3 Lifecycle policy to move the data to S3 Glacier Instant Retrieval.

<details>
<summary>Show answer</summary>

**Answer: D**

S3 Glacier Instant Retrieval is purpose-built for long-term archival of rarely accessed data and provides millisecond retrieval (well within the 2-hour requirement) at the lowest cost among the viable options. EFS + Direct Connect (A) is not cost-effective without a file system requirement. EBS snapshots on S3 (B) add unnecessary cost by requiring EBS in addition to S3. S3 Standard-IA (C) is cheaper than Standard but more expensive than Glacier for long-term archival of rarely accessed data.
</details>

### Q18: Components required to build a site-to-site VPN connection to AWS (Select TWO)

Which components are required to build a site-to-site VPN connection to AWS? (Select TWO.)

- A. An internet gateway
- B. A NAT gateway
- C. A customer gateway
- D. Amazon API Gateway
- E. A virtual private gateway

<details>
<summary>Show answer</summary>

**Answer: C, E**

A virtual private gateway is attached to the VPC on the AWS side to terminate the VPN connection. A customer gateway is the device (physical or software) on the customer's on-premises side that is configured to establish the VPN tunnel. An internet gateway (A) allows general internet traffic into/out of a VPC but is not part of an encrypted VPN connection. A NAT gateway (B) enables private EC2 instances to reach the internet but cannot create a VPN connection. Amazon API Gateway (D) is a managed API service unrelated to VPN connectivity.
</details>

### Q19: Centralized auth, authorization, and user management for a mobile app

A company is developing a new mobile version of its popular web application in the AWS Cloud. The mobile app must be accessible to internal and external users. The mobile app must handle authorization, authentication, and user management from one central source.

Which solution meets these requirements?

- A. IAM roles
- B. IAM users and groups
- C. Amazon Cognito user pools
- D. AWS Security Token Service (AWS STS)

<details>
<summary>Show answer</summary>

**Answer: C**

Amazon Cognito provides authentication, authorization, and user management for web and mobile apps from a single service. Users can sign in directly with a username and password or through a trusted third party. IAM roles (A) define permissions for AWS entities but do not control application-level access. IAM users and groups (B) control access to AWS services, not to applications. AWS STS (D) issues temporary security credentials for AWS resource access but does not manage application user authentication.
</details>

### Q20: Protect CloudFront + ALB + EC2 app against XSS and SQL injection

A company runs its website on Amazon EC2 instances behind an Application Load Balancer that is configured as the origin for an Amazon CloudFront distribution. The company wants to protect against cross-site scripting and SQL injection attacks.

Which approach should a solutions architect recommend to meet these requirements?

- A. Enable AWS Shield Advanced. List the CloudFront distribution as a protected resource.
- B. Define an AWS Shield Advanced policy in AWS Firewall Manager to block cross-site scripting and SQL injection attacks.
- C. Deploy AWS Firewall Manager on the EC2 instances. Create conditions and rules that block cross-site scripting and SQL injection attacks.
- D. Set up AWS WAF on the CloudFront distribution. Use conditions and rules that block cross-site scripting and SQL injection attacks.

<details>
<summary>Show answer</summary>

**Answer: D**

AWS WAF can detect and block SQL injection and cross-site scripting (XSS) attacks using managed rules and custom conditions. Attaching it to the CloudFront distribution inspects traffic at the edge before it reaches the origin. Shield Advanced (A, B) protects against DDoS attacks but does not protect against XSS or SQL injection. Firewall Manager (C) is a managed service for centrally configuring WAF and Shield policies — it is not deployed on EC2 instances.
</details>

### Q21: Modes available in the Storage Gateway appliance (Select THREE)

Which of the following are modes available in the Storage Gateway appliance?

(Select THREE.)

- A. Internet Gateway
- B. Tape Gateway
- C. Volume Gateway
- D. Virtual Gateway
- E. Amazon S3 File Gateway
- F. NAT Gateway

<details>
<summary>Show answer</summary>

**Answer: B, C, E**

AWS Storage Gateway has three modes: **Tape Gateway** (virtual tape library backed by S3/Glacier for backup workflows), **Volume Gateway** (iSCSI block storage volumes backed by S3, in cached or stored mode), and **Amazon S3 File Gateway** (stores files as objects in S3 via NFS/SMB). Internet Gateway (A) and NAT Gateway (F) are VPC networking components, not Storage Gateway modes. Virtual Gateway (D) does not exist as an AWS service.
</details>

### Q22: AWS service to run SQL queries directly on data in S3

Which AWS service lets you run SQL queries directly on data stored in Amazon S3 without loading it into a database?

- A. Amazon Redshift
- B. Amazon Athena
- C. AWS Glue
- D. Amazon QuickSight

<details>
<summary>Show answer</summary>

**Answer: B**

Amazon Athena is a serverless interactive query service that lets you run SQL queries directly on data in S3 with no data loading or ETL required — you pay only per query. Redshift (A) is a data warehouse that requires data to be loaded into it. AWS Glue (C) is an ETL service for data preparation and transformation, not query execution. Amazon QuickSight (D) is a business intelligence and visualization tool, not a query engine for raw S3 data.
</details>

### Q23: Auto Scaling strategy to maintain 75% CPU with unpredictable traffic

You have an application with unpredictable traffic patterns that runs on at least two instances. You want the CPU utilization to stay at about 75 percent. Which Amazon EC2 Auto Scaling strategy should you choose?

- A. Scheduled
- B. Predictive
- C. Dynamic
- D. Manual

<details>
<summary>Show answer</summary>

**Answer: C**

Dynamic scaling adjusts capacity in real time based on current demand using a target tracking policy — you simply set the desired metric (e.g., CPU at 75%) and Auto Scaling continuously adds or removes instances to maintain that target. It is the right choice for unpredictable traffic. Scheduled scaling (A) triggers at predefined times and requires knowing when traffic spikes occur. Predictive scaling (B) uses ML to forecast traffic but requires historical patterns — not suitable for truly unpredictable workloads. Manual scaling (D) requires human intervention and cannot react automatically to traffic changes.
</details>

### Q24: Use cases for AWS CloudTrail data (Select TWO)

Which of the following are use cases for AWS CloudTrail data? (Select TWO.)

- A. Provide real-time observability of AWS resources.
- B. Store log data as a record of account usage.
- C. Log events for a particular service or application.
- D. Capture root login failures.
- E. Collect metric data measuring CPU utilization.

<details>
<summary>Show answer</summary>

**Answer: B, D**

CloudTrail records API calls and account activity across AWS services, providing a history of actions taken — making it the right tool for audit trails (B) and detecting security events like root login failures (D). Real-time observability (A) is provided by Amazon CloudWatch. Application-level event logging (C) is handled by CloudWatch Logs. CPU utilization metrics (E) are collected by CloudWatch Metrics, not CloudTrail.
</details>

### Q25: Make long-running report generation more responsive behind ALB

A web application runs on Amazon EC2 instances behind an Application Load Balancer (ALB). The application allows users to create custom reports of historical weather data. Generating a report can take up to 5 minutes. These long-running requests use many of the available incoming connections, making the system unresponsive to other users.

How can a solutions architect make the system more responsive?

- A. Use Amazon SQS with AWS Lambda to generate reports.
- B. Increase the idle timeout on the ALB to 5 minutes.
- C. Update the client-side application code to increase its request timeout to 5 minutes.
- D. Publish the reports to Amazon S3 and use Amazon CloudFront for downloading to the user.

<details>
<summary>Show answer</summary>

**Answer: A**

Offloading report generation to SQS + Lambda decouples the long-running work from the web tier, freeing EC2 connections for other users. Increasing the ALB idle timeout (B) prevents premature disconnects but doesn't reduce connection consumption. Updating client-side timeouts (C) prevents client-side failures but does nothing to free server resources. Publishing to S3 + CloudFront (D) helps with delivery but doesn't solve the problem of long-running requests consuming connections during generation.
</details>

### Q26: Shared storage for 10–50 EC2 instances reading and writing to the same 50 GB folder

A solutions architect is designing an elastic application that will have between 10 and 50 Amazon EC2 concurrent instances running, depending on the load. Each instance must mount storage that will read and write to the same 50 GB folder.

Which storage type meets the requirements?

- A. Amazon S3
- B. Amazon Elastic File System (Amazon EFS)
- C. Amazon Elastic Block Store (Amazon EBS) volumes
- D. Amazon EC2 instance store

<details>
<summary>Show answer</summary>

**Answer: B**

EFS is a fully managed, shared file system that can be mounted simultaneously by thousands of EC2 instances across multiple AZs using NFS. S3 (A) is object storage and cannot be natively mounted as a file system. EBS (C) volumes attach to a single EC2 instance (Multi-Attach is limited to io1/io2 and up to 16 instances with a cluster-aware file system). Instance store (D) is ephemeral local storage tied to a single instance.
</details>

### Q27: Most secure method for ECS task on EC2 to publish messages to SQS

A company has an application running as a service in Amazon Elastic Container Service (Amazon ECS) using the Amazon EC2 launch type. The application code makes AWS API calls to publish messages to Amazon Simple Queue Service (Amazon SQS).

What is the MOST secure method of giving the application permission to publish messages to Amazon SQS?

- A. Use AWS Identity and Access Management (IAM) to grant SQS permissions to the role used by the launch configuration for the Auto Scaling group of the ECS cluster.
- B. Create a new IAM user with SQS permissions. Then update the task definition to declare the access key ID and secret access key as environment variables.
- C. Create a new IAM role with SQS permissions. Then update the task definition to use this role for the task role setting.
- D. Update the security group used by the ECS cluster to allow access to Amazon SQS.

<details>
<summary>Show answer</summary>

**Answer: C**

ECS task roles (taskRoleArn) provide fine-grained, per-task IAM permissions — only the specific task gets SQS access. Granting permissions via the instance role (A) gives ALL containers on the EC2 instance SQS access, violating least privilege. IAM user credentials as environment variables (B) uses long-term credentials, which is insecure and harder to rotate. Security groups (D) operate at the network layer and cannot control AWS API permissions.
</details>

### Q28: Immediate solution for viral traffic spike on an on-premises media site

A company runs an online media site, hosted on-premises. An employee posted a product review that contained videos and pictures. The review went viral, and the company needs to handle the resulting spike in website traffic.

What action would provide an immediate solution?

- A. Redesign the website to use Amazon API Gateway, and use AWS Lambda to deliver content.
- B. Add server instances using Amazon EC2 and use Amazon Route 53 with a failover routing policy.
- C. Serve the images and videos using an Amazon CloudFront distribution created using the media site as the origin.
- D. Use Amazon ElastiCache for Redis for caching and reducing the load requests from the origin.

<details>
<summary>Show answer</summary>

**Answer: C**

CloudFront can be set up quickly with the existing on-premises site as the origin, immediately caching and distributing static media content (images and videos) at edge locations worldwide. Redesigning with API Gateway + Lambda (A) requires significant development — not immediate. Adding EC2 + Route 53 failover (B) requires provisioning and configuration time. ElastiCache (D) is an in-memory cache within AWS, not designed to front an on-premises website for external users.
</details>

### Q29: Self-managed service for governance evaluations and remediations across AWS Organizations

A company's cloud operations team wants to standardize resource remediation. The company wants to provide a standard set of governance evaluations and remediations to all member accounts in its organization in AWS Organizations.

Which self-managed AWS service can the company use to meet these requirements with the LEAST amount of operational effort?

- A. AWS Security Hub compliance standards
- B. AWS Config conformance packs
- C. AWS CloudTrail
- D. AWS Trusted Advisor

<details>
<summary>Show answer</summary>

**Answer: B**

AWS Config conformance packs are collections of AWS Config rules and remediation actions that you can deploy as a single entity in an account and a Region, or across an entire organization in AWS Organizations. This delivers a standard set of governance evaluations and remediations with the least operational effort.

- **A is wrong:** Security Hub gives a comprehensive view of your security state and checks against security standards, but the scenario requires general governance evaluations, not just security.
- **C is wrong:** CloudTrail records API calls for governance, compliance, and auditing, but does not make recommendations or perform remediation.
- **D is wrong:** Trusted Advisor provides real-time best-practice guidance, but does not provide standardized remediation.
</details>

### Q30: Most secure way for EC2 in private subnet to access SNS with PII data

An application launched on Amazon EC2 instances needs to publish personally identifiable information (PII) about customers using Amazon Simple Notification Service (Amazon SNS). The application is launched in private subnets within an Amazon VPC.

What is the MOST secure way to allow the application to access service endpoints in the same AWS Region?

- A. Use an internet gateway.
- B. Use AWS PrivateLink.
- C. Use a NAT gateway.
- D. Use a proxy instance.

<details>
<summary>Show answer</summary>

**Answer: B**

AWS PrivateLink (VPC interface endpoints) allows private connectivity to AWS services like SNS without leaving the AWS network. Traffic never traverses the public internet, which is critical when handling PII. An internet gateway (A) would require the instances to be in public subnets and expose traffic to the internet. A NAT gateway (C) routes traffic through the public internet, which is less secure for PII. A proxy instance (D) adds operational overhead and still routes traffic through the internet.
</details>

### Q31: Optimize Auto Scaling costs for highly variable demand without impacting performance

A company hosts its website on AWS. To address the highly variable demand, the company has implemented Amazon EC2 Auto Scaling. Management is concerned that the company is over-provisioning its infrastructure, especially at the front end of the three-tier application. A solutions architect needs to ensure costs are optimized without impacting performance.

What should the solutions architect do to accomplish this?

- A. Use Auto Scaling with Reserved Instances.
- B. Use Auto Scaling with a scheduled scaling policy.
- C. Use Auto Scaling with the suspend-resume feature.
- D. Use Auto Scaling with a target tracking scaling policy.

<details>
<summary>Show answer</summary>

**Answer: D**

Target tracking scaling automatically adjusts capacity to maintain a specific metric (e.g., CPU utilization at a target percentage), scaling in when demand drops and scaling out when demand increases. This directly addresses over-provisioning by closely matching capacity to actual demand. Reserved Instances (A) reduce per-instance cost but don't address capacity optimization. Scheduled scaling (B) requires predictable demand patterns and cannot handle highly variable traffic. Suspend-resume (C) pauses scaling actions entirely, which would impact availability.
</details>

### Q32: Improve security for EC2 running website and AD controller, minimize admin overhead

A company is performing an AWS Well-Architected Framework review of an existing workload deployed on AWS. The review identified a public-facing website running on the same Amazon EC2 instance as a Microsoft Active Directory domain controller that was installed recently to support other AWS services. A solutions architect needs to recommend a new design that would improve the security of the architecture and minimize the administrative demand on IT staff.

What should the solutions architect recommend?

- A. Use AWS Managed Microsoft AD to create a managed Active Directory. Uninstall Active Directory on the current EC2 instance.
- B. Create EC2 instances in the same subnet and reinstall Active Directory on a separate EC2 instance. Uninstall Active Directory on the current EC2 instance.
- C. Use AWS Directory Service to create an Active Directory Connector to the Active Directory controller running on the current EC2 instance.
- D. Configure AWS IAM Identity Center (AWS Single Sign-On) with Security Assertion Markup Language (SAML) 2.0 federation with the current Active Directory controller. Modify the EC2 instance's security group to deny public access to Active Directory.

<details>
<summary>Show answer</summary>

**Answer: A**

AWS Managed Microsoft AD fully separates the Active Directory workload from the public-facing website and offloads AD management to AWS, addressing both security (separation of concerns) and administrative overhead (fully managed service). Reinstalling AD on a separate EC2 instance (B) improves security through separation but does not reduce admin effort — AD is still self-managed. An AD Connector (C) requires an existing directory to connect to and doesn't replace the AD on the instance. IAM Identity Center + security group (D) keeps AD on the same instance as the website, which doesn't resolve the fundamental architectural issue.
</details>

### Q33: Most secure way to store an API key for AWS Lambda accessing a third-party service

An application running on AWS Lambda requires an API key to access a third-party service. The key must be stored securely with audited access to the Lambda function only.

What is the MOST secure way to store the key?

- A. As an object in Amazon S3
- B. As a secure string in AWS Systems Manager Parameter Store
- C. Inside a file on an Amazon EBS volume attached to the Lambda function
- D. Inside a secrets file stored on Amazon EFS

<details>
<summary>Show answer</summary>

**Answer: B**

AWS Systems Manager Parameter Store SecureString parameters encrypt values using AWS KMS and integrate with IAM for fine-grained access control. Access to the parameter can be restricted to the Lambda function's execution role and all access is logged via CloudTrail for auditing. S3 (A) can store encrypted objects but lacks the native integration and access auditing purpose-built for secrets. EBS volumes (C) cannot be attached to Lambda functions. EFS (D) can be mounted by Lambda but storing secrets as files lacks built-in encryption management and fine-grained auditing.
</details>

### Q34: SQS configuration to prioritize paid users over free users for photo processing

An online photo application lets users upload photos and perform image editing operations. The application is built on Amazon EC2 instances and offers two classes of service: free and paid. Photos submitted by paid users are processed before those submitted by free users. Photos are uploaded to Amazon S3 and the job information is sent to Amazon SQS.

Which configuration should a solutions architect recommend?

- A. Use one SQS FIFO queue. Assign a higher priority to the photos from paid users so they are processed first.
- B. Use two SQS FIFO queues: one for paid users and one for free users. Set the free queue to use short polling and the paid queue to use long polling.
- C. Use two SQS standard queues: one for paid users and one for free users. Configure the application on the Amazon EC2 instances to prioritize polling for the paid queue over the free queue.
- D. Use one SQS standard queue. Set the visibility timeout of the photos from paid users to zero. Configure the application on the Amazon EC2 instances to prioritize visibility settings so photos from paid users are processed first.

<details>
<summary>Show answer</summary>

**Answer: C**

Two separate queues are necessary for free and paid requests, and the prioritization happens in the application running on the EC2 instances — it polls the paid queue ahead of the free queue. Standard queues are the right fit here since the requirement is prioritization, not strict ordering.

- **A is wrong:** Two separate queues are needed to differentiate paid from free requests; prioritization must occur in the application, not in a single FIFO queue.
- **B is wrong:** Although two queues are correct, polling type (short vs long polling) is not the mechanism for prioritization in this scenario.
- **D is wrong:** Two separate queues are needed for free and paid requests; visibility timeout is unrelated to queue prioritization.
</details>

### Q35: Document storage on EC2 across multi-AZ with immediate retrieval

A company is building a document storage application on AWS. The application runs on Amazon EC2 instances in multiple Availability Zones. The company requires the document store to be highly available. The documents need to be available to all EC2 instances hosting the application and returned immediately when requested multiple times per month. The lead engineer has configured the application to use Amazon Elastic Block Store (Amazon EBS) to store the documents, but is willing to consider other options to meet the availability requirements.

What should a solutions architect recommend?

- A. Snapshot the EBS volumes regularly and build new volumes from those snapshots in additional Availability Zones.
- B. Use Amazon EBS for the EC2 instance root volumes. Configure the application to build the document store on Amazon S3.
- C. Use Amazon EBS for the EC2 instance root volumes. Configure the application to build the document store on Amazon S3 Glacier Flexible Retrieval.
- D. Use at least three Provisioned IOPS EBS volumes for EC2 instances. Mount the volumes to the EC2 instances in a RAID 1 configuration.

<details>
<summary>Show answer</summary>

**Answer: B**

Amazon S3 provides 99.999999999% (11 nines) durability, is accessible from any AZ or Region, and returns objects immediately — meeting all requirements. EBS snapshots (A) add complexity and lag since volumes must be rebuilt in each AZ. S3 Glacier Flexible Retrieval (C) has retrieval times of minutes to hours, not immediate. RAID 1 with EBS (D) provides redundancy within a single instance but does not make documents available across AZs.
</details>

### Q36: Most cost-effective way to improve UDP audio streaming performance for North American users

A company has a well-architected application that streams audio data by using UDP in the AWS Cloud. The company hosts the application in the eu-central-1 Region. The company plans to offer services to North American users. A solutions architect must improve application network performance for the North American users.

Which of the following is the MOST cost-effective solution?

- A. Create an AWS Global Accelerator standard accelerator with an endpoint group in eu-central-1.
- B. Use AWS CloudFormation to deploy additional application infrastructure in the us-east-1 Region and the us-west-1 Region.
- C. Create an Amazon CloudFront distribution and use the North America (United States, Mexico, Canada) and Europe and Israel price classes.
- D. Configure the application to use an Amazon Route 53 latency-based routing policy.

<details>
<summary>Show answer</summary>

**Answer: A**

AWS Global Accelerator routes UDP and TCP traffic through the AWS global backbone network, providing consistent low-latency performance without duplicating infrastructure. It is the most cost-effective because only an accelerator layer is added. Deploying in US regions (B) requires full infrastructure duplication — significantly more expensive. CloudFront (C) does not support UDP traffic. Route 53 latency-based routing (D) directs users to the nearest endpoint, but with only one endpoint in eu-central-1, it provides no benefit.
</details>

### Q37: Auto Scaling default termination policy behavior during scale-in across two AZs

An environment has an Auto Scaling group across two Availability Zones referred to as AZ-a and AZ-b. AZ-a has four Amazon EC2 instances, and AZ-b has three EC2 instances. The Auto Scaling group uses a default termination policy. None of the instances are protected from a scale-in event.

How will Auto Scaling proceed if there is a scale-in event?

- A. Auto Scaling selects an instance to terminate randomly.
- B. Auto Scaling terminates the instance with the oldest launch configuration of all instances.
- C. Auto Scaling selects the Availability Zone with four EC2 instances, and then continues to evaluate.
- D. Auto Scaling terminates the instance with the closest next billing hour of all instances.

<details>
<summary>Show answer</summary>

**Answer: C**

The default termination policy first selects the AZ with the most instances to maintain balance across AZs (AZ-a with 4 instances). Then within that AZ, it evaluates further: selecting the instance with the oldest launch configuration/template, then the one closest to the next billing hour, and finally randomly if still tied. It does not select randomly across all AZs (A), nor does it skip the AZ-balancing step to evaluate launch configuration globally (B) or billing hour globally (D).
</details>

### Q38: Fix S3 bucket policy error missing required element

An administrator wants to apply a resource-based policy to the S3 bucket named "iam-policy-testbucket" to restrict access and to allow accounts to only write objects to the bucket. When the administrator tries to apply the following policy to the "iam-policy-testbucket" bucket, the S3 bucket presents an error.

`{ "Version": "2012-10-17", "Id": "Policy1646946718956", "Statement": [ { "Sid": "Stmt1646946717210", "Effect": "Allow", "Action": "s3:PutObject", "Resource": "arn:aws:s3:::iam-policy-testbucket/*" } ] }`

How can the administrator correct the policy to resolve the error and successfully apply the policy?

- A. Change the Action element from s3:PutObject to s3:*.
- B. Remove the Resource element because it is unnecessary for resource-based policies.
- C. Change the Resource element to NotResource.
- D. Add a Principal element to the policy to declare which accounts have access.

<details>
<summary>Show answer</summary>

**Answer: D**

Resource-based policies (such as S3 bucket policies) require a Principal element to specify who is granted or denied access. Without it, the policy is invalid and S3 rejects it. Changing the Action to s3:* (A) broadens permissions but doesn't fix the missing Principal. Removing the Resource element (B) is incorrect — Resource is required in bucket policies. Changing to NotResource (C) inverts the resource scope but doesn't address the missing Principal.
</details>

### Q39: Collect 50,000 page clicks per second and process sequentially per user

A company's website receives 50,000 requests each second. The company wants to use multiple applications to analyze the navigation patterns of the website users so that the experience can be personalized.

Which AWS service or feature should a solutions architect use to collect page clicks for the website and process them sequentially for each user?

- A. Amazon Kinesis Data Streams
- B. Amazon Simple Queue Service (Amazon SQS) standard queue
- C. Amazon Simple Queue Service (Amazon SQS) FIFO queue
- D. AWS CloudTrail

<details>
<summary>Show answer</summary>

**Answer: A**

Kinesis Data Streams handles massive throughput (tens of thousands of records per second) and uses partition keys to guarantee sequential processing within each shard — ideal for per-user ordering. SQS standard queues (B) do not guarantee message ordering. SQS FIFO queues (C) guarantee ordering but are limited to 300 transactions per second (3,000 with batching), far below the 50,000 requirement. CloudTrail (D) logs AWS API calls, not website clickstream data.
</details>

### Q40: Prevent direct linking to S3 bucket assets for a hosted website

A company is designing a website that will be hosted on Amazon S3.

How should users be prevented from linking directly to the assets in the S3 bucket?

- A. Create a static website, then update the bucket policy to require users to access the resources with the static website URL.
- B. Create an Amazon CloudFront distribution with an origin access control (OAC) and update the bucket policy to grant permission to the OAC only.
- C. Create a static website, then configure an Amazon Route 53 record set with an alias pointing to the static website. Provide this URL to users.
- D. Create an Amazon CloudFront distribution with an AWS WAF web ACL that permits access to the origin server through the distribution only.

<details>
<summary>Show answer</summary>

**Answer: B**

CloudFront with an origin access control (OAC) restricts S3 bucket access so that only the CloudFront distribution can read objects. The bucket policy explicitly denies all access except from the OAC, preventing users from bypassing CloudFront with direct S3 URLs. Updating the bucket policy for the static website URL (A) does not prevent direct S3 object access. A Route 53 alias (C) provides a friendly URL but doesn't restrict direct bucket access. A WAF web ACL (D) filters incoming requests at the CloudFront level but does not prevent direct S3 URL access.
</details>

### Q41: Private subnet access to DynamoDB without leaving the AWS network

An application running in a private subnet accesses an Amazon DynamoDB table. The data cannot leave the AWS network to meet security requirements.

How should this requirement be met?

- A. Configure a network ACL on DynamoDB to limit traffic to the private subnet.
- B. Enable DynamoDB encryption at rest using an AWS Key Management Service (AWS KMS) key.
- C. Add a NAT gateway and configure the route table on the private subnet.
- D. Create a VPC endpoint for DynamoDB and configure the endpoint policy.

<details>
<summary>Show answer</summary>

**Answer: D**

A VPC gateway endpoint for DynamoDB keeps all traffic between the private subnet and DynamoDB within the AWS network — no internet traversal. Network ACLs (A) cannot be applied directly to DynamoDB, which is a managed service outside the VPC. Encryption at rest (B) protects stored data but does not control network routing. A NAT gateway (C) routes traffic through the public internet, which violates the requirement that data cannot leave the AWS network.
</details>

### Q42: Private connectivity to AWS services without public internet, most operationally efficient

A solutions architect is designing a secure cloud-based application that uses Amazon EC2 instances within a VPC. The application uses other supported AWS services within the same Region. The network traffic between the instances and AWS services must remain private and must not travel across the public internet.

Which service or resource will meet the security requirement with the MOST operational efficiency?

- A. Internet gateway
- B. NAT gateway
- C. VPC endpoint
- D. AWS Direct Connect

<details>
<summary>Show answer</summary>

**Answer: C**

VPC endpoints (gateway and interface) provide private connectivity between a VPC and supported AWS services entirely within the AWS network, with minimal setup and no infrastructure to manage. An internet gateway (A) enables public internet access, violating the private traffic requirement. A NAT gateway (B) routes traffic through the public internet. AWS Direct Connect (D) provides dedicated private connectivity from on-premises to AWS but is unnecessary and expensive for VPC-to-service communication within the same Region.
</details>

### Q43: Backup website with contact info when primary EC2 site is down

A company runs a website on Amazon EC2 instances behind an ELB Application Load Balancer. Amazon Route 53 is used for the DNS. The company wants to set up a backup website with a message including a phone number and email address that users can reach if the primary website is down.

How should the company deploy this solution?

- A. Use Amazon S3 website hosting for the backup website and a Route 53 failover routing policy.
- B. Use Amazon S3 website hosting for the backup website and a Route 53 latency routing policy.
- C. Deploy the application in another AWS Region and use ELB health checks for failover routing.
- D. Deploy the application in another AWS Region and use server-side redirection on the primary website.

<details>
<summary>Show answer</summary>

**Answer: A**

S3 static website hosting is ideal for a simple backup page (static HTML with contact info) — it's highly available, low cost, and requires no servers. Route 53 failover routing automatically directs traffic to the S3 backup when the primary ALB health check fails. Latency routing (B) routes based on lowest latency, not health status — it won't failover when the primary is down. Deploying a full application in another Region (C) is overkill for a simple static message page. Server-side redirection (D) requires the primary website to be running, which defeats the purpose of a backup when it's down.
</details>

### Q44: SQS dead-letter queue for failed microservice messages

A solutions architect is redesigning a monolithic application to be a loosely coupled application composed of two microservices: Microservice A and Microservice B.

Microservice A places messages in a main Amazon Simple Queue Service (Amazon SQS) queue for Microservice B to consume. When Microservice B fails to process a message after four retries, the message needs to be removed from the queue and stored for further investigation.

What should the solutions architect do to meet these requirements?

- A. Create an SQS dead-letter queue. Microservice B adds failed messages to that queue after it receives and fails to process the message four times.
- B. Create an SQS dead-letter queue. Configure the dead-letter queue to deliver messages to the dead-letter queue after the message has been received four times.
- C. Create an SQS queue for failed messages. Microservice A adds failed messages to that queue after Microservice B receives and fails to process the message four times.
- D. Create an SQS queue for failed messages. Configure the SQS queue for failed messages to pull messages from the main SQS queue after the original message has been received four times.

<details>
<summary>Show answer</summary>

**Answer: B**

An SQS dead-letter queue (DLQ) with a redrive policy configured on the main queue automatically moves messages that exceed the maxReceiveCount (set to 4) to the DLQ. This is a native SQS feature that requires no application code changes. Option A puts the burden on Microservice B to manually route messages, adding complexity. Options C and D describe non-standard patterns — SQS queues don't pull from other queues, and having Microservice A track Microservice B's failures breaks the decoupled architecture.
</details>

### Q45: Storage for high random IOPS with consistent performance and persistence

A solutions architect is designing a database solution that must support a high rate of random disk reads and writes. It must provide consistent performance, and requires long-term persistence.

Which storage solution meets these requirements?

- A. An Amazon Elastic Block Store (Amazon EBS) Provisioned IOPS volume
- B. An Amazon Elastic Block Store (Amazon EBS) General Purpose volume
- C. An Amazon Elastic Block Store (Amazon EBS) magnetic volume
- D. An Amazon EC2 instance store

<details>
<summary>Show answer</summary>

**Answer: A**

EBS Provisioned IOPS (io1/io2) volumes are designed for I/O-intensive workloads that require consistently high random read/write performance, making them ideal for databases. General Purpose (gp2/gp3) volumes (B) provide burstable performance but cannot guarantee sustained high IOPS. Magnetic volumes (C) offer the lowest performance tier and are not suitable for high IOPS workloads. EC2 instance store (D) provides high I/O but is ephemeral — data is lost when the instance stops or terminates, failing the persistence requirement.
</details>

### Q46: Caching strategy that ensures cache is never stale on writes

A company has a three-tier architecture solution in which an application writes to a relational database. Because of frequent requests, the company wants to cache data whenever the application writes data to the database. The company's priority is to minimize latency for data retrieval and to ensure that data in the cache is never stale.

Which caching strategy should the company use to meet these requirements?

- A. Amazon ElastiCache with write-through
- B. Amazon DynamoDB Accelerator (DAX)
- C. Amazon ElastiCache with lazy loading
- D. Amazon Simple Queue Service (Amazon SQS)

<details>
<summary>Show answer</summary>

**Answer: A**

Write-through caching updates the cache simultaneously whenever data is written to the database, ensuring the cache is always in sync and never stale. This directly meets the requirement. Lazy loading (C) only populates the cache on read misses, meaning stale data can be served until the cache entry expires — violating the "never stale" requirement. DAX (B) is a caching layer specifically for DynamoDB, not relational databases. SQS (D) is a messaging service, not a caching solution.
</details>

### Q47: Waiting list service that preserves customer order

A restaurant reservation application needs to access a waiting list. When a customer tries to reserve a table, and none are available, the customer application will put the user on the waiting list, and the application will notify the customer when a table becomes free. The waiting list must preserve the order in which customers were added to the waiting list.

Which service should the solutions architect recommend to store this waiting list?

- A. Amazon Simple Notification Service (Amazon SNS)
- B. AWS Step Functions invoking AWS Lambda functions
- C. A FIFO queue in Amazon Simple Queue Service (Amazon SQS)
- D. A standard queue in Amazon Simple Queue Service (Amazon SQS)

<details>
<summary>Show answer</summary>

**Answer: C**

An SQS FIFO (First-In-First-Out) queue guarantees that messages are processed in exactly the order they were sent, which is essential for a waiting list where position order matters. A standard SQS queue (D) provides best-effort ordering but does not guarantee strict order, so customers could be served out of turn. SNS (A) is a pub/sub notification service, not a queue for ordered message storage. Step Functions (B) is a workflow orchestration service, not designed for ordered queue management.
</details>

### Q48: Resources that still generate costs when EC2 instances are stopped (Select TWO)

A company stops a cluster of Amazon EC2 instances over a weekend. The costs decrease, but they do not drop to zero.

Which resources could still be generating costs? (Select TWO.)

- A. Elastic IP addresses
- B. Data transfer out
- C. Regional data transfers
- D. Amazon Elastic Block Store (Amazon EBS) volumes
- E. AWS Auto Scaling

<details>
<summary>Show answer</summary>

**Answer: A, D**

Elastic IP addresses (A) incur charges when they are allocated but not associated with a running instance — stopped instances count as unassociated. EBS volumes (D) continue to incur storage charges regardless of whether the attached EC2 instance is running or stopped. Data transfer out (B) and regional data transfers (C) only occur when instances are actively sending data, which doesn't happen when they're stopped. Auto Scaling (E) is a free service — you only pay for the underlying resources it manages.
</details>

### Q49: Secure file downloads reducing web server load, users access only their own files

An application provides a feature that allows users to securely download private and personal files. The web server is currently overwhelmed with serving files for download. A solutions architect must find a more effective solution to reduce the web server load and cost, and must allow users to download only their own files.

Which solution meets all requirements?

- A. Store the files securely on Amazon S3 and have the application generate an Amazon S3 presigned URL for the user to download.
- B. Store the files in an encrypted Amazon Elastic Block Store (Amazon EBS) volume, and use a separate set of servers to serve the downloads.
- C. Have the application encrypt the files and store them in the local Amazon EC2 instance store prior to serving them up for download.
- D. Create an Amazon CloudFront distribution to distribute and cache the files.

<details>
<summary>Show answer</summary>

**Answer: A**

S3 presigned URLs offload file serving from the web server to S3 (reducing load and cost) while ensuring security — each URL is time-limited and scoped to a specific object, so users can only download their own files. EBS with separate servers (B) doesn't reduce infrastructure burden — it just shifts it. EC2 instance store (C) is ephemeral and doesn't reduce web server load. CloudFront (D) caches and distributes content but doesn't inherently restrict users to only their own files without additional authorization logic.
</details>

### Q50: Resolving EC2 QUOTA EXCEEDED error with Auto Scaling

A company has decided to use AWS to achieve high availability. The company's architecture consists of an Application Load Balancer in front of an Auto Scaling group that consists of Amazon EC2 instances. The company uses Amazon CloudWatch metrics and alarms to monitor the architecture. A solutions architect notices that the company is not able to launch some instances. The solutions architect finds the following message: EC2 QUOTA EXCEEDED.

How can the solutions architect ensure that the company is able to launch all the EC2 instances correctly?

- A. Modify the Auto Scaling group to raise the maximum number of instances that the company can launch.
- B. Use Service Quotas to request an increase to the number of EC2 instances that the company can launch.
- C. Recreate the Auto Scaling group to ensure the Auto Scaling group is connected to the Application Load Balancer.
- D. Modify the CloudWatch metric that the company monitors to launch the instances.

<details>
<summary>Show answer</summary>

**Answer: B**

The "EC2 QUOTA EXCEEDED" error indicates the AWS account has hit its service quota (limit) for the number of EC2 instances in the Region. The fix is to request a quota increase through AWS Service Quotas. Modifying the Auto Scaling group max (A) changes the desired capacity ceiling but doesn't address the account-level quota. Recreating the Auto Scaling group (C) doesn't affect service quotas. Modifying CloudWatch metrics (D) changes monitoring, not instance launch capacity.
</details>

### Q51: Improving Athena query performance on large CSV dataset (Select TWO)

A company wants to measure the effectiveness of its recent marketing campaigns. The company performs batch processing on .csv files of sales data and stores the results in an Amazon S3 bucket once every hour. The S3 bucket contains petabytes of objects. The company runs one-time queries in Amazon Athena to determine which products are most popular on a particular date for a particular region. Queries sometimes fail or take longer than expected to finish running.

Which actions should a solutions architect take to improve the query performance and reliability? (Select TWO.)

- A. Reduce the S3 object sizes to less than 128 MB.
- B. Partition the data by date and region in Amazon S3.
- C. Store the files as large, single objects in Amazon S3.
- D. Use Amazon Kinesis Data Analytics to run the queries as part of the batch processing operation.
- E. Use an AWS Glue extract, transform, and load (ETL) process to convert the .csv files into Apache Parquet format.

<details>
<summary>Show answer</summary>

**Answer: B, E**

Partitioning data by date and region (B) allows Athena to scan only the relevant subset of data instead of the entire petabyte-scale dataset, drastically reducing query time and cost. Converting CSV to Apache Parquet (E) provides columnar storage with compression, which Athena reads far more efficiently than row-based CSV — reducing data scanned and improving performance. Reducing object sizes (A) doesn't address the core issue of scanning too much data. Storing as large single objects (C) would actually worsen performance. Kinesis Data Analytics (D) is for real-time streaming analytics, not one-time batch queries.
</details>

### Q52: Decoupling click-tracking infrastructure for fastest SQL access to new data

A media company has an application that tracks user clicks on its websites and performs analytics to provide near-real-time recommendations. The application has a fleet of Amazon EC2 instances that receive data from the websites and send the data to an Amazon RDS DB instance for long-term retention. Another fleet of EC2 instances handles the portion of the application that is continuously checking changes in the database and running SQL queries to provide recommendations.

Management has requested a redesign to decouple the infrastructure. The solution must ensure that data analysts are writing SQL to analyze the new data only. No data can be lost during the deployment.

What should a solutions architect recommend to meet these requirements and to provide the FASTEST access to the user activity?

- A. Use Amazon Kinesis Data Streams to capture the data from the websites, Kinesis Data Firehose to persist the data on Amazon S3, and Amazon Athena to query the data.
- B. Use Amazon Kinesis Data Streams to capture the data from the websites, Kinesis Data Analytics to query the data, and Kinesis Data Firehose to persist the data on Amazon S3.
- C. Use Amazon Simple Queue Service (Amazon SQS) to capture the data from the websites, keep the fleet of EC2 instances, and change to a bigger instance type in the Auto Scaling group configuration.
- D. Use Amazon Simple Notification Service (Amazon SNS) to receive data from the websites and proxy the messages to AWS Lambda functions that run the queries and persist the data. Change Amazon RDS to Amazon Aurora Serverless to persist the data.

<details>
<summary>Show answer</summary>

**Answer: B**

Kinesis Data Streams captures click data in real time with no data loss. Kinesis Data Analytics allows running SQL queries directly on the streaming data, providing the fastest access to new user activity — analysts see data as it arrives rather than waiting for it to land in storage. Kinesis Data Firehose handles persistence to S3 for long-term storage. Option A uses Athena, which queries data at rest in S3 — slower since data must first be delivered and stored. Option C doesn't decouple the architecture. Option D introduces unnecessary complexity with SNS and Lambda for what is fundamentally a streaming analytics use case.
</details>

### Q53: Ensuring high availability for S3-Lambda-DynamoDB-RDS application

A team has an application that detects when new objects are uploaded into an Amazon S3 bucket. The uploads invoke an AWS Lambda function to write object metadata into an Amazon DynamoDB table and an Amazon RDS for PostgreSQL database.

Which action should the team take to ensure high availability?

- A. Enable Cross-Region Replication in the S3 bucket.
- B. Create a Lambda function for each Availability Zone the application is deployed in.
- C. Enable Multi-AZ on the RDS for PostgreSQL database.
- D. Create a DynamoDB stream for the DynamoDB table.

<details>
<summary>Show answer</summary>

**Answer: C**

RDS for PostgreSQL is the only component in this architecture that is not highly available by default. S3 is inherently highly available (99.99%), Lambda automatically runs across multiple AZs, and DynamoDB replicates across three AZs by default. Enabling Multi-AZ on RDS creates a standby replica in a different AZ with automatic failover, eliminating the single point of failure. Cross-Region Replication (A) addresses disaster recovery, not in-Region high availability. Creating per-AZ Lambda functions (B) is unnecessary since Lambda is already multi-AZ. DynamoDB Streams (D) enables change data capture but doesn't improve availability.
</details>

### Q54: Audio version of product manual with custom pronunciations, least operational overhead

A company wants to create an audio version of its product manual. The product manual contains custom product names and abbreviations. The product manual is divided into sections.

Which solution will meet these requirements with the LEAST operational overhead?

- A. Use Amazon Polly. Build custom lexicons for the product names and abbreviations. Use the StartSpeechSynthesisTask API operation for each section of the product manual.
- B. Use Amazon Polly. Build custom Speech Synthesis Markup Language (SSML) for the product names and abbreviations. Use the StartDocumentTextDetection API operation for each section of the product manual.
- C. Use Amazon Textract. Build custom Speech Synthesis Markup Language (SSML) for the product names and abbreviations. Use the StartDocumentTextDetection API operation for each section of the product manual.
- D. Use Amazon Textract. Build custom lexicons for the product names. Use the StartTranscriptionJob API operation for each section of the product manual.

<details>
<summary>Show answer</summary>

**Answer: A**

Amazon Polly is the AWS text-to-speech service, and custom lexicons allow you to define how custom product names and abbreviations are pronounced. StartSpeechSynthesisTask is the correct Polly API for generating speech output. Option B incorrectly pairs Polly with StartDocumentTextDetection, which is an Amazon Textract API for extracting text from images. Option C uses Textract, which extracts text from documents — not a speech service. Option D uses Textract with StartTranscriptionJob, which belongs to Amazon Transcribe (speech-to-text), the opposite direction.
</details>

### Q55: Package tracking with variable traffic, searchable by multiple IDs, auto-expire old data

A company is creating a three-tier web application consisting of a web server, an application server, and a database server. The application will track GPS coordinates of packages as they are being delivered. The application will update the database every 0.5 seconds.

The tracking will need to be read as fast as possible for users to check the status of their packages. Only a few packages might be tracked on some days, whereas millions of packages might be tracked on other days. Tracking will need to be searchable by tracking ID, customer ID, and order ID. Orders older than 1 month no longer need to be tracked.

What should a solutions architect recommend to accomplish this with minimal total cost of ownership?

- A. Use Amazon DynamoDB. Activate Auto Scaling for the DynamoDB table. Schedule an automatic deletion script for items older than 1 month.
- B. Use Amazon DynamoDB with global secondary indexes. Activate Auto Scaling for the DynamoDB table and the global secondary indexes. Turn on TTL for the DynamoDB table.
- C. Use an Amazon RDS On-Demand Instance with Provisioned IOPS (PIOPS). Configure Amazon CloudWatch alarms to send notifications when PIOPS are exceeded. Increase and decrease PIOPS as needed.
- D. Use an Amazon RDS Reserved Instance with Provisioned IOPS (PIOPS). Configure Amazon CloudWatch alarms to send notifications when PIOPS are exceeded. Increase and decrease PIOPS as needed.

<details>
<summary>Show answer</summary>

**Answer: B**

DynamoDB with Auto Scaling handles the highly variable traffic pattern (few to millions of packages) cost-effectively by scaling capacity up and down automatically. Global secondary indexes enable efficient searching by tracking ID, customer ID, and order ID. TTL (Time to Live) automatically deletes items older than 1 month with no additional cost or operational overhead — unlike option A which requires maintaining a separate deletion script. RDS options (C, D) are more expensive, don't scale as elastically for variable workloads, and require more operational management.
</details>

### Q56: Multi-Region disaster recovery with 30-minute RTO on a budget

A solutions architect is responsible for a new highly available three-tier architecture on AWS. An Application Load Balancer distributes traffic to two different Availability Zones with an auto scaling group that consists of Amazon EC2 instances and a Multi-AZ Amazon RDS DB instance. The solutions architect must recommend a multi-Region recovery plan with a recovery time objective (RTO) of 30 minutes. Because of budget constraints, the solutions architect cannot recommend a plan that replicates the entire architecture. The recovery plan should not use the secondary Region unless necessary.

Which disaster recovery strategy will meet these requirements?

- A. Backup and restore
- B. Multi-site active/active
- C. Pilot light
- D. Warm standby

<details>
<summary>Show answer</summary>

**Answer: C**

Pilot light keeps only the most critical core elements running in the secondary Region (such as a cross-Region RDS read replica), while compute resources remain off until needed. This meets the 30-minute RTO — when disaster strikes, the read replica is promoted and compute resources are launched from pre-configured AMIs and templates. It satisfies the budget constraint since only the database replica incurs ongoing cost, not the full architecture. Backup and restore (A) has an RTO of hours, too slow. Multi-site active/active (B) replicates the entire architecture in both Regions, violating the budget constraint. Warm standby (D) runs a scaled-down but fully functional copy, which is more expensive than pilot light and closer to replicating the full architecture.
</details>

### Q57: Reducing EBS io2 costs without downtime (Select TWO)

Cost Explorer is showing charges higher than expected for Amazon Elastic Block Store (Amazon EBS) volumes connected to application servers in a production account. A significant portion of the charges from Amazon EBS are from volumes that were created as Provisioned IOPS SSD (io2) volume types. Controlling costs is the highest priority for this application.

Which steps should the user take to analyze and reduce the EBS costs without incurring any application downtime? (Select TWO.)

- A. Use the Amazon EC2 ModifyInstanceAttribute action to enable EBS optimization on the application server instances.
- B. Use the Amazon CloudWatch GetMetricData action to evaluate the read/write operations and read/write bytes of each volume.
- C. Use the Amazon EC2 ModifyVolume action to reduce the size of the underutilized io2 volumes.
- D. Use the Amazon EC2 ModifyVolume action to change the volume type of the underutilized io2 volumes to General Purpose SSD (gp3).
- E. Use an Amazon S3 PutBucketPolicy action to migrate existing volume snapshots to Amazon S3 Glacier Flexible Retrieval.

<details>
<summary>Show answer</summary>

**Answer: B, D**

First, you need to analyze actual volume usage with CloudWatch metrics (B) to identify which io2 volumes are underutilized — you can't optimize what you haven't measured. Then, for underutilized volumes, use ModifyVolume (D) to change them from expensive io2 to cost-effective gp3, which can be done live without downtime. Enabling EBS optimization (A) doesn't reduce costs — it improves network throughput to EBS. Reducing volume size (C) is not supported by ModifyVolume (you can only increase size). Migrating snapshots via S3 PutBucketPolicy (E) is not a valid operation — EBS snapshots are managed by AWS and cannot be moved to Glacier through S3 bucket policies.
</details>

### Q58: Most secure way to grant Lambda access to DynamoDB

A solutions architect is designing a new workload in which an AWS Lambda function will access an Amazon DynamoDB table.

What is the MOST secure means of granting the Lambda function access to the DynamoDB table?

- A. Create an IAM role with the necessary permissions to access the DynamoDB table. Assign the role to the Lambda function.
- B. Create a DynamoDB user name and password and give them to the developer to use in the Lambda function.
- C. Create an IAM user, and create access and secret keys for the user. Give the user the necessary permissions to access the DynamoDB table. Have the developer use these keys to access the resources.
- D. Create an IAM role allowing access from AWS Lambda. Assign the role to the DynamoDB table.

<details>
<summary>Show answer</summary>

**Answer: A**

IAM roles are the most secure way to grant Lambda access to AWS resources. The Lambda service automatically assumes the execution role and receives temporary credentials — no long-term secrets to manage or risk leaking. DynamoDB doesn't have its own user/password system (B), so that option is invalid. IAM users with access keys (C) require embedding long-term credentials in code, which is a security anti-pattern. Option D incorrectly assigns the role to the DynamoDB table — roles are attached to the compute resource (Lambda), not to the data resource.
</details>

### Q59: Mobile app with MFA, minimal build time and maintenance

A company designs a mobile app for its customers to upload photos to a website. The app needs a secure login with multi-factor authentication (MFA). The company wants to limit the initial build time and the maintenance of the solution.

Which solution should a solutions architect recommend to meet these requirements?

- A. Use Amazon Cognito Identity with SMS-based MFA.
- B. Edit IAM policies to require MFA for all users.
- C. Federate IAM against the corporate Active Directory that requires MFA.
- D. Use Amazon API Gateway and require server-side encryption (SSE) for photos.

<details>
<summary>Show answer</summary>

**Answer: A**

Amazon Cognito provides fully managed user authentication for mobile and web apps with built-in MFA support (SMS, TOTP). It minimizes build time since authentication, user management, and MFA are all handled by the service — no custom auth infrastructure needed. IAM MFA (B) is designed for AWS console and API access, not for mobile app end users. Federating with Active Directory (C) adds complexity and is designed for enterprise/corporate users, not consumer-facing mobile apps. API Gateway with SSE (D) addresses data encryption, not user authentication or MFA.
</details>

### Q60: Database for gaming app with unstructured data, millions of users, least operational support

A database architect is designing an online gaming application that uses a simple, unstructured data format. The database must have the ability to store user information and to track the progress of each user. The database must have the ability to scale to millions of users throughout the week.

Which database service will meet these requirements with the LEAST amount of operational support?

- A. Amazon RDS Multi-AZ
- B. Amazon Neptune
- C. Amazon DynamoDB
- D. Amazon Aurora

<details>
<summary>Show answer</summary>

**Answer: C**

DynamoDB is a fully managed NoSQL database that handles unstructured/semi-structured data natively, scales seamlessly to millions of users with on-demand capacity, and requires virtually no operational support — no patching, no provisioning, no cluster management. RDS Multi-AZ (A) and Aurora (D) are relational databases that require schema design and more operational management, and are better suited for structured data with complex relationships. Neptune (B) is a graph database optimized for highly connected datasets, which is overkill for simple user progress tracking.
</details>

### Q61: Shared storage for ECS tasks across AZs with highest throughput

A solutions architect is designing a solution that involves orchestrating a series of Amazon Elastic Container Service (Amazon ECS) tasks. The tasks run on an ECS cluster that uses Amazon EC2 instances across multiple Availability Zones. The output and state data for all tasks must be stored. The amount of data that each task outputs is approximately 10 MB, and hundreds of tasks can run at a time. As old outputs are archived and deleted, the storage size will not exceed 1 TB.

Which storage solution should the solutions architect recommend to meet these requirements with the HIGHEST throughput?

- A. An Amazon DynamoDB table that is accessible by all ECS cluster instances
- B. An Amazon Elastic Block Store (Amazon EBS) volume that is mounted to the ECS cluster instances
- C. An Amazon Elastic File System (Amazon EFS) file system with Bursting Throughput mode
- D. An Amazon Elastic File System (Amazon EFS) file system with Provisioned Throughput mode

<details>
<summary>Show answer</summary>

**Answer: D**

EFS with Provisioned Throughput mode allows you to specify throughput independent of storage size, guaranteeing high throughput regardless of how much data is stored. With Bursting Throughput mode (C), throughput scales with storage size — at 1 TB, baseline throughput is only ~50 MiB/s, which may not be sufficient for hundreds of concurrent tasks. EBS (B) cannot be shared across instances in different AZs — each volume attaches to a single instance. DynamoDB (A) is a database service, not optimized for storing 10 MB file outputs from tasks.
</details>

### Q62: Highly resilient AWS Direct Connect configuration

A solutions architect is designing a hybrid application using the AWS Cloud. The network between the on-premises data center and AWS will use an AWS Direct Connect (DX) connection. The application connectivity between AWS and the on-premises data center must be highly resilient.

Which DX configuration should be implemented to meet these requirements?

- A. Configure a DX connection with a VPN on top of it.
- B. Configure a DX connection using the most reliable DX partner.
- C. Configure multiple virtual interfaces on top of a DX connection.
- D. Configure DX connections at multiple DX locations.

<details>
<summary>Show answer</summary>

**Answer: D**

Multiple DX connections at different physical DX locations provides true high resiliency by eliminating single points of failure — if one location experiences an outage, traffic fails over to the other. A VPN on top of DX (A) adds encryption but doesn't improve resiliency since it still depends on a single DX connection. Using a reliable DX partner (B) is still a single connection and single point of failure. Multiple virtual interfaces on one DX connection (C) share the same physical link, so a failure of that link takes down all VIFs.
</details>

### Q63: Decoupling food ordering app and making it scalable

A company built a food ordering application that captures user data and stores it for future analysis. The application's static front-end is deployed on an Amazon EC2 instance. The front-end application sends the requests to the backend application running on a separate EC2 instance. The backend application then stores the data in Amazon RDS.

What should a solutions architect do to decouple the architecture and make it scalable?

- A. Use Amazon S3 to serve the static front-end application, which sends requests to Amazon EC2 to run the backend application. The backend application will process and store the data in Amazon RDS.
- B. Use Amazon S3 to serve the static front-end application and write requests to an Amazon Simple Notification Service (Amazon SNS) topic. Subscribe the backend Amazon EC2 instance HTTPS endpoint to the topic, and process and store the data in Amazon RDS.
- C. Use an EC2 instance to serve the static front-end application and write requests to an Amazon SQS queue. Place the backend instances in an Auto Scaling group, and scale based on the queue depth to process and store the data in Amazon RDS.
- D. Use Amazon S3 to serve the static front-end application and send requests to Amazon API Gateway, which writes the requests to an Amazon SQS queue. Place the backend instances in an Auto Scaling group, and scale based on the queue length to process and store the data in Amazon RDS.

<details>
<summary>Show answer</summary>

**Answer: D**

This architecture fully decouples every tier: S3 hosts the static frontend (no EC2 needed), API Gateway provides a managed REST API endpoint, SQS queues requests for asynchronous processing, and Auto Scaling adjusts backend capacity based on queue depth. Option A still has a tightly coupled backend with no scaling. Option B uses SNS which is push-based and doesn't buffer requests — if the backend is overwhelmed, messages are lost. Option C keeps the frontend on EC2, missing the opportunity to decouple it with S3. Only D decouples all layers and enables horizontal scaling.
</details>

### Q64: Cost-effective Aurora dev cluster for intermittent use

A company wants to deploy an additional Amazon Aurora MySQL DB cluster for development purposes. This cluster will be used several times a week for a few minutes upon request to debug production query issues. The company wants to keep overhead low for this resource.

Which solution meets the company's requirements MOST cost-effectively?

- A. Purchase a Reserved Instance for the DB instances.
- B. Run the DB instances on Aurora Serverless.
- C. Create a stop/start schedule for the DB instances.
- D. Create an AWS Lambda function to stop DB instances if there are no active connections.

<details>
<summary>Show answer</summary>

**Answer: B**

Aurora Serverless automatically starts, scales, and pauses based on demand — you pay only for the capacity consumed while the cluster is active. This is ideal for an intermittent, on-request workload used only a few minutes a week, and it requires no manual management. Reserved Instances (A) are cost-effective only for steady-state, always-on workloads. A stop/start schedule (C) doesn't fit because usage is on-request and unpredictable, not on a fixed schedule. A custom Lambda function (D) adds operational overhead that Aurora Serverless eliminates natively.
</details>

### Q65: S3 storage class for logs with unpredictable access patterns

A solutions architect at an ecommerce company wants to store application log data using Amazon S3. The solutions architect is unsure how frequently the logs will be accessed or which logs will be accessed the most. The company wants to keep costs as low as possible by using the appropriate S3 storage class.

Which S3 storage class should be implemented to meet these requirements?

- A. S3 Glacier Flexible Retrieval (formerly S3 Glacier)
- B. S3 Intelligent-Tiering
- C. S3 Standard-Infrequent Access (S3 Standard-IA)
- D. S3 One Zone-Infrequent Access (S3 One Zone-IA)

<details>
<summary>Show answer</summary>

**Answer: B**

S3 Intelligent-Tiering automatically moves objects between frequent and infrequent access tiers based on changing access patterns, optimizing cost without performance impact or operational overhead. It is purpose-built for exactly this scenario — unknown or unpredictable access patterns. Glacier (A) is for archival with retrieval delays. Standard-IA (C) and One Zone-IA (D) assume you already know the data is infrequently accessed and charge retrieval fees, which is risky when access patterns are unknown.
</details>

### Q66: Overlooked cost factor for stopped EC2 data warehouse

A company runs an application on three very large Amazon EC2 instances in a single Availability Zone in the us-east-1 Region. Multiple 16 TB Amazon Elastic Block Store (Amazon EBS) volumes are attached to each EC2 instance. The operations team uses an AWS Lambda script triggered by a schedule-based Amazon EventBridge rule to stop the instances on evenings and weekends, and start the instances on weekday mornings.

Before deploying the solution, the company used the public AWS pricing documentation to estimate the overall costs of running this data warehouse solution 5 days a week for 10 hours a day. When looking at monthly Cost Explorer charges for this new account, the overall charges are higher than the estimate.

What is the MOST likely cost factor that the company overlooked?

- A. EC2 data transfer charges between the instances are much higher than expected.
- B. EC2 and EBS rates are higher in us-east-1 than most other AWS Regions.
- C. The Lambda charges to stop and start the instances are much higher than expected.
- D. The company is being billed for the EBS storage on nights and weekends.

<details>
<summary>Show answer</summary>

**Answer: D**

EBS volumes continue to incur storage charges 24/7 regardless of whether the attached EC2 instances are running or stopped. With multiple 16 TB volumes per instance across three instances, that's a substantial ongoing cost that persists during evenings and weekends — easily overlooked when estimating based only on EC2 running hours. Data transfer between instances (A) is unlikely to be significant in this scenario. us-east-1 (B) is one of the cheapest AWS Regions, not more expensive. Lambda charges (C) for a simple stop/start script are negligible and covered by the free tier.
</details>

### Q67: Improving Site-to-Site VPN throughput

A company is using AWS Site-to-Site VPN connections for secure connectivity to its AWS Cloud resources from on-premises locations. Due to an increase in traffic across the VPN connections to the Amazon EC2 instances, users are experiencing slower VPN connectivity. The network team reports that the internet connection used for the VPN has additional unused throughput.

Which solution will improve the VPN throughput?

- A. Implement multiple customer gateways for the same network.
- B. Configure a virtual private gateway with equal cost multipath routing and multiple channels.
- C. Use a transit gateway with equal cost multipath routing and add additional VPN tunnels.
- D. Increase the number of tunnels in the VPN configuration.

<details>
<summary>Show answer</summary>

**Answer: C**

A single Site-to-Site VPN tunnel is limited to ~1.25 Gbps of throughput. To scale beyond that, a transit gateway supports Equal Cost Multipath (ECMP) routing, which aggregates bandwidth across multiple VPN tunnels — distributing traffic and increasing total throughput. A virtual private gateway (B) does not support ECMP for aggregating VPN bandwidth. Multiple customer gateways (A) alone don't aggregate throughput. Simply adding tunnels to a VPN connection (D) doesn't help because, without ECMP on a transit gateway, the second tunnel serves as failover/redundancy, not aggregation.
</details>

### Q68: Serverless processing of S3 location uploads with auto-scaling

A user is designing a new service that receives location updates from 3,600 rental cars every hour. The cars upload their location to an Amazon S3 bucket. Each location must be checked for distance from the original rental location.

Which services will process the updates and automatically scale?

- A. Amazon EC2 and Amazon Elastic Block Store (Amazon EBS)
- B. Amazon Data Firehose and Amazon S3
- C. Amazon Elastic Container Service (Amazon ECS) and Amazon RDS
- D. Amazon S3 events and AWS Lambda

<details>
<summary>Show answer</summary>

**Answer: D**

S3 event notifications automatically trigger an AWS Lambda function whenever a car uploads its location to the bucket. Lambda processes each update (calculating distance from the original location) and scales automatically to handle any number of concurrent uploads — no servers to manage. EC2 with EBS (A) requires manual scaling configuration. Data Firehose (B) is for streaming data delivery, not running custom processing logic. ECS with RDS (C) requires provisioning and managing container infrastructure and a database, adding unnecessary overhead.
</details>

### Q69: Applying least privilege to security group rules between tiers

A company is reviewing a recent migration of a three-tier application to a VPC. The security team discovers that the principle of least privilege is not being applied to Amazon EC2 security group ingress and egress rules between the application tiers.

What should a solutions architect do to correct this issue?

- A. Create security group rules using the instance ID as the source or destination.
- B. Create security group rules using the security group ID as the source or destination.
- C. Create security group rules using the VPC CIDR blocks as the source or destination.
- D. Create security group rules using the subnet CIDR blocks as the source or destination.

<details>
<summary>Show answer</summary>

**Answer: B**

Referencing another security group's ID as the source or destination enforces least privilege — only instances belonging to that specific security group can communicate, regardless of their IP addresses, and it automatically adapts as instances are added or removed. Instance IDs (A) cannot be used as a source/destination in security group rules. VPC CIDR blocks (C) allow the entire VPC, and subnet CIDR blocks (D) allow an entire subnet — both are far too broad and violate the principle of least privilege.
</details>

### Q70: Querying an S3 data lake with infrequent SQL queries at minimal cost

A company is developing a data lake solution in Amazon S3 to analyze large-scale datasets. The solution makes infrequent SQL queries only. In addition, the company wants to minimize infrastructure costs.

Which AWS service should be used to meet these requirements?

- A. Amazon Athena
- B. Amazon Redshift Spectrum
- C. Amazon RDS for PostgreSQL
- D. Amazon Aurora

<details>
<summary>Show answer</summary>

**Answer: A**

Amazon Athena is a serverless, interactive query service that runs standard SQL directly against data in S3. You pay only per query based on the amount of data scanned — with no infrastructure to provision or manage — making it ideal for infrequent queries at minimal cost. Redshift Spectrum (B) requires a running Redshift cluster, incurring ongoing infrastructure cost. RDS for PostgreSQL (C) and Aurora (D) require loading data into a managed database that runs continuously, which is unnecessary cost and effort for infrequent ad-hoc queries on a data lake.
</details>

### Q71: Cost-effective storage for rarely accessed documents that must stay immediately available

A company has a three-tier web application on AWS for document storage and retrieval. The application stores documents on a shared NFS volume and references documents by using a Multi-AZ deployment of an Amazon RDS for MySQL DB instance. The document metadata is consulted regularly. The documents are not accessed more than one time a year, but they must remain immediately available. A solutions architect needs to optimize the workload and implement application modifications.

Which solution will meet these requirements MOST cost-effectively?

- A. Use an Amazon FSx for Lustre shared volume for document storage. Use a Multi-AZ deployment of an RDS for MySQL DB instance to keep document metadata.
- B. Use an Amazon S3 bucket with the S3 Glacier Deep Archive storage class for document storage. Use an Amazon DynamoDB table to keep document metadata.
- C. Use an Amazon S3 bucket with the S3 Standard-Infrequent Access (S3 Standard-IA) storage class for document storage. Use an Amazon DynamoDB table to keep document metadata.
- D. Use an Amazon Elastic File System (Amazon EFS) file system with the EFS Standard-Infrequent Access (EFS Standard-IA) storage class for document storage. Use a Multi-AZ deployment of an RDS for MySQL DB instance to keep document metadata.

<details>
<summary>Show answer</summary>

**Answer: C**

S3 Standard-IA is designed for data accessed infrequently (such as once a year) while still providing immediate, millisecond access — meeting the "must remain immediately available" requirement at a lower storage cost than S3 Standard. DynamoDB is a cost-effective, fully managed store for the regularly consulted metadata. Glacier Deep Archive (B) is the cheapest storage but has retrieval times of hours, violating the immediate-availability requirement. FSx for Lustre (A) is a high-performance file system for HPC, far too expensive for archival. EFS Standard-IA (D) costs more than S3 Standard-IA, and keeping RDS Multi-AZ for simple metadata is more expensive than DynamoDB.
</details>

### Q72: Reducing NAT gateway costs for EMR jobs accessing S3

An application team has started using Amazon EMR to run batch jobs, using datasets located in Amazon S3. During the initial testing of the workload, a solutions architect notices that the account is starting to accrue NAT gateway data processing costs.

How can the team optimize the cost of the workload?

- A. Detach the NAT gateway from the subnet where the Amazon EMR clusters are running.
- B. Replace the NAT gateway with a customer gateway.
- C. Replace the NAT gateway with an S3 VPC endpoint.
- D. Configure a network ACL on the subnets where the Amazon EMR clusters are running to open access to Amazon S3.

<details>
<summary>Show answer</summary>

**Answer: C**

A gateway VPC endpoint for S3 routes traffic between the EMR clusters and S3 directly over the AWS network, bypassing the NAT gateway entirely — and gateway endpoints incur no data processing charges. This eliminates the NAT data processing costs while preserving connectivity. Detaching the NAT gateway (A) would break the EMR clusters' other internet-bound traffic. A customer gateway (B) is a VPN component, irrelevant here. A network ACL (D) controls traffic permissions but doesn't change routing, so traffic would still flow through the NAT gateway and accrue charges.
</details>

### Q73: Securing a web tier and private database tier without disrupting running apps

A company hosts a popular web application. The web application connects to a database running in a private VPC subnet. The web servers must be accessible only to customers on an SSL connection. The Amazon RDS for MySQL database server must be accessible only from the web servers.

How should a solutions architect design a solution to meet the requirements without impacting running applications?

- A. Create a network ACL on the web server's subnet, allow HTTPS inbound and MySQL outbound. Place both database and web servers on the same subnet.
- B. Open an HTTPS port on the security group for web servers and set the source to 0.0.0.0/0. Open the MySQL port on the database security group and attach it to the MySQL instance. Set the source to web server security group.
- C. Create a network ACL on the web server's subnet; allow HTTPS inbound, and specify the source as 0.0.0.0/0. Create a network ACL on a database subnet, allow MySQL port inbound for web servers, and deny all outbound traffic.
- D. Open the MySQL port on the security group for web servers and set the source to 0.0.0.0/0. Open the HTTPS port on the database security group and attach it to the MySQL instance. Set the source to web server security group.

<details>
<summary>Show answer</summary>

**Answer: B**

Security groups are stateful and can be applied without disrupting running applications. Opening HTTPS (443) with source 0.0.0.0/0 on the web server security group allows customers to reach the web tier over SSL. Setting the database security group's MySQL port (3306) source to the web server security group enforces least privilege — only the web servers can reach the database. Network ACLs (A, C) are stateless, harder to manage for stateful traffic, and option A's placement of the database in the web subnet undermines isolation. Option D inverts the ports/services incorrectly (MySQL open to the internet, HTTPS on the database).
</details>

### Q74: Minimizing throughput impact of a slow-running API endpoint

A company has a web application that makes requests to a backend API service. The API service runs on Amazon EC2 instances accessed behind an Elastic Load Balancer.

Most backend API service endpoint calls finish very quickly, but one endpoint that makes calls to create objects in an external service takes a long time to complete. These long-running calls are causing client timeouts and increasing overall system latency.

What should be done to minimize the system throughput impact of the slow-running endpoint?

- A. Change the EC2 instance size to increase memory and compute capacity.
- B. Use Amazon Simple Queue Service (Amazon SQS) to offload the long-running requests for asynchronous processing by separate workers.
- C. Increase the load balancer idle timeout to allow the long-running requests to complete.
- D. Use Amazon ElastiCache for Redis to cache responses from the external service.

<details>
<summary>Show answer</summary>

**Answer: B**

Offloading the long-running object-creation requests to an SQS queue decouples them from the synchronous request path. Separate worker processes consume the queue and call the external service asynchronously, so slow calls no longer block resources or cause client timeouts — preserving overall system throughput. Increasing instance size (A) doesn't fix the blocking behavior. Increasing the load balancer idle timeout (C) just makes clients wait longer instead of eliminating the bottleneck. ElastiCache (D) caches read responses, but these calls create objects (writes), so caching doesn't apply.
</details>

### Q75: Least-privilege IAM policy for a Lambda function accessing one DynamoDB table

A company has implemented one of its microservices on AWS Lambda that accesses an Amazon DynamoDB table named "Books". A solutions architect is designing an IAM policy to be attached to the Lambda function's IAM role giving it access to put, update, and delete items in the "Books" table. The IAM policy must prevent the function from performing any other actions on the "Books" table and any other table.

Which IAM policy would fulfill these needs and provide the LEAST privileged access?

- A. Allow only the `dynamodb:PutItem`, `dynamodb:UpdateItem`, and `dynamodb:DeleteItem` actions on the resource `arn:aws:dynamodb:us-west-2:123456789012:table/Books`.
- B. Allow the `dynamodb:PutItem`, `dynamodb:UpdateItem`, and `dynamodb:DeleteItem` actions on the resource `arn:aws:dynamodb:us-west-2:123456789012:table/*` (all tables).
- C. Allow the action `dynamodb:*` (all DynamoDB actions) on the resource `arn:aws:dynamodb:us-west-2:123456789012:table/Books`.
- D. Allow `dynamodb:*` on the `Books` table, plus a separate statement that denies `dynamodb:*` on `*`.

<details>
<summary>Show answer</summary>

**Answer: A**

Granting exactly the three required actions (PutItem, UpdateItem, DeleteItem) scoped to the specific `table/Books` resource ARN is the definition of least privilege — nothing more is allowed. Option B uses a wildcard resource (`table/*`), granting access to all tables. Option C allows every DynamoDB action (`dynamodb:*`) on the Books table, far more than the three needed operations. Option D both over-grants (all actions on Books) and creates a conflicting/over-broad explicit Deny that would also block the intended write actions.
</details>

### Q76: Relational database with multi-Region DR, 1-second RPO and 1-minute RTO

A company needs to implement a relational database with a multi-Region disaster recovery Recovery Point Objective (RPO) of 1 second and a Recovery Time Objective (RTO) of 1 minute.

Which AWS solution can achieve this?

- A. Amazon Aurora Global Database
- B. Amazon DynamoDB global tables
- C. Amazon RDS for MySQL with Multi-AZ turned on
- D. Amazon RDS for MySQL with a cross-Region snapshot copy

<details>
<summary>Show answer</summary>

**Answer: A**

Aurora Global Database replicates data across Regions with typical latency under 1 second (meeting the 1-second RPO) and supports promoting a secondary Region in under a minute (meeting the 1-minute RTO) — and it is a relational database. DynamoDB global tables (B) meet the RPO/RTO but are NoSQL, not relational. RDS Multi-AZ (C) provides high availability within a single Region, not multi-Region DR. Cross-Region snapshot copy (D) has an RPO/RTO measured in hours, far exceeding the requirements.
</details>

### Q77: Privately exposing an application to thousands of consumer accounts (PHI)

A company wants to create an application that will transmit protected health information (PHI) to thousands of service consumers in different AWS accounts. The application servers will sit in private VPC subnets. The routing for the application must be fault tolerant.

What should be done to meet these requirements?

- A. Create a VPC endpoint service and grant permissions to specific service consumers to create a connection.
- B. Create a virtual private gateway connection between each pair of service provider VPCs and service consumer VPCs.
- C. Create an internal Application Load Balancer in the service provider VPC and put application servers behind it.
- D. Create a proxy server in the service provider VPC to route requests from service consumers to the application servers.

<details>
<summary>Show answer</summary>

**Answer: A**

AWS PrivateLink (a VPC endpoint service) allows a provider to expose an application privately to thousands of consumers across different AWS accounts. Traffic stays entirely on the AWS network — never traversing the public internet — which is ideal for sensitive PHI data. Backed by a Network Load Balancer spanning multiple Availability Zones, it is inherently fault tolerant, and access is granted per consumer. Virtual private gateways (B) are VPN connections that don't scale to thousands of accounts. An internal ALB (C) is not reachable from other accounts. A custom proxy server (D) adds operational overhead and is not inherently fault tolerant.
</details>

### Q78: Modernizing a hospital's database write process to lower operational overhead (Select TWO)

A hospital is migrating from another cloud provider to AWS and wants to modernize as they migrate. The hospital has containerized applications that run on tablets. During spikes caused by increases in patient visits, the communications from the applications to the central database occasionally fail. As a result, the hospital currently has the applications try to write to the central database once. If that write fails, the application writes to a dedicated application PostgreSQL database run by the hospital IT team on premises. Each of the PostgreSQL databases then sends batch information to the central database.

The hospital is asking for recommendations on migrating or refactoring the database write process to lower operational overhead.

What should the solutions architect recommend? (Select TWO.)

- A. Migrate the containerized applications to AWS Fargate.
- B. Migrate the local databases to Amazon Aurora Serverless PostgreSQL-Compatible Edition.
- C. Migrate the PostgreSQL databases to an Amazon RDS instance with a read replica that replaces each of the local databases.
- D. Refactor the applications to use an Amazon Simple Queue Service (Amazon SQS) and eliminate the local PostgreSQL databases.
- E. Refactor the central database to add an Amazon ElastiCache lazy loading cache in front of the database.

<details>
<summary>Show answer</summary>

**Answer: A, D**

Migrating the containerized applications to AWS Fargate (A) provides a serverless container platform with no infrastructure to manage, modernizing the apps and reducing operational overhead. Refactoring to use SQS (D) durably buffers writes during patient-visit spikes — messages are never lost and are processed to the central database at a sustainable rate, which eliminates the need for the on-premises PostgreSQL workaround databases entirely. Migrating the local databases to Aurora Serverless (B) or RDS with read replicas (C) keeps the unnecessary intermediate databases instead of removing them. ElastiCache (E) is a read cache and does nothing to solve failing writes.
</details>

### Q79: Preventing lost DynamoDB transactions during flash sales

A company is using Amazon DynamoDB with provisioned throughput for the inventory database tier of its ecommerce website. During flash sales, customers experience periods of time when the database cannot handle the high number of transactions taking place. This causes the company to lose transactions. During normal periods, the database performs appropriately.

Which solution solves the performance problem the company faces?

- A. Switch DynamoDB to on-demand mode during flash sales.
- B. Implement DynamoDB Accelerator (DAX).
- C. Use Amazon Kinesis to queue transactions for processing to DynamoDB.
- D. Use Amazon Simple Queue Service (Amazon SQS) to queue transactions to DynamoDB.

<details>
<summary>Show answer</summary>

**Answer: A**

Switching DynamoDB to on-demand mode gives the table the ability to scale to meet sudden increases in demand that occur during flash sales, solving the throttling/lost-transaction problem without managing capacity.

- **B is wrong:** DAX improves read performance for repetitive queries; it does not help one-time queries or improve write performance.
- **C is wrong:** Recording purchases as Kinesis messages and then processing them into DynamoDB means the writes could be out of sync with the reads — the company could sell inventory that is not available.
- **D is wrong:** Same problem as C — queuing purchases through SQS and processing them asynchronously means writes could lag reads, leading to selling unavailable inventory.
</details>

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

<details>
<summary>Show answer</summary>

**Answer: B, D**

A bastion host (jump box) in a public subnet with a security group that only allows SSH from the corporate network (B) ensures access originates only from the corporate network and not the open internet. Configuring the private web servers' security group to allow SSH only from the bastion's security group (D) prevents any direct SSH from the internet while permitting developers to reach them through the bastion. Authenticating against the corporate directory (A) is not required by the stated requirements. IAM roles (C) grant AWS API permissions, not SSH access. Denying SSH from the corporate network in a NACL (E) directly contradicts the requirement.
</details>

### Q81: Encrypting S3 objects at rest without managing keys, while controlling key access

A company is planning to use Amazon S3 to store images uploaded by its users. The images must be encrypted at rest in Amazon S3. The company does not want to spend time managing and rotating the keys, but it does want to control who can access those keys.

What should a solutions architect use to accomplish this?

- A. Server-Side Encryption with encryption keys stored in an S3 bucket
- B. Server-Side Encryption with Customer-Provided Keys (SSE-C)
- C. Server-Side Encryption with encryption keys stored in AWS Systems Manager Parameter Store
- D. Server-Side Encryption with AWS KMS-Managed Keys (SSE-KMS)

<details>
<summary>Show answer</summary>

**Answer: D**

SSE-KMS encrypts objects at rest using keys managed by AWS Key Management Service, which handles key storage and automatic rotation — so the company doesn't manage or rotate keys — while KMS key policies and IAM give the company fine-grained control over who can use the keys. SSE-C (B) requires the customer to provide and manage their own keys, the opposite of the requirement. Options A and C describe storing keys in S3 or Parameter Store, which are not valid S3 server-side encryption mechanisms.
</details>

### Q82: Internet access for private EC2 instances that is fault tolerant per Availability Zone

An application runs on Amazon EC2 instances in multiple Availability Zones behind an Application Load Balancer. The load balancer is in public subnets. The EC2 instances are in private subnets and must not be accessible from the internet. The EC2 instances must call external services on the internet. Each Availability Zone must be able to call the external services, regardless of the status of the other Availability Zones.

How should these requirements be met?

- A. Create a NAT gateway attached to the VPC. Add a route to the gateway that connects to each private subnet route table.
- B. Configure an internet gateway. Add a route to the gateway that connects to each private subnet route table.
- C. Create a NAT instance in the private subnet of each Availability Zone. Update the route tables for each private subnet to direct internet-bound traffic to the NAT instance.
- D. Create a NAT gateway in each Availability Zone. Update the route tables for each private subnet to direct internet-bound traffic to the NAT gateway.

<details>
<summary>Show answer</summary>

**Answer: D**

Deploying a NAT gateway in each Availability Zone makes outbound internet access fault tolerant per AZ — if one AZ (or its NAT gateway) fails, the other AZs continue to reach external services independently. NAT gateways allow outbound traffic while keeping the private instances unreachable from the internet. A single NAT gateway (A) is a single point of failure across AZs. An internet gateway in private subnet route tables (B) would expose the instances to inbound internet traffic, violating the requirement. NAT instances (C) belong in public subnets, are self-managed, and are less resilient than NAT gateways.
</details>

### Q83: Reducing AWS Lambda cost by adjusting memory allocation (Select TWO)

A development team is deploying a new product on AWS and is using AWS Lambda as part of the deployment. The team allocates 512 MB of memory for one of the Lambda functions. With this memory allocation, the function is completed in 2 minutes. The function runs millions of times monthly, and the development team is concerned about cost. The team conducts tests to see how different Lambda memory allocations affect the cost of the function.

Which steps will reduce the Lambda costs for the product? (Select TWO.)

- A. Increase the memory allocation for this Lambda function to 1,024 MB if this change causes the run time of each function to be less than 1 minute.
- B. Increase the memory allocation for this Lambda function to 1,024 MB if this change causes the run time of each function to be less than 90 seconds.
- C. Reduce the memory allocation for this Lambda function to 256 MB if this change causes the run time of each function to be less than 4 minutes.
- D. Increase the memory allocation for this Lambda function to 2,048 MB if this change causes the run time of each function to be less than 1 minute.
- E. Reduce the memory allocation for this Lambda function to 256 MB if this change causes the run time of each function to be less than 5 minutes.

<details>
<summary>Show answer</summary>

**Answer: A, C**

Lambda billing is based on GB-seconds (memory × duration). The baseline is 512 MB × 120 s = 61,440 MB-seconds. Option A (1,024 MB × under 60 s) yields under 61,440 MB-seconds — cheaper. Option C (256 MB × under 240 s) also yields under 61,440 MB-seconds — cheaper. Option B (1,024 MB × 90 s = 92,160 MB-seconds) and option D (2,048 MB × 60 s = 122,880 MB-seconds) both cost more. Option E (256 MB × 300 s = 76,800 MB-seconds) also costs more than the baseline.
</details>

### Q84: Migrating a desktop relational app to a highly available solution with least disruption

During a review of business applications, a solutions architect identifies a critical application with a relational database that was built by a business user and is running on the user's desktop. To reduce the risk of a business interruption, the solutions architect wants to migrate the application to a highly available, multi-tiered solution in AWS.

What should the solutions architect do to accomplish this with the LEAST amount of disruption to the business?

- A. Create an import package of the application code for upload to AWS Lambda, and include a function to create another Lambda function to migrate data into an Amazon RDS database.
- B. Create an image of the user's desktop and migrate it to Amazon EC2 using VM Import. Place the EC2 instance in an Auto Scaling group.
- C. Pre-stage new Amazon EC2 instances running the application code on AWS behind an Application Load Balancer and an Amazon RDS Multi-AZ DB instance.
- D. Use AWS Database Migration Service (AWS DMS) to migrate the backend database to an Amazon RDS Multi-AZ DB instance. Migrate the application code to AWS Elastic Beanstalk.

<details>
<summary>Show answer</summary>

**Answer: D**

AWS DMS migrates the relational database to a highly available RDS Multi-AZ instance with minimal downtime, while AWS Elastic Beanstalk hosts the application code in a managed, automatically scalable, multi-tiered environment — together delivering high availability with the least disruption. Lambda (A) would require significant refactoring of a desktop application. Imaging the desktop to a single EC2 instance (B) is just a lift-and-shift that doesn't create a proper multi-tier, highly available architecture and leaves the database embedded in the image. Pre-staging EC2 with hand-deployed code (C) requires manually re-platforming the application, which is more disruptive and error-prone than the managed DMS + Elastic Beanstalk approach.
</details>

### Q85: Cost-optimizing an underutilized Aurora cluster used intermittently for reads

A solutions architect finds that an Amazon Aurora cluster with On-Demand Instance pricing is being underutilized for a blog application. The application is used only for a few minutes several times each day for reads.

What should a solutions architect do to optimize utilization MOST cost-effectively?

- A. Turn on Auto Scaling on the original Aurora database.
- B. Refactor the blog application to use Aurora parallel query.
- C. Convert the original Aurora database to an Aurora global database.
- D. Convert the original Aurora database to Amazon Aurora Serverless.

<details>
<summary>Show answer</summary>

**Answer: D**

Aurora Serverless automatically scales capacity up and down based on demand and pauses when idle, so the company pays only for the capacity consumed during the brief periods of use — ideal for a blog accessed only a few minutes several times a day. Auto Scaling (A) adds read replicas but the always-on base instance continues to incur On-Demand charges during idle periods. Aurora parallel query (B) accelerates analytic queries but doesn't reduce idle cost. An Aurora global database (C) adds cross-Region replication, increasing cost rather than reducing it.
</details>

### Q86: Most cost-effective compute for a short, low-memory prediction process

A prediction process requires access to a trained model that is stored in an Amazon S3 bucket. The process takes a few seconds to process an image and make a prediction. The process is not overly resource-intensive, does not require any specialized hardware, and takes less than 512 MB of memory to run.

What is the MOST cost-effective compute solution for this use case?

- A. Amazon Elastic Container Service (Amazon ECS)
- B. Amazon EC2 Spot Instances
- C. AWS Elastic Beanstalk
- D. AWS Lambda functions

<details>
<summary>Show answer</summary>

**Answer: D**

Lambda runs code without provisioning or managing servers and charges only for the compute time consumed, with no charge when idle — ideal for a short (seconds), low-memory (<512 MB), non-specialized task. ECS (A), Beanstalk (C), and Spot Instances (B) all rely on EC2 instances that incur cost while running and add management overhead, making them more expensive for this lightweight, intermittent workload. Spot Instances are also unsuitable as a service backend because capacity is not guaranteed.
</details>

### Q87: Most cost-effective storage for archived audit data retrievable next business day

A company is using an Amazon S3 bucket to store archived data for audits. The company needs long-term storage for the data. The data is rarely accessed and must be available for retrieval the next business day.

After a quarterly review, the company wants to reduce the storage cost for the S3 bucket. A solutions architect must recommend the most cost-effective solution to store the archived data.

Which solution will meet these requirements?

- A. Store the data on an Amazon EC2 instance that uses Amazon Elastic Block Store (Amazon EBS).
- B. Store the data in S3 Glacier Flexible Retrieval.
- C. Use an S3 Lifecycle configuration rule to move the data to S3 Standard-Infrequent Access (S3 Standard-IA).
- D. Store the data in another S3 bucket in a different AWS Region.

<details>
<summary>Show answer</summary>

**Answer: B**

S3 Glacier Flexible Retrieval is built for long-term archival of rarely accessed data and its standard retrieval (3–5 hours) easily satisfies a next-business-day requirement at a much lower storage cost than the other options. EBS on EC2 (A) is expensive block storage that must stay running. S3 Standard-IA (C) costs more than Glacier for archival data that is essentially never read. Copying to another Region (D) increases cost and addresses replication, not archival savings.
</details>

### Q88: Automating EBS snapshot lifecycle with least effort (Select TWO)

After reviewing the cost optimization checks in AWS Trusted Advisor, a team finds that it has 10,000 Amazon Elastic Block Store (Amazon EBS) snapshots in its account that are more than 30 days old. The team has determined that it needs to implement better governance for the lifecycle of its resources.

Which actions should the team take to automate the lifecycle management of the EBS snapshots with the LEAST effort? (Select TWO.)

- A. Create and schedule a backup plan with AWS Backup.
- B. Copy the EBS snapshots to Amazon S3, and then create lifecycle configurations in the S3 bucket.
- C. Use Amazon Data Lifecycle Manager.
- D. Use a scheduled event in Amazon EventBridge and invoke AWS Step Functions to manage the snapshots.
- E. Schedule and run backups in AWS Systems Manager.

<details>
<summary>Show answer</summary>

**Answer: A, C**

Amazon Data Lifecycle Manager is purpose-built to automate creation, retention, and deletion of EBS snapshots through simple policies. AWS Backup centrally automates and schedules backup/retention lifecycles across services including EBS. Both require minimal effort. Copying snapshots to S3 (B) is manual and S3 lifecycle rules don't act on EBS snapshots. EventBridge + Step Functions (D) and Systems Manager scripting (E) require building and maintaining custom automation — far more effort.
</details>

### Q89: Lifecycle policy for images accessed for 30 days then rarely, with millisecond access

A company's application gives users the ability to upload image files to an Amazon S3 bucket. These files are accessed frequently for the first 30 days. After 30 days, these files are rarely accessed. However, the files need to be durably stored and available within milliseconds upon request. A solutions architect needs to configure a lifecycle policy that minimizes the overall cost while meeting the application requirements.

Which solution will meet these requirements?

- A. Configure a lifecycle policy to move the files to the S3 Standard-Infrequent Access (S3 Standard-IA) storage class after 30 days.
- B. Configure a lifecycle policy to move the files to the S3 Glacier Flexible Retrieval storage class after 30 days.
- C. Configure a lifecycle policy to move the files to the S3 Glacier Deep Archive storage class after 30 days.
- D. Configure a lifecycle policy to move the files to the S3 One Zone-Infrequent Access (S3 One Zone-IA) storage class after 30 days.

<details>
<summary>Show answer</summary>

**Answer: A**

S3 Standard-IA delivers millisecond retrieval and stores data redundantly across multiple Availability Zones at a lower cost than S3 Standard — perfect for infrequently accessed files that still must be returned within milliseconds. Glacier Flexible Retrieval (B) and Glacier Deep Archive (C) require minutes to hours for retrieval, violating the millisecond requirement. S3 One Zone-IA (D) gives millisecond access but stores data in a single AZ, reducing durability against AZ loss.
</details>

### Q90: Charging departments for a shared analytics environment with least effort

A company is deploying an environment for a new data processing application. The application will be frequently accessed by 20 different departments across the globe seeking to run analytics. The company plans to charge each department for the cost of that department's access.

Which solution will meet these requirements with the LEAST effort?

- A. Amazon Aurora with global databases. Each department will query a database in a different Region, and the Region is tagged in the billing console.
- B. Amazon RDS for PostgreSQL, with read replicas for each department. Each department will query the read replica tagged for their department in the billing console.
- C. Amazon Redshift, with clusters set up for each department. Each department will query the cluster tagged for their department in the billing console.
- D. Amazon Athena with workgroups set up for each department. Each department will query through the workgroup tagged for their department in the billing console.

<details>
<summary>Show answer</summary>

**Answer: D**

Amazon Athena lets you query data directly in Amazon S3, and workgroups are purpose-built to separate users/teams and track and allocate query costs per group with minimal setup. Aurora global databases (A) and RDS read replicas (B) are built for transactional workloads, not analytics, and spinning up per-department copies adds cost and effort. Redshift clusters per department (C) duplicate infrastructure and add significant cost and management overhead.
</details>

### Q91: Removing EC2 Spot Instances after a demonstration

A company used Amazon EC2 Spot Instances for a demonstration that is now complete. A solutions architect must remove the Spot Instances to stop them from incurring cost.

What should the solutions architect do to meet this requirement?

- A. Cancel the Spot request. Terminate the Spot Instances.
- B. Cancel the Spot request only.
- C. Terminate the Spot Instances only.
- D. Terminate the Spot Instances. Cancel the Spot request.

<details>
<summary>Show answer</summary>

**Answer: A**

To stop Spot Instances from incurring cost you must first cancel the Spot request so it cannot launch replacements, and then terminate the running Spot Instances. Canceling the request only (B) leaves running instances incurring cost. Terminating instances only (C), or terminating before canceling (D), can cause the still-active request to launch new instances to maintain target capacity. The correct order is cancel the request, then terminate the instances.
</details>

### Q92: Database tier requiring full OS control and high availability

A financial services company is migrating its multi-tier web application to AWS. The application architecture consists of a fleet of web servers, application servers, and an Oracle database. The company must have full control over the database's underlying operating system. The database must be highly available.

Which approach should a solutions architect use for the database tier to meet these requirements?

- A. Migrate the database to an Amazon RDS for Oracle DB Single-AZ DB instance.
- B. Migrate the database to an Amazon RDS for Oracle Multi-AZ DB instance.
- C. Migrate to Amazon EC2 instances in two Availability Zones. Install Oracle and configure the instances to operate as a cluster.
- D. Migrate to Amazon EC2 instances in a single Availability Zone. Install Oracle and configure the instances to operate as a cluster.

<details>
<summary>Show answer</summary>

**Answer: C**

Full control over the underlying operating system rules out Amazon RDS, which is a managed service that does not grant OS access — eliminating options A and B. Running Oracle on self-managed EC2 instances gives the company OS-level control, and deploying across two Availability Zones in a cluster provides high availability. A single-AZ deployment (D) cannot survive an Availability Zone failure, so it does not meet the high-availability requirement.
</details>

### Q93: DR strategy with seconds RPO, minutes RTO, and a scaled-down running environment

A solutions architect must create a disaster recovery (DR) solution for a company's business-critical applications. The DR site must reside in a different AWS Region than the primary site. The solution requires a recovery point objective (RPO) in seconds and a recovery time objective (RTO) in minutes. The solution also requires the deployment of a completely functional but scaled-down version of the applications.

Which DR strategy will meet these requirements?

- A. Multi-site active-active
- B. Backup and restore
- C. Pilot light
- D. Warm standby

<details>
<summary>Show answer</summary>

**Answer: D**

Warm standby keeps a fully functional but scaled-down copy of the environment always running in the DR Region, enabling an RPO in seconds and an RTO in minutes by simply scaling up when failover occurs. Multi-site active-active (A) also meets the RTO/RPO but runs a full-scale environment, which is more than "scaled-down" and the most expensive. Backup and restore (B) has RTO/RPO in hours. Pilot light (C) keeps only core services running (not a "completely functional" version), so bringing up the full application takes longer.
</details>

### Q94: Highly available MySQL database that maximizes ease of management

A company is deploying a production portal application on AWS. The database tier runs on a MySQL database. The company requires a highly available database solution that maximizes ease of management.

How can the company meet these requirements?

- A. Deploy the database on multiple Amazon EC2 instances that are backed by Amazon Elastic Block Store (Amazon EBS) across multiple Availability Zones. Schedule periodic EBS snapshots.
- B. Use Amazon RDS with a Single-AZ deployment. Schedule periodic database snapshots.
- C. Use Amazon RDS with a Multi-AZ deployment. Schedule periodic database snapshots.
- D. Use Amazon DynamoDB with a DynamoDB Accelerator (DAX) cluster. Create periodic on-demand backups.

<details>
<summary>Show answer</summary>

**Answer: C**

Amazon RDS is a managed service that maximizes ease of management, and a Multi-AZ deployment provides high availability by automatically failing over to a standby in another Availability Zone. Self-managing MySQL on EC2 (A) maximizes operational effort, the opposite of the requirement. RDS Single-AZ (B) is easy to manage but not highly available. DynamoDB (D) is a NoSQL database and is not compatible with the existing MySQL (relational) workload.
</details>

### Q95: Resilient, highly scalable architecture for a fast-growing gaming application

A gaming company is experiencing exponential growth. On multiple occasions, customers have been unable to access resources. To keep up with the increased demand, management is considering deploying a cloud-based solution. The company wants a solution that can match the on-premises resilience of multiple data centers and is robust enough to withstand the increased growth in usage.

Which configuration should a solutions architect implement to meet these requirements?

- A. A VPC configured with an ELB Network Load Balancer that targets an Amazon EC2 Auto Scaling group consisting of Amazon EC2 instances that span two Availability Zones.
- B. A VPC configured with an ELB Application Load Balancer that targets an Amazon EC2 Auto Scaling group consisting of Amazon EC2 instances across two Availability Zones.
- C. Multiple Amazon EC2 instances that are configured within peered VPCs across two Availability Zones.
- D. A VPC configured with an ELB Application Load Balancer that targets an Amazon EC2 Auto Scaling group consisting of Amazon EC2 instances that span two AWS Regions.

<details>
<summary>Show answer</summary>

**Answer: B**

An Application Load Balancer fronting an EC2 Auto Scaling group across two Availability Zones provides both resilience (multiple AZs mirror multiple on-premises data centers) and elastic scaling to absorb growth. A Network Load Balancer (A) is for extreme TCP/UDP performance and is less suited to this web-facing application. Peered VPCs (C) add complexity without an autoscaling/load-balancing front end. An ALB cannot span two AWS Regions (D) — load balancers are Regional — so that option is not valid.
</details>

### Q96: Cost-effective, highly available data store protected from accidental deletion

A solutions architect must create a data store location that will be able to handle different file formats of unknown sizes. The data must be highly available and protected from being accidentally deleted.

What solution meets the requirements and is the MOST cost-effective?

- A. Deploy an Amazon S3 bucket and activate Cross-Region Replication.
- B. Deploy an Amazon DynamoDB table and activate global tables.
- C. Deploy a database by using Amazon RDS and configure a Multi-AZ deployment for that database.
- D. Deploy an Amazon S3 bucket and enable object versioning.

<details>
<summary>Show answer</summary>

**Answer: D**

Amazon S3 is object storage ideal for arbitrary file formats of any size and is highly durable and available across multiple Availability Zones by default. Enabling object versioning protects against accidental deletion or overwrite at minimal extra cost. Cross-Region Replication (A) adds cost mainly for regional resilience, which isn't required here. DynamoDB (B) and RDS (C) are structured databases unsuited to storing arbitrary files and cost more for this use case.
</details>

### Q97: Centrally automating SSL/TLS certificate deployment and management

A SysOps administrator wants to automate the deployment of new SSL/TLS certificates to web servers. The administrator wants a centralized way to keep track of and manage the deployed certificates.

Which AWS service can the administrator use to meet these requirements?

- A. AWS Key Management Service (AWS KMS)
- B. Run Command, a capability of AWS Systems Manager
- C. AWS Certificate Manager (ACM)
- D. Parameter Store, a capability of AWS Systems Manager

<details>
<summary>Show answer</summary>

**Answer: C**

AWS Certificate Manager provisions, manages, deploys, and automatically renews public and private SSL/TLS certificates from a central place, removing the manual work of purchasing, uploading, and renewing them. KMS (A) manages encryption keys, not SSL/TLS certificates. Run Command (B) can push a one-time deployment but does not provide ongoing certificate management. Parameter Store (D) stores configuration data and secrets but cannot manage SSL/TLS certificates.
</details>

### Q98: Identifying the destination of malicious network traffic from an EC2 instance

A solutions architect notices an abnormal amount of network traffic coming from an Amazon EC2 instance. The traffic is determined to be malicious, and the destination needs to be determined.

What tool can the solutions architect use to identify the destination of the malicious network traffic?

- A. Turn on AWS CloudTrail and filter the logs.
- B. Turn on VPC Flow Logs and filter the logs.
- C. Consult the AWS Health Dashboard.
- D. Filter the logs from Amazon CloudWatch.

<details>
<summary>Show answer</summary>

**Answer: B**

VPC Flow Logs capture information about the IP traffic going to and from network interfaces in a VPC, including source and destination IP addresses and ports — exactly what is needed to identify where the malicious traffic is going. CloudTrail (A) records API calls, not network packet flows. The AWS Health Dashboard (C) reports AWS service health. CloudWatch (D) collects metrics and application logs but does not, by itself, record network traffic destinations.
</details>

### Q99: Securing a three-tier VPC architecture with HTTPS-only web access (Select TWO)

A solutions architect needs to design a secure environment for AWS resources that are being deployed to Amazon EC2 instances in a VPC. The solution should support a three-tier architecture that consists of web servers, application servers, and a database cluster. The VPC needs to allow resources in the web tier to be accessible from the internet over only the HTTPS protocol.

Which combination of actions would meet these requirements? (Select TWO.)

- A. Attach an Amazon API Gateway to the VPC. Create private subnets for the web, application, and database tiers.
- B. Attach an internet gateway to the VPC. Create public subnets for the web tier. Create private subnets for the application and database tiers.
- C. Attach a virtual private gateway to the VPC. Create public subnets for the web and application tiers. Create private subnets for the database tier.
- D. Create a web server security group that allows all traffic from the internet. Create an application server security group that allows requests from only the Amazon API Gateway on the application port. Create a database cluster security group that allows requests from the application security group on the database port only.
- E. Create a web server security group that allows HTTPS requests from the internet. Create an application server security group that allows requests from the web security group only. Create a database cluster security group that allows requests from the application security group on the database port only.

<details>
<summary>Show answer</summary>

**Answer: B, E**

An internet gateway with public subnets for the web tier and private subnets for the application and database tiers correctly isolates the back-end tiers from direct internet access. Security groups that allow only HTTPS from the internet to the web tier, only web-tier traffic to the application tier, and only application-tier traffic to the database enforce least-privilege flow between tiers. Option D allows all traffic to the web tier (not HTTPS-only). A virtual private gateway (C) is for VPN connectivity, not internet access, and placing the application tier in public subnets reduces security. API Gateway (A) does not provide the EC2 three-tier network isolation described.
</details>

### Q100: Minimizing data transfer cost for a global static website on S3

A company will host a static website within an Amazon S3 bucket. The website will serve millions of users globally, and the company wants to minimize data transfer costs.

What should a solutions architect do to ensure costs are kept to a minimum?

- A. Implement an AWS Auto Scaling group for the website to ensure it grows with use.
- B. Use Cross-Region Replication to copy the website to an additional S3 bucket in a different AWS Region.
- C. Move the website to large compute-optimized Amazon EC2 instances with on-demand pricing.
- D. Create an Amazon CloudFront distribution, with the S3 bucket as the origin server.

<details>
<summary>Show answer</summary>

**Answer: D**

Amazon CloudFront caches the static content at edge locations close to users worldwide, reducing the volume of data served directly from S3 and lowering data transfer costs while improving performance. Auto Scaling (A) and EC2 instances (C) don't apply to static content served from S3 and add cost. Cross-Region Replication (B) duplicates storage and adds replication cost without reducing data transfer for global users.
</details>

### Q101: Cost-effective secure login to EC2 instances in a private subnet

A company is developing an application that runs on Amazon EC2 instances in a private subnet. The EC2 instances use a NAT gateway to access the internet. A solutions architect must provide a secure option so that developers can log in to the instances.

Which solution meets these requirements MOST cost-effectively?

- A. Configure AWS Systems Manager Session Manager for the EC2 instances to activate login.
- B. Configure a bastion host in a public subnet to log in to the EC2 instances in a private subnet.
- C. Use the existing NAT gateway to log in to the EC2 instances in a private subnet.
- D. Configure AWS Site-to-Site VPN to log in directly to the EC2 instances.

<details>
<summary>Show answer</summary>

**Answer: A**

AWS Systems Manager Session Manager provides secure, auditable shell access to private EC2 instances without opening inbound ports or running extra infrastructure, and there is no additional charge for the service — making it the most cost-effective and secure option. A bastion host (B) requires a continuously running EC2 instance and incurs cost. A NAT gateway (C) only enables outbound internet access and cannot be used for inbound login. Site-to-Site VPN (D) is for connecting networks and adds cost and complexity not warranted here.
</details>

### Q102: Cost-effective microservices with no infrastructure management, scaling, and DDoS protection

A startup company is looking for a solution to cost-effectively run and access microservices without the operational overhead of managing infrastructure. The solution needs to be able to scale quickly to accommodate rapid changes in the volume of requests and protect against common DDoS attacks.

What is the MOST cost-effective solution that meets these requirements?

- A. Run the microservices in containers using AWS Elastic Beanstalk.
- B. Run the microservices in AWS Lambda behind an Amazon API Gateway.
- C. Run the microservices on Amazon EC2 instances in an Auto Scaling group.
- D. Run the microservices in containers using Amazon Elastic Container Service (Amazon ECS) backed by EC2 instances.

<details>
<summary>Show answer</summary>

**Answer: B**

AWS Lambda removes infrastructure management entirely and scales automatically with request volume, while you pay only for execution time. Fronting it with Amazon API Gateway provides a managed entry point that integrates with AWS Shield Standard for common DDoS protection. Elastic Beanstalk (A), EC2 Auto Scaling (C), and ECS on EC2 (D) all rely on EC2 instances that incur cost while idle and require more operational management, making them less cost-effective for spiky microservice traffic.
</details>

### Q103: Shared file storage for Windows servers integrated with Active Directory

A data processing facility wants to move a group of Microsoft Windows servers to the AWS Cloud. These servers require access to a shared file system that can integrate with the facility's existing Active Directory (AD) infrastructure for file and folder permissions. The solution needs to provide seamless support for shared files with AD users and groups located on premises. The solution must be highly available. The chosen solution should provide added security by supporting encryption at rest and in transit. Which storage solution would meet these requirements?

- A. An AWS S3 File Gateway joined to the existing AD domain.
- B. An Amazon FSx for Windows File Server file system joined to the existing AD domain.
- C. An Amazon Elastic File System (Amazon EFS) file system joined to an AWS managed AD domain.
- D. An Amazon S3 bucket mounted on Amazon EC2 instances in multiple Availability Zones running a Windows server.

<details>
<summary>Show answer</summary>

**Answer: B**

Amazon FSx for Windows File Server provides a fully managed native Windows SMB file system that integrates directly with Active Directory for file and folder permissions, supports Multi-AZ high availability, and offers encryption at rest and in transit. S3 File Gateway (A) presents S3 as a file share but is not a native Windows file system with AD-based NTFS permissions. EFS (C) supports only Linux/NFS, not Windows SMB. Mounting an S3 bucket on EC2 (D) is not a native shared Windows file system and lacks AD-integrated file permissions.
</details>

### Q104: API Gateway features for privacy, scale, and external customer security (Select THREE)

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

<details>
<summary>Show answer</summary>

**Answer: A, B, F**

A private endpoint keeps internal APIs reachable only from within the VPC, protecting the privacy of internal information. DDoS protection and throttling let API Gateway absorb and rate-limit millions of concurrent calls. An edge-optimized endpoint routes requests through the Amazon CloudFront network, improving security and latency for geographically distributed external customers. AWS Lambda (C) is a compute backend, not an API Gateway feature. NAT (D) is a networking construct, not an API Gateway feature. API Gateway cache (E) improves performance/cost but doesn't directly address these three stated concerns.
</details>

### Q105: Fastest way to migrate large data given limited VPN bandwidth and a 3-week deadline

You need to move a large amount of data from your data center to AWS in the next 3 weeks. You have a virtual private network (VPN) currently set up that provides 100 Mbps of bandwidth. Current saturation on the VPN is 75 percent, making it an untenable solution for your transfer. Which AWS service would be the FASTEST option for migrating the data within the 3-week timeframe?

- A. AWS Snow Family products
- B. AWS Storage Gateway
- C. AWS WAF
- D. Amazon CloudFront

<details>
<summary>Show answer</summary>

**Answer: A**

With the VPN already 75 percent saturated, transferring a large data set over the network within three weeks is impractical. AWS Snow Family devices let you physically ship the data to AWS, making them the fastest option under limited bandwidth. Storage Gateway (B) still relies on the constrained network link. AWS WAF (C) is a web application firewall and CloudFront (D) is a content delivery network — neither is a bulk data-migration service.
</details>

### Q106: Route 53 routing policy to split traffic 80/20 across two Regions

A network engineer wants to route 80 percent of web traffic to the ap-southeast-2 Region. The remaining 20 percent of traffic will be directed to the eu-west-1 Region. Which Route 53 routing policy is the best choice for this use case?

- A. Geoproximity routing
- B. Weighted routing
- C. Geolocation routing
- D. Simple routing
- E. Multivalue answer routing

<details>
<summary>Show answer</summary>

**Answer: B**

Weighted routing lets you assign relative weights to records so traffic is distributed by percentage — for example, 80 percent to one Region and 20 percent to another. Geoproximity (A) and geolocation (C) route based on the user's physical or geographic location, not fixed percentages. Simple routing (D) returns a single record with no traffic splitting. Multivalue answer routing (E) returns multiple healthy records at random but does not enforce specific percentage splits.
</details>

### Q107: Best service for system metrics, dashboards, and alert notifications

You are migrating from an on-premises data center to AWS. You need a monitoring system with system metrics such as CPU usage, access logs, dashboards, and alert notifications. In addition, you need to monitor AWS account-specific failures and notifications. Which service is BEST for this use case?

- A. Amazon CloudWatch
- B. AWS CloudTrail
- C. AWS Health
- D. AWS Health Dashboard

<details>
<summary>Show answer</summary>

**Answer: A**

Amazon CloudWatch collects system metrics (such as CPU usage), ingests logs, builds dashboards, and sends alert notifications through alarms — covering the core monitoring requirements. It can also receive AWS account events to surface failures and notifications. CloudTrail (B) records API activity for auditing, not metrics/dashboards. AWS Health (C) and the AWS Health Dashboard (D) report on service/account health events only and don't provide system metrics or custom dashboards.
</details>

### Q108: First resource to review when an uploaded image isn't processed in a serverless lab

A student is running the Build Serverless Architecture lab and is testing their architecture with a .jpeg file. Reviewing the web folder on the Amazon Simple Storage Service (Amazon S3) bucket, the student notices there is no .jpeg file. Which AWS resource should the student review first to solve this?

- A. AWS Lambda Web Function
- B. Amazon Simple Notification Service (Amazon SNS) topic
- C. AWS Lambda Mobile Function
- D. Amazon Simple Queue Service (Amazon SQS) queue

<details>
<summary>Show answer</summary>

**Answer: A**

In this serverless image-processing architecture, the Lambda Web Function is responsible for processing the uploaded image and writing the result (the .jpeg) into the web folder. If the file is missing, the Lambda Web Function is the first place to investigate for errors. SNS (B) and SQS (D) handle messaging/decoupling, not the file transformation, and the Lambda Mobile Function (C) is part of a different path in the lab.
</details>

### Q109: Efficiently restricting services across hundreds of accounts in many departments

Your organization has hundreds of accounts across many departments and needs to apply a restriction on specific services. Which of the following is an efficient way to implement the administration requirements?

- A. Assign AWS accounts to individual Organizational Units (OUs) in AWS Organizations and apply an Identity and Access Management (IAM) Policy to each OU.
- B. Assign the AWS accounts to Organizational Units (OUs) based on their departments in AWS Organizations and apply a service control policy at the OU level.
- C. Assign AWS accounts to Organizational Units (OUs) based on their departments in AWS Organizations and apply an Identity and Access Management (IAM) Policy at the root.
- D. Assign AWS accounts to individual Organizational Units (OUs) in AWS Organizations and apply a service control policy to each OU.

<details>
<summary>Show answer</summary>

**Answer: B**

Grouping accounts into OUs by department and attaching a service control policy (SCP) at the OU level lets you restrict specific services for whole groups of accounts at once — the most efficient approach for hundreds of accounts. SCPs (not IAM policies) are the AWS Organizations mechanism for restricting services across accounts, which eliminates A and C. Applying SCPs to individual OUs per account (D) defeats the efficiency of grouping by department.
</details>

### Q110: Required VPC components for a public web tier and a private database tier (Select TWO)

You are a Solutions Architect designing the architecture of the virtual private clouds (VPCs) for a web application using Amazon Elastic Compute Cloud (Amazon EC2) instances. The application consists of a web tier and a database tier. The web tier needs to be accessible to internet based clients; however, the database tier needs to be accessible to the web tier instances only. Which of the following are required to make this solution work and follow best practices? (Select TWO.)

- A. Create an internet gateway and attach it to the VPC.
- B. Create a Security Group for the web tier and a Security Group for the database tier.
- C. Create a public and private subnet for the web tier and another public and private subnet for the database tier.
- D. Create a NAT gateway and attach it to a public subnet.
- E. Create one VPC for the web tier and one VPC for the database tier.

<details>
<summary>Show answer</summary>

**Answer: A, B**

An internet gateway attached to the VPC is required so internet-based clients can reach the public web tier. Separate security groups for the web and database tiers let you allow internet (HTTPS) traffic to the web tier while restricting the database tier to accept traffic only from the web tier's security group. A NAT gateway (D) provides outbound internet access for private instances, which isn't the requirement here. Splitting tiers across separate VPCs (E) adds unnecessary complexity, and the subnet layout in C is not the minimal required pair compared with the gateway and security groups.
</details>

### Q111: Best services to ingest and process clickstream data for a recommendation engine (Select TWO)

An ecommerce website wants to process clickstream from their visitors and needs to store stream data while processing through a recommendation engine. What are the BEST AWS services for this use case? (Select TWO.)

- A. AWS Auto Scaling
- B. Amazon Kinesis Data Streams
- C. Amazon Kinesis Data Analytics
- D. AWS Elastic Beanstalk

<details>
<summary>Show answer</summary>

**Answer: B, C**

Amazon Kinesis Data Streams ingests and durably stores high-volume, real-time clickstream data. Amazon Kinesis Data Analytics processes that streaming data in real time so it can feed a recommendation engine. AWS Auto Scaling (A) adjusts compute capacity but doesn't ingest or process streams. Elastic Beanstalk (D) deploys and runs applications but is not a streaming data ingestion/processing service.
</details>

### Q112: Factors to consider when picking an AWS Region (Select TWO)

What are the factors to consider when picking an AWS Region? (Select TWO.)

- A. Latency to end users
- B. Local data regulations
- C. Operating system requirements
- D. Support for hybrid networking
- E. Programming language of your application

<details>
<summary>Show answer</summary>

**Answer: A, B**

Latency to end users drives Region choice because placing workloads closer to users reduces round-trip time. Local data regulations (data sovereignty/compliance) can legally require data to stay within a specific country or jurisdiction, dictating which Region you must use. Operating system requirements (C), hybrid networking support (D), and the application's programming language (E) are available across all Regions and are not differentiating factors when selecting one. (Service availability and pricing are also real factors but are not among the listed options.)
</details>

### Q113: Scalable connectivity for hundreds of VPCs with an uncertain future count

Your organization has grown very quickly and has applications deployed using hundreds of isolated virtual private clouds (VPCs). Now there is a need for connectivity between many VPCs. The company is uncertain how many future VPCs will be connected and is concerned about scalability of the chosen solution. As the Solutions Architect, which solution would you advise for them?

- A. AWS Site-to-Site VPN
- B. AWS Direct Connect
- C. VPC peering connections
- D. AWS Transit Gateway

<details>
<summary>Show answer</summary>

**Answer: D**

AWS Transit Gateway acts as a central hub that connects thousands of VPCs (and on-premises networks) through a single gateway, scaling cleanly as new VPCs are added and supporting transitive routing. VPC peering (C) is non-transitive and requires a full mesh — the number of connections grows quadratically, which does not scale to hundreds of VPCs. Site-to-Site VPN (A) and Direct Connect (B) are for connecting on-premises networks to AWS, not for scalable VPC-to-VPC connectivity.
</details>

### Q114: Benefits of using the AWS CDK with AWS CloudFormation (Select TWO)

Which of the following are benefits of using AWS Cloud Development Kit (AWS CDK) with AWS CloudFormation? (Select TWO.)

- A. Components are limited to a single user.
- B. Developers can use common programming languages.
- C. Bulk discounts are automatically applied to resource usage.
- D. Using AWS Cloud Development Kit (AWS CDK) does not require an AWS account or credentials.
- E. Developers can call preconfigured resources with proven defaults.

<details>
<summary>Show answer</summary>

**Answer: B, E**

The AWS CDK lets developers define infrastructure using familiar programming languages (TypeScript, Python, Java, C#, Go) instead of writing raw CloudFormation templates. It also provides constructs — preconfigured, reusable components with sensible defaults and AWS best practices baked in. CDK components can be shared, not limited to a single user (A). CDK does not change pricing or apply bulk discounts (C). Like any tool that deploys to AWS, it still requires an AWS account and credentials (D).
</details>

### Q115: Recommended service to deploy containers with limited operations knowledge

For customers that want to deploy containers on AWS, but do not have enough operations or deployment strategy knowledge, which service should you recommend?

- A. Amazon Elastic Compute Cloud (Amazon EC2)
- B. AWS Fargate
- C. Amazon Elastic Kubernetes Service (Amazon EKS)
- D. Amazon Elastic Container Service (Amazon ECS)

<details>
<summary>Show answer</summary>

**Answer: B**

AWS Fargate is a serverless compute engine for containers that removes the need to provision, patch, and manage the underlying servers or capacity — ideal for teams without deep operations or deployment expertise. Running containers on EC2 (A) requires managing the instances yourself. EKS (C) and ECS (D) are orchestration services, but with the EC2 launch type you still manage the cluster infrastructure; Fargate abstracts that away entirely.
</details>

### Q116: Consolidating backups across EC2, EFS, and RDS into one place

You have decided to consolidate backup in your organization. You currently have Amazon Elastic Compute Cloud (Amazon EC2), Amazon Elastic File System (Amazon EFS), and Amazon Relational Database Service (Amazon RDS) services running in your environment. What AWS service can you use to consolidate all backups to one place?

- A. AWS Backup
- B. AWS Elastic Beanstalk
- C. AWS S3
- D. AWS Secrets Manager

<details>
<summary>Show answer</summary>

**Answer: A**

AWS Backup is a fully managed service that centralizes and automates backups across AWS services — including EC2, EFS, and RDS — from a single console with unified backup policies. Elastic Beanstalk (B) deploys and runs applications, not backups. S3 (C) is object storage and would require building custom backup logic per service. Secrets Manager (D) stores and rotates secrets, not backups.
</details>

### Q117: Disaster recovery strategy that keeps only part of the stack running

While working on your disaster recovery plan (DR) you have decided to create a backup stack on AWS. Unfortunately, you need to keep some of the stack running but do not have the budget to keep the entire stack running. What type of recovery strategy is this?

- A. Backup and restore
- B. Multi-site active/active
- C. Pilot light
- D. AWS Snowball Edge Compute Optimized

<details>
<summary>Show answer</summary>

**Answer: C**

Pilot light keeps the core, critical components of the stack running (such as the database) while the rest stays switched off until needed — matching the requirement to keep only part of the stack running on a limited budget. Backup and restore (A) keeps nothing running and rebuilds from backups (highest RTO). Multi-site active/active (B) keeps the entire stack running in parallel (highest cost). Snowball Edge Compute Optimized (D) is a data-transfer/edge-compute device, not a DR strategy.
</details>

### Q118: Compute purchase option with a commitment that is flexible across instance families

Which compute purchase option includes a 1-year or 3-year commitment and is also flexible to use across Amazon Elastic Compute Cloud (Amazon EC2) instance families?

- A. EC2 Instance Savings Plans
- B. On-Demand Instances
- C. EC2 Spot Instances
- D. Compute Savings Plans

<details>
<summary>Show answer</summary>

**Answer: D**

Compute Savings Plans offer a 1-year or 3-year commitment and the most flexibility — discounts apply automatically across any instance family, size, Region, OS, and tenancy, and even to Fargate and Lambda. EC2 Instance Savings Plans (A) also require a commitment but are locked to a specific instance family in a chosen Region. On-Demand (B) has no commitment and no discount. Spot Instances (C) have no commitment and can be interrupted by AWS.
</details>

### Q119: Shared file system for two Linux applications in different Availability Zones

You have two Linux applications in different Availability Zones that must share a common file system. Which of the following is the best solution for this use case?

- A. AWS Storage Gateway
- B. Amazon FSx for Windows File Server
- C. Amazon Elastic File System (Amazon EFS)
- D. Amazon Simple Storage Service (Amazon S3)

<details>
<summary>Show answer</summary>

**Answer: C**

Amazon EFS is a fully managed NFS file system for Linux that can be mounted concurrently by instances across multiple Availability Zones, making it ideal for sharing a common file system between the two applications. FSx for Windows File Server (B) serves Windows/SMB workloads, not Linux. S3 (D) is object storage and cannot be natively mounted as a shared file system. Storage Gateway (A) is for hybrid on-premises-to-AWS storage integration, not multi-AZ shared file access within AWS.
</details>

### Q120: Cost-effective scaling for a video processing workload

A company has developed an application that processes photos and videos. When users upload files, a job processes them. The job can take up to 1 hour to process a long video. The company uses Amazon EC2 On-Demand instances to serve web servers and processing jobs. The processing layer must be able to scale, and during peak hours the systems are at full capacity. What should a solutions architect do so that the application will process all the jobs in the MOST cost-effective manner?

- A. Use a larger instance size in the Auto Scaling groups of the web layer and the processing layer.
- B. Use Spot Instances for the Auto Scaling groups of the web layer and the processing layer.
- C. Use an Amazon Simple Queue Service (Amazon SQS) standard queue between the web layer and the processing layer. Use a custom queue metric to scale the Auto Scaling group in the processing layer.
- D. Use AWS Lambda functions instead of EC2 instances and Auto Scaling groups. Increase the service quota so that sufficient concurrent functions can run at the same time.

<details>
<summary>Show answer</summary>

**Answer: C**

An SQS queue decouples the web layer from the processing layer so that jobs are buffered and never lost during peak demand. Scaling the processing Auto Scaling group on a custom queue-depth metric (such as the number of messages waiting) adds capacity only when there is a backlog and removes it when the queue drains, which is the most cost-effective fit. A larger instance size (A) raises cost continuously without addressing the bursty pattern. Spot Instances (B) can be reclaimed mid-job, which is risky for tasks that take up to an hour. Lambda (D) has a 15-minute execution limit, so it cannot run a 1-hour job.
</details>

### Q121: Combining solutions to ingest spiky IoT sensor data into a data lake (Select TWO)

A company is building a distributed application that sends sensor IoT data — including weather conditions and wind speed from wind turbines — to the AWS Cloud for further processing. The data is spiky and the application must be able to scale. The streaming data must be stored in a key-value database, then sent to a centralized data lake where it can be transformed, analyzed, and combined with diverse datasets to derive insights. Which combination of solutions accomplishes this with the LEAST operational overhead? (Select TWO.)

- A. Configure Amazon Kinesis to deliver streaming data to an Amazon S3 data lake.
- B. Use Amazon DocumentDB to store the IoT sensor data.
- C. Write AWS Lambda functions to deliver streaming data to Amazon S3.
- D. Use Amazon DynamoDB to store the IoT sensor data and turn on Kinesis Data Streams.
- E. Use Amazon Kinesis to deliver streaming data to Amazon Redshift and turn on Amazon Redshift Spectrum.

<details>
<summary>Show answer</summary>

**Answer: A, D**

DynamoDB is a fully managed, serverless key-value database that scales seamlessly with spiky traffic, and DynamoDB Streams/Kinesis Data Streams capture changes for downstream processing (D). Amazon Kinesis Data Firehose can then deliver the streaming data directly into an S3 data lake with no servers to manage (A). Writing custom Lambda functions (C) adds operational overhead compared with Kinesis's managed delivery. DocumentDB (B) is a document database, not the required key-value store, and isn't serverless. Redshift with Spectrum (E) is a data warehouse, not the S3 data lake described, and adds cluster management overhead.
</details>

### Q122: Member accounts accessing a shared services VPC with the least operational overhead

A large international company has a management account in AWS Organizations and hundreds of VPCs across member accounts. A shared services VPC hosts an application that all the member accounts must reach. Communication among all the VPCs should be allowed. How can the member accounts access the shared services VPC with the LEAST operational overhead?

- A. Create an Application Load Balancer with a target of the private IP address of the shared services VPC. Add a Certificate Authority Authorization (CAA) record for the ALB to Amazon Route 53. Point all requests for shared services in the VPC routing tables to that record.
- B. Create a peering connection between each of the VPCs and the shared services VPC.
- C. Create a Network Load Balancer across the Availability Zones in the shared services VPC. Create service consumer roles in IAM, and set endpoint connection acceptance to automatically accept. Create consumer endpoints in each division VPC and point to the Network Load Balancer.
- D. Create a VPN connection between each of the VPCs and the shared services VPC.

<details>
<summary>Show answer</summary>

**Answer: C**

A VPC endpoint service (AWS PrivateLink) backed by a Network Load Balancer lets the shared services VPC expose its application privately to hundreds of consumer VPCs across accounts, with auto-accepted endpoint connections to minimize manual approvals. This scales cleanly and keeps traffic on the AWS network. VPC peering (B) is non-transitive and requires a full mesh that grows quadratically — unmanageable for hundreds of VPCs. A VPN connection per VPC (D) has the same scaling and operational problem. The ALB/CAA approach (A) is not how cross-VPC private connectivity works (CAA records relate to certificate issuance, not routing).
</details>

### Q123: Choosing connectivity for consistent bandwidth in a hybrid cloud

Your organization is beginning a journey to the cloud and initially will implement a hybrid cloud infrastructure. The primary concern is consistency of bandwidth between the on-premises resources and AWS resources. Which option would you recommend for connectivity?

- A. Virtual Private Cloud (VPC) Peering Connections
- B. AWS Transit Gateway
- C. AWS Direct Connect
- D. AWS Site-to-Site VPN

<details>
<summary>Show answer</summary>

**Answer: C**

AWS Direct Connect provides a dedicated, private physical connection between on-premises and AWS, delivering consistent, predictable bandwidth and latency — exactly the concern stated. A Site-to-Site VPN (D) runs over the public internet, so bandwidth and latency vary. VPC Peering (A) connects VPCs to each other, not on-premises to AWS. Transit Gateway (B) is a routing hub for many VPCs and VPNs; it does not by itself provide the dedicated, consistent bandwidth link to on-premises.
</details>

### Q124: Disaster recovery model with an RTO in minutes at the lowest cost

Which disaster recovery model offers an RTO in minutes at the lowest cost?

- A. Fully working low-capacity standby
- B. Pilot light
- C. Backup and restore
- D. Multi-site active/active

<details>
<summary>Show answer</summary>

**Answer: B**

Pilot light keeps only the core elements (such as a replicated database) running while the rest of the environment is pre-provisioned but switched off, so it can be scaled up within minutes during failover — giving an RTO in minutes at low cost. A fully working low-capacity standby (warm standby, A) runs continuously and costs more. Backup and restore (C) is the cheapest but has the slowest RTO (hours). Multi-site active/active (D) gives the lowest RTO but is the most expensive.
</details>

### Q125: Metric that defines how often data must be backed up

Which metric defines how often data must be backed up?

- A. RTO
- B. RPO
- C. Available storage
- D. Amount of data

<details>
<summary>Show answer</summary>

**Answer: B**

Recovery Point Objective (RPO) is the maximum acceptable amount of data loss measured in time, which directly determines how frequently backups must be taken. Recovery Time Objective (RTO, A) defines how quickly a system must be restored after a disruption, not backup frequency. Available storage (C) and amount of data (D) are capacity considerations, not recovery metrics.
</details>

### Q126: Features of AWS Backup (Select THREE)

Which of the following are features of AWS Backup? (Select THREE.)

- A. Encrypted backups
- B. Works across every AWS service
- C. Works across multiple services
- D. Automated failover to read replicas
- E. Incremental backups
- F. Automated machine conversion

<details>
<summary>Show answer</summary>

**Answer: A, C, E**

AWS Backup centralizes and automates data protection: it encrypts backups (A) using AWS KMS, works across multiple supported AWS services such as EBS, EFS, RDS, DynamoDB, and Storage Gateway (C), and performs incremental backups so only changed data is stored after the first full backup (E). It does not work across *every* AWS service (B) — only supported ones. Automated failover to read replicas (D) is an RDS high-availability feature, not a backup feature. Automated machine conversion (F) relates to migration tooling, not AWS Backup.
</details>

### Q127: Making an existing RDS DB instance highly available with minimal RTO

What is the best way to make an existing Amazon RDS DB instance highly available and minimize your RTO?

- A. Run a secondary copy of your DB instance in another Region.
- B. Run a Multi-AZ DB instance in the same Region.
- C. Create a read replica in another Region.
- D. Create a read replica in the same Region.

<details>
<summary>Show answer</summary>

**Answer: B**

A Multi-AZ deployment maintains a synchronous standby replica in a different Availability Zone within the same Region and automatically fails over to it, providing high availability with an RTO of about a minute or two. A secondary copy in another Region (A) is a disaster recovery pattern with higher RTO and complexity. Read replicas (C, D) are for scaling read traffic and replicate asynchronously; promoting one is a manual process that does not provide automatic failover.
</details>

### Q128: Querying DynamoDB attributes that are not key columns

An existing application built on Amazon DynamoDB needs to query attributes that are not defined in key columns. Which of the following will accommodate the change most efficiently?

- A. No change is required since you can query any column on DynamoDB.
- B. Build a local secondary index.
- C. Build a global secondary index.
- D. Create another DynamoDB table, define the required keys, and then copy the data to the new table.

<details>
<summary>Show answer</summary>

**Answer: C**

A global secondary index (GSI) lets you query a table using an alternate partition key (and optional sort key) that differs from the base table's keys, which is exactly what's needed to query non-key attributes efficiently on an existing table. DynamoDB cannot efficiently query arbitrary attributes without an index (A is false). A local secondary index (B) must share the same partition key as the base table and can only be created at table creation time, so it can't be added to an existing table. Creating a new table and copying data (D) is far more operational effort than adding a GSI.
</details>

### Q129: Static public IP that persists across stop and start

You are deploying an application into an AWS virtual private cloud (VPC). Internet hosts must connect to a static public IP address that is persistent across stop and start. Which of the following options should be used?

- A. An Elastic IP address
- B. A public IP address
- C. A private IP address
- D. A NAT gateway

<details>
<summary>Show answer</summary>

**Answer: A**

An Elastic IP address is a static public IPv4 address that you allocate to your account and associate with an instance; it remains the same across stops and starts, satisfying the persistent-address requirement. A default public IP address (B) is released and reassigned when an instance is stopped and started, so it is not persistent. A private IP address (C) is not reachable from internet hosts. A NAT gateway (D) enables outbound internet access for private instances; it is not a static inbound address for the application.
</details>

### Q130: What a connection to a transit gateway is called

What is a connection to a transit gateway called?

- A. Virtual Private Network (VPN)
- B. Attachment
- C. Route
- D. Virtual Private Cloud (VPC)

<details>
<summary>Show answer</summary>

**Answer: B**

In AWS Transit Gateway terminology, each connection from a VPC, VPN, Direct Connect gateway, or peered transit gateway is called an *attachment*. A VPN (A) is one type of resource you attach, not the generic term for the connection. A route (C) is an entry in a route table that directs traffic, not the connection itself. A VPC (D) is a resource that you attach to the transit gateway via an attachment.
</details>

### Q131: True statements about VPC peering connections (Select TWO)

What is TRUE about Virtual Private Cloud (VPC) peering connections? (Select TWO.)

- A. Connections are one-to-many.
- B. Connections are one-to-one.
- C. Connections require a transit gateway.
- D. Connections can span accounts.
- E. Connections are transitive.

<details>
<summary>Show answer</summary>

**Answer: B, D**

A VPC peering connection is a one-to-one link between exactly two VPCs (B), and the two VPCs can belong to different AWS accounts — and even different Regions (D). Peering is not one-to-many (A); each pair needs its own connection. It does not require a transit gateway (C) — peering is a direct relationship. Peering is non-transitive (E), so traffic cannot flow through an intermediate VPC to reach a third one.
</details>

### Q132: SQS queue type that provides at-least-once delivery

Which type of Amazon Simple Queue Service (Amazon SQS) queue provides at-least-once delivery?

- A. First in, first out (FIFO) queue
- B. Standard queue
- C. Dead-letter queue
- D. Long polling

<details>
<summary>Show answer</summary>

**Answer: B**

Standard queues guarantee at-least-once delivery, which means a message may occasionally be delivered more than once, in exchange for nearly unlimited throughput and best-effort ordering. FIFO queues (A) provide exactly-once processing and strict ordering, not at-least-once. A dead-letter queue (C) is a destination for messages that can't be processed successfully, not a delivery model. Long polling (D) is a message-retrieval technique, not a queue type.
</details>

### Q133: Advantage of long polling over short polling in SQS

What is an advantage of long polling compared to short polling while using Amazon Simple Queue Service (Amazon SQS)?

- A. Long polling provides an immediate response from a ReceiveMessage call.
- B. Long polling is more stable when using a single thread to poll multiple queues.
- C. Long polling reduces the cost of using Amazon SQS by reducing the number of empty responses and false empty responses.
- D. Long polling reduces cost by only sampling a subset of Amazon SQS servers.

<details>
<summary>Show answer</summary>

**Answer: C**

Long polling waits until a message is available (or the timeout expires) before responding, which eliminates most empty responses and avoids false empty responses by querying all SQS servers. Fewer empty `ReceiveMessage` calls mean lower request costs. An immediate response (A) describes short polling, not long polling. Long polling is not about single-thread stability (B). Sampling only a subset of servers (D) describes short polling, which is what causes false empty responses.
</details>

### Q134: A feature of Amazon Simple Notification Service (Amazon SNS)

What is a feature of Amazon Simple Notification Service (Amazon SNS)?

- A. Amazon SNS exchanges messages through a polling model.
- B. Amazon SNS can send messages to distributed components of applications without requiring each component to be concurrently available.
- C. Amazon SNS can push messages to multiple subscribers.
- D. Amazon SNS keeps messages persistent.

<details>
<summary>Show answer</summary>

**Answer: C**

Amazon SNS is a publish/subscribe (pub/sub) service that pushes messages to multiple subscribers (such as SQS queues, Lambda functions, HTTP endpoints, and email) at once. It uses a push model, not polling (A) — polling describes SQS. Decoupling components that don't need to be concurrently available (B) and keeping messages persistent in a queue (D) describe Amazon SQS, not SNS.
</details>

### Q135: Potential benefits of an Amazon CloudFront distribution (Select TWO)

What are the potential benefits of implementing an Amazon CloudFront distribution? (Select TWO.)

- A. Increased application security
- B. Two global static IP addresses
- C. Automatic redundancy for all application content
- D. Reduced latency for access to application content
- E. On-premises data caching

<details>
<summary>Show answer</summary>

**Answer: A, D**

CloudFront caches content at edge locations close to users, reducing latency for accessing application content (D), and it increases application security by integrating with AWS WAF, AWS Shield, and HTTPS/TLS at the edge (A). Two global static IP addresses (B) is a feature of AWS Global Accelerator, not CloudFront. CloudFront caches content but does not provide automatic redundancy for *all* application content (C). It caches at AWS edge locations, not on-premises (E).
</details>

### Q136: Benefit of AWS Outposts servers versus an Outposts rack

What is a benefit of choosing AWS Outposts servers when compared to an Outposts rack?

- A. More AWS services are available on Outposts servers
- B. Pool compute and storage capacity between multiple Outposts servers
- C. A smaller-sized device can be placed in your own rack
- D. You don't need to host your Outposts servers in a data center

<details>
<summary>Show answer</summary>

**Answer: C**

Outposts servers are individual 1U or 2U devices that fit into your existing rack, making them ideal for smaller spaces such as retail stores or branch offices where a full Outposts rack won't fit (C). Outposts racks actually support more AWS services and larger capacity (A is a rack benefit). Only racks let you pool compute and storage across multiple units (B). Both form factors still require a suitable on-premises facility with power, cooling, and network (D is false).
</details>

### Q137: Preventing duplicate processing of records from a queue

An API receives a high volume of sensor data. The data is written to a queue before being processed to produce trend analysis and forecasting reports. With the current architecture, some data records are being received and processed more than once. How can a solutions architect modify the architecture to ensure that duplicate records are not processed?

- A. Configure the API to send the records to Amazon Kinesis Data Streams.
- B. Configure the API to send the records to Amazon Kinesis Data Firehose.
- C. Configure the API to send the records to Amazon Simple Notification Service (Amazon SNS).
- D. Configure the API to send the records to an Amazon Simple Queue Service (Amazon SQS) FIFO queue.

<details>
<summary>Show answer</summary>

**Answer: D**

An SQS FIFO queue provides exactly-once processing and deduplication (using a deduplication ID within a 5-minute interval), which guarantees that duplicate records are not processed. SQS standard queues, Kinesis Data Streams (A), and Kinesis Data Firehose (B) all provide at-least-once delivery, so consumers can see duplicates. SNS (C) is a pub/sub push service that also delivers at least once and does not deduplicate.
</details>

### Q138: DNS failover to a static website hosted on Amazon S3

Which AWS service can you implement to create a DNS failover to a static website hosted on Amazon S3?

<details>
<summary>Show answer</summary>

**Answer: Amazon Route 53 with the failover routing policy**

Route 53 failover routing uses health checks on the primary endpoint and automatically redirects DNS queries to a secondary resource — such as a static website hosted in an S3 bucket — when the primary becomes unhealthy. This provides DNS-level failover for high availability.
</details>

### Q139: S3 storage class for cold data

What Amazon S3 storage class would you choose to store cold data?

<details>
<summary>Show answer</summary>

**Answer: Amazon S3 Glacier**

S3 Glacier storage classes are purpose-built for cold (rarely accessed) archival data at the lowest cost. Depending on retrieval needs you choose Glacier Instant Retrieval, Glacier Flexible Retrieval, or Glacier Deep Archive — all far cheaper than S3 Standard for data that is seldom accessed.
</details>

### Q140: Parallel file system for frequently accessed data

What storage solution would you choose if you needed a parallel file system to store frequently accessed data?

<details>
<summary>Show answer</summary>

**Answer: Amazon FSx for Lustre**

Amazon FSx for Lustre is a fully managed, high-performance parallel file system designed for compute-intensive workloads (HPC, machine learning, media processing) that need fast access to frequently used data with high throughput and low latency.
</details>

### Q141: Synchronous data replication across Availability Zones for Amazon RDS

If you need to design and implement synchronous data replication across Availability Zones for your Amazon RDS instances, what is the best solution?

<details>
<summary>Show answer</summary>

**Answer: Multi-AZ deployment**

An RDS Multi-AZ deployment synchronously replicates data to a standby instance in a different Availability Zone and automatically fails over to it, providing high availability and durability within a Region.
</details>

### Q142: Asynchronous data replication to an Amazon RDS instance in another Region

If you need to design and implement asynchronous data replication to another Amazon RDS instance in another AWS Region, what is the best solution?

<details>
<summary>Show answer</summary>

**Answer: Add a read replica (cross-Region read replica)**

RDS read replicas use asynchronous replication and can be created in another AWS Region. They offload read traffic and can be promoted to a standalone database, supporting cross-Region scaling and disaster recovery.
</details>

### Q143: Encrypting an existing unencrypted Amazon RDS MySQL database

A company's legacy application is currently relying on a single-instance Amazon RDS MySQL database without encryption. Due to new compliance requirements, all existing and new data in this database must be encrypted.

How should this be accomplished?

- A. Create an Amazon S3 bucket with server-side encryption turned on. Move all the data to Amazon S3. Delete the RDS instance.
- B. Configure RDS Multi-AZ mode with encryption at rest turned on. Perform a failover to the standby instance to delete the original instance.
- C. Take a snapshot of the RDS instance. Create an encrypted copy of the snapshot. Restore the RDS instance from the encrypted snapshot.
- D. Create an RDS read replica with encryption at rest turned on. Promote the read replica to primary and switch the application over to the new primary. Delete the old RDS instance.

<details>
<summary>Show answer</summary>

**Answer: C — Take a snapshot, create an encrypted copy, and restore from it**

You can only enable encryption for an RDS DB instance at creation time. To encrypt an existing unencrypted database, take a snapshot, create an encrypted copy of that snapshot, and restore a new DB instance from the encrypted snapshot.

- **A is wrong:** RDS MySQL already supports encryption, and Amazon S3 is not a viable replacement for a relational database.
- **B is wrong:** You cannot create an encrypted standby DB instance from an unencrypted primary.
- **D is wrong:** You cannot create an encrypted read replica of an unencrypted DB instance (nor an unencrypted replica of an encrypted one).
</details>

### Q144: Encrypting future Amazon RDS automated backups

A company currently operates a web application backed by an Amazon RDS MySQL database. It has automated backups that run daily and are not encrypted. A security audit requires future backups to be encrypted and the unencrypted backups to be destroyed. The company will make at least one encrypted backup before destroying the old backups.

What should be done to set up encryption for future backups?

- A. Turn on default encryption for the Amazon S3 bucket where backups are stored.
- B. Modify the backup section of the database configuration to toggle the Enable encryption check box.
- C. Create a snapshot of the database. Copy it to an encrypted snapshot. Restore the database from the encrypted snapshot.
- D. Configure an encrypted read replica on RDS for MySQL. Promote the encrypted read replica to primary. Remove the original database instance.

<details>
<summary>Show answer</summary>

**Answer: C — Snapshot the database, copy it to an encrypted snapshot, and restore from it**

You cannot encrypt an existing unencrypted DB instance directly. Take a snapshot, create an encrypted copy of that snapshot, and restore a new (encrypted) DB instance from it — its automated backups will then also be encrypted.

- **A is wrong:** Users do not have access to the S3 bucket that stores RDS automated backups, so encryption cannot be turned on there.
- **B is wrong:** There is no option to toggle encryption on an existing DB instance; encryption can only be configured when the instance is created.
- **D is wrong:** You cannot create an encrypted read replica of an unencrypted DB instance.
</details>

### Q145: Immutable infrastructure with safe, testable deployments (Select TWO)

A company wants to build an immutable infrastructure for its software application. The company wants to test the software applications before sending traffic to them. The company needs an efficient solution that limits the effects of application bugs.

Which combination of steps should a solutions architect recommend? (Select TWO.)

- A. Use AWS CloudFormation to update the production infrastructure and roll back the stack if the update fails.
- B. Apply Amazon Route 53 weighted routing to test the staging environment and gradually increase the traffic as the tests pass.
- C. Apply Amazon Route 53 failover routing to test the staging environment and fail over to the production environment if the tests pass.
- D. Use AWS CloudFormation with a parameter set to the staging value in a separate environment other than the production environment.
- E. Use AWS CloudFormation to deploy the staging environment with a snapshot deletion policy and reuse the resources in the production environment.

<details>
<summary>Show answer</summary>

**Answer: B and D**

- **B is correct:** Route 53 weighted routing sends a percentage of traffic to multiple resources, enabling a blue/green deployment strategy that deploys predictably and rolls back quickly if tests fail.
- **D is correct:** Using a separate environment (driven by a CloudFormation parameter) lets the company test changes before deploying them to production.

- **A is wrong:** Updating the production infrastructure directly would impact applications and users; a separate environment is needed to test before deploying updates.
- **C is wrong:** Failover routing is for disaster recovery — routing to a healthy resource when one becomes unhealthy — not for testing deployments.
- **E is wrong:** A snapshot deletion policy does not support all resource types and may not retain all required resources.
</details>

### Q146: Boost performance and availability for a UDP streaming platform while keeping a custom DNS

A media streaming service aims to boost the performance and availability of its global platform, which relies on UDP for data transmission. The company needs a solution that allows for swift regional failover in the event of a regional outage, while still using their proprietary DNS system.

Which AWS service should they implement?

- A. AWS Global Accelerator
- B. AWS Elastic Load Balancing (ELB)
- C. Amazon CloudFront

<details>
<summary>Show answer</summary>

**Answer: A**

AWS Global Accelerator is tailored to enhance the performance and availability of applications by leveraging the AWS global network. It is ideal for UDP-based applications, such as media streaming, as it offers rapid regional failover and improves performance by minimizing latency and jitter. It provides static anycast IP addresses, so the company can keep its own proprietary DNS system.

- **B is wrong:** ELB distributes traffic within a single Region and does not provide cross-region failover or global network acceleration.
- **C is wrong:** CloudFront is a CDN that does not support UDP traffic; it is designed for cacheable HTTP/HTTPS content.
</details>

### Q147: Most efficient method to transfer a 2 GB file daily to Amazon S3 from a remote location

A company needs to transfer a 2 GB compressed data file daily to Amazon S3 from a remote location. What is the most efficient method to achieve this?

- A. Perform a single upload operation
- B. Utilize only multipart upload without acceleration
- C. Use multipart upload with Amazon S3 Transfer Acceleration

<details>
<summary>Show answer</summary>

**Answer: C**

To efficiently upload large files to Amazon S3, the combination of multipart upload and Amazon S3 Transfer Acceleration is recommended. Transfer Acceleration leverages Amazon CloudFront's global edge network to optimize the transfer path, reducing latency and increasing speed. Multipart upload allows the file to be divided into smaller parts, which can be uploaded concurrently, enhancing the upload speed and reliability.

- **A is wrong:** A single upload operation is slow for large files and must restart from scratch if it fails.
- **B is wrong:** Multipart upload alone improves reliability and concurrency but, without Transfer Acceleration, it does not optimize the network path from a remote location.
</details>

### Q148: Secure archival for a financial institution with strict compliance using Amazon S3 Glacier

You are tasked with designing a secure archival system for a financial institution that must adhere to strict compliance regulations. Which approach would you recommend using Amazon S3 Glacier to ensure that data access complies with these regulations?

- A. Store data in Amazon S3 Glacier and apply an Amazon S3 Bucket Policy for compliance
- B. Use Amazon S3 Glacier and manage access with IAM roles for compliance
- C. Implement Amazon S3 Glacier Vault Lock to apply a vault lock policy for compliance on archived data

<details>
<summary>Show answer</summary>

**Answer: C**

Amazon S3 Glacier is suitable for secure and cost-effective data archiving. To meet compliance regulations, the Amazon S3 Glacier Vault Lock feature should be used. This feature allows you to apply a vault lock policy that enforces compliance requirements such as 'write once, read many' (WORM), ensuring the policy is immutable once locked, thus making it the appropriate choice for regulatory compliance.

- **A is wrong:** A bucket policy controls access permissions but can be modified at any time, so it cannot enforce immutable, tamper-proof compliance controls.
- **B is wrong:** IAM roles manage who can access the data but do not enforce a WORM model or prevent the policy from being changed later.
</details>

### Q149: Low-latency handling of a surge in real-time tracking data updates and retrievals

A logistics company needs to optimize its application to handle a surge in real-time tracking data as its fleet size doubles. Which solution ensures high performance with low latency for frequent data updates and retrievals?

- A. Configure Multi-AZ for the main RDS instance to improve write durability and distribute read operations during high demand periods
- B. Transition the tracking data to Amazon OpenSearch Service, leveraging its geospatial capabilities for real-time data indexing and visualization
- C. Deploy an Amazon ElastiCache for Redis cluster to cache tracking data, using a TTL-based eviction policy to manage data freshness and reduce database load

<details>
<summary>Show answer</summary>

**Answer: C**

Implementing Amazon ElastiCache for Redis offers a scalable and low-latency solution for managing frequent data updates and retrievals. Redis, being an in-memory data store, provides sub-millisecond response times, which is crucial for real-time tracking applications. By caching tracking data in Redis with a time-to-live (TTL) eviction policy, the system can offload the primary database, ensuring quick access to the most recent data.

- **A is wrong:** RDS Multi-AZ improves availability and durability, but the standby is not used to serve reads, so it does not reduce read latency for a surge in traffic.
- **B is wrong:** OpenSearch is built for search, analytics, and visualization, not for the sub-millisecond, high-frequency read/write caching that real-time tracking requires.
</details>

### Q150: Secure DNS resolution from AWS to on-premises services for an internal application

A financial institution is hosting an internal application on AWS that requires secure DNS resolution for its on-premises services. What is the BEST method to achieve this?

- A. Implement a Route 53 private hosted zone for the on-premises domain and link it to the VPC for DNS resolution
- B. Set up a Route 53 Resolver inbound endpoint and enable recursive DNS resolution within the VPC for on-premises services
- C. Establish a Route 53 Resolver outbound endpoint and configure forwarding rules to direct DNS queries for on-premises domains to the on-premises DNS server

<details>
<summary>Show answer</summary>

**Answer: C**

To securely resolve DNS queries from AWS to on-premises services, setting up a Route 53 Resolver outbound endpoint is recommended. This allows DNS queries from resources in the VPC to be forwarded to the on-premises DNS server over a site-to-site VPN. By configuring forwarding rules, specific domain names can be directed to the on-premises DNS server, ensuring secure and efficient DNS resolution. This method adheres to AWS best practices for hybrid DNS setups.

- **A is wrong:** A private hosted zone resolves records hosted within Route 53 itself; it cannot forward queries to an external on-premises DNS server.
- **B is wrong:** An inbound endpoint handles DNS queries coming *from* on-premises *into* the VPC — the opposite direction of what is needed here.
</details>

### Q151: Data transfer cost considerations when setting up Amazon RDS read replicas

A company is planning to set up read replicas for their Amazon RDS database to improve read performance. What should they consider regarding data transfer costs?

- A. Data transfer costs apply when replicating data to read replicas in different AWS Regions.
- B. Data transfer costs apply when replicating data to read replicas within the same AWS Region.
- C. Data transfer costs apply when replicating data to read replicas within the same Availability Zone.

<details>
<summary>Show answer</summary>

**Answer: A**

Data transfer between Amazon RDS instances within the same AWS Region does not incur charges. However, if read replicas are set up in different AWS Regions, data transfer costs will apply for the cross-Region replication traffic.

- **B is wrong:** Replication to a read replica within the same Region (including across Availability Zones) is free of data transfer charges.
- **C is wrong:** Replication within the same Availability Zone is also free; charges only start with cross-Region replication.
</details>

### Q152: Minimizing S3 data transfer costs for a video streaming traffic surge

A media company is experiencing a surge in traffic to its video streaming service, leading to increased data transfer costs from their Amazon S3 storage. As a Solutions Architect, what strategy can you implement to minimize these costs while ensuring quick content delivery to users?

- A. Migrate the data to Amazon EFS and serve it directly from there
- B. Deploy Amazon CloudFront to cache and deliver content from Amazon S3 efficiently
- C. Use Amazon S3 Transfer Acceleration to speed up data transfer to S3

<details>
<summary>Show answer</summary>

**Answer: B**

Implementing Amazon CloudFront as a content delivery network (CDN) in front of Amazon S3 can significantly reduce data transfer costs and improve delivery speed. CloudFront caches content at edge locations, which decreases the load on S3 and reduces latency for end-users. This approach is more cost-effective than directly serving content from S3, as it eliminates data transfer charges from S3 to CloudFront.

- **A is wrong:** EFS is a file system for compute workloads, not a content delivery solution for streaming to internet users, and it would not reduce delivery costs or latency.
- **C is wrong:** S3 Transfer Acceleration speeds up *uploads* into S3; it does not cache content or reduce the cost of delivering content out to viewers.
</details>

### Q153: Shared file system for both Windows and Linux servers with NTFS permissions and AD integration

A company needs a shared file system that can be accessed by both Windows and Linux servers on AWS, while maintaining Windows-specific features like NTFS permissions and integration with Active Directory. Which AWS service should they use?

- A. Use Amazon FSx for Windows File Server to provide a shared file system accessible via SMB from both Windows and Linux servers
- B. Implement Amazon EFS and connect using NFS from both Windows and Linux servers
- C. Set up Amazon FSx for Lustre and access it with POSIX-compliant clients from both systems

<details>
<summary>Show answer</summary>

**Answer: A**

Amazon FSx for Windows File Server is designed to provide a fully managed Windows file system that supports the SMB protocol, NTFS permissions, and Active Directory integration. This makes it suitable for environments where both Windows and Linux servers need to access a shared file system, as Linux can also connect using SMB clients.

- **B is wrong:** EFS is an NFS file system aimed at Linux workloads and does not provide NTFS permissions or native Active Directory integration; Windows does not natively mount NFS.
- **C is wrong:** FSx for Lustre is a high-performance file system for HPC/ML workloads and lacks NTFS permissions and Active Directory integration.
</details>

### Q154: Ordered processing of 1000 tracking notifications per second with Amazon SQS

A logistics company needs to ensure that their package tracking notifications are processed in the exact order they are received, with a peak throughput of 1000 notifications per second. Which solution should they implement using Amazon SQS?

- A. Deploy an Amazon SQS standard queue for processing notifications
- B. Implement an Amazon SQS FIFO queue with batch processing of 4 messages per operation to handle the peak load
- C. Use a single Amazon SQS FIFO queue without batch processing

<details>
<summary>Show answer</summary>

**Answer: B**

Amazon SQS FIFO queues guarantee that messages are processed in the order they are sent. By using FIFO queues in batch mode with 4 messages per operation, the system can handle up to 1200 messages per second, which satisfies the requirement of processing 1000 messages per second.

- **A is wrong:** Standard queues offer best-effort ordering only, so they cannot guarantee the exact order required.
- **C is wrong:** A FIFO queue without batching is limited to 300 messages per second, which falls short of the 1000-per-second requirement.
</details>

### Q155: Modernizing application infrastructure while keeping sensitive data on premises

A financial institution wants to modernize its application infrastructure while ensuring that all sensitive customer data remains within its own data centers. Which AWS service should the institution implement to achieve this goal?

- A. Implement an AWS Snowball Edge for on-site data processing and periodically transfer data to Amazon S3 for analysis
- B. Deploy an AWS Outposts rack in the data center and use Amazon EKS Anywhere to manage Kubernetes clusters locally, integrating with AWS services
- C. Set up Amazon ECS with Fargate in an AWS Local Zone and connect it to the data center via a secure VPN

<details>
<summary>Show answer</summary>

**Answer: B**

The best solution is to deploy an AWS Outposts rack within the financial institution's data center and utilize Amazon EKS Anywhere on Outposts. This setup allows the institution to run Kubernetes clusters on-premises, leveraging AWS APIs and services while ensuring that all data stays within the local data center, thus meeting data residency and compliance requirements.

- **A is wrong:** Snowball Edge is for offline data transfer and edge processing; periodically moving data to Amazon S3 would take sensitive data out of the data center, violating the residency requirement.
- **C is wrong:** A Local Zone is AWS-owned infrastructure outside the institution's data center, so data would not remain within their own facilities.
</details>

### Q156: Resources created when copying an AMI from one Region to another

A company has duplicated an Amazon Machine Image (AMI) from Region A to Region B. What resources are now present in Region B?

- A. One AMI and one snapshot are present in Region B
- B. One EC2 instance and one AMI are present in Region B
- C. One EC2 instance, one AMI, and one snapshot are present in Region B

<details>
<summary>Show answer</summary>

**Answer: A**

Copying an AMI to another Region creates a new AMI and an associated snapshot in the target Region. It does not create an EC2 instance automatically. The copied AMI can be used to launch new instances in Region B as needed.

- **B is wrong:** Copying an AMI does not launch an EC2 instance, and it produces a snapshot (not just an AMI).
- **C is wrong:** No EC2 instance is created by the copy operation; only the AMI and its backing snapshot are present.
</details>

### Q157: Centralized multi-account governance with shared networking and low operational complexity

A tech startup aims to separate its development, staging, and production environments by utilizing distinct AWS accounts. They seek a solution that allows for centralized management of network resources and automatic application of security policies when new accounts are established. Which approach best fulfills these requirements while keeping operational complexity low?

- A. Leverage AWS Organizations to create accounts and a shared networking account with a central VPC. Share VPC subnets using AWS RAM and manually enforce policies with service control policies (SCPs)
- B. Utilize AWS Service Catalog to establish approved VPC templates. Deploy a separate VPC for each account from the catalog and apply networking policies using AWS Config conformance packs
- C. Implement AWS Control Tower to manage account creation and governance. Set up a centralized VPC in a shared networking account and distribute its subnets to other accounts using AWS Resource Access Manager (AWS RAM)

<details>
<summary>Show answer</summary>

**Answer: C**

AWS Control Tower is tailored for managing multi-account AWS setups, offering automated account creation with integrated security and compliance features like Service Control Policies (SCPs) and AWS Config rules. By setting up a centralized VPC in a shared networking account, you can efficiently manage and distribute network resources across accounts using AWS Resource Access Manager (AWS RAM). This strategy reduces redundancy, cuts costs, and ensures uniform security practices with minimal operational burden.

- **A is wrong:** Using AWS Organizations alone with *manual* SCP enforcement adds operational overhead and lacks the automated guardrails and account provisioning that Control Tower provides.
- **B is wrong:** Deploying a separate VPC per account via Service Catalog increases redundancy and management effort, the opposite of the centralized, low-complexity goal.
</details>

### Q158: Retaining EC2 application logs for analysis after Auto Scaling instance termination

A company is experiencing issues with accessing application logs after their EC2 instances, managed by an Auto Scaling Group, are terminated. What is the best approach to ensure log availability for analysis even after instance termination?

- A. Schedule a script to transfer logs to Amazon S3 using AWS Lambda before instance termination
- B. Deploy the Amazon CloudWatch Logs agent on EC2 instances to stream logs to Amazon CloudWatch Logs
- C. Create an AMI of the EC2 instance to preserve logs before termination

<details>
<summary>Show answer</summary>

**Answer: B**

To maintain access to logs after an EC2 instance is terminated, deploying the Amazon CloudWatch Logs agent on each instance is recommended. This agent streams logs to Amazon CloudWatch Logs, ensuring they are available for review and analysis regardless of the instance's lifecycle. This method is both efficient and cost-effective.

- **A is wrong:** Relying on a script triggered before termination is fragile — abrupt or failed terminations can skip the transfer, causing log loss.
- **C is wrong:** Creating an AMI captures a point-in-time image and is not a practical way to continuously collect and analyze logs; it adds storage cost and operational overhead.
</details>

### Q159: Cutting costs and improving performance for a read-intensive Lambda + API Gateway + Aurora app

A company runs a read-intensive application using AWS Lambda, Amazon API Gateway, and Amazon Aurora. They want to cut costs and enhance performance with minimal adjustments. What is the most effective strategy?

- A. Implement caching in Amazon API Gateway
- B. Deploy additional Amazon Aurora Read Replicas
- C. Use in-memory caching with AWS Lambda

<details>
<summary>Show answer</summary>

**Answer: A**

Utilizing caching in Amazon API Gateway can greatly decrease the number of requests that reach your backend services by storing responses temporarily. This is advantageous for read-heavy applications where data can be slightly outdated, as it lessens the load on the database and boosts response times, leading to cost and performance optimization.

- **B is wrong:** Adding Aurora Read Replicas increases cost and is a larger architectural change; it does not reduce the number of backend calls the way response caching does.
- **C is wrong:** Lambda is stateless and short-lived, so in-memory caching is not reliably shared across invocations and would not consistently serve cached responses.
</details>

### Q160: Cost-effective NFS-compatible storage that auto-tiers infrequently accessed data

A company needs a cost-effective cloud storage solution that supports NFS compatibility and automatically moves less frequently accessed data to cheaper storage. Which option should they choose?

- A. Implement AWS Storage Gateway File Gateway to provide an NFS-compatible file share. Store data in Amazon S3 and utilize S3 Lifecycle policies to automatically transition less frequently accessed data to lower-cost storage classes.
- B. Set up Amazon Elastic File System (Amazon EFS) with the One Zone-IA storage class. Use AWS DataSync for NFS data migration and enable lifecycle management for automatic tiering of infrequently accessed files.
- C. Use AWS Storage Gateway Volume Gateway in cached mode, attaching it as a block device to an on-premises server with NFS. Store snapshots in Amazon S3 Glacier Deep Archive and manage tiering with AWS Backup.

<details>
<summary>Show answer</summary>

**Answer: A**

AWS Storage Gateway File Gateway offers an NFS-compatible interface, allowing seamless integration with existing applications. It stores data in Amazon S3, which is both scalable and cost-effective. By using S3 Lifecycle policies, data can be automatically transitioned to more economical storage classes as it becomes less frequently accessed, optimizing costs while maintaining NFS compatibility.

- **B is wrong:** EFS is more expensive than S3 for this use case, and Volume/DataSync-based migration adds complexity; EFS lifecycle management tiers within EFS rather than to the cheapest S3 classes.
- **C is wrong:** Volume Gateway presents block (iSCSI) storage, not an NFS file share, and using Glacier Deep Archive snapshots does not provide on-demand access to less frequently used files.
</details>

### Q161: Efficiently synchronizing media files from local storage to Amazon EFS during migration

A company needs to efficiently synchronize newly generated media files from their local storage to an Amazon EFS file system as part of their cloud migration strategy. What is the MOST effective method to achieve this?

- A. Deploy an AWS DataSync agent on the local server to access the storage system. Use AWS Direct Connect with a PrivateLink interface VPC endpoint to transfer data directly to Amazon EFS. Schedule DataSync tasks to update the EFS file system daily.
- B. Set up an AWS DataSync agent on the local server to access the storage system. Transfer data to an Amazon S3 bucket using a public VIF over AWS Direct Connect. Use AWS Lambda to move files from S3 to Amazon EFS.
- C. Install an AWS DataSync agent on the local server to access the storage system. Transfer data to an Amazon S3 bucket using AWS Direct Connect and a VPC gateway endpoint. Use AWS Lambda to move files from S3 to Amazon EFS.

<details>
<summary>Show answer</summary>

**Answer: A**

To efficiently synchronize media files from on-premises storage to Amazon EFS, deploying AWS DataSync is optimal. By installing a DataSync agent on the local server, data can be securely transferred to Amazon EFS using AWS Direct Connect with a PrivateLink interface VPC endpoint. This setup ensures a streamlined and secure data transfer process, and scheduling DataSync tasks allows for regular updates to the EFS file system.

- **B is wrong:** Routing through an S3 bucket and then using Lambda to move files into EFS adds unnecessary hops and complexity; a public VIF also does not provide the private connectivity of a PrivateLink endpoint.
- **C is wrong:** It also detours through S3 with Lambda, and a VPC gateway endpoint only supports S3 and DynamoDB — not a direct, private path to EFS.
</details>

### Q162: Securely giving private subnet instances outbound internet access

How can instances located in private subnets be configured to access the internet securely?

- A. Private subnet instances should not have internet access
- B. Use a single NAT gateway in any public subnet for all subnets
- C. Deploy a NAT gateway in each public subnet, aligned with the Availability Zone of the private subnet
- D. Deploy a NAT gateway in each private subnet

<details>
<summary>Show answer</summary>

**Answer: C**

To enable instances in private subnets to connect to the internet, a NAT gateway should be deployed in a public subnet within the same Availability Zone as the private subnet. This configuration allows outbound internet traffic from the private subnet while preventing unsolicited inbound traffic, ensuring security and availability.

- **A is wrong:** Private instances often need outbound internet access (e.g., for updates); a NAT gateway provides this securely without exposing them to inbound traffic.
- **B is wrong:** A single NAT gateway is a single point of failure and routes cross-AZ traffic (incurring data transfer charges and reducing fault tolerance).
- **D is wrong:** A NAT gateway must live in a *public* subnet so its own traffic can reach the internet via the internet gateway; placing it in a private subnet would not work.
</details>

### Q163: Handling unpredictable flash-sale order spikes by scaling on SQS queue depth

A retail company experiences unpredictable spikes in online orders during flash sales. Which strategy will enhance the application's ability to handle these surges effectively?

- A. Set up scheduled Auto Scaling for the processing tier based on historical peak traffic times, using average CPU utilization as a scaling metric.
- B. Implement an EC2 Auto Scaling group with a target tracking policy that monitors the ApproximateNumberOfMessages in the SQS queue to dynamically adjust the processing tier.
- C. Integrate Amazon Kinesis Data Streams to buffer incoming orders and configure the processing tier to use enhanced fan-out for high throughput.

<details>
<summary>Show answer</summary>

**Answer: B**

Implementing an EC2 Auto Scaling group with a target tracking policy that observes the ApproximateNumberOfMessages in the SQS queue is the optimal solution. This allows the system to automatically adjust the number of EC2 instances in response to the queue's depth, ensuring that the application can efficiently manage increased demand during peak times. This approach is particularly beneficial for applications with variable traffic patterns, as it dynamically scales resources to maintain optimal performance.

- **A is wrong:** Scheduled scaling relies on known, historical peak times, so it cannot react to truly unpredictable spikes, and CPU utilization may not reflect the backlog of pending orders.
- **C is wrong:** Kinesis Data Streams with enhanced fan-out adds complexity for streaming/real-time analytics; it does not, by itself, scale the processing tier to clear an order backlog the way queue-depth-based Auto Scaling does.
</details>

### Q164: Scalable customer feedback collection and sentiment analysis with one-year data retention

A company needs to implement a system for collecting customer feedback and performing sentiment analysis, ensuring the data is retained for one year. What is the most efficient and scalable solution?

- A. Implement a RESTful API with Amazon API Gateway to send feedback data to an Amazon SQS queue. Use an AWS Lambda function to process the queue messages, perform sentiment analysis with Amazon Comprehend, and store the results in a DynamoDB table with a 365-day TTL on each item.
- B. Utilize Amazon Kinesis Data Streams to handle feedback submissions. Deploy an AWS Lambda consumer to process records in batches, apply Amazon Translate for language detection and translation, and store the output in an Amazon OpenSearch Service index. Set OpenSearch Index State Management (ISM) policies to delete documents after one year.
- C. Leverage Amazon EventBridge to capture feedback events and route them to an AWS Step Functions workflow. The workflow triggers Lambda functions for data validation, uses Amazon Transcribe to convert text to audio for archival, and saves the results in an Amazon RDS database. Apply a lifecycle policy to delete records after one year.

<details>
<summary>Show answer</summary>

**Answer: A**

The optimal solution involves using Amazon API Gateway to create a RESTful API that directs feedback data to an Amazon SQS queue. This architecture supports scalable and decoupled data ingestion. An AWS Lambda function processes the messages from the queue, utilizing Amazon Comprehend for sentiment analysis, and stores the results in a DynamoDB table with a Time to Live (TTL) of 365 days. This serverless setup automatically scales with demand and efficiently manages data retention for a year.

- **B is wrong:** Amazon Translate handles language translation, not sentiment analysis, so it does not meet the core requirement; the OpenSearch pipeline also adds unnecessary cost and management.
- **C is wrong:** Amazon Transcribe converts speech to text (not text to audio), so it is misapplied here, and the Step Functions + RDS design adds complexity without performing sentiment analysis.
</details>

### Q165: Quickly reverting a DynamoDB table to a previous state after erroneous transactions

A financial services firm operates a transaction processing system using an Amazon DynamoDB table. The operations team has noticed that sometimes, erroneous transactions are recorded. When such errors are identified, the team needs to quickly revert the table to its previous state. What solution should be implemented?

- A. Set up the Amazon DynamoDB table as a global table and switch the application to use a replica in another AWS region without errors
- B. Utilize Amazon DynamoDB point-in-time recovery to revert the table to its state before the erroneous transactions occurred
- C. Employ Amazon DynamoDB on-demand backup to restore the table to its state before the erroneous transactions occurred

<details>
<summary>Show answer</summary>

**Answer: B**

Amazon DynamoDB's point-in-time recovery (PITR) feature enables you to restore your table to any second within the last 35 days. This capability is particularly useful for recovering from accidental data corruption or deletion, as it provides a continuous backup mechanism. In this case, PITR is the most effective solution to revert the table to its state before the erroneous transactions were recorded.

- **A is wrong:** A global table replicates data (including the erroneous transactions) to other Regions; it provides availability, not the ability to roll back to a prior point in time.
- **C is wrong:** On-demand backups are point-in-time snapshots taken manually, so they may not capture the exact moment just before the errors; PITR offers continuous, second-level granularity.
</details>

### Q166: Fully serverless processing of fluctuating IoT data for a real-time analytics platform

A tech startup is developing a real-time analytics platform for IoT devices that requires a fully serverless architecture to handle fluctuating data loads. Which combination of AWS services should they use to efficiently process incoming data?

- A. Use Amazon Simple Queue Service (Amazon SQS) to queue incoming data, which is processed by AWS Lambda functions and stored in an Amazon DynamoDB table for further analysis
- B. Deploy an Amazon EC2 instance to process data from Amazon Simple Queue Service (Amazon SQS) and store it in an Amazon DynamoDB table
- C. Utilize Amazon Kinesis Data Streams to ingest data, which is processed by AWS Lambda and stored in an Amazon RDS database

<details>
<summary>Show answer</summary>

**Answer: A**

The optimal solution involves using Amazon SQS with AWS Lambda. AWS Lambda executes code in a serverless environment, automatically scaling with the data load, while Amazon SQS provides a fully managed message queuing service that decouples and scales applications. This combination ensures that the architecture remains serverless and can handle varying data volumes without manual intervention.

- **B is wrong:** An Amazon EC2 instance is not serverless — it requires provisioning, patching, and manual scaling, which conflicts with the fully serverless requirement.
- **C is wrong:** Amazon RDS is a managed but server-based relational database (not serverless in this design), so the architecture would no longer be fully serverless.
</details>

### Q167: Smooth blue-green traffic transition for a mobile app where devices cache DNS

A company is planning a blue-green deployment strategy for their mobile application, which is used by a large number of customers. Given that mobile devices often cache DNS entries, what solution would you suggest to ensure a smooth transition of traffic between the old and new environments within a 48-hour window?

- A. Configure Amazon Route 53 with weighted routing to balance traffic
- B. Utilize Elastic Load Balancing (ELB) to direct traffic between environments
- C. Implement AWS Global Accelerator to manage traffic flow between environments

<details>
<summary>Show answer</summary>

**Answer: C**

AWS Global Accelerator is the best choice for managing traffic in a blue-green deployment scenario where DNS caching is a concern. It allows you to control traffic flow using endpoint weights and traffic dials, ensuring that changes are applied quickly and reliably, unaffected by DNS caching issues.

- **A is wrong:** Route 53 weighted routing depends on DNS, so cached DNS entries on mobile devices would delay the traffic shift and prevent a smooth transition within the window.
- **B is wrong:** ELB distributes traffic to targets within a single environment/Region; it is not designed to shift traffic between separate blue and green environments.
</details>

### Q168: Securely connecting a Lambda function to RDS PostgreSQL without static credentials

What is a secure way to allow an AWS Lambda function to connect to an Amazon RDS PostgreSQL instance without embedding static credentials?

- A. Implement IAM database authentication for the Lambda function
- B. Store the database username and password in AWS Secrets Manager
- C. Include the database credentials directly in the Lambda function code

<details>
<summary>Show answer</summary>

**Answer: A**

Utilizing IAM database authentication enhances security by eliminating the need for static credentials. By assigning an IAM role to the Lambda function, it can generate temporary authentication tokens to access the RDS PostgreSQL database, ensuring credentials are not hardcoded or stored in the environment.

- **B is wrong:** Secrets Manager securely stores credentials and is a valid pattern, but the username/password are still static, long-lived credentials — IAM database authentication uses short-lived tokens and best fits "without static credentials."
- **C is wrong:** Hardcoding credentials in the function code is the least secure option, exposing them to anyone who can read the code.
</details>

### Q169: Secure private access from a VPC to a third-party vendor's RDS without internet, Direct Connect, or VPN

A financial services firm needs to establish a secure connection from its private VPC to a third-party vendor's Amazon RDS for PostgreSQL instance located in the vendor's AWS account. The firm's VPC does not have internet access, Direct Connect, or VPN connections. Which solution ensures secure and private access to the RDS database while keeping the setup straightforward and adhering to security protocols?

- A. Establish VPC peering between the firm's VPC and the vendor's VPC. Utilize AWS Transit Gateway in the vendor's account to direct traffic from the firm's VPC to the database. Update the RDS subnet route tables to permit access from the firm's CIDR block
- B. Request the vendor to enable public access on the Amazon RDS instance and configure a security group rule to permit inbound access from the firm's IP range. The firm accesses the database over the public internet using a NAT Gateway set up in a private subnet
- C. Ask the vendor to deploy a Network Load Balancer (NLB) in front of the Amazon RDS for PostgreSQL instance and use AWS PrivateLink to expose the NLB as an interface VPC endpoint in the firm's VPC

<details>
<summary>Show answer</summary>

**Answer: C**

The most effective solution is for the vendor to set up a Network Load Balancer (NLB) in front of the Amazon RDS for PostgreSQL instance and use AWS PrivateLink to present the NLB as an interface VPC endpoint in the firm's VPC. This method provides secure and private connectivity without needing internet access, VPN, or Direct Connect, and it maintains data traffic within the AWS infrastructure, aligning with security standards.

- **A is wrong:** VPC peering and Transit Gateway expose the entire networks to each other and require careful CIDR/route management; this is more complex and less isolated than exposing only the database via PrivateLink.
- **B is wrong:** Enabling public access on RDS and traversing the public internet violates the secure, private-access requirement and increases the attack surface.
</details>

### Q170: Cost-effectively relieving repeated-query read pressure on Amazon Aurora

A company is experiencing high read traffic on its Amazon Aurora database due to repeated queries. What is the most cost-effective strategy to alleviate this pressure?

- A. Switch to Aurora Serverless v2 to automatically adjust capacity based on demand, directing all traffic through the main endpoint
- B. Deploy an additional Aurora read replica to handle increased read traffic and balance the load
- C. Implement Amazon ElastiCache for Redis to cache frequently accessed data, reducing the number of repeated reads to the Aurora database

<details>
<summary>Show answer</summary>

**Answer: C**

Using Amazon ElastiCache for Redis as a caching layer between the application and the Aurora database is the most cost-effective solution. By storing frequently accessed data in Redis, the application can reduce the number of repeated read requests to the Aurora database. This reduces the load on the database, enhances performance, and scales efficiently without the need for additional read replicas.

- **A is wrong:** Aurora Serverless v2 scales compute capacity (and cost) with demand but still executes every repeated query against the database; it does not eliminate the redundant reads.
- **B is wrong:** Adding a read replica increases ongoing cost and still serves each repeated query from the database, rather than caching results to avoid the reads entirely.
</details>

### Q171: Minimizing AWS KMS encryption costs for frequently accessed S3 video files

A media company needs to securely store frequently accessed video files in Amazon S3. They want to minimize encryption costs while using AWS KMS for server-side encryption. What strategy should they implement?

- A. Opt for server-side encryption with Amazon S3 managed keys (SSE-S3) to avoid AWS KMS charges, while keeping the same encryption standards
- B. Activate S3 Bucket Keys for server-side encryption with AWS KMS (SSE-KMS) to use a bucket-level key, reducing the need for individual KMS key requests for each object
- C. Implement client-side encryption by creating a local symmetric key and attaching it to each object's metadata for decryption in Amazon S3

<details>
<summary>Show answer</summary>

**Answer: B**

By enabling S3 Bucket Keys with SSE-KMS, Amazon S3 can generate data keys locally using a bucket-level key, reducing the number of AWS KMS requests. This maintains the security benefits of AWS KMS while lowering the cost of frequent KMS API calls, which is ideal for high-access scenarios.

- **A is wrong:** SSE-S3 avoids KMS charges entirely but the requirement is to keep using AWS KMS; it does not meet the "using AWS KMS" condition and gives up KMS key control/auditing.
- **C is wrong:** Client-side encryption with a self-managed local key adds significant operational complexity and key-management risk, and storing keys in object metadata is insecure.
</details>

### Q172: IAM policy type that defines which entities can assume a role

In AWS Identity and Access Management (IAM), which type of policy is specifically used to define which entities can assume a role?

- A. Trust policy
- B. Permissions boundary
- C. Service control policy
- D. Access control list (ACL)

<details>
<summary>Show answer</summary>

**Answer: A**

In AWS IAM, trust policies are used to specify which entities (users, services, or accounts) are allowed to assume a particular role. These are the only resource-based policies supported by IAM.

- **B is wrong:** A permissions boundary sets the maximum permissions an identity can have; it does not define who can assume a role.
- **C is wrong:** A service control policy (SCP) sets permission guardrails across accounts in AWS Organizations; it does not control role assumption.
- **D is wrong:** An ACL is a legacy access-control mechanism (e.g., for S3 objects/buckets) and is not used to define who can assume an IAM role.
</details>

### Q173: Securely linking a corporate network to AWS with encryption and detailed access control

What is the most effective method to securely link a company's internal network to its AWS infrastructure, ensuring encryption at both the transport and application layers, while also allowing detailed security management?

- A. Implement AWS Client VPN for individual user connections from the corporate network to the VPC, controlling access with security groups and IAM policies
- B. Establish a bastion host within a public subnet of the VPC to facilitate SSH access from the corporate network, using security groups for access management
- C. Deploy an AWS Site-to-Site VPN to connect the corporate network with the AWS VPC, utilizing security groups and network ACLs for precise access control

<details>
<summary>Show answer</summary>

**Answer: C**

Implementing an AWS Site-to-Site VPN is the optimal solution as it provides transport layer encryption through IPsec tunnels, securing data exchange between the corporate network and AWS. This setup allows for detailed security management using security groups and network ACLs. Additionally, application layer encryption can be applied to meet comprehensive encryption needs, making it a secure and cost-efficient choice for hybrid connectivity.

- **A is wrong:** AWS Client VPN is designed for individual remote user connections, not for linking an entire internal corporate network to AWS.
- **B is wrong:** A bastion host only provides SSH access to instances and exposes a public entry point; it does not establish an encrypted network-to-network link.
</details>

### Q174: High availability with content-based routing for an EC2-based media streaming app

A media streaming service is planning to transition its main application to a set of Amazon EC2 instances. The company requires a solution that supports high availability and can perform content-based routing. As a Solutions Architect, what would you recommend?

- A. Utilize an Auto Scaling group to distribute traffic to Amazon EC2 instances across multiple Availability Zones and configure a Static IP for instance failure management.
- B. Implement an Application Load Balancer to route traffic to Amazon EC2 instances across multiple Availability Zones and use an Auto Scaling group to handle instance failures.
- C. Deploy a Network Load Balancer to route traffic to Amazon EC2 instances across multiple Availability Zones and use Elastic IPs to manage instance failures.

<details>
<summary>Show answer</summary>

**Answer: B**

The Application Load Balancer (ALB) is designed for handling HTTP and HTTPS traffic and provides advanced request routing based on the content of the request, which is essential for content-based routing. It functions at Layer 7 and can direct traffic to targets within an Amazon VPC, making it the appropriate choice for this scenario. To ensure high availability, deploying instances across multiple Availability Zones and using an Auto Scaling group to manage instance failures is recommended.

- **A is wrong:** An Auto Scaling group distributes and replaces instances but does not perform load balancing or content-based routing; a Static IP does not provide HA traffic management.
- **C is wrong:** A Network Load Balancer operates at Layer 4 and cannot perform content-based (Layer 7) routing; Elastic IPs are not the right mechanism for managing instance failures behind a load balancer.
</details>

### Q175: Tracking resource configuration changes for compliance and change history

A company wants to ensure that their AWS infrastructure remains compliant with internal security policies and needs to keep a record of all configuration changes. As a solutions architect, which service would you suggest to achieve this?

- A. Implement AWS Config to monitor and record resource configurations for compliance and change history
- B. Utilize Amazon CloudWatch to monitor and record resource configurations for compliance and change history
- C. Deploy AWS Systems Manager to monitor and record resource configurations for compliance and change history

<details>
<summary>Show answer</summary>

**Answer: A**

AWS Config is specifically designed to help you track and evaluate the configurations of your AWS resources. It provides a comprehensive history of configuration changes and assists in compliance auditing, making it the best choice for maintaining compliance and a record of changes.

- **B is wrong:** Amazon CloudWatch monitors metrics, logs, and operational performance; it does not track resource configuration state or provide configuration change history.
- **C is wrong:** AWS Systems Manager handles operational management tasks (patching, automation, parameter storage), not continuous recording and evaluation of resource configuration changes for compliance.
</details>

### Q176: Restricting CloudFront content to specific authorized users

How can you ensure that only specific users can access content served by Amazon CloudFront?

- A. Use VPC Security Groups
- B. Implement signed URLs with CloudFront
- C. Activate AWS WAF for CloudFront

<details>
<summary>Show answer</summary>

**Answer: B**

To control access to content delivered through Amazon CloudFront, you can implement signed URLs or signed cookies. Signed URLs are useful for granting access to individual files, while signed cookies are ideal for granting access to multiple files without altering URLs. Both methods effectively limit access to authorized users only.

- **A is wrong:** VPC security groups control network traffic to resources inside a VPC; CloudFront is a global edge service, so security groups cannot restrict who accesses its content.
- **C is wrong:** AWS WAF filters requests based on rules (IP, patterns, rate limits) but does not authenticate or restrict access to specific individual users.
</details>

### Q177: Service for a custom application to handle and analyze real-time data streams

A company needs to develop a custom application that can handle and analyze real-time data streams for its specialized operations. Which AWS service should be recommended to ensure efficient processing and scalability?

- A. Implement Amazon Kinesis Data Streams to efficiently handle and analyze the real-time data streams while ensuring scalability.
- B. Implement Amazon Simple Notification Service (Amazon SNS) to efficiently handle and analyze the real-time data streams while ensuring scalability.
- C. Implement Amazon Kinesis Data Firehose to efficiently handle and analyze the real-time data streams while ensuring scalability.

<details>
<summary>Show answer</summary>

**Answer: A**

Amazon Kinesis Data Streams is specifically designed for real-time data streaming and processing. It allows applications to capture, process, and store data streams from multiple sources, facilitating real-time analytics. This service supports the decoupling of data producers and consumers, which is crucial for building scalable and resilient systems.

- **B is wrong:** Amazon SNS is a pub/sub notification service for delivering messages; it does not provide stream storage, replay, or custom real-time stream processing.
- **C is wrong:** Kinesis Data Firehose is for loading/delivering streaming data into destinations (S3, Redshift, etc.); it does not allow custom, low-latency processing of records the way Data Streams does.
</details>

### Q178: Placement strategy for high-speed communication between compute nodes

You are tasked with designing a solution for a financial analytics firm that requires rapid data processing and high-speed communication between compute nodes on AWS. Which deployment strategy should you choose to achieve optimal network performance?

- A. Deploy instances in a Cluster placement group
- B. Deploy instances in a Spread placement group
- C. Utilize Amazon EC2 Auto Scaling for dynamic resource allocation

<details>
<summary>Show answer</summary>

**Answer: A**

Choosing a Cluster placement group is the best option for applications needing high-speed communication between instances. This configuration ensures that instances are placed close together within a single Availability Zone, minimizing network latency and maximizing throughput, which is crucial for high-performance computing tasks.

- **B is wrong:** A Spread placement group deliberately places instances on distinct hardware across AZs to reduce correlated failures; this increases isolation but does not provide the low-latency, high-throughput networking needed here.
- **C is wrong:** EC2 Auto Scaling adjusts the number of instances based on demand; it does not control physical placement and therefore does not optimize inter-node network performance.
</details>

### Q179: High-performance storage with cost-effective tiering for semiconductor EDA workflows

A tech company is developing a new semiconductor and needs a solution to enhance the performance of their electronic design automation (EDA) workflows. Which AWS service should they use to efficiently manage high-speed data processing and storage for frequently accessed data, while also providing economical storage for less frequently accessed data?

- A. Amazon FSx for Lustre
- B. Amazon Redshift
- C. Amazon FSx for Windows File Server

<details>
<summary>Show answer</summary>

**Answer: A**

Amazon FSx for Lustre is specifically designed for high-performance computing applications that demand fast data processing and storage. It can be integrated with Amazon S3, which allows for seamless management of frequently accessed 'hot data' and cost-effective storage for 'cold data' with quick retrieval times. This makes it the most suitable option for the company's EDA requirements.

- **B is wrong:** Amazon Redshift is a data warehouse for analytical SQL queries, not a high-performance file system for EDA compute workloads.
- **C is wrong:** FSx for Windows File Server provides SMB/NTFS file shares for Windows workloads; it is not optimized for the high-throughput, low-latency HPC processing that EDA requires, nor for S3 hot/cold tiering.
</details>

### Q180: Cost-effective POSIX-compliant storage for rarely accessed archival data

An educational institution plans to migrate its archival data from local servers to a cloud-based POSIX-compliant file storage solution on AWS. The data will be retrieved only once annually for a week-long review. As an AWS Solutions Architect, which AWS service would you recommend to minimize costs?

- A. Amazon S3 Glacier
- B. Amazon S3 Standard
- C. Amazon EFS Standard
- D. Amazon EFS Infrequent Access (EFS IA)

<details>
<summary>Show answer</summary>

**Answer: D**

Amazon EFS Infrequent Access (EFS IA) is designed for files that are rarely accessed, offering a cost-effective solution compared to Amazon EFS Standard. It maintains POSIX compliance, which is necessary for this scenario, and is ideal for data that is accessed infrequently, such as once a year.

- **A is wrong:** S3 Glacier is object storage, not a POSIX-compliant file system, and retrieval can take time depending on the tier.
- **B is wrong:** S3 Standard is also object storage (not POSIX-compliant) and is more expensive for rarely accessed data.
- **C is wrong:** EFS Standard is POSIX-compliant but costs significantly more than EFS IA for data accessed only once a year.
</details>

### Q181: Why an Auto Scaling group is not terminating instances marked as unhealthy

A company is using an Auto Scaling group to manage its fleet of EC2 instances. Despite some instances being marked as unhealthy, they are not being terminated. What could be a reason for this behavior?

- A. The Auto Scaling group is using an outdated launch template
- B. The health check grace period is still active
- C. The instances are failing EC2 health checks

<details>
<summary>Show answer</summary>

**Answer: B**

Auto Scaling groups have a health check grace period during which instances are not terminated even if they are marked unhealthy. This allows instances time to fully initialize and pass health checks. If the grace period has not expired, the Auto Scaling group will not terminate the instance.

- **A is wrong:** An outdated launch template affects how new instances are launched, not whether unhealthy instances are terminated.
- **C is wrong:** Failing EC2 health checks would normally trigger replacement; it is not a reason an unhealthy instance would be left running (and during the grace period failures are ignored).
</details>

### Q182: Ensuring high availability for a secure access point (bastion) to a private network

You are tasked with designing a robust architecture for a secure access point to your private network on AWS. Which solution would you implement to ensure high availability for this access point?

- A. Implement an Application Load Balancer (ALB)
- B. Store access logs in an Amazon S3 bucket
- C. Deploy a Network Load Balancer (NLB)
- D. Use Amazon CloudFront for caching

<details>
<summary>Show answer</summary>

**Answer: C**

To achieve high availability for a secure access point like a bastion host, deploying a Network Load Balancer (NLB) is optimal. NLBs are designed to handle large volumes of connections and operate at the transport layer (Layer 4), making them ideal for managing TCP traffic such as SSH. This setup ensures that incoming connections are efficiently distributed across multiple EC2 instances, enhancing both availability and performance.

- **A is wrong:** An ALB operates at Layer 7 (HTTP/HTTPS) and is not designed to load balance TCP-based protocols like SSH used to reach a bastion host.
- **B is wrong:** Storing access logs in S3 supports auditing/visibility but does nothing to provide high availability for the access point.
- **D is wrong:** CloudFront is a content delivery/caching service for web content, not a solution for highly available SSH/TCP access to a private network.
</details>

### Q183: Efficiently identifying S3 buckets without versioning with minimal administrative effort

A company wants to ensure all its Amazon S3 buckets have versioning enabled to protect against accidental data loss. What is the most efficient method to identify buckets without versioning, while keeping administrative tasks to a minimum?

- A. Review each bucket's settings manually in the AWS Management Console.
- B. Enable advanced metrics in Amazon S3 Storage Lens to track versioning.
- C. Deploy AWS Config rules to evaluate each bucket's versioning setting.
- D. Create a script using AWS CLI to check versioning for each bucket.

<details>
<summary>Show answer</summary>

**Answer: B**

Amazon S3 Storage Lens offers a detailed analysis of storage usage and activity, including versioning status. By enabling advanced metrics, you can easily filter and identify buckets without versioning across multiple accounts and regions. This approach is efficient and reduces operational overhead by providing a centralized dashboard for monitoring and compliance.

- **A is wrong:** Manually reviewing each bucket does not scale and is the most labor-intensive option.
- **C is wrong:** AWS Config rules can evaluate compliance but require setting up and managing rules per account/Region, adding more administrative overhead than Storage Lens's built-in metrics.
- **D is wrong:** A custom AWS CLI script must be written, maintained, and run, adding operational burden rather than minimizing it.
</details>

### Q184: Handling a sudden traffic surge on an e-commerce platform during a promotional event

You are tasked with preparing an e-commerce platform for a major promotional event that is expected to cause a surge in user activity. Which strategy would you implement to ensure the platform can handle a sudden increase in traffic?

- A. Deploy an Amazon CloudFront distribution to cache content
- B. Implement an Auto Scaling Group to manage EC2 instances
- C. Configure Amazon Route 53 with latency-based routing

<details>
<summary>Show answer</summary>

**Answer: B**

Implementing an Auto Scaling Group is the most effective approach for managing a sudden increase in web traffic. It dynamically adjusts the number of Amazon EC2 instances based on the current demand, ensuring that the infrastructure can scale up to accommodate the increased load and scale down when the demand subsides, optimizing both performance and cost.

- **A is wrong:** CloudFront caches and accelerates static/cacheable content but does not scale the compute tier handling dynamic e-commerce transactions.
- **C is wrong:** Route 53 latency-based routing directs users to the lowest-latency endpoint; it does not add capacity to handle a traffic surge.
</details>

### Q185: Integrating directory-dependent applications with an existing on-premises Microsoft AD

An organization is migrating its directory-dependent applications to AWS and requires a solution that can integrate with their existing on-premises Microsoft Active Directory. Which AWS service should they use to establish this integration?

- A. AWS Managed Microsoft AD
- B. Simple AD
- C. AWS Transit Gateway

<details>
<summary>Show answer</summary>

**Answer: A**

AWS Managed Microsoft AD is the appropriate service for integrating AWS-hosted applications with on-premises Microsoft Active Directory. It allows for the creation of trust relationships, enabling seamless integration and authentication across environments. This service provides a fully managed Active Directory environment in AWS, supporting applications that need comprehensive AD features.

- **B is wrong:** Simple AD is a lightweight, standalone directory that does not support trust relationships with an on-premises Microsoft Active Directory.
- **C is wrong:** AWS Transit Gateway is a network hub for connecting VPCs and on-premises networks; it provides connectivity, not directory/Active Directory integration.
</details>

### Q186: Why an Auto Scaling group does not replace an instance the Load Balancer marks unhealthy

In a cloud environment, a web application is experiencing downtime because its Auto Scaling group is not replacing an instance marked as unhealthy by the Load Balancer. What could be the reason for this issue?

- A. The Auto Scaling group and Load Balancer both use the same health check mechanism
- B. The Auto Scaling group is configured with EC2 instance status checks, while the Load Balancer uses its own health checks
- C. The Load Balancer is configured with EC2 instance status checks, while the Auto Scaling group uses its own health checks

<details>
<summary>Show answer</summary>

**Answer: B**

The problem occurs when the Auto Scaling group relies on EC2 instance status checks, while the Load Balancer uses its own health check criteria. If the Load Balancer identifies an instance as unhealthy and the Auto Scaling group does not, due to differing health check mechanisms, the Auto Scaling group will not automatically replace the instance. The fix is to configure the Auto Scaling group to use ELB (Load Balancer) health checks.

- **A is wrong:** If both used the same health check mechanism, the Auto Scaling group would detect the same unhealthy state and replace the instance — so this would not cause the issue.
- **C is wrong:** Load Balancers do not perform EC2 instance status checks; this configuration is not how the services work, so it does not describe the actual cause.
</details>

### Q187: Efficiently enhancing availability for a web application accessed by US users

A company operates a web application primarily accessed by users in the United States. What is the MOST efficient approach to enhance the application's availability?

- A. Deploy web-tier Amazon EC2 instances in two different regions with an Elastic Load Balancer and use Amazon RDS MySQL with read replicas.
- B. Deploy web-tier Amazon EC2 instances in two Availability Zones (AZs) with an Elastic Load Balancer and use Amazon RDS MySQL in Multi-AZ configuration.
- C. Deploy web-tier Amazon EC2 instances in two Availability Zones (AZs) with an Elastic Load Balancer and use Amazon RDS MySQL with read replicas.

<details>
<summary>Show answer</summary>

**Answer: B**

To boost application availability efficiently, deploying web-tier Amazon EC2 instances across two Availability Zones (AZs) with an Elastic Load Balancer is the optimal solution. This setup balances incoming traffic and provides redundancy. Additionally, using Amazon RDS MySQL in a Multi-AZ configuration ensures high availability by automatically maintaining a standby instance in a separate AZ, ready to take over in case of failure.

- **A is wrong:** Deploying across two Regions is more complex and costly than necessary for a US-only user base, and read replicas provide read scaling, not automatic failover for availability.
- **C is wrong:** Read replicas improve read performance but do not provide automatic database failover; only a Multi-AZ configuration delivers the high availability required.
</details>

### Q188: Interpreting an IAM policy that restricts EC2 actions by Region and source IP

A company has an IAM policy that restricts all EC2 operations unless they occur in the 'us-east-1' region. However, it permits the termination of EC2 instances if the request originates from an IP address within the '192.168.1.0/24' range. Which statement accurately describes the permissions granted by this policy?

- A. Users can terminate an EC2 instance in any region except us-east-1 if their source IP is '192.168.1.50'.
- B. Users can terminate an EC2 instance in the 'us-east-1' region if their source IP is '192.168.1.50'.
- C. Users can terminate an EC2 instance in the 'us-east-1' region if the instance's IP address is '192.168.1.50'.

<details>
<summary>Show answer</summary>

**Answer: B**

The IAM policy is designed to deny all EC2 actions unless they are performed in the 'us-east-1' region. However, it makes an exception for terminating instances if the request comes from an IP within the '192.168.1.0/24' range. Therefore, a user with the IP '192.168.1.50' can terminate instances in the 'us-east-1' region.

- **A is wrong:** The policy restricts operations to us-east-1, so actions in other regions are denied — not allowed.
- **C is wrong:** The `aws:SourceIp` condition evaluates the requester's source IP, not the instance's IP address.
</details>

### Q189: DDoS protection with compliance-grade logging and minimal changes for a financial web app

A financial services company needs to protect its customer-facing web application from DDoS attacks while ensuring detailed logging for compliance audits. The solution should require minimal changes to the current AWS setup. What is the most suitable approach?

- A. Set up an Amazon CloudFront distribution with AWS WAF to block malicious traffic and analyze CloudFront logs for suspicious activity.
- B. Implement AWS Shield Advanced for enhanced DDoS protection and leverage the AWS DDoS Response Team for expert guidance. Use the service's logging features for audit compliance.
- C. Utilize Amazon GuardDuty to monitor for threats and manually adjust security group rules on EC2 instances based on findings.

<details>
<summary>Show answer</summary>

**Answer: B**

AWS Shield Advanced offers robust DDoS protection for AWS resources, including Application Load Balancers (ALBs). It provides real-time threat detection, automated mitigation, and access to the AWS DDoS Response Team (DRT) for expert support. Additionally, it includes detailed logging and reporting capabilities, which are essential for compliance audits, all while requiring minimal architectural changes.

- **A is wrong:** CloudFront with AWS WAF mitigates web exploits and some volumetric traffic but is not a dedicated DDoS protection service, and it would require adding a CloudFront/WAF layer rather than minimal changes.
- **C is wrong:** GuardDuty is a threat-detection service, not a DDoS mitigation service, and manually adjusting security groups is reactive and operationally heavy.
</details>

### Q190: Sharing an EFS document with teams in different AWS regions while keeping management low

You are tasked with ensuring that a shared document on Amazon EFS is accessible to teams located in different AWS regions, while keeping management efforts low. What is the best approach to achieve this?

- A. Deploy a distinct EFS file system in each region
- B. Replicate the EFS data to each region separately
- C. Set up an inter-region VPC peering connection to allow access from other regions
- D. Transfer the data to Amazon S3 for global access

<details>
<summary>Show answer</summary>

**Answer: D**

Amazon EFS is a regional service and cannot be directly accessed across regions, even with inter-region VPC peering. To share data with users in multiple regions while keeping management simple, it is best to use Amazon S3, which provides global accessibility and built-in cross-region replication if required.

- **A is wrong:** Deploying a separate EFS file system per region creates isolated copies that must be kept in sync manually, increasing management effort.
- **B is wrong:** Manually replicating EFS data to each region adds operational overhead and risk of inconsistency.
- **C is wrong:** Inter-region VPC peering provides network connectivity but does not make a regional EFS file system directly mountable from another region.
</details>

### Q191: Determining EC2 instance tenancy from configuration template and VPC settings

A company is deploying two sets of EC2 instances using Configuration Template A and Configuration Template B. What is true about the tenancy of these instances?

- A. Instances launched using Configuration Template A will have dedicated tenancy, while those using Configuration Template B will have default tenancy.
- B. Instances launched using Configuration Template A and Configuration Template B will have default tenancy.
- C. Instances launched using Configuration Template A and Configuration Template B will have dedicated tenancy.

<details>
<summary>Show answer</summary>

**Answer: C**

The tenancy of EC2 instances can be determined by the settings in the configuration template or the VPC. If either specifies dedicated tenancy, the instances will be launched with dedicated tenancy. In this case, both Configuration Template A and Configuration Template B result in instances with dedicated tenancy due to either the template or the VPC settings.

- **A is wrong:** Both sets end up with dedicated tenancy; the tenancy is not split between the two templates because the VPC (or template) setting forces dedicated.
- **B is wrong:** Because dedicated tenancy is specified by the template or the VPC, the instances cannot fall back to default tenancy.
</details>

### Q192: Scaling for predictable month-end traffic surges on an e-commerce platform

A company experiences predictable traffic surges on their e-commerce platform at the end of each month. As a solutions architect, what strategy would you recommend to ensure their Amazon EC2 instances scale appropriately during these peak periods?

- A. Create a scheduled action in your Auto Scaling group to set both the minimum and maximum instance counts to 10 at the specified time, ensuring readiness for peak demand.
- B. Implement a target tracking scaling policy to automatically adjust the instance count to 10 at the specified time, ensuring readiness for peak demand.
- C. Set up a scheduled action in your Auto Scaling group to adjust the desired capacity to 10 instances at the specified time on the last day of the month, ensuring readiness for peak demand.

<details>
<summary>Show answer</summary>

**Answer: C**

For predictable traffic increases, such as those occurring at a specific time each month, implementing scheduled scaling actions in an Auto Scaling group is effective. By configuring a scheduled action to set the desired capacity to a specific number of instances at the required time, you ensure that the system is prepared for the increased demand. This method is more precise than setting minimum and maximum instance counts, which define a range rather than a specific target.

- **A is wrong:** Setting only the minimum and maximum counts defines a range, not a guaranteed number of running instances; adjusting the desired capacity is the precise way to launch exactly 10 instances.
- **B is wrong:** Target tracking reacts to a real-time metric (e.g., CPU); it is suited to unpredictable load, not to proactively provisioning capacity ahead of a known scheduled surge.
</details>

### Q193: Automating SFTP/NFS data migration from on-premises to Amazon EFS (Choose Two)

A media company stores all the media files in an on-premises data center in North America. The organization wants to migrate its on-premises data center to AWS. The data center hosts an SFTP server with 500 GB of data stored on an NFS-based file system. The requirement is to migrate this data to an Amazon EC2 instance leveraging an Amazon Elastic File System (Amazon EFS) file system. What combination of actions should a solutions architect implement to automate this data transfer? (Choose Two.)

- A. Launch the EC2 instance into the same Availability Zone as the EFS file system.
- B. Install an AWS DataSync agent in the on-premises data center.
- C. Create a secondary Amazon Elastic Block Store (Amazon EBS) volume on the EC2 instance for the data.
- D. Manually use an operating system copy command to push the data to the EC2 instance.
- E. Use AWS DataSync to create a location configuration for the on-premises SFTP server.

<details>
<summary>Show answer</summary>

**Answer: B and E**

To automate the transfer of NFS data from an on-premises SFTP server to Amazon EFS, install an AWS DataSync agent in the on-premises data center to read data from the local storage system and securely transfer it to AWS, then use AWS DataSync to create a location configuration for the on-premises SFTP source. DataSync supports SFTP as a source location and enables automated, scheduled, and managed transfers directly into Amazon EFS.

- **A is wrong:** EFS is a regional service with mount targets in multiple AZs; DataSync transfers directly to EFS, so co-locating the EC2 instance in a single AZ is unnecessary.
- **C is wrong:** The requirement is to migrate data to Amazon EFS, not EBS.
- **D is wrong:** A manual OS copy command is not an automated solution and does not meet the requirement for an automated transfer.
</details>

### Q194: Cost-effective private access to S3 from a gaming platform without traversing the internet

An online gaming company hosts their gaming platform within a private subnet of an AWS VPC. The VPC has a NAT gateway and an Internet Gateway (IGW). The application needs to store objects from the application to an Amazon S3 bucket located inside the same PROD account. The organization's security policy strictly prohibits application traffic from traversing the public internet. What is the MOST cost-effective solution to meet these requirements?

- A. Configure an S3 interface endpoint. Create a security group that allows outbound traffic to Amazon S3.
- B. Configure an S3 gateway endpoint. Update the VPC route table to use the endpoint.
- C. Configure an S3 bucket policy to allow traffic from the Elastic IP address that is assigned to the NAT gateway.
- D. Create a second NAT gateway in the same subnet where the legacy application is deployed. Update the VPC route table to use the second NAT gateway.

<details>
<summary>Show answer</summary>

**Answer: B**

An Amazon S3 gateway VPC endpoint keeps traffic between the private subnet and Amazon S3 entirely on the AWS network, satisfying the policy against traversing the public internet. Gateway endpoints incur no additional charge, making this the most cost-effective option.

- **A is wrong:** Interface endpoints are more expensive than gateway endpoints (ENI hourly cost plus data processing charges), while a gateway endpoint is free.
- **C is wrong:** Routing through the NAT gateway's Elastic IP sends traffic from the private subnet to S3 over the public internet, violating the security policy.
- **D is wrong:** NAT gateways are for internet-bound traffic and add cost; a second NAT gateway does not keep traffic on the AWS network.
</details>

### Q195: Efficient lift-and-shift migration of on-premises VMs to AWS (Choose Three)

A video game company is undergoing rapid expansion and needs to migrate its on-premises workloads to AWS due to scalability limitations of its current data center. The migration is time-sensitive, and the company aims to use a lift-and-shift strategy for its non-critical workloads. Which combination of actions will efficiently meet these requirements? (Choose THREE.)

- A. Use the AWS Schema Conversion Tool (AWS SCT) to collect data about the VMs.
- B. Use AWS Application Migration Service. Install the AWS Replication Agent on the VMs.
- C. Complete the initial replication of the VMs. Launch test instances to perform acceptance tests on the VMs.
- D. Stop all operations on the VMs. Launch a cutover instance.
- E. Use AWS App2Container (A2C) to collect data about the VMs.
- F. Use AWS Database Migration Service (AWS DMS) to migrate the VMs.

<details>
<summary>Show answer</summary>

**Answer: B, C, and D**

For a lift-and-shift migration of VMs, AWS Application Migration Service (MGN) is the right tool: install the AWS Replication Agent on the VMs, complete the initial replication and launch test instances for non-disruptive acceptance testing, then stop operations and launch a cutover instance for the final sync. Stopping workloads before cutover ensures data consistency.

- **A is wrong:** AWS SCT is used for heterogeneous database migration, not VM migration.
- **E is wrong:** App2Container is for modernizing applications into containers, not for lift-and-shift migration.
- **F is wrong:** AWS DMS migrates databases, not entire VM workloads or server images.
</details>

### Q196: Aggregating global site data into a single S3 bucket with minimal operational complexity

A weather forecasting company collects data for temperature, humidity, and atmospheric pressure in cities across multiple continents. The average volume of data that the organization collects from each source is 500 GB per day. Files uploaded once per day. Each site has a high-speed Internet connection. The company wants to aggregate the data from all these global sites as quickly as possible in a single Amazon S3 bucket that the organization owns. The solution must minimize operational complexity. Which solution meets these requirements?

- A. Upload the data from each site to an S3 bucket in the closest AWS Region. Use S3 Cross-Region Replication to copy objects to the destination S3 bucket. Then remove the data from the origin S3 bucket.
- B. Schedule AWS Snowball Edge Storage Optimized device jobs daily to transfer data from each site to the closest AWS Region. Use S3 Cross-Region Replication to copy objects to the destination S3 bucket.
- C. Upload the data from each site to an EC2 instance in the closest AWS Region. Store the data in an Amazon Elastic Block Store (Amazon EBS) volume. At regular intervals, take an EBS snapshot and copy it to the AWS Region that contains the destination S3 bucket. Restore the EBS volume in that AWS Region.
- D. Turn on S3 Transfer Acceleration on the destination S3 bucket. Use multipart uploads to directly upload site data to the destination S3 bucket.

<details>
<summary>Show answer</summary>

**Answer: D**

S3 Transfer Acceleration uses Amazon CloudFront's globally distributed edge locations to provide fast uploads from distant sites directly into the destination S3 bucket, with minimal operational overhead. Combined with multipart uploads, it delivers fast global uploads without extra infrastructure.

- **A is wrong:** Uploading to a regional bucket and then using Cross-Region Replication adds unnecessary operational complexity and increases latency.
- **B is wrong:** Snowball Edge is intended for offline or limited bandwidth scenarios; these sites have high-speed internet connections.
- **C is wrong:** The EC2/EBS snapshot-and-restore approach is operationally complex, slow, and indirect.
</details>

### Q197: Rotating RDS credentials across multiple Regions with the least operational overhead

An online gaming company maintained its gaming platform infrastructure across multiple AWS regions. The company performs monthly maintenance on its AWS infrastructure. Based on the regulatory compliance, the company must rotate the Amazon RDS for MySQL database credentials across multiple AWS regions. Which solution will meet these requirements with the LEAST operational overhead?

- A. Store the credentials as secrets in AWS Secrets Manager. Use multi-Region secret replication for the required regions. Configure Secrets Manager to rotate the secrets on a schedule.
- B. Store the credentials as secrets in AWS Systems Manager by creating a secure string parameter. Use multi-Region secret replication for the required regions. Configure Secrets Manager to rotate the secrets on a schedule.
- C. Store the credentials in an Amazon S3 bucket that has server-side encryption (SSE) enabled. Use Amazon EventBridge (Amazon CloudWatch Events) to invoke an AWS Lambda function to rotate the credentials.
- D. Encrypt the credentials as secrets by using AWS Key Management Service (AWS KMS) multi-Region customer managed keys. Store the secrets in an Amazon DynamoDB global table. Use an AWS Lambda function to retrieve the secrets from DynamoDB. Use the RDS API to rotate the secrets.

<details>
<summary>Show answer</summary>

**Answer: A**

AWS Secrets Manager natively supports multi-Region secret replication and built-in scheduled rotation for Amazon RDS credentials, minimizing custom code and operational effort.

- **B is wrong:** "Multi-Region secret replication" is a Secrets Manager feature, not a Systems Manager Parameter Store feature; secure string parameters do not provide it.
- **C is wrong:** This requires custom rotation logic and Lambda code, and you must handle consistency and permissions across regions.
- **D is wrong:** Building KMS multi-Region keys, a DynamoDB global table, and Lambda rotation is operationally heavier than the managed Secrets Manager + RDS rotation integration.
</details>

### Q198: Durable, stateless image-compression processing with SQS and Lambda (Choose Two)

A media company wants to convert large images to a smaller and compressed format. The application development team is designing a microservice to perform the image compression activity. When a user uploads an image through the web interface, the microservice should store the image in an Amazon S3 bucket, process and compress the image with an AWS Lambda function and store the image in its compressed form in a different S3 bucket. A solutions architect needs to design a solution that uses durable, stateless components to process the images automatically. Which combination of actions will meet these requirements? (Choose two.)

- A. Create an Amazon Simple Queue Service (Amazon SQS) queue. Configure the S3 bucket to send a notification to the SQS queue when an image is uploaded to the S3 bucket.
- B. Configure the Lambda function to use the Amazon Simple Queue Service (Amazon SQS) queue as the invocation source. When the SQS message is successfully processed, delete the message in the queue.
- C. Configure the Lambda function to monitor the S3 bucket for new uploads. When an uploaded image is detected, write the file name to a text file in memory and use the text file to keep track of the images that were processed.
- D. Launch an Amazon EC2 instance to monitor an Amazon Simple Queue Service (Amazon SQS) queue. When items are added to the queue, log the file name in a text file on the EC2 instance and invoke the Lambda function.
- E. Configure an Amazon EventBridge (Amazon CloudWatch Events) event to monitor the S3 bucket. When an image is uploaded, send an alert to an Amazon Simple Notification Service (Amazon SNS) topic with the application owner's email address for further processing.

<details>
<summary>Show answer</summary>

**Answer: A and B**

Configuring the S3 bucket to send a notification to an SQS queue when an image is uploaded creates a durable, decoupled trigger. Using that SQS queue as the Lambda invocation source (event source mapping) provides automatic, scalable, stateless processing, with messages deleted from the queue after successful processing.

- **C is wrong:** Lambda execution environments are ephemeral, so an in-memory text file is not durable or stateless and cannot reliably track processed images.
- **D is wrong:** Adding an Amazon EC2 instance to monitor the queue violates the goal of using stateless, serverless components.
- **E is wrong:** Sending an SNS email alert notifies a person; it does not automatically process and compress the images.
</details>

### Q199: Secure integration between a Cognito-authenticated app in a private subnet and S3 (Select Two)

A research institute hosts an application in a private subnet. The company has integrated the application with Amazon Cognito using a user pool for authentication. The company now requires the application to securely store user documents in an Amazon S3 bucket. Which combination of steps will achieve secure integration between the application and Amazon S3? (Select TWO.)

- A. Create an Amazon Cognito identity pool to generate secure Amazon S3 access tokens for users upon successful login.
- B. Use the existing Amazon Cognito user pool to generate Amazon S3 access tokens for users upon successful login.
- C. Create an Amazon S3 VPC endpoint in the same VPC where the company hosts the application.
- D. Create a NAT gateway in the VPC where the company hosts the application. Assign a policy to the S3 bucket to deny any request that is not initiated from Amazon Cognito.
- E. Attach a policy to the S3 bucket that allows access only from the users' IP addresses.

<details>
<summary>Show answer</summary>

**Answer: A and C**

An Amazon Cognito identity pool (federated identities) exchanges a successful login for temporary AWS credentials that grant scoped access to Amazon S3. An Amazon S3 VPC (gateway) endpoint lets the private-subnet application reach S3 over the AWS network without traversing the public internet.

- **B is wrong:** Cognito user pools issue JWTs for authentication, not AWS credentials; an identity pool is required to obtain S3 access.
- **D is wrong:** A NAT gateway would send the S3 traffic over public routes, which is undesirable for a private-subnet use case.
- **E is wrong:** Restricting the bucket policy to users' IP addresses is unreliable because client/user IPs are often dynamic.
</details>

### Q200: Granting pod-level access to DynamoDB and S3 from an EKS cluster

An online retail company has an application running on an Amazon EKS cluster on Amazon EC2 instances. The application's UI uses Amazon DynamoDB, and its data services utilize Amazon S3 as part of the deployment. The company requires that EKS Pods for the UI can access only Amazon DynamoDB, and EKS Pods for data services can access only Amazon S3. The company uses AWS Identity and Access Management (IAM). Which solution meets these requirements?

- A. Create separate IAM policies for Amazon S3 and DynamoDB access with the required permissions. Attach both IAM policies to the EC2 instance profile. Use role-based access control (RBAC) to control access to Amazon S3 or DynamoDB for the respective EKS Pods.
- B. Create separate IAM policies for Amazon S3 and DynamoDB access with the required permissions. Attach the Amazon S3 IAM policy directly to the EKS Pods for the data services and the DynamoDB policy to the EKS Pods for the UI.
- C. Create separate Kubernetes service accounts for the UI and data services to assume an IAM role. Attach the AmazonS3FullAccess policy to the data services account and the AmazonDynamoDBFullAccess policy to the UI service account.
- D. Create separate Kubernetes service accounts for the UI and data services to assume an IAM role. Use IAM Role for Service Accounts (IRSA) to provide access to the EKS Pods for the UI to Amazon S3 and the EKS Pods for the data services to DynamoDB.

<details>
<summary>Show answer</summary>

**Answer: C**

Creating separate Kubernetes service accounts that assume IAM roles (IAM Roles for Service Accounts) provides pod-level IAM permissions. Attaching the S3 policy to the data services account and the DynamoDB policy to the UI account scopes each set of pods to exactly the service it needs.

- **A is wrong:** Putting both permissions on the node (EC2) instance profile lets any pod on that node potentially use them, breaking least privilege.
- **B is wrong:** IAM policies cannot be applied directly to EKS pods; access must be granted via a service account/IAM role.
- **D is wrong:** It reverses the access — the UI should access DynamoDB only and the data services should access S3 only.
</details>

### Q201: Encrypting security certificates and storing them in highly available storage with least overhead

A company's containerized application runs on an Amazon EC2 instance. The application needs to download security certificates before it can communicate with other business applications. The company wants a highly secure solution to encrypt and decrypt the certificates in near real time. The solution also needs to store data in highly available storage after the data is encrypted. Which solution will meet these requirements with the LEAST operational overhead?

- A. Create AWS Secrets Manager secrets for encrypted certificates. Manually update the certificates as needed. Control access to the data by using fine-grained IAM access.
- B. Create an AWS Lambda function that uses the Python cryptography library to receive and perform encryption operations. Store the function in an Amazon S3 bucket.
- C. Create an AWS Key Management Service (AWS KMS) customer managed key. Allow the EC2 role to use the KMS key for encryption operations. Store the encrypted data on Amazon S3.
- D. Create an AWS Key Management Service (AWS KMS) customer managed key. Allow the EC2 role to use the KMS key for encryption operations. Store the encrypted data on Amazon Elastic Block Store (Amazon EBS) volumes.

<details>
<summary>Show answer</summary>

**Answer: C**

An AWS KMS customer managed key provides secure, near-real-time encryption and decryption, and granting the EC2 role permission to use the key keeps operational overhead low. Storing the encrypted certificates in Amazon S3 provides highly available, durable storage.

- **A is wrong:** Manually updating certificates in Secrets Manager increases operational effort.
- **B is wrong:** Writing custom Lambda crypto code introduces unnecessary custom code and operational burden.
- **D is wrong:** Amazon EBS is not "highly available" storage (it is tied to a single Availability Zone), unlike Amazon S3.
</details>

### Q202: Resilient, continuously available, cost-effective DynamoDB architecture for a growing gaming platform

An online gaming company is migrating its user data storage to Amazon DynamoDB to accommodate its growing user base. The company's DynamoDB tables store user profiles, achievements, and in-game transactions. The company requires a resilient and continuously available DynamoDB architecture to ensure a seamless gaming experience while remaining cost-effective. Which solution BEST fulfills these requirements?

- A. Create DynamoDB tables in a single AWS Region. Use on-demand capacity mode. Use global tables to replicate data across multiple Regions.
- B. Use DynamoDB Accelerator (DAX) to cache frequently accessed data. Deploy tables in a single AWS Region and enable auto scaling. Configure Cross-Region Replication manually to additional Regions.
- C. Create DynamoDB tables in multiple AWS Regions. Use on-demand capacity mode. Use DynamoDB Streams for Cross-Region Replication between Regions.
- D. Use DynamoDB global tables for automatic multi-Region replication. Deploy tables in multiple AWS Regions. Use provisioned capacity mode. Enable auto scaling.

<details>
<summary>Show answer</summary>

**Answer: D**

DynamoDB global tables provide active-active, multi-Region replication for high resilience and continuous availability. Pairing them with provisioned capacity mode and auto scaling keeps costs predictable and efficient while still handling a growing user base.

- **A is wrong:** You don't use DynamoDB global tables while keeping tables only in a single Region — replication requires tables in multiple Regions.
- **B is wrong:** DAX improves read latency but does not provide multi-Region continuous availability, and manual Cross-Region Replication adds overhead.
- **C is wrong:** On-demand can be more expensive than provisioned, and using DynamoDB Streams for replication is custom and operationally heavy compared to native global tables.
</details>

### Q203: Centralized identity for multiple AWS accounts using on-premises Active Directory with least overhead

A manufacturing company with a globally distributed development team needs to grant secure access to the company's AWS resources while adhering to security policies. The company utilizes an on-premises Active Directory for internal authentication and AWS Organizations to manage multiple AWS accounts across different projects. The desired solution should integrate with the existing infrastructure to provide centralized identity management and access control with the LEAST operational overhead. Which solution best meets these requirements?

- A. Set up AWS Directory Service to create an AWS-managed Microsoft Active Directory on AWS. Establish a trust relationship with the on-premises Active Directory. Use IAM roles that are assigned to Active Directory groups to access AWS resources within the company's AWS accounts.
- B. Create an IAM user for each developer. Manually manage permissions for each IAM user based on each user's involvement with each project. Enforce multi-factor authentication (MFA) as an additional layer of security.
- C. Use AD Connector in AWS Directory Service to connect to the on-premises Active Directory. Integrate AD Connector with AWS IAM Identity Center. Configure permission sets to give each AD group access to specific AWS accounts and resources.
- D. Use Amazon Cognito to deploy an identity federation solution. Integrate the identity federation solution with the on-premises Active Directory. Use Amazon Cognito to provide access tokens for developers to access AWS accounts and resources.

<details>
<summary>Show answer</summary>

**Answer: C**

AD Connector lets AWS authenticate users directly against the existing on-premises Active Directory without duplicating directories. Integrating it with AWS IAM Identity Center and using permission sets gives each AD group scoped access across the AWS Organizations accounts — centralized identity management with the least operational overhead.

- **A is wrong:** Deploying an AWS Managed Microsoft AD plus a trust relationship adds directory infrastructure and increases operational overhead.
- **B is wrong:** Manually managing an IAM user per developer does not scale well.
- **D is wrong:** Amazon Cognito is intended for application end-user authentication, not centralized workforce access to AWS accounts.
</details>

### Q204: Fully managed shared storage that supports Lustre clients

An online gaming company is implementing a shared storage solution for their most popular gaming platform that is hosted on an on-premises data center. The company needs the ability to use Lustre clients to access data. The solution must be fully managed. Which solution meets these requirements?

- A. Create an AWS Storage Gateway file gateway. Create a file share that uses the required client protocol. Connect the application server to the file share.
- B. Create an Amazon EC2 Windows instance. Install and configure a Windows file share role on the instance. Connect the application server to the file share.
- C. Create an Amazon Elastic File System (Amazon EFS) file system and configure it to support Lustre. Attach the file system to the origin server. Connect the application server to the file system.
- D. Create an Amazon FSx for Lustre file system. Attach the file system to the origin server. Connect the application server to the file system.

<details>
<summary>Show answer</summary>

**Answer: D**

Amazon FSx for Lustre is a fully managed, high-performance file system that natively supports the Lustre protocol and Lustre clients.

- **A is wrong:** AWS Storage Gateway file gateway supports NFS and SMB protocols, not Lustre.
- **B is wrong:** A Windows file share instance is not fully managed and only supports SMB-based file sharing.
- **C is wrong:** Amazon EFS supports NFS only and cannot be configured to support Lustre.
</details>

### Q205: Scaling a monolithic image-processing workload with the least operational overhead

A digital image processing company hosts its image processing application in an on-premises data center. The application is a monolithic application. As part of the data center evacuation exercise, the organization wants to migrate the application to the AWS Cloud. The application processes thousands of images and generates large files as part of the image processing workflow. The organization is looking for a solution that can manage the growing number of image processing jobs. The solution must also reduce the manual tasks in the image processing workflow. The company does not want to manage the underlying infrastructure of the solution. Which solution should a Solution Architect propose that will meet these requirements with the LEAST operational overhead?

- A. Use Amazon Elastic Container Service (Amazon ECS) with Amazon EC2 Spot Instances to process the images. Configure Amazon Simple Queue Service (Amazon SQS) to orchestrate the workflow. Store the processed files in Amazon Elastic File System (Amazon EFS).
- B. Use AWS Batch jobs to process the images. Use AWS Step Functions to orchestrate the workflow. Store the processed files in an Amazon S3 bucket.
- C. Use AWS Lambda functions and Amazon EC2 Spot Instances to process the images. Store the processed files in Amazon FSx.
- D. Deploy a group of Amazon EC2 instances to process the images. Use AWS Step Functions to orchestrate the workflow. Store the processed files in an Amazon Elastic Block Store (Amazon EBS) volume.

<details>
<summary>Show answer</summary>

**Answer: B**

AWS Batch is a fully managed job scheduler that can scale to thousands of batch jobs without managing the underlying infrastructure, and AWS Step Functions orchestrates the workflow to reduce manual tasks. Storing the processed files in Amazon S3 provides durable, scalable storage.

- **A is wrong:** ECS with EC2 Spot Instances still requires managing the underlying compute infrastructure.
- **C is wrong:** Mixing Lambda and EC2 Spot Instances still implies managing compute capacity.
- **D is wrong:** Running and managing a fleet of EC2 instances is the option with the highest operational burden.
</details>

### Q206: Transforming clinical trial data with customer-specific encryption and least operational effort

A medical company wants to perform transformations on a large amount of clinical trial data that comes from several customers. The company must extract the data from a relational database that contains the customer data. Then the company will transform the data by using a series of complex rules. The company will load the data to Amazon S3 when the transformations are complete. All data must be encrypted where it is processed before the company stores the data in Amazon S3. All data must be encrypted by using customer-specific keys. Which solution will meet these requirements with the LEAST amount of operational effort?

- A. Create one AWS Glue job for each customer. Attach a security configuration to each job that uses server-side encryption with Amazon S3 managed keys (SSE-S3) to encrypt the data.
- B. Create one Amazon EMR cluster for each customer. Attach a security configuration to each cluster that uses client-side encryption with a custom client-side root key (CSE-Custom) to encrypt the data.
- C. Create one AWS Glue job for each customer. Attach a security configuration to each job that uses client-side encryption with AWS KMS managed keys (CSE-KMS) to encrypt the data.
- D. Create one Amazon EMR cluster for each customer. Attach a security configuration to each cluster that uses server-side encryption with AWS KMS managed keys (SSE-KMS) to encrypt the data.

<details>
<summary>Show answer</summary>

**Answer: C**

AWS Glue is serverless, and CSE-KMS ensures each customer's data is encrypted before it is written to S3 using a customer-specific KMS key — meeting both the "encrypted where it is processed" and "customer-specific keys" requirements with the least operational effort.

- **A is wrong:** SSE-S3 uses S3-managed keys, not customer-specific keys.
- **B is wrong:** Running one Amazon EMR cluster per customer adds significant operational overhead.
- **D is wrong:** It also uses Amazon EMR clusters, which add significant operational overhead compared to serverless Glue.
</details>

### Q207: Low-latency on-premises access to data moved to S3 in the most cost-effective way

A media company runs its media rendering application on premises. The company wants to reduce storage costs and has moved all data to Amazon S3. The on-premises rendering application needs low-latency access to storage to maintain performance. Which storage solution will meet these requirements in the MOST cost-effective way?

- A. Use Mountpoint for Amazon S3 to access the data in Amazon S3 for the on-premises application.
- B. Configure an Amazon S3 File Gateway to provide storage for the on-premises application.
- C. Copy the data from Amazon S3 to Amazon FSx for Windows File Server. Configure an Amazon FSx File Gateway to provide storage for the on-premises application.
- D. Configure an on-premises file server. Use the Amazon S3 API to connect to S3 storage. Configure the application to access the storage from the on-premises file server.

<details>
<summary>Show answer</summary>

**Answer: B**

Amazon S3 File Gateway is a cost-effective, common pattern for on-premises applications that need fast (low-latency) access to S3-backed storage. It caches frequently accessed data locally while durably storing all data as objects in S3.

- **A is wrong:** Mountpoint for Amazon S3 is not designed for managed on-premises integration and does not provide the on-premises caching gateway pattern.
- **C is wrong:** Copying data to FSx for Windows File Server adds an extra managed file system layer with higher cost and complexity.
- **D is wrong:** A self-managed on-premises file server using the S3 API has higher operational overhead and is less reliable than Storage Gateway.
</details>

### Q208: Tiered access control for a CloudFront streaming platform (Select TWO)

A streaming platform uses Amazon CloudFront to deliver content from S3. The platform offers two tiers — free users can watch three specific preview clips, while premium subscribers have access to the full library of 800 videos. The solutions architect must implement access control that restricts content appropriately for each tier. Which combination of features should be used? (Select TWO.)

- A. CloudFront Signed URLs for free users to access the three preview clips
- B. CloudFront Geo-Restriction with an allowlist for premium subscriber countries
- C. CloudFront Signed Cookies for premium subscribers to access the full video library
- D. AWS WAF rate-based rules to limit free user requests to three per session
- E. Lambda@Edge at the Origin Response trigger to filter content based on user tier

<details>
<summary>Show answer</summary>

**Answer: A, C**

Signed URLs grant time-limited access to individual files, which fits free users needing exactly three preview clips — one URL per clip that expires after a set time (A). Signed Cookies grant access to multiple files with a single cookie, ideal for premium subscribers who need access to the entire 800-video library without generating a URL per file (C).

- **B is wrong:** Geo-Restriction controls access by country — it has no concept of user subscription tier.
- **D is wrong:** WAF rate-based rules throttle request frequency, not which content a user is entitled to.
- **E is wrong:** Lambda@Edge at Origin Response runs *after* the origin has already processed the request, so it cannot enforce tier-based access control upfront.
</details>

### Q209: Region for an ACM certificate used by a CloudFront distribution

A solutions architect is configuring HTTPS for a new Amazon CloudFront distribution. The company uses a custom domain cloudexperts.solutions.com and needs a free SSL/TLS certificate. The company's infrastructure is deployed in ap-southeast-2 (Sydney). Where must the AWS Certificate Manager (ACM) certificate be provisioned?

- A. ap-southeast-2 (Sydney) — the same region as the company's infrastructure
- B. us-west-2 (Oregon) — the closest US region to Sydney
- C. us-east-1 (N. Virginia) — regardless of where the infrastructure or users are located
- D. Any region — CloudFront automatically replicates ACM certificates globally

<details>
<summary>Show answer</summary>

**Answer: C**

A certificate used with CloudFront MUST be created in us-east-1 (N. Virginia) regardless of where the infrastructure, origin, or users are located.

- **A is wrong:** ACM certificates in ap-southeast-2 work for ALBs and other regional services in Sydney — but not for CloudFront.
- **B is wrong:** There is no rule linking CloudFront to the "nearest US region."
- **D is wrong:** CloudFront does not auto-discover or replicate ACM certificates globally.
</details>

### Q210: Improving global image/video load performance with the least architectural change

A global retail company serves product images and videos to customers worldwide through their e-commerce website. The images are stored in Amazon S3 in us-east-1. Users in Asia and Australia are complaining about slow page load times. A solutions architect needs to improve performance for global users with the least amount of architectural change. Which solution best addresses this requirement?

- A. Migrate the S3 bucket to ap-southeast-1 to be closer to Asian users
- B. Enable S3 Transfer Acceleration on the bucket to speed up content delivery
- C. Create an Amazon CloudFront distribution with the S3 bucket as the origin
- D. Deploy EC2 instances in multiple regions and replicate the images across all of them

<details>
<summary>Show answer</summary>

**Answer: C**

CloudFront is specifically designed to solve this exact problem, caching content at 400+ edge locations worldwide so users in Asia and Australia are served from a nearby location.

- **A is wrong:** Migrating the S3 bucket to ap-southeast-1 helps Asian users but hurts US and European users.
- **B is wrong:** S3 Transfer Acceleration speeds up uploads. It doesn't cache content or improve download performance for website visitors.
- **D is wrong:** Deploying EC2 instances across multiple regions to replicate images is massively over-engineered for static content delivery.
</details>

### Q211: Resilient CloudFront architecture with automatic S3 origin failover

A media company hosts a static website using Amazon S3 and delivers it through Amazon CloudFront. The website serves users globally and must remain available even if the primary S3 bucket in us-east-1 becomes unavailable. The solutions architect must design a resilient architecture with automatic recovery and no manual intervention. Which solution meets these requirements?

- A. Enable S3 Cross-Region Replication to us-west-2 and configure Route 53 failover routing between the two S3 buckets
- B. Create a CloudFront Origin Group with the us-east-1 S3 bucket as the primary origin and a replicated us-west-2 S3 bucket as the secondary — CloudFront will automatically failover on 5xx errors
- C. Deploy an Application Load Balancer across two regions and configure CloudFront to use the ALB as the origin with health checks
- D. Configure AWS Lambda to monitor the primary S3 bucket and automatically update the CloudFront distribution's origin if the bucket becomes unavailable

<details>
<summary>Show answer</summary>

**Answer: B**

A CloudFront Origin Group designates a primary and a secondary origin and automatically retries with the secondary — transparently, within the same user request — when the primary returns a 5xx error. This gives automatic recovery with no manual intervention.

- **A is wrong:** Route 53 failover routing involves manual configuration, DNS delays, and a potential downtime window.
- **C is wrong:** An ALB cannot span two AWS regions.
- **D is wrong:** A Lambda function monitoring S3 and updating the CloudFront origin configuration introduces significant latency in the failover process.
</details>

### Q212: Improving CloudFront cache hit ratio for varying query string order

A developer notices that their CloudFront distribution's cache hit ratio is only 35% — far below the expected 85%. Investigation reveals that the application sends requests with query strings in varying parameter orders. For example, `/products?color=red&size=M` and `/products?size=M&color=red` return identical content but are being treated as different cache entries by CloudFront. What is the MOST effective solution to improve the cache hit ratio?

- A. Increase the CloudFront Maximum TTL from 86,400 seconds to 604,800 seconds
- B. Configure the CloudFront cache policy to normalize query string parameter order so that equivalent query strings share the same cache key
- C. Submit a CloudFront invalidation for `/*` every hour to clear fragmented cache entries
- D. Create separate CloudFront distributions for each unique query string combination

<details>
<summary>Show answer</summary>

**Answer: B**

Normalizing query string parameter order in the cache policy means the same content is cached under a single cache key, even when query parameters arrive in different orders — directly fixing the root cause of the low hit ratio.

- **A is wrong:** Increasing the Maximum TTL helps content stay cached longer, but it doesn't fix the root cause of duplicate cache keys.
- **C is wrong:** Submitting hourly invalidations actively makes the problem worse by clearing the cache repeatedly.
- **D is wrong:** Creating separate distributions per query string is architecturally absurd and operationally unmanageable.
</details>

### Q213: Static IPs for firewall whitelisting plus regulatory country blocking (Select TWO)

A financial services company uses Amazon CloudFront to deliver a trading application to enterprise clients worldwide. Corporate clients require that the application's IP addresses remain static so they can be whitelisted in enterprise firewalls. The company also needs to serve users in 45 countries but must block access from users in three specific countries due to regulatory requirements. Which combination of services and features should the solutions architect recommend? (Select TWO.)

- A. Amazon CloudFront with Geo-Restriction blocklist for the three restricted countries
- B. AWS Global Accelerator to provide two static Anycast IP addresses for firewall whitelisting
- C. Amazon Route 53 with geolocation routing to redirect users from restricted countries
- D. AWS WAF with geo-match conditions to block traffic from the three restricted countries
- E. Amazon CloudFront with AWS Shield Advanced to provide static IP addresses

<details>
<summary>Show answer</summary>

**Answer: A, B**

CloudFront Geo-Restriction with a blocklist satisfies the regulatory requirement to block the three restricted countries (A). AWS Global Accelerator provides two static Anycast IP addresses that never change, satisfying the firewall whitelisting requirement (B).

- **C is wrong:** Route 53 geolocation relies on DNS and TTL propagation, doesn't enforce hard blocks, and doesn't provide static IPs.
- **D is wrong:** WAF doesn't solve the static IP requirement.
- **E is wrong:** Shield Advanced provides DDoS protection — it does not provide static IP addresses.
</details>

### Q214: Immediately serving updated content after an S3 origin update

A company has a CloudFront distribution with an S3 origin. The development team deploys a new version of the website and updates the S3 content. However, users continue seeing the old version of the website for up to 24 hours because of the cached content at CloudFront edge locations. The team needs to immediately serve the updated content to all users worldwide. What is the MOST operationally efficient solution?

- A. Delete and recreate the CloudFront distribution to clear all cached content
- B. Change the CloudFront Minimum TTL to 0 seconds on all cache behaviors
- C. Submit a CloudFront cache invalidation request for `/*` to remove all cached objects from edge locations immediately
- D. Contact AWS Support to manually purge the CloudFront edge caches

<details>
<summary>Show answer</summary>

**Answer: C**

A CloudFront cache invalidation with path `/*` immediately removes all cached objects from every edge location worldwide, so users are served the updated content right away.

- **A is wrong:** Deleting and recreating the distribution is an extreme measure that changes your distribution domain name, breaks existing URLs, requires reconfiguring SSL certificates and behaviors, and causes significant downtime.
- **B is wrong:** Changing Minimum TTL to 0 affects future cache behavior — it doesn't retroactively clear objects that are currently cached at edge locations.
- **D is wrong:** AWS Support does not manually purge edge caches on request — it's operationally burdensome and completely unnecessary.
</details>

### Q215: Applying different caching rules per URL path in one CloudFront distribution

A company runs a web application that serves both static HTML/CSS/JavaScript files and dynamic API responses. Static files rarely change and should be cached aggressively. API responses are unique per user and must not be cached. Both types of content are served from the same CloudFront distribution using different URL paths. Which CloudFront feature allows the solutions architect to apply different caching rules to different URL paths within a single distribution?

- A. CloudFront Origin Groups with separate TTL configurations per group
- B. CloudFront Cache Behaviors with different path patterns and cache policies for each content type
- C. CloudFront Lambda@Edge functions at the Origin Response trigger to set TTL per content type
- D. Multiple CloudFront distributions — one for static content and one for API responses

<details>
<summary>Show answer</summary>

**Answer: B**

Cache Behaviors are CloudFront's mechanism for applying different rules to different URL paths within a single distribution. Each behavior matches a path pattern and applies its own cache policy.

- **A is wrong:** Origin Groups don't have TTL configurations and don't route based on URL path patterns.
- **C is wrong:** Lambda@Edge at the origin is an overly complex approach to what Cache Behaviors solve natively and cleanly.
- **D is wrong:** Two separate distributions would work technically but is operationally unnecessary and more complex to manage.
</details>

### Q216: Verifying JWT tokens via an external API as close to the user as possible

A company wants to implement authentication for their CloudFront-delivered application. When users make a request, the system must verify a JWT token by calling an external authentication API. Only requests with valid tokens should be forwarded to the origin. The verification must happen as close to the user as possible to minimize latency. Which solution meets these requirements?

- A. Create a CloudFront Function at the Viewer Request trigger to call the authentication API and verify the JWT token
- B. Create a Lambda@Edge function at the Viewer Request trigger that calls the external authentication API and returns a 403 response for invalid tokens
- C. Create a Lambda@Edge function at the Origin Request trigger to verify JWT tokens before the request reaches the origin
- D. Configure an Application Load Balancer with an authentication rule to verify JWT tokens before CloudFront forwards requests

<details>
<summary>Show answer</summary>

**Answer: B**

Lambda@Edge at the Viewer Request trigger perfectly satisfies both requirements — it runs at the edge (closest to the user) and can make network calls to the external authentication API, returning a 403 for invalid tokens before forwarding valid requests to the origin.

- **A is wrong:** CloudFront Functions cannot make network calls, so they can't call an external authentication API.
- **C is wrong:** The Origin Request trigger runs only on cache misses and only after CloudFront has decided to contact the origin — not as close to the user as possible.
- **D is wrong:** An ALB authentication rule contradicts the requirement that verification happens "as close to the user as possible."
</details>

### Q217: Reducing CloudFront costs when users are concentrated in a few regions

A startup has deployed a SaaS application using Amazon CloudFront. After three months of operation, the team analyzes their AWS bill and finds CloudFront costs are 40% higher than projected. Traffic analytics show that 96% of users are in the United States and Canada, with only 4% from other continents. The team wants to significantly reduce CloudFront costs while maintaining excellent performance for the vast majority of users. What is the MOST cost-effective solution?

- A. Enable CloudFront Origin Shield in us-east-1 to reduce origin requests and lower overall costs
- B. Switch the CloudFront distribution Price Class from All to Price Class 100 to serve content only from North America and Europe edge locations
- C. Migrate the application origin from S3 to an EC2 instance to reduce data transfer costs
- D. Reduce all CloudFront cache TTL values to 60 seconds to minimize the volume of cached data stored at edge locations

<details>
<summary>Show answer</summary>

**Answer: B**

96% of users are in the US and Canada — which are covered by Price Class 100 (North America and Europe). Restricting the distribution to the cheaper edge locations significantly lowers CloudFront costs while keeping excellent performance for the vast majority of users.

- **A is wrong:** Origin Shield helps reduce origin infrastructure costs, not CloudFront service costs.
- **C is wrong:** The origin type (S3 vs EC2) has no bearing on CloudFront pricing.
- **D is wrong:** Reducing TTL to 60 seconds causes more cache misses, which will increase costs.
</details>

### Q218: Adding a static security header at the edge with minimum latency and cost

A solutions architect needs to add a Content-Security-Policy (CSP) security header to all CloudFront responses for compliance requirements. The header value is static and does not change based on the request. The solution must add the header with the absolute minimum latency overhead and at the lowest possible cost. Which solution should the architect choose?

- A. Create a Lambda@Edge function at the Viewer Response trigger to add the Content-Security-Policy header to every response
- B. Configure the origin application to include the Content-Security-Policy header in all responses before they reach CloudFront
- C. Create a CloudFront Function at the Viewer Response trigger to add the Content-Security-Policy header to every response
- D. Attach an AWS WAF Web ACL to the CloudFront distribution and create a custom rule to inject the Content-Security-Policy header

<details>
<summary>Show answer</summary>

**Answer: C**

Adding a static header to every response is a simple, deterministic operation — no network calls, no external dependencies, pure string manipulation. CloudFront Functions run at the edge with the lowest latency and cost, making them ideal for this case.

- **A is wrong:** Lambda@Edge at Viewer Response would work technically, but it's significantly more expensive and slower than CloudFront Functions for this simple operation.
- **B is wrong:** Configuring the origin to add the header works but adds complexity — every origin server or service must be individually configured.
- **D is wrong:** AWS WAF is not designed to inject custom headers into responses.
</details>

### Q219: Reducing origin load during traffic spikes through cache optimization

A company serves a global e-commerce website through Amazon CloudFront. The origin is an Application Load Balancer in us-east-1 backed by an Auto Scaling group. During a major product launch, traffic spikes 500% — causing the origin to become overwhelmed. A solutions architect needs to design a solution that reduces the load on the origin during traffic spikes while maintaining fast response times for users. Which strategy MOST effectively reduces origin load during traffic spikes?

- A. Increase the EC2 instance sizes in the Auto Scaling group to handle higher traffic volumes
- B. Optimize CloudFront cache behaviors — increase TTL for static assets, use versioned filenames for deployables, and ensure cache-control headers are set correctly on the origin to maximize the cache hit ratio
- C. Deploy the application in multiple AWS regions and use Route 53 latency-based routing to distribute traffic
- D. Enable CloudFront Real-Time Logs to monitor traffic patterns and manually adjust cache settings during the spike

<details>
<summary>Show answer</summary>

**Answer: B**

Increasing TTL for static assets, using versioned filenames for deployables (eliminating constant invalidations), and ensuring proper Cache-Control headers on the origin maximizes the cache hit ratio — so CloudFront serves most requests from the edge and the origin load drops during spikes.

- **A is wrong:** Scaling up EC2 instance sizes increases capacity to handle origin load — but it doesn't reduce that load.
- **C is wrong:** This is horizontal scaling of the origin, not load reduction through caching.
- **D is wrong:** Real-Time Logs provide observability — they don't reduce origin load.
</details>

### Q220: Restricting content to specific countries with a custom error page for blocked users

A media company distributes licensed sports broadcast content through Amazon CloudFront. Due to broadcasting rights agreements, the content must be accessible only in the United Kingdom, Ireland, and Australia. Users in all other countries must be blocked. Blocked users should see a custom error page explaining regional restrictions rather than a generic error. Which combination of CloudFront features implements this requirement?

- A. AWS WAF with geo-match rules for UK, Ireland, and Australia — with a custom WAF block response page
- B. CloudFront Geo-Restriction with an allowlist containing UK, Ireland, and Australia — combined with a CloudFront custom error page configuration returning a user-friendly 403 response
- C. Lambda@Edge at the Viewer Request trigger to check the CloudFront-Viewer-Country header and return a custom error response for blocked countries
- D. CloudFront Signed URLs distributed only to users who have verified their location in UK, Ireland, or Australia through the application

<details>
<summary>Show answer</summary>

**Answer: B**

CloudFront Geo-Restriction with an allowlist of UK, Ireland, and Australia ensures ONLY users in those countries can access the distribution, and a CloudFront custom error page configuration returns a user-friendly 403 explaining the regional restriction.

- **A is wrong:** WAF geo-match rules are costlier and more complex to configure than CloudFront's native Geo-Restriction feature.
- **C is wrong:** It's an over-engineered solution for a requirement that CloudFront's Geo-Restriction can handle natively.
- **D is wrong:** Signed URLs control which users can access content based on a signed token — they don't block based on geography.
</details>

### Q221: Rewriting URLs at the edge with minimal latency and cost

A company uses Amazon CloudFront to deliver a web application. The application currently uses the URL path `/category/Electronics/Laptops`. The marketing team wants to change the URL structure to `/shop/laptops` for SEO purposes. The origin application cannot be modified immediately. The solution must redirect users and search engines from the old URL structure to the new one at the edge, with minimal latency and cost. Which solution best meets these requirements?

- A. Create a Lambda@Edge function at the Origin Request trigger to rewrite the URL before forwarding to the origin
- B. Create a CloudFront Function at the Viewer Request trigger to rewrite the URL path from the old structure to the new structure
- C. Configure an Application Load Balancer listener rule to redirect the old URL to the new URL before CloudFront processes the request
- D. Update the S3 origin to include redirect rules that map old URL paths to new paths using S3 website redirect configuration

<details>
<summary>Show answer</summary>

**Answer: B**

URL rewriting is a simple string transformation — no external calls, no complex logic, pure text manipulation. A CloudFront Function at the Viewer Request trigger runs at the edge with the lowest latency and cost, perfect for this case.

- **A is wrong:** For a simple URL rewrite with no external dependencies, Lambda@Edge is overkill.
- **C is wrong:** An ALB listener rule runs in the origin region — not at the edge close to the user. It doesn't meet the "edge" requirement.
- **D is wrong:** S3 redirects are origin-level, not edge-level.
</details>

### Q222: Why API responses are never cached despite a 24-hour Default TTL

A company's web application is delivered through Amazon CloudFront. The origin server returns a Cache-Control: no-store header on all API responses. However, the CloudFront distribution's Default TTL is configured to 86,400 seconds. The development team reports that all API responses are never being cached by CloudFront, even though the Default TTL is set to 24 hours. The solutions architect is asked to explain this behavior. What is the correct explanation?

- A. Cache-Control: no-store from the origin instructs CloudFront never to cache the response — the Default TTL only applies when the origin sends NO cache headers, so the origin header takes precedence
- B. The Default TTL of 86,400 seconds overrides the Cache-Control: no-store header — this behavior indicates a CloudFront configuration error
- C. The Minimum TTL must be set to 0 for Cache-Control: no-store to take effect — the Default TTL is preventing the header from working
- D. CloudFront ignores Cache-Control: no-store — a custom Cache Policy must be explicitly configured to honor this header

<details>
<summary>Show answer</summary>

**Answer: A**

When the origin explicitly sends Cache-Control: no-store, CloudFront honors that instruction and does not cache the response. The Default TTL only applies when the origin sends no cache headers, so the origin's header takes precedence.

- **B is wrong:** The Default TTL does not override Cache-Control: no-store.
- **C is wrong:** Setting Minimum TTL to 0 is not required for Cache-Control: no-store to work.
- **D is wrong:** CloudFront does not ignore Cache-Control: no-store.
</details>

### Q223: Combining CloudFront features to satisfy five access-control requirements (Select THREE)

A company is designing a CloudFront-based content delivery architecture for a premium online education platform. The platform has these requirements:

- Course videos in S3 must not be directly accessible — only through CloudFront
- Enrolled students must access all course videos and PDFs seamlessly after login
- Trial users can access two specific preview lessons — links expire after 48 hours
- Users in sanctioned countries must be blocked
- The platform must be protected from SQL injection attacks on the enrollment API

Which combination of CloudFront features satisfies ALL five requirements? (Select THREE.)

- A. OAC on the S3 origin + updated S3 bucket policy
- B. CloudFront Signed Cookies for enrolled students + CloudFront Signed URLs for trial users
- C. CloudFront Geo-Restriction blocklist + AWS WAF with SQL injection managed rule group
- D. AWS Shield Advanced for SQL injection protection + CloudFront Signed URLs for all users
- E. Lambda@Edge at Origin Request to authenticate students + CloudFront Functions for trial user access

<details>
<summary>Show answer</summary>

**Answer: A, B, C**

- **A** is the textbook solution for requirement 1 — OAC restricts S3 access to the specific CloudFront distribution only.
- **B** satisfies requirements 2 and 3 by using the right tool for each access pattern: Signed Cookies for enrolled students (multiple files seamlessly) and Signed URLs for trial users (time-limited individual links).
- **C** satisfies requirements 4 and 5: Geo-Restriction blocklist handles sanctioned-country blocking and AWS WAF handles SQL injection natively.

- **D is wrong:** Shield Advanced has no SQL injection detection or filtering capability.
- **E is wrong:** Lambda@Edge for trial access is architecturally over-engineered when native CloudFront features are available.
</details>

### Q224: Optimizing both performance and cost for a global news website (Select THREE)

A solutions architect is designing a CloudFront architecture for a global news website. The website has the following characteristics:

- Breaking news articles change every few minutes
- Feature articles are updated once a week
- Hero images and thumbnails never change once published
- The website serves 85% of users in North America and Europe
- A/B testing is needed for a new homepage layout — routing requires checking a feature flag stored in DynamoDB
- URL normalization is needed to remove trailing slashes before cache lookup

Which combination of features should the architect implement to optimize BOTH performance AND cost? (Select THREE.)

- A. Set TTL to 300 seconds for breaking news, 604,800 seconds for feature articles, and 31,536,000 seconds for images — using separate cache behaviors per content type
- B. Use Price Class 100 to serve content only from North America and Europe edge locations
- C. Use Lambda@Edge at the Viewer Request trigger for A/B testing that reads from DynamoDB
- D. Use CloudFront Functions at the Viewer Request trigger for both URL normalization AND DynamoDB-based A/B testing
- E. Use Price Class All for all edge locations to ensure best performance for the 15% international users
- F. Set a single TTL of 3,600 seconds for all content types to simplify configuration

<details>
<summary>Show answer</summary>

**Answer: A, B, C**

- **A** matches each content type's change frequency — a short 300s TTL for breaking news, ~1 week for feature articles, and ~1 year for images that never change. Separate cache behaviors apply the right rule per path, maximizing cache hits and minimizing origin load.
- **B** Price Class 100 covers North America and Europe, where 85% of users are — serving from the cheaper edge locations lowers cost while keeping performance for the vast majority.
- **C** A/B testing requires reading a feature flag from DynamoDB, which is a network call. Only Lambda@Edge can make network calls, so it's the correct tool for this routing logic.

- **D is wrong:** CloudFront Functions cannot make network calls, so they cannot read DynamoDB for A/B testing. (URL normalization alone would suit a CloudFront Function, but bundling the DynamoDB lookup into it makes this option invalid.)
- **E is wrong:** Price Class All serves every edge location, increasing cost just to benefit 15% of users — the opposite of cost optimization.
- **F is wrong:** A single 3,600s TTL ignores the very different change frequencies — it's too long for breaking news and far too short for images that never change.
</details>

### Q225: Blocking a malicious IP and forcing traffic through the ALB (Select TWO)

A company runs a web application on EC2 instances behind an Application Load Balancer. The security team discovers that a specific IP address 198.51.100.45 is sending thousands of malicious requests per minute directly to the EC2 instances, bypassing the ALB. The EC2 instances are in a public subnet with public IP addresses. The security team wants to immediately block this IP address from reaching the EC2 instances while also preventing direct internet access to EC2 instances in the future — all traffic should flow through the ALB only. Which combination of actions should the solutions architect take? (Select TWO.)

- A. Add a deny rule to the EC2 instances' Security Group blocking inbound traffic from 198.51.100.45
- B. Add a DENY rule to the Network ACL on the EC2 subnet blocking all inbound traffic from 198.51.100.45
- C. Move the EC2 instances to a private subnet, remove their public IP addresses, and update the EC2 Security Group to allow inbound traffic only from the ALB Security Group
- D. Enable AWS Shield Advanced on the EC2 instances to automatically block malicious IP addresses
- E. Attach an AWS WAF Web ACL to the EC2 instances directly to filter malicious requests

<details>
<summary>Show answer</summary>

**Answer: B, C**

Network ACLs support explicit DENY rules at the subnet level, so adding a DENY for 198.51.100.45 immediately blocks that IP from reaching the instances (B). Moving the EC2 instances to a private subnet and removing their public IPs eliminates the ability for the internet to reach them directly — all traffic must now flow through the ALB (C).

- **A is wrong:** Security Groups have no DENY rules — they only ALLOW.
- **D is wrong:** Shield Advanced protects against DDoS volumetric attacks at layers 3 and 4 — it doesn't block specific malicious request IPs at the application level.
- **E is wrong:** WAF cannot be attached directly to EC2 instances.
</details>

### Q226: Handling a known flash-sale spike while minimizing cost in normal traffic

A company runs a web application on an Auto Scaling Group of EC2 instances behind an Application Load Balancer. The ASG is configured with a minimum of 2 and a maximum of 10 instances. During a flash sale event, traffic increases dramatically over 5 minutes — faster than the default Auto Scaling cooldown period allows new instances to become healthy and serve traffic. Several users experience timeouts and errors during the scaling event. Which solution BEST addresses the slow scaling response while minimizing cost during normal traffic periods?

- A. Set the ASG minimum capacity to 10 instances permanently to ensure enough capacity is always available
- B. Configure a scheduled scaling action to increase capacity 30 minutes before the flash sale, combined with a target tracking scaling policy based on ALB request count per target
- C. Replace the Auto Scaling Group with a fixed fleet of 10 Reserved Instances to guarantee consistent capacity
- D. Enable EC2 instance hibernation on the ASG instances so they can resume faster during traffic spikes

<details>
<summary>Show answer</summary>

**Answer: B**

Scheduled scaling pre-warms capacity before the known event, so instances are already healthy when the flash sale starts. Target tracking based on ALB request count per target then automatically scales during the event. This handles the spike while keeping costs low during normal traffic.

- **A is wrong:** Setting minimum to 10 permanently solves the scaling problem but wastes significant compute capacity during normal traffic.
- **C is wrong:** A fixed fleet of 10 Reserved Instances eliminates Auto Scaling entirely and always pays for peak capacity.
- **D is wrong:** Hibernated instances in an ASG are terminated, not hibernated — hibernation is not a valid ASG scaling mechanism.
</details>

### Q227: Improving read performance for a read-heavy RDS MySQL e-commerce app

A company runs a read-heavy e-commerce application with an Amazon RDS MySQL database in a single-AZ deployment. The database is experiencing high CPU utilization and slow query response times — approximately 80% of database queries are read operations. The development team cannot modify the application code significantly. The solutions architect needs to improve read performance with minimal application changes. Which solution MOST effectively addresses the read performance issue?

- A. Upgrade the RDS instance to a larger instance type with more CPU and memory
- B. Enable RDS Multi-AZ deployment to distribute read traffic across the primary and standby instances
- C. Create one or more Amazon RDS Read Replicas and update the application's database connection string to point read operations to the Read Replica endpoint
- D. Migrate the database from MySQL to Amazon DynamoDB to take advantage of its horizontal scaling capabilities

<details>
<summary>Show answer</summary>

**Answer: C**

RDS Read Replicas are purpose-built for read-heavy workloads. They offload read traffic from the primary using asynchronous replication, directly relieving the high CPU and slow query times, and require only a connection-string change for read operations — minimal application impact.

- **A is wrong:** Scaling up the instance temporarily helps but doesn't architecturally solve the read-heavy problem.
- **B is wrong:** RDS Multi-AZ standby instances do NOT serve read traffic — they exist solely for automatic failover.
- **D is wrong:** Migrating from MySQL to DynamoDB is a major architectural change requiring a significant application rewrite.
</details>

### Q228: Securing premium video for paying subscribers only (Select TWO)

A media company delivers premium video content through Amazon CloudFront from an S3 origin. The company wants to ensure that only paying subscribers can access the video files, that individual video links expire after 24 hours, and that the S3 bucket cannot be accessed directly — bypassing CloudFront. Which combination of CloudFront features implements ALL three requirements? (Select TWO.)

- A. Configure CloudFront Signed URLs with a 24-hour expiry for each video file and distribute them only to authenticated subscribers
- B. Configure CloudFront Geo-Restriction to limit access to countries where the company has paying subscribers
- C. Create a CloudFront Origin Access Control (OAC) and update the S3 bucket policy to allow access only from the CloudFront distribution
- D. Enable AWS WAF on the CloudFront distribution to block unauthenticated requests
- E. Configure CloudFront to require HTTPS and use AWS Certificate Manager for SSL termination

<details>
<summary>Show answer</summary>

**Answer: A, C**

Signed URLs are exactly right for individual-file access with time-limited expiry — a 24-hour URL per video grants access only to authenticated subscribers (A). Origin Access Control locks the S3 bucket so ONLY the CloudFront distribution can access it, preventing direct-URL bypass (C).

- **B is wrong:** Geo-Restriction controls access by country — not by subscription status.
- **D is wrong:** WAF filters traffic based on request patterns, not to enforce subscription-based business logic.
- **E is wrong:** HTTPS with ACM is a security best practice for encrypting data in transit — it doesn't restrict who can access the content.
</details>

### Q229: Meeting a 5-minute RPO and 1-minute RTO for RDS PostgreSQL

A financial services company runs a mission-critical application on Amazon RDS for PostgreSQL. The RPO (Recovery Point Objective) is 5 minutes, and the RTO (Recovery Time Objective) is 1 minute. The database currently uses automated backups with a 7-day retention period. During a recent DR test, the team discovered that restoring from an automated backup took 45 minutes — far exceeding the 1-minute RTO. Which solution meets BOTH the RPO and RTO requirements?

- A. Increase the automated backup retention period to 35 days to have more restore points available
- B. Enable RDS Multi-AZ deployment — in the event of a primary failure, the standby automatically promotes within approximately 60 seconds
- C. Create an RDS Read Replica in a second Availability Zone and manually promote it during a failure event
- D. Take manual RDS snapshots every 5 minutes using a Lambda function and restore from the most recent snapshot during failures

<details>
<summary>Show answer</summary>

**Answer: B**

When the primary fails, RDS Multi-AZ automatically promotes the standby — typically within 60-120 seconds — meeting the 1-minute RTO, while synchronous replication to the standby keeps data loss near zero, satisfying the 5-minute RPO.

- **A is wrong:** Longer backup retention gives more restore points — but restoring from any backup still takes 30-45+ minutes.
- **C is wrong:** Promoting a Read Replica manually introduces human-intervention latency, failing the 1-minute RTO.
- **D is wrong:** Manual snapshots every 5 minutes could meet the RPO — but snapshot restoration still takes 30-45+ minutes.
</details>

### Q230: Addressing Lambda throttling for bursty image processing with SQS

A company processes image uploads from users. When an image is uploaded to S3, a Lambda function is triggered to resize the image and generate thumbnails. During peak hours, thousands of images are uploaded simultaneously. The Lambda function is timing out and the SQS queue depth is growing. The team discovers that Lambda is hitting its concurrency limit and throttling requests. Which solution MOST effectively addresses the Lambda throttling issue while maintaining the event-driven architecture?

- A. Replace Lambda with EC2 instances in an Auto Scaling Group to handle the image processing workload
- B. Request a Lambda concurrency limit increase through AWS Service Quotas and configure a reserved concurrency limit for the image processing function
- C. Configure S3 event notifications to write to an SQS queue, and set up Lambda with the SQS queue as an event source — Lambda will automatically scale concurrency to process the backlog, and the queue provides buffering during traffic spikes
- D. Increase the Lambda function timeout from 3 seconds to 15 minutes to allow more processing time per invocation

<details>
<summary>Show answer</summary>

**Answer: C**

Using SQS as an event source for Lambda is the correct pattern for high-volume, bursty workloads. The queue buffers the spike of uploads while Lambda scales concurrency to drain the backlog, keeping the event-driven architecture intact.

- **A is wrong:** Replacing Lambda with EC2 loses the serverless benefits.
- **B is wrong:** A concurrency increase alone doesn't solve the bursty nature of the problem.
- **D is wrong:** Increasing the timeout from 3 seconds to 15 minutes doesn't solve throttling.
</details>

### Q231: Choosing an S3 encryption option for company-controlled, auditable, revocable keys

A company stores sensitive customer data in Amazon S3. The security team requires that all data must be encrypted at rest, encryption keys must be managed by the company (not AWS), and all key usage must be auditable. Additionally, if the company chooses to revoke a key, all data encrypted with that key must become immediately inaccessible. Which S3 encryption option meets ALL requirements?

- A. SSE-S3 (Server-Side Encryption with S3-managed keys) — AWS manages encryption automatically
- B. SSE-KMS (Server-Side Encryption with AWS KMS keys) — using an AWS managed key
- C. SSE-KMS with a Customer Managed Key (CMK) — the company creates and controls the key in AWS KMS, audits key usage in CloudTrail, and can disable or delete the key to revoke access
- D. SSE-C (Server-Side Encryption with Customer-Provided Keys) — the company provides the key with each API request

<details>
<summary>Show answer</summary>

**Answer: C**

SSE-KMS with a Customer Managed Key (CMK) satisfies all requirements. The company creates and fully controls the key in AWS KMS, all key usage is logged in CloudTrail for auditing, and disabling or deleting the key makes all data encrypted with it immediately inaccessible.

- **A is wrong:** With SSE-S3 the company has no control over the keys, cannot audit individual usage, and cannot revoke access.
- **B is wrong:** SSE-KMS with an AWS managed key provides KMS integration and CloudTrail logging — but the company doesn't fully control the key.
- **D is wrong:** SSE-C requires the customer to provide the encryption key with EVERY API request.
</details>

### Q232: Reducing ASG recovery time during AZ failures

A company runs a stateless web application on EC2 instances in an Auto Scaling Group across two Availability Zones. The ALB distributes traffic evenly. During an AZ failure simulation, the ASG successfully launches replacement instances in the surviving AZ — but the application experiences elevated error rates for approximately 8 minutes while new instances initialize and pass health checks. Which solution MOST effectively reduces the recovery time during AZ failures?

- A. Enable EC2 instance termination protection on all ASG instances to prevent them from being terminated during AZ events
- B. Configure the ASG with lifecycle hooks to delay health check registration until the application has fully initialized, combined with pre-warming by maintaining warm pool instances
- C. Switch from an Application Load Balancer to a Network Load Balancer for faster health check detection
- D. Enable RDS Multi-AZ to ensure database availability during EC2 recovery

<details>
<summary>Show answer</summary>

**Answer: B**

ASG Warm Pools maintain a pool of pre-initialized, stopped instances that can launch significantly faster than starting fresh instances, and lifecycle hooks ensure an instance only registers as healthy once the application has fully initialized — directly cutting the initialization-driven recovery delay.

- **A is wrong:** Termination protection would actually prevent the ASG from managing instance lifecycle properly.
- **C is wrong:** The bottleneck is application initialization time — not health check detection speed.
- **D is wrong:** RDS Multi-AZ has no effect on how quickly EC2 application instances initialize and pass health checks.
</details>

### Q233: Reducing leaderboard latency for a read-heavy gaming app

A gaming company's leaderboard application reads player rankings from an Amazon RDS MySQL database. As the game grows, the leaderboard page experiences response times exceeding 3 seconds because the ranking queries are computationally expensive and run against millions of rows. The rankings update every 60 seconds. The team needs to reduce leaderboard response time to under 100 milliseconds. Which solution MOST effectively meets the latency requirement?

- A. Create an RDS Read Replica and direct all leaderboard queries to the replica
- B. Implement Amazon ElastiCache for Redis — cache the computed leaderboard results with a 60-second TTL so the expensive ranking query runs once per minute and all subsequent requests are served from cache in microseconds
- C. Upgrade the RDS instance to a memory-optimized instance type to improve query execution speed
- D. Enable RDS Performance Insights to identify and optimize the slow ranking queries

<details>
<summary>Show answer</summary>

**Answer: B**

ElastiCache for Redis is purpose-built for this exact use case. With the cache-aside pattern and a 60-second TTL, the expensive ranking query runs only once per minute and all other requests are served from cache in microseconds — easily meeting the sub-100 ms target for read-heavy, periodically updated data.

- **A is wrong:** A Read Replica helps reduce load on the primary but still executes the expensive ranking query against millions of rows.
- **C is wrong:** Upgrading to a memory-optimized instance improves query execution speed — but complex ranking queries on millions of rows will still take seconds on any single RDS instance size.
- **D is wrong:** Performance Insights identifies slow queries and their causes — it's a diagnostic tool, not a performance solution.
</details>

### Q234: Exposing a single microservice to customer VPCs with overlapping CIDRs

A company runs a SaaS platform and needs to expose a single microservice to multiple enterprise customers. Each customer has their own VPC in their own AWS account and their CIDR ranges overlap — some customers use 10.0.0.0/16, some use 172.16.0.0/16, and others use 192.168.0.0/16. The company needs to expose ONLY this specific microservice — not their entire VPC — to customers privately without traversing the internet. Which solution correctly handles overlapping CIDRs while exposing only the specific microservice?

- A. Create VPC Peering connections between the SaaS provider VPC and each customer VPC
- B. Deploy the microservice behind a Network Load Balancer and create an AWS PrivateLink Endpoint Service — customers create Interface VPC Endpoints in their own VPCs to connect to the service
- C. Deploy AWS Transit Gateway and attach all customer VPCs — use route tables to expose only the microservice subnet
- D. Assign a public IP address to the microservice and use HTTPS for secure communication between customers and the service

<details>
<summary>Show answer</summary>

**Answer: B**

AWS PrivateLink is the only option that handles overlapping CIDRs AND exposes only a specific service. The microservice sits behind a Network Load Balancer fronted by an Endpoint Service; each customer connects via an Interface VPC Endpoint, so no VPC routing (and therefore no CIDR coordination) is required, and only that one service is exposed — never the whole VPC.

- **A is wrong:** Customers have overlapping ranges (10.0.0.0/16, etc.), so peering connections would be impossible.
- **C is wrong:** Transit Gateway also requires non-overlapping CIDRs for routing to work correctly.
- **D is wrong:** Assigning a public IP and using HTTPS means traffic traverses the public internet — violating the private connectivity requirement.
</details>

### Q235: Securing an RDS MySQL database tier — no internet, app-tier only, encrypted in transit (Select TWO)

A solutions architect is designing the database tier for a multi-tier application in a VPC. The application uses Amazon RDS for MySQL. The security team requires that the database must not be accessible from the internet, must only accept connections from the application tier EC2 instances, and all connections to the database must be encrypted in transit. Which combination of configurations meets ALL three security requirements? (Select TWO.)

- A. Place the RDS instance in a private subnet with no route to the Internet Gateway, and configure the RDS Security Group to allow inbound port 3306 only from the application-tier Security Group
- B. Place the RDS instance in a public subnet and use a strong password policy to prevent unauthorized access
- C. Enable RDS encryption at rest using AWS KMS to secure data stored in the database
- D. Enable the require_secure_transport parameter in the RDS parameter group and configure the application to connect using SSL/TLS
- E. Deploy the RDS instance in a Multi-AZ configuration to automatically encrypt replication traffic between primary and standby

<details>
<summary>Show answer</summary>

**Answer: A, D**

Placing RDS in a private subnet with no route to the Internet Gateway handles both network-layer and instance-layer controls in one answer — it blocks internet access and the Security Group rule restricts inbound 3306 to only the application-tier Security Group (A). Enabling the require_secure_transport parameter forces all database connections to use SSL/TLS, meeting the encryption-in-transit requirement (D).

- **B is wrong:** Public subnet placement provides internet accessibility regardless of Security Group rules.
- **C is wrong:** Encryption at rest (KMS) protects stored data on disk — it has no effect on encryption in transit.
- **E is wrong:** Multi-AZ replication between primary and standby is encrypted by AWS internally — it doesn't address client connection encryption.
</details>

### Q236: Preventing future accidental deletions in S3 (Select TWO)

A company stores critical business documents in Amazon S3. A developer accidentally deletes an important file, and the company needs to recover it. The S3 bucket does not have versioning enabled, and the file was deleted 3 days ago. The company wants to prevent this from happening again. The file cannot be recovered. Which combination of S3 features should be enabled to prevent future accidental deletions and enable recovery? (Select TWO.)

- A. Enable S3 Versioning on the bucket — all future object versions are preserved and deleted objects can be recovered by removing the delete marker
- B. Enable S3 Cross-Region Replication to automatically copy all objects to a bucket in another region
- C. Enable MFA Delete on the bucket — requires multi-factor authentication to permanently delete object versions or disable versioning
- D. Enable S3 Intelligent-Tiering to automatically move objects to cheaper storage classes and protect against deletion
- E. Configure an S3 Lifecycle policy to transition objects to Glacier after 30 days to prevent accidental deletion

<details>
<summary>Show answer</summary>

**Answer: A, C**

With S3 Versioning enabled, deleted objects only get a delete marker — the object isn't permanently gone and can be recovered by removing the marker (A). MFA Delete adds a second factor of authentication required to permanently delete object versions or suspend versioning, preventing accidental or malicious permanent deletion (C).

- **B is wrong:** Cross-Region Replication copies objects to another region — providing geographic redundancy, not deletion protection.
- **D is wrong:** Intelligent-Tiering automatically moves objects between storage tiers based on access patterns — it doesn't protect against deletion.
- **E is wrong:** Lifecycle policies transition objects to Glacier for cost savings — they don't prevent deletion.
</details>

## References

- [AWS Solutions Architect Associate - Official Exam Guide](https://aws.amazon.com/certification/certified-solutions-architect-associate/)
