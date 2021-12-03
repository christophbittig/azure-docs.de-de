---
title: Aktivieren der VM-Erweiterung mithilfe von Azure PowerShell
description: In diesem Artikel wird beschrieben, wie Sie mit Azure PowerShell VM-Erweiterungen auf Azure Arc-fähigen Servern bereitstellen, die in hybriden Cloudumgebungen ausgeführt werden.
ms.date: 10/21/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 03fb598989c7f308b3e481824e17d423f6d31824
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131470621"
---
# <a name="enable-azure-vm-extensions-using-azure-powershell"></a>Aktivieren von Azure-VM-Erweiterungen mithilfe von Azure PowerShell

In diesem Artikel erfahren Sie, wie Sie Azure-Erweiterungen für virtuelle Computer (VM), die von Azure-Arc-fähigen Servern unterstützt werden, auf einem Linux- oder Windows-Hybridcomputer unter Verwendung von Azure PowerShell bereitstellen, aktualisieren oder deinstallieren.

> [!NOTE]
> Das Bereitstellen und Verwalten von VM-Erweiterungen auf Azure-VMs wird auf Servern mit Azure Arc-Unterstützung nicht unterstützt. Informationen zu virtuellen Azure-Computern finden Sie im Artikel [Erweiterungen und Features für virtuelle Azure-Computer](../../virtual-machines/extensions/overview.md).

## <a name="prerequisites"></a>Voraussetzungen

- Einen Computer mit Azure PowerShell. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/).

Bevor Sie Azure PowerShell zum Verwalten von VM-Erweiterungen auf Ihrem Hybridserver verwenden können, der von Azure Arc-fähigen Servern verwaltet wird, müssen Sie das Modul `Az.ConnectedMachine` installieren. Diese Verwaltungsvorgänge können von Ihrer Arbeitsstation aus ausgeführt werden. Sie müssen sie nicht auf dem Azure Arc-fähigen Server ausführen.

Führen Sie auf Ihrem Azure Arc-fähigen Server den folgenden Befehl aus:

`Install-Module -Name Az.ConnectedMachine`.

Nach Abschluss der Installation wird die folgende Meldung zurückgegeben:

`The installed extension `Az.ConnectedMachine` is experimental and not covered by customer support. Please use with discretion.`

## <a name="enable-extension"></a>Aktivieren der Erweiterung

Verwenden Sie [New-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/new-azconnectedmachineextension) mit den Parametern `-Name`, `-ResourceGroupName`, `-MachineName`, `-Location`, `-Publisher`, -`ExtensionType` und `-Settings`, um eine VM-Erweiterung auf dem Azure Arc-fähigen Server zu aktivieren.

Im folgenden Beispiel wird die Log Analytics-VM-Erweiterung auf einem Azure Arc-fähigen Linux-Server aktiviert:

```powershell
$Setting = @{ "workspaceId" = "workspaceId" }
$protectedSetting = @{ "workspaceKey" = "workspaceKey" }
New-AzConnectedMachineExtension -Name OMSLinuxAgent -ResourceGroupName "myResourceGroup" -MachineName "myMachineName" -Location "regionName" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -Settings $Setting -ProtectedSetting $protectedSetting -ExtensionType "OmsAgentForLinux"
```

Ändern Sie im vorherigen Beispiel den Wert für den Parameter `-ExtensionType` in `"MicrosoftMonitoringAgent"`, um die Log Analytics-VM-Erweiterung auf einem Azure Arc-fähigen Windows-Server zu aktivieren.

Im folgenden Beispiel wird die Erweiterung für benutzerdefinierte Skripts auf einem Server mit Azure Arc-Unterstützung aktiviert:

```powershell
$Setting = @{ "commandToExecute" = "powershell.exe -c Get-Process" }
New-AzConnectedMachineExtension -Name "custom" -ResourceGroupName "myResourceGroup" -MachineName "myMachineName" -Location "regionName" -Publisher "Microsoft.Compute"  -Settings $Setting -ExtensionType CustomScriptExtension
```

Das folgende Beispiel aktiviert die Microsoft Antimalware-Erweiterung auf einem Azure Arc-fähigen Windows-Server:

```powershell
$Setting = @{ "AntimalwareEnabled" = $true }
New-AzConnectedMachineExtension -Name "IaaSAntimalware" -ResourceGroupName "myResourceGroup" -MachineName "myMachineName" -Location "regionName" -Publisher "Microsoft.Azure.Security" -Settings $Setting -ExtensionType "IaaSAntimalware"
```

### <a name="key-vault-vm-extension"></a>Azure Key Vault-VM-Erweiterung

> [!WARNING]
> PowerShell-Clients fügen `"` häufig `\` in der Datei „settings.json“ hinzu. Dies verursacht bei akvvm_service folgenden Fehler: `[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`

Das folgende Beispiel aktiviert die Key Vault-VM-Erweiterung auf einem Server mit Azure Arc-Unterstützung:

```powershell
# Build settings
    $settings = @{
      secretsManagementSettings = @{
       observedCertificates = @(
        "observedCert1"
       )
      certificateStoreLocation = "myMachineName" # For Linux use "/var/lib/waagent/Microsoft.Azure.KeyVault.Store/"
      certificateStore = "myCertificateStoreName"
      pollingIntervalInS = "pollingInterval"
      }
    authenticationSettings = @{
     msiEndpoint = "http://localhost:40342/metadata/identity"
     }
    }

    $resourceGroup = "resourceGroupName"
    $machineName = "myMachineName"
    $location = "regionName"

    # Start the deployment
    New-AzConnectedMachineExtension -ResourceGroupName $resourceGRoup -Location $location -MachineName $machineName -Name "KeyVaultForWindows or KeyVaultforLinux" -Publisher "Microsoft.Azure.KeyVault" -ExtensionType "KeyVaultforWindows or KeyVaultforLinux" -Setting (ConvertTo-Json $settings)
```

## <a name="list-extensions-installed"></a>Auflisten der Installierten Erweiterungen

Verwenden Sie [Get-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/get-azconnectedmachineextension) mit den Parametern `-MachineName` und `-ResourceGroupName`, um eine Liste der VM-Erweiterungen auf dem Azure Arc-fähigen Server abzurufen.

Beispiel:

```powershell
Get-AzConnectedMachineExtension -ResourceGroupName myResourceGroup -MachineName myMachineName

Name    Location  PropertiesType        ProvisioningState
----    --------  --------------        -----------------
custom  westus2   CustomScriptExtension Succeeded
```

## <a name="update-extensions"></a>Aktualisieren von Erweiterungen

Um eine installierte Erweiterung neu zu konfigurieren, können Sie das Cmdlet [Aktualisieren-Az-verbundene Computer-Erweiterung (Update-AzConnectedMachineExtension)](/powershell/module/az.connectedmachine/update-azconnectedmachineextension) mit den Parametern `-Name`, `-MachineName`, `-ResourceGroupName` und `-Settings` verwenden.

Lesen Sie den Referenzartikel für das Cmdlet, um die verschiedenen Methoden zum Bereitstellen der Änderungen zu verstehen, die Sie an der Erweiterung vornehmen möchten.

## <a name="remove-extensions"></a>Entfernen von Erweiterungen

Verwenden Sie [Remove-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/remove-azconnectedmachineextension) mit den Parametern `-Name`, `-MachineName` und `-ResourceGroupName`, um eine installierte VM-Erweiterung auf dem Azure Arc-fähigen Server zu entfernen.

Um beispielsweise die Log Analytics-VM-Erweiterung für Linux zu entfernen, führen Sie den folgenden Befehl aus:

```powershell
Remove-AzConnectedMachineExtension -MachineName myMachineName -ResourceGroupName myResourceGroup -Name OmsAgentforLinux
```

## <a name="next-steps"></a>Nächste Schritte

- Sie können VM-Erweiterungen über die [Azure-Befehlszeilenschnittstelle](manage-vm-extensions-cli.md), das [Azure-Portal](manage-vm-extensions-portal.md) oder [Azure Resource Manager-Vorlagen](manage-vm-extensions-template.md) bereitstellen, verwalten und entfernen.

- Informationen zur Problembehandlung finden Sie im [Problembehandlungs-Handbuch für VM-Erweiterungen](troubleshoot-vm-extensions.md).
