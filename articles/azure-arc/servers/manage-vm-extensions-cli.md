---
title: Aktivieren von VM-Erweiterungen mithilfe der Azure-Befehlszeilenschnittstelle
description: In diesem Artikel erfahren Sie, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle VM-Erweiterungen auf Servern mit Azure Arc-Unterstützung bereitstellen, die in Hybrid Cloud-Umgebungen ausgeführt werden.
ms.date: 10/28/2021
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: b7294285cd6e4fa7c2bbfac859c3fec8dfa3a474
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440410"
---
# <a name="enable-azure-vm-extensions-using-the-azure-cli"></a>Aktivieren von Azure-VM-Erweiterungen mithilfe der Azure-Befehlszeilenschnittstelle

In diesem Artikel erfahren Sie, wie Sie die Azure-Befehlszeilenschnittstelle verwenden, um VM-Erweiterungen, die von Servern mit Azure Arc-Unterstützung unterstützt werden, auf einem Linux- oder Windows-Hybridcomputer bereitzustellen, zu upgraden, zu aktualisieren oder zu deinstallieren.

> [!NOTE]
> Das Bereitstellen und Verwalten von VM-Erweiterungen auf Azure-VMs wird auf Servern mit Azure Arc-Unterstützung nicht unterstützt. Informationen zu Azure-VMs finden Sie im Artikel [Erweiterungen und Features für virtuelle Azure-Computer](../../virtual-machines/extensions/overview.md).

[!INCLUDE [Azure CLI Prepare your environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="install-the-azure-cli-extension"></a>Installieren der Erweiterung für die Azure-Befehlszeilenschnittstelle

Die ConnectedMachine-Befehle sind nicht Bestandteil der Azure-Befehlszeilenschnittstelle. Damit Sie die Azure-Befehlszeilenschnittstelle verwenden können, um eine Verbindung mit Azure herzustellen und VM-Erweiterungen auf Ihrem Hybridserver zu verwalten, der von Servern mit Azure Arc-Unterstützung verwaltet wird, müssen Sie zunächst die ConnectedMachine-Erweiterung laden. Diese Verwaltungsvorgänge können von Ihrer Arbeitsstation aus ausgeführt werden. Sie müssen sie nicht auf dem Azure Arc-fähigen Server ausführen.

Rufen Sie sie mit dem folgenden Befehl ab:

```azurecli
az extension add --name connectedmachine
```

## <a name="enable-extension"></a>Aktivieren der Erweiterung

Verwenden Sie [az connectedmachine extension create](/cli/azure/connectedmachine/extension#az_connectedmachine_extension_create) mit den Parametern `--machine-name`, `--extension-name`, `--location`, `--type`, `settings` und `--publisher`, um eine VM-Erweiterung auf Ihrem Server mit Azure Arc-Unterstützung zu aktivieren.

Das folgende Beispiel aktiviert die Log Analytics-VM-Erweiterung auf einem Server mit Azure Arc-Unterstützung:

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "OmsAgentForLinux or MicrosoftMonitoringAgent" --location "regionName" --settings '{\"workspaceId\":\"myWorkspaceId\"}' --protected-settings '{\"workspaceKey\":\"myWorkspaceKey\"}' --resource-group "myResourceGroup" --type-handler-version "1.13" --type "OmsAgentForLinux or MicrosoftMonitoringAgent" --publisher "Microsoft.EnterpriseCloud.Monitoring" 
```

Im folgenden Beispiel wird die Erweiterung für benutzerdefinierte Skripts auf einem Server mit Azure Arc-Unterstützung aktiviert:

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "CustomScriptExtension" --location "regionName" --type "CustomScriptExtension" --publisher "Microsoft.Compute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -gt 10000 }\\\"\"}" --type-handler-version "1.10" --resource-group "myResourceGroup"
```

Das folgende Beispiel aktiviert die Key Vault-VM-Erweiterung auf einem Server mit Azure Arc-Unterstützung:

```azurecli
az connectedmachine extension create --resource-group "resourceGroupName" --machine-name "myMachineName" --location "regionName" --publisher "Microsoft.Azure.KeyVault" --type "KeyVaultForLinux or KeyVaultForWindows" --name "KeyVaultForLinux or KeyVaultForWindows" --settings '{"secretsManagementSettings": { "pollingIntervalInS": "60", "observedCertificates": ["observedCert1"] }, "authenticationSettings": { "msiEndpoint": "http://localhost:40342/metadata/identity" }}'
```

Das folgende Beispiel aktiviert die Microsoft Antimalware-Erweiterung auf Windows Server mit Azure Arc-Unterstützung:

```azurecli
az connectedmachine extension create --resource-group "resourceGroupName" --machine-name "myMachineName" --location "regionName" --publisher "Microsoft.Azure.Security" --type "IaaSAntimalware" --name "IaaSAntimalware" --settings '{"AntimalwareEnabled": true}'
```

## <a name="list-extensions-installed"></a>Auflisten der Installierten Erweiterungen

Verwenden Sie [az connectedmachine extension list](/cli/azure/connectedmachine/extension#az_connectedmachine_extension_list) mit den Parametern `--machine-name` und `--resource-group`, um eine Liste der VM-Erweiterungen auf dem Server mit Azure Arc-Unterstützung abzurufen.

Beispiel:

```azurecli
az connectedmachine extension list --machine-name "myMachineName" --resource-group "myResourceGroup"
```

Standardmäßig erfolgt die Ausgabe von Azure CLI-Befehlen in JSON (JavaScript Object Notation). Um beispielsweise die Standardausgabe in eine Liste oder Tabelle zu ändern, verwenden Sie [az config set core.output=table](/cli/azure/reference-index). Ferner können Sie `--output` an beliebige Befehle anfügen, um eine einmalige Änderung des Ausgabeformats zu erreichen.

Das folgende Beispiel zeigt die partielle JSON-Ausgabe des Befehls `az connectedmachine extension -list`:

```json
[
  {
    "autoUpgradingMinorVersion": "false",
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.HybridCompute/machines/SVR01/extensions/DependencyAgentWindows",
    "location": "regionName",
    "name": "DependencyAgentWindows",
    "namePropertiesInstanceViewName": "DependencyAgentWindows",
```

## <a name="update-extension-configuration"></a>Aktuelen der Erweiterungskonfiguration

Einige VM-Erweiterungen erfordern Konfigurationseinstellungen, um sie auf dem Arc-fähigen Server zu installieren, z. B. die benutzerdefinierte Skripterweiterung und die Log Analytics-Agent-VM-Erweiterung. Verwenden Sie [az connectedmachine extension update](/cli/azure/connectedmachine/extension#az_connectedmachine_extension_update), um die Konfiguration einer Erweiterung zu aktualisieren.

Das folgende Beispiel zeigt, wie die benutzerdefinierte Skripterweiterung konfiguriert wird:

```azurecli
az connectedmachine extension update --name "CustomScriptExtension" --type "CustomScriptExtension" --publisher "Microsoft.HybridCompute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -lt 100 }\\\"\"}" --type-handler-version "1.10" --machine-name "myMachine" --resource-group "myResourceGroup"
```

## <a name="upgrade-extensions"></a>Upgraden von Erweiterungen

Wenn eine neue Version einer unterstützten VM-Erweiterung veröffentlicht wird, können Sie diese auf diese neueste Version upgraden. Verwenden Sie [az connectedmachine upgrade-extension](/cli/azure/connectedmachine) mit den Parametern `--machine-name`, `--resource-group` und `--extension-targets`, um eine VM-Erweiterung zu upgraden.

Für den `--extension-targets`-Parameter müssen Sie die Erweiterung und die neueste verfügbare Version angeben. Sie können diese Informationen auf der Seite **Erweiterungen** für den ausgewählten Arc-fähigen Server im Azure-Portal abrufen oder [az vm extension image list](/cli/azure/vm/extension/image#az_vm_extension_image_list) ausführen, um zu ermitteln, welche Version aktuell verfügbar ist.

Führen Sie den folgenden Befehl aus, um die Log Analytics-Agent-Erweiterung für Windows mit einer neueren Version zu upgraden:

```azurecli
az connectedmachine upgrade-extension --machine-name "myMachineName" --resource-group "myResourceGroup --extension-targets  --extension-targets "{\"MicrosoftMonitoringAgent\":{\"targetVersion\":\"1.0.18053.0\"}}"" 
```

Sie können die Version der installierten VM-Erweiterungen jederzeit überprüfen, indem Sie den Befehl [az connectedmachine extension list](/cli/azure/connectedmachine/extension#az_connectedmachine_extension_list) ausführen. Der `typeHandlerVersion`-Eigenschaftswert stellt die Version der Erweiterung dar.

## <a name="remove-extensions"></a>Entfernen von Erweiterungen

Verwenden Sie [az connectedmachine extension delete](/cli/azure/connectedmachine/extension#az_connectedmachine_extension_delete) mit den Parametern `--extension-name`, `--machine-name` und `--resource-group`, um eine installierte VM-Erweiterung von Ihrem Server mit Azure Arc-Unterstützung zu entfernen.

Um beispielsweise die Log Analytics-VM-Erweiterung für Linux zu entfernen, führen Sie den folgenden Befehl aus:

```azurecli
az connectedmachine extension delete --machine-name "myMachineName" --name "OmsAgentForLinux" --resource-group "myResourceGroup"
```

## <a name="next-steps"></a>Nächste Schritte

- Sie können VM-Erweiterungen über [Azure PowerShell](manage-vm-extensions-powershell.md), das [Azure-Portal](manage-vm-extensions-portal.md) oder [Azure Resource Manager-Vorlagen](manage-vm-extensions-template.md) bereitstellen, verwalten und entfernen.

- Informationen zur Problembehandlung finden Sie im [Problembehandlungs-Handbuch für VM-Erweiterungen](troubleshoot-vm-extensions.md).

- Weitere Informationen zu den Befehlen finden Sie in der [Übersicht](/cli/azure/connectedmachine/extension) über die VM-Erweiterung der Azure-Befehlszeilenschnittstelle.
