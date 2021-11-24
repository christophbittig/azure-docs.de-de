---
title: Auswählen einer API in Azure Cosmos DB
description: Erfahren Sie, wie Sie basierend auf Ihren Workloadanforderungen zwischen SQL/Core-, MongoDB-, Cassandra-, Gremlin- und Tabellen-APIs in Azure Cosmos DB wählen.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/12/2021
ms.openlocfilehash: e120d4fa4d85ba2d52821460da1aa62eafd0455d
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158027"
---
# <a name="choose-an-api-in-azure-cosmos-db"></a>Auswählen einer API in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB ist eine vollständig verwaltete NoSQL-Datenbank für die moderne App-Entwicklung. Azure Cosmos DB nimmt Ihnen die Datenbankverwaltung durch automatische Verwaltung, Updates und Patches ab. Außerdem übernimmt der Dienst die Kapazitätsverwaltung mit kostengünstigen serverlosen und automatischen Skalierungsoptionen, die auf Anwendungsanforderungen reagieren, um die Kapazität an den Bedarf anzupassen.

## <a name="apis-in-azure-cosmos-db"></a>APIs in Azure Cosmos DB

Azure Cosmos DB bietet mehrere Datenbank-APIs, einschließlich der Core-API (SQL), API für MongoDB, Cassandra-API, Gremlin-API und Tabellen-API. Mithilfe dieser APIs können Sie reale Daten anhand von Dokumenten, Schlüssel-Wert-, Graph- und Spaltenfamilien-Datenmodellen modellieren. Diese APIs ermöglichen es Ihren Anwendungen, Azure Cosmos DB so zu handhaben, als ob es sich um verschiedene andere Datenbanktechnologien handelte, jedoch ohne den Verwaltungsaufwand und Skalierungsansätze. Mit diesen APIs hilft Ihnen Azure Cosmos DB, die Ökosysteme, Tools und Fähigkeiten zu nutzen, die Sie bereits für die Datenmodellierung und -abfrage besitzen.

Alle APIs bieten automatische Skalierung von Speicher und Durchsatz, Flexibilität und Leistungsgarantien. Es gibt nicht die eine beste API, und Sie können eine beliebige API zum Erstellen Ihrer Anwendung auswählen. In diesem Artikel erfahren Sie, wie Sie eine API auf Grundlage Ihrer Workload- und Teamanforderungen auswählen.

## <a name="considerations-when-choosing-an-api"></a>Überlegungen bei der Auswahl einer API

Die Core-API (SQL) ist die native Azure Cosmos DB-API.

Die APIs für MongoDB, Cassandra, Gremlin und Tabellen implementieren das Wire Protocol von Open-Source-Datenbank-Engines. Diese APIs sind unter den folgenden Bedingungen am besten geeignet:

* Wenn Sie über vorhandene MongoDB-, Cassandra- oder Gremlin-Anwendungen verfügen.
* Wenn Sie nicht Ihre gesamte Datenzugriffsebene umschreiben möchten.
* Wenn Sie das Open-Source-Entwicklerökosystem, Clienttreiber, Fachwissen und Ressourcen für Ihre Datenbank nutzen möchten.
* Wenn Sie die wichtigsten Azure Cosmos DB-Features wie globale Verteilung, elastische Skalierung von Speicher und Durchsatz, Leistung, geringe Latenz, die Möglichkeit zum Ausführen von Transaktions- und Analyseworkloads sowie eine vollständig verwaltete Plattform nutzen möchten.
* Wenn Sie modernisierte Apps in einer Multicloudumgebung entwickeln.

Sie können mit diesen APIs neue Anwendungen erstellen oder Ihre vorhandenen Daten migrieren. Zum Ausführen der migrierten Apps ändern Sie die Verbindungszeichenfolge Ihrer Anwendung, und setzen Sie die Ausführung wie zuvor fort. Stellen Sie beim Migrieren vorhandener Apps sicher, dass Sie die Featureunterstützung dieser APIs prüfen.

Sie müssen je nach Workload die API auswählen, die Ihren Anforderungen entspricht. Die folgende Abbildung zeigt ein Flussdiagramm zur Auswahl der richtigen API beim Erstellen neuer Apps oder beim Migrieren vorhandener Apps zu Azure Cosmos DB:

:::image type="content" source="./media/choose-api/choose-api-decision-tree.png" alt-text="Entscheidungsstruktur zur Auswahl einer API in Azure Cosmos DB" lightbox="./media/choose-api/choose-api-decision-tree.png":::

## <a name="coresql-api"></a>Core-API (SQL)

Diese API speichert Daten im Dokumentformat. Sie bietet das beste End-to-End-Szenario, da sie uns vollständige Kontrolle über die Schnittstelle, den Dienst und die SDK-Clientbibliotheken ermöglicht. Jedes neue Feature, das in Azure Cosmos DB eingeführt wird, ist zuerst für SQL-API-Konten verfügbar. SQL-API-Konten von Azure Cosmos DB bieten Unterstützung für das Abfragen von Elementen mithilfe der SQL-Syntax (Structured Query Language), einer der bekanntesten und beliebtesten Abfragesprachen zum Abfragen von JSON-Objekten. Weitere Informationen finden Sie im Lernmodul zur [SQL-API von Azure Cosmos DB](/learn/modules/intro-to-azure-cosmos-db-core-api/) und im Artikel [Erste Schritte mit SQL-Abfragen](sql-query-getting-started.md).

Wenn Sie von anderen Datenbanken wie Oracle, DynamoDB, HBase usw. migrieren und die modernisierten Technologien zum Erstellen Ihrer Apps verwenden möchten, wird die SQL-API empfohlen. Die SQL-API unterstützt Analysen und bietet Leistungsisolation zwischen operativen und analytischen Workloads.

### <a name="capacity-planning-for-migration-to-api-for-sqlcoreapi"></a>Kapazitätsplanung für die Migration zu API for SQL(Core)API

Sie möchten die Kapazitätsplanung für eine Migration zur SQL-API für Azure Cosmos DB aus einem vorhandenen Datenbankcluster durchführen? Sie können Informationen zu Ihrem vorhandenen Datenbankcluster für die Kapazitätsplanung verwenden.
* Wenn Sie nur die Anzahl der virtuellen Kerne und Server in Ihrem vorhandenen Shard-Datenbankcluster und replizierten Datenbankcluster kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](convert-vcore-to-request-unit.md).
* Sollten Sie die typischen Anforderungsraten Ihrer aktuellen Datenbankworkload kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mit dem Azure Cosmos DB-Kapazitätsplaner](estimate-ru-with-capacity-planner.md).

## <a name="api-for-mongodb"></a>API für MongoDB

Diese API speichert Daten in einer Dokumentstruktur im BSON-Format. Sie ist mit dem MongoDB-Wire Protocol kompatibel, verwendet jedoch keinen nativen MongoDB-bezogenen Code. Diese API ist eine gute Wahl, wenn Sie das umfassendere MongoDB-Ökosystem und die entsprechenden Fähigkeiten nutzen möchten, ohne auf die Features von Azure Cosmos DB wie Skalierung, Hochverfügbarkeit, Georeplikation, mehrere Schreibstandorte, automatische und transparente Shardverwaltung, transparente Replikation zwischen Betriebs- und Analysespeichern und vieles mehr zu verzichten.

Sie können Ihre vorhandenen MongoDB-Apps mit der API für MongoDB verwenden, indem Sie einfach die Verbindungszeichenfolge ändern. Sie können alle vorhandenen Daten mit nativen MongoDB-Tools wie mongodump und mongorestore oder mit unserem Tool für die Azure-Databankmigration verschieben. Tools wie die MongoDB-Shell, [MongoDB Compass](mongodb/connect-using-compass.md) und [Robo3T](mongodb/connect-using-robomongo.md) können Abfragen ausführen und mit Daten arbeiten wie mit der nativen MongoDB.

Die API für MongoDB ist mit den MongoDB-Serverversionen 4.0, 3.6 und 3.2 kompatibel. Serverversion 4.0 wird empfohlen, da sie die beste Leistung und vollständige Featureunterstützung bietet. Weitere Informationen finden Sie im Artikel zur [API für MongoDB](mongodb/mongodb-introduction.md).

### <a name="capacity-planning-for-migration-to-api-for-mongodb"></a>Kapazitätsplanung für die Migration zur API für MongoDB

Sie möchten die Kapazitätsplanung für eine Migration zur Azure Cosmos DB-API für MongoDB aus einem vorhandenen Datenbankcluster durchführen? Sie können Informationen zu Ihrem vorhandenen Datenbankcluster für die Kapazitätsplanung verwenden.
* Wenn Sie nur die Anzahl der virtuellen Kerne und Server in Ihrem vorhandenen Datenbankcluster kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](convert-vcore-to-request-unit.md).
* Sollten Sie die typischen Anforderungsraten Ihrer aktuellen Datenbankworkload kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mit dem Azure Cosmos DB-Kapazitätsplaner](./mongodb/estimate-ru-capacity-planner.md).

## <a name="cassandra-api"></a>Cassandra-API

Diese API speichert Daten im spaltenorientierten Schema. Apache Cassandra bietet einen Ansatz mit hohem Verteilungsgrad und horizontaler Skalierung zum Speichern großer Datenmengen und gleichzeitig einen flexiblen Ansatz für ein spaltenorientiertes Schema. Die Cassandra-API in Azure Cosmos DB entspricht dieser Philosophie für verteilte NoSQL-Datenbanken. Die Cassandra-API ist mit dem Wire Protocol von Apache Cassandra kompatibel. Sie sollten die Cassandra-API in Betracht ziehen, wenn Sie von der Elastizität und der vollständigen Verwaltung von Azure Cosmos DB profitieren und trotzdem die meisten der nativen Apache Cassandra-Features, die Tools und das Ökosystem nutzen möchten. Das bedeutet, dass Sie bei der Cassandra-API das Betriebssystem, die Java-VM, den Garbage Collector, die Lese-/Schreibleistung, Knoten, Cluster usw. nicht verwalten müssen.

Sie können Apache Cassandra-Clienttreiber verwenden, um eine Verbindung mit der Cassandra-API herzustellen. Die Cassandra-API ermöglicht Ihnen die Interaktion mit Daten mithilfe der Cassandra-Abfragesprache (Cassandra Query Language, CQL) und Tools wie der CQL-Shell und bereits vertrauten Cassandra-Clienttreibern. Die Cassandra-API unterstützt derzeit nur OLTP-Szenarien. Mit der Cassandra-API können Sie auch die einzigartigen Features von Azure Cosmos DB verwenden, z. B. den Änderungsfeed. Weitere Informationen finden Sie im Artikel zur [Cassandra-API](cassandra-introduction.md).

## <a name="gremlin-api"></a>Gremlin-API

Mit dieser API können Benutzer Graphabfragen durchführen und Daten als Kanten und Scheitelpunkte speichern. Verwenden Sie diese API für Szenarien mit dynamischen Daten, Daten mit komplexen Beziehungen, Daten, die zu komplex sind, um mit relationalen Datenbanken modelliert zu werden, und wenn Sie das vorhandene Gremlin-Ökosystem und die entsprechenden Fähigkeiten nutzen möchten. Die Gremlin-API von Azure Cosmos DB kombiniert die Leistung von Algorithmen für Graphdatenbanken mit einer hochgradig skalierbaren, verwalteten Infrastruktur. Sie bietet eine einzigartige, flexible Lösung für die häufigsten Datenprobleme, die mit mangelnder Flexibilität und relationalen Ansätzen verbunden sind. Die Gremlin-API unterstützt derzeit nur OLTP-Szenarien.

Die Gremlin-API von Azure Cosmos DB basiert auf dem Graphcomputingframework [Apache TinkerPop](https://tinkerpop.apache.org/). Die Gremlin-API verwendet zum Erfassen und Abfragen von Daten die gleiche Graphabfragesprache. Sie nutzt die Partitionsstrategie von Azure Cosmos DB, um Lese-/Schreibvorgänge von der Graphdatenbank-Engine durchzuführen. Die Gremlin-API bietet Wire Protocol-Unterstützung mit Open-Source-Gremlin, sodass Sie die Open-Source-Gremlin SDKs zum Erstellen Ihrer Anwendung verwenden können. Die Gremlin-API von Azure Cosmos DB arbeitet auch mit Apache Spark und [GraphFrames](https://github.com/graphframes/graphframes) für komplexe analytische Graphszenarien. Weitere Informationen finden Sie im Artikel zur [Gremlin-API](graph-introduction.md).

## <a name="table-api"></a>Tabelle-API

Diese API speichert Daten im Schlüssel-Wert-Format. Wenn Sie derzeit Azure Table Storage verwenden, können Sie möglicherweise einige Einschränkungen bei Latenz, Skalierung, Durchsatz, globaler Verteilung, Indexverwaltung und eine niedrige Abfrageleistung feststellen. Die Tabellen-API überwindet diese Einschränkungen und wird zum Migrieren Ihrer App empfohlen, wenn Sie die Vorteile von Azure Cosmos DB nutzen möchten. Die Tabellen-API unterstützt nur OLTP-Szenarien.

Anwendungen, die für Azure Table Storage geschrieben wurden, können mit geringfügigen Codeänderungen zur Tabellen-API migriert werden und Premium-Funktionen nutzen. Weitere Informationen finden Sie im Artikel zur [Tabellen-API](table/introduction.md).

## <a name="next-steps"></a>Nächste Schritte

* [Erste Schritte mit der SQL-API von Azure Cosmos DB](create-sql-api-dotnet.md)
* [Erste Schritte mit der Azure Cosmos DB-API für MongoDB](mongodb/create-mongodb-nodejs.md)
* [Erste Schritte mit der Cassandra-API von Azure Cosmos DB](cassandra/manage-data-dotnet.md)
* [Erste Schritte mit der Gremlin-API von Azure Cosmos DB](create-graph-dotnet.md)
* [Erste Schritte mit der Tabellen-API von Azure Cosmos DB](create-table-dotnet.md)
* Versuchen Sie, die Kapazitätsplanung für eine Migration zu Azure Cosmos DB durchzuführen? Sie können Informationen zu Ihrem vorhandenen Datenbankcluster für die Kapazitätsplanung verwenden.
    * Wenn Sie nur die Anzahl der virtuellen Kerne und Server in Ihrem vorhandenen Datenbankcluster kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](convert-vcore-to-request-unit.md) 
    * Wenn Sie die typischen Anforderungsraten für Ihre aktuelle Datenbankworkload kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mit dem Azure Cosmos DB-Kapazitätsplaner](estimate-ru-with-capacity-planner.md)
