---
title: Suchen von Fehlercodes
description: Beschreibt, wie Sie Fehlercodes suchen, um Probleme mit Azure-Ressourcen zu beheben, die mit Azure Resource Manager-Vorlagen (ARM-Vorlagen) oder Bicep-Dateien bereitgestellt wurden.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 11/02/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8343dd5523c57a172dda53a8ad2d758825cccc28
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478757"
---
# <a name="find-error-codes"></a>Suchen von Fehlercodes

Wenn eine Azure-Ressourcenbereitstellung unter Verwendung von Azure Resource Manager-Vorlagen (ARM-Vorlagen) oder Bicep-Dateien fehlschlägt, wird ein Fehlercode empfangen. In diesem Artikel wird beschrieben, wie Sie Fehlercodes auffinden können, damit Sie das Problem behandeln können. Weitere Informationen zu Fehlercodes finden Sie unter [Häufige Bereitstellungsfehler](common-deployment-errors.md).

## <a name="error-types"></a>Fehlertypen

Es gibt drei Arten von Fehlern, die auftreten können:

- **Validierungsfehler** treten auf, bevor eine Bereitstellung beginnt, und werden durch Syntaxfehler verursacht. Verwenden Sie zum Identifizieren von Validierungsfehlern [Visual Studio Code](https://code.visualstudio.com) mit der neuesten [Bicep-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep) oder [Azure Resource Manager-Tools-Erweiterung](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools).
- **Preflight-Validierungsfehler** treten auf, wenn die Bereitstellung zwar beginnt, Ressourcen aber nicht bereitgestellt werden. Beispielsweise ein falscher Parameterwert oder ein ungültiger Ressourcenname.
- **Bereitstellungsfehler** treten während der Bereitstellung auf, und eine Problembehandlung ist erforderlich, um die Ursache zu ermitteln.

Validierungs- und Preflight-Fehlercodes werden im Aktivitätsprotokoll einer Ressourcengruppe und im [Aktivitätsprotokoll](../../azure-monitor/essentials/activity-log.md) des Abonnements gemeldet. Eine Ausnahme ist die Bicep-Syntaxvalidierung, die nur in der Ausgabe des Editors oder des Bereitstellungsbefehls angezeigt wird. Bereitstellungsfehlercodes werden im Bereitstellungsverlauf und Aktivitätsprotokoll einer Ressourcengruppe angezeigt.

## <a name="validation-errors"></a>Überprüfungsfehler

Vorlagen werden während des Bereitstellungsprozesses überprüft, und Fehlercodes werden angezeigt. Bevor Sie eine Bereitstellung ausführen, können Sie Validierungstests mit Azure PowerShell oder Azure CLI ausführen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Eine ARM-Vorlage kann über das Portal bereitgestellt werden. Wenn die Vorlage Syntaxfehler enthält, wird beim Versuch, die Bereitstellung auszuführen, ein Validierungsfehler angezeigt. Weitere Informationen zu Portalbereitstellungen finden Sie unter [Bereitstellen von Ressourcen mithilfe einer benutzerdefinierten Vorlage](../templates/deploy-portal.md#deploy-resources-from-custom-template).

Im folgenden Beispiel wird versucht, ein Speicherkonto bereitzustellen, und es tritt ein Validierungsfehler auf.

:::image type="content" source="media/find-error-code/validation-error.png" alt-text="Screenshot: Validierungsfehler im Azure-Portal.":::

Wählen Sie die Meldung aus, um weitere Details zu sehen. Die Vorlage weist einen Syntaxfehler mit dem Fehlercode `InvalidTemplate` auf. Die **Zusammenfassung** zeigt, dass einem Ausdruck eine schließende Klammer fehlt.

:::image type="content" source="media/find-error-code/validation-details.png" alt-text="Screenshot: Validierungsfehlermeldung, die einen Syntaxfehler zeigt.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um eine ARM-Vorlage vor der Bereitstellung zu überprüfen, führen Sie [Test-AzResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment) aus. Derselbe Fehler wird angezeigt, wenn Sie eine Bereitstellung ausführen.

```azurepowershell
Test-AzResourceGroupDeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile azuredeploy.json
```

Die Ausgabe zeigt Fehlercodes wie `InvalidTemplateDeployment` oder `AccountNameInvalid` an, mit denen Sie eine Problembehandlung der Vorlage durchführen und sie ggf. korrigieren können.

Bei einer Bicep-Datei wird in der Ausgabe für ein Syntaxvalidierungsproblem ein Parameterfehler angezeigt.

```Output
Test-AzResourceGroupDeployment: Cannot retrieve the dynamic parameters for the cmdlet.
Cannot find path '/tmp/11111111-1111-1111-1111-111111111111/main.json' because it does not exist.
```

Um weitere Informationen zur Problembehandlung zu erhalten, verwenden Sie den Bicep-Befehl [build](../bicep/bicep-cli.md). Die Ausgabe zeigt die Zeilen- und Spaltennummer jedes Fehlers in Klammern sowie die Fehlermeldung an.

```bicep
bicep build main.bicep
```

```Output
/azuredeploy.bicep(22,51) : Error BCP064: Found unexpected tokens in interpolated expression.
/azuredeploy.bicep(22,51) : Error BCP004: The string at this location is not terminated due to an
  unexpected new line character.
```

Es gibt weitere PowerShell-Cmdlets zum Überprüfen von Bereitstellungsvorlagen:

- [Test-AzDeployment](/powershell/module/az.resources/test-azdeployment) für Bereitstellungen auf Abonnementebene.
- [Test-AzManagementGroupDeployment](/powershell/module/az.resources/test-azmanagementgroupdeployment)
- [Test-AzTenantDeployment](/powershell/module/az.resources/test-aztenantdeployment)

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um eine ARM-Vorlage vor der Bereitstellung zu überprüfen, führen Sie [az deployment group validate](/cli/azure/deployment/group#az_deployment_group_validate) aus. Derselbe Fehler wird angezeigt, wenn Sie eine Bereitstellung ausführen.

```azurecli
az deployment group validate \
  --resource-group examplegroup \
  --template-file azuredeploy.json
```

Die Ausgabe zeigt Fehlercodes wie `InvalidTemplateDeployment` oder `AccountNameInvalid` an, mit denen Sie eine Problembehandlung der Vorlage durchführen und sie ggf. korrigieren können.

Bei einer Bicep-Datei zeigt die Ausgabe die Zeilen- und Spaltennummer jedes Fehlers in Klammern sowie die Fehlermeldung an.

```azurecli
az deployment group validate \
  --resource-group examplegroup \
  --template-file main.bicep
```

```Output
/azuredeploy.bicep(22,51) : Error BCP064: Found unexpected tokens in interpolated expression.
/azuredeploy.bicep(22,51) : Error BCP004: The string at this location is not terminated due to an
  unexpected new line character.
```

Es gibt weitere Azure CLI-Befehle zum Überprüfen von Bereitstellungsvorlagen:

- [az deployment sub validate](/cli/azure/deployment/sub#az_deployment_sub_validate)
- [az deployment mg validate](/cli/azure/deployment/mg#az_deployment_mg_validate)
- [az deployment tenant validate](/cli/azure/deployment/tenant#az_deployment_tenant_validate)

---

## <a name="deployment-errors"></a>Bereitstellungsfehler

Mehrere Vorgänge werden verarbeitet, um eine Azure-Ressource bereitzustellen. Bereitstellungsfehler treten auf, wenn ein Vorgang die Überprüfung besteht, aber während der Bereitstellung fehlschlägt. Sie können Meldungen zu jedem Bereitstellungsvorgang und jeder Bereitstellung für eine Ressourcengruppe anzeigen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Um Meldungen zu den Vorgängen einer Bereitstellung anzuzeigen, verwenden Sie das **Aktivitätsprotokoll** der Ressourcengruppe :

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.
1. Wechseln Sie zu **Ressourcengruppen**, und wählen Sie den Namen der Ressourcengruppe der Bereitstellung aus.
1. Wählen Sie **Aktivitätsprotokoll** aus.
1. Verwenden Sie die Filter, um das Fehlerprotokoll eines Vorgangs zu suchen.

    :::image type="content" source="./media/find-error-code/activity-log.png" alt-text="Screenshot des Aktivitätsprotokolls der Ressourcengruppe, das eine fehlgeschlagene Bereitstellung hervorhebt.":::

1. Wählen Sie das Fehlerprotokoll aus, um die Details des Vorgangs anzuzeigen.

    :::image type="content" source="./media/find-error-code/activity-log-details.png" alt-text="Screenshot der Details des Aktivitätsprotokolls, das die Fehlermeldung einer fehlgeschlagenen Bereitstellung anzeigt.":::

So zeigen Sie das Ergebnis einer Bereitstellung an

1. Wechseln Sie zur Ressourcengruppe.
1. Wählen Sie **Einstellungen** > **Bereitstellungen** aus.
1. Wählen Sie für die Bereitstellung **Fehlerdetails**  aus.

    :::image type="content" source="media/find-error-code/deployment-error-details.png" alt-text="Screenshot: Link einer Ressourcengruppe zu Fehlerdetails für eine fehlerhafte Bereitstellung.":::

1. Die Fehlermeldung und der Fehlercode `NoRegisteredProviderFound` werden angezeigt.

    :::image type="content" source="media/find-error-code/deployment-error-summary.png" alt-text="Screenshot: Meldung mit Details zu Bereitstellungsfehlern.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um die Bereitstellungsmeldungen einer Bereitstellung mit PowerShell anzuzeigen, verwenden Sie [Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azresourcegroupdeploymentoperation).

So zeigen Sie alle Vorgänge für eine Bereitstellung an

```azurepowershell
Get-AzResourceGroupDeploymentOperation `
  -DeploymentName exampledeployment `
  -ResourceGroupName examplegroup
```

So geben Sie einen bestimmten Eigenschaftstyp an

```azurepowershell
(Get-AzResourceGroupDeploymentOperation `
  -DeploymentName exampledeployment `
  -ResourceGroupName examplegroup).StatusCode
```

Verwenden Sie zum Abrufen des Ergebnisses einer Bereitstellung [Get-AzResourceGroupDeployment](/powershell/module/az.resources/get-azresourcegroupdeployment).

```azurepowershell
Get-AzResourceGroupDeployment `
  -DeploymentName exampledeployment `
  -ResourceGroupName examplegroup
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie zum Anzeigen der Vorgangsmeldungen einer Bereitstellung mit Azure CLI [az deployment operation group list](/cli/azure/deployment/operation/group#az_deployment_operation_group_list).

So zeigen Sie alle Vorgänge für eine Bereitstellung an

```azurecli
az deployment operation group list \
  --name exampledeployment \
  --resource-group examplegroup \
  --query "[*].properties"
```

So geben Sie einen bestimmten Eigenschaftstyp an

```azurecli
az deployment operation group list \
  --name exampledeployment \
  --resource-group examplegroup \
  --query "[*].properties.statusCode"
```

Verwenden Sie zum Abrufen des Ergebnisses einer Bereitstellung [az deployment group show](/cli/azure/deployment/group#az_deployment_group_show).

```azurecli
az deployment group show \
  --resource-group examplegroup \
  --name exampledeployment
```

---

## <a name="next-steps"></a>Nächste Schritte

- [Häufige Bereitstellungsfehler](common-deployment-errors.md)
- [Debugprotokollierung aktivieren](enable-debug-logging.md)
- [Erstellen einer Vorlage zur Problembehandlung](create-troubleshooting-template.md)
