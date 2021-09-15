---
title: 'Schnellstart: Textanalyse-Clientbibliothek v3 für Node.js | Microsoft-Dokumentation'
description: Hier finden Sie Informationen zu den ersten Schritten mit der Textanalyse-Clientbibliothek v3 für Node.js.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 08/17/2021
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.custom: devx-track-js
ms.openlocfilehash: c00334378ebbc2f399a9daa1c20ed4c47c84df65
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864857"
---
<a name="HOLTop"></a>

# <a name="version-32-preview1"></a>[Version 3.2-preview.1](#tab/version-3-2)

[Referenzdokumentation zu v3.2-preview](/javascript/api/overview/azure/ai-text-analytics-readme?preserve-view=true&view=azure-node-preview) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [Paket (NPM) für v3.2-preview](https://www.npmjs.com/package/@azure/ai-text-analytics/v/5.2.0-beta.1) | [Beispiele](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/textanalytics/ai-text-analytics/samples)


# <a name="version-31"></a>[Version 3.1](#tab/version-3-1)

[Referenzdokumentation zu v3.1](/javascript/api/overview/azure/ai-text-analytics-readme?preserve-view=true&view=azure-node-latest) | [Quellcode der v3-Bibliothek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [Paket (NPM) für v3.1](https://www.npmjs.com/package/@azure/ai-text-analytics/v/5.1.0) | [Beispiele](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/textanalytics/ai-text-analytics/samples)

---

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* Die aktuelle Version von [Node.js](https://nodejs.org/).
* [!INCLUDE [contributor-requirement](../../../includes/quickstarts/contributor-requirement.md)]
* Sobald Sie über Ihr Azure-Abonnement verfügen, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Erstellen einer Textanalyseressource"  target="_blank"> erstellen Sie eine Textanalyseressource </a> im Azure-Portal, um Ihren Schlüssel und Endpunkt zu erhalten. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und den Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Textanalyse-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.
* Sie benötigen eine Textanalyse-Ressource des Standard-Tarifs (S), um das Analyze-Feature nutzen zu können.

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

# <a name="version-32-preview1"></a>[Version 3.2-preview.1](#tab/version-3-2)

Installieren Sie das NPM-Paket `@azure/ai-text-analytics`:

```console
npm install @azure/ai-text-analytics@5.2.0-beta.1
```

Features in dieser Version der Textanalyse-API:

* Stimmungsanalyse
* Opinion Mining
* Spracherkennung
* Entitätserkennung
* Entitätsverknüpfung
* Erkennung personenbezogener Informationen
* Schlüsselwortextraktion
* Asynchrone Methoden
* Textanalyse für Gesundheit
* Textzusammenfassung

# <a name="version-31"></a>[Version 3.1](#tab/version-3-1)

Installieren Sie das NPM-Paket `@azure/ai-text-analytics`:

```console
npm install --save @azure/ai-text-analytics@5.1.0
```

Features in dieser Version der Textanalyse-API:

* Stimmungsanalyse
* Opinion Mining
* Spracherkennung
* Entitätserkennung
* Entitätsverknüpfung
* Erkennung personenbezogener Informationen
* Schlüsselwortextraktion
* Asynchrone Methoden
* Textanalyse für Gesundheit

---

Die Datei `package.json` Ihrer App wird mit den Abhängigkeiten aktualisiert.
Erstellen Sie eine Datei namens `index.js`, und fügen Sie Folgendes hinzu:

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
```

Erstellen Sie Variablen für den Azure-Endpunkt und -Schlüssel Ihrer Ressource.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = '<paste-your-text-analytics-endpoint-here>';
```

## <a name="object-model"></a>Objektmodell

Der Textanalyseclient ist ein Objekt vom Typ `TextAnalyticsClient`, das sich bei Azure mit Ihrem Schlüssel authentifiziert. Der Client verfügt über verschiedene Methoden zum Analysieren von Text als einzelne Zeichenfolge oder als Batch.

Text wird als `documents`-Liste an die API gesendet. Hierbei handelt es sich um `dictionary`-Objekte, die je nach verwendeter Methode eine Kombination aus den Attributen `id`, `text` und `language` enthalten. Das `text`-Attribut speichert den zu analysierenden Text in der Ausgangssprache (`language`), und `id` kann ein beliebiger Wert sein. 

Das Antwortobjekt ist eine Liste mit den Analyseinformationen für jedes Dokument. 

[!INCLUDE [text-analytics-character-limits](../character-limits.md)]

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Erstellen Sie ein neues Objekt vom Typ `TextAnalyticsClient` mit Ihrem Schlüssel und Ihrem Endpunkt als Parameter.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));
```

## <a name="sentiment-analysis"></a>Stimmungsanalyse

Erstellen Sie ein Zeichenfolgenarray, das das zu analysierende Dokument enthält. Rufen Sie die Methode `analyzeSentiment()` des Clients auf, und rufen Sie das zurückgegebene Objekt `SentimentBatchResult` ab. Durchlaufen Sie die Liste mit den Ergebnissen, und geben Sie die ID des jeweiligen Dokuments sowie die Stimmung auf Dokumentebene zusammen mit Zuverlässigkeitsbewertungen aus. Das Ergebnis enthält die Stimmung auf Satzebene sowie Offsets, Länge und Zuverlässigkeitsbewertungen für jedes Dokument.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput);

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
        });
    });
}
sentimentAnalysis(textAnalyticsClient)
```

Führen Sie in Ihrem Konsolenfenster Ihren Code mit `node index.js` aus.

### <a name="output"></a>Output

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
```


## <a name="opinion-mining"></a>Opinion Mining

Zum Durchführen von Stimmungsanalyse mit Opinion Mining erstellen Sie ein Array aus Zeichenfolgen, die das zu analysierende Dokument enthalten. Rufen Sie die Methode `analyzeSentiment()` des Clients mit hinzugefügtem Optionsflag `includeOpinionMining: true` auf, und rufen Sie das zurückgegebene `SentimentBatchResult`-Objekt ab. Durchlaufen Sie die Liste mit den Ergebnissen, und geben Sie die ID des jeweiligen Dokuments sowie die Stimmung auf Dokumentebene zusammen mit Zuverlässigkeitsbewertungen aus. Für jedes Dokument enthält das Ergebnis nicht nur die Stimmung auf Satzebene wie oben sondern auch die Stimmung auf Ansichts- und Meinungsebene.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```javascript
async function sentimentAnalysisWithOpinionMining(client){

  const sentimentInput = [
    {
      text: "The food and service were unacceptable, but the concierge were nice",
      id: "0",
      language: "en"
    }
  ];
  const results = await client.analyzeSentiment(sentimentInput, { includeOpinionMining: true });

  for (let i = 0; i < results.length; i++) {
    const result = results[i];
    console.log(`- Document ${result.id}`);
    if (!result.error) {
      console.log(`\tDocument text: ${sentimentInput[i].text}`);
      console.log(`\tOverall Sentiment: ${result.sentiment}`);
      console.log("\tSentiment confidence scores:", result.confidenceScores);
      console.log("\tSentences");
      for (const { sentiment, confidenceScores, opinions } of result.sentences) {
        console.log(`\t- Sentence sentiment: ${sentiment}`);
        console.log("\t  Confidence scores:", confidenceScores);
        console.log("\t  Mined opinions");
        for (const { target, assessments } of opinions) {
          console.log(`\t\t- Target text: ${target.text}`);
          console.log(`\t\t  Target sentiment: ${target.sentiment}`);
          console.log("\t\t  Target confidence scores:", target.confidenceScores);
          console.log("\t\t  Target assessments");
          for (const { text, sentiment } of assessments) {
            console.log(`\t\t\t- Text: ${text}`);
            console.log(`\t\t\t  Sentiment: ${sentiment}`);
          }
        }
      }
    } else {
      console.error(`\tError: ${result.error}`);
    }
  }
}
sentimentAnalysisWithOpinionMining(textAnalyticsClient)
```

Führen Sie in Ihrem Konsolenfenster Ihren Code mit `node index.js` aus.

### <a name="output"></a>Output

```console
- Document 0
        Document text: The food and service were unacceptable, but the concierge were nice
        Overall Sentiment: positive
        Sentiment confidence scores: { positive: 0.84, neutral: 0, negative: 0.16 }
        Sentences
        - Sentence sentiment: positive
          Confidence scores: { positive: 0.84, neutral: 0, negative: 0.16 }
          Mined opinions
                - Target text: food
                  Target sentiment: negative
                  Target confidence scores: { positive: 0.01, negative: 0.99 }
                  Target assessments
                        - Text: unacceptable
                          Sentiment: negative
                - Target text: service
                  Target sentiment: negative
                  Target confidence scores: { positive: 0.01, negative: 0.99 }
                  Target assessments
                        - Text: unacceptable
                          Sentiment: negative
                - Target text: concierge
                  Target sentiment: positive
                  Target confidence scores: { positive: 1, negative: 0 }
                  Target assessments
                        - Text: nice
                          Sentiment: positive
```

## <a name="language-detection"></a>Spracherkennung

Erstellen Sie ein Zeichenfolgenarray, das das zu analysierende Dokument enthält. Rufen Sie die Methode `detectLanguage()` des Clients auf, und rufen Sie das zurückgegebene Ergebnis (`DetectLanguageResultCollection`) ab. Durchlaufen Sie dann die Ergebnisse, und geben Sie für jedes Dokument die ID sowie die entsprechende primäre Sprache aus.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ];
    const languageResult = await client.detectLanguage(languageInputArray);

    languageResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

Führen Sie in Ihrem Konsolenfenster Ihren Code mit `node index.js` aus.

### <a name="output"></a>Output

```console
ID: 0
        Primary Language French
```


## <a name="named-entity-recognition-ner"></a>Erkennung benannter Entitäten (NER)

Erstellen Sie ein Zeichenfolgenarray, das das zu analysierende Dokument enthält. Rufen Sie die Methode `recognizeEntities()` des Clients auf, und rufen Sie das Objekt `RecognizeEntitiesResult` ab. Durchlaufen Sie die Liste mit den Ergebnissen, und geben Entitätsname, Typ, Untertyp, Offset, Länge und Bewertung aus.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];
    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.confidenceScore}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Führen Sie in Ihrem Konsolenfenster Ihren Code mit `node index.js` aus.

### <a name="output"></a>Output

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.29
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.78
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.82
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.25
```

## <a name="personally-identifying-information-pii-recognition"></a>Erkennung von personenbezogenen Informationen (Personally Identifying Information, PII)

Erstellen Sie ein Zeichenfolgenarray, das das zu analysierende Dokument enthält. Rufen Sie die Methode `recognizePiiEntities()` des Clients auf, und rufen Sie das Objekt `RecognizePIIEntitiesResult` ab. Durchlaufen Sie die Liste mit den Ergebnissen, und geben Entitätsname, Typ und Bewertung aus.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```javascript
async function piiRecognition(client) {

    const documents = [
        "The employee's phone number is (555) 555-5555."
    ];

    const results = await client.recognizePiiEntities(documents, "en");
    for (const result of results) {
        if (result.error === undefined) {
            console.log("Redacted Text: ", result.redactedText);
            console.log(" -- Recognized PII entities for input", result.id, "--");
            for (const entity of result.entities) {
                console.log(entity.text, ":", entity.category, "(Score:", entity.confidenceScore, ")");
            }
        } else {
            console.error("Encountered an error:", result.error);
        }
    }
}
piiRecognition(textAnalyticsClient)
```

Führen Sie in Ihrem Konsolenfenster Ihren Code mit `node index.js` aus.

### <a name="output"></a>Output

```console
Redacted Text:  The employee's phone number is **************.
 -- Recognized PII entities for input 0 --
(555) 555-5555 : Phone Number (Score: 0.8 )
```

## <a name="entity-linking"></a>Entitätsverknüpfung

Erstellen Sie ein Zeichenfolgenarray, das das zu analysierende Dokument enthält. Rufen Sie die Methode `recognizeLinkedEntities()` des Clients auf, und rufen Sie das Objekt `RecognizeLinkedEntitiesResult` ab. Durchlaufen Sie die Liste mit den Ergebnissen, und geben Entitätsname, ID, Datenquelle, URL und Treffer aus. Jedes Objekt im Array `matches` enthält Offset, Länge und Bewertung für den Treffer.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.dataSourceEntityId} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text} \tScore: ${match.confidenceScore.toFixed(2)}`);
        })
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Führen Sie in Ihrem Konsolenfenster Ihren Code mit `node index.js` aus.

### <a name="output"></a>Output

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800       Score: 0.88
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.63
                Text: Gates     Score: 0.63
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.60
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.55
                Text: Microsoft         Score: 0.55
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.32
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.33
```

## <a name="key-phrase-extraction"></a>Schlüsselwortextraktion

Erstellen Sie ein Zeichenfolgenarray, das das zu analysierende Dokument enthält. Rufen Sie die Methode `extractKeyPhrases()` des Clients auf, und rufen Sie das zurückgegebene Objekt `ExtractKeyPhrasesResult` ab. Durchlaufen Sie die Ergebnisse, und geben Sie die ID und alle erkannten Schlüsselbegriffe aus.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```javascript
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

Führen Sie in Ihrem Konsolenfenster Ihren Code mit `node index.js` aus.

### <a name="output"></a>Output

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

## <a name="extract-health-entities"></a>Extrahieren von Integritätsentitäten

[!INCLUDE [health operation pricing](../health-operation-pricing-caution.md)]

Sie können die Textanalyse verwenden, um eine asynchrone Anforderung zum Extrahieren von Integritätsentitäten aus dem Text durchzuführen. Nachfolgend sehen Sie ein einfaches Beispiel für diesen Vorgang. Ein komplexeres Beispiel können Sie auf [GitHub](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/textanalytics/ai-text-analytics/samples/v5/javascript/beginAnalyzeHealthcareEntities.js) finden.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```javascript
async function healthExample(client) {
    console.log("== Recognize Healthcare Entities Sample ==");

    const documents = [
        "Prescribed 100mg ibuprofen, taken twice daily."
      ];
    const poller = await client.beginAnalyzeHealthcareEntities(documents, "en", {
      includeStatistics: true
    });
  
    poller.onProgress(() => {
      console.log(
        `Last time the operation was updated was on: ${poller.getOperationState().lastModifiedOn}`
      );
    });
    console.log(
      `The analyze healthcare entities operation was created on ${
        poller.getOperationState().createdOn
      }`
    );
    console.log(
      `The analyze healthcare entities operation results will expire on ${
        poller.getOperationState().expiresOn
      }`
    );
  
    const results = await poller.pollUntilDone();
  
    for await (const result of results) {
      console.log(`- Document ${result.id}`);
      if (!result.error) {
        console.log("\tRecognized Entities:");
        for (const entity of result.entities) {
          console.log(`\t- Entity "${entity.text}" of type ${entity.category}`);
        }
        if (result.entityRelations && (result.entityRelations.length > 0)) {
          console.log(`\tRecognized relations between entities:`);
          for (const relation of result.entityRelations) {
            console.log(
              `\t\t- Relation of type ${relation.relationType} found between the following entities:`
            );
            for (const role of relation.roles) {
              console.log(`\t\t\t- "${role.entity.text}" with the role ${role.name}`);
            }
          }
        }
      } else console.error("\tError:", result.error);
    }
  }
  
  healthExample(textAnalyticsClient).catch((err) => {
    console.error("The sample encountered an error:", err);
  });
```

### <a name="output"></a>Output

```console
- Document 0
    Recognized Entities:
    - Entity "100mg" of type Dosage
    - Entity "ibuprofen" of type MedicationName
    - Entity "twice daily" of type Frequency
    Recognized relations between entities:
        - Relation of type DosageOfMedication found between the following entities:   
                - "100mg" with the role Dosage
                - "ibuprofen" with the role Medication
        - Relation of type FrequencyOfMedication found between the following entities:
                - "ibuprofen" with the role Medication
                - "twice daily" with the role Frequency
```


## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>Asynchrones Verwenden der API mit dem Analyze-Vorgang

Mithilfe des Analysevorgangs können Sie asynchrone Batchanforderungen für Folgendes ausführen: NER, Schlüsselbegriffserkennung, Stimmungsanalyse und Erkennung personenbezogener Informationen. Nachfolgend sehen Sie ein einfaches Beispiel für einen Vorgang. Erweiterte Beispiele für [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/textanalytics/ai-text-analytics/samples/v5/javascript/beginAnalyzeActions.js) und [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/textanalytics/ai-text-analytics/samples/v5/typescript/src/beginAnalyzeActions.ts) finden Sie auf GitHub.

[!INCLUDE [Analyze Batch Action pricing](../analyze-operation-pricing-caution.md)]

Erstellen Sie die neue Funktion `analyze_example()`, die die Funktion `beginAnalyze()` aufruft. Daraus resultiert ein zeitintensiver Vorgang, aus dem Ergebnisse abgerufen werden.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```javascript
async function analyze_example(client) {
    const documents = [
        "Microsoft was founded by Bill Gates and Paul Allen.",
    ];

    const actions = {
        recognizeEntitiesActions: [{ modelVersion: "latest" }],
        extractKeyPhrasesActions: [{ modelVersion: "latest" }]
    };
    const poller = await client.beginAnalyzeActions(documents, actions, "en");

    console.log(
        `The analyze batch actions operation was created on ${poller.getOperationState().createdOn}`
    );
    console.log(
        `The analyze batch actions operation results will expire on ${poller.getOperationState().expiresOn
        }`
    );
    const resultPages = await poller.pollUntilDone();
    for await (const page of resultPages) {
        const entitiesAction = page.recognizeEntitiesResults[0];
        if (!entitiesAction.error) {
            for (const doc of entitiesAction.results) {
                console.log(`- Document ${doc.id}`);
                if (!doc.error) {
                    console.log("\tEntities:");
                    for (const entity of doc.entities) {
                        console.log(`\t- Entity ${entity.text} of type ${entity.category}`);
                    }
                } else {
                    console.error("\tError:", doc.error);
                }
            }
        }
        const keyPhrasesAction = page.extractKeyPhrasesResults[0];
        if (!keyPhrasesAction.error) {
            for (const doc of keyPhrasesAction.results) {
                console.log(`- Document ${doc.id}`);
                if (!doc.error) {
                    console.log("\tKey phrases:");
                    for (const phrase of doc.keyPhrases) {
                        console.log(`\t- ${phrase}`);
                    }
                } else {
                    console.error("\tError:", doc.error);
                }
            }
        }
    }
}
analyze_example(textAnalyticsClient)
```

### <a name="output"></a>Ausgabe

```console
The analyze batch actions operation was created on Fri Jun 18 2021 12:34:52 GMT-0700 (Pacific Daylight Time)
The analyze batch actions operation results will expire on Sat Jun 19 2021 12:34:52 GMT-0700 (Pacific Daylight Time)
- Document 0
        Entities:
        - Entity Microsoft of type Organization
        - Entity Bill Gates of type Person
        - Entity Paul Allen of type Person
- Document 0
        Key phrases:
        - Bill Gates
        - Paul Allen
        - Microsoft
```

Sie können den Analysevorgang auch verwenden, um NER, Schlüsselbegriffserkennung, Stimmungsanalyse und die Erkennung personenbezogener Informationen durchzuführen. Die Analyze-Beispiele für [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/textanalytics/ai-text-analytics/samples/v5/javascript/beginAnalyzeActions.js) und [TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples/v5/typescript/src) finden Sie auf GitHub.

## <a name="text-summarization"></a>Textzusammenfassung

# <a name="version-32-preview1"></a>[Version 3.2-preview.1](#tab/version-3-2)

Sie können die Textanalyse verwenden, um große Textblöcke zusammenzufassen. Die Methode ist asynchron und gibt die obersten Sätze zurück, wenn der zeitintensive Vorgang abgeschlossen ist.

```javascript
async function summarization_example(client) {
    const documents = [`The extractive summarization feature in Text Analytics uses natural language processing techniques to locate key sentences in an unstructured text document. 
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

# <a name="version-31"></a>[Version 3.1](#tab/version-3-1)

Dieses Feature ist in Version 3.1 nicht verfügbar.

---

Führen Sie die Anwendung mit dem Befehl `node` für die Schnellstartdatei aus.

```console
node index.js
```
