---
title: Benutzerdefinierte und zusammengesetzte Modelle – Formularerkennung
titleSuffix: Azure Applied AI Services
description: Erfahren Sie, wie Sie benutzerdefinierte und zusammengesetzte Modelle für die Formularerkennung erstellen, verwenden und verwalten, sowie welche Einschränkungen dafür bestehen.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/09/2021
ms.author: lajanuar
ms.openlocfilehash: 9c995c5ce63c44473cd3d7b824ef85e64868ae1e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128546634"
---
# <a name="form-recognizer-custom-and-composed-models"></a>Formularerkennung – benutzerdefinierte und zusammengesetzte Modelle

Die Formularerkennung verwendet fortschrittliche ML-Technologie, um Informationen in Dokumentbildern zu erkennen, zu extrahieren und sie in Form einer strukturierten JSON-Ausgabe zurück zu geben. Mit der Formularerkennung können Sie eigenständige benutzerdefinierte Modelle trainieren, zusammengesetzte Modelle erstellen oder mit unseren vordefinierten Modellen beginnen.

* **Benutzerdefinierte Modelle:** Benutzerdefinierte Modelle für die Formularerkennung ermöglichen es Ihnen, Daten in unternehmensspezifischen Formularen und Dokumenten zu analysieren und zu extrahieren. Benutzerdefinierte Modelle werden speziell für Ihre individuellen Daten und Anwendungsfälle trainiert.

* **Zusammengesetzte Modelle**. Ein zusammengesetztes Modell wird erstellt, indem eine Sammlung benutzerdefinierter Modelle verwendet und zu einem einzigen Modell zusammengesetzt wird, das Ihre Formulartypen umfasst. Wenn ein Dokument an ein zusammengesetztes Modell übermittelt wird, führt der Dienst einen Klassifizierungsschritt aus, um zu entscheiden, welchem benutzerdefiniertem Modell das zur Analyse eingereichte Formular genau entspricht.

* **Vordefinierte Modelle:** Die Formularerkennung unterstützt derzeit vordefinierte Modelle für [Visitenkarten](concept-business-cards.md), [Layout](concept-layout.md), [Identitätsdokumente](concept-identification-cards.md), [Rechnungen](concept-invoices.md) und [Quittungen](concept-receipts.md).

In diesem Artikel untersuchen wir den Prozess zum Erstellen benutzerdefinierter und zusammengesetzter Formularerkennung-Modelle mit unserem [Tool zum Bezeichnen von Beispielen für die Formularerkennung](label-tool.md), [REST-APIs](quickstarts/client-library.md?branch=main&pivots=programming-language-rest-api#train-a-custom-model) oder [Clientbibliothek-SDKs](quickstarts/client-library.md?branch=main&pivots=programming-language-csharp#train-a-custom-model).

## <a name="what-is-a-custom-model"></a>Was ist ein benutzerdefiniertes Modell?

Ein benutzerdefiniertes Modell ist ein ML-Programm, das trainiert wurde, um Formularfelder in Ihren individuellen Inhalten zu erkennen und Schlüssel-Wert-Paare und Tabellendaten zu extrahieren. Für den Einstieg benötigen Sie nur fünf Beispiele desselben Formulartyps, und Ihr benutzerdefiniertes Modell kann mit oder ohne bezeichnete Datasets trainiert werden.

## <a name="what-is-a-composed-model"></a>Was ist ein zusammengesetztes Modell?

In einem zusammengesetzten Modell werden verschiedene benutzerdefinierten Modelle kombiniert und dann mit einer einzigen Modell-ID aufgerufen. Dies ist nützlich, wenn Sie mehrere Modelle trainiert haben und diese gruppieren möchten, um ähnliche Formulartypen zu analysieren. Ihr zusammengesetztes Modell kann z. B. aus benutzerdefinierten Modellen bestehen, die trainiert wurden, um Ihre Bestellungsdokumente für Material, Ausrüstung und Möbel zu analysieren. Anstatt das entsprechende Modell manuell auszuwählen, können Sie ein zusammengesetztes Modell verwenden, um das zutreffende benutzerdefinierte Modell für jede Analyse und Extraktion zu bestimmen.

## <a name="try-it"></a>Ausprobieren

Erste Schritte mit unserem Tool zum Bezeichnen von Beispielen für die Formularerkennung:

> [!div class="nextstepaction"]
> [Ein benutzerdefiniertes Modell ausprobieren](https://aka.ms/fott-2.1-ga "Beginnen Sie mit „Benutzerdefiniert“, um ein Modell mit Bezeichnungen zu trainieren und Schlüssel-Wert-Paare zu suchen.")

## <a name="create-your-models"></a>Erstellen Ihrer Modelle

Dies sind die Schritte zum Erstellen, Trainieren und Verwenden benutzerdefinierter und zusammengesetzter Modelle:

* [**Zusammenstellen des Trainingsdatasets**](#assemble-your-training-dataset)
* [**Hochladen Ihres Trainingssets auf Azure Blob Storage**](#upload-your-training-dataset)
* [**Trainieren Ihres benutzerdefinierten Modells**](#train-your-custom-model)
* [**Zusammensetzen benutzerdefinierter Modelle**](#create-a-composed-model)
* [**Analysieren von Dokumenten**](#analyze-documents-with-your-custom-or-composed-model)
* [**Verwalten von benutzerdefinierten Modellen**](#manage-your-custom-models)

## <a name="assemble-your-training-dataset"></a>Zusammenstellen des Trainingsdatasets

Die Erstellung eines benutzerdefinierten Modells beginnt mit dem Einrichten des Trainingsdatasets. Für das Beispieldataset benötigen Sie mindestens fünf ausgefüllte Formulare desselben Typs. Diese können unterschiedliche Dateitypen (jpg, png, pdf, tiff) aufweisen und Text sowie Handschrift enthalten. Die Formulare müssen die [Eingabeanforderungen](build-training-data-set.md#custom-model-input-requirements) für die Formularerkennung erfüllen.

## <a name="upload-your-training-dataset"></a>Hochladen Ihres Trainingsdatasets

Sie müssen die [Trainingsdaten in einen Azure Blob Storage-Container hochladen](build-training-data-set.md#upload-your-training-data). Informationen zum Erstellen eines Azure Storage-Kontos mit einem Container finden *Sie im* [Azure Storage-Schnellstart für das Azure-Portal](../../storage/blobs/storage-quickstart-blobs-portal.md). Sie können den kostenlosen Tarif (F0) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

## <a name="train-your-custom-model"></a>Trainieren Ihres benutzerdefinierten Modells

Das [Trainieren Ihres Modells](quickstarts/client-library.md#train-a-custom-model) ist mit oder ohne bezeichnete Datasets möglich. Das Erkennen und Identifizieren der Schlüsselinformationen ohne weitere Benutzereingabe basiert bei nicht bezeichneten Datasets ausschließlich auf der [Layout-API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeLayoutAsync). Auch bezeichnete Datasets basieren auf der Layout-API, enthalten jedoch zusätzliche Benutzereingaben, z. B. Ihre spezifischen Bezeichnungen und Feldpositionen. Wenn Sie sowohl bezeichnete als auch nicht bezeichnete Daten verwenden möchten, beginnen Sie mit mindestens fünf ausgefüllten Formularen desselben Typs für die bezeichneten Trainingsdaten, und fügen Sie dann dem gewünschten Dataset nicht bezeichnete Daten hinzu.

## <a name="create-a-composed-model"></a>Erstellen zusammengesetzter Modelle

> [!NOTE]
> **Es können nur benutzerdefinierte Modelle zusammengesetzt werden, die _mit_ Bezeichnungen trainiert wurden.** Der Versuch, Modelle ohne Bezeichnung zusammenzusetzen, erzeugt einen Fehler.

Sie können einem zusammengesetzten Modell bis zu 100 trainierte benutzerdefinierte Modelle zuweisen. Wenn Sie mit der ID dieses zusammengesetzten Modells die Option zum Analysieren aufrufen, klassifiziert die Formularerkennung zunächst das übermittelte Formular, wählt das am ehesten übereinstimmende Modell aus und gibt dann Ergebnisse für dieses Modell zurück. Dieser Vorgang ist nützlich, wenn eingehende Formulare zu einer von mehreren Vorlagen gehören können.

Folgen Sie bei Verwendung des Tools zum Bezeichnen von Beispielen für die Formularerkennung, der REST-API oder der Clientbibliothek-SDKs den nachstehenden Schritten, um ein benutzerdefiniertes Modell zu erstellen:

1. [**Erfassen der IDs Ihrer benutzerdefinierten Modelle**](#gather-your-custom-model-ids)
1. [**Zusammensetzen Ihrer benutzerdefinierten Modelle**](#compose-your-custom-models)

#### <a name="gather-your-custom-model-ids"></a>Erfassen der IDs Ihrer benutzerdefinierten Modelle

Nachdem der Trainingsprozess erfolgreich abgeschlossen wurde, wird Ihrem benutzerdefinierten Modell eine Modell-ID zugewiesen. Sie können eine Modell-ID wie folgt abrufen:

### <a name="form-recognizer-sample-labeling-tool"></a>[**Tool zum Bezeichnen von Beispielen für die Formularerkennung**](#tab/fott)

Wenn Sie Modelle mithilfe des [**Tools zum Bezeichnen von Beispielen für die Formularerkennung**](https://fott-2-1.azurewebsites.net/) trainieren, befindet sich die Modell-ID im Fenster mit den Trainingsresultaten:

:::image type="content" source="media/fott-training-results.png" alt-text="Screenshot: Fenster der Trainingsresultate":::

### <a name="rest-api"></a>[**REST-API**](#tab/rest-api)

Die [**REST-API**](quickstarts/client-library.md?pivots=programming-language-rest-api#train-a-custom-model) gibt eine `201 (Success)`-Antwort mit einem **Adressheader** zurück. Der Wert des letzten Parameters in diesem Header ist die Modell-ID für das neu trainierte Modell:

:::image type="content" source="media/model-id.png" alt-text="Screenshot: Der zurückgegebene Adressheader mit der Modell-ID.":::

### <a name="client-library-sdks"></a>[**Clientbibliothek-SDKs**](#tab/sdks)

 Die [**Clientbibliothek-SDKs**](quickstarts/client-library.md?pivots=programming-language-csharp#train-a-custom-model) geben ein Modellobjekt zurück, bei dem die ID des Trainierten Modells abgefragt werden kann:

* C: \#  | [Klasse „CustomFormModel“](/dotnet/api/azure.ai.formrecognizer.training.customformmodel?view=azure-dotnet&preserve-view=true#properties "Azure SDK für .NET")

* Java: [Klasse „CustomFormModelInfo“](/java/api/com.azure.ai.formrecognizer.training.models.customformmodelinfo?view=azure-java-stable&preserve-view=true#methods "Azure SDK für Java")

* JavaScript: [Interface „CustomFormModelInfo“](/javascript/api/@azure/ai-form-recognizer/customformmodelinfo?view=azure-node-latest&preserve-view=true&branch=main#properties "Azure SDK für JavaScript")

* Python: [Klasse „CustomFormModelInfo“](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.customformmodelinfo?view=azure-python&preserve-view=true&branch=main#variables "Azure SDK für Python")

---

#### <a name="compose-your-custom-models"></a>Zusammensetzen Ihrer benutzerdefinierten Modelle

Nachdem Sie die benutzerdefinierten Modelle gesammelt haben, die einem einzelnen Formulartyp entsprechen, können Sie sie zu einem einzelnen Modell zusammensetzen.

### <a name="form-recognizer-sample-labeling-tool"></a>[**Tool zum Bezeichnen von Beispielen für die Formularerkennung**](#tab/fott)

Mit dem **Tool zum Bezeichnen von Beispielen** können Sie schnell mit dem Trainieren von Modellen beginnen und diese zu einer einzigen Modell-ID zusammensetzen.

Nachdem Sie das Training abgeschlossen haben, erstellen Sie Ihre Modelle wie folgt:

1. Wählen Sie auf der linken Menüschiene das **Modellzusammensetzungssymbol** (ein zusammengeführter Pfeil) aus.

1. Wählen Sie im Hauptfenster die Modelle aus, die Sie einer einzelnen Modell-ID zuweisen möchten. Modelle mit dem Pfeilsymbol sind bereits zusammengesetzte Modelle.

1. Wählen Sie in der linken oberen Ecke die **Schaltfläche „Zusammensetzen“** aus.

1. Geben Sie im Popupfenster einen Namen für Ihr neu zusammengesetztes Modell ein, und wählen Sie **Zusammensetzen** aus.

Nach Abschluss des Vorgangs sollte das neue zusammengesetzte Modell in der Liste angezeigt werden.

  :::image type="content" source="media/custom-model-compose.png" alt-text="Screenshot: Fenster zum Zusammensetzen von Modellen" lightbox="media/custom-model-compose-expanded.png":::

### <a name="rest-api"></a>[**REST-API**](#tab/rest-api)

Mithilfe der **REST-API** können Sie eine Anforderung zum [**Zusammensetzen benutzerdefinierter Modelle**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/Compose) senden, um ein einzelnes zusammengesetztes Modell aus vorhandenen Modellen zu erstellen. Für den Anforderungskörper ist ein Array mit Strings Ihrer `modelIds` erforderlich, und optional können Sie den `modelName` definieren.  *Siehe* [Asynchrones Zusammensetzen von Modellen](/rest/api/formrecognizer/2.1preview2/compose-custom-models-async/compose-custom-models-async).

### <a name="client-library-sdks"></a>[**Clientbibliothek-SDKs**](#tab/sdks)

Verwenden Sie den Programmiersprachencode Ihrer Wahl, um ein zusammengesetztes Modell zu erstellen, das mit einer einzelnen Modell-ID aufgerufen wird. Im Folgenden finden Sie Links zu Codebeispielen, die veranschaulichen, wie sie ein zusammengesetztes Modell aus vorhandenen benutzerdefinierten Modellen erstellen:

* [**C#/.NET**](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/Sample8_ModelCompose.md).

* [**Java**](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/java/com/azure/ai/formrecognizer/CreateComposedModel.java)

* [**JavaScript**](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/formrecognizer/ai-form-recognizer/samples/v3/javascript/createComposedModel.js)

* [**Python**](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_create_composed_model.py)

---

## <a name="analyze-documents-with-your-custom-or-composed-model"></a>Analysieren von Dokumenten mit Ihrem benutzerdefinierten oder zusammengesetzten Modell

 Der benutzerdefinierte Vorgang zum **Analysieren** von Formularen erfordert, dass Sie die `modelID` im Aufruf der Formularerkennung verwenden. Sie können eine einzelne benutzerdefinierte Modell-ID oder eine zusammengesetzte Modell-ID für den Parameter `modelID` angeben.

### <a name="form-recognizer-sample-labeling-tool"></a>[**Tool zum Bezeichnen von Beispielen für die Formularerkennung**](#tab/fott)

1. Wählen Sie im Menü auf der linken Seite des Tools das **Analysieren-Symbol** (Glühbirne) aus.

1. Wählen Sie eine lokale Datei oder Bild-URL aus, die analysiert werden soll.

1. Wählen Sie die Schaltfläche **Analyse Ausführen**.

1. Das Tool wendet Tags in Begrenzungsrahmen an und meldet die Konfidenz jedes Tags.

:::image type="content" source="media/analyze.png" alt-text="Screenshot: Fenster „analyze-a-custom-form“ (benutzerdefiniertes Formular analysieren) der Formularerkennung":::

### <a name="rest-api"></a>[**REST-API**](#tab/rest-api)

Mithilfe der REST-API können Sie eine [Formularanalyse](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)-Anforderung erstellen, um ein Dokument zu analysieren und Schlüssel-Wert-Paare und Tabellendaten zu extrahieren.

### <a name="client-library-sdks"></a>[**Clientbibliothek-SDKs**](#tab/sdks)

Verwenden Sie die Programmiersprache Ihrer Wahl, um ein Formular oder Dokument mit einem benutzerdefinierten oder zusammengesetzten Modell zu analysieren. Sie benötigen Ihren Formularerkennungsendpunkt, den API-Schlüssel und die Modell-ID.

* [**C#/.NET**](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/Sample8_ModelCompose.md#recognize-a-custom-form-using-a-composed-model)

* [**Java**](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/java/com/azure/ai/formrecognizer/RecognizeCustomFormsFromUrl.java)

* [**JavaScript**](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/formrecognizer/ai-form-recognizer/samples/v3/javascript/recognizeCustomForm.js)

* [**Python**](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_recognize_custom_forms.py)

---

Testen Sie Ihre neu trainierten Modelle, indem Sie [Formulare analysieren,](quickstarts/client-library.md#analyze-forms-with-a-custom-model) die nicht Teil des Trainingsdatasets waren. Je nach gemeldeter Genauigkeit können Sie weitere Trainingsiterationen durchführen, um das Modell zu verbessern. Sie können das Training fortführen, um die Ergebnisse zu [verbessern.](label-tool.md#improve-results)

## <a name="manage-your-custom-models"></a>Verwalten von benutzerdefinierten Modellen

Während des gesamten Lebenszyklus können Sie [Ihre benutzerdefinierten Modelle verwalten](quickstarts/client-library.md#manage-custom-models), indem Sie eine [Liste aller benutzerdefinierten Modelle](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetCustomModels) unter Ihrem Abonnement anzeigen, Informationen zu einem [bestimmten benutzerdefinierten Modell](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetCustomModel) abrufen und [benutzerdefinierte Modelle aus Ihrem Konto löschen](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/DeleteCustomModel).

Sehr gut! Sie haben erfahren, wie Sie benutzerdefinierte und zusammengesetzte Modelle erstellen und für Ihre Formularerkennungsprojekte und Anwendungen verwenden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Formularerkennungs Clientbibliothek finden Sie in unserer API-Referenzdokumentation.

> [!div class="nextstepaction"]
> [Referenz zur Formularerkennungs-API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)
>
