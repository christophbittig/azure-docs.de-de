---
title: Hinzufügen oder Ändern von Azure-Abonnementadministratoren
description: Beschreibt das Hinzufügen oder Ändern eines Azure-Abonnementadministrators mithilfe der rollenbasierten Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC).
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 10/28/2021
ms.author: banders
ms.openlocfilehash: ae4a48e62f3e61e6c3d56cfa9083a2a95b4b1a69
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131473298"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Hinzufügen oder Ändern von Azure-Abonnementadministratoren


Für die Verwaltung von Azure-Ressourcen müssen Sie über die entsprechende Administratorrolle verfügen. Azure bietet ein als [rollenbasierte Zugriffssteuerung](../../role-based-access-control/overview.md) (Azure Role-Based Access Control, Azure RBAC) bezeichnetes Autorisierungssystem mit verschiedenen integrierten Rollen, unter denen Sie wählen können. Sie können diesen Rollen verschiedene Gültigkeitsbereiche zuweisen, wie etwa Verwaltungsgruppe, Abonnement oder Ressourcengruppe. Standardmäßig kann die Person, die ein neues Azure-Abonnement erstellt, anderen Benutzern Administratorzugriff auf ein Abonnement gewähren.

In diesem Artikel wird beschrieben, wie die Administratorrolle für einen Benutzer mithilfe von Azure RBAC auf Abonnementebene hinzugefügt oder geändert werden kann.

Microsoft empfiehlt das Verwalten des Zugriffs auf Ressourcen mithilfe der Azure RBAC. Wenn Sie aber weiterhin das klassische Bereitstellungsmodell verwenden und die klassischen Ressourcen mit dem [PowerShell-Modul für die Azure-Dienstverwaltung](/powershell/module/servicemanagement/azure.service) verwalten, müssen Sie einen klassischen Administrator verwenden.

> [!TIP]
> Wenn Sie klassische Ressourcen nur über das Azure-Portal verwalten, müssen Sie den klassischen Administrator nicht verwenden.

Weitere Informationen finden Sie unter [Azure Resource Manager und klassische Bereitstellung](../../azure-resource-manager/management/deployment-models.md) und [Azure classic subscription administrators](../../role-based-access-control/classic-administrators.md) (Klassische Azure-Abonnementadministratoren).

## <a name="determine-account-billing-administrator"></a>Ermitteln des Abrechnungsadministrators

<a name="whoisaa"></a>

Der Abrechnungsadministrator ist die Person, die über Berechtigungen zum Verwalten der Abrechnung für ein Konto verfügt. Sie ist berechtigt, im [Azure-Portal](https://portal.azure.com) auf die Abrechnung zuzugreifen und verschiedene Abrechnungsaufgaben durchzuführen (Abonnements erstellen, Rechnungen anzeigen und bezahlen, Zahlungsmethoden aktualisieren und Ähnliches).

Um Konten zu identifizieren, für die Sie der Abrechnungsadministrator sind, besuchen Sie die Seite [„Kostenverwaltung + Abrechnung“ im Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview). Wählen Sie dann im linken Bereich die Option **Alle Abrechnungsbereiche** aus. Auf der Seite „Abonnements“ werden alle Abonnements aufgelistet, für die Sie als Abrechnungsadministrator fungieren.

Wenn Sie sich nicht sicher sind, wer der Kontoadministrator für ein Abonnement ist, besuchen Sie die [Seite „Abonnements“ im Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Wählen Sie dann das zu überprüfende Abonnement aus, und sehen Sie unter **Einstellungen** nach. Wählen Sie **Eigenschaften** aus, und der Kontoadministrator des Abonnements wird im Feld **Kontoadministrator** angezeigt.


## <a name="assign-a-subscription-administrator"></a>Zuweisen eines Abonnementadministrators

<a name="add-an-admin-for-a-subscription"></a>

Um einen Benutzer zum Administrator eines Azure-Abonnements zu machen, weist ihm ein vorhandener Rechnungsadministrator die Rolle [Owner](../../role-based-access-control/built-in-roles.md#owner) (eine Azure-Rolle) im Abonnementbereich zu. Durch die Rolle „Besitzer“ erhält der Benutzer vollständigen Zugriff auf alle Ressourcen im Abonnement, einschließlich des Rechts, den Zugriff an andere Personen zu delegieren. Diese Schritte sind identisch mit allen anderen Rollenzuweisungen.

Wenn Sie nicht sicher sind, wer der Administrator für die Rechnungsstellung für ein Abonnement ist, führen Sie die folgenden Schritte aus, um dies herauszufinden.

1. Öffnen Sie die [Seite „Abonnements“ im Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Wählen Sie das zu überprüfende Abonnement aus, und sehen Sie unter **Einstellungen** nach.
1. Wählen Sie **Eigenschaften** aus. Der Kontoverwalter des Abonnements wird im Feld **Kontoverwalter** angezeigt.

### <a name="to-assign-a-user-as-an-administrator"></a>Zuweisen eines Benutzers als Administrator

- Weisen Sie einem Benutzer im Abonnementbereich die Rolle „Besitzer“ zu.  
     Ausführliche Informationen finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="need-help-contact-support"></a>Sie brauchen Hilfe? Support kontaktieren

[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls Sie weitere Hilfe benötigen, um das Problem schnell beheben zu lassen.

## <a name="next-steps"></a>Nächste Schritte

* [Was ist die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)?](../../role-based-access-control/overview.md)
* [Grundlegendes zu den verschiedenen Rollen in Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Zuordnen oder Hinzufügen eines Azure-Abonnements zu Ihrem Azure Active Directory-Mandanten](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
* [Berechtigungen der Administratorrolle in Azure Active Directory](../../active-directory/roles/permissions-reference.md)