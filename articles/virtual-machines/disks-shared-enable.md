---
title: Aktivieren von freigegebenen Datenträgern für verwaltete Azure-Datenträger
description: Konfigurieren eines verwalteten Azure-Datenträgers mit freigegebenen Datenträgern, um ihn für mehrere virtuelle Computer freigeben zu können
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/16/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: d2a770dd007c801d2192ff08349966ff915bdd0a
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122351043"
---
# <a name="enable-shared-disk"></a>Aktivieren freigegebener Datenträger

In diesem Artikel erfahren Sie, wie Sie das Feature für freigegebene Datenträger für verwaltete Azure-Datenträger aktivieren. Freigegebene Azure-Datenträger sind ein neues Feature für verwaltete Azure-Datenträger, mit dem Sie einen verwalteten Datenträger gleichzeitig an mehrere virtuelle Computer (Virtual Machines, VMs) anfügen können. Durch das Anfügen eines verwalteten Datenträgers an mehrere virtuelle Computer können Sie entweder neue gruppierte Anwendungen in Azure bereitstellen oder bereits vorhandene gruppierte Anwendungen zu Azure migrieren. 

Informationen zum Konzept verwalteter Datenträger mit aktivierten freigegebenen Datenträgern finden Sie unter [Freigegebene Azure-Datenträger](disks-shared.md).

## <a name="prerequisites"></a>Voraussetzungen

Für die Skripte und Befehle in diesem Artikel ist Folgendes erforderlich:

- Version 6.0.0 oder höher des Azure PowerShell-Moduls

oder
- Die aktuelle Version der Azure-Befehlszeilenschnittstelle

## <a name="limitations"></a>Einschränkungen

[!INCLUDE [virtual-machines-disks-shared-limitations](../../includes/virtual-machines-disks-shared-limitations.md)]

## <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme

Freigegebene Datenträger unterstützen verschiedene Betriebssysteme. Informationen zu den unterstützten Betriebssystemen finden Sie im Konzeptartikel in den Abschnitten [Windows](./disks-shared.md#windows) und [Linux](./disks-shared.md#linux).

## <a name="disk-sizes"></a>Datenträgergrößen

[!INCLUDE [virtual-machines-disks-shared-sizes](../../includes/virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-shared-disks"></a>Bereitstellen von freigegebenen Datenträgern

### <a name="deploy-a-premium-ssd-as-a-shared-disk"></a>Bereitstellen einer SSD Premium als freigegebenen Datenträger

Um einen verwalteten Datenträger mit aktivierter Funktion freigegebener Datenträger bereitzustellen, verwenden Sie die neue Eigenschaft `maxShares`, und definieren Sie einen Wert, der größer als 1 ist. Dadurch kann der Datenträger für mehrere VMs freigegeben werden.

> [!IMPORTANT]
> Der Wert von `maxShares` kann nur festgelegt oder geändert werden, wenn die Einbindung eines Datenträgers auf allen VMs aufgehoben wird. Informationen zu den zulässigen Werten für `maxShares` finden Sie unter [Datenträgergrößen](#disk-sizes).

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Melden Sie sich beim Azure-Portal an. 
1. Suchen Sie nach **Datenträger**, und wählen Sie diese Option aus.
1. Wählen Sie **+ Erstellen** aus, um einen neuen Datenträger zu erstellen.
1. Geben Sie die Details ein, wählen Sie eine geeignete Region aus, und wählen Sie dann **Größe ändern** aus.

    :::image type="content" source="media/disks-shared-enable/create-shared-disk-basics-pane.png" alt-text="Screenshot: Bereich „Verwalteten Datenträger erstellen“ mit hervorgehobener Option „Größe ändern“" lightbox="media/disks-shared-enable/create-shared-disk-basics-pane.png":::

1. Wählen Sie die gewünschte SSD Premium-Größe und dann **OK** aus.

    :::image type="content" source="media/disks-shared-enable/select-premium-shared-disk.png" alt-text="Screenshot: Datenträger-SKU, SSD Premium hervorgehoben" lightbox="media/disks-shared-enable/select-premium-shared-disk.png":::

1. Fahren Sie mit der Bereitstellung fort, bis Sie zum Bereich **Erweitert** gelangen.
1. Wählen Sie **Ja** für **Freigegebenen Datenträger aktivieren** aus, und wählen Sie die Anzahl **Maximaler Freigaben** aus, die Sie zulassen möchten.

    :::image type="content" source="media/disks-shared-enable/enable-premium-shared-disk.png" alt-text="Screenshot: Bereich „Erweitert“ mit hervorgehobener Option „Freigegebenen Datenträger aktivieren“ auf „Ja“" lightbox="media/disks-shared-enable/enable-premium-shared-disk.png":::

1. Klicken Sie auf **Überprüfen + erstellen**.


# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az disk create -g myResourceGroup -n mySharedDisk --size-gb 1024 -l westcentralus --sku Premium_LRS --max-shares 2
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$dataDiskConfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType Premium_LRS -CreateOption Empty -MaxSharesCount 2

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $dataDiskConfig
```

# <a name="resource-manager-template"></a>[Resource Manager-Vorlage](#tab/azure-resource-manager)

Bevor Sie die folgende Vorlage verwenden, ersetzen Sie `[parameters('dataDiskName')]`, `[resourceGroup().location]`, `[parameters('dataDiskSizeGB')]` und `[parameters('maxShares')]` durch Ihre eigenen Werte.

[Vorlage für freigegebene SSD Premium-Datenträger](https://aka.ms/SharedPremiumDiskARMtemplate)

---

### <a name="deploy-a-standard-ssd-as-a-shared-disk"></a>Bereitstellen einer SSD Standard als freigegebenen Datenträger

Um einen verwalteten Datenträger mit aktivierter Funktion freigegebener Datenträger bereitzustellen, verwenden Sie die neue Eigenschaft `maxShares`, und definieren Sie einen Wert, der größer als 1 ist. Dadurch kann der Datenträger für mehrere VMs freigegeben werden.

> [!IMPORTANT]
> Der Wert von `maxShares` kann nur festgelegt oder geändert werden, wenn die Einbindung eines Datenträgers auf allen VMs aufgehoben wird. Informationen zu den zulässigen Werten für `maxShares` finden Sie unter [Datenträgergrößen](#disk-sizes).

# <a name="portal"></a>[Portal](#tab/azure-portal)

Sie können derzeit keine freigegebene SSD Standard über das Azure-Portal bereitstellen. Verwenden Sie entweder die Azure CLI, das Azure PowerShell-Modul oder eine Azure Resource Manager-Vorlage.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az disk create -g myResourceGroup -n mySharedDisk --size-gb 1024 -l westcentralus --sku StandardSSD_LRS --max-shares 2
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$dataDiskConfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType StandardSSD_LRS -CreateOption Empty -MaxSharesCount 2

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $dataDiskConfig
```

# <a name="resource-manager-template"></a>[Resource Manager-Vorlage](#tab/azure-resource-manager)

Ersetzen Sie die Werte in dieser Azure Resource Manager-Vorlage durch Ihre eigenen, bevor Sie sie verwenden:

```rest
{ 
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "dataDiskName": {
      "type": "string",
      "defaultValue": "mySharedDisk"
    },
    "dataDiskSizeGB": {
      "type": "int",
      "defaultValue": 1024
    },
    "maxShares": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('dataDiskName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-07-01",
      "sku": {
        "name": "StandardSSD_LRS"
      },
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeGB')]",
        "maxShares": "[parameters('maxShares')]"
      }
    }
  ] 
}
```

---

### <a name="deploy-an-ultra-disk-as-a-shared-disk"></a>Bereitstellen einer Disk Ultra als freigegebenen Datenträger

Um einen verwalteten Datenträger mit aktiviertem Feature für freigegebene Datenträger bereitzustellen, ändern Sie den Parameter `maxShares` in einen Wert, der größer als 1 ist. Dadurch kann der Datenträger für mehrere VMs freigegeben werden.

> [!IMPORTANT]
> Der Wert von `maxShares` kann nur festgelegt oder geändert werden, wenn die Einbindung eines Datenträgers auf allen VMs aufgehoben wird. Informationen zu den zulässigen Werten für `maxShares` finden Sie unter [Datenträgergrößen](#disk-sizes).

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Melden Sie sich beim Azure-Portal an. 
1. Suchen Sie nach **Datenträger**, und wählen Sie diese Option aus.
1. Wählen Sie **+ Erstellen** aus, um einen neuen Datenträger zu erstellen.
1. Geben Sie die Details ein, und wählen Sie dann **Größe ändern**.
1. Wählen Sie für die **Datenträger-SKU** Disk Ultra aus.

    :::image type="content" source="media/disks-shared-enable/select-ultra-shared-disk.png" alt-text="Screenshot der Datenträger-SKU, Disk Ultra hervorgehoben" lightbox="media/disks-shared-enable/select-ultra-shared-disk.png":::

1. Wählen Sie die gewünschte Datenträgergröße und dann **OK** aus.
1. Fahren Sie mit der Bereitstellung fort, bis Sie zum Bereich **Erweitert** gelangen.
1. Wählen Sie **Ja** für **Freigegebenen Datenträger aktivieren** aus, und wählen Sie die Anzahl **Maximaler Freigaben** aus, die Sie zulassen möchten.
1. Klicken Sie auf **Überprüfen + erstellen**.

    :::image type="content" source="media/disks-shared-enable/enable-ultra-shared-disk.png" alt-text="Screenshot: Bereich „Erweitert“ mit hervorgehobener Option „Freigegebenen Datenträger aktivieren“" lightbox="media/disks-shared-enable/enable-ultra-shared-disk.png":::

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

##### <a name="regional-disk-example"></a>Beispiel für einen regionalen Datenträger

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

##### <a name="zonal-disk-example"></a>Beispiel für einen zonalen Datenträger

Dieses Beispiel ist fast identisch mit dem vorherigen, es wird jedoch ein Datenträger in der Verfügbarkeitszone 1 erstellt.

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1 --zone 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

##### <a name="regional-disk-example"></a>Beispiel für einen regionalen Datenträger

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

##### <a name="zonal-disk-example"></a>Beispiel für einen zonalen Datenträger

Dieses Beispiel ist fast identisch mit dem vorherigen, es wird jedoch ein Datenträger in der Verfügbarkeitszone 1 erstellt.

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5 -Zone 1

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

# <a name="resource-manager-template"></a>[Resource Manager-Vorlage](#tab/azure-resource-manager)

##### <a name="regional-disk-example"></a>Beispiel für einen regionalen Datenträger

Bevor Sie die folgende Vorlage verwenden, ersetzen Sie `[parameters('dataDiskName')]`, `[resourceGroup().location]`, `[parameters('dataDiskSizeGB')]`, `[parameters('maxShares')]`, `[parameters('diskIOPSReadWrite')]`, `[parameters('diskMBpsReadWrite')]`, `[parameters('diskIOPSReadOnly')]` und `[parameters('diskMBpsReadOnly')]` durch Ihre eigenen Werte.

[Vorlage für regionale freigegebene Disk Ultra-Datenträger](https://aka.ms/SharedUltraDiskARMtemplateRegional)

##### <a name="zonal-disk-example"></a>Beispiel für einen zonalen Datenträger

Bevor Sie die folgende Vorlage verwenden, ersetzen Sie `[parameters('dataDiskName')]`, `[resourceGroup().location]`, `[parameters('dataDiskSizeGB')]`, `[parameters('maxShares')]`, `[parameters('diskIOPSReadWrite')]`, `[parameters('diskMBpsReadWrite')]`, `[parameters('diskIOPSReadOnly')]` und `[parameters('diskMBpsReadOnly')]` durch Ihre eigenen Werte.

[Vorlage für zonale freigegebene Disk Ultra-Datenträger](https://aka.ms/SharedUltraDiskARMtemplateZonal)

---

## <a name="using-azure-shared-disks-with-your-vms"></a>Verwenden von freigegebenen Azure-Datenträgern mit ihren VMs

Nachdem Sie mit `maxShares>1` einen freigegebenen Datenträger bereitgestellt haben, können Sie den Datenträger auf einem oder mehreren virtuellen Computern einbinden.

> [!NOTE]
> Wenn Sie einen Ultra-Datenträger bereitstellen, stellen Sie sicher, dass er die erforderlichen Anforderungen erfüllt. Weitere Informationen finden Sie unter [Verwenden von Azure Ultra Disks](disks-enable-ultra-ssd.md).

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress"

$dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup -DiskName "mySharedDisk"

$vm = Add-AzVMDataDisk -VM $vm -Name "mySharedDisk" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0

update-AzVm -VM $vm -ResourceGroupName $resourceGroup
```

## <a name="supported-scsi-pr-commands"></a>Unterstützte SCSI-PR-Befehle

Nachdem Sie den freigegebenen Datenträger auf Ihren VMs in Ihrem Cluster eingebunden haben, können Sie mithilfe von SCSI-PR ein Quorum und Lese-/Schreibzugriff auf den Datenträger einrichten. Die folgenden PR-Befehle sind für freigegebene Azure-Datenträger verfügbar:

Wenn Sie mit dem Datenträger interagieren möchten, beginnen Sie mit der Liste „persistent-reservation-action“:

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

Wenn Sie PR_RESERVE, PR_PREEMPT_RESERVATION oder PR_RELEASE_RESERVATION verwenden, geben Sie einen der folgenden permanenten Reservierungstypen (persistent-reservation-type) an:

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

Ferner müssen Sie beim Verwenden von PR_RESERVE, PR_REGISTER_AND_IGNORE, PR_REGISTER_KEY, PR_PREEMPT_RESERVATION, PR_CLEAR_RESERVATION, oder PR_RELEASE-RESERVATION einen persistent-reservation-key angeben.


## <a name="next-steps"></a>Nächste Schritte

Wenn Sie die Bereitstellung des Datenträgers mithilfe von Azure Resource Manager-Vorlagen bevorzugen, sehen Sie sich die folgenden Beispielvorlagen an:
- [SSD Premium](https://aka.ms/SharedPremiumDiskARMtemplate)
- [Regionale Ultra-Datenträger](https://aka.ms/SharedUltraDiskARMtemplateRegional)
- [Zonale Ultra-Datenträger](https://aka.ms/SharedUltraDiskARMtemplateZonal)
