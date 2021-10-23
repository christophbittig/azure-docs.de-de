---
title: 'Schnellstart: Erstellen einer Funktions-App auf Azure Arc'
description: Beginnen Sie mit Azure Functions auf Azure Arc, indem Sie Ihre erste Funktions-App implementieren.
ms.topic: quickstart
ms.date: 05/10/2021
ms.openlocfilehash: 4fd5a7a2792e6e90748c60336d92d5ed80388267
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129706994"
---
# <a name="create-your-first-function-on-azure-arc-preview"></a>Erstellen Sie Ihre erste Funktion auf Azure Arc (Vorschau)

In diesem Quickstart erstellen Sie ein Azure Functions-Projekt und stellen es für eine Funktions-App bereit, die auf einem [Azure Arc-fähigen Kubernetes-Cluster ausgeführt wird](../azure-arc/kubernetes/overview.md). Weitere Informationen finden Sie unter [App Service, Funktionen und Logic Apps auf Azure Arc](../app-service/overview-arc-integration.md). Dieses Szenario unterstützt nur Funktions-Apps, die auf Linux ausgeführt werden.   

> [!NOTE]
> Die Unterstützung für die Ausführung von Funktionen auf einem Kubernetes-Cluster mit Azure Arc-Unterstützung befindet sich derzeit in der Vorschau.  
>  
> Das Veröffentlichen von PowerShell-Funktionsprojekten in Azure Arc-fähigen Kubernetes-Clustern wird derzeit nicht unterstützt. Wenn Sie PowerShell-Funktionen in Azure Arc-fähigen Kubernetes-Clustern bereitstellen müssen, [erstellen Sie Ihre Funktions-App in einem Container](create-first-function-arc-custom-container.md). 

## <a name="prerequisites"></a>Voraussetzungen

Auf Ihrem lokalen Computer:

# <a name="c"></a>[C\#](#tab/csharp)

+ [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download)
+ [Azure Functions Core Tools](functions-run-local.md#v2), Version 3.0.3245 oder höher.
+ [Azure CLI, Version  2.4 oder höher](/cli/azure/install-azure-cli).

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

+ [Node.js](https://nodejs.org/), Version 12. Node.js, Version 10 wird ebenfalls unterstützt.
+ [Azure Functions Core Tools](functions-run-local.md#v2), Version 3.0.3245 oder höher.
+ [Azure CLI, Version  2.4 oder höher](/cli/azure/install-azure-cli).

# <a name="python"></a>[Python](#tab/python)

+ [Von Azure Functions unterstützte Python-Versionen](supported-languages.md#languages-by-runtime-version)
+ [Azure Functions Core Tools](functions-run-local.md#v2), Version 3.0.3245 oder höher.
+ [Azure CLI, Version  2.4 oder höher](/cli/azure/install-azure-cli).

---

[!INCLUDE [functions-arc-create-environment](../../includes/functions-arc-create-environment.md)]

[!INCLUDE [app-service-arc-cli-install-extensions](../../includes/app-service-arc-cli-install-extensions.md)]

## <a name="create-the-local-function-project"></a>Erstellen Sie das lokale Funktions-App-Projekt

In Azure Functions ist ein Funktionsprojekt die Einheit für die Bereitstellung und Ausführung einer oder mehrerer einzelner Funktionen, die jeweils auf einen bestimmten Auslöser reagieren. Für alle Funktionen eines Projekts werden die gleichen lokalen Konfigurationen und Hostkonfigurationen gemeinsam genutzt. In diesem Abschnitt erstellen Sie ein Funktionsprojekt, das nur eine Funktion enthält.

1. Führen Sie den Befehl `func init` wie folgt aus, um in einem Ordner mit dem Namen *LocalFunctionProj* ein Funktionsprojekt mit der angegebenen Runtime zu erstellen:  

    # <a name="c"></a>[C\#](#tab/csharp)

    ```console
    func init LocalFunctionProj --dotnet
    ```
    # <a name="javascript"></a>[JavaScript](#tab/nodejs)

    ```console
    func init LocalFunctionProj --javascript
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

    Nun erstellen Sie das Projekt in der virtuellen Umgebung. 
    
    ```console
    func init LocalFunctionProj --python
    ```

    ---

1. Navigieren Sie zum Projektordner:

    ```console
    cd LocalFunctionProj
    ```

    Dieser Ordner enthält verschiedene Dateien für das Projekt, z. B. die Konfigurationsdateien [local.settings.json](functions-develop-local.md#local-settings-file) und [host.json](functions-host-json.md). Standardmäßig wird die Datei *local.settings.json* von der Versionskontrolle in der Datei *.gitignore* ausgeschlossen. Dieser Ausschluss ist darauf zurückzuführen, dass die Datei vertrauliche Informationen enthalten kann, die aus Azure heruntergeladen werden.

1. Fügen Sie dem Projekt über den unten gezeigten Befehl eine Funktion hinzu. Hierbei ist das `--name`-Argument der eindeutige Name Ihrer Funktion (HttpExample), mit dem `--template`-Argument wird der Trigger der Funktion (HTTP) angegeben.

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```  
[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-arc-get-custom-location](../../includes/functions-arc-get-custom-location.md)]

## <a name="create-azure-resources"></a>Erstellen von Azure-Ressourcen 

Bevor Sie Ihren Funktionscode in Ihrer neuen App Service Kubernetes-Umgebung bereitstellen können, müssen Sie zwei weitere Ressourcen erstellen:

- Ein [Speicherkonto](../storage/common/storage-account-create.md), das derzeit für Tools erforderlich ist und nicht Teil der Umgebung ist.
- Eine Funktions-App, die den Kontext für die Ausführung Ihres Funktionscodes bereitstellt. Die Funktions-App wird in der App Service Kubernetes-Umgebung ausgeführt und Ihrem lokalen Funktionsprojekt zugeordnet. Mit einer Funktions-App können Sie Funktionen in logischen Einheiten gruppieren. Dies erleichtert die Verwaltung, Bereitstellung und Freigabe von Ressourcen.

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
az functionapp create --resource-group MyResourceGroup --name <APP_NAME> --custom-location <CUSTOM_LOCATION_ID> --storage-account <STORAGE_NAME> --functions-version 3 --runtime dotnet 
```

# <a name="javascript"></a>[JavaScript](#tab/nodejs)  
```azurecli
az functionapp create --resource-group MyResourceGroup --name <APP_NAME> --custom-location <CUSTOM_LOCATION_ID> --storage-account <STORAGE_NAME> --functions-version 3 --runtime node --runtime-version 12
```

# <a name="python"></a>[Python](#tab/python)  
```azurecli
az functionapp create --resource-group MyResourceGroup --name <APP_NAME> --custom-location <CUSTOM_LOCATION_ID> --storage-account <STORAGE_NAME> --functions-version 3 --runtime python --runtime-version 3.8
```
---

Ersetzen Sie in diesem Beispiel `<CUSTOM_LOCATION_ID>` durch die ID des benutzerdefinierten Speicherorts, den Sie für die App Service Kubernetes-Umgebung definiert haben. Ersetzen Sie auch im vorherigen Beispiel `<STORAGE_NAME>` durch den Namen des Kontos, das Sie im vorherigen Schritt verwendet haben, und ersetzen Sie `<APP_NAME>` mit einem global eindeutigen Namen, der für Sie geeignet ist. 

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

Da es einige Zeit dauern kann, bis eine vollständige Bereitstellung in einem Azure Arc-fähigen Kubernetes-Cluster abgeschlossen ist, sollten Sie den folgenden Befehl erneut ausführen, um Ihre veröffentlichten Funktionen zu überprüfen:

```command
func azure functionapp list-functions
``` 

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
