---
title: Neuerungen in der Formularerkennung
titleSuffix: Azure Applied AI Services
description: Informieren Sie sich über die neuesten Änderungen an der Formularerkennungs-API.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: 685a5f6a7eec89f966e165179db7417f7d848d9b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026598"
---
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD036 -->
# <a name="whats-new-in-azure-form-recognizer"></a>Neuerungen in der Azure-Formularerkennung

Der Formularerkennungsdienst wird fortlaufend aktualisiert. Speichern Sie ein Lesezeichen für diese Seite, um bei Versionshinweisen, Verbesserungen von Features und Aktualisierungen der Dokumentation auf dem neuesten Stand zu bleiben.

## <a name="october-2021"></a>Oktober 2021

### <a name="form-recognizer-new-preview-release"></a>Formularerkennung neue Vorabversion

 Die neue Vorschauversion von Formularerkennung führt mehrere neue Funktionen und Möglichkeiten ein:

* [**Allgemeines Dokumentenmodell**](concept-general-document.md) ist eine neue API, die ein vorab trainiertes Modell verwendet, um Text, Tabellen, Strukturen, Schlüssel-Wert-Paare und benannte Entitäten aus Formularen und Dokumenten zu extrahieren.
* [**Hotelquittung**](concept-receipt.md) Modell zur vorgefertigten Quittungsverarbeitung hinzugefügt.
* [**Erweiterte Felder für ID-Dokumente**](concept-id-document.md) Das ID-Modell unterstützt Vermerke, Einschränkungen und die Extraktion der Fahrzeugklassifizierung aus US-Führerscheinen.
* [**Unterschriftenfeld**](concept-custom.md) ist ein neuer Feldtyp in benutzerdefinierten Formularen zur Erkennung des Vorhandenseins einer Unterschrift in einem Formularfeld.

* [**Sprachenerweiterung**](language-support.md) Unterstützung für 122 Sprachen (gedruckt) und 7 Sprachen (handschriftlich). Formularerkennung Layout und Benutzerdefiniertes Formular erweitern mit der neuesten Vorschau die [unterstützten Sprachen](language-support.md) auf 122. Dazu gehört die Textextraktion für gedruckten Text in 49 neuen Sprachen, darunter Russisch, Bulgarisch und andere kyrillische sowie weitere lateinische Sprachen. Darüber hinaus unterstützt die Extraktion von handgeschriebenem Text jetzt 7 Sprachen, darunter Englisch, sowie neue Vorschauen für Chinesisch (vereinfacht), Französisch, Deutsch, Italienisch, Portugiesisch und Spanisch.

* **Verbesserungen bei der Extraktion von Tabellen und Text** Layout unterstützt jetzt die Extraktion von einzeiligen Tabellen, auch Key-Value-Tabellen genannt. Zu den Verbesserungen bei der Textextraktion gehören eine bessere Verarbeitung von digitalen PDFs und Maschinenlesbare Zone Text (MRZ) in Identitätsdokumenten sowie eine allgemeine Leistungssteigerung.

* [**Formularerkennung Studio**](https://formrecognizer.appliedai.azure.com) Um die Nutzung des Dienstes zu vereinfachen, können Sie jetzt auf das Formularerkennungs-Studio zugreifen, um die verschiedenen vorgefertigten Modelle zu testen oder ein benutzerdefiniertes Modell zu beschriften und zu trainieren

Starten Sie mit der neuen [REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm), [Python](quickstarts/try-v3-python-sdk.md) oder [.NET](quickstarts/try-v3-csharp-sdk.md) SDK für die API-Vorschau v3.0.

 #### <a name="form-recognizer-model-data-extraction"></a>Formularerkennung Modelldatenextraktion

  | **Modell**   | **Textextraktion** |**Schlüssel-Werte-Paare** |**Auswahlmarkierungen**   | **Tabellen**   |**Entitäten** |
  | --- | :---: |:---:| :---: | :---: |:---: |
  |🆕Allgemeines Dokument  | ✓  |  ✓ | ✓  | ✓  | ✓  |
  | Layout  | ✓  |   | ✓  | ✓  |   |
  | Rechnung  | ✓ | ✓  | ✓  | ✓ ||
  |Rechnung  | ✓  |   ✓ |   |  ||
  | ID-Dokument | ✓  |   ✓  |   |   ||
  | Visitenkarte    | ✓  |   ✓ |   |   ||
  | Benutzerdefiniert             |✓  |  ✓ | ✓  | ✓  | ✓  |

## <a name="september-2021"></a>September 2021

* [Erweiterte Funktionen des Azure-Metrik-Explorers](../../azure-monitor/essentials/metrics-charts.md) sind in Ihrer Ressourcenübersicht der Formularerkennung im Azure-Portal verfügbar.

    ### <a name="monitoring-menu"></a>Menü „Überwachung“

    :::image type="content" source="media/portal-metrics.png" alt-text="Screenshot des Menüs „Überwachung“ im Azure-Portal":::

    ### <a name="charts"></a>Diagramme

    :::image type="content" source="media/portal-metrics-charts.png" alt-text="Screenshot eines Beispieldiagramms für Metriken im Azure-Portal.":::

*  **ID-Dokument**-Modellaktualisierung: gegebene Namen einschließlich eines Suffixes, mit oder ohne Punkt, erfolgreich verarbeiten:

    |Eingabetext | Ergebnis mit Update |
    |------------|-------------------------------------------|
    | William Isaac Kirby Jr. |**Vorname**: William Isaac</br></br>**Nachname**: Kirby Jr. |
    | Henry Caleb Ross Sr | **Vorname**: Henry Caleb </br></br> **Nachname**: Ross Sr |

## <a name="july-2021"></a>Juli 2021

### <a name="system-assigned-managed-identity-support"></a>Unterstützung für systemseitig zugewiesene verwaltete Identität

 Sie können ab sofort eine systemseitig zugewiesene verwaltete Identität aktivieren, um der Formularerkennung eingeschränkten Zugriff auf private Speicherkonten zu gewähren, einschließlich solcher, die durch ein virtuelles Netzwerk (VNet) oder eine Firewall geschützt sind oder für die BYOS (Bring Your Own Storage) aktiviert ist. *Siehe* [Erstellen und Verwenden einer verwalteten Identität für Ihre Formularerkennungsressource](managed-identity-byos.md) für weitere Informationen.

## <a name="june-2021"></a>Juni 2021

### <a name="form-recognizer-containers-v21-released-in-gated-preview"></a>Container für Formularerkennung v2.1 als geschlossene Vorschauversion veröffentlicht

Die Formularerkennungsfunktionen werden jetzt durch sechs Featurecontainer unterstützt: **Layout**, **Visitenkarte**,**Ausweisdokument**, **Quittung**, **Rechnung** und **Benutzerdefiniert**. Wenn Sie die Container verwenden möchten, müssen Sie eine [Onlineanfrage](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xUNlpBU1lFSjJUMFhKNzVHUUVLN1NIOEZETiQlQCN0PWcu) einreichen und diese genehmigen lassen.

*Siehe* [**Installieren und Ausführen von Docker-Containern für die Formularerkennung**](containers/form-recognizer-container-install-run.md?branch=main&tabs=layout) und [**Konfigurieren von Containern für die Formularerkennung**](containers/form-recognizer-container-configuration.md?branch=main)

### <a name="form-recognizer-connector-released-in-preview"></a>Connector für die Formularerkennung als Vorschauversion veröffentlicht

  Der [**Connector für die Formularerkennung**](/connectors/formrecognizer) ist in [Azure Logic Apps](../../logic-apps/logic-apps-overview.md), [Microsoft Power Automate](/power-automate/getting-started) und [Microsoft Power Apps](/powerapps/powerapps-overview) integrierbar. Der Connector unterstützt Workflowaktionen und Trigger zum Extrahieren und Analysieren von Dokumentdaten und -strukturen aus benutzerdefinierten und vordefinierten Formularen, Rechnungen, Quittungen, Visitenkarten und Ausweisdokumenten.

### <a name="form-recognizer-sdk-v310-patched-to-v311-for-c-java-and-python"></a>SDK v3.1.0 der Formularerkennung für C#, Java und Python auf v3.1.1 gepatcht

Der Patch behebt Rechnungen, bei denen keine Felder für Unterzeilen erkannt werden, z. B. `FormField` mit `Text`, aber ohne `BoundingBox`- oder `Page`-Informationen.

### <a name="c"></a>[**C#**](#tab/csharp)

| [Referenzdokumentation](/dotnet/api/azure.ai.formrecognizer?view=azure-dotnet&preserve-view=true) | [NuGet-Paketversion 3.1.1](https://www.nuget.org/packages/Azure.AI.FormRecognizer) |

### <a name="java"></a>[**Java**](#tab/java)

 | [Referenzdokumentation](/java/api/com.azure.ai.formrecognizer.models?view=azure-java-stable&preserve-view=true)| [Maven-Artefaktpaketabhängigkeit, Version 3.1.1](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer/3.1.1) |

### <a name="javascript"></a>[**JavaScript**](#tab/javascript)

> [!NOTE]
> Es gibt keine Updates für JavaScript SDK v3.1.0.

| [Referenzdokumentation](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-latest&preserve-view=true)| [npm-Paketabhängigkeit, form-recognizer 3.1.0](https://www.npmjs.com/package/@azure/ai-form-recognizer) |

### <a name="python"></a>[**Python**](#tab/python)

| [Referenzdokumentation](/java/api/com.azure.ai.formrecognizer.models?view=azure-java-stable&preserve-view=true)| [PyPi azure-ai-formrecognizer 3.1.1](https://pypi.org/project/azure-ai-formrecognizer/) |

---

## <a name="may-2021"></a>Mai 2021

### <a name="form-recognizer-21-api-generally-available-ga-release"></a>GA-Release der API für Formularerkennung 2.1

* Die Formularerkennung 2.1 ist allgemein verfügbar. Dieses GA-Release (General Availability) gewährleistet die Stabilität von Änderungen, die in Vorschaupaketen vor Version 2.1 eingeführt wurden. Bei dieser Version können Sie Informationen und Daten aus folgenden Ressourcen erkennen und extrahieren:

* [Dokumente](concept-layout.md)
* [Receipts](./concept-receipt.md)
* [Visitenkarten](./concept-business-card.md)
* [Invoices](./concept-invoice.md)
* [Identitätsdokumente](./concept-id-document.md)
* [Benutzerdefinierte Formulare](concept-custom.md)

#### <a name="get-started"></a>Erste Schritte

Wechseln Sie zum [Beispieltool für die Formularerkennung](https://fott-2-1.azurewebsites.net/), und bearbeiten Sie den [Schnellstart](./quickstarts/try-sample-label-tool.md).

### <a name="layout-adds-table-headers"></a>Layout unterstützt jetzt Tabellenkopfzeilen

Das Tabellenfeature der aktualisierten Layout-API wurde um die Kopfzeilenerkennung bei Spaltenköpfen erweitert, die mehrere Zeilen umfassen können. Jede Tabellenzelle verfügt über ein Attribut, das angibt, ob sie Teil einer Kopfzeile ist oder nicht. Anhand dessen kann bestimmt werden, welche Zeilen den Tabellenkopf bilden.

#### <a name="sdk-updates"></a>SDK-Updates

### <a name="c"></a>[**C#**](#tab/csharp)

| [Referenzdokumentation](/dotnet/api/azure.ai.formrecognizer?view=azure-dotnet&preserve-view=true) | [NuGet-Paketversion 3.0.1](https://www.nuget.org/packages/Azure.AI.FormRecognizer) |

#### <a name="non-breaking-changes"></a>**Geringfügige Änderungen**

* Die **FormRecognizerModelFactory**-Klasse unterstützt ab sofort Aktualisierungen von **TextAppearance** und **ReadingOrder** und das Entfernen von **TextStyle**-Modellen. Siehe [Breaking Changes](#breaking-changes-may).

#### <a name="breaking-changes-may"></a>**Breaking Changes (Mai)**

* Der Client verwendet standardmäßig die neueste unterstützte Dienstversion, derzeit v2.1. Sie können Version 2.0 in der Eigenschaft **Version** des Objekts **FormRecognizerClientOptions** angeben.

* **StartRecognizeIdentityDocuments**. Methoden und Methodenparameter mit Verwendung von **Identity** wurden umbenannt, um das Schlüsselwort _Id_ für alle zugehörigen API-Funktionen zur Erkennung von Ausweisdokumenten zu ersetzen.

* **FormReadingOrder**. *ReadingOrder* wurde in **FormReadingOrder** umbenannt.

* **AsCountryRegion**. *AsCountryCode* wurde in **AsCountryRegion** umbenannt.

* **TextAppearance** enthält jetzt die Eigenschaften **StyleName** und **StyleConfidence** (vormals Bestandteil des Objekts **TextStyle**).

* **FieldValueType**.  Der Wert **Gender** wurde aus dem Modell entfernt.

* Das Modell **TextStyle** wurde entfernt.

* Der Typ **FieldValueGender** wurde entfernt.

### <a name="java"></a>[**Java**](#tab/java)

  | [Referenzdokumentation](/java/api/com.azure.ai.formrecognizer.models?view=azure-java-stable&preserve-view=true)| [Maven-Artefaktpaketabhängigkeit, Version 3.1.0](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer) |

#### <a name="non-breaking-changes"></a>**Geringfügige Änderungen**

* **FormRecognizerClientBuilder** und **FormTrainingClientBuilder**. Die Methoden **clientOptions** und **getDefaultLogOptions** wurden hinzugefügt.

* **FormRecognizerLanguage**.  Weitere Sprachenfelder wurden hinzugefügt.

#### <a name="breaking-changes-may"></a>**Breaking Changes (Mai)**

* Der Client verwendet standardmäßig die neueste unterstützte Dienstversion, derzeit v2.1. Sie können Version 2.0 in der Methode **serviceVersion** des Objekts **FormRecognizerClientBuilder** angeben.

* Die Unterstützung für v2.1-preview.1 und v2.1-preview.2 wurde entfernt.

* **beginRecognizeIdentityDocuments**.  Methoden und Methodenparameter mit Verwendung von **Identity** wurden umbenannt, um das Schlüsselwort _Id_ für alle zugehörigen API-Funktionen zur Erkennung von Ausweisdokumenten zu ersetzen.

* **FormReadingOrder**. Die Klasse *ReadingOrder* wurde in **FormReadingOrder** umbenannt und zu einer erweiterbaren Zeichenfolgenklasse umbenannt.

* **asCountryRegion**. Die Methode *asCountry* wurde in **asCountryRegion** umbenannt.

* **FieldValueType**. Der Feldwert *COUNTRY* wurde in **COUNTRY_REGION** umbenannt.

* Die Klasse **TextAppearance** enthält jetzt die Eigenschaften **styleName** und **styleConfidence** (vormals Bestandteil des Objekts **TextStyle**).

* **FieldValueType**. Der Wert *Gender* wurde aus dem Modell entfernt.

* Das Modell **TextStyle** wurde entfernt.

* Der Klassentyp **FieldValueGender** wurde entfernt.

* **pollInterval**. Die pollInterval-Methoden wurden aus den Klassen **RecognizeBusinessCardsOptions**, **RecognizeContentOptions**, **RecognizeCustomFormsOptions**, **RecognizeIdentityDocumentOptions**, **RecognizeInvoicesOptions** und **RecognizeReceiptsOptions** entfernt. Das Abrufintervall kann mithilfe der Azure Core-Methoden [**SyncPoller setPollInterval**](/java/api/com.azure.core.util.polling.syncpoller.setpollinterval?view=azure-java-stable&preserve-view=true) oder [**PollerFlux setPollInterval**](/java/api/com.azure.core.util.polling.pollerflux.setpollinterval?view=azure-java-stable&preserve-view=true) synchron bzw. asynchron aktualisiert werden.

* **FormLine**, **FormPage**, **FormTable**, **FormSelectionMark**, **TextAppearance**, **CustomFormModel**, **CustomFormModelInfo**, **CustomFormModelProperties**, **CustomFormSubmodel** und **TrainingDocumentInfo** sind jetzt unveränderliche Modellklassen.

### <a name="javascript"></a>[**JavaScript**](#tab/javascript)

| [Referenzdokumentation](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-latest&preserve-view=true)| [npm-Paketabhängigkeit, form-recognizer 3.1.0](https://www.npmjs.com/package/@azure/ai-form-recognizer)  |

#### <a name="non-breaking-changes"></a>**Geringfügige Änderungen**

* Alle REST-API-Aufrufe werden zum v2.1-Endpunkt migriert.

* Die Enumeration **KnownFormLocale** wurde hinzugefügt, um auf mögliche Werte von Formulargebietsschemas zu zugreifen.

* **beginRecognizeIdDocuments...** . Methoden und Methodenparameter mit Verwendung von **Identity** wurden umbenannt, um das Schlüsselwort _Id_ für alle zugehörigen API-Funktionen zur Erkennung von Ausweisdokumenten zu ersetzen.

* **FormReadingOrder** und **FormLanguage**. *ReadingOrder* wurde in *FormReadingOrder* umbenannt. *Language* wurde in **FormLanguage** umbenannt.

* **FormCountryRegionField** und **countryRegion**. Der Typ *FormCountryField* wurde in **FormCountryRegionField** umbenannt, und der valueType *country* wurde in **countryRegion** umbenannt.

* Die Schnittstelle **TextAppearance** enthält jetzt die Eigenschaften **styleName** und **styleConfidence** (vormals Namens- und Zuverlässigkeitseigenschaften in der Schnittstelle **TextStyle**).

* Die Enumerationen **KnownStyleName**, **KnownSelectionMarkState** und **KnownKeyValueType** wurden entfernt.

* Der Typ **FormGenderField** wurde entfernt. Jeder erkannte Wert, der zuvor als _FormGenderField_ erstellt wurde, wird jetzt als FormStringField-Typ zurückgegeben, und der Wert bleibt unverändert.

* Der Typ **TextStyle** wurde entfernt.

#### <a name="breaking-changes-may"></a>**Breaking Changes (Mai)**

**Keine Breaking Changes**

### <a name="python"></a>[**Python**](#tab/python)

| [Referenzdokumentation](/java/api/com.azure.ai.formrecognizer.models?view=azure-java-stable&preserve-view=true)| [PyPi azure-ai-formrecognizer 3.1.0](https://pypi.org/project/azure-ai-formrecognizer/) |

#### <a name="non-breaking-changes"></a>**Geringfügige Änderungen**

* Die Methoden **to_dict** und **from_dict** wurden allen Modellen hinzugefügt.

#### <a name="breaking-changes-may"></a>**Breaking Changes (Mai)**

* **begin_recognize_identity_documents** und **begin_recognize_identity_documents_from_url**. Methoden und Methodenparameter mit Verwendung von **Identity** wurden umbenannt, um das Schlüsselwort _Id_ zu ersetzen.

* **FieldValueType**. Der Werttyp *country* wurde in **countryRegion** umbenannt.  Der Werttyp *gender* wurde entfernt.

* Das Modell **TextAppearance** umfasst jetzt die Eigenschaften **style_name** und **style_confidence** (vormals Namens- und Zuverlässigkeitseigenschaften im Objekt **TextStyle**).

* Das Modell **TextStyle** wurde entfernt.

---

## <a name="april-2021"></a>April 2021
<!-- markdownlint-disable MD029 -->

### <a name="sdk-preview-updates-for-api--version-21-preview3"></a>SDK-Vorschauupdates für API-Version 2.1-preview.3

### <a name="c"></a>[**C#**](#tab/csharp)

NuGet package version 3.1.0-beta.4

* **Neue Methoden zum Analysieren von Daten aus Identitätsdokumenten:**

   **StartRecognizeIdDocumentsFromUriAsync**

   **StartRecognizeIdDocumentsAsync**

   Eine Liste der Feldwerte finden _Sie_ in der Dokumentation zur Formularerkennung unter [Extrahierte Felder](./concept-id-document.md).

* Die Gruppe der Dokumentsprachen, die für die Methode **[StartRecognizeContent](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizecontent?view=azure-dotnet-preview&preserve-view=true)** angegeben werden können, wurde erweitert.

* **Neue Eigenschaft `Pages`, die von den folgenden Klassen unterstützt wird:**

   **[RecognizeBusinessCardsOptions](/dotnet/api/azure.ai.formrecognizer.recognizebusinesscardsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[RecognizeCustomFormsOptions](/dotnet/api/azure.ai.formrecognizer.recognizecustomformsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[RecognizeInvoicesOptions](/dotnet/api/azure.ai.formrecognizer.recognizeinvoicesoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[RecognizeReceiptsOptions](/dotnet/api/azure.ai.formrecognizer.recognizereceiptsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>

   Mit der Eigenschaft `Pages` können Sie bei PDF- und TIFF-Dokumenten mit mehreren Seiten einzelne Seiten oder einen Seitenbereich auswählen. Geben Sie für einzelne Seiten die Seitenzahl ein (beispielsweise `3`). Geben Sie für einen Seitenbereich (beispielsweise Seite 2 und Seiten 5 bis 7) die Seitennummern und Bereiche in einem kommagetrennten Format ein: `2, 5-7`.

* **Neue Eigenschaft `ReadingOrder`, die für die folgende Klasse unterstützt wird:**

   **[RecognizeContentOptions](/dotnet/api/azure.ai.formrecognizer.recognizecontentoptions?view=azure-dotnet-preview&preserve-view=true)**

  Die Eigenschaft `ReadingOrder` ist ein optionaler Parameter, mit dem Sie angeben können, welcher Algorithmus für die Lesereihenfolge (`basic` oder `natural`) angewendet werden soll, um die Extraktionsreihenfolge von Textelementen zu bestimmen. Wenn Sie hier nichts angeben, lautet der Standardwert `basic`.

#### <a name="breaking-changes-april"></a>Breaking Changes (April)

* Der Client verwendet standardmäßig die neueste unterstützte Dienstversion (aktuell **2.1-preview.3**).

* Von der Methode **[StartRecognizeCustomForms](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizecustomforms?view=azure-dotnet-preview&preserve-view=true#Azure_AI_FormRecognizer_FormRecognizerClient_StartRecognizeCustomForms_System_String_System_IO_Stream_Azure_AI_FormRecognizer_RecognizeCustomFormsOptions_System_Threading_CancellationToken_)** wird jetzt eine Ausnahme vom Typ `RequestFailedException()` ausgelöst, wenn eine ungültige Datei übergeben wird.

### <a name="java"></a>[**Java**](#tab/java)

Maven artifact package dependency version 3.1.0-beta.3

* **Neue Methoden zum Analysieren von Daten aus Identitätsdokumenten:**

  **[beginRecognizeIdDocumentsFromUrl]**

  **[beginRecognizeIdDocuments]**

   Eine Liste der Feldwerte finden _Sie_ in der Dokumentation zur Formularerkennung unter [Extrahierte Felder](./concept-id-document.md).

* **Unterstützung von Bitmapbilddateien (.bmp) für benutzerdefinierte Formulare und Trainingsmethoden in der Enumeration `FormContentType`:**

* `image/bmp`

* **Neue Eigenschaft `Pages`, die von den folgenden Klassen unterstützt wird:**

   **[RecognizeBusinessCardsOptions](/java/api/com.azure.ai.formrecognizer.models.recognizebusinesscardsoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[RecognizeCustomFormOptions](/java/api/com.azure.ai.formrecognizer.models.recognizecustomformsoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[RecognizeInvoicesOptions](/java/api/com.azure.ai.formrecognizer.models.recognizeinvoicesoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[RecognizeReceiptsOptions](/java/api/com.azure.ai.formrecognizer.models.recognizereceiptsoptions?view=azure-java-preview&preserve-view=true)**</br>

  Mit der Eigenschaft `Pages` können Sie bei PDF- und TIFF-Dokumenten mit mehreren Seiten einzelne Seiten oder einen Seitenbereich auswählen. Geben Sie für einzelne Seiten die Seitenzahl ein (beispielsweise `3`). Geben Sie für einen Seitenbereich (beispielsweise Seite 2 und Seiten 5 bis 7) die Seitennummern und Bereiche in einem kommagetrennten Format ein: `2, 5-7`.

* **Unterstützung von Bitmapbilddateien (.bmp) für benutzerdefinierte Formulare und Trainingsmethoden in den Feldern vom Typ [FormContentType](/java/api/com.azure.ai.formrecognizer.models.formcontenttype?view=azure-java-preview&preserve-view=true#fields):**

  `image/bmp`

* **Neues Schlüsselwortargument `ReadingOrder`, das für die folgenden Methoden unterstützt wird:**

* **[beginRecognizeContent](/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizecontent?preserve-view=true&view=azure-java-preview)**</br>
**[beginRecognizeContentFromUrl](/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizecontentfromurl?view=azure-java-preview&preserve-view=true)**</br>

   Das Schlüsselwortargument `ReadingOrder` ist ein optionaler Parameter, mit dem Sie angeben können, welcher Algorithmus für die Lesereihenfolge (`basic` oder `natural`) angewendet werden soll, um die Extraktionsreihenfolge von Textelementen zu bestimmen. Wenn Sie hier nichts angeben, lautet der Standardwert `basic`.

* Der Client verwendet standardmäßig die neueste unterstützte Dienstversion (aktuell **2.1-preview.3**).

### <a name="javascript"></a>[**JavaScript**](#tab/javascript)

npm package version 3.1.0-beta.3

* **Neue Methoden zum Analysieren von Daten aus Identitätsdokumenten:**

    **[beginRecognizeIdDocumentsFromUrl](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-preview&preserve-view=true&branch=main#beginRecognizeIdDocumentsFromUrl_string__BeginRecognizeIdDocumentsOptions_)**

    **[beginRecognizeIdDocuments](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-preview&preserve-view=true&branch=main#beginRecognizeIdDocuments_FormRecognizerRequestBody__BeginRecognizeIdDocumentsOptions_)**

    Eine Liste der Feldwerte finden _Sie_ in der Dokumentation zur Formularerkennung unter [Extrahierte Felder](./concept-id-document.md).

* **Neue Feldwerte für die FieldValue-Schnittstelle:**

    `gender`: Mögliche Werte sind `M`, `F` oder `X`.</br>
   `country`: Mögliche Werte sind dreistellige Ländercodezeichenfolgen gemäß [ISO Alpha-3](https://www.iso.org/obp/ui/#search).

* Neue Option `pages`, die von allen Formularerkennungsmethoden unterstützt wird (benutzerdefinierte Formulare und alle vordefinierten Modelle). Mit dem Argument können Sie bei PDF- und TIFF-Dokumenten mit mehreren Seiten einzelne Seiten oder einen Seitenbereich auswählen. Geben Sie für einzelne Seiten die Seitenzahl ein (beispielsweise `3`). Geben Sie für einen Seitenbereich (beispielsweise Seite 2 und Seiten 5 bis 7) die Seitennummern und Bereiche in einem kommagetrennten Format ein: `2, 5-7`.

* Den Inhaltserkennungsmethoden wurde die Unterstützung des Typs **[ReadingOrder](/javascript/api/@azure/ai-form-recognizer/formreadingorder?view=azure-node-latest&preserve-view=true to the URL)** hinzugefügt. Mithilfe dieser Option können Sie den Algorithmus steuern, mit dem der Dienst bestimmt, wie erkannte Textzeilen sortiert werden sollen. Sie können angeben, welcher Algorithmus für die Lesereihenfolge (`basic` oder `natural`) angewendet werden soll, um die Extraktionsreihenfolge von Textelementen zu bestimmen. Wenn Sie hier nichts angeben, lautet der Standardwert `basic`.

* Der Typ **[FormField](/javascript/api/@azure/ai-form-recognizer/formfield?view=azure-node-preview&preserve-view=true)** wurde auf mehrere verschiedene Schnittstellen aufgeteilt. Dieses Update sollte nur in bestimmten Grenzfällen (undefinierter Werttyp) zu API-Kompatibilitätsproblemen führen.

* Für alle REST-API-Aufrufe wurde zur Version **2.1-preview.3** des Formularerkennungsdiensts migriert.

### <a name="python"></a>[**Python**](#tab/python)

pip-Paketversion 3.1.0b4

* **Neue Methoden zum Analysieren von Daten aus Identitätsdokumenten:**

  **[begin_recognize_id_documents_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python&preserve-view=true)**

  **[begin_recognize_id_documents](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python&preserve-view=true)**

  Eine Liste der Feldwerte finden _Sie_ in der Dokumentation zur Formularerkennung unter [Extrahierte Felder](./concept-id-document.md).

* **Neue Feldwerte für die Enumeration [FieldValueType](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.fieldvaluetype?view=azure-python-preview&preserve-view=true):**

   gender: Mögliche Werte sind `M`, `F` oder `X`.

  country: Mögliche Werte sind [Ländercodes gemäß ISO Alpha-3](https://www.iso.org/obp/ui/#search).

* **Unterstützung von Bitmapbilddateien (.bmp) für benutzerdefinierte Formulare und Trainingsmethoden in der Enumeration [FormContentType](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formcontenttype?view=azure-python-preview&preserve-view=true):**

    image/bmp

* **Neues Schlüsselwortargument `pages`, das von folgenden Methoden unterstützt wird:**

    **[begin_recognize_receipts](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true&branch=main#begin-recognize-receipts-receipt----kwargs-)**

    **[begin_recognize_receipts_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-receipts-from-url-receipt-url----kwargs-)**

   **[begin_recognize_business_cards](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-business-cards-business-card----kwargs-)**

   **[begin_recognize_business_cards_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-business-cards-from-url-business-card-url----kwargs-)**

   **[begin_recognize_invoices](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-invoices-invoice----kwargs-)**

   **[begin_recognize_invoices_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-invoices-from-url-invoice-url----kwargs-)**

   **[begin_recognize_content](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-form----kwargs-)**

  **[begin_recognize_content_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-from-url-form-url----kwargs-)**

   Mit dem Schlüsselwortargument `pages` können Sie bei PDF- und TIFF-Dokumenten mit mehreren Seiten einzelne Seiten oder einen Seitenbereich auswählen. Geben Sie für einzelne Seiten die Seitenzahl ein (beispielsweise `3`). Geben Sie für einen Seitenbereich (beispielsweise Seite 2 und Seiten 5 bis 7) die Seitennummern und Bereiche in einem kommagetrennten Format ein: `2, 5-7`.

* **Neues Schlüsselwortargument `readingOrder`, das für die folgenden Methoden unterstützt wird:**

   **[begin_recognize_content](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-form----kwargs-)**

   **[begin_recognize_content_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-from-url-form-url----kwargs-)**

   Das Schlüsselwortargument `readingOrder` ist ein optionaler Parameter, mit dem Sie angeben können, welcher Algorithmus für die Lesereihenfolge (`basic` oder `natural`) angewendet werden soll, um die Extraktionsreihenfolge von Textelementen zu bestimmen. Wenn Sie hier nichts angeben, lautet der Standardwert `basic`.

---

## <a name="march-2021"></a>März 2021

**Die öffentliche Vorschauversion 3 der Formularerkennung v2.1 ist jetzt verfügbar.** v2.1-preview.3 wurde veröffentlicht und enthält die folgenden Funktionen:

* **Neues vordefiniertes ID-Modell:** Mit dem neuen vordefinierten ID-Modell können Kunden Ausweispapiere erfassen und strukturierte Daten zurückgeben, um die Verarbeitung zu automatisieren. Dabei werden unsere leistungsstarken Funktionen der optischen Zeichenerkennung (Optical Character Recognition, OCR) mit ID-Erkennungsmodellen kombiniert, mit denen wesentliche Informationen aus Reisepässen und US-Führerscheinen extrahiert werden, z. B. Name, Geburtsdatum, Ausstellungsdatum, Ablaufdatum usw.

  [Weitere Informationen zum vordefinierten ID-Modell](./concept-id-document.md)

   :::image type="content" source="./media/id-canada-passport-example.png" alt-text="Beispiel eines Reisepasses" lightbox="./media/id-canada-passport-example.png":::

* **Extraktion von Einzelposten für das Rechnungsmodell** - Das vorgefertigte Rechnungsmodell unterstützt jetzt die Extraktion von Einzelposten; es extrahiert jetzt vollständige Artikel und ihre Bestandteile - Beschreibung, Betrag, Menge, Produkt-ID, Datum und mehr. Mit einem einfachen API-/SDK-Aufruf können Sie nützliche Daten aus Rechnungen extrahieren, z. B. Text, Tabellen, Schlüssel-Wert-Paare und Einzelposten.

   [Erfahren Sie mehr über das Rechnungsmodell](./concept-invoice.md)

* **Überwachtes Beschriften und Trainieren von Tabellen, Beschriftung leerer Werte:** Zusätzlich zu den [hochmodernen Deep Learning-Funktionen der Formularerkennung zur automatischen Tabellenextraktion](https://techcommunity.microsoft.com/t5/azure-ai/enhanced-table-extraction-from-documents-with-form-recognizer/ba-p/2058011) können Kunden nun auch Tabellen beschriften und trainieren. Dieses neue Release bietet die Möglichkeit, Einzelposten und Tabellen (dynamisch und fest) zu beschriften und zu trainieren sowie ein benutzerdefiniertes Modell zum Extrahieren von Schlüssel-Wert-Paaren und Einzelposten zu trainieren. Nach dem Trainieren eines Modells werden mit dem Modell Einzelposten als Teil der JSON-Ausgabe im Abschnitt „documentResults“ extrahiert.

    :::image type="content" source="./media/table-labeling.png" alt-text="Tabellenbeschriftung" lightbox="./media/table-labeling.png":::

    Neben Tabellen können nun auch leere Werte beschriftet werden. Wenn Dokumente im Trainingssatz keine Werte für bestimmte Felder enthalten, können Sie sie beschriften, damit Werte ordnungsgemäß aus analysierten Dokumenten extrahiert werden können.

* **Unterstützung für 66 neue Sprachen**: Die Layout-API und benutzerdefinierten Modelle für die Formularerkennung unterstützen jetzt 73 Sprachen.

  [Weitere Informationen zur Sprachunterstützung für die Formularerkennung](language-support.md)

* **Natürliche Leserichtung, Handschriftklassifizierung und Seitenauswahl:** Mit diesem Update können Sie festlegen, dass Textzeilen in der natürlichen Leserichtung ausgegeben werden anstatt in der standardmäßigen Leserichtung von links nach rechts und von oben nach unten. Dazu verwenden Sie den neuen Abfrageparameter „readingOrder“ und legen ihn für eine benutzerfreundlichere Ausgabe der Leserichtung auf den Wert „natural“ fest. Für lateinische Sprachen klassifiziert die Formularerkennung außerdem Textzeilen als handschriftlich oder nicht handschriftlich und gibt eine Konfidenzbewertung an.

* **Qualitätsverbesserungen beim vordefinierten Belegmodell:** Dieses Update enthält zahlreiche Qualitätsverbesserungen für das vordefinierte Belegmodell, insbesondere rund um die Extraktion von Einzelposten.

## <a name="november-2020"></a>November 2020

### <a name="new-features"></a>Neue Funktionen

**Die öffentliche Vorschauversion 2 der Formularerkennung v2.1 ist jetzt verfügbar.** v2.1-preview.2 wurde veröffentlicht und enthält die folgenden Funktionen:

* **Neues vordefiniertes Rechnungsmodell:** Mit dem neuen vordefinierten Rechnungsmodell können Kunden Rechnungen in verschiedenen Formaten erstellen und strukturierte Daten zurückgeben, um die Rechnungsverarbeitung zu automatisieren. Es kombiniert unsere leistungsstarken Funktionen zur optischen Zeichenerkennung (Optical Character Recognition, OCR) mit Deep Learning-Modellen zum Rechnungsverständnis, um wichtige Informationen aus Rechnungen in englischer Sprache zu extrahieren. Es extrahiert wichtige Angaben, Tabellen und Informationen wie Kunde, Anbieter, Rechnungs-ID, Fälligkeitsdatum für die Rechnung, Summe, fälliger Betrag, Steuerbetrag, Lieferadresse und Rechnungsadresse.

  > [Weitere Informationen zum vordefinierten Rechnungsmodell](./concept-invoice.md)

  :::image type="content" source="./media/invoice-example.jpg" alt-text="Beispiel für Rechnung" lightbox="./media/invoice-example.jpg":::

* **Erweiterte Tabellenextraktion** – Die Formularerkennung bietet jetzt eine verbesserte Tabellenextraktion, die unsere leistungsstarken OCR-Funktionen (Optical Character Recognition, Optische Zeichenerkennung) mit einem Deep Learning-Modell für Tabellenextraktion kombiniert. Die Formularerkennung kann Daten aus Tabellen extrahieren, einschließlich komplexer Tabellen mit zusammengeführten Spalten, Zeilen, ohne Rahmen und mehr.

  :::image type="content" source="./media/tables-example.jpg" alt-text="Beispiel für Tabellen" lightbox="./media/tables-example.jpg":::

  > [Weitere Informationen zur Layoutextraktion](concept-layout.md)

* **Update der Clientbibliothek:** Die aktuelle Version der [Clientbibliotheken](./quickstarts/try-sdk-rest-api.md) für .NET, Python, Java und JavaScript unterstützt die Formularerkennung 2.1-API.
* **Neue unterstützte Sprache: Japanisch** – Die folgenden neuen Sprachen werden jetzt unterstützt: für `AnalyzeLayout` und `AnalyzeCustomForm`: Japanisch (`ja`). [Sprachunterstützung](language-support.md)
* **Textzeilenstilanzeige (handschriftlich/anders) (nur lateinische Sprachen)**  – Die Formularerkennung gibt jetzt ein `appearance`-Objekt aus, das – zusammen mit einer Zuverlässigkeitsbewertung – klassifiziert, ob jede Textzeile von Hand geschrieben wurde oder nicht. Dieses Feature wird nur für lateinische Sprachen unterstützt.
* **Qualitätsverbesserungen** – Extraktionsverbesserungen, einschließlich Verbesserungen der einstelligen Extraktion.
* **Neues Feature „try-it-out“ im Tool für die Beschriftung von Beispielen für die Formularerkennung** – Möglichkeit zum Ausprobieren von vordefinierten Modellen für Rechnungen, Belege und Visitenkarten sowie der Layout-API mithilfe dieses Tools. Sehen Sie sich an, wie Ihre Daten extrahiert werden, ohne Code schreiben zu müssen.

  [**Probieren Sie das Formularerkennungstool für die Bezeichnung von Beispielen aus**](https://fott-2-1.azurewebsites.net)

  ![Screenshot: Tool für die Bezeichnung von Beispielen.](./media/ui-preview.jpg)

* **Feedbackschleife** – Wenn Sie Dateien über das Tool für die Bezeichnung von Beispielen analysieren, können Sie es jetzt auch dem Trainingssatz hinzufügen und die Bezeichnungen bei Bedarf anpassen und trainieren, um das Modell zu verbessern.
* **Automatisches Bezeichnen von Dokumenten:** Bezeichnet zusätzliche Dokumente automatisch basierend auf zuvor bezeichneten Dokumenten im Projekt

## <a name="august-2020"></a>August 2020

### <a name="new-features"></a>Neue Funktionen

**Die öffentliche Vorschau der Formularerkennung v2.1 ist jetzt verfügbar.** V2.1-preview.1 wurde veröffentlicht, einschließlich der folgenden Features:

* **REST-API-Referenz ist verfügbar**: [v2.1-preview.1-Referenz](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync) anzeigen
* **Zusätzlich zu Englisch werden neue Sprachen unterstützt.** Die folgenden [Sprachen](language-support.md) werden jetzt unterstützt: für `Layout` und `Train Custom Model`: Englisch (`en`), Chinesisch (vereinfacht) (`zh-Hans`), Niederländisch (`nl`), Französisch (`fr`), Deutsch (`de`), Italienisch (`it`), Portugiesisch (`pt`) und Spanisch (`es`).
* **Kontrollkästchen-/Auswahlmarkierungserkennung**: Die Formularerkennung unterstützt das Erkennen und Extrahieren von Auswahlmarkierungen, z. B. von Kontrollkästchen und Optionsfeldern. Auswahlmarkierungen werden in `Layout` extrahiert, und Sie können jetzt auch `Train Custom Model` - _mit Bezeichnungen trainieren_, um Schlüssel-Wert-Paare für Auswahlmarkierungen zu extrahieren.
* **Model Compose** – Ermöglicht das Zusammensetzen und Aufrufen mehrerer Modelle mit einer einzigen Modell-ID. Wenn Sie ein Dokument zur Analyse mit einer zusammengesetzten Modell-ID übermitteln, wird zunächst ein Klassifizierungsschritt durchgeführt, um es an das richtige benutzerdefinierte Modell weiterzuleiten. Model Compose steht für das `Train Custom Model` - _Trainieren mit Bezeichnungen_ zur Verfügung.
* **Modellname** – Fügen Sie Ihren benutzerdefinierten Modellen einen Anzeigenamen zur einfacheren Verwaltung und Nachverfolgung hinzu.
* **[Neues vordefiniertes Modell für Visitenkarten](./concept-business-card.md)** zum Extrahieren allgemeiner Felder auf Visitenkarten in englischer Sprache.
* **[Neue Gebietsschemas für vordefinierte Belege](./concept-receipt.md)** : Neben en-US ist jetzt zusätzliche Unterstützung für en-AU, en-CA, en-GB und en-IN verfügbar.
* **Qualitätsverbesserungen** für `Layout`, `Train Custom Model` - _Trainieren ohne Bezeichnungen_ und _Trainieren mit Bezeichnungen_.

**v2.0** enthält das folgende Update:

* Die [Clientbibliotheken](./quickstarts/try-sdk-rest-api.md) für .NET, Python, Java und JavaScript sind nun allgemein verfügbar.

**Neue Beispiele** sind auf GitHub verfügbar.

* Unter [Knowledge Extraction Recipes - Forms Playbook](https://github.com/microsoft/knowledge-extraction-recipes-forms) werden bewährte Methoden aus echten Kundenengagements mit der Formularerkennung gesammelt und nützliche Codebeispiele, Checklisten und Beispielpipelines für die Entwicklung dieser Projekte bereitgestellt.
* Das [Tool für die Bezeichnung von Beispielen](https://github.com/microsoft/OCR-Form-Tools) wurde aktualisiert, um die neue Funktionalität von v2.1 zu unterstützen. Informationen zu den ersten Schritten mit dem Tool finden Sie in diesem [Schnellstart](label-tool.md).
* Im Formularerkennungsbeispiel [Intelligent Kiosk](https://github.com/microsoft/Cognitive-Samples-IntelligentKiosk/blob/master/Documentation/FormRecognizer.md) wird gezeigt, wie `Analyze Receipt` und `Train Custom Model` - _Trainieren ohne Bezeichnungen_ integriert werden.

## <a name="july-2020"></a>Juli 2020

### <a name="new-features"></a>Neue Funktionen
<!-- markdownlint-disable MD004 -->
* **v2.0-Referenz verfügbar** – Mehr dazu finden Sie in der [v2.0 API-Referenz](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) sowie in den aktualisierten SDKs für [.NET](/dotnet/api/overview/azure/ai.formrecognizer-readme), [Python](/python/api/overview/azure/), [Java](/java/api/overview/azure/ai-formrecognizer-readme) und [JavaScript](/javascript/api/overview/azure/).
* **Tabellenerweiterungen und Extraktionserweiterungen** – Umfasst Verbesserungen der Genauigkeit und der Tabellenextraktion, insbesondere eine Funktion zum Erlernen von Tabellenheadern und -strukturen beim _benutzerdefinierten Trainieren ohne Bezeichnungen_.

* **Währungsunterstützung** – Erkennung und Extraktion von globalen Währungssymbolen.
* **Azure Gov** – Die Formularerkennung steht jetzt auch in Azure Gov zur Verfügung.
* **Erweiterte Sicherheitsfeatures**:
  * **Bring Your Own Key (BYOK)**  – Die Formularerkennung verschlüsselt Ihre Daten automatisch, wenn sie in der Cloud persistent gespeichert werden, um sie zu schützen und Sie bei der Einhaltung der Sicherheits- und Complianceverpflichtungen Ihrer Organisation zu unterstützen. Standardmäßig verwendet Ihr Abonnement von Microsoft verwaltete Verschlüsselungsschlüssel. Sie können Ihr Abonnement jetzt auch mit eigenen Verschlüsselungsschlüsseln verwalten. [Kundenseitig verwaltete Schlüssel, auch bezeichnet als „Bring Your Own Key“ (BYOK),](./encrypt-data-at-rest.md) bieten eine größere Flexibilität beim Erstellen, Rotieren, Deaktivieren und Widerrufen von Zugriffssteuerungen. Außerdem können Sie die zum Schutz Ihrer Daten verwendeten Verschlüsselungsschlüssel überwachen.
  * **Private Endpunkte:** Ermöglichen in einem virtuellen Netzwerk den [sicheren Zugriff auf Daten über eine private Verbindung](../../private-link/private-link-overview.md).

## <a name="june-2020"></a>Juni 2020

### <a name="new-features"></a>Neue Funktionen

* **CopyModel-API wurde zu Client-SDKs hinzugefügt** – Sie können jetzt Modelle mithilfe der Client-SDKs aus einem Abonnement in ein anderes kopieren. Allgemeine Informationen zu diesem Feature finden Sie unter [Sichern und Wiederherstellen von Modellen](./disaster-recovery.md).
* **Azure Active Directory-Integration** – Sie können jetzt Clientobjekte der Formularerkennung mithilfe Ihrer Azure AD-Anmeldeinformationen in den SDKs authentifizieren.
* **SDK-spezifische Änderungen** – Hierzu zählen sowohl geringfügige Featureergänzungen als auch Breaking Changes. Weitere Informationen finden Sie in den SDK-Änderungsprotokollen.
  * [Änderungsprotokoll zu C# SDK Preview 3](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/formrecognizer/Azure.AI.FormRecognizer/CHANGELOG.md)
  * [Änderungsprotokoll zu Python SDK Preview 3](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Änderungsprotokoll zu Java SDK Preview 3](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Änderungsprotokoll zu JavaScript SDK Preview 3](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_1.0.0-preview.3/sdk/formrecognizer/ai-form-recognizer/CHANGELOG.md)

## <a name="april-2020"></a>April 2020

### <a name="new-features"></a>Neue Funktionen

* **SDK-Unterstützung für Version 2.0 der Formularerkennungs-API (Public Preview):**  – In diesem Monat haben wir unsere Dienstunterstützung um ein Vorschau-SDK für Version 2.0 der Formularerkennung (Vorschauversion) erweitert. Verwenden Sie die folgenden Links, um die ersten Schritte mit Ihrer bevorzugten Sprache auszuführen:
  * [.NET SDK](/dotnet/api/overview/azure/ai.formrecognizer-readme)
  * [Java SDK](/java/api/overview/azure/ai-formrecognizer-readme)
  * [Python SDK](/python/api/overview/azure/ai-formrecognizer-readme)
  * [JavaScript SDK](/javascript/api/overview/azure/ai-form-recognizer-readme)

  Das neue SDK unterstützt alle Features von Version 2.0 der REST-API für die Formularerkennung. Sie können beispielsweise ein Modell mit oder ohne Bezeichnungen trainieren und Text, Schlüssel-Wert-Paare und Tabellen aus Ihren Formularen extrahieren, mit dem vorgefertigten Belegdienst Daten aus Belegen extrahieren sowie mit dem Layoutdienst Text und Tabellen aus Ihren Dokumenten extrahieren. Sie können Ihr Feedback zu den SDKs über das [SDK-Feedbackformular](https://aka.ms/FR_SDK_v1_feedback) teilen.

* **Kopieren eines benutzerdefinierten Modells:** Mithilfe der neuen Funktion zum Kopieren benutzerdefinierter Modelle können Sie nun Modelle zwischen Regionen und Abonnements kopieren. Vor dem Aufrufen der API zum Kopieren eines benutzerdefinierten Modells müssen Sie zunächst die Autorisierung zum Kopieren in die Zielressource erhalten, indem Sie den Vorgang für die Kopierautorisierung für den Zielressourcenendpunkt aufrufen.

  * REST-API zum [Generieren einer Kopierautorisierung](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModelAuthorization)
  * REST-API zum [Kopieren eines benutzerdefinierten Modells](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModel)

### <a name="security-improvements"></a>Verbesserungen der Sicherheit

* Kundenseitig verwaltete Schlüssel stehen jetzt für die Formularerkennung zur Verfügung. Weitere Informationen finden Sie unter [Verschlüsselung für ruhende Daten der Formularerkennung](./encrypt-data-at-rest.md).
* Verwenden Sie verwaltete Identitäten für den Zugriff auf Azure-Ressourcen mit Azure Active Directory. Weitere Informationen finden Sie unter [Autorisieren des Zugriffs auf verwaltete Identitäten](../../cognitive-services/authentication.md#authorize-access-to-managed-identities).

## <a name="march-2020"></a>März 2020

### <a name="new-features"></a>Neue Funktionen

* **Werttypen für die Bezeichnung** Sie können nun die Typen der Werte angeben, die Sie mit dem Tool Formularerkennung zur Bezeichnung von Beispielen beschriften können. Derzeit werden die folgenden Werttypen und Variationen unterstützt:
  * `string`
    * Standardwert, `no-whitespaces`, `alphanumeric`
  * `number`
    * Standardwert, `currency`
  * `date`
    * Standardwert, `dmy`, `mdy`, `ymd`
  * `time`
  * `integer`

  Weitere Informationen zur Verwendung dieses Features finden Sie in der Anleitung zum [Tool zum Bezeichnen von Beispielen](label-tool.md#specify-tag-value-types).

* **Visualisierung von Tabellen** Im Tool für die Bezeichnung von Beispielen werden nun im Dokument erkannte Tabellen angezeigt. Mithilfe dieses Features können Sie die im Dokument erkannten und aus dem Dokument extrahierten Tabellen anzeigen, bevor Sie die Beschriftung und Analyse durchführen. Dieses Feature kann mithilfe der Ebenenoption aktiviert bzw. deaktiviert werden.

  Die folgende Abbildung ist ein Beispiel für die Erkennung und Extraktion von Tabellen:

  > [!div class="mx-imgBorder"]
  > ![Tabellenvisualisierung mit dem Tool für die Bezeichnung von Beispielen](./media/whats-new/table-viz.png)

    Die extrahierten Tabellen stehen in der JSON-Ausgabe unter `"pageResults"` zur Verfügung.

  > [!IMPORTANT]
  > Das Beschriften von Tabellen wird nicht unterstützt. Nicht automatisch erkannte und extrahierte Tabellen können nur als Schlüssel-Wert-Paare beschriftet werden. Beim Beschriften von Tabellen als Schlüssel-Wert-Paare muss jede Zelle als eindeutiger Wert beschriftet werden.

### <a name="extraction-enhancements"></a>Extraktionsverbesserungen

Dieses Release bietet Verbesserungen bei der Extraktion sowie eine verbesserte Genauigkeit. Hierzu zählt insbesondere die Möglichkeit, mehrere Schlüssel-Wert-Paare in der gleichen Textzeile zu beschriften und zu extrahieren.

### <a name="sample-labeling-tool-is-now-open-source"></a>Tool für die Beschriftung von Beispielen jetzt als Open-Source-Projekt verfügbar

Das Tool Formularerkennung für die Bezeichnung von Beispielen ist jetzt als Open-Source-Projekt verfügbar. Sie können es in Ihre Lösungen integrieren und kundenspezifische Änderungen vornehmen, um es an Ihre individuellen Anforderungen anzupassen.

Weitere Informationen zum Tool Formularerkennung für die Bezeichnung von Beispielen finden Sie in der Dokumentation auf [GitHub](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

### <a name="tls-12-enforcement"></a>Erzwingen von TLS 1.2

TLS 1.2 wird nun für alle HTTP-Anforderungen erzwungen, die an diesen Dienst gerichtet werden. Weitere Informationen finden Sie unter [Sicherheit von Azure Cognitive Services](../../cognitive-services/cognitive-services-security.md).

## <a name="january-2020"></a>Januar 2020

In diesem Release wird die Formularerkennung 2.0 (Vorschauversion) eingeführt. In den folgenden Abschnitten finden Sie weitere Informationen zu neuen Features, Verbesserungen und Änderungen.

### <a name="new-features"></a>Neue Funktionen

* **Benutzerdefiniertes Modell**
  * **Trainieren mit Bezeichnungen** Sie können jetzt ein benutzerdefiniertes Modell mit manuell gekennzeichneten Daten trainieren. Diese Methode führt zu Modellen mit besserer Leistung und kann Modelle hervorbringen, die mit komplexen Formularen oder Formularen arbeiten, die Werte ohne Schlüssel enthalten.
  * **Asynchrone API** Sie können asynchrone API-Aufrufe verwenden, um mit großen Datasets und Dateien zu trainieren und diese zu analysieren.
  * **Unterstützung von TIFF-Dateien** Sie können jetzt mit TIFF-Dokumenten trainieren und Daten aus ihnen extrahieren.
  * **Verbesserte Extrahierungsgenauigkeit**

* **Vordefiniertes Belegmodell**
  * **Trinkgeldbeträge** Sie können jetzt Trinkgeldbeträge und andere handschriftliche Werte extrahieren.
  * **Extraktion von Zeilenelementen** Sie können Werte von Zeilenelementen aus Belegen extrahieren.
  * **Zuverlässigkeitswerte** Sie können die Zuverlässigkeit des Modells für jeden extrahierten Wert anzeigen.
  * **Verbesserte Extrahierungsgenauigkeit**

* **Layoutextraktion** Sie können nun mit der Layout-API Textdaten und Tabellendaten aus Ihren Formularen extrahieren.

### <a name="custom-model-api-changes"></a>Änderungen an der benutzerdefinierten Modell-API

Alle APIs für das Trainieren und Verwenden benutzerdefinierter Modelle wurden umbenannt, und einige synchrone Methoden sind jetzt asynchron. Dies sind die wichtigsten Änderungen:

* Der Prozess zum Trainieren eines Modells ist jetzt asynchron. Sie initiieren das Training über den API-Aufruf **/custom/models**. Dieser Aufruf gibt eine Vorgangs-ID zurück, die Sie in **custom/models/{modelID}** übergeben können, um die Trainingsergebnisse zurückzugeben.
* Die Schlüssel-Wert-Extraktion wird jetzt vom API-Aufruf **/custom/models/{modelID}/analyze** initiiert. Dieser Aufruf gibt eine Vorgangs-ID zurück, die Sie in **custom/models/{modelID}/analyzeResults/{resultID}** übergeben können, um die Extraktionsergebnisse zurückzugeben.
* Vorgangs-IDs für das Trainieren finden sich jetzt im Header **Location** von HTTP-Antworten, nicht mehr im Header **Operation-Location**.

### <a name="receipt-api-changes"></a>Änderungen an der Beleg-API

Die APIs zum Lesen von Belegen wurden umbenannt.

* Die Extraktion von Belegdaten wird jetzt vom API-Aufruf **/prebuilt/receipt/analyze** initiiert. Dieser Aufruf gibt eine Vorgangs-ID zurück, die Sie in **/prebuilt/receipt/analyzeResults/{resultID}** übergeben können, um die Extraktionsergebnisse zurückzugeben.

### <a name="output-format-changes"></a>Änderungen am Ausgabeformat

Die JSON-Antworten für alle API-Aufrufe haben neue Formate. Es wurden einige Schlüssel und Werte hinzugefügt, entfernt oder umbenannt. Beispiele für die aktuellen JSON-Formate finden Sie in den Schnellstarts.

## <a name="next-steps"></a>Nächste Schritte

Durchlaufen Sie einen [Schnellstart](./quickstarts/try-sdk-rest-api.md) zu den ersten Schritten zum Schreiben einer Formularverarbeitungs-App mit der Formularerkennung in der Entwicklungssprache Ihrer Wahl.

## <a name="see-also"></a>Weitere Informationen

* [Was ist die Formularerkennung?](./overview.md)
