---
title: Azure-Hybridvorteil für Windows Server
description: Erfahren Sie, wie Sie die Vorteile von Windows Software Assurance optimal nutzen, um lokale Lizenzen in Azure zu verwenden.
author: xujing-ms
ms.service: virtual-machines
ms.subservice: billing
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 4/22/2018
ms.author: xujing
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d634d1affd74d8a6c8be229d689bbcb6c63bd0db
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129216021"
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Azure-Hybridvorteil für Windows Server

**Gilt für:** :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Für Kunden mit Software Assurance ermöglicht der Azure-Hybridvorteil für Windows Server die Verwendung der lokalen Windows Server-Lizenzen und die Ausführung von virtuellen Windows-Computern in Azure zu geringeren Kosten. Sie können den Azure-Hybridvorteil für Windows Server dazu nutzen, neue virtuelle Computer mit dem Windows-Betriebssystem bereitzustellen. In diesem Artikel werden die Schritte zum Bereitstellen der neuen VMs mit dem Azure-Hybridvorteil für Windows Server und das Aktualisieren von vorhandenen, ausgeführten VMs beschrieben. Weitere Informationen zum Azure-Hybridvorteil für die Windows Server-Lizenzierung und den Kosteneinsparungen finden Sie auf der Seite zum [Azure-Hybridvorteil für die Windows Server-Lizenzierung](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

Jede Lizenz für zwei Prozessoren oder jeder Satz an Lizenzen für je 16 Kerne kann für zwei Instanzen mit bis zu acht Kernen oder für eine Instanz mit bis zu 16 Kernen eingesetzt werden. Der Azure-Hybridvorteil für Standard Edition-Lizenzen kann nur einmalig entweder lokal oder in Azure verwendet werden. Mit der Datacenter Edition ist die gleichzeitige Nutzung sowohl lokal als auch in Azure möglich.

Die Nutzung des Azure-Hybridvorteils für Windows Server mit virtuellen Computern mit dem Windows Server-Betriebssystem wird jetzt in allen Regionen unterstützt. Dies gilt auch für virtuelle Computer mit zusätzlicher Software, z.B. SQL Server oder Marketplace-Software von Drittanbietern. 


## <a name="classic-vms"></a>Klassische virtuelle Computer

Bei klassischen VMs wird die Bereitstellung von neuen VMs nur über lokale benutzerdefinierte Images unterstützt. Um die in diesem Artikel genannten unterstützten Funktionen nutzen zu können, müssen Sie klassische VMs zunächst zum Resource Manager-Modell migrieren.

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]
 

## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Möglichkeiten zur Nutzung des Azure-Hybridvorteils für Windows Server
Es gibt mehrere Möglichkeiten, um virtuelle Windows-Computer mit dem Azure-Hybridvorteil zu nutzen:

1. Sie können VMs über eines der bereitgestellten Windows Server-Images im Azure Marketplace bereitstellen.
2. Sie können einen benutzerdefinierten virtuellen Computer hochladen und die Bereitstellung per Resource Manager-Vorlage durchführen oder dazu Azure PowerShell nutzen.
3. Sie können einen vorhandenen virtuellen Computer zwischen der Ausführung mit dem Azure-Hybridvorteil umschalten und konvertieren oder bei Bedarf für Windows Server bezahlen.
4. Sie können den Azure-Hybridvorteil für Windows Server zudem für eine VM-Skalierungsgruppe anwenden.


## <a name="create-a-vm-with-azure-hybrid-benefit-for-windows-server"></a>Erstellen eines virtuellen Computers mit dem Azure-Hybridvorteil für Windows Server
Für den Azure-Hybridvorteil für Windows Server werden alle auf dem Windows Server-Betriebssystem basierenden Images unterstützt. Sie können Azure-Plattformimages verwenden oder eigene benutzerdefinierte Windows Server-Images hochladen. 

### <a name="portal"></a>Portal
Zum Erstellen einer VM mit Azure-Hybridvorteil für Windows Server scrollen Sie während der Erstellung auf der Registerkarte **Grundlagen** nach unten und aktivieren das Kontrollkästchen **Lizenzierung**, um eine vorhandene Windows Server-Lizenz zu verwenden. 

### <a name="powershell"></a>PowerShell

```powershell
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -ImageName "Win2016Datacenter" `
    -LicenseType "Windows_Server"
```

### <a name="cli"></a>Befehlszeilenschnittstelle (CLI)
```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --license-type Windows_Server
```

### <a name="template"></a>Vorlage
In den Resource Manager-Vorlagen muss ein zusätzlicher Parameter für `licenseType` angegeben werden. Informieren Sie sich weiter über das [Erstellen von Azure Resource Manager-Vorlagen](../../azure-resource-manager/templates/syntax.md).

```json
"properties": {
    "licenseType": "Windows_Server",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
}    
```

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Konvertieren eines vorhandenen virtuellen Computers mit Azure-Hybridvorteil für Windows Server
Wenn Sie einen vorhandenen virtuellen Computer konvertieren möchten, um den Azure-Hybridvorteil für Windows Server zu nutzen, können Sie den Lizenztyp des virtuellen Computers anhand der folgenden Anleitungen aktualisieren.

> [!NOTE]
> Das Ändern des Lizenztyps auf dem virtuellen Computer führt weder zu einem Neustart des Systems noch zu einer Dienstunterbrechung.  Es ist einfach eine Aktualisierung eines Metadatenflags.
> 

### <a name="portal"></a>Portal
Über das VM-Blatt im Portal können Sie den virtuellen Computer zur Verwendung des Azure-Hybridvorteils konvertieren, indem Sie die Option „Konfiguration“ auswählen und die Option „Azure-Hybridvorteil“ aktivieren.

### <a name="powershell"></a>PowerShell
- Konvertieren vorhandener virtueller Windows Server-Computer für den Azure-Hybridvorteil für Windows Server

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "Windows_Server"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
- Konvertieren virtueller Windows Server-Computer mit Hybridvorteil zurück zur nutzungsbasierten Bezahlung

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "None"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
### <a name="cli"></a>Befehlszeilenschnittstelle (CLI)
- Konvertieren vorhandener virtueller Windows Server-Computer für den Azure-Hybridvorteil für Windows Server

    ```azurecli
    az vm update --resource-group myResourceGroup --name myVM --set licenseType=Windows_Server
    ```

### <a name="how-to-verify-your-vm-is-utilizing-the-licensing-benefit"></a>Überprüfen, ob für den virtuellen Computer der Lizenzierungsvorteil genutzt wird
Nachdem Sie den virtuellen Computer über PowerShell, die Resource Manager-Vorlage oder das Portal bereitgestellt haben, können Sie die Einstellung wie folgt überprüfen.

### <a name="portal"></a>Portal
Auf dem Blatt des virtuellen Computers im Portal können Sie die Umschaltfläche für den Azure-Hybridvorteil für Windows Server anzeigen, indem Sie die Registerkarte „Konfiguration“ auswählen.

### <a name="powershell"></a>PowerShell
Im folgenden Beispiel wird der Lizenztyp für einen einzelnen virtuellen Computer angezeigt.
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Ausgabe:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Diese Ausgabe steht im Gegensatz zu der folgenden VM, die ohne Azure-Hybridvorteil für die Windows Server-Lizenzierung bereitgestellt wird:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

### <a name="cli"></a>Befehlszeilenschnittstelle (CLI)
```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVM --query "[?licenseType=='Windows_Server']" -o table
```

> [!NOTE]
> Das Ändern des Lizenztyps auf dem virtuellen Computer führt weder zu einem Neustart des Systems noch zu einer Dienstunterbrechung. Es ist nur ein Metadatelizenzierungsflag.
>

## <a name="list-all-vms-and-vmss-with-azure-hybrid-benefit-for-windows-server-in-a-subscription"></a>Auflisten aller VMs and VMSS mit dem Azure-Hybridvorteil für Windows Server in einem Abonnement
Um alle mit dem Azure-Hybridvorteil für Windows Server bereitgestellten virtuellen Computer und VM-Skalierungsgruppen anzuzeigen und aufzulisten, können Sie den folgenden Befehl aus Ihrem Abonnement ausführen:

### <a name="portal"></a>Portal
Auf dem Ressourcenblatt des virtuellen Computers oder der VM-Skalierungsgruppe können Sie eine Liste aller virtuellen Computer und des entsprechenden Lizenztyps anzeigen, indem Sie die Tabellenspalte so konfigurieren, dass sie „Azure-Hybridvorteil“ enthält. Die VM-Einstellung kann einen der Status „Aktiviert“, „Nicht aktiviert“ oder „Nicht unterstützt“ aufweisen.

### <a name="powershell"></a>PowerShell
Für virtuelle Computer:
```powershell
Get-AzVM | ?{$_.LicenseType -like "Windows_Server"} | select ResourceGroupName, Name, LicenseType
```

Für VM-Skalierungsgruppen:
```powershell
Get-AzVmss | Select * -ExpandProperty VirtualMachineProfile | ? LicenseType -eq 'Windows_Server' | select ResourceGroupName, Name, LicenseType
```

### <a name="cli"></a>Befehlszeilenschnittstelle (CLI)
Für virtuelle Computer:
```azurecli
az vm list --query "[?licenseType=='Windows_Server']" -o table
```

Für VM-Skalierungsgruppen:
```azurecli
az vmss list --query "[?virtualMachineProfile.licenseType=='Windows_Server']" -o table
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Bereitstellen einer VM-Skalierungsgruppe mit dem Azure-Hybridvorteil für Windows Server
In den Resource Manager-Vorlagen der VM-Skalierungsgruppe muss ein zusätzlicher Parameter für `licenseType` in der Eigenschaft „VirtualMachineProfile“ angegeben werden. Dies kann während der Erstellung oder Aktualisierung der Skalierungsgruppe über die ARM-Vorlage, PowerShell, die Azure-Befehlszeilenschnittstelle oder REST erfolgen.

Im folgenden Beispiel wird die ARM-Vorlage mit einem Windows Server 2016 Datacenter-Image verwendet:

```json
"virtualMachineProfile": {
    "storageProfile": {
        "osDisk": {
            "createOption": "FromImage"
        },
        "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2016-Datacenter",
            "version": "latest"
        }
    },
    "licenseType": "Windows_Server",
    "osProfile": {
            "computerNamePrefix": "[parameters('vmssName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
    }
}    
```
Weitere Möglichkeiten zum Aktualisieren der Skalierungsgruppe finden Sie unter [Ändern einer VM-Skalierungsgruppe](../../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md).

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zu [Einsparungen durch den Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-use-benefit/)
- Weitere Informationen zu [häufig gestellten Fragen zum Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-use-benefit/faq/)
- Weitere ausführliche Informationen finden Sie unter [Azure-Hybridvorteil für die Windows Server-Lizenzierung](/windows-server/get-started/azure-hybrid-benefit).
- Weitere Informationen zur [Nutzung des Azure-Hybridvorteils für Windows Server und Azure Site Recovery für eine noch kosteneffizientere Anwendungsmigration zu Azure](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/)
- Weitere Informationen zum [Bereitstellen von Windows 10 unter Azure mit mehrinstanzenfähigen Hostingrechten](./windows-desktop-multitenant-hosting-deployment.md)
- Weitere Informationen zum [Verwenden von Resource Manager-Vorlagen](../../azure-resource-manager/management/overview.md)
