---
title: 'Schnellstart: JavaScript SDK für Formularerkennung v3.0 | Vorschau'
titleSuffix: Azure Applied AI Services
description: Formular- und Dokumentverarbeitung, Datenextraktion und Analyse mit JavaScript-Clientbibliothek-SDKs für Formularerkennung v3.0 (Vorschau)
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 364eea405653ac54032787a14a612fbc57de03db
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030199"
---
# <a name="quickstart-form-recognizer-javascript-client-library-sdks-v30--preview"></a>Schnellstart: JavaScript-Clientbibliothek-SDKs für Formularerkennung v3.0 | Vorschau

>[!NOTE]
> Formularerkennung v3.0 befindet sich derzeit in der öffentlichen Vorschauphase. Dies bedeutet, dass einige Features unter Umständen nicht unterstützt werden oder nur eingeschränkt verwendbar sind.

[Referenzdokumentation](https://azuresdkdocs.blob.core.windows.net/$web/javascript/azure-ai-form-recognizer/4.0.0-beta.1/index.html) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/formrecognizer/ai-form-recognizer/src) | [Paket (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer) | [Beispiele](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_4.0.0-beta.1/sdk/formrecognizer/ai-form-recognizer/README.md)

Informieren Sie sich über die ersten Schritte mit der Azure-Formularerkennung und der Programmiersprache JavaScript. Die Azure-Formularerkennung ist ein cloudbasierter Dienst von Azure Applied AI Services, bei dem maschinelles Lernen genutzt wird, um Formularfelder, Text und Tabellen aus Ihren Dokumenten zu extrahieren und zu analysieren. Sie können Formularerkennungsmodelle leicht aufrufen, indem Sie unsere Clientbibliothek-SDKs in Ihre Workflows und Anwendungen integrieren. Sie sollten den kostenlosen Dienst nutzen, wenn Sie die Technologie erlernen. Bedenken Sie, dass die Anzahl der kostenlosen Seiten auf 500 pro Monat beschränkt ist.

Weitere Informationen zu Funktionen der Formularerkennung und zu Entwicklungsoptionen finden Sie auf unserer [Übersichtsseite](../overview.md#form-recognizer-features-and-development-options).

In dieser Schnellstartanleitung verwenden Sie die folgenden Funktionen, um Daten und Werte aus Formularen und Dokumenten zu analysieren und zu extrahieren:

* [🆕 **Allgemeines Dokument:**](#try-it-general-document-model) Analysieren und Extrahieren von Text, Tabellen, Strukturen, Schlüssel-Wert-Paaren und benannten Entitäten

* [**Layout:**](#try-it-layout-model) Analysieren und Extrahieren von Tabellen, Zeilen, Wörtern und Auswahlmarkierungen, z. B. Optionsfeldern und Kontrollkästchen in Formulardokumenten, ohne ein Modell trainieren zu müssen

* [**Vordefinierte Rechnungen:**](#try-it-prebuilt-model) Analysieren und Extrahieren häufig verwendeter Felder aus Rechnungen über ein vortrainiertes Rechnungsmodell

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services/)

* Die aktuelle Version von [Visual Studio Code](https://code.visualstudio.com/) oder Ihrer bevorzugten IDE 

* Die aktuelle LTS-Version von [Node.js](https://nodejs.org/about/releases/)

* Eine Cognitive Services- oder Formularerkennungsressource. Wenn Sie über Ihr Azure-Abonnement verfügen, können Sie im Azure-Portal eine Formularerkennungsressource mit [einem Dienst](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) oder [mehreren Diensten](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) erstellen, um Ihren Schlüssel und Endpunkt zu erhalten. Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

    > [!TIP]
    > Erstellen Sie eine Cognitive Services-Ressource, wenn Sie planen, über einen einzelnen Endpunkt bzw. Schlüssel auf mehrere Cognitive Services-Instanzen zuzugreifen. Erstellen Sie eine Formularerkennungsressource, falls nur auf die Formularerkennung zugegriffen werden soll. Beachten Sie hierbei, dass Sie eine Ressource mit einem einzelnen Dienst benötigen, falls Sie die [Azure Active Directory-Authentifizierung](/azure/active-directory/authentication/overview-authentication) nutzen möchten.

* Wählen Sie nach der Bereitstellung der Ressource **Zu Ressource wechseln** aus. Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Formularerkennungs-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt:

  :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="Screenshot: Schlüssel und Endpunktspeicherort im Azure-Portal.":::

## <a name="set-up"></a>Einrichten

1. Erstellen Sie eine neue Node.js-Anwendung. Erstellen Sie in einem Konsolenfenster (etwa cmd, PowerShell oder Bash) ein neues Verzeichnis für Ihre App, und rufen Sie es auf.

    ```console
    mkdir form-recognizer-app && cd form-recognizer-app
    ```

1. Führen Sie den Befehl `npm init` aus, um eine Knotenanwendung mit der Datei `package.json` zu erstellen.

    ```console
    npm init
    ```

1. Installieren Sie das npm-Paket der Clientbibliothek `ai-form-recognizer`:

    ```console
    npm install @azure/ai-form-recognizer@4.0.0-beta.1 @azure/identity
    ```

    * Die Datei `package.json` Ihrer App wird mit den Abhängigkeiten aktualisiert.

1. Erstellen Sie eine Datei mit dem Namen `index.js`, öffnen Sie sie, und fügen Sie die folgenden Bibliotheken hinzu:

    ```javascript
   const { AzureKeyCredential, DocumentAnalysisClient } = require("@azure/ai-form-recognizer");
    ```

1. Erstellen Sie Variablen für den Azure-Endpunkt und -Schlüssel Ihrer Ressource:

    ```javascript
    const apiKey = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
    const endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";
    ```

An diesem Punkt sollte Ihre JavaScript-Anwendung die folgenden Codezeilen enthalten:

```javascript
const { AzureKeyCredential, DocumentAnalysisClient } = require("@azure/ai-form-recognizer");

const endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";
const apiKey = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
```

### <a name="select-a-code-sample-to-copy-and-paste-into-your-application"></a>Wählen Sie ein Codebeispiel zum Kopieren und Einfügen in Ihre Anwendung aus:

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
> * Für die Analyse einer bestimmten Datei unter einem URI verwenden Sie die `beginExtractGenericDocument`-Methode.
> * Sie haben den Datei-URI-Wert der Variable `formUrl` im oberen Bereich der Datei hinzugefügt.
> * Eine Liste aller unterstützten Felder mit den entsprechenden Typen finden Sie auf der Konzeptseite [Allgemeines Dokument](../concept-general-document.md#named-entity-recognition-ner-categories).

### <a name="add-the-following-code-to-your-general-document-application-on-the-line-below-the-apikey-variable"></a>Fügen Sie Ihrer allgemeinen Dokumentanwendung in der Zeile unterhalb der Variablen `apiKey` den folgenden Code hinzu:

```javascript

const formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf"

async function main() {
    const client = new DocumentAnalysisClient(endpoint, new DefaultAzureCredential(apiKey));

    const poller = await client.beginExtractGenericDocument(formUrl);

    const {
        keyValuePairs,
        entities
    } = await poller.pollUntilDone();

    if (keyValuePairs.length <= 0) {
        console.log("No key-value pairs were extracted from the document.");
    } else {
        console.log("Key-Value Pairs:");
        for (const {
                key,
                value,
                confidence
            } of keyValuePairs) {
            console.log("- Key  :", `"${key.content}"`);
            console.log("  Value:", `"${value?.content ?? "<undefined>"}" (${confidence})`);
        }
    }

    if (entities.length <= 0) {
        console.log("No entities were extracted from the document.");
    } else {
        console.log("Entities:");
        for (const entity of entities) {
            console.log(
                `- "${entity.content}" ${entity.category} - ${entity.subCategory ?? "<none>"} (${
          entity.confidence
        })`
            );
        }
    }
}

main().catch((error) => {
    console.error("An error occurred:", error);
    process.exit(1);
});
```

## <a name="try-it-layout-model"></a>**Try it** (Ausprobieren): Layoutmodell

> [!div class="checklist"]
>
> * Für dieses Beispiel benötigen Sie eine **Formulardokumentdatei unter einem URI**. Sie können für diese Schnellstartanleitung unser [Beispielformulardokument](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) verwenden.
> * Sie haben den Datei-URI-Wert der Variable `formUrl` im oberen Bereich der Datei hinzugefügt.
> * Für die Analyse einer bestimmten Datei unter einem URI verwenden Sie die `beginExtractLayout`-Methode.

### <a name="add-the-following-code-to-your-layout-application-on-the-line-below-the-apikey-variable"></a>Fügen Sie Ihrer Layoutanwendung in der Zeile unterhalb der Variablen `apiKey` den folgenden Code hinzu:

```javascript

const formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf"

async function main() {
    const client = new DocumentAnalysisClient(endpoint, new AzureKeyCredential(apiKey));

    const poller = await client.beginExtractLayout(formUrl);

    const {
        pages,
        tables
    } = await poller.pollUntilDone();

    if (pages.length <= 0) {
        console.log("No pages were extracted from the document.");
    } else {
        console.log("Pages:");
        for (const page of pages) {
            console.log("- Page", page.pageNumber, `(unit: ${page.unit})`);
            console.log(`  ${page.width}x${page.height}, angle: ${page.angle}`);
            console.log(`  ${page.lines.length} lines, ${page.words.length} words`);
        }
    }

    if (tables.length <= 0) {
        console.log("No tables were extracted from the document.");
    } else {
        console.log("Tables:");
        for (const table of tables) {
            console.log(
                `- Extracted table: ${table.columnCount} columns, ${table.rowCount} rows (${table.cells.length} cells)`
            );
        }
    }
}

main().catch((error) => {
    console.error("An error occurred:", error);
    process.exit(1);
});

```

## <a name="try-it-prebuilt-model"></a>**Probieren Sie es aus:** Vordefiniertes Modell

In diesem Beispiel wird veranschaulicht, wie Sie Daten aus bestimmten Arten von häufig verwendeten Dokumenttypen mit vortrainierten Modellen analysieren, wobei eine Rechnung als Beispiel verwendet wird.

> [!div class="checklist"]
>
> * In diesem Beispiel analysieren Sie ein Rechnungsdokument mithilfe eines vordefinierten Modells. Für diese Schnellstartanleitung können Sie unser [Beispiel für ein Rechnungsdokument](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf) verwenden.
> * Sie haben den Datei-URI-Wert der Variable `invoiceUrl` im oben in der Datei hinzugefügt.
> * Um eine bestimmte Datei an einem URI zu analysieren, verwenden Sie die `beginAnalyzeDocuments`-Methode und übergeben `PrebuiltModels.Invoice` als Modell-ID. Der Rückgabewert ist ein `result`-Objekt, das Daten zum übermittelten Dokument enthält.
> * Der Einfachheit halber werden hier nicht alle Schlüssel-Wert-Paare angezeigt, die der Dienst zurückgibt. Eine Liste aller unterstützten Felder mit den entsprechenden Typen finden Sie auf der Konzeptseite [Rechnung](../concept-invoice.md#field-extraction).

### <a name="choose-the-invoice-prebuilt-model-id"></a>Auswählen der ID für das vordefinierte Rechnungsmodell

Sie sind hierbei nicht auf Rechnungen beschränkt, sondern können zwischen mehreren vordefinierten Modellen wählen, von denen jedes über eine Gruppe unterstützter Felder verfügt. Welches Modell für den Analysevorgang verwendet wird, hängt vom Typ des zu analysierenden Dokuments ab. Hier sind die Modell-IDs für die vordefinierten Modelle angegeben, die vom Dienst „Formularerkennung“ derzeit unterstützt werden:

* [**prebuilt-invoice:**](../concept-invoice.md) Extrahieren von Text, Auswahlmarkierungen, Tabellen, Schlüssel-Wert-Paaren und wichtigen Informationen aus Rechnungen
* [**prebuilt-receipt:**](../concept-receipt.md) Extrahieren von Text und wichtigen Informationen aus Belegen
* [**prebuilt-idDocument:**](../concept-id-document.md) Extrahieren von Text und wichtigen Informationen aus Führerscheinen und Reisepässen
* [**prebuilt-businessCard:**](../concept-business-card.md) Extrahieren von Text und wichtigen Informationen aus Visitenkarten

### <a name="add-the-following-code-to-your-prebuilt-invoice-application-below-the-apikey-variable"></a>Fügen Sie Ihrer vordefinierten Rechnungsanwendung unterhalb der Variablen `apiKey` den folgenden Code hinzu:

```javascript

const {PreBuiltModels} = require("@azure/ai-form-recognizer");

// Use of PrebuiltModels.Receipt above (rather than the raw model ID), adds strong typing of the model's output

const invoiceUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf";

async function main() {

    const client = new DocumentAnalysisClient(endpoint, new AzureKeyCredential(apiKey));

    const poller = await client.beginAnalyzeDocuments(PrebuiltModels.Invoice, invoiceUrl);

    const {
        documents: [result]
    } = await poller.pollUntilDone();

    if (result) {
        const invoice = result.fields;

        console.log("Vendor Name:", invoice.vendorName?.value);
        console.log("Customer Name:", invoice.customerName?.value);
        console.log("Invoice Date:", invoice.invoiceDate?.value);
        console.log("Due Date:", invoice.dueDate?.value);

        console.log("Items:");
        for (const {
                properties: item
            } of invoice.items?.values ?? []) {
            console.log("-", item.productCode?.value ?? "<no product code>");
            console.log("  Description:", item.description?.value);
            console.log("  Quantity:", item.quantity?.value);
            console.log("  Date:", item.date?.value);
            console.log("  Unit:", item.unit?.value);
            console.log("  Unit Price:", item.unitPrice?.value);
            console.log("  Tax:", item.tax?.value);
            console.log("  Amount:", item.amount?.value);
        }

        console.log("Subtotal:", invoice.subTotal?.value);
        console.log("Previous Unpaid Balance:", invoice.previousUnpaidBalance?.value);
        console.log("Tax:", invoice.totalTax?.value);
        console.log("Amount Due:", invoice.amountDue?.value);
    } else {
        throw new Error("Expected at least one receipt in the result.");
    }
}

main().catch((error) => {
    console.error("An error occurred:", error);
    process.exit(1);
});

```

## <a name="run-your-application"></a>Ausführen der Anwendung

1. Navigieren Sie zu dem Ordner, in dem Ihre Formularerkennungsanwendung (form-recognizer-app) installiert ist.

1. Geben Sie in Ihrem Terminal den folgenden Befehl ein:

```console
node index.js
```

Herzlichen Glückwunsch! In diesem Schnellstart haben Sie das JavaScript SDK für die Formularerkennung verwendet, um verschiedene Formulare auf unterschiedliche Arten zu analysieren. Lesen Sie als Nächstes die Referenzdokumentation, um weitere Informationen zur API für Formularerkennung v3.0 zu erhalten.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Referenzdokumentation zur REST-API v3.0](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)

> [!div class="nextstepaction"]
> [JavaScript-Referenzbibliothek für die Formularerkennung](https://azuresdkdocs.blob.core.windows.net/$web/javascript/azure-ai-form-recognizer/4.0.0-beta.1/index.html)
