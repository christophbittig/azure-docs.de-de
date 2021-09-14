---
title: Verhindern von Ratenbegrenzungsfehlern bei Vorgängen der Azure Cosmos DB-API für MongoDB
description: Erfahren Sie, wie Sie Ratenbegrenzungsfehler bei Vorgängen Ihrer Azure Cosmos DB-API für MongoDB mithilfe des SSR-Features (Server-Side Retry, serverseitige Wiederholung) vermeiden können.
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
ms.author: gahllevy
ms.openlocfilehash: 1e9067a87887c47229b8bba687535e2730c2ed1b
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123030902"
---
# <a name="prevent-rate-limiting-errors-for-azure-cosmos-db-api-for-mongodb-operations"></a>Verhindern von Ratenbegrenzungsfehlern bei Vorgängen der Azure Cosmos DB-API für MongoDB
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

Bei Vorgängen der Azure Cosmos DB-API für MongoDB treten möglicherweise Ratenbegrenzungsfehler (16500/429) auf, wenn sie den Durchsatzgrenzwert (in RUs) einer Sammlung überschreiten. 

Sie können das Feature für die serverseitige Wiederholung (Server-Side Retry, SSR) aktivieren, damit der Server diese Vorgänge automatisch wiederholt. Die Anforderungen werden nach einer kurzen Verzögerung für alle Sammlungen in Ihrem Konto wiederholt. Dieses Feature ist eine praktische Alternative zur Behandlung von Ratenbegrenzungsfehlern in der Clientanwendung.

## <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Navigieren Sie zu Ihrem Konto für die Azure Cosmos DB-API für MongoDB.

1. Wechseln Sie im Abschnitt **Einstellungen** zum Bereich **Features**.

1. Wählen Sie **Serverseitige Wiederholung** aus.

1. Klicken Sie auf **Aktivieren**, um dieses Feature für alle Sammlungen in Ihrem Konto zu aktivieren.

:::image type="content" source="./media/prevent-rate-limiting-errors/portal-features-server-side-retry.png" alt-text="Screenshot des Features zur serverseitigen Wiederholung für die Azure Cosmos DB-API für MongoDB":::

## <a name="use-the-azure-cli"></a>Verwenden der Azure-CLI

1. Überprüfen Sie, ob SSR für Ihr Konto bereits aktiviert ist:

   ```azurecli-interactive
   az cosmosdb show --name accountname --resource-group resourcegroupname
   ```

1. **Aktivieren** Sie SSR für alle Sammlungen in Ihrem Datenbankkonto. Es kann bis zu 15 Minuten dauern, bis diese Änderung wirksam wird.

   ```azurecli-interactive
   az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo DisableRateLimitingResponses
   ```

1. Mit dem folgenden Befehl wird die serverseitige Wiederholung für alle Sammlungen in Ihrem Datenbankkonto **deaktiviert**, indem `DisableRateLimitingResponses` aus der Liste der Funktionen entfernt wird. Es kann bis zu 15 Minuten dauern, bis diese Änderung wirksam wird.

   ```azurecli-interactive
   az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo
   ```

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="how-are-requests-retried"></a>Wie werden Anforderungen wiederholt?

Anforderungen werden fortlaufend (also immer wieder) wiederholt, bis ein 60-Sekunden-Timeout erreicht wird. Bei Erreichen des Timeouts empfängt der Client eine [Ausnahme vom Typ „ExceededTimeLimit“ (50)](error-codes-solutions.md).

### <a name="how-can-i-monitor-the-effects-of-a-server-side-retry"></a>Wie kann ich die Auswirkungen einer serverseitigen Wiederholung überwachen?

Sie können im Bereich mit den Cosmos DB-Metriken die serverseitig wiederholten Ratenbegrenzungsfehler (429) anzeigen. Beachten Sie, dass diese Fehler nicht an den Client gesendet werden, wenn SSR aktiviert ist, da sie serverseitig behandelt und wiederholt werden.

Sie können in Ihren [Cosmos DB-Ressourcenprotokollen](../cosmosdb-monitor-resource-logs.md) nach Protokolleinträgen suchen, die *estimatedDelayFromRateLimitingInMilliseconds* enthalten.

### <a name="will-server-side-retry-affect-my-consistency-level"></a>Wirkt sich die serverseitige Wiederholung auf meine Konsistenzebene aus?

Die serverseitige Wiederholung hat keine Auswirkung auf die Konsistenz einer Anforderung. Anforderungen werden serverseitig wiederholt, wenn bei ihnen eine Ratenbegrenzung vorliegt (Fehler vom Typ 429).

### <a name="does-server-side-retry-affect-any-type-of-error-that-my-client-might-receive"></a>Hat die serverseitige Wiederholung Auswirkungen auf Fehlertypen, die ggf. von meinem Client empfangen werden?

Nein, die serverseitige Wiederholung hat nur Auswirkungen auf Ratenbegrenzungsfehler (429), indem diese serverseitig wiederholt werden. Dieses Feature sorgt dafür, dass Ratenbegrenzungsfehler nicht in der Clientanwendung behandelt werden müssen. Alle [anderen Fehler](error-codes-solutions.md) werden an den Client gesendet.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Problembehandlung bei häufigen Fehlern finden Sie in diesem Artikel:

* [Behandeln häufiger Probleme in der Azure Cosmos DB-API für MongoDB](error-codes-solutions.md)

Versuchen Sie, die Kapazitätsplanung für eine Migration zu Azure Cosmos DB durchzuführen? Sie können Informationen zu Ihrem vorhandenen Datenbankcluster für die Kapazitätsplanung verwenden.
* Wenn Sie nur die Anzahl der virtuellen Kerne und Server in Ihrem vorhandenen Datenbankcluster kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](../convert-vcore-to-request-unit.md) 
* Wenn Sie die typischen Anforderungsraten für Ihre aktuelle Datenbank-Workload kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mit dem Azure Cosmos DB-Kapazitätsplaner](estimate-ru-capacity-planner.md)
