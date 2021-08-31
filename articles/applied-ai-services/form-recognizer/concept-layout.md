---
title: Layouts – Formularerkennung
titleSuffix: Azure Applied AI Services
description: Lernen Sie die Konzepte im Zusammenhang mit Layoutanalyse mit der Formularerkennung-API (Verwendung und Einschränkungen) kennen.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/09/2021
ms.author: lajanuar
ms.openlocfilehash: af45cf3d3f9ae534be52ae617b13c76eb0d2eae8
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122356648"
---
# <a name="form-recognizer-layout-service"></a>Layoutdienst für die Formularerkennung

Die Layout-API der Azure-Formularerkennung extrahiert Text, Tabellen, Auswahlmarkierungen und Strukturinformationen aus Dokumenten (PDF, TIFF) und Bildern (JPG, PNG, BMP). Sie ermöglicht es Kunden, für Dokumente in vielen verschiedenen Formaten strukturierte Datendarstellungen der Dokumente zurückzugeben. Die API kombiniert eine verbesserte Version der leistungsstarken Funktionen zur [optischen Zeichenerkennung (Optical Character Recognition, OCR)](../../cognitive-services/computer-vision/overview-ocr.md) mit Deep Learning-Modellen zum Extrahieren von Text, Tabellen, Auswahlmarkierungen und der Dokumentstruktur.

## <a name="what-does-the-layout-service-do"></a>Welche Aufgaben führt der Layoutdienst aus?

Die Layout-API extrahiert Text, Tabellen einschließlich Tabellenkopfzeilen, Auswahlmarkierungen und Strukturinformationen aus Dokumenten mit einer herausragenden Genauigkeit und gibt eine organisierte strukturierte JSON-Antwort zurück. Dokumente können viele verschiedene Formate und Qualitätsmerkmale aufweisen, beispielsweise mit dem Handy fotografierte Bilder, gescannte Dokumente und digitale PDF-Dateien. Die Layout-API extrahiert die strukturierte Ausgabe exakt aus allen diesen Dokumenten.

![Beispiel für ein Layout](./media/layout-demo.gif)

## <a name="try-it&quot;></a>Ausprobieren

Wenn Sie den Layoutdienst für die Formularerkennung ausprobieren möchten, wechseln Sie zum Onlinetool für die Beispielbenutzeroberfläche:

> [!div class=&quot;nextstepaction&quot;]
> [Layoutmodell ausprobieren](https://aka.ms/fott-2.1-ga &quot;Beginnen Sie mit dem vordefinierten Layoutmodell, um Daten aus Ihren Formularen zu extrahieren.")

Zum Ausprobieren der Layout-API für die Formularerkennung benötigen Sie ein Azure-Abonnement ([kann hier kostenlos erstellt werden](https://azure.microsoft.com/free/cognitive-services)), einen Endpunkt für eine [Formularerkennungsressource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) und einen entsprechenden Schlüssel.

![Screenshot der Beispielbenutzeroberfläche; der Text, die Tabellen und Auswahlmarkierungen eines Dokuments werden analysiert.](./media/analyze-layout.png)

## <a name="input-requirements"></a>Eingabeanforderungen

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="analyze-layout"></a>Analysieren des Layouts

Rufen Sie zunächst den Vorgang [Analyze Layout](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeLayoutAsync) auf. Der Vorgang „Analyze Layout“ verwendet ein Dokument (Bild-, TIFF- oder PDF-Datei) als Eingabe und extrahiert den Text, die Tabellen, Auswahlmarkierungen und Struktur des Dokuments. Bei diesem Aufruf wird ein Antwortheaderfeld namens `Operation-Location` zurückgegeben. Der `Operation-Location`-Wert ist eine URL, die die Ergebnis-ID enthält, die im nächsten Schritt verwendet werden soll.

|Antwortheader| Ergebnis-URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1/layout/analyzeResults/{resultId}' |

## <a name="get-analyze-layout-result"></a>Abrufen des Ergebnisses der Layoutanalyse

Im zweiten Schritt wird der Vorgang [Abrufen des Ergebnisses der Layoutanalyse](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetAnalyzeLayoutResult) aufgerufen. Bei diesem Vorgang wird die Ergebnis-ID als Eingabe verwendet, die durch den Vorgang „Analysieren des Layouts“ erstellt wurde. Er gibt eine JSON-Antwort zurück, die ein **Status**-Feld mit den folgenden möglichen Werten enthält.

|Feld| type | Mögliche Werte |
|:-----|:----:|:----|
|status | Zeichenfolge | `notStarted`: Der Analysevorgang wurde noch nicht gestartet.<br /><br />`running`: Der Analysevorgang wird ausgeführt.<br /><br />`failed`: Beim Analysevorgang ist ein Fehler aufgetreten.<br /><br />`succeeded`: Der Analysevorgang war erfolgreich.|

Rufen Sie diesen Vorgang solange auf, bis der Wert `succeeded` zurückgegeben wird. Verwenden Sie ein Intervall von 3 bis 5 Sekunden, um zu vermeiden, dass die Rate der Anforderungen pro Sekunde (RPS) überschritten wird.

Wenn das Feld **status** den Wert `succeeded` aufweist, enthält die JSON-Antwort das extrahierte Layout, den extrahierten Text und die extrahierten Tabellen und Auswahlmarkierungen. Die extrahierten Daten enthalten die extrahierten Textzeilen und Wörter, Begrenzungsrahmen, die handschriftliche Anzeige von Textdarstellung, Tabellen und Auswahlmarkierungen mit der Angabe „ausgewählt“ oder „nicht ausgewählt“.

## <a name="sample-json-output"></a>JSON-Beispielausgabe

Die Antwort auf den Vorgang *Get Analyze Layout Result* ist eine strukturierte Darstellung des Dokuments mit allen extrahierten Informationen.
Hier sehen Sie ein [Beispiel für eine Dokumentdatei](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout.pdf) und deren strukturierte Ausgabe [Beispiel für eine Layoutausgabe](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout-output.json).

Die JSON-Ausgabe besteht aus zwei Teilen:

* Der Knoten `readResults` enthält den gesamten erkannten Text und alle erkannten Auswahlmarkierungen. Der Text ist nach Seite, dann nach Zeile und dann nach einzelnen Wörtern sortiert.
* Der Knoten `pageResults` enthält die Tabellen und Zellen, die mit ihren umgebenden Feldern, Konfidenz (Zuverlässigkeit) und einem Verweis auf die Zeilen und Wörter in „readResults“ extrahiert wurden.

## <a name="features"></a>Funktionen

### <a name="tables-and-table-headers"></a>Tabellen und Tabellenheader

Die Layout-API extrahiert Tabellen in den `pageResults` Abschnitt der JSON-Ausgabe. Dokumente können gescannt, fotografiert oder digitalisiert sein. Tabellen können komplexe Tabellen mit zusammengeführten Zellen oder Spalten (mit oder ohne Rahmen) und mit ungeraden Winkeln sein. Die extrahierten Tabelleninformationen umfassen die Anzahl von Spalten und Zeilen sowie den Zeilen- und Spaltenabstand. Jede Zelle mit ihrem Begrenzungsrahmen wird zusammen mit Informationen ausgegeben, unabhängig davon, ob sie als Teil einer Kopfzeile erkannt werden oder nicht. Die vom Modell vorhergesagten Kopfzellen können mehrere Zeilen umfassen und müssen nicht zwingend die ersten Zeilen in einer Tabelle sein. Dies funktioniert auch bei gedrehten Tabellen. Jede Tabellenzelle enthält auch den vollständigen Text mit Verweisen auf die einzelnen Wörter im `readResults`-Abschnitt.

:::image type="content" source="./media/layout-table-headers-example.png" alt-text="Ausgabe von Layouttabellenheadern":::

### <a name="selection-marks"></a>Auswahlmarkierungen

Die Layout-API extrahiert auch Auswahlmarkierungen aus Dokumenten. Extrahierte Auswahlmarkierungen enthalten den Begrenzungsrahmen, die Konfidenz (Zuverlässigkeit) und den Status („ausgewählt“ oder „nicht ausgewählt“). Informationen zur Auswahlmarkierung werden im Abschnitt `readResults` der JSON-Ausgabe extrahiert.

:::image type="content" source="./media/layout-selection-marks.png" alt-text="Ausgabe von Layoutauswahlmarkierungen":::

### <a name="text-lines-and-words"></a>Textzeilen und Wörter

Die Layout-API extrahiert Text aus Dokumenten und Bildern mit unterschiedlichen Textneigungen und Farben. Sie unterstützt Fotos von Dokumenten, Faxe, gedruckten und handschriftlichen Text (nur Englisch) und gemischte Modi. Text wird mit Informationen zu Zeilen, Wörtern, Begrenzungsrahmen, Konfidenzbewertungen und Stil (handschriftlich oder anderer) extrahiert. Alle Textinformationen sind im Abschnitt `readResults` der JSON-Ausgabe enthalten.

:::image type="content" source="./media/layout-text-extraction.png" alt-text="Ausgabe der Layouttextextraktion":::

### <a name="natural-reading-order-for-text-lines-latin-only"></a>Natürliche Leserichtung für Textzeilen (nur Lateinisch)

Mit dem Abfrageparameter `readingOrder` können Sie die Leserichtung angeben, in der die Textzeilen ausgegeben werden. Verwenden Sie `natural` für eine benutzerfreundlichere Ausgabe der Lesereihenfolge, wie im folgenden Beispiel gezeigt. Dieses Feature wird nur für lateinische Sprachen unterstützt.

:::image type="content" source="./media/layout-reading-order-example.png" alt-text="Beispiel für Layout-Leserichtung" lightbox="../../cognitive-services/Computer-vision/Images/ocr-reading-order-example.png":::

### <a name="handwritten-classification-for-text-lines-latin-only"></a>Handschriftliche Klassifizierung für Textzeilen (nur Lateinisch)

Die Antwort umfasst die Klassifizierung, ob es sich bei den einzelnen Textzeilen um einen handschriftlichen Stil handelt, sowie eine Konfidenzbewertung. Dieses Feature wird nur für lateinische Sprachen unterstützt. Das folgende Beispiel zeigt die handschriftliche Klassifizierung für den Text im Bild.

:::image type="content" source="./media/layout-handwriting-classification.png" alt-text="Beispiel für Handschriftklassifizierung":::

### <a name="select-page-numbers-or-ranges-for-text-extraction"></a>Auswählen von Seitenzahlen oder Seitenbereichen für die Textextraktion

Verwenden Sie für umfangreiche Dokumente mit mehreren Seiten den Abfrageparameter `pages`, um bestimmte Seitenzahlen oder Seitenbereiche für die Textextraktion anzugeben. Das folgende Beispiel zeigt ein Dokument mit 10 Seiten, wobei für beide Fälle – alle Seiten (1-10) und ausgewählte Seiten (3-6) – Text extrahiert wurde.

:::image type="content" source="./media/layout-select-pages-for-text.png" alt-text="Layout – Ausgabe ausgewählter Seiten":::

## <a name="next-steps"></a>Nächste Schritte

* Testen einer eigenen Layoutextraktion mithilfe des [Onlinetools für die Beispielbenutzeroberfläche zur Formularerkennung](https://aka.ms/fott-2.1-ga)
* Arbeiten Sie einen [Schnellstart für die Formularerkennung](quickstarts/client-library.md#analyze-layout) durch, um die ersten Schritte zum Extrahieren von Layouts in der Entwicklungssprache Ihrer Wahl auszuführen.

## <a name="see-also"></a>Weitere Informationen

* [Was ist die Formularerkennung?](./overview.md)
* [REST-API-Referenzdokumente](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeLayoutAsync)
