---
title: Erstellen einer App Service-App mithilfe einer Azure Resource Manager-Vorlage
description: Mit einer ARM-Vorlage (Azure Resource Manager) können Sie Ihre erste Azure App Service-App innerhalb von wenigen Sekunden erstellen. Hierbei handelt es sich um eine von vielen Bereitstellungsmöglichkeiten in App Service.
author: msangapu-msft
ms.author: msangapu
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 10/16/2020
ms.custom: subject-armqs
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 7d6111a68be8bc195b7268a778a77facdd944772
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2021
ms.locfileid: "129084130"
---
# <a name="quickstart-create-app-service-app-using-an-arm-template"></a>Schnellstart: Erstellen einer App Service-App mit einer ARM-Vorlage

Führen Sie erste Schritte mit [Azure App Service](overview.md) aus, indem Sie eine App mithilfe der folgenden Komponente bereitstellen: <abbr title="Eine JSON-Datei, die deklarativ eine oder mehrere Azure-Ressourcen und Abhängigkeiten zwischen den bereitgestellten Ressourcen definiert. Die Vorlage kann zum konsistenten und wiederholten Bereitstellen der Ressourcen verwendet werden.">ARM-Vorlage</abbr> und [Azure CLI](/cli/azure/get-started-with-azure-cli) in Cloud Shell. Da Sie einen kostenlosen App Service-Tarif verwenden, fallen bei dieser Schnellstartanleitung keine Kosten an. <abbr title="In deklarativer Syntax beschreiben Sie Ihre beabsichtigte Bereitstellung, ohne die Reihenfolge der Programmierbefehle zur Erstellung der Bereitstellung zu schreiben.">Die Vorlage verwendet eine deklarative Syntax.</abbr>

 Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von [ARM-Vorlagen](../azure-resource-manager/templates/overview.md) vertraut sind, wählen Sie die Schaltfläche **In Azure bereitstellen** aus. Die Vorlage wird im Azure-Portal geöffnet.

::: zone pivot="platform-windows"
[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.web%2Fapp-service-docs-windows%2Fazuredeploy.json)
::: zone-end

::: zone pivot="platform-linux"
[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.web%2Fapp-service-docs-linux%2Fazuredeploy.json)
::: zone-end

<hr/>

## <a name="1-prepare-your-environment"></a>1. Vorbereiten der Umgebung

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<hr/>

## <a name="2-review-the-template"></a>2. Überprüfen der Vorlage

::: zone pivot="platform-windows"
Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/app-service-docs-windows). Sie stellt einen App Service-Plan und eine App Service-App unter Windows bereit.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.web/app-service-docs-windows/azuredeploy.json":::

<details>
<summary>Welche Ressourcen und Parameter sind in der Vorlage definiert?</summary>

Zwei Azure-Ressourcen sind in der Vorlage definiert:

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): erstellt einen App Service-Plan
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): erstellt eine App Service-App

In der folgenden Tabelle sind die Standardparameter und ihre Beschreibungen aufgeführt:

| Parameter | type    | Standardwert                | Beschreibung |
|------------|---------|------------------------------|-------------|
| webAppName | string  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | App-Name |
| location   | string  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | App-Region |
| sku        | string  | "F1"                         | Instanzgröße (F1 = Free-Tarif) |
| language   | string  | ".net"                       | Sprachstapel (.NET, PHP, Node, HTML) |
| helloWorld | boolean | False                        | True = Hallo Welt-App bereitstellen |
| repoUrl    | string  | " "                          | Externes Git-Repository (optional) |

---

</details>
::: zone-end
::: zone pivot="platform-linux"
Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/app-service-docs-linux). Sie stellt einen App Service-Plan und eine App Service-App unter Windows bereit.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.web/app-service-docs-linux/azuredeploy.json":::

Diese Vorlage enthält Azure-Ressourcen und -Parameter, die der Einfachheit halber für Sie definiert wurden.

<details>
<summary>Welche Ressourcen und Parameter sind in der Vorlage definiert?</summary>

Zwei Azure-Ressourcen sind in der Vorlage definiert:

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): erstellt einen App Service-Plan
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): erstellt eine App Service-App

In der folgenden Tabelle sind die Standardparameter und ihre Beschreibungen aufgeführt:

| Parameter | type    | Standardwert                | Beschreibung |
|------------|---------|------------------------------|-------------|
| webAppName | string  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | App-Name |
| location   | string  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | App-Region |
| sku        | string  | "F1"                         | Instanzgröße (F1 = Free-Tarif) |
| linuxFxVersion   | string  | "DOTNETCORE&#124;3.0        | Sprachstapel &#124; Version |
| repoUrl    | string  | " "                          | Externes Git-Repository (optional) |

---

</details>

::: zone-end

<hr/>

## <a name="3-deploy-the-template"></a>3. Bereitstellen der Vorlage

::: zone pivot="platform-windows"
Führen Sie den folgenden Code aus, um eine .NET Framework-App unter Windows mithilfe der Azure CLI bereitzustellen. 

Replace <abbr title="Gültige Zeichen: `a-z`, `0-9` und `-`."> \<app-name> </abbr> durch einen global eindeutigen App-Namen. Weitere Informationen <abbr title="Sie können auch das Azure-Portal, Azure PowerShell oder die REST-API verwenden.">zu Bereitstellungsmethoden</abbr>finden Sie unter [Bereitstellen von Vorlagen](../azure-resource-manager/templates/deploy-powershell.md). [Hier](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sites) finden Sie weitere Beispiele für Azure App Service-Vorlagen.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup \
--parameters language=".net" helloWorld="true" webAppName="<app-name>" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.web/app-service-docs-windows/azuredeploy.json"
```
::: zone-end
::: zone pivot="platform-linux"
Führen Sie den folgenden Code aus, um eine Python-App unter Linux zu erstellen. 

Ersetzen Sie \<app-name\> durch einen global eindeutigen App-Namen. Gültige Zeichen: `a-z`, `0-9` und `-`.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --parameters webAppName="<app-name>" linuxFxVersion="PYTHON|3.7" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.web/app-service-docs-linux/azuredeploy.json"
```
::: zone-end

<details>
<summary>Was bewirkt der Code?</summary>
<p>Mit den Befehlen werden folgende Aktionen ausgeführt:</p>
<ul>
<li>Erstellen einer standardmäßigen <abbr title="Ein logischer Container für verwandte Azure-Ressourcen, die Sie als Einheit verwalten können.">Ressourcengruppe</abbr>.</li>
<li>Erstellen eines standardmäßigen <abbr title="Dieser Plan gibt den Standort, die Größe und die Features der Webserverfarm an, die Ihre App hostet.">App Service-Plan</abbr>.</li>
<li><a href="/cli/azure/webapp#az_webapp_create">Erstellen einer <abbr title="Die Darstellung Ihrer Web-App, die den Code Ihrer App, die DNS-Hostnamen, die Zertifikate und verwandte Ressourcen enthält.">App Service-App</abbr></a> mit dem angegebenen Namen.</li>
</ul>
</details>

::: zone pivot="platform-windows"
<details>
<summary>Wie stelle ich einen anderen Sprachstapel bereit?</summary>
Aktualisieren Sie zum Bereitstellen eines anderen Sprachstapels <abbr title="Diese Vorlage ist mit .NET Core, .NET Framework, PHP, Node.js und statischen HTML-Apps kompatibel.">den Sprachparameter</abbr> mit entsprechenden Werten. Informationen zu Java finden Sie unter <a href="/azure/app-service/quickstart-java-uiex">Erstellen einer Java-App</a>.

| Parameter | type    | Standardwert                | BESCHREIBUNG |
|------------|---------|------------------------------|-------------|
| language   | string  | ".net"                       | Sprachstapel (.NET, PHP, Node, HTML) |

---

</details>
::: zone-end
::: zone pivot="platform-linux"
<details>
<summary>Wie stelle ich einen anderen Sprachstapel bereit?</summary>
 
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

</details>
::: zone-end

<hr/>

## <a name="4-validate-the-deployment"></a>4. Überprüfen der Bereitstellung

Rufen Sie `http://<app_name>.azurewebsites.net/` auf, und überprüfen Sie, ob die Erstellung erfolgreich war.

<hr/>

## <a name="5-clean-up-resources"></a>5. Bereinigen von Ressourcen

[Löschen Sie die Ressourcengruppe](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group), wenn Sie sie nicht mehr benötigen.

<hr/>

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen über lokales Git](deploy-local-git.md)
- [ASP.NET Core mit SQL-Datenbank](tutorial-dotnetcore-sqldb-app.md)
- [Python mit Postgres](tutorial-python-postgresql-app.md)
- [PHP mit MySQL](tutorial-php-mysql-app.md)
- [Schnellstart: Abfragen einer Azure SQL-Datenbank mithilfe von Java](../azure-sql/database/connect-query-java.md?toc=%2fazure%2fjava%2ftoc.json)
- [Zuordnen einer benutzerdefinierten Domäne](app-service-web-tutorial-custom-domain-uiex.md)
