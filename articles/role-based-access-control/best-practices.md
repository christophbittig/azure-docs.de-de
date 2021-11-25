---
title: Bewährte Methoden für Azure RBAC
description: Bewährte Methoden für die Verwendung der rollenbasierten Zugriffssteuerung von Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 11/15/2021
ms.author: rolyon
ms.openlocfilehash: 947645848fd60a6d2864a1715ddc32424a683ce8
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524833"
---
# <a name="best-practices-for-azure-rbac"></a>Bewährte Methoden für Azure RBAC

In diesem Artikel werden einige bewährte Methoden für die Verwendung der rollenbasierten Zugriffssteuerung in Azure (Azure RBAC) beschrieben. Diese bewährten Methoden leiten sich aus unseren Erfahrungen mit Azure RBAC und den Erfahrungen von Kunden wie Ihnen ab.

## <a name="only-grant-the-access-users-need"></a>Nur den für Benutzer erforderlichen Zugriff gewähren

Mithilfe von Azure RBAC können Sie Aufgaben in Ihrem Team verteilen und Benutzern nur den Zugriff gewähren, den sie zur Ausführung ihrer Aufgaben benötigen. Anstatt allen uneingeschränkte Berechtigungen in Ihrem Azure-Abonnement oder Ihren Ressourcen zu gewähren, können Sie die Berechtigungen auf bestimmte Aktionen in einem bestimmten Bereich beschränken.

Bei der Planung Ihrer Strategie für die Zugriffssteuerung hat es sich bewährt, Benutzern die geringsten Rechte zum Ausführen ihrer Aufgaben zu erteilen. Vermeiden Sie es, umfangreichere Rollen in umfassenderen Bereichen zuzuweisen, auch wenn dies anfänglich bequemer erscheint. Wenn Sie benutzerdefinierte Rollen erstellen, schließen Sie nur die Berechtigungen ein, die Benutzer benötigen. Durch das Einschränken von Rollen und Bereichen begrenzen Sie die Ressourcen, die gefährdet sind, wenn der Sicherheitsprinzipal kompromittiert wird.

Das folgende Diagramm zeigt ein vorgeschlagenes Muster für die Verwendung von Azure RBAC.

![Azure RBAC und der Ansatz der geringsten Rechte](./media/best-practices/rbac-least-privilege.png)

Weitere Informationen zum Zuweisen von Rollen finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](role-assignments-portal.md).

## <a name="limit-the-number-of-subscription-owners"></a>Begrenzen der Anzahl von Abonnementbesitzern

Sie sollten höchstens 3 Abonnementbesitzer haben, um die Möglichkeit einer Sicherheitsverletzung durch einen kompromittierten Besitzer zu verringern. Diese Empfehlung kann in Microsoft Defender für Cloud überwacht werden. Weitere Identitäts- und Zugriffsempfehlungen in Defender für Cloud finden Sie unter [Sicherheitsempfehlungen – ein Referenzhandbuch](../security-center/recommendations-reference.md).

## <a name="use-azure-ad-privileged-identity-management"></a>Verwenden von Azure AD Privileged Identity Management

Um privilegierte Konten vor bösartigen Cyberangriffen zu schützen, können Sie mit Azure Active Directory Privileged Identity Management (PIM) die Dauer der Aktivierung von Berechtigungen verkürzen und sich mittels Berichten und Warnungen einen besseren Überblick über deren Verwendung verschaffen. PIM hilft Ihnen beim Schutz privilegierter Konten, indem es privilegierten Just-In-Time-Zugriff auf Azure AD- und Azure-Ressourcen gewährt. Der Zugriff kann zeitgebunden sein, sodass Berechtigungen nach dessen Ablauf automatisch entzogen werden. 

Weitere Informationen finden Sie unter [Was ist Azure AD Privileged Identity Management?](../active-directory/privileged-identity-management/pim-configure.md).

## <a name="assign-roles-to-groups-not-users"></a>Zuweisen von Rollen zu Gruppen,nicht zu Benutzern

Damit Rollenzuweisungen besser verwaltbar sind, sollten Sie Benutzern keine Rollen direkt zuweisen. Weisen Sie Rollen stattdessen Gruppen zu. Das Zuweisen von Rollen zu Gruppen anstelle von Benutzern trägt auch dazu bei, die Anzahl von Rollenzuweisungen zu minimieren, die auf [eine bestimmte Anzahl von Rollenzuweisungen pro Abonnement](troubleshooting.md#azure-role-assignments-limit) beschränkt sind.

## <a name="assign-roles-using-the-unique-role-id-instead-of-the-role-name"></a>Zuweisen von Rollen mithilfe der eindeutigen Rollen-ID anstelle des Rollennamens

In bestimmten Fällen kann sich ein Rollenname ändern, z. B.:

- Sie verwenden eine eigene benutzerdefinierte Rolle und beschließen, den Namen zu ändern.
- Sie verwenden eine als Vorschauversion verfügbare Rolle, deren Name den Zusatz **(Vorschauversion)** enthält. Beim Veröffentlichen wird die Rolle umbenannt.

Auch wenn eine Rolle umbenannt wird, ändert sich die Rollen-ID nicht. Wenn Sie Ihre Rollenzuweisungen mithilfe von Skripts oder mittels Automatisierung erstellen, wird empfohlen, die eindeutige Rollen-ID anstelle des Rollennamens zu verwenden. Wird eine Rolle umbenannt, ist auf diese Weise die Wahrscheinlichkeit größer, dass Ihre Skripts funktionieren.

Weitere Informationen finden Sie unter [Zuweisen einer Rolle mithilfe der eindeutigen Rollen-ID und Azure PowerShell](role-assignments-powershell.md#assign-a-role-for-a-user-using-the-unique-role-id-at-a-resource-group-scope) und [Zuweisen einer Rolle mithilfe der eindeutigen Rollen-ID und Azure CLI](role-assignments-cli.md#assign-a-role-for-a-user-using-the-unique-role-id-at-a-resource-group-scope).

## <a name="next-steps"></a>Nächste Schritte

- [Behandeln von Problemen bei Azure RBAC](troubleshooting.md)
