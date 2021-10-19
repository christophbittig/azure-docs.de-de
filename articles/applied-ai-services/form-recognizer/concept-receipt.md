---
title: Belegmodell der Formularerkennung
titleSuffix: Azure Applied AI Services
description: Konzepte, die die Datenextraktion und -analyse mithilfe des vordefinierten Belegmodells umfassen
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/05/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 48ebbb4975e57db34fe080db2a61328cef65ca80
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716448"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-receipt-model"></a>Belegmodell der Formularerkennung

Das Belegmodell kombiniert leistungsstarke OCR-Funktionen (Optical Character Recognition, optische Zeichenerkennung) mit Deep Learning-Modellen, um wichtige Informationen aus Kaufbelegen zu analysieren und zu extrahieren. Belege können verschiedene Formate und unterschiedliche Qualität aufweisen, einschließlich gedruckter und handschriftlicher Belege. Die API extrahiert Schlüsselinformationen wie Händlername, Händlertelefonnummer, Transaktionsdatum, Steuer und Transaktionssumme und gibt eine strukturierte JSON-Datendarstellung zurück.

##### <a name="sample-receipt-processed-with-form-recognizer-sample-labeling-tool"></a>Beispielbeleg verarbeitet mit dem [Beispielbeschriftungstool der Formularerkennung](https://fott-2-1.azurewebsites.net/):

:::image type="content" source="./media/overview-receipt.jpg" alt-text="Beispielbeleg" lightbox="./media/overview-receipt.jpg":::

## <a name="try-form-recognizer-studio-preview"></a>Ausprobieren von Formularerkennung Studio (Vorschau)

* Formularerkennung Studio ist mit der Vorschau-API (v3.0) verfügbar.

* Extrahieren Sie beispielsweise die Uhrzeit und das Datum von Transaktionen, Händlerinformationen oder Gesamtbeträge mit der Belegfunktion in Formularerkennung Studio:

> [!div class="nextstepaction"]
> [Ausprobieren von Formularerkennung Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=receipt)

## <a name="try-it-sample-labeling-tool"></a>Jetzt testen: Tool für die Beschriftung von Beispielen

Sie können sehen, wie Belegdaten extrahiert werden, indem Sie unser Tool für die Beschriftung von Beispielen ausprobieren. Sie benötigen Folgendes:

* Azure-Abonnement – Sie können ein [kostenloses Abonnement erstellen](https://azure.microsoft.com/free/cognitive-services/)

* Eine [Formularerkennungsinstanz](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) im Azure-Portal Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst auszuprobieren. Klicken Sie nach der Bereitstellung Ihrer Ressource auf **Zu Ressource wechseln**, um Ihren API-Schlüssel und Endpunkt abzurufen.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Screenshot: Schlüssel und Endpunktspeicherort im Azure-Portal":::

* Ein Belegdokument. Sie können unser [Beispielbelegdokument](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-receipt.png) verwenden.

> [!div class="nextstepaction"]
  > [Testen](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

Auf der Benutzeroberfläche der Formularerkennung:

  1. Wählen Sie **Verwenden eines vordefinierten Modells zum Abrufen von Daten** aus.
  1. Wählen Sie aus dem Dropdownmenü **Formulartyp** die Option **Beleg** aus:

  :::image type="content" source="media/try-receipt.png" alt-text="Screenshot: Dropdownmenü des Tools für die Beschriftung von Beispielen für das Auswahlmenü für vordefinierte Modelle":::

## <a name="input-requirements"></a>Eingabeanforderungen

* Die besten Ergebnisse erzielen Sie, wenn Sie pro Dokument ein deutliches Foto oder einen hochwertigen Scan einreichen.
* Unterstützte Dateiformate: JPEG, PNG, BMP, TIFF und PDF (in Text eingebettet oder gescannt). In Text eingebettete PDF-Dateien sind am besten geeignet, um die Möglichkeit von Fehlern beim Extrahieren und Auffinden von Zeichen auszuschließen.
* Bei PDF und TIFF können bis zu 2000 Seiten verarbeitet werden (bei einem kostenlosen Abonnement werden nur die ersten beiden Seiten verarbeitet).
* Die Dateigröße muss weniger als 50 MB betragen.
* Bei Bildern müssen die Abmessungen zwischen 50 × 50 Pixel und 10.000 × 10.000 Pixel liegen.
* Die PDF-Abmessungen sind bis zu 17 x 17 Zoll, was dem Papierformat Legal oder A3 entspricht, oder kleiner.
* Der Gesamtumfang der Trainingsdaten beträgt 500 Seiten oder weniger.
* Wenn Ihre PDFs passwortgeschützt sind, müssen Sie die Sperre vor der Einreichung aufheben.
* Für unüberwachtes Lernen (ohne bezeichnete Daten):
  * Die Daten müssen Schlüssel und Werte enthalten.
  * Die Schlüssel müssen über oder links von den Werten stehen; sie dürfen nicht unter oder rechts von ihnen stehen.

## <a name="supported-languages-and-locales-v21"></a>Unterstützte Sprachen und Gebietsschemas in Version 2.1

>[!NOTE]
 > Es ist nicht erforderlich, ein Gebietsschema anzugeben. Dies ist ein optionaler Parameter. Die Deep Learning-Technologie der Formularerkennung erkennt automatisch die Sprache des Texts in Ihrem Bild.

| Modell | Sprache – Gebietsschemacode | Standard |
|--------|:----------------------|:---------|
|Rechnung| <ul><li>Englisch (USA) – en-US</li><li> Englisch (Australien) – en-AU</li><li>Englisch (Kanada) – en-CA</li><li>Englisch (Vereinigtes Königreich) – en-GB</li><li>Englisch (Indien) – en-IN</li></ul>  | Automatisch erkannt |

## <a name="key-value-pair-extraction"></a>Schlüssel-Wert-Paarextraktion

|Name| type | BESCHREIBUNG | Standardisierte Ausgabe |
|:-----|:----|:----|:----|
| ReceiptType | Zeichenfolge | Der Typ des Belegs |  Aufgeschlüsselt |
| MerchantName | Zeichenfolge | Der Name des Händlers, der den Beleg ausstellt |  |
| MerchantPhoneNumber | phoneNumber | Die aufgeführte Telefonnummer des Händlers | +1 xxx xxx xxxx |
| MerchantAddress | Zeichenfolge | Die aufgeführte Adresse des Händlers |   |
| TransactionDate | date | Das Datum der Ausstellung des Belegs | yyyy-mm-dd |
| TransactionTime | time | Die Uhrzeit der Ausstellung des Belegs | hh-mm-ss (24 Stunden)  |
| Gesamt | Zahl (USD)| Die Gesamttransaktion des Belegs | Gleitkommazahl mit zwei Dezimalstellen|
| Subtotal (Zwischensumme) | Zahl (USD) | Die Zwischensumme des Belegs, oft vor Steuern | Gleitkommazahl mit zwei Dezimalstellen|
| Tax (Steuern) | Zahl (USD) | Die Steuern auf dem Beleg (oft Mehrwertsteuer oder Äquivalent) | Gleitkommazahl mit zwei Dezimalstellen |
| Tipp | Zahl (USD) | Vom Käufer gegebenes Trinkgeld | Gleitkommazahl mit zwei Dezimalstellen|
| Elemente | Array von Objekten | Die extrahierten Positionen mit extrahierten Werten für Name, Menge, Stückpreis und Gesamtpreis | |
| Name | Zeichenfolge | Name des Elements | |
| Menge | number | Menge der einzelnen Positionen | integer |
| Preis | number | Einzelpreis der einzelnen Positionen| Gleitkommazahl mit zwei Dezimalstellen |
| Gesamtpreis | number | Gesamtpreis der Position | Gleitkommazahl mit zwei Dezimalstellen |

## <a name="form-recognizer-preview-v30"></a>Formularerkennung Vorschau v3.0

 Die Vorschauversion der Formularerkennung bietet eine Reihe neuer Features und Funktionen:

* Das Modell **Beleg** unterstützt die Verarbeitung von einseitigen Hotelbelegen.

    ### <a name="hotel-receipt-key-value-pair-extraction"></a>Extraktion von Schlüssel-Wert-Paaren des Hotelbelegs

    |Name| type | BESCHREIBUNG | Standardisierte Ausgabe |
    |:-----|:----|:----|:----|
    | ArrivalDate | date | Ankunftsdatum | yyyy-mm-dd |
    | Währung | currency | Währungseinheit der Belegbeträge. Beispiel: USD, EUR oder MIXED, wenn mehrere Werte gefunden werden ||
    | DepartureDate | date | Abreisedatum | yyyy-mm-dd |
    | Items | array | | |
    | Items.*.Category | Zeichenfolge | Kategorie der Position, z. B. Zimmer, Steuern usw. |  |
    | Items.*.Date | date | Datum der Position | yyyy-mm-dd |
    | Items.*.Description | Zeichenfolge | Beschreibung der Position | |
    | Items.*.TotalPrice |  number | Gesamtpreis der Position | integer |
    | Gebietsschema | Zeichenfolge | Gebietsschema des Belegs, z. B. en-US. | ISO-Code im Format „Sprache-Land“   |
    | MerchantAddress | Zeichenfolge | Die aufgeführte Adresse des Händlers | |
    | MerchantAliases | array| | |
    | MerchantAliases.* | Zeichenfolge | Alternativer Name des Händlers |  |
    | MerchantName | Zeichenfolge | Der Name des Händlers, der den Beleg ausstellt | |
    | MerchantPhoneNumber | phoneNumber | Die aufgeführte Telefonnummer des Händlers | +1 xxx xxx xxxx|
    | ReceiptType | Zeichenfolge | Art des Belegs, z. B. Hotel, aufgeschlüsselt | |
    | Gesamt | number | Die Gesamttransaktion des Belegs | Gleitkommazahl mit zwei Dezimalstellen |

    ### <a name="hotel-receipt-supported-languages-and-locales"></a>Unterstützte Sprachen und Gebietsschemas für Hotelbelege

    | Modell | Sprache – Gebietsschemacode | Standard |
    |--------|:----------------------|:---------|
    |Beleg (Hotel) | <ul><li>Englisch (USA) – en-US</li></ul>| Englisch (USA) – en-US|

* Erfahren Sie, wie Sie die Vorschauversion in Ihren Anwendungen und Workflows verwenden können, indem Sie unseren [**Formularerkennung v3.0 Migrationsleitfaden**](v3-migration-guide.md) befolgen.

* Erkunden Sie unsere [**REST-API (Vorschau)** ](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument), um mehr über die Vorschauversion und neue Funktionen zu erfahren.

## <a name="next-steps"></a>Nächste Schritte

* Durcharbeiten eines Schnellstarts zur Formularerkennung:

  > [!div class="nextstepaction"]
  > [Formularerkennung: Schnellstart](quickstarts/try-sdk-rest-api.md)

* Erkunden Sie unsere REST-API:

    > [!div class="nextstepaction"]
    > [Formularerkennung-API Version 2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeReceiptAsync)
