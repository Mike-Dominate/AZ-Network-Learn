# Lab 2B — Service Endpoint to Azure Storage

## Objective

Prove that an Azure Storage account can keep its standard public endpoint while access is restricted to a trusted subnet using a VNet Service Endpoint and Storage firewall rule.

## Step 1 — Create a Storage account and proof blob

Set variables:

```powershell
$STG = "sttopic2$(Get-Random -Minimum 10000 -Maximum 99999)"
$CONTAINER = "topic2"
$BLOB = "proof.txt"
```

Create the Storage account:

```powershell
az storage account create `
  --name $STG `
  --resource-group $RG `
  --location $LOC `
  --sku Standard_LRS `
  --kind StorageV2 `
  --https-only true `
  --min-tls-version TLS1_2
```

Get an account key:

```powershell
$STGKEY = az storage account keys list `
  --resource-group $RG `
  --account-name $STG `
  --query "[0].value" `
  --output tsv
```

Create a proof file:

```powershell
"Topic 2 Service Endpoint test succeeded" |
  Set-Content "$env:TEMP\topic2-proof.txt"
```

Create the container and upload the blob:

```powershell
az storage container create `
  --name $CONTAINER `
  --account-name $STG `
  --account-key $STGKEY

az storage blob upload `
  --account-name $STG `
  --account-key $STGKEY `
  --container-name $CONTAINER `
  --name $BLOB `
  --file "$env:TEMP\topic2-proof.txt" `
  --overwrite
```

## Step 2 — Create a temporary read-only SAS URL

```powershell
$EXPIRY = (Get-Date).ToUniversalTime().AddHours(2).ToString("yyyy-MM-ddTHH:mmZ")

$SAS = az storage blob generate-sas `
  --account-name $STG `
  --account-key $STGKEY `
  --container-name $CONTAINER `
  --name $BLOB `
  --permissions r `
  --expiry $EXPIRY `
  --https-only `
  --output tsv

$BLOBURL = "https://${STG}.blob.core.windows.net/${CONTAINER}/${BLOB}?${SAS}"
```

## Step 3 — Enable the Storage Service Endpoint on the client subnet only

```powershell
az network vnet subnet update `
  --resource-group $RG `
  --vnet-name $VNET `
  --name $SNETCLIENT `
  --service-endpoints Microsoft.Storage
```

## Step 4 — Restrict the Storage account to the client subnet

```powershell
az storage account network-rule add `
  --resource-group $RG `
  --account-name $STG `
  --vnet-name $VNET `
  --subnet $SNETCLIENT

az storage account update `
  --resource-group $RG `
  --name $STG `
  --default-action Deny
```

Verify the Service Endpoint:

```powershell
az network vnet subnet show `
  --resource-group $RG `
  --vnet-name $VNET `
  --name $SNETCLIENT `
  --query "{Subnet:name,ServiceEndpoints:serviceEndpoints[].service}" `
  --output json
```

Verify the Storage network rule:

```powershell
az storage account show `
  --resource-group $RG `
  --name $STG `
  --query "{DefaultAction:networkRuleSet.defaultAction,VirtualNetworkRules:networkRuleSet.virtualNetworkRules[].virtualNetworkResourceId}" `
  --output json
```

## Step 5 — Test from the trusted client subnet

On `vm-topic2-client`, set the complete SAS URL using Bash syntax with no spaces around `=`:

```bash
BLOBURL='<COMPLETE_SAS_URL>'
```

Then:

```bash
curl -i --connect-timeout 5 --max-time 10 "$BLOBURL"
```

Expected: HTTP `200 OK` and the proof text.

## Step 6 — Test from the untrusted server subnet

On `vm-topic2-server`, use the same SAS URL:

```bash
BLOBURL='<COMPLETE_SAS_URL>'
curl -i --connect-timeout 5 --max-time 10 "$BLOBURL"
```

Expected: HTTP `403` / authorization failure.

## Step 7 — Inspect effective routes

```powershell
az network nic show-effective-route-table `
  --resource-group $RG `
  --name $NICCLIENT `
  --output table

az network nic show-effective-route-table `
  --resource-group $RG `
  --name $NICSERVER `
  --output table
```

The client NIC should show one or more `VirtualNetworkServiceEndpoint` routes. The server NIC should not.

## Traffic flow

```text
Trusted subnet
    |
    | Microsoft.Storage Service Endpoint
    v
Azure backbone
    |
    v
Storage public endpoint
    |
    | Storage firewall recognizes trusted subnet
    v
ALLOW
```

The untrusted subnet reaches the service differently and is denied by the Storage network policy.

## What this proves

A Service Endpoint does **not** give the Storage account a private IP. The service still uses its normal Azure Storage endpoint, but the subnet receives service-endpoint routing and can be trusted by the service firewall.

## Real-world application

Use this when:

- Azure PaaS access should be limited to known subnets.
- You want a simple subnet-based access control model.
- The service can keep its standard endpoint.
- You do not require Private Link or private DNS architecture.

Typical examples include trusted application subnets accessing supported Storage or SQL services.

## Beginner takeaway

**Service Endpoint = the Azure service trusts my subnet while keeping its normal service endpoint.**

Next: [Lab 2C — Private Endpoint](../Lab-02C-Private-Endpoint/README.md)
