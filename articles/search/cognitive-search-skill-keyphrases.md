---
title: Der kognitive Skill „Schlüsselbegriffserkennung“
titleSuffix: Azure Cognitive Search
description: Bewertet unstrukturierten Text und gibt für jeden Datensatz eine Liste mit Schlüsselbegriffen in einer KI-Anreicherungspipeline in der kognitiven Azure-Suche zurück.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 7d364cd73a866bfa3e40542329ddfc5a6dffcf13
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122349960"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>Der kognitive Skill „Schlüsselbegriffserkennung“

Der Skill **Schlüsselbegriffserkennung** bewertet unstrukturierten Text und gibt für jeden Datensatz eine Liste mit Schlüsselbegriffen zurück. Bei dieser Qualifikation werden die Machine Learning-Modelle verwendet, die in Cognitive Services über die [Textanalyse](../cognitive-services/text-analytics/overview.md) bereitgestellt werden.

Diese Funktion ist nützlich, wenn Sie die wichtigsten Gesprächspunkte in dem Datensatz schnell identifizieren müssen. Zum Beispiel, wenn der eingegebene Text „Das Essen war köstlich, und es gab hervorragendes Personal“ lautet, gibt der Dienst „Essen“ und „hervorragendes Personal“ zurück.

> [!NOTE]
> Dieser Skill ist an Cognitive Services gebunden und erfordert [eine abrechenbare Ressource](cognitive-search-attach-cognitive-services.md) für Transaktionen, die 20 Dokumente pro Indexer und Tag überschreiten. Die Ausführung integrierter Qualifikationen wird nach dem bestehenden [nutzungsbasierten Preis für Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) berechnet.
>

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>Datengrenzwerte
Die maximale Größe eines Datensatzes beträgt 50.000 Zeichen (gemessen durch [`String.Length`](/dotnet/api/system.string.length)). Wenn Sie Ihre Daten teilen müssen, bevor Sie sie an die Schlüsselbegriffserkennung senden, denken Sie daran, den [Skill „Text teilen“](cognitive-search-skill-textsplit.md) zu verwenden.

## <a name="skill-parameters"></a>Skillparameter

Bei den Parametern wird zwischen Groß- und Kleinschreibung unterschieden.

| Eingaben | BESCHREIBUNG |
|---------------------|-------------|
| `defaultLanguageCode` | (Optional) Der Sprachcode, der auf Dokumente angewendet wird, in denen die Sprache nicht explizit angegeben ist.  Wenn kein Standardsprachcode festgelegt ist, wird Englisch (en) als Standardsprachcode verwendet. <br/> Siehe die [vollständige Liste der unterstützten Sprachen](../cognitive-services/text-analytics/language-support.md). |
| `maxKeyPhraseCount`   | (Optional) Die maximale Anzahl der Schlüsselbegriffe, die erzeugt werden. |
| `modelVersion`   | (Optional) Die Version des Modells, die beim Aufruf des Textanalysediensts verwendet werden soll. Wenn nichts angegeben ist, wird standardmäßig die neueste verfügbare Version verwendet. Es empfiehlt sich, diesen Wert nur anzugeben, wenn es unbedingt notwendig ist. Weitere Einzelheiten finden Sie unter [Versionsverwaltung der Modelle in der Textanalyse-API](../cognitive-services/text-analytics/concepts/model-versioning.md). |

## <a name="skill-inputs"></a>Skilleingaben

| Eingabe  | BESCHREIBUNG |
|--------------------|-------------|
| `text` | Der zu analysierende Text|
| `languageCode`    |  Eine Zeichenfolge, die die Sprache der Datensätze angibt. Wenn dieser Parameter nicht angegeben ist, wird der Standardsprachcode zur Analyse der Datensätze verwendet. <br/>Siehe die [vollständige Liste der unterstützten Sprachen](../cognitive-services/text-analytics/language-support.md).|

## <a name="skill-outputs"></a>Skillausgaben

| Output     | BESCHREIBUNG |
|--------------------|-------------|
| `keyPhrases` | Eine Liste mit aus dem Eingabetext extrahierten Schlüsselausdrücken. Die Schlüsselausdrücke werden in der Reihenfolge ihrer Wichtigkeit zurückgegeben. |


##  <a name="sample-definition"></a>Beispieldefinition

Gehen Sie von einem SQL-Datensatz mit den folgenden Feldern aus:

```json
{
    "content": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
    "language": "en"
}
```

Ihre Skilldefinition sieht dann möglicherweise folgendermaßen aus:

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      },
      {
        "name": "languageCode",
        "source": "/document/language" 
      }
    ],
    "outputs": [
      {
        "name": "keyPhrases",
        "targetName": "myKeyPhrases"
      }
    ]
  }
```

##  <a name="sample-output"></a>Beispielausgabe

Im obigen Beispiel wird die Ausgabe Ihres Skills in einen neuen Knoten in der angereicherten Informationsstruktur namens „document/myKeyPhrases“ geschrieben, da dies der angegebene Wert für `targetName` ist. Wenn Sie keinen Wert für `targetName` angeben, wird „document/keyPhrases“ verwendet.

#### <a name="documentmykeyphrases"></a>document/myKeyPhrases 
```json
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
```

Sie können „document/myKeyPhrase“ als Eingabe für andere Skills oder als Quelle für eine [Ausgabefeldzuordnung](cognitive-search-output-field-mapping.md) verwenden.

## <a name="warnings"></a>Warnungen
Wenn Sie einen nicht unterstützte Sprachcode bereitstellen, wird eine Warnung generiert, und es werden keine Schlüsselbegriffe extrahiert.
Wenn Ihr Text leer ist, wird eine Warnung angezeigt.
Wenn Ihre Text mehr als 50.000 Zeichen umfasst, werden nur die ersten 50.000 Zeichen analysiert und eine Warnung ausgegeben.

## <a name="see-also"></a>Weitere Informationen

+ [Integrierte Qualifikationen](cognitive-search-predefined-skills.md)
+ [Definieren eines Skillsets](cognitive-search-defining-skillset.md)
+ [Zuordnen von mit KI angereicherten Feldern zu einem durchsuchbaren Index](cognitive-search-output-field-mapping.md)