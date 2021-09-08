---
title: Aufrufen der Lese-API
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie die Lese-API aufrufen und ihr Verhalten im Detail konfigurieren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: pafarley
ms.openlocfilehash: d5a1da6bbe251e6200cd3a64117748e9ebbfae2a
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2021
ms.locfileid: "112298113"
---
# <a name="call-the-read-api"></a>Aufrufen der Lese-API

In dieser Anleitung erfahren Sie, wie Sie die Lese-API zum Extrahieren von Text aus Bildern aufrufen. Sie erfahren mehr über die verschiedenen Möglichkeiten für die Konfiguration dieser API entsprechend Ihren Anforderungen.

In diesem Leitfaden wird davon ausgegangen, dass Sie bereits eine <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Ressource für maschinelles Sehen erstellt"  target="_blank">Erstellen einer Ressource für maschinelles Sehen-Ressource</a> und einen Abonnementschlüssel und eine Endpunkt-URL erhalten haben. Falls nicht, führen Sie die Schritte in [dieser Schnellstartanleitung](../quickstarts-sdk/client-library.md) aus, um loszulegen.

## <a name="submit-data-to-the-service"></a>Übermitteln von Daten an den Dienst

Sie übermitteln entweder ein lokales Bild oder ein Remotebild an die Lese-API. Bei einem lokalen Bild platzieren Sie die binären Bilddaten im HTTP-Anforderungstext. Bei einem lokalen Bild geben Sie die URL des Bilds an, indem Sie den Anforderungstext wie folgt formatieren: `{"url":"http://example.com/images/test.jpg"}`.

Beim [Read-Aufruf](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) der Read-API wird ein Bild oder PDF-Dokument als Eingabe erfasst und asynchron Text extrahiert.

`https://{endpoint}/vision/v3.2/read/analyze[?language][&pages][&readingOrder]`

Der Aufruf wird mit einem Antwortheaderfeld namens `Operation-Location` zurückgegeben. Der `Operation-Location`-Wert ist eine URL, die die Vorgangs-ID enthält, die im nächsten Schritt verwendet werden soll.

|Antwortheader| Beispielwert |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.2/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Abrechnung** 
>
> Die Seite [Preise für maschinelles Sehen](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) enthält den Tarif für Read. Jedes analysierte Bild oder jede analysierte Seite ist eine Transaktion. Wenn Sie den Vorgang mit einem PDF- oder TIFF-Dokument mit 100 Seiten aufrufen, wird dies vom Read-Vorgang als 100 Transaktionen gezählt und Ihnen werden 100 Transaktionen in Rechnung gestellt. Wenn Sie für den Vorgang 50 Aufrufe durchgeführt haben und jeder Aufruf ein Dokument mit 100 Seiten übermittelt hat, werden Ihnen 5000 Transaktionen (50 x 100) in Rechnung gestellt.

## <a name="determine-how-to-process-the-data"></a>Festlegen, wie die Daten verarbeitet werden sollen

### <a name="language-specification"></a>Sprachspezifikation

Der [Read](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)-Aufruf weist einen optionalen Anforderungsparameter für die Sprache auf. Read unterstützt die automatische Sprachidentifikation und mehrsprachige Dokumente. Geben Sie also nur einen Sprachcode an, wenn Sie erzwingen möchten, dass das Dokument in dieser spezifischen Sprache verarbeitet wird.

### <a name="natural-reading-order-output-latin-languages-only"></a>Ausgabe der natürlichen Leserichtung (nur lateinische Sprachen)

Mit dem Abfrageparameter `readingOrder` geben Sie die Leserichtung an, in der die Textzeilen ausgegeben werden. Verwenden Sie `natural` für eine benutzerfreundlichere Ausgabe der Lesereihenfolge, wie im folgenden Beispiel gezeigt. Dieses Feature wird nur für lateinische Sprachen unterstützt.

:::image type="content" source="../Images/ocr-reading-order-example.png" alt-text="Beispiel für OCR-Leserichtung" border="true" :::

### <a name="select-pages-or-page-ranges-for-text-extraction"></a>Auswählen von Seiten oder Seitenbereichen für die Textextraktion

Verwenden Sie bei großen mehrseitigen Dokumenten den Abfrageparameter `pages` zur Angabe von Seitenzahlen oder Seitenbereichen, um Text nur von diesen Seiten zu extrahieren. Das folgende Beispiel zeigt ein Dokument mit 10 Seiten, wobei für beide Fälle – alle Seiten (1-10) und ausgewählte Seiten (3-6) – Text extrahiert wurde.

:::image type="content" source="../Images/ocr-select-pages.png" alt-text="Ausgabe ausgewählter Seiten" border="true" :::

## <a name="get-results-from-the-service"></a>Abrufen von Ergebnissen aus dem Dienst

Der zweite Schritt umfasst das Aufrufen des Vorgangs [Get Read Results](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d9869604be85dee480c8750). Dieser Vorgang erfasst die Vorgangs-ID, die beim Read-Vorgang erstellt wurde, als Eingabe.

`https://{endpoint}/vision/v3.2/read/analyzeResults/{operationId}`

Er gibt eine JSON-Antwort zurück, die ein **Status**-Feld mit den folgenden möglichen Werten enthält.

|Wert | Bedeutung |
|:-----|:----|
| `notStarted`| Der Vorgang wurde noch nicht gestartet. |
| `running`| Der Vorgang wird verarbeitet. |
| `failed`| Beim Vorgang ist ein Fehler aufgetreten. |
| `succeeded`| Der Vorgang war erfolgreich. |

Sie rufen diesen Vorgang iterativ auf, bis er mit dem Wert **succeeded** (erfolgreich) zurückgegeben wird. Verwenden Sie ein Intervall von 1 bis 2 Sekunden, um zu vermeiden, dass die Rate der Anforderungen pro Sekunde (RPS) überschritten wird.

> [!NOTE]
> Im kostenlosen Tarif wird die Anforderungsrate auf 20 Aufrufe pro Minute beschränkt. Im kostenpflichtige Tarif sind 10 Anforderungen pro Sekunde (RPS) zulässig, die auf Anforderung erhöht werden können. Notieren Sie sich ihre Azure-Ressourcen-ID und Ihre Region, und öffnen Sie ein Azure-Supportticket, oder wenden Sie sich an Ihr Kontoteam, um eine höhere RPS-Rate (Request per Second, Anforderungen pro Sekunde) anzufordern.

Wenn das Feld **Status** den Wert `succeeded` aufweist, enthält die JSON-Antwort den extrahierten Textinhalt aus Ihrem Bild oder Dokument. In der JSON-Antwort werden die ursprünglichen Zeilengruppierungen der erkannten Wörter beibehalten. Sie enthält die extrahierten Textzeilen und die zugehörigen Begrenzungsrahmenkoordinaten. Jede Textzeile enthält alle extrahierten Wörter mit den zugehörigen Koordinaten und Zuverlässigkeitsbewertungen.

> [!NOTE]
> Die an den Vorgang `Read` übermittelten Daten werden vorübergehend verschlüsselt, für eine kurze Dauer im Ruhezustand gespeichert und dann gelöscht. Dadurch können Ihre Anwendungen den extrahierten Text als Teil der Dienstantwort abrufen.

### <a name="sample-json-output"></a>JSON-Beispielausgabe

Eine erfolgreiche JSON-Antwort sieht in etwa wie folgendes Beispiel aus:

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-02-04T06:32:08.2752706+00:00",
  "lastUpdatedDateTime": "2021-02-04T06:32:08.7706172+00:00",
  "analyzeResult": {
    "version": "3.2",
    "readResults": [
      {
        "page": 1,
        "angle": 2.1243,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              58,
              42,
              314,
              59,
              311,
              123,
              56,
              121
            ],
            "text": "Tabs vs",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.96
              }
            },
            "words": [
              {
                "boundingBox": [
                  68,
                  44,
                  225,
                  59,
                  224,
                  122,
                  66,
                  123
                ],
                "text": "Tabs",
                "confidence": 0.933
              },
              {
                "boundingBox": [
                  241,
                  61,
                  314,
                  72,
                  314,
                  123,
                  239,
                  122
                ],
                "text": "vs",
                "confidence": 0.977
              }
            ]
          }
        ]
      }
    ]
  }
}
```

### <a name="handwritten-classification-for-text-lines-latin-languages-only"></a>Handschriftliche Klassifizierung für Textzeilen (nur lateinische Sprachen)

Die Antwort umfasst die Klassifizierung, ob es sich bei den einzelnen Textzeilen um einen handschriftlichen Stil handelt, sowie eine Konfidenzbewertung. Dieses Feature wird nur für lateinische Sprachen unterstützt. Das folgende Beispiel zeigt die handschriftliche Klassifizierung für den Text im Bild.

:::image type="content" source="../Images/ocr-handwriting-classification.png" alt-text="Beispiel für OCR-Handschriftklassifizierung" border="true" :::

## <a name="next-steps"></a>Nächste Schritte

Um die REST-API zu testen, wechseln Sie zur [Referenz für die Lese-API](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005).
