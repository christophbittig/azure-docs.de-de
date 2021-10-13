---
title: Einstieg in die Verwendung von PIM – Azure Active Directory | Microsoft-Dokumentation
description: Hier erhalten Sie Informationen zum Aktivieren von und zu den ersten Schritten mit Azure AD Privileged Identity Management (PIM) im Azure-Portal.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: how-to
ms.workload: identity
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 890f794846427b66081d97018e5590225142c45f
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667286"
---
# <a name="start-using-privileged-identity-management"></a>Einstieg in Privileged Identity Management

In diesem Artikel werden das Aktivieren von und die ersten Schritte mit Privileged Identity Management (PIM) beschrieben.

Mit Privileged Identity Management (PIM) können Sie den Zugriff innerhalb Ihrer Azure Active Directory-Organisation (Azure AD) verwalten, steuern und überwachen. Mit PIM können Sie bedarfsgesteuerten Zugriff und Just-In-Time-Zugriff auf Azure-Ressourcen, Azure AD-Ressourcen und andere Microsoft-Onlinedienste wie Microsoft 365 oder Microsoft Intune bereitstellen.

## <a name="prerequisites"></a>Voraussetzungen

Um Privileged Identity Management verwenden zu können, müssen Sie über eine der folgenden Lizenzen verfügen:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Weitere Informationen finden Sie unter [Lizenzanforderungen für die Verwendung von PIM](subscription-requirements.md).

> [!Note]
> Wenn ein aktiver Benutzer mit einer privilegierten Rolle in einer Azure AD-Organisation mit einer Premium P2-Lizenz in Azure AD zu **Rollen und Administratoren** navigiert und eine Rolle auswählt (oder sogar lediglich auf Privileged Identity Management zugreift):
>
> - PIM wird automatisch für die Organisation aktiviert.
> - Der Benutzer kann nun entweder eine „herkömmliche“ Rollenzuweisung oder eine zulässige Rollenzuweisung zuweisen.
>
> Die Aktivierung von PIM hat keine anderen Auswirkungen auf Ihre Organisation, um die Sie sich sorgen müssen. Dies bietet Ihnen zusätzliche Zuweisungsoptionen wie aktive oder zulässige Zuweisungen mit Anfangs- und Endzeit. PIM ermöglicht Ihnen außerdem das Definieren des Gültigkeitsbereichs für Rollenzuweisungen mithilfe von Verwaltungseinheiten und benutzerdefinierten Rollen. Wenn Sie ein globaler Administrator oder Administrator mit privilegierter Rolle sind, erhalten Sie möglicherweise einige weitere E-Mails wie die wöchentliche PIM-Zusammenfassung. Darüber hinaus wird Ihnen möglicherweise auch der MS-PIM-Dienstprinzipal im Überwachungsprotokoll für die Rollenzuweisung angezeigt. Dabei handelt es sich um eine erwartete Änderung, die sich nicht auf Ihren Workflow auswirken sollte.

## <a name="prepare-pim-for-azure-ad-roles"></a>Vorbereiten von PIM für Azure AD-Rollen

Folgende Aufgaben werden zur Vorbereitung von Privileged Identity Management für die Verwaltung von Azure AD-Rollen empfohlen:

1. [Konfigurieren von Einstellungen für Azure AD-Rollen](pim-how-to-change-default-settings.md)
1. [Gewähren der berechtigten Zuweisungen](pim-how-to-add-role-to-user.md)
1. [Erlauben, die Azure AD-Rolle berechtigter Benutzer Just-In-Time zu aktivieren](pim-how-to-activate-role.md)

## <a name="prepare-pim-for-azure-roles"></a>Vorbereiten von PIM für Azure-Rollen

Folgende Aufgaben werden zur Vorbereitung von Privileged Identity Management für die Verwaltung von Azure-Rollen für ein Abonnement empfohlen:

1. [Ermitteln von Azure-Ressourcen](pim-resource-roles-discover-resources.md)
1. [Konfigurieren von Einstellungen für Azure-Rollen](pim-resource-roles-configure-role-settings.md)
1. [Gewähren der berechtigten Zuweisungen](pim-resource-roles-assign-roles.md)
1. [Erlauben der berechtigten Benutzer, ihre Azure-Rollen Just-In-Time zu aktivieren](pim-resource-roles-activate-your-roles.md)

## <a name="navigate-to-your-tasks"></a>Navigieren zu Ihren Aufgaben

Nach der Einrichtung von Privileged Identity Management können Sie sich mit der Verwendung vertraut machen.

![Navigationsfenster in Privileged Identity Management mit den Optionen „Aufgaben“ und „Verwalten“](./media/pim-getting-started/pim-quickstart-tasks.png)

| „Aufgaben“ und „Verwalten“ | BESCHREIBUNG |
| --- | --- |
| **Meine Rollen**  | Zeigt eine Liste mit berechtigten und aktiven Rollen an, die Ihnen zugewiesen sind. Hier können Sie zugewiesene berechtigte Rollen aktivieren. |
| **Ausstehende Anforderungen** | Zeigt Ihre ausstehenden Anforderungen zum Aktivieren berechtigter Rollenzuweisungen an. |
| **Genehmigen von Anforderungen** | Zeigt eine Liste mit Anforderungen zum Aktivieren berechtigter Rollen nach Benutzern in Ihrem Verzeichnis an, für deren Genehmigung Sie zuständig sind. |
| **Überprüfen des Zugriffs** | Zeigt eine Liste mit aktiven Zugriffsüberprüfungen an, die Ihnen zugewiesen sind. Dabei spielt es keine Rolle, ob Sie den Zugriff für sich selbst oder für eine andere Person überprüfen. |
| **Azure AD-Rollen** | Zeigt für Administratoren privilegierter Rollen ein Dashboard und Einstellungen zum Verwalten von Azure AD-Rollenzuweisungen an. Für Benutzer, die kein Administrator für privilegierte Rollen sind, ist dieses Dashboard deaktiviert. Diese Benutzer haben Zugriff auf ein spezielles Dashboard namens „Meine Ansicht“. Auf dem Dashboard „Meine Ansicht“ werden nur Informationen zu dem Benutzer angezeigt, der auf das Dashboard zugreift (also keine Informationen zur gesamten Organisation). |
| **Azure-Ressourcen** | Zeigt für Administratoren privilegierter Rollen ein Dashboard und Einstellungen zum Verwalten von Azure-Ressourcenrollenzuweisungen an. Für Benutzer, die kein Administrator für privilegierte Rollen sind, ist dieses Dashboard deaktiviert. Diese Benutzer haben Zugriff auf ein spezielles Dashboard namens „Meine Ansicht“. Auf dem Dashboard „Meine Ansicht“ werden nur Informationen zu dem Benutzer angezeigt, der auf das Dashboard zugreift (also keine Informationen zur gesamten Organisation). |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Hinzufügen einer PIM-Kachel zum Dashboard

Fügen Sie Ihrem Dashboard im Azure-Portal eine PIM-Kachel hinzu, um Privileged Identity Management einfacher öffnen zu können.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Wählen Sie **Alle Dienste** aus, und suchen Sie nach dem Dienst **Azure AD Privileged Identity Management**.

    ![Azure AD Privileged Identity Management in „Alle Dienste“](./media/pim-getting-started/pim-all-services-find.png)

1. Wählen Sie den Privileged Identity Management-**Schnellstart** aus.

1. Wählen Sie **Blatt an Dashboard anheften** aus, um die Seite für den **Schnellstart** von Privileged Identity Management an das Dashboard anzuheften.

    ![Reißzweckensymbol für das Anheften der Privileged Identity Management-Seite an das Dashboard](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Im Azure-Dashboard wird eine Kachel wie diese angezeigt:

    ![Kachel für den Privileged Identity Management-Schnellstart auf dem Dashboard](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Nächste Schritte

- [Zuweisen von Azure AD-Rollen in PIM](pim-how-to-add-role-to-user.md)
- [Verwalten des Azure-Ressourcenzugriffs in Privileged Identity Management](pim-resource-roles-discover-resources.md)
