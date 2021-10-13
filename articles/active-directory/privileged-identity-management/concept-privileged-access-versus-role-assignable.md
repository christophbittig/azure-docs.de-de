---
title: Worin besteht der Unterschied zwischen Gruppen mit privilegiertem Zugriff und Gruppen, denen Rollen zugewiesen werden können – Azure AD | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den Unterschied zwischen Gruppen mit privilegiertem Zugriff und Gruppen, denen Rollen zugewiesen werden können, in Azure AD Privileged Identity Management (PIM) erkennen.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/10/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d903f3aa1e46005ad848a5f63139ce5fd17c8c5
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129668198"
---
# <a name="whats-the-difference-between-privileged-access-groups-and-role-assignable-groups"></a>Worin besteht der Unterschied zwischen Gruppen mit privilegiertem Zugriff und Gruppen, denen Rollen zugewiesen werden können?

Privileged Identity Management (PIM) unterstützt die Möglichkeit, privilegierten Zugriff für Gruppen zu ermöglichen, denen Rollen zugewiesen werden können. Da eine verfügbare Gruppe, der Rollen zugewiesen werden können, jedoch eine Voraussetzung für das Erstellen einer Gruppe mit privilegiertem Zugriff ist, werden in diesem Artikel die Unterschiede und deren Nutzung erläutert.

## <a name="what-are-azure-ad-role-assignable-groups"></a>Was sind Azure AD-Gruppen, denen Rollen zugewiesen werden können?

Azure AD ermöglicht Ihnen, einer Azure AD-Rolle eine Azure AD-Sicherheitsgruppe in der Cloud zuzuweisen. Globale Administratoren und Administratoren für privilegierte Rollen müssen eine neue Sicherheitsgruppe erstellen und die Gruppen zum Zeitpunkt der Erstellung als Gruppe festlegen, der Rollen zugewiesen werden können. Nur Benutzer mit den Rollen „Globaler Administrator“, „Administrator für privilegierte Rollen“ oder „Besitzer“ der Gruppe können die Mitgliedschaft in der Gruppe ändern. Außerdem können keine anderen Benutzer das Kennwort der Benutzer zurücksetzen, die Mitglieder der Gruppe sind. Dieses Feature hilft zu verhindern, dass Administratoren auf eine Rolle mit höheren Berechtigungen hochgestuft werden, ohne ein Anforderungs- und Genehmigungsverfahren zu durchlaufen.

## <a name="what-are-privileged-access-groups"></a>Was sind Gruppen mit privilegiertem Zugriff?

In Gruppen mit privilegiertem Zugriff können Benutzer auf die Rolle „Besitzer“ oder „Mitglied“ einer Azure AD-Sicherheitsgruppe heraufgestuft werden. Mit diesem Feature können Sie Just-In-Time-Workflows nicht nur für Azure AD- und Azure-Rollen in Batches einrichten, sondern auch Just-In-Time-Szenarien für andere Anwendungsfälle wie Azure SQL, Azure Key Vault, Intune oder andere Anwendungsrollen. Weitere Informationen finden Sie unter [Verwaltungsfunktionen für Gruppen mit privilegiertem Zugriff](groups-features.md).

>[!Note]
>Für Gruppen mit berechtigtem Zugriff, die für die Erhöhung Azure AD Rollen verwendet werden, empfiehlt Microsoft, dass Sie einen Genehmigungsprozess für berechtigte Mitgliedszuweisungen benötigen. Zuweisungen, die ohne Genehmigung aktiviert werden können, können Sie anfällig für ein Sicherheitsrisiko durch Administratoren mit geringeren Privilegien machen. Beispielsweise verfügt der Helpdeskadministrator über die Berechtigung zum Zurücksetzen der Kennwörter eines berechtigten Benutzers.

## <a name="when-to-use-each-type-of-group"></a>Verwenden der einzelnen Gruppentypen

Sie können Just-in-Time-Zugriff für Berechtigungen und Rollen einrichten, die über Azure AD und Azure-Ressourcen hinausgehen. Wenn Sie über andere Ressourcen verfügen, deren Autorisierung mit einer Azure AD-Sicherheitsgruppe verbunden werden kann (für Azure Key Vault, Intune, Azure SQL oder andere Apps und Dienste), sollten Sie den privilegierten Zugriff für die Gruppe aktivieren und Benutzern die Berechtigung für die Mitgliedschaft in der Gruppe zuweisen.

Wenn Sie einer Azure AD- oder Azure-Ressourcenrolle eine Gruppe zuweisen möchten und eine Rechteerweiterung über einen PIM-Prozess erfordern, gibt es zwei Möglichkeiten:

- **Weisen Sie der Rolle die Gruppe dauerhaft zu**. Anschließend gewähren Sie Benutzern berechtigten Mitgliedszugriff auf die Gruppe in PIM. Berechtigte Benutzer müssen dann ihre Mitgliedschaft aktivieren, um in die Gruppe zu kommen, die der Rolle dauerhaft zugewiesen ist. Für diesen Pfad muss eine Gruppe, der Rollen zugewiesen werden können, in PIM als Gruppe mit privilegiertem Zugriff für die Azure AD-Rolle aktiviert werden.
- **Weisen Sie die Gruppe über PIM als „Berechtigt“ für eine Rolle zu**. Alle Benutzer in der Gruppe müssen ihre Zuweisung aktivieren, um Zugriff auf die Rolle zu erhalten. Dieser Pfad erfordert eine Gruppe, der Rollen zugewiesen werden können, für Azure AD-Rolle und eine Sicherheitsgruppe für Azure-Ressourcen.

    ![Diagramm, das zwei Möglichkeiten zum Zuweisen von Rollen mithilfe von Gruppen mit privilegiertem Zugriff in PIM zeigt.](./media/concept-privileged-access-versus-role-assignable/concept-privileged-access.png)

Eine dieser Methoden kann für das End-to-End-Szenario verwendet werden. In den meisten Fällen wird die erste Methode empfohlen. Sie sollten die zweite Methode nur verwenden, wenn Sie Folgendes versuchen:

- Weisen Sie eine Gruppe zu mehreren Azure AD- oder Azure-Ressourcenrollen zu, und lassen Sie Benutzer eine einmalige Registrierung ausführen, um Zugriff auf mehrere Rollen zu erhalten.
- Verwalten Sie unterschiedliche Aktivierungsrichtlinien für verschiedene Gruppen von Benutzern, um auf eine Azure AD- oder Azure-Ressourcenrolle zuzugreifen. Wenn Sie beispielsweise möchten, dass einige Benutzer genehmigt werden, bevor sie die Rolle „Globaler Administrator erhalten, während Sie für andere Benutzer die automatische Genehmigung zulassen, können Sie zwei Gruppen mit privilegiertem Zugriff einrichten, beide dauerhaft (eine „permanente“ Zuweisung in Privileged Identity Management) der Rolle „Globaler Administrator“ zuweisen und dann unterschiedliche Aktivierungsrichtlinien für die Mitgliedsrolle für jede Gruppe verwenden.

## <a name="next-steps"></a>Nächste Schritte

- [Genehmigen oder Ablehnen von Anforderungen für Azure AD-Rollen](azure-ad-pim-approval-workflow.md)
- [Genehmigen oder Ablehnen von Anforderungen für Azure-Ressourcenrollen](pim-resource-roles-approval-workflow.md)
- [Genehmigen von Aktivierungsanforderungen für Mitglieder und Besitzer privilegierter Zugriffsgruppen (Vorschau)](groups-approval-workflow.md)
