# 03 – VPC Design and CIDR Planning

## 1. Introduction

Before creating an Amazon VPC, we need to determine its IPv4 address range and plan how that range will be divided into smaller subnet networks.

This process is called CIDR (Classless Inter-Domain Routing) planning.

For the ABC Insurance project, we will create a dedicated VPC with sufficient address space for the initial networking and database requirements, while leaving room for future expansion.

## 2. VPC CIDR Selection

### 2.1 Proposed Configuration

| Setting | Value |
|---|---|
| VPC Name | abc-insurance-vpc |
| AWS Region | us-east-1 |
| IPv4 CIDR | 10.0.0.0/16 |
| IPv6 CIDR | None initially |
| Tenancy | Default |

### 2.2 Why 10.0.0.0/16?

The CIDR block `10.0.0.0/16` provides 65,536 total IPv4 addresses.

The calculation is:

- IPv4 addresses contain 32 bits.
- The `/16` prefix uses 16 bits for the network.
- The remaining 16 bits are available for host addressing.

Therefore:

`2^(32 - 16) = 65,536`

The complete address range is:

`10.0.0.0 – 10.0.255.255`

This provides sufficient space for our initial subnets and future network expansion.

## 3. Understanding CIDR Notation

CIDR notation identifies how much of an IP address belongs to the network portion.

| CIDR | Host bits | Total addresses | AWS usable addresses in a subnet |
|---|---:|---:|---:|
| /16 | 16 | 65,536 | Not applicable to VPC as a whole |
| /20 | 12 | 4,096 | 4,091 |
| /24 | 8 | 256 | 251 |
| /25 | 7 | 128 | 123 |
| /26 | 6 | 64 | 59 |

AWS reserves five IPv4 addresses in each subnet:

1. Network address.
2. VPC router address.
3. DNS address.
4. Future use.
5. Broadcast address (reserved by AWS, although AWS does not support broadcast traffic).

For example, a `/24` subnet contains 256 total addresses, of which 251 are usable by AWS resources.

## 4. Subnet CIDR Allocation

The proposed VPC will initially contain four subnets.

| Subnet Name | Purpose | CIDR | Total Addresses | AWS Usable |
|---|---|---|---:|---:|
| abc-public-subnet-a | Public resources | 10.0.1.0/24 | 256 | 251 |
| abc-public-subnet-b | Public resources | 10.0.2.0/24 | 256 | 251 |
| abc-private-db-subnet-a | Database resources | 10.0.20.0/24 | 256 | 251 |
| abc-private-db-subnet-b | Database resources | 10.0.21.0/24 | 256 | 251 |
| **Total** | | | **1,024** | **1,004** |

The four subnets use 1,024 addresses from the VPC's 65,536-address range.

This leaves 64,512 addresses outside these four subnet allocations for potential future subnet planning.

Note: The remaining addresses are not automatically assigned to any subnet. They must be allocated through valid, non-overlapping CIDR blocks.

## 5. Visualizing the IP Address Allocation

The following diagram shows the proposed subnet ranges within the VPC.

```text
ABC INSURANCE VPC
10.0.0.0/16
Range: 10.0.0.0 - 10.0.255.255
Total: 65,536 addresses

|
|-- 10.0.1.0/24
|      Public Subnet A
|
|-- 10.0.2.0/24
|      Public Subnet B
|
|-- 10.0.20.0/24
|      Private DB Subnet A
|
|-- 10.0.21.0/24
|      Private DB Subnet B
|
|-- Remaining address space
       Reserved for future subnet planning
```

## 6. Why Choose /24 for Our Subnets?

A `/24` subnet provides 256 total addresses and 251 AWS-usable addresses.

For this learning project, it offers:

- Sufficient address space for the initial exercises.
- Easy-to-understand IP range calculations.
- Consistent subnet sizing.
- Room for additional resources without immediately requiring a larger subnet.

The `/24` choice is a design decision for this project, not an AWS requirement.

Smaller or larger subnet sizes can be appropriate depending on workload and resource requirements.

## 7. Availability Zone Allocation

The four subnets will be distributed across two Availability Zones in `us-east-1`.

| Subnet | Planned Availability Zone |
|---|---|
| Public Subnet A | AZ A |
| Private DB Subnet A | AZ A |
| Public Subnet B | AZ B |
| Private DB Subnet B | AZ B |

We will select two available Availability Zones when implementing the design.

The actual Availability Zone names and IDs will be recorded during implementation.

Using two AZs allows the architecture to support multi-AZ database configurations and future application resilience.

## 8. CIDR Overlapping Considerations

When designing networks, we must ensure that subnet CIDR ranges do not overlap.

For example:

**VPC A**

`10.0.0.0/16`

Address range:

`10.0.0.0 – 10.0.255.255`

**VPC B**

`10.0.10.0/24`

Address range:

`10.0.10.0 – 10.0.10.255`

These networks overlap because the entire VPC B address range is contained within VPC A.

Overlapping CIDR ranges can prevent normal private routing between VPCs through VPC peering.

For this project, we will keep all subnet CIDR blocks within the VPC's address range and ensure that no subnet overlaps another.

## 9. Future Network Expansion

The `/16` VPC allocation provides room for future expansion.

Potential future subnet allocations include:

| Purpose | Example CIDR |
|---|---|
| Application Subnet A | 10.0.30.0/24 |
| Application Subnet B | 10.0.31.0/24 |
| Additional Private Subnet A | 10.0.40.0/24 |
| Additional Private Subnet B | 10.0.41.0/24 |

These are reserved planning examples, not actual subnet allocations.

Before creating any additional subnet, we will verify that its CIDR does not overlap an existing subnet.

## 10. Implementation Validation Checklist

After creating the VPC and subnets, we will validate:

- [ ] VPC has the expected CIDR block.
- [ ] All subnet CIDRs fall within the VPC CIDR range.
- [ ] No subnet CIDR ranges overlap.
- [ ] Subnets are distributed across two Availability Zones.
- [ ] Each subnet has the expected address capacity.
- [ ] AWS-reserved IPv4 addresses are understood.
- [ ] Actual subnet IDs and AZ details are documented.

## 11. Screenshots and Evidence

The following screenshots will be added after implementation:

| Screenshot | Evidence |
|---|---|
| 01-vpc-details.png | VPC CIDR and configuration |
| 02-subnet-list.png | All four subnet CIDRs |
| 03-subnet-details.png | Individual subnet and AZ information |

Sensitive account information will be redacted before publishing screenshots.

## 12. Project Status

**Design status:** Proposed.

**Implementation status:** Not yet created.

**Next milestone:** Create the ABC Insurance VPC and validate its CIDR configuration through the AWS Management Console.
