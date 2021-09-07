---
title: Clientbibliothek für die Dokumentübersetzung (C#/.NET oder Python)
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie die C#/.NET- oder Python-Clientbibliothek (SDK) der Textübersetzung verwenden, um einen cloudbasierten Batchdienst und -prozess für die Dokumentübersetzung zu erhalten.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 07/06/2021
ms.author: lajanuar
ms.openlocfilehash: b7bcf5339f6bff6a0f055e2016bcd3e12bfd5f45
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122354812"
---
# <a name="document-translation-client-library-sdks"></a>Clientbibliothek-SDKs für die Dokumentübersetzung
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD001 -->
Die [Dokumentübersetzung](overview.md) ist ein cloudbasiertes Feature des [Azure-Textübersetzungsdiensts](../translator-overview.md). Sie können ganze Dokumente übersetzen oder Batchdokumentübersetzungen in verschiedenen Dateiformaten verarbeiten und dabei die ursprüngliche Dokumentstruktur und das ursprüngliche Format beibehalten. In diesem Artikel erfahren Sie, wie Sie die C#/.NET- und Python-Clientbibliotheken des Dokumentübersetzungsdiensts verwenden. Informationen zur REST-API finden Sie in unserer [Schnellstartanleitung](get-started-with-document-translation.md).

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

* Ein aktives [**Azure-Konto**](https://azure.microsoft.com/free/cognitive-services/).  Falls Sie noch kein Konto haben, können Sie ein [**kostenloses Konto erstellen**](https://azure.microsoft.com/free/).

* Eine [**Textübersetzungsressource in Form eines einzelnen Diensts**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) (**keine** Cognitive Services-Ressource mit mehreren Diensten).

* Ein [**Azure Blob Storage-Konto**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Für Quell- und Zieldateien müssen Sie in Ihrem Azure-Blobspeicherkonto [**Container erstellen**](../../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container).

  * **Quellcontainer**: In diesen Container laden Sie Ihre Dateien für die Übersetzung hoch (erforderlich).
  * **Zielcontainer**: In diesem Container werden Ihre übersetzten Dateien gespeichert (erforderlich).

* Außerdem müssen SAS-Token (Shared Access Signature) für Ihre Quell- und Zielcontainer erstellt werden. `sourceUrl` und `targetUrl` müssen ein als Abfragezeichenfolge angefügtes SAS-Token (Shared Access Signature) enthalten. Das Token kann Ihrem Container oder bestimmten Blobs zugewiesen sein. *Informationen hierzu finden Sie unter* [**Erstellen von SAS-Token für die Verarbeitung der Dokumentübersetzung**](create-sas-tokens.md).

  * Ihr **Quellcontainer** oder Blob muss über Zugriff vom Typ **Lesen** und **Auflisten** verfügen.
  * Ihr **Zielcontainer** oder Blob muss über Zugriff vom Typ **Schreiben** und **Auflisten** verfügen.

Weitere Informationen finden Sie unter [Erstellen von SAS-Token für die Verarbeitung der Dokumentübersetzung](create-sas-tokens.md).

## <a name="client-libraries"></a>Clientbibliotheken

### <a name="cnet"></a>[C#/.NET](#tab/csharp)

| [Paket (NuGet)][documenttranslation_nuget_package] | [Clientbibliothek][documenttranslation_client_library_docs] |  [REST-API][documenttranslation_rest_api] | [Produktdokumentation][documenttranslation_docs] | [Beispiele][documenttranslation_samples] |

> [!IMPORTANT]
> Dies ist eine Vorabversion des SDK für die Dokumentübersetzung. Sie wird verfügbar gemacht, damit Kunden frühzeitig Zugriff erhalten und Feedback geben können. Vorabversionen befinden sich noch in der Entwicklung, werden unter Umständen noch geändert, und bestimmte Features werden ggf. nicht unterstützt oder verfügen möglicherweise über eingeschränkte Funktionen. Verwenden Sie sie daher nicht in Produktionsanwendungen.

### <a name="set-up-your-project"></a>Einrichten des Projekts

Verwenden Sie in einem Konsolenfenster (z. B. cmd, PowerShell oder Bash) den Befehl `dotnet new` zum Erstellen einer neuen Konsolen-App mit dem Namen `batch-document-translation`. Dieser Befehl erstellt ein einfaches C#-Projekt vom Typ „Hallo Welt“ mit einer einzelnen Quelldatei: *program.cs*.

```console
dotnet new console -n batch-document-translation
```

Wechseln Sie zum Ordner der neu erstellten App. Erstellen Sie Ihre Anwendung mithilfe des folgenden Befehls:

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

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek

Installieren Sie im Anwendungsverzeichnis die Clientbibliothek für die Dokumentübersetzung für .NET mithilfe einer der folgenden Methoden:

#### <a name="net-cli"></a>**.NET CLI**

```console
dotnet add package Azure.AI.Translation.Document --version 1.0.0-beta.2
```

#### <a name="nuget-package-manager"></a>**NuGet-Paket-Manager**

```console
Install-Package Azure.AI.Translation.Document -Version 1.0.0-beta.2
```

#### <a name="nuget-packagereference"></a>**NuGet PackageReference**

```xml
<ItemGroup>
    <!-- ... -->
<PackageReference Include="Azure.AI.Translation.Document" Version="1.0.0-beta.2" />
    <!-- ... -->
</ItemGroup>
```

Öffnen Sie aus dem Projektverzeichnis die Datei Program.cs in Ihrem bevorzugten Editor oder Ihrer bevorzugten IDE. Fügen Sie die folgenden using-Direktiven hinzu:

```csharp
using Azure;
using Azure.AI.Translation.Document;

using System;
using System.Threading;
```

Erstellen Sie in der Klasse **Program** der Anwendung eine Variable für Ihren Abonnementschlüssel und Ihren benutzerdefinierten Endpunkt. Ausführliche Informationen finden Sie unter [Beschaffen des Namens Ihrer benutzerdefinierten Domäne und des Abonnementschlüssels](get-started-with-document-translation.md#custom-domain-name-and-subscription-key).

```csharp
private static readonly string endpoint = "<your custom endpoint>";
private static readonly string subscriptionKey = "<your subscription key>";
```

### <a name="translate-a-document-or-batch-files"></a>Übersetzen von einzelnen Dokumenten oder Batchdateien

* Um einen Übersetzungsvorgang für ein einzelnes Dokument oder für mehrere Dokumente in einem einzelnen Blobcontainer zu starten, wird die Methode `StartTranslationAsync` aufgerufen.

* Zum Aufrufen von `StartTranslationAsync` muss ein Objekt vom Typ `DocumentTranslationInput` initialisiert werden, das die folgenden Parameter enthält:

* **sourceUri**: Der SAS-URI für den Quellcontainer mit den zu übersetzenden Dokumenten.
* **targetUri**: Der SAS-URI für den Zielcontainer, in den die übersetzten Dokumente geschrieben werden.
* **targetLanguageCode**: Der Sprachcode für die übersetzten Dokumente. Sprachcodes finden Sie auf der Seite [Sprachunterstützung für Text- und Sprachübersetzung](../language-support.md).

```csharp

public void StartTranslation() {
  Uri sourceUri = new Uri("<sourceUrl>");
  Uri targetUri = new Uri("<targetUrl>");

  DocumentTranslationClient client = new DocumentTranslationClient(new Uri(endpoint), new AzureKeyCredential(subscriptionKey));

  DocumentTranslationInput input = new DocumentTranslationInput(sourceUri, targetUri, "es")

  DocumentTranslationOperation operation = await client.StartTranslationAsync(input);

  await operation.WaitForCompletionAsync();

  Console.WriteLine($ "  Status: {operation.Status}");
  Console.WriteLine($ "  Created on: {operation.CreatedOn}");
  Console.WriteLine($ "  Last modified: {operation.LastModified}");
  Console.WriteLine($ "  Total documents: {operation.DocumentsTotal}");
  Console.WriteLine($ "    Succeeded: {operation.DocumentsSucceeded}");
  Console.WriteLine($ "    Failed: {operation.DocumentsFailed}");
  Console.WriteLine($ "    In Progress: {operation.DocumentsInProgress}");
  Console.WriteLine($ "    Not started: {operation.DocumentsNotStarted}");

  await foreach(DocumentStatusResult document in operation.Value) {
    Console.WriteLine($ "Document with Id: {document.DocumentId}");
    Console.WriteLine($ "  Status:{document.Status}");
    if (document.Status == TranslationStatus.Succeeded) {
      Console.WriteLine($ "  Translated Document Uri: {document.TranslatedDocumentUri}");
      Console.WriteLine($ "  Translated to language: {document.TranslatedTo}.");
      Console.WriteLine($ "  Document source Uri: {document.SourceDocumentUri}");
    }
    else {
      Console.WriteLine($ "  Error Code: {document.Error.ErrorCode}");
      Console.WriteLine($ "  Message: {document.Error.Message}");
    }
  }
}
```

Das ist alles! Sie haben mithilfe der .NET-Clientbibliothek ein Programm zum Übersetzen von Dokumenten in einem Blobcontainer erstellt.

### <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
 > [**Erkunden weiterer C#-Codebeispiele**](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.Translation.Document_1.0.0-beta.2/sdk/translation/Azure.AI.Translation.Document/samples)

<!-- LINKS -->

[documenttranslation_nuget_package]: https://www.nuget.org/packages/Azure.AI.Translation.Document/1.0.0-beta.2
[documenttranslation_client_library_docs]: /dotnet/api/azure.ai.translation.document
[documenttranslation_docs]: overview.md
[documenttranslation_rest_api]: reference/rest-api-guide.md
[documenttranslation_samples]: https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/translation/Azure.AI.Translation.Document/samples

### <a name="python"></a>[Python](#tab/python)

| [Paket (PyPI)][python-dt-pypi] |  [Clientbibliothek][python-dt-client-library] |  [REST-API][python-rest-api] | [Produktdokumentation][python-dt-product-docs] | [Beispiele][python-dt-samples] |

> [!IMPORTANT]
> Dies ist eine Vorabversion des SDK für die Dokumentübersetzung. Sie wird verfügbar gemacht, damit Kunden frühzeitig Zugriff erhalten und Feedback geben können. Vorabversionen befinden sich noch in der Entwicklung, werden unter Umständen noch geändert, und bestimmte Features werden ggf. nicht unterstützt oder verfügen möglicherweise über eingeschränkte Funktionen. Verwenden Sie sie daher nicht in Produktionsanwendungen.

### <a name="set-up-your-project"></a>Einrichten des Projekts

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek

Sofern noch nicht geschehen, installieren Sie [Python](https://www.python.org/downloads/) und anschließend die neueste Clientbibliothek für die Textübersetzung:

```console
pip install azure-ai-translation-document
```

### <a name="create-your-application"></a>Erstellen Ihrer Anwendung

Erstellen Sie eine neue Python-Anwendung in Ihrem bevorzugten Editor oder Ihrer bevorzugten IDE. Importieren Sie dann die folgenden Bibliotheken:

```python
    import os
    from azure.core.credentials import AzureKeyCredential
    from azure.ai.translation.document import DocumentTranslationClient
```

Erstellen Sie Variablen für Ihren Ressourcenabonnementschlüssel und Ihren benutzerdefinierten Endpunkt sowie für die Quell-URL (sourceUrl) und die Ziel-URL (targetUrl). Weitere Informationen finden Sie unter [Beschaffen des Namens Ihrer benutzerdefinierten Domäne und des Abonnementschlüssels](get-started-with-document-translation.md#custom-domain-name-and-subscription-key).

```python
 subscriptionKey = "<your-subscription-key>"
 endpoint = "<your-custom-endpoint>"
 sourceUrl = "<your-container-sourceUrl>"
 targetUrl = "<your-container-targetUrl>"
```

### <a name="translate-a-document-or-batch-files"></a>Übersetzen von einzelnen Dokumenten oder Batchdateien

```python
client = DocumentTranslationClient(endpoint, AzureKeyCredential(subscriptionKey))

    poller = client.begin_translation(sourceUrl, targetUrl, "fr")
    result = poller.result()

    print("Status: {}".format(poller.status()))
    print("Created on: {}".format(poller.details.created_on))
    print("Last updated on: {}".format(poller.details.last_updated_on))
    print("Total number of translations on documents: {}".format(poller.details.documents_total_count))

    print("\nOf total documents...")
    print("{} failed".format(poller.details.documents_failed_count))
    print("{} succeeded".format(poller.details.documents_succeeded_count))

    for document in result:
        print("Document ID: {}".format(document.id))
        print("Document status: {}".format(document.status))
        if document.status == "Succeeded":
            print("Source document location: {}".format(document.source_document_url))
            print("Translated document location: {}".format(document.translated_document_url))
            print("Translated to language: {}\n".format(document.translated_to))
        else:
            print("Error Code: {}, Message: {}\n".format(document.error.code, document.error.message))
```

Das ist alles! Sie haben mithilfe der Python-Clientbibliothek ein Programm zum Übersetzen von Dokumenten in einem Blobcontainer erstellt.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erkunden weiterer Python-Codebeispiele](https://github.com/Azure/azure-sdk-for-python/tree/azure-ai-translation-document_1.0.0b1/sdk/translation/azure-ai-translation-document/samples)

<!-- LINKS -->
[python-dt-pypi]: https://aka.ms/azsdk/python/texttranslation/pypi
[python-dt-client-library]: https://aka.ms/azsdk/python/documenttranslation/docs
[python-rest-api]: reference/rest-api-guide.md
[python-dt-product-docs]: overview.md
[python-dt-samples]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/translation/azure-ai-translation-document/samples

---