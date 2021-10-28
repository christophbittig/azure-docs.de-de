---
title: 'Azure CLI: Einschränken des Import-/Exportzugriffs auf verwaltete Datenträger mit privaten Links'
description: Aktivieren Sie mit der Azure CLI private Links für Ihre verwalteten Datenträger. Dadurch können Sie Datenträger nur innerhalb Ihres virtuellen Netzwerks sicher exportieren und importieren.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 07/15/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: fa4bdcd3e9eccb3c8911dda758de431e7c459ce2
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130236749"
---
# <a name="azure-cli---restrict-importexport-access-for-managed-disks-with-private-links"></a>Azure CLI: Einschränken des Import-/Exportzugriffs auf verwaltete Datenträger mit privaten Links

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Sie können [private Endpunkte](../../private-link/private-endpoint-overview.md) verwenden, um den Export und Import verwalteter Datenträger einzuschränken und sicheren Zugriff auf Daten über einen [privaten Link](../../private-link/private-link-overview.md) von Clients in Ihrem virtuellen Azure-Netzwerk zu ermöglichen. Der private Endpunkt verwendet eine IP-Adresse aus dem Adressraum des virtuellen Netzwerks für Ihren Dienst für verwaltete Datenträger. Der Netzwerkdatenverkehr zwischen den Clients im virtuellen Netzwerk und den verwalteten Datenträgern wird nur über das virtuelle Netzwerk und eine private Verbindung im Microsoft-Backbonenetzwerk geleitet und somit nicht dem öffentlichen Internet ausgesetzt.

Wenn Sie private Links zum Exportieren und Importieren verwalteter Datenträger verwenden möchten, erstellen Sie zunächst eine Datenträgerzugriffsressource und verknüpfen sie mit einem virtuellen Netzwerk im gleichen Abonnement, indem Sie einen privaten Endpunkt erstellen. Ordnen Sie dann einer Instanz des Datenträgerzugriffs einen Datenträger oder eine Momentaufnahme zu. Legen Sie abschließend die NetworkAccessPolicy-Eigenschaft des Datenträgers oder der Momentaufnahme auf `AllowPrivate` fest. Dadurch wird der Zugriff auf das virtuelle Netzwerk beschränkt. 

Sie können die NetworkAccessPolicy-Eigenschaft auf `DenyAll` festlegen, um zu verhindern, dass Daten auf einem Datenträger oder in einer Momentaufnahme exportiert werden. Der Standardwert für die NetworkAccessPolicy-Eigenschaft ist `AllowAll`.

## <a name="limitations"></a>Einschränkungen

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../../includes/virtual-machines-disks-private-links-limitations.md)]


## <a name="log-in-into-your-subscription-and-set-your-variables"></a>Anmelden bei Ihrem Abonnement und Festlegen Ihrer Variablen

```azurecli-interactive
subscriptionId=yourSubscriptionId
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessForPrivateLinks
vnetName=yourVNETForPrivateLinks
subnetName=yourSubnetForPrivateLinks
privateEndPointName=yourPrivateLinkForSecureMDExportImport
privateEndPointConnectionName=yourPrivateLinkConnection

#The name of an existing disk which is the source of the snapshot
sourceDiskName=yourSourceDiskForSnapshot

#The name of the new snapshot which will be secured via Private Links
snapshotNameSecuredWithPL=yourSnapshotNameSecuredWithPL

az login

az account set --subscription $subscriptionId

```

## <a name="create-a-disk-access-using-azure-cli"></a>Erstellen eines Datenträgerzugriffs mithilfe der Azure CLI
```azurecli
az disk-access create -n $diskAccessName -g $resourceGroupName -l $region

diskAccessId=$(az disk-access show -n $diskAccessName -g $resourceGroupName --query [id] -o tsv)
```

## <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

Netzwerkrichtlinien wie Netzwerksicherheitsgruppen (NSG) werden für private Endpunkte nicht unterstützt. Zum Bereitstellen eines privaten Endpunkts in einem bestimmten Subnetz ist eine explizite Einstellung zum Deaktivieren in diesem Subnetz erforderlich. 

```azurecli
az network vnet create --resource-group $resourceGroupName \
    --name $vnetName \
    --subnet-name $subnetName
```
## <a name="disable-subnet-private-endpoint-policies"></a>Deaktivieren von Richtlinien für den privaten Endpunkt im Subnetz

Azure stellt Ressourcen für ein Subnetz innerhalb eines virtuellen Netzwerks bereit. Daher müssen Sie das Subnetz aktualisieren, um die Netzwerkrichtlinien für den privaten Endpunkt zu deaktivieren. 

```azurecli
az network vnet subnet update --resource-group $resourceGroupName \
    --name $subnetName  \
    --vnet-name $vnetName \
    --disable-private-endpoint-network-policies true
```
## <a name="create-a-private-endpoint-for-the-disk-access-object"></a>Erstellen eines privaten Endpunkts für das Datenträgerzugriffsobjekt

```azurecli
az network private-endpoint create --resource-group $resourceGroupName \
    --name $privateEndPointName \
    --vnet-name $vnetName  \
    --subnet $subnetName \
    --private-connection-resource-id $diskAccessId \
    --group-ids disks \
    --connection-name $privateEndPointConnectionName
```

## <a name="configure-the-private-dns-zone"></a>Konfigurieren der privaten DNS-Zone

Erstellen Sie eine private DNS-Zone für die Storage Blob-Domäne, eine Zuordnungsverknüpfung mit dem virtuellen Netzwerk und eine DNS-Zonengruppe, um den privaten Endpunkt der privaten DNS-Zone zuzuordnen. 

```azurecli
az network private-dns zone create --resource-group $resourceGroupName \
    --name "privatelink.blob.core.windows.net"

az network private-dns link vnet create --resource-group $resourceGroupName \
    --zone-name "privatelink.blob.core.windows.net" \
    --name yourDNSLink \
    --virtual-network $vnetName \
    --registration-enabled false 

az network private-endpoint dns-zone-group create \
   --resource-group $resourceGroupName \
   --endpoint-name $privateEndPointName \
   --name yourZoneGroup \
   --private-dns-zone "privatelink.blob.core.windows.net" \
   --zone-name disks
```

## <a name="create-a-disk-protected-with-private-links"></a>Erstellen eines Datenträgers, der durch private Links geschützt ist
```azurecli-interactive
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessName
diskName=yourDiskName
diskSkuName=Standard_LRS
diskSizeGB=128

diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)

az disk create -n $diskName \
-g $resourceGroupName \
-l $region \
--size-gb $diskSizeGB \
--sku $diskSkuName \
--network-access-policy AllowPrivate \
--disk-access $diskAccessId 
```

## <a name="create-a-snapshot-of-a-disk-protected-with-private-links"></a>Erstellen einer Momentaufnahme eines Datenträgers, der durch private Links geschützt ist
```azurecli-interactive
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessName
sourceDiskName=yourSourceDiskForSnapshot
snapshotNameSecuredWithPL=yourSnapshotName

diskId=$(az disk show -n $sourceDiskName -g $resourceGroupName --query [id] -o tsv)

diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)

az snapshot create -n $snapshotNameSecuredWithPL \
-g $resourceGroupName \
-l $region \
--source $diskId \
--network-access-policy AllowPrivate \
--disk-access $diskAccessId 
```

## <a name="next-steps"></a>Nächste Schritte

- Hochladen einer VHD in Azure oder Kopieren eines verwalteten Datenträgers in eine andere Region: [Azure CLI](disks-upload-vhd-to-managed-disk-cli.md) oder [Azure PowerShell-Modul](../windows/disks-upload-vhd-to-managed-disk-powershell.md)
- Herunterladen einer VHD: [Windows](../windows/download-vhd.md) oder [Linux](download-vhd.md)
- [Häufig gestellte Fragen zu privaten Links](../faq-for-disks.yml)
- [Exportieren/Kopieren verwalteter Momentaufnahmen als VHD in ein Speicherkonto in einer anderen Region mit der Befehlszeilenschnittstelle](/previous-versions/azure/virtual-machines/scripts/virtual-machines-cli-sample-copy-managed-disks-vhd)
