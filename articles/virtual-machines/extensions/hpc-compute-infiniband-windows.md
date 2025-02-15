---
title: InfiniBand-Treibererweiterung – Azure-Windows-VMs
description: Microsoft Azure-Erweiterung für die Installation von InfiniBand-Treibern auf Compute-VMs der H- und N-Serie unter Windows.
services: virtual-machines
documentationcenter: ''
author: vermagit
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: hpc
ms.collection: windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/14/2021
ms.author: amverma
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0f2e6d04542df5795bd3c30763f0197295a21f78
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130038344"
---
# <a name="infiniband-driver-extension-for-windows"></a>InfiniBand-Treibererweiterung für Windows

Diese Erweiterung installiert InfiniBand ND-Treiber (für nicht SR-IOV-fähige) und OFED-Treiber (für SR-IOV-fähige) („r“-Größen) für virtuelle Computer der [H-Serie](../sizes-hpc.md) und [N-Serie](../sizes-gpu.md) unter Windows. Je nach VM-Familie installiert die Erweiterung die entsprechenden Treiber für die Connect-X-NIC.

Außerdem ist eine Erweiterung zur Installation der InfiniBand-Treiber für [Linux-VMs](hpc-compute-infiniband-linux.md) verfügbar.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="operating-system"></a>Betriebssystem

Diese Erweiterung unterstützt die folgenden Betriebssystem-Distributionen, abhängig von der Treiberunterstützung für bestimmte BS-Versionen. Notieren Sie sich die jeweilige InfiniBand-NIC für die relevanten VM-Größen der H- und N-Serie.

| Distribution | InfiniBand-NIC-Treiber |
|---|---|
| Windows 10 | CX3-Pro, CX5, CX6 |
| Windows Server 2019 | CX3-Pro, CX5, CX6 |
| Windows Server 2016 | CX3-Pro, CX5, CX6 |
| Windows Server 2012 R2 | CX3-Pro, CX5, CX6 |
| Windows Server 2012 | CX3-Pro, CX5, CX6 |

### <a name="internet-connectivity"></a>Internetkonnektivität

Die Microsoft Azure-Erweiterung für InfiniBand-Treiber erfordert, dass der virtuelle Zielcomputer mit dem Internet verbunden ist und Zugriff hat.

## <a name="extension-schema"></a>Erweiterungsschema

Der folgende JSON-Code zeigt das Schema für die Erweiterung.

```json
{
  "name": "<myExtensionName>",
  "type": "extensions",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <myVM>)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "InfiniBandDriverWindows",
    "typeHandlerVersion": "1.5",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>Eigenschaften

| Name | Wert/Beispiel | Datentyp |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.HpcCompute | Zeichenfolge |
| type | InfiniBandDriverWindows | Zeichenfolge |
| typeHandlerVersion | 1.5 | INT |



## <a name="deployment"></a>Bereitstellung


### <a name="azure-resource-manager-template"></a>Azure Resource Manager-Vorlage 

Azure-VM-Erweiterungen können mithilfe von Azure Resource Manager-Vorlagen bereitgestellt werden. Vorlagen sind ideal, wenn Sie virtuelle Computer bereitstellen, die nach der Bereitstellung konfiguriert werden müssen.

Die JSON-Konfiguration für eine VM-Erweiterung kann innerhalb der VM-Ressource geschachtelt oder im Stamm bzw. auf der obersten Ebene einer Resource Manager-JSON-Vorlage platziert werden. Die Platzierung der JSON-Konfiguration wirkt sich auf den Wert von Name und Typ der Ressource aus. Weitere Informationen finden Sie unter [Set name and type for child resources](../../azure-resource-manager/templates/child-resource-name-type.md) (Festlegen von Name und Typ für untergeordnete Ressourcen). 

Im folgenden Beispiel wird davon ausgegangen, dass die Erweiterung in der VM-Ressource geschachtelt ist. Beim Schachteln der Ressource für die Erweiterung wird der JSON-Code im `"resources": []`-Objekt des virtuellen Computers platziert.

```json
{
  "name": "myExtensionName",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', myVM)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "InfiniBandDriverWindows",
    "typeHandlerVersion": "1.5",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "InfiniBandDriverWindows" `
    -ExtensionType "InfiniBandDriverWindows" `
    -TypeHandlerVersion 1.5 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name InfiniBandDriverWindows \
  --publisher Microsoft.HpcCompute \
  --version 1.5 
```

### <a name="add-extension-to-a-virtual-machine-scale-set"></a>Hinzufügen einer Erweiterung zu einer VM-Skalierungsgruppe

Durch folgendes Beispiel wird die neueste Version (1.5) der Erweiterung „InfiniBandDriverWindows“ auf allen RDMA-fähigen VMs in einer vorhandenen VM-Skalierungsgruppe mit dem Namen *myVMSS* installiert, die in der Ressourcengruppe mit dem Namen *myResourceGroup* bereitgestellt wurde:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.5"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
```


## <a name="troubleshoot-and-support"></a>Problembehandlung und Support

### <a name="troubleshoot"></a>Problembehandlung

Daten zum Status von Erweiterungsbereitstellungen können über das Azure-Portal und mithilfe von Azure PowerShell und der Azure-Befehlszeilenschnittstelle abgerufen werden. Führen Sie den folgenden Befehl aus, um den Bereitstellungsstatus von Erweiterungen für einen bestimmten virtuellen Computer anzuzeigen.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Die Ausgabe der Erweiterungsausführung wird in der folgenden Datei protokolliert. In dieser Datei finden Sie Informationen zum Nachverfolgen des Status von Installationen sowie zur Problembehandlung.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.InfiniBandDriverWindows\
```

### <a name="exit-codes"></a>Exitcodes

Die folgende Tabelle beschreibt die Bedeutung und empfohlene Aktionen basierend auf Exitcodes des Installationsprozesses für die Erweiterung.

| Fehlercode | Bedeutung | Mögliche Aktion |
| :---: | --- | --- |
| 0 | Vorgang erfolgreich |
| 3010 | Vorgang erfolgreich. Es ist ein Neustart erforderlich. |
| 100 | Der Vorgang wird nicht unterstützt oder konnte nicht abgeschlossen werden. | Mögliche Ursachen: PowerShell-Version nicht unterstützt, VM-Größe ist keine InfiniBand-fähige VM, Fehler beim Herunterladen von Daten. Überprüfen Sie die Protokolldateien, um die Ursache des Fehlers zu ermitteln. |
| 240, 840 | Timeout bei Vorgang. | Wiederholen Sie den Vorgang. |
| -1 | Ausnahme. | Überprüfen Sie die Protokolldateien, um die Ursache der Ausnahme zu ermitteln. |

### <a name="support"></a>Support

Sollten Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie sich über das [MSDN Azure-Forum oder über das Stack Overflow-Forum](https://azure.microsoft.com/support/community/) mit Azure-Experten in Verbindung setzen. Alternativ können Sie auf der [Website des Azure-Supports](https://azure.microsoft.com/support/options/) einen Supportfall erstellen. Informationen zur Nutzung von Azure-Support finden Sie unter [Microsoft Azure-Support-FAQ](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu VMs mit InfiniBand-Aktivierung (r-Größen) finden Sie unter [H-Serie](../sizes-hpc.md) und [N-Serie](../sizes-gpu.md).

> [!div class="nextstepaction"]
> [Informationen zu Erweiterungen und Features für virtuelle Computer für Linux](features-linux.md)
