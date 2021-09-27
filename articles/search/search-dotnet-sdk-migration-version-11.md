---
title: Upgrade auf .NET SDK-Version 11
titleSuffix: Azure Cognitive Search
description: Migrieren Sie Code von älteren Versionen zum Azure Cognitive Search .NET SDK-Version 11.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/16/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 37c03a5be2f81fff0f4a1cac01153601a39ddd10
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128557151"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-11"></a>Upgrade auf Version 11 des Azure Cognitive Search .NET SDK

Wenn Ihre Suchlösung auf dem [**Microsoft Azure SDK für .NET**](/dotnet/azure/) basiert, hilft Ihnen dieser Artikel bei der Migration Ihres Codes von früheren Versionen von [**Microsoft.Azure.Search**](/dotnet/api/overview/azure/search/client10) zu Version 11, der neuen [**Azure.Search.Documents**](/dotnet/api/overview/azure/search.documents-readme)-Clientbibliothek. Version 11 ist eine vollständig neu gestaltete Clientbibliothek, die vom Azure SDK-Entwicklungsteam freigegeben wurde (frühere Versionen wurden vom Azure Cognitive Search-Entwicklungsteam erstellt). 

Mit [einer Ausnahme](#WhatsNew) werden alle Features von Version 10 in Version 11 implementiert. Zu den wichtigsten Unterschieden gehören:

+ Ein Paket (**Azure.Search.Documents**) anstelle von vier Paketen
+ Drei Clients anstelle von zwei: SearchClient, SearchIndexClient, SearchIndexerClient
+ Benennungsunterschiede bei verschiedenen APIs und kleine strukturelle Unterschiede, die einige Aufgaben vereinfachen

Das [Änderungsprotokoll](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) der Clientbibliothek umfasst eine detaillierte Liste mit Updates.

Alle C#-Codebeispiele und -Codeausschnitte in der Cognitive Search-Produktdokumentation wurden überarbeitet, um die neue **Azure.Search.Documents**-Clientbibliothek zu verwenden.

## <a name="why-upgrade"></a>Gründe für ein Upgrade

Die Vorteile des Upgrades werden wie folgt zusammengefasst:

+ Neue Features werden nur zu **Azure.Search.Documents** hinzugefügt. Die vorherige Version (Microsoft.Azure.Search) ist jetzt ein Legacyclient. Updates für Legacybibliotheken sind nur auf Fehlerbehebungen mit hoher Priorität beschränkt.

+ Konsistenz mit anderen Azure-Clientbibliotheken. **Azure.Search.Documents** ist abhängig von [Azure.Core](/dotnet/api/azure.core) und [System.Text.Json](/dotnet/api/system.text.json) und folgt herkömmlichen Ansätzen für allgemeine Aufgaben wie Clientverbindungen und die Autorisierung.

## <a name="package-comparison"></a>Paketvergleich

Version 11 konsolidiert und vereinfacht die Paketverwaltung, sodass weniger verwaltet werden muss.

| Version 10 und früher | Version 11 |
|------------------------|------------|
| [Microsoft.Azure.Search](https://www.nuget.org/packages/Microsoft.Azure.Search/) </br>[Microsoft.Azure.Search.Service](https://www.nuget.org/packages/Microsoft.Azure.Search.Service/) </br>[Microsoft.Azure.Search.Data](https://www.nuget.org/packages/Microsoft.Azure.Search.Data/) </br>[Microsoft.Azure.Search.Common](https://www.nuget.org/packages/Microsoft.Azure.Search.Common/)  | [Azure.Search.Documents-Paket](https://www.nuget.org/packages/Azure.Search.Documents/) |

## <a name="client-comparison"></a>Clientvergleich

In der folgenden Tabelle werden die Clientbibliotheken soweit möglich einer der beiden Versionen zugeordnet.

|  Clientvorgänge | Microsoft.Azure.Search&nbsp;(v10) | Azure.Search.Documents&nbsp;(v11) |
|---------------------|------------------------------|------------------------------|
| Zielt auf die Dokumentensammlung eines Indexes ab (Abfragen und Datenimport) | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) | [SearchClient](/dotnet/api/azure.search.documents.searchclient) |
| Zielt auf indexbezogene Objekte (Indizes, Analysetools, Synonymzuordnungen) ab. | [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) |
| Zielt auf indexerbezogene Objekte (Indexer, Datenquellen, Skillsets) ab. | [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexerClient (**neu**)](/dotnet/api/azure.search.documents.indexes.searchindexerclient) |

> [!Caution]
> Beachten Sie, dass SearchIndexClient in beiden Versionen vorhanden ist, aber unterschiedliche Vorgänge als Ziel verwendet. In Version 10 erstellt SearchIndexClient Indizes und andere Objekte. In Version 11 arbeitet SearchIndexClient mit vorhandenen Indizes, die auf die Dokumentensammlung mit Abfrage- und Datenerfassungs-APIs abzielen. Achten Sie auf die Reihenfolge, in der die Clientverweise aktualisiert werden, damit keine Unklarheiten entstehen. Befolgen Sie die Reihenfolge in den [Schritten zum Aktualisieren](#UpgradeSteps), um Probleme beim Ersetzen von Zeichenfolgen zu vermeiden.

<a name="naming-differences"></a>

## <a name="naming-and-other-api-differences"></a>Benennung und andere API-Unterschiede

Abgesehen von den Clientunterschieden (die bereits erwähnt und daher hier weggelassen werden), wurden mehrere andere APIs umbenannt und in einigen Fällen überarbeitet. Die Unterschiede bei den Klassennamen sind unten zusammengefasst. Diese Liste ist nicht vollständig, aber darin werden API-Änderungen nach Aufgaben gruppiert, was für Revisionen bei bestimmten Codeblöcken hilfreich sein kann. Eine detaillierte Liste der API-Updates finden Sie im [Änderungsprotokoll](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) für `Azure.Search.Documents` auf GitHub.

### <a name="authentication-and-encryption"></a>Authentifizierung und Verschlüsselung

| Version 10 | Entsprechung in Version 11 |
|------------|-----------------------|
| [SearchCredentials](/dotnet/api/microsoft.azure.search.searchcredentials) | [AzureKeyCredential](/dotnet/api/azure.azurekeycredential) |
| EncryptionKey (In API-Referenz nicht dokumentiert. Die Unterstützung für diese API wurde in v10 allgemein verfügbar, war aber nur im [Vorschau-SDK](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) verfügbar.) | [SearchResourceEncryptionKey](/dotnet/api/azure.search.documents.indexes.models.searchresourceencryptionkey) |

### <a name="indexes-analyzers-synonym-maps"></a>Indizes, Analysetools, Synonymzuordnungen

| Version 10 | Entsprechung in Version 11 |
|------------|-----------------------|
| [Index](/dotnet/api/microsoft.azure.documents.index) | [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) |
| [Feld](/dotnet/api/microsoft.azure.search.models.field) | [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) |
| [DataType](/dotnet/api/microsoft.azure.search.models.datatype) | [SearchFieldDataType](/dotnet/api/azure.search.documents.indexes.models.searchfielddatatype) |
| [ItemError](/dotnet/api/microsoft.azure.search.models.itemerror) | [SearchIndexerError](/dotnet/api/azure.search.documents.indexes.models.searchindexererror) |
| [Analyzer](/dotnet/api/microsoft.azure.search.models.analyzer) | [LexicalAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzer) (auch `AnalyzerName` in `LexicalAnalyzerName`) |
| [AnalyzeRequest](/dotnet/api/microsoft.azure.search.models.analyzerequest) | [AnalyzeTextOptions](/dotnet/api/azure.search.documents.indexes.models.analyzetextoptions) |
| [StandardAnalyzer](/dotnet/api/microsoft.azure.search.models.standardanalyzer) | [LuceneStandardAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lucenestandardanalyzer) |
| [StandardTokenizer](/dotnet/api/microsoft.azure.search.models.standardtokenizer) | [LuceneStandardTokenizer](/dotnet/api/azure.search.documents.indexes.models.lucenestandardtokenizer) (auch `StandardTokenizerV2` in `LuceneStandardTokenizerV2`) |
| [TokenInfo](/dotnet/api/microsoft.azure.search.models.tokeninfo) | [AnalyzedTokenInfo](/dotnet/api/azure.search.documents.indexes.models.analyzedtokeninfo) |
| [Tokenizer](/dotnet/api/microsoft.azure.search.models.tokenizer) | [LexicalTokenizer](/dotnet/api/azure.search.documents.indexes.models.lexicaltokenizer) (auch `TokenizerName` in `LexicalTokenizerName`) |
| [SynonymMap.Format](/dotnet/api/microsoft.azure.search.models.synonymmap.format) | Keine. Verweise auf `Format` entfernen. |

Felddefinitionen sind optimiert: [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield), [SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield), [ComplexField](/dotnet/api/azure.search.documents.indexes.models.complexfield) sind neue APIs zur Erstellung von Felddefinitionen.

### <a name="indexers-datasources-skillsets"></a>Indexer, Datenquellen, Skillsets

| Version 10 | Entsprechung in Version 11 |
|------------|-----------------------|
| [Indexer](/dotnet/api/microsoft.azure.search.models.indexer) | [SearchIndexer](/dotnet/api/azure.search.documents.indexes.models.searchindexer) |
| [DataSource](/dotnet/api/microsoft.azure.search.models.datasource) | [SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) |
| [Skill](/dotnet/api/microsoft.azure.search.models.skill) | [SearchIndexerSkill](/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [Qualifikationsgruppe](/dotnet/api/microsoft.azure.search.models.skillset) | [SearchIndexerSkillset](/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [DataSourceType](/dotnet/api/microsoft.azure.search.models.datasourcetype) | [SearchIndexerDataSourceType](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) |

### <a name="data-import"></a>Datenimport

| Version 10 | Entsprechung in Version 11 |
|------------|-----------------------|
| [IndexAction](/dotnet/api/microsoft.azure.search.models.indexaction) | [IndexDocumentsAction](/dotnet/api/azure.search.documents.models.indexdocumentsaction) |
| [IndexBatch](/dotnet/api/microsoft.azure.search.models.indexbatch) | [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) |

### <a name="query-requests-and-responses"></a>Abfrageanforderungen und -antworten

| Version 10 | Entsprechung in Version 11 |
|------------|-----------------------|
| [DocumentsOperationsExtensions.SearchAsync](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.searchasync) | [SearchClient.SearchAsync](/dotnet/api/azure.search.documents.searchclient.searchasync) |
| [DocumentSearchResult](/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) | [SearchResult](/dotnet/api/azure.search.documents.models.searchresult-1) oder [SearchResults](/dotnet/api/azure.search.documents.models.searchresults-1), abhängig davon, ob das Ergebnis ein einziges Dokument oder mehrere Dokumente ist. |
| [DocumentSuggestResult](/dotnet/api/microsoft.azure.search.models.documentsuggestresult-1) | [SuggestResults](/dotnet/api/azure.search.documents.models.suggestresults-1) |
| [SearchParameters](/dotnet/api/microsoft.azure.search.models.searchparameters) |  [SearchOptions](/dotnet/api/azure.search.documents.searchoptions)  |
| [SuggestParameters](/dotnet/api/microsoft.azure.search.models.suggestparameters) |  [SuggestOptions](/dotnet/api/azure.search.documents.suggestoptions) |
| [SearchParameters.Filter](/dotnet/api/microsoft.azure.search.models.searchparameters.filter) |  [SearchFilter](/dotnet/api/azure.search.documents.searchfilter) (neue Klasse zum Erstellen von OData-Filterausdrücken) |

### <a name="json-serialization"></a>JSON-Serialisierung

Standardmäßig verwendet das Azure SDK [System.Text.Json](/dotnet/api/system.text.json) für die JSON-Serialisierung und nutzt dabei die Funktionen dieser APIs zur Verarbeitung von Texttransformationen. Diese war zuvor über eine native [SerializePropertyNamesAsCamelCaseAttribute](/dotnet/api/microsoft.azure.search.models.serializepropertynamesascamelcaseattribute)-Klasse implementiert, für die es in der neuen Bibliothek kein Pendant gibt.

Zum Serialisieren von Eigenschaftsnamen in die CamelCase-Schreibweise können Sie das [JsonPropertyNameAttribute](/dotnet/api/system.text.json.serialization.jsonpropertynameattribute) verwenden (ähnlich wie in [diesem Beispiel](https://github.com/Azure/azure-sdk-for-net/tree/d263f23aa3a28ff4fc4366b8dee144d4c0c3ab10/sdk/search/Azure.Search.Documents#use-c-types-for-search-results)).

Alternativ dazu können Sie eine [JsonNamingPolicy](/dotnet/api/system.text.json.jsonnamingpolicy) festlegen, die in [JsonSerializerOptions](/dotnet/api/system.text.json.jsonserializeroptions) angegeben ist. Das folgende System.Text.Json-Codebeispiel aus der [Infodatei zu „Microsoft.Azure.Core.Spatial“](https://github.com/Azure/azure-sdk-for-net/blob/259df3985d9710507e2454e1591811f8b3a7ad5d/sdk/core/Microsoft.Azure.Core.Spatial/README.md#deserializing-documents) veranschaulicht die Verwendung von CamelCase, ohne für jede Eigenschaft Attribute angeben zu müssen:

```csharp
// Get the Azure Cognitive Search endpoint and read-only API key.
Uri endpoint = new Uri(Environment.GetEnvironmentVariable("SEARCH_ENDPOINT"));
AzureKeyCredential credential = new AzureKeyCredential(Environment.GetEnvironmentVariable("SEARCH_API_KEY"));

// Create serializer options with our converter to deserialize geographic points.
JsonSerializerOptions serializerOptions = new JsonSerializerOptions
{
    Converters =
    {
        new MicrosoftSpatialGeoJsonConverter()
    },
    PropertyNamingPolicy = JsonNamingPolicy.CamelCase
};

SearchClientOptions clientOptions = new SearchClientOptions
{
    Serializer = new JsonObjectSerializer(serializerOptions)
};

SearchClient client = new SearchClient(endpoint, "mountains", credential, clientOptions);
Response<SearchResults<Mountain>> results = client.Search<Mountain>("Rainier");
```

Wenn Sie Newtonsoft.Json für die JSON-Serialisierung verwenden, können Sie globale Benennungsrichtlinien mithilfe ähnlicher Attribute oder mithilfe von Eigenschaften in [JsonSerializerSettings](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonSerializerSettings.htm) übergeben. Ein Beispiel entsprechend dem obigen finden Sie im [Beispiel für die Deserialisierung von Dokumenten](https://github.com/Azure/azure-sdk-for-net/blob/259df3985d9710507e2454e1591811f8b3a7ad5d/sdk/core/Microsoft.Azure.Core.Spatial.NewtonsoftJson/README.md) in der Newtonsoft.Json-Infodatei.

<a name="WhatsNew"></a>

## <a name="inside-v11"></a>In v11

Jede Version einer Azure Cognitive Search-Clientbibliothek ist auf eine entsprechende Version der REST-API ausgelegt. Die REST-API gilt als Basis für den Dienst, wobei einzelne SDKs eine Version der REST-API umschließen. Als .NET-Entwickler kann es hilfreich sein, die ausführlichere [REST-API-Dokumentation](/rest/api/searchservice/) zu lesen, um detailliertere Informationen zu bestimmten Objekten oder Vorgängen zu erhalten. Version 11 ist auf den [2020-06-30-Suchdienst](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/search/data-plane/Azure.Search/preview/2020-06-30/searchservice.json) ausgelegt. 

Version 11.0 bietet vollständige Unterstützung für die folgenden Objekte und Vorgänge:

+ Indexerstellung und -verwaltung
+ Erstellung und Verwaltung von Synonymzuordnungen
+ Erstellung und Verwaltung von Indexern
+ Erstellung und Verwaltung von Indexerdatenquellen
+ Erstellung und Verwaltung von Skillsets
+ Alle Abfragetypen und Syntax

Ergänzungen zu Version 11.1 (Details zum [Änderungsprotokoll](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md#1110-2020-08-11)):

+ [FieldBuilder](/dotnet/api/azure.search.documents.indexes.fieldbuilder) (in 11.1 hinzugefügt)
+ [Serialisierungseigenschaft](/dotnet/api/azure.search.documents.searchclientoptions.serializer) (in 11.1 hinzugefügt) zur Unterstützung der benutzerdefinierten Serialisierung

Ergänzungen zu Version 11.2 (Details zum [Änderungsprotokoll](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md#1120-2021-02-10)):

+ Mit der [EncryptionKey](/dotnet/api/azure.search.documents.indexes.models.searchindexer.encryptionkey)-Eigenschaft wurden Indexer, Datenquellen und Skillsets hinzugefügt.
+ Unterstützung für die Eigenschaft [IndexingParameters.IndexingParametersConfiguration](/dotnet/api/azure.search.documents.indexes.models.indexingparametersconfiguration)
+ [Räumliche Typen](/dotnet/api/azure.search.documents.indexes.models.searchfielddatatype.geographypoint) werden in [FieldBuilder](/dotnet/api/azure.search.documents.indexes.fieldbuilder.build) nativ unterstützt. [SearchFilter](/dotnet/api/azure.search.documents.searchfilter) kann geometrische Typen aus „Microsoft.Spatial“ ohne explizite Assemblyabhängigkeit codieren.

  Sie können auch weiterhin explizit die Abhängigkeit von [Microsoft.Spatial](https://www.nuget.org/packages/Microsoft.Spatial/) deklarieren. Beispiele für dieses Verfahren sind für [System.Text.Json](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Microsoft.Azure.Core.Spatial/README.md) und [Newtonsoft.Json](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Microsoft.Azure.Core.Spatial.NewtonsoftJson/README.md) verfügbar.

Derzeit in keiner Version von „Azure.Search.Documents“ unterstützt:

+ [Wissensspeicher](knowledge-store-concept-intro.md)

## <a name="before-upgrading"></a>Vor dem Upgrade

+ [Schnellstarts](search-get-started-dotnet.md), Tutorials und [C#-Beispiele](samples-dotnet.md) wurden aktualisiert, um das Azure.Search.Documents-Paket zu verwenden. Es wird empfohlen, dass Sie sich vorhandene Beispiele und exemplarische Vorgehensweisen ansehen, um vor einer Migration mehr über die neuen APIs zu erfahren.

+ Im Artikel [Verwenden von Azure.Search.Documents in einer in C# geschriebenen .NET-Anwendung](search-howto-dotnet-sdk.md) werden die am häufigsten verwendeten APIs erläutert. Selbst Benutzer mit fundierten Kenntnissen im Zusammenhang mit Cognitive Search sollten diese Einführung in die neue Bibliothek als Vorstufe der Migration lesen.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Schritte zum Upgrade

Mit den folgenden Schritten können Sie eine Codemigration beginnen, indem Sie die ersten erforderlichen Schritte absolvieren (insbesondere im Hinblick auf Clientverweise).

1. Installieren Sie das [Azure.Search.Documents-Paket](https://www.nuget.org/packages/Azure.Search.Documents/), indem Sie mit der rechten Maustaste auf Ihre Projektreferenzen klicken und in Visual Studio „NuGet-Pakete verwalten...“ auswählen.

1. Ersetzen Sie die using-Anweisungen für Microsoft.Azure.Search durch die folgenden:

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. Ersetzen Sie für Klassen, die eine JSON-Serialisierung erfordern, `using Newtonsoft.Json` durch `using System.Text.Json.Serialization`.

1. Überarbeiten Sie den Authentifizierungscode für den Client. In früheren Versionen verwendeten Sie Eigenschaften im Clientobjekt, um den API-Schlüssel festzulegen (z. B. die [SearchServiceClient.Credentials](/dotnet/api/microsoft.azure.search.searchserviceclient.credentials)-Eigenschaft). In der aktuellen Version verwenden Sie die [AzureKeyCredential](/dotnet/api/azure.azurekeycredential)-Klasse, um den Schlüssel als Anmeldeinformationen zu übergeben, sodass Sie bei Bedarf den API-Schlüssel aktualisieren können, ohne neue Clientobjekte zu erstellen.

   Clienteigenschaften wurden (soweit möglich) auf `Endpoint`, `ServiceName` und `IndexName` optimiert. Im folgenden Beispiel wird die [Uri](/dotnet/api/system.uri)-Systemklasse verwendet, um den Endpunkt anzugeben, und die [Environment](/dotnet/api/system.environment)-Klasse, um den Schlüsselwert einzulesen:

   ```csharp
   Uri endpoint = new Uri(Environment.GetEnvironmentVariable("SEARCH_ENDPOINT"));
   AzureKeyCredential credential = new AzureKeyCredential(
      Environment.GetEnvironmentVariable("SEARCH_API_KEY"));
   SearchIndexClient indexClient = new SearchIndexClient(endpoint, credential);
   ```

1. Fügen Sie neue Clientverweise für indexerbezogene Objekte hinzu. Wenn Sie Indexer, Datenquellen oder Skillsets verwenden, ändern Sie die Clientverweise in [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient). Dieser Client ist in Version 11 neu und hat keine Vorgängerversion.

1. Überarbeiten Sie Sammlungen und Listen. Im neuen SDK sind alle Listen schreibgeschützt, um Downstreamprobleme zu vermeiden, wenn die Liste NULL-Werte enthält. Der Code muss so geändert werden, dass Elemente in eine Liste eingefügt werden. Anstatt beispielsweise einer Select-Eigenschaft Zeichenfolgen zuzuweisen, fügen Sie diese wie folgt hinzu:

   ```csharp
   var options = new SearchOptions
    {
       SearchMode = SearchMode.All,
       IncludeTotalCount = true
    };

    // Select fields to return in results.
    options.Select.Add("HotelName");
    options.Select.Add("Description");
    options.Select.Add("Tags");
    options.Select.Add("Rooms");
    options.Select.Add("Rating");
    options.Select.Add("LastRenovationDate");
   ```

   „Select“, „Facets“, „SearchFields“, „SourceFields“, „ScoringParameters“ und „OrderBy“ sind jeweils Listen, die nun rekonstruiert werden müssen.

1. Aktualisieren Sie Clientverweise für Abfragen und Datenimport. Instanzen von [SearchIndexClient](/dotnet/api/microsoft.azure.search.searchindexclient) müssen in [SearchClient](/dotnet/api/azure.search.documents.searchclient) geändert werden. Stellen Sie sicher, dass Sie alle Instanzen abfangen, bevor Sie mit dem nächsten Schritt fortfahren, um Namensverwechslungen zu vermeiden.

1. Aktualisieren Sie Clientverweise für Index-, Synonymzuordnungs- und Analysetoolobjekte. Instanzen von [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) müssen in [SearchIndexClient](/dotnet/api/microsoft.azure.search.searchindexclient) geändert werden. 

1. Aktualisieren Sie in Ihrem restlichen Code die Klassen, Methoden und Eigenschaften, um die APIs der neuen Bibliothek zu verwenden. Sie können mit dem Abschnitt [Benennungsunterschiede](#naming-differences) beginnen oder auch das [Änderungsprotokoll](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) einsehen.

   Wenn Sie Schwierigkeiten haben, entsprechende APIs zu finden, schlagen wir vor, ein Problem unter [https://github.com/MicrosoftDocs/azure-docs/issues](https://github.com/MicrosoftDocs/azure-docs/issues) zu melden, damit wir die Dokumentation verbessern oder das Problem untersuchen können.

1. Generieren Sie die Projektmappe neu. Wenn Sie alle Buildfehler bzw. Warnungen behoben haben, können Sie weitere Änderungen an Ihrer Anwendung vornehmen, um die [neue Funktionalität](#WhatsNew) zu nutzen.

<a name="ListOfChanges"></a>

## <a name="breaking-changes"></a>Aktuelle Änderungen

Angesichts der tiefgreifenden Änderungen an Bibliotheken und APIs ist ein Upgrade auf Version 11 nicht trivial und stellt einen Breaking Change in der Hinsicht dar, dass Ihr Code nicht mehr abwärtskompatibel mit Version 10 und früheren Versionen ist. Eine ausführliche Übersicht über die Unterschiede finden Sie in dem [Änderungsprotokoll](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) für `Azure.Search.Documents`.

Hinsichtlich der Dienstversionsupdates, die Codeänderungen in Version 11 für vorhandene Funktionen (und nicht nur ein Refactoring der APIs) nach sich ziehen, treten die folgenden Verhaltensänderungen auf:

+ Der [BM25-Ähnlichkeitsalgorithmus für die Rangfolge](index-ranking-similarity.md) ersetzt den bisherigen Rangfolgealgorithmus durch neuere Technologie. Neue Dienste verwenden diesen Algorithmus automatisch. Für vorhandene Dienste müssen Sie Parameter festlegen, um den neuen Algorithmus zu verwenden.

+ Die Behandlung von Nullwerten in [sortierten Ergebnissen](search-query-odata-orderby.md) hat sich in dieser Version geändert, Nullwerte werden bei `asc`-Sortierung zuerst und bei `desc`-Sortierung zuletzt angezeigt. Wenn Sie die Handhabung von Nullwerten in Code geschrieben haben, sollten Sie diesen überprüfen und möglicherweise entfernen, wenn er nicht mehr erforderlich ist.

## <a name="next-steps"></a>Nächste Schritte

+ [Verwenden von Azure.Search.Documents in einer in C# geschriebenen .NET-Anwendung](search-howto-dotnet-sdk.md)
+ [Tutorial: Hinzufügen von Suchfunktionen zu Web-Apps](tutorial-csharp-overview.md)
+ [Azure.Search.Documents-Paket](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [Beispiele bei GitHub](https://github.com/azure/azure-sdk-for-net/tree/Azure.Search.Documents_11.0.0/sdk/search/Azure.Search.Documents/samples)
+ [Azure.Search.Document-API-Referenz](/dotnet/api/overview/azure/search.documents-readme)
