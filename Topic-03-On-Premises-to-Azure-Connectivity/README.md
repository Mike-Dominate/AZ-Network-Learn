# Topic 3 — Communication Between On-Premises and Azure

This topic covers three common ways users and on-premises networks connect to Azure:

1. **Point-to-Site (P2S) VPN** — one device connects securely to Azure over the internet.
2. **Site-to-Site (S2S) VPN** — an entire on-premises network connects to an Azure VNet through an IPsec VPN tunnel over the internet.
3. **ExpressRoute** — an on-premises network connects privately to Microsoft through a connectivity provider instead of using the public internet as the transport path.

## Traffic-flow diagram

![On-premises to Azure connectivity](./azure_onprem_to_azure_connectivity.png)

## Core mental model

- **P2S VPN:** one device → encrypted VPN tunnel → Azure VPN Gateway → Azure VNet.
- **S2S VPN:** on-premises network → VPN device → IPsec tunnel → Azure VPN Gateway → Azure VNet.
- **ExpressRoute:** on-premises network → connectivity provider/private circuit → Microsoft network → ExpressRoute Gateway → Azure VNet.

## Everyday-world mapping

- **P2S:** one employee uses a personal access badge to enter the office.
- **S2S:** two office buildings are linked by a secure tunnel so everyone in one building can reach the other.
- **ExpressRoute:** the company leases a private road between its site and Microsoft instead of using public roads.

## Quick decision rule

- One remote user/device → **P2S VPN**
- Whole branch/site → **S2S VPN**
- Enterprise private connectivity → **ExpressRoute**
