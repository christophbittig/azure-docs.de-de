---
title: 'Genehmigen von Aktivierungsanforderungen für Gruppenmitglieder und -besitzer in Privileged Identity Management: Azure AD'
description: Hier erfahren Sie, wie Sie Anforderungen für Gruppen mit Rollenzuweisung in Azure AD Privileged Identity Management (PIM) genehmigen oder ablehnen.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c07c654e5a4baf0f817cefa8c48be9c49cebae4
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129668217"
---
# <a name="approve-activation-requests-for-privileged-access-group-members-and-owners-preview"></a>Genehmigen von Aktivierungsanforderungen für Mitglieder und Besitzer privilegierter Zugriffsgruppen (Vorschau)

In Azure Active Directory (Azure AD) können Sie mit Privileged Identity Management (PIM) Mitglieder und Besitzer privilegierter Zugriffsgruppen konfigurieren, um die Genehmigung für die Aktivierung anzufordern, und Benutzer oder Gruppen aus Ihrer Azure AD-Organisation als delegierte genehmigende Personen auswählen. Es wird empfohlen, mindestens zwei genehmigende Personen für jede Gruppe auszuwählen, um die Arbeitsauslastung des Administrators für privilegierte Rollen zu verringern. Delegierte genehmigende Personen haben zum Genehmigen von Anforderungen 24 Stunden Zeit. Wenn eine Anforderung nicht innerhalb von 24 Stunden genehmigt wird, muss der berechtigte Benutzer eine neue Anforderung senden. Das 24-Stunden-Zeitfenster für die Genehmigung ist nicht konfigurierbar.

Führen Sie die in diesem Artikel beschriebenen Schritte aus, um Anforderungen für Azure-Ressourcenrollen zu genehmigen oder abzulehnen.

## <a name="view-pending-requests"></a>Anzeigen ausstehender Anforderungen

Als delegierte genehmigende Person erhalten Sie eine E-Mail-Benachrichtigung, wenn Ihre Genehmigung einer Anforderung für eine Azure-Ressourcenrolle aussteht. Sie können ausstehende Anforderungen in Privileged Identity Management anzeigen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Wählen Sie **Anforderungen genehmigen** aus.

    ![Anforderungen genehmigen: Seite „Azure-Ressourcen“ mit einer Überprüfungsanforderung](./media/groups-approval-workflow/groups-select-request.png)

    Im Abschnitt **Anforderungen für Rollenaktivierungen** wird eine Liste der Anforderungen angezeigt, die Sie noch genehmigen müssen.

## <a name="approve-requests"></a>Genehmigen von Anforderungen

1. Suchen Sie nach der zu genehmigenden Anforderung, und wählen Sie diese aus. Wählen Sie anschließend **Genehmigen** aus.

    ![Screenshot: Seite „Anforderungen genehmigen“ mit den hervorgehobenen Schaltflächen „Genehmigen“ und „Bestätigen“](./media/groups-approval-workflow/groups-confirm-approval.png)

1. Geben Sie im Feld **Begründung** die geschäftliche Begründung ein.

1. Klicken Sie auf **Bestätigen**. Durch Ihre Genehmigung wird eine Azure-Benachrichtigung generiert.

## <a name="deny-requests"></a>Ablehnen von Anforderungen

1. Suchen Sie nach der Anforderung, die Sie ablehnen möchten, und wählen Sie sie aus. Wählen Sie anschließend **Ablehnen** aus.

    ![Anforderungen genehmigen: Bereich zum Genehmigen oder Ablehnen von Anforderungen mit Details und dem Feld „Begründung“](./media/groups-approval-workflow/groups-confirm-denial.png)

1. Geben Sie im Feld **Begründung** die geschäftliche Begründung ein.

1. Klicken Sie auf **Bestätigen**. Durch Ihre Ablehnung wird eine Azure-Benachrichtigung generiert.

## <a name="workflow-notifications"></a>Workflowbenachrichtigungen

Nachfolgend finden Sie einige Informationen zu Workflowbenachrichtigungen:

- Die genehmigenden Personen werden per E-Mail benachrichtigt, wenn für die Anforderung einer Gruppenzuweisung die Überprüfung aussteht. E-Mail-Benachrichtigungen enthalten einen direkten Link zur Anforderung, damit diese von der genehmigenden Person genehmigt oder abgelehnt werden kann.
- Anforderungen werden von der ersten genehmigenden Person bearbeitet, die sich mit der Genehmigung oder Ablehnung befasst.
- Wenn eine genehmigende Person auf die Anforderung reagiert, werden alle genehmigenden Personen über die Aktion informiert.

>[!Note]
>Wenn ein Administrator der Meinung ist, dass ein genehmigter Benutzer nicht aktiv sein sollte, kann er die aktive Gruppenzuweisung in Privileged Identity Management entfernen. Ressourcenadministratoren werden nur über ausstehende Anforderungen benachrichtigt, wenn sie genehmigende Personen sind. Sie können aber ausstehende Anforderungen aller Benutzer anzeigen und abbrechen, indem sie die ausstehenden Anforderungen in Privileged Identity Management anzeigen.

## <a name="next-steps"></a>Nächste Schritte

- [Verlängern oder Erneuern von Zuweisungen von Azure-Ressourcenrollen in Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [E-Mail-Benachrichtigungen in Privileged Identity Management](pim-email-notifications.md)
- [Genehmigen oder Ablehnen von Anforderungen für Azure AD-Rollen in PIM](azure-ad-pim-approval-workflow.md)
