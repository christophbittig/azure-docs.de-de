---
title: Hinzufügen eines vorhandenen Azure-Abonnements zu Ihrem Mandanten – Azure AD
description: Anleitungen zum Hinzufügen eines vorhandenen Azure-Abonnements zu Ihrem Azure Active Directory (Azure AD)-Mandanten.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 03/05/2021
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18, contperf-fy20q4
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc428a79e38f390d926f268bd8147eb7ee06a648
ms.sourcegitcommit: 04d2dec0316576388e6e10c1558b0a0b3688d01c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/21/2021
ms.locfileid: "112421823"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Zuordnen oder Hinzufügen eines Azure-Abonnements zu Ihrem Azure Active Directory-Mandanten

Ein Azure-Abonnement weist eine Vertrauensstellung mit Azure Active Directory (Azure AD) auf. Ein Abonnement vertraut Azure AD bei der Authentifizierung von Benutzern, Diensten und Geräten.

Mehrere Abonnements können demselben Azure AD-Verzeichnis vertrauen. Jedes Abonnement kann nur einem einzelnen Verzeichnis vertrauen.

Mindestens ein Azure-Abonnement kann eine Vertrauensstellung mit einer Instanz von Azure Active Directory (Azure AD) einrichten, um Sicherheitsprinzipale und Geräte für Azure-Dienste zu authentifizieren und zu autorisieren.  Wenn ein Abonnement abläuft, verbleibt die vertrauenswürdige Instanz des Azure AD-Diensts, aber die Sicherheitsprinzipale können auf Azure-Ressourcen nicht mehr zugreifen.

Wenn sich ein Benutzer für einen Microsoft-Clouddienst registriert, wird ein neuer Azure AD-Mandant erstellt, und der Benutzer wird Mitglied der Rolle „Globaler Administrator“. Wenn ein Besitzer eines Abonnements sein Abonnement jedoch mit einem vorhandenen Mandanten verbindet, wird ihm die Rolle „Globaler Administrator“ nicht zugewiesen.

Alle Ihre Benutzer verfügen über ein einzelnes *Basisverzeichnis* für die Authentifizierung. Ihre Benutzer können jedoch auch Gäste in anderen Verzeichnissen sein. Ihnen werden sowohl das Basis- als auch das Gastverzeichnis für jeden Benutzer in Azure AD angezeigt.

:::image type="content" source="media/active-directory-how-subscriptions-associated-directory/trust-relationship-azure-ad.png" alt-text="Screenshot: Vertrauensstellung zwischen Azure-Abonnements und aktiven Azure-Verzeichnissen":::

> [!Important]
> Wenn Sie ein Abonnement einem anderen Verzeichnis zuordnen, verlieren Benutzer, denen Rollen mit der [rollenbasierten Zugriffssteuerung von Azure](../../role-based-access-control/role-assignments-portal.md) zugewiesen wurden, den Zugriff. Klassische Abonnementadministratoren (Dienstadministrator und Co-Admin) verlieren ebenfalls ihren Zugriff.
>
> Das Verschieben Ihres Azure Kubernetes Service-Clusters (AKS-Clusters) in ein anderes Abonnement oder das Verschieben des Abonnements, das den Cluster besitzt, in einen neuen Mandanten führt dazu, dass der Cluster seine Funktionalität aufgrund des Verlusts von Rollenzuweisungen und Dienstprinzipalrechten verliert. Weitere Informationen zu AKS finden Sie unter [Azure Kubernetes Service (AKS)](../../aks/index.yml).

## <a name="before-you-begin"></a>Voraussetzungen

Bevor Sie Ihr Abonnement zuweisen oder hinzufügen können, müssen Sie die folgenden Aufgaben ausführen:

- Überprüfen Sie die folgende Liste mit Änderungen, die nach dem Zuordnen oder Hinzufügen Ihres Abonnements vorgenommen werden, und ihre möglichen Folgen für Sie:

  - Benutzer, denen Rollen mithilfe von Azure RBAC zugewiesen wurden, verlieren den Zugriff.
  - Dienstadministrator und Co-Admins verlieren den Zugriff.
  - Wenn Sie über Schlüsseltresore verfügen, kann darauf nicht mehr zugegriffen werden. Sie müssen das Problem nach der Zuordnung beheben.
  - Wenn Sie über verwaltete Identitäten für Ressourcen wie Virtual Machines oder Logic Apps verfügen, müssen Sie diese nach der Zuordnung erneut aktivieren oder erstellen.
  - Wenn Sie Azure Stack registriert haben, müssen Sie es nach der Zuordnung erneut registrieren.
  - Weitere Informationen finden Sie unter [Übertragen eines Azure-Abonnements in ein anderes Azure AD-Verzeichnis](../../role-based-access-control/transfer-subscription.md).

- Melden Sie sich mit einem Konto an, das:

  - Über die Rollenzuweisung [Besitzer](../../role-based-access-control/built-in-roles.md#owner) für das Abonnement verfügt. Informationen zum Zuweisen der Rolle „Besitzer“ finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../../role-based-access-control/role-assignments-portal.md).
  - Ist im aktuellen Verzeichnis und im neuen Verzeichnis vorhanden. Das aktuelle Verzeichnis ist dem Abonnement zugeordnet. Verknüpfen Sie das neue Verzeichnis mit dem Abonnement. Weitere Informationen zum Zugriff auf ein anderes Verzeichnis finden Sie unter [Hinzufügen von Azure Active Directory B2B-Zusammenarbeitsbenutzern über das Azure-Portal](../external-identities/add-users-administrator.md).

- Achten Sie darauf, dass Sie kein Azure-Cloud-Dienstanbieter-Abonnement (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), kein internes Microsoft-Abonnement (MS-AZR-0015P) und kein Microsoft Azure for Students Starter-Abonnement (MS-AZR-0144P) verwenden.

## <a name="associate-a-subscription-to-a-directory"></a>Zuordnen eines Abonnements zu einem Verzeichnis<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

Führen Sie die folgenden Schritte aus, um Ihrem Azure AD-Verzeichnis ein vorhandenes Abonnement zuzuweisen:

1. Melden Sie sich an, und wählen Sie im [Azure-Portal auf der Seite „Abonnements“](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ein Abonnement aus.

1. Wählen Sie **Verzeichnis ändern** aus.

   :::image type="content" source="media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png" alt-text="Screenshot der Seite „Abonnements“ mit hervorgehobener Option „Verzeichnis ändern“.":::

1. Überprüfen Sie alle angezeigten Warnungen, und wählen Sie dann **Ändern** aus.

   :::image type="content" source="media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png" alt-text="Screenshot der Seite „Verzeichnis ändern“ mit einem Beispielverzeichnis und der hervorgehobenen Schaltfläche „Ändern“.":::

   Nachdem das Verzeichnis für das Abonnement geändert wurde, wird eine Erfolgsmeldung angezeigt.

1. Wählen Sie auf der Abonnementseite **Verzeichnis wechseln** aus, um zu Ihrem neuen Verzeichnis zu wechseln.

   :::image type="content" source="media/active-directory-how-subscriptions-associated-directory/directory-switcher.png" alt-text="Screenshot der Seite „Verzeichnis wechseln“ mit Beispielinformationen.":::

   Es kann mehrere Stunden dauern, bis alles ordnungsgemäß angezeigt wird. Wenn es zu lange dauert, überprüfen Sie den **globalen Abonnementfilter**. Stellen Sie sicher, dass das verschobene Abonnement nicht ausgeblendet ist. Möglicherweise müssen Sie sich beim Azure-Portal abmelden und wieder anmelden, damit das neue Verzeichnis angezeigt wird.

Das Ändern des Abonnementverzeichnisses erfolgt auf Dienstebene, sodass sich der Vorgang nicht auf die Abonnementabrechnung auswirkt. Um das ursprüngliche Verzeichnis zu löschen, müssen Sie die Abonnementabrechnung einem neuen Kontoadministrator übertragen. Weitere Informationen zum Übertragen der Abrechnung finden Sie unter [Übertragen des Besitzes eines Azure-Abonnements auf ein anderes Konto](../../cost-management-billing/manage/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Schritte nach der Zuordnung

Nachdem Sie ein Abonnement einem anderen Verzeichnis zugeordnet haben, müssen Sie möglicherweise die folgenden Aufgaben ausführen, um den Vorgang fortzusetzen:

- Wenn Sie über Schlüsseltresore verfügen, müssen Sie die Mandanten-ID des Schlüsseltresors ändern. Weitere Informationen finden Sie unter [Ändern der Mandanten-ID des Schlüsseltresors nach einer Abonnementverschiebung](../../key-vault/general/move-subscription.md).

- Wenn Sie vom System zugewiesene verwaltete Identitäten für Ressourcen verwendet haben, müssen Sie diese nochmal aktivieren. Wenn Sie vom Benutzer zugewiesene verwaltete Identitäten verwendet haben, müssen Sie diese nochmal erstellen. Nach dem erneuten Aktivieren oder Erstellen der verwalteten Identitäten müssen Sie die Berechtigungen, die diesen Identitäten zugewiesen sind, erneut einrichten. Weitere Informationen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../managed-identities-azure-resources/overview.md).

- Wenn Sie mit diesem Abonnement eine Azure Stack-Instanz registriert haben, müssen Sie diese nochmal registrieren. Weitere Informationen finden Sie unter [Registrieren von Azure Stack Hub in Azure](/azure-stack/operator/azure-stack-registration).

- Weitere Informationen finden Sie unter [Übertragen eines Azure-Abonnements in ein anderes Azure AD-Verzeichnis](../../role-based-access-control/transfer-subscription.md).

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Erstellen eines neuen Azure AD-Mandanten finden Sie unter [Schnellstart: Erstellen eines neuen Mandanten in Azure Active Directory](active-directory-access-create-new-tenant.md).

- Weitere Informationen zum Steuern des Zugriffs auf Ressourcen durch Microsoft Azure finden Sie unter [Administratorrollen für klassische Abonnements, Azure-Rollen und Azure AD-Administratorrollen](../../role-based-access-control/rbac-and-directory-admin-roles.md).

- Weitere Informationen zum Zuweisen von Rollen in Azure AD finden Sie unter [Zuweisen von Administrator- und anderen Rollen zu Benutzern mithilfe von Azure Active Directory](active-directory-users-assign-role-azure-portal.md).
