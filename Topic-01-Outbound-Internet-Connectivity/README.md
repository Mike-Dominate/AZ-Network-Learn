# Topic 1 — Azure VNet Outbound Internet Connectivity

This topic covers three common ways Azure resources can reach the internet:

1. **NAT Gateway** — multiple private resources share one or more public IP addresses for outbound connectivity.
2. **Public IP on a VM** — a VM has its own public internet identity.
3. **Public Load Balancer outbound SNAT** — backend VMs can use the load balancer frontend public IP for outbound connections.

## Traffic-flow diagram

![Azure VNet outbound internet connectivity](./azure_vnet_outbound_internet_methods.png)

## Core mental model

- **NAT Gateway:** private machines → shared outbound public identity → internet.
- **Public IP on VM:** one VM → its own public identity → internet.
- **Public Load Balancer:** backend pool → load balancer frontend public identity → internet.

The important idea is that a private Azure resource needs an outbound mechanism that gives its traffic a publicly routable source identity before the traffic can traverse the public internet.

---

# Hands-on Lab 1 — Beginner implementation

The theory above has now been implemented as a complete beginner lab using one private Ubuntu VM and three outbound scenarios.

## Completed lab flow

![Topic 1 lab outbound traffic flows](./azure_vnet_outbound_internet_methods_flow.png)

## Lab sequence

1. [Lab 1 overview and common setup](./LAB-01-README.md)
2. [Lab 1A — NAT Gateway](./Lab-01A-NAT-Gateway/README.md)
3. [Lab 1B — Public IP on the VM](./Lab-01B-VM-Public-IP/README.md)
4. [Lab 1C — Public Load Balancer outbound SNAT](./Lab-01C-Load-Balancer-Outbound/README.md)

## Verified results

| Scenario | Observed outbound public IP | Result |
|---|---|---|
| Baseline — no outbound mechanism | None; connection timed out | PASS |
| Lab 1A — NAT Gateway | `4.196.234.60` | PASS |
| Lab 1B — VM Public IP | `20.213.95.101` | PASS |
| Lab 1C — Load Balancer outbound | `52.187.242.240` | PASS |

The lab intentionally starts with a VM that has no public IP and `defaultOutboundAccess = false`, proves that DNS resolution can still work while Internet traffic fails, and then enables each outbound mechanism one at a time so the learner can see exactly which Azure component provides the public source identity.
