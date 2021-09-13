---
title: Erstellen einer inkrementellen Momentaufnahme
description: Erfahren Sie mehr über inkrementelle Momentaufnahmen für verwaltete Datenträger und darüber, wie sie mit dem Azure-Portal, PowerShell und Azure Resource Manager erstellt werden.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/10/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: bbd87d8f8e4c140c1ced76a3c60e82d3066d2f5a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122347009"
---
# <a name="create-an-incremental-snapshot-for-managed-disks"></a>Erstellen einer inkrementellen Momentaufnahme für verwaltete Datenträger

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

## <a name="next-steps"></a>Nächste Schritte

Beispielcode mit .NET, der die differenzielle Funktionalität von inkrementellen Momentaufnahmen veranschaulicht, finden Sie unter [Kopieren von Azure Managed Disks-Sicherungen in eine andere Region mit der differenziellen Funktionalität von inkrementellen Momentaufnahmen](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).
