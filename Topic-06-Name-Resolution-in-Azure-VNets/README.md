# Topic 6 — Name Resolution in Azure VNets

DNS answers one question: **What IP address belongs to this name?**

This topic covers the main name-resolution ideas from the Azure VNet design lesson:

1. **Azure Public DNS** — public DNS zones and records that Internet clients can resolve.
2. **DNS Delegation** — the parent domain points queries for a zone to the Azure DNS name servers that are authoritative for it.
3. **Child DNS Zones** — subdomains can be delegated separately so different teams or DNS services can manage them.
4. **Azure Private DNS** — private DNS zones used for names that should resolve only inside linked private Azure networks.

## Traffic-flow diagram

![Name resolution in Azure VNets](./azure_name_resolution_vnet.png)

## Core mental model

- **DNS:** name → IP address.
- **Public DNS:** everyone on the Internet can ask for the name.
- **Private DNS:** only private networks with the right DNS visibility should resolve the name.
- **Delegation:** the parent DNS zone tells the client which authoritative DNS servers are responsible for the child zone.

## Packet / request flow

1. An application asks for a name such as `www.contoso.com` or `db.internal.contoso.com`.
2. DNS resolution returns an IP address.
3. Only after DNS succeeds does routing decide how to reach that IP.
4. Filtering then decides whether the traffic is allowed.

## Everyday-world mapping

- **Public DNS:** a public telephone directory anyone can use.
- **Private DNS:** an internal company directory only employees can use.
- **DNS delegation:** main reception tells you which department or receptionist is responsible for a particular area.

## Key distinction

- **DNS does not move the packet.**
- **DNS only tells the client which IP address to try.**

The sequence to remember is:

`Name → DNS resolution → IP address → Routing → Filtering → Destination`
