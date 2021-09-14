---
title: Planen Sie eine Bereitstellung von Privileged Identity Management? – Azure | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Privileged Identity Management (PIM) in Ihrer Azure AD-Organisation bereitstellen.
services: active-directory
documentationcenter: ''
author: BarbaraSelden
manager: martinco
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: conceptual
ms.date: 07/26/2021
ms.author: baselden
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99bdfeff59f26f59c9d64bcca9226d9b1f70ec1d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122340185"
---
# <a name="plan-a-privileged-identity-management-deployment"></a>Planen einer Bereitstellung von Privileged Identity Management

**Privileged Identity Management (PIM)** bietet eine zeit- und genehmigungsbasierte Rollenaktivierung, damit die Risiken in Verbindung mit übermäßigen, unnötigen oder missbräuchlich verwendeten Zugriffsberechtigungen für wichtige Ressourcen verringert werden können. Diese Ressourcen umfassen Ressourcen in Azure Active Directory (Azure AD), Azure und anderen Microsoft-Onlinediensten wie Microsoft 365 oder Microsoft Intune. Sie können PIM auch mit Dienstanwendungen (SaaS) verwenden.

Mit PIM können Sie bestimmte Aktionen für einen bestimmten Bereich zulassen. Hauptfunktionen:

* Gewähren von privilegiertem **Just-In-Time**-Zugriff auf Ressourcen

* Zuweisen der **Berechtigung für eine Mitgliedschaft oder den Besitz** (in) einer Gruppe mit privilegiertem Zugriff

* Zuweisen von **zeitgebundenem** Zugriff auf Ressourcen (mit Start- und Enddatum)

* Anfordern einer **Genehmigung** bei der Aktivierung privilegierter Rollen

* Erzwingen der **mehrstufigen Authentifizierung** zum Aktivieren einer beliebigen Rolle

* Anfordern einer **Begründung**, um nachvollziehen zu können, warum Benutzer eine Aktivierung vornehmen

* Erhalten von **Benachrichtigungen**, wenn privilegierte Rollen aktiviert werden

* Durchführen von **Zugriffsüberprüfungen**, um zu prüfen, ob Benutzer die Rollen weiterhin benötigen

* Herunterladen eines **Überwachungsverlaufs** zur internen oder externen Überwachung

Damit Sie von diesem Bereitstellungsplan bestmöglich profitieren, ist es wichtig, dass Sie sich unter [Was ist Privileged Identity Management?](pim-configure.md) einen vollständigen Überblick über den PIM-Dienst verschaffen.

## <a name="understand-pim"></a>Verstehen von PIM

Die PIM-Konzepte in diesem Abschnitt helfen Ihnen zu verstehen, welche Anforderung Ihre Organisation an privilegierte Identitäten stellt.

### <a name="what-can-you-manage-in-pim"></a>Was können Sie in PIM verwalten?

Heute können Sie PIM mit Folgendem verwenden:

* **Azure AD-Rollen**: Diese manchmal auch als Verzeichnisrollen bezeichneten Rollen enthalten integrierte und benutzerdefinierte Rollen zum Verwalten von Azure AD und anderen Microsoft 365-Onlinediensten.

* **Azure-Rollen**: Die Rollen für die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) in Azure, die Zugriff auf Verwaltungsgruppen, Abonnements, Ressourcengruppen und Ressourcen gewähren

* **Gruppen mit privilegiertem Zugriff**: Zum Einrichten des Just-In-Time-Zugriffs auf die Rolle „Mitglied“ und „Besitzer“ einer Azure AD-Sicherheitsgruppe. Gruppen mit privilegiertem Zugriff bieten Ihnen nicht nur eine alternative Möglichkeit zum Einrichten von PIM für Azure AD-Rollen und Azure-Rollen, sondern auch zum Einrichten von PIM für andere Berechtigungen in Microsoft-Onlinediensten wie Intune, Azure Key Vault und Azure Information Protection. 

Sie können diesen Rollen oder Gruppen Folgendes zuweisen: 

* **Benutzer**: Damit Sie Just-In-Time-Zugriff auf Azure AD-Rollen, Azure-Rollen und Gruppen mit privilegiertem Zugriff erhalten 

* **Gruppen**: Jeder Benutzer in einer Gruppe, der Just-In-Time-Zugriff auf Azure AD-Rollen und Azure-Rollen erhalten soll. Im Fall von Azure AD-Rollen muss es sich bei der Gruppe um eine neu erstellte Cloudgruppe handeln, die als einer Rolle zuweisbar gekennzeichnet ist, während die Gruppe im Fall von Azure-Rollen eine beliebige Azure AD-Sicherheitsgruppe sein kann. Es wird nicht empfohlen, eine Gruppe einer Gruppe mit privilegiertem Zugriff zuzuweisen bzw. eine Gruppe in einer Gruppe mit privilegiertem Zugriff zu schachteln. 

> [!NOTE] 
>Sie können Dienstprinzipale nicht als berechtigt für Azure AD-Rollen, Azure-Rollen und Gruppen mit privilegiertem Zugriff zuweisen, aber Sie können allen dreien eine zeitlich begrenzte aktive Zuweisung gewähren.

### <a name="principle-of-least-privilege"></a>Prinzip der geringsten Rechte

Sie weisen Benutzern die Rolle mit den [geringsten Rechten](../roles/delegate-by-task.md) zu, die zum Ausführen ihrer Aufgaben erforderlich sind. Diese Methode minimiert die Anzahl von globalen Administratoren, indem stattdessen spezifische Administratorrollen für bestimmte Szenarien verwendet werden.

> [!NOTE] 
> Microsoft verfügt über sehr wenige globale Administratoren. Weitere Informationen finden Sie unter [Verwenden von Azure AD Privileged Identity Management für erhöhte Zugriffsrechte](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access).

### <a name="type-of-assignments"></a>Typen von Zuweisungen 

Es gibt zwei Typen von Zuweisungen: **berechtigt** und **aktiv**. Wenn ein Benutzer zu einer Rolle berechtigt ist, kann er die Rolle aktivieren, wenn er privilegierte Aufgaben ausführen muss. 

Sie können auch eine Start- und Endzeit für jeden Typ von Zuweisung festlegen. Durch diese zusätzliche Festlegung erhalten Sie vier mögliche Zuweisungstypen:

* Dauerhaft berechtigt

* Dauerhaft aktiv

* Zeitgebunden berechtigt, mit angegebenen Start- und Enddaten für die Zuweisung

* Zeitgebunden aktiv, mit angegebenen Start- und Enddaten für die Zuweisung

Falls die Rolle abläuft, können Sie diese Zuweisungen **verlängern** oder **erneuern**. 

**Empfehlung**: Legen Sie für Rollen keine anderen dauerhaft aktiven Zuweisungen fest als die empfohlenen [zwei Konten für den Notfallzugriff](../roles/security-emergency-access.md), die über die permanente Rolle „Globaler Administrator“ verfügen müssen. 

## <a name="plan-the-project"></a>Planen des Projekts

Wenn Technologieprojekte scheitern, ist dies häufig auf nicht erfüllte Erwartungen in Bezug auf Auswirkungen, Ergebnisse und Zuständigkeiten zurückzuführen. Um diese Fallstricke zu vermeiden, stellen Sie sicher, dass Sie die [richtigen Beteiligten hinzuziehen](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders) und dass die Rollen der Beteiligten im Projekt gut verstanden werden.

### <a name="plan-a-pilot"></a>Planen eines Pilotprojekts

Stellen Sie in jeder Phase der Bereitstellung sicher, dass ausgewertet wird, ob die Ergebnisse den Erwartungen entsprechen. Lesen Sie hierzu [Bewährte Methoden für einen Pilotversuch](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot).

* Beginnen Sie mit einer kleinen Gruppe von Benutzern (Pilotgruppe), und vergewissern Sie sich, dass das Systemverhalten des PIM-Diensts den Erwartungen entspricht.

* Überprüfen Sie, ob die gesamte von Ihnen für die Rollen oder für Gruppen mit privilegiertem Zugriff festgelegte Konfiguration ordnungsgemäß funktioniert. 

* Stellen Sie den Dienst erst nach gründlicher Testung in der Produktion bereit. 

### <a name="plan-communications"></a>Planen der Benachrichtigungen

Kommunikation ist ein kritischer Faktor für den Erfolg jedes neuen Diensts. Kommunizieren Sie proaktiv mit Ihren Benutzern darüber, wie sich die Nutzung ändern wird, wann sie sich ändert und wie sie Unterstützung erhalten, wenn sie auf Probleme stoßen.

Planen Sie Zeit mit Ihrem internen IT-Support ein, um die Mitarbeiter mit dem PIM-Workflow vertraut zu machen. Stellen Sie ihnen die entsprechenden Dokumentationsmaterialien sowie Ihre Kontaktdaten zur Verfügung.

## <a name="plan-testing-and-rollback"></a>Planen von Tests und Rollbacks

> [!NOTE] 
> Im Fall von Azure AD-Rollen führen Organisationen häufig zuerst Tests und Bereitstellungen für globale Administratoren durch. Im Fall von Azure-Ressourcen wird der PIM-Dienst in der Regel für jeweils nur ein Azure-Abonnement gleichzeitig getestet. 

### <a name="plan-testing"></a>Planen von Tests

Erstellen Sie Testbenutzer, um sich zu vergewissern, ob die PIM-Einstellungen erwartungsgemäß funktionieren, bevor Sie sie für echte Benutzer aktivieren und dadurch möglicherweise deren Zugriff auf Anwendungen und Ressourcen unterbrechen. Erstellen Sie einen Testplan, um die erwarteten Ergebnisse mit den tatsächlichen Ergebnissen vergleichen zu können. 

Die folgende Tabelle veranschaulicht einen Testfall: 

| Role| Erwartetes Verhalten während der Aktivierung| Tatsächliche Ergebnisse |
| --- | --- | --- |
|Globaler Administrator| <li> Anfordern von MFA <br><li>  Anfordern einer Genehmigung <br><li>  Genehmigende Person erhält Benachrichtigung und kann Genehmigung erteilen <br><li>  Rolle läuft nach voreingestellter Zeit ab|

Vergewissern Sie sich bei Azure AD-Rollen und Azure-Ressourcenrollen, dass Benutzer vorhanden sind, die diese Rollen übernehmen sollen. Berücksichtigen Sie außerdem die folgenden Rollen, wenn Sie den PIM-Dienst in Ihrer gestaffelten Umgebung testen:

| Rollen| Azure AD-Rollen| Azure-Ressourcenrollen| Gruppen mit privilegiertem Zugriff |
| --- | --- | --- |--- |
| Mitglied einer Gruppe| | | x |
| Mitglieder einer Rolle| x| x|  |
| IT-Dienstbesitzer| x| | x |
| Abonnement- oder Ressourcenbesitzer| | x| x |
| Besitzer einer Gruppe mit privilegiertem Zugriff| | | x |

### <a name="plan-rollback"></a>Planen eines Rollbacks

Wenn der PIM-Dienst in der Produktionsumgebung nicht wie gewünscht funktioniert, können Sie erneut die Rollenzuweisung von berechtigt in aktiv ändern. Wählen Sie für jede von Ihnen konfigurierte Rolle die Auslassungspunkte (...) für alle Benutzer mit einer Zuweisung vom Typ **berechtigt** aus. Sie können dann die Option **Make active** (Aktivieren) auswählen, um zurück zu wechseln und die Rollenzuweisung zu **aktivieren**.

## <a name="plan-and-implement-pim-for-azure-ad-roles"></a>Planen und Implementieren von PIM für Azure AD-Rollen

Führen Sie diese Aufgaben aus, um PIM für die Verwaltung von Azure AD-Rollen vorzubereiten. 

### <a name="discover-and-mitigate-privileged-roles"></a>Entdecken und Reduzieren des Risikos privilegierter Rollen

Erstellen Sie eine Liste der Benutzer mit privilegierten Rollen in Ihrer Organisation. Überprüfen Sie die zugewiesenen Benutzer, identifizieren Sie Administratoren, die die Rolle nicht mehr benötigen, und entfernen Sie sie aus ihren Zuweisungen. 

Sie können [Zugriffsüberprüfungen für Azure AD-Rollen](pim-how-to-start-security-review.md) verwenden, um die Ermittlung, Überprüfung und Genehmigung oder Entfernung von Zuweisungen zu automatisieren.

### <a name="determine-roles-to-be-managed-by-pim"></a>Bestimmen von Rollen, die von PIM verwaltet werden sollen

Priorisieren Sie den Schutz von Azure AD-Rollen, die über die meisten Berechtigungen verfügen. Außerdem ist es wichtig zu berücksichtigen, welche Daten und Berechtigungen für Ihre Organisation am sensibelsten sind. 

Vergewissern Sie sich zunächst, dass alle Rollen von globalen Administratoren und Sicherheitsadministratoren mit PIM verwaltet werden, da dies die Benutzer sind, die den größten Schaden anrichten können, wenn sie kompromittiert werden. Ziehen Sie dann weitere Rollen in Betracht, die verwaltet werden sollten und die anfällig für Angriffe sein könnten.

### <a name="configure-pim-settings-for-azure-ad-roles"></a>Konfigurieren von PIM-Einstellungen für Azure AD-Rollen

[Entwerfen und konfigurieren Sie die PIM-Einstellungen](pim-how-to-change-default-settings.md) für jede privilegierte Azure AD-Rolle, die Ihre Organisation verwendet. 

Die folgende Tabelle enthält Beispieleinstellungen:

| Role| Anfordern von MFA| Benachrichtigung| Vorfallsticket| Genehmigung anfordern| Genehmigende Person| Aktivierungsdauer| Permanenter Administrator |
| --- | --- | --- |--- |--- |--- |--- |--- |
| Globaler Administrator| :heavy_check_mark:| :heavy_check_mark:| :heavy_check_mark:| :heavy_check_mark:| Anderer globaler Administrator| 1 Stunde| Konten für den Notfallzugriff |
| Exchange-Verwaltung| :heavy_check_mark:| :heavy_check_mark:| :x:| :x:| Keine| 2 Stunden| Keine |
| Helpdeskadministrator| :x:| :x:| :heavy_check_mark:| :x:| Keine| 8 Stunden| Keine |


### <a name="assign-and-activate-azure-ad-roles"></a>Zuweisen und Aktivieren von Azure AD-Rollen 

Für Azure AD-Rollen in PIM gilt: Nur die Benutzer mit der Rolle „Administrator für privilegierte Rollen“ oder der Rolle „Globaler Administrator“ können Zuweisungen für andere Administratoren verwalten. Globale Administratoren, Sicherheitsadministratoren, globale Leser und Benutzer mit Leseberechtigung für die Sicherheitsfunktionen können auch die Azure AD-Rollen in PIM anzeigen. 

Folgen Sie den Anweisungen unter den nachstehenden Links:

1. [Gewähren der berechtigten Zuweisungen](pim-how-to-add-role-to-user.md)

2. [Zulassen, dass berechtigte Benutzer für ihre Azure AD-Rolle eine Just-In-Time-Aktivierung durchführen](pim-how-to-activate-role.md)

Wenn sich der Ablaufzeitpunkt der Rolle nähert, verwenden Sie [PIM, um die Rollen zu verlängern oder zu erneuern](pim-resource-roles-renew-extend.md). Diese beiden vom Benutzer initiierten Aktionen erfordern eine Genehmigung von einem globalen Administrator oder einem Administrator für privilegierte Rollen.  Diese beiden vom Benutzer initiierten Aktionen erfordern eine Genehmigung von einem globalen Administrator oder einem Administrator für privilegierte Rollen. 

Wenn diese wichtigen Ereignisse bei Azure AD-Rollen auftreten, werden von PIM, je nach Rollen-, Ereignis- und Benachrichtigungseinstellungen, [E-Mail-Benachrichtigungen und wöchentliche Digest-E-Mails](pim-email-notifications.md) an Berechtigungen verwaltende Administratoren gesendet. Diese E-Mail-Benachrichtigungen können auch Links zu relevanten Aufgaben, z.B. Aktivieren oder Erneuern einer Rolle, enthalten. 

> [!NOTE] 
>Sie können diese PIM-Aufgaben auch [mithilfe der Microsoft Graph-APIs für Azure AD-Rollen ausführen](pim-apis.md). 

### <a name="approve-or-deny-pim-activation-requests"></a>Genehmigen oder Verweigern von PIM-Aktivierungsanforderungen 

Eine delegierte genehmigende Person erhält eine E-Mail-Benachrichtigung, wenn eine Anforderung zur Genehmigung aussteht. Führen Sie die folgenden Schritte aus, um [Anforderungen zum Aktivieren einer Azure-Ressourcenrolle zu genehmigen oder zu verweigern](pim-resource-roles-approval-workflow.md).

### <a name="view-audit-history-for-azure-ad-roles"></a>Anzeigen des Überwachungsverlaufs für Azure AD-Rollen

[Zeigen Sie den Überwachungsverlauf für alle Rollenzuweisungen und -aktivierungen](pim-how-to-use-audit-log.md) innerhalb der letzten 30 Tage für Azure AD-Rollen an. Globale Administratoren und Administratoren für privilegierte Rollen können auf die Überwachungsprotokolle zugreifen. 

**Empfehlung**: Mindestens ein Administrator sollte wöchentlich alle Überwachungsereignisse lesen und monatlich alle Überwachungsereignisse exportieren.

### <a name="security-alerts-for-azure-ad-roles"></a>Sicherheitswarnungen für Azure AD-Rollen

[Konfigurieren Sie Sicherheitswarnungen für die Azure AD-Rollen](pim-how-to-configure-security-alerts.md), die bei verdächtigen und unsicheren Aktivitäten eine Warnung auslösen.

## <a name="plan-and-implement-pim-for-azure-resource-roles"></a>Planen und Implementieren von PIM für Azure-Ressourcenrollen

Führen Sie diese Aufgaben aus, um PIM für die Verwaltung von Azure-Ressourcenrollen vorzubereiten.

### <a name="discover-and-mitigate-privileged-roles"></a>Entdecken und Reduzieren des Risikos privilegierter Rollen

Minimieren Sie Zuweisungen der Rollen „Besitzer“ und „Benutzerzugriffsadministrator“ für die einzelnen Abonnements oder Ressourcen, und entfernen Sie unnötige Zuweisungen.

Als globaler Administrator können Sie den [Zugriff erhöhen, um alle Azure-Abonnements zu verwalten](../../role-based-access-control/elevate-access-global-admin.md). Anschließend können Sie die Besitzer der einzelnen Abonnements ermitteln und in Zusammenarbeit mit ihnen unnötige Zuweisungen innerhalb ihrer Abonnements entfernen.

Verwenden Sie [Zugriffsüberprüfungen für Azure-Ressourcen](pim-resource-roles-start-access-review.md), um unnötige Rollenzuweisungen zu überwachen und zu entfernen. 

### <a name="determine-roles-to-be-managed-by-pim"></a>Bestimmen von Rollen, die von PIM verwaltet werden sollen

Bei der Entscheidung, welche Rollenzuweisungen mithilfe von PIM für Azure-Ressourcen verwaltet werden sollen, müssen Sie zunächst die [Verwaltungsgruppen](../../governance/management-groups/overview.md), Abonnements, Ressourcengruppen und Ressourcen identifizieren, die für Ihre Organisation am wichtigsten sind. Erwägen Sie die Verwendung von Verwaltungsgruppen, um alle ihre Ressourcen in ihrer Organisation zu organisieren.

**Es wird empfohlen**, alle Rollen des Typs „Abonnementbesitzer“ und „Benutzerzugriffsadministrator“ mit PIM zu verwalten. 

Arbeiten Sie mit Abonnementbesitzern zusammen, um Ressourcen zu dokumentieren, die von den einzelnen Abonnements verwaltet werden, und klassifizieren Sie die Risikostufe jeder Ressource, wenn sie kompromittiert wird. Priorisieren Sie anhand dieser Risikostufen, welche Ressourcen mit PIM verwaltet werden sollen. Dies beinhaltet auch benutzerdefinierte Ressourcen, die dem Abonnement zugeordnet sind.

**Weitere Empfehlung**: Arbeiten Sie mit Abonnement- oder Ressourcenbesitzern von kritischen Diensten zusammen, um den PIM-Workflow für alle Rollen in sensiblen Abonnements oder Ressourcen einzurichten.

Für Abonnements oder Ressourcen, die nicht so kritisch sind, müssen Sie PIM nicht für alle Rollen einrichten. Allerdings sollten Sie die Rollen „Besitzer“ und „Benutzerzugriffsadministrator“ mit PIM schützen.

### <a name="configure-pim-settings-for-azure-resource-roles"></a>Konfigurieren von PIM-Einstellungen für Azure-Ressourcenrollen

[Entwerfen und konfigurieren Sie Einstellungen](pim-resource-roles-configure-role-settings.md) für die Azure-Ressourcenrollen, die Sie mit PIM schützen möchten. 

Die folgende Tabelle enthält Beispieleinstellungen:

| Role| Anfordern von MFA| Benachrichtigung| Genehmigung anfordern| Genehmigende Person| Aktivierungsdauer| Aktiver Administrator| Ablauf der Aktivierung| Ablauf der Berechtigung|
| --- | --- | --- |--- |--- |--- |--- |---|---|
| Besitzer von wichtigen Abonnements| :heavy_check_mark:| :heavy_check_mark:| :heavy_check_mark:| Andere Besitzer des Abonnements| 1 Stunde| Keine| –| 3 Monate |
| Benutzerzugriffsadministrator von weniger wichtigen Abonnements| :heavy_check_mark:| :heavy_check_mark:| :x:| Keine| 1 Stunde| Keine| –| 3 Monate |

### <a name="assign-and-activate-azure-resource-role"></a>Zuweisen und Aktivieren der Azure-Ressourcenrolle

Für Azure-Ressourcenrollen in PIM kann nur ein Besitzer oder Benutzerzugriffsadministrator Zuweisungen für andere Administratoren verwalten. Benutzer, die als Administratoren für privilegierte Rollen, Sicherheitsadministratoren oder Sicherheitsleseberechtigte fungieren, haben nicht standardmäßig Zugriff zum Anzeigen von Zuweisungen zu Azure-Ressourcenrollen.

Folgen Sie den Anweisungen unter den nachstehenden Links:

1. [Gewähren der berechtigten Zuweisungen](pim-resource-roles-assign-roles.md)

2. [Zulassen, dass berechtigte Benutzer für ihre Azure-Rolle eine Just-In-Time-Aktivierung durchführen](pim-resource-roles-activate-your-roles.md)

Wenn sich der Ablaufzeitpunkt der Zuweisung einer privilegierten Rolle nähert, [verwenden Sie PIM, um die Rolle zu verlängern oder zu erneuern](pim-resource-roles-renew-extend.md). Beide vom Benutzer initiierten Aktionen erfordern eine Genehmigung des Ressourcenbesitzers oder Benutzerzugriffsadministrators. 

Wenn diese wichtigen Ereignisse bei Azure-Ressourcenrollen auftreten, werden von PIM [E-Mail-Benachrichtigungen](pim-email-notifications.md) an Besitzer und Benutzerzugriffsadministratoren gesendet. Diese E-Mail-Benachrichtigungen können auch Links zu relevanten Aufgaben, z.B. Aktivieren oder Erneuern einer Rolle, enthalten.

>[!NOTE]
>Sie können diese PIM-Aufgaben auch [mithilfe der Microsoft Azure Resource Manager-APIs für Azure-Ressourcenrollen ausführen](pim-apis.md). 

### <a name="approve-or-deny-pim-activation-requests"></a>Genehmigen oder Verweigern von PIM-Aktivierungsanforderungen

[Genehmigen oder Verweigern von Aktivierungsanforderungen für Azure AD-Rollen](azure-ad-pim-approval-workflow.md): Eine delegierte genehmigende Person empfängt eine E-Mail-Benachrichtigung, wenn eine Anforderung zur Genehmigung aussteht.

### <a name="view-audit-history-for-azure-resource-roles"></a>Anzeigen des Überwachungsverlaufs für Azure-Ressourcenrollen

[Zeigen Sie den Überwachungsverlauf für alle Zuweisungen und Aktivierungen](azure-pim-resource-rbac.md) innerhalb der letzten 30 Tage für Azure-Ressourcenrollen an.

### <a name="security-alerts-for-azure-resource-roles"></a>Sicherheitswarnungen für Azure-Ressourcenrollen

[Konfigurieren Sie Sicherheitswarnungen für die Azure-Ressourcenrollen](pim-resource-roles-configure-alerts.md), die bei verdächtigen und unsicheren Aktivitäten eine Warnung auslösen.

## <a name="plan-and-implement-pim-for-privileged-access-groups"></a>Planen und Implementieren von PIM für Gruppen mit privilegiertem Zugriff

Führen Sie diese Aufgaben aus, um PIM für die Verwaltung von Gruppen mit privilegiertem Zugriff vorzubereiten.

### <a name="discover-privileged-access-groups"></a>Entdecken von Gruppen mit privilegiertem Zugriff

Es kann der Fall sein, dass eine Person fünf oder sechs berechtigte Zuweisungen zu Azure AD-Rollen über PIM hat. Sie müssen dann jede Rolle einzeln aktivieren, was die Produktivität verringern kann. Schlimmer noch, ihnen können auch Dutzende oder Hunderte von Azure-Ressourcen zugewiesen sein, was das Problem noch verschlimmert.

In diesem Fall sollten Sie privilegierte Zugriffsgruppenverwenden. Erstellen Sie eine Gruppe mit privilegiertem Zugriff, und gewähren Sie ihr permanenten aktiven Zugriff auf mehrere Rollen. Weitere Informationen finden Sie unter [Verwaltungsfunktionen für Gruppen mit privilegiertem Zugriff](groups-features.md).

Damit Sie eine Azure AD-Gruppe, der Rollen zugewiesen werden können, als Gruppe mit privilegiertem Zugriff verwalten können, müssen Sie [diese der Verwaltung in PIM unterstellen](groups-discover-groups.md).

### <a name="configure-pim-settings-for-privileged-access-groups"></a>Konfigurieren von PIM-Einstellungen für Gruppen mit privilegiertem Zugriff

[Entwerfen und konfigurieren Sie Einstellungen](groups-role-settings.md) für die Gruppen mit privilegiertem Zugriff, die Sie mit PIM schützen möchten.

Die folgende Tabelle enthält Beispieleinstellungen:

| Role| Anfordern von MFA| Benachrichtigung| Genehmigung anfordern| Genehmigende Person| Aktivierungsdauer| Aktiver Administrator| Ablauf der Aktivierung| Ablauf der Berechtigung |
| --- | --- | --- |--- |--- |--- |--- |---|---|
| Besitzer| :heavy_check_mark:| :heavy_check_mark:| :heavy_check_mark:| Andere Besitzer der Ressource| 1 Stunde| Keine| –| 3 Monate |
| Member| :heavy_check_mark:| :heavy_check_mark:| :x:| Keine| 5 Stunden| Keine| –| 3 Monate |

### <a name="assign-eligibility-for-privileged-access-groups"></a>Zuweisen der Berechtigung für Gruppen mit privilegiertem Zugriff

Sie können [Mitgliedern oder Besitzern der Gruppen mit privilegiertem Zugriff die Berechtigung zuweisen](groups-assign-member-owner.md). Mit nur einer Aktivierung haben sie Zugriff auf alle verlinkten Ressourcen. 

>[!NOTE] 
>Sie können die Gruppe mit privilegiertem Zugriff einer oder mehreren Azure AD-Rollen und Azure-Ressourcenrollen auf die gleiche Weise zuweisen, wie Sie auch Benutzern Rollen zuweisen. In einer einzigen Azure AD-Organisation (Mandant) können maximal 250 Gruppen erstellt werden, denen Rollen zugewiesen werden können.

![Zuweisen der Berechtigung für Gruppen mit privilegiertem Zugriff](media/pim-deployment-plan/privileged-access-groups.png)


Wenn sich der Ablaufzeitpunkt der Zuweisung einer Gruppe mit privilegiertem Zugriff nähert, verwenden Sie [PIM, um die Gruppenzuweisung zu verlängern oder zu erneuern](groups-renew-extend.md). Sie benötigen eine Genehmigung des Gruppenbesitzers.

### <a name="approve-or-deny-pim-activation-request"></a>Genehmigen oder Verweigern von PIM-Aktivierungsanforderungen

Konfigurieren Sie Mitglieder und Besitzer von Gruppen mit privilegiertem Zugriff so, dass eine Genehmigung für die Aktivierung erforderlich ist, und wählen Sie Benutzer oder Gruppen aus Ihrer Azure AD-Organisation als delegierte genehmigende Personen aus. Es wird empfohlen, mindestens zwei genehmigende Personen für jede Gruppe auszuwählen, um die Arbeitsauslastung des Administrators für privilegierte Rollen zu verringern. 

[Genehmigen Sie Rollenaktivierungsanforderungen für Gruppen mit privilegiertem Zugriff, oder lehnen Sie diese ab](groups-approval-workflow.md). Als delegierte genehmigende Person erhalten Sie eine E-Mail-Benachrichtigung, wenn Ihre Genehmigung einer Anforderung aussteht.

### <a name="view-audit-history-for-privileged-access-groups"></a>Anzeigen des Überwachungsverlaufs für Gruppen mit privilegiertem Zugriff

[Zeigen Sie den Überwachungsverlauf für alle Zuweisungen und Aktivierungen](groups-audit.md) innerhalb der letzten 30 Tage für Gruppen mit privilegiertem Zugriff an.

## <a name="next-steps"></a>Nächste Schritte

* Wenn PIM-bezogene Probleme vorhanden sind, finden Sie weitere Informationen unter [Problembehandlung bei PIM](pim-troubleshoot.md).

* [Bereitstellen anderer Identitätsfeatures](../fundamentals/active-directory-deployment-plans.md)

 

