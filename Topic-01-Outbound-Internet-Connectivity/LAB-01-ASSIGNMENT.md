# Lab 1 Final Assignment — Real-World Outbound Connectivity Challenge

Complete this assignment only after finishing Lab 1A, Lab 1B, and Lab 1C.

There are deliberately **no implementation commands** here. The goal is to prove that you can translate requirements into an Azure design without following a step-by-step script.

---

# Scenario

You have joined the infrastructure team at **Northwind Services**, which is moving a small application environment into Azure.

The company has three workload groups:

1. **Private application servers** must reach software repositories and approved external APIs. They must not receive individual public IP addresses, and the team wants a predictable shared outbound identity.
2. A **temporary troubleshooting VM** needs its own public identity so an administrator can test connectivity independently.
3. A **web-server group** will sit behind a Standard Public Load Balancer. The backend VMs must remain without individual public IP addresses but still need outbound Internet connectivity through the Load Balancer.

Design, deploy, test, document, and safely remove all three solutions when finished.

---

# Requirements

## Part A — NAT Gateway

Build a solution where at least one private VM uses NAT Gateway for outbound Internet access.

Prove that:

- The VM has no public IP attached directly to its NIC.
- The NAT Gateway is associated with the correct subnet.
- The VM can reach the public Internet.
- The public source IP seen by an Internet service matches the NAT Gateway public IP.

## Part B — VM public IP

Build or modify a VM so that its outbound connectivity uses a public IP attached directly to its NIC.

Prove that:

- The VM has a public IP attached to its NIC configuration.
- The VM can reach the public Internet.
- The public source IP seen by an Internet service matches the VM public IP.

## Part C — Standard Load Balancer outbound rule

Build or modify the environment so that a private VM uses a Standard Public Load Balancer outbound rule.

Prove that:

- The VM has no public IP of its own.
- The VM is a member of the Load Balancer backend pool.
- An outbound rule exists for that backend pool.
- The VM can reach the public Internet.
- The public source IP seen by an Internet service matches the Load Balancer frontend public IP.

---

# Constraints

- Use Azure CLI wherever practical.
- Use clear, meaningful resource names.
- Keep workloads private unless the scenario explicitly requires a VM public IP.
- Do not rely on implicit default outbound access.
- Use an interactive method that lets you run tests from inside the VM.
- Choose a currently available, appropriately small VM size.
- Do not copy IP addresses from the guided lab; inspect and validate your own environment.
- Keep assignment resources grouped so they can be safely removed when the assessment is complete.

---

# Evidence to submit

For each design, collect enough evidence to prove that the architecture works:

- The Azure resource providing the public identity.
- Whether the VM NIC has a public IP.
- The outbound test run from inside the VM.
- The public source IP observed by the external service.
- A short explanation of why the result proves the intended design is working.
- A simple outbound-and-return traffic flow diagram.

For teardown, also collect evidence that:

- The assignment resource group was deleted.
- A verification check confirms the resource group no longer exists.

---

# Success criteria

The assignment is complete when you can independently prove all three paths:

```text
Private workload subnet
→ NAT Gateway
→ Internet
```

```text
Single VM
→ VM public IP
→ Internet
```

```text
Private backend VM
→ Load Balancer backend pool
→ outbound rule
→ Load Balancer frontend public IP
→ Internet
```

You must also be able to explain why you would choose one method over another in a real environment.

The final teardown is considered successful only when your verification shows that the assignment resource group no longer exists.

---

# Interview challenge

Answer these questions **without looking at the guided lab commands**.

## Simple 1

**What is the purpose of an Azure NAT Gateway, and where is it associated in a virtual network?**

## Simple 2

**A VM has a private IP but no public IP. Does that automatically mean the VM cannot reach the Internet? Explain.**

## Medium 1

**You have ten private application VMs in one subnet. A third-party API requires all requests to come from a known public IP. Which outbound design from this lab would you normally consider first, and why?**

## Medium 2

**A VM is in a Standard Load Balancer backend pool but still cannot reach the Internet. What configuration from this lab would you verify before assuming the Load Balancer is providing outbound SNAT?**

## Hard 1

**Your company has three workload groups: private application servers that call external APIs, Internet-facing web servers behind a Standard Public Load Balancer, and sensitive infrastructure servers that should have tightly controlled Internet access. Design the outbound strategy for each group and justify when you would use or avoid NAT Gateway, VM public IPs, and Load Balancer outbound rules.**

---

# Completion standard

Do not mark the assignment complete just because the Azure resources deployed successfully.

You should be able to:

1. Build all three designs without copying the guided commands line by line.
2. Test each design from inside the VM.
3. Identify which Azure resource supplied the public source identity.
4. Explain the outbound and return traffic flow.
5. Delete the assignment environment and verify that cleanup is complete.
6. Answer all five interview questions in your own words.

Back to: [Lab 1 overview](./LAB-01-README.md)
