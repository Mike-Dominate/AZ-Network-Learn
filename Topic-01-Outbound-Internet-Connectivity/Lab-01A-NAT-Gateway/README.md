# Lab 1A — NAT Gateway

## Objective

Give a private VM outbound Internet access using an Azure NAT Gateway while keeping the VM itself without a public IP.

> **Beginner note:** Azure assigns IP addresses when resources are created. Your values will normally be different from another learner's values. Validate relationships between resources instead of comparing literal IP addresses.

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

## Step 5 — Record the NAT Gateway public IP

```powershell
az network public-ip show `
  --resource-group $RG `
  --name $NATPIP `
  --query ipAddress `
  --output tsv
```

Record the value returned as:

```text
<NAT_GATEWAY_PUBLIC_IP>
```

## Step 6 — Test from inside the VM

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

`<OBSERVED_EGRESS_IP>` must match `<NAT_GATEWAY_PUBLIC_IP>`.

```text
VM public IP:                None
NAT Gateway public IP:       <NAT_GATEWAY_PUBLIC_IP>
Internet-observed source IP: <OBSERVED_EGRESS_IP>
Status:                      PASS when the two public IP values match
```

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
    | source is translated to the NAT Gateway public IP
    v
Internet destination
    |
    | reply returns to the NAT Gateway public IP
    v
NAT Gateway
    |
    | connection mapping sends the reply back
    v
Private VM
```

## Skill you are building

You are learning to provide **predictable shared outbound Internet connectivity for private workloads** without assigning a public IP to each VM.

### Where this skill is used in the real world

- Private application servers that must download updates or call external APIs.
- Workloads that connect to a partner or SaaS provider that requires a known source IP for an allowlist.
- Subnets containing multiple private VMs that should share controlled outbound connectivity.
- Cloud networking, platform engineering, infrastructure, and security operations roles.

## Beginner takeaway

A NAT Gateway is associated with a **subnet**. Eligible resources in that subnet can use the NAT Gateway's public IP for outbound connections.

Next: [Lab 1B — Public IP on the VM](../Lab-01B-VM-Public-IP/README.md)
