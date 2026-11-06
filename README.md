# ABC Insurance – AWS VPC & RDS Exploration

## 1. Project Overview

This project explores the design, configuration, and automation of AWS networking and relational database services using the AWS Management Console and Python Boto3.

The project uses a fictional insurance company, **ABC Insurance**, as a business scenario.

ABC Insurance needs a secure cloud environment to manage:

- Customer information
- Insurance policies
- Claims and payment records
- Structured data used by downstream analytics and AI applications

The objective is to understand how AWS networking and database services work together, rather than simply deploying an end-to-end application.

## 2. Project Objectives

1. Understand Amazon VPC architecture and networking fundamentals.
2. Design public and private subnets across multiple Availability Zones.
3. Configure Internet Gateways, route tables, security groups, and network ACLs.
4. Understand RDS architecture, database engines, storage, backups, and high availability.
5. Explore Amazon Aurora and its differences from standard RDS.
6. Automate AWS resource configuration using Python Boto3.
7. Understand how relational databases support modern AI and Generative AI architectures.
8. Monitor AWS resource usage and control learning costs.

## 3. Technology Stack

| Technology | Purpose |
|---|---|
| Amazon VPC | Isolated cloud network |
| AWS Subnets | Network segmentation |
| Internet Gateway | Internet connectivity |
| Route Tables | Network traffic routing |
| Security Groups | Resource-level network security |
| Network ACLs | Subnet-level network security |
| Amazon RDS | Managed relational database |
| Amazon Aurora | Cloud-optimized relational database |
| Python | Automation and database interaction |
| Boto3 | AWS SDK for Python |
| AWS Management Console | Visual configuration and exploration |

## 4. Initial Network Design

| Component | Planned configuration |
|---|---|
| VPC name | abc-insurance-vpc |
| IPv4 CIDR | 10.0.0.0/16 |
| Public subnet A | 10.0.1.0/24 |
| Public subnet B | 10.0.2.0/24 |
| Private DB subnet A | 10.0.20.0/24 |
| Private DB subnet B | 10.0.21.0/24 |
| Availability Zones | Two AZs in us-east-1 |

*Note: This is the proposed design. Actual configurations and resource IDs will be documented after implementation.*

## 5. Learning Approach

Each AWS component will be explored using the following process:

1. Understand the concept and business use case.
2. Document the architecture and design decisions.
3. Configure the resource using the AWS Console.
4. Capture screenshots as implementation evidence.
5. Recreate the configuration using Python Boto3.
6. Validate the configuration.
7. Document observations, challenges, and lessons learned.

## 6. Project Documentation

Detailed documentation will be maintained under the `docs/` directory.

- Project overview
- VPC architecture and CIDR planning
- Subnet design
- Internet Gateway and routing
- Security groups and network ACLs
- RDS architecture and configuration
- Aurora exploration
- Cost management
- Lessons learned

## 7. Cost Management

This is a learning project with a target AWS experiment budget of approximately **USD $30 per month**.

Resources will be created only when required for hands-on exercises. Chargeable services will be monitored and cleaned up after testing.

## 8. Security Practices

- AWS credentials will never be committed to GitHub.
- Sensitive resource details will be redacted from public screenshots.
- IAM permissions will follow least-privilege principles wherever practical.
- Databases will use private networking where appropriate.

## 9. Project Status

**Status:** Planning and documentation

Implementation will be updated as each learning milestone is completed.
