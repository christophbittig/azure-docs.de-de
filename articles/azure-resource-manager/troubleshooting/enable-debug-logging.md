---
title: Debugprotokollierung aktivieren
description: Beschreibt, wie Sie die Debugprotokollierung aktivieren, um Probleme mit Azure-Ressourcen zu beheben, die mit Azure Resource Manager Vorlagen (ARM-Vorlagen) oder Bicep-Dateien bereitgestellt wurden.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 11/02/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d68041953979b594c83059a28a78e3440ca297ac
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478893"
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

Sie können eine Eigenschaft angeben. Die `StatusMessage`-Eigenschaft gibt z. B. die gleichen Daten wie die Azure CLI `response`-Eigenschaft aus.

```azurepowershell
(Get-AzResourceGroupDeploymentOperation `
  -DeploymentName exampledeployment `
  -ResourceGroupName examplegroup).StatusMessage
```

Verwenden Sie Azure CLI, um das Debuggen `request` und die Informationen `response` zu erhalten. In den Az-Modulversionen 4.8 und höher enthält `Get-AzResourceGroupDeploymentOperation` diese Eigenschaften nicht in der Ausgabe. Eine Liste der verfügbaren Eigenschaften finden Sie unter [Ausgaben](/powershell/module/az.resources/get-azresourcegroupdeploymentoperation#outputs).

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Sie können die Debugprotokollierung nicht mit Azure CLI aktivieren, aber Sie können Debugprotokollierungsdaten abrufen.

Rufen Sie die Bereitstellungsvorgänge mit folgendem Befehl ab:

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
