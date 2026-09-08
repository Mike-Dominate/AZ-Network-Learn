# Lab 2 Final Assignment — Azure Resource Communication Challenge

Complete this assignment only after finishing Lab 2A, Lab 2B, Lab 2C, and Lab 2D.

There are deliberately **no implementation commands** in this section. The goal is to prove that you can translate requirements into an Azure networking design without following a step-by-step script.

---

# Scenario

You have joined the cloud infrastructure team at **Contoso Digital Services**. The company is moving a three-tier application and several shared services into Azure.

The design requirements are:

1. The web tier and application tier are in different subnets of the same VNet and must communicate privately.
2. A reporting subnet must access an Azure Storage account through a **Service Endpoint**, while an untrusted subnet must be denied.
3. A sensitive application must access a second Azure Storage account through a **Private Endpoint**. Public network access to that Storage account must be disabled.
4. A separate shared-services VNet contains an internal service that must communicate privately with the application VNet through **VNet Peering**.

Your task is to design, deploy, test, document, and tear down all four communication patterns.

---

# Requirements

## Part A — Same VNet communication

Build two private VMs in different subnets of the same VNet.

Prove that:

- Both VMs have private IP addresses.
- Neither VM requires a public IP for the test.
- A service listening on one VM can be reached from the other VM.
- The effective route table shows that the VNet address space is reachable using Azure's local VNet routing.

## Part B — Service Endpoint

Configure a supported Azure PaaS service using a Service Endpoint design.

Prove that:

- The trusted subnet has the correct Service Endpoint enabled.
- The service firewall or network rule allows the trusted subnet.
- The trusted VM can access the service.
- A VM in an untrusted subnet is denied.
- The trusted NIC's effective route table shows Service Endpoint routing behavior.

## Part C — Private Endpoint

Configure a second PaaS service using a Private Endpoint.

Prove that:

- A Private Endpoint receives a private IP from a dedicated subnet.
- Private DNS resolves the normal service hostname to the Private Endpoint IP.
- Public network access to the PaaS service is disabled.
- Workloads in the linked VNet can access the service privately.
- The effective route table shows the Private Endpoint host route.

## Part D — VNet Peering

Create a second VNet and a private workload inside it.

Prove that:

- Connectivity fails before peering exists.
- Peering is configured in both directions.
- Peering state is `Connected`.
- The same connectivity test succeeds after peering.
- The effective route table shows the remote VNet address space with next-hop type `VNetPeering`.

---

# Constraints

- Use Azure CLI wherever practical.
- Use clear, meaningful resource names.
- Keep workload VMs private unless the design explicitly requires otherwise.
- Do not rely on implicit default outbound access for the workload subnets.
- Use an interactive method such as Bastion to run tests from inside the VMs.
- Use the smallest practical VM size available for the lab.
- Do not copy Azure-assigned IP addresses from another learner's environment.
- Do not publish real passwords, SAS tokens, storage keys, or other secrets in your documentation.

---

# Evidence to submit

For each design, collect enough evidence to prove that the architecture works:

- Relevant Azure resource configuration.
- Source and destination subnets or VNets.
- DNS result where applicable.
- Effective route information where applicable.
- Connectivity test result.
- A simple traffic-flow diagram.
- A short explanation of why the result proves the intended communication model.

---

# Real-world design explanation

For each of the four communication models, explain:

1. Where you would use it in production.
2. Why it fits that scenario.
3. What security or operational problem it solves.
4. What alternative you considered and why you did not choose it.

---

# Teardown requirement

After all evidence has been collected, review the resource group and remove the complete assignment environment.

The assignment is not complete until you verify that the resource group no longer exists.

Expected final verification:

```text
false
```

---

# Interview challenge

Answer these questions **without looking at the guided lab commands**.

## Simple 1

**Two VMs are in different subnets of the same Azure VNet. Do they need VNet Peering to communicate? Why or why not?**

## Simple 2

**What is the main difference between a Service Endpoint and a Private Endpoint?**

## Medium 1

**A Storage account must remain on its standard service endpoint, but only workloads from one Azure subnet should be allowed to access it. Which design from this lab would you consider and what must be configured on both the subnet and the Storage account?**

## Medium 2

**An application resolves an Azure Storage hostname to a private IP address and public network access on the Storage account is disabled. Which Azure networking components are likely involved, and how would you verify the traffic path?**

## Hard 1

**You are designing an enterprise Azure environment with a hub VNet, multiple spoke VNets, shared platform services, sensitive PaaS resources, and application tiers split across subnets. Explain where you would use same-VNet routing, Service Endpoints, Private Endpoints, and VNet Peering. Include the security and routing trade-offs of each and explain why VNet Peering alone does not automatically make a hub-and-spoke design transitive.**

---

# Completion standard

Do not mark this assignment complete just because the Azure resources deployed successfully.

You should be able to:

1. Build all four designs without copying the guided commands line by line.
2. Test each design from the correct source workload.
3. Identify what changed in routing, DNS, or service network policy.
4. Explain the traffic and return path for each design.
5. Explain where each design belongs in a real production architecture.
6. Answer all five interview questions in your own words.
7. Tear down the assignment and verify cleanup.

Back to: [Lab 2 overview](./LAB-02-README.md)
