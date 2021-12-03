---
title: 'Schnellstart: Formularerkennung Studio | Vorschau'
titleSuffix: Azure Applied AI Services
description: Formular- und Dokumentverarbeitung, Datenextraktion und Analyse mit Formularerkennung Studio (Vorschau)
author: sanjeev3
manager: netahw
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 09/14/2021
ms.author: sajagtap
ms.custom: ignite-fall-2021
ms.openlocfilehash: dc4f54a6ab42089068c3db8b7fe64bfada59adf5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132290683"
---
# <a name="get-started-form-recognizer-studio--preview"></a>Erste Schritte: Formularerkennung Studio | Vorschau

>[!NOTE]
> Formularerkennung Studio befindet sich derzeit in der öffentlichen Vorschauphase. Dies bedeutet, dass einige Features unter Umständen nicht unterstützt werden oder nur eingeschränkt verwendbar sind. 

[Formularerkennung Studio (Vorschau)](https://formrecognizer.appliedai.azure.com/) ist ein Onlinetool für das grafische Erkunden und Kennenlernen der Features des Diensts „Formularerkennung“ und das Integrieren in Ihre Anwendungen. Steigen Sie ein, indem Sie die vortrainierten Modelle mit Beispieldokumenten oder Ihren eigenen Dokumenten erkunden. Erstellen Sie Projekte für die Entwicklung von benutzerdefinierten Formularmodellen, und verweisen Sie in Ihren Anwendungen darauf, indem Sie die Informationen zum [Python SDK (Vorschau)](try-v3-python-sdk.md) und in anderen Schnellstartanleitungen verwenden.

## <a name="migrating-from-the-sample-labeling-tool"></a>Migrieren vom Tool für die Beschriftung von Beispielen

Wenn Sie zuvor das [Tool für die Beschriftung von Beispielen](try-sample-label-tool.md) genutzt haben, überspringen Sie die Voraussetzungen, um [**sich bei der Studio-Vorschauversion anzumelden**](try-v3-form-recognizer-studio.md#sign-into-the-form-recognizer-studio-preview) und Ihr vorhandenes Azure-Konto sowie Formularerkennungs- oder Cognitive Services-Ressourcen mit Studio zu verwenden. 

Wenn Sie Ihre vorhandenen benutzerdefinierten Projekte zu Studio migrieren möchten, fahren Sie mit dem Abschnitt [**Erste Schritte mit dem benutzerdefinierten Modell**](try-v3-form-recognizer-studio.md#custom-model-basics) fort, um ein neues Projekt zu erstellen und es auf den gleichen Speicherort in Azure Blob Storage zu verweisen (vorausgesetzt Sie haben in Azure Zugriff darauf). Nachdem Sie ein neues Projekt konfiguriert haben, lädt Studio alle Dokumente und Zwischendateien zum Beschriften und Trainieren.

## <a name="minimum-prerequisites-for-new-users"></a>Mindestvoraussetzungen für neue Benutzer

* Ein aktives [**Azure-Konto**](https://azure.microsoft.com/free/cognitive-services/).  Falls Sie noch kein Konto haben, können Sie ein [**kostenloses Konto erstellen**](https://azure.microsoft.com/free/).
* Eine [**Formularerkennung**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) oder [**Cognitive Services Multi-Service**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) Ressource.

## <a name="additional-prerequisites-for-custom-projects"></a>Zusätzliche Voraussetzungen für benutzerdefinierte Projekte

Neben dem Azure-Konto und einer Formularerkennung- oder Cognitive Services-Ressource benötigen Sie Folgendes:

### <a name="azure-blob-storage-container"></a>Azure Blob Storage-Container

**Standardleistung**[ **Azure Blob Storage Konto**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Sie erstellen Container zum Speichern und Organisieren Ihrer Blobdaten unter Ihrem Speicherkonto. Wenn Sie nicht wissen, wie Sie ein Azure Speicherkonto mit einem Container erstellen können, befolgen Sie diese Schnellstarts:

  * [**Erstellen Sie ein Speicherkonto**](../../../storage/common/storage-account-create.md). Stellen Sie bei der Erstellung Ihres Speicherkontos sicher, dass Sie Leistung **Standard** im Feld **Instanzdetails -> Leistung** auswählen.
  * [**Erstellen Sie einen Container**](../../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container). Stellen Sie bei der Erstellung Ihres Containers das Feld **Öffentliche Zugriffsebene** auf **Container**(anonymer Lesezugriff für Container und Blobs) im dem Fenster **Neuer Container**.

### <a name="configure-cors"></a>Konfigurieren von CORS

Für Ihr Azure-Speicherkonto muss [CORS (Cross Origin Resource Sharing)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) konfiguriert werden, damit über Formularerkennung Studio auf das Konto zugegriffen werden kann. Zum Konfigurieren von CORS im Azure-Portal benötigen Sie Zugriff auf das Blatt „CORS“ Ihres Speicherkontos.

:::image type="content" source="../media/quickstarts/cors-updated-image.png" alt-text="Screenshot: CORS-Konfiguration für ein Speicherkonto":::

1. Wählen Sie das Blatt „CORS“ für das Speicherkonto aus.
2. Erstellen Sie zunächst einen neuen CORS-Eintrag im Blob-Dienst.
3. Legen Sie für **Allowed Origins** (Zulässige Ursprünge) den Wert **https://formrecognizer.appliedai.azure.com** fest.
4. Wählen Sie für **Zulässige Methoden** alle verfügbaren acht Optionen aus.
5. Genehmigen Sie die Angaben für **Zulässige Header** und **Verfügbar gemachte Header**, indem Sie in jedes Feld ein Sternchen (*) eingeben.
6. Legen Sie für **Max. Alter** 120 Sekunden oder einen anderen gültigen Wert ein.
7. Klicken Sie oben auf der Seite auf die Schaltfläche „Speichern“, um die Änderungen zu speichern.

CORS sollte jetzt für die Verwendung des Speicherkontos über Formularerkennung Studio konfiguriert sein.

### <a name="sample-documents-set"></a>Gruppe von Beispieldokumenten

1. Gehen Sie zum [Azure-Portal](https://ms.portal.azure.com/#home) und navigieren Sie wie folgt: **Ihr Speicherkonto** -> **Datenspeicher** -> **Container**

   :::image border="true" type="content" source="../media/sas-tokens/data-storage-menu.png" alt-text="Screenshot: Menü Datenspeicher im Azure-Portal.":::

1. Wählen Sie einen **Container** aus der Liste aus.

1. Wählen Sie **Hochladen** aus dem Menü am oberen Rand der Seite.

    :::image border="true" type="content" source="../media/sas-tokens/container-upload-button.png" alt-text="Screenshot: Schaltfläche zum Hochladen von Containern im Azure-Portal.":::

1. Das Fenster **Blob hochladen** wird angezeigt.

1. Wählen Sie Ihre Datei(en) zum Hochladen aus.

    :::image border="true" type="content" source="../media/sas-tokens/upload-blob-window.png" alt-text="Screenshot: Fenster Blob hochladen im Azure-Portal.":::

> [!NOTE]
> Standardmäßig werden von Studio Formulardokumente verwendet, die sich im Stammverzeichnis Ihres Containers befinden. Sie können aber auch Daten verwenden, die in Ordnern angeordnet sind, wenn Sie dies in den Schritten zum Erstellen des Projekts für benutzerdefinierte Formulare angeben. *Siehe* [**Organisieren Sie Ihre Daten in Unterordnern**](../build-training-data-set.md#organize-your-data-in-subfolders-optional)

## <a name="sign-into-the-form-recognizer-studio-preview"></a>Anmelden bei Formularerkennung Studio (Vorschau)

Navigieren Sie nach der Erfüllung der Voraussetzungen zu [Formularerkennung Studio (Vorschau)](https://formrecognizer.appliedai.azure.com).

1. Wählen Sie auf der Startseite von Studio ein Feature des Diensts „Formularerkennung“ aus.

1. Wählen Sie Ihr Azure-Abonnement, die Ressourcengruppe und die Ressource aus. (Sie können die Ressourcen jederzeit im obersten Menü in „Einstellungen“ ändern.)

1. Überprüfen und bestätigen Sie Ihre Auswahl.

:::image border="true" type="content" source="../media/quickstarts/form-recognizer-studio-get-started-v2.gif" alt-text="Formularerkennung Studio: Beispiel für erste Schritte":::

## <a name="layout"></a>Layout

In der Layoutansicht:

1. Wählen Sie den Befehl „Analysieren“ aus, um die Layoutanalyse für das Beispieldokument auszuführen, oder versuchen Sie es mit einem eigenen Dokument, indem Sie den Befehl „Hinzufügen“ verwenden.

1. Achten Sie auf den hervorgehobenen extrahierten Text, die Tabellensymbole, auf denen die Orte der extrahierten Tabellen angezeigt werden, und die hervorgehobenen Auswahlmarkierungen.

1. Verwenden Sie die Steuerelemente am unteren Bildschirmrand, um die Dokumentansicht zu vergrößern bzw. zu verkleinern und zu drehen.

1. Blenden Sie die Ebenen für den Text, die Tabellen und die Auswahlmarkierungen entsprechend ein bzw. aus, damit Sie sich jeweils auf eine Ebene konzentrieren können.

1. Sehen Sie sich im Ausgabeabschnitt auf der Registerkarte „Ergebnis“ die JSON-Ausgabe an, um sich mit dem Ausgabeformat des Diensts vertraut zu machen. Führen Sie den Kopiervorgang und den Download durch, um mit der Integration zu beginnen.

:::image border="true" type="content" source="../media/quickstarts/layout-get-started-v2.gif" alt-text="Formularerkennung: Layoutbeispiel":::

## <a name="prebuilt-models"></a>Vordefinierte Modelle

Sie können zwischen mehreren vordefinierten Modellen wählen, von denen jedes über mehrere unterstützte Felder verfügt. Welches Modell für den Analysevorgang verwendet wird, hängt vom Typ des zu analysierenden Dokuments ab. Hier sind die vordefinierten Modelle angegeben, die vom Dienst Formularerkennung derzeit unterstützt werden:

* [🆕 **Allgemeines Dokument:**](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=document) Analysieren und Extrahieren von Text, Tabellen, Strukturen, Schlüssel-Wert-Paaren und benannten Entitäten
* [**Rechnung:**](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=invoice) Extrahieren von Text, Auswahlmarkierungen, Tabellen, Schlüssel-Wert-Paaren und wichtigen Informationen aus Rechnungen
* [**Beleg**](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=receipt): Extrahieren von Text und wichtigen Informationen aus Belegen
* [**Ausweisdokument:**](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=idDocument) Extrahieren von Text und wichtigen Informationen aus Führerscheinen und Reisepässen
* [**Visitenkarte:**](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=businessCard) Extrahieren von Text und wichtigen Informationen aus Visitenkarten

Gehen Sie in der Ansicht mit den vordefinierten Modellen wie folgt vor:

1. Wählen Sie auf der Startseite von Studio eines der vordefinierten Modelle aus. In diesem Beispiel verwenden wir das Rechnungsmodell.

1. Wählen Sie den Befehl „Analysieren“ aus, um die Analyse für das Beispieldokument auszuführen, oder versuchen Sie es mit einer eigenen Rechnung, indem Sie den Befehl „Hinzufügen“ verwenden.

1. Achten Sie im Visualisierungsabschnitt auf die hervorgehobenen Felder und Werte sowie die Rechnungspositionen. Darüber hinaus werden der gesamte extrahierte Text und die Tabellen angezeigt.

1. Notieren Sie sich im Ausgabeabschnitt auf der Registerkarte „Felder“ die aufgelisteten Felder und Werte, und wählen Sie die Positionen aus, um diese in einem tabellenähnlichen Format anzuzeigen.

1. Sehen Sie sich im Ausgabeabschnitt auf der Registerkarte „Ergebnis“ die JSON-Ausgabe an, um sich mit dem Ausgabeformat des Diensts vertraut zu machen. Führen Sie den Kopiervorgang und den Download durch, um mit der Integration zu beginnen.

:::image border="true" type="content" source="../media/quickstarts/prebuilt-get-started-v2.gif" alt-text="Formularerkennung: Beispiel für vordefinierte Modelle":::

## <a name="custom-model-basics"></a>Grundlagen benutzerdefinierter Modelle

### <a name="getting-started"></a>Erste Schritte

Für die Erstellung benutzerdefinierter Modelle konfigurieren Sie zunächst Ihr Projekt:

1. Wählen Sie auf der Studio-Startseite das [Projekt für ein benutzerdefiniertes Formular](https://formrecognizer.appliedai.azure.com/studio/customform/projects) aus, um die entsprechende Startseite zu öffnen.

1. Verwenden Sie den Befehl „Projekt erstellen“, um den Konfigurations-Assistenten für neue Projekte zu starten.

1. Geben Sie Projektdetails ein, und wählen Sie das Azure-Abonnement und die -Ressource sowie den Azure Blob Storage-Container aus, in dem Ihre Daten enthalten sind.

1. Überprüfen und übermitteln Sie Ihre Einstellungen, um das Projekt zu erstellen.

:::image border="true" type="content" source="../media/quickstarts/1-custom-model-get-started-v2.gif" alt-text="Formularerkennung: Beispiel für erste Schritte für benutzerdefiniertes Projekt":::

### <a name="basic-flow"></a>Grundlegender Ablauf

Gehen Sie nach dem Schritt für die Projekterstellung in der Phase für das benutzerdefinierte Modell wie folgt vor:

1. Definieren Sie in der Bezeichnungsansicht die Bezeichnungen und die zugehörigen Typen, die Sie extrahieren möchten.

1. Wählen Sie den Text im Dokument und dann in der Dropdownliste oder im Bereich „Bezeichnungen“ die Bezeichnung aus.

1. Versehen Sie vier weitere Dokumente mit Bezeichnungen, damit Sie mindestens über fünf bezeichnete Dokumente verfügen.

1. Wählen Sie den Befehl „Trainieren“ aus, und geben Sie den Namen und die Beschreibung des Modells ein, um mit dem Trainieren Ihres benutzerdefinierten Modells zu beginnen.

1. Verwenden Sie nach der Fertigstellung des Modells den Befehl „Testen“, um eine Überprüfung mit Ihren Testdokumenten durchzuführen, und sehen Sie sich anschließend die Ergebnisse an.

:::image border="true" type="content" source="../media/quickstarts/2-custom-model-basic-steps-v2.gif" alt-text="Formularerkennung: Beispiel für einen einfachen Workflow für ein benutzerdefiniertes Projekt":::

### <a name="other-features"></a>Andere Funktionen

Zeigen Sie darüber hinaus Ihre gesamten Modelle an, indem Sie links die Registerkarte „Modelle“ verwenden. Wählen Sie in der Listenansicht Modelle aus, um die folgenden Aktionen durchzuführen:

1. Testen des Modells in der Listenansicht

1. Verwenden des Befehls „Löschen“, um nicht erforderliche Modelle zu löschen

1. Herunterladen von Modelldetails für die Offlineanzeige

1. Auswählen mehrerer Modelle und Zusammenfügen zu einem neuen Modell für die Verwendung in Ihren Anwendungen

## <a name="labeling-as-tables"></a>Bezeichnen als Tabellen

Beim Erstellen Ihrer benutzerdefinierten Modelle müssen Sie unter Umständen Datensammlungen aus Ihren Dokumenten extrahieren. Diese können in verschiedenen Formaten angezeigt werden. Verwenden von Tabellen als visuelles Muster:

* Dynamische oder variable Anzahl von Werten (Zeilen) für eine bestimmte Gruppe von Feldern (Spalten)

* Bestimmte Sammlung mit Werten für eine bestimmte Gruppe von Feldern (Spalten und/oder Zeilen)

### <a name="label-as-dynamic-table"></a>Bezeichnen als dynamische Tabelle

Verwenden Sie dynamische Tabellen, um die variable Anzahl von Werten (Zeilen) für eine bestimmte Gruppe von Feldern (Spalten) zu extrahieren:

1. Fügen Sie die neue Typbezeichnung „Table“ (Tabelle) hinzu, wählen Sie den Typ „Dynamische Tabelle“ aus, und geben Sie Ihrer Bezeichnung einen Namen.

1. Fügen Sie die Anzahl von Spalten (Feldern) und Zeilen (für Daten) hinzu, die Sie benötigen.

1. Wählen Sie den Text auf Ihrer Seite und dann die Zelle aus, die dem Text zugewiesen werden soll. Wiederholen Sie diesen Vorgang für alle Zeilen und Spalten auf allen Seiten Ihrer gesamten Dokumente.

:::image border="true" type="content" source="../media/quickstarts/custom-tables-dynamic.gif" alt-text="Formularerkennung: Beispiel für die Bezeichnung als dynamische Tabelle":::

### <a name="label-as-fixed-table"></a>Bezeichnen als feste Tabelle

Verwenden Sie feste Tabellen, um eine bestimmte Sammlung mit Werten für eine bestimmte Gruppe von Feldern (Spalten und/oder Zeilen) zu extrahieren:

1. Erstellen Sie die neue Typbezeichnung „Table“ (Tabelle), wählen Sie den Typ „Feste Tabelle“ aus, und geben Sie Ihrer Bezeichnung einen Namen.

1. Fügen Sie die Anzahl von Spalten und Zeilen hinzu, die Sie für die beiden Gruppen von Feldern benötigen.

1. Wählen Sie den Text auf Ihrer Seite und dann die Zelle aus, die dem Text zugewiesen werden soll. Wiederholen Sie diesen Vorgang für andere Dokumente.

:::image border="true" type="content" source="../media/quickstarts/custom-tables-fixed.gif" alt-text="Formularerkennung: Beispiel für die Bezeichnung als feste Tabelle":::

## <a name="labeling-for-signature-detection"></a>Bezeichnen für die Signaturerkennung

Gehen Sie wie folgt vor, um eine Bezeichnung für die Signaturerkennung hinzuzufügen:

1. Erstellen Sie in der Bezeichnungsansicht die neue Typbezeichnung „Signature“ (Signatur), und geben Sie ihr einen Namen.

1. Verwenden Sie den Befehl „Region“, um an der erwarteten Position der Signatur einen rechteckigen Bereich zu erstellen.

1. Wählen Sie den gezeichneten Bereich und die Typbezeichnung „Signature“ (Signatur) aus, um sie Ihrem gezeichneten Bereich zuzuweisen. Wiederholen Sie diesen Vorgang für andere Dokumente.

:::image border="true" type="content" source="../media/quickstarts/custom-signature.gif" alt-text="Formularerkennung: Beispiel für die Bezeichnung zur Signaturerkennung":::

## <a name="next-steps"></a>Nächste Schritte

* Befolgen Sie unser [**Migrationshandbuch zur Formularerkennung v3.0**](../v3-migration-guide.md), um die Unterschiede zur vorherigen Version der REST-API kennen zu lernen.
* Erkunden Sie unsere [**Schnellstartanleitungen zum Vorschau-SDK**](try-v3-python-sdk.md), um die Previewfunktionen in Ihren Anwendungen mithilfe der neuen SDKs auszuprobieren.
* Sehen Sie sich unsere [**Schnellstartanleitungen zur Vorschau-REST-API**](try-v3-rest-api.md) an, um die Previewfunktionen mithilfe der neuen REST-API auszuprobieren.

[Erste Schritte: Formularerkennung Studio (Vorschau)](https://formrecognizer.appliedai.azure.com)
