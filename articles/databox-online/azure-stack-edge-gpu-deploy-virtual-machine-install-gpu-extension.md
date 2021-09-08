---
title: Installieren der GPU-Erweiterung auf GPU-VMs auf Ihrem Azure Stack Edge Pro GPU-Gerät
description: Beschreibt, wie GPU-Erweiterungen auf GPU-VMs auf einem Azure Stack Edge Pro GPU-Gerät installiert werden.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/02/2021
ms.author: alkohli
ms.openlocfilehash: 80337c4953182e903655eddb360fc8b49a88ca59
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346319"
---
# <a name="install-gpu-extension-on-vms-for-your-azure-stack-edge-pro-gpu-device"></a>Installieren der GPU-Erweiterung auf VMs auf Ihrem Azure Stack Edge Pro GPU-Gerät

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Dieser Artikel beschreibt, wie Sie die GPU-Treibererweiterung installieren, um die entsprechenden Nvidia-Treiber auf den GPU-VMs zu installieren, die auf Ihrem Azure Stack Edge-Gerät ausgeführt werden. In diesem Artikel werden die Schritte zur Installation einer GPU-Erweiterung mithilfe von Azure Resource Manager-Vorlagen auf Windows- und Linux-VMs beschrieben.

> [!NOTE]
> Im Azure-Portal können Sie eine GPU-Erweiterung während der VM-Erstellung oder nach der Bereitstellung der VM installieren. Informationen zu den Schritten und Anforderungen finden Sie unter [Bereitstellen virtueller GPU-Computer](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md).


## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie Folgendes sicher, bevor Sie die GPU-Erweiterung auf den GPU-VMs installieren, die auf Ihrem Gerät ausgeführt werden:

1. Sie haben Zugriff auf ein Azure Stack Edge-Gerät, auf dem Sie eine oder mehrere GPU-VMs bereitgestellt haben. Erfahren Sie, wie Sie [eine GPU-VM auf Ihrem Gerät bereitstellen](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md).

    - Stellen Sie sicher, dass der für das Computenetzwerk aktivierte Port auf Ihrem Gerät mit dem Internet verbunden ist und Zugriff hat. Die GPU-Treiber werden über das Internet heruntergeladen.

        Im folgenden Beispiel ist Port 2 mit dem Internet verbunden und wird zum Aktivieren des Computenetzwerks verwendet. Wenn Kubernetes in Ihrer Umgebung nicht bereitgestellt ist, können Sie das Zuweisen der Kubernetes-Knoten-IP und der externen Dienst-IP überspringen.

        ![Screenshot des Bereichs „Compute“ für ein Azure Stack Edge-Gerät. Computeeinstellungen für Port 2 sind hervorgehoben.](media/azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension/enable-compute-network-1.png)
1. [Laden Sie die GPU-Erweiterungsvorlagen und Parameterdateien](https://aka.ms/ase-vm-templates) auf Ihren Clientcomputer herunter. Entpacken Sie die Vorlagen und Dateien in einem Verzeichnis, das Sie als Arbeitsverzeichnis verwenden.
1. Vergewissern Sie sich, dass der Client, den Sie für den Zugriff auf Ihr Gerät verwenden, weiterhin über Azure PowerShell mit dem Azure Resource Manager verbunden ist. Die Verbindung mit Azure Resource Manager läuft alle 1,5 Stunden oder beim Neustart des Azure Stack Edge-Geräts ab. In diesem Fall geben alle ausgeführten Cmdlets eine Fehlermeldung mit dem Inhalt zurück, dass keine Verbindung mit Azure mehr besteht. Sie müssen sich erneut anmelden. Eine ausführliche Anleitung finden Sie unter [Herstellen einer Verbindung mit Azure Resource Manager auf Ihrem Azure Stack Edge-Gerät](azure-stack-edge-gpu-connect-resource-manager.md).

## <a name="edit-parameters-file"></a>Bearbeiten der Parameterdatei

Je nach Betriebssystem Ihrer VM können Sie die GPU-Erweiterung für Windows oder für Linux installieren.


### <a name="windows"></a>[Windows](#tab/windows)

Um Nvidia-GPU-Treiber für eine vorhandene VM bereitzustellen, bearbeiten Sie die Parameterdatei `addGPUExtWindowsVM.parameters.json` und stellen dann die Vorlage `addGPUextensiontoVM.json` bereit.

Die Datei `addGPUExtWindowsVM.parameters.json` kann die folgenden Parameter enthalten:

```json
"parameters": { 
    "vmName": {
    "value": "<name of the VM>" 
    },
    "extensionName": {
    "value": "<name for the extension. Example: windowsGpu>" 
    },
    "publisher": {
    "value": "Microsoft.HpcCompute" 
    },
    "type": {
    "value": "NvidiaGpuDriverWindows" 
    },
    "typeHandlerVersion": {
    "value": "1.3" 
    },
    "settings": {
    "value": {
    "DriverURL" : "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
    "DriverCertificateUrl" : "https://go.microsoft.com/fwlink/?linkid=871664",
    "DriverType":"CUDA"
    }
    }
    }
```

### <a name="linux"></a>[Linux](#tab/linux)

Um Nvidia-GPU-Treiber für eine vorhandene Linux-VM bereitzustellen, bearbeiten Sie die Parameterdatei und stellen dann die Vorlage `addGPUextensiontoVM.json` bereit. 

Wenn Sie Ubuntu oder Red Hat Enterprise Linux (RHEL) verwenden, akzeptiert die Datei `addGPUExtLinuxVM.parameters.json` die folgenden Parameter:

```powershell
"parameters": { 
    "vmName": {
    "value": "<name of the VM>" 
    },
    "extensionName": {
    "value": "<name for the extension. Example: linuxGpu>" 
    },
    "publisher": {
    "value": "Microsoft.HpcCompute" 
    },
    "type": {
    "value": "NvidiaGpuDriverLinux" 
    },
    "typeHandlerVersion": {
    "value": "1.3" 
    },
    "settings": {
    }
    }
    }
```

Im Folgenden finden Sie die Beispielparameterdatei für Ubuntu, die in diesem Artikel verwendet wurde:

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "VM1" 
        },
        "extensionName": {
            "value": "gpuLinux" 
        },
        "publisher": {
            "value": "Microsoft.HpcCompute" 
        },
        "type": {
            "value": "NvidiaGpuDriverLinux" 
        },
        "typeHandlerVersion": {
            "value": "1.3" 
        },
        "settings": {
        }
    }
}
```

### <a name="gpu-vms-from-rhel-byos-images"></a>GPU-VMs aus RHEL-BYOS-Images

Wenn Sie Ihre VM mithilfe eines Red Hat Enterprise Linux Bring Your Own Subscription-Image (RHEL BYOS) erstellt haben, stellen Sie Folgendes sicher:

- Sie haben die Schritte unter [Verwenden des RHEL BYOS-Images](azure-stack-edge-gpu-create-virtual-machine-image.md)ausgeführt. 
- Nachdem Sie die GPU-VM erstellt haben, haben Sie über das Red Hat-Kundenportal eine Registrierung und ein Abonnement für die VM abgeschlossen. Wenn Ihre VM nicht ordnungsgemäß registriert ist, wird die Installation nicht fortgesetzt, da sie nicht berechtigt ist. Weitere Informationen unter [Registrieren und automatisches Abonnieren in einem Schritt mit dem Red Hat Subscription Manager](https://access.redhat.com/solutions/253273). Durch diesen Schritt kann das Installationsskript relevante Pakete für den GPU-Treiber herunterladen.
- Entweder Sie installieren das Paket `vulkan-filesystem` manuell, oder Sie fügen Ihrer Yum-Repositoryliste das CentOS7-Repository hinzu. Wenn Sie die GPU-Erweiterung installieren, sucht das Installationsskript nach einem `vulkan-filesystem`-Paket, das sich im CentOS7-Repository (für RHEL7) befindet. 

---

## <a name="deploy-template"></a>Bereitstellen der Vorlage 

### <a name="windows"></a>[Windows](#tab/windows)

Stellen Sie die Vorlage `addGPUextensiontoVM.json` bereit. Diese Vorlage stellt die Erweiterung für eine vorhandene VM bereit. Führen Sie den folgenden Befehl aus:

```powershell
$templateFile = "<Path to addGPUextensiontoVM.json>" 
$templateParameterFile = "<Path to addGPUExtWindowsVM.parameters.json>" 
$RGName = "<Name of your resource group>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Name for your deployment>"
```
> [!NOTE]
> Die Bereitstellung der Erweiterung ist ein zeitintensiver Auftrag, der etwa 10 Minuten dauert.

Hier ist eine Beispielausgabe:

```powershell
PS C:\WINDOWS\system32> "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addGPUExtWindowsVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment3"

DeploymentName          : deployment3
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/16/2020 12:18:50 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM2
                          extensionName    String                     windowsgpuext
                          publisher        String                     Microsoft.HpcCompute
                          type             String                     NvidiaGpuDriverWindows
                          typeHandlerVersion  String                     1.3
                          settings         Object                     {
                            "DriverURL": "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
                            "DriverCertificateUrl": "https://go.microsoft.com/fwlink/?linkid=871664",
                            "DriverType": "CUDA"
                          }

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```

### <a name="linux"></a>[Linux](#tab/linux)

Stellen Sie die Vorlage `addGPUextensiontoVM.json` bereit. Diese Vorlage stellt die Erweiterung für eine vorhandene VM bereit. Führen Sie den folgenden Befehl aus:

```powershell
$templateFile = "Path to addGPUextensiontoVM.json" 
$templateParameterFile = "Path to addGPUExtLinuxVM.parameters.json" 
$RGName = "<Name of your resource group>" 
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Name for your deployment>"
``` 

> [!NOTE]
> Die Bereitstellung der Erweiterung ist ein zeitintensiver Auftrag, der etwa 10 Minuten dauert.

Hier ist eine Beispielausgabe:

```powershell
Copyright (C) Microsoft Corporation. All rights reserved.
Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addGPUExtLinuxVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "rg2"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "delpoyment7"

DeploymentName          : delpoyment7
ResourceGroupName       : rg2
ProvisioningState       : Succeeded
Timestamp               : 12/10/2020 10:43:23 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM1
                          extensionName    String                     gpuLinux
                          publisher        String                     Microsoft.HpcCompute
                          type             String                     NvidiaGpuDriverLinux
                          typeHandlerVersion  String                     1.3
                          settings         Object                     {
                            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
                            "PUBKEY_URL":
                          "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                            "CUDA_ver": "10.0.130",
                            "InstallCUDA": "true"
                          }

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```
---

## <a name="track-deployment"></a>Nachverfolgen der Bereitstellung

### <a name="windows"></a>[Windows](#tab/windows)

Führen Sie den folgenden Befehl aus, um den Bereitstellungsstatus von Erweiterungen für eine bestimmte VM anzuzeigen: 

```powershell
Get-AzureRmVMExtension -ResourceGroupName <Name of resource group> -VMName <Name of VM> -Name <Name of the extension>
```
Hier ist eine Beispielausgabe:

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM2 -Name windowsgpuext

ResourceGroupName       : myasegpuvm1
VMName                  : VM2
Name                    : windowsgpuext
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.HpcCompute
ExtensionType           : NvidiaGpuDriverWindows
TypeHandlerVersion      : 1.3
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM2/extensions/windowsgpuext
PublicSettings          : {
                            "DriverURL": "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
                            "DriverCertificateUrl": "https://go.microsoft.com/fwlink/?linkid=871664",
                            "DriverType": "CUDA"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

Die Ausgabe der Erweiterungsausführung wird in der folgenden Datei protokolliert. Sie können den Installationsstatus in dieser Datei `C:\Packages\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverWindows\1.3.0.0\Status` nachverfolgen. 


Eine erfolgreiche Installation wird durch eine `message` vom Typ `Enable Extension` und einen `status` mit dem Wert `success` angegeben.

```powershell
"status":  {
                       "formattedMessage":  {
                                                "message":  "Enable Extension",
                                                "lang":  "en"
                                            },
                       "name":  "NvidiaGpuDriverWindows",
                       "status":  "success",
```

### <a name="linux"></a>[Linux](#tab/linux)

Die Vorlagenbereitstellung ist ein zeitintensiver Auftrag. Um den Bereitstellungsstatus von Erweiterungen für eine bestimmte VM zu überprüfen, öffnen Sie eine weitere PowerShell-Sitzung (als Administrator). Führen Sie den folgenden Befehl aus: 

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName <VM Name> -Name <Extension Name>
```
Hier ist eine Beispielausgabe: 

```powershell
Copyright (C) Microsoft Corporation. All rights reserved.
Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName rg2 -VMName VM1 -Name gpulinux

ResourceGroupName       : rg2
VMName                  : VM1
Name                    : gpuLinux
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.HpcCompute
ExtensionType           : NvidiaGpuDriverLinux
TypeHandlerVersion      : 1.3
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/rg2/providers/Microsoft.Compute/virtualMachines/VM1/extensions/gpuLinux
PublicSettings          : {
                            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
                            "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                            "CUDA_ver": "10.0.130",
                            "InstallCUDA": "true"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

> [!NOTE]
> Nach Abschluss der Bereitstellung wird der `ProvisioningState` in `Succeeded` geändert.

Die Ausgabe der Erweiterungsausführung wird in der folgenden Datei protokolliert: `/var/log/azure/nvidia-vmext-status`.

---

## <a name="verify-driver-installation"></a>Überprüfen der Treiberinstallation

### <a name="windows"></a>[Windows](#tab/windows)

Melden Sie sich auf der VM an, und führen Sie das mit dem Treiber installierte Befehlszeilen-Hilfsprogramm nvidia-smi aus. Die Datei `nvidia-smi.exe` befindet sich unter `C:\Program Files\NVIDIA Corporation\NVSMI\nvidia-smi.exe`. Wenn die Datei nicht angezeigt wird, läuft die Treiberinstallation möglicherweise immer noch im Hintergrund. Warten Sie 10 Minuten, und überprüfen Sie dann den Status erneut.

Wenn der Treiber installiert wurde, wird eine Ausgabe ähnlich der folgenden angezeigt: 

```powershell
PS C:\Users\Administrator> cd "C:\Program Files\NVIDIA Corporation\NVSMI"
PS C:\Program Files\NVIDIA Corporation\NVSMI> ls

    Directory: C:\Program Files\NVIDIA Corporation\NVSMI

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        2/26/2020  12:00 PM         849640 MCU.exe
-a----        2/26/2020  12:00 PM         443104 nvdebugdump.exe
-a----        2/25/2020   2:06 AM          81823 nvidia-smi.1.pdf
-a----        2/26/2020  12:01 PM         566880 nvidia-smi.exe
-a----        2/26/2020  12:01 PM         991344 nvml.dll

PS C:\Program Files\NVIDIA Corporation\NVSMI> .\nvidia-smi.exe
Wed Dec 16 00:35:51 2020
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 442.50       Driver Version: 442.50       CUDA Version: 10.2     |
|-------------------------------+----------------------+----------------------+
| GPU  Name            TCC/WDDM | Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  Tesla T4            TCC  | 0000503C:00:00.0 Off |                    0 |
| N/A   35C    P8    11W /  70W |      8MiB / 15205MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+
PS C:\Program Files\NVIDIA Corporation\NVSMI>
```

Weitere Informationen finden Sie unter [Nvidia-GPU-Treibererweiterung für Windows](../virtual-machines/extensions/hpccompute-gpu-windows.md).

> [!NOTE]
> Nachdem Sie die Installation des GPU-Treibers und der GPU-Erweiterung abgeschlossen haben, müssen Sie keinen Port mit Internetzugriff für Compute mehr verwenden.

### <a name="linux"></a>[Linux](#tab/linux)

Führen Sie diese Schritte aus, um die Treiberinstallation zu überprüfen:

1. Stellen Sie eine Verbindung mit der GPU-VM her. Befolgen Sie die Anweisungen unter [Herstellen einer Verbindung mit einer Linux-VM](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-linux-vm). 

    Hier ist eine Beispielausgabe:

    ```powershell
    PS C:\WINDOWS\system32> ssh -l Administrator 10.57.50.60
    Administrator@10.57.50.60's password:
    Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.0.0-1031-azure x86_64)
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
      System information as of Thu Dec 10 22:57:01 UTC 2020
    
      System load:  0.0                Processes:           133
      Usage of /:   24.8% of 28.90GB   Users logged in:     0
      Memory usage: 2%                 IP address for eth0: 10.57.50.60
      Swap usage:   0%
    
    249 packages can be updated.
    140 updates are security updates.
    
    Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.0.0-1031-azure x86_64)    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage    
      System information as of Thu Dec 10 22:57:01 UTC 2020    
      System load:  0.0                Processes:           133
      Usage of /:   24.8% of 28.90GB   Users logged in:     0
      Memory usage: 2%                 IP address for eth0: 10.57.50.60
      Swap usage:   0%
        
    249 packages can be updated.
    140 updates are security updates.
    
    New release '20.04.1 LTS' available.
    Run 'do-release-upgrade' to upgrade to it.
        
    *** System restart required ***
    Last login: Thu Dec 10 21:49:29 2020 from 10.90.24.23
    To run a command as administrator (user "root"), use "sudo <command>".
    See "man sudo_root" for details.
    
    Administrator@VM1:~$
    ```
2. Führen Sie das mit dem Treiber installierte Befehlszeilen-Hilfsprogramm nvidia-smi aus. Wenn der Treiber erfolgreich installiert wurde, können Sie das Hilfsprogramm ausführen, das die folgende Ausgabe anzeigt:

    ```powershell
    Administrator@VM1:~$ nvidia-smi
    Thu Dec 10 22:58:46 2020
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 455.45.01    Driver Version: 455.45.01    CUDA Version: 11.1     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            Off  | 0000941F:00:00.0 Off |                    0 |
    | N/A   48C    P0    27W /  70W |      0MiB / 15109MiB |      5%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    Administrator@VM1:~$
    ```

Weitere Informationen finden Sie unter [Nvidia-GPU-Treibererweiterung für Linux](../virtual-machines/extensions/hpccompute-gpu-linux.md).

> [!NOTE]
> Nachdem Sie die Installation des GPU-Treibers und der GPU-Erweiterung abgeschlossen haben, müssen Sie keinen Port mit Internetzugriff für Compute mehr verwenden.


---


## <a name="remove-gpu-extension"></a>Entfernen der GPU-Erweiterung

Verwenden Sie zum Entfernen der GPU-Erweiterung den folgenden Befehl:

`Remove-AzureRmVMExtension -ResourceGroupName <Resource group name> -VMName <VM name> -Name <Extension name>`

Hier ist eine Beispielausgabe:

```powershell
PS C:\azure-stack-edge-deploy-vms> Remove-AzureRmVMExtension -ResourceGroupName rgl -VMName WindowsVM -Name windowsgpuext
Virtual machine extension removal operation
This cmdlet will remove the specified virtual machine extension. Do you want to continue? [Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
Requestld IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------    
          True                OK         OK
```


## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel werden folgende Themen erläutert:

- [Behandeln von Problemen mit der GPU-Erweiterung](azure-stack-edge-gpu-troubleshoot-virtual-machine-gpu-extension-installation.md)
- [Überwachen der VM-Aktivität auf Ihrem Gerät](azure-stack-edge-gpu-monitor-virtual-machine-activity.md)
- [Verwalten von VM-Datenträgern](azure-stack-edge-gpu-manage-virtual-machine-disks-portal.md).
- [Verwalten von VM-Netzwerkschnittstellen](azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal.md).
- [Verwalten von VM-Größen](azure-stack-edge-gpu-manage-virtual-machine-resize-portal.md).