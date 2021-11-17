---
title: Beispiele für eine Projektion
titleSuffix: Azure Cognitive Search
description: Erkunden Sie ein ausführliches Beispiel, in dem die Ausgabe eines umfangreichen Skillsets in komplexen Formen projiziert wird, die die Struktur und Zusammensetzung von Inhalten in einem Wissensspeicher ergeben.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: a820101897ae20dbdeac029187d4ace356ae08af
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491106"
---
# <a name="detailed-example-of-shapes-and-projections-in-a-knowledge-store"></a>Detailliertes Beispiel für Formen und Projektionen in einem Wissensspeicher

Dieser Artikel enthält ein ausführliches Beispiel, das [grundlegende Konzepte](knowledge-store-projection-overview.md) und [syntaxbasierte Artikel](knowledge-store-projections-examples.md) ergänzt, indem Sie durch die Schritte zur Strukturierung und Projektion geführt werden, die für den vollständigen Ausdruck der Ausgabe eines umfangreichen Skillsets in einem [Wissensspeicher](knowledge-store-concept-intro.md) erforderlich sind.

Wenn Ihre Anwendungsanforderungen mehrere Skills und Projektionen erfordern, können Sie anhand dieses Beispiels besser verstehen, wie sich Formen und Projektionen überschneiden.

## <a name="download-sample-definitions"></a>Herunterladen von Beispieldefinitionen

In diesem Beispiel werden die [Postman Desktop-Anwendung](https://www.postman.com/downloads/) und die [Search-REST-APIs](/rest/api/searchservice/) verwendet.

Klonen Sie oder laden Sie [azure-search-postman-samples](https://github.com/Azure-Samples/azure-search-postman-samples) auf GitHub herunter, und importieren Sie die [**Projektionssammlung**](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/projections), um die einzelnen Schritte dieses Beispiels selbst auszuführen.

## <a name="set-up-sample-data"></a>Einrichten von Beispieldaten

Beispieldokumente sind nicht speziell in der Sammlung Projektionen enthalten, doch die [Demodatendateien zur KI-Anreicherung](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/ai-enrichment-mixed-media) aus dem [Repository azure-search-sample-data](https://github.com/Azure-Samples/azure-search-sample-data) enthalten Text und Bilder, um die in diesem Beispiel beschriebenen Projektionen zu unterstützen.

Erstellen Sie in Azure Storage einen Blobcontainer, und laden Sie alle 14 Elemente hoch.

Kopieren Sie in Azure Storage eine Verbindungszeichenfolge, damit Sie diese in der Postman-Sammlung angeben können.

## <a name="example-skillset"></a>Beispielskillset

Um die Abhängigkeit zwischen Formen und Projektionen zu verstehen, sehen Sie sich das folgende Skillset an, das angereicherte Inhalte erstellt. Dieses Skillset verarbeitet sowohl Rohdatenbilder als auch Text und erzeugt Ausgaben, auf die in Formen und Projektionen verwiesen wird.

Achten Sie besonders auf Skillausgaben (targetNames). Auf Ausgaben, die in die angereicherte Dokumentstruktur geschrieben werden, wird in Projektionen und in Formen (über Shaper-Skills) verwiesen.

```json
{
    "name": "projections-demo-ss",
    "description": "Skillset that enriches blob data found in "merged_content". The enrichment granularity is a document.",
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
            "name": "#1",
            "description": null,
            "context": "/document/merged_content",
            "categories": [
                "Person",
                "Quantity",
                "Organization",
                "URL",
                "Email",
                "Location",
                "DateTime"
            ],
            "defaultLanguageCode": "en",
            "minimumPrecision": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "organizations",
                    "targetName": "organizations"
                },
                {
                    "name": "locations",
                    "targetName": "locations"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "name": "#2",
            "description": null,
            "context": "/document/merged_content",
            "defaultLanguageCode": "en",
            "maxKeyPhraseCount": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyphrases"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "name": "#3",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "language"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
            "name": "#4",
            "description": null,
            "context": "/document",
            "insertPreTag": " ",
            "insertPostTag": " ",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "itemsToInsert",
                    "source": "/document/normalized_images/*/text"
                },
                {
                    "name": "offsets",
                    "source": "/document/normalized_images/*/contentOffset"
                }
            ],
            "outputs": [
                {
                    "name": "mergedText",
                    "targetName": "merged_content"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
            "name": "#5",
            "description": null,
            "context": "/document/normalized_images/*",
            "textExtractionAlgorithm": "printed",
            "lineEnding": "Space",
            "defaultLanguageCode": "en",
            "detectOrientation": true,
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "text",
                    "targetName": "text"
                },
                {
                    "name": "layoutText",
                    "targetName": "layoutText"
                }
            ]
        }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "A Cognitive Services resource in the same region as Search.",
        "key": "<COGNITIVE SERVICES All-in-ONE KEY>"
    },
    "knowledgeStore": null
}
```

## <a name="example-shaper-skill"></a>Shaper-Beispielskill

Ein [Shaper-Skill](cognitive-search-skill-shaper.md) ist ein Hilfsprogramm, das es Ihnen ermöglicht, mit vorhandenen angereicherten Inhalten zu arbeiten, anstatt neue angereicherte Inhalte zu erstellen. Wenn Sie einem Skillset einen Shaper hinzufügen, können Sie eine benutzerdefinierte Form erstellen, die Sie in Tabellen- oder Blobspeicher projizieren können. Ohne eine benutzerdefinierte Form verweisen Projektionen nur auf einen einzelnen Knoten (eine Projektion pro Ausgabe), und dies ist für Tabellen nicht geeignet. Durch das Erstellen einer benutzerdefinierten Form werden verschiedene Elemente in einer neuen logischen Einheit aggregiert, die als eine einzelne Tabelle projiziert oder in Segmenten auf eine Sammlung von Tabellen verteilt werden kann. 

In diesem Beispiel kombiniert die benutzerdefinierte Form Blobmetadaten, identifizierte Entitäten und Schlüsselbegriffe. Der Name der benutzerdefinierten Form lautet `projectionShape` und ist `/document` untergeordnet. 

Ein Zweck der Strukturierung besteht darin, sicherzustellen, dass alle Anreicherungsknoten in wohlgeformten JSON-Objekten ausgedrückt werden. Dies ist für die Projektion in den Wissensspeicher erforderlich. Das gilt insbesondere, wenn eine Anreicherungsstruktur Knoten enthält, die keine wohlgeformten JSON-Objekte sind (beispielsweise wenn eine Anreicherung einem einfachen Typ wie einer Zeichenfolge untergeordnet ist).

Beachten Sie die letzten beiden Knoten `KeyPhrases` und `Entities`. Diese werden von einem gültigen JSON-Objekt mit dem `sourceContext` umschlossen. Dies ist erforderlich, da `keyphrases` und `entities` Anreicherungen von einfachen Typen sind und in gültige JSON-Objekte konvertiert werden müssen, bevor sie projiziert werden können.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForTables",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_content_type",
            "source": "/document/metadata_storage_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_path",
            "source": "/document/metadata_storage_path",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_content_type",
            "source": "/document/metadata_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "Entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "projectionShape"
        }
    ]
}
```

### <a name="add-shapers-to-a-skillset"></a>Hinzufügen von Shapern zu einem Skillset

Der Shaper-Skill war in dem am Anfang dieses Artikels vorgestellten Beispiel-Skillset nicht enthalten, Shaper-Skills gehören jedoch zu einem Skillset und werden häufig am Ende platziert.

Innerhalb eines Skillsets kann ein Shaper-Skill wie folgt aussehen:

```json
    "name": "projections-demo-ss",
    "skills": [
        {
            <Shaper skill goes here>
            }
        ],
    "cognitiveServices":  "A key goes here",
    "knowledgeStore": []
}  
```

## <a name="projecting-to-tables"></a>Projizieren in Tabellen

Auf Grundlage der obigen Beispiele gibt es eine bekannte Menge von Anreicherungen und Datenformen, auf die in Tabellenprojektionen verwiesen werden kann. In der Tabellenprojektion unten werden drei Tabellen definiert, indem die Eigenschaften `tableName`, `source` und `generatedKeyName` festgelegt werden.

Alle drei Tabellen werden über generierte Schlüssel und durch das freigegebene übergeordnete `/document/projectionShape`-Element verknüpft.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [
                {
                    "tableName": "tblDocument",
                    "generatedKeyName": "Documentid",
                    "source": "/document/projectionShape"
                },
                {
                    "tableName": "tblKeyPhrases",
                    "generatedKeyName": "KeyPhraseid",
                    "source": "/document/projectionShape/keyPhrases/*"
                },
                {
                    "tableName": "tblEntities",
                    "generatedKeyName": "Entityid",
                    "source": "/document/projectionShape/Entities/*"
                }
            ],
            "objects": [],
            "files": []
        }
    ]
}
```

### <a name="test-your-work"></a>Testen Ihrer Arbeit

Sie können Projektionsdefinitionen überprüfen, indem Sie die folgenden Schritte ausführen:

1. Legen Sie die `storageConnectionString`-Eigenschaft des Wissensspeichers auf eine gültige Verbindungszeichenfolge für ein Speicherkonto vom Typ „Universell v2“ fest.  

1. Aktualisieren Sie das Skillset durch Ausgabe der PUT-Anforderung.

1. Führen Sie nach dem Aktualisieren des Skillsets den Indexer aus. 

Sie verfügen nun über eine funktionierende Projektion mit drei Tabellen. Wenn [diese Tabellen in Power BI importiert werden](knowledge-store-connect-power-bi.md), sollte Power BI die Beziehungen automatisch ermitteln.

Bevor Sie mit dem nächsten Beispiel fortfahren, sollten Sie die Aspekte der Tabellenprojektion analysieren, um die Verfahren zum Aufteilen und Abgleichen von Daten zu verstehen.

### <a name="slicing-a-table-into-multiple-child-tables"></a>Aufteilen einer Tabelle in mehrere untergeordnete Tabellen

Beim Aufteilen handelt es sich um ein Verfahren, mit dem eine vollständige, konsolidierte Form in ihre Bestandteile unterteilt wird. Das Ergebnis besteht aus separaten, aber verknüpften Tabellen, die Sie einzeln verarbeiten können.

Im Beispiel ist `projectionShape` die konsolidierte Form (oder der Anreicherungsknoten). In der Projektionsdefinition wird `projectionShape` in zusätzliche Tabellen aufgeteilt, sodass Sie die Teile `keyPhrases` und `Entities` der Form abrufen können. Dies ist in Power BI nützlich, wenn jedem Dokument mehrere Entitäten und Schlüsselausdrücke zugeordnet sind. Sie können zusätzliche Erkenntnisse gewinnen, wenn die Entitäten und Schlüsselausdrücke als kategorisierte Daten vorliegen.

Durch das Aufteilen wird implizit eine Beziehung zwischen der übergeordneten und der untergeordneten Tabelle hergestellt. Dabei wird der `generatedKeyName` in der übergeordneten Tabelle verwendet, um in der untergeordneten Tabelle eine Spalte mit demselben Namen zu erstellen. 

### <a name="naming-relationships"></a>Namensbeziehungen

Mithilfe der Eigenschaften `generatedKeyName` und `referenceKeyName` werden Daten über Tabellen hinweg oder sogar über Projektionstypen hinweg miteinander verknüpft. Jede Zeile in der untergeordneten Tabelle verfügt über eine Eigenschaft, die zurück auf das übergeordnete Element verweist. Der Name der Spalte oder Eigenschaft im untergeordneten Element ist der `referenceKeyName` des übergeordneten Elements. Wenn kein `referenceKeyName` bereitgestellt wird, verwendet der Dienst standardmäßig den `generatedKeyName` des übergeordneten Elements. 

Power BI benötigt diese generierten Schlüssel, um Beziehungen innerhalb der Tabellen zu ermitteln. Wenn Sie die Spalte in der untergeordneten Tabelle anders benennen möchten, legen Sie die `referenceKeyName`-Eigenschaft der übergeordneten Tabelle fest. Ein Beispiel wäre das Festlegen von `generatedKeyName` auf die ID in der tblDocument-Tabelle und von `referenceKeyName` auf die DocumentID. Dies würde dazu führen, dass die Spalte in den Tabellen tblEntities und tblKeyPhrases die Dokument-ID mit dem Namen DocumentID enthält.

## <a name="projecting-blob-documents"></a>Projizieren von Blobdokumenten

Objektprojektionen sind JSON-Darstellungen der Anreicherungsstruktur, die aus beliebigen Knoten erstellt werden können. Im Vergleich zu Tabellenprojektionen sind Objektprojektionen einfacher zu definieren und werden bei der Projektion ganzer Dokumente verwendet. Objektprojektionen sind auf eine einzelne Projektion in einem Container beschränkt und können nicht aufgeteilt werden.

Zum Definieren einer Objektprojektion verwenden Sie das Array `objects` in der projections-Eigenschaft.

Die Quelle ist der Pfad zu einem Knoten der Anreicherungsstruktur, der als Stamm der Projektion dient. Der Knotenpfad ist in der Regel die Ausgabe eines Shaper-Skills, auch wenn dies nicht erforderlich ist. Dies liegt daran, dass die meisten Skills keine gültigen JSON-Objekte selbst ausgeben, was bedeutet, dass eine Art der Strukturierung erforderlich ist. In vielen Fällen kann über die Qualifikation Shaper, mit der eine Tabellenprojektion erstellt wird, auch eine Objektprojektion generiert werden. Alternativ kann die Quelle auch auf einen Knoten mit [Inlinestrukturierung](knowledge-store-projection-shape.md#inline-shape) festgelegt werden, um die Struktur zu bieten.

Das Ziel ist immer ein Blobcontainer.

Im folgenden Beispiel werden einzelne Hoteldokumente, ein Hoteldokument pro Blob, in den Container `hotels` projiziert.

```json
"knowledgeStore": {
  "storageConnectionString": "an Azure storage connection string",
  "projections" : [
    {
      "tables": [ ]
    },
    {
      "objects": [
        {
        "storageContainer": "hotels",
        "source": "/document/objectprojection",
        }
      ]
    },
    {
        "files": [ ]
    }
  ]
}
```

Die Quelle ist die Ausgabe des Shaper-Skills namens „objectprojection“. Jedes Blob enthält eine JSON-Darstellung der einzelnen Feldeingaben.

```json
    {
      "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
      "name": "#3",
      "description": null,
      "context": "/document",
      "inputs": [
        {
          "name": "HotelId",
          "source": "/document/HotelId"
        },
        {
          "name": "HotelName",
          "source": "/document/HotelName"
        },
        {
          "name": "Category",
          "source": "/document/Category"
        },
        {
          "name": "keyPhrases",
          "source": "/document/HotelId/keyphrases/*"
        },
      ],
      "outputs": [
        {
          "name": "output",
          "targetName": "objectprojection"
        }
      ]
    }
```

## <a name="projecting-an-image-file"></a>Projizieren einer Bilddatei

Dateiprojektionen sind immer binäre, normalisierte Bilder, wobei sich die Normalisierung auf potenzielle Größenänderungen und Drehungen bezieht, die bei der Ausführung von Skillsets verwendet werden können. Dateiprojektionen werden ähnlich wie Objektprojektionen als Blobs in Azure Storage erstellt und enthalten das Bild.

Zum Definieren einer Dateiprojektion verwenden Sie das Array `files` in der projections-Eigenschaft.

Die Quelle ist immer `/document/normalized_images/*`. Dateiprojektionen wirken sich nur auf die `normalized_images`-Sammlung aus. Weder Indexer noch ein Skillset geben das ursprüngliche nicht normalisierte Bild weiter.

Das Ziel ist immer ein Blobcontainer mit einem Ordnerpräfix des Base64-codierten Werts der Dokument-ID. Dateiprojektionen können nicht denselben Container wie Objektprojektionen verwenden und müssen in einen anderen Container projiziert werden. 

Im folgenden Beispiel werden alle normalisierten Bilder, die aus dem Dokumentknoten eines angereicherten Dokuments extrahiert wurden, in den Container `myImages` projiziert.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [ ],
            "objects": [ ],
            "files": [
                {
                    "storageContainer": "myImages",
                    "source": "/document/normalized_images/*"
                }
            ]
        }
    ]
}
```

## <a name="projecting-to-multiple-types"></a>Projizieren auf mehrere Typen

Ein komplexeres Szenario erfordert möglicherweise das Projizieren der Inhalte auf verschiedene Projektionstypen. Sie können z. B. Schlüsselbegriffe und Entitäten in Tabellen projizieren, die OCR-Ergebnisse von Text und Layouttext als Objekte speichern und dann die Bilder als Dateien projizieren. 

Schritte für mehrere Projektionstypen:

1. Erstellen einer Tabelle mit einer Zeile für jedes Dokument
1. Erstellen einer Tabelle, die mit der Dokumenttabelle verknüpft ist, wobei jeder Schlüsselausdruck als Zeile in dieser Tabelle bezeichnet wird
1. Erstellen einer Tabelle, die mit der Dokumenttabelle verknüpft ist, wobei jede Entität als Zeile in dieser Tabelle bezeichnet wird
1. Erstellen einer Objektprojektion mit dem Layouttext für jedes Bild
1. Erstellen einer Dateiprojektion, die jedes extrahierte Bild projiziert
1. Erstellen einer Querverweistabelle mit Verweisen auf die Dokumenttabelle, die Objektprojektion mit dem Layouttext und die Dateiprojektion

### <a name="shape-data-for-cross-projection"></a>Strukturieren von Daten für eine Kreuzprojektion

Um die für diese Projektionen benötigten Formen zu erhalten, fügen Sie zunächst eine neue Shaper-Qualifikation hinzu, die ein strukturiertes Objekt namens `crossProjection` erstellt. 

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForCrossProjection",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        },
        {
            "name": "images",
            "source": null,
            "sourceContext": "/document/normalized_images/*",
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                },
                {
                    "name": "layoutText",
                    "source": "/document/normalized_images/*/layoutText"
                },
                {
                    "name": "ocrText",
                    "source": "/document/normalized_images/*/text"
                }
                ]
        }
 
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "crossProjection"
        }
    ]
}
```

### <a name="define-table-object-and-file-projections"></a>Definieren von Tabellen-, Objekt- und Dateiprojektionen

Teilen Sie das Objekt aus dem konsolidierten crossProjection-Objekt in mehrere Tabellen auf, erfassen Sie die OCR-Ausgabe als Blobs und speichern Sie das Bild dann als Dateien (auch in Blobspeicher).

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
            {
            "tables": [
                {
                    "tableName": "crossDocument",
                    "generatedKeyName": "Id",
                    "source": "/document/crossProjection"
                },
                {
                    "tableName": "crossEntities",
                    "generatedKeyName": "EntityId",
                    "source": "/document/crossProjection/entities/*"
                },
                {
                    "tableName": "crossKeyPhrases",
                    "generatedKeyName": "KeyPhraseId",
                    "source": "/document/crossProjection/keyPhrases/*"
                },
                {
                    "tableName": "crossReference",
                    "generatedKeyName": "CrossId",
                    "source": "/document/crossProjection/images/*"
                }
                    
            ],
            "objects": [
                {
                    "storageContainer": "crossobject",
                    "generatedKeyName": "crosslayout",
                    "source": null,
                    "sourceContext": "/document/crossProjection/images/*/layoutText",
                    "inputs": [
                        {
                            "name": "OcrLayoutText",
                            "source": "/document/crossProjection/images/*/layoutText"
                        }
                    ]
                }
            ],
            "files": [
                {
                    "storageContainer": "crossimages",
                    "generatedKeyName": "crossimages",
                    "source": "/document/crossProjection/images/*/image"
                }
            ]
        }
    ]
}
```

Objektprojektionen erfordern einen Containernamen für jede Projektion. Objektprojektionen und Dateiprojektionen dürfen nicht in demselben Container verwendet werden. 

### <a name="relationships-among-table-object-and-file-projections"></a>Beziehungen zwischen Tabellen-, Objekt- und Dateiprojektionen

In diesem Beispiel wird ein weiteres Feature von Projektionen veranschaulicht. Durch die Definition mehrerer Projektionstypen innerhalb desselben Projektionsobjekts wird eine Beziehung innerhalb der Typen (Tabellen, Objekte, Dateien) und die verschiedenen Typen übergreifend ausgedrückt. So können Sie mit einer Tabellenzeile für ein Dokument beginnen und den gesamten OCR-Text für die Bilder in diesem Dokument in der Objektprojektion suchen. 

Wenn Sie nicht möchten, dass die Daten in Beziehung gesetzt werden, definieren Sie die Projektionen in verschiedenen Projektionsgruppen. Der folgende Codeausschnitt führt z. B. dazu, dass die Tabellen verknüpft werden, jedoch ohne Beziehungen zwischen den Tabellen- und den Objektprojektionen (OCR-Text) herzustellen. 

Projektionsgruppen sind nützlich, wenn Sie dieselben Daten in unterschiedlichen Formen für verschiedene Anforderungen projizieren möchten. Beispiel: eine Projektionsgruppe für das Power BI-Dashboard und eine weitere Projektionsgruppe für die Erfassung von Daten zum Trainieren eines Machine Learning-Modells in einer benutzerdefinierten Qualifikation.

Wenn Sie Projektionen für unterschiedliche Typen erstellen, werden zunächst die Datei- und Objektprojektionen generiert und die Pfade den Tabellen hinzugefügt.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [
                {
                    "tableName": "unrelatedDocument",
                    "generatedKeyName": "Documentid",
                    "source": "/document/projectionShape"
                },
                {
                    "tableName": "unrelatedKeyPhrases",
                    "generatedKeyName": "KeyPhraseid",
                    "source": "/document/projectionShape/keyPhrases"
                }
            ],
            "objects": [
                
            ],
            "files": []
        }, 
        {
            "tables": [],
            "objects": [
                {
                    "storageContainer": "unrelatedocrtext",
                    "source": null,
                    "sourceContext": "/document/normalized_images/*/text",
                    "inputs": [
                        {
                            "name": "ocrText",
                            "source": "/document/normalized_images/*/text"
                        }
                    ]
                },
                {
                    "storageContainer": "unrelatedocrlayout",
                    "source": null,
                    "sourceContext": "/document/normalized_images/*/layoutText",
                    "inputs": [
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

Das Beispiel in diesem Artikel veranschaulicht allgemeine Muster zum Erstellen von Projektionen. Wenn Sie sich ausführlich mit den Konzepten vertraut gemacht haben, verfügen Sie über bessere Voraussetzungen für das Erstellen von Projektionen für Ihr spezielles Szenario.

> [!div class="nextstepaction"]
> [Konfigurieren der Zwischenspeicherung für die inkrementelle Anreicherung](search-howto-incremental-index.md)
