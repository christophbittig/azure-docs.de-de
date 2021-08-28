---
title: Aktivieren der Diagnose mithilfe von PowerShell in Azure Cloud Services (klassisch) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mithilfe von PowerShell die Erfassung von Diagnosedaten aus einem Azure-Clouddienst mit der Azure-Diagnoseerweiterung aktivieren.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 1492462cdab3b92c170f90bce961a32c0150e1ac
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122824911"
---
# <a name="enable-diagnostics-in-azure-cloud-services-classic-using-powershell"></a>Aktivieren der Diagnose mithilfe von PowerShell in Azure Cloud Services (klassisch)

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

Mit der Azure-Diagnoseerweiterung können Sie Diagnosedaten wie Anwendungsprotokolle, Leistungsindikatoren usw. von einem Clouddienst sammeln. In diesem Artikel wird beschrieben, wie Sie die Azure-Diagnoseerweiterung für einen Clouddienst mithilfe von PowerShell aktivieren.  Informationen zu den erforderlichen Komponenten für diesen Artikel finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/).

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Aktivieren der Diagnoseerweiterung bei der Bereitstellung eines Clouddiensts
Dieser Ansatz eignet sich für Szenarien mit fortlaufender Integration, bei denen die Diagnoseerweiterung im Rahmen der Bereitstellung von Clouddiensten aktiviert werden kann. Beim Erstellen einer neuen Clouddienstbereitstellung können Sie die Diagnoseerweiterung aktivieren, indem Sie den *ExtensionConfiguration* -Parameter an das Cmdlet [New-AzureDeployment](/powershell/module/servicemanagement/azure.service/new-azuredeployment) übergeben. Der *ExtensionConfiguration* -Parameter akzeptiert ein Array von Diagnosekonfigurationen, die Sie mit dem Cmdlet [New-AzureServiceDiagnosticsExtensionConfig](/powershell/module/servicemanagement/azure.service/new-azureservicediagnosticsextensionconfig) erstellen können.

Das folgende Beispiel zeigt, wie Sie die Diagnose für einen Clouddienst mit einer Webrolle und einer Workerrolle mit jeweils unterschiedlicher Diagnosekonfiguration aktivieren können.

```powershell
$service_name = "MyService"
$service_package = "CloudService.cspkg"
$service_config = "ServiceConfiguration.Cloud.cscfg"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)
```

Falls in der Diagnosekonfigurationsdatei ein `StorageAccount`-Element mit einem Speicherkontonamen angegeben ist, verwendet das Cmdlet `New-AzureServiceDiagnosticsExtensionConfig` automatisch dieses Speicherkonto. Dazu muss das Speicherkonto allerdings dem gleichen Abonnement angehören wie der bereitgestellte Clouddienst.

Ab Azure SDK 2.6 enthalten die von der MSBuild-Veröffentlichungszielausgabe generierten Erweiterungskonfigurationsdateien den Speicherkontonamen basierend auf der Diagnosekonfigurations-Zeichenfolge, die in der Dienstkonfigurationsdatei (.cscfg) angegeben ist. Das folgende Skript veranschaulicht das Analysieren der Erweiterungskonfigurationsdateien aus der Veröffentlichungszielausgabe und Konfigurieren der Diagnoseerweiterung für jede Rolle, wenn der Clouddienst bereitgestellt wird.

```powershell
$service_name = "MyService"
$service_package = "C:\build\output\CloudService.cspkg"
$service_config = "C:\build\output\ServiceConfiguration.Cloud.cscfg"

#Find the Extensions path based on service configuration file
$extensionsSearchPath = Join-Path -Path (Split-Path -Parent $service_config) -ChildPath "Extensions"

$diagnosticsExtensions = Get-ChildItem -Path $extensionsSearchPath -Filter "PaaSDiagnostics.*.PubConfig.xml"
$diagnosticsConfigurations = @()
foreach ($extPath in $diagnosticsExtensions)
{
    #Find the RoleName based on file naming convention PaaSDiagnostics.<RoleName>.PubConfig.xml
    $roleName = ""
    $roles = $extPath -split ".",0,"simplematch"
    if ($roles -is [system.array] -and $roles.Length -gt 1)
    {
        $roleName = $roles[1]
        $x = 2
        while ($x -le $roles.Length)
            {
               if ($roles[$x] -ne "PubConfig")
                {
                    $roleName = $roleName + "." + $roles[$x]
                }
                else
                {
                    break
                }
                $x++
            }
        $fullExtPath = Join-Path -path $extensionsSearchPath -ChildPath $extPath
        $diagnosticsconfig = New-AzureServiceDiagnosticsExtensionConfig -Role $roleName -DiagnosticsConfigurationPath $fullExtPath
        $diagnosticsConfigurations += $diagnosticsconfig
    }
}
New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration $diagnosticsConfigurations
```

Visual Studio Online verwendet einen ähnlichen Ansatz für automatisierte Bereitstellungen von Clouddiensten mit der Diagnoseerweiterung. Unter [Publish-AzureCloudDeployment.ps1](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureCloudPowerShellDeploymentV1/Publish-AzureCloudDeployment.ps1) finden Sie ein vollständiges Beispiel.

Wenn in der Diagnosekonfiguration kein `StorageAccount` angegeben wurde, müssen Sie den *StorageAccountName*-Parameter an das Cmdlet übergeben. Bei Angabe des Parameters *StorageAccountName* verwendet das Cmdlet immer das im Parameter angegebene Speicherkonto und nicht das in der Diagnosekonfigurationsdatei angegebene.

Wenn das Diagnosespeicherkonto einem anderen Abonnement angehört als der Clouddienst, müssen die Parameter *StorageAccountName* und *StorageAccountKey* explizit an das Cmdlet übergeben werden. Der Parameter *StorageAccountKey* ist nicht erforderlich, wenn das Diagnosespeicherkonto dem gleichen Abonnement angehört, da das Cmdlet beim Aktivieren der Diagnoseerweiterung den Schlüsselwert automatisch abfragen und festlegen kann. Gehört das Diagnosespeicherkonto dagegen einem anderen Abonnement an, kann das Cmdlet den Schlüssel unter Umständen nicht automatisch abrufen, weshalb er in diesem Fall explizit über den Parameter *StorageAccountKey* angegeben werden muss.

```powershell
$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Aktivieren der Diagnoseerweiterung für einen vorhandenen Clouddienst
Mit dem Cmdlet [Set-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azureservicediagnosticsextension) können Sie die Diagnosekonfiguration für einen Clouddienst aktivieren oder aktualisieren, der bereits ausgeführt wird.

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

```powershell
$service_name = "MyService"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

Set-AzureServiceDiagnosticsExtension -DiagnosticsConfiguration @($webrole_diagconfig,$workerrole_diagconfig) -ServiceName $service_name
```

## <a name="get-current-diagnostics-extension-configuration"></a>Abrufen der aktuellen Konfiguration der Diagnoseerweiterung
Mit dem Cmdlet [Get-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/get-azureservicediagnosticsextension) können Sie die aktuelle Diagnosekonfiguration für einen Clouddienst abrufen.

```powershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

## <a name="remove-diagnostics-extension"></a>Entfernen der Diagnoseerweiterung
Sie können zum Deaktivieren der Diagnose für einen Clouddienst das Cmdlet [Remove-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/remove-azureservicediagnosticsextension) verwenden.

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Wenn Sie die Diagnoseerweiterung entweder mit *Set-AzureServiceDiagnosticsExtension* oder mit *New-AzureServiceDiagnosticsExtensionConfig* ohne den *Role*-Parameter aktiviert haben, können Sie die Erweiterung mit *Remove-AzureServiceDiagnosticsExtension* ohne den *Role*-Parameter entfernen. Wenn beim Aktivieren der Erweiterung der *Role* -Parameter verwendet wurde, muss er auch zum Entfernen der Erweiterung verwendet werden.

So entfernen Sie die Diagnoseerweiterung aus jeder einzelnen Rolle

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```

## <a name="next-steps"></a>Nächste Schritte
* Weitere Anleitungen zur Verwendung von Azure-Diagnose und anderen Techniken zur Problembehandlung finden Sie unter [Aktivieren der Diagnose in Azure Cloud Services und Virtual Machines](cloud-services-dotnet-diagnostics.md).
* Das [Diagnosekonfigurationsschema](../azure-monitor/agents/diagnostics-extension-schema-windows.md) erläutert die verschiedenen Optionen der XML-Konfigurationen für die Diagnoseerweiterung.
* Weitere Informationen zum Aktivieren der Diagnoseerweiterung für virtuelle Computer finden Sie unter [Erstellen eines virtuellen Windows-Computers mit Überwachung und Diagnose mithilfe von Azure-Ressourcen-Manager-Vorlagen](../virtual-machines/extensions/diagnostics-template.md)