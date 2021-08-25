---
title: 'Schnellstart: Beschriften von Formularen, Trainieren eines Modells und Analysieren von Formularen mithilfe des Tools für die Beschriftung von Beispielen – Formularerkennung'
titleSuffix: Azure Applied AI Services
description: In dieser Schnellstartanleitung verwenden Sie das Formularerkennungstool für die Beschriftung von Beispielen, um Formulardokumente manuell zu beschriften. Danach trainieren Sie ein benutzerdefiniertes Modell für die Dokumentverarbeitung mit den beschrifteten Dokumenten und verwenden das Modell, um Schlüssel-Wert-Paare zu extrahieren.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 05/14/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-may-2021
keywords: Verarbeiten von Dokumenten
ms.openlocfilehash: 5fd03b5ebf4aa40a68c62d029fbbe1a09839b7c0
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122327360"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
<!-- markdownlint-disable MD029 -->
# <a name="get-started-with-form-recognizer"></a>Erste Schritte mit der Formularerkennung

Erste Schritte mit der Formularerkennung mithilfe des Beispieltools für die Formularerkennung Die Azure-Formularerkennung ist ein kognitiver Dienst, mit dem Sie unter Verwendung von Technologie für maschinelles Lernen Software für die automatisierte Datenverarbeitung entwickeln können. Identifizieren und extrahieren Sie Schlüssel-Wert-Paare, Auswahlmarkierungen, Tabellendaten und Ähnliches aus Formulardokumenten. Der Dienst gibt strukturierte Daten aus, die auch die Beziehungen in der ursprünglichen Datei umfassen. Die Formularerkennung kann über das Beispieltool oder per REST-API oder SDK verwendet werden. Führen Sie diese Schritte aus, um die Formularerkennung über das Beispieltool zu testen.

Verwenden Sie die Formularerkennung für Folgendes:

* Analysieren des Layouts
* Analysieren mithilfe eines vordefinierten Modells (Rechnungen, Belege, ID-Dokumente)
* Trainieren und Analysieren eines benutzerdefinierten Formulars

## <a name="prerequisites"></a>Voraussetzungen

Für diesen Schnellstart benötigen Sie Folgendes:

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* Sobald Sie über Ihr Azure-Abonnement verfügen, sollten Sie über <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Erstellen einer Formularerkennungsressource"  target="_blank"> im Azure-Portal eine Formularerkennungsressource </a> erstellen, um Ihren Schlüssel und Endpunkt abzurufen.
  * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.
* (Optional) Herunterladen und Entzippen der folgenden Schnellstartbeispieldokumente

## <a name="create-a-form-recognizer-resource"></a>Erstellen einer Formularerkennungsressource

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-layout"></a>Analysieren des Layouts

Extrahieren von Text, Tabellen, Auswahlmarkierungen und der Struktur aus einem Dokument.

1. Wechseln Sie zum [Beispieltool für die Formularerkennung](https://fott-2-1.azurewebsites.net/).
2. Wählen Sie auf der Startseite des Beispieltools die Option zum Verwenden des Layouts zum Abrufen von Text, Tabellen und Auswahlmarkierungen aus.

     :::image type="content" source="../media/label-tool/layout-1.jpg" alt-text="Verbindungseinstellungen für das Layouttool der Formularerkennung":::.

3. Ersetzen Sie „{need Endpoint}“ durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.

4. Ersetzen Sie {need apiKey} durch den Abonnementschlüssel, den Sie von Ihrer Formularerkennungsressource erhalten haben.

    :::image type="content" source="../media/label-tool/layout-2.jpg" alt-text="Verbindungseinstellungen für das Layouttool der Formularerkennung":::.

5. Wählen Sie die Quell-URL aus, fügen Sie die folgende URL des Beispieldokuments `https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/layout-page-001.jpg` ein, und klicken Sie auf die Schaltfläche „Abrufen“.

1. Klicken Sie auf „Layout ausführen“. Das Tool für die Beschriftung von Beispielen der Formularerkennung ruft die Analyze Layout-API auf und analysiert das Dokument.

1. Anzeigen der Ergebnisse – Sehen Sie sich den hervorgehobenen extrahierten Text, die erkannten Auswahlmarkierungen und die erkannten Tabellen an.

    :::image type="content" source="../media/label-tool/layout-3.jpg" alt-text="Verbindungseinstellungen für das Formularerkennungstool":::.

1. Laden Sie die JSON-Ausgabedatei herunter, um die ausführlichen Layoutergebnisse anzuzeigen.
     * Der Knoten „readResults“ enthält jede Textzeile mit der Platzierung des zugehörigen Begrenzungsrahmens auf der Seite.
     * Der Knoten „selectionMarks“ zeigt jede Auswahlmarkierung (Kontrollkästchen, Optionsfeld) und ihren Status („ausgewählt“ oder „nicht ausgewählt“).
     * Der Abschnitt „pageResults“ enthält die extrahierten Tabellen. Für jede Tabelle werden der Text-, Zeilen- und Spaltenindex, die Zeilen- und Spaltenaufteilung, der Begrenzungsrahmen und mehr extrahiert.

## <a name="analyze-using-a-prebuilt-model-invoices-receipts-ids-"></a>Analysieren mithilfe eines vordefinierten Modells (Rechnungen, Belege, IDs, ..)

Extrahieren Sie Text, Tabellen und Schlüssel-Wert-Paare aus Rechnungen, Verkaufsbelegen, Identitätsdokumenten oder Visitenkarten mithilfe eines vordefinierten Modells für die Formularerkennung.

1. Wechseln Sie zum [Beispieltool für die Formularerkennung](https://fott-2-1.azurewebsites.net/).
2. Wählen Sie auf der Startseite des Beispieltools die Option zum Verwenden des vordefiniertes Modell zum Abrufen von Daten aus.

    :::image type="content" source="../media/label-tool/prebuilt-1.jpg" alt-text="Analysieren der Ergebnisse des Formularerkennungslayouts":::

3. Auswählen der Quell-URL

4. Wählen Sie aus den folgenden Optionen die Datei aus, die Sie analysieren möchten:

    * Eine URL für ein Bild einer Rechnung. Für diese Schnellstartanleitung können Sie ein [Dokument einer Beispielrechnung](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/forms/Invoice_1.pdf) verwenden.
    * Eine URL für ein Bild eines Verkaufsbelegs. Für diese Schnellstartanleitung können Sie ein [Dokument einer Beispiel-ID](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/id-license.jpg) verwenden.
    * Eine URL für ein Bild eines Verkaufsbelegs. Für diesen Schnellstart können Sie ein [Bild eines Beispielbelegs](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg) verwenden.
    * Eine URL für ein Bild einer Visitenkarte. Für diesen Schnellstart können Sie ein [Bild einer Beispielvisitenkarte](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/business_cards/business-card-english.jpg) verwenden.

5. Ersetzen Sie „{need Endpoint}“ durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.

6. Ersetzen Sie {need apiKey} durch den Abonnementschlüssel, den Sie von Ihrer Formularerkennungsressource erhalten haben.

    :::image type="content" source="../media/label-tool/prebuilt-3.jpg" alt-text="Verbindungseinstellungen für das Tool zur vordefinierten Formularerkennung":::.

7. Wählen Sie den zu analysierenden Formulartyp – Rechnung, Beleg, Visitenkarten oder ID – basierend auf dem Typ des Dokuments aus, das Sie analysieren und auswählen möchten.

8. Klicken Sie auf „Analyse ausführen“. Das Tool für die Beschriftung von Beispielen der Formularerkennung ruft die Analyze Prebuilt-API auf und analysiert das Dokument.
9. Zeigen Sie die Ergebnisse an. Sehen Sie sich die extrahierten Schlüssel-Wert-Paare, die Positionen, den extrahierten markierten Text und die erkannten Tabellen an.

    :::image type="content" source="../media/label-tool/prebuilt-2.jpg" alt-text="Analysieren der Ergebnisse der vordefinierten Rechnung der Formularerkennung":::

10. Laden Sie die JSON-Ausgabedatei herunter, um die ausführlichen Ergebnisse anzuzeigen.

    * Der Knoten „readResults“ enthält jede Textzeile mit der Platzierung des zugehörigen Begrenzungsrahmens auf der Seite.
    * Der Knoten „selectionMarks“ zeigt jede Auswahlmarkierung (Kontrollkästchen, Optionsfeld) und ihren Status („ausgewählt“ oder „nicht ausgewählt“).
    * Der Abschnitt „pageResults“ enthält die extrahierten Tabellen. Für jede Tabelle werden der Text-, Zeilen- und Spaltenindex, die Zeilen- und Spaltenaufteilung, der Begrenzungsrahmen und mehr extrahiert.
    * Das Feld „documentResults“ enthält Informationen zu Schlüssel-Wert-Paaren und Positionen für die relevantesten Teile des Dokuments.

## <a name="train-a-custom-form-model"></a>Trainieren eines benutzerdefinierten Formularmodells

Trainieren Sie ein benutzerdefiniertes Formularmodell, das auf Ihre Dokumente abgestimmt ist. Extrahieren Sie Text, Tabellen, Auswahlmarkierungen und Schlüssel-Wert-Paare aus Ihren Dokumenten mithilfe der Formularerkennung – Benutzerdefiniert.

### <a name="prerequisites-for-training-a-custom-form-model"></a>Voraussetzungen für das Trainieren eines benutzerdefinierten Formularmodells

* Ein Azure Storage-Blobcontainer, der eine Reihe von Trainingsdaten enthält. Stellen Sie zunächst sicher, dass alle Trainingsdokumente im selben Format vorliegen. Wenn Ihre Formulare unterschiedliche Formate aufweisen, sortieren Sie sie in Unterordner für jeweils ein Format. In dieser Schnellstartanleitung können Sie die Dateien im Ordner „Trainieren“ des [Beispieldatasets](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample_data_without_labels.zip) verwenden (sample_data.zip herunterladen und extrahieren).
* Konfigurieren Sie die domänenübergreifende Ressourcenfreigabe (Cross-Domain Resource Sharing, CORS) im Azure Storage-Blob. Aktivieren Sie CORS für Ihr Speicherkonto. Wählen Sie im Azure-Portal Ihr Speicherkonto und dann im linken Bereich die Registerkarte **CORS** aus. Geben Sie auf der untersten Zeile die folgenden Werte ein. Wählen Sie oben **Speichern** aus. </br></br>

  * Zulässige Ursprünge = *
  * Zulässige Methoden = \[alle auswählen\]
  * Zulässige Header = *
  * Verfügbar gemachte Header = *
  * Max. Alter = 200

> [!div class="mx-imgBorder"]
> ![Einrichtung von CORS im Azure-Portal](../media/label-tool/cors-setup.png)

### <a name="use-the-sample-labeling-tool"></a>Verwenden des Beispieltools für Beschriftungen

1. Wechseln Sie zum [Beispieltool für die Formularerkennung](https://fott-2-1.azurewebsites.net/).

1. Wählen Sie auf der Startseite des Beispieltools die Option zum Verwenden des benutzerdefinierten Formulars zum Trainieren eines Modells mit Bezeichnungen und Abrufen von Schlüssel-Wert-Paaren aus.

    :::image type="content" source="../media/label-tool/custom-1.jpg" alt-text="Trainieren eines benutzerdefinierten Modells":::

2. Wählen Sie „Neues Projekt“ aus.

#### <a name="create-a-new-project"></a>Erstellen eines neuen Projekts

Konfigurieren Sie die Projekteinstellungen, und füllen Sie die Felder mit den folgenden Werten aus:

* **Anzeigename**: der Anzeigename des Projekts.
* **Sicherheitstoken**: Einige Projekteinstellungen können vertrauliche Werte wie z. B. API-Schlüssel oder andere gemeinsam genutzte Geheimnisse enthalten. Jedes Projekt generiert ein Sicherheitstoken, das zum Verschlüsseln und Entschlüsseln von vertraulichen Projekteinstellungen verwendet werden kann. Sie finden die Sicherheitstoken in den Anwendungseinstellungen, indem Sie unten auf der linken Navigationsleiste das Zahnradsymbol auswählen.

* **Quellverbindung**: Das Tool für die Beschriftung von Beispielen stellt eine Verbindung mit einer Quelle (Ihre hochgeladenen Originalformulare) und einem Ziel (erstellte Beschriftungen und Ausgabedaten) her. Verbindungen können projektübergreifend eingerichtet und freigegeben werden. Dabei wird ein erweiterbares Anbietermodell verwendet, sodass Sie ganz einfach neue Anbieter von Quellen und Zielen hinzufügen können. Erstellen Sie eine neue Verbindung, und klicken Sie auf die Schaltfläche **Verbindung hinzufügen**. Geben Sie die folgenden Werte in die Felder ein:
  * **Anzeigename**: der Anzeigename der Verbindung.
  * **Beschreibung**: die Beschreibung Ihres Projekts.
  * **SAS-URL**: die Shared Access Signature-URL (SAS) Ihres Azure Blob Storage-Containers.

  * [!INCLUDE [get SAS URL](../includes/sas-instructions.md)]

   :::image type="content" source="../media/quickstarts/get-sas-url.png" alt-text="SAS-Speicherort":::

* **Ordnerpfad** (optional): Wenn Ihre Quellformulare in einem Ordner im Blobcontainer gespeichert sind, geben Sie hier den Ordnernamen an.
* **URI des Formularerkennungsdiensts**: Die URL Ihres Formularerkennungs-Endpunkts.
* **API-Schlüssel**: Der Schlüssel Ihres Formularerkennungsabonnements.
* **Beschreibung** (optional): Projektbeschreibung.

    :::image type="content" source="../media/label-tool/connections.png"  alt-text="Verbindungseinstellungen":::

#### <a name="label-your-forms"></a>Beschriften Ihrer Formulare

  :::image type="content" source="../media/label-tool/new-project.png"  alt-text="Neue Projektseite":::

Wenn Sie ein Projekt erstellen oder öffnen, wird das Hauptfenster des Beschriftungs-Editors geöffnet. Der Beschriftungs-Editor besteht aus drei Teilen:

* Ein Vorschaubereich, dessen Größe angepasst werden kann und der eine scrollbare Liste mit Formularen aus der Quellverbindung enthält.
* Der Hauptbereich des Editors, in dem Sie Beschriftungen anwenden können.
* Der Bearbeitungsbereich des Editors, in dem Sie Beschriftungen ändern, sperren, neu anordnen und löschen können.

##### <a name="identify-text-and-tables"></a>Identifizieren von Text und Tabellen

Wählen Sie im linken Bereich **OCR in allen Dateien ausführen** aus, um Text- und Tabellenlayoutinformationen für jedes Dokument abzurufen. Das Beschriftungstool zeichnet einen Begrenzungsrahmen um jedes Textelement.

Das Beschriftungstool zeigt außerdem, welche Tabellen automatisch extrahiert wurden. Wählen Sie das Tabellen-/Rastersymbol auf der linken Seite des Dokuments aus, um die extrahierte Tabelle anzuzeigen. Da in dieser Schnellstartanleitung der Tabelleninhalt automatisch extrahiert wird, versehen Sie ihn nicht mit Bezeichnungen, sondern verlassen sich stattdessen auf die automatisierte Extraktion.

  :::image type="content" source="../media/label-tool/table-extraction.png" alt-text="Tabellenvisualisierung im Tool für die Beschriftung von Beispielen":::

##### <a name="apply-labels-to-text"></a>Anwenden von Beschriftungen auf Text

Als nächstes erstellen Sie Tags (Beschriftungen) und wenden sie auf die Textelemente an, die das Modell analysieren soll. Beachten Sie, dass das Dataset für die Beispielbeschriftung bereits beschriftete Felder enthält und wir ein weiteres Feld hinzufügen werden.

1. Verwenden Sie zuerst den Tag-Editor-Bereich, um ein neues Tag zu erstellen, das Sie identifizieren möchten.
   1. Wählen Sie **+** aus, um ein neues Tag zu erstellen.
   1. Geben Sie den Tagnamen ein. Fügen Sie das Tag „Gesamt“ hinzu.
   1. Drücken Sie die EINGABETASTE, um das Tag zu speichern.
1. Wählen Sie im Hauptbereich des Editors den Gesamtwert in den markierten Textelementen aus.
1. Wählen Sie das Tag „Gesamt“ aus, das Sie auf den Wert anwenden möchten, oder drücken Sie die entsprechende Taste auf der Tastatur. Die Zifferntasten sind als Schnellzugriffstasten für die ersten zehn Tags zugewiesen. Sie können die Beschriftungen mithilfe der nach oben und unten weisenden Pfeilsymbole im Bearbeitungsbereich neu anordnen.

    > [!Tip]
    > Beachten Sie beim Beschriften Ihrer Formulare die folgenden Tipps:
    >
    > * Sie können auf jedes ausgewählte Element nur ein Tag anwenden.
    > * Jedes Tag kann nur einmal pro Seite angewendet werden. Wenn ein Wert in demselben Formular mehrfach erscheint, sollten Sie für jede Instanz andere Tags erstellen. Beispiel: „rechnung 1“, „rechnung 2“ usw.
    > * Tags können nicht seitenübergreifend genutzt werden.
    > * Beschriften Sie Werte so, wie sie im Formular vorkommen. Versuchen Sie nicht, einen Wert mit zwei unterschiedlichen Tags in zwei Teile zu unterteilen. Ein Adressfeld sollte beispielsweise auch dann nur mit einem Tag beschriftet werden, wenn es über mehrere Zeilen verläuft.
    > * Fügen Sie in Ihre beschrifteten Felder keine Schlüssel ein, sondern nur die Werte.
    > * Die Tabellendaten sollten automatisch erkannt werden und sind in der fertigen JSON-Ausgabedatei im Abschnitt „pageResults“ enthalten. Wenn das Modell jedoch nicht alle Tabellendaten erkennen kann, können Sie ein Modell auch beschriften und trainieren, um Tabellen zu erkennen. Weitere Informationen finden Sie unter „Trainieren und Beschriften“ << Route zur Vorgehensweise >>
    > * Verwenden Sie die Schaltflächen rechts neben dem **+** -Zeichen, um Ihre Tags zu suchen, umzubenennen, neu anzuordnen und zu löschen.
    > * Um ein angewendetes Tag zu entfernen, ohne das Tag selbst zu löschen, wählen Sie das Tagrechteck in der Dokumentansicht aus und drücken die Taste ENTF.
    >

Führen Sie die obigen Schritte aus, um die fünf Formulare im Beispieldataset zu beschriften.

  :::image type="content" source="../media/label-tool/custom-1.jpg" alt-text="Beschriften Sie die Beispiele.":::

#### <a name="train-a-custom-model"></a>Trainieren eines benutzerdefinierten Modells

Wählen Sie im linken Bereich das Symbol „Trainieren“ aus, um die Seite „Training“ zu öffnen. Wählen Sie dann die Schaltfläche **Trainieren** aus, um mit dem Training des Modells zu beginnen. Sobald der Trainingsprozess abgeschlossen ist, werden folgende Informationen angezeigt:

* **Modell-ID**: Die ID des Modells, das erstellt und trainiert wurde. Jeder Trainingsaufruf erstellt ein neues Modell mit eigener ID. Kopieren Sie diese Zeichenfolge an einen sicheren Speicherort. Sie werden sie benötigen, wenn Sie Vorhersageaufrufe über die [REST-API](./client-library.md?pivots=programming-language-rest-api) oder [Clientbibliothek](./client-library.md) ausführen möchten.
* **Durchschnittliche Genauigkeit**: Die durchschnittliche Genauigkeit des Modells. Sie können die Modellgenauigkeit verbessern, indem Sie weitere Formulare beschriften und erneut ein Training ausführen, um ein neues Modell zu erstellen. Es wird empfohlen, zunächst fünf Formulare zu beschriften sowie die Ergebnisse zu analysieren und zu testen, um dann bei Bedarf weitere Formulare hinzuzufügen.
* Die Liste der Beschriftungen und die geschätzte Genauigkeit für jede Beschriftung.

    :::image type="content" source="../media/label-tool/custom-3.jpg" alt-text="Tool für die Trainingsansicht":::

    

#### <a name="analyze-a-custom-form"></a>Analysieren eines benutzerdefinierten Formulars

Wählen Sie links das Symbol für die Analyse (Glühbirne) aus, um Ihr Modell zu testen. Wählen Sie die Quelle „Lokale Datei“ aus. Suchen Sie nach einer Datei, und wählen Sie eine Datei aus dem Beispieldataset aus, das Sie im Testordner entzippt haben. Wählen Sie dann die Schaltfläche **Analyse ausführen** aus, um Schlüssel-Wert-Paare, Text- und Tabellenvorhersagen für das Formular abzurufen. Das Tool wendet Beschriftungen in Begrenzungsrahmen an und meldet die Konfidenz jeder Beschriftung.

   :::image type="content" source="../media/analyze.png" alt-text="Trainingsansicht":::

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie erfahren, wie Sie das Beispieltool für die Formularerkennung verwenden, um ein benutzerdefiniertes Modell zu gestalten, vorzudefinieren und zu trainieren sowie ein benutzerdefiniertes Formular mit manuell beschrifteten Daten zu analysieren. Jetzt können Sie das Clientbibliotheks-SDK oder die REST-API ausprobieren, um die Formularerkennung zu verwenden.

> [!div class="nextstepaction"]
> [Schnellstart: Untersuchen des Clientbibliotheks-SDKs und der REST-API der Formularerkennung](client-library.md)
