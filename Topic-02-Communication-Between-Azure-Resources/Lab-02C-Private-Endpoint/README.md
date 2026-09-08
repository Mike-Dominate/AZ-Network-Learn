# Lab 2C — Private Endpoint to Azure Storage

## Objective

Replace the Service Endpoint design with Azure Private Link so the Storage account is reached through a private IP inside the VNet and public network access can be disabled.

## Step 1 — Create a Private Endpoint subnet

```powershell
$SNETPE = "snet-private-endpoint"

az network vnet subnet create `
  --resource-group $RG `
  --vnet-name $VNET `
  --name $SNETPE `
  --address-prefixes 10.60.3.0/24
```

## Step 2 — Remove the Service Endpoint design

```powershell
az network vnet subnet update `
  --resource-group $RG `
  --vnet-name $VNET `
  --name $SNETCLIENT `
  --remove serviceEndpoints

az storage account network-rule remove `
  --resource-group $RG `
  --account-name $STG `
  --vnet-name $VNET `
  --subnet $SNETCLIENT
```

Verify that the client subnet no longer has Service Endpoints:

```powershell
az network vnet subnet show `
  --resource-group $RG `
  --vnet-name $VNET `
  --name $SNETCLIENT `
  --query "{Subnet:name,ServiceEndpoints:serviceEndpoints}" `
  --output json
```

## Step 3 — Set Private Endpoint variables

```powershell
$PE       = "pe-topic2-storage-blob"
$PECONN   = "pec-topic2-storage-blob"
$DNSZONE  = "privatelink.blob.core.windows.net"
$DNSLINK  = "link-topic2-core"
$DNSGROUP = "pdzg-topic2-storage"

$STGID = az storage account show `
  --resource-group $RG `
  --name $STG `
  --query id `
  --output tsv
```

## Step 4 — Create the Storage blob Private Endpoint

```powershell
az network private-endpoint create `
  --resource-group $RG `
  --name $PE `
  --location $LOC `
  --vnet-name $VNET `
  --subnet $SNETPE `
  --private-connection-resource-id $STGID `
  --group-id blob `
  --connection-name $PECONN
```

## Step 5 — Create and link the Private DNS zone

```powershell
az network private-dns zone create `
  --resource-group $RG `
  --name $DNSZONE

az network private-dns link vnet create `
  --resource-group $RG `
  --zone-name $DNSZONE `
  --name $DNSLINK `
  --virtual-network $VNET `
  --registration-enabled false
```

## Step 6 — Associate the Private Endpoint with Private DNS

```powershell
az network private-endpoint dns-zone-group create `
  --resource-group $RG `
  --endpoint-name $PE `
  --name $DNSGROUP `
  --private-dns-zone $DNSZONE `
  --zone-name $DNSZONE
```

## Step 7 — Disable public network access on Storage

```powershell
az storage account update `
  --resource-group $RG `
  --name $STG `
  --public-network-access Disabled
```

## Step 8 — Verify the Private Endpoint

```powershell
az network private-endpoint show `
  --resource-group $RG `
  --name $PE `
  --query "{Name:name,State:provisioningState,PrivateIP:customDnsConfigs[0].ipAddresses[0]}" `
  --output json
```

Record the private IP assigned from `snet-private-endpoint` as:

```text
<PRIVATE_ENDPOINT_IP>
```

Verify the connection status:

```powershell
az storage account show `
  --resource-group $RG `
  --name $STG `
  --query "privateEndpointConnections[].{Status:privateLinkServiceConnectionState.status,Description:privateLinkServiceConnectionState.description}" `
  --output table
```

Expected status: `Approved`.

## Step 9 — Prove Private DNS resolution

On `vm-topic2-client`:

```bash
getent ahostsv4 <STORAGE_ACCOUNT_NAME>.blob.core.windows.net
```

The normal Storage hostname should resolve through the `privatelink.blob.core.windows.net` zone to `<PRIVATE_ENDPOINT_IP>`.

## Step 10 — Test Storage access privately

Use a valid read-only SAS URL:

```bash
BLOBURL='<COMPLETE_SAS_URL>'
curl -i --connect-timeout 5 --max-time 10 "$BLOBURL"
```

Expected: HTTP `200 OK`.

Repeat the DNS and HTTP tests from `vm-topic2-server`. Because the Private DNS zone is linked to the VNet and both VMs can route to the private endpoint, both should succeed.

## Step 11 — Inspect effective routes

```powershell
az network nic show-effective-route-table `
  --resource-group $RG `
  --name $NICCLIENT `
  --output table
```

Look for a host route to the Private Endpoint IP with next-hop type `InterfaceEndpoint`.

## Traffic flow

```text
VM
 |
 | DNS query for normal Storage hostname
 v
Private DNS
 |
 | returns private endpoint IP
 v
Private Endpoint
 |
 | Azure Private Link
 v
Azure Storage
```

## What this proves

The application can keep using the normal Storage hostname while DNS redirects the connection to a private endpoint IP inside the VNet. Public network access to the Storage account can be disabled.

## Real-world application

Use Private Endpoint when:

- PaaS services must be privately reachable.
- Public network access must be disabled.
- Regulatory or sensitive workloads require private connectivity.
- On-premises systems need private access to Azure PaaS through VPN or ExpressRoute.
- Zero-trust or enterprise architectures require private service exposure.

Common examples include Storage, Azure SQL, Key Vault, Azure OpenAI, and other services that support Private Link.

## Beginner takeaway

**Private Endpoint = bring a private doorway to the Azure service into your network.**

Next: [Lab 2D — VNet Peering](../Lab-02D-VNet-Peering/README.md)
