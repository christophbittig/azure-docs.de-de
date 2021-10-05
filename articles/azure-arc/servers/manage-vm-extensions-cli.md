---
title: Aktivieren von VM-Erweiterungen mithilfe der Azure-Befehlszeilenschnittstelle
description: In diesem Artikel erfahren Sie, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle VM-Erweiterungen auf Servern mit Azure Arc-Unterstützung bereitstellen, die in Hybrid Cloud-Umgebungen ausgeführt werden.
ms.date: 08/05/2021
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2e9427d714681883fd5422ab0a7d17fd337c9568
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124807442"
---
# <a name="enable-azure-vm-extensions-using-the-azure-cli"></a>Aktivieren von Azure-VM-Erweiterungen mithilfe der Azure-Befehlszeilenschnittstelle

In diesem Artikel erfahren Sie, wie Sie die Azure-Befehlszeilenschnittstelle verwenden, um VM-Erweiterungen, die von Servern mit Azure Arc-Unterstützung unterstützt werden, auf einem Linux- oder Windows-Hybridcomputer bereitzustellen oder zu deinstallieren.

> [!NOTE]
> Das Bereitstellen und Verwalten von VM-Erweiterungen auf Azure-VMs wird auf Servern mit Azure Arc-Unterstützung nicht unterstützt. Informationen zu Azure-VMs finden Sie im Artikel [Erweiterungen und Features für virtuelle Azure-Computer](../../virtual-machines/extensions/overview.md).

[!INCLUDE [Azure CLI Prepare your environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="install-the-azure-cli-extension"></a>Installieren der Erweiterung für die Azure-Befehlszeilenschnittstelle

Die ConnectedMachine-Befehle sind nicht Bestandteil der Azure-Befehlszeilenschnittstelle. Damit Sie die Azure-Befehlszeilenschnittstelle verwenden können, um VM-Erweiterungen auf Ihrem Hybridserver zu verwalten, der von Servern mit Azure Arc-Unterstützung verwaltet wird, müssen Sie zunächst die ConnectedMachine-Erweiterung laden. Rufen Sie sie mit dem folgenden Befehl ab:

```azurecli
az extension add --name connectedmachine
```

## <a name="enable-extension"></a>Aktivieren der Erweiterung

Verwenden Sie [az connectedmachine extension create](/cli/azure/connectedmachine/extension#az_connectedmachine_extension_create) mit den Parametern `--machine-name`, `--extension-name`, `--location`, `--type`, `settings` und `--publisher`, um eine VM-Erweiterung auf Ihrem Server mit Azure Arc-Unterstützung zu aktivieren.

Das folgende Beispiel aktiviert die Log Analytics-VM-Erweiterung auf einem Server mit Azure Arc-Unterstützung:

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "OmsAgentForLinux or MicrosoftMonitoringAgent" --location "eastus" --settings '{\"workspaceId\":\"myWorkspaceId\"}' --protected-settings '{\"workspaceKey\":\"myWorkspaceKey\"}' --resource-group "myResourceGroup" --type-handler-version "1.13" --type "OmsAgentForLinux or MicrosoftMonitoringAgent" --publisher "Microsoft.EnterpriseCloud.Monitoring" 
```

Im folgenden Beispiel wird die Erweiterung für benutzerdefinierte Skripts auf einem Server mit Azure Arc-Unterstützung aktiviert:

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "CustomScriptExtension" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.Compute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -gt 10000 }\\\"\"}" --type-handler-version "1.10" --resource-group "myResourceGroup"
```

Das folgende Beispiel aktiviert die Key Vault-VM-Erweiterung auf einem Server mit Azure Arc-Unterstützung:

```azurecli
az connectedmachine extension create --resource-group "resourceGroupName" --machine-name "myMachineName" --location "regionName" --publisher "Microsoft.Azure.KeyVault" --type "KeyVaultForLinux or KeyVaultForWindows" --name "KeyVaultForLinux or KeyVaultForWindows" --settings '{"secretsManagementSettings": { "pollingIntervalInS": "60", "observedCertificates": ["observedCert1"] }, "authenticationSettings": { "msiEndpoint": "http://localhost:40342/metadata/identity" }}'
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
    "location": "eastus",
    "name": "DependencyAgentWindows",
    "namePropertiesInstanceViewName": "DependencyAgentWindows",
```

## <a name="remove-an-installed-extension"></a>Entfernen einer installierten Erweiterung

Verwenden Sie [az connectedmachine extension delete](/cli/azure/connectedmachine/extension#az_connectedmachine_extension_delete) mit den Parametern `--extension-name`, `--machine-name` und `--resource-group`, um eine installierte VM-Erweiterung von Ihrem Server mit Azure Arc-Unterstützung zu entfernen.

Um beispielsweise die Log Analytics-VM-Erweiterung für Linux zu entfernen, führen Sie den folgenden Befehl aus:

```azurecli
az connectedmachine extension delete --machine-name "myMachineName" --name "OmsAgentForLinux" --resource-group "myResourceGroup"
```

## <a name="next-steps"></a>Nächste Schritte

- Sie können VM-Erweiterungen über [Azure PowerShell](manage-vm-extensions-powershell.md), das [Azure-Portal](manage-vm-extensions-portal.md) oder [Azure Resource Manager-Vorlagen](manage-vm-extensions-template.md) bereitstellen, verwalten und entfernen.

- Informationen zur Problembehandlung finden Sie im [Problembehandlungs-Handbuch für VM-Erweiterungen](troubleshoot-vm-extensions.md).

- Weitere Informationen zu den Befehlen finden Sie in der [Übersicht](/cli/azure/connectedmachine/extension) über die VM-Erweiterung der Azure-Befehlszeilenschnittstelle.
