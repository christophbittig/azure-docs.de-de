---
title: Extrahieren von Informationen in Excel mithilfe von Power Automate
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie mit Erkennung benannter Entitäten und Power Automate Excel-Text extrahieren können, ohne Code schreiben zu müssen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: article
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-ner, ignite-fall-2021
ms.openlocfilehash: a53ff3e69842bcd2f42e8463f3f84638f512450f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095126"
---
# <a name="extract-information-in-excel-using-named-entity-recognitionner-and-power-automate"></a>Extrahieren von Informationen in Excel mit Erkennung benannter Entitäten (NER) und Power Automate 

In diesem Tutorial erstellen Sie einen Power Automate-Flow zum Extrahieren von Text aus einem Excel-Arbeitsblatt, ohne Code schreiben zu müssen. 

Dieser Flow klassifiziert die für einen Apartmentkomplex gemeldeten Probleme in einem Arbeitsblatt in zwei Kategorien: sanitäre und andere Probleme. Außerdem werden die Namen und Telefonnummern der Mieter extrahiert, die sie gemeldet haben. Schließlich fügt der Flow diese Informationen an das Excel-Arbeitsblatt an. 

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Verwenden von Power Automate zum Erstellen eines Flows
> * Hochladen von Excel-Daten aus OneDrive for Business
> * Extrahieren von Text aus Excel und Senden dieses Texts zur Erkennung benannter Entitäten (NER) 
> * Verwenden der Informationen aus der API zum Aktualisieren eines Excel-Arbeitsblatts

## <a name="prerequisites"></a>Voraussetzungen

- Ein Microsoft Azure-Konto. [Erstellen Sie ein kostenloses Konto](https://azure.microsoft.com/free/cognitive-services/), oder [melden Sie sich an](https://portal.azure.com/).
- Eine Sprachressource. Wenn Sie noch nicht über eine verfügen, können Sie [im Azure-Portal eine erstellen](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) und den Free-Tarif verwenden, um dieses Tutorial abzuschließen.
- [Schlüssel und Endpunkt](../../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource), die bei der Registrierung für Sie generiert wurden
- Ein Arbeitsblatt mit Mieterproblemen. Beispieldaten werden auf GitHub bereitgestellt.
- Microsoft 365 mit OneDrive for Business

## <a name="add-the-excel-file-to-onedrive-for-business"></a>Hinzufügen der Excel-Datei zu OneDrive for Business

Laden Sie die Excel-Beispieldatei von [GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/TextAnalytics/sample-data/ReportedIssues.xlsx) herunter. Diese Datei muss in Ihrem OneDrive for Business-Konto gespeichert werden.

:::image type="content" source="../media/tutorials/excel/example-data.png" alt-text="Beispiele aus der Excel-Datei" lightbox="../media/tutorials/excel/example-data.png":::

Die Probleme werden im Rohtext angezeigt. Wir verwenden das NER-Feature, um den Namen und die Telefonnummer der Person zu extrahieren. Anschließend sucht der Flow in der Beschreibung nach dem Wort „plumbing“ (sanitärbezogen), um die Probleme zu kategorisieren. 

## <a name="create-a-new-power-automate-workflow"></a>Erstellen eines neuen Power Automate-Workflows

Wechseln Sie zur Website von [Power Automate](https://preview.flow.microsoft.com/), und melden Sie sich an. Klicken Sie dann auf **Erstellen** und **Geplanter Flow**.

:::image type="content" source="../media/tutorials/excel/flow-creation.png" alt-text="Der Bildschirm zur Erstellung des Workflows" lightbox="../media/tutorials/excel/flow-creation.png":::

Initialisieren Sie auf der Seite **Geplanten Cloud-Flow erstellen** den Flow mit den folgenden Feldern:

|Feld |Wert  |
|---------|---------|
|**Flowname**     | **Geplante Überprüfung** oder ein anderer Name.         |
|**Wird gestartet**     |  Geben Sie das aktuelle Datum und die Uhrzeit ein.       |
|**Wiederholen alle**     | **1 Stunde**        |

## <a name="add-variables-to-the-flow"></a>Hinzufügen von Variablen zum Flow

Erstellen Sie Variablen, die die der Excel-Datei hinzugefügten Informationen darstellen. Klicken Sie auf **Neuer Schritt**, und suchen Sie nach **Variable initialisieren**. Führen Sie dies vier Mal aus, um vier Variablen zu erstellen.

:::image type="content" source="../media/tutorials/excel/initialize-variables.png" alt-text="Der Schritt zum Initialisieren von Variablen" lightbox="../media/tutorials/excel/initialize-variables.png":::


Fügen Sie den erstellten Variablen die folgenden Informationen hinzu. Sie stellen die Spalten der Excel-Datei dar. Wenn Variablen reduziert sind, können Sie darauf klicken, um sie zu erweitern.

| Aktion |Name   | type | Wert |
|---------|---------|---|---|
| Initialisieren einer Variablen | var_person | String | Person |
| Variable initialisieren 2 | var_phone | String | Rufnummer |
| Variable initialisieren 3 | var_plumbing | String | plumbing |
| Variable initialisieren 4 | var_other | String | Sonstige | 

:::image type="content" source="../media/tutorials/excel/flow-variables.png" alt-text="In Flowvariablen enthaltene Informationen" lightbox="../media/tutorials/excel/flow-variables.png":::

## <a name="read-the-excel-file"></a>Lesen der Excel-Datei

Klicken Sie auf **Neuer Schritt**, geben Sie **Excel** ein, und wählen Sie dann in der Liste der Aktionen **In Tabelle vorhandene Zeilen auflisten** aus.

:::image type="content" source="../media/tutorials/excel/list-excel-rows.png" alt-text="Hinzufügen von Excel-Zeilen zum Flow" lightbox="../media/tutorials/excel/list-excel-rows.png":::

Sie fügen dem Flow die Excel-Datei hinzu, indem Sie die Felder in dieser Aktion ausfüllen. Für dieses Tutorial muss die Datei in OneDrive for Business hochgeladen werden.

:::image type="content" source="../media/tutorials/excel/list-excel-rows-options.png" alt-text="Ausfüllen der Excel-Zeilen im Flow" lightbox="../media/tutorials/excel/list-excel-rows-options.png":::

Klicken Sie auf **Neuer Schritt**, und fügen Sie eine Aktion **Auf alle anwenden** hinzu.

:::image type="content" source="../media/tutorials/excel/add-apply-action.png" alt-text="Hinzufügen einer „Auf alle anwenden“-Aktion" lightbox="../media/tutorials/excel/add-apply-action.png":::

Klicken Sie auf **Ausgabe von vorherigen Schritten auswählen**. Wählen Sie im angezeigten Feld mit dynamischem Inhalt **Wert** aus.

:::image type="content" source="../media/tutorials/excel/select-output.png" alt-text="Auswählen der Ausgabe von der Excel-Datei" lightbox="../media/tutorials/excel/select-output.png":::

## <a name="send-a-request-for-entity-recognition"></a>Senden einer Anforderung für die Entitätserkennung

Erstellen Sie im Azure-Portal eine [Sprachressource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics), sofern noch nicht geschehen.

### <a name="create-a-language-service-connection"></a>Erstellen einer Sprachdienstverbindung

Klicken Sie in **Auf alle anwenden** auf **Aktion hinzufügen**. Wechseln Sie im Azure-Portal zur Seite **Schlüssel und Endpunkt** Ihrer Sprachressource, und rufen Sie den Schlüssel und den Endpunkt Ihrer Sprachressource ab.

Geben Sie in Ihrem Flow die folgenden Informationen ein, um eine neue Sprachverbindung zu erstellen.

> [!NOTE]
> Wenn Sie bereits eine Sprachverbindung erstellt haben und die Verbindungsdetails ändern möchten, klicken Sie in der rechten oberen Ecke auf die Auslassungspunkte und dann auf **+ Neue Verbindung hinzufügen**.

| Feld           | Wert                                                                                                             |
|-----------------|-------------------------------------------------------------------------------------------------------------------|
| Verbindungsname | Ein Name für die Verbindung mit Ihrer Sprachressource. Beispiel: `TAforPowerAutomate`. |
| Kontoschlüssel     | Der Schlüssel für Ihre Language-Ressource.                                                                                   |
| Website-URL        | Der Endpunkt für Ihre Sprachressource.                                                       |

:::image type="content" source="../media/tutorials/excel/add-credentials.png" alt-text="Hinzufügen von Anmeldeinformationen für die Sprachressource zum Flow" lightbox="../media/tutorials/excel/add-credentials.png":::


## <a name="extract-the-excel-content"></a>Extrahieren des Excel-Inhalts 

Nachdem die Verbindung erstellt wurde, suchen Sie nach **Textanalyse**, und wählen Sie **Erkennung benannter Entitäten** aus. Damit werden Informationen aus der Spalte „description“ (Beschreibung) des Problems extrahiert.

:::image type="content" source="../media/tutorials/excel/extract-info.png" alt-text="Extrahieren der Entitäten aus der Excel-Tabelle" lightbox="../media/tutorials/excel/extract-info.png":::

Klicken Sie in das Feld **Text**, und wählen Sie im angezeigten Fenster mit dynamischem Inhalt **Beschreibung** aus. Geben Sie `en` als Sprache und einen eindeutigen Namen als Dokument-ID ein (möglicherweise müssen Sie auf **Erweiterte Optionen anzeigen** klicken).

:::image type="content" source="../media/tutorials/excel/description-from-dynamic-content.png" alt-text="Abrufen des Beschreibungsspaltentexts aus der Excel-Tabelle" lightbox="../media/tutorials/excel/description-from-dynamic-content.png":::


Klicken Sie in **Auf alle anwenden** auf **Aktion hinzufügen**, und erstellen Sie eine weitere Aktion **Auf alle anwenden**. Klicken Sie in das Textfeld, und wählen Sie im angezeigten Fenster mit dynamischem Inhalt **Dokumente** aus.

:::image type="content" source="../media/tutorials/excel/apply-to-each-documents.png" alt-text="Erstellen einer weiteren „Auf alle anwenden“-Aktion." lightbox="../media/tutorials/excel/apply-to-each-documents.png":::


## <a name="extract-the-person-name"></a>Extrahieren des Namens der Person

Als nächstes suchen Sie den Entitätstyp „Person“ in der NER-Ausgabe. Klicken Sie in **Auf alle anwenden 2** auf **Aktion hinzufügen**, und erstellen Sie eine weitere Aktion **Auf alle anwenden**. Klicken Sie in das Textfeld, und wählen Sie im angezeigten Fenster mit dynamischem Inhalt **Entitäten** aus.

:::image type="content" source="../media/tutorials/excel/add-apply-action-2.png" alt-text="Suchen der Entität „Person“ in der NER-Ausgabe" lightbox="../media/tutorials/excel/add-apply-action-2.png":::


Klicken Sie in der neu erstellten Aktion **Auf alle anwenden 3** auf **Aktion hinzufügen**, und fügen Sie das Steuerelement **Bedingung** hinzu.

:::image type="content" source="../media/tutorials/excel/create-condition.png" alt-text="Hinzufügen des Steuerelements „Bedingung“ zur Aktion „Auf alle anwenden 3“" lightbox="../media/tutorials/excel/create-condition.png":::


Klicken Sie im Fenster „Bedingung“ auf das erste Textfeld. Suchen Sie im Fenster mit dynamischem Inhalt nach **Kategorie**, und wählen Sie diese Option aus.

:::image type="content" source="../media/tutorials/excel/choose-entities-value.png" alt-text="Hinzufügen der Kategorie zum Steuerelement „Bedingung“" lightbox="../media/tutorials/excel/choose-entities-value.png":::


Vergewissern Sie sich, dass das zweite Feld auf **ist gleich** festgelegt ist. Wählen Sie dann das dritte Feld aus, und suchen Sie im Fenster mit dynamischem Inhalt nach `var_person`. 

:::image type="content" source="../media/tutorials/excel/choose-variable-value.png" alt-text="Hinzufügen der Variablen „Person“" lightbox="../media/tutorials/excel/choose-variable-value.png":::


Geben Sie in der Bedingung **Falls ja** den Begriff „Excel“ ein, und wählen Sie dann **Zeile aktualisieren** aus.

:::image type="content" source="../media/tutorials/excel/yes-column-action.png" alt-text="Aktualisieren der „Ja“-Bedingung" lightbox="../media/tutorials/excel/yes-column-action.png":::

Geben Sie die Excel-Informationen ein, und aktualisieren Sie die Felder **Schlüsselspalte**, **Schlüsselwert** und **PersonName**. Dadurch wird der von der API erkannte Name an das Excel-Arbeitsblatt angehängt. 

:::image type="content" source="../media/tutorials/excel/yes-column-action-options.png" alt-text="Hinzufügen der Excel-Informationen" lightbox="../media/tutorials/excel/yes-column-action-options.png":::

## <a name="get-the-phone-number"></a>Abrufen der Telefonnummer

Minimieren Sie die Aktion **Auf alle anwenden 3**, indem Sie auf den Namen klicken. Fügen Sie dann wie zuvor eine weitere **Auf alle anwenden**-Aktion zu **Auf alle anwenden 2** hinzu. Sie erhält den Namen **Auf alle anwenden 4**. Wählen Sie das Textfeld aus, und fügen Sie **Entitäten** als Ausgabe für diese Aktion hinzu. 

:::image type="content" source="../media/tutorials/excel/add-apply-action-phone.png" alt-text="Hinzufügen der Entitäten aus der NER-Ausgabe zu einer anderen „Auf alle anwenden“-Aktion" lightbox="../media/tutorials/excel/add-apply-action-phone.png":::

Fügen Sie in **Auf alle anwenden 4** ein Steuerelement **Bedingung** hinzu. Es erhält den Namen **Bedingung 2**. Suchen Sie im ersten Textfeld nach **Kategorien**, und fügen Sie diese Option aus dem Fenster mit dynamischem Inhalt hinzu. Vergewissern Sie sich, dass das mittlere Feld auf **ist gleich** festgelegt ist. Geben Sie dann im rechten Textfeld `var_phone` ein. 

:::image type="content" source="../media/tutorials/excel/condition-2-options.png" alt-text="Hinzufügen eines zweiten Steuerelements „Bedingung“" lightbox="../media/tutorials/excel/condition-2-options.png":::

Fügen Sie in der Bedingung **Falls ja** eine Aktion **Zeile aktualisieren** hinzu. Geben Sie dann die Informationen wie oben für die Spalte „Telefonnummern“ des Excel-Arbeitsblatts ein. Dadurch wird die von der API erkannte Telefonnummer an das Excel-Arbeitsblatt angehängt. 

:::image type="content" source="../media/tutorials/excel/condition-2-yes-column.png" alt-text="Hinzufügen der Excel-Informationen zur zweiten „Falls ja“-Bedingung" lightbox="../media/tutorials/excel/condition-2-yes-column.png":::

## <a name="get-the-plumbing-issues"></a>Abrufen von sanitären Problemen

Minimieren Sie **Auf alle anwenden 4**, indem Sie auf den Namen klicken. Erstellen Sie dann eine weitere Aktion **Auf alle anwenden** in der übergeordneten Aktion. Wählen Sie das Textfeld aus, und fügen Sie **Entitäten** als Ausgabe für diese Aktion aus dem Fenster mit dynamischem Inhalt hinzu. 

:::image type="content" source="../media/tutorials/excel/add-apply-action-plumbing.png" alt-text="Erstellen einer weiteren „Auf alle anwenden“-Aktion" lightbox="../media/tutorials/excel/add-apply-action-plumbing.png":::

Anschließend prüft der Flow, ob die Problembeschreibung aus der Zeile im Excel-Arbeitsblatt das Wort „plumbing“ (sanitärbezogen) enthält. Ist dies der Fall, wird in der Spalte „IssueType“ der Eintrag „plumbing“ (sanitärbezogen) hinzugefügt. Andernfalls wird „other“ (Sonstiges) eingetragen.

Fügen Sie innerhalb der Aktion **Auf alle anwenden 4** ein Steuerelement **Bedingung** hinzu. Es erhält den Namen **Bedingung 3**. Suchen Sie im ersten Textfeld mithilfe des Fensters mit dynamischem Inhalt in der Excel-Datei nach **Description** (Beschreibung). Vergewissern Sie sich, dass das mittlere Feld **enthält** lautet. Suchen Sie dann im rechten Textfeld nach `var_plumbing`, und wählen Sie diese Option aus. 

:::image type="content" source="../media/tutorials/excel/condition-3-options.png" alt-text="Erstellen eines neuen Steuerelements „Bedingung“" lightbox="../media/tutorials/excel/condition-3-options.png":::

Klicken Sie in der Bedingung **Falls ja** auf **Aktion hinzufügen**, und wählen Sie **Zeile aktualisieren** aus. Geben Sie dann die Informationen wie zuvor ein. Wählen Sie in der Spalte „IssueType“ die Option `var_plumbing` aus. Dadurch wird die Bezeichnung „plumbing“ (sanitärbezogen) auf die Zeile angewandt.

Klicken Sie in der Bedingung **Falls nein** auf **Aktion hinzufügen**, und wählen Sie **Zeile aktualisieren** aus. Geben Sie dann die Informationen wie zuvor ein. Wählen Sie in der Spalte „IssueType“ die Option `var_other` aus. Dadurch wird die Bezeichnung „other“ (andere) auf die Zeile angewandt.

:::image type="content" source="../media/tutorials/excel/plumbing-issue-condition.png" alt-text="Hinzufügen von Informationen zu beiden Bedingungen" lightbox="../media/tutorials/excel/plumbing-issue-condition.png":::

## <a name="test-the-workflow"></a>Testen des Workflows

Klicken Sie in der rechten oberen Ecke des Bildschirms auf **Speichern** und dann auf **Testen**. Wählen Sie unter **Flow testen** die Option **Manuell** aus. Klicken Sie dann auf **Testen** und **Flow ausführen**.

Die Excel-Datei wird in Ihrem OneDrive-Konto aktualisiert. Sie sieht wie folgt aus.

:::image type="content" source="../media/tutorials/excel/updated-excel-sheet.png" alt-text="Testen des Workflows und Anzeigen der Ausgabe" lightbox="../media/tutorials/excel/updated-excel-sheet.png":::

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Aufrufen der Erkennung benannter Entitäten (NER) mithilfe der REST-API oder Clientbibliothek](../quickstart.md)
