---
title: Erstellen einer inkrementellen Momentaufnahme
description: Erfahren Sie mehr über inkrementelle Momentaufnahmen für verwaltete Datenträger und darüber, wie sie mit dem Azure-Portal, PowerShell und Azure Resource Manager erstellt werden.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: devx-track-azurepowershell, ignite-fall-2021
ms.openlocfilehash: ba03ec11522ea5a4e4a011d1e62fa09b25aec749
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131022296"
---
# <a name="create-an-incremental-snapshot-for-managed-disks"></a>Erstellen einer inkrementellen Momentaufnahme für verwaltete Datenträger

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](../../includes/virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>Beschränkungen

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](../../includes/virtual-machines-disks-incremental-snapshots-restrictions.md)]

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Sie können die Azure-Befehlszeilenschnittstelle verwenden, um eine inkrementelle Momentaufnahme zu erstellen. Hierzu benötigen Sie die aktuellste Version der Azure-Befehlszeilenschnittstelle. Informationen zum [Installieren](/cli/azure/install-azure-cli) oder [Aktualisieren](/cli/azure/update-azure-cli) der Azure-Befehlszeilenschnittstelle finden Sie in den folgenden Artikeln.

Mit dem folgenden Skript wird eine inkrementelle Momentaufnahme von einem bestimmten Datenträger erstellt:

```azurecli
# Declare variables
diskName="yourDiskNameHere"
resourceGroupName="yourResourceGroupNameHere"
snapshotName="desiredSnapshotNameHere"

# Get the disk you need to backup
yourDiskID=$(az disk show -n $diskName -g $resourceGroupName --query "id" --output tsv)

# Create the snapshot
az snapshot create -g $resourceGroupName -n $snapshotName --source $yourDiskID --incremental true
```

Sie können inkrementelle Momentaufnahmen auf dem gleichen Datenträger mit der Eigenschaft `SourceResourceId` der Momentaufnahmen identifizieren. `SourceResourceId` ist die Azure Resource Manager-Ressourcen-ID des übergeordneten Datenträgers.

Sie können `SourceResourceId` verwenden, um eine Liste aller Momentaufnahmen zu erstellen, die einem bestimmten Datenträger zugeordnet sind. Ersetzen Sie `yourResourceGroupNameHere` durch Ihren Wert. Anschließend können Sie das folgende Beispiel verwenden, um Ihre vorhandenen inkrementellen Momentaufnahmen aufzulisten:


```azurecli
# Declare variables and create snapshot list
subscriptionId="yourSubscriptionId"
resourceGroupName="yourResourceGroupNameHere"
diskName="yourDiskNameHere"

az account set --subscription $subscriptionId

diskId=$(az disk show -n $diskName -g $resourceGroupName --query [id] -o tsv)

az snapshot list --query "[?creationData.sourceResourceId=='$diskId' && incremental]" -g $resourceGroupName --output table
```


# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Sie können das Azure PowerShell-Modul verwenden, um eine inkrementelle Momentaufnahme zu erstellen. Sie benötigen die neueste Version des Azure PowerShell-Moduls. Mit dem folgenden Befehl wird sie installiert oder Ihre vorhandene Installation auf die neueste Version aktualisiert:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Melden Sie sich nach der Installation bei der PowerShell-Sitzung mit `Connect-AzAccount` an.

Um eine inkrementelle Momentaufnahme mit Azure PowerShell zu erstellen, legen Sie die Konfiguration mit [New-AzSnapShotConfig](/powershell/module/az.compute/new-azsnapshotconfig) mit dem Parameter `-Incremental` fest und übergeben diese dann als Variable über den Parameter `-Snapshot` an [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot).

```PowerShell
$diskName = "yourDiskNameHere>"
$resourceGroupName = "yourResourceGroupNameHere"
$snapshotName = "yourDesiredSnapshotNameHere"

# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName $diskName -ResourceGroupName $resourceGroupName

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName -Snapshot $snapshotConfig 
```

Sie können inkrementelle Momentaufnahmen auf dem gleichen Datenträger mit den Eigenschaften `SourceResourceId` und `SourceUniqueId` der Momentaufnahmen identifizieren. `SourceResourceId` ist die Azure Resource Manager-Ressourcen-ID des übergeordneten Datenträgers. `SourceUniqueId` ist der Wert, der von der `UniqueId`-Eigenschaft des Datenträgers geerbt wird. Wenn Sie einen Datenträger löschen und dann einen Datenträger mit demselben Namen erstellen, ändert sich der Wert der `UniqueId`-Eigenschaft.

Sie können `SourceResourceId` und `SourceUniqueId` verwenden, um eine Liste aller Momentaufnahmen zu erstellen, die einem bestimmten Datenträger zugeordnet sind. Ersetzen Sie `yourResourceGroupNameHere` durch Ihren Wert. Anschließend können Sie das folgende Beispiel verwenden, um Ihre vorhandenen inkrementellen Momentaufnahmen aufzulisten:

```PowerShell
$resourceGroupName = "yourResourceGroupNameHere"
$snapshots = Get-AzSnapshot -ResourceGroupName $resourceGroupName

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
```

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [virtual-machines-disks-incremental-snapshots-portal](../../includes/virtual-machines-disks-incremental-snapshots-portal.md)]

# <a name="resource-manager-template"></a>[Resource Manager-Vorlage](#tab/azure-resource-manager)

Sie können auch Azure Resource Manager-Vorlagen verwenden, um eine inkrementelle Momentaufnahme zu erstellen. Sie müssen sicherstellen, dass die apiVersion auf **2019-03-01** festgelegt ist. Die inkrementelle Eigenschaft muss ebenfalls auf TRUE festgelegt sein. Der folgende Codeausschnitt ist ein Beispiel dafür, wie Sie eine inkrementelle Momentaufnahme mit Resource Manager-Vorlagen erstellen:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "contosodisk1"
    },
  "diskResourceId": {
    "defaultValue": "<your_managed_disk_resource_ID>",
    "type": "String"
  }
  }, 
  "resources": [
  {
    "type": "Microsoft.Compute/snapshots",
    "name": "[concat( parameters('diskName'),'_snapshot1')]",
    "location": "[resourceGroup().location]",
    "apiVersion": "2019-03-01",
    "properties": {
      "creationData": {
        "createOption": "Copy",
        "sourceResourceId": "[parameters('diskResourceId')]"
      },
      "incremental": true
    }
  }
  ]
}
```
---

## <a name="cross-region-snapshot-copy-preview"></a>Regionsübergreifende Momentaufnahmekopie (Vorschau)

Sie können die Option CopyStart (Vorschau) verwenden, um eine Kopie inkrementeller Momentaufnahmen aus einer Region in eine beliebige Region Ihrer Wahl zu initiieren. Azure übernimmt den Kopiervorgang der inkrementellen Momentaufnahmen und stellt sicher, dass nur Deltaänderungen seit der letzten Momentaufnahme in die Zielregion kopiert werden, wodurch der Datenbedarf reduziert wird. Kunden können den Fortschritt der Kopie überprüfen, damit sie wissen können, wann eine Zielmomentaufnahme bereit ist, Datenträger in der Zielregion wiederherzustellen. Sie können diesen Prozess verwenden, um Momentaufnahmen zur langfristigen Aufbewahrung in ein anderes Abonnement zu kopieren. Sie können dies auch verwenden, um Momentaufnahmen in derselben Region zu kopieren, um sicherzustellen, dass Momentaufnahmen im [zonenredundanten Speicher](disks-redundancy.md#zone-redundant-storage-for-managed-disks) vollständig gehärtet werden und dass Momentaufnahmen im Falle eines Zonenfehlers verfügbar sind.

:::image type="content" source="media/disks-incremental-snapshots/cross-region-snapshot.png" alt-text="Diagramm der regionsübergreifenden Kopie inkrementeller Momentaufnahmen in Azure über die Option „Klonen“." lightbox="media/disks-incremental-snapshots/cross-region-snapshot.png":::

### <a name="pre-requisites"></a>Voraussetzungen

Sie müssen die Funktion in Ihrem Abonnement aktivieren, um die Vorschaufunktion zu verwenden. Um sich für die Funktion zu registrieren, verwenden Sie den folgenden Befehl:

```azurecli
az feature register --namespace Microsoft.Compute --name CreateOptionClone
```

Es kann einige Minuten dauern, bis die Registrierung abgeschlossen ist. Sie können den folgenden Befehl verwenden, um den Status zu überprüfen:

```azurecli
az feature show --namespace Microsoft.Compute --name CreateOptionClone
```

### <a name="restrictions"></a>Beschränkungen

- Das regionsübergreifende Kopieren von Momentaufnahmen ist derzeit nur in „USA, Ost 2“ und „USA, mittlerer Westen“ verfügbar.
- Sie müssen Version 2020-12-01 oder neuer der Azure Compute REST-API verwenden.

### <a name="get-started"></a>Erste Schritte

```azurecli
subscriptionId=<yourSubscriptionID>
resourceGroupName=<yourResourceGroupName>
name=<targetSnapshotName>
sourceSnapshotResourceId=<sourceSnapshotResourceId>
targetRegion=<validRegion>

az login
az account set --subscription $subscriptionId
az group deployment create -g $resourceGroupName \
--template-uri https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/CrossRegionCopyOfSnapshots/CopyStartIncrementalSnapshots.json \
--parameters "name=$name" "sourceSnapshotResourceId=$sourceSnapshotResourceId" "targetRegion=$targetRegion"
az resource show -n $name -g $resourceGroupName --namespace Microsoft.Compute --resource-type snapshots --api-version 2020-12-01 --query [properties.completionPercent] -o tsv
```

## <a name="next-steps"></a>Nächste Schritte

Beispielcode mit .NET, der die differenzielle Funktionalität von inkrementellen Momentaufnahmen veranschaulicht, finden Sie unter [Kopieren von Azure Managed Disks-Sicherungen in eine andere Region mit der differenziellen Funktionalität von inkrementellen Momentaufnahmen](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).
