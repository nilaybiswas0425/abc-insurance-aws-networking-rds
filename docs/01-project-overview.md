# 01 – Project Overview

## 1. Business Scenario

ABC Insurance is a fictional insurance company that provides insurance products and services to individual customers.

As the company grows, it needs a reliable and secure cloud environment to manage its business data and support future analytics and Artificial Intelligence (AI) applications.

The company maintains several categories of information:

| Data Category | Examples | Data Type |
|---|---|---|
| Customer Information | Customer ID, name, address, contact details | Structured |
| Insurance Policies | Policy number, coverage, premium, expiry date | Structured |
| Claims | Claim ID, policy number, claim amount, claim status | Structured |
| Payments | Payment ID, premium amount, payment date | Structured |
| Policy Documents | Policy PDFs, terms and conditions | Unstructured |
| Claim Documents | Claim forms, supporting documents, reports | Unstructured |

The company wants to explore AWS services that can securely store, manage, and process these different types of data.

## 2. Business Challenges

ABC Insurance has identified the following technical requirements:

### 2.1 Secure Network Infrastructure

The company needs an isolated cloud network where application components and databases can communicate securely.

Requirements:

- Establish a dedicated Amazon VPC.
- Separate public-facing and private resources.
- Deploy resources across multiple Availability Zones.
- Control network traffic using security groups and network ACLs.
- Restrict direct internet access to databases.

### 2.2 Relational Database Management

The company needs a relational database to manage structured business information.

Requirements:

- Store customer, policy, claim, and payment records.
- Support SQL queries and relational operations.
- Understand database storage and compute configuration.
- Explore automated backups and point-in-time recovery.
- Understand Multi-AZ deployment and read replicas.
- Compare Amazon RDS with Amazon Aurora.

### 2.3 Automation

The company wants to reduce manual infrastructure configuration.

Requirements:

- Explore resources through the AWS Management Console.
- Recreate configurations using Python Boto3.
- Understand AWS API operations.
- Validate resource configurations programmatically.
- Follow secure credential-management practices.

### 2.4 Future AI Integration

ABC Insurance plans to introduce Generative AI capabilities.

Potential use cases:

- Answer questions about insurance policies.
- Retrieve customer policy information.
- Summarize claim records.
- Retrieve information from insurance documents.
- Generate contextual responses using company data.

The architecture will be explored to understand how relational databases, object storage, vector search, and foundation models can work together.

## 3. Project Objectives

The main objectives are:

1. Understand AWS networking fundamentals through practical implementation.
2. Design a dedicated V
