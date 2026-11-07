# Module 02 – Amazon RDS for PostgreSQL

**Project:** ABC Insurance  
**AWS Region:** `us-east-1`  
**Status:** RDS created; private connectivity tested successfully  
**Date:** November 6, 2026

## 1. Objective

This module explores how Amazon RDS for PostgreSQL supplies structured operational data to an insurance application and, later, to a Generative AI application.

The focus is AWS configuration, private connectivity, security, operations, and application access—not deep PostgreSQL development.

In the planned ABC Insurance AI assistant:

- **Amazon RDS for PostgreSQL** stores structured operational records such as customers, policies, claims, and payments.
- **Amazon S3 and an Amazon Bedrock Knowledge Base** provide retrieval from unstructured insurance policy documents, coverage terms, and exclusions.
- **A Python application/orchestration layer** retrieves information from both sources and supplies relevant context to an Amazon Bedrock model.

RDS does not automatically connect to a Bedrock Knowledge Base. The application coordinates the two retrieval paths.

## 2. Network architecture

The RDS database is deployed in the dedicated VPC created in Module 01.

| Resource | Configuration |
|---|---|
| VPC | `abc-insurance-vpc` |
| VPC ID | `vpc-05dcc05b6e590fbf1` |
| VPC CIDR | `10.0.0.0/16` |
| DB subnet group | `abc-insurance-db-subnet-group` |
| Private DB subnet A | `10.0.20.0/24` – `us-east-1a` |
| Private DB subnet B | `10.0.21.0/24` – `us-east-1b` |
| RDS security group | `abc-insurance-rds-sg` |
| Application security group | `abc-insurance-app-sg` |

The DB subnet group contains two private subnets in separate Availability Zones. A DB subnet group defines the subnets/AZs where RDS can place a database. It does not, by itself, make the database Multi-AZ.

The RDS console confirmed **Publicly accessible: No**. Applications should use the RDS DNS endpoint rather than a hard-coded IP address.

## 3. RDS instance configuration

The following settings were selected or observed during creation. Items marked **Verify** should be checked in the live console before treating them as final inventory.

| Setting | Value |
|---|---|
| DB instance identifier | `abc-insurance-database` |
| Engine | Amazon RDS for PostgreSQL (standard RDS, not Aurora) |
| Engine version | PostgreSQL `17.7-R1` |
| Template | Sandbox |
| Master username | `abc_insurance` |
| Instance class | `db.t4g.micro` |
| vCPU / memory shown | 2 vCPU / 1 GiB |
| Allocated storage | 20 GiB |
| Storage type | Verify in RDS console |
| Network type | IPv4 |
| VPC | `abc-insurance-vpc` |
| DB subnet group | `abc-insurance-db-subnet-group` |
| Publicly accessible | No |
| Port | `5432` |
| Availability deployment | Verify whether the created instance is Single-AZ or Multi-AZ |
| Encryption | Enabled; AWS-managed `aws/rds` KMS key selected |
| Parameter group | `default.postgres17` |
| Option group | `default:postgres-17` |
| Certificate authority | `rds-ca-rsa2048-g1` |
| Automated backups | Enabled; 1-day retention selected |
| Backup window | No preference |
| Copy tags to snapshots | Enabled |
| Backup replication | Disabled |
| Automatic minor version upgrade | Enabled |
| Deletion protection | Disabled |
| RDS Proxy | Not enabled |
| Extended Support | Checkbox left unchecked |
| Authentication | Password authentication |
| Credentials management | Self-managed |

## 4. Connectivity and security

The RDS console showed:

- VPC: `abc-insurance-vpc`
- DB subnet group: `abc-insurance-db-subnet-group`
- Port: `5432`
- Publicly accessible: `No`
- Security group: `abc-insurance-rds-sg`

This keeps the database off the public internet. A client needs a private network path into the VPC and permission from the relevant security groups.

### Security group rule

The RDS security group initially had no inbound rules. After creating the CloudShell VPC environment, one inbound rule was added:

| Type | Protocol | Port | Source |
|---|---|---:|---|
| PostgreSQL | TCP | 5432 | `abc-insurance-app-sg` |

No `0.0.0.0/0` inbound rule was added.

The application security group was selected for the CloudShell VPC environment. The exact CloudShell subnet selection should be confirmed from the environment details and recorded in the final inventory.

Security groups are stateful. The RDS inbound rule permits PostgreSQL traffic from network interfaces associated with the application security group. The application's outbound rules must also permit the connection; its default allow-all outbound rule was retained.

## 5. CloudShell VPC connectivity test

A standard CloudShell session was inspected first. Its route output included:

```text
169.254.255.192/26 dev eth0 proto kernel scope link src 169.254.255.194
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
```

These addresses did not establish that standard CloudShell was attached to the project VPC. A **CloudShell VPC environment** was then created and configured for the project VPC, using `abc-insurance-app-sg`.

The CloudShell environment already had the PostgreSQL command-line client:

```text
psql (PostgreSQL) 16.15
```

Using the RDS endpoint, port `5432`, username `abc_insurance`, and the interactive password prompt, the connection succeeded and displayed:

```text
postgres=>
```

This verified that the selected CloudShell VPC environment could reach the private RDS endpoint and authenticate.

### PostgreSQL server version

The following query was run:

```sql
SELECT version();
```

Observed result:

```text
PostgreSQL 17.7 on aarch64-unknown-linux-gnu, compiled by aarch64-unknown-linux-gnu-gcc (GCC) 12.4.0, 64-bit
```

The CloudShell `psql` client reported version 16.15 while the RDS server reported version 17.7. The connection succeeded.

### Current database and user

```sql
SELECT current_database();
```

Result: `postgres`

```sql
SELECT current_user;
```

Result: `abc_insurance`

The connection was authenticated as `abc_insurance` and was initially connected to the default `postgres` database.

### Database inventory and application database

The `\l` psql meta-command returned an error because its generated catalog query referenced `d.daticulocale`, a column not present on the server. A direct SQL query successfully listed the databases:

```sql
SELECT datname FROM pg_database;
```

Observed databases:

```text
template0
template1
postgres
rdsadmin
abc_insurance
```

The `abc_insurance` database was created inside the existing RDS instance. This is a logical PostgreSQL database, not a second RDS instance.

Important distinction:

- `abc-insurance-database` — the AWS RDS DB instance (managed PostgreSQL server).
- `abc_insurance` — a PostgreSQL database hosted inside that RDS instance.
- `abc_insurance` — also the PostgreSQL login username. A database and a user/role are separate objects even when they share a name.

Next, switch the current `psql` session to the application database:

```text
\c abc_insurance
```

Then verify:

```sql
SELECT current_database(), current_user;
```


## 6. Role of RDS in the Generative AI application

The intended retrieval flow is:

1. An employee asks the ABC Insurance AI assistant a question.
2. The Python application identifies the structured facts needed.
3. The application queries RDS for operational records such as customer, claim, policy status, or payment information.
4. The application retrieves relevant policy-document passages from the Amazon Bedrock Knowledge Base backed by S3 documents.
5. The application combines the structured records and retrieved document evidence as context for the model.
6. Amazon Bedrock generates a response grounded in both sources.

RDS is the source for structured operational facts. The Knowledge Base is the retrieval source for unstructured document content. The application/orchestration layer connects the two.



## 9. Suggested screenshots

Store screenshots under `screenshots/02-rds/`:

```text
screenshots/
└── 07-rds/
    ├── 01-db-subnet-group-created.png
```
