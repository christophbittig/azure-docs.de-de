---
title: 'Schnellstart: C# SDK für Formularerkennung v3.0 | Vorschau'
titleSuffix: Azure Applied AI Services
description: Formular- und Dokumentverarbeitung, Datenextraktion und Analyse mit C#-Clientbibliothek-SDKs für Formularerkennung v3.0 (Vorschau)
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 50c1cfbcfc79212f03fd67f783afaff110ce1e09
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130216730"
---
# <a name="quickstart-form-recognizer-c-client-library-sdks-v30--preview"></a>Schnellstart: C#-Clientbibliothek-SDKs für Formularerkennung v3.0 | Vorschau

Informieren Sie sich über die ersten Schritte mit der Azure-Formularerkennung mit der Programmiersprache C#. Azure-Formularerkennung ist ein Clouddienst unter [Azure Applied AI Services](../../../applied-ai-services/index.yml), mit dem Sie über die Technologie für maschinelles Lernen Software für die automatisierte Datenverarbeitung entwickeln können. Sie können die Formularerkennung per REST-API oder SDK verwenden. Sie sollten den kostenlosen Dienst nutzen, wenn Sie die Technologie erlernen. Bedenken Sie, dass die Anzahl der kostenlosen Seiten auf 500 pro Monat beschränkt ist.

>[!NOTE]
> Formularerkennung v3.0 befindet sich derzeit in der öffentlichen Vorschauphase. Dies bedeutet, dass einige Features unter Umständen nicht unterstützt werden oder nur eingeschränkt verwendbar sind. 

[Referenzdokumentation](/dotnet/api/overview/azure/ai.formrecognizer-readme?view=azure-dotnet&preserve-view=true ) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src) | [Paket (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer) | [Beispiele](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

Die Formularerkennung unter Azure Cognitive Services ist ein Clouddienst, bei dem maschinelles Lernen genutzt wird, um Formularfelder, Text und Tabellen aus Ihren Dokumenten zu extrahieren und zu analysieren. Sie können Formularerkennungsmodelle leicht aufrufen, indem Sie unsere Clientbibliothek-SDKs in Ihre Workflows und Anwendungen integrieren.

### <a name="form-recognizer-models"></a>Formularerkennungsmodelle

Das C# SDK unterstützt die folgenden Modelle und Funktionen:

* 🆕Allgemeines Dokument: Analysieren und Extrahieren von Text, Tabellen, Struktur, Schlüssel-Wert-Paaren und benannten Entitäten.|
* Layout: Analysieren und Extrahieren von Tabellen, Zeilen, Wörtern und Auswahlmarkierungen, z. B. Optionsfelder und Kontrollkästchen in Formulardokumenten, ohne ein Modell trainieren zu müssen.
* Benutzerdefiniert: Analysieren und Extrahieren von Formularfeldern und anderen Inhalten aus Ihren benutzerdefinierten Formularen mit Modellen, die Sie mit Ihren eigenen Formulartypen trainiert haben.
* Rechnungen: Analysieren und Extrahieren häufig verwendeter Felder aus Rechnungen über ein vortrainiertes Rechnungsmodell.
* Belege: Analysieren und Extrahieren häufig verwendeter Felder aus Belegen über ein vortrainiertes Belegmodell.
* Ausweisdokumente: Analysieren und Extrahieren häufig verwendeter Felder aus Ausweisdokumenten, z. B. Pässen oder Führerscheinen, mit einem vortrainierten Modell für Ausweisdokumente.
* Visitenkarten: Analysieren und Extrahieren häufig verwendeter Felder aus Visitenkarten mit einem vortrainierten Modell für Visitenkarten.

In dieser Schnellstartanleitung verwenden Sie die folgenden Funktionen, um Daten und Werte aus Formularen und Dokumenten zu analysieren und zu extrahieren:

* [**Allgemeines Dokument**](#try-it-general-document-model)

* [**Layout**](#try-it-layout-model)

* [**Vordefinierte Rechnung**](#try-it-prebuilt-invoice-model)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services/)

* Aktuelle Version der [Visual Studio-IDE](https://visualstudio.microsoft.com/vs/) oder [.NET Core](https://dotnet.microsoft.com/download).

* Eine Cognitive Services- oder Formularerkennungsressource. Wenn Sie über Ihr Azure-Abonnement verfügen, können Sie im Azure-Portal eine Formularerkennungsressource mit [einem Dienst](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) oder [mehreren Diensten](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) erstellen, um Ihren Schlüssel und Endpunkt zu erhalten. Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

> [!TIP] 
> Erstellen Sie eine Cognitive Services-Ressource, wenn Sie planen, über einen einzelnen Endpunkt bzw. Schlüssel auf mehrere Cognitive Services-Instanzen zuzugreifen. Erstellen Sie eine Formularerkennungsressource, falls nur auf die Formularerkennung zugegriffen werden soll. Beachten Sie hierbei, dass Sie eine Ressource mit einem einzelnen Dienst benötigen, falls Sie die [Azure Active Directory-Authentifizierung](../../../active-directory/authentication/overview-authentication.md) nutzen möchten.

* Klicken Sie nach der Bereitstellung Ihrer Ressource auf **Zu Ressource wechseln**. Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Formularerkennungs-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt:

  :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="Screenshot: Schlüssel und Endpunkt im Azure-Portal":::

## <a name="set-up"></a>Einrichten

### <a name="option-1-net-command-line-interface-cli"></a>[Option 1: .NET-Befehlszeilenschnittstelle (Command-Line Interface, CLI)](#tab/cli)

Verwenden Sie in einem Konsolenfenster (z. B. cmd, PowerShell oder Bash) den Befehl `dotnet new` zum Erstellen einer neuen Konsolen-App mit dem Namen `formrecognizer-quickstart`. Dieser Befehl erstellt ein einfaches „Hallo Welt“-C#-Projekt mit einer einzigen Quelldatei: *Program.cs*.

```console
dotnet new console -n formrecognizer-quickstart
```

Öffnen Sie eine Befehlszeile, und wechseln Sie zu dem Verzeichnis, das Ihre Projektdatei enthält. Erstellen Sie die Anwendung mit:

```console
dotnet build
```

Die Buildausgabe sollte keine Warnungen oder Fehler enthalten.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library-with-nuget"></a>Installieren der Clientbibliothek mit NuGet

Installieren Sie in dem Verzeichnis, das Ihr Projekt enthält, mit dem folgenden Befehl die Clientbibliothek der Formularerkennung für .NET:

```console
dotnet add package Azure.AI.FormRecognizer
```

Für diese Version der Clientbibliothek wird standardmäßig die Version „2021-09-30-preview“ des Diensts verwendet.

### <a name="option-2-visual-studio"></a>[Option 2: Visual Studio](#tab/vs)

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

 1. Wählen Sie im Dropdownmenü die gewünschte Version und anschließend die Option **Installieren** aus.

     Für diese Version der Clientbibliothek wird standardmäßig die Version „2021-09-30-preview“ des Diensts verwendet.

---

## <a name="build-your-application"></a>Erstellen Ihrer Anwendung

Für die Interaktion mit dem Dienst „Formularerkennung“ müssen Sie eine Instanz der `DocumentAnalysisClient`-Klasse erstellen. Erstellen Sie hierfür ein `AzureKeyCredential`-Element mit Ihrem „apiKey“ und eine `DocumentAnalysisClient`-Instanz mit dem `AzureKeyCredential`-Element und Ihrem `endpoint` für die Formularerkennung.

1. Öffnen Sie die Datei **Program.cs**.

1. Fügen Sie die folgenden using-Direktiven ein:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.AI.FormRecognizer.DocumentAnalysis;
    ```

1. Legen Sie Ihre Umgebungsvariablen `endpoint` und `apiKey` fest, und erstellen Sie Ihre `AzureKeyCredential`- und `DocumentAnalysisClient`-Instanz:

    ```csharp
    string endpoint = "<your-endpoint>";
    string apiKey = "<your-apiKey>";
    var credential = new AzureKeyCredential(apiKey);
    var client = new DocumentAnalysisClient(new Uri(endpoint), credential);
    ```

1. Löschen Sie die Zeile `Console.Writeline("Hello World!");`, und fügen Sie den Code für **Try It** (Ausprobieren) der **Main**-Methode in der Datei **Program.cs** hinzu:

    :::image type="content" source="../media/quickstarts/add-code-here.png" alt-text="Screenshot: Hinzufügen des Beispielcodes zur „Main“-Methode":::

## <a name="try-it-general-document-model"></a>**Try it** (Ausprobieren): Allgemeines Dokumentmodell

> [!div class="checklist"]
>
> * Für dieses Beispiel benötigen Sie eine **Formulardokumentdatei unter einem URI**. Sie können für diese Schnellstartanleitung unser [Beispielformulardokument](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) verwenden.
> * Fügen Sie den Datei-URI-Wert oben in der „Main“-Methode der Variablen `string fileUri` hinzu.
> * Für die Analyse einer bestimmten Datei unter einem URI verwenden Sie die `StartAnalyzeDocumentFromUri`-Methode. Der zurückgegebene Wert ist ein `AnalyzeResult`-Objekt mit Daten zum übermittelten Dokument.

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
> * Fügen Sie den Datei-URI-Wert oben in der „Main“-Methode der Variablen `string fileUri` hinzu.
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

## <a name="try-it-prebuilt-invoice-model"></a>**Try it** (Ausprobieren): Vordefiniertes Rechnungsmodell

In diesem Beispiel wird veranschaulicht, wie Sie Daten aus bestimmten Arten von häufig verwendeten Dokumenten mit vortrainierten Modellen analysieren, indem eine Rechnung als Beispiel verwendet wird.

> [!div class="checklist"]
>
> * Für dieses Beispiel benötigen Sie eine **Rechnungsdokumentdatei unter einem URI**. Für diese Schnellstartanleitung können Sie unser [Beispiel für ein Rechnungsdokument](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf) verwenden.
> * Fügen Sie den Datei-URI-Wert oben in der „Main“-Methode der Variablen `string fileUri` hinzu.
> * Verwenden Sie zum Analysieren einer bestimmten Datei unter einem URI die `StartAnalyzeDocumentFromUri`-Methode, und übergeben Sie `prebuilt-invoice` als Modell-ID. Der zurückgegebene Wert ist ein `AnalyzeResult`-Objekt mit Daten aus dem übermittelten Dokument.

### <a name="choose-the-invoice-prebuilt-model-id"></a>Auswählen der ID für das vordefinierte Rechnungsmodell

Sie sind hierbei nicht auf Rechnungen beschränkt, sondern können zwischen mehreren vordefinierten Modellen wählen, von denen jedes über eine Gruppe unterstützter Felder verfügt. Welches Modell für den Analysevorgang verwendet wird, hängt vom Typ des zu analysierenden Dokuments ab. Hier sind die Modell-IDs für die vordefinierten Modelle angegeben, die vom Dienst „Formularerkennung“ derzeit unterstützt werden:

* **prebuilt-invoice**: Dient zum Extrahieren von Text, Auswahlmarkierungen, Tabellen, Schlüssel-Wert-Paaren und wichtigen Informationen aus Rechnungen.
* **prebuilt-businessCard**: Dient zum Extrahieren von Text und wichtigen Informationen aus Visitenkarten.
* **prebuilt-idDocument**: Dient zum Extrahieren von Text und wichtigen Informationen aus Führerscheinen und Reisepässen.
* **prebuilt-receipt**: Dient zum Extrahieren von Text und wichtigen Informationen aus Belegen.

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

### <a name="net-command-line-interface-cli"></a>[.NET-Befehlszeilenschnittstelle (Command-Line Interface, CLI)](#tab/cli)

Öffnen Sie die Eingabeaufforderung, navigieren Sie zu dem Verzeichnis, in dem Ihr Projekt enthalten ist, und geben Sie Folgendes ein:

```console
dotnet run formrecognizer-quickstart.dll
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

Wählen Sie die grüne Schaltfläche **Starten** neben „formRecognizer_quickstart“ aus, um Ihr Programm zu kompilieren und auszuführen, oder drücken Sie **F5**.

  :::image type="content" source="../media/quickstarts/run-visual-studio.png" alt-text="Screenshot: Ausführen Ihres Visual Studio-Programms":::

---

Herzlichen Glückwunsch! In dieser Schnellstartanleitung haben Sie das C# SDK für die Formularerkennung verwendet, um verschiedene Formulare und Dokumente auf unterschiedliche Arten zu analysieren. Lesen Sie als Nächstes die Referenzdokumentation, um die Formularerkennungs-API eingehender kennenzulernen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Referenzdokumentation zur REST-API v3.0](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)