# Lab 1C — Public Load Balancer Outbound SNAT

## Objective

Remove the VM's direct public IP and provide outbound Internet connectivity through a Standard Public Load Balancer outbound rule.

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

## Step 7 — Record the Load Balancer frontend public IP

```powershell
az network public-ip show `
  --resource-group $RG `
  --name $LBPIP `
  --query ipAddress `
  --output tsv
```

Record the result as `<LOAD_BALANCER_PUBLIC_IP>`.

## Step 8 — Test from inside the VM

```bash
curl -4 -s https://api.ipify.org
echo
```

Record the result as `<OBSERVED_EGRESS_IP>`.

## Validation

The lab passes when:

```text
<OBSERVED_EGRESS_IP> = <LOAD_BALANCER_PUBLIC_IP>
```

The VM should have no public IP of its own.

## Traffic flow

```text
Private VM
      |
      v
Load Balancer backend pool
      |
      v
Outbound rule
      |
      | SNAT uses frontend public IP
      v
Internet
      |
      | reply returns to Load Balancer
      v
Load Balancer
      |
      | connection mapping returns response
      v
Private VM
```

## Why this matters

A Load Balancer outbound rule provides a shared public identity to selected backend pool members. This is useful when workloads are already part of a Standard Load Balancer design and outbound behavior needs to follow backend pool membership.

Back to: [Lab 1 overview](../LAB-01-README.md)
