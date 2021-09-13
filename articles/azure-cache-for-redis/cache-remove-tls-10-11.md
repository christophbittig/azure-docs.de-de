---
title: Entfernen der Verwendung von TLS 1.0 und 1.1 mit Azure Cache for Redis
description: Erfahren Sie, wie Sie TLS 1.0 und 1.1 bei der Kommunikation mit Azure Cache for Redis aus Ihrer Anwendung entfernen.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: f9c6f8cc3c973d3172166514815018979f744173
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122347003"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>Entfernen der Verwendung von TLS 1.0 und 1.1 mit Azure Cache for Redis

Es zeichnet sich ein branchenweiter Trend zur ausschließlichen Verwendung von Transport Layer Security (TLS) Version 1.2 oder höher ab. Die TLS-Versionen 1.0 und 1.1 sind bekanntermaßen anfällig für Angriffe wie BEAST und POODLE und weisen andere allgemeine Sicherheitslücken und Schwachstellen (CVE, Common Vulnerabilities and Exposures) auf. Außerdem unterstützen diese Versionen nicht die durch die Konformitätsstandards der Payment Card Industry (PCI) empfohlenen modernen Verschlüsselungsmethoden und Verschlüsselungssammlungen. Einige dieser Sicherheitslücken werden in diesem [Blog zur TLS-Sicherheit](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) ausführlicher erläutert.

Im Rahmen dieser Anstrengung nehmen wir die folgenden Änderungen an Azure Cache for Redis vor:

* **Phase 1:** Wir konfigurieren die standardmäßige TLS-Mindestversion für neu erstellte Cache-Instanzen mit 1.2 (zuvor TLS 1.0). Vorhandene Cache-Instanzen werden zu diesem Zeitpunkt nicht aktualisiert. Sie können weiterhin das Azure-Portal oder andere Verwaltungs-APIs nutzen, um zum Zweck der Abwärtskompatibilität [die TLS-Mindestversion](cache-configure.md#access-ports) auf 1.0 oder 1.1 zu ändern.
* **Phase 2:** Wir stellen die Unterstützung der TLS-Versionen 1.1 und 1.0 ein. Nach dieser Änderung muss Ihre Anwendung für die Kommunikation mit dem Cache TLS 1.2 oder höher verwenden. Es wird vorausgesetzt, dass der Dienst Azure Cache for Redis verfügbar ist, während wir ihn zur Unterstützung von TLS 1.2 oder höher migrieren.

  > [!NOTE]
  > Phase 2 wird aufgrund von COVID-19 verschoben. Es wird dringend empfohlen, jetzt mit der Planung für diese Änderung zu beginnen und Clients zur Unterstützung von TLS 1.2 oder höher proaktiv zu aktualisieren. 
  >

Als Teil dieser Änderung entfernen wir auch die Unterstützung für ältere Verschlüsselungssammlungen, die nicht sicher sind. Die unterstützten Verschlüsselungsverfahren sind auf folgende beschränkt, wenn der Cache mit TLS 1.2 oder höher konfiguriert ist:

* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

Dieser Artikel enthält eine allgemeine Anleitung zum Erkennen von Abhängigkeiten von diesen früheren TLS-Versionen und zum Entfernen der Versionen aus Ihrer Anwendung.

Die Daten, zu denen diese Änderungen wirksam werden sind:

| Cloud                | Phase 1 Startdatum | Phase 2 Startdatum         |
|----------------------|--------------------|----------------------------|
| Azure (global)       |  13. Januar 2020  | Aufgrund von COVID-19 verschoben  |
| Azure Government     |  13. März 2020    | Aufgrund von COVID-19 verschoben  |
| Azure Deutschland        |  13. März 2020    | Aufgrund von COVID-19 verschoben  |
| Azure China 21Vianet |  13. März 2020    | Aufgrund von COVID-19 verschoben  |

> [!NOTE]
> Phase 2 wird aufgrund von COVID-19 verschoben. Dieser Artikel wird aktualisiert, sobald spezifische Termine festgelegt wurden.
>

## <a name="check-whether-your-application-is-already-compliant"></a>Überprüfen, ob Ihre Anwendung bereits konform ist

Sie können herausfinden, ob Ihre Anwendung mit TLS 1.2 funktioniert, indem Sie den Wert **TLS-Mindestversion** in einem Test- oder Stagingcache auf TLS 1.2 festlegen und anschließend Tests ausführen. Die Einstellung **TLS-Mindestversion** befindet sich unter [Erweiterte Einstellungen](cache-configure.md#advanced-settings) für Ihre Cache-Instanz im Azure-Portal.  Wenn die Anwendung nach dieser Änderung weiterhin erwartungsgemäß ausgeführt wird, ist sie wahrscheinlich konform. Möglicherweise müssen Sie die in der Anwendung verwendete Redis-Clientbibliothek so konfigurieren, dass TLS 1.2 aktiviert wird, um eine Verbindung mit Azure Cache for Redis herzustellen.

## <a name="configure-your-application-to-use-tls-12"></a>Konfigurieren Ihrer Anwendung für die Verwendung von TLS 1.2

In den meisten Anwendungen werden Redis-Clientbibliotheken für die Kommunikation mit den zugehörigen Caches verwendet. Hier finden Sie Anweisungen zum Konfigurieren einiger gängiger Clientbibliotheken in verschiedenen Programmiersprachen und Frameworks für die Verwendung von TLS 1.2.

### <a name="net-framework"></a>.NET Framework

Bei Redis .NET-Clients wird standardmäßig die früheste TLS-Version in .NET Framework 4.5.2 oder früher und die neueste TLS-Version in .NET Framework 4.6 oder höher verwendet. Wenn Sie eine ältere Version von .NET Framework verwenden, aktivieren Sie TLS 1.2 manuell:

* **StackExchange.Redis:** Legen Sie `ssl=true` und `sslprotocols=tls12` in der Verbindungszeichenfolge fest.
* **ServiceStack.Redis:** Befolgen Sie die Anweisungen für [ServiceStack.Redis](https://github.com/ServiceStack/ServiceStack.Redis#servicestackredis-ssl-support). Dazu ist mindestens ServiceStack.Redis v5.6 erforderlich.

### <a name="net-core"></a>.NET Core

Redis .NET Core-Clients verwenden standardmäßig die TLS-Standardversion des Betriebssystems, die vom Betriebssystem abhängig ist. 

Je nach verwendeter Betriebssystemversion und den angewandten Patches kann die TLS-Standardversion variieren. Weitere Informationen finden Sie [hier](/dotnet/framework/network-programming/#support-for-tls-12).

Wenn Sie ein altes Betriebssystem verwenden oder nur sichergehen möchten, wird empfohlen, die bevorzugte TLS-Version manuell über den Client zu konfigurieren.


### <a name="java"></a>Java

Bei Redis Java-Clients wird TLS 1.0 für Java Version 6 oder früher verwendet. Bei Jedis, Lettuce, und Redisson kann keine Verbindung mit Azure Cache for Redis hergestellt werden, wenn TLS 1.0 für den Cache deaktiviert ist. Führen Sie ein Upgrade Ihres Java-Frameworks durch, um neue TLS-Versionen zu verwenden.

Für Java 7 wird bei Redis-Clients standardmäßig TLS 1.2 nicht verwendet, die Clients können aber dafür konfiguriert werden. In Jedis können Sie die zugrunde liegenden TLS-Einstellungen mit dem folgenden Codeausschnitt angeben:

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

Die Lettuce- und Redisson-Clients unterstützen noch nicht das Festlegen der TLS-Version. Es erfolgt ein Abbruch, wenn der Cache nur TLS 1.2-Verbindungen akzeptiert. Korrekturen für diese Clients werden überprüft. Sehen Sie also für diese Pakete nach, ob eine aktualisierte Version mit dieser Unterstützung vorhanden ist.

In Java 8 wird standardmäßig TLS 1.2 verwendet und sollte in den meisten Fällen keine Updates Ihrer Clientkonfiguration erfordern. Um sicherzugehen, testen Sie Ihre Anwendung.

### <a name="nodejs"></a>Node.js

Bei Node Redis und IORedis wird standardmäßig TLS 1.2 verwendet.

### <a name="php"></a>PHP

#### <a name="predis"></a>Predis
 
* Versionen vor PHP 7: Predis unterstützt nur TLS 1.0. Diese Versionen funktionieren nicht mit TLS 1.2. Führen Sie ein Upgrade durch, um TLS 1.2 verwenden zu können.
 
* PHP 7.0 bis PHP 7.2.1: Predis verwendet standardmäßig nur TLS 1.0 oder 1.1. Zur Verwendung von TLS 1.2 kann die folgende Problemumgehung verwendet werden: Geben Sie beim Erstellen der Clientinstanz TLS 1.2 an:

  ``` PHP
  $redis=newPredis\Client([
      'scheme'=>'tls',
      'host'=>'host',
      'port'=>6380,
      'password'=>'password',
      'ssl'=>[
          'crypto_type'=>STREAM_CRYPTO_METHOD_TLSv1_2_CLIENT,
      ],
  ]);
  ```

* PHP 7.3 und höhere Versionen: Predis verwendet die aktuelle TLS-Version.

#### <a name="phpredis"></a>PhpRedis

Bei PhpRedis wird TLS in keiner PHP-Version unterstützt.

### <a name="python"></a>Python

Bei Redis-py wird standardmäßig TLS 1.2 verwendet.

### <a name="go"></a>GO

Bei Redigo wird standardmäßig TLS 1.2 verwendet.

## <a name="additional-information"></a>Zusätzliche Informationen

- [Konfigurieren von Azure Cache for Redis](cache-configure.md)