# Lab 2A — Same VNet Communication

## Objective

Prove that two private VMs in different subnets of the same VNet can communicate using Azure system routing.

## Step 1 — Connect to the server VM

Use Azure Bastion Developer and sign in to `vm-topic2-server`.

Start a temporary HTTP server:

```bash
nohup python3 -m http.server 8080 --bind 0.0.0.0 >/tmp/topic2-http.log 2>&1 &
```

Verify:

```bash
ss -lnt | grep 8080
```

Expected: port `8080` is listening.

## Step 2 — Connect to the client VM

Use Bastion to connect to `vm-topic2-client`.

Ping the server VM's private IP:

```bash
ping -c 4 <SERVER_PRIVATE_IP>
```

Then test the web service:

```bash
curl -v http://<SERVER_PRIVATE_IP>:8080
```

## Expected result

- Ping succeeds.
- HTTP request returns `200 OK`.
- Neither VM requires a public IP.
- Traffic stays inside the VNet.

## Traffic flow

```text
Client VM
snet-client
    |
    | destination belongs to the same VNet address space
    v
Azure system route: VnetLocal
    |
    v
snet-server
Server VM
```

## What this proves

Subnets do not automatically isolate workloads from each other. Azure installs system routes for the VNet address space, so resources in different subnets can communicate unless another control such as an NSG, UDR, firewall, or guest firewall blocks the traffic.

## Real-world application

This pattern is common in multi-tier applications:

```text
snet-web
   |
snet-app
   |
snet-data
```

Use separate subnets when you need different security policies, routing policies, scaling boundaries, or operational ownership while keeping the workloads in one VNet.

Typical production examples:

- Web servers communicating with application servers.
- Application servers communicating with databases.
- Shared monitoring or management services inside the same VNet.
- Internal microservices using private IP connectivity.

## Beginner takeaway

**Same VNet = Azure already knows the road between the subnets.**

Next: [Lab 2B — Service Endpoint](../Lab-02B-Service-Endpoint/README.md)
