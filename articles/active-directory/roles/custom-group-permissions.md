---
title: Gruppenverwaltungsberechtigungen für benutzerdefinierte Azure AD-Rollen (Vorschau) – Azure Active Directory
description: Gruppenverwaltungsberechtigungen für benutzerdefinierte Azure AD-Rollen (Vorschau) im Azure-Portal, in PowerShell oder in der Microsoft Graph-API.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 10/26/2021
ms.author: rolyon
ms.reviewer: ''
ms.custom: it-pro
ms.openlocfilehash: 5e768d9661a7030fb75d8089a6a49e9d5022d612
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057208"
---
# <a name="group-management-permissions-for-azure-ad-custom-roles-preview"></a>Gruppenverwaltungsberechtigungen für benutzerdefinierte Azure AD-Rollen (Vorschau)

> [!IMPORTANT]
> Gruppenverwaltungsberechtigungen für benutzerdefinierte Azure AD-Rollen befinden sich derzeit in der VORSCHAU.
> Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten rechtliche Bedingungen. Sie gelten für diejenigen Azure-Features, die sich in der Beta- oder Vorschauversion befinden oder aber anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

Gruppenverwaltungsberechtigungen können in benutzerdefinierten Rollendefinitionen in Azure Active Directory (Azure AD) verwendet werden, um einen differenzierten Zugriff wie den folgenden zu gewähren:

- Verwalten von Gruppeneigenschaften wie Name und Beschreibung
- Verwalten von Mitgliedern und Besitzern
- Erstellen oder Löschen von Gruppen
- Lesen von Überwachungsprotokollen
- Verwalten eines bestimmten Gruppentyps

In diesem Artikel werden die Berechtigungen aufgeführt, die Sie in Ihren benutzerdefinierten Rollen für verschiedene Gruppenverwaltungsszenarien verwenden können. Informationen zum Erstellen benutzerdefinierter Rollen finden Sie unter [Erstellen und Zuweisen einer benutzerdefinierten Rolle](custom-create.md).

> [!NOTE]
> Das Zuweisen von benutzerdefinierten Rollen in einem Gruppenbereich mithilfe des Azure-Portal ist derzeit **nur** für Azure AD Premium P1 verfügbar.

## <a name="how-to-interpret-group-management-permissions"></a>Interpretieren von Gruppenverwaltungsberechtigungen

Für die Interpretation der Gruppenverwaltungsberechtigungen ist es hilfreich, die Bedeutung der verschiedenen Untertypen von Berechtigungen zu verstehen.

> [!div class="mx-tableFixed"]
> | Berechtigungsuntertyp | Beschreibung des Berechtigungsuntertyps |
> | --- | --- |
> | groups | Verwalten von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | groups.unified | Verwalten von Microsoft 365-Gruppen mit dem Mitgliedschaftstyp „dynamisch“ und „zugewiesen“ (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | groups.unified.assignedMembership | Verwalten von Microsoft 365-Gruppen ausschließlich für den Mitgliedschaftstyp „zugewiesen“ (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | groups.security | Verwalten von Sicherheitsgruppen mit dem Mitgliedschaftstyp „dynamisch“ und „zugewiesen“ (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | groups.security.assignedMembership | Verwalten von Sicherheitsgruppen ausschließlich für den Mitgliedschaftstyp „zugewiesen“ (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |

Die folgende Tabelle enthält Beispielberechtigungen zum Aktualisieren von Gruppenmitgliedern unterschiedlicher Untertypen. 

> [!div class="mx-tableFixed"]
> | Berechtigungsbeispiel | Berechtigungsbeschreibung |
> | --- | --- |
> | microsoft.directory/**groups**/members/update | Aktualisieren der Mitglieder von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/**groups.unified**/members/update | Aktualisieren der Mitglieder von Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/**groups.unified.assignedMembership**/members/update | Aktualisieren der Mitglieder von Microsoft 365-Gruppen mit dem Mitgliedschaftstyp „zugewiesen“ (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/**groups.security**/members/update | Aktualisieren der Mitglieder von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/**groups.security.assignedMembership**/members/update | Aktualisieren der Mitglieder von Sicherheitsgruppen mit dem Mitgliedschaftstyp „zugewiesen“ (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |

## <a name="read-group-information"></a>Lesen von Gruppeninformationen

Die folgenden Berechtigungen stehen zum Lesen von Eigenschaften, Mitgliedern und Besitzern von Gruppen zur Verfügung.

> [!div class="mx-tableFixed"]
> | Berechtigung | BESCHREIBUNG |
> | ---------- | ----------- |
> | microsoft.directory/groups/allProperties/read | Lesen aller Eigenschaften (einschließlich privilegierter Eigenschaften) von Sicherheits- und Microsoft 365-Gruppen (einschließlich Gruppen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/standard/read | Lesen der Standardeigenschaften von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen eingeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/members/read | Lesen der Mitglieder von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen eingeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/memberOf/read | Lesen der memberOf-Eigenschaft von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen eingeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/owners/read | Lesen der Besitzer von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen eingeschlossen, denen Rollen zugewiesen werden können) |

## <a name="create-groups"></a>Gruppen erstellen

Die folgenden Berechtigungen stehen zum Erstellen von Gruppen verschiedener Typen zur Verfügung.

> [!div class="mx-tableFixed"]
> | Berechtigung | BESCHREIBUNG |
> | ---------- | ----------- |
> | microsoft.directory/groups/create | Erstellen von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.unified/create | Erstellen von Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.unified.assignedMembership/create | Erstellen von Microsoft 365-Gruppen mit dem Mitgliedschaftstyp „zugewiesen“ (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security/create | Erstellen von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security.assignedMembership/create | Erstellen von Sicherheitsgruppen mit dem Mitgliedschaftstyp „zugewiesen“ (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/createAsOwner | Erstellen von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können). Der Ersteller wird als erster Besitzer hinzugefügt. |
> | microsoft.directory/groups.unified/createAsOwner | Erstellen von Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können). Der Ersteller wird als erster Besitzer hinzugefügt. |
> | microsoft.directory/groups.unified.assignedMembership/createAsOwner | Erstellen von Microsoft 365-Gruppen mit dem Mitgliedschaftstyp „zugewiesen“ (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können). Der Ersteller wird als erster Besitzer hinzugefügt. |
> | microsoft.directory/groups.security/createAsOwner | Erstellen von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) Der Ersteller wird als erster Besitzer hinzugefügt. |
> | microsoft.directory/groups.security.assignedMembership/createAsOwner | Erstellen von Sicherheitsgruppen mit dem Mitgliedschaftstyp „zugewiesen“ (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können). Der Ersteller wird als erster Besitzer hinzugefügt. |

## <a name="update-group-information"></a>Aktualisieren von Gruppeninformationen

Die folgenden Berechtigungen stehen zum Aktualisieren von Eigenschaften und Mitgliedern von Gruppen zur Verfügung.

> [!div class="mx-tableFixed"]
> | Berechtigung | BESCHREIBUNG |
> | ---------- | ----------- |
> | microsoft.directory/groups/allProperties/update | Aktualisieren aller Eigenschaften (einschließlich privilegierter Eigenschaften) von Sicherheits- und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.unified/allProperties/update | Aktualisieren aller Eigenschaften (einschließlich privilegierter Eigenschaften) von Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.unified.assignedMembership/allProperties/update | Aktualisieren aller Eigenschaften (einschließlich privilegierter Eigenschaften) von Microsoft 365-Gruppen mit dem Mitgliedschaftstyp „zugewiesen“ (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security/allProperties/update | Aktualisieren aller Eigenschaften (einschließlich privilegierter Eigenschaften) von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security.assignedMembership/allProperties/update | Aktualisieren aller Eigenschaften (einschließlich privilegierter Eigenschaften) von Sicherheitsgruppen mit dem Mitgliedschaftstyp „zugewiesen“ (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/basic/update | Aktualisieren grundlegender Eigenschaften von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.unified/basic/update | Aktualisieren grundlegender Eigenschaften von Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.unified.assignedMembership/basic/update | Aktualisieren von grundlegenden Eigenschaften für Microsoft 365-Gruppen mit dem Mitgliedschaftstyp „zugewiesen“ (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security/basic/update | Aktualisieren grundlegender Eigenschaften von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security.assignedMembership/basic/update | Aktualisieren von grundlegenden Eigenschaften für Sicherheitsgruppen mit dem Mitgliedschaftstyp „zugewiesen“ (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/classification/update | Aktualisieren der Klassifizierungseigenschaft von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.unified/classification/update | Aktualisieren der Klassifizierungseigenschaft von Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.unified.assignedMembership/classification/update | Aktualisieren der Klassifizierungseigenschaft von Microsoft 365-Gruppen mit dem Mitgliedschaftstyp „zugewiesen“ (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security/classification/update | Aktualisieren der classification-Eigenschaft von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security.assignedMembership/classification/update | Aktualisieren der Klassifizierungseigenschaft von Sicherheitsgruppen mit dem Mitgliedschaftstyp „zugewiesen“ (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/dynamicMembershipRule/update | Aktualisieren der Regel für eine dynamische Mitgliedschaft für Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.unified/dynamicMembershipRule/update | Aktualisieren der Regel für die dynamische Mitgliedschaft von Sicherheits- und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security/dynamicMembershipRule/update | Aktualisieren der Regel für die dynamische Mitgliedschaft von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/members/update | Aktualisieren der Mitglieder von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.unified/members/update | Aktualisieren der Mitglieder von Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.unified.assignedMembership/members/update | Aktualisieren der Mitglieder von Microsoft 365-Gruppen mit dem Mitgliedschaftstyp „zugewiesen“ (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security/members/update | Aktualisieren der Mitglieder von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security.assignedMembership/members/update | Aktualisieren der Mitglieder von Sicherheitsgruppen mit dem Mitgliedschaftstyp „zugewiesen“ (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |

## <a name="update-members-of-different-group-types"></a>Aktualisieren der Mitglieder verschiedener Gruppentypen

Die folgenden Berechtigungen stehen für die Aktualisierung von Mitgliedern verschiedener Gruppentypen zur Verfügung.

> [!div class="mx-tableFixed"]
> | Berechtigung | BESCHREIBUNG |
> | ---------- | ----------- |
> | microsoft.directory/groups/members/update | Aktualisieren der Mitglieder von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.unified/members/update | Aktualisieren der Mitglieder von Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.unified.assignedMembership/members/update | Aktualisieren der Mitglieder von Microsoft 365-Gruppen mit dem Mitgliedschaftstyp „zugewiesen“ (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security/members/update | Aktualisieren der Mitglieder von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security.assignedMembership/members/update | Aktualisieren der Mitglieder von Sicherheitsgruppen mit dem Mitgliedschaftstyp „zugewiesen“ (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
 
## <a name="delete-groups"></a>Löschen von Gruppen

Die folgenden Berechtigungen stehen zum Löschen von Gruppen zur Verfügung.

> [!div class="mx-tableFixed"]
> | Berechtigung | BESCHREIBUNG |
> | ---------- | ----------- |
> | microsoft.directory/groups/delete | Löschen von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.unified/members/update | Aktualisieren der Mitglieder von Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.unified.assignedMembership/members/update | Aktualisieren der Mitglieder von Microsoft 365-Gruppen mit dem Mitgliedschaftstyp „zugewiesen“ (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security/members/update | Aktualisieren der Mitglieder von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security.assignedMembership/members/update | Aktualisieren der Mitglieder von Sicherheitsgruppen mit dem Mitgliedschaftstyp „zugewiesen“ (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen und Zuweisen einer benutzerdefinierten Rolle in Azure Active Directory](custom-create.md)
- [Auflisten von Azure AD-Rollenzuweisungen](view-assignments.md)
