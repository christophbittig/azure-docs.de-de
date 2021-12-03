---
title: 'Tutorial: Verwenden einer verwalteten Identität zum Aufrufen von Azure Functions'
description: Verwenden einer verwalteten Identität zum Aufrufen von Azure Functions über eine Azure Spring Cloud-App
author: karlerickson
ms.author: margard
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/10/2020
ms.openlocfilehash: e246fa6c20e506952001dff59d3a2f0a9eccc8d1
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491600"
---
# <a name="tutorial-use-a-managed-identity-to-invoke-azure-functions-from-an-azure-spring-cloud-app"></a>Tutorial: Verwenden einer verwalteten Identität zum Aufrufen von Azure Functions über eine Azure Spring Cloud-App

In diesem Artikel erfahren Sie, wie Sie eine verwaltete Identität für eine Azure Spring Cloud-App erstellen und damit über HTTP ausgelöste Funktionen aufrufen.

Die Unterstützung der Azure AD-Authentifizierung (Azure Active Directory) ist sowohl in Azure Functions als auch in App Services integriert. Die Verwendung dieser integrierten Authentifizierungsfunktion in Kombination mit verwalteten Identitäten für Azure Spring Cloud ermöglicht das Aufrufen von RESTful-Diensten mit moderner OAuth-Semantik. Diese Methode erfordert keine Speicherung von Geheimnissen im Code und ermöglicht eine präzisere Steuerung des Zugriffs auf externe Ressourcen.

## <a name="prerequisites"></a>Voraussetzungen

* [Registrierung für ein Azure-Abonnement](https://azure.microsoft.com/free/)
* [Installation der Azure CLI, Version 2.0.67 oder höher](/cli/azure/install-azure-cli)
* [Installation von Maven 3.0 oder höher](https://maven.apache.org/download.cgi)
* [Installation von Azure Functions Core Tools, Version 3.0.2009 oder höher](../azure-functions/functions-run-local.md#install-the-azure-functions-core-tools)

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Erstellen Sie mithilfe des Befehls [az group create](/cli/azure/group#az_group_create) eine einzelne Ressourcengruppe für die Funktions-App und für Spring Cloud:

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-function-app"></a>Erstellen einer Funktions-App

Um eine Funktions-App erstellen zu können, müssen Sie zunächst ein unterstützendes Speicherkonto erstellen. Verwenden Sie dazu den Befehl [az storage account create](/cli/azure/storage/account#az_storage_account_create):

> [!Important]
> Jede Funktions-App und jedes Speicherkonto muss über einen eindeutigen Namen verfügen. Ersetzen Sie in den folgenden Beispielen *\<your-functionapp-name>* durch den Namen Ihrer Funktions-App und *\<your-storageaccount-name>* durch den Namen Ihres Speicherkontos.

```azurecli
az storage account create --name <your-storageaccount-name> --resource-group myResourceGroup --location eastus --sku Standard_LRS
```

Nachdem Sie das Speicherkonto erstellt haben, können Sie die Funktions-App erstellen.

```azurecli
az functionapp create --name <your-functionapp-name> --resource-group myResourceGroup --consumption-plan-location eastus --os-type windows --runtime node --storage-account <your-storageaccount-name> --functions-version 3
```

Notieren Sie sich die zurückgegebenen Hostnamen (**hostNames**). Diese sind im Format *https://\<your-functionapp-name>.azurewebsites.net* angegeben. Der Hostname wird in einem späteren Schritt benötigt.

## <a name="enable-azure-active-directory-authentication"></a>Aktivieren der Azure Active Directory-Authentifizierung

Greifen Sie über das [Azure-Portal](https://portal.azure.com) auf die neu erstellte Funktions-App zu, und wählen Sie im Einstellungsmenü die Option „Authentifizierung/Autorisierung“ aus. Aktivieren Sie die App Service-Authentifizierung, und legen Sie „Die auszuführende Aktion, wenn die Anforderung nicht authentifiziert ist“ auf „Mit Azure Active Directory anmelden“ fest. Diese Einstellung sorgt dafür, dass alle nicht authentifizierten Anforderungen abgelehnt werden (401-Antwort).

![Authentifizierungseinstellungen mit Azure Active Directory als Standardanbieter](media/spring-cloud-tutorial-managed-identities-functions/function-auth-config-1.jpg)

Wählen Sie unter „Authentifizierungsanbieter“ die Option „Azure Active Directory“ aus, um die Anwendungsregistrierung zu konfigurieren. Wenn Sie den Verwaltungsmodus „Express“ auswählen, wird in Ihrem Azure AD-Mandanten automatisch eine Anwendungsregistrierung mit der korrekten Konfiguration erstellt.

![Auf den Verwaltungsmodus „Express“ festgelegter Azure Active Directory-Anbieter](media/spring-cloud-tutorial-managed-identities-functions/function-auth-config-2.jpg)

Nachdem Sie die Einstellungen gespeichert haben, wird die Funktions-App neu gestartet, und bei allen nachfolgenden Anforderungen wird eine Anmeldung über Azure AD angefordert. Sie können testen, ob nicht authentifizierte Anforderungen nun abgelehnt werden, indem Sie zur Stamm-URL der Funktions-App navigieren. (Diese wurde im obigen Schritt in der Ausgabe **hostNames** zurückgegeben.) Daraufhin sollten Sie zum Azure AD-Anmeldebildschirm Ihrer Organisation umgeleitet werden.

## <a name="create-an-http-triggered-function"></a>Erstellen einer über HTTP ausgelösten Funktion

Erstellen Sie in einem leeren lokalen Verzeichnis eine neue Funktions-App, und fügen Sie eine über HTTP ausgelöste Funktion hinzu.

```console
func init --worker-runtime node
func new --template HttpTrigger --name HttpTrigger
```

Von Functions wird zum Schutz von HTTP-Endpunkten standardmäßig eine schlüsselbasierte Authentifizierung verwendet. Da hier die Azure AD-Authentifizierung aktiviert wird, um den Zugriff auf Functions zu schützen, müssen wir [die Funktionsauthentifizierungsebene auf „Anonym“ festlegen](../azure-functions/functions-bindings-http-webhook-trigger.md#secure-an-http-endpoint-in-production) (in der Datei *function.json*).

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      ...
    }
  ]
}
```

Die App kann jetzt in der [Funktions-App-Instanz](#create-a-function-app) veröffentlicht werden, die im vorherigen Schritt erstellt wurde.

```console
func azure functionapp publish <your-functionapp-name>
```

Die Ausgabe des Veröffentlichungsbefehls sollte die URL Ihrer neu erstellten Funktion enthalten.

```output
Deployment completed successfully.
Syncing triggers...
Functions in <your-functionapp-name>:
    HttpTrigger - [httpTrigger]
        Invoke url: https://<your-functionapp-name>.azurewebsites.net/api/httptrigger
```

## <a name="create-azure-spring-cloud-service-and-app"></a>Erstellen eines Azure Spring Cloud-Diensts und einer -App

Erstellen Sie nach der Installation der Spring Cloud-Erweiterung mithilfe des Azure CLI-Befehls `az spring-cloud create` eine Azure Spring Cloud-Instanz.

```azurecli
az extension add --name spring-cloud
az spring-cloud create --name mymsispringcloud --resource-group myResourceGroup --location eastus
```

Im folgenden Beispiel wird eine App namens `msiapp` mit einer vom System zugewiesenen verwalteten Identität erstellt. Dies wird vom Parameter `--assign-identity` angefordert.

```azurecli
az spring-cloud app create --name "msiapp" --service "mymsispringcloud" --resource-group "myResourceGroup" --assign-endpoint true --assign-identity
```

## <a name="build-sample-spring-boot-app-to-invoke-the-function"></a>Erstellen einer Spring Boot-Beispiel-App zum Aufrufen der Funktion

In diesem Beispiel wird die über HTTP ausgelöste Funktion aufgerufen. Hierzu wird zunächst ein Zugriffstoken vom [MSI-Endpunkt](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md#get-a-token-using-http) angefordert und dieses Token dann zum Authentifizieren der HTTP-Anforderung der Funktion verwendet.

1. Klonen Sie das Beispielprojekt.

    ```bash
    git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
    ```

2. Geben Sie Ihren Funktions-URI und den Triggernamen in Ihren App-Eigenschaften an.

    ```bash
    cd Azure-Spring-Cloud-Samples/managed-identity-function
    vim src/main/resources/application.properties
    ```

    Um die verwaltete Identität für Azure Spring Cloud-Apps zu verwenden, fügen Sie *src/main/resources/application.properties* Eigenschaften mit dem folgenden Inhalt hinzu.

    ```properties
    azure.function.uri=https://<your-functionapp-name>.azurewebsites.net
    azure.function.triggerPath=httptrigger
    ```

3. Erstellen Sie eine Paket aus Ihrer Beispiel-App.

    ```bash
    mvn clean package
    ```

4. Stellen Sie die App nun mit dem Azure CLI-Befehl `az spring-cloud app deploy` in Azure bereit.

    ```azurecli
    az spring-cloud app deploy  --name "msiapp" --service "mymsispringcloud" --resource-group "myResourceGroup" --jar-path target/sc-managed-identity-function-sample-0.1.0.jar
    ```

5. Greifen Sie auf den öffentlichen Endpunkt oder den Testendpunkt zu, um Ihre App zu testen.

    ```bash
    curl https://mymsispringcloud-msiapp.azuremicroservices.io/func/springcloud
    ```

    Im Antworttext wird die folgende Nachricht zurückgegeben:

    ```output
    Function Response: Hello, springcloud. This HTTP triggered function executed successfully.
    ```

    Sie können verschiedene Werte an die Funktion übergeben, indem Sie den Pfadparameter ändern.

## <a name="next-steps"></a>Nächste Schritte

* [Aktivieren der systemseitig zugewiesenen verwalteten Identität für Anwendungen in Azure Spring Cloud](./how-to-enable-system-assigned-managed-identity.md)
* [Weitere Informationen zu verwalteten Identitäten für Azure-Ressourcen](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Konfigurieren von Client-Apps für den Zugriff auf App Service](../app-service/configure-authentication-provider-aad.md#configure-client-apps-to-access-your-app-service)
