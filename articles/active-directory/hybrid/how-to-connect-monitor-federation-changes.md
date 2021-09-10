---
title: Überwachen von Änderungen an der Verbundkonfiguration in Azure AD | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, wie Sie Änderungen an Ihrer Verbundkonfiguration mit Azure AD überwachen.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/21/2021
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 7ffc5980eed3268f299ee0073cfa810c17878e53
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2021
ms.locfileid: "114229707"
---
# <a name="monitor-changes-to-federation-configuration-in-your-azure-ad"></a>Überwachen von Änderungen an der Verbundkonfiguration in Azure AD

Wenn Sie einen Verbund Ihrer lokalen Umgebung mit Azure AD erstellen, wird eine Vertrauensstellung zwischen dem lokalen Identitätsanbieter und Azure AD eingerichtet. 

Aufgrund dieser Vertrauensstellung akzeptiert Azure AD das vom lokalen Identitätsanbieter nach der Authentifizierung ausgestellte Sicherheitstoken, um Zugriff auf Ressourcen zu gewähren, die durch Azure AD geschützt sind. 

Daher ist es von großer Bedeutung, dass diese Vertrauensstellung (Verbundkonfiguration) genau überwacht wird und jegliche ungewöhnliche oder verdächtige Aktivitäten erfasst werden.

Zum Überwachen der Vertrauensbeziehung empfiehlt es sich, Warnungen einzurichten, sodass Sie benachrichtigt werden, wenn Änderungen an der Verbundkonfiguration vorgenommen werden.


## <a name="set-up-alerts-to-monitor-the-trust-relationship"></a>Einrichten von Warnungen zum Überwachen der Vertrauensbeziehung

Führen Sie die folgenden Schritte aus, um Warnungen zur Überwachung der Vertrauensbeziehung einzurichten:

1. [Konfigurieren Sie Azure AD-Überwachungsprotokolle](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md), die an einen Azure Log Analytics-Arbeitsbereich gesendet werden. 
2. [Erstellen Sie eine Warnungsregel](../../azure-monitor/alerts/alerts-log.md), die basierend auf einer Azure AD-Protokollabfrage ausgelöst wird. 
3. [Fügen Sie der Warnungsregel eine Aktionsgruppe hinzu](../../azure-monitor/alerts/action-groups.md), die benachrichtigt wird, wenn die Warnungsbedingung erfüllt ist.  

Nach der Konfiguration der Umgebung sieht der Datenfluss wie folgt aus: 

 1. Azure AD-Protokolle werden gemäß der Aktivität im Mandanten aufgefüllt.  
 2. Die Protokollinformationen werden an den Azure Log Analytics-Arbeitsbereich übermittelt.  
 3. Ein Hintergrundauftrag von Azure Monitor führt die Protokollabfrage basierend auf der Konfiguration der Warnungsregel im obigen Konfigurationsschritt 2 aus.  
    ```
     AuditLogs 
     |  extend TargetResource = parse_json(TargetResources) 
     | where ActivityDisplayName contains "Set federation settings on domain" or ActivityDisplayName contains "Set domain authentication" 
     | project TimeGenerated, SourceSystem, TargetResource[0].displayName, AADTenantId, OperationName, InitiatedBy, Result, ActivityDisplayName, ActivityDateTime, Type 
     ```
     
 4. Wenn das Ergebnis der Abfrage mit der Warnungslogik übereinstimmt (also die Anzahl der Ergebnisse größer als oder gleich 1 ist), tritt die Aktionsgruppe in Kraft. Wir nehmen an, dass dies der Fall ist. Dann geht es mit Schritt 5 weiter.  
 5. Eine Benachrichtigung wird an die Aktionsgruppe gesendet, die während der Konfiguration der Warnung ausgewählt wurde.

 > [!NOTE]
 >  Zusätzlich zum Einrichten von Warnungen empfiehlt es sich, die konfigurierten Domänen in Ihrem Azure AD-Mandanten regelmäßig zu überprüfen und veraltete, nicht erkannte oder verdächtige Domänen zu entfernen. 




## <a name="next-steps"></a>Nächste Schritte

- [Integrieren von Azure AD-Protokollen mit Azure Monitor-Protokollen](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)
- [Erstellen, Anzeigen und Verwalten von Protokollwarnungen mithilfe von Azure Monitor](../../azure-monitor/alerts/alerts-log.md)
- [Verwalten der AD FS-Vertrauensstellung mit Azure AD und Azure AD Connect](how-to-connect-azure-ad-trust.md)
- [Best Practices zum Sichern von Active Directory-Verbunddiensten (AD FS)](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs)