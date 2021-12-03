---
title: Aufrufen der Bildanalyse-API
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie die Bildanalyse-API aufrufen und ihr Verhalten konfigurieren.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 46334015d932f667e5bbb7a10153e9b70aaa0764
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132058597"
---
# <a name="call-the-image-analysis-api"></a>Aufrufen der Bildanalyse-API

In diesem Artikel wird veranschaulicht, wie Sie die Bildanalyse-API aufrufen, um Informationen zu den visuellen Merkmalen eines Bilds zurückzugeben.

In diesem Leitfaden wird davon ausgegangen, dass Sie bereits eine <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Ressource für maschinelles Sehen erstellt"  target="_blank">Erstellen einer Ressource für maschinelles Sehen-Ressource</a> und einen Abonnementschlüssel und eine Endpunkt-URL erhalten haben. Falls nicht, führen Sie die Schritte in [dieser Schnellstartanleitung](../quickstarts-sdk/image-analysis-client-library.md) aus, um loszulegen.
  
## <a name="submit-data-to-the-service"></a>Übermitteln von Daten an den Dienst

Sie übermitteln entweder ein lokales Bild oder ein Remotebild an die Analyse-API. Bei einem lokalen Bild platzieren Sie die binären Bilddaten im HTTP-Anforderungstext. Bei einem lokalen Bild geben Sie die URL des Bilds an, indem Sie den Anforderungstext wie folgt formatieren: `{"url":"http://example.com/images/test.jpg"}`.

## <a name="determine-how-to-process-the-data"></a>Festlegen, wie die Daten verarbeitet werden sollen

###  <a name="select-visual-features"></a>Auswählen visueller Merkmale

Die [Analyse-API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f21b) bietet Ihnen Zugriff auf alle Bildanalysefunktionen des Diensts. Sie müssen angeben, welche Funktionen Sie verwenden möchten, indem Sie die URL-Abfrageparameter festlegen. Ein Parameter kann mehrere, durch Kommas getrennte Werte enthalten. Für jede von Ihnen angegebene Funktion ist eine zusätzliche Berechnungszeit erforderlich. Geben Sie daher nur die erforderlichen Funktionen an.

|URL-Parameter | Wert | Beschreibung|
|---|---|--|
|`visualFeatures`|`Adult` | Erkennt, ob das Bild pornografischer Natur (Nacktheit oder sexuelle Handlungen) oder gewalttätig (extreme Gewalt oder Blut) ist. Zweideutige und freizügige Inhalte werden ebenfalls erkannt.|
||`Brands` | Erkennt verschiedene Marken in einem Bild, einschließlich der ungefähren Position. Das Brands-Argument ist nur für Englisch verfügbar.|
||`Categories` | Kategorisiert Bildinhalte gemäß einer Taxonomie, die in der Dokumentation definiert ist. Dies ist der Standardwert für `visualFeatures`.|
||`Color` | Ermittelt die Akzentfarbe und die dominante Farbe und ob ein Bild schwarz-weiß ist.|
||`Description` | Beschreibt den Bildinhalt in unterstützten Sprachen mit einem vollständigen Satz.|
||`Faces` | Erkennt, ob Gesichter vorhanden sind. Wenn Gesichter vorhanden sind, generiert dieses Merkmal Informationen zu Koordinaten, Geschlecht und Alter.|
||`ImageType` | Erkennt, ob es sich beim Bild um ClipArt oder eine Strichzeichnung handelt.|
||`Objects` | Erkennt verschiedene Objekte in einem Bild, einschließlich der ungefähren Position. Das Objects-Argument ist nur für Englisch verfügbar.|
||`Tags` | Erstellt Tags für das Bild in einer detaillierten Liste aus Wörtern, die sich auf den Bildinhalt beziehen.|
|`details`| `Celebrities` | Identifiziert Prominente in einem Bild.|
||`Landmarks` |Identifiziert Sehenswürdigkeiten in einem Bild.|

Eine aufgefüllte URL kann folgendermaßen aussehen:

`https://{endpoint}/vision/v2.1/analyze?visualFeatures=Description,Tags&details=Celebrities`

### <a name="specify-languages"></a>Angeben von Sprachen

Sie können auch die Sprache der zurückgegebenen Daten angeben. Die Sprache wird mit dem folgenden URL-Abfrageparameter angegeben. Standardwert: `en`.

|URL-Parameter | Wert | Beschreibung|
|---|---|--|
|`language`|`en` | Englisch|
||`es` | Spanisch|
||`ja` | Japanisch|
||`pt` | Portugiesisch|
||`zh` | Chinesisch (vereinfacht)|

Eine aufgefüllte URL kann folgendermaßen aussehen:

`https://{endpoint}/vision/v2.1/analyze?visualFeatures=Description,Tags&details=Celebrities&language=en`

> [!NOTE]
> **Bereichsbezogene API-Aufrufe**
>
> Einige der Funktionen in der Bildanalyse können sowohl direkt als auch über den Aufruf der Analyse-API aufgerufen werden. Sie können beispielsweise eine Bereichsanalyse nur von Bildtags durchführen, indem Sie eine Anforderung an `https://{endpoint}/vision/v3.2/tag` senden. Informationen zu weiteren Funktionen, die separat aufgerufen werden können, finden Sie in der [Referenzdokumentation](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f21b).

## <a name="get-results-from-the-service"></a>Abrufen von Ergebnissen aus dem Dienst

Der Dienst gibt eine `200`-HTTP-Antwort zurück, und der Text enthält die zurückgegebenen Daten in Form einer JSON-Zeichenfolge. Das folgende Beispiel zeigt eine JSON-Antwort:

```json
{  
  "tags":[  
    {  
      "name":"outdoor",
      "score":0.976
    },
    {  
      "name":"bird",
      "score":0.95
    }
  ],
  "description":{  
    "tags":[  
      "outdoor",
      "bird"
    ],
    "captions":[  
      {  
        "text":"partridge in a pear tree",
        "confidence":0.96
      }
    ]
  }
}
```

In der folgenden Tabelle finden Sie Erläuterungen zu den Feldern in diesem Beispiel:

Feld | type | Inhalt
------|------|------|
`Tags`  | `object` | Das Objekt der obersten Ebene für das Array von Tags
tags[].Name | `string`    | Das Schlüsselwort vom Tags-Klassifizierer
tags[].Score    | `number`    | Die Zuverlässigkeitsbewertung, zwischen 0 und 1
description     | `object`    | Das Objekt der obersten Ebene für eine Bildbeschreibung
description.tags[] |    `string`    | Die Liste der Tags. Wenn nicht sicher ist, dass eine Beschriftung erstellt werden kann, sind diese Tags ggf. die einzigen Informationen, die dem Aufrufenden zur Verfügung stehen.
description.captions[].text    | `string`    | Ein Ausdruck, der das Bild beschreibt.
description.captions[].confidence    | `number`    | Die Zuverlässigkeit für den Ausdruck

### <a name="error-codes"></a>Fehlercodes

Die folgende Liste enthält mögliche Fehler und ihre Ursachen:

* 400
    * InvalidImageUrl: Die Bild-URL ist falsch formatiert, oder es kann nicht darauf zugegriffen werden.
    * InvalidImageFormat: Bei den Eingabedaten handelt es sich nicht um ein gültiges Bild.
    * InvalidImageSize: Das Eingabebild ist zu groß.
    * NotSupportedVisualFeature: Der angegebene Merkmalstyp ist ungültig.
    * NotSupportedImage: Nicht unterstütztes Bild, etwa Kinderpornografie
    * InvalidDetails: Nicht unterstützter Parameterwert für `detail`
    * NotSupportedLanguage: Der angeforderte Vorgang wird in der angegebenen Sprache nicht unterstützt.
    * BadArgument: Zusätzliche Details werden in der Fehlermeldung bereitgestellt.
* Fehler 415: Nicht unterstützter Medientyp. Für den Inhaltstyp ist kein zulässiger Typ angegeben:
    * Bild-URL: Als Inhaltstyp muss „application/json“ festgelegt werden.
    * Binäre Bilddaten: Als Inhaltstyp muss „application/octet-stream“ oder „multipart/form-data“ festgelegt werden.
* 500
    * FailedToProcess
    * Timeout: Timeout bei der Bildverarbeitung
    * InternalServerError

> [!TIP]
> Bei der Verwendung von maschinellem Sehen kann es zu vorübergehenden Fehlern kommen. Diese können auf durch den Dienst erzwungene [Ratenlimits](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) oder andere vorübergehende Probleme (etwa Netzwerkausfälle) zurückzuführen sein. Informationen zur Behandlung solcher Fehler finden Sie im Leitfaden für Cloudentwurfsmuster unter [Wiederholungsmuster](/azure/architecture/patterns/retry) sowie unter dem dazugehörigen [Trennschalter-Muster](/azure/architecture/patterns/circuit-breaker).


## <a name="next-steps"></a>Nächste Schritte

Wenn Sie die REST-API ausprobieren möchten, rufen Sie die [Referenz zur Bildanalyse-API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f21b) auf.