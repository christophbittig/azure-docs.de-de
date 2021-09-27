---
title: Integrieren von Azure API Management mit Azure Application Insights
titleSuffix: Azure API Management
description: Informationen zum Protokollieren und Anzeigen von Ereignissen aus Azure API Management in Azure Application Insights.
services: api-management
author: dlepow
ms.service: api-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/04/2021
ms.author: danlep
ms.openlocfilehash: 94c83ace24a5605a72809a30291ae6c1229870e4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676578"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Vorgehensweise beim Integrieren von Azure API Management in Azure Application Insights

Azure API Management ermöglicht die mühelose Integration in Azure Application Insights, einen erweiterbaren Dienst für Webentwickler, die Apps auf mehreren Plattformen erstellen und verwalten. Diese Anleitung führt Sie durch jeden Schritt einer solchen Integration und beschreibt Strategien zur Reduzierung der Auswirkungen auf die Leistung Ihrer API Management-Dienstinstanz.

## <a name="prerequisites"></a>Voraussetzungen

Um dieser Anleitung folgen zu können, benötigen Sie eine Azure API Management-Instanz. Wenn Sie keine besitzen, führen Sie zuerst das [Tutorial](get-started-create-service-instance.md) durch.

## <a name="create-an-application-insights-instance"></a>Erstellen einer Application Insights-Instanz

Bevor Sie Application Insights verwenden können, müssen Sie zunächst eine Instanz des Diensts erstellen. Schritte zum Erstellen einer Instanz mithilfe des Azure-Portals finden Sie unter [Arbeitsbereichsbasierte Application Insights-Ressourcen](../azure-monitor/app/create-workspace-resource.md).
## <a name="create-a-connection-between-application-insights-and-api-management"></a>Herstellen einer Verbindung zwischen Application Insights und API Management

1. Navigieren Sie im **Azure-Portal** zu Ihrer **Azure API Management-Dienstinstanz**.
1. Wählen Sie **Application Insights** im Menü auf der linken Seite aus.
1. Wählen Sie **+ Hinzufügen**.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-1.png" alt-text="Screenshot, der zeigt, wo eine neue Verbindung hinzugefügt wird":::
1. Wählen Sie die zuvor erstellte **Application Insights**-Instanz aus, und geben Sie eine kurze Beschreibung.
1. Aktivieren Sie das Kontrollkästchen **Verfügbarkeitsmonitor hinzufügen**, um die [Verfügbarkeitsüberwachung](../azure-monitor/app/monitor-web-app-availability.md) Ihrer API Management-Instanz in Application Insights zu aktivieren.

    Mit dieser Einstellung wird regelmäßig überprüft, ob der API Management-Dienstendpunkt antwortet. Die Ergebnisse werden im Bereich **Verfügbarkeit** der Application Insights-Instanz angezeigt.
1. Klicken Sie auf **Erstellen**.
1. Sie haben gerade eine Application Insights-Protokollierung mit einem Instrumentierungsschlüssel erstellt. Sie sollte jetzt in der Liste angezeigt werden.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-2.png" alt-text="Screenshot, der zeigt, wo die neu erstellte Application Insights-Protokollierung mit Instrumentierungsschlüssel angezeigt wird":::

> [!NOTE]
> Im Hintergrund wird eine [Protokollierungsentität](/rest/api/apimanagement/2020-12-01/logger/create-or-update) in der API Management-Instanz erstellt, die den Instrumentierungsschlüssel der Application Insights-Instanz enthält.

## <a name="enable-application-insights-logging-for-your-api"></a>Aktivieren der Application Insights-Protokollierung für Ihre API

1. Navigieren Sie im **Azure-Portal** zu Ihrer **Azure API Management-Dienstinstanz**.
1. Wählen Sie im Menü links **APIs** aus.
1. Klicken Sie auf Ihre API, in diesem Fall **Demo Conference API**. Wenn diese Option konfiguriert ist, wählen Sie eine Version aus.
1. Wechseln Sie zur Registerkarte **Einstellungen** in der oberen Leiste.
1. Scrollen Sie nach unten zum Abschnitt **Diagnoseprotokolle**.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-api-1.png" alt-text="Application Insights-Protokollierung":::
1. Aktivieren Sie das Kontrollkästchen **Aktivieren**.
1. Wählen Sie eine angefügte Protokollierung in der Dropdownliste **Ziel** aus.
1. Geben Sie **100** als **Sampling (%)** ein, und aktivieren Sie das Kontrollkästchen **Fehler immer protokollieren**.
1. Wählen Sie **Speichern** aus.

> [!WARNING]
> Überschreiben des Standardwerts **0** in der Einstellung **Anzahl der zu protokollierenden Nutzlastbytes** kann die Leistung Ihrer APIs erheblich beeinträchtigen.

> [!NOTE]
> Im Hintergrund wird eine [Diagnose](/rest/api/apimanagement/2020-12-01/diagnostic/create-or-update)entität namens „applicationinsights“ auf API-Ebene erstellt.

| Einstellungsname                        | Werttyp                        | BESCHREIBUNG                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Aktivieren                              | boolean                           | Gibt an, ob die Protokollierung dieser API aktiviert ist.                                                                                                                                                                                                                                                                                                |
| Destination                         | Azure Application Insights-Protokollierung | Gibt zu verwendende Azure Application Insights-Protokollierung an                                                                                                                                                                                                                                                                                           |
| Sampling (%)                        | Decimal                           | Werte von 0 bis 100 (Prozent). <br/> Gibt an, welcher Prozentsatz der Anforderungen in Application Insights protokolliert wird. 0% Sampling bedeutet, dass 0 (null) Anforderungen protokolliert werden, während ein Sampling von 100% bedeutet, dass alle Anforderungen protokolliert werden. <br/> Verwenden Sie diese Einstellung, um die Auswirkung auf die Leistung zu reduzieren, wenn Anforderungen in Application Insights protokolliert werden. Weitere Informationen finden Sie unter [Auswirkungen auf Leistung und Protokollsampling](#performance-implications-and-log-sampling). |
| Fehler immer protokollieren                   | boolean                           | Wenn diese Einstellung ausgewählt ist, werden alle Fehler unabhängig von der Einstellung **Sampling** in Application Insights protokolliert.   
| Client-IP-Adresse protokollieren | |  Wenn diese Einstellung ausgewählt ist, wird die Client-IP-Adresse für API-Anforderungen in Application Insights protokolliert.                                         |
| Ausführlichkeit         |                                   | Gibt den Ausführlichkeitsgrad an. Nur benutzerdefinierte Ablaufverfolgungen mit höherem Schweregrad werden protokolliert. Standardwert: Information.      | 
| Korrelationsprotokoll |  |  Wählen Sie das Protokoll aus, mit dem Telemetriedaten, die von mehreren Komponenten gesendet werden, korreliert werden. Standard: Legacy <br/>Weitere Informationen finden Sie unter [Telemetriekorrelation in Application Insights](../azure-monitor/app/correlation.md).  |
| Grundlegende Optionen: zu protokollierende Header              | list                              | Gibt die Header an, die in Application Insights für Anforderungen und Antworten protokolliert werden.  Standard: Es werden keine Header protokolliert.                                                                                                                                                                                                             |
| Grundlegende Optionen: Anzahl der zu protokollierenden Nutzlastbytes | integer                           | Gibt an, wie viele erste Bytes des Texts in Application Insights für Anforderungen und Antworten protokolliert werden.  Standardwert: 0.                                                                                                                                                                                                    |
| Erweiterte Optionen: Front-End-Anforderung  |                                   | Gibt an, ob und wie *Front-End-Anforderungen* in Application Insights protokolliert werden. Eine *Front-End-Anforderung* ist eine an den Azure API Management-Dienst gerichtete Anforderung.                                                                                                                                                                        |
| Erweiterte Optionen: Front-End-Antwort |                                   | Gibt an, ob und wie *Front-End-Antworten* in Application Insights protokolliert werden. *Front-End-Antwort* ist eine vom Azure API Management-Dienst ausgehende Antwort.                                                                                                                                                                   |
| Erweiterte Optionen: Back-End-Anforderung   |                                   | Gibt an, ob und wie *Back-End-Anforderungen* in Application Insights protokolliert werden. Eine *Back-End-Anforderung* ist eine vom Azure API Management-Dienst ausgehende Anforderung.                                                                                                                                                                        |
| Erweiterte Optionen: Back-End-Antwort  |                                   | Gibt an, ob und wie *Back-End-Antworten* in Application Insights protokolliert werden. *Back-End-Antwort* ist eine an den Azure API Management-Dienst gerichtete Antwort.                                                                                                                                                                       |

> [!NOTE]
> Sie können auf verschiedenen Ebenen Protokollierungen angeben – eine Protokollierung für eine einzelne API oder eine Protokollierung für alle APIs.
>  
> Wenn Sie beide angeben:
> + Wenn es sich um unterschiedliche Protokollierungen handelt, werden beide verwendet (Multiplexprotokolle),
> + wenn die Protokollierungen identisch sind, jedoch unterschiedliche Einstellungen haben, überschreibt die für die einzelne API (mit der feineren Granularität) die für alle APIs.

## <a name="what-data-is-added-to-application-insights"></a>Welche Daten Application Insights hinzugefügt werden

Application Insights empfängt:

+ *Anforderungs* telemetrieereignis, für jede eingehende Anforderung:
    + *Front-End-Anforderung*, *Front-End-Antwort*
+ *Abhängigkeits* telemetrieereignis, für jede an einen Back-End-Dienst weitergeleitete Anforderung:
    + *Back-End-Anforderung*, *Back-End-Antwort*
+ ein *Ausnahmetelemetrieereignis* für jede fehlerhafte Anforderung:
    + Sie ist aufgrund einer geschlossenen Clientverbindung fehlerhaft
    + sie wurde im *bei Fehler*-Abschnitt der API-Richtlinien ausgelöst
    + sie verfügt über einen mit 4xx oder 5xx übereinstimmenden Antwort-HTTP-Statuscode
+ Telemetrieelement zur *Ablaufverfolgung*, wenn Sie eine Richtlinie zur [Ablaufverfolgung](api-management-advanced-policies.md#Trace) konfigurieren. 
    + In der Application Insights-Protokollierung muss die `severity`-Einstellung in der `trace`-Richtlinie größer als die `verbosity`-Einstellung oder gleich sein.

Sie können auch benutzerdefinierte Metriken ausgeben, indem Sie die Richtlinie [`emit-metric`](api-management-advanced-policies.md#emit-metrics) konfigurieren.

> [!NOTE]
> Unter [Application Insights](../azure-monitor/service-limits.md#application-insights) finden Sie Informationen zur maximalen Größe und Anzahl von Metriken und Ereignissen pro Application Insights-Instanz.

## <a name="performance-implications-and-log-sampling"></a>Auswirkungen auf die Leistung und Protokollsampling

> [!WARNING]
> Die Protokollierung aller Ereignisse kann abhängig von der Rate der eingehenden Anforderungen schwerwiegende Auswirkungen auf die Leistung haben.

Bei internen Auslastungstests reduzierte die Aktivierung dieses Features den Durchsatz um 40-50%, wenn die Anforderungsrate 1.000 Anforderungen pro Sekunde überschritt. Mit Application Insights wird die statistische Analyse zur Bewertung der Anwendungsleistung verwendet. Jedoch ist dieses Programm kein Überwachungssystem und nicht zur Protokollierung jeder einzelnen an APIs mit hohem Volumen gerichteten Anforderung geeignet.

Sie können die Anzahl der protokollierten Anforderungen durch Anpassen der Einstellung **Sampling** beeinflussen (siehe die obigen Schritte). Ein Wert von 100 % bedeutet, dass alle Anforderungen protokolliert werden, während 0 % keine Protokollierung widerspiegelt. Mit **Sampling** können Sie das Volumen von Telemetriedaten reduzieren, einen erheblichen Leistungsabfall effektiv verhindern und dabei dennoch die Vorteile der Protokollierung nutzen.

Das Überspringen der Protokollierung von Headern und Textkörpern der Anforderungen und Antworten hat auch positive Auswirkungen auf die Abschwächung von Leistungsproblemen.

## <a name="video"></a>Video

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>Nächste Schritte

+ Erfahren Sie mehr über [Azure Application Insights](/azure/application-insights/).
+ Betrachten Sie die [Protokollierung mit Azure Event Hubs](api-management-howto-log-event-hubs.md).
