---
title: Problembehandlung bei Bereitstellungen von ARM-Vorlagen
description: Hier erfahren Sie, wie Sie Probleme bei der Bereitstellung von ARM-Vorlagen (Azure Resource Manager) behandeln.
ms.date: 11/08/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ce9bf9ee097a41b3e3ecbc0cd515dd58cf30ea82
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132062049"
---
# <a name="quickstart-troubleshoot-arm-template-deployments"></a>Schnellstart: Fehlerbehebung bei der Bereitstellung von ARM-Vorlagen

Dieser Schnellstart beschreibt, wie Sie Fehler bei der Bereitstellung von Azure Resource Manager-Vorlagen (ARM-Vorlagen) beheben können. Sie richten eine Vorlage mit Fehlern ein und erfahren, wie Sie die Fehler beheben.

Es gibt drei Arten von Fehlern, die mit einer Bereitstellung zusammenhängen:

- **Validierungsfehler** treten auf, bevor ein Einsatz beginnt, und werden durch Syntaxfehler in Ihrer Datei verursacht. Ihr Editor kann diese Fehler identifizieren.
- **Preflight-Validierungsfehler** treten auf, wenn ein Bereitstellungsbefehl zwar ausgeführt wird, Ressourcen aber nicht bereitgestellt werden. Diese Fehler werden gefunden, ohne dass die Bereitstellung gestartet wird. Wenn zum Beispiel ein Parameterwert falsch ist, wird der Fehler bei der Preflight-Validierung gefunden.
- **Einrichtungsfehler** treten während des Einrichtungsprozesses auf und können nur durch die Bewertung des Einrichtungsfortschritts gefunden werden.

Alle Fehlertypen geben einen Fehlercode zurück, den Sie bei der Fehlersuche in der Bereitstellung verwenden können. Validierungs- und Preflight-Fehler werden im Aktivitätsprotokoll angezeigt, aber nicht im Bereitstellungsverlauf.

## <a name="prerequisites"></a>Voraussetzungen

Um diesen Schnellstart durchzuführen, benötigen Sie die folgenden Gegenstände:

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
- [Visual Studio Code](https://code.visualstudio.com/) mit der neuesten [Azure Resource Manager Tools-Erweiterung](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools).
- Installieren Sie die aktuelle Version von [Azure PowerShell](/powershell/azure/install-az-ps) oder der [Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-template-with-errors"></a>Eine Vorlage mit Fehlern erstellen

Kopieren Sie die folgende Vorlage, und speichern Sie sie lokal. Sie verwenden diese Datei, um einen Validierungsfehler, einen Preflight-Fehler und einen Bereitstellungsfehler zu beheben. In dieser Schnellstartanleitung wird davon ausgegangen, dass Sie die Datei _troubleshoot.json_ genannt haben. Sie können aber auch einen anderen Namen verwenden.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameterss": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ]
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "prefixName": {
      "type": "string"
    }
  },
  "variables": {
    "storageAccountName": "[concat(parameters('prefixName'), uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-04-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    },
    "vnetResult": {
      "type": "object",
      "value": "[reference(resourceId('Microsoft.Network/virtualNetworks', 'doesnotexist'), '2021-03-01', 'Full')]"
    }
  }
}
```

## <a name="fix-validation-error"></a>Validierungsfehler beheben

Öffnen Sie die Datei in Visual Studio Code. Die Wellenlinie unter `parameterss:` weist auf einen Fehler hin. Zeigen Sie auf den Fehler, um den Validierungsfehler anzuzeigen.

:::image type="content" source="media/quickstart-troubleshoot-arm-deployment/validation-error.png" alt-text="Screenshot: Fehler bei der Überprüfung einer Vorlage in Visual Studio Code":::

Wie Sie sehen, liegen für `variables` und `resources` Fehler aufgrund eines nicht definierten Parameterverweises (_undefined parameter reference_) vor. Wählen Sie zum Anzeigen der Validierungsfehler der Vorlage **Ansicht** > **Probleme** aus.

:::image type="content" source="media/quickstart-troubleshoot-arm-deployment/validation-undefined-parameter.png" alt-text="Screenshot: Visual Studio Code mit Fehlern aufgrund eines nicht definierten Parameterverweises":::

Alle Fehler werden durch einen falsch geschriebenen Elementnamen verursacht.

```json
"parameterss": {
```

Die Fehlermeldung lautet: _Fehler bei der Vorlagenüberprüfung: Member "parameterss" für Objekt vom Typ "Template" nicht gefunden. Pfad "parameterss", Zeile 4, Position 16_.

Die ARM-Vorlagensyntax für [parameters](../templates/syntax.md#parameters) zeigt, dass `parameters` der korrekte Elementname ist.

Korrigieren Sie die Schreibweise, und speichern Sie die Datei, um den Validierungsfehler sowie die Fehler aufgrund eines nicht definierten Parameterverweises (_undefined parameter reference_) zu beheben.

```json
"parameters": {
```

## <a name="fix-preflight-error"></a>Preflight-Fehler beheben

Verwenden Sie einen falschen Wert für den Parameter `prefixName`, um einen Fehler bei der Preflightüberprüfung zu verursachen.

In dieser Schnellstartanleitung wird _troubleshootRG_ als Ressourcengruppenname verwendet, Sie können aber auch einen anderen Namen verwenden.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az group create --name troubleshootRG --location westus
az deployment group create \
  --resource-group troubleshootRG \
  --template-file troubleshoot.json \
  --parameters prefixName=long!!StoragePrefix
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup -Name troubleshootRG -Location westus
New-AzResourceGroupDeployment `
  -ResourceGroupName troubleshootRG `
  -TemplateFile troubleshoot.json `
  -prefixName long!!StoragePrefix
```

---

Die Preflightüberprüfung für die Vorlage ist nicht erfolgreich, und die Bereitstellung wird nicht ausgeführt. `prefixName` ist mehr als elf Zeichen lang und enthält Sonderzeichen und Großbuchstaben.

Speichernamen müssen zwischen drei und 24 Zeichen lang sein und dürfen nur Kleinbuchstaben und Ziffern enthalten. Durch den Präfixwert ist ein ungültiger Speichername entstanden. Weitere Informationen finden Sie unter [Beheben von Fehlern bei Speicherkontonamen](error-storage-account-name.md). Um den Preflightfehler zu beheben, verwenden Sie ein Präfix mit maximal elf Zeichen, das nur Kleinbuchstaben oder Zahlen enthält.

Da die Bereitstellung nicht ausgeführt wurde, gibt es auch keinen Bereitstellungsverlauf.

:::image type="content" source="media/quickstart-troubleshoot-arm-deployment/preflight-no-deploy.png" alt-text="Screenshot: Ressourcengruppenübersicht ohne Bereitstellung für den Preflightfehler":::

Das Aktivitätsprotokoll enthält den Preflightfehler. Wählen Sie das Protokoll aus, um die Details des Fehlers anzuzeigen.

:::image type="content" source="media/quickstart-troubleshoot-arm-deployment/preflight-activity-log.png" alt-text="Screenshot: Aktivitätsprotokoll der Ressourcengruppe mit Preflightfehler":::

## <a name="fix-deployment-error"></a>Einsatzfehler beheben

Führen Sie die Bereitstellung mit einem gültigen Präfixwert (beispielsweise `storage`) aus.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az group create --name troubleshootRG --location westus
az deployment group create \
  --resource-group troubleshootRG \
  --template-file troubleshoot.json \
  --parameters prefixName=storage
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup -Name troubleshootRG -Location westus
New-AzResourceGroupDeployment `
  -ResourceGroupName troubleshootRG `
  -TemplateFile troubleshoot.json `
  -prefixName storage
```

---

Die Bereitstellung beginnt und wird im Bereitstellungsverlauf angezeigt. Die Bereitstellung ist nicht erfolgreich, da von `outputs` auf ein virtuelles Netzwerk verwiesen wird, das in der Ressourcengruppe nicht vorhanden ist. Da jedoch keine Fehler für das Speicherkonto aufgetreten sind, wurde die Ressource bereitgestellt. Der Bereitstellungsverlauf enthält eine nicht erfolgreiche Bereitstellung.

:::image type="content" source="media/quickstart-troubleshoot-arm-deployment/deployment-failed.png" alt-text="Screenshot: Ressourcengruppenübersicht mit einer nicht erfolgreichen Bereitstellung":::

Um den Bereitstellungsfehler zu beheben, ändern Sie die Verweisfunktion so, dass eine gültige Ressource verwendet wird. Weitere Informationen finden Sie unter [Beheben von Fehlern des Typs „Ressource nicht gefunden“](error-not-found.md). Löschen Sie für diese Schnellstartanleitung das Komma vor `vnetResult` und den gesamten Code für `vnetResult`. Speichern Sie anschließend die Datei, und führen Sie die Bereitstellung erneut aus.

```json
"vnetResult": {
  "type": "object",
  "value": "[reference(resourceId('Microsoft.Network/virtualNetworks', 'doesnotexist'), '2021-03-01', 'Full')]"
}
```

Nachdem die Überprüfungs-, Preflight- und Bereitstellungsfehler behoben wurden, stellt die folgende Vorlage ein Speicherkonto bereit. Bereitstellungsverlauf und Aktivitätsprotokoll enthalten jeweils eine erfolgreiche Bereitstellung.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ]
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "prefixName": {
      "type": "string"
    }
  },
  "variables": {
    "storageAccountName": "[concat(parameters('prefixName'), uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-04-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    }
  }
}
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Azure-Ressourcen nicht mehr benötigt werden, löschen Sie die Ressourcengruppe.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az group delete --name troubleshootRG
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name troubleshootRG
```

---

Um die Ressourcengruppe aus dem Portal zu löschen, gehen Sie wie folgt vor:

1. Geben Sie im Azure-Portal im Suchfeld **Ressourcengruppen** ein.
1. Geben Sie den Namen der Ressourcengruppe in das Feld **Nach Name filtern** ein.
1. Klicken Sie auf den Namen der Ressourcengruppe.
1. Wählen Sie die Option **Ressourcengruppe löschen**.
1. Um den Löschvorgang zu bestätigen, geben Sie den Namen der Ressourcengruppe ein, und wählen Sie **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie gelernt, wie Sie Fehler bei der Bereitstellung von ARM-Vorlagen beheben können.

> [!div class="nextstepaction"]
> [Fehlerbehebung für häufige Azure-Bereitstellungsfehler](common-deployment-errors.md)
