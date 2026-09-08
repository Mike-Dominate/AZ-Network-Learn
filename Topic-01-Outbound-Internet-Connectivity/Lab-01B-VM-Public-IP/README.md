# Lab 1B — Public IP on the VM

## Objective

Give the VM its own public IP address and verify that its outbound Internet traffic uses that public identity.

## Step 1 — Remove the NAT Gateway from the subnet

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

## Step 3 — Attach the public IP to the VM NIC

```powershell
az network nic ip-config update `
  --resource-group $RG `
  --nic-name $NIC `
  --name ipconfig1 `
  --public-ip-address $VMPIP
```

## Step 4 — Record the VM public IP

```powershell
az network public-ip show `
  --resource-group $RG `
  --name $VMPIP `
  --query ipAddress `
  --output tsv
```

Record the result as `<VM_PUBLIC_IP>`.

## Step 5 — Test from inside the VM

```bash
curl -4 -s https://api.ipify.org
echo
```

Record the result as `<OBSERVED_EGRESS_IP>`.

## Validation

The lab passes when:

```text
<OBSERVED_EGRESS_IP> = <VM_PUBLIC_IP>
```

## Traffic flow

```text
VM with public IP
      |
      | outbound traffic uses VM public identity
      v
Internet
      |
      | reply returns to VM public IP
      v
Azure networking
      |
      v
VM
```

## Why this matters

A public IP gives a single VM its own Internet identity. This can be useful for temporary test, administrative, or troubleshooting systems, but it should be used deliberately because the public identity belongs directly to that VM.

Next: [Lab 1C — Public Load Balancer outbound SNAT](../Lab-01C-Load-Balancer-Outbound/README.md)
