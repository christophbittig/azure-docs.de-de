---
title: Dokumente zu Administratorrollen für Microsoft 365-Dienste – Azure AD | Microsoft-Dokumentation
description: Suchen von Inhalten und API-Referenzen für Administratorrollen für Microsoft 365-Dienste in Azure Active Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b3fa2fbceba39ee6044c252703236c85545e46db
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132349165"
---
# <a name="roles-for-microsoft-365-services-in-azure-active-directory"></a>Rollen für Microsoft 365-Dienste in Azure Active Directory

Alle Produkte in Microsoft 365 können mit Administratorrollen in Azure Active Directory (Azure AD) verwaltet werden. Einige Produkte bieten darüber hinaus zusätzliche Rollen, die für dieses Produkt spezifisch sind. Informationen zu den von den einzelnen Produkten unterstützten Rollen finden Sie in der folgenden Tabelle. Richtlinien zur Planung der Rollensicherheit finden Sie unter [Schützen des privilegierten Zugriffs für hybride und Cloudbereitstellungen in Azure AD](security-planning.md).

## <a name="where-to-find-content"></a>Ort

> [!div class="mx-tableFixed"]
> | Microsoft 365-Dienst | Rolleninhalt | API-Inhalt |
> | ---------------------- | ------------------ | ----------------- |
> | Administratorrollen in Office 365- und Microsoft 365-Businessplänen | [Microsoft 365-Administratorrollen](/office365/admin/add-users/about-admin-roles) | Nicht verfügbar |
> | Azure Active Directory (Azure AD) und Azure AD Identity Protection| [Integrierte Rollen in Azure AD](permissions-reference.md) | [Graph-API](/graph/api/overview)<br>[Abrufen von Rollenzuweisungen](/graph/api/directoryrole-list) |
> | Exchange Online| [Rollenbasierte Zugriffssteuerung in Exchange](/exchange/understanding-role-based-access-control-exchange-2013-help) |  [PowerShell für Exchange](/powershell/module/exchange/role-based-access-control/add-managementroleentry)<br>[Abrufen von Rollenzuweisungen](/powershell/module/exchange/role-based-access-control/get-rolegroup) |
> | SharePoint Online | [Integrierte Rollen in Azure AD](permissions-reference.md)<br>Auch [Informationen zur SharePoint-Administratorrolle in Microsoft 365](/sharepoint/sharepoint-admin-role) | [Graph-API](/graph/api/overview)<br>[Abrufen von Rollenzuweisungen](/graph/api/directoryrole-list) |
> | Teams/Skype for Business | [Integrierte Rollen in Azure AD](permissions-reference.md) | [Graph-API](/graph/api/overview)<br>[Abrufen von Rollenzuweisungen](/graph/api/directoryrole-list) |
> | Security & Compliance Center (Office 365 Advanced Threat Protection, Schutz für Exchange Online, Information Protection) | [Office 365-Administratorrollen](/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Exchange PowerShell](/powershell/module/exchange/role-based-access-control/add-managementroleentry)<br>[Abrufen von Rollenzuweisungen](/powershell/module/exchange/role-based-access-control/get-rolegroup) |
> | Sicherheitsbewertung | [Integrierte Rollen in Azure AD](permissions-reference.md) | [Graph-API](/graph/api/overview)<br>[Abrufen von Rollenzuweisungen](/graph/api/directoryrole-list) |
> | Compliance-Manager | [Compliance-Manager-Rollen](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Nicht verfügbar |
> | Azure Information Protection | [Integrierte Rollen in Azure AD](permissions-reference.md) | [Graph-API](/graph/api/overview)<br>[Abrufen von Rollenzuweisungen](/graph/api/directoryrole-list) |
> | Microsoft Defender für Cloud-Apps | [Rollenbasierte Zugriffssteuerung](/cloud-app-security/manage-admins) | [API-Referenz](/cloud-app-security/api-tokens)  |
> | Azure Advanced Threat Protection | [Azure ATP-Rollengruppen](/azure-advanced-threat-protection/atp-role-groups) | Nicht verfügbar |
> | Windows Defender Advanced Threat Protection | [Rollenbasierte Zugriffssteuerung von Windows Defender ATP](/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Nicht verfügbar |
> | Privileged Identity Management | [Integrierte Rollen in Azure AD](permissions-reference.md) | [Graph-API](/graph/api/overview)<br>[Abrufen von Rollenzuweisungen](/graph/api/directoryrole-list) |
> | Intune | [Rollenbasierte Zugriffssteuerung von Intune](/intune/role-based-access-control) | [Graph-API](/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta&preserve-view=true)<br>[Abrufen von Rollenzuweisungen](/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta&preserve-view=true) |
> | Managed Desktop | [Integrierte Rollen in Azure AD](permissions-reference.md) | [Graph-API](/graph/api/overview)<br>[Abrufen von Rollenzuweisungen](/graph/api/directoryrole-list) |

## <a name="next-steps"></a>Nächste Schritte

* [Anzeigen und Zuweisen von Azure AD-Administratorrollen](manage-roles-portal.md)
* [Integrierte Rollen in Azure AD](permissions-reference.md)
