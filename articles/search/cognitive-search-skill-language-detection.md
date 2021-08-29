---
title: Der kognitive Skill „Spracherkennung“
titleSuffix: Azure Cognitive Search
description: Bewertet unstrukturierten Text und gibt für jeden Datensatz einen Sprachbezeichner mit einer Punktzahl zurück, die die Stärke der Analyse in einer KI-Anreicherungspipeline in der kognitiven Azure-Suche angibt.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 01b6449baa7c5d13b041f886074425d78e037579
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122340388"
---
#   <a name="language-detection-cognitive-skill"></a>Der kognitive Skill „Spracherkennung“

Der Skill **Spracherkennung** erkennt die Sprache von Eingabetexten und meldet einen einzigen Sprachcode für jedes Dokument, das auf Anforderung gesendet wird. Der Sprachcode ist mit einem Wert kombiniert, der die Stärke der Analyse angibt. Bei dieser Qualifikation werden die Machine Learning-Modelle verwendet, die in Cognitive Services über die [Textanalyse](../cognitive-services/text-analytics/overview.md) bereitgestellt werden.

Diese Funktion ist besonders nützlich, wenn Sie die Sprache des Texts als Eingabe für andere Skills (z.B. den [Skill „Standpunktanalyse“](cognitive-search-skill-sentiment-v3.md) oder den [Skill „Text teilen“](cognitive-search-skill-textsplit.md)) benötigen.

„Spracherkennung“ nutzt die Bibliotheken von Bing zur Verarbeitung natürlicher Sprache, wodurch die Anzahl von [unterstützten Sprachen und Regionen](../cognitive-services/text-analytics/language-support.md) überschritten wird, die für „Textanalyse“ aufgeführt sind. Die genaue Sprachenliste wird nicht veröffentlicht. Sie enthält aber alle verbreiteten Sprachen, plus Varianten, Dialekte sowie einige Regional- und Kultursprachen. Bei Inhalten in einer seltener verwendeten Sprache können Sie [die Sprachenerkennungs-API ausprobieren](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages), um zu sehen, ob sie einen Code zurückgibt. Die Antwort bei Sprachen, die nicht erkannt werden können, lautet `(Unknown)`.

> [!NOTE]
> Dieser Skill ist an Cognitive Services gebunden und erfordert [eine abrechenbare Ressource](cognitive-search-attach-cognitive-services.md) für Transaktionen, die 20 Dokumente pro Indexer und Tag überschreiten. Die Ausführung integrierter Qualifikationen wird nach dem bestehenden [nutzungsbasierten Preis für Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) berechnet.
>

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>Datengrenzwerte
Die maximale Größe eines Datensatzes beträgt 50.000 Zeichen (gemessen durch [`String.Length`](/dotnet/api/system.string.length)). Wenn Sie Ihre Daten teilen müssen, bevor Sie sie an den Skill für Sprachenerkennung senden, können Sie den [Skill „Text teilen“](cognitive-search-skill-textsplit.md) verwenden.

## <a name="skill-parameters"></a>Skillparameter

Bei den Parametern wird zwischen Groß- und Kleinschreibung unterschieden.

| Eingaben | BESCHREIBUNG |
|---------------------|-------------|
| `defaultCountryHint` | (Optional) Es kann ein ISO 3166-1-Alpha-2-Ländercode aus zwei Buchstaben angegeben werden, der als Hinweis für das Spracherkennungsmodell verwendet wird, wenn es die Sprache nicht eindeutig zuordnen kann. Weitere Details finden Sie in der [Textanalyse-Dokumentation](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-language-detection.md#ambiguous-content) zu diesem Thema. Insbesondere wird der Parameter `defaultCountryHint` mit Dokumenten verwendet, die die `countryHint`-Eingabe nicht explizit festlegen.  |
| `modelVersion`   | (Optional) Die Version des Modells, die beim Aufruf des Textanalysediensts verwendet werden soll. Wenn nichts angegeben ist, wird standardmäßig die neueste verfügbare Version verwendet. Es empfiehlt sich, diesen Wert nur anzugeben, wenn es unbedingt notwendig ist. Weitere Einzelheiten finden Sie unter [Versionsverwaltung der Modelle in der Textanalyse-API](../cognitive-services/text-analytics/concepts/model-versioning.md). |

## <a name="skill-inputs"></a>Skilleingaben

Bei den Parametern wird zwischen Groß- und Kleinschreibung unterschieden.

| Eingaben     | BESCHREIBUNG |
|--------------------|-------------|
| `text` | Der zu analysierende Text|
| `countryHint` | Ein ISO 3166-1-Alpha-2-Ländercode aus zwei Buchstaben, der als Hinweis für das Spracherkennungsmodell verwendet wird, wenn es die Sprache nicht eindeutig zuordnen kann. Weitere Details finden Sie in der [Textanalyse-Dokumentation](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-language-detection.md#ambiguous-content) zu diesem Thema. |

## <a name="skill-outputs"></a>Skillausgaben

| Ausgabename    | BESCHREIBUNG |
|--------------------|-------------|
| `languageCode` | Der für die Sprache identifizierte ISO-6391-Sprachcode. Beispiel: „en“. |
| `languageName` | Der Name der Sprache. Beispiel: „Englisch“. |
| `score` | Ein Wert zwischen 0 und 1 ein. Die Wahrscheinlichkeit, dass die Sprache korrekt identifiziert ist. Der Wert kann kleiner als 1 sein, wenn im Satz verschiedene Sprachen kombiniert sind.  |

##  <a name="sample-definition"></a>Beispieldefinition

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      },
      {
        "name": "countryHint",
        "source": "/document/countryHint"
      }
    ],
    "outputs": [
      {
        "name": "languageCode",
        "targetName": "myLanguageCode"
      },
      {
        "name": "languageName",
        "targetName": "myLanguageName"
      },
      {
        "name": "score",
        "targetName": "myLanguageScore"
      }

    ]
  }
```

##  <a name="sample-input"></a>Beispieleingabe

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. "
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Estamos muy felices de estar con ustedes."
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "impossible",
             "countryHint": "fr"
           }
      }
    ]
```


##  <a name="sample-output"></a>Beispielausgabe

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
            {
              "languageCode": "en",
              "languageName": "English",
              "score": 1,
            }
      },
      {
        "recordId": "2",
        "data":
            {
              "languageCode": "es",
              "languageName": "Spanish",
              "score": 1,
            }
      },
      {
        "recordId": "3",
        "data":
            {
              "languageCode": "fr",
              "languageName": "French",
              "score": 1,
            }
      }
    ]
}
```

## <a name="see-also"></a>Weitere Informationen

+ [Integrierte Qualifikationen](cognitive-search-predefined-skills.md)
+ [Definieren eines Skillsets](cognitive-search-defining-skillset.md)