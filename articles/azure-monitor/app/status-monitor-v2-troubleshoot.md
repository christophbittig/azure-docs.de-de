---
title: 'Azure Application Insights-Agent: Problembehandlung und bekannte Probleme | Microsoft-Dokumentation'
description: Hier finden Sie Informationen zu den bekannten Problemen des Application Insights-Agents sowie Problembehandlungsbeispiele. Überwachen Sie die Websiteleistung ohne erneute Bereitstellung der Website. Funktioniert mit ASP.NET-Web-Apps, die lokal, auf virtuellen Computern oder in Azure gehostet werden.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: b353409ed1cacfabc8ac407e4ad17f4b1e167fe8
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2021
ms.locfileid: "114727443"
---
# <a name="troubleshooting-application-insights-agent-formerly-named-status-monitor-v2"></a>Behandeln von Problemen mit dem Application Insights-Agent (ehemals „Statusmonitor v2“)

Wenn Sie die Überwachung aktivieren, können Probleme auftreten, die die Datensammlung verhindern.
In diesem Artikel sind alle bekannten Probleme und Beispiele für die Problembehandlung aufgeführt.

## <a name="known-issues"></a>Bekannte Probleme

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>In Konflikt stehende DLLs im Verzeichnis „bin“ einer App

Wenn eine der folgenden DLLs im Verzeichnis „bin“ vorhanden ist, kann die Überwachung fehlschlagen:

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

Einige dieser DLLs werden in die App-Standardvorlagen von Visual Studio sogar dann einbezogen, wenn Ihre App sie nicht verwendet.
Sie können mithilfe von Problembehandlungstools symptomatisches Verhalten anzeigen:

- PerfView:
    ```
    ThreadID="7,500" 
    ProcessorNumber="0" 
    msg="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ExtVer="2.8.13.5972" 
    SubscriptionId="" 
    AppName="" 
    FormattedMessage="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ```

- IISReset und app load (ohne Telemetrie). Führen Sie eine Untersuchung mit Sysinternals (Handle.exe und ListDLLs.exe) durch:
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="powershell-versions"></a>PowerShell-Versionen
Dieses Produkt wurde mithilfe von PowerShell v5.1 geschrieben und getestet.
Dieses Modul ist nicht kompatibel mit den PowerShell-Versionen 6 oder 7.
Wir empfehlen die Verwendung von PowerShell v5.1 parallel zu neueren Versionen. Weitere Informationen finden Sie unter [Parallele Nutzung von PowerShell 7 mit Windows PowerShell 5.1](/powershell/scripting/install/migrating-from-windows-powershell-51-to-powershell-7#using-powershell-7-side-by-side-with-windows-powershell-51).

### <a name="conflict-with-iis-shared-configuration"></a>Konflikt mit IIS-Freigabekonfiguration

Wenn Sie über einen Cluster von Webservern verfügen, verwenden Sie möglicherweise eine [Freigabekonfiguration](/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
Das HttpModule kann in diese Freigabekonfiguration nicht eingefügt werden.
Führen Sie den Befehl „Enable“ auf jedem Webserver aus, um die DLL im GAC jedes Servers zu installieren.

Führen Sie nach Ausführung des Befehls „Enable“ die folgenden Schritte aus:
1. Wechseln Sie zum Verzeichnis für die Freigabekonfiguration, und suchen Sie die Datei „applicationHost.config“.
2. Fügen Sie im Modulabschnitt Ihrer Konfiguration folgende Zeile hinzu:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```

### <a name="iis-nested-applications"></a>Geschachtelte IIS-Anwendungen

In Version 1.0 werden geschachtelte Anwendungen in IIS nicht instrumentiert.

### <a name="advanced-sdk-configuration-isnt-available"></a>Die erweiterte SDK-Konfiguration ist nicht verfügbar.

Die SDK-Konfiguration ist in Version 1.0 für den Endbenutzer nicht verfügbar.

    
    
## <a name="troubleshooting"></a>Problembehandlung
    
### <a name="troubleshooting-powershell"></a>Problembehandlung bei PowerShell

#### <a name="determine-which-modules-are-available"></a>Ermitteln, welche Module verfügbar sind
Mit dem Befehl `Get-Module -ListAvailable` können Sie ermitteln, welche Module installiert sind.

#### <a name="import-a-module-into-the-current-session"></a>Importieren eines Moduls in die aktuelle Sitzung
Wenn ein Modul noch nicht in eine PowerShell-Sitzung geladen wurde, können Sie es mit dem Befehl `Import-Module <path to psd1>` manuell laden.


### <a name="troubleshooting-the-application-insights-agent-module"></a>Behandeln von Problemen mit dem Application Insights-Agent-Modul

#### <a name="list-the-commands-available-in-the-application-insights-agent-module"></a>Auflisten der im Application Insights-Agent-Modul verfügbaren Befehle
Führen Sie den Befehl `Get-Command -Module Az.ApplicationMonitor` aus, um die verfügbaren Befehle anzuzeigen:

```
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Disable-ApplicationInsightsMonitoring              0.4.0      Az.ApplicationMonitor
Cmdlet          Disable-InstrumentationEngine                      0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-ApplicationInsightsMonitoring               0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-InstrumentationEngine                       0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.4.0      Az.ApplicationMonitor
Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Start-ApplicationInsightsMonitoringTrace           0.4.0      Az.ApplicationMonitor
```

#### <a name="determine-the-current-version-of-the-application-insights-agent-module"></a>Bestimmen der aktuellen Version des Application Insights-Agent-Moduls
Führen Sie den Befehl `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` aus, um folgende Informationen zum Modul anzuzeigen:
   - PowerShell-Modulversion
   - Version des Application Insights SDK
   - Dateipfade des PowerShell-Moduls
    
Lesen Sie die [API-Referenz](status-monitor-v2-api-reference.md) mit einer ausführlichen Beschreibung zur Verwendung dieses Cmdlets.


### <a name="troubleshooting-running-processes"></a>Problembehandlung bei aktuell ausgeführten Prozessen

Sie können die Prozesse auf dem instrumentierten Computer überprüfen, um zu ermitteln, ob alle DLLs geladen und alle Umgebungsvariablen festgelegt wurden.
Wenn die Überwachung funktioniert, sollten mindestens 12 DLLS geladen worden sein.

* Verwenden Sie den Befehl `Get-ApplicationInsightsMonitoringStatus -InspectProcess` zum Überprüfen der DLLs.
* Verwenden Sie den Befehl `(Get-Process -id {PID}).StartInfo.EnvironmentVariables`, um die Umgebungsvariablen zu überprüfen. Im Folgenden finden Sie die Umgebungsvariablen, die im Workerprozess oder dotnet core-Prozess festgelegt werden:

```
COR_ENABLE_PROFILING=1
COR_PROFILER={324F817A-7420-4E6D-B3C1-143FBED6D855}
COR_PROFILER_PATH_32=Path to MicrosoftInstrumentationEngine_x86.dll
COR_PROFILER_PATH_64=Path to MicrosoftInstrumentationEngine_x64.dll
MicrosoftInstrumentationEngine_Host={CA487940-57D2-10BF-11B2-A3AD5A13CBC0}
MicrosoftInstrumentationEngine_HostPath_32=Path to Microsoft.ApplicationInsights.ExtensionsHost_x86.dll
MicrosoftInstrumentationEngine_HostPath_64=Path to Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
MicrosoftInstrumentationEngine_ConfigPath32_Private=Path to Microsoft.InstrumentationEngine.Extensions.config
MicrosoftInstrumentationEngine_ConfigPath64_Private=Path to Microsoft.InstrumentationEngine.Extensions.config
MicrosoftAppInsights_ManagedHttpModulePath=Path to Microsoft.ApplicationInsights.RedfieldIISModule.dll
MicrosoftAppInsights_ManagedHttpModuleType=Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule
ASPNETCORE_HOSTINGSTARTUPASSEMBLIES=Microsoft.ApplicationInsights.StartupBootstrapper
DOTNET_STARTUP_HOOKS=Path to Microsoft.ApplicationInsights.StartupHook.dll
```

Lesen Sie die [API-Referenz](status-monitor-v2-api-reference.md) mit einer ausführlichen Beschreibung zur Verwendung dieses Cmdlets.


### <a name="collect-etw-logs-by-using-perfview"></a>Sammeln von ETW-Protokollen mit PerfView

#### <a name="setup"></a>Einrichten

1. Laden Sie PerfView.exe und PerfView64.exe aus [GitHub](https://github.com/Microsoft/perfview/releases) herunter.
2. Starten Sie PerfView64.exe.
3. Erweitern Sie **Erweiterte Optionen**.
4. Aktivieren Sie diese Kontrollkästchen:
    - **Zip**
    - **Merge** (Zusammenführen)
    - **.NET-Symbolsammlung**
5. Legen Sie folgende **Zusätzliche Anbieter** fest: `61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,252e28f4-43f9-5771-197a-e8c7e750a984,f9c04365-1d1f-5177-1cdc-a0b0554b6903`


#### <a name="collecting-logs"></a>Sammeln von Protokollen

1. Führen Sie in einer Befehlskonsole mit Administratorrechten den Befehl `iisreset /stop` aus, um IIS und alle Web-Apps zu deaktivieren.
2. Wählen Sie in PerfView **Sammlung starten** aus.
3. Führen Sie in einer Befehlskonsole mit Administratorrechten den Befehl `iisreset /start` aus, um IIS zu starten.
4. Versuchen Sie, zu Ihrer App zu navigieren.
5. Nachdem Ihre App geladen wurde, kehren Sie zu PerfView zurück, und wählen Sie **Sammlung beenden** aus.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich in der [API-Referenz](status-monitor-v2-overview.md#powershell-api-reference) über Parameter, die Sie möglicherweise übersehen haben.
