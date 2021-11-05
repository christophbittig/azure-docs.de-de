---
title: Erstellen einer Python-Funktion über die Befehlszeile für Azure Functions
description: Hier erfahren Sie, wie Sie eine Python-Funktion über die Befehlszeile erstellen und das lokale Projekt für das serverlose Hosten in Azure Functions veröffentlichen.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-powershell
- devx-track-azurecli
- devx-track-azurepowershell
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 951a0cec92d531aa980b519df2a046355997c175
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131039333"
---
# <a name="quickstart-create-a-python-function-in-azure-from-the-command-line"></a>Schnellstart: Erstellen einer Python-Funktion über die Befehlszeile in Azure

> [!div class="op_single_selector" title1="Wählen Sie Ihre Funktionssprache aus: "]
> - [Python](create-first-function-cli-python.md)
> - [C#](create-first-function-cli-csharp.md)
> - [Java](create-first-function-cli-java.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [TypeScript](create-first-function-cli-typescript.md)

In diesem Artikel verwenden Sie Befehlszeilentools zum Erstellen einer Python-Funktion, die auf HTTP-Anforderungen antwortet. Der Code wird lokal getestet und anschließend in der <abbr title="Eine Runtimecomputingumgebung, in der alle Serverdetails für Anwendungsentwickler transparent sind. Dies vereinfacht den Prozess der Bereitstellung sowie die Codeverwaltung.">Serverlos</abbr> Umgebung in <abbr title="Ein Azure-Dienst, der eine kostengünstige Umgebung für serverloses Computing für Anwendungen bietet.">Azure-Funktionen</abbr>.

Im Rahmen dieser Schnellstartanleitung fallen in Ihrem Azure-Konto ggf. geringfügige Kosten im Centbereich an.

Es gibt auch eine [Visual Studio Code-basierte Version](create-first-function-vs-code-python.md) dieses Artikels.

## <a name="1-configure-your-environment"></a>1. Konfigurieren Ihrer Umgebung

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

+ Ein <abbr title="Das Profil, mit dem Abrechnungsinformationen zur Azure-Nutzung verwaltet werden.">account</abbr> mit einem aktiven <abbr title="Die grundlegende Organisationsstruktur, in der Sie Ressourcen in Azure verwalten. Diese wird in der Regel einer Einzelperson oder Abteilung innerhalb einer Organisation zugeordnet.">Abonnement</abbr>. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#v2), Version 3.x.

+ Entweder die <abbr title="Eine Reihe plattformübergreifender Befehlszeilentools für die Verwendung von Azure-Ressourcen auf Ihrem lokalen Entwicklungscomputer (als Alternative zur Verwendung des Azure-Portals).">Azure CLI</abbr> oder <abbr title="Ein PowerShell-Modul, das Befehle für die Verwendung von Azure-Ressourcen auf Ihrem lokalen Entwicklungscomputer bereitstellt (als Alternative zur Verwendung des Azure-Portals).">Azure PowerShell</abbr> zum Erstellen von Azure-Ressourcen:

    + [Azure CLI, Version  2.4 oder höher](/cli/azure/install-azure-cli).

    + Das [Az PowerShell-Modul](/powershell/azure/install-az-ps) Version 5.9.0 oder höher

+ [Python 3.8 (64 Bit)](https://www.python.org/downloads/release/python-382/), [Python 3.7 (64 Bit)](https://www.python.org/downloads/release/python-375/), [Python 3.6 (64 Bit)](https://www.python.org/downloads/release/python-368/). Diese Versionen werden alle von Version 3.x von Azure Functions unterstützt.

### <a name="11-prerequisite-check"></a>1.1 Voraussetzungsprüfung

Überprüfen Sie Ihre Voraussetzungen. Diese sind davon abhängig, ob Sie die Azure CLI oder Azure PowerShell zum Erstellen von Azure-Ressourcen verwenden:

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

+ Führen Sie in einem Terminal- oder Befehlsfenster `func --version` aus, um sich zu vergewissern, dass von <abbr title="Die Befehlszeilentools für die Arbeit mit Azure Functions auf dem lokalen Computer.">Azure Functions Core Tools</abbr> Version 3.x installiert ist.

+ Führen Sie `az --version` aus, um zu überprüfen, ob die Version 2.4 oder höher der Azure CLI verwendet wird.

+ Führen Sie `az login` aus, um sich bei Azure anzumelden und zu überprüfen, ob ein aktives Abonnement vorhanden ist.

+ Führen Sie `python --version` (Linux/macOS) oder `py --version` (Windows) aus, um zu überprüfen, ob Python-Version 3.8.x, 3.7.x oder 3.6.x verwendet wird.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ Führen Sie in einem Terminal- oder Befehlsfenster `func --version` aus, um sich zu vergewissern, dass von <abbr title="Die Befehlszeilentools für die Arbeit mit Azure Functions auf dem lokalen Computer.">Azure Functions Core Tools</abbr> die Version 3.x installiert ist.

+ Führen Sie `(Get-Module -ListAvailable Az).Version` aus, und stellen Sie sicher, dass Version 5.0 oder höher ausgeführt wird.

+ Führen Sie `Connect-AzAccount` aus, um sich bei Azure anzumelden und zu überprüfen, ob ein aktives Abonnement vorhanden ist.

+ Führen Sie `python --version` (Linux/macOS) oder `py --version` (Windows) aus, um zu überprüfen, ob Python-Version 3.8.x, 3.7.x oder 3.6.x verwendet wird.

---

<br/>

---

## <a name="2-create-and-activate-a-virtual-environment"></a>2. <a name="create-venv"></a>Erstellen und Aktivieren einer virtuellen Umgebung

Führen Sie die folgenden Befehle in einem geeigneten Ordner aus, um eine virtuelle Umgebung mit dem Namen `.venv` zu erstellen und zu aktivieren. Achten Sie darauf, dass Sie Python 3.8, 3.7 oder 3.6 verwenden. Diese Versionen werden von Azure Functions unterstützt.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Führen Sie den folgenden Befehl aus, wenn über Python das venv-Paket auf Ihrer Linux-Distribution nicht installiert wurde:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

Sie führen alle nachfolgenden Befehle in dieser aktivierten virtuellen Umgebung aus.

<br/>

---

## <a name="3-create-a-local-function-project"></a>3. Erstellen eines lokalen Funktionsprojekts

In diesem Abschnitt erstellen Sie ein lokales <abbr title="Ein logischer Container für eine oder mehrere einzelne Funktionen, die zusammen bereitgestellt und verwaltet werden können.">Azure Functions-Projekt</abbr> in Python. Jede Funktion im Projekt reagiert auf einen bestimmten <abbr title="Die Art von Ereignis, durch die der Funktionscode aufgerufen wird – etwa eine HTTP-Anforderung, eine Warteschlangennachricht oder eine bestimmte Uhrzeit.">Trigger (trigger)</abbr>.

1. Führen Sie den Befehl `func init` aus, um in einem Ordner mit dem Namen *LocalFunctionProj* ein Funktionsprojekt mit der angegebenen Runtime zu erstellen:

    ```console
    func init LocalFunctionProj --python
    ```

1. Navigieren Sie zum Projektordner:

    ```console
    cd LocalFunctionProj
    ```

    <br/>
    <details>
    <summary><strong>Was wird im Ordner „LocalFunctionProj“ erstellt?</strong></summary>

    Dieser Ordner enthält verschiedene Dateien für das Projekt, z. B. die Konfigurationsdateien [local.settings.json](functions-develop-local.md#local-settings-file) und [host.json](functions-host-json.md). Da *local.settings.json* aus Azure heruntergeladene Geheimnisse enthalten kann, wird die Datei in der *GITIGNORE*-Datei standardmäßig aus der Quellcodeverwaltung ausgeschlossen.
    </details>

1. Fügen Sie Ihrem Projekt mithilfe des folgenden Befehls eine Funktion hinzu:

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```
    Das Argument `--name` ist der eindeutige Name Ihrer Funktion (HttpExample).

    Das Argument `--template` gibt den Trigger (HTTP) der Funktion an.

    Durch `func new` wird ein Unterordner mit dem Namen der Funktion erstellt. Dieser enthält eine Datei vom Typ *\_\_init\_\_.py* mit dem Code der Funktion sowie eine Konfigurationsdatei namens *function.json*.

    <br/>
    <details>
    <summary><strong>Code für __init__.py</strong></summary>

    *\_\_init\_\_.py* enthält eine Python-Funktion vom Typ `main()`, die gemäß der Konfiguration in *function.json* ausgelöst wird.

    :::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

    Für einen HTTP-Trigger empfängt die Funktion Anforderungsdaten in der Variablen `req`, wie dies in *function.json* definiert ist. `req` ist eine Instanz der [azure.functions.HttpRequest-Klasse](/python/api/azure-functions/azure.functions.httprequest). Das Rückgabeobjekt, das in *function.json* als `$return` definiert ist, ist eine Instanz der [azure.functions.HttpResponse-Klasse](/python/api/azure-functions/azure.functions.httpresponse). Weitere Informationen finden Sie unter [HTTP-Trigger und -Bindungen in Azure Functions](./functions-bindings-http-webhook.md?tabs=python).
    </details>

    <br/>
    <details>
    <summary><strong>Code für „function.json“</strong></summary>

    *function.json* ist eine Konfigurationsdatei, die die <abbr title="Deklarative Verbindungen zwischen einer Funktion und anderen Ressourcen. Eine Eingabebindung stellt Daten für die Funktion bereit. Eine Ausgabebindung stellt Daten aus der Funktion für andere Ressourcen bereit.">Eingabe- und Ausgabebindungen</abbr> für die Funktion definiert – einschließlich des Triggertyps.

    Falls erforderlich, können Sie `scriptFile` auch so ändern, dass eine andere Python-Datei aufgerufen wird.

    :::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

    Für jede Bindung sind eine Richtung, ein Typ und ein eindeutiger Name erforderlich. Der HTTP-Trigger weist eine Eingabebindung vom Typ [`httpTrigger`](functions-bindings-http-webhook-trigger.md) und eine Ausgabebindung vom Typ [`http`](functions-bindings-http-webhook-output.md) auf.
    </details>

<br/>

---

## <a name="4-run-the-function-locally"></a>4. Lokales Ausführen der Funktion

1. Führen Sie Ihre Funktion aus, indem Sie den lokalen Azure Functions-Runtimehost im Ordner *LocalFunctionProj* starten:

    ```
    func start
    ```

    Gegen Ende der Ausgabe sollten die folgenden Zeilen angezeigt werden:

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    ...

    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.

    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...

    </pre>

    <br/>
    <details>
    <summary><strong>„HttpExample“ wird in der Ausgabe nicht angezeigt.</strong></summary>

    Sollte „HttpExample“ nicht angezeigt werden, haben Sie den Host wahrscheinlich außerhalb des Stammordners des Projekts gestartet. Drücken Sie in diesem Fall <kbd>STRG+C</kbd>, um den Host zu beenden. Navigieren Sie anschließend zum Stammordner des Projekts, und führen Sie den vorherigen Befehl erneut aus.
    </details>

1. Kopieren Sie die URL Ihrer **HttpExample**-Funktion aus dieser Ausgabe in einen Browser, und fügen Sie die Abfragezeichenfolge **?name=<IHR_NAME>** an. Die vollständige URL lautet dann wie folgt: **http://localhost:7071/api/HttpExample?name=Functions** . Im Browser sollte eine Meldung wie **Hello Functions** angezeigt werden:

    ![Ergebnis der lokal im Browser ausgeführten Funktion](../../includes/media/functions-run-function-test-local-cli/function-test-local-browser.png)

1. Im Terminal, in dem Sie Ihr Projekt gestartet haben, wird beim Senden von Anforderungen auch die Protokollausgabe angezeigt.

1. Wenn Sie fertig sind, drücken Sie <kbd>STRG+C</kbd>, und wählen Sie <kbd>y</kbd> aus, um den Funktionshost zu beenden.

<br/>

---

## <a name="5-create-supporting-azure-resources-for-your-function"></a>5. Erstellen von unterstützenden Azure-Ressourcen für Ihre Funktion

Zum Bereitstellen Ihres Funktionscodes in Azure müssen Sie drei Ressourcen erstellen: eine <abbr title="Ein logischer Container für verwandte Azure-Ressourcen, die Sie als Einheit verwalten können.">Ressourcengruppe</abbr>, ein <abbr title="Ein Konto, das alle Azure-Speicherdatenobjekte enthält. Das Speicherkonto bietet einen eindeutigen Namespace für Ihre Speicherdaten.">Speicherkonto</abbr>und eine <abbr title="Die Cloudressource, die serverlose Funktionen in Azure hostet und die zugrunde liegende Compute-Umgebung für die Ausführung von Funktionen bereitstellt.">Funktions-App.</abbr> Verwenden Sie dazu die folgenden Befehle:

1. Melden Sie sich bei Azure an, falls dies noch nicht geschehen ist:

    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
    ```azurecli
    az login
    ```

    Mit dem Befehl [az login](/cli/azure/reference-index#az_login) werden Sie bei Ihrem Azure-Konto angemeldet.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    ```azurepowershell
    Connect-AzAccount
    ```

    Das Cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) meldet Sie bei Ihrem Konto an.

    ---

1. Erstellen Sie eine Ressourcengruppe mit dem Namen `AzureFunctionsQuickstart-rg` in der Region `westeurope`.

    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```

    Mit dem Befehl [az group create](/cli/azure/group#az_group_create) wird eine Ressourcengruppe erstellt. Im Allgemeinen erstellen Sie Ressourcengruppen und Ressourcen in einer <abbr title="Ein geografischer Verweis auf ein bestimmtes Azure-Rechenzentrum, in dem Ressourcen zugeordnet werden.">region</abbr> in Ihrer Nähe, indem Sie eine verfügbare Region verwenden, die vom Befehl `az account list-locations` zurückgegeben wird.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    Der Befehl [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) erstellt eine Ressourcengruppe. Im Allgemeinen erstellen Sie Ihre Ressourcengruppe und die Ressourcen in einer Region in Ihrer Nähe, indem Sie eine verfügbare Region verwenden, die vom Cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation) zurückgegeben wird.

    ---

    In derselben Ressourcengruppe können nicht gleichzeitig Linux- und Windows-Apps gehostet werden. Wenn Sie über eine bestehende Ressourcengruppe mit dem Namen `AzureFunctionsQuickstart-rg` und einer Windows-Funktions-App oder -Web-App verfügen, müssen Sie eine andere Ressourcengruppe verwenden.

1. Erstellen Sie in Ihrer Ressourcengruppe und Region ein universelles Azure Storage-Konto:

    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    Der Befehl [az storage account create](/cli/azure/storage/account#az_storage_account_create) erstellt ein Speicherkonto.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```

    Das Cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) erstellt das Speicherkonto.

    ---

    Ersetzen Sie `<STORAGE_NAME>` durch einen Namen, der für Sie geeignet und <abbr title="Der Name muss für alle Speicherkonten, die von Azure-Kunden auf der ganzen Welt genutzt werden, eindeutig sein. Sie können beispielsweise eine Kombination aus Ihrem persönlichen Namen oder Organisationsnamen, dem Anwendungsnamen und einem numerischen Bezeichner verwenden, etwa „contosobizappstorage20“.">in Azure Storage eindeutig</abbr>. Namen dürfen nur 3 bis 24 Zeichen und ausschließlich Kleinbuchstaben enthalten. Mit `Standard_LRS` wird ein universelles Konto angegeben, das [von Functions unterstützt](storage-considerations.md#storage-account-requirements) wird.

    Mit diesem Speicherkonto fallen für diese Schnellstartanleitung nur Kosten in Höhe von wenigen Cent (USD) an.

1. Erstellen Sie die Funktions-App in Azure:

    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime python --runtime-version 3.8 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME> --os-type linux
    ```

    Der Befehl [az functionapp create](/cli/azure/functionapp#az_functionapp_create) erstellt die Funktions-App in Azure. Wenn Sie Python 3.7 oder 3.6 verwenden, ändern Sie `--runtime-version` in `3.7` bzw. `3.6`.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -FunctionsVersion 3 -RuntimeVersion 3.8 -Runtime python -Location 'West Europe'
    ```

    Das Cmdlet [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) erstellt die Funktions-App in Azure. Wenn Sie Python 3.7 oder 3.6 verwenden, ändern Sie `-RuntimeVersion` in `3.7` bzw. `3.6`.

    ---

    Ersetzen Sie `<STORAGE_NAME>` durch den Namen des Kontos, das Sie im vorherigen Schritt verwendet haben.

    Ersetzen Sie `<APP_NAME>` durch einen <abbr title="Ein Name, der für alle Azure-Kunden auf der ganzen Welt eindeutig sein muss. Sie können beispielsweise eine Kombination aus Ihrem persönlichen Namen oder Organisationsnamen, dem Anwendungsnamen und einem numerischen Bezeichner verwenden, etwa „contoso-bizapp-func-20“.">global eindeutigen Namen, der für Sie geeignet ist</abbr>. `<APP_NAME>` ist gleichzeitig die DNS-Standarddomäne für die Funktions-App.

    <br/>
    <details>
    <summary><strong>Welche Kosten fallen für die in Azure bereitgestellten Ressourcen an?</strong></summary>

    Mit diesem Befehl wird eine Funktions-App erstellt, für die die von Ihnen angegebene Language Runtime unter dem [Azure Functions-Verbrauchstarif](functions-scale.md#overview-of-plans) ausgeführt wird. Dies ist für die Nutzungsmenge, die in diesem Fall anfällt, kostenlos. Darüber hinaus wird mit dem Befehl auch eine zugeordnete Azure Application Insights-Instanz in derselben Ressourcengruppe bereitgestellt, mit der Sie Ihre Funktions-App überwachen und Protokolle anzeigen können. Weitere Informationen finden Sie unter [Überwachen von Azure Functions](functions-monitoring.md). Für die Instanz fallen erst Kosten an, wenn Sie sie aktivieren.
    </details>

<br/>

---

## <a name="6-deploy-the-function-project-to-azure"></a>6. Bereitstellen des Funktionsprojekts in Azure

Nach erfolgreicher Erstellung Ihrer Funktions-App in Azure können Sie **Ihr lokales Funktionsprojekt bereitstellen**. Verwenden Sie dazu den Befehl [func azure functionapp publish](functions-run-local.md#project-file-deployment).

Ersetzen Sie im folgenden Beispiel `<APP_NAME>` durch den Namen Ihrer App.

```console
func azure functionapp publish <APP_NAME>
```

Mit dem Befehl `publish` werden Ergebnisse wie in der folgenden (gekürzten) Ausgabe angezeigt:

<pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
...

Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample
</pre>

<br/>

---

## <a name="7-invoke-the-function-on-azure"></a>7. Aufrufen der Funktion in Azure

Da für Ihre Funktion ein HTTP-Trigger verwendet wird, können Sie sie aufrufen, indem Sie im Browser eine HTTP-Anforderung an die entsprechende URL senden oder ein Tool wie <abbr title="Ein Befehlszeilentool zum Erstellen von HTTP-Anforderungen an eine URL. Siehe https://curl.se/.">curl</abbr>.

# <a name="browser"></a>[Browser](#tab/browser)

Kopieren Sie die vollständige **Aufruf-URL**, die in der Ausgabe des Befehls `publish` angezeigt wird, in eine Browseradressleiste, und fügen Sie den Abfrageparameter **&name=Functions** an. Im Browser sollte eine ähnliche Ausgabe wie bei der lokalen Ausführung der Funktion angezeigt werden.

![Ausgabe der in Azure ausgeführten Funktion in einem Browser](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

Führen Sie [`curl`](https://curl.haxx.se/) mit der **Aufruf-URL** aus, und fügen Sie den Parameter **&name=Functions** an. Die Ausgabe des Befehls sollte der Text „Hello Functions“ sein.

![Ausgabe der in Azure per curl ausgeführten Funktion](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---

### <a name="71-view-real-time-streaming-logs"></a>7.1 Anzeigen von Streamingprotokollen in Echtzeit

Führen Sie den folgenden Befehl aus, um [Streamingprotokolle](functions-run-local.md#enable-streaming-logs) nahezu in Echtzeit in Application Insights im Azure-Portal anzuzeigen:

```console
func azure functionapp logstream <APP_NAME> --browser
```

Ersetzen Sie `<APP_NAME>` durch den Namen der Funktions-App.

In einem separaten Terminalfenster oder im Browser rufen Sie die Remotefunktion erneut auf. Ein ausführliches Protokoll der Funktionsausführung in Azure wird im Terminal angezeigt.

<br/>

---

## <a name="8-clean-up-resources"></a>8. Bereinigen von Ressourcen

Wenn Sie mit dem [nächsten Schritt](#next-steps) fortfahren und eine <abbr title="Eine Möglichkeit, eine Funktion einer Speicherwarteschlange zuzuordnen, damit Nachrichten in der Warteschlange erstellt werden können. ">Azure Storage-Warteschlangen-Ausgabebindung</abbr>hinzufügen, müssen alle Ihre Ressourcen erhalten bleiben, um darauf aufbauen zu können.

Verwenden Sie andernfalls den unten angegebenen Befehl, um die Ressourcengruppe und alle darin enthaltenen Ressourcen zu löschen, damit keine weiteren Kosten anfallen.

 # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name AzureFunctionsQuickstart-rg
```

<br/>

---

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Herstellen einer Verbindung mit einer Azure Storage-Warteschlange](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-python)

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/python-functions-qs-survey)
