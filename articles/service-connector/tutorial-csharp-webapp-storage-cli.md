---
title: 'Tutorial: Bereitstellen einer Webanwendung, die mit Azure Storage Blob verbunden ist, mithilfe des Dienstconnectors'
description: Erstellen Sie mithilfe des Dienstconnectors eine Webanwendung, die mit Azure Storage Blob verbunden ist.
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: tutorial
ms.date: 10/28/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8768d188ae56896fe1f7272ee18533e95343e69c
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846556"
---
# <a name="tutorial-deploy-web-application-connected-to-azure-storage-blob-with-service-connector"></a>Tutorial: Bereitstellen einer Webanwendung, die mit Azure Storage Blob verbunden ist, mithilfe des Dienstconnectors

Es wird beschrieben, wie Sie für eine Web-App (kein angemeldeter Benutzer), die in Azure App Service ausgeführt wird, mit verwalteten Identitäten auf Azure-Speicher zugreifen. In diesem Tutorial wird die Azure CLI verwendet, um folgende Aufgaben auszuführen:

> [!div class="checklist"]
> * Einrichten der anfänglichen Umgebung mit der Azure-Befehlszeilenschnittstelle
> * Erstellen eines Speicherkontos und eines Azure Blob Storage-Containers
> * Bereitstellen von Code für Azure App Service und Herstellen einer Verbindung mit dem Speicher mithilfe einer verwalteten Identität und des Dienstconnectors

## <a name="1-set-up-your-initial-environment"></a>1. Einrichten der anfänglichen Umgebung

1. Sie benötigen ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
2. Installieren Sie die <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.30.0 oder höher, mit der Sie Befehle in einer beliebigen Shell ausführen, um Azure-Ressourcen bereitzustellen und zu konfigurieren.


Vergewissern Sie sich, dass Sie mindestens Version 2.30.0 der Azure CLI verwenden:

```Azure CLI
az --version
```

Wenn Sie ein Upgrade durchführen müssen, versuchen Sie es mit dem Befehl `az upgrade` (erfordert mindestens Version 2.11), oder lesen Sie die Informationen unter <a href="/cli/azure/install-azure-cli" target="_blank">Installieren der Azure CLI</a>.

Melden Sie sich anschließend über die CLI bei Azure an:

```Azure CLI
az login
```

Dieser Befehl öffnet einen Browser zum Erfassen Ihrer Anmeldeinformationen. Wenn der Befehl abgeschlossen ist, wird eine JSON-Ausgabe mit Informationen zu Ihren Abonnements angezeigt.

Nachdem Sie sich angemeldet haben, können Sie Azure-Befehle mit der Azure CLI ausführen, um Ressourcen in Ihrem Abonnement zu verwenden.

## <a name="2-clone-or-download-the-sample-app"></a>2. Klonen oder Herunterladen der Beispiel-App

Klonen Sie das Beispielrepository:
```Bash
git clone https://github.com/Azure-Samples/serviceconnector-webapp-storageblob-dotnet.git
```

Und wechseln Sie zum Stammverzeichnis des Repositorys:
```Bash
cd WebAppStorageMISample
```

## <a name="3-create-the-app-service-app"></a>3. Erstellen der App Service-App

Vergewissern Sie sich im Terminal, dass Sie sich im Repositoryordner *WebAppStorageMISample* befinden, der den App-Code enthält.

Erstellen Sie mithilfe des Befehls [`az webapp up`](/cli/azure/webapp#az_webapp_up) eine App Service-App (den Hostprozess):

```Azure CLI
az webapp up --name <app-name> --sku B1 --location eastus --resource-group ServiceConnector-tutorial-rg
```

- Achten Sie beim `--location`-Argument darauf, einen Standort zu verwenden, der vom [Dienstconnector unterstützt](concept-region-support.md) wird.
- **Ersetzen** Sie *\<app-name>* durch einen innerhalb von Azure eindeutigen Namen. (Der Serverendpunkt ist `https://<app-name>.azurewebsites.net`.) Zulässige Zeichen für *\<app-name>* sind `A`-`Z`, `0`-`9` und `-`. Ein bewährtes Muster ist eine Kombination aus Ihrem Firmennamen und einer App-ID.

## <a name="4-create-a-storage-account-and-blob-storage-container"></a>4. Erstellen eines Speicherkontos und Blob Storage-Containers

Führen Sie im Terminal den folgenden Befehl aus, um ein Speicherkonto vom Typ „Universell V2“ und einen Blob Storage-Container zu erstellen. **Ersetzen** Sie *\<storage-name>* durch einen eindeutigen Namen. Der Containername muss klein geschrieben werden, mit einem Buchstaben oder einer Zahl beginnen und darf nur Buchstaben, Zahlen und Bindestriche (-) enthalten.

```Azure CLI
az storage account create --name <storage-name> --resource-group ServiceConnector-tutorial-rg --sku Standard_RAGRS --https-only
```


## <a name="5-connect-app-service-app-to-blob-storage-container-with-managed-identity"></a>5. Verbinden der App Service-App mit einem Blob Storage-Container mithilfe einer verwalteten Identität

Führen Sie im Terminal den folgenden Befehl aus, um Ihre Web-App mithilfe einer verwalteten Identität mit Blob Storage zu verbinden.

```Azure CLI
az webapp connection create storage-blob -g ServiceConnector-tutorial-rg -n <app-name> --tg ServiceConnector-tutorial-rg --account <storage-name> --system-identity
```

- **Ersetzen** Sie *\<app-name>* durch den Namen Ihrer Web-App, die Sie in Schritt 3 verwendet haben.
- **Ersetzen** Sie *\<storage-name>* durch den Namen Ihrer Speicher-App, die Sie in Schritt 4 verwendet haben.

> [!NOTE]
> Wird die Fehlermeldung „Das Abonnement ist nicht für die Verwendung des Ressourcenanbieters "{0}" registriert.“ angezeigt, führen Sie `az provider register -n Microsoft.ServiceLinker` aus, um den Dienstconnector-Ressourcenanbieter zu registrieren. Führen Sie anschließend erneut den Verbindungsbefehl aus. 

## <a name="6-run-sample-code"></a>6. Ausführen des Beispielcodes

Führen Sie im Terminal den folgenden Befehl aus, um die Beispielanwendung in Ihrem Browser zu öffnen. Ersetzen Sie *\<app-name>* durch den Namen Ihrer Web-App, die Sie in Schritt 3 verwendet haben.

```Azure CLI
az webapp browse --name <app-name> 
```

Der Beispielcode ist eine Webanwendung. Jedes Mal, wenn Sie die Indexseite aktualisieren, wird ein Blob mit dem Text `Hello Service Connector! Current is {UTC Time Now}` im Speichercontainer erstellt oder aktualisiert und dann zurückgelesen, um es auf der Indexseite anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte

Befolgen Sie die unten aufgeführten Tutorials, um mehr über Service Connector zu erfahren.

> [!div class="nextstepaction"]
> [Informationen zu Service Connector-Konzepten](./concept-service-connector-internals.md)
