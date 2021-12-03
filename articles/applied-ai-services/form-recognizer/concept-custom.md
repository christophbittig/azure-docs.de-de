---
title: Formularerkennung – benutzerdefinierte und zusammengesetzte Modelle
titleSuffix: Azure Applied AI Services
description: Erfahren Sie, wie Sie benutzerdefinierte und zusammengesetzte Modelle für die Formularerkennung erstellen, verwenden und verwalten.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 825bd073759d27f789aac454eb2b384bbf065c4b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027650"
---
# <a name="form-recognizer-custom-and-composed-models"></a>Formularerkennung – benutzerdefinierte und zusammengesetzte Modelle

Die Formularerkennung verwendet fortschrittliche ML-Technologie, um Informationen in Dokumentbildern zu erkennen, zu extrahieren und sie in Form einer strukturierten JSON-Ausgabe zurück zu geben. Mit der Formularerkennung können Sie eigenständige benutzerdefinierte Modelle trainieren oder benutzerdefinierte Modelle vereinen, um zusammengesetzte Modelle zu erstellen.

* **Benutzerdefinierte Modelle:** Benutzerdefinierte Modelle für die Formularerkennung ermöglichen es Ihnen, Daten in unternehmensspezifischen Formularen und Dokumenten zu analysieren und zu extrahieren. Benutzerdefinierte Modelle werden speziell für Ihre individuellen Daten und Anwendungsfälle trainiert.

* **Zusammengesetzte Modelle**. Ein zusammengesetztes Modell wird erstellt, indem eine Sammlung benutzerdefinierter Modelle verwendet und zu einem einzigen Modell zusammengesetzt wird, das Ihre Formulartypen umfasst. Wenn ein Dokument an ein zusammengesetztes Modell übermittelt wird, führt der Dienst einen Klassifizierungsschritt aus, um zu entscheiden, welchem benutzerdefiniertem Modell das zur Analyse eingereichte Formular genau entspricht.

:::image type="content" source="media/studio/analyze-custom.png" alt-text="Screenshot: Fenster „analyze-a-custom-form“ (benutzerdefiniertes Formular analysieren) der Formularerkennung":::

## <a name="what-is-a-custom-model"></a>Was ist ein benutzerdefiniertes Modell?

Ein benutzerdefiniertes Modell ist ein ML-Programm, das trainiert wurde, um Formularfelder in Ihren individuellen Inhalten zu erkennen und Schlüssel-Wert-Paare und Tabellendaten zu extrahieren. Für den Einstieg benötigen Sie nur fünf Beispiele desselben Formulartyps, und Ihr benutzerdefiniertes Modell kann mit oder ohne bezeichnete Datasets trainiert werden.

## <a name="what-is-a-composed-model"></a>Was ist ein zusammengesetztes Modell?

In einem zusammengesetzten Modell werden verschiedene benutzerdefinierten Modelle kombiniert und dann mit einer einzigen Modell-ID aufgerufen. Dies ist nützlich, wenn Sie mehrere Modelle trainiert haben und diese gruppieren möchten, um ähnliche Formulartypen zu analysieren. Ihr zusammengesetztes Modell kann z. B. benutzerdefinierte Modelle einschließen, die trainiert wurden, um Ihre Bestellungsdokumente für Material, Ausrüstung und Möbel zu analysieren. Anstatt das entsprechende Modell manuell auszuwählen, können Sie ein zusammengesetztes Modell verwenden, um das zutreffende benutzerdefinierte Modell für jede Analyse und Extraktion zu bestimmen.

## <a name="development-options"></a>Entwicklungsoptionen

Die folgenden Ressourcen werden von der Azure-Formularerkennung v2.1 unterstützt:

| Funktion | Ressourcen |
|----------|-------------------------|
|**Benutzerdefiniertes Modell**| <ul><li>[**Formularerkennungstool für die Bezeichnung**](https://fott-2-1.azurewebsites.net)</li><li>[**REST-API**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-forms-with-a-custom-model)</li><li>[**Clientbibliothek/SDK**](quickstarts/try-sdk-rest-api.md)</li><li>[**Docker-Container für Formularerkennung**](containers/form-recognizer-container-install-run.md?tabs=custom#run-the-container-with-the-docker-compose-up-command)</li></ul>|

Die folgenden Ressourcen werden von der Azure-Formularerkennung v3.0 unterstützt:

| Funktion | Ressourcen |
|----------|-------------|
|**Benutzerdefiniertes Modell**| <ul><li>[**Formularerkennung Studio**](https://fott-2-1.azurewebsites.net)</li><li>[**REST-API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li></ul>| 

### <a name="try-form-recognizer"></a>Formularerkennung ausprobieren

Erfahren Sie, wie Daten aus Ihren spezifischen oder individuellen Dokumenten mithilfe benutzerdefinierter Modelle extrahiert werden. Sie benötigen Folgendes:

* Azure-Abonnement – Sie können ein [kostenloses Abonnement erstellen](https://azure.microsoft.com/free/cognitive-services/)

* Eine [Formularerkennungsinstanz](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) im Azure-Portal Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst auszuprobieren. Wählen Sie nach der Bereitstellung Ihrer Ressource **Zu Ressource wechseln** aus, um Ihren API-Schlüssel und -Endpunkt abzurufen.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Screenshot: Schlüssel und Endpunktspeicherort im Azure-Portal":::

#### <a name="form-recognizer-studio-preview"></a>Formularerkennung Studio (Vorschau)

> [!NOTE]
> Formularerkennung Studio ist mit der Vorschau-API (v3.0) verfügbar.

1. Wählen Sie auf der Startseite von Azure-Formularerkennung Studio **Benutzerdefiniertes Formular** aus.

1. Wählen Sie unter **Meine Projekte** die Option **+ Projekt erstellen** aus.

1. Füllen Sie die Felder mit den **Projektdetails** aus.

1. **Konfigurieren Sie die Dienstressource**.

1. Fügen Sie **Speicherkonto** und **Blobcontainer** hinzu, um eine **Verbindung mit Ihrer Trainingsdatenquelle herzustellen**.

1. **Überprüfen und erstellen** Sie Ihr Projekt.

1. Zum Erstellen und Testen Ihres benutzerdefinierten Modells steht Ihnen eine Reihe von Beispielen zur Verfügung.

    > [!div class="nextstepaction"]
    > [Ausprobieren von Formularerkennung Studio](https://formrecognizer.appliedai.azure.com/studio/customform/projects)

#### <a name="sample-labeling-tool"></a>Beispiel-Beschriftungstool

Sie benötigen einen Satz von mindestens sechs Formularen desselben Typs. Diese Daten verwenden Sie zum Trainieren des Modells und zum Testen eines Formulars. Sie können unser [Beispieldataset](https://go.microsoft.com/fwlink/?linkid=2090451) verwenden. Laden Sie die Datei *sample_data.zip* herunter, extrahieren Sie sie, und laden Sie den Inhalt in Ihren Azure Blob Storage-Container hoch.

Gehen Sie auf der Benutzeroberfläche der Formularerkennung folgendermaßen vor:

1. Wählen Sie auf der Startseite des Tool für die Beschriftung von Beispielen die Option **Use Custom to train a model with labels and get key value pairs** (Benutzerdefinierte Option zum Trainieren eines Modells mit Beschriftungen und Abrufen von Schlüssel-Wert-Paaren verwenden) aus.

      :::image type="content" source="media/label-tool/fott-use-custom.png" alt-text="Screenshot: FOTTtool-Auswahl der benutzerdefinierten Option.":::

1. Wählen Sie im nächsten Fenster die Option **Neues Projekt** aus:

    :::image type="content" source="media/label-tool/fott-new-project.png" alt-text="Screenshot: FOTTtools wählen neues Projekt aus.":::

    Ausführlichere Anweisungen *finden* Sie in unserem Schnellstart zum [Tool für die Beschriftung von Beispielen](quickstarts/try-sample-label-tool.md).

    > [!div class="nextstepaction"]
    > [Ausprobieren des Beispiel-Beschriftungstools](https://fott-2-1.azurewebsites.net/projects/create)

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

  > [!TIP]
  > **Trainingsdaten**
  >
  >* Verwenden Sie nach Möglichkeit textbasierte PDF-Dokumente anstelle von bildbasierten Dokumenten. Gescannte PDF-Dateien werden als Bilder behandelt.
  > * Verwenden Sie als ausgefüllte Formulare Exemplare, in denen alle Felder ausgefüllt sind.
  > * Verwenden Sie Formulare mit verschiedenen Werten in jedem Feld.
  >* Wenn Ihre Formularbilder eine mäßige Qualität aufweisen, verwenden Sie ein größeres Dataset (beispielsweise 10–15 Bilder).

> [!NOTE]
> Das [Tool für die Beschriftung von Beispielen](https://fott-2-1.azurewebsites.net/) unterstützt nicht das BMP-Dateiformat. Dies ist eine Einschränkung des Tools, nicht des Formularerkennungsdiensts.

## <a name="supported-languages-and-locales"></a>Unterstützte Sprachen und Regionen

 Die Vorschauversion der Formularerkennung bietet zusätzliche Sprachunterstützung für benutzerdefinierte Modelle. *Eine* vollständige Liste der unterstützten handschriftlichen und gedruckten Texte finden Sie in unserem [Sprach-Support](language-support.md#layout-and-custom-model).

## <a name="form-recognizer-preview-v30"></a>Formularerkennung Vorschau v3.0

 Formularerkennung v3.0 (Vorschau) bietet eine Reihe neuer Funktionen und Möglichkeiten:

* **Die benutzerdefinierte Modell-API (v3.0)** unterstützt die Erkennung von Signaturen für benutzerdefinierte Formulare. Beim Trainieren benutzerdefinierter Modelle können Sie bestimmte Felder als Signaturen angeben.  Wenn ein Dokument mit Ihrem benutzerdefinierten Modell analysiert wird, gibt es an, ob eine Signatur erkannt wurde.

* Erfahren Sie, wie Sie die Vorschauversion in Ihren Anwendungen und Workflows verwenden können, indem Sie unseren [**Formularerkennung v3.0-Migrationsleitfaden**](v3-migration-guide.md) befolgen.

* Erkunden Sie die [**REST-API (Vorschau)**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument), um mehr über die Vorschauversion und neue Funktionen zu erfahren.

### <a name="try-signature-detection"></a>Signaturerkennung ausprobieren

1. Bauen Sie Ihr Trainingsdataset auf.

1. Navigieren Sie zu [Formularerkennung Studio,](https://formrecognizer.appliedai.azure.com/studio) und wählen Sie unter „Benutzerdefinierte Modelle“ **Benutzerdefiniertes Formular** aus:

    :::image type="content" source="media/label-tool/select-custom-form.png" alt-text="Screenshot: Auswählen einer benutzerdefinierten Formularseite in Formularerkennung Studio.":::

1. Folgen Sie dem Workflow, um ein neues Projekt zu erstellen:

1. Befolgen Sie die Eingabeanforderungen für benutzerdefinierte Modelle.

1. Bezeichnen Sie Ihre Dokumente. Für Signaturfelder wird die Verwendung der Bezeichnung von Regionen empfohlen, um die Genauigkeit zu verbessern.

1. Bezeichnen Sie Ihre Dokumente. Für Signaturfelder wird die Verwendung der Bezeichnung von Regionen empfohlen, um die Genauigkeit zu verbessern.

    :::image type="content" source="media/label-tool/signature-label-region-too.png" alt-text="Screenshot: Feld „Bezeichnungssignatur“":::

Nachdem Ihr Trainingssatz bezeichnet wurde, können Sie Ihr benutzerdefiniertes Modell trainieren und zum Analysieren von Dokumenten verwenden. Die Signaturfelder geben an, ob eine Signatur erkannt wurde.

## <a name="next-steps"></a>Nächste Schritte

* Sehen Sie sich eine Schnellstartanleitung zur Formularerkennung an:

  > [!div class="nextstepaction"]
  > [Formularerkennung: Schnellstart](quickstarts/try-sdk-rest-api.md)

* Erkunden Sie unsere REST-API:

    > [!div class="nextstepaction"]
    > [Formularerkennung-API Version 2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)
