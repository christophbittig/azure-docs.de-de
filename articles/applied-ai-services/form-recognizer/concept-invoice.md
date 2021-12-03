---
title: Rechnungsmodell der Formularerkennung
titleSuffix: Azure Applied AI Services
description: Konzepte für die Datenextraktion und -analyse mit vorgefertigten Rechnungsmodellen
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0f9c2f1603a87e30b0db32041f7d7aeff259600e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027353"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-invoice-model"></a>Rechnungsmodell der Formularerkennung

 Das Rechnungsmodell kombiniert leistungsstarke OCR-Funktionen (Optical Character Recognition, optische Zeichenerkennung) mit Deep Learning-Modellen, um wichtige Felder und Rechnungspositionen aus Verkaufsrechnungen zu analysieren und zu extrahieren.  Rechnungen können viele verschiedene Formate und Qualitätsmerkmale aufweisen, beispielsweise mit dem Handy fotografierte Bilder, gescannte Dokumente und digitale PDF-Dateien. Die API analysiert den Rechnungtext, extrahiert Schlüsselinformationen wie Kundenname, Rechnungsadresse, Fälligkeitsdatum und fälliger Betrag und gibt eine strukturierte JSON-Datendarstellung zurück.

**Beispielrechnung, die mit [Formularerkennung Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=invoice) verarbeitet wird**

:::image type="content" source="media/studio/overview-invoices.png" alt-text="Beispielrechnung" lightbox="media/overview-invoices-big.jpg":::

## <a name="development-options"></a>Entwicklungsoptionen

Die folgenden Ressourcen werden von der Azure-Formularerkennung v2.1 unterstützt:

| Funktion | Ressourcen |
|----------|-------------------------|
|**Rechnungsmodell**| <ul><li>[**Formularerkennungstool für die Bezeichnung**](https://fott-2-1.azurewebsites.net/prebuilts-analyze)</li><li>[**REST-API**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-invoices)</li><li>[**Clientbibliothek/SDK**](quickstarts/try-sdk-rest-api.md)</li><li>[**Docker-Container für Formularerkennung**](containers/form-recognizer-container-install-run.md?tabs=invoice#run-the-container-with-the-docker-compose-up-command)</li></ul>|

Die folgenden Ressourcen werden von der Azure-Formularerkennung v3.0 unterstützt:

| Funktion | Ressourcen | Modell-ID |
|----------|-------------|-----------|
|**Rechnungsmodell** | <ul><li>[**Formularerkennung Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**REST-API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li><li>[**Java SDK**](quickstarts/try-v3-java-sdk.md)</li><li>[**JavaScript SDK**](quickstarts/try-v3-javascript-sdk.md)</li></ul>|**Vordefinierte Rechnung**|

### <a name="try-form-recognizer"></a>Formularerkennung ausprobieren

Erfahren Sie, wie Daten, einschließlich Kundeninformationen, Herstellerdetails und Positionen, mithilfe von Formularerkennung Studio oder unserem Beispielbeschriftungstool aus Rechnungen extrahiert werden. Sie benötigen Folgendes:

* Azure-Abonnement – Sie können ein [kostenloses Abonnement erstellen](https://azure.microsoft.com/free/cognitive-services/)

* Eine [Formularerkennungsinstanz](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) im Azure-Portal Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst auszuprobieren. Wählen Sie nach der Bereitstellung Ihrer Ressource **Zu Ressource wechseln** aus, um Ihren API-Schlüssel und -Endpunkt abzurufen.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Screenshot: Schlüssel und Endpunktspeicherort im Azure-Portal":::

#### <a name="form-recognizer-studio-preview"></a>Formularerkennung Studio (Vorschau)

1. Wählen Sie auf der Startseite von Azure-Formularerkennung Studio die Option **Rechnungen** aus.

1. Sie können entweder die Beispielrechnung analysieren oder die Schaltfläche **+ Hinzufügen** auswählen, um Ihr eigenes Beispiel hochzuladen.

1. Wählen Sie die Schaltfläche **Analysieren** aus:

    :::image type="content" source="media/studio/invoice-analyze.png" alt-text="Screenshot: Menü „Rechnung analysieren“":::

> [!div class="nextstepaction"]
> [Ausprobieren von Formularerkennung Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=invoice)

#### <a name="sample-labeling-tool"></a>Beispiel-Beschriftungstool

Sie benötigen ein Rechnungsdokument. Sie können unser [Beispielrechnungsdokument](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf) verwenden.

1. Wählen Sie auf der Startseite des Beispiel-Beschriftungstools die Option **Vordefiniertes Modell zum Abrufen von Daten verwenden** aus.

1. Wählen Sie in der Dropdownliste **Formulartyp** die Option **Rechnung** aus:

    :::image type="content" source="media/try-invoice.png" alt-text="Screenshot: Dropdownmenü des Beispiel-Beschriftungstools für die Auswahl vordefinierter Modelle":::

    > [!div class="nextstepaction"]
    > [Ausprobieren des Beispiel-Beschriftungstools](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

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
  * Die Schlüssel müssen über oder links von den Werten stehen; sie dürfen nicht unter oder rechts von ihnen stehen.

> [!NOTE]
> Das [Beispielbeschriftungstool](https://fott-2-1.azurewebsites.net/) unterstützt das BMP-Dateiformat nicht. Dies ist eine Einschränkung des Tools, nicht des Formularerkennungsdiensts.

## <a name="supported-languages-and-locales"></a>Unterstützte Sprachen und Gebietsschemas

| Modell | Sprache – Gebietsschemacode | Standard |
|--------|:----------------------|:---------|
|Rechnung| <ul><li>Englisch (USA) – en-US</li></ul>| Englisch (USA) – en-US|

## <a name="field-extraction"></a>Feldextraktion

|Name| type | BESCHREIBUNG | Standardisierte Ausgabe |
|:-----|:----|:----|:---:|
| CustomerName | String | Fakturierter Kunde| |
| CustomerId | String | Kundenreferenz-ID | |
| PurchaseOrder | String | Referenznummer für die Bestellung | |
| InvoiceId | String | ID für diese bestimmte Rechnung (oftmals „Rechnungsnummer“) | |
| Rechnungsdatum | Date | Datum, an dem die Rechnung ausgestellt wurde | yyyy-mm-dd|
| DueDate | Date | Datum, an dem die Zahlung für diese Rechnung fällig ist | yyyy-mm-dd|
| VendorName | String | Herstellername |  |
| VendorAddress | String |  Postanschrift des Anbieters|  |
| VendorAddressRecipient | String | Der „VendorAddress“ (Anbieteranschrift) zugeordnete Name |  |
| CustomerAddress | String | Postanschrift für den Kunden | |
| CustomerAddressRecipient | String | Der „CustomerAddress“ (Kundenadresse) zugeordnete Name | |
| BillingAddress | String | Explizite Rechnungsadresse für den Kunden |  |
| BillingAddressRecipient | String | Der „BillingAddress“ (Rechnungsadresse) zugeordnete Name | |
| ShippingAddress | String | Explizite Lieferadresse für den Kunden | |
| ShippingAddressRecipient | String | Der „ShippingAdresss“ (Lieferadresse) zugeordnete Name |  |
| SubTotal | Number | In dieser Rechnung identifiziertes Feld „Subtotal“ (Zwischensumme) | Integer |
| TotalTax | Number | In dieser Rechnung identifiziertes Feld „Total tax“ (Gesamtsteuerbetrag) | Integer |
| InvoiceTotal | Betrag (USD) | Summe der dieser Rechnung zugeordneten neuen Gebühren | Integer |
| AmountDue |  Betrag (USD) | Gesamtbetrag, der an den Anbieter zu zahlen ist | Integer |
| ServiceAddress | String | Explizite Dienstadresse oder Immobilienadresse für den Kunden | |
| ServiceAddressRecipient | String | Der „ServiceAddress“ (Dienstadresse) zugeordnete Name |  |
| RemittanceAddress | String | Explizite Überweisungs- oder Zahlungsadresse für den Kunden |   |
| RemittanceAddressRecipient | String | Der „RemittanceAddress“ (Überweisungsadresse) zugeordnete Name |  |
| ServiceStartDate | Date | Anfangsdatum für den Dienstzeitraum (z. B. ein Dienstzeitraum für Hilfsprogrammrechnungen) | yyyy-mm-dd |
| ServiceEndDate | Date | Enddatum für den Dienstzeitraum (z B. ein Dienstzeitraum für Hilfsprogrammrechnungen) | yyyy-mm-dd|
| PreviousUnpaidBalance | Number | Zuvor explizit ausstehende Zahlung | Integer |

### <a name="line-items"></a>Rechnungspositionen

Im Folgenden finden Sie die aus einer Rechnung extrahierten Rechnungspositionen in der JSON-Ausgabeantwort (die folgende Ausgabe verwendet diese [Beispielrechnung](media/sample-invoice.jpg)).

|Name| type | BESCHREIBUNG | Text (Rechnungsposition 1) | Wert (standardisierte Ausgabe) |
|:-----|:----|:----|:----| :----|
| Elemente | String | Vollständige Zeichenfolgentextzeile der Rechnungsposition | 3/4/2021 A123 Consulting Services 2 hours $30.00 10% $60.00 | |
| Amount (Betrag) | Number | Der Betrag der Rechnungsposition | $60.00 | 100 |
| BESCHREIBUNG | String | Die Textbeschreibung für die Rechnungsposition der Rechnung | Beratungsdienst | Beratungsdienst |
| Menge | Number | Die Menge für die Rechnungsposition | 2 | 2 |
| UnitPrice | Number | Der Netto- oder Bruttopreis (abhängig von der Bruttorechnungseinstellung der Rechnung) einer Einheit dieses Elements | 30,00 $ | 30 |
| ProductCode | String| Produktcode, Produktnummer oder SKU der spezifischen Rechnungsposition | A123 | |
| Einheit | String| Die Einheit der Rechnungsposition, z. B. kg, lb usw. | Stunden | |
| Date | Date| Hierbei handelt es sich um das Datum für jede entsprechende Rechnungsposition. In den meisten Fällen handelt es sich dabei um das Datum, an dem eine Rechnungsposition versandt wurde. | 3/4/2021| 2021-03-04 |
| Tax (Steuern) | Number | Hierbei handelt es sich um die Steuern für jede Rechnungsposition. Zu den möglichen Werten gehören der Steuerbetrag, die Steuer in Prozent und „tax Y/N“ (Steuer Ja/Nein). | 10 % | |

Die extrahierten Schlüssel-Wert-Paare der Rechnung und die Rechnungspositionen befinden sich im Abschnitt `documentResults` der JSON-Ausgabe. 

## <a name="form-recognizer-preview-v30"></a>Formularerkennung Vorschau v3.0

 Die Vorschauversion der Formularerkennung bietet eine Reihe neuer Features und Möglichkeiten.

* Erfahren Sie, wie Sie die Vorschauversion in Ihren Anwendungen und Workflows verwenden können, indem Sie unseren [**Formularerkennung v3.0-Migrationsleitfaden**](v3-migration-guide.md) befolgen.

* Erkunden Sie die [**REST-API (Vorschau)**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument), um mehr über die Vorschauversion und neue Funktionen zu erfahren.

## <a name="next-steps"></a>Nächste Schritte

* Sehen Sie sich eine Schnellstartanleitung zur Formularerkennung an:

  > [!div class="nextstepaction"]
  > [Formularerkennung: Schnellstart](quickstarts/try-sdk-rest-api.md)

* Erkunden Sie unsere REST-API:

    > [!div class="nextstepaction"]
    > [Formularerkennung-API Version 2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)
