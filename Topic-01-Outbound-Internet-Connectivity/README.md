# Topic 1 — Azure VNet Outbound Internet Connectivity

Azure workloads often use private IP addresses but still need to initiate connections to the public Internet. This topic introduces three explicit outbound connectivity methods and then turns them into a beginner hands-on lab.

## The three outbound methods

| Method | Public identity is provided by | Grouping model |
|---|---|---|
| **NAT Gateway** | NAT Gateway public IP or prefix | Subnet |
| **Public IP on a VM** | Public IP attached to the VM NIC | Individual VM |
| **Standard Public Load Balancer outbound rule** | Load Balancer frontend public IP | Backend pool |

## Concept diagram

![Azure VNet outbound Internet methods](./azure_vnet_outbound_internet_methods.png)

## Core mental model

- **NAT Gateway:** private workloads share a subnet-level outbound public identity.
- **VM public IP:** one VM has its own public identity.
- **Load Balancer outbound rule:** selected backend pool members share the Load Balancer frontend public identity.

The important question in every scenario is:

> **Which Azure resource provides the public source identity seen by the Internet?**

## Topic 1 learning path

1. **Understand the three methods** — this page.
2. **Build and test them step by step** — [Lab 1 guided implementation](./LAB-01-README.md).
3. **Rebuild the solution independently** — [Lab 1 final assignment](./LAB-01-ASSIGNMENT.md).
4. **Answer the interview challenge** — included at the end of the final assignment.

## Start the hands-on lab

[**Continue to Lab 1 — Azure VM Outbound Internet Connectivity**](./LAB-01-README.md)
