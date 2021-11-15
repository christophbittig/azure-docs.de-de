---
title: Arbeiten mit Azure Functions Core Tools
description: Erfahren Sie, wie Sie Azure Functions über die Eingabeaufforderung oder das Terminal auf dem lokalen Computer codieren und testen, bevor Sie sie in Azure Functions ausführen.
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.topic: conceptual
ms.date: 10/05/2021
ms.custom: devx-track-csharp, 80e4ff38-5174-43
ms.openlocfilehash: 8512df7ba32e7fabee5d666ca119197da7d82d6b
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132028399"
---
# <a name="work-with-azure-functions-core-tools"></a>Arbeiten mit Azure Functions Core Tools

Mit Azure Functions Core Tools können Sie Ihre Funktionen über die Eingabeaufforderung oder das Terminal auf dem lokalen Computer entwickeln und testen. Die lokalen Funktionen können mit Azure-Livediensten verbunden werden, und Sie können die Funktionen unter Verwendung der vollständigen Functions-Runtime auf dem lokalen Computer debuggen. Sie können sogar eine Funktions-App in Ihrem Azure-Abonnement bereitstellen.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Beim Entwickeln von Funktionen auf Ihrem lokalen Computer und der anschließenden Veröffentlichung auf Azure mithilfe von Core Tools führen Sie diese grundlegenden Schritte aus:

> [!div class="checklist"]
> * [Installieren Sie die Core-Tools und Abhängigkeiten.](#v2)
> * [Erstellen Sie ein Projekt für eine Funktions-App aus einer sprachspezifischen Vorlage.](#create-a-local-functions-project)
> * [Registrieren Sie die Trigger- und Bindungserweiterungen.](#register-extensions)
> * [Definieren Sie Speicher- und andere Verbindungen.](#local-settings)
> * [Erstellen Sie eine Funktion aus einem Trigger und einer sprachspezifischen Vorlage.](#create-func)
> * [Lokales Ausführen der Funktion](#start)
> * [Veröffentlichen des Projekts in Azure](#publish)

## <a name="prerequisites"></a>Voraussetzungen

Azure Functions Core Tools verwendet zurzeit entweder die [Azure CLI](/cli/azure/install-azure-cli) oder [Azure PowerShell](/powershell/azure/install-az-ps), um sich mit Ihrem Azure-Konto zu authentifizieren. Dies bedeutet, dass Sie eines dieser Tools installieren müssen, um von Azure Functions Core Tools aus [in Azure veröffentlichen](#publish) zu können. 

## <a name="core-tools-versions"></a><a name="v2"></a>Core Tools-Versionen

Es sind zwei Versionen von Azure Functions Core Tools verfügbar. Welche Version Sie verwenden, hängt von der lokalen Entwicklungsumgebung, der [Sprachauswahl](supported-languages.md) und der erforderlichen Unterstützung ab.

Wählen Sie unten eine Registerkarte „Version“ aus, um mehr über die einzelnen Versionen und detaillierte Installationsanweisungen zu erfahren:

# <a name="version-4x"></a>[Version 4.x](#tab/v4)

Unterstützt [Version 4.x](functions-versions.md) der Functions-Runtime. Diese Versionen unterstützen Windows, macOS und Linux und verwenden plattformspezifische Paket-Manager oder npm für die Installation. Dies ist die empfohlene Version der Functions-Runtime und der Core Tools.

# <a name="version-3x"></a>[Version 3.x](#tab/v3)

Unterstützt [Version 3.x](functions-versions.md) der Azure Functions-Runtime. Diese Versionen unterstützen Windows, macOS und Linux und verwenden plattformspezifische Paket-Manager oder npm für die Installation. 

# <a name="version-2x"></a>[Version 2.x](#tab/v2)

Unterstützt [Version 2.x](functions-versions.md) der Azure Functions-Runtime. Diese Versionen unterstützen Windows, macOS und Linux und verwenden plattformspezifische Paket-Manager oder npm für die Installation. 

# <a name="version-1x"></a>[Version 1.x](#tab/v1) 

Unterstützt Version 1.x der Azure Functions-Runtime. Diese Version der Tools wird nur auf Windows-Computern unterstützt und wird über ein [npm-Paket](https://www.npmjs.com/package/azure-functions-core-tools) installiert.

---

Sie können auf einem Computer immer nur jeweils eine Version der Core Tools installieren.  Sofern nicht anders angegeben, gelten die Beispiele in diesem Artikel für Version 3.x.

## <a name="install-the-azure-functions-core-tools"></a>Installieren von Azure Functions Core Tools

[Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools) umfasst eine Version der gleichen Runtime, auf der die Azure Functions-Runtime basiert, die Sie auf dem lokalen Entwicklungscomputer ausführen können. Zudem sind Befehle zum Erstellen von Funktionen, Herstellen einer Verbindung mit Azure und Bereitstellen von Functions-Projekten enthalten.

Ab Version 2.x werden Core Tools unter [Windows](?tabs=windows#v2), [macOS](?tabs=macos#v2) und [Linux](?tabs=linux#v2) ausgeführt.

# <a name="windows"></a>[Windows](#tab/windows/v4)

In den folgenden Schritten wird ein Windows Installer (MSI) zum Installieren der Core Tools v4.x verwendet. Weitere Informationen zu anderen paketbasierten Installationsprogrammen finden Sie in der [Infodatei zu Core Tools](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

Laden Sie den Core Tools-Installer basierend auf Ihrer Version von Windows herunter, und führen Sie ihn aus:

- [v4.x: Windows 64-Bit](https://go.microsoft.com/fwlink/?linkid=2174087) (Empfohlen. [Visual Studio Code-Debuggen](functions-develop-vs-code.md#debugging-functions-locally) erfordert 64-Bit.)
- [v4.x: Windows 32-Bit](https://go.microsoft.com/fwlink/?linkid=2174159)

# <a name="windows"></a>[Windows](#tab/windows/v3)

Die folgenden Schritte verwenden einen Windows Installer (MSI) zum Installieren der Core Tools v3.x. Weitere Informationen zu anderen paketbasierten Installationsprogrammen finden Sie in der [Infodatei zu Core Tools](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

Laden Sie den Core Tools-Installer basierend auf Ihrer Version von Windows herunter, und führen Sie ihn aus:

- [v3.x: Windows 64-Bit](https://go.microsoft.com/fwlink/?linkid=2135274) (empfohlen. [Visual Studio Code-Debuggen](functions-develop-vs-code.md#debugging-functions-locally) erfordert 64-Bit.)
- [v3.x: Windows 32-Bit](https://go.microsoft.com/fwlink/?linkid=2135275)

# <a name="windows"></a>[Windows](#tab/windows/v2)

Für die Installation von Version 2.x der Core Tools ist derzeit npm erforderlich. Sie können auch [Chocolatey verwenden, um das Paket zu installieren](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#azure-functions-core-tools).

1. [Installieren Sie Node.js mit npm](https://nodejs.org/en/download/), falls Sie dies noch nicht getan haben. 

1. Führen Sie den folgenden npm-Befehl aus, um das Core Tools-Paket zu installieren:

    ```
    npm install -g azure-functions-core-tools@2 --unsafe-perm true
    ```

# <a name="windows"></a>[Windows](#tab/windows/v1)

Wenn Sie Version 1.x der Core Tools installieren müssen, die nur unter Windows ausgeführt wird, finden Sie weitere Informationen im [GitHub-Repository](https://github.com/Azure/azure-functions-core-tools/blob/v1.x/README.md#installing).

# <a name="macos"></a>[macOS](#tab/macos/v4)

Die folgenden Schritte verwenden Homebrew zum Installieren der Core Tools unter macOS.

1. Installieren Sie [Homebrew](https://brew.sh/), sofern dies noch nicht geschehen ist.

1. Installieren Sie das Core Tools-Paket:

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@4
    # if upgrading on a machine that has 2.x or 3.x installed:
    brew link --overwrite azure-functions-core-tools@4
    ```

# <a name="macos"></a>[macOS](#tab/macos/v3)

Die folgenden Schritte verwenden Homebrew zum Installieren der Core Tools unter macOS.

1. Installieren Sie [Homebrew](https://brew.sh/), sofern dies noch nicht geschehen ist.

1. Installieren Sie das Core Tools-Paket:

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@3
    # if upgrading on a machine that has 2.x installed:
    brew link --overwrite azure-functions-core-tools@3
    ```

# <a name="macos"></a>[macOS](#tab/macos/v2)

Die folgenden Schritte verwenden Homebrew zum Installieren der Core Tools unter macOS.

1. Installieren Sie [Homebrew](https://brew.sh/), sofern dies noch nicht geschehen ist.

1. Installieren Sie das Core Tools-Paket:

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@2
    ```

# <a name="macos"></a>[macOS](#tab/macos/v1)

Version 1.x der Core Tools wird unter macOS nicht unterstützt. Wählen Sie auf macOS Version 2.x oder höher aus.

# <a name="linux"></a>[Linux](#tab/linux/v4)

[!INCLUDE [functions-core-tools-linux-install](../../includes/functions-core-tools-linux-install.md)]

5. Installieren Sie das Core Tools-Paket:

    ```bash
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools-4
    ```

# <a name="linux"></a>[Linux](#tab/linux/v3)

[!INCLUDE [functions-core-tools-linux-install](../../includes/functions-core-tools-linux-install.md)]

5. Installieren Sie das Core Tools-Paket:

    ```bash
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools-3
    ```

# <a name="linux"></a>[Linux](#tab/linux/v2)

[!INCLUDE [functions-core-tools-linux-install](../../includes/functions-core-tools-linux-install.md)]

5. Installieren Sie das Core Tools-Paket:

    ```bash
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools-2
    ```

# <a name="linux"></a>[Linux](#tab/linux/v1)

Version 1.x der Core Tools wird unter Linux nicht unterstützt. Wählen Sie auf Linux Version 2.x oder höher aus.

---

## <a name="changing-core-tools-versions"></a>Core Tools-Versionen wechseln

Wenn Sie zu einer anderen Version von Core Tools wechseln, sollten Sie denselben Paket-Manager wie bei der ursprünglichen Installation verwenden, um zu einer anderen Paketversion zu wechseln. Wenn Sie beispielsweise Core Tools Version 2.x mit npm installiert haben, sollten Sie den folgenden Befehl verwenden, um ein Upgrade auf Version 3.x auszuführen:

```bash
npm install -g azure-functions-core-tools@3 --unsafe-perm true
```

Wenn Sie das Windows-Installationsprogramm (MSI) zum Installieren von Core Tools auf Windows verwendet haben, sollten Sie die alte Version unter „Programme hinzufügen oder entfernen“ deinstallieren, bevor Sie eine andere Version installieren.

## <a name="create-a-local-functions-project"></a>Erstellen eines lokalen Functions-Projekts

Ein Functions-Projektverzeichnis enthält unabhängig von der Sprache die folgenden Dateien und Ordner: 

| Dateiname | BESCHREIBUNG |
| --- | --- |
| host.json | Weitere Informationen finden Sie in der [Referenz zu „host.json“](functions-host-json.md). |
| local.settings.json | Einstellungen, die bei lokaler Ausführung von Core Tools verwendet werden, einschließlich App-Einstellungen. Weitere Informationen finden Sie unter [lokale Einstellungen](#local-settings). |
| .gitignore | Verhindert, dass die Datei „local.settings.json“ versehentlich in einem Git-Repository veröffentlicht wird. Weitere Informationen finden Sie unter [lokale Einstellungen](#local-settings).|
| .vscode\extensions.json | Datei mit Einstellungen, die beim Öffnen des Projektordners in Visual Studio Code verwendet wird.  |

Weitere Informationen zum Functions-Projektordner finden Sie unter [Azure Functions-Entwicklerhandbuch](functions-reference.md#folder-structure).

Führen Sie im Terminalfenster oder über eine Eingabeaufforderung den folgenden Befehl aus, um das Projekt und ein lokales Git-Repository zu erstellen:

```
func init MyFunctionProj
```

In diesem Beispiel wird ein Functions-Projekt in einem neuen `MyFunctionProj`-Ordner erstellt. Sie werden aufgefordert, eine Standardsprache für Ihr Projekt auszuwählen. 

Die folgenden Überlegungen gelten für die Projektinitialisierung:

+ Wenn Sie die `--worker-runtime`-Option im Befehl nicht angeben, werden Sie aufgefordert, Ihre Sprache auszuwählen. Weitere Informationen finden Sie in der [Referenz zu func init](functions-core-tools-reference.md#func-init).

+ Wenn Sie keinen Projektnamen angeben, wird der aktuelle Ordner initialisiert. 

+ Wenn Sie ihr Projekt in einem benutzerdefinierten Linux-Container veröffentlichen möchten, verwenden Sie die `--dockerfile`-Option, um sicherzustellen, dass eine Dockerfile-Datei für Ihr Projekt generiert wird. Weitere Informationen finden Sie unter [Erstellen einer Funktion unter Linux mithilfe eines benutzerdefinierten Images](functions-create-function-linux-custom-image.md). 

Für bestimmte Sprachen können zusätzliche Überlegungen erforderlich sein:

# <a name="c"></a>[C\#](#tab/csharp)

+ Ab Version 2.x der Core-Tools werden Funktions-App-Projekte für die .NET-Runtime als [C#-Klassenprojekte](functions-dotnet-class-library.md) (.csproj) erstellt. Version 3.x unterstützt auch das Erstellen von Funktionen, die [unter .NET 5.0 in einem isolierten Prozess ausgeführt werden](dotnet-isolated-process-guide.md). Diese C#-Projekte, die mit Visual Studio oder Visual Studio Code verwendet werden können, werden während des Debuggens und beim Veröffentlichen in Azure kompiliert. 

+ Verwenden Sie den `--csx`-Parameter, wenn Sie lokal mit C#-Skriptdateien (CSX-Dateien) arbeiten möchten. Dies sind die gleichen Dateien, die Sie beim Erstellen von Funktionen im Azure-Portal und bei Verwendung von Version 1.x der Core Tools erhalten. Weitere Informationen finden Sie in der [Referenz zu func init](functions-core-tools-reference.md#func-init).

# <a name="java"></a>[Java](#tab/java)

+ Java verwendet einen Maven-Archetyp, um das lokale Functions-Projekt zusammen mit der ersten durch HTTP ausgelösten Funktion zu erstellen. Anstatt `func init` und `func new`zu verwenden, sollten Sie die Schritte im [Befehlszeilen-Schnellstart](./create-first-function-cli-java.md) ausführen.  

# <a name="javascript"></a>[JavaScript](#tab/node)

+ Um einen `--worker-runtime`-Wert von `node` zu verwenden, geben Sie `--language` als `javascript` an. 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Es gibt keine zusätzlichen Überlegungen für PowerShell.

# <a name="python"></a>[Python](#tab/python)

+ Sie sollten alle Befehle (einschließlich `func init`) innerhalb einer virtuellen Umgebung ausführen. Weitere Informationen finden Sie unter [Erstellen und Aktivieren einer virtuellen Umgebung](create-first-function-cli-python.md#create-venv).

# <a name="typescript"></a>[TypeScript](#tab/ts)

+ Um einen `--worker-runtime`-Wert von `node` zu verwenden, geben Sie `--language` als `javascript` an.

+ Informationen zu `func init`- Verhalten, das für TypeScript spezifisch ist, finden Sie im [Abschnitt „TypeScript“ in der JavaScript-Entwicklerreferenz](functions-reference-node.md#typescript). 

--- 

## <a name="register-extensions"></a>Registrieren von Erweiterungen

Ab Runtimeversion 2.x werden Functions-Trigger und -Bindungen als .NET-Erweiterungspakete (NuGet) implementiert. Bei kompilierten C#-Projekten verweisen Sie einfach auf die NuGet-Erweiterungspakete für die spezifischen Trigger und Bindungen, die Sie verwenden. Für HTTP-Bindungen und Zeitgebertrigger sind keine Erweiterungen erforderlich. 

Um die Entwicklungserfahrung für Nicht-C#-Projekte zu verbessern, können Sie mit Functions auf ein Erweiterungsbundle mit Versionsangabe in Ihrer Projektdatei „host.json“ verweisen. [Erweiterungsbundles](functions-bindings-register.md#extension-bundles) machen alle Erweiterungen für Ihre App verfügbar und beseitigen die Wahrscheinlichkeit von Paketkompatibilitätsproblemen zwischen Erweiterungen. Durch Erweiterungsbundles ist es auch nicht mehr notwendig, das .NET Core 3.1.x SDK zu installieren und sich mit der Datei „extensions.csproj“ zu befassen.

Erweiterungsbundles sind der empfohlene Ansatz für andere Funktionsprojekte als kompilierte C#-Projekte. Für diese Projekte wird die Erweiterungsbundleeinstellung während der Initialisierung in der Datei _host.json_ generiert. Wenn dies für Sie funktioniert, können Sie diesen Abschnitt überspringen.  

### <a name="use-extension-bundles"></a>Verwenden von Erweiterungsbündeln

[!INCLUDE [Register extensions](../../includes/functions-extension-bundles.md)]

 Wenn sie von Ihrer Sprache unterstützt werden, sollten Erweiterungsbundles bereits aktiviert sein, nachdem Sie `func init` aufgerufen haben. Sie sollten Erweiterungsbündel in de Datei „host.json“ hinzufügen, bevor Sie der Datei „function.json“ Bindungen hinzufügen. Weitere Informationen finden Sie unter [Registrieren von Bindungserweiterungen von Azure Functions](functions-bindings-register.md#extension-bundles). 

### <a name="explicitly-install-extensions"></a>Explizites Installieren von Erweiterungen

In einem Nicht-.NET Projekt kann es vorkommen, dass Sie keine Erweiterungsbundles verwenden können, z. B. wenn Sie eine bestimmte Version einer Erweiterung als Ziel verwenden müssen, die nicht im Paket enthalten ist. In diesen seltenen Fällen können Sie die Core Tools verwenden, um die spezifischen Erweiterungspakete lokal zu installieren, die für Ihr Projekt erforderlich sind. Weitere Informationen finden Sie unter [Explizites Installieren von Erweiterungen](functions-bindings-register.md#explicitly-install-extensions).

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Standardmäßig werden diese Einstellungen nicht automatisch migriert, wenn das Projekt in Azure veröffentlicht wird. Verwenden Sie die [`--publish-local-settings`-Option][func azure functionapp publish] bei der Veröffentlichung, um sicherzustellen, dass diese Einstellungen zur Funktions-App in Azure hinzugefügt werden. Werte im Abschnitt `ConnectionStrings` werden nie veröffentlicht.

Die Werte für Funktions-App-Einstellungen können auch im Code als Umgebungsvariablen gelesen werden. Weitere Informationen finden Sie im Abschnitt „Umgebungsvariablen“ dieser sprachspezifischen Referenzthemen:

* [Vorkompilierter C#-Code](functions-dotnet-class-library.md#environment-variables)
* [C#-Skript (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)
* [PowerShell](functions-reference-powershell.md#environment-variables)
* [Python](functions-reference-python.md#environment-variables)

Wenn keine gültige Speicherverbindungszeichenfolge für [`AzureWebJobsStorage`] festgelegt ist und der Emulator nicht verwendet wird, wird die folgende Fehlermeldung angezeigt:

> Missing value for AzureWebJobsStorage in local.settings.json (Fehlender Wert für AzureWebJobsStorage in local.settings.json). Dies ist für alle Nicht-HTTP-Trigger erforderlich. Sie können „func azure functionapp fetch-app-settings \<functionAppName\>“ ausführen oder eine Verbindungszeichenfolge in „local.settings.json“ eingeben.

### <a name="get-your-storage-connection-strings"></a>Abrufen der Speicherverbindungszeichenfolgen

Auch wenn der Microsoft Azure-Speicheremulator für die Entwicklung verwendet wird, empfiehlt es sich, eine tatsächliche Speicherverbindung lokal auszuführen. Wenn Sie bereits ein [Speicherkonto erstellt](../storage/common/storage-account-create.md) haben, können Sie eine gültige Speicherverbindungszeichenfolge mit einer der folgenden Methoden abrufen:

# <a name="portal"></a>[Portal](#tab/portal)

1. Suchen Sie im [Azure portal] nach **Speicherkonten**, und wählen Sie diese Option aus. 

    ![Auswählen von Speicherkonten im Azure-Portal](./media/functions-run-local/select-storage-accounts.png)
  
1.  Wählen Sie Ihr Speicherkonto aus, wählen Sie **Zugriffsschlüssel** unter **Einstellungen** aus, und kopieren Sie dann einen der Werte für **Verbindungszeichenfolge**.

    ![Kopieren der Verbindungszeichenfolge im Azure-Portal](./media/functions-run-local/copy-storage-connection-portal.png)

# <a name="core-tools"></a>[Kerntools](#tab/azurecli)

Verwenden Sie im Stammverzeichnis des Projekts einen der folgenden Befehle, um die Verbindungszeichenfolge aus Azure herunterzuladen:

  + Herunterladen aller Einstellungen aus einer vorhandenen Funktions-App:

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```

  + Abrufen der Verbindungszeichenfolge für ein bestimmtes Speicherkonto:

    ```
    func azure storage fetch-connection-string <StorageAccountName>
    ```

    Wenn Sie noch nicht in Azure angemeldet sind, werden Sie aufgefordert, sich anzumelden. Diese Befehle überschreiben alle vorhandenen Einstellungen in der Datei „local.settings.json“. Weitere Informationen finden Sie unter den Befehlen [`func azure functionapp fetch-app-settings`](functions-core-tools-reference.md#func-azure-functionapp-fetch-app-settings) und [`func azure storage fetch-connection-string`](functions-core-tools-reference.md#func-azure-storage-fetch-connection-string).

# <a name="storage-explorer"></a>[Storage-Explorer](#tab/storageexplorer)

1. Führen Sie [Azure Storage-Explorer](https://storageexplorer.com/) aus. 

1. Erweitern Sie im **Explorer** Ihr Abonnement, und erweitern Sie dann **Speicherkonten**.

1. Wählen Sie Ihr Speicherkonto aus, und kopieren Sie die primäre oder sekundäre Verbindungszeichenfolge.

    ![Kopieren der Verbindungszeichenfolge im Storage-Explorer](./media/functions-run-local/storage-explorer.png)

---

## <a name="create-a-function"></a><a name="create-func"></a>Erstellen einer Funktion

Führen Sie den folgenden Befehl aus, um eine Funktion in einem vorhandenen Projekt zu erstellen:

```
func new
```

In Version 3.x/2.x werden Sie bei der Ausführung von `func new` aufgefordert, eine Vorlage in der Standardsprache Ihrer Funktions-App auszuwählen. Anschließend werden Sie aufgefordert, einen Namen für Ihre Funktion auszuwählen. Auch in Version 1.x müssen Sie auch die Sprache auswählen. 

Sie können auch den Funktionsnamen und die Vorlage im `func new`-Befehl angeben. Im folgenden Beispiel wird die `--template`-Option verwendet, um einen HTTP-Trigger namens `MyHttpTrigger` zu erstellen:

```
func new --template "Http Trigger" --name MyHttpTrigger
```

In diesem Beispiel wird ein Queue Storage-Trigger mit dem Namen `MyQueueTrigger` erstellt:

```
func new --template "Queue Trigger" --name MyQueueTrigger
```

Weitere Informationen finden Sie unter dem [`func new`-Befehl](functions-core-tools-reference.md#func-new).

## <a name="run-functions-locally"></a><a name="start"></a>Lokales Ausführen von Funktionen

Um ein Functions-Projekt auszuführen, führen Sie den Functions-Host aus dem Stammverzeichnis Ihres Projekts aus. Der Host aktiviert Trigger für alle Funktionen im Projekt. Der [`start`-Befehl](functions-core-tools-reference.md#func-start) variiert je nach Projektsprache.

# <a name="c"></a>[C\#](#tab/csharp)

```
func start
```

# <a name="java"></a>[Java](#tab/java)

```
mvn clean package 
mvn azure-functions:run
```

# <a name="javascript"></a>[JavaScript](#tab/node)

```
func start
```


# <a name="powershell"></a>[PowerShell](#tab/powershell)

```
func start
```

# <a name="python"></a>[Python](#tab/python)

```
func start
```
Dieser Befehl [muss in einer virtuellen Umgebung ausgeführt werden](./create-first-function-cli-python.md).

# <a name="typescript"></a>[TypeScript](#tab/ts)

```
npm install
npm start     
```

---

>[!NOTE]  
> Version 1.x der Functions-Runtime erfordert stattdessen `func host start`. Weitere Informationen finden Sie in der[Referenz zu Azure Functions Core Tools](functions-core-tools-reference.md?tabs=v1#func-start). 

Wenn der Functions-Host startet, gibt er wie im folgenden Beispiel gezeigt die URL der HTTP-ausgelösten Funktionen aus:

<pre>
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
</pre>

>[!IMPORTANT]
>Bei lokaler Ausführung wird die Autorisierung für HTTP-Endpunkte nicht erzwungen. Das bedeutet, dass alle lokalen HTTP-Anforderungen wie `authLevel = "anonymous"` behandelt werden. Weitere Informationen finden Sie im Artikel über [HTTP-Bindungen](functions-bindings-http-webhook-trigger.md#authorization-keys).

### <a name="passing-test-data-to-a-function"></a>Übergeben von Testdaten an eine Funktion

Um Ihre Funktionen lokal zu testen, [starten Sie den Functions-Host](#start), und rufen Sie Endpunkte auf dem lokalen Server mithilfe von HTTP-Anforderungen auf. Der aufgerufene Endpunkt hängt vom Typ der Funktion ab.

>[!NOTE]
> Beispiele in diesem Thema verwenden das Tool cURL, um HTTP-Anforderungen vom Terminal oder einer Eingabeaufforderung zu senden. Sie können ein Tool Ihrer Wahl verwenden, um HTTP-Anforderungen an den lokalen Server zu senden. Das Tool cURL ist auf Linux-basierten Systemen sowie im Windows 10 Build 17063 und höher standardmäßig verfügbar. Unter einem älteren Windows müssen Sie das [cURL-Tool](https://curl.haxx.se/) erst herunterladen und installieren.

Allgemeinere Informationen zum Testen von Funktionen finden Sie unter [Strategien zum Testen Ihres Codes in Azure Functions](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>Über HTTP und Webhook ausgelöste Funktionen

Sie rufen die folgenden Endpunkte auf, um mit HTTP und Webhooks ausgelöste Funktionen lokal auszuführen:

```
http://localhost:{port}/api/{function_name}
```

Achten Sie darauf, den gleichen Servernamen und Port zu verwenden, auf die der Functions-Host lauscht. Dies können Sie der generierten Ausgabe entnehmen, wenn der Functions-Host gestartet wird. Sie können diese URL mit jeder HTTP-Methode aufrufen, die vom Trigger unterstützt wird.

Der folgende cURL-Befehl löst die Schnellstart-Funktion `MyHttpTrigger` von einer GET-Anforderung aus, wobei der _name_-Parameter in der Abfragezeichenfolge übergeben wird.

```
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```

Im folgenden Beispiel wird dieselbe Funktion über eine POST-Anforderung aufgerufen, die _name_ im Hauptteil der Anforderung übergibt:

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
curl --request POST http://localhost:7071/api/MyHttpTrigger --data "{'name':'Azure Rocks'}"
```
---

Sie können GET-Anforderungen über einen Browser ausführen, indem Sie Daten in der Abfragezeichenfolge übergeben. Für alle anderen HTTP-Methoden müssen Sie cURL, Fiddler, Postman oder ein ähnliches HTTP-Testtool verwenden, das POST-Anforderungen unterstützt.

#### <a name="non-http-triggered-functions"></a>Nicht über HTTP ausgelöste Funktionen

Für alle Funktionen mit Ausnahme von HTTP- und Event Grid-Triggern können Sie Ihre Funktionen lokal mit REST testen, indem Sie einen speziellen Endpunkt aufrufen, der als _Administrationsendpunkt_ bezeichnet wird. Wenn Sie diesen Endpunkt mit einer HTTP POST-Anforderung auf dem lokalen Server aufrufen, wird die Funktion ausgelöst. 

Informationen zum lokalen Testen der durch Event Grid ausgelösten Funktionen finden Sie unter [Lokales Testen mit Viewer-Web-App](functions-bindings-event-grid-trigger.md#local-testing-with-viewer-web-app).

Optional können Sie Testdaten im Hauptteil der POST-Anforderung an die Ausführung übergeben. Diese Funktionalität ähnelt der Registerkarte **Test** im Azure-Portal.

Sie rufen den folgenden Administratorendpunkt zum Auslösen von Nicht-HTTP-Funktionen auf:

```
http://localhost:{port}/admin/functions/{function_name}
```

Wenn Sie Testdaten an den Administratorendpunkt einer Funktion übergeben möchten, müssen Sie die Daten im Hauptteil einer POST-Anforderungsnachricht bereitstellen. Der Nachrichtentext muss das folgende JSON-Format aufweisen:

```JSON
{
    "input": "<trigger_input>"
}
```

Der Wert `<trigger_input>` enthält Daten in einem von der Funktion erwarteten Format. Das folgende cURL-Beispiel wird per POST an eine `QueueTriggerJS`-Funktion übergeben. In diesem Fall ist die Eingabe eine Zeichenfolge, die der in der Warteschlange erwarteten Nachricht entspricht.

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTrigger
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```bash
curl --request POST -H "Content-Type:application/json" --data "{'input':'sample queue data'}" http://localhost:7071/admin/functions/QueueTrigger
```
---

Wenn Sie einen Administratorendpunkt in Ihrer Funktions-App in Azure aufrufen, müssen Sie einen Zugriffsschlüssel angeben. Weitere Informationen finden Sie unter [Funktionszugriffsschlüssel](functions-bindings-http-webhook-trigger.md#authorization-keys).

## <a name="publish-to-azure"></a><a name="publish"></a>Veröffentlichen in Azure

Die Azure Functions Core Tools unterstützen drei Bereitstellungstypen:

| Bereitstellungstyp | Befehl | BESCHREIBUNG |
| ----- | ----- | ----- |
| Projektdateien | [`func azure functionapp publish`](functions-core-tools-reference.md#func-azure-functionapp-publish) | Stellt Funktionsprojektdateien mithilfe der [ZIP-Bereitstellung](functions-deployment-technologies.md#zip-deploy) direkt in Ihrer Funktions-App bereit. |
| Benutzerdefinierter Container | `func deploy` | Stellt Ihr Projekt in einer Linux-Funktions-App als benutzerdefinierten Docker-Container bereit.  |
| Kubernetes-Cluster | `func kubernetes deploy` | Stellt Ihre Linux-Funktions-App als benutzerdefinierten Docker-Kundencontainer in einem Kubernetes-Cluster bereit. | 

### <a name="before-you-publish"></a>Vor dem Veröffentlichen 

>[!IMPORTANT]
>Die [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli) oder [Azure PowerShell](/powershell/azure/install-az-ps) muss lokal installiert sein, um von Core Tools aus Veröffentlichungsvorgänge in Azure durchführen zu können.  

Ein Projektordner kann sprachspezifische Dateien und Verzeichnisse enthalten, die nicht veröffentlicht werden sollen. Ausgeschlossene Elemente werden in einer .funcignore-Datei im Stammprojektordner aufgelistet.  

Sie müssen bereits [eine Funktions-App im Azure-Abonnement erstellt haben](functions-cli-samples.md#create), für die Sie den Code bereitstellen. Projekte, für die eine Kompilierung erforderlich ist, müssen so erstellt werden, dass die Binärdateien bereitgestellt werden können.

Informationen zum Erstellen einer Funktions-App über die Eingabeaufforderung oder ein Terminalfenster mithilfe der Azure-Befehlszeilenschnittstelle oder Azure PowerShell finden Sie unter [Erstellen einer Funktions-App für die serverlose Ausführung](./scripts/functions-cli-create-serverless.md). 

>[!IMPORTANT]
> Wenn Sie eine Funktions-App im Azure-Portal erstellen, verwendet sie automatisch Version 3.x der Functions-Runtime. Damit die Funktions-App Version 1.x der Laufzeit verwendet, befolgen Sie die Anweisungen unter [Run on version 1.x](functions-versions.md#creating-1x-apps) (Ausführen unter Version 1.x).
> Sie können die Runtimeversion für eine Funktions-App, die über Funktionen verfügt, nicht ändern.


### <a name="deploy-project-files"></a><a name="project-file-deployment"></a>Bereitstellen von Projektdateien

Um Ihren lokalen Code in einer Funktions-App in Azure zu veröffentlichen, verwenden Sie den `publish`-Befehl:

```
func azure functionapp publish <FunctionAppName>
```

Die folgenden Überlegungen gelten für diese Art von Bereitstellung:

+ Bei der Veröffentlichung werden vorhandene Dateien in der Funktions-App überschrieben.

+ Verwenden Sie die [`--publish-local-settings`-Option][func azure functionapp publish] zum automatischen Erstellen von App-Einstellungen in Ihrer Funktions-App basierend auf Werten in der Datei „local.settings.json“.  

+ Ein [Remotebuild](functions-deployment-technologies.md#remote-build) wird für kompilierte Projekte ausgeführt. Dies kann mithilfe der [`--no-build`-Option][func azure functionapp publish] gesteuert werden.  

+ Ihr Projekt wird so bereitgestellt, dass es [aus dem Bereitstellungspaket ausgeführt wird](run-functions-from-deployment-package.md). Dieser empfohlene Bereitstellungsmodus kann mithilfe der [`--nozip`-Option][func azure functionapp publish] deaktiviert werden.

+ Java verwendet Maven, um das lokale Projekt in Azure zu veröffentlichen. Verwenden Sie stattdessen den folgenden Befehl zum Veröffentlichen in Azure: `mvn azure-functions:deploy`. Azure-Ressourcen werden während der ersten Bereitstellung erstellt.

+ Sie erhalten eine Fehlermeldung, wenn Sie versuchen, an einen `<FunctionAppName>` zu veröffentlichen, der in Ihrem Abonnement nicht vorhanden ist. 

### <a name="kubernetes-cluster"></a>Kubernetes-Cluster

Mit Functions können Sie Ihr Functions-Projekt auch für die Ausführung in einem Docker-Container definieren. Verwenden Sie die [`--docker`-Option][func init] von `func init`, um eine Dockerfile-Datei für Ihre spezifische Sprache zu generieren. Diese Datei wird dann beim Erstellen eines bereitzustellenden Containers verwendet. 

Core Tools können verwendet werden, um Ihr Projekt als benutzerdefiniertes Containerimage in einem Kubernetes-Cluster bereitzustellen. Der von Ihnen verwendete Befehl hängt vom Typ des im Cluster verwendeten Scalers ab.  

Der folgende Befehl verwendet die Dockerfile-Datei, um einen Container zu generieren und in einem Kubernetes-Cluster bereitzustellen. 

# <a name="keda"></a>[KEDA](#tab/keda)

```command
func kubernetes deploy --name <DEPLOYMENT_NAME> --registry <REGISTRY_USERNAME> 
```

Weitere Informationen finden Sie unter [Bereitstellen einer Funktions-App in Kubernetes](functions-kubernetes-keda.md#deploying-a-function-app-to-kubernetes). 

# <a name="defaultknative"></a>[Standard/KNative](#tab/default)

```command
func deploy --name <FUNCTION_APP> --platform kubernetes --registry <REGISTRY_USERNAME> 
```

Ersetzen Sie im Beispiel oben `<FUNCTION_APP>` durch den Namen der Funktions-App in Azure und `<REGISTRY_USERNAME>` durch den Namen Ihres Registrierungskontos, z. B. durch den Docker-Benutzernamen. Der Container wird lokal erstellt und mit einem Imagenamen, der auf `<FUNCTION_APP>` basiert, in Ihr Docker-Registrierungskonto gepusht. Die Docker-Befehlszeilentools müssen installiert sein.

Weitere Informationen finden Sie unter dem [`func deploy`-Befehl](functions-core-tools-reference.md#func-deploy).

---

Informationen zum Veröffentlichen eines benutzerdefinierten Containers in Azure ohne Kubernetes finden Sie unter [Erstellen einer Funktion unter Linux mithilfe eines benutzerdefinierten Containers](functions-create-function-linux-custom-image.md).

## <a name="monitoring-functions"></a>Überwachen von Funktionen

Die empfohlene Methode zum Überwachen der Ausführung Ihrer Funktionen ist die Integration in Azure Application Insights. Sie können auch Ausführungsprotokolle an Ihren lokalen Computer streamen. Weitere Informationen finden Sie unter [Überwachen von Azure Functions](functions-monitoring.md).

### <a name="application-insights-integration"></a>Application Insights-Integration

Application Insights-Integration sollte standardmäßig aktiviert sein, wenn Sie Ihre Funktions-App im Azure-Portal erstellen. Wenn Ihre Funktions-App aus irgendeinem Grund nicht mit einer Application Insights-Instanz verbunden ist, lässt sich diese Integration einfach über das Azure-Portal durchführen. Weitere Informationen finden Sie unter [Aktivieren der Application Insights-Integration](configure-monitoring.md#enable-application-insights-integration).

### <a name="enable-streaming-logs"></a>Aktivieren von Streamingprotokollen

Sie können in einer Befehlszeilensitzung auf Ihrem lokalen Computer einen Stream von Protokolldateien anzeigen, die von Ihren Funktionen generiert werden. 

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

Diese Art von Streamingprotokollen erfordert, dass die Application Insights-Integration für Ihre Funktions-App aktiviert ist.   


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie Azure Functions mithilfe Azure Functions Core Tools entwickeln, testen und veröffentlichen: [Microsoft Learn-Modul](/learn/modules/develop-test-deploy-azure-functions-with-core-tools/). Azure Functions Core Tools ist [Open Source und wird auf GitHub gehostet](https://github.com/azure/azure-functions-cli).  
Um einen Fehler zu melden oder ein Feature anzufordern, [öffnen Sie ein GitHub-Problem](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`FUNCTIONS_WORKER_RUNTIME`]: functions-app-settings.md#functions_worker_runtime
[AzureWebJobsStorage]: functions-app-settings.md#azurewebjobsstorage
[extension bundles]: functions-bindings-register.md#extension-bundles
[func azure functionapp publish]: functions-core-tools-reference.md?tabs=v2#func-azure-functionapp-publish
[func init]: functions-core-tools-reference.md?tabs=v2#func-init
