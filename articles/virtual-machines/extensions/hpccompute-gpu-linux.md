---
title: NVIDIA-GPU-Treibererweiterung – Azure-Linux-VMs
description: Microsoft Azure-Erweiterung für die Installation von NVIDIA-GPU-Treibern auf Compute-VMs der N-Serie unter Linux.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: hpc
ms.collection: linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/15/2021
ms.author: amverma
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4b8f4b2d9b86620e16064f6d005d5e538e78e863
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132492547"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>NVIDIA-GPU-Treibererweiterung für Linux

## <a name="overview"></a>Übersicht

Diese Erweiterung installiert NVIDIA-GPU-Treiber auf Linux-VMs der N-Serie. Je nach VM-Familie installiert die Erweiterung CUDA- oder GRID-Treiber. Bei der Installation von NVIDIA Treibern mit dieser Erweiterung akzeptieren Sie die Bedingungen des [NVIDIA-Endbenutzer-Lizenzvertrags](https://go.microsoft.com/fwlink/?linkid=874330) und stimmen diesen zu. Während der Installation wird der virtuelle Computer möglicherweise neu gestartet, um die Treibereinrichtung abzuschließen.

Es sind Anleitungen für die manuelle Installation der Treiber und aktuell unterstützten Versionen verfügbar. Weitere Informationen finden Sie unter [Installieren von NVIDIA GPU-Treibern für virtuelle Computer der Serie N mit Linux](../linux/n-series-driver-setup.md).
Es ist auch eine Erweiterung zum Installieren von NVIDIA-GPU-Treibern auf [Windows-VMs der N-Serie](hpccompute-gpu-windows.md) verfügbar.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="operating-system"></a>Betriebssystem

Diese Erweiterung unterstützt die folgenden Betriebssystem-Distributionen, abhängig von der Treiberunterstützung für bestimmte BS-Versionen.

| Distribution | Version |
|---|---|
| Linux: Ubuntu | 16.04 LTS, 18.04 LTS, 20.04 LTS |
| Linux: Red Hat Enterprise Linux | 7.3, 7.4, 7.5, 7.6, 7.7, 7.8 |
| Linux: CentOS | 7.3, 7.4, 7.5, 7.6, 7.7, 7.8 |

> [!NOTE]
> Die neueste unterstützte CUDA-Treiberversion für VMs der NC-Serie ist derzeit 470.82.01. Neuere Treiberversionen werden auf den K80-Karten in NC nicht unterstützt. Während die Erweiterung mit dem Ende des Supports für NC aktualisiert wird, installieren Sie die CUDA-Treiber für K80-Karten der NC-Serie manuell.


### <a name="internet-connectivity"></a>Internetkonnektivität

Die Microsoft Azure-Erweiterung für NVIDIA-GPU-Treiber erfordert, dass der virtuelle Zielcomputer mit dem Internet verbunden ist und Zugriff hat.

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
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.6",
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
| type | NvidiaGpuDriverLinux | Zeichenfolge |
| typeHandlerVersion | 1.6 | INT |

### <a name="settings"></a>Einstellungen

Alle Einstellungen sind optional. Das Standardverhalten ist, den Kernel nicht zu aktualisieren, wenn dies für die Treiberinstallation nicht erforderlich ist, den neuesten unterstützten Treiber und das CUDA-Toolkit (falls zutreffend) zu installieren.

| Name | BESCHREIBUNG | Standardwert | Gültige Werte | Datentyp |
| ---- | ---- | ---- | ---- | ---- |
| updateOS | Aktualisieren des Kernel, auch wenn nicht für die Treiberinstallation erforderlich ist | false | true, false | boolean |
| driverVersion | NV: GRID-Treiberversion<br> NC/ND: CUDA-Toolkitversion. Die neuesten Treiber für den ausgewählten CUDA werden automatisch installiert. | latest | [Liste](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json) der unterstützten Treiberversionen | Zeichenfolge |
| installCUDA | UDA-Toolkit installieren. Nur relevant für virtuelle Computer der NC-/ND-Serie. | true | true, false | boolean |


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
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.6",
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
    -ExtensionName "NvidiaGpuDriverLinux" `
    -ExtensionType "NvidiaGpuDriverLinux" `
    -TypeHandlerVersion 1.6 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

Im folgenden Beispiel wird das obige Beispiel für Azure Resource Manager und PowerShell gespiegelt.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name NvidiaGpuDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.6 
```

Außerdem werden zwei optionale benutzerdefinierte Einstellungen als Beispiel für die Installation von Nicht-Standard-Treibern hinzugefügt. Insbesondere wird der Betriebssystemkernel auf die neueste Version aktualisiert und ein bestimmter CUDA-Toolkit-Versionstreiber installiert. Auch hier ist zu beachten, dass „--settings“ optional und Standard ist. Beachten Sie, dass die Aktualisierung des Kernels die Installationszeiten für Erweiterungen verlängern kann. Außerdem ist die Auswahl einer bestimmten (älteren) CUDA-Toolkit-Version möglicherweise nicht immer mit neueren Kernels kompatibel.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name NvidiaGpuDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.6 \
  --settings '{ \
    "updateOS": true, \
    "driverVersion": "10.0.130" \
  }'
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

Die Ausgabe der Erweiterungsausführung wird in der folgenden Datei protokolliert. In dieser Datei finden Sie Informationen zum Nachverfolgen des Status von Installationen (mit langer Laufzeit) sowie zur Problembehandlung.

```bash
/var/log/azure/nvidia-vmext-status
```

### <a name="exit-codes"></a>Exitcodes

| Exitcode | Bedeutung | Mögliche Aktion |
| :---: | --- | --- |
| 0 | Vorgang erfolgreich |
| 1 | Falsche Verwendung der Erweiterung | Protokoll zur Ausführungsausgabe überprüfen |
| 10 | Linux Integration Services für Hyper-V und Azure nicht verfügbar oder nicht installiert | Ispci-Ausgabe überprüfen |
| 11 | NVIDIA-GPU kann bei dieser VM-Größe nicht gefunden werden. | Verwenden Sie eine [VM-Größe und ein Betriebssystem](../linux/n-series-driver-setup.md), die unterstützt werden. |
| 12 | Imageangebot nicht unterstützt |
| 13 | VM-Größe nicht unterstützt | Verwenden eines virtuellen Computers der N-Serie für die Bereitstellung |
| 14 | Vorgang nicht erfolgreich | Protokoll zur Ausführungsausgabe überprüfen |


### <a name="support"></a>Support

Sollten Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie sich über das [MSDN Azure-Forum oder über das Stack Overflow-Forum](https://azure.microsoft.com/support/community/) mit Azure-Experten in Verbindung setzen. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie „Support erhalten“ aus. Informationen zur Nutzung von Azure-Support finden Sie unter [Microsoft Azure-Support-FAQ](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Erweiterungen finden Sie unter [Erweiterungen und Features für virtuelle Computer für Linux](features-linux.md).

Weitere Informationen zu virtuellen Computern der N-Serie finden Sie unter [Für GPU optimierte VM-Größen](../sizes-gpu.md).
