---
title: Beispiele für Azure Cache for Redis
description: 'Erfahren Sie anhand der folgenden Codebeispiele, wie Sie Azure Cache for Redis verwenden können: Herstellen einer Verbindung mit einem Cache, Lesen von Daten aus einem Cache und Schreiben in einen Cache, Anbieter von ASP.NET-Azure Cache for Redis.'
author: curib
ms.author: cauribeg
ms.service: cache
ms.custom: devx-track-dotnet
ms.topic: sample
ms.date: 01/23/2017
ms.openlocfilehash: 899bf97fc638fa2a763013319e517b76cd37d361
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129537621"
---
# <a name="azure-cache-for-redis-samples"></a>Beispiele für Azure Cache for Redis
In diesem Artikel finden Sie eine Liste von Azure Cache for Redis-Beispielen. Die Beispiele behandeln Szenarien wie die folgenden: 

* Herstellen einer Verbindung mit einem Cache
* Lesen von Daten aus einem Cache und Schreiben von Daten in einen Cache
* Verwenden der ASP.NET Azure Cache for Redis-Anbieter 

Einige Beispiele können als Projekte heruntergeladen werden. Andere Beispiele bieten schrittweise Anleitungen, die Codeausschnitte enthalten, aber nicht mit einem herunterladbaren Projekt verknüpft sind.

## <a name="hello-world-samples"></a>Hello World-Beispiele
Die Beispiele in diesem Abschnitt zeigen die Grundlagen zum Herstellen einer Verbindung mit einer Azure Cache for Redis-Instanz. Das Beispiel zeigt auch das Lesen von Daten aus dem Cache sowie das Schreiben von Daten in den Cache mit verschiedenen Sprachen und Redis-Clients.

Das [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld)-Beispiel zeigt, wie verschiedene Cachevorgänge mithilfe des [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis)-.NET-Clients ausgeführt werden.

Dieses Beispiel zeigt Folgendes:

* Verwenden von verschiedenen Verbindungsoptionen
* Lesen und Schreiben von Objekten aus dem und in den Cache mithilfe von synchronen und asynchronen Vorgängen
* Verwenden der Redis-MGET/MSET-Befehle zum Zurückgeben von Werten aus angegebenen Schlüsseln
* Ausführen von Redis-Transaktionsvorgängen
* Arbeiten mit Listen und sortierten Sätzen von Redis
* Speichern von .NET-Objekten mithilfe von JsonConvert-Serialisierungsprogrammen
* Verwenden von Redis-Sätzen zum Implementieren des Taggings
* Arbeiten mit dem Redis-Cluster

Weitere Informationen finden Sie in der [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis)-Dokumentation auf GitHub. Weitere Verwendungsszenarien finden Sie in den [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/tests)-Komponententests.

[Schnellstart: Verwenden von Azure Cache for Redis mit Python](cache-python-get-started.md) zeigt die ersten Schritte mit Azure Cache for Redis mit Python und dem Client [redis-py](https://github.com/andymccurdy/redis-py).

[Arbeiten mit .NET-Objekten im Cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) zeigt eine Möglichkeit zum Serialisieren von .NET-Objekten, um sie in eine Azure Cache for Redis-Instanz zu schreiben und daraus zu lesen. 

## <a name="use-azure-cache-for-redis-as-a-scale-out-backplane-for-aspnet-signalr"></a>Verwenden von Azure Cache for Redis als horizontal skalierbare Backplane für ASP.NET SignalR
Das Beispiel [Use Redis Cache as a Scale out Backplane for ASP.NET SignalR](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) (Verwenden von Azure Cache for Redis als aufskalierbare Backplane für ASP.NET SignalR) veranschaulicht die Verwendung von Azure Cache for Redis als SignalR-Backplane. Weitere Informationen zur Backplane finden Sie unter [Horizontales Hochskalieren von SignalR mit Redis](https://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="azure-cache-for-redis-customer-query-sample"></a>Azure Cache for Redis: Kundenabfragebeispiel
Dieses Beispiel vergleicht die Leistung zwischen dem Zugriff auf Daten aus einem Cache und dem Zugriff auf Daten in persistentem Speicher. Dieses Beispiel enthält zwei Projekte.

* [Demo how Redis Cache can improve performance by Caching data](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample) (Demo: Verbessern der Leistung durch Zwischenspeichern von Daten in Redis Cache)
* [Seeding der Datenbank und des Caches für die Demo](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>ASP.NET-Sitzungszustand und Ausgabezwischenspeicherung
Das Beispiel [Use Azure Cache for Redis to store ASP.NET SessionState and OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) (Verwenden von Azure Cache for Redis zum Speichern von „SessionState“ und „OutputCache“ von ASP.NET) veranschaulicht Folgendes:  

* Verwenden von Azure Cache for Redis zum Speichern von ASP.NET-Sitzungs- und -Ausgabecaches
* Verwenden der „SessionState“- und „OutputCache“-Anbieter für Redis.

## <a name="manage-azure-cache-for-redis-with-maml"></a>Verwalten von Azure Cache for Redis mit MAML
Das Beispiel [Manage Azure Redis Cache using Azure Management Libraries](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) (Verwalten von Azure Redis Cache mithilfe von Azure-Verwaltungsbibliotheken) veranschaulicht, wie Sie Azure-Verwaltungsbibliotheken zum Verwalten (Erstellen/Aktualisieren/Löschen) Ihres Caches verwenden. 

## <a name="custom-monitoring-sample"></a>Beispiel einer benutzerdefinierten Überwachung
Das Beispiel [Access Redis Cache Monitoring data](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) (Zugreifen auf Überwachungsdaten von Redis Cache) zeigt, wie Sie außerhalb des Azure-Portals auf Überwachungsdaten für Ihre Azure Cache for Redis-Instanz zugreifen.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>Ein Klon im Stil von Twitter, geschrieben mit PHP und Redis
Das Beispiel [Retwis](https://github.com/SyntaxC4-MSFT/retwis) ist die Hello-World-Variante von Redis. Es handelt sich um den minimalen Klon eines sozialen Netzwerks im Twitter-Stil, der mithilfe von Redis und PHP mit dem [Predis](https://github.com/nrk/predis)-Client geschrieben wurde. Der Quellcode ist einfach gehalten und zeigt dennoch verschiedene Redis-Datenstrukturen.

## <a name="bandwidth-monitor"></a>Bandbreitenüberwachung
Das Beispiel [Bandbreitenüberwachung](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) ermöglicht die Überwachung der auf dem Client genutzten Bandbreite. Um die Bandbreite zu messen, führen Sie das Beispiel auf dem Cacheclientcomputer aus, rufen Sie den Cache auf, und beobachten Sie die durch das Beispiel gemeldete Bandbreite.
