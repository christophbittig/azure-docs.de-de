---
title: 'Erste Schritte: Formularerkennungs-Clientbibliothek für Java v2.1'
description: Verwenden Sie das Formularerkennungs-SDK für Java, um eine Formularverarbeitungs-App zu erstellen, die Schlüssel-Wert-Paare und Tabellendaten aus Ihren benutzerdefinierten Dokumenten extrahiert.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4e93e5b157cc7bb17eb2e66a97770f4af0d915c2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030228"
---
> [!IMPORTANT]
>
> In dieser Schnellstartanleitung geht es um Version **2.1** der Azure-Formularerkennung.

[Referenzdokumentation](/java/api/overview/azure/ai-formrecognizer-readme) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src) | [Paket (Maven)](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer) | [Beispiele](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)

In dieser Schnellstartanleitung verwenden Sie die folgenden APIs zum Extrahieren strukturierter Daten aus Formularen und Dokumenten:

* [Layout](#try-it-layout-model)

* [Rechnung](#try-it-prebuilt-model)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services/)
* [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable&preserve-view=true
), Version 8 oder höher
* Eine Cognitive Services- oder Formularerkennungsressource. Wenn Sie über Ihr Azure-Abonnement verfügen, können Sie im Azure-Portal eine Formularerkennungsressource mit [einem Dienst](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) oder [mehreren Diensten](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) erstellen, um Ihren Schlüssel und Endpunkt zu erhalten. Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

> [!TIP]
> Erstellen Sie eine Cognitive Services-Ressource, wenn Sie planen, über einen einzelnen Endpunkt bzw. Schlüssel auf mehrere Cognitive Services-Instanzen zuzugreifen. Erstellen Sie eine Formularerkennungsressource, falls nur auf die Formularerkennung zugegriffen werden soll. Beachten Sie hierbei, dass Sie eine Ressource mit einem einzelnen Dienst benötigen, falls Sie die [Azure Active Directory-Authentifizierung](/azure/active-directory/authentication/overview-authentication) nutzen möchten.

* Klicken Sie nach der Bereitstellung Ihrer Ressource auf **Zu Ressource wechseln**. Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Formularerkennungs-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt:

  :::image type="content" source="../../media/containers/keys-and-endpoint.png" alt-text="Screenshot: Schlüssel und Endpunktspeicherort im Azure-Portal.":::

## <a name="set-up"></a>Einrichten

### <a name="create-a-new-gradle-project"></a>Erstellen eines neuen Gradle-Projekts

Erstellen Sie in einem Konsolenfenster (z. B. cmd, PowerShell oder Bash) ein neues Verzeichnis für Ihre App mit dem Namen **form-recognizer-app**, und navigieren Sie zu diesem Verzeichnis.

```console
mkdir form-recognizer-app && form-recognizer-app
```

1. Führen Sie den Befehl `gradle init` in Ihrem Arbeitsverzeichnis aus. Mit diesem Befehl werden grundlegende Builddateien für Gradle, u. a. die Datei *build.gradle.kts*, erstellt. Diese Datei wird zur Laufzeit zum Erstellen und Konfigurieren Ihrer Anwendung verwendet.

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
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "3.1.1")
}
```

### <a name="create-a-java-file"></a>Erstellen einer Java-Datei

Führen Sie in Ihrem Arbeitsverzeichnis den folgenden Befehl aus:

```console
mkdir -p src/main/java
```

Sie erstellen die folgende Verzeichnisstruktur:

:::image type="content" source="../../media/quickstarts/java-directories.png" alt-text="Screenshot: Java-Verzeichnisstruktur":::

Navigieren Sie zum Java-Verzeichnis, und erstellen Sie eine Datei mit dem Namen *FormRecognizer.java*.  Öffnen Sie sie in Ihrem bevorzugten Editor bzw. Ihrer bevorzugten IDE, und fügen Sie die folgende Paketdeklaration und die folgenden `import`-Anweisungen hinzu:

```java
import com.azure.ai.formrecognizer.*;
import com.azure.ai.formrecognizer.models.*;

import java.util.concurrent.atomic.AtomicReference;
import java.util.List;
import java.util.Map;
import java.time.LocalDate;

import com.azure.core.credential.AzureKeyCredential;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.util.Context;
import com.azure.core.util.polling.SyncPoller;
```

### <a name="select-a-code-sample-to-copy-and-paste-into-your-applications-main-method"></a>Wählen Sie ein Codebeispiel zum Kopieren und Einfügen in die main-Methode Ihrer Anwendung aus:

* [**Layout**](#try-it-layout-model)

* [**Vordefinierte Rechnung**](#try-it-prebuilt-model)

> [!IMPORTANT]
>
> Denken Sie daran, den Schlüssel aus Ihrem Code zu entfernen, wenn Sie fertig sind, und ihn niemals zu veröffentlichen. Verwenden Sie in der Produktionsumgebung sichere Methoden, um Ihre Anmeldeinformationen zu speichern und darauf zuzugreifen. Weitere Informationen finden Sie im Cognitive Services-Artikel zur [Sicherheit](/azure/cognitive-services/cognitive-services-security.md).

## <a name="try-it-layout-model"></a>**Try it** (Ausprobieren): Layoutmodell

Dient zum Extrahieren von Text, Auswahlmarkierungen, Textformaten und Tabellenstrukturen sowie der zugehörigen Begrenzungsbereichskoordinaten aus Dokumenten.

> [!div class="checklist"]
>
> * Für dieses Beispiel benötigen Sie eine **Formulardokumentdatei unter einem URI**. Sie können für diese Schnellstartanleitung unser [Beispielformulardokument](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) verwenden.
> * Für die Analyse einer bestimmten Datei unter einem URI verwenden Sie die `beginRecognizeContentFromUrl`-Methode.
> * Wir haben den Datei-URI-Wert der Variablen `formUrl` in der main-Methode hinzugefügt.

Aktualisieren Sie die **FormRecognizer**-Klasse Ihrer Anwendung mit dem folgenden Code (mit Aktualisierung der Schlüssel- und Endpunktvariablen mit Werten aus Ihrer Instanz der Formularerkennung im Azure-Portal):

```java

static final String key = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
static final String endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";

public static void main(String[] args) {FormRecognizerClient recognizerClient = new FormRecognizerClientBuilder()
                .credential(new AzureKeyCredential(key)).endpoint(endpoint).buildClient();

    String formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf";

    System.out.println("Get form content...");
        GetContent(recognizerClient, formUrl);
  }
    private static void GetContent(FormRecognizerClient recognizerClient, String invoiceUri) {
        String analyzeFilePath = invoiceUri;
        SyncPoller<FormRecognizerOperationResult, List<FormPage>> recognizeContentPoller = recognizerClient
                .beginRecognizeContentFromUrl(analyzeFilePath);

        List<FormPage> contentResult = recognizeContentPoller.getFinalResult();
        // </snippet_getcontent_call>
        // <snippet_getcontent_print>
        contentResult.forEach(formPage -> {
            // Table information
            System.out.println("----Recognizing content ----");
            System.out.printf("Has width: %f and height: %f, measured with unit: %s.%n", formPage.getWidth(),
                    formPage.getHeight(), formPage.getUnit());
            formPage.getTables().forEach(formTable -> {
                System.out.printf("Table has %d rows and %d columns.%n", formTable.getRowCount(),
                        formTable.getColumnCount());
                formTable.getCells().forEach(formTableCell -> {
                    System.out.printf("Cell has text %s.%n", formTableCell.getText());
                });
                System.out.println();
            });
        });
    }

```

## <a name="try-it-prebuilt-model"></a>**Probieren Sie es aus:** Vordefiniertes Modell

In diesem Beispiel wird veranschaulicht, wie Sie Daten aus bestimmten Arten von häufig verwendeten Dokumenten mit vortrainierten Modellen analysieren, indem eine Rechnung als Beispiel verwendet wird.

> [!div class="checklist"]
>
> * In diesem Beispiel analysieren Sie ein Rechnungsdokument mithilfe eines vordefinierten Modells. Für diese Schnellstartanleitung können Sie unser [Beispiel für ein Rechnungsdokument](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf) verwenden.
> * Für die Analyse einer bestimmten Datei unter einem URI verwenden Sie `beginRecognizeInvoicesFromUrl`.
> * Wir haben den Datei-URI-Wert der Variablen `invoiceUrl` in der main-Methode hinzugefügt.
> * Der Einfachheit halber werden hier nicht alle Felder angezeigt, die vom Dienst zurückgegeben werden. Eine Liste aller unterstützten Felder mit den entsprechenden Typen finden Sie auf der Konzeptseite [Rechnung](../../concept-invoice.md#field-extraction).

### <a name="choose-a-prebuilt-model"></a>Auswählen eines vordefinierten Modells

Sie sind hierbei nicht auf Rechnungen beschränkt, sondern können zwischen mehreren vordefinierten Modellen wählen, von denen jedes über eine Gruppe unterstützter Felder verfügt. Welches Modell für den Analysevorgang verwendet wird, hängt vom Typ des zu analysierenden Dokuments ab. Dies sind die vordefinierten Modelle, die vom Formularerkennungsdienst derzeit unterstützt werden:

* [**Rechnung**](../../concept-invoice.md): Extrahieren von Text, Auswahlmarkierungen, Tabellen, Feldern und wichtigen Informationen aus Rechnungen
* [**Beleg**](../../concept-receipt.md): Extrahieren von Text und wichtigen Informationen aus Belegen
* [**Ausweisdokument**](../../concept-id-document.md): Extrahieren von Text und wichtigen Informationen aus Führerscheinen und Reisepässen
* [**Visitenkarte**](../../concept-business-card.md): Extrahieren von Text und wichtigen Informationen aus Visitenkarten

Aktualisieren Sie die **FormRecognizer**-Klasse Ihrer Anwendung mit dem folgenden Code (mit Aktualisierung der Schlüssel- und Endpunktvariablen mit Werten aus Ihrer Instanz der Formularerkennung im Azure-Portal):

```java

static final String key = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
static final String endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";

public static void main(String[] args) {
    FormRecognizerClient recognizerClient = new FormRecognizerClientBuilder().credential(new AzureKeyCredential(key)).endpoint(endpoint).buildClient();

    String invoiceUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf";

    System.out.println("Analyze invoice...");
        AnalyzeInvoice(recognizerClient, invoiceUrl);
  }
    private static void AnalyzeInvoice(FormRecognizerClient recognizerClient, String invoiceUrl) {
      SyncPoller < FormRecognizerOperationResult,
        List < RecognizedForm >> recognizeInvoicesPoller = recognizerClient.beginRecognizeInvoicesFromUrl(invoiceUrl);
      List < RecognizedForm > recognizedInvoices = recognizeInvoicesPoller.getFinalResult();

      for (int i = 0; i < recognizedInvoices.size(); i++) {
        RecognizedForm recognizedInvoice = recognizedInvoices.get(i);
        Map < String,
        FormField > recognizedFields = recognizedInvoice.getFields();
        System.out.printf("----------- Recognized invoice info for page %d -----------%n", i);
        FormField vendorNameField = recognizedFields.get("VendorName");
        if (vendorNameField != null) {
            if (FieldValueType.STRING == vendorNameField.getValue().getValueType()) {
                String merchantName = vendorNameField.getValue().asString();
                System.out.printf("Vendor Name: %s, confidence: %.2f%n", merchantName, vendorNameField.getConfidence());
            }
        }

        FormField vendorAddressField = recognizedFields.get("VendorAddress");
        if (vendorAddressField != null) {
            if (FieldValueType.STRING == vendorAddressField.getValue().getValueType()) {
                String merchantAddress = vendorAddressField.getValue().asString();
                System.out.printf("Vendor address: %s, confidence: %.2f%n", merchantAddress, vendorAddressField.getConfidence());
            }
        }

        FormField customerNameField = recognizedFields.get("CustomerName");
        if (customerNameField != null) {
            if (FieldValueType.STRING == customerNameField.getValue().getValueType()) {
                String merchantAddress = customerNameField.getValue().asString();
                System.out.printf("Customer Name: %s, confidence: %.2f%n", merchantAddress, customerNameField.getConfidence());
            }
        }

        FormField customerAddressRecipientField = recognizedFields.get("CustomerAddressRecipient");
        if (customerAddressRecipientField != null) {
            if (FieldValueType.STRING == customerAddressRecipientField.getValue().getValueType()) {
                String customerAddr = customerAddressRecipientField.getValue().asString();
                System.out.printf("Customer Address Recipient: %s, confidence: %.2f%n", customerAddr, customerAddressRecipientField.getConfidence());
            }
        }

        FormField invoiceIdField = recognizedFields.get("InvoiceId");
        if (invoiceIdField != null) {
            if (FieldValueType.STRING == invoiceIdField.getValue().getValueType()) {
                String invoiceId = invoiceIdField.getValue().asString();
                System.out.printf("Invoice Id: %s, confidence: %.2f%n", invoiceId, invoiceIdField.getConfidence());
            }
        }

        FormField invoiceDateField = recognizedFields.get("InvoiceDate");
        if (customerNameField != null) {
            if (FieldValueType.DATE == invoiceDateField.getValue().getValueType()) {
                LocalDate invoiceDate = invoiceDateField.getValue().asDate();
                System.out.printf("Invoice Date: %s, confidence: %.2f%n", invoiceDate, invoiceDateField.getConfidence());
            }
        }

        FormField invoiceTotalField = recognizedFields.get("InvoiceTotal");
        if (customerAddressRecipientField != null) {
            if (FieldValueType.FLOAT == invoiceTotalField.getValue().getValueType()) {
                Float invoiceTotal = invoiceTotalField.getValue().asFloat();
                System.out.printf("Invoice Total: %.2f, confidence: %.2f%n", invoiceTotal, invoiceTotalField.getConfidence());
            }
        }
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
> [Referenzdokumentation zur REST-API v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)

> [!div class="nextstepaction"]
> [Referenz zur Java-Bibliothek für die Formularerkennung](/java/api/overview/azure/ai-formrecognizer-readme?view=azure-java-stable&preserve-view=true)
