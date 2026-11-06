# 04 – Subnet Design

## 1. Overview

ABC Insurance will use four subnets within its dedicated VPC, distributed across two Availability Zones in AWS Region `us-east-1`.

The design separates public-facing resources from private database resources.

## 2. Proposed Subnet Configuration

| Subnet Name | Type | CIDR | Availability Zone |
|---|---|---|---|
| abc-public-subnet-a | Public | 10.0.1.0/24 | AZ A |
| abc-public-subnet-b | Public | 10.0.2.0/24 | AZ B |
| abc-private-db-subnet-a | Private | 10.0.20.0/24 | AZ A |
| abc-private-db-subnet-b | Private | 10.0.21.0/24 | AZ B |

Each `/24` subnet provides 256 total IPv4 addresses, of which 251 are usable in AWS.

## 3. Design Decisions

| Decision | Reason |
|---|---|
| Two public subnets | Future application and internet-facing components |
| Two private subnets | Host database resources |
| Two Availability Zones | Support multi-AZ architecture |
| `/24` CIDR | Simple address allocation with sufficient capacity |
| Separate route tables | Control public and private traffic independently |
| No NAT Gateway initially | Avoid unnecessary costs |

## 4. RDS Placement

The two private database subnets will be included in a DB subnet group:

`abc-insurance-db-subnet-group`

This provides RDS with subnet placement options across two Availability Zones.

The RDS database will be configured as **not publicly accessible**.

Note: A DB subnet group does not automatically enable Multi-AZ. That is a separate RDS deployment setting.

## 5. Planned Network Layout

```text
ABC Insurance VPC
10.0.0.0/16
│
├── Availability Zone A
│   ├── Public:  10.0.1.0/24
│   └── Private: 10.0.20.0/24
│
└── Availability Zone B
    ├── Public:  10.0.2.0/24
    └── Private: 10.0.21.0/24
```

## 6. Implementation Evidence

Screenshots to capture after implementation:

- `01-subnet-list.png`
- `02-public-subnet-details.png`
- `03-private-subnet-details.png`
- `04-availability-zone-distribution.png`
