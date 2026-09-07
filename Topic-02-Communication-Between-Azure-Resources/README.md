# Topic 2 — Communication Between Azure Resources

This topic covers four common ways Azure resources communicate:

1. **Same VNet** — resources in subnets of the same VNet communicate using Azure system routing.
2. **VNet Service Endpoint** — a subnet reaches a supported Azure PaaS service over the Azure backbone while the service keeps its public endpoint.
3. **Private Endpoint** — an Azure PaaS service is represented by a private IP inside a VNet through a private endpoint.
4. **VNet Peering** — two separate VNets communicate privately over the Microsoft backbone.

## Traffic-flow diagram

![Communication between Azure resources](./azure_resource_communication_guide.png)

## Core mental model

- **Same VNet:** subnet → Azure system routing → another subnet in the same VNet.
- **Service Endpoint:** subnet → Azure backbone → public Azure service endpoint; the subnet is trusted by the service.
- **Private Endpoint:** workload → private IP in a VNet → Azure PaaS service.
- **VNet Peering:** VNet A ↔ Microsoft backbone ↔ VNet B.

The key distinction to remember is:

- **Service Endpoint:** the Azure service remains publicly addressed, but access can be restricted to selected VNets/subnets.
- **Private Endpoint:** the Azure service is reached through a private IP placed inside your network.
- **VNet Peering:** connects separate VNet routing domains and is non-transitive.
