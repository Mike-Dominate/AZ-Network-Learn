# Lab 1B — Public IP on the VM

## Objective

Remove the NAT Gateway from the traffic path and give the VM its own public IP address for outbound Internet connectivity.

## Step 1 — Detach the NAT Gateway

```powershell
az network vnet subnet update `
  --resource-group $RG `
  --vnet-name $VNET `
  --name $SNET `
  --remove natGateway
```

## Step 2 — Create a Standard public IP for the VM

```powershell
$VMPIP = "pip-vm-topic1-outbound"

az network public-ip create `
  --resource-group $RG `
  --name $VMPIP `
  --location $LOC `
  --sku Standard `
  --allocation-method Static
```

Observed public IP:

```text
20.213.95.101
```

## Step 3 — Attach the public IP to the VM NIC

```powershell
az network nic ip-config update `
  --resource-group $RG `
  --nic-name $NIC `
  --name ipconfig1 `
  --public-ip-address $VMPIP
```

## Step 4 — Verify the public IP

```powershell
az network public-ip show `
  --resource-group $RG `
  --name $VMPIP `
  --query ipAddress `
  --output tsv
```

Observed:

```text
20.213.95.101
```

## Step 5 — Test from inside the VM

In the Bastion terminal:

```bash
curl -4 -s https://api.ipify.org
echo
```

Observed:

```text
20.213.95.101
```

## Result

```text
VM private IP:      10.50.1.4
VM public IP:       20.213.95.101
Internet observed:  20.213.95.101
Status:             PASS
```

## Traffic flow

```text
VM
Private IP: 10.50.1.4
Public IP:  20.213.95.101
      |
      v
Internet
      |
      | reply to 20.213.95.101
      v
Azure networking
      |
      v
VM
```

## Beginner takeaway

In this scenario, the public identity belongs directly to the VM NIC. Unlike NAT Gateway, this public IP is specific to this VM.

Next: [Lab 1C — Public Load Balancer outbound SNAT](../Lab-01C-Load-Balancer-Outbound/README.md)
