---
title: Erstellen von Azure-Funktionen unter Linux mit einem benutzerdefinierten Image
description: Hier erfahren Sie, wie Sie Azure Functions erstellen, die auf einem benutzerdefinierten Linux-Image ausgeführt werden.
ms.date: 12/2/2020
ms.topic: tutorial
ms.custom: devx-track-csharp, mvc, devx-track-python, devx-track-azurepowershell, devx-track-azurecli
zone_pivot_groups: programming-languages-set-functions-full
ms.openlocfilehash: a6e3ad07f9ba46bd15fe662f370ae2ffc3deb4a8
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830516"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>Erstellen einer Funktion unter Linux mit einem benutzerdefinierten Container

In diesem Tutorial erstellen Sie Code und stellen ihn anschließend in Azure Functions als benutzerdefinierten Docker-Container mit einem Linux-Basisimage bereit. Normalerweise verwenden Sie ein benutzerdefiniertes Image, wenn Sie für Ihre Funktionen eine bestimmte Sprachversion benötigen oder über eine bestimmte Abhängigkeit oder Konfiguration verfügen, die über das integrierte Image nicht bereitgestellt wird.

::: zone pivot="programming-language-other"
Von Azure Functions wird jede Sprache oder Runtime mit [benutzerdefinierten Handlern](functions-custom-handlers.md) unterstützt. Für einige Sprachen (beispielsweise die in diesem Tutorial verwendete Programmiersprache R) müssen Sie die Runtime oder zusätzliche Bibliotheken als Abhängigkeiten installieren, die die Verwendung eines benutzerdefinierten Containers erfordern.
::: zone-end

Die Bereitstellung Ihres Funktionscodes in einem benutzerdefinierten Linux-Container erfordert Hosting im [Premium-Tarif](functions-premium-plan.md) oder in einem [Dedizierten Tarif (App Service-Tarif)](dedicated-plan.md). Das Abschließen dieses Tutorials verursacht Kosten von einigen USD auf Ihrem Azure-Konto, die Sie durch [Bereinigen der Ressourcen](#clean-up-resources) minimieren können, wenn Sie fertig sind.

Sie können auch einen Azure App Service-Standardcontainer verwenden. Dies ist unter [Schnellstart: Erstellen Ihrer ersten unter Linux gehosteten Funktion unter Verwendung von Befehlszeilentools](./create-first-function-cli-csharp.md?pivots=programming-language-python) beschrieben. Unterstützte Basisimages für Azure Functions finden Sie im [Azure Functions-Repository für Basisimages](https://hub.docker.com/_/microsoft-azure-functions-base).

In diesem Tutorial lernen Sie Folgendes:

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
> [!div class="checklist"]
> * Erstellen einer Funktions-App und Dockerfile mit den Azure Functions Core Tools.
> * Erstellen eines benutzerdefinierten Images mit Docker.
> * Veröffentlichen eines benutzerdefinierten Images in einer Containerregistrierung.
> * Erstellen von unterstützenden Ressourcen in Azure für die Funktions-App.
> * Bereitstellen einer Funktions-App über Docker Hub.
> * Hinzufügen von Anwendungseinstellungen für die Funktions-App.
> * Aktivieren Sie Continuous Deployment.
> * Aktivieren Sie SSH-Verbindungen mit dem Container.
> * Fügen Sie eine Queue Storage-Ausgabebindung hinzu. 
::: zone-end
::: zone pivot="programming-language-other"
> [!div class="checklist"]
> * Erstellen einer Funktions-App und Dockerfile mit den Azure Functions Core Tools.
> * Erstellen eines benutzerdefinierten Images mit Docker.
> * Veröffentlichen eines benutzerdefinierten Images in einer Containerregistrierung.
> * Erstellen von unterstützenden Ressourcen in Azure für die Funktions-App.
> * Bereitstellen einer Funktions-App über Docker Hub.
> * Hinzufügen von Anwendungseinstellungen für die Funktions-App.
> * Aktivieren Sie Continuous Deployment.
> * Aktivieren Sie SSH-Verbindungen mit dem Container.
::: zone-end

Sie können dieses Tutorial auf allen Computern durcharbeiten, auf denen Windows, macOS oder Linux ausgeführt wird. 

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

<!---Requirements specific to Docker --->
+ [Docker](https://docs.docker.com/install/)  

+ [Docker-ID](https://hub.docker.com/signup)

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-and-test-the-local-functions-project"></a>Erstellen und Testen des lokalen Funktionsprojekts

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Führen Sie in einem Terminal oder an einer Eingabeaufforderung den folgenden Befehl für die gewählte Sprache aus, um ein Funktions-App-Projekt im aktuellen Ordner zu erstellen.  
::: zone-end  
::: zone pivot="programming-language-csharp"  

# <a name="in-process"></a>[In-Process](#tab/in-process)
```console
func init --worker-runtime dotnet --docker
```

# <a name="isolated-process"></a>[Isolierter Prozess](#tab/isolated-process)
```console
func init --worker-runtime dotnet-isolated --docker
```
---
::: zone-end  
::: zone pivot="programming-language-javascript"  
```console
func init --worker-runtime node --language javascript --docker
```
::: zone-end  
::: zone pivot="programming-language-powershell"  
```console
func init --worker-runtime powershell --docker
```
::: zone-end  
::: zone pivot="programming-language-python"  
```console
func init --worker-runtime python --docker
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```console
func init --worker-runtime node --language typescript --docker
```
::: zone-end
::: zone pivot="programming-language-java"  
Führen Sie in einem leeren Ordner den folgenden Befehl aus, um das Functions-Projekt über einen [Maven-Archetyp](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html) zu generieren.

# <a name="bash"></a>[Bash](#tab/bash)
```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8 -Ddocker
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
---

Der `-DjavaVersion`-Parameter teilt der Functions-Runtime mit, welche Version von Java verwendet werden soll. Verwenden Sie `-DjavaVersion=11`, wenn Sie Ihre Funktionen in Java 11 ausführen möchten. Wenn Sie `-DjavaVersion` nicht angeben, wird für Maven standardmäßig Java 8 verwendet. Weitere Informationen finden Sie unter [Java-Versionen](functions-reference-java.md#java-versions).

> [!IMPORTANT]
> Damit Sie diesen Artikel durcharbeiten können, muss die Umgebungsvariable `JAVA_HOME` auf den Installationsspeicherort der richtigen Version des JDK festgelegt sein.

Maven fordert Sie zur Eingabe von Werten auf, die erforderlich sind, um die Generierung des Projekts bei der Bereitstellung abzuschließen.   
Geben Sie die folgenden Werte ein, wenn Sie dazu aufgefordert werden:

| Prompt | Wert | BESCHREIBUNG |
| ------ | ----- | ----------- |
| **groupId** | `com.fabrikam` | Ein Wert, der Ihr Projekt projektübergreifend eindeutig identifiziert. Für den Wert müssen die [Paketbenennungsregeln](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) für Java eingehalten werden. |
| **artifactId** | `fabrikam-functions` | Der Name des Behälters (ohne Versionsnummer). |
| **version** | `1.0-SNAPSHOT` | Wählen Sie den Standardwert aus. |
| **package** | `com.fabrikam.functions` | Das Java-Paket für den generierten Funktionscode. Verwenden Sie den Standardwert. |

Geben Sie zur Bestätigung `Y` ein, oder drücken Sie die EINGABETASTE.

Maven erstellt die Projektdateien in einem neuen Ordner und benennt ihn mit dem Wert von _artifactId_ (in diesem Beispiel: `fabrikam-functions`). 
::: zone-end

::: zone pivot="programming-language-other"  
```console
func init --worker-runtime custom --docker
```
::: zone-end

Mit der Option `--docker` wird eine `Dockerfile` für das Projekt generiert. Hiermit wird ein geeigneter benutzerdefinierter Container zur Verwendung mit Azure Functions und der ausgewählten Runtime definiert.

::: zone pivot="programming-language-java"  
Navigieren Sie zum Projektordner:

```console
cd fabrikam-functions
```
::: zone-end  
::: zone pivot="programming-language-csharp"  

# <a name="in-process"></a>[In-Process](#tab/in-process)
An der Dockerfile-Datei sind keine Änderungen erforderlich.
# <a name="isolated-process"></a>[Isolierter Prozess](#tab/isolated-process)
Öffnen Sie die Dockerfile-Datei, und fügen Sie nach der ersten `FROM`-Anweisung die folgenden Zeilen hinzu, sofern sie noch nicht vorhanden sind:

```docker
# Build requires 3.1 SDK
COPY --from=mcr.microsoft.com/dotnet/core/sdk:3.1 /usr/share/dotnet /usr/share/dotnet
```
---
::: zone-end  
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
Fügen Sie dem Projekt eine Funktion hinzu, indem Sie den unten angegebenen Befehl verwenden. Hierbei ist das `--name`-Argument der eindeutige Name Ihrer Funktion, und mit dem `--template`-Argument wird der Trigger der Funktion angegeben. `func new` erstellt eine C#-Codedatei in Ihrem Projekt.

```console
func new --name HttpExample --template "HTTP trigger" --authlevel anonymous
```
::: zone-end

::: zone pivot="programming-language-other,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
Fügen Sie dem Projekt eine Funktion hinzu, indem Sie den unten angegebenen Befehl verwenden. Hierbei ist das `--name`-Argument der eindeutige Name Ihrer Funktion, und mit dem `--template`-Argument wird der Trigger der Funktion angegeben. Mit `func new` wird ein Unterordner mit dem Funktionsnamen erstellt, der eine Konfigurationsdatei mit dem Namen *function.json* enthält.

```console
func new --name HttpExample --template "HTTP trigger" --authlevel anonymous
```
::: zone-end  
::: zone pivot="programming-language-other" 
Erstellen Sie in einem Text-Editor eine Datei im Projektordner mit dem Namen *handler.R*. Fügen Sie den folgenden Inhalt hinzu:

```r
library(httpuv)

PORTEnv <- Sys.getenv("FUNCTIONS_CUSTOMHANDLER_PORT")
PORT <- strtoi(PORTEnv , base = 0L)

http_not_found <- list(
  status=404,
  body='404 Not Found'
)

http_method_not_allowed <- list(
  status=405,
  body='405 Method Not Allowed'
)

hello_handler <- list(
  GET = function (request) {
    list(body=paste(
      "Hello,",
      if(substr(request$QUERY_STRING,1,6)=="?name=") 
        substr(request$QUERY_STRING,7,40) else "World",
      sep=" "))
  }
)

routes <- list(
  '/api/HttpExample' = hello_handler
)

router <- function (routes, request) {
  if (!request$PATH_INFO %in% names(routes)) {
    return(http_not_found)
  }
  path_handler <- routes[[request$PATH_INFO]]

  if (!request$REQUEST_METHOD %in% names(path_handler)) {
    return(http_method_not_allowed)
  }
  method_handler <- path_handler[[request$REQUEST_METHOD]]

  return(method_handler(request))
}

app <- list(
  call = function (request) {
    response <- router(routes, request)
    if (!'status' %in% names(response)) {
      response$status <- 200
    }
    if (!'headers' %in% names(response)) {
      response$headers <- list()
    }
    if (!'Content-Type' %in% names(response$headers)) {
      response$headers[['Content-Type']] <- 'text/plain'
    }

    return(response)
  }
)

cat(paste0("Server listening on :", PORT, "...\n"))
runServer("0.0.0.0", PORT, app)
```

Ändern Sie in *host.json* den Abschnitt `customHandler`, um den Startbefehl des benutzerdefinierten Handlers zu konfigurieren.

```json
"customHandler": {
  "description": {
      "defaultExecutablePath": "Rscript",
      "arguments": [
      "handler.R"
    ]
  },
  "enableForwardingHttpRequest": true
}
```
::: zone-end

Starten Sie zum lokalen Testen der Funktion den lokalen Azure Functions-Runtimehost im Stammverzeichnis des Projektordners: 
::: zone pivot="programming-language-csharp"  
```console
func start  
```
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-powershell,programming-language-python"   
```console
func start  
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```console
npm install
npm start
```
::: zone-end  
::: zone pivot="programming-language-java"  
```console
mvn clean package  
mvn azure-functions:run
```
::: zone-end
::: zone pivot="programming-language-other"
```console
R -e "install.packages('httpuv', repos='http://cran.rstudio.com/')"
func start
```
::: zone-end 

Navigieren Sie zu `http://localhost:7071/api/HttpExample?name=Functions`, nachdem in der Ausgabe der `HttpExample`-Endpunkt angezeigt wird. Im Browser sollte eine Begrüßungsnachricht mit `Functions` (dem für den Abfrageparameter `name` angegebenen Wert) angezeigt werden.

Verwenden Sie **STRG**-**C**, um den Host zu beenden.

## <a name="build-the-container-image-and-test-locally"></a>Erstellen und lokales Testen des Containerimages

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-powershell,programming-language-python,programming-language-java,programming-language-typescript"
(Optional) Untersuchen Sie die *Dockerfile* im Stammverzeichnis des Projektordners. In der Dockerfile wird die Umgebung beschrieben, die zum Ausführen der Funktions-App unter Linux benötigt wird.  Die vollständige Liste mit den unterstützten Basisimages für Azure Functions finden Sie auf der [Seite mit Azure Functions-Basisimages](https://hub.docker.com/_/microsoft-azure-functions-base).
::: zone-end

::: zone pivot="programming-language-other"
Untersuchen Sie das *Dockerfile* im Stammverzeichnis des Projektordners. In der Dockerfile wird die Umgebung beschrieben, die zum Ausführen der Funktions-App unter Linux benötigt wird. Von Anwendungen mit benutzerdefiniertem Handler wird das Image `mcr.microsoft.com/azure-functions/dotnet:3.0-appservice` als Grundlage verwendet.

Ändern Sie das *Dockerfile*, um R zu installieren. Ersetzen Sie den Inhalt des *Dockerfile* durch Folgendes:

```dockerfile
FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice 
ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
    AzureFunctionsJobHost__Logging__Console__IsEnabled=true

RUN apt update && \
    apt install -y r-base && \
    R -e "install.packages('httpuv', repos='http://cran.rstudio.com/')"

COPY . /home/site/wwwroot
```
::: zone-end

Führen Sie im Stammprojektordner den Befehl [docker build](https://docs.docker.com/engine/reference/commandline/build/) aus, und geben Sie einen Namen (`azurefunctionsimage`) und ein Tag (`v1.0.0`) an. Ersetzen Sie `<DOCKER_ID>` durch Ihre Docker Hub-Konto-ID. Dieser Befehl erstellt das Docker-Image für den Container.

```console
docker build --tag <DOCKER_ID>/azurefunctionsimage:v1.0.0 .
```

Nachdem der Befehl abgeschlossen wurde, können Sie den neuen Container lokal ausführen.
    
Führen Sie das Image zum Testen des Builds in einem lokalen Container aus, indem Sie den Befehl [docker run](https://docs.docker.com/engine/reference/commandline/run/) verwenden und erneut `<DOCKER_ID` durch Ihre Docker-ID ersetzen und das Portargument `-p 8080:80` hinzufügen:

```console
docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
```

::: zone pivot="programming-language-csharp"
# <a name="in-process"></a>[In-Process](#tab/in-process)
Nachdem das Image im lokalen Container gestartet wird, navigieren Sie zu `http://localhost:8080/api/HttpExample?name=Functions`, das die gleiche „Hallo“-Meldung wie zuvor anzeigen sollte. Da die von Ihnen erstellte von HTTP ausgelöste Funktion anonyme Autorisierung verwendet, können Sie die im Container ausgeführte Funktion aufrufen, ohne einen Zugriffsschlüssel abrufen zu müssen. Weitere Informationen finden Sie unter [Autorisierungsschlüssel].
# <a name="isolated-process"></a>[Isolierter Prozess](#tab/isolated-process)
Nachdem das Image im lokalen Container gestartet wird, navigieren Sie zu `http://localhost:8080/api/HttpExample`, das die gleiche Begrüßungsmeldung wie zuvor anzeigen sollte. Da die von Ihnen erstellte von HTTP ausgelöste Funktion anonyme Autorisierung verwendet, können Sie die im Container ausgeführte Funktion aufrufen, ohne einen Zugriffsschlüssel abrufen zu müssen. Weitere Informationen finden Sie unter [Autorisierungsschlüssel].

---
::: zone-end
::: zone pivot="programming-language-java,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-other"  
Nachdem das Image im lokalen Container gestartet wird, navigieren Sie zu `http://localhost:8080/api/HttpExample?name=Functions`, das die gleiche „Hallo“-Meldung wie zuvor anzeigen sollte. Da die von Ihnen erstellte von HTTP ausgelöste Funktion anonyme Autorisierung verwendet, können Sie die im Container ausgeführte Funktion aufrufen, ohne einen Zugriffsschlüssel abrufen zu müssen. Weitere Informationen finden Sie unter [Autorisierungsschlüssel].
::: zone-end  

Nachdem Sie die Funktions-App im Container überprüft haben, beenden Sie Docker mit **STRG**+**C**.

## <a name="push-the-image-to-docker-hub"></a>Übertragen des Images per Pushvorgang auf Docker Hub

Docker Hub ist eine Containerregistrierung, mit der Images gehostet und Image- und Containerdienste bereitgestellt werden. Um Ihr Image freizugeben (umfasst auch die Bereitstellung in Azure), müssen Sie es per Pushvorgang in eine Registrierung übertragen.

1. Wenn Sie sich noch nicht bei Docker angemeldet haben, können Sie dies durchführen, indem Sie den Befehl [docker login](https://docs.docker.com/engine/reference/commandline/login/) verwenden und `<docker_id>` durch Ihre Docker-ID ersetzen. Mit diesem Befehl werden Sie zum Eingeben Ihres Benutzernamens und Kennworts aufgefordert. Mit einer Meldung der Art „Anmeldung erfolgreich“ wird bestätigt, dass die Anmeldung erfolgreich war.

    ```console
    docker login
    ```
    
1. Übertragen Sie das Image nach dem Anmelden per Pushvorgang an Docker Hub, indem Sie den Befehl [docker push](https://docs.docker.com/engine/reference/commandline/push/) verwenden und `<docker_id>` wieder durch Ihre Docker-ID ersetzen.

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. Je nach Netzwerkgeschwindigkeit kann der erste Pushvorgang für das Image einige Minuten dauern (das Pushen der nachfolgenden Änderungen geht deutlich schneller). Während Sie warten, können Sie mit dem nächsten Abschnitt fortfahren und Azure-Ressourcen in einem anderen Terminal erstellen.

## <a name="create-supporting-azure-resources-for-your-function"></a>Erstellen von unterstützenden Azure-Ressourcen für Ihre Funktion

Zum Bereitstellen Ihres Funktionscodes in Azure müssen Sie drei Ressourcen erstellen:

- Eine [Ressourcengruppe](../azure-resource-manager/management/overview.md), bei der es sich um einen logischen Container für verwandte Ressourcen handelt.
- Ein [Speicherkonto](../storage/common/storage-account-create.md), das verwendet wird, um den Status und andere Informationen zu Ihren Funktionen zu verwalten.
- Eine Funktions-App, die als Umgebung zum Ausführen Ihres Funktionscodes dient. Eine Funktions-App ist Ihrem lokalen Funktionsprojekt zugeordnet und ermöglicht Ihnen das Gruppieren von Funktionen als logische Einheit, um die Verwaltung, Bereitstellung und Freigabe von Ressourcen zu vereinfachen.

Verwenden Sie die folgenden Befehle, um diese Elemente zu erstellen. Sowohl die Azure CLI als auch PowerShell werden unterstützt.

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

1. Erstellen Sie eine Ressourcengruppe mit dem Namen `AzureFunctionsContainers-rg` in der ausgewählten Region:

    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location <REGION>
    ```
 
    Mit dem Befehl [az group create](/cli/azure/group#az_group_create) wird eine Ressourcengruppe erstellt. Ersetzen Sie im Befehl oben `<REGION>` durch eine Region in Ihrer Nähe, indem Sie einen verfügbaren Regionscode verwenden, der mit dem Befehl [az account list-locations](/cli/azure/account#az_account_list_locations) zurückgegeben wird.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsContainers-rg -Location <REGION>
    ```

    Der Befehl [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) erstellt eine Ressourcengruppe. Im Allgemeinen erstellen Sie Ihre Ressourcengruppe und die Ressourcen in einer Region in Ihrer Nähe, indem Sie eine verfügbare Region verwenden, die vom Cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation) zurückgegeben wird.

    ---

1. Erstellen Sie in Ihrer Ressourcengruppe und Region ein universelles Speicherkonto:

    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location <REGION> --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```

    Der Befehl [az storage account create](/cli/azure/storage/account#az_storage_account_create) erstellt ein Speicherkonto. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsContainers-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location <REGION>
    ```

    Das Cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) erstellt das Speicherkonto.

    ---

    Ersetzen Sie im vorherigen Beispiel `<STORAGE_NAME>` durch einen Namen, der für Sie geeignet und eindeutig in Azure Storage ist. Namen dürfen nur 3 bis 24 Zeichen und ausschließlich Kleinbuchstaben enthalten. Mit `Standard_LRS` wird ein universelles Konto angegeben, das [von Functions unterstützt](storage-considerations.md#storage-account-requirements) wird.
    
1. Verwenden Sie den Befehl, um einen Premium-Plan für Azure Functions mit dem Namen `myPremiumPlan` in der Preisstufe **Elastic Premium 1** (`--sku EP1`) in Ihrer `<REGION>` und in einem Linux-Container (`--is-linux`) zu erstellen.

    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location <REGION> --number-of-workers 1 --sku EP1 --is-linux
    ```
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    ```powershell
    New-AzFunctionAppPlan -ResourceGroupName AzureFunctionsContainers-rg -Name MyPremiumPlan -Location <REGION> -Sku EP1 -WorkerType Linux
    ```
    ---
    Wir verwenden hier den Premium-Plan, der je nach Bedarf skaliert werden kann. Weitere Informationen zum Hosting finden Sie unter [Vergleich von Hostingplänen für Azure Functions](functions-scale.md). Informationen zum Berechnen der Kosten finden Sie auf der Seite [Azure Functions – Preise](https://azure.microsoft.com/pricing/details/functions/).

    Darüber hinaus wird mit dem Befehl auch eine zugeordnete Azure Application Insights-Instanz in derselben Ressourcengruppe bereitgestellt, mit der Sie Ihre Funktions-App überwachen und Protokolle anzeigen können. Weitere Informationen finden Sie unter [Überwachen von Azure Functions](functions-monitoring.md). Für die Instanz fallen erst Kosten an, wenn Sie sie aktivieren.

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>Erstellen und Konfigurieren einer Funktions-App in Azure mit dem Image

Mit einer Funktions-App in Azure wird die Ausführung der Funktionen Ihres Hostingplans verwaltet. In diesem Abschnitt verwenden Sie die Azure-Ressourcen aus dem vorherigen Abschnitt, um eine Funktions-App aus einem Image unter Docker Hub zu erstellen und mit einer Verbindungszeichenfolge für Azure Storage zu konfigurieren.

1. Erstellen Sie mit dem folgenden Befehl eine Funktions-App:

    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
    ```azurecli
    az functionapp create --name <APP_NAME> --storage-account <STORAGE_NAME> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --deployment-container-image-name <DOCKER_ID>/azurefunctionsimage:v1.0.0
    ```

    Im Befehl [az functionapp create](/cli/azure/functionapp#az_functionapp_create) gibt der Parameter *deployment-container-image-name* das für die Funktions-App zu verwendende Image an. Sie können den Befehl [az functionapp config container show](/cli/azure/functionapp/config/container#az_functionapp_config_container_show) verwenden, um Informationen zum für die Bereitstellung verwendeten Image anzuzeigen. Darüber hinaus können Sie den Befehl [az functionapp config container set](/cli/azure/functionapp/config/container#az_functionapp_config_container_set) nutzen, um ein anderes Image für die Bereitstellung zu verwenden.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsContainers-rg -PlanName myPremiumPlan -StorageAccount <STORAGE_NAME> -DockerImageName <DOCKER_ID>/azurefunctionsimage:v1.0.0
    ```
    ---
    
    Ersetzen Sie in diesem Beispiel `<STORAGE_NAME>` durch den Namen, den Sie im vorherigen Abschnitt für das Speicherkonto verwendet haben. Ersetzen Sie außerdem `<APP_NAME>` durch einen für Sie geeigneten global eindeutigen Namen und `<DOCKER_ID>` durch Ihre DockerHub-ID.    
    
    > [!TIP]  
    > Sie können die [`DisableColor`-Einstellung](functions-host-json.md#console) in der host.json-Datei verwenden, um zu verhindern, dass ANSI-Steuerzeichen in die Containerprotokolle geschrieben werden. 

1. Verwenden Sie den folgenden Befehl, um die Verbindungszeichenfolge für das von Ihnen erstellte Speicherkonto abzurufen:

    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <STORAGE_NAME> --query connectionString --output tsv
    ```

    Die Verbindungszeichenfolge für das Speicherkonto wird mit dem Befehl [az storage account show-connection-string](/cli/azure/storage/account) zurückgegeben. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    ```azurepowershell
    $storage_name = "glengagtestdockerstorage"
    $key = (Get-AzStorageAccountKey -ResourceGroupName AzureFunctionsContainers-rg -Name $storage_name)[0].Value
    $string = "DefaultEndpointsProtocol=https;EndpointSuffix=core.windows.net;AccountName=" + $storage_name + ";AccountKey=" + $key
    Write-Output($string) 
    ```
    Der vom Cmdlet [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) zurückgegebene Schlüssel wird verwendet, um die Verbindungszeichenfolge für das Speicherkonto zu erstellen.

    ---    

    Ersetzen Sie `<STORAGE_NAME>` durch den Namen des zuvor erstellten Speicherkontos.

1. Fügen Sie diese Einstellung mit dem folgenden Befehl der Funktions-App hinzu: 
 
    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
    ```azurecli
    az functionapp config appsettings set --name <APP_NAME> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<CONNECTION_STRING>
    ```
    Der Befehl [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az_functionapp_config_ppsettings_set) erstellt die Einstellung. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    ```azurepowershell
    Update-AzFunctionAppSetting -Name <APP_NAME> -ResourceGroupName AzureFunctionsContainers-rg -AppSetting @{"AzureWebJobsStorage"="<CONNECTION_STRING>"}
    ```
    Das Cmdlet [Update-AzFunctionAppSetting](/powershell/module/az.functions/update-azfunctionappsetting) erstellt die Einstellung.

    ---

    Ersetzen Sie in diesem Befehl `<APP_NAME>` durch den Namen Ihrer Funktions-App und `<CONNECTION_STRING>` durch die Verbindungszeichenfolge aus dem vorherigen Schritt. Die Verbindung sollte eine lang codierte Zeichenfolge sein, die mit `DefaultEndpointProtocol=` beginnt.
 

1. Die Funktion kann diese Verbindungszeichenfolge jetzt verwenden, um auf das Speicherkonto zuzugreifen.

> [!NOTE]    
> Wenn Sie Ihr benutzerdefiniertes Image unter einem privaten Containerkonto veröffentlichen, sollten Sie stattdessen Umgebungsvariablen in der Dockerfile für die Verbindungszeichenfolge verwenden. Weitere Informationen finden Sie unter der [ENV-Anweisung](https://docs.docker.com/engine/reference/builder/#env). Sie sollten auch die Variablen `DOCKER_REGISTRY_SERVER_USERNAME` und `DOCKER_REGISTRY_SERVER_PASSWORD` festlegen. Zum Verwenden der Werte müssen Sie das Image dann neu erstellen und per Pushvorgang in die Registrierung übertragen und anschließend die Funktions-App in Azure neu starten.

## <a name="verify-your-functions-on-azure"></a>Überprüfen Ihrer Funktionen in Azure

Nachdem das Image in Ihrer Funktions-App in Azure bereitgestellt wurde, können Sie die Funktion nun wie zuvor über HTTP-Anforderungen aufrufen.
Navigieren Sie in Ihrem Browser zu einer URL wie der folgenden:

::: zone pivot="programming-language-java,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
`https://<APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions`  
::: zone-end  
::: zone pivot="programming-language-csharp"  
# <a name="in-process"></a>[In-Process](#tab/in-process) 
`https://<APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions`
# <a name="isolated-process"></a>[Isolierter Prozess](#tab/isolated-process)
`https://<APP_NAME>.azurewebsites.net/api/HttpExample`

---
:::zone-end  

Ersetzen Sie `<APP_NAME>` durch den Namen der Funktions-App. Wenn Sie zu dieser URL navigieren, sollte der Browser eine ähnliche Ausgabe anzeigen wie bei der lokalen Ausführung der Funktion.

## <a name="enable-continuous-deployment-to-azure"></a>Aktivieren von Continuous Deployment in Azure

Sie können Azure Functions aktivieren, um Ihre Bereitstellung eines Images jeweils automatisch zu aktualisieren, wenn Sie das Bild in der Registrierung aktualisieren.

1. Aktivieren Sie Continuous Deployment, und rufen Sie die Webhook-URL mit den folgenden Befehlen ab:

    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <APP_NAME> --resource-group AzureFunctionsContainers-rg
    ```
    
    Der Befehl [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az_functionapp_deployment_container_config) aktiviert Continuous Deployment und gibt die Webhook-URL der Bereitstellung zurück. Sie können diese URL später jederzeit abrufen, indem Sie den Befehl [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az_functionapp_deployment_container_show_cd_url) verwenden.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    ```azurepowershell
    Update-AzFunctionAppSetting -Name <APP_NAME> -ResourceGroupName AzureFunctionsContainers-rg -AppSetting @{"DOCKER_ENABLE_CI" = "true"}
    Get-AzWebAppContainerContinuousDeploymentUrl -Name <APP_NAME> -ResourceGroupName AzureFunctionsContainers-rg
    ```
    
    Die `DOCKER_ENABLE_CI`-Anwendungseinstellung steuert, ob Continuous Deployment über das Containerrepository aktiviert ist.  Das Cmdlet [Get-AzWebAppContainerContinuousDeploymentUrl](/powershell/module/az.websites/get-azwebappcontainercontinuousdeploymenturl) gibt die URL des Bereitstellungswebhooks zurück.

    ---    

    Ersetzen Sie wie zuvor `<APP_NAME>` durch den Namen Ihrer Funktions-App. 

1. Kopieren Sie die Webhook-URL der Bereitstellung in die Zwischenablage.

1. Öffnen Sie [Docker Hub](https://hub.docker.com/), melden Sie sich an, und wählen Sie in der Navigationsleiste die Option **Repositories** (Repositorys) aus. Suchen Sie nach dem Image, und wählen Sie es aus. Wählen Sie die Registerkarte **Webhooks** aus, geben Sie einen **Webhooknamen** ein, fügen Sie unter **Webhook-URL** Ihre URL ein, und wählen Sie **Erstellen** aus:

    ![Hinzufügen des Webhooks in Ihrem DockerHub-Repository](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. Nachdem der Webhook festgelegt wurde, wird Ihr Image von Azure Functions jeweils erneut bereitgestellt, wenn Sie es in Docker Hub aktualisieren.

## <a name="enable-ssh-connections"></a>Aktivieren von SSH-Verbindungen

SSH ermöglicht die sichere Kommunikation zwischen einem Container und einem Client. Wenn SSH aktiviert ist, können Sie mithilfe der erweiterten App Service-Tools (Kudu) eine Verbindung mit Ihrem Container herstellen. Azure Functions stellt ein Basisimage bereit, für das SSH bereits aktiviert ist, um das Herstellen einer Verbindung mit Ihrem Container per SSH zu vereinfachen. Sie müssen nur Ihre Dockerfile bearbeiten und dann das Image neu erstellen und bereitstellen. Anschließend können Sie mit den Advanced Tools (Kudu) eine Verbindung mit dem Container herstellen.

1. Fügen Sie in Ihrer Dockerfile die Zeichenfolge `-appservice` an das Basisimage in Ihrer `FROM`-Anweisung an:

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end
    
1. Erstellen Sie das Image neu, indem Sie den Befehl `docker build` verwenden und `<docker_id>` durch Ihre Docker-ID ersetzen:

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. Übertragen Sie das aktualisierte Image per Pushvorgang an Docker Hub. Dies sollte deutlich weniger Zeit als der erste Pushvorgang in Anspruch nehmen, da nur die aktualisierten Segmente des Images hochgeladen werden müssen.

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Azure Functions stellt das Image automatisch für Ihre Funktions-App bereit. Der Prozess dauert weniger als eine Minute.

1. Öffnen Sie in einem Browser `https://<app_name>.scm.azurewebsites.net/`, indem Sie `<app_name>` durch Ihren eindeutigen Namen ersetzen. Diese URL ist der Advanced Tools-Endpunkt (Kudu) für Ihren Funktions-App-Container.

1. Melden Sie sich an Ihrem Azure-Konto an, und wählen Sie **SSH** aus, um eine Verbindung mit dem Container herzustellen. Die Verbindungsherstellung kann etwas dauern, falls das Containerimage von Azure noch aktualisiert wird.

1. Nachdem eine Verbindung mit Ihrem Container hergestellt wurde, können Sie den Befehl `top` ausführen, um die derzeit ausgeführten Prozesse anzuzeigen. 

    ![Linux-Befehl „top“, der in einer SSH-Sitzung ausgeführt wird](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="write-to-azure-queue-storage"></a>Schreiben in Azure Queue Storage

Mit Azure Functions können Sie Ihre Funktionen mit anderen Azure-Diensten und -Ressourcen verbinden, ohne dass Sie eigenen Integrationscode schreiben müssen. Diese *Bindungen*, die sowohl Eingabe als auch Ausgabe darstellen, werden innerhalb der Funktionsdefinition deklariert. Daten von Bindungen werden der Funktion als Parameter bereitgestellt. Ein *Trigger* ist ein spezieller Typ von Eingabebindung. Eine Funktion hat zwar nur einen Trigger, kann aber mehrere Ein- und Ausgabebindungen haben. Weitere Informationen finden Sie unter [Konzepte der Trigger und Bindungen in Azure Functions](functions-triggers-bindings.md).

In diesem Abschnitt wird gezeigt, wie Sie Ihre Funktion in eine Azure Queue Storage-Instanz integrieren. Die Ausgabebindung, die Sie dieser Funktion hinzufügen, schreibt Daten aus einer HTTP-Anforderung in eine Nachricht in der Warteschlange.

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]
::: zone-end

::: zone pivot="programming-language-csharp"  
## <a name="register-binding-extensions"></a>Registrieren von Bindungserweiterungen
::: zone-end 

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end  

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="add-code-to-use-the-output-binding"></a>Hinzufügen von Code für die Verwendung der Ausgabebindung

Nachdem die Warteschlangenbindung definiert wurde, können Sie nun Ihre Funktion aktualisieren, um Nachrichten mithilfe des Bindungsparameters in die Warteschlange zu schreiben.
::: zone-end

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
::: zone-end

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
### <a name="update-the-image-in-the-registry"></a>Aktualisieren des Images in der Registrierung

1. Führen Sie im Stammordner erneut `docker build` aus, und aktualisieren Sie die Version im Tag dieses Mal auf `v1.0.1`. Ersetzen Sie `<docker_id>` wie zuvor durch Ihre Docker Hub-Konto-ID:

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1 .
    ```
    
1. Übertragen Sie das aktualisierte Image per `docker push` zurück in das Repository:

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. Da Sie Continuous Delivery konfiguriert haben, wird Ihre Funktions-App beim erneuten Aktualisieren des Images in der Registrierung automatisch in Azure aktualisiert.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Anzeigen der Nachricht in der Azure Storage-Warteschlange

Verwenden Sie in einem Browser dieselbe URL wie zuvor, um Ihre Funktion aufzurufen. Im Browser sollte die gleiche Antwort wie zuvor angezeigt werden, da Sie diesen Teil des Funktionscodes nicht geändert haben. Aufgrund des hinzugefügten Codes wurde aber eine Nachricht in die Speicherwarteschlange `outqueue` geschrieben, indem der URL-Parameter `name` verwendet wurde.

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

::: zone-end

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie mit Azure Functions weiterarbeiten und die in diesem Tutorial erstellten Ressourcen weiterhin nutzen möchten, können Sie sie alle beibehalten. Da Sie einen Premium-Plan für Azure Functions erstellt haben, fallen für Sie pro Tag ein oder zwei US-Dollar an laufenden Kosten an.

Löschen Sie zur Vermeidung laufender Kosten die Ressourcengruppe `AzureFunctionsContainer-rg`, um alle Ressourcen dieser Gruppe zu bereinigen: 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>Nächste Schritte

+ [Überwachen von Funktionen](functions-monitoring.md)
+ [Skalierungs- und Hostingoptionen](functions-scale.md)
+ [Kubernetes-basiertes serverloses Hosting](functions-kubernetes-keda.md)

[Autorisierungsschlüssel]: functions-bindings-http-webhook-trigger.md#authorization-keys