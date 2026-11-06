# 05 – VPC Creation and Configuration

## 1. Overview

Created a dedicated Amazon VPC for the ABC Insurance networking project in AWS Region `us-east-1`.

The VPC will serve as the network boundary for future application and database resources.

## 2. VPC Configuration

| Property | Actual Configuration |
|---|---|
| VPC Name | abc-insurance-vpc |
| IPv4 CIDR | 10.0.0.0/16 |
| IPv6 CIDR | Not configured |
| State | Available |
| Default VPC | No |
| Tenancy | Default |
| DNS Resolution | Enabled |
| DNS Hostnames | Disabled at initial creation- |

**VPC ID:** Recorded in AWS Console.

## 3. Configuration Decisions

- Created a dedicated VPC instead of modifying the default VPC.
- Selected `/16` to provide sufficient IP address space for future expansion.
- Used default tenancy.
- Did not configure IPv6 for this initial exercise.
- Retained DNS resolution enabled.

DNS hostnames were disabled at initial creation. They will be reviewed and enabled if required for subsequent exercises.

## 4. AWS Console Implementation

Steps performed:

1. Opened Amazon VPC in AWS Management Console.
2. Selected **Your VPCs → Create VPC**.
3. Selected **VPC only**.
4. Entered the VPC name and IPv4 CIDR.
5. Selected default tenancy.
6. Created the VPC.
7. Verified the VPC state and configuration.

## 5. Validation Results

| Validation | Result |
|---|---|
| VPC creation | Successful |
| VPC name | Verified |
| IPv4 CIDR | Verified |
| VPC state | Available |
| Default VPC | No |
| DNS resolution | Enabled |

## 6. Screenshot Evidence

Screenshot: `screenshots/01-vpc/01-vpc-created.png`

Shows the successfully created ABC Insurance VPC and its configuration.

## 7. Resources Created

| Resource | Quantity |
|---|---:|
| VPC | 1 |

No subnets, NAT Gateways, or RDS databases have been created at this stage.

## 8. Cost Considerations

Amazon VPC creation has no additional charge.

No NAT Gateway or chargeable database resources were provisioned during this step.

## 9. Project Status

**VPC creation:** Completed.

**Next step:** Review VPC settings and begin subnet creation after finalizing the VPC configuration.
