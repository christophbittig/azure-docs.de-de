---
title: Beheben von Problemen bei Richtlinien für bedingten Zugriff – Azure Active Directory
description: Diagnostizieren Sie Änderungen an der Richtlinie für bedingten Zugriff mit den Azure AD Überwachungsprotokollen.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 08/09/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7683b64449a32013d7ac5e548ea9acaf85c51666
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128561620"
---
# <a name="troubleshooting-conditional-access-policy-changes"></a>Beheben von Problemen bei Richtlinien für bedingten Zugriff

Das Überwachungsprotokoll von Azure Active Directory (Azure AD) ist eine wertvolle Informationsquelle bei der Problembehandlung, um festzustellen, warum und wie Änderungen der Richtlinie für bedingten Zugriff in Ihrer Umgebung vorgenommen wurden.

Überwachungsprotokolldaten werden standardmäßig nur 30 Tage lang aufbewahrt, was möglicherweise nicht für jede Organisation lang genug ist. Organisationen können Daten über einen längeren Zeitraum speichern, indem sie die Diagnoseeinstellungen in Azure AD ändern, um: 

- Daten an einen Log Analytics-Arbeitsbereich zu senden
- Daten in einem Speicherkonto zu archivieren
- Daten an einen Event Hub zu streamen
- Daten an eine Partnerlösung zu senden
 
Sie finden diese Optionen im **Azure-Portal** > **Azure Active Directory**, **Diagnoseeinstellungen** > **Einstellung bearbeiten**. Wenn Sie keine Diagnoseeinstellung haben, befolgen Sie die Anweisungen im Artikel zum [Erstellen von Diagnoseeinstellungen, um Plattformprotokolle und Metriken an verschiedene Ziele zu senden](../../azure-monitor/essentials/diagnostic-settings.md), um eine zu erstellen. 

## <a name="use-the-audit-log"></a>Verwenden des Überwachungsprotokolls

1. Melden Sie sich beim **Azure-Portal** als globaler Administrator, Sicherheitsadministrator oder Administrator für bedingten Zugriff an.
1. Navigieren Sie zu **Azure Active Directory** > **Überwachungsprotokolle**.
1. Wählen Sie den **Datumsbereich** aus, in dem Sie die Abfrage durchführen möchten.
1. Wählen Sie **Aktivität** und dann eine der folgenden Optionen aus:
   1. **Richtlinie für bedingten Zugriff hinzufügen**:Diese Aktivität listet neu erstellte Richtlinien auf.
   1. **Richtlinie für bedingten Zugriff aktualisieren**:Diese Aktivität listet neu geänderte Richtlinien auf.
   1. **Richtlinie für bedingten Zugriff löschen**:Diese Aktivität listet gelöschte Richtlinien auf.

:::image type="content" source="media/troubleshoot-policy-changes-audit-log/old-and-new-policy-properties.png" alt-text="Überwachungsprotokolleintrag mit alten und neuen JSON-Werten für die Richtlinie für bedingten Zugriff" lightbox="media/troubleshoot-policy-changes-audit-log/old-and-new-policy-properties.png":::

## <a name="use-log-analytics"></a>Verwenden von Log Analytics

Log Analytics ermöglicht Organisationen das Abfragen von Daten mit vordefinierten Abfragen oder benutzerdefinierten erstellten Kusto-Abfragen. Weitere Informationen finden Sie unter [Erste Schritte mit Protokollabfragen in Azure Monitor](../../azure-monitor/logs/get-started-queries.md).

:::image type="content" source="media/troubleshoot-policy-changes-audit-log/log-analytics-new-old-value.png" alt-text="Log Analytics-Abfrage nach Aktualisierungen von Richtlinien für bedingten Zugriff, die die Position des neuen und alten Werts anzeigt" lightbox="media/troubleshoot-policy-changes-audit-log/log-analytics-new-old-value.png":::

Ermitteln Sie nach der Aktivierung den Zugriff auf Log Analytics im **Azure-Portal** > **Azure AD** > **Log Analytics**. Die Tabelle, die für Administratoren für bedingten Zugriff am wichtigsten ist, ist **AuditLogs**.

```kusto
AuditLogs 
| where OperationName == "Update conditional access policy"
```

Änderungen befinden sich unter **TargetResources** > **modifiedProperties**.

## <a name="reading-the-values"></a>Lesen der Werte

Die alten und neuen Werte aus dem Überwachungsprotokoll und von Log Analytics liegen im JSON-Format vor. Vergleichen Sie die beiden Werte, um die Änderungen an der Richtlinie festzustellen.

Beispiel für eine alte Richtlinie:

```json
{
    "conditions": {
        "applications": {
            "applicationFilter": null,
            "excludeApplications": [
            ],
            "includeApplications": [
                "797f4846-ba00-4fd7-ba43-dac1f8f63013"
            ],
            "includeAuthenticationContextClassReferences": [
            ],
            "includeUserActions": [
            ]
        },
        "clientAppTypes": [
            "browser",
            "mobileAppsAndDesktopClients"
        ],
        "servicePrincipalRiskLevels": [
        ],
        "signInRiskLevels": [
        ],
        "userRiskLevels": [
        ],
        "users": {
            "excludeGroups": [
                "eedad040-3722-4bcb-bde5-bc7c857f4983"
            ],
            "excludeRoles": [
            ],
            "excludeUsers": [
            ],
            "includeGroups": [
            ],
            "includeRoles": [
            ],
            "includeUsers": [
                "All"
            ]
        }
    },
    "displayName": "Common Policy - Require MFA for Azure management",
    "grantControls": {
        "builtInControls": [
            "mfa"
        ],
        "customAuthenticationFactors": [
        ],
        "operator": "OR",
        "termsOfUse": [
            "a0d3eb5b-6cbe-472b-a960-0baacbd02b51"
        ]
    },
    "id": "334e26e9-9622-4e0a-a424-102ed4b185b3",
    "modifiedDateTime": "2021-08-09T17:52:40.781994+00:00",
    "state": "enabled"
}

```

Beispiel für eine aktualisierte Richtlinie:

```json
{
    "conditions": {
        "applications": {
            "applicationFilter": null,
            "excludeApplications": [
            ],
            "includeApplications": [
                "797f4846-ba00-4fd7-ba43-dac1f8f63013"
            ],
            "includeAuthenticationContextClassReferences": [
            ],
            "includeUserActions": [
            ]
        },
        "clientAppTypes": [
            "browser",
            "mobileAppsAndDesktopClients"
        ],
        "servicePrincipalRiskLevels": [
        ],
        "signInRiskLevels": [
        ],
        "userRiskLevels": [
        ],
        "users": {
            "excludeGroups": [
                "eedad040-3722-4bcb-bde5-bc7c857f4983"
            ],
            "excludeRoles": [
            ],
            "excludeUsers": [
            ],
            "includeGroups": [
            ],
            "includeRoles": [
            ],
            "includeUsers": [
                "All"
            ]
        }
    },
    "displayName": "Common Policy - Require MFA for Azure management",
    "grantControls": {
        "builtInControls": [
            "mfa"
        ],
        "customAuthenticationFactors": [
        ],
        "operator": "OR",
        "termsOfUse": [
        ]
    },
    "id": "334e26e9-9622-4e0a-a424-102ed4b185b3",
    "modifiedDateTime": "2021-08-09T17:52:54.9739405+00:00",
    "state": "enabled"
}

``` 

Im obigen Beispiel enthält die aktualisierte Richtlinie keine Nutzungsbedingungen in Gewährungssteuerelementen.

### <a name="restoring-conditional-access-policies"></a>Wiederherstellen von Richtlinien für bedingten Zugriff

Weitere Informationen zum programmgesteuerten Aktualisieren Ihrer Richtlinien für bedingten Zugriff mithilfe der Microsoft Graph-API finden Sie im Artikel [Bedingter Zugriff: Programmgesteuerter Zugriff](howto-conditional-access-apis.md).

## <a name="next-steps"></a>Nächste Schritte

- [Was ist die Azure Active Directory-Überwachung?](../reports-monitoring/overview-monitoring.md)
- [Installieren und Verwenden der Log Analytics-Ansichten für Azure Active Directory](../reports-monitoring/howto-install-use-log-analytics-views.md)
- [Bedingter Zugriff: Programmgesteuerter Zugriff](howto-conditional-access-apis.md)
