---
title: 'Schnellstart: Verwenden von Azure Cache for Redis mit .NET Framework'
description: In dieser Schnellstartanleitung wird beschrieben, wie Sie über Ihre .NET-Apps auf Azure Cache for Redis zugreifen.
author: curib
ms.author: cauribeg
ms.service: cache
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp, mvc
ms.date: 06/18/2020
ms.openlocfilehash: acc3fb68c7d03a8e456088401e87d6858e7e9970
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455395"
---
# <a name="quickstart-use-azure-cache-for-redis-in-net-framework"></a>Schnellstart: Verwenden von Azure Cache for Redis mit .NET Framework

In dieser Schnellstartanleitung integrieren Sie Azure Cache für Redis in eine .NET Framework-App, um Zugriff auf einen sicheren, dedizierten Cache zu erhalten, der von jeder Anwendung in Azure aus zugänglich ist. Sie verwenden insbesondere den [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis)-Client mit C#-Code in einer .NET-Konsolen-App.

## <a name="skip-to-the-code-on-github"></a>Direktes Navigieren zum Code auf GitHub

Wenn Sie direkt mit dem Code fortfahren möchten, finden Sie im [.NET Framework-Schnellstart](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/dotnet) auf GitHub weitere Informationen.

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://www.visualstudio.com/downloads/)
- [.NET Framework 4 oder höher](https://dotnet.microsoft.com/download/dotnet-framework), das vom „StackExchange.Redis“-Client benötigt wird.

## <a name="create-a-cache"></a>Erstellen eines Caches
[!INCLUDE [redis-cache-create](includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](includes/redis-cache-access-keys.md)]

Erstellen Sie auf Ihrem Computer eine Datei namens *CacheSecrets.config*, und platzieren Sie die Datei an einem Speicherort, an dem sie nicht mit dem Quellcode Ihrer Beispielanwendung eingecheckt wird. In diesem Schnellstart befindet sich die Datei *CacheSecrets.config* im Verzeichnis *C:\AppSecrets\CacheSecrets.config*.

Bearbeiten Sie die Datei *CacheSecrets.config*, und fügen Sie ihr folgende Inhalte hinzu:

```xml
<appSettings>
    <add key="CacheConnection" value="<host-name>,abortConnect=false,ssl=true,allowAdmin=true,password=<access-key>"/>
</appSettings>
```

Ersetzen Sie `<host-name>` durch den Cachehostnamen.

Ersetzen Sie `<access-key>` durch den Primärschlüssel für Ihren Cache.


## <a name="create-a-console-app"></a>Erstellen einer Konsolen-App

Klicken Sie in Visual Studio auf **Datei** > **Neu** > **Projekt**.

Wählen Sie **Konsolen-App (.NET Framework)** und **Weiter** aus, um Ihre App zu konfigurieren. Geben Sie einen **Projektnamen** ein, vergewissern Sie sich, dass das **.NET Framework 4.6.1** oder höher ausgewählt ist, und klicken Sie dann auf **Erstellen**, um eine neue Konsolenanwendung zu erstellen.

<a name="configure-the-cache-clients"></a>

## <a name="configure-the-cache-client"></a>Konfigurieren des Cacheclients

In diesem Abschnitt konfigurieren Sie die Konsolenanwendung zur Verwendung des [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis)-Clients für .NET.

Wählen Sie in Visual Studio das **Tools** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole** und führen Sie im Fenster der Paket-Manager-Konsole den folgenden Befehl aus.

```powershell
Install-Package StackExchange.Redis
```

Nach Abschluss der Installation kann der *StackExchange.Redis*-Cacheclient für Ihr Projekt verwendet werden.


## <a name="connect-to-the-cache"></a>Herstellen einer Verbindung mit dem Cache

Öffnen Sie in Visual Studio die Datei *App.config*, und aktualisieren Sie sie, damit sie ein `appSettings`-`file`-Attribut enthält, das auf die Datei *CacheSecrets.config* verweist.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.7.2" />
    </startup>

    <appSettings file="C:\AppSecrets\CacheSecrets.config"></appSettings>
</configuration>
```

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Verweise** und wählen Sie anschließend **Einen Verweis hinzufügen** aus. Fügen Sie einen Verweis auf die Assembly **System.Configuration** hinzu.

Fügen Sie der Datei *Program.cs* die folgenden `using`-Anweisungen hinzu:

```csharp
using StackExchange.Redis;
using System.Configuration;
```

Die Verbindung mit Azure Cache for Redis wird durch die Klasse `ConnectionMultiplexer` verwaltet. Diese Klasse muss in Ihrer gesamten Clientanwendung freigegeben und wiederverwendet werden. Erstellen Sie nicht für jeden Vorgang eine neue Verbindung. 

Speichern Sie niemals Anmeldeinformationen im Quellcode. Hier wird nur eine externe Konfigurationsdatei für Geheimnisse verwendet, um dieses Beispiel einfach zu halten. Ein besserer Ansatz wäre die Nutzung von [Azure Key Vault mit Zertifikaten](/rest/api/keyvault/certificate-scenarios).

Fügen Sie in der Datei *Program.cs* der `Program`-Klasse Ihrer Konsolenanwendung die folgenden Member hinzu:

```csharp
private static Lazy<ConnectionMultiplexer> lazyConnection = CreateConnection();

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}

private static Lazy<ConnectionMultiplexer> CreateConnection()
{
    return new Lazy<ConnectionMultiplexer>(() =>
    {
        string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
        return ConnectionMultiplexer.Connect(cacheConnection);
    });
}
```

Bei diesem Ansatz zum Freigeben einer `ConnectionMultiplexer`-Instanz in Ihrer Anwendung wird eine statische Eigenschaft verwendet, die eine verbundene Instanz zurückgibt. Dieser Code ist eine threadsichere Möglichkeit, um nur eine einzelne verbundene `ConnectionMultiplexer`-Instanz zu initialisieren. `abortConnect` ist auf „false“ festgelegt. Das bedeutet, dass der Aufruf erfolgreich ist, auch wenn keine Verbindung mit Azure Cache for Redis hergestellt wird. Eine wichtige Funktion von `ConnectionMultiplexer` ist, dass die Verbindung mit dem Cache automatisch wiederhergestellt wird, sobald das Netzwerkproblem oder andere Ursachen beseitigt wurden.

Der Wert der appSetting-Einstellung *CacheConnection* wird verwendet, um über das Azure-Portal auf die Cacheverbindungszeichenfolge als Kennwortparameter zu verweisen.

## <a name="handle-redisconnectionexception-and-socketexception-by-reconnecting"></a>Behandeln von RedisConnectionException und SocketException durch erneutes Herstellen einer Verbindung

Als bewährte Vorgehensweise beim Aufrufen von Methoden für `ConnectionMultiplexer` wird empfohlen, die automatische Auflösung der Ausnahmen `RedisConnectionException` und `SocketException` zu versuchen, indem Sie die Verbindung trennen und erneut herstellen.

Fügen Sie der Datei *Program.cs* die folgenden `using`-Anweisungen hinzu:

```csharp
using System.Net.Sockets;
using System.Threading;
```

Fügen Sie der *-Klasse in* Program.cs`Program` die folgenden Member hinzu:

```csharp
private static long _lastReconnectTicks = DateTimeOffset.MinValue.UtcTicks;
private static DateTimeOffset _firstErrorTime = DateTimeOffset.MinValue;
private static DateTimeOffset _previousErrorTime = DateTimeOffset.MinValue;
private static SemaphoreSlim _reconnectSemaphore = new SemaphoreSlim(initialCount: 1, maxCount: 1);
private static SemaphoreSlim _initSemaphore = new SemaphoreSlim(initialCount: 1, maxCount: 1);
private static ConnectionMultiplexer _connection;
private static bool _didInitialize = false;
// In general, let StackExchange.Redis handle most reconnects,
// so limit the frequency of how often ForceReconnect() will
// actually reconnect.
public static TimeSpan ReconnectMinInterval => TimeSpan.FromSeconds(60);
// If errors continue for longer than the below threshold, then the
// multiplexer seems to not be reconnecting, so ForceReconnect() will
// re-create the multiplexer.
public static TimeSpan ReconnectErrorThreshold => TimeSpan.FromSeconds(30);
public static TimeSpan RestartConnectionTimeout => TimeSpan.FromSeconds(15);
public static int RetryMaxAttempts => 5;
public static ConnectionMultiplexer Connection { get { return _connection; } }
private static async Task InitializeAsync()
{
    if (_didInitialize)
    {
        throw new InvalidOperationException("Cannot initialize more than once.");
    }
    _connection = await CreateConnectionAsync();
    _didInitialize = true;
}
// This method may return null if it fails to acquire the semaphore in time.
// Use the return value to update the "connection" field
private static async Task<ConnectionMultiplexer> CreateConnectionAsync()
{
    if (_connection != null)
    {
        // If we already have a good connection, let's re-use it
        return _connection;
    }
    try
    {
        await _initSemaphore.WaitAsync(RestartConnectionTimeout);
    }
    catch
    {
        // We failed to enter the semaphore in the given amount of time. Connection will either be null, or have a value that was created by another thread.
        return _connection;
    }
    // We entered the semaphore successfully.
    try
    {
        if (_connection != null)
        {
            // Another thread must have finished creating a new connection while we were waiting to enter the semaphore. Let's use it
            return _connection;
        }
        // Otherwise, we really need to create a new connection.
        string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
        return await ConnectionMultiplexer.ConnectAsync(cacheConnection);
    }
    finally
    {
        _initSemaphore.Release();
    }
}
private static async Task CloseConnectionAsync(ConnectionMultiplexer oldConnection)
{
    if (oldConnection == null)
    {
        return;
    }
    try
    {
        await oldConnection.CloseAsync();
    }
    catch (Exception)
    {
        // Ignore any errors from the oldConnection
    }
}
/// <summary>
/// Force a new ConnectionMultiplexer to be created.
/// NOTES:
///     1. Users of the ConnectionMultiplexer MUST handle ObjectDisposedExceptions, which can now happen as a result of calling ForceReconnectAsync().
///     2. Call ForceReconnectAsync() for RedisConnectionExceptions and RedisSocketExceptions. You can also call it for RedisTimeoutExceptions,
///         but only if you're using generous ReconnectMinInterval and ReconnectErrorThreshold. Otherwise, establishing new connections can cause
///         a cascade failure on a server that's timing out because it's already overloaded.
///     3. The code will:
///         a. wait to reconnect for at least the "ReconnectErrorThreshold" time of repeated errors before actually reconnecting
///         b. not reconnect more frequently than configured in "ReconnectMinInterval"
/// </summary>
public static async Task ForceReconnectAsync()
{
    var utcNow = DateTimeOffset.UtcNow;
    long previousTicks = Interlocked.Read(ref _lastReconnectTicks);
    var previousReconnectTime = new DateTimeOffset(previousTicks, TimeSpan.Zero);
    TimeSpan elapsedSinceLastReconnect = utcNow - previousReconnectTime;
    // If multiple threads call ForceReconnectAsync at the same time, we only want to honor one of them.
    if (elapsedSinceLastReconnect < ReconnectMinInterval)
    {
        return;
    }
    try
    {
        await _reconnectSemaphore.WaitAsync(RestartConnectionTimeout);
    }
    catch
    {
        // If we fail to enter the semaphore, then it is possible that another thread has already done so.
        // ForceReconnectAsync() can be retried while connectivity problems persist.
        return;
    }
    try
    {
        utcNow = DateTimeOffset.UtcNow;
        elapsedSinceLastReconnect = utcNow - previousReconnectTime;
        if (_firstErrorTime == DateTimeOffset.MinValue)
        {
            // We haven't seen an error since last reconnect, so set initial values.
            _firstErrorTime = utcNow;
            _previousErrorTime = utcNow;
            return;
        }
        if (elapsedSinceLastReconnect < ReconnectMinInterval)
        {
            return; // Some other thread made it through the check and the lock, so nothing to do.
        }
        TimeSpan elapsedSinceFirstError = utcNow - _firstErrorTime;
        TimeSpan elapsedSinceMostRecentError = utcNow - _previousErrorTime;
        bool shouldReconnect =
            elapsedSinceFirstError >= ReconnectErrorThreshold // Make sure we gave the multiplexer enough time to reconnect on its own if it could.
            && elapsedSinceMostRecentError <= ReconnectErrorThreshold; // Make sure we aren't working on stale data (e.g. if there was a gap in errors, don't reconnect yet).
        // Update the previousErrorTime timestamp to be now (e.g. this reconnect request).
        _previousErrorTime = utcNow;
        if (!shouldReconnect)
        {
            return;
        }
        _firstErrorTime = DateTimeOffset.MinValue;
        _previousErrorTime = DateTimeOffset.MinValue;
        ConnectionMultiplexer oldConnection = _connection;
        await CloseConnectionAsync(oldConnection);
        _connection = null;
        _connection = await CreateConnectionAsync();
        Interlocked.Exchange(ref _lastReconnectTicks, utcNow.UtcTicks);
    }
    finally
    {
        _reconnectSemaphore.Release();
    }
}
// In real applications, consider using a framework such as
// Polly to make it easier to customize the retry approach.
private static async Task<T> BasicRetryAsync<T>(Func<T> func)
{
    int reconnectRetry = 0;
    int disposedRetry = 0;
    while (true)
    {
        try
        {
            return func();
        }
        catch (Exception ex) when (ex is RedisConnectionException || ex is SocketException)
        {
            reconnectRetry++;
            if (reconnectRetry > RetryMaxAttempts)
                throw;
            await ForceReconnectAsync();
        }
        catch (ObjectDisposedException)
        {
            disposedRetry++;
            if (disposedRetry > RetryMaxAttempts)
                throw;
        }
    }
}
public static Task<IDatabase> GetDatabaseAsync()
{
    return BasicRetryAsync(() => Connection.GetDatabase());
}
public static Task<System.Net.EndPoint[]> GetEndPointsAsync()
{
    return BasicRetryAsync(() => Connection.GetEndPoints());
}
public static Task<IServer> GetServerAsync(string host, int port)
{
    return BasicRetryAsync(() => Connection.GetServer(host, port));
}
```

## <a name="executing-cache-commands"></a>Ausführen von Cachebefehlen

Fügen Sie den folgenden Code für die Prozedur `Main` der `Program`-Klasse Ihrer Konsolenanwendung hinzu:

```csharp
static void Main(string[] args)
{
    IDatabase cache = GetDatabase();

    // Perform cache operations using the cache object...

    // Simple PING command
    string cacheCommand = "PING";
    Console.WriteLine("\nCache command  : " + cacheCommand);
    Console.WriteLine("Cache response : " + cache.Execute(cacheCommand).ToString());

    // Simple get and put of integral data types into the cache
    cacheCommand = "GET Message";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
    Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

    cacheCommand = "SET Message \"Hello! The cache is working from a .NET console app!\"";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringSet()");
    Console.WriteLine("Cache response : " + cache.StringSet("Message", "Hello! The cache is working from a .NET console app!").ToString());

    // Demonstrate "SET Message" executed as expected...
    cacheCommand = "GET Message";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
    Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

    // Get the client list, useful to see if connection list is growing...
    // Note that this requires allowAdmin=true in the connection string
    cacheCommand = "CLIENT LIST";
    Console.WriteLine("\nCache command  : " + cacheCommand);
    var endpoint = (System.Net.DnsEndPoint)GetEndPoints()[0];
    IServer server = GetServer(endpoint.Host, endpoint.Port);
    ClientInfo[] clients = server.ClientList();

    Console.WriteLine("Cache response :");
    foreach (ClientInfo client in clients)
    {
        Console.WriteLine(client.Raw);
    }

    CloseConnection(lazyConnection);
}
```

Für Azure Cache for Redis kann eine konfigurierbare Anzahl von Datenbanken (standardmäßig 16) konfiguriert werden, mit denen die Daten innerhalb einer Azure Cache for Redis-Instanz logisch getrennt werden können. Der Code stellt eine Verbindung mit der Standarddatenbank (DB 0) her. Weitere Informationen finden Sie unter [Was sind Redis-Datenbanken?](cache-development-faq.yml#what-are-redis-databases-) und [Standardmäßige Redis-Serverkonfiguration](cache-configure.md#default-redis-server-configuration).

Cacheelemente können mit den Methoden `StringSet` und `StringGet` gespeichert und abgerufen werden.

Redis speichert die meisten Daten als Redis-Zeichenfolgen. Diese können jedoch unterschiedliche Datentypen enthalten, inklusive serialisierter Binärdaten, die zum Speichern von .NET-Objekten im Cache verwendet werden können.

Drücken Sie **STRG+F5**, um die Konsolen-App zu erstellen und auszuführen.

Im folgenden Beispiel können Sie sehen, dass der `Message`-Schlüssel zuvor einen zwischengespeicherten Wert aufgewiesen hat, der im Azure-Portal über die Redis-Konsole festgelegt wurde. Die App hat diesen zwischengespeicherten Wert aktualisiert. Außerdem hat die App die Befehle `PING` und `CLIENT LIST` ausgeführt.

![Konsolen-App – Partiell](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-console-app-partial.png)


## <a name="work-with-net-objects-in-the-cache"></a>Arbeiten mit .NET-Objekten im Cache

Azure Cache for Redis kann sowohl .NET-Objekte als auch primitive Datentypen zwischenspeichern. .NET-Objekte müssen allerdings zunächst serialisiert werden. Die Serialisierung des .NET-Objekts ist Aufgabe des Anwendungsentwicklers und überlässt dem Entwickler die Freiheit bei der Wahl des Serialisierers.

Eine einfache Möglichkeit zum Serialisieren von Objekten stellt die Verwendung der `JsonConvert`-Serialisierungsmethoden in [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) und die Serialisierung in und aus JSON dar. In diesem Abschnitt fügen Sie dem Cache ein .NET-Objekt hinzu.

Wählen Sie in Visual Studio das **Tools** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole** und führen Sie im Fenster der Paket-Manager-Konsole den folgenden Befehl aus.

```powershell
Install-Package Newtonsoft.Json
```

Fügen Sie am Anfang der Datei *Program.cs* die folgende `using`-Anweisung hinzu:

```csharp
using Newtonsoft.Json;
```

Fügen Sie der Datei *Program.cs* die folgende `Employee`-Klassendefinition hinzu:

```csharp
class Employee
{
    public string Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }

    public Employee(string employeeId, string name, int age)
    {
        Id = employeeId;
        Name = name;
        Age = age;
    }
}
```

Fügen Sie in *Program.cs* am unteren Ende der Prozedur `Main()` und vor dem Aufruf von `CloseConnection()` die folgenden Codezeilen hinzu, um ein serialisiertes .NET-Objekt zwischenzuspeichern und abzurufen:

```csharp
    // Store .NET object to cache
    Employee e007 = new Employee("007", "Davide Columbo", 100);
    Console.WriteLine("Cache response from storing Employee .NET object : " + 
    cache.StringSet("e007", JsonConvert.SerializeObject(e007)));

    // Retrieve .NET object from cache
    Employee e007FromCache = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e007"));
    Console.WriteLine("Deserialized Employee .NET object :\n");
    Console.WriteLine("\tEmployee.Name : " + e007FromCache.Name);
    Console.WriteLine("\tEmployee.Id   : " + e007FromCache.Id);
    Console.WriteLine("\tEmployee.Age  : " + e007FromCache.Age + "\n");
```

Drücken Sie **STRG+F5**, um die Konsolen-App zum Testen der Serialisierung von .NET-Objekten zu erstellen und auszuführen. 

![Konsolen-App abgeschlossen](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-console-app-complete.png)


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie mit dem nächsten Tutorial fortfahren möchten, können Sie die in dieser Schnellstartanleitung erstellten Ressourcen beibehalten und wiederverwenden.

Wenn Sie die Schnellstart-Beispielanwendung nicht mehr benötigen, können Sie die in dieser Schnellstartanleitung erstellten Azure-Ressourcen löschen, um das Anfallen von Kosten zu vermeiden. 

> [!IMPORTANT]
> Das Löschen einer Ressourcengruppe kann nicht rückgängig gemacht werden. Die Ressourcengruppe und alle darin enthaltenen Ressourcen werden also dauerhaft gelöscht. Achten Sie daher darauf, dass Sie nicht versehentlich die falsche Ressourcengruppe oder die falschen Ressourcen löschen. Falls Sie die Ressourcen zum Hosten dieses Beispiels in einer vorhandenen Ressourcengruppe erstellt haben, die beizubehaltende Ressourcen enthält, können Sie die Ressourcen einzeln auf der linken Seite löschen, statt die Ressourcengruppe zu löschen.
>

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und klicken Sie auf **Ressourcengruppen**.

Geben Sie im Textfeld **Nach Name filtern...** den Namen Ihrer Ressourcengruppe ein. In diesem Artikel wurde eine Ressourcengruppe mit dem Namen *TestResources* verwendet. Klicken Sie in der Ergebnisliste in Ihrer Ressourcengruppe auf **...** und dann auf **Ressourcengruppe löschen**.

![Löschen](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-delete-resource-group.png)

Sie werden aufgefordert, das Löschen der Ressourcengruppe zu bestätigen. Geben Sie zur Bestätigung den Namen Ihrer Ressourcengruppe ein, und klicken Sie auf **Löschen**.

Daraufhin werden die Ressourcengruppe und alle darin enthaltenen Ressourcen gelöscht.



<a name="next-steps"></a>

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung wurde beschrieben, wie Sie Azure Cache for Redis über eine .NET-Anwendung verwenden. In der nächsten Schnellstartanleitung erfahren Sie, wie Sie Azure Cache for Redis mit einer ASP.NET-Web-App verwenden.

> [!div class="nextstepaction"]
> [Erstellen einer ASP.NET-Web-App, die eine Azure Cache for Redis-Instanz verwendet](./cache-web-app-howto.md)

Möchten Sie Ihre Cloudausgaben optimieren und dabei sparen?

> [!div class="nextstepaction"]
> [Beginnen mit der Kostenanalyse mit Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
