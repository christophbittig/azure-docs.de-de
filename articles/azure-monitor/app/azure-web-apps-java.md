---
title: Überwachung der Leistung von Azure-Anwendungsdiensten mit Java | Microsoft-Dokumentation
description: Überwachung der Anwendungsleistung für Azure-Anwendungsdienste mit Java. Ladezeit für Diagramme und Antwortzeit, Informationen zu den Abhängigkeiten und Festlegen von Benachrichtigungen zur Leistung.
ms.topic: conceptual
ms.date: 08/05/2021
ms.custom: devx-track-java
ms.openlocfilehash: d673524b30eae13e24758aff23a68bd2b38c2e3e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "128700113"
---
# <a name="application-monitoring-for-azure-app-service-and-java"></a>Anwendungsüberwachung für Azure App Service und Java

Die Überwachung Ihrer Java-basierten Webanwendungen, die auf [Azure App Services](../../app-service/index.yml) laufen, erfordert keine Änderungen am Code. Dieser Artikel führt Sie durch die Aktivierung von Azure Monitor Application Insights Monitoring und stellt eine erste Anleitung zur Automatisierung des Prozesses für umfangreiche Bereitstellungen bereit.

## <a name="enable-application-insights"></a>Aktivieren von Application Insights

Es wird empfohlen, die Anwendungsüberwachung für Java-Anwendungen, die auf Azure App Services laufen, über das Azure-Portal zu aktivieren. Wenn Sie die Anwendungsüberwachung im Azure-Portal aktivieren, wird Ihre Anwendung automatisch mit Application Insights instrumentiert.  

### <a name="auto-instrumentation-through-azure-portal"></a>Automatische Instrumentierung über das Azure-Portal

Diese Methode erfordert keine Code-Änderungen oder erweiterte Konfigurationen und ist damit der einfachste Weg, um mit der Überwachung für Azure App Services zu beginnen. Sie können zusätzliche Konfigurationen anwenden und dann auf der Grundlage Ihres spezifischen Szenarios bewerten, ob eine erweiterte Überwachung durch manuelle [Instrumentierung](./java-2x-get-started.md?tabs=maven) erforderlich ist.

### <a name="enable-backend-monitoring"></a>Aktivieren der Back-End-Überwachung

Sie können die Überwachung für Ihre Java-Apps, die in Azure App Service ausgeführt werden, mit nur einem Klick aktivieren, ohne dass Code geändert werden muss. Application Insights für Java ist in App Service unter Linux sowohl für codebasierte als auch benutzerdefinierte Container und in App Service unter Windows für codebasierte Apps integriert. Es ist wichtig zu wissen, wie Ihre Anwendung überwacht wird. Die Integration fügt [Application Insights Java 3.x](./java-in-process-agent.md) hinzu und die Telemetrie wird automatisch gesammelt.

1. **Wählen Sie Application Insights** im Azure-Kontrollpanel für Ihren App-Dienst aus und wählen Sie dann **Aktivieren**.

    :::image type="content"source="./media/azure-web-apps/enable.png" alt-text="Screenshot von der Registerkarte Application Insights mit Aktivieren ausgewählt":::. 

2. Erstellen Sie eine neue Ressource, oder wählen Sie eine vorhandene Application Insights-Ressource für diese Anwendung aus.

    > [!NOTE]
    > Wenn Sie **OK** wählen, um die neue Ressource zu erstellen, werden Sie **Überwachungseinstellungen Anwenden** aufgefordert. Wenn Sie **Weiter** wählen, wird Ihre neue Application Insights-Ressource mit Ihrer App Service-Instanz verknüpft, und außerdem wird **ein Neustart Ihrer App Service-Instanz ausgelöst**. 

     :::image type="content"source="./media/azure-web-apps/change-resource.png" alt-text="Screenshot mit Dropdown-Menü &quot;Change your resource&quot;."::: 

3. Dieser Schritt ist nicht erforderlich. Nachdem Sie angegeben haben, welche Ressource verwendet werden soll, können Sie den Java-Agent konfigurieren. Wenn Sie den Java-Agent nicht konfigurieren, gelten die Standardkonfigurationen. 

    Es steht der gesamte [Satz von Konfigurationen](./java-standalone-config.md) zur Verfügung, Sie müssen nur eine gültige [json Datei](./java-standalone-config.md#an-example) einfügen. **Schließen Sie die Verbindungszeichenfolge und alle Konfigurationen aus, die sich in der Vorschau befinden** – Sie können die Elemente hinzufügen, die sich derzeit in der Vorschau befinden, sobald sie allgemein verfügbar werden.

    Nachdem Sie die Konfigurationen über Azure-Portal geändert haben, wird APPLICATIONINSIGHTS_CONFIGURATION_FILE Umgebungsvariable automatisch aufgefüllt und im Einstellungsbereich App Service angezeigt. Diese Variable enthält den vollständigen JSON-Inhalt, den Sie in Azure-Portal Konfigurationstextfeld für Ihre Java-App eingefügt haben. 

    :::image type="content"source="./media/azure-web-apps-java/create-app-service-ai.png" alt-text="Screenshot von dem Instrument ihrer Anwendung."::: 
    

## <a name="enable-client-side-monitoring"></a>Aktivieren der clientseitigen Überwachung

Um die clientseitige Überwachung für Ihre Java-Anwendung zu aktivieren, müssen Sie [Ihrer Anwendung das clientseitige JavaScript-SDK manuell hinzufügen](./javascript.md).

## <a name="automate-monitoring"></a>Automatisieren der Überwachung

### <a name="application-settings"></a>Anwendungseinstellungen

Um die Sammlung von Telemetriedaten mit Application Insights zu aktivieren, müssen nur die folgenden Anwendungseinstellungen festgelegt werden:

|Name der App-Einstellung |  Definition | Wert |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Steuert die Laufzeitüberwachung | `~2` für Windows oder `~3` für Linux |
|XDT_MicrosoftApplicationlnsights_Java |  Flag, mit dem gesteuert werden kann, dass der Java-Agent eingeschlossen ist | 0 oder 1 nur unter Windows anwendbar
|APPLICATIONINSIGHTS_SELF_DIAGNOSTICS_LEVEL | Nur verwenden, wenn Sie die Integration von Application Insights mit dem App Service debuggen müssen | debug

:::image type="content"source="./media/azure-web-apps-java/application-settings-java.png" alt-text="Screenshot von den Anwendungseinstellungen des App Service mit den verfügbaren Einstellungen für Application Insights."::: 

> [!NOTE]
> Profiler und Momentaufnahmedebugger sind für Java-Anwendungen nicht verfügbar

[!INCLUDE [azure-web-apps-arm-automation](../../../includes/azure-monitor-app-insights-azure-web-apps-arm-automation.md)]

## <a name="troubleshooting"></a>Problembehandlung

Im Folgenden finden Sie eine Schritt-für-Schritt-Anleitung zur Problembehandlung für Java-basierte Anwendungen, die auf Azure App Services ausgeführt werden.

1. Überprüfen Sie, ob die `ApplicationInsightsAgent_EXTENSION_VERSION`App-Einstellung auf einen Wert von "~2" unter Windows und "~3" unter Linux eingestellt ist
1. Überprüfen Sie die Protokolldatei, um zu sehen, ob der Agent erfolgreich gestartet wurde: Wechseln Sie zu ' https://yoursitename.scm.azurewebsites.net/ unter SSH in das Stammverzeichnis, die Protokolldatei befindet sich unter LogFiles/Applicationlnsights. 
  
    :::image type="content"source="./media/azure-web-apps-java/app-insights-java-status.png" alt-text="Screenshot von dem Link über der Ergebnisseite."::: 

1. Nachdem Sie die Anwendungsüberwachung für Ihre Java-Anwendung aktiviert haben, können Sie überprüfen, ob der Agent funktioniert, indem Sie sich die Live-Metriken ansehen – noch bevor Sie die Anwendung für den App Service bereitstellen, werden Sie einige Anforderungen aus der Umgebung sehen. Denken Sie daran, dass die vollständigen Telemetriedaten nur dann zur Verfügung stehen, wenn Sie Ihre Anwendung bereitgestellt haben und diese ausgeführt wird. 
1. Setzen Sie die Umgebungsvariable APPLICATIONINSIGHTS_SELF_DIAGNOSTICS_LEVEL auf 'debug', wenn Sie keine Fehler sehen und keine Telemetrie vorhanden ist
1. Manchmal ist die neueste Version des Java-Agenten von Application Insights nicht im App Service verfügbar – es dauert ein paar Monate, bis die neuesten Versionen in allen Regionen verfügbar sind. Falls Sie die neueste Version des Java-Agenten benötigen, um Ihre Anwendung in App Service zu überwachen, können Sie den Agenten manuell hochladen: 
    * Hochladen der Java-Agent-Jar-Datei zu App Service
        * Get die neueste Version von [Azure CLI](/cli/azure/install-azure-cli-windows?tabs=azure-cli)
        * Get die neueste Version des [Application Insights Java-Agenten](./java-in-process-agent.md)
        * Bereitstellen von dem Java-Agenten für App Service – ein Beispielbefehl für die Bereitstellung des Java-Agenten jar: `az webapp deploy --src-path applicationinsights-agent-{VERSION_NUMBER}.jar --target-path java/applicationinsights-agent-{VERSION_NUMBER}.jar --type static --resource-group {YOUR_RESOURCE_GROUP} --name {YOUR_APP_SVC_NAME}` oder verwenden Sie [diese Führungslinie](../../app-service/quickstart-java.md?tabs=javase&pivots=platform-linux#configure-the-maven-plugin) für die Bereitstellung über das Maven-Plugin
    * Nachdem die jar-Datei des Agenten hochgeladen wurde, gehen Sie zu den Konfigurationen des App Service und fügen Sie eine neue Umgebungsvariable JAVA_OPTS hinzu und setzen Sie ihren Wert auf `-javaagent:D:/home/{PATH_TO_THE_AGENT_JAR}/applicationinsights-agent-{VERSION_NUMBER}.jar`
    * Deaktivieren Sie Application Insights über die Registerkarte Application Insights
    * Neustarten der App

    > [!NOTE]
    > Wenn Sie die Umgebungsvariable JAVA_OPTS setzen, müssen Sie Application Insights im Portal deaktivieren. Wenn Sie es andererseits vorziehen, Application Insights über das Portal zu aktivieren, stellen Sie sicher, dass Sie die Variable JAVA_OPTS in den Konfigurationseinstellungen des App Service nicht setzen. 


[!INCLUDE [azure-web-apps-troubleshoot](../../../includes/azure-monitor-app-insights-azure-web-apps-troubleshoot.md)]

## <a name="release-notes"></a>Anmerkungen zu diesem Release

Informationen zu den neuesten Updates und Fehlerbehebungen [finden Sie in den Versionshinweisen](web-app-extension-release-notes.md).

## <a name="next-steps"></a>Nächste Schritte

* [Überwachen von Azure Functions (mit Application Insights)](monitor-functions.md).
* [Ermöglichen des Sendens von Azure-Diagnosedaten an Application Insights](../agents/diagnostics-extension-to-application-insights.md)
* [Überwachen von Dienstintegritätsmetriken](../data-platform.md) , um sicherzustellen, dass Ihr Dienst verfügbar und reaktionsfähig ist
* [Empfangen von Warnbenachrichtigungen](../alerts/alerts-overview.md) , wenn ein Vorgangsereignis auftritt oder Metriken einen Schwellenwert überschreiten.
* Verwenden von [Application Insights für JavaScript-Apps und Webseiten](javascript.md), um Clienttelemetriedaten von den Browsern zu erhalten, mit denen auf eine Webseite zugegriffen wird
* [Einrichten von Verfügbarkeitswebtests](monitor-web-app-availability.md), um benachrichtigt zu werden, wenn Ihre Website nicht verfügbar ist