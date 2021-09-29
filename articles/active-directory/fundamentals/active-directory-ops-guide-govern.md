---
title: Referenzleitfaden zu Azure Active Directory-Governancevorgängen
description: In diesem Referenzleitfaden werden die Überprüfungen und Aktionen beschrieben, die Sie zum Sichern der Governanceverwaltung vornehmen sollten.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 065b0ca66b2e8d253ae798ce672638877ddceef8
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124732654"
---
# <a name="azure-active-directory-governance-operations-reference-guide"></a>Referenzleitfaden zu Azure Active Directory-Governancevorgängen

In diesem Abschnitt des [Referenzleitfadens zu Azure AD-Vorgängen](active-directory-ops-guide-intro.md) werden die Überprüfungen und Aktionen beschrieben, die zum Bewerten und Bestätigen des Zugriffs für privilegierte und nicht privilegierte Identitäten sowie zum Überwachen und Prüfen von Änderungen an der Umgebung erforderlich sind.

> [!NOTE]
> Diese Empfehlungen sind auf dem Stand des Veröffentlichungsdatums, können sich aber im Laufe der Zeit ändern. Organisationen sollten ihre Governancemethoden fortlaufend überprüfen, da sich die Produkte und Dienste von Microsoft mit der Zeit entwickeln.

## <a name="key-operational-processes"></a>Wichtige Betriebsabläufe

### <a name="assign-owners-to-key-tasks"></a>Zuweisen von Besitzern zu wichtigen Aufgaben

Für die Verwaltung von Azure Active Directory ist die kontinuierliche Ausführung wichtiger betrieblicher Aufgaben und Prozesse erforderlich, die möglicherweise nicht Teil eines Rolloutprojekts sind. Es ist außerdem wichtig, dass Sie diese Aufgaben einrichten, um Ihre Umgebung zu optimieren. Im Folgenden werden die wichtigen Aufgaben und empfohlene Besitzer für diese aufgeführt:

| Aufgabe | Besitzer |
| :- | :- |
| Archivieren von Azure AD-Überwachungsprotokollen im SIEM-System | Team für Informationssicherheitvorgänge |
| Ermitteln von Anwendungen, deren Verwaltung nicht konform ist | Team für IAM-Vorgänge (Identity & Access Management, Identitäts- und Zugriffsverwaltung) |
| Regelmäßiges Überprüfen des Zugriffs auf Anwendungen | Team für die Informationssicherheitsarchitektur |
| Regelmäßiges Überprüfen des Zugriffs auf externe Identitäten | Team für die Informationssicherheitsarchitektur |
| Regelmäßige Überprüfung, wer privilegierte Rollen innehat | Team für die Informationssicherheitsarchitektur |
| Definieren von Sicherheitsgates zum Aktivieren privilegierter Rollen | Team für die Informationssicherheitsarchitektur |
| Regelmäßiges Überprüfen der Einwilligungen | Team für die Informationssicherheitsarchitektur |
| Entwerfen von Katalogen und Zugreifen auf Pakete für Anwendungen und Ressourcen, die für Mitarbeiter in der Organisation vorgesehen sind | App-Besitzer |
| Definieren von Sicherheitsrichtlinien zum Zuweisen von Benutzern zu Zugriffspaketen | Informationssicherheitsteam und App-Besitzer |
| Wenn Richtlinien Genehmigungsworkflows enthalten, müssen Sie die Einwilligungen für diese Workflows regelmäßig überprüfen. | App-Besitzer |
| Überprüfen von Ausnahmen in Sicherheitsrichtlinien, z. B. Richtlinien für den bedingten Zugriff mit Zugriffsüberprüfungen | Team für Informationssicherheitvorgänge |

Beim Überprüfen Ihrer Liste stellen Sie ggf. fest, dass Sie entweder einen Besitzer für Aufgaben zuweisen müssen, denen kein Besitzer zugeteilt ist, oder Aufgaben anpassen müssen, deren Besitzer nicht den obigen Empfehlungen entspricht.

#### <a name="owner-recommended-reading"></a>Empfohlene Artikel für Besitzer

- [Zuweisen von Administratorrollen in Azure Active Directory](../roles/permissions-reference.md)
- [Governance in Azure](../../governance/index.yml)

### <a name="configuration-changes-testing"></a>Testen von Konfigurationsänderungen

Für einige Änderungen sind beim Testen besondere Maßnahmen erforderlich, beispielsweise einfache Techniken wie das Rollout für eine Teilmenge der Zielbenutzer oder das Bereitstellen einer Änderung in einem parallelen Testmandanten. Wenn Sie keine Teststrategie implementiert haben, sollten Sie einen Testansatz definieren, der auf den Richtlinien in der folgenden Tabelle basiert:

| Szenario| Empfehlung |
|-|-|
|Ändern des Authentifizierungstyps von „Verbund“ in „PHS/PTA“ oder umgekehrt| Testen Sie die Auswirkung der Änderung des Authentifizierungstyp mit einem [gestaffelten Rollout](../hybrid/how-to-connect-staged-rollout.md).|
|Rollout einer neuen Richtlinie für bedingten Zugriff oder einer Identitätsschutzrichtlinie|Erstellen Sie eine neue Richtlinie für bedingten Zugriff, und weisen Sie diese Testbenutzern zu.|
|Onboarding einer Testumgebung für eine Anwendung|Fügen Sie die Anwendung zu einer Produktionsumgebung hinzu, blenden Sie sie im MyApps-Panel aus, und weisen Sie sie während der Qualitätssicherungsphase Testbenutzern zu.|
|Ändern von Synchronisierungsregeln|Führen Sie die Änderungen in einer Azure AD Connect-Testumgebung mit der gleichen Konfiguration aus, die zu diesem Zeitpunkt in der Produktion verwendet wird (auch als Stagingmodus bezeichnet), und analysieren Sie die Ergebnisse von CSExport. Wenn Sie zufrieden sind, wechseln Sie in die Produktion.|
|Brandingänderung|Führen Sie einen Test in einem separaten Testmandanten durch.|
|Rollout eines neuen Features|Wenn das Feature das Rollout für bestimmte Zielbenutzer ermöglicht, legen Sie Pilotbenutzer fest, und erweitern Sie diese nach und nach. Für die Self-Service-Kennwortzurücksetzung und die mehrstufige Authentifizierung können beispielsweise bestimmte Zielbenutzer oder -gruppen festgelegt werden.|
|Umstellung einer Anwendung von einem lokalen Identitätsanbieter (IdP) wie Azure Active Directory zu Azure AD|Wenn die Anwendung mehrere IdP-Konfigurationen unterstützt, z. B. Salesforce, konfigurieren Sie alle verfügbaren Optionen, und testen Sie Azure AD während eines Änderungsfensters (falls die Anwendung die HRD-Seite einführt). Wenn die Anwendung nur einen Identitätsanbieter unterstützt, sollten Sie das Testen während eines Änderungssteuerungsfensters und einer Downtime des Programms ansetzen.|
|Aktualisieren dynamischer Gruppenregeln|Erstellen Sie eine parallele dynamische Gruppe mit der neuen Regel. Gleichen Sie das berechnete Ergebnis ab, indem Sie beispielsweise PowerShell mit der gleichen Bedingung ausführen.<br>Wenn der Test erfolgreich verlaufen ist, tauschen Sie die Stellen aus, an denen die alte Gruppe verwendet wurde (sofern möglich).|
|Migrieren von Produktlizenzen|Weitere Informationen finden Sie unter [Ändern der Lizenz für einen einzelnen Benutzer in einer lizenzierten Gruppe in Azure Active Directory](../enterprise-users/licensing-groups-change-licenses.md).|
|Ändern von AD FS-Regeln wie Autorisierung, Ausstellung, MFA|Verwenden Sie einen Gruppenanspruch, um eine Teilmenge der Benutzer zu verwenden.|
|Ändern der AD FS-Authentifizierungsfunktion oder ähnliche farmweite Änderungen|Erstellen Sie eine parallele Farm mit demselben Hostnamen, implementieren Sie Konfigurationsänderungen, testen Sie sie auf Clients mithilfe von HOSTS-Dateien, NLB-Routingregeln oder ähnlichem Routing.<br>Wenn die Zielplattform (z. B. mobile Geräte) keine Hostdateien unterstützt, überprüfen Sie die Änderung.|

## <a name="access-reviews"></a>Zugriffsüberprüfungen

### <a name="access-reviews-to-applications"></a>Zugriffsüberprüfungen auf Anwendungen

Im Laufe der Zeit können Benutzer den Zugriff auf Ressourcen ansammeln, wenn sie verschiedene Teams und Positionen durchlaufen. Es ist wichtig, dass Ressourcenbesitzer den Zugriff auf Anwendungen regelmäßig überprüfen und Berechtigungen entfernen, die von bestimmten Benutzern nicht mehr benötigt werden. Mithilfe von [Azure AD-Zugriffsüberprüfungen](../governance/access-reviews-overview.md) können Unternehmen Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen sowie Rollenzuweisungen effizient verwalten. Ressourcenbesitzer sollten den Benutzerzugriff regelmäßig überprüfen, um sicherzustellen, dass nur die richtigen Personen weiterhin Zugriff besitzen. Im Idealfall sollten Sie Azure AD-Zugriffsüberprüfungen für diese Aufgabe verwenden.

![Startseite für Zugriffsüberprüfungen](./media/active-directory-ops-guide/active-directory-ops-img15.png)

> [!NOTE]
> Jeder Benutzer, der mit Zugriffsüberprüfungen interagiert, benötigt eine kostenpflichtige Azure AD Premium P2-Lizenz.

### <a name="access-reviews-to-external-identities"></a>Zugriffsüberprüfungen für externe Identitäten

Es ist von entscheidender Bedeutung, den Zugriff auf externe Identitäten nur auf Ressourcen zu beschränken, die während einer bestimmten Zeit benötigt werden. Richten Sie mithilfe der [Azure AD-Zugriffsüberprüfungen](../governance/access-reviews-overview.md) einen regulären automatisierten Prozess für die Zugriffsüberprüfung für alle externen Identitäten und den Anwendungszugriff ein. Wenn ein Prozess bereits lokal vorhanden ist, sollten Sie die Verwendung der Azure AD Zugriffsüberprüfungen in Erwägung ziehen. Wenn eine Anwendung eingestellt oder nicht mehr verwendet wird, entfernen Sie alle externen Identitäten, die Zugriff auf die Anwendung haben.

> [!NOTE]
> Jeder Benutzer, der mit Zugriffsüberprüfungen interagiert, benötigt eine kostenpflichtige Azure AD Premium P2-Lizenz.

## <a name="privileged-account-management"></a>Verwaltung privilegierter Konten

### <a name="privileged-account-usage"></a>Verwendung privilegierter Konten

Hacker haben häufig Administratorkonten und andere Komponenten mit privilegiertem Zugriff im Visier, um mit Angriffen schnell Zugriff auf vertrauliche Daten und Systeme zu erhalten.  Da sich Benutzer mit privilegierten Rollen tendenziell im Laufe der Zeit ansammeln, ist es wichtig, den Administratorzugriff regelmäßig zu überprüfen und zu verwalten und privilegierten Just-In-Time-Zugriff auf Azure AD- und Azure-Ressourcen zu gewähren.

Wenn in Ihrer Organisation kein Prozess zum Verwalten privilegierter Konten vorhanden ist, oder wenn einige Administratoren derzeit ihre regulären Benutzerkonten zum Verwalten von Diensten und Ressourcen verwenden, sollten Sie sofort mit der Verwendung separater Konten beginnen, z. B. eines für reguläre Alltagsaktivitäten und ein anderes für privilegierten Zugriff, das mit MFA konfiguriert wurde. Wenn Ihre Organisation ein Azure AD Premium P2-Abonnement besitzt, sollten Sie sofort [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md#license-requirements) aktivieren. Im gleichen Zug sollten Sie auch diese privilegierten Konten überprüfen und ggf. [Rollen mit geringeren Berechtigungen](../roles/security-planning.md) zuweisen.

Sie sollten im Rahmen der Verwaltung privilegierter Konten zudem [Zugriffsüberprüfungen](../governance/access-reviews-overview.md) für diese Konten festlegen (manuell oder [über PIM automatisiert](../privileged-identity-management/pim-perform-azure-ad-roles-and-resource-roles-review.md)).

#### <a name="privileged-account-management-recommended-reading"></a>Empfohlene Artikel zur Verwaltung privilegierter Konten

- [Rollen in Azure AD Privileged Identity Management](../privileged-identity-management/pim-roles.md)

### <a name="emergency-access-accounts"></a>Konten für den Notfallzugriff

Organisationen müssen [Notfallkonten](../roles/security-emergency-access.md) einrichten, damit sie im Fall von Authentifizierungsausfällen wie den folgenden auf die Verwaltung von Azure AD vorbereitet sind:

- Ausfall von Komponenten der Authentifizierungsinfrastruktur (AD FS, lokale AD-Instanzen, MFA-Dienst)
- Mitarbeiterfluktuation in der Verwaltungsebene

Sie sollten zwei oder mehr Notfallkonten erstellen, um sicherzustellen, dass diese mit den [Best Practices](../roles/security-planning.md) und [Break-Glass-Verfahren](../roles/security-planning.md#break-glass-what-to-do-in-an-emergency) von Microsoft konform sind. So können Sie vermeiden, versehentlich aus Ihrem Mandanten ausgesperrt zu werden, weil Sie sich mit einem bestehenden individuellen Benutzerkonto nicht als Administrator anmelden oder dieses aktivieren können.

### <a name="privileged-access-to-azure-ea-portal"></a>Privilegierter Zugriff auf das Azure EA-Portal

Über das [Azure EA-Portal (Azure Enterprise Agreement)](https://azure.microsoft.com/blog/create-enterprise-subscription-experience-in-azure-portal-public-preview/) können Sie Azure-Abonnements für ein Master-Enterprise-Agreement erstellen. Diese Rolle ist in einem Unternehmen besonders wichtig. Häufig wird dieses Portal erstellt, noch bevor Azure AD eingerichtet ist. Sie müssen also Azure AD-Identitäten verwenden, um es zu sperren, persönliche Konten aus dem Portal zu entfernen, sicherzustellen, dass eine ordnungsgemäße Delegierung vorhanden ist, und das Risiko einer Sperrung zu verhindern.

Wenn die Authentifizierungsebene derzeit also auf „Gemischter Modus“ festgelegt ist, müssen Sie allen [Microsoft-Konten](https://support.skype.com/en/faq/FA12059/what-is-a-microsoft-account) den privilegierten Zugriff im EA-Portal entziehen und das Portal so konfigurieren, dass nur Azure AD-Konten verwendet werden können. Wenn die delegierten Rollen im EA-Portal nicht konfiguriert sind, sollten Sie delegierte Rollen für Abteilungen und Konten festlegen und implementieren.

#### <a name="privileged-access-recommended-reading"></a>Empfohlene Artikel für den privilegierten Zugriff

- [Berechtigungen der Administratorrolle in Azure Active Directory](../roles/permissions-reference.md)

## <a name="entitlement-management"></a>Berechtigungsverwaltung

Mit der [Berechtigungsverwaltung](../governance/entitlement-management-overview.md) können App-Besitzer Ressourcen bündeln und diese bestimmten internen oder externen Personas in der Organisation zuweisen. So können Sie die Self-Service-Registrierung und Delegierung für Unternehmensbesitzer einrichten und gleichzeitig Governancerichtlinien für das Genehmigen von Zugriff, das Festlegen der Zugriffsdauer und das Zulassen von Genehmigungsworkflows beibehalten. 

> [!NOTE]
> Für die Azure AD-Berechtigungsverwaltung sind Azure AD Premium P2-Lizenzen erforderlich.

## <a name="summary"></a>Zusammenfassung

Eine sichere Identitätsgovernance zeichnet sich durch acht Merkmale aus. In dieser Liste werden die Aktionen beschrieben, die zum Bewerten und Bestätigen des Zugriffs für privilegierte und nicht privilegierte Identitäten sowie zum Überwachen und Prüfen von Änderungen an der Umgebung erforderlich sind.

- Zuweisen von Besitzern zu wichtigen Aufgaben
- Implementieren Sie eine Teststrategie.
- Verwenden Sie Azure AD-Zugriffsüberprüfungen, um Gruppenmitgliedschaften den Zugriff auf Unternehmensanwendungen und Rollenzuweisungen effizient zu verwalten.
- Richten Sie einen regulären automatisierten Prozess für die Zugriffsüberprüfung für alle externen Identitäten und den Anwendungszugriff ein.
- Richten Sie einen Zugriffsüberprüfungsprozess ein, um den Administratorzugriff regelmäßig zu überprüfen und zu verwalten und privilegierten Just-In-Time-Zugriff auf Azure AD- und Azure-Ressourcen zu gewähren.
- Richten Sie Notfallkonten ein, die für die Verwaltung von Azure AD bei unerwarteten Ausfällen vorbereitet werden sollen.
- Sperren Sie den Zugriff auf das Azure EA-Portal.
- Implementieren Sie die Berechtigungsverwaltung, um den kontrollierten Zugriff auf eine Ressourcensammlung zu ermöglichen.

## <a name="next-steps"></a>Nächste Schritte

Machen Sie sich zunächst mit [Überprüfungen und Aktionen für Azure AD](active-directory-ops-guide-ops.md) vertraut.