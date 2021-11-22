---
title: Erstellen eines Suchindex
titleSuffix: Azure Cognitive Search
description: Erstellen Sie einen Suchindex mithilfe des Azure-Portals, der REST-APIs oder eines Azure SDKs.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: how-to
ms.date: 11/12/2021
ms.openlocfilehash: 203b4c6c55c4476e27dad484a2edef4609211343
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487995"
---
# <a name="create-a-search-index-in-azure-cognitive-search"></a>Erstellen eines Suchindex in Azure Cognitive Search

Abfragen in durchsuchbarem Azure Cognitive Search-Zieltext in einem Suchindex. In diesem Artikel lernen Sie die Schritte zum Definieren und Veröffentlichen eines Suchindexes mithilfe einer der von Azure Cognitive Search unterstützten Methoden kennen. 

Sofern Sie keinen [Indexer](search-howto-create-indexers.md) verwenden, sind das Erstellen eines Indexes und das Auffüllen eines Indexes zwei separate Aufgaben. Für Szenarien ohne Indexer besteht der nächste Schritt nach der Indexerstellung im [Datenimport](search-what-is-data-import.md). 

Weitere Informationen zu indexbezogenen Konzepten finden Sie unter [Durchsuchen von Indizes in Azure Cognitive Search](search-what-is-an-index.md).

## <a name="prerequisites"></a>Voraussetzungen

Schreibberechtigungen sind zum Erstellen und Laden von Indizes erforderlich, die über einen [Administrator-API-Schlüssel](search-security-api-keys.md) für die Anforderung gewährt werden. Wenn Sie an der [öffentlichen Vorschauversion der rollenbasierten Zugriffssteuerung](search-security-rbac.md) von Azure Active Directory teilnehmen, können Sie alternativ Ihre Anforderung als Mitglied der Rolle „Mitwirkender an der Suche“ ausstellen.

Die Indexerstellung ist größtenteils eine Schemadefinitionsübung. Vor dem Erstellen sollten Sie über Folgendes verfügen:

+ Eine klare Vorstellung davon, welche Felder in Ihrem Index durchsuchbar, abrufbar, filterbar, facettierbar und sortierbar sein sollen (mehr dazu in der [Schemaprüfliste](#schema-checklist)).

+ Ein eindeutiger Bezeichner in Quelldaten, der als Dokumentschlüssel (oder ID) im Index verwendet werden kann.

+ Eine stabile Indexposition. Das Verschieben eines vorhandenen Indexes in einen anderen Suchdienst wird nicht standardmäßig unterstützt. Schauen Sie sich die Anwendungsanforderungen erneut an, und vergewissern Sie sich, dass der vorhandene Suchdienst, seine Kapazität und sein Speicherort für Ihre Anforderungen ausreichend sind.

Schließlich verfügen alle Dienstebenen über [Indexgrenzwerte](search-limits-quotas-capacity.md#index-limits) für die Anzahl von Objekten, die Sie erstellen können. Wenn Sie beispielsweise mit dem Free-Tarif experimentieren, können jeweils nur drei Indizes gleichzeitig vorhanden sein. Innerhalb des Index selbst gibt es Grenzwerte für die Anzahl komplexer Felder und Sammlungen.

## <a name="allowed-updates"></a>Zulässige Updates

[**Index erstellen**](/rest/api/searchservice/create-index) ist ein Vorgang, der die physischen Datenstrukturen (Dateien und invertierte Indizes) in Ihrem Suchdienst erstellt. Sobald der Index erstellt wurde, hängt die Möglichkeit, Änderungen mithilfe von [**Index erstellen**](/rest/api/searchservice/update-index) vorzunehmen, davon ab, ob diese physischen Strukturen durch Ihre Änderungen ungültig werden. Die meisten Feldattribute können nicht mehr geändert werden, nachdem das Feld in Ihrem Index erstellt wurde.

Um die Änderungsrate im Entwurfsprozess zu minimieren, wird in der folgenden Tabelle beschrieben, welche Elemente im Schema fest und welche flexibel sind. Das Ändern eines festen Elements erfordert eine Indexneuerstellung, während flexible Elemente jederzeit ohne Beeinträchtigung der physischen Implementierung geändert werden können. 

| Element | Kann aktualisiert werden? |
|---------|-----------------|
| Name | Nein |
| Schlüssel | Nein |
| Feldnamen und -typen | Nein |
| Feldattribute (durchsuchbar, filterbar, facettierbar, sortierbar) | Nein |
| Feldattribut (abrufbar) | Ja |
| [Analyzer](search-analyzers.md) | Sie können benutzerdefinierte Analyzer im Index hinzufügen und ändern. In Bezug auf Analyzerzuweisungen in Zeichenfolgenfeldern können Sie nur „searchAnalyzer“ ändern. Alle anderen Zuweisungen und Änderungen erfordern eine Neuerstellung. |
| [Bewertungsprofile](index-add-scoring-profiles.md) | Ja |
| [Vorschläge](index-add-suggesters.md) | Nein |
| [Cross-Origin Remote Scripting (CORS)](#corsoptions) | Ja |
| [Verschlüsselung](search-security-manage-encryption-keys.md) | Ja |

> [!NOTE]
> [Synonymzuordnungen](search-synonyms.md) sind nicht Teil einer Indexdefinition. Änderungen an einer Synonymzuordnung haben keine Auswirkungen auf den physischen Suchindex.

## <a name="schema-checklist"></a>Schemaprüfliste

Verwenden Sie diese Prüfliste bei den Entwurfsentscheidungen für Ihren Suchindex.

1. Überprüfen Sie die [Benennungskonventionen](/rest/api/searchservice/naming-rules), damit Index- und Feldnamen den Benennungsregeln entsprechen.

1. Sehen Sie sich die [unterstützten Datentypen](/rest/api/searchservice/supported-data-types) an. Der Datentyp wirkt sich auf die Verwendung des Felds aus. Numerische Inhalte können beispielsweise gefiltert, aber nicht per Volltextsuche durchsucht werden. Der gängigste Datentyp ist `Edm.String` für durchsuchbaren Text, in Token umgewandelt und abgefragt mithilfe der Volltextsuchmaschine.

1. Identifizieren Sie ein Feld in den Quelldaten, das eindeutige Werte enthält, sodass es als Schlüsselfeld in Ihrem Index fungieren kann. Wenn Sie beispielsweise die Indizierung über Blob Storage vornehmen, wird der Speicherpfad häufig als Dokumentschlüssel verwendet. 

   Jeder Index erfordert ein Feld, das als *Dokumentschlüssel* dient (manchmal auch als „Dokument-ID“ bezeichnet). Der Schlüssel ist eine Zeichenfolge im Suchindex, aber Sie können ihn jedem eindeutigen Bezeichner in Ihren Quelldaten zuordnen. Die Möglichkeit, bestimmte Suchdokumente eindeutig zu identifizieren, ist für die Wiederherstellung eines Datensatzes oder einer Entität in einem Suchergebnis, für das Abrufen eines bestimmten Dokuments im Suchindex und für die selektive Datenverarbeitung auf Dokumentebene erforderlich.

1. Identifizieren Sie die Felder in Ihrer Datenquelle, die durchsuchbare Inhalte zum Index beitragen. Durchsuchbare Inhalte sind beispielweise kurze oder lange Zeichenfolgen, die mit der Volltextsuchmaschine abgefragt werden. Wenn der Inhalt ausführlich ist (kleine Ausdrücke oder größere Blöcke), experimentieren Sie mit verschiedenen Analyzern, um zu sehen, wie der Text in Token umgewandelt wird.

   [Feldattributzuweisungen](search-what-is-an-index.md#index-attributes) bestimmen sowohl das Suchverhalten als auch die physische Darstellung Ihres Indexes im Suchdienst. Festzulegen, wie Felder angegeben werden sollen, ist für viele Kunden ein iterativer Prozess. Um Iterationen zu beschleunigen, beginnen Sie mit Beispieldaten, damit Sie problemlos löschen und neu erstellen können.

1. Identifizieren Sie, welche Quellfelder als Filter verwendet werden können. Numerische Inhalte und kurze Textfelder, insbesondere solche mit sich wiederholenden Werten, sind gute Optionen. Beachten Sie beim Arbeiten mit Filtern Folgendes:

   + Filterbare Felder können optional in der Facettennavigation verwendet werden.

   + Filterbare Felder werden in beliebiger Reihenfolge zurückgegeben. Erwägen Sie daher, sie auch sortierbar zu machen.

## <a name="formulate-a-request"></a>Formulieren einer Anforderung

Wenn Sie bereit sind, den Index zu erstellen, gibt es mehrere Möglichkeiten, fortzufahren. Für die anfängliche Entwicklung sowie für Proof of Concept-Tests empfiehlt sich die Verwendung des Azure-Portals oder der REST-APIs.

Planen Sie während der Entwicklung häufige Neuerstellungen ein. Da physische Strukturen im Dienst erstellt werden, ist bei vielen Änderungen das [Löschen und Neuerstellen von Indizes](search-howto-reindex.md) erforderlich. Sie sollten erwägen, mit einer Teilmenge Ihrer Daten zu arbeiten, damit Neuerstellungen schneller gehen.

### <a name="azure-portal"></a>[**Azure-Portal**](#tab/indexer-portal)

Bei der Indexerstellung über das Portal werden Anforderungen und Schemaregeln für bestimmte Datentypen erzwungen, z. B. das Deaktivieren von Funktionen zur Volltextsuche bei numerischen Feldern. Im Portal stehen zwei Optionen für die Erstellung eines Suchindex zur Verfügung: 

+ **Index hinzufügen** ist ein eingebetteter Editor zum Angeben eines Indexschemas.
+ [**Daten importieren**](search-import-data-portal.md) ist ein Assistent.

Der Assistent integriert zusätzliche Vorgänge, indem er außerdem einen Indexer und eine Datenquelle erstellt und Daten lädt. Wenn Sie diesen Umfang nicht benötigen, sollten Sie einfach **Index hinzufügen** oder einen anderen Ansatz verwenden.

Der folgende Screenshot zeigt, wo Sie **Index hinzufügen** und **Daten importieren** in der Befehlsleiste finden. Nachdem ein Index erstellt wurde, können Sie ihn auf der Registerkarte **Indizes** wiederfinden.

  :::image type="content" source="media/search-what-is-an-index/add-index.png" alt-text="Befehl „Index hinzufügen“" border="true":::

> [!Tip]
> Nachdem Sie einen Index im Portal erstellt haben, können Sie die JSON-Darstellung kopieren und ihrem Anwendungscode hinzufügen.

### <a name="rest"></a>[**REST**](#tab/kstore-rest)

[**Index erstellen (REST)**](/rest/api/searchservice/create-index) wird verwendet, um einen Index zu erstellen. Sowohl Postman als auch Visual Studio Code (mit einer Erweiterung für Azure Cognitive Search) kann als Suchindexclient fungieren. Mit beiden Tools können Sie eine Verbindung mit Ihrem Suchdienst herstellen und die Anforderungen senden:

+ [Schnellstart: Erstellen eines Azure Cognitive Search-Index mithilfe von REST-APIs](search-get-started-rest.md)
+ [Erste Schritte mit Visual Studio Code und Azure Cognitive Search](search-get-started-vs-code.md)

Die REST-API stellt Standardwerte für die Feldzuordnung bereit. Beispielsweise sind alle Edm.String-Felder standardmäßig durchsuchbar. Unten werden die Attribute zur Veranschaulichung vollständig angezeigt, aber Sie können die Zuordnung in Fällen, in denen die Standardwerte gelten, weglassen.

Hilfreiche Informationen zum Formulieren von Indexanforderungen finden Sie unter [Indexvorgänge (REST)](/rest/api/searchservice/index-operations).

```json
POST https://[servicename].search.windows.net/indexes?api-version=[api-version] 
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "retrievable": true, "searchable": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "retrievable": true, "searchable": true, "filterable": false, "sortable": true, "facetable": false },
    { "name": "Description", "type": "Edm.String", "retrievable": true, "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.microsoft" },
    { "name": "Description_fr", "type": "Edm.String", "retrievable": true, "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.microsoft" },
    { "name": "Address", "type": "Edm.ComplexType", 
      "fields": [
          { "name": "StreetAddress", "type": "Edm.String", "retrievable": true, "filterable": false, "sortable": false, "facetable": false, "searchable": true },
          { "name": "City", "type": "Edm.String", "retrievable": true, "searchable": true, "filterable": true, "sortable": true, "facetable": true },
          { "name": "StateProvince", "type": "Edm.String", "retrievable": true, "searchable": true, "filterable": true, "sortable": true, "facetable": true }
        ]
    }
  ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "analyzers":(optional)[ ... ]
  }
}
```

### <a name="net-sdk"></a>[**.NET SDK**](#tab/kstore-dotnet)

Das Azure SDK für .NET verfügt über [**SearchIndexClient**](/dotnet/api/azure.search.documents.indexes.searchindexclient) mit Methoden zum Erstellen und Aktualisieren von Indizes.

```csharp
// Create the index
string indexName = "hotels";
SearchIndex index = new SearchIndex(indexName)
{
    Fields =
    {
        new SimpleField("hotelId", SearchFieldDataType.String) { IsKey = true, IsFilterable = true, IsSortable = true },
        new SearchableField("hotelName") { IsFilterable = true, IsSortable = true },
        new SearchableField("description") { AnalyzerName = LexicalAnalyzerName.EnLucene },
        new SearchableField("descriptionFr") { AnalyzerName = LexicalAnalyzerName.FrLucene }
        new ComplexField("address")
        {
            Fields =
            {
                new SearchableField("streetAddress"),
                new SearchableField("city") { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new SearchableField("stateProvince") { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new SearchableField("country") { SynonymMapNames = new[] { synonymMapName }, IsFilterable = true, IsSortable = true, IsFacetable = true },
                new SearchableField("postalCode") { IsFilterable = true, IsSortable = true, IsFacetable = true }
            }
        }
    }
};

await indexClient.CreateIndexAsync(index);
```

Weitere Beispiele finden Sie unter [azure-search-dotnet-samples/quickstart/v11/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11).

### <a name="other-sdks"></a>[**Andere SDKs**](#tab/other-sdks)

Für Cognitive Search werden von den Azure-SDKs allgemein verfügbare Features implementiert. Daher kann jedes der SDKs für die Erstellung eines Suchindex verwendet werden. Alle bieten einen **SearchIndexClient**, der über Methoden zum Erstellen und Aktualisieren von Indizes verfügt.

| Azure SDK | Client | Beispiele |
|-----------|--------|----------|
| Java | [SearchIndexClient](/java/api/com.azure.search.documents.indexes.searchindexclient) | [CreateIndexExample.java](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) |
| JavaScript | [SearchIndexClient](/javascript/api/@azure/search-documents/searchindexclient) | [Indizes](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/search/search-documents/samples/v11/javascript) |
| Python | [SearchIndexClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient) | [sample_index_crud_operations.py](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) |

---

<a name="corsoptions"></a>

## <a name="set-corsoptions-for-cross-origin-queries"></a>`corsOptions` für ursprungsübergreifende Abfragen festlegen

Indexschemas enthalten einen Abschnitt zum Festlegen von `corsOptions`. Clientseitiger JavaScript-Code kann standardmäßig keine APIs aufrufen, da der Browser jegliche ursprungsübergreifenden Anforderungen verhindert. Um ursprungsübergreifende Abfragen für Ihren Index zu ermöglichen, aktivieren Sie CORS (Cross-Origin Resource Sharing), indem Sie das Attribut **corsOptions** festlegen. Aus Sicherheitsgründen wird CORS nur von [Abfrage-APIs](search-query-create.md#choose-query-methods) unterstützt.

```json
"corsOptions": {
  "allowedOrigins": [
    "*"
  ],
  "maxAgeInSeconds": 300
```

Die folgenden Eigenschaften können für CORS festgelegt werden:

+ **allowedOrigins** (erforderlich): Dies ist eine Liste der Ursprünge, denen Zugriff auf Ihren Index gewährt wird. Dies bedeutet, dass jeglicher von diesen Ursprüngen bereitgestellte Javascript-Code Indexabfragen durchführen kann (sofern er den richtigen API-Schlüssel angibt). Ursprünge werden in der Regel im Format `protocol://<fully-qualified-domain-name>:<port>` angegeben, wobei `<port>` häufig weggelassen wird. Weitere Informationen finden Sie unter [Cross-Origin Resource Sharing, CORS (Wikipedia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing).

  Wenn Sie den Zugriff auf alle Ursprünge zulassen möchten, beziehen Sie `*` als ein einzelnes Element in das Array **allowedOrigins** ein. *Dies ist keine empfohlene Vorgehensweise für Produktionssuchdienste*, ist für Entwicklung und Debugging jedoch oft nützlich.

+ **maxAgeInSeconds** (optional): Von Browsern wird dieser Wert verwendet, um die Dauer (in Sekunden) des Zwischenspeicherns von CORS-Preflight-Antworten zu ermitteln. Dies muss eine positive ganze Zahl sein. Mit dem Wert steigt auch die Leistung, aber es dauert auch länger, bis CORS-Richtlinienänderungen in Kraft treten. Wenn diese Einstellung nicht festgelegt ist, gilt die Standarddauer von 5 Minuten.

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie die folgenden Links, um sich mit dem Laden eines Index mit Daten vertraut zu machen.

+ [Übersicht über den Datenimport](search-what-is-data-import.md)

+ [Hinzufügen, Aktualisieren oder Löschen von Dokumenten (REST)](/rest/api/searchservice/addupdate-or-delete-documents) 