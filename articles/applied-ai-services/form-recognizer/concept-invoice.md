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
ms.openlocfilehash: 458e6d9b9de91dcf9f9214f2021fb780841ff7b4
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130164348"
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

* Azure-Abonnement – Sie können ein [kostenloses Abonnement erstellen](https://azure.microsoft.com/free/cognitive-services/).

* Eine [Instanz der Formularerkennung](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) im Azure-Portal. Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst auszuprobieren. Klicken Sie nach der Bereitstellung Ihrer Ressource auf **Zu Ressource wechseln**, um Ihren API-Schlüssel und -Endpunkt abzurufen.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Screenshot: Schlüssel und Endpunktspeicherort im Azure-Portal":::

* Ein Rechnungsdokument. Sie können unser [Beispielrechnungsdokument](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf) verwenden.

> [!div class="nextstepaction"]
  > [Testen](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

  Gehen Sie auf der Benutzeroberfläche der Formularerkennung folgendermaßen vor:

  1. Wählen Sie **Verwenden eines vordefinierten Modells zum Abrufen von Daten** aus.
  1. Wählen Sie in der Dropdownliste **Formulartyp** die Option **Rechnung** aus:

  :::image type="content" source="media/try-invoice.png" alt-text="Screenshot: Dropdownmenü des Tools für die Beschriftung von Beispielen für das Auswahlmenü für vordefinierte Modelle":::

## <a name="input-requirements"></a>Eingabeanforderungen

* Die besten Ergebnisse erzielen Sie, wenn Sie pro Dokument ein deutliches Foto oder einen hochwertigen Scan bereitstellen.
* Unterstützte Dateiformate: JPEG, PNG, BMP, TIFF und PDF (in Text eingebettet oder gescannt). In Text eingebettete PDF-Dateien sind am besten geeignet, um die Möglichkeit von Fehlern beim Extrahieren und Auffinden von Zeichen auszuschließen.
* In den Formaten PDF und TIFF können bis zu 2.000 Seiten verarbeitet werden (bei einem kostenlosen Abonnement werden nur die ersten beiden Seiten verarbeitet).
* Die Dateigröße muss unter 50 MB liegen.
* Bei Bildern müssen die Abmessungen zwischen 50 × 50 Pixel und 10.000 × 10.000 Pixel liegen.
* Die PDF-Abmessungen sind bis zu 17 × 17 Zoll, sodass die Papierformate Legal oder A3 hineinpassen, oder kleiner.
* Der Gesamtumfang der Trainingsdaten beträgt 500 Seiten oder weniger.
* Wenn Ihre PDFs kennwortgeschützt sind, müssen Sie die Sperre vor dem Senden entfernen.
* Für unbeaufsichtigtes Lernen (ohne beschriftete Daten) gilt Folgendes:
  * Die Daten müssen Schlüssel und Werte enthalten.
  * Die Schlüssel müssen über oder links von den Werten stehen; sie dürfen nicht darunter oder rechts davon stehen.

> [!NOTE]
> Das [Tool für die Beschriftung von Beispielen](https://fott-2-1.azurewebsites.net/) unterstützt das BMP-Dateiformat nicht. Dies ist eine Einschränkung des Tools, nicht des Formularerkennungsdiensts.

## <a name="supported-languages-and-locales"></a>Unterstützte Sprachen und Gebietsschemas

| Modell | Sprache – Gebietsschemacode | Standard |
|--------|:----------------------|:---------|
|Rechnung| <ul><li>Englisch (USA) – en-US</li></ul>| Englisch (USA) – en-US|

## <a name="key-value-pair-extraction"></a>Schlüssel-Wert-Paar-Extraktion

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

### <a name="line-items"></a>Rechnungspositionen

Im Folgenden finden Sie die aus einer Rechnung extrahierten Rechnungspositionen in der JSON-Ausgabeantwort (die folgende Ausgabe verwendet diese [Beispielrechnung](media/sample-invoice.jpg)).

|Name| type | BESCHREIBUNG | Text (Rechnungsposition 1) | Wert (standardisierte Ausgabe) |
|:-----|:----|:----|:----| :----|
| Elemente | Zeichenfolge | Vollständige Zeichenfolgentextzeile der Rechnungsposition | 3/4/2021 A123 Consulting Services 2 hours $30.00 10% $60.00 | |
| Amount (Betrag) | number | Der Betrag der Rechnungsposition | $60.00 | 100 |
| BESCHREIBUNG | Zeichenfolge | Die Textbeschreibung für die Rechnungsposition der Rechnung | Beratungsdienst | Beratungsdienst |
| Menge | number | Die Menge für die Rechnungsposition | 2 | 2 |
| UnitPrice | number | Der Netto- oder Bruttopreis (abhängig von der Bruttorechnungseinstellung der Rechnung) einer Einheit dieses Elements | 30,00 $ | 30 |
| ProductCode | Zeichenfolge| Produktcode, Produktnummer oder SKU der spezifischen Rechnungsposition | A123 | |
| Einheit | Zeichenfolge| Die Einheit der Rechnungsposition, z. B. kg, lb usw. | Stunden | |
| Datum | Datum| Hierbei handelt es sich um das Datum für jede entsprechende Rechnungsposition. In den meisten Fällen handelt es sich dabei um das Datum, an dem eine Rechnungsposition versandt wurde. | 3/4/2021| 2021-03-04 |
| Tax (Steuern) | number | Hierbei handelt es sich um die Steuern für jede Rechnungsposition. Zu den möglichen Werten gehören der Steuerbetrag, die Steuer in Prozent und „tax Y/N“ (Steuer Ja/Nein). | 10 % | |

Die extrahierten Schlüssel-Wert-Paare der Rechnung und die Rechnungspositionen befinden sich im Abschnitt `documentResults` der JSON-Ausgabe. 

## <a name="form-recognizer-preview-v30"></a>Formularerkennung Vorschau v3.0

 Die Vorschauversion der Formularerkennung bietet eine Reihe neuer Features und Möglichkeiten.

* Erfahren Sie, wie Sie die Vorschauversion in Ihren Anwendungen und Workflows verwenden können, indem Sie unseren [**Formularerkennung v3.0 Migrationsleitfaden**](v3-migration-guide.md) befolgen.

* Erkunden Sie die [**REST-API (Vorschau)**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument), um mehr über die Vorschauversion und neue Funktionen zu erfahren.

## <a name="next-steps"></a>Nächste Schritte

* Sehen Sie sich eine Schnellstartanleitung zur Formularerkennung an:

  > [!div class="nextstepaction"]
  > [Formularerkennung: Schnellstart](quickstarts/try-sdk-rest-api.md)

* Erkunden Sie unsere REST-API:

    > [!div class="nextstepaction"]
    > [Formularerkennung-API Version 2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)
