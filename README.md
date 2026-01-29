# aws-notes
Notes about the AWS certifications

## AWS Cloud Practitioner Certification - Basic Knowledge

### AWS Well-Architected Framework

The AWS Well-Architected Framework helps you understand the pros and cons of decisions you make while building systems on AWS. It provides a consistent approach for customers and partners to evaluate architectures and implement scalable designs.

**The Six Pillars:**

1. **Operational Excellence**
   - Focus: Running and monitoring systems to deliver business value
   - Key practices: Automating changes, responding to events, defining standards to manage daily operations
   - Design principles: Perform operations as code, make frequent small reversible changes, refine operations procedures frequently, anticipate failure, learn from operational failures

2. **Security**
   - Focus: Protecting information, systems, and assets
   - Key practices: Strong identity foundation, traceability, security at all layers, automation, data protection
   - Design principles: Implement a strong identity foundation, enable traceability, apply security at all layers, automate security best practices, protect data in transit and at rest, keep people away from data, prepare for security events

3. **Reliability**
   - Focus: Ensuring a workload performs its intended function correctly and consistently
   - Key practices: Automatic recovery, testing recovery procedures, scaling horizontally, managing change
   - Design principles: Automatically recover from failure, test recovery procedures, scale horizontally, stop guessing capacity, manage change through automation

4. **Performance Efficiency**
   - Focus: Using computing resources efficiently to meet system requirements
   - Key practices: Democratizing advanced technologies, going global in minutes, using serverless architectures
   - Design principles: Democratize advanced technologies, go global in minutes, use serverless architectures, experiment more often, consider mechanical sympathy

5. **Cost Optimization**
   - Focus: Avoiding unnecessary costs
   - Key practices: Adopting a consumption model, measuring overall efficiency, analyzing expenditure
   - Design principles: Implement cloud financial management, adopt a consumption model, measure overall efficiency, stop spending money on undifferentiated heavy lifting, analyze and attribute expenditure

6. **Sustainability**
   - Focus: Minimizing environmental impacts of running cloud workloads
   - Key practices: Understanding your impact, establishing sustainability goals, maximizing utilization
   - Design principles: Understand your impact, establish sustainability goals, maximize utilization, anticipate and adopt new more efficient hardware and software, use managed services, reduce downstream impact

### Amazon S3 Storage Classes

Amazon S3 offers different storage classes designed for different use cases, balancing cost and performance.

1. **S3 Standard**
   - Use case: Frequently accessed data
   - Availability: 99.99%
   - Durability: 99.999999999% (11 9's)
   - Features: Low latency and high throughput, ideal for dynamic websites, content distribution, mobile apps, big data analytics

2. **S3 Intelligent-Tiering**
   - Use case: Data with unknown or changing access patterns
   - Features: Automatically moves objects between access tiers when access patterns change
   - Tiers: Frequent Access, Infrequent Access, Archive Instant Access, Archive Access, Deep Archive Access
   - No retrieval fees, small monthly monitoring and automation fee

3. **S3 Standard-IA (Infrequent Access)**
   - Use case: Long-lived but infrequently accessed data
   - Availability: 99.9%
   - Features: Lower storage cost than Standard, but has retrieval fee
   - Ideal for: Backups, disaster recovery, long-term storage

4. **S3 One Zone-IA**
   - Use case: Infrequently accessed data that doesn't require multi-AZ resilience
   - Availability: 99.5%
   - Features: 20% lower cost than Standard-IA, stored in a single AZ
   - Ideal for: Secondary backup copies, easily re-creatable data

5. **S3 Glacier Instant Retrieval**
   - Use case: Archive data that needs immediate access
   - Features: Millisecond retrieval, lowest cost storage for long-lived data accessed once per quarter
   - Minimum storage duration: 90 days

6. **S3 Glacier Flexible Retrieval (formerly Glacier)**
   - Use case: Archive data that doesn't need immediate access
   - Retrieval options: Expedited (1-5 minutes), Standard (3-5 hours), Bulk (5-12 hours)
   - Minimum storage duration: 90 days
   - Ideal for: Backup and archive data

7. **S3 Glacier Deep Archive**
   - Use case: Long-term retention and digital preservation
   - Retrieval time: Standard (12 hours), Bulk (48 hours)
   - Minimum storage duration: 180 days
   - Lowest cost storage option
   - Ideal for: Data accessed once or twice per year, regulatory compliance

8. **S3 Outposts**
   - Use case: Object storage on-premises
   - Features: S3 APIs on AWS Outposts for local data residency

### EC2 Pricing Options

Amazon EC2 provides flexible pricing options to optimize costs based on your workload needs.

1. **On-Demand Instances**
   - Payment: Pay per hour or per second (minimum 60 seconds) with no long-term commitments
   - Use case: Short-term, spiky, or unpredictable workloads that cannot be interrupted
   - Benefits: No upfront payment, no long-term commitment, complete control over instance lifecycle
   - Best for: Applications with short-term workloads, testing/development, first-time applications

2. **Reserved Instances (RI)**
   - Payment: Commitment for 1 or 3 years with significant discount (up to 72% compared to On-Demand)
   - Payment options: All Upfront, Partial Upfront, No Upfront
   - Types:
     - Standard Reserved Instances: Up to 72% discount, best for steady-state usage
     - Convertible Reserved Instances: Up to 54% discount, can change instance families, OS, tenancy
   - Use case: Steady-state or predictable usage applications
   - Best for: Production workloads with predictable usage patterns

3. **Savings Plans**
   - Payment: Commit to a consistent amount of usage ($/hour) for 1 or 3 years
   - Discount: Up to 72% compared to On-Demand
   - Types:
     - Compute Savings Plans: Most flexible, apply to EC2, Fargate, Lambda
     - EC2 Instance Savings Plans: Lower prices in exchange for commitment to specific instance family in a region
   - Benefits: Flexibility to change instance size, OS, tenancy, region (Compute Savings Plans)

4. **Spot Instances**
   - Payment: Request unused EC2 capacity at up to 90% discount compared to On-Demand
   - Availability: Can be interrupted by AWS with 2-minute warning when capacity is needed
   - Use case: Fault-tolerant, flexible, stateless workloads
   - Best for: Batch processing, big data analysis, CI/CD, web servers, high-performance computing, containerized workloads
   - Not suitable for: Databases, critical jobs, persistent workloads

5. **Dedicated Hosts**
   - Payment: Physical EC2 server dedicated for your use
   - Pricing: On-Demand (hourly) or Reserved (1 or 3 years)
   - Use case: Regulatory requirements, server-bound software licenses (per-socket, per-core, per-VM)
   - Benefits: Visibility and control over instance placement, socket and core visibility
   - Most expensive option

6. **Dedicated Instances**
   - Payment: Instances running on hardware dedicated to a single customer
   - Difference from Dedicated Hosts: Less visibility and control over instance placement
   - Use case: Compliance requirements requiring dedicated hardware
   - Additional fee: Per-region fee plus per-instance fee

### AWS Support Plans

AWS offers different support levels to meet various business needs.

1. **Basic Support**
   - Cost: Free for all AWS customers
   - Features:
     - Customer Service & Communities: 24/7 access via email
     - AWS Trusted Advisor: Access to 7 core Trusted Advisor checks
     - AWS Personal Health Dashboard: Personalized view of service health
   - Documentation, whitepapers, and support forums
   - No technical support cases

2. **Developer Support**
   - Cost: Greater of $29/month or 3% of monthly AWS usage
   - All Basic Support features, plus:
     - Technical support: Business hours access via email
     - Response times:
       - General guidance: < 24 business hours
       - System impaired: < 12 business hours
     - Unlimited support cases, 1 primary contact
   - Best for: Experimenting or testing in AWS

3. **Business Support**
   - Cost: Greater of $100/month or tiered pricing based on usage (10% for first $0-$10K, 7% for $10K-$80K, 5% for $80K-$250K, 3% for $250K+)
   - All Developer Support features, plus:
     - Technical support: 24/7 access via email, chat, and phone
     - Response times:
       - General guidance: < 24 hours
       - System impaired: < 12 hours
       - Production system impaired: < 4 hours
       - Production system down: < 1 hour
     - AWS Trusted Advisor: Full set of checks
     - Infrastructure Event Management: Additional fee
     - Unlimited contacts, unlimited cases
   - Best for: Production workloads in AWS

4. **Enterprise On-Ramp Support**
   - Cost: Greater of $5,500/month or tiered pricing (10% for $0-$150K, 7% for $150K-$500K, 5% for $500K-$1M, 3% for $1M+)
   - All Business Support features, plus:
     - Response times:
       - Business-critical system down: < 30 minutes
     - Access to a pool of Technical Account Managers (TAMs)
     - Consultative review and guidance
     - Infrastructure Event Management: Included (one per year)
     - Cost Optimization Workshop: Included (one per year)
     - Access to AWS Incident Detection and Response
   - Best for: Production and business-critical workloads

5. **Enterprise Support**
   - Cost: Greater of $15,000/month or tiered pricing (10% for $0-$150K, 7% for $150K-$500K, 5% for $500K-$1M, 3% for $1M+)
   - All Enterprise On-Ramp features, plus:
     - Response times:
       - Business-critical system down: < 15 minutes
     - Dedicated Technical Account Manager (TAM)
     - Concierge Support Team (billing and account best practices)
     - Infrastructure Event Management: Included and proactive
     - Operations Reviews and tools
     - Training and Game Days
     - Well-Architected Reviews
   - Best for: Mission-critical workloads in AWS

**Key Differences Summary:**
- **Response Time**: Decreases from Developer → Business → Enterprise On-Ramp → Enterprise
- **Communication Channels**: Email only (Developer) → Email/Chat/Phone (Business+)
- **Trusted Advisor**: 7 checks (Basic/Developer) → Full checks (Business+)
- **TAM Access**: None → Pool (Enterprise On-Ramp) → Dedicated (Enterprise)
- **Cost**: Increases with features and SLA requirements
