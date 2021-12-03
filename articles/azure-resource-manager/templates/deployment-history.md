---
title: Bereitstellungsverlauf
description: Erfahren Sie, wie Sie Azure Resource Manager-Bereitstellungsvorgänge mit dem Portal, mit PowerShell, mit der Azure-Befehlszeilenschnittstelle (CLI) und der REST-API anzeigen.
tags: top-support-issue
ms.topic: conceptual
ms.date: 11/10/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 60bc06ab15e7f36a5c67ff5fc4de9da8bad28f88
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132310224"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Anzeigen des Bereitstellungsverlaufs mit Azure Resource Manager

Mit Azure Resource Manager können Sie den Bereitstellungsverlauf anzeigen. Sie können bestimmte Vorgänge in früheren Bereitstellungen überprüfen und sehen, welche Ressourcen bereitgestellt wurden. Dieser Verlauf enthält Informationen zu Fehlern.

Der Bereitstellungsverlauf einer Ressourcengruppe ist auf 800 Bereitstellungen beschränkt. Wenn Sie den Grenzwert fast erreicht haben, werden Bereitstellungen automatisch aus dem Verlauf gelöscht. Weitere Informationen finden Sie unter [Automatische Löschungen aus dem Bereitstellungsverlauf](deployment-history-deletions.md).

Hilfe zum Beheben bestimmter Bereitstellungsfehler finden Sie unter [Problembehandlung von häufigen Azure-Bereitstellungsfehlern](common-deployment-errors.md).

## <a name="get-deployments-and-correlation-id"></a>Abrufen von Bereitstellungen und der Korrelations-ID

Sie können Details zu einer Bereitstellung über das Azure-Portal, die PowerShell, die Azure CLI oder die REST-API anzeigen. Jede Bereitstellung verfügt über eine Korrelations-ID, die zum Nachverfolgen verwandter Ereignisse verwendet wird. Wenn Sie eine [Azure-Supportanfrage](../../azure-portal/supportability/how-to-create-azure-support-request.md) erstellen, werden Sie vom Support unter Umständen nach der Korrelations-ID gefragt. Der Support verwendet die Korrelations-ID, um die Vorgänge für die fehlerhafte Bereitstellung zu identifizieren.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Wählen Sie die zu untersuchende Ressourcengruppe aus.

1. Wählen Sie den Link unter **Bereitstellungen** aus.

   :::image type="content" source="media/deployment-history/select-deployment-history.png" alt-text="Screenshot: Ressourcengruppenübersicht mit einer erfolgreichen Bereitstellung.":::

1. Wählen Sie eine der Bereitstellungen aus dem Bereitstellungsverlauf aus.

   :::image type="content" source="media/deployment-history/select-details.png" alt-text="Screenshot des hervorgehobenen Links für eine Ressourcenbereitstellung.":::

1. Es wird eine Zusammenfassung der Bereitstellung angezeigt, einschließlich der Korrelations-ID.

   :::image type="content" source="media/deployment-history/show-correlation-id.png" alt-text="Screenshot des Bereitstellungsverlaufs, der die Korrelations-ID hervorhebt.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um alle Bereitstellungen für eine Ressourcengruppe aufzulisten, verwenden Sie den Befehl [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment).

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Um eine bestimmte Bereitstellung aus einer Ressourcengruppe abzurufen, fügen Sie den Parameter `DeploymentName` hinzu.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

Gehen Sie folgendermaßen vor, um die Korrelations-ID abzurufen:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment).CorrelationId
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um alle Bereitstellungen für eine Ressourcengruppe aufzulisten, verwenden Sie [az deployment group list](/cli/azure/deployment/group#az_deployment_group_list).

```azurecli-interactive
az deployment group list --resource-group ExampleGroup
```

Um eine bestimmte Bereitstellung abzurufen, verwenden Sie [az deployment group show](/cli/azure/deployment/group#az_deployment_group_show).

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment
```

Gehen Sie folgendermaßen vor, um die Korrelations-ID abzurufen:

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="http"></a>[HTTP](#tab/http)

Um die Bereitstellung für eine Ressourcengruppe aufzulisten, verwenden Sie den folgenden Vorgang. Informationen zur neuesten API-Versionsnummer, die in der Anforderung verwendet werden soll, finden Sie unter [Bereitstellungen – Liste nach Ressourcengruppe](/rest/api/resources/deployments/listbyresourcegroup).

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/?api-version={api-version}
```

Um eine bestimmte Bereitstellung zu erhalten, verwenden Sie den folgenden Vorgang. Informationen zur neuesten API-Versionsnummer, die in der Anforderung verwendet werden soll, finden Sie unter [Bereitstellungen – Abrufen](/rest/api/resources/deployments/get).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

Die Antwort enthält die Korrelations-ID.

```json
{
 ...
 "properties": {
   "mode": "Incremental",
   "provisioningState": "Failed",
   "timestamp": "2019-11-26T14:18:36.4518358Z",
   "duration": "PT26.2091817S",
   "correlationId": "11111111-1111-1111-1111-111111111111",
   ...
 }
}
```

---

## <a name="get-deployment-operations-and-error-message"></a>Vorgänge zum Abrufen von Bereitstellungen und Fehlermeldungen

Jede Bereitstellung kann mehrere Vorgänge umfassen. Um weiter Informationen zu einer Bereitstellung anzuzeigen, zeigen Sie die Bereitstellungsvorgänge an. Beim Fehlschlagen einer Bereitstellung enthalten die Bereitstellungsvorgänge eine Fehlermeldung.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Wählen Sie in der Zusammenfassung für eine Bereitstellung **Vorgangsdetails** aus.

   :::image type="content" source="media/deployment-history/get-operation-details.png" alt-text="Screenshot der fehlerhaften Bereitstellung, der den Link für Vorgangsdetails hervorhebt.":::

1. Es werden die ausführlichen Informationen zu dem Bereitstellungsschritt angezeigt. Wenn ein Fehler auftritt, enthalten die Details die Fehlermeldung.

   :::image type="content" source="media/deployment-history/see-operation-details.png" alt-text="Screenshot der Vorgangsdetails der fehlerhaften Bereitstellung.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwenden Sie zum Anzeigen der Bereitstellungsvorgänge für die Bereitstellung in einer Ressourcengruppe den Befehl [Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azresourcegroupdeploymentoperation).

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

Zum Anzeigen fehlerhafter Vorgänge filtern Sie Vorgänge mit dem Zustand **Fehler**.

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -Name ExampleDeployment | Where-Object { $_.ProvisioningState -eq "Failed" }
```

Verwenden Sie den folgenden Befehl, um die Statusmeldung für einen fehlerhaften Vorgang abzurufen:

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -Name ExampleDeployment | Where-Object { $_.ProvisioningState -eq "Failed" }).StatusMessage
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie zum Anzeigen der Bereitstellungsvorgänge für die Bereitstellung in einer Ressourcengruppe den Befehl [az deployment operation group list](/cli/azure/deployment/operation/group#az_deployment_operation_group_list). Sie müssen Azure CLI 2.6.0 oder höher verwenden.

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeployment
```

Zum Anzeigen fehlerhafter Vorgänge filtern Sie Vorgänge mit dem Zustand **Fehler**.

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeployment --query "[?properties.provisioningState=='Failed']"
```

Verwenden Sie den folgenden Befehl, um die Statusmeldung für einen fehlerhaften Vorgang abzurufen:

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeployment --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="http"></a>[HTTP](#tab/http)

Um Bereitstellungsvorgänge abzurufen, verwenden Sie den folgenden Vorgang. Informationen zur neuesten API-Versionsnummer, die in der Anforderung verwendet werden soll, finden Sie unter [Bereitstellungsvorgänge – Auflisten](/rest/api/resources/deploymentoperations/list).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```

Die Antwort enthält eine Fehlermeldung.

```json
{
  "value": [
    {
      "id": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/examplegroup/providers/Microsoft.Resources/deployments/exampledeployment/operations/1234567890ABCDEF",
      "operationId": "1234567890ABCDEF",
      "properties": {
        "provisioningOperation": "Create",
        "provisioningState": "Failed",
        "timestamp": "2019-11-26T14:18:36.3177613Z",
        "duration": "PT21.0580179S",
        "trackingId": "11111111-1111-1111-1111-111111111111",
        "serviceRequestId": "11111111-1111-1111-1111-111111111111",
        "statusCode": "BadRequest",
        "statusMessage": {
          "error": {
            "code": "InvalidAccountType",
            "message": "The AccountType Standard_LRS1 is invalid. For more information, see - https://aka.ms/storageaccountskus"
          }
        },
        "targetResource": {
          "id": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/storage",
          "resourceType": "Microsoft.Storage/storageAccounts",
          "resourceName": "storage"
        }
      }
    },
    ...
  ]
}
```

---

## <a name="next-steps"></a>Nächste Schritte

- Hilfe zum Beheben bestimmter Bereitstellungsfehler finden Sie unter [Problembehandlung von häufigen Azure-Bereitstellungsfehlern](common-deployment-errors.md).
- Weitere Informationen zur Verwaltung von Bereitstellungen im Verlauf finden Sie unter [Automatische Löschungen aus dem Bereitstellungsverlauf](deployment-history-deletions.md).
- Informationen zum Anzeigen einer Änderungsvorschau vor der Bereitstellung einer Vorlage finden Sie unter [Was-wäre-wenn-Vorgang bei der Bereitstellung von ARM-Vorlagen](deploy-what-if.md).
