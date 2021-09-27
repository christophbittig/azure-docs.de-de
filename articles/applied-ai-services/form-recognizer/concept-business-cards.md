---
title: Visitenkarten – Formularerkennung
titleSuffix: Azure Applied AI Services
description: Lernen Sie die Konzepte im Zusammenhang mit der Visitenkartenanalyse mit der Formularerkennungs-API (Verwendung und Einschränkungen) kennen.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/09/2021
ms.author: lajanuar
ms.openlocfilehash: 779c65487fd34a94426c2f9dc8a6ffaad36d2313
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676521"
---
# <a name="form-recognizer-prebuilt-business-cards-model"></a>Vordefiniertes Visitenkartenmodell der Formularerkennung

Mit der Azure-Formularerkennung können Kontaktinformationen aus Visitenkarten mithilfe des vordefinierten Modells für Visitenkarten analysiert und extrahiert werden. Hierbei werden die leistungsstarken Funktionen der optischen Zeichenerkennung (Optical Character Recognition, OCR) mit unserem Modell zur Visitenkartenerfassung kombiniert, um die wichtigsten Informationen auf Visitenkarten zu extrahieren (in englischer Sprache). Dabei werden persönliche Kontaktinformationen, der Firmenname, die Position und weitere Details extrahiert. Die vordefinierte Visitenkarten-API ist in der Formularerkennung (v2.1) öffentlich verfügbar.

## <a name="customer-scenarios"></a>Kundenszenarien

Die Daten, die mit der Visitenkarten-API extrahiert werden, können für eine Vielzahl von Aufgaben eingesetzt werden. Durch das Extrahieren dieser Kontaktinformationen wird der Zeitaufwand für Benutzer mit Kundenkontakt deutlich reduziert. Im Folgenden finden Sie einige Beispiele dafür, was unsere Kunden mit der Visitenkarten-API erreicht haben:

* Extrahieren der Kontaktinformationen von Visitenkarten und schnelles Erstellen von Telefonkontakten.
* Integration in CRM, um Kontakte automatisch anhand von Visitenkartenbildern zu erstellen.
* Verfolgen von Vertriebsleads.
* Extrahieren von Kontaktinformationen von vorhandenen Visitenkartenbildern als Massenvorgang.

Die Visitenkarten-API unterstützt zudem die [AI Builder-Funktion zur Verarbeitung von Visitenkarten](/ai-builder/prebuilt-business-card).

## <a name="try-it"></a>Ausprobieren

Wenn Sie den Belegdienst „Formularerkennung“ ausprobieren möchten, wechseln Sie zum Onlinetool für die Beispielbenutzeroberfläche:

> [!div class="nextstepaction"]
> [Visitenkartenmodell testen](https://aka.ms/fott-2.1-ga "Mit einem vordefinierten Modell beginnen, um Daten aus einer Visitenkarte zu extrahieren")

## <a name="what-does-the-business-card-service-do"></a>Wozu wird der Dienst für Visitenkarten eingesetzt?

Die vorgefertigte Visitenkarten-API extrahiert wichtige Felder von Visitenkarten und gibt diese Informationen in einer organisierten JSON-Antwort zurück.

![Aufgeschlüsselte Contoso-Abbildung aus dem Tool für die Beschriftung von Beispielen mit JSON-Ausgabe](./media/business-card-example.jpg)

### <a name="fields-extracted"></a>Extrahierte Felder:

|Name| type | BESCHREIBUNG | Text | Wert (standardisierte Ausgabe) |
|:-----|:----|:----|:----|:----|
| ContactNames | Array von Objekten | Aus der Visitenkarte extrahierter Name des Kontakts | [{ "FirstName": "Chris", "LastName": "Smith" }] | |
| FirstName | Zeichenfolge | Vorname des Kontakts | "Chris" | "Chris" |
| LastName | Zeichenfolge | Nachname des Kontakts |     „Smith“ | „Smith“ |
| CompanyNames | array of strings | Aus der Visitenkarte extrahierter Unternehmensname | ["CONTOSO"] | CONTOSO |
| Departments | array of strings | Abteilung oder Organisation des Kontakts | ["Cloud & Al Department"] | Cloud & Al Department |
| JobTitles | array of strings | Aufgeführte Position des Kontakts | ["Senior Researcher"] | Senior Researcher |
| E-Mails | array of strings | Aus der Visitenkarte extrahierte E-Mail-Adresse des Kontakts | ["chris.smith@contoso.com"] | chris.smith@contoso.com |
| Websites | array of strings | Aus der Visitenkarte extrahierte Website | ["https://www.contoso.com"] | https://www.contoso.com |
| Adressen | array of strings | Aus der Visitenkarte extrahierte Adresse | ["4001 1st Ave NE Redmond, WA 98052"] | 4001 1st Ave NE Redmond, WA 98052 |
| MobilePhones | Array aus Telefonnummern | Aus der Visitenkarte extrahierte Mobiltelefonnummer | ["+1 (987) 123-4567"] | +19871234567 |
| Faxnummern | Array aus Telefonnummern | Aus der Visitenkarte extrahierte Faxnummer | ["+1 (987) 312-6745"] | +19873126745 |
| WorkPhones | Array aus Telefonnummern | Aus der Visitenkarte extrahierte geschäftliche Telefonnummer | ["+1 (987) 213-5674"] | +19872135674 | 
| OtherPhones     | Array aus Telefonnummern | Aus der Visitenkarte extrahierte weitere Telefonnummern | ["+1 (987) 213-5673"] | +19872135673 |

Darüber hinaus kann die Visitenkarten-API auch den gesamten erkannten Text auf der Visitenkarte zurückgeben. Diese OCR-Ausgabe ist in der JSON-Antwort enthalten.

### <a name="input-requirements"></a>Eingabeanforderungen

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>Unterstützte Gebietsschemas

**Vordefinierte Visitenkarten v2.1** unterstützen die folgenden Gebietsschemas: **en-us**, **en-au**, **en-ca**, **en-gb**, **en-in**

## <a name="analyze-business-card"></a>Analysieren von Visitenkarten

Beim Vorgang zur [Analyse der Visitenkarte](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeBusinessCardAsync) wird ein Bild oder eine PDF-Version einer Visitenkarte als Eingabe verwendet, um die relevanten Werte zu extrahieren. Bei diesem Aufruf wird ein Antwortheaderfeld namens `Operation-Location` zurückgegeben. Der `Operation-Location`-Wert ist eine URL, die die Ergebnis-ID enthält, die im nächsten Schritt verwendet werden soll.

|Antwortheader| Ergebnis-URL |
|:-----|:----|
|Operation-Location | `https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeBusinessCardAsync` |

## <a name="get-analyze-business-card-result"></a>Get Analyze Business Card Result (Ergebnis von Visitenkartenanalyse abrufen)

Im zweiten Schritt wird der Vorgang zum [Abrufen des Ergebnisses der Visitenkartenanalyse](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetAnalyzeBusinessCardResult) aufgerufen. Bei diesem Vorgang wird die Ergebnis-ID als Eingabe verwendet, die durch den Vorgang zur Analyse der Visitenkarte erstellt wurde. Er gibt eine JSON-Antwort zurück, die ein **Status**-Feld mit den folgenden möglichen Werten enthält. Sie rufen diesen Vorgang iterativ auf, bis er mit dem Wert **succeeded** (erfolgreich) zurückgegeben wird. Verwenden Sie ein Intervall von 3 bis 5 Sekunden, um zu vermeiden, dass die Rate der Anforderungen pro Sekunde (RPS) überschritten wird.

|Feld| type | Mögliche Werte |
|:-----|:----:|:----|
|status | Zeichenfolge | notStarted: Der Analysevorgang wurde noch nicht gestartet.<br /><br />running: Der Analysevorgang wird ausgeführt.<br /><br />failed: Beim Analysevorgang ist ein Fehler aufgetreten.<br /><br />succeeded: Der Analysevorgang war erfolgreich.|

Wenn im Feld **status** der Wert **succeeded** angezeigt wird, enthält die JSON-Antwort die Ergebnisse der Visitenkartenanalyse und optional die der Texterkennung, sofern diese angefordert wurde. Das Ergebnis der Visitenkartenerfassung ist als Wörterbuch benannter Feldwerte organisiert, wobei jeder Wert den extrahierten Text, den normalisierten Wert, den Begrenzungsrahmen, die Konfidenz und die entsprechenden Wortelemente enthält. Das Ergebnis der Texterkennung ist als eine Hierarchie von Zeilen und Wörtern mit Text, Begrenzungsrahmen und Informationen zum Vertrauensgrad organisiert.

![Beispielausgabe für eine Visitenkarte](./media/business-card-results.png)

### <a name="sample-json-output"></a>JSON-Beispielausgabe

Die Antwort auf den Vorgang zum Abrufen des Ergebnisses der Visitenkartenanalyse ist die strukturierte Darstellung der Visitenkarte mit allen extrahierten Informationen.  Hier finden Sie eine [Beispieldatei für eine Visitenkarte](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-english.jpg) und deren strukturierte Ausgabe ([Beispiel für eine Visitenkartenausgabe](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-result.json)).

Sehen Sie sich das folgende Beispiel für eine erfolgreiche JSON-Antwort an (die Ausgabe wurde der Einfachheit halber gekürzt):
* Der Knoten `"readResults"` enthält den gesamten erkannten Text. Der Text ist nach Seite, dann nach Zeile und dann nach einzelnen Wörtern sortiert.
* Der Knoten `"documentResults"` enthält die visitenkartenspezifischen Werte, die vom Modell erkannt wurden. Er enthält nützliche Kontaktinformationen wie Vorname, Nachname, Firmenname usw.

```json
{
    "status": "succeeded",
    "createdDateTime": "2021-05-27T02:18:35Z",
    "lastUpdatedDateTime": "2021-05-27T02:18:37Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": 0.0255,
                "width": 2592,
                "height": 4608,
                "unit": "pixel",
                "lines": [
                    {
                        "text": "CONTOSO",
                        "boundingBox": [
                            533,
                            1570,
                            1334,
                            1570,
                            1333,
                            1721,
                            533,
                            1720
                        ],
                        "words": [
                            {
                                "text": "CONTOSO",
                                "boundingBox": [
                                    535,
                                    1571,
                                    1278,
                                    1571,
                                    1279,
                                    1722,
                                    534,
                                    1719
                                ],
                                "confidence": 0.994
                            }
                        ],
                        "appearance": {
                            "style": {
                                "name": "other",
                                "confidence": 0.878
                            }
                        }
                    },
                    ...
                ]
            }
        ],   
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "Addresses": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "4001 1st Ave NE Redmond, WA 98052",
                                "text": "4001 1st Ave NE Redmond, WA 98052",
                                "boundingBox": [
                                    400,
                                    2789,
                                    1514,
                                    2789,
                                    1514,
                                    2857,
                                    400,
                                    2857
                                ],
                                "page": 1,
                                "confidence": 0.986,
                                "elements": [
                                    "#/readResults/0/lines/9/words/0",
                                    "#/readResults/0/lines/9/words/1",
                                    "#/readResults/0/lines/9/words/2",
                                    "#/readResults/0/lines/9/words/3",
                                    "#/readResults/0/lines/9/words/4",
                                    "#/readResults/0/lines/9/words/5",
                                    "#/readResults/0/lines/9/words/6"
                                ]
                            }
                        ]
                    },
                    "CompanyNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "CONTOSO",
                                "text": "CONTOSO",
                                "boundingBox": [
                                    535,
                                    1571,
                                    1278,
                                    1571,
                                    1279,
                                    1722,
                                    534,
                                    1719
                                ],
                                "page": 1,
                                "confidence": 0.985,
                                "elements": [
                                    "#/readResults/0/lines/0/words/0"
                                ]
                            }
                        ]
                    },
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Chris",
                                        "text": "Chris",
                                        "boundingBox": [
                                            1556,
                                            2018,
                                            1915,
                                            2021,
                                            1915,
                                            2156,
                                            1558,
                                            2154
                                        ],
                                        "page": 1,
                                        "elements": [
                                            "#/readResults/0/lines/1/words/0"
                                        ]
                                    },
                                    "LastName": {
                                        "type": "string",
                                        "valueString": "Smith",
                                        "text": "Smith",
                                        "boundingBox": [
                                            1944,
                                            2021,
                                            2368,
                                            2016,
                                            2364,
                                            2156,
                                            1944,
                                            2156
                                        ],
                                        "page": 1,
                                        "elements": [
                                            "#/readResults/0/lines/1/words/1"
                                        ]
                                    }
                                },
                                "text": "Chris Smith",
                                "boundingBox": [
                                    1556.1,
                                    2010.3,
                                    2368,
                                    2016,
                                    2367,
                                    2159.6,
                                    1555.1,
                                    2154
                                ],
                                "page": 1,
                                "confidence": 0.99,
                                "elements": [
                                    "#/readResults/0/lines/1/words/0",
                                    "#/readResults/0/lines/1/words/1"
                                ]
                            }
                        ]
                    },
                    "Departments": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Cloud & Al Department",
                                "text": "Cloud & Al Department",
                                "boundingBox": [
                                    1578,
                                    2288.8,
                                    2277,
                                    2295.1,
                                    2276.3,
                                    2367.8,
                                    1577.3,
                                    2361.5
                                ],
                                "page": 1,
                                "confidence": 0.989,
                                "elements": [
                                    "#/readResults/0/lines/3/words/0",
                                    "#/readResults/0/lines/3/words/1",
                                    "#/readResults/0/lines/3/words/2",
                                    "#/readResults/0/lines/3/words/3"
                                ]
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "chris.smith@contoso.com",
                                "text": "chris.smith@contoso.com",
                                "boundingBox": [
                                    1583,
                                    2381,
                                    2309,
                                    2382,
                                    2308,
                                    2445,
                                    1584,
                                    2447
                                ],
                                "page": 1,
                                "confidence": 0.99,
                                "elements": [
                                    "#/readResults/0/lines/4/words/0"
                                ]
                            }
                        ]
                    },
                    "Faxes": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "valuePhoneNumber": "+19873126745",
                                "text": "+1 (987) 312-6745",
                                "boundingBox": [
                                    740,
                                    2703.8,
                                    1273,
                                    2702.1,
                                    1273.2,
                                    2774.1,
                                    740.2,
                                    2775.8
                                ],
                                "page": 1,
                                "confidence": 0.99,
                                "elements": [
                                    "#/readResults/0/lines/8/words/1",
                                    "#/readResults/0/lines/8/words/2",
                                    "#/readResults/0/lines/8/words/3"
                                ]
                            }
                        ]
                    },
                    "JobTitles": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Senior Researcher",
                                "text": "Senior Researcher",
                                "boundingBox": [
                                    1578,
                                    2206,
                                    2117,
                                    2207.6,
                                    2116.8,
                                    2272.6,
                                    1577.8,
                                    2271
                                ],
                                "page": 1,
                                "confidence": 0.99,
                                "elements": [
                                    "#/readResults/0/lines/2/words/0",
                                    "#/readResults/0/lines/2/words/1"
                                ]
                            }
                        ]
                    },
                    "MobilePhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "valuePhoneNumber": "+19871234567",
                                "text": "+1 (987) 123-4567",
                                "boundingBox": [
                                    744,
                                    2529,
                                    1281,
                                    2529,
                                    1281,
                                    2603,
                                    744,
                                    2603
                                ],
                                "page": 1,
                                "confidence": 0.99,
                                "elements": [
                                    "#/readResults/0/lines/5/words/1",
                                    "#/readResults/0/lines/5/words/2",
                                    "#/readResults/0/lines/5/words/3"
                                ]
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    1576,
                                    2462,
                                    2383,
                                    2462,
                                    2380,
                                    2535,
                                    1576,
                                    2535
                                ],
                                "page": 1,
                                "confidence": 0.99,
                                "elements": [
                                    "#/readResults/0/lines/6/words/0"
                                ]
                            }
                        ]
                    },
                    "WorkPhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "valuePhoneNumber": "+19872135674",
                                "text": "+1 (987) 213-5674",
                                "boundingBox": [
                                    736,
                                    2617.6,
                                    1267.1,
                                    2618.5,
                                    1267,
                                    2687.5,
                                    735.9,
                                    2686.6
                                ],
                                "page": 1,
                                "confidence": 0.984,
                                "elements": [
                                    "#/readResults/0/lines/7/words/1",
                                    "#/readResults/0/lines/7/words/2",
                                    "#/readResults/0/lines/7/words/3"
                                ]
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

* Testen Sie eigene Visitenkarten und Beispiele in der [Beispielbenutzeroberfläche für die Formularerkennung](https://fott-preview.azurewebsites.net/).
* Sehen Sie sich die Informationen unter [Schnellstart: Verwenden der Clientbibliothek für die Formularerkennung](quickstarts/client-library.md) an, um damit zu beginnen, eine Verarbeitungs-App für Visitenkarten mit Formularerkennung in einer Entwicklungssprache Ihrer Wahl zu schreiben.
