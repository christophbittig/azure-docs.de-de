---
title: 'Erstellen einer C#-Funktion über die Befehlszeile: Azure Functions'
description: Es wird beschrieben, wie Sie eine C#-Funktion über die Befehlszeile erstellen und anschließend das lokale Projekt für serverloses Hosten in Azure Functions veröffentlichen.
ms.date: 09/14/2021
ms.topic: quickstart
ms.custom:
- devx-track-csharp
- devx-track-azurecli
- devx-track-azurepowershell
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-cli-csharp-ieux
ms.openlocfilehash: 75d8177b8feaeb5bf4dfa0d7a3791b65fab930d4
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026677"
---
# <a name="quickstart-create-a-c-function-in-azure-from-the-command-line"></a>Schnellstart: Erstellen einer C#-Funktion über die Befehlszeile in Azure

In diesem Artikel erstellen Sie mithilfe von Befehlszeilentools eine C#-Funktion, die auf HTTP-Anforderungen antwortet. Der Code wird lokal getestet und anschließend in der serverlosen Umgebung von Azure Functions bereitgestellt.

[!INCLUDE [functions-dotnet-execution-model](../../includes/functions-dotnet-execution-model.md)]

In diesem Artikel wird eine durch HTTP ausgelöste Funktion erstellt, die unter .NET 6.0 ausgeführt wird. Es gibt auch eine [Visual Studio Code-basierte Version](create-first-function-vs-code-csharp.md) dieses Artikels.

Im Rahmen dieser Schnellstartanleitung fallen in Ihrem Azure-Konto ggf. geringfügige Kosten im Centbereich an.

## <a name="configure-your-local-environment"></a>Konfigurieren Ihrer lokalen Umgebung

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

+ [.NET 6.0 SDK](https://dotnet.microsoft.com/download)

+ [Azure Functions Core Tools](./functions-run-local.md#v2), Version 4.x.

+ Eines der folgenden Tools zum Erstellen von Azure-Ressourcen:

    + [Azure CLI, Version  2.4 oder höher](/cli/azure/install-azure-cli).

    + Die Azure [Az PowerShell-Modul](/powershell/azure/install-az-ps)-Version 5.9.0 oder höher.

Sie benötigen außerdem ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

### <a name="prerequisite-check"></a>Prüfen der Voraussetzungen

Überprüfen Sie Ihre Voraussetzungen, die davon abhängig sind, ob Sie die Azure CLI oder Azure PowerShell zum Erstellen von Azure-Ressourcen verwenden:

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

+ Führen Sie in einem Terminal- oder Befehlsfenster `func --version` aus, um zu überprüfen, ob Version 4.x der Azure Functions Core Tools verwendet wird.

+ Überprüfen Sie durch Ausführung von `dotnet --list-sdks`, ob die erforderlichen Versionen installiert sind.

+ Führen Sie `az --version` aus, um zu überprüfen, ob die Version 2.4 oder höher der Azure CLI verwendet wird.

+ Führen Sie `az login` aus, um sich bei Azure anzumelden und zu überprüfen, ob ein aktives Abonnement vorhanden ist.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ Führen Sie in einem Terminal- oder Befehlsfenster `func --version` aus, um zu überprüfen, ob Version 4.x der Azure Functions Core Tools verwendet wird.

+ Überprüfen Sie durch Ausführung von `dotnet --list-sdks`, ob die erforderlichen Versionen installiert sind.

+ Führen Sie `(Get-Module -ListAvailable Az).Version` aus, und stellen Sie sicher, dass Version 5.0 oder höher ausgeführt wird.

+ Führen Sie `Connect-AzAccount` aus, um sich bei Azure anzumelden und zu überprüfen, ob ein aktives Abonnement vorhanden ist.

---

## <a name="create-a-local-function-project"></a>Erstellen eines lokalen Funktionsprojekts

In Azure Functions handelt es sich bei einem Funktionsprojekt um einen Container für eine oder mehrere individuelle Funktionen, die jeweils auf einen bestimmten Trigger reagieren. Für alle Funktionen eines Projekts werden die gleichen lokalen Konfigurationen und Hostkonfigurationen gemeinsam genutzt. In diesem Abschnitt erstellen Sie ein Funktionsprojekt, das nur eine Funktion enthält.

1. Führen Sie den Befehl `func init` wie folgt aus, um in einem Ordner mit dem Namen *LocalFunctionProj* ein Funktionsprojekt mit der angegebenen Runtime zu erstellen:

    # <a name="in-process"></a>[In-Process](#tab/in-process)

    ```console
    func init LocalFunctionProj --dotnet
    ```

    # <a name="isolated-process"></a>[Isolierter Prozess](#tab/isolated-process)

    ```console
    func init LocalFunctionProj --worker-runtime dotnet-isolated
    ```
    ---

1. Navigieren Sie zum Projektordner:

    ```console
    cd LocalFunctionProj
    ```

    Dieser Ordner enthält verschiedene Dateien für das Projekt, z. B. die Konfigurationsdateien [local.settings.json](functions-develop-local.md#local-settings-file) und [host.json](functions-host-json.md). Da *local.settings.json* aus Azure heruntergeladene Geheimnisse enthalten kann, wird die Datei in der *GITIGNORE*-Datei standardmäßig aus der Quellcodeverwaltung ausgeschlossen.

1. Fügen Sie dem Projekt über den unten gezeigten Befehl eine Funktion hinzu. Hierbei ist das `--name`-Argument der eindeutige Name Ihrer Funktion (HttpExample), mit dem `--template`-Argument wird der Trigger der Funktion (HTTP) angegeben.

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```

    `func new` erstellt die Codedatei „HttpExample.cs“.

### <a name="optional-examine-the-file-contents"></a>(Optional) Untersuchen des Dateiinhalts

Bei Bedarf können Sie [Lokales Ausführen der Funktion](#run-the-function-locally) überspringen und den Dateiinhalt später untersuchen.

#### <a name="httpexamplecs"></a>HttpExample.cs

Der anhand der Vorlage generierte Funktionscode hängt vom Typ des kompilierten C#-Projekts ab.

# <a name="in-process"></a>[In-Process](#tab/in-process)

*HttpExample.cs* enthält eine `Run`-Methode, die Anforderungsdaten über die Variable `req` empfängt. Hier ist dies eine Anforderung vom Typ [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest), die durch das **HttpTriggerAttribute** zum Definieren des Triggerverhaltens ergänzt wird.

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

Das Rückgabeobjekt ist ein [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult)-Element, das eine Antwortnachricht entweder als [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) oder [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400) zurückgibt.

# <a name="isolated-process"></a>[Isolierter Prozess](#tab/isolated-process)

*HttpExample.cs* enthält eine `Run`-Methode, die Anforderungsdaten in der Variable `req` empfängt. Hier ist dies ein [HttpRequestData](/dotnet/api/microsoft.azure.functions.worker.http.httprequestdata)-Objekt, das durch das **HttpTriggerAttribute** zum Definieren des Triggerverhaltens ergänzt wird. Aufgrund des isolierten Prozessmodells handelt es sich bei `HttpRequestData` um eine Darstellung des tatsächlichen `HttpRequest` und nicht um das Anforderungsobjekt selbst.

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-isolated/HttpExample.cs":::

Das Rückgabeobjekt ist ein [HttpResponseData](/dotnet/api/microsoft.azure.functions.worker.http.httpresponsedata)-Objekt, das die Daten enthält, die an die HTTP-Antwort zurückgegeben werden.

---

Weitere Informationen finden Sie unter [HTTP-Trigger und -Bindungen in Azure Functions](./functions-bindings-http-webhook.md?tabs=csharp).

## <a name="run-the-function-locally"></a>Lokales Ausführen der Funktion

1. Führen Sie Ihre Funktion aus, indem Sie den lokalen Azure Functions-Runtimehost im Ordner *LocalFunctionProj* starten:

    ```
    func start
    ```

    Gegen Ende der Ausgabe sollten die folgenden Zeilen angezeigt werden:

    <pre>
    ...

    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.

    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...

    </pre>

    >[!NOTE]
    > Sollte „HttpExample“ nicht wie oben dargestellt angezeigt werden, haben Sie den Host wahrscheinlich außerhalb des Stammordners des Projekts gestartet. Drücken Sie in diesem Fall **STRG**+**C**, um den Host zu beenden. Navigieren Sie anschließend zum Stammordner des Projekts, und führen Sie den vorherigen Befehl erneut aus.

1. Kopieren Sie die URL Ihrer `HttpExample`-Funktion aus dieser Ausgabe in einen Browser:

    # <a name="in-process"></a>[In-Process](#tab/in-process)

     Fügen Sie die Abfragezeichenfolge `?name=<YOUR_NAME>` an die Funktions-URL an, um eine vollständige URL wie `http://localhost:7071/api/HttpExample?name=Functions` zu erhalten. Im Browser sollte eine Antwortmeldung angezeigt werden, die den Wert Ihrer Abfragezeichenkette zurückgibt. Im Terminal, in dem Sie Ihr Projekt gestartet haben, wird beim Senden von Anforderungen auch die Protokollausgabe angezeigt.

    # <a name="isolated-process"></a>[Isolierter Prozess](#tab/isolated-process)

    Navigieren Sie zur Funktions-URL. Sie daraufhin die Meldung _Willkommen bei Azure Functions_ erhalten.

    ---

1. Wenn Sie fertig sind, drücken Sie **STRG**+**C**, und wählen Sie `y` aus, um den Funktionshost zu beenden.

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. Erstellen Sie die Funktions-App in Azure:

    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli/in-process)

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location <REGION> --runtime dotnet --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    Der Befehl [az functionapp create](/cli/azure/functionapp#az_functionapp_create) erstellt die Funktions-App in Azure.

    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli/isolated-process)

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location <REGION> --runtime dotnet-isolated --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```

    Der Befehl [az functionapp create](/cli/azure/functionapp#az_functionapp_create) erstellt die Funktions-App in Azure.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell/in-process)

    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime dotnet -FunctionsVersion 3 -Location '<REGION>'
    ```

    Das Cmdlet [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) erstellt die Funktions-App in Azure.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell/isolated-process)

    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime dotnet-isolated -FunctionsVersion 3 -Location '<REGION>'
    ```

    Das Cmdlet [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) erstellt die Funktions-App in Azure.

    ---

    > [!NOTE]
    > Mit diesem Befehl wird unter Verwendung von Version 3.x der Azure Functions-Runtime eine Funktions-App erstellt. Der Befehl `func azure functionapp publish`, den Sie später ausführen, aktualisiert die App auf Version 4.x.

    Ersetzen Sie im vorherigen Beispiel `<STORAGE_NAME>` durch den Namen des Kontos, das Sie im vorherigen Schritt verwendet haben, und `<APP_NAME>` durch einen global eindeutigen Namen, der für Sie geeignet ist. `<APP_NAME>` ist gleichzeitig die DNS-Standarddomäne für die Funktions-App.

    Mit diesem Befehl wird eine Funktions-App erstellt, für die die von Ihnen angegebene Language Runtime unter dem [Azure Functions-Verbrauchstarif](consumption-plan.md) ausgeführt wird. Dies ist für die Nutzungsmenge, die in diesem Fall anfällt, kostenlos. Darüber hinaus wird mit dem Befehl auch eine zugeordnete Azure Application Insights-Instanz in derselben Ressourcengruppe bereitgestellt, mit der Sie Ihre Funktions-App überwachen und Protokolle anzeigen können. Weitere Informationen finden Sie unter [Überwachen von Azure Functions](functions-monitoring.md). Für die Instanz fallen erst Kosten an, wenn Sie sie aktivieren.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

## <a name="invoke-the-function-on-azure"></a>Aufrufen der Funktion in Azure

Da Ihre Funktion einen HTTP-Trigger verwendet und GET-Anforderungen unterstützt, rufen Sie sie auf, indem Sie eine HTTP-Anforderung an die zugehörige URL senden. Am einfachsten erreichen Sie dies über einen Browser.

# <a name="in-process"></a>[In-Process](#tab/in-process)

Kopieren Sie die vollständige **Aufruf-URL**, die in der Ausgabe des Befehls zum Veröffentlichen (publish) angezeigt wird, in eine Browseradressleiste, und fügen Sie den Abfrageparameter `?name=Functions` an. Wenn Sie zu dieser URL navigieren, sollte der Browser eine ähnliche Ausgabe anzeigen wie bei der lokalen Ausführung der Funktion.

# <a name="isolated-process"></a>[Isolierter Prozess](#tab/isolated-process)

Kopieren Sie die vollständige **Aufruf-URL**, die in der Ausgabe des publish-Befehls angezeigt wird, in eine Browseradressleiste. Wenn Sie zu dieser URL navigieren, sollte der Browser eine ähnliche Ausgabe anzeigen wie bei der lokalen Ausführung der Funktion.

---

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Nächste Schritte

# <a name="in-process"></a>[In-Process](#tab/in-process)

> [!div class="nextstepaction"]
> [Verbindungsherstellung mit Azure Queue Storage](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp&tabs=in-process)

# <a name="isolated-process"></a>[Isolierter Prozess](#tab/isolated-process)

> [!div class="nextstepaction"]
> [Verbindungsherstellung mit Azure Queue Storage](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp&tabs=isolated-process)

---
