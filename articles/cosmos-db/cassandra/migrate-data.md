---
title: 'Migrieren Ihrer Daten zu einem Cassandra-API-Konto in Azure Cosmos DB: Tutorial'
description: In diesem Tutorial erfahren Sie, wie Sie Daten aus Apache Cassandra in ein Cassandra-API-Konto in Azure Cosmos DB kopieren.
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 7cf1a8f329ff735e9c82ca6361bedbcb8e25f4ac
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121784128"
---
# <a name="tutorial-migrate-your-data-to-a-cassandra-api-account"></a>Tutorial: Migrieren Ihrer Daten zu einem Cassandra-API-Konto
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

Entwickler verfügen unter Umständen über lokal oder in der Cloud ausgeführte Cassandra-Workloads, die sie zu Azure migrieren möchten. Solche Workloads können zu einem Cassandra-API-Konto in Azure Cosmos DB migriert werden. Dieses Tutorial enthält Informationen zu verschiedenen Optionen, die für die Migration von Apache Cassandra-Daten zum Cassandra-API-Konto in Azure Cosmos DB zur Verfügung stehen.

Dieses Tutorial enthält die folgenden Aufgaben:

> [!div class="checklist"]
> * Planen der Migration
> * Voraussetzungen für die Migration
> * Migrieren von Daten mithilfe des Befehls `cqlsh` `COPY`
> * Migrieren von Daten mithilfe von Spark

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites-for-migration"></a>Voraussetzungen für die Migration

* **Ermitteln des voraussichtlichen Durchsatzbedarfs:** Ermitteln Sie den voraussichtlichen Durchsatzbedarf für Ihre Workload, bevor Sie Daten zum Cassandra-API-Konto in Azure Cosmos DB migrieren. Im Allgemeinen beginnen Sie mit dem für CRUD-Vorgänge erforderlichen durchschnittlichen Durchsatz. Fügen Sie später den zusätzlich für ETL (Extrahieren, Transformieren und Laden) oder für Vorgänge mit Lastspitzen erforderlichen Durchsatz hinzu. Sie benötigen die folgenden Details für die Planung der Migration: 

  * **Größe der vorhandenen Daten oder geschätzte Datengröße:** Legen Sie die Mindestanforderungen für die Datenbankgröße und den Durchsatz fest. Wenn Sie die Datengröße für eine neue Anwendung schätzen, können Sie davon ausgehen, dass die Daten gleichmäßig über die Zeilen verteilt sind und den Wert durch Multiplizieren mit der Datengröße schätzen. 

  * **Erforderlicher Durchsatz:** Geben Sie die ungefähre Durchsatzrate für Leseoperationen (Abfragen/Abrufen) und Schreiben (Aktualisieren/Löschen/Einfügen) an. Dieser Wert ist erforderlich, um die erforderlichen Anforderungseinheiten zusammen mit der Größe der Daten mit festem Zustand zu berechnen.  

  * **Das Schema:** Stellen Sie über `cqlsh` eine Verbindung mit Ihrem Cassandra-Cluster her, und exportieren Sie das Schema aus Cassandra: 

    ```bash
    cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
    ```

    Nachdem Sie die Anforderungen Ihrer Workload ermittelt haben, erstellen Sie anhand der gesammelten Durchsatzanforderungen ein Azure Cosmos DB-Konto, eine Datenbank und Container.  

  * **Bestimmen der RU-Gebühr für einen Vorgang:** Sie können die RUs mithilfe eines der von der Cassandra-API unterstützten SDKs ermitteln. Dieses Beispiel zeigt die .NET-Version zum Abrufen von RU-Gebühren.

    ```csharp
    var tableInsertStatement = table.Insert(sampleEntity);
    var insertResult = await tableInsertStatement.ExecuteAsync();

    foreach (string key in insertResult.Info.IncomingPayload)
      {
         byte[] valueInBytes = customPayload[key];
         double value = Encoding.UTF8.GetString(valueInBytes);
         Console.WriteLine($"CustomPayload:  {key}: {value}");
      }
    ```

* **Zuordnen des erforderlichen Durchsatzes:** Azure Cosmos DB kann Speicher und Durchsatz automatisch gemäß Ihren wachsenden Anforderungen skalieren. Sie können Ihre Durchsatzanforderungen mit dem [Anforderungseinheitenrechner von Azure Cosmos DB](https://www.documentdb.com/capacityplanner) schätzen. 

* **Erstellen von Tabellen im Cassandra-API-Konto:** Bevor Sie mit der Migration von Daten beginnen, erstellen Sie zunächst alle Ihre Tabellen im Azure-Portal oder mit `cqlsh`. Wenn Sie als Migrationsziel ein Azure Cosmos DB-Konto mit Durchsatz auf Datenbankebene verwenden, geben Sie beim Erstellen der Container einen Partitionsschlüssel an.

* **Erhöhung des Durchsatzes:** Die Dauer der Datenmigration richtet sich nach der Durchsatzmenge, die Sie für Ihre Tabellen in Azure Cosmos DB bereitstellen. Erhöhen Sie den Durchsatz für die Dauer der Migration. Mit dem höheren Durchsatz können Sie eine Ratenbegrenzung vermeiden und Migrationen in kürzerer Zeit durchführen. Nachdem die Migration abgeschlossen ist, können Sie den Durchsatz wieder verringern, um Kosten zu sparen. Wir empfehlen außerdem, ein Azure Cosmos DB-Konto in derselben Region wie die Quelldatenbank zu verwenden. 

* **Aktivieren von TLS:** Für Azure Cosmos DB gelten strenge Sicherheitsanforderungen und -standards. Achten Sie darauf, TLS für die Interaktion mit Ihrem Konto zu aktivieren. Wenn Sie CQL mit SSH verwenden, können Sie TLS-Informationen bereitstellen.

## <a name="options-to-migrate-data"></a>Optionen zum Migrieren von Daten

Mithilfe der folgenden Optionen können Sie Daten mit dem Befehl `cqlsh`, `COPY` oder mit Spark aus vorhandenen Cassandra-Workloads nach Azure Cosmos DB verschieben. 

### <a name="migrate-data-by-using-the-cqlsh-copy-command"></a>Migrieren von Daten mit dem cqlsh COPY-Befehl

Der [Befehl CQL COPY](https://cassandra.apache.org/doc/latest/cassandra/tools/cqlsh.html#cqlshrc) wird verwendet, um lokale Daten in das Cassandra-API-Konto in Azure Cosmos DB zu kopieren.

1. Rufen Sie die Informationen zur Verbindungszeichenfolge für das Cassandra-API-Konto ab:

   * Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und wechseln Sie zu Ihrem Azure Cosmos DB-Konto.

   * Öffnen Sie den Bereich **Verbindungszeichenfolge**. Hier finden Sie alle Informationen, die Sie benötigen, um Ihr Cassandra-API-Konto aus `cqlsh` zu verbinden.

1. Melden Sie sich mit den Verbindungsinformationen aus dem Portal bei `cqlsh` an.

1. Kopieren Sie mit dem Befehl `CQL` `COPY` lokale Daten zum Cassandra API-Konto.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

### <a name="migrate-data-by-using-spark"></a>Migrieren von Daten mithilfe von Spark 

Führen Sie die folgenden Schritte aus, um Daten mithilfe von Spark zum Cassandra-API-Konto zu migrieren:

1. Stellen Sie einen [Azure Databricks-Cluster](spark-databricks.md) oder einen [Azure HDInsight-Cluster](spark-hdinsight.md) bereit. 

1. Verschieben Sie Daten an den Zielendpunkt der Cassandra-API. Weitere Informationen finden Sie in dieser [Schrittanleitung](migrate-data-databricks.md) für die Migration mit Azure Databricks.

Das Migrieren von Daten mithilfe von Spark-Aufträgen empfiehlt sich, wenn sich Ihre Daten in einem vorhandenen Cluster auf virtuellen Azure-Computern oder in einer anderen Cloud befinden. Zu diesem Zweck müssen Sie Spark als Vermittler für die einmalige oder reguläre Erfassung einrichten. Sie können diese Migration mithilfe von Azure ExpressRoute-Verbindungen zwischen Ihrer lokalen Umgebung und Azure beschleunigen. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcengruppe, das Azure Cosmos DB-Konto und die dazugehörigen Ressourcen nicht mehr benötigen, können Sie sie löschen. Wählen Sie dazu die Ressourcengruppe für den virtuellen Computer und anschließend **Löschen** aus, und bestätigen Sie den Namen der zu löschenden Ressourcengruppe.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie Ihre Daten zu einem Cassandra-API-Konto in Azure Cosmos DB migrieren. Nun können Sie sich noch weiter über andere Konzepten in Azure Cosmos DB informieren:

> [!div class="nextstepaction"]
> [Einstellbare Datenkonsistenzebenen in Azure Cosmos DB](../consistency-levels.md)
