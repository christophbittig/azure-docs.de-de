---
title: Integrieren von Azure API Management mit Azure Application Insights
titleSuffix: Azure API Management
description: Informationen zum Protokollieren und Anzeigen von Ereignissen aus Azure API Management in Azure Application Insights.
services: api-management
author: dlepow
ms.service: api-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/27/2021
ms.author: danlep
ms.openlocfilehash: eaeacb016098df74d4a8ff7f20ebf677e4322479
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131447453"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Vorgehensweise beim Integrieren von Azure API Management in Azure Application Insights

Sie können Azure Application Insights ganz einfach in Azure API Management integrieren. Azure Application Insights ist ein erweiterbarer Dienst für Webentwickler, die Apps auf mehreren Plattformen erstellen und verwalten. In diesem Leitfaden werden Sie:
* jeden einzelnen Schritt für die Integration von Application Insights in API Management durchgehen und
* Strategien zur Verringerung der Auswirkungen auf die Leistung Ihrer API Management-Dienstinstanz lernen.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen eine Azure API Management-Dienstinstanz. Als Erstes müssen Sie eine [Instanz erstellen](get-started-create-service-instance.md).

## <a name="create-an-application-insights-instance"></a>Erstellen einer Application Insights-Instanz

Um Application Insights zu verwenden, müssen Sie zunächst [eine Instanz des Application Insights-Diensts erstellen](../azure-monitor/app/create-new-resource.md). Um eine Instanz mithilfe des Azure-Portals zu erstellen, lesen Sie [Arbeitsbereichsbasierte Application Insights-Ressourcen](../azure-monitor/app/create-workspace-resource.md).

## <a name="create-a-connection-between-application-insights-and-api-management"></a>Herstellen einer Verbindung zwischen Application Insights und API Management

1. Navigieren Sie im **Azure-Portal** zu Ihrer **Azure API Management-Dienstinstanz**.
1. Wählen Sie **Application Insights** im Menü auf der linken Seite aus.
1. Wählen Sie **+ Hinzufügen**.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-1.png" alt-text="Screenshot, der zeigt, wo eine neue Verbindung hinzugefügt wird":::
1. Wählen Sie die zuvor erstellte **Application Insights**-Instanz aus, und geben Sie eine kurze Beschreibung.
1. Aktivieren Sie das Kontrollkästchen **Verfügbarkeitsmonitor hinzufügen**, um die [Verfügbarkeitsüberwachung](../azure-monitor/app/monitor-web-app-availability.md) Ihrer API Management-Instanz in Application Insights zu aktivieren.
    * Mit dieser Einstellung wird regelmäßig überprüft, ob der API Management-Dienstendpunkt antwortet. 
    * Die Ergebnisse werden im Bereich **Verfügbarkeit** der Application Insights-Instanz angezeigt.
1. Klicken Sie auf **Erstellen**.
1. Überprüfen Sie jetzt, ob die neue Application Insights-Protokollierung mit einem Instrumentierungsschlüssel in der Liste angezeigt wird.  
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
1. Lassen Sie die übrigen Einstellungen unverändert.

    > [!WARNING]
    > Das Überschreiben des Standardwerts (**Anzahl der zu protokollierenden Nutzlastbytes**) **0** kann die Leistung Ihrer APIs erheblich beeinträchtigen.

1. Wählen Sie **Speichern** aus.
1. Im Hintergrund wird eine [Diagnose](/rest/api/apimanagement/2020-12-01/diagnostic/create-or-update)entität namens `applicationinsights` auf API-Ebene erstellt.

> [!NOTE]
> Die Anforderungen sind erfolgreich, sobald API Management die gesamte Antwort an den Client sendet.


| Einstellungsname                        | Werttyp                        | BESCHREIBUNG                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Aktivieren                              | boolean                           | Gibt an, ob die Protokollierung dieser API aktiviert ist.                                                                                                                                                                                                                                                                                                |
| Destination                         | Azure Application Insights-Protokollierung | Gibt die zu verwendende Azure Application Insights-Protokollierung an.                                                                                                                                                                                                                                                                                           |
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
> Sie können Protokollierungen auf verschiedenen Ebenen angeben: 
> + Einzelne API-Protokollierung.
> + Eine Protokollierung für alle APIs.
>  
> *Beide* angeben:
> + wenn es sich um unterschiedliche Protokollierungen handelt, werden beide verwendet (Multiplexprotokolle).
> + wenn es sich um die gleichen Protokollierungen mit unterschiedlichen Einstellungen handelt, überschreibt die einzelne API-Protokollierung (mit der feineren Granularität) die für alle APIs.

## <a name="what-data-is-added-to-application-insights"></a>Welche Daten Application Insights hinzugefügt werden

Application Insights empfängt:

| Telemetrieelement | BESCHREIBUNG |
| -------------- | ----------- |
| *Anforderung* | Für jede eingehende Anforderung: <ul><li>*Front-End-Anforderung*</li><li>*Front-End-Antwort*</li></ul> |
| *Abhängigkeit* | Für jede Anforderung, die an einen Back-End-Dienst weitergeleitet wird: <ul><li>*Back-End-Anforderung*</li><li>*Back-End-Antwort*</li></ul> |
| *Exception* | Für jede fehlgeschlagene Anforderung: <ul><li>Sie ist aufgrund einer geschlossenen Clientverbindung fehlerhaft</li><li>Sie wurde im *bei Fehler*-Abschnitt der API-Richtlinien ausgelöst</li><li>Sie verfügt über einen mit 4xx oder 5xx übereinstimmenden Antwort-HTTP-Statuscode</li></ul> |
| *Ablaufverfolgung* | Wenn Sie eine Richtlinie zur [Ablaufverfolgung](api-management-advanced-policies.md#Trace) konfigurieren. <br /> In der Application Insights-Protokollierung muss die `severity`-Einstellung in der `trace`-Richtlinie größer als die `verbosity`-Einstellung oder gleich sein. |

### <a name="emit-custom-metrics"></a>Ausgabe benutzerdefinierter Metriken
Sie können benutzerdefinierte Metriken ausgeben, indem Sie die Richtlinie [`emit-metric`](api-management-advanced-policies.md#emit-metrics) konfigurieren. 

Um die vorab aggregierten Metriken von Application Insights in API Management verfügbar zu machen, müssen Sie die benutzerdefinierten Metriken des Diensts manuell aktivieren.
1. Verwenden Sie dazu die Richtlinie [`emit-metric`](api-management-advanced-policies.md#emit-metrics) mit der [API zum Erstellen oder Aktualisieren](https://docs.microsoft.com/rest/api/apimanagement/2021-04-01-preview/api-diagnostic/create-or-update).
1. Fügen Sie der Nutzlast, zusammen mit sämtlichen anderen Eigenschaften, `"metrics":true` hinzu.

> [!NOTE]
> Unter [Application Insights](../azure-monitor/service-limits.md#application-insights) finden Sie Informationen zur maximalen Größe und Anzahl von Metriken und Ereignissen pro Application Insights-Instanz.

## <a name="performance-implications-and-log-sampling"></a>Auswirkungen auf die Leistung und Protokollsampling

> [!WARNING]
> Die Protokollierung aller Ereignisse kann abhängig von der Rate der eingehenden Anforderungen schwerwiegende Auswirkungen auf die Leistung haben.

Bei internen Auslastungstests reduzierte die Aktivierung des Protokollierungsfeatures den Durchsatz um 40-50%, wenn die Anforderungsrate 1.000 Anforderungen pro Sekunde überschritt. Bei Application Insights wird die statistische Analyse zur Bewertung der Anwendungsleistung verwendet. Es ist nicht:
* auf den Einsatz als Überwachungssystem ausgelegt.
* für die Protokollierung jeder einzelnen Anforderung für APIs mit hohem Volumen geeignet.

Sie können die Anzahl der protokollierten Anforderungen durch [Anpassen der Einstellung **Sampling** beeinflussen](#enable-application-insights-logging-for-your-api). Ein Wert von 100 % bedeutet, dass alle Anforderungen protokolliert werden, während 0 % keine Protokollierung widerspiegelt. 

Mit **Sampling** können Sie das Volumen von Telemetriedaten reduzieren, einen erheblichen Leistungsabfall effektiv verhindern und dabei dennoch die Vorteile der Protokollierung nutzen.

Um Leistungsprobleme zu umgehen, überspringen Sie:
* Anforderungs- und Antwortheader
* Textprotokollierung

## <a name="video"></a>Video

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>Nächste Schritte

+ Erfahren Sie mehr über [Azure Application Insights](/azure/application-insights/).
+ Betrachten Sie die [Protokollierung mit Azure Event Hubs](api-management-howto-log-event-hubs.md).
