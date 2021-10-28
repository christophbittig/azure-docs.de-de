---
title: Rollen, die nicht in Privileged Identity Management verwaltet werden können – Azure Active Directory | Microsoft-Dokumentation
description: In diesem Artikel werden die Rollen beschrieben, die Sie nicht in Azure AD Privileged Identity Management (PIM) verwalten können.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ae4f269bbd34a0f8f863799c24f592f4fc20196
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130253551"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Rollen, die nicht in Privileged Identity Management verwaltet werden können

Mit Azure Active Directory (Azure AD) Privileged Identity Management (PIM) können Sie alle [Azure AD-Rollen](../roles/permissions-reference.md) und alle [Azure-Rollen](../../role-based-access-control/built-in-roles.md) verwalten. Zu diesen Azure-Rollen gehören auch benutzerdefinierten Rollen, die an Ihre Verwaltungsgruppen, Abonnements, Ressourcengruppen und Ressourcen angefügt sind. Es gibt jedoch einige Rollen, die Sie nicht verwalten können. In diesem Artikel werden die Rollen beschrieben, die Sie nicht in Privileged Identity Management verwalten können.

## <a name="classic-subscription-administrator-roles"></a>Administrator für klassisches Abonnement

Die folgenden Administratorrollen klassischer Abonnements können Sie nicht in Privileged Identity Management verwalten:

- Kontoadministrator
- Dienstadministrator
- Co-Administrator

Weitere Informationen zu Administratorrollen klassischer Abonnements finden Sie unter [Administratorrollen für klassische Abonnements, Azure-Rollen und Azure AD-Administratorrollen](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-microsoft-365-admin-roles"></a>Wie sieht es mit Microsoft 365-Administratorrollen aus?

Es werden alle Microsoft 365-Rollen in der Azure AD-Rollenumgebung und im Administratorportal unterstützt, z. B. „Exchange-Administrator“ und „SharePoint-Administrator“, aber es werden keine spezifischen Rollen innerhalb der rollenbasierten Zugriffssteuerung (RBAC) von Exchange oder SharePoint unterstützt. Weitere Informationen zu diesen Microsoft 365-Diensten finden Sie unter [Microsoft 365-Administratorrollen](/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> - Bei berechtigten Benutzern für die Rolle „SharePoint-Administrator“, die Rolle „Geräteadministrator“ sowie allen Rollen, die versuchen, auf das Microsoft Security and Compliance Center zuzugreifen, können nach dem Aktivieren ihrer Rolle Verzögerungen von bis zu einigen Stunden auftreten. Wir arbeiten mit diesen Teams zusammen daran, diese Probleme zu beheben.
> - Informationen zu Verzögerungen beim Aktivieren der Rolle „Lokaler Administrator für in Azure AD eingebundenes Gerät“ finden Sie unter [Verwalten der lokalen Administratorgruppe auf in Azure AD eingebundenen Geräten](../devices/assign-local-admin.md#manage-the-device-administrator-role).

## <a name="next-steps"></a>Nächste Schritte

- [Zuweisen von Azure AD-Rollen in PIM](pim-how-to-add-role-to-user.md)
- [Zuweisen von Azure-Ressourcenrollen in PIM](pim-resource-roles-assign-roles.md)