---
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: devx-track-js, ignite-fall-2021
ms.openlocfilehash: 6ca78f4e32495628d6597e2f6910e4b306d544df
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029540"
---
[Referenzdokumentation](/javascript/api/overview/azure/ai-text-analytics-readme?preserve-view=true&view=azure-node-preview) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [Paket (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics/v/5.2.0-beta.1) | [Beispiele](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/textanalytics/ai-text-analytics/samples)

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

Installieren Sie die NPM-Pakete:

```console
npm install --save @azure/ai-text-analytics@5.2.0-beta.1
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

// Example method for summarizing text
async function summarization_example(client) {
    const documents = [`The extractive summarization feature uses natural language processing techniques to locate key sentences in an unstructured text document. 
        These sentences collectively convey the main idea of the document. This feature is provided as an API for developers. 
        They can use it to build intelligent solutions based on the relevant information extracted to support various use cases. 
        In the public preview, extractive summarization supports several languages. It is based on pretrained multilingual transformer models, part of our quest for holistic representations. 
        It draws its strength from transfer learning across monolingual and harness the shared nature of languages to produce models of improved quality and efficiency.`];

    console.log("== Analyze Sample For Extract Summary ==");

    const actions = {
        extractSummaryActions: [{ modelVersion: "latest", orderBy: "Rank", maxSentenceCount: 5 }],
    };
    const poller = await client.beginAnalyzeActions(documents, actions, "en");

    poller.onProgress(() => {
        console.log(
            `Number of actions still in progress: ${poller.getOperationState().actionsInProgressCount}`
        );
    });

    console.log(`The analyze actions operation created on ${poller.getOperationState().createdOn}`);

    console.log(
        `The analyze actions operation results will expire on ${poller.getOperationState().expiresOn}`
    );

    const resultPages = await poller.pollUntilDone();

    for await (const page of resultPages) {
        const extractSummaryAction = page.extractSummaryResults[0];
        if (!extractSummaryAction.error) {
            for (const doc of extractSummaryAction.results) {
                console.log(`- Document ${doc.id}`);
                if (!doc.error) {
                    console.log("\tSummary:");
                    for (const sentence of doc.sentences) {
                        console.log(`\t- ${sentence.text}`);
                    }
                } else {
                    console.error("\tError:", doc.error);
                }
            }
        }
    } 
}
summarization_example(textAnalyticsClient).catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

### <a name="output"></a>Output

```console
== Analyze Sample For Extract Summary ==
The analyze actions operation created on Thu Sep 16 2021 13:12:31 GMT-0700 (Pacific Daylight Time)
The analyze actions operation results will expire on Fri Sep 17 2021 13:12:31 GMT-0700 (Pacific Daylight Time)

- Document 0
        Summary:
        - They can use it to build intelligent solutions based on the relevant information extracted to support various use cases.
        - This feature is provided as an API for developers.
        - The extractive summarization feature uses natural language processing techniques to locate key sentences in an unstructured 
text document.
        - These sentences collectively convey the main idea of the document.
        - In the public preview, extractive summarization supports several languages.
```
