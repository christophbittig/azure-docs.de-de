---
title: Ausführen einer Sprachenerkennung
titleSuffix: Azure Cognitive Services
description: In diesem Artikel erfahren Sie, wie Sie die Sprache eines geschriebenen Texts mithilfe der Sprachenerkennung erkennen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: sample
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-language-detection, ignite-fall-2021
ms.openlocfilehash: 55584c58ebba3faf3d9517cf65827fd784d331a6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131100471"
---
# <a name="how-to-use-language-detection"></a>Verwenden der Sprachenerkennung

Das Feature für die Sprachenerkennung kann Text auswerten und einen Sprachbezeichner zurückgeben, der die Sprache angibt, in der ein Dokument geschrieben wurde.

Die Sprachenerkennung ist für Inhaltsspeicher nützlich, die beliebigen Text erfassen, dessen Sprache unbekannt ist. Sie können die Analyseergebnisse analysieren, um die Sprache des Eingabedokuments zu bestimmen. Die Antwort gibt außerdem eine Bewertung zwischen 0 und 1 zurück, um die Konfidenz des Modells anzugeben.

Mit dem Feature für die Sprachenerkennung können eine Vielzahl von Sprachen, Varianten und Dialekten sowie einige Regional- und Kultursprachen erkannt werden.

> [!TIP]
> Informationen zu den ersten Schritte zur Verwendung dieses Features finden Sie im [Schnellstartartikel](../quickstart.md). Sie können auch Beispielanforderungen in [Language Studio](../../language-studio.md) erstellen, ohne Code schreiben zu müssen.

## <a name="determine-how-to-process-the-data-optional"></a>Festlegen der Art der Datenverarbeitung (optional)

### <a name="specify-the-language-detection-model"></a>Angeben des Sprachenerkennungsmodells

Standardmäßig verwendet die Sprachenerkennung das neueste verfügbare KI-Modell für Ihren Text. Sie können Ihre API-Anforderungen auch für die Verwendung einer bestimmten Modellversion konfigurieren. Das angegebene Modell wird verwendet, um Sprachenerkennungsvorgänge durchzuführen.

| Unterstützte Versionen | Aktuelle Version |
|--|--|
|  `2019-10-01`, `2020-07-01`, `2020-09-01`, `2021-01-05` | `2021-01-05`   |

### <a name="input-languages"></a>Eingabesprachen

Wenn Sie auszuwertende Dokumente übermitteln, versucht die Sprachenerkennung, festzustellen, ob der Text in einer der [unterstützten Sprachen](../language-support.md) geschrieben wurde.  

Bei Inhalten in einer seltener verwendeten Sprache können Sie das Feature für die Sprachenerkennung ausprobieren, um zu sehen, ob es einen Code zurückgibt. Die Antwort bei Sprachen, die nicht erkannt werden können, lautet `unknown`.

## <a name="submitting-data"></a>Übermitteln der Daten

> [!TIP]
> Sie können einen [Docker-Container](use-containers.md) für die Sprachenerkennung verwenden, sodass Sie die API lokal verwenden können.

Die Analyse erfolgt, wenn die Anforderung eingeht. Informationen zur Größe und Anzahl von Anforderungen, die Sie pro Minute und Sekunde senden können, finden Sie in den nachstehend aufgeführten Datengrenzwerten.

Die synchrone Verwendung des Spracherkennungsfeatures ist zustandslos. Auf Ihrem Konto werden keine Daten gespeichert, und die Ergebnisse werden sofort in der Antwort zurückgegeben.

Wenn Sie dieses Feature asynchron verwenden, sind die API-Ergebnisse ab der Erfassung der Anforderung wie in der Antwort angegeben 24 Stunden lang verfügbar. Nach diesem Zeitraum werden die Ergebnisse endgültig gelöscht und stehen nicht mehr zum Abruf zur Verfügung.


## <a name="getting-language-detection-results"></a>Abrufen von Sprachenerkennungsergebnissen

Wenn Sie Ergebnisse von der Sprachenerkennung erhalten, können Sie die Ergebnisse an eine Anwendung streamen oder die Ausgabe in einer Datei im lokalen System speichern.

Die Sprachenerkennung gibt für jedes übermittelte Dokument eine vorherrschende Sprache sowie den [ISO 639-1](https://www.iso.org/standard/22109.html)-Namen, einen Anzeigenamen und eine Konfidenzbewertung zurück. Eine positive Bewertung von 1 stellt das höchstmögliche Konfidenzniveau der Analyse dar.

### <a name="ambiguous-content"></a>Mehrdeutige Inhalte

In einigen Fällen ist es unter Umständen schwierig, die Sprachen basierend auf den Eingaben eindeutig zu bestimmen. Mithilfe des Parameters `countryHint` können Sie einen Länder-/Regionscode gemäß [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2) angeben. Standardmäßig verwendet die API „US“ als Standardangabe für Land/Region. Wenn dieses Verhalten nicht erwünscht ist, können Sie diesen Parameter zurücksetzen, indem Sie diesen Wert auf eine leere Zeichenfolge, `countryHint = ""`, festlegen.

„Impossible“ ist beispielsweise sowohl im Englischen als auch im Französischen gebräuchlich, und bei unzureichendem Kontext basiert die Antwort auf dem Länder-/Regionshinweis „US“. Sollte bekannt sein, dass der Text aus Frankreich stammt, kann ein entsprechender Hinweis angegeben werden.

**Input** (Eingabe)

```json
{
    "documents": [
        {
            "id": "1",
            "text": "impossible"
        },
        {
            "id": "2",
            "text": "impossible",
            "countryHint": "fr"
        }
    ]
}
```

Das Sprachenerkennungsmodell verfügt jetzt über zusätzlichen Kontext, um eine bessere Beurteilung zu ermöglichen: 

**Ausgabe**

```json
{
    "documents":[
        {
            "detectedLanguage":{
                "confidenceScore":0.62,
                "iso6391Name":"en",
                "name":"English"
            },
            "id":"1",
            "warnings":[
                
            ]
        },
        {
            "detectedLanguage":{
                "confidenceScore":1.0,
                "iso6391Name":"fr",
                "name":"French"
            },
            "id":"2",
            "warnings":[
                
            ]
        }
    ],
    "errors":[
        
    ],
    "modelVersion":"2020-09-01"
}
```

Wenn das Analysetool die Eingabe nicht analysieren kann, wird `(Unknown)` zurückgegeben. Ein Beispiel dafür ist die Übermittlung einer Textzeichenfolge, die ausschließlich aus Zahlen besteht.

```json
{
    "documents": [
        {
            "id": "1",
            "detectedLanguage": {
                "name": "(Unknown)",
                "iso6391Name": "(Unknown)",
                "confidenceScore": 0.0
            },
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2021-01-05"
}
```

### <a name="mixed-language-content"></a>Inhalt in verschiedenen Sprachen

Wenn in einem Dokument Inhalte in verschiedenen Sprachen enthalten sind, wird die Sprache zurückgegeben, die in den Inhalten überwiegt – allerdings mit einer niedrigeren positiven Bewertung. Die Bewertung spiegelt die marginale Stärke dieser Bewertung wider. Im folgenden Beispiel enthält die Eingabe eine Mischung aus Englisch, Spanisch und Französisch. Das Analysetool zählt die Zeichen der einzelnen Segmente, um die vorherrschende Sprache zu bestimmen.

**Input** (Eingabe)

```json
{
    "documents": [
        {
            "id": "1",
            "text": "Hello, I would like to take a class at your University. ¿Se ofrecen clases en español? Es mi primera lengua y más fácil para escribir. Que diriez-vous des cours en français?"
        }
    ]
}
```

**Ausgabe**

Die resultierende Ausgabe enthält die vorherrschende Sprache mit einer Punktzahl von weniger als 1,0, wodurch eine niedrigere Zuverlässigkeit angegeben wird.

```json
{
    "documents": [
        {
            "id": "1",
            "detectedLanguage": {
                "name": "Spanish",
                "iso6391Name": "es",
                "confidenceScore": 0.88
            },
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2021-01-05"
}
```

## <a name="data-limits"></a>Datengrenzwerte

> [!NOTE]
> * Wenn Sie größere Dokumente analysieren müssen, als der Grenzwert zulässt, können Sie den Text in kleinere Textabschnitte aufteilen, bevor Sie ihn an die API senden. 
> * Ein Dokument ist eine einzelne aus Textzeichen bestehende Zeichenfolge.  

| Begrenzung | Wert |
|------------------------|---------------|
| Maximale Größe eines einzelnen Dokuments (synchron) | 5\.120 Zeichen (gemessen von [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements)). |
| Maximale Zeichenanzahl pro Anforderung (asynchron)  | 125.000 Zeichen für alle übermittelten Dokumente, gemessen mithilfe von [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Maximale Größe der gesamte Anforderung | 1 MB.  |
| Max. Anzahl von Dokumenten pro Anforderung | 1000 |

Wenn bei einem Dokument das Zeichenlimit überschritten wird, verhält sich die API abhängig vom verwendeten Endpunkt unterschiedlich:

* Asynchron: Die API lehnt die gesamte Anforderung ab und gibt einen Fehler vom Typ `400 bad request` zurück, wenn ein enthaltenes Dokument die maximal zulässige Größe überschreitet.
* Synchron: Dokumente, die die maximal zulässige Größe überschreiten, werden von der API nicht verarbeitet, und es wird jeweils ein Fehler mit einem Hinweis auf ein ungültiges Dokument zurückgegeben. Wenn eine API-Anforderung mehrere Dokumente umfasst, fährt die API mit deren Verarbeitung fort, sofern sie sich innerhalb des Zeichenlimits befinden.

### <a name="rate-limits"></a>Ratenbegrenzungen

Die Ratenbegrenzung variiert je nach [Tarif](https://aka.ms/unifiedLanguagePricing).

| Tarif          | Anforderungen pro Sekunde | Anforderungen pro Minute |
|---------------|---------------------|---------------------|
| S/Mehrere Dienste | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |

## <a name="see-also"></a>Siehe auch

* [Übersicht über die Sprachenerkennung](../overview.md)
