---
title: Alternativen beim Selbsthosten eines Entwicklerportals
titleSuffix: Azure API Management
description: Hier finden Sie Informationen zu alternativen Ansätzen, die Sie verwenden können, wenn Sie ein Entwicklerportal in Azure API Management selbst hosten.
author: dlepow
ms.author: danlep
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: fb8514423bf4efd42373ad2f111aa3a96c5c0969
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128582895"
---
# <a name="alternative-approaches-to-self-host-developer-portal"></a>Alternative Ansätze beim Selbsthosten eines Entwicklerportals

Es gibt mehrere alternative Ansätze, die ggf. für Sie in Frage kommen, wenn Sie [ein Entwicklerportal selbst hosten](developer-portal-self-host.md):

* Verwenden von Produktionsbuilds des Designers und des Herausgebers

* Verwenden einer Azure-Funktions-App, um Ihr Portal zu veröffentlichen

* Bereitstellen der Dateien Ihres Portals mit einem Content Delivery Network (CDN), um die Seitenladezeiten zu verringern

Dieser Artikel enthält Informationen zu jedem dieser Ansätze. 

Richten Sie bei Bedarf eine [lokale Umgebung](developer-portal-self-host.md#step-1-set-up-local-environment) für das neueste Release des Entwicklerportals ein.

## <a name="build-for-production"></a>Erstellen für die Produktion

Wenn Sie die Entwicklungsumgebung des Portals online hosten möchten, um Zusammenarbeit zu ermöglichen, verwenden Sie Produktionsbuilds des Designers und des Herausgebers. In Produktionsbuilds werden unter anderem die Dateien gebündelt und Sourcemaps ausgeschlossen.

Führen Sie den folgenden Befehl aus, um ein Paket im Verzeichnis `./dist/designer` zu erstellen:

```sh
npm run build-designer
```

Das Ergebnis ist eine Single-Page-Webanwendung, die weiterhin für einen statischen Webhost wie etwa die statische Azure Blob Storage-Website bereitgestellt werden kann.

Platzieren Sie analog dazu einen kompilierten und optimierten Herausgeber im Ordner `./dist/publisher`:

```sh
npm run build-publisher
```

## <a name="use-function-app-to-publish-the-portal"></a>Verwenden einer Funktions-App zum Veröffentlichen des Portals

Führen Sie alternativ zur lokalen Ausführung den Veröffentlichungsschritt in der Cloud aus.

Wenn Sie die Veröffentlichung mit einer Azure-Funktions-App implementieren möchten, ist Folgendes erforderlich:

- [Erstellen Sie eine Azure-Funktion.](../azure-functions/functions-get-started.md) Bei der Funktion muss es sich um eine JavaScript-Sprachfunktion handeln.
- Installieren Sie Azure Functions Core Tools:
    ```console
    npm install –g azure-function-core-tools
    ```

### <a name="step-1-configure-output-storage"></a>Schritt 1: Konfigurieren des Ausgabespeichers

Laden Sie den Inhalt direkt an die Hostingwebsite (Container „$web“ des Ausgabespeichers) hoch anstatt in den lokalen Ordner. Konfigurieren Sie diese Änderung in der Datei `./src/config.publish.json`:

```json
{
   ...
   "outputBlobStorageContainer": "$web",
   "outputBlobStorageConnectionString": "DefaultEndpointsProtocol=...",
   ...
}
```

### <a name="step-2-build-and-deploy-the-function-app"></a>Schritt 2: Erstellen und Bereitstellen der Funktions-App

Der Ordner `./examples` enthält eine exemplarische HTTP-Triggerfunktion. Führen Sie den folgenden Befehl aus, um sie zu erstellen und in `./dist/function` zu platzieren:

```sh
npm run build-function
```

Melden Sie sich dann bei der Azure CLI an, und stellen Sie die Funktion bereit:

```azurecli
az login
cd ./dist/function
func azure functionapp publish <function app name>
```

Nach der Bereitstellung können Sie sie mit einem HTTP-Aufruf aufrufen:

```sh
curl -X POST https://<function app name>.azurewebsites.net/api/publish
```

## <a name="hosting-and-cdn"></a>Hosting und CDN

Im Artikel [Selbsthosten des API Management-Entwicklerportals](developer-portal-self-host.md) wurde das Hosten Ihrer Website unter Verwendung eines Azure-Speicherkontos empfohlen. Die Dateien können jedoch über eine beliebige Lösung veröffentlicht werden. Dies schließt Dienste von Hostinganbietern mit ein.

Die Dateien können auch mit einem Content Delivery Network (CDN) bereitgestellt werden, um die Seitenladezeiten zu verringern. Wir empfehlen [Azure CDN](https://azure.microsoft.com/services/cdn/).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Entwicklerportal:

- [Übersicht über das Azure API Management-Entwicklerportal](api-management-howto-developer-portal.md)
