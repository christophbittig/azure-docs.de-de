---
title: Vorbereitende Schritte für Datenmigrationen zur Azure Cosmos DB-API für MongoDB
description: In diesem Artikel erhalten Sie einen Überblick über die Voraussetzungen für die Datenmigration von MongoDB nach Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
author: gahl-levy
ms.author: gahllevy
ms.openlocfilehash: 80bb166925964624dc4a4b9811989ab15a5cfe9a
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123035673"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Vorbereitende Schritte für Datenmigrationen von MongoDB zur Azure Cosmos DB-API für MongoDB
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

> [!IMPORTANT]  
> Lesen Sie bitte diesen gesamten Leitfaden, bevor Sie Ihre vorbereitenden Schritte für die Migration ausführen.
>

Dieser MongoDB-Leitfaden für die Migrationsvorbereitung ist ein Teil der Reihe zur MongoDB-Migration. Die wichtigen MongoDB-Migrationsschritte umfassen die Migrationsvorbereitung, die Migration und die [Migrationsnachbereitung](post-migration-optimization.md), wie unten dargestellt.

![Ein Diagramm der Migrationsschritte.](./media/pre-migration-steps/overall-migration-steps.png)

## <a name="overview-of-pre-migration"></a>Eine Übersicht der Migrationsvorbereitung

Es ist wichtig, im Vorfeld eine bestimmte Planung und Entscheidungsfindung für Ihre Migration durchzuführen, bevor Sie tatsächlich Daten verschieben. Dieser anfängliche Entscheidungsprozess ist die "Migrationsvorbereitung". Ihr Ziel bei der Migrationsvorbereitung ist es (1) sicherzustellen, dass Sie Azure Cosmos DB einrichten, um die Anforderungen Ihrer Anwendung nach der Migration zu erfüllen und (2) die Ausführung der Migration zu planen.

Führen Sie diese Schritte aus, um eine umfassende Migrationsvorbereitung durchzuführen
* [Entdecken Sie Ihre vorhandenen MongoDB-Ressourcen und erstellen Sie ein Artefakt zum Nachverfolgen dieser Ressourcen](#pre-migration-discovery)
* [Bewerten Sie die Bereitschaft Ihrer vorhandenen MongoDB-Ressourcen für die Datenmigration](#pre-migration-assessment)
* [Ordnen Sie Ihre vorhandenen MongoDB-Ressourcen den neuen Azure Cosmos DB Ressourcen zu](#pre-migration-mapping)
* [Planen Sie die Logistik des End-to-End Migrationsprozesses, bevor Sie mit der vollständigen Datenmigration beginnen](#execution-logistics)

Führen Sie ihre Migration dann in Übereinstimmung mit Ihrem Migrationsvorbereitungsplan aus.

Führen Sie abschließend die wichtigen [Schritte der Migrationsnachbereitung wie die Übernahme und die Optimierung durch](post-migration-optimization.md).

Alle der oben genannten Schritte sind wichtig, um eine erfolgreiche Migration sicherzustellen.

Wenn Sie eine Migration planen, wird empfohlen, dass Sie, nach Möglichkeit, auf der Ebene einzelner Ressourcen planen.

## <a name="pre-migration-discovery"></a>Die Ermittlung der Migrationsvorbereitung

Der erste Migrationsvorbereitungsschritt ist die Ressourcenermittlung. In diesem Schritt versuchen Sie, eine umfassende Liste der vorhandenen Ressourcen in Ihrem MongoDB-Datenressourcenbereich zu erstellen.

### <a name="create-a-data-estate-migration-spreadsheet"></a>Das Erstellen einer Datenbestandsmigrationstabelle

Das Erstellen einer **Datenbestandsmigrationstabelle** als Nachverfolgungsdokument für Ihre Migration mit Ihrer bevorzugten Produktivitätssoftware. 
   * Der Zweck dieser Tabelle besteht darin, Ihre Produktivität zu steigern und Sie bei der Planung der Migration von End-to-End zu unterstützen.
   * Die Struktur der Tabelle liegt bei Ihnen. Die folgenden Aufzählungspunkte enthalten einige Empfehlungen.
   * Diese Tabelle sollte als ein Datensatz Ihrer Datenbestandsressourcen in Listenform strukturiert werden.
   * Jede Zeile entspricht einer Ressource (Datenbank oder Sammlung).
   * Jede Spalte entspricht einer Eigenschaft der Ressource. Vorerst sollten Sie mindestens den *Namen* und die *Datengröße (GB)* als Spalten haben. Im Idealfall können Sie jedoch auch die Informationen zur MongoDB-Version für jede Ressource sammeln. Fügen Sie in diesem Fall auch eine *Mongo-Versionsspalte* hinzu. 
   * Zunächst füllen Sie diese Tabelle mit einer Liste der vorhandenen Ressourcen in Ihrem MongoDB-Datenbestand aus. Während Sie diesen Leitfaden durchlaufen, verarbeiten Sie diese Tabelle in ein Nachverfolgungsdokument für Ihre End-to-End-Migrationsplanung und fügen bei Bedarf Spalten hinzu.

### <a name="discover-existing-mongodb-data-estate-resources"></a>Das Ermitteln vorhandener MongoDB-Datenbestandsressourcen

Identifizieren Sie mithilfe eines geeigneten Ermittlungstools die Ressourcen (Datenbanken, Sammlungen) in Ihrem vorhandenen MongoDB-Datenbestand so umfassend wie möglich. 

Hier sind einige Tools, die Sie zum Ermitteln von den Ressourcen verwenden können:
   * [MongoDB-Shell](https://www.mongodb.com/try/download/shell)
   * [MongoDB-Kompass](https://www.mongodb.com/try/download/compass)

## <a name="pre-migration-assessment"></a>Die Migrationsvorbereitungsbewertung

Bewerten Sie als zweiten Schritt, zur Vorbereitung der Planung Ihrer Migration, die Bereitschaft der einzelnen Ressourcen in Ihrem Datenbestand für die Migration. 

Die MongoDB-Version ist der primäre Faktor, der sich auf die Bereitschaft auswirkt. Azure Cosmos DB unterstützt derzeit die Binärprotokollversionen 3.2, 3.6 und 4.0 von der MongoDB. Idealerweise haben Sie in Ihrem Tabellenblatt zur Migrationsplanung eine Spalte für die *MongoDB-Version*. Gehen Sie die Tabelle schrittweise durch und markieren Sie alle Ressourcen, die inkompatible MongoDB-Versionen für die Azure Cosmos DB verwenden.

## <a name="pre-migration-mapping"></a>Die Zuordnung der Migrationsvorbereitung

Wenn die Ermittlungs- und Bewertungsschritte abgeschlossen sind, ist die MongoDB-Seite der Gleichung fertig. Jetzt wird die Azure Cosmos DB-Seite der Gleichung geplant. Wie werden Sie Ihre Produktions-Azure Cosmos DB-Ressourcen einrichten und konfigurieren? Führen Sie Ihre Planung auf *einzelnen Ressourcen* ebenen durch. Das bedeutet, dass Sie Ihrer Planungstabelle die folgenden Spalten hinzufügen sollten: 
* Azure Cosmos DB-Zuordnung 
* Shardschlüssel 
* Datenmodell
* Der Dedizierte und gemeinsam genutzter Durchsatz im Vergleich

Weitere Details hierzu finden Sie in den folgenden Abschnitten.

### <a name="capacity-planning"></a>Kapazitätsplanung

Versuchen Sie, die Kapazitätsplanung für eine Migration zu Azure Cosmos DB durchzuführen?
* Wenn Sie nur die Anzahl der virtuellen Kerne und Server in Ihrem vorhandenen Datenbankcluster kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](../convert-vcore-to-request-unit.md) 
* Wenn Sie die typischen Anforderungsraten für Ihre aktuelle Datenbank-Workload kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mit dem Azure Cosmos DB-Kapazitätsplaner](estimate-ru-capacity-planner.md)

### <a name="considerations-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Aspekte beim Verwenden der Azure Cosmos DB-API für MongoDB

Bevor Sie Ihren Azure Cosmos DB-Datenbestand planen, stellen Sie sicher, dass Sie die folgenden Azure Cosmos DB-Konzepte verstehen:

- **Kapazitätsmodell**: Die Datenbankkapazität in Azure Cosmos DB basiert auf einem durchsatzbasierten Modell. Dieses Modell basiert auf [Anforderungseinheiten pro Sekunde](../request-units.md). Dabei handelt es sich um eine Einheit, die die Anzahl von Datenbankvorgängen darstellt, die pro Sekunde für eine Sammlung ausgeführt werden können. Diese Kapazität kann auf [Datenbank- oder Sammlungsebene](../set-throughput.md) zugeordnet werden, und sie kann in einem Zuordnungsmodell oder mithilfe des [per Autoskalierung bereitgestellten Durchsatzes](../provision-throughput-autoscale.md) bereitgestellt werden.

- **Anforderungseinheiten**: In Azure Cosmos DB sind jedem Datenbankvorgang Kosten für Anforderungseinheiten (Request Units, RUs) zugeordnet. Bei der Ausführung werden diese von der verfügbaren Anforderungseinheitenebene für eine bestimmte Sekunde subtrahiert. Wenn für eine Anforderung mehr RUs als die aktuell zugeordnete Anzahl von Anforderungseinheiten pro Sekunde erforderlich sind, gibt es zwei Möglichkeiten, das Problem zu beheben: Sie können die Anzahl von RUs erhöhen oder warten, bis die nächste Sekunde beginnt, und dann den Vorgang wiederholen.

- **Elastische Kapazität**: Die Kapazität für eine bestimmte Sammlung oder Datenbank kann sich jederzeit ändern. Dadurch kann die Datenbank elastisch an die Durchsatzanforderungen Ihrer Workload angepasst werden.

- **Automatisches Sharding**: Azure Cosmos DB bietet ein automatisches Partitionierungssystem, das nur einen Shard (oder einen Partitionsschlüssel) erfordert. Der [automatische Partitionierungsmechanismus](../partitioning-overview.md) wird von allen Azure Cosmos DB-APIs gemeinsam genutzt und ermöglicht nahtlose Daten und die gesamte Skalierung durch horizontale Verteilung.

### <a name="plan-the-azure-cosmos-db-data-estate"></a>Planen Sie den Azure Cosmos DB-Datenbestand

Bestimmen Sie, welche Azure Cosmos DB-Ressourcen Sie erstellen werden. Das bedeutet, dass Sie Ihre Datenbestandsmigrationstabelle schrittweise durchgehen und jede vorhandene MongoDB-Ressource einer neuen Azure Cosmos DB-Ressource zuordnen. 
* Gehen Sie davon aus, dass aus jeder MongoDB-Datenbank eine Azure Cosmos DB Datenbank wird
* Gehen Sie davon aus, dass aus jeder MongoDB-Sammlung eine Azure Cosmos DB-Sammlung wird
* Wählen Sie eine Namenskonvention für Ihre Azure Cosmos DB-Ressourcen aus. Sofern sich die Struktur von den Datenbanken und Sammlungen nicht ändert, ist es in der Regel eine gute Wahl, die gleichen Ressourcennamen beizubehalten.
* In der MongoDB sind die Sharding-Sammlungen optional. In der Azure Cosmos DB unterliegt jede Sammlung dem Shard.
* *Gehen Sie nicht davon aus, dass Ihr MongoDB-Sammlung Shard-Schlüssel ihr Azure Cosmos DB-Sammlung Shard-Schlüssel wird. Gehen Sie nicht davon aus, dass Ihre vorhandene MongoDB-Datenmodell-/Dokumentstruktur das ist, was Sie für die Azure Cosmos DB einstellen werden.* 
   * Der Shard-Schlüssel ist die wichtigste Einstellung zum Optimieren der Skalierbarkeit und Leistung von der Azure Cosmos DB. Die Datenmodellierung ist die zweit wichtigste Einstellung. Beide Einstellungen sind unveränderlich und können nicht mehr geändert werden, nachdem sie festgelegt wurden. Es ist daher äußerst wichtig, sie bereits in der Planungsphase zu optimieren. Für weitere Informationen, befolgen Sie die Anleitung im Abschnitt [Unveränderliche Entscheidungen](#immutable-decisions).
* Die Azure Cosmos DB erkennt bestimmte MongoDB-Sammlungstypen, z. B. die begrenzten Sammlungen, nicht. Erstellen Sie für diese Ressourcen einfach normale Azure Cosmos DB Sammlungen.
* Azure Cosmos DB verfügt über zwei eigene Sammlungstypen: den gemeinsam genutzten und den dedizierten Durchsatz. Den gemeinsamen oder den dedizierten Durchsatz zu nutzen ist eine weitere wichtige, unveränderliche Entscheidung, die in der Planungsphase unbedingt getroffen werden muss. Für weitere Informationen, befolgen Sie die Anleitung im Abschnitt [Unveränderliche Entscheidungen](#immutable-decisions).

### <a name="immutable-decisions"></a>Die Unveränderlichen Entscheidungen

Die folgenden Azure Cosmos DB Konfigurationsoptionen können nicht mehr geändert oder rückgängig gemacht werden, nachdem Sie eine Azure Cosmos DB-Ressource erstellt haben. Es ist daher wichtig, für sie die richtige Entscheidung bei der Planung der Migrationsvorbereitung zu treffen, bevor Sie irgendeine Migrationen starten:
* Befolgen Sie [diese Anleitung](../partitioning-overview.md), um den besten Shard-Schlüssel auszuwählen. Die Partitionierung (auch als Sharding bezeichnet) ist ein wichtiger Aspekt, den Sie vor dem Migrieren von Daten berücksichtigen müssen. Azure Cosmos DB verwendet die vollständig verwaltete Partitionierung, um die Kapazität in einer Datenbank zur Erfüllung der Speicher- und Durchsatzanforderungen zu erhöhen. Diese Funktion erfordert weder das Hosting noch die Konfiguration von Routingservern.   
   * Auf ähnliche Weise fügt die Partitionierungsfunktion automatisch Kapazität hinzu und gleicht die Daten entsprechend neu aus. Ausführliche Informationen und Empfehlungen zum Auswählen des richtigen Partitionsschlüssels für Ihre Daten finden Sie unter [Auswählen eines Partitionsschlüssels](../partitioning-overview.md#choose-partitionkey). 
* Befolgen Sie [diese Anleitung](../modeling-data.md), um ein Datenmodell auszuwählen
* Befolgen Sie [diese Anleitung,](../optimize-cost-throughput.md#optimize-by-provisioning-throughput-at-different-levels) um zwischen dediziertem und gemeinsam genutzten Durchsatz für jede Ressource zu wählen, die Sie migrieren möchten
* [Hier](../how-to-model-partition-example.md) sehen Sie ein praxisorientiertes Beispiel für Sharding und Datenmodellierung, um Sie bei der Entscheidungsfindung zu unterstützen

### <a name="cost-of-ownership"></a>Die Betriebskosten

* Schätzen Sie die Betriebskosten Ihrer neuen Azure Cosmos DB Ressourcen mithilfe des [Azure Cosmos DB-Kapazitätsrechners](https://cosmos.azure.com/capacitycalculator/).

### <a name="estimating-throughput"></a>Das Schätzen des Durchsatzes

* In Azure Cosmos DB wird der Durchsatz vorab bereitgestellt und in Anforderungseinheiten (RU) pro Sekunde gemessen. Im Gegensatz zu VMs oder lokalen Servern können RUs jederzeit einfach hoch- und herunterskaliert werden. Sie können die Anzahl der bereitgestellten RUs sofort ändern. Weitere Informationen finden Sie unter [Anforderungseinheiten in Azure Cosmos DB](../request-units.md).

* Sie können den [Azure Cosmos DB Capacity Calculator](https://cosmos.azure.com/capacitycalculator/) verwenden, um die Anzahl von Anforderungseinheiten basierend auf der Konfiguration Ihres Datenbankkontos, der Datenmenge, der Dokumentgröße und den erforderlichen Lese- und Schreibvorgängen pro Sekunde zu ermitteln.

* Im Folgenden sind wichtige Faktoren beschrieben, die sich auf die Anzahl der erforderlichen RUs auswirken:
   * **Dokumentgröße**: Je größer ein Element/Dokument, desto mehr RUs werden beim Lesen oder Schreiben des Elements/Dokuments genutzt.

   * **Anzahl der Dokumenteigenschaften**: Die Anzahl der zum Erstellen oder Aktualisieren eines Dokuments verbrauchten RUs bezieht sich auf die Anzahl, Komplexität und Länge der zugehörigen Eigenschaften. Sie können die für Schreibvorgänge genutzten Anforderungseinheiten verringern, indem Sie die [Anzahl indizierter Eigenschaften begrenzen](mongodb-indexing.md).

   * **Abfragemuster**: Die Komplexität einer Abfrage wirkt sich darauf aus, wie viele Anforderungseinheiten von ihr verbraucht werden. 

* Am besten können Sie die Kosten von Abfragen verstehen, wenn Sie Beispieldaten in Azure Cosmos DB verwenden und mithilfe des Befehls `getLastRequestStastistics` [Beispielabfragen aus der MongoDB-Shell ausführen](connect-mongodb-account.md), um die Anforderungsgebühr abzurufen. Dadurch wird die Anzahl der verbrauchten RUs ausgegeben:

    `db.runCommand({getLastRequestStatistics: 1})`

    Dieser Befehl gibt ein JSON-Dokument wie das folgende aus:

    ```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

* Sie können auch die [Diagnoseeinstellungen](../cosmosdb-monitor-resource-logs.md) verwenden, um die Häufigkeit und die Muster der für Azure Cosmos DB ausgeführten Abfragen zu verstehen. Die Ergebnisse der Diagnoseprotokolle können an ein Speicherkonto, eine EventHub-Instanz oder an [Azure Log Analytics](../../azure-monitor/logs/log-analytics-tutorial.md) gesendet werden.  

## <a name="pre-migration-logistics-planning"></a>Das Planen der Migrationsvorbereitungslogistik

Nachdem Sie nun einen Überblick über Ihren bestehenden Datenbestand und ein Design für Ihren neuen Azure Cosmos DB-Datenbestand haben, können Sie planen, wie Sie Ihren Migrationsprozess End-to-End durchführen. Auch hier planen Sie auf der Ebene der *einzelnen Ressourcen* und fügen Spalten in Ihre Tabelle hinzu, um die unten aufgeführten logistischen Dimensionen zu erfassen.

### <a name="execution-logistics"></a>Die Ausführungslogistik
* Das Zuweisen der Verantwortung für die Migration jeder vorhandenen Ressource von der MongoDB zur Azure Cosmos DB. Wie Sie die Ressourcen Ihres Teams einsetzen, um Ihre Migration zum Abschluss zu bringen, bleibt Ihnen überlassen. Bei kleinen Migrationen können Sie ein Team die gesamte Migration starten und den Fortschritt überwachen lassen. Bei größeren Migrationen können Sie den Teammitgliedern ressourcenspezifische Verantwortung für die Migration und Überwachung dieser Ressource zuweisen.
* Nachdem Sie die Verantwortung für die Migration Ihren Ressourcen zugewiesen haben, sollten Sie nun das/die richtige(n) Migrationstool(s) für die Migration auswählen. Bei kleinen Migrationen können Sie möglicherweise ein Migrationstool wie ein natives MongoDB-Tool oder den Azure DMS verwenden, um alle Ihre Ressourcen auf einmal zu migrieren. Für größere Migrationen oder solche mit speziellen Anforderungen sollten Sie Migrationstools mit einer Granularität für die einzelnen Ressourcen auswählen.
   * Bevor Sie die zu verwendenden Migrationstools planen, sollten Sie sich mit den verfügbaren Optionen vertraut machen. Der [Azure Database Migration Service für die Azure Cosmos DB-API für MongoDB](../../dms/tutorial-mongodb-cosmos-db.md) bietet einen Mechanismus, der die Datenmigration vereinfacht, indem eine vollständig verwaltete Hostingplattform, Migrationsüberwachungsoptionen und die automatische Drosselung bereitgestellt werden. Nachfolgend finden Sie die vollständige Liste der Optionen:

   |**Migrationstyp**|**Lösung**|**Überlegungen**|
   |---------|---------|---------|
   |Online|[Azure Database Migration Service](../../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; Nutzt die Azure Cosmos DB-BulkExecutor-Bibliothek <br/>&bull; Eignet sich für große Datasets und übernimmt die Replikation von Liveänderungen <br/>&bull; Funktioniert nur mit anderen MongoDB-Quellen|
   |Offline|[Azure Database Migration Service](../../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; Nutzt die Azure Cosmos DB-BulkExecutor-Bibliothek <br/>&bull; Eignet sich für große Datasets und übernimmt die Replikation von Liveänderungen <br/>&bull; Funktioniert nur mit anderen MongoDB-Quellen|
   |Offline|[Azure Data Factory](../../data-factory/connector-azure-cosmos-db.md)|&bull; Einfache Einrichtung und Unterstützung mehrerer Quellen <br/>&bull; Nutzt die Azure Cosmos DB-BulkExecutor-Bibliothek <br/>&bull; Geeignet für große Datasets <br/>&bull; Fehlende Prüfpunktausführung: Wenn während der Migration ein Problem auftritt, muss der gesamte Migrationsprozess neu gestartet werden.<br/>&bull; Fehlende Warteschlange für unzustellbare Nachrichten: Dies bedeutet, dass wenige fehlerhafte Dateien den gesamten Migrationsprozess unterbrechen können. <br/>&bull; Erfordert benutzerdefinierten Code, um den Lesedurchsatz für bestimmte Datenquellen zu erhöhen|
   |Offline|[Vorhandene Mongo-Tools (mongodump, mongorestore, Studio3T)](tutorial-mongotools-cosmos-db.md)|&bull; Einfache Einrichtung und Integration <br/>&bull; Erfordert die benutzerdefinierte Behandlung von Drosselungen|
   |offline / online|[Azure Databricks und Spark](migrate-databricks.md)|&bull; Vollständige Steuerung der Migrationsrate und Datentransformation <br/>&bull; Erfordert benutzerdefinierten Code|
    
   * Wenn Ihre Ressource eine Offline-Migration tolerieren kann, verwenden Sie das folgende Diagramm, um das entsprechende Migrationstool zu wählen:

   ![Die Offline-Migrationstools.](./media/pre-migration-steps/offline-tools.png)

   * Wenn Ihre Ressource eine Online-Migration erfordert, verwenden Sie das folgende Diagramm, um das entsprechende Migrationstool zu wählen:

   ![Die Online-Migrationstools.](./media/pre-migration-steps/online-tools.png)
   
   Eine Übersicht über die oben erwähnten Migrationslösungen sowie eine Demo finden Sie in [diesem Video](https://www.youtube.com/watch?v=WN9h80P4QJM).

* Nachdem Sie die Migrationstools für jede Ressource ausgewählt haben, besteht der nächste Schritt im Priorisieren der zu migrierenden Ressourcen. Eine gute Priorisierung kann dazu beitragen, ihre Migration im Zeitplan zu halten. Eine bewährte Methode ist, die Migration der Ressourcen zu priorisieren, die am meisten Zeit für das Verschieben beanspruchen. Diese Ressourcen zuerst zu migrieren, bringt den größten Fortschritt in Richtung Fertigstellung. Diese zeitaufwändigen Migrationen umfassen in der Regel mehr Daten und sind daher ressourcenintensiver für das Migrationstool. Daher ist es wahrscheinlicher, dass frühzeitig Probleme mit Ihrer Migrationspipeline auftreten. Das minimiert die Wahrscheinlichkeit, dass Ihr Zeitplan aufgrund von Schwierigkeiten mit Ihrer Migrationspipeline ins Wanken gerät.
* Planen Sie die Überwachung des Migrationsfortschritts nach dem Start. Wenn Sie Ihren Datenmigrationsaufwand unter einem Team koordinieren, planen Sie einen regelmäßigen Rhythmus von Team-Synchronisierungen. So behalten Sie einen umfassenden Überblick über die Migrationen mit hoher Priorität.
  

### <a name="supported-migration-scenarios"></a>Unterstützte Migrationsszenarien

Die beste Wahl für das MongoDB-Migrationstool hängt von Ihrem Migrationsszenario ab. 

#### <a name="types-of-migrations"></a>Die Migrationstypen

Die kompatiblen Tools für jedes Migrationsszenario sind nachfolgend dargestellt:

![Die unterstützten Migrationsszenarien.](./media/pre-migration-steps/migration-tools-use-case-table.png)

#### <a name="tooling-support-for-mongodb-versions"></a>Die Toolunterstützung für die MongoDB-Versionen

Unter der Voraussetzung, dass Sie von einer bestimmten MongoDB-Version migrieren, sind die unterstützten Tools nachfolgend aufgeführt:

![Die MongoDB-Versionen, die von Migrationstools unterstützt werden.](./media/pre-migration-steps/migration-tool-compatibility.png)

### <a name="post-migration"></a>Nach der Migration

Nehmen Sie sich in der Migrationsvorbereitungsphase etwas Zeit, um zu planen, welche Schritte Sie für die App-Migration und die Optimierung nach der Migration unternehmen werden.
* In der Phase der Migrationsnachbereitung führen Sie eine Umstellung Ihrer Anwendung aus, um die Azure Cosmos DB anstelle Ihres vorhandenen MongoDB-Datenbestands zu verwenden. 
* Achten Sie darauf, die Indizierung, die globale Verteilung, die Konsistenz und andere *veränderliche* Azure Cosmos DB-Eigenschaften auf den einzelnen Ressourcenebenen zu planen. Diese Azure Cosmos DB-Konfigurationseinstellungen *können* jedoch später geändert werden. Rechnen sie also damit, später Anpassungen an diesen Einstellungen vorzunehmen. Lassen Sie nicht zu, dass diese Aspekte eine Ursache für eine Analyseparalyse sind. Sie werden diese veränderlichen Konfigurationen nach der Migration anwenden.
* Die beste Anleitung für die Migrationsnachbereitung finden Sie [hier](post-migration-optimization.md).

## <a name="next-steps"></a>Nächste Schritte

* Versuchen Sie, die Kapazitätsplanung für eine Migration zu Azure Cosmos DB durchzuführen?
    * Wenn Sie nur die Anzahl der virtuellen Kerne und Server in Ihrem vorhandenen Datenbankcluster kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](../convert-vcore-to-request-unit.md) 
    * Wenn Sie die typischen Anforderungsraten für Ihre aktuelle Datenbank-Workload kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mit dem Azure Cosmos DB-Kapazitätsplaner](estimate-ru-capacity-planner.md)
* Migrieren zur Azure Cosmos DB-API für MongoDB
   * [Offline-Migration mit nativen MongoDB-Tools](tutorial-mongotools-cosmos-db.md)
   * [Offline-Migration mit dem Azure Database Migration Service (DMS)](../../dms/tutorial-mongodb-cosmos-db.md)
   * [Online-Migration mit dem Azure-Datenbank-Migration Service (DMS)](../../dms/tutorial-mongodb-cosmos-db-online.md)
   * [Offline/Online-Migration unter Verwendung von Azure Databricks und Spark](migrate-databricks.md)
* [Leitfaden nach der Migration](post-migration-optimization.md): Optimierungsschritte nach der Migration zu Azure Cosmos DB-API für MongoDB
* [Bereitstellen des Durchsatzes für Azure Cosmos-Container und -Datenbanken](../set-throughput.md)
* [Partitioning in Azure Cosmos DB](../partitioning-overview.md) (Partitionierung in Azure Cosmos DB)
* [Globale Verteilung in Azure Cosmos DB](../distribute-data-globally.md)
* [Indizierung in Azure Cosmos DB](../index-overview.md)
* [Anforderungseinheiten in Azure Cosmos DB](../request-units.md)
