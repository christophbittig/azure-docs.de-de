---
title: Mandanten, Benutzer und Rollen in Azure Lighthouse-Szenarien
description: Informationen zur Verwendung von Azure Active Directory-Mandanten, -Benutzern und -Rollen in Azure Lighthouse-Szenarien.
ms.date: 06/23/2021
ms.topic: conceptual
ms.openlocfilehash: cdf8c10d52e0add4513d42a99d2054e1af0ed796
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2021
ms.locfileid: "112542254"
---
# <a name="tenants-users-and-roles-in-azure-lighthouse-scenarios"></a>Mandanten, Benutzer und Rollen in Azure Lighthouse-Szenarien

Vor dem Onboarding von Kunden für [Azure Lighthouse](../overview.md) ist es wichtig, die Funktionsweise von Azure Active Directory-Mandanten, -Benutzern und -Rollen sowie deren Verwendung in Azure Lighthouse-Szenarien zu verstehen.

Ein *Mandant* ist eine dedizierte und vertrauenswürdige Instanz von Azure Active Directory (Azure AD). Typischerweise repräsentiert jeder Mandant eine einzelne Organisation. Azure Lighthouse ermöglicht eine [logische Projektion](architecture.md#logical-projection) von Ressourcen von einem Mandanten auf einen anderen Mandanten. Dies ermöglicht es Benutzern im verwaltenden Mandanten (z. B. dem Mandanten eines Dienstanbieters), auf delegierte Ressourcen in einem Kundenmandanten zuzugreifen. [Unternehmen mit mehreren Mandanten können auf diese Weise ihre Verwaltungsabläufe zentralisieren](enterprise.md).

Um diese logische Projektion zu erreichen, muss für ein Abonnement (oder mindestens eine Ressourcengruppe in einem Abonnement) im Kundenmandanten ein *Onboarding* für Azure Lighthouse durchgeführt werden. Dieser Onboardingprozess kann entweder [über Azure Resource Manager-Vorlagen](../how-to/onboard-customer.md) oder durch das [Veröffentlichen eines öffentlichen oder privaten Angebots im Azure Marketplace](../how-to/publish-managed-services-offers.md) erfolgen.

Unabhängig von der gewählten Onboardingmethode müssen Sie *Autorisierungen* definieren. Jede Autorisierung gibt eine **principalId** an, die Zugriff auf die delegierten Ressourcen erhält. Außerdem wird eine integrierte Rolle angegeben, mit der die Berechtigungen dieser Benutzer für die Ressourcen festgelegt werden. Die **principalId** definiert einen Azure AD-Benutzer, eine Gruppe oder einen Dienstprinzipal im verwaltenden Mandanten.

> [!NOTE]
> Wenn nicht explizit angegeben, können sich Verweise auf einen „Benutzer“ in der Azure Lighthouse-Dokumentation auf einen Azure AD-Benutzer, eine Gruppe oder einen Dienstprinzipal in einer Autorisierung beziehen.

## <a name="best-practices-for-defining-users-and-roles"></a>Best Practices für das Definieren von Benutzern und Rollen

Beim Erstellen Ihrer Autorisierungen empfehlen wir die Einhaltung der folgenden Best Practices:

- In den meisten Fällen sollten Sie einer Azure AD-Benutzergruppe oder einem Dienstprinzipal Berechtigungen zuzuweisen, anstatt eine Reihe einzelner Benutzerkonten. Auf diese Weise können Sie den Zugriff für einzelne Benutzer hinzufügen oder entfernen, ohne den Plan aktualisieren und erneut veröffentlichen zu müssen, wenn sich ihre Zugriffsanforderungen ändern.
- Beachten Sie, dass Sie das Prinzip der geringsten Rechte befolgen, damit Benutzer nur über die Berechtigungen verfügen, die zum Durchführen Ihrer Aufgaben erforderlich sind, um die Wahrscheinlichkeit von unbeabsichtigten Fehlern zu verringern. Weitere Informationen finden Sie unter [Empfohlene Sicherheitsmaßnahmen](../concepts/recommended-security-practices.md).
- Fügen Sie einen Benutzer mit der [Rolle „Registrierungszuweisung für verwaltete Dienste löschen“](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) hinzu, damit Sie den [Zugriff auf die Delegierung](../how-to/remove-delegation.md) später bei Bedarf entfernen können. Wenn diese Rolle nicht zugewiesen wird, können delegierte Ressourcen nur durch einen Benutzer im Kundenmandanten entfernt werden.
- Stellen Sie sicher, dass allen Benutzern, die die [Seite „Meine Kunden“ im Azure-Portal anzeigen](../how-to/view-manage-customers.md) müssen, die Rolle [Leser](../../role-based-access-control/built-in-roles.md#reader) (oder eine andere integrierte Rolle mit Lesezugriff) zugewiesen wird.

> [!IMPORTANT]
> Um Berechtigungen für eine Azure AD-Gruppe hinzuzufügen, muss der **Gruppentyp** auf **Sicherheit** festgelegt werden. Diese Option wird bei der Erstellung der Gruppe ausgewählt. Weitere Informationen dazu finden Sie in [Erstellen einer Basisgruppe und Hinzufügen von Mitgliedern mit Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="role-support-for-azure-lighthouse"></a>Rollenunterstützung für Azure Lighthouse

Bei der Definition einer Berechtigung muss jedem Benutzerkonto eine der [integrierten Azure-Rollen](../../role-based-access-control/built-in-roles.md) zugewiesen werden. Benutzerdefinierte Rollen und [klassische Abonnementadministratorrollen](../../role-based-access-control/classic-administrators.md) werden nicht unterstützt.

Für die Azure Lighthouse werden aktuell alle [integrierten Rollen](../../role-based-access-control/built-in-roles.md) unterstützt. Dabei gelten die folgenden Ausnahmen:

- Die Rolle [Besitzer](../../role-based-access-control/built-in-roles.md#owner) wird nicht unterstützt.
- Integrierte Rollen mit der Berechtigung [DataActions](../../role-based-access-control/role-definitions.md#dataactions) werden nicht unterstützt.
- Die integrierte Rolle [Benutzerzugriffsadministrator](../../role-based-access-control/built-in-roles.md#user-access-administrator) wird unterstützt, aber nur für den eingeschränkten Zweck der [Zuweisung von Rollen zu einer verwalteten Identität im Kundenmandanten](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant). Weitere Berechtigungen, die üblicherweise über diese Rolle gewährt werden, werden nicht angewendet. Wenn Sie einen Benutzer mit dieser Rolle definieren, müssen Sie auch die integrierten Rollen angeben, die dieser Benutzer verwalteten Identitäten zuweisen kann.

> [!NOTE]
> Nachdem Azure eine passende neue integrierte Rolle hinzugefügt wurde, kann sie beim [Onboarding eines Kunden mithilfe von Azure Resource Manager-Vorlagen](../how-to/onboard-customer.md) zugewiesen werden. Es kann zu einer Verzögerung kommen, bevor die neu hinzugefügte Rolle beim [Veröffentlichen eines verwalteten Dienstangebots](../how-to/publish-managed-services-offers.md) im Partner Center verfügbar wird.

## <a name="transferring-delegated-subscriptions-between-azure-ad-tenants"></a>Übertragen von delegierten Abonnements zwischen Azure AD-Mandanten

Wenn ein Abonnement [zu einem anderen Azure AD-Mandantenkonto übertragen](../../cost-management-billing/manage/billing-subscription-transfer.md#transfer-a-subscription-to-another-azure-ad-tenant-account) wird, bleiben die beim [Azure Lighthouse Onboardingprozess](architecture.md#delegation-resources-created-in-the-customer-tenant) erstellte [Registrierungsdefinition und die Registrierungszuordnungsressourcen](../how-to/onboard-customer.md) erhalten. Das bedeutet, dass der durch Azure Lighthouse gewährte Zugang zur Mandantenverwaltung für dieses Abonnement (oder für delegierte Ressourcengruppen innerhalb dieses Abonnements) gültig bleibt.

Die einzige Ausnahme ist, wenn das Abonnement an einen Azure AD-Mandanten übertragen wird, dem es bereits delegiert wurde. In diesem Fall werden die Delegationsressourcen für diesen Mandanten entfernt und der durch Azure Lighthouse gewährte Zugang gilt nicht mehr, da das Abonnement jetzt direkt zu diesem Mandanten gehört (und ihm nicht mehr durch Azure Lighthouse delegiert wird). Wenn dieses Abonnement jedoch auch anderen verwaltenden Mandanten delegiert wurde, behalten diese anderen verwaltenden Mandanten denselben Zugang zum Abonnement.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [Empfohlene Sicherheitsmaßnahmen für Azure Lighthouse](recommended-security-practices.md).
- Onboarding Ihrer Kunden in Azure Lighthouse, entweder unter [Verwendung von Azure Resource Manager-Vorlagen](../how-to/onboard-customer.md) oder mittels [Veröffentlichung eines privaten oder öffentlichen Angebots für verwaltete Dienste im Azure Marketplace](../how-to/publish-managed-services-offers.md).
