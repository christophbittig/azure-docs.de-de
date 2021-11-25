---
title: Berechtigungen in Microsoft Sentinel | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Microsoft Sentinel die rollenbasierte Zugriffssteuerung in Azure zum Zuweisen von Berechtigungen für Benutzer verwendet und die zulässigen Aktionen für jede Rolle identifiziert.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 77d9af26c175de162576fc8948dc3b8d5eedcada
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132516986"
---
# <a name="permissions-in-microsoft-sentinel"></a>Berechtigungen in Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Sentinel verwendet die [rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)](../role-based-access-control/role-assignments-portal.md). Dabei werden [integrierte Rollen](../role-based-access-control/built-in-roles.md) bereitgestellt, die Benutzern, Gruppen und Diensten in Azure zugewiesen werden können.

Verwenden Sie Azure RBAC, um Rollen innerhalb Ihres Security Operations-Teams zu erstellen und zuzuweisen und damit angemessenen Zugriff auf Microsoft Sentinel zu ermöglichen. Mithilfe der verschiedenen Rollen können Sie präzise steuern, was Benutzer von Microsoft Sentinel anzeigen und welche Aktionen sie ausführen können. Azure-Rollen können direkt im Microsoft Sentinel-Arbeitsbereich zugewiesen werden (siehe Hinweis unten) oder in einem Abonnement oder einer Ressourcengruppe, zu dem bzw. der der Arbeitsbereich gehört, der von Microsoft Sentinel geerbt wird.

## <a name="roles-for-working-in-microsoft-sentinel"></a>Rollen für die Arbeit in Microsoft Sentinel

### <a name="microsoft-sentinel-specific-roles"></a>Microsoft Sentinel-spezifische Rollen

**Alle in Microsoft Sentinel integrierten Rollen gewähren Lesezugriff auf die Daten in Ihrem Microsoft Sentinel-Arbeitsbereich.**

- [Microsoft Sentinel-Leser](../role-based-access-control/built-in-roles.md#microsoft-sentinel-reader) können Daten, Incidents, Arbeitsmappen und andere Microsoft Sentinel-Ressourcen anzeigen.

- [Microsoft Sentinel-Antwortberechtigte](../role-based-access-control/built-in-roles.md#microsoft-sentinel-responder) können zusätzlich zu den oben genannten Aktionen auch Incidents verwalten (zuweisen, verwerfen usw.).

- [Microsoft Sentinel-Mitwirkende](../role-based-access-control/built-in-roles.md#microsoft-sentinel-contributor) können zusätzlich zu den oben aufgeführten Aktionen Arbeitsmappen, Analyseregeln und andere Microsoft Sentinel-Ressourcen erstellen und bearbeiten.

- Die Rolle [Mitwirkender für Microsoft Sentinel-Automatisierung](../role-based-access-control/built-in-roles.md#microsoft-sentinel-contributor) ermöglicht es Microsoft Sentinel, Playbooks zu Automatisierungsregeln hinzuzufügen. Sie ist nicht für Benutzerkonten vorgesehen.

> [!NOTE]
>
> - Um optimale Ergebnisse zu erzielen, sollten diese Rollen für die **Ressourcengruppe** zugewiesen werden, die den Microsoft Sentinel-Arbeitsbereich enthält. Auf diese Weise gelten die Rollen für alle Ressourcen, die zur Unterstützung von Microsoft Sentinel bereitgestellt werden, da diese Ressourcen auch in derselben Ressourcengruppe platziert werden sollten.
>
> - Eine andere Möglichkeit besteht darin, die Rollen direkt für den Microsoft Sentinel-**Arbeitsbereich** selbst zuzuweisen. Wenn Sie so vorgehen, müssen Sie die gleichen Rollen auch für die SecurityInsights-**Lösungsressource** in diesem Arbeitsbereich zuweisen. Sie müssen sie möglicherweise auch anderen Ressourcen zuweisen, und Sie müssen die Rollenzuweisungen für Ressourcen kontinuierlich verwalten.

### <a name="additional-roles-and-permissions"></a>Weitere Rollen und Berechtigungen

Benutzern mit besonderen Arbeitsanforderungen müssen möglicherweise zusätzliche Rollen oder spezifische Berechtigungen zugewiesen werden, um ihre Aufgaben erfüllen zu können.

- **Arbeiten mit Playbooks zur Automatisierung von Reaktionen auf Bedrohungen**

    Microsoft Sentinel verwendet **Playbooks** für automatische Reaktionen auf Bedrohungen. Playbooks nutzen **Azure Logic Apps** und sind eine separate Azure-Ressource. Sie können bestimmten Mitgliedern Ihres Security Operations-Teams die Option zuweisen, Logic Apps für SOAR-Vorgänge (Sicherheitsorchestrierung, Automatisierung und Reaktion) zu verwenden. Sie können die Rolle [Mitwirkender für Logik-Apps](../role-based-access-control/built-in-roles.md#logic-app-contributor) verwenden, um eine explizite Berechtigung für die Verwendung von Playbooks zu erteilen.

- **Verbinden von Datenquellen mit Microsoft Sentinel**

    Damit ein Benutzer **Data Connectors** hinzufügen kann, müssen Sie dem Benutzer Schreibberechtigungen im Microsoft Sentinel-Arbeitsbereich zuweisen. Beachten Sie außerdem die erforderlichen zusätzlichen Berechtigungen für jeden Connector, die auf der Seite zum jeweiligen Connector aufgeführt werden.

- **Gastbenutzer, die Incidents zuweisen**

    Wenn ein Gastbenutzer in der Lage sein muss, Incidents zuzuweisen, muss dem Benutzer zusätzlich zur Rolle „Microsoft Sentinel-Antwortberechtigter“ auch die Rolle [Verzeichnisleseberechtigte](../active-directory/roles/permissions-reference.md#directory-readers) zugewiesen werden. Beachten Sie, dass diese Rolle *keine* Azure-Rolle, sondern eine **Azure Active Directory**-Rolle ist, und dass Standardbenutzern (keine Gastbenutzer) diese Rolle standardmäßig zugewiesen ist.

- **Erstellen und Löschen von Arbeitsmappen**

    Damit ein Benutzer eine Microsoft Sentinel-Arbeitsmappe erstellen und löschen kann, muss ihm auch die Azure Monitor-Rolle [Mitwirkender an der Überwachung](../role-based-access-control/built-in-roles.md#monitoring-contributor) zugewiesen werden. Diese Rolle ist nur zum Erstellen und Löschen von Arbeitsmappen erforderlich, nicht aber für ihre *Verwendung*.

### <a name="other-roles-you-might-see-assigned"></a>Weitere Rollen, die möglicherweise zugewiesen sind

Bei der Zuweisung von Microsoft Sentinel-spezifischen Azure-Rollen können Sie auf andere Azure- und Log Analytics-Rollen stoßen, die Benutzern für andere Zwecke zugewiesen worden sein können. Sie sollten sich bewusst sein, dass diese Rollen eine breitere Palette von Berechtigungen festlegen, die auch den Zugriff auf Ihren Microsoft Sentinel-Arbeitsbereich und andere Ressourcen umfasst:

- **Azure-Rollen:** [Besitzer](../role-based-access-control/built-in-roles.md#owner), [Mitwirkender](../role-based-access-control/built-in-roles.md#contributor) und [Leser](../role-based-access-control/built-in-roles.md#reader). Azure-Rollen gewähren Zugriff auf alle Ihre Azure-Ressourcen, einschließlich Log Analytics-Arbeitsbereiche und Microsoft Sentinel-Ressourcen.

- **Log Analytics-Rollen:** [Log Analytics-Mitwirkender](../role-based-access-control/built-in-roles.md#log-analytics-contributor) und [Log Analytics-Leser](../role-based-access-control/built-in-roles.md#log-analytics-reader). Log Analytics-Rollen gewähren Zugriff auf Ihre Log Analytics-Arbeitsbereiche.

Beispielsweise kann ein Benutzer, dem die Rolle **Microsoft Sentinel-Leser**, aber nicht die Rolle **Microsoft Sentinel-Mitwirkender** zugewiesen wurde, immer noch Ressourcen in Microsoft Sentinel bearbeiten, wenn ihm die Rolle **Mitwirkender** auf Azure-Ebene zugewiesen wurde. Wenn Sie daher einem Benutzer Berechtigungen nur in Microsoft Sentinel gewähren möchten, sollten Sie die vorherigen Berechtigungen dieses Benutzers sorgfältig entfernen und sicherstellen, dass Sie keinen erforderlichen Zugriff auf eine andere Ressource aufheben.

## <a name="microsoft-sentinel-roles-and-allowed-actions"></a>Microsoft Sentinel-Rollen und zulässige Aktionen

In der folgenden Tabelle sind die Microsoft Sentinel-Rollen und die jeweils zulässigen Aktionen in Microsoft Sentinel zusammengefasst:

| Role | Playbooks erstellen und ausführen| Analyseregeln und andere Microsoft Sentinel-Ressourcen erstellen und bearbeiten[*](#workbooks) | Incidents verwalten (verwerfen, zuweisen usw.) | Anzeigen von Daten, Incidents, Arbeitsmappen und anderen Microsoft Sentinel-Ressourcen |
|---|---|---|---|---|
| Microsoft Sentinel-Leser | -- | -- | -- | &#10003; |
| Microsoft Sentinel-Antwortdienst | -- | -- | &#10003; | &#10003; |
| Microsoft Sentinel-Mitwirkender | -- | &#10003; | &#10003; | &#10003; |
| Microsoft Sentinel-Mitwirkender und Mitwirkender für Logik-Apps | &#10003; | &#10003; | &#10003; | &#10003; |
| | | | | |

<a name=workbooks></a>* Zum Erstellen und Löschen von Arbeitsmappen ist die zusätzliche Rolle [Mitwirkender an der Überwachung](../role-based-access-control/built-in-roles.md#monitoring-contributor) erforderlich. Weitere Informationen finden Sie unter [Weitere Rollen und Berechtigungen](#additional-roles-and-permissions).
## <a name="custom-roles-and-advanced-azure-rbac"></a>Benutzerdefinierte Rollen und erweiterte Azure RBAC-Rollen

- **Benutzerdefinierte Rollen**. Zusätzlich zu integrierten Azure-Rollen oder anstelle von diesen können Sie benutzerdefinierte Azure-Rollen für Microsoft Sentinel erstellen. Benutzerdefinierte Azure-Rollen für Microsoft Sentinel werden genauso wie andere [benutzerdefinierte Azure-Rollen](../role-based-access-control/custom-roles-rest.md#create-a-custom-role) basierend auf [bestimmten Berechtigungen für Microsoft Sentinel](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) und für [Azure Log Analytics-Ressourcen](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights) erstellt.

- **RBAC in Log Analytics**. Sie können die erweiterte rollenbasierte Zugriffssteuerung in Azure von Log Analytics für die Daten in Ihrem Microsoft Sentinel-Arbeitsbereich verwenden. Dies umfasst sowohl auf dem Datentyp basierendes Azure RBAC als auch Azure RBAC im Ressourcenkontext. Weitere Informationen finden Sie unter

    - [Verwalten von Protokolldaten und Arbeitsbereichen in Azure Monitor](../azure-monitor/logs/manage-access.md#manage-access-using-workspace-permissions)

    - [Ressourcenkontext-RBAC für Microsoft Sentinel](resource-context-rbac.md)
    - [Table-level RBAC (RBAC auf Tabellenebene)](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043)

    Die rollenbasierte Zugriffssteuerung (RBAC) im Ressourcenkontext und RBAC auf Tabellenebene sind zwei Methoden der Bereitstellung von Zugriff auf bestimmte Daten im Microsoft Sentinel-Arbeitsbereich, ohne Zugriff auf die gesamte Microsoft Sentinel-Umgebung zu gewähren.

## <a name="role-recommendations"></a>Empfehlungen für Rollen

Nachdem Sie verstanden haben, wie Rollen und Berechtigungen in Microsoft Sentinel funktionieren, empfiehlt sich die folgende Anleitung mit bewährten Methoden zur Zuweisung von Rollen zu Ihren Benutzern:

|Benutzertyp  |Rolle |Resource group  |BESCHREIBUNG  |
|---------|---------|---------|---------|
|**Sicherheitsanalysten**     | [Microsoft Sentinel-Antwortdienst](../role-based-access-control/built-in-roles.md#microsoft-sentinel-responder)        | Ressourcengruppe von Microsoft Sentinel        | Anzeigen von Daten, Incidents, Arbeitsmappen und anderen Microsoft Sentinel-Ressourcen <br><br>Verwalten von Incidents, z. B. Zuweisen oder Schließen von Incidents        |
|     | [Logic Apps-Mitwirkender](../role-based-access-control/built-in-roles.md#logic-app-contributor)        | Ressourcengruppe von Microsoft Sentinel oder die Ressourcengruppe, in der Ihre Playbooks gespeichert sind        | Anfügen von Playbooks an Analyse- und Automatisierungsregeln und Ausführen von Playbooks <br><br>**Hinweis**: Mit dieser Rolle können Benutzer auch Playbooks ändern.         |
|**Sicherheitstechniker**     | [Microsoft Sentinel-Mitwirkender](../role-based-access-control/built-in-roles.md#microsoft-sentinel-contributor)       |Ressourcengruppe von Microsoft Sentinel         |   Anzeigen von Daten, Incidents, Arbeitsmappen und anderen Microsoft Sentinel-Ressourcen <br><br>Verwalten von Incidents, z. B. Zuweisen oder Schließen von Incidents <br><br>Erstellen und Bearbeiten von Arbeitsmappen, Analyseregeln und anderen Microsoft Sentinel-Ressourcen      |
|     | [Logic Apps-Mitwirkender](../role-based-access-control/built-in-roles.md#logic-app-contributor)        | Ressourcengruppe von Microsoft Sentinel oder die Ressourcengruppe, in der Ihre Playbooks gespeichert sind        | Anfügen von Playbooks an Analyse- und Automatisierungsregeln und Ausführen von Playbooks <br><br>**Hinweis**: Mit dieser Rolle können Benutzer auch Playbooks ändern.         |
|  **Dienstprinzipal**   | [Microsoft Sentinel-Mitwirkender](../role-based-access-control/built-in-roles.md#microsoft-sentinel-contributor)      |  Ressourcengruppe von Microsoft Sentinel       | Automatisierte Konfiguration für Verwaltungsaufgaben |
|     |         |        | |

> [!TIP]
> Abhängig von den erfassten oder überwachten Daten sind möglicherweise zusätzliche Rollen erforderlich. Beispielsweise können Azure AD-Rollen wie z  B. „Globaler Administrator“ oder „Sicherheitsadministrator“ erforderlich sein, um Datenconnectors für Dienste in anderen Microsoft-Portalen einzurichten.
>

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie mit Rollen für Microsoft Sentinel-Benutzer arbeiten und was die einzelnen Rollen den Benutzern ermöglichen.

Blogbeiträge zur Sicherheit und Compliance von Azure finden Sie im [Microsoft Sentinel-Blog](https://aka.ms/azuresentinelblog).
