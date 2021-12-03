---
title: 'Schrittanleitung: Verwenden benutzerdefinierter und zusammengesetzter Modelle'
titleSuffix: Azure Applied AI Services
description: Erfahren Sie, wie Sie benutzerdefinierte und zusammengesetzte Modelle für die Formularerkennung erstellen, verwenden und verwalten.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5389e30e2aca2d93ba0fb27c71a6b934d7bf10e0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027783"
---
# <a name="use-custom-and-composed-models"></a>Verwenden benutzerdefinierter und zusammengesetzter Modelle

Die Formularerkennung verwendet fortschrittliche ML-Technologie, um Informationen in Dokumentbildern zu erkennen, zu extrahieren und sie in Form einer strukturierten JSON-Ausgabe zurück zu geben. Mit der Formularerkennung können Sie eigenständige benutzerdefinierte Modelle trainieren oder benutzerdefinierte Modelle vereinen, um zusammengesetzte Modelle zu erstellen.

* **Benutzerdefinierte Modelle:** Benutzerdefinierte Modelle für die Formularerkennung ermöglichen es Ihnen, Daten in unternehmensspezifischen Formularen und Dokumenten zu analysieren und zu extrahieren. Benutzerdefinierte Modelle werden speziell für Ihre individuellen Daten und Anwendungsfälle trainiert.

* **Zusammengesetzte Modelle**. Ein zusammengesetztes Modell wird erstellt, indem eine Sammlung benutzerdefinierter Modelle verwendet und zu einem einzigen Modell zusammengesetzt wird, das Ihre Formulartypen umfasst. Wenn ein Dokument an ein zusammengesetztes Modell übermittelt wird, führt der Dienst einen Klassifizierungsschritt aus, um zu entscheiden, welchem benutzerdefiniertem Modell das zur Analyse eingereichte Formular genau entspricht.

***Fenster für die Modellkonfiguration in Formularerkennung Studio***

:::image type="content" source="media/studio/composed-model.png" alt-text="Screenshot: Fenster für die Modellkonfiguration in Formularerkennung Studio":::

In diesem Artikel erfahren Sie, wie Sie benutzerdefinierte und zusammengesetzte Modelle der Formularerkennung mit unserem [Bespielbeschriftungstool für die Formularerkennung](label-tool.md), [REST-APIs](quickstarts/client-library.md?branch=main&pivots=programming-language-rest-api#train-a-custom-model) oder [Clientbibliothek-SDKs](quickstarts/client-library.md?branch=main&pivots=programming-language-csharp#train-a-custom-model) erstellen.

## <a name="sample-labeling-tool"></a>Tool für die Beschriftung von Beispielen

Sie können sehen, wie Daten aus benutzerdefinierten Formularen extrahiert werden, indem Sie unser Tool für die Bezeichnung von Beispielen ausprobieren. Sie benötigen Folgendes:

* Azure-Abonnement – Sie können ein [kostenloses Abonnement erstellen](https://azure.microsoft.com/free/cognitive-services/).

* Eine [Instanz der Formularerkennung](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) im Azure-Portal. Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst auszuprobieren. Wählen Sie nach der Bereitstellung Ihrer Ressource **Zu Ressource wechseln** aus, um Ihren API-Schlüssel und -Endpunkt abzurufen.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Screenshot: Schlüssel und Endpunktspeicherort im Azure-Portal":::

> [!div class="nextstepaction"]
> [Testen](https://fott-2-1.azurewebsites.net/projects/create)

Auf der Benutzeroberfläche der Formularerkennung:

1. Wählen Sie **„Benutzerdefiniert verwenden“, um ein Modell mit Bezeichnungen zu trainieren und Schlüssel-Wert-Paare zu erhalten**.
  
      :::image type="content" source="media/label-tool/fott-use-custom.png" alt-text="Screenshot: Fott-Toolauswahl der benutzerdefinierten Option":::

1. Wählen Sie im nächsten Fenster die Option **Neues Projekt** aus:

    :::image type="content" source="media/label-tool/fott-new-project.png" alt-text="Screenshot: Fott-Toolauswahl für neues Projekt"::: 

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

Das [Trainieren Ihres Modells](./quickstarts/try-sdk-rest-api.md#train-a-custom-model) ist mit oder ohne bezeichnete Datasets möglich. Das Erkennen und Identifizieren der Schlüsselinformationen ohne weitere Benutzereingabe basiert bei nicht bezeichneten Datasets ausschließlich auf der [Layout-API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeLayoutAsync). Auch bezeichnete Datasets basieren auf der Layout-API, enthalten jedoch zusätzliche Benutzereingaben, z. B. Ihre spezifischen Bezeichnungen und Feldpositionen. Wenn Sie sowohl bezeichnete als auch nicht bezeichnete Daten verwenden möchten, beginnen Sie mit mindestens fünf ausgefüllten Formularen desselben Typs für die bezeichneten Trainingsdaten, und fügen Sie dann dem gewünschten Dataset nicht bezeichnete Daten hinzu.

### <a name="train-without-labels"></a>Trainieren ohne Beschriftungen

Die Formularerkennung verwendet nicht überwachtes Lernen, um das Layout und die Beziehungen zwischen Feldern und Einträgen in Ihren Formularen nachzuvollziehen. Anhand der übermittelten Eingabeformulare gruppiert der Algorithmus die Formulare nach Typ, erkennt, welche Schlüssel und Tabellen vorhanden sind, und ordnet Schlüsseln Werte und Tabellen Einträge zu. Für das Trainieren ohne Beschriftungen ist keine manuelle Datenbeschriftung oder intensive Codierung und Wartung erforderlich, und Sie sollten diese Methode zuerst testen.

Tipps zum Sammeln von Trainingsdokumenten finden Sie unter [Erstellen eines Trainingsdatasets für ein benutzerdefiniertes Modell](./build-training-data-set.md).

### <a name="train-with-labels"></a>Trainieren mit Beschriftungen

Wenn Sie mit beschrifteten Daten trainieren, extrahiert das Modell relevante Werte mit überwachtem Lernen, wobei die von Ihnen bereitgestellten beschrifteten Formulare verwendet werden. Beschriftete Daten führen zu Modellen mit besserer Leistung und können Modelle hervorbringen, die mit komplexen Formularen oder Formularen arbeiten, die Werte ohne Schlüssel enthalten.

Die Formularerkennung verwendet die [Layout](concept-layout.md)-API, um die erwarteten Größen und Positionen von gedruckten und handschriftlichen Textelementen zu erlernen und Tabellen zu extrahieren. Anschließend werden benutzerdefinierte Beschriftungen verwendet, um die Schlüssel-Wert-Zuordnungen und Tabellen in den Dokumenten zu erlernen. Wir empfehlen, fünf manuell beschriftete Formulare gleichen Typs (gleiche Struktur) zu verwenden, um mit dem Trainieren eines neuen Modells zu beginnen, und weitere beschriftete Daten nach Bedarf hinzuzufügen, um die Modellgenauigkeit zu verbessern. Die Formularerkennung ermöglicht das Trainieren eines Modells zum Extrahieren von Schlüssel-Wert-Paaren und Tabellen mithilfe der Funktionen für beaufsichtigtes Lernen.

[Trainieren eines Formularerkennungsmodells mit Beschriftungen mithilfe des Tools für die Beschriftung von Beispielen](label-tool.md)

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]

## <a name="create-a-composed-model"></a>Erstellen zusammengesetzter Modelle

> [!NOTE]
> **Es können nur benutzerdefinierte Modelle zusammengesetzt werden, die _mit_ Bezeichnungen trainiert wurden.** Der Versuch, Modelle ohne Bezeichnung zusammenzusetzen, erzeugt einen Fehler.

Sie können einem zusammengesetzten Modell bis zu 100 trainierte benutzerdefinierte Modelle zuweisen. Wenn Sie mit der ID dieses zusammengesetzten Modells die Option zum Analysieren aufrufen, klassifiziert die Formularerkennung zunächst das übermittelte Formular, wählt das am ehesten übereinstimmende Modell aus und gibt dann Ergebnisse für dieses Modell zurück. Dieser Vorgang ist nützlich, wenn eingehende Formulare zu einer von mehreren Vorlagen gehören können.

Folgen Sie bei Verwendung des Beispielbeschriftungstool für die Formularerkennung, der REST-API oder der Clientbibliothek-SDKs den nachstehenden Schritten, um ein benutzerdefiniertes Modell zu erstellen:

1. [**Erfassen der IDs Ihrer benutzerdefinierten Modelle**](#gather-your-custom-model-ids)
1. [**Zusammensetzen Ihrer benutzerdefinierten Modelle**](#compose-your-custom-models)

#### <a name="gather-your-custom-model-ids"></a>Erfassen der IDs Ihrer benutzerdefinierten Modelle

Nachdem der Trainingsprozess erfolgreich abgeschlossen wurde, wird Ihrem benutzerdefinierten Modell eine Modell-ID zugewiesen. Sie können eine Modell-ID wie folgt abrufen:

### <a name="form-recognizer-sample-labeling-tool"></a>[**Beispielbeschriftungstool für die Formularerkennung**](#tab/fott)

Wenn Sie Modelle mithilfe des [**Beispielbeschriftungstools für die Formularerkennung**](https://fott-2-1.azurewebsites.net/) trainieren, befindet sich die Modell-ID im Fenster mit den Trainingsresultaten:

:::image type="content" source="media/fott-training-results.png" alt-text="Screenshot: Fenster der Trainingsresultate":::

### <a name="rest-api"></a>[**REST-API**](#tab/rest-api)

Die [**REST-API**](./quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#train-a-custom-model) gibt eine `201 (Success)`-Antwort mit einem **Adressheader** zurück. Der Wert des letzten Parameters in diesem Header ist die Modell-ID für das neu trainierte Modell:

:::image type="content" source="media/model-id.png" alt-text="Screenshot: Der zurückgegebene Adressheader mit der Modell-ID.":::

### <a name="client-library-sdks"></a>[**Clientbibliothek-SDKs**](#tab/sdks)

 Die [**Clientbibliothek-SDKs**](./quickstarts/try-sdk-rest-api.md?pivots=programming-language-csharp#train-a-custom-model) geben ein Modellobjekt zurück, bei dem die ID des Trainierten Modells abgefragt werden kann:

* C: \#  | [Klasse „CustomFormModel“](/dotnet/api/azure.ai.formrecognizer.training.customformmodel?view=azure-dotnet&preserve-view=true#properties "Azure SDK für .NET")

* Java: [Klasse „CustomFormModelInfo“](/java/api/com.azure.ai.formrecognizer.training.models.customformmodelinfo?view=azure-java-stable&preserve-view=true#methods "Azure SDK für Java")

* JavaScript: [Interface „CustomFormModelInfo“](/javascript/api/@azure/ai-form-recognizer/customformmodelinfo?view=azure-node-latest&preserve-view=true&branch=main#properties "Azure SDK für JavaScript")

* Python: [Klasse „CustomFormModelInfo“](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.customformmodelinfo?view=azure-python&preserve-view=true&branch=main#variables "Azure SDK für Python")

---

#### <a name="compose-your-custom-models"></a>Zusammensetzen Ihrer benutzerdefinierten Modelle

Nachdem Sie die benutzerdefinierten Modelle gesammelt haben, die einem einzelnen Formulartyp entsprechen, können Sie sie zu einem einzelnen Modell zusammensetzen.

### <a name="form-recognizer-sample-labeling-tool"></a>[**Beispielbeschriftungstool für die Formularerkennung**](#tab/fott)

Mit dem **Beispielbeschriftungstool** können Sie schnell mit dem Trainieren von Modellen beginnen und diese zu einer einzelnen Modell-ID zusammensetzen.

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

* [**C#/.NET**](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/Sample_ModelCompose.md).

* [**Java**](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/java/com/azure/ai/formrecognizer/administration/CreateComposedModel.java)

* [**JavaScript**](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/formrecognizer/ai-form-recognizer/samples/v3/javascript/createComposedModel.js)

* [**Python**](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/formrecognizer/azure-ai-formrecognizer/samples/v3.2-beta/sample_create_composed_model.py)

---

## <a name="analyze-documents-with-your-custom-or-composed-model"></a>Analysieren von Dokumenten mit Ihrem benutzerdefinierten oder zusammengesetzten Modell

 Der benutzerdefinierte Vorgang zum **Analysieren** von Formularen erfordert, dass Sie die `modelID` im Aufruf der Formularerkennung verwenden. Sie können eine einzelne benutzerdefinierte Modell-ID oder eine zusammengesetzte Modell-ID für den Parameter `modelID` angeben.

### <a name="form-recognizer-sample-labeling-tool"></a>[**Beispielbeschriftungstool für die Formularerkennung**](#tab/fott)

1. Wählen Sie im Menü auf der linken Seite des Tools das **Analysieren-Symbol** (Glühbirne) aus.

1. Wählen Sie eine lokale Datei oder Bild-URL aus, die analysiert werden soll.

1. Wählen Sie die Schaltfläche **Analyse Ausführen**.

1. Das Tool wendet Tags in Begrenzungsrahmen an und meldet die Konfidenz jedes Tags.

:::image type="content" source="media/analyze.png" alt-text="Screenshot: Fenster „analyze-a-custom-form“ (benutzerdefiniertes Formular analysieren) der Formularerkennung":::

### <a name="rest-api"></a>[**REST-API**](#tab/rest-api)

Mithilfe der REST-API können Sie eine [Formularanalyse](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)-Anforderung erstellen, um ein Dokument zu analysieren und Schlüssel-Wert-Paare und Tabellendaten zu extrahieren.

### <a name="client-library-sdks"></a>[**Clientbibliothek-SDKs**](#tab/sdks)

Verwenden Sie die Programmiersprache Ihrer Wahl, um ein Formular oder Dokument mit einem benutzerdefinierten oder zusammengesetzten Modell zu analysieren. Sie benötigen Ihren Formularerkennungsendpunkt, den API-Schlüssel und die Modell-ID.

* [**C#/.NET**](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/Sample_ModelCompose.md)

* [**Java**](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/java/com/azure/ai/formrecognizer/AnalyzeCustomDocumentFromUrl.java)

* [**JavaScript**](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/formrecognizer/ai-form-recognizer/samples/v3/javascript/recognizeCustomForm.js)

* [**Python**](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/formrecognizer/azure-ai-formrecognizer/samples/v3.1/sample_recognize_custom_forms.py)

---

Testen Sie Ihre neu trainierten Modelle, indem Sie [Formulare analysieren,](./quickstarts/try-sdk-rest-api.md#analyze-forms-with-a-custom-model) die nicht Teil des Trainingsdatasets waren. Je nach gemeldeter Genauigkeit können Sie weitere Trainingsiterationen durchführen, um das Modell zu verbessern. Sie können das Training fortführen, um die Ergebnisse zu [verbessern.](label-tool.md#improve-results)

## <a name="manage-your-custom-models"></a>Verwalten von benutzerdefinierten Modellen

Während des gesamten Lebenszyklus können Sie [Ihre benutzerdefinierten Modelle verwalten](./quickstarts/try-sdk-rest-api.md#manage-custom-models), indem Sie eine [Liste aller benutzerdefinierten Modelle](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetCustomModels) unter Ihrem Abonnement anzeigen, Informationen zu einem [bestimmten benutzerdefinierten Modell](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetCustomModel) abrufen und [benutzerdefinierte Modelle aus Ihrem Konto löschen](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/DeleteCustomModel).

Sehr gut! Sie haben erfahren, wie Sie benutzerdefinierte und zusammengesetzte Modelle erstellen und für Ihre Formularerkennungsprojekte und Anwendungen verwenden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Formularerkennungs Clientbibliothek finden Sie in unserer API-Referenzdokumentation.

> [!div class="nextstepaction"]
> [Referenz zur Formularerkennungs-API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)
>
