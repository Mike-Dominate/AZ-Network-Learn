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
