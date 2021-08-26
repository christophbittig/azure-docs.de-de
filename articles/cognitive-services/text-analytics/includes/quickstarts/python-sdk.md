---
author: aahill
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/15/2021
ms.author: aahi
ms.openlocfilehash: 6ece1121244341acb8f81d11491acf27f4c9fec8
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122442500"
---
<a name="HOLTop"></a>

# <a name="version-31"></a>[Version 3.1](#tab/version-3-1)

[v3.1-Referenzdokumentation](/python/api/azure-ai-textanalytics/azure.ai.textanalytics?preserve-view=true&view=azure-python-preview) | [v3.1-Bibliotheksquellcode](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/textanalytics/azure-ai-textanalytics) | [v3.1-Paket (PiPy)](https://pypi.org/project/azure-ai-textanalytics/5.1.0/) | [v3.1-Beispiele](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/textanalytics/azure-ai-textanalytics/samples)

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

[v3-Referenzdokumentation](/python/api/azure-ai-textanalytics/azure.ai.textanalytics) | [Quellcode der v3-Bibliothek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | [v3-Paket (PiPy)](https://pypi.org/project/azure-ai-textanalytics/) | [v3-Beispiele](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

---

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* [!INCLUDE [contributor-requirement](../../../includes/quickstarts/contributor-requirement.md)]
* Sobald Sie über Ihr Azure-Abonnement verfügen, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Erstellen einer Textanalyseressource"  target="_blank"> erstellen Sie eine Textanalyseressource </a> im Azure-Portal, um Ihren Schlüssel und Endpunkt zu erhalten. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und den Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Textanalyse-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.
* Sie benötigen eine Textanalyse-Ressource des Standard-Tarifs (S), um das Analyze-Feature nutzen zu können.

## <a name="setting-up"></a>Einrichten

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek

Nach der Installation von Python, können Sie die Clientbibliothek mit Folgendem installieren:

# <a name="version-31"></a>[Version 3.1](#tab/version-3-1)

```console
pip install azure-ai-textanalytics==5.1.0
```

> [!TIP]
> Möchten Sie sich sofort die gesamte Codedatei für die Schnellstartanleitung ansehen? Die Datei steht [auf GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py) zur Verfügung. Dort finden Sie die Codebeispiele aus dieser Schnellstartanleitung. 

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

```console
pip install --upgrade azure-ai-textanalytics
```

> [!TIP]
> Möchten Sie sich sofort die gesamte Codedatei für die Schnellstartanleitung ansehen? Die Datei steht [auf GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py) zur Verfügung. Dort finden Sie die Codebeispiele aus dieser Schnellstartanleitung. 


---

### <a name="create-a-new-python-application"></a>Erstellen einer neuen Python-Anwendung

Erstellen Sie eine neue Python-Datei, und erstellen Sie Variablen für den Azure-Endpunkt und den Abonnementschlüssel Ihrer Ressource.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Objektmodell

# <a name="version-31"></a>[Version 3.1](#tab/version-3-1)

Der Textanalyseclient ist ein Objekt vom Typ `TextAnalyticsClient`, das sich bei Azure authentifiziert. Der Client bietet verschiedene Methoden, um Text zu analysieren. 

Bei der Verarbeitung wird Text an die API in Form einer Liste von `documents` gesendet, die ihrerseits entweder eine Zeichenfolgenliste, eine Liste in einer wörterbuchähnlichen Darstellung oder eine Liste von `TextDocumentInput/DetectLanguageInput` sind. Ein `dict-like`-Objekt enthält eine Kombination aus `id`, `text` und `language/country_hint`. Das `text`-Attribut speichert den zu analysierenden Text in der Ausgangssprache (`country_hint`), und `id` kann ein beliebiger Wert sein. 

Das Antwortobjekt ist eine Liste mit den Analyseinformationen für jedes Dokument. 

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Der Textanalyseclient ist ein Objekt vom Typ `TextAnalyticsClient`, das sich bei Azure mit Ihrem Schlüssel authentifiziert. Der Client bietet verschiedene Methoden, um Text als Batch zu analysieren. 

Bei der Batchverarbeitung wird Text als Liste vom Typ `documents` an die API gesendet. Hierbei handelt es sich um Objekte vom Typ `dictionary`, die je nach verwendeter Methode eine Kombination aus den Attributen `id`, `text` und `language` enthalten. Das `text`-Attribut speichert den zu analysierenden Text in der Ausgangssprache (`language`), und `id` kann ein beliebiger Wert sein. 

Das Antwortobjekt ist eine Liste mit den analysierten Informationen für jedes Dokument. 

---

[!INCLUDE [text-analytics-character-limits](../character-limits.md)]

## <a name="code-examples"></a>Codebeispiele

Diese Codeausschnitte veranschaulichen, wie die folgenden Aufgaben mit der Textanalyse-Clientbibliothek für Python durchgeführt werden:

# <a name="version-31"></a>[Version 3.1](#tab/version-3-1)

* [Authentifizieren des Clients](#authenticate-the-client)
* [Standpunktanalyse](#sentiment-analysis)
* [Opinion Mining](#opinion-mining)
* [Sprachenerkennung](#language-detection)
* [Erkennung benannter Entitäten](#named-entity-recognition-ner) 
* [Erkennung von personenbezogenen Informationen](#personally-identifiable-information-pii-recognition) 
* [Entitätsverknüpfung](#entity-linking)
* [Schlüsselbegriffserkennung](#key-phrase-extraction)


# <a name="version-30"></a>[Version 3.0](#tab/version-3)

* [Authentifizieren des Clients](#authenticate-the-client)
* [Standpunktanalyse](#sentiment-analysis)
* [Sprachenerkennung](#language-detection)
* [Erkennung benannter Entitäten](#named-entity-recognition-ner) 
* [Entitätsverknüpfung](#entity-linking)
* [Schlüsselbegriffserkennung](#key-phrase-extraction)

---

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

# <a name="version-31"></a>[Version 3.1](#tab/version-3-1)

Erstellen Sie eine Funktion, um das Objekt `TextAnalyticsClient` mit dem oben erstellten Schlüssel (`key`) und Endpunkt (`endpoint`) zu instanziieren. Erstellen Sie anschließend einen neuen Client. 

```python
from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

def authenticate_client():
    ta_credential = AzureKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, 
            credential=ta_credential)
    return text_analytics_client

client = authenticate_client()
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Erstellen Sie eine Funktion, um das Objekt `TextAnalyticsClient` mit dem oben erstellten Schlüssel (`key`) und Endpunkt (`endpoint`) zu instanziieren. Erstellen Sie anschließend einen neuen Client. Beachten Sie, dass `api_version=TextAnalyticsApiVersion.V3_0` für die Verwendung von Version 3.0 definiert sein muss.

```python
# use this code if you're using SDK version is 5.0.0
from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

def authenticate_client():
    ta_credential = AzureKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, 
            credential=ta_credential) 
    return text_analytics_client

client = authenticate_client()
```

Wenn Sie Version 5.1.0 der Clientbibliothek mit `pip install azure-ai-textanalytics --pre` installiert haben, können Sie Version 3.0 der Textanalyse-API mit dem Parameter `api_version` des Clients angeben. Verwenden Sie die folgende `authenticate_client()`-Methode nur, wenn Ihr Client Version 5.1.0 oder höher aufweist.

```python
# Only use the following code sample if you're using v5.1.0 of the client library, 
# and are looking to specify v3.0 of the Text Analytics API for your client
from azure.ai.textanalytics import TextAnalyticsClient, TextAnalyticsApiVersion
from azure.core.credentials import AzureKeyCredential
def authenticate_client():
   ta_credential = AzureKeyCredential(key)
   text_analytics_client = TextAnalyticsClient(
     endpoint=endpoint,
     credential=ta_credential,
     api_version=TextAnalyticsApiVersion.V3_0
   )
   
client = authenticate_client()
```

--- 

## <a name="sentiment-analysis"></a>Stimmungsanalyse

# <a name="version-31"></a>[Version 3.1](#tab/version-3-1)

Erstellen Sie eine neue Funktion namens `sentiment_analysis_example()`, die den Client als Argument akzeptiert und anschließend die Funktion `analyze_sentiment()` aufruft. Das zurückgegebene Antwortobjekt enthält die Stimmungsbezeichnung und den Stimmungswert des gesamten Eingabedokuments sowie eine Standpunktanalyse für jeden Satz.


```python
def sentiment_analysis_example(client):

    documents = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(documents=documents)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
        response.confidence_scores.positive,
        response.confidence_scores.neutral,
        response.confidence_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("Sentence: {}".format(sentence.text))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
            sentence.confidence_scores.positive,
            sentence.confidence_scores.neutral,
            sentence.confidence_scores.negative,
        ))
          
sentiment_analysis_example(client)
```

### <a name="output"></a>Output

```console
Document Sentiment: positive
Overall scores: positive=1.00; neutral=0.00; negative=0.00 

Sentence: I had the best day of my life.
Sentence 1 sentiment: positive
Sentence score:
Positive=1.00
Neutral=0.00
Negative=0.00

Sentence: I wish you were there with me.
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.21
Neutral=0.77
Negative=0.02
```


# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Erstellen Sie eine neue Funktion namens `sentiment_analysis_example()`, die den Client als Argument akzeptiert und anschließend die Funktion `analyze_sentiment()` aufruft. Das zurückgegebene Antwortobjekt enthält die Stimmungsbezeichnung und den Stimmungswert des gesamten Eingabedokuments sowie eine Standpunktanalyse für jeden Satz.


```python
def sentiment_analysis_example(client):

    documents = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(documents = documents)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
        response.confidence_scores.positive,
        response.confidence_scores.neutral,
        response.confidence_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("Sentence: {}".format(sentence.text))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
            sentence.confidence_scores.positive,
            sentence.confidence_scores.neutral,
            sentence.confidence_scores.negative,
        ))
          
sentiment_analysis_example(client)
```

### <a name="output"></a>Output

```console
Document Sentiment: positive
Overall scores: positive=1.00; neutral=0.00; negative=0.00 

Sentence: I had the best day of my life.
Sentence 1 sentiment: positive
Sentence score:
Positive=1.00
Neutral=0.00
Negative=0.00

Sentence: I wish you were there with me.
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.21
Neutral=0.77
Negative=0.02
```

---

## <a name="opinion-mining"></a>Opinion Mining

# <a name="version-31"></a>[Version 3.1](#tab/version-3-1)


Zum Durchführen von Stimmungsanalyse mit Opinion Mining erstellen Sie eine neue Funktion mit dem Namen `sentiment_analysis_with_opinion_mining_example()`, die den Client als Argument annimmt und dann die Funktion `analyze_sentiment()` mit dem Optionsflag `show_opinion_mining=True` aufruft. Das zurückgegebene Antwortobjekt enthält nicht nur die Stimmungsbezeichnung und den Stimmungswert des gesamten Eingabedokuments mit einer Stimmungsanalyse für jeden Satz, sondern außerdem die Standpunktanalyse auf Ansichts- und Meinungsebene.


```python
def sentiment_analysis_with_opinion_mining_example(client):

    documents = [
        "The food and service were unacceptable, but the concierge were nice"
    ]

    result = client.analyze_sentiment(documents, show_opinion_mining=True)
    doc_result = [doc for doc in result if not doc.is_error]

    positive_reviews = [doc for doc in doc_result if doc.sentiment == "positive"]
    negative_reviews = [doc for doc in doc_result if doc.sentiment == "negative"]

    positive_mined_opinions = []
    mixed_mined_opinions = []
    negative_mined_opinions = []

    for document in doc_result:
        print("Document Sentiment: {}".format(document.sentiment))
        print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
            document.confidence_scores.positive,
            document.confidence_scores.neutral,
            document.confidence_scores.negative,
        ))
        for sentence in document.sentences:
            print("Sentence: {}".format(sentence.text))
            print("Sentence sentiment: {}".format(sentence.sentiment))
            print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
                sentence.confidence_scores.positive,
                sentence.confidence_scores.neutral,
                sentence.confidence_scores.negative,
            ))
            for mined_opinion in sentence.mined_opinions:
                target = mined_opinion.target
                print("......'{}' target '{}'".format(target.sentiment, target.text))
                print("......Target score:\n......Positive={0:.2f}\n......Negative={1:.2f}\n".format(
                    target.confidence_scores.positive,
                    target.confidence_scores.negative,
                ))
                for assessment in mined_opinion.assessments:
                    print("......'{}' assessment '{}'".format(assessment.sentiment, assessment.text))
                    print("......Assessment score:\n......Positive={0:.2f}\n......Negative={1:.2f}\n".format(
                        assessment.confidence_scores.positive,
                        assessment.confidence_scores.negative,
                    ))
            print("\n")
        print("\n")
          
sentiment_analysis_with_opinion_mining_example(client)
```

### <a name="output"></a>Output

```console
Document Sentiment: positive
Overall scores: positive=0.84; neutral=0.00; negative=0.16

Sentence: The food and service were unacceptable, but the concierge were nice
Sentence sentiment: positive
Sentence score:
Positive=0.84
Neutral=0.00
Negative=0.16

......'negative' target 'food'
......Target score:
......Positive=0.01
......Negative=0.99

......'negative' assessment 'unacceptable'
......Assessment score:
......Positive=0.01
......Negative=0.99

......'negative' target 'service'
......Target score:
......Positive=0.01
......Negative=0.99

......'negative' assessment 'unacceptable'
......Assessment score:
......Positive=0.01
......Negative=0.99

......'positive' target 'concierge'
......Target score:
......Positive=1.00
......Negative=0.00

......'positive' assessment 'nice'
......Assessment score:
......Positive=1.00
......Negative=0.00





Press any key to continue . . .

```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Dieses Feature ist in Version 3.0 nicht verfügbar.

---

## <a name="language-detection"></a>Spracherkennung

# <a name="version-31"></a>[Version 3.1](#tab/version-3-1)

Erstellen Sie eine neue Funktion namens `language_detection_example()`, die den Client als Argument akzeptiert und anschließend die Funktion `detect_language()` aufruft. Das zurückgegebene Antwortobjekt enthält die erkannte Sprache in `primary_language` (sofern erfolgreich). Andernfalls enthält es eine Fehlermeldung (`error`).

> [!Tip]
> In einigen Fällen ist es unter Umständen schwierig, die Sprachen basierend auf den Eingaben eindeutig zu bestimmen. Mithilfe des Parameters `country_hint` können Sie einen zweistelligen Ländercode angeben. Die API verwendet standardmäßig „US“ als Wert für „countryHint“. Wenn Sie dieses Verhalten entfernen möchten, können Sie diesen Parameter zurücksetzen, indem Sie seinen Wert auf eine leere Zeichenfolge (`country_hint : ""`) festlegen. 

```python
def language_detection_example(client):
    try:
        documents = ["Ce document est rédigé en Français."]
        response = client.detect_language(documents = documents, country_hint = 'us')[0]
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(client)
```


### <a name="output"></a>Output

```console
Language:  French
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Erstellen Sie eine neue Funktion namens `language_detection_example()`, die den Client als Argument akzeptiert und anschließend die Funktion `detect_language()` aufruft. Das zurückgegebene Antwortobjekt enthält die erkannte Sprache in `primary_language` (sofern erfolgreich). Andernfalls enthält es eine Fehlermeldung (`error`).

> [!Tip]
> In einigen Fällen ist es unter Umständen schwierig, die Sprachen basierend auf den Eingaben eindeutig zu bestimmen. Mithilfe des Parameters `country_hint` können Sie einen zweistelligen Ländercode angeben. Die API verwendet standardmäßig „US“ als Wert für „countryHint“. Wenn Sie dieses Verhalten entfernen möchten, können Sie diesen Parameter zurücksetzen, indem Sie seinen Wert auf eine leere Zeichenfolge (`country_hint : ""`) festlegen. 

```python
def language_detection_example(client):
    try:
        documents = ["Ce document est rédigé en Français."]
        response = client.detect_language(documents = documents, country_hint = 'us')[0]
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(client)
```


### <a name="output"></a>Output

```console
Language:  French
```


---

## <a name="named-entity-recognition-ner"></a>Erkennung benannter Entitäten (NER)

# <a name="version-31"></a>[Version 3.1](#tab/version-3-1)

> [!NOTE]
> In Version `3.1`: 
> * Die Entitätsverknüpfung ist eine separate Anforderung.

Erstellen Sie eine neue Funktion namens `entity_recognition_example`, die den Client als Argument akzeptiert und anschließend die Funktion `recognize_entities()` aufruft und die Ergebnisse durchläuft. Das zurückgegebene Antwortobjekt enthält die Liste erkannter Entitäten in `entity` (sofern erfolgreich). Andernfalls enthält es eine Fehlermeldung (`error`). Geben Sie für die erkannten Entitäten jeweils die Kategorie und Unterkategorie aus (sofern vorhanden).

```python
def entity_recognition_example(client):

    try:
        documents = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(documents = documents)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tConfidence Score: \t", round(entity.confidence_score, 2), "\tLength: \t", entity.length, "\tOffset: \t", entity.offset, "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>Output

```console
Named Entities:

        Text:    trip   Category:        Event  SubCategory:     None
        Confidence Score:        0.61   Length:          4      Offset:          18

        Text:    Seattle        Category:        Location       SubCategory:     GPE
        Confidence Score:        0.82   Length:          7      Offset:          26

        Text:    last week      Category:        DateTime       SubCategory:     DateRange
        Confidence Score:        0.8    Length:          9      Offset:          34
```

## <a name="personally-identifiable-information-pii-recognition"></a>Erkennung von personenbezogenen Informationen (PII)

Erstellen Sie eine neue Funktion namens `pii_recognition_example`, die den Client als Argument akzeptiert und anschließend die Funktion `recognize_pii_entities()` aufruft und die Ergebnisse durchläuft. Das zurückgegebene Antwortobjekt enthält die Liste erkannter Entitäten in `entity` (sofern erfolgreich). Andernfalls enthält es eine Fehlermeldung (`error`). Geben Sie für die erkannten Entitäten jeweils die Kategorie und Unterkategorie aus (sofern vorhanden).

```python
def pii_recognition_example(client):
    documents = [
        "The employee's SSN is 859-98-0987.",
        "The employee's phone number is 555-555-5555."
    ]
    response = client.recognize_pii_entities(documents, language="en")
    result = [doc for doc in response if not doc.is_error]
    for doc in result:
        print("Redacted Text: {}".format(doc.redacted_text))
        for entity in doc.entities:
            print("Entity: {}".format(entity.text))
            print("\tCategory: {}".format(entity.category))
            print("\tConfidence Score: {}".format(entity.confidence_score))
            print("\tOffset: {}".format(entity.offset))
            print("\tLength: {}".format(entity.length))
pii_recognition_example(client)
```

### <a name="output"></a>Output

```console
Redacted Text: The employee's SSN is ***********.
Entity: 859-98-0987
        Category: U.S. Social Security Number (SSN)
        Confidence Score: 0.65
        Offset: 22
        Length: 11
Redacted Text: The employee's phone number is ************.
Entity: 555-555-5555
        Category: Phone Number
        Confidence Score: 0.8
        Offset: 31
        Length: 12
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Erstellen Sie eine neue Funktion namens `entity_recognition_example`, die den Client als Argument akzeptiert und anschließend die Funktion `recognize_entities()` aufruft und die Ergebnisse durchläuft. Das zurückgegebene Antwortobjekt enthält die Liste erkannter Entitäten in `entity` (sofern erfolgreich). Andernfalls enthält es eine Fehlermeldung (`error`). Geben Sie für die erkannten Entitäten jeweils die Kategorie und Unterkategorie aus (sofern vorhanden).

```python
def entity_recognition_example(client):

    try:
        documents = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(documents = documents)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tConfidence Score: \t", round(entity.confidence_score, 2), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>Output

```console
Named Entities:

        Text:    trip   Category:        Event  SubCategory:     None
        Confidence Score:        0.61

        Text:    Seattle        Category:        Location       SubCategory:     GPE
        Confidence Score:        0.82

        Text:    last week      Category:        DateTime       SubCategory:     DateRange
        Confidence Score:        0.8
```


---

## <a name="entity-linking"></a>Entitätsverknüpfung

# <a name="version-31"></a>[Version 3.1](#tab/version-3-1)

Erstellen Sie eine neue Funktion namens `entity_linking_example()`, die den Client als Argument akzeptiert und anschließend die Funktion `recognize_linked_entities()` aufruft und die Ergebnisse durchläuft. Das zurückgegebene Antwortobjekt enthält die Liste erkannter Entitäten in `entities` (sofern erfolgreich). Andernfalls enthält es eine Fehlermeldung (`error`). Da verknüpfte Entitäten eindeutig identifiziert werden, werden Vorkommen der gleichen Entität unter einem Objekt vom Typ `entity` als Liste von Objekten des Typs `match` gruppiert.

```python
def entity_linking_example(client):

    try:
        documents = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(documents = documents)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.data_source_entity_id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tConfidence Score: {0:.2f}".format(match.confidence_score))
                print("\t\tOffset: {}".format(match.offset))
                print("\t\tLength: {}".format(match.length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>Output

```console
Linked Entities:

        Name:  Microsoft        Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft
        Data Source:  Wikipedia
        Matches:
                Text: Microsoft
                Confidence Score: 0.55
                Offset: 0
                Length: 9
                Text: Microsoft
                Confidence Score: 0.55
                Offset: 168
                Length: 9
        Name:  Bill Gates       Id:  Bill Gates         Url:  https://en.wikipedia.org/wiki/Bill_Gates
        Data Source:  Wikipedia
        Matches:
                Text: Bill Gates
                Confidence Score: 0.63
                Offset: 25
                Length: 10
                Text: Gates
                Confidence Score: 0.63
                Offset: 179
                Length: 5
        Name:  Paul Allen       Id:  Paul Allen         Url:  https://en.wikipedia.org/wiki/Paul_Allen
        Data Source:  Wikipedia
        Matches:
                Text: Paul Allen
                Confidence Score: 0.60
                Offset: 40
                Length: 10
        Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4
        Data Source:  Wikipedia
        Matches:
                Text: April 4
                Confidence Score: 0.32
                Offset: 54
                Length: 7
        Name:  BASIC    Id:  BASIC      Url:  https://en.wikipedia.org/wiki/BASIC
        Data Source:  Wikipedia
        Matches:
                Text: BASIC
                Confidence Score: 0.33
                Offset: 98
                Length: 5
        Name:  Altair 8800      Id:  Altair 8800        Url:  https://en.wikipedia.org/wiki/Altair_8800
        Data Source:  Wikipedia
        Matches:
                Text: Altair 8800
                Confidence Score: 0.88
                Offset: 125
                Length: 11
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Erstellen Sie eine neue Funktion namens `entity_linking_example()`, die den Client als Argument akzeptiert und anschließend die Funktion `recognize_linked_entities()` aufruft und die Ergebnisse durchläuft. Das zurückgegebene Antwortobjekt enthält die Liste erkannter Entitäten in `entities` (sofern erfolgreich). Andernfalls enthält es eine Fehlermeldung (`error`). Da verknüpfte Entitäten eindeutig identifiziert werden, werden Vorkommen der gleichen Entität unter einem Objekt vom Typ `entity` als Liste von Objekten des Typs `match` gruppiert.

```python
def entity_linking_example(client):

    try:
        documents = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(documents = documents)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.data_source_entity_id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tConfidence Score: {0:.2f}".format(match.confidence_score))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>Output

```console
Linked Entities:

        Name:  Altair 8800      Id:  Altair 8800        Url:  https://en.wikipedia.org/wiki/Altair_8800
        Data Source:  Wikipedia
        Matches:
                Text: Altair 8800
                Confidence Score: 0.88
        Name:  Bill Gates       Id:  Bill Gates         Url:  https://en.wikipedia.org/wiki/Bill_Gates
        Data Source:  Wikipedia
        Matches:
                Text: Bill Gates
                Confidence Score: 0.63
                Text: Gates
                Confidence Score: 0.63
        Name:  Paul Allen       Id:  Paul Allen         Url:  https://en.wikipedia.org/wiki/Paul_Allen
        Data Source:  Wikipedia
        Matches:
                Text: Paul Allen
                Confidence Score: 0.60
        Name:  Microsoft        Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft
        Data Source:  Wikipedia
        Matches:
                Text: Microsoft
                Confidence Score: 0.55
                Text: Microsoft
                Confidence Score: 0.55
        Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4
        Data Source:  Wikipedia
        Matches:
                Text: April 4
                Confidence Score: 0.32
        Name:  BASIC    Id:  BASIC      Url:  https://en.wikipedia.org/wiki/BASIC
        Data Source:  Wikipedia
        Matches:
                Text: BASIC
                Confidence Score: 0.33
```

---

## <a name="key-phrase-extraction"></a>Schlüsselwortextraktion

# <a name="version-31"></a>[Version 3.1](#tab/version-3-1)

Erstellen Sie eine neue Funktion namens `key_phrase_extraction_example()`, die den Client als Argument akzeptiert und anschließend die Funktion `extract_key_phrases()` aufruft. Das Ergebnis enthält bei erfolgreicher Ausführung die Liste der erkannten Schlüsselbegriffe in `key_phrases` und andernfalls `error`. Geben Sie alle erkannten Schlüsselbegriffe aus.

```python
def key_phrase_extraction_example(client):

    try:
        documents = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(documents = documents)[0]

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(client)
```


### <a name="output"></a>Output

```console
    Key Phrases:
         cat
         veterinarian
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Erstellen Sie eine neue Funktion namens `key_phrase_extraction_example()`, die den Client als Argument akzeptiert und anschließend die Funktion `extract_key_phrases()` aufruft. Das Ergebnis enthält bei erfolgreicher Ausführung die Liste der erkannten Schlüsselbegriffe in `key_phrases` und andernfalls `error`. Geben Sie alle erkannten Schlüsselbegriffe aus.

```python
def key_phrase_extraction_example(client):

    try:
        documents = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(documents = documents)[0]

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(client)
```


### <a name="output"></a>Output

```console
    Key Phrases:
         cat
         veterinarian
```


---

## <a name="extract-health-entities"></a>Extrahieren von Integritätsentitäten

Sie können Textanalyse verwenden, um eine asynchrone Anforderung zum Extrahieren von Integritätsentitäten aus Text auszuführen. Nachfolgend sehen Sie ein einfaches Beispiel für diesen Vorgang. Ein komplexeres Beispiel können Sie auf [GitHub](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/textanalytics/azure-ai-textanalytics/samples/sample_analyze_healthcare_entities.py) finden.

[!INCLUDE [health operation pricing](../health-operation-pricing-caution.md)]

# <a name="version-31"></a>[Version 3.1](#tab/version-3-1)

``` python
def health_example(client):
    documents = [
        """
        Patient needs to take 50 mg of ibuprofen.
        """
    ]

    poller = client.begin_analyze_healthcare_entities(documents)
    result = poller.result()

    docs = [doc for doc in result if not doc.is_error]

    for idx, doc in enumerate(docs):
        for entity in doc.entities:
            print("Entity: {}".format(entity.text))
            print("...Normalized Text: {}".format(entity.normalized_text))
            print("...Category: {}".format(entity.category))
            print("...Subcategory: {}".format(entity.subcategory))
            print("...Offset: {}".format(entity.offset))
            print("...Confidence score: {}".format(entity.confidence_score))
        for relation in doc.entity_relations:
            print("Relation of type: {} has the following roles".format(relation.relation_type))
            for role in relation.roles:
                print("...Role '{}' with entity '{}'".format(role.name, role.entity.text))
        print("------------------------------------------")
health_example(client)
```

### <a name="output"></a>Ausgabe

```console
Entity: 50 mg
...Normalized Text: None
...Category: Dosage
...Subcategory: None
...Offset: 31
...Confidence score: 1.0
Entity: ibuprofen
...Normalized Text: ibuprofen
...Category: MedicationName
...Subcategory: None
...Offset: 40
...Confidence score: 1.0
Relation of type: DosageOfMedication has the following roles
...Role 'Dosage' with entity '50 mg'
...Role 'Medication' with entity 'ibuprofen'
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Dieses Feature ist in Version 3.0 nicht verfügbar.

---


## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>Asynchrones Verwenden der API mit dem Analyze-Vorgang

# <a name="version-31"></a>[Version 3.1](#tab/version-3-1)

Mithilfe des Analysevorgangs können Sie asynchrone Batchanforderungen für Folgendes ausführen: NER, Schlüsselbegriffserkennung, Stimmungsanalyse und Erkennung personenbezogener Informationen. Nachfolgend sehen Sie ein einfaches Beispiel für einen Vorgang. Ein komplexeres Beispiel können Sie auf [GitHub](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/textanalytics/azure-ai-textanalytics/samples/sample_analyze_actions.py) finden.

[!INCLUDE [Analyze operation pricing](../analyze-operation-pricing-caution.md)]

Erstellen Sie eine neue Funktion namens `analyze_batch_example()`, die den Client als Argument akzeptiert und anschließend die Funktion `begin_analyze_actions()` aufruft. Daraus resultiert ein zeitintensiver Vorgang, aus dem Ergebnisse abgerufen werden.

```python
from azure.ai.textanalytics import (
    RecognizeEntitiesAction,
    ExtractKeyPhrasesAction
)

def analyze_batch_example(client):
        documents = [
            "Microsoft was founded by Bill Gates and Paul Allen."
        ]

        poller = client.begin_analyze_actions(
            documents,
            display_name="Sample Text Analysis",
            actions=[RecognizeEntitiesAction(), ExtractKeyPhrasesAction()]
        )

        result = poller.result()
        action_results = [action_result for action_result in list(result)]
        first_action_result = action_results[0][0]
        print("Results of Entities Recognition action:")

        for entity in first_action_result.entities:
            print("Entity: {}".format(entity.text))
            print("...Category: {}".format(entity.category))
            print("...Confidence Score: {}".format(entity.confidence_score))
            print("...Offset: {}".format(entity.offset))
            print("...Length: {}".format(entity.length))
        print("------------------------------------------")

        second_action_result = action_results[0][1]
        print("Results of Key Phrase Extraction action:")
        
        for key_phrase in second_action_result.key_phrases:
            print("Key Phrase: {}\n".format(key_phrase))
        print("------------------------------------------")

analyze_batch_example(client)
```

### <a name="output"></a>Ausgabe

```console
Results of Entities Recognition action:
Entity: Microsoft
...Category: Organization
...Confidence Score: 1.0
...Offset: 0
...Length: 9
Entity: Bill Gates
...Category: Person
...Confidence Score: 1.0
...Offset: 25
...Length: 10
Entity: Paul Allen
...Category: Person
...Confidence Score: 1.0
...Offset: 40
...Length: 10
------------------------------------------
Results of Key Phrase Extraction action:
Key Phrase: Bill Gates

Key Phrase: Paul Allen

Key Phrase: Microsoft

------------------------------------------
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Dieses Feature ist in Version 3.0 nicht verfügbar.

---
