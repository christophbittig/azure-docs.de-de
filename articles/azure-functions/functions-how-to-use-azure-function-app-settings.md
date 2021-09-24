---
title: Konfigurieren der Einstellungen einer Funktions-App in Azure Functions
description: Erfahren Sie, wie Sie die Einstellungen einer Funktions-App in Azure Functions konfigurieren.
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: cc996988-fb4f-47, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: aac032247383fe1e0b1e181c0d78864ecda778e7
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122831368"
---
# <a name="manage-your-function-app"></a>Verwalten Ihrer Funktions-App 

In Azure Functions wird mit einer Funktionen-App der Ausführungskontext für die einzelnen Funktionen angegeben. Funktionen-App-Verhalten gelten für alle von einer bestimmten Funktionen-App gehosteten Funktionen. Alle Funktionen in einer Funktions-App müssen in der gleichen [Sprache](supported-languages.md) geschrieben sein. 

Einzelne Funktionen in einer Funktions-App werden gemeinsam bereitgestellt und skaliert. Beim Skalieren der Funktions-App nutzen alle Funktionen in der gleichen Funktions-App die Ressourcen gemeinsam (pro Instanz). 

Verbindungszeichenfolgen, Umgebungsvariablen und andere Anwendungseinstellungen werden für jede Funktions-App separat definiert. Daten, die von Funktions-Apps gemeinsam genutzt werden sollen, müssen extern in einem persistenten Speicher gespeichert werden.

## <a name="get-started-in-the-azure-portal"></a>Erste Schritte im Azure-Portal

1. Wechseln Sie zunächst zum [Azure portal], und melden Sie sich bei Ihrem Azure-Konto an. Geben Sie auf der Suchleiste oben im Portal den Namen der Funktions-App ein, und wählen Sie sie in der Liste aus. 

2. Wählen Sie im linken Bereich unter **Einstellungen** die Option **Konfiguration** aus.

    :::image type="content" source="./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png" alt-text="Übersicht über Funktions-Apps im Azure-Portal":::

Auf der Übersichtsseite können Sie zu allen Punkten navigieren, die Sie zur Verwaltung Ihrer Funktions-App benötigen, insbesondere zu den **[Anwendungseinstellungen](#settings)** und **[Plattformfeatures](#platform-features)** .

## <a name="work-with-application-settings"></a><a name="settings"></a>Verwenden von Anwendungseinstellungen

Sie können eine beliebige Anzahl von Anwendungseinstellungen erstellen, die für Ihren Funktionscode erforderlich sind. Es gibt auch vordefinierte Anwendungseinstellungen, die von Functions verwendet werden. Weitere Informationen finden Sie unter [Referenz zu App-Einstellungen für Azure Functions](functions-app-settings.md).

Diese Einstellungen werden verschlüsselt gespeichert. Weitere Informationen finden Sie unter [Anwendungseinstellungen](security-concepts.md#application-settings).

Anwendungseinstellungen können über das [Azure-Portal](functions-how-to-use-azure-function-app-settings.md?tabs=portal#settings) und mithilfe der [Azure CLI](functions-how-to-use-azure-function-app-settings.md?tabs=azurecli#settings) und [Azure PowerShell](functions-how-to-use-azure-function-app-settings.md?tabs=powershell#settings)verwaltet werden. Sie können Anwendungseinstellungen auch über [Visual Studio Code](functions-develop-vs-code.md#application-settings-in-azure) und [Visual Studio](functions-develop-vs.md#function-app-settings) verwalten. 

# <a name="portal"></a>[Portal](#tab/portal)

Informationen zu den Anwendungseinstellungen finden Sie unter [Erste Schritte im Azure-Portal](#get-started-in-the-azure-portal). 

Die Registerkarte **Anwendungseinstellungen** verwaltet Einstellungen, die von Ihrer Funktions-App verwendet werden. Sie müssen **Werte anzeigen** auswählen, um die Werte im Portal anzuzeigen. Wählen Sie zum Hinzufügen einer Einstellung im Portal **Neue Anwendungseinstellung** aus, und fügen Sie das neue Schlüssel-Wert-Paar hinzu.

![Einstellungen der Funktions-App im Azure-Portal.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Der Befehl [`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_list) gibt die vorhandenen Anwendungseinstellungen zurück, wie im folgenden Beispiel zu sehen:

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

Der Befehl [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set) dient zum Hinzufügen oder Aktualisieren einer Anwendungseinstellung. Im folgenden Beispiel wird eine Einstellung mit einem Schlüssel namens `CUSTOM_FUNCTION_APP_SETTING` und dem Wert `12345` erstellt:


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Das Cmdlet [`Get-AzFunctionAppSetting`](/powershell/module/az.functions/get-azfunctionappsetting) gibt die vorhandenen Anwendungseinstellungen zurück, wie im folgenden Beispiel zu sehen: 

```azurepowershell-interactive
Get-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME>
```

Der Befehl [`Update-AzFunctionAppSetting`](/powershell/module/az.functions/update-azfunctionappsetting) dient zum Hinzufügen oder Aktualisieren einer Anwendungseinstellung. Im folgenden Beispiel wird eine Einstellung mit einem Schlüssel namens `CUSTOM_FUNCTION_APP_SETTING` und dem Wert `12345` erstellt:

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"CUSTOM_FUNCTION_APP_SETTING" = "12345"}
```

---

### <a name="use-application-settings"></a>Verwenden von Anwendungseinstellungen

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

Wenn Sie eine Funktions-App lokal entwickeln, müssen Sie lokale Kopien dieser Werte in der Projektdatei „local.settings.json“ speichern. Weitere Informationen finden Sie unter [Datei für lokale Einstellungen](functions-develop-local.md#local-settings-file).

## <a name="hosting-plan-type"></a>Typ des Hostingplans

Wenn Sie eine Funktions-App erstellen, erstellen Sie auch einen Hostingplan, in dem die App ausgeführt wird. Ein Plan kann mehrere Funktions-App umfassen. Funktionalität, Skalierung und Preis Ihrer Funktionen hängen vom Typ des Plans ab. Weitere Informationen finden Sie unter [Azure Functions-Hostingoptionen](functions-scale.md).

Sie können den von Ihrer Funktions-App verwendeten Plantyp im Azure-Portal oder mithilfe der Azure-Befehlszeilenschnittstelle oder Azure PowerShell-APIs bestimmen. 

Die folgenden Werte geben den Plantyp an:

| Plantyp | Portal | Azure-Befehlszeilenschnittstelle/PowerShell |
| --- | --- | --- |
| [Verbrauch](consumption-plan.md) | **Verbrauch** | `Dynamic` |
| [Premium](functions-premium-plan.md) | **ElasticPremium** | `ElasticPremium` |
| [Dediziert (App Service)](dedicated-plan.md) | Verschiedene | Verschiedene |

# <a name="portal"></a>[Portal](#tab/portal)

Wenn Sie den von Ihrer Funktions-App verwendeten Plantyp ermitteln möchten, sehen Sie im [Azure-Portal](https://portal.azure.com) auf der Registerkarte **Übersicht** für die Funktions-App unter **App Service-Plan** nach. Um den Tarif anzuzeigen, wählen Sie den Namen des **App Service-Plans** aus, und wählen Sie dann **Eigenschaften** im linken Bereich aus.

![Anzeigen des Skalierungsplans im Portal](./media/functions-scale/function-app-overview-portal.png)

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Führen Sie zum Abrufen des Hostingplantyps den folgenden Azure CLI-Befehl aus:

```azurecli-interactive
functionApp=<FUNCTION_APP_NAME>
resourceGroup=FunctionMonitoringExamples
appServicePlanId=$(az functionapp show --name $functionApp --resource-group $resourceGroup --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv

```  

Ersetzen Sie im vorherigen Beispiel `<RESOURCE_GROUP>` und `<FUNCTION_APP_NAME>` durch die Namen der Ressourcengruppe bzw. der Funktions-App. 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Führen Sie zum Abrufen des Hostingplantyps den folgenden Azure PowerShell-Befehl aus:

```azurepowershell-interactive
$FunctionApp = '<FUNCTION_APP_NAME>'
$ResourceGroup = '<RESOURCE_GROUP>'

$PlanID = (Get-AzFunctionApp -ResourceGroupName $ResourceGroup -Name $FunctionApp).AppServicePlan
(Get-AzFunctionAppPlan -Name $PlanID -ResourceGroupName $ResourceGroup).SkuTier
```
Ersetzen Sie im vorherigen Beispiel `<RESOURCE_GROUP>` und `<FUNCTION_APP_NAME>` durch die Namen der Ressourcengruppe bzw. der Funktions-App. 

---

## <a name="plan-migration"></a>Planen der Migration

Sie können Azure CLI-Befehle verwenden, um eine Funktions-App unter Windows zwischen einem Verbrauchsplan und einem Premium-Plan zu migrieren. Die spezifischen Befehle hängen von der Richtung der Migration ab. Die direkte Migration zu einem dedizierten Plan (App Service-Plan) wird derzeit nicht unterstützt.

Die Migration wird unter Linux nicht unterstützt.

### <a name="consumption-to-premium"></a>Vom Verbrauchsplan zum Premium-Plan

Gehen Sie wie folgt vor, um unter Windows von einem Verbrauchsplan zu einem Premium-Plan zu migrieren:

1. Führen Sie den folgenden Befehl aus, um einen neuen App Service-Plan (Elastic Premium) in derselben Region und Ressourcengruppe wie Ihre vorhandene Funktions-App zu erstellen.  

    ```azurecli-interactive
    az functionapp plan create --name <NEW_PREMIUM_PLAN_NAME> --resource-group <MY_RESOURCE_GROUP> --location <REGION> --sku EP1
    ```

1. Führen Sie den folgenden Befehl aus, um die vorhandene Funktions-App zum neuen Premium-Plan zu migrieren.

    ```azurecli-interactive
    az functionapp update --name <MY_APP_NAME> --resource-group <MY_RESOURCE_GROUP> --plan <NEW_PREMIUM_PLAN>
    ```

1. Wenn Sie den vorherigen Verbrauchs-Funktions-App-Plan nicht mehr benötigen, löschen Sie den ursprünglichen Funktions-App-Plan, nachdem Sie die erfolgreiche Migration zum neuen bestätigt haben. Führen Sie den folgenden Befehl aus, um eine Liste aller Verbrauchspläne in Ihrer Ressourcengruppe abzurufen.

    ```azurecli-interactive
    az functionapp plan list --resource-group <MY_RESOURCE_GROUP> --query "[?sku.family=='Y'].{PlanName:name,Sites:numberOfSites}" -o table
    ```

    Sie können den Plan mit 0 (null) Sites sicher löschen, denn dies ist der Plan, von dem aus Sie migriert haben.

1. Führen Sie den folgenden Befehl aus, um den Verbrauchsplan zu löschen, von dem aus Sie migriert haben.

    ```azurecli-interactive
    az functionapp plan delete --name <CONSUMPTION_PLAN_NAME> --resource-group <MY_RESOURCE_GROUP>
    ```

### <a name="premium-to-consumption"></a>Vom Premium-Plan zum Verbrauchsplan

Gehen Sie wie folgt vor, um unter Windows von einem Premium-Plan zu einem Verbrauchsplan zu migrieren:

1. Führen Sie den folgenden Befehl aus, um eine neue Funktions-App (Verbrauch) in derselben Region und Ressourcengruppe wie Ihre vorhandene Funktions-App zu erstellen. Mit diesem Befehl wird auch ein neuer Verbrauchsplan erstellt, in dem die Funktions-App ausgeführt wird.

    ```azurecli-interactive
    az functionapp create --resource-group <MY_RESOURCE_GROUP> --name <NEW_CONSUMPTION_APP_NAME> --consumption-plan-location <REGION> --runtime dotnet --functions-version 3 --storage-account <STORAGE_NAME>
    ```

1. Führen Sie den folgenden Befehl aus, um die vorhandene Funktions-App zum neuen Verbrauchsplan zu migrieren.

    ```azurecli-interactive
    az functionapp update --name <MY_APP_NAME> --resource-group <MY_RESOURCE_GROUP> --plan <NEW_CONSUMPTION_PLAN>
    ```

1. Löschen Sie die Funktions-App, die Sie in Schritt 1 erstellt haben, da Sie nur den Plan benötigen, der erstellt wurde, um die vorhandene Funktions-App auszuführen.

    ```azurecli-interactive
    az functionapp delete --name <NEW_CONSUMPTION_APP_NAME> --resource-group <MY_RESOURCE_GROUP>
    ```

1. Wenn Sie den vorherigen Premium-Funktions-App-Plan nicht mehr benötigen, löschen Sie den ursprünglichen Funktions-App-Plan, nachdem Sie die erfolgreiche Migration zum neuen bestätigt haben. Beachten Sie Folgendes: Wenn der Plan nicht gelöscht wird, wird Ihnen der Premium-Tarif weiterhin in Rechnung gestellt. Führen Sie den folgenden Befehl aus, um eine Liste aller Premium-Pläne in Ihrer Ressourcengruppe abzurufen.

    ```azurecli-interactive
    az functionapp plan list --resource-group <MY_RESOURCE_GROUP> --query "[?sku.family=='EP'].{PlanName:name,Sites:numberOfSites}" -o table
    ```

1. Führen Sie den folgenden Befehl aus, um den Premium-Plan zu löschen, von dem aus Sie migriert haben.

    ```azurecli-interactive
    az functionapp plan delete --name <PREMIUM_PLAN> --resource-group <MY_RESOURCE_GROUP>
    ```

## <a name="get-your-function-access-keys"></a>Abrufen Ihrer Funktionszugriffsschlüssel

Durch HTTP ausgelöste Funktionen können im Allgemeinen mithilfe einer URL im folgenden Format aufgerufen werden: `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>`. Wenn die Autorisierung für Ihre Funktion auf einen anderen Wert als `anonymous` festgelegt ist, müssen Sie in Ihrer Anforderung auch einen Zugriffsschlüssel angeben. Der Zugriffsschlüssel kann entweder in der URL mithilfe der `?code=`-Abfragezeichenfolge oder im Anforderungsheader angegeben werden. Weitere Informationen finden Sie unter [Funktionszugriffsschlüssel](functions-bindings-http-webhook-trigger.md#authorization-keys). Es gibt mehrere Möglichkeiten, Ihre Zugriffsschlüssel abzurufen. 

# <a name="portal"></a>[Portal](#tab/portal)

1. Melden Sie sich beim Azure-Portal an, suchen Sie nach **Funktions-App**, und wählen Sie diese Option aus.

1. Wählen Sie die Funktion aus, die Sie überprüfen möchten.

1. Wählen Sie im linken Navigationsbereich unter **Funktionen** die Option **App-Schlüssel** aus.

    Dadurch werden die Hostschlüssel zurückgegeben, die für den Zugriff auf eine beliebige Funktion in der App verwendet werden können. Außerdem wird der Systemschlüssel zurückgegeben, der jedem Zugriff auf Administratorebene auf alle Funktions-App-APIs gewährt.   

Sie können die minimalen Berechtigungen auch anwenden, indem Sie den Schlüssel nur für den bestimmten Funktionsschlüssel verwenden, indem Sie **Funktionsschlüssel** unter **Entwickler** in Ihrer durch HTTP ausgelösten Funktion auswählen. 

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Führen Sie das folgende Skript in Azure Cloud Shell aus, dessen Ausgabe der [Standardschlüssel (Host)](functions-bindings-http-webhook-trigger.md#authorization-scopes-function-level) ist, der für den Zugriff auf jede durch HTTP ausgelöste Funktion in der Funktions-App verwendet werden kann.

```azurecli-interactive
subName='<SUBSCRIPTION_ID>'
resGroup=AzureFunctionsContainers-rg
appName=glengagtestdocker
path=/subscriptions/$subName/resourceGroups/$resGroup/providers/Microsoft.Web/sites/$appName/host/default/listKeys?api-version=2018-11-01
az rest --method POST --uri $path --query functionKeys.default --output tsv
```

Ersetzen Sie in diesem Skript `<SUBSCRIPTION_ID>` und `<APP_NAME>` durch die ID Ihres Abonnements bzw. den Namen Ihrer Funktions-App. Dieses Skript wird unter Bash in Cloud Shell ausgeführt. Es muss so geändert werden, dass es in einer Windows-Eingabeaufforderung ausgeführt werden kann.  

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Führen Sie das folgende Skript aus, dessen Ausgabe der [Standardschlüssel (Host)](functions-bindings-http-webhook-trigger.md#authorization-scopes-function-level) ist, der für den Zugriff auf jede durch HTTP ausgelöste Funktion in der Funktions-App verwendet werden kann. 

```powershell-interactive
$subName = '<SUBSCRIPTION_ID>'
$rGroup = 'AzureFunctionsContainers-rg'
$appName = '<APP_NAME>'
$path = "/subscriptions/$subName/resourceGroups/$rGroup/providers/Microsoft.Web/sites/$appName/host/default/listKeys?api-version=2018-11-01"
((Invoke-AzRestMethod -Path $path -Method POST).Content | ConvertFrom-JSON).functionKeys.default
```

Ersetzen Sie in diesem Skript `<SUBSCRIPTION_ID>` und `<APP_NAME>` durch die ID Ihres Abonnements bzw. den Namen Ihrer Funktions-App. 

---

## <a name="platform-features"></a>Plattformfeatures

Funktions-Apps werden auf der Azure App Service-Plattform ausgeführt und verwaltet. So haben die Funktionen-Apps Zugriff auf die meisten Features der Azure-Kernplattform für das Webhosting. Im linken Bereich greifen Sie auf die vielen Features der App Service-Plattform zu, die Sie in Ihren Funktions-Apps verwenden können. 

> [!NOTE]
> Nicht alle App Service-Features sind verfügbar, wenn eine Funktionen-App nach dem verbrauchsbasierten Hostingplan ausgeführt wird.

Im weiteren Verlauf dieses Artikels werden schwerpunktmäßig folgende App Service-Features im Azure-Portal behandelt, die für Functions nützlich sind:

+ [App Service-Editor](#editor)
+ [Console](#console)
+ [Erweiterte Tools (Kudu)](#kudu)
+ [Bereitstellungsoptionen](#deployment)
+ [CORS](#cors)
+ [Authentifizierung](#auth)

Weitere Informationen zum Verwenden von App Service-Einstellungen finden Sie unter [Konfigurieren von Web-Apps in Azure App Service](../app-service/configure-common.md).

### <a name="app-service-editor"></a><a name="editor"></a>App Service-Editor

![App Service-Editor](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

Der App Service-Editor ist ein leistungsstarker Editor im Portal, mit dem Sie JSON-Konfigurationsdateien und Codedateien gleichermaßen bearbeiten können. Bei Auswahl dieser Option wird eine separate Browserregisterkarte mit einem einfachen Editor gestartet. Sie können damit im Git-Repository arbeiten, Code ausführen und debuggen und Einstellungen von Funktionen-Apps ändern. Im Vergleich zum integrierten Funktionen-Editor bietet dieser Editor eine erweiterte Entwicklungsumgebung für Ihre Funktionen.  

Es empfiehlt sich, Funktionen auf dem lokalen Computer zu entwickeln. Wenn Sie Funktionen lokal entwickeln und in Azure veröffentlichen, sind Ihre Projektdateien im Portal schreibgeschützt. Weitere Informationen finden Sie unter [Lokales Codieren und Testen von Azure Functions](functions-develop-local.md).

### <a name="console"></a><a name="console"></a>Konsole

![Konsole für Funktionen-Apps](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

Die Konsole im Portal ist das ideale Entwicklungstool, wenn Sie Funktionen-Apps über die Befehlszeile konfigurieren möchten. Häufig verwendete Befehle sind z.B. Erstellen von Verzeichnissen und Dateien, Navigation sowie das Ausführen von Batchdateien und -skripts. 

Bei der lokalen Entwicklung wird die Verwendung von [Azure Functions Core Tools](functions-run-local.md) und [Azure-Befehlszeilenschnittstelle] empfohlen.

### <a name="advanced-tools-kudu"></a><a name="kudu"></a>Erweiterte Tools (Kudu)

![Konfigurieren von Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

Die erweiterten Tools für App Service (auch bekannt als Kudu) ermöglichen den Zugriff auf erweiterte Verwaltungsfunktionen der Funktionen-App. Über Kudu können Sie Systeminformationen, App-Einstellungen, Umgebungsvariablen, Websiteerweiterungen, HTTP-Header und Servervariablen verwalten. Sie können **Kudu** auch starten, indem Sie zum SCM-Endpunkt für die Funktionen-App navigieren, z.B. zu `https://<myfunctionapp>.scm.azurewebsites.net/`. 


### <a name="deployment-center"></a><a name="deployment"></a>Bereitstellungscenter

Wenn Sie Ihren Funktionencode mithilfe einer Quellcodeverwaltungslösung entwickeln und verwalten, können Sie im Bereitstellungscenter beim Erstellen und Bereitstellen auf die Quellcodeverwaltung zurückgreifen. Ihr Projekt wird in Azure erstellt und bereitgestellt, wenn Sie Aktualisierungen vornehmen. Weitere Informationen finden Sie unter [Bereitstellungstechnologien in Azure Functions](functions-deployment-technologies.md).

### <a name="cross-origin-resource-sharing"></a><a name="cors"></a>Cross-Origin Resource Sharing

Um die Ausführung von schädlichem Code auf dem Client zu verhindern, blockieren moderne Browser Anforderungen, die von Webanwendungen an Ressourcen in einer separaten Domäne gerichtet werden. Mit [Cross-Origin Resource Sharing (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS) kann ein Header vom Typ `Access-Control-Allow-Origin` die Ursprünge deklarieren, von denen Endpunkte in Ihrer Funktions-App aufgerufen werden dürfen.

#### <a name="portal"></a>Portal

Wenn Sie die Liste **Zulässige Ursprünge** für Ihre Funktions-App konfigurieren, wird der Header `Access-Control-Allow-Origin` automatisch allen Antworten von HTTP-Endpunkten in Ihrer Funktions-App hinzugefügt. 

![Konfigurieren der CORS-Liste für die Funktions-App](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

Bei Verwendung des Platzhalterzeichens (`*`) werden alle anderen Domänen ignoriert. 

Verwenden Sie den Befehl [`az functionapp cors add`](/cli/azure/functionapp/cors#az_functionapp_cors_add),um der Liste mit den zulässigen Ursprüngen eine Domäne hinzuzufügen. Im folgenden Beispiel wird die Domäne „contoso.com“ hinzugefügt:

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

Verwenden Sie den Befehl [`az functionapp cors show`](/cli/azure/functionapp/cors#az_functionapp_cors_show), um die derzeit zulässigen Ursprünge aufzulisten.

### <a name="authentication"></a><a name="auth"></a>Authentifizierung

![Konfigurieren der Authentifizierung für eine Funktionen-App](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

Wenn Funktionen einen HTTP-Trigger verwenden, können Sie festlegen, dass Aufrufe zunächst authentifiziert werden müssen. App Service unterstützt die Azure Active Directory-Authentifizierung sowie die Anmeldung über Anbieter von sozialen Netzwerken wie Facebook, Microsoft und Twitter. Weitere Informationen zum Konfigurieren von bestimmten Authentifizierungsanbietern finden Sie unter [Authentifizierung und Autorisierung in Azure App Service](../app-service/overview-authentication-authorization.md). 


## <a name="next-steps"></a>Nächste Schritte

+ [Konfigurieren von Web-Apps in Azure App Service](../app-service/configure-common.md)
+ [Kontinuierliche Bereitstellung für Azure Functions](functions-continuous-deployment.md)

[Azure-Befehlszeilenschnittstelle]: /cli/azure/
[Azure portal]: https://portal.azure.com
