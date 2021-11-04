---
title: Erstellen einer ASP.NET-Web-App mit Azure Cache for Redis
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie eine ASP.NET-Web-App mit Azure Cache for Redis erstellen.
author: curib
ms.service: cache
ms.topic: quickstart
ms.date: 09/29/2020
ms.author: cauribeg
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 5512407bc68223d6c8f84e3f18af3b24a65a31f9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440323"
---
# <a name="quickstart-use-azure-cache-for-redis-with-an-aspnet-web-app"></a>Schnellstart: Verwenden von Azure Cache for Redis mit einer ASP.NET-Web-App

In dieser Schnellstartanleitung erstellen Sie mit Visual Studio 2019 eine ASP.NET-Webanwendung, die sich mit Azure Cache for Redis verbindet, um Daten aus dem Cache zu speichern und abzurufen. Sie stellen die App dann für Azure App Service bereit.

## <a name="skip-to-the-code-on-github"></a>Überspringen und mit dem Code auf GitHub fortfahren

Wenn Sie direkt mit dem Code fortfahren möchten, finden Sie im [ASP.NET-Schnellstart](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/aspnet) auf GitHub weitere Informationen.

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/dotnet)
- [Visual Studio 2019](https://www.visualstudio.com/downloads/) mit den Workloads **ASP.NET und Webentwicklung** und **Azure-Entwicklung**.

## <a name="create-the-visual-studio-project"></a>Erstellen des Visual Studio-Projekts

1. Öffnen Sie Visual Studio, und wählen Sie anschließend **Datei** > **Neu** > **Projekt** aus.

2. Führen Sie im Dialogfeld **Neues Projekt erstellen** die folgenden Schritte aus:

    ![Projekt erstellen](./media/cache-web-app-howto/cache-create-project.png)

    a. Geben Sie im Suchfeld _C#-ASP.NET-Webanwendung_ ein.

    b. Wählen Sie **ASP.NET-Webanwendung (.NET Framework)** aus.

    c. Wählen Sie **Weiter** aus.

3. Geben Sie im Feld **Projektname** einen Namen für das Projekt ein. In diesem Beispiel haben wir **ContosoTeamStats** verwendet.

4. Stellen Sie sicher, dass **.NET Framework 4.6.1** oder höher ausgewählt ist.

5. Klicken Sie auf **Erstellen**.

6. Wählen Sie als Projekttyp die Option **MVC** aus.

7. Stellen Sie sicher, dass für die Einstellungen unter **Authentifizierung** die Option **Keine Authentifizierung** angegeben ist. Je nach Ihrer Version von Visual Studio kann die Standardeinstellung für **Authentifizierung** auch anders lauten. Um die Einstellung zu ändern, wählen Sie zunächst **Authentifizierung ändern** und anschließend **Keine Authentifizierung** aus.

8. Wählen Sie **Create** (Erstellen), um das Projekt zu erstellen.

## <a name="create-a-cache"></a>Erstellen eines Caches

Als Nächstes erstellen Sie den Cache für die App.

[!INCLUDE [redis-cache-create](includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](includes/redis-cache-access-keys.md)]

### <a name="to-edit-the-cachesecretsconfig-file"></a>So bearbeiten Sie die Datei *CacheSecrets.config*

1. Erstellen Sie auf Ihrem Computer eine Datei mit dem Namen *CacheSecrets.config*. Speichern Sie sie an einem Ort, an dem sie nicht mit dem Quellcode Ihrer Beispielanwendung eingecheckt wird. In diesem Schnellstart befindet sich die Datei *CacheSecrets.config* im Verzeichnis *C:\AppSecrets\CacheSecrets.config*.

1. Bearbeiten Sie die Datei *CacheSecrets.config*. Fügen Sie anschließend folgenden Inhalt hinzu:

    ```xml
    <appSettings>
        <add key="CacheConnection" value="<cache-name>.redis.cache.windows.net,abortConnect=false,ssl=true,allowAdmin=true,password=<access-key>"/>
    </appSettings>
    ```

1. Ersetzen Sie `<cache-name>` durch den Cachehostnamen.

1. Ersetzen Sie `<access-key>` durch den Primärschlüssel für Ihren Cache.

    > [!TIP]
    > Sie können den sekundären Zugriffsschlüssel bei der Schlüsselrotation als alternativen Schlüssel verwenden, während Sie den primären Zugriffsschlüssel neu generieren.
   >
1. Speichern Sie die Datei .

## <a name="update-the-mvc-application"></a>Aktualisieren der MVC-Anwendung

In diesem Abschnitt aktualisieren Sie die Anwendung, um eine neue Ansicht zu unterstützen, in der ein einfacher Test für Azure Cache for Redis angezeigt wird.

- [Aktualisieren der Datei „Web.config“ mit einer App-Einstellung für den Cache](#update-the-webconfig-file-with-an-app-setting-for-the-cache)
- Konfigurieren der Anwendung für die Verwendung des Clients „StackExchange.Redis“
- Aktualisieren von HomeController und Layout
- Hinzufügen einer neuen RedisCache-Ansicht

### <a name="update-the-webconfig-file-with-an-app-setting-for-the-cache"></a>Aktualisieren der Datei „Web.config“ mit einer App-Einstellung für den Cache

Wenn Sie die Anwendung lokal ausführen, werden die Informationen in der Datei *CacheSecrets.config* verwendet, um eine Verbindung mit Ihrer Azure Cache for Redis-Instanz herzustellen. Später stellen Sie diese Anwendung für Azure bereit. Zu diesem Zeitpunkt konfigurieren Sie eine App-Einstellung in Azure, die von der Anwendung verwendet wird, um die Cacheverbindungsinformationen anstelle dieser Datei abzurufen.

Weil die Datei *CacheSecrets.config* nicht mit Ihrer Anwendung in Azure bereitgestellt wird, verwenden Sie die Datei nur, wenn Sie die Anwendung lokal testen. Speichern Sie diese Informationen so sicher wie möglich, um missbräuchlichen Zugriff auf Ihre Cachedaten zu verhindern.

#### <a name="to-update-the-webconfig-file"></a>So aktualisieren Sie die Datei *web.config*

1. Doppelklicken Sie im **Projektmappen-Explorer** auf die Datei *Web.config*, um sie zu öffnen.

    ![Web.config](./media/cache-web-app-howto/cache-web-config.png)

2. Suchen Sie in der Datei *web.config* nach dem Element `<appSetting>`. Fügen Sie anschließend das folgende `file`-Attribut hinzu. Falls Sie einen anderen Dateinamen oder -speicherort verwendet haben, müssen die Werte aus dem Beispiel durch diese Werte ersetzt werden.

- Vorher: `<appSettings>`
- Nachher: `<appSettings file="C:\AppSecrets\CacheSecrets.config">`

Die ASP.NET-Laufzeit führt die Inhalte der externen Datei mit dem Markup im `<appSettings>`-Element zusammen. Falls die angegebene Datei nicht gefunden wird, wird das Dateiattribut ignoriert. Ihre vertraulichen Daten (die Verbindungszeichenfolge für Ihren Cache) sind nicht Bestandteil des Quellcodes für die Anwendung. Die Datei *CacheSecrets.config* wird bei der Bereitstellung der Web-App in Azure nicht bereitgestellt.

### <a name="to-configure-the-application-to-use-stackexchangeredis"></a>So konfigurieren Sie die Anwendung für die Verwendung von „StackExchange.Redis“

1. Um die App für die Verwendung des [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis)-NuGet-Pakets für Visual Studio zu konfigurieren, wählen Sie **Extras > NuGet-Paket-Manager > Paket-Manager-Konsole** aus.

2. Führen Sie im Fenster `Package Manager Console` den folgenden Befehl aus:

    ```powershell
    Install-Package StackExchange.Redis
    ```

3. Das NuGet-Paket wird heruntergeladen und fügt die benötigten Assemblyverweise zu Ihrer Clientanwendung hinzu, um mithilfe des StackExchange.Azure Cache for Redis-Clients auf Azure Cache for Redis zuzugreifen. Installieren Sie das Paket `StackExchange.Redis`, wenn Sie es vorziehen, eine Version der `StackExchange.Redis`-Clientbibliothek mit sicheren Namen zu verwenden.

### <a name="to-update-the-homecontroller-and-layout"></a>So aktualisieren Sie HomeController und Layout

1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Controller**, und öffnen Sie anschließend die Datei *HomeController.cs*.

2. Fügen Sie am Anfang der Datei die folgenden `using`-Anweisungen hinzu:

    ```csharp
    using StackExchange.Redis;
    using System.Configuration;
    using System.Net.Sockets;
    using System.Text;
    using System.Threading;
    ```

3. Fügen Sie der `HomeController`-Klasse die folgenden Member hinzu, um eine neue `RedisCache`-Aktion zu unterstützen, die einige Befehle für den neuen Cache ausführt:

    ```csharp
         public async Task<ActionResult> RedisCache()
        {
            ViewBag.Message = "A simple example with Azure Cache for Redis on ASP.NET.";

            if (Connection == null)
            {
                await InitializeAsync();
            }

            IDatabase cache = await GetDatabaseAsync();

            // Perform cache operations using the cache object...

            // Simple PING command
            ViewBag.command1 = "PING";
            ViewBag.command1Result = cache.Execute(ViewBag.command1).ToString();

            // Simple get and put of integral data types into the cache
            ViewBag.command2 = "GET Message";
            ViewBag.command2Result = cache.StringGet("Message").ToString();

            ViewBag.command3 = "SET Message \"Hello! The cache is working from ASP.NET!\"";
            ViewBag.command3Result = cache.StringSet("Message", "Hello! The cache is working from ASP.NET!").ToString();

            // Demonstrate "SET Message" executed as expected...
            ViewBag.command4 = "GET Message";
            ViewBag.command4Result = cache.StringGet("Message").ToString();

            // Get the client list, useful to see if connection list is growing...
            // Note that this requires allowAdmin=true in the connection string
            ViewBag.command5 = "CLIENT LIST";
            StringBuilder sb = new StringBuilder();
            var endpoint = (System.Net.DnsEndPoint)(await GetEndPointsAsync())[0];
            IServer server = await GetServerAsync(endpoint.Host, endpoint.Port);
            ClientInfo[] clients = await server.ClientListAsync();

            sb.AppendLine("Cache response :");
            foreach (ClientInfo client in clients)
            {
                sb.AppendLine(client.Raw);
            }

            ViewBag.command5Result = sb.ToString();

            return View();
        }

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

        public static async Task InitializeAsync()
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

4. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Ansichten** > **Freigegeben**. Öffnen Sie anschließend die Datei *_Layout.cshtml*.

    Ersetzen Sie:

    ```csharp
    @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
    ```

    Durch:

    ```csharp
    @Html.ActionLink("Azure Cache for Redis Test", "RedisCache", "Home", new { area = "" }, new { @class = "navbar-brand" })
    ```

### <a name="to-add-a-new-rediscache-view"></a>So fügen Sie eine neue RedisCache-Ansicht hinzu

1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Ansichten**, und klicken Sie dann mit der rechten Maustaste auf den Ordner **Home**. Wählen Sie **Hinzufügen** > **Ansicht...** aus.

2. Geben Sie im Dialogfeld **Ansicht hinzufügen** den Ansichtsnamen **RedisCache** ein. Wählen Sie anschließend **Hinzufügen**.

3. Ersetzen Sie den Code in der Datei *RedisCache.cshtml* durch den folgenden Code:

    ```csharp
    @{
        ViewBag.Title = "Azure Cache for Redis Test";
    }

    <h2>@ViewBag.Title.</h2>
    <h3>@ViewBag.Message</h3>
    <br /><br />
    <table border="1" cellpadding="10">
        <tr>
            <th>Command</th>
            <th>Result</th>
        </tr>
        <tr>
            <td>@ViewBag.command1</td>
            <td><pre>@ViewBag.command1Result</pre></td>
        </tr>
        <tr>
            <td>@ViewBag.command2</td>
            <td><pre>@ViewBag.command2Result</pre></td>
        </tr>
        <tr>
            <td>@ViewBag.command3</td>
            <td><pre>@ViewBag.command3Result</pre></td>
        </tr>
        <tr>
            <td>@ViewBag.command4</td>
            <td><pre>@ViewBag.command4Result</pre></td>
        </tr>
        <tr>
            <td>@ViewBag.command5</td>
            <td><pre>@ViewBag.command5Result</pre></td>
        </tr>
    </table>
    ```

## <a name="run-the-app-locally"></a>Lokales Ausführen der App

Standardmäßig ist das Projekt für das lokale Hosten der App in [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) zum Testen und Debuggen konfiguriert.

### <a name="to-run-the-app-locally"></a>So führen Sie die App lokal aus

1. Wählen Sie in Visual Studio **Debuggen** > **Debugging starten** aus, um die App zum Testen und Debuggen lokal zu erstellen und zu starten.

2. Wählen Sie im Browser auf der Navigationsleiste **Azure Cache for Redis Test** (Azure Cache for Redis-Test) aus.

3. Im folgenden Beispiel hat der `Message`-Schlüssel zuvor einen zwischengespeicherten Wert aufgewiesen, der im Portal über die Azure Cache for Redis-Konsole festgelegt wurde. Die App hat diesen zwischengespeicherten Wert aktualisiert. Außerdem hat die App die Befehle `PING` und `CLIENT LIST` ausgeführt.

    ![Einfacher abgeschlossener lokaler Test](./media/cache-web-app-howto/cache-simple-test-complete-local.png)

## <a name="publish-and-run-in-azure"></a>Veröffentlichen und Ausführen in Azure

Nachdem Sie die App erfolgreich lokal getestet haben, stellen Sie die App für Azure bereit und führen sie in der Cloud aus.

### <a name="to-publish-the-app-to-azure"></a>So veröffentlichen Sie die App in Azure

1. Klicken Sie in Visual Studio im Projektmappen-Explorer mit der rechten Maustaste auf den Projektknoten. Wählen Sie anschließend **Veröffentlichen** aus.

    ![Veröffentlichen](./media/cache-web-app-howto/cache-publish-app.png)

2. Wählen Sie **Microsoft Azure App Service**, **Neu erstellen** und anschließend **Veröffentlichen** aus.

    ![Veröffentlichen in App Service](./media/cache-web-app-howto/cache-publish-to-app-service.png)

3. Nehmen Sie im Dialogfeld **App Service erstellen** folgende Änderungen vor:

    | Einstellung | Empfohlener Wert | BESCHREIBUNG |
    | ------- | :---------------: | ----------- |
    | **App-Name** | Verwenden Sie den Standardwert. | Bei der Bereitstellung der App für Azure wird der App-Name als Hostname für die App verwendet. Dem Namen kann bei Bedarf ein Zeitstempelsuffix hinzugefügt werden, um ihn eindeutig zu machen. |
    | **Abonnement** | Wählen Sie Ihr Azure-Abonnement aus. | Für dieses Abonnement werden alle damit verbundenen Hostingkosten berechnet. Wenn Sie über mehrere Azure-Abonnements verfügen, stellen Sie sicher, dass das gewünschte Abonnement ausgewählt ist.|
    | **Ressourcengruppe** | Verwenden Sie die Ressourcengruppe, in der Sie den Cache erstellt haben (z.B. *TestResourceGroup*). | Die Ressourcengruppe hilft Ihnen, alle Ressourcen als Gruppe zu verwalten. Wenn Sie die App später löschen möchten, können Sie die Gruppe einfach löschen. |
    | **App Service-Plan** | Wählen Sie **Neu** aus, und erstellen Sie anschließend einen neuen App Service-Plan mit dem Namen *TestingPlan*. <br />Verwenden Sie den gleichen **Speicherort**, den Sie beim Erstellen Ihres Caches verwendet haben. <br />Wählen Sie **Free** für die Größe aus. | Mit einem App Service-Plan wird ein Satz von Computeressourcen für die Ausführung einer Web-App definiert. |

    ![Dialogfeld „App Service“](./media/cache-web-app-howto/cache-create-app-service-dialog.png)

4. Nachdem Sie die App Service-Hostingeinstellungen konfiguriert haben, wählen Sie **Erstellen** aus.

5. Überwachen Sie in Visual Studio das Fenster **Ausgabe**, um den Veröffentlichungsstatus anzuzeigen. Nach der Veröffentlichung der App wird die URL für die App protokolliert:

    ![Veröffentlichungsausgabe](./media/cache-web-app-howto/cache-publishing-output.png)

### <a name="add-the-app-setting-for-the-cache"></a>Hinzufügen der App-Einstellung für den Cache

Fügen Sie nach dem Veröffentlichen der neuen App eine neue App-Einstellung hinzu. Diese Einstellung wird zum Speichern der Cacheverbindungsinformationen verwendet.

#### <a name="to-add-the-app-setting"></a>So fügen Sie die App-Einstellung hinzu

1. Geben Sie oben im Azure-Portal in der Suchleiste den App-Namen ein, um nach der neuen App zu suchen, die Sie erstellt haben.

    ![Nach App suchen](./media/cache-web-app-howto/cache-find-app-service.png)

2. Fügen Sie eine neue App-Einstellung mit dem Namen **CacheConnection** hinzu, die von der App zum Herstellen einer Verbindung mit dem Cache verwendet wird. Verwenden Sie den gleichen Wert, den Sie für `CacheConnection` in der Datei *CacheSecrets.config* konfiguriert haben. Der Wert enthält den Cachehostnamen und den Zugriffsschlüssel.

    ![App-Einstellung hinzufügen](./media/cache-web-app-howto/cache-add-app-setting.png)

### <a name="run-the-app-in-azure"></a>Ausführen der App in Azure

Navigieren Sie in Ihrem Browser zur URL für die App. Die URL wird in Visual Studio im Fenster „Ausgabe“ in den Ergebnissen des Veröffentlichungsvorgangs angezeigt. Sie wird auch im Azure-Portal auf der Seite „Übersicht“ der von Ihnen erstellten App angegeben.

Wählen Sie auf der Navigationsleiste **Azure Cache for Redis Test** (Azure Cache for Redis-Test) aus, um den Cachezugriff zu testen.

![Einfacher abgeschlossener Azure-Test](./media/cache-web-app-howto/cache-simple-test-complete-azure.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie mit dem nächsten Tutorial fortfahren möchten, können Sie die in dieser Schnellstartanleitung erstellten Ressourcen beibehalten und wiederverwenden.

Wenn Sie die Schnellstart-Beispielanwendung nicht mehr benötigen, können Sie die in dieser Schnellstartanleitung erstellten Azure-Ressourcen löschen, um das Anfallen von Kosten zu vermeiden.

> [!IMPORTANT]
> Das Löschen einer Ressourcengruppe kann nicht rückgängig gemacht werden. Beim Löschen einer Ressourcengruppe werden alle darin enthaltenen Ressourcen unwiderruflich gelöscht. Achten Sie daher darauf, dass Sie nicht versehentlich die falsche Ressourcengruppe oder die falschen Ressourcen löschen. Falls Sie die Ressourcen zum Hosten dieses Beispiels in einer vorhandenen Ressourcengruppe erstellt haben, die beizubehaltende Ressourcen enthält, können Sie die Ressourcen einzeln auf der linken Seite löschen, statt die Ressourcengruppe zu löschen.

### <a name="to-delete-a-resource-group"></a>So löschen Sie eine Ressourcengruppe

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und wählen Sie anschließend **Ressourcengruppen** aus.

2. Geben Sie im Feld **Nach Name filtern...** den Namen Ihrer Ressourcengruppe ein. In diesem Artikel wurde eine Ressourcengruppe mit dem Namen *TestResources* verwendet. Wählen Sie in Ihrer Ressourcengruppe in der Ergebnisliste **...** und anschließend **Ressourcengruppe löschen** aus.

    ![Löschen](./media/cache-web-app-howto/cache-delete-resource-group.png)

Sie werden aufgefordert, das Löschen der Ressourcengruppe zu bestätigen. Geben Sie den Namen Ihrer Ressourcengruppe ein, und wählen Sie **Löschen** aus.

Daraufhin werden die Ressourcengruppe und alle darin enthaltenen Ressourcen gelöscht.

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Tutorial verwenden Sie Azure Cache for Redis in einem realitätsnäheren Szenario zur Verbesserung der Leistung einer App. Sie werden diese Anwendung zum Zwischenspeichern von Leaderboard-Ergebnissen anhand des cachefremden Musters mit ASP.NET und einer Datenbank aktualisieren.

> [!div class="nextstepaction"]
> [Erstellen eines cachefremden Leaderboards in ASP.NET](cache-web-app-cache-aside-leaderboard.md)
