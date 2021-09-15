---
title: 'Schnellstart: Textanalyse-Clientbibliothek für C# | Microsoft-Dokumentation'
description: Steigen Sie in die Textanalyse-Clientbibliothek für C# ein.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 08/17/2021
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 2b4a3a8049c7d6752c581200277a21d794a248c9
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864856"
---
<a name="HOLTop"></a>

# <a name="version-32-preview1"></a>[Version 3.2-preview.1](#tab/version-3-2)

[Referenzdokumentation zu v3.2-preview](/dotnet/api/azure.ai.textanalytics?preserve-view=true&view=azure-dotnet-preview) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics) | [Paket (NuGet) für v3.2-preview](https://www.nuget.org/packages/Azure.AI.TextAnalytics/5.2.0-beta.1) | [Beispiele für v3.2-preview](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

# <a name="version-31"></a>[Version 3.1](#tab/version-3-1)

[Referenzdokumentation zu v3.1](/dotnet/api/azure.ai.textanalytics?preserve-view=true&view=azure-dotnet) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics) | [Paket (NuGet) für v3.1](https://www.nuget.org/packages/Azure.AI.TextAnalytics/5.1.0) | [Beispiele](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

---

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* [Visual Studio-IDE](https://visualstudio.microsoft.com/vs/)
* [!INCLUDE [contributor-requirement](../../../includes/quickstarts/contributor-requirement.md)]
* Sobald Sie über Ihr Azure-Abonnement verfügen, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Erstellen einer Textanalyseressource"  target="_blank"> erstellen Sie eine Textanalyseressource </a> im Azure-Portal, um Ihren Schlüssel und Endpunkt zu erhalten.  Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und den Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Textanalyse-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.
* Sie benötigen eine Textanalyse-Ressource des Standard-Tarifs (S), um das Analyze-Feature nutzen zu können.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-net-core-application"></a>Erstellen einer neuen .NET Core-Anwendung

Erstellen Sie über die Visual Studio-IDE eine neue .NET Core-Konsolenanwendung. Dadurch wird ein Projekt vom Typ „Hallo Welt“ mit einer einzelnen C#-Quelldatei (*program.cs*) erstellt.

# <a name="version-32-preview1"></a>[Version 3.2-preview.1](#tab/version-3-2)

Installieren Sie die Clientbibliothek, indem Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die Projektmappe klicken und **NuGet-Pakete verwalten** auswählen. Wählen Sie im daraufhin geöffneten Paket-Manager die Option **Durchsuchen** aus, und suchen Sie nach `Azure.AI.TextAnalytics`. Stellen Sie sicher, dass **Vorabversion einbeziehen** aktiviert ist. Wählen Sie die Version `5.2.0-beta.1` und anschließend **Installieren** aus. Sie können auch die [Paket-Manager-Konsole](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package) verwenden.

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

Installieren Sie die Clientbibliothek, indem Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die Projektmappe klicken und **NuGet-Pakete verwalten** auswählen. Wählen Sie im daraufhin geöffneten Paket-Manager die Option **Durchsuchen** aus, und suchen Sie nach `Azure.AI.TextAnalytics`. Wählen Sie die Version `5.1.0` und anschließend **Installieren** aus. Sie können auch die [Paket-Manager-Konsole](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package) verwenden.

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

Öffnen Sie die Datei *program.cs*, und fügen Sie die folgenden `using`-Anweisungen hinzu:

```csharp
using Azure;
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

Erstellen Sie in der `Program`-Klasse der Anwendung Variablen für den Schlüssel und Endpunkt Ihrer Ressource.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly AzureKeyCredential credentials = new AzureKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Ersetzen Sie die `Main`-Methode der Anwendung. Die hier aufgerufenen Methoden definieren Sie später. 

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    SentimentAnalysisWithOpinionMiningExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityLinkingExample(client);
    RecognizePIIExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

## <a name="object-model"></a>Objektmodell

Der Textanalyse-Client ist ein Objekt vom Typ `TextAnalyticsClient`, das sich mit Ihrem Schlüssel bei Azure authentifiziert und Funktionen bereitstellt, um Text als einzelne Zeichenfolgen oder als Batch zu akzeptieren. Sie können Text synchron oder asynchron an die API senden. Das Antwortobjekt enthält die Analyseinformationen für die einzelnen gesendeten Dokumente. 

Wenn Sie Version `3.x` des Diensts verwenden, können Sie eine optionale `TextAnalyticsClientOptions`-Instanz nutzen, um den Client mit verschiedenen Standardeinstellungen wie Standardsprache oder Länder-/Regionshinweis zu initialisieren. Sie können für die Authentifizieren auch ein Azure Active Directory-Token verwenden. 

[!INCLUDE [text-analytics-character-limits](../character-limits.md)]

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Stellen Sie sicher, dass mit Ihrer zuvor erstellten Main-Methode ein neues Clientobjekt mit Ihrem Endpunkt und Ihren Anmeldeinformationen erstellt wird.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```


## <a name="sentiment-analysis"></a>Stimmungsanalyse

Erstellen Sie eine neue Funktion namens `SentimentAnalysisExample()`, die den zuvor erstellten Client verwendet, und rufen Sie die zugehörige Funktion `AnalyzeSentiment()` auf. Das zurückgegebene Objekt vom Typ `Response<DocumentSentiment>` enthält die Stimmungsbezeichnung und den Stimmungswert des gesamten Eingabedokuments sowie eine Standpunktanalyse für jeden Satz (sofern erfolgreich). Im Falle eines Fehlers wird ein Fehler vom Typ `RequestFailedException` ausgelöst.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tText: \"{sentence.Text}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>Output

```console
Document sentiment: Positive

        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

## <a name="opinion-mining"></a>Opinion Mining

Erstellen Sie eine neue Funktion namens `SentimentAnalysisWithOpinionMiningExample()`, die den zuvor erstellten Client verwendet, und rufen Sie die zugehörige `AnalyzeSentimentBatch()` Funktion mit`IncludeOpinionMining` der Option `AnalyzeSentimentOptions` bag auf. Das zurückgegebene `AnalyzeSentimentResultCollection`-Objekt enthält die Auflistung von `AnalyzeSentimentResult`, in der `Response<DocumentSentiment>` darstellt ist. Der Unterschied zwischen `SentimentAnalysis()` und `SentimentAnalysisWithOpinionMiningExample()` besteht darin, dass das letztere in jedem Satz `SentenceOpinion` enthält, was ein analysiertes Ziel und die zugehörige(n) Bewertung(en) anzeigt. Im Falle eines Fehlers wird ein Fehler vom Typ `RequestFailedException` ausgelöst.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```csharp
static void SentimentAnalysisWithOpinionMiningExample(TextAnalyticsClient client)
{
    var documents = new List<string>
    {
        "The food and service were unacceptable, but the concierge were nice."
    };

    AnalyzeSentimentResultCollection reviews = client.AnalyzeSentimentBatch(documents, options: new AnalyzeSentimentOptions()
    {
        IncludeOpinionMining = true
    });

    foreach (AnalyzeSentimentResult review in reviews)
    {
        Console.WriteLine($"Document sentiment: {review.DocumentSentiment.Sentiment}\n");
        Console.WriteLine($"\tPositive score: {review.DocumentSentiment.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {review.DocumentSentiment.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {review.DocumentSentiment.ConfidenceScores.Neutral:0.00}\n");
        foreach (SentenceSentiment sentence in review.DocumentSentiment.Sentences)
        {
            Console.WriteLine($"\tText: \"{sentence.Text}\"");
            Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
            Console.WriteLine($"\tSentence positive score: {sentence.ConfidenceScores.Positive:0.00}");
            Console.WriteLine($"\tSentence negative score: {sentence.ConfidenceScores.Negative:0.00}");
            Console.WriteLine($"\tSentence neutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");

            foreach (SentenceOpinion sentenceOpinion in sentence.Opinions)
            {
                Console.WriteLine($"\tTarget: {sentenceOpinion.Target.Text}, Value: {sentenceOpinion.Target.Sentiment}");
                Console.WriteLine($"\tTarget positive score: {sentenceOpinion.Target.ConfidenceScores.Positive:0.00}");
                Console.WriteLine($"\tTarget negative score: {sentenceOpinion.Target.ConfidenceScores.Negative:0.00}");
                foreach (AssessmentSentiment assessment in sentenceOpinion.Assessments)
                {
                    Console.WriteLine($"\t\tRelated Assessment: {assessment.Text}, Value: {assessment.Sentiment}");
                    Console.WriteLine($"\t\tRelated Assessment positive score: {assessment.ConfidenceScores.Positive:0.00}");
                    Console.WriteLine($"\t\tRelated Assessment negative score: {assessment.ConfidenceScores.Negative:0.00}");
                }
            }
        }
        Console.WriteLine($"\n");
    }
}
```

### <a name="output"></a>Output

```console
Document sentiment: Positive

        Positive score: 0.84
        Negative score: 0.16
        Neutral score: 0.00

        Text: "The food and service were unacceptable, but the concierge were nice."
        Sentence sentiment: Positive
        Sentence positive score: 0.84
        Sentence negative score: 0.16
        Sentence neutral score: 0.00

        Target: food, Value: Negative
        Target positive score: 0.01
        Target negative score: 0.99
                Related Assessment: unacceptable, Value: Negative
                Related Assessment positive score: 0.01
                Related Assessment negative score: 0.99
        Target: service, Value: Negative
        Target positive score: 0.01
        Target negative score: 0.99
                Related Assessment: unacceptable, Value: Negative
                Related Assessment positive score: 0.01
                Related Assessment negative score: 0.99
        Target: concierge, Value: Positive
        Target positive score: 1.00
        Target negative score: 0.00
                Related Assessment: nice, Value: Positive
                Related Assessment positive score: 1.00
                Related Assessment negative score: 0.00

Press any key to exit.
```

## <a name="language-detection"></a>Spracherkennung

Erstellen Sie eine neue Funktion namens `LanguageDetectionExample()`, die den zuvor erstellten Client verwendet, und rufen Sie die zugehörige Funktion `DetectLanguage()` auf. Das zurückgegebene Objekt vom Typ `Response<DetectedLanguage>` enthält die erkannte Sprache sowie den zugehörigen Namen und ISO-6391-Code. Im Falle eines Fehlers wird ein Fehler vom Typ `RequestFailedException` ausgelöst.

> [!Tip]
> In einigen Fällen ist es unter Umständen schwierig, die Sprachen basierend auf den Eingaben eindeutig zu bestimmen. Mithilfe des Parameters `countryHint` können Sie einen zweistelligen Länder-/Regionscode angeben. Die API verwendet standardmäßig „US“ als Wert für „countryHint“. Wenn Sie dieses Verhalten entfernen möchten, können Sie diesen Parameter zurücksetzen, indem Sie seinen Wert auf eine leere Zeichenfolge (`countryHint = ""`) festlegen. Wenn Sie einen anderen Standardwert festlegen möchten, legen Sie die Eigenschaft `TextAnalyticsClientOptions.DefaultCountryHint` fest, und übergeben Sie sie während der Initialisierung des Clients.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    DetectedLanguage detectedLanguage = client.DetectLanguage("Ce document est rédigé en Français.");
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Output

```console
Language:
        French, ISO-6391: fr
```

## <a name="named-entity-recognition-ner"></a>Erkennung benannter Entitäten (NER)

Erstellen Sie eine neue Funktion namens `EntityRecognitionExample()`, die den zuvor erstellten Client verwendet, rufen Sie die zugehörige Funktion `RecognizeEntities()` auf, und durchlaufen Sie die Ergebnisse. Das zurückgegebene `Response<CategorizedEntityCollection>`-Objekt enthält die Sammlung der erkannten `CategorizedEntity`-Entitäten. Im Falle eines Fehlers wird ein Fehler vom Typ `RequestFailedException` ausgelöst.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tScore: {entity.ConfidenceScore:F2},\tLength: {entity.Length},\tOffset: {entity.Offset}\n");
    }
}
```

### <a name="output"></a>Output

```console
Named Entities:
        Text: trip,     Category: Event,        Sub-Category:
                Score: 0.61,    Length: 4,      Offset: 18

        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Score: 0.82,    Length: 7,      Offset: 26

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Score: 0.80,    Length: 9,      Offset: 34
```

## <a name="personally-identifiable-information-pii-recognition"></a>Erkennung von personenbezogenen Informationen (PII)

Erstellen Sie eine neue Funktion namens `RecognizePIIExample()`, die den zuvor erstellten Client verwendet, rufen Sie die zugehörige Funktion `RecognizePiiEntities()` auf, und durchlaufen Sie die Ergebnisse. Die zurückgegebene `PiiEntityCollection` stellt die Liste der erkannten PII-Entitäten dar. Im Falle eines Fehlers wird ein Fehler vom Typ `RequestFailedException` ausgelöst.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```csharp
static void RecognizePIIExample(TextAnalyticsClient client)
{
    string document = "A developer with SSN 859-98-0987 whose phone number is 800-102-1100 is building tools with our APIs.";

    PiiEntityCollection entities = client.RecognizePiiEntities(document).Value;

    Console.WriteLine($"Redacted Text: {entities.RedactedText}");
    if (entities.Count > 0)
    {
        Console.WriteLine($"Recognized {entities.Count} PII entit{(entities.Count > 1 ? "ies" : "y")}:");
        foreach (PiiEntity entity in entities)
        {
            Console.WriteLine($"Text: {entity.Text}, Category: {entity.Category}, SubCategory: {entity.SubCategory}, Confidence score: {entity.ConfidenceScore}");
        }
    }
    else
    {
        Console.WriteLine("No entities were found.");
    }
}
```

### <a name="output"></a>Output

```console
Redacted Text: A developer with SSN *********** whose phone number is ************ is building tools with our APIs.
Recognized 2 PII entities:
Text: 859-98-0987, Category: U.S. Social Security Number (SSN), SubCategory: , Confidence score: 0.65
Text: 800-102-1100, Category: Phone Number, SubCategory: , Confidence score: 0.8
```

## <a name="entity-linking"></a>Entitätsverknüpfung

Erstellen Sie eine neue Funktion namens `EntityLinkingExample()`, die den zuvor erstellten Client verwendet, rufen Sie die zugehörige Funktion `RecognizeLinkedEntities()` auf, und durchlaufen Sie die Ergebnisse. Das zurückgegebene `Response<LinkedEntityCollection>`-Objekt enthält die Sammlung der erkannten `LinkedEntity`-Entitäten. Im Falle eines Fehlers wird ein Fehler vom Typ `RequestFailedException` ausgelöst. Da verknüpfte Entitäten eindeutig identifiziert werden, werden Vorkommen der gleichen Entität unter einem Objekt vom Typ `LinkedEntity` als Liste von Objekten des Typs `LinkedEntityMatch` gruppiert.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.DataSourceEntityId},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tScore: {match.ConfidenceScore:F2}");
            Console.WriteLine($"\t\tLength: {match.Length}");
            Console.WriteLine($"\t\tOffset: {match.Offset}\n");
        }
    }
}
```

### <a name="output"></a>Output

```console
Linked Entities:
        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Score: 0.55
                Length: 9
                Offset: 0

                Text: Microsoft
                Score: 0.55
                Length: 9
                Offset: 150

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Score: 0.63
                Length: 10
                Offset: 25

                Text: Gates
                Score: 0.63
                Length: 5
                Offset: 161

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Score: 0.60
                Length: 10
                Offset: 40

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Score: 0.32
                Length: 7
                Offset: 54

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Score: 0.33
                Length: 5
                Offset: 89

        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Score: 0.88
                Length: 11
                Offset: 116
```

## <a name="key-phrase-extraction"></a>Schlüsselwortextraktion

Erstellen Sie eine neue Funktion namens `KeyPhraseExtractionExample()`, die den zuvor erstellten Client verwendet, und rufen Sie die zugehörige Funktion `ExtractKeyPhrases()` auf. Das zurückgegebene Objekt vom Typ `<Response<KeyPhraseCollection>` enthält die Liste erkannter Schlüsselbegriffe. Im Falle eines Fehlers wird ein Fehler vom Typ `RequestFailedException` ausgelöst.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>Output

```console
Key phrases:
    cat
    veterinarian
```

## <a name="extract-health-entities"></a>Extrahieren von Integritätsentitäten

[!INCLUDE [health operation pricing](../health-operation-pricing-caution.md)]

Fügen Sie Ihrer C#-Datei die folgenden Verwendungsanweisungen hinzu, sofern noch nicht geschehen:

```csharp
using System.Threading.Tasks;
using System.Collections.Generic;
using System.Linq;
```

Sie können die Textanalyse verwenden, um eine asynchrone Anforderung zum Extrahieren von Integritätsentitäten aus dem Text durchzuführen. Nachfolgend sehen Sie ein einfaches Beispiel für diesen Vorgang. Ein komplexeres Beispiel können Sie auf [GitHub](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/textanalytics/Azure.AI.TextAnalytics/samples/Sample7_AnalyzeHealthcareEntities.md) finden.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```csharp
static async Task healthExample(TextAnalyticsClient client)
{
    string document = "Prescribed 100mg ibuprofen, taken twice daily.";

    List<string> batchInput = new List<string>()
    {
        document
    };
    AnalyzeHealthcareEntitiesOperation healthOperation = await client.StartAnalyzeHealthcareEntitiesAsync(batchInput);
    await healthOperation.WaitForCompletionAsync();

    await foreach (AnalyzeHealthcareEntitiesResultCollection documentsInPage in healthOperation.Value)
    {
        Console.WriteLine($"Results of Azure Text Analytics \"Healthcare Async\" Model, version: \"{documentsInPage.ModelVersion}\"");
        Console.WriteLine("");

        foreach (AnalyzeHealthcareEntitiesResult entitiesInDoc in documentsInPage)
        {
            if (!entitiesInDoc.HasError)
            {
                foreach (var entity in entitiesInDoc.Entities)
                {
                    // view recognized healthcare entities
                    Console.WriteLine($"  Entity: {entity.Text}");
                    Console.WriteLine($"  Category: {entity.Category}");
                    Console.WriteLine($"  Offset: {entity.Offset}");
                    Console.WriteLine($"  Length: {entity.Length}");
                    Console.WriteLine($"  NormalizedText: {entity.NormalizedText}");
                }
                Console.WriteLine($"  Found {entitiesInDoc.EntityRelations.Count} relations in the current document:");
                Console.WriteLine("");

                // view recognized healthcare relations
                foreach (HealthcareEntityRelation relations in entitiesInDoc.EntityRelations)
                {
                    Console.WriteLine($"    Relation: {relations.RelationType}");
                    Console.WriteLine($"    For this relation there are {relations.Roles.Count} roles");

                    // view relation roles
                    foreach (HealthcareEntityRelationRole role in relations.Roles)
                    {
                        Console.WriteLine($"      Role Name: {role.Name}");

                        Console.WriteLine($"      Associated Entity Text: {role.Entity.Text}");
                        Console.WriteLine($"      Associated Entity Category: {role.Entity.Category}");
                        Console.WriteLine("");
                    }
                    Console.WriteLine("");
                }
            }
            else
            {
                Console.WriteLine("  Error!");
                Console.WriteLine($"  Document error code: {entitiesInDoc.Error.ErrorCode}.");
                Console.WriteLine($"  Message: {entitiesInDoc.Error.Message}");
            }
            Console.WriteLine("");
        }
    }
}
```

Nachdem Sie das Beispiel zu Ihrer Anwendung hinzugefügt haben, können Sie es mithilfe von `await` in der Methode `main()` aufrufen. Da der Analysevorgang asynchron ist, müssen Sie Ihre `Main()` Methode auf den Typ `async Task` aktualisieren.

```csharp
static async Task Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    await healthExample(client);
}
```

```console
Results of Azure Text Analytics "Healthcare Async" Model, version: "2021-05-15"

  Entity: 100mg
  Category: Dosage
  Offset: 11
  Length: 5
  NormalizedText:
  Entity: ibuprofen
  Category: MedicationName
  Offset: 17
  Length: 9
  NormalizedText: ibuprofen
  Entity: twice daily
  Category: Frequency
  Offset: 34
  Length: 11
  NormalizedText:
  Found 2 relations in the current document:

    Relation: DosageOfMedication
    For this relation there are 2 roles
      Role Name: Dosage
      Associated Entity Text: 100mg
      Associated Entity Category: Dosage

      Role Name: Medication
      Associated Entity Text: ibuprofen
      Associated Entity Category: MedicationName


    Relation: FrequencyOfMedication
    For this relation there are 2 roles
      Role Name: Medication
      Associated Entity Text: ibuprofen
      Associated Entity Category: MedicationName

      Role Name: Frequency
      Associated Entity Text: twice daily
      Associated Entity Category: Frequency
```


## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>Asynchrones Verwenden der API mit dem Analyze-Vorgang

Mithilfe des Analysevorgangs können Sie asynchrone Batchanforderungen für Folgendes ausführen: NER, Schlüsselbegriffserkennung, Stimmungsanalyse und Erkennung personenbezogener Informationen. Nachfolgend sehen Sie ein einfaches Beispiel für einen Vorgang. Ein komplexeres Beispiel können Sie auf [GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples/Sample_AnalyzeActions.md) finden.

[!INCLUDE [Analyze operation pricing](../analyze-operation-pricing-caution.md)]

Fügen Sie Ihrer C#-Datei die folgenden Verwendungsanweisungen hinzu, sofern noch nicht geschehen:

```csharp
using System.Threading.Tasks;
using System.Collections.Generic;
using System.Linq;
```

Erstellen Sie eine neue Funktion namens `AnalyzeOperationExample()`, die den zuvor erstellten Client verwendet, und rufen Sie die zugehörige Funktion `StartAnalyzeBatchActionsAsync()` auf. Der zurückgegebene Vorgang enthält ein `AnalyzeBatchActionsResult`-Objekt. Da es sich um einen zeitintensiven Vorgang handelt, muss `await` in der `operation.WaitForCompletionAsync()`-Methode für den Wert aktualisiert werden. Sobald `WaitForCompletionAsync()` ausgeführt wurde, sollte die Sammlung in `operation.Value` aktualisiert sein. Im Falle eines Fehlers wird ein Fehler vom Typ `RequestFailedException` ausgelöst.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]


```csharp
static async Task AnalyzeOperationExample(TextAnalyticsClient client)
{
    string inputText = "Microsoft was founded by Bill Gates and Paul Allen.";

    var batchDocuments = new List<string> { inputText };


    TextAnalyticsActions actions = new TextAnalyticsActions()
    {
        RecognizeEntitiesActions = new List<RecognizeEntitiesAction>() { new RecognizeEntitiesAction() },
        ExtractKeyPhrasesActions = new List<ExtractKeyPhrasesAction>() { new ExtractKeyPhrasesAction() },
        DisplayName = "Analyze Operation Quick Start Example"
    };

    AnalyzeActionsOperation operation = await client.StartAnalyzeActionsAsync(batchDocuments, actions);

    await operation.WaitForCompletionAsync();

    Console.WriteLine($"Status: {operation.Status}");
    Console.WriteLine($"Created On: {operation.CreatedOn}");
    Console.WriteLine($"Expires On: {operation.ExpiresOn}");
    Console.WriteLine($"Last modified: {operation.LastModified}");
    if (!string.IsNullOrEmpty(operation.DisplayName))
        Console.WriteLine($"Display name: {operation.DisplayName}");
    Console.WriteLine($"  Succeeded actions: {operation.ActionsSucceeded}");
    Console.WriteLine($"  Failed actions: {operation.ActionsFailed}");
    Console.WriteLine($"  In progress actions: {operation.ActionsInProgress}");

    await foreach (AnalyzeActionsResult documentsInPage in operation.Value)
    {
        RecognizeEntitiesResultCollection entitiesResult = documentsInPage.RecognizeEntitiesResults.FirstOrDefault().DocumentsResults;
        ExtractKeyPhrasesResultCollection keyPhrasesResults = documentsInPage.ExtractKeyPhrasesResults.FirstOrDefault().DocumentsResults;

        Console.WriteLine("Recognized Entities");

        foreach (RecognizeEntitiesResult result in entitiesResult)
        {
            Console.WriteLine($"  Recognized the following {result.Entities.Count} entities:");

            foreach (CategorizedEntity entity in result.Entities)
            {
                Console.WriteLine($"  Entity: {entity.Text}");
                Console.WriteLine($"  Category: {entity.Category}");
                Console.WriteLine($"  Offset: {entity.Offset}");
                Console.WriteLine($"  Length: {entity.Length}");
                Console.WriteLine($"  ConfidenceScore: {entity.ConfidenceScore}");
                Console.WriteLine($"  SubCategory: {entity.SubCategory}");
            }
            Console.WriteLine("");
        }

        Console.WriteLine("Key Phrases");
        
        foreach (ExtractKeyPhrasesResult documentResults in keyPhrasesResults)
        {
            Console.WriteLine($"  Recognized the following {documentResults.KeyPhrases.Count} Keyphrases:");

            foreach (string keyphrase in documentResults.KeyPhrases)
            {
                Console.WriteLine($"  {keyphrase}");
            }
            Console.WriteLine("");
        }
        
    }
}
```

Nachdem Sie das Beispiel zu Ihrer Anwendung hinzugefügt haben, können Sie es mithilfe von `await` in der Methode `main()` aufrufen. Da der Analysevorgang asynchron ist, müssen Sie Ihre `Main()` Methode auf den Typ `async Task` aktualisieren.

```csharp
static async Task Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    await AnalyzeOperationExample(client);
}
```

### <a name="output"></a>Ausgabe

```console
Status: succeeded
Created On: 3/10/2021 2:25:01 AM +00:00
Expires On: 3/11/2021 2:25:01 AM +00:00
Last modified: 3/10/2021 2:25:05 AM +00:00
Display name: Analyze Operation Quick Start Example
Total actions: 1
  Succeeded actions: 1
  Failed actions: 0
  In progress actions: 0
Recognized Entities
    Recognized the following 3 entities:
    Entity: Microsoft
    Category: Organization
    Offset: 0
    ConfidenceScore: 0.83
    SubCategory: 
    Entity: Bill Gates
    Category: Person
    Offset: 25
    ConfidenceScore: 0.85
    SubCategory: 
    Entity: Paul Allen
    Category: Person
    Offset: 40
    ConfidenceScore: 0.9
    SubCategory: 
```

## <a name="text-summarization"></a>Textzusammenfassung

# <a name="version-32-preview1"></a>[Version 3.2-preview.1](#tab/version-3-2)

Sie können die Textanalyse verwenden, um große Textblöcke zusammenzufassen. 

Fügen Sie Ihrer C#-Datei die folgenden Verwendungsanweisungen hinzu, sofern noch nicht geschehen:

```csharp
using System.Threading.Tasks;
using System.Collections.Generic;
```

Erstellen Sie eine neue Funktion mit dem Namen `TextSummarizationExample()`, die den Client als Argument akzeptiert. Daraus resultiert ein zeitintensiver Vorgang, aus dem Ergebnisse abgerufen werden. 

```csharp
static async Task TextSummarizationExample(TextAnalyticsClient client)
{
    string document = @"The extractive summarization feature in Text Analytics uses natural language processing techniques to locate key sentences in an unstructured text document. 
        These sentences collectively convey the main idea of the document. This feature is provided as an API for developers. 
        They can use it to build intelligent solutions based on the relevant information extracted to support various use cases. 
        In the public preview, extractive summarization supports several languages. It is based on pretrained multilingual transformer models, part of our quest for holistic representations. 
        It draws its strength from transfer learning across monolingual and harness the shared nature of languages to produce models of improved quality and efficiency." ;

    // Prepare analyze operation input. You can add multiple documents to this list and perform the same
    // operation to all of them.
    var batchInput = new List<string>
    {
        document
    };

    TextAnalyticsActions actions = new TextAnalyticsActions()
    {
        ExtractSummaryActions = new List<ExtractSummaryAction>() { new ExtractSummaryAction() }
    };

    // Start analysis process.
    AnalyzeActionsOperation operation = await client.StartAnalyzeActionsAsync(batchInput, actions);
    await operation.WaitForCompletionAsync();
    // View operation status.
    Console.WriteLine($"AnalyzeActions operation has completed");
    Console.WriteLine();

    Console.WriteLine($"Created On   : {operation.CreatedOn}");
    Console.WriteLine($"Expires On   : {operation.ExpiresOn}");
    Console.WriteLine($"Id           : {operation.Id}");
    Console.WriteLine($"Status       : {operation.Status}");

    Console.WriteLine();
    // View operation results.
    await foreach (AnalyzeActionsResult documentsInPage in operation.Value)
    {
        IReadOnlyCollection<ExtractSummaryActionResult> summaryResults = documentsInPage.ExtractSummaryResults;

        foreach (ExtractSummaryActionResult summaryActionResults in summaryResults)
        {
            if (summaryActionResults.HasError)
            {
                Console.WriteLine($"  Error!");
                Console.WriteLine($"  Action error code: {summaryActionResults.Error.ErrorCode}.");
                Console.WriteLine($"  Message: {summaryActionResults.Error.Message}");
                continue;
            }

            foreach (ExtractSummaryResult documentResults in summaryActionResults.DocumentsResults)
            {
                if (documentResults.HasError)
                {
                    Console.WriteLine($"  Error!");
                    Console.WriteLine($"  Document error code: {documentResults.Error.ErrorCode}.");
                    Console.WriteLine($"  Message: {documentResults.Error.Message}");
                    continue;
                }

                Console.WriteLine($"  Extracted the following {documentResults.Sentences.Count} sentence(s):");
                Console.WriteLine();

                foreach (SummarySentence sentence in documentResults.Sentences)
                {
                    Console.WriteLine($"  Sentence: {sentence.Text}");
                    Console.WriteLine();
                }
            }
        }
    }

}

```

Nachdem Sie das Beispiel zu Ihrer Anwendung hinzugefügt haben, können Sie es mithilfe von `await` in der Methode `main()` aufrufen. Da der Analysevorgang asynchron ist, müssen Sie Ihre `Main()` Methode auf den Typ `async Task` aktualisieren.

```csharp
static async Task Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    await TextSummarizationExample(client);
}
```

### <a name="output"></a>Ausgabe

```console
AnalyzeActions operation has completed

Created On   : 8/17/2021 9:06:07 PM +00:00
Expires On   : 8/18/2021 9:06:07 PM +00:00
Id           : 70dc2e88-e930-483e-9d96-1ba5f0f27f18
Status       : succeeded

Extracted the following 3 sentence(s):

Sentence: The extractive summarization feature in Text Analytics uses natural language processing techniques to locate key sentences in an unstructured text document.
Sentence: This feature is provided as an API for developers.
Sentence: They can use it to build intelligent solutions based on the relevant information extracted to support various use cases.

```

# <a name="version-31"></a>[Version 3.1](#tab/version-3-1)

Dieses Feature ist in Version 3.1 nicht verfügbar.

---
