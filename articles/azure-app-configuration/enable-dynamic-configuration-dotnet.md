---
title: '.NET Framework-Tutorial: dynamische Konfiguration in Azure App Configuration'
description: In diesem Tutorial wird beschrieben, wie Sie die Konfigurationsdaten für .NET Framework-Apps mithilfe von Azure App Configuration dynamisch aktualisieren.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: tutorial
ms.date: 07/24/2020
ms.author: alkemper
ms.openlocfilehash: d6c3bc0ee45c214419820208598e5721309ea144
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130261042"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-framework-app"></a>Tutorial: Verwenden der dynamischen Konfiguration in einer .NET Framework-App

Daten aus App Configuration können als App-Einstellungen in eine .NET Framework-App geladen werden. Weitere Informationen finden Sie im [Schnellstart](./quickstart-dotnet-app.md). Wie im .NET Framework vorgesehen, können die App-Einstellungen jedoch nur beim Neustart der App aktualisiert werden. Der .NET-Anbieter von App Configuration ist eine .NET Standard-Bibliothek. Sie unterstützt das dynamische Zwischenspeichern und Aktualisieren der Konfiguration ohne App-Neustart. In diesem Tutorial wird veranschaulicht, wie Sie dynamische Konfigurationsupdates in einer .NET Framework-Konsolen-App implementieren können.

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
> * Einrichten Ihrer .NET Framework-App für die Aktualisierung der Konfiguration als Reaktion auf Änderungen in einem App Configuration-Speicher
> * Einfügen der aktuellen Konfiguration in Ihre Anwendung

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/)
- [Visual Studio](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.2 oder höher](https://dotnet.microsoft.com/download/dotnet-framework)

## <a name="create-an-app-configuration-store"></a>Erstellen eines App Configuration-Speichers

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Wählen Sie **Konfigurations-Explorer** >  **+ Erstellen** > **Schlüssel-Wert** aus, um das folgende Schlüssel-Wert-Paar hinzuzufügen:

    | Key                        | Wert                               |
    |----------------------------|-------------------------------------|
    | *TestApp:Settings:Message* | *Daten aus Azure App Configuration* |

    Lassen Sie **Bezeichnung** und **Inhaltstyp** leer.

## <a name="create-a-net-framework-console-app"></a>Erstellen einer .NET Framework-Konsolen-App

1. Starten Sie Visual Studio, und wählen Sie **Neues Projekt erstellen** aus.

1. Filtern Sie unter **Neues Projekt erstellen** nach dem Projekttyp **Konsole**, und klicken Sie in der Projektvorlagenliste auf **Konsolen-App (.NET Framework)** mit C#. Klicken Sie auf **Weiter**.

1. Geben Sie unter **Neues Projekt konfigurieren** einen Projektnamen ein. Wählen Sie unter **Framework** die Option **.NET Framework 4.7.2** oder höher aus. Klicken Sie auf **Erstellen**.

## <a name="reload-data-from-app-configuration"></a>Erneutes Laden von Daten aus App Configuration
1. Klicken Sie mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **NuGet-Pakete verwalten** aus. Suchen Sie auf der Registerkarte **Durchsuchen** die neueste Version des folgenden NuGet-Pakets, und fügen Sie sie Ihrem Projekt hinzu.

   *Microsoft.Extensions.Configuration.AzureAppConfiguration*

1. Öffnen Sie *Program.cs*, und fügen Sie die folgenden Namespaces hinzu.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Fügen Sie zwei Variablen zum Speichern konfigurationsbezogener Objekte hinzu.

    ```csharp
    private static IConfiguration _configuration;
    private static IConfigurationRefresher _refresher;
    ```

1. Aktualisieren Sie mit den angegebenen Aktualisierungsoptionen die Methode `Main`, um eine Verbindung mit App Configuration herzustellen.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                    // Load all keys that start with `TestApp:`.
                    .Select("TestApp:*")
                    // Configure to reload the key 'TestApp:Settings:Message' if it is modified.
                    .ConfigureRefresh(refresh =>
                    {
                        refresh.Register("TestApp:Settings:Message")
                               .SetCacheExpiration(TimeSpan.FromSeconds(10));
                    });

            _refresher = options.GetRefresher();
        });

        _configuration = builder.Build();
        PrintMessage().Wait();
    }
    ```

    In der Methode `ConfigureRefresh` wird ein Schlüssel in Ihrem App Configuration-Speicher für die Änderungsüberwachung registriert. Die Methode `Register` enthält den optionalen booleschen Parameter `refreshAll`, mit dem angegeben werden kann, ob alle Konfigurationswerte bei einer Änderung des registrierten Schlüssels aktualisiert werden sollen. In diesem Beispiel wird nur der Schlüssel *TestApp:Settings:Message* aktualisiert. Durch die Methode `SetCacheExpiration` wird angegeben, wie viel Zeit mindestens verstreichen muss, bevor eine neue Anforderung an App Configuration gesendet wird, um nach Konfigurationsänderungen zu suchen. In diesem Beispiel wird die Standardablaufzeit von 30 Sekunden zu Demonstrationszwecken in 10 Sekunden geändert.

1. Fügen Sie eine Methode namens `PrintMessage()` hinzu, die eine Aktualisierung der Konfigurationsdaten aus App Configuration auslöst.

    ```csharp
    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.TryRefreshAsync();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

    Das Aufrufen der Methode `ConfigureRefresh` allein führt nicht dazu, dass die Konfiguration automatisch aktualisiert wird. Sie rufen die Methode `TryRefreshAsync` über die Schnittstelle `IConfigurationRefresher` auf, um eine Aktualisierung auszulösen. Dadurch sollen Phantomanforderungen vermieden werden, die an App Configuration gesendet werden, auch wenn sich Ihre Anwendung im Leerlauf befindet. Sie können den Aufruf `TryRefreshAsync` dort verwenden, wo Sie Ihre Anwendung als aktiv betrachten. Dies kann beispielsweise der Fall sein, wenn Sie eine eingehende Nachricht, eine Bestellung oder eine Iteration einer komplexen Aufgabe verarbeiten. Er kann auch Teil eines Timers sein, wenn Ihre Anwendung ständig aktiv ist. In diesem Beispiel rufen Sie `TryRefreshAsync` auf, wenn Sie die EINGABETASTE drücken. Beachten Sie, dass Ihre Anwendung auch dann weiterhin die zwischengespeicherte Konfiguration verwendet, wenn beim Aufruf `TryRefreshAsync` aus irgendeinem Grund ein Fehler auftritt. Ein weiterer Versuch wird unternommen, wenn die konfigurierte Cacheablaufzeit verstrichen ist und der Aufruf `TryRefreshAsync` von Ihrer Anwendungsaktivität erneut ausgelöst wird. Das Aufrufen von `TryRefreshAsync` ist vor Verstreichen der konfigurierten Cacheablaufzeit keine Option. Daher sind die Auswirkungen auf die Leistung minimal, auch wenn der Aufruf häufig erfolgt.

## <a name="build-and-run-the-app-locally"></a>Lokales Erstellen und Ausführen der App

1. Legen Sie eine Umgebungsvariable namens **ConnectionString** auf die Verbindungszeichenfolge mit schreibgeschütztem Schlüssel fest, die Sie bei der Erstellung Ihres App Configuration-Speichers erhalten haben. 

    Führen Sie bei Verwendung der Windows-Eingabeaufforderung den folgenden Befehl aus:
    ```console
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Führen Sie bei Verwendung von Windows PowerShell den folgenden Befehl aus:
    ```powershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

1. Starten Sie Visual Studio, damit die Änderung wirksam wird. 

1. Drücken Sie STRG+F5, um die Konsolen-App zu erstellen und auszuführen.

    ![Lokaler App-Start](./media/dotnet-app-run.png)

1. Navigieren Sie im Azure-Portal zum **Konfigurations-Explorer** Ihres App Configuration-Speichers, und aktualisieren Sie den Wert des folgenden Schlüssels.

    | Key                        | Wert                                         |
    |----------------------------|-----------------------------------------------|
    | *TestApp:Settings:Message* | *Daten aus Azure App Configuration: Aktualisiert* |

1. Drücken Sie in der ausgeführten Anwendung die EINGABETASTE, um eine Aktualisierung auszulösen, und geben Sie den aktualisierten Wert in der Eingabeaufforderung oder im PowerShell-Fenster aus.

    ![Lokale App-Aktualisierung](./media/dotnet-app-run-refresh.png)
    
    > [!NOTE]
    > Da die Cacheablaufzeit beim Angeben der Konfiguration für den Aktualisierungsvorgang mit der `SetCacheExpiration`-Methode auf zehn Sekunden festgelegt wurde, wird der Wert für die Konfigurationseinstellung erst aktualisiert, wenn seit der letzten Aktualisierung für diese Einstellung mindestens zehn Sekunden verstrichen sind.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Ihre .NET Framework-App aktiviert, um Konfigurationseinstellungen dynamisch aus App Configuration zu aktualisieren. Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie die dynamische Konfiguration in einer ASP.NET-Webanwendung (.NET Framework) aktivieren:

> [!div class="nextstepaction"]
> [Aktivieren der dynamischen Konfiguration in ASP.NET-Webanwendungen](./enable-dynamic-configuration-aspnet-netfx.md)

Im nächsten Tutorial erfahren Sie, wie Sie eine von Azure verwaltete Identität hinzufügen, um den Zugriff auf App Configuration zu optimieren:

> [!div class="nextstepaction"]
> [Integration der verwalteten Identität](./howto-integrate-azure-managed-service-identity.md)
