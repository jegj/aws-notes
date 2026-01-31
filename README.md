# aws-notes

Notes about the AWS certifications

## Table of Contents

- [General Concepts](#aws-cloud-practitioner---general-concepts)
  - [AWS Shared Responsibility Model](#aws-shared-responsibility-model)
  - [AWS Well-Architected Framework](#aws-well-architected-framework)
  - [AWS Support Plans](#aws-support-plans)
- [AWS Services](#aws-cloud-practitioner---aws-services)
  - [Amazon S3 Storage Classes](#amazon-s3-storage-classes)
  - [EC2 Pricing Options](#ec2-pricing-options)
  - [AWS Database Services](#aws-database-services)
  - [AWS Machine Learning and AI Services](#aws-machine-learning-and-ai-services)
  - [AWS Hybrid and On-Premises Connectivity Services](#aws-hybrid-and-on-premises-connectivity-services)
  - [AWS Network Security: Security Groups and NACLs](#aws-network-security-security-groups-and-nacls)
  - [AWS Storage Services](#aws-storage-services)
  - [AWS Snow Family](#aws-snow-family)
  - [AWS ETL and Data Integration Services](#aws-etl-and-data-integration-services)
- [References](#references)

## AWS Cloud Practitioner - General Concepts

### AWS Shared Responsibility Model

The AWS Shared Responsibility Model defines the division
of security and compliance responsibilities between AWS
and the customer. Understanding this model is fundamental
to operating securely in the cloud.

**Core Principle:**

- **AWS is responsible for security "OF" the cloud** -
  the infrastructure that runs all the services offered
  in the AWS Cloud
- **The customer is responsible for security "IN" the
  cloud** - the configuration and management of the
  services they use

**AWS Responsibilities (Security of the Cloud):**

- Physical security of data centers (facilities,
  environmental controls, physical access)
- Hardware and infrastructure (servers, storage,
  networking equipment)
- Software infrastructure (host operating system,
  virtualization layer, service software)
- Network infrastructure (routers, switches, load
  balancers, firewalls)
- Global infrastructure (Regions, Availability Zones,
  Edge Locations)
- Managed services underlying infrastructure (patching,
  maintenance of managed services like RDS, Lambda,
  DynamoDB)

**Customer Responsibilities (Security in the Cloud):**

- Customer data (encryption, integrity, backups)
- Identity and access management (IAM users, roles,
  policies, MFA)
- Operating system and network configuration (EC2
  instance OS patching, firewall/security group rules)
- Application-level security (application code,
  encryption in transit)
- Client-side data encryption and data integrity
  authentication
- Server-side encryption (file system and/or data
  encryption)
- Network traffic protection (VPC configuration, NACLs,
  security groups, SSL/TLS)

**Responsibility Varies by Service Type:**

1. **Infrastructure Services (e.g., EC2)**
   - Customer manages: OS, applications, security groups,
     firewall rules, network configuration, account
     management
   - AWS manages: Hardware, global infrastructure,
     compute, storage, networking foundation
   - Most customer responsibility

2. **Container Services (e.g., RDS, ECS)**
   - Customer manages: Firewall rules, IAM, customer
     data, encryption
   - AWS manages: OS, platform, hardware, networking,
     patching
   - Shared responsibility

3. **Abstracted Services (e.g., S3, DynamoDB, Lambda)**
   - Customer manages: Client-side encryption, IAM,
     bucket/table policies, data classification
   - AWS manages: Server-side encryption, platform,
     OS, networking, infrastructure
   - Least customer responsibility

**Key Examples for the Exam:**

- **Patching**: AWS patches managed services (RDS engine,
  Lambda runtime); customer patches EC2 instances and
  guest OS
- **Encryption**: AWS provides encryption tools and
  features; customer is responsible for enabling and
  managing encryption
- **Physical security**: Always AWS responsibility
- **IAM configuration**: Always customer responsibility
- **Security groups and NACLs**: Customer responsibility
- **Data center access controls**: AWS responsibility
- **AWS Global Infrastructure protection**: AWS
  responsibility
- **Customer data classification and handling**: Customer
  responsibility

### AWS Well-Architected Framework

The AWS Well-Architected Framework helps you understand the
pros and cons of decisions you make while building systems on
AWS. It provides a consistent approach for customers and
partners to evaluate architectures and implement scalable
designs.

**The Six Pillars:**

1. **Operational Excellence**
   - Focus: Running and monitoring systems to deliver
     business value
   - Key practices: Automating changes, responding to
     events, defining standards to manage daily operations
   - Design principles: Perform operations as code, make
     frequent small reversible changes, refine operations
     procedures frequently, anticipate failure, learn from
     operational failures

2. **Security**
   - Focus: Protecting information, systems, and assets
   - Key practices: Strong identity foundation,
     traceability, security at all layers, automation,
     data protection
   - Design principles: Implement a strong identity
     foundation, enable traceability, apply security at
     all layers, automate security best practices, protect
     data in transit and at rest, keep people away from
     data, prepare for security events

3. **Reliability**
   - Focus: Ensuring a workload performs its intended
     function correctly and consistently
   - Key practices: Automatic recovery, testing recovery
     procedures, scaling horizontally, managing change
   - Design principles: Automatically recover from failure,
     test recovery procedures, scale horizontally, stop
     guessing capacity, manage change through automation

4. **Performance Efficiency**
   - Focus: Using computing resources efficiently to meet
     system requirements
   - Key practices: Democratizing advanced technologies,
     going global in minutes, using serverless architectures
   - Design principles: Democratize advanced technologies,
     go global in minutes, use serverless architectures,
     experiment more often, consider mechanical sympathy

5. **Cost Optimization**
   - Focus: Avoiding unnecessary costs
   - Key practices: Adopting a consumption model, measuring
     overall efficiency, analyzing expenditure
   - Design principles: Implement cloud financial
     management, adopt a consumption model, measure overall
     efficiency, stop spending money on undifferentiated
     heavy lifting, analyze and attribute expenditure

6. **Sustainability**
   - Focus: Minimizing environmental impacts of running
     cloud workloads
   - Key practices: Understanding your impact, establishing
     sustainability goals, maximizing utilization
   - Design principles: Understand your impact, establish
     sustainability goals, maximize utilization, anticipate
     and adopt new more efficient hardware and software,
     use managed services, reduce downstream impact

### AWS Support Plans

AWS offers different support levels to meet various
business needs.

1. **Basic Support**
   - Cost: Free for all AWS customers
   - Features:
     - Customer Service & Communities: 24/7 access via
       email
     - AWS Trusted Advisor: Access to 7 core Trusted
       Advisor checks
     - AWS Personal Health Dashboard: Personalized view
       of service health
   - Documentation, whitepapers, and support forums
   - No technical support cases

2. **Developer Support**
   - Cost: Greater of $29/month or 3% of monthly AWS
     usage
   - All Basic Support features, plus:
     - Technical support: Business hours access via email
     - Response times:
       - General guidance: < 24 business hours
       - System impaired: < 12 business hours
     - Unlimited support cases, 1 primary contact
   - Best for: Experimenting or testing in AWS

3. **Business Support**
   - Cost: Greater of $100/month or tiered pricing based
     on usage (10% for first $0-$10K, 7% for $10K-$80K,
     5% for $80K-$250K, 3% for $250K+)
   - All Developer Support features, plus:
     - Technical support: 24/7 access via email, chat,
       and phone
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
   - Cost: Greater of $5,500/month or tiered pricing
     (10% for $0-$150K, 7% for $150K-$500K,
     5% for $500K-$1M, 3% for $1M+)
   - All Business Support features, plus:
     - Response times:
       - Business-critical system down: < 30 minutes
     - Access to a pool of Technical Account Managers
       (TAMs)
     - Consultative review and guidance
     - Infrastructure Event Management: Included
       (one per year)
     - Cost Optimization Workshop: Included
       (one per year)
     - Access to AWS Incident Detection and Response
   - Best for: Production and business-critical workloads

5. **Enterprise Support**
   - Cost: Greater of $15,000/month or tiered pricing
     (10% for $0-$150K, 7% for $150K-$500K,
     5% for $500K-$1M, 3% for $1M+)
   - All Enterprise On-Ramp features, plus:
     - Response times:
       - Business-critical system down: < 15 minutes
     - Dedicated Technical Account Manager (TAM)
     - Concierge Support Team (billing and account
       best practices)
     - Infrastructure Event Management: Included and
       proactive
     - Operations Reviews and tools
     - Training and Game Days
     - Well-Architected Reviews
   - Best for: Mission-critical workloads in AWS

**Key Differences Summary:**

- **Response Time**: Decreases from Developer to Business
  to Enterprise On-Ramp to Enterprise
- **Communication Channels**: Email only (Developer) to
  Email/Chat/Phone (Business+)
- **Trusted Advisor**: 7 checks (Basic/Developer) to Full
  checks (Business+)
- **TAM Access**: None to Pool (Enterprise On-Ramp) to
  Dedicated (Enterprise)
- **Cost**: Increases with features and SLA requirements

## AWS Cloud Practitioner - AWS Services

### Amazon S3 Storage Classes

Amazon S3 offers different storage classes designed for
different use cases, balancing cost and performance.

1. **S3 Standard**
   - Use case: Frequently accessed data
   - Availability: 99.99%
   - Durability: 99.999999999% (11 9's)
   - Features: Low latency and high throughput, ideal for
     dynamic websites, content distribution, mobile apps,
     big data analytics

2. **S3 Intelligent-Tiering**
   - Use case: Data with unknown or changing access patterns
   - Features: Automatically moves objects between access
     tiers when access patterns change
   - Tiers: Frequent Access, Infrequent Access, Archive
     Instant Access, Archive Access, Deep Archive Access
   - No retrieval fees, small monthly monitoring and
     automation fee

3. **S3 Standard-IA (Infrequent Access)**
   - Use case: Long-lived but infrequently accessed data
   - Availability: 99.9%
   - Features: Lower storage cost than Standard, but has
     retrieval fee
   - Ideal for: Backups, disaster recovery, long-term
     storage

4. **S3 One Zone-IA**
   - Use case: Infrequently accessed data that doesn't
     require multi-AZ resilience
   - Availability: 99.5%
   - Features: 20% lower cost than Standard-IA, stored
     in a single AZ
   - Ideal for: Secondary backup copies, easily
     re-creatable data

5. **S3 Glacier Instant Retrieval**
   - Use case: Archive data that needs immediate access
   - Features: Millisecond retrieval, lowest cost storage
     for long-lived data accessed once per quarter
   - Minimum storage duration: 90 days

6. **S3 Glacier Flexible Retrieval (formerly Glacier)**
   - Use case: Archive data that doesn't need immediate
     access
   - Retrieval options: Expedited (1-5 minutes),
     Standard (3-5 hours), Bulk (5-12 hours)
   - Minimum storage duration: 90 days
   - Ideal for: Backup and archive data

7. **S3 Glacier Deep Archive**
   - Use case: Long-term retention and digital preservation
   - Retrieval time: Standard (12 hours), Bulk (48 hours)
   - Minimum storage duration: 180 days
   - Lowest cost storage option
   - Ideal for: Data accessed once or twice per year,
     regulatory compliance

8. **S3 Outposts**
   - Use case: Object storage on-premises
   - Features: S3 APIs on AWS Outposts for local data
     residency

### EC2 Pricing Options

Amazon EC2 provides flexible pricing options to optimize
costs based on your workload needs.

1. **On-Demand Instances**
   - Payment: Pay per hour or per second (minimum
     60 seconds) with no long-term commitments
   - Use case: Short-term, spiky, or unpredictable
     workloads that cannot be interrupted
   - Benefits: No upfront payment, no long-term commitment,
     complete control over instance lifecycle
   - Best for: Applications with short-term workloads,
     testing/development, first-time applications

2. **Reserved Instances (RI)**
   - Payment: Commitment for 1 or 3 years with significant
     discount (up to 72% compared to On-Demand)
   - Payment options: All Upfront, Partial Upfront,
     No Upfront
   - Types:
     - Standard Reserved Instances: Up to 72% discount,
       best for steady-state usage
     - Convertible Reserved Instances: Up to 54% discount,
       can change instance families, OS, tenancy
   - Use case: Steady-state or predictable usage
     applications
   - Best for: Production workloads with predictable
     usage patterns

3. **Savings Plans**
   - Payment: Commit to a consistent amount of usage
     ($/hour) for 1 or 3 years
   - Discount: Up to 72% compared to On-Demand
   - Types:
     - Compute Savings Plans: Most flexible, apply to
       EC2, Fargate, Lambda
     - EC2 Instance Savings Plans: Lower prices in
       exchange for commitment to specific instance
       family in a region
   - Benefits: Flexibility to change instance size, OS,
     tenancy, region (Compute Savings Plans)

4. **Spot Instances**
   - Payment: Request unused EC2 capacity at up to 90%
     discount compared to On-Demand
   - Availability: Can be interrupted by AWS with 2-minute
     warning when capacity is needed
   - Use case: Fault-tolerant, flexible, stateless
     workloads
   - Best for: Batch processing, big data analysis, CI/CD,
     web servers, high-performance computing, containerized
     workloads
   - Not suitable for: Databases, critical jobs, persistent
     workloads

5. **Dedicated Hosts**
   - Payment: Physical EC2 server dedicated for your use
   - Pricing: On-Demand (hourly) or Reserved (1 or
     3 years)
   - Use case: Regulatory requirements, server-bound
     software licenses (per-socket, per-core, per-VM)
   - Benefits: Visibility and control over instance
     placement, socket and core visibility
   - Most expensive option

6. **Dedicated Instances**
   - Payment: Instances running on hardware dedicated to
     a single customer
   - Difference from Dedicated Hosts: Less visibility and
     control over instance placement
   - Use case: Compliance requirements requiring dedicated
     hardware
   - Additional fee: Per-region fee plus per-instance fee

### AWS Database Services

AWS offers a variety of purpose-built database services
designed for different data models and use cases,
enabling customers to choose the right tool for each
workload.

**Relational Databases:**

1. **Amazon RDS (Relational Database Service)**
   - Managed relational database service that simplifies
     setup, operation, and scaling of databases in the
     cloud
   - Supported engines: MySQL, PostgreSQL, MariaDB,
     Oracle, Microsoft SQL Server
   - Features: Automated backups, software patching,
     Multi-AZ deployment for high availability, read
     replicas for read scaling, automated failover
   - Use case: Traditional applications, ERP, CRM,
     e-commerce platforms requiring structured data
     with complex queries

2. **Amazon Aurora**
   - MySQL and PostgreSQL-compatible relational database
     built for the cloud, combining the performance and
     availability of high-end commercial databases with
     the simplicity and cost-effectiveness of open-source
   - Performance: Up to 5x faster than standard MySQL and
     3x faster than standard PostgreSQL
   - Features: Auto-scaling storage (up to 128 TB),
     up to 15 read replicas, continuous backup to S3,
     Multi-AZ by default, Aurora Serverless for
     variable workloads, Global Database for cross-region
     replication
   - Availability: 99.99%
   - Use case: Enterprise applications, SaaS products,
     high-performance workloads requiring relational data

3. **Amazon Redshift**
   - Fully managed, petabyte-scale data warehouse service
     for analytics and reporting
   - Features: Columnar storage, massively parallel
     processing (MPP), Redshift Spectrum for querying
     data in S3, machine learning integration,
     concurrency scaling
   - Performance: Up to 10x faster than traditional data
     warehouses
   - Use case: Business intelligence, big data analytics,
     data lake analytics, log analysis

**Key-Value and Document Databases:**

1. **Amazon DynamoDB**
   - Fully managed NoSQL key-value and document database
     delivering single-digit millisecond performance at
     any scale
   - Features: Serverless (no infrastructure to manage),
     automatic scaling, built-in security, backup and
     restore, in-memory caching with DAX, global tables
     for multi-region replication, ACID transaction
     support
   - Capacity modes: On-demand (pay-per-request) or
     provisioned (predictable workloads)
   - Use case: Web and mobile applications, gaming,
     IoT, ad tech, session management, shopping carts

2. **Amazon DocumentDB (with MongoDB compatibility)**
   - Managed document database service that supports
     MongoDB workloads
   - Features: MongoDB-compatible API, auto-scaling
     storage up to 64 TB, up to 15 read replicas,
     continuous backup to S3, encryption at rest and
     in transit
   - Use case: Content management, catalogs, user
     profiles, applications currently using MongoDB

**In-Memory Databases:**

1. **Amazon ElastiCache**
   - Fully managed in-memory caching service for
     sub-millisecond data access
   - Supported engines:
     - Redis: Advanced data structures, persistence,
       replication, pub/sub messaging
     - Memcached: Simple caching, multi-threaded
       performance
   - Features: Automatic failover, backup and restore,
     Multi-AZ, scaling, encryption
   - Use case: Caching, session stores, real-time
     analytics, gaming leaderboards, message queues

2. **Amazon MemoryDB for Redis**
   - Redis-compatible, durable, in-memory database
     service for ultra-fast performance with data
     durability
   - Features: Microsecond read and single-digit
     millisecond write latency, Multi-AZ durability,
     data persistence, up to 100+ TB of storage
   - Use case: Applications needing both Redis speed
     and database durability (session stores,
     financial transactions)

**Graph Databases:**

1. **Amazon Neptune**
   - Fully managed graph database service for building
     and running applications that work with highly
     connected datasets
   - Supported models: Property Graph (Apache
     TinkerPop/Gremlin) and RDF (SPARQL)
   - Features: Up to 15 read replicas, point-in-time
     recovery, continuous backup to S3, encryption at
     rest, Multi-AZ high availability
   - Use case: Social networking, knowledge graphs,
     fraud detection, recommendation engines, network
     management

**Time Series Databases:**

1. **Amazon Timestream**
   - Fully managed, serverless time series database
     for collecting, storing, and analyzing time-ordered
     data
   - Features: Automatic data tiering (in-memory and
     magnetic storage), built-in time series analytics
     functions, up to 1000x faster and 1/10th the cost
     of relational databases for time series data
   - Use case: IoT applications, DevOps monitoring,
     application monitoring, industrial telemetry

**Ledger Databases:**

1. **Amazon QLDB (Quantum Ledger Database)**
   - Fully managed ledger database that provides a
     transparent, immutable, and cryptographically
     verifiable transaction log
   - Features: Immutable journal, SHA-256 cryptographic
     verification, serverless, document data model,
     PartiQL query language (SQL-compatible)
   - Use case: Financial transactions, supply chain
     tracking, registration systems, audit trails

**Database Migration:**

1. **AWS DMS (Database Migration Service)**
   - Service for migrating databases to AWS with minimal
     downtime
   - Features: Supports homogeneous (same engine) and
     heterogeneous (different engine) migrations,
     continuous data replication, Schema Conversion Tool
     (SCT) for heterogeneous migrations
   - Supported sources: Oracle, SQL Server, MySQL,
     PostgreSQL, MongoDB, and others
   - Use case: Database migration to AWS, cross-region
     replication, development and test database creation,
     database consolidation

**Key Concepts for the Cloud Practitioner Exam:**

- **Purpose-built databases**: AWS recommends choosing
  the right database for each workload rather than using
  one database for everything
- **Managed vs. unmanaged**: AWS managed database services
  (RDS, DynamoDB) handle patching, backups, and scaling;
  self-managed databases on EC2 require customer management
- **SQL vs. NoSQL**: RDS and Aurora for structured,
  relational data; DynamoDB for flexible, key-value or
  document data
- **Data warehousing**: Redshift is purpose-built for
  analytics, not transactional workloads
- **Caching**: ElastiCache sits in front of databases
  to reduce read load and improve response times
- **Migration**: DMS enables migrating existing databases
  to AWS with minimal downtime

### AWS Machine Learning and AI Services

AWS provides a broad set of machine learning and artificial
intelligence services that enable developers to add
intelligence to their applications without needing deep ML
expertise.

**AI/ML Platforms and Infrastructure:**

1. **Amazon SageMaker**
   - Fully managed service to build, train, and deploy
     machine learning models at scale
   - Features: Built-in algorithms, Jupyter notebooks,
     automatic model tuning, one-click deployment,
     SageMaker Studio IDE
   - Use case: Custom ML model development, training,
     and production deployment for data scientists and
     developers

2. **Amazon Bedrock**
   - Fully managed service to access and use foundation
     models (FMs) from leading AI companies through a
     single API
   - Providers: Anthropic (Claude), Meta (Llama), Amazon
     (Titan), Mistral AI, Cohere, Stability AI, and others
   - Features: Model customization with your data, Retrieval
     Augmented Generation (RAG), Agents for orchestrating
     multi-step tasks, Guardrails for responsible AI
   - Use case: Building generative AI applications such as
     chatbots, content generation, summarization, and
     code generation

3. **Amazon Q**
   - Generative AI-powered assistant for business and
     development use cases
   - Variants:
     - Amazon Q Business: Answers questions and generates
       content using enterprise data and knowledge
     - Amazon Q Developer: AI coding companion for IDE
       and CLI, helps write, debug, and transform code
   - Use case: Enterprise productivity, software development
     acceleration, business intelligence

**Language and Text Services:**

1. **Amazon Comprehend**
   - Natural language processing (NLP) service that uses
     machine learning to extract insights from text
   - Features: Sentiment analysis, entity recognition,
     key phrase extraction, language detection, topic
     modeling, PII detection
   - Use case: Analyzing customer feedback, content
     classification, document processing

2. **Amazon Translate**
   - Neural machine translation service for fast,
     high-quality language translation
   - Features: Supports 75+ languages, real-time and batch
     translation, custom terminology, automatic language
     detection
   - Use case: Multilingual applications, website
     localization, translating user-generated content

3. **Amazon Lex**
   - Service for building conversational interfaces
     (chatbots) using voice and text
   - Features: Automatic speech recognition (ASR), natural
     language understanding (NLU), multi-turn conversation
     support, integration with AWS Lambda
   - Powers the same technology as Amazon Alexa
   - Use case: Customer service chatbots, interactive
     voice response (IVR) systems, virtual assistants

4. **Amazon Polly**
   - Text-to-speech service that turns text into
     lifelike speech
   - Features: Dozens of lifelike voices across multiple
     languages, Neural Text-to-Speech (NTTS) for
     natural-sounding speech, Speech Marks for lip syncing
   - Use case: Accessibility features, voice-enabled
     applications, e-learning content, news readers

5. **Amazon Transcribe**
   - Automatic speech recognition (ASR) service that
     converts speech to text
   - Features: Real-time and batch transcription, custom
     vocabularies, automatic language identification,
     speaker diarization, PII redaction
   - Use case: Transcribing customer calls, generating
     subtitles, meeting notes, medical transcription
     (Amazon Transcribe Medical)

**Vision Services:**

1. **Amazon Rekognition**
   - Image and video analysis service powered by deep
     learning
   - Features: Object and scene detection, facial analysis,
     face comparison, celebrity recognition, text in image
     detection, content moderation, custom labels
   - Use case: Identity verification, content moderation,
     media analysis, public safety

2. **Amazon Textract**
   - Service that automatically extracts text, handwriting,
     and structured data from scanned documents
   - Features: OCR, form extraction (key-value pairs),
     table extraction, identity document analysis, expense
     analysis
   - Goes beyond simple OCR by understanding document
     structure and relationships
   - Use case: Document processing automation, invoice
     processing, ID verification, mortgage processing

**Search and Personalization:**

1. **Amazon Kendra**
   - Intelligent enterprise search service powered by
     machine learning
   - Features: Natural language queries, understands
     context and intent, connector support for popular
     data sources (S3, SharePoint, Salesforce, databases),
     incremental learning from user feedback
   - Use case: Enterprise knowledge search, IT help desk,
     research portals, customer self-service

2. **Amazon Personalize**
   - Machine learning service for building real-time
     personalized recommendation systems
   - Features: Personalized recommendations, user
     segmentation, personalized search results, no ML
     expertise required
   - Based on the same technology used by Amazon.com
   - Use case: Product recommendations, personalized
     content feeds, targeted marketing campaigns

**Forecasting and Analytics:**

1. **Amazon Forecast**
   - Fully managed service that uses machine learning to
     deliver highly accurate forecasts
   - Features: Automated ML pipeline, incorporates
     related data (weather, pricing, promotions), no ML
     experience needed
   - Use case: Demand forecasting, financial planning,
     resource planning, inventory management

2. **Amazon Fraud Detector**
   - Fully managed service to identify potentially
     fraudulent online activities
   - Features: Custom fraud detection models, rule-based
     predictions, real-time fraud detection, no ML
     experience required
   - Use case: Online payment fraud, fake account creation,
     loyalty program abuse, account takeover detection

**Developer and Specialty Services:**

1. **Amazon Augmented AI (A2I)**
   - Service for building human review workflows for ML
     predictions
   - Features: Built-in human review workflows for Amazon
     Rekognition and Textract, custom workflows for any
     ML model, workforce management
   - Use case: Content moderation review, document
     processing verification, sentiment analysis
     validation

2. **AWS DeepRacer**
   - Autonomous 1/18th scale race car for learning
     reinforcement learning
   - Features: 3D racing simulator, global racing league,
     hands-on reinforcement learning experience
   - Use case: Learning and experimenting with
     reinforcement learning concepts

3. **AWS DeepComposer**
   - Machine learning-enabled musical keyboard for
     learning generative AI
   - Features: Compose music using generative AI models,
     learn about GANs (Generative Adversarial Networks)
   - Use case: Educational tool for learning generative
     AI techniques

**Key Concepts for the Cloud Practitioner Exam:**

- **Managed AI Services** (Rekognition, Comprehend,
  Translate, Polly, Transcribe, Lex): Pre-trained
  models, no ML expertise needed, pay-per-use
- **ML Platforms** (SageMaker): For custom model
  building, training, and deployment when pre-built
  services don't meet needs
- **Generative AI** (Bedrock, Amazon Q): Access to
  foundation models for building generative AI
  applications
- Most AWS ML/AI services are fully managed, meaning AWS
  handles the infrastructure, scaling, and model updates
- Services follow the shared responsibility model:
  AWS manages the ML infrastructure, customers manage
  their data and access controls

### AWS Hybrid and On-Premises Connectivity Services

AWS provides services to securely connect on-premises
data centers, offices, and remote locations to the AWS
cloud, enabling hybrid architectures and data migration.

**Network Connectivity:**

1. **AWS Direct Connect**
   - Dedicated, private network connection from an
     on-premises data center to AWS
   - Features: Consistent network performance, reduced
     bandwidth costs, private connectivity bypassing the
     public internet, supports 1 Gbps, 10 Gbps, and
     100 Gbps dedicated connections, plus hosted
     connections from 50 Mbps to 10 Gbps
   - Use case: Large-scale data transfers, real-time
     data feeds, hybrid environments requiring low
     latency and high throughput
   - Note: Takes weeks to establish as physical
     infrastructure must be provisioned

2. **AWS Site-to-Site VPN**
   - Encrypted IPsec VPN connection between an
     on-premises network and an AWS VPC over the
     public internet
   - Features: Encrypted tunnel, supports static and
     dynamic routing (BGP), redundant tunnels for high
     availability, quick to set up compared to Direct
     Connect
   - Use case: Secure connectivity to AWS when Direct
     Connect is not needed or as a backup for Direct
     Connect, extending corporate networks to the cloud

3. **AWS Client VPN**
   - Managed VPN service that enables individual users
     to securely access AWS resources and on-premises
     networks from any location
   - Features: OpenVPN-based, scales automatically,
     integrates with AWS Directory Service and SAML-based
     identity providers, split-tunnel support
   - Use case: Remote workforce access to AWS and
     on-premises resources, secure remote administration

4. **AWS Transit Gateway**
   - Network transit hub that connects VPCs, on-premises
     networks, and other AWS accounts through a central
     gateway
   - Features: Hub-and-spoke model, simplifies network
     topology, supports thousands of VPC connections,
     inter-region peering, multicast support, route
     tables for network segmentation
   - Use case: Connecting multiple VPCs and on-premises
     networks at scale, centralizing network management,
     multi-account architectures

**Hybrid Infrastructure:**

1. **AWS Outposts**
   - Fully managed service that extends AWS
     infrastructure, services, APIs, and tools to
     on-premises facilities
   - Features: Same AWS hardware and software as in
     AWS Regions, supports EC2, EBS, S3, ECS, EKS,
     RDS, and other services locally, managed and
     maintained by AWS
   - Use case: Low-latency workloads that must remain
     on-premises, local data processing, data residency
     requirements, migration transition

2. **AWS Wavelength**
   - Embeds AWS compute and storage at the edge of
     5G networks within telecommunication providers'
     data centers
   - Features: Ultra-low latency access to AWS services
     from 5G devices, deployed within carrier networks,
     supports EC2, EBS, and VPC
   - Use case: Real-time gaming, live video streaming,
     AR/VR, IoT at the edge, machine learning inference
     at the edge

3. **AWS Local Zones**
   - Extension of an AWS Region that places compute,
     storage, database, and other services closer to
     end users in specific geographic areas
   - Features: Single-digit millisecond latency to
     local users, extension of the parent Region's VPC,
     supports EC2, EBS, FSx, ELB, and other services
   - Use case: Media and entertainment content creation,
     real-time gaming, live streaming, latency-sensitive
     applications in specific metros

**Data Transfer and Migration:**

1. **AWS DataSync**
   - Online data transfer service for automating and
     accelerating data movement between on-premises
     storage and AWS
   - Features: Transfers up to 10x faster than
     open-source tools, automatic encryption and data
     integrity validation, supports NFS, SMB, HDFS,
     S3, EFS, and FSx, scheduling and filtering
   - Use case: Data migration, ongoing data replication,
     cold data archival, data processing workflows

2. **AWS Snow Family**
   - Physical devices for offline data transfer and
     edge computing when network transfer is impractical
   - See the dedicated **AWS Snow Family** section below
     for detailed information on each device

3. **AWS Transfer Family**
   - Fully managed service for file transfers to and
     from Amazon S3 or EFS using SFTP, FTPS, FTP, and
     AS2 protocols
   - Features: Managed infrastructure, integrates with
     existing authentication systems (AD, LDAP, custom),
     scales automatically, DNS routing with Route 53
   - Use case: Migrating file transfer workflows to AWS,
     B2B file exchanges, data distribution to partners

4. **AWS Storage Gateway**
   - Hybrid cloud storage service that connects
     on-premises environments to AWS cloud storage
   - Types:
     - S3 File Gateway: NFS/SMB access to S3 objects
     - FSx File Gateway: SMB access to Amazon FSx for
       Windows File Server
     - Volume Gateway: iSCSI block storage backed by S3
       snapshots (cached or stored volumes)
     - Tape Gateway: Virtual tape library backed by S3
       and Glacier for backup applications
   - Use case: Extending on-premises storage to the
     cloud, backup and archiving, disaster recovery,
     tiered storage

**Key Concepts for the Cloud Practitioner Exam:**

- **Direct Connect vs. VPN**: Direct Connect provides
  dedicated private connectivity with consistent
  performance; VPN is encrypted over the public internet
  and faster to set up
- **Snow Family**: Used when network transfer would take
  too long or bandwidth is limited; rule of thumb is
  consider Snow devices when transfer would take more
  than a week over the network
- **Hybrid strategies**: Outposts for running AWS
  services on-premises, Storage Gateway for bridging
  on-premises storage with AWS, Local Zones for
  latency-sensitive applications near users
- **Data migration path**: DataSync for online
  transfers, Snow Family for offline transfers, DMS
  for database migrations, Transfer Family for
  protocol-based file transfers

### AWS Network Security: Security Groups and NACLs

AWS provides two layers of network security for
controlling traffic to and from resources within a VPC:
Security Groups and Network Access Control Lists (NACLs).

**Security Groups (Stateful):**

- Virtual firewall that controls inbound and outbound
  traffic at the **instance level** (ENI - Elastic
  Network Interface)
- **Stateful**: If inbound traffic is allowed, the
  return outbound traffic is automatically allowed
  regardless of outbound rules (and vice versa)
- Rules:
  - Support **allow rules only** (no deny rules)
  - Evaluated as a whole: all rules are checked before
    deciding whether to allow traffic
  - Default inbound: All traffic denied
  - Default outbound: All traffic allowed
- Can reference other security groups as sources or
  destinations (e.g., allow traffic from instances in
  security group A)
- Applied at the instance level; an instance can have
  multiple security groups
- Changes take effect immediately

**Network Access Control Lists - NACLs (Stateless):**

- Optional layer of security that controls inbound and
  outbound traffic at the **subnet level**
- **Stateless**: Return traffic must be explicitly
  allowed by rules regardless of inbound rules; both
  inbound and outbound rules are evaluated independently
- Rules:
  - Support both **allow and deny rules**
  - Evaluated in **order by rule number** (lowest first);
    first matching rule is applied and the rest are
    ignored
  - Default NACL: Allows all inbound and outbound traffic
  - Custom NACL: Denies all inbound and outbound traffic
    by default
- One NACL per subnet; one subnet can only be associated
  with one NACL at a time
- Includes an explicit deny rule (*) at the end that
  denies all traffic not matched by any numbered rule
- Useful for blocking specific IP addresses or ranges

**Security Groups vs. NACLs Comparison:**

| Feature | Security Groups | NACLs |
| --- | --- | --- |
| Level | Instance (ENI) | Subnet |
| State | Stateful | Stateless |
| Rules | Allow only | Allow and Deny |
| Evaluation | All rules evaluated | Rules in order by number |
| Default inbound | Deny all | Allow all (default NACL) |
| Default outbound | Allow all | Allow all (default NACL) |
| Return traffic | Automatic | Must be explicitly allowed |

**Key Concepts for the Cloud Practitioner Exam:**

- **Stateful vs. Stateless**: Security Groups are
  stateful (track connections, return traffic is
  automatic); NACLs are stateless (each request is
  evaluated independently, return traffic needs
  explicit rules)
- **Defense in depth**: Use both Security Groups and
  NACLs together for layered security within a VPC
- **Security Groups** are the first line of defense for
  most workloads; **NACLs** add subnet-level protection
  and are useful for explicitly denying specific traffic
- **Ephemeral ports**: Because NACLs are stateless,
  outbound rules must allow ephemeral ports
  (1024-65535) for return traffic from clients
- Security Groups are required for every instance;
  NACLs are optional but applied at the subnet level
  by default

### AWS Storage Services

AWS offers a range of storage services beyond S3,
designed for different workload types including block
storage, file storage, and backup solutions.

**Block Storage:**

1. **Amazon EBS (Elastic Block Store)**
   - Persistent block-level storage volumes for use
     with EC2 instances
   - Features: Snapshots for backup (stored in S3),
     encryption at rest and in transit, resizable
     volumes, Multi-Attach for io1/io2 volumes
   - Volume types:
     - General Purpose SSD (gp2/gp3): Balanced price
       and performance, up to 16,000 IOPS, suitable
       for most workloads
     - Provisioned IOPS SSD (io1/io2): High-performance,
       up to 64,000 IOPS, for I/O-intensive workloads
       like databases
     - Throughput Optimized HDD (st1): Low-cost HDD for
       frequently accessed, throughput-intensive
       workloads like big data and data warehouses
     - Cold HDD (sc1): Lowest cost HDD for infrequently
       accessed data
   - Availability: Replicated within a single
     Availability Zone
   - Use case: Boot volumes, databases, enterprise
     applications, throughput-intensive workloads

2. **EC2 Instance Store**
   - Temporary block-level storage physically attached
     to the host machine
   - Features: Very high I/O performance, included in
     the instance cost, ideal for temporary data
   - **Ephemeral**: Data is lost when the instance is
     stopped, terminated, or the underlying hardware
     fails
   - Use case: Buffers, caches, scratch data, temporary
     content, data that is replicated across a fleet

**File Storage:**

1. **Amazon EFS (Elastic File System)**
   - Fully managed, elastic, serverless NFS file system
     for Linux-based workloads
   - Features: Automatically scales up and down as files
     are added or removed, supports thousands of
     concurrent connections, Multi-AZ by default,
     encryption at rest and in transit
   - Storage classes:
     - Standard: Frequently accessed data
     - Infrequent Access (IA): Lower cost for
       infrequently accessed files
     - Archive: Lowest cost for rarely accessed data
   - Performance modes: General Purpose (latency
     sensitive) and Max I/O (high throughput)
   - Use case: Content management, web serving, home
     directories, shared file systems for containers
     and serverless, big data analytics

2. **Amazon FSx**
   - Fully managed file systems built on popular
     commercial and open-source technologies
   - Variants:
     - **Amazon FSx for Windows File Server**: Fully
       managed Windows-native file system with SMB
       protocol, Active Directory integration, and
       Windows NTFS; supports DFS namespaces and
       replication
     - **Amazon FSx for Lustre**: High-performance file
       system for compute-intensive workloads;
       integrates natively with S3; delivers hundreds
       of gigabytes per second of throughput and
       millions of IOPS
     - **Amazon FSx for NetApp ONTAP**: Fully managed
       shared storage with NetApp ONTAP features;
       supports NFS, SMB, and iSCSI protocols; data
       deduplication and compression
     - **Amazon FSx for OpenZFS**: Fully managed file
       storage on OpenZFS; supports NFS protocol;
       snapshots, cloning, and data compression
   - Use case: Windows-based applications (FSx for
     Windows), machine learning and HPC (FSx for
     Lustre), enterprise applications requiring
     multi-protocol access (FSx for ONTAP), Linux
     workloads requiring ZFS features (FSx for OpenZFS)

**Backup and Recovery:**

1. **AWS Backup**
   - Centralized, fully managed backup service for
     automating and managing backups across AWS services
   - Supported services: EC2, EBS, RDS, DynamoDB, EFS,
     FSx, S3, Aurora, DocumentDB, Neptune, Storage
     Gateway, and more
   - Features: Backup policies (backup plans), automated
     scheduling, retention management, cross-region and
     cross-account backup, compliance reporting,
     encryption, vault lock for immutable backups
   - Use case: Centralized backup management, compliance
     and audit requirements, disaster recovery,
     automated backup lifecycle

**Key Concepts for the Cloud Practitioner Exam:**

- **Block vs. File vs. Object storage**: EBS provides
  block storage (attached to one EC2 instance), EFS and
  FSx provide file storage (shared across instances), S3
  provides object storage (accessed via API)
- **EBS scope**: Volumes are tied to a single AZ; use
  snapshots to move data across AZs or Regions
- **Instance Store vs. EBS**: Instance Store is
  ephemeral (data lost on stop/terminate); EBS is
  persistent (data survives instance stop)
- **EFS vs. FSx**: EFS is for Linux (NFS); FSx for
  Windows File Server is for Windows (SMB); FSx for
  Lustre is for high-performance computing
- **AWS Backup**: Single service to manage backups
  across multiple AWS services with policy-based
  automation

### AWS Snow Family

The AWS Snow Family is a collection of physical devices
designed for data transfer between on-premises data
centers and AWS, as well as edge computing in
environments with limited or no network connectivity.
These devices are useful when transferring data over
the network would be too slow, too expensive, or not
feasible.

**How It Works:**

1. Request a Snow device from the AWS Management Console
2. AWS ships the device to your location
3. Connect the device to your local network and transfer
   data using the AWS OpsHub GUI or CLI
4. Ship the device back to AWS
5. AWS uploads your data to S3 (or your chosen service)

**Devices:**

1. **AWS Snowcone**
   - Smallest and most portable member of the Snow Family
   - Specifications:
     - Snowcone: 8 TB HDD storage
     - Snowcone SSD: 14 TB SSD storage
     - Weight: 4.5 lbs (2.1 kg), 8 inches long
     - 2 vCPUs, 4 GB memory
   - Features: Rugged, portable, withstands harsh
     environments (dust, water, vibration), battery
     operated (optional), can run EC2 instances and
     AWS IoT Greengrass at the edge
   - Data transfer: Offline shipping to AWS or online
     transfer using AWS DataSync (pre-installed)
   - Use case: Edge computing and data collection in
     remote or austere environments (military, disaster
     response), IoT data collection, content
     distribution, healthcare data capture

2. **AWS Snowball Edge**
   - Mid-range device for large-scale data transfer and
     edge computing
   - Variants:
     - **Storage Optimized**: 80 TB usable HDD storage,
       1 TB SSD (for block volumes), 40 vCPUs, 80 GB
       memory; best for large-scale data migration and
       local storage workloads
     - **Compute Optimized**: 42 TB usable HDD storage,
       7.68 TB SSD, 52 vCPUs, 208 GB memory, optional
       NVIDIA V100 GPU; best for machine learning,
       video processing, and advanced edge computing
   - Features: Runs EC2 instances and Lambda functions
     locally, supports S3-compatible storage, can
     cluster up to 5-10 devices for increased storage
     and compute, encryption (256-bit), tamper-resistant
     and tamper-evident enclosure, AWS OpsHub management
   - Use case: Large data center migrations, disaster
     recovery, manufacturing and industrial IoT, media
     and entertainment content processing, military
     and tactical edge computing

3. **AWS Snowmobile**
   - Exabyte-scale data transfer service using a
     45-foot ruggedized shipping container pulled by
     a semi-trailer truck
   - Specifications:
     - Up to 100 PB per Snowmobile
     - Multiple layers of security: GPS tracking, alarm
       monitoring, 24/7 video surveillance, dedicated
       security personnel, optional escort security
       vehicle
   - Features: Connected directly to your data center
     network (high-speed), AWS personnel assist with
     setup, data encrypted with 256-bit keys managed
     through AWS KMS
   - Use case: Migrating entire data centers to AWS,
     moving massive datasets (video libraries, genomic
     data, seismic data), scenarios where even
     Snowball Edge would require too many devices

**Choosing the Right Snow Device:**

| Feature | Snowcone | Snowball Edge | Snowmobile |
| --- | --- | --- | --- |
| Storage | 8-14 TB | 42-80 TB | Up to 100 PB |
| Data scale | GBs to TBs | TBs to PBs | PBs to EBs |
| Edge compute | Basic | Advanced | None |
| Form factor | Handheld | Suitcase-sized | Shipping container |
| Use case | Remote/portable | Data center migration | Exabyte migration |

**Key Concepts for the Cloud Practitioner Exam:**

- **When to use Snow devices**: When data transfer over
  the network would take more than one week, when there
  is limited or no internet connectivity, or when
  network costs would be prohibitive
- **Data flow**: Data is always encrypted on the device
  before shipping; AWS never sees unencrypted data
  during transit
- **Snowcone vs. Snowball Edge**: Snowcone for small,
  portable, rugged use cases; Snowball Edge for larger
  migrations and more powerful edge computing
- **Snowmobile**: Only for extreme-scale migrations
  (typically 10 PB or more); for anything less, multiple
  Snowball Edge devices are more practical
- **Edge computing**: Both Snowcone and Snowball Edge
  can run EC2 instances and process data locally before
  shipping to AWS
- **AWS OpsHub**: Graphical user interface for managing
  Snow Family devices (transferring data, launching
  instances, monitoring devices)

### AWS ETL and Data Integration Services

AWS provides services for extracting, transforming, and
loading (ETL) data, enabling customers to prepare and
move data between data stores for analytics, reporting,
and machine learning.

1. **AWS Glue**
   - Fully managed, serverless ETL service for
     discovering, preparing, and combining data
   - Features:
     - Glue Data Catalog: Central metadata repository
       that stores table definitions, schemas, and
       partitions; acts as a persistent Hive-compatible
       metastore for Athena, Redshift Spectrum, and EMR
     - Glue Crawlers: Automatically scan data sources
       (S3, RDS, DynamoDB, JDBC), infer schemas, and
       populate the Data Catalog
     - Glue ETL Jobs: Auto-generated Python or Scala
       code for data transformations, runs on a managed
       Apache Spark environment
     - Glue Studio: Visual interface for creating,
       running, and monitoring ETL workflows without
       writing code
     - Glue DataBrew: Visual data preparation tool for
       cleaning and normalizing data using 250+
       built-in transformations without writing code
     - Job bookmarks to track processed data and avoid
       reprocessing
   - Use case: Building data lakes, preparing data for
     analytics, cataloging data across multiple sources,
     migrating data between databases

2. **Amazon EMR (Elastic MapReduce)**
   - Managed big data platform for processing vast
     amounts of data using open-source frameworks
   - Supported frameworks: Apache Spark, Apache Hive,
     Apache HBase, Apache Flink, Presto, Trino
   - Features: Auto-scaling, runs on EC2, EKS, or
     serverless (EMR Serverless), integrates with S3
     for storage, notebook support for interactive
     analysis
   - Use case: Large-scale data transformations, log
     analysis, clickstream analysis, genomics, financial
     analysis, ETL at petabyte scale

3. **Amazon Kinesis**
   - Platform for collecting, processing, and analyzing
     real-time streaming data
   - Services:
     - **Kinesis Data Streams**: Capture and store
       streaming data in real time for custom
       processing; data retained for 24 hours to
       365 days
     - **Kinesis Data Firehose**: Load streaming data
       into destinations (S3, Redshift, OpenSearch,
       Splunk) with optional transformation using
       Lambda; fully managed, near real-time delivery
     - **Kinesis Data Analytics**: Analyze streaming
       data in real time using SQL or Apache Flink;
       detect anomalies, generate alerts, create
       real-time dashboards
     - **Kinesis Video Streams**: Capture, process, and
       store video streams for analytics and machine
       learning
   - Use case: Real-time analytics, log and event
     processing, IoT data ingestion, clickstream
     capture, video analytics

4. **Amazon Athena**
   - Serverless, interactive query service for analyzing
     data directly in Amazon S3 using standard SQL
   - Features: No infrastructure to manage, pay per
     query (per TB scanned), supports CSV, JSON, Parquet,
     ORC, and Avro formats, integrates with AWS Glue
     Data Catalog for schema management, federated
     queries to other data sources
   - Use case: Ad-hoc querying of S3 data lakes, log
     analysis, business intelligence reporting, data
     exploration without loading into a database

5. **Amazon QuickSight**
   - Serverless, cloud-native business intelligence
     service for creating interactive dashboards and
     visualizations
   - Features: Machine learning-powered insights
     (anomaly detection, forecasting, narratives),
     SPICE in-memory engine for fast performance,
     embedded analytics for applications, pay-per-session
     pricing, integrates with RDS, Redshift, Athena, S3,
     and many other data sources
   - Use case: Business dashboards and reporting,
     embedded analytics in applications, data
     exploration and visualization

6. **AWS Data Pipeline**
   - Managed orchestration service for moving and
     transforming data between AWS services and
     on-premises data sources on a schedule
   - Features: Scheduled data movement, fault-tolerant
     execution, supports S3, RDS, DynamoDB, Redshift,
     and on-premises sources, precondition checks,
     retry logic
   - Use case: Periodic data movement between services,
     scheduled ETL jobs, importing/exporting data
     from DynamoDB
   - Note: AWS recommends AWS Glue or Step Functions
     for new ETL workloads

7. **AWS Step Functions**
   - Serverless orchestration service for coordinating
     multiple AWS services into workflows
   - Features: Visual workflow designer, state machines
     with branching and error handling, supports Lambda,
     ECS, Glue, EMR, Athena, and 200+ AWS services,
     Standard (long-running) and Express (high-volume)
     workflows
   - Use case: Orchestrating complex ETL pipelines,
     coordinating microservices, automating data
     processing workflows, ML model training pipelines

**Key Concepts for the Cloud Practitioner Exam:**

- **AWS Glue**: The primary serverless ETL service; know
  the Data Catalog (metadata), Crawlers (auto-discover
  schemas), and ETL Jobs (transform data)
- **Batch vs. streaming**: Glue and EMR for batch ETL;
  Kinesis for real-time streaming data
- **Serverless analytics stack**: Glue (ETL) + Athena
  (query) + S3 (storage) + QuickSight (visualization)
  form a fully serverless analytics pipeline
- **Kinesis vs. Glue**: Kinesis is for real-time data
  streams; Glue is for batch ETL and data cataloging
- **EMR**: For large-scale processing with open-source
  frameworks (Spark, Hive); more control but more
  management than Glue
- **Athena**: Query S3 directly with SQL without loading
  data into a database; pay only for data scanned

## References

- [AWS Cloud Practitioner - YouTube Playlist](https://www.youtube.com/playlist?list=PL7Jj8Ba9Yr6AlmnfXo_UwoLF_CG5SP_mH)
