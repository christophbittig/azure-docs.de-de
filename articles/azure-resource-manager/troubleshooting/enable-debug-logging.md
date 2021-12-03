---
title: Debugprotokollierung aktivieren
description: Beschreibt, wie Sie die Debugprotokollierung aktivieren, um Probleme mit Azure-Ressourcen zu beheben, die mit Azure Resource Manager Vorlagen (ARM-Vorlagen) oder Bicep-Dateien bereitgestellt wurden.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 11/05/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fcd2cbdf052f934bb797b3f1dab148d951d09167
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131989647"
---
# <a name="enable-debug-logging"></a>Debugprotokollierung aktivieren

Um einen Bereitstellungsfehler zu beheben, ist es hilfreich, weitere Informationen zu sammeln. Verwenden Sie Azure PowerShell, um die Debugprotokollierung zu aktivieren. Sie können Daten zur Anforderung und Antwort einer Bereitstellung erhalten, um die Ursache des Problems zu erfahren. Die Debugprotokollierung funktioniert mit Azure Resource Manager-Vorlagen (ARM-Vorlagen) und Bicep-Dateien.

## <a name="get-debug-information"></a>Abrufen von Debuginformationen

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwenden Sie in PowerShell [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment), um den `DeploymentDebugLogLevel`-Parameter auf `All`, `ResponseContent` oder `RequestContent` festzulegen. Wenn Debugprotokollierung aktiviert ist, wird eine Warnung angezeigt, dass Geheimnisse wie Kennwörter oder `listKeys` mit Befehlen wie [Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azresourcegroupdeploymentoperation) protokolliert werden können.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile azuredeploy.json `
  -DeploymentDebugLogLevel All
```

Die Ausgabe zeigt die Debugprotokollierung:

```Output
DeploymentDebugLogLevel : RequestContent, ResponseContent
```

So zeigen Sie alle Eigenschaften für Bereitstellungsvorgänge an:

```azurepowershell
Get-AzResourceGroupDeploymentOperation `
  -DeploymentName exampledeployment `
  -ResourceGroupName examplegroup
```

Sie können eine Eigenschaft wie `StatusMessage` oder `StatusCode` angeben, um die Ausgabe zu filtern.

```azurepowershell
(Get-AzResourceGroupDeploymentOperation `
  -DeploymentName exampledeployment `
  -ResourceGroupName examplegroup).StatusMessage
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Sie können die Debugprotokollierung nicht mit Azure CLI aktivieren, aber Sie können Debugprotokollierungsdaten abrufen.

Rufen Sie die Bereitstellungsvorgänge mit dem Befehl [az deployment operation group list](/cli/azure/deployment/operation/group#az_deployment_operation_group_list) ab:

```azurecli
az deployment operation group list \
  --resource-group examplegroup \
  --name exampledeployment
```

Rufen Sie den Anforderungsinhalt mit folgendem Befehl ab:

```azurecli
az deployment operation group list \
  --name exampledeployment \
  --resource-group examplegroup \
  --query [].properties.request
```

Rufen Sie den Antwortinhalt mit folgendem Befehl ab:

```azurecli
az deployment operation group list \
  --name exampledeployment \
  --resource-group examplegroup \
  --query [].properties.response
```

---

## <a name="nested-template"></a>Geschachtelte Vorlage

Verwenden Sie zum Protokollieren von Debuginformationen für eine [geschachtelte](../templates/linked-templates.md#nested-template) ARM-Vorlage das Element [Microsoft.Resources/deployments](/azure/templates/microsoft.resources/deployments) `debugSetting`.

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2020-10-01",
  "name": "nestedTemplate",
  "properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri": "{template-uri}",
      "contentVersion": "1.0.0.0"
    },
    "debugSetting": {
       "detailLevel": "requestContent, responseContent"
    }
  }
}
```

Bicep verwendet [Module](../bicep/modules.md) anstelle von `Microsoft.Resources/deployments`. Mit Modulen können Sie Ihren Code wiederverwenden, um eine Bicep-Datei aus einer anderen Bicep-Datei bereitzustellen.

## <a name="next-steps"></a>Nächste Schritte

- [Häufige Bereitstellungsfehler](common-deployment-errors.md)
- [Fehlercodes suchen](find-error-code.md)
- [Erstellen einer Vorlage zur Problembehandlung](create-troubleshooting-template.md)
