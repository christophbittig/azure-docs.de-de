---
title: 'Schnellstart: Java SDK für Formularerkennung v3.0 | Vorschau'
titleSuffix: Azure Applied AI Services
description: Formular- und Dokumentverarbeitung, Datenextraktion und Analyse mit Java-Clientbibliothek-SDKs für Formularerkennung v3.0 (Vorschau)
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 23d7ef3bf6eb29e81723cb102427ef32d8bc83b0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029465"
---
# <a name="quickstart-java-client-library-sdk-v30--preview"></a>Schnellstart: Java-Clientbibliothek-SDK v3.0 | Vorschau

>[!NOTE]
> Formularerkennung v3.0 befindet sich derzeit in der öffentlichen Vorschauphase. Dies bedeutet, dass einige Features unter Umständen nicht unterstützt werden oder nur eingeschränkt verwendbar sind.

[Referenzdokumentation](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-formrecognizer/4.0.0-beta.1/index.html) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src) | [Paket (Maven)](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer) | [Beispiele](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)

Informieren Sie sich über die ersten Schritte mit der Azure-Formularerkennung mit der Programmiersprache Java. Die Azure-Formularerkennung ist ein cloudbasierter Dienst von Azure Applied AI Services, bei dem maschinelles Lernen genutzt wird, um Formularfelder, Text und Tabellen aus Ihren Dokumenten zu extrahieren und zu analysieren. Sie können Formularerkennungsmodelle leicht aufrufen, indem Sie unsere Clientbibliothek-SDKs in Ihre Workflows und Anwendungen integrieren. Sie sollten den kostenlosen Dienst nutzen, wenn Sie die Technologie erlernen. Bedenken Sie, dass die Anzahl der kostenlosen Seiten auf 500 pro Monat beschränkt ist.

Weitere Informationen zu Funktionen der Formularerkennung und zu Entwicklungsoptionen finden Sie auf unserer [Übersichtsseite](../overview.md#form-recognizer-features-and-development-options).

In dieser Schnellstartanleitung verwenden Sie die folgenden Funktionen, um Daten und Werte aus Formularen und Dokumenten zu analysieren und zu extrahieren:

* [🆕 **Allgemeines Dokument:**](#try-it-general-document-model) Analysieren und Extrahieren von Text, Tabellen, Strukturen, Schlüssel-Wert-Paaren und benannten Entitäten

* [**Layout:**](#try-it-layout-model) Analysieren und Extrahieren von Tabellen, Zeilen, Wörtern und Auswahlmarkierungen, z. B. Optionsfeldern und Kontrollkästchen in Formulardokumenten, ohne ein Modell trainieren zu müssen

* [**Vordefinierte Rechnungen:**](#try-it-prebuilt-model) Analysieren und Extrahieren häufig verwendeter Felder aus Rechnungen über ein vortrainiertes Rechnungsmodell

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services/)
* [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable&preserve-view=true
), Version 8 oder höher
* Eine Cognitive Services- oder Formularerkennungsressource. Wenn Sie über Ihr Azure-Abonnement verfügen, können Sie im Azure-Portal eine Formularerkennungsressource mit [einem Dienst](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) oder [mehreren Diensten](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) erstellen, um Ihren Schlüssel und Endpunkt zu erhalten. Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

> [!TIP] 
> Erstellen Sie eine Cognitive Services-Ressource, wenn Sie planen, über einen einzelnen Endpunkt bzw. Schlüssel auf mehrere Cognitive Services-Instanzen zuzugreifen. Erstellen Sie eine Formularerkennungsressource, falls nur auf die Formularerkennung zugegriffen werden soll. Beachten Sie hierbei, dass Sie eine Ressource mit einem einzelnen Dienst benötigen, falls Sie die [Azure Active Directory-Authentifizierung](/azure/active-directory/authentication/overview-authentication) nutzen möchten.

* Klicken Sie nach der Bereitstellung Ihrer Ressource auf **Zu Ressource wechseln**. Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Formularerkennungs-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt:

  :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="Screenshot: Schlüssel und Endpunktspeicherort im Azure-Portal.":::

## <a name="set-up"></a>Einrichten

### <a name="create-a-new-gradle-project"></a>Erstellen eines neuen Gradle-Projekts

Erstellen Sie in einem Konsolenfenster (z. B. cmd, PowerShell oder Bash) ein neues Verzeichnis für Ihre App mit dem Namen **form-recognizer-app**, und navigieren Sie zu diesem Verzeichnis.

```console
mkdir form-recognizer-app && form-recognizer-app
```

Führen Sie den Befehl `gradle init` in Ihrem Arbeitsverzeichnis aus. Mit diesem Befehl werden grundlegende Builddateien für Gradle, u. a. die Datei *build.gradle.kts*, erstellt. Diese Datei wird zur Laufzeit zum Erstellen und Konfigurieren Ihrer Anwendung verwendet.

```console
gradle init --type basic
```

1. Wenn Sie zur Auswahl einer **DSL** aufgefordert werden, wählen Sie **Kotlin** aus.

1. Übernehmen Sie den Standardprojektnamen (form-recognizer-app).

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek

In dieser Schnellstartanleitung wird der Gradle-Abhängigkeits-Manager verwendet. Die Clientbibliothek und Informationen zu anderen Abhängigkeits-Managern finden Sie im [zentralen Maven-Repository](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer).

Fügen Sie in die Datei *build.gradle.kts* Ihres Projekts die Clientbibliothek als als `implementation`-Anweisung sowie die erforderlichen Plug-Ins und Einstellungen ein.

```kotlin
plugins {
    java
    application
}
application {
    mainClass.set("FormRecognizer")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "4.0.0-beta.1")
}
```

### <a name="create-a-java-file"></a>Erstellen einer Java-Datei

Führen Sie in Ihrem Arbeitsverzeichnis den folgenden Befehl aus:

```console
mkdir -p src/main/java
```

Sie erstellen die folgende Verzeichnisstruktur:

:::image type="content" source="../media/quickstarts/java-directories.png" alt-text="Screenshot: Java-Verzeichnisstruktur":::

Navigieren Sie zu dem neuen Ordner, und erstellen Sie eine Datei mit dem Namen *FormRecognizer.java* (im Verlauf des Befehls `gradle init` erstellt). Öffnen Sie sie in Ihrem bevorzugten Editor bzw. Ihrer bevorzugten IDE, und fügen Sie die folgende Paketdeklaration und die folgenden `import`-Anweisungen hinzu:

```java
package com.azure.ai.formrecognizer;

import com.azure.ai.formrecognizer.models.AnalyzeDocumentOptions;
import com.azure.ai.formrecognizer.models.AnalyzedDocument;
import com.azure.core.credential.AzureKeyCredential;
import com.azure.core.http.HttpPipeline;
import com.azure.core.http.HttpPipelineBuilder;
import com.azure.core.util.Context;

import java.io.ByteArrayInputStream;
import java.io.File;
import java.io.IOException;
import java.io.InputStream;
import java.nio.file.Files;
import java.util.Arrays;
```

### <a name="select-a-code-sample-to-copy-and-paste-into-your-applications-main-method"></a>Wählen Sie ein Codebeispiel zum Kopieren und Einfügen in die Main-Methode Ihrer Anwendung aus:

* [**Allgemeines Dokument**](#try-it-general-document-model)

* [**Layout**](#try-it-layout-model)

* [**Vordefinierte Rechnung**](#try-it-prebuilt-model)

> [!IMPORTANT]
>
> Denken Sie daran, den Schlüssel aus Ihrem Code zu entfernen, wenn Sie fertig sind, und ihn niemals zu veröffentlichen. Verwenden Sie in der Produktionsumgebung sichere Methoden, um Ihre Anmeldeinformationen zu speichern und darauf zuzugreifen. Weitere Informationen finden Sie im Cognitive Services-Artikel zur [Sicherheit](../../../cognitive-services/cognitive-services-security.md).

## <a name="try-it-general-document-model"></a>**Try it** (Ausprobieren): Allgemeines Dokumentmodell

> [!div class="checklist"]
>
> * Für dieses Beispiel benötigen Sie eine **Formulardokumentdatei unter einem URI**. Sie können für diese Schnellstartanleitung unser [Beispielformulardokument](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) verwenden.
> * Um eine bestimmte Datei an einem URI zu analysieren, verwenden Sie die `beginAnalyzeDocumentFromUrl`-Methode und übergeben `prebuilt-document` als Modell-ID. Der Rückgabewert ist ein `AnalyzeResult`-Objekt, das Daten zum übermittelten Dokument enthält.
> * Wir haben den Datei-URI-Wert der Variablen `documentUrl` in der main-Methode hinzugefügt.
> * Der Einfachheit halber werden hier nicht alle Entitätsfelder angezeigt, die der Dienst zurückgibt. Eine Liste aller unterstützten Felder mit den entsprechenden Typen finden Sie auf der Konzeptseite [Allgemeines Dokument](../concept-general-document.md#named-entity-recognition-ner-categories).

Aktualisieren Sie die **FormRecognizer**-Klasse Ihrer Anwendung mit dem folgenden Code (mit Aktualisierung der Schlüssel- und Endpunktvariablen mit Werten aus Ihrer Instanz der Formularerkennung im Azure-Portal):

```java
public class FormRecognizer {

    static final String key = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
    static final String endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";

    public static void main(String[] args) {

        DocumentAnalysisClient documentAnalysisClient = new DocumentAnalysisClientBuilder()
            .credential(new AzureKeyCredential("{key}"))
            .endpoint("{endpoint}")
            .buildClient();

        String documentUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf";
        String modelId = "prebuilt-document";
        SyncPoller < DocumentOperationResult, AnalyzeResult > analyzeDocumentPoller =
            documentAnalysisClient.beginAnalyzeDocumentFromUrl(modelId, documentUrl);

        AnalyzeResult analyzeResult = analyzeDocumentPoller.getFinalResult();

        for (int i = 0; i < analyzeResult.getDocuments().size(); i++) {
            final AnalyzedDocument analyzedDocument = analyzeResult.getDocuments().get(i);
            System.out.printf("----------- Analyzing document %d -----------%n", i);
            System.out.printf("Analyzed document has doc type %s with confidence : %.2f%n",
                analyzedDocument.getDocType(), analyzedDocument.getConfidence());
        }

        analyzeResult.getPages().forEach(documentPage - > {
            System.out.printf("Page has width: %.2f and height: %.2f, measured with unit: %s%n",
                documentPage.getWidth(),
                documentPage.getHeight(),
                documentPage.getUnit());

            // lines
            documentPage.getLines().forEach(documentLine - >
                System.out.printf("Line %s is within a bounding box %s.%n",
                    documentLine.getContent(),
                    documentLine.getBoundingBox().toString()));

            // words
            documentPage.getWords().forEach(documentWord - >
                System.out.printf("Word %s has a confidence score of %.2f%n.",
                    documentWord.getContent(),
                    documentWord.getConfidence()));
        });

        // tables
        List < DocumentTable > tables = analyzeResult.getTables();
        for (int i = 0; i < tables.size(); i++) {
            DocumentTable documentTable = tables.get(i);
            System.out.printf("Table %d has %d rows and %d columns.%n", i, documentTable.getRowCount(),
                documentTable.getColumnCount());
            documentTable.getCells().forEach(documentTableCell - > {
                System.out.printf("Cell '%s', has row index %d and column index %d.%n",
                    documentTableCell.getContent(),
                    documentTableCell.getRowIndex(), documentTableCell.getColumnIndex());
            });
            System.out.println();
        }

        // Entities
        analyzeResult.getEntities().forEach(documentEntity - > {
            System.out.printf("Entity category : %s, sub-category %s%n: ",
                documentEntity.getCategory(), documentEntity.getSubCategory());
            System.out.printf("Entity content: %s%n: ", documentEntity.getContent());
            System.out.printf("Entity confidence: %.2f%n", documentEntity.getConfidence());
        });

        // Key-value
        analyzeResult.getKeyValuePairs().forEach(documentKeyValuePair - > {
            System.out.printf("Key content: %s%n", documentKeyValuePair.getKey().getContent());
            System.out.printf("Key content bounding region: %s%n",
                documentKeyValuePair.getKey().getBoundingRegions().toString());

            System.out.printf("Value content: %s%n", documentKeyValuePair.getValue().getContent());
            System.out.printf("Value content bounding region: %s%n", documentKeyValuePair.getValue().getBoundingRegions().toString());
        });
        Build a custom document analysis model
        In 3. x.x, creating a custom model required specifying useTrainingLabels to indicate whether to use labeled data when creating the custom model with the beginTraining method.
        In 4. x.x, we introduced the new general document model(prebuilt - document) to replace the train without labels functionality from 3. x.x which extracts entities, key - value pairs, and layout from a document with the beginBuildModel method.In 4. x.x the beginBuildModel always returns labeled data otherwise.
        Train a custom model using 3. x.x beginTraining:

            String trainingFilesUrl = "{SAS_URL_of_your_container_in_blob_storage}";
        SyncPoller < FormRecognizerOperationResult, CustomFormModel > trainingPoller =
            formTrainingClient.beginTraining(trainingFilesUrl,
                false,
                new TrainingOptions()
                .setModelName("my model trained without labels"),
                Context.NONE);

        CustomFormModel customFormModel = trainingPoller.getFinalResult();

        // Model Info
        System.out.printf("Model Id: %s%n", customFormModel.getModelId());
        System.out.printf("Model name given by user: %s%n", customFormModel.getModelName());
        System.out.printf("Model Status: %s%n", customFormModel.getModelStatus());
        System.out.printf("Training started on: %s%n", customFormModel.getTrainingStartedOn());
        System.out.printf("Training completed on: %s%n%n", customFormModel.getTrainingCompletedOn());

        System.out.println("Recognized Fields:");
        // looping through the subModels, which contains the fields they were trained on
        // Since the given training documents are unlabeled we still group them but, they do not have a label.
        customFormModel.getSubmodels().forEach(customFormSubmodel - > {
            System.out.printf("Submodel Id: %s%n: ", customFormSubmodel.getModelId());
            // Since the training data is unlabeled, we are unable to return the accuracy of this model
            customFormSubmodel.getFields().forEach((field, customFormModelField) - >
                System.out.printf("Field: %s Field Label: %s%n",
                    field, customFormModelField.getLabel()));
        });

        System.out.println();
        customFormModel.getTrainingDocuments().forEach(trainingDocumentInfo - > {
            System.out.printf("Document name: %s%n", trainingDocumentInfo.getName());
            System.out.printf("Document status: %s%n", trainingDocumentInfo.getStatus());
            System.out.printf("Document page count: %d%n", trainingDocumentInfo.getPageCount());
            if (!trainingDocumentInfo.getErrors().isEmpty()) {
                System.out.println("Document Errors:");
                trainingDocumentInfo.getErrors().forEach(formRecognizerError - >
                    System.out.printf("Error code %s, Error message: %s%n", formRecognizerError.getErrorCode(),
                        formRecognizerError.getMessage()));
            }
        });
    }
```

## <a name="try-it-layout-model"></a>**Try it** (Ausprobieren): Layoutmodell

Dient zum Extrahieren von Text, Auswahlmarkierungen, Textformaten und Tabellenstrukturen sowie der zugehörigen Begrenzungsbereichskoordinaten aus Dokumenten.

> [!div class="checklist"]
>
> * Für dieses Beispiel benötigen Sie eine **Formulardokumentdatei unter einem URI**. Sie können für diese Schnellstartanleitung unser [Beispielformulardokument](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) verwenden.
> * Um eine bestimmte Datei an einem URI zu analysieren, verwenden Sie die `beginAnalyzeDocumentFromUrl`-Methode und übergeben `prebuilt-layout` als Modell-ID. Der Rückgabewert ist ein `AnalyzeResult`-Objekt, das Daten zum übermittelten Dokument enthält.
> * Wir haben den Datei-URI-Wert der Variablen `documentUrl` in der main-Methode hinzugefügt.

Aktualisieren Sie die **FormRecognizer**-Klasse Ihrer Anwendung mit dem folgenden Code (mit Aktualisierung der Schlüssel- und Endpunktvariablen mit Werten aus Ihrer Instanz der Formularerkennung im Azure-Portal):

```java
public class FormRecognizer {

    static final String key = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
    static final String endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";

    public static void main(String[] args) {

        DocumentAnalysisClient documentAnalysisClient = new DocumentAnalysisClientBuilder()
            .credential(new AzureKeyCredential("{key}"))
            .endpoint("{endpoint}")
            .buildClient();

        String documentUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf"
        String modelId = "prebuilt-layout";

        SyncPoller < DocumentOperationResult, AnalyzeResult > analyzeLayoutResultPoller =
            documentAnalysisClient.beginAnalyzeDocument(modelId, documentUrl);

        AnalyzeResult analyzeLayoutResult = analyzeLayoutResultPoller.getFinalResult();

        // pages
        analyzeLayoutResult.getPages().forEach(documentPage - > {
            System.out.printf("Page has width: %.2f and height: %.2f, measured with unit: %s%n",
                documentPage.getWidth(),
                documentPage.getHeight(),
                documentPage.getUnit());

            // lines
            documentPage.getLines().forEach(documentLine - >
                System.out.printf("Line %s is within a bounding box %s.%n",
                    documentLine.getContent(),
                    documentLine.getBoundingBox().toString()));

            // selection marks
            documentPage.getSelectionMarks().forEach(documentSelectionMark - >
                System.out.printf("Selection mark is %s and is within a bounding box %s with confidence %.2f.%n",
                    documentSelectionMark.getState().toString(),
                    documentSelectionMark.getBoundingBox().toString(),
                    documentSelectionMark.getConfidence()));
        });

        // tables
        List < DocumentTable > tables = analyzeLayoutResult.getTables();
        for (int i = 0; i < tables.size(); i++) {
            DocumentTable documentTable = tables.get(i);
            System.out.printf("Table %d has %d rows and %d columns.%n", i, documentTable.getRowCount(),
                documentTable.getColumnCount());
            documentTable.getCells().forEach(documentTableCell - > {
                System.out.printf("Cell '%s', has row index %d and column index %d.%n", documentTableCell.getContent(),
                    documentTableCell.getRowIndex(), documentTableCell.getColumnIndex());
            });
            System.out.println();
        }
    }
```

## <a name="try-it-prebuilt-model"></a>**Probieren Sie es aus:** Vordefiniertes Modell

In diesem Beispiel wird veranschaulicht, wie Sie Daten aus bestimmten Arten von häufig verwendeten Dokumenten mit vortrainierten Modellen analysieren, indem eine Rechnung als Beispiel verwendet wird.

> [!div class="checklist"]
>
> * In diesem Beispiel analysieren Sie ein Rechnungsdokument mithilfe eines vordefinierten Modells. Für diese Schnellstartanleitung können Sie unser [Beispiel für ein Rechnungsdokument](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf) verwenden.
> * Um eine bestimmte Datei an einem URI zu analysieren, verwenden Sie die `beginAnalyzeDocumentFromUrl`-Methode und übergeben `prebuilt-invoice` als Modell-ID. Der Rückgabewert ist ein `AnalyzeResult`-Objekt, das Daten zum übermittelten Dokument enthält.
> * Wir haben den Datei-URI-Wert der Variablen `invoiceUrl` in der main-Methode hinzugefügt.
> * Der Einfachheit halber werden hier nicht alle Schlüssel-Wert-Paare angezeigt, die der Dienst zurückgibt. Eine Liste aller unterstützten Felder mit den entsprechenden Typen finden Sie auf der Konzeptseite [Rechnung](../concept-invoice.md#field-extraction).

### <a name="choose-the-invoice-prebuilt-model-id"></a>Auswählen der ID für das vordefinierte Rechnungsmodell

Sie sind hierbei nicht auf Rechnungen beschränkt, sondern können zwischen mehreren vordefinierten Modellen wählen, von denen jedes über eine Gruppe unterstützter Felder verfügt. Welches Modell für den Analysevorgang verwendet wird, hängt vom Typ des zu analysierenden Dokuments ab. Hier sind die Modell-IDs für die vordefinierten Modelle angegeben, die vom Dienst „Formularerkennung“ derzeit unterstützt werden:

* [**prebuilt-invoice:**](../concept-invoice.md) Extrahieren von Text, Auswahlmarkierungen, Tabellen, Schlüssel-Wert-Paaren und wichtigen Informationen aus Rechnungen
* [**prebuilt-receipt:**](../concept-receipt.md) Extrahieren von Text und wichtigen Informationen aus Belegen
* [**prebuilt-idDocument:**](../concept-id-document.md) Extrahieren von Text und wichtigen Informationen aus Führerscheinen und Reisepässen
* [**prebuilt-businessCard:**](../concept-business-card.md) Extrahieren von Text und wichtigen Informationen aus Visitenkarten

Aktualisieren Sie die **FormRecognizer**-Klasse Ihrer Anwendung mit dem folgenden Code (mit Aktualisierung der Schlüssel- und Endpunktvariablen mit Werten aus Ihrer Instanz der Formularerkennung im Azure-Portal):

```java

public class FormRecognizer {

    static final String key = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
    static final String endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";

    public static void main(String[] args) {

        DocumentAnalysisClient documentAnalysisClient = new DocumentAnalysisClientBuilder()
            .credential(new AzureKeyCredential("{key}"))
            .endpoint("{endpoint}")
            .buildClient();

        String invoiceUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf"
        String modelId = "prebuilt-invoice";

        PollerFlux < DocumentOperationResult, AnalyzeResult > analyzeInvoicePoller = client.beginAnalyzeDocumentFromUrl("prebuilt-invoice", invoiceUrl);

        Mono < AnalyzeResult > analyzeInvoiceResultMono = analyzeInvoicePoller
            .last()
            .flatMap(pollResponse - > {
                if (pollResponse.getStatus().isComplete()) {
                    System.out.println("Polling completed successfully");
                    return pollResponse.getFinalResult();
                } else {
                    return Mono.error(new RuntimeException("Polling completed unsuccessfully with status:" +
                        pollResponse.getStatus()));
                }
            });

        analyzeInvoiceResultMono.subscribe(analyzeInvoiceResult - > {
            for (int i = 0; i < analyzeInvoiceResult.getDocuments().size(); i++) {
                AnalyzedDocument analyzedInvoice = analyzeInvoiceResult.getDocuments().get(i);
                Map < String, DocumentField > invoiceFields = analyzedInvoice.getFields();
                System.out.printf("----------- Analyzing invoice  %d -----------%n", i);
                DocumentField vendorNameField = invoiceFields.get("VendorName");
                if (vendorNameField != null) {
                    if (DocumentFieldType.STRING == vendorNameField.getType()) {
                        String merchantName = vendorNameField.getValueString();
                        System.out.printf("Vendor Name: %s, confidence: %.2f%n",
                            merchantName, vendorNameField.getConfidence());
                    }
                }

                DocumentField vendorAddressField = invoiceFields.get("VendorAddress");
                if (vendorAddressField != null) {
                    if (DocumentFieldType.STRING == vendorAddressField.getType()) {
                        String merchantAddress = vendorAddressField.getValueString();
                        System.out.printf("Vendor address: %s, confidence: %.2f%n",
                            merchantAddress, vendorAddressField.getConfidence());
                    }
                }

                DocumentField customerNameField = invoiceFields.get("CustomerName");
                if (customerNameField != null) {
                    if (DocumentFieldType.STRING == customerNameField.getType()) {
                        String merchantAddress = customerNameField.getValueString();
                        System.out.printf("Customer Name: %s, confidence: %.2f%n",
                            merchantAddress, customerNameField.getConfidence());
                    }
                }

                DocumentField customerAddressRecipientField = invoiceFields.get("CustomerAddressRecipient");
                if (customerAddressRecipientField != null) {
                    if (DocumentFieldType.STRING == customerAddressRecipientField.getType()) {
                        String customerAddr = customerAddressRecipientField.getValueString();
                        System.out.printf("Customer Address Recipient: %s, confidence: %.2f%n",
                            customerAddr, customerAddressRecipientField.getConfidence());
                    }
                }

                DocumentField invoiceIdField = invoiceFields.get("InvoiceId");
                if (invoiceIdField != null) {
                    if (DocumentFieldType.STRING == invoiceIdField.getType()) {
                        String invoiceId = invoiceIdField.getValueString();
                        System.out.printf("Invoice ID: %s, confidence: %.2f%n",
                            invoiceId, invoiceIdField.getConfidence());
                    }
                }

                DocumentField invoiceDateField = invoiceFields.get("InvoiceDate");
                if (customerNameField != null) {
                    if (DocumentFieldType.DATE == invoiceDateField.getType()) {
                        LocalDate invoiceDate = invoiceDateField.getValueDate();
                        System.out.printf("Invoice Date: %s, confidence: %.2f%n",
                            invoiceDate, invoiceDateField.getConfidence());
                    }
                }

                DocumentField invoiceTotalField = invoiceFields.get("InvoiceTotal");
                if (customerAddressRecipientField != null) {
                    if (DocumentFieldType.FLOAT == invoiceTotalField.getType()) {
                        Float invoiceTotal = invoiceTotalField.getValueFloat();
                        System.out.printf("Invoice Total: %.2f, confidence: %.2f%n",
                            invoiceTotal, invoiceTotalField.getConfidence());
                    }
                }

                DocumentField invoiceItemsField = invoiceFields.get("Items");
                if (invoiceItemsField != null) {
                    System.out.printf("Invoice Items: %n");
                    if (DocumentFieldType.LIST == invoiceItemsField.getType()) {
                        List < DocumentField > invoiceItems = invoiceItemsField.getValueList();
                        invoiceItems.stream()
                            .filter(invoiceItem - > DocumentFieldType.MAP == invoiceItem.getType())
                            .map(formField - > formField.getValueMap())
                            .forEach(formFieldMap - > formFieldMap.forEach((key, formField) - > {
                                // See a full list of fields found on an invoice here:
                                // https://aka.ms/formrecognizer/invoicefields
                                if ("Description".equals(key)) {
                                    if (DocumentFieldType.STRING == formField.getType()) {
                                        String name = formField.getValueString();
                                        System.out.printf("Description: %s, confidence: %.2fs%n",
                                            name, formField.getConfidence());
                                    }
                                }
                                if ("Quantity".equals(key)) {
                                    if (DocumentFieldType.FLOAT == formField.getType()) {
                                        Float quantity = formField.getValueFloat();
                                        System.out.printf("Quantity: %f, confidence: %.2f%n",
                                            quantity, formField.getConfidence());
                                    }
                                }
                                if ("UnitPrice".equals(key)) {
                                    if (DocumentFieldType.FLOAT == formField.getType()) {
                                        Float unitPrice = formField.getValueFloat();
                                        System.out.printf("Unit Price: %f, confidence: %.2f%n",
                                            unitPrice, formField.getConfidence());
                                    }
                                }
                                if ("ProductCode".equals(key)) {
                                    if (DocumentFieldType.FLOAT == formField.getType()) {
                                        Float productCode = formField.getValueFloat();
                                        System.out.printf("Product Code: %f, confidence: %.2f%n",
                                            productCode, formField.getConfidence());
                                    }
                                }
                            }));
                    }
                }
            }
        });
    }
}

```

## <a name="build-and-run-your-application"></a>Erstellen und Ausführen der Anwendung

Navigieren Sie zurück zum Hauptverzeichnis (**form-recognizer-app**) Ihres Projekts.

1. Erstellen Sie Ihre Anwendung mit dem Befehl `build`:

```console
gradle build
```

1. Führen Sie Ihre Anwendung mit dem Befehl `run` aus:

```console
gradle run
```

Herzlichen Glückwunsch! In dieser Schnellstartanleitung haben Sie das Java SDK für die Formularerkennung verwendet, um verschiedene Formulare und Dokumente auf unterschiedliche Arten zu analysieren. Lesen Sie als Nächstes die Referenzdokumentation, um die Formularerkennungs-API eingehender kennenzulernen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Referenzdokumentation zur REST-API v3.0](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)

> [!div class="nextstepaction"]
> [Referenz zur Java-Bibliothek für die Formularerkennung](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-formrecognizer/4.0.0-beta.1/index.html)
