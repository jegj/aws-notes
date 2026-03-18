# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code)
when working with code in this repository.

## Project Overview

This is an AWS certification study notes repository. It is
a pure documentation/knowledge base project with no
application code, build system, tests, or CI/CD pipeline.
The site is published to GitHub Pages using Jekyll with the
`just-the-docs` theme.

## Structure

```
README.md                          # Index linking to each certification doc
index.md                           # Jekyll home page (includes README.md)
_config.yml                        # Jekyll + just-the-docs configuration
docs/
  cloud-practitioner.md            # AWS Cloud Practitioner CLF-C02 notes
  solutions-architect-associate.md # AWS Solutions Architect Associate SAA-C03 notes
```

Each file in `docs/` starts with Jekyll front matter (`title`, `nav_order`)
so `just-the-docs` registers it in the sidebar navigation.

### `docs/cloud-practitioner.md` topics

- **Domain 1 - Cloud Concepts**: Cloud value proposition,
  deployment models (IaaS/PaaS/SaaS), Global Infrastructure
  (Regions, AZs, Edge Locations), Well-Architected Framework,
  Cloud Adoption Framework (CAF)
- **Domain 2 - Security and Compliance**: Shared
  Responsibility Model, IAM, Security Services (WAF, Shield,
  GuardDuty, Inspector, Macie, Detective, Security Hub),
  Encryption and Key Management (KMS, CloudHSM, ACM),
  Compliance and Audit (Artifact, CloudTrail, Config, Cognito),
  Security Groups and NACLs
- **Domain 3 - Cloud Technology and Services**: Compute (EC2,
  Lambda, ECS/EKS/Fargate, Beanstalk, Lightsail, Batch),
  Storage (S3, EBS, EFS, FSx, Snow Family), Databases (RDS,
  Aurora, DynamoDB, Redshift, and more), Networking (VPC,
  Route 53, CloudFront, ELB, API Gateway, Global Accelerator),
  Application Integration (SQS, SNS, EventBridge), Management
  (CloudWatch, CloudFormation, Systems Manager, Auto Scaling,
  Organizations, Trusted Advisor), ML/AI Services, ETL/Data
  Integration, Hybrid Connectivity
- **Domain 4 - Billing, Pricing, and Support**: Pricing
  models, Free Tier, Cost Explorer, Budgets, Cost and Usage
  Report, Pricing Calculator, Consolidated Billing, Support
  Plans

### `docs/solutions-architect-associate.md` topics

Work in progress.

## Conventions

- Each file in `docs/` must start with Jekyll front matter
  (`title`, `nav_order`) — do not remove it
- Content is written in GitHub-flavored Markdown
- Each major AWS topic gets its own `##` heading
- Subtopics use nested headings, bullet points, and numbered lists
- Include practical details: features, use cases, pricing, and comparisons

## Adding new questions

- Whenever a picture is shared to get the conent of the question, always include the options and mark the valid answer
