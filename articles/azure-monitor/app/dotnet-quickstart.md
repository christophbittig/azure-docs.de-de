---
title: 'Schnellstart: Überwachen einer ASP.NET Core-App mit Azure Monitor Application Insights'
description: Instrumentieren Sie eine ASP.NET Core-Web-App für die Überwachung mit Azure Monitor Application Insights.
ms.topic: quickstart
ms.date: 06/11/2021
ms.custom: devx-track-dotnet
ms.openlocfilehash: 344f34116a1c6b40ac452fea4c9f27d8c89ba3ad
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130134049"
---
# <a name="quickstart-monitor-an-aspnet-core-app-with-azure-monitor-application-insights"></a>Schnellstart: Überwachen einer ASP.NET Core-App mit Azure Monitor Application Insights

In diesem Schnellstart instrumentieren Sie eine ASP.NET Core-App mit dem Application Insights SDK, um client- und serverseitige Telemetriedaten zu erfassen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/dotnet).
- .NET 5.0 SDK oder höher [Installieren Sie das neueste .NET 5.0 SDK](https://dotnet.microsoft.com/download/dotnet/5.0) für Ihre Plattform.

## <a name="create-an-application-insights-resource"></a>Erstellen einer Application Insights-Ressource

Um mit der Erfassung von Telemetriedaten zu beginnen, erstellen Sie eine Application Insights-Ressource in Ihrem Azure-Abonnement.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Klicken Sie auf **Ressource erstellen** > **Entwicklertools** > **Application Insights**.

1. Füllen Sie das angezeigte Formular aus:
    1. Überprüfen Sie das ausgewählte **Abonnement**.
    1. Wählen Sie eine vorhandene **Ressourcengruppe** aus, oder erstellen Sie eine neue Ressourcengruppe.
    1. Geben Sie einen **Namen** für diese Application Insights-Ressource an.
    1. Wählen Sie eine **Region** in Ihrer Nähe aus.
    1. Legen Sie den **Ressourcenmodus** auf *Klassisch* fest. 

1. Wählen Sie die Schaltfläche **Überprüfen + erstellen** aus.
1. Wählen Sie die Schaltfläche **Erstellen**.
1. Wählen Sie nach Abschluss der Bereitstellung die Schaltfläche **Zu Ressource wechseln** aus.
1. Kopieren Sie in der angezeigten **Übersicht** Ihren **Instrumentierungsschlüssel** (unter **Zusammenfassung**).

## <a name="create-and-configure-an-aspnet-core-web-app"></a>Erstellen und Konfigurieren einer ASP.NET Core-Web-App

Führen Sie die folgenden Schritte aus, um eine neue ASP.NET Core-Web-App zu erstellen und zu konfigurieren:

1. Erstellen einer ASP.NET Core Razor Pages-App
    
    ```dotnetcli
    dotnet new razor -o ai.quickstart
    ```
    
    Mit dem vorherigen Befehl wird eine neue ASP.NET Core Razor Pages-App im Verzeichnis *ai.quickstart* erstellt. 
    
    > [!TIP]
    > Möglicherweise möchten Sie [Visual Studio verwenden, um Ihre App zu erstellen](/visualstudio/ide/quickstart-aspnet-core).

1. Fügen Sie dem Projekt das Paket `Microsoft.ApplicationInsights.AspNetCore` aus dem Projektverzeichnis hinzu. Wenn Sie Visual Studio verwenden, können Sie den [NuGet-Paket-Manager](/nuget/consume-packages/install-use-packages-visual-studio) verwenden.

    ```dotnetcli
    dotnet add package Microsoft.ApplicationInsights.AspNetCore --version 2.17.0 
    ```

1. Ändern Sie mithilfe eines Text-Editors oder einer IDE die Datei *appsettings.js* so, dass sie wie gezeigt einen Wert für `ApplicationInsights.InstrumentationKey` enthält. Verwenden Sie den zuvor kopierten Instrumentierungsschlüssel.

    :::code language="json" source="~/dotnet-samples/azure/app-insights-aspnet-core-quickstart/appsettings.json" range="1-12" highlight="2-4":::
    
    > [!IMPORTANT]
    > Das Application Insights SDK erwartet den Konfigurationswert `ApplicationInsights.InstrumentationKey`. Achten Sie darauf, ihn richtig zu benennen!

## <a name="configure-server-side-telemetry"></a>Konfigurieren der serverseitigen Telemetrie

Fügen Sie der Pipeline in der `ConfigureServices`-Methode von *Startup.cs* den Application Insights-Dienst hinzu. Fügen Sie die hervorgehobene Zeile hinzu:

:::code language="csharp" source="~/dotnet-samples/azure/app-insights-aspnet-core-quickstart/startup.cs" range="24-28" highlight="4":::

## <a name="configure-client-side-telemetry"></a>Konfigurieren der clientseitigen Telemetrie

Führen Sie die folgenden Schritte aus, um die App zum Senden clientseitiger Telemetriedaten zu instrumentieren:

1. Fügen Sie in *Pages/_ViewImports.cshtml* die folgende Zeile hinzu:

    ```cshtml
    @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

    Die vorherige Änderung registriert eine `Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet`-Abhängigkeit, die das clientseitige Application Insights-Skriptelement enthält.

1. Fügen Sie in *Pages/Shared/_Layout.cshtml* im `<head>`-Element die hervorgehobene Zeile hinzu:

    :::code language="cshtml" source="~/dotnet-samples/azure/app-insights-aspnet-core-quickstart/pages/shared/_layout.cshtml" range="3-10" highlight="7":::

   Durch diese Änderung wird das eingefügte `JavaScriptSnippet`-Objekt verwendet, um sicherzustellen, dass das `<script>`-Element im `<head>`-Element jeder Seite in der App gerendert wird.

## <a name="validate-telemetry-ingestion"></a>Überprüfen der Telemetrieerfassung

Es dauert einige Minuten, bis Telemetriedaten für die Analyse in Application Insights erfasst werden. Verwenden Sie **Livemetriken**, um zu überprüfen, ob Ihre App Telemetriedaten in Echtzeit sendet:

1. Führen Sie die Web-App mit `dotnet run` oder in Ihrer IDE aus.
1. Wählen Sie im Azure-Portal beim Anzeigen Ihrer Application Insights-Ressource unter **Untersuchen** die Option **Livemetriken** aus.
1. Wählen Sie in Ihrer App (`http://localhost:5000`) wiederholt die Links **Home** (Startseite) und **Privacy** (Datenschutz) aus.
1. Beobachten Sie die Aktivität in den **Livemetriken**, die angezeigt werden, wenn in der App Anforderungen ausgeführt werden.

## <a name="next-steps"></a>Nächste Schritte

Glückwunsch! Sie können jetzt die von Ihrer App gesendeten Telemetriedaten für Folgendes verwenden:

- [Suchen von Runtimeausnahmen](tutorial-runtime-exceptions.md)
- [Suchen von Leistungsproblemen](tutorial-performance.md)
- [Ausgeben von Warnungen zur App-Integrität](tutorial-alert.md)

> [!div class="nextstepaction"]
> [Weitere Informationen zu Application Insights in ASP.NET Core](asp-net-core.md)
