---
title: Optimierung der Schreibleistung in der Azure Cosmos DB-API für MongoDB
description: In diesem Artikel wird beschrieben, wie die Schreibleistung in der Azure Cosmos DB-API für MongoDB optimiert werden kann, um den größtmöglichen Durchsatz bei möglichst geringen Kosten zu erzielen.
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
ms.author: gahllevy
ms.openlocfilehash: 2e04953e766c76275079731751cb006fe46712e7
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039551"
---
# <a name="optimize-write-performance-in-azure-cosmos-db-api-for-mongodb"></a>Optimierung der Schreibleistung in der Azure Cosmos DB-API für MongoDB
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

Die Optimierung der Schreibleistung hilft Ihnen dabei, die unbegrenzte Skalierbarkeit der Azure Cosmos DB API für MongoDB voll auszuschöpfen. Im Gegensatz zu anderen verwalteten MongoDB-Diensten werden Ihre Sammlungen (wenn partitionierte Sammlungen verwendet werden) von der API für MongoDB automatisch und transparent partitioniert, sodass sie unbegrenzt skalierbar sind. 

Berücksichtigen Sie dies bei der Art, wie Sie Daten schreiben, indem Sie Daten parallelisieren und auf Partitionen verteilen, um die meisten Schreibvorgänge aus Ihren Datenbanken und Sammlungen herauszuholen. Dieser Artikel erklärt bewährte Methoden, um die Schreibleistung zu optimieren.

## <a name="spread-the-load-across-your-shards"></a>Lastverteilung auf Ihre Partitionen
Wenn Sie Daten auf eine partitionierte API für die MongoDB-Sammlung schreiben, werden Ihre Daten in kleine Segmente geteilt (partitioniert) und auf jede Partition geschrieben, die auf den Wert Ihres Blockschlüsselfeldes basiert. Jedes Segment ist ein kleines Stück eines virtuellen Computers, der nur Dokumente mit einem eindeutigen Blockschlüsselwert speichert. 

Wenn Ihre Anwendung eine große Datenmenge auf eine einzige Partition schreibt, ist das nicht effizient, weil die App den Durchsatz einer Partition ausschöpfen würde, statt die Last auf all Ihre Partitionen zu verteilen. Ihre Schreiblast wird gleichmäßig auf Ihre Sammlung verteilt, indem parallel auf viele Dokumente mit eindeutigen Blockschlüsselwerten geschrieben wird.

Ein Beispiel hierfür wäre eine Produktkataloganwendung, die nach dem Kategoriefeld partitioniert ist. Statt jeweils in eine Kategorie (Partition) zu schreiben, ist es besser, in alle Kategorien gleichzeitig zu schreiben, um einen maximalen Schreibdurchsatz zu erreichen. 

## <a name="reduce-the-number-of-indexes"></a>Reduzierung der Indexanzahl
Die [Indizierung](../mongodb-indexing.md) ist ein nützliches Feature, mit dem Daten deutlich schneller abgefragt werden können. Für die flexibelste Abfrageleistung ermöglicht die API für MongoDB standardmäßig einen Platzhalterindex für Ihre Daten, um die Abfrage nach allen Feldern schnell auszuführen. Dennoch führen alle Indizes, einschließlich der Platzhalterindizes, beim Schreiben von Daten zu einer zusätzlichen Last, da die Daten beim Schreiben die Sammlung und die Indizes verändern. 

Die Reduzierung der Indexanzahl auf Indizes, die Ihre Abfragen unterstützen, macht das Schreiben schneller und günstiger. Allgemein wird Folgendes empfohlen:

* Für jedes Feld, nach dem Sie filtern, sollte es einen entsprechenden Einzelfeldindex geben. Diese Option ermöglicht auch Multifeldfilterung.
* Jede Feldgruppe, die Sie filtern, sollte einen zusammengesetzten Index für diese Gruppe besitzen. 

## <a name="set-ordered-to-false-in-the-mongodb-drivers"></a>Festlegen von „ordered“ auf „false“ in MongoDB-Treibern
Die MongoDB-Treiber legen beim Schreiben standardmäßig die Option „ordered“ auf „true“ fest, wodurch nacheinander in der festgelegten Reihenfolge in jedes Dokument geschrieben wird. Diese Option reduziert die Schreibleistung, da jede Schreibanforderung wartet, bis die vorherige abgeschlossen ist. Legen Sie beim Schreiben von Daten diese Option auf „false“ fest, um die Leistung zu verbessern. 

```JavaScript
db.collection.insertMany(
   [ <doc1> , <doc2>, ... ],
   {
      ordered: false
   }
)
```

## <a name="tune-for-the-optimal-batch-size-and-thread-count"></a>Optimieren einer optimalen Batchgröße und Threadanzahl
Die Parallelisierung der Schreibvorgänge über mehrere Threads/Prozesse ist der Schlüssel zum skalierten Schreiben. Die API für MongoDB nimmt Schreibvorgänge in Batches von bis zu 1.000 Dokumenten je Prozess/Thread an. 

Wenn Sie mehr als 1.000 Dokumente je Prozess/Thread am Stück schreiben, dann sollten Clientfunktionen wie `insertMany()` auf ungefähr 1.000 Dokumente beschränkt werden. Ansonsten wartet der Client, bis jeder Batch übertragen wurde, bevor er mit dem nächsten Batch fortfährt. In manchen Fällen ist die Aufteilung der Batches mit weniger oder etwas mehr als 1.000 Dokumenten schneller.



## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [Indizierung in der API für MongoDB](../mongodb-indexing.md).
* Lesen Sie mehr über das [Sharding und Partitionieren in Azure Cosmos DB](../partitioning-overview.md).
* Lesen Sie mehr über die [Behandlung häufiger Probleme](error-codes-solutions.md).
* Versuchen Sie, die Kapazitätsplanung für eine Migration zu Azure Cosmos DB durchzuführen? Sie können Informationen zu Ihrem vorhandenen Datenbankcluster für die Kapazitätsplanung verwenden.
    * Wenn Sie nur die Anzahl der virtuellen Kerne und Server in Ihrem vorhandenen Datenbankcluster kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](../convert-vcore-to-request-unit.md) 
    * Wenn Sie die typischen Anforderungsraten für Ihre aktuelle Datenbank-Workload kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mit dem Azure Cosmos DB-Kapazitätsplaner](estimate-ru-capacity-planner.md)
