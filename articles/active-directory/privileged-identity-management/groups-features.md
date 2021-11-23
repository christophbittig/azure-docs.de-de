---
title: Verwalten von Gruppen mit privilegiertem Zugriff in Privileged Identity Management (PIM) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Mitglieder und Besitzer privilegierter Zugriffsgruppen in Privileged Identity Management (PIM) verwalten.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/07/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: d8a36c1837273fc3fa173994e2ec3b3465ed4cb6
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520254"
---
# <a name="management-capabilities-for-privileged-access-groups-preview"></a>Verwaltungsfunktionen für Gruppen mit privilegiertem Zugriff (Vorschau)

In Privileged Identity Management (PIM) kann nun die Berechtigung zur Mitgliedschaft in privilegierten Zugriffsgruppen oder zum Besitz privilegierter Zugriffsgruppen zugewiesen werden. Ab dieser Vorschauversion können Sie Cloudgruppen integrierte Azure AD-Rollen (Azure Active Directory) zuweisen und die Berechtigung und Aktivierung von Gruppenmitgliedern und -besitzern mithilfe von PIM verwalten. Weitere Informationen zu Gruppen, die in Azure AD Rollen zugewiesen werden können, finden Sie unter [Verwenden von Azure AD-Gruppen zum Verwalten von Rollenzuweisungen](../roles/groups-concept.md).

>[!Important]
> Verwenden Sie zum Zuweisen einer Gruppe mit privilegiertem Zugriff zu einer Rolle für den Administratorzugriff auf Exchange, Security & Compliance Center oder SharePoint die Funktion **Rollen und Administratoren** im Azure AD-Portal und nicht die Funktion für Gruppen mit privilegiertem Zugriff, um den Benutzer oder die Gruppe als berechtigt für die Aktivierung in der Gruppe festzulegen.

> [!NOTE]
> Für Gruppen mit berechtigtem Zugriff, die für die Erhöhung von Azure AD-Rollen verwendet werden, empfiehlt es sich, einen Genehmigungsprozess für berechtigte Mitgliedszuweisungen vorauszusetzen. Zuweisungen, die ohne Genehmigung aktiviert werden können, können ein Sicherheitsrisiko für Administratoren mit einer niedrigeren Berechtigungsebene darstellen. Beispielsweise verfügt der Helpdeskadministrator über die Berechtigung zum Zurücksetzen des Kennworts eines berechtigten Benutzers.

## <a name="require-different-policies-for-each-role-assignable-group"></a>Verwenden unterschiedlicher Richtlinien für Gruppen, die Rollen zugewiesen werden können

Einige Organisationen verwenden Tools wie Azure AD B2B-Zusammenarbeit (Business-to-Business), um ihre Partner als Gäste in Ihre Azure AD-Organisation einzuladen. Anstelle einer einzelnen Just-In-Time-Richtlinie für alle Zuweisungen zu einer privilegierten Rolle können Sie zwei unterschiedliche privilegierte Zugriffsgruppen mit jeweils eigenen Richtlinien erstellen. So können Sie weniger strenge Anforderungen für Ihre vertrauenswürdigen Mitarbeiter und strengere Anforderungen wie etwa einen Genehmigungsworkflow für Ihre Partner erzwingen, wenn diese eine Aktivierung in der zugewiesenen Gruppe anfordern.

## <a name="activate-multiple-role-assignments-in-a-single-request"></a>Aktivieren mehrerer Rollenzuweisungen in einer einzelnen Anforderung

Mit der Vorschauversion privilegierter Zugriffsgruppen können Sie workloadspezifischen Administratoren mit einer einzelnen Just-In-Time-Anforderung schnell Zugriff auf mehrere Rollen gewähren. Ein Beispiel: Ihre Office-Administratoren der Ebene 0 benötigen ggf. täglich Just-In-Time-Zugriff auf die Rollen „Exchange-Administrator“, „Administrator für Office-Apps“, „Teams-Administrator“ und „Suchadministrator“, um Vorfälle sorgfältig untersuchen zu können. Früher war dies mit einem gewissen Zeitaufwand verbunden, da hierzu vier aufeinanderfolgende Anforderungen erforderlich waren. Nun können Sie stattdessen eine Gruppe namens „Office-Administratoren der Ebene 0“ erstellen, die Rollen zugewiesen werden kann. Diese Gruppe können Sie dann jeder der vier zuvor genannten Rollen (oder anderen integrierten Azure AD-Rollen) zuweisen und im Aktivitätsabschnitt der Gruppe für privilegierten Zugriff aktivieren. Nach der Aktivierung für privilegierten Zugriff können Sie die Just-In-Time-Einstellungen für Mitglieder der Gruppe konfigurieren und ihre Administratoren und Besitzer als berechtigt zuweisen. Wenn die Administratoren in die Gruppe aufsteigen, werden Sie zu Mitgliedern aller vier Azure AD-Rollen.

## <a name="extend-and-renew-group-assignments"></a>Verlängern und Erneuern von Gruppenzuweisungen

Nach der Einrichtung Ihrer zeitgebundenen Besitzer- oder Mitgliederzuweisungen fragen Sie sich vielleicht, was passiert, wenn eine Zuweisung abläuft. In dieser neuen Version bieten wir zwei Optionen für dieses Szenario:

- Verlängern: Wenn eine Rollenzuweisung demnächst abläuft, kann der Benutzer über Privileged Identity Management eine Verlängerung für die Rollenzuweisung anfordern.
- Erneuern: Wenn eine Rollenzuweisung bereits abgelaufen ist, kann der Benutzer über Privileged Identity Management eine Erneuerung für die Rollenzuweisung anfordern.

Diese beiden vom Benutzer initiierten Aktionen erfordern eine Genehmigung von einem globalen Administrator oder einem Administrator für privilegierte Rollen. Administratoren müssen sich also nicht mehr um die Verwaltung von ablaufenden Zuweisungen kümmern. Sie können einfach auf die Anforderungen zur Verlängerung oder Erneuerung warten und sie genehmigen, wenn die Anforderung berechtigt ist.

## <a name="next-steps"></a>Nächste Schritte

- [Zuweisen der Berechtigung für eine privilegierte Zugriffsgruppe (Vorschau) in Privileged Identity Management](groups-assign-member-owner.md)
- [Genehmigen von Aktivierungsanforderungen für Mitglieder und Besitzer privilegierter Zugriffsgruppen (Vorschau)](groups-approval-workflow.md)
