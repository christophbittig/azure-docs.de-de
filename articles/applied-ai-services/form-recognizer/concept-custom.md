---
title: Formularerkennung – benutzerdefinierte und zusammengesetzte Modelle
titleSuffix: Azure Applied AI Services
description: Erfahren Sie, wie Sie benutzerdefinierte und zusammengesetzte Modelle für die Formularerkennung erstellen, verwenden und verwalten.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 181d96d2e722d7f9b79c47285bab417bf0337133
ms.sourcegitcommit: af303268d0396c0887a21ec34c9f49106bb0c9c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2021
ms.locfileid: "129754670"
---
# <a name="form-recognizer-custom-and-composed-models"></a>Formularerkennung – benutzerdefinierte und zusammengesetzte Modelle

Die Formularerkennung verwendet fortschrittliche ML-Technologie, um Informationen in Dokumentbildern zu erkennen, zu extrahieren und sie in Form einer strukturierten JSON-Ausgabe zurück zu geben. Mit der Formularerkennung können Sie eigenständige benutzerdefinierte Modelle trainieren oder benutzerdefinierte Modelle vereinen, um zusammengesetzte Modelle zu erstellen.

* **Benutzerdefinierte Modelle:** Benutzerdefinierte Modelle für die Formularerkennung ermöglichen es Ihnen, Daten in unternehmensspezifischen Formularen und Dokumenten zu analysieren und zu extrahieren. Benutzerdefinierte Modelle werden speziell für Ihre individuellen Daten und Anwendungsfälle trainiert.

* **Zusammengesetzte Modelle**. Ein zusammengesetztes Modell wird erstellt, indem eine Sammlung benutzerdefinierter Modelle verwendet und zu einem einzigen Modell zusammengesetzt wird, das Ihre Formulartypen umfasst. Wenn ein Dokument an ein zusammengesetztes Modell übermittelt wird, führt der Dienst einen Klassifizierungsschritt aus, um zu entscheiden, welchem benutzerdefiniertem Modell das zur Analyse eingereichte Formular genau entspricht.

:::image type="content" source="media/analyze.png" alt-text="Screenshot: Fenster „analyze-a-custom-form“ (benutzerdefiniertes Formular analysieren) der Formularerkennung":::


## <a name="what-is-a-custom-model"></a>Was ist ein benutzerdefiniertes Modell?

Ein benutzerdefiniertes Modell ist ein ML-Programm, das trainiert wurde, um Formularfelder in Ihren individuellen Inhalten zu erkennen und Schlüssel-Wert-Paare und Tabellendaten zu extrahieren. Für den Einstieg benötigen Sie nur fünf Beispiele desselben Formulartyps, und Ihr benutzerdefiniertes Modell kann mit oder ohne bezeichnete Datasets trainiert werden.

## <a name="what-is-a-composed-model"></a>Was ist ein zusammengesetztes Modell?

In einem zusammengesetzten Modell werden verschiedene benutzerdefinierten Modelle kombiniert und dann mit einer einzigen Modell-ID aufgerufen. Dies ist nützlich, wenn Sie mehrere Modelle trainiert haben und diese gruppieren möchten, um ähnliche Formulartypen zu analysieren. Ihr zusammengesetztes Modell kann z. B. benutzerdefinierte Modelle einschließen, die trainiert wurden, um Ihre Bestellungsdokumente für Material, Ausrüstung und Möbel zu analysieren. Anstatt das entsprechende Modell manuell auszuwählen, können Sie ein zusammengesetztes Modell verwenden, um das zutreffende benutzerdefinierte Modell für jede Analyse und Extraktion zu bestimmen.

## <a name="try-form-recognizer-studio-preview"></a>Formularerkennung Studio ausprobieren (Vorschau)

* Formularerkennung Studio ist mit der Vorschau-API (v3.0) verfügbar.

* Analysieren Sie Formulare eines bestimmten oder eindeutigen Typs mit unserer Funktion „benutzerdefiniertes Formular“ für Formularerkennung Studio:

> [!div class="nextstepaction"]
> [Ausprobieren von Formularerkennung Studio](https://formrecognizer.appliedai.azure.com/studio/customform/projects)

## <a name="try-form-recognizer-sample-labeling-tool"></a>Tool zum Bezeichnen von Beispielen für die Formularerkennung ausprobieren

Sie können sehen, wie Daten aus benutzerdefinierten Formularen extrahiert werden, indem Sie unser Tool für die Bezeichnung von Beispielen ausprobieren. Sie benötigen Folgendes:

* Azure-Abonnement – Sie können ein [kostenloses Abonnement erstellen](https://azure.microsoft.com/free/cognitive-services/)

* Eine [Formularerkennungsinstanz](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) im Azure-Portal. Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst auszuprobieren. Klicken Sie nach der Bereitstellung Ihrer Ressource auf **Zu Ressource wechseln**, um Ihren API-Schlüssel und Endpunkt abzurufen.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Screenshot: Schlüssel und Endpunktspeicherort im Azure-Portal.":::

> [!div class="nextstepaction"]
> [Testen](https://fott-2-1.azurewebsites.net/projects/create)

In der Benutzeroberfläche Formularerkennung:

1. Wählen Sie **„Benutzerdefiniert verwenden“, um ein Modell mit Bezeichnungen zu trainieren und Schlüssel-Wert-Paare zu erhalten**.

      :::image type="content" source="media/label-tool/fott-use-custom.png" alt-text="Screenshot: FOTTtool-Auswahl der benutzerdefinierten Option.":::

1. Wählen Sie im nächsten Fenster die Option **Neues Projekt** aus:

    :::image type="content" source="media/label-tool/fott-new-project.png" alt-text="Screenshot: FOTTtools wählen neues Projekt aus.":::

## <a name="input-requirements"></a>Eingabeanforderungen

* Die besten Ergebnisse erzielen Sie, wenn Sie pro Dokument ein deutliches Foto oder einen hochwertigen Scan einreichen.
* Unterstützte Dateiformate: JPEG, PNG, BMP, TIFF und PDF (in Text eingebettet oder gescannt). In Text eingebettete PDF-Dateien sind am besten geeignet, um die Möglichkeit von Fehlern beim Extrahieren und Auffinden von Zeichen auszuschließen.
* Bei PDF und TIFF können bis zu 2000 Seiten verarbeitet werden (bei einem kostenlosen Abonnement werden nur die ersten beiden Seiten verarbeitet).
* Die Dateigröße muss weniger als 50 MB betragen.
* Bei Bildern müssen die Abmessungen zwischen 50 × 50 Pixel und 10.000 × 10.000 Pixel liegen.
* Die PDF-Abmessungen sind bis zu 17 x 17 Zoll, was dem Papierformat Legal oder A3 entspricht, oder kleiner.
* Der Gesamtumfang der Trainingsdaten beträgt 500 Seiten oder weniger.
* Wenn Ihre PDFs passwortgeschützt sind, müssen Sie die Sperre vor der Einreichung aufheben.
* Für unüberwachtes Lernen (ohne gelabelte Daten):
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
> Das [Probenbeschriftungswerkzeug](https://fott-2-1.azurewebsites.net/) unterstützt nicht das BMP-Dateiformat. Dies ist eine Einschränkung des Tools, nicht des Formularerkennungsdienstes.

## <a name="supported-languages-and-locales"></a>Unterstützte Sprachen und Regionen

 Die Vorschauversion der Formularerkennung bietet zusätzliche Sprachunterstützung für benutzerdefinierte Modelle. *Eine* vollständige Liste der unterstützten handschriftlichen und gedruckten Texte finden Sie in unserem [Sprach-Support](language-support.md#layout-and-custom-model).

## <a name="form-recognizer-preview-v30"></a>Formularerkennung Vorschau v3.0

 Formularerkennung v3.0 (Vorschau) bietet eine Reihe neuer Funktionen und Möglichkeiten:

* **Die benutzerdefinierte Modell-API (v3.0)** unterstützt die Erkennung von Signaturen für benutzerdefinierte Formulare. Beim Trainieren benutzerdefinierter Modelle können Sie bestimmte Felder als Signaturen angeben.  Wenn ein Dokument mit Ihrem benutzerdefinierten Modell analysiert wird, gibt es an, ob eine Signatur erkannt wurde.

* Erfahren Sie, wie Sie die Vorschauversion in Ihren Anwendungen und Workflows verwenden können, indem Sie unseren [**Formularerkennung v3.0 Migrationsleitfaden**](v3-migration-guide.md) befolgen.

* Erkunden Sie unsere [**REST-API (Vorschau),** ](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) um mehr über die Vorschauversion und neue Funktionen zu erfahren.

### <a name="try-signature-detection"></a>Signaturerkennung ausprobieren

1. [**Bauen Sie Ihr Trainingsdataset auf**](build-training-data-set.md#custom-model-input-requirements).

1. Navigieren Sie zum [**Formularerkennungs-Tool für die Bezeichnung von Beispielen**](https://fott-preview-private.azurewebsites.net), und wählen Sie **„Benutzerdefiniert verwenden“ aus, um ein Modell mit Bezeichnungen zu trainieren und Schlüssel-Wert-Paare zu erhalten**:

    :::image type="content" source="media/label-tool/fott-use-custom.png" alt-text="Screenshot: FOTTtools-Auswahl der benutzerdefinierten Option.":::

1. Wählen Sie im nächsten Fenster die Option **Neues Projekt** aus:

    :::image type="content" source="media/label-tool/fott-new-project.png" alt-text="Screenshot: FOTTtools wählen neues Projekt aus.":::

1. Befolgen Sie die [**Eingabeanforderungen für benutzerdefinierte Modelle**](build-training-data-set.md#custom-model-input-requirements).

1. Erstellen Sie eine Bezeichnung mit dem Typ **Signatur**.

1. **Bezeichnen Sie Ihre Dokumente**.  Für Signaturfelder wird die Verwendung der Bezeichnung von Regionen empfohlen, um die Genauigkeit zu verbessern.

1. Nachdem Ihr Trainingssatz bezeichnet wurde, können Sie **Ihr benutzerdefiniertes Modell trainieren** und zum Analysieren von Dokumenten verwenden. Die Signaturfelder geben an, ob eine Signatur erkannt wurde.

## <a name="next-steps"></a>Nächste Schritte

* Durcharbeiten eines Schnellstarts zur Formularerkennung:

  > [!div class="nextstepaction"]
  > [Formularerkennung: Schnellstart](quickstarts/try-sdk-rest-api.md)

* Erkunden Sie unsere REST-API:

    > [!div class="nextstepaction"]
    > [Formularerkennung-API Version 2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)
