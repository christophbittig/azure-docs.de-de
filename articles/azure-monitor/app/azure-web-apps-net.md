---
title: 'Überwachen der Leistung von Azure App Services: ASP.NET | Microsoft-Dokumentation'
description: Überwachung der Anwendungsleistung von Azure App Services mithilfe von ASP.NET. Ladezeit für Diagramme und Antwortzeit, Informationen zu den Abhängigkeiten und Festlegen von Benachrichtigungen zur Leistung.
ms.topic: conceptual
ms.date: 08/05/2021
ms.custom: devx-track-js, devx-track-dotnet
ms.openlocfilehash: 97c0d2710d14e523338ac7cd670383caa75c7cef
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131443602"
---
# <a name="application-monitoring-for-azure-app-service-and-aspnet"></a>Anwendungsüberwachung für Azure App Service und ASP.NET 

Das Aktivieren der Überwachung Ihrer ASP.NET-basierten Webanwendungen, die in [Azure App Services](../../app-service/index.yml) ausgeführt werden, ist jetzt einfacher als je zuvor. Während Sie Ihre App zuvor manuell instrumentieren mussten, ist die neueste Erweiterung/der neueste Agent nun standardmäßig in das App Service-Image integriert. Dieser Artikel führt Sie Schritt für Schritt durch das Aktivieren der Application Insights-Überwachung von Azure Monitor und bietet eine vorläufige Anleitung zur Automatisierung des Prozesses für umfangreiche Bereitstellungen.

> [!NOTE]
> Das manuelle Hinzufügen einer Application Insights-Websiteerweiterung über **Entwicklungstools** > **Erweiterungen** ist veraltet. Diese Methode der Erweiterungsinstallation war von manuellen Updates für jede neue Version abhängig. Die neueste stabile Version der Erweiterung ist jetzt als Teil des App Service-Images [vorinstalliert](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions). Die Dateien befinden sich in `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` und werden mit jeder stabilen Version automatisch aktualisiert. Wenn Sie den weiter unten beschriebenen Anweisungen zum Aktivieren der Agent-basierten Überwachung folgen, wird die veraltete Erweiterung automatisch entfernt.

> [!NOTE]
> Wenn sowohl die Agent-basierte Überwachung als auch die manuelle SDK-basierte Instrumentierung erkannt wird, werden nur die Einstellungen der manuellen Instrumentierung berücksichtigt. Dadurch wird verhindert, dass doppelte Daten gesendet werden. Weitere Informationen dazu finden Sie im [Abschnitt zur Problembehandlung](#troubleshooting) weiter unten.

## <a name="enable-agent-based-monitoring"></a>Aktivieren der Agent-basierten Überwachung

> [!NOTE]
> Die Kombination von APPINSIGHTS_JAVASCRIPT_ENABLED und urlCompression wird nicht unterstützt. Weitere Informationen enthält die Erläuterung im [Problembehandlungsabschnitt](#appinsights_javascript_enabled-and-urlcompression-is-not-supported).

1. Wählen Sie in der Azure-Systemsteuerung die Option **Application Insights** für Ihren App-Dienst und dann **Aktivieren** aus.

    :::image type="content"source="./media/azure-web-apps/enable.png" alt-text="Screenshot: Registerkarte „Application Insights“ mit ausgewählter Option „Aktivieren“."::: 

2. Erstellen Sie eine neue Ressource, oder wählen Sie eine vorhandene Application Insights-Ressource für diese Anwendung aus. 

    > [!NOTE]
    > Beim Klicken auf **OK** zum Erstellen der neuen Ressource wird die Aufforderung **Überwachungseinstellungen anwenden** angezeigt. Wenn Sie **Weiter** wählen, wird Ihre neue Application Insights-Ressource mit Ihrer App Service-Instanz verknüpft, und außerdem wird **ein Neustart Ihrer App Service-Instanz ausgelöst**. 

     :::image type="content"source="./media/azure-web-apps/change-resource.png" alt-text="Screenshot: Dropdownliste „Ressource ändern“."::: 

3. Nach Angabe der zu verwendenden Ressource können Sie plattformspezifisch auswählen, wie Application Insights Daten für Ihre Anwendung erfassen soll. Die Überwachung von ASP.NET-Apps ist standardmäßig mit zwei verschiedenen Erfassungsstufen aktiviert.

    :::image type="content"source="./media/azure-web-apps-net/instrument-net.png" alt-text="Screenshot der Seite mit Application Insights-Websiteerweiterungen mit ausgewählter Option „Neue Ressource erstellen“"::: 
 
     Es folgt eine Übersicht über die gesammelten Daten für jede Route:
            
    | Daten | ASP.NET: Basissammlung | ASP.NET: Empfohlene Sammlung |
    | --- | --- | --- |
    | Fügt CPU-, Speicher- und E/A-Nutzungstrends hinzu. |Ja |Ja |
    | Sammelt Nutzungstrends und ermöglicht die Korrelation von Verfügbarkeitsergebnissen und Transaktionen. | Ja |Ja |
    | Erfasst Ausnahmen, die vom Hostprozess nicht behandelt werden. | Ja |Ja |
    | Verbessert die Genauigkeit der APM-Metriken unter Last, wenn die Stichprobe verwendet wird. | Ja |Ja |
    | Korreliert Microservices über Anforderungs-/Abhängigkeitsgrenzen hinweg. | Nein (nur APM-Einzelinstanzfunktionen) |Ja |

4. Zum Konfigurieren der Stichprobenentnahme, die Sie zuvor über die Datei „applicationinsights.config“ steuern konnten, können Sie jetzt über die Anwendungseinstellungen mit dem entsprechenden Präfix `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor` interagieren. 

    - Wenn Sie beispielsweise den anfänglichen Prozentsatz für die Stichprobenerstellung ändern möchten, können Sie die Anwendungseinstellung `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` und einen Wert von `100` erstellen.
    - Legen Sie zum Deaktivieren der Stichprobenentnahme `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_MinSamplingPercentage` auf den Wert `100` fest.
    - Folgende Einstellungen werden unterstützt:
        - `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage`
        - `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_MinSamplingPercentage`
        - `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_EvaluationInterval`
        - `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_MaxTelemetryItemsPerSecond`
        
    - Eine Liste der unterstützten Einstellungen und Definitionen des Telemetrieprozessors für die adaptive Stichprobenerstellung finden Sie im [Code](https://github.com/microsoft/ApplicationInsights-dotnet/blob/master/BASE/Test/ServerTelemetryChannel.Test/TelemetryChannel.Tests/AdaptiveSamplingTelemetryProcessorTest.cs) und der [Dokumentation zur Stichprobenentnahme](./sampling.md#configuring-adaptive-sampling-for-aspnet-applications).


## <a name="enable-client-side-monitoring"></a>Aktivieren der clientseitigen Überwachung

Die clientseitige Überwachung für ASP.NET ist optional. Zum Aktivieren der clientseitigen Überwachung gehen Sie folgendermaßen vor:

* **Einstellungen** **>** **Konfiguration**
   * Erstellen Sie unter „Anwendungseinstellungen“ eine **neue Anwendungseinstellung**:

     Name: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Wert: `true`

   * **Speichern** Sie die Einstellungen, und **starten Sie die App neu**.

Zum Deaktivieren der clientseitigen Überwachung entfernen Sie entweder das zugeordnete Schlüssel-Wert-Paar aus den Anwendungseinstellungen oder legen Sie den Wert auf „false“ fest.

## <a name="automate-monitoring"></a>Automatisieren der Überwachung

Sie müssen nur die Anwendungseinstellungen festlegen, um die Sammlung von Telemetriedaten mit Application Insights zu aktivieren:

:::image type="content"source="./media/azure-web-apps-net/application-settings-net.png" alt-text="Screenshot: App Service-Anwendungseinstellungen mit Application Insights-Einstellungen."::: 

### <a name="application-settings-definitions"></a>Definitionen von Anwendungseinstellungen

|Name der App-Einstellung |  Definition | Wert |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Die Haupterweiterung, die die Laufzeitüberwachung steuert. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  Im Standardmodus werden nur wichtige Features aktiviert, um eine optimale Leistung zu gewährleisten. | `default` oder `recommended` |
|InstrumentationEngine_EXTENSION_VERSION | Legt fest, ob die Engine zum binären erneuten Generieren `InstrumentationEngine` aktiviert ist. Diese Einstellung wirkt sich auf die Leistung aus und beeinträchtigt den Kaltstart/die Startzeit. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Legt fest, ob SQL- und Azure-Tabellentext gemeinsam mit Abhängigkeitsaufrufen erfasst wird. Leistungswarnung: die Kaltstartzeit der Anwendung wird beeinträchtigt. Diese Einstellung erfordert die `InstrumentationEngine`. | `~1` |

[!INCLUDE [azure-web-apps-arm-automation](../../../includes/azure-monitor-app-insights-azure-web-apps-arm-automation.md)]


## <a name="upgrade-monitoring-extensionagent---net"></a>Upgrade von Überwachungserweiterung/-Agent: .NET 

### <a name="upgrade-from-versions-289-and-up"></a>Upgrade von Versionen 2.8.9 und höher

Das Upgrade von Version 2.8.9 erfolgt automatisch ohne zusätzliche Aktionen. Die neuen Überwachungsfunktionen werden dem Ziel-App-Service im Hintergrund bereitgestellt und beim Neustart der Anwendung übernommen.

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
> Wenn Sie eine Web-App mit den `ASP.NET`-Runtimes in Azure App Services erstellen, wird eine einzelne statische HTML-Seite als Startwebsite bereitgestellt. Es wird **nicht** empfohlen, ein Problem mit der Standardvorlage zu beheben. Stellen Sie eine Anwendung vor der Problembehandlung zur Verfügung.

Im Folgenden finden Sie eine Schrittanleitung zur Problembehandlung für die auf Erweiterungen oder Agents basierende Überwachung von ASP.NET-basierten Anwendungen, die in Azure App Services ausgeführt werden.

1. Überprüfen Sie, ob die App-Einstellung `ApplicationInsightsAgent_EXTENSION_VERSION` auf den Wert „~2“ festgelegt ist.
2. Navigieren Sie zu `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`.  

    :::image type="content"source="./media/azure-web-apps/app-insights-sdk-status.png" alt-text="Screenshot von dem Link über der Ergebnisseite."border ="false"::: 
    
    - Stellen Sie sicher, dass für `Application Insights Extension Status``Pre-Installed Site Extension, version 2.8.x.xxxx, is running.`angegeben ist. 
    
         Wird diese nicht ausgeführt, folgen Sie den [Anweisungen zum Aktivieren der Application Insights-Überwachung](#enable-agent-based-monitoring).

    - Stellen Sie sicher, dass die Statusquelle vorhanden ist und folgendermaßen aussieht: `Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`

         Wenn kein ähnlicher Wert vorhanden ist, bedeutet dies, dass die Anwendung derzeit nicht ausgeführt oder nicht unterstützt wird. Um sicherzustellen, dass die Anwendung ausgeführt wird, rufen Sie die Anwendungs-URL/Anwendungsendpunkte manuell auf, wodurch die Laufzeitinformationen verfügbar werden.

    - Stellen Sie sicher, dass `IKeyExists` den Wert `true` hat. Wenn der Wert `false` ist, fügen Sie den Anwendungseinstellungen mit Ihrer Instrumentierungsschlüssel-GUID `APPINSIGHTS_INSTRUMENTATIONKEY` und `APPLICATIONINSIGHTS_CONNECTION_STRING` hinzu.

    - Stellen Sie sicher, dass keine Einträge für `AppAlreadyInstrumented`, `AppContainsDiagnosticSourceAssembly` und `AppContainsAspNetTelemetryCorrelationAssembly` vorhanden sind.

         Wenn einer dieser Einträge vorhanden ist, entfernen Sie die folgenden Pakete aus Ihrer Anwendung: `Microsoft.ApplicationInsights`, `System.Diagnostics.DiagnosticSource` und `Microsoft.AspNet.TelemetryCorrelation`.

#### <a name="default-website-deployed-with-web-apps-does-not-support-automatic-client-side-monitoring"></a>Die mit Web-Apps bereitgestellte Standardwebsite unterstützt keine automatische clientseitige Überwachung

Wenn Sie eine Web-App mit den `ASP.NET`-Runtimes in Azure App Services erstellen, wird eine einzelne statische HTML-Seite als Startwebsite bereitgestellt. Die statische Webseite lädt auch ein ASP.NET-verwaltetes Webpart in IIS. Dies ermöglicht das Testen der serverseitigen Überwachung ohne Code, unterstützt jedoch nicht die automatische clientseitige Überwachung.

Wenn Sie die server- und clientseitige Überwachung ohne Code für ASP.NET in einer Azure App Services-Web-App testen möchten, empfehlen wir, den offiziellen Anleitungen zum [Erstellen einer ASP.NET Framework-Web-App](../../app-service/quickstart-dotnetcore.md?tabs=netframework48) zu folgen und die Überwachung anschließend anhand der Anweisungen im vorliegenden Artikel zu aktivieren.


### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>Die Kombination von APPINSIGHTS_JAVASCRIPT_ENABLED und urlCompression wird nicht unterstützt.

Wenn Sie „APPINSIGHTS_JAVASCRIPT_ENABLED=true“ in Fällen verwenden, in denen der Inhalt codiert ist, erhalten Sie möglicherweise Fehlermeldungen ähnlich der folgenden:

- 500-URL-Rewrite-Fehler
- Der 500.53-URL-Rewrite-Modul-Fehler mit der Meldung, dass Ausgangs-Rewrite-Regeln nicht angewendet werden können, wenn der Inhalt der HTTP-Antwort codiert ist („gzip“).

Dies liegt daran, dass die Anwendungseinstellung APPINSIGHTS_JAVASCRIPT_ENABLED auf „true“ gesetzt und die Inhaltscodierung zur gleichen Zeit vorhanden ist. Dieses Szenario wird noch nicht unterstützt. Die Problemumgehung besteht darin, APPINSIGHTS_JAVASCRIPT_ENABLED aus Ihren Anwendungseinstellungen zu entfernen. Leider bedeutet dies, dass manuelle SDK-Verweise für Ihre Webseiten erforderlich sind, wenn die JavaScript-Instrumentierung auf der Client/Browser-Seite noch erforderlich ist. Führen Sie die [Anweisungen](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup) für manuelle Instrumentierung mit dem JavaScript SDK aus.

Aktuelle Informationen zu Application Insights-Agent/Erweiterung finden Sie in den [Versionshinweisen](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md).

[!INCLUDE [azure-web-apps-troubleshoot](../../../includes/azure-monitor-app-insights-azure-web-apps-troubleshoot.md)]

### <a name="php-and-wordpress-are-not-supported"></a>PHP und WordPress werden nicht unterstützt.

PHP- und WordPress-Sites werden nicht unterstützt. Zurzeit ist kein offiziell unterstütztes SDK/unterstützter Agent für die serverseitige Überwachung dieser Workloads verfügbar. Das manuelle Instrumentieren clientseitiger Transaktionen auf einer PHP- oder WordPress-Website durch Hinzufügen von clientseitigem JavaScript zu Ihren Webseiten kann jedoch mithilfe des [JavaScript SDK](./javascript.md) erreicht werden.

Die folgende Tabelle enthält eine ausführlichere Beschreibung der Bedeutung dieser Werte, der zugrunde liegenden Ursachen und der empfohlenen Problembehebungen:

|Problemwert|Erklärung|Fix
|---- |----|---|
| `AppAlreadyInstrumented:true` | Dieser Wert zeigt an, dass die Erweiterung erkannt hat, dass ein Teil des SDK bereits in der Anwendung vorhanden ist, und der Vorgang wird abgebrochen. Mögliche Ursache ist ein Verweis auf `System.Diagnostics.DiagnosticSource`, `Microsoft.AspNet.TelemetryCorrelation` oder `Microsoft.ApplicationInsights`.  | Entfernen Sie die Verweise. Einige dieser Verweise werden standardmäßig aus bestimmten Visual Studio-Vorlagen hinzugefügt, und ältere Versionen von Visual Studio können Verweise zu `Microsoft.ApplicationInsights` hinzufügen.
|`AppAlreadyInstrumented:true` | Wenn die Anwendung auf ASP.NET Core 2.1 oder 2.2 ausgerichtet ist, zeigt dieser Wert an, dass die Erweiterung erkannt hat, dass ein Teil des SDK bereits in der Anwendung vorhanden ist, und der Vorgang wird abgebrochen. | Kunden mit .NET Core 2.1, 2.2 wird [empfohlen](https://github.com/aspnet/Announcements/issues/287), stattdessen das Microsoft.AspNetCore.App-Metapaket zu verwenden. Aktivieren Sie außerdem „Interoperabel mit Application Insights SDK“ im Portal (siehe die obigen Anweisungen).|
|`AppAlreadyInstrumented:true` | Dieser Wert kann auch durch Vorhandensein der oben genannten DLLs im App-Ordner aus einer früheren Bereitstellung verursacht werden. | Bereinigen Sie den App-Ordner, um sicherzustellen, dass diese DLLs entfernt werden. Überprüfen Sie sowohl das Verzeichnis „bin“ Ihrer lokalen App als auch das Verzeichnis „wwwroot“ im App Service. (Überprüfen des Verzeichnisses „wwwroot“ Ihrer App Service-Web-App: Erweiterte Tools (Kudu) > Debugging-Konsole > CMD > home\site\wwwroot).
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Dieser Wert gibt an, dass die Erweiterung Verweise auf `Microsoft.AspNet.TelemetryCorrelation` in der Anwendung gefunden hat, und der Vorgang wird abgebrochen. | Entfernen Sie den Verweis.
|`AppContainsDiagnosticSourceAssembly**:true`|Dieser Wert gibt an, dass die Erweiterung Verweise auf `System.Diagnostics.DiagnosticSource` in der Anwendung gefunden hat, und der Vorgang wird abgebrochen.| Entfernen Sie für ASP.NET den Verweis. 
|`IKeyExists:false`|Dieser Wert gibt an, dass der Instrumentierungsschlüssel nicht in der App-Einstellung `APPINSIGHTS_INSTRUMENTATIONKEY` vorhanden ist. Mögliche Ursachen: Die Werte wurden möglicherweise versehentlich entfernt, nicht im Automatisierungsskript festgelegt usw. | Stellen Sie sicher, dass die Einstellung in den App Service-Anwendungseinstellungen vorhanden ist.

## <a name="release-notes"></a>Versionshinweise

Informationen zu den neuesten Updates und Fehlerbehebungen [finden Sie in den Versionshinweisen](web-app-extension-release-notes.md).

## <a name="next-steps"></a>Nächste Schritte

* [Ausführen des Profilers in Ihrer Live-App](./profiler.md)
* [Überwachen von Azure Functions mit Application Insights](monitor-functions.md).
* [Ermöglichen des Sendens von Azure-Diagnosedaten an Application Insights](../agents/diagnostics-extension-to-application-insights.md)
* [Überwachen von Dienstintegritätsmetriken](../data-platform.md) , um sicherzustellen, dass Ihr Dienst verfügbar und reaktionsfähig ist
* [Empfangen von Warnbenachrichtigungen](../alerts/alerts-overview.md) , wenn ein Vorgangsereignis auftritt oder Metriken einen Schwellenwert überschreiten.
* Verwenden von [Application Insights für JavaScript-Apps und Webseiten](javascript.md), um Clienttelemetriedaten von den Browsern zu erhalten, mit denen auf eine Webseite zugegriffen wird
* [Einrichten von Verfügbarkeitswebtests](monitor-web-app-availability.md), um benachrichtigt zu werden, wenn Ihre Website nicht verfügbar ist
