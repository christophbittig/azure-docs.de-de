---
title: Datenformate für die benutzerdefinierte Textklassifizierung
titleSuffix: Azure Cognitive Services
description: Enthält Informationen zu den Datenformaten, die von der benutzerdefinierten Entitätsextraktion akzeptiert werden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: f99dd2b0d540cad6fad605e264df4b6054240e35
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101516"
---
# <a name="accepted-data-formats"></a>Akzeptierte Datenformate

Wenn Daten von Ihrem Modell zu Lernzwecken verwendet werden, wird erwartet, dass die Daten in einem bestimmten Format vorliegen. Wenn Sie Ihre Daten in Language Studio mit Tags versehen, werden sie in das in diesem Artikel beschriebene JSON-Format konvertiert. Sie können Ihre Dateien auch manuell mit Tags versehen.


## <a name="json-file-format"></a>JSON-Dateiformat

Ihre Tagdatei sollte das unten angegebene `json`-Format haben.

```json
{
    "classifiers": [
        {
            "name": "Class1"
        },
        {
            "name": "Class2"
        }
    ],
    "documents": [
        {
            "location": "doc1.txt",
            "language": "en-us",
            "classifiers": [
                {
                    "classifierName": "Class2"
                },
                {
                    "classifierName": "Class1"
                }
            ]
        }
    ]
}
```

### <a name="data-description"></a>Datenbeschreibung

* `classifiers`: Ein Array mit Klassifizierern für Ihre Daten. Jeder Klassifizierer steht für eine der Klassen, mit denen Sie Ihre Daten per Tag kennzeichnen möchten.
* `documents`: Ein Array mit per Tag gekennzeichneten Dokumenten. Beispiel:
  * `location`: Der Pfad der JSON-Datei, in der die Tags enthalten sind. Die Tagdatei muss sich im Stammverzeichnis des Speichercontainers befinden.
  * `language`: Die Sprache des Dokuments. Verwenden Sie eines der [unterstützten Kulturgebietsschemas](../language-support.md).
  * `classifiers`: Array mit Klassifizierungsobjekten, die dem Dokument zugewiesen sind. Wenn Sie nur an einem Klassifizierungsprojekt arbeiten, sollte auch nur ein Klassifizierer vorhanden sein.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Versehen Ihrer Daten mit Tags finden Sie im [Anleitungsartikel](../how-to/tag-data.md). Wenn Sie die Kennzeichnung Ihrer Daten abgeschlossen haben, können Sie [Ihr Modell trainieren](../how-to/train-model.md).  
