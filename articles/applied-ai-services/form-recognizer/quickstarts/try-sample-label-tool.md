---
title: 'Schnellstart: Beschriften von Formularen, Trainieren eines Modells und Analysieren von Formularen mithilfe des Tools für die Beschriftung von Beispielen – Formularerkennung'
titleSuffix: Azure Applied AI Services
description: In dieser Schnellstartanleitung lernen Sie, wie Sie das Formularerkennungstool für die Beschriftung von Beispielen verwenden, um Formulardokumente manuell zu beschriften. Danach trainieren Sie ein benutzerdefiniertes Modell für die Dokumentverarbeitung mit den beschrifteten Dokumenten und verwenden das Modell, um Schlüssel-Wert-Paare zu extrahieren.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/02/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-may-2021, ignite-fall-2021
keywords: Verarbeiten von Dokumenten
ms.openlocfilehash: 5848fff7fab23d561bf79f1afbc2d21d4d7c3875
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026771"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
<!-- markdownlint-disable MD029 -->
# <a name="get-started-with-the-sample-labeling-tool"></a>Erste Schritte mit dem Tool für die Beschriftung von Beispielen

Die Azure-Formularerkennung ist ein cloudbasierter Azure Applied AI-Dienst, der Machine Learning-Modelle verwendet, um Formularfelder, Text und Tabellen aus Ihren Dokumenten zu extrahieren und zu analysieren. Sie können die Formularerkennung verwenden, um die Datenverarbeitung in Anwendungen und Workflows zu automatisieren, datengesteuerte Strategien zu verbessern und Suchfunktionen für Dokumente zu erweitern. 

Das Formularerkennungstool für die Beschriftung von Beispielen ist ein Open-Source-Tool, mit dem Sie die neuesten Features der Formularerkennungs- und OCR-Dienste (Optical Character Recognition) von Azure testen können:

* [Analysieren von Dokumenten mit der Layout-API](#analyze-layout). Testen Sie die Layout-API, um Text, Tabellen, Auswahlmarkierungen und Struktur aus Dokumenten zu extrahieren.

* [Analysieren von Dokumenten mit einem vordefinierten Modell](#analyze-using-a-prebuilt-model). Beginnen Sie mit einem vordefinierten Modell, um Daten aus Rechnungen, Belegen, Identitätsdokumenten oder Visitenkarten zu extrahieren.

* [Trainieren und Analysieren eines benutzerdefinierten Formulars](#train-a-custom-form-model). Verwenden Sie ein benutzerdefiniertes Modell, um Daten aus Dokumenten zu extrahieren, die spezifisch für verschiedene Geschäftsdaten und Anwendungsfälle sind.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Azure-Abonnement – Sie können ein [kostenloses Abonnement erstellen](https://azure.microsoft.com/free/cognitive-services/).

* Eine Cognitive Services- oder Formularerkennungsressource. Wenn Sie über Ihr Azure-Abonnement verfügen, können Sie im Azure-Portal eine Formularerkennungsressource mit [einem Dienst](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) oder [mehreren Diensten](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) erstellen, um Ihren Schlüssel und Endpunkt zu erhalten. Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

    > [!TIP]
    > Erstellen Sie eine Cognitive Services-Ressource, wenn Sie planen, über einen einzelnen Endpunkt bzw. Schlüssel auf mehrere Cognitive Services-Instanzen zuzugreifen. Erstellen Sie eine Formularerkennungsressource, falls nur auf die Formularerkennung zugegriffen werden soll. Beachten Sie hierbei, dass Sie eine Ressource mit einem einzelnen Dienst benötigen, falls Sie die [Azure Active Directory-Authentifizierung](/azure/active-directory/authentication/overview-authentication) nutzen möchten.

## <a name="create-a-form-recognizer-resource"></a>Erstellen einer Formularerkennungsressource

[!INCLUDE [create resource](../includes/create-resource.md)]

 :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="Screenshot: Schlüssel und Endpunktspeicherort im Azure-Portal":::

## <a name="analyze-using-a-prebuilt-model"></a>Analysieren mithilfe eines vordefinierten Modells

Die Formularerkennung bietet verschiedene vordefinierte Modelle zur Auswahl. Jedes Modell verfügt über einen eigenen Satz unterstützter Felder. Welches Modell für den Analysevorgang verwendet wird, hängt vom Typ des zu analysierenden Dokuments ab. Dies sind die vordefinierten Modelle, die vom Formularerkennungsdienst derzeit unterstützt werden:

* [**Rechnung**](../concept-invoice.md): Extrahieren von Text, Auswahlmarkierungen, Tabellen, Schlüssel-Wert-Paaren und wichtigen Informationen aus Rechnungen
* [**Beleg**](../concept-receipt.md): Extrahieren von Text und wichtigen Informationen aus Belegen
* [**Ausweisdokument**](../concept-id-document.md): Extrahieren von Text und wichtigen Informationen aus Führerscheinen und Reisepässen
* [**Visitenkarte**](../concept-business-card.md): Extrahieren von Text und wichtigen Informationen aus Visitenkarten

1. Wechseln Sie zum [Beispieltool für die Formularerkennung](https://fott-2-1.azurewebsites.net/).

1. Wählen Sie auf der Startseite des Beispieltools die Option **Use prebuilt model to get data** (Vordefiniertes Modell zum Abrufen von Daten verwenden) aus.

    :::image type="content" source="../media/label-tool/prebuilt-1.jpg" alt-text="Analysieren der Ergebnisse des Formularerkennungslayouts":::

1. Wählen Sie im Dropdownfenster den **Form Type** (Formulartyp) aus, den Sie analysieren möchten.

1. Wählen Sie aus den folgenden Optionen eine URL zu der Datei aus, die Sie analysieren möchten:

    * [**Beispielrechnungsdokument**](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/forms/Invoice_1.pdf).
    * [**Beispiel-ID-Dokument**](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/DriverLicense.png).
    * [**Beispielbild eines Belegs**](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg).
    * [**Beispielbild einer Visitenkarte**](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/business_cards/business-card-english.jpg).

1. Fügen Sie im Feld **Source: URL** (Quelle: URL) die ausgewählte URL ein, und wählen Sie die Schaltfläche **Fetch** (Abrufen) aus.

1. Fügen Sie in das Feld **Form recognizer service endpoint** (Dienstendpunkt der Formularerkennung) den Endpunkt ein, den Sie mit Ihrem Abonnement für die Formularerkennung abgerufen haben.

1. Fügen Sie in das Feld **API key** (API-Schlüssel) den Abonnementschlüssel ein, den Sie aus Ihrer Ressource für die Formularerkennung abgerufen haben.

    :::image type="content" source="../media/fott-select-form-type.png" alt-text="Screenshot: Dropdownfenster zum Auswählen des Formulartyps":::

1. Wählen Sie **Run Analysis** (Analyse ausführen) aus. Das Formularerkennungstool für die Beschriftung von Beispielen ruft die Analyze Prebuilt-API auf und analysiert das Dokument.

1. Zeigen Sie die Ergebnisse an. Sehen Sie sich die extrahierten Schlüssel-Wert-Paare, die Positionen, den extrahierten markierten Text und die erkannten Tabellen an.

    :::image type="content" source="../media/label-tool/prebuilt-2.jpg" alt-text="Analysieren der Ergebnisse des Rechnungsmodells der Formularerkennung":::

1. Laden Sie die JSON-Ausgabedatei herunter, um die ausführlichen Ergebnisse anzuzeigen.

    * Der Knoten „readResults“ enthält jede Textzeile mit der Platzierung des zugehörigen Begrenzungsrahmens auf der Seite.
    * Der Knoten „selectionMarks“ zeigt jede Auswahlmarkierung (Kontrollkästchen, Optionsfeld) und ihren Status („ausgewählt“ oder „nicht ausgewählt“).
    * Der Abschnitt „pageResults“ enthält die extrahierten Tabellen. Für jede Tabelle werden der Text-, Zeilen- und Spaltenindex, die Zeilen- und Spaltenaufteilung, der Begrenzungsrahmen und mehr extrahiert.
    * Das Feld „documentResults“ enthält Informationen zu Schlüssel-Wert-Paaren und Positionen für die relevantesten Teile des Dokuments.

## <a name="analyze-layout"></a>Analysieren des Layouts

Die Layout-API der Azure-Formularerkennung extrahiert Text, Tabellen, Auswahlmarkierungen und Strukturinformationen aus Dokumenten (PDF, TIFF) und Bildern (JPG, PNG, BMP).

1. Wechseln Sie zum [Beispieltool für die Formularerkennung](https://fott-2-1.azurewebsites.net/).

1. Wählen Sie auf der Startseite des Beispieltools die Option **Use Layout to get text, tables and selection marks** (Layout zum Abrufen von Text, Tabellen und Auswahlmarkierungen verwenden) aus.

     :::image type="content" source="../media/label-tool/layout-1.jpg" alt-text="Verbindungseinstellungen für das Layouttool der Formularerkennung":::.

1. Fügen Sie in das Feld **Form recognizer service endpoint** (Dienstendpunkt der Formularerkennung) den Endpunkt ein, den Sie mit Ihrem Abonnement für die Formularerkennung abgerufen haben.

1. Fügen Sie in das Feld **API key** (API-Schlüssel) den Abonnementschlüssel ein, den Sie aus Ihrer Ressource für die Formularerkennung abgerufen haben.

1. Fügen Sie im Feld **Source: URL** (Quelle: URL) die URL `https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/layout-page-001.jpg` ein, und wählen Sie die Schaltfläche **Fetch** (Abrufen) aus.

1. Wählen Sie **Run Layout** (Layout ausführen) aus. Das Formularerkennungstool für die Beschriftung von Beispielen ruft die Analyze Layout-API auf und analysiert das Dokument.

    :::image type="content" source="../media/fott-layout.png" alt-text="Screenshot: Dropdownfenster für Layout":::

1. Anzeigen der Ergebnisse – Sehen Sie sich den hervorgehobenen extrahierten Text, die erkannten Auswahlmarkierungen und die erkannten Tabellen an.

    :::image type="content" source="../media/label-tool/layout-3.jpg" alt-text="Verbindungseinstellungen für das Formularerkennungstool":::.

1. Laden Sie die JSON-Ausgabedatei herunter, um die ausführlichen Layoutergebnisse anzuzeigen.
     * Der Knoten `readResults` enthält jede Textzeile mit der Platzierung des zugehörigen Begrenzungsrahmens auf der Seite.
     * Der Knoten `selectionMarks` zeigt jede Auswahlmarkierung (Kontrollkästchen, Optionsfeld) und ihren Status – `selected` oder `unselected` – an.
     * Der Abschnitt `pageResults` enthält die extrahierten Tabellen. Für jede Tabelle werden der Text-, Zeilen- und Spaltenindex, die Zeilen- und Spaltenaufteilung, der Begrenzungsrahmen und mehr extrahiert.

## <a name="train-a-custom-form-model"></a>Trainieren eines benutzerdefinierten Formularmodells

Trainieren Sie ein benutzerdefiniertes Modell, um Daten aus Formularen und Dokumenten zu analysieren und zu extrahieren, die für Ihr Unternehmen spezifisch sind. Bei der API handelt es sich um ein maschinelles Lernprogramm, das darauf trainiert ist, Formularfelder innerhalb Ihrer unterschiedlichen Inhalte zu erkennen und Schlüssel-Wert-Paare sowie Tabellendaten zu extrahieren. Am Anfang benötigen Sie mindestens fünf Beispiele desselben Formulartyps, und Ihr benutzerdefiniertes Modell kann mit oder ohne beschriftete Datasets trainiert werden.

### <a name="prerequisites-for-training-a-custom-form-model"></a>Voraussetzungen für das Trainieren eines benutzerdefinierten Formularmodells

* Ein Azure Storage-Blobcontainer, der eine Reihe von Trainingsdaten enthält. Stellen Sie sicher, dass alle Trainingsdokumente im selben Format vorliegen. Wenn Ihre Formulare unterschiedliche Formate aufweisen, sortieren Sie sie in Unterordner für jeweils ein Format. Für dieses Projekt können Sie unser [Beispieldataset](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample_data_without_labels.zip) verwenden.

* Konfigurieren von CORS

    Für Ihr Azure-Speicherkonto muss [CORS (Cross Origin Resource Sharing)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) konfiguriert werden, damit über Formularerkennung Studio auf das Konto zugegriffen werden kann. Zum Konfigurieren von CORS im Azure-Portal benötigen Sie Zugriff auf das Blatt „CORS“ Ihres Speicherkontos.

    :::image type="content" source="../media/quickstarts/storage-cors-example.png" alt-text="Screenshot: CORS-Konfiguration für ein Speicherkonto":::

    1. Wählen Sie das Blatt „CORS“ für das Speicherkonto aus.

    1. Erstellen Sie zunächst einen neuen CORS-Eintrag im Blob-Dienst.

    1. Legen Sie für **Allowed Origins** (Zulässige Ursprünge) den Wert **https://formrecognizer.appliedai.azure.com** fest.

    1. Wählen Sie für **Zulässige Methoden** alle verfügbaren acht Optionen aus.

    1. Genehmigen Sie die Angaben für **Zulässige Header** und **Verfügbar gemachte Header**, indem Sie in jedes Feld ein Sternchen (*) eingeben.

    1. Legen Sie für **Max. Alter** 120 Sekunden oder einen anderen gültigen Wert ein.

    1. Klicken Sie oben auf der Seite auf die Schaltfläche „Speichern“, um die Änderungen zu speichern.

    CORS sollte jetzt für die Verwendung des Speicherkontos über Formularerkennung Studio konfiguriert sein.

### <a name="use-the-sample-labeling-tool"></a>Verwenden des Tools für die Beschriftung von Beispielen

1. Wechseln Sie zum [Beispieltool für die Formularerkennung](https://fott-2-1.azurewebsites.net/).

1. Wählen Sie auf der Startseite des Beispieltools die Option **Use Custom to train a model with labels and get key value pairs** (Benutzerdefinierte Option zum Trainieren eines Modells mit Beschriftungen und Abrufen von Schlüssel-Wert-Paaren verwenden) aus.

    :::image type="content" source="../media/label-tool/custom-1.jpg" alt-text="Trainieren eines benutzerdefinierten Modells":::

1. Wählen Sie **New Project** (Neues Projekt) aus.

    :::image type="content" source="../media/fott-new-project.png" alt-text="Screenshot: Eingabeaufforderung zum Auswählen eines neuen Projekts":::

#### <a name="create-a-new-project"></a>Erstellen eines neuen Projekts

Konfigurieren Sie die Felder **Project Settings** (Projekteinstellungen) mit den folgenden Werten:

1. **Display Name** (Anzeigename). Geben Sie dem Projekt einen Namen.

1. **Security Token** (Sicherheitstoken). Jedes Projekt generiert automatisch ein Sicherheitstoken, das zum Verschlüsseln und Entschlüsseln von vertraulichen Projekteinstellungen verwendet werden kann. Sie finden die Sicherheitstoken in den Anwendungseinstellungen, indem Sie unten auf der linken Navigationsleiste das Zahnradsymbol auswählen.

1. **Source connection** (Quellverbindung). Das Tool für die Beschriftung von Beispielen stellt eine Verbindung mit einer Quelle (Ihre hochgeladenen Originalformulare) und einem Ziel (erstellte Beschriftungen und Ausgabedaten) her. Verbindungen können projektübergreifend eingerichtet und freigegeben werden. Dabei wird ein erweiterbares Anbietermodell verwendet, sodass Sie ganz einfach neue Anbieter von Quellen und Zielen hinzufügen können.

    * Erstellen Sie eine neue Verbindung, und wählen Sie die Schaltfläche **Add Connection** (Verbindung hinzufügen) aus. Geben Sie die folgenden Werte in die Felder ein:

    > [!div class="checklist"]
    >
    > * **Display Name** (Anzeigename). Vergeben Sie einen Namen für die Verbindung.
    > * **Beschreibung**. Fügen Sie eine kurze Beschreibung hinzu.
    > * **SAS URL** (SAS-URL). Fügen Sie die Shared Access Signature-URL (SAS) Ihres Azure Blob Storage-Containers ein.

    * Navigieren Sie zu Ihrer Speicherressource im Azure-Portal, und wählen Sie die Registerkarte **Speicher-Explorer** aus, um die SAS-URL für Ihre benutzerdefinierten Modelltrainingdaten abzurufen. Wechseln Sie zum Container, klicken Sie mit der rechten Maustaste auf diesen, und wählen Sie **Shared Access Signature abrufen** aus. Achten Sie darauf, die SAS für den Container abzurufen, nicht für das Speicherkonto. Stellen Sie sicher, dass die Berechtigungen **Lesen**, **Schreiben**, **Löschen** und **Auflisten** aktiviert sind, und klicken Sie anschließend auf **Erstellen**. Kopieren Sie den Wert im Abschnitt **URL** dann an einen temporären Speicherort. Er muss das Format `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` aufweisen.

       :::image type="content" source="../media/quickstarts/get-sas-url.png" alt-text="SAS-Speicherort":::

1. **Folder Path** (Ordnerpfad) (optional).  Wenn sich Ihre Quellformulare in einem Ordner im Blobcontainer befinden, geben Sie den Ordnernamen an.

1. **URI des Formularerkennungsdiensts**: Die URL Ihres Formularerkennungs-Endpunkts.

1. **API Key** (API-Schlüssel). Der Schlüssel Ihres Abonnements für die Formularerkennung.

1. **API version** (API-Version). Behalten Sie den Standardwert bei (v2.1).

1. **Beschreibung** (optional). Beschreiben Sie Ihr Projekt.

    :::image type="content" source="../media/label-tool/connections.png"  alt-text="Verbindungseinstellungen":::

#### <a name="label-your-forms"></a>Beschriften Ihrer Formulare

  :::image type="content" source="../media/label-tool/new-project.png"  alt-text="Neue Projektseite":::

Wenn Sie ein Projekt erstellen oder öffnen, wird das Hauptfenster des Beschriftungs-Editors geöffnet. Der Beschriftungs-Editor besteht aus drei Teilen:

* Ein Vorschaubereich, dessen Größe angepasst werden kann und der eine scrollbare Liste mit Formularen aus der Quellverbindung enthält.
* Der Hauptbereich des Editors, in dem Sie Beschriftungen anwenden können.
* Der Bearbeitungsbereich des Editors, in dem Sie Beschriftungen ändern, sperren, neu anordnen und löschen können.

##### <a name="identify-text-and-tables"></a>Identifizieren von Text und Tabellen

Wählen Sie im linken Bereich **OCR in allen Dateien ausführen** aus, um Text- und Tabellenlayoutinformationen für jedes Dokument abzurufen. Das Beschriftungstool zeichnet einen Begrenzungsrahmen um jedes Textelement.

Das Beschriftungstool zeigt außerdem, welche Tabellen automatisch extrahiert wurden. Wählen Sie das Tabellen-/Rastersymbol auf der linken Seite des Dokuments aus, um die extrahierte Tabelle anzuzeigen. Da der Tabelleninhalt automatisch extrahiert wird, werden wir ihn nicht beschriften, sondern verlassen uns auf die automatisierte Extraktion.

  :::image type="content" source="../media/label-tool/table-extraction.png" alt-text="Tabellenvisualisierung im Tool für die Beschriftung von Beispielen":::

##### <a name="apply-labels-to-text"></a>Anwenden von Beschriftungen auf Text

Als nächstes erstellen Sie Tags (Beschriftungen) und wenden sie auf die Textelemente an, die das Modell analysieren soll. Beachten Sie, dass das Dataset für die Beispielbeschriftung bereits beschriftete Felder enthält. Wir fügen ein weiteres Feld hinzu.

Verwenden Sie den Tag-Editor-Bereich, um ein neues Tag zu erstellen, das identifiziert werden soll:

1. Wählen Sie **+** (Pluszeichen) aus, um ein neues Tag zu erstellen.

1. Geben Sie als Namen des Tags „Total“ ein.

1. Drücken Sie die **EINGABETASTE**, um das Tag zu speichern.

1. Wählen Sie im Hauptbereich des Editors den Gesamtwert in den markierten Textelementen aus.

1. Wählen Sie das Tag „Total“ aus, das auf den Wert angewendet werden soll, oder drücken Sie die entsprechende Taste auf der Tastatur. Die Zifferntasten sind als Schnellzugriffstasten für die ersten zehn Tags zugewiesen. Sie können die Beschriftungen mithilfe der nach oben und unten weisenden Pfeilsymbole im Bearbeitungsbereich neu anordnen.

    > [!Tip]
    > Beachten Sie beim Beschriften Ihrer Formulare die folgenden Tipps:
    >
    > * Sie können auf jedes ausgewählte Element nur ein Tag anwenden.
    >
    > * Jedes Tag kann nur einmal pro Seite angewendet werden. Wenn ein Wert in demselben Formular mehrfach erscheint, sollten Sie für jede Instanz andere Tags erstellen. Beispiel: „rechnung 1“, „rechnung 2“ usw.
    > * Tags können nicht seitenübergreifend genutzt werden.
    > * Beschriften Sie Werte so, wie sie im Formular vorkommen. Versuchen Sie nicht, einen Wert mit zwei unterschiedlichen Tags in zwei Teile zu unterteilen. Ein Adressfeld sollte beispielsweise auch dann nur mit einem Tag beschriftet werden, wenn es über mehrere Zeilen verläuft.
    > * Fügen Sie in Ihre beschrifteten Felder keine Schlüssel ein, sondern nur die Werte.
    > * Die Tabellendaten sollten automatisch erkannt werden und sind in der fertigen JSON-Ausgabedatei im Abschnitt „pageResults“ enthalten. Wenn das Modell jedoch nicht alle Tabellendaten erkennt, können Sie auch Beschriftungen hinzufügen und ein Modell speziell für die Erkennung von Tabellen trainieren. Weitere Informationen finden Sie unter [Trainieren eines benutzerdefinierten Modells | Beschriften von Formularen](../label-tool.md#label-your-forms).
    > * Verwenden Sie die Schaltflächen rechts neben dem **+** -Zeichen, um Ihre Tags zu suchen, umzubenennen, neu anzuordnen und zu löschen.
    > * Um ein angewendetes Tag zu entfernen, ohne das Tag selbst zu löschen, wählen Sie das Tagrechteck in der Dokumentansicht aus und drücken die Taste ENTF.
    >

1. Wiederholen Sie die obigen Schritte, um alle fünf Formulare im Beispieldataset zu beschriften.

  :::image type="content" source="../media/label-tool/custom-1.jpg" alt-text="Beschriften Sie die Beispiele.":::

#### <a name="train-a-custom-model"></a>Trainieren eines benutzerdefinierten Modells

Wählen Sie im linken Bereich das Symbol „Trainieren“ aus, um die Seite „Training“ zu öffnen. Wählen Sie dann die Schaltfläche **Trainieren** aus, um mit dem Training des Modells zu beginnen. Sobald der Trainingsprozess abgeschlossen ist, werden folgende Informationen angezeigt:

* **Modell-ID**: Die ID des Modells, das erstellt und trainiert wurde. Jeder Trainingsaufruf erstellt ein neues Modell mit eigener ID. Kopieren Sie diese Zeichenfolge an einen sicheren Speicherort. Sie werden sie benötigen, wenn Sie Vorhersageaufrufe über die [REST-API](./try-sdk-rest-api.md?pivots=programming-language-rest-api) oder [Clientbibliothek](./try-sdk-rest-api.md) ausführen möchten.

* **Durchschnittliche Genauigkeit**: Die durchschnittliche Genauigkeit des Modells. Sie können die Modellgenauigkeit verbessern, indem Sie weitere Formulare beschriften und erneut ein Training ausführen, um ein neues Modell zu erstellen. Es wird empfohlen, zunächst fünf Formulare zu beschriften sowie die Ergebnisse zu analysieren und zu testen, um dann bei Bedarf weitere Formulare hinzuzufügen.
* Die Liste der Beschriftungen und die geschätzte Genauigkeit für jede Beschriftung.

    :::image type="content" source="../media/label-tool/custom-3.jpg" alt-text="Tool für die Trainingsansicht":::

#### <a name="analyze-a-custom-form"></a>Analysieren eines benutzerdefinierten Formulars

1. Wählen Sie links das Symbol für die **Analyse** (Glühbirne) aus, um Ihr Modell zu testen. 

1. Wählen Sie **Local file** (Lokale Datei) aus, und suchen Sie im Beispieldataset, das Sie im Testordner entpackt haben, nach einer Datei. 

1. Wählen Sie dann die Schaltfläche **Run Analysis** (Analyse ausführen) aus, um Schlüssel-Wert-Paare, Text- und Tabellenvorhersagen für das Formular abzurufen. Das Tool wendet Beschriftungen in Begrenzungsrahmen an und meldet die Konfidenz jeder Beschriftung.

   :::image type="content" source="../media/analyze.png" alt-text="Trainingsansicht":::

Das ist alles! Sie haben gelernt, wie Sie das Beispieltool der Formularerkennung für vordefinierte, layoutbasierte und benutzerdefinierte Modelle verwenden. Sie haben auch gelernt, wie Sie ein benutzerdefiniertes Formular mit manuell beschrifteten Daten analysieren. Jetzt können Sie ein Clientbibliothek-SDK oder eine REST-API für die Formularerkennung ausprobieren.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Schnellstart: Untersuchen des Clientbibliotheks-SDKs und der REST-API der Formularerkennung](../quickstarts/get-started-sdk-rest-api.md)
