---
title: NVIDIA-GPU-Treibererweiterung – Azure-Windows-VMs
description: Microsoft Azure-Erweiterung für die Installation von NVIDIA-GPU-Treibern auf Compute-VMs der N-Serie unter Windows
services: virtual-machines
documentationcenter: ''
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.collection: windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/14/2021
ms.author: amverma
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8e164fad73fd04ea2f9093e99b454f43aa441088
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130038306"
---
# <a name="nvidia-gpu-driver-extension-for-windows"></a>NVIDIA-GPU-Treibererweiterung für Windows

## <a name="overview"></a>Übersicht

Diese Erweiterung installiert NVIDIA-GPU-Treiber auf Windows-VMs der N-Serie. Je nach VM-Familie installiert die Erweiterung CUDA- oder GRID-Treiber. Bei der Installation von NVIDIA Treibern mit dieser Erweiterung akzeptieren Sie die Bedingungen des [NVIDIA-Endbenutzer-Lizenzvertrags](https://go.microsoft.com/fwlink/?linkid=874330) und stimmen diesen zu. Während der Installation wird der virtuelle Computer möglicherweise neu gestartet, um die Treibereinrichtung abzuschließen.

Es sind Anleitungen für die manuelle Installation der Treiber und aktuell unterstützten Versionen verfügbar. Weitere Informationen finden Sie unter [Installieren von NVIDIA GPU-Treibern für virtuelle Computer der Serie N mit Windows](../windows/n-series-driver-setup.md).
Es ist auch eine Erweiterung zum Installieren von NVIDIA-GPU-Treibern auf [Linux-VMs der N-Serie](hpccompute-gpu-linux.md) verfügbar.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="operating-system"></a>Betriebssystem

Diese Erweiterung unterstützt die folgenden Betriebssysteme:

| Distribution | Version |
|---|---|
| Windows 10 | Core |
| Windows Server 2019 | Core |
| Windows Server 2016 | Core |
| Windows Server 2012 R2 | Core |

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
    "type": "NvidiaGpuDriverWindows",
    "typeHandlerVersion": "1.4",
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
| type | NvidiaGpuDriverWindows | Zeichenfolge |
| typeHandlerVersion | 1.4 | INT |


## <a name="deployment"></a>Bereitstellung

### <a name="azure-portal"></a>Azure-Portal

Sie können Azure Nvidia-VM-Erweiterungen im Azure-Portal bereitstellen.

1. Wechseln Sie in einem Browser zum [Azure-Portal](https://portal.azure.com).

2. Navigieren Sie zu der VM, auf der Sie den Treiber installieren möchten.

3. Wählen Sie im Menü auf der linken Seite die Option **Erweiterungen** aus.

    :::image type="content" source="./media/nvidia-ext-portal/extensions-menu.png" alt-text="Screenshot: Auswahl von Erweiterungen im Menü des Azure-Portals.":::

4. Wählen Sie **Hinzufügen**.

    :::image type="content" source="./media/nvidia-ext-portal/add-extension.png" alt-text="Screenshot: Hinzufügen einer VM-Erweiterung für die ausgewählte VM.":::

5. Scrollen Sie zu **NVIDIA GPU-Treibererweiterung**, wählen Sie sie aus, und wählen Sie dann **Weiter** aus.

    :::image type="content" source="./media/nvidia-ext-portal/select-nvidia-extension.png" alt-text="Screenshot: Auswählen des NVIDIA GPU-Treibers.":::

6. Wählen Sie **Überprüfen und erstellen** aus, und warten Sie einige Minuten, bis der Treiber bereitgestellt wurde.

    :::image type="content" source="./media/nvidia-ext-portal/create-nvidia-extension.png" alt-text="Screenshot: Auswählen der Schaltfläche „Überprüfen und Erstellen“.":::
  
7. Bestätigen Sie, dass die Erweiterung der Liste der installierten Erweiterungen hinzugefügt wird.

    :::image type="content" source="./media/nvidia-ext-portal/verify-extension.png" alt-text="Screenshot: Neue Erweiterung in der Liste der Erweiterungen für die VM":::.

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
    "type": "NvidiaGpuDriverWindows",
    "typeHandlerVersion": "1.4",
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
    -ExtensionName "NvidiaGpuDriverWindows" `
    -ExtensionType "NvidiaGpuDriverWindows" `
    -TypeHandlerVersion 1.4 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name NvidiaGpuDriverWindows \
  --publisher Microsoft.HpcCompute \
  --version 1.4 \
  --settings '{ \
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

Die Ausgabe der Erweiterungsausführung wird im folgenden Verzeichnis protokolliert:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverWindows\
```

### <a name="error-codes"></a>Fehlercodes

| Fehlercode | Bedeutung | Mögliche Aktion |
| :---: | --- | --- |
| 0 | Vorgang erfolgreich |
| 1 | Vorgang erfolgreich. Es ist ein Neustart erforderlich. |
| 100 | Der Vorgang wird nicht unterstützt oder konnte nicht abgeschlossen werden. | Mögliche Ursachen: PowerShell-Version nicht unterstützt, VM-Größe ist keine VM der N-Serie, Fehler beim Herunterladen von Daten. Überprüfen Sie die Protokolldateien, um die Ursache des Fehlers zu ermitteln. |
| 240, 840 | Timeout bei Vorgang. | Wiederholen Sie den Vorgang. |
| -1 | Ausnahme. | Überprüfen Sie die Protokolldateien, um die Ursache der Ausnahme zu ermitteln. |
| -5x | Vorgang aufgrund ausstehenden Neustarts unterbrochen. | Starten Sie die VM neu. Die Installation wird nach dem Neustart fortgesetzt. Eine Deinstallation sollte manuell aufgerufen werden. |


### <a name="support"></a>Support

Sollten Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie sich über das [MSDN Azure-Forum oder über das Stack Overflow-Forum](https://azure.microsoft.com/support/community/) mit Azure-Experten in Verbindung setzen. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie „Support erhalten“ aus. Informationen zur Nutzung von Azure-Support finden Sie unter [Microsoft Azure-Support-FAQ](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Erweiterungen finden Sie unter [Erweiterungen und Features für virtuelle Computer für Windows](features-windows.md).

Weitere Informationen zu virtuellen Computern der N-Serie finden Sie unter [Für GPU optimierte VM-Größen](../sizes-gpu.md).
