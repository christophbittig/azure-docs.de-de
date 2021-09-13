---
title: Aktivieren der VM-Erweiterung mithilfe von Azure PowerShell
description: In diesem Artikel wird beschrieben, wie Sie mit Azure PowerShell VM-Erweiterungen auf Azure Arc-fähigen Servern bereitstellen, die in hybriden Cloudumgebungen ausgeführt werden.
ms.date: 08/05/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9a626e42b5447cafcf0fe99876eb0146a02c25f3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346420"
---
# <a name="enable-azure-vm-extensions-using-azure-powershell"></a>Aktivieren von Azure-VM-Erweiterungen mithilfe von Azure PowerShell

In diesem Artikel erfahren Sie, wie Sie Azure-VM-Erweiterungen, die von Azure Arc-fähigen Servern unterstützt werden, auf einem Linux- oder Windows-Hybridcomputer unter Verwendung von Azure PowerShell bereitstellen oder deinstallieren.

> [!NOTE]
> Das Bereitstellen und Verwalten von VM-Erweiterungen auf Azure-VMs wird auf Servern mit Azure Arc-Unterstützung nicht unterstützt. Informationen zu virtuellen Azure-Computern finden Sie im Artikel [Erweiterungen und Features für virtuelle Azure-Computer](../../virtual-machines/extensions/overview.md).

## <a name="prerequisites"></a>Voraussetzungen

- Einen Computer mit Azure PowerShell. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/).

Bevor Sie Azure PowerShell zum Verwalten von VM-Erweiterungen auf Ihrem Hybridserver verwenden können, der von Arc-fähigen Servern verwaltet wird, müssen Sie das Modul `Az.ConnectedMachine` installieren. Führen Sie auf Ihrem Server mit Arc-Unterstützung den folgenden Befehl aus:

`Install-Module -Name Az.ConnectedMachine`.

Nach Abschluss der Installation wird die folgende Meldung zurückgegeben:

`The installed extension `Az.ConnectedMachine` is experimental and not covered by customer support. Please use with discretion.`

## <a name="enable-extension"></a>Aktivieren der Erweiterung

Verwenden Sie [New-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/new-azconnectedmachineextension) mit den Parametern `-Name`, `-ResourceGroupName`, `-MachineName`, `-Location`, `-Publisher`, -`ExtensionType` und `-Settings`, um eine VM-Erweiterung auf dem Arc-fähigen Server zu aktivieren.

Im folgenden Beispiel wird die Log Analytics-VM-Erweiterung auf einem Linux-Server mit Arc-Unterstützung aktiviert:

```powershell
PS C:\> $Setting = @{ "workspaceId" = "workspaceId" }
PS C:\> $protectedSetting = @{ "workspaceKey" = "workspaceKey" }
PS C:\> New-AzConnectedMachineExtension -Name OMSLinuxAgent -ResourceGroupName "myResourceGroup" -MachineName "myMachine" -Location "eastus" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -Settings $Setting -ProtectedSetting $protectedSetting -ExtensionType "OmsAgentForLinux"
```

Ändern Sie im vorherigen Beispiel den Wert für den Parameter `-ExtensionType` in `"MicrosoftMonitoringAgent"`, um die Log Analytics-VM-Erweiterung auf einem Windows-Server mit Arc-Unterstützung zu aktivieren.

Im folgenden Beispiel wird die Erweiterung für benutzerdefinierte Skripts auf einem Server mit Arc-Unterstützung aktiviert:

```powershell
PS C:\> $Setting = @{ "commandToExecute" = "powershell.exe -c Get-Process" }
PS C:\> New-AzConnectedMachineExtension -Name custom -ResourceGroupName myResourceGroup -MachineName myMachineName -Location eastus -Publisher "Microsoft.Compute"  -Settings $Setting -ExtensionType CustomScriptExtension
```

### <a name="key-vault-vm-extension"></a>Azure Key Vault-VM-Erweiterung 

> [!WARNING]
> PowerShell-Clients fügen `"` häufig `\` in der Datei „settings.json“ hinzu. Dies verursacht bei akvvm_service folgenden Fehler: `[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`

Im folgenden Beispiel wird die Key Vault-VM-Erweiterung auf einem Server mit Arc-Unterstützung aktiviert:

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

Verwenden Sie [Get-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/get-azconnectedmachineextension) mit den Parametern `-MachineName` und `-ResourceGroupName`, um eine Liste der VM-Erweiterungen auf dem Arc-fähigen Server abzurufen.

Beispiel:

```powershell
Get-AzConnectedMachineExtension -ResourceGroupName myResourceGroup -MachineName myMachineName

Name    Location  PropertiesType        ProvisioningState
----    --------  --------------        -----------------
custom  westus2   CustomScriptExtension Succeeded
```

## <a name="remove-an-installed-extension"></a>Entfernen einer installierten Erweiterung

Verwenden Sie [Remove-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/remove-azconnectedmachineextension) mit den Parametern `-Name`, `-MachineName` und `-ResourceGroupName`, um eine installierte VM-Erweiterung auf dem Arc-fähigen Server zu entfernen.

Um beispielsweise die Log Analytics-VM-Erweiterung für Linux zu entfernen, führen Sie den folgenden Befehl aus:

```powershell
Remove-AzConnectedMachineExtension -MachineName myMachineName -ResourceGroupName myResourceGroup -Name OmsAgentforLinux
```

## <a name="next-steps"></a>Nächste Schritte

- Sie können VM-Erweiterungen über die [Azure-Befehlszeilenschnittstelle](manage-vm-extensions-cli.md), das [Azure-Portal](manage-vm-extensions-portal.md) oder [Azure Resource Manager-Vorlagen](manage-vm-extensions-template.md) bereitstellen, verwalten und entfernen.

- Informationen zur Problembehandlung finden Sie im [Problembehandlungs-Handbuch für VM-Erweiterungen](troubleshoot-vm-extensions.md).
