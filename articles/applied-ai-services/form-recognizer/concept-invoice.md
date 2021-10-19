---
title: Rechnungsmodell der Formularerkennung
titleSuffix: Azure Applied AI Services
description: Konzepte für die Datenextraktion und -analyse mit vorgefertigten Rechnungsmodellen
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 4b660d464a8615886be9b466fd2e9de808ef3bd9
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716457"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-invoice-model"></a>Rechnungsmodell der Formularerkennung

 Das Rechnungsmodell kombiniert leistungsstarke OCR-Funktionen (Optical Character Recognition, optische Zeichenerkennung) mit Deep Learning-Modellen, um wichtige Felder und Rechnungspositionen aus Verkaufsrechnungen zu analysieren und zu extrahieren.  Rechnungen können viele verschiedene Formate und Qualitätsmerkmale aufweisen, beispielsweise mit dem Handy fotografierte Bilder, gescannte Dokumente und digitale PDF-Dateien. Die API analysiert den Rechnungtext, extrahiert Schlüsselinformationen wie Kundenname, Rechnungsadresse, Fälligkeitsdatum und fälliger Betrag und gibt eine strukturierte JSON-Datendarstellung zurück.

##### <a name="sample-invoice-processed-with-form-recognizer-sample-labeling-tool"></a>Beispielrechnung verarbeitet mit [Formularerkennung und Beschriftungstool](https://fott-2-1.azurewebsites.net/):

:::image type="content" source="media/overview-invoices.jpg" alt-text="Beispielrechnung" lightbox="media/overview-invoices-big.jpg":::

## <a name="try-form-recognizer-studio-preview"></a>Ausprobieren von Formularerkennung Studio (Vorschau)

* Formularerkennung Studio ist mit der Vorschau-API (v3.0) verfügbar.

* Extrahieren Sie Kunden- und Herstellerdetails, Rechnungspositionen und vieles mehr mit unserem Rechnungsfeature von Formularerkennung Studio:

> [!div class="nextstepaction"]
> [Ausprobieren von Formularerkennung Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=invoice)

## <a name="try-it-sample-labeling-tool"></a>Jetzt testen: Tool für die Beschriftung von Beispielen

Sie können sehen, wie Rechnungsdaten extrahiert werden, indem Sie unser Tool für die Beschriftung von Beispielen ausprobieren. Sie benötigen Folgendes:

* Azure-Abonnement – Sie können ein [kostenloses Abonnement erstellen](https://azure.microsoft.com/free/cognitive-services/)

* Eine [Formularerkennungsinstanz](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) im Azure-Portal Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst auszuprobieren. Klicken Sie nach der Bereitstellung Ihrer Ressource auf **Zu Ressource wechseln**, um Ihren API-Schlüssel und Endpunkt abzurufen.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Screenshot: Schlüssel und Endpunktspeicherort im Azure-Portal":::

* Ein Rechnungsdokument. Sie können unser [Beispielrechnungsdokument](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf) verwenden.

> [!div class="nextstepaction"]
  > [Testen](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

  Auf der Benutzeroberfläche der Formularerkennung:

  1. Wählen Sie **Verwenden eines vordefinierten Modells zum Abrufen von Daten** aus.
  1. Wählen Sie in der Dropdownliste **Formulartyp** die Option **Rechnung** aus:

  :::image type="content" source="media/try-invoice.png" alt-text="Screenshot: Dropdownmenü des Tools für die Beschriftung von Beispielen für das Auswahlmenü für vordefinierte Modelle":::

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

> [!NOTE]
> Das [Tool für die Beschriftung von Beispielen](https://fott-2-1.azurewebsites.net/) unterstützt nicht das BMP-Dateiformat. Dies ist eine Einschränkung des Tools, nicht des Formularerkennungsdiensts.

## <a name="supported-languages-and-locales"></a>Unterstützte Sprachen und Gebietsschemas

| Modell | Sprache – Gebietsschemacode | Standard |
|--------|:----------------------|:---------|
|Rechnung| <ul><li>Englisch (USA) – en-US</li></ul>| Englisch (USA) – en-US|

## <a name="key-value-pair-extraction"></a>Schlüssel-Wert-Paarextraktion

|Name| type | BESCHREIBUNG | Standardisierte Ausgabe |
|:-----|:----|:----|:---:|
| CustomerName | Zeichenfolge | Fakturierter Kunde| |
| CustomerId | Zeichenfolge | Kundenreferenz-ID | |
| PurchaseOrder | Zeichenfolge | Referenznummer für die Bestellung | |
| InvoiceId | Zeichenfolge | ID für diese bestimmte Rechnung (oftmals „Rechnungsnummer“) | |
| Rechnungsdatum | date | Datum, an dem die Rechnung ausgestellt wurde | yyyy-mm-dd|
| DueDate | date | Datum, an dem die Zahlung für diese Rechnung fällig ist | yyyy-mm-dd|
| VendorName | Zeichenfolge | Herstellername |  |
| VendorAddress | Zeichenfolge |  Postanschrift des Anbieters|  |
| VendorAddressRecipient | Zeichenfolge | Der „VendorAddress“ (Anbieteranschrift) zugeordnete Name |  |
| CustomerAddress | Zeichenfolge | Postanschrift für den Kunden | |
| CustomerAddressRecipient | Zeichenfolge | Der „CustomerAddress“ (Kundenadresse) zugeordnete Name | |
| BillingAddress | Zeichenfolge | Explizite Rechnungsadresse für den Kunden |  |
| BillingAddressRecipient | Zeichenfolge | Der „BillingAddress“ (Rechnungsadresse) zugeordnete Name | |
| ShippingAddress | Zeichenfolge | Explizite Lieferadresse für den Kunden | |
| ShippingAddressRecipient | Zeichenfolge | Der „ShippingAdresss“ (Lieferadresse) zugeordnete Name |  |
| SubTotal | Anzahl | In dieser Rechnung identifiziertes Feld „Subtotal“ (Zwischensumme) | integer |
| TotalTax | Anzahl | In dieser Rechnung identifiziertes Feld „Total tax“ (Gesamtsteuerbetrag) | integer |
| InvoiceTotal | Zahl (USD) | Summe der dieser Rechnung zugeordneten neuen Gebühren | integer |
| AmountDue |  Zahl (USD) | Gesamtbetrag, der an den Anbieter zu zahlen ist | integer |
| ServiceAddress | Zeichenfolge | Explizite Dienstadresse oder Immobilienadresse für den Kunden | |
| ServiceAddressRecipient | Zeichenfolge | Der „ServiceAddress“ (Dienstadresse) zugeordnete Name |  |
| RemittanceAddress | Zeichenfolge | Explizite Überweisungs- oder Zahlungsadresse für den Kunden |   |
| RemittanceAddressRecipient | Zeichenfolge | Der „RemittanceAddress“ (Überweisungsadresse) zugeordnete Name |  |
| ServiceStartDate | date | Anfangsdatum für den Dienstzeitraum (z. B. ein Dienstzeitraum für Hilfsprogrammrechnungen) | yyyy-mm-dd |
| ServiceEndDate | date | Enddatum für den Dienstzeitraum (z B. ein Dienstzeitraum für Hilfsprogrammrechnungen) | yyyy-mm-dd|
| PreviousUnpaidBalance | Anzahl | Zuvor explizit ausstehende Zahlung | integer |

## <a name="form-recognizer-preview-v30"></a>Formularerkennung Vorschau v3.0

 Die Vorschauversion der Formularerkennung bietet eine Reihe neuer Features und Möglichkeiten.

* Erfahren Sie, wie Sie die Vorschauversion in Ihren Anwendungen und Workflows verwenden können, indem Sie unseren [**Formularerkennung v3.0 Migrationsleitfaden**](v3-migration-guide.md) befolgen.

* Erkunden Sie unsere [**REST-API (Vorschau)** ](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument), um mehr über die Vorschauversion und neue Funktionen zu erfahren.

## <a name="next-steps"></a>Nächste Schritte

* Durcharbeiten eines Schnellstarts zur Formularerkennung:

  > [!div class="nextstepaction"]
  > [Formularerkennung: Schnellstart](quickstarts/try-sdk-rest-api.md)

* Erkunden Sie unsere REST-API:

    > [!div class="nextstepaction"]
    > [Formularerkennung-API Version 2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)
