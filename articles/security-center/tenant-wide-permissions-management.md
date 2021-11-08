---
title: Gewähren und Anfordern von mandantenweiten Berechtigungen in Microsoft Defender für Cloud
description: Es wird beschrieben, wie Sie mandantenweite Berechtigungen in Microsoft Defender für Cloud verwalten.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/11/2021
ms.openlocfilehash: 23bb5771923b3a15ed58ed3e3189b3edf46b4664
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131424986"
---
# <a name="grant-and-request-tenant-wide-visibility"></a>Gewähren und Anfordern der mandantenweiten Sichtbarkeit

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Es kann sein, dass ein Benutzer mit der Azure Active Directory-Rolle **Globaler Administrator** mandantenweite Aufgaben durchführen muss, aber nicht über die Azure-Berechtigungen zum Anzeigen der organisationsweiten Informationen in Microsoft Defender für Cloud verfügt. Eine Berechtigungserhöhung ist erforderlich, da Azure AD-Rollenzuweisungen keinen Zugriff auf Azure-Ressourcen gewähren. 

## <a name="grant-tenant-wide-permissions-to-yourself"></a>Erteilen von mandantenweiten Berechtigungen an sich selbst

So weisen Sie sich selbst Berechtigungen auf Mandantenebene zu:

1. Wenn Ihre Organisation den Ressourcenzugriff über [Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-configure.md) oder ein anderes PIM-Tool verwaltet, muss die Rolle des globalen Administrators für den Benutzer, der nachfolgende Prozedur ausführt, aktiv sein.

1. Öffnen Sie als globaler Administrator ohne Zuweisung in der Stammverwaltungsgruppe des Mandanten die Seite **Übersicht** von Defender für Cloud, und wählen Sie im Banner den Link **Mandantenweite Sichtbarkeit** aus. 

    :::image type="content" source="media/management-groups-roles/enable-tenant-level-permissions-banner.png" alt-text="Aktivieren von Berechtigungen auf Mandantenebene in Microsoft Defender für Cloud":::

1. Wählen Sie die neue Azure-Rolle aus, die Sie zuweisen möchten. 

    :::image type="content" source="media/management-groups-roles/enable-tenant-level-permissions-form.png" alt-text="Formular zum Definieren der Berechtigungen auf Mandantenebene, die Ihrem Benutzer zugewiesen werden sollen":::

    > [!TIP]
    > Im Allgemeinen ist die Rolle „Sicherheitsadministrator“ erforderlich, um Richtlinien auf der Stammebene anzuwenden, während „Sicherheitsleseberechtigter“ ausreicht, um Sichtbarkeit für die Mandantenebene bereitzustellen. Weitere Informationen zu den Berechtigungen, die durch diese Rollen gewährt werden, finden Sie in der [Beschreibung der integrierten Rolle „Sicherheitsadministrator“](../role-based-access-control/built-in-roles.md#security-admin) bzw. in der [Beschreibung der integrierten Rolle „Sicherheitsleseberechtigter“](../role-based-access-control/built-in-roles.md#security-reader).
    >
    > Informationen zu den Unterschieden zwischen diesen spezifischen Defender für Cloud-Rollen finden Sie in der Tabelle unter [Rollen und zulässige Aktionen](permissions.md#roles-and-allowed-actions).

    Eine organisationsweite Ansicht lässt sich erzielen, indem Sie Rollen auf Ebene der Stammverwaltungsgruppe des Mandanten zuweisen.  

1. Melden Sie sich vom Azure-Portal ab und dann wieder an.

1. Wenn Sie erhöhte Zugriffsrechte besitzen, können Sie Microsoft Defender für Cloud öffnen bzw. aktualisieren, um sicherzustellen, dass die Sichtbarkeit für alle Abonnements Ihres Azure AD-Mandanten vorhanden ist. 

Mit dem oben beschriebenen einfachen Prozess wird automatisch eine Reihe von Vorgängen für Sie durchführt:

1. Die Benutzerberechtigungen werden vorübergehend erhöht.
1. Mithilfe der neuen Berechtigungen wird der Benutzer der gewünschten Azure RBAC-Rolle in der Stammverwaltungsgruppe zugewiesen.
1. Die erhöhten Berechtigungen werden entfernt.

Weitere Informationen zur Erhöhung der Azure AD-Rechte finden Sie unter [Erhöhen der Zugriffsrechte zum Verwalten aller Azure-Abonnements und Verwaltungsgruppen](../role-based-access-control/elevate-access-global-admin.md).


## <a name="request-tenant-wide-permissions-when-yours-are-insufficient"></a>Anfordern mandantenweiter Berechtigungen, wenn die eigenen Berechtigungen nicht ausreichen

Wenn Sie sich bei Defender für Cloud anmelden und ein Banner mit dem Hinweis angezeigt wird, dass Ihre Ansicht eingeschränkt ist, können Sie per Klick fortfahren, um eine Anforderung an den globalen Administrator Ihrer Organisation zu senden. In die Anforderung können Sie die Rolle einschließen, deren Zuweisung Sie wünschen, und der globale Administrator entscheidet, welche Rolle gewährt wird. 

Der globale Administrator entscheidet, ob er diese Anforderungen annimmt oder ablehnt. 

> [!IMPORTANT]
> Sie können nur alle sieben Tage eine Anforderung übermitteln.

So fordern Sie von ihrem globalen Administrator erhöhte Berechtigungen an:

1. Öffnen Sie im Azure-Portal den Dienst „Microsoft Defender für Cloud“.

1. Wenn das Banner „Sie sehen eingeschränkte Informationen.“ angezeigt wird, wählen Sie es aus.

    :::image type="content" source="media/management-groups-roles/request-tenant-permissions.png" alt-text="Banner mit der Information, dass der Benutzer mandantenweite Berechtigungen anfordern kann":::

1. Wählen Sie im ausführlichen Anforderungsformular die gewünschte Rolle und die Begründung aus, warum Sie diese Berechtigungen benötigen.

    :::image type="content" source="media/management-groups-roles/request-tenant-permissions-details.png" alt-text="Detailseite zum Anfordern mandantenweiter Berechtigungen von Ihrem globalen Azure-Administrator":::

1. Wählen Sie **Zugriff anfordern** aus.

    Eine E-Mail wird an den globalen Administrator gesendet. Die E-Mail enthält einen Link zu Defender für Cloud, über den die Anforderung genehmigt oder abgelehnt werden kann.

    :::image type="content" source="media/management-groups-roles/request-tenant-permissions-email.png" alt-text="An den globalen Administrator gesendete E-Mail zum Anfordern neuer Berechtigungen":::

    Sobald der globale Administrator **Anforderung überprüfen** auswählt und den Prozess abschließt, wird die Entscheidung per E-Mail an den anfordernden Benutzer gesendet. 

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Defender für Cloud-Berechtigungen finden Sie auf der folgenden Seite:

- [Berechtigungen in Microsoft Defender für Cloud](permissions.md)