---
title: Identity Governance – Azure Active Directory | Microsoft-Dokumentation
description: Azure Active Directory Identity Governance ermöglicht Ihnen, den Sicherheitsbedarf Ihrer Organisation und die Produktivität von Mitarbeitern mit den richtigen Prozessen sowie Transparenz in Einklang zu bringen.
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 072cfcd66207d8c778c77f3ece4db49a30e8ca28
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124826856"
---
# <a name="what-is-azure-ad-identity-governance"></a>Was ist Azure AD Identity Governance?

Azure Active Directory (Azure AD) Identity Governance ermöglicht Ihnen, den Sicherheitsbedarf Ihrer Organisation und die Produktivität von Mitarbeitern mit den richtigen Prozessen sowie Transparenz in Einklang zu bringen. Bereitgestellt werden Funktionen, die den richtigen Zugriff der richtigen Personen auf die richtigen Ressourcen gewährleisten. Mit diesen und den zugehörigen Funktion von Azure AD und Enterprise Mobility + Security können Sie das Zugriffsrisiko minimieren, indem Sie kritische Ressourcen und den Zugriff darauf schützen und überwachen, gleichzeitig aber auch die Produktivität von Mitarbeitern und Geschäftspartnern sicherstellen.  

Mit Identity Governance können Organisationen für Mitarbeiter, Geschäftspartner und Anbieter als auch für Dienste und Anwendungen, sowohl lokal als auch in Clouds, die folgenden Aufgaben ausführen:

- Steuern des Identitätslebenszyklus
- Steuern des Zugriffslebenszyklus
- Sichern des privilegierten Zugriffs für die Verwaltung

Insbesondere soll Identity Governance Organisationen bei der Beantwortung der folgenden vier Schlüsselfragen unterstützen:

- Welche Benutzer sollen Zugriff auf welche Ressourcen haben?
- Wozu nutzen diese Benutzer den Zugriff?
- Gibt es effektive organisatorische Kontrollen zum Verwalten des Zugriffs?
- Können Prüfer feststellen, ob die Kontrollen funktionieren?

## <a name="identity-lifecycle"></a>Identitätslebenszyklus

Identity Governance hilft Organisationen, ein Gleichgewicht herzustellen zwischen der *Produktivität* – wie schnell eine Person auf die benötigten Ressourcen zugreifen kann (beispielsweise, wenn sie der Organisation beitritt) – und der *Sicherheit* – wie sich der Zugriff im Laufe der Zeit ändern sollte (beispielsweise aufgrund von Änderungen des Beschäftigungsstatus einer Person).  Identity Lifecycle Management ist die Grundlage für Identity Governance, und eine effektive Governance in großem Maßstab erfordert eine Modernisierung der Identity Lifecycle Management-Infrastruktur für Anwendungen.

![Identitätslebenszyklus](./media/identity-governance-overview/identity-lifecycle.png)

Bei vielen Organisationen ist der Identitätslebenszyklus für Mitarbeiter mit der Darstellung des Benutzers in einem Personalverwaltungssystem (Human Capital Management, HCM) verknüpft.  Bei Azure AD Premium werden Benutzeridentitäten für Personen, die in Workday und SuccessFactors enthalten sind, sowohl in Active Directory als auch in Azure Active Directory automatisch vorgehalten. Dies ist unter [Planen der HR-Cloudanwendung für die Azure Active Directory-Benutzerbereitstellung](../app-provisioning/plan-cloud-hr-provision.md) beschrieben.  In Azure AD Premium ist zudem [Microsoft Identity Manager](/microsoft-identity-manager/) enthalten. Dies ist eine Lösung, mit der Datensätze aus lokalen Personalverwaltungssystemen wie SAP HCM, Oracle eBusiness und Oracle PeopleSoft importiert werden können.

Immer mehr Szenarien erfordern heute die Kollaboration mit Personen außerhalb Ihrer Organisation. Mithilfe der [Azure AD B2B](/azure/active-directory/b2b/)-Kollaboration (Business-to-Business) können Sie die Anwendungen und Dienste Ihrer Organisation für Gastbenutzer und externe Partner von beliebigen Organisationen sicher freigeben und dabei die Kontrolle über Ihre eigenen Unternehmensdaten behalten.  Mit der [Azure AD-Berechtigungsverwaltung](entitlement-management-overview.md) können Sie auswählen, welche Benutzer der Organisation Zugriff anfordern dürfen und als B2B-Gäste dem Verzeichnis Ihrer Organisation hinzugefügt werden sollen. Außerdem können Sie sicherstellen, dass diese Gäste entfernt werden, wenn Sie keinen Zugriff mehr benötigen.

## <a name="access-lifecycle"></a>Zugriffslebenszyklus

Organisationen benötigen einen Prozess, um den Zugriff über die anfänglich beim Erstellen der Identität eines Benutzers zugewiesenen Rechte hinaus zu verwalten.  Zudem müssen Unternehmensorganisationen in der Lage sein, ihre Systeme effizient zu skalieren, damit sie Zugriffsrichtlinien und -kontrollen kontinuierlich entwickeln und erzwingen können.

![Zugriffslebenszyklus](./media/identity-governance-overview/access-lifecycle.png)

In der Regel delegiert die IT-Abteilung Entscheidungen bezüglich der Zugriffsgenehmigung an Entscheidungsträger im Unternehmen.  Darüber hinaus kann die IT die Benutzer selbst in den Prozess einbeziehen.  Beispielsweise müssen Benutzer, die in Europa auf vertrauliche Kundendaten in der Marketinganwendung eines Unternehmens zugreifen, die Richtlinien des Unternehmens kennen. Gastbenutzer kennen die Anforderungen für die Behandlung von Daten in einer Organisation, zu der sie eingeladen wurden, möglicherweise nicht.

Organisationen können den Zugriffslebenszyklus mit Technologien wie [dynamischen Gruppen](../enterprise-users/groups-dynamic-membership.md) in Verbindung mit der Benutzerbereitstellung in [SaaS-Apps](../saas-apps/tutorial-list.md) oder [in SCIM integrierten Apps](../app-provisioning/use-scim-to-provision-users-and-groups.md) automatisieren.  Außerdem können Organisationen steuern, welche [Gastbenutzer Zugriff auf lokale Anwendungen haben](../external-identities/hybrid-cloud-to-on-premises.md).  Diese Zugriffsrechte können dann regelmäßig mithilfe von wiederkehrenden [Azure AD-Zugriffsüberprüfungen](access-reviews-overview.md) überprüft werden.   Mit der [Azure AD-Berechtigungsverwaltung](entitlement-management-overview.md) können Sie außerdem definieren, wie Benutzer übergreifend über Pakete von Gruppen- und Teammitgliedschaften, Anwendungsrollen und SharePoint Online-Rollen Zugriff anfordern.

Wenn ein Benutzer auf Anwendungen zuzugreifen versucht, erzwingt Azure AD Richtlinien für [bedingten Zugriff](../conditional-access/index.yml). Richtlinien für bedingten Zugriff können beispielsweise beinhalten, dass [Nutzungsbedingungen](../conditional-access/terms-of-use.md) angezeigt werden und [der Benutzer diesen Bedingungen zustimmen muss](../conditional-access/require-tou.md), bevor er auf eine Anwendung zugreifen kann.

## <a name="privileged-access-lifecycle"></a>Privilegierter Zugriffslebenszyklus

In der Vergangenheit wurde der privilegierte Zugriff von anderen Anbietern als eine von Identity Governance getrennte Funktion dargestellt. Wir bei Microsoft denken jedoch, dass die Steuerung des privilegierten Zugriffs ein wichtiger Bestandteil von Identity Governance ist – insbesondere angesichts des mit diesen Administratorrechten einhergehenden möglichen Missbrauchs und Schadens für eine Organisation. Die Mitarbeiter, Anbieter und Auftragnehmer, denen Administratorrechte gewährt werden, müssen gesteuert werden.

![Privilegierter Zugriffslebenszyklus](./media/identity-governance-overview/privileged-access-lifecycle.png)

[Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) bietet zusätzliche Steuerungen für die Sicherung der Zugriffsrechte für Ressourcen in Azure AD, Azure und anderen Microsoft Online Services.  Zusätzlich zur mehrstufigen Authentifizierung und zum bedingten Zugriff stehen Ihnen mit den Funktionen für Just-In-Time-Zugriff und Warnungen für Rollenänderungen von Azure AD PIM umfassende Governancekontrollen zum Schützen der Ressourcen Ihres Unternehmens (Verzeichnis, Microsoft 365 und Azure-Ressourcenrollen) zur Verfügung. Wie bei anderen Formen des Zugriffs können Organisationen mithilfe von Zugriffsüberprüfungen eine periodische erneute Zertifizierung für alle Benutzer mit Administratorrollen konfigurieren.

## <a name="governance-capabilities-in-other-azure-ad-features"></a>Governancefunktionen anderer Azure AD-Features

Zusätzlich zu den obigen Features werden die folgenden weiteren Azure AD-Features häufig für Identity Governance-Szenarien genutzt:

| Funktion | Szenario |Feature
| ------- | --------------------- |-----|
|Identitätslebenszyklus (Mitarbeiter)|Administratoren können die Bereitstellung von Benutzerkonten aus Workday oder SuccessFactors über die HR-Cloudumgebung oder die lokale HR-Umgebung ermöglichen.|[Benutzerbereitstellung aus der HR-Cloudumgebung in Azure AD](../app-provisioning/plan-cloud-hr-provision.md):|
|Identitätslebenszyklus (Gäste)|Administratoren können das Self-Service-Onboarding für Gastbenutzer über einen anderen Azure AD-Mandanten, direkten Verbund, per Einmalkennung (One-Time Passcode, OTP) oder über Google-Konten ermöglichen.  Die Bereitstellung von Gastbenutzern wird gemäß den Lebenszyklusrichtlinien automatisch durchgeführt und aufgehoben.|[Berechtigungsverwaltung](entitlement-management-overview.md) per [B2B](../external-identities/what-is-b2b.md)|
|Berechtigungsverwaltung|Ressourcenbesitzer können Zugriffspakete erstellen, die Apps, Teams, Azure AD- und Microsoft 365-Gruppen sowie SharePoint Online-Websites enthalten.|[Berechtigungsverwaltung](entitlement-management-overview.md)|
|Zugriffsanforderungen|Endbenutzer können die Gruppenmitgliedschaft oder den Anwendungszugriff anfordern. Endbenutzer, einschließlich der Gäste aus anderen Organisationen, können Zugriff auf Zugriffspakete anfordern.|[Berechtigungsverwaltung](entitlement-management-overview.md)|
|Workflow|Ressourcenbesitzer können die genehmigenden Personen und Eskalationsgenehmiger für Zugriffsanforderungen sowie die genehmigenden Personen für Rollenaktivierungsanforderungen definieren.  |[Berechtigungsverwaltung](entitlement-management-overview.md) und [PIM](../privileged-identity-management/pim-configure.md)|
|Richtlinien- und Rollenverwaltung|Der Administrator kann Richtlinien für bedingten Zugriff definieren, die für den Zugriff auf Anwendungen zur Laufzeit gelten.  Ressourcenbesitzer können Richtlinien für den Benutzerzugriff über Zugriffspakete definieren.|Richtlinien für [bedingten Zugriff](../conditional-access/overview.md) und [Berechtigungsverwaltung](entitlement-management-overview.md)|
|Zugriffszertifizierung|Administratoren können die erneute Zertifizierung für wiederkehrende Zugriffe für folgende Komponenten ermöglichen: SaaS-Apps oder Cloudgruppenmitgliedschaften, Zuweisungen von Azure AD- oder Azure-Ressourcenrollen. Automatisches Entfernen des Ressourcenzugriffs, Blockieren des Gastzugriffs und Löschen von Gastkonten.|[Zugriffsüberprüfungen](access-reviews-overview.md), die auch unter [PIM](../privileged-identity-management/pim-create-azure-ad-roles-and-resource-roles-review.md) beschrieben wurden|
|Erfüllung und Bereitstellung|Automatische Bereitstellung und Aufhebung der Bereitstellung in mit Azure AD verbundenen Apps, z. B. per SCIM und auf SharePoint Online-Websites. |[Benutzerbereitstellung](../app-provisioning/user-provisioning.md)|
|Berichterstellung und Analyse|Administratoren können Überwachungsprotokolle der aktuellen Benutzerbereitstellung und Anmeldeaktivitäten abrufen. Integration mit Azure Monitor und Zugriffserteilung über Zugriffspakete.|[Azure AD-Berichte](../reports-monitoring/overview-reports.md) und [Überwachung](../reports-monitoring/overview-monitoring.md)|
|Privilegierter Zugriff|Just-In-Time- und geplanter Zugriff, Warnungen, Genehmigungsworkflows für Azure AD-Rollen (einschließlich benutzerdefinierte Rollen) und Azure-Ressourcenrollen.|[Azure AD PIM](../privileged-identity-management/pim-configure.md)|
|Überwachung|Administratoren können über die Erstellung von Administratorkonten benachrichtigt werden.|[Azure AD PIM-Warnungen](../privileged-identity-management/pim-how-to-configure-security-alerts.md)|

## <a name="getting-started"></a>Erste Schritte

Öffnen Sie im Azure-Portal die Registerkarte „Erste Schritte“ von **Identity Governance**, um mit der Verwendung der Berechtigungsverwaltung, von Zugriffsüberprüfungen, Privileged Identity Management und Nutzungsbedingungen zu beginnen.

![Identity Governance, Registerkarte „Erste Schritte“](./media/identity-governance-overview/getting-started.png)


Wenn Sie uns Feedback zu Identity Governance-Features geben möchten, klicken Sie im Azure-Portal auf **Haben Sie Feedback für uns?** , um Ihr Feedback zu senden. Das Team überprüft regelmäßig Ihr Feedback.

Es gibt keine perfekte Lösung oder Empfehlung, die für alle Kunden geeignet ist. Die folgenden Konfigurationsleitfäden können jedoch als Anhaltspunkt für die Basisrichtlinien dienen, die Microsoft zum Verbessern der Sicherheit und Produktivität von Mitarbeitern empfiehlt.

- [Konfigurationen für den Identitäts- und Gerätezugriff](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Sichern des privilegierten Zugriffs](../roles/security-planning.md)

## <a name="appendix---least-privileged-roles-for-managing-in-identity-governance-features"></a>Anhang: Die am wenigsten privilegierten Rollen für die Verwaltung von Identity Governance-Funktionen

Für die Ausführung administrativer Aufgaben in Identity Governance gilt die Verwendung der Rolle mit den geringsten Rechten als bewährte Methode. Es wird empfohlen, Azure AD PIM zu verwenden, um nach Bedarf eine Rolle zum Ausführen dieser Aufgaben zu aktivieren. Nachfolgend werden die Verzeichnisrollen mit den geringsten Rechten zum Konfigurieren von Identity Governance-Features aufgeführt:

| Funktion | Am wenigsten privilegierte Rolle |
| ------- | --------------------- |
| Berechtigungsverwaltung | Identity Governance-Administrator |
| Zugriffsüberprüfungen | Benutzeradministrator (mit Ausnahme von Zugriffsüberprüfungen für Azure- oder Azure AD-Rollen, wofür ein Administrator für privilegierte Rollen erforderlich ist) |
|Privileged Identity Management | Administrator für privilegierte Rollen |
| Nutzungsbedingungen | Sicherheitsadministrator oder Administrator für bedingten Zugriff |

>[!NOTE]
>Die Rolle mit den geringsten Berechtigungen für die Berechtigungsverwaltung wird von der Rolle „Benutzeradministrator“ in die Rolle „Identity Governance-Administrator“ geändert.

## <a name="next-steps"></a>Nächste Schritte

- [Was ist Azure AD-Berechtigungsverwaltung?](entitlement-management-overview.md)
- [Was sind Azure AD-Zugriffsüberprüfungen?](access-reviews-overview.md)
- [Was ist Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)
- [Welche Möglichkeiten bieten die Nutzungsbedingungen?](../conditional-access/terms-of-use.md)