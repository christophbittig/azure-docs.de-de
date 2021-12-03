---
title: 'Erste Schritte: Formularerkennungs-Clientbibliothek für Python v2.1'
titleSuffix: Azure Applied AI Services
description: Formular- und Dokumentverarbeitung, Datenextraktion und Analyse mit Formularerkennungs-Clientbibliothek für Python v2.1
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: includes
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: f323d19c1941837c92a66f18eaf8be88260f058e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030215"
---
> [!IMPORTANT]
>
> In dieser Schnellstartanleitung geht es um die REST-API **v2.1** für die Azure-Formularerkennung.
>

[Referenzdokumentation](/python/api/azure-ai-formrecognizer) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/azure/ai/formrecognizer) | [Paket (PyPi)](https://pypi.org/project/azure-ai-formrecognizer/) | [Beispiele](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)

In dieser Schnellstartanleitung verwenden Sie die folgenden APIs zum Extrahieren strukturierter Daten aus Formularen und Dokumenten:

* [Layout](#try-it-layout-model)

* [Rechnung](#try-it-prebuilt-model)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)

* [Python 3.x](https://www.python.org/)

  * Ihre Python-Installation sollte [pip](https://pip.pypa.io/en/stable/) enthalten. Sie können überprüfen, ob pip installiert ist, indem Sie `pip --version` in der Befehlszeile ausführen. Installieren Sie die aktuelle Python-Version, um pip zu erhalten.

* Eine Cognitive Services- oder Formularerkennungsressource. Wenn Sie über Ihr Azure-Abonnement verfügen, können Sie im Azure-Portal eine Formularerkennungsressource mit [einem Dienst](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) oder [mehreren Diensten](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) erstellen, um Ihren Schlüssel und Endpunkt zu erhalten. Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

    > [!TIP]
    > Erstellen Sie eine Cognitive Services-Ressource, wenn Sie planen, über einen einzelnen Endpunkt bzw. Schlüssel auf mehrere Cognitive Services-Instanzen zuzugreifen. Erstellen Sie eine Formularerkennungsressource, falls nur auf die Formularerkennung zugegriffen werden soll. Beachten Sie hierbei, dass Sie eine Ressource mit einem einzelnen Dienst benötigen, falls Sie die [Azure Active Directory-Authentifizierung](/azure/active-directory/authentication/overview-authentication) nutzen möchten.

* Wählen Sie nach der Bereitstellung der Ressource **Zu Ressource wechseln** aus. Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Formularerkennungs-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt:

  :::image type="content" source="../../media/containers/keys-and-endpoint.png" alt-text="Screenshot: Schlüssel und Endpunktspeicherort im Azure-Portal.":::

## <a name="set-up"></a>Einrichten

Öffnen Sie in Ihrer lokalen Umgebung ein Terminalfenster, und installieren Sie die Azure-Formularerkennung-Clientbibliothek für Python mit pip:

```console
pip install azure-ai-formrecognizer

```

### <a name="create-a-new-python-application"></a>Erstellen einer neuen Python-Anwendung

Erstellen Sie mit Ihrem bevorzugten Editor bzw. Ihrer IDE eine neue Python-Anwendung mit dem Namen **form_recognizer_quickstart.py**. Importieren Sie anschließend die folgenden Bibliotheken:

```python
import os
from azure.ai.formrecognizer import FormRecognizerClient
from azure.core.credentials import AzureKeyCredential
```

### <a name="create-variables-for-your-azure-resource-endpoint-and-key"></a>Erstellen von Variablen für den Endpunkt und Schlüssel Ihrer Azure-Ressource

```python
endpoint = "YOUR_FORM_RECOGNIZER_ENDPOINT"
key = "YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY"
```

An diesem Punkt sollte Ihre Python-Anwendung die folgenden Codezeilen enthalten:

```python
import os
from azure.core.exceptions import ResourceNotFoundError
from azure.ai.formrecognizer import DocumentAnalysisClient
from azure.core.credentials import AzureKeyCredential

endpoint = "YOUR_FORM_RECOGNIZER_ENDPOINT"
key = "YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY"
```

### <a name="select-a-code-sample-to-copy-and-paste-into-your-application"></a>Wählen Sie ein Codebeispiel zum Kopieren und Einfügen in Ihre Anwendung aus:

* [**Layout**](#try-it-layout-model)

* [**Vordefinierte Rechnung**](#try-it-prebuilt-model)

> [!IMPORTANT]
>
> Denken Sie daran, den Schlüssel aus Ihrem Code zu entfernen, wenn Sie fertig sind, und ihn niemals zu veröffentlichen. Verwenden Sie in der Produktionsumgebung sichere Methoden, um Ihre Anmeldeinformationen zu speichern und darauf zuzugreifen. Weitere Informationen finden Sie im Cognitive Services-Artikel zur [Sicherheit](/azure/cognitive-services/cognitive-services-security.md).

## <a name="try-it-layout-model"></a>**Try it** (Ausprobieren): Layoutmodell

> [!div class="checklist"]
>
> * Für dieses Beispiel benötigen Sie eine **Formulardokumentdatei unter einem URI**. Sie können für diese Schnellstartanleitung unser [Beispielformulardokument](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) verwenden.
> * Sie haben den Datei-URI-Wert der Variable `formUrl` im oberen Bereich der Datei hinzugefügt.
> * Für die Analyse einer bestimmten Datei unter einem URI verwenden Sie die `begin_recognize_content_from_url`-Methode.

### <a name="add-the-following-code-to-your-layout-application-on-the-line-below-the-key-variable"></a>Fügen Sie Ihrer Layoutanwendung in der Zeile unterhalb der Variablen `key` den folgenden Code hinzu:

```python

  def format_bounding_box(bounding_box):
    if not bounding_box:
        return "N/A"
    return ", ".join(["[{}, {}]".format(p.x, p.y) for p in bounding_box])

 def recognize_content():
    # sample form document
    formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf"

    form_recognizer_client = FormRecognizerClient(
        endpoint=endpoint, credential=AzureKeyCredential(key)
    )

    poller = form_recognizer_client.begin_recognize_content_from_url(formUrl)
    form_pages = poller.result()

    for idx, content in enumerate(form_pages):
        print(
            "Page has width: {} and height: {}, measured with unit: {}".format(
                content.width, content.height, content.unit
            )
        )
        for table_idx, table in enumerate(content.tables):
            print(
                "Table # {} has {} rows and {} columns".format(
                    table_idx, table.row_count, table.column_count
                )
            )
            print(
                "Table # {} location on page: {}".format(
                    table_idx, format_bounding_box(table.bounding_box)
                )
            )
            for cell in table.cells:
                print(
                    "...Cell[{}][{}] has text '{}' within bounding box '{}'".format(
                        cell.row_index,
                        cell.column_index,
                        cell.text,
                        format_bounding_box(cell.bounding_box),
                    )
                )

        for line_idx, line in enumerate(content.lines):
            print(
                "Line # {} has word count '{}' and text '{}' within bounding box '{}'".format(
                    line_idx,
                    len(line.words),
                    line.text,
                    format_bounding_box(line.bounding_box),
                )
            )
            if line.appearance:
                if (
                    line.appearance.style_name == "handwriting"
                    and line.appearance.style_confidence > 0.8
                ):
                    print(
                        "Text line '{}' is handwritten and might be a signature.".format(
                            line.text
                        )
                    )
            for word in line.words:
                print(
                    "...Word '{}' has a confidence of {}".format(
                        word.text, word.confidence
                    )
                )

        for selection_mark in content.selection_marks:
            print(
                "Selection mark is '{}' within bounding box '{}' and has a confidence of {}".format(
                    selection_mark.state,
                    format_bounding_box(selection_mark.bounding_box),
                    selection_mark.confidence,
                )
            )
        print("----------------------------------------")


if __name__ == "__main__":
    recognize_content()
```

## <a name="try-it-prebuilt-model"></a>**Probieren Sie es aus:** Vordefiniertes Modell

In diesem Beispiel wird veranschaulicht, wie Sie Daten aus bestimmten Arten von häufig verwendeten Dokumenten mit vortrainierten Modellen analysieren, indem eine Rechnung als Beispiel verwendet wird. Eine vollständige Liste mit [**Rechnungsfeldern**](../../concept-invoice.md#field-extraction) *finden Sie auf unserer Seite mit den vordefinierten Konzepten*.

> [!div class="checklist"]
>
> * In diesem Beispiel analysieren Sie ein Rechnungsdokument mithilfe eines vordefinierten Modells. Für diese Schnellstartanleitung können Sie unser [Beispiel für ein Rechnungsdokument](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf) verwenden.
> * Wir haben den Datei-URI-Wert der Variablen „formUrl“ oben in der Datei hinzugefügt.
> * Sie verwenden die Methode „begin_recognize_invoices_from_url“, um eine bestimmte Datei unter einem URI zu analysieren.
> * Der Einfachheit halber werden hier nicht alle Felder angezeigt, die vom Dienst zurückgegeben werden. Eine Liste aller unterstützten Felder mit den entsprechenden Typen finden Sie auf der Konzeptseite [Rechnung](../../concept-invoice.md#field-extraction).

### <a name="choose-a-prebuilt-model"></a>Auswählen eines vordefinierten Modells

Sie sind hierbei nicht auf Rechnungen beschränkt, sondern können zwischen mehreren vordefinierten Modellen wählen, von denen jedes über eine Gruppe unterstützter Felder verfügt. Welches Modell für den Analysevorgang verwendet wird, hängt vom Typ des zu analysierenden Dokuments ab. Dies sind die vordefinierten Modelle, die vom Formularerkennungsdienst derzeit unterstützt werden:

* [**Rechnung**](../../concept-invoice.md): Extrahieren von Text, Auswahlmarkierungen, Tabellen, Feldern und wichtigen Informationen aus Rechnungen
* [**Beleg**](../../concept-receipt.md): Extrahieren von Text und wichtigen Informationen aus Belegen
* [**Ausweisdokument**](../../concept-id-document.md): Extrahieren von Text und wichtigen Informationen aus Führerscheinen und Reisepässen
* [**Visitenkarte**](../../concept-business-card.md): Extrahieren von Text und wichtigen Informationen aus Visitenkarten

### <a name="add-the-following-code-to-your-prebuilt-invoice-application-below-the-key-variable"></a>Fügen Sie Ihrer vordefinierten Rechnungsanwendung unterhalb der Variablen `key` den folgenden Code hinzu:

```python

def recognize_invoice():

    invoiceUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf"

    form_recognizer_client = FormRecognizerClient(
        endpoint=endpoint, credential=AzureKeyCredential(key)
    )

    poller = form_recognizer_client.begin_recognize_invoices_from_url(
        invoiceUrl, locale="en-US"
    )
    invoices = poller.result()

    for idx, invoice in enumerate(invoices):
        vendor_name = invoice.fields.get("VendorName")
        if vendor_name:
            print(
                "Vendor Name: {} has confidence: {}".format(
                    vendor_name.value, vendor_name.confidence
                )
            )
        vendor_address = invoice.fields.get("VendorAddress")
        if vendor_address:
            print(
                "Vendor Address: {} has confidence: {}".format(
                    vendor_address.value, vendor_address.confidence
                )
            )
        vendor_address_recipient = invoice.fields.get("VendorAddressRecipient")
        if vendor_address_recipient:
            print(
                "Vendor Address Recipient: {} has confidence: {}".format(
                    vendor_address_recipient.value, vendor_address_recipient.confidence
                )
            )
        customer_name = invoice.fields.get("CustomerName")
        if customer_name:
            print(
                "Customer Name: {} has confidence: {}".format(
                    customer_name.value, customer_name.confidence
                )
            )
        customer_id = invoice.fields.get("CustomerId")
        if customer_id:
            print(
                "Customer Id: {} has confidence: {}".format(
                    customer_id.value, customer_id.confidence
                )
            )
        customer_address = invoice.fields.get("CustomerAddress")
        if customer_address:
            print(
                "Customer Address: {} has confidence: {}".format(
                    customer_address.value, customer_address.confidence
                )
            )
        customer_address_recipient = invoice.fields.get("CustomerAddressRecipient")
        if customer_address_recipient:
            print(
                "Customer Address Recipient: {} has confidence: {}".format(
                    customer_address_recipient.value,
                    customer_address_recipient.confidence,
                )
            )
        invoice_id = invoice.fields.get("InvoiceId")
        if invoice_id:
            print(
                "Invoice Id: {} has confidence: {}".format(
                    invoice_id.value, invoice_id.confidence
                )
            )
        invoice_date = invoice.fields.get("InvoiceDate")
        if invoice_date:
            print(
                "Invoice Date: {} has confidence: {}".format(
                    invoice_date.value, invoice_date.confidence
                )
            )
        invoice_total = invoice.fields.get("InvoiceTotal")
        if invoice_total:
            print(
                "Invoice Total: {} has confidence: {}".format(
                    invoice_total.value, invoice_total.confidence
                )
            )
        due_date = invoice.fields.get("DueDate")
        if due_date:
            print(
                "Due Date: {} has confidence: {}".format(
                    due_date.value, due_date.confidence
                )
            )
        purchase_order = invoice.fields.get("PurchaseOrder")
        if purchase_order:
            print(
                "Purchase Order: {} has confidence: {}".format(
                    purchase_order.value, purchase_order.confidence
                )
            )
        billing_address = invoice.fields.get("BillingAddress")
        if billing_address:
            print(
                "Billing Address: {} has confidence: {}".format(
                    billing_address.value, billing_address.confidence
                )
            )
        billing_address_recipient = invoice.fields.get("BillingAddressRecipient")
        if billing_address_recipient:
            print(
                "Billing Address Recipient: {} has confidence: {}".format(
                    billing_address_recipient.value,
                    billing_address_recipient.confidence,
                )
            )
        shipping_address = invoice.fields.get("ShippingAddress")
        if shipping_address:
            print(
                "Shipping Address: {} has confidence: {}".format(
                    shipping_address.value, shipping_address.confidence
                )
            )
        shipping_address_recipient = invoice.fields.get("ShippingAddressRecipient")
        if shipping_address_recipient:
            print(
                "Shipping Address Recipient: {} has confidence: {}".format(
                    shipping_address_recipient.value,
                    shipping_address_recipient.confidence,
                )
            )
        print("Invoice items:")
        for idx, item in enumerate(invoice.fields.get("Items").value):
            item_description = item.value.get("Description")
            if item_description:
                print(
                    "......Description: {} has confidence: {}".format(
                        item_description.value, item_description.confidence
                    )
                )
            item_quantity = item.value.get("Quantity")
            if item_quantity:
                print(
                    "......Quantity: {} has confidence: {}".format(
                        item_quantity.value, item_quantity.confidence
                    )
                )
            unit = item.value.get("Unit")
            if unit:
                print(
                    "......Unit: {} has confidence: {}".format(
                        unit.value, unit.confidence
                    )
                )
            unit_price = item.value.get("UnitPrice")
            if unit_price:
                print(
                    "......Unit Price: {} has confidence: {}".format(
                        unit_price.value, unit_price.confidence
                    )
                )
            product_code = item.value.get("ProductCode")
            if product_code:
                print(
                    "......Product Code: {} has confidence: {}".format(
                        product_code.value, product_code.confidence
                    )
                )
            item_date = item.value.get("Date")
            if item_date:
                print(
                    "......Date: {} has confidence: {}".format(
                        item_date.value, item_date.confidence
                    )
                )
            tax = item.value.get("Tax")
            if tax:
                print(
                    "......Tax: {} has confidence: {}".format(tax.value, tax.confidence)
                )
            amount = item.value.get("Amount")
            if amount:
                print(
                    "......Amount: {} has confidence: {}".format(
                        amount.value, amount.confidence
                    )
                )
        subtotal = invoice.fields.get("SubTotal")
        if subtotal:
            print(
                "Subtotal: {} has confidence: {}".format(
                    subtotal.value, subtotal.confidence
                )
            )
        total_tax = invoice.fields.get("TotalTax")
        if total_tax:
            print(
                "Total Tax: {} has confidence: {}".format(
                    total_tax.value, total_tax.confidence
                )
            )
        previous_unpaid_balance = invoice.fields.get("PreviousUnpaidBalance")
        if previous_unpaid_balance:
            print(
                "Previous Unpaid Balance: {} has confidence: {}".format(
                    previous_unpaid_balance.value, previous_unpaid_balance.confidence
                )
            )
        amount_due = invoice.fields.get("AmountDue")
        if amount_due:
            print(
                "Amount Due: {} has confidence: {}".format(
                    amount_due.value, amount_due.confidence
                )
            )
        service_start_date = invoice.fields.get("ServiceStartDate")
        if service_start_date:
            print(
                "Service Start Date: {} has confidence: {}".format(
                    service_start_date.value, service_start_date.confidence
                )
            )
        service_end_date = invoice.fields.get("ServiceEndDate")
        if service_end_date:
            print(
                "Service End Date: {} has confidence: {}".format(
                    service_end_date.value, service_end_date.confidence
                )
            )
        service_address = invoice.fields.get("ServiceAddress")
        if service_address:
            print(
                "Service Address: {} has confidence: {}".format(
                    service_address.value, service_address.confidence
                )
            )
        service_address_recipient = invoice.fields.get("ServiceAddressRecipient")
        if service_address_recipient:
            print(
                "Service Address Recipient: {} has confidence: {}".format(
                    service_address_recipient.value,
                    service_address_recipient.confidence,
                )
            )
        remittance_address = invoice.fields.get("RemittanceAddress")
        if remittance_address:
            print(
                "Remittance Address: {} has confidence: {}".format(
                    remittance_address.value, remittance_address.confidence
                )
            )
        remittance_address_recipient = invoice.fields.get("RemittanceAddressRecipient")
        if remittance_address_recipient:
            print(
                "Remittance Address Recipient: {} has confidence: {}".format(
                    remittance_address_recipient.value,
                    remittance_address_recipient.confidence,
                )
            )


if __name__ == "__main__":
    recognize_invoice()

```

## <a name="run-your-application"></a>Ausführen der Anwendung

1. Navigieren Sie zu dem Ordner, in dem Ihre Datei **form_recognizer_quickstart.py** enthalten ist.

1. Geben Sie in Ihrem Terminal den folgenden Befehl ein:

```console
python form_recognizer_quickstart.py
```

Herzlichen Glückwunsch! In dieser Schnellstartanleitung haben Sie das Python SDK für die Formularerkennung verwendet, um verschiedene Formulare auf unterschiedliche Arten zu analysieren. Lesen Sie als Nächstes die Referenzdokumentation, um weitere Informationen zur API für Formularerkennung v3.0 zu erhalten.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Referenzdokumentation zur REST-API v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)

> [!div class="nextstepaction"]
> [Python-Referenzbibliothek für die Formularerkennung](/python/api/overview/azure/ai-formrecognizer-readme?view=azure-python&preserve-view=true)
