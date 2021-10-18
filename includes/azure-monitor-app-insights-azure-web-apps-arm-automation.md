---
ms.topic: include
ms.date: 09/03/2021
ms.author: lagayhar
author: lgayhardt
services: azure-monitor
ms.subservice: application-insights
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b88ee4d53f137f98aa82ee736043943f844f36ca
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154589"
---
### <a name="app-service-application-settings-with-azure-resource-manager"></a>App Service-Anwendungseinstellungen mit dem Azure Resource Manager.

Sie können die App Service-Anwendungseinstellungen mit [Azure Resource Manager-Vorlagen](../articles/azure-resource-manager/templates/syntax.md) verwalten und konfigurieren. Diese Methode kann bei der Bereitstellung neuer App Service-Ressourcen mit Azure Resource Manager-Automatisierung oder zur Änderung der Einstellungen vorhandener Ressourcen verwendet werden.

Die Grundstruktur des JSON-Codes für die Anwendungseinstellungen für eine App Service-Instanz sieht folgendermaßen aus:

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]
```

Ein Beispiel für eine Azure Resource Manager-Vorlage mit Anwendungseinstellungen für Application Insights finden Sie in [dieser Vorlage](https://github.com/Andrew-MSFT/BasicImageGallery), insbesondere im Abschnitt ab [Zeile 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatisieren der Erstellung einer Application Insights-Ressource und Verknüpfen Ihrer neu erstellten App Service-Instanz

Das Erstellen einer Azure Resource Manager-Vorlage mit allen Application Insights-Standardeinstellungen ähnelt am Anfang dem Erstellen einer neuen Web-App mit aktiviertem Application Insights-Dienst. 

1. Erstellen Sie eine neue App Service-Ressource mit den gewünschten Web-App-Informationen. Aktivieren Sie Application Insights auf der Registerkarte **Überwachung**.

2. Wählen Sie **Review +Erstellen** und dann unten **Vorlage für die Automatisierung herunterladen** aus.

    :::image type="content" source="../articles/azure-monitor/app/media/azure-web-apps/create-web-app.png" alt-text="Screenshot des Menüs zum Erstellen einer App Service-Web-App." :::

    Mit dieser Option wird die aktuellste Azure Resource Manager-Vorlage mit allen erforderlichen Einstellungen generiert.
    
    :::image type="content" source="../articles/azure-monitor/app/media/azure-web-apps/arm-template.png" alt-text="Screenshot der Vorlage für eine App Service-Web-App." border="false":::
    

Es folgt ein Beispiel. Ersetzen Sie alle Instanzen von `AppMonitoredSite` durch Ihren Websitenamen:

```json
{
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "properties": {
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').ConnectionString]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        }
                    ]
                },
                "name": "[parameters('name')]",
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "microsoft.insights/components/AppMonitoredSite"
            ],
            "apiVersion": "2016-03-01",
            "location": "[parameters('location')]"
        },
        {
            "apiVersion": "2016-09-01",
            "name": "[parameters('hostingPlanName')]",
            "type": "Microsoft.Web/serverfarms",
            "location": "[parameters('location')]",
            "properties": {
                "name": "[parameters('hostingPlanName')]",
                "workerSizeId": "[parameters('workerSize')]",
                "numberOfWorkers": "1",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "sku": {
                "Tier": "[parameters('sku')]",
                "Name": "[parameters('skuCode')]"
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "AppMonitoredSite",
            "type": "microsoft.insights/components",
            "location": "West US 2",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ],
    "parameters": {
        "name": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "hostingEnvironment": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "sku": {
            "type": "string"
        },
        "skuCode": {
            "type": "string"
        },
        "workerSize": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "subscriptionId": {
            "type": "string"
        }
    },
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

### <a name="enable-through-powershell"></a>Aktivieren über PowerShell

Zum Aktivieren der Anwendungsüberwachung über PowerShell müssen nur die zugrunde liegenden Anwendungseinstellungen geändert werden. Es folgt ein Beispiel, bei dem die Anwendungsüberwachung für eine Website namens „AppMonitoredSite“ in der Ressourcengruppe „AppMonitoredRG“ aktiviert wird und Daten zum Senden an den Instrumentierungsschlüssel „012345678-Abcd-ef01-2345-6789abcd“ konfiguriert werden.

[!INCLUDE [updated-for-az](updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} # preserve non Application Insights application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights instrumentation key
$newAppSettings["APPLICATIONINSIGHTS_CONNECTION_STRING"] = "InstrumentationKey=012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights connection string
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```