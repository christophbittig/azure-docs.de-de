---
title: Integrierter Azure Cosmos DB-Cache
description: Der integrierte Azure Cosmos DB-Cache ist ein In-Memory-Cache, der Sie dabei unterstützt, überschaubare Kosten und niedrige Latenzen bei wachsendem Anforderungsvolumen zu gewährleisten.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/26/2021
ms.author: tisande
ms.openlocfilehash: 2642f1e85e12ce0251e9b7bfff84b5d468a342d2
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/07/2021
ms.locfileid: "113361402"
---
# <a name="azure-cosmos-db-integrated-cache---overview-preview"></a>Übersicht: Integrierter Azure Cosmos DB-Cache (Vorschauversion)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Der integrierte Azure Cosmos DB-Cache ist ein In-Memory-Cache, der Sie dabei unterstützt, überschaubare Kosten und niedrige Latenzen bei wachsendem Anforderungsvolumen zu gewährleisten. Die Einrichtung des integrierten Caches ist einfach, und Sie müssen keine Zeit damit verbringen, benutzerdefinierten Code für die Cacheinvalidierung oder zum Verwalten der Back-End-Infrastruktur zu schreiben. Ihr integrierter Cache nutzt ein [dediziertes Gateway](dedicated-gateway.md) in Ihrem Azure Cosmos DB-Konto. Der integrierte Cache ist das erste von vielen Azure Cosmos DB-Features, die ein dediziertes Gateway nutzen, um die Leistung zu verbessern. Sie können eine von drei dedizierten Gatewaygrößen anhand der Anzahl von Kernen und des Speichers auswählen, die Sie für Ihre Workload benötigen.

Ein integrierter Cache wird automatisch innerhalb des dedizierten Gateways konfiguriert. Der integrierte Cache besteht aus zwei Teilen: 

* Ein Elementcache für Punktlesevorgänge 
* Ein Abfragecache für Abfragen

Der integrierte Cache ist ein Lese-/Schreibcache mit einer Entfernungsrichtlinie nach seltener Verwendung. Der Elementcache und der Abfragecache teilen sich die gleiche Kapazität innerhalb des integrierten Caches, und die Entfernungsrichtlinie nach seltener Verwendung gilt für beide. Das bedeutet, das Entfernen von Daten aus dem Cache erfolgt streng danach, welche am längsten nicht verwendet wurden, unabhängig davon, ob es sich um einen Punktlesevorgang oder eine Abfrage handelt.

> [!NOTE]
> Möchten Sie Feedback zum integrierten Cache äußern? Teilen Sie uns Ihre Meinung mit! Sie können Feedback direkt an das Azure Cosmos DB-Entwicklungsteam senden: cosmoscachefeedback@microsoft.com

## <a name="workloads-that-benefit-from-the-integrated-cache"></a>Vom integrierten Cache profitierende Workloads

Das Hauptziel des integrierten Caches ist die Reduzierung der Kosten von Workloads mit vielen Lesevorgängen. Die niedrige Latenz ist zwar nützlich, aber nicht der Hauptvorteil des integrierten Caches, da Azure Cosmos DB auch ohne die Zwischenspeicherung bereits schnell ist.

Punktlesevorgänge und Abfragen, die den integrierten Cache betreffen, nutzen keine RUs. Das bedeutet, für alle Cachetreffer fallen keine RU-Gebühren an. Cachetreffer bieten deutlich geringere Kosten pro Vorgang als Lesevorgänge aus der Back-End-Datenbank.

Für Workloads, die den folgenden Merkmalen entsprechen, sollte in Betracht gezogen werden, ob der integrierte Cache zu Kostenvergünstigungen führt:

-   Workloads mit vielen Lesevorgängen
-   Viele wiederholte Punktlesevorgänge für große Elemente
-   Viele wiederholte Abfragen mit großen Mengen von RUs
-   Häufig angeforderte Partitionsschlüssel für Lesevorgänge

Der größte Faktor der erwarteten Einsparungen ist der Grad, in dem sich Lesevorgänge wiederholen. Wenn Ihre Workload konsistent dieselben Punktlesevorgänge oder Abfragen in einem kurzen Zeitraum ausführt, eignet sie sich bestens für den integrierten Cache. Wenn Sie den integrierten Cache für wiederholte Lesevorgänge verwenden, werden RUs nur für den ersten Lesevorgang verwendet. Nachfolgende Lesevorgänge, die über denselben dedizierten Gatewayknoten geleitet werden (im `MaxIntegratedCacheStaleness`-Fenster, sofern die Daten nicht entfernt wurden), nutzen den Durchsatz nicht.

Für einige Workloads sollte der integrierte Cache nicht in Betracht gezogen werden, zum Beispiel:

-   Workloads mit vielen Schreibvorgängen
-  Selten wiederholte Punktlesevorgänge oder Abfragen

## <a name="item-cache"></a>Elementcache

Sie können den Elementcache für Punktlesevorgänge verwenden (d. h., Schlüssel-/Wertsuchen anhand der Element-ID und des Partitionsschlüssels).

### <a name="populating-the-item-cache"></a>Auffüllen des Elementcaches

- Neue Schreib-, Update- und Löschvorgänge werden automatisch im Elementcache aufgefüllt.
- Wenn Ihre App versucht, ein bestimmtes Element zu lesen, das sich zuvor nicht im Cache befand (Cachefehler), würde das Element nun im Elementcache gespeichert werden.

### <a name="item-cache-invalidation-and-eviction"></a>Elementcacheinvalidierung und -entfernung

- Aktualisieren oder Löschen von Elementen
- Seltene Verwendung
- Cacheaufbewahrungszeit (d. h. `MaxIntegratedCacheStaleness`)

## <a name="query-cache"></a>Abfragecache

Der Abfragecache kann zum Zwischenspeichern von Abfragen verwendet werden. Der Abfragecache transformiert eine Abfrage in eine Schlüssel-/Wertsuche, bei der der Schlüssel dem Abfragetext und der Wert den Abfrageergebnissen entsprechen. Der integrierte Cache verfügt über keine Abfrage-Engine, er speichert lediglich die Schlüssel-/Wertsuche für jede Abfrage.

### <a name="populating-the-query-cache"></a>Auffüllen des Abfragecaches

- Wenn der Cache über kein Ergebnis für diese Abfrage verfügt (Cachefehler), wird die Abfrage an das Back-End gesendet. Nachdem die Abfrage ausgeführt wurde, speichert der Cache die Ergebnisse für diese Abfrage.

### <a name="query-cache-eviction"></a>Abfragecacheentfernung

- Seltene Verwendung
- Cacheaufbewahrungszeit (d. h. `MaxIntegratedCacheStaleness`)

### <a name="working-with-the-query-cache"></a>Arbeiten mit dem Abfragecache

Sie benötigen keinen speziellen Code, wenn Sie mit dem Abfragecache arbeiten, selbst wenn die Ergebnisse Ihrer Abfragen mehrere Seiten umfassen. Die bewährten Methoden und der Code für die Abfragepaginierung sind unabhängig davon identisch, ob Ihre Abfrage den integrierten Cache betrifft oder für die Back-End-Abfrage-Engine ausgeführt wird.

Abfragefortsetzungstoken werden ggf. automatisch im Abfragecache zwischengespeichert. Wenn die Ergebnisse Ihrer Abfrage mehrere Seiten umfassen, fallen für Seiten keine RU-Gebühren an, die im integrierten Cache gespeichert sind. Wenn für nachfolgende Seiten mit Abfrageergebnissen Back-End-Ausführung erforderlich ist, verfügen diese über ein Fortsetzungstoken der vorherigen Seite, um die Duplizierung des vorherigen Arbeitsaufwands zu vermeiden.

> [!NOTE]
> Integrierte Cache-Instanzen in unterschiedlichen dedizierten Gatewayknoten verfügen über voneinander unabhängige Caches. Wenn Daten in einem Knoten zwischengespeichert werden, werden diese nicht unbedingt in anderen zwischengespeichert.

## <a name="integrated-cache-consistency"></a>Konsistenz des integrierten Caches

Der integrierte Cache unterstützt nur die letztliche [Konsistenz](consistency-levels.md). Wenn ein Lesevorgang über einen konsistenten Präfix, eine Sitzung, begrenzte Veraltung oder starke Konsistenz verfügt, umgeht dieser immer den integrierten Cache.

Die einfachste Vorgehensweise zum Konfigurieren der letztlichen Konsistenz für alle Lesevorgänge besteht darin, [diese auf Kontoebene festzulegen](consistency-levels.md#configure-the-default-consistency-level). Wenn Sie die letztliche Konsistenz jedoch nur für einige Ihrer Lesevorgänge festlegen möchten, können Sie die Konsistenz auch auf [Anforderungsebene](how-to-manage-consistency.md#override-the-default-consistency-level) konfigurieren.

## <a name="maxintegratedcachestaleness"></a>MaxIntegratedCacheStaleness

`MaxIntegratedCacheStaleness` ist die maximal zulässige Veraltung für zwischengespeichertes Lesen und Abfragen von Punkten. `MaxIntegratedCacheStaleness` ist auf Anforderungsebene konfigurierbar. Wenn Sie beispielsweise für `MaxIntegratedCacheStaleness` einen Wert von 2 Stunden festlegen, gibt Ihre Anforderung nur dann zwischengespeicherte Daten zurück, wenn die Daten weniger als 2 Stunden alt sind. Um die Wahrscheinlichkeit von wiederholten Lesezugriffen mithilfe des integrierten Caches zu erhöhen, sollten Sie für `MaxIntegratedCacheStaleness` den maximalen Wert festlegen, den Ihre Geschäftsanforderungen zulassen.

Es ist wichtig zu verstehen, dass `MaxIntegratedCacheStaleness` beim Konfigurieren für eine Anforderung, die den Cache auffüllt, sich nicht auf die Dauer der Zwischenspeicherung dieser Anforderung auswirkt. `MaxIntegratedCacheStaleness` erzwingt Konsistenz, wenn Sie versuchen, zwischengespeicherte Daten zu verwenden. Es gibt keine globale Einstellung für TTL oder Cachedatenaufbewahrung, sodass Daten nur dann aus dem Cache entfernt werden, wenn entweder der integrierte Cache voll ist oder ein neuer Lesezugriff mit einem `MaxIntegratedCacheStaleness` ausgeführt wird, der niedriger als das Alter des aktuellen zwischengespeicherten Eintrags ist.

Dies ist eine Verbesserung gegenüber der Funktionsweise der meisten Caches und ermöglicht die folgende zusätzliche Anpassung:

- Sie können unterschiedliche Veraltungsanforderungen für jedes Lesen oder Abfragen von Punkten festlegen.
- Verschiedene Clients können auch dann unterschiedliche `MaxIntegratedCacheStaleness`-Werte konfigurieren, wenn sie dasselbe Lesen oder Abfragen von Punkten ausführen.
- Wenn Sie die Lesekonsistenz bei der Verwendung zwischengespeicherter Daten ändern möchten, hat die Änderung von `MaxIntegratedCacheStaleness` sofortige Auswirkungen auf die Lesekonsistenz.

> [!NOTE]
> Ohne explizite Konfiguration hat MaxIntegratedCacheStaleness den Standardwert 5 Minuten.

Sehen Sie sich das folgende Beispiel an, um den `MaxIntegratedCacheStaleness`-Parameter besser zu verstehen:

| Time       | Anforderung                                         | Antwort                                                     |
| ---------- | ----------------------------------------------- | ------------------------------------------------------------ |
| t = 0 s  | Ausführen von Abfrage A mit MaxIntegratedCacheStaleness = 30 Sekunden | Zurückgeben von Ergebnissen aus der Back-End-Datenbank (normale RU-Gebühren) und Auffüllen des Caches     |
| t = 0 s  | Ausführen von Abfrage B mit MaxIntegratedCacheStaleness = 60 Sekunden | Zurückgeben von Ergebnissen aus der Back-End-Datenbank (normale RU-Gebühren) und Auffüllen des Caches     |
| t = 20 s | Ausführen von Abfrage A mit MaxIntegratedCacheStaleness = 30 Sekunden | Zurückgeben von Ergebnissen aus integriertem Cache (0 RU-Gebühr)           |
| t = 20 s | Ausführen von Abfrage B mit MaxIntegratedCacheStaleness = 60 Sekunden | Zurückgeben von Ergebnissen aus integriertem Cache (0 RU-Gebühr)           |
| t = 40 s | Ausführen von Abfrage A mit MaxIntegratedCacheStaleness = 30 Sekunden | Zurückgeben von Ergebnissen aus der Back-End-Datenbank (normale RU-Gebühren) und Aktualisieren des Caches |
| t = 40 s | Ausführen von Abfrage B mit MaxIntegratedCacheStaleness = 60 Sekunden | Zurückgeben von Ergebnissen aus integriertem Cache (0 RU-Gebühr)           |
| t = 50 s | Ausführen von Abfrage B mit MaxIntegratedCacheStaleness = 20 Sekunden | Zurückgeben von Ergebnissen aus der Back-End-Datenbank (normale RU-Gebühren) und Aktualisieren des Caches |

> [!NOTE]
> Das Anpassen von `MaxIntegratedCacheStaleness` wird nur in den neuesten .NET- und Java-Vorschau-SDKs unterstützt.

[Lernen Sie, `MaxIntegratedCacheStaleness` zu konfigurieren.](how-to-configure-integrated-cache.md#adjust-maxintegratedcachestaleness)

## <a name="metrics"></a>Metriken

Bei Verwendung des integrierten Caches ist es hilfreich, einige wichtige Metriken zu überwachen. Zu diesen Metriken des integrierten Caches gehören:

- `DedicatedGatewayAverageCpuUsage`: Durchschnittliche CPU-Auslastung aller dedizierten Gatewayknoten.
- `DedicatedGatewayMaxCpuUsage`: Maximale CPU-Auslastung aller dedizierten Gatewayknoten.
- `DedicatedGatewayAverageMemoryUsage`: Durchschnittliche Arbeitsspeicherauslastung aller dedizierten Gatewayknoten, die sowohl für das Weiterleiten von Anforderungen als auch das Zwischenspeichern von Daten verwendet werden.
- `DedicatedGatewayRequests`: Gesamtanzahl dedizierter Gatewayanforderungen für alle dedizierten Gatewayinstanzen.
- `IntegratedCacheEvictedEntriesSize`: Die durchschnittliche Datenmenge, die aufgrund von LRU aus dem integrierten Cache über dedizierte Gatewayknoten hinweg entfernt wurde. Dieser Wert enthält keine Daten, die aufgrund einer `MaxIntegratedCacheStaleness`-Zeitüberschreitung abgelaufen sind.
- `IntegratedCacheItemExpirationCount`: Die Anzahl der Elemente, die aus dem integrierten Cache entfernt werden, weil zwischengespeicherte Punktlesezugriffe die `MaxIntegratedCacheStaleness`-Zeit überschritten haben. Dieser Wert ist ein Durchschnitt der integrierten Cacheinstanzen auf allen dedizierten Gatewayknoten.
- `IntegratedCacheQueryExpirationCount`: Die Anzahl der Abfragen, die aus dem integrierten Cache entfernt werden, weil zwischengespeicherte Abfragen die `MaxIntegratedCacheStaleness`-Zeit überschritten haben. Dieser Wert ist ein Durchschnitt der integrierten Cacheinstanzen auf allen dedizierten Gatewayknoten.
- `IntegratedCacheItemHitRate`: Der Anteil der Punktlesevorgänge, die den integrierten Cache verwendet haben (aus allen Punktlesevorgängen, die mit letztlicher Konsistenz über das dedizierte Gateway weitergeleitet wurden). Dieser Wert ist ein Durchschnitt der integrierten Cacheinstanzen auf allen dedizierten Gatewayknoten.
- `IntegratedCacheQueryHitRate`: Der Anteil der Abfragen, die den integrierten Cache verwendet haben (aus allen Abfragen, die mit letztlicher Konsistenz über das dedizierte Gateway weitergeleitet wurden). Dieser Wert ist ein Durchschnitt der integrierten Cacheinstanzen auf allen dedizierten Gatewayknoten.

Alle vorhandenen Metriken sind standardmäßig über das Blatt **Metriken** verfügbar (nicht über das Blatt „Metriken (klassisch)“):

   :::image type="content" source="./media/integrated-cache/integrated-cache-metrics.png" alt-text="Screenshot: Metriken des integrierten Caches" border="false":::

Metriken sind entweder ein Durchschnittswert, ein Maximum oder eine Summe für alle dedizierten Gatewayknoten. Wenn Sie beispielsweise einen dedizierten Gatewaycluster mit fünf Knoten bereitstellen, spiegeln die Metriken den aggregierten Wert aller fünf Knoten wider. Es ist nicht möglich, die Metrikwerte für jeden einzelnen Knoten zu bestimmen.

## <a name="troubleshooting-common-issues"></a>Behandlung häufig auftretender Probleme

In den folgenden Beispielen wird das Debuggen einiger gängiger Szenarios veranschaulicht:

### <a name="i-cant-tell-if-my-application-is-using-the-dedicated-gateway"></a>Wie erkenne ich, ob meine Anwendung das dedizierte Gateway verwendet?

Überprüfen Sie die Metrik `DedicatedGatewayRequests`. Diese Metrik umfasst alle Anforderungen, die das dedizierte Gateway nutzen, unabhängig davon, ob sie den integrierten Cache betreffen. Wenn Ihre Anwendung das Standardgateway oder den direkten Modus mit Ihrer ursprünglichen Verbindungszeichenfolge verwendet, wird Ihnen keine Fehlermeldung angezeigt, aber `DedicatedGatewayRequests` entspricht 0 (null).

### <a name="i-cant-tell-if-my-requests-are-hitting-the-integrated-cache"></a>Wie erkenne ich, ob meine Anforderungen den integrierten Cache betreffen?

Überprüfen Sie `IntegratedCacheItemHitRate` und `IntegratedCacheQueryHitRate`. Wenn beide Werte 0 (null) sind, treffen Anforderungen nicht den integrierten Cache. Überprüfen Sie, ob Sie die Verbindungszeichenfolge des dedizierten Gateways verwenden, indem Sie eine [Verbindung mit dem Gatewaymodus herstellen](sql-sdk-connection-modes.md) und die [letztliche Konsistenz festgelegt haben](consistency-levels.md#configure-the-default-consistency-level).

### <a name="i-want-to-understand-if-my-dedicated-gateway-is-too-small"></a>Woran erkenne ich, ob mein dediziertes Gateway zu klein ist?

Überprüfen Sie `IntegratedCacheItemHitRate` und `IntegratedCacheQueryHitRate`. Wenn dieser Wert hoch ist (z. B. über 0,7- 0,8), ist das ein gutes Anzeichen dafür, dass die Größe des dedizierten Gateways genügt.

Wenn `IntegratedCacheItemHitRate` oder `IntegratedCacheQueryHitRate` niedrig ist, überprüfen Sie `IntegratedCacheEvictedEntriesSize`. Wenn der `IntegratedCacheEvictedEntriesSize`-Wert hoch ist, bedeutet dies, dass ein größeres dediziertes Gateway von Vorteil wäre. Sie können experimentieren, indem Sie die Größe des dedizierten Gateways erhöhen und die neuen Werte von `IntegratedCacheItemHitRate` und `IntegratedCacheQueryHitRate` vergleichen. Wenn ein größeres dediziertes Gateway die Werte von `IntegratedCacheItemHitRate` oder `IntegratedCacheQueryHitRate` nicht verbessert, ist es möglich, dass sich Lesezugriffe einfach nicht genug wiederholen, sodass der integrierte Cache keine Auswirkung hat.

### <a name="i-want-to-understand-if-my-dedicated-gateway-is-too-large"></a>Woran erkenne ich, ob mein dediziertes Gateway zu groß ist?

Es ist schwieriger zu messen, ob ein dediziertes Gateway zu groß ist, als zu messen, ob ein dediziertes Gateway zu klein ist. Im Allgemeinen sollten Sie klein anfangen und die Größe des dedizierten Gateways langsam erhöhen, bis die Werte von `IntegratedCacheItemHitRate` und `IntegratedCacheQueryHitRate` sich nicht mehr verbessern. In einigen Fällen ist nur eine der beiden Cachetreffermetriken wichtig, nicht beide. Wenn es sich bei Ihrer Workload beispielsweise hauptsächlich um Abfragen und nicht um Punktlesevorgänge handelt, ist `IntegratedCacheQueryHitRate` viel wichtiger als `IntegratedCacheItemHitRate`.

Wenn die meisten Daten aufgrund der Überschreitung des `MaxIntegratedCacheStaleness`-Werts aus dem Cache entfernt werden, anstatt aufgrund der Entfernungsrichtlinie nach seltener Verwendung, ist Ihr Cache möglicherweise größer als notwendig. Wenn `IntegratedCacheItemExpirationCount` und `IntegratedCacheQueryExpirationCount` kombiniert fast so groß wie `IntegratedCacheEvictedEntriesSize` sind, können Sie mit einer kleineren Größe des dedizierten Gateways experimentieren und die Leistung vergleichen.

### <a name="i-want-to-understand-if-i-need-to-add-more-dedicated-gateway-nodes"></a>Ich möchte wissen, ob ich weitere dedizierte Gatewayknoten hinzufügen muss.

Wenn die Latenz unerwartet hoch ist, benötigen Sie in einigen Fällen möglicherweise mehr dedizierte Gatewayknoten als größere Knoten. Überprüfen Sie `DedicatedGatewayMaxCpuUsage` und `DedicatedGatewayAverageMemoryUsage`, um zu ermitteln, ob das Hinzufügen weiterer dedizierter Gatewayknoten die Latenz reduzieren würde. Beachten Sie: Da alle Instanzen des integrierten Caches voneinander unabhängig sind, wird der Wert von `IntegratedCacheEvictedEntriesSize` durch das Hinzufügen weiterer dedizierter Gatewayknoten nicht reduziert. Das Hinzufügen weiterer Knoten verbessert jedoch das Anforderungsvolumen, das Ihr dedizierter Gatewaycluster verarbeiten kann.

## <a name="next-steps"></a>Nächste Schritte

- [Häufig gestellte Fragen zum integrierten Cache](integrated-cache-faq.md)
- [Konfigurieren des integrierten Caches](how-to-configure-integrated-cache.md)
- [Dediziertes Gateway](dedicated-gateway.md)