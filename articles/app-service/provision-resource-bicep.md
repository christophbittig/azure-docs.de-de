---
title: Erstellen einer App Service-App mit Azure Bicep
description: Mithilfe von Azure Bicep können Sie Ihre erste Azure App Service-App innerhalb von Sekunden erstellen. Hierbei handelt es sich um eine von vielen Bereitstellungsmöglichkeiten in App Service.
author: seligj95
ms.author: msangapu
ms.topic: article
ms.date: 8/26/2021
ms.openlocfilehash: 764a44a79ce6f892f0715dde6d657755a3048acf
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130257193"
---
# <a name="create-app-service-app-using-bicep"></a>Erstellen einer App Service-App mit Azure Bicep

Beginnen Sie mit der Verwendung von [Azure App Service](overview.md), indem Sie eine App mithilfe einer [Bicep](../azure-resource-manager/bicep/index.yml)-Datei und der [Azure CLI](/cli/azure/get-started-with-azure-cli) in Cloud Shell in der Cloud Shell bereitstellen. Da Sie einen kostenlosen App Service-Tarif verwenden, fallen bei dieser Schnellstartanleitung keine Kosten an.

Bicep ist eine domänenspezifische Sprache (DSL), die deklarative Syntax zum Bereitstellen von Azure-Ressourcen verwendet. Sie bietet eine präzise Syntax, zuverlässige Typsicherheit und Unterstützung für die Wiederverwendung von Code. Sie können Bicep anstelle von JSON verwenden, um Azure Resource Manager-Vorlagen ([ARM-Vorlagen](../azure-resource-manager/templates/overview.md)) zu erstellen. Die JSON-Syntax zum Erstellen einer ARM-Vorlage kann ausführlich sein und komplizierte Ausdrücke erfordern. Die Bicep-Syntax reduziert diese Komplexität und erleichtert die Entwicklung. Bicep ist eine transparente Abstraktion von ARM-Vorlagen-JSON-Code und büßt keine der JSON-Vorlagenfunktionen ein. Während der Bereitstellung transpiliert die Bicep CLI eine Bicep-Datei in ARM-Vorlagen-JSON-Code.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Zum effektiven Erstellen von Ressourcen mit Bicep müssen Sie eine Bicep-[Entwicklungsumgebung](../azure-resource-manager/bicep/install.md) einrichten. Die Bicep-Erweiterung für [Visual Studio Code](https://code.visualstudio.com/) bietet Sprachunterstützung und automatische Vervollständigung für Ressourcen. Die Erweiterung unterstützt Sie beim Erstellen und Überprüfen von Bicep-Dateien und wird für die Entwickler empfohlen, die nach Abschluss dieser Schnellstartanleitung Ressourcen mithilfe von Bicep erstellen möchten.

## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage ist unten zu sehen. Sie stellt einen App Service-Plan und eine App Service-App unter Linux sowie eine „Hallo Welt“-Node.js-Beispiel-App aus dem [Azure-Beispiele](https://github.com/Azure-Samples)-Repository bereit.

```bicep
param webAppName string = uniqueString(resourceGroup().id) // Generate unique String for web app name
param sku string = 'F1' // The SKU of App Service Plan
param linuxFxVersion string = 'node|14-lts' // The runtime stack of web app
param location string = resourceGroup().location // Location for all resources
param repositoryUrl string = 'https://github.com/Azure-Samples/nodejs-docs-hello-world'
param branch string = 'master'
var appServicePlanName = toLower('AppServicePlan-${webAppName}')
var webSiteName = toLower('wapp-${webAppName}')
resource appServicePlan 'Microsoft.Web/serverfarms@2020-06-01' = {
  name: appServicePlanName
  location: location
  properties: {
    reserved: true
  }
  sku: {
    name: sku
  }
  kind: 'linux'
}
resource appService 'Microsoft.Web/sites@2020-06-01' = {
  name: webSiteName
  location: location
  properties: {
    serverFarmId: appServicePlan.id
    siteConfig: {
      linuxFxVersion: linuxFxVersion
    }
  }
}
resource srcControls 'Microsoft.Web/sites/sourcecontrols@2021-01-01' = {
  name: '${appService.name}/web'
  properties: {
    repoUrl: repositoryUrl
    branch: branch
    isManualIntegration: true
  }
}
```

In der Vorlage sind drei Azure-Ressourcen definiert:

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): erstellt einen App Service-Plan
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): erstellt eine App Service-App
* [**Microsoft.Web/sites/sourcecontrols**](/azure/templates/microsoft.web/sites/sourcecontrols): Erstellen einer externen Git-Bereitstellungskonfiguration.

Diese Vorlage enthält mehrere vordefinierte Parameter. In der folgenden Tabelle finden Sie Standardwerte und Beschreibungen für die Parameter:

| Parameter | type    | Standardwert                | Beschreibung |
|------------|---------|------------------------------|-------------|
| webAppName | Zeichenfolge  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | App-Name |
| location   | Zeichenfolge  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | App-Region |
| sku        | Zeichenfolge  | "F1"                         | Instanzgröße  |
| linuxFxVersion   | string  | "NODE&#124;14-LTS"       | Sprachstapel &#124; Version |
| repositoryUrl    | string  | "https://github.com/Azure-Samples/nodejs-docs-hello-world"    | Externes Git-Repository (optional) |
| Verzweigung    | string  | „master“    | Standardbranch für Codebeispiel |

---

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

Kopieren Sie die Vorlage, fügen Sie sie in Ihre/n bevorzugte/n Editor/IDE ein, und speichern Sie die Datei in Ihrem lokalen Arbeitsverzeichnis.

Hier wird die Azure CLI zum Bereitstellen der Vorlage verwendet. Sie können auch das Azure-Portal, Azure PowerShell oder die REST-API verwenden. Informationen zu anderen Bereitstellungsmethoden finden Sie unter [Bicep-Bereitstellungsbefehle](../azure-resource-manager/bicep/deploy-cli.md).

Der folgende Code erstellt eine Ressourcengruppe, einen App Service-Plan und eine Web-App. Eine Standardressourcengruppe, ein App Service-Plan und ein Standort wurden für Sie festgelegt. Ersetzen Sie `<app-name>` durch einen global eindeutigen App-Namen (gültige Zeichen sind `a-z`, `0-9` und `-`).

Öffnen Sie ein Terminal, in dem die Azure CLI installiert ist, und führen Sie den folgenden Code aus, um eine Node.js-App unter Linux zu erstellen.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --template-file <path-to-template>
```

Aktualisieren Sie `linuxFxVersion` mit den entsprechenden Werten, um einen anderen Sprachstapel bereitzustellen. Im Anschluss finden Sie einige Beispiele. Führen Sie in Cloud Shell den folgenden Befehl aus, um die aktuellen Versionen anzuzeigen: `az webapp config show --resource-group myResourceGroup --name <app-name> --query linuxFxVersion`

| Sprache    | Beispiel                                              |
|-------------|------------------------------------------------------|
| **.NET**    | linuxFxVersion="DOTNETCORE&#124;3.0"                 |
| **PHP**     | linuxFxVersion="PHP&#124;7.4"                        |
| **Node.js** | linuxFxVersion="NODE&#124;10.15"                     |
| **Java**    | linuxFxVersion="JAVA&#124;1.8 &#124;TOMCAT&#124;9.0" |
| **Python**  | linuxFxVersion="PYTHON&#124;3.7"                     |
| **Ruby**    | linuxFxVersion="RUBY&#124;2.6"                       |

---

## <a name="validate-the-deployment"></a>Überprüfen der Bereitstellung

Rufen Sie `http://<app_name>.azurewebsites.net/` auf, und überprüfen Sie, ob die Erstellung erfolgreich war.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[Löschen Sie die Ressourcengruppe](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group), wenn Sie sie nicht mehr benötigen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bicep-Dokumentation](../azure-resource-manager/bicep/index.yml)
> [!div class="nextstepaction"]
> [Bicep-Beispiele für Azure App Service](./samples-bicep.md)