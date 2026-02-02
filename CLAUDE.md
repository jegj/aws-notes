# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code)
when working with code in this repository.

## Project Overview

This is an AWS certification study notes repository. It is
a pure documentation/knowledge base project with no
application code, build system, tests, or CI/CD pipeline.

## Structure

All content lives in `README.md` as structured Markdown,
organized by AWS certification topic (currently focused on
AWS Cloud Practitioner CLF-C02). Topics covered include:

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

## Conventions

- Content is written in GitHub-flavored Markdown
- Each major AWS topic gets its own top-level heading (`##`)
- Subtopics use nested headings, bullet points, and numbered lists
- Include practical details: features, use cases, pricing, and comparisons
