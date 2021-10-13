---
title: Durchführen einer Zugriffsüberprüfung für Azure-Ressourcenrollen und Azure AD-Rollen in PIM – Azure AD | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den Zugriff auf Azure-Ressourcenrollen und Azure AD-Rollen in Azure AD Privileged Identity Management (PIM) überprüfen.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 31a2d1b9e15f795da4931ffbff88f5222aad69c2
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669585"
---
# <a name="perform-an-access-review-of-azure-resource-and-azure-ad-roles-in-pim"></a>Durchführen einer Zugriffsüberprüfung für Azure-Ressourcenrollen und Azure AD-Rollen in PIM

Privileged Identity Management (PIM) vereinfacht die Art und Weise, in der Unternehmen den privilegierten Zugriff auf Ressourcen in Azure Active Directory (Azure AD) und anderen Microsoft-Onlinediensten wie Microsoft 365 oder Microsoft Intune verwalten. Führen Sie die Schritte in diesem Artikel aus, um Überprüfungen für den Zugriff auf Rollen durchzuführen.

Wenn Ihnen eine Administratorrolle zugewiesen wurde, werden Sie vom Administrator für privilegierte Rollen Ihrer Organisation möglicherweise gebeten, regelmäßig zu bestätigen, dass Sie diese Rolle für Ihre Aufgaben benötigen. Sie erhalten möglicherweise eine E-Mail mit einem Link, oder Sie können direkt zum [Azure-Portal](https://portal.azure.com) navigieren und mit der Arbeit beginnen.

Wenn Sie Administrator für privilegierte Rollen oder globaler Administrator sind und sich für Zugriffsüberprüfungen interessieren, erhalten Sie weitere Informationen unter [Starten einer Zugriffsüberprüfung](pim-create-azure-ad-roles-and-resource-roles-review.md).

## <a name="approve-or-deny-access"></a>Genehmigen oder Verweigern des Zugriffs

Sie können den Zugriff basierend darauf genehmigen oder verweigern, ob der Benutzer weiterhin Zugriff auf die Rolle benötigt. Wählen Sie **Genehmigen** aus, wenn der Benutzer in der Rolle bleiben soll, oder **Verweigern**, wenn der Benutzer den Zugriff nicht mehr benötigt. Der Zuweisungsstatus der Benutzer ändert sich erst, wenn die Überprüfung geschlossen wird und der Administrator die Ergebnisse anwendet. Häufige Szenarien, in denen auf bestimmte abgelehnte Benutzer keine Ergebnisse angewendet werden können, können Folgendes umfassen:

- **Überprüfen von Mitgliedern einer synchronisierten lokalen Windows-AD-Gruppe**: Wenn die Gruppe von einem lokalen Windows AD synchronisiert wird, kann die Gruppe nicht in Azure AD verwaltet werden, und daher kann die Mitgliedschaft nicht geändert werden.
- **Überprüfen einer Rolle mit zugewiesenen verschachtelten Gruppen**: Für Benutzer, die über eine Mitgliedschaft durch eine verschachtelte Gruppe verfügen, wird durch die Zugriffsüberprüfung die Mitgliedschaft in der verschachtelten Gruppe nicht entfernt, und daher behalten sie den Zugriff auf die Rolle, die überprüft wird.
- **Benutzer nicht gefunden oder andere Fehler**: Diese können auch dazu führen, dass ein Anwenden des Ergebnisses nicht unterstützt wird.

Gehen Sie wie folgt vor, um die Zugriffsüberprüfung zu finden und abzuschließen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Wählen Sie **Azure Active Directory** aus, und öffnen Sie **Privileged Identity Management**.
1. Wählen Sie **Zugriff überprüfen** aus. Wenn Zugriffsüberprüfungen ausstehen, werden diese auf der Seite der Zugriffsüberprüfungen angezeigt.

    :::image type="content" source="media/pim-perform-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-complete.png" alt-text="Screenshot der Privileged Identity Management-Anwendung mit ausgewähltem Blatt „Zugriff überprüfen“ für Azure AD-Rollen" lightbox="media/pim-perform-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-complete.png":::

1. Wählen Sie die Überprüfung aus, die Sie abschließen möchten.
1. Wählen Sie **Genehmigen** oder **Ablehnen** aus. Geben Sie im Feld **Geben Sie eine Grund an** nach Bedarf eine geschäftliche Begründung für Ihre Entscheidung ein.

    :::image type="content" source="media/pim-perform-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-completed.png" alt-text="Screenshot der Privileged Identity Management-Anwendung mit der ausgewählten Zugriffsüberprüfung für Azure AD-Rollen." lightbox="media/pim-perform-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-completed.png":::

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen einer Zugriffsüberprüfung für Azure-Ressourcenrollen und Azure AD-Rollen in PIM](pim-create-azure-ad-roles-and-resource-roles-review.md)
- [Abschließen einer Zugriffsüberprüfung für Azure-Ressourcenrollen und Azure AD-Rollen in PIM](pim-complete-azure-ad-roles-and-resource-roles-review.md)
