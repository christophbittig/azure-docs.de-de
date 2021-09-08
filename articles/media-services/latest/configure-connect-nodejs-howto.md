---
title: Verbinden mit der Azure Media Services v3-API – Node.js
description: In diesem Artikel wird demonstriert, wie Sie mit Node.js eine Verbindung mit der Media Services v3-API herstellen.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/17/2021
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: 7c6bba2084247d76a79de9ae9efb1b3ea356a5c4
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221498"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Verbinden mit der Media Services v3-API – Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In diesem Artikel wird gezeigt, wie Sie mit der Methode der Dienstprinzipalanmeldung eine Verbindung zum Azure Media Services v3 Node.js SDK herstellen. Sie arbeiten dabei mit Dateien im Beispielrepository *media-services-v3-node-tutorials*. Das Beispiel *HelloWorld-ListAssets* enthält den Code zum Herstellen einer Verbindung und anschließenden Auflisten von Medienobjekten im Konto.

## <a name="prerequisites"></a>Voraussetzungen

- Eine Installation von Visual Studio Code.
- Installieren Sie [Node.js](https://nodejs.org/en/download/).
- Installieren Sie [TypeScript](https://www.typescriptlang.org/download).
- [Erstellen Sie ein Media Services-Konto.](./account-create-how-to.md) Merken Sie sich unbedingt den Namen der Ressourcengruppe und den Namen des Media Services-Kontos.
- Erstellen Sie einen Dienstprinzipal für Ihre Anwendung. Informationen finden Sie unter dem [Zugriff auf APIs](./access-api-howto.md).<br/>**Profi-Tipp:** Lassen Sie dieses Fenster geöffnet, oder kopieren Sie den gesamten Inhalt der JSON-Registerkarte in den Editor. 
- Verwenden Sie unbedingt die aktuelle Version des [AzureMediaServices SDK für JavaScript](https://www.npmjs.com/package/@azure/arm-mediaservices).

> [!IMPORTANT]
> Sehen Sie sich die [Benennungskonventionen](media-services-apis-overview.md#naming-conventions) für Azure Media Services an, um die wichtigen Benennungseinschränkungen für Entitäten zu verstehen.

## <a name="clone-the-nodejs-samples-repo"></a>Klonen des Node.js-Beispielrepositorys

Sie arbeiten mit einigen Dateien in Azure-Beispielen. Klonen Sie das Node.js-Beispielrepository.

```git
git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
```

## <a name="install-the-nodejs-packages"></a>Installieren des Node.js-Pakets

### <a name="install-azurearm-mediaservices"></a>Installieren von @azure/arm-mediaservices

```bash
npm install @azure/arm-mediaservices
```

### <a name="install-azurems-rest-nodeauth"></a>Installieren von @azure/ms-rest-nodeauth

Installieren Sie die Mindestversion von @azure/ms-rest-nodeauth: "^3.0.0".

```bash
npm install @azure/ms-rest-nodeauth@"^3.0.0"
```

In diesem Beispiel verwenden Sie die folgenden Pakete in der Datei `package.json`.

|Paket|BESCHREIBUNG|
|---|---|
|`@azure/arm-mediaservices`|Azure Media Services SDK <br/>Um sicherzustellen, dass Sie das neueste Azure Media Services-Paket verwenden, überprüfen Sie [npm install @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices).|
|`@azure/ms-rest-nodeauth` | Ist für die AAD-Authentifizierung mit Dienstprinzipal oder verwalteter Identität erforderlich.|
|`@azure/storage-blob`|Storage SDK Wird beim Hochladen von Dateien in Medienobjekte verwendet.|
|`@azure/ms-rest-js`| Wird zur Anmeldung verwendet.|
|`@azure/storage-blob` | Wird zum Hoch- und Herunterladen von Dateien in Medienobjekte in Azure Media Services für die Codierung verwendet.|
|`@azure/abort-controller`| Wird zusammen mit dem Speicherclient für das Timeout lang andauernder Downloadvorgänge verwendet.|

### <a name="create-the-packagejson-file"></a>Erstellen der Datei „package.json“

1. Erstellen Sie eine Datei `package.json` mit Ihrem bevorzugten Editor.
1. Öffnen Sie die Datei, und fügen Sie den folgenden Code ein:

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.ts",
  "dependencies": {
    "@azure/arm-mediaservices": "^8.0.0",
    "@azure/abort-controller": "^1.0.2",
    "@azure/ms-rest-nodeauth": "^3.0.6",
    "@azure/storage-blob": "^12.4.0",
  }
}
```

## <a name="connect-to-nodejs-client-using-typescript"></a>Herstellen einer Verbindung zum Node.js-Client mit TypeScript

### <a name="sample-env-file"></a>*.env*-Beispieldatei

Kopieren Sie den Inhalt dieser Datei in eine Datei mit dem Namen *.env*. Sie sollte im Stammverzeichnis Ihres Arbeitsrepositorys gespeichert werden. Dies sind die Werte, die Sie von der Seite für den API-Zugriff für Ihr Media Services-Konto im Portal erhalten haben.

Um auf die Werte zuzugreifen, die für die Eingabe in die *.env*-Datei benötigt werden, empfiehlt es sich, zunächst den Artikel [Zugriff auf die API](./access-api-howto.md) zu lesen und zu überprüfen.
Sie können entweder das Azure-Portal oder die CLI verwenden, um die Werte abzurufen, die für die Eingabe in die Umgebungsvariablendatei dieses Beispiels erforderlich sind.

Nachdem Sie die Datei *.env* erstellt haben, können Sie mit den Beispielen arbeiten.

```nodejs
# Values from the API Access page in the portal
AZURE_CLIENT_ID=""
AZURE_CLIENT_SECRET= ""
AZURE_TENANT_ID= ""

# Change this to match your AAD Tenant domain name. 
AAD_TENANT_DOMAIN = "microsoft.onmicrosoft.com"

# Set this to your Media Services Account name, resource group it is contained in, and location
AZURE_MEDIA_ACCOUNT_NAME = ""
AZURE_LOCATION= ""
AZURE_RESOURCE_GROUP= ""

# Set this to your Azure Subscription ID
AZURE_SUBSCRIPTION_ID= ""

# You must change these if you are using Gov Cloud, China, or other non standard cloud regions
AZURE_ARM_AUDIENCE= "https://management.core.windows.net"
AZURE_ARM_ENDPOINT="https://management.azure.com"

# DRM Testing
DRM_SYMMETRIC_KEY="add random base 64 encoded string here"
```

## <a name="run-the-sample-application-helloworld-listassets"></a>Ausführen der Beispielanwendung *HelloWorld-ListAssets*

1. Ändern Sie das Verzeichnis in den Ordner *AMSv3Samples*.

```bash
cd AMSv3Samples
```

2. Installieren Sie die Pakete, die in der Datei *package.json* verwendet werden.

```
npm install 
```

3. Ändern Sie das Verzeichnis in den Ordner *HelloWorld-ListAssets*.

```bash
cd HelloWorld-ListAssets
```

4. Starten Sie Visual Studio Code aus dem Ordner „AMSv3Samples“. Dies ist erforderlich, um aus dem Ordner zu starten, in dem sich der Ordner „.vscode“ und die Dateien „tsconfig.json“ befinden.

```dotnetcli
cd ..
code .
```

Öffnen Sie den Ordner für *HelloWorld-ListAssets*, und öffnen Sie die Datei *index.ts* im Visual Studio Code-Editor.

Drücken Sie bei geöffneter Datei *index.ts* die Taste F5, um den Debugger zu starten. Es sollte eine Liste von Medienobjekten angezeigt werden, sofern bereits Medienobjekte im Konto vorhanden sind. Ist das Konto leer, wird eine leere Liste angezeigt.  

Zum schnellen Anzeigen einer Liste von Medienobjekten laden Sie einige Videodateien über das Portal hoch. Medienobjekte werden jeweils automatisch erstellt, und beim erneuten Ausführen dieses Skripts werden die entsprechenden Namen zurückgegeben.

### <a name="a-closer-look-at-the-helloworld-listassets-sample"></a>Genauere Betrachtung des Beispiels *HelloWorld-ListAssets*

Das Beispiel *HelloWorld-ListAssets* zeigt, wie Sie mit einem Dienstprinzipal eine Verbindung zum Media Services-Client herstellen und Medienobjekte im Konto auflisten. Eine ausführliche Erläuterung der Funktionsweise finden Sie in den Kommentaren im Code.

```ts
import * as msRestNodeAuth from "@azure/ms-rest-nodeauth";
import { AzureMediaServices } from '@azure/arm-mediaservices';
import { AzureMediaServicesOptions } from "@azure/arm-mediaservices/esm/models";
// Load the .env file if it exists
import * as dotenv from "dotenv";
dotenv.config();

export async function main() {
  // Copy the samples.env file and rename it to .env first, then populate it's values with the values obtained 
  // from your Media Services account's API Access page in the Azure portal.
  const clientId = process.env.AZURE_CLIENT_ID as string;
  const secret = process.env.AZURE_CLIENT_SECRET as string;
  const tenantDomain = process.env.AAD_TENANT_DOMAIN as string;
  const subscriptionId = process.env.AZURE_SUBSCRIPTION_ID as string;
  const resourceGroup = process.env.AZURE_RESOURCE_GROUP as string;
  const accountName = process.env.AZURE_MEDIA_ACCOUNT_NAME as string;

  let clientOptions: AzureMediaServicesOptions = {
    longRunningOperationRetryTimeout: 5, // set the time out for retries to 5 seconds
    noRetryPolicy: false // use the default retry policy.
  }

  const creds = await msRestNodeAuth.loginWithServicePrincipalSecret(clientId, secret, tenantDomain);
  const mediaClient = new AzureMediaServices(creds, subscriptionId, clientOptions);

  // List Assets in Account
  console.log("Listing Assets Names in account:")
  var assets = await mediaClient.assets.list(resourceGroup, accountName);

  assets.forEach(asset => {
    console.log(asset.name);
  });

  if (assets.odatanextLink) {
    console.log("There are more than 1000 assets in this account, use the assets.listNext() method to continue listing more assets if needed")
    console.log("For example:  assets = await mediaClient.assets.listNext(assets.odatanextLink)");
  }
}

main().catch((err) => {
  console.error("Error running sample:", err.message);
});
```

## <a name="more-samples"></a>Weitere Beispiele

Die folgenden Beispiele sind im [Repository](https://github.com/Azure-Samples/media-services-v3-node-tutorials) verfügbar.

|Projektname|Anwendungsfall|
|---|---|
|Live/index.ts| Einfaches Livestreaming-Beispiel. **WARNUNG**: Stellen Sie sicher, dass alle Ressourcen bereinigt sind und im Portal nicht mehr abgerechnet werden, wenn Sie Livestreaming verwenden.|
|StreamFilesSample/index.ts| Einfaches Beispiel für das Hochladen einer lokalen Datei oder Codieren aus einer Quell-URL. Das Beispiel zeigt, wie Sie Storage SDK zum Herunterladen von Inhalten verwenden und wie Sie an einen Player streamen. |
|StreamFilesWithDRMSample/index.ts| Zeigt, wie Sie mithilfe von Widevine und PlayReady DRM codieren und streamen. |
|VideoIndexerSample/index.ts| Beispiel für die Verwendung von Voreinstellungen für das Video- und Audioanalysetool zum Generieren von Metadaten und Erkenntnissen aus einer Video- oder Audiodatei. |

## <a name="see-also"></a>Siehe auch

- [Referenzdokumentation zu Azure Media Services-Modulen für Node.js](/javascript/api/overview/azure/media-services)
- [Azure für JavaScript- und Node.js-Entwickler](/azure/developer/javascript/)
- [Media Services-Quellcode im GitHub-Repository @azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Dokumentation zum Azure-Paket für Node.js-Entwickler](/javascript/api/overview/azure/)
- [Media Services-Konzepte](concepts-overview.md)
- [npm install @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [Azure für JavaScript- und Node.js-Entwickler](/azure/developer/javascript/)
- [Media Services-Quellcode im Repository @azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>Nächste Schritte

Machen Sie sich mit der [Node.js-Referenzdokumentation](/javascript/api/overview/azure/mediaservices/management) für Media Services sowie mit [Beispielen](https://github.com/Azure-Samples/media-services-v3-node-tutorials) vertraut, die die Verwendung der Media Services-API mit Node.js veranschaulichen.
