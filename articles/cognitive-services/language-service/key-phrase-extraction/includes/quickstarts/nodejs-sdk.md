---
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: devx-track-js, ignite-fall-2021
ms.openlocfilehash: bb469013debdc35a41cc907d1aced2097738898d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131100886"
---
[Referenzdokumentation](/javascript/api/overview/azure/ai-text-analytics-readme?preserve-view=true&view=azure-node-latest) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [Paket (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics/v/5.1.0) | [Beispiele](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/textanalytics/ai-text-analytics/samples)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* Die aktuelle Version von [Node.js](https://nodejs.org/).
* Sobald Sie über Ihr Azure-Abonnement verfügen, sollten Sie im Azure-Portal <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Erstellen einer Sprachressource"  target="_blank">eine Sprachressource erstellen</a>, um Ihren Schlüssel und Endpunkt abzurufen. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.
* Sie benötigen eine Sprachressource des Standard-Tarifs (S), um die Analysefunktion verwenden zu können.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-nodejs-application"></a>Erstellen einer neuen Node.js-Anwendung

Erstellen Sie in einem Konsolenfenster (etwa cmd, PowerShell oder Bash) ein neues Verzeichnis für Ihre App, und rufen Sie es auf. 

```console
mkdir myapp 

cd myapp
```

Führen Sie den Befehl `npm init` aus, um eine Knotenanwendung mit der Datei `package.json` zu erstellen. 

```console
npm init
```

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek

Installieren Sie das NPM-Paket:

```console
npm install --save @azure/ai-text-analytics@5.1.0
```

## <a name="code-example"></a>Codebeispiel

Öffnen Sie die Datei, und kopieren Sie den folgenden Code. Denken Sie daran, die Variable `key` durch den Schlüssel für Ihre Ressource und die Variable `endpoint` durch den Endpunkt für Ihre Ressource zu ersetzen. 

[!INCLUDE [find the key and endpoint for a resource](../../../includes/find-azure-resource-info.md)]

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
const key = '<paste-your-key-here>';
const endpoint = '<paste-your-endpoint-here>';
// Authenticate the client with your key and endpoint
const textAnalyticsClient = new TextAnalyticsClient(endpoint, new AzureKeyCredential(key));

// Example method for extracting key phrases from text
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ];
    const keyPhraseResult = await client.extractKeyPhrases(keyPhrasesInput);
    
    keyPhraseResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

### <a name="output"></a>Output

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```
