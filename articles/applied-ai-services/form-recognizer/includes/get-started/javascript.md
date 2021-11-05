---
title: 'Schnellstart: JavaScript SDK für Formularerkennung v2.1'
titleSuffix: Azure Applied AI Services
description: Formular- und Dokumentverarbeitung, Datenextraktion und Analyse mit JavaScript-Clientbibliothek für Formularerkennung v2.1
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: f978333bdb10433f19831b5255010dacef7c81aa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030212"
---
> [!IMPORTANT]
>
> In dieser Schnellstartanleitung geht es um die REST-API **v2.1** für die Azure-Formularerkennung.

[Referenzdokumentation](/javascript/api/overview/azure/ai-form-recognizer-readme?view=azure-node-latest&preserve-view=true) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/formrecognizer/ai-form-recognizer/) | [Paket (npm)](https://www.npmjs.com/package/@azure/ai-form-recognizer) | [Beispiele](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)

In dieser Schnellstartanleitung verwenden Sie die folgenden APIs zum Extrahieren strukturierter Daten aus Formularen und Dokumenten:

* [Layout](#try-it-layout-model)

* [Rechnung](#try-it-prebuilt-model)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services/)

* Die aktuelle Version von [Visual Studio Code](https://code.visualstudio.com/) oder Ihrer bevorzugten IDE

* Die aktuelle LTS-Version von [Node.js](https://nodejs.org/about/releases/)

* Eine Cognitive Services- oder Formularerkennungsressource. Wenn Sie über Ihr Azure-Abonnement verfügen, können Sie im Azure-Portal eine Formularerkennungsressource mit [einem Dienst](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) oder [mehreren Diensten](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) erstellen, um Ihren Schlüssel und Endpunkt zu erhalten. Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

    > [!TIP]
    > Erstellen Sie eine Cognitive Services-Ressource, wenn Sie planen, über einen einzelnen Endpunkt bzw. Schlüssel auf mehrere Cognitive Services-Instanzen zuzugreifen. Erstellen Sie eine Formularerkennungsressource, falls nur auf die Formularerkennung zugegriffen werden soll. Beachten Sie hierbei, dass Sie eine Ressource mit einem einzelnen Dienst benötigen, falls Sie die [Azure Active Directory-Authentifizierung](/azure/active-directory/authentication/overview-authentication) nutzen möchten.

* Wählen Sie nach der Bereitstellung der Ressource **Zu Ressource wechseln** aus. Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Formularerkennungs-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt:

  :::image type="content" source="../../media/containers/keys-and-endpoint.png" alt-text="Screenshot: Schlüssel und Endpunktspeicherort im Azure-Portal.":::

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
    npm install @azure/ai-form-recognizer
    ```

    Die Datei `package.json` Ihrer App wird mit den Abhängigkeiten aktualisiert.

1. Erstellen Sie eine Datei mit dem Namen `index.js`, öffnen Sie sie, und importieren Sie die folgenden Bibliotheken:

    ```javascript
    const { FormRecognizerClient, AzureKeyCredential } = require("@azure/ai-form-recognizer");
    ```

1. Erstellen Sie Variablen für den Azure-Endpunkt und -Schlüssel Ihrer Ressource:

    ```javascript
    const apiKey = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
    const endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";
    ```

1. An diesem Punkt sollte Ihre JavaScript-Anwendung die folgenden Codezeilen enthalten:

    ```javascript

    const { FormRecognizerClient, AzureKeyCredential } = require("@azure/ai-form-recognizer");

    const endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";
    const apiKey = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
    ```

### <a name="select-a-code-sample-to-copy-and-paste-into-your-application"></a>Wählen Sie ein Codebeispiel zum Kopieren und Einfügen in Ihre Anwendung aus:

* [**Layout**](#try-it-layout-model)

* [**Vordefinierte Rechnung**](#try-it-prebuilt-model)

> [!IMPORTANT]
>
> Denken Sie daran, den Schlüssel aus Ihrem Code zu entfernen, wenn Sie fertig sind, und ihn niemals zu veröffentlichen. Verwenden Sie in der Produktionsumgebung sichere Methoden, um Ihre Anmeldeinformationen zu speichern und darauf zuzugreifen. Weitere Informationen finden Sie in unserem Artikel zur Cognitive Services-[Sicherheit]((/azure/cognitive-services/cognitive-services-security.md).

## <a name="try-it-layout-model"></a>**Try it** (Ausprobieren): Layoutmodell

> [!div class="checklist"]
>
> * Für dieses Beispiel benötigen Sie eine **Formulardokumentdatei unter einem URI**. Sie können für diese Schnellstartanleitung unser [Beispielformulardokument](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) verwenden.
> * Sie haben den Datei-URI-Wert der Variable `formUrl` im oberen Bereich der Datei hinzugefügt.
> * Für die Analyse einer bestimmten Datei unter einem URI verwenden Sie die `beginRecognizeContent`-Methode.

### <a name="add-the-following-code-to-your-layout-application-on-the-line-below-the-apikey-variable"></a>Fügen Sie Ihrer Layoutanwendung in der Zeile unterhalb der Variablen `apiKey` den folgenden Code hinzu:

```javascript
const formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf";

const formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf";

async function recognizeContent() {
    const client = new FormRecognizerClient(endpoint, new AzureKeyCredential(apiKey));
    const poller = await client.beginRecognizeContentFromUrl(formUrl);
    const pages = await poller.pollUntilDone();

    if (!pages || pages.length === 0) {
        throw new Error("Expecting non-empty list of pages!");
    }

    for (const page of pages) {
        console.log(
            `Page ${page.pageNumber}: width ${page.width} and height ${page.height} with unit ${page.unit}`
        );
        for (const table of page.tables) {
            for (const cell of table.cells) {
                console.log(`cell [${cell.rowIndex},${cell.columnIndex}] has text ${cell.text}`);
            }
        }
    }
}

recognizeContent().catch((err) => {
    console.error("The sample encountered an error:", err);
});

```

## <a name="try-it-prebuilt-model"></a>**Probieren Sie es aus:** Vordefiniertes Modell

In diesem Beispiel wird veranschaulicht, wie Sie Daten aus bestimmten Arten von häufig verwendeten Dokumenten mit vortrainierten Modellen analysieren, indem eine Rechnung als Beispiel verwendet wird. Eine vollständige Liste mit [**Rechnungsfeldern**](../../concept-invoice.md#field-extraction) *finden Sie auf unserer Seite mit den vordefinierten Konzepten*.

> [!div class="checklist"]
>
> * In diesem Beispiel analysieren Sie ein Rechnungsdokument mithilfe eines vordefinierten Modells. Für diese Schnellstartanleitung können Sie unser [Beispiel für ein Rechnungsdokument](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf) verwenden.
> * Sie haben den Datei-URI-Wert der Variable `invoiceUrl` im oben in der Datei hinzugefügt.
> * Für die Analyse einer bestimmten Datei unter einem URI verwenden Sie die `beginRecognizeInvoices`-Methode.
> * Der Einfachheit halber werden hier nicht alle Felder angezeigt, die vom Dienst zurückgegeben werden. Eine Liste aller unterstützten Felder mit den entsprechenden Typen finden Sie auf der Konzeptseite [Rechnung](../../concept-invoice.md#field-extraction).

### <a name="choose-a-prebuilt-model"></a>Auswählen eines vordefinierten Modells

Sie sind hierbei nicht auf Rechnungen beschränkt, sondern können zwischen mehreren vordefinierten Modellen wählen, von denen jedes über eine Gruppe unterstützter Felder verfügt. Welches Modell für den Analysevorgang verwendet wird, hängt vom Typ des zu analysierenden Dokuments ab. Dies sind die vordefinierten Modelle, die vom Formularerkennungsdienst derzeit unterstützt werden:

* [**Rechnung**](../../concept-invoice.md): Extrahieren von Text, Auswahlmarkierungen, Tabellen, Feldern und wichtigen Informationen aus Rechnungen
* [**Beleg**](../../concept-receipt.md): Extrahieren von Text und wichtigen Informationen aus Belegen
* [**Ausweisdokument**](../../concept-id-document.md): Extrahieren von Text und wichtigen Informationen aus Führerscheinen und Reisepässen
* [**Visitenkarte**](../../concept-business-card.md): Extrahieren von Text und wichtigen Informationen aus Visitenkarten

### <a name="add-the-following-code-to-your-prebuilt-invoice-application-below-the-apikey-variable"></a>Fügen Sie Ihrer vordefinierten Rechnungsanwendung unterhalb der Variablen `apiKey` den folgenden Code hinzu:

```javascript

const invoiceUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf";

async function recognizeInvoices() {

    const client = new FormRecognizerClient(endpoint, new AzureKeyCredential(apiKey));

    const poller = await client.beginRecognizeInvoicesFromUrl(invoiceUrl);
    const [invoice] = await poller.pollUntilDone();

    if (invoice === undefined) {
        throw new Error("Failed to extract data from at least one invoice.");
    }

    /**
     * This is a helper function for printing a simple field with an elemental type.
     */
    function fieldToString(field) {
        const {
            name,
            valueType,
            value,
            confidence
        } = field;
        return `${name} (${valueType}): '${value}' with confidence ${confidence}'`;
    }

    console.log("Invoice fields:");

    /**
     * Invoices contain a lot of optional fields, but they are all of elemental types
     * such as strings, numbers, and dates, so we will just enumerate them all.
     */
    for (const [name, field] of Object.entries(invoice.fields)) {
        if (field.valueType !== "array" && field.valueType !== "object") {
            console.log(`- ${name} ${fieldToString(field)}`);
        }
    }

    // Invoices also support nested line items, so we can iterate over them.
    let idx = 0;

    console.log("- Items:");

    const items = invoice.fields["Items"]?.value;
    for (const item of items ?? []) {
        const value = item.value;

        // Each item has several subfields that are nested within the item. We'll
        // map over this list of the subfields and filter out any fields that
        // weren't found. Not all fields will be returned every time, only those
        // that the service identified for the particular document in question.

        const subFields = [
                "Description",
                "Quantity",
                "Unit",
                "UnitPrice",
                "ProductCode",
                "Date",
                "Tax",
                "Amount"
            ]
            .map((fieldName) => value[fieldName])
            .filter((field) => field !== undefined);

        console.log(
            [
                `  - Item #${idx}`,
                // Now we will convert those fields into strings to display
                ...subFields.map((field) => `    - ${fieldToString(field)}`)
            ].join("\n")
        );
    }
}

recognizeInvoices().catch((err) => {
    console.error("The sample encountered an error:", err);
});

```

Herzlichen Glückwunsch! In diesem Schnellstart haben Sie das JavaScript SDK für die Formularerkennung verwendet, um verschiedene Formulare auf unterschiedliche Arten zu analysieren. Lesen Sie als Nächstes die Referenzdokumentation, um weitere Informationen zur API für Formularerkennung v3.0 zu erhalten.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Referenzdokumentation zur REST-API v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)

> [!div class="nextstepaction"]
> [JavaScript-Referenzbibliothek für die Formularerkennung](/javascript/api/overview/azure/ai-form-recognizer-readme?view=azure-node-latest&preserve-view=true)
