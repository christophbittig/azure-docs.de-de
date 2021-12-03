---
title: Verwenden von Azure AD-Gruppen zum Verwalten von Rollenzuweisungen – Azure Active Directory
description: Verwenden Sie Azure AD Gruppen, um die Verwaltung der Rollenzuweisung in Azure Active Directory zu vereinfachen.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 09/28/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3826c7a2cc50e90b42ff1a4f0047dc5a6ca1cf6
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520729"
---
# <a name="use-azure-ad-groups-to-manage-role-assignments"></a>Verwenden von Azure AD-Gruppen zum Verwalten von Rollenzuweisungen

Azure Active Directory (Azure AD) bietet die Möglichkeit, Azure AD-Gruppen als Ziel für Rollenzuweisungen zu verwenden. Das Zuweisen von Rollen zu Gruppen kann die Verwaltung von Rollenzuweisungen in Azure AD mit minimalem Aufwand durch Ihre globalen Administratoren und Administratoren für privilegierte Rollen vereinfachen.

## <a name="why-assign-roles-to-groups"></a>Gründe für das Zuweisen von Rollen zu Gruppen

Betrachten Sie das folgende Beispiel: Contoso hat Personal in verschiedenen geografischen Regionen eingestellt, um Kennwörter für Mitarbeiter in seiner Azure AD-Organisation zu verwalten und zurückzusetzen. Anstatt einen Administrator für privilegierte Rollen oder einen globalen Administrator zu bitten, jeder Person einzeln die Rolle „Helpdeskadministrator“ zuzuweisen, kann eine Gruppe „Contoso_Helpdesk_Administrators“ erstellt und die Rolle der Gruppe zugewiesen werden. Wenn Benutzer der Gruppe beitreten, wird ihnen die Rolle indirekt zugewiesen. Der vorhandene Governanceworkflow kann dann den Genehmigungsprozess und die Überwachung der Gruppenmitgliedschaft übernehmen, um sicherzustellen, dass nur berechtigte Benutzer Mitglieder der Gruppe sind und somit der Rolle „Helpdeskadministrator“ zugewiesen werden.

## <a name="how-role-assignments-to-groups-work"></a>Funktionsweise von Rollenzuweisungen für Gruppen

Um einer Gruppe eine Rolle zuzuweisen, müssen Sie eine neue Sicherheits- oder Microsoft 365-Gruppe erstellen, wobei die `isAssignableToRole`-Eigenschaft auf `true` festgelegt wird. Legen Sie im Azure-Portal die Option **Azure AD-Rollen können der Gruppe zugewiesen werden** auf **Ja** fest. In beiden Fällen können Sie anschließend eine oder mehrere Azure AD-Rollen der Gruppe genauso zuweisen, wie Sie auch Benutzern Rollen zuweisen.

![Screenshot der Seite „Rollen und Administratoren“](./media/groups-concept/role-assignable-group.png)

## <a name="restrictions-for-role-assignable-groups"></a>Einschränkungen für Gruppen, denen Rollen zugewiesen werden können

Für Gruppen, denen Rollen zugewiesen werden können, gelten die folgenden Einschränkungen:

- Sie können nur die `isAssignableToRole`-Eigenschaft oder die Option **Azure AD-Rollen können der Gruppe zugewiesen werden** für neue Gruppen festlegen.
- Die `isAssignableToRole`-Eigenschaft ist **unveränderlich**. Sobald eine Gruppe mit dieser Eigenschaft erstellt wurde, kann diese nicht mehr geändert werden.
- Sie können eine vorhandene Gruppe nicht zu einer Gruppe machen, der Rollen zugewiesen werden können.
- In einer einzigen Azure AD-Organisation (Mandant) können maximal 400 Gruppen erstellt werden, denen Rollen zugewiesen werden können.

## <a name="how-are-role-assignable-groups-protected"></a>Wie werden Gruppen, denen Rollen zugewiesen werden können, geschützt?

Wenn eine Gruppe einer Rolle zugewiesen wird, kann jeder IT-Administrator, der die Gruppenmitgliedschaft verwalten kann, indirekt auch die Mitgliedschaft in dieser Rolle verwalten. Angenommen, eine Gruppe namens „Contoso_User_Administrators“ wird der Rolle „Benutzeradministrator“ zugewiesen. Ein Exchange-Administrator, der die Gruppenmitgliedschaft ändern kann, kann sich selbst der Gruppe „Contoso_User_Administrators“ hinzufügen und auf diese Weise zu einem Benutzerkontoadministrator werden. Wie Sie sehen, kann ein Administrator seine Berechtigung auf nicht erwünschte Weise erweitern.

Nur Gruppen, für die bei der Erstellung die `isAssignableToRole`-Eigenschaft auf `true` festgelegt wurde, kann eine Rolle zugewiesen werden. Diese Eigenschaft ist unveränderlich. Sobald eine Gruppe mit dieser Eigenschaft erstellt wurde, kann diese nicht mehr geändert werden. Sie können die Eigenschaft nicht für eine vorhandene Gruppe festlegen.

Gruppen, denen Rollen zugewiesen werden können, sollen potenzielle Sicherheitsverletzungen verhindern. Daher gelten die folgenden Einschränkungen:

- Nur globale Administratoren und Administratoren für privilegierte Rollen können eine Gruppe erstellen, der Rollen zugewiesen werden können.
- Der Typ der Mitgliedschaft für Gruppen, denen Rollen zugewiesen werden können, muss „Zugewiesen“ sein und darf keine dynamische Azure AD-Gruppe sein. Das automatisierte Auffüllen dynamischer Gruppen kann dazu führen, dass der Gruppe ein unerwünschtes Konto hinzugefügt und sie somit der Rolle zugewiesen wird.
- Standardmäßig können nur globale Administratoren und Administratoren für privilegierte Rollen die Mitgliedschaft in einer Gruppe mit Rollenzuweisung verwalten, Sie können jedoch die Verwaltung von Gruppen mit Rollenzuweisung delegieren, indem Sie Gruppenbesitzer hinzufügen.
- Um die Mitgliedschaft dieser Gruppen verwalten zu können, ist die Microsoft Graph-Berechtigung „RoleManagement.ReadWrite.All“ erforderlich. „Group.ReadWrite.All“ funktioniert nicht.
- Um eine Rechteerweiterung zu verhindern, können die Anmeldeinformationen oder die mehrstufige Authentifizierung von Mitgliedern und Besitzern einer Gruppe mit Rollenzuweisung nur von einem privilegierten Authentifizierungsadministrator oder einem globalen Administrator geändert werden.
- Eine Schachtelung von Gruppen wird nicht unterstützt. Eine Gruppe kann nicht als Mitglied einer Gruppe mit Rollenzuweisung hinzugefügt werden.

## <a name="use-pim-to-make-a-group-eligible-for-a-role-assignment"></a>Verwenden von PIM, um eine Gruppe für eine Rollenzuweisung zu berechtigen

Wenn Sie nicht möchten, dass Mitglieder der Gruppe dauerhaften Zugriff auf eine Rolle haben, können Sie [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) verwenden, um eine Gruppe für eine Rollenzuweisung zu berechtigen. Jedes Mitglied der Gruppe ist dann berechtigt, die Rollenzuweisung für einen festen Zeitraum zu aktivieren.

> [!NOTE]
> Für Gruppen mit berechtigtem Zugriff, die für die Erhöhung von Azure AD-Rollen verwendet werden, empfiehlt es sich, einen Genehmigungsprozess für berechtigte Mitgliedszuweisungen vorauszusetzen. Zuweisungen, die ohne Genehmigung aktiviert werden können, können ein Sicherheitsrisiko für Administratoren mit einer niedrigeren Berechtigungsebene darstellen. Beispielsweise verfügt der Helpdeskadministrator über die Berechtigung zum Zurücksetzen des Kennworts eines berechtigten Benutzers.

## <a name="scenarios-not-supported"></a>Nicht unterstützte Szenarien

Folgende Szenarios werden nicht unterstützt:  

- Zuweisen von Azure AD-Rollen (integriert oder benutzerdefiniert) zu lokalen Gruppen

## <a name="known-issues"></a>Bekannte Probleme

Bei Gruppen, denen Rollen zugewiesen werden können, sind folgende Probleme bekannt:

- *Nur Kunden mit Azure AD P2-Lizenz*: Auch nach dem Löschen der Gruppe wird sie noch immer als ein berechtigtes Mitglied der Rolle auf der PIM-Benutzeroberfläche angezeigt. Funktionell gibt es kein Problem. Es ist lediglich ein Cacheproblem im Azure-Portal.  
- Verwenden Sie das neue [Exchange Admin Center](https://admin.exchange.microsoft.com/) zum Zuweisen von Rollen über Gruppenmitgliedschaften. Das alte Exchange Admin Center unterstützt dieses Feature noch nicht. Exchange PowerShell-Cmdlets funktionieren den Erwartungen entsprechend.
- Das Azure Information Protection-Portal (klassisches Portal) erkennt die Rollenmitgliedschaft über eine Gruppe noch nicht. Sie können [zur Plattform für einheitliche Vertraulichkeitsbezeichnungen migrieren](/azure/information-protection/configure-policy-migrate-labels) und dann das Office 365 Security & Compliance Center verwenden, um Gruppenzuweisungen für das Verwalten von Rollen zu nutzen.
- [Apps Admin Center](https://config.office.com/) unterstützt dieses Feature noch nicht. Weisen Sie Benutzer direkt der Rolle als Office Apps-Administrator zu.

## <a name="license-requirements"></a>Lizenzanforderungen

Für die Verwendung dieses Features ist eine Azure AD Premium P1-Lizenz erforderlich. Um auch Privileged Identity Management für die Just-In-Time-Rollenaktivierung zu verwenden, benötigen Sie eine Azure AD Premium P2-Lizenz. Um die richtige Lizenz für Ihre Anforderungen zu ermitteln, lesen Sie [Vergleich der allgemein verfügbaren Features der Editionen Free und Premium](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing).

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen einer Gruppe, der Rollen zugeordnet werden können](groups-create-eligible.md)
- [Zuweisen von Azure AD-Rollen zu Gruppen](groups-assign-role.md)
