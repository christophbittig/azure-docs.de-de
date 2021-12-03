---
title: Indizieren von Daten aus der Gremlin-API (Vorschau)
titleSuffix: Azure Cognitive Search
description: Richten Sie einen Azure Cosmos DB-Indexer ein, um die Indizierung von Gremlin-API-Inhalten für Volltextsuchen in Azure Cognitive Search zu automatisieren.
author: gmndrg
ms.author: gimondra
manager: nitinme
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/11/2021
ms.openlocfilehash: a0876ef39d74403ad6ae5d88fbac12aeed543e27
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893802"
---
# <a name="index-data-using-azure-cosmos-db-gremlin-api"></a>Indizieren von Daten mithilfe der Gremlin-API von Azure Cosmos DB

> [!IMPORTANT]
> Der Cosmos DB-Gremlin-API-Indexer befindet sich derzeit unter [Zusätzliche Nutzungsbedingungen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) in der öffentlichen Vorschauphase. Fordern Sie [Zugriff](https://aka.ms/azure-cognitive-search/indexer-preview) auf dieses Feature an, und verwenden Sie, nachdem der Zugriff aktiviert wurde, eine [Vorschau-REST-API (2020-06-30-preview oder höher)](search-api-preview.md), um Ihre Inhalte zu indizieren. Die Portalunterstützung ist momentan eingeschränkt, und das .NET SDK wird nicht unterstützt.

In diesem Artikel erfahren Sie, wie Sie einen Azure Cosmos DB-Indexer zum Extrahieren von Inhalten konfigurieren und dafür sorgen, dass er in der kognitiven Azure-Suche durchsucht werden kann. Mit diesem Workflow erstellen Sie einen Suchindex für Azure Cognitive Search und laden ihn mit bestehendem Inhalt, der mithilfe der Gremlin-API aus Azure Cosmos DB extrahiert wurde.

Da die Begrifflichkeiten etwas verwirrend sind, soll hier darauf hingewiesen werden, dass die [Indizierung in Azure Cosmos DB](../cosmos-db/index-overview.md) und die [Indizierung in der kognitiven Azure-Suche](search-what-is-an-index.md) zwei verschiedene Vorgänge sind, die jedem Dienst eigen sind. Wenn Sie mit der Indizierung in der kognitiven Azure-Suche beginnen, muss Ihre Azure Cosmos DB-Datenbank bereits vorhanden sein und Daten enthalten.

## <a name="prerequisites"></a>Voraussetzungen

Damit die Azure Cognitive Search Daten in Cosmos DB über die Gremlin-API indexieren kann, muss auch die [Cosmos DB-eigene Indexierung](../cosmos-db/index-overview.md) aktiviert und auf [Konsistent](../cosmos-db/index-policy.md#indexing-mode) festgelegt sein. Dies ist die Standardkonfiguration für Cosmos DB. Die Indexierung von Azure Cognitive Search funktioniert nicht, wenn Cosmos DB-Indexierung bereits aktiviert ist.

## <a name="get-started"></a>Erste Schritte

Sie können die [REST-API Vorschau](/rest/api/searchservice/index-preview) verwenden, um Azure Cosmos DB-Daten zu indexieren, die über die Gremlin-API verfügbar sind, indem Sie einen dreiteiligen Arbeitsablauf befolgen, der für alle Indexer in Azure Cognitive Search gilt: Erstellen einer Datenquelle, Erstellen eines Index, Erstellen eines Indexers. Im folgenden Prozess beginnt die Datenextraktion aus Cosmos DB, wenn Sie die Anforderung „Indexer erstellen“ übermitteln.

Standardmäßig macht Azure Cognitive Search Cosmos DB Gremlin-API-Indexer jeden Vertex in Ihrem Graphen zu einem Dokument im Index. Kanten werden ignoriert. Alternativ können Sie die Abfrage so festlegen, dass nur die Kanten indexiert werden.

### <a name="step-1---assemble-inputs-for-the-request"></a>Schritt 1: Zusammenstellen der Eingaben für die Anforderung

Bei jeder Anfrage müssen Sie den Dienstnamen und den Administratorschlüssel für Azure Cognitive Search (im POST-Header) angeben. Mit [Postman](./search-get-started-rest.md) oder einem beliebigen REST API-Client können Sie HTTPS-Anfragen an Azure Cognitive Search senden.

Kopieren und speichern Sie die folgenden Werte zur Verwendung in Ihrer Anfrage:

+ Dienstname der kognitiven Azure-Suche
+ Administratorschlüssel der kognitiven Azure-Suche
+ Cosmos DB Gremlin API-Verbindungszeichenfolge

Sie können diese Werte im Azure-Portal finden:

1. Kopieren Sie in den Portalseiten für die kognitive Azure-Suche die Suchdienst-URL von der Seite „Übersicht“.

2. Klicken Sie im linken Navigationsbereich auf **Schlüssel** und kopieren Sie dann entweder den primären oder sekundären Schlüssel.

3. Wechseln Sie zu den Portalseiten für Ihr Cosmos-DB-Konto. Klicken Sie im linken Navigationsbereich unter **Einstellungen** auf die Option **Schlüssel**. Diese Seite enthält einen URI, zwei Sätze Verbindungszeichenfolgen und zwei Sätze Schlüssel. Kopieren Sie eine der Verbindungszeichenfolgen in den Editor.

### <a name="step-2---create-a-data-source"></a>Schritt 2: Erstellen einer Datenquelle

Eine **Datenquelle** gibt die zu indizierenden Daten, Anmeldeinformationen und Richtlinien für das Bestimmen von Änderungen in den Daten an (z.B. geänderte oder gelöschte Dokumente in Ihrer Sammlung). Die Datenquelle wird als unabhängige Ressource definiert, sodass sie von mehreren Indexern verwendet werden kann.

Formulieren Sie zum Erstellen einer Datenquelle eine POST-Anforderung:

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [Search service admin key]
    
    {
        "name": "mycosmosdbgremlindatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;ApiKind=Gremlin;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myGraphId", "query": null }
    }
```

Der Anforderungstext umfasst die Datenquellendefinition, welche die folgenden Felder enthalten sollte:

| Feld   | BESCHREIBUNG |
|---------|-------------|
| **name** | Erforderlich. Wählen Sie für Ihr Datenquellenobjekt einen beliebigen Namen aus. |
|**type**| Erforderlich. Muss `cosmosdb`lauten. |
|**credentials** | Erforderlich. Der **connectionString** muss einen AccountEndpoint, AccountKey, ApiKind und Database enthalten. ApiKind ist **Gremlin.**</br></br>Beispiel:<br/>`AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=Gremlin`<br/><br/>AccountEndpoint muss den Endpunkt `*.documents.azure.com` verwenden.
| **container** | Enthält die folgenden Elemente: <br/>**name:** Erforderlich. Bestimmen Sie die ID des Diagramms.<br/>**Abfrage**: Optional. Der Standardwert ist `g.V()`. Um die Kanten zu indexieren, setzen Sie die Abfrage auf `g.E()`. |
| **dataChangeDetectionPolicy** | Standardmäßig wird der inkrementelle Fortschritt aktiviert, indem `_ts` als Spalte für die Hochwassermarke verwendet wird. |
|**dataDeletionDetectionPolicy** | Optional. Weitere Informationen finden Sie im Abschnitt [Indizieren von gelöschten Dokumenten](#DataDeletionDetectionPolicy).|

### <a name="step-3---create-a-target-search-index"></a>Schritt 3: Erstellen eines Zielsuchindex

[Erstellen Sie einen Zielindex der kognitiven Azure-Suche ](/rest/api/searchservice/create-index), wenn Sie bislang noch über keinen verfügen. Anhand des folgenden Beispiels wird ein Index mit ID und einem Beschreibungsfeld erstellt:

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [
        {
            "name": "rid",
            "type": "Edm.String",
            "facetable": false,
            "filterable": false,
            "key": true,
            "retrievable": true,
            "searchable": true,
            "sortable": false,
            "analyzer": "standard.lucene",
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "synonymMaps": [],
            "fields": []
        },{
            "name": "id",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "retrievable": true,
            "sortable": false,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": "standard.lucene",
            "synonymMaps": []
        }, {
            "name": "label",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "retrievable": true,
            "sortable": false,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": "standard.lucene",
            "synonymMaps": []
       }]
     }
```

Achten Sie darauf, dass das Schema Ihres Zielindex mit Ihrem Diagramm kompatibel ist.

Bei partitionierten Sammlungen ist der Standarddokumentschlüssel die Eigenschaft `_rid` von Azure Cosmos DB. Diese wird von Azure Cognitive Search automatisch in `rid` umbenannt, da Feldnamen nicht mit einem Unterstrich beginnen dürfen. Darüber hinaus enthalten die `_rid`-Werte von Azure Cosmos DB Zeichen, die in Schlüsseln der kognitiven Azure-Suche ungültig sind. Deshalb sollten die `_rid`-Werte Base64-kodiert sein, wenn Sie sie zu Ihrem Dokumentschlüssel machen möchten.

### <a name="mapping-between-json-data-types-and-azure-cognitive-search-data-types"></a>Zuordnung zwischen JSON-Datentypen und Datentypen der kognitiven Azure-Suche

| JSON-Datentyp | Kompatible Feldtypen im Zielindex |
| --- | --- |
| Bool |Edm.Boolean, Edm.String |
| Zahlen, die wie Ganzzahlen aussehen |Edm.Int32, Edm.Int64, Edm.String |
| Zahlen, die wie Gleitkommas aussehen |Edm.Double, Edm.String |
| String |Edm.String |
| Arrays primitiver Typen, z.B. ["a", "b", "c"] |Collection(Edm.String) |
| Zeichenfolgen, die wie Datumsangaben aussehen |Edm.DateTimeOffset, Edm.String |
| GeoJSON-Objekte, z.B. { "type": "Point", "coordinates": [long, lat] } |Edm.GeographyPoint |
| Andere JSON-Objekte |– |

### <a name="step-4---configure-and-run-the-indexer"></a>Schritt 4: Konfigurieren und Ausführen des Indexers

Nach der Erstellung von Index und Datenquelle können Sie den Indexer erstellen:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name": "mycosmosdbgremlinindexer",
      "description": "My Cosmos DB Gremlin API indexer",
      "dataSourceName": "mycosmosdbgremlindatasource",
      "targetIndexName": "mysearchindex"
    }
```

Dieser Indexer wird nach seiner Erstellung gestartet und nur einmal ausgeführt. Sie können den optionalen Zeitplanparameter zur Anfrage hinzufügen, um Ihren Indexer so einzustellen, dass er nach einem Zeitplan läuft. Weitere Informationen zum Definieren von Indexerzeitplänen finden Sie unter [Festlegen eines Zeitplans für Indexer in der kognitiven Azure-Suche](search-howto-schedule-indexers.md).

Weitere Informationen zur API zum Erstellen eines Indexers finden Sie unter [Erstellen eines Indexers](/rest/api/searchservice/create-indexer).

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>Indizieren von gelöschten Dokumenten

Wenn Diagrammdaten gelöscht werden, sollten Sie eventuell auch das zugehörige Dokument aus dem Suchindex löschen. Der Zweck einer Richtlinie zur Erkennung von Datenlöschung besteht darin, gelöschte Datenelemente effizient zu identifizieren und das vollständige Dokument aus dem Index zu löschen. Die Richtlinie zur Erkennung von Datenlöschung dient nicht zum Löschen teilweiser Dokumentinformationen. Zurzeit ist `Soft Delete` die einzige unterstützte Richtlinie (die Löschung wird durch ein bestimmtes Kennzeichen markiert). Diese wird folgendermaßen festgelegt:

```http
    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }
```

Im folgenden Beispiel wird eine Datenquelle mit einer Richtlinie zum vorläufigen Löschen erstellt:

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [Search service admin key]
    
    {
        "name": "mycosmosdbgremlindatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;ApiKind=Gremlin;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "`_ts`"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }
```

<a name="MappingGraphData"></a>

## <a name="mapping-graph-data-to-a-search-index"></a>Zuordnen von Diagrammdaten zu einem Suchindex

Der Cosmos DB Gremlin-API-Indexer wird automatisch ein paar Teile der Diagrammdaten für Sie zuordnen:

1. Der Indexer wird `_rid` einem Feld im Index `rid` zugeordnet, sofern vorhanden. Beachten Sie bitte, dass Sie, wenn Sie den Wert `rid` als Schlüssel in Ihrem Index verwenden möchten, den Schlüssel Base64-kodieren sollten, da `_rid` Zeichen enthalten kann, die in Azure Cognitive Search-Dokumentschlüsseln ungültig sind.

1. Der Indexer wird `_id` einem Feld im Index `id` zugeordnet, sofern vorhanden.

1. Bei der Abfrage Ihrer Cosmos DB-Datenbank mit der Gremlin-API werden Sie vielleicht feststellen, dass die JSON-Ausgabe für jede Eigenschaft ein `id` und ein `value` hat. Der Azure Cognitive Search Cosmos DB-Indexer ordnet die Eigenschaften `value` automatisch einem Feld in Ihrem Suchindex zu, das denselben Namen wie die Eigenschaft hat, sofern vorhanden. In dem folgenden Beispiel würde 450 auf ein `pages`-Feld im Suchindex abgebildet werden.

```http
    {
        "id": "Cookbook",
        "label": "book",
        "type": "vertex",
        "properties": {
          "pages": [
            {
              "id": "48cf6285-a145-42c8-a0aa-d39079277b71",
              "value": "450"
            }
          ]
        }
    }
```

Möglicherweise müssen Sie [Output Field Mappings](cognitive-search-output-field-mapping.md) verwenden, um Ihre Abfrageausgabe auf die Felder in Ihrem Index abzubilden. Wahrscheinlich werden Sie Output Field Mappings statt [Field Mappings](search-indexer-field-mappings.md) verwenden wollen, da die benutzerdefinierte Abfrage wahrscheinlich komplexe Daten enthalten wird.

Angenommen, Ihre Abfrage erzeugt die folgende Ausgabe:

```json
    [
      {
        "vertex": {
          "id": "Cookbook",
          "label": "book",
          "type": "vertex",
          "properties": {
            "pages": [
              {
                "id": "48cf6085-a211-42d8-a8ea-d38642987a71",
                "value": "450"
              }
            ],
          }
        },
        "written_by": [
          {
            "yearStarted": "2017"
          }
        ]
      }
    ]
```

Falls Sie den Wert von `pages` im obigen JSON auf ein `totalpages` Feld in Ihrem Index zuordnen möchten, können Sie die folgende [Ausgabefeldzuordnung](cognitive-search-output-field-mapping.md) zu Ihrer Indexerdefinition hinzufügen:

```json
    ... // rest of indexer definition 
    "outputFieldMappings": [
        {
          "sourceFieldName": "/document/vertex/pages",
          "targetFieldName": "totalpages"
        }
    ]
```

Achten Sie darauf, dass das Output Field Mapping mit `/document` beginnt und keinen Verweis auf den Eigenschaftenschlüssel im JSON enthält. Dies geschieht, weil der Indexer beim Einlesen der Diagrammdaten jedes Dokument unter den `/document`-Knoten legt und der Indexer es Ihnen zudem automatisch ermöglicht, den Wert von `pages` durch einfaches Referenzieren von `pages` zu referenzieren, anstatt auf das erste Objekt im Array von `pages` verweisen zu müssen.

## <a name="next-steps"></a>Nächste Schritte

+ Weitere Informationen über die Azure Cosmos DB Gremlin-API finden Sie in der [Einführung in Azure Cosmos DB: Gremlin-API]()../cosmos-db/graph-introduction.md).

+ Weitere Informationen zu Azure Cognitive Search-Szenarien und -Preisen finden Sie auf der [Suchdienst-Seite auf azure.microsoft.com](https://azure.microsoft.com/services/search/).