---
title: Exportieren und Verwenden von Azure Active Directory Identity Protection-Daten
description: Erfahren Sie, wie Sie unter Verwendung von langfristigen Daten in Azure Active Directory Identity Protection Untersuchungen durchführen.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 07/30/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: f723b6bf700b1dc18bea90b4ed0be00356c716a1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132300744"
---
# <a name="how-to-export-risk-data"></a>Anleitung: Exportieren von Risikodaten

Azure AD speichert Berichte und Sicherheitssignale für einen definierten Zeitraum. Wenn es um Risikoinformationen geht, ist dies möglicherweise nicht lang genug.

| Bericht/Signal | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| --- | --- | --- | --- |
| Überwachungsprotokolle | 7 Tage | 30 Tage | 30 Tage |
| Anmeldungen | 7 Tage | 30 Tage | 30 Tage |
| Verwendung von Azure AD MFA | 30 Tage | 30 Tage | 30 Tage |
| Riskante Anmeldungen | 7 Tage | 30 Tage | 30 Tage |

Organisationen können Daten für längere Zeiträume speichern, indem sie die Diagnoseeinstellungen in Azure AD so ändern, dass **RiskyUsers**- und **UserRiskEvents**-Daten an einen Log Analytics-Arbeitsbereich gesendet, Daten in einem Speicherkonto archiviert, Daten an einen Event Hub gestreamt oder Daten an eine Partnerlösung gesendet werden. Sie finden diese Optionen im **Azure-Portal** > **Azure Active Directory**, **Diagnoseeinstellungen** > **Einstellung bearbeiten**. Wenn Sie keine Diagnoseeinstellung haben, befolgen Sie die Anweisungen im Artikel zum [Erstellen von Diagnoseeinstellungen, um Plattformprotokolle und Metriken an verschiedene Ziele zu senden](../../azure-monitor/essentials/diagnostic-settings.md), um eine zu erstellen.

>[!NOTE]
>Die Diagnoseeinstellungen für „RiskyUsers“ und „UserRiskEvents“ befinden sich derzeit in der öffentlichen Vorschau.

[ ![Bildschirm „Diagnoseeinstellungen“ in Azure AD mit der bestehenden Konfiguration](./media/howto-export-risk-data/change-diagnostic-setting-in-portal.png) ](./media/howto-export-risk-data/change-diagnostic-setting-in-portal.png#lightbox)

## <a name="log-analytics"></a>Log Analytics

Log Analytics ermöglicht Organisationen das Abfragen von Daten mit vordefinierten Abfragen oder benutzerdefinierten erstellten Kusto-Abfragen. Weitere Informationen finden Sie unter [Erste Schritte mit Protokollabfragen in Azure Monitor](../../azure-monitor/logs/get-started-queries.md).

Nach der Aktivierung erhalten Sie den Zugriff auf Log Analytics im **Azure-Portal** > **Azure AD** > **Log Analytics**. Folgende Tabellen sind für Identity Protection-Administratoren am wichtigsten: **AADRiskyUsers** und **AADUserRiskEvents**.

- AADRiskyUsers: Stellt Daten wie den Bericht **Riskante Benutzer** in Identity Protection zur Verfügung.
- AADUserRiskEvents: Stellt Daten wie den Bericht **Risikoerkennungen** in Identity Protection zur Verfügung.

[ ![Log Analytics-Ansicht mit einer Abfrage der Tabelle „AADUserRiskEvents“ und den fünf wichtigsten Ereignissen](./media/howto-export-risk-data/log-analytics-view-query-user-risk-events.png) ](./media/howto-export-risk-data/log-analytics-view-query-user-risk-events.png#lightbox)

In der obigen Abbildung wurde die folgende Abfrage ausgeführt, um die letzten fünf ausgelösten Risikoerkennungen anzuzeigen. 

```kusto
AADUserRiskEvents
| take 5
```

Eine weitere Möglichkeit besteht in der Abfrage der Tabelle „AADRiskyUsers“, um alle riskanten Benutzer anzuzeigen.

```kusto
AADRiskyUsers
```

> [!NOTE]
> Log Analytics hat nur Einblick in Daten, während sie gestreamt werden. Ereignisse vor dem Aktivieren des Ereignisversands von Azure AD werden nicht angezeigt.

## <a name="storage-account"></a>Speicherkonto

Wenn Sie Protokolle an ein Azure-Speicherkonto weiterleiten, können Sie sie länger aufbewahren als die Standardaufbewahrungsdauer vorgibt. Weitere Informationen finden Sie im Artikel [Tutorial: Archivieren von Azure AD-Protokollen in einem Azure Storage-Konto](../reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md).

## <a name="azure-event-hubs"></a>Azure Event Hubs

Azure Event Hubs können eingehende Daten aus Quellen wie Azure AD Identity Protection anzeigen sowie Echtzeitanalyse und Korrelation bereitstellen. Weitere Informationen finden Sie im Artikel [Tutorial: Streamen von Azure Active Directory-Protokollen an einen Azure Event Hub](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).

## <a name="other-options"></a>Weitere Optionen

Organisationen können auch [Azure AD-Daten zur weiteren Verarbeitung mit Microsoft Sentinel verbinden](../../sentinel/data-connectors-reference.md#azure-active-directory-identity-protection).

Organisationen können die [Microsoft Graph-API verwenden, um programmgesteuert mit Risikoereignissen zu interagieren](howto-identity-protection-graph-api.md).

## <a name="next-steps"></a>Nächste Schritte

- [Was ist die Azure Active Directory-Überwachung?](../reports-monitoring/overview-monitoring.md)
- [Installieren und Verwenden der Log Analytics-Ansichten für Azure Active Directory](../reports-monitoring/howto-install-use-log-analytics-views.md)
- [Verbinden von Daten aus Azure AD Identity Protection](../../sentinel/data-connectors-reference.md#azure-active-directory-identity-protection)
- [Azure Active Directory Identity Protection und das Microsoft Graph PowerShell SDK](howto-identity-protection-graph-api.md)
- [Tutorial: Streamen von Azure Active Directory-Protokollen an einen Azure Event Hub](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)
