---
title: 'API-Konzepte in Privileged Identity Management (PIM): Azure AD | Microsoft-Dokumentation'
description: Informationen zum Verstehen der APIs von Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: how-to
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ac89554135277e0656392a7035b34aac307008b7
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129668540"
---
# <a name="understand-the-privileged-identity-management-apis"></a>Verstehen der Privileged Identity Management-APIs

Tasks zum Privileged Identity Management (PIM) können Sie mithilfe der Rollen für Microsoft Graph-APIs für Azure Active Directory (Azure AD) und Azure Resource Manager-API für Azure (auch Azure RBAC-Rollen genannt). Dieser Artikel beschreibt wichtige Konzepte für die Verwendung der APIs für Privileged Identity Management.

Informationen zu Anforderungen und weitere Details zu PIM-APIs finden Sie unter:

- [API-Referenz: PIM für Azure AD-Rollen](/graph/api/resources/unifiedroleeligibilityschedulerequest?view=graph-rest-beta&preserve-view=true)
- [Referenz zu PIM für Azure-Ressourcen Rollen-API](/rest/api/authorization/roleeligibilityschedulerequests)

> [!IMPORTANT]
> PIM-APIs [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="pim-api-history"></a>PIM-API-Verlauf

Im Laufe der Jahre gab es diverse Iterationen der PIM-API. Sie werden einige Überlappungen in der Funktionalität feststellen, aber sie stellen keinen linearen Fortschritt der Versionen dar.

### <a name="iteration-1--only-supports-azure-ad-roles-deprecating"></a>Iteration 1: unterstützt nur Azure AD Rollen, veraltet

Unter dem Endpunkt „/beta/privilegedRoles“ hatte Microsoft eine klassische Version der PIM-API, die in den meisten Mandanten nicht mehr unterstützt wird. Wir sind dabei, den verbleibenden Zugriff auf diese API am 31. Mai als veraltet zu verwenden.

### <a name="iteration-2--supports-azure-ad-roles-and-azure-resource-roles"></a>Iteration 2: unterstützt Azure AD-Rollen und Azure-Ressourcenrollen

Unter dem Endpunkt „/beta/privilegedAccess“ hat Microsoft sowohl „/aadRoles“ als auch „/azureResources“ unterstützt. Dieser Endpunkt ist weiterhin in Ihrem Mandanten verfügbar, Microsoft empfiehlt jedoch, keine neue Entwicklung mit dieser API zu starten. Diese Beta-API wird nie als allgemein verfügbar veröffentlicht und letztendlich veraltet werden.

### <a name="current-iteration--azure-ad-roles-in-microsoft-graph-and-azure-resource-roles-in-azure-resource-manager"></a>Aktuelle Iteration: Azure AD-Rollen in Microsoft Graph und Azure-Ressourcenrollen in Azure Resource Manager

In der Betaphase hat Microsoft die letzte Iteration der PIM-API, bevor wir die API allgemein verfügbar machen. Basierend auf Kundenfeedback befindet sich die Azure AD PIM-API jetzt unter dem „unifiedRoleManagement“-API-Satz, und die Azure Resource PIM-API befindet sich jetzt unter der Azure Resource Manager-Rollenzuweisungs-API. Diese Standorte bieten auch einige zusätzliche Vorteile, z. B.:

- Ausrichtung der PIM-API für die reguläre Rollenzuweisungs-API sowohl für Azure AD- als auch für Azure-Ressourcenrollen.
- Reduzieren der Notwendigkeit des Aufrufens einer zusätzlichen PIM-API für das Onboarding einer Ressource, zum Abrufen einer Ressource oder zum Abrufen einer Rollendefinition.
- Unterstützung ausschließlicher App-Berechtigungen.
- Neue Features wie Genehmigungs- und E-Mail-Benachrichtigungskonfiguration.

In der aktuellen Iteration gibt es keine API-Unterstützung für PIM-Warnungen und Gruppen mit privilegiertem Zugriff.

## <a name="current-permissions-required"></a>Aktuelle Berechtigungen erforderlich

### <a name="azure-ad-roles"></a>Azure AD-Rollen

  Um die PIM-Graph-API für Azure AD-Rollen aufzurufen, benötigen Sie mindestens eine der folgenden Berechtigungen:

- RoleManagement.ReadWrite.Directory
- RoleManagement.Read.Directory

  Die erforderlichen Berechtigungen werden am einfachsten über das Azure AD-Zustimmungsframework angegeben.

### <a name="azure-resource-roles"></a>Azure-Ressourcenrollen

  Die PIM-API für Azure-Ressourcenrollen wird auf Grundlage des Azure Resource Manager-Frameworks entwickelt. Sie müssen der Azure-Ressourcenverwaltung zustimmen, benötigen jedoch keine Graph-API-Berechtigung. Außerdem müssen Sie sicherstellen, dass der Benutzer oder der Dienstprinzipal, der die API aufruft, mindestens über die Rolle „Besitzer“ oder „Benutzerzugriffsadministrator“ für die Ressource verfügt, die Sie verwalten möchten.

## <a name="calling-pim-api-with-an-app-only-token"></a>Aufrufen der PIM-API mit einem reinen App-Token

### <a name="azure-ad-roles"></a>Azure AD-Rollen

  Die PIM-API unterstützt jetzt neben delegierten Berechtigungen nur ausschließliche App-Berechtigungen.

- Bei „Nur App“-Berechtigungen müssen Sie die API mit einer Anwendung aufrufen, der bereits mit den erforderlichen Azure AD-Berechtigungen oder Azure-Rollenberechtigungen zugestimmt wurde.
- Für delegierte Berechtigungen müssen Sie die PIM-API sowohl mit einem Benutzer- als auch mit einem Anwendungstoken aufrufen. Der Benutzer muss entweder der Rolle „Globaler Administrator“ oder „Administrator für privilegierte Rollen“ zugewiesen sein und sicherstellen, dass der Dienstprinzipal, der die API aufruft, mindestens über die Rolle „Besitzer“ oder „Benutzerzugriffsadministrator“ für die Ressource verfügt, die Sie verwalten möchten.

### <a name="azure-resource-roles"></a>Azure-Ressourcenrollen

  Die PIM-API für Azure-Ressourcen unterstützt ausschließliche Benutzer- bzw. Anwendungs-Aufrufe. Stellen Sie einfach sicher, dass der Dienstprinzipal entweder über die Rolle „Besitzer“ oder „Benutzerzugriffsadministrator“ für die Ressource verfügt.

## <a name="design-of-current-api-iteration"></a>Entwurf der aktuellen API-Iteration

Die PIM-API besteht aus zwei Kategorien, die sowohl für die API für Azure AD-Rollen als auch für Azure-Ressourcenrollen konsistent sind: API-Anforderungen für Zuweisung und Aktivierung sowie Richtlinieneinstellungen.

### <a name="assignment-and-activation-api"></a>Zuweisungs- und Aktivierungs-API

Um berechtigte Zuweisungen, zeitgebundene berechtigte bzw. aktive Zuweisungen und Zuweisungen generell zu aktivieren, stellt PIM die folgenden Entitäten zur Verfügung:

- RoleAssignmentSchedule
- RoleEligibilitySchedule
- RoleAssignmentScheduleInstance
- RoleEligibilityScheduleInstance
- RoleAssignmentScheduleRequest
- RoleEligibilityScheduleRequest

Diese Entitäten funktionieren zusammen mit bereits vorhandenen „roleDefinition“- und „roleAssignment“-Entitäten für Azure AD- und Azure-Rollen, damit Sie End-to-End-Szenarien erstellen können.

- Wenn Sie versuchen, eine permanente (aktive) Rollenzuweisung ohne Zeitplan (Start- oder Endzeit) zu erstellen oder abzurufen, sollten Sie diese PIM-Entitäten vermeiden und sich auf die Lese-/Schreibvorgänge unter der Entität „roleAssignment“ konzentrieren.

- Um eine berechtigte Zuweisung mit oder ohne Ablaufzeit zu erstellen, können Sie den Schreibvorgang für „roleEligibilityScheduleRequest“ verwenden.

- Um eine permanente (aktive) Zuweisung mit einem Zeitplan (Start- oder Endzeit) zu erstellen, können Sie den Schreibvorgang für „roleAssignmentScheduleRequest“ verwenden.  

- Um eine berechtigte Zuweisung zu aktivieren, sollten Sie auch den Schreibvorgang für „roleAssignmentScheduleRequest“ mit einem geänderten Aktionsparameter namens „selfActivate“ verwenden.

Jedes Anforderungsobjekt würde entweder ein „roleAssignmentSchedule“- oder ein „roleEligibilitySchedule“-Objekt erstellen. Diese Objekte sind schreibgeschützt und zeigen einen Zeitplan aller aktuellen und zukünftigen Zuweisungen an.

Wenn eine berechtigte Zuweisung aktiviert wird, ist „roleEligibilityScheduleInstance“ weiterhin vorhanden. „RoleAssignmentScheduleRequest“ für die Aktivierung würde für diese aktivierte Dauer eine separate „roleAssignmentSchedule“ und „roleAssignmentScheduleInstance“ erstellen.

Die Instanzobjekte sind die tatsächlichen Zuweisungen, die derzeit vorhanden sind – unabhängig davon, ob es sich um eine berechtigte oder eine aktive Zuweisung handelt. Sie sollten den GET-Vorgang für die Instanzentität verwenden, um eine Liste der berechtigten (aktiven) Zuweisungen für eine Rolle bzw. einen Benutzer abzurufen.

### <a name="policy-setting-api"></a>Richtlinieneinstellungs-API

Um die Einstellung zu verwalten, stellen wir die folgenden Entitäten bereit:

- roleManagementPolicy
- roleManagementPolicyAssignment

Die *Rollenverwaltungsrichtlinie* definiert die Einstellung der Regel. Beispielsweise, ob MFA bzw. Genehmigung erforderlich ist, ob und an wen die E-Mail-Benachrichtigungen gesendet werden sollen oder ob dauerhafte Zuweisungen zulässig sind oder nicht. Die *Richtlinienzuweisung* fügt die Richtlinie an eine bestimmte Rolle an.

Verwenden Sie diese API, um eine Liste aller Entitäten des Typs „roleManagementPolicyAssignments“ abzurufen, sie nach der „roleDefinitionID“ zu filtern, die Sie ändern möchten, und dann die Richtlinie zu aktualisieren, die der Richtlinienzuweisung („policyAssignment“) zugeordnet ist.

## <a name="relationship-between-pim-entities-and-role-assignment-entities"></a>Beziehung zwischen PIM-Entitäten und Rollenzuweisungsentitäten

Der einzige Link zwischen der PIM-Entität und der Rollenzuweisungsentität für die permanente (aktive) Zuweisung für Azure AD- oder Azure-Rollen ist „roleAssignmentScheduleInstance“. Es gibt eine 1:1-Zuordnung zwischen den beiden Entitäten. Diese Zuordnung bedeutet, dass sowohl „roleAssignment“ als auch „roleAssignmentScheduleInstance“ Folgendes umfassen:  

- Persistente (aktive) Zuweisungen außerhalb von PIM
- Persistente (aktive) Zuweisungen mit einem Zeitplan innerhalb von PIM
- Aktivierte berechtigte Zuweisungen

## <a name="next-steps"></a>Nächste Schritte

- [Referenz zur Azure AD Privileged Identity Management-API](/graph/api/resources/privilegedidentitymanagement-root?view=graph-rest-beta&preserve-view=true)