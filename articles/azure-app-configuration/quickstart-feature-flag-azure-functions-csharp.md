---
title: Schnellstartanleitung zum Hinzufügen von Featureflags zu Azure Functions | Microsoft-Dokumentation
description: In dieser Schnellstartanleitung verwenden Sie Azure Functions mit Featureflags aus Azure App Configuration und testen die Funktion lokal.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 8/26/2020
ms.author: alkemper
ms.openlocfilehash: 22de8b7775d6825d99afe8eb4f0dc043f8ec639c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131031554"
---
# <a name="quickstart-add-feature-flags-to-an-azure-functions-app"></a>Schnellstart: Hinzufügen von Featureflags zu einer Azure Functions-App

In dieser Schnellstartanleitung erstellen Sie eine Azure Functions-App und verwenden darin Featureflags. Sie nutzen die Featureverwaltung von Azure App Configuration zum zentralen Speichern Ihrer gesamten Featureflags und zum Steuern der zugehörigen Zustände.

Die .NET-Bibliotheken für die Featureverwaltung erweitern das Framework um Unterstützung für Featureflags. Diese Bibliotheken bauen auf dem .NET-Konfigurationssystem auf. Über ihren .NET-Konfigurationsanbieter lassen sie sich in App Configuration integrieren.

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) mit der Workload **Azure-Entwicklung**.
- [Azure Functions-Tools](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>Erstellen eines App Configuration-Speichers

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Wählen Sie **Feature-Manager** >  **+Hinzufügen** aus, um ein Featureflag namens `Beta` hinzuzufügen.

    > [!div class="mx-imgBorder"]
    > ![Aktivieren eines Featureflags mit dem Namen „Beta“](media/add-beta-feature-flag.png)

    Definieren Sie `label` und `Description` vorerst nicht.

8. Wählen Sie **Übernehmen** aus, um das neue Featureflag zu speichern.

## <a name="create-a-functions-app"></a>Erstellen einer Functions-App

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Herstellen einer Verbindung mit einem App Configuration-Speicher

Bei diesem Projekt wird die [Abhängigkeitsinjektion in Azure Functions (.NET)](../azure-functions/functions-dotnet-dependency-injection.md) genutzt. Hierbei wird Azure App Configuration als zusätzliche Konfigurationsquelle hinzugefügt, in der Ihre Featureflags gespeichert werden.

1. Klicken Sie mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **NuGet-Pakete verwalten** aus. Suchen Sie auf der Registerkarte **Durchsuchen** nach den folgenden NuGet-Paketen, und fügen Sie sie Ihrem Projekt hinzu.
   - [Microsoft.Extensions.Configuration.AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration/): Version 4.1.0 oder höher
   - [Microsoft.FeatureManagement](https://www.nuget.org/packages/Microsoft.FeatureManagement/): Version 2.2.0 oder höher
   - [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/): Version 1.1.0 oder höher 

2. Fügen Sie die neue Datei *Startup.cs* mit dem folgenden Code hinzu. Hierbei wird eine Klasse mit dem Namen `Startup` definiert, mit der die abstrakte Klasse `FunctionsStartup` implementiert wird. Es wird ein assembly-Attribut verwendet, um den Typnamen anzugeben, der beim Starten von Azure Functions genutzt wird.

    ```csharp
    using System;
    using Microsoft.Azure.Functions.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;
    using Microsoft.FeatureManagement;

    [assembly: FunctionsStartup(typeof(FunctionApp.Startup))]

    namespace FunctionApp
    {
        class Startup : FunctionsStartup
        {
            public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
            {
            }

            public override void Configure(IFunctionsHostBuilder builder)
            {
            }
        }
    }
    ```


3. Aktualisieren Sie die `ConfigureAppConfiguration`-Methode, und fügen Sie den Azure App Configuration-Anbieter als zusätzliche Konfigurationsquelle hinzu, indem Sie `AddAzureAppConfiguration()` aufrufen. 

    Mit der `UseFeatureFlags()`-Methode wird der Anbieter aufgefordert, Featureflags zu laden. Alle Featureflags verfügen über eine Standardablaufdauer von 30 Sekunden für den Cache, bevor erneut eine Überprüfung auf Änderungen durchgeführt wird. Das Ablaufintervall kann aktualisiert werden, indem die `FeatureFlagsOptions.CacheExpirationInterval`-Eigenschaft festgelegt wird, die an die `UseFeatureFlags`-Methode übergeben wird. 

    ```csharp
    public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
    {
        builder.ConfigurationBuilder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   .Select("_")
                   .UseFeatureFlags();
        });
    }
    ```

    > [!TIP]
    > Wenn Sie nicht möchten, dass eine andere Konfiguration als Featureflags in Ihre Anwendung geladen wird, können Sie `Select("_")` nur einen nicht vorhandenen Dummyschlüssel laden `"_"` aufrufen. Standardmäßig werden alle Konfigurationsschlüsselwerte aus Ihrem App Configuration-Speicher geladen, wenn keine `Select`-Methode aufgerufen wird.

4. Aktualisieren Sie die `Configure`-Methode, um Azure App Configuration-Dienste und den Feature-Manager per Abhängigkeitsinjektion verfügbar zu machen.

    ```csharp
    public override void Configure(IFunctionsHostBuilder builder)
    {
        builder.Services.AddAzureAppConfiguration();
        builder.Services.AddFeatureManagement();
    }
    ```

5. Öffnen Sie *Function1.cs*, und fügen Sie die folgenden Namespaces hinzu.

    ```csharp
    using System.Linq;
    using Microsoft.FeatureManagement;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

   Fügen Sie einen Konstruktor hinzu, der verwendet wird, um Instanzen von `_featureManagerSnapshot` und `IConfigurationRefresherProvider` per Abhängigkeitsinjektion abzurufen. Über `IConfigurationRefresherProvider` können Sie die Instanz von `IConfigurationRefresher` abrufen.

    ```csharp
    private readonly IFeatureManagerSnapshot _featureManagerSnapshot;
    private readonly IConfigurationRefresher _configurationRefresher;

    public Function1(IFeatureManagerSnapshot featureManagerSnapshot, IConfigurationRefresherProvider refresherProvider)
    {
        _featureManagerSnapshot = featureManagerSnapshot;
        _configurationRefresher = refresherProvider.Refreshers.First();
    }
    ```

6. Aktualisieren Sie die `Run`-Methode, um den Wert der angezeigten Meldung abhängig vom Status des Featureflags zu ändern.

   Die `TryRefreshAsync`-Methode wird am Anfang des Functions-Aufrufs aufgerufen, um Featureflags zu aktualisieren. Dies ist ein No-Op-Zustand, wenn das Zeitfenster für den Cacheablauf nicht erreicht wird. Entfernen Sie den Operator `await`, wenn Sie es vorziehen, dass die Featureflags aktualisiert werden, ohne den aktuellen Functions-Aufruf zu blockieren. In diesem Fall erhalten spätere Functions-Aufrufe den aktualisierten Wert.

    ```csharp
    [FunctionName("Function1")]
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
        ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await _configurationRefresher.TryRefreshAsync();

        string message = await _featureManagerSnapshot.IsEnabledAsync("Beta")
                ? "The Feature Flag 'Beta' is turned ON"
                : "The Feature Flag 'Beta' is turned OFF";

        return (ActionResult)new OkObjectResult(message);
    }
    ```

## <a name="test-the-function-locally"></a>Lokales Testen der Funktion

1. Legen Sie eine Umgebungsvariable mit dem Namen **ConnectionString** fest. Der Wert ist hierbei die Verbindungszeichenfolge, die Sie zuvor in Ihrem App Configuration-Speicher unter **Zugriffsschlüssel** abgerufen haben. Führen Sie bei Verwendung einer Windows-Eingabeaufforderung den folgenden Befehl aus, und starten Sie die Eingabeaufforderung neu, damit die Änderung wirksam wird:

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Führen Sie bei Verwendung von Windows PowerShell den folgenden Befehl aus:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Führen Sie bei Verwendung von macOS oder Linux den folgenden Befehl aus:

    ```bash
        export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

1. Drücken Sie F5, um Ihre Funktion zu testen. Akzeptieren Sie die entsprechende Aufforderung von Visual Studio zum Herunterladen und Installieren der **Azure Functions Core (CLI)** -Tools. Sie müssen möglicherweise auch eine Firewallausnahme aktivieren, damit die Tools HTTP-Anforderungen verarbeiten können.

1. Kopieren Sie die URL Ihrer Funktion aus der Azure Functions-Laufzeitausgabe.

    ![Schnellstart: Debuggen von Funktionen in VS](./media/quickstarts/function-visual-studio-debugging.png)

1. Fügen Sie die URL zu der HTTP-Anforderung in die Adressleiste Ihres Browsers ein. Die folgende Abbildung zeigt die Antwort, die angibt, dass das Featureflag `Beta` deaktiviert ist. 

    ![Featureflag für Schnellstartfunktion deaktiviert](./media/quickstarts/functions-launch-ff-disabled.png)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Alle Ressourcen** und dann den App Configuration-Speicher aus, den Sie erstellt haben.

1. Wählen Sie **Feature-Manager** aus, und ändern Sie den Zustand des **Beta**-Schlüssels in **Ein**.

1. Aktualisieren Sie den Browser mehrmals. Nachdem das zwischengespeicherte Featureflag nach 30 Sekunden abgelaufen ist, sollte auf der Seite angezeigt werden, dass das Featureflag `Beta` aktiviert ist. Dies ist in der Abbildung unten dargestellt.
 
    ![Featureflag für Schnellstartfunktion aktiviert](./media/quickstarts/functions-launch-ff-enabled.png)

> [!NOTE]
> Der in diesem Tutorial verwendete Beispielcode kann aus dem [GitHub-Repository für Azure App Configuration](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction) heruntergeladen werden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie ein Featureflag erstellt und mit einer Azure Functions-App über die [Microsoft.FeatureManagement](/dotnet/api/microsoft.featuremanagement)-Bibliothek verwendet.

- Weitere Informationen zur [Featureverwaltung](./concept-feature-management.md)
- [Verwalten von Featureflags](./manage-feature-flags.md)
- [Verwenden von bedingten Featureflags](./howto-feature-filters-aspnet-core.md)
- [Aktivieren des gestaffelten Rollouts von Features für Zielgruppen](./howto-targetingfilter-aspnet-core.md)
- [Verwenden der dynamischen Konfiguration in einer Azure Functions-App](./enable-dynamic-configuration-azure-functions-csharp.md)