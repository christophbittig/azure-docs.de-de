---
title: Problembehandlung für den Azure Application Insights-Momentaufnahmedebugger
description: Dieser Artikel enthält Schritte zur Problembehandlung sowie Informationen, um Entwickler bei der Aktivierung und Verwendung des Application Insights-Momentaufnahmedebuggers zu unterstützen.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 9ae4a7d070419aae974742802ec5b6b3ae958c87
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355528"
---
# <a name="troubleshoot-problems-enabling-application-insights-snapshot-debugger-or-viewing-snapshots"></a><a id="troubleshooting"></a> Behandeln von Problemen beim Aktivieren des Application Insights-Momentaufnahmedebuggers oder Anzeigen von Momentaufnahmen
Wenn Sie den Application Insights-Momentaufnahmedebugger für Ihre Anwendung aktiviert haben, aber keine Momentaufnahmen für Ausnahmen angezeigt werden, können Sie diese Anweisungen zur Problembehandlung verwenden.

Es kann viele verschiedene Gründe geben, warum keine Momentaufnahmen generiert werden. Sie können zunächst die Integritätsprüfung für Momentaufnahmen ausführen, um einige der möglichen Ursachen zu ermitteln.

## <a name="make-sure-youre-using-the-appropriate-snapshot-debugger-endpoint"></a>Stellen Sie sicher, dass Sie den richtigen Endpunkt für den Momentaufnahmedebugger verwenden.

Derzeit sind [Azure Government](../../azure-government/compare-azure-government-global-azure.md#application-insights) und [Azure China](/azure/china/resources-developer-guide) die einzigen Regionen, für die Endpunktänderungen erforderlich sind.

Für App Service und Anwendungen, die das Application Insights SDK verwenden, müssen Sie die Verbindungszeichenfolge mithilfe den unterstützten Überschreibungen für den Momentaufnahmedebugger aktualisieren, wie unten gezeigt:

|Eigenschaft mit der Verbindungszeichenfolge    | US Government-Cloud | China-Cloud |   
|---------------|---------------------|-------------|
|SnapshotEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

Weitere Informationen zu anderen Verbindungsüberschreibungen finden Sie in der [Application Insights-Dokumentation](./sdk-connection-string.md?tabs=net#connection-string-with-explicit-endpoint-overrides).

Für Funktions-Apps müssen Sie die Datei `host.json` mithilfe der unterstützten Überschreibungen wie unten aktualisieren:

|Eigenschaft    | US Government-Cloud | China-Cloud |   
|---------------|---------------------|-------------|
|AgentEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

Im Folgenden finden Sie ein Beispiel für die Datei `host.json`, die mit dem Agent-Endpunkt der US Government-Cloud aktualisiert wurde:
```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingExcludedTypes": "Request",
      "samplingSettings": {
        "isEnabled": true
      },
      "snapshotConfiguration": {
        "isEnabled": true,
        "agentEndpoint": "https://snapshot.monitor.azure.us"
      }
    }
  }
}
```

## <a name="use-the-snapshot-health-check"></a>Verwenden der Integritätsprüfung für Momentaufnahmen
Einige verbreitete Probleme führen dazu, dass „Debugmomentaufnahme öffnen“ nicht angezeigt wird. Beispiele wären etwa die Verwendung eines veralteten Snapshot Collectors, das Erreichen des täglichen Uploadlimits oder ein zeitaufwendiger Uploadvorgang für die Momentaufnahme. Verwenden Sie die Integritätsprüfung für Momentaufnahmen, um verbreitete Probleme zu behandeln.

Der Ausnahmenbereich der End-to-End-Überwachungsansicht enthält einen Link, über den Sie zur Integritätsprüfung für Momentaufnahmen gelangen.

![Aufrufen der Integritätsprüfung für Momentaufnahmen](./media/snapshot-debugger/enter-snapshot-health-check.png)

Die interaktive, chatähnliche Oberfläche sucht nach verbreiteten Problemen und unterstützt Sie bei der Behebung.

![Integritätsprüfung](./media/snapshot-debugger/healthcheck.png)

Sollte sich das Problem dadurch nicht beheben lassen, lesen Sie die folgenden Schritte zur manuellen Problembehandlung:

## <a name="verify-the-instrumentation-key"></a>Überprüfen des Instrumentierungsschlüssels

Vergewissern Sie sich, dass Sie in Ihrer veröffentlichten Anwendung den richtigen Instrumentierungsschlüssel verwenden. Der Instrumentierungsschlüssel wird in der Regel aus der Datei „ApplicationInsights.config“ gelesen. Vergewissern Sie sich, dass der Wert dem Instrumentierungsschlüssel für die im Portal angezeigte Application Insights-Ressource entspricht.

## <a name="check-tlsssl-client-settings-aspnet"></a><a id="SSL"></a>Überprüfen der TLS/SSL-Clienteinstellungen (ASP.NET)

Wenn Sie auf einer VM eine ASP.NET-Anwendung haben, die in Azure App Service oder IIS gehostet wird, kann Ihre Anwendung aufgrund eines fehlenden SSL-Sicherheitsprotokolls möglicherweise keine Verbindung mit dem Dienst „Momentaufnahmedebugger“ herstellen.

[Der Endpunkt von Momentaufnahmedebugger erfordert die TLS-Version 1.2](snapshot-debugger-upgrade.md?toc=/azure/azure-monitor/toc.json). Die Gruppe der SSL-Sicherheitsprotokolle ist eine der Tücken, die sich durch den Wert „httpRuntime targetFramework“ im Abschnitt „system.web“ der Datei „web.config“ ergibt. Wenn „httpRuntime targetFramework“ 4.5.2 oder niedriger ist, ist TLS 1.2 nicht standardmäßig inbegriffen.

> [!NOTE]
> Der Wert von „httpRuntime targetFramework“ ist unabhängig von dem beim Erstellen Ihrer Anwendung verwendeten Zielframework.

Um die Einstellung zu überprüfen, öffnen Sie die Datei „web.config“, und wechseln Sie zum Abschnitt „system.web“. Vergewissern Sie sich, dass `targetFramework` für `httpRuntime` auf 4.6 oder höher festgelegt ist.

   ```xml
   <system.web>
      ...
      <httpRuntime targetFramework="4.7.2" />
      ...
   </system.web>
   ```

> [!NOTE]
> Das Ändern des Werts „httpRuntime targetFramework“ wirkt sich auf das Verhalten Ihrer Anwendung zur Laufzeit aus und kann zu anderen, geringfügigen Verhaltensänderungen führen. Testen Sie Ihre Anwendung nach dieser Änderung unbedingt gründlich. Eine vollständige Liste der Kompatibilitätsänderungen finden Sie unter [Änderungen durch Neuausrichtung](/dotnet/framework/migration-guide/application-compatibility#retargeting-changes).

> [!NOTE]
> Wenn „targetFramework“ 4.7 oder höher ist, bestimmt Windows die verfügbaren Protokolle. In Azure App Service ist TLS 1.2 verfügbar. Wenn Sie jedoch Ihren eigenen virtuellen Computer verwenden, müssen Sie möglicherweise TLS 1.2 im Betriebssystem aktivieren.

## <a name="preview-versions-of-net-core"></a>Vorschauversionen von .NET Core
Wenn Sie eine Vorschauversion von .NET Core verwenden oder Ihre Anwendung direkt oder indirekt über eine abhängige Assembly auf das Application Insights SDK verweist, befolgen Sie die Anweisungen zum [Aktivieren des Momentaufnahmedebuggers für andere Umgebungen](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json).

## <a name="check-the-diagnostic-services-site-extension-status-page"></a>Überprüfen der Seite „Status“ der Websiteerweiterung für Diagnosedienste
Wenn der Momentaufnahmedebugger über den Bereich [Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json) im Portal aktiviert wurde, erfolgte die Aktivierung über die Websiteerweiterung für Diagnosedienste.

> [!NOTE]
> Die codefreie Installation des Application Insights-Momentaufnahmedebuggers folgt der Richtlinie zur .NET Core-Unterstützung.
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

## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Upgraden auf die neueste Version des NuGet-Pakets
Abhängig von der Aktivierung des Momentaufnahmedebuggers gibt es folgende Optionen:

* Wenn der Momentaufnahmedebugger über den [Application Insights-Bereich im Portal](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json) aktiviert wurde, sollte in Ihrer Anwendung bereits das aktuellste NuGet-Paket ausgeführt werden.

* Wurde der Momentaufnahmedebugger durch Einbinden des NuGet-Pakets [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) aktiviert, verwenden Sie den NuGet-Paket-Manager in Visual Studio, um sicherzustellen, dass Sie die neueste Version von „Microsoft.ApplicationInsights.SnapshotCollector“ verwenden.

Informationen zu den neuesten Updates und Fehlerbehebungen [finden Sie in den Versionshinweisen](./snapshot-collector-release-notes.md).

## <a name="check-the-uploader-logs"></a>Überprüfen der Uploaderprotokolle

Nachdem eine Momentaufnahme erstellt wurde, wird eine Minidumpdatei (DMP) auf dem Datenträger erstellt. Diese Minidumpdatei wird durch einen separaten Uploaderprozess erstellt und zusammen mit allen dazugehörigen PDB-Dateien in den Speicher des Application Insights-Momentaufnahmedebuggers hochgeladen. Nachdem die Minidumpdatei erfolgreich hochgeladen wurde, wird sie vom Datenträger gelöscht. Die Protokolldateien für den Uploaderprozess bleiben auf dem Datenträger erhalten. In einer App Service-Umgebung finden Sie diese Protokolle unter `D:\Home\LogFiles`. Verwenden Sie die Verwaltungswebsite von Kudu für App Service, um nach diesen Protokolldateien zu suchen.

1. Öffnen Sie im Azure-Portal Ihre App Service-Anwendung.
2. Wählen Sie **Erweiterte Tools** aus, oder suchen Sie nach **Kudu**.
3. Klicken Sie auf **Starten**.
4. Wählen Sie im Dropdown-Listenfeld **Debugging-Konsole** die Option **CMD** aus.
5. Wählen Sie **LogFiles** aus.

Daraufhin sollte mindestens eine Datei mit einem Namen angezeigt werden, der mit `Uploader_` oder `SnapshotUploader_` beginnt und die Erweiterung `.log` aufweist. Wählen Sie das entsprechende Symbol aus, um Protokolldateien herunterzuladen oder in einem Browser zu öffnen.
Der Dateiname enthält ein eindeutiges Suffix, mit dem die App Service-Instanz identifiziert wird. Wenn Ihre App Service-Instanz auf mehreren Computern gehostet wird, werden separate Protokolldateien für jeden Computer erstellt. Wenn der Uploader eine neue Minidumpdatei erkennt, wird diese in der Protokolldatei erfasst. Hier ist ein Beispiel für eine erfolgreiche Momentaufnahme und einen Upload angegeben:

```
SnapshotUploader.exe Information: 0 : Received Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Creating minidump from Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Dump placeholder file created: 139e411a23934dc0b9ea08a626db16c5.dm_
    DateTime=2018-03-09T01:42:41.8728496Z
SnapshotUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7525022Z
SnapshotUploader.exe Information: 0 : Successfully wrote minidump to D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 214.42 MB (uncompressed)
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Upload successful. Compressed size 86.56 MB
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2018-03-09T01:42:59.6809606Z
SnapshotUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2018-03-09T01:42:59.8059929Z
SnapshotUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:59.8530649Z
```

> [!NOTE]
> Das obige Beispiel stammt aus Version 1.2.0 des NuGet-Pakets „Microsoft.ApplicationInsights.SnapshotCollector“. In früheren Versionen hatte der Uploader-Prozess die Bezeichnung `MinidumpUploader.exe`, und das Protokoll enthielt weniger Details.

Im vorherigen Beispiel lautet der Instrumentierungsschlüssel `c12a605e73c44346a984e00000000000`. Dieser Wert sollte dem Instrumentierungsschlüssel für Ihre Anwendung entsprechen.
Die Minidumpdatei ist einer Momentaufnahme mit der ID `139e411a23934dc0b9ea08a626db16c5` zugeordnet. Sie können diese ID später verwenden, um nach dem zugehörigen Ausnahmedatensatz in Application Insights Analytics zu suchen.

Der Uploader sucht alle 15 Minuten nach neuen PDB-Dateien. Hier sehen Sie ein Beispiel:

```
SnapshotUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Scanning D:\home\site\wwwroot for local PDBs.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2018-03-09T01:47:19.4614027Z
SnapshotUploader.exe Information: 0 : Deleted PDB scan marker : D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\6368.pdbscan
    DateTime=2018-03-09T01:47:19.4614027Z
```

Bei Anwendungen, die _nicht_ in App Service gehostet werden, befinden sich die Uploaderprotokolle im selben Ordner wie die Minidumpdateien: `%TEMP%\Dumps\<ikey>` (`<ikey>` steht hierbei für Ihren Instrumentierungsschlüssel).

## <a name="troubleshooting-cloud-services"></a>Problembehandlung bei Cloud Services
In Cloud Services ist der standardmäßige temporäre Ordner möglicherweise zu klein, um die Minidump-Dateien zu speichern, was dazu führt, dass Momentaufnahmen verloren gehen.

Der erforderliche Speicherplatz hängt vom gesamten Arbeitssatz Ihrer Anwendung und der Anzahl gleichzeitiger Momentaufnahmen ab.

Der Arbeitssatz einer 32-Bit-ASP.NET-Webrolle liegt in der Regel zwischen 200 MB und 500 MB. Lassen Sie mindestens zwei gleichzeitige Momentaufnahmen zu.

Wenn Ihre Anwendung beispielsweise 1 GB des gesamten Arbeitssatzes verwendet, sollten Sie sicherstellen, dass ein Speicherplatz von mindestens 2 GB zum Speichern von Momentaufnahmen zur Verfügung steht.

Führen Sie die folgenden Schritte aus, um Ihre Clouddienstrolle mit einer dedizierten lokalen Ressource für Momentaufnahmen zu konfigurieren.

1. Fügen Sie Ihrem Clouddienst eine neue lokale Ressource hinzu, indem Sie die Clouddienst-Definitionsdatei (.csdef) bearbeiten. Im folgenden Beispiel wird eine Ressource namens `SnapshotStore` mit einer Größe von 5 GB definiert.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Ändern Sie den Startcode Ihrer Rolle, um eine Umgebungsvariable hinzuzufügen, die auf die lokale Ressource `SnapshotStore` zeigt. Für Workerrollen sollte der Code der `OnStart`-Methode Ihrer Rolle hinzugefügt werden:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   Für Webrollen (ASP.NET) sollte der Code der `Application_Start`-Methode Ihrer Webanwendung hinzugefügt werden:
   ```csharp
   using Microsoft.WindowsAzure.ServiceRuntime;
   using System;

   namespace MyWebRoleApp
   {
       public class MyMvcApplication : System.Web.HttpApplication
       {
          protected void Application_Start()
          {
             Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
             // TODO: The rest of your application startup code
          }
       }
   }
   ```

3. Aktualisieren Sie die Datei „ApplicationInsights.config“ Ihrer Rolle, um den von `SnapshotCollector` verwendeten Speicherort des temporären Ordners zu überschreiben.
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

## <a name="overriding-the-shadow-copy-folder"></a>Überschreiben des Momentaufnahmeordners

Wenn der Snapshot Collector gestartet wird, versucht er, einen Ordner auf dem Datenträger zu finden, der für die Ausführung des Snapshot Uploader-Prozesses geeignet ist. Der ausgewählte Ordner wird als Momentaufnahmeordner bezeichnet.

Der Snapshot Collector überprüft einige bekannte Speicherorte und stellt dabei sicher, dass er über die Berechtigungen zum Kopieren der Binärdateien für den Snapshot Uploader verfügt. Folgende Umgebungsvariablen werden verwendet:
- Fabric_Folder_App_Temp
- LOCALAPPDATA
- APPDATA
- TEMP

Kann kein geeigneter Ordner gefunden werden, meldet Snapshot Collector einen Fehler, dass _kein geeigneter Momentaufnahmeordner gefunden wurde_.

Bei einem fehlerhaften Kopiervorgang meldet Snapshot Collector den Fehler `ShadowCopyFailed`.

Wenn der Uploader nicht gestartet werden kann, meldet Snapshot Collector den Fehler `UploaderCannotStartFromShadowCopy`. Der Text der Meldung enthält häufig `System.UnauthorizedAccessException`. Dieser Fehler tritt gewöhnlich auf, wenn die Anwendung unter einem Konto mit eingeschränkten Berechtigungen ausgeführt wird. Das Konto verfügt über die Berechtigung zum Schreiben in den Momentaufnahmeordner, aber es hat keine Berechtigung zum Ausführen von Code.

Da diese Fehler in der Regel während des Starts auftreten, folgt darauf meist der Fehler `ExceptionDuringConnect`, der aussagt, dass _der Uploader nicht gestartet werden konnte_.

Um diese Fehler zu umgehen, können Sie den Momentaufnahmeordner manuell über die Konfigurationsoption `ShadowCopyFolder` angeben. Sie können beispielsweise „ApplicationInsights.config“ verwenden:

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

Oder Sie können „appsettings.json“ mit einer .NET Core-Anwendung verwenden:

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "ShadowCopyFolder": "D:\\SnapshotUploader"
     }
   }
   ```

## <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Suchen nach Ausnahmen mit Momentaufnahmen über die Application Insights-Suche

Wenn eine Momentaufnahme erstellt wird, wird die auslösende Ausnahme mit einer Momentaufnahme-ID gekennzeichnet. Diese Momentaufnahme-ID ist als benutzerdefinierte Eigenschaft enthalten, wenn die Ausnahme an Application Insights gemeldet wird. In Application Insights können Sie über die **Suche** nach sämtlichen Datensätzen mit der benutzerdefinierten Eigenschaft `ai.snapshot.id` suchen.

1. Navigieren Sie im Azure-Portal zu Ihrer Application Insights-Ressource.
2. Klicken Sie auf **Suchen**.
3. Geben Sie in das Textfeld „Suchen“ `ai.snapshot.id` ein, und drücken Sie die Eingabetaste.

![Suchen nach Telemetrie im Portal mit einer Momentaufnahme-ID](./media/snapshot-debugger/search-snapshot-portal.png)

Wenn bei dieser Suche keine Ergebnisse zurückgegeben werden, wurden Application Insights im ausgewählten Zeitraum keine Momentaufnahmen gemeldet.

Um über die Uploaderprotokolle nach einer bestimmten Momentaufnahme-ID zu suchen, geben Sie die jeweilige ID in das Suchfeld ein. Wenn Sie für eine Momentaufnahme, die Ihres Wissens nach hochgeladen wurde, keine Datensätze finden können, gehen Sie folgendermaßen vor:

1. Vergewissern Sie sich, dass Sie die Suche in der richtigen Application Insights-Ressource durchführen, indem Sie den Instrumentierungsschlüssel überprüfen.

2. Passen Sie mithilfe des Zeitstempels aus dem Uploaderprotokoll den Filter „Zeitbereich“ an, um den jeweiligen Zeitbereich bei der Suche zu berücksichtigen.

Wenn trotzdem keine Ausnahme mit dieser Momentaufnahme-ID angezeigt wird, wurde Application Insights der Ausnahmedatensatz nicht gemeldet. Diese Situation kann vorkommen, wenn Ihre Anwendung abgestürzt ist, nachdem die Momentaufnahme erfasst wurde, jedoch bevor der Ausnahmedatensatz gemeldet wurde. Überprüfen Sie in diesem Fall die App Service-Protokolle unter `Diagnose and solve problems`, um festzustellen, ob unerwartete Neustarts oder Ausnahmefehler vorliegen.

## <a name="edit-network-proxy-or-firewall-rules"></a>Bearbeiten von Netzwerkproxy- oder Firewallregeln

Wenn Ihre Anwendung über einen Proxy oder über eine Firewall mit dem Internet verbunden ist, müssen Sie unter Umständen die Regeln aktualisieren, damit sie mit dem Momentaufnahmedebugger-Dienst kommunizieren kann.

Die vom Application Insights-Momentaufnahmedebugger verwendeten IP-Adressen sind im Azure Monitor-Diensttag enthalten. Weitere Informationen finden Sie in der [Dokumentation zu Diensttags](../../virtual-network/service-tags-overview.md).