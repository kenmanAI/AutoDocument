# AWS Webserver Stack Infrastructure Documentation

## Overview

This infrastructure documentation describes the "webserver-stack" AWS CloudFormation stack, which was deployed on 2025-06-27T05:16:16Z. The stack is located in the us-east-2 region and is part of the "Development" environment, as indicated by the tags.

The purpose of this infrastructure is to host a web server application. This is inferred from the presence of security groups and subnets, which are commonly used for web server deployments.

## VPC

The VPC (Virtual Private Cloud) for this infrastructure has the following configuration:

| Attribute | Value |
| --- | --- |
| VPC ID | vpc-04c94eb4dbaa9ae68 |
| CIDR Block | 10.0.0.0/16 |
| Tenancy | default |
| State | available |
| Tags | <ul><li>LastUpdated: 2025-06-20</li><li>Name: DevVPCNetwork</li><li>Environment: Development</li></ul> |
| DHCP Options ID | dopt-c198fbaa |
| Public Access Blocked | false (InternetGatewayBlockMode: off) |

The VPC serves as the isolated network environment for the web server stack, providing network-level security and control over the resources within it.

## Subnets

The infrastructure has two subnets, with the following configurations:

### Subnet 1

| Attribute | Value |
| --- | --- |
| Subnet ID | subnet-019a2cda3c649b1bc |
| CIDR Block | 10.0.10.0/24 |
| Availability Zone | us-east-2a |
| Available IP Addresses | 251 |
| Public IP on Launch | true |
| Tags | <ul><li>Name: MyPublicSubnet1</li></ul> |

This subnet is designated as a public subnet, as indicated by the "MapPublicIpOnLaunch" attribute being set to "true". It is likely intended to host the web server instances, allowing them to be directly accessible from the internet.

### Subnet 2

| Attribute | Value |
| --- | --- |
| Subnet ID | subnet-05dc08ce320f99ad2 |
| CIDR Block | 10.0.20.0/24 |
| Availability Zone | us-east-2b |
| Available IP Addresses | 251 |
| Public IP on Launch | true |
| Tags | <ul><li>Name: MyPublicSubnet2</li></ul> |

Similar to Subnet 1, this subnet is also designated as a public subnet, allowing for internet-facing resources to be deployed within it.

## Route Tables

The infrastructure has two route tables:

### Main Route Table

| Attribute | Value |
| --- | --- |
| Route Table ID | rtb-0be747d95afbf717d |
| Main Route Table | true |
| Routes | <ul><li>Destination CIDR Block: 10.0.0.0/16, Gateway ID: local</li></ul> |
| Tags | None |

This is the main route table for the VPC, containing a single route for the VPC's CIDR block, allowing local communication within the VPC.

### Custom Route Table

| Attribute | Value |
| --- | --- |
| Route Table ID | rtb-024a20b5404994209 |
| Main Route Table | false |
| Routes | <ul><li>Destination CIDR Block: 10.0.0.0/16, Gateway ID: local</li><li>Destination CIDR Block: 0.0.0.0/0, Gateway ID: igw-09e6d91be53849aa1</li></ul> |
| Tags | <ul><li>Name: MyRouteTable</li></ul> |
| Associated Subnets | <ul><li>subnet-019a2cda3c649b1bc</li><li>subnet-05dc08ce320f99ad2</li></ul> |

This custom route table is associated with both public subnets (Subnet 1 and Subnet 2). It contains a route to the local VPC CIDR block, as well as a default route (0.0.0.0/0) that directs traffic to the internet gateway, enabling internet connectivity for resources in these subnets.

## Internet Gateway

| Attribute | Value |
| --- | --- |
| Internet Gateway ID | igw-09e6d91be53849aa1 |
| VPC ID | vpc-04c94eb4dbaa9ae68 |
| Tags | <ul><li>Name: MyInternetGateway</li></ul> |

The internet gateway is attached to the VPC and enables internet connectivity for resources within the public subnets. The custom route table's default route (0.0.0.0/0) directs traffic to this internet gateway, allowing web server instances in the public subnets to communicate with the internet.

## Security Groups

The infrastructure has two security groups:

### Web Server Security Group

| Attribute | Value |
| --- | --- |
| Security Group ID | sg-0e8ea7fe4d808b072 |
| VPC ID | vpc-04c94eb4dbaa9ae68 |
| Inbound Rules | <ul><li>TCP port 80 (HTTP) from 0.0.0.0/0</li><li>TCP port 22 (SSH) from 0.0.0.0/0</li><li>TCP port 3389 (RDP) from 0.0.0.0/0</li></ul> |
| Outbound Rules | Allow all traffic (0.0.0.0/0) |
| Tags | <ul><li>Name: WebServerSecurityGroup</li></ul> |

This security group is likely applied to the web server instances, allowing inbound HTTP, SSH, and RDP traffic from any IP address (0.0.0.0/0). The outbound rules allow unrestricted access to the internet.

### Restricted Security Group

| Attribute | Value |
| --- | --- |
| Security Group ID | sg-0b7278996b47cb376 |
| VPC ID | vpc-04c94eb4dbaa9ae68 |
| Inbound Rules | <ul><li>TCP port 22 (SSH) from 192.168.1.0/24</li><li>TCP port 443 (HTTPS) from 10.0.0.0/8</li></ul> |
| Outbound Rules | Allow all traffic (0.0.0.0/0) |
| Tags | <ul><li>Name: RestrictedSecurityGroup</li></ul> |

This security group is more restrictive, allowing SSH access only from the 192.168.1.0/24 network and HTTPS access only from the 10.0.0.0/8 network (likely the VPC CIDR block). This security group could be used for more sensitive resources or management instances within the infrastructure.

## Relationships

The resources in this infrastructure are interconnected as follows:

1. The VPC (vpc-04c94eb4dbaa9ae68) serves as the isolated network environment for the web server stack.
2. The two public subnets (subnet-019a2cda3c649b1bc and subnet-05dc08ce320f99ad2) reside within the VPC.
3. The custom route table (rtb-024a20b5404994209) is associated with both public subnets, routing traffic to the internet gateway (igw-09e6d91be53849aa1) for external connectivity.
4. The web server security group (sg-0e8ea7fe4d808b072) and the restricted security group (sg-0b7278996b47cb376) are defined within the VPC, controlling inbound and outbound traffic for the resources they are applied to.

The overall infrastructure is managed as a CloudFormation stack, as indicated by the stack ARN: `arn:aws:cloudformation:us-east-2:739767513717:stack/webserver-stack/7d3ee2d0-5314-11f0-93ab-021a37af75cf`.

## Metadata

- Deployment Timestamp: 2025-06-27T05:16:16Z
- Deployment Environment: Production (inferred from tags)

## Summary

This AWS infrastructure, deployed as the "webserver-stack" CloudFormation stack, is designed to host a web server application. The key configurations include:

- A VPC with a CIDR block of 10.0.0.0/16, located in the us-east-2 region and part of the "Development" environment.
- Two public subnets, each with a /24 CIDR block, located in different availability zones (us-east-2a and us-east-2b).
- A custom route table associated with the public subnets, routing internet-bound traffic through an internet gateway.
- Two security groups: one for the web server instances, allowing HTTP, SSH, and RDP access, and a more restrictive security group for sensitive resources.

The resources are interconnected within the VPC, enabling web server instances in the public subnets to communicate with the internet via the internet gateway and the custom route table. This infrastructure provides a secure and scalable environment for hosting the web server application.