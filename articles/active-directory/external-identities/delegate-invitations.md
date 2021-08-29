---
title: 'Azure AD: Aktivieren der Einstellungen für externe B2B-Zusammenarbeit'
description: Hier erfahren Sie, wie Sie die externe B2B-Zusammenarbeit in Azure Active Directory aktivieren und steuern, wer Gastbenutzer einladen darf. Verwenden Sie die Rolle „Gasteinladender“, um Einladungen zu delegieren.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 08/24/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c6dbac365b10b7abaeea6c4ae600cef24fcd1d2
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122821796"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>Aktivieren der externen B2B-Zusammenarbeit und Steuern, wer Gäste einladen kann

In diesem Artikel wird beschrieben, wie die B2B-Zusammenarbeit in Azure Active Directory (Azure AD) aktiviert wird und wie Sie festlegen, wer Gäste einladen darf und welche Berechtigungen die Gastbenutzer in Azure AD erhalten. 

Per Standardeinstellung können alle Benutzer und Gäste in Ihrem Verzeichnis Gäste einladen, auch denn, wenn ihnen keine Administratorrolle zugewiesen ist. In den Einstellungen für die externe Zusammenarbeit können Sie die Möglichkeit, Gäste einzuladen, für verschiedene Benutzertypen in Ihrer Organisation aktivieren oder deaktivieren. Diese Möglichkeit können Sie auch an einzelne Benutzer delegieren, indem Sie diesen Benutzern Rollen zuweisen, die es ihnen ermöglichen, Gäste einzuladen.

Mit Azure AD können Sie einschränken, was externe Gastbenutzer in Ihrem Azure AD-Verzeichnis sehen können. Standardmäßig ist Gastbenutzern eine eingeschränkte Berechtigungsstufe zugewiesen, die sie daran hindert, Benutzer, Gruppen oder andere Verzeichnisressourcen aufzulisten, ihnen aber ermöglicht, die Mitgliedschaft in allen nicht ausgeblendeten Gruppen anzuzeigen. Mit einer neuen Vorschaueinstellung können Sie den Gastzugriff noch weiter einschränken, sodass Gäste nur ihre eigenen Profilinformationen sehen können. Einzelheiten finden Sie unter [Einschränken von Gastzugriffsberechtigungen (Vorschau)](../enterprise-users/users-restrict-guest-permissions.md).

## <a name="configure-b2b-external-collaboration-settings"></a>Konfigurieren der Einstellungen für externe B2B-Zusammenarbeit

In der Azure AD B2B-Zusammenarbeit kann ein Mandantenadministrator folgende Einladungsrichtlinien festlegen:

- Einladungen deaktivieren.
- Nur Administratoren und Benutzer in der Rolle „Gasteinladender“ dürfen einladen.
- Administratoren, Benutzer in der Rolle „Gasteinladender“ und Mitglieder dürfen einladen.
- Alle Benutzer, einschließlich Gästen, dürfen einladen.

Per Standardeinstellung können alle Benutzer, einschließlich Gäste, Gastbenutzer einladen.

### <a name="to-configure-external-collaboration-settings"></a>So können Sie die Einstellungen für externe Zusammenarbeit konfigurieren:

1. Melden Sie sich als Mandantenadministrator im [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie **Azure Active Directory** aus.
3. Wählen Sie **Externe Identitäten** > **Einstellungen für externe Zusammenarbeit** aus.

4. Wählen Sie unter **Zugriffseinschränkungen für Gastbenutzer (Vorschau)** die Zugriffsebene aus, die Gastbenutzern zugewiesen werden soll:
  
   - **Gastbenutzer haben denselben Zugriff wie Mitglieder (umfassendste Einstellung)** : Diese Option gibt Gästen den gleichen Zugriff auf die Ressourcen und Verzeichnisdaten von Azure AD wie Mitgliedsbenutzern.

   - **Gastbenutzer haben eingeschränkten Zugriff auf Eigenschaften und Mitgliedschaften von Verzeichnisobjekten**: (Standard) Diese Einstellung verhindert, dass Gäste bestimmte Verzeichnisaufgaben ausführen können, z. B. Benutzer, Gruppen oder andere Verzeichnisressourcen aufzulisten. Gäste können die Mitgliedschaft in allen nicht ausgeblendeten Gruppen anzeigen.

   - **Der Gastbenutzerzugriff ist auf Eigenschaften und Mitgliedschaften eigener Verzeichnisobjekte beschränkt (restriktivste Einstellung)** : Mit dieser Einstellung können Gäste nur auf Ihre eigenen Profile zugreifen. Gäste dürfen keine Profile, Gruppen oder Gruppenmitgliedschaften anderer Benutzer anzeigen.

5. Wählen Sie unter **Einstellungen für Gasteinladungen** die entsprechenden Einstellungen aus:

    ![Einstellungen für Gasteinladungen](./media/delegate-invitations/guest-invite-settings.png)

   - **Jeder in der Organisation kann Gastbenutzer einladen, einschließlich Gästen und Nicht-Administratoren (umfassendste Einstellung):** Damit Gäste in der Organisation andere Gäste (einschließlich derjenigen, die keine Mitglieder einer Organisation sind) einladen können, müssen Sie dieses Optionsfeld aktivieren.
   - **Mitgliedsbenutzer und Benutzer, die bestimmten Administratorrollen zugewiesen sind, können Gastbenutzer einladen, einschließlich Gästen mit Mitgliedsberechtigungen**: Damit Mitgliedsbenutzer und Benutzer mit bestimmten Administratorrollen Gäste einladen können, müssen Sie dieses Optionsfeld aktivieren.
   - **Nur Benutzer, die bestimmten Administratorrollen zugewiesen sind, können Gastbenutzer einladen**: Wenn Sie nur Benutzern mit Administratorrollen das Einladen von Gästen erlauben möchten, aktivieren Sie dieses Optionsfeld. Zu den Administratorrollen gehören [globaler Administrator,](../roles/permissions-reference.md#global-administrator) [Benutzeradministrator](../roles/permissions-reference.md#user-administrator) und [Gasteinladender](../roles/permissions-reference.md#guest-inviter).
   - **Niemand in der Organisation kann Gastbenutzer einladen, einschließlich Administratoren (restriktivste Einstellung)** : Wenn Sie allen Benutzern in der Organisation das Einladen von Gästen verweigern möchten, aktivieren Sie dieses Optionsfeld.
     > [!NOTE]
     > Wenn **Mitglieder können einladen** auf **Nein** festgelegt ist und **Administratoren und Benutzer mit der Rolle „Gasteinladender“ können einladen** auf **Ja** festgelegt ist, können Benutzer mit der Rolle **Gasteinladender** weiterhin Gäste einladen.

6. Wählen Sie unter **Self-Service-Registrierung von Gästen über Benutzerflows aktivieren** die Option **Ja** aus, wenn Sie Benutzerflows erstellen möchten, über die sich Benutzer für Apps registrieren können. Weitere Informationen zu dieser Einstellung finden Sie unter [Hinzufügen eines Benutzerflows für die Self-Service-Registrierung zu einer App](self-service-sign-up-user-flow.md).

    ![Einstellung „Self-Service-Registrierung über Benutzerflow“](./media/delegate-invitations/self-service-sign-up-setting.png)

7. Unter **Einschränkungen für die Zusammenarbeit** können Sie auswählen, ob Sie Einladungen für die von Ihnen angegebenen Domänen zulassen oder verweigern möchten. Sie können außerdem in den Textfeldern bestimmte Domänennamen eingeben. Geben Sie für mehrere Domänen jede Domäne in einer neuen Zeile ein. Weitere Informationen finden Sie unter [Zulassen oder Blockieren von Einladungen für B2B-Benutzer von bestimmten Organisationen](allow-deny-list.md).

    ![Einstellungen für die Einschränkung der Zusammenarbeit](./media/delegate-invitations/collaboration-restrictions.png)
## <a name="assign-the-guest-inviter-role-to-a-user"></a>Zuweisen der Rolle „Gasteinladender“ an einen Benutzer

Über die Rolle „Gasteinladender“ können Sie einzelnen Benutzern die Möglichkeit geben, Gäste einzuladen, ohne ihnen dafür eine globale Administratorrolle oder eine andere Administratorrolle zuweisen zu müssen. Weisen Sie die Rolle „Gasteinladender“ einzelnen Benutzern zu. Stellen Sie dann sicher, dass die Richtlinie **Administratoren und Benutzer mit der Rolle „Gasteinladender“ können einladen** auf **Ja** festgelegt ist.

Hier ist ein Beispiel, das zeigt, wie PowerShell verwendet wird, um einen Benutzer der Rolle „Gasteinladender“ hinzuzufügen:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Artikel zur Azure AD B2B-Zusammenarbeit:

- [Was ist die Azure AD B2B-Zusammenarbeit?](what-is-b2b.md)
- [Hinzufügen von Gastbenutzern der B2B-Zusammenarbeit ohne Einladung](add-user-without-invite.md)
- [Hinzufügen eines B2B-Zusammenarbeitsbenutzers zu einer Rolle](./add-users-administrator.md)