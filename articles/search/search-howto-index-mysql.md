---
title: Indizieren von Daten aus Azure MySQL (Vorschau)
titleSuffix: Azure Cognitive Search
description: Richten Sie einen Suchindexer ein, um in Azure MySQL gespeicherte Daten für die Volltextsuche in Azure Cognitive Search zu indizieren.
author: gmndrg
ms.author: gimondra
manager: nitinme
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/17/2021
ms.openlocfilehash: aaf8597887eb7d5f7f416fc2c9aa8731b5b8ce1e
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892986"
---
# <a name="index-data-from-azure-mysql"></a>Indizieren von Daten aus Azure MySQL

> [!IMPORTANT] 
> Die Unterstützung für MySQL befindet sich derzeit in der öffentlichen Vorschauphase unter [Zusätzliche Nutzungsbedingungen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Fordern Sie [Zugriff](https://aka.ms/azure-cognitive-search/indexer-preview) auf dieses Feature an, und verwenden Sie, nachdem der Zugriff aktiviert wurde, eine [Vorschau-REST-API (2020-06-30-preview oder höher)](search-api-preview.md), um Ihre Inhalte zu indizieren. Unterstützung für SDK und Portal ist derzeit nicht vorhanden.

Der Azure Cognitive Search-Indexer für MySQL durchforscht Ihre MySQL-Datenbank in Azure, extrahiert durchsuchbare Daten und indiziert sie in Azure Cognitive Search. Der Indexer nimmt alle Änderungen, Uploads und Löschungen für Ihre MySQL-Datenbank vor und übernimmt diese Änderungen in Ihrem Suchindex.

Sie können einen Azure MySQL-Indexer mit einem der folgenden Clients einrichten:

* [Azure portal](https://ms.portal.azure.com)
* [REST-API](/rest/api/searchservice/Indexer-operations) für die kognitive Azure-Suche
* [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexer) für die kognitive Azure-Suche

In diesem Artikel werden die REST-APIs verwendet. 

## <a name="create-an-azure-mysql-indexer"></a>Erstellen eines Azure MySQL-Indexers

Führen Sie die folgenden Schritte aus, um MySQL in Azure zu indizieren.

### <a name="step-1-create-a-data-source"></a>Schritt 1: Erstellen einer Datenquelle

Zum Erstellen des Index senden Sie die folgende Anforderung:

```http

    POST https://[search service name].search.windows.net/datasources?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
    {   
        "name" : "[Data source name]"
        "description" : "[Description of MySQL data source]",
        "type" : "mysql",
        "credentials" : { 
            "connectionString" : 
                "Server=[MySQLServerName].MySQL.database.azure.com; Port=3306; Database=[DatabaseName]; Uid=[UserName]; Pwd=[Password]; SslMode=Preferred;" 
        },
        "container" : { 
            "name" : "[TableName]" 
        },
        "dataChangeDetectionPolicy" : { 
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "[HighWaterMarkColumn]"
        }
    }

```

### <a name="step-2-create-an-index"></a>Schritt 2: Erstellen eines Index

Erstellen Sie den Zielindex der kognitiven Azure-Suche, wenn Sie bislang noch über keinen verfügen.

```http

    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
       "name": "[Index name]",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "searchable": true,
         "sortable": false,
         "facetable": false
       }]
    }

```

### <a name="step-3-create-the-indexer"></a>Schritt 3: Erstellen des Indexers

Nach der Erstellung von Index und Datenquelle können Sie den Indexer erstellen.

```http

    POST https://[search service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
    {
        "name" : "[Indexer name]"
        "description" : "[Description of MySQL indexer]",
        "dataSourceName" : "[Data source name]",
        "targetIndexName" : "[Index name]"
    }

```

## <a name="run-indexers-on-a-schedule"></a>Ausführen von Indexern nach einem Zeitplan
Sie können den Indexer auch so konfigurieren, dass er regelmäßig nach einem Zeitplan ausgeführt wird. Dazu fügen Sie die **schedule**-Eigenschaft beim Erstellen oder Aktualisieren des Indexers hinzu. Das folgende Beispiel zeigt eine PUT-Anforderung den Indexer, um den zu aktualisieren:

```http
    PUT https://[search service name].search.windows.net/indexers/[Indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin-key]

    {
        "dataSourceName" : "[Data source name]",
        "targetIndexName" : "[Index name]",
        "schedule" : { 
            "interval" : "PT10M", 
            "startTime" : "2021-01-01T00:00:00Z"
        }
    }
```

Der Parameter **interval** ist erforderlich. Das Intervall bezieht sich auf den Zeitraum zwischen dem Start von zwei aufeinander folgenden Indexerausführungen. Das kleinste zulässige Intervall beträgt 5 Minuten. Das längste ist ein Tag. Es muss als XSD-Wert „dayTimeDuration“ formatiert sein (eine eingeschränkte Teilmenge eines [ISO 8601-Zeitwerts](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)). Das Muster hierfür lautet wie folgt: `P(nD)(T(nH)(nM))`. Beispiele: `PT15M` = alle 15 Minuten, `PT2H` = alle 2 Stunden.

Weitere Informationen zum Definieren von Indexerzeitplänen finden Sie unter [Festlegen eines Zeitplans für Indexer in der kognitiven Azure-Suche](search-howto-schedule-indexers.md).

## <a name="capture-new-changed-and-deleted-rows"></a>Erfassen neuer, geänderter und gelöschter Zeilen

In der kognitiven Azure-Suche wird eine **inkrementelle Indizierung** verwendet, um zu vermeiden, dass die gesamte Tabelle oder Ansicht jedes Mal neu indiziert wird, wenn ein Indexer ausgeführt wird.

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>Richtlinie zum Erkennen von Änderungen mit oberem Grenzwert

Diese Richtlinie zur Erkennung von Änderungen basiert auf einer Spalte mit oberem Grenzwert, die Version oder Uhrzeit der letzten Aktualisierung einer Zeile erfasst. Bei Verwendung einer Sicht müssen Sie eine Richtlinie mit oberem Grenzwert einsetzen. Die Spalte mit dem oberen Grenzwert muss die folgenden Anforderungen erfüllen.

#### <a name="requirements"></a>Requirements (Anforderungen) 

* Alle Einfügungen geben einen Wert für die Spalte an.
* Alle Updates für ein Element ändern auch den Wert der Spalte.
* Der Wert dieser Spalte wird bei jeder Einfügung oder Aktualisierung erhöht.
* Abfragen mit den folgenden WHERE- und ORDER BY-Klauseln können effizient ausgeführt werden: `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

#### <a name="usage"></a>Verwendung

Um eine Richtlinie mit oberem Grenzwert anzuwenden, erstellen oder aktualisieren Sie die Datenquelle wie folgt:

```http
    {
        "name" : "[Data source name]",
        "type" : "mysql",
        "credentials" : { "connectionString" : "[connection string]" },
        "container" : { "name" : "[table or view name]" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[last_updated column name]"
      }
    }
```

> [!WARNING]
> Wenn die Quelltabelle keinen Index für die Spalte mit dem oberen Grenzwert enthält, kann das Zeitlimit für die vom MySQL-Indexer verwendeten Abfragen überschritten werden. Insbesondere die Klausel `ORDER BY [High Water Mark Column]` erfordert für die effiziente Ausführung einen Index, wenn die Tabelle viele Zeilen enthält.

### <a name="soft-delete-column-deletion-detection-policy"></a>Richtlinie zum Erkennen von Löschungen anhand der Spalte „Vorläufig löschen“
Wenn Zeilen aus der Quelltabelle gelöscht werden, sollten Sie diese Zeilen auch aus dem Suchindex löschen. Wenn Zeilen physisch aus der Tabelle entfernt werden, gibt es in der kognitiven Azure-Suche keine Möglichkeit, das Vorhandensein von Datensätzen abzuleiten, die nicht mehr vorhanden sind.  Mit der Methode des „vorläufigen Löschens“ können Sie jedoch Zeilen logisch löschen, ohne sie aus der Tabelle zu entfernen. Fügen Sie dazu der Tabelle eine Spalte hinzu, um Zeilen unter Verwendung dieser Spalte anzuzeigen und als gelöscht zu markieren.

Wenn Sie die Methode des "vorläufigen Löschens" verwenden, können Sie die Richtlinie zum Erkennen von Löschungen anhand der "Vorläufig löschen"-Spalte wie folgt beim Erstellen oder Aktualisieren der Datenquelle angeben:

```http
    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }
```

**softDeleteMarkerValue** muss eine Zeichenfolge sein. Verwenden Sie die Zeichenfolgendarstellung des tatsächlichen Werts. Wenn Sie z.B. über eine „integer“-Spalte verfügen, in der gelöschte Zeilen durch den Wert 1 gekennzeichnet sind, verwenden Sie `"1"`. Wenn Sie über eine BIT-Spalte verfügen, in der gelöschte Zeilen durch den booleschen Wert TRUE gekennzeichnet sind, verwenden Sie das Zeichenfolgenliteral `True` oder `true` (die Groß- und Kleinschreibung spielt keine Rolle).

<a name="TypeMapping"></a>

## <a name="mapping-between-mysql-and-azure-cognitive-search-data-types"></a>Zuordnung zwischen MySQL-Datentypen und Azure Cognitive Search-Datentypen

> [!NOTE]
> Die Preview-Version unterstützt keine Geometrietypen und Blobs.

| MySQL-Datentyp | Zulässige Ziel-Index-Feldtypen |
| --- | --- |
| bool, boolean | Edm.Boolean, Edm.String |
| tinyint, smallint, mediumint, int, integer, year | Edm.Int32, Edm.Int64, Edm.String |
| BIGINT | Edm.Int64, Edm.String |
| float, double, real | Edm.Double, Edm.String |
| date, datetime, timestamp | Edm.DateTimeOffset, Edm.String |
| char, varchar, tinytext, mediumtext, text, longtext, enum, set, time | Edm.String |
| Numerische Daten ohne Vorzeichen, serial, decimal, dec, bit, blob, binary, geometry | – |


## <a name="next-steps"></a>Nächste Schritte

Glückwunsch! Sie wissen nun, wie MySQL unter Verwendung eines Indexers in Azure Cognitive Search integriert wird.

+ Weitere Informationen zu Indexern finden Sie unter [Erstellen von Indexern in Azure Cognitive Search](search-howto-create-indexers.md).
