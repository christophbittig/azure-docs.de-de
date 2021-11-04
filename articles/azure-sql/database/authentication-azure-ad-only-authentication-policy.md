---
title: Azure Policy für reine Azure Active Directory-Authentifizierung
description: Dieser Artikel enthält Informationen zum Erzwingen einer Azure-Richtlinie zum Erstellen einer Azure SQL-Datenbank oder Azure SQL Managed Instance, bei der die reine Azure Active Directory (Azure AD)-Authentifizierung aktiviert ist.
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
ms.service: sql-db-mi
ms.subservice: security
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 11/02/2021
ms.openlocfilehash: 7315b27fccf261dcf25ffe347a22fbb41e0d52cb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458796"
---
# <a name="azure-policy-for-azure-active-directory-only-authentication-with-azure-sql"></a>Azure Policy für reine Azure Active Directory-Authentifizierung mit Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure Policy kann die Erstellung einer Azure SQL-Datenbank oder Azure SQL Managed Instance erzwingen, bei der die [reine Azure AD-Authentifizierung](authentication-azure-ad-only-authentication.md) während der Bereitstellung aktiviert ist. Wenn diese Richtlinie eingerichtet ist, schlagen alle Versuche, einen [logischen Server in Azure](logical-servers.md) oder eine verwaltete Instanz zu erstellen, fehl, wenn bei der Erstellung nicht die reine Azure AD-Authentifizierung aktiviert wird.

Azure Policy kann auf das gesamte Azure-Abonnement oder nur innerhalb einer Ressourcengruppe angewendet werden.

Zwei neue integrierte Richtlinien wurden in Azure Policy eingeführt:

- Für Azure SQL-Datenbank darf nur die Azure Active Directory-Authentifizierung aktiviert sein
- Für Azure SQL Managed Instance darf nur die Azure Active Directory-Authentifizierung aktiviert sein

Weitere Informationen zu Azure Policy finden Sie unter [Was ist Azure Policy?](../../governance/policy/overview.md) und [Struktur von Azure Policy-Definitionen](../../governance/policy/concepts/definition-structure.md).

## <a name="permissions"></a>Berechtigungen

Eine Übersicht über die Berechtigungen, die zum Verwalten von Azure Policy erforderlich sind, finden Sie unter [Azure RBAC-Berechtigungen in Azure Policy](../../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy).

### <a name="actions"></a>Aktionen

Wenn Sie eine benutzerdefinierte Rolle zum Verwalten von Azure Policy verwenden, sind die folgenden [Aktionen](../../role-based-access-control/role-definitions.md#actions) erforderlich.

- */Lesen
- Microsoft.Authorization/policyassignments/*
- Microsoft.Authorization/policydefinitions/*
- Microsoft.Authorization/policyexemptions/*
- Microsoft.Authorization/policysetdefinitions/*
- Microsoft.PolicyInsights/*

Weitere Informationen zu benutzerdefinierten Rollen finden Sie unter [Benutzerdefinierte Azure-Rollen](../../role-based-access-control/custom-roles.md).

## <a name="manage-azure-policy-for-azure-ad-only-authentication"></a>Verwalten von Azure Policy für die reine Azure AD-Authentifizierung

Die Richtlinien für die reine Azure AD-Authentifizierung können Sie verwalten, indem Sie zum [Azure-Portal](https://portal.azure.com) wechseln und nach dem Dienst **Policy** suchen. Suchen Sie unter **Definitionen** nach *Azure Active Directory only authentication* (Reine Azure Active Directory-Authentifizierung).

:::image type="content" source="media/authentication-azure-ad-only-authentication-policy/policy-azure-ad-only-authentication.png" alt-text="Screenshot: Azure Policy für reine Azure AD-Authentifizierung":::

Eine Anleitung zum Hinzufügen einer Azure Policy für die reine Azure AD-Authentifizierung finden Sie unter [Verwenden von Azure Policy zur Erzwingung von reiner Azure Active Directory-Authentifizierung mit Azure SQL](authentication-azure-ad-only-authentication-policy-how-to.md).

Für diese Richtlinien gibt es drei Auswirkungen:

- **Überwachung**: Die Standardeinstellung, mit der ein Überwachungsbericht in den Azure Policy-Aktivitätsprotokollen erfasst wird
- **Verweigern**: Verhindert die Erstellung eines logischen Servers oder einer verwalteten Instanz ohne aktivierte [reine Azure AD-Authentifizierung](authentication-azure-ad-only-authentication.md)
- **Deaktiviert**: Deaktiviert die Richtlinie und verhindert nicht, dass Benutzer einen logischen Server oder eine verwaltete Instanz ohne aktivierte reine Azure AD-Authentifizierung erstellen

Wenn die Azure Policy für die reine Azure AD-Authentifizierung auf **Verweigern** festgelegt ist, schlägt die Erstellung eines logischen Azure SQL-Servers oder einer verwalteten Instanz fehl. Die Details zu diesem Fehler werden im **Aktivitätsprotokoll** der Ressourcengruppe aufgezeichnet.

## <a name="policy-compliance"></a>Richtlinienkonformität

Über die Einstellung **Konformität** des Diensts **Policy** können Sie den Konformitätszustand anzeigen. Der **Konformitätszustand** gibt an, ob der Server oder die verwaltete Instanz derzeit mit aktivierter reiner Azure AD-Authentifizierung konform ist. 

Azure Policy kann verhindern, dass ein neuer logischer Server oder eine verwaltete Instanz erstellt wird, ohne dass die reine Azure AD-Authentifizierung aktiviert ist. Das Feature kann jedoch nach der Erstellung des Servers oder der verwalteten Instanz geändert werden. Wenn ein Benutzer die reine Azure AD-Authentifizierung deaktiviert hat, nachdem der Server oder die verwaltete Instanz erstellt wurde, ist der Konformitätszustand `Non-compliant`, wenn Azure Policy auf **Verweigern** festgelegt ist.

:::image type="content" source="media/authentication-azure-ad-only-authentication-policy/check-compliance-policy-azure-ad-only-authentication.png" alt-text="Screenshot: Azure Policy-Menü „Konformität“ für reine Azure AD-Authentifizierung":::

## <a name="limitations"></a>Einschränkungen

- Azure Policy erzwingt die reine Azure AD-Authentifizierung während der Erstellung eines logischen Servers oder einer verwalteten Instanz. Sobald der Server erstellt wurde, können autorisierte Azure AD-Benutzer mit speziellen Rollen (z. B. SQL Security Manager) das Feature für die reine Azure AD-Authentifizierung deaktivieren. Die Azure Policy erlaubt dies, aber in diesem Fall wird der Server oder die verwaltete Instanz im Konformitätsbericht als `Non-compliant` aufgeführt, und der Bericht gibt den Namen des Servers oder der verwalteten Instanz an.  
- Weitere Hinweise, bekannte Probleme und die erforderlichen Berechtigungen finden Sie unter [Reine Azure AD-Authentifizierung](authentication-azure-ad-only-authentication.md).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Verwenden von Azure Policy zur Erzwingung der reinen Azure Active Directory-Authentifizierung mit Azure SQL](authentication-azure-ad-only-authentication-policy-how-to.md)