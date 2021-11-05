---
title: Problembehandlung bei Bereitstellungen von ARM-Vorlagen
description: Hier erfahren Sie, wie Azure Ressource Manager-Vorlagenbereitstellungen (ARM-Vorlagen) überwacht und Probleme damit behandelt werden. Sie finden hier Aktivitätsprotokolle und den Bereitstellungsverlauf.
ms.date: 11/01/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2b2dae6d4f90410b8ce1f8fc455aa95ede0ab0d3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131100395"
---
# <a name="quickstart-troubleshoot-arm-template-deployments"></a>Schnellstart: Fehlerbehebung bei der Bereitstellung von ARM-Vorlagen

Dieser Schnellstart beschreibt, wie Sie Fehler bei der Bereitstellung von Azure Resource Manager-Vorlagen (ARM-Vorlagen) beheben können. Sie werden eine Vorlage mit zwei Fehlern einrichten und lernen, wie Sie die Aktivitätsprotokolle und den Bereitstellungsverlauf verwenden, um die Fehler zu beheben.

Im Zusammenhang mit der Vorlagenbereitstellung gibt es zwei Fehlertypen:

- **Validierungsfehler** treten auf, bevor ein Einsatz beginnt, und werden durch Syntaxfehler in Ihrer Datei verursacht.
- **Einsatzfehler** treten während des Einsatzes auf und können durch einen falschen Wert, z. B. eine API-Version, verursacht werden.

Beide Fehlertypen geben einen Fehlercode zurück, der für die Problembehandlung für die Bereitstellung genutzt werden kann. Beide Fehlertypen werden im Aktivitätsprotokoll angezeigt. Validierungsfehler werden nicht in Ihrem Verteilungsprotokoll angezeigt, da die Verteilung nie gestartet wurde.

## <a name="prerequisites"></a>Voraussetzungen

Um diesen Schnellstart durchzuführen, benötigen Sie die folgenden Gegenstände:

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
- [Visual Studio Code](https://code.visualstudio.com/) mit der neuesten [Azure Resource Manager Tools-Erweiterung](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools).
- Überprüfen Sie, wie Sie eine lokale Vorlage mit [Azure Cloud Shell](../templates/deploy-cloud-shell.md#deploy-local-template) bereitstellen.

## <a name="create-a-template-with-errors"></a>Eine Vorlage mit Fehlern erstellen

Die folgende Vorlage enthält einen Validierungsfehler und einen Bereitstellungsfehler. Sie beheben die Fehler in der Vorlage, damit das Speicherkonto bereitgestellt werden kann.

Die beiden Fehler der Mustervorlage befinden sich in der folgenden Zeile:

```json
"apiVersion1": "2018-07-02",
```

1. Öffnen Sie Visual Studio Code und wählen Sie **Datei** > **Neue Datei**.
1. Um die Vorlage zu kopieren, wählen Sie **Kopieren** und fügen Sie sie in die neue Datei ein.
1. Wählen Sie **Datei** > **Speichern unter**, um die Datei _azuredeploy.json_ zu benennen und sie auf Ihrem lokalen Computer zu speichern.

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
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion1": "2018-07-02",
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

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

Laden Sie die Vorlage in Cloud Shell hoch und stellen Sie sie mit Azure CLI oder Azure PowerShell bereit.

1. Melden Sie sich bei [Cloud Shell](https://shell.azure.com) an.
1. Wählen Sie **Bash** oder **PowerShell** in der oberen linken Ecke.

    :::image type="content" source="media/quickstart-troubleshoot-arm-deployment/cloud-shell-upload.png" alt-text="Screenshot der Azure Cloud Shell zur Auswahl einer Shell und zum Hochladen einer Datei.":::

1. Wählen Sie **Dateien hochladen/herunterladen** aus, und laden Sie die Datei _azuredeploy.json_ hoch.
1. Kopieren Sie zum Bereitstellen der Vorlage die folgenden Befehle und fügen Sie sie in das Shell-Fenster ein.

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter a resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    New-AzResourceGroup -Name "$resourceGroupName" -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json"
    Write-Host "Press [ENTER] to continue ..."
    ```

    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

    ```azurecli
    echo "Enter a resource group name:" &&
    read resourceGroupName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    az group create --name $resourceGroupName --location $location &&
    az deployment group create --resource-group $resourceGroupName --template-file $HOME/azuredeploy.json &&
    echo "Press [ENTER] to continue ..."
    ```

    ---

    > [!NOTE]
    > Verwenden Sie denselben Namen der Ressourcengruppe und denselben Speicherort, an dem Sie die Verteilungen durchführen. Wenn Sie aufgefordert werden, die Ressourcengruppe zu aktualisieren, wählen Sie **Ja**.

## <a name="troubleshoot-the-validation-error"></a>Behandeln des Überprüfungsfehlers

Bei einem Validierungsfehler wird in der Shell eine Fehlermeldung mit dem Fehlercode `InvalidRequestContent` angezeigt.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```Output
New-AzResourceGroupDeployment: 6:49:10 PM - Error: Code=InvalidRequestContent; Message=The request content
was invalid and could not be deserialized: 'Could not find member 'apiVersion1' on object of type
'TemplateResource'. Path 'properties.template.resources[0].apiVersion1', line 34, position 24.'.

New-AzResourceGroupDeployment: The deployment validation failed
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```Output
{"error":{"code":"InvalidRequestContent","message":"The request content was invalid and could not be
deserialized: 'Could not find member 'apiVersion1' on object of type 'TemplateResource'.
Path 'properties.template.resources[0].apiVersion1', line 32, position 20.'."}}
```

---

### <a name="fix-the-validation-error"></a>Beheben des Überprüfungsfehlers

Der Fehler liegt darin, dass `apiVersion1` falsch ist. Verwenden Sie Visual Studio Code, um `apiVersion1` in `apiVersion` zu ändern, und speichern Sie die Vorlage. Führen Sie die Schritte unter [Bereitstellung der Vorlage](#deploy-the-template) aus, um die Vorlage hochzuladen und die Bereitstellung erneut zu starten.

## <a name="troubleshoot-the-deployment-error"></a>Behandeln des Bereitstellungsfehlers

Der Bereitstellungsfehler zeigt in der Shell eine Fehlermeldung mit dem Fehlercode `NoRegisteredProviderFound` an. Sie können die Fehler auch im **Einsatzprotokoll** und **Aktivitätsprotokoll** der Ressourcengruppe einsehen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```Output
New-AzResourceGroupDeployment: 6:51:48 PM - The deployment 'azuredeploy' failed with error(s).
Showing 1 out of 1 error(s).
Status Message: No registered resource provider found for location 'centralus' and API version '2018-07-02'
for type 'storageAccounts'. The supported api-versions are '2021-06-01, 2021-05-01, 2021-04-01, 2021-02-01,
2021-01-01, 2020-08-01-preview, 2019-06-01, 2019-04-01, 2018-11-01, 2018-07-01, 2018-03-01-preview,
2018-02-01, 2017-10-01, 2017-06-01, 2016-12-01, 2016-05-01, 2016-01-01, 2015-06-15, 2015-05-01-preview'.
The supported locations are 'eastus, eastus2, westus, westeurope, eastasia, southeastasia, japaneast,
japanwest, northcentralus, southcentralus, centralus, northeurope, brazilsouth, australiaeast,
australiasoutheast, southindia, centralindia, westindia, canadaeast, canadacentral, westus2, westcentralus,
uksouth, ukwest, koreacentral, koreasouth, francecentral, australiacentral, southafricanorth, uaenorth,
switzerlandnorth, germanywestcentral, norwayeast, westus3, jioindiawest'.
(Code:NoRegisteredProviderFound)
CorrelationId: 11111111-1111-1111-1111-111111111111
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```Output
{"status":"Failed","error":{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage
details.", "details":[{"code":"BadRequest","message":"{\r\n  \"error\": {\r\n
\"code\": \"NoRegisteredProviderFound\", \r\n    \"message\": \"No registered resource provider found for
location 'centralus' and API version '2018-07-02' for type 'storageAccounts'. The supported api-versions
are '2021-06-01, 2021-05-01, 2021-04-01, 2021-02-01, 2021-01-01, 2020-08-01-preview, 2019-06-01,
2019-04-01, 2018-11-01, 2018-07-01, 2018-03-01-preview, 2018-02-01, 2017-10-01, 2017-06-01, 2016-12-01,
2016-05-01, 2016-01-01, 2015-06-15, 2015-05-01-preview'.
The supported locations are 'eastus, eastus2, westus, westeurope, eastasia, southeastasia, japaneast,
japanwest, northcentralus, southcentralus, centralus, northeurope, brazilsouth, australiaeast,
australiasoutheast, southindia, centralindia, westindia, canadaeast, canadacentral, westus2, westcentralus,
uksouth, ukwest, koreacentral, koreasouth, francecentral, australiacentral, southafricanorth, uaenorth,
switzerlandnorth, germanywestcentral, norwayeast, westus3, jioindiawest'.\"\r\n  }\r\n}"}]}}
```

---

### <a name="deployments"></a>Bereitstellungen

Der Bereitstellungsfehler kann im Azure-Portal anhand der folgenden Schritte angezeigt werden:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Geben Sie _Ressourcengruppen_ in das Suchfeld ein und wählen Sie **Ressourcengruppen**.

    :::image type="content" source="./media/quickstart-troubleshoot-arm-deployment/search-box.png" alt-text="Screenshot des Azure-Portal-Suchfeldes.":::

1. Wählen Sie den Namen der Ressourcengruppe für die Bereitstellung aus.
1. Gehen Sie zu **Übersicht** und wählen Sie den Link **1 Fehlgeschlagen**.

    :::image type="content" source="./media/quickstart-troubleshoot-arm-deployment/arm-template-deployment-error.png" alt-text="Screenshot, der den Link zur fehlgeschlagenen Bereitstellung hervorhebt.":::

1. Wählen Sie unter **Einsätze** die Option **Fehlerdetails**.

    :::image type="content" source="./media/quickstart-troubleshoot-arm-deployment/arm-template-deployment-error-details.png" alt-text="Screenshot des fehlgeschlagenen Einsatzes Link zu den Fehlerdetails.":::

    Die Fehlermeldung ist die gleiche wie die Ausgabe des Deployment-Befehls:

    :::image type="content" source="./media/quickstart-troubleshoot-arm-deployment/arm-template-deployment-error-summary.png" alt-text="Screenshot der Details des Bereitstellungsfehlers.":::

### <a name="activity-log"></a>Aktivitätsprotokoll

Sie können den Fehler auch in den Aktivitätsprotokollen der Ressourcengruppe finden. Es dauert ein paar Minuten, bis das Aktivitätsprotokoll die neuesten Einsatzinformationen anzeigt.

1. Wählen Sie in der Ressourcengruppe **Aktivitätsprotokoll**.
1. Verwenden Sie die Filter, um das Protokoll zu finden, und wählen Sie das gewünschte Protokoll aus.

    :::image type="content" source="./media/quickstart-troubleshoot-arm-deployment/arm-template-deployment-activity-log.png" alt-text="Screenshot des Aktivitätsprotokolls der Ressourcengruppe, das eine fehlgeschlagene Bereitstellung hervorhebt.":::

1. Nachdem Sie das Protokoll ausgewählt haben, werden die Details angezeigt.

    :::image type="content" source="./media/quickstart-troubleshoot-arm-deployment/arm-template-deployment-activity-log-details.png" alt-text="Screenshot der Details des Aktivitätsprotokolls, das die Fehlermeldung einer fehlgeschlagenen Bereitstellung anzeigt.":::

### <a name="fix-the-deployment-error"></a>Beheben Sie den Bereitstellungsfehler

Der Bereitstellungsfehler **Kein registrierter Ressourcenanbieter gefunden** wird durch eine falsche API-Version verursacht. Verwenden Sie Visual Studio Code, um die API-Version in einen gültigen Wert wie `2021-04-01` zu ändern, und speichern Sie die Vorlage. Führen Sie die Schritte unter [Bereitstellung der Vorlage](#deploy-the-template) aus, um die Vorlage hochzuladen und die Bereitstellung erneut zu starten.

Nachdem die Validierungs- und Bereitstellungsfehler behoben sind, wird das Speicherkonto erstellt. Gehen Sie zur **Übersicht** der Ressourcengruppe, um die Ressource zu betrachten. Das **Einrichtungsprotokoll** und das **Aktivitätsprotokoll** zeigen eine erfolgreiche Einrichtung an.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Azure-Ressourcen nicht mehr benötigt werden, löschen Sie die Ressourcengruppe. Sie können die Ressourcengruppe aus Cloud Shell oder dem Portal löschen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ersetzen Sie `<rgname>` einschließlich der spitzen Klammern durch den Namen Ihrer Ressourcengruppe.

```azurepowershell
Remove-AzResourceGroup -Name <rgname>
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Ersetzen Sie `<rgname>` einschließlich der spitzen Klammern durch den Namen Ihrer Ressourcengruppe.

```azurecli
az group delete --name <rgname>
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
