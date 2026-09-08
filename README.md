# AZ-Network-Learn

**A beginner-first Azure networking course built around understanding how traffic actually moves.**

This repository teaches Azure networking through clear mental models, visual diagrams, Azure CLI labs, real-world scenarios, independent assignments, and job-focused interview questions.

The goal is not simply to memorise Azure networking products. The goal is to understand **how Azure resources communicate, how traffic reaches them, how that traffic is controlled, and how to troubleshoot the path when something goes wrong**.

---

# What is Azure networking?

Azure networking is the collection of services and controls that allow Azure resources, users, the Internet, on-premises networks, and other cloud services to communicate.

Whenever two systems need to communicate, networking must answer questions such as:

- What address identifies the destination?
- How is a name translated into an IP address?
- Which path should the packet take?
- Is the traffic allowed?
- Does the workload need a public or private identity?
- Should traffic pass through a NAT Gateway, Load Balancer, firewall, VPN, or another network service?
- How does the response find its way back?

These questions appear repeatedly across Azure, regardless of the workload being deployed.

---

# Why networking matters in the cloud

Creating an Azure resource is only part of making a system work. The resource must usually be able to **communicate with something else**.

A virtual machine may need to reach the Internet for software updates. A web application may need users to reach it securely. An application server may need to connect privately to a database. A storage account may need to be reachable only through a private endpoint. An organisation may need to connect its office network to Azure through VPN or ExpressRoute.

Cloud networking determines how those connections happen.

A useful mental model is:

```text
Resource exists
     |
     v
How is it addressed?
     |
     v
How is its name resolved?
     |
     v
Where should traffic go?
     |
     v
Is the traffic allowed?
     |
     v
What network service handles the connection?
     |
     v
Destination reached
     |
     v
How does the response return?
```

Understanding that flow makes Azure much easier to reason about.

---

# Why learn Azure networking early?

Networking provides a foundational mental model for understanding how **almost every Azure workload communicates, is reached, is protected, and connects to other services**.

The same networking concepts appear across many Azure technologies:

| Azure workload or service | Networking question you eventually need to answer |
|---|---|
| Virtual Machines | How is the VM addressed, reached, protected, and given outbound access? |
| Web and application workloads | How do users reach the application and how does the application reach its dependencies? |
| Databases and Storage | Should access use public endpoints or private connectivity? |
| Private Endpoints | How does a service receive a private IP and how does DNS resolve it? |
| Load Balancers / Application Gateway | Where does incoming traffic go and how is it distributed? |
| Azure Firewall / NSGs | Which traffic is allowed or denied? |
| AKS and container platforms | How do nodes, workloads, services, and external clients communicate? |
| VPN / ExpressRoute | How does an on-premises network reach Azure privately? |
| Hub-and-spoke networks | How is traffic routed between many networks and shared services? |

You do not need to become a network specialist before learning other Azure services. But if you understand **IP addressing, DNS, routing, filtering, private connectivity, and traffic flow**, you gain a framework that transfers across the platform.

---

# What this course teaches

This course focuses on the **flow and actions of networking**, not just product definitions.

For each topic, the learner should be able to answer:

> **What happens to the traffic, in what order, and which Azure resource performs each action?**

The course develops practical understanding of areas such as:

- Virtual Networks and subnets
- Public and private IP addressing
- Outbound Internet connectivity
- Communication between Azure resources
- Hybrid connectivity
- Network filtering and security boundaries
- Routing and next-hop decisions
- DNS and name resolution
- NAT and SNAT
- Load balancing
- Private connectivity
- Troubleshooting network paths

---

# How the course works

The learning method is deliberately practical:

```text
1. Technical concept
        ↓
2. Traffic-flow mental model
        ↓
3. Everyday-world analogy
        ↓
4. Guided Azure CLI lab
        ↓
5. Test and observe the result
        ↓
6. Explain what happened
        ↓
7. Independent real-world assignment
        ↓
8. Job-focused interview questions
```

The guided labs are written for beginners, but the learner is expected to gradually stop copying commands and start making design decisions independently.

---

# Skills you should develop

By working through the course, you should become increasingly comfortable with:

- Reading a network diagram and predicting traffic flow.
- Building Azure networking resources with Azure CLI.
- Understanding the relationship between a VNet, subnet, NIC, IP address, route, and security control.
- Testing connectivity from inside workloads instead of relying only on the Azure portal.
- Determining which Azure component provided a public or private network identity.
- Separating DNS problems from routing, filtering, and connectivity problems.
- Choosing an Azure networking approach based on workload requirements.
- Explaining your design choices in language suitable for real engineering discussions and interviews.

---

# Course roadmap

1. [**Topic 1 — Azure VNet Outbound Internet Connectivity**](./Topic-01-Outbound-Internet-Connectivity/README.md)  
   NAT Gateway, VM public IP, and Standard Load Balancer outbound connectivity.

2. [**Topic 2 — Communication Between Azure Resources**](./Topic-02-Communication-Between-Azure-Resources/README.md)  
   Same-VNet communication, service endpoints, private endpoints, and VNet peering.

3. [**Topic 3 — Communication Between On-Premises and Azure**](./Topic-03-On-Premises-to-Azure-Connectivity/README.md)  
   Point-to-Site VPN, Site-to-Site VPN, and ExpressRoute.

4. [**Topic 4 — Filtering Network Traffic**](./Topic-04-Filtering-Network-Traffic/README.md)  
   Network Security Groups and Network Virtual Appliances.

5. [**Topic 5 — Routing Network Traffic**](./Topic-05-Routing-Network-Traffic/README.md)  
   Azure system routes, User Defined Routes, next hops, and BGP-learned routes.

6. [**Topic 6 — Name Resolution in Azure VNets**](./Topic-06-Name-Resolution-in-Azure-VNets/README.md)  
   Public DNS, DNS delegation, child zones, and Azure Private DNS.

More topics and hands-on labs can be added as the course grows.

---

# Relationship to AZ-700

The concepts in this repository directly support **AZ-700: Designing and Implementing Microsoft Azure Networking Solutions**, but this is not intended to be an exam-cram repository.

The objective is broader:

> **Understand Azure networking well enough to build, test, troubleshoot, and explain real Azure environments.**

Certification becomes much easier when the underlying traffic flows already make sense.

---

# Who this course is for

This course is suitable for learners who are beginning Azure networking or who have used Azure resources without feeling fully confident about what happens to the traffic underneath them.

It is particularly useful for aspiring or practising:

- Azure Administrators
- Cloud Engineers
- Infrastructure Engineers
- Network Engineers
- Platform Engineers
- DevOps Engineers
- Cloud Support Engineers
- Solutions Architects who want stronger implementation foundations

---

# Start here

Begin with:

## [Topic 1 — Azure VNet Outbound Internet Connectivity](./Topic-01-Outbound-Internet-Connectivity/README.md)

The first topic introduces a pattern that will continue throughout the course:

**build it → test it → observe the traffic → explain why it worked.**
