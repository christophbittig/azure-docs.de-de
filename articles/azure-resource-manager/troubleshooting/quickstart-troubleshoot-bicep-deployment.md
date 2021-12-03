---
title: Fehlerbehebung bei der Bereitstellung von Bicep-Dateien
description: Erfahren Sie, wie Sie die Bereitstellung von Bicep-Dateien überwachen und Fehler beheben können. Sie finden hier Aktivitätsprotokolle und den Bereitstellungsverlauf.
ms.date: 11/04/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 31d212ef608b5fbabb4430b5320ae033ae2be325
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131849442"
---
# <a name="quickstart-troubleshoot-bicep-file-deployments"></a>Schnellstart: Fehlerbehebung bei der Bereitstellung von Bicep-Dateien

Dieser Schnellstart beschreibt, wie Sie Fehler bei der Bereitstellung von Bicep-Dateien beheben können. Sie erstellen eine Datei mit Fehlern und lernen, wie Sie die Fehler beheben können.

Es gibt drei Arten von Fehlern, die mit einer Bereitstellung zusammenhängen:

- **Validierungsfehler** treten auf, bevor ein Einsatz beginnt, und werden durch Syntaxfehler in Ihrer Datei verursacht. Ihr Editor kann diese Fehler identifizieren.
- **Preflight-Validierungsfehler** treten auf, wenn ein Bereitstellungsbefehl zwar ausgeführt wird, Ressourcen aber nicht bereitgestellt werden. Diese Fehler werden gefunden, ohne dass die Bereitstellung gestartet wird. Wenn zum Beispiel ein Parameterwert falsch ist, wird der Fehler bei der Preflight-Validierung gefunden.
- **Einrichtungsfehler** treten während des Einrichtungsprozesses auf und können nur durch die Bewertung des Einrichtungsfortschritts gefunden werden.

Alle Fehlertypen geben einen Fehlercode zurück, den Sie bei der Fehlersuche in der Bereitstellung verwenden können. Validierungs- und Preflight-Fehler werden im Aktivitätsprotokoll angezeigt, aber nicht im Bereitstellungsverlauf. Eine Bicep-Datei mit Syntaxfehlern wird weder in JSON kompiliert noch im Aktivitätsprotokoll angezeigt.

## <a name="prerequisites"></a>Voraussetzungen

Um diesen Schnellstart durchzuführen, benötigen Sie die folgenden Gegenstände:

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
- [Visual Studio Code](https://code.visualstudio.com) mit der neuesten [Bicep-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep).
- Die aktuelle Version von [Azure PowerShell](/powershell/azure/install-az-ps) oder der [Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-bicep-file-with-errors"></a>Erstellen einer Bicep-Datei mit Fehlern

Kopieren Sie die folgende Bicep-Datei und speichern Sie sie lokal. Sie verwenden diese Datei, um einen Validierungsfehler, einen Preflight-Fehler und einen Bereitstellungsfehler zu beheben. Dieser Schnellstart geht davon aus, dass Sie die Datei **troubleshoot.bicep** benannt haben, aber Sie können ihr einen beliebigen Namen geben.

```bicep
@description('SKU for the storage account')
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
parameter storageAccountType string = 'Standard_LRS'

@description('Prefix for storage name.')
param prefixName string

var storageAccountName = '${prefixName}${uniqueString(resourceGroup().id)}'

resource storageAccount 'Microsoft.Storage/storageAccounts@2021-06-01' = {
  name: storageAccountName
  location: resourceGroup().location
  sku: {
    name: storageAccountType
  }
  kind: 'StorageV2'
  properties: {}
}

resource existingVNet 'Microsoft.Network/virtualNetworks@2021-03-01' existing = {
  name: 'doesnotexist'
}

output storageAccountName string = storageAccountName
output vnetResult object = existingVNet
```

## <a name="fix-validation-error"></a>Validierungsfehler beheben

Öffnen Sie die Datei in Visual Studio Code. Sie werden feststellen, dass Visual Studio Code einen Syntaxfehler identifiziert. Die erste Parameterdeklaration ist mit roten Schnörkeln markiert, um einen Fehler anzuzeigen.

:::image type="content" source="media/quickstart-troubleshoot-bicep-deployment/show-visual-studio-code-error.png" alt-text="Screenshot von Visual Studio Code zeigt Fehler in der Syntax.":::

Die mit einem Fehler gekennzeichneten Zeilen sind:

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
parameter storageAccountType string = 'Standard_LRS'
```

Wenn Sie den Mauszeiger über `parameter` bewegen, wird eine Fehlermeldung angezeigt.

:::image type="content" source="media/quickstart-troubleshoot-bicep-deployment/declaration-not-recognized.png" alt-text="Screenshot der Fehlermeldung in Visual Studio Code.":::

Die Meldung lautet: "Dieser Deklarationstyp wird nicht erkannt. Geben Sie einen Parameter, eine Variable, eine Ressource oder eine Ausgabedeklaration an." Wenn Sie versuchen, diese Datei zu verteilen, erhalten Sie dieselbe Fehlermeldung vom Verteilungsbefehl.

Wenn Sie sich die Dokumentation für eine [Parameterdeklaration](../bicep/parameters.md) ansehen, werden Sie sehen, dass das Schlüsselwort eigentlich `param` ist. Wenn Sie diese Syntax ändern, verschwindet der Validierungsfehler. Der `@allowed`-Dekorator wurde ebenfalls als Fehler markiert, aber auch dieser Fehler wird durch Änderung der Parameterdeklaration behoben. Der Dekorator wurde als Fehler markiert, weil er eine Parameterdeklaration nach dem Dekorator erwartet. Diese Bedingung war nicht erfüllt, als die Erklärung falsch war.

Die Festnetznummer lautet:

```bicep
param storageAccountType string = 'Standard_LRS'
```

## <a name="fix-preflight-error"></a>Preflight-Fehler beheben

Nachdem Sie nun den Validierungsfehler behoben haben, können Sie die Datei bereitstellen. Wenn Sie jedoch einen falschen Parameterwert angeben, wird ein Preflight-Fehler angezeigt.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az group create --name troubleshootRG --location westus
az deployment group create \
  --resource-group troubleshootRG \
  --template-file troubleshoot.bicep \
  --parameters prefixName=longNamewith!!Charactersthatarenotallowed
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup -Name troubleshootRG -Location westus
New-AzResourceGroupDeployment `
  -ResourceGroupName troubleshootRG `
  -TemplateFile troubleshoot.bicep `
  -prefixName longNamewith!!Charactersthatarenotallowed
```

---

Azure Resource Manager stellt fest, dass der Name des Speicherkontos Zeichen enthält, die nicht zulässig sind. Sie versucht nicht, den Einsatz zu starten.

Sie erhalten eine Fehlermeldung, die besagt, dass die Preflight-Validierung fehlgeschlagen ist. Außerdem wird eine Meldung angezeigt, die besagt, dass der Name des Speicherkontos zwischen 3 und 24 Zeichen lang sein muss und nur Zahlen und Kleinbuchstaben enthalten darf. Die von Ihnen angegebene Vorwahl erfüllte diese Anforderung nicht. Weitere Informationen zu diesem Fehlercode finden Sie unter [Fehler für Speicherkontonamen beheben](error-storage-account-name.md).

Da der Fehler beim Preflight aufgetreten ist, gibt es in der Historie keine Bereitstellung.

:::image type="content" source="media/quickstart-troubleshoot-bicep-deployment/no-deployment.png" alt-text="Screenshot des Portals ohne Einsatz in der Historie. ":::

Die fehlgeschlagene Bereitstellung ist jedoch im Aktivitätsprotokoll aufgeführt.

:::image type="content" source="media/quickstart-troubleshoot-bicep-deployment/preflight-activity-log.png" alt-text="Screenshot des Aktivitätsprotokolls mit Fehler.":::

Sie können die Details des Protokolleintrags öffnen, um die Fehlermeldung zu sehen.

## <a name="fix-deployment-error"></a>Einsatzfehler beheben

Sie stellen die Datei erneut bereit und geben einen zulässigen Wert für den Parameter Namenspräfix an.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az group create --name troubleshootRG --location westus
az deployment group create \
  --resource-group troubleshootRG \
  --template-file troubleshoot.bicep \
  --parameters prefixName=stg
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup -Name troubleshootRG -Location westus
New-AzResourceGroupDeployment `
  -ResourceGroupName troubleshootRG `
  -TemplateFile troubleshoot.bicep `
  -prefixName stg
```

---

Die Bereitstellung beginnt, schlägt aber mit der Meldung fehl, dass das virtuelle Netzwerk nicht gefunden wurde. Normalerweise würden Sie diesen Fehler beheben, indem Sie den Verweis auf die Ressource ändern. In diesem Schnellstart werden Sie die Referenz löschen. Weitere Informationen zu diesem Fehlercode finden Sie unter [Resolve resource not found errors](error-not-found.md).

Beachten Sie im Portal, dass die Bereitstellung in der Historie erscheint.

:::image type="content" source="media/quickstart-troubleshoot-bicep-deployment/view-deployment-history.png" alt-text="Screenshot der Verteilungshistorie im Portal.":::

Sie können den Eintrag im Bereitstellungsprotokoll öffnen, um Einzelheiten über den Fehler zu erfahren. Der Fehler ist auch im Aktivitätsprotokoll vorhanden.

Die Bicep-Datei versucht, auf ein virtuelles Netzwerk zu verweisen, das in Ihrer Ressourcengruppe nicht vorhanden ist. Löschen Sie den Verweis auf das vorhandene virtuelle Netzwerk, um den Fehler zu beheben.

```bicep
@description('SKU for the storage account')
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
parameter storageAccountType string = 'Standard_LRS'

@description('Prefix for storage name.')
param prefixName string

var storageAccountName = '${prefixName}${uniqueString(resourceGroup().id)}'

resource storageAccount 'Microsoft.Storage/storageAccounts@2021-06-01' = {
  name: storageAccountName
  location: resourceGroup().location
  sku: {
    name: storageAccountType
  }
  kind: 'StorageV2'
  properties: {}
}

output storageAccountName string = storageAccountName
```

Sie können diese Bicep-Datei ohne Fehler bereitstellen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Azure-Ressourcen nicht mehr benötigt werden, löschen Sie die Ressourcengruppe. Sie können die Ressourcengruppe aus Cloud Shell oder dem Portal löschen.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az group delete --name troubleshootRG
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ersetzen Sie `<rgname>` einschließlich der spitzen Klammern durch den Namen Ihrer Ressourcengruppe.

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

In diesem Schnellstart haben Sie gelernt, wie Sie Fehler bei der Bereitstellung von Bicep-Dateien beheben können.

> [!div class="nextstepaction"]
> [Fehlerbehebung für häufige Azure-Bereitstellungsfehler](common-deployment-errors.md)
