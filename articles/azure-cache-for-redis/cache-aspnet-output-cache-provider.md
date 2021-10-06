---
title: ASP.NET-Ausgabecacheanbieter für Azure Cache for Redis
description: Erfahren Sie, wie Sie die ASP.NET-Seitenausgabe mit Azure Cache for Redis zwischenspeichern. Der Redis-Ausgabecacheanbieter ist ein prozessunabhängiger Speichermechanismus für Ausgabecachedaten.
author: curib
ms.author: cauribeg
ms.service: cache
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.date: 04/22/2018
ms.openlocfilehash: 7354f0504197742a8c8acf2d0c555a524fef95d9
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129538850"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>ASP.NET-Ausgabecacheanbieter für Azure Cache for Redis

Der Redis-Ausgabecacheanbieter ist ein prozessunabhängiger Speichermechanismus für Ausgabecachedaten. Diese Daten sind für vollständige HTTP-Antworten bestimmt (Zwischenspeichern von Seitenausgaben). Der Anbieter wird zum neuen Erweiterungspunkt des Ausgabecacheanbieters hinzugefügt, der in ASP.NET 4 eingeführt wurde. Informationen zu ASP.NET Core-Anwendungen finden Sie unter [Zwischenspeichern von Antworten in ASP.NET Core](/aspnet/core/performance/caching/response). 

Zum Verwenden des Redis-Ausgabecacheanbieters konfigurieren Sie zunächst den Cache. Danach konfigurieren Sie Ihre ASP.NET-Anwendung mithilfe des Redis-Ausgabecacheanbieter-NuGet-Pakets. Dieser Artikel enthält eine Anleitung darüber, wie Sie Ihre Anwendung für die Verwendung des Redis-Ausgabecacheanbieters konfigurieren. Informationen über das Erstellen und Konfigurieren einer Azure Cache for Redis-Instanz finden Sie unter [Erstellen eines Caches](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Speichern der ASP.NET-Seitenausgabe im Cache

Wählen Sie im Menü **Extras** im **NuGet-Paket-Manager** die Option **Paket-Manager-Konsole** zum Konfigurieren einer Clientanwendung in Visual Studio mit dem Azure Cache for Redis Session State NuGet-Paket.

Führen Sie im Fenster `Package Manager Console` den folgenden Befehl aus.

```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Das Redis-Ausgabecacheanbieter-NuGet-Paket steht in einer Abhängigkeitsbeziehung zum Paket „StackExchange.Redis.StrongName“. Wenn das Paket „StackExchange.Redis.StrongName“ nicht in Ihrem Projekt vorhanden ist, wird es installiert. Weitere Informationen zum Redis-Ausgabecacheanbieter-NuGet-Paket finden Sie auf der NuGet-Seite [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/).

>[!NOTE]
>Neben dem Paket „StackExchange.Redis.StrongName“ mit starkem Namen gibt es zudem noch das Paket „StackExchange.Redis“ als Version ohne starken Namen. Wenn in Ihrem Projekt die Version „StackExchange.Redis“ ohne starken Namen verwendet wird, müssen Sie sie deinstallieren. Andernfalls treten Namenskonflikte in Ihrem Projekt auf. Weitere Informationen über diese Pakete finden Sie unter [Konfigurieren der .NET-Cacheclients](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

Mit dem heruntergeladenen NuGet-Paket werden die erforderlichen Assemblyverweise sowie der folgende Abschnitt in Ihrer Datei „web.config“ hinzugefügt. Dieser Abschnitt enthält die Konfiguration, die in Ihrer ASP.NET-Anwendung für die Verwendung des Redis-Ausgabecacheanbieters erforderlich ist.

```xml
<caching>
  <outputCache defaultProvider="MyRedisOutputCache">
    <providers>
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider"
           host=""
           accessKey=""
           ssl="true" />
    </providers>
  </outputCache>
</caching>
```

Konfigurieren Sie die Attribute auf der linken Seite mit den Werten aus Ihrem Cache im Microsoft Azure-Portal. Konfigurieren Sie außerdem die anderen von Ihnen gewünschten Werte. Anweisungen zum Zugreifen auf die Cacheeigenschaften finden Sie unter [Konfigurieren von Azure Cache for Redis-Einstellungen](cache-configure.md#configure-azure-cache-for-redis-settings).

| attribute | type | Standard | BESCHREIBUNG |
| --------- | ---- | ------- | ----------- |
| *host* | Zeichenfolge | „localhost“ | IP-Adresse oder Hostname des Redis-Servers |
| *port* | Positive Ganzzahl | 6379 (TLS-/SSL-fremd)<br/>6380 (TLS/SSL) | Redis-Serverport |
| *accessKey* | Zeichenfolge | "" | Redis-Serverkennwort, wenn Redis-Autorisierung aktiviert ist. Der Wert ist standardmäßig eine leere Zeichenfolge, was bedeutet, dass der Sitzungszustandsanbieter kein Kennwort verwendet, wenn eine Verbindung mit dem Redis-Server hergestellt wird. **Wenn sich Ihr Redis-Server in einem öffentlich zugänglichen Netzwerk wie Azure Cache for Redis befindet, denken Sie daran, die Redis-Autorisierung zu aktivieren, um die Sicherheit zu erhöhen, und ein sicheres Kennwort anzugeben.** |
| *ssl* | boolean | **false** | Gibt an, ob die Verbindung mit dem Redis-Server über TLS hergestellt werden soll. Dieser Wert ist standardmäßig auf **false** gestellt, da der TLS von Redis nicht standardmäßig unterstützt wird. **Wenn Sie Azure Cache for Redis verwenden, das standardmäßig SSL unterstützt, sollten Sie diesen Wert auf „true“ setzen, um die Sicherheit zu erhöhen.**<br/><br/>Der TLS-fremde Port ist für neue Caches standardmäßig deaktiviert. Legen Sie diese Einstellung auf **true** fest, um den TLS-fremden Port zu verwenden. Weitere Informationen zum Aktivieren des TLS-fremden Ports finden Sie im Abschnitt [Zugriffsports](cache-configure.md#access-ports) des Artikels [Konfigurieren eines Caches](cache-configure.md). |
| *databaseIdNumber* | Positive Ganzzahl | 0 | *Dieses Attribut kann nur entweder über „web.config“ oder über „appSettings“ angegeben werden.*<br/><br/>Geben Sie an, welche Redis-Datenbank verwendet werden soll. |
| *connectionTimeoutInMilliseconds* | Positive Ganzzahl | Wird von „StackExchange.Redis“ angegeben. | Wird zum Festlegen von *ConnectTimeout* verwendet, wenn „StackExchange.Redis.ConnectionMultiplexer“ erstellt wird. |
| *operationTimeoutInMilliseconds* | Positive Ganzzahl | Wird von „StackExchange.Redis“ angegeben. | Wird zum Festlegen von *SyncTimeout* verwendet, wenn „StackExchange.Redis.ConnectionMultiplexer“ erstellt wird. |
| *connectionString* (Gültige Verbindungszeichenfolge für „StackExchange.Redis“) | Zeichenfolge | *Nicht zutreffend* | Entweder ein Parameterverweis auf „appSettings“ oder auf „Web.config“ oder sonst eine gültige Verbindungszeichenfolge für „StackExchange.Redis“. Dieses Attribut kann die Werte für *host*, *port*, *accessKey*, *ssl* und andere Attribute von „StackExchange.Redis“ bereitstellen. Ausführlichere Informationen zu *connectionString* finden Sie unter [Festlegen von „connectionString“](#setting-connectionstring) im Abschnitt [Hinweise zu Attributen](#attribute-notes). |
| *settingsClassName*<br/>*settingsMethodName* | Zeichenfolge<br/>Zeichenfolge | *Nicht zutreffend* | *Diese Attribute können nur entweder über „web.config“ oder über „appSettings“ angegeben werden.*<br/><br/>Verwenden Sie diese Attribute, um eine Verbindungszeichenfolge bereitzustellen. *settingsClassName* sollte ein Assembly-qualifizierter Klassenname sein, der die von *settingsMethodName* angegebene Methode enthält.<br/><br/>Die von *settingsMethodName* angegebene Methode sollte öffentlich, statisch und „void“ (keine Parameter akzeptieren) sein, und den Rückgabetyp **string** besitzen. Diese Methode gibt die tatsächliche Verbindungszeichenfolge zurück. |
| *loggingClassName*<br/>*loggingMethodName* | Zeichenfolge<br/>Zeichenfolge | *Nicht zutreffend* | *Diese Attribute können nur entweder über „web.config“ oder über „appSettings“ angegeben werden.*<br/><br/>Verwenden Sie diese Attribute, um Ihre Anwendung zu debuggen, indem Sie Protokolle aus dem Sitzungszustands-/Ausgabecache zusammen mit Protokollen aus „StackExchange.Redis“ bereitstellen. *loggingClassName* sollte ein Assembly-qualifizierter Klassenname sein, der die von *loggingMethodName* angegebene Methode enthält.<br/><br/>Die von *loggingMethodName* angegebene Methode sollte öffentlich, statisch und „void“ (keine Parameter akzeptieren) sein, und den Rückgabetyp **System.IO.TextWriter** besitzen. |
| *applicationName* | Zeichenfolge | Der Modulname des aktuellen Prozesses oder „/“ | *Nur SessionStateProvider*<br/>*Dieses Attribut kann nur entweder über „web.config“ oder über „appSettings“ angegeben werden.*<br/><br/>Das in Redis Cache zu verwendende App-Namenspräfix. Der Kunde kann denselben Redis Cache für unterschiedliche Zwecke verwenden. Um sicherzustellen, dass bei den Sitzungsschlüsseln kein Konflikt entsteht, kann der Anwendungsname vorangestellt werden. |
| *throwOnError* | boolean | true | *Nur SessionStateProvider*<br/>*Dieses Attribut kann nur entweder über „web.config“ oder über „appSettings“ angegeben werden.*<br/><br/>Ob eine Ausnahme ausgelöst werden soll, wenn ein Fehler auftritt.<br/><br/>Weitere Informationen zu *throwOnError* finden Sie unter [ Hinweise zu *throwOnError*](#notes-on-throwonerror) im Abschnitt [Hinweise zu Attributen](#attribute-notes). |
| *retryTimeoutInMilliseconds* | Positive Ganzzahl | 5.000 | *Nur SessionStateProvider*<br/>*Dieses Attribut kann nur entweder über „web.config“ oder über „appSettings“ angegeben werden.*<br/><br/>Wie lange wiederholt werden soll, wenn ein Vorgang fehlschlägt. Wenn dieser Wert kleiner als *operationTimeoutInMilliseconds* ist, versucht es der Anbieter nicht noch mal.<br/><br/>Weitere Informationen zu *retryTimeoutInMilliseconds* finden Sie unter [ Hinweise zu *retryTimeoutInMilliseconds*](#notes-on-retrytimeoutinmilliseconds) im Abschnitt [Hinweise zu Attributen](#attribute-notes). |
| *redisSerializerType* | Zeichenfolge | *Nicht zutreffend* | Gibt den Assembly-qualifizierten Typnamen einer Klasse an, die „Microsoft.Web.Redis“ implementiert. Ein Serialisierungsprogramm das eine benutzerdefinierte Logik zum Serialisieren und Deserialisieren der Werte enthält. Weitere Informationen finden Sie unter [Informationen zu *redisSerializerType*](#about-redisserializertype) im Abschnitt [Hinweise zu Attributen](#attribute-notes). |

## <a name="attribute-notes"></a>Hinweise zu Attributen

### <a name="setting-connectionstring"></a>Festlegen von *connectionString*

Der Wert von *connectionString* wird als Schlüssel verwendet, um die tatsächliche Verbindungszeichenfolge von „AppSettings“ abzurufen, wenn solch eine Zeichenfolge in „AppSettings“ vorhanden ist. Wenn sie nicht in „AppSettings“ gefunden wird, wird der Wert von *connectionString* als Schlüssel verwendet, um die tatsächliche Verbindungszeichenfolge aus dem Abschnitt **ConnectionString** der Datei „web.config“ abzurufen, wenn dieser Abschnitt vorhanden ist. Wenn die Verbindungszeichenfolge weder in „AppSettings“ noch in dem Abschnitt **ConnectionString** von „web.config“ vorhanden ist, wird der Literalwert von *connectionString* als Verbindungszeichenfolge verwendet, wenn ein „StackExchange.Redis.ConnectionMultiplexer“ erstellt wird.

Die folgenden Beispiele veranschaulichen, wie *connectionString* verwendet wird.

#### <a name="example-1"></a>Beispiel 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

Verwenden Sie in `web.config` den oben stehenden Schlüssel als Parameterwert anstelle des tatsächlichen Werts.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-2"></a>Beispiel 2

```xml
<appSettings>
    <add key="MyRedisConnectionString" value="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</appSettings>
```

Verwenden Sie in `web.config` den oben stehenden Schlüssel als Parameterwert anstelle des tatsächlichen Werts.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-3"></a>Beispiel 3

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False"/>
    </providers>
</sessionState>
```

### <a name="notes-on-throwonerror"></a>Hinweise zu *throwOnError*

Zurzeit löst der Sitzungszustandsanbieter, wenn während eines Sitzungsvorgangs ein Fehler auftritt, eine Ausnahme aus. Durch das Auslösen der Ausnahme wird die Anwendung heruntergefahren.

Dieses Verhalten wurde in einer Weise geändert, die die Erwartungen der Benutzer von vorhandenen ASP.NET-Sitzungszustandsanbietern erfüllt und Ihnen gleichzeitig die Möglichkeit bietet, auf Ausnahmen zu reagieren. Beim Standardverhalten wird immer noch eine Ausnahme ausgelöst, wenn ein Fehler auftritt, der mit anderen ASP.NET-Sitzungsstatusanbietern konsistent ist. Ein vorhandener Code sollte genauso funktionieren wie zuvor.

Wenn Sie Einstellung *throwOnError* auf **false** festlegen, dann wird beim Auftreten eines Fehlers keine Ausnahme ausgelöst. Um festzustellen, ob ein Fehler aufgetreten ist, und wenn dies der Fall war, um die Ausnahme zu ermitteln, überprüfen Sie die statische Eigenschaft *Microsoft.Web.Redis.RedisSessionStateProvider.LastException*.

### <a name="notes-on-retrytimeoutinmilliseconds"></a>Hinweise zu *retryTimeoutInMilliseconds*

Die Einstellung *retryTimeoutInMilliseconds* bietet eine gewisse Logik, um den Fall zu vereinfachen, in dem ein Sitzungsvorgang bei einem Fehler aufgrund einer Netzwerkpanne oder eines anderen Vorgangs erneut versucht werden soll. Mit der Einstellung *retryTimeoutInMilliseconds* können Sie auch das Wiederholungs-Timeout steuern oder die Wiederholung vollständig abmelden.

Wenn Sie die Einstellung *retryTimeoutInMilliseconds* auf eine Zahl, z. B. 2000, festlegen und ein Fehler bei einem Sitzungsvorgang auftritt, wird der Vorgang 2000 Millisekunden lang erneut versucht, bevor er als Fehler behandelt wird. Damit der Sitzungszustandsanbieter diese Wiederholungslogik anwendet, konfigurieren Sie lediglich das Timeout. Der erste Wiederholungsversuch erfolgt nach 20 Millisekunden, was in den meisten Fällen ausreichend ist, wenn eine Netzwerkstörung auftritt. Anschließend wird der Vorgang jede Sekunde wiederholt, bis das Timeout eintritt. Direkt nach dem Timeout erfolgt noch eine einzige Wiederholung, um sicherzustellen, dass das Timeout nicht um (höchstens um) eine Sekunde unterschritten wird.

Wenn Sie glauben, dass Sie die Wiederholung nicht benötigen oder wenn Sie die Wiederholungslogik selbst übernehmen möchten, setzen Sie die Einstellung *retryTimeoutInMilliseconds* auf 0. Wenn Sie beispielsweise den Redis-Server auf demselben Computer wie Ihre Anwendung ausführen, sollten Sie den Vorgang nicht wiederholen.

### <a name="about-redisserializertype"></a>Informationen zu *redisSerializerType*

Die Serialisierung zum Speichern der Werte auf Redis erfolgt standardmäßig in einem Binärformat, das von der **BinaryFormatter**-Klasse bereitgestellt wird. Verwenden Sie *redisSerializerType*, um den Assembly-qualifizierten Typnamen einer Klasse anzugeben, die **Microsoft.Web.Redis.ISerializer** implementiert und die benutzerdefinierte Logik zum Serialisieren und Deserialisieren der Werte besitzt. Hier sehen Sie z. B. eine Json-Serialisierungsklasse, die JSON.NET verwendet:

```cs
namespace MyCompany.Redis
{
    public class JsonSerializer : ISerializer
    {
        private static JsonSerializerSettings _settings = new JsonSerializerSettings() { TypeNameHandling = TypeNameHandling.All };

        public byte[] Serialize(object data)
        {
            return Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(data, _settings));
        }

        public object Deserialize(byte[] data)
        {
            if (data == null)
            {
                return null;
            }
            return JsonConvert.DeserializeObject(Encoding.UTF8.GetString(data), _settings);
        }
    }
}
```

Unter der Voraussetzung, dass diese Klasse in einer Assembly mit dem Namen **MyCompanyDll** definiert ist, können Sie den Parameter *redisSerializerType* festlegen, um sie zu verwenden:

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvider"
             name = "MySessionStateStore"
             redisSerializerType = "MyCompany.Redis.JsonSerializer,MyCompanyDll"
             ... />
    </providers>
</sessionState>
```

## <a name="output-cache-directive"></a>OutputCache-Direktive

Fügen Sie jeder Seite, für die Sie die Ausgabe zwischenspeichern möchten, eine OutputCache-Direktive hinzu.

```xml
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

Im vorherigen Beispiel verbleiben die zwischengespeicherten Seitendaten 60 Sekunden lang im Cache. Für jede Parameterkombination wird eine andere Version der Seite zwischengespeichert. Weitere Informationen über die OutputCache-Direktive finden Sie unter [@OutputCache](/previous-versions/dotnet/netframework-4.0/hdxfb6cy(v=vs.100)).

Nach dem Abschluss dieser Schritte ist Ihre Anwendung für die Verwendung des Redis-Ausgabecacheanbieters konfiguriert.

## <a name="third-party-output-cache-providers"></a>Ausgabecacheanbieter von Drittanbieter

* [NCache](https://www.alachisoft.com/blogs/how-to-use-a-distributed-cache-for-asp-net-output-cache/)
* [Apache Ignite](https://apacheignite-net.readme.io/docs/aspnet-output-caching)

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die Seite [ASP.NET-Sitzungszustandsanbieter für Azure Cache for Redis](cache-aspnet-session-state-provider.md).
