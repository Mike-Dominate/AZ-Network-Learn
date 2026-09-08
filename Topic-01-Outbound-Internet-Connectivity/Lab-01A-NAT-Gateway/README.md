# Lab 1A — NAT Gateway

## Objective

Give the private VM outbound Internet access using an Azure NAT Gateway.

The VM stays private:

```text
VM private IP: 10.50.1.4
VM public IP:  None
```

The NAT Gateway provides the public identity.

## Step 1 — Set variables

```powershell
$NAT    = "nat-topic1-outbound"
$NATPIP = "pip-nat-topic1-outbound"
```

## Step 2 — Create a Standard public IP for the NAT Gateway

```powershell
az network public-ip create `
  --resource-group $RG `
  --name $NATPIP `
  --location $LOC `
  --sku Standard `
  --allocation-method Static
```

Observed public IP:

```text
4.196.234.60
```

## Step 3 — Create the NAT Gateway

```powershell
az network nat gateway create `
  --resource-group $RG `
  --name $NAT `
  --location $LOC `
  --public-ip-addresses $NATPIP
```

## Step 4 — Attach the NAT Gateway to the workload subnet

```powershell
az network vnet subnet update `
  --resource-group $RG `
  --vnet-name $VNET `
  --name $SNET `
  --nat-gateway $NAT
```

## Step 5 — Verify the NAT public IP

```powershell
az network public-ip show `
  --resource-group $RG `
  --name $NATPIP `
  --query ipAddress `
  --output tsv
```

Observed:

```text
4.196.234.60
```

## Step 6 — Test from inside the VM

In the Bastion terminal:

```bash
curl -4 -s https://api.ipify.org
echo
```

Observed:

```text
4.196.234.60
```

## Result

```text
VM private IP:        10.50.1.4
NAT Gateway public:   4.196.234.60
Internet observed:    4.196.234.60
Status:               PASS
```

## Traffic flow

```text
VM 10.50.1.4
      |
      v
snet-workload
      |
      v
NAT Gateway
10.50.1.4 → 4.196.234.60
      |
      v
Internet
      |
      | reply to 4.196.234.60
      v
NAT Gateway
      |
      | maps the reply back
      v
VM 10.50.1.4
```

## Beginner takeaway

The NAT Gateway is attached to the subnet, not directly to one VM. Multiple eligible VMs in that subnet can therefore share the same outbound public identity.

Next: [Lab 1B — Public IP on the VM](../Lab-01B-VM-Public-IP/README.md)
