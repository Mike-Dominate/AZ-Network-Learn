# Lab 1 Final Assignment — Real-World Outbound Connectivity Challenge

This assignment is completed **after** Lab 1A, Lab 1B, and Lab 1C.

There are deliberately **no implementation commands in this section**. Use the guided labs as your reference, then build the required environment independently.

---

# Scenario

You have joined the infrastructure team at **Northwind Services**, a company moving a small application environment into Azure.

The company has three different workload requirements:

1. A group of private application servers must reach software repositories and approved external APIs. The servers must **not** receive individual public IP addresses. The network team wants the group to share a predictable outbound public identity.
2. A temporary troubleshooting VM must have its **own** public identity so an administrator can test connectivity from that machine independently of the other workloads.
3. A group of web servers will sit behind a **Standard Public Load Balancer**. The web servers must remain without individual public IP addresses but must be able to initiate outbound Internet connections through the Load Balancer.

Your task is to design, deploy, test, and document all three solutions.

---

# Assignment requirements

## Part A — NAT Gateway solution

Build an Azure network containing at least one private VM whose outbound Internet connectivity is provided by a NAT Gateway.

Your solution must demonstrate that:

- The VM has no public IP attached directly to its NIC.
- The NAT Gateway is associated with the correct subnet.
- The VM can reach the public Internet.
- The public source IP observed by an Internet service matches the public IP assigned to the NAT Gateway.

## Part B — VM public IP solution

Build or modify a VM so that its outbound Internet connectivity uses a public IP attached directly to the VM NIC.

Your solution must demonstrate that:

- The VM has a public IP attached to its NIC configuration.
- The VM can reach the public Internet.
- The public source IP observed by an Internet service matches the VM's public IP.

## Part C — Standard Load Balancer outbound solution

Build or modify the environment so that a private VM uses a Standard Public Load Balancer outbound rule.

Your solution must demonstrate that:

- The VM does not have its own public IP.
- The VM is a member of the Load Balancer backend pool.
- An outbound rule exists for the backend pool.
- The VM can reach the public Internet.
- The public source IP observed by an Internet service matches the Load Balancer frontend public IP.

---

# Constraints

- Use Azure CLI for the deployment wherever practical.
- Use clear resource names that identify their purpose.
- Keep workload VMs private unless the scenario explicitly requires a VM public IP.
- Do not rely on implicit default outbound access.
- Use an interactive connection method that lets you run tests from inside the VM.
- Choose currently available VM sizes appropriate for a small lab.
- Do not copy Azure-assigned IP addresses from another lab. Record and validate the values created in your own environment.

---

# Evidence to collect

For each of the three solutions, capture enough evidence to prove that the design works. Your evidence should include:

- The Azure resource that provides the public identity.
- Confirmation of whether the VM NIC has a public IP.
- The outbound test executed from inside the VM.
- The public source IP observed by the external test service.
- A short explanation of why that observed IP proves the intended design is working.

You should also be able to draw the outbound and return traffic path for each solution.

---

# Success criteria

The assignment is complete when you can independently prove all three statements:

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

You should also be able to explain when you would choose each method in a real environment.

---

# Job interview questions

Answer these questions **without looking at the guided commands first**. These are the kinds of questions that can follow directly from the skills practised in this lab.

## Simple 1

**What is the purpose of an Azure NAT Gateway, and where is it associated in a virtual network?**

A strong answer should identify that NAT Gateway provides outbound Internet connectivity/public source translation for private resources and is associated with a subnet.

## Simple 2

**A VM has a private IP but no public IP. Does that automatically mean the VM cannot reach the Internet? Explain.**

A strong answer should distinguish the VM's own public IP from other outbound mechanisms such as NAT Gateway or Load Balancer outbound rules.

## Medium 1

**You have ten private application VMs in one subnet. A third-party API requires all requests to come from a known public IP. Which outbound design from this lab would you normally consider first, and why?**

A strong answer should discuss NAT Gateway as a subnet-level, shared, predictable outbound design rather than assigning a public IP to every VM.

## Medium 2

**A VM is in a Standard Load Balancer backend pool but still cannot reach the Internet. What configuration from this lab would you verify before assuming the Load Balancer is providing outbound SNAT?**

A strong answer should mention the outbound rule, backend pool membership, frontend public IP, and the absence of a conflicting/alternative outbound design where relevant.

## Hard 1

**Your company has three workload groups: private application servers that call external APIs, Internet-facing web servers behind a Standard Public Load Balancer, and sensitive infrastructure servers that should have tightly controlled Internet access. Design the outbound strategy for each group and justify why you would or would not use NAT Gateway, VM public IPs, and Load Balancer outbound rules in each case.**

A strong answer should compare exposure, scale, operational control, public source identity, grouping model, and the principle of avoiding unnecessary public IPs. There is more than one defensible design; the important part is being able to explain the trade-offs.

---

# Completion standard

Do not mark this assignment complete just because the Azure resources deployed successfully.

You should be able to:

1. Build all three designs without copying the guided commands line by line.
2. Test each design from inside the VM.
3. Identify which Azure resource supplied the public source identity.
4. Explain the outbound and return traffic flow.
5. Answer all five interview questions in your own words.

Back to: [Lab 1 overview](./LAB-01-README.md)
