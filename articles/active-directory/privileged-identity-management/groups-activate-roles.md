---
title: Aktivieren von privilegierten Zugriffsgruppenrollen in Azure AD Privileged Identity Management (PIM) | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihre privilegierte Zugriffsgruppenrollen in Azure AD Privileged Identity Management (PIM) aktivieren.
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
ms.openlocfilehash: 365f480af81289a9f48dd6ce5a1102be7a7ed6a1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131444153"
---
# <a name="activate-my-privileged-access-group-roles-in-privileged-identity-management"></a>Aktivieren von „Meine privilegierten Zugriffsgruppenrollen“ in Privileged Identity Management

Verwenden von Privileged Identity Management (PIM), um zuzulassen, dass berechtigte Rollenmitglieder für privilegierte Zugriffsgruppen die Rollenaktivierung für ein bestimmtes Datum und eine bestimmte Uhrzeit planen können. Sie können auch eine Aktivierungsdauer bis zu der von den Administratoren festgelegten Höchstdauer wählen.

Dieser Artikel gilt für berechtigte Mitglieder, die ihre privilegierte Zugriffsgruppenrolle in Privileged Identity Management aktivieren möchten.

## <a name="activate-a-role"></a>Aktivieren einer Rolle

Wenn Sie eine privilegierte Ressourcengruppenrolle übernehmen müssen, können Sie in Privileged Identity Management über die Navigationsoption **Meine Rollen** die Aktivierung anfordern.

1. [Melden Sie sich beim Azure AD-Portal](https://aad.portal.azure.com) mit den Berechtigungen eines globalen Administrators oder Gruppenbesitzers an.

1. Öffnen Sie [Privileged Identity Management](https://portal.azure.com/#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart).

1. Wählen Sie **Privilegierte Zugriffsgruppen (Vorschau)** und dann **Rolle aktivieren**, um die Seite **Meine Rollen** für privilegierte Zugriffsgruppen zu öffnen.

    ![Die Seite „Rollen für den privilegierten Zugriff“ in PIM](./media/groups-activate-roles/groups-select-group.png)

1. Auf der Seite **Meine Rollen**, wählen Sie **Aktivieren** auf die Reihe der berechtigten Zuweisung, die Sie aktivieren möchten.

    ![Aktivieren Sie den Link in der Zeile für die berechtigte Rollenzuweisung](./media/groups-activate-roles/groups-activate-link.png)

1. Wenn Ihre Rolle eine mehrstufige Authentifizierung erfordert, klicken Sie auf **Überprüfen Sie Ihre Identität, bevor Sie den Vorgang fortsetzen**. Sie müssen sich nur einmal pro Sitzung authentifizieren.

    ![Überprüfung meiner Identität mit MFA vor der Rollenaktivierung](./media/groups-activate-roles/groups-my-roles-mfa.png)

1. Wählen Sie **Meine Identität überprüfen** aus, und folgen Sie den Anweisungen zur Bereitstellung einer zusätzlichen Sicherheitsüberprüfung.

    ![Anzeige, die eine Sicherheitsüberprüfung wie z. B. einen PIN-Code verlangt](./media/groups-activate-roles/groups-mfa-enter-code.png)

1. Falls erforderlich, geben Sie einen Startzeitpunkt für die Aktivierung an. Das Mitglied oder der Besitzer soll erst nach dem ausgewählten Zeitpunkt aktiviert werden.

1. Geben Sie im Feld **Grund** den Grund für die Aktivierungsanforderung ein.

    ![Die Seite „Aktivieren“ für die Einstellung der Dauer und die Angabe der Begründung](./media/groups-activate-roles/groups-activate-page.png)

1. Wählen Sie **Aktivieren** aus.

Wenn für die [Aktivierung der Rolle eine Genehmigung erforderlich](pim-resource-roles-approval-workflow.md) ist, wird in der oberen rechten Ecke Ihres Browsers eine Azure-Benachrichtigung angezeigt, in der Sie darüber informiert werden, dass die Genehmigung der Anforderung aussteht.

## <a name="view-the-status-of-your-requests"></a>Anzeigen des Status Ihrer Anforderungen

Sie können den Status Ihrer ausstehenden Aktivierungsanforderungen anzeigen.

1. Öffnen Sie Azure AD Privileged Identity Management.

1. Wählen Sie **Meine Anforderungen** aus, um eine Liste mit Ihren Anforderungen von Azure AD-Rollen und privilegierten Zugriffsgruppenrollen anzuzeigen.

1. Scrollen Sie nach rechts, fall das erforderlich ist, um die Spalte **Anforderungsstatus** anzuzeigen.

## <a name="cancel-a-pending-request"></a>Abbrechen einer ausstehenden Anforderung

Sollten Sie die Aktivierung einer Rolle, für die eine Genehmigung erforderlich ist, nicht mehr benötigen, können Sie eine ausstehende Anforderung jederzeit abbrechen.

1. Öffnen Sie Azure AD Privileged Identity Management.

1. Wählen Sie **Meine Anforderungen** aus.

1. Wählen Sie für die Rolle, für die Sie eine ausstehende Anforderung abbrechen möchten, den Link **Abbrechen** aus.

    Durch Auswählen von **Abbrechen** wird die Anforderung abgebrochen. Um die Rolle erneut zu aktivieren, müssen Sie eine neue Anforderung zur Aktivierung übermitteln.

## <a name="troubleshoot"></a>Problembehandlung

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Keine Gewährung von Berechtigungen nach der Aktivierung einer Rolle

Wenn Sie in Privileged Identity Management eine Rolle aktivieren, wird die Aktivierung möglicherweise nicht sofort an alle Portale weitergegeben, für die diese privilegierte Rolle benötigt wird. In einigen Fällen kann die Web-Zwischenspeicherung im Portal auch dann dazu führen, dass die Änderung nicht sofort wirksam ist, wenn diese weitergegeben wurde. Falls die Aktivierung verzögert ist, sollten Sie Folgendes tun.

1. Melden Sie sich vom Azure-Portal ab und dann wieder an.
1. Überprüfen Sie in Privileged Identity Management, ob Sie als Mitglied der Rolle aufgeführt sind.

## <a name="next-steps"></a>Nächste Schritte

- [Erweitern oder Verlängern von „Privilegierten Zugriffsgruppenrollen“ in Privileged Identity Management](groups-renew-extend.md)
- [Zuweisen von „Meine privilegierten Zugriffsgruppenrollen“ in Privileged Identity Management](groups-assign-member-owner.md)
