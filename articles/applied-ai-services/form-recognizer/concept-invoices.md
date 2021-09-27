---
title: Rechnungen –-Formularerkennung
titleSuffix: Azure Applied AI Services
description: Lernen Sie die Konzepte im Zusammenhang mit Rechnungsanalyse mit der Formularerkennungs-API (Verwendung und Einschränkungen) kennen.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/09/2021
ms.author: lajanuar
ms.openlocfilehash: 92f20d9275aad08a47e20202e1daab621d1a4578
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646985"
---
# <a name="form-recognizer-prebuilt-invoice-model"></a>Vordefiniertes Rechnungsmodell für die Formularerkennung

Die Azure-Formularerkennung kann Informationen aus Verkaufsrechnungen mithilfe seiner vordefinierten Rechnungsmodelle analysieren und extrahieren. Mit der Rechnungs-API können Kunden Rechnungen in verschiedenen Formaten verwenden und strukturierte Daten zurückgeben, um die Rechnungsverarbeitung zu automatisieren. Sie kombiniert unsere leistungsstarken Funktionen zur [optischen Zeichenerkennung (Optical Character Recognition, OCR)](../../cognitive-services/computer-vision/overview-ocr.md) mit Deep Learning-Modellen zum Rechnungsverständnis, um wichtige Informationen aus Rechnungen in englischer Sprache zu extrahieren. Sie extrahiert den Text, Tabellen und Informationen wie Kunde, Anbieter, Rechnungs-ID, Fälligkeitsdatum für die Rechnung, Summe, fälliger Betrag, Steuerbetrag, Lieferadresse, Rechnungsadresse, Rechnungspositionen und mehr. Die vordefinierte Rechnungs-API ist in der Formularerkennung (v2.1) öffentlich verfügbar.

## <a name="what-does-the-invoice-service-do"></a>Welche Aufgaben führt der Rechnungsdienst aus?

Die Rechnungs-API extrahiert Schlüsselfelder und Rechnungspositionen aus Rechnungen und gibt sie in einer organisierten strukturierten JSON-Antwort zurück. Rechnungen können viele verschiedene Formate und Qualitätsmerkmale haben, beispielsweise mit dem Handy fotografierte Bilder, gescannte Dokumente und digitale PDF-Dateien. Die Rechnungs-API extrahiert die strukturierte Ausgabe aus allen diesen Rechnungen.

![Beispiel für eine Contoso-Rechnung](./media/invoice-example-new.jpg)

## <a name="try-it&quot;></a>Ausprobieren

Wenn Sie den Rechnungsdienst „Formularerkennung“ ausprobieren möchten, wechseln Sie zum Onlinetool für die Beispielbenutzeroberfläche:

> [!div class=&quot;nextstepaction&quot;]
> [Testen des Rechnungsmodells](https://aka.ms/fott-2.1-ga &quot;Beginnen Sie mit einem vordefinierten Modell, um Daten aus Rechnungen zu extrahieren.")

Zum Ausprobieren des Rechnungsdiensts „Formularerkennung“ benötigen Sie ein Azure-Abonnement ([kann hier kostenlos erstellt werden](https://azure.microsoft.com/free/cognitive-services)), einen Endpunkt für eine [Formularerkennungsressource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) und einen entsprechenden Schlüssel.

:::image type="content" source="media/analyze-invoice-new.png" alt-text="Beispiel für eine analysierte Rechnung" lightbox="media/analyze-invoice-new.png":::

### <a name="input-requirements"></a>Eingabeanforderungen

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>Unterstützte Gebietsschemas

Die **vorgefertigte Rechnung v2.1** unterstützt Rechnungen im Gebietsschema **en-us**.

## <a name="analyze-invoice"></a>Analysieren von Rechnungen

Der Vorgang [Analysieren der Rechnung](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291) verwendet ein Bild oder eine PDF-Datei einer Rechnung als Eingabe und extrahiert die relevanten Werte. Bei diesem Aufruf wird ein Antwortheaderfeld namens `Operation-Location` zurückgegeben. Der `Operation-Location`-Wert ist eine URL, die die Ergebnis-ID enthält, die im nächsten Schritt verwendet werden soll.

|Antwortheader| Ergebnis-URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1/prebuilt/invoice/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="get-analyze-invoice-result"></a>Get Analyze Invoice Result (Ergebnis von Rechnungsanalyse abrufen)

Im zweiten Schritt wird der Vorgang zum [Abrufen des Ergebnisses der Rechnungsanalyse](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9acb78c40a2533aee83) aufgerufen. Dieser Vorgang verwendet als Eingabe die Ergebnis-ID, die durch den Vorgang „Analysieren der Rechnung“ erstellt wurde. Er gibt eine JSON-Antwort zurück, die ein **Status**-Feld mit den folgenden möglichen Werten enthält. Sie rufen diesen Vorgang iterativ auf, bis er mit dem Wert **succeeded** (erfolgreich) zurückgegeben wird. Verwenden Sie ein Intervall von 3 bis 5 Sekunden, um zu vermeiden, dass die Rate der Anforderungen pro Sekunde (RPS) überschritten wird.

|Feld| type | Mögliche Werte |
|:-----|:----:|:----|
|status | Zeichenfolge | notStarted: Der Analysevorgang wurde noch nicht gestartet.<br /><br />running: Der Analysevorgang wird ausgeführt.<br /><br />failed: Beim Analysevorgang ist ein Fehler aufgetreten.<br /><br />succeeded: Der Analysevorgang war erfolgreich.|

Wenn im Feld **status** der Wert **succeeded** (erfolgreich) angezeigt wird, enthält die JSON-Antwort die Ergebnisse des Rechnungsverständnisses und optional die der Texterkennung, wenn sie angefordert wurden. Das Ergebnis des Rechnungsverständnisses ist als Wörterbuch benannter Feldwerte organisiert, wobei jeder Wert den extrahierten Text, den normalisierten Wert, den Begrenzungsrahmen, den Vertrauensgrad und die entsprechenden Wortelemente enthält. Außerdem sind die Zeilenelemente enthalten, die extrahiert werden, wobei jedes Zeilenelement den Betrag, die Beschreibung, den unitPrice, die Menge usw. enthält. Das Ergebnis der Texterkennung ist als eine Hierarchie von Linien und Wörtern mit Text, Begrenzungsfeldern und Konfidenzinformationen organisiert.

### <a name="sample-json-output"></a>JSON-Beispielausgabe

Die Antwort auf den Vorgang „Abrufen des Ergebnisses der Rechnungsanalyse“ ist die strukturierte Darstellung der Rechnung mit allen extrahierten Informationen.
Hier finden Sie ein [Beispiel für eine Rechnungsdatei](media/sample-invoice.jpg) und deren strukturierte Ausgabe ([Beispiel für eine Rechnungsausgabe](media/invoice-example-new.jpg)).

Die JSON-Ausgabe besteht aus drei Teilen:
* Der Knoten `"readResults"` enthält den gesamten erkannten Text und alle erkannten Auswahlmarkierungen. Der Text ist nach Seite, dann nach Zeile und dann nach einzelnen Wörtern sortiert.
* Der Knoten `"pageResults"` enthält die Tabellen und Zellen, die mit ihren umgebenden Feldern, Konfidenz (Zuverlässigkeit) und einem Verweis auf die Zeilen und Wörter in „readResults“ extrahiert wurden.
* Der Knoten `"documentResults"` enthält die spezifischen Werte und Rechnungspositionen der Rechnung, die vom Modell ermittelt wurden. Hier finden Sie alle Felder aus der Rechnung, wie z B. Rechnungs-ID, Lieferadresse, Rechnungsadresse, Kunde, Gesamtsumme, Rechnungspositionen und viele mehr.

## <a name="example-output"></a>Beispielausgabe

Der Rechnungsdienst extrahiert den Text, die Tabellen und 26 Rechnungsfelder. Im Folgenden werden die aus einer Rechnung extrahierten Felder in der JSON-Ausgabeantwort aufgeführt (die folgende Ausgabe basiert auf dieser [Beispielrechnung](media/sample-invoice.jpg)).

### <a name="key-value-pairs"></a>Schlüssel-Wert-Paare 

|Name| type | BESCHREIBUNG | Text | Wert (standardisierte Ausgabe) |
|:-----|:----|:----|:----| :----|
| CustomerName | Zeichenfolge | Kunde, dem die Rechnung gestellt wird | Microsoft Corp. |  |
| CustomerId | Zeichenfolge | Referenz-ID für den Kunden | CID-12345 |  |
| PurchaseOrder | Zeichenfolge | Eine Referenznummer für die Bestellung | PO-3333 | |
| InvoiceId | Zeichenfolge | ID für diese bestimmte Rechnung (oftmals „Rechnungsnummer“) | INV-100 | |
| Rechnungsdatum | date | Datum, an dem die Rechnung ausgestellt wurde | 15.11.2019 | 15.11.2019 |
| DueDate | date | Datum, an dem die Zahlung für diese Rechnung fällig ist | 15.12.2019 | 2019-12-15 |
| VendorName | Zeichenfolge | Anbieter, der diese Rechnung erstellt hat | CONTOSO LTD. | |
| VendorAddress | Zeichenfolge | Postanschrift für den Anbieter | 123 456th St New York, NY, 10001 | |
| VendorAddressRecipient | Zeichenfolge | Der „VendorAddress“ (Anbieteranschrift) zugeordnete Name | Contoso Headquarters | |
| CustomerAddress | Zeichenfolge | Postanschrift für den Kunden | 123 Other St, Redmond WA, 98052 | |
| CustomerAddressRecipient | Zeichenfolge | Der „CustomerAddress“ (Kundenadresse) zugeordnete Name | Microsoft Corp. | |
| BillingAddress | Zeichenfolge | Explizite Rechnungsadresse für den Kunden | 123 Bill St, Redmond WA, 98052 | |
| BillingAddressRecipient | Zeichenfolge | Der „BillingAddress“ (Rechnungsadresse) zugeordnete Name | Microsoft-Dienste | |
| ShippingAddress | Zeichenfolge | Explizite Lieferadresse für den Kunden | 123 Ship St, Redmond WA, 98052 | |
| ShippingAddressRecipient | Zeichenfolge | Der „ShippingAdresss“ (Lieferadresse) zugeordnete Name | Microsoft-Lieferung | |
| SubTotal | Anzahl | In dieser Rechnung identifiziertes Feld „Subtotal“ (Zwischensumme) | $ 100,00 | 100 |
| TotalTax | Anzahl | In dieser Rechnung identifiziertes Feld „Total tax“ (Gesamtsteuerbetrag) | $ 10,00 | 10 |
| InvoiceTotal | Anzahl | Summe der dieser Rechnung zugeordneten neuen Gebühren | $ 110,00 | 110 |
| AmountDue |  Anzahl | Gesamtbetrag, der an den Anbieter zu zahlen ist | $ 610,00 | 610 |
| ServiceAddress | Zeichenfolge | Explizite Dienstadresse oder Immobilienadresse für den Kunden | 123 Service St, Redmond WA, 98052 | |
| ServiceAddressRecipient | Zeichenfolge | Der „ServiceAddress“ (Dienstadresse) zugeordnete Name | Microsoft-Dienste | |
| RemittanceAddress | Zeichenfolge | Explizite Überweisungs- oder Zahlungsadresse für den Kunden | 123 Remit St New York, NY, 10001 |  |
| RemittanceAddressRecipient | Zeichenfolge | Der „RemittanceAddress“ (Überweisungsadresse) zugeordnete Name | Contoso-Abrechnung |  |
| ServiceStartDate | date | Anfangsdatum für den Dienstzeitraum (z. B. ein Dienstzeitraum für Hilfsprogrammrechnungen) | 14.10.2019 | 14.10.2019 |
| ServiceEndDate | date | Enddatum für den Dienstzeitraum (z B. ein Dienstzeitraum für Hilfsprogrammrechnungen) | 14.11.2019 | 2019-11-14 |
| PreviousUnpaidBalance | Anzahl | Zuvor explizit ausstehende Zahlung | $ 500,00 | 500 |

### <a name="line-items"></a>Rechnungspositionen

Im Folgenden finden Sie die aus einer Rechnung extrahierten Rechnungspositionen in der JSON-Ausgabeantwort (die folgende Ausgabe verwendet diese [Beispielrechnung](./media/sample-invoice.jpg)).

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

Die extrahierten Schlüssel-Wert-Paare der Rechnung und die Rechnungspositionen befinden sich im Abschnitt „documentResults“ der JSON-Ausgabe. 

## <a name="next-steps"></a>Nächste Schritte

- Probieren Sie Ihre eigenen Rechnungen und Beispiele in der [Beispielbenutzeroberfläche für die Formularerkennung](https://aka.ms/fott-2.1-ga) aus.
- Sehen Sie sich die Informationen unter [Schnellstart: Verwenden der Clientbibliothek für die Formularerkennung](quickstarts/client-library.md) an, um damit zu beginnen, eine Rechnungsverarbeitungs-App mit Formularerkennung in der Entwicklungssprache Ihrer Wahl zu schreiben.

## <a name="see-also"></a>Weitere Informationen

* [Was ist die Formularerkennung?](./overview.md)
* [REST-API-Referenzdokumente](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)
