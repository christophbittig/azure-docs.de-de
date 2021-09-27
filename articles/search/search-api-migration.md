---
title: Aktualisieren von REST-API-Versionen
titleSuffix: Azure Cognitive Search
description: Überprüfen Sie die Unterschiede in den API-Versionen, und erfahren Sie, welche Aktionen erforderlich sind, um vorhandenen Code zur neuesten Version der REST-API für den Dienst für die kognitive Azure-Suche zu migrieren.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/16/2021
ms.openlocfilehash: d925b4542a5d03340558f302a2251b4c191de2e0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601290"
---
# <a name="upgrade-to-the-latest-rest-api-in-azure-cognitive-search"></a>Ausführen eines Upgrades auf die aktuelle Version der REST-API in Azure Cognitive Search

Wenn Sie eine frühere Version der [**Search-REST-API**](/rest/api/searchservice/) verwenden, unterstützt dieser Artikel Sie beim Upgrade Ihrer Anwendung auf die neueste allgemein verfügbare API-Version **2020-06-30**.

Version 2020-06-30 enthält ein wichtiges neues Feature ([Wissensspeicher](knowledge-store-concept-intro.md)) und führt einige geringfügige Behavior Changes ein. Daher ist diese Version größtenteils abwärtskompatibel, sodass nur minimale Codeänderungen erforderlich sein sollten, wenn Sie ein Upgrade von der vorherigen Version (2019-05-06) ausführen.

> [!NOTE]
> Eine Suchdienst unterstützt eine Reihe von REST-API-Versionen, einschließlich früherer Versionen. Sie können diese API-Versionen weiterhin verwenden, es wird jedoch empfohlen, den Code zur neuesten Version zu migrieren, damit Sie auf neue Funktionen zugreifen können. Im Lauf der Zeit werden die alten Versionen der REST-API als veraltet markiert und [nicht mehr unterstützt](search-api-versions.md#unsupported-versions).

<a name="UpgradeSteps"></a>

## <a name="how-to-upgrade"></a>Aktualisieren

Beim Upgrade auf eine neue Version müssen Sie wahrscheinlich keine Änderungen an Ihrem Code vornehmen, außer die Versionsnummer zu ändern. Nur in folgenden Situationen müssen Sie möglicherweise Code ändern:

* Der Code erzeugt Fehler, wenn nicht erkannte Eigenschaften in einer API-Antwort zurückgegeben werden. Standardmäßig sollte die Anwendung Eigenschaften ignorieren, die sie nicht versteht.

* Ihr Code behält API-Anforderungen bei und versucht, sie erneut an die neue API-Version zu senden. Dies kann beispielsweise vorkommen, wenn Ihre Anwendung Fortsetzungstoken beibehält, die von der Search-API zurückgegeben wurden (weitere Informationen finden Sie, indem Sie in der [Search-API-Referenz](/rest/api/searchservice/Search-Documents) nach `@search.nextPageParameters` suchen).

* Ihr Code verweist auf eine API-Version vor 2019-05-06 und ist von mindestens einem Breaking Change betroffen, der mit dieser Version eingeführt wurde. Weitere Details finden Sie im Abschnitt [Upgrade auf 2019-05-06](#upgrade-to-2019-05-06). 

Wenn Sie von einer dieser Situationen betroffen sind, müssen Sie Ihren Code möglicherweise entsprechend ändern. Darüber hinaus sollten keine Änderungen erforderlich sein, es sei denn, Sie möchten Features verwenden, die in der neuen Version hinzugefügt wurden.

## <a name="upgrade-to-2020-06-30"></a>Upgrade auf 2020-06-30

Version 2020-06-30 ist das neueste allgemein verfügbare Release der REST-API. Es gibt einen Breaking Change und mehrere Abweichungen beim Verhalten. 

Zu den Features, die in dieser API-Version jetzt allgemein verfügbar sind, gehören:

* [Wissensspeicher](knowledge-store-concept-intro.md), dauerhafte Speicherung von angereicherten Inhalten, die durch Skillsets für die Downstreamanalyse und die Verarbeitung durch andere Anwendungen erstellt wurden. Mithilfe dieser Funktion kann eine indexergesteuerte KI-Anreicherungspipeline einen Wissensspeicher zusätzlich zu einem Suchindex auffüllen. Falls Sie die Vorschauversion dieses Features verwendet haben: sie ist zur allgemein verfügbaren Version gleichwertig. Die einzige erforderliche Codeänderung besteht im Ändern der API-Version.

### <a name="breaking-change"></a>Unterbrechende Änderung

Vorhandener Code, der für frühere API-Versionen geschrieben wurde, wird ab API-Version 2020-06-30 unterbrochen, wenn der Code folgende Funktionen enthält:

* Alle Edm.Date-Literale (ein Datum bestehend aus Jahr-Monat-Tag, z. B. `2020-12-12`) in Filterausdrücken muss dem Format von Edm.DateTimeOffset folgen: `2020-12-12T00:00:00Z`. Diese Änderung war erforderlich, um fehlerhafte oder unerwartete Abfrageergebnisse aufgrund von Zeitzonenunterschieden zu behandeln.

### <a name="behavior-changes"></a>Verhaltensänderungen

* Der [BM25-Ähnlichkeitsalgorithmus für die Rangfolge](index-ranking-similarity.md) ersetzt den bisherigen Rangfolgealgorithmus durch neuere Technologie. Neue Dienste verwenden diesen Algorithmus automatisch. Für vorhandene Dienste müssen Sie Parameter festlegen, um den neuen Algorithmus zu verwenden.

* Die Behandlung von Nullwerten in sortierten Ergebnissen hat sich in dieser Version geändert, Nullwerte werden bei `asc`-Sortierung zuerst und bei `desc`-Sortierung zuletzt angezeigt. Wenn Sie Code zur Behandlung von Nullwerten geschrieben haben, seien Sie sich dieser Änderung bewusst.

## <a name="upgrade-to-2019-05-06"></a>Upgrade auf 2019-05-06

Version 2019-05-06 ist der Vorgänger des neuesten allgemein verfügbaren Releases der REST-API. Zu den Features, die in dieser API-Version allgemein verfügbar wurden, gehören:

* [AutoVervollständigen](index-add-suggesters.md) ist eine Funktion zur Textvervollständigung, mit der ein teilweise eingegebener Begriff vervollständigt wird.
* [Komplexe Typen](search-howto-complex-data-types.md) bieten native Unterstützung für strukturierte Objektdaten in einem Suchindex.
* Der [Analysemodus JsonLines](search-howto-index-json-blobs.md) ist Teil der Azure-Blobindizierung. Dabei wird ein Suchdokument pro JSON-Entität erstellt, die durch eine neue Zeile getrennt ist.
* Die [KI-Anreicherung](cognitive-search-concept-intro.md) ermöglicht eine Indizierung, bei der die Engines zur KI-Anreicherung von Cognitive Services genutzt werden.

### <a name="breaking-changes"></a>Aktuelle Änderungen

Vorhandener Code, der für frühere API-Versionen geschrieben wurde, wird ab API-Version 2019-05-06 unterbrochen, wenn der Code folgende Funktionen enthält:

#### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Indexer für Azure Cosmos DB: Datenquelle ist jetzt „type“: „cosmosdb“

Wenn Sie einen [Cosmos DB-Indexer](search-howto-index-cosmosdb.md ) verwenden, müssen Sie `"type": "documentdb"` in `"type": "cosmosdb"` ändern.

#### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Fehler in Indexer-Ausführungsergebnissen weisen keinen Status mehr auf

Die Fehlerstruktur für die Indexer-Ausführung enthielt bisher ein `status`-Element. Dieses Element wurde entfernt, da es keine nützlichen Informationen enthielt.

#### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>Die Datenquellen-API des Indexers gibt keine Verbindungszeichenfolgen mehr zurück

Ab den API-Versionen 2019-05-06 und 2019-05-06-Preview gibt die Datenquellen-API als Antwort auf einen REST-Vorgang keine Verbindungszeichenfolgen mehr zurück. In früheren API-Versionen wurde in der kognitiven Azure-Suche für mithilfe der POST-Methode erstellte Datenquellen **201** zurückgegeben, gefolgt von der OData-Antwort, die die Verbindungszeichenfolge im Textformat enthielt.

#### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>Kognitive Qualifikation „Benannte Entität erkennen“ wird nicht mehr verwendet

Wenn Sie die Qualifikation [Benannte Entität erkennen](cognitive-search-skill-named-entity-recognition.md) im Code aufgerufen haben, treten beim Aufruf Fehler auf. Die Ersatzfunktion ist [Skill „Entitätserkennung“ (V3)](cognitive-search-skill-entity-recognition-v3.md). Führen Sie unter Berücksichtigung der Empfehlungen unter [Veraltete Qualifikationen für die kognitive Suche](cognitive-search-skill-deprecated.md) eine Migration zu einer unterstützten Qualifikation durch.

### <a name="upgrading-complex-types"></a>Aktualisieren von komplexen Typen

In API-Version 2019-05-06 wurde förmliche Unterstützung für komplexe Typen hinzugefügt. Wenn in Ihrem Code frühere Empfehlungen für Äquivalenz zu komplexen Typen in 2017-11-11-Preview oder 2016-09-01-Preview implementiert sind, gelten ab Version 2019-05-06 neue und geänderte Grenzwerte, die Sie berücksichtigen müssen:

+ Die Limits für die Tiefe der Unterfelder und die Anzahl der komplexen Sammlungen pro Index wurden gesenkt. Wenn Sie mit den API-Vorschauversionen Indizes erstellt haben, die diese Limits überschreiten, treten beim Aktualisieren oder Neuerstellen der Indizes mit API-Version 2019-05-06 Fehler auf. Wenn dies bei Ihnen der Fall ist, müssen Sie Ihr Schema entsprechend den neuen Limits ändern und dann den Index neu erstellen.

+ Ab API-Version 2019-05-06 gilt ein neuer Grenzwert für die Anzahl der Elemente von komplexen Sammlungen pro Dokument. Wenn Sie mit den API-Vorschauversionen Indizes mit Dokumenten erstellt haben, die diese Limits überschreiten, treten beim Neuindizieren dieser Daten mit API-Version 2019-05-06 Fehler auf. Wenn dies bei Ihnen der Fall ist, müssen Sie die Anzahl der Elemente von komplexen Sammlungen pro Dokument verringern, bevor Sie die Daten neu indizieren.

Weitere Informationen finden Sie unter [Grenzwerte für den Dienst für die kognitive Azure-Suche](search-limits-quotas-capacity.md).

#### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Aktualisieren einer älteren Struktur mit komplexen Typen

Wenn im Code komplexe Typen mit einer der älteren API-Vorschauversionen verwendet werden, verwenden Sie möglicherweise ein Indexdefinitionsformat, das wie folgt aussieht:

```json
{
  "name": "hotels",  
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.microsoft" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.microsoft" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true, "analyzer": "tagsAnalyzer" },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true },
    { "name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType" },
    { "name": "Address/StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
    { "name": "Address/City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)" }, 
    { "name": "Rooms/Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene" },
    { "name": "Rooms/Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "Rooms/Type", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
    { "name": "Rooms/BedOptions", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "Rooms/SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Rooms/Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "facetable": true, "analyzer": "tagsAnalyzer" }
  ]
}  
```

In API-Version 2017-11-11-Preview wurde ein neueres strukturähnliches Format zum Definieren von Indexfeldern eingeführt. In dem neuen Formular enthält jedes komplexe Feld eine Feldsammlung, in der die Unterfelder definiert sind. In API-Version 2019-05-06 wird ausschließlich dieses neue Format verwendet, sodass beim Erstellen oder Aktualisieren eines Index mit dem alten Format Fehler auftreten. Wenn Sie Indizes mit dem alten Format erstellt haben, müssen Sie sie mithilfe der API-Version 2017-11-11-Preview in das neue Format ändern, damit sie mit API-Version 2019-05-06 verwaltet werden können.

Mit den folgenden Schritten können Sie „flache“ Indizes mithilfe der API-Version 2017-11-11-Preview in das neue Format ändern:

1. Führen Sie eine GET-Anforderung aus, um den Index abzurufen. Wenn dieser bereits im neuen Format vorliegt, ist kein weiterer Schritt erforderlich.

2. Wandeln Sie den Index vom „flachen“ Format in das neue Format um. Dafür müssen Sie Code schreiben, da zum Zeitpunkt der Erstellung dieses Artikels kein Beispielcode vorhanden ist.

3. Führen Sie eine PUT-Anforderung aus, um den Index auf das neue Format zu aktualisieren. Stellen Sie sicher, dass keine anderen Details des Index geändert werden, z. B. die Durchsuchbarkeit oder Filterfunktion von Feldern, da dies mit der API zum Aktualisieren von Indizes nicht zulässig ist.

> [!NOTE]
> Indizes, die mit dem alten „flachen“ Format erstellt wurden, können über das Azure-Portal nicht verwaltet werden. Ändern Sie Ihre Indizes schnellstmöglich von der „flachen“ Darstellung in die Darstellung in „Strukturform“.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die Referenzdokumentation für die Search-REST-API an. Wenn Probleme auftreten, können Sie sich über [Stack Overflow](https://stackoverflow.com/) an uns wenden oder [den Support kontaktieren](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Referenz zur REST-API für den Azure Search-Dienst](/rest/api/searchservice/)
