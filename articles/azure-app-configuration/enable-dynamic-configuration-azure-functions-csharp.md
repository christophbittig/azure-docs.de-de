---
title: 'Tutorial: Verwenden der dynamischen Konfiguration von Azure App Configuration in einer Azure Functions-App | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie die Konfigurationsdaten für Azure Functions-Apps dynamisch aktualisieren.
services: azure-app-configuration
documentationcenter: ''
author: zhenlan
manager: qingye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 11/17/2019
ms.author: zhenlwa
ms.custom: devx-track-csharp, azure-functions
ms.tgt_pltfrm: Azure Functions
ms.openlocfilehash: ffa2b1a4537093f9a612f0e57b7f416ee7ed0012
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132301910"
---
# <a name="tutorial-use-dynamic-configuration-in-an-azure-functions-app"></a>Tutorial: Verwenden der dynamischen Konfiguration in einer Azure Functions-App

Der .NET-Konfigurationsanbieter App Configuration unterstützt das dynamische Zwischenspeichern und Aktualisieren der Konfiguration durch Anwendungsaktivitäten. In diesem Tutorial wird veranschaulicht, wie Sie dynamische Konfigurationsupdates in Ihrem Code implementieren können. Es baut auf der Azure Functions-App auf, die in den Schnellstarts vorgestellt wurde. Absolvieren Sie zunächst [Erstellen einer Azure Functions-App mit Azure App Configuration](./quickstart-azure-functions-csharp.md), bevor Sie fortfahren.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Einrichten Ihrer Azure Functions-App für die Aktualisierung der Konfiguration als Reaktion auf Änderungen in einem App Configuration-Speicher
> * Einfügen der neuesten Konfiguration in Ihre Azure Functions-Aufrufe

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) mit der Workload **Azure-Entwicklung**
- [Azure Functions-Tools](../azure-functions/functions-develop-vs.md#check-your-tools-version)
- Abgeschlossener Schnellstart: [Erstellen einer Azure Functions-App mit Azure App Configuration](./quickstart-azure-functions-csharp.md)

## <a name="reload-data-from-app-configuration"></a>Erneutes Laden von Daten aus App Configuration

1. Öffnen Sie *Startup.cs*, und aktualisieren Sie die Methode `ConfigureAppConfiguration`. 

   Mit der Methode `ConfigureRefresh` registrieren Sie eine Einstellung, die immer dann auf Änderungen überprüft wird, wenn eine Aktualisierung innerhalb der Anwendung ausgelöst wird. Dazu fügen Sie später `_configurationRefresher.TryRefreshAsync()` hinzu. Der Parameter `refreshAll` weist den App Configuration-Anbieter an, die gesamte Konfiguration neu zu laden, wenn eine Änderung in der registrierten Einstellung erkannt wird.

    Alle Einstellungen, die für die Aktualisierung registriert sind, haben einen standardmäßigen Cacheablauf von 30 Sekunden. Dieser kann durch Aufrufen der Methode `AzureAppConfigurationRefreshOptions.SetCacheExpiration` aktualisiert werden.

    ```csharp
    public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
    {
        builder.ConfigurationBuilder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   // Load all keys that start with `TestApp:`
                   .Select("TestApp:*")
                   // Configure to reload configuration if the registered sentinel key is modified
                   .ConfigureRefresh(refreshOptions =>
                      refreshOptions.Register("TestApp:Settings:Sentinel", refreshAll: true));
        });
    }
    ```

   > [!TIP]
   > Wenn Sie mehrere Schlüsselwerte in App Configuration aktualisieren, möchten Sie in der Regel nicht, dass Ihre Anwendung die Konfiguration neu lädt, bevor alle Änderungen vorgenommen wurden. Sie können einen *Sentinel-Schlüssel* registrieren und nur eine Aktualisierung durchführen, wenn alle anderen Konfigurationsänderungen abgeschlossen sind. Dadurch wird die Konsistenz der Konfiguration in Ihrer Anwendung sichergestellt.

2. Aktualisieren Sie die `Configure`-Methode, um App Configuration-Dienste per Abhängigkeitsinjektion verfügbar zu machen.

    ```csharp
    public override void Configure(IFunctionsHostBuilder builder)
    {
        builder.Services.AddAzureAppConfiguration();
    }
    ```

3. Öffnen Sie *Function1.cs*, und fügen Sie die folgenden Namespaces hinzu:

    ```csharp
    using System.Linq;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

   Aktualisieren Sie den Konstruktor, um per Abhängigkeitsinjektion die Instanz von `IConfigurationRefresherProvider` abzurufen, über die Sie die Instanz von `IConfigurationRefresher` abrufen können.

    ```csharp
    private readonly IConfiguration _configuration;
    private readonly IConfigurationRefresher _configurationRefresher;

    public Function1(IConfiguration configuration, IConfigurationRefresherProvider refresherProvider)
    {
        _configuration = configuration;
        _configurationRefresher = refresherProvider.Refreshers.First();
    }
    ```

4. Aktualisieren Sie die `Run`-Methode, und signalisieren Sie die Aktualisierung der Konfiguration mithilfe der `TryRefreshAsync`-Methode am Anfang des Funktionsaufrufs. Dies ist ein No-Op-Zustand, wenn das Zeitfenster für den Cacheablauf nicht erreicht wird. Entfernen Sie den Operator `await`, wenn Sie es vorziehen, dass die Konfiguration aktualisiert wird, ohne den aktuellen Functions-Aufruf zu blockieren. In diesem Fall erhalten spätere Functions-Aufrufe den aktualisierten Wert.

    ```csharp
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await _configurationRefresher.TryRefreshAsync(); 

        string keyName = "TestApp:Settings:Message";
        string message = _configuration[keyName];
            
        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>Lokales Testen der Funktion

1. Legen Sie eine Umgebungsvariable mit dem Namen **ConnectionString** fest, und geben Sie dafür den Zugriffsschlüssel für Ihren App-Konfigurationsspeicher an. Führen Sie bei Verwendung einer Windows-Eingabeaufforderung den folgenden Befehl aus, und starten Sie die Eingabeaufforderung neu, damit die Änderung wirksam wird:

    ```console
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Führen Sie bei Verwendung von Windows PowerShell den folgenden Befehl aus:

    ```powershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Führen Sie bei Verwendung von macOS oder Linux den folgenden Befehl aus:

    ```console
    export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. Drücken Sie F5, um Ihre Funktion zu testen. Akzeptieren Sie die entsprechende Aufforderung von Visual Studio zum Herunterladen und Installieren der **Azure Functions Core (CLI)** -Tools. Sie müssen möglicherweise auch eine Firewallausnahme aktivieren, damit die Tools HTTP-Anforderungen verarbeiten können.

3. Kopieren Sie die URL Ihrer Funktion aus der Azure Functions-Laufzeitausgabe.

    ![Schnellstart: Debuggen von Funktionen in VS](./media/quickstarts/function-visual-studio-debugging.png)

4. Fügen Sie die URL zu der HTTP-Anforderung in die Adressleiste Ihres Browsers ein. In der folgenden Abbildung sehen Sie die Antwort des Browsers auf die von der Funktion zurückgegebene lokale GET-Anforderung.

    ![Schnellstart: Lokales Starten von Funktionen](./media/quickstarts/dotnet-core-function-launch-local.png)

5. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Alle Ressourcen** und dann den App Configuration-Speicher aus, den Sie in der Schnellstartanleitung erstellt haben.

6. Wählen Sie den **Konfigurations-Explorer** aus, und aktualisieren Sie den Wert der folgenden Schlüssel:

    | Schlüssel | Wert |
    |---|---|
    | TestApp:Settings:Message | Daten aus Azure App Configuration: Aktualisiert |

   Erstellen Sie anschließend den Sentinel-Schlüssel, oder ändern Sie seinen Wert, wenn er bereits vorhanden ist. Beispiel:

    | Schlüssel | Wert |
    |---|---|
    | TestApp:Settings:Sentinel | v1 |


7. Aktualisieren Sie den Browser mehrmals. Wenn die zwischengespeicherte Einstellung nach 30 Sekunden abläuft, zeigt die Seite die Antwort des Funktionsaufrufs mit aktualisiertem Wert an.

    ![Schnellstart: Lokales Aktualisieren von Funktionen](./media/quickstarts/dotnet-core-function-refresh-local.png)

> [!NOTE]
> Der in diesem Tutorial verwendete Beispielcode kann aus dem [GitHub-Repository für App Configuration](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction) heruntergeladen werden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Ihre Azure Functions-App aktiviert, um Konfigurationseinstellungen dynamisch aus App Configuration zu aktualisieren. Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie eine von Azure verwaltete Identität hinzufügen, um den Zugriff auf App Configuration zu optimieren.

> [!div class="nextstepaction"]
> [Integration der verwalteten Identität](./howto-integrate-azure-managed-service-identity.md)
