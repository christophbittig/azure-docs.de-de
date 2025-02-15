---
title: Der Skill „Shaper“
titleSuffix: Azure Cognitive Search
description: Extrahieren Sie Metadaten und strukturierte Informationen aus unstrukturierten Daten, und bringen Sie diese in die Form eines komplexen Typs in einer KI-Anreicherungspipeline in der kognitiven Azure-Suche.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 9395f0446680135bde99193609bde82385f64b0b
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123038780"
---
# <a name="shaper-cognitive-skill"></a>Der Skill „Shaper“

Die Qualifikation **Shaper** konsolidiert mehrere Eingaben in einem [komplexen Typ](search-howto-complex-data-types.md), auf den später in der Anreicherungspipeline verwiesen werden kann. Im Wesentlichen ermöglicht es Ihnen der Skill **Shaper**, eine Struktur zu erstellen, den Namen der Elemente dieser Struktur zu definieren und jedem Element Werte zuzuweisen. Beispiele für konsolidierte Felder, die in Suchszenarien nützlich sind, sind die Kombination von Vor- und Nachnamen in einer einzigen Struktur, Stadt und Land in einer einzigen Struktur oder Name und Geburtsdatum in einer einzigen Struktur, um eine eindeutige Identität zu schaffen.

Darüber hinaus fügt die Qualifikation **Shaper**, die in [Szenario 3](#nested-complex-types) gezeigt wird, der Eingabe eine neue optionale *sourceContext*-Eigenschaft hinzu. Die Eigenschaften *source* und *sourceContext* schließen sich gegenseitig aus. Wenn die Eingabe im Kontext der Qualifikation liegt, verwenden Sie einfach *source*. Wenn die Eingabe einen *anderen* Kontext als die Qualifikation aufweist, verwenden Sie *sourceContext*. Für *sourceContext* müssen Sie eine geschachtelte Eingabe mit dem Element definieren, das als Quelle verwendet werden soll. 

Der Ausgabename lautet immer „output“. Intern kann die Pipeline einen anderen Namen zuordnen (z. B. „analyzedText“ wie in den folgenden Beispielen), die Qualifikation **Shaper** selbst gibt in der Antwort jedoch „output“ zurück. Dies kann wichtig sein, wenn Sie angereicherte Dokumente debuggen und die Namensdiskrepanz bemerken, oder wenn Sie einen benutzerdefinierten Skill erstellen und die Antwort selbst strukturieren.

> [!NOTE]
> Dieser Skill ist nicht an Cognitive Services gebunden. Er ist nicht gebührenpflichtig und weist keine Cognitive Services-Schlüsselanforderungen auf.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="scenario-1-complex-types"></a>Szenario 1: komplexe Typen

Stellen Sie sich ein Szenario vor, in dem Sie eine Struktur namens *analyzedText* erstellen möchten, die zwei Elemente hat: *text* und *sentiment*. In einem Index wird ein mehrteiliges, durchsuchbares Feld als *komplexer Typ* bezeichnet und oft erstellt, wenn die Quelldaten über eine entsprechende komplexe Struktur verfügen, die dem Typ entspricht.

Einen anderen Ansatz für das Erstellen komplexer Typen stellt die Qualifikation **Shaper** dar. Durch das Einschließen dieser Qualifikation in eine Qualifikationsgruppe können die speicherinternen Vorgänge während der Verarbeitung von Qualifikationsgruppen Datenformen mit geschachtelten Strukturen ausgeben, die dann einem komplexen Typ in Ihrem Index zugeordnet werden können. 

Die folgende Beispieldefinition für eine Qualifikation stellt die Elementnamen als Eingabe bereit. 

```json
{
  "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
  "context": "/document/content/phrases/*",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content/phrases/*"
    },
    {
      "name": "sentiment",
      "source": "/document/content/phrases/*/sentiment"
    }
  ],
  "outputs": [
    {
      "name": "output",
      "targetName": "analyzedText"
    }
  ]
}
```

### <a name="sample-index"></a>Beispielindex

Eine Qualifikationsgruppe wird von einem Indexer aufgerufen, der wiederum einen Index erfordert. Die Darstellung eines komplexen Felds in Ihrem Index kann wie im folgenden Beispiel aussehen. 

```json
"name":"my-index",
"fields":[
   { "name":"myId", "type":"Edm.String", "key":true, "filterable":true  },
   { "name":"analyzedText", "type":"Edm.ComplexType",
      "fields":[
         {
            "name":"text",
            "type":"Edm.String",
            "facetable":false,
            "filterable":false,
            "searchable":true,
            "sortable":false  },
         {
            "name":"sentiment",
            "type":"Edm.Double",
            "facetable":true,
            "filterable":true,
            "searchable":true,
            "sortable":true }
      }
```

### <a name="skill-input"></a>Qualifikationseingaben

So könnte ein eingehendes JSON-Dokument aussehen, das hilfreiche Eingabewerte für diese Qualifikation **Shaper** enthält:

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "this movie is awesome",
                "sentiment": 0.9
            }
        }
    ]
}
```


### <a name="skill-output"></a>Qualifikationsausgaben

Der Skill **Shaper** generiert ein neues Element namens *analyzedText* mit den kombinierten Elementen von *text* und *sentiment*. Diese Ausgabe entspricht dem Indexschema. Sie wird importiert und in einem Index der kognitiven Azure-Suche indiziert.

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "analyzedText": 
              {
                "text": "this movie is awesome" ,
                "sentiment": 0.9
              }
           }
      }
    ]
}
```

## <a name="scenario-2-input-consolidation"></a>Szenario 2: Eingabekonsolidierung

In einem anderen Beispiel stellen Sie sich vor, dass Sie in verschiedenen Phasen der Pipelineverarbeitung den Titel eines Buchs und die Kapitelüberschriften auf verschiedenen Seiten des Buchs extrahiert haben. Sie können nun eine einzige Struktur aus diesen verschiedenen Eingaben erstellen.

Die Definition der Qualifikation **Shaper** für dieses Szenario könnte wie im folgenden Beispiel aussehen:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "source": "/document/content/pages/*/chapterTitles/*/title"
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>Qualifikationsausgaben
In diesem Fall vereinfacht **Shaper** alle Kapiteltitel, um ein einzelnes Array zu erstellen. 

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                        "Start young",
                        "Laugh often",
                        "Eat, sleep and exercise"
                    ]
                }
            }
        }
    ]
}
```

<a name="nested-complex-types"></a>

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>Szenario 3: Eingabekonsolidierung aus geschachtelten Kontexten

Angenommen, Sie verfügen über Titel, Kapitel und Inhalt eines Buchs, haben die Erkennung von Entitäten und Schlüsselausdrücken für den Inhalt ausgeführt und möchten jetzt die Ergebnisse der einzelnen Qualifikationen in einer einzelnen Form mit den Kapitelnamen, Entitäten und Schlüsselbegriffen aggregieren.

Die Definition der Qualifikation **Shaper** für dieses Szenario könnte wie im folgenden Beispiel aussehen:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "sourceContext": "/document/content/pages/*/chapterTitles/*",
            "inputs": [
              {
                  "name": "title",
                  "source": "/document/content/pages/*/chapterTitles/*/title"
              },
              {
                  "name": "number",
                  "source": "/document/content/pages/*/chapterTitles/*/number"
              }
            ]
        }

    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>Qualifikationsausgaben
In diesem Fall erstellt **Shaper** einen komplexen Typ. Diese Struktur ist im Arbeitsspeicher enthalten. Wenn Sie sie in einem [Wissensspeicher](knowledge-store-concept-intro.md) speichern möchten, sollten Sie eine Projektion in Ihrer Qualifikationsgruppe erstellen, die Speichereigenschaften definiert.

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                      { "title": "Start young", "number": 1},
                      { "title": "Laugh often", "number": 2},
                      { "title": "Eat, sleep and exercise", "number: 3}
                    ]
                }
            }
        }
    ]
}
```

## <a name="see-also"></a>Weitere Informationen

+ [Integrierte Qualifikationen](cognitive-search-predefined-skills.md)
+ [Definieren eines Skillsets](cognitive-search-defining-skillset.md)
+ [Verwenden komplexer Typen](search-howto-complex-data-types.md)
+ [Wissensspeicher](knowledge-store-concept-intro.md)
+ [Erstellen von Wissensspeichern in REST](knowledge-store-create-rest.md)