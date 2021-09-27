---
title: Verwalten von Azure Active Directory B2C
titleSuffix: Azure Active Directory B2C
description: Erfahren Sie, wie Sie Ihren Azure Active Directory B2C-Mandanten verwalten. Erfahren Sie, welche Azure AD-Features in Azure AD B2C unterstützt werden, wie Sie Administratorrollen zum Verwalten von Ressourcen verwenden und Ihrem Azure AD B2C-Mandanten Arbeitskonten und Gastbenutzer hinzufügen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3851f9893799902c9cdcda71db67a9152de028ec
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128595805"
---
# <a name="manage-your-azure-active-directory-b2c-tenant"></a>Verwalten Ihres Azure Active Directory B2C-Mandanten

Ein Mandant in Azure Active Directory B2C (Azure AD B2C) ist ein Verzeichnis von Consumer-Benutzern. Jeder Azure AD B2C-Mandant unterscheidet sich von anderen Azure AD B2C-Mandanten und ist von diesen getrennt. Der Azure AD B2C-Mandant unterscheidet sich von einem Azure Active Directory-Mandanten, wie Sie ihn möglicherweise bereits verwenden. In diesem Artikel erfahren Sie, wie Sie Ihren Azure AD B2C-Mandanten verwalten.

## <a name="supported-azure-ad-features"></a>Unterstützte Azure AD-Features

Azure AD B2C basiert auf der Azure AD-Plattform. Die folgenden Azure AD-Features können in Ihrem Azure AD B2C-Mandanten verwendet werden.

|Funktion  |Azure AD  | Azure AD B2C |
|---------|---------|---------|
| [Gruppen](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) | Gruppen können zum Verwalten von Administrator- und Benutzerkonten verwendet werden.| Gruppen können zum Verwalten von Administratorkonten verwendet werden. [Consumerkonten](user-overview.md#consumer-user) dürfen keiner Gruppe angehören. |
| [Einladen von External Identities-Gästen](../active-directory//external-identities/add-users-administrator.md)| Sie können Gastbenutzer einladen und External Identities-Features wie Verbinden und Anmelden mit Facebook- und Google-Konten konfigurieren. | Sie können nur ein Microsoft-Konto oder einen Azure AD Benutzer als Gast zu Ihrem Azure AD-Mandanten einladen, um auf Anwendungen zuzugreifen oder Mandanten zu verwalten. Für [Consumer-Konten](user-overview.md#consumer-user) verwenden Sie Azure AD B2C Benutzerflows und benutzerdefinierte Richtlinien, um Benutzer zu verwalten und sich bei externen Identitätsanbietern wie Google oder Facebook zu registrieren oder anzumelden. |
| [Rollen und Administratoren](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)| Wird für Administrator- und Benutzerkonten vollständig unterstützt. | Rollen werden für [Consumer-Konten](user-overview.md#consumer-user)nicht unterstützt. Consumer-Konten haben keinen Zugriff auf Azure-Ressourcen.|
| [Benutzerdefinierte Domänennamen](../active-directory/fundamentals/add-custom-domain.md) |  Sie können Azure AD benutzerdefinierte Domänen nur für Administratorkonten verwenden. | [Consumer-Konten](user-overview.md#consumer-user) können sich mit einem Benutzernamen, einer Telefonnummer oder einer beliebigen E-Mail-Adresse anmelden. Sie können [benutzerdefinierte Domänen](custom-domain.md) in Ihren Umleitungs-URLs verwenden.|
| [Bedingter Zugriff](../active-directory/conditional-access/overview.md) | Wird für Administrator- und Benutzerkonten vollständig unterstützt. | Eine Teilmenge der Azure AD-Features für den bedingten Zugriff wird für [Consumerkonten](user-overview.md#consumer-user) unterstützt. Erfahren Sie, wie Sie den [bedingten Zugriff](conditional-access-user-flow.md) in Azure AD B2C konfigurieren.|
| [Premium P1](https://azure.microsoft.com/pricing/details/active-directory) | Vollständig unterstützt für Azure AD Premium P1-Features. Beispiele: [Kennwortschutz](../active-directory/authentication/concept-password-ban-bad.md), [Hybrididentitäten](../active-directory/hybrid/whatis-hybrid-identity.md), [Bedingter Zugriff](../active-directory/roles/permissions-reference.md#), [Dynamische Gruppen](../active-directory/enterprise-users/groups-create-rule.md) usw. | Eine Teilmenge der Azure AD-Features für den bedingten Zugriff wird für [Consumerkonten](user-overview.md#consumer-user) unterstützt. Erfahren Sie, wie Sie den [bedingten Zugriff](conditional-access-user-flow.md) in Azure AD B2C konfigurieren.|
| [Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) | Vollständig unterstützt für Azure AD Premium P2-Features. Beispiele: [Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) und [Identity Governance](../active-directory/governance/identity-governance-overview.md).  | Eine Teilmenge der Azure AD Identity Protection-Features wird für [Consumerkonten](user-overview.md#consumer-user) unterstützt. Erfahren Sie, wie Sie [Risiken mit Identity Protection untersuchen](identity-protection-investigate-risk.md) und den [bedingten Zugriff](conditional-access-user-flow.md) in Azure AD B2C konfigurieren. |

## <a name="other-azure-resources-in-your-tenant"></a>Andere Azure-Ressourcen in Ihrem Mandanten

In einem Azure AD B2C-Mandanten können Sie keine anderen Azure-Ressourcen wie virtuelle Computer, Azure-Web-Apps oder Azure-Funktionen bereitstellen. Sie müssen diese Ressourcen in Ihrem Azure AD erstellen.

## <a name="azure-ad-b2c-accounts-overview"></a>Übersicht über die Azure AD B2C-Konten

Die folgenden Arten von Konten können in einem Azure AD B2C-Mandanten erstellt werden:

In einem Azure AD B2C-Mandanten gibt es verschiedene Arten von Konten, die wie in diesem Artikel [Übersicht über Benutzerkonten Azure Active Directory B2C](user-overview.md) beschreiben erstellt werden können.

- **Geschäftskonto**: Ein Geschäftskonto kann auf Ressourcen in einem Mandanten zugreifen und mit einer Administratorrolle Mandanten verwalten.
- **Gastkonto**: Ein Gastkonto kann nur ein Microsoft-Konto oder ein Azure Active Directory-Benutzerkonto sein, das für den Zugriff auf Anwendungen oder die Verwaltung von Mandanten verwendet werden kann.
- **Consumerkonto**: Ein Consumerkonto wird von einem Benutzer der Anwendungen verwendet, die Sie bei Azure AD B2C registriert haben.

Weitere Informationen zu diesen Kontotypen finden Sie unter [Übersicht über Benutzerkonten in Azure Active Directory B2C](user-overview.md). Jeder Benutzer, der zum Verwalten Ihres Azure AD B2C-Mandanten zugewiesen wird, muss über ein Azure AD-Benutzerkonto verfügen, damit er auf Azure-bezogene Dienste zugreifen kann. Sie können einen solchen Benutzer hinzufügen, indem Sie in Ihrem Azure AD B2C-Mandanten ein [Konto (Arbeitskonto) erstellen](#add-an-administrator-work-account) oder sie als Gastbenutzer zu Ihrem Azure AD B2C-Mandanten [einladen](#invite-an-administrator-guest-account).

## <a name="use-roles-to-control-resource-access"></a>Verwalten des Ressourcenzugriffs mithilfe von Rollen

Bei der Planung Ihrer Zugriffssteuerungsstrategie ist es am besten, Benutzern die Rolle mit den geringsten Berechtigungen zuzuweisen, die für den Zugriff auf Ressourcen erforderlich ist. In der folgenden Tabelle werden die primären Ressourcen in Ihrem Azure AD B2C-Mandanten und die am besten geeigneten Administratorrollen für die Benutzer beschrieben, die sie verwalten.

|Resource  |BESCHREIBUNG  |Role  |
|---------|---------|---------|
|[Anwendungsregistrierungen](tutorial-register-applications.md) | Erstellen und verwalten Sie alle Aspekte Ihrer Web-, mobilen und nativen Anwendungsregistrierungen in Azure AD B2C.|[Anwendungsadministrator](../active-directory/roles/permissions-reference.md#application-administrator)|
|[Identitätsanbieter](add-identity-provider.md)| Konfigurieren Sie den [lokalen Identitätsanbieter](identity-provider-local.md) und externe soziale Netzwerke oder Unternehmensidentitätsanbieter. | [Externer Identitätsanbieteradministrator](../active-directory/roles/permissions-reference.md#external-identity-provider-administrator)|
|[API-Connectors](add-api-connector.md)| Integrieren Sie Ihre Benutzerabläufe mit Web-APIs, um das Benutzererlebnis anzupassen und mit externen Systemen zu integrieren.|[Administrator für Benutzerflows mit externer ID](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[Unternehmensbranding](customize-ui.md#configure-company-branding)| Passen Sie Ihre Benutzerflow-Seiten an.| [Globaler Administrator](../active-directory/roles/permissions-reference.md#global-administrator)|
|[Benutzerattribute](user-flow-custom-attributes.md)| Benutzer mit dieser Rolle können benutzerdefinierte Attribute, die für alle Benutzerflows im Mandanten verfügbar sind, hinzufügen und löschen.| [Administrator für Benutzerflowattribute mit externer ID](../active-directory/roles/permissions-reference.md#external-id-user-flow-attribute-administrator)|
|Verwalten von Benutzern| Verwalten Sie [Consumer-Konten](manage-users-portal.md) und Administratorkonten wie in diesem Artikel beschrieben.| [Benutzeradministrator](../active-directory/roles/permissions-reference.md#user-administrator)|
|Rollen und Administratoren| Verwalten von Rollenzuweisungen im Azure AD B2C-Verzeichnis. Erstellen und Verwalten von Gruppen, die Azure AD B2C-Rollen zugewiesen werden können. |[Globaler Administrator](../active-directory/roles/permissions-reference.md#global-administrator), [Administrator für privilegierte Rollen](../active-directory/roles/permissions-reference.md#privileged-role-administrator)|
|[Benutzerabläufe](user-flow-overview.md)|Mit Benutzerflows konfigurieren und aktivieren Sie schnell häufige Identitätsaufgaben wie Registrierung, Anmeldung und Bearbeitung des Profils.| [Administrator für Benutzerflows mit externer ID](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[Benutzerdefinierte Richtlinien](user-flow-overview.md)| Erstellen, Lesen, Aktualisieren und Löschen aller benutzerdefinierten Richtlinien in Azure AD B2C.| [B2C-IEF-Richtlinienadministrator](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)|
|[Richtlinienschlüssel](policy-keys-overview.md)|Hinzufügen und Verwalten von Verschlüsselungsschlüsseln zum Signieren und Überprüfen von Token, geheimen Clientschlüsseln, Zertifikaten und Kennwörtern in benutzerdefinierten Richtlinien.|[B2C-IEF-Schlüsselsatzadministrator](../active-directory/roles/permissions-reference.md#b2c-ief-keyset-administrator)|


## <a name="add-an-administrator-work-account"></a>Hinzufügen eines Administrators (Arbeitskonto)

Führen Sie diese Schritte aus, um ein neues Administratorenkonto zu erstellen:

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com/) als Benutzer mit Berechtigungen der Rollen „Globaler Administrator“ oder „Administrator für privilegierte Rollen“ an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie unter **Azure-Dienste** die Option **Azure AD B2C** aus. Oder verwenden Sie das Suchfeld, um nach **Azure AD B2C** zu suchen und diese Option auszuwählen.
1. Wählen Sie unter **Verwalten** die Option **Benutzer** aus.
1. Wählen Sie **Neuer Benutzer** aus.
1. Geben Sie auf der Seite **Benutzer** Informationen für diesen Benutzer ein:

   - **Name**: Erforderlich. Der Vor- und Nachname des neuen Benutzers. Beispielsweise *Mary Parker*.
   - **Benutzername**. Erforderlich. Der Benutzername des neuen Benutzers. Beispiel: `mary@contoso.com`.
     Der Domänenteil des Benutzernamens muss entweder der anfängliche Standarddomänenname oder *\<yourdomainname>onmicrosoft.com* sein.
   - **Gruppen**. Optional können Sie den Benutzer einer oder mehreren vorhandenen Gruppen hinzufügen. Sie können den Benutzer auch später zu Gruppen hinzufügen. 
   - **Verzeichnisrolle**: Wenn Sie Azure AD-Administratorberechtigungen für den Benutzer benötigen, können Sie diese einer Azure AD-Rolle hinzufügen. Sie können dem Benutzer die Rolle „Globaler Administrator“ oder mindestens eine der eingeschränkten Administratorrollen in Azure AD zuweisen. Weitere Informationen zum Zuweisen von Rollen finden Sie unter [Verwenden von Rollen zum Steuern des Ressourcenzugriffs](#use-roles-to-control-resource-access).
   - **Angaben zum Beruf**: Hier können Sie weitere Informationen zum Benutzer hinzufügen oder dies später erledigen. 

1. Kopieren Sie das automatisch generierte Kennwort aus dem Feld **Kennwort**. Sie müssen dieses Kennwort dem Benutzer für die erste Anmeldung bereitstellen.
1. Klicken Sie auf **Erstellen**.

Der Benutzer wird erstellt und Ihrem Azure AD B2C-Mandanten hinzugefügt. Es ist besser, mindestens ein natives Arbeitskonto für Ihren Azure AD B2C-Mandanten zu verwenden, dem die Rolle „Globaler Administrator“ zugewiesen ist. Dieses Konto kann als Break-Glass-Konto betrachtet werden.

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

   - **Name**: Erforderlich. Der Vor- und Nachname des neuen Benutzers. Beispielsweise *Mary Parker*.
   - **E-Mail-Adresse**. Erforderlich. Die E-Mail-Adresse des Benutzers, den Sie einladen möchten. Beispiel: `mary@contoso.com`.   
   - **Persönliche Nachricht**: Sie fügen eine persönliche Nachricht hinzu, die in der Einladungs-E-Mail enthalten ist.
   - **Gruppen**. Optional können Sie den Benutzer einer oder mehreren vorhandenen Gruppen hinzufügen. Sie können den Benutzer auch später zu Gruppen hinzufügen.
   - **Verzeichnisrolle**: Wenn Sie Azure AD-Administratorberechtigungen für den Benutzer benötigen, können Sie diese einer Azure AD-Rolle hinzufügen. Sie können dem Benutzer die Rolle „Globaler Administrator“ oder mindestens eine der eingeschränkten Administratorrollen in Azure AD zuweisen. Weitere Informationen zum Zuweisen von Rollen finden Sie unter [Verwenden von Rollen zum Steuern des Ressourcenzugriffs](#use-roles-to-control-resource-access).
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

- [Erstellen eines Azure Active Directory B2C-Mandanten im Azure-Portal](tutorial-create-tenant.md)
