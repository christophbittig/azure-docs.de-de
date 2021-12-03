---
title: Unterstützte Features und Syntax in der Azure Cosmos DB-API für MongoDB (Serverversion 4.0)
description: Hier finden Sie Informationen zu unterstützten Features und zur Syntax der Azure Cosmos DB-API für MongoDB (Serverversion 4.0). Erfahren Sie mehr über die Datenbankbefehle, die Unterstützung der Abfragesprache, Datentypen, Aggregationspipelinebefehle und unterstützte Operatoren.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 08/26/2021
author: gahl-levy
ms.author: gahllevy
ms.openlocfilehash: a5994bdfcdaf1203246c71ecddc2a94f4be5a022
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450056"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-40-server-version-supported-features-and-syntax"></a>Azure Cosmos DB-API für MongoDB (Serverversion 4.0): unterstützte Features und Syntax
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

Azure Cosmos DB ist ein global verteilter Datenbankdienst von Microsoft mit mehreren Modellen. Sie können mit der API für MongoDB von Azure Cosmos DB über einen der Open-Source-MongoDB-Clienttreiber kommunizieren. (Diese Treiber finden Sie [hier](https://docs.mongodb.org/ecosystem/drivers)). Die API für MongoDB von Azure Cosmos DB ermöglicht die Verwendung vorhandener Clienttreiber durch Nutzung des [Wire Protocol](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol) von MongoDB.

Mit der API für MongoDB von Azure Cosmos DB können Sie die Vorteile der vertrauten MongoDB mit allen Unternehmensfunktionen von Cosmos DB kombinieren. Hierzu zählen unter anderem [globale Verteilung](../distribute-data-globally.md), [automatisches Sharding](../partitioning-overview.md), Gewährleistung der Verfügbarkeit und Latenz, Verschlüsselung ruhender Daten sowie Sicherungen.

## <a name="protocol-support"></a>Protokollunterstützung

Die unterstützten Operatoren und alle Einschränkungen oder Ausnahmen sind unten aufgeführt. Alle Clienttreiber, die diese Protokolle verstehen, sollten auch mit der API für MongoDB von Azure Cosmos DB eine Verbindung herstellen können. Bei Verwendung der Azure Cosmos DB-API für MongoDB-Konten, weist der Endpunkt bei der Kontoversion 3.6 und höher das Format `*.mongo.cosmos.azure.com` auf, während der Endpunkt bei der Kontoversion 3.2 das Format `*.documents.azure.com` hat.

> [!NOTE]
> Dieser Artikel enthält nur die unterstützten Serverbefehle und keine clientseitigen Wrapperfunktionen. Für clientseitige Wrapperfunktionen, z. B. `deleteMany()` und `updateMany()`, werden intern die Serverbefehle `delete()` und `update()` genutzt. Funktionen, für die unterstützte Serverbefehle genutzt werden, sind mit der API für MongoDB von Azure Cosmos DB kompatibel.

## <a name="query-language-support"></a>Unterstützung der Abfragesprache

Die API für MongoDB von Azure Cosmos DB bietet umfassende Unterstützung für MongoDB-Abfragesprachkonstrukte. Im Anschluss finden Sie eine detaillierte Aufstellung der aktuell unterstützten Vorgänge, Operatoren, Phasen, Befehle und Optionen:

## <a name="database-commands"></a>Datenbankbefehle

Die API für MongoDB von Azure Cosmos DB unterstützt die folgenden Datenbankbefehle:

### <a name="query-and-write-operation-commands"></a>Befehle für Abfrage- und Schreibvorgänge

| Get-Help | Unterstützt |
|---------|---------|
| [Änderungsdatenströme](change-streams.md) | Ja |
| delete | Ja |
| eval | Nein |
| Suchen | Ja |
| findAndModify | Ja |
| getLastError | Ja |
| getMore | Ja |
| getPrevError | Nein |
| insert | Ja |
| parallelCollectionScan | Nein |
| resetError | Nein |
| aktualisieren | Ja |

### <a name="transaction-commands"></a>Transaktionsbefehle

| Get-Help | Unterstützt |
|---------|---------|
| abortTransaction | Ja |
| commitTransaction | Ja |

### <a name="authentication-commands"></a>Authentifizierungsbefehle

| Get-Help | Unterstützt |
|---------|---------|
| authenticate | Ja |
| getnonce | Ja |
| logout | Ja |

### <a name="administration-commands"></a>Verwaltungsbefehle

| Get-Help | Unterstützt |
|---------|---------|
| cloneCollectionAsCapped | Nein |
| collMod | Nein |
| connectionStatus | Nein |
| convertToCapped | Nein |
| copydb | Nein |
| create | Ja |
| createIndexes | Ja |
| currentOp | Ja |
| drop | Ja |
| dropDatabase | Ja |
| dropIndexes | Ja |
| filemd5 | Ja |
| killCursors | Ja |
| killOp | Nein |
| listCollections | Ja |
| listDatabases | Ja |
| listIndexes | Ja |
| reIndex | Ja |
| renameCollection | Nein |

### <a name="diagnostics-commands"></a>Diagnosebefehle

| Get-Help | Unterstützt |
|---------|---------|
| buildInfo | Ja |
| collStats | Ja |
| connPoolStats | Nein |
| connectionStatus | Nein |
| dataSize | Nein |
| dbHash | Nein |
| dbStats | Ja |
| explain | Ja |
| Features | Nein |
| hostInfo | Ja |
| listDatabases | Ja |
| listCommands | Nein |
| profiler | Nein |
| serverStatus | Nein |
| top | Nein |
| whatsmyuri | Ja |

## <a name="aggregation-pipeline"></a><a name="aggregation-pipeline"></a>Aggregationspipeline

### <a name="aggregation-commands"></a>Aggregationsbefehle

| Get-Help | Unterstützt |
|---------|---------|
| aggregate | Ja |
| count | Ja |
| distinct | Ja |
| mapReduce | Nein |

### <a name="aggregation-stages"></a>Aggregationsphasen

| Get-Help | Unterstützt |
|---------|---------|
| $addFields | Ja |
| $bucket | Nein |
| $bucketAuto | Nein |
| $changeStream | Ja |
| $collStats | Nein |
| $count | Ja |
| $currentOp | Nein |
| $facet | Ja |
| $geoNear | Ja |
| $graphLookup | Ja |
| $group | Ja |
| $indexStats | Nein |
| $limit | Ja |
| $listLocalSessions | Nein |
| $listSessions | Nein |
| $lookup | Teilweise |
| $match | Ja |
| $out | Ja |
| $project | Ja |
| $redact | Ja |
| $replaceRoot | Ja |
| $replaceWith | Nein |
| $sample | Ja |
| $skip | Ja |
| $sort | Ja |
| $sortByCount | Ja |
| $unwind | Ja |

> [!NOTE]
> Von `$lookup` wird das in der Serverversion 3.6 eingeführte Feature für [nicht korrelierte Unterabfragen](https://docs.mongodb.com/manual/reference/operator/aggregation/lookup/#join-conditions-and-uncorrelated-sub-queries) nicht unterstützt. Sie erhalten eine Fehlermeldung mit dem Hinweis `let is not supported`, wenn Sie versuchen, den Operator `$lookup` mit den Feldern `let` und `pipeline` zu verwenden.

### <a name="boolean-expressions"></a>Boolesche Ausdrücke

| Get-Help | Unterstützt |
|---------|---------|
| $and | Ja |
| $not | Ja |
| $or | Ja |

### <a name="conversion-expressions"></a>Konvertierungsausdrücke

| Get-Help | Unterstützt |
|---------|---------|
| $convert | Ja |
| $toBool | Ja |
| $toDate | Ja |
| $toDecimal | Ja |
| $toDouble | Ja |
| $toInt | Ja |
| $toLong | Ja |
| $toObjectId | Ja |
| $toString | Ja |

### <a name="set-expressions"></a>Set-Ausdrücke

| Get-Help | Unterstützt |
|---------|---------|
| $setEquals | Ja |
| $setIntersection | Ja |
| $setUnion | Ja |
| $setDifference | Ja |
| $setIsSubset | Ja |
| $anyElementTrue | Ja |
| $allElementsTrue | Ja |

### <a name="comparison-expressions"></a>Vergleichsausdrücke

> [!NOTE]
> Die API für MongoDB unterstützt keine Vergleichsausdrücke mit einem Arrayliteral in der Abfrage.

| Get-Help | Unterstützt |
|---------|---------|
| $cmp | Ja |
| $eq | Ja | 
| $gt | Ja | 
| $gte | Ja | 
| $lt | Ja |
| $lte | Ja | 
| $ne | Ja | 
| $in | Ja | 
| $nin | Ja | 

### <a name="arithmetic-expressions"></a>Arithmetische Ausdrücke

| Get-Help | Unterstützt |
|---------|---------|
| $abs | Ja |
| $add | Ja |
| $ceil | Ja |
| $divide | Ja |
| $exp | Ja |
| $floor | Ja |
| $ln | Ja |
| $log | Ja |
| $log10 | Ja |
| $mod | Ja |
| $multiply | Ja |
| $pow | Ja |
| $sqrt | Ja |
| $subtract | Ja |
| $trunc | Ja |

### <a name="string-expressions"></a>Zeichenfolgenausdrücke

| Get-Help | Unterstützt |
|---------|---------|
| $concat | Ja |
| $indexOfBytes | Ja |
| $indexOfCP | Ja |
| $ltrim | Ja |
| $rtrim | Ja |
| $trim | Ja |
| $split | Ja |
| $strLenBytes | Ja |
| $strLenCP | Ja |
| $strcasecmp | Ja |
| $substr | Ja |
| $substrBytes | Ja |
| $substrCP | Ja |
| $toLower | Ja |
| $toUpper | Ja |

### <a name="text-search-operator"></a>Operator für die Textsuche

| Get-Help | Unterstützt |
|---------|---------|
| $meta | Nein |

### <a name="array-expressions"></a>Arrayausdrücke

| Get-Help | Unterstützt |
|---------|---------|
| $arrayElemAt | Ja |
| $arrayToObject | Ja |
| $concatArrays | Ja |
| $filter | Ja |
| $indexOfArray | Ja |
| $isArray | Ja |
| $objectToArray | Ja |
| $range | Ja |
| $reverseArray | Ja |
| $reduce | Ja |
| $size | Ja |
| $slice | Ja |
| $zip | Ja |
| $in | Ja |

### <a name="variable-operators"></a>Variablenoperatoren

| Get-Help | Unterstützt |
|---------|---------|
| $map | Ja |
| $let | Ja |

### <a name="system-variables"></a>Systemvariablen

| Get-Help | Unterstützt |
|---------|---------|
| $$CURRENT | Ja |
| $$DESCEND | Ja |
| $$KEEP | Ja |
| $$PRUNE | Ja |
| $$REMOVE | Ja |
| $$ROOT | Ja |

### <a name="literal-operator"></a>Literaloperator

| Get-Help | Unterstützt |
|---------|---------|
| $literal | Ja |

### <a name="date-expressions"></a>Datumsausdrücke

| Get-Help | Unterstützt |
|---------|---------|
| $dayOfYear | Ja |
| $dayOfMonth | Ja |
| $dayOfWeek | Ja |
| $year | Ja |
| $month | Ja | 
| $week | Ja |
| $hour | Ja |
| $minute | Ja | 
| $second | Ja |
| $millisecond | Ja | 
| $dateToString | Ja |
| $isoDayOfWeek | Ja |
| $isoWeek | Ja |
| $dateFromParts | Ja | 
| $dateToParts | Ja |
| $dateFromString | Ja |
| $isoWeekYear | Ja |

### <a name="conditional-expressions"></a>Bedingte Ausdrücke

| Get-Help | Unterstützt |
|---------|---------|
| $cond | Ja |
| $ifNull | Ja |
| $switch | Ja |

### <a name="data-type-operator"></a>Datentypoperator

| Get-Help | Unterstützt |
|---------|---------|
| $type | Ja |

### <a name="accumulator-expressions"></a>Akkumulatorausdrücke

| Get-Help | Unterstützt |
|---------|---------|
| $sum | Ja |
| $avg | Ja |
| $first | Ja |
| $last | Ja |
| $max | Ja |
| $min | Ja |
| $push | Ja |
| $addToSet | Ja |
| $stdDevPop | Ja |
| $stdDevSamp | Ja |

### <a name="merge-operator"></a>Zusammenführungsoperator

| Get-Help | Unterstützt |
|---------|---------|
| $mergeObjects | Ja |

## <a name="data-types"></a>Datentypen

Die Azure Cosmos DB-API for MongoDB unterstützt Dokumente, die im BSON-Format von MongoDB codiert sind. Die API-Version 4.0 verbessert die interne Verwendung dieses Formats, um die Leistung zu verbessern und Kosten zu senken. Dokumente, die über einen Endpunkt mit 4.0 geschrieben oder aktualisiert werden, profitieren davon.
 
In einem [Upgradeszenario](upgrade-mongodb-version.md) profitieren Dokumente, die vor dem Upgrade auf Version 4.0 geschrieben wurden, so lange nicht von der verbesserten Leistung, bis Sie durch einen Schreibvorgang über den 4.0-Endpunkt aktualisiert werden.

| Get-Help | Unterstützt |
|---------|---------|
| Double | Ja |
| String | Ja |
| Object | Ja |
| Array | Ja |
| Binary Data | Ja | 
| ObjectID | Ja |
| Boolean | Ja |
| Date | Ja |
| Null | Ja |
| 32-Bit-Ganzzahl (int) | Ja |
| Timestamp | Ja |
| 64-Bit-Ganzzahl (long) | Ja |
| MinKey | Ja |
| MaxKey | Ja |
| Decimal128 | Ja | 
| Regular Expression | Ja |
| JavaScript | Ja |
| JavaScript (mit Gültigkeitsbereich)| Ja |
| Nicht definiert | Ja |

## <a name="indexes-and-index-properties"></a>Indizes und Indexeigenschaften

### <a name="indexes"></a>Indizes

| Get-Help | Unterstützt |
|---------|---------|
| Einzelfeldindex | Ja |
| Verbundindex | Ja |
| Index mit mehreren Schlüsseln | Ja |
| Textindex | Nein |
| 2dsphere | Ja |
| 2D-Index | Nein |
| Hashindex | Ja |

### <a name="index-properties"></a>Indexeigenschaften

| Get-Help | Unterstützt |
|---------|---------|
| TTL | Ja |
| Eindeutig | Ja |
| Teilweise | Nein |
| Keine Beachtung von Groß-/Kleinschreibung | Nein |
| Platzsparend | Nein |
| Hintergrund | Ja |

## <a name="operators"></a>Operatoren

### <a name="logical-operators"></a>Logische Operatoren

| Get-Help | Unterstützt |
|---------|---------|
| $or | Ja |
| $and | Ja |
| $not | Ja |
| $nor | Ja | 

### <a name="element-operators"></a>Elementoperatoren

| Get-Help | Unterstützt |
|---------|---------|
| $exists | Ja |
| $type | Ja |

### <a name="evaluation-query-operators"></a>Abfrageoperatoren für die Auswertung

| Get-Help | Unterstützt |
|---------|---------|
| $expr | Nein |
| $jsonSchema | Nein |
| $mod | Ja |
| $regex | Ja |
| $text | Nein (Nicht unterstützt. Verwenden Sie stattdessen „$regex“.)| 
| $where | Nein | 

In $regex-Abfragen ermöglichen linksverankerte Ausdrücke eine Indexsuche. Die Verwendung des „i“-Modifizierers (keine Berücksichtigung der Groß-/Kleinschreibung) und des „m“-Modifizierers (mehrere Zeilen) führt jedoch zur Sammlungsüberprüfung in allen Ausdrücken.

Wenn „$“ oder „|“ eingeschlossen werden muss, empfiehlt es sich, zwei (oder mehr) RegEx-Abfragen zu erstellen. Die folgende ursprüngliche Abfrage `find({x:{$regex: /^abc$/})` muss beispielsweise wie folgt geändert werden:

`find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})`

Der erste Teil verwendet den Index zum Einschränken der Suche auf die Dokumente, die mit „^abc“ beginnen, und der zweite Teil stimmt die exakten Einträge ab. Der Strichoperator „|“ dient als „oder“-Funktion: Die Abfrage `find({x:{$regex: /^abc |^def/})` stimmt die Dokumente ab, in denen das Feld „x“ Werte enthält, die mit „abc“ oder „def“ beginnen. Zur Nutzung des Index wird empfohlen, die Abfrage in zwei unterschiedliche Abfragen zu unterteilen, die durch den „$or“-Operator verbunden sind: `find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })`.

### <a name="array-operators"></a>Arrayoperatoren

| Get-Help | Unterstützt | 
|---------|---------|
| $all | Ja | 
| $elemMatch | Ja | 
| $size | Ja | 

### <a name="comment-operator"></a>Kommentaroperator

| Get-Help | Unterstützt | 
|---------|---------|
| $comment | Ja | 

### <a name="projection-operators"></a>Projektionsoperatoren

| Get-Help | Unterstützt |
|---------|---------|
| $elemMatch | Ja |
| $meta | Nein |
| $slice | Ja |

### <a name="update-operators"></a>Aktualisierungsoperatoren

#### <a name="field-update-operators"></a>Operatoren für die Feldaktualisierung

| Get-Help | Unterstützt |
|---------|---------|
| $inc | Ja |
| $mul | Ja |
| $rename | Ja |
| $setOnInsert | Ja |
| $set | Ja |
| $unset | Ja |
| $min | Ja |
| $max | Ja |
| $currentDate | Ja |

#### <a name="array-update-operators"></a>Operatoren für die Arrayaktualisierung

| Get-Help | Unterstützt |
|---------|---------|
| $ | Ja |
| $[]| Ja |
| $[\<identifier\>]| Ja |
| $addToSet | Ja |
| $pop | Ja |
| $pullAll | Ja |
| $pull | Ja |
| $push | Ja |
| $pushAll | Ja |

#### <a name="update-modifiers"></a>Aktualisierungsmodifizierer

| Get-Help | Unterstützt |
|---------|---------|
| $each | Ja |
| $slice | Ja |
| $sort | Ja |
| $position | Ja |

#### <a name="bitwise-update-operator"></a>Bitweiser Updateoperator

| Get-Help | Unterstützt |
|---------|---------|
| $bit | Ja | 
| $bitsAllSet | Nein |
| $bitsAnySet | Nein |
| $bitsAllClear | Nein |
| $bitsAnyClear | Nein |

### <a name="geospatial-operators"></a>Räumliche Operatoren

Operator | Unterstützt | 
--- | --- |
$geoWithin | Ja |
$geoIntersects | Ja | 
$near | Ja |
$nearSphere | Ja |
$geometry | Ja |
$minDistance | Ja |
$maxDistance | Ja |
$center | Nein |
$centerSphere | Nein |
$box | Nein |
$polygon | Nein |

## <a name="sort-operations"></a>Sortiervorgänge

Bei Verwendung des `findOneAndUpdate`-Vorgangs werden Sortiervorgänge für ein einzelnes Feld unterstützt, Sortiervorgänge für mehrere Felder jedoch nicht.

## <a name="indexing"></a>Indizierung
Die API für MongoDB [unterstützt eine Vielzahl von Indizes](mongodb-indexing.md), um die Sortierung nach mehreren Feldern zu ermöglichen, die Abfrageleistung zu verbessern und Eindeutigkeit zu erzwingen.

## <a name="gridfs"></a>GridFS

Azure Cosmos DB unterstützt GridFS über jeden GridFS-kompatiblen Mongo-Treiber.

## <a name="replication"></a>Replikation

Azure Cosmos DB unterstützt die automatische, native Replikation auf den niedrigsten Ebenen. Diese Logik wird erweitert, um auch die globale Replikation mit geringer Latenz zu erreichen. Cosmos DB unterstützt keine manuellen Replikationsbefehle.

## <a name="retryable-writes"></a>Wiederholbare Schreibvorgänge

Cosmos DB unterstützt noch keine wiederholbaren Schreibvorgänge. Bei Clienttreibern muss der URL-Parameter „retryWrites=false“ zur Verbindungszeichenfolge hinzugefügt werden. Weitere URL-Parameter können hinzugefügt werden, indem ihnen das Präfix „&“ vorangestellt wird. 

## <a name="sharding"></a>Sharding (Horizontales Partitionieren)

Azure Cosmos DB unterstützt das automatische, serverseitige Sharding. Die Erstellung, die Platzierung und der Ausgleich von Shards wird automatisch verwaltet. Azure Cosmos DB unterstützt keine manuellen Shardingbefehle. Das bedeutet, dass Sie keine Befehle wie „addShard“, „balancerStart“, „moveChunk“ usw. aufrufen müssen. Sie müssen beim Erstellen der Container oder beim Abfragen der Daten nur den Shardschlüssel angeben.

## <a name="sessions"></a>Sitzungen

Für Azure Cosmos DB werden noch keine serverseitigen Sitzungsbefehle unterstützt.

## <a name="time-to-live-ttl"></a>Gültigkeitsdauer (TTL)

Azure Cosmos DB unterstützt eine Gültigkeitsdauer (Time-to-live, TTL) basierend auf dem Zeitstempel des Dokuments. TTL kann für Sammlungen über das [Azure-Portal](https://portal.azure.com) aktiviert werden.

## <a name="transactions"></a>Transaktionen

Transaktionen mit mehreren Dokumenten werden in einer Sammlung ohne Shards unterstützt. Transaktionen mit mehreren Dokumenten werden nicht sammlungsübergreifend oder in Sammlungen mit Shards unterstützt. Das Timeout für Transaktionen ist auf fünf Sekunden festgelegt.

## <a name="user-and-role-management"></a>Benutzer- und Rollenverwaltung

Azure Cosmos DB unterstützt noch keine Benutzer und Rollen. Cosmos DB unterstützt jedoch die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC) sowie Lese-/Schreibkennwörter/-schlüssel und Schreibschutzkennwörter/-schlüssel, die über das [Azure-Portal](https://portal.azure.com) (Seite „Verbindungszeichenfolge“) abgerufen werden können.

## <a name="write-concern"></a>Schreibbestätigung

Einige Anwendungen unterstützen eine [Schreibbestätigung](https://docs.mongodb.com/manual/reference/write-concern/). Diese gibt die Anzahl der Antworten an, die während eines Schreibvorgangs erforderlich sind. Aufgrund der Art und Weise, in der Cosmos DB die Replikation im Hintergrund durchführt, gilt für alle Schreibvorgänge automatisch und standardmäßig ein Quorum. Alle Schreibvorgänge, die durch den Clientcode angegeben werden, werden ignoriert. Weitere Informationen finden Sie unter [Verwenden von Konsistenzebenen zum Maximieren der Verfügbarkeit und Leistung](../consistency-levels.md).

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [Studio 3T](connect-using-mongochef.md) mit der API für MongoDB von Azure Cosmos DB verwenden.
- Erfahren Sie, wie Sie [Robo 3T](connect-using-robomongo.md) mit der API für MongoDB von Azure Cosmos DB verwenden.
- Untersuchen Sie MongoDB-[Beispiele](nodejs-console-app.md) mit der API für MongoDB von Azure Cosmos DB.
- Versuchen Sie, die Kapazitätsplanung für eine Migration zu Azure Cosmos DB durchzuführen? Sie können Informationen zu Ihrem vorhandenen Datenbankcluster für die Kapazitätsplanung verwenden.
    - Wenn Sie nur die Anzahl der virtuellen Kerne und Server in Ihrem vorhandenen Datenbankcluster kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](../convert-vcore-to-request-unit.md) 
    - Wenn Sie die typischen Anforderungsraten für Ihre aktuelle Datenbankworkload kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mit dem Azure Cosmos DB-Kapazitätsplaner](estimate-ru-capacity-planner.md)
