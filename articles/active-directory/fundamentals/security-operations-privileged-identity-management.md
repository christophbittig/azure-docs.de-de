---
title: Azure Active Directory-Sicherheitsvorgänge für Privileged Identity Management
description: Leitfaden zum Einrichten von Baselines und Verwenden von Azure Active Directory Privileged Identity Management (PIM) zum Überwachen und Warnen bei potenziellen Problemen mit Konten, die mit PIM verwaltet werden
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ef4ca7858e50c43a2ed17656ecdd11c3affef6d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132300802"
---
# <a name="azure-active-directory-security-operations-for-privileged-identity-management-pim"></a>Azure Active Directory-Sicherheitsvorgänge für Privileged Identity Management (PIM)

Die Sicherheit der Geschäftsressourcen hängt von der Integrität der privilegierten Konten ab, mit denen Ihre IT-Systeme verwaltet werden. Cyberangreifer haben es beim Diebstahl von Anmeldeinformationen besonders auf Administratorkonten und andere privilegierte Zugriffskonten abgesehen, um Zugriff auf vertrauliche Daten zu erhalten.

Bei Clouddiensten sind Cloud-Dienstanbieter und Kunde gemeinsam verantwortlich für Prävention und Reaktion. 

Klassischerweise haben sich Unternehmen bezüglich der Sicherheit auf die Ein- und Ausstiegspunkte eines Netzwerks als Sicherheitsperimeter konzentriert. Mit SaaS-Apps und persönlichen Geräten hat dieser Ansatz jedoch an Wirksamkeit verloren. In Azure Active Directory (Azure AD) wird der Netzwerksicherheitsperimeter durch eine Authentifizierung in der Identitätsschicht Ihrer Organisation ersetzt. Da Benutzern privilegierte Administratorrollen erteilt werden, muss ihr Zugriff in lokalen, cloudbasierten und hybriden Umgebungen geschützt werden.

Sie sind für alle Sicherheitsebenen Ihrer lokalen IT-Umgebung verantwortlich. Wenn Sie Azure-Dienste verwenden, liegen die Vorbeugung und Reaktion in der gemeinsamen Verantwortung von Microsoft als Clouddienstanbieter und Ihnen als Kunde. 

* Weitere Informationen zum Modell der gemeinsamen Verantwortung finden Sie unter [Gemeinsame Verantwortung in der Cloud](../../security/fundamentals/shared-responsibility.md).

* Weitere Informationen zum Schützen des Zugriffs von privilegierten Benutzern finden Sie unter [Schützen des privilegierten Zugriffs für Hybrid- und Cloudbereitstellungen in Azure AD](../roles/security-planning.md).

* Eine Vielzahl von Videos, Schrittanleitungen und Ressourcen zu wichtigen Konzepten zu privilegierten Identitäten finden Sie in der [Dokumentation für Privileged Identity Management](../privileged-identity-management/index.yml). 

Privileged Identity Management (PIM) ist ein Azure AD-Dienst, mit dem Sie den Zugriff auf wichtige Ressourcen innerhalb Ihrer Organisation verwalten, steuern und überwachen können. Diese Ressourcen umfassen Ressourcen in Azure AD, Azure und anderen Microsoft-Onlinediensten wie Microsoft 365 oder Microsoft Intune. Mit PIM können Sie die folgenden Risiken minimieren:

* Identifizieren und Minimieren der Anzahl der Personen, die Zugriff auf sichere Informationen oder Ressourcen haben

* Erkennen übermäßiger, unnötiger oder missbräuchlich genutzter Zugriffsberechtigungen auf vertrauliche Ressourcen

* Verringern der Wahrscheinlichkeit, dass böswillige Akteure Zugriff auf gesicherte Informationen oder Ressourcen haben

* Verringern der Wahrscheinlichkeit, dass ein nicht autorisierter Benutzer versehentlich vertrauliche Ressourcen beeinträchtigen kann

Dieser Artikel ist ein Leitfaden zum Festlegen von Baselines und Überwachen der Anmeldung bei und Verwendung von privilegierten Konten. Außerdem ist die Quelle der Überwachungsprotokolle aufgeführt, mit denen Sie die Integrität Ihrer privilegierten Konten gewährleisten können. 

## <a name="where-to-look"></a>Zu verwendende Ressourcen

Zur Untersuchung und Überwachung verwenden Sie die folgenden Protokolldateien: 

* [Azure AD-Überwachungsprotokolle](../reports-monitoring/concept-audit-logs.md)

* [Anmeldeprotokolle](../reports-monitoring/concept-all-sign-ins.md)

* [Microsoft 365-Überwachungsprotokolle](/microsoft-365/compliance/auditing-solutions-overview) 

* [Azure Key Vault-Protokolle](../../key-vault/general/logging.md?tabs=Vault)

Im Azure-Portal können Sie die Azure AD-Überwachungsprotokolle anzeigen und als CSV- oder JSON-Dateien (Comma-Separated Value bzw. JavaScript Object Notation) herunterladen. Das Azure-Portal bietet mehrere Möglichkeiten zur Integration von Azure AD-Protokollen mit anderen Tools, die eine umfassendere Automatisierung von Überwachung und Warnmeldungen ermöglichen:

* [**Microsoft Sentinel**](../../sentinel/overview.md): Ermöglicht intelligente Sicherheitsanalysen auf Unternehmensebene, indem SIEM-Funktionen (Security Information and Event Management) zur Verfügung gestellt werden. 

* [**Azure Monitor**](../../azure-monitor/overview.md) : ermöglicht die automatisierte Überwachung verschiedener Bedingungen und entsprechende Warnungen. Damit können Arbeitsmappen erstellt oder verwendet werden, um Daten aus verschiedenen Quellen zu kombinieren.

* [**Azure Event Hubs**](../../event-hubs/event-hubs-about.md) **mit Integration mit einem SIEM-System:** - [ Azure AD-Protokolle können über die Azure Event Hubs-Integration in andere SIEM-Systeme](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) wie Splunk, ArcSight, QRadar und Sumo Logic integriert werden.

* [**Microsoft Defender für Cloud-Apps**](/cloud-app-security/what-is-cloud-app-security): Ermöglichen Ihnen die Erkennung und Verwaltung von Apps, die Steuerung von Apps und Ressourcen sowie die Überprüfung der Compliance Ihrer Cloud-Apps. 

Der übrige Teil dieses Artikels enthält Empfehlungen zum Festlegen einer Baseline, die mithilfe eines Ebenenmodells organisiert ist und auf deren Grundlage die Überwachung und Warnungen erfolgen. Links zu vorgefertigten Lösungen sind im Anschluss an die Tabelle aufgeführt. Sie können Warnungen auch mithilfe der oben genannten Tools erstellen. Der Inhalt ist in die folgenden PIM-Themenbereiche unterteilt:

* Basislinien

* Azure AD-Rollenzuweisung 

* Warnungseinstellungen für Azure AD-Rollen

* Rollenzuweisung zu Azure-Ressourcen

* Zugriffsverwaltung für Azure-Ressourcen

* Erhöhte Zugriffsrechte zum Verwalten von Azure-Abonnements

## <a name="baselines"></a>Basislinien

Im Folgenden sind empfohlene Baselineeinstellungen angegeben:

| Zu überwachende Elemente| Risikostufe| Empfehlung| Rollen| Notizen |
| - |- |- |- |- |
| Azure AD-Rollenzuweisung| Hoch| <li>Begründung für die Aktivierung erfordern<li>Genehmigung für die Aktivierung erfordern<li>Zweistufigen Genehmigungsprozess festlegen<li>Bei der Aktivierung Azure Active Directory Multi-Factor Authentication (MFA) erfordern<li>Maximale Dauer für die Rechteerweiterung auf 8 Stunden festlegen| <li>Privileged Role Administration (Verwaltung privilegierter Rollen)<li>Globaler Administrator| Ein Administrator für privilegierte Rollen kann PIM in seiner Azure AD-Organisation anpassen und hierbei auch die Art ändern, in der Benutzer eine berechtigte Rollenzuweisung aktivieren. |
| Konfiguration von Azure-Ressourcenrollen| Hoch| <li>Begründung für die Aktivierung erfordern<li>Genehmigung für die Aktivierung erfordern<li>Zweistufigen Genehmigungsprozess festlegen<li>Bei der Aktivierung Azure MFA erfordern<li>Maximale Dauer für die Rechteerweiterung auf 8 Stunden festlegen| <li>Besitzer<li>Resource Administrator (Ressourcenadministrator)<li>Benutzerzugriff <li>Administrator<li>Globaler Administrator<li>Sicherheitsadministrator| Wenn es sich um keine geplante Änderung handelt, sollten Sie sie sofort untersuchen. Diese Einstellung könnte einem Angreifer Zugriff auf Azure-Abonnements in Ihrer Umgebung ermöglichen. |


## <a name="azure-ad-roles-assignment"></a>Azure AD-Rollenzuweisung

Ein Administrator für privilegierte Rollen kann PIM in seiner Azure AD-Organisation anpassen. Er kann die Art, wie Benutzer eine berechtigte Rollenzuweisung aktivieren, z. B. wie folgt ändern:

* Verhindern, dass ein böswilliger Akteur Azure MFA-Anforderungen entfernt, um sich privilegierten Zugriff zu verschaffen

* Verhindern, dass ein böswilliger Akteur bei der Aktivierung des privilegierten Zugriffs die Begründung und Genehmigung umgeht

| Zu überwachende Elemente| Risikostufe| Hierbei gilt:| Filter/Unterfilter| Notizen |
| - |- |- |- |- |
| Warnung zu „Add changes to privileged account permissions“ (Änderungen an Berechtigungen für privilegierter Konten hinzufügen)| Hoch| Azure AD-Überwachungsprotokolle| Kategorie = Role Management (Rollenverwaltung)<br>- und -<br>Aktivitätstyp – Add eligible member (permanent) (Berechtigtes Mitglied hinzufügen (dauerhaft)) <br>- und -<br>Aktivitätstyp – Add eligible member (eligible) (Berechtigtes Mitglied hinzufügen (berechtigt)) <br>- und -<br>Status = Erfolg/Fehler<br>- und -<br>Geänderte Eigenschaften = Role.DisplayName| Überwachen Sie Änderungen an den Rollen „Administrator für privilegierte Rollen“ und „Globaler Administrator“, und richten Sie Warnungen für jegliche Änderungen ein. <li>Dies kann ein Hinweis darauf sein, dass ein Angreifer versucht, Berechtigungen zum Ändern von Rollenzuweisungseinstellungen zu erlangen.<li> Wenn Sie keinen Schwellenwert definieren, sollten Sie für Benutzer bei 4 Änderungen innerhalb von 60 Minuten und für privilegierte Konten bei 2 Änderungen innerhalb von 60 Minuten warnen. |
| Warnung bei Änderungen der Massenlöschung von Berechtigungen privilegierter Konten| Hoch| Azure AD-Überwachungsprotokolle| Kategorie = Role Management (Rollenverwaltung)<br>- und -<br>Aktivitätstyp – Remove eligible member (permanent) (Berechtigtes Mitglied entfernen (dauerhaft)) <br>- und -<br>Aktivitätstyp – Remove eligible member (eligible) (Berechtigtes Mitglied entfernen (berechtigt)) <br>- und -<br>Status = Erfolg/Fehler<br>- und -<br>Geänderte Eigenschaften = Role.DisplayName| Wenn es sich um keine geplante Änderung handelt, sollten Sie sie sofort untersuchen. Diese Einstellung könnte einem Angreifer Zugriff auf Azure-Abonnements in Ihrer Umgebung ermöglichen. |
| Änderungen an den PIM-Einstellungen| Hoch| Azure AD-Überwachungsprotokoll| Dienst = PIM<br>- und -<br>Kategorie = Role Management (Rollenverwaltung)<br>- und -<br>Aktivitätstyp = Rolleneinstellung in PIM aktualisieren<br>- und -<br>Statusursache = MFA on activation disabled (MFA bei Aktivierung deaktiviert) (Beispiel)| Überwachen Sie Änderungen an den Rollen „Administrator für privilegierte Rollen“ und „Globaler Administrator“, und richten Sie Warnungen für jegliche Änderungen ein. <li>Dies kann ein Hinweis darauf sein, dass ein Angreifer bereits Zugriff erhalten hat und die Einstellungen für Rollenzuweisungen ändern kann.<li>Eine dieser Aktionen könnte die Sicherheit der PIM-Rechteerweiterung verringern und Angreifern den Zugriff auf ein privilegiertes Konto erleichtern. |
| Rechteerweiterungen genehmigen und verweigern| Hoch| Azure AD-Überwachungsprotokoll| Dienst = Zugriffsüberprüfung<br>- und -<br>Kategorie = Benutzerverwaltung<br>- und -<br>Aktivitätstyp = Anforderung genehmigt/abgelehnt<br>- und -<br>Initiated actor (Initiierender Akteur) = UPN| Alle Rechteerweiterungen sollten überwacht werden. Protokollieren Sie alle Rechteerweiterungen. Dies sollte eine eindeutige Zeitskala eines Angriffs bieten. |
| Deaktivierung von Warnungseinstellungen| Hoch| Azure AD-Überwachungsprotokolle| Dienst = PIM<br>- und -<br>Kategorie = Role Management (Rollenverwaltung)<br>- und -<br>Aktivitätstyp = Disable PIM Alert (PIM-Warnung deaktivieren)<br>- und -<br>Status = Erfolg/Fehler| Lassen Sie immer warnen. <li>Hilft bei der Erkennung böswilliger Akteure, die Warnungen zu Azure MFA-Anforderungen entfernen, um sich privilegierten Zugriff zu verschaffen<li>Hilft bei der Erkennung verdächtiger oder unsicherer Aktivitäten |


Weitere Informationen zum Identifizieren von Änderungen an Rolleneinstellungen im Azure AD-Überwachungsprotokoll finden Sie unter [Anzeigen des Überwachungsverlaufs für Azure AD-Rollen in Privileged Identity Management](../privileged-identity-management/pim-how-to-use-audit-log.md). 

## <a name="azure-resource-role-assignment"></a>Rollenzuweisung zu Azure-Ressourcen

Die Überwachung von Azure-Ressourcenrollenzuweisungen bietet Einblick in Aktivitäten und Aktivierungen für Ressourcenrollen. Diese können missbraucht werden, um eine Angriffsfläche für eine Ressource zu schaffen. Wenn Sie solche Aktivitäten überwachen, versuchen Sie, Folgendes zu erkennen:

* Abfragen von Rollenzuweisungen für bestimmte Ressourcen

* Rollenzuweisungen für alle untergeordneten Ressourcen

* Alle Änderungen an aktiven und berechtigten Rollenzuweisungen

| Zu überwachende Elemente| Risikostufe| Hierbei gilt:| Filter/Unterfilter| Notizen |
| - |- |- |- |- |
| Überwachungswarnungen, Ressourcenüberwachungsprotokoll auf Aktivitäten für privilegierte Konten überprüfen| Hoch| In PIM, unter „Azure-Ressourcen“, „Ressourcenüberwachung“| Aktion: Hinzufügen von berechtigtem Mitglied zu Rolle in PIM abgeschlossen (zeitgebunden) <br>- und -<br>Primäres Ziel <br>- und -<br>Type User (Typbenutzer)<br>- und -<br>Status = Erfolgreich<br>| Lassen Sie immer warnen. Hilft bei der Erkennung eines böswilligen Akteurs, der berechtigte Rollen hinzufügt, um alle Ressourcen in Azure zu verwalten |
| Überwachungswarnungen, Ressourcenüberwachung für die Deaktivierung von Warnungen| Medium| In PIM, unter „Azure-Ressourcen“, „Ressourcenüberwachung“| Aktion: Warnung deaktivieren<br>- und -<br>Primäres Ziel: Zu viele Besitzer zu Ressource zugewiesen<br>- und -<br>Status = Erfolgreich| Hilft bei der Erkennung eines böswilligen Akteurs, der Warnungen im Warnungsbereich deaktiviert, womit die Untersuchung böswilliger Aktivitäten umgangen werden kann |
| Überwachungswarnungen, Ressourcenüberwachung für die Deaktivierung von Warnungen| Medium| In PIM, unter „Azure-Ressourcen“, „Ressourcenüberwachung“| Aktion: Warnung deaktivieren<br>- und -<br>Primäres Ziel: Zu viele dauerhafte Besitzer zu Ressource zugewiesen<br>- und -<br>Status = Erfolgreich| Hindern Sie böswillige Akteure daran, Warnungen im Warnungsbereich zu deaktivieren, womit die Untersuchung böswilliger Aktivitäten umgangen werden kann. |
| Überwachungswarnungen, Ressourcenüberwachung für die Deaktivierung von Warnungen| Medium| In PIM, unter „Azure-Ressourcen“, „Ressourcenüberwachung“| Aktion: Warnung deaktivieren<br>- und -<br>Primäres Ziel: Doppelte Rolle erstellt<br>- und -<br>Status = Erfolgreich| Hindern Sie böswillige Akteure daran, Warnungen im Warnungsbereich zu deaktivieren, womit die Untersuchung böswilliger Aktivitäten umgangen werden kann. |


Weitere Informationen zum Konfigurieren von Warnungen und Überwachen von Azure-Ressourcenrollen finden Sie unter:

* [Konfigurieren von Sicherheitswarnungen für Azure-Ressourcenrollen in Privileged Identity Management](../privileged-identity-management/pim-resource-roles-configure-alerts.md)

* [Anzeigen von Aktivitäten und des Überwachungsverlaufs für Azure-Ressourcenrollen in Privileged Identity Management](../privileged-identity-management/azure-pim-resource-rbac.md)

## <a name="access-management-for-azure-resources-and-subscriptions"></a>Zugriffsverwaltung für Azure-Ressourcen und -Abonnements

Benutzer oder Mitglieder von Gruppen, die der Abonnementsrolle „Besitzer“ oder „Benutzerzugriffsadministrator“ zugewiesen sind, sowie globale Azure AD-Administratoren, die die Abonnementverwaltung in Azure AD ermöglichen, haben standardmäßig Ressourcenadministratorberechtigungen. Diese Administratoren können Rollen zuweisen, Rolleneinstellungen konfigurieren und mit Privileged Identity Management (PIM) den Zugriff auf Azure-Ressourcen überprüfen. 

Ein Benutzer mit Ressourcenadministratorberechtigungen kann PIM für Ressourcen verwalten. Das damit einhergehende Risiko, das Sie überwachen und minimieren müssen, besteht darin, dass die Funktion böswilligen Akteuren privilegierten Zugriff auf Azure-Abonnementressourcen wie VMs oder Speicherkonten ermöglichen kann.

| Zu überwachende Elemente| Risikostufe| Hierbei gilt:| Filter/Unterfilter| Notizen |
| - |- |- |- |- |
| Höhenangaben| Hoch| Azure AD, unter „Verwalten“, „Eigenschaften“| Überprüfen Sie die Einstellung in regelmäßigen Abständen.<br>Zugriffsverwaltung für Azure-Ressourcen| Globale Administratoren können Rechte erhöhen, indem sie die Zugriffsverwaltung für Azure-Ressourcen aktivieren.<br>Vergewissern Sie sich, dass keine böswilligen Akteure über Berechtigungen für das Zuweisen von Rollen in allen Azure-Abonnements und -Verwaltungsgruppen verfügen, die mit Active Directory verknüpft sind. |


Weitere Informationen finden Sie unter [Zuweisen von Azure-Ressourcenrollen in Privileged Identity Management](../privileged-identity-management/pim-resource-roles-assign-roles.md).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in den folgenden Artikeln im Leitfaden zu Sicherheitsvorgängen:

[Azure AD: Übersicht über Sicherheitsvorgänge](security-operations-introduction.md)

[Sicherheitsvorgänge für Benutzerkonten](security-operations-user-accounts.md)

[Sicherheitsvorgänge für privilegierte Konten](security-operations-privileged-accounts.md)

[Sicherheitsvorgänge für Privileged Identity Management](security-operations-privileged-identity-management.md)

[Sicherheitsvorgänge für Anwendungen](security-operations-applications.md)

[Sicherheitsvorgänge für Geräte](security-operations-devices.md)
 
[Sicherheitsvorgänge für die Infrastruktur](security-operations-infrastructure.md)
