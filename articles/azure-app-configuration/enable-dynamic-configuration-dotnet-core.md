---
title: 'Tutorial: Verwenden der dynamischen Konfiguration in einer .NET Core-App'
titleSuffix: Azure App Configuration
description: In diesem Tutorial wird beschrieben, wie Sie die Konfigurationsdaten für .NET Core-Apps dynamisch aktualisieren.
services: azure-app-configuration
documentationcenter: ''
author: GrantMeStrength
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: jken
ms.openlocfilehash: 9a2a8f88cd39b452a54d6fc668737fc1b5be68cc
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129996965"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-core-app"></a>Tutorial: Verwenden der dynamischen Konfiguration in einer .NET Core-App

Die .NET-Anbieterbibliothek von App Configuration unterstützt die bedarfsgesteuerte Aktualisierung der Konfigurationseinstellungen, ohne dass eine Anwendung neu gestartet werden muss. In diesem Tutorial wird veranschaulicht, wie Sie dynamische Konfigurationsupdates in Ihrem Code implementieren können. Dies baut auf der App auf, die in der Schnellstartanleitung vorgestellt wurde. Durchlaufen Sie die Schnellstartanleitung zum [Erstellen einer .NET Core-App mit Azure App Configuration](./quickstart-dotnet-core-app.md), bevor Sie fortfahren.

Für die Ausführung der Schritte dieses Tutorials können Sie einen beliebigen Code-Editor verwenden. [Visual Studio Code](https://code.visualstudio.com/) ist eine hervorragende Option, die auf Windows-, macOS- und Linux-Plattformen verfügbar ist.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Einrichten Ihrer .NET Code-App für die Aktualisierung der Konfiguration als Reaktion auf Änderungen in einem App Configuration-Speicher
> * Verwenden der aktuellen Konfiguration in Ihrer Anwendung

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Durchlaufen Sie den Schnellstart zum [Erstellen einer .NET Core-App mit App Configuration](./quickstart-dotnet-core-app.md).

## <a name="activity-driven-configuration-refresh"></a>Aktivitätsgesteuerte Konfigurationsaktualisierung

Öffnen Sie die Datei *Program.cs*, und aktualisieren Sie den Code wie folgt:

```csharp
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Configuration.AzureAppConfiguration;
using System;
using System.Threading.Tasks;

namespace TestConsole
{
    class Program
    {
        private static IConfiguration _configuration = null;
        private static IConfigurationRefresher _refresher = null;

        static void Main(string[] args)
        {
            var builder = new ConfigurationBuilder();
            builder.AddAzureAppConfiguration(options =>
            {
                options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
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

        private static async Task PrintMessage()
        {
            Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

            // Wait for the user to press Enter
            Console.ReadLine();

            await _refresher.TryRefreshAsync();
            Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
        }
    }
}
```

In der Methode `ConfigureRefresh` wird ein Schlüssel in Ihrem App Configuration-Speicher für die Änderungsüberwachung registriert. Die Methode `Register` enthält den optionalen booleschen Parameter `refreshAll`, mit dem angegeben werden kann, ob alle Konfigurationswerte bei einer Änderung des registrierten Schlüssels aktualisiert werden sollen. In diesem Beispiel wird nur der Schlüssel *TestApp:Settings:Message* aktualisiert. Durch die Methode `SetCacheExpiration` wird angegeben, wie viel Zeit mindestens verstreichen muss, bevor eine neue Anforderung an App Configuration gesendet wird, um nach Konfigurationsänderungen zu suchen. In diesem Beispiel wird die Standardablaufzeit von 30 Sekunden zu Demonstrationszwecken in 10 Sekunden geändert.

Das Aufrufen der Methode `ConfigureRefresh` allein führt nicht dazu, dass die Konfiguration automatisch aktualisiert wird. Sie rufen die Methode `TryRefreshAsync` über die Schnittstelle `IConfigurationRefresher` auf, um eine Aktualisierung auszulösen. Dadurch sollen Phantomanforderungen vermieden werden, die an App Configuration gesendet werden, auch wenn sich Ihre Anwendung im Leerlauf befindet. Sie sollten den Aufruf `TryRefreshAsync` dort verwenden, wo Sie Ihre Anwendung als aktiv betrachten. Dies kann beispielsweise der Fall sein, wenn Sie eine eingehende Nachricht, eine Bestellung oder eine Iteration einer komplexen Aufgabe verarbeiten. Er kann auch Teil eines Timers sein, wenn Ihre Anwendung ständig aktiv ist. In diesem Beispiel rufen Sie `TryRefreshAsync` jedes Mal auf, wenn Sie die EINGABETASTE drücken. Beachten Sie, dass Ihre Anwendung auch dann weiterhin die zwischengespeicherte Konfiguration verwendet, wenn beim Aufruf `TryRefreshAsync` aus irgendeinem Grund ein Fehler auftritt. Ein weiterer Versuch wird unternommen, wenn die konfigurierte Cacheablaufzeit verstrichen ist und der Aufruf `TryRefreshAsync` von Ihrer Anwendungsaktivität erneut ausgelöst wird. Das Aufrufen von `TryRefreshAsync` ist vor Verstreichen der konfigurierten Cacheablaufzeit keine Option. Daher sind die Auswirkungen auf die Leistung minimal, auch wenn der Aufruf häufig erfolgt.

## <a name="build-and-run-the-app-locally"></a>Lokales Erstellen und Ausführen der App

1. Legen Sie eine Umgebungsvariable mit dem Namen **ConnectionString** fest, und geben Sie dafür den Zugriffsschlüssel für Ihren App Configuration-Speicher an. Führen Sie bei Verwendung einer Windows-Eingabeaufforderung den folgenden Befehl aus, und starten Sie die Eingabeaufforderung neu, damit die Änderung wirksam wird:

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

1. Führen Sie den folgenden Befehl aus, um die Konsolen-App zu erstellen:

    ```console
     dotnet build
    ```

1. Führen Sie nach der erfolgreichen Erstellung den folgenden Befehl aus, um die App lokal auszuführen:

    ```console
     dotnet run
    ```

    ![Schnellstartanleitung: Lokales Starten der App](./media/quickstarts/dotnet-core-app-run.png)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Klicken Sie auf **Alle Ressourcen**, und wählen Sie dann die Instanz des App Configuration-Speichers aus, die Sie in der Schnellstartanleitung erstellt haben.

1. Wählen Sie den **Konfigurations-Explorer** aus, und aktualisieren Sie die Werte der folgenden Schlüssel:

    | Schlüssel | Wert |
    |---|---|
    | TestApp:Settings:Message | Daten aus Azure App Configuration: Aktualisiert |

1. Drücken Sie die EINGABETASTE, um eine Aktualisierung auszulösen, und geben Sie den aktualisierten Wert in der Eingabeaufforderung oder im PowerShell-Fenster aus.

    ![Schnellstart: Lokale Aktualisierung der App](./media/quickstarts/dotnet-core-app-run-refresh.png)
    
    > [!NOTE]
    > Da die Cacheablaufzeit beim Angeben der Konfiguration für den Aktualisierungsvorgang mit der `SetCacheExpiration`-Methode auf zehn Sekunden festgelegt wurde, wird der Wert für die Konfigurationseinstellung erst aktualisiert, wenn seit der letzten Aktualisierung für diese Einstellung mindestens zehn Sekunden verstrichen sind.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Ihre .NET Core-App aktiviert, um Konfigurationseinstellungen dynamisch aus App Configuration zu aktualisieren. Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie eine von Azure verwaltete Identität hinzufügen, um den Zugriff auf App Configuration zu optimieren.

> [!div class="nextstepaction"]
> [Integration der verwalteten Identität](./howto-integrate-azure-managed-service-identity.md)
