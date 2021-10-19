---
title: Verhindern von Ratenbegrenzungsfehlern bei der Azure Cosmos DB-API für Cassandra
description: Verhindern Sie Ratenbegrenzungsfehler bei Vorgängen Ihrer Azure Cosmos DB-API für Cassandra mithilfe des SSR-Features (Server-Side Retry, serverseitige Wiederholung).
author: dileepraotv-github
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/11/2021
ms.author: turao
ms.openlocfilehash: 469dc3f87bb7c783f2c3138e2bcf592c85312006
ms.sourcegitcommit: af303268d0396c0887a21ec34c9f49106bb0c9c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2021
ms.locfileid: "129754975"
---
# <a name="prevent-rate-limiting-errors-for-azure-cosmos-db-api-for-cassandra-operations"></a>Verhindern von Ratenbegrenzungsfehlern bei Vorgängen der Azure Cosmos DB-API für Cassandra
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

Die Kosten sämtlicher Datenbankvorgänge werden von Azure Cosmos DB normalisiert und als Anforderungseinheiten (Request Units, RUs) ausgedrückt. Anforderungseinheiten stellen praktisch die „Währung“ zur Abstrahierung von Systemressourcen wie CPU, IOPS und Arbeitsspeicher dar, die zum Ausführen der von Azure Cosmos DB unterstützten Datenbankvorgänge erforderlich sind.

Bei Vorgängen der Azure Cosmos DB-API für Cassandra treten möglicherweise Ratenbegrenzungsfehler (OverloadedException/429) auf, wenn sie das Durchsatzlimit (in RUs) einer Tabelle überschreiten. Dies kann clientseitig gehandhabt werden, wie es in der Wiederholungsrichtlinie im [Dokument mit Empfehlungen für die Cassandra-API](https://devblogs.microsoft.com/cosmosdb/cassandra-api-java/#retry-policy) beschrieben ist. Wenn die Wiederholungsrichtlinie des Clients nicht implementiert werden kann, um den Ratenbegrenzungsfehler zu behandeln, können Sie das SSR-Feature (Server-Side Retry, serverseitige Widerholung) verwenden. Hiermit werden Vorgänge, die das Durchsatzlimit einer Tabelle überschreiten, nach einer kurzen Verzögerung automatisch wiederholt. Dies ist eine Einstellung auf Kontoebene und gilt für alle Schlüsselräume und Tabellen im Konto.

## <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

2. Navigieren Sie zu Ihrem Konto für die Azure Cosmos DB-API für Cassandra.

3. Wechseln Sie im Abschnitt **Einstellungen** zum Bereich **Features**.

4. Wählen Sie **Serverseitige Wiederholung** aus.

5. Klicken Sie auf **Aktivieren**, um dieses Feature für alle Sammlungen in Ihrem Konto zu aktivieren.

:::image type="content" source="./media/prevent-rate-limiting-errors/prevent-rate-limiting-errors.png" alt-text="Screenshot des Features zur serverseitigen Wiederholung für die Azure Cosmos DB-API für Cassandra":::

## <a name="use-the-azure-cli"></a>Verwenden der Azure-CLI

1. Überprüfen Sie, ob SSR für Ihr Konto bereits aktiviert ist:

   ```azurecli-interactive
   az cosmosdb show --name accountname --resource-group resourcegroupname
   ```

2. **Aktivieren** Sie SSR für alle Tabellen in Ihrem Datenbankkonto. Es kann bis zu 15 Minuten dauern, bis diese Änderung wirksam wird.

   ```azurecli-interactive
   az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableCassandra DisableRateLimitingResponses
   ```

3. Mit dem folgenden Befehl wird die serverseitige Wiederholung für alle Tabellen in Ihrem Datenbankkonto **deaktiviert**, indem `DisableRateLimitingResponses` aus der Liste der Funktionen entfernt wird. Es kann bis zu 15 Minuten dauern, bis diese Änderung wirksam wird.

   ```azurecli-interactive
   az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableCassandra
   ```

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="how-are-requests-retried"></a>Wie werden Anforderungen wiederholt?

Anforderungen werden fortlaufend (also immer wieder) wiederholt, bis ein 60-Sekunden-Timeout erreicht wird. Wenn das Timeout erreicht ist, erhält der Client entsprechend einen Timeoutfehler für den Lese- oder Schreibvorgang.

### <a name="when-is-ssr-most-beneficial"></a>Wann ist SSR am nützlichsten?

Die serverseitige Wiederholung (Server-Side Retry, SSR) ist besonders nützlich, wenn eine plötzliche Spitze über einen kurzen Zeitraum von weniger als einer Minute auftritt, bei der Drosselungsfehler vermieden werden können. Wenn die Arbeitsauslastung zugenommen hat und konstant oberhalb des angegebenen RU-Werts liegt, wird SSR nicht viel nützen. Hier wird vorgeschlagen, die Anzahl der RUs entsprechend zu erhöhen.

### <a name="suggested-client-side-settings"></a>Welche clientseitigen Einstellungen werden empfohlen?

Nachdem SSR aktiviert wurde, sollte die Client-App das Lesetimeout auf einen höheren Wert als die Wiederholungseinstellung des Servers von 60 Sekunden festlegen. Um auf der sichereren Seite zu sein, werden 90 Sekunden empfohlen.

SocketOptions setReadTimeoutMillis DefaultDriverOption.REQUEST_TIMEOUT


### <a name="how-can-i-monitor-the-effects-of-a-server-side-retry"></a>Wie kann ich die Auswirkungen einer serverseitigen Wiederholung überwachen?

Sie können im Bereich mit den Cosmos DB-Metriken die serverseitig wiederholten Ratenbegrenzungsfehler (429) anzeigen. Diese Fehler werden nicht an den Client gesendet, wenn SSR aktiviert ist, da sie serverseitig behandelt und wiederholt werden.

Sie können in Ihren [Cosmos DB-Ressourcenprotokollen](../cosmosdb-monitor-resource-logs.md) nach Protokolleinträgen suchen, die *estimatedDelayFromRateLimitingInMilliseconds* enthalten.

### <a name="will-server-side-retry-affect-my-consistency-level"></a>Wirkt sich die serverseitige Wiederholung auf meine Konsistenzebene aus?

Die serverseitige Wiederholung hat keine Auswirkung auf Konsistenzebenen. Anforderungen werden serverseitig wiederholt, wenn bei ihnen eine Ratenbegrenzung vorliegt (Fehler 429).

### <a name="does-server-side-retry-affect-any-type-of-error-that-my-client-might-receive"></a>Hat die serverseitige Wiederholung Auswirkungen auf Fehlertypen, die ggf. von meinem Client empfangen werden?

Nein, die serverseitige Wiederholung hat nur Auswirkungen auf Ratenbegrenzungsfehler (429), indem diese serverseitig wiederholt werden. Dieses Feature sorgt dafür, dass Ratenbegrenzungsfehler nicht in der Clientanwendung behandelt werden müssen. Alle [anderen Fehler](troubleshoot-common-issues.md) werden an den Client gesendet.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Problembehandlung bei häufigen Fehlern finden Sie in diesem Artikel:

* [Behandeln häufiger Probleme in der Cassandra-API von Azure Cosmos DB](troubleshoot-common-issues.md)


Informationen zur Durchsatzbereitstellung in Azure Cosmos DB finden Sie in den folgenden Artikeln:

* [Durchsatz und Anforderungseinheiten in Azure Cosmos DB](../request-units.md)
* [Bereitstellen des Durchsatzes für Container und Datenbanken](../how-to-provision-throughput-cassandra.md) 
* [Bewährte Methoden für Partitionsschlüssel](../cassandra-partitioning.md)

