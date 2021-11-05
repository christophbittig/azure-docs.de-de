---
title: 'Erste Schritte: Formularerkennungs-Clientbibliothek für .NET v2.1'
description: Verwenden Sie das Formularerkennungs-SDK für .NET, um eine Formularverarbeitungs-App zu erstellen, die Schlüssel-Wert-Paare und Tabellendaten aus Ihren benutzerdefinierten Dokumenten extrahiert.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: bface4c7df450e3123f8e4be73e5c942ff95a8cc
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030223"
---
<!-- markdownlint-disable MD024 -->

<!-- markdownlint-disable MD033 -->
> [!IMPORTANT]
>
> In dieser Schnellstartanleitung geht es um Version **2.1** der REST-API für die Azure-Formularerkennung mit cURL zum Ausführen von REST-API-Aufrufen.
>
>* Im Code dieses Artikels werden der Einfachheit halber synchrone Methoden und ein ungeschützter Anmeldeinformationsspeicher verwendet.

[Referenzdokumentation](/dotnet/api/overview/azure/ai.formrecognizer-readme) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src) | [Paket (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer) | [Beispiele](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

In dieser Schnellstartanleitung verwenden Sie die folgenden APIs zum Extrahieren strukturierter Daten aus Formularen und Dokumenten:

* [Layout](#try-it-layout-model)

* [Rechnung](#try-it-prebuilt-model)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services/)

* Aktuelle Version der [Visual Studio-IDE](https://visualstudio.microsoft.com/vs/) <!-- or [.NET Core](https://dotnet.microsoft.com/download). -->

* Eine Cognitive Services- oder Formularerkennungsressource. Wenn Sie über Ihr Azure-Abonnement verfügen, können Sie im Azure-Portal eine Formularerkennungsressource mit [einem Dienst](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) oder [mehreren Diensten](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) erstellen, um Ihren Schlüssel und Endpunkt zu erhalten. Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

    > [!TIP]
    > Erstellen Sie eine Cognitive Services-Ressource, wenn Sie planen, über einen einzelnen Endpunkt bzw. Schlüssel auf mehrere Cognitive Services-Instanzen zuzugreifen. Erstellen Sie eine Formularerkennungsressource, falls nur auf die Formularerkennung zugegriffen werden soll. Beachten Sie hierbei, dass Sie eine Ressource mit einem einzelnen Dienst benötigen, falls Sie die [Azure Active Directory-Authentifizierung](/azure/active-directory/authentication/overview-authentication) nutzen möchten.

* Klicken Sie nach der Bereitstellung Ihrer Ressource auf **Zu Ressource wechseln**. Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Formularerkennungs-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt:

  :::image type="content" source="../../media/containers/keys-and-endpoint.png" alt-text="Screenshot: Schlüssel und Endpunktspeicherort im Azure-Portal.":::

## <a name="set-up"></a>Einrichten

1. Starten Sie Visual Studio 2019.

1. Wählen Sie auf der Startseite Neues Projekt erstellen aus.

    :::image type="content" source="../../media/quickstarts/start-window.png" alt-text="Screenshot: Visual Studio-Startfenster":::

1. Geben Sie auf der Seite **Neues Projekt erstellen** im Suchfeld den Suchbegriff **Konsole** ein. Wählen Sie die Vorlage **Konsolenanwendung** und dann **Weiter** aus.

    :::image type="content" source="../../media/quickstarts/create-new-project.png" alt-text="Screenshot: Seite „Neues Projekt erstellen“ in Visual Studio":::

1. Geben Sie im Dialogfeld **Neues Projekt konfigurieren** im Feld für den Projektnamen den Namen `formRecognizer_quickstart` ein. Wählen Sie anschließend „Weiter“ aus.

    :::image type="content" source="../../media/quickstarts/configure-new-project.png" alt-text="Screenshot: Dialogfeld „Neues Projekt konfigurieren“ in Visual Studio":::

1. Wählen Sie im Dialogfeld **Weitere Informationen** die Option **.NET 5.0 (aktuell)** und dann **Erstellen** aus.

    :::image type="content" source="../../media/quickstarts/additional-information.png" alt-text="Screenshot: Dialogfeld „Weitere Informationen“ in Visual Studio":::

### <a name="install-the-client-library-with-nuget"></a>Installieren der Clientbibliothek mit NuGet

 1. Klicken Sie mit der rechten Maustaste auf das Projekt **formRecognizer_quickstart**, und wählen Sie die Option **NuGet-Pakete verwalten...** aus.

    :::image type="content" source="../../media/quickstarts/select-nuget-package.png" alt-text="Screenshot: select-nuget-package.png":::

 1. Wählen Sie die Registerkarte „Durchsuchen“ aus, und geben Sie „Azure.AI.FormRecognizer“ ein.

     :::image type="content" source="../../media/quickstarts/azure-nuget-package.png" alt-text="Screenshot: select-form-recognizer-package.png":::

 1. Wählen Sie im Dropdownmenü die Version **3.1.1** und anschließend die Option **Installieren** aus.

## <a name="build-your-application"></a>Erstellen Ihrer Anwendung

Für die Interaktion mit dem Dienst „Formularerkennung“ müssen Sie eine Instanz der `FormRecognizerClient`-Klasse erstellen. Erstellen Sie hierfür ein `AzureKeyCredential`-Element mit Ihrem „apiKey“ und eine `FormRecognizerClient`-Instanz mit dem `AzureKeyCredential`-Element und Ihrem `endpoint` für die Formularerkennung.

1. Öffnen Sie die Datei **Program.cs**.

1. Fügen Sie die folgenden using-Direktiven ein:

```csharp
using System;
using Azure;
using Azure.AI.FormRecognizer;
using Azure.AI.FormRecognizer.Models;
using System.Threading.Tasks;
```

1. Legen Sie Ihre Umgebungsvariablen `endpoint` und `apiKey` fest, und erstellen Sie Ihre Instanzen von `AzureKeyCredential` und `FormRecognizerClient`:

```csharp
private static readonly string endpoint = "your-form-recognizer-endpoint";
private static readonly string apiKey = "your-api-key";
private static readonly AzureKeyCredential credential = new AzureKeyCredential(apiKey);
```

1. Löschen Sie die Zeile `Console.Writeline("Hello World!");`, und fügen Sie die Codebeispiele für **Try It** (Ausprobieren) der **Main**-Methode in der Datei **Program.cs** hinzu:

    :::image type="content" source="../../media/quickstarts/add-code-here.png" alt-text="Screenshot: Hinzufügen des Beispielcodes zur „Main“-Methode":::

1. Wählen Sie ein Codebeispiel zum Kopieren und Einfügen in die Main-Methode Ihrer Anwendung aus:

    * [**Layout**](#try-it-layout-model)

    * [**Vordefinierte Rechnung**](#try-it-prebuilt-model)

> [!IMPORTANT]
>
> Denken Sie daran, den Schlüssel aus Ihrem Code zu entfernen, wenn Sie fertig sind, und ihn niemals zu veröffentlichen. Verwenden Sie in der Produktionsumgebung sichere Methoden, um Ihre Anmeldeinformationen zu speichern und darauf zuzugreifen. Weitere Informationen finden Sie im Artikel zur Cognitive Services-[Sicherheit](.(/azure/cognitive-services/cognitive-services-security.md).

## <a name="try-it-layout-model"></a>**Try it** (Ausprobieren): Layoutmodell

Dient zum Extrahieren von Text, Auswahlmarkierungen, Textformaten und Tabellenstrukturen sowie der zugehörigen Begrenzungsbereichskoordinaten aus Dokumenten.

> [!div class="checklist"]
>
> * Für dieses Beispiel benötigen Sie eine **Formulardokumentdatei unter einem URI**. Sie können für diese Schnellstartanleitung unser [Beispielformulardokument](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) verwenden.
> * Wir haben den Datei-URI-Wert der Variablen `formUri` hinzugefügt.
> * Verwenden Sie zum Extrahieren des Layouts aus einer bestimmten Datei unter einem URI die `StartRecognizeContentFromUriAsync`-Methode.

### <a name="add-the-following-code-to-your-layout-application-main-method"></a>Fügen Sie der **Main**-Methode Ihrer Layoutanwendung den folgenden Code hinzu:

```csharp

FormRecognizerClient recognizerClient = AuthenticateClient();

Task recognizeContent = RecognizeContent(recognizerClient);
Task.WaitAll(recognizeContent);
```

### <a name="add-the-following-code-below-the-main-method"></a>Fügen Sie den folgenden Code unterhalb der **Main**-Methode hinzu:

```csharp

private static FormRecognizerClient AuthenticateClient()
            {
                var credential = new AzureKeyCredential(apiKey);
                var client = new FormRecognizerClient(new Uri(endpoint), credential);
                return client;
            }

            private static async Task RecognizeContent(FormRecognizerClient recognizerClient)
        {
            string formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf";
            FormPageCollection formPages = await recognizerClient
        .StartRecognizeContentFromUri(new Uri(formUrl))
        .WaitForCompletionAsync();

            foreach (FormPage page in formPages)
            {
                Console.WriteLine($"Form Page {page.PageNumber} has {page.Lines.Count} lines.");

                for (int i = 0; i < page.Lines.Count; i++)
                {
                    FormLine line = page.Lines[i];
                    Console.WriteLine($"    Line {i} has {line.Words.Count} word{(line.Words.Count > 1 ? "s" : "")}, and text: '{line.Text}'.");
                }

                for (int i = 0; i < page.Tables.Count; i++)
                {
                    FormTable table = page.Tables[i];
                    Console.WriteLine($"Table {i} has {table.RowCount} rows and {table.ColumnCount} columns.");
                    foreach (FormTableCell cell in table.Cells)
                    {
                        Console.WriteLine($"    Cell ({cell.RowIndex}, {cell.ColumnIndex}) contains text: '{cell.Text}'.");
                    }
                }
            }
        }
    }
}

```

## <a name="try-it-prebuilt-model"></a>**Probieren Sie es aus:** Vordefiniertes Modell

In diesem Beispiel wird veranschaulicht, wie Sie Daten aus bestimmten Arten von häufig verwendeten Dokumenten mit vortrainierten Modellen analysieren, indem eine Rechnung als Beispiel verwendet wird.

> [!div class="checklist"]
>
> * In diesem Beispiel analysieren Sie ein Rechnungsdokument mithilfe eines vordefinierten Modells. Für diese Schnellstartanleitung können Sie unser [Beispiel für ein Rechnungsdokument](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf) verwenden.
> * Sie haben den Datei-URI-Wert oben in der Main-Methode der Variable `invoiceUri` hinzugefügt.
> * Verwenden Sie für die Analyse einer bestimmten Datei unter einem URI die `StartRecognizeInvoicesFromUriAsync`-Methode.
> * Der Einfachheit halber werden hier nicht alle Felder angezeigt, die vom Dienst zurückgegeben werden. Eine Liste aller unterstützten Felder mit den entsprechenden Typen finden Sie auf der Konzeptseite [Rechnung](../../concept-invoice.md#field-extraction).

### <a name="choose-a-prebuilt-model"></a>Auswählen eines vordefinierten Modells

Sie sind hierbei nicht auf Rechnungen beschränkt, sondern können zwischen mehreren vordefinierten Modellen wählen, von denen jedes über eine Gruppe unterstützter Felder verfügt. Welches Modell für den Analysevorgang verwendet wird, hängt vom Typ des zu analysierenden Dokuments ab. Dies sind die vordefinierten Modelle, die vom Formularerkennungsdienst derzeit unterstützt werden:

* [**Rechnung**](../../concept-invoice.md): Extrahieren von Text, Auswahlmarkierungen, Tabellen, Feldern und wichtigen Informationen aus Rechnungen
* [**Beleg**](../../concept-receipt.md): Extrahieren von Text und wichtigen Informationen aus Belegen
* [**Ausweisdokument**](../../concept-id-document.md): Extrahieren von Text und wichtigen Informationen aus Führerscheinen und Reisepässen
* [**Visitenkarte**](../../concept-business-card.md): Extrahieren von Text und wichtigen Informationen aus Visitenkarten

### <a name="add-the-following-code-to-your-prebuilt-invoice-application-main-method"></a>Fügen Sie der **Main**-Methode Ihrer vordefinierten Rechnungsanwendung den folgenden Code hinzu:

```csharp
FormRecognizerClient recognizerClient = AuthenticateClient();

            Task analyzeinvoice = AnalyzeInvoice(recognizerClient, invoiceUrl);
            Task.WaitAll(analyzeinvoice);
```

### <a name="add-the-following-code-below-the-main-method"></a>Fügen Sie den folgenden Code unterhalb der **Main**-Methode hinzu:

```csharp
   private static FormRecognizerClient AuthenticateClient() {
     var credential = new AzureKeyCredential(apiKey);
     var client = new FormRecognizerClient(new Uri(endpoint), credential);
     return client;
   }

   static string invoiceUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf";

   private static async Task AnalyzeInvoice(FormRecognizerClient recognizerClient, string invoiceUrl) {
     var options = new RecognizeInvoicesOptions() {
       Locale = "en-US"
     };
     RecognizedFormCollection invoices = await recognizerClient.StartRecognizeInvoicesFromUriAsync(new Uri(invoiceUrl), options).WaitForCompletionAsync();

     RecognizedForm invoice = invoices[0];

     FormField invoiceIdField;
     if (invoice.Fields.TryGetValue("InvoiceId", out invoiceIdField)) {
       if (invoiceIdField.Value.ValueType == FieldValueType.String) {
         string invoiceId = invoiceIdField.Value.AsString();
         Console.WriteLine($ "    Invoice Id: '{invoiceId}', with confidence {invoiceIdField.Confidence}");
       }
     }

     FormField invoiceDateField;
     if (invoice.Fields.TryGetValue("InvoiceDate", out invoiceDateField)) {
       if (invoiceDateField.Value.ValueType == FieldValueType.Date) {
         DateTime invoiceDate = invoiceDateField.Value.AsDate();
         Console.WriteLine($ "    Invoice Date: '{invoiceDate}', with confidence {invoiceDateField.Confidence}");
       }
     }

     FormField dueDateField;
     if (invoice.Fields.TryGetValue("DueDate", out dueDateField)) {
       if (dueDateField.Value.ValueType == FieldValueType.Date) {
         DateTime dueDate = dueDateField.Value.AsDate();
         Console.WriteLine($ "    Due Date: '{dueDate}', with confidence {dueDateField.Confidence}");
       }
     }

     FormField vendorNameField;
     if (invoice.Fields.TryGetValue("VendorName", out vendorNameField)) {
       if (vendorNameField.Value.ValueType == FieldValueType.String) {
         string vendorName = vendorNameField.Value.AsString();
         Console.WriteLine($ "    Vendor Name: '{vendorName}', with confidence {vendorNameField.Confidence}");
       }
     }

     FormField vendorAddressField;
     if (invoice.Fields.TryGetValue("VendorAddress", out vendorAddressField)) {
       if (vendorAddressField.Value.ValueType == FieldValueType.String) {
         string vendorAddress = vendorAddressField.Value.AsString();
         Console.WriteLine($ "    Vendor Address: '{vendorAddress}', with confidence {vendorAddressField.Confidence}");
       }
     }

     FormField customerNameField;
     if (invoice.Fields.TryGetValue("CustomerName", out customerNameField)) {
       if (customerNameField.Value.ValueType == FieldValueType.String) {
         string customerName = customerNameField.Value.AsString();
         Console.WriteLine($ "    Customer Name: '{customerName}', with confidence {customerNameField.Confidence}");
       }
     }

     FormField customerAddressField;
     if (invoice.Fields.TryGetValue("CustomerAddress", out customerAddressField)) {
       if (customerAddressField.Value.ValueType == FieldValueType.String) {
         string customerAddress = customerAddressField.Value.AsString();
         Console.WriteLine($ "    Customer Address: '{customerAddress}', with confidence {customerAddressField.Confidence}");
       }
     }

     FormField customerAddressRecipientField;
     if (invoice.Fields.TryGetValue("CustomerAddressRecipient", out customerAddressRecipientField)) {
       if (customerAddressRecipientField.Value.ValueType == FieldValueType.String) {
         string customerAddressRecipient = customerAddressRecipientField.Value.AsString();
         Console.WriteLine($ "    Customer address recipient: '{customerAddressRecipient}', with confidence {customerAddressRecipientField.Confidence}");
       }
     }

     FormField invoiceTotalField;
     if (invoice.Fields.TryGetValue("InvoiceTotal", out invoiceTotalField)) {
       if (invoiceTotalField.Value.ValueType == FieldValueType.Float) {
         float invoiceTotal = invoiceTotalField.Value.AsFloat();
         Console.WriteLine($ "    Invoice Total: '{invoiceTotal}', with confidence {invoiceTotalField.Confidence}");
       }
     }
   }
 }
}
```

## <a name="run-your-application"></a>Ausführen der Anwendung

Wählen Sie die grüne Schaltfläche **Starten** neben „formRecognizer_quickstart“ aus, um Ihr Programm zu kompilieren und auszuführen, oder drücken Sie **F5**.

  :::image type="content" source="../../media/quickstarts/run-visual-studio.png" alt-text="Screenshot: Ausführen Ihres Visual Studio-Programms":::

<!-- --- -->

Herzlichen Glückwunsch! In dieser Schnellstartanleitung haben Sie das C# SDK für die Formularerkennung verwendet, um verschiedene Formulare und Dokumente auf unterschiedliche Arten zu analysieren. Lesen Sie als Nächstes die Referenzdokumentation, um die Formularerkennungs-API eingehender kennenzulernen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Referenzdokumentation zur REST-API v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)

> [!div class="nextstepaction"]
> [C#/.NET-Referenzbibliothek für die Formularerkennung](/dotnet/api/overview/azure/AI.FormRecognizer-readme)
