# aws-notes

Notes about the AWS certifications

## AWS Cloud Practitioner Certification - Basic Knowledge

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
