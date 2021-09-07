---
title: Konfigurieren des Redis-Clusterings – Azure Cache for Redis-Instanzen im Premium-Tarif
description: Hier erfahren Sie, wie Sie das Redis-Clustering für Azure Cache for Redis-Instanzen im Premium-Tarif erstellen und verwalten.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: c4812bb6b352ad57fa07ad766329644ef6af9c57
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122356632"
---
# <a name="configure-redis-clustering-for-a-premium-azure-cache-for-redis-instance"></a>Konfigurieren des Redis-Clusterings für eine Azure Cache for Redis-Instanz im Premium-Tarif

Azure Cache for Redis bietet Redis-Cluster gemäß der [Implementierung in Redis](https://redis.io/topics/cluster-tutorial). Redis Cluster bringt Ihnen die folgenden Vorteile:

* Die Möglichkeit, das DataSet automatisch zwischen mehreren Knoten aufzuteilen.
* Die Möglichkeit, Vorgänge fortzusetzen, wenn bei einer Teilmenge der Knoten Fehler auftreten oder nicht alle Knoten im Cluster miteinander kommunizieren können.
* Höherer Durchsatz: Der Durchsatz erhöht sich linear, wenn Sie die Shardanzahl erhöhen.
* Mehr Arbeitsspeicher: Die Arbeitsspeichergröße erhöht sich linear, wenn Sie die Shardanzahl erhöhen.  

Clustering erhöht nicht die Anzahl von Verbindungen, die für einen gruppierten Cache verfügbar sind. Weitere Informationen zur Größe, zum Durchsatz und zur Bandbreite von Premium-Caches finden Sie unter [Auswählen der richtigen Ebene](cache-overview.md#choosing-the-right-tier).

In Azure wird Redis Cluster als Modell mit primärem Cache und Replikatcache angeboten, in dem jeder Shard über ein Paar aus primärem Cache und Replikatcache mit Replikation verfügt und die Replikation durch den Azure Cache for Redis-Dienst verwaltet wird.

## <a name="set-up-clustering"></a>Einrichten des Clusterings

Clustering wird während der Cacheerstellung links unter **New Azure Cache for Redis** (Azure Cache for Redis – Neu) aktiviert.

1. Melden Sie sich zum Erstellen eines Premium-Caches beim [Azure-Portal](https://portal.azure.com) an, und wählen Sie **Ressource erstellen** aus. Sie können Caches nicht nur über das Azure-Portal, sondern auch mithilfe von Resource Manager-Vorlagen, PowerShell oder der Azure-Befehlszeilenschnittstelle erstellen. Weitere Informationen zum Erstellen einer Azure Cache for Redis-Instanz finden Sie unter [Erstellen eines Caches](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Ressource erstellen.":::

2. Wählen Sie auf der Seite **Neu** die Option **Datenbanken** und dann **Azure Cache for Redis** aus.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Auswählen von „Azure Cache for Redis“.":::

3. Konfigurieren Sie auf der Seite **Neuer Redis Cache** die Einstellungen für den neuen Premium-Cache.

   | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS-Name** | Geben Sie einen global eindeutigen Namen ein. | Der Cache-Name muss eine Zeichenfolge mit 1 bis 63 Zeichen sein. Die Zeichenfolge darf nur Zahlen, Buchstaben oder Bindestriche enthalten. Der Name muss mit einer Zahl oder einem Buchstaben beginnen und enden und darf keine aufeinanderfolgenden Bindestriche enthalten. Der *Hostname* Ihrer Cache-Instanz lautet *\<DNS name>.redis.cache.windows.net*. |
   | **Abonnement** | Öffnen Sie die Dropdownliste, und wählen Sie Ihr Abonnement aus. | Das Abonnement, unter dem diese neue Azure Cache for Redis-Instanz erstellt wird. |
   | **Ressourcengruppe** | Öffnen Sie die Dropdownliste, und wählen Sie eine Ressourcengruppe aus. Alternativ dazu wählen Sie **Neu erstellen** aus und geben einen Namen für eine neue Ressourcengruppe ein. | Der Name der Ressourcengruppe, in der Ihr Cache und weitere Ressourcen erstellt werden. Wenn Sie alle Ihre App-Ressourcen in einer Ressourcengruppe zusammenfassen, können Sie sie einfacher gemeinsam verwalten oder löschen. |
   | **Location** | Öffnen Sie die Dropdownliste, und wählen Sie einen Standort aus. | Wählen Sie eine [Region](https://azure.microsoft.com/regions/) in der Nähe anderer Dienste aus, die Ihren Cache verwenden. |
   | **Cachetyp** | Öffnen Sie die Dropdownliste, und wählen Sie einen Premium-Cache aus, um Premium-Features zu konfigurieren. Weitere Informationen finden Sie unter [Azure Cache for Redis – Preise](https://azure.microsoft.com/pricing/details/cache/). |  Der Tarif bestimmt Größe, Leistung und verfügbare Features für den Cache. Weitere Informationen finden Sie unter [What is Azure Cache for Redis](cache-overview.md) (Was ist Azure Cache for Redis?). |

4. Wählen Sie die Registerkarte **Netzwerk** oder unten auf der Seite die Schaltfläche **Netzwerk** aus.

5. Wählen Sie auf der Registerkarte **Netzwerk** Ihre Konnektivitätsmethode aus. Bei Premium-Cache-Instanzen können Sie die Verbindung entweder öffentlich über öffentliche IP-Adressen oder Dienstendpunkte oder privat über einen privaten Endpunkt herstellen.

6. Wählen Sie die Registerkarte **Weiter: Erweitert** oder unten auf der Seite die Schaltfläche **Weiter: Erweitert** aus.

7. Konfigurieren Sie auf der Registerkarte **Erweitert** für eine Premium-Cache-Instanz die Einstellungen für einen Nicht-TLS-Port sowie für Clustering und Datenpersistenz. Zum Aktivieren des Clusterings klicken Sie auf **Aktivieren**.

    :::image type="content" source="media/cache-how-to-premium-clustering/redis-cache-clustering.png" alt-text="Umschaltfunktion für das Clustering.":::

    Sie können bis zu 10 Shards im Cluster festlegen. Nachdem Sie auf **Aktiviert** geklickt haben, verschieben Sie den Schieberegler, oder geben Sie eine Zahl zwischen 1 und 10 für die **Shardanzahl** ein. Klicken Sie anschließend auf **OK**.

    Jeder Shard ist ein Paar aus primärem Cache und Replikatcache, das in Azure verwaltet wird. Die Gesamtgröße des Caches wird durch Multiplikation der Anzahl der Shards mit der im Tarif ausgewählten Cachegröße berechnet.

    :::image type="content" source="media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png" alt-text="Umschalter für Clustering ausgewählt.":::

    Nach der Erstellung des Caches können Sie eine Verbindung mit dem Cache herstellen und ihn genauso wie einen nicht gruppierten Cache verwenden. Redis verteilt die Daten auf alle Cache-Shards. Wenn die Diagnosefunktion [aktiviert](cache-how-to-monitor.md#enable-cache-diagnostics) ist, werden Metriken für jeden Shard separat erfasst und können links in Azure Cache for Redis [angezeigt](cache-how-to-monitor.md) werden.

8. Wählen Sie die Registerkarte **Weiter: Tags** oder unten auf der Seite die Schaltfläche **Weiter: Tags** aus.

9. Geben Sie optional auf der Registerkarte **Tags** den Namen und den Wert ein, wenn Sie die Ressource kategorisieren möchten.

10. Klicken Sie auf **Überprüfen + erstellen**. Sie werden zur Registerkarte „Überprüfen und erstellen“ weitergeleitet, auf der Azure Ihre Konfiguration überprüft.

11. Wenn die grüne Meldung „Validierung erfolgreich“ angezeigt wird, wählen Sie **Erstellen** aus.

Es dauert eine Weile, bis der Cache erstellt wird. Sie können den Fortschritt auf der Seite **Übersicht** von Azure Cache for Redis überwachen. Wenn **Wird ausgeführt** als **Status** angezeigt wird, ist der Cache einsatzbereit.

> [!NOTE]
>
> Es gibt einige geringfügige Unterschiede, die in Ihrer Clientanwendung erforderlich sind, wenn das Clustering konfiguriert ist. Weitere Informationen finden Sie unter [Muss ich Änderungen an meiner Clientanwendung vornehmen, um Clustering verwenden zu können?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
>
>

Beispielcode zum Arbeiten mit Clustering mit dem StackExchange.Redis-Client finden Sie im [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs)-Teil des [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld)-Beispiels.

<a name="cluster-size"></a>

## <a name="change-the-cluster-size-on-a-running-premium-cache"></a>Ändern der Clustergröße auf einem ausgeführten Premium-Cache

Klicken Sie zum Ändern der Clustergröße in einem ausgeführten Premium-Cache mit aktiviertem Clustering im **Ressourcenmenü** auf **Clustergröße**.

![Redis-Clustergröße][redis-cache-redis-cluster-size]

Verwenden Sie den Schieberegler, oder geben Sie im Textfeld **Shardanzahl** eine Zahl zwischen 1 und 10 ein, um die Clustergröße zu ändern. Wählen Sie dann **OK** aus, um die Änderung zu speichern.

Wenn Sie den Cluster vergrößern, erhöht sich der maximale Durchsatz, und der Cache vergrößert sich. Die Vergrößerung des Clusters führt nicht zu einer Erhöhung der maximalen Anzahl der für Clients verfügbaren Verbindungen.

> [!NOTE]
> Bei der Skalierung eines Clusters wird der Befehl [MIGRATE](https://redis.io/commands/migrate) ausgeführt, bei dem es sich um einen Befehl mit hohen Kosten handelt, daher sollten Sie erwägen, den Vorgang in der Nebenzeit auszuführen. Während der Migration wird bei der Serverlast ein Spitzenwert angezeigt. Das Skalieren eines Clusters ist ein langer Prozess und die benötigte Zeit hängt von der Anzahl der Schlüssel und der Größe der Werte ab, die diesen Schlüsseln zugeordnet sind.
>
>

## <a name="clustering-faq"></a>Clustering – häufig gestellte Fragen

Die folgende Liste enthält Antworten auf häufig gestellte Fragen zum Clustering von Azure Cache for Redis-Instanzen.

* [Muss ich Änderungen an meiner Clientanwendung vornehmen, um das Clustering verwenden zu können?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
* [Wie sind Schlüssel in einem Cluster verteilt?](#how-are-keys-distributed-in-a-cluster)
* [Was ist die maximale Cachegröße, die ich erstellen kann?](#what-is-the-largest-cache-size-i-can-create)
* [Wird das Clustering auf allen Redis-Clients unterstützt?](#do-all-redis-clients-support-clustering)
* [Wie stelle ich eine Verbindung mit dem Cache her, wenn das Clustering aktiviert ist?](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
* [Kann ich direkt eine Verbindung mit den einzelnen Shards des Caches herstellen?](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
* [Kann ich das Clustering für einen bereits erstellten Cache konfigurieren?](#can-i-configure-clustering-for-a-previously-created-cache)
* [Kann ich das Clustering für einen Basic- oder Standard-Cache konfigurieren?](#can-i-configure-clustering-for-a-basic-or-standard-cache)
* [Kann ich das Clustering mit den Redis ASP.NET-Sitzungszustands- und -Ausgabezwischenspeicherungsanbietern verwenden?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)
* [Bei Verwenden von StackExchange.Redis und Clustering erhalte ich MOVE-Ausnahmen. Was soll ich tun?](#im-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do)

### <a name="do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering"></a>Muss ich Änderungen an meiner Clientanwendung vornehmen, um das Clustering verwenden zu können?

* Wenn das Clustering aktiviert ist, ist nur Datenbank 0 verfügbar. Wenn die Clientanwendung mehrere Datenbanken verwendet und versucht, eine andere Datenbank als Datenbank 0 zu lesen oder Schreibvorgänge dafür auszuführen, wird die folgende Ausnahme ausgelöst: `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->``StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
  
  Weitere Informationen finden Sie unter [Redis Cluster Specification - Implemented subset](https://redis.io/topics/cluster-spec#implemented-subset)(Spezifikation für Redis-Cluster – implementierte Teilmenge).
* Wenn Sie [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/)nutzen, müssen Sie Version 1.0.481 oder höher verwenden. Sie stellen eine Verbindung mit dem Cache mit den gleichen [Endpunkten, Ports und Schlüsseln](cache-configure.md#properties) her, die Sie auch für einen Cache verwenden, bei dem das Clustering deaktiviert ist. Der einzige Unterschied besteht darin, dass alle Lese- und Schreibvorgänge für Datenbank 0 erfolgen müssen.
  
  Für andere Clients gelten möglicherweise andere Anforderungen. Weitere Informationen finden Sie unter [Wird das Clustering auf allen Redis-Clients unterstützt?](#do-all-redis-clients-support-clustering)
* Wenn für Ihre Anwendung mehrere Schlüsselvorgänge zu einem einzelnen Befehl zusammengefasst sind, müssen sich alle Schlüssel in demselben Shard befinden. Informationen zum Suchen von Schlüsseln im gleichen Shard finden Sie unter [Wie sind Schlüssel in einem Cluster verteilt?](#how-are-keys-distributed-in-a-cluster)
* Bei Verwendung des Redis ASP.NET-Sitzungszustandsanbieters müssen Sie Version 2.0.1 oder höher verwenden. Weitere Informationen finden Sie unter [Kann ich das Clustering mit den Redis ASP.NET-Sitzungszustands- und -Ausgabezwischenspeicherungsanbietern verwenden?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### <a name="how-are-keys-distributed-in-a-cluster"></a>Wie sind Schlüssel in einem Cluster verteilt?

Laut Redis-Dokumentation zum [Schlüsselverteilungsmodell](https://redis.io/topics/cluster-spec#keys-distribution-model) wird der Schlüsselbereich in 16.384 Slots aufgeteilt. Jeder Schlüssel wird gehasht und einem dieser Slots zugewiesen, die auf die Knoten des Clusters verteilt werden. Sie können konfigurieren, welcher Teil des Schlüssels gehasht ist, um sicherzustellen, dass mehrere Schlüssel in demselben Shard angeordnet sind. Verwenden Sie hierfür Hashtags.

* Schlüssel mit einem Hashtag: Wenn ein Teil des Schlüssels in `{` und `}` gesetzt ist, wird nur dieser Teil des Schlüssels gehasht, um den Hashslot eines Schlüssels zu ermitteln. Die folgenden drei Schlüssel würden sich beispielsweise in demselben Shard befinden: `{key}1`, `{key}2` und `{key}3`, da nur der `key`-Teil des Namens gehasht ist. Eine vollständige Liste mit den Spezifikationen für Schlüssel-Hashtags finden Sie unter [Schlüssel-Hashtags](https://redis.io/topics/cluster-spec#keys-hash-tags).
* Schlüssel ohne Hashtag: Der gesamte Schlüsselname wird für das Hashing verwendet, was zu einer statistisch gleichmäßigen Verteilung auf die Shards des Caches führt.

Zum Erzielen einer optimalen Leistung und eines hohen Durchsatzes empfehlen wir die gleichmäßige Verteilung der Schlüssel. Wenn Sie Schlüssel mit Hashtag verwenden, muss von der Anwendung sichergestellt werden, dass die Schlüssel gleichmäßig verteilt werden.

Weitere Informationen finden Sie unter [Schlüsselverteilungsmodell](https://redis.io/topics/cluster-spec#keys-distribution-model), [Redis Cluster-Daten-Sharding](https://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding) und [Schlüssel-Hashtags](https://redis.io/topics/cluster-spec#keys-hash-tags).

Beispielcode zum Arbeiten mit Clustering und Suchen von Schlüsseln im gleichen Shard mit dem StackExchange.Redis-Client finden Sie im [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs)-Teil des [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld)-Beispiels (Hallo Welt).

### <a name="what-is-the-largest-cache-size-i-can-create"></a>Was ist die maximale Cachegröße, die ich erstellen kann?

Die größte Cachegröße, die Sie haben können, beträgt 1,2 TB. Dabei handelt es sich um einen gruppierten P5-Cache mit 10 Shards. Weitere Informationen finden Sie unter [Azure Cache for Redis – Preise](https://azure.microsoft.com/pricing/details/cache/).

### <a name="do-all-redis-clients-support-clustering"></a>Wird das Clustering auf allen Redis-Clients unterstützt?

Viele, aber nicht alle Clients unterstützen Redis-Clustering. Sehen Sie in der Dokumentation der Bibliothek, die Sie verwenden, nach, um zu überprüfen, ob Sie eine Bibliothek und Version verwenden, die Clustering unterstützen. „StackExchange.Redis“" ist eine Bibliothek, die in ihren neueren Versionen Clustering unterstützt. Weitere Informationen zu anderen Clients finden Sie im [Redis Cluster Tutorial](https://redis.io/topics/cluster-tutorial) im Abschnitt [Playing with the cluster (Arbeiten mit dem Cluster)](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster).

Das Redis-Clusteringprotokoll erfordert, dass jeder Client eine Verbindung mit jedem Shard direkt im Clustermodus herstellen muss, und definiert ferner neue Fehlerantworten wie „MOVED“ und „CROSSSLOTS“. Wenn Sie versuchen, einen Client, der kein Clustering unterstützt, mit einem Cache im Clustermodus zu verwenden, kann dies im Ergebnis zu zahlreichen [MOVED-Umleitungsausnahmen](https://redis.io/topics/cluster-spec#moved-redirection) führen oder einfach Ihre Anwendung beschädigen, wenn Sie slotübergreifende Anforderungen mit mehreren Schlüsseln ausführen.

> [!NOTE]
> Wenn Sie StackExchange.Redis als Client verwenden, müssen Sie sicherstellen, dass Sie die neueste Version [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 oder höher verwenden, damit das Clustering ordnungsgemäß ausgeführt wird. Weitere Informationen zu allen mit MOVE-Ausnahmen finden Sie unter [MOVE-Ausnahmen](#move-exceptions).
>

### <a name="how-do-i-connect-to-my-cache-when-clustering-is-enabled"></a>Wie stelle ich eine Verbindung mit dem Cache her, wenn das Clustering aktiviert ist?

Sie können eine Verbindung mit dem Cache mit den gleichen [Endpunkten](cache-configure.md#properties), [Ports](cache-configure.md#properties) und [Schlüsseln](cache-configure.md#access-keys) herstellen, die Sie auch für einen Cache verwenden, bei dem das Clustering nicht aktiviert ist. Redis verwaltet das Clustering auf dem Back-End, sodass es nicht über Ihren Client verwaltet werden muss.

### <a name="can-i-directly-connect-to-the-individual-shards-of-my-cache"></a>Kann ich direkt eine Verbindung mit den einzelnen Shards des Caches herstellen?

Für das Clusteringprotokoll muss der Client die richtigen Shardverbindungen herstellen, weshalb der Client Freigabeverbindungen für Sie herstellen sollte. Allerdings besteht jeder Shard aus einem Paar aus primärem Cache und Replikatcache, die zusammen als Cacheinstanz bezeichnet werden. Mit dem Hilfsprogramm redis-cli in der [unstable](https://redis.io/download) -Verzweigung des Redis-Repositorys auf GitHub können Sie eine Verbindung mit diesen Cacheinstanzen herstellen. Diese Version implementiert grundlegende Unterstützung, wenn sie mit dem Switch `-c` gestartet wird. Weitere Informationen finden Sie im [Redis Cluster-Tutorial](https://redis.io/topics/cluster-tutorial) unter [Playing with the cluster](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) (Ausprobieren des Clusters) auf [https://redis.io](https://redis.io).

Ohne TLS verwenden Sie die folgenden Befehle.

```bash
Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
...
Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)
```

Bei Verwendung von TLS ersetzen Sie `1300N` durch `1500N`.

### <a name="can-i-configure-clustering-for-a-previously-created-cache"></a>Kann ich das Clustering für einen bereits erstellten Cache konfigurieren?

Ja. Stellen Sie zunächst sicher, dass Ihr Cache Premium ist, und wenn nicht, erzielen Sie dies durch Hochskalierung. Als Nächstes können Sie die Clusterkonfigurationsoptionen anzeigen, einschließlich einer Option zum Aktivieren von Clustern. Ändern Sie die Clustergröße, nachdem der Cache erstellt wurde, oder nachdem Sie das Clustering zum ersten Mal aktiviert haben.

   >[!IMPORTANT]
   >Die Aktivierung des Clusterings lässt sich nicht rückgängig machen. Und ein Cache mit aktivierter Clusterunterstützung und nur einem Shard verhält sich *anders* als ein Cache derselben Größe *ohne* Clustering.

### <a name="can-i-configure-clustering-for-a-basic-or-standard-cache"></a>Kann ich das Clustering für einen Basic- oder Standard-Cache konfigurieren?

Clustering ist nur für Premium-Caches verfügbar.

### <a name="can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers"></a>Kann ich das Clustering mit den Redis ASP.NET-Sitzungszustands- und -Ausgabezwischenspeicherungsanbietern verwenden?

* **Redis-Ausgabecacheanbieter:** Keine Änderungen erforderlich.
* **Redis-Sitzungszustandsanbieter:** Zum Verwenden des Clusterings müssen Sie [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 oder höher verwenden. Andernfalls wird eine Ausnahme ausgelöst, wobei es sich um einen Breaking Change handelt. Weitere Informationen finden Sie unter [v2.0.0 Breaking Change Details (v2.0.0 – Details zu unterbrechenden Änderungen)](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

<a name="move-exceptions"></a>

### <a name="im-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do"></a>Bei Verwenden von StackExchange.Redis und Clustering erhalte ich MOVE-Ausnahmen. Was soll ich tun?

Wenn Sie StackExchange.Redis und Clustering verwenden und dabei `MOVE` -Ausnahmen erhalten, vergewissern Sie sich, dass Sie mindestens [StackExchange.Redis 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) verwenden. Anweisungen zum Konfigurieren Ihrer .NET-Anwendungen für die Verwendung von StackExchange.Redis finden Sie unter [Konfigurieren der Cacheclients](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Azure Cache for Redis-Features.

* [Premium-Dienstebenen für Azure Cache for Redis](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png
