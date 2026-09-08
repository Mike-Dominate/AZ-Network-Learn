# Lab 1B — Public IP on the VM

## Objective

Give a VM its own public IP address and verify that its outbound Internet traffic uses that public identity.

> **Beginner note:** Azure assigns the actual IP address when the resource is created. Your value will normally be different from another learner's value. Validate that the IP observed from the Internet matches the public IP attached to your VM.

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

Record the value returned as:

```text
<VM_PUBLIC_IP>
```

## Step 5 — Test from inside the VM

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

`<OBSERVED_EGRESS_IP>` must match `<VM_PUBLIC_IP>`.

```text
VM public IP:                <VM_PUBLIC_IP>
Internet-observed source IP: <OBSERVED_EGRESS_IP>
Status:                      PASS when the values match
```

## Traffic flow

```text
VM with public IP
      |
      | outbound request uses the VM public identity
      v
Internet destination
      |
      | reply returns to the VM public IP
      v
Azure networking
      |
      v
VM
```

## Skill you are building

You are learning how to give a single Azure VM a **direct public network identity** and verify how that identity is used for outbound traffic.

### Where this skill is used in the real world

- Small test or demonstration VMs that need direct Internet connectivity.
- Temporary administrative or troubleshooting systems where a dedicated public identity is acceptable.
- Understanding why assigning public IPs broadly can increase the exposure and management burden of an environment.
- Cloud support, systems administration, infrastructure engineering, and network operations roles.

## Beginner takeaway

In this scenario, the public identity belongs directly to the VM NIC. It is specific to that VM rather than shared by a whole subnet or a Load Balancer backend pool.

Next: [Lab 1C — Public Load Balancer outbound SNAT](../Lab-01C-Load-Balancer-Outbound/README.md)
