---
title: Konfigurieren von Sicherheitswarnungen für Azure-Ressourcenrollen in Privileged Identity Management – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Sicherheitswarnungen für Azure-Ressourcenrollen in Azure AD Privileged Identity Management (PIM) konfigurieren.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae012d6c6fb332914da8083b264c5dd0241e7cd6
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669908"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-privileged-identity-management"></a>Konfigurieren von Sicherheitswarnungen für Azure-Ressourcenrollen in Privileged Identity Management (PIM)

Privileged Identity Management (PIM) generiert bei verdächtigen oder nicht sicheren Aktivitäten in Ihrer Azure Active Directory-Organisation (Azure AD) Warnungen. Wenn eine Warnung ausgelöst wird, wird sie auf der Seite „Warnungen“ angezeigt.

![Azure-Ressourcen – Auflisten der Warnung auf der Seite „Warnungen“, Risikostufe und Anzahl](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Überprüfen von Warnungen

Wählen Sie eine Warnung aus, um einen Bericht mit den Benutzern oder Rollen, die die Warnung ausgelöst haben, zusammen mit einer Anleitung zur Lösung anzuzeigen.

![Warnungenbericht mit Anzeige von letzter Überprüfungszeit, Beschreibung, Entschärfungsschritten, Typ, Schweregrad, Sicherheitsauswirkungen und Vorschlägen zur zukünftigen Verhinderung](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Alerts

| Warnung | severity | Trigger | Empfehlung |
| --- | --- | --- | --- |
| **Zu viele Besitzer zu Ressource zugewiesen** |Medium |Zu viele Benutzer verfügen über die Rolle „Besitzer“. |Überprüfen Sie die Benutzer in der Liste, und weisen Sie einigen Benutzern Rollen mit weniger Rechten zu. |
| **Zu viele dauerhafte Besitzer zu Ressource zugewiesen** |Medium |Zu viele Benutzer sind dauerhaft einer Rolle zugewiesen. |Überprüfen Sie die Benutzer in der Liste, und weisen Sie einige neu zu, damit für die Rollennutzung die Aktivierung erzwungen wird. |
| **Doppelte Rolle erstellt** |Medium |Mehrere Rollen verfügen über die gleichen Kriterien. |Verwenden Sie nur eine dieser Rollen. |

### <a name="severity"></a>severity

- **Hoch**: Aufgrund einer Richtlinienverletzung ist eine sofortige Aktion erforderlich. 
- **Mittel:** Es ist keine sofortige Aktion erforderlich, aber es wird eine potenzielle Richtlinienverletzung angezeigt.
- **Niedrig**: Es ist keine sofortige Aktion erforderlich, aber es wird eine vorzuziehende Richtlinienänderung vorgeschlagen.

## <a name="configure-security-alert-settings"></a>Konfigurieren der Einstellungen für Sicherheitswarnungen

Navigieren Sie auf der Seite „Warnungen“ zu **Einstellungen**.

![Seite „Warnungen“ mit hervorgehobenen „Einstellungen“](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

Passen Sie die Einstellungen für die verschiedenen Warnungen so an, dass sie für Ihre Umgebung und Sicherheitsziele funktionieren.

![Einstellungenseite für eine Warnung zum Aktivieren und Konfigurieren von Einstellungen](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren von Einstellungen für Azure-Ressourcenrollen in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
