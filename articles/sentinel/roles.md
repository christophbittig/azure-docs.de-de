---
title: Berechtigungen in Azure Sentinel | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Azure Sentinel die rollenbasierte Zugriffssteuerung in Azure zum Zuweisen von Berechtigungen für Benutzer verwendet und die zulässigen Aktionen für jede Rolle identifiziert.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/11/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2f70dc3f062adb89977e103721b01e78ab638092
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131022966"
---
# <a name="permissions-in-azure-sentinel"></a>Berechtigungen in Azure Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Azure Sentinel verwendet die [rollenbasierte Zugriffssteuerung in Azure](../role-based-access-control/role-assignments-portal.md) (Azure Role-Based Access Control, Azure RBAC). Dabei werden [integrierte Rollen](../role-based-access-control/built-in-roles.md) bereitgestellt, die Benutzern, Gruppen und Diensten in Azure zugewiesen werden können.

Verwenden Sie Azure RBAC, um Rollen innerhalb Ihres Security Operations-Teams zu erstellen und zuzuweisen und damit angemessenen Zugriff auf Azure Sentinel zu ermöglichen. Mithilfe der verschiedenen Rollen können Sie präzise steuern, was Benutzer von Azure Sentinel anzeigen und welche Aktionen sie ausführen können. Azure-Rollen können direkt im Azure Sentinel-Arbeitsbereich zugewiesen werden (siehe Hinweis unten) oder in einem Abonnement oder einer Ressourcengruppe, zu dem bzw. der der Arbeitsbereich gehört, der von Azure Sentinel geerbt wird.

## <a name="roles-for-working-in-azure-sentinel"></a>Rollen für das Arbeiten in Azure Sentinel

### <a name="azure-sentinel-specific-roles"></a>Azure Sentinel-spezifische Rollen

**Alle in Azure Sentinel integrierten Rollen gewähren Lesezugriff auf die Daten in Ihrem Azure Sentinel-Arbeitsbereich.**

- [Azure Sentinel-Leser](../role-based-access-control/built-in-roles.md#azure-sentinel-reader) können Daten, Incidents, Arbeitsmappen und andere Azure Sentinel-Ressourcen anzeigen.

- [Azure Sentinel-Antwortberechtigte](../role-based-access-control/built-in-roles.md#azure-sentinel-responder) können zusätzlich zu den oben genannten Aktionen auch Incidents verwalten (zuweisen, verwerfen usw.).

- [Azure Sentinel-Mitwirkende](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor) können zusätzlich zu den oben aufgeführten Aktionen Arbeitsmappen, Analyseregeln und andere Azure Sentinel-Ressourcen erstellen und bearbeiten.

- Die Rolle [Mitwirkender für Azure Sentinel-Automatisierung](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor) ermöglicht es Azure Sentinel, Playbooks zu Automatisierungsregeln hinzuzufügen. Sie ist nicht für Benutzerkonten vorgesehen.

> [!NOTE]
>
> - Um optimale Ergebnisse zu erzielen, sollten diese Rollen für die **Ressourcengruppe** zugewiesen werden, die den Azure Sentinel-Arbeitsbereich enthält. Auf diese Weise gelten die Rollen für alle Ressourcen, die zur Unterstützung von Azure Sentinel bereitgestellt werden, da diese Ressourcen auch in derselben Ressourcengruppe platziert werden sollten.
>
> - Eine andere Möglichkeit besteht darin, die Rollen direkt für den Azure Sentinel-**Arbeitsbereich** selbst zuzuweisen. Wenn Sie so vorgehen, müssen Sie die gleichen Rollen auch für die SecurityInsights-**Lösungsressource** in diesem Arbeitsbereich zuweisen. Sie müssen sie möglicherweise auch anderen Ressourcen zuweisen, und Sie müssen die Rollenzuweisungen für Ressourcen kontinuierlich verwalten.

### <a name="additional-roles-and-permissions"></a>Weitere Rollen und Berechtigungen

Benutzern mit besonderen Arbeitsanforderungen müssen möglicherweise zusätzliche Rollen oder spezifische Berechtigungen zugewiesen werden, um ihre Aufgaben erfüllen zu können.

- **Arbeiten mit Playbooks zur Automatisierung von Reaktionen auf Bedrohungen**

    Azure Sentinel verwendet **Playbooks** für automatische Reaktionen auf Bedrohungen. Playbooks nutzen **Azure Logic Apps** und sind eine separate Azure-Ressource. Sie können bestimmten Mitgliedern Ihres Security Operations-Teams die Option zuweisen, Logic Apps für SOAR-Vorgänge (Sicherheitsorchestrierung, Automatisierung und Reaktion) zu verwenden. Sie können die Rolle [Mitwirkender für Logik-Apps](../role-based-access-control/built-in-roles.md#logic-app-contributor) verwenden, um eine explizite Berechtigung für die Verwendung von Playbooks zu erteilen.

- **Verbinden von Datenquellen mit Azure Sentinel**

    Damit ein Benutzer **Data Connectors** hinzufügen kann, müssen Sie dem Benutzer Schreibberechtigungen im Azure Sentinel-Arbeitsbereich zuweisen. Beachten Sie außerdem die erforderlichen zusätzlichen Berechtigungen für jeden Connector, die auf der Seite zum jeweiligen Connector aufgeführt werden.

- **Gastbenutzer, die Incidents zuweisen**

    Wenn ein Gastbenutzer in der Lage sein muss, Incidents zuzuweisen, muss dem Benutzer zusätzlich zur Rolle „Azure Sentinel-Antwortberechtigter“ auch die Rolle [Verzeichnisleseberechtigte](../active-directory/roles/permissions-reference.md#directory-readers) zugewiesen werden. Beachten Sie, dass diese Rolle *keine* Azure-Rolle, sondern eine **Azure Active Directory**-Rolle ist, und dass Standardbenutzern (keine Gastbenutzer) diese Rolle standardmäßig zugewiesen ist.

- **Erstellen und Löschen von Arbeitsmappen**

    Damit ein Benutzer eine Azure Sentinel-Arbeitsmappe erstellen und löschen kann, muss ihm auch die Azure Monitor-Rolle [Mitwirkender an der Überwachung](../role-based-access-control/built-in-roles.md#monitoring-contributor) zugewiesen werden. Diese Rolle ist nur zum Erstellen und Löschen von Arbeitsmappen erforderlich, nicht aber für ihre *Verwendung*.

### <a name="other-roles-you-might-see-assigned"></a>Weitere Rollen, die möglicherweise zugewiesen sind

Bei der Zuweisung von Azure Sentinel-spezifischen Azure-Rollen können Sie auf andere Azure- und Log Analytics-Rollen stoßen, die Benutzern für andere Zwecke zugewiesen worden sein können. Sie sollten sich bewusst sein, dass diese Rollen eine breitere Palette von Berechtigungen festlegen, die auch den Zugriff auf Ihren Azure Sentinel-Arbeitsbereich und andere Ressourcen umfasst:

- **Azure-Rollen:** [Besitzer](../role-based-access-control/built-in-roles.md#owner), [Mitwirkender](../role-based-access-control/built-in-roles.md#contributor) und [Leser](../role-based-access-control/built-in-roles.md#reader). Azure-Rollen gewähren Zugriff auf alle Ihre Azure-Ressourcen, einschließlich Log Analytics-Arbeitsbereiche und Azure Sentinel-Ressourcen.

- **Log Analytics-Rollen:** [Log Analytics-Mitwirkender](../role-based-access-control/built-in-roles.md#log-analytics-contributor) und [Log Analytics-Leser](../role-based-access-control/built-in-roles.md#log-analytics-reader). Log Analytics-Rollen gewähren Zugriff auf Ihre Log Analytics-Arbeitsbereiche.

Beispielsweise kann ein Benutzer, dem die Rolle **Azure Sentinel-Leser**, aber nicht die Rolle **Azure Sentinel-Mitwirkender** zugewiesen wurde, immer noch Ressourcen in Azure Sentinel bearbeiten, wenn ihm die Rolle **Mitwirkender** auf Azure-Ebene zugewiesen wurde. Wenn Sie daher einem Benutzer Berechtigungen nur in Azure Sentinel gewähren möchten, sollten Sie die vorherigen Berechtigungen dieses Benutzers sorgfältig entfernen und sicherstellen, dass Sie keinen erforderlichen Zugriff auf eine andere Ressource aufheben.

## <a name="azure-sentinel-roles-and-allowed-actions"></a>Azure Sentinel-Rollen und zulässige Aktionen

In der folgenden Tabelle sind die Azure Sentinel-Rollen und die jeweils zulässigen Aktionen in Azure Sentinel zusammengefasst:

| Role | Playbooks erstellen und ausführen| Analyseregeln und andere Azure Sentinel-Ressourcen erstellen und bearbeiten[*](#workbooks) | Incidents verwalten (verwerfen, zuweisen usw.) | Anzeigen von Daten, Incidents, Arbeitsmappen und anderen Azure Sentinel-Ressourcen |
|---|---|---|---|---|
| Azure Sentinel-Leser | -- | -- | -- | &#10003; |
| Azure Sentinel-Antwortender | -- | -- | &#10003; | &#10003; |
| Azure Sentinel-Mitwirkender | -- | &#10003; | &#10003; | &#10003; |
| Azure Sentinel-Mitwirkender und Mitwirkender für Logik-Apps | &#10003; | &#10003; | &#10003; | &#10003; |
| | | | | |

<a name=workbooks></a>* Zum Erstellen und Löschen von Arbeitsmappen ist die zusätzliche Rolle [Mitwirkender an der Überwachung](../role-based-access-control/built-in-roles.md#monitoring-contributor) erforderlich. Weitere Informationen finden Sie unter [Weitere Rollen und Berechtigungen](#additional-roles-and-permissions).
## <a name="custom-roles-and-advanced-azure-rbac"></a>Benutzerdefinierte Rollen und erweiterte Azure RBAC-Rollen

- **Benutzerdefinierte Rollen**. Zusätzlich zu integrierten Azure-Rollen oder anstelle von diesen können Sie benutzerdefinierte Azure-Rollen für Azure Sentinel erstellen. Benutzerdefinierte Azure-Rollen für Azure Sentinel werden genauso wie andere [benutzerdefinierte Azure-Rollen](../role-based-access-control/custom-roles-rest.md#create-a-custom-role) basierend auf [bestimmten Berechtigungen für Azure Sentinel](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) und für [Azure Log Analytics-Ressourcen](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights) erstellt.

- **RBAC in Log Analytics**. Sie können die erweiterte rollenbasierte Zugriffssteuerung in Azure von Log Analytics für die Daten in Ihrem Azure Sentinel-Arbeitsbereich verwenden. Dies umfasst sowohl auf dem Datentyp basierendes Azure RBAC als auch Azure RBAC im Ressourcenkontext. Weitere Informationen finden Sie unter:

    - [Verwalten von Protokolldaten und Arbeitsbereichen in Azure Monitor](../azure-monitor/logs/manage-access.md#manage-access-using-workspace-permissions)

    - [Verwalten des Zugriffs auf Azure Sentinel-Daten nach Ressource](resource-context-rbac.md)
    - [Table-level RBAC (RBAC auf Tabellenebene)](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043)

    Die rollenbasierte Zugriffssteuerung (RBAC) im Ressourcenkontext und RBAC auf Tabellenebene sind zwei Methoden der Bereitstellung von Zugriff auf bestimmte Daten im Azure Sentinel-Arbeitsbereich, ohne Zugriff auf die gesamte Azure Sentinel-Umgebung zu gewähren.

## <a name="role-recommendations"></a>Empfehlungen für Rollen

Nachdem Sie verstanden haben, wie Rollen und Berechtigungen in Azure Sentinel funktionieren, empfiehlt sich die folgende Anleitung mit bewährten Methoden zur Zuweisung von Rollen zu Ihren Benutzern:

|Benutzertyp  |Rolle |Resource group  |BESCHREIBUNG  |
|---------|---------|---------|---------|
|**Sicherheitsanalysten**     | [Azure Sentinel-Antwortender](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)        | Ressourcengruppe von Azure Sentinel        | Anzeigen von Daten, Incidents, Arbeitsmappen und anderen Azure Sentinel-Ressourcen <br><br>Verwalten von Incidents, z. B. Zuweisen oder Schließen von Incidents        |
|     | [Logic Apps-Mitwirkender](../role-based-access-control/built-in-roles.md#logic-app-contributor)        | Ressourcengruppe von Azure Sentinel oder die Ressourcengruppe, in der Ihre Playbooks gespeichert sind        | Anfügen von Playbooks an Analyse- und Automatisierungsregeln und Ausführen von Playbooks <br><br>**Hinweis**: Mit dieser Rolle können Benutzer auch Playbooks ändern.         |
|**Sicherheitstechniker**     | [Azure Sentinel-Mitwirkender](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)       |Ressourcengruppe von Azure Sentinel         |   Anzeigen von Daten, Incidents, Arbeitsmappen und anderen Azure Sentinel-Ressourcen <br><br>Verwalten von Incidents, z. B. Zuweisen oder Schließen von Incidents <br><br>Erstellen und Bearbeiten von Arbeitsmappen, Analyseregeln und anderen Azure Sentinel-Ressourcen      |
|     | [Logic Apps-Mitwirkender](../role-based-access-control/built-in-roles.md#logic-app-contributor)        | Ressourcengruppe von Azure Sentinel oder die Ressourcengruppe, in der Ihre Playbooks gespeichert sind        | Anfügen von Playbooks an Analyse- und Automatisierungsregeln und Ausführen von Playbooks <br><br>**Hinweis**: Mit dieser Rolle können Benutzer auch Playbooks ändern.         |
|  **Dienstprinzipal**   | [Azure Sentinel-Mitwirkender](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)      |  Ressourcengruppe von Azure Sentinel       | Automatisierte Konfiguration für Verwaltungsaufgaben |
|     |         |        | |

> [!TIP]
> Abhängig von den erfassten oder überwachten Daten sind möglicherweise zusätzliche Rollen erforderlich. Beispielsweise können Azure AD-Rollen wie z  B. „Globaler Administrator“ oder „Sicherheitsadministrator“ erforderlich sein, um Datenconnectors für Dienste in anderen Microsoft-Portalen einzurichten.
>

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie mit Rollen für Azure Sentinel-Benutzer arbeiten und was die einzelnen Rollen den Benutzern ermöglichen.

Blogbeiträge zur Sicherheit und Compliance von Azure finden Sie im [Azure Sentinel-Blog](https://aka.ms/azuresentinelblog).
