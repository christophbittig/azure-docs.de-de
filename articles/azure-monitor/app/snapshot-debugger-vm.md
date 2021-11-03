---
title: Aktivieren des Momentaufnahmedebuggers für.NET-Anwendungen in Azure Service Fabric, Cloud Service und Virtual Machines | Microsoft-Dokumentation
description: Aktivieren des Momentaufnahmedebuggers für.NET-Anwendungen in Azure Service Fabric, Cloud Service und Virtual Machines
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 99a0c2e765d725731e24b680a135b1ecab1a9bad
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131006694"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-service-fabric-cloud-service-and-virtual-machines"></a>Aktivieren des Momentaufnahmedebuggers für.NET-Anwendungen in Azure Service Fabric, Cloud Service und Virtual Machines

Wenn Sie Ihre ASP.NET- oder ASP.NET Core-Anwendung in Azure App Service ausführen, wird dringend empfohlen, [den Momentaufnahmedebugger über die Application Insights-Portalseite zu aktivieren](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json). Wenn Ihre Anwendung jedoch eine benutzerdefinierte Konfiguration für den Momentaufnahmedebugger oder eine Vorschauversion von .NET Core erfordert, sollte diese Anweisung ***zusätzlich*** zu den Anweisungen zum [Aktivieren über die Application Insights-Portalseite](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json) befolgt werden.

Wenn Ihre Anwendung in Azure Service Fabric, Cloud Service, Virtual Machines oder auf lokalen Computern ausgeführt wird, sollten die folgenden Anweisungen verwendet werden. 

## <a name="configure-snapshot-collection-for-aspnet-applications"></a>Konfigurieren der Momentaufnahmesammlung für ASP.NET-Anwendungen

1. Falls noch nicht geschehen, [aktivieren Sie Application Insights für Ihre Web-App](./asp-net.md).

2. Binden Sie das NuGet-Paket [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) in Ihre App ein.

3. Bei Bedarf können Sie die Konfiguration des Momentaufnahmedebuggers anpassen, die zu [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) hinzugefügt wurde. Die Standardkonfiguration des Momentaufnahmedebuggers ist weitgehend leer und alle Einstellungen sind optional. Hier folgt ein Beispiel für eine Konfiguration, die der Standardkonfiguration entspricht:

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>1</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often we reconnect to the stamp. The default value is 15 minutes.-->
        <ReconnectInterval>00:15:00</ReconnectInterval>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>1.00:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in ten minutes.The default value is 1. -->
        <SnapshotsPerTenMinutesLimit>3</SnapshotsPerTenMinutesLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>30</SnapshotsPerDayLimit>
        <!-- Whether or not to collect snapshot in low IO priority thread. The default value is true. -->
        <SnapshotInLowPriorityThread>true</SnapshotInLowPriorityThread>
        <!-- Agree to send anonymous data to Microsoft to make this product better. -->
        <ProvideAnonymousTelemetry>true</ProvideAnonymousTelemetry>
        <!-- The limit on the number of failed requests to request snapshots before the telemetry processor is disabled. -->
        <FailedRequestLimit>3</FailedRequestLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. Momentaufnahmen werden nur zu Ausnahmen erfasst, die Application Insights gemeldet wurden. In einigen Fällen (z.B. bei älteren Versionen der .NET-Plattform) müssen Sie möglicherweise die [Ausnahmesammlung konfigurieren](./asp-net-exceptions.md#exceptions), um Ausnahmen mit Momentaufnahmen im Portal zu sehen.


## <a name="configure-snapshot-collection-for-applications-using-aspnet-core-20-or-above"></a>Konfigurieren der Momentaufnahmesammlung für Anwendungen mit ASP.NET Core 2.0 oder höher

1. Falls noch nicht geschehen, [aktivieren Sie Application Insights für Ihre ASP.NET Core-Web-App](./asp-net-core.md).

    > [!NOTE]
    > Stellen Sie sicher, dass Ihre Anwendung mindestens auf Version 2.1.1 des Microsoft.ApplicationInsights.AspNetCore-Pakets verweist.

2. Binden Sie das NuGet-Paket [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) in Ihre App ein.

3. Ändern Sie die `Startup`-Klasse Ihrer Anwendung, um den Telemetrieprozessor des Momentaufnahmesammlers hinzuzufügen und zu konfigurieren.
    1. Wenn das NuGet-Paket [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) Version 1.3.5 oder höher verwendet wird, fügen Sie mithilfe von Anweisungen Folgendes zu `Startup.cs` hinzu.

       ```csharp
            using Microsoft.ApplicationInsights.SnapshotCollector;
       ```

       Fügen Sie am Ende der Methode „ConfigureServices“ in der Klasse `Startup` in `Startup.cs` den folgenden Code hinzu.

       ```csharp
            services.AddSnapshotCollector((configuration) => Configuration.Bind(nameof(SnapshotCollectorConfiguration), configuration));
       ```
    2. Wenn das NuGet-Paket [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) Version 1.3.4 oder niedriger verwendet wird, fügen Sie mithilfe von Anweisungen Folgendes zu `Startup.cs` hinzu.

       ```csharp
       using Microsoft.ApplicationInsights.SnapshotCollector;
       using Microsoft.Extensions.Options;
       using Microsoft.ApplicationInsights.AspNetCore;
       using Microsoft.ApplicationInsights.Extensibility;
       ```

       Fügen Sie der `Startup`-Klasse die folgende `SnapshotCollectorTelemetryProcessorFactory`-Klasse hinzu.

       ```csharp
       class Startup
       {
           private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
           {
               private readonly IServiceProvider _serviceProvider;

               public SnapshotCollectorTelemetryProcessorFactory(IServiceProvider serviceProvider) =>
                   _serviceProvider = serviceProvider;

               public ITelemetryProcessor Create(ITelemetryProcessor next)
               {
                   var snapshotConfigurationOptions = _serviceProvider.GetService<IOptions<SnapshotCollectorConfiguration>>();
                   return new SnapshotCollectorTelemetryProcessor(next, configuration: snapshotConfigurationOptions.Value);
               }
           }
           ...
        ```
        Fügen Sie der Startpipeline die Dienste `SnapshotCollectorConfiguration` und `SnapshotCollectorTelemetryProcessorFactory` hinzu:

        ```csharp
           // This method gets called by the runtime. Use this method to add services to the container.
           public void ConfigureServices(IServiceCollection services)
           {
               // Configure SnapshotCollector from application settings
               services.Configure<SnapshotCollectorConfiguration>(Configuration.GetSection(nameof(SnapshotCollectorConfiguration)));

               // Add SnapshotCollector telemetry processor.
               services.AddSingleton<ITelemetryProcessorFactory>(sp => new SnapshotCollectorTelemetryProcessorFactory(sp));

               // TODO: Add other services your application needs here.
           }
       }
       ```

4. Bei Bedarf können Sie die Konfiguration des Momentaufnahmedebuggers anpassen, indem Sie den Abschnitt „SnapshotCollectorConfiguration“ in „appsettings.json“ hinzufügen. Alle Einstellungen in der Konfiguration des Momentaufnahmedebuggers sind optional. Hier folgt ein Beispiel für eine Konfiguration, die der Standardkonfiguration entspricht:

   ```json
   {
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": false,
       "ThresholdForSnapshotting": 1,
       "MaximumSnapshotsRequired": 3,
       "MaximumCollectionPlanSize": 50,
       "ReconnectInterval": "00:15:00",
       "ProblemCounterResetInterval":"1.00:00:00",
       "SnapshotsPerTenMinutesLimit": 1,
       "SnapshotsPerDayLimit": 30,
       "SnapshotInLowPriorityThread": true,
       "ProvideAnonymousTelemetry": true,
       "FailedRequestLimit": 3
     }
   }
   ```

## <a name="configure-snapshot-collection-for-other-net-applications"></a>Konfigurieren der Momentaufnahmesammlung für andere .NET-Anwendungen

1. Wenn Ihre Anwendung noch nicht mit Application Insights instrumentiert wurde, beginnen Sie mit dem [Aktivieren von Application Insights und Festlegen des Instrumentierungsschlüssels](./windows-desktop.md).

2. Fügen Sie das NuGet-Paket [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) in Ihrer App hinzu.

3. Momentaufnahmen werden nur zu Ausnahmen erfasst, die Application Insights gemeldet wurden. Sie müssen möglicherweise den Code ändern, um diese melden zu können. Der Code zur Behandlung von Ausnahmen hängt von der Struktur Ihrer Anwendung ab. Im Folgenden wird jedoch ein Beispiel gezeigt:

    ```csharp
    TelemetryClient _telemetryClient = new TelemetryClient();

    void ExampleRequest()
    {
        try
        {
            // TODO: Handle the request.
        }
        catch (Exception ex)
        {
            // Report the exception to Application Insights.
            _telemetryClient.TrackException(ex);

            // TODO: Rethrow the exception if desired.
        }
    }
    ```

## <a name="next-steps"></a>Nächste Schritte

- Generieren Sie Datenverkehr für Ihre Anwendung, der eine Ausnahme auslösen kann. Warten Sie dann 10 bis 15 Minuten, bis die Momentaufnahmen an die Application Insights-Instanz gesendet werden.
- Weitere Informationen finden Sie unter [Momentaufnahmen](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) im Azure-Portal.
- Hilfe bei der Behandlung von Problemen mit dem Momentaufnahmedebugger finden Sie unter [Problembehandlung für Momentaufnahmedebugger](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

