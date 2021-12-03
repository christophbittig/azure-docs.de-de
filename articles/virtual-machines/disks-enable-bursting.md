---
title: Aktivieren des bedarfsgesteuerten Datenträgerburstings
description: Aktivieren Sie das bedarfsgesteuerte Datenträgerbursting auf dem verwalteten Datenträger.
author: roygara
ms.author: rogarana
ms.date: 11/09/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 27a832f7f02fd10cde7f3c9f603f38d0d5e466b6
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132136191"
---
# <a name="enable-on-demand-bursting"></a>Aktivieren des bedarfsgesteuerten Burstings

SSD Premium-Datenträger verfügen über zwei verfügbare Modelle für das Bursting: guthabenbasiertes Bursting und bedarfsgesteuertes Bursting. In diesem Artikel wird beschrieben, wie Sie zum bedarfsgesteuerten Bursting wechseln. Datenträger, die das bedarfsgesteuerte Modell verwenden, können das Bursting über ihre ursprünglich bereitgestellten Ziele hinaus durchführen. Das bedarfsgesteuerte Bursting erfolgt so oft, wie es für den Workload erforderlich ist, bis zum maximalen Burstziel. Beim bedarfsgesteuerten Bursting fallen zusätzliche Gebühren an.

Weitere Informationen zum Datenträgerbursting finden Sie unter [Verwaltetes Datenträgerbursting](disk-bursting.md). 

Informationen zu den maximalen Burstzielen auf jedem unterstützten Datenträger finden Sie unter [Skalierbarkeits- und Leistungsziele für VM-Datenträger](disks-scalability-targets.md#premium-ssd-managed-disks-per-disk-limits).

> [!IMPORTANT]
> Sie müssen die Schritte in diesem Artikel nicht befolgen, um das guthabenbasierte Bursting zu verwenden. Standardmäßig ist das guthabenbasierte Bursting auf allen berechtigten Datenträgern aktiviert.

Bevor Sie das bedarfsgesteuerte Bursting aktivieren, sollten Sie Folgendes beachten:

[!INCLUDE [managed-disk-bursting-regions-limitations](../../includes/managed-disk-bursting-regions-limitations.md)]

### <a name="regional-availability"></a>Regionale Verfügbarkeit

[!INCLUDE [managed-disk-bursting-availability](../../includes/managed-disk-bursting-availability.md)]

## <a name="get-started"></a>Erste Schritte

Das bedarfsgesteuerte Bursting kann entweder mit dem Azure PowerShell-Modul, der Azure CLI oder den Azure Resource Manager-Vorlagen aktiviert werden. Die folgenden Beispiele behandeln das Erstellen eines neuen Datenträgers mit aktiviertem bedarfsgesteuertem Bursting und das Aktivieren des bedarfsgesteuerten Burstings auf vorhandenen Datenträgern.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Cmdlets für bedarfsgesteuertes Bursting sind in Version 5.5.0 und höher des Az-Moduls verfügbar. Alternativ können Sie auch die [Azure Cloud Shell](https://shell.azure.com/) verwenden.
### <a name="create-an-empty-data-disk-with-on-demand-bursting"></a>Erstellen eines leeren Datenträgers mit bedarfsgesteuertem Bursting

Ein verwalteter Datenträger muss größer als 512 GiB sein, um bedarfsgesteuertes Bursting zu ermöglichen. Ersetzen Sie die Parameter `<myResourceGroupDisk>` und `<myDataDisk>`, und führen Sie dann das folgende Skript aus, um einen SSD Premium-Datenträger mit bedarfsgesteuertem Bursting zu erstellen:

```azurepowershell
Set-AzContext -SubscriptionName <yourSubscriptionName>

$diskConfig = New-AzDiskConfig -Location 'WestCentralUS' -CreateOption Empty -DiskSizeGB 1024 -SkuName Premium_LRS -BurstingEnabled $true

$dataDisk = New-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> -Disk $diskConfig
```

### <a name="enable-on-demand-bursting-on-an-existing-disk"></a>Aktivieren des bedarfsgesteuerten Burstings auf einem vorhandenen Datenträger

Ein verwalteter Datenträger muss größer als 512 GiB sein, um bedarfsgesteuertes Bursting zu ermöglichen. Ersetzen Sie die Parameter `<myResourceGroupDisk>`, `<myDataDisk>`, und führen Sie diesen Befehl aus, um das bedarfsgesteuerte Bursting auf einem vorhandenen Datenträger zu aktivieren:

```azurepowershell
New-AzDiskUpdateConfig -BurstingEnabled $true | Update-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> //Set the flag to $false to disable on-demand bursting
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Cmdlets für bedarfsgesteuertes Bursting sind in Version 2.19.0 und höher des [Azure CLI-Moduls](/cli/azure/install-azure-cli) verfügbar. Alternativ können Sie auch die [Azure Cloud Shell](https://shell.azure.com/) verwenden.

### <a name="create-and-attach-a-on-demand-bursting-data-disk"></a>Erstellen und Anfügen eines Datenträgers mit bedarfsgesteuertem Bursting

Ein verwalteter Datenträger muss größer als 512 GiB sein, um bedarfsgesteuertes Bursting zu ermöglichen. Ersetzen Sie die Parameter `<yourDiskName>`, `<yourResourceGroup>` und `<yourVMName>`, und führen Sie dann die folgenden Befehle aus, um einen SSD Premium-Datenträger mit bedarfsgesteuertem Bursting zu erstellen:

```azurecli
az disk create -g <yourResourceGroup> -n <yourDiskName> --size-gb 1024 --sku Premium_LRS -l westcentralus --enable-bursting true

az vm disk attach --vm-name <yourVMName> --name <yourDiskName> --resource-group <yourResourceGroup>
```

### <a name="enable-on-demand-bursting-on-an-existing-disk---cli"></a>Aktivieren des bedarfsgesteuerten Burstings auf einem vorhandenen Datenträger: CLI

Ein verwalteter Datenträger muss größer als 512 GiB sein, um bedarfsgesteuertes Bursting zu ermöglichen. Ersetzen Sie die Parameter `<myResourceGroupDisk>` und `<yourDiskName>`, und führen Sie diesen Befehl aus, um das bedarfsgesteuerte Bursting auf einem vorhandenen Datenträger zu aktivieren:

```azurecli
az disk update --name <yourDiskName> --resource-group <yourResourceGroup> --enable-bursting true //Set the flag to false to disable on-demand bursting
```

# <a name="azure-resource-manager"></a>[Azure Resource Manager](#tab/azure-resource-manager)

Mit der Datenträger-API `2020-09-30` können Sie das bedarfsgesteuerte Bursting auf neu erstellten oder vorhandenen SSD Premium-Datenträgern aktivieren, die größer als 512 GiB sind. Die `2020-09-30`-API hat eine neue Eigenschaft eingeführt, `burstingEnabled`. Standardmäßig ist diese Eigenschaft auf „false“ festgelegt. Die folgende Beispielvorlage erstellt einen SSD Premium-Datenträger mit 1 TiB in der Region „USA, Westen-Mitte“, wobei das Datenträgerbursting aktiviert ist:

```
{
  "$schema": "http://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskSkuName": {
        "type": "string",
        "defaultValue": "Premium_LRS" //Supported on premium SSDs only
},
    "dataDiskSizeInGb": {
      "type": "string",
      "defaultValue": "1024" //Supported on disk size > 512 GiB
},
    "location": {
      "type": "string",
      "defaultValue": "westcentralus" //Preview regions: West Central US
},
"diskApiVersion": {
      "type": "string",
      "defaultValue": "2020-09-30" //Preview supported version: 2020-09-30 or above
    }
  },
  "resources": [
    {
      "apiVersion": "[parameters('diskApiVersion')]",
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('diskName')]",
      "location": "[parameters(location)]",
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeInGb')]",
        "burstingEnabled": "true" //Feature flag to enable disk bursting on disks > 512 GiB
      },
      "sku": {
        "name": "[parameters('diskSkuName')]"
      }
    ]
}
```
---
 
## <a name="next-steps"></a>Nächste Schritte

Wie Sie einen Einblick in Ihre Burstingressourcen erhalten, erfahren Sie unter [Metriken zum Datenträgerbursting](disks-metrics.md).
