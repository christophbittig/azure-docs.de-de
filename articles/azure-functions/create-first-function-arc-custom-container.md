---
title: 'Schnellstart: Erstellen einer Funktions-App auf Azure Arc in einem benutzerdefinierten Container'
description: Starten Sie mit Azure Functions auf Azure Arc, indem Sie Ihre erste Funktions-App in einem benutzerdefinierten Linux-Container bereitstellen.
ms.topic: quickstart
ms.date: 05/11/2021
ms.openlocfilehash: a22907387a647ca46a5784661324d3f6edcc3594
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129706932"
---
# <a name="create-your-first-function-on-azure-arc-using-a-custom-container-preview"></a>Erstellen Sie Ihre erste Funktion auf Azure Arc unter Verwendung eines benutzerdefinierten Containers (Vorschau)

In diesem Schnellstart erstellen Sie ein Azure Functions-Projekt, das in einem benutzerdefinierten Container ausgeführt wird, und stellen es über Ihr Docker Hub-Konto in einem [Azure Arc-fähigen Kubernetes-Cluster](../azure-arc/kubernetes/overview.md) bereit. Weitere Informationen finden Sie unter [App Service, Funktionen und Logic Apps auf Azure Arc](../app-service/overview-arc-integration.md). Dieses Szenario unterstützt nur Funktions-Apps, die auf Linux ausgeführt werden.   

> [!NOTE]
> Die Unterstützung für die Ausführung von Funktionen auf einem Kubernetes-Cluster mit Azure Arc-Unterstützung befindet sich derzeit in der Vorschau.  

## <a name="prerequisites"></a>Voraussetzungen

Auf Ihrem lokalen Computer:

# <a name="c"></a>[C\#](#tab/csharp)

+ [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download)
+ [Azure Functions Core Tools](functions-run-local.md#v2), Version 3.0.3245 oder höher.
+ [Azure CLI, Version  2.4 oder höher](/cli/azure/install-azure-cli).
+ [Docker](https://docs.docker.com/install/)  
+ [Docker-ID](https://hub.docker.com/signup)

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

+ [Node.js](https://nodejs.org/), Version 12. Node.js, Version 10 wird ebenfalls unterstützt.
+ [Azure Functions Core Tools](functions-run-local.md#v2), Version 3.0.3245 oder höher.
+ [Azure CLI, Version  2.4 oder höher](/cli/azure/install-azure-cli).
+ [Docker](https://docs.docker.com/install/)  
+ [Docker-ID](https://hub.docker.com/signup)

# <a name="python"></a>[Python](#tab/python)

+ [Von Azure Functions unterstützte Python-Versionen](supported-languages.md#languages-by-runtime-version)
+ [Azure Functions Core Tools](functions-run-local.md#v2), Version 3.0.3245 oder höher.
+ [Azure CLI, Version  2.4 oder höher](/cli/azure/install-azure-cli).
+ [Docker](https://docs.docker.com/install/)  
+ [Docker-ID](https://hub.docker.com/signup)

---

[!INCLUDE [functions-arc-create-environment](../../includes/functions-arc-create-environment.md)]

[!INCLUDE [app-service-arc-cli-install-extensions](../../includes/app-service-arc-cli-install-extensions.md)]

## <a name="create-the-local-function-project"></a>Erstellen Sie das lokale Funktions-Projekt

In Azure Functions ist ein Funktionsprojekt der Kontext für eine oder mehrere einzelne Funktionen, die jeweils auf einen bestimmten Auslöser reagieren. Für alle Funktionen eines Projekts werden die gleichen lokalen Konfigurationen und Hostkonfigurationen gemeinsam genutzt. In diesem Abschnitt erstellen Sie ein Funktionsprojekt, das nur eine Funktion enthält.

1. Führen Sie den Befehl `func init` wie folgt aus, um in einem Ordner mit dem Namen *LocalFunctionProj* ein Funktionsprojekt mit der angegebenen Runtime zu erstellen:  

    # <a name="c"></a>[C\#](#tab/csharp)

    ```console
    func init LocalFunctionProj --dotnet --docker
    ```
    # <a name="javascript"></a>[JavaScript](#tab/nodejs)

    ```console
    func init LocalFunctionProj --javascript --docker
    ```

    # <a name="python"></a>[Python](#tab/python)

    Python erfordert eine virtuelle Umgebung, deren Befehle sich zwischen Bash und einer Windows-Befehlszeile unterscheiden.
    
     + Bash: 

        ```bash
        python -m venv .venv
        source .venv/bin/activate
        ```
    
     + Befehlszeile:

        ```cmd
        py -m venv .venv
        .venv\scripts\activate
        ```  

    Nun erstellen Sie das Projekt innerhalb der virtuellen Umgebung. 
    
    ```console
    func init LocalFunctionProj --python --docker
    ```
    ---

    Mit der Option `--docker` wird eine `Dockerfile` für das Projekt generiert. Hiermit wird ein geeigneter benutzerdefinierter Container zur Verwendung mit Azure Functions und der ausgewählten Runtime definiert.

1. Navigieren Sie zum Projektordner:

    ```console
    cd LocalFunctionProj
    ```

    Dieser Ordner enthält die Dockerfile und andere Dateien für das Projekt, z. B. die Konfigurationsdateien [local.settings.json](functions-develop-local.md#local-settings-file) und [host.json](functions-host-json.md). Standardmäßig wird die Datei *local.settings.json* von der Versionskontrolle in der Datei *.gitignore* ausgeschlossen. Dieser Ausschluss ist darauf zurückzuführen, dass die Datei vertrauliche Informationen enthalten kann, die aus Azure heruntergeladen werden.

1. Öffnen Sie die generierte `Dockerfile`, und suchen Sie das `3.0`-Tag für das Basisimage. Wenn ein `3.0`-Tag vorhanden ist, ersetzen Sie es durch ein `3.0.15885`-Tag. In einer JavaScript-Anwendung sollte die Docker-Datei z. B. so geändert werden, dass Sie `FROM mcr.microsoft.com/azure-functions/node:3.0.15885` enthält. Diese Version des Basisimages unterstützt die Bereitstellung in einem Kubernetes-Cluster mit Azure Arc-Unterstützung. 

1. Fügen Sie dem Projekt über den unten gezeigten Befehl eine Funktion hinzu. Hierbei ist das `--name`-Argument der eindeutige Name Ihrer Funktion (HttpExample), mit dem `--template`-Argument wird der Trigger der Funktion (HTTP) angegeben.

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```  

## <a name="build-the-container-image-and-test-locally"></a>Erstellen und lokales Testen des Containerimages

Die Dockerfile-Datei im Projektstamm beschreibt die minimal erforderliche Umgebung zum Ausführen der Funktions-App in einem Container. Die vollständige Liste mit den unterstützten Basisimages für Azure Functions finden Sie auf der [Seite mit Azure Functions-Basisimages](https://hub.docker.com/_/microsoft-azure-functions-base).

1. Führen Sie im Stammprojektordner den Befehl [docker build](https://docs.docker.com/engine/reference/commandline/build/) aus, und geben Sie einen Namen (`azurefunctionsimage`) und ein Tag (`v1.0.0`) an.   

    Der folgende Befehl erstellt das Docker-Image für den Container.

    ```console
    docker build --tag <DOCKER_ID>/azurefunctionsimage:v1.0.0 .
    ```

    Ersetzen Sie in diesem Beispiel `<DOCKER_ID>` mit Ihrer Docker Hub-Konto-ID. Nachdem der Befehl abgeschlossen wurde, können Sie den neuen Container lokal ausführen.
    
1. Um den Build zu testen, führen Sie das Image in einem lokalen Container mit dem Befehl [docker run](https://docs.docker.com/engine/reference/commandline/run/) aus, wobei Sie das Ports-Argument `-p 8080:80` hinzufügen.

    ```console
    docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
    ```

    Ersetzen Sie erneut `<DOCKER_ID` mit Ihrer Docker-ID, und fügen Sie das Ports-Argument `-p 8080:80` hinzu

1. Nachdem das Image in einem lokalen Container ausgeführt wird, navigieren Sie zu `http://localhost:8080/api/HttpExample?name=Functions`, das die gleiche „Hallo“-Meldung wie zuvor anzeigen sollte. Da die HTTP-ausgelöste Funktion eine anonyme Autorisierung verwendet, können Sie die Funktion trotzdem aufrufen, obwohl sie im Container ausgeführt wird. Einstellungen für den Funktionszugriffsschlüssel werden erzwungen, wenn sie lokal in einem Container ausgeführt werden. Wenn beim Aufrufen der Funktion Probleme auftreten, stellen Sie sicher, dass der [Zugriff auf die Funktion](functions-bindings-http-webhook-trigger.md#authorization-keys) auf anonym eingestellt ist.  

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

[!INCLUDE [functions-arc-get-custom-location](../../includes/functions-arc-get-custom-location.md)]

## <a name="create-azure-resources"></a>Erstellen von Azure-Ressourcen 

Bevor Sie Ihren Container in Ihrer neuen App Service Kubernetes-Umgebung einsetzen können, müssen Sie zwei weitere Ressourcen erstellen:

- Ein [Speicherkonto](../storage/common/storage-account-create.md), das derzeit für Tools erforderlich ist und nicht Teil der Umgebung ist.
- Eine Funktions-App, die den Kontext für die Ausführung Ihres Containers bereitstellt. Die Funktions-App wird in der App Service Kubernetes-Umgebung ausgeführt und Ihrem lokalen Funktionsprojekt zugeordnet. Mit einer Funktions-App können Sie Funktionen in logischen Einheiten gruppieren. Dies erleichtert die Verwaltung, Bereitstellung und Freigabe von Ressourcen.

> [!NOTE]
> Funktions-Apps werden in einer App Service Kubernetes-Umgebung mit einem dedizierten Plan (App Service) ausgeführt. Wenn Sie Ihre Funktions-App ohne vorhandenen Plan erstellen, wird der korrekte Plan für Sie erstellt.  

### <a name="create-storage-account"></a>Speicherkonto erstellen

Verwenden Sie den Befehl [az storage account create](/cli/azure/storage/account#az_storage_account_create), um ein Allzweck-Speicherkonto in Ihrer Ressourcengruppe und Region zu erstellen:

```azurecli
az storage account create --name <STORAGE_NAME> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```

> [!NOTE]  
> Für Azure Functions Tooling ist derzeit ein Speicherkonto erforderlich. 

Ersetzen Sie im vorherigen Beispiel `<STORAGE_NAME>` durch einen Namen, der für Sie geeignet und eindeutig in Azure Storage ist. Namen dürfen nur 3 bis 24 Zeichen und ausschließlich Kleinbuchstaben enthalten. Mit `Standard_LRS` wird ein universelles Konto angegeben, das [von Functions unterstützt](storage-considerations.md#storage-account-requirements) wird. Der `--location`-Wert ist eine Azure-Standardregion. 

### <a name="create-the-function-app"></a>Erstellen der Funktionen-App

Führen Sie den Befehl [az functionapp create](/cli/azure/functionapp#az_functionapp_create) aus, um eine neue Funktions-App in der Umgebung zu erstellen.

# <a name="c"></a>[C\#](#tab/csharp)  
```azurecli
az functionapp create --resource-group MyResourceGroup --name <APP_NAME> --custom-location <CUSTOM_LOCATION_ID> --storage-account <STORAGE_NAME> --functions-version 3 --runtime dotnet --deployment-container-image-name <DOCKER_ID>/azurefunctionsimage:v1.0.0
```

# <a name="javascript"></a>[JavaScript](#tab/nodejs)  
```azurecli
az functionapp create --resource-group MyResourceGroup --name <APP_NAME> --custom-location <CUSTOM_LOCATION_ID> --storage-account <STORAGE_NAME> --functions-version 3 --runtime node --runtime-version 12 --deployment-container-image-name <DOCKER_ID>/azurefunctionsimage:v1.0.0
```

# <a name="python"></a>[Python](#tab/python)  
```azurecli
az functionapp create --resource-group MyResourceGroup --name <APP_NAME> --custom-location <CUSTOM_LOCATION_ID> --storage-account <STORAGE_NAME> --functions-version 3 --runtime python --runtime-version 3.8 --deployment-container-image-name <DOCKER_ID>/azurefunctionsimage:v1.0.0
```
---

Ersetzen Sie in diesem Beispiel `<CUSTOM_LOCATION_ID>` durch die ID des benutzerdefinierten Speicherorts, den Sie für die App Service Kubernetes-Umgebung definiert haben. Ersetzen Sie außerdem `<STORAGE_NAME>` mit dem Namen des Kontos, das Sie im vorherigen Schritt verwendet haben, `<APP_NAME>` mit einem für Sie passenden, global-eindeutigen Namen und `<DOCKER_ID>` mit Ihrer Docker Hub ID. 

Mit dem Parameter *deployment-container-image-name* wird das Image angegeben, das für die Funktions-App verwendet wird. Sie können den Befehl [az functionapp config container show](/cli/azure/functionapp/config/container#az_functionapp_config_container_show) verwenden, um Informationen zum für die Bereitstellung verwendeten Image anzuzeigen. Darüber hinaus können Sie den Befehl [az functionapp config container set](/cli/azure/functionapp/config/container#az_functionapp_config_container_set) nutzen, um ein anderes Image für die Bereitstellung zu verwenden.

Wenn Sie die Funktions-App zum ersten Mal erstellen, zieht sie das anfängliche Image aus Ihrem Docker Hub. Sie können die [kontinuierliche Bereitstellung in Azure](functions-create-function-linux-custom-image.md#enable-continuous-deployment-to-azure) auch über Docker Hub aktivieren.  

Informationen zum Aktivieren von SSH im Image finden Sie unter [Aktivieren von SSH-Verbindungen](functions-create-function-linux-custom-image.md#enable-ssh-connections).

### <a name="set-required-app-settings"></a>Festlegen der erforderlichen App-Einstellungen

Führen Sie die folgenden Befehle aus, um eine App-Einstellung für die Verbindungszeichenfolge des Speicherkontos zu erstellen:

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <STORAGE_NAME> --query connectionString --output tsv)
az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=$storageConnectionString
```

Dieser Code muss entweder in der Cloud Shell oder in Bash auf Ihrem lokalen Computer ausgeführt werden. Ersetzen `<STORAGE_NAME>` Sie mit dem Namen des Speicherkontos und `<APP_NAME>` mit dem Namen der Funktions-App.  

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

## <a name="next-steps"></a>Nächste Schritte

Ihre Funktions-App wird nun in einem Container und einer App Service Kubernetes-Umgebung mit Azure Arc-Unterstützung ausgeführt. Sie können diese jetzt mit Azure Storage verbinden, indem Sie eine Ausgangsbindung für Queue Storage hinzufügen.

# <a name="c"></a>[C\#](#tab/csharp)  

> [!div class="nextstepaction"]
> [Herstellen einer Verbindung mit einer Azure Storage-Warteschlange](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp)

# <a name="javascript"></a>[JavaScript](#tab/nodejs)  

> [!div class="nextstepaction"]
> [Herstellen einer Verbindung mit einer Azure Storage-Warteschlange](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-javascript)

# <a name="python"></a>[Python](#tab/python)  

> [!div class="nextstepaction"]
> [Herstellen einer Verbindung mit einer Azure Storage-Warteschlange](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-python)

---
