# Topic 4 — Filtering Network Traffic

This topic focuses on how Azure decides whether traffic is allowed after a route exists.

1. **Network Security Group (NSG)** — rule-based allow/deny filtering applied at a subnet or NIC boundary.
2. **Network Virtual Appliance (NVA)** — a routed network device that can inspect, filter, and forward traffic.

## Traffic-flow diagram

![Filtering network traffic](./azure_network_traffic_filtering_infographic.png)

## Core mental model

- **Routing:** where should the packet go?
- **Filtering:** is the packet allowed to continue?
- **NSG:** checks source, destination, protocol, and ports, then allows or denies traffic.
- **NVA:** only sees traffic that routing deliberately sends through it; it can inspect, filter, and forward.

## Everyday-world mapping

- **NSG:** a security guard at a doorway checks whether a visitor is allowed through.
- **NVA:** a road checkpoint that vehicles must be routed through before they can continue.

## Quick distinction

- **NSG = rule attached to the network boundary.**
- **NVA = device placed in the traffic path.**

A route can exist while security still blocks the packet, so troubleshoot in this order:

1. Does a route exist?
2. Is the packet allowed?
