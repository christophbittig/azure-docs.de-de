---
title: Layouts – Formularerkennung
titleSuffix: Azure Applied AI Services
description: Lernen Sie die Konzepte im Zusammenhang mit Layoutanalyse mit der Formularerkennung-API (Verwendung und Einschränkungen) kennen.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 425a5cb9ab332076ddd0d745bba7b914e5601917
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129707144"
---
# <a name="form-recognizer-layout-model"></a>Layoutmodell der Formularerkennung

Die Layout-API der Azure-Formularerkennung extrahiert Text, Tabellen, Auswahlmarkierungen und Strukturinformationen aus Dokumenten (PDF, TIFF) und Bildern (JPG, PNG, BMP). Sie ermöglicht es Kunden, für Dokumente in verschiedenen Formaten strukturierte Datendarstellungen der Dokumente zurückzugeben. Die API kombiniert eine verbesserte Version der leistungsstarken Funktionen zur [optischen Zeichenerkennung (Optical Character Recognition, OCR)](../../cognitive-services/computer-vision/overview-ocr.md) mit Deep Learning-Modellen zum Extrahieren von Text, Tabellen, Auswahlmarkierungen und der Dokumentstruktur.

##### <a name="sample-form-processed-with-form-recognizer-sample-labeling-tool--layout-feature"></a>Beispielformular, das mit dem Beschriftungswerkzeug der [Formularerkennung verarbeitet wurde ](https://fott-2-1.azurewebsites.net/) Layout-Funktion:

:::image type="content" source="media/layout-demo.gif" alt-text="{alt-text}":::

**Features für die Datenextraktion**

| **Layoutmodell**   | **Textextraktion**   | **Auswahlmarkierungen**   | **Tabellen**  |
| --- | --- | --- | --- |
| Layout  | ✓  | ✓  | ✓  |

## <a name="try-form-recognizer-studio-preview"></a>Ausprobieren von Formularerkennung Studio (Vorschau)

* Formularerkennung Studio ist mit der Vorschau-API (v3.0) verfügbar.

* Extrahieren Sie Tabellen, Kontrollkästchen und Text aus Formularen und Dokumenten mit unserem Layoutfeature von Formularerkennung Studio:

> [!div class="nextstepaction"]
> [Ausprobieren von Formularerkennung Studio](https://formrecognizer.appliedai.azure.com/studio/layout)

## <a name="try-form-recognizer-sample-labeling-tool"></a>Tool zum Bezeichnen von Beispielen für die Formularerkennung ausprobieren

Sie können sehen, wie Layoutdaten extrahiert werden, indem Sie unser Tool für die Beschriftung von Beispielen ausprobieren. Sie benötigen Folgendes:

* Azure-Abonnement – Sie können ein [kostenloses Abonnement erstellen](https://azure.microsoft.com/free/cognitive-services/)

* Eine [Formularerkennungsinstanz](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) im Azure-Portal Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst auszuprobieren. Klicken Sie nach der Bereitstellung Ihrer Ressource auf **Zu Ressource wechseln**, um Ihren API-Schlüssel und Endpunkt abzurufen.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Screenshot: Schlüssel und Endpunktspeicherort im Azure-Portal":::

* Ein Formulardokument. Sie können unser [Beispielformulardokument](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) verwenden.

> [!div class="nextstepaction"]
  > [Testen](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

  Auf der Benutzeroberfläche der Formularerkennung:

  1. Wählen Sie **Layout zum Abrufen von Text, Tabellen und Auswahlmarkierungen verwenden** aus.
  1. Wählen Sie im Dropdownmenü die Option **Lokale Datei** aus.
  1. Laden Sie Ihre Datei hoch, und wählen Sie **Layout ausführen** aus.

  :::image type="content" source="media/try-layout.png" alt-text="Screenshot: Auswahlmenü für die Layoutdateiquelle – Tool für die Beschriftung von Beispielen":::

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

 Die Vorschauversion der Formularerkennung bietet zusätzliche Sprachunterstützung für das Layoutmodell. *Eine* vollständige Liste der unterstützten handschriftlichen und gedruckten Texte finden Sie in unserem [Sprach-Support](language-support.md#layout-and-custom-model).

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
    > [Formularerkennung-API Version 2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeLayoutAsync)