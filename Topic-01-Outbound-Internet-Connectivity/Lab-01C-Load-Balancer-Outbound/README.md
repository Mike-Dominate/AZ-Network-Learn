# Lab 1C — Public Load Balancer Outbound SNAT

## Objective

Remove the VM's direct public IP and use a Standard Public Load Balancer outbound rule to provide Internet connectivity.

## Step 1 — Remove the VM public IP

```powershell
az network nic ip-config update `
  --resource-group $RG `
  --nic-name $NIC `
  --name ipconfig1 `
  --remove publicIPAddress
```

Delete the old VM public IP:

```powershell
az network public-ip delete `
  --resource-group $RG `
  --name $VMPIP
```

The VM is private again:

```text
10.50.1.4
```

## Step 2 — Set Load Balancer variables

```powershell
$LB     = "lb-topic1-outbound"
$LBPIP  = "pip-lb-topic1-outbound"
$LBFE   = "fe-topic1-outbound"
$LBPOOL = "be-topic1-outbound"
$LBOUT  = "outbound-topic1"
```

## Step 3 — Create the Load Balancer public IP

```powershell
az network public-ip create `
  --resource-group $RG `
  --name $LBPIP `
  --location $LOC `
  --sku Standard `
  --allocation-method Static
```

Observed public IP:

```text
52.187.242.240
```

## Step 4 — Create the Standard Public Load Balancer

```powershell
az network lb create `
  --resource-group $RG `
  --name $LB `
  --location $LOC `
  --sku Standard `
  --public-ip-address $LBPIP `
  --frontend-ip-name $LBFE `
  --backend-pool-name $LBPOOL
```

## Step 5 — Add the VM NIC to the backend pool

```powershell
az network nic ip-config address-pool add `
  --resource-group $RG `
  --nic-name $NIC `
  --ip-config-name ipconfig1 `
  --lb-name $LB `
  --address-pool $LBPOOL
```

## Step 6 — Create the outbound rule

```powershell
az network lb outbound-rule create `
  --resource-group $RG `
  --lb-name $LB `
  --name $LBOUT `
  --protocol All `
  --address-pool $LBPOOL `
  --frontend-ip-configs $LBFE `
  --outbound-ports 1024 `
  --enable-tcp-reset true
```

## Step 7 — Verify the Load Balancer public IP

```powershell
az network public-ip show `
  --resource-group $RG `
  --name $LBPIP `
  --query ipAddress `
  --output tsv
```

Observed:

```text
52.187.242.240
```

## Step 8 — Test from inside the VM

In the Bastion terminal:

```bash
curl -4 -s https://api.ipify.org
echo
```

Observed:

```text
52.187.242.240
```

## Result

```text
VM private IP:       10.50.1.4
VM public IP:        None
LB frontend IP:      52.187.242.240
Internet observed:   52.187.242.240
Status:              PASS
```

## Traffic flow

```text
VM 10.50.1.4
      |
      v
Load Balancer backend pool
      |
      v
Outbound rule
      |
      v
SNAT to 52.187.242.240
      |
      v
Internet
      |
      | reply to 52.187.242.240
      v
Load Balancer
      |
      | connection mapping
      v
VM 10.50.1.4
```

## Beginner takeaway

The Load Balancer provides outbound connectivity only for backend pool members covered by the outbound rule. Multiple backend VMs can share the Load Balancer frontend public IP.

Back to: [Lab 1 overview](../LAB-01-README.md)
