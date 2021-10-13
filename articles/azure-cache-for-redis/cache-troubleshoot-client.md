---
title: Behandeln von clientseitigen Problemen bei Azure Cache for Redis
description: Erfahren Sie, wie Sie gängige clientseitige Probleme mit Azure Cache for Redis lösen, zum Beispiel hohe Arbeitsspeicherauslastung des Redis-Clients, sprunghafter Anstieg des Datenverkehrsvolumen, hohe CPU-Auslastung, eingeschränkte Bandbreite, umfangreiche Anforderungen oder umfangreiche Antworten.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: troubleshooting
ms.date: 10/18/2019
ms.openlocfilehash: bd0ca3b20cc37ecf2107e03eea5d6e4a62633f16
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2021
ms.locfileid: "129807178"
---
# <a name="troubleshoot-azure-cache-for-redis-client-side-issues"></a>Behandeln von clientseitigen Problemen bei Azure Cache for Redis

In diesem Abschnitt wird das Behandeln von Problemen beschrieben, die wegen eines Zustands des Redis-Clients auftreten, den Ihre Anwendung verwendet.

- [Hohe Arbeitsspeicherauslastung auf dem Redis-Client](#memory-pressure-on-redis-client)
- [Sprunghafter Anstieg des Datenverkehrsvolumens](#traffic-burst)
- [Hohe Auslastung der Client-CPU](#high-client-cpu-usage)
- [Clientseitige Bandbreitenbegrenzung](#client-side-bandwidth-limitation)
<!-- [Large request or response size](#large-request-or-response-size) -->

## <a name="memory-pressure-on-redis-client"></a>Hohe Arbeitsspeicherauslastung auf dem Redis-Client

Hohe Arbeitsspeicherauslastung auf dem Clientcomputer führt zu Leistungsproblemen aller Art, die die Verarbeitung von Antworten aus dem Cache verzögern können. Wenn es zu hoher Arbeitsspeicherauslastung kommt, kann das System beginnen, Daten auf den Datenträger auszulagern. Diese sogenannten _Seitenfehler_ bewirken eine deutliche Verlangsamung des Systems.

So erkennen sie hohe Speicherauslastung auf dem Client

- Überwachen Sie die Speicherauslastung auf dem Computer und stellen Sie sicher, dass sie den verfügbaren Speicher nicht überschreitet.
- Überwachen Sie den Leistungsindikator `Page Faults/Sec` des Clients. Während des normalen Betriebs kommt es auf den meisten Systeme zu ein paar Seitenfehlern. Auftretende Spitzen bei Seitenfehlern, die mit Anforderungstimeouts korrespondieren, können auf hohe Arbeitsspeicherauslastung hindeuten.

Hoher Arbeitsspeicherauslastung auf dem Client kann auf mehrere Arten gemindert werden:

- Analysieren Sie die Muster Ihrer Arbeitsspeichernutzung, um die Auslastung auf dem Client zu verringern.
- Führen Sie ein Upgrade Ihrer Client-VM auf eine größere VM mit mehr Arbeitsspeicher aus.

## <a name="traffic-burst"></a>Sprunghafter Anstieg des Datenverkehrsvolumens

Plötzliche Anstiege des Datenverkehrsvolumens in Verbindung mit unzureichenden `ThreadPool` -Einstellungen können zu Verzögerungen beim Verarbeiten der vom Redis-Server bereits gesendeten, aber auf der Clientseite noch nicht genutzten Daten führen.

Überwachen Sie die Änderungen bei den `ThreadPool`-Statistiken im zeitlichen Verlauf unter Verwendung [eines Beispiel-`ThreadPoolLogger`-](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Sie können `TimeoutException`-Meldungen von StackExchange.Redis wie unten gezeigt verwenden, um weitere Untersuchungen anzustellen:

```output
    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)
```

In der oben aufgeführten Ausnahme zeigen sich mehrere interessante Probleme:

- Beachten Sie, dass in den Abschnitten `IOCP` und `WORKER` der `Busy`-Wert größer als der `Min`-Wert ist. Dieser Unterschied bedeutet, dass die `ThreadPool`-Einstellungen angepasst werden müssen.
- Außerdem wird `in: 64221` angegeben. Dieser Wert bedeutet, dass 64.211 Bytes auf der Kernelsocketebene des Clients empfangen, aber von der Anwendung noch nicht gelesen wurden. Dieser Unterschied bedeutet in der Regel, dass Ihre Anwendung (z. B. StackExchange.Redis) Daten aus dem Netzwerk nicht so schnell liest, wie sie vom Server gesendet werden.

Sie können Ihre [`ThreadPool`-Einstellungen konfigurieren](cache-management-faq.yml#important-details-about-threadpool-growth), um sicherzustellen, dass Ihr Threadpool bei Datenverkehrsspitzen schnell hochskaliert wird.

## <a name="high-client-cpu-usage"></a>Hohe Auslastung der Client-CPU

Hohe Auslastung der Client-CPU weist darauf hin, dass das System mit der angeforderten Arbeitsmenge nicht Schritt halten kann. Obgleich der Client die Antwort schnell gesendet hat, schafft es der Client eventuell nicht, die Antwort rechtzeitig zu verarbeiten.

Überwachen Sie die systemweite CPU-Auslastung des Clients mithilfe der im Azure-Portal zur Verfügung stehenden Metriken oder mithilfe von Leistungsindikatoren auf dem Computer. Achten Sie darauf, dass Sie nicht die *Prozess*-CPU überwachen. Für einen einzelnen Prozess kann die CPU-Auslastung gering sein, während aber die systemweite CPU-Auslastung hoch sein kann. Beobachten Sie die Spitzen bei der CPU-Auslastung, denn sie korrespondieren mit Timeouts. Eine hohe CPU-Auslastung kann auch hohe Werte für `in: XXX` in den `TimeoutException`-Fehlermeldungen verursachen, wie im Abschnitt [Sprunghafter Anstieg des Datenverkehrsvolumens](#traffic-burst) beschrieben.

> [!NOTE]
> Ab StackExchange.Redis 1.1.603 ist in den `TimeoutException`-Fehlermeldungen die Metrik `local-cpu` enthalten. Verwenden Sie immer die neueste Version des [NuGet-Pakets für StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/). Es werden ständig Fehler im Code behoben, um ihn widerstandsfähiger gegen Timeouts zu machen. Das Nutzen der neuesten Version ist also wichtig.
>

So mindern Sie eine hohe CPU-Auslastung beim Client

- Untersuchen Sie, was CPU-Spitzen verursacht.
- Führen Sie ein Upgrade Ihres Clients auf eine größere VM mit höherer CPU-Kapazität aus.

## <a name="client-side-bandwidth-limitation"></a>Clientseitige Bandbreitenbegrenzung

Je nach Architektur der Clientcomputer bestehen möglicherweise Einschränkungen im Hinblick auf die verfügbare Netzwerkbandbreite. Wenn auf dem Client durch Überladen der Netzwerkkapazität die verfügbare Bandbreite überschritten wird, werden die Daten auf der Clientseite nicht so schnell verarbeitet, wie sie vom Server gesendet werden. Diese Situation kann zu Timeouts führen.

Überwachen Sie die Änderungen bei der Bandbreitennutzung im zeitlichen Verlauf unter Verwendung eines [Beispiel-`BandwidthLogger`](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Dieser Code wird in einigen Umgebungen mit eingeschränkten Berechtigungen (z.B. Azure-Websites) möglicherweise nicht erfolgreich ausgeführt.

Verringern Sie zur Minderung den Netzwerkbandbreitenverbrauch, oder vergrößern Sie die Client-VM auf eine mit höherer Netzwerkkapazität.

<!-- 
## Large request or response Size

A large request/response can cause timeouts. As an example, suppose your timeout value configured on your client is 1 second. Your application requests two keys (for example, 'A' and 'B') at the same time (using the same physical network connection). Most clients support request "pipelining", where both requests 'A' and 'B' are sent one after the other without waiting for their responses. The server sends the responses back in the same order. If response 'A' is large, it can eat up most of the timeout for later requests.

In the following example, request 'A' and 'B' are sent quickly to the server. The server starts sending responses 'A' and 'B' quickly. Because of data transfer times, response 'B' must wait behind response 'A' times out even though the server responded quickly.

```console
|-------- 1 Second Timeout (A)----------|
|-Request A-|
     |-------- 1 Second Timeout (B) ----------|
     |-Request B-|
            |- Read Response A --------|
                                       |- Read Response B-| (**TIMEOUT**)
```

This request/response is a difficult one to measure. You could instrument your client code to track large requests and responses.

Resolutions for large response sizes are varied but include:

1. Optimize your application for a large number of small values, rather than a few large values.
    - The preferred solution is to break up your data into related smaller values.
    - See the post [What is the ideal value size range for redis? Is 100 KB too large?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) for details on why smaller values are recommended.
1. Increase the size of your VM to get higher bandwidth capabilities
    - More bandwidth on your client or server VM may reduce data transfer times for larger responses.
    - Compare your current network usage on both machines to the limits of your current VM size. More bandwidth on only the server or only on the client may not be enough.
1. Increase the number of connection objects your application uses.
    - Use a round-robin approach to make requests over different connection objects.

 -->
## <a name="additional-information"></a>Zusätzliche Informationen

- [Behandeln von serverseitigen Problemen bei Azure Cache for Redis](cache-troubleshoot-server.md)
- [Wie kann ich die Leistung meines Caches messen und testen?](cache-management-faq.yml#how-can-i-benchmark-and-test-the-performance-of-my-cache-)
