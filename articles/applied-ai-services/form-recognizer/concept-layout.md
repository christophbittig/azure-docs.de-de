---
title: Layouts – Formularerkennung
titleSuffix: Azure Applied AI Services
description: Lernen Sie die Konzepte im Zusammenhang mit der Analyse der Layout-API mit der Formularerkennung-API kennen – Verwendung und Einschränkungen.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 325e11a16162646b7cd4a57c0330d9bc36d51f46
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027429"
---
# <a name="form-recognizer-layout-model"></a>Layoutmodell der Formularerkennung

Die Layout-API der Azure-Formularerkennung extrahiert Text, Tabellen, Auswahlmarkierungen und Strukturinformationen aus Dokumenten (PDF, TIFF) und Bildern (JPG, PNG, BMP).  Das Layoutmodell kombiniert eine verbesserte Version der leistungsstarken Funktionen zur [optischen Zeichenerkennung (Optical Character Recognition, OCR)](../../cognitive-services/computer-vision/overview-ocr.md) mit Deep Learning-Modellen zum Extrahieren von Text, Tabellen, Auswahlmarkierungen und der Dokumentstruktur.

***Beispielformular, das mit dem [Beschriftungstool der Formularerkennung](https://fott-2-1.azurewebsites.net/) verarbeitet wurde – Layoutfeature***

:::image type="content" source="media/layout-demo.gif" alt-text="Screenshot: GIF-Datei zum Beispielbeschriftungstool":::

**Features für die Datenextraktion**

| **Layoutmodell**   | **Textextraktion**   | **Auswahlmarkierungen**   | **Tabellen**  |
| --- | --- | --- | --- |
| Layout  | ✓  | ✓  | ✓  |

## <a name="development-options"></a>Entwicklungsoptionen

Die folgenden Ressourcen werden von der Azure-Formularerkennung v2.1 unterstützt:

| Funktion | Ressourcen |
|----------|-------------------------| 
|**Layout-API**| <ul><li>[**Formularerkennungstool für die Bezeichnung**](https://fott-2-1.azurewebsites.net/layout-analyze)</li><li>[**REST-API**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-layout)</li><li>[**Clientbibliothek/SDK**](quickstarts/try-sdk-rest-api.md)</li><li>[**Docker-Container für Formularerkennung**](containers/form-recognizer-container-install-run.md?branch=main&tabs=layout#run-the-container-with-the-docker-compose-up-command)</li></ul>|

Die folgenden Ressourcen werden von der Azure-Formularerkennung v3.0 unterstützt:

| Funktion | Ressourcen | Modell-ID |
|----------|------------|------------|
|**Layoutmodell**| <ul><li>[**Formularerkennung Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**REST-API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li><li>[**Java SDK**](quickstarts/try-v3-java-sdk.md)</li><li>[**JavaScript SDK**](quickstarts/try-v3-javascript-sdk.md)</li></ul>|**prebuilt-layout**|

### <a name="try-form-recognizer"></a>Formularerkennung ausprobieren

Erfahren Sie, wie Daten, einschließlich Tabellen, Kontrollkästchen und Text aus Formularen und Dokumenten mithilfe von Formularerkennung Studio oder unserem Beispielbeschriftungstool extrahiert werden. Sie benötigen Folgendes:

* Azure-Abonnement – Sie können ein [kostenloses Abonnement erstellen](https://azure.microsoft.com/free/cognitive-services/)

* Eine [Formularerkennungsinstanz](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) im Azure-Portal Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst auszuprobieren. Wählen Sie nach der Bereitstellung Ihrer Ressource **Zu Ressource wechseln** aus, um Ihren API-Schlüssel und -Endpunkt abzurufen.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Screenshot: Schlüssel und Endpunktspeicherort im Azure-Portal":::

#### <a name="form-recognizer-studio-preview"></a>Formularerkennung Studio (Vorschau)

> [!NOTE]
> Formularerkennung Studio ist mit der Vorschau-API (v3.0) verfügbar.

***Beispielformular, das mit [Formularerkennung Studio](https://formrecognizer.appliedai.azure.com/studio/layout) verarbeitet wird***

:::image type="content" source="media/studio/sample-layout.png" alt-text="Screenshot: Dokumentverarbeitung in Formularerkennung Studio":::

1. Wählen Sie auf der Startseite von Azure-Formularerkennung Studio die Option **Layout** aus.

1. Sie können entweder das Beispieldokument analysieren oder die Schaltfläche **+ Hinzufügen** auswählen, um Ihr eigenes Beispiel hochzuladen.

1. Wählen Sie die Schaltfläche **Analysieren** aus:

    :::image type="content" source="media/studio/layout-analyze.png" alt-text="Screenshot: Menü „Layout analysieren“":::

   > [!div class="nextstepaction"]
   > [Ausprobieren von Formularerkennung Studio](https://formrecognizer.appliedai.azure.com/studio/layout)

#### <a name="sample-labeling-tool"></a>Beispiel-Beschriftungstool

Sie benötigen ein Formulardokument. Sie können unser [Beispielformulardokument](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) verwenden.

1. Wählen Sie auf der Startseite des Beispielbeschriftungstools die Option **Use Layout to get text, tables and selection marks** (Layout zum Abrufen von Text, Tabellen und Auswahlmarkierungen verwenden) aus.

1. Wählen Sie im Dropdownmenü die Option **Lokale Datei** aus.

1. Laden Sie Ihre Datei hoch, und wählen Sie **Layout ausführen** aus.

   :::image type="content" source="media/try-layout.png" alt-text="Screenshot: Auswahlmenü für die Layoutdateiquelle – Beispielbeschriftungstool":::

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

* Erfahren Sie, wie Sie die Vorschauversion in Ihren Anwendungen und Workflows verwenden können, indem Sie unseren [**Formularerkennung v3.0-Migrationsleitfaden**](v3-migration-guide.md) befolgen.

* Erkunden Sie die [**REST-API (Vorschau)**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument), um mehr über die Vorschauversion und neue Funktionen zu erfahren.

## <a name="next-steps"></a>Nächste Schritte

* Sehen Sie sich eine Schnellstartanleitung zur Formularerkennung an:

  > [!div class="nextstepaction"]
  > [Formularerkennung: Schnellstart](quickstarts/try-sdk-rest-api.md)

* Erkunden Sie unsere REST-API:

    > [!div class="nextstepaction"]
    > [Formularerkennung-API Version 2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeLayoutAsync)
