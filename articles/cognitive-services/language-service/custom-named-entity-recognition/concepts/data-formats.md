---
title: Datenformate für die benutzerdefinierte Erkennung benannter Entitäten
titleSuffix: Azure Cognitive Services
description: Erfahren Sie mehr über die Datenformate, die von der benutzerdefinierten NER akzeptiert werden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: de37751b3d814110b5d37f1b7c84d41278b2bea9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101757"
---
# <a name="data-formats-accepted-by-custom-ner"></a>Von der benutzerdefinierten Erkennung benannter Entitäten akzeptierte Datenformate

Wenn Daten von Ihrem Modell zum Lernen verwendet werden, wird erwartet, dass die Daten in einem bestimmten Format vorliegen. Wenn Sie Ihre Daten in Language Studio mit Tags versehen, werden sie in das in diesem Artikel beschriebene JSON-Format konvertiert. Sie können Ihre Dateien auch manuell mit Tags versehen.


## <a name="json-file-format"></a>JSON-Dateiformat

Beim Kennzeichnen von Entitäten werden die Tags wie im folgenden JSON-Format gespeichert. Wenn Sie eine Tagdatei hochladen, sollte sie das gleiche Format aufweisen.

```json
{
    //List of entity names. Their index within this array is used as an ID. 
    "entityNames": [
        "entity_name1",
        "entity_name2"
    ],
    "documents": "path_to_document", //Relative file path to get the text.
    "culture": "en-US", //Standard culture strings supported by CultureInfo.
    "entities": [
        {
            "regionStart": 0,
            "regionLength": 69,
            "labels": [
                {
                    "entity": 0, // Index of the entity in the "entityNames" array. Positions are relative to the original text (not bounding box)
                    "start": 4,
                    "length": 10
                },
                {
                    "entity": 1,
                    "start": 18,
                    "length": 11
                }
            ]
        }
    ]    
}
```

In der folgenden Liste werden die verschiedenen JSON-Eigenschaften des Beispiels oben beschrieben.

* `entityNames`: Ein Array von Entitätsnamen. Der Index einer Entität innerhalb des Arrays wird als ihre ID verwendet.
* `documents`: Ein Array mit gekennzeichneten Dokumenten.
  * `location`: Der Pfad des Dokuments relativ zur JSON-Datei. Beispielsweise für Dokumente auf der gleichen Ebene wie die Tagdatei `file.txt`, für Dokumente innerhalb einer Verzeichnisebene `dir1/file.txt`.
  * `culture`: Kultur/Sprache des Dokuments. <!-- See [language support](../language-support.md) for more information. -->
  * `entities`: Gibt die Tags zur Entitätserkennung an.
    * `regionStart`: Die inklusive Zeichenposition des Textanfangs.
    * `regionLength`: Die Länge des Begrenzungsfelds, ausgedrückt in UTF16-Zeichen. Beim Training werden nur die Daten in diesem Bereich berücksichtigt. Wenn es sich also um eine gekennzeichnete Datei handelt, legen Sie `regionStart` auf 0 und die `regionLength` auf den letzten Index des letzten Zeichens in der Datei fest. Sie können diesen Bereich auch festlegen, wenn Sie ein negatives Beispiel in das Training einführen möchten, indem Sie den Bereich als Teil der Datei ohne Tags definieren.

    * `labels`: Alle Tags, die innerhalb des Begrenzungsfelds auftreten.
      * `entity`: Der Index der Entität im `entityNames`-Array.
      * `start`: Die inklusive Zeichenposition des Beginns des Tags im Dokumenttext. Diese ist nicht relativ zum Begrenzungsfeld.
      * `length`: Die Länge des Tags, ausgedrückt in UTF16-Zeichen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Versehen Ihrer Daten mit Tags finden Sie im [Anleitungsartikel](../how-to/tag-data.md). Wenn Sie die Kennzeichnung Ihrer Daten abgeschlossen haben, können Sie [Ihr Modell trainieren](../how-to/train-model.md).  
