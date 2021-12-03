---
title: Verwenden der Clientbibliothek der Formularerkennung für C#/.NET
description: Verwenden Sie die Clientbibliothek der Formularerkennung für C#/.NET, um eine Formularverarbeitungs-App zu erstellen, die Schlüssel-Wert-Paare und Tabellendaten aus Ihren benutzerdefinierten Dokumenten extrahiert.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/02/2021
ms.author: lajanuar
ms.custom: devx-track-csharp, ignite-fall-2021
ms.openlocfilehash: dcc00675a0ded5e3c0dc00d8c13f3539171dca00
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131094927"
---
<!-- markdownlint-disable MD024 -->

<!-- markdownlint-disable MD033 -->
> [!IMPORTANT]
>
 > * Dieses Projekt ist auf die Formularerkennung REST-API **v2.1** ausgerichtet.
>
>* Im Code dieses Artikels werden der Einfachheit halber synchrone Methoden und ein ungeschützter Anmeldeinformationsspeicher verwendet.

[Referenzdokumentation](/dotnet/api/overview/azure/ai.formrecognizer-readme) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src) | [Paket (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer) | [Beispiele](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services/)
* Die [Visual Studio-IDE](https://visualstudio.microsoft.com/vs/) oder die aktuelle Version von [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Trainingsdaten in einem Azure Storage-Blob. Tipps und Optionen für das Zusammenstellen eines Trainingsdatasets finden Sie unter [Erstellen eines Trainingsdatasets für ein benutzerdefiniertes Modell](../../build-training-data-set.md). Für dieses Projekt können Sie die Dateien im Ordner **Trainieren** des [Beispieldatasets](https://go.microsoft.com/fwlink/?linkid=2090451) (herunterladen und extrahieren von *sample_data.zip*) verwenden.
* Sobald Sie über Ihr Azure-Abonnement verfügen, sollten Sie über <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Erstellen einer Formularerkennungsressource"  target="_blank"> im Azure-Portal eine Formularerkennungsressource </a> erstellen, um Ihren Schlüssel und Endpunkt abzurufen. Wählen Sie nach Abschluss der Bereitstellung **Zu Ressource wechseln** aus.
  * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Formularerkennungs-API zu verbinden. Fügen Sie Ihren Schlüssel und Endpunkt später im Projekt in den nachfolgenden Code ein.
  * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

## <a name="setting-up"></a>Einrichten

Verwenden Sie in einem Konsolenfenster (z. B. cmd, PowerShell oder Bash) den Befehl `dotnet new` zum Erstellen einer neuen Konsolen-App mit dem Namen `formrecognizer-project`. Dieser Befehl erstellt ein einfaches C#-Projekt vom Typ „Hallo Welt“ mit einer einzelnen Quelldatei: *program.cs*.

```console
dotnet new console -n formrecognizer-project
```

Wechseln Sie zum Ordner der neu erstellten App. Sie können die Anwendung mit folgendem Befehl erstellen:

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

Installieren Sie im Anwendungsverzeichnis mit dem folgenden Befehl die Formularerkennungs-Clientbibliothek für .NET:

```console
dotnet add package Azure.AI.FormRecognizer --version 3.1.1
```

Öffnen Sie aus dem Projektverzeichnis die Datei *Program.cs* in Ihrem bevorzugten Editor oder Ihrer bevorzugten IDE. Fügen Sie die folgenden `using`-Anweisungen hinzu:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_using)]

Erstellen Sie in der **Program**-Klasse der Anwendung Variablen für den Schlüssel und Endpunkt Ihrer Ressource.

> [!IMPORTANT]
> Öffnen Sie das Azure-Portal. Wenn die im Abschnitt **Voraussetzungen** erstellte Formularerkennungsressource erfolgreich bereitgestellt wurde, klicken Sie unter **Nächste Schritte** auf die Schaltfläche **Zu Ressource wechseln**. Schlüssel und Endpunkt finden Sie auf der Seite mit dem **Schlüssel und dem Endpunkt** der Ressource unter **Ressourcenverwaltung**.
>
> Denken Sie daran, den Schlüssel aus Ihrem Code zu entfernen, wenn Sie fertig sind, und ihn niemals zu veröffentlichen. Verwenden Sie in der Produktionsumgebung sichere Methoden, um Ihre Anmeldeinformationen zu speichern und darauf zuzugreifen. Weitere Informationen _finden Sie_ im Cognitive Services-Artikel zur [Sicherheit](../../../../cognitive-services/cognitive-services-security.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_creds)]

Fügen Sie in der Methode **Main** der Anwendung einen Aufruf der asynchronen Aufgaben hinzu, die in diesem Projekt verwendet werden. Die Implementierung erfolgt zu einem späteren Zeitpunkt:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_main)]

## <a name="object-model"></a>Objektmodell

Mit der Formularerkennung können Sie zwei verschiedene Clienttypen erstellen. Der erste (`FormRecognizerClient`) wird zum Abfragen des Diensts nach erkannten Formularfeldern und -inhalten verwendet. Der zweite (`FormTrainingClient`) wird zum Erstellen und Verwalten von benutzerdefinierten Modellen verwendet, um die Erkennung zu verbessern.

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient` stellt Vorgänge für Folgendes bereit:

* Erkennen von Formularfeldern und -inhalten mithilfe von benutzerdefinierten Modellen, die zur Analyse Ihrer benutzerdefinierten Formulare trainiert wurden.  Diese Werte werden in einer Sammlung von `RecognizedForm`-Objekten zurückgegeben. Sehen Sie sich das Beispiel zum [Analysieren benutzerdefinierter Formulare](#analyze-forms-with-a-custom-model) an.
* Erkennen von Formularinhalten (einschließlich Tabellen, Zeilen und Wörtern), ohne dass ein Modell trainiert werden muss.  Der Formularinhalt wird in einer Sammlung von `FormPage`-Objekten zurückgegeben. Weitere Informationen finden Sie im Beispiel zum [Analysieren des Layouts](#analyze-layout).
* Erkennen gängiger Felder in US-amerikanischen Belegen, Visitenkarten, Rechnungen und Ausweisdokumenten unter Verwendung eines vorab trainierten Modells für den Formularerkennungsdienst

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient` stellt Vorgänge für Folgendes bereit:

* Trainieren benutzerdefinierter Modelle, um alle Felder und Werte in Ihren benutzerdefinierten Formularen zu analysieren.  Ein `CustomFormModel`-Element wird zurückgegeben, das angibt, welche Formulartypen vom Modell analysiert und welche Felder für jeden Formtyp extrahiert werden.
* Trainieren benutzerdefinierter Modelle zum Analysieren bestimmter Felder und Werte, die Sie durch Bezeichnen Ihrer benutzerdefinierten Formulare angeben.  Ein `CustomFormModel`-Element wird zurückgegeben, das die vom Modell extrahierten Felder sowie die geschätzte Genauigkeit für jedes Feld angibt.
* Verwalten der in Ihrem Konto erstellten Modelle
* Kopieren eines benutzerdefinierten Modells aus einer Formularerkennungsressource in eine andere

Sehen Sie sich die Beispiele zum [Trainieren eines Modells](#train-a-custom-model) und zum [Verwalten eines benutzerdefinierten Modells](#manage-custom-models) an.

> [!NOTE]
> Modelle können auch mithilfe einer grafischen Benutzeroberfläche trainiert werden, z. B. mit dem [Formularerkennungstool für die Bezeichnung](../../label-tool.md).

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Erstellen Sie unterhalb von **Main** eine neue Methode mit dem Namen `AuthenticateClient`. Sie verwenden diese Methode in anderen Aufgaben, um Ihre Anforderungen beim Formularerkennungsdienst zu authentifizieren. Diese Methode verwendet das `AzureKeyCredential`-Objekt, damit Sie bei Bedarf den API-Schlüssel aktualisieren können, ohne neue Clientobjekte zu erstellen.

> [!IMPORTANT]
> Rufen Sie Ihren Schlüssel und Endpunkt im Azure-Portal ab. Wenn die im Abschnitt **Voraussetzungen** erstellte Formularerkennungsressource erfolgreich bereitgestellt wurde, klicken Sie unter **Nächste Schritte** auf die Schaltfläche **Zu Ressource wechseln**. Schlüssel und Endpunkt finden Sie auf der Seite mit dem **Schlüssel und dem Endpunkt** der Ressource unter **Ressourcenverwaltung**.
>
> Denken Sie daran, den Schlüssel aus Ihrem Code zu entfernen, wenn Sie fertig sind, und ihn niemals zu veröffentlichen. Verwenden Sie in der Produktionsumgebung sichere Methoden, um Ihre Anmeldeinformationen zu speichern und darauf zuzugreifen. Beispielsweise [Azure Key Vault](../../../../key-vault/general/overview.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_auth)]

Wiederholen Sie die oben genannten Schritte für eine neue Methode, mit der ein Trainingsclient authentifiziert wird.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_auth_training)]

## <a name="get-assets-for-testing"></a>Ressourcen zum Testen abrufen

Sie müssen außerdem Verweise auf die URLs für Ihre Trainings- und Testdaten hinzufügen. Fügen Sie diese Verweise dem Stamm Ihrer **Program**-Klasse hinzu.

* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS-URL-Abruf":::
* Führen Sie anschließend erneut die obigen Schritte aus, um die SAS-URL eines einzelnen Dokuments im Blobspeichercontainer abzurufen. Speichern Sie sie ebenfalls an einem temporären Speicherort.
* Speichern Sie abschließend die URL der Beispielbilder (unten enthalten und auch auf [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms) verfügbar).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_urls)]

## <a name="analyze-layout"></a>Analysieren des Layouts

Mit der Formularerkennung können Sie Tabellen, Zeilen und Wörter in Dokumenten analysieren, ohne ein Modell trainieren zu müssen. Der zurückgegebene Wert ist eine Sammlung aus **FormPage**-Objekten: eines für jede Seite im übermittelten Dokument. Weitere Informationen zur Layoutextraktion finden Sie im [Konzeptleitfaden zum Layout](../../concept-layout.md).

Verwenden Sie die Methode `StartRecognizeContentFromUri`, um den Inhalt einer Datei unter einer angegebenen URL zu analysieren.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_getcontent_call)]

> [!TIP]
> Außerdem können Sie Inhalte aus einer lokalen Datei abrufen. Mehr dazu erfahren Sie bei den [FormRecognizerClient](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient)-Methoden, z. B. **StartRecognizeContent**. Alternativ finden Sie im Beispielcode auf [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) Szenarien zu lokalen Bildern.

Im restlichen Teil dieser Aufgabe werden die Inhaltsinformationen in der Konsole ausgegeben.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_getcontent_print)]

### <a name="output"></a>Output

```console
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    Line 5 has 3 words, and text: '6000 Redmond, WA'.
    Line 6 has 3 words, and text: 'Sunnayvale, CA 87659'.
    Line 7 has 1 word, and text: '99243'.
    Line 8 has 2 words, and text: 'Invoice Number'.
    Line 9 has 2 words, and text: 'Invoice Date'.
    Line 10 has 3 words, and text: 'Invoice Due Date'.
    Line 11 has 1 word, and text: 'Charges'.
    Line 12 has 2 words, and text: 'VAT ID'.
    Line 13 has 1 word, and text: '34278587'.
    Line 14 has 1 word, and text: '6/18/2017'.
    Line 15 has 1 word, and text: '6/24/2017'.
    Line 16 has 1 word, and text: '$56,651.49'.
    Line 17 has 1 word, and text: 'PT'.
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    Cell (0, 3) contains text: 'Charges'.
    Cell (0, 5) contains text: 'VAT ID'.
    Cell (1, 0) contains text: '34278587'.
    Cell (1, 1) contains text: '6/18/2017'.
    Cell (1, 2) contains text: '6/24/2017'.
    Cell (1, 3) contains text: '$56,651.49'.
    Cell (1, 5) contains text: 'PT'.
```

## <a name="analyze-receipts"></a>Analysieren von Belegen

In diesem Abschnitt wird veranschaulicht, wie Sie mithilfe eines vorab trainierten Belegmodells gängige Felder in US-Belegen analysieren und extrahieren. Weitere Informationen zur Beleganalyse finden Sie im [Konzeptleitfaden zu Belegen](../../concept-receipt.md).

Verwenden Sie die Methode `StartRecognizeReceiptsFromUri`, um Belege unter einer URL zu analysieren.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_receipt_call)]

> [!TIP]
> Auch lokale Belegbilder können analysiert werden. Mehr dazu erfahren Sie bei den [FormRecognizerClient](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient)-Methoden, z. B. **StartRecognizeReceipts**. Alternativ finden Sie im Beispielcode auf [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) Szenarien zu lokalen Bildern.

Der zurückgegebene Wert ist eine Sammlung aus `RecognizedForm`-Objekten: eines für jede Seite im übermittelten Dokument. Mit dem folgenden Code wird der Beleg unter dem angegebenen URI verarbeitet, und die wichtigsten Felder und Werte werden in der Konsole ausgegeben.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_receipt_print)]

### <a name="output"></a>Output

```console
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    Line 5 has 3 words, and text: '6000 Redmond, WA'.
    Line 6 has 3 words, and text: 'Sunnayvale, CA 87659'.
    Line 7 has 1 word, and text: '99243'.
    Line 8 has 2 words, and text: 'Invoice Number'.
    Line 9 has 2 words, and text: 'Invoice Date'.
    Line 10 has 3 words, and text: 'Invoice Due Date'.
    Line 11 has 1 word, and text: 'Charges'.
    Line 12 has 2 words, and text: 'VAT ID'.
    Line 13 has 1 word, and text: '34278587'.
    Line 14 has 1 word, and text: '6/18/2017'.
    Line 15 has 1 word, and text: '6/24/2017'.
    Line 16 has 1 word, and text: '$56,651.49'.
    Line 17 has 1 word, and text: 'PT'.
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    Cell (0, 3) contains text: 'Charges'.
    Cell (0, 5) contains text: 'VAT ID'.
    Cell (1, 0) contains text: '34278587'.
    Cell (1, 1) contains text: '6/18/2017'.
    Cell (1, 2) contains text: '6/24/2017'.
    Cell (1, 3) contains text: '$56,651.49'.
    Cell (1, 5) contains text: 'PT'.
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
```

## <a name="analyze-business-cards"></a>Analysieren von Visitenkarten

In diesem Abschnitt wird veranschaulicht, wie Sie mithilfe eines vorab trainierten Modells gängige Felder englischsprachiger Visitenkarten analysieren und extrahieren. Weitere Informationen zur Analyse von Visitenkarten finden Sie im [Konzeptleitfaden zu Visitenkarten](../../concept-business-card.md).

Verwenden Sie die Methode `StartRecognizeBusinessCardsFromUriAsync`, um Visitenkarten unter einer URL zu analysieren.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_bc_call)]

> [!TIP]
> Auch lokale Belegbilder können analysiert werden. Sehen Sie sich dazu die Methoden vom Typ [FormRecognizerClient](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) an (beispielsweise **StartRecognizeBusinessCards**). Alternativ finden Sie im Beispielcode auf [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) Szenarien zu lokalen Bildern.

Der folgende Code verarbeitet die Visitenkarte unter dem angegebenen URI und gibt die wichtigsten Felder und Werte in der Konsole aus:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_bc_print)]

## <a name="analyze-invoices"></a>Analysieren von Rechnungen

In diesem Abschnitt wird veranschaulicht, wie Sie mithilfe eines vorab trainierten Modells gängige Rechnungsfelder analysieren und extrahieren. Weitere Informationen zur Rechnungsanalyse finden Sie im [Konzeptleitfaden zu Rechnungen](../../concept-invoice.md).

Verwenden Sie die Methode `StartRecognizeInvoicesFromUriAsync`, um Rechnungen unter einer URL zu analysieren.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_invoice_call)]

> [!TIP]
> Auch lokale Rechnungsbilder können analysiert werden. Sehen Sie sich dazu die Methoden vom Typ [FormRecognizerClient](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) an (beispielsweise **StartRecognizeInvoices**). Alternativ finden Sie im Beispielcode auf [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) Szenarien zu lokalen Bildern.

Der folgende Code verarbeitet die Rechnung unter dem angegebenen URI und gibt die wichtigsten Felder und Werte in der Konsole aus:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_invoice_print)]

## <a name="analyze-id-documents"></a>Analysieren von Ausweisdokumenten

In diesem Abschnitt wird veranschaulicht, wie Sie mithilfe des vordefinierten Formularerkennungs-ID-Modells wichtige Informationen aus von staatlichen Behörden ausgestellten Ausweisdokumenten (internationale Reisepässe und US-Führerscheine) analysieren und extrahieren. Weitere Informationen zur Ausweisdokumentanalyse finden Sie unter [Vordefiniertes ID-Modell der Formularerkennung für Ausweise](../../concept-id-document.md).

Verwenden Sie die Methode `StartRecognizeIdentityDocumentsFromUriAsync`, um Ausweisdokumente unter einem URI zu analysieren.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_id_call)]

> [!TIP]
> Auch lokale Ausweisdokumentbilder können analysiert werden. Sehen Sie sich dazu die Methoden vom Typ [FormRecognizerClient](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) an (beispielsweise **StartRecognizeIdentityDocumentsAsync**). Sie finden auch im Beispielcode auf [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) Szenarien zu lokalen Bildern.

Der folgende Code verarbeitet das Ausweisdokument unter dem angegebenen URI und gibt die wichtigsten Felder und Werte in der Konsole aus:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_id_print)]

## <a name="train-a-custom-model"></a>Trainieren eines benutzerdefinierten Modells

In diesem Abschnitt wird gezeigt, wie Sie ein Modell mit eigenen Daten trainieren. Ein trainiertes Modell kann strukturierte Daten ausgeben, die die Schlüssel-Wert-Beziehungen im ursprünglichen Formulardokument enthalten. Nachdem das Modell trainiert wurde, können Sie es testen, neu trainieren und schließlich verwenden, um Daten aus weiteren Formularen zuverlässig nach Ihren Bedürfnissen zu extrahieren.

> [!NOTE]
> Sie können Modelle auch mithilfe einer grafischen Benutzeroberfläche trainieren, z. B. dem [Formularerkennungstool für die Bezeichnung von Beispielen](../../label-tool.md).

### <a name="train-a-model-without-labels"></a>Trainieren eines Modells ohne Bezeichnungen

Trainieren Sie benutzerdefinierte Modelle, sodass alle Felder und Werte in Ihren benutzerdefinierten Formularen analysiert werden, ohne dass Sie die Trainingsdokumente manuell beschriften müssen. Die folgende Methode trainiert ein Modell mit einem angegebenen Dokumentensatz und gibt den Status des Modells an der Konsole aus.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train)]

Das zurückgegebene `CustomFormModel`-Objekt enthält Informationen zu den Formulartypen, die vom Modell analysiert werden können, und zu den Feldern, die das Modell aus jedem Formulartyp extrahieren kann. Der folgende Codeblock gibt diese Informationen an der Konsole aus.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train_response)]

Abschließend wird die ID des trainierten Modells zur späteren Verwendung in weiteren Schritten zurückgegeben.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train_return)]

### <a name="output"></a>Output

Diese Antwort wurde zur besseren Lesbarkeit gekürzt.

```console
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    ...
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    Cell (0, 3) contains text: 'Charges'.
    ...
Custom Model Info:
    Model Id: 95035721-f19d-40eb-8820-0c806b42798b
    Model Status: Ready
    Training model started on: 8/24/2020 6:36:44 PM +00:00
    Training model completed on: 8/24/2020 6:36:50 PM +00:00
Submodel Form Type: form-95035721-f19d-40eb-8820-0c806b42798b
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    ...
Custom Model Info:
    Model Id: e7a1181b-1fb7-40be-bfbe-1ee154183633
    Model Status: Ready
    Training model started on: 8/24/2020 6:36:44 PM +00:00
    Training model completed on: 8/24/2020 6:36:52 PM +00:00
Submodel Form Type: form-0
    FieldName: field-0, FieldLabel: Additional Notes:
    FieldName: field-1, FieldLabel: Address:
    FieldName: field-2, FieldLabel: Company Name:
    FieldName: field-3, FieldLabel: Company Phone:
    FieldName: field-4, FieldLabel: Dated As:
    FieldName: field-5, FieldLabel: Details
    FieldName: field-6, FieldLabel: Email:
    FieldName: field-7, FieldLabel: Hero Limited
    FieldName: field-8, FieldLabel: Name:
    FieldName: field-9, FieldLabel: Phone:
    ...
```

### <a name="train-a-model-with-labels"></a>Trainieren eines Modells mit Bezeichnungen

Sie können benutzerdefinierte Modelle auch trainieren, indem Sie die Trainingsdokumente manuell bezeichnen. Das Training mit Bezeichnungen führt in einigen Szenarien zu einer besseren Leistung. Zum Training mit Bezeichnungen benötigen Sie zusätzlich zu den Trainingsdokumenten spezielle Informationsdateien mit Bezeichnungen (`\<filename\>.pdf.labels.json`) in Ihrem Blobspeichercontainer. Das [Formularerkennungstool für die Bezeichnung von Beispielen](../../label-tool.md) bietet eine Benutzeroberfläche, auf der Sie diese Bezeichnungsdateien erstellen können. Sobald Sie darüber verfügen, können Sie die `StartTrainingAsync`-Methode mit dem auf `true` festgelegten Parameter `uselabels` aufrufen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_trainlabels)]

Das zurückgegebene `CustomFormModel` gibt die Felder an, die das Modell extrahieren kann, und bietet Informationen zur geschätzten Genauigkeit in jedem Feld. Der folgende Codeblock gibt diese Informationen an der Konsole aus.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_trainlabels_response)]

### <a name="output"></a>Output

Diese Antwort wurde zur besseren Lesbarkeit gekürzt.

```console
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    Line 5 has 3 words, and text: '6000 Redmond, WA'.
    ...
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    ...
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
Custom Model Info:
    Model Id: 63c013e3-1cab-43eb-84b0-f4b20cb9214c
    Model Status: Ready
    Training model started on: 8/24/2020 6:42:54 PM +00:00
    Training model completed on: 8/24/2020 6:43:01 PM +00:00
Submodel Form Type: form-63c013e3-1cab-43eb-84b0-f4b20cb9214c
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    FieldName: DatedAs
    FieldName: Email
    FieldName: Merchant
    ...
```

## <a name="analyze-forms-with-a-custom-model"></a>Analysieren von Formularen mit einem benutzerdefinierten Modell

In diesem Abschnitt wird veranschaulicht, wie Sie mithilfe von Modellen, die Sie mit Ihren eigenen Formularen trainiert haben, Schlüssel-Wert-Informationen und andere Inhalte aus Ihren benutzerdefinierten Formulartypen extrahieren.

> [!IMPORTANT]
> Um dieses Szenario zu implementieren, müssen Sie bereits ein Modell trainiert haben, sodass Sie seine ID an die unten stehende Methode übergeben können.

Sie verwenden die Methode `StartRecognizeCustomFormsFromUri`.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_analyze)]

> [!TIP]
> Sie können auch eine lokale Datei analysieren. Mehr dazu erfahren Sie bei den [FormRecognizerClient](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient)-Methoden, z. B. **StartRecognizeCustomForms**. Alternativ finden Sie im Beispielcode auf [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) Szenarien zu lokalen Bildern.

Der zurückgegebene Wert ist eine Sammlung aus `RecognizedForm`-Objekten: eines für jede Seite im übermittelten Dokument. Der folgende Code gibt die Analyseergebnisse an der Konsole aus. Der Code gibt jedes erkannte Feld und den zugehörigen Wert sowie eine Zuverlässigkeitsbewertung aus.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_analyze_response)]

### <a name="output"></a>Output

Diese Antwort wurde zur besseren Lesbarkeit gekürzt.

```console
Custom Model Info:
    Model Id: 9b0108ee-65c8-450e-b527-bb309d054fc4
    Model Status: Ready
    Training model started on: 8/24/2020 7:00:31 PM +00:00
    Training model completed on: 8/24/2020 7:00:32 PM +00:00
Submodel Form Type: form-9b0108ee-65c8-450e-b527-bb309d054fc4
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    ...
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    ...

Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    ...
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
Custom Model Info:
    Model Id: dc115156-ce0e-4202-bbe4-7426e7bee756
    Model Status: Ready
    Training model started on: 8/24/2020 7:00:31 PM +00:00
    Training model completed on: 8/24/2020 7:00:41 PM +00:00
Submodel Form Type: form-0
    FieldName: field-0, FieldLabel: Additional Notes:
    FieldName: field-1, FieldLabel: Address:
    FieldName: field-2, FieldLabel: Company Name:
    FieldName: field-3, FieldLabel: Company Phone:
    FieldName: field-4, FieldLabel: Dated As:
    ...
Form of type: custom:form
Field 'Azure.AI.FormRecognizer.Models.FieldValue:
    Value: '$56,651.49
    Confidence: '0.249
Field 'Azure.AI.FormRecognizer.Models.FieldValue:
    Value: 'PT
    Confidence: '0.245
Field 'Azure.AI.FormRecognizer.Models.FieldValue:
    Value: '99243
    Confidence: '0.114
   ...
```

## <a name="manage-custom-models"></a>Verwalten benutzerdefinierter Modelle

In diesem Abschnitt wird veranschaulicht, wie Sie die in Ihrem Konto gespeicherten benutzerdefinierten Modelle verwalten. Sie führen in der folgenden Methode mehrere Vorgänge durch:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage)]

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Überprüfen der Anzahl von Modellen im FormRecognizer-Ressourcenkonto

Der folgende Codeblock überprüft, wie viele Modelle in Ihrem Formularerkennungskonto gespeichert sind, und vergleicht diese Zahl mit dem Kontolimit.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_count)]

### <a name="output"></a>Output

```console
Account has 20 models.
It can have at most 5000 models.
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Auflisten der zurzeit im Ressourcenkonto gespeicherten Modelle

Der folgende Codeblock listet die aktuell in Ihrem Konto vorhandenen Modelle auf und gibt ihre Details an der Konsole aus.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_list)]

### <a name="output"></a>Output

Diese Antwort wurde zur besseren Lesbarkeit gekürzt.

```console
Custom Model Info:
    Model Id: 05932d5a-a2f8-4030-a2ef-4e5ed7112515
    Model Status: Creating
    Training model started on: 8/24/2020 7:35:02 PM +00:00
    Training model completed on: 8/24/2020 7:35:02 PM +00:00
Custom Model Info:
    Model Id: 150828c4-2eb2-487e-a728-60d5d504bd16
    Model Status: Ready
    Training model started on: 8/24/2020 7:33:25 PM +00:00
    Training model completed on: 8/24/2020 7:33:27 PM +00:00
Custom Model Info:
    Model Id: 3303e9de-6cec-4dfb-9e68-36510a6ecbb2
    Model Status: Ready
    Training model started on: 8/24/2020 7:29:27 PM +00:00
    Training model completed on: 8/24/2020 7:29:36 PM +00:00
```

### <a name="get-a-specific-model-using-the-models-id"></a>Abrufen eines bestimmten Modells anhand der Modell-ID

Der folgende Codeblock trainiert ein neues Modell (so wie im Abschnitt [Trainieren eines Modells](#train-a-model-without-labels) beschrieben) und ruft dann anhand der Modell-ID einen zweiten Verweis darauf ab.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_get)]

### <a name="output"></a>Output

Diese Antwort wurde zur besseren Lesbarkeit gekürzt.

```console
Custom Model Info:
    Model Id: 150828c4-2eb2-487e-a728-60d5d504bd16
    Model Status: Ready
    Training model started on: 8/24/2020 7:33:25 PM +00:00
    Training model completed on: 8/24/2020 7:33:27 PM +00:00
Submodel Form Type: form-150828c4-2eb2-487e-a728-60d5d504bd16
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    FieldName: DatedAs
    FieldName: Email
    FieldName: Merchant
    FieldName: PhoneNumber
    FieldName: PurchaseOrderNumber
    FieldName: Quantity
    FieldName: Signature
    FieldName: Subtotal
    FieldName: Tax
    FieldName: Total
    FieldName: VendorName
    FieldName: Website
...
```

### <a name="delete-a-model-from-the-resource-account"></a>Löschen eines Modells aus dem Ressourcenkonto

Sie können ein Modell auch aus Ihrem Konto löschen, indem Sie auf die ID des Modells verweisen. In diesem Schritt wird die Methode auch geschlossen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_delete)]

## <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie die Anwendung mit dem Befehl `dotnet run` aus dem Anwendungsverzeichnis aus.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Cognitive Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind.

* [Portal](../../../../cognitive-services/cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-Befehlszeilenschnittstelle](../../../../cognitive-services/cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Problembehandlung

Wenn Sie über das .NET SDK mit der Formularerkennungs-Clientbibliothek von Cognitive Services interagieren, führen vom Dienst zurückgegebene Fehler zu einer `RequestFailedException`. Diese enthalten denselben HTTP-Statuscode, der auch von einer REST-API-Anforderung zurückgegeben würde.

Ein Beispiel: Wenn Sie ein Belegbild mit einem ungültigen URI übermitteln, wird ein `400`-Fehler zurückgegeben, der auf eine ungültige Anforderung hinweist.

```csharp
try
{
    RecognizedReceiptCollection receipts = await client.StartRecognizeReceiptsFromUri(new Uri(receiptUri)).WaitForCompletionAsync();
}
catch (RequestFailedException e)
{
    Console.WriteLine(e.ToString());
}
```

Sie werden feststellen, dass zusätzliche Informationen protokolliert werden, wie etwa die Clientanforderungs-ID des Vorgangs.

```console

Message:
    Azure.RequestFailedException: Service request failed.
    Status: 400 (Bad Request)

Content:
    {"error":{"code":"FailedToDownloadImage","innerError":
    {"requestId":"8ca04feb-86db-4552-857c-fde903251518"},
    "message":"Failed to download image from input URL."}}

Headers:
    Transfer-Encoding: chunked
    x-envoy-upstream-service-time: REDACTED
    apim-request-id: REDACTED
    Strict-Transport-Security: REDACTED
    X-Content-Type-Options: REDACTED
    Date: Mon, 20 Apr 2020 22:48:35 GMT
    Content-Type: application/json; charset=utf-8
```

## <a name="next-steps"></a>Nächste Schritte

Für dieses Projekt haben Sie die Clientbibliothek der Formularerkennung für .NET verwendet, um auf unterschiedliche Weise Modelle zu trainieren und Formulare zu analysieren. Lesen Sie im Anschluss die Tipps zum Erstellen eines besseren Trainingsdatasets und zum Generieren genauerer Modelle.

> [!div class="nextstepaction"]
> [Erstellen eines Trainingsdatasets](../../build-training-data-set.md)

* [Was ist die Formularerkennung?](../../overview.md)

* Der Beispielcode für dieses Projekt ist auf [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/FormRecognizer/csharp-sdk-quickstart.cs) verfügbar.
