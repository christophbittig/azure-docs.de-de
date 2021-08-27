---
title: 'Schnellstart: Textanalyse-Clientbibliothek v3 für Java | Microsoft-Dokumentation'
description: Hier finden Sie Informationen zu den ersten Schritten mit der Textanalyse-Clientbibliothek v3 für Java.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/15/2021
ms.custom: devx-track-java
ms.author: aahi
ms.reviewer: tasharm, assafi, sumeh
ms.openlocfilehash: 58503476a3c17fdaff3c33585210dc762812b539
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114594254"
---
<a name="HOLTop"></a>

# <a name="version-31"></a>[Version 3.1](#tab/version-3-1)

[Referenzdokumentation](/java/api/overview/azure/ai-textanalytics-readme?preserve-view=true&view=azure-java-preview) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/textanalytics/azure-ai-textanalytics) | [Paket](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0) | [Beispiele](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/textanalytics/azure-ai-textanalytics/src/samples)

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

[Referenzdokumentation](/java/api/overview/azure/ai-textanalytics-readme) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-java/blob/azure-ai-textanalytics_5.0.0/sdk/textanalytics/azure-ai-textanalytics) | [Paket](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.0.0) | [Beispiele](https://github.com/Azure/azure-sdk-for-java/tree/azure-ai-textanalytics_5.0.0/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

---

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html), Version 8 oder höher
* Sobald Sie über Ihr Azure-Abonnement verfügen, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Erstellen einer Textanalyseressource"  target="_blank"> erstellen Sie eine Textanalyseressource </a> im Azure-Portal, um Ihren Schlüssel und Endpunkt zu erhalten.  Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und den Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Textanalyse-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.
* Sie benötigen eine Textanalyse-Ressource des Standard-Tarifs (S), um das Analyze-Feature nutzen zu können.

## <a name="setting-up"></a>Einrichten

### <a name="add-the-client-library"></a>Hinzufügen der Clientbibliothek

# <a name="version-31"></a>[Version 3.1](#tab/version-3-1)

Erstellen Sie ein Maven-Projekt in Ihrer bevorzugten IDE oder Entwicklungsumgebung. Fügen Sie anschließend der Datei *pom.xml* Ihres Projekts die folgende Abhängigkeit hinzu. Die Implementierungssyntax [für andere Buildtools](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0-beta.7) ist online verfügbar.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>5.1.0</version>
    </dependency>
</dependencies>
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Erstellen Sie ein Maven-Projekt in Ihrer bevorzugten IDE oder Entwicklungsumgebung. Fügen Sie anschließend der Datei *pom.xml* Ihres Projekts die folgende Abhängigkeit hinzu. Die Implementierungssyntax [für andere Buildtools](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.0.0) ist online verfügbar.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>5.0.0</version>
    </dependency>
</dependencies>
```

> [!TIP]
> Möchten Sie sich sofort die gesamte Codedatei für die Schnellstartanleitung ansehen? Die Datei steht [auf GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/TextAnalytics/TextAnalyticsSamples.java) zur Verfügung. Dort finden Sie die Codebeispiele aus dieser Schnellstartanleitung. 

---

Erstellen Sie eine Java-Datei mit dem Namen `TextAnalyticsSamples.java`. Öffnen Sie die Datei, und fügen Sie die folgenden `import`-Anweisungen hinzu:

# <a name="version-31-preview"></a>[Version 3.1-Preview](#tab/version-3-1)


```java

import com.azure.ai.textanalytics.TextAnalyticsAsyncClient;
import com.azure.core.credential.AzureKeyCredential;
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;

import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.TimeUnit;

import java.util.Arrays;
import com.azure.core.util.Context;
import com.azure.core.util.polling.SyncPoller;
import com.azure.ai.textanalytics.util.AnalyzeHealthcareEntitiesResultCollection;
import com.azure.ai.textanalytics.util.AnalyzeHealthcareEntitiesPagedIterable;
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

```java
import com.azure.core.credential.AzureKeyCredential;
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
```

---




Fügen Sie in der Java-Datei wie weiter unten gezeigt eine neue Klasse sowie den Schlüssel und den Endpunkt Ihrer Azure-Ressource hinzu.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```java
public class TextAnalyticsSamples {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

Fügen Sie der Klasse die folgende main-Methode hinzu. Die hier aufgerufenen Methoden definieren Sie später.

# <a name="version-31"></a>[Version 3.1](#tab/version-3-1)

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisWithOpinionMiningExample(client)
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    recognizePiiEntitiesExample(client);
    extractKeyPhrasesExample(client);
}
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisExample(client);
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    extractKeyPhrasesExample(client);
        AnalyzeOperationExample(client)
}
```

---


## <a name="object-model"></a>Objektmodell

Der Textanalyse-Client ist ein Objekt vom Typ `TextAnalyticsClient`, das sich mit Ihrem Schlüssel bei Azure authentifiziert und Funktionen bereitstellt, um Text als einzelne Zeichenfolgen oder als Batch zu akzeptieren. Sie können Text synchron oder asynchron an die API senden. Das Antwortobjekt enthält die Analyseinformationen für die einzelnen gesendeten Dokumente. 

## <a name="code-examples"></a>Codebeispiele

* [Authentifizieren des Clients](#authenticate-the-client)
* [Standpunktanalyse](#sentiment-analysis) 
* [Opinion Mining](#opinion-mining)
* [Sprachenerkennung](#language-detection)
* [Erkennung benannter Entitäten](#named-entity-recognition-ner)
* [Entitätsverknüpfung](#entity-linking)
* [Schlüsselbegriffserkennung](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Erstellen Sie eine Methode, um das Objekt `TextAnalyticsClient`mit dem Schlüssel und dem Endpunkt für Ihre Textanalyseressource zu instanziieren. Dieses Beispiel ist für die Versionen 3.0 und 3.1 der API gleich.

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .credential(new AzureKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```


Rufen Sie in der `main()`-Methode des Programms die Authentifizierungsmethode auf, um den Client zu instanziieren.

## <a name="sentiment-analysis"></a>Stimmungsanalyse

# <a name="version-31"></a>[Version 3.1](#tab/version-3-1)

> [!NOTE]
> In Version `3.1`:
> * Die Stimmungsanalyse schließt Opinion Mining als optionales Flag ein. 
> * Opinion Mining enthält die Stimmung auf Ansichts- und Meinungsebene. 

Erstellen Sie eine neue Funktion namens `sentimentAnalysisExample()`, die den zuvor erstellten Client verwendet, und rufen Sie die zugehörige Funktion `analyzeSentiment()` auf. Das zurückgegebene Objekt `AnalyzeSentimentResult` enthält `documentSentiment` und `sentenceSentiments` (sofern erfolgreich). Andernfalls enthält es eine Fehlermeldung (`errorMessage`). 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
    System.out.printf(
        "Recognized document sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
        documentSentiment.getSentiment(),
        documentSentiment.getConfidenceScores().getPositive(),
        documentSentiment.getConfidenceScores().getNeutral(),
        documentSentiment.getConfidenceScores().getNegative());

    for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
        System.out.printf(
            "Recognized sentence sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
            sentenceSentiment.getSentiment(),
            sentenceSentiment.getConfidenceScores().getPositive(),
            sentenceSentiment.getConfidenceScores().getNeutral(),
            sentenceSentiment.getConfidenceScores().getNegative());
        }
    }
}
```

### <a name="output"></a>Output

```console
Recognized document sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```

## <a name="opinion-mining"></a>Opinion Mining

Zum Durchführen von Stimmungsanalyse mit Opinion Mining erstellen Sie eine neue Funktion mit dem Namen `sentimentAnalysisWithOpinionMiningExample()`, die den von Ihnen zuvor erstellten Client akzeptiert, und rufen seine `analyzeSentiment()`-Funktion mit dem Einstellungsoptionsobjekt `AnalyzeSentimentOptions` auf. Das zurückgegebene Objekt `AnalyzeSentimentResult` enthält `documentSentiment` und `sentenceSentiments` (sofern erfolgreich). Andernfalls enthält es eine Fehlermeldung (`errorMessage`). 


```java
 static void sentimentAnalysisWithOpinionMiningExample(TextAnalyticsClient client)
 {
     // The document that needs be analyzed.
     String document = "Bad atmosphere. Not close to plenty of restaurants, hotels, and transit! Staff are not friendly and helpful.";

     System.out.printf("Document = %s%n", document);

     AnalyzeSentimentOptions options = new AnalyzeSentimentOptions().setIncludeOpinionMining(true);
     final DocumentSentiment documentSentiment = client.analyzeSentiment(document, "en", options);
     SentimentConfidenceScores scores = documentSentiment.getConfidenceScores();
     System.out.printf(
             "Recognized document sentiment: %s, positive score: %f, neutral score: %f, negative score: %f.%n",
             documentSentiment.getSentiment(), scores.getPositive(), scores.getNeutral(), scores.getNegative());


     documentSentiment.getSentences().forEach(sentenceSentiment -> {
         SentimentConfidenceScores sentenceScores = sentenceSentiment.getConfidenceScores();
         System.out.printf("\tSentence sentiment: %s, positive score: %f, neutral score: %f, negative score: %f.%n",
                 sentenceSentiment.getSentiment(), sentenceScores.getPositive(), sentenceScores.getNeutral(), sentenceScores.getNegative());
         sentenceSentiment.getOpinions().forEach(opinion -> {
             TargetSentiment targetSentiment = opinion.getTarget();
             System.out.printf("\t\tTarget sentiment: %s, target text: %s%n", targetSentiment.getSentiment(),
                     targetSentiment.getText());
             for (AssessmentSentiment assessmentSentiment : opinion.getAssessments()) {
                 System.out.printf("\t\t\t'%s' assessment sentiment because of \"%s\". Is the assessment negated: %s.%n",
                         assessmentSentiment.getSentiment(), assessmentSentiment.getText(), assessmentSentiment.isNegated());
             }
         });
     });
 }
```

### <a name="output"></a>Output

```console
Document = Bad atmosphere. Not close to plenty of restaurants, hotels, and transit! Staff are not friendly and helpful.
Recognized document sentiment: negative, positive score: 0.010000, neutral score: 0.140000, negative score: 0.850000.
    Sentence sentiment: negative, positive score: 0.000000, neutral score: 0.000000, negative score: 1.000000.
        Target sentiment: negative, target text: atmosphere
            'negative' assessment sentiment because of "bad". Is the assessment negated: false.
    Sentence sentiment: negative, positive score: 0.020000, neutral score: 0.440000, negative score: 0.540000.
    Sentence sentiment: negative, positive score: 0.000000, neutral score: 0.000000, negative score: 1.000000.
        Target sentiment: negative, target text: Staff
            'negative' assessment sentiment because of "friendly". Is the assessment negated: true.
            'negative' assessment sentiment because of "helpful". Is the assessment negated: true.
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Erstellen Sie eine neue Funktion namens `sentimentAnalysisExample()`, die den zuvor erstellten Client verwendet, und rufen Sie die zugehörige Funktion `analyzeSentiment()` auf. Das zurückgegebene Objekt `AnalyzeSentimentResult` enthält `documentSentiment` und `sentenceSentiments` (sofern erfolgreich). Andernfalls enthält es eine Fehlermeldung (`errorMessage`). 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
    System.out.printf(
        "Recognized document sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
        documentSentiment.getSentiment(),
        documentSentiment.getConfidenceScores().getPositive(),
        documentSentiment.getConfidenceScores().getNeutral(),
        documentSentiment.getConfidenceScores().getNegative());

    for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
        System.out.printf(
            "Recognized sentence sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
            sentenceSentiment.getSentiment(),
            sentenceSentiment.getConfidenceScores().getPositive(),
            sentenceSentiment.getConfidenceScores().getNeutral(),
            sentenceSentiment.getConfidenceScores().getNegative());
        }
    }
}
```

### <a name="output"></a>Output

```console
Recognized document sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```

---

## <a name="language-detection"></a>Spracherkennung

Erstellen Sie eine neue Funktion namens `detectLanguageExample()`, die den zuvor erstellten Client verwendet, und rufen Sie die zugehörige Funktion `detectLanguage()` auf. Das zurückgegebene Objekt `DetectLanguageResult` enthält eine primäre erkannte Sprache und eine Liste mit anderen erkannten Sprachen (sofern erfolgreich). Andernfalls enthält es eine Fehlermeldung (`errorMessage`). Dieses Beispiel ist für die Versionen 3.0 und 3.1 der API gleich.

> [!Tip]
> In einigen Fällen ist es unter Umständen schwierig, die Sprachen basierend auf den Eingaben eindeutig zu bestimmen. Mithilfe des Parameters `countryHint` können Sie einen zweistelligen Ländercode angeben. Die API verwendet standardmäßig „US“ als Wert für „countryHint“. Wenn Sie dieses Verhalten entfernen möchten, können Sie diesen Parameter zurücksetzen, indem Sie seinen Wert auf eine leere Zeichenfolge (`countryHint = ""`) festlegen. Wenn Sie einen anderen Standardwert festlegen möchten, legen Sie die Eigenschaft `TextAnalyticsClientOptions.DefaultCountryHint` fest, und übergeben Sie sie während der Initialisierung des Clients.

```java
static void detectLanguageExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Ce document est rédigé en Français.";

    DetectedLanguage detectedLanguage = client.detectLanguage(text);
    System.out.printf("Detected primary language: %s, ISO 6391 name: %s, score: %.2f.%n",
        detectedLanguage.getName(),
        detectedLanguage.getIso6391Name(),
        detectedLanguage.getConfidenceScore());
}
```

### <a name="output"></a>Output

```console
Detected primary language: French, ISO 6391 name: fr, score: 1.00.
```

## <a name="named-entity-recognition-ner"></a>Erkennung benannter Entitäten (NER)

# <a name="version-31"></a>[Version 3.1](#tab/version-3-1)

> [!NOTE]
> In Version `3.1`:
> * Die Erkennung benannter Entitäten enthält separate Methoden zum Erkennen personenbezogener Informationen. 
> * Die Entitätsverknüpfung ist eine separate Anforderung.

Erstellen Sie eine neue Funktion namens `recognizeEntitiesExample()`, die den zuvor erstellten Client verwendet, und rufen Sie die zugehörige Funktion `recognizeEntities()` auf. Das zurückgegebene Objekt `CategorizedEntityCollection` enthält eine Liste mit Elementen vom Typ `CategorizedEntity` (sofern erfolgreich). Andernfalls enthält es eine Fehlermeldung (`errorMessage`).

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, score: %s, offset: %s, length: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubcategory(),
            entity.getConfidenceScore(),
            entity.getOffset(),
            entity.getLength());
    }
}
```

### <a name="output"></a>Output

```console
Recognized entity: trip, entity category: Event, entity sub-category: null, score: 0.61, offset: 8, length: 4.
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.82, offset: 16, length: 7.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8, offset: 24, length: 9.
```


## <a name="personally-identifiable-information-pii-recognition"></a>Erkennung von personenbezogenen Informationen (PII)

Erstellen Sie eine neue Funktion namens `recognizePiiEntitiesExample()`, die den zuvor erstellten Client verwendet, und rufen Sie die zugehörige Funktion `recognizePiiEntities()` auf. Das zurückgegebene Objekt `PiiEntityCollection` enthält eine Liste mit Elementen vom Typ `PiiEntity` (sofern erfolgreich). Andernfalls enthält es eine Fehlermeldung (`errorMessage`). Es enthält außerdem den bearbeiteten Text, der aus dem Eingabetext besteht, in dem alle identifizierbaren Entitäten durch `*****` ersetzt wurden.

```java
static void recognizePiiEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String document = "My SSN is 859-98-0987";
    PiiEntityCollection piiEntityCollection = client.recognizePiiEntities(document);
    System.out.printf("Redacted Text: %s%n", piiEntityCollection.getRedactedText());
    piiEntityCollection.forEach(entity -> System.out.printf(
        "Recognized Personally Identifiable Information entity: %s, entity category: %s, entity subcategory: %s,"
            + " confidence score: %f.%n",
        entity.getText(), entity.getCategory(), entity.getSubcategory(), entity.getConfidenceScore()));
}
```

### <a name="output"></a>Output

```console
Redacted Text: My SSN is ***********
Recognized Personally Identifiable Information entity: 859-98-0987, entity category: U.S. Social Security Number (SSN), entity subcategory: null, confidence score: 0.650000.
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

> [!NOTE]
> In Version `3.0`:
> * Die Erkennung benannter Entitäten enthält separate Methoden zum Erkennen personenbezogener Informationen. 
> * Die Entitätsverknüpfung ist eine separate Anforderung.

Erstellen Sie eine neue Funktion namens `recognizeEntitiesExample()`, die den zuvor erstellten Client verwendet, und rufen Sie die zugehörige Funktion `recognizeEntities()` auf. Das zurückgegebene Objekt `CategorizedEntityCollection` enthält eine Liste mit Elementen vom Typ `CategorizedEntity` (sofern erfolgreich). Andernfalls enthält es eine Fehlermeldung (`errorMessage`).

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, score: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubcategory(),
            entity.getConfidenceScore());
    }
}
```

### <a name="output"></a>Output

```console
Recognized entity: trip, entity category: Event, entity sub-category: null, score: 0.61.
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.82.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8.
```

---

## <a name="entity-linking"></a>Entitätsverknüpfung

# <a name="version-31"></a>[Version 3.1](#tab/version-3-1)

Erstellen Sie eine neue Funktion namens `recognizeLinkedEntitiesExample()`, die den zuvor erstellten Client verwendet, und rufen Sie die zugehörige Funktion `recognizeLinkedEntities()` auf. Das zurückgegebene Objekt `LinkedEntityCollection` enthält eine Liste mit Elementen vom Typ `LinkedEntity` (sofern erfolgreich). Andernfalls enthält es eine Fehlermeldung (`errorMessage`). Da verknüpfte Entitäten eindeutig identifiziert werden, werden Vorkommen der gleichen Entität unter einem Objekt vom Typ `LinkedEntity` als Liste von Objekten des Typs `LinkedEntityMatch` gruppiert.


```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.";

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : client.recognizeLinkedEntities(text)) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
            linkedEntity.getName(),
            linkedEntity.getDataSourceEntityId(),
            linkedEntity.getUrl(),
            linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getMatches()) {
            System.out.printf("Text: %s, Score: %.2f, Offset: %s, Length: %s%n",
            linkedEntityMatch.getText(),
            linkedEntityMatch.getConfidenceScore(),
            linkedEntityMatch.getOffset(),
            linkedEntityMatch.getLength());
        }
    }
}
```

### <a name="output"></a>Output

```console
Linked Entities:
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.55, Offset: 9, Length: 0
Text: Microsoft, Score: 0.55, Offset: 9, Length: 150
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.63, Offset: 10, Length: 25
Text: Gates, Score: 0.63, Offset: 5, Length: 161
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.60, Offset: 10, Length: 40
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.32, Offset: 7, Length: 54
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.33, Offset: 5, Length: 89
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Score: 0.88, Offset: 11, Length: 116
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Erstellen Sie eine neue Funktion namens `recognizeLinkedEntitiesExample()`, die den zuvor erstellten Client verwendet, und rufen Sie die zugehörige Funktion `recognizeLinkedEntities()` auf. Das zurückgegebene Objekt `LinkedEntityCollection` enthält eine Liste mit Elementen vom Typ `LinkedEntity` (sofern erfolgreich). Andernfalls enthält es eine Fehlermeldung (`errorMessage`). Da verknüpfte Entitäten eindeutig identifiziert werden, werden Vorkommen der gleichen Entität unter einem Objekt vom Typ `LinkedEntity` als Liste von Objekten des Typs `LinkedEntityMatch` gruppiert.

```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.";

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : client.recognizeLinkedEntities(text)) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
            linkedEntity.getName(),
            linkedEntity.getDataSourceEntityId(),
            linkedEntity.getUrl(),
            linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getMatches()) {
            System.out.printf("Text: %s, Score: %.2f%n",
            linkedEntityMatch.getText(),
            linkedEntityMatch.getConfidenceScore());
        }
    }
}
```

### <a name="output"></a>Output

```console
Linked Entities:
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Score: 0.88
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.63
Text: Gates, Score: 0.63
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.60
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.55
Text: Microsoft, Score: 0.55
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.32
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.33
```

---

## <a name="key-phrase-extraction"></a>Schlüsselwortextraktion

Erstellen Sie eine neue Funktion namens `extractKeyPhrasesExample()`, die den zuvor erstellten Client verwendet, und rufen Sie die zugehörige Funktion `extractKeyPhrases()` auf. Das zurückgegebene Objekt `ExtractKeyPhraseResult` enthält eine Liste mit Schlüsselbegriffen (sofern erfolgreich). Andernfalls enthält es eine Fehlermeldung (`errorMessage`). Dieses Beispiel ist für die Versionen 3.0 und 3.1 der API gleich.

```java
static void extractKeyPhrasesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "My cat might need to see a veterinarian.";

    System.out.printf("Recognized phrases: %n");
    for (String keyPhrase : client.extractKeyPhrases(text)) {
        System.out.printf("%s%n", keyPhrase);
    }
}
```

### <a name="output"></a>Output

```console
Recognized phrases: 
cat
veterinarian
```
---

## <a name="extract-health-entities"></a>Extrahieren von Integritätsentitäten

# <a name="version-31"></a>[Version 3.1](#tab/version-3-1)

Sie können die Textanalyse verwenden, um eine asynchrone Anforderung zum Extrahieren von Integritätsentitäten aus Text durchzuführen. Nachfolgend sehen Sie ein einfaches Beispiel für diesen Vorgang. Ein komplexeres Beispiel können Sie auf [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics/lro/AnalyzeHealthcareEntities.java) finden.


```java
static void healthExample(TextAnalyticsClient client){
    List<TextDocumentInput> documents = Arrays.asList(
            new TextDocumentInput("0",
                    "Prescribed 100mg ibuprofen, taken twice daily."));

    AnalyzeHealthcareEntitiesOptions options = new AnalyzeHealthcareEntitiesOptions().setIncludeStatistics(true);

    SyncPoller<AnalyzeHealthcareEntitiesOperationDetail, AnalyzeHealthcareEntitiesPagedIterable>
            syncPoller = client.beginAnalyzeHealthcareEntities(documents, options, Context.NONE);

    System.out.printf("Poller status: %s.%n", syncPoller.poll().getStatus());
    syncPoller.waitForCompletion();

    // Task operation statistics
    AnalyzeHealthcareEntitiesOperationDetail operationResult = syncPoller.poll().getValue();
    System.out.printf("Operation created time: %s, expiration time: %s.%n",
            operationResult.getCreatedAt(), operationResult.getExpiresAt());
    System.out.printf("Poller status: %s.%n", syncPoller.poll().getStatus());

    for (AnalyzeHealthcareEntitiesResultCollection resultCollection : syncPoller.getFinalResult()) {
        // Model version
        System.out.printf(
                "Results of Azure Text Analytics \"Analyze Healthcare Entities\" Model, version: %s%n",
                resultCollection.getModelVersion());

        for (AnalyzeHealthcareEntitiesResult healthcareEntitiesResult : resultCollection) {
            System.out.println("Document ID = " + healthcareEntitiesResult.getId());
            System.out.println("Document entities: ");
            // Recognized healthcare entities
            for (HealthcareEntity entity : healthcareEntitiesResult.getEntities()) {
                System.out.printf(
                        "\tText: %s, normalized name: %s, category: %s, subcategory: %s, confidence score: %f.%n",
                        entity.getText(), entity.getNormalizedText(), entity.getCategory(),
                        entity.getSubcategory(), entity.getConfidenceScore());
            }
            // Recognized healthcare entity relation groups
            for (HealthcareEntityRelation entityRelation : healthcareEntitiesResult.getEntityRelations()) {
                System.out.printf("Relation type: %s.%n", entityRelation.getRelationType());
                for (HealthcareEntityRelationRole role : entityRelation.getRoles()) {
                    HealthcareEntity entity = role.getEntity();
                    System.out.printf("\tEntity text: %s, category: %s, role: %s.%n",
                            entity.getText(), entity.getCategory(), role.getName());
                }
            }
        }
    }
}
```

### <a name="output"></a>output

```console
Poller status: IN_PROGRESS.
Operation created time: 2021-07-20T19:45:50Z, expiration time: 2021-07-21T19:45:50Z.
Poller status: SUCCESSFULLY_COMPLETED.
Results of Azure Text Analytics "Analyze Healthcare Entities" Model, version: 2021-05-15
Document ID = 0
Document entities: 
    Text: 100mg, normalized name: null, category: Dosage, subcategory: null, confidence score: 1.000000.
    Text: ibuprofen, normalized name: ibuprofen, category: MedicationName, subcategory: null, confidence score: 1.000000.
    Text: twice daily, normalized name: null, category: Frequency, subcategory: null, confidence score: 1.000000.
Relation type: DosageOfMedication.
    Entity text: 100mg, category: Dosage, role: Dosage.
    Entity text: ibuprofen, category: MedicationName, role: Medication.
Relation type: FrequencyOfMedication.
    Entity text: ibuprofen, category: MedicationName, role: Medication.
    Entity text: twice daily, category: Frequency, role: Frequency.
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Dieses Feature ist in Version 3.0 nicht verfügbar.

---

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>Asynchrones Verwenden der API mit dem Analyze-Vorgang

# <a name="version-31"></a>[Version 3.1](#tab/version-3-1)

Mithilfe des Analysevorgangs können Sie asynchrone Batchanforderungen für Folgendes ausführen: NER, Schlüsselbegriffserkennung, Stimmungsanalyse und Erkennung personenbezogener Informationen. Nachfolgend sehen Sie ein einfaches Beispiel für einen Vorgang. Ein komplexeres Beispiel können Sie auf [GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples/Sample_AnalyzeActions.md) finden.

[!INCLUDE [Analyze Batch Action pricing](../analyze-operation-pricing-caution.md)]

Erstellen Sie die neue Funktion `analyzeBatchActionsExample()`, die die Funktion `beginAnalyzeBatchActions()` aufruft. Daraus resultiert ein zeitintensiver Vorgang, aus dem Ergebnisse abgerufen werden.

```java
static void analyzeActionsExample(TextAnalyticsClient client){
        List<TextDocumentInput> documents = new ArrayList<>();
        documents.add(new TextDocumentInput("0", "Microsoft was founded by Bill Gates and Paul Allen."));


        SyncPoller<AnalyzeActionsOperationDetail, AnalyzeActionsResultPagedIterable> syncPoller =
                client.beginAnalyzeActions(documents,
                        new TextAnalyticsActions().setDisplayName("Example analyze task")
                                .setRecognizeEntitiesActions(new RecognizeEntitiesAction())
                                .setExtractKeyPhrasesActions(
                                        new ExtractKeyPhrasesAction().setModelVersion("latest")),
                        new AnalyzeActionsOptions().setIncludeStatistics(false),
                        Context.NONE);

        // Task operation statistics details
        while (syncPoller.poll().getStatus() == LongRunningOperationStatus.IN_PROGRESS) {
            final AnalyzeActionsOperationDetail operationDetail = syncPoller.poll().getValue();
            System.out.printf("Action display name: %s, Successfully completed actions: %d, in-process actions: %d,"
                            + " failed actions: %d, total actions: %d%n",
                    operationDetail.getDisplayName(), operationDetail.getSucceededCount(),
                    operationDetail.getInProgressCount(), operationDetail.getFailedCount(),
                    operationDetail.getTotalCount());
        }

        syncPoller.waitForCompletion();

        Iterable<PagedResponse<AnalyzeActionsResult>> pagedResults = syncPoller.getFinalResult().iterableByPage();
        for (PagedResponse<AnalyzeActionsResult> perPage : pagedResults) {
            System.out.printf("Response code: %d, Continuation Token: %s.%n", perPage.getStatusCode(),
                    perPage.getContinuationToken());
            for (AnalyzeActionsResult actionsResult : perPage.getElements()) {
                System.out.println("Entities recognition action results:");
                for (RecognizeEntitiesActionResult actionResult : actionsResult.getRecognizeEntitiesResults()) {
                    if (!actionResult.isError()) {
                        for (RecognizeEntitiesResult documentResult : actionResult.getDocumentsResults()) {
                            if (!documentResult.isError()) {
                                for (CategorizedEntity entity : documentResult.getEntities()) {
                                    System.out.printf(
                                            "\tText: %s, category: %s, confidence score: %f.%n",
                                            entity.getText(), entity.getCategory(), entity.getConfidenceScore());
                                }
                            } else {
                                System.out.printf("\tCannot recognize entities. Error: %s%n",
                                        documentResult.getError().getMessage());
                            }
                        }
                    } else {
                        System.out.printf("\tCannot execute Entities Recognition action. Error: %s%n",
                                actionResult.getError().getMessage());
                    }
                }

                System.out.println("Key phrases extraction action results:");
                for (ExtractKeyPhrasesActionResult actionResult : actionsResult.getExtractKeyPhrasesResults()) {
                    if (!actionResult.isError()) {
                        for (ExtractKeyPhraseResult documentResult : actionResult.getDocumentsResults()) {
                            if (!documentResult.isError()) {
                                System.out.println("\tExtracted phrases:");
                                for (String keyPhrases : documentResult.getKeyPhrases()) {
                                    System.out.printf("\t\t%s.%n", keyPhrases);
                                }
                            } else {
                                System.out.printf("\tCannot extract key phrases. Error: %s%n",
                                        documentResult.getError().getMessage());
                            }
                        }
                    } else {
                        System.out.printf("\tCannot execute Key Phrases Extraction action. Error: %s%n",
                                actionResult.getError().getMessage());
                    }
                }
            }
        }
    }
```

Nachdem Sie das Beispiel zu Ihrer Anwendung hinzugefügt haben, können Sie es in der Methode `main()` aufrufen.

```java
analyzeBatchActionsExample(client);
```

### <a name="output"></a>Ausgabe

```console
Action display name: Example analyze task, Successfully completed actions: 1, in-process actions: 1, failed actions: 0, total actions: 2
Response code: 200, Continuation Token: null.
Entities recognition action results:
    Text: Microsoft, category: Organization, confidence score: 1.000000.
    Text: Bill Gates, category: Person, confidence score: 1.000000.
    Text: Paul Allen, category: Person, confidence score: 1.000000.
Key phrases extraction action results:
    Extracted phrases:
        Bill Gates.
        Paul Allen.
        Microsoft.
```

Sie können den Analysevorgang auch verwenden, um NER, Schlüsselbegriffserkennung, Stimmungsanalyse und die Erkennung personenbezogener Informationen durchzuführen. Das [Analyze-Beispiel](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics/lro/AnalyzeActionsAsync.java) finden Sie auf GitHub.

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Dieses Feature ist in Version 3.0 nicht verfügbar.

---
