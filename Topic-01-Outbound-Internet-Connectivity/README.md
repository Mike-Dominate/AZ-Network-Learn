# Topic 1 — Azure VNet Outbound Internet Connectivity

This topic covers three common ways Azure resources can reach the Internet:

1. **NAT Gateway** — multiple private resources share one or more public IP addresses for outbound connectivity.
2. **Public IP on a VM** — a VM has its own public Internet identity.
3. **Public Load Balancer outbound SNAT** — backend VMs can use the Load Balancer frontend public IP for outbound connections.

## Traffic-flow diagram

![Azure VNet outbound internet connectivity](./azure_vnet_outbound_internet_methods.png)

## Core mental model

- **NAT Gateway:** private machines → shared outbound public identity → Internet.
- **Public IP on VM:** one VM → its own public identity → Internet.
- **Public Load Balancer:** backend pool → Load Balancer frontend public identity → Internet.

The important idea is that a private Azure resource needs an outbound mechanism that gives its traffic a publicly routable source identity before the traffic can traverse the public Internet.

---

# Hands-on Lab 1 — Beginner implementation

The theory above is implemented as a complete beginner lab using one private Ubuntu VM and three outbound scenarios.

> **Course note:** Azure-assigned IP addresses are intentionally not published as expected answers. Each learner must inspect the addresses created in their own environment and prove that the correct Azure resource supplies the public outbound identity.

## Lab flow

![Topic 1 lab outbound traffic flows](./azure_vnet_outbound_internet_methods_flow.png)

## Guided lab sequence

1. [Lab 1 overview and common setup](./LAB-01-README.md)
2. [Lab 1A — NAT Gateway](./Lab-01A-NAT-Gateway/README.md)
3. [Lab 1B — Public IP on the VM](./Lab-01B-VM-Public-IP/README.md)
4. [Lab 1C — Public Load Balancer outbound SNAT](./Lab-01C-Load-Balancer-Outbound/README.md)

## What the learner must prove

| Scenario | Validation target |
|---|---|
| Baseline — no outbound mechanism | DNS can resolve, but the Internet connection fails |
| Lab 1A — NAT Gateway | Internet-observed source IP matches the NAT Gateway public IP |
| Lab 1B — VM Public IP | Internet-observed source IP matches the VM public IP |
| Lab 1C — Load Balancer outbound | Internet-observed source IP matches the Load Balancer frontend public IP |

The lab starts with a VM that has no public IP and `defaultOutboundAccess = false`, proves that DNS resolution can still work while Internet traffic fails, and then enables each outbound mechanism one at a time.

---

# Skills gained

By completing Topic 1, the learner should be able to:

- Build and validate Azure NAT Gateway outbound connectivity.
- Configure a VM with its own public IP and verify its public source identity.
- Configure a Standard Public Load Balancer backend pool and outbound rule.
- Test outbound connectivity from inside a Linux VM.
- Distinguish DNS resolution from actual Internet reachability.
- Explain the difference between subnet-level, VM-level, and backend-pool-level outbound designs.
- Select an outbound method based on workload requirements rather than simply attaching public IPs everywhere.

## Real-world application

These skills apply to private application servers, web farms, partner/API allowlisting, software update paths, test systems, cloud migrations, and production troubleshooting. They are directly relevant to Azure Administrator, Cloud Engineer, Infrastructure Engineer, Network Engineer, Platform Engineer, and Cloud Support roles.

---

# Independent assessment

After completing the guided labs, the learner must rebuild and validate all three approaches from a real-world scenario **without implementation commands**.

[**Lab 1 Final Assignment — Real-World Outbound Connectivity Challenge**](./LAB-01-ASSIGNMENT.md)

The assignment also contains five job interview questions:

- 2 simple
- 2 medium
- 1 hard

The learner should be able to answer them in their own words after completing the practical work.
