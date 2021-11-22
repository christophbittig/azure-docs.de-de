---
title: Ermitteln des aktuellen Status externer Zusammenarbeit mit Azure Active Directory
description: Hier lernen Sie Methoden kennen, mit denen Sie den aktuellen Status Ihrer Zusammenarbeit ermitteln können.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f3874b4be6cd9f84a98a8e218f5faa983499ab5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132300925"
---
# <a name="discover-the-current-state-of-external-collaboration-in-your-organization"></a>Ermitteln des aktuellen Status der Zusammenarbeit mit externen Benutzern in Ihrer Organisation

Vor dem Ermitteln des aktuellen Zustands der Zusammenarbeit mit externen Benutzern sollten Sie [den gewünschten Sicherheitsstatus](1-secure-access-posture.md) ermitteln. Sie berücksichtigen die Anforderungen Ihres Unternehmens an zentralisierte und delegierte Steuerung sowie relevante Ziele in Bezug auf Governance, gesetzliche Bestimmungen und Compliance.

Personen in Ihrer Organisation arbeiten wahrscheinlich bereits mit Benutzern aus anderen Organisationen zusammen. Die Zusammenarbeit kann über Features in Produktivitätsanwendungen (z. B. Microsoft 365), per E-Mail oder mithilfe sonstiger Ressourcen für die Zusammenarbeit mit externen Benutzern erfolgen. Die Säulen Ihres Governanceplans werden deutlich, wenn Sie Folgendes entdecken:

* Benutzer die Zusammenarbeit mit externen Benutzern initiieren.
* Die externen Benutzer und Organisationen, mit denen Sie zusammenarbeiten.
* Den für externe Benutzer gewährten Zugriff.

## <a name="users-initiating-external-collaboration"></a>Benutzer, die Zusammenarbeit mit externen Benutzern initiieren

Die Benutzer, die die Zusammenarbeit mit externen Benutzern initiieren, wissen am besten, welche Anwendungen für diese Zusammenarbeit am relevantesten sind und welche Zugriffsberechtigungen gewährt werden sollten. Wenn Sie sich in die Lage dieser Benutzer versetzen, finden Sie schnell heraus, wer über delegierte Berechtigungen zum Einladen externer Benutzer, Erstellen von Zugriffspaketen und Durchführen von Zugriffsüberprüfungen verfügen sollte.

Überprüfen Sie das [Microsoft 365-Überwachungsprotokoll für Freigabe- und Zugriffsanforderungsaktivitäten](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance#sharing-and-access-request-activities), um nach Benutzern zu suchen, die derzeit mit anderen zusammenarbeiten. Sie können sich auch das [Azure AD-Überwachungsprotokoll ansehen, um herauszufinden, wer B2B-Benutzer](../external-identities/auditing-and-reporting.md) in Ihr Verzeichnis eingeladen hat.

## <a name="find-current-collaboration-partners"></a>Suchen nach aktuellen Kollaborationspartnern

Externe Benutzer sind möglicherweise [Azure AD B2B-Benutzer](../external-identities/what-is-b2b.md) (bevorzugt) mit von Partnern verwalteten Anmeldeinformationen oder externe Benutzer mit lokal bereitgestellten Anmeldeinformationen. Diese Benutzer sind in der Regel (aber nicht immer) mit dem UserType „Gast“ gekennzeichnet. Gastbenutzer können über die [Microsoft Graph-API](/graph/api/user-list?tabs=http), [PowerShell](/graph/api/user-list?tabs=http) oder das [Azure-Portal](../enterprise-users/users-bulk-download.md) aufgelistet werden.

### <a name="use-email-domains-and-companyname-property"></a>Verwenden von E-Mail-Domänen und der companyName-Eigenschaft

Externe Organisationen können durch die Domänennamen der E-Mail-Adressen externer Benutzer bestimmt werden. Wenn Consumeridentitätsanbieter wie Google unterstützt werden, ist dies unter Umständen nicht möglich. In diesem Fall empfiehlt es sich, das companyName-Attribut zu schreiben, um die externe Organisation des Benutzers eindeutig zu identifizieren.

### <a name="use-allow-or-deny-lists"></a>Verwenden von Zulassungs- oder Verweigerungslisten

Unter Umständen soll in Ihrer Organisation nur die Zusammenarbeit mit bestimmten Organisationen erlaubt sein. Es kann auch sein, dass die Zusammenarbeit mit bestimmten Organisationen blockiert werden soll. Auf der Mandantenebene befindet sich eine [Zulassungs- oder Verweigerungsliste](../external-identities/allow-deny-list.md), mit der alle B2B-Einladungen und Einlösungen unabhängig von der Quelle (z. B, Microsoft Teams, Microsoft SharePoint, oder das Azure-Portal) kontrolliert werden können.

Bei Verwendung der Berechtigungsverwaltung können Sie Zugriffspakete auch auf einen Teil Ihrer Partner begrenzen, indem Sie wie unten gezeigt die Einstellung „Bestimmte verbundene Organisationen“ verwenden.

![Screenshot: Zulassungsliste oder Verweigerungsliste beim Erstellen eines neuen Zugriffspakets.](media/secure-external-access/2-new-access-package.png)

## <a name="find-access-being-granted-to-external-users"></a>Ermitteln der Zugriffsberechtigungen, die externen Benutzern gewährt werden

Wenn Sie mit mehreren externen Benutzern und Organisationen zusammenarbeiten, können Sie die Zugriffsberechtigungen für diese Benutzer mithilfe der Microsoft Graph-API festlegen, um [Azure AD-Gruppenmitgliedschaften](/graph/api/resources/groups-overview) oder[direkte Anwendungszuweisungen](/graph/api/resources/approleassignment) in Azure AD zu verwalten.

### <a name="enumerate-application-specific-permissions"></a>Aufzählen anwendungsspezifischer Berechtigungen

Möglicherweise können Sie auch anwendungsspezifische Berechtigungsaufzählungen durchführen. Sie können beispielsweise mithilfe [dieses Skripts](https://gallery.technet.microsoft.com/office/SharePoint-Online-c9ec4f64) programmgesteuert einen Berechtigungsbericht für SharePoint Online erstellen.

Untersuchen Sie insbesondere den Zugriff auf alle geschäfts- und unternehmenskritischen Apps, damit Sie über jeglichen externen Zugriff vollständig Bescheid wissen.

### <a name="detect-ad-hoc-sharing"></a>Erkennen von Ad-hoc-Freigaben

Wenn Ihre E-Mail-Infrastruktur und Ihre Netzwerkpläne es zulassen, können Sie per E-Mail oder über nicht autorisierte SaaS-Apps (Software-as-a-Service) freigegebene Inhalte untersuchen. Die [Microsoft 365-Mechanismen zur Verhinderung von Datenverlust (Data Loss Protection, DLP)](/microsoft-365/compliance/data-loss-prevention-policies) unterstützen Sie beim Identifizieren, Verhindern und Überwachen von versehentlichen Freigaben vertraulicher Informationen in Ihrer gesamten Microsoft 365-Infrastruktur. Mit [Defender für Cloud-Apps](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/cloud-app-security) können Sie die Verwendung nicht autorisierter SaaS-Apps in Ihrer Umgebung identifizieren.

## <a name="next-steps"></a>Nächste Schritte

In den folgenden Artikeln finden Sie Informationen zum Sichern des externen Zugriffs auf Ressourcen. Es wird empfohlen, die Aktionen in der angegebenen Reihenfolge auszuführen.

1. [Ermitteln des Sicherheitsstatus für externen Zugriff](1-secure-access-posture.md)

2. [Ermitteln des aktuellen Status](2-secure-access-current-state.md) (in diesem Artikel erläuterter Schritt)

3. [Erstellen eines Governanceplans für den externen Zugriff](3-secure-access-plan.md)

4. [Verwenden von Gruppen für die Sicherheit](4-secure-access-groups.md)

5. [Durchführen der Umstellung auf Azure AD B2B](5-secure-access-b2b.md)

6. [Schützen des Zugriffs mit der Berechtigungsverwaltung](6-secure-access-entitlement-managment.md)

7. [Schützen des Zugriffs mit Richtlinien für bedingten Zugriff](7-secure-access-conditional-access.md)

8. [Schützen des Zugriffs mit Vertraulichkeitsbezeichnungen](8-secure-access-sensitivity-labels.md)

9. [Schützen des Zugriffs auf Microsoft Teams, OneDrive und SharePoint](9-secure-access-teams-sharepoint.md)
