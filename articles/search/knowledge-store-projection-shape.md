---
title: Strukturieren von Daten für den Wissensspeicher
titleSuffix: Azure Cognitive Search
description: Definieren Sie die Datenstrukturen in einem Wissensspeicher, indem Sie diese erstellen und an eine Projektion übergeben.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: f0558d7e3cffd744ec4effa1d4f81f359e909e97
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131503169"
---
# <a name="shaping-data-for-projection-into-a-knowledge-store"></a>Strukturieren von Daten für die Projektion in einen Wissensspeicher

In Azure Cognitive Search beschreibt das „Strukturieren von Daten“ einen Schritt im [Workflow für den Wissensspeicher](knowledge-store-concept-intro.md), bei dem eine Datendarstellung des Inhalts erstellt wird, den Sie in Tabellen, Objekte und Dateien in Azure Storage projizieren möchten.

Beim Ausführen von Skills werden die Ausgaben in eine Anreicherungsstruktur in einer Knotenhierarchie geschrieben. Zwar möchten Sie die Anreicherungsstruktur möglicherweise in ihrer Gesamtheit anzeigen und nutzen, doch ist es wahrscheinlicher, dass Sie eine differenziertere Struktur wünschen und Teilmengen von Knoten für verschiedene Szenarios erstellen, z. B. indem Sie die Knoten im Zusammenhang mit übersetztem Text oder extrahierten Entitäten in bestimmten Tabellen platzieren.

Die Anreicherungsstruktur selbst enthält keine Logik, die Aufschluss darüber gibt, wie ihr Inhalt in einem Wissensspeicher dargestellt wird. Datenstrukturen füllen diese Lücke, indem sie das Schema für die einzelnen Tabellen-, Objekt- und Dateiprojektionen bereitstellen. Sie können sich eine Datenstruktur als eine benutzerdefinierte Definition oder Ansicht der angereicherten Daten vorstellen. Sie können beliebig viele Strukturen erstellen und diese dann [Projektionen](knowledge-store-projection-overview.md) in einer Wissensspeicherdefinition zuweisen. 

## <a name="approaches-for-creating-shapes"></a>Ansätze zum Erstellen von Strukturen

Es gibt zwei Möglichkeiten, angereicherte Inhalte so zu strukturieren, dass sie in einen Wissensspeicher projiziert werden können:

+ Verwenden Sie den [Shaper-Skill](cognitive-search-skill-shaper.md), um Knoten in einer Anreicherungsstruktur zu erstellen, die ausdrücklich für die Projektion verwendet werden. Die meisten Skills erstellen neue Inhalte. Im Gegensatz dazu verwendet ein Shaper-Skill vorhandene Knoten, zumeist um mehrere Knoten in einem einzigen komplexen Objekt zu konsolidieren. Dies ist nützlich für Tabellen, bei denen die Ausgabe mehrerer Knoten physisch als Spalten in der Tabelle ausgedrückt werden soll. 

+ Verwenden Sie eine Inlinestruktur innerhalb der Projektionsdefinition selbst.

Durch die Verwendung des Shaper-Skills wird die Struktur externalisiert, sodass sie von mehreren Projektionen oder sogar anderen Skills verwendet werden kann. Außerdem wird sichergestellt, dass alle Mutationen der Anreicherungsstruktur im Skill enthalten sind und dass die Ausgabe ein Objekt ist, das wiederverwendet werden kann. Mit der Inlinestrukturierung können Sie im Gegensatz dazu die benötigte Form erstellen, aber es handelt sich um ein anonymes Objekt, das nur für die Projektion verfügbar ist, für die es definiert wurde.

Die Ansätze können gleichzeitig oder getrennt einzeln verwendet werden. In diesem Artikel wird beides veranschaulicht: ein Shaper-Skill für die Tabellenprojektionen und die Inlinestrukturierung mit Schlüsselbegriffen für die Tabellenprojektion.

## <a name="use-a-shaper-skill"></a>Verwenden eines Shaper-Skills

Shaper-Skills werden in der Regel am Ende eines Skillsets platziert, um eine Ansicht der Daten zu erstellen, die Sie an eine Projektion übergeben möchten. In diesem Beispiel wird eine Struktur namens „tableprojection“ mit den Knoten „reviews_text“, „reviews_title“ und „AzureSearch_DocumentKey“ sowie Stimmungsbewertungen und Schlüsselbegriffen aus ausgelagerten Überprüfungen erstellt. 

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "#5",
    "description": null,
    "context": "/document",
    "inputs": [        
        {
            "name": "reviews_text",
            "source": "/document/reviews_text",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "reviews_title",
            "source": "/document/reviews_title",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "AzureSearch_DocumentKey",
            "source": "/document/AzureSearch_DocumentKey",
            "sourceContext": null,
            "inputs": []
        },  
        {
            "name": "pages",
            "source": null,
            "sourceContext": "/document/reviews_text/pages/*",
            "inputs": [
                {
                    "name": "Sentiment",
                    "source": "/document/reviews_text/pages/*/Sentiment",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "LanguageCode",
                    "source": "/document/Language",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "Page",
                    "source": "/document/reviews_text/pages/*",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyphrase",
                    "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                    "inputs": [
                        {
                            "source": "/document/reviews_text/pages/*/Keyphrases/*",
                            "name": "Keyphrases"
                        }
                    ]
                }
            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "tableprojection"
        }
    ]
}
```

### <a name="sourcecontext-property"></a>SourceContext-Eigenschaft

Innerhalb eines Shaper-Skills kann eine Eingabe ein `sourceContext`-Element aufweisen. Dieselbe Eigenschaft kann auch in Inlinestrukturen in Projektionen verwendet werden. 

Mit `sourceContext` werden geschachtelte Objekte mit mehreren Ebenen in einer Anreicherungspipeline erstellt. Wenn die Eingabe einen *anderen* Kontext als die Qualifikation aufweist, verwenden Sie *sourceContext*. Für *sourceContext* müssen Sie eine geschachtelte Eingabe mit dem Element definieren, das als Quelle verwendet werden soll. 

Im obigen Beispiel wurden die Stimmungsanalyse und Schlüsselbegriffserkennung für Text durchgeführt, der zur effizienteren Analyse in Seiten aufgeteilt wurde. Wenn die Bewertungen und Begriffe in eine Tabelle projiziert werden sollen, müssen Sie nun den Kontext auf eine geschachtelte Eingabe festlegen, die die Bewertung und den Begriff enthält.

### <a name="projecting-a-shape-into-multiple-tables"></a>Projizieren einer Struktur in mehrere Tabellen

Mit dem im obigen Abschnitt `tableprojection` definierten `outputs`-Knoten können Sie Teile des `tableprojection`-Knotens in einzelne, zugehörige Tabellen aufteilen:

```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsDocument",
                "generatedKeyName": "Documentid",
                "source": "/document/tableprojection"
            },
            {
                "tableName": "hotelReviewsPages",
                "generatedKeyName": "Pagesid",
                "source": "/document/tableprojection/pages/*"
            },
            {
                "tableName": "hotelReviewsKeyPhrases",
                "generatedKeyName": "KeyPhrasesid",
                "source": "/document/tableprojection/pages/*/keyphrase/*"
            }
        ]
    }
]
```

## <a name="inline-shape-for-table-projections"></a>Inlineform für Tabellenprojektionen

Die Inlinestrukturierung ist die Fähigkeit, neue Strukturen innerhalb der Projektionsdefinition selbst zu bilden. Die Inlinestrukturierung weist die folgenden Merkmale auf:

+ Die Struktur kann nur von der Projektion verwendet werden, die sie enthält.
+ Die Struktur kann mit dem identisch sein, was ein Shaper-Skill erzeugen würde.

Eine Inlinestruktur wird mithilfe von `sourceContext` und `inputs` erstellt.

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| sourceContext | Legt den Stamm der Projektion fest.  |
| inputs | Jede Eingabe ist eine Spalte in der Tabelle. „name“ ist der Spaltenname. „source“ ist der Anreicherungsknoten, der den Wert bereitstellt. |

Um die gleichen Daten wie im vorherigen Beispiel zu projizieren, würde die Inlineprojektionsoption wie folgt aus:

```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsInlineDocument",
                "generatedKeyName": "Documentid",
                "sourceContext": "/document",     
                "inputs": [
                    {
                        "name": "reviews_text",
                        "source": "/document/reviews_text"
                    },
                    {
                        "name": "reviews_title",
                        "source": "/document/reviews_title"
                    },
                    {
                        "name": "AzureSearch_DocumentKey",
                        "source": "/document/AzureSearch_DocumentKey"
                    }                             
                ]
            },
            {
                "tableName": "hotelReviewsInlinePages",
                "generatedKeyName": "Pagesid",
                "sourceContext": "/document/reviews_text/pages/*",
                "inputs": [
                        {
                    "name": "Sentiment",
                    "source": "/document/reviews_text/pages/*/Sentiment"
                    },
                    {
                        "name": "LanguageCode",
                        "source": "/document/Language"
                    },
                    {
                        "name": "Page",
                        "source": "/document/reviews_text/pages/*"
                    }
                ]
            },
            {
                "tableName": "hotelReviewsInlineKeyPhrases",
                "generatedKeyName": "KeyPhraseId",
                "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                "inputs": [
                    {
                        "name": "Keyphrases",
                        "source": "/document/reviews_text/pages/*/Keyphrases/*"
                    }
                ]
            }
        ]
    }
]
```
  
Bei beiden Ansätzen ist zu beobachten, wie Werte von „Keyphrases“ mithilfe von „sourceContext“ projiziert werden. Der „Keyphrases“-Knoten, der eine Sammlung von Zeichenfolgen enthält, ist selbst ein untergeordnetes Element des Seitentexts. Da Projektionen jedoch ein JSON-Objekt erfordern und es sich bei der Seite um einen Grundtyp (Zeichenfolge) handelt, wird „sourceContext“ verwendet, um den Schlüsselbegriff mit einem Objekt mit einer benannten Eigenschaft zu umschließen. Diese Technik ermöglicht, dass sogar Grundtypen unabhängig projiziert werden können.

<a name="inline-shape"></a>

## <a name="inline-shape-for-object-projections"></a>Inlineform für Objektprojektionen

Sie können mithilfe der Shaper-Qualifikation eine neue Form generieren oder die Inline-Strukturierung der Objektprojektion verwenden. Während das Tabellenbeispiel den Ansatz der Erstellung einer Form und der anschließenden Aufteilung zeigt, veranschaulicht dieses Beispiel die Verwendung der Inline-Strukturierung. 

Die Inline-Strukturierung ist die Möglichkeit, eine neue Form in der Definition der Eingaben für eine Projektion zu erstellen. Bei der Inlinestrukturierung wird ein anonymes Objekt erstellt, das mit dem Ergebnis einer Shaper-Ausführung identisch ist (in diesem Fall `projectionShape`). Die Inline-Strukturierung ist nützlich, wenn Sie eine Form definieren, die nicht wiederverwendet werden soll.

Die Projektionseigenschaft ist ein Array. In diesem Beispiel wird dem Array eine neue Projektionsinstanz hinzugefügt, in der die knowledgeStore-Definition Inline-Projektionen enthält. Wenn Sie Inlineprojektionen verwenden, können Sie den Shaper-Skill weglassen.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
            {
            "tables": [ ],
            "objects": [
                {
                    "storageContainer": "sampleobject",
                    "source": null,
                    "generatedKeyName": "myobject",
                    "sourceContext": "/document",
                    "inputs": [
                        {
                            "name": "metadata_storage_name",
                            "source": "/document/metadata_storage_name"
                        },
                        {
                            "name": "metadata_storage_path",
                            "source": "/document/metadata_storage_path"
                        },
                        {
                            "name": "content",
                            "source": "/document/content"
                        },
                        {
                            "name": "keyPhrases",
                            "source": "/document/merged_content/keyphrases/*"
                        },
                        {
                            "name": "entities",
                            "source": "/document/merged_content/entities/*/name"
                        },
                        {
                            "name": "ocrText",
                            "source": "/document/normalized_images/*/text"
                        },
                        {
                            "name": "ocrLayoutText",
                            "source": "/document/normalized_images/*/layoutText"
                        }
                    ]

                }
            ],
            "files": []
        }
    ]
}
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel werden die Konzepte und Prinzipien von Projektionsstrukturen beschrieben. Im nächsten Schritt erfahren Sie, wie diese in Mustern für Tabellen-, Objekt- und Dateiprojektionen angewendet werden.

> [!div class="nextstepaction"]
> [Definieren von Projektionen in einem Wissensspeicher](knowledge-store-projections-examples.md)
