---
title: Überwachung der Leistung von Azure-Anwendungsdiensten NET Core | Microsoft-Dokumentation
description: Überwachung der Anwendungsleistung für Azure-Anwendungsdienste mit ASP.NET Core. Ladezeit für Diagramme und Antwortzeit, Informationen zu den Abhängigkeiten und Festlegen von Benachrichtigungen zur Leistung.
ms.topic: conceptual
ms.date: 08/05/2021
ms.custom: devx-track-js, devx-track-dotnet
ms.openlocfilehash: 747b79affc2cb2ef862c1fb4000879ddbbe5b9f5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132308768"
---
# <a name="application-monitoring-for-azure-app-service-and-aspnet-core"></a>Anwendungsüberwachung für Azure App Service und ASP.NET Core 

Die Aktivierung der Überwachung IhrerASP.NET Core-basierten Webanwendungen, die auf [Azure App Service](../../app-service/index.yml) laufen, ist jetzt einfacher denn je. Während Sie Ihre App zuvor manuell instrumentieren mussten, ist die neueste Erweiterung/der neueste Agent nun standardmäßig in das App Service-Image integriert. Dieser Artikel führt Sie Schritt für Schritt durch das Aktivieren der Application Insights-Überwachung von Azure Monitor und bietet eine vorläufige Anleitung zur Automatisierung des Prozesses für umfangreiche Bereitstellungen.

## <a name="enable-agent-based-monitoring"></a>Aktivieren der Agent-basierten Überwachung

# <a name="windows"></a>[Windows](#tab/Windows)

> [!IMPORTANT]
> Die folgenden Versionen von ASP.NET Core werden für die automatische Instrumentierung unter Windows unterstützt: ASP.NET Core 3.1, 5.0 und 6.0. Die Versionen 2.0, 2.1, 2.2 und 3.0 wurden eingestellt und werden nicht mehr unterstützt. Führen Sie ein Upgrade auf eine [unterstützte Version](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) von .NET Core aus, damit die automatische Instrumentierung funktioniert.

[Das Kürzen eigenständiger Bereitstellungen](/dotnet/core/deploying/trimming/trim-self-contained) wird **nicht unterstützt.** Verwenden Sie stattdessen die [manuelle Instrumentierung](./asp-net-core.md) über Code.

Lesen Sie im Folgenden den Abschnitt zum [Aktivieren der Überwachung](#enable-monitoring ), um mit der Einrichtung von Application Insights mit Ihrer App Service-Ressource zu beginnen. 

# <a name="linux"></a>[Linux](#tab/Linux)

> [!IMPORTANT]
> Nur ASP.NET Core 6.0 wird für die automatische Instrumentierung unter Linux unterstützt.

> [!NOTE]
> Die Aktivierung der automatischen Instrumentierung unter Linux im App Services-Portal befindet sich in der Public Preview. Diese Vorschauversionen werden ohne Vereinbarung zum Servicelevel bereitgestellt. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.

[Das Kürzen eigenständiger Bereitstellungen](/dotnet/core/deploying/trimming/trim-self-contained) wird **nicht unterstützt.** Verwenden Sie stattdessen die [manuelle Instrumentierung](./asp-net-core.md) über Code.

Lesen Sie im Folgenden den Abschnitt zum [Aktivieren der Überwachung](#enable-monitoring ), um mit der Einrichtung von Application Insights mit Ihrer App Service-Ressource zu beginnen. 

---
 

### <a name="enable-monitoring"></a>Aktivieren der Überwachung 

1. **Wählen Sie Application Insights** in Azure Einstellungen für Ihren App Service und dann **Aktivieren**.

    :::image type="content"source="./media/azure-web-apps/enable.png" alt-text="Screenshot von der Registerkarte Application Insights mit „Aktivieren“ ausgewählt."::: 

2. Erstellen Sie eine neue Ressource, oder wählen Sie eine vorhandene Application Insights-Ressource für diese Anwendung aus.

    > [!NOTE]
    > Beim Klicken auf **OK** zum Erstellen der neuen Ressource wird die Aufforderung **Überwachungseinstellungen anwenden** angezeigt. Wenn Sie **Weiter** wählen, wird Ihre neue Application Insights-Ressource mit Ihrer App Service-Instanz verknüpft, und außerdem wird **ein Neustart Ihrer App Service-Instanz ausgelöst**. 

    :::image type="content"source="./media/azure-web-apps/change-resource.png" alt-text="Screenshot mit Dropdown-Menü „Change your resource“."::: 

2. Nach Angabe der zu verwendenden Ressource können Sie plattformspezifisch auswählen, wie Application Insights Daten für Ihre Anwendung erfassen soll. ASP.NET Core bietet **Empfohlene Sammlung**  oder **Deaktiviert** an.

    :::image type="content"source="./media/azure-web-apps-net-core/instrument-net-core.png" alt-text=" Screenshot des Abschnitts „Instrumentieren Ihrer Anwendung“."::: 


## <a name="enable-client-side-monitoring"></a>Aktivieren der clientseitigen Überwachung

Die clientseitige Überwachung für ASP.NET Core-Apps ist **standardmäßig aktiviert** mit **Empfohlene Sammlung**, unabhängig davon, ob die App-Einstellung „APPINSIGHTS_JAVASCRIPT_ENABLED“ vorhanden ist.

Wenn Sie aus irgendeinem Grund die clientseitige Überwachung deaktivieren möchten, gehen Sie folgendermaßen vor:

* **Einstellungen** **>** **Konfiguration**
   * Erstellen Sie unter „Anwendungseinstellungen“ eine **neue Anwendungseinstellung**:

     Name: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Wert: `false`

   * **Speichern** Sie die Einstellungen, und **starten Sie die App neu**.


## <a name="automate-monitoring"></a>Automatisieren der Überwachung

Um die Sammlung von Telemetriedaten mit Application Insights zu aktivieren, brauchen Sie nur die Anwendungseinstellungen festzulegen:

:::image type="content"source="./media/azure-web-apps-net-core/application-settings-net-core.png" alt-text="Screenshot von App Service-Anwendungseinstellungen mit verfügbaren Application Insights-Einstellungen."::: 


### <a name="application-settings-definitions"></a>Definitionen von Anwendungseinstellungen

|Name der App-Einstellung |  Definition | Wert |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Die Haupterweiterung, die die Laufzeitüberwachung steuert. | `~2` für Windows oder `~3` für Linux |
|XDT_MicrosoftApplicationInsights_Mode |  Im Standardmodus werden nur wichtige Funktionen aktiviert, um eine optimale Leistung zu gewährleisten. | `disabled` oder `recommended`. |
|XDT_MicrosoftApplicationInsights_PreemptSdk | Diese Einstellung ist nur für ASP.NET Core-Apps geeignet. Sie aktiviert die Interoperabilität mit dem Application Insights-SDK. Außerdem lädt sie die Erweiterung zusammen mit dem SDK und verwendet es, um Telemetriedaten zu senden. Das Application Insights-SDK wird dabei deaktiviert. |`1`|


[!INCLUDE [azure-web-apps-arm-automation](../../../includes/azure-monitor-app-insights-azure-web-apps-arm-automation.md)]


## <a name="upgrade-monitoring-extensionagent---net"></a>Upgrade von Überwachungserweiterung/-Agent: .NET 

### <a name="upgrade-from-versions-289-and-up"></a>Upgrade von Versionen 2.8.9 und höher

Das Upgrade von Version 2.8.9 erfolgt automatisch ohne zusätzliche Aktionen. Die neuen Überwachungsbits werden im Hintergrund an den Dienst der Zielanwendung übermittelt und beim Neustart der Anwendung abgeholt.

Informationen zur aktuell verwendeten Version der Erweiterung finden Sie unter `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`.

:::image type="content"source="./media/azure-web-apps/extension-version.png" alt-text="Screenshot von dem URL-Pfad zum Überprüfen der Version der von Ihnen ausgeführten Erweiterung." border="false"::: 

### <a name="upgrade-from-versions-100---265"></a>Upgrade von Version 1.0.0 – 2.6.5

Ab Version 2.8.9 wird die vorinstallierte Websiteerweiterung verwendet. Bei einer früheren Version können Sie eine Aktualisierung auf zwei Arten vornehmen:

* [Upgrade durch Aktivierung über das Portal](#enable-agent-based-monitoring). (Selbst wenn die Application Insights-Erweiterung für Azure App Service installiert ist, wird nur die Schaltfläche **Aktivieren** auf der Benutzeroberfläche angezeigt. Im Hintergrund wird die alte private Websiteerweiterung entfernt.)

* [Upgrade über PowerShell](#enable-through-powershell):

    1. Legen Sie die Anwendungseinstellungen so fest, dass die vorinstallierte Websiteerweiterung „ApplicationInsightsAgent“ aktiviert wird. Siehe [Aktivieren über PowerShell](#enable-through-powershell).
    2. Entfernen Sie die private Websiteerweiterung mit dem Namen Application Insights-Erweiterung für Azure App Service manuell.

Wenn das Upgrade für eine frühere Version als 2.5.1 ausgeführt wird, vergewissern Sie sich, dass die ApplicationInsights-DLL-Dateien aus dem Ordner „bin“ der Anwendung entfernt werden ([siehe Schritte zur Problembehandlung](#troubleshooting)).

## <a name="troubleshooting"></a>Problembehandlung

> [!NOTE]
> Wenn Sie eine Web-App mit den `ASP.NET Core`-Runtimes in Azure App Services erstellen, wird eine einzelne statische HTML-Seite als Startwebsite bereitgestellt. Es wird **nicht** empfohlen, ein Problem mit der Standardvorlage zu beheben. Stellen Sie eine Anwendung vor der Problembehandlung bereit.

Im Folgenden finden Sie eine Schritt-für-Schritt-Anleitung zur Problembehandlung für die Überwachung von aufASP.NET Core basierenden Anwendungen, die auf Azure App Services ausgeführt werden.

# <a name="windows"></a>[Windows](#tab/windows)

1. Überprüfen Sie, ob die App-Einstellung `ApplicationInsightsAgent_EXTENSION_VERSION` auf den Wert „~2“ festgelegt ist.
2. Navigieren Sie zu `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`.  

    :::image type="content"source="./media/azure-web-apps/app-insights-sdk-status.png" alt-text="Screenshot von dem Link über der Ergebnisseite."border ="false"::: 
    
    - Stellen Sie sicher, dass für `Application Insights Extension Status``Pre-Installed Site Extension, version 2.8.x.xxxx, is running.`angegeben ist. 
    
         Wenn es nicht läuft, befolgen Sie die Anweisungen [Aktivieren Sie die Überwachung von Application Insights](#enable-agent-based-monitoring).

    - Stellen Sie sicher, dass die Statusquelle vorhanden ist und folgendermaßen aussieht: `Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`

         Wenn ein ähnlicher Wert nicht vorhanden ist, bedeutet dies, dass die Anwendung derzeit nicht ausgeführt oder nicht unterstützt wird. Um sicherzustellen, dass die Anwendung ausgeführt wird, rufen Sie die Anwendungs-URL/Anwendungsendpunkte manuell auf, wodurch die Laufzeitinformationen verfügbar werden.

    - Stellen Sie sicher, dass `IKeyExists` den Wert `true` hat. Wenn der Wert `false` ist, fügen Sie den Anwendungseinstellungen mit Ihrer Instrumentierungsschlüssel-GUID `APPINSIGHTS_INSTRUMENTATIONKEY` und `APPLICATIONINSIGHTS_CONNECTION_STRING` hinzu.

    -  Falls Ihre Anwendung auf ein Application Insights-Paket verweist, z.B. wenn Sie Ihre Anwendung zuvor mit dem [ASP.NET Core SDK](./asp-net-core.md) instrumentiert haben (oder versucht haben, sie zu instrumentieren), kann es sein, dass die Aktivierung der App Service-Integration nicht wirksam ist und die Daten nicht in Application Insights erscheinen. Um das Problem zu beheben, aktivieren Sie im Portal die Funktion "Interop mit Application Insights SDK" und Sie werden die Daten in Application Insights sehen. 
    - 
        > [!IMPORTANT]
        > Diese Funktion befindet sich in der Vorschauphase. 

        :::image type="content"source="./media/azure-web-apps-net-core/interop.png" alt-text="Screenshot von der aktivierten Interop-Einstellung."::: 
        
        Die Daten werden jetzt mithilfe eines codelosen Ansatzes gesendet, auch wenn Application Insights SDK ursprünglich verwendet wurde oder versucht wurde, es zu verwenden.

        > [!IMPORTANT]
        > Wenn die Anwendung das Application Insights SDK verwendet hat, um Telemetriedaten zu senden, werden diese Telemetriedaten deaktiviert – anders ausgedrückt: Benutzerdefinierte Telemetriedaten, sofern vorhanden, z. B. alle Track*()-Methoden und alle benutzerdefinierten Einstellungen, wie z. B. Sampling, werden deaktiviert. 

# <a name="linux"></a>[Linux](#tab/linux)

1. Überprüfen Sie, ob die App-Einstellung `ApplicationInsightsAgent_EXTENSION_VERSION` auf den Wert „~3“ festgelegt ist.
2. Navigieren Sie zu */home\LogFiles\ApplicationInsights\status*, und öffnen Sie *status_557de146e7fa_27_1.json*.

    Bestätigen Sie, dass `AppAlreadyInstrumented` auf „false“, `AiHostingStartupLoaded` auf „true“ und `IKeyExists` auf „true“ gesetzt ist.

    Im Folgenden finden Sie ein Beispiel für die JSON-Datei:

    ```json
        "AppType":".NETCoreApp,Version=v6.0",
                
        "MachineName":"557de146e7fa",
                
        "PID":"27",
                
        "AppDomainId":"1",
                
        "AppDomainName":"dotnet6demo",
                
        "InstrumentationEngineLoaded":false,
                
        "InstrumentationEngineExtensionLoaded":false,
                
        "HostingStartupBootstrapperLoaded":true,
                
        "AppAlreadyInstrumented":false,
                
        "AppDiagnosticSourceAssembly":"System.Diagnostics.DiagnosticSource, Version=6.0.0.0, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51",
                
        "AiHostingStartupLoaded":true,
                
        "IKeyExists":true,
                
        "IKey":"00000000-0000-0000-0000-000000000000",
                
        "ConnectionString":"InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://westus-0.in.applicationinsights.azure.com/"
    
    ```
    
    Wenn `AppAlreadyInstrumented` auf „true“ gesetzt ist, zeigt dies an, dass die Erweiterung erkannt hat, dass ein Teil des SDK bereits in der Anwendung vorhanden ist, und der Vorgang wird abgebrochen.

##### <a name="no-data"></a>Keine Daten

1. Listen Sie den Prozess auf, der eine App hostet, und identifizieren Sie ihn. Navigieren Sie zu Ihrem Terminal, und geben Sie in der Befehlszeile `ps ax` ein. 
    
    Die Ausgabe sollte in etwa wie folgt aussehen: 

   ```bash
     PID TTY      STAT   TIME COMMAND
    
        1 ?        SNs    0:00 /bin/bash /opt/startup/startup.sh
    
       19 ?        SNs    0:00 /usr/sbin/sshd
    
       27 ?        SNLl   5:52 dotnet dotnet6demo.dll
    
       50 ?        SNs    0:00 sshd: root@pts/0
    
       53 pts/0    SNs+   0:00 -bash
    
       55 ?        SNs    0:00 sshd: root@pts/1
    
       57 pts/1    SNs+   0:00 -bash
   ``` 


1. Listen Sie anschließend Umgebungsvariablen aus dem App-Prozess auf. Geben Sie in der Befehlszeile `cat /proc/27/environ | tr '\0' '\n` ein.
    
    Die Ausgabe sollte in etwa wie folgt aussehen: 

    ```bash
    ASPNETCORE_HOSTINGSTARTUPASSEMBLIES=Microsoft.ApplicationInsights.StartupBootstrapper
    
    DOTNET_STARTUP_HOOKS=/DotNetCoreAgent/2.8.39/StartupHook/Microsoft.ApplicationInsights.StartupHook.dll
    
    APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://westus-0.in.applicationinsights.azure.com/
    
    ```
    
1. Überprüfen Sie, ob `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_STARTUP_HOOKS` und `APPLICATIONINSIGHTS_CONNECTION_STRING` eingestellt sind.

---

#### <a name="default-website-deployed-with-web-apps-does-not-support-automatic-client-side-monitoring"></a>Die mit Web-Apps bereitgestellte Standardwebsite unterstützt keine automatische clientseitige Überwachung

Wenn Sie eine Web-App mit den `ASP.NET Core`Runtimes in Azure App Services erstellen, wird eine einzelne statische HTML-Seite als Starter-Website bereitgestellt. Die statische Webseite lädt auch ein ASP.NET-verwaltetes Webpart in IIS. Dies ermöglicht das Testen der serverseitigen Überwachung ohne Code, unterstützt jedoch nicht die automatische clientseitige Überwachung.

Wenn Sie die Server- und clientseitige Überwachung ohne Code für ASP.NET Core in einer Azure-App Services-Web-App testen möchten, empfehlen wir Ihnen, die offiziellen Leitfäden zum [Erstellen einer ASP.NET Core-Web-App](../../app-service/quickstart-dotnetcore.md) zu verwenden. Verwenden Sie dann die Anweisungen im aktuellen Artikel, um die Überwachung zu aktivieren.

[!INCLUDE [azure-web-apps-troubleshoot](../../../includes/azure-monitor-app-insights-azure-web-apps-troubleshoot.md)]

### <a name="php-and-wordpress-are-not-supported"></a>PHP und WordPress werden nicht unterstützt.

PHP- und WordPress-Sites werden nicht unterstützt. Zurzeit ist kein offiziell unterstützter SDK/Agent für die serverseitige Überwachung dieser Workloads verfügbar. Das manuelle Instrumentieren clientseitiger Transaktionen auf einer PHP- oder WordPress-Website durch Hinzufügen von clientseitigem JavaScript zu Ihren Webseiten kann jedoch mithilfe des [JavaScript SDK](./javascript.md) erreicht werden.

Die folgende Tabelle enthält eine ausführlichere Beschreibung der Bedeutung dieser Werte, der zugrunde liegenden Ursachen und der empfohlenen Problembehebungen:

|Problemwert |Erklärung |Fix |
|---- |----|---|
| `AppAlreadyInstrumented:true` | Dieser Wert zeigt an, dass die Erweiterung erkannt hat, dass ein Teil des SDK bereits in der Anwendung vorhanden ist, und der Vorgang wird abgebrochen. Mögliche Ursache ist ein Verweis auf `Microsoft.ApplicationInsights.AspNetCore` oder `Microsoft.ApplicationInsights`  | Entfernen Sie die Verweise. Einige dieser Verweise werden standardmäßig aus bestimmten Visual Studio-Vorlagen hinzugefügt, und ältere Versionen von Visual Studio können Verweise zu `Microsoft.ApplicationInsights` hinzufügen. |
|`AppAlreadyInstrumented:true` | Wenn die Anwendung auf ASP.NET Core 2.1 oder 2.2 ausgerichtet ist, zeigt dieser Wert an, dass die Erweiterung erkannt hat, dass ein Teil des SDK bereits in der Anwendung vorhanden ist, und der Vorgang wird abgebrochen. | Kunden mit .NET Core 2.1, 2.2 wird [empfohlen](https://github.com/aspnet/Announcements/issues/287), stattdessen das Microsoft.AspNetCore.App-Metapaket zu verwenden. Aktivieren Sie außerdem „Interoperabel mit Application Insights SDK“ im Portal (siehe die obigen Anweisungen).|
|`AppAlreadyInstrumented:true` | Dieser Wert kann auch durch das Vorhandensein von Microsoft.ApplicationsInsights dll im Anwendungsordner aus einer früheren Bereitstellung verursacht werden. | Bereinigen Sie den App-Ordner, um sicherzustellen, dass diese DLLs entfernt werden. Überprüfen Sie sowohl das Verzeichnis „bin“ Ihrer lokalen App als auch das Verzeichnis „wwwroot“ im App Service. (Überprüfen des Verzeichnisses „wwwroot“ Ihrer App Service-Web-App: Erweiterte Tools (Kudu) > Debugging-Konsole > CMD > home\site\wwwroot). |
|`IKeyExists:false`|Dieser Wert gibt an, dass der Instrumentierungsschlüssel nicht in der App-Einstellung `APPINSIGHTS_INSTRUMENTATIONKEY` vorhanden ist. Mögliche Ursachen: Die Werte wurden möglicherweise versehentlich entfernt, nicht im Automatisierungsskript festgelegt usw. | Stellen Sie sicher, dass die Einstellung in den App Service-Anwendungseinstellungen vorhanden ist. |

## <a name="release-notes"></a>Anmerkungen zu diesem Release

Informationen zu den neuesten Updates und Fehlerbehebungen [finden Sie in den Versionshinweisen](web-app-extension-release-notes.md).

## <a name="next-steps"></a>Nächste Schritte
* [Ausführen des Profilers in Ihrer Live-App](./profiler.md)
* [Überwachen von Azure Functions mit Application Insights](monitor-functions.md).
* [Ermöglichen des Sendens von Azure-Diagnosedaten an Application Insights](../agents/diagnostics-extension-to-application-insights.md)
* [Überwachen von Dienstintegritätsmetriken](../data-platform.md) , um sicherzustellen, dass Ihr Dienst verfügbar und reaktionsfähig ist
* [Empfangen von Warnbenachrichtigungen](../alerts/alerts-overview.md) , wenn ein Vorgangsereignis auftritt oder Metriken einen Schwellenwert überschreiten.
* Verwenden von [Application Insights für JavaScript-Apps und Webseiten](javascript.md), um Clienttelemetriedaten von den Browsern zu erhalten, mit denen auf eine Webseite zugegriffen wird
* [Einrichten von Verfügbarkeitswebtests](monitor-web-app-availability.md), um benachrichtigt zu werden, wenn Ihre Website nicht verfügbar ist
