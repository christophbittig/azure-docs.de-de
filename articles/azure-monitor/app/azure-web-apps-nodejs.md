---
title: Überwachen der Azure App Services Leistung von Node.js | Microsoft-Dokumentation
description: Überwachung der Anwendungsleistung für Azure App Services mit Node.js. Ladezeit für Diagramme und Antwortzeit, Informationen zu den Abhängigkeiten und Festlegen von Benachrichtigungen zur Leistung.
ms.topic: conceptual
ms.date: 08/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: 5f80666d4a184da40979a38f6d7f17782291c9e0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "128700103"
---
# <a name="application-monitoring-for-azure-app-service-and-nodejs"></a>Anwendungsüberwachung für Azure App Service und mit Node.js

Die Aktivierung der Überwachung Ihrer Node.js-basierten Webanwendungen, die auf [Azure App Services](../../app-service/index.yml) laufen, ist jetzt einfacher denn je. Während Sie Ihre App zuvor manuell instrumentieren mussten, ist die neueste Erweiterung/der neueste Agent nun standardmäßig in das App Service-Image integriert. Dieser Artikel führt Sie Schritt für Schritt durch das Aktivieren der Application Insights-Überwachung von Azure Monitor und bietet eine vorläufige Anleitung zur Automatisierung des Prozesses für umfangreiche Bereitstellungen.

> [!NOTE]
> Wenn sowohl die Agent-basierte Überwachung als auch die manuelle SDK-basierte Instrumentierung erkannt wird, werden nur die Einstellungen der manuellen Instrumentierung berücksichtigt. Dadurch wird verhindert, dass doppelte Daten gesendet werden. Weitere Informationen dazu finden Sie im [Abschnitt zur Problembehandlung](#troubleshooting) weiter unten.

## <a name="enable-agent-based-monitoring"></a>Aktivieren der Agent-basierten Überwachung

Sie können Ihre Node.js-Apps, die in Azure App Service ausgeführt werden, ohne Codeänderung mit nur wenigen einfachen Schritten überwachen. Application Insights für Node.js-Anwendungen ist in App Service unter Linux sowohl für codebasierte als auch benutzerdefinierte Container und in App Service unter Windows für codebasierte Apps integriert. Die Integration befindet sich in der öffentlichen Vorschau. Durch die Integration wird das Node.js SDK hinzugefügt, das allgemein verfügbar ist (GA). 

1. **Wählen Sie Application Insights** im Azure-Kontrollpanel für Ihren App-Dienst aus und wählen Sie dann **Aktivieren**.

    :::image type="content"source="./media/azure-web-apps/enable.png" alt-text="Screenshot von der Registerkarte Application Insights mit Aktivieren ausgewählt":::. 

2. Erstellen Sie eine neue Ressource, oder wählen Sie eine vorhandene Application Insights-Ressource für diese Anwendung aus.

     > [!NOTE]
     > Beim Klicken auf **OK** zum Erstellen der neuen Ressource wird die Aufforderung **Überwachungseinstellungen anwenden** angezeigt. Wenn Sie **Weiter** wählen, wird Ihre neue Application Insights-Ressource mit Ihrer App Service-Instanz verknüpft, und außerdem wird **ein Neustart Ihrer App Service-Instanz ausgelöst**. 
    
    :::image type="content"source="./media/azure-web-apps/change-resource.png" alt-text="Screenshot mit Dropdown-Menü „Change your resource“."::: 
    
3. Nachdem Sie angegeben haben, welche Ressource verwendet werden soll, ist alles bereit. 


    :::image type="content"source="./media/azure-web-apps-nodejs/app-service-node.png" alt-text="Screenshot von dem Instrument ihrer Anwendung."::: 


## <a name="enable-client-side-monitoring"></a>Aktivieren der clientseitigen Überwachung

Um die clientseitige Überwachung für Ihre Node.js-Anwendung zu aktivieren, müssen Sie [Ihrer Anwendung das clientseitige JavaScript-SDK manuell hinzufügen](./javascript.md).

## <a name="automate-monitoring"></a>Automatisieren der Überwachung

Sie müssen nur die Anwendungseinstellungen festlegen, um die Sammlung von Telemetriedaten mit Application Insights zu aktivieren:

:::image type="content"source="./media/azure-web-apps-nodejs/application-settings-nodejs.png" alt-text="Screenshot von App Service-Anwendungseinstellungen mit verfügbaren Application Insights-Einstellungen."::: 


### <a name="application-settings-definitions"></a>Definitionen von Anwendungseinstellungen

|Name der App-Einstellung |  Definition | Wert |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Die Haupterweiterung, die die Laufzeitüberwachung steuert. | `~2` in Windows oder `~3` in Linux |
|XDT_MicrosoftApplicationInsights_NodeJS |  Flag zum Steuern, ob node.js-Agent enthalten ist. | 0 oder 1 nur unter Windows anwendbar. |


[!INCLUDE [azure-web-apps-arm-automation](../../../includes/azure-monitor-app-insights-azure-web-apps-arm-automation.md)]


## <a name="troubleshooting"></a>Problembehandlung

Im Folgenden finden Sie eine Schritt-für-Schritt-Anleitung zur Problembehandlung für die Erweiterung/Agent-basierte Überwachung von Node.js-basierten Anwendungen, die auf Azure App Services laufen.

# <a name="windows"></a>[Windows](#tab/windows)

1. Überprüfen Sie, ob die App-Einstellung `ApplicationInsightsAgent_EXTENSION_VERSION` auf den Wert „~2“ festgelegt ist.
2. Navigieren Sie zu `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`.  

    :::image type="content"source="./media/azure-web-apps/app-insights-sdk-status.png" alt-text="Screenshot von dem Link über der Ergebnisseite."border ="false"::: 

    - Stellen Sie sicher, dass für `Application Insights Extension Status``Pre-Installed Site Extension, version 2.8.x.xxxx, is running.`angegeben ist. 

         Wird diese nicht ausgeführt, folgen Sie den [Anweisungen zum Aktivieren der Application Insights-Überwachung](#enable-agent-based-monitoring).

    - Navigieren Sie zu *D:\local\Temp\status.json* und öffnen Sie *status.json*.

    Vergewissern Sie sich, dass `SDKPresent` auf false, und `AgentInitializedSuccessfully` auf true gesetzt sind und `IKey` einen gültigen iKey hat.

    Im Folgenden finden Sie ein Beispiel für die JSON-Datei:

    ```json
        "AppType":"node.js",
                
        "MachineName":"c89d3a6d0357",
                
        "PID":"47",
                
        "AgentInitializedSuccessfully":true,
                
        "SDKPresent":false,
                
        "IKey":"00000000-0000-0000-0000-000000000000",
                
        "SdkVersion":"1.8.10"
    
    ```

    Wenn `SDKPresent` auf „true“ gesetzt ist, zeigt dies an, dass die Erweiterung erkannt hat, dass ein Teil des SDK bereits in der Anwendung vorhanden ist, und der Vorgang wird abgebrochen.


# <a name="linux"></a>[Linux](#tab/linux)

1. Überprüfen Sie, ob die App-Einstellung `ApplicationInsightsAgent_EXTENSION_VERSION` auf den Wert „~3“ festgelegt ist.
2. Navigieren Sie zu */var/log/applicationinsights/* und öffnen Sie *status.json*.

    Vergewissern Sie sich, dass `SDKPresent` auf false, und `AgentInitializedSuccessfully` auf true gesetzt sind und `IKey` einen gültigen iKey hat.

    Im Folgenden finden Sie ein Beispiel für die JSON-Datei:

    ```json
        "AppType":"node.js",
                
        "MachineName":"c89d3a6d0357",
                
        "PID":"47",
                
        "AgentInitializedSuccessfully":true,
                
        "SDKPresent":false,
                
        "IKey":"00000000-0000-0000-0000-000000000000",
                
        "SdkVersion":"1.8.10"
    
    ```

    Wenn `SDKPresent` auf „true“ gesetzt ist, zeigt dies an, dass die Erweiterung erkannt hat, dass ein Teil des SDK bereits in der Anwendung vorhanden ist, und der Vorgang wird abgebrochen.
---

[!INCLUDE [azure-web-apps-troubleshoot](../../../includes/azure-monitor-app-insights-azure-web-apps-troubleshoot.md)]

## <a name="release-notes"></a>Versionshinweise

Informationen zu den neuesten Updates und Fehlerbehebungen [finden Sie in den Versionshinweisen](web-app-extension-release-notes.md).

## <a name="next-steps"></a>Nächste Schritte
* [Überwachen von Azure Functions mit Application Insights](monitor-functions.md).
* [Ermöglichen des Sendens von Azure-Diagnosedaten an Application Insights](../agents/diagnostics-extension-to-application-insights.md)
* [Überwachen von Dienstintegritätsmetriken](../data-platform.md) , um sicherzustellen, dass Ihr Dienst verfügbar und reaktionsfähig ist
* [Empfangen von Warnbenachrichtigungen](../alerts/alerts-overview.md) , wenn ein Vorgangsereignis auftritt oder Metriken einen Schwellenwert überschreiten.
* Verwenden von [Application Insights für JavaScript-Apps und Webseiten](javascript.md), um Clienttelemetriedaten von den Browsern zu erhalten, mit denen auf eine Webseite zugegriffen wird
* [Einrichten von Verfügbarkeitswebtests](monitor-web-app-availability.md), um benachrichtigt zu werden, wenn Ihre Website nicht verfügbar ist