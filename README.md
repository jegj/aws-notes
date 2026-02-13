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
  - [AWS Migration Services](#aws-migration-services)
  - [AWS Network Security: Security Groups and NACLs](#aws-network-security-security-groups-and-nacls)
  - [AWS IAM](#aws-iam-identity-and-access-management)
  - [AWS Security Services](#aws-security-services)
  - [AWS Encryption and Key Management](#aws-encryption-and-key-management)
  - [AWS Compliance and Audit Services](#aws-compliance-and-audit-services)
  - [AWS Storage Services](#aws-storage-services)
  - [AWS Snow Family](#aws-snow-family)
  - [AWS ETL and Data Integration Services](#aws-etl-and-data-integration-services)
  - [AWS Compute Services](#aws-compute-services)
  - [AWS Networking and Content Delivery](#aws-networking-and-content-delivery)
  - [AWS Application Integration Services](#aws-application-integration-services)
  - [AWS Management and Governance Services](#aws-management-and-governance-services)
  - [AWS Cloud Concepts and Value Proposition](#aws-cloud-concepts-and-value-proposition)
  - [AWS Global Infrastructure](#aws-global-infrastructure)
  - [AWS Cloud Adoption Framework](#aws-cloud-adoption-framework-aws-caf)
  - [AWS Billing, Pricing, and Cost Management](#aws-billing-pricing-and-cost-management)
  - [Tricky Exam Questions](#tricky-exam-questions)
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

### AWS Migration Services

AWS provides a suite of services to help plan, execute,
and track migrations of applications, servers, and
databases from on-premises or other cloud environments
to AWS.

**Migration Planning and Tracking:**

1. **AWS Migration Hub**
   - Central location for planning, tracking, and
     managing migrations to AWS across multiple tools
     and services
   - Features: Single dashboard to monitor migration
     progress from multiple AWS services (Application
     Migration Service, Database Migration Service) and
     partner tools; Migration Hub Orchestrator for
     pre-built migration templates (SAP, Microsoft SQL
     Server); grouping of servers and applications for
     coordinated migration tracking
   - Integrates with: AWS Application Discovery Service,
     AWS Application Migration Service (MGN), AWS DMS,
     and third-party migration tools
   - Use case: Coordinating enterprise-wide migrations,
     tracking migration status across multiple teams and
     tools, managing complex multi-application migrations

2. **AWS Application Discovery Service**
   - Service for discovering and collecting data about
     on-premises servers and applications to plan
     migrations
   - Features: Agentless discovery (via VMware vCenter)
     or agent-based discovery for detailed data;
     collects server hostnames, IP/MAC addresses,
     resource allocation, CPU/memory/disk utilization;
     maps server dependencies by analyzing network
     connections; data stored encrypted in AWS and
     available in Migration Hub
   - Use case: Building an inventory of on-premises
     infrastructure, identifying application
     dependencies before migration, assessing migration
     readiness

**Server Migration:**

1. **AWS Application Migration Service (MGN)**
   - Primary AWS service for lift-and-shift migrations
     of physical, virtual, or cloud servers to run
     natively on AWS
   - Features: Automated server replication to AWS,
     continuous data replication (minimizes cutover
     window to minutes), non-disruptive testing before
     cutover, supports Windows and Linux, converts
     servers to run on EC2 instances
   - Replaces: AWS Server Migration Service (SMS) and
     CloudEndure Migration
   - Use case: Migrating on-premises servers to EC2,
     rehosting applications without modification,
     disaster recovery with minimal downtime

**Key Concepts for the Cloud Practitioner Exam:**

- **Migration Hub** is for tracking and coordinating
  migrations; it does not perform the migration itself
- **Application Discovery Service** is for discovering
  on-premises infrastructure; **Migration Hub** is for
  tracking migration progress; **MGN** performs the
  actual lift-and-shift migration
- **MGN (Application Migration Service)** is the
  recommended service for lift-and-shift server
  migrations to AWS
- **DMS** is for database migrations; **MGN** is for
  server/application migrations
- Migration workflow: Discover (Application Discovery
  Service) → Plan (Migration Hub) → Migrate (MGN/DMS)
  → Track (Migration Hub)

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

### AWS IAM (Identity and Access Management)

AWS IAM enables you to securely manage access to AWS
services and resources. It is a global service (not
region-specific) and is central to AWS security.

**Core Components:**

1. **IAM Users**
   - Represents a person or application that interacts
     with AWS
   - Has permanent long-term credentials (username/
     password for console, access keys for CLI/API)
   - Best practice: Create individual IAM users instead
     of sharing credentials

2. **IAM Groups**
   - Collection of IAM users that share the same
     permissions
   - A user can belong to multiple groups (up to 10)
   - Groups cannot be nested (no groups within groups)
   - Use case: Assign permissions by job function
     (e.g., Developers, Admins, Auditors)

3. **IAM Roles**
   - Identity with permissions that can be assumed
     temporarily by users, applications, or AWS services
   - No permanent credentials; temporary security
     credentials are issued when a role is assumed
   - Common use cases:
     - EC2 instances accessing S3 or DynamoDB
     - Cross-account access
     - Federated users (external identity providers)
     - AWS services acting on your behalf (e.g., Lambda
       execution role)
   - Best practice: Use roles instead of sharing
     access keys

4. **IAM Policies**
   - JSON documents that define permissions (allow or
     deny actions on resources)
   - Types:
     - AWS Managed Policies: Pre-built by AWS for
       common use cases (e.g., AmazonS3ReadOnlyAccess)
     - Customer Managed Policies: Created and managed
       by you for custom requirements
     - Inline Policies: Embedded directly in a single
       user, group, or role (not reusable)
   - Policy evaluation: Explicit deny always overrides
     any allow; by default all actions are denied
     (implicit deny)

**Multi-Factor Authentication (MFA):**

- Adds a second layer of authentication beyond
  username and password
- Supported MFA devices:
  - Virtual MFA device (authenticator app like Google
    Authenticator or Authy)
  - Hardware TOTP token (physical key fob)
  - FIDO2 security key (e.g., YubiKey)
- Best practice: Enable MFA for all users, especially
  the root user

**IAM Identity Center (formerly AWS SSO):**

- Centralized access management for multiple AWS
  accounts and business applications
- Single sign-on (SSO) for workforce users
- Integrates with external identity providers (Active
  Directory, Okta, Azure AD) via SAML 2.0
- Assigns permissions across AWS accounts managed by
  AWS Organizations
- Use case: Managing access for employees across
  multiple AWS accounts from a single location

**Root User vs. IAM User:**

- **Root user**: Created when the AWS account is first
  set up; has complete, unrestricted access to all
  resources
  - Should only be used for account-level tasks that
    require root (e.g., changing account settings,
    closing the account, changing the support plan,
    restoring IAM permissions, enabling MFA on the
    S3 bucket delete policy)
  - Best practices: Enable MFA, do not create access
    keys, do not use for daily tasks
- **IAM user**: Created within the account with
  specific permissions; should be used for all
  day-to-day operations

**Key Security Principles:**

- **Least privilege**: Grant only the minimum
  permissions required to perform a task
- **Separation of duties**: Distribute tasks across
  multiple users to reduce risk
- **Regular credential rotation**: Periodically
  rotate access keys and passwords
- **Use groups for permissions**: Assign policies to
  groups rather than individual users for easier
  management

**Key Concepts for the Cloud Practitioner Exam:**

- IAM is free to use (no additional charge)
- IAM is global; users and roles are not tied to a
  specific Region
- Root user should be protected with MFA and not used
  for everyday tasks
- Roles are preferred over access keys for granting
  AWS services access to other services
- Policies follow an explicit deny > explicit allow >
  implicit deny evaluation order
- IAM Identity Center is the recommended way to
  manage workforce access across multiple accounts

### AWS Security Services

AWS provides a comprehensive set of security services
for protecting workloads, detecting threats, and
maintaining compliance.

**Firewall and DDoS Protection:**

1. **AWS WAF (Web Application Firewall)**
   - Protects web applications from common web
     exploits and bots at the application layer
     (Layer 7)
   - Features: Custom rules to filter traffic based
     on IP addresses, HTTP headers, body content, or
     URI strings; managed rule groups from AWS and
     AWS Marketplace; rate-based rules to block
     request floods; Bot Control for managing bot
     traffic
   - Deployed on: Amazon CloudFront, Application Load
     Balancer (ALB), Amazon API Gateway, AWS AppSync
   - Use case: Protecting against SQL injection, XSS,
     blocking specific countries or IP ranges, rate
     limiting

2. **AWS Shield**
   - Managed DDoS (Distributed Denial of Service)
     protection service
   - **Shield Standard**:
     - Automatically enabled for all AWS customers at
       no additional cost
     - Protects against most common network and
       transport layer (Layer 3/4) DDoS attacks
     - Always-on detection and automatic inline
       mitigation
   - **Shield Advanced**:
     - Cost: $3,000/month plus data transfer fees
     - Enhanced protection for EC2, ELB, CloudFront,
       Global Accelerator, and Route 53
     - 24/7 access to the AWS DDoS Response Team (DRT)
     - Cost protection (DDoS scaling charges refunded)
     - Real-time visibility into attacks
     - Integration with AWS WAF (WAF included at no
       extra cost)
   - Use case: Protecting internet-facing applications
     from DDoS attacks

3. **AWS Firewall Manager**
   - Central management service for configuring and
     managing firewall rules across multiple accounts
     and resources in AWS Organizations
   - Manages: AWS WAF rules, AWS Shield Advanced
     protections, VPC security groups, AWS Network
     Firewall rules, and Route 53 Resolver DNS
     Firewall rules
   - Features: Automatic enforcement of security
     policies on new resources as they are created,
     centralized compliance reporting, delegated
     administrator support
   - Use case: Enforcing consistent WAF rules across
     all ALBs and CloudFront distributions in an
     organization, ensuring all accounts have Shield
     Advanced enabled

4. **AWS Network Firewall**
   - Managed network firewall service for filtering
     traffic at the VPC level (Layer 3 to Layer 7)
   - Features: Stateful and stateless packet
     inspection, intrusion prevention system (IPS),
     web filtering, custom rule groups using Suricata-
     compatible rules, integration with AWS Firewall
     Manager for centralized management
   - Deployed in a dedicated firewall subnet within
     your VPC
   - Use case: Filtering inbound and outbound VPC
     traffic, blocking known malicious domains,
     deep packet inspection for network threats

**Threat Detection and Investigation:**

1. **Amazon GuardDuty**
   - Intelligent threat detection service that
     continuously monitors AWS accounts and workloads
     for malicious activity using AI, machine learning,
     anomaly detection, and integrated threat
     intelligence
   - One-click deployment; no additional software or
     infrastructure to manage
   - **Foundational Data Sources** (always active):
     AWS CloudTrail management events, VPC Flow Logs,
     and DNS logs
   - **Protection Plans** (optional, individually
     enabled):
     - **S3 Protection**: Monitors CloudTrail S3 data
       events for suspicious bucket activity
     - **EKS Protection**: Monitors EKS cluster control
       plane via Kubernetes audit logs
     - **Runtime Monitoring**: OS-level visibility for
       EC2, ECS, and EKS using a lightweight agent
     - **Lambda Protection**: Monitors VPC Flow Logs
       from Lambda functions for threats
     - **RDS Protection**: ML-based detection of brute
       force attacks and suspicious logins on Aurora
     - **Malware Protection**: Scans EBS volumes (EC2),
       S3 objects, and AWS Backup resources for malware
   - **Extended Threat Detection**: Automatically
     correlates signals across network, runtime, and
     API activity to detect multi-stage attacks
     (enabled at no additional cost)
   - **Finding Severity Levels**: Critical (active
     attack sequence), High (resource actively
     compromised), Medium (suspicious deviation),
     Low (attempted activity, no compromise)
   - Integration: EventBridge for automated response,
     Security Hub for aggregation, Detective for
     investigation
   - Multi-account support via AWS Organizations
   - Pricing: Based on volume of events and logs
     analyzed; 30-day free trial
   - Use case: Detecting unauthorized access,
     compromised instances, cryptocurrency mining,
     data exfiltration, malware on workloads,
     multi-stage attack sequences

2. **Amazon Inspector**
   - Automated vulnerability management service that
     continuously scans workloads for software
     vulnerabilities and network exposure
   - Scans: EC2 instances, container images in ECR,
     Lambda functions
   - Features: Automated and continuous scanning,
     risk scoring using CVSS, integration with
     Security Hub, findings prioritized by severity
   - Use case: Vulnerability assessment, compliance
     scanning, identifying unpatched software

3. **Amazon Detective**
   - Security investigation service that helps
     analyze, investigate, and identify the root
     cause of security findings
   - Features: Automatically collects and processes
     data from GuardDuty, CloudTrail, and VPC Flow
     Logs; graph-based visualizations of resource
     behavior and interactions; ML-based analysis
   - Use case: Investigating security alerts from
     GuardDuty, triaging findings, understanding
     the scope of security incidents

**Data Protection and Discovery:**

1. **Amazon Macie**
   - Data security service that uses machine learning
     to discover, classify, and protect sensitive data
     stored in Amazon S3
   - Features: Automatically discovers personally
     identifiable information (PII), financial data,
     credentials, and other sensitive data; custom
     data identifiers; automated alerts; integration
     with Security Hub and EventBridge
   - Use case: Data privacy compliance (GDPR, HIPAA),
     discovering sensitive data in S3 buckets,
     monitoring data security posture

2. **AWS Secrets Manager**
   - Service for managing, retrieving, and rotating
     secrets such as database credentials, API keys,
     and tokens throughout their lifecycle
   - Features: Automatic rotation of secrets (built-in
     support for RDS, Redshift, DocumentDB),
     fine-grained IAM policies to control access,
     encryption using KMS, cross-account access,
     versioning of secrets
   - Difference from Systems Manager Parameter Store:
     Secrets Manager has built-in automatic rotation
     and is designed specifically for secrets;
     Parameter Store is a general-purpose configuration
     store with a free tier for standard parameters
   - Use case: Storing and rotating database
     credentials, managing API keys for third-party
     services, eliminating hardcoded secrets in
     application code

**Centralized Security Management:**

1. **AWS Security Hub**
   - Centralized security service that aggregates,
     organizes, and prioritizes security findings
     from multiple AWS services and partner products
   - Integrates with: GuardDuty, Inspector, Macie,
     Firewall Manager, IAM Access Analyzer, Systems
     Manager, and third-party tools
   - Features: Automated compliance checks against
     standards (CIS AWS Foundations, PCI DSS, AWS
     Foundational Security Best Practices), security
     score, automated remediation workflows
   - Use case: Centralized security view across
     multiple accounts, compliance monitoring,
     prioritizing security issues

2. **IAM Access Analyzer**
   - Service that identifies resources in your account
     that are shared with external entities (other
     accounts, organizations, or public access)
   - Analyzes: S3 buckets, IAM roles, KMS keys,
     Lambda functions, SQS queues, and Secrets Manager
     secrets
   - Features: Continuously monitors for new or
     updated resource policies, generates findings
     for unintended access, policy validation to
     check policies against best practices before
     deployment
   - Use case: Identifying S3 buckets or IAM roles
     accidentally shared publicly, validating
     resource policies comply with security standards

3. **Amazon Security Lake**
   - Centralized security data lake that automatically
     collects, normalizes, and stores security data
     from AWS services, third-party sources, and
     custom sources
   - Uses the Open Cybersecurity Schema Framework
     (OCSF) for data normalization
   - Sources: CloudTrail, VPC Flow Logs, Route 53
     resolver logs, Security Hub findings, S3 data
     events, Lambda execution logs, EKS audit logs
   - Features: Automatic data collection and
     normalization, subscriber management for
     analytics tools, multi-account and multi-Region
     support, data stored in S3 with Apache Parquet
     format
   - Use case: Centralizing security data for SIEM
     tools and analytics, long-term security data
     retention, cross-source security investigation

**Key Concepts for the Cloud Practitioner Exam:**

- **Shield Standard** is free and automatic; **Shield
  Advanced** is paid and provides enhanced DDoS
  protection with response team access
- **WAF** operates at Layer 7 (application); **Shield**
  operates at Layer 3/4 (network/transport);
  **Network Firewall** operates at Layer 3-7 for VPC
  traffic
- **Firewall Manager** centrally manages WAF, Shield,
  Network Firewall, and security group rules across
  accounts in AWS Organizations
- **GuardDuty** is for threat detection (what is
  happening); **Inspector** is for vulnerability
  scanning (what could be exploited); **Detective**
  is for investigation (understanding what happened)
- **GuardDuty** foundational detection is always on;
  protection plans (S3, EKS, Runtime Monitoring,
  Lambda, RDS, Malware Protection) are optional
- **GuardDuty** finding severity: Critical > High >
  Medium > Low
- **Macie** is specifically for discovering sensitive
  data in S3
- **Secrets Manager** is for secrets with automatic
  rotation; **Parameter Store** is for general
  configuration data
- **Security Hub** is the single pane of glass for
  security findings across multiple services
- **IAM Access Analyzer** identifies resources
  unintentionally shared with external entities
- **Security Lake** centralizes security data using
  the OCSF standard for analytics and investigation

### AWS Encryption and Key Management

AWS provides services for managing encryption keys
and certificates to protect data at rest and in
transit.

**Key Management:**

1. **AWS KMS (Key Management Service)**
   - Managed service for creating, managing, and
     controlling cryptographic keys used to encrypt
     data
   - Features: Centralized key management, automatic
     key rotation (yearly), integration with most
     AWS services (S3, EBS, RDS, Redshift, Lambda,
     etc.), audit key usage via CloudTrail
   - Key types:
     - AWS managed keys: Created and managed by AWS
       on your behalf (automatic, free for supported
       services)
     - Customer managed keys (CMK): Created and
       managed by you with full control over key
       policies, rotation, and lifecycle
     - AWS owned keys: Used by AWS services internally;
       not visible in your account
   - Use case: Encrypting data across AWS services,
     meeting compliance requirements for key
     management, envelope encryption

2. **AWS CloudHSM**
   - Dedicated hardware security module (HSM) in the
     AWS Cloud for generating and managing your own
     encryption keys
   - Features: Single-tenant HSM hardware, FIPS
     140-2 Level 3 validated, you control the keys
     (AWS has no access), supports symmetric and
     asymmetric keys, runs in your VPC
   - Difference from KMS: CloudHSM gives you exclusive
     access to dedicated hardware; KMS is multi-tenant
     and fully managed
   - Use case: Regulatory compliance requiring
     dedicated HSMs, SSL/TLS offloading, certificate
     authority, Oracle TDE

3. **AWS Certificate Manager (ACM)**
   - Service for provisioning, managing, and deploying
     public and private SSL/TLS certificates
   - Features: Free public certificates, automatic
     renewal, integration with ELB, CloudFront, API
     Gateway, and other AWS services
   - Use case: Enabling HTTPS on websites and
     applications, securing API endpoints, encrypting
     data in transit

**Encryption Concepts:**

- **Encryption at rest**: Protects data stored on
  disk (e.g., EBS volumes, S3 objects, RDS databases)
  - Typically uses AES-256 encryption
  - Most AWS services support encryption at rest via
    KMS integration
- **Encryption in transit**: Protects data as it moves
  between systems (e.g., HTTPS/TLS, VPN connections)
  - SSL/TLS certificates (managed via ACM)
  - AWS services encrypt data in transit by default
    between AWS endpoints
- **Client-side encryption**: Customer encrypts data
  before sending it to AWS
- **Server-side encryption**: AWS encrypts data after
  receiving it (e.g., SSE-S3, SSE-KMS, SSE-C)

**Key Concepts for the Cloud Practitioner Exam:**

- KMS is the primary encryption service for most AWS
  workloads; it integrates with most AWS services
- CloudHSM is for customers who need dedicated
  hardware and full key control
- ACM provides free SSL/TLS certificates for AWS
  services; you cannot export them for external use
- Know the difference between encryption at rest and
  encryption in transit
- AWS manages the encryption infrastructure;
  customers choose whether and how to encrypt

### AWS Compliance and Audit Services

AWS provides services for compliance reporting,
auditing, resource configuration tracking, and
user authentication.

**Compliance:**

1. **AWS Artifact**
   - Self-service portal for accessing AWS compliance
     reports and agreements
   - Features:
     - Artifact Reports: Access to AWS security and
       compliance documents (SOC reports, PCI DSS,
       ISO certifications, HIPAA, and others)
     - Artifact Agreements: Review, accept, and manage
       agreements with AWS (e.g., BAA for HIPAA,
       GDPR DPA)
   - Use case: Providing compliance evidence to
     auditors, reviewing AWS security controls,
     managing compliance agreements

**Auditing and Configuration:**

1. **AWS CloudTrail**
   - Service that records API calls and actions made
     in your AWS account, providing a complete audit
     trail
   - Features: Logs all API calls (who, what, when,
     from where), enabled by default (90-day event
     history), management events and data events,
     multi-region and organization trails, CloudTrail
     Lake for SQL-based analysis, integration with
     CloudWatch Logs and S3
   - Event types:
     - Management events: Operations on AWS resources
       (e.g., creating an EC2 instance, modifying a
       security group)
     - Data events: Operations on data within
       resources (e.g., S3 object-level operations,
       Lambda function invocations)
     - Insight events: Detect unusual API activity
   - Use case: Security auditing, compliance, incident
     investigation, operational troubleshooting

2. **AWS Config**
   - Service that continuously monitors and records
     AWS resource configurations and evaluates them
     against desired settings
   - Features: Configuration history for resources,
     configuration snapshots, compliance rules
     (managed and custom), remediation actions,
     aggregated view across accounts and Regions
   - Use case: Compliance auditing, change management,
     security analysis, troubleshooting configuration
     changes

**User Authentication:**

1. **Amazon Cognito**
   - Service for adding user sign-up, sign-in, and
     access control to web and mobile applications
   - Components:
     - User Pools: User directory for authentication;
       handles sign-up, sign-in, account recovery,
       MFA, and social/enterprise identity federation
     - Identity Pools: Provides temporary AWS
       credentials to access AWS services directly
       (e.g., S3, DynamoDB) for authenticated or
       guest users
   - Features: Supports OAuth 2.0, SAML, OpenID
     Connect; integrates with social identity
     providers (Google, Facebook, Apple); scales to
     millions of users
   - Use case: Adding authentication to web/mobile
     apps, federating identities, granting temporary
     AWS access to app users

**Key Concepts for the Cloud Practitioner Exam:**

- **Artifact** is for downloading compliance reports
  and managing agreements (not a security scanning
  tool)
- **CloudTrail** answers "who did what and when" in
  your AWS account; enabled by default with 90-day
  history
- **Config** answers "what is the current configuration
  and has it changed"; used for compliance rules
- **CloudTrail vs. Config**: CloudTrail tracks API
  activity (actions); Config tracks resource
  configuration state
- **Cognito** is for application-level user
  authentication (end users of your app), not for
  AWS account access (that is IAM)

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

### AWS Compute Services

AWS offers a range of compute services from virtual
servers to serverless functions, enabling customers to
choose the right compute model for each workload.

**Virtual Servers:**

1. **Amazon EC2 (Elastic Compute Cloud)**
   - Virtual servers in the cloud with full control
     over the operating system, networking, and storage
   - Instance type categories:
     - **General Purpose (T, M families)**: Balanced
       compute, memory, and networking; web servers,
       code repositories, development environments
     - **Compute Optimized (C family)**: High-
       performance processors; batch processing,
       media transcoding, gaming servers, ML inference
     - **Memory Optimized (R, X, z families)**: Large
       amounts of RAM; in-memory databases, real-time
       big data analytics, SAP HANA
     - **Storage Optimized (I, D, H families)**: High
       sequential read/write to large datasets;
       data warehousing, distributed file systems,
       OLTP systems
     - **Accelerated Computing (P, G, Inf, Trn
       families)**: Hardware accelerators (GPUs,
       FPGAs); machine learning training, graphics
       rendering, video encoding
   - See the **EC2 Pricing Options** section for
     pricing details

**Serverless Compute:**

1. **AWS Lambda**
   - Serverless compute service that runs code in
     response to events without provisioning or
     managing servers
   - Features: Automatic scaling, pay only for compute
     time consumed (per millisecond), supports
     Python, Node.js, Java, Go, C#, Ruby, and custom
     runtimes, up to 15-minute execution timeout,
     up to 10 GB memory allocation
   - Pricing: Pay per request (first 1 million
     requests free per month) and per compute
     duration (GB-seconds)
   - Triggers: API Gateway, S3 events, DynamoDB
     Streams, Kinesis, SNS, SQS, CloudWatch Events,
     and many more
   - Use case: Event-driven processing, real-time
     file processing, API backends, data
     transformation, scheduled tasks (cron jobs)

**Container Services:**

1. **Amazon ECS (Elastic Container Service)**
   - Fully managed container orchestration service
     for running Docker containers
   - Launch types:
     - EC2 launch type: You manage the underlying
       EC2 instances
     - Fargate launch type: Serverless (no
       infrastructure management)
   - Features: Integration with ALB for load
     balancing, IAM roles per task, CloudWatch
     integration, service auto scaling
   - Use case: Running microservices, batch jobs,
     web applications in containers

2. **Amazon EKS (Elastic Kubernetes Service)**
   - Managed Kubernetes service for running
     Kubernetes on AWS without installing and
     operating your own control plane
   - Features: Managed control plane, supports
     EC2 and Fargate worker nodes, integrates with
     AWS services (ALB, IAM, VPC), supports
     Kubernetes ecosystem tools
   - Use case: Organizations already using Kubernetes,
     multi-cloud portability, complex container
     orchestration

3. **AWS Fargate**
   - Serverless compute engine for containers that
     works with both ECS and EKS
   - Features: No server or cluster management,
     pay for vCPU and memory used, each task runs
     in its own isolated environment, automatic
     scaling
   - Difference from EC2 launch type: Fargate
     eliminates the need to manage underlying
     instances; you only define your containers
   - Use case: Running containers without managing
     infrastructure, microservices, batch processing

**Platform as a Service:**

1. **AWS Elastic Beanstalk**
   - PaaS that deploys and manages web applications
     and services automatically
   - Supported platforms: Java, .NET, PHP, Node.js,
     Python, Ruby, Go, Docker
   - Features: Automatic capacity provisioning, load
     balancing, auto scaling, health monitoring,
     application versioning, environment management
   - You upload your code; Beanstalk handles
     deployment, from capacity provisioning and load
     balancing to auto scaling and health monitoring
   - You retain full control over the underlying AWS
     resources
   - No additional charge (pay only for underlying
     resources like EC2, ELB, RDS)
   - Use case: Quickly deploying web applications
     without managing infrastructure, developers
     who want to focus on code

**Simple Virtual Private Servers:**

1. **Amazon Lightsail**
   - Simplified virtual private server (VPS) service
     for simple web applications and workloads
   - Features: Pre-configured development stacks
     (LAMP, MEAN, WordPress, etc.), fixed monthly
     pricing, includes compute, storage, networking,
     and DNS management
   - Use case: Simple websites, blogs, small business
     applications, development/test environments,
     users new to AWS who want predictable pricing

**Batch Computing:**

1. **AWS Batch**
   - Fully managed batch processing service for
     running batch computing workloads at any scale
   - Features: Dynamically provisions compute
     resources (EC2 and Spot instances), job
     scheduling, job queues, integrates with Step
     Functions for workflow orchestration
   - No software to install; submits jobs and AWS
     Batch handles execution
   - Use case: Financial modeling, drug screening,
     genomics analysis, rendering, large-scale data
     processing, ML model training

**Key Concepts for the Cloud Practitioner Exam:**

- **Lambda** is the primary serverless compute service;
  know it runs code in response to events with no
  server management
- **Containers**: ECS is AWS-native; EKS is for
  Kubernetes; Fargate is serverless for both
- **Beanstalk vs. Lambda**: Beanstalk is for web
  applications (long-running); Lambda is for
  event-driven, short-duration functions
- **Lightsail vs. EC2**: Lightsail is simplified with
  fixed pricing for simple workloads; EC2 provides
  full control for complex requirements
- **EC2 instance types**: Know the general categories
  (general purpose, compute optimized, memory
  optimized, storage optimized, accelerated computing)
- **Serverless** means no infrastructure management:
  Lambda, Fargate, and Beanstalk (partially)

### AWS Networking and Content Delivery

AWS provides networking services for building
isolated cloud networks, routing traffic, delivering
content globally, and load balancing applications.

**Virtual Private Cloud:**

1. **Amazon VPC (Virtual Private Cloud)**
   - Isolated virtual network within the AWS Cloud
     where you launch AWS resources
   - Key components:
     - **Subnets**: Segments of a VPC's IP address
       range; public subnets (internet accessible)
       and private subnets (no direct internet access)
     - **Internet Gateway (IGW)**: Allows communication
       between VPC resources and the internet; one
       per VPC; enables public subnet internet access
     - **NAT Gateway**: Allows instances in private
       subnets to access the internet for updates
       and patches while preventing inbound internet
       connections; managed by AWS, deployed in a
       public subnet
     - **Route Tables**: Rules that determine where
       network traffic is directed; each subnet is
       associated with a route table
     - **VPC Peering**: Private connection between two
       VPCs using AWS network; non-transitive (A-B
       and B-C does not mean A-C)
     - **VPC Endpoints**: Private connections from your
       VPC to AWS services without traversing the
       public internet
       - Gateway Endpoints: For S3 and DynamoDB (free)
       - Interface Endpoints: For other AWS services
         (uses PrivateLink, per-hour + per-GB charges)
   - VPCs are Region-specific; subnets are
     Availability Zone-specific
   - Use case: Running applications in an isolated
     network, controlling network access, connecting
     to on-premises networks

**DNS and Domain Management:**

1. **Amazon Route 53**
   - Highly available and scalable DNS (Domain Name
     System) web service
   - Features: Domain registration, DNS routing,
     health checking, 100% availability SLA
   - Routing policies:
     - Simple: Route to a single resource
     - Weighted: Distribute traffic across multiple
       resources by weight
     - Latency-based: Route to the region with lowest
       latency
     - Failover: Active-passive failover using health
       checks
     - Geolocation: Route based on user's geographic
       location
     - Geoproximity: Route based on geographic
       location of resources with traffic bias
     - Multi-value answer: Return multiple healthy
       records
   - Use case: Domain registration, DNS management,
     traffic routing, health checking and failover

**Content Delivery:**

1. **Amazon CloudFront**
   - Content delivery network (CDN) that delivers
     data, videos, applications, and APIs to users
     globally with low latency
   - Features: 450+ Points of Presence (Edge
     Locations) worldwide, caches content at edge
     locations, DDoS protection with Shield Standard
     included, integration with S3, ALB, EC2, API
     Gateway, and Lambda@Edge
   - Supports: Static and dynamic content, video
     streaming (live and on-demand), WebSocket
   - Use case: Accelerating website delivery,
     streaming media, serving static assets, API
     acceleration

**API Management:**

1. **Amazon API Gateway**
   - Fully managed service for creating, publishing,
     maintaining, and securing APIs at any scale
   - API types: REST APIs, HTTP APIs, WebSocket APIs
   - Features: Throttling, caching, authentication
     and authorization (IAM, Cognito, Lambda
     authorizers), API versioning, usage plans and
     API keys, integration with Lambda, EC2, and
     other AWS services
   - Use case: Building serverless APIs (with Lambda),
     creating REST APIs for microservices, real-time
     communication with WebSocket APIs

**Load Balancing:**

1. **Elastic Load Balancing (ELB)**
   - Automatically distributes incoming application
     traffic across multiple targets (EC2 instances,
     containers, IP addresses, Lambda functions)
   - Types:
     - **Application Load Balancer (ALB)**: Layer 7
       (HTTP/HTTPS); content-based routing, host-based
       routing, path-based routing; best for web
       applications and microservices
     - **Network Load Balancer (NLB)**: Layer 4
       (TCP/UDP/TLS); ultra-high performance, millions
       of requests per second, ultra-low latency;
       best for extreme performance and static IPs
     - **Gateway Load Balancer (GLB)**: Layer 3
       (IP packets); deploys, scales, and manages
       third-party virtual appliances (firewalls,
       IDS/IPS); best for network security appliances
   - Features: Health checks, auto scaling
     integration, cross-zone load balancing, SSL/TLS
     termination, sticky sessions (ALB)
   - Use case: Distributing traffic across multiple
     AZs, high availability, fault tolerance

**Global Networking:**

1. **AWS Global Accelerator**
   - Networking service that improves application
     availability and performance using the AWS
     global network
   - Features: Static Anycast IP addresses, routes
     traffic to optimal endpoints over the AWS
     backbone network, automatic failover between
     Regions, health checking
   - Difference from CloudFront: Global Accelerator
     optimizes the network path (Layer 4); CloudFront
     caches content at edge locations (Layer 7)
   - Use case: Global applications requiring static
     IPs, multi-Region failover, improving
     performance for TCP/UDP traffic

**Key Concepts for the Cloud Practitioner Exam:**

- **VPC**: Know the purpose of subnets (public vs.
  private), IGW (internet access), NAT Gateway
  (outbound internet for private subnets), and
  route tables
- **Route 53**: DNS service + domain registration;
  name comes from DNS port 53
- **CloudFront**: CDN that caches content at edge
  locations to reduce latency globally
- **ALB vs. NLB**: ALB for HTTP/HTTPS (Layer 7); NLB
  for TCP/UDP (Layer 4) high-performance workloads
- **VPC Endpoints**: Access AWS services privately
  without using the public internet
- **Global Accelerator vs. CloudFront**: Global
  Accelerator for network optimization; CloudFront
  for content caching

### AWS Application Integration Services

AWS provides messaging and event services that enable
decoupled, scalable communication between application
components.

1. **Amazon SQS (Simple Queue Service)**
   - Fully managed message queuing service for
     decoupling application components
   - Queue types:
     - Standard Queue: Nearly unlimited throughput,
       at-least-once delivery, best-effort ordering
     - FIFO Queue: First-In-First-Out delivery,
       exactly-once processing, up to 3,000 messages
       per second (with batching)
   - Features: Message retention up to 14 days
     (default 4 days), message size up to 256 KB,
     dead-letter queues for failed messages,
     long polling and short polling, visibility
     timeout, server-side encryption
   - Use case: Decoupling microservices, buffering
     requests, handling asynchronous processing,
     order processing systems

2. **Amazon SNS (Simple Notification Service)**
   - Fully managed pub/sub (publish/subscribe)
     messaging service for sending notifications
   - Features: Topic-based messaging, fan-out pattern
     (one message to many subscribers), supports
     multiple subscriber types (SQS, Lambda, HTTP/S,
     email, SMS, mobile push), message filtering,
     FIFO topics for ordered delivery
   - Use case: Sending alerts and notifications,
     fan-out messages to multiple services, mobile
     push notifications, email/SMS notifications

3. **Amazon EventBridge**
   - Serverless event bus for building event-driven
     applications at scale
   - Features: Event routing from AWS services, SaaS
     applications, and custom sources; event rules
     to filter and route events to targets; schema
     registry for discovering event structures;
     archive and replay events; scheduled events
     (cron/rate)
   - Targets: Lambda, SQS, SNS, Step Functions,
     Kinesis, ECS, and 20+ other AWS services
   - Use case: Building event-driven architectures,
     integrating SaaS applications, automating
     responses to AWS resource changes, scheduled
     tasks

**Key Concepts for the Cloud Practitioner Exam:**

- **SQS** is for queuing (one-to-one, pull-based);
  **SNS** is for pub/sub (one-to-many, push-based)
- **SQS + SNS** fan-out pattern: SNS sends a message
  to multiple SQS queues for parallel processing
- **EventBridge** is the evolution of CloudWatch Events;
  preferred for event-driven architectures with
  advanced filtering and SaaS integration
- **Decoupling**: SQS and SNS help decouple
  application components for better scalability and
  resilience

### AWS Management and Governance Services

AWS provides services for monitoring, infrastructure
as code, operational management, scaling, and
multi-account governance.

**Monitoring and Observability:**

1. **Amazon CloudWatch**
   - Monitoring and observability service for AWS
     resources and applications
   - Features:
     - **CloudWatch Metrics**: Collect and track
       metrics from AWS services (CPU utilization,
       network traffic, etc.) and custom metrics
     - **CloudWatch Alarms**: Set alarms on metrics
       to trigger actions (Auto Scaling, SNS
       notifications, EC2 actions)
     - **CloudWatch Logs**: Collect, monitor, and
       store log files from EC2, Lambda, CloudTrail,
       Route 53, and other sources
     - **CloudWatch Dashboards**: Customizable
       visualizations of metrics and logs
     - **CloudWatch Events/EventBridge**: Respond to
       state changes in AWS resources (now part of
       Amazon EventBridge)
   - Use case: Monitoring AWS resource utilization,
     setting alarms for cost or performance
     thresholds, centralized log management,
     operational visibility

**Infrastructure as Code:**

1. **AWS CloudFormation**
   - Service for modeling and provisioning AWS
     resources using templates (Infrastructure as
     Code)
   - Features: JSON or YAML templates, stack
     management, change sets for previewing changes,
     drift detection, nested stacks, rollback on
     failure, cross-Region and cross-account
     deployment (StackSets)
   - Concepts:
     - Template: Defines the AWS resources to create
     - Stack: A collection of resources created from
       a template
     - Change set: Preview of changes before applying
   - No additional charge (pay only for resources
     created)
   - Use case: Repeatable infrastructure deployment,
     environment replication, disaster recovery,
     version-controlled infrastructure

**Operational Management:**

1. **AWS Systems Manager**
   - Unified interface for managing AWS resources and
     on-premises servers at scale
   - Key features:
     - Session Manager: Secure remote access to EC2
       instances without SSH keys or bastion hosts
     - Patch Manager: Automate OS and application
       patching across instances
     - Parameter Store: Secure hierarchical storage
       for configuration data and secrets (free tier
       available)
     - Run Command: Execute commands across multiple
       instances without SSH
     - Automation: Create runbooks for common
       maintenance tasks
   - Use case: Fleet management, patching, remote
     access, configuration management, operational
     automation

**Auto Scaling:**

1. **AWS Auto Scaling**
   - Service that automatically adjusts the number
     of compute resources to maintain performance
     and minimize costs
   - Scaling types:
     - **EC2 Auto Scaling**: Adds or removes EC2
       instances based on demand (most common)
     - **Application Auto Scaling**: Scales resources
       for other services (DynamoDB, ECS, Aurora,
       Lambda provisioned concurrency)
   - Scaling policies:
     - Target tracking: Maintain a specific metric
       value (e.g., 70% CPU)
     - Step scaling: Scale based on alarm thresholds
     - Scheduled scaling: Scale based on predictable
       patterns (time-based)
     - Predictive scaling: Uses ML to forecast demand
       and pre-scale
   - Components: Launch template/configuration
     (defines instance settings), Auto Scaling group
     (defines min, max, desired capacity), scaling
     policies
   - Use case: Maintaining application availability,
     optimizing costs by scaling down during low
     demand, handling traffic spikes

**Multi-Account Management:**

1. **AWS Organizations**
   - Service for centrally managing and governing
     multiple AWS accounts
   - Features:
     - Consolidated billing: Single payment method
       for all accounts; volume discounts aggregated
       across accounts
     - Organizational Units (OUs): Group accounts for
       hierarchical management
     - Service Control Policies (SCPs): Set permission
       guardrails across accounts (restrict what
       services or actions accounts can use)
     - AWS account creation automation
   - SCPs do not grant permissions; they set the
     maximum permissions boundary
   - Use case: Managing multiple AWS accounts,
     enforcing security policies, consolidated
     billing, environment isolation (dev, staging,
     production)

**Best Practices Advisory:**

1. **AWS Trusted Advisor**
   - Online tool that inspects your AWS environment
     and provides real-time recommendations across
     five categories:
     - **Cost Optimization**: Idle resources, unused
       Reserved Instances, underutilized resources
     - **Performance**: Over-utilized instances, high
       utilization EBS volumes, CloudFront
       optimization
     - **Security**: MFA on root account, security
       group rules, IAM access keys, S3 bucket
       permissions, exposed access keys
     - **Fault Tolerance**: EBS snapshots, Multi-AZ,
       Auto Scaling, backup configurations
     - **Service Limits**: Checks usage against service
       quotas and warns when approaching limits
   - Access levels:
     - Basic and Developer Support: 7 core checks
       (6 security + service limits)
     - Business, Enterprise On-Ramp, and Enterprise
       Support: Full set of checks with programmatic
       access via API
   - Use case: Identifying cost savings, improving
     security posture, increasing fault tolerance,
     monitoring service limits

**Key Concepts for the Cloud Practitioner Exam:**

- **CloudWatch** is the primary monitoring service;
  know metrics, alarms, logs, and dashboards
- **CloudFormation** is AWS's native Infrastructure
  as Code service; templates define resources
  declaratively
- **Auto Scaling** adjusts capacity automatically;
  works with EC2, DynamoDB, ECS, and more
- **Organizations**: Know consolidated billing and
  SCPs for security guardrails
- **Trusted Advisor**: Know the five categories and
  that full checks require Business+ support
- **Systems Manager**: Key for operational management;
  know Session Manager (secure access) and
  Parameter Store (configuration/secrets)

### AWS Cloud Concepts and Value Proposition

Understanding cloud computing fundamentals and the
value AWS provides is core to the Cloud Practitioner
exam.

**Benefits of Cloud Computing:**

1. **Agility**: Quickly spin up resources as needed;
   reduce time from weeks to minutes for provisioning
   infrastructure
2. **Elasticity**: Automatically scale resources up
   or down based on demand; no need to over-provision
   for peak capacity
3. **Cost savings (pay-as-you-go)**: Pay only for what
   you use; convert capital expense (CapEx) to
   operational expense (OpEx); no upfront investment
   in data centers
4. **Global reach**: Deploy applications in multiple
   Regions around the world in minutes; serve
   customers with low latency globally
5. **Economies of scale**: AWS aggregates usage from
   hundreds of thousands of customers, passing
   savings on as lower prices
6. **Speed and innovation**: Experiment quickly; fail
   fast and learn; reduce risk of large investments
   in unproven ideas

**Cloud Deployment Models:**

1. **Public Cloud**: Resources are owned and operated
   by a third-party cloud provider (e.g., AWS, Azure,
   GCP) and delivered over the internet. Multi-tenant
   environment; you share infrastructure with other
   customers
2. **Private Cloud (On-Premises)**: Cloud
   infrastructure operated solely for a single
   organization, either on-premises or hosted by a
   third party. Full control over hardware and
   software; higher costs
3. **Hybrid Cloud**: Combination of public and private
   cloud environments, connected by networking. Allows
   data and applications to move between environments.
   AWS services for hybrid: Outposts, Storage Gateway,
   Direct Connect, VPN
4. **Multi-Cloud**: Using services from multiple cloud
   providers simultaneously (e.g., AWS + Azure).
   Reduces vendor lock-in but adds complexity

**Cloud Computing Models (Service Models):**

1. **IaaS (Infrastructure as a Service)**
   - Provides virtualized computing resources over
     the internet (servers, storage, networking)
   - Customer manages: OS, middleware, runtime,
     applications, data
   - AWS examples: EC2, VPC, EBS
   - Most customer control and responsibility

2. **PaaS (Platform as a Service)**
   - Provides a platform for developing, running,
     and managing applications without managing
     infrastructure
   - Customer manages: Applications and data
   - AWS examples: Elastic Beanstalk, Lightsail,
     RDS (partially)
   - Balanced control and responsibility

3. **SaaS (Software as a Service)**
   - Complete software applications delivered over
     the internet on a subscription basis
   - Customer manages: User data and access
   - AWS examples: Amazon WorkSpaces, Amazon Chime,
     AWS Managed Grafana
   - Least customer control and responsibility

**Key Concepts for the Cloud Practitioner Exam:**

- Know all six benefits of cloud computing (agility,
  elasticity, cost savings, global reach, economies
  of scale, speed)
- **CapEx vs. OpEx**: Cloud converts upfront capital
  expenses to variable operational expenses
- **Deployment models**: Understand when to use public,
  private, hybrid, or multi-cloud
- **IaaS vs. PaaS vs. SaaS**: Know the level of
  customer responsibility for each model
- **Elasticity** is automatic; **scalability** is the
  ability to handle increased load (manual or auto)

### AWS Global Infrastructure

AWS operates a global infrastructure designed for
high availability, fault tolerance, and low latency.

**Core Components:**

1. **Regions**
   - Separate geographic areas where AWS clusters
     data centers (e.g., us-east-1, eu-west-1,
     ap-southeast-1)
   - Each Region is fully independent and isolated
     from other Regions for fault isolation
   - Most AWS services are Region-scoped
   - Factors for choosing a Region:
     - **Compliance**: Data residency and regulatory
       requirements (e.g., GDPR requires data in EU)
     - **Proximity**: Choose a Region close to your
       users to reduce latency
     - **Service availability**: Not all services are
       available in every Region
     - **Pricing**: Prices vary between Regions

2. **Availability Zones (AZs)**
   - Each Region has 3 or more AZs (minimum 3)
   - Each AZ is one or more discrete data centers
     with independent power, cooling, and networking
   - AZs within a Region are connected by high-speed,
     low-latency networking
   - AZs are physically separated (typically miles
     apart) to protect against localized failures
   - Deploying across multiple AZs provides high
     availability and fault tolerance

3. **Edge Locations**
   - Locations outside of AWS Regions used for caching
     content and running edge services
   - Many more Edge Locations than Regions (450+)
   - Services using Edge Locations: CloudFront (CDN),
     Route 53 (DNS), AWS WAF, Lambda@Edge, AWS Global
     Accelerator
   - Use case: Delivering content to end users with
     low latency

4. **Regional Edge Caches**
   - Sit between CloudFront Edge Locations and the
     origin server
   - Larger cache than Edge Locations; store less
     frequently accessed content
   - Reduce load on the origin server

**High Availability and Fault Tolerance:**

- **High availability (HA)**: System remains
  operational and accessible even when components
  fail; achieved by deploying across multiple AZs
  (e.g., Multi-AZ RDS, ALB across AZs)
- **Fault tolerance**: System continues operating
  correctly even when a component fails; achieved
  through redundancy and failover mechanisms
- **Disaster recovery**: Strategy for recovering
  from catastrophic failures; can involve
  multi-Region deployments
- Best practice: Deploy production workloads across
  at least 2 AZs within a Region for high
  availability

**Key Concepts for the Cloud Practitioner Exam:**

- **Regions > Availability Zones > Edge Locations**:
  Know the hierarchy and purpose of each
- **AZs provide high availability** within a Region;
  **multiple Regions** provide disaster recovery
- **Edge Locations** are for content delivery (CDN)
  and DNS, not for running primary workloads
- **Choosing a Region**: Compliance, proximity,
  service availability, and pricing
- Some services are global (IAM, Route 53, CloudFront,
  WAF); most services are Regional

### AWS Cloud Adoption Framework (AWS CAF)

The AWS Cloud Adoption Framework provides guidance
to help organizations develop an efficient plan for
their cloud adoption journey. It organizes guidance
into six areas of focus called perspectives.

**Six Perspectives:**

1. **Business Perspective**
   - Stakeholders: Business managers, finance
     managers, budget owners, strategy stakeholders
   - Focus: Ensuring IT aligns with business needs
     and that IT investments can be traced to
     business results
   - Capabilities: Strategy management, benefits
     realization, business risk management, cloud
     financial management
   - Helps: Create a strong business case for cloud
     adoption, prioritize cloud initiatives

2. **People Perspective**
   - Stakeholders: HR, staffing managers, people
     managers
   - Focus: Evaluating organizational structures and
     roles, identifying new skills and processes,
     and managing change
   - Capabilities: Culture evolution, transformational
     leadership, cloud fluency, workforce
     transformation, change acceleration,
     organizational design
   - Helps: Develop an organization-wide change
     management strategy for cloud adoption

3. **Governance Perspective**
   - Stakeholders: CIO, program managers, enterprise
     architects, business analysts, portfolio managers
   - Focus: Aligning IT strategy with business
     strategy, maximizing the value of IT investments
     while minimizing risks
   - Capabilities: Program and project management,
     benefits management, risk management, cloud
     financial management, application portfolio
     management, data governance and curation
   - Helps: Identify and prioritize IT investments,
     evaluate business outcomes

4. **Platform Perspective**
   - Stakeholders: CTO, IT managers, solutions
     architects
   - Focus: Building an enterprise-grade, scalable,
     hybrid cloud platform and modernizing workloads
   - Capabilities: Platform architecture, data
     architecture, platform engineering, data
     engineering, provisioning and orchestration,
     modern application development, CI/CD
   - Helps: Build the cloud platform, implement new
     solutions, migrate existing workloads

5. **Security Perspective**
   - Stakeholders: CISO, IT security managers,
     security analysts and engineers
   - Focus: Achieving the confidentiality, integrity,
     and availability of data and cloud workloads
   - Capabilities: Security governance, security
     assurance, identity and access management,
     threat detection, vulnerability management,
     infrastructure protection, data protection,
     application security, incident response
   - Helps: Structure the selection and implementation
     of security controls

6. **Operations Perspective**
   - Stakeholders: IT operations managers, IT support
     managers, site reliability engineers
   - Focus: Ensuring cloud services are delivered at
     levels that meet business needs
   - Capabilities: Observability, event management,
     incident and problem management, change and
     release management, performance and capacity
     management, configuration management, patch
     management, availability and continuity
     management
   - Helps: Define operating procedures, identify
     process improvements, implement best practices

**Cloud Transformation Domains:**

The AWS CAF identifies four transformation domains:

1. **Technology**: Migrate and modernize legacy
   infrastructure, applications, and data platforms
2. **Process**: Digitize, automate, and optimize
   business operations
3. **Organization**: Reimagine the operating model
   and organize teams around products and value
   streams
4. **Product**: Reimagine the business model by
   creating new value propositions and revenue models

**Key Concepts for the Cloud Practitioner Exam:**

- Know the six perspectives and which stakeholders
  are associated with each
- **Business, People, Governance** are business-focused
  perspectives
- **Platform, Security, Operations** are technical-
  focused perspectives
- The CAF is a guidance framework (not a service or
  tool)
- Understand that the CAF helps organizations plan
  and execute cloud adoption

### AWS Billing, Pricing, and Cost Management

Understanding AWS pricing models and cost management
tools is essential for the Cloud Practitioner exam.

**AWS Pricing Models:**

1. **Pay-as-you-go**: Pay only for the resources you
   consume; no upfront commitments or long-term
   contracts. Enables agility and reduces risk
2. **Save when you reserve**: Commit to a consistent
   amount of usage (1 or 3 years) for significant
   discounts. Applies to EC2, RDS, Redshift,
   ElastiCache, and others (Reserved Instances and
   Savings Plans)
3. **Pay less by using more**: Volume-based discounts;
   the more you use, the lower the per-unit cost.
   Applies to S3 storage tiers and data transfer

**AWS Free Tier:**

- **Always Free**: Services that are always free
  within certain limits (e.g., Lambda 1M requests/
  month, DynamoDB 25 GB storage, SNS 1M publishes)
- **12 Months Free**: Free for 12 months after
  account creation (e.g., EC2 750 hours/month t2.micro
  or t3.micro, S3 5 GB, RDS 750 hours/month)
- **Trials**: Short-term free trials for specific
  services (e.g., SageMaker, Redshift, Lightsail)

**Cost Management Tools:**

1. **AWS Cost Explorer**
   - Visualize, understand, and manage your AWS costs
     and usage over time
   - Features: Custom reports, hourly/daily/monthly
     granularity, filtering by service/region/account/
     tag, forecasting future costs (up to 12 months),
     Reserved Instance recommendations, Savings Plans
     recommendations
   - Use case: Analyzing spending trends, identifying
     cost drivers, planning budgets

2. **AWS Budgets**
   - Set custom budgets and receive alerts when costs
     or usage exceed (or are forecasted to exceed)
     defined thresholds
   - Budget types: Cost budgets, usage budgets,
     reservation budgets, Savings Plans budgets
   - Features: Email and SNS notifications, budget
     actions (e.g., apply IAM policy, stop EC2
     instances when budget is exceeded)
   - First 2 budgets are free; additional budgets
     cost $0.02/day each
   - Use case: Preventing cost overruns, tracking
     Reserved Instance utilization, team-level
     cost management

3. **AWS Cost and Usage Report (CUR)**
   - Most comprehensive cost and usage data available
     from AWS
   - Features: Detailed line-item data for all AWS
     services, delivered to S3 bucket, includes
     resource-level detail, integrates with Athena,
     Redshift, and QuickSight for analysis
   - Use case: Detailed cost analysis, chargeback/
     showback to teams, custom reporting and
     analytics

4. **AWS Pricing Calculator**
   - Web-based tool for estimating the monthly cost
     of AWS services before you start using them
   - Features: Estimate costs for individual services
     or full architectures, compare pricing options,
     export estimates, share with stakeholders
   - Replaces the legacy Simple Monthly Calculator
   - Use case: Pre-migration cost planning, comparing
     deployment options, budgeting for new projects

**Consolidated Billing with AWS Organizations:**

- Single payment method for all member accounts in
  an organization
- Combined usage across accounts qualifies for volume
  pricing discounts (e.g., S3 storage tiers, data
  transfer)
- Reserved Instances and Savings Plans are shared
  across the organization (if sharing is enabled)
- Detailed billing data available for each member
  account
- Simplifies financial management for multi-account
  environments

**Additional Pricing Concepts:**

- **Data transfer pricing**: Inbound data transfer to
  AWS is generally free; outbound data transfer from
  AWS is charged per GB (tiered pricing); data
  transfer between AZs is charged; data transfer
  within the same AZ is free
- **Tagging for cost allocation**: Use resource tags
  to categorize and track costs by project, team,
  environment, or any custom dimension

**Key Concepts for the Cloud Practitioner Exam:**

- Know the three pricing models: pay-as-you-go,
  reserve, pay less by using more
- **Free Tier** has three types: Always Free, 12 Months
  Free, Trials
- **Cost Explorer** is for analyzing past and
  forecasting future costs
- **Budgets** is for setting spending alerts and
  thresholds
- **Pricing Calculator** is for estimating future
  costs before deploying
- **Consolidated Billing** provides volume discounts
  across multiple accounts
- **Data transfer in** is free; **data transfer out**
  is charged

### Tricky Exam Questions

This section covers commonly confused scenarios and
tricky questions that appear on the CLF-C02 exam.

1. **Server Migration with Database**

   > A company has an on-premises Linux-based server with
   > an Oracle database that runs on it. The company wants
   > to migrate the database server to run on an Amazon EC2
   > instance in AWS.
   >
   > Which service should the company use to complete the
   > migration?

   **Answer**: AWS Application Migration Service (MGN)

   Why not DMS? This is a common trap. The question asks
   to migrate the *server* (Linux server with Oracle on it)
   to EC2, not just the database. AWS DMS is for migrating
   *database data* between database engines. MGN is for
   lift-and-shift migration of entire servers (including
   the OS, applications, and databases running on them)
   to EC2 instances.

   - Use **MGN** when: Migrating entire servers/VMs to EC2
     (lift-and-shift)
   - Use **DMS** when: Migrating database data from one
     database to another (e.g., on-premises Oracle to
     Amazon RDS, or Oracle to Aurora)

2. **Encryption in Transit**

   > Which AWS service should be used to implement
   > encryption in transit?

   **Answer**: AWS Certificate Manager (ACM)

   Why not KMS? This is a common trap. KMS is for
   **encryption at rest** (encrypting data stored on disk).
   ACM provides SSL/TLS certificates for **encryption in
   transit** (encrypting data as it moves between systems).

   - Use **ACM** when: Enabling HTTPS on websites, securing
     API endpoints, encrypting data moving between clients
     and servers (in transit)
   - Use **KMS** when: Encrypting data stored in S3, EBS,
     RDS, or other storage services (at rest)

3. **Sensitive Data Discovery in S3**

   > A user needs to automatically discover, classify, and
   > protect sensitive data stored in Amazon S3.
   >
   > Which AWS service should be used?

   **Answer**: Amazon Macie

   Why not GuardDuty or Inspector? This is a common trap.
   The key words are "discover, classify, and protect
   *sensitive data* in S3." Macie uses machine learning
   specifically to find sensitive data (PII, financial
   data, credentials) in S3 buckets.

   - Use **Macie** when: Discovering sensitive data (PII,
     credit cards, etc.) in S3, data privacy compliance
     (GDPR, HIPAA)
   - Use **GuardDuty** when: Detecting threats and malicious
     activity across your AWS account (compromised
     instances, unauthorized access)
   - Use **Inspector** when: Scanning for software
     vulnerabilities in EC2, ECR images, and Lambda

4. **Large Data Transfer with Limited Bandwidth**

   > A company needs to perform a one-time migration of
   > 40 TB of data from its on-premises storage servers
   > to Amazon S3. The transfer must happen as quickly as
   > possible while keeping costs to a minimum. The company
   > has 100 Mbps internet connectivity.
   >
   > Which AWS service should be used?

   **Answer**: AWS Snowball Edge

   Why not DataSync or Direct Connect? Do the math:
   40 TB over 100 Mbps = ~37 days of continuous transfer.
   The question says "as quickly as possible" — shipping
   a physical Snowball device is much faster. Direct
   Connect requires setup time and ongoing costs, making
   it unsuitable for a one-time migration.

   Rule of thumb: If network transfer would take more than
   1 week, consider Snow Family devices.

   - Use **Snowball Edge** when: Large one-time transfers
     (TBs to PBs), limited bandwidth, transfer would take
     weeks over the network
   - Use **DataSync** when: Online transfers where network
     bandwidth is sufficient, ongoing/repeated transfers
   - Use **Direct Connect** when: Ongoing private
     connectivity needs, not one-time migrations

5. **AWS CAF Perspective for Risk Management**

   > A company that is migrating to the AWS Cloud wants
   > guidance about operational risk management. The company
   > wants to lower its overall risk profile.
   >
   > Which perspective of the AWS Cloud Adoption Framework
   > (AWS CAF) provides guidance on risk management?

   **Answer**: Governance Perspective

   Why not Security or Operations? This is tricky because
   "risk" appears in multiple contexts. The Governance
   perspective specifically addresses *organizational risk
   management*, compliance, and ensuring cloud investments
   align with business objectives. Security focuses on
   protecting data and workloads; Operations focuses on
   running services effectively.

   AWS CAF has 6 perspectives (memorize these):

   - **Business**: Strategy, benefits realization, business
     outcomes
   - **People**: Culture, organizational change, skills
   - **Governance**: Risk management, compliance, portfolio
     management, budget/cost
   - **Platform**: Infrastructure, architecture, CI/CD
   - **Security**: Identity management, data protection,
     incident response
   - **Operations**: Observability, event management,
     incident management

6. **Analyzing AWS Costs Over Time**

   > Which AWS service or feature gives a company the
   > ability to analyze AWS costs and usage over time?

   **Answer**: AWS Cost Explorer

   Why not Budgets or Cost and Usage Report? The key phrase
   is "analyze costs *over time*." Cost Explorer provides
   visualizations and graphs to analyze historical spending
   patterns and forecast future costs. Budgets is for
   setting alerts, not analyzing. CUR provides raw data
   but no built-in analysis interface.

   - Use **Cost Explorer** when: Analyzing past costs,
     visualizing spending trends, forecasting future costs,
     getting RI/Savings Plans recommendations
   - Use **Budgets** when: Setting spending thresholds and
     receiving alerts when exceeded
   - Use **Cost and Usage Report (CUR)** when: Needing
     detailed raw data for custom analysis in Athena,
     Redshift, or QuickSight
   - Use **Pricing Calculator** when: Estimating costs
     *before* deploying (not analyzing existing usage)

7. **Hybrid Storage for On-Premises Applications**

   > Which AWS hybrid storage service enables a user's
   > on-premises applications to seamlessly use AWS Cloud
   > storage?

   **Answer**: AWS Storage Gateway

   Why not DataSync or Snow Family? The key words are
   "hybrid storage" and "seamlessly use." Storage Gateway
   provides on-premises applications with transparent
   access to cloud storage via standard protocols (NFS,
   SMB, iSCSI). DataSync is for *transferring* data, not
   providing seamless ongoing access. Snow Family is for
   offline/edge scenarios.

   - Use **Storage Gateway** when: On-premises apps need
     seamless, ongoing access to cloud storage (hybrid
     storage); backup to cloud; tiered storage
   - Use **DataSync** when: Migrating or replicating data
     between on-premises and AWS (one-time or scheduled
     transfers)
   - Use **Snow Family** when: Offline data transfer,
     edge computing, limited/no network connectivity

8. **Finding Third-Party Assistance for Deployment**

   > A company wants assistance in creating a cloud
   > environment for its business.
   >
   > Which AWS service or feature can the company use to
   > find a third party to assist with the deployment?

   **Answer**: AWS Partner Network (APN) / AWS Marketplace

   Why not Professional Services or Support? The key phrase
   is "find a *third party*." AWS Professional Services is
   AWS's own team, not third-party. AWS Support provides
   technical assistance, not deployment partners. The APN
   and Marketplace help you find certified consulting
   partners and software vendors.

   - Use **AWS Partner Network (APN)** when: Finding
     certified consulting partners, system integrators,
     or technology partners for deployment assistance
   - Use **AWS Marketplace** when: Finding third-party
     software, services, and consulting offerings
   - Use **AWS Professional Services** when: Engaging
     AWS's own experts (not third-party)
   - Use **AWS Support** when: Getting technical help
     with AWS services (not deployment assistance)

9. **Isolating Costs by Workload Type**

   > How can a company isolate the costs of production and
   > non-production workloads on AWS?

   **Answer**: Cost allocation tags

   Why not separate accounts or Cost Explorer? While
   separate accounts *can* isolate costs, the simplest and
   most common answer for this question is cost allocation
   tags. Tags let you categorize resources (e.g.,
   Environment: Production vs. Environment: Development)
   and then filter costs in Cost Explorer or CUR reports.

   - Use **Cost allocation tags** when: Categorizing and
     tracking costs by project, team, environment, or any
     custom dimension within an account
   - Use **Separate AWS accounts** when: Needing complete
     isolation (security, billing, resource limits) — more
     complex but stronger separation
   - Use **Cost Explorer** when: Analyzing and visualizing
     costs (works with tags to filter by category)

10. **Guidance for Cloud Migration Process**

    > A company is planning to move to the AWS Cloud. The
    > company wants guidance and best practices to guide
    > key stakeholders on the cloud migration process.
    >
    > Which AWS service or guidance will meet this
    > requirement?

    **Answer**: AWS Cloud Adoption Framework (AWS CAF)

    Why not Well-Architected Framework or Migration Hub?
    The key phrases are "guide key stakeholders" and
    "migration process." AWS CAF is specifically designed
    to help organizations plan and execute cloud adoption
    across 6 perspectives (Business, People, Governance,
    Platform, Security, Operations). Well-Architected is
    for designing workloads *after* you're in the cloud.
    Migration Hub is a tool for tracking migrations, not
    guidance.

    - Use **AWS CAF** when: Planning cloud adoption,
      guiding stakeholders through migration strategy,
      organizational transformation
    - Use **Well-Architected Framework** when: Designing
      and reviewing workloads already in the cloud (best
      practices for architecture)
    - Use **Migration Hub** when: Tracking the progress
      of actual migrations (a tool, not guidance)

11. **Sharing Reserved Instances Across Accounts**

    > How should Reserved Instances be shared across
    > multiple AWS accounts?

    **Answer**: AWS Organizations with consolidated billing

    Why not IAM or Resource Access Manager? Reserved
    Instances and Savings Plans are automatically shared
    across all accounts in an AWS Organization when
    consolidated billing is enabled. This is a billing
    feature, not a resource sharing feature. RAM is for
    sharing resources like subnets or Transit Gateways,
    not billing discounts.

    - Use **AWS Organizations (consolidated billing)**
      when: Sharing Reserved Instances and Savings Plans
      discounts across multiple accounts, getting volume
      discounts
    - Use **Resource Access Manager (RAM)** when: Sharing
      AWS resources (subnets, Transit Gateways, License
      Manager configs) across accounts — not for billing
    - Use **IAM** when: Managing access permissions within
      or across accounts — not for sharing RI discounts

12. **CAF Perspective Connecting Technology and Business**

    > Which perspective of the AWS Cloud Adoption Framework
    > (AWS CAF) connects technology and business?

    **Answer**: Platform Perspective

    Why not Business or Governance? The Platform perspective
    is the bridge between business capabilities and
    technical implementation. It focuses on building an
    enterprise-grade, scalable, hybrid cloud platform,
    modernizing workloads, and implementing new solutions.
    Business perspective focuses on business outcomes;
    Governance focuses on risk and compliance.

    CAF perspectives grouped by focus:

    - **Business capabilities** (business-focused):
      - Business: Aligns IT with business outcomes
      - People: Culture, training, organizational change
      - Governance: Risk, compliance, budget management
    - **Technical capabilities** (technology-focused):
      - Platform: Connects business to technology,
        architecture, CI/CD — *the bridge*
      - Security: Protects data, workloads, accounts
      - Operations: Runs and monitors cloud services

13. **Short-Term EC2 Without Interruptions**

    > A cloud practitioner needs an Amazon EC2 instance to
    > launch and run for 7 hours without interruptions.
    >
    > What is the MOST cost-effective option for this task?

    **Answer**: On-Demand Instance

    Why not Spot Instance? The key phrase is "without
    interruptions." Spot Instances can be terminated by
    AWS with 2 minutes notice when capacity is needed,
    so they don't guarantee uninterrupted execution.
    Reserved Instances require 1-3 year commitments —
    overkill for 7 hours. On-Demand is pay-per-hour with
    no commitment and no interruption risk.

    - Use **On-Demand** when: Short-term workloads,
      unpredictable usage, no interruptions allowed,
      no upfront commitment
    - Use **Spot Instances** when: Flexible, fault-tolerant
      workloads that can handle interruptions (batch
      processing, CI/CD, stateless apps) — up to 90% off
    - Use **Reserved Instances** when: Steady-state,
      predictable workloads running 24/7 for 1-3 years
    - Use **Savings Plans** when: Flexible commitment
      across instance families or compute services

14. **Dedicated Hardware for Compliance Requirements**

    > Which AWS service helps users meet contractual and
    > regulatory compliance requirements for data security
    > by using dedicated hardware appliances within the
    > AWS Cloud?

    **Answer**: AWS CloudHSM

    Why not KMS or Dedicated Hosts? The key phrases are
    "dedicated hardware appliances" and "compliance
    requirements." CloudHSM provides single-tenant,
    dedicated Hardware Security Modules (HSMs) that are
    FIPS 140-2 Level 3 validated. KMS is multi-tenant
    (shared infrastructure). Dedicated Hosts are for
    running EC2 instances on dedicated physical servers,
    not for key management or encryption compliance.

    - Use **CloudHSM** when: Regulatory compliance requires
      dedicated hardware for key management (FIPS 140-2
      Level 3), you need full control over encryption keys,
      SSL/TLS offloading, certificate authority
    - Use **KMS** when: Standard encryption needs, no
      dedicated hardware requirement, managed key rotation,
      integration with AWS services
    - Use **Dedicated Hosts** when: Licensing compliance
      (per-socket, per-core software licenses), not for
      encryption hardware

15. **AWS Trusted Advisor Checks**

    > Which recommendations are included in the AWS Trusted
    > Advisor checks? (Select TWO)
    >
    > A. Amazon S3 bucket permissions
    > B. AWS service outages for services
    > C. MFA use on the root account
    > D. Available software patches for EC2 instances
    > E. Number of users in the account

    **Answer**: A and C

    - **A. S3 bucket permissions** ✓ — Trusted Advisor
      checks for S3 buckets with open access permissions
      (Security category)
    - **B. Service outages** ✗ — This is AWS Health
      Dashboard / Personal Health Dashboard, not Trusted
      Advisor
    - **C. MFA on root account** ✓ — Trusted Advisor checks
      if MFA is enabled on root account (Security category)
    - **D. Software patches for EC2** ✗ — This is AWS
      Systems Manager Patch Manager or Amazon Inspector,
      not Trusted Advisor
    - **E. Number of users** ✗ — Trusted Advisor doesn't
      count users; use IAM Credential Report for this

    Trusted Advisor has 5 categories:

    - **Cost Optimization**: Idle resources, underutilized
      instances, Reserved Instance optimization
    - **Performance**: Over-utilized instances, CloudFront
      optimization
    - **Security**: S3 permissions, security groups, MFA,
      IAM access keys, exposed access keys
    - **Fault Tolerance**: EBS snapshots, RDS backups,
      Multi-AZ, Auto Scaling
    - **Service Limits**: Approaching service quotas

16. **Well-Architected Framework Pillars**

    > Which of the following are pillars of the AWS
    > Well-Architected Framework? (Select TWO)
    >
    > A. Multiple Availability Zones
    > B. Performance Efficiency
    > C. Sustainability
    > D. Encryption usage
    > E. High availability

    **Answer**: B and C

    - **A. Multiple Availability Zones** ✗ — This is a
      best practice/design pattern, not a pillar
    - **B. Performance Efficiency** ✓ — One of the 6
      pillars
    - **C. Sustainability** ✓ — One of the 6 pillars
      (added in 2021)
    - **D. Encryption usage** ✗ — This falls under the
      Security pillar, but is not a pillar itself
    - **E. High availability** ✗ — This is a concept
      covered under Reliability pillar, not a pillar itself

    The 6 Well-Architected Framework Pillars (memorize):

    1. **Operational Excellence**: Run and monitor systems,
       continually improve processes
    2. **Security**: Protect data, systems, and assets
    3. **Reliability**: Recover from failures, meet demand
    4. **Performance Efficiency**: Use resources efficiently
    5. **Cost Optimization**: Avoid unnecessary costs
    6. **Sustainability**: Minimize environmental impact

    Memory trick: **O-S-R-P-C-S** or "Oscar Sells Real
    Purple Colored Socks"

17. **Comparing On-Premises Costs to AWS**

    > A company is reviewing the current costs of running
    > its own infrastructure on premises. The company wants
    > to compare these on-premises costs to the costs of
    > running infrastructure in the AWS Cloud.
    >
    > How should the company make this comparison?

    **Answer**: AWS Pricing Calculator (or Migration
    Evaluator for detailed TCO analysis)

    Why not Cost Explorer or Budgets? The key phrase is
    "compare on-premises costs to AWS costs" — this is a
    *pre-migration* estimate, not analyzing existing AWS
    usage. Cost Explorer analyzes costs you've already
    incurred in AWS. Budgets sets alerts for current
    spending. Pricing Calculator estimates future AWS
    costs before you deploy.

    Note: AWS previously had a "TCO Calculator" which was
    retired. Migration Evaluator provides detailed TCO
    analysis for migrations, while Pricing Calculator
    provides general AWS cost estimates.

    - Use **Pricing Calculator** when: Estimating AWS costs
      before deployment, comparing pricing options,
      building cost proposals
    - Use **Migration Evaluator** when: Detailed Total Cost
      of Ownership (TCO) analysis for migration planning,
      comparing on-premises vs. AWS costs
    - Use **Cost Explorer** when: Analyzing costs *after*
      you're already using AWS
    - Use **Budgets** when: Setting spending alerts for
      current AWS usage

18. **VPC Security Mechanisms**

    > Which AWS security mechanisms can be used to restrict
    > or permit access to resources within a VPC?
    > (Select TWO)
    >
    > A. Security Groups
    > B. Network ACLs
    > C. IAM policies
    > D. AWS WAF
    > E. AWS Shield
    > F. Route Tables

    **Answer**: A and B

    - **A. Security Groups** ✓ — Virtual firewalls at the
      instance/ENI level; control inbound/outbound traffic
    - **B. Network ACLs** ✓ — Firewalls at the subnet level;
      control traffic in/out of subnets
    - **C. IAM policies** ✗ — Control access to AWS APIs
      and services, not network traffic within a VPC
    - **D. AWS WAF** ✗ — Protects web applications at
      Layer 7 (CloudFront, ALB, API Gateway), not VPC
      network traffic
    - **E. AWS Shield** ✗ — DDoS protection service, not
      a VPC access control mechanism
    - **F. Route Tables** ✗ — Route tables determine *where*
      traffic is directed (routing), not whether traffic
      is *allowed or denied* (security). They control
      network paths, not access permissions

    Security Groups vs. Network ACLs:

    | Feature | Security Groups | Network ACLs |
    | --- | --- | --- |
    | Level | Instance/ENI | Subnet |
    | State | Stateful | Stateless |
    | Rules | Allow only | Allow and Deny |
    | Default | Deny all inbound | Allow all |
    | Evaluation | All rules | Rules in order |

19. **Support Plan for 15-Minute Response Time**

    > A company is running a production workload on AWS.
    >
    > Which AWS Support plan will provide the company with
    > technical support within 15 minutes of a user opening
    > a case concerning a critical service interruption?

    **Answer**: Enterprise Support (or Enterprise On-Ramp)

    Why not Business Support? The key is "15 minutes" for
    critical issues. Business Support offers 1-hour
    response for production system down. Only Enterprise
    plans offer 15-minute response for business-critical
    system down.

    Support Plan Response Times (critical/production down):

    | Plan | Critical Response | Production Down |
    | --- | --- | --- |
    | Basic | N/A | N/A |
    | Developer | N/A | N/A |
    | Business | N/A | < 1 hour |
    | Enterprise On-Ramp | < 30 min | < 1 hour |
    | Enterprise | < 15 min | < 1 hour |

    Key differences to memorize:

    - **Basic**: Free, no technical support cases
    - **Developer**: $29+/month, business hours email,
      < 24 hr general, < 12 hr system impaired
    - **Business**: $100+/month, 24/7 phone/chat,
      < 1 hr production down, Trusted Advisor full
    - **Enterprise On-Ramp**: $5,500/month, < 30 min
      critical, pool of TAMs
    - **Enterprise**: $15,000+/month, < 15 min critical,
      dedicated TAM, Concierge Support

20. **Shared Controls in Shared Responsibility Model**

    > Which controls or features are shared controls
    > according to the AWS shared responsibility model?
    > (Select TWO)
    >
    > A. Physical security controls
    > B. Environmental controls
    > C. Patch management
    > D. Zone security
    > E. Configuration management

    **Answer**: C and E

    - **A. Physical security controls** ✗ — AWS
      responsibility only (data centers, hardware)
    - **B. Environmental controls** ✗ — AWS responsibility
      only (power, cooling, fire suppression)
    - **C. Patch management** ✓ — Shared: AWS patches
      infrastructure/managed services; customer patches
      guest OS, applications, and EC2 instances
    - **D. Zone security** ✗ — Not a standard term in the
      shared responsibility model
    - **E. Configuration management** ✓ — Shared: AWS
      configures infrastructure; customer configures
      guest OS, databases, applications, security groups

    Shared Responsibility Model categories:

    - **AWS responsibility** ("Security OF the cloud"):
      Physical security, environmental controls, hardware,
      network infrastructure, virtualization layer
    - **Customer responsibility** ("Security IN the cloud"):
      Customer data, IAM, application security, OS/network
      configuration, encryption
    - **Shared controls** (both AWS and customer):
      Patch management, configuration management,
      awareness & training

21. **Encrypting CloudTrail Data**

    > A user must encrypt data that is received, stored,
    > and managed by AWS CloudTrail.
    >
    > Which AWS service will provide this capability?

    **Answer**: AWS KMS (Key Management Service)

    Why not CloudHSM or ACM? CloudTrail logs are stored in
    S3. To encrypt CloudTrail data, you configure the
    trail to use a KMS key (SSE-KMS encryption). CloudHSM
    is for dedicated hardware requirements and doesn't
    directly integrate with CloudTrail. ACM is for SSL/TLS
    certificates (encryption in transit), not encrypting
    stored log files.

    Note: CloudTrail encrypts log files by default using
    S3 server-side encryption (SSE-S3). For additional
    control, you can use SSE-KMS with your own KMS key.

    - Use **KMS** when: Encrypting data at rest in AWS
      services (S3, EBS, RDS, CloudTrail logs), need
      centralized key management and audit trail
    - Use **CloudHSM** when: Regulatory compliance requires
      dedicated hardware, full control over HSM
    - Use **ACM** when: SSL/TLS certificates for encryption
      in transit (HTTPS)

22. **Improving Availability and Performance**

    > A company wants to improve the overall availability
    > and performance of its applications that are hosted
    > on AWS.
    >
    > Which AWS service should the company use?

    **Answer**: AWS Global Accelerator

    Why not CloudFront or Route 53? This is tricky because
    multiple services improve performance. The key is
    "availability AND performance" for *applications*.
    Global Accelerator uses AWS's global network to route
    traffic to optimal endpoints, improving both
    availability (automatic failover) and performance
    (reduced latency). CloudFront is for caching content
    (CDN). Route 53 provides DNS-based routing but doesn't
    accelerate the actual network path.

    - Use **Global Accelerator** when: Improving
      availability and performance for TCP/UDP
      applications (gaming, IoT, VoIP), need static
      anycast IPs, automatic failover across regions
    - Use **CloudFront** when: Caching static/dynamic
      content closer to users (CDN), streaming media,
      reducing origin load
    - Use **Route 53** when: DNS management, domain
      registration, DNS-based routing (latency, failover,
      geolocation)
    - Use **Elastic Load Balancing** when: Distributing
      traffic across targets within a region

23. **Data Visualization for Business Intelligence**

    > A Cloud Practitioner needs to recommend a data
    > visualization tool for analysts to use to create
    > user-friendly business intelligence insights.
    >
    > Which AWS service should the Cloud Practitioner
    > recommend?

    **Answer**: Amazon QuickSight

    Why not Athena or Redshift? The key phrases are "data
    visualization" and "business intelligence insights."
    QuickSight is AWS's serverless BI service for creating
    dashboards and visualizations. Athena is for querying
    data (SQL), not visualizing it. Redshift is a data
    warehouse for storing and analyzing data, not a
    visualization tool.

    - Use **QuickSight** when: Creating dashboards,
      visualizations, business intelligence reports,
      sharing insights with stakeholders, ML-powered
      insights (QuickSight Q)
    - Use **Athena** when: Running SQL queries on data
      in S3 (serverless query engine)
    - Use **Redshift** when: Data warehousing, complex
      analytical queries on petabytes of data
    - Use **Glue** when: ETL jobs, data cataloging,
      preparing data for analytics

24. **Auditing and Monitoring Changes to Resources**

    > Which service gives customers the ability to audit
    > and monitor changes in AWS resources?

    **Answer**: AWS Config

    Why not CloudTrail or CloudWatch? This is a common
    trap. The key phrase is "changes in AWS resources"
    (configuration changes). AWS Config tracks resource
    configurations and changes over time. CloudTrail
    records API calls (who did what), not the state of
    resources. CloudWatch monitors performance metrics
    and logs, not configuration changes.

    - Use **AWS Config** when: Tracking resource
      configuration changes, compliance auditing,
      configuration history, evaluating resources against
      rules
    - Use **CloudTrail** when: Auditing API activity (who
      made what API call, when, from where), security
      investigation
    - Use **CloudWatch** when: Monitoring metrics (CPU,
      memory), logs, setting alarms, dashboards for
      operational health

25. **Checking Infrastructure Against Best Practices**

    > After a merger, a company inherits an AWS account
    > with an existing infrastructure. The company needs
    > to ensure that the infrastructure follows AWS best
    > practices for cost optimization, security, and
    > performance efficiency.
    >
    > Which AWS service or tool should the company use to
    > meet these requirements?

    **Answer**: AWS Trusted Advisor

    Why not Well-Architected Tool or AWS Config? The key
    is checking *existing infrastructure* against *best
    practices* across multiple categories. Trusted Advisor
    provides automated checks across 5 categories: cost
    optimization, security, fault tolerance, performance,
    and service limits. Well-Architected Tool is for
    reviewing workload architecture against the framework
    (more manual, design-focused). AWS Config tracks
    configuration compliance, not best practice
    recommendations.

    - Use **Trusted Advisor** when: Automated best practice
      checks for existing infrastructure, quick assessment
      across cost/security/performance/fault tolerance
    - Use **Well-Architected Tool** when: In-depth
      architectural review of specific workloads against
      the 6 pillars, generating improvement plans
    - Use **AWS Config** when: Compliance rules for
      specific configurations, tracking resource changes

26. **Gateway for Site-to-Site VPN**

    > A company needs to establish an AWS Site-to-Site VPN
    > tunnel.
    >
    > Which type of gateway should the company use?

    **Answer**: Virtual Private Gateway (VGW)

    Why not Internet Gateway or NAT Gateway? The key is
    "Site-to-Site VPN" which connects on-premises networks
    to AWS. A Virtual Private Gateway is the VPN
    concentrator on the AWS side of the Site-to-Site VPN
    connection. Internet Gateway is for internet access
    (public traffic). NAT Gateway is for outbound internet
    access from private subnets.

    Note: Transit Gateway can also terminate Site-to-Site
    VPN connections and is preferred for multiple VPCs.

    - Use **Virtual Private Gateway (VGW)** when:
      Site-to-Site VPN connection to a single VPC,
      Direct Connect termination
    - Use **Transit Gateway** when: Site-to-Site VPN to
      multiple VPCs, hub-and-spoke network architecture,
      connecting many VPCs and on-premises networks
    - Use **Internet Gateway** when: Enabling internet
      access for resources in public subnets
    - Use **NAT Gateway** when: Enabling outbound internet
      access for resources in private subnets

27. **Guidance for Migration Preparation**

    > A company wants to move to the AWS Cloud. The company
    > wants guidance based on best practices to help them
    > prepare for the migration.
    >
    > Which AWS resource should the company use that will
    > meet this requirement?

    **Answer**: AWS Cloud Adoption Framework (AWS CAF)

    Why not Well-Architected Framework? This is a common
    trap. The key phrase is "prepare for the migration."
    AWS CAF provides guidance for *planning and preparing*
    cloud adoption across 6 perspectives (Business, People,
    Governance, Platform, Security, Operations). The
    Well-Architected Framework is for *designing and
    reviewing workloads* that are already in the cloud —
    it's about architecture best practices, not migration
    preparation.

    Why not AWS Application Migration Service (MGN)? MGN is
    a tool for *executing* lift-and-shift migrations (moving
    servers), not guidance for preparing the migration
    strategy.

    - Use **AWS CAF** when: Planning cloud adoption,
      preparing migration strategy, guiding stakeholders,
      organizational transformation
    - Use **Well-Architected Framework** when: Designing
      workloads in the cloud, reviewing existing architectures
      against the 6 pillars (after migration)
    - Use **AWS MGN** when: Actually migrating servers to EC2
      (lift-and-shift execution)
    - Use **AWS Support** when: Getting technical help with
      AWS services, not strategic migration guidance

28. **Operational Excellence - Planning for Failure**

    > Which design principle in the operational excellence
    > pillar of the AWS Well-Architected Framework describes
    > the need to plan for potential issues and ensure system
    > resilience?

    **Answer**: Anticipate failure

    Why not the other options? All four options are valid
    design principles of Operational Excellence, but only
    "Anticipate failure" specifically addresses planning for
    potential issues and system resilience. The key phrases
    are "plan for potential issues" and "ensure resilience."

    Operational Excellence design principles:

    - **Anticipate failure** — Plan for failures, test
      failure scenarios, understand impact of failures,
      design for resilience (matches the question)
    - **Perform operations as code** — Define infrastructure
      and operations as code (IaC), automate procedures
    - **Make frequent, small, reversible changes** — Design
      workloads to allow incremental changes that can be
      rolled back if needed
    - **Refine operations procedures frequently** —
      Continuously improve operational procedures, conduct
      game days, learn from failures
    - **Learn from all operational failures** — Share
      lessons learned across teams

29. **Well-Architected Pillar for Data Protection**

    > Which pillar of the AWS Well-Architected Framework
    > focuses on the design principle of providing protection
    > to safeguard data, systems, and applications?

    **Answer**: Security pillar

    Why not Reliability? This can be confusing because
    Reliability also involves protecting systems — but from
    *failures*, not from *threats*. The key phrase is
    "safeguard data, systems, and applications" which
    implies protection from unauthorized access, breaches,
    and security threats. That's Security.

    The 6 Well-Architected Framework pillars:

    - **Security** — Protecting data, systems, and assets
      through confidentiality, integrity, and availability;
      identity management, detection, infrastructure
      protection, data protection, incident response
    - **Reliability** — Ability to recover from failures,
      meet demand, mitigate disruptions; focuses on
      fault tolerance and disaster recovery
    - **Performance Efficiency** — Using compute resources
      efficiently, maintaining efficiency as demand changes
    - **Operational Excellence** — Running and monitoring
      systems, continuously improving processes
    - **Cost Optimization** — Avoiding unnecessary costs,
      understanding spending, selecting appropriate resources
    - **Sustainability** — Minimizing environmental impact,
      reducing energy consumption

30. **Full Trusted Advisor Access - Cost Effective**

    > A company wants access to the full set of AWS Trusted
    > Advisor checks. Which AWS Support plan is the MOST
    > cost-effective option to meet the requirement?

    **Answer**: AWS Business Support plan

    Why not Enterprise? Enterprise also provides full
    Trusted Advisor access, but the question asks for the
    "MOST cost-effective" option. Business Support is
    cheaper than Enterprise and still provides full access
    to all Trusted Advisor checks.

    Why not Developer or Basic? These plans only provide
    access to a limited set of core Trusted Advisor checks
    (7 checks), not the full set (50+ checks).

    Trusted Advisor access by Support plan:

    - **Basic & Developer** — Limited to 7 core checks:
      - S3 bucket permissions
      - Security groups (specific ports unrestricted)
      - IAM use
      - MFA on root account
      - EBS public snapshots
      - RDS public snapshots
      - Service limits
    - **Business & Enterprise** — Full access to all 50+
      checks across all 5 categories (Cost Optimization,
      Performance, Security, Fault Tolerance, Service Limits)

    Support plan pricing (approximate):

    - **Basic**: Free
    - **Developer**: $29/month or 3% of monthly usage
    - **Business**: $100/month or 3-10% of monthly usage
    - **Enterprise On-Ramp**: $5,500/month
    - **Enterprise**: $15,000/month or 3-10% of monthly usage

31. **Consolidated Billing Cost Savings**

    > How does consolidated billing within AWS Organizations
    > help lower overall monthly expenses?

    **Answer**: By pooling usage across multiple accounts to
    achieve a pricing tier discount

    Why not the other options?

    - **SCPs for centralized service management** — SCPs
      control what services/actions accounts can use, but
      don't directly reduce costs. This is about governance,
      not billing savings.
    - **Automating account creation through APIs** — This is
      a convenience feature, not a cost-saving mechanism.
    - **Consolidated view of monthly billing** — This helps
      you *see* costs but doesn't *reduce* them. Visibility
      is not the same as savings.

    How usage pooling saves money:

    - AWS services like S3, EC2, and data transfer have
      **volume-based pricing tiers** — the more you use,
      the lower the per-unit price
    - Consolidated billing **aggregates usage** across all
      accounts in the organization
    - Combined usage reaches higher discount tiers faster
      than individual accounts would alone
    - Example: If 5 accounts each use 20 TB of S3, they're
      billed as 100 TB total, reaching a lower price tier

    Other consolidated billing benefits:

    - Single payment method for all accounts
    - Shared Reserved Instances and Savings Plans across
      accounts
    - Combined Free Tier usage (watch out — this can also
      mean hitting limits faster)

32. **AWS Incident Detection and Response Access**

    > Which AWS Support plan offers access to AWS Incident
    > Detection and Response for an additional fee?

    **Answer**: AWS Enterprise Support plan

    Why not Enterprise On-Ramp? This is tricky because
    Enterprise On-Ramp is also a premium support tier.
    However, AWS Incident Detection and Response is an
    add-on service available *only* to full Enterprise
    Support customers, not Enterprise On-Ramp.

    AWS Incident Detection and Response:

    - Proactive monitoring and incident management service
    - AWS experts monitor your critical workloads 24/7
    - Automated incident detection and rapid response
    - Available as an add-on for an additional fee
    - Requires Enterprise Support plan (not On-Ramp)

    Support plan feature comparison:

    - **Basic**: Free, limited to documentation and forums
    - **Developer**: $29+/month, business hours email support
    - **Business**: $100+/month, 24/7 phone/chat, full
      Trusted Advisor, Infrastructure Event Management
      (additional fee)
    - **Enterprise On-Ramp**: $5,500+/month, TAM pool,
      Concierge Support team
    - **Enterprise**: $15,000+/month, designated TAM,
      Infrastructure Event Management (included), access
      to Incident Detection and Response (additional fee)

33. **Managing and Rotating Credentials**

    > Which AWS service provides the ability to manage,
    > rotate, and retrieve credentials, such as API keys
    > and database credentials?

    **Answer**: AWS Secrets Manager

    Why not KMS or IAM? The key phrases are "manage,
    rotate, and retrieve credentials" like API keys and
    database passwords. Secrets Manager is purpose-built
    for storing and rotating secrets.

    - **AWS Config** ✗ — Tracks resource configuration
      changes and compliance, has nothing to do with
      credential management
    - **IAM** ✗ — Manages *access permissions* (users,
      roles, policies), not credential storage/rotation.
      IAM can create access keys but doesn't manage
      application secrets like database passwords
    - **KMS** ✗ — Manages *encryption keys* for encrypting
      data, not credentials. KMS creates and controls
      cryptographic keys, not application secrets
    - **Secrets Manager** ✓ — Stores, rotates, and
      retrieves credentials and secrets; integrates with
      RDS for automatic password rotation

    Secrets Manager vs Systems Manager Parameter Store:

    - **Secrets Manager**: Built-in automatic rotation,
      designed for secrets, higher cost, native RDS
      integration
    - **Parameter Store**: Free tier available, stores
      config values and secrets, no built-in rotation
      (requires custom Lambda), simpler and cheaper

34. **Automated Security Assessment for EC2**

    > A user needs an automated security assessment report
    > that identifies unintended network access to Amazon
    > EC2 instances and vulnerabilities on those instances.
    > Which AWS service or feature will provide this
    > assessment report?

    **Answer**: Amazon Inspector

    Why not the other options? The key phrases are
    "automated security assessment," "unintended network
    access," and "vulnerabilities." Inspector is the only
    service that scans for both network exposure and
    software vulnerabilities on EC2 instances.

    - **EC2 security groups** ✗ — Security groups *control*
      network access (firewall rules), but don't generate
      assessment reports or scan for vulnerabilities
    - **AWS Config** ✗ — Tracks resource configuration
      changes and compliance rules, but doesn't perform
      vulnerability scanning or network access analysis
    - **Amazon Macie** ✗ — Discovers sensitive data in S3
      (PII, financial data), has nothing to do with EC2
      vulnerabilities or network access
    - **Amazon Inspector** ✓ — Automated vulnerability
      management that scans EC2 instances, container
      images (ECR), and Lambda functions for software
      vulnerabilities and unintended network exposure

    Inspector vs GuardDuty:

    - **Inspector**: Proactive scanning — finds
      vulnerabilities *before* they're exploited (software
      CVEs, network exposure)
    - **GuardDuty**: Reactive detection — identifies
      threats and malicious activity *as they happen*
      (compromised instances, unusual API calls)

35. **Protecting Web Apps from Common Exploits**

    > Which AWS service protects web applications from
    > common web exploits and vulnerabilities by filtering
    > and monitoring HTTP(S) traffic?

    **Answer**: AWS WAF

    Why not GuardDuty? The key phrases are "web
    applications," "web exploits," and "filtering and
    monitoring HTTP(S) traffic." AWS WAF (Web Application
    Firewall) operates at the application layer (Layer 7)
    and inspects HTTP/HTTPS requests to block common
    attacks like SQL injection, XSS, and other OWASP
    top 10 vulnerabilities.

    - **AWS Artifact** ✗ — Provides access to AWS
      compliance reports and agreements (SOC, PCI, ISO),
      not a security filtering service
    - **Amazon GuardDuty** ✗ — Threat detection service
      that monitors for malicious activity across your
      account (API calls, network flows), but doesn't
      filter HTTP traffic or protect web applications
    - **AWS Config** ✗ — Tracks resource configuration
      changes and compliance, unrelated to web traffic
      filtering
    - **AWS WAF** ✓ — Filters and monitors HTTP(S)
      requests, protects against SQL injection, XSS,
      and other web exploits

    WAF vs Shield vs GuardDuty:

    - **WAF**: Layer 7 protection — filters HTTP(S)
      traffic, blocks web exploits (SQL injection, XSS),
      custom rules, rate limiting
    - **Shield**: DDoS protection — Standard (free,
      automatic) and Advanced (paid, enhanced DDoS
      protection with 24/7 response team)
    - **GuardDuty**: Threat detection — analyzes CloudTrail
      logs, VPC flow logs, and DNS logs to detect
      malicious activity

36. **Multi-Account Environment Setup**

    > Which AWS service provides a simple way to set up a
    > new multi-account AWS environment and govern it at
    > scale?

    **Answer**: AWS Control Tower

    Why not AWS Organizations? This is tricky because
    Organizations *does* manage multiple accounts, but the
    key phrase is "set up a new multi-account environment
    and govern it at scale." Control Tower is the
    higher-level service that automates the setup of a
    well-architected multi-account environment using best
    practices.

    - **AWS Organizations** ✗ — Manages accounts,
      consolidated billing, and SCPs, but doesn't automate
      the *setup* of a landing zone or provide governance
      guardrails out of the box
    - **AWS CloudFormation** ✗ — Infrastructure as code
      for provisioning resources, not designed for
      multi-account governance
    - **AWS Trusted Advisor** ✗ — Provides best practice
      recommendations, but doesn't set up or govern
      multi-account environments
    - **AWS Control Tower** ✓ — Automates setup of a
      secure, well-architected multi-account landing zone
      with pre-configured guardrails

    Control Tower vs Organizations:

    - **Control Tower**: Built *on top of* Organizations;
      automates landing zone setup, provides guardrails
      (preventive and detective), Account Factory for
      provisioning new accounts, dashboard for governance
    - **Organizations**: Foundation layer; manages account
      hierarchy (OUs), consolidated billing, and Service
      Control Policies (SCPs)

37. **Remote EC2 Access Without SSH**

    > A company wants to improve its security and audit
    > posture by limiting Amazon EC2 inbound access. What
    > should the company use to access instances remotely
    > instead of opening inbound SSH ports and managing
    > SSH keys?

    **Answer**: AWS Systems Manager Session Manager

    Why not a bastion host or EC2 Instance Connect? The
    key phrases are "limiting inbound access," "instead of
    opening inbound SSH ports," and "managing SSH keys."
    Session Manager provides secure shell access *without*
    opening any inbound ports or managing SSH keys.

    - **Amazon EC2 Instance Connect** ✗ — Simplifies SSH
      key management by pushing temporary keys, but still
      requires inbound SSH port 22 to be open in the
      security group
    - **Bastion host (jump box)** ✗ — Still requires an
      open inbound SSH port on the bastion, adds another
      instance to manage and secure, and still uses SSH
      keys
    - **Direct Connect** ✗ — Dedicated network connection
      from on-premises to AWS, unrelated to instance
      access management
    - **Systems Manager Session Manager** ✓ — Browser or
      CLI-based shell access with no open inbound ports,
      no SSH keys, full audit logging via CloudTrail

    Session Manager benefits:

    - No inbound security group rules needed (uses the
      SSM agent's outbound HTTPS connection)
    - No SSH keys to create, distribute, or rotate
    - Full session logging to S3 and CloudWatch
    - IAM-based access control (who can start sessions)
    - Audit trail in CloudTrail (who accessed what, when)
    - Port forwarding support without exposing ports

38. **24/7 Cloud Support Engineer Access**

    > A company needs access to cloud support engineers
    > 24 hours a day, 7 days a week. What is the LEAST
    > expensive AWS Support plan that meets this
    > requirement?

    **Answer**: AWS Business Support

    Why not Developer or Enterprise? The key phrases are
    "cloud support engineers," "24/7," and "LEAST
    expensive." Developer Support only provides business
    hours email access. Business Support is the *first*
    tier that offers 24/7 phone, chat, and email access
    to cloud support engineers.

    - **Basic** ✗ — Free, but no access to cloud support
      engineers at all; limited to documentation, forums,
      and service health dashboard
    - **Developer** ✗ — $29+/month, provides access to
      cloud support *associates* (not engineers) via
      email only during business hours
    - **Business** ✓ — $100+/month, first tier with 24/7
      access to cloud support engineers via phone, chat,
      and email
    - **Enterprise On-Ramp** ✗ — $5,500+/month, also
      offers 24/7 engineer access but is far more
      expensive than Business
    - **Enterprise** ✗ — $15,000+/month, also offers
      24/7 engineer access but is the most expensive plan

    Support plan 24/7 access comparison:

    - **Basic**: No technical support
    - **Developer**: Business hours email only (cloud
      support associates)
    - **Business**: 24/7 phone, chat, email (cloud
      support engineers)
    - **Enterprise On-Ramp**: 24/7 phone, chat, email +
      TAM pool
    - **Enterprise**: 24/7 phone, chat, email + designated
      TAM

39. **Rightsizing and Architecture Principles**

    > Which architecture design principle applies to the
    > concept of rightsizing the AWS Cloud infrastructure?

    **Answer**: Optimize cost

    Why not "scale horizontally"? This is tricky because
    scaling and rightsizing both deal with resource
    capacity. However, rightsizing means selecting the
    most appropriate instance type and size for your
    workload to avoid over-provisioning — that's a *cost
    optimization* practice, not a scaling strategy.

    - **Scale horizontally for increased workload** ✗ —
      Reliability/performance principle; adds more
      instances to handle load, but doesn't address
      whether each instance is the right size or type
    - **Optimize cost** ✓ — Rightsizing is a core cost
      optimization practice; it ensures you're not paying
      for resources you don't need by matching instance
      types and sizes to actual workload requirements
    - **Make frequent, small, reversible changes** ✗ —
      Operational Excellence principle; about deploying
      changes safely, unrelated to infrastructure sizing
    - **Perform operations as code** ✗ — Operational
      Excellence principle; about automating
      infrastructure with IaC (CloudFormation, Terraform),
      not about choosing the right resource size

    Rightsizing examples:

    - Downgrading from m5.xlarge to m5.large when CPU
      usage averages 20%
    - Switching from general-purpose to compute-optimized
      instances for CPU-bound workloads
    - Using AWS Cost Explorer rightsizing recommendations
    - Using AWS Compute Optimizer for data-driven
      suggestions

40. **Cost-Effective Instance Types for Base and Peak Traffic**

    > A company has a stateless application that can handle
    > interruptions. The application must be available all
    > day and night. Traffic increases heavily during
    > business hours. Which instance types for base and
    > peak traffic are MOST cost-efficient to meet these
    > requirements?

    **Answer**: Reserved Instances for base traffic and
    Spot Instances for peak traffic using an Amazon EC2
    Fleet

    Why not On-Demand or Reserved for everything? The key
    is matching each traffic pattern to the right pricing
    model. Base traffic is predictable and constant (24/7)
    — perfect for Reserved Instances. Peak traffic is
    variable and the app handles interruptions — perfect
    for Spot Instances.

    - **Reserved Instances for both base and peak** ✗ —
      Reserved Instances have a one-year or three-year
      commitment; not cost-effective for a workload that
      only peaks during business hours
    - **Reserved Instances for base + Spot Instances for
      peak using EC2 Fleet** ✓ — Reserved Instances are
      cost-effective for the steady 24/7 base load; Spot
      Instances give up to 90% discount for the
      interruptible peak traffic; EC2 Fleet manages the
      mix automatically
    - **On-Demand for base + On-Demand with auto scaling
      for peak** ✗ — On-Demand is more expensive than
      both Reserved and Spot; not the most cost-efficient
      option for either base or peak
    - **On-Demand for base + Spot for peak using EC2
      Fleet** ✗ — On-Demand is not cost-effective for
      base traffic that runs 24/7; Reserved Instances
      would be significantly cheaper for that steady load

    EC2 pricing model cheat sheet:

    - **On-Demand**: Pay by the hour/second, no commitment,
      highest price
    - **Reserved**: 1 or 3-year commitment, up to 72%
      discount, best for steady-state workloads
    - **Spot**: Up to 90% discount, can be interrupted,
      best for fault-tolerant/flexible workloads
    - **EC2 Fleet**: Manages a mix of On-Demand, Reserved,
      and Spot instances to meet capacity targets

41. **Security Principles in the Well-Architected Framework**

    > Which of the following are security principles in the
    > AWS Well-Architected Framework? (Select TWO.)

    **Answer**: Monitor, alert, and audit actions and
    changes to AWS resources **AND** Protect data in
    transit and at rest

    Why is this tricky? Each option is a valid
    Well-Architected principle, but they belong to
    *different pillars*. You need to know which principles
    map to the Security pillar specifically.

    - **Analyze and attribute expenditures** ✗ — Cost
      Optimization pillar principle; about tracking and
      understanding where money is spent
    - **Monitor, alert, and audit actions and changes to
      AWS resources** ✓ — Security pillar principle;
      traceability includes monitoring and alerting on
      actions and changes
    - **Deploy globally in minutes** ✗ — Performance
      Efficiency pillar principle; about leveraging
      global infrastructure for low-latency deployments
    - **Protect data in transit and at rest** ✓ — Security
      pillar principle; ensuring encryption and protection
      of data at all stages
    - **Perform operations as code** ✗ — Operational
      Excellence pillar principle; about automating
      infrastructure and operations with IaC

    Security pillar design principles:

    - Implement a strong identity foundation
    - Enable traceability (monitor, alert, audit)
    - Apply security at all layers
    - Automate security best practices
    - Protect data in transit and at rest
    - Keep people away from data
    - Prepare for security events

42. **VPC Service Limit Checks**

    > Which of the following will help a user determine if
    > they need to request a VPC service limit increase?

    **Answer**: AWS Trusted Advisor

    Why not Health Dashboard? This is tricky because
    Health Dashboard monitors AWS service *status and
    health*, not your account's service limits. Trusted
    Advisor is the service that checks your usage against
    service limits and recommends increases.

    - **AWS Health Dashboard** ✗ — Shows the status and
      health of AWS services and reported service-related
      events across Regions; doesn't check your account's
      service limits
    - **AWS Trusted Advisor** ✓ — Performs checks across
      your environment including service limit checks;
      flags when you're approaching VPC limits (and other
      service limits) so you can request an increase
    - **AWS Cost and Usage Report** ✗ — Tracks AWS usage
      and estimated charges; a billing tool, not a service
      limits tool
    - **AWS Service Catalog** ✗ — Lets IT admins create,
      manage, and distribute portfolios of approved
      products to end users; unrelated to service limits

    Trusted Advisor service limit checks include:

    - VPC limits (VPCs per Region, subnets per VPC, etc.)
    - EC2 limits (On-Demand instances, Elastic IPs)
    - EBS limits (volume count, snapshot count)
    - IAM limits (users, groups, roles)
    - RDS limits (DB instances, snapshots)

43. **Shared Responsibility Between User and AWS**

    > Which responsibility is shared between the user and
    > AWS?

    **Answer**: Provide awareness and training

    Why is this tricky? Most Shared Responsibility Model
    questions have clear-cut answers (customer or AWS),
    but this one asks about a *shared* control. AWS trains
    its own employees, and the customer must train their
    own employees — making awareness and training a shared
    responsibility.

    - **Provide a key for Amazon S3 client-side
      encryption** ✗ — Customer responsibility; users
      must provide and maintain their own client-side
      encryption keys, often due to regulatory
      requirements
    - **Configure an Amazon EC2 instance** ✗ — Customer
      responsibility; EC2 is IaaS, so the user handles
      OS patching, security configuration, and management
      of the instance
    - **Control the environment of physical AWS data
      centers** ✗ — AWS responsibility; AWS is responsible
      for the protection of the physical infrastructure
      that runs all cloud services
    - **Provide awareness and training** ✓ — Shared
      control; AWS trains its employees, customers must
      train their own employees

    Shared Responsibility Model categories:

    - **AWS responsibility** ("security *of* the cloud"):
      Physical infrastructure, hardware, networking,
      managed services, data center environment
    - **Customer responsibility** ("security *in* the
      cloud"): Data, IAM, OS/network/firewall config,
      client-side encryption, application security
    - **Shared controls**: Awareness and training, patch
      management (AWS patches infrastructure, customer
      patches guest OS), configuration management

44. **Auto Deployment for Java Web Applications**

    > A company has a Java web application. The company
    > wants to use auto deployment to create the AWS
    > environment and deploy new versions of its
    > application. Which AWS service will meet these
    > requirements?

    **Answer**: AWS Elastic Beanstalk

    Why not Auto Scaling or EC2? The key phrases are
    "auto deployment," "create the AWS environment," and
    "deploy new versions." Elastic Beanstalk is the only
    option that handles both environment creation *and*
    application deployment automatically.

    - **AWS Auto Scaling** ✗ — Adjusts resource capacity
      to meet demand, but doesn't create environments or
      deploy applications; it scales resources that
      already exist
    - **AWS Elastic Beanstalk** ✓ — Upload your code and
      Beanstalk automatically handles deployment,
      capacity provisioning, load balancing, auto scaling,
      and application health monitoring
    - **AWS Control Tower** ✗ — Creates and manages
      multi-account environments following best practices;
      doesn't deploy applications
    - **Amazon EC2** ✗ — Provides scalable compute
      servers, but customers must manually manage
      instances; no automatic deployment functionality

    Elastic Beanstalk key points:

    - Supports Java, .NET, PHP, Node.js, Python, Ruby,
      Go, and Docker
    - PaaS — you manage the code, AWS manages the
      infrastructure
    - Creates and configures EC2, ELB, Auto Scaling, RDS
      under the hood
    - Supports rolling deployments, blue/green deployments
    - No additional charge — you only pay for the
      underlying resources

45. **AWS CAF Operations Perspective Capability**

    > Which option is an AWS Cloud Adoption Framework
    > (AWS CAF) foundational capability for the operations
    > perspective?

    **Answer**: Performance and capacity management

    Why is this tricky? Each option is a valid CAF
    capability, but they belong to *different
    perspectives*. You need to know which capabilities
    map to the Operations perspective specifically.

    - **Performance and capacity management** ✓ —
      Operations perspective capability; focuses on
      monitoring and managing workload performance and
      resource capacity
    - **Application portfolio management** ✗ — Governance
      perspective capability; about managing and
      rationalizing the application portfolio
    - **Identity and access management** ✗ — Security
      perspective capability; about controlling who can
      access what resources
    - **Product management** ✗ — Business perspective
      capability; about managing products and services
      as business offerings

    AWS CAF perspectives and example capabilities:

    - **Business**: Product management, strategic
      partnership, data monetization
    - **People**: Culture evolution, organizational design,
      cloud fluency
    - **Governance**: Application portfolio management,
      program/project management, data governance
    - **Platform**: Platform architecture, data
      architecture, provisioning and orchestration
    - **Security**: Identity and access management,
      vulnerability management, incident response
    - **Operations**: Performance and capacity management,
      event management, incident and problem management

46. **Protecting S3 from Accidental Overwrites or Deletions**

    > Which feature can be used to protect Amazon S3
    > buckets from accidental overwrites or deletions?

    **Answer**: Bucket versioning

    Why not server-side encryption? This is tricky
    because encryption sounds like "protection," but it
    protects data from *unauthorized access*, not from
    accidental overwrites or deletions. Versioning is the
    feature that preserves previous versions so you can
    recover from accidental changes.

    - **Lifecycle policies** ✗ — Sets rules to transition
      objects to different storage classes (e.g., move to
      Glacier after 90 days); does not prevent overwrites
      or deletions
    - **Bucket versioning** ✓ — Maintains multiple
      versions of an object; if you accidentally delete
      or overwrite, S3 inserts a delete marker instead of
      permanently removing the object, allowing you to
      restore previous versions
    - **Server-side encryption** ✗ — Protects data at
      rest by encrypting it, but does not prevent
      overwrites or deletions
    - **S3 access points** ✗ — Simplifies access
      management to a subset of objects in a bucket; does
      not prevent overwrites or deletions

    S3 data protection features:

    - **Versioning**: Recover from accidental deletes and
      overwrites by keeping all object versions
    - **MFA Delete**: Requires MFA to permanently delete
      object versions (extra layer on top of versioning)
    - **Object Lock**: Prevents objects from being deleted
      or overwritten for a fixed period or indefinitely
      (WORM model)
    - **Server-side encryption**: Protects data at rest
      (SSE-S3, SSE-KMS, SSE-C)

47. **Detecting Outages and Redirecting to Alternate Servers**

    > Which AWS service can help a company detect an outage
    > of its website servers and redirect users to
    > alternate servers?

    **Answer**: Amazon Route 53

    Why not CloudFront? This is tricky because CloudFront
    also sits in front of your servers, but it's a CDN
    that caches content at edge locations — it doesn't
    detect outages and redirect to alternate servers.
    Route 53 uses health checks and failover routing
    policies to do exactly that.

    - **Amazon CloudFront** ✗ — CDN that caches static
      and dynamic data at edge locations; reduces latency
      but doesn't redirect users to alternate servers
      during an outage
    - **Amazon GuardDuty** ✗ — Security service that
      monitors for unusual activity and unauthorized
      access; doesn't handle traffic routing or outage
      detection
    - **Amazon Route 53** ✓ — DNS service that resolves
      domain names to IP addresses; supports health
      checks to detect outages and failover routing
      policies to redirect traffic to alternate servers
    - **AWS Trusted Advisor** ✗ — Provides best practice
      recommendations on cost, performance, security, and
      fault tolerance; doesn't redirect traffic

    Route 53 routing policies:

    - **Simple**: Single resource, no health checks
    - **Weighted**: Distribute traffic by percentage
    - **Latency-based**: Route to lowest-latency Region
    - **Failover**: Active-passive setup, redirects to
      standby when primary health check fails
    - **Geolocation**: Route based on user's location
    - **Multivalue answer**: Return multiple healthy IPs

48. **Customer Responsibilities in the Shared Responsibility Model**

    > Which tasks are the customer's responsibility,
    > according to the AWS shared responsibility model?
    > (Select TWO.)

    **Answer**: Patch the guest operating system **AND**
    Configure firewalls

    Why is this tricky? The question mixes physical
    infrastructure tasks (AWS responsibility) with
    logical/software tasks (customer responsibility). The
    rule of thumb: if it involves physical hardware, it's
    AWS; if it involves software configuration on your
    instances, it's you.

    - **Patch the guest operating system** ✓ — Customer
      responsibility; the customer has full responsibility
      to patch operating systems and applications running
      on EC2 instances
    - **Physically secure the data center hardware** ✗ —
      AWS responsibility; customers cannot access data
      centers, so physical security is entirely on AWS
    - **Patch the network hardware** ✗ — AWS
      responsibility; customers have no physical access
      to network hardware, so AWS handles all network
      infrastructure patching
    - **Configure firewalls** ✓ — Customer responsibility;
      customers must configure all firewall rules
      (security groups, NACLs) for their OS and
      applications
    - **Decommission deprecated storage devices** ✗ —
      AWS responsibility; AWS handles all physical
      hardware lifecycle including decommissioning old
      storage devices

    Quick rule for Shared Responsibility questions:

    - **Physical** = AWS (hardware, data centers, network
      infrastructure, storage devices)
    - **Logical/Software** = Customer (OS patching,
      firewall config, IAM, encryption, application code)

49. **Cost Estimation for Future AWS Usage**

    > A company wants to build a data analytics application
    > that uses Amazon Redshift. The company needs a cost
    > estimate for its future Amazon Redshift usage. Which
    > AWS tool will provide a high-level cost estimation?

    **Answer**: AWS Pricing Calculator

    Why not Cost Explorer? This is tricky because both
    deal with costs, but they serve different purposes.
    Cost Explorer analyzes *past and current* usage, while
    Pricing Calculator estimates costs for *future*
    services you haven't deployed yet.

    - **AWS Budgets** ✗ — Sets custom cost and usage
      budgets with alerts when thresholds are exceeded;
      monitors spending, doesn't estimate future costs
    - **AWS Pricing Calculator** ✓ — Creates cost
      estimates for AWS use cases *before* you build;
      lets you model your architecture and get projected
      monthly costs
    - **AWS Cost Explorer** ✗ — Visualizes and analyzes
      *historical* costs and usage over time; great for
      understanding past spending, not for estimating
      future services
    - **Savings Plans** ✗ — Flexible pricing model for
      cost savings on compute usage (EC2, Lambda, Fargate)
      through commitment; not a cost estimation tool

    AWS cost tools cheat sheet:

    - **Pricing Calculator**: Estimate *future* costs
      before deploying
    - **Cost Explorer**: Analyze *past* costs and usage
      trends
    - **Budgets**: Set spending *limits* and get alerts
    - **Cost and Usage Report**: Most *detailed* billing
      data (line-item level)

50. **Visualizing AWS Spending and Usage Over Time**

    > Which AWS service or tool can a company use to
    > visualize, understand, and manage AWS spending and
    > usage over time?

    **Answer**: AWS Cost Explorer

    Why not Budgets? This is tricky because both are cost
    management tools, but they do different things.
    Budgets sets *thresholds and alerts*, while Cost
    Explorer *visualizes and analyzes* spending trends
    over time. The key phrase is "visualize, understand,
    and manage."

    - **AWS Trusted Advisor** ✗ — Analyzes your
      environment and provides best practice
      recommendations (cost, performance, security); not
      a spending visualization tool
    - **Amazon CloudWatch** ✗ — Monitors *performance*
      of AWS workloads (CPU, memory, latency); not
      designed for spending and usage analysis
    - **AWS Cost Explorer** ✓ — Visualizes, understands,
      and manages AWS spending and usage over time with
      graphs, filters, and forecasting
    - **AWS Budgets** ✗ — Sets custom cost and usage
      budgets with alerts when thresholds are exceeded;
      doesn't provide visualization of spending trends

    Cost Explorer vs Budgets:

    - **Cost Explorer**: "Where did my money go?" —
      visualize past spending, filter by service/Region/
      tag, forecast future costs, identify trends
    - **Budgets**: "Tell me when I spend too much" — set
      spending limits, get alerts via email/SNS when
      thresholds are breached, track against planned
      budget

51. **Storing Petabytes of Unstructured Data in a Data Lake**

    > Which storage service provides users with the ability
    > to store petabytes of unstructured data in a data
    > lake on AWS?

    **Answer**: Amazon S3

    Why not Redshift? This is tricky because Redshift is
    commonly associated with big data, but it's a data
    *warehouse* for structured/analytical queries, not a
    data *lake* for unstructured data storage. S3 is the
    foundation of AWS data lakes.

    - **Amazon Elastic Block Store (Amazon EBS)** ✗ —
      Block storage attached to EC2 instances; designed
      for single-instance volumes (like a hard drive),
      not for petabyte-scale data lakes
    - **Amazon Athena** ✗ — Serverless query service that
      *analyzes* data in S3 using SQL; it's a query
      engine, not a storage service
    - **Amazon Redshift** ✗ — Data warehouse for
      structured data and analytical queries (OLAP); not
      designed for storing unstructured data in a data
      lake
    - **Amazon S3** ✓ — Object storage with virtually
      unlimited capacity; stores any type of data
      (structured, semi-structured, unstructured) at
      petabyte scale and is the standard foundation for
      AWS data lakes

    Data lake vs Data warehouse:

    - **Data lake (S3)**: Stores raw data in any format
      (JSON, CSV, Parquet, images, video, logs);
      schema-on-read; petabyte scale; cheap storage
    - **Data warehouse (Redshift)**: Stores processed,
      structured data; schema-on-write; optimized for
      complex analytical SQL queries

52. **Linking AWS to On-Premises Without the Internet**

    > Which AWS service or feature helps link a company's
    > AWS environment directly to the company's internal
    > network without using the internet?

    **Answer**: AWS Direct Connect

    Why not Amazon Connect? This is a classic naming trap.
    Amazon Connect is a cloud *contact center* service
    (phone/chat support), not a networking service. AWS
    *Direct* Connect is the dedicated network connection.

    - **AWS Direct Connect** ✓ — Establishes a dedicated,
      private network connection from on-premises to AWS;
      bypasses the public internet for more consistent
      performance, lower latency, and increased security
    - **Amazon Connect** ✗ — Cloud-based contact center
      service for customer support (voice, chat); has
      nothing to do with network connectivity
    - **VPC peering** ✗ — Connects two VPCs together
      within AWS (same or cross-account/Region); doesn't
      connect on-premises networks to AWS
    - **An egress-only internet gateway** ✗ — Allows
      outbound IPv6 traffic from a VPC to the internet
      while preventing inbound connections; still uses the
      internet

    On-premises to AWS connectivity options:

    - **AWS Direct Connect**: Dedicated private connection,
      consistent bandwidth, lowest latency, higher cost
    - **AWS Site-to-Site VPN**: Encrypted tunnel over the
      public internet, quick to set up, lower cost than
      Direct Connect
    - **Direct Connect + VPN**: Combines both for an
      encrypted connection over a dedicated link

## References

- [AWS Cloud Practitioner - YouTube Playlist](https://www.youtube.com/playlist?list=PL7Jj8Ba9Yr6AlmnfXo_UwoLF_CG5SP_mH)
