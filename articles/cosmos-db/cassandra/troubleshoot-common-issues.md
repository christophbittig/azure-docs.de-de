---
title: Behandeln häufiger Fehler in der Cassandra-API von Azure Cosmos DB
description: In diesem Artikel werden häufige Probleme in der Cassandra-API von Azure Cosmos DB und deren Behandlung erläutert.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: feeb3e7eb0522b515b0fd23591dd7966d80cec73
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/21/2021
ms.locfileid: "122633535"
---
# <a name="troubleshoot-common-issues-in-the-azure-cosmos-db-cassandra-api"></a>Behandeln häufiger Probleme in der Cassandra-API von Azure Cosmos DB

[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

Die Cassandra-API in [Azure Cosmos DB](../introduction.md) ist eine Kompatibilitätsebene, die [Wire Protocol-Unterstützung](cassandra-support.md) für die Open-Source-Datenbank Apache Cassandra bereitstellt.

In diesem Artikel werden häufige Fehler und deren Lösungen für Anwendungen beschrieben, die die Cassandra-API von Azure Cosmos DB verwenden. Wenn Ihr Fehler nicht aufgeführt ist und beim Ausführen eines [unterstützten Vorgangs in Cassandra](cassandra-support.md) ein Fehler auftritt, der bei Verwendung von nativem Apache Cassandra jedoch nicht auftritt, [erstellen Sie eine Azure-Supportanfrage](../../azure-portal/supportability/how-to-create-azure-support-request.md).

>[!NOTE]
>Als vollständig verwalteter cloudnativer Dienst bietet Azure Cosmos DB [Garantien für Verfügbarkeit, Durchsatz und Konsistenz](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) für die Cassandra-API. Außerdem ermöglicht die Cassandra-API die Ausführung von Plattformvorgängen ohne Wartung und das Patchen ohne Ausfallzeiten.
>
>Diese Garantien sind in früheren Implementierungen von Apache Cassandra nicht möglich, sodass sich viele der Back-End-Vorgänge der Cassandra-API von Apache Cassandra unterscheiden. Zur Vermeidung häufiger Fehler werden bestimmte Einstellungen und Methoden empfohlen.

## <a name="nonodeavailableexception"></a>NoNodeAvailableException

Bei diesem Fehler handelt es sich um eine übergeordnete Wrapperausnahme mit einer Vielzahl möglicher Ursachen und innerer Ausnahmen, von denen viele mit dem Client zusammenhängen können.

Häufige Ursachen und Lösungen:

- **Leerlauftimeout von Azure Load Balancer-Instanzen**: Dieses Problem kann auch in Form von `ClosedConnectionException` auftreten. Zur Behebung des Problems legen Sie die Keep-Alive-Einstellung im Treiber fest (siehe [Aktivieren von Keep-Alive für den Java-Treiber](#enable-keep-alive-for-the-java-driver)) und erhöhen die Keep-Alive-Einstellungen in Ihrem Betriebssystem. Sie können auch das [Leerlauftimeout in Azure Load Balancer anpassen](../../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal).

- **Ressourcenauslastung der Clientanwendung:** Vergewissern Sie sich, dass die Clientcomputer über genügend Ressourcen verfügen, um die Anforderung abzuwickeln.

## <a name="cant-connect-to-a-host"></a>Verbindungsherstellung mit einem Host nicht möglich

Möglicherweise wird folgende Fehlermeldung angezeigt: „Cannot connect to any host, scheduling retry in 600000 milliseconds.“ (Es kann keine Verbindung mit einem Host hergestellt werden, Wiederholungsversuch in 600000 Millisekunden geplant.)

Dieser Fehler wird möglicherweise durch eine clientseitige SNAT-Auslastung (Source Network Address Translation, Übersetzung der Quellnetzwerkadresse) verursacht. Gehen Sie wie unter [Verwenden von SNAT für ausgehende Verbindungen](../../load-balancer/load-balancer-outbound-connections.md) beschrieben vor, um dieses Problem auszuschließen.

Bei dem Fehler kann sich aber auch um ein Leerlauftimeoutproblem handeln. (Das Leerlauftimeout von Azure Load Balancer ist standardmäßig auf vier Minuten festgelegt.) Weitere Informationen finden Sie unter [Leerlauftimeout für Load Balancer](../../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal). [Aktivieren Sie Keep-Alive für den Java-Treiber](#enable-keep-alive-for-the-java-driver), und legen Sie das `keepAlive`-Intervall für das Betriebssystem auf weniger als vier Minuten fest.

## <a name="overloadedexception-java"></a>OverloadedException (Java)

Anforderungen werden gedrosselt, da die Gesamtanzahl der verbrauchten Anforderungseinheiten höher ist als die Anzahl der Anforderungseinheiten, die Sie für den Keyspace oder die Tabelle bereitgestellt haben.

Sie sollten ggf. den Durchsatz, der einem Keyspace oder einer Tabelle zugewiesen ist, über das Azure-Portal skalieren (siehe [Elastisches Skalieren eines Cassandra-API-Kontos für Azure Cosmos DB](scale-account-throughput.md)) oder eine Wiederholungsrichtlinie implementieren.

Informationen zu Java finden Sie unter den Beispielen für Wiederholungslogik für den [v3.x-Treiber](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) und den [v4.x-Treiber](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4). Weitere Informationen finden Sie auch unter [Cassandra-Erweiterungen für Java in Azure Cosmos DB](https://github.com/Azure/azure-cosmos-cassandra-extensions).

### <a name="overloadedexception-despite-sufficient-throughput"></a>OverloadedException trotz ausreichendem Durchsatz

Das System scheint Anforderungen zu drosseln, obwohl genügend Durchsatz für das Anforderungsvolumen oder die verbrauchten Anforderungseinheiten bereitgestellt wird. Es gibt zwei mögliche Ursachen:

- **Vorgänge auf Schemaebene**: Die Cassandra-API implementiert ein Budget für den Systemdurchsatz bei Vorgängen auf Schemaebene (CREATE TABLE, ALTER TABLE, DROP TABLE). Dieses Budget sollte für Schemavorgänge in einem Produktionssystem ausreichen. Bei einer sehr großen Anzahl von Vorgängen auf Schemaebene wird dieser Grenzwert möglicherweise überschritten.

  Da das Budget nicht benutzergesteuert ist, sollte ggf. die Anzahl der ausgeführten Schemavorgänge verringert werden. Wenn das Problem dadurch nicht behoben wird oder diese Aktion für Ihre Workload nicht möglich ist, [erstellen Sie eine Azure-Supportanfrage](../../azure-portal/supportability/how-to-create-azure-support-request.md).

- **Datenschiefe**: Wenn Durchsatz über die Cassandra-API bereitgestellt wird, wird er gleichmäßig auf physische Partitionen aufgeteilt, und jede physische Partition hat eine Obergrenze. Wenn bei Ihnen eine große Datenmenge von einer bestimmten Partition eingefügt oder abgefragt wird, kann es trotz Bereitstellung eines hohen Gesamtdurchsatzes (Anforderungseinheiten) für diese Tabelle zu einer Ratenbegrenzung kommen.

  Überprüfen Sie Ihr Datenmodell, und vergewissern Sie sich, dass keine übermäßige Schiefe vorhanden ist, die zu heißen Partitionen führen kann.

## <a name="intermittent-connectivity-errors-java"></a>Zeitweilige Verbindungsfehler (Java)

Die Verbindung wird unerwartet getrennt, oder es tritt ein Timeout auf.

Die Apache Cassandra-Treiber für Java bieten zwei native Richtlinien für die erneute Verbindungsherstellung: `ExponentialReconnectionPolicy` und `ConstantReconnectionPolicy`. Der Standardwert ist `ExponentialReconnectionPolicy`. Für die Cassandra-API von Azure Cosmos DB wird jedoch `ConstantReconnectionPolicy` mit einer Verzögerung von zwei Sekunden empfohlen.

Weitere Informationen finden Sie in der [Dokumentation für den Java 4.x-Treiber](https://docs.datastax.com/en/developer/java-driver/4.9/manual/core/reconnection/), in der [Dokumentation für den Java 3.x-Treiber](https://docs.datastax.com/en/developer/java-driver/3.7/manual/reconnection/) oder in den Beispielen unter [Konfigurieren von ReconnectionPolicy für die Java-Treiber](#configure-reconnectionpolicy-for-the-java-driver).

## <a name="error-with-load-balancing-policy"></a>Fehler im Zusammenhang mit der Lastenausgleichsrichtlinie

Möglicherweise haben Sie eine Lastenausgleichsrichtlinie in v3.x des Java-DataStax-Treibers implementiert und dabei Code wie den folgenden verwendet:

```java
cluster = Cluster.builder()
        .addContactPoint(cassandraHost)
        .withPort(cassandraPort)
        .withCredentials(cassandraUsername, cassandraPassword)
        .withPoolingOptions(new PoolingOptions() .setConnectionsPerHost(HostDistance.LOCAL, 1, 2)
                .setMaxRequestsPerConnection(HostDistance.LOCAL, 32000).setMaxQueueSize(Integer.MAX_VALUE))
        .withSSL(sslOptions)
        .withLoadBalancingPolicy(DCAwareRoundRobinPolicy.builder().withLocalDc("West US").build())
        .withQueryOptions(new QueryOptions().setConsistencyLevel(ConsistencyLevel.LOCAL_QUORUM))
        .withSocketOptions(getSocketOptions())
        .build();
```

Wenn der Wert für `withLocalDc()` nicht dem Rechenzentrum am Kontaktpunkt entspricht, tritt möglicherweise der folgende vorübergehende Fehler auf: `com.datastax.driver.core.exceptions.NoHostAvailableException: All host(s) tried for query failed (no host was tried)`.

Implementieren Sie [CosmosLoadBalancingPolicy](https://github.com/Azure/azure-cosmos-cassandra-extensions/blob/master/driver-3/src/main/java/com/azure/cosmos/cassandra/CosmosLoadBalancingPolicy.java). Damit dies funktioniert, müssen Sie möglicherweise DataStax mithilfe des folgenden Codes aktualisieren:

```java
LoadBalancingPolicy loadBalancingPolicy = new CosmosLoadBalancingPolicy.Builder().withWriteDC("West US").withReadDC("West US").build();
```

## <a name="the-count-fails-on-a-large-table"></a>Fehler beim Zählvorgang für eine große Tabelle

Wenn `select count(*) from table` oder ein ähnlicher Befehl für eine große Anzahl von Zeilen ausgeführt wird, tritt ein Servertimeout auf.

Wenn Sie einen lokalen CQLSH-Client verwenden, ändern Sie die Einstellung `--connect-timeout` oder `--request-timeout` zu ändern. Weitere Informationen finden Sie unter [cqlsh: die CQL-Shell](https://cassandra.apache.org/doc/latest/cassandra/tools/cqlsh.html).

Sollte trotzdem weiterhin ein Timeout für den Zählvorgang auftreten, können Sie die Anzahl von Datensätzen über die Telemetriedaten des Azure Cosmos DB-Back-Ends abrufen. Wählen Sie hierzu im Azure-Portal auf der Registerkarte „Metriken“ die Metrik `document count` aus, und fügen Sie dann einen Filter für die Datenbank oder Sammlung (Entsprechung der Tabelle in Azure Cosmos DB) hinzu. In dem resultierenden Graphen können Sie dann auf den Zeitpunkt zeigen, für den Sie die Anzahl von Datensätzen ermitteln möchten.

:::image type="content" source="./media/troubleshoot-common-issues/metrics.png" alt-text="Metrikenansicht":::

## <a name="configure-reconnectionpolicy-for-the-java-driver"></a>Konfigurieren von ReconnectionPolicy für den Java-Treiber

### <a name="version-3x"></a>Version 3.x

Bei Version 3.x des Java-Treibers konfigurieren Sie die Richtlinie für Verbindungswiederholungen beim Erstellen eines Clusterobjekts:

```java
import com.datastax.driver.core.policies.ConstantReconnectionPolicy;

Cluster.builder()
  .withReconnectionPolicy(new ConstantReconnectionPolicy(2000))
  .build();
```

### <a name="version-4x"></a>Version 4.x

Bei Version 4.x des Java-Treibers konfigurieren Sie die Richtlinie für Verbindungswiederholungen durch Überschreiben der Einstellungen in der Datei `reference.conf`:

```xml
datastax-java-driver {
  advanced {
    reconnection-policy{
      # The driver provides two implementations out of the box: ExponentialReconnectionPolicy and
      # ConstantReconnectionPolicy. We recommend ConstantReconnectionPolicy for Cassandra API, with 
      # base-delay of 2 seconds.
      class = ConstantReconnectionPolicy
      base-delay = 2 second
    }
}
```

## <a name="enable-keep-alive-for-the-java-driver"></a>Aktivieren von Keep-Alive für den Java-Treiber

### <a name="version-3x"></a>Version 3.x

Bei Version 3.x des Java-Treibers legen Sie die Keep-Alive-Einstellung beim Erstellen eines Clusterobjekts fest. Sie müssen anschließend sicherstellen, dass Keep-Alive [im Betriebssystem aktiviert](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089) ist:

```java
import java.net.SocketOptions;
    
SocketOptions options = new SocketOptions();
options.setKeepAlive(true);
cluster = Cluster.builder().addContactPoints(contactPoints).withPort(port)
  .withCredentials(cassandraUsername, cassandraPassword)
  .withSocketOptions(options)
  .build();
```

### <a name="version-4x"></a>Version 4.x

Bei Version 4.x des Java-Treibers legen Sie die Keep-Alive-Einstellung durch Überschreiben der Einstellungen in `reference.conf` fest. Sie müssen anschließend sicherstellen, dass Keep-Alive [im Betriebssystem aktiviert](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089) ist:

```xml
datastax-java-driver {
  advanced {
    socket{
      keep-alive = true
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über [unterstützte Features](cassandra-support.md) in der Cassandra-API von Azure Cosmos DB.
- Erfahren Sie, wie Sie [vom nativen Apache Cassandra zur Cassandra-API von Azure Cosmos DB migrieren](migrate-data-databricks.md).
