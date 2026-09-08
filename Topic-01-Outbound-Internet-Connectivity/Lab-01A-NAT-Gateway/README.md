# Lab 1A — NAT Gateway

## Objective

Give the private VM outbound Internet access through an Azure NAT Gateway while keeping the VM itself without a public IP.

## Step 1 — Set variables

```powershell
$NAT    = "nat-topic1-outbound"
$NATPIP = "pip-nat-topic1-outbound"
```

## Step 2 — Create a Standard public IP

```powershell
az network public-ip create `
  --resource-group $RG `
  --name $NATPIP `
  --location $LOC `
  --sku Standard `
  --allocation-method Static
```

## Step 3 — Create the NAT Gateway

```powershell
az network nat gateway create `
  --resource-group $RG `
  --name $NAT `
  --location $LOC `
  --public-ip-addresses $NATPIP
```

## Step 4 — Associate the NAT Gateway with the workload subnet

```powershell
az network vnet subnet update `
  --resource-group $RG `
  --vnet-name $VNET `
  --name $SNET `
  --nat-gateway $NAT
```

## Step 5 — Record the NAT Gateway public IP

```powershell
az network public-ip show `
  --resource-group $RG `
  --name $NATPIP `
  --query ipAddress `
  --output tsv
```

Record the result as `<NAT_GATEWAY_PUBLIC_IP>`.

## Step 6 — Test from inside the VM

```bash
curl -4 -s https://api.ipify.org
echo
```

Record the result as `<OBSERVED_EGRESS_IP>`.

## Validation

The lab passes when:

```text
<OBSERVED_EGRESS_IP> = <NAT_GATEWAY_PUBLIC_IP>
```

The VM itself should still have no public IP.

## Traffic flow

```text
Private VM
    |
    v
Workload subnet
    |
    v
NAT Gateway
    |
    | SNAT to NAT Gateway public IP
    v
Internet
    |
    | response returns to NAT Gateway
    v
NAT Gateway
    |
    | connection mapping returns response
    v
Private VM
```

## Why this matters

NAT Gateway is useful when multiple private workloads need a shared, predictable outbound identity without receiving individual public IP addresses. It is associated with the **subnet**, so eligible resources in that subnet can use it for outbound connections.

Next: [Lab 1B — Public IP on the VM](../Lab-01B-VM-Public-IP/README.md)
