---
title: Problembehandlung bei Timeouts bei Azure Cache for Redis
description: Erfahren Sie, wie Sie häufige Timeoutprobleme mit Azure Cache for Redis beheben, z. B. Patchen des Redis-Servers und StackExchange.Redis-Timeoutausnahmen.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 11/3/2021
ms.openlocfilehash: 09f461b5d64b52ded281e338e1307876d7e5f86b
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131843820"
---
# <a name="troubleshoot-azure-cache-for-redis-timeouts"></a>Problembehandlung bei Timeouts bei Azure Cache for Redis

In diesem Abschnitt wird die Problembehandlung bei Timeouts erläutert, die beim Herstellen einer Verbindung mit Azure Cache for Redis auftreten.

- [Redis-Serverpatching](#redis-server-patching)
- [StackExchange.Redis-Timeoutausnahmen](#stackexchangeredis-timeout-exceptions)

> [!NOTE]
> Etliche Problembehandlungsschritte in diesem Leitfaden enthalten Anleitungen zum Ausführen von Redis-Befehlen und zum Überwachen verschiedener Leistungsmetriken. Weitere Informationen und Anleitungen finden Sie in den Artikeln im Abschnitt [Weitere Informationen](#additional-information) .
>

## <a name="redis-server-patching"></a>Redis-Serverpatching

Azure Cache for Redis aktualisiert regelmäßig im Rahmen der bereitgestellten verwalteten Dienstfunktionalität die Serversoftware. Diese [Patching](cache-failover.md)-Aktivität findet größtenteils im Hintergrund statt. Wenn Redis-Serverknoten gepatcht werden, können beim Failover Redis-Clients, die mit diesen Knoten verbunden sind, temporäre Timeouts erleben, da Verbindungen zwischen diesen Knoten umgeschaltet werden. Weitere Informationen zu den Nebenwirkungen des Patchings für Ihre Anwendung sowie Informationen zu einer besseren Handhabung von Patchereignissen finden Sie unter [Wie wirkt sich ein Failover auf meine Clientanwendung aus?](cache-failover.md#how-does-a-failover-affect-my-client-application).

## <a name="stackexchangeredis-timeout-exceptions"></a>StackExchange.Redis-Timeoutausnahmen

„StackExchange.Redis“ verwendet für synchrone Vorgänge eine Konfigurationseinstellung mit dem Namen `synctimeout` mit einem Standardwert von 5000 ms. Wenn ein synchroner Aufruf nicht innerhalb dieses Zeitraums abgeschlossen wird, löst der StackExchange.Redis-Client einen Timeoutfehler ähnlich dem folgenden Beispiel aus:

```output
    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)
```

Diese Fehlermeldung enthält Metriken, mit deren Hilfe Sie die Ursache und die mögliche Lösung des Problems ermitteln können. In der folgenden Tabelle finden Sie Details zu den Metriken in der Fehlermeldung.

| Metrik in der Fehlermeldung | Details |
| --- | --- |
| `inst` |In der letzten Zeitscheibe: 0 Befehle wurden ausgegeben. |
| `mgr` |Der Socket-Manager führt `socket.select` aus, was bedeutet, dass er das Betriebssystem auffordert, einen Socket anzugeben, der beschäftigt ist. Der Reader liest nicht aktiv aus dem Netzwerk, weil er meint, dass nichts zu tun ist |
| `queue` |Es sind insgesamt 73 Vorgänge in Bearbeitung. |
| `qu` |6 der in Bearbeitung befindlichen Vorgänge befinden sich in der Warteschlange für „Nicht gesendet“ und wurden noch nicht in das Netzwerk für ausgehenden Datenverkehr geschrieben. |
| `qs`|67 der in Bearbeitung befindlichen Vorgänge wurden an den Server gesendet, aber es ist noch keine Antwort verfügbar. Als Antwort ist Folgendes möglich: `Not yet sent by the server` oder `sent by the server but not yet processed by the client.`. |
| `qc` |Für keinen (Null) der in Bearbeitung befindlichen Vorgänge liegt eine Antwort vor, aber die Vorgänge wurden noch nicht als abgeschlossen markiert, weil noch auf die Abschlussschleife gewartet wird. |
| `wr` |Es ist ein aktiver Writer vorhanden (d. h., die sechs noch nicht gesendeten Anforderungen werden nicht ignoriert); Bytes/aktive Writer. |
| `in` |Es sind keine aktiven Reader vorhanden, und null Bytes sind zum Lesen auf der NIC (Network Interface Card, Netzwerkschnittstellenkarte) vorhanden; Bytes/aktive Reader |

Im vorherigen Ausnahmebeispiel enthalten die Abschnitte `IOCP` und `WORKER` jeweils einen `Busy`-Wert, der größer als der `Min`-Wert ist. Der Unterschied bedeutet, dass Sie Ihre `ThreadPool`-Einstellungen anpassen sollten. Sie können Ihre [ThreadPool-Einstellungen](cache-management-faq.yml#important-details-about-threadpool-growth) so konfigurieren, dass sichergestellt ist, dass Ihr Threadpool bei Datenverkehrsspitzen schnell hochskaliert wird.

Mithilfe der folgenden Schritte können Sie mögliche Ursachen beseitigen.

1. Stellen Sie als bewährte Methode sicher, dass Sie das ForceReconnect-Muster verwenden, um angehaltene Verbindungen zu erkennen und zu ersetzen, wie im Artikel [Verbindungsresilienz](cache-best-practices-connection.md#using-forcereconnect-with-stackexchangeredis) beschrieben.

   Weitere Informationen zur Verwendung von „StackExchange.Redis“ finden Sie unter [Herstellen einer Verbindung mit dem Cache mithilfe von StackExchange.Redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache). 

1. Stellen Sie sicher, dass sich Ihr Server und die Clientanwendung in der gleichen Azure-Region befinden. Wenn Ihr Cache z. B. in der Region „USA, Osten“ und Ihr Client in der Region „USA, Westen“ angesiedelt ist, erhalten Sie möglicherweise Timeouts, und die Anforderung wird nicht innerhalb des `synctimeout`-Intervalls abgeschlossen. Auch beim Debuggen über den lokalen Entwicklungscomputer können Timeouts auftreten.

    Es wird dringend empfohlen, den Cache und den Client in der gleichen Azure-Region anzusiedeln. Wenn Ihr Szenario regionsübergreifende Aufrufe beinhaltet, sollten Sie das `synctimeout`-Intervall auf einen höheren Wert als die standardmäßigen 5000 ms festlegen. Nehmen Sie zu diesem Zweck eine `synctimeout`-Eigenschaft in die Verbindungszeichenfolge auf. Das folgende Beispiel zeigt einen Codeausschnitt aus einer Verbindungszeichenfolge für StackExchange.Redis, bereitgestellt von Azure Cache for Redis, mit einem `synctimeout` von 8000 ms.

    ```output
    synctimeout=8000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
    ```

1. Verwenden Sie immer die neueste Version des [NuGet-Pakets für StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/). Es werden ständig Fehler im Code behoben, um ihn widerstandsfähiger gegen Timeouts zu machen. Das Nutzen der neuesten Version ist also wichtig.
1. Wenn Ihre Anforderungen durch Bandbreiteneinschränkungen auf dem Server oder dem Client eingeschränkt sind, dauert deren Ausführung länger, und es können Timeouts auftreten. Um festzustellen, ob ein Timeout durch die Netzwerkbandbreite auf dem Server verursacht wird, lesen Sie [Serverseitige Bandbreitenbegrenzung](cache-troubleshoot-server.md#server-side-bandwidth-limitation). Um festzustellen, ob ein Timeout durch die Netzwerkbandbreite auf dem Client verursacht wird, lesen Sie [Clientseitige Bandbreitenbegrenzung](cache-troubleshoot-client.md#client-side-bandwidth-limitation).
1. Werden Sie durch die CPU auf dem Server oder auf dem Client behindert?

   - Überprüfen Sie, ob Sie durch die CPU auf Ihrem Client eingeschränkt werden. Eine hohe CPU-Auslastung kann dazu führen, dass die Anforderung nicht innerhalb des `synctimeout`-Intervalls verarbeitet wird und ein Timeout der Anforderung verursachen. Dieses Problem lässt sich möglicherweise durch Verwendung eines größeren Clients oder durch Verteilung der Last lösen.
   - Überprüfen Sie, ob Sie durch die CPU auf dem Server behindert werden, indem Sie die [Cacheleistungsmetrik](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) der CPU überwachen. Wenn Anforderungen eingehen, während Redis durch die CPU behindert wird, können für diese Anforderungen Timeouts auftreten. Sie können Abhilfe schaffen, indem Sie die Last auf mehrere Shards in einem Premium-Cache verteilen. Alternativ können Sie ein Upgrade zu einer größer dimensionierten CPU durchführen oder in einen höheren Tarif wechseln. Weitere Informationen finden Sie unter [Serverseitige Bandbreitenbegrenzung](cache-troubleshoot-server.md#server-side-bandwidth-limitation).
1. Dauert die Verarbeitung von Befehlen auf dem Server lange? Wenn die Verarbeitung und Ausführung von Befehlen auf dem Redis-Server lange dauert, kann dies zu Timeouts führen. Weitere Informationen zu Befehlen mit langer Ausführungsdauer finden Sie unter [Befehle mit langer Ausführungsdauer](cache-troubleshoot-server.md#long-running-commands). Sie können mithilfe des redis-cli-Clients eine Verbindung mit Ihrer Azure Cache for Redis-Instanz herstellen oder die [Redis-Konsole](cache-configure.md#redis-console) verwenden. Führen Sie dann den Befehl [SLOWLOG](https://redis.io/commands/slowlog) aus, um festzustellen, ob Anforderungen langsamer als erwartet sind. Der Redis-Server und StackExchange.Redis sind für viele kleine Anforderungen optimiert, nicht für wenige große. Durch eine Aufteilung Ihrer Daten in kleinere Blöcke können Sie u.U. Verbesserungen erzielen.

    Informationen zum Herstellen einer Verbindung mit dem TLS-/SSL-Endpunkt Ihres Caches mithilfe von „redis-cli“ und „stunnel“ finden Sie im Blogbeitrag [Ankündigung des ASP.NET-Sitzungszustandsanbieters für Redis (Vorschauversion)](https://devblogs.microsoft.com/aspnet/announcing-asp-net-session-state-provider-for-redis-preview-release/).
1. Eine hohe Auslastung des Redis-Servers kann zu Timeouts führen. Sie können die Serverauslastung überwachen, indem Sie die [Cacheleistungsmetrik](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Redis Server Load` überwachen. Eine Serverauslastung von 100 (Maximalwert) bedeutet, dass der Redis-Server mit der Verarbeitung von Anforderungen voll ausgelastet war und keine Leerlaufzeiten aufgetreten sind. Um festzustellen, ob bestimmte Anforderungen die gesamte Serverkapazität in Anspruch nehmen, führen Sie den Befehl „SlowLog“ aus, wie im vorherigen Absatz beschrieben. Weitere Informationen finden Sie unter „Hohe CPU-Auslastung/hohe Serverauslastung“.
1. Gab es ein anderes Ereignis auf der Clientseite, das ein Netzwerkflackern verursacht haben könnte? Allgemeine Ereignisse umfassen: zentrales Hoch- oder Herunterskalieren der Anzahl von Clientinstanzen, Bereitstellen einer neuen Version des Clients oder Aktivieren der Autoskalierung. Bei unseren Tests haben wir festgestellt, dass Autoskalierung oder zentrales Hoch- oder Herunterskalieren bei ausgehenden Netzwerkverbindungen zu Unterbrechungen von einigen Sekunden verursachen kann. Der StackExchange.Redis-Code ist unempfindlich gegen solche Ereignisse und stellt die Verbindung anschließend wieder her. Beim Wiederherstellen der Verbindung kann für Anforderungen, die sich in der Warteschlange befinden, ein Timeout auftreten.
1. Gab es vor mehreren kleineren Anforderungen eine große Anforderung an den Cache, für die ein Timeout auftrat? Der Parameter `qs` in der Fehlermeldung teilt Ihnen mit, wie viele Anforderungen vom Client an den Server gesendet wurden, für die aber bisher keine Antwort verarbeitet wurde. Dieser Wert kann fortlaufend wachsen, weil StackExchange.Redis eine einzige TCP-Verbindung nutzt und zu einem Zeitpunkt jeweils immer nur eine Antwort lesen kann. Obwohl es beim ersten Vorgang zu einem Timeout gekommen ist, wird das Senden von weiteren Daten an den oder vom Server dadurch nicht unterbrochen. Andere Anforderungen werden blockiert, bis die umfangreiche Anforderung fertig gestellt ist. Hierbei kann es zu Timeouts kommen. Als mögliche Lösung können Sie die Wahrscheinlichkeit von Timeouts verringern. Stellen Sie zu diesem Zweck sicher, dass Ihr Cache für Ihre Workload groß genug ist, und teilen Sie große Werte in kleinere Blöcke auf. Eine weitere Lösung besteht darin, in Ihrem Client einen Pool von `ConnectionMultiplexer`-Objekten zu verwenden und beim Senden einer neuen Anforderung den am wenigsten ausgelasteten `ConnectionMultiplexer` zu verwenden. Durch das Laden über mehrere Verbindungsobjekte können Sie verhindern, dass ein einzelnes Timeout weitere Timeouts für andere Anforderungen nach sich zieht.
1. Wenn Sie `RedisSessionStateProvider` verwenden, müssen Sie das Timeout für Wiederholungsversuche richtig festlegen. `retryTimeoutInMilliseconds` sollte höher sein als `operationTimeoutInMilliseconds`, andernfalls erfolgen keine Wiederholungsversuche. Im folgenden Beispiel ist `retryTimeoutInMilliseconds` auf 3000 festgelegt. Weitere Informationen finden Sie unter [ASP.NET-Sitzungszustandsanbieter für Azure Cache for Redis](cache-aspnet-session-state-provider.md) und [How to use configuration parameters of Session State Provider and Output Cache Provider](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration) (Verwenden der Konfigurationsparameter des Sitzungszustandsanbieters und des Ausgabecacheanbieters).

    ```xml
    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />
    ```

1. Überprüfen Sie die Speicherauslastung auf dem Azure Cache for Redis-Server, indem Sie `Used Memory RSS` und `Used Memory` [überwachen](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Wenn eine Entfernungsrichtlinie vorhanden ist und `Used_Memory` die Cachegröße erreicht, beginnt Redis mit dem Entfernen von Schlüsseln. Im Idealfall sollte `Used Memory RSS` nur geringfügig höher als `Used memory` sein. Ein großer Unterschied bedeutet, dass eine (interne oder externe) Speicherfragmentierung vorhanden ist. Wenn `Used Memory RSS` niedriger ist als `Used Memory`, bedeutet das, dass ein Teil des Cachespeichers vom Betriebssystem ausgelagert wurde. Im Fall einer solchen Auslagerung müssen Sie mit erheblichen Latenzen rechnen. Da Redis nicht steuern kann, wie seine Belegungen den Speicherseiten zugeordnet werden, ist ein hoher Wert für `Used Memory RSS` oft das Ergebnis einer Speicherauslastungsspitze. Wenn Redis-Server Arbeitsspeicher freigibt, übernimmt der Allocator diesen Speicher, wobei er den Speicher dem System zurückgeben kann, aber nicht muss. Es kann zu einer Diskrepanz zwischen dem Wert für `Used Memory` und der vom Betriebssystem gemeldeten Speichernutzung kommen. Arbeitsspeicher wurde möglicherweise von Redis genutzt und freigegeben, aber nicht an das System zurückgegeben. Um Speicherprobleme möglichst gering zu halten, können Sie die folgenden Schritte ausführen:

   - Führen Sie ein Upgrade auf einen größeren Cache durch, damit in Ihrem System genügend Speicher vorhanden ist und entsprechende Einschränkungen wegfallen.
   - Legen Sie Ablauffristen für die Schlüssel fest, damit ältere Werte proaktiv entfernt werden.
   - Überwachen Sie die Cachemetrik `used_memory_rss`. Wenn sich dieser Wert der Größe Ihres Caches nähert, sind Leistungsprobleme absehbar. Falls Sie einen Premium-Cache nutzen, verteilen Sie die Daten über mehrere Shards. Andernfalls führen Sie ein Upgrade auf einen größeren Cache durch.

   Weitere Informationen finden Sie unter [Hohe Arbeitsspeicherauslastung auf dem Redis-Server](cache-troubleshoot-server.md#memory-pressure-on-redis-server).

## <a name="additional-information"></a>Zusätzliche Informationen

- [Behandeln von clientseitigen Problemen bei Azure Cache for Redis](cache-troubleshoot-client.md)
- [Behandeln von serverseitigen Problemen bei Azure Cache for Redis](cache-troubleshoot-server.md)
- [Wie kann ich die Leistung meines Caches messen und testen?](cache-management-faq.yml#how-can-i-benchmark-and-test-the-performance-of-my-cache-)
- [Überwachen von Azure Cache for Redis](cache-how-to-monitor.md)
