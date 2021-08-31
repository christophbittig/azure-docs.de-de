---
title: Skalieren einer Azure Cache for Redis-Instanz
description: Erfahren Sie, wie Sie Ihre Azure Cache for Redis-Instanzen über das Azure-Portal sowie mithilfe von Tools wie Azure PowerShell und der Azure-Befehlszeilenschnittstelle skalieren.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 0b76f98da4823341d5ec0f32c1d8f65a1c37fade
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355542"
---
# <a name="scale-an-azure-cache-for-redis-instance"></a>Skalieren einer Azure Cache for Redis-Instanz

Für Azure Cache for Redis stehen verschiedene Cacheangebote bereit, die Flexibilität bei der Auswahl von Cachegröße und -funktionen bieten. Bei einem Basic-, Standard- oder Premium-Cache können Sie nach dem Erstellen die Größe und den Tarif ändern, um ihn an die Anforderungen Ihrer Anwendung anzupassen. In diesem Artikel wird erläutert, wie Sie Ihren Cache im Azure-Portal mithilfe von Tools wie Azure PowerShell und der Azure-Befehlszeilenschnittstelle skalieren.

## <a name="when-to-scale"></a>Zeitpunkt für die Skalierung

Sie können mithilfe der [Überwachungsfunktionen](cache-how-to-monitor.md) von Azure Cache for Redis die Integrität und Leistung Ihres Caches überwachen. Verwenden Sie diese Informationen, um zu bestimmen, wann der Cache skaliert werden soll.

Überwachen Sie die folgenden Metriken überwachen, um herauszufinden, ob eine Skalierung notwendig ist.

- Redis-Serverlast
  - Der Redis-Server ist ein Singlethreadprozess. Eine hohe Redis-Serverauslastung bedeutet, dass der Server nicht mit den Anforderungen aller Clientverbindungen Schritt halten kann. In solchen Situationen ist es hilfreich, das Clustering zu aktivieren oder die Anzahl der Shards zu erhöhen, sodass die Mehraufwandfunktionen auf mehrere Redis-Prozesse verteilt werden. Clustering und höhere Shardanzahl verteilen die TLS-Verschlüsselung und -Entschlüsselung sowie TLS-Verbindungen und -Verbindungstrennungen.
  - Weitere Informationen finden Sie unter [Einrichten des Clusterings](cache-how-to-premium-clustering.md#set-up-clustering).
- Speicherauslastung
  - Eine hohe Speicherauslastung zeigt an, dass Ihre Datengröße für die aktuelle Cachegröße zu groß ist. Erwägen Sie die Skalierung auf eine Cachegröße mit größerem Arbeitsspeicher.
- Clientverbindungen
  - Für jede Cachegröße ist die Anzahl der Clientverbindungen, die unterstützt werden können, begrenzt. Wenn Ihre Clientverbindungen dem Grenzwert für die Cachegröße nahe kommen, sollten Sie das Hochskalieren auf eine größere Ebene oder das Aufskalieren erwägen, um Clustering zu aktivieren und die Shardanzahl zu erhöhen. Ihre Wahl hängt von der Redis-Serverauslastung und Arbeitsspeicherauslastung ab.
  - Weitere Informationen zu Verbindungsgrenzwerten in Abhängigkeit von der Cachegröße finden Sie unter [Häufig gestellte Fragen zur Azure Cache for Redis-Planung](./cache-planning-faq.yml).
- Netzwerkbandbreite
  - Wenn der Redis-Server die verfügbare Bandbreite überschreitet, kann bei Clientanforderungen ein Timeout auftreten, weil der Server Daten nicht schnell genug an den Client pushen kann. Aktivieren Sie die Metriken „Cache Read“ (Cachelesevorgänge) und „Cache Write“ (Cacheschreibvorgänge) können, um festzustellen, wie viel Bandbreite auf der Serverseite verwendet wird. Wenn Ihr Redis-Server die verfügbare Netzwerkbandbreite überschreitet, sollten Sie eine Hochskalierung auf eine größere Cachegröße mit höherer Netzwerkbandbreite in Betracht ziehen.
  - Weitere Informationen zur verfügbaren Netzwerkbandbreite in Abhängigkeit von der Cachegröße finden Sie unter [Häufig gestellte Fragen zur Planung von Azure Cache for Redis](./cache-planning-faq.yml).

Wenn Sie feststellen, dass der Cache die Anforderungen der Anwendung nicht mehr erfüllt, können Sie auf einen geeigneten Cachetarif für Ihre Anwendung skalieren. Sie können einen größeren oder kleineren Cache auswählen, der Ihren Anforderungen entspricht.

Weitere Informationen, wie Sie ermitteln, welcher Cachetarif geeignet ist, finden Sie unter [Auswählen der richtigen Ebene](cache-overview.md#choosing-the-right-tier) und [Häufig gestellte Fragen zur Planung von Azure Cache for Redis](./cache-planning-faq.yml).

## <a name="scale-a-cache"></a>Skalieren eines Caches

Zum Skalieren Ihres Caches [navigieren Sie zum Cache](cache-configure.md#configure-azure-cache-for-redis-settings) im [Azure-Portal](https://portal.azure.com) und wählen im Menü **Ressource** den Eintrag **Skalieren** aus.

![Skalieren](./media/cache-how-to-scale/redis-cache-scale-menu.png)

Wählen Sie auf der linken Seite unter **Tarif auswählen** und **Auswählen** den gewünschten Tarif aus.

:::image type="content" source="media/cache-how-to-scale/redis-cache-pricing-tier-blade.png" alt-text="Screenshot des Redis-Cachetarifs":::

Sie können mit den folgenden Einschränkungen zu einem anderen Tarif wechseln:

- Sie können keine Skalierung von einem höheren Tarif auf einen niedrigeren Tarif vornehmen.
  - Eine Skalierung von einem **Premium**-Cache auf einen niedrigeren **Standard**- oder **Basic**-Cache ist nicht möglich.
  - Ein **Standard**-Cache kann nicht auf einen niedrigeren **Basic**-Cache skaliert werden.
- Ein **Basic**-Cache kann auf einen **Standard**-Cache skaliert werden, die Größe kann jedoch nicht gleichzeitig geändert werden. Wenn Sie eine andere Größe benötigen, können Sie anschließend einen Skalierungsvorgang auf die gewünschte Größe durchführen.
- Ein **Basic**-Cache kann nicht direkt auf einen **Premium**-Cache skaliert werden. Skalieren Sie zunächst in einem ersten Skalierungsvorgang von **Basic** auf **Standard** und dann im nächsten Skalierungsvorgang von **Standard** auf **Premium**.
- Von einer größeren Größe kann nicht auf **C0 (250 MB)** herunterskaliert werden. Allerdings ist ein Herunterskalieren auf eine beliebige andere Größe innerhalb desselben Tarifs möglich. Beispielsweise können Sie von „C5 Standard“ auf „C1 Standard“ herunterskalieren.

Während der Skalierung des Caches auf den neuen Tarif wird links unter **Azure Cache for Redis** der Status **Wird skaliert** angezeigt.

:::image type="content" source="media/cache-how-to-scale/redis-cache-scaling.png" alt-text="Redis-Cacheskalierung":::

Wenn die Skalierung abgeschlossen ist, ändert sich der Status von **Wird skaliert** zu **Wird ausgeführt**.

## <a name="how-to-automate-a-scaling-operation"></a>Automatisieren eines Skalierungsvorgangs

Sie können Ihre Cache-Instanzen im Azure-Portal skalieren. Außerdem können Sie die Skalierung mithilfe von PowerShell-Cmdlets, Azure CLI und mithilfe der Microsoft Azure Management Libraries (MAML) ausführen.

- [Skalieren mithilfe von PowerShell](#scale-using-powershell)
- [Skalieren über die Azure-Befehlszeilenschnittstelle](#scale-using-azure-cli)
- [Skalieren mithilfe der Microsoft Azure-Verwaltungsbibliotheken](#scale-using-maml)

### <a name="scale-using-powershell"></a>Skalieren mithilfe von PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Sie können die Azure Cache for Redis-Instanzen mithilfe von PowerShell skalieren. Dazu verwenden Sie das Cmdlet [Set-AzRedisCache](/powershell/module/az.rediscache/set-azrediscache) und ändern die Eigenschaften `Size`, `Sku` oder `ShardCount`. Das folgende Beispiel veranschaulicht, wie ein Cache mit Namen `myCache` zu einem 2,5-GB-Cache skaliert wird.

```powershell
   Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB
```

Weitere Informationen zum Skalieren mithilfe von PowerShell finden Sie unter [Skalieren eines Azure Cache for Redis mit PowerShell](cache-how-to-manage-redis-cache-powershell.md#scale).

### <a name="scale-using-azure-cli"></a>Skalieren über die Azure-Befehlszeilenschnittstelle

Wenn Sie die Azure Cache for Redis-Instanzen über die Azure-Befehlszeilenschnittstelle (CLI) skalieren möchten, rufen Sie den Befehl `azure rediscache set` auf und übergeben Ihre Konfigurationsänderungen. Abhängig vom gewünschten Skalierungsvorgang zählen dazu die neue Größe, die SKU oder die Clustergröße.

Weitere Informationen zum Skalieren über die Azure-Befehlszeilenschnittstelle finden Sie unter [Ändern der Einstellungen eines vorhandenen Azure Cache for Redis](cache-manage-cli.md#scale).

### <a name="scale-using-maml"></a>Skalieren mithilfe der Microsoft Azure-Verwaltungsbibliotheken

Wenn Sie die Azure Cache for Redis-Instanzen mithilfe der [Microsoft Azure-Verwaltungsbibliotheken](https://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/) (MAML, Microsoft Azure Management Libraries) skalieren möchten, rufen Sie die Methode `IRedisOperations.CreateOrUpdate` auf und übergeben die neue Größe für `RedisProperties.SKU.Capacity`.

```csharp
    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://azure.microsoft.com/documentation/articles/cache-configure/#access-keys
        string token = GetAuthorizationHeader();

        TokenCloudCredentials creds = new TokenCloudCredentials(subscriptionId,token);

        RedisManagementClient client = new RedisManagementClient(creds);
        var redisProperties = new RedisProperties();

        // To scale, set a new size for the redisSKUCapacity parameter.
        redisProperties.Sku = new Sku(redisSKUName,redisSKUFamily,redisSKUCapacity);
        redisProperties.RedisVersion = redisVersion;
        var redisParams = new RedisCreateOrUpdateParameters(redisProperties, redisCacheRegion);
        client.Redis.CreateOrUpdate(resourceGroupName,cacheName, redisParams);
    }
```

Weitere Informationen finden Sie im Beispiel [Verwalten von Azure Cache for Redis mithilfe von Microsoft Azure-Verwaltungsbibliotheken](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML).

## <a name="scaling-faq"></a>Häufig gestellte Fragen zur Skalierung

Die folgende Liste enthält Antworten auf häufig gestellte Fragen zur Skalierung von Azure Cache for Redis-Instanzen.

- [Kann ich eine Skalierung auf einen Premium-Cache, aus diesem oder innerhalb von diesem vornehmen?](#can-i-scale-to-from-or-within-a-premium-cache)
- [Muss ich nach dem Skalieren den Namen oder die Zugriffsschlüssel für den Cache ändern?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
- [Wie funktioniert die Skalierung?](#how-does-scaling-work)
- [Gehen während der Skalierung Daten aus dem Cache verloren?](#will-i-lose-data-from-my-cache-during-scaling)
- [Hat die Skalierung Auswirkungen auf meine benutzerdefinierte Einstellung für Datenbanken?](#is-my-custom-databases-setting-affected-during-scaling)
- [Ist der Cache während der Skalierung verfügbar?](#will-my-cache-be-available-during-scaling)
- [Gibt es Skalierungseinschränkungen bei Verwendung der Georeplikation?](#are-there-scaling-limitations-with-geo-replication)
- [Vorgänge, die nicht unterstützt werden](#operations-that-arent-supported)
- [Wie lange dauert die Skalierung?](#how-long-does-scaling-take)
- [Woher weiß ich, dass die Skalierung abgeschlossen ist?](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>Kann ich eine Skalierung auf einen Premium-Cache, aus diesem oder innerhalb von diesem vornehmen?

- Eine Skalierung von einem **Premium**-Cache auf die niedrigeren Tarife **Basic** oder **Standard** ist nicht möglich.
- Eine Skalierung von einem bestimmten **Premium** -Cachetarif zu einem anderen ist jedoch möglich.
- Ein **Basic**-Cache kann nicht direkt auf einen **Premium**-Cache skaliert werden. Skalieren Sie zunächst in einem ersten Skalierungsvorgang von **Basic** auf **Standard** und dann in einem späteren Skalierungsvorgang von **Standard** auf **Premium**.
- Wenn Sie beim Erstellen des **Premium** -Caches die Clusterunterstützung aktiviert haben, können Sie die [Clustergröße ändern](cache-how-to-premium-clustering.md#cluster-size). Wenn der Cache ohne aktiviertes Clustering erstellt wurde, können Sie das Clustering zu einem späteren Zeitpunkt konfigurieren.

Weitere Informationen finden Sie unter [Konfigurieren von Clustern für Azure Cache for Redis vom Typ „Premium“](cache-how-to-premium-clustering.md).

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>Muss ich nach dem Skalieren den Namen oder die Zugriffsschlüssel für den Cache ändern?

Nein, Cachename und -schlüssel bleiben während eines Skalierungsvorgangs unverändert.

### <a name="how-does-scaling-work"></a>Wie funktioniert die Skalierung?

- Wenn Sie einen **Basic**-Cache auf eine andere Größe skalieren, wird er heruntergefahren, und es wird ein neuer Cache mit der neuen Größe bereitgestellt. Während dieser Zeit ist der Cache nicht verfügbar, und alle Daten im Cache gehen verloren.
- Wenn Sie einen **Basic**-Cache auf einen **Standard**-Cache skalieren, wird ein Replikatcache bereitgestellt, und die Daten werden aus dem primären Cache in den Replikatcache kopiert. Der Cache bleibt während des Skalierungsvorgangs verfügbar.
- Wenn Sie einen **Standard**-Cache auf eine andere Größe oder auf einen **Premium**-Cache skalieren, wird eines der Replikate heruntergefahren und mit der neuen Größe erneut bereitgestellt, und die Daten werden übertragen. Anschließend führt das andere Replikat ein Failover durch, bevor es erneut bereitgestellt wird. Dieser Vorgang ähnelt dem, der beim Ausfall eines Cacheknotens durchgeführt wird.
- Wenn Sie einen gruppierten Cache aufskalieren, werden neue Shards bereitgestellt und dem Redis-Servercluster hinzugefügt. Die Daten werden dann über alle Shards hinweg neu horizontal partitioniert.
- Wenn Sie einen gruppierten Cache abskalieren, werden die Daten zuerst neu horizontal partitioniert, und anschließend wird die Clustergröße auf die Anzahl erforderlicher Shards reduziert.

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>Gehen während der Skalierung Daten aus dem Cache verloren?

- Wenn Sie einen **Basic**-Cache auf eine neue Größe skalieren, gehen alle Daten verloren, und der Cache ist während des Skalierungsvorgangs nicht verfügbar.
- Wenn Sie einen **Basic**-Cache auf einen **Standard**-Cache skalieren, werden die Daten im Cache in der Regel beibehalten.
- Wenn Sie einen **Standard**-Cache auf eine größere Größe oder auf einen höheren Tarif bzw. ein **Premium**-Cache auf eine größere Größe skalieren, bleiben normalerweise alle Daten erhalten. Wenn Sie einen Standard- oder Premium-Cache auf eine kleinere Größe skalieren, können Daten verloren gehen, wenn die Datengröße die neue kleinere Größe beim Herunterskalieren überschreitet. Wenn Daten beim Herunterskalieren verloren gehen, werden die Schlüssel mithilfe der Entfernungsrichtlinie [allkeys-lru](https://redis.io/topics/lru-cache) entfernt.

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>Hat die Skalierung Auswirkungen auf meine benutzerdefinierte Einstellung für Datenbanken?

Wenn Sie bei der Cacheerstellung einen benutzerdefinierten Wert für die Einstellung `databases` konfiguriert haben, gelten bei einigen Tarifen andere [Grenzwerte für Datenbanken](cache-configure.md#databases). Hier sind einige Aspekte aufgeführt, die beim Skalieren in diesem Szenario wichtig sind:

- Wenn Sie auf einen Tarif mit einem niedrigeren `databases` -Grenzwert skalieren als beim aktuellen Tarif:
  - Wenn Sie die Standardanzahl für `databases` verwenden (sie beträgt bei allen Tarifen 16), gehen keine Daten verloren.
  - Wenn Sie eine benutzerdefinierte Anzahl für `databases` verwenden, die innerhalb der Grenzwerte für den Tarif liegt, auf den Sie skalieren, wird diese `databases`-Einstellung beibehalten, und es gehen keine Daten verloren.
  - Wenn Sie eine benutzerdefinierte Anzahl für `databases` verwenden, die die Grenzwerte des neuen Tarifs überschreitet, wird die `databases`-Einstellung auf die Grenzwerte des neuen Tarifs herabgesetzt, und alle Daten in den entfernten Datenbanken gehen verloren.
- Wenn Sie auf einen Tarif mit dem gleichen oder einem höheren `databases`-Grenzwert skalieren wie beim aktuellen Tarif, wird Ihre `databases`-Einstellung beibehalten, und es gehen keine Daten verloren.

Für Caches der Tarife „Standard“ und „Premium“ gilt zwar eine SLA (Service Level Agreement, Vereinbarung zum Servicelevel) von 99,9 % für die Verfügbarkeit, aber keine SLA zum Datenverlust.

### <a name="will-my-cache-be-available-during-scaling"></a>Ist der Cache während der Skalierung verfügbar?

- **Standard**- und **Premium**-Caches bleiben während des Skalierungsvorgangs verfügbar. Es können jedoch Verbindungsunterbrechungen während der Skalierung von Standard- und Premium-Caches und auch während der Skalierung von Basic- auf Standard-Caches auftreten. Diese Verbindungsunterbrechungen sollten voraussichtlich kurz sein, und die Redis-Clients können in der Regel ihre Verbindung sofort erneut herstellen.
- **Basic**-Caches sind während der Skalierung auf eine andere Größe offline. Basic-Caches bleiben bei einer Skalierung von **Basic** auf **Standard** verfügbar, es können aber kurze Verbindungsunterbrechungen auftreten. Wenn Verbindungsunterbrechungen auftreten, können die Redis-Clients in der Regel ihre Verbindung sofort erneut herstellen.

### <a name="are-there-scaling-limitations-with-geo-replication"></a>Gibt es Skalierungseinschränkungen bei Verwendung der Georeplikation?

Wenn die Georeplikation konfiguriert ist, stellen Sie möglicherweise fest, dass Sie einen Cache nicht skalieren oder die Shards in einem Cluster nicht ändern können. Eine Georeplikationsverknüpfung zwischen zwei Caches hindert Sie an Skalierungsvorgängen oder an der Änderung der Anzahl von Shards in einem Cluster. Wenn Sie diese Befehle verwenden möchten, müssen Sie zuerst die Verknüpfung für den Cache aufheben. Weitere Informationen finden Sie unter [Vorgehensweise zum Konfigurieren der Georeplikation für Azure Redis Cache](cache-how-to-geo-replication.md).

### <a name="operations-that-arent-supported"></a>Vorgänge, die nicht unterstützt werden

- Sie können keine Skalierung von einem höheren Tarif auf einen niedrigeren Tarif vornehmen.
  - Eine Skalierung von einem **Premium**-Cache auf einen niedrigeren **Standard**- oder **Basic**-Cache ist nicht möglich.
  - Ein **Standard**-Cache kann nicht auf einen niedrigeren **Basic**-Cache skaliert werden.
- Ein **Basic**-Cache kann auf einen **Standard**-Cache skaliert werden, die Größe kann jedoch nicht gleichzeitig geändert werden. Wenn Sie eine andere Größe benötigen, können Sie später einen Skalierungsvorgang auf die gewünschte Größe durchführen.
- Ein **Basic**-Cache kann nicht direkt auf einen **Premium**-Cache skaliert werden. Skalieren Sie zunächst in einem ersten Skalierungsvorgang von **Basic** auf **Standard** und dann in einem späteren Vorgang von **Standard** auf **Premium**.
- Von einer größeren Größe kann nicht auf **C0 (250 MB)** herunterskaliert werden.

Wenn bei einem Skalierungsvorgang ein Fehler auftritt, versucht der Dienst, den Vorgang rückgängig zu machen, und der Cache wird auf die ursprüngliche Größe zurückgesetzt.

### <a name="how-long-does-scaling-take"></a>Wie lange dauert die Skalierung?

Die Skalierungsdauer hängt von einigen Faktoren ab. Hier sind einige Faktoren, die sich auf die Dauer der Skalierung auswirken können.

- Datenmenge: Die Replikation größerer Datenmengen dauert länger.
- Hohe Schreibanforderungen: Eine höhere Anzahl von Schreibvorgängen bedeutet, dass mehr Daten knoten- oder shardübergreifend repliziert werden.
- Hohe Serverauslastung: Eine höhere Serverauslastung bedeutet, dass der Redis-Server ausgelastet ist und über begrenzte CPU-Zyklen verfügt, um die Neuverteilung der Daten abzuschließen.

Wenn Sie einen Cache ohne Daten skalieren, dauert es im Allgemeinen etwa 20 Minuten. Bei gruppierten Caches dauert die Skalierung ungefähr 20 Minuten pro Shard bei minimaler Datenmenge.

### <a name="how-can-i-tell-when-scaling-is-complete"></a>Woher weiß ich, dass die Skalierung abgeschlossen ist?

Im Azure-Portal können Sie den Fortschritt der Skalierung anzeigen. Wenn die Skalierung abgeschlossen ist, ändert sich der Status des Caches zu **Wird ausgeführt**.

<!-- IMAGES -->

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png