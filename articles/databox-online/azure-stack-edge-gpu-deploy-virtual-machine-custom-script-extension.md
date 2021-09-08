---
title: Verwenden von benutzerdefinierten Skripterweiterungen für VMs auf Ihrem Azure Stack Edge Pro-Gerät
description: Erfahren Sie, wie Sie mithilfe von Vorlagen benutzerdefinierte Skripterweiterungen auf VMs (Virtual Machines, virtuelle Computer) installieren, die auf einem Azure Stack Edge Pro-Gerät ausgeführt werden.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/03/2021
ms.author: alkohli
ms.openlocfilehash: 6ed084e26b12851985e2b3b906be1367b7b4a437
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355393"
---
# <a name="deploy-custom-script-extension-on-vms-running-on-your-azure-stack-edge-pro-device"></a>Bereitstellen von benutzerdefinierten Skripterweiterungen auf VMs, die auf Ihrem Azure Stack Edge Pro-Gerät ausgeführt werden

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Die benutzerdefinierte Skripterweiterung lädt Skripts oder Befehle auf VMs auf Ihren Azure Stack Edge Pro-Geräten herunter und führt sie aus. In diesem Artikel wird erläutert, wie Sie die benutzerdefinierte Skripterweiterung mithilfe einer Azure Resource Manager-Vorlage installieren und ausführen.

## <a name="about-custom-script-extension"></a>Informationen zur benutzerdefinierten Skripterweiterung

Die benutzerdefinierte Skripterweiterung ist hilfreich bei der Konfiguration nach der Bereitstellung, bei der Softwareinstallation oder bei anderen Konfigurations-/Verwaltungsaufgaben. Sie können Skripts von Azure Storage oder einem anderen zugänglichen Speicherort im Internet herunterladen, oder Sie können Skripts oder Befehle für die Erweiterungsruntime bereitstellen.

Die Erweiterung für benutzerdefinierte Skripts ist mit Azure Resource Manager-Vorlagen integriert. Sie können sie auch mithilfe der PowerShell, der Azure-Befehlszeilenschnittstelle oder der Azure Virtual Machines-REST-API ausführen.

## <a name="os-for-custom-script-extension"></a>Betriebssystem für die benutzerdefinierte Skripterweiterung

#### <a name="supported-os-for-custom-script-extension-on-windows"></a>Unterstützte Betriebssysteme für die benutzerdefinierte Skripterweiterung unter Windows

Die benutzerdefinierte Skripterweiterung für Windows kann auf den folgenden Betriebssystemen ausgeführt werden. Andere Versionen funktionieren zwar möglicherweise ebenfalls, wurden jedoch nicht intern auf VMs getestet, die auf Azure Stack Edge Pro-Geräten ausgeführt werden.

| Distribution | Version |
|---|---|
| Windows Server 2019 | Core |
| Windows Server 2016 | Core |

#### <a name="supported-os-for-custom-script-extension-on-linux"></a>Unterstützte Betriebssysteme für die benutzerdefinierte Skripterweiterung unter Linux

Die benutzerdefinierte Skripterweiterung für Linux kann auf den von folgenden Betriebssystemen ausgeführt werden. Andere Versionen funktionieren zwar möglicherweise ebenfalls, wurden jedoch nicht intern auf VMs getestet, die auf Azure Stack Edge Pro-Geräten ausgeführt werden.

| Distribution | Version |
|---|---|
| Linux: Ubuntu | 18.04 LTS |
| Linux: Red Hat Enterprise Linux | 7.4, 7.5, 7.7 |

<!--### Script location

Instead of the scripts, in this article, we pass a command to execute via the Custom Script Extension. 

### Internet Connectivity

To download a script externally such as from GitHub or Azure Storage, make sure that the port on which you enable compute network, is connected to the internet. 

If your script is on a local server, then you may still need additional firewall and Network Security Group ports need to be opened.

> [!NOTE]
> Before you install the Custom Script extension, make sure that the port enabled for compute network on your device is connected to Internet and has access. -->

## <a name="prerequisites"></a>Voraussetzungen

1. [Laden Sie die VM-Vorlagen und Parameterdateien](https://aka.ms/ase-vm-templates) auf Ihrem Clientcomputer herunter. Entpacken Sie den Download in einem Verzeichnis, das Sie als Arbeitsverzeichnis verwenden.

1. Sie sollten bereits eine VM erstellt und auf Ihrem Gerät bereitgestellt haben. Führen Sie zum Erstellen von VMs alle Schritte unter [Bereitstellen von VMs auf Ihrem Azure Stack Edge Pro-Gerät mithilfe von Vorlagen](azure-stack-edge-gpu-deploy-virtual-machine-templates.md) aus.

    Wenn Sie ein Skript von einem externen Speicherort wie GitHub oder Azure Storage herunterladen müssen, aktivieren Sie beim Konfigurieren des Computenetzwerks den Port, der mit dem Internet verbunden ist, für Compute. Dies ermöglicht es Ihnen, das Skript herunterzuladen.

    Im folgenden Beispiel ist Port 2 mit dem Internet verbunden und wird zum Aktivieren des Computenetzwerks verwendet. Wenn Sie im vorausgehenden Schritt festgestellt haben, dass Kubernetes nicht benötigt wird, können Sie die IP-Adresse des Kubernetes-Knotens und die IP-Zuweisung für externe Dienste überspringen.

    ![Screenshot des Bereichs „Compute“ für ein Azure Stack Edge-Gerät. Computeeinstellungen für Port 2 sind hervorgehoben.](media/azure-stack-edge-gpu-deploy-virtual-machine-custom-script-extension/enable-compute-network-1.png)

## <a name="install-custom-script-extension"></a>Installieren der benutzerdefinierten Skripterweiterung

Je nach Betriebssystem Ihrer VM können Sie die benutzerdefinierte Skripterweiterung für Windows oder für Linux installieren.
 

### <a name="custom-script-extension-for-windows"></a>CustomScript-Erweiterung für Windows

Zum Bereitstellen der benutzerdefinierten Skripterweiterung für Windows für eine VM, die auf Ihrem Gerät ausgeführt wird, bearbeiten Sie die Parameterdatei `addCSExtWindowsVM.parameters.json` und stellen dann die Vorlage `addCSextensiontoVM.json` bereit.

#### <a name="edit-parameters-file"></a>Bearbeiten der Parameterdatei

Die Datei `addCSExtWindowsVM.parameters.json` kann die folgenden Parameter enthalten:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<Name of VM>" 
        },
        "extensionName": {
            "value": "<Name of extension>" 
        },
        "publisher": {
            "value": "Microsoft.Compute" 
        },
        "type": {
            "value": "CustomScriptExtension" 
        },
        "typeHandlerVersion": {
            "value": "1.10" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "<Command to execute>"
            }
        }
    }
}
```
Geben Sie den Namen Ihrer VM, den Namen für die Erweiterung und den auszuführenden Befehl an.

Im Folgenden finden Sie die Beispielparameterdatei, die in diesem Artikel verwendet wurde.

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "VM5" 
        },
        "extensionName": {
            "value": "CustomScriptExtension" 
        },
        "publisher": {
            "value": "Microsoft.Compute" 
        },
        "type": {
            "value": "CustomScriptExtension" 
        },
        "typeHandlerVersion": {
            "value": "1.10" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "md C:\\Users\\Public\\Documents\\test"
            }
        }
    }
}
```
#### <a name="deploy-template"></a>Bereitstellen der Vorlage

Stellen Sie die Vorlage `addCSextensiontoVM.json` bereit. Diese Vorlage stellt die Erweiterung für eine vorhandene VM bereit. Führen Sie den folgenden Befehl aus:

```powershell
$templateFile = "<Path to addCSExtensiontoVM.json file>"
$templateParameterFile = "<Path to addCSExtWindowsVM.parameters.json file>"
$RGName = "<Resource group name>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Deployment name>"
```
> [!NOTE]
> Die Bereitstellung der Erweiterung ist ein zeitintensiver Auftrag, der etwa 10 Minuten dauert.

Hier ist eine Beispielausgabe:

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addCSExtensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addCSExtWindowsVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment7"

DeploymentName          : deployment7
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/17/2020 10:07:44 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM5
                          extensionName    String                     CustomScriptExtension
                          publisher        String                     Microsoft.Compute
                          type             String                     CustomScriptExtension
                          typeHandlerVersion  String                     1.10
                          settings         Object                     {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
                          }

Outputs                 :
DeploymentDebugLogLevel :

PS C:\WINDOWS\system32>
```
#### <a name="track-deployment"></a>Nachverfolgen der Bereitstellung

Führen Sie den folgenden Befehl aus, um den Bereitstellungsstatus von Erweiterungen für eine bestimmte VM anzuzeigen: 

```powershell
Get-AzureRmVMExtension -ResourceGroupName <Name of resource group> -VMName <Name of VM> -Name <Name of the extension>
```
Hier ist eine Beispielausgabe:

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM5 -Name CustomScriptExtension

ResourceGroupName       : myasegpuvm1
VMName                  : VM5
Name                    : CustomScriptExtension
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.Compute
ExtensionType           : CustomScriptExtension
TypeHandlerVersion      : 1.10
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM5/extensions/CustomScriptExtension
PublicSettings          : {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
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

Die Ausgabe der Erweiterung wird in Dateien im folgenden Ordner auf dem virtuellen Zielcomputer protokolliert. 

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Die angegebenen Dateien werden in den folgenden Ordner auf dem virtuellen Zielcomputer heruntergeladen.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
Hierbei ist *n* eine dezimale Ganzzahl, die sich zwischen verschiedenen Ausführungen der Erweiterung ändern kann. Der Wert „1.*“ entspricht dem tatsächlichen aktuellen `typeHandlerVersion`-Wert der Erweiterung. Das tatsächliche Verzeichnis lautete in diesem Fall z. B. `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.9\Downloads\0`. 


Der Befehl für die Ausführung der benutzerdefinierten Erweiterung lautete `md C:\\Users\\Public\\Documents\\test`. Wenn die Erweiterung erfolgreich installiert wurde, können Sie überprüfen, ob das Verzeichnis auf der VM unter dem im Befehl angegebenen Pfad erstellt wurde. 


### <a name="custom-script-extension-for-linux"></a>Benutzerdefinierte Script-Erweiterung für Linux

Zum Bereitstellen der benutzerdefinierten Skripterweiterung für Linux für eine VM, die auf Ihrem Gerät ausgeführt wird, bearbeiten Sie die Parameterdatei `addCSExtLinuxVM.parameters.json` und stellen dann die Vorlage `addCSExtensiontoVM.json` bereit.

#### <a name="edit-parameters-file"></a>Bearbeiten der Parameterdatei

Die Datei `addCSExtLinuxVM.parameters.json` kann die folgenden Parameter enthalten:

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<Name of your VM>" 
        },
        "extensionName": {
            "value": "<Name of your extension>" 
        },
        "publisher": {
            "value": "Microsoft.Azure.Extensions" 
        },
        "type": {
            "value": "CustomScript" 
        },
        "typeHandlerVersion": {
            "value": "2.0" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "<Command to execute>"
            }
        }
    }
}
```
Geben Sie den Namen Ihrer VM, den Namen für die Erweiterung und den auszuführenden Befehl an.

Im Folgenden finden Sie die Beispielparameterdatei, die in diesem Artikel verwendet wurde:

```powershell
$templateFile = "<Path to addCSExtensionToVM.json file>"
$templateParameterFile = "<Path to addCSExtLinuxVM.parameters.json file>"
$RGName = "<Resource group name>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Deployment name>"
``` 

> [!NOTE]
> Die Bereitstellung der Erweiterung ist ein zeitintensiver Auftrag, der etwa 10 Minuten dauert.

Hier ist eine Beispielausgabe:

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addCSExtensionToVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addCSExtLinuxVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment99"

DeploymentName          : deployment99
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/18/2020 1:55:23 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM6
                          extensionName    String                     LinuxCustomScriptExtension
                          publisher        String                     Microsoft.Azure.Extensions
                          type             String                     CustomScript
                          typeHandlerVersion  String                     2.0
                          settings         Object                     {
                            "commandToExecute": "sudo echo 'some text' >> /home/Administrator/file2.txt"
                          }

Outputs                 :
DeploymentDebugLogLevel :

PS C:\WINDOWS\system32>
```

Der `commandToExecute` wurde so festgelegt, dass die Datei `file2.txt` im Verzeichnis `/home/Administrator` mit dem Inhalt `some text` erstellt wird. In diesem Fall können Sie überprüfen, ob die Datei unter dem angegebenen Pfad erstellt wurde.

```powershell
Administrator@VM6:~$ dir
file2.txt
Administrator@VM6:~$ cat file2.txt
some text
Administrator@VM6:
```

#### <a name="track-deployment-status"></a>Nachverfolgen des Bereitstellungsstatus    
    
Die Vorlagenbereitstellung ist ein zeitintensiver Auftrag. Um den Bereitstellungsstatus von Erweiterungen für eine bestimmte VM zu überprüfen, öffnen Sie eine weitere PowerShell-Sitzung (als Administrator). Führen Sie den folgenden Befehl aus: 

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName <VM Name> -Name <Extension Name>
```
Hier ist eine Beispielausgabe: 

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM5 -Name CustomScriptExtension

ResourceGroupName       : myasegpuvm1
VMName                  : VM5
Name                    : CustomScriptExtension
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.Compute
ExtensionType           : CustomScriptExtension
TypeHandlerVersion      : 1.10
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM5/extensions/CustomScriptExtension
PublicSettings          : {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
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

Die Ausgabe der Erweiterungsausführung wird in der folgenden Datei protokolliert: `/var/lib/waagent/custom-script/download/0/`.


## <a name="remove-custom-script-extension"></a>Entfernen der benutzerdefinierten Skripterweiterung

Verwenden Sie zum Entfernen der benutzerdefinierten Skripterweiterung den folgenden Befehl:

`Remove-AzureRmVMExtension -ResourceGroupName <Resource group name> -VMName <VM name> -Name <Extension name>`

Hier ist eine Beispielausgabe:

```powershell
PS C:\WINDOWS\system32> Remove-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM6 -Name LinuxCustomScriptExtension
Virtual machine extension removal operation
This cmdlet will remove the specified virtual machine extension. Do you want to continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Yes
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```


## <a name="next-steps"></a>Nächste Schritte

[Azure Resource Manager-Cmdlets](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)
