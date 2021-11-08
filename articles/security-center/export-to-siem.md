---
title: Übertragen Sie Ihre Warnmeldungen von Microsoft Defender for Cloud auf SIEM-Systeme (Security Information and Event Management) und andere Überwachungslösungen
description: Erfahren Sie, wie Sie Ihre Sicherheitswarnungen an Microsoft Sentinel, SIEMs von Drittanbietern, SOAR oder ITSM-Lösungen übertragen können.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/06/2020
ms.author: memildin
ms.openlocfilehash: 34fcb3b4a8771f9c2b635a0f8e2c5a43ddf896a3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131428613"
---
# <a name="stream-alerts-to-a-siem-soar-or-it-service-management-solution"></a>Streamen von Warnungen in eine SIEM-, SOAR- oder IT Service Management-Lösung

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender für Cloud kann Ihre Sicherheitswarnungen in die gängigsten Lösungen für Security Information und Event Management (SIEM), Security Orchestration Automated Response (SOAR) und IT Service Management (ITSM) übertragen.

Es gibt native Azure-Tools, mit denen sichergestellt wird, dass Sie die Warnungsdaten in allen heute gebräuchlichen Lösungen anzeigen können. Dazu zählen:

- **Microsoft Sentinel**
- **Splunk Enterprise and Splunk Cloud**
- **QRadar von IBM**
- **ServiceNow**
- **ArcSight**
- **Power BI**
- **Palo Alto Networks**

## <a name="stream-alerts-to-microsoft-sentinel"></a>Warnmeldungen an Microsoft Sentinel übertragen 

Defender for Cloud ist nativ mit Microsoft Sentinel integriert, der Cloud-nativen SIEM- und SOAR-Lösung von Azure. 

[Erfahren Sie mehr über Microsoft Sentinel](../sentinel/overview.md).

### <a name="microsoft-sentinels-connectors-for-defender-for-cloud"></a>Microsoft Sentinels Anschlüsse für Defender for Cloud

Microsoft Sentinel enthält integrierte Konnektoren für Microsoft Defender for Cloud auf Abonnement- und Mandantenebene:

- [Streaming von Warnmeldungen an Microsoft Sentinel auf Abonnementebene](../sentinel/connect-azure-security-center.md)
- [Verbinden Sie alle Abonnements in Ihrem Tenant mit Microsoft Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-security-center-auto-connect-to-sentinel/ba-p/1387539) 

Wenn Sie Defender für Cloud mit Microsoft Sentinel verbinden, wird der Status von Defender für Cloud-Warnungen, die in Microsoft Sentinel aufgenommen werden, zwischen den beiden Diensten synchronisiert. Wenn also beispielsweise eine Warnung in Defender for Cloud geschlossen wird, wird diese Warnung auch in Microsoft Sentinel als geschlossen angezeigt. Die Änderung des Status einer Warnung in Defender für Cloud wirkt sich nicht auf den Status aller Microsoft Sentinel **Vorfälle** aus, die die synchronisierte Microsoft Sentinel-Warnung enthalten, sondern nur auf den der synchronisierten Warnung selbst.

Durch die Aktivierung der Vorschaufunktion **Bidirektionale Alarmsynchronisierung** wird der Status der ursprünglichen Defender für Cloud-Alarme automatisch mit Microsoft Sentinel-Vorfällen synchronisiert, die Kopien dieser Defender für Cloud-Alarme enthalten. Wenn zum Beispiel ein Microsoft Sentinel-Vorfall, der eine Defender für Cloud-Warnung enthält, geschlossen wird, schließt Defender für Cloud automatisch die entsprechende ursprüngliche Warnung.

Erfahren Sie mehr unter [Verbinden Sie Defender für Cloud-Warnungen mit Microsoft Defender für Cloud](../sentinel/connect-azure-security-center.md).

> [!NOTE]
> Das Feature der bidirektionalen Warnungssynchronisierung ist in der Azure Government-Cloud nicht verfügbar. 

### <a name="configure-ingestion-of-all-audit-logs-into-microsoft-sentinel"></a>Konfigurieren Sie die Aufnahme aller Audit-Protokolle in Microsoft Sentinel 

Eine weitere Alternative zur Untersuchung von Defender für Cloud-Warnungen in Microsoft Sentinel ist das Streaming Ihrer Audit-Protokolle in Microsoft Sentinel:
    - [Herstellen einer Verbindung mit Windows-Sicherheitsereignissen](../sentinel/connect-windows-security-events.md)
    - [Sammeln von Daten aus Linux-basierten Quellen mithilfe von Syslog](../sentinel/connect-syslog.md)
    - [Verknüpfen von Daten aus dem Azure-Aktivitätsprotokoll](../sentinel/data-connectors-reference.md#azure-activity)

> [!TIP]
> Microsoft Sentinel wird auf der Grundlage des Datenvolumens abgerechnet, das zur Analyse in Microsoft Sentinel aufgenommen und im Azure Monitor Log Analytics-Arbeitsbereich gespeichert wird. Microsoft Sentinel bietet ein flexibles und berechenbares Preismodell. [Weitere Informationen finden Sie auf der Microsoft Sentinel-Preisseite](https://azure.microsoft.com/pricing/details/azure-sentinel/).


## <a name="stream-alerts-with-microsoft-graph-security-api"></a>Streamen von Warnungen mit der Microsoft Graph-Sicherheits-API

Defender für Cloud verfügt über eine sofort einsatzbereite Integration mit Microsoft Graph Security API. Es ist keine Konfiguration erforderlich, und es fallen keine zusätzlichen Kosten an. 

Mithilfe dieser API können Sie Warnungen von Ihrem **gesamten Mandanten** (und Daten von vielen anderen Microsoft-Sicherheitsprodukten) in Drittanbieter-SIEMs und andere beliebte Plattformen streamen:

- **Splunk Enterprise und Splunk Cloud**: [Verwenden des Microsoft Graph-Sicherheits-API-Add-Ons für Splunk](https://splunkbase.splunk.com/app/4564/) 
- **Power BI**: [Herstellen einer Verbindung mit der Microsoft Graph-Sicherheits-API in Power BI Desktop](/power-bi/connect-data/desktop-connect-graph-security)
- **ServiceNow**: [Installieren und Konfigurieren der Microsoft Graph-Sicherheits-API-Anwendung aus dem ServiceNow Store](https://docs.servicenow.com/bundle/orlando-security-management/page/product/secops-integration-sir/secops-integration-ms-graph/task/ms-graph-install.html)
- **QRadar** - [IBMs Geräteunterstützungsmodul für Microsoft Defender für Cloud über Microsoft Graph API](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/c_dsm_guide_ms_azure_security_center_overview.html) 
- **Palo Alto Networks**, **Anomali**, **Lookout**, **InSpark** und mehr: [Microsoft Graph-Sicherheits-API](https://www.microsoft.com/security/business/graph-security-api#office-MultiFeatureCarousel-09jr2ji)

[Erfahren Sie mehr über die Microsoft Graph-Sicherheits-API](https://www.microsoft.com/security/business/graph-security-api).


## <a name="stream-alerts-with-azure-monitor"></a>Streamen von Warnungen mit Azure Monitor 

Zum Streamen von Warnungen in **ArcSight**, **Splunk**, **SumoLogic**, Syslog-Server, **LogRhythm**, **Logz.io Cloud Observability Platform** und andere Überwachungslösungen verbinden Sie Defender für Cloud mit Azure Monitor über Azure Event Hubs:

1. Aktivieren Sie [kontinuierlichen Export](continuous-export.md), um Defender für Cloud-Warnungen in einen dedizierten Azure Event Hub auf Abonnementebene zu streamen. 
    > [!TIP]
    > Informationen zur Durchführung auf Verwaltungsgruppenebene mithilfe von Azure Policy finden Sie unter [Erstellen von Konfigurationen zur Automatisierung des fortlaufenden Exports](continuous-export.md?tabs=azure-policy#configure-continuous-export-at-scale-using-the-supplied-policies).

1. [Stellen Sie eine Verbindung zwischen dem Azure Event Hub und Ihrer bevorzugten Lösung mithilfe des integrierten Connectors von Azure Monitor her](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration).

1. Optional können Sie die unformatierten Protokolle in den Azure Event Hub streamen und eine Verbindung mit Ihrer bevorzugten Lösung herstellen. Weitere Informationen finden Sie unter [Verfügbare Überwachungsdaten](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#monitoring-data-available).

> [!TIP]
> Die Ereignisschemas der exportierten Datentypen finden Sie bei den [Event Hub-Ereignisschemas](https://aka.ms/ASCAutomationSchemas).


## <a name="next-steps"></a>Nächste Schritte

Auf dieser Seite wird erklärt, wie Sie sicherstellen, dass Ihre Microsoft Defender für Cloud-Warndaten im SIEM-, SOAR- oder ITSM-Tool Ihrer Wahl verfügbar sind. Verwandte Informationen finden Sie hier:

- [Was ist Microsoft Sentinel?](../sentinel/overview.md)
- [Alarmvalidierung in Microsoft Defender für Cloud](alert-validation.md) - Überprüfen Sie, ob Ihre Alarme richtig konfiguriert sind
- [Kontinuierlicher Export von Defender für Cloud-Daten](continuous-export.md)