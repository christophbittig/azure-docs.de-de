---
title: 'Schnellstart: Erstellen einer Registrierung – Bicep'
description: Erfahren Sie, wie Sie eine Azure-Containerregistrierung mithilfe einer Bicep-Datei erstellen.
services: azure-resource-manager
author: mumian
ms.author: jgao
ms.date: 09/27/2021
ms.topic: quickstart
ms.service: azure-resource-manager
ms.openlocfilehash: a945540dc44b0ed5fd19b721424cfacee4fb924d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131430598"
---
# <a name="quickstart-create-a-container-registry-by-using-a-bicep-file"></a>Schnellstart: Erstellen einer Azure-Containerregistrierung mithilfe einer Bicep-Datei

In diesem Schnellstart erfahren Sie, wie Sie mithilfe einer Bicep-Datei eine Azure Container Registry-Instanz erstellen.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-bicep-introduction.md)]

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="review-the-bicep-file"></a>Überprüfen der Bicep-Datei

Verwenden Sie Visual Studio Code oder Ihren bevorzugten Editor, um eine Datei mit dem folgenden Inhalt zu erstellen, und nennen Sie sie **main.bicep**:

```bicep
@minLength(5)
@maxLength(50)
@description('Provide a globally unique name of your Azure Container Registry')
param acrName string = 'acr${uniqueString(resourceGroup().id)}'

@description('Provide a location for the registry.')
param location string = resourceGroup().location

@description('Provide a tier of your Azure Container Registry.')
param acrSku string = 'Basic'

resource acrResource 'Microsoft.ContainerRegistry/registries@2021-06-01-preview' = {
  name: acrName
  location: location
  sku: {
    name: acrSku
  }
  properties: {
    adminUserEnabled: false
  }
}

@description('Output the login server property for later use')
output loginServer string = acrResource.properties.loginServer

```

In der Bicep-Datei wird die folgende Ressource definiert:

* **[Microsoft.ContainerRegistry/registries](/azure/templates/microsoft.containerregistry/registries)** : Erstellen einer Azure Container Registry-Instanz.

Weitere Beispiele für Azure Container Registry-Vorlagen finden Sie im [Schnellstartvorlagenkatalog](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerregistry&pageNumber=1&sort=Popular).

## <a name="deploy-the-bicep-file"></a>Bereitstellen der Bicep-Datei

Um die erstellte Datei bereitzustellen, öffnen Sie die PowerShell oder Azure CLI. Wenn Sie das integrierte Visual Studio Code-Terminal verwenden möchten, wählen Sie die Tastenkombination `ctrl` + ```` ` ```` aus. Ändern Sie das aktuelle Verzeichnis in das Verzeichnis, in dem sich die Bicep-Datei befindet.

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

```azurecli
az group create --name myContainerRegRG --location centralus

az deployment group create --resource-group myContainerRegRG --template-file main.bicep --parameters acrName={your-unique-name}
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
New-AzResourceGroup -Name myContainerRegRG -Location centralus

New-AzResourceGroupDeployment -ResourceGroupName myContainerRegRG -TemplateFile ./main.bicep -acrName "{your-unique-name}"
```

---

> [!NOTE]
> Ersetzen Sie **{Ihren-eindeutigen-Namen}** (einschließlich der geschweiften Klammern) durch einen eindeutigen Containerregistrierungsnamen.

Nach Abschluss der Bereitstellung sollte eine Meldung mit dem Hinweis angezeigt werden, dass die Bereitstellung erfolgreich war.

## <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

Verwenden Sie das Azure-Portal oder ein Tool wie die Azure CLI, um die Eigenschaften der Containerregistrierung zu überprüfen.

1. Suchen Sie im Portal nach **Containerregistrierungen**, und wählen Sie die von Ihnen erstellte Containerregistrierung aus.

1. Notieren Sie sich auf der Seite **Übersicht** den **Anmeldeserver** der Registrierung. Verwenden Sie diesen URI, wenn Sie Docker verwenden, um Images zu markieren und in Ihre Registrierung zu pushen. Informationen finden Sie unter [Pushen Ihres ersten Images mit der Docker CLI](container-registry-get-started-docker-cli.md).

    :::image type="content" source="media/container-registry-get-started-bicep/registry-overview.png" alt-text="Registrierungsübersicht":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressource nicht mehr benötigen, löschen Sie die Ressourcengruppe und die Registrierung. Wechseln Sie hierzu zum Azure-Portal, wählen Sie die Ressourcengruppe aus, die die Registrierung enthält, und wählen Sie anschließend **Ressourcengruppe löschen** aus.

:::image type="content" source="media/container-registry-get-started-bicep/delete-resource-group.png" alt-text="Ressourcengruppe löschen":::

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie eine Azure Container Registry-Instanz mit einer Bicep-Datei erstellt. Fahren Sie mit den Azure Container Registry-Tutorials fort, um eingehendere Informationen zu ACR zu erhalten.

> [!div class="nextstepaction"]
> [Tutorials zu Azure Container Registry](container-registry-tutorial-prepare-registry.md)

Ein Schritt-für-Schritt-Tutorial mit Anleitungen zum Erstellen einer Bicep-Datei finden Sie unter folgendem Link:

> [!div class="nextstepaction"]
> [Schnellstart: Erstellen von Bicep-Dateien mit Visual Studio Code](../azure-resource-manager/bicep/quickstart-create-bicep-use-visual-studio-code.md)
