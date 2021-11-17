---
title: Überwachen und Debuggen mit Insights in Azure Cosmos DB
description: Verwenden Sie Metriken in Azure Cosmos DB zum Beheben häufiger Probleme und Überwachen der Datenbank.
ms.author: esarroyo
author: StefArroyo
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 11/08/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: ba9fac9fef3e418cc48bc3185ca91af89dd7946f
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057060"
---
# <a name="monitor-and-debug-with-insights-in-azure-cosmos-db"></a>Überwachen und Debuggen mit Insights in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB bietet Insights für Durchsatz, Speicher, Konsistenz, Verfügbarkeit und Latenz. Das Azure-Portal bietet eine aggregierte Ansicht dieser Metriken. Sie können Azure Cosmos DB-Metriken auch über die Azure Monitor-API anzeigen. Bei den Dimensionswerten für die Metriken, z. B. dem Containernamen, wird die Groß-/Kleinschreibung nicht berücksichtigt. Daher müssen Sie bei Zeichenfolgenvergleichen für diese Dimensionswerte Vergleiche verwenden, bei denen die Groß-/Kleinschreibung nicht berücksichtigt wird. Informationen zum Anzeigen von Metriken von Azure Monitor finden Sie im Artikel [Abrufen von Metriken von Azure Monitor](./monitor-cosmos-db.md).

Dieser Artikel behandelt häufige Anwendungsfälle und zeigt, wie Azure Cosmos DB-Insights zum Analysieren und Beheben der Probleme verwendet werden können. Standardmäßig werden die Metrikinsights alle fünf Minuten gesammelt und sieben Tage lang aufbewahrt.

## <a name="view-insights-from-azure-portal"></a>Anzeigen von Insights über das Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und navigieren Sie zu Ihrem Azure Cosmos DB-Konto.

1. Sie können Ihre Kontometriken entweder im Bereich **Metriken** oder im Bereich **Insights** einsehen.

   * **Metriken:** Dieser Bereich bietet numerische Metriken, die in regelmäßigen Abständen erfasst werden und einen Aspekt eines Systems zu einem bestimmten Zeitpunkt beschreiben. So können Sie beispielsweise die [serverseitige Latenzmetrik](monitor-server-side-latency.md), die [normalisierte Metrik zur Nutzung von Anfrageeinheiten](monitor-normalized-request-units.md) usw. anzeigen und überwachen.

   * **Insights:** Dieser Bereich bietet eine angepasste Überwachungsbenutzeroberfläche für Azure Cosmos DB. Sie verwenden die gleichen Metriken und Protokolle, die in Azure Monitor gesammelt werden, und zeigen eine aggregierte Ansicht für Ihr Konto an.

1. Öffnen Sie den Bereich **Insights**. Standardmäßig werden im Bereich „Insights“ die Metriken für Durchsatz, Anforderungen, Speicher, Verfügbarkeit, Latenz, System und Kontoverwaltung für alle Container in Ihrem Konto angezeigt. Sie können **Zeitbereiche**, **Datenbanken** und **Container** auswählen, für die Sie Insights anzeigen möchten. Auf der Registerkarte **Übersicht** werden die RU/s-Nutzung, Datennutzung, Indexnutzung, gedrosselte Anforderungen und der normalisierte RU/s-Verbrauch für die ausgewählte Datenbank und den Container angezeigt.

   :::image type="content" source="./media/use-metrics/performance-metrics.png" alt-text="Cosmos DB-Leistungsmetriken im Azure-Portal" lightbox="./media/use-metrics/performance-metrics.png" :::

1. Die folgenden Metriken sind im Bereich **Insights** verfügbar:

   * **Durchsatz**: Diese Registerkarte zeigt die gesamte Anzahl der genutzten oder fehlerhaften angeforderten Einheiten an (Antwortcode 429), da der Durchsatz oder die bereitgestellte Speicherkapazität für den Container überschritten wurde.

   * **Anforderungen**: Auf dieser Registerkarte werden die Gesamtzahl der vom Statuscode verarbeiteten Anforderungen nach Vorgangstyp und die Anzahl der fehlerhaften Anforderungen (Antwortcode 429) angezeigt. Anforderungen sind fehlerhaft, wenn der für den Container bereitgestellte Durchsatz oder die bereitgestellte Speicherkapazität überschritten wird.

   * **Speicher**: Auf dieser Registerkarte werden die Größe der Daten und die Indexnutzung im ausgewählten Zeitraum angezeigt.

   * **Verfügbarkeit**: Diese Registerkarte zeigt den Prozentsatz erfolgreicher Anforderungen der Gesamtzahl der Anforderungen pro Stunde an. Die Erfolgsrate wird durch Azure Cosmos DB-SLAs definiert.

   * **Latenz**: Diese Registerkarte zeigt die Wartezeit beim Lesen und Schreiben an, die von Azure Cosmos DB in der Region, in der Ihr Konto ausgeführt wird, gemessen wird. Sie können die regionsübergreifende Wartezeit für ein georepliziertes Konto visualisieren. Sie können die serverseitige Latenz auch durch verschiedene Vorgänge darstellen. Diese Metrik stellt nicht die End-to-End-Anforderungswartezeit dar.

   * **System**: Diese Registerkarte zeigt an, wie viele Metadatenanforderungen von der primären Partition erfüllt werden. Diese hilft auch beim Identifizieren der gedrosselten Anforderungen.

   * **Kontoverwaltung**: Auf dieser Registerkarte werden die Metriken für Kontoverwaltungsaktivitäten wie Kontoerstellung, Löschung, Schlüsselupdates, Netzwerk und Replikationseinstellungen angezeigt.

In den folgenden Abschnitten werden allgemeine Szenarios erläutert, in denen Sie Azure Cosmos DB-Metriken verwenden können.

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>Ermitteln der Anzahl von Anforderungen, die erfolgreich ausgeführt werden oder Fehler verursachen

Rufen Sie zunächst das [Azure-Portal](https://portal.azure.com) auf, und navigieren Sie zum Blatt **Insights**. Öffnen Sie auf diesem Blatt die Registerkarte **Anforderungen**. Dort wird ein Diagramm mit der Gesamtzahl der Anforderungen nach Statuscode und Vorgangstyp segmentiert angezeigt. Weitere Informationen zu HTTP-Statuscodes finden Sie unter [HTTP Status Codes for Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) (HTTP-Statuscodes für Azure Cosmos DB).

Der häufigste Fehlerstatuscode ist 429 (Ratenbegrenzung/Drosselung). Dieser Fehler besagt, dass Anforderungen an Azure Cosmos DB den bereitgestellten Durchsatz überschreiten. Die gängigste Lösung für dieses Problem ist das [Hochskalieren der Anforderungseinheiten](./set-throughput.md) (Request Units, RUs) für die angegebene Sammlung.

:::image type="content" source="media/use-metrics/request-count.png" alt-text="Anzahl von Anforderungen pro Minute" lightbox= "media/use-metrics/request-count.png":::

## <a name="determine-the-throughput-consumption-by-a-partition-key-range"></a>Bestimmen des Durchsatzverbrauchs nach Partitionsschlüsselbereich

Für jede skalierbare Anwendung ist eine gute Kardinalität der Partitionsschlüssel von wesentlicher Bedeutung. Um die nach Partitionsschlüssel-Bereichs-IDs aufgeschlüsselte Verteilung des Durchsatzes jedes partitionierten Containers zu bestimmen, navigieren Sie zum Bereich **Insights**. Wenn Sie die Registerkarte **Durchsatz** öffnen, wird der normalisierte RU/s-Verbrauch für verschiedene Partitionsschlüsselbereiche im Diagramm angezeigt.

:::image type="content" source="media/use-metrics/throughput-consumption-partition-key-range.png" alt-text="Normalisierter Durchsatzverbrauch nach Partitionsschlüsselbereich-IDs" lightbox="media/use-metrics/throughput-consumption-partition-key-range.png":::

Mithilfe dieses Diagramms können Sie ermitteln, ob eine heiße Partition vorhanden ist. Eine ungleichmäßige Verteilung des Durchsatzes kann *Hot*-Partitionen verursachen, die zu gedrosselten Anforderungen führen können und möglicherweise eine Neupartitionierung erfordern. Nachdem Sie den Partitionsschlüssel identifiziert haben, der die verzerrte Verteilung verursacht, müssen Sie möglicherweise den Container mit einem Partitionsschlüssel für eine gleichmäßigere Verteilung neu partitionieren. Weitere Informationen zum Partitionieren in Azure Cosmos DB finden Sie unter [Partitionieren und Skalieren in Azure Cosmos DB](./partitioning-overview.md).

## <a name="determine-the-data-and-index-usage"></a>Bestimmen der Daten- und Indexnutzung

Es ist wichtig, die Speicherverteilung von partitionierten Containern nach Datennutzung, Indexnutzung und Dokumentnutzung zu bestimmen. Sie können die Indexnutzung minimieren, die Datennutzung maximieren und Ihre Abfragen optimieren. Um diese Daten abzurufen, navigieren Sie zum Bereich **Insights**, und öffnen Sie die Registerkarte **Speicher**:

:::image type="content" source="media/use-metrics/data-index-consumption.png" alt-text="Daten-, Index- und Dokumentnutzung" lightbox="media/use-metrics/data-index-consumption.png" :::

## <a name="compare-data-size-against-index-size"></a>Vergleichen der Datengröße mit der Indexgröße

In Azure Cosmos DB ist die Summe des Speicherverbrauchs die Kombination von Datengröße und Indexgröße. In der Regel ist die Indexgröße ein Bruchteil der Datengröße. Weitere Informationen finden Sie im Artikel [Indexgröße](index-policy.md#index-size). Im [Azure-Portal](https://portal.azure.com) wird im Blatt „Metriken“ auf der Registerkarte „Speicher“ die Aufschlüsselung des Speicherverbrauchs auf der Grundlage von Daten und Index angezeigt.

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

Wenn Sie Indexspeicher sparen möchten, können Sie die [Indizierungsrichtlinie](index-policy.md) anpassen.

## <a name="debug-why-queries-are-running-slow"></a>Debuggen der Ursachen für eine langsame Ausführung von Abfragen

Azure Cosmos DB bietet in den SDKs der SQL-API Statistiken zur Ausführung von Abfragen.

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName),
 "SELECT * FROM c WHERE c.city = 'Seattle'",
 new FeedOptions
 {
 PopulateQueryMetrics = true,
 MaxItemCount = -1,
 MaxDegreeOfParallelism = -1,
 EnableCrossPartitionQuery = true
 }).AsDocumentQuery();
FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;
```

*QueryMetrics* liefert Details zur Ausführungsdauer jeder Komponente der Abfrage. Die häufigste Ursache für die langsame Ausführung von Abfragen sind Überprüfungen (d. h. die Abfrage konnte die Indizes nicht nutzen). Dieses Problem kann durch eine bessere Filterbedingung behoben werden.

## <a name="next-steps"></a>Nächste Schritte

Jetzt wissen Sie, wie Sie mit den Metriken im Azure-Portal Problembereiche überwachen und Probleme beheben können. In den folgenden Artikeln erfahren Sie mehr darüber, wie Sie die Datenbankleistung verbessern können:

* Informationen zum Anzeigen von Metriken von Azure Monitor finden Sie im Artikel [Abrufen von Metriken von Azure Monitor](./monitor-cosmos-db.md). 
* [Leistungs- und Skalierungstests mit Azure Cosmos DB](performance-testing.md)
* [Leistungstipps für Azure Cosmos DB](performance-tips.md)