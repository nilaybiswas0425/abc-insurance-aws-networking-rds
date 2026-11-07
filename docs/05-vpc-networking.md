# ABC Insurance – VPC Networking

## Overview

Dedicated AWS network foundation for the ABC Insurance application and PostgreSQL database.

| Property | Configuration |
|---|---|
| AWS Region | `us-east-1` (US East – N. Virginia) |
| VPC name | `abc-insurance-vpc` |
| VPC ID | `vpc-05dcc05b6e590fbf1` |
| IPv4 CIDR | `10.0.0.0/16` |
| Availability Zones | `us-east-1a`, `us-east-1b` |
| DNS resolution | Enabled |
| DNS hostnames | Enabled |

The VPC is dedicated to this project and is separate from the AWS default VPC.

## Subnet design

Four `/24` subnets are distributed across two Availability Zones.

| Subnet | Availability Zone | CIDR | Type | Auto-assign public IPv4 |
|---|---|---|---|---|
| `abc-public-subnet-a` | `us-east-1a` | `10.0.1.0/24` | Public | Yes |
| `abc-public-subnet-b` | `us-east-1b` | `10.0.2.0/24` | Public | Yes |
| `abc-private-db-subnet-a` | `us-east-1a` | `10.0.20.0/24` | Private DB | No |
| `abc-private-db-subnet-b` | `us-east-1b` | `10.0.21.0/24` | Private DB | No |

Public IPv4 auto-assignment is enabled for the public subnets and disabled for the database subnets. A subnet's public/private routing classification is determined by its route table, not by this setting alone.

## Internet Gateway

| Property | Configuration |
|---|---|
| Name | `abc-insurance-igw` |
| Internet Gateway ID | `igw-05cd11b60d8f545a8` |
| Attachment | Attached to `abc-insurance-vpc` |

The Internet Gateway provides internet connectivity for resources in public subnets when their addressing and security configuration also permit it.

## Route tables

### Public route table

| Property | Configuration |
|---|---|
| Name | `abc-public-rt` |
| Route table ID | `rtb-00944d13250ab8b2d` |
| Associated subnets | `abc-public-subnet-a`, `abc-public-subnet-b` |

| Destination | Target |
|---|---|
| `10.0.0.0/16` | `local` |
| `0.0.0.0/0` | `abc-insurance-igw` |

The default route sends traffic destined outside the VPC to the Internet Gateway.

### Private route table

| Property | Configuration |
|---|---|
| Name | `abc-private-rt` |
| Route table ID | `rtb-0be4c76edb7f70485` |
| Associated subnets | `abc-private-db-subnet-a`, `abc-private-db-subnet-b` |

| Destination | Target |
|---|---|
| `10.0.0.0/16` | `local` |

No Internet Gateway or NAT Gateway route is configured. The database subnets have VPC-local routing only.

### Main route table

The VPC's automatically created main route table remains in place. All four subnets have explicit associations with the dedicated public or private route table, so the main route table has no subnet associations.

## Network ACL

| Property | Configuration |
|---|---|
| NACL ID | `acl-0993ffa67e708dbd9` |
| Default NACL | Yes |
| Associated subnets | All four subnets |

The default NACL is retained without modification.

| Direction | Rule | Traffic | CIDR | Action |
|---|---:|---|---|---|
| Inbound | 100 | All traffic | `0.0.0.0/0` | Allow |
| Inbound | `*` | All traffic | `0.0.0.0/0` | Deny |
| Outbound | 100 | All traffic | `0.0.0.0/0` | Allow |
| Outbound | `*` | All traffic | `0.0.0.0/0` | Deny |

NACLs are stateless. The default NACL is permissive; subnet-level filtering has not been tightened in this learning environment. Security Groups provide the primary resource-level access controls in this design.

## Security Groups

Two dedicated Security Groups were created in `abc-insurance-vpc`.

| Security Group | ID | Purpose |
|---|---|---|
| `abc-insurance-rds-sg` | `sg-04c1019444daf16bd` | Intended for the PostgreSQL RDS database |
| `abc-insurance-app-sg` | `sg-05791f2eb453c60ea` | Intended for application resources |

Current rules:

| Security Group | Inbound | Outbound |
|---|---|---|
| `abc-insurance-rds-sg` | None | Default allow-all |
| `abc-insurance-app-sg` | None | Default allow-all |

No inbound rule exposes PostgreSQL port `5432` to the internet. The RDS inbound rule will be added later to allow traffic from the application Security Group when the application connectivity design is implemented.

Security Groups are stateful. Return traffic for permitted connections is automatically allowed.

## DNS settings

| VPC setting | Final value | Reason |
|---|---|---|
| DNS resolution | Enabled | Allows DNS name resolution within the VPC |
| DNS hostnames | Enabled | Supports DNS hostnames for eligible resources |

These settings support hostname-based connectivity, including use of an RDS DNS endpoint rather than a hardcoded database IP address.

## Final architecture

```text
                         ABC INSURANCE VPC
                            10.0.0.0/16
                                  |
                    +-------------+-------------+
                    |                           |
             abc-public-rt               abc-private-rt
              IGW + local                  local only
                    |                           |
          +---------+---------+       +---------+---------+
          |                   |       |                   |
     Public subnet A     Public subnet B  Private DB A   Private DB B
      10.0.1.0/24         10.0.2.0/24     10.0.20.0/24  10.0.21.0/24
       us-east-1a          us-east-1b      us-east-1a     us-east-1b
          |                   |                |              |
          +---------+---------+                +-------+------+
                    |                                  |
              Internet Gateway                   RDS Security
                                                Group (planned
                                                DB attachment)
```

The public subnets have a route to the Internet Gateway. The private database subnets do not have a direct internet route. No NAT Gateway has been created.

## Cost and deployment notes

- The VPC, subnets, route tables, route-table associations, Internet Gateway, Network ACLs, and Security Groups do not have an hourly resource charge.
- NAT Gateway, data transfer, public IPv4 addresses, and future compute/database resources can incur charges.
- No NAT Gateway or RDS database instance is included in this networking module.
- Review current AWS pricing before creating chargeable resources.

## Screenshots

Store the console screenshots in the repository using this structure. Update filenames if your local names differ.

```text
screenshots/
├── 01-vpc/
│   ├── 01-vpc-details.png
│   └── 06-vpc-resource-map.png
├── 02-subnets/
│   ├── 01-public-subnet-a.png
│   ├── 02-public-subnet-b.png
│   ├── 03-private-db-subnet-a.png
│   └── 04-private-db-subnet-b.png
├── 03-internet-gateway/
│   └── 01-internet-gateway-attached.png
├── 04-routing/
│   ├── 01-public-route-table.png
│   ├── 02-public-subnet-associations.png
│   ├── 03-private-route-table.png
│   └── 04-private-subnet-associations.png
└── 05-security/
    ├── 01-rds-security-group.png
    ├── 02-application-security-group.png
    ├── 03-network-acl-inbound.png
    └── 04-network-acl-outbound.png
```

The screenshots document the AWS Console configuration at the time this module was completed. Review and update this document if the infrastructure changes.
