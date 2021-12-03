---
title: 'Schnellstart: C# SDK für Formularerkennung v3.0 | Vorschau'
titleSuffix: Azure Applied AI Services
description: Formular- und Dokumentverarbeitung, Datenextraktion und Analyse mit C#-Clientbibliothek-SDKs für Formularerkennung v3.0 (Vorschau)
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4ec6d3ea7467edc08f380077790f67324c1b2371
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131851323"
---
# <a name="quickstart-c-client-library-sdk-v30--preview"></a>Schnellstart: C#-Clientbibliothek-SDK v3.0 | Vorschau

>[!NOTE]
> Formularerkennung v3.0 befindet sich derzeit in der öffentlichen Vorschauphase. Dies bedeutet, dass einige Features unter Umständen nicht unterstützt werden oder nur eingeschränkt verwendbar sind.

[Referenzdokumentation](/dotnet/api/overview/azure/ai.formrecognizer-readme?view=azure-dotnet&preserve-view=true ) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.FormRecognizer_4.0.0-beta.1/sdk/formrecognizer/Azure.AI.FormRecognizer/src) | [Paket (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer) | [Beispiele](https://github.com/Azure/azure-sdk-for-net/blob/Azure.AI.FormRecognizer_4.0.0-beta.1/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

Informieren Sie sich über die ersten Schritte mit der Azure-Formularerkennung mit der Programmiersprache C#. Die Azure-Formularerkennung ist ein cloudbasierter Dienst von Azure Applied AI Services, bei dem maschinelles Lernen genutzt wird, um Formularfelder, Text und Tabellen aus Ihren Dokumenten zu extrahieren und zu analysieren. Sie können Formularerkennungsmodelle leicht aufrufen, indem Sie unsere Clientbibliothek-SDKs in Ihre Workflows und Anwendungen integrieren. Sie sollten den kostenlosen Dienst nutzen, wenn Sie die Technologie erlernen. Bedenken Sie, dass die Anzahl der kostenlosen Seiten auf 500 pro Monat beschränkt ist.

Weitere Informationen zu Funktionen der Formularerkennung und zu Entwicklungsoptionen finden Sie auf unserer [Übersichtsseite](../overview.md#form-recognizer-features-and-development-options).

In dieser Schnellstartanleitung verwenden Sie die folgenden Funktionen, um Daten und Werte aus Formularen und Dokumenten zu analysieren und zu extrahieren:

* [🆕 **Allgemeines Dokument:**](#try-it-general-document-model) Analysieren und Extrahieren von Text, Tabellen, Strukturen, Schlüssel-Wert-Paaren und benannten Entitäten

* [**Layout:**](#try-it-layout-model) Analysieren und Extrahieren von Tabellen, Zeilen, Wörtern und Auswahlmarkierungen, z. B. Optionsfeldern und Kontrollkästchen in Formulardokumenten, ohne ein Modell trainieren zu müssen

* [**Vordefiniertes Modell (Rechnung):**](#try-it-prebuilt-model) Analysieren und Extrahieren häufig verwendeter Felder aus Rechnungen über ein vortrainiertes Rechnungsmodell

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services/)

* Aktuelle Version der [Visual Studio-IDE](https://visualstudio.microsoft.com/vs/) <!-- or [.NET Core](https://dotnet.microsoft.com/download). -->

* Eine Cognitive Services- oder Formularerkennungsressource. Wenn Sie über Ihr Azure-Abonnement verfügen, können Sie im Azure-Portal eine Formularerkennungsressource mit [einem Dienst](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) oder [mehreren Diensten](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) erstellen, um Ihren Schlüssel und Endpunkt zu erhalten. Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

> [!TIP]
> Erstellen Sie eine Cognitive Services-Ressource, wenn Sie planen, über einen einzelnen Endpunkt bzw. Schlüssel auf mehrere Cognitive Services-Instanzen zuzugreifen. Erstellen Sie eine Formularerkennungsressource, falls nur auf die Formularerkennung zugegriffen werden soll. Beachten Sie hierbei, dass Sie eine Ressource mit einem einzelnen Dienst benötigen, falls Sie die [Azure Active Directory-Authentifizierung](../../../active-directory/authentication/overview-authentication.md) nutzen möchten.

* Wählen Sie nach der Bereitstellung der Ressource **Zu Ressource wechseln** aus. Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Formularerkennungs-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten im Schnellstart in den Code eingefügt:

  :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="Screenshot: Schlüssel und Endpunktspeicherort im Azure-Portal.":::

## <a name="set-up"></a>Einrichten

<!--- 
### [Option 1: .NET Command-line interface (CLI)](#tab/cli)

In a console window (such as cmd, PowerShell, or Bash), use the `dotnet new` command to create a new console app with the name `formrecognizer-quickstart`. This command creates a simple "Hello World" C# project with a single source file: *Program.cs*.

```console
dotnet new console -n formrecognizer-quickstart
```

Open a command line and switch to the directory that contains your project file. Build the application with:

```console
dotnet build
```

The build output should contain no warnings or errors.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### Install the client library with NuGet

In the directory that contains your project, install the Form Recognizer client library for .NET with the following command:

```console
dotnet add package Azure.AI.FormRecognizer
```

This version of the client library defaults to the 2021-09-30-preview version of the service.

### [Option 2: Visual Studio](#tab/vs)
--->

1. Starten Sie Visual Studio 2019.

1. Wählen Sie auf der Startseite Neues Projekt erstellen aus.

    :::image type="content" source="../media/quickstarts/start-window.png" alt-text="Screenshot: Visual Studio-Startfenster":::

1. Geben Sie auf der Seite **Neues Projekt erstellen** im Suchfeld den Suchbegriff **Konsole** ein. Wählen Sie die Vorlage **Konsolenanwendung** und dann **Weiter** aus.

    :::image type="content" source="../media/quickstarts/create-new-project.png" alt-text="Screenshot: Seite „Neues Projekt erstellen“ in Visual Studio":::

1. Geben Sie im Dialogfeld **Neues Projekt konfigurieren** im Feld für den Projektnamen den Namen `formRecognizer_quickstart` ein. Wählen Sie anschließend „Weiter“ aus.

    :::image type="content" source="../media/quickstarts/configure-new-project.png" alt-text="Screenshot: Dialogfeld „Neues Projekt konfigurieren“ in Visual Studio":::

1. Wählen Sie im Dialogfeld **Weitere Informationen** die Option **.NET 5.0 (aktuell)** und dann **Erstellen** aus.

    :::image type="content" source="../media/quickstarts/additional-information.png" alt-text="Screenshot: Dialogfeld „Weitere Informationen“ in Visual Studio":::

### <a name="install-the-client-library-with-nuget"></a>Installieren der Clientbibliothek mit NuGet

 1. Klicken Sie mit der rechten Maustaste auf das Projekt **formRecognizer_quickstart**, und wählen Sie die Option **NuGet-Pakete verwalten...** aus.

    :::image type="content" source="../media/quickstarts/select-nuget-package.png" alt-text="Screenshot: select-nuget-package.png":::

 1. Wählen Sie die Registerkarte „Durchsuchen“ aus, und geben Sie „Azure.AI.FormRecognizer“ ein.

     :::image type="content" source="../media/quickstarts/azure-nuget-package.png" alt-text="Screenshot: select-form-recognizer-package.png":::

 1. Aktivieren Sie das Kontrollkästchen **Vorabversion einbeziehen**.

 1. Wählen Sie im Dropdownmenü die Version **4.0.0-beta.1** und anschließend die Option **Installieren** aus.

     :::image type="content" source="../media/quickstarts/prerelease-nuget-package.png" alt-text="{alt-text}":::

<!-- --- -->
## <a name="build-your-application"></a>Erstellen Ihrer Anwendung

Für die Interaktion mit dem Dienst „Formularerkennung“ müssen Sie eine Instanz der `DocumentAnalysisClient`-Klasse erstellen. Erstellen Sie hierfür ein `AzureKeyCredential`-Element mit Ihrem „apiKey“ und eine `DocumentAnalysisClient`-Instanz mit dem `AzureKeyCredential`-Element und Ihrem `endpoint` für die Formularerkennung.

1. Öffnen Sie die Datei **Program.cs**.

1. Fügen Sie die folgenden using-Direktiven ein:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure;
    using Azure.AI.FormRecognizer;
    using Azure.AI.FormRecognizer.DocumentAnalysis;
    ```

1. Legen Sie Ihre Umgebungsvariablen `endpoint` und `apiKey` fest, und erstellen Sie Ihre Instanzen von `AzureKeyCredential` und `DocumentAnalysisClient`:

    ```csharp
    string endpoint = "<your-endpoint>";
    string apiKey = "<your-apiKey>";
    AzureKeyCredential credential = new AzureKeyCredential(apiKey);
    DocumentAnalysisClient client = new DocumentAnalysisClient(new Uri(endpoint), credential);
    ```

1. Löschen Sie die Zeile `Console.Writeline("Hello World!");`, und fügen Sie die Codebeispiele für **Try It** (Ausprobieren) der **Main**-Methode in der Datei **Program.cs** hinzu:

    :::image type="content" source="../media/quickstarts/add-code-here.png" alt-text="Screenshot: Hinzufügen des Beispielcodes zur „Main“-Methode":::

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
> * Für die Analyse einer bestimmten Datei unter einem URI verwenden Sie die `StartAnalyzeDocumentFromUri`-Methode. Der zurückgegebene Wert ist ein `AnalyzeResult`-Objekt mit Daten zum übermittelten Dokument.
> * Sie haben den Datei-URI-Wert oben in der Main-Methode der Variable `string fileUri` hinzugefügt.
> * Der Einfachheit halber werden hier nicht alle Entitätsfelder angezeigt, die der Dienst zurückgibt. Eine Liste aller unterstützten Felder mit den entsprechenden Typen finden Sie auf der Konzeptseite [Allgemeines Dokument](../concept-general-document.md#named-entity-recognition-ner-categories).

### <a name="add-the-following-code-to-your-general-document-application-main-method"></a>Fügen Sie der **Main**-Methode Ihrer allgemeinen Dokumentanwendung den folgenden Code hinzu:

```csharp
// sample form document
string fileUri = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf";

AnalyzeDocumentOperation operation = await client.StartAnalyzeDocumentFromUriAsync("prebuilt-document", fileUri);

await operation.WaitForCompletionAsync();

AnalyzeResult result = operation.Value;

Console.WriteLine("Detected entities:");

foreach (DocumentEntity entity in result.Entities)
{
    if (entity.SubCategory == null)
    {
        Console.WriteLine($"  Found entity '{entity.Content}' with category '{entity.Category}'.");
    }
    else
    {
        Console.WriteLine($"  Found entity '{entity.Content}' with category '{entity.Category}' and sub-category '{entity.SubCategory}'.");
    }
}

Console.WriteLine("Detected key-value pairs:");

foreach (DocumentKeyValuePair kvp in result.KeyValuePairs)
{
    if (kvp.Value.Content == null)
    {
        Console.WriteLine($"  Found key with no value: '{kvp.Key.Content}'");
    }
    else
    {
        Console.WriteLine($"  Found key-value pair: '{kvp.Key.Content}' and '{kvp.Value.Content}'");
    }
}

foreach (DocumentPage page in result.Pages)
{
    Console.WriteLine($"Document Page {page.PageNumber} has {page.Lines.Count} line(s), {page.Words.Count} word(s),");
    Console.WriteLine($"and {page.SelectionMarks.Count} selection mark(s).");

    for (int i = 0; i < page.Lines.Count; i++)
    {
        DocumentLine line = page.Lines[i];
        Console.WriteLine($"  Line {i} has content: '{line.Content}'.");

        Console.WriteLine($"    Its bounding box is:");
        Console.WriteLine($"      Upper left => X: {line.BoundingBox[0].X}, Y= {line.BoundingBox[0].Y}");
        Console.WriteLine($"      Upper right => X: {line.BoundingBox[1].X}, Y= {line.BoundingBox[1].Y}");
        Console.WriteLine($"      Lower right => X: {line.BoundingBox[2].X}, Y= {line.BoundingBox[2].Y}");
        Console.WriteLine($"      Lower left => X: {line.BoundingBox[3].X}, Y= {line.BoundingBox[3].Y}");
    }

    for (int i = 0; i < page.SelectionMarks.Count; i++)
    {
        DocumentSelectionMark selectionMark = page.SelectionMarks[i];

        Console.WriteLine($"  Selection Mark {i} is {selectionMark.State}.");
        Console.WriteLine($"    Its bounding box is:");
        Console.WriteLine($"      Upper left => X: {selectionMark.BoundingBox[0].X}, Y= {selectionMark.BoundingBox[0].Y}");
        Console.WriteLine($"      Upper right => X: {selectionMark.BoundingBox[1].X}, Y= {selectionMark.BoundingBox[1].Y}");
        Console.WriteLine($"      Lower right => X: {selectionMark.BoundingBox[2].X}, Y= {selectionMark.BoundingBox[2].Y}");
        Console.WriteLine($"      Lower left => X: {selectionMark.BoundingBox[3].X}, Y= {selectionMark.BoundingBox[3].Y}");
    }
}

foreach (DocumentStyle style in result.Styles)
{
    // Check the style and style confidence to see if text is handwritten.
    // Note that value '0.8' is used as an example.

    bool isHandwritten = style.IsHandwritten.HasValue && style.IsHandwritten == true;

    if (isHandwritten && style.Confidence > 0.8)
    {
        Console.WriteLine($"Handwritten content found:");

        foreach (DocumentSpan span in style.Spans)
        {
            Console.WriteLine($"  Content: {result.Content.Substring(span.Offset, span.Length)}");
        }
    }
}

Console.WriteLine("The following tables were extracted:");

for (int i = 0; i < result.Tables.Count; i++)
{
    DocumentTable table = result.Tables[i];
    Console.WriteLine($"  Table {i} has {table.RowCount} rows and {table.ColumnCount} columns.");

    foreach (DocumentTableCell cell in table.Cells)
    {
        Console.WriteLine($"    Cell ({cell.RowIndex}, {cell.ColumnIndex}) has kind '{cell.Kind}' and content: '{cell.Content}'.");
    }
}

```

## <a name="try-it-layout-model"></a>**Try it** (Ausprobieren): Layoutmodell

Dient zum Extrahieren von Text, Auswahlmarkierungen, Textformaten und Tabellenstrukturen sowie der zugehörigen Begrenzungsbereichskoordinaten aus Dokumenten.

> [!div class="checklist"]
>
> * Für dieses Beispiel benötigen Sie eine **Formulardokumentdatei unter einem URI**. Sie können für diese Schnellstartanleitung unser [Beispielformulardokument](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) verwenden.
> * Sie haben den Datei-URI-Wert oben in der Main-Methode der Variable `string fileUri` hinzugefügt.
> * Verwenden Sie zum Extrahieren des Layouts aus einer bestimmten Datei unter einem URI die `StartAnalyzeDocumentFromUri`-Methode, und übergeben Sie `prebuilt-layout` als Modell-ID. Der zurückgegebene Wert ist ein `AnalyzeResult`-Objekt mit Daten aus dem übermittelten Dokument.

### <a name="add-the-following-code-to-your-layout-application-main-method"></a>Fügen Sie der **Main**-Methode Ihrer Layoutanwendung den folgenden Code hinzu:

```csharp
// sample form document
string fileUri = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf";

AnalyzeDocumentOperation operation = await client.StartAnalyzeDocumentFromUriAsync("prebuilt-layout", fileUri);

await operation.WaitForCompletionAsync();

AnalyzeResult result = operation.Value;

foreach (DocumentPage page in result.Pages)
{
    Console.WriteLine($"Document Page {page.PageNumber} has {page.Lines.Count} line(s), {page.Words.Count} word(s),");
    Console.WriteLine($"and {page.SelectionMarks.Count} selection mark(s).");

    for (int i = 0; i < page.Lines.Count; i++)
    {
        DocumentLine line = page.Lines[i];
        Console.WriteLine($"  Line {i} has content: '{line.Content}'.");

        Console.WriteLine($"    Its bounding box is:");
        Console.WriteLine($"      Upper left => X: {line.BoundingBox[0].X}, Y= {line.BoundingBox[0].Y}");
        Console.WriteLine($"      Upper right => X: {line.BoundingBox[1].X}, Y= {line.BoundingBox[1].Y}");
        Console.WriteLine($"      Lower right => X: {line.BoundingBox[2].X}, Y= {line.BoundingBox[2].Y}");
        Console.WriteLine($"      Lower left => X: {line.BoundingBox[3].X}, Y= {line.BoundingBox[3].Y}");
    }

    for (int i = 0; i < page.SelectionMarks.Count; i++)
    {
        DocumentSelectionMark selectionMark = page.SelectionMarks[i];

        Console.WriteLine($"  Selection Mark {i} is {selectionMark.State}.");
        Console.WriteLine($"    Its bounding box is:");
        Console.WriteLine($"      Upper left => X: {selectionMark.BoundingBox[0].X}, Y= {selectionMark.BoundingBox[0].Y}");
        Console.WriteLine($"      Upper right => X: {selectionMark.BoundingBox[1].X}, Y= {selectionMark.BoundingBox[1].Y}");
        Console.WriteLine($"      Lower right => X: {selectionMark.BoundingBox[2].X}, Y= {selectionMark.BoundingBox[2].Y}");
        Console.WriteLine($"      Lower left => X: {selectionMark.BoundingBox[3].X}, Y= {selectionMark.BoundingBox[3].Y}");
    }
}

foreach (DocumentStyle style in result.Styles)
{
    // Check the style and style confidence to see if text is handwritten.
    // Note that value '0.8' is used as an example.

    bool isHandwritten = style.IsHandwritten.HasValue && style.IsHandwritten == true;

    if (isHandwritten && style.Confidence > 0.8)
    {
        Console.WriteLine($"Handwritten content found:");

        foreach (DocumentSpan span in style.Spans)
        {
            Console.WriteLine($"  Content: {result.Content.Substring(span.Offset, span.Length)}");
        }
    }
}

Console.WriteLine("The following tables were extracted:");

for (int i = 0; i < result.Tables.Count; i++)
{
    DocumentTable table = result.Tables[i];
    Console.WriteLine($"  Table {i} has {table.RowCount} rows and {table.ColumnCount} columns.");

    foreach (DocumentTableCell cell in table.Cells)
    {
        Console.WriteLine($"    Cell ({cell.RowIndex}, {cell.ColumnIndex}) has kind '{cell.Kind}' and content: '{cell.Content}'.");
    }
}

```

## <a name="try-it-prebuilt-model"></a>**Probieren Sie es aus:** Vordefiniertes Modell

In diesem Beispiel wird veranschaulicht, wie Sie Daten aus bestimmten Arten von häufig verwendeten Dokumenttypen mit vortrainierten Modellen analysieren, wobei eine Rechnung als Beispiel verwendet wird.

> [!div class="checklist"]
>
> * In diesem Beispiel analysieren Sie ein Rechnungsdokument mithilfe eines vordefinierten Modells. Für diese Schnellstartanleitung können Sie unser [Beispiel für ein Rechnungsdokument](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf) verwenden.
> * Sie haben den Datei-URI-Wert oben in der Main-Methode der Variable `string fileUri` hinzugefügt.
> * Verwenden Sie zum Analysieren einer bestimmten Datei unter einem URI die `StartAnalyzeDocumentFromUri`-Methode, und übergeben Sie `prebuilt-invoice` als Modell-ID. Der zurückgegebene Wert ist ein `AnalyzeResult`-Objekt mit Daten aus dem übermittelten Dokument.
> * Der Einfachheit halber werden hier nicht alle Schlüssel-Wert-Paare angezeigt, die der Dienst zurückgibt. Eine Liste aller unterstützten Felder mit den entsprechenden Typen finden Sie auf der Konzeptseite [Rechnung](../concept-invoice.md#field-extraction).

### <a name="choose-the-invoice-prebuilt-model-id"></a>Auswählen der ID für das vordefinierte Rechnungsmodell

Sie sind hierbei nicht auf Rechnungen beschränkt, sondern können zwischen mehreren vordefinierten Modellen wählen, von denen jedes über eine Gruppe unterstützter Felder verfügt. Welches Modell für den Analysevorgang verwendet wird, hängt vom Typ des zu analysierenden Dokuments ab. Hier sind die Modell-IDs für die vordefinierten Modelle angegeben, die vom Dienst „Formularerkennung“ derzeit unterstützt werden:

* [**prebuilt-invoice:**](../concept-invoice.md) Extrahieren von Text, Auswahlmarkierungen, Tabellen, Schlüssel-Wert-Paaren und wichtigen Informationen aus Rechnungen
* [**prebuilt-receipt:**](../concept-receipt.md) Extrahieren von Text und wichtigen Informationen aus Belegen
* [**prebuilt-idDocument:**](../concept-id-document.md) Extrahieren von Text und wichtigen Informationen aus Führerscheinen und Reisepässen
* [**prebuilt-businessCard:**](../concept-business-card.md) Extrahieren von Text und wichtigen Informationen aus Visitenkarten

### <a name="add-the-following-code-to-your-prebuilt-invoice-application-main-method"></a>Fügen Sie der **Main**-Methode Ihrer vordefinierten Rechnungsanwendung den folgenden Code hinzu:

```csharp
// sample invoice document
string filePath = "(https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf";

using var stream = new FileStream(filePath, FileMode.Open);

AnalyzeDocumentOperation operation = await client.StartAnalyzeDocumentAsync("prebuilt-invoice", stream);

await operation.WaitForCompletionAsync();

AnalyzeResult result = operation.Value;

for (int i = 0; i < result.Documents.Count; i++)
{
    Console.WriteLine($"Document {i}:");

    AnalyzedDocument document = result.Documents[i];

    if (document.Fields.TryGetValue("VendorName", out DocumentField vendorNameField))
    {
        if (vendorNameField.ValueType == DocumentFieldType.String)
        {
            string vendorName = vendorNameField.AsString();
            Console.WriteLine($"Vendor Name: '{vendorName}', with confidence {vendorNameField.Confidence}");
        }
    }

    if (document.Fields.TryGetValue("CustomerName", out DocumentField customerNameField))
    {
        if (customerNameField.ValueType == DocumentFieldType.String)
        {
            string customerName = customerNameField.AsString();
            Console.WriteLine($"Customer Name: '{customerName}', with confidence {customerNameField.Confidence}");
        }
    }

    if (document.Fields.TryGetValue("Items", out DocumentField itemsField))
    {
        if (itemsField.ValueType == DocumentFieldType.List)
        {
            foreach (DocumentField itemField in itemsField.AsList())
            {
                Console.WriteLine("Item:");

                if (itemField.ValueType == DocumentFieldType.Dictionary)
                {
                    IReadOnlyDictionary<string, DocumentField> itemFields = itemField.AsDictionary();

                    if (itemFields.TryGetValue("Description", out DocumentField itemDescriptionField))
                    {
                        if (itemDescriptionField.ValueType == DocumentFieldType.String)
                        {
                            string itemDescription = itemDescriptionField.AsString();

                            Console.WriteLine($"  Description: '{itemDescription}', with confidence {itemDescriptionField.Confidence}");
                        }
                    }

                    if (itemFields.TryGetValue("Amount", out DocumentField itemAmountField))
                    {
                        if (itemAmountField.ValueType == DocumentFieldType.Double)
                        {
                            double itemAmount = itemAmountField.AsDouble();

                            Console.WriteLine($"  Amount: '{itemAmount}', with confidence {itemAmountField.Confidence}");
                        }
                    }
                }
            }
        }
    }

    if (document.Fields.TryGetValue("SubTotal", out DocumentField subTotalField))
    {
        if (subTotalField.ValueType == DocumentFieldType.Double)
        {
            double subTotal = subTotalField.AsDouble();
            Console.WriteLine($"Sub Total: '{subTotal}', with confidence {subTotalField.Confidence}");
        }
    }

    if (document.Fields.TryGetValue("TotalTax", out DocumentField totalTaxField))
    {
        if (totalTaxField.ValueType == DocumentFieldType.Double)
        {
            double totalTax = totalTaxField.AsDouble();
            Console.WriteLine($"Total Tax: '{totalTax}', with confidence {totalTaxField.Confidence}");
        }
    }

    if (document.Fields.TryGetValue("InvoiceTotal", out DocumentField invoiceTotalField))
    {
        if (invoiceTotalField.ValueType == DocumentFieldType.Double)
        {
            double invoiceTotal = invoiceTotalField.AsDouble();
            Console.WriteLine($"Invoice Total: '{invoiceTotal}', with confidence {invoiceTotalField.Confidence}");
        }
    }
}

```

## <a name="run-your-application"></a>Ausführen der Anwendung

<!-- ### [.NET Command-line interface (CLI)](#tab/cli)

Open your command prompt and go to the directory that contains your project and type the following:

```console
dotnet run formrecognizer-quickstart.dll
```

### [Visual Studio](#tab/vs) -->

Wählen Sie die grüne Schaltfläche **Starten** neben „formRecognizer_quickstart“ aus, um Ihr Programm zu kompilieren und auszuführen, oder drücken Sie **F5**.

  :::image type="content" source="../media/quickstarts/run-visual-studio.png" alt-text="Screenshot: Ausführen Ihres Visual Studio-Programms":::

<!-- --- -->

Herzlichen Glückwunsch! In dieser Schnellstartanleitung haben Sie das C# SDK für die Formularerkennung verwendet, um verschiedene Formulare und Dokumente auf unterschiedliche Arten zu analysieren. Lesen Sie als Nächstes die Referenzdokumentation, um die Formularerkennungs-API eingehender kennenzulernen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Referenzdokumentation zur REST-API v3.0](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)

> [!div class="nextstepaction"]
> [C#/.NET-Referenzbibliothek für die Formularerkennung](/dotnet/api/overview/azure/ai.formrecognizer-readme?view=azure-dotnet&preserve-view=true)
