# Lab 1C — Public Load Balancer Outbound SNAT

## Objective

Remove the VM's direct public IP and use a Standard Public Load Balancer outbound rule to provide Internet connectivity for backend pool members.

> **Beginner note:** Azure assigns the actual frontend public IP when the resource is created. Your value will normally be different from another learner's value. Validate that the Internet-observed source IP matches the Load Balancer frontend public IP.

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

Record the value returned as:

```text
<LOAD_BALANCER_PUBLIC_IP>
```

## Step 8 — Test from inside the VM

In the Bastion terminal:

```bash
curl -4 -s https://api.ipify.org
echo
```

Record the value returned as:

```text
<OBSERVED_EGRESS_IP>
```

## Expected result

`<OBSERVED_EGRESS_IP>` must match `<LOAD_BALANCER_PUBLIC_IP>`.

```text
VM public IP:                None
LB frontend public IP:       <LOAD_BALANCER_PUBLIC_IP>
Internet-observed source IP: <OBSERVED_EGRESS_IP>
Status:                      PASS when the two public IP values match
```

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
      | SNAT uses the Load Balancer frontend public IP
      v
Internet destination
      |
      | reply returns to the frontend public IP
      v
Load Balancer
      |
      | connection mapping returns the reply
      v
Private VM
```

## Skill you are building

You are learning how to provide **shared outbound connectivity to selected backend workloads** through a Standard Public Load Balancer outbound rule.

### Where this skill is used in the real world

- Groups of web or application servers already placed behind a Standard Load Balancer.
- Environments where backend pool membership determines which workloads share the frontend public identity.
- Troubleshooting SNAT and outbound connectivity for load-balanced applications.
- Cloud networking, infrastructure engineering, platform operations, and production support roles.

## Beginner takeaway

A Load Balancer outbound rule applies to members of its **backend pool**. Multiple backend VMs can therefore share the Load Balancer frontend public IP for outbound connections.

Back to: [Lab 1 overview](../LAB-01-README.md)
