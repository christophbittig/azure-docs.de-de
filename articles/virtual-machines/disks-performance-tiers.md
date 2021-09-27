---
title: Ändern der Leistung verwalteter Azure-Datenträger – CLI/PowerShell
description: Erfahren Sie, wie Sie Leistungsstufen für vorhandene verwaltete Datenträger mithilfe des Azure PowerShell-Moduls oder der Azure CLI ändern können.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 06/29/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: b72066dbeda75ae651b26c76b99697d978986a50
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123435292"
---
# <a name="change-your-performance-tier-without-downtime-using-the-azure-powershell-module-or-the-azure-cli"></a>Ändern der Leistungsstufe ohne Ausfallzeiten mithilfe des Azure PowerShell-Moduls oder der Azure CLI

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

[!INCLUDE [virtual-machines-disks-performance-tiers-intro](../../includes/virtual-machines-disks-performance-tiers-intro.md)]

## <a name="restrictions"></a>Beschränkungen

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="prerequisites"></a>Voraussetzungen
# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
Installieren Sie die neueste Version der [Azure CLI](/cli/azure/install-az-cli2), und melden Sie sich über [az login](/cli/azure/reference-index) bei einem Azure-Konto an.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Installieren Sie die neueste [Azure PowerShell-Version](/powershell/azure/install-az-ps), und melden Sie sich über `Connect-AzAccount` bei einem Azure-Konto an.

---

## <a name="create-an-empty-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>Erstellen/Aktualisieren eines Datenträgers mit einer höheren Stufe als der Baselinestufe
# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
subscriptionId=<yourSubscriptionIDHere>
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
diskSize=<yourDiskSizeHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus

az account set --subscription $subscriptionId

az disk create -n $diskName -g $resourceGroupName -l $region --sku Premium_LRS --size-gb $diskSize --tier $performanceTier
```
## <a name="create-an-os-disk-with-a-tier-higher-than-the-baseline-tier-from-an-azure-marketplace-image"></a>Erstellen eines Betriebssystemdatenträgers mit einer höheren Stufe als der Baselinestufe aus einem Azure Marketplace-Image

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus
image=Canonical:UbuntuServer:18.04-LTS:18.04.202002180

az disk create -n $diskName -g $resourceGroupName -l $region --image-reference $image --sku Premium_LRS --tier $performanceTier
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$subscriptionId='yourSubscriptionID'
$resourceGroupName='yourResourceGroupName'
$diskName='yourDiskName'
$diskSizeInGiB=4
$performanceTier='P50'
$sku='Premium_LRS'
$region='westcentralus'

Connect-AzAccount

Set-AzContext -Subscription $subscriptionId

$diskConfig = New-AzDiskConfig -SkuName $sku -Location $region -CreateOption Empty -DiskSizeGB $diskSizeInGiB -Tier $performanceTier
New-AzDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
```
---

## <a name="update-the-tier-of-a-disk-without-downtime"></a>Aktualisieren des Tarifs eines Datenträgers ohne Downtime

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

1. Sie müssen das Feature für Ihr Abonnement aktivieren, bevor Sie die Leistungsstufe eines Datenträgers ohne Downtime ändern können. Durch die folgenden Schritte wird das Feature für Ihr Abonnement aktiviert:

    1.  Führen Sie den folgenden Befehl aus, um das Feature für Ihr Abonnement zu registrieren.

        ```azurecli
        az feature register --namespace Microsoft.Compute --name LiveTierChange
        ```

    1.  Überprüfen Sie mithilfe des folgenden Befehls, ob der Registrierungsstatus **Registriert** lautet (dies kann einige Minuten dauern), bevor Sie das Feature ausprobieren.

        ```azurecli
        az feature show --namespace Microsoft.Compute --name LiveTierChange
        ```
2. Aktualisieren der Ebene eines Datenträgers, selbst wenn er an eine aktuell ausgeführte VM angefügt ist

    ```azurecli
    resourceGroupName=<yourResourceGroupNameHere>
    diskName=<yourDiskNameHere>
    performanceTier=<yourDesiredPerformanceTier>

    az disk update -n $diskName -g $resourceGroupName --set tier=$performanceTier
    ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Sie müssen das Feature für Ihr Abonnement aktivieren, bevor Sie die Leistungsstufe eines Datenträgers ohne Downtime ändern können. Durch die folgenden Schritte wird das Feature für Ihr Abonnement aktiviert:

    1.  Führen Sie den folgenden Befehl aus, um das Feature für Ihr Abonnement zu registrieren.

        ```azurepowershell
         Register-AzProviderFeature -FeatureName "LiveTierChange" -ProviderNamespace "Microsoft.Compute" 
        ```

    1.  Überprüfen Sie mithilfe des folgenden Befehls, ob der Registrierungsstatus **Registriert** lautet (dies kann einige Minuten dauern), bevor Sie das Feature ausprobieren.

        ```azurepowershell
        Register-AzProviderFeature -FeatureName "LiveTierChange" -ProviderNamespace "Microsoft.Compute" 
        ```
2. Aktualisieren der Ebene eines Datenträgers, selbst wenn er an eine aktuell ausgeführte VM angefügt ist

    ```azurepowershell
    $resourceGroupName='yourResourceGroupName'
    $diskName='yourDiskName'
    $performanceTier='P1'

    $diskUpdateConfig = New-AzDiskUpdateConfig -Tier $performanceTier

    Update-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName -DiskUpdate $diskUpdateConfig
    ```
---

## <a name="show-the-tier-of-a-disk"></a>Anzeigen der Stufe eines Datenträgers

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az disk show -n $diskName -g $resourceGroupName --query [tier] -o tsv
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName

$disk.Tier
```
---

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie die Größe eines Datenträgers ändern müssen, um die höheren Leistungsstufen zu nutzen, lesen Sie die folgenden Artikel:

- [Erweitern von virtuellen Festplatten auf virtuellen Linux-Computern mit der Azure-CLI](linux/expand-disks.md)
- [Erweitern eines verwalteten Datenträgers, der an einen virtuellen Windows-Computer angefügt ist](windows/expand-os-disk.md)
