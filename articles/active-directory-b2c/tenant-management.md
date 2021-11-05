---
title: Verwalten von Azure Active Directory B2C
titleSuffix: Azure Active Directory B2C
description: Erfahren Sie, wie Sie Ihren Azure Active Directory B2C-Mandanten verwalten. Erfahren Sie, welche Azure AD-Features in Azure AD B2C unterstützt werden, wie Sie Administratorrollen zum Verwalten von Ressourcen verwenden und Ihrem Azure AD B2C-Mandanten Arbeitskonten und Gastbenutzer hinzufügen.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 10/25/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: c0713bd45f344ce6dc994cde61cb275f9fdef3a4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131036331"
---
# <a name="manage-your-azure-active-directory-b2c-tenant"></a>Verwalten Ihres Azure Active Directory B2C-Mandanten

Ein Mandant in Azure Active Directory B2C (Azure AD B2C) ist ein Verzeichnis von Consumer-Benutzern. Jeder Azure AD B2C-Mandant unterscheidet sich von anderen Azure AD B2C-Mandanten und ist von diesen getrennt. Der Azure AD B2C-Mandant unterscheidet sich von einem Azure Active Directory-Mandanten, wie Sie ihn möglicherweise bereits verwenden. In diesem Artikel erfahren Sie, wie Sie Ihren Azure AD B2C-Mandanten verwalten.

## <a name="prerequisites"></a>Voraussetzungen
- Wenn Sie Ihren eigenen [Azure AD B2C-Mandanten](tutorial-create-tenant.md) noch nicht erstellt haben, erstellen Sie jetzt einen. Sie können einen vorhandenen Azure AD B2C-Mandanten verwenden.
- Grundlegendes zu [Benutzerkonten in Azure AD B2C](user-overview.md)
- Grundlegendes zum [Steuern des Ressourcenzugriffs mithilfe von Benutzerrollen](roles-resource-access-control.md)


## <a name="add-an-administrator-work-account"></a>Hinzufügen eines Administrators (Arbeitskonto)

Führen Sie diese Schritte aus, um ein neues Administratorenkonto zu erstellen:

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com/) als Benutzer mit Berechtigungen der Rollen „Globaler Administrator“ oder „Administrator für privilegierte Rollen“ an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie unter **Azure-Dienste** die Option **Azure AD B2C** aus. Oder verwenden Sie das Suchfeld, um nach **Azure AD B2C** zu suchen und diese Option auszuwählen.
1. Wählen Sie unter **Verwalten** die Option **Benutzer** aus.
1. Wählen Sie **Neuer Benutzer** aus.
1. Wählen Sie **Benutzer erstellen** aus (Sie können viele Benutzer gleichzeitig erstellen, indem Sie **Ich möchte Benutzer per Massenvorgang erstellen** auswählen).
1. Geben Sie auf der Seite **Benutzer** Informationen für diesen Benutzer ein:

  
   - **Benutzername**. *Erforderlich*. Der Benutzername des neuen Benutzers. Beispiel: `mary@contoso.com`.
     Der Domänenteil des Benutzernamens muss entweder der anfängliche Standarddomänenname , *\<tenant name>.onmicrosoft.com*, oder Ihr [benutzerdefinierter Domänennamen](custom-domain.md), z. B. `contoso.com` sein.
   - **Name**: *Erforderlich*. Der Vor- und Nachname des neuen Benutzers. Beispielsweise *Mary Parker*.
   - **Gruppen**. *Optional:* Sie können den Benutzer einer oder mehreren vorhandenen Gruppen hinzufügen. Sie können den Benutzer auch später zu Gruppen hinzufügen. 
   - **Verzeichnisrolle**: Wenn Sie Azure AD-Administratorberechtigungen für den Benutzer benötigen, können Sie diese einer Azure AD-Rolle hinzufügen. Sie können dem Benutzer die Rolle „Globaler Administrator“ oder mindestens eine der eingeschränkten Administratorrollen in Azure AD zuweisen. Weitere Informationen zum Zuweisen von Rollen finden Sie unter [Verwenden von Rollen zum Steuern des Ressourcenzugriffs](roles-resource-access-control.md).
   - **Angaben zum Beruf**: Hier können Sie weitere Informationen zum Benutzer hinzufügen oder dies später erledigen. 

1. Kopieren Sie das automatisch generierte Kennwort aus dem Feld **Kennwort**. Sie müssen dieses Kennwort dem Benutzer für die erste Anmeldung bereitstellen.
1. Klicken Sie auf **Erstellen**.

Der Benutzer wird erstellt und Ihrem Azure AD B2C-Mandanten hinzugefügt. Es ist besser, mindestens ein natives Arbeitskonto für Ihren Azure AD B2C-Mandanten zu verwenden, dem die Rolle „Globaler Administrator“ zugewiesen ist. Dieses Konto kann als *Break-Glass-Konto* betrachtet werden.

## <a name="invite-an-administrator-guest-account"></a>Einladen eines Administrators (Gastkonto)

Sie können auch einen neuen Gastbenutzer einladen, um Ihren Mandanten zu verwalten. Das Gastkonto ist die bevorzugte Option, wenn Ihre Organisation auch über Azure AD verfügt, da der Lebenszyklus dieser Identität extern verwaltet werden kann. 

Um einen Benutzer einzuladen, befolgen Sie diese Schritte:

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com/) als Benutzer mit Berechtigungen der Rollen „Globaler Administrator“ oder „Administrator für privilegierte Rollen“ an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie unter **Azure-Dienste** die Option **Azure AD B2C** aus. Oder verwenden Sie das Suchfeld, um nach **Azure AD B2C** zu suchen und diese Option auszuwählen.
1. Wählen Sie unter **Verwalten** die Option **Benutzer** aus.
1. Wählen Sie **Neues Gastkonto** aus.
1. Geben Sie auf der Seite **Benutzer** Informationen für diesen Benutzer ein:

   - **Name**: *Erforderlich*. Der Vor- und Nachname des neuen Benutzers. Beispielsweise *Mary Parker*.
   - **E-Mail-Adresse**. *Erforderlich*. Die E-Mail-Adresse des Benutzers, den Sie einladen möchten, bei der es sich um ein Microsoft-Konto handeln muss. Beispiel: `mary@contoso.com`.   
   - **Persönliche Nachricht**: Sie fügen eine persönliche Nachricht hinzu, die in der Einladungs-E-Mail enthalten ist.
   - **Gruppen**. *Optional:* Sie können den Benutzer einer oder mehreren vorhandenen Gruppen hinzufügen. Sie können den Benutzer auch später zu Gruppen hinzufügen.
   - **Verzeichnisrolle**: Wenn Sie Azure AD-Administratorberechtigungen für den Benutzer benötigen, können Sie diese einer Azure AD-Rolle hinzufügen. Sie können dem Benutzer die Rolle „Globaler Administrator“ oder mindestens eine der eingeschränkten Administratorrollen in Azure AD zuweisen. Weitere Informationen zum Zuweisen von Rollen finden Sie unter [Verwenden von Rollen zum Steuern des Ressourcenzugriffs](roles-resource-access-control.md).
   - **Angaben zum Beruf**: Hier können Sie weitere Informationen zum Benutzer hinzufügen oder dies später erledigen.

1. Klicken Sie auf **Erstellen**.

Eine Einladungs-E-Mail wird an den Benutzer gesendet. Der Benutzer muss die Einladung annehmen, um sich anmelden zu können.

### <a name="resend-the-invitation-email"></a>Erneutes Senden der Einladungs-E-Mail

Wenn der Gast die Einladungs-E-Mail nicht erhalten hat oder die Einladung abgelaufen ist, können Sie die Einladung erneut senden. Alternativ zur Einladungs-E-Mail können Sie einem Gast einen direkten Link zu Ihrer App zur Verfügung stellen, um die Einladung anzunehmen. So senden Sie die Einladung erneut und erhalten den direkten Link:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie unter **Azure-Dienste** die Option **Azure AD B2C** aus. Oder verwenden Sie das Suchfeld, um nach **Azure AD B2C** zu suchen und diese Option auszuwählen.
1. Wählen Sie unter **Verwalten** die Option **Benutzer** aus.
1. Suchen Sie nach dem Benutzer, an den Sie die Einladung erneut senden möchten, und wählen Sie diesen aus.
1. Wählen Sie auf der **Benutzer | Profil**-Seite unter **Identität** die Option **(Verwalten)** aus.
        ![Screenshot: Erneutes Senden der Einladungs-E-Mail an das Gastkonto.](./media/tenant-management/guest-account-resend-invite.png)

1. Wählen Sie für **Einladung erneut senden?** die Option **Ja** aus. Wenn **Sind Sie sicher, dass Sie die Einladung erneut senden möchten?** angezeigt wird, wählen Sie **Ja** aus.
1. Azure AD B2C sendet die Einladung. Sie können die Einladungs-URL auch kopieren und direkt für den Gast bereitstellen.
    
    ![Screenshot: Erhalten der Einladungs-URL.](./media/tenant-management/guest-account-invitation-url.png)  
 
## <a name="add-a-role-assignment"></a>Hinzufügen einer Rollenzuweisung

Sie können eine Rolle zuweisen, wenn Sie [einen Benutzer erstellen](#add-an-administrator-work-account) oder [einen Gastbenutzer einladen](#invite-an-administrator-guest-account). Sie können eine Rolle hinzufügen, die Rolle ändern oder eine Rolle für einen Benutzer entfernen:

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com/) als Benutzer mit Berechtigungen der Rollen „Globaler Administrator“ oder „Administrator für privilegierte Rollen“ an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie unter **Azure-Dienste** die Option **Azure AD B2C** aus. Oder verwenden Sie das Suchfeld, um nach **Azure AD B2C** zu suchen und diese Option auszuwählen.
1. Wählen Sie unter **Verwalten** die Option **Benutzer** aus.
1. Wählen Sie den Benutzer, dessen Rolle Sie ändern möchten. Wählen Sie **Zugewiesene Rollen** aus.
1. Wählen Sie **Zuweisung hinzufügen** und die Rolle aus, die zugewiesen werden soll (z. B. *Anwendungsadministrator*), und wählen Sie dann **Hinzufügen** aus.

## <a name="remove-a-role-assignment"></a>Entfernen einer Rollenzuweisung

Wenn Sie eine Rollenzuweisung von einem Benutzer entfernen müssen, führen Sie die folgenden Schritte aus:

1. Wählen **Azure AD B2C** aus, wählen Sie **Benutzer** aus, suchen Sie dann nach dem Benutzer und wählen Sie diesen aus.
1. Wählen Sie **Zugewiesene Rollen** aus. Wählen Sie die Rolle aus, die Sie entfernen möchten, z. B. *Anwendungsadministrator* und wählen Sie dann **Zuweisung entfernen** aus.

## <a name="review-administrator-account-role-assignments"></a>Überprüfen von Rollenzuweisungen für Administratorkonten

Im Rahmen eines Überwachungsprozesses überprüfen Sie in der Regel, welche Benutzer bestimmten Rollen im Azure AD B2C zugewiesen sind. Verwenden Sie die folgenden Schritte, um zu überwachen, welchen Benutzern derzeit privilegierte Rollen zugewiesen sind.

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com/) als Benutzer mit Berechtigungen der Rollen „Globaler Administrator“ oder „Administrator für privilegierte Rollen“ an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie unter **Azure-Dienste** die Option **Azure AD B2C** aus. Oder verwenden Sie das Suchfeld, um nach **Azure AD B2C** zu suchen und diese Option auszuwählen.
1. Wählen Sie unter **Verwalten** den Eintrag **Rollen und Administratoren** aus.
1. Wählen Sie eine Rolle aus, z. B. **Globaler Administrator**. Die auf der Seite **Rolle | Zuweisungen**  werden die Benutzer mit dieser Rolle aufgeführt.

## <a name="delete-an-administrator-account"></a>Löschen eines Administratorkontos

Um einen vorhandenen Benutzer zu löschen, müssen Sie über die zugewiesene Rolle *Globaler Administrator* verfügen. Globale Administratoren können alle Benutzer löschen, auch andere Administratoren. *Benutzeradministratoren* können alle Benutzer löschen, die keine Administratoren sind.

1. Wählen Sie im Azure AD B2C Verzeichnis **Benutzer**, und wählen Sie dann den Benutzer aus, den Sie löschen möchten.
1. Wählen Sie **Löschen** und dann **Ja**, um den Löschvorgang zu bestätigen.

Der Benutzer wird gelöscht und nicht mehr auf der Seite **Benutzer – Alle Benutzer** angezeigt. Sie können den Benutzer für die nächsten 30 Tage auf der Seite **Gelöschte Benutzer** anzeigen und während dieses Zeitraums wiederherstellen. Weitere Informationen zum Wiederherstellen eines Benutzers finden Sie unter [Wiederherstellen oder Entfernen eines kürzlich gelöschten Benutzers mithilfe von Azure Active Directory](../active-directory/fundamentals/active-directory-users-restore.md).

## <a name="protect-administrative-accounts"></a>Verwaltungskonten schützen

Es wird empfohlen, alle Administratorkonten mit mehrstufiger Authentifizierung (Multi-Factor Authentication, MFA) zu schützen, um mehr Sicherheit zu gewährleisten. MFA ist ein Identitätsüberprüfungsprozess während der Anmeldung, bei dem der Benutzer zur Eingabe einer besseren Form der Identifizierung aufgefordert wird, z. B. über einen Überprüfungscode auf dem mobilen Gerät oder eine Anforderung in der Microsoft Authenticator App.

![Screeshot: Verwendete Authentifizierungsmethoden auf dem Anmeldebildschirm](./media/tenant-management/sing-in-with-multi-factor-authentication.png)

Sie können die [Azure AD Sicherheitsstandards](../active-directory/fundamentals/concept-fundamentals-security-defaults.md) aktivieren, um zu erzwingen, dass alle Administratorkonten MFA verwenden.

## <a name="get-your-tenant-name"></a>Abrufen des Mandantennamens

Führen Sie die folgenden Schritte aus, um den Namen Ihres Azure AD B2C-Mandanten abzurufen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.
1. Kopieren Sie in der **Übersicht** den **Domänennamen**.

![Screenshot: Abrufen des Azure AD B2C-Mandantennamens](./media/tenant-management/get-azure-ad-b2c-tenant-name.png)  

## <a name="get-your-tenant-id"></a>Abrufen Ihrer Mandanten-ID

Führen Sie die folgenden Schritte aus, um Ihre Azure AD B2C-Mandanten-ID abzurufen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Suchen Sie im Azure-Portal nach **Azure Active Directory**, und wählen Sie es aus.
1. Kopieren Sie in der **Übersicht** die **Mandanten-ID**.

![Screenshot: Abrufen der Azure AD B2C-Mandanten-ID](./media/tenant-management/get-azure-ad-b2c-tenant-id.png)  

## <a name="next-steps"></a>Nächste Schritte

- [Bereinigen von Ressourcen und Löschen des Mandanten](tutorial-delete-tenant.md)
