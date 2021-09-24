---
title: Behandeln von Problemen mit Azure Application Insights Profiler
description: Dieser Artikel enthält Schritte zur Problembehandlung sowie Informationen, um Entwickler bei der Aktivierung und Verwendung von Application Insights Profiler zu unterstützen.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: f4d8a3df98730b5bdcb6e32d3f3c51ad9d01bf3a
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867255"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Behandeln von Problemen mit dem Aktivieren oder Anzeigen von Application Insights Profiler

## <a name="general-troubleshooting"></a><a id="troubleshooting"></a>Allgemeine Problembehandlung

### <a name="make-sure-youre-using-the-appropriate-profiler-endpoint"></a>Stellen Sie sicher, dass Sie den richtigen Profiler-Endpunkt verwenden.

Derzeit sind [Azure Government](../../azure-government/compare-azure-government-global-azure.md#application-insights) und [Azure China](/azure/china/resources-developer-guide) die einzigen Regionen, für die Endpunktänderungen erforderlich sind.

|App-Einstellung    | US Government-Cloud | China-Cloud |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | `https://profiler.monitor.azure.us`    | `https://profiler.monitor.azure.cn` |
|ApplicationInsightsProfilerEndpoint | `https://dc.applicationinsights.us` | `https://dc.applicationinsights.azure.cn` |

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>Profile werden nur bei vorhandenen Anforderungen für die Anwendung während der Profiler-Ausführung hochgeladen.

Azure Application Insights Profiler sammelt jede Stunde zwei Minuten lang Daten. Außerdem werden Daten gesammelt, wenn Sie im Bereich **Application Insights Profiler konfigurieren** die Schaltfläche **Jetzt Profil erstellen** auswählen.

> [!NOTE]
> Die Profilerstellungsdaten werden nur hochgeladen, wenn sie an eine Anforderung angefügt werden können, die während der Profiler-Ausführung erfolgt ist. 

Profiler schreibt Meldungen zur Ablaufverfolgung und benutzerdefinierten Ereignissen in Ihre Application Insights-Ressource. Anhand dieser Ereignisse können Sie feststellen, wie Profiler ausgeführt wird:

1. Suchen Sie nach Meldungen zur Ablaufverfolgung, die von Profiler an Ihre Application Insights-Ressource gesendet wurden. Anhand dieser Suchzeichenfolge können Sie die relevanten Daten suchen:

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    Die folgende Abbildung zeigt zwei Beispiele für Suchvorgänge aus zwei KI-Ressourcen: 
    
   * Auf der linken Seite erhält die Anwendung keine Anforderungen, während Profiler ausgeführt wird. Die Meldung erläutert, dass der Upload aufgrund von Inaktivität abgebrochen wurde. 

   * Auf der rechten Seite wurde Profiler gestartet und hat benutzerdefinierte Ereignisse gesendet, wenn während der Profiler-Ausführung Anforderungen erkannt wurden. Wenn das benutzerdefinierte Ereignis `ServiceProfilerSample` angezeigt wird, bedeutet dies, dass ein Profil aufgezeichnet wurde und im Bereich **Leistung** von Application Insights verfügbar ist.

     Wenn keine Datensätze angezeigt werden, wird Profiler nicht ausgeführt. Informationen zur Problembehandlung finden Sie in den entsprechenden Abschnitten für Ihren spezifischen App-Typ weiter unten in diesem Artikel.  

     ![Suchen nach Profiler-Telemetriedaten][profiler-search-telemetry]

### <a name="other-things-to-check"></a>Weitere erforderliche Überprüfungen
* Vergewissern Sie sich, dass Ihre App unter .NET Framework 4.6 ausgeführt wird.
* Wenn Ihre Web-App eine ASP.NET Core-Anwendung ist, muss sie mindestens ASP.NET Core 2.0 ausführen.
* Falls die Daten, die Sie anzeigen möchten, bereits mehrere Wochen alt sind, schränken Sie Ihren Zeitfilter ein, und versuchen Sie es noch mal. Ablaufverfolgungen werden nach sieben Tagen gelöscht.
* Stellen Sie sicher, dass der Zugriff auf https://gateway.azureserviceprofiler.net nicht durch Proxys oder eine Firewall blockiert wird.
* Profiler wird in App Service-Plänen vom Typ „Free“ oder „Shared“ nicht unterstützt. Wenn Sie einen dieser Pläne verwenden, sollten Sie eine Skalierung auf einen der „Basic“-Pläne durchführen. Profiler sollte dann funktionieren.

### <a name="double-counting-in-parallel-threads"></a><a id="double-counting"></a>Doppelte Erfassung in parallelen Threads

Manchmal übersteigt die Gesamtzeitmetrik im Stapel-Viewer die Dauer der Anforderung.

Diese Situation kann eintreten, wenn einer Anforderung mehrere parallele Threads zugeordnet sind. In diesem Fall übersteigt die Gesamtzeit der Threads die verstrichene Zeit.

Ein Thread wartet möglicherweise auf den Abschluss eines anderen Threads. Der Viewer versucht, solche Fälle zu erkennen und die nicht relevante Wartezeit zu ignorieren. Im Zweifelsfall werden jedoch eher zu viele Informationen angezeigt, um zu vermeiden, dass womöglich entscheidende Informationen ausgelassen werden.

Wenn Ihnen in Ihren Ablaufverfolgungen parallele Threads auffallen, bestimmen Sie die wartenden Threads, um den kritischen Pfad für die Anforderung ermitteln zu können.

In der Regel wartet der Thread, der schnell in einen Wartezustand wechselt, einfach auf den Abschluss der anderen Threads. Konzentrieren Sie sich auf die anderen Threads, und ignorieren Sie die Zeit in den wartenden Threads.

### <a name="error-report-in-the-profile-viewer"></a>Fehlerbericht im Profil-Viewer
Senden Sie ein Supportticket über das Portal. Geben Sie dabei die Korrelations-ID aus der Fehlermeldung an.

## <a name="troubleshoot-profiler-on-azure-app-service"></a>Problembehandlung für Profiler in Azure App Service

Damit Profiler ordnungsgemäß funktioniert, müssen die folgenden Voraussetzungen erfüllt sein:
* Für die Web-App muss mindestens ein App Service-Plan mit Basic-Tarif verwendet werden.
* Für Ihre Web-App muss Application Insights aktiviert sein.
* Ihre Web-App muss die folgenden App-Einstellungen aufweisen:

    |App-Einstellung    | Wert    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey für Ihre Application Insights-Ressource    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |


* Der Webauftrag **ApplicationInsightsProfiler3** muss ausgeführt werden. So überprüfen Sie den Webauftrag:
   1. Wechseln Sie zu [Kudu](/archive/blogs/cdndevs/the-kudu-debug-console-azure-websites-best-kept-secret).
   1. Wählen Sie im Menü **Tools** das **WebJobs-Dashboard** aus.  
      Der Bereich **WebJobs** wird geöffnet. 
   
      ![Screenshot: Bereich „WebJobs“, in dem der Name, der Status und die Uhrzeit der letzten Ausführung von Jobs angezeigt wird][profiler-webjob]   
   
   1. Klicken Sie zum Anzeigen der WebJob-Details einschließlich des Protokolls auf den Link **ApplicationInsightsProfiler3**.  
     Der Bereich **Continuous WebJob Details** (Details des fortlaufenden WebJobs) wird geöffnet.

      ![Screenshot: Bereich „Continuous WebJob Details“ (Details des fortlaufenden WebJobs).][profiler-webjob-log]

Wenn Profiler nicht funktioniert, haben Sie die Möglichkeit, das Protokoll herunterzuladen und unter serviceprofilerhelp@microsoft.com an unser Team zu senden, um Unterstützung zu erhalten.

### <a name="check-the-diagnostic-services-site-extension-status-page"></a>Überprüfen der Seite „Status“ der Websiteerweiterung für Diagnosedienste
Wenn Profiler über den Bereich [Application Insights](profiler.md) im Portal aktiviert wurde, erfolgte die Aktivierung über die Websiteerweiterung für Diagnosedienste.

> [!NOTE]
> Die codefreie Installation von Application Insights Profiler folgt der Richtlinie zur .NET Core-Unterstützung.
> Weitere Informationen zu unterstützten Runtimes finden Sie in der [.NET Core-Unterstützungsrichtlinie](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

Sie können die Statusseite dieser Erweiterung unter der folgenden URL überprüfen: `https://{site-name}.scm.azurewebsites.net/DiagnosticServices`.

> [!NOTE]
> Die Domäne des Links zur Statusseite hängt von der Cloud ab.
Diese Domäne ist mit der Kudu-Verwaltungswebsite für App Service identisch.

Auf dieser Statusseite wird der Installationsstatus von Profiler und den Snapshot Collector-Agents angezeigt. Wenn ein unerwarteter Fehler aufgetreten ist, wird er zusammen mit Vorschlägen zur Behebung angezeigt.

Sie können die Kudu-Verwaltungswebsite für App Service dazu verwenden, die Basis-URL dieser Statusseite abzurufen:
1. Öffnen Sie im Azure-Portal Ihre App Service-Anwendung.
2. Wählen Sie **Erweiterte Tools** aus, oder suchen Sie nach **Kudu**.
3. Klicken Sie auf **Starten**.
4. Nachdem Sie zur Kudu-Verwaltungswebsite navigiert sind, fügen Sie an die URL **die Zeichenfolge `/DiagnosticServices` an und drücken dann die EINGABETASTE**.
 Das Ergebnis sieht wie folgt aus: `https://<kudu-url>/DiagnosticServices`.

Damit zeigen Sie eine Statusseite ähnlich der folgenden an: ![Statusseite der Diagnosedienste](./media/diagnostic-services-site-extension/status-page.png)
    
### <a name="manual-installation"></a>Manuelle Installation

Wenn Sie Profiler konfigurieren, werden an den Einstellungen der Web-App Aktualisierungen vorgenommen. Sie können die Updates manuell anwenden, wenn dies für Ihre Umgebung erforderlich ist. Beispiel: Ihre Anwendung wird in einer Web-Apps-Umgebung für Power Apps ausgeführt. So wenden Sie Updates manuell an:

1. Öffnen Sie im **Steuerungsbereich der Web-App** die Option **Einstellungen**.

1. Legen Sie die **.NET Framework-Version** auf **v4.6** fest.

1. Legen Sie **Immer bereit** auf **Ein** fest.
1. Erstellen Sie die folgenden App-Einstellungen:

    |App-Einstellung    | Wert    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey für Ihre Application Insights-Ressource    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |

### <a name="too-many-active-profiling-sessions"></a>Zu viele aktive Profilerstellungssitzungen

Sie können Profiler für maximal vier Web-Apps aktivieren, die im selben Diensttarif ausgeführt werden. Wenn Sie mehr als vier Web-Apps verwenden, löst Profiler möglicherweise eine *Microsoft.ServiceProfiler.Exceptions.TooManyETWSessionException* aus. Um dieses Problem zu beheben, verschieben Sie einige Web-Apps in einen anderen Diensttarif.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootapp_datajobs"></a>Bereitstellungsfehler: Verzeichnis nicht leer 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

Wenn Sie Ihre Web-App erneut für eine Web-Apps-Ressource bereitstellen und Profiler aktiviert ist, wird möglicherweise eine Meldung wie die folgende angezeigt:

*Verzeichnis nicht leer „D:\\home\\site\\wwwroot\\App_Data\\jobs“*

Dieser Fehler tritt auf, wenn Sie Web Deploy über Skripts oder Azure Pipelines ausführen. Als Lösung fügen Sie die folgenden Bereitstellungsparameter dem Web Deploy-Task hinzu:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Diese Parameter löschen den Ordner, der von Application Insights Profiler verwendet wird, und heben die Sperre der erneuten Bereitstellung auf. Sie haben keine Auswirkungen auf die Profiler-Instanz, die gerade ausgeführt wird.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Wie kann ich feststellen, ob Application Insights Profiler ausgeführt wird?

Profiler wird als fortlaufender Webauftrag in der Web-App ausgeführt. Sie können die Web-App-Ressource im [Azure-Portal](https://portal.azure.com) öffnen. Überprüfen Sie im Bereich **WebJobs** den Status von **ApplicationInsightsProfiler**. Wenn er nicht ausgeführt wird, öffnen Sie **Protokolle**, um weitere Informationen zu erhalten.

## <a name="troubleshoot-vms-and-cloud-services"></a>Problembehandlung bei VMs und Cloud Services

>**Der Fehler im Profiler, der in WAD für Cloud Services enthalten ist, wurde behoben.** Die neueste Version von WAD (1.12.2.0) für Cloud Services funktioniert mit allen neueren Versionen des App Insights-SDK. Cloud Service-Hosts führen ein automatisches Upgrade für WAD durch, doch erfolgt dies nicht unmittelbar. Soll ein Upgrade erzwungen werden, können Sie den Dienst erneut bereitstellen oder den Knoten neu starten.

Anhand der folgenden Schritte können Sie feststellen, ob Profiler von der Azure-Diagnose richtig konfiguriert wurde: 
1. Vergewissern Sie sich, dass der Inhalt der bereitgestellten Azure-Diagnose-Konfiguration Ihren Erwartungen entspricht. 

1. Zweitens: Vergewissern Sie sich, dass die Azure-Diagnose in der Profiler-Befehlszeile den richtigen iKey übergibt. 

1. Drittens: Prüfen Sie in der Profiler-Protokolldatei, ob Profiler ausgeführt wurde, aber einen Fehler zurückgegeben hat. 

Überprüfen der Einstellungen, die zum Konfigurieren der Azure-Diagnose verwendet wurden:

1. Melden Sie sich beim virtuellen Computer (VM) an, und öffnen Sie die Protokolldatei an diesem Speicherort. Die Plug-in-Version ist auf Ihrem Computer möglicherweise neuer.
    
    Für VMs:
    ```
    c:\WindowsAzure\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log
    ```
    
    Für Cloud Services:
    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log  
    ```

1. Suchen Sie in der Datei nach der Zeichenfolge **WadCfg**, um die Einstellungen zu finden, die zum Konfigurieren der Azure-Diagnose an den virtuellen Computer übergeben wurden. Sie können überprüfen, ob der von der Profiler-Senke verwendete iKey richtig ist.

1. Überprüfen Sie die Befehlszeile, mit der Profiler gestartet wird. Die zum Starten von Profiler verwendeten Argumente befinden sich in der folgenden Datei. (Das Laufwerk kann „c:“ oder „d:“ sein, und das Verzeichnis ist möglicherweise ausgeblendet.)

    Für VMs:
    ```
    C:\ProgramData\ApplicationInsightsProfiler\config.json
    ```
    
    Für Cloud Services:
    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. Stellen Sie sicher, dass der iKey in der Profiler-Befehlszeile richtig ist. 

1. Überprüfen Sie die Profiler-Protokolldatei namens **BootstrapN.log** anhand des Pfads in der vorherigen Datei *config.json*. Darin werden die Debuginformationen angezeigt, welche die von Profiler verwendeten Einstellungen angeben. Außerdem werden Status- und Fehlermeldungen von Profiler angezeigt.  

    Für VMs befindet sich die Datei hier:
    ```
    C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.17.0.6\ApplicationInsightsProfiler
    ```

    Für Cloud Services:
    ```
    C:\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.17.0.6\ApplicationInsightsProfiler
    ```

    Wenn Profiler ausgeführt wird, während Ihre Anwendung Anforderungen empfängt, wird die folgende Meldung angezeigt: *Aktivität aus iKey erkannt*. 

    Wenn die Ablaufverfolgung hochgeladen wird, wird die folgende Meldung angezeigt: *Start to upload trace* (Upload der Ablaufverfolgung starten). 


## <a name="edit-network-proxy-or-firewall-rules"></a>Bearbeiten von Netzwerkproxy- oder Firewallregeln

Wenn Ihre Anwendung über einen Proxy oder über eine Firewall mit dem Internet verbunden ist, müssen Sie eventuell die Regeln aktualisieren, damit sie mit dem Profiler-Dienst kommunizieren kann.

Die von Application Insights Profiler verwendeten IP-Adressen sind im Azure Monitor-Diensttag enthalten. Weitere Informationen finden Sie in der [Dokumentation zu Diensttags](../../virtual-network/service-tags-overview.md).


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png