# Lab 2D — VNet Peering

## Objective

Prove that two separate VNets cannot communicate by default, then connect them with VNet Peering and verify the routing change.

## Step 1 — Set peer VNet variables

```powershell
$VNETPEER    = "vnet-topic2-peer-aue"
$SNETPEER    = "snet-peer"
$VMPEER      = "vm-topic2-peer"
$NICPEER     = "nic-topic2-peer"
$BASTIONPEER = "bas-topic2-peer"
```

## Step 2 — Create the second VNet and subnet

```powershell
az network vnet create `
  --resource-group $RG `
  --name $VNETPEER `
  --location $LOC `
  --address-prefixes 10.70.0.0/16 `
  --subnet-name $SNETPEER `
  --subnet-prefixes 10.70.1.0/24

az network vnet subnet update `
  --resource-group $RG `
  --vnet-name $VNETPEER `
  --name $SNETPEER `
  --default-outbound false
```

## Step 3 — Create the peer VM

```powershell
az network nic create `
  --resource-group $RG `
  --name $NICPEER `
  --location $LOC `
  --vnet-name $VNETPEER `
  --subnet $SNETPEER

$PEERPASSWORD = Read-Host "Enter a strong lab password"

az vm create `
  --resource-group $RG `
  --name $VMPEER `
  --location $LOC `
  --nics $NICPEER `
  --image Ubuntu2204 `
  --size $VMSIZE `
  --admin-username azureuser `
  --authentication-type password `
  --admin-password $PEERPASSWORD
```

Verify that the VM has a private IP and no public IP:

```powershell
az vm list-ip-addresses `
  --resource-group $RG `
  --query "[?virtualMachine.name=='$VMPEER'].{VM:virtualMachine.name,PrivateIP:virtualMachine.network.privateIpAddresses[0],PublicIP:virtualMachine.network.publicIpAddresses[0].ipAddress}" `
  --output table
```

Record the peer VM private IP as:

```text
<PEER_VM_PRIVATE_IP>
```

## Step 4 — Create Bastion Developer for the peer VNet

```powershell
az network bastion create `
  --name $BASTIONPEER `
  --resource-group $RG `
  --vnet-name $VNETPEER `
  --location $LOC `
  --sku Developer
```

## Step 5 — Start a web service on the peer VM

Connect to `vm-topic2-peer` through Bastion and run:

```bash
nohup python3 -m http.server 8080 --bind 0.0.0.0 >/tmp/topic2-peer-http.log 2>&1 &
ss -lnt | grep 8080
```

## Step 6 — Prove the pre-peering failure

Connect to `vm-topic2-client` and test the peer VM:

```bash
ping -c 4 <PEER_VM_PRIVATE_IP>
```

```bash
curl -v --connect-timeout 5 --max-time 10 http://<PEER_VM_PRIVATE_IP>:8080
```

Expected before peering:

- Ping fails.
- HTTP connection times out.

## Step 7 — Create peering in both directions

```powershell
az network vnet peering create `
  --resource-group $RG `
  --name "peer-core-to-peer" `
  --vnet-name $VNET `
  --remote-vnet $VNETPEER `
  --allow-vnet-access

az network vnet peering create `
  --resource-group $RG `
  --name "peer-peer-to-core" `
  --vnet-name $VNETPEER `
  --remote-vnet $VNET `
  --allow-vnet-access
```

Verify both directions:

```powershell
az network vnet peering list `
  --resource-group $RG `
  --vnet-name $VNET `
  --query "[].{Name:name,State:peeringState}" `
  --output table

az network vnet peering list `
  --resource-group $RG `
  --vnet-name $VNETPEER `
  --query "[].{Name:name,State:peeringState}" `
  --output table
```

Expected state: `Connected` in both directions.

## Step 8 — Repeat the connectivity test

From `vm-topic2-client`:

```bash
ping -c 4 <PEER_VM_PRIVATE_IP>
```

```bash
curl -v --connect-timeout 5 --max-time 10 http://<PEER_VM_PRIVATE_IP>:8080
```

Expected after peering:

- Ping succeeds.
- HTTP request returns `200 OK`.

## Step 9 — Inspect the effective route

```powershell
az network nic show-effective-route-table `
  --resource-group $RG `
  --name $NICCLIENT `
  --output table
```

Look for the remote VNet address space with next-hop type:

```text
VNetPeering
```

## Traffic flow

```text
VNet A
   |
   | route to remote VNet -> VNetPeering
   v
Microsoft backbone
   |
   v
VNet B
   |
   v
Peer VM
```

## What this proves

Separate VNets are separate routing domains. They do not automatically communicate because they are in the same subscription or region. VNet Peering creates private routing between the address spaces.

## Real-world application

Use VNet Peering for:

- Hub-and-spoke architectures.
- Shared-services VNets containing DNS, firewalls, monitoring, or domain services.
- Dev, test, production, or business-unit networks that need private connectivity.
- Cross-subscription private connectivity where supported and intentionally configured.
- Regional application architectures where separate VNets must communicate privately.

> **Important:** VNet Peering is not automatically transitive. If VNet A is peered with a hub and VNet B is also peered with that hub, A and B do not automatically gain ordinary routed communication through the hub without additional routing or gateway design.

## Beginner takeaway

**VNet Peering = build a private road between two separate Azure networks.**

Back to: [Lab 2 overview](../LAB-02-README.md)
