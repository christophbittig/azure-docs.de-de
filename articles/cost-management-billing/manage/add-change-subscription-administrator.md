---
title: Hinzufügen oder Ändern von Azure-Abonnementadministratoren
description: Beschreibt das Hinzufügen oder Ändern eines Azure-Abonnementadministrators mithilfe der rollenbasierten Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC).
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 06/27/2021
ms.author: banders
ms.openlocfilehash: b4bd2615dd99145bd75fd7c1095056961d3b2d80
ms.sourcegitcommit: 1c12bbaba1842214c6578d914fa758f521d7d485
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/28/2021
ms.locfileid: "112988353"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Hinzufügen oder Ändern von Azure-Abonnementadministratoren


Für die Verwaltung von Azure-Ressourcen müssen Sie über die entsprechende Administratorrolle verfügen. Azure bietet ein als [rollenbasierte Zugriffssteuerung](../../role-based-access-control/overview.md) (Azure Role-Based Access Control, Azure RBAC) bezeichnetes Autorisierungssystem mit verschiedenen integrierten Rollen, unter denen Sie wählen können. Sie können diesen Rollen verschiedene Gültigkeitsbereiche zuweisen, wie etwa Verwaltungsgruppe, Abonnement oder Ressourcengruppe. Standardmäßig kann die Person, die ein neues Azure-Abonnement erstellt, anderen Benutzern Administratorzugriff auf ein Abonnement gewähren.

In diesem Artikel wird beschrieben, wie die Administratorrolle für einen Benutzer mithilfe von Azure RBAC auf Abonnementebene hinzugefügt oder geändert werden kann.

Microsoft empfiehlt das Verwalten des Zugriffs auf Ressourcen mithilfe der Azure RBAC. Wenn Sie aber weiterhin das klassische Bereitstellungsmodell verwenden und die klassischen Ressourcen mit dem [PowerShell-Modul für die Azure-Dienstverwaltung](/powershell/module/servicemanagement/azure.service) verwalten, müssen Sie einen klassischen Administrator verwenden.

> [!TIP]
> Wenn Sie klassische Ressourcen nur über das Azure-Portal verwalten, müssen Sie den klassischen Administrator nicht verwenden.

Weitere Informationen finden Sie unter [Azure Resource Manager und klassische Bereitstellung](../../azure-resource-manager/management/deployment-models.md) und [Azure classic subscription administrators](../../role-based-access-control/classic-administrators.md) (Klassische Azure-Abonnementadministratoren).

<a name="add-an-admin-for-a-subscription"></a>

## <a name="assign-a-subscription-administrator"></a>Zuweisen eines Abonnementadministrators

Wenn ein Administrator einen Benutzer zum Administrator für ein Azure-Abonnement machen möchte, weist er ihm die Rolle [Besitzer](../../role-based-access-control/built-in-roles.md#owner) (eine Azure-Rolle) im Abonnementbereich zu. Durch die Rolle „Besitzer“ erhält der Benutzer vollständigen Zugriff auf alle Ressourcen im Abonnement, einschließlich des Rechts, den Zugriff an andere Personen zu delegieren. Diese Schritte sind identisch mit allen anderen Rollenzuweisungen.

Verwenden Sie die folgenden Schritte für die Ermittlung, wenn Sie nicht sicher sind, wer der Kontoadministrator für ein Abonnement ist.

1. Öffnen Sie die [Seite „Abonnements“ im Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Wählen Sie das zu überprüfende Abonnement aus, und sehen Sie unter **Einstellungen** nach.
1. Wählen Sie **Eigenschaften** aus. Der Kontoadministrator des Abonnements wird im Feld **Kontoadministrator** angezeigt.

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