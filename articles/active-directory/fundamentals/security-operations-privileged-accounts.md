---
title: Azure Active Directory-Sicherheitsvorgänge für privilegierte Konten
description: Hier erfahren Sie, wie Sie Baselines festlegen und dann potenzielle Sicherheitsprobleme bei privilegierten Konten in Azure Active Directory überwachen und davor warnen.
services: active-directory
author: BarbaraSelden
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: a10d5f1e21f741382ad0d82c06c3f60b19e2c1c4
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132519323"
---
# <a name="security-operations-for-privileged-accounts"></a>Sicherheitsvorgänge für privilegierte Konten

Die Sicherheit der Geschäftsressourcen hängt von der Integrität der privilegierten Konten ab, mit denen Ihre IT-Systeme verwaltet werden. Durch den Diebstahl von Anmeldeinformationen und andere Angriffe versuchen Cyberkriminelle, auf privilegierte Konten und vertrauliche Daten zuzugreifen.

Bisher haben sich Unternehmen bezüglich der Organisationssicherheit auf die Ein- und Ausstiegspunkte eines Netzwerks als Sicherheitsperimeter konzentriert. Durch die Verwendung von Software-as-a-Service-Anwendungen (SaaS-Anwendungen) und persönlichen Geräten im Internet hat dieser Ansatz jedoch an Wirksamkeit verloren. 

Azure Active Directory (Azure AD) verwendet die Identitäts- und Zugriffsverwaltung (Identity & Access Management, IAM) als Steuerungsebene. Auf der Identitätsebene Ihrer Organisation haben Benutzer, denen privilegierte Administratorrollen zugewiesen sind, die Kontrolle. Die für den Zugriff verwendeten Konten müssen geschützt werden, unabhängig davon, ob es sich um eine lokale, eine Cloud- oder eine Hybridumgebung handelt.

Sie sind in vollem Umfang für alle Sicherheitsebenen Ihrer lokalen IT-Umgebung verantwortlich. Wenn Sie Azure-Dienste verwenden, sind Microsoft als Clouddienstanbieter und Sie als Kunde für Prävention und Reaktion gemeinsam verantwortlich.

* Weitere Informationen zum Modell der gemeinsamen Verantwortung finden Sie unter [Gemeinsame Verantwortung in der Cloud](../../security/fundamentals/shared-responsibility.md).

* Weitere Informationen zum Schützen des Zugriffs für privilegierte Benutzer finden Sie unter [Schützen des privilegierten Zugriffs für hybride und Cloudbereitstellungen in Azure AD](../roles/security-planning.md).

* Eine Vielzahl von Videos, Schrittanleitungen und Inhalten wichtiger Konzepte für privilegierte Identitäten finden Sie in der [Privileged Identity Management-Dokumentation](../privileged-identity-management/index.yml).

## <a name="where-to-look"></a>Zu verwendende Ressourcen

Zur Untersuchung und Überwachung verwenden Sie die folgenden Protokolldateien: 

* [Azure AD-Überwachungsprotokolle](../reports-monitoring/concept-audit-logs.md)

* [Microsoft 365-Überwachungsprotokolle](/microsoft-365/compliance/auditing-solutions-overview) 

* [Azure Key Vault Insights](../../azure-monitor/insights/key-vault-insights-overview.md)

Im Azure-Portal können Sie die Azure AD-Überwachungsprotokolle anzeigen und als CSV- oder JSON-Dateien (Comma-Separated Value, JavaScript Object Notation) herunterladen. Das Azure-Portal bietet mehrere Möglichkeiten zur Integration von Azure AD-Protokollen in andere Tools, die eine umfassendere Automatisierung von Überwachung und Warnmeldungen ermöglichen:

* [Microsoft Sentinel](../../sentinel/overview.md) ermöglicht intelligente Sicherheitsanalysen auf Unternehmensebene, indem SIEM-Funktionen (Security Information and Event Management) zur Verfügung gestellt werden. 

* [Azure Monitor](../../azure-monitor/overview.md): Ermöglicht die automatisierte Überwachung von und Warnung vor verschiedenen Bedingungen. Damit können Arbeitsmappen erstellt oder verwendet werden, um Daten aus verschiedenen Quellen zu kombinieren.

* [Azure Event Hubs](../../event-hubs/event-hubs-about.md) integriert in ein SIEM-System: [Azure AD-Protokolle können über die Azure Event Hub-Integration in andere SIEM-Systeme](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) wie Splunk, ArcSight, QRadar und Sumo Logic übertragen werden.

* [Microsoft Defender für Cloud Apps](/cloud-app-security/what-is-cloud-app-security) ermöglichen Ihnen die Erkennung und Verwaltung von Apps, die Steuerung von Apps und Ressourcen sowie die Überprüfung der Compliance Ihrer Cloud-Apps. 

* Microsoft Graph: Sie können die Daten exportieren und mit Microsoft Graph weitere Analysen durchführen. Weitere Informationen zu Microsoft Graph finden Sie unter [Azure Active Directory Identity Protection und das Microsoft Graph PowerShell SDK](../identity-protection/howto-identity-protection-graph-api.md). 

* [Identity Protection](../identity-protection/overview-identity-protection.md): Generiert drei wichtige Berichte, die Sie bei der Untersuchung verwenden können:

   * Riskante Benutzer: Enthält Informationen darüber, welche Benutzer gefährdet sind, Details zu Erkennungen, den Verlauf aller riskante Anmeldungen und den Risikoverlauf.

   * Riskante Anmeldungen: Enthält Informationen zu den Umständen einer Anmeldung, die auf verdächtige Situationen hinweisen können. Weitere Informationen zum Untersuchen von Informationen aus diesem Bericht finden Sie unter [Anleitung: Untersuchen eines Risikos](../identity-protection/howto-identity-protection-investigate-risk.md). 

   * Risikoerkennungen enthalten Informationen zu anderen Risiken, die bei Erkennung eines Risikos ausgelöst werden, sowie andere relevante Informationen wie den Anmeldeort und Details von Microsoft Defender für Cloud Apps.

 

Privilegierte Konten können über ständige Administratorrechte verfügen, wovon wir jedoch abraten. Wenn Sie sich für die Verwendung von ständigen Berechtigungen entscheiden und das Konto kompromittiert wird, kann dies sehr negative Auswirkungen haben. Wir empfehlen Ihnen, die Überwachung privilegierter Konten zu priorisieren und die Konten in Ihre Konfiguration von Privileged Identity Management (PIM) einzuschließen. Weitere Informationen zu PIM finden Sie unter [Einstieg in Privileged Identity Management](../privileged-identity-management/pim-getting-started.md). Darüber hinaus empfehlen wir Ihnen, Folgendes zu überprüfen/sicherzustellen:

* Die Administratorkonten sind erforderlich.

* Den Administratorkonten sind die geringsten Berechtigungen zum Ausführen der erforderlichen Aktivitäten zugewiesen.

* Die Administratorkonten sind mindestens mit MFA geschützt.

* Die Administratorkonten werden auf Privileged Access Workstations (PAWs) oder Secure Admin Workstations (SAWs) ausgeführt. 

Im restlichen Teil dieses Artikels wird beschrieben, welche Ereignisse Sie überwachen und wofür Sie Warnungen erstellen sollten, wobei die Inhalte nach der Art der Bedrohung organisiert sind. Wenn es spezielle vorgefertigte Lösungen gibt, sind diese im Anschluss an die Tabelle verlinkt. Andernfalls können Sie Warnungen mithilfe der oben genannten Tools erstellen. Dieser Artikel enthält insbesondere Details zum Festlegen von Baselines, zum Überwachen der Anmeldung mit und der Verwendung von privilegierten Konten sowie zu Tools und Ressourcen, mit denen Sie die Integrität Ihrer privilegierten Konten gewährleisten können. Der Inhalt ist in die folgenden Themenbereiche unterteilt:

* Konten für den Notfallzugriff/Notfallkonten 

* Anmeldung mit privilegiertem Konto

* Änderungen von privilegierten Konten

* Privilegierte Gruppen

* Rechtezuweisung und Rechteerweiterungen

## <a name="emergency-access-accounts"></a>Konten für den Notfallzugriff

Es ist wichtig, dass Sie verhindern, versehentlich aus Ihrem Azure Active Directory-Mandanten (Azure AD-Mandanten) ausgesperrt zu werden. Sie können die Auswirkungen eines versehentlichen Aussperrens abmildern, indem Sie in Ihrer Organisation Konten für den Notfallzugriff erstellen. Konten für den Notfallzugriff werden auch als „Notfallkonten“ bezeichnet – engl. „break-glass accounts“ in Anlehnung an die Meldungen „break glass in case of emergency“, die auf physischen Sicherheitsvorrichtungen wie Feueralarmmeldern zu finden sind.

Konten für den Notfallzugriff verfügen über umfangreiche Rechte und werden keinen Einzelpersonen zugewiesen. Konten für den Notfallzugriff sind auf Notfallsituationen oder Szenarien beschränkt, in denen normale privilegierte Konten nicht verwendet werden können, beispielsweise wenn eine Richtlinie für bedingten Zugriff falsch konfiguriert ist und alle normalen Administratorkonten sperrt. Verwenden Sie Notfallkonten nur in Fällen, in denen dies unbedingt erforderlich ist.

Lesen Sie auch unseren Artikel [Schützen des privilegierten Zugriffs für hybride und Cloudbereitstellungen in Azure AD](../roles/security-planning.md) mit der Anleitung, was bei einem Notfall zu tun ist.

Senden Sie bei jeder Verwendung eines Kontos für den Notfallzugriff eine Warnung mit hoher Priorität.

### <a name="discovery"></a>Ermittlung

Da Notfallkonten nur bei einem Notfall verwendet werden, sollte Ihre Überwachung keine Kontoaktivitäten ermitteln. Senden Sie bei jeder Verwendung oder Änderung eines Kontos für den Notfallzugriff eine Warnung mit hoher Priorität.  Jedes der folgenden Ereignisse kann darauf hindeuten, dass ein böswilliger Akteur versucht, Ihre Umgebungen zu gefährden.

* Verwendetes Konto: Überwachung von und Warnung bei allen Aktivitäten, die mit dieser Art von Konto ausgeführt werden, einschließlich:

* Anmelden

* Änderung des Kontokennworts 

* Änderung von Kontoberechtigungen/-rollen 

* Hinzugefügte oder geänderte Anmeldeinformationen oder Authentifizierungsmethode 

Weitere Informationen zum Verwalten dieser Konten finden Sie unter [Verwalten von Konten für den Notfallzugriff in Azure AD](../roles/security-emergency-access.md). Ausführliche Informationen zum Erstellen einer Warnung für ein Notfallkonto finden Sie unter [Erstellen einer Warnungsregel](../roles/security-emergency-access.md). 

## <a name="privileged-account-sign-in"></a>Anmeldung mit privilegiertem Konto

Überwachen Sie alle Anmeldeaktivitäten von privilegierten Konten mithilfe der Azure AD-Anmeldeprotokolle als Datenquelle. Zusätzlich zu den Informationen zu erfolgreichen und fehlerhaften Anmeldungen enthalten die Protokolle die folgenden Details:

* Interrupts
* Gerät
* Standort
* Risiko
* Application
* Datum und Uhrzeit
* Ob das Konto deaktiviert ist
* Sperrung
* MFA-Betrug
* Fehler bei bedingtem Zugriff

### <a name="things-to-monitor"></a>Zu überwachende Ereignisse

Sie können Anmeldeereignisse privilegierter Konten in den Azure AD-Anmeldeprotokollen überwachen. Erstellen Sie eine Warnung für und untersuchen Sie die folgenden Ereignisse für privilegierte Konten.

| Zu überwachende Elemente | Risikostufe |  Hierbei gilt: |  Filter/Unterfilter | Notizen |
| - | - | - | - | - |
| Anmeldefehler, Schwellenwert für falsches Kennwort | Hoch | Azure AD-Anmeldeprotokoll | Status = Fehler<br>- und -<br>Fehlercode = 50126 | Legen Sie einen Basisschwellenwert fest, den Sie dann überwachen und entsprechend dem Verhalten Ihrer Organisation anpassen können, um die Generierung falscher Warnungen zu minimieren. |
| Fehler aufgrund einer Anforderung des bedingten Zugriffs |Hoch | Azure AD-Anmeldeprotokoll | Status = Fehler<br>- und -<br>Fehlercode = 53003<br>- und -<br>Fehlergrund = blockiert durch bedingten Zugriff | Dies kann ein Hinweis darauf sein, dass ein Angreifer versucht, auf das Konto zuzugreifen. |
| Privilegierte Konten, die nicht der Benennungsrichtlinie entsprechen.| | Azure-Abonnement | [Auflisten von Azure-Rollenzuweisungen mithilfe des Azure-Portals – Azure RBAC](../../role-based-access-control/role-assignments-list-portal.md)| Listen Sie Rollenzuweisungen für Abonnements auf, und geben Sie eine Warnung aus, wenn der Anmeldename nicht dem Format Ihrer Organisation entspricht. Beispielsweise „ADM_“ als Präfix. |
| Interrupt |  Hoch/Mittel | Azure AD-Anmeldungen | Status = Unterbrochen<br>- und -<br>Fehlercode = 50074<br>- und -<br>Fehlergrund = Strenge Authentifizierung erforderlich<br>Status = Unterbrochen<br>- und -<br>Fehlercode = 500121<br>Fehlergrund = Fehler bei der Authentifizierung während der Anforderung einer strengen Authentifizierung | Dies kann ein Hinweis darauf sein, dass ein Angreifer über das Kennwort für das Konto verfügt, aber die MFA-Abfrage nicht erfolgreich abschließen kann. | 
| Privilegierte Konten, die nicht der Benennungsrichtlinie entsprechen.| Hoch | Azure AD-Verzeichnis | [Auflisten von Azure AD-Rollenzuweisungen](../roles/view-assignments.md)| Listen Sie Rollenzuweisungen für Azure AD-Rollen auf, und geben Sie eine Warnung aus, wenn der UPN nicht dem Format Ihrer Organisation entspricht. Beispielsweise „ADM_“ als Präfix. |
| Entdecken Sie privilegierte Konten, die nicht für MFA registriert sind. | Hoch | Microsoft Graph-API| Abfrage von „isMfaRegistered“ ist für Administratorkonten „false“. [CredentialUserRegistrationDetails auflisten – Microsoft Graph-Betaversion](/graph/api/reportroot-list-credentialuserregistrationdetails?view=graph-rest-beta&preserve-view=true&tabs=http) | Überwachen und untersuchen Sie dies, um zu ermitteln, ob Absicht oder ein Versehen vorliegt. |
| Kontosperrung | Hoch | Azure AD-Anmeldeprotokoll | Status = Fehler<br>- und -<br>Fehlercode = 50053 | Legen Sie einen Basisschwellenwert fest, den Sie dann überwachen und entsprechend dem Verhalten Ihrer Organisation anpassen können, um die Generierung falscher Warnungen zu minimieren. |
| Konto für Anmeldungen deaktiviert/blockiert | Niedrig | Azure AD-Anmeldeprotokoll | Status = Fehler<br>- und -<br>Ziel = Benutzer-UPN<br>- und -<br>Fehlercode = 50057 | Dies könnte darauf hindeuten, dass jemand versucht, sich Zugriff auf ein Konto zu verschaffen, nachdem er eine Organisation verlassen hat. Trotz der Blockierung des Kontos ist es dennoch wichtig, diese Aktivität zu protokollieren und eine entsprechende Warnung auszugeben. |
| MFA-Betrugswarnung/Blockierung | Hoch | Azure AD-Anmeldeprotokoll/Azure Log Anaylitics | Anmeldungen>Authentifizierungsdetails Ergebnisdetails = MFA verweigert, Betrugscode eingegeben | Ein privilegierter Benutzer hat angegeben, dass er die MFA-Eingabeaufforderung nicht initiiert hat, was darauf hindeuten könnte, dass ein Angreifer über das Kennwort für das Konto verfügt. |
| MFA-Betrugswarnung/Blockierung | Hoch | Azure AD Überwachungsprotokoll/Azure Log Anaylitics | Aktivitätstyp = Betrug gemeldet – Benutzer für MFA gesperrt oder Betrug gemeldet – keine Aktion ausgeführt (auf Basis der Einstellungen für Betrugsberichte auf Mandantenebene) | Ein privilegierter Benutzer hat angegeben, dass er die MFA-Eingabeaufforderung nicht initiiert hat, was darauf hindeuten könnte, dass ein Angreifer über das Kennwort für das Konto verfügt. |
| Anmeldungen mit privilegierten Konten außerhalb der erwarteten Kontrollen. |  | Azure AD-Anmeldeprotokoll | Status = Fehler<br>UserPricipalName = \<Admin account\><br>Standort = \<unapproved location\><br>IP-Adresse = \<unapproved IP\><br>Geräteinformationen = \<unapproved Browser, Operating System\> | Überwachen Sie Einträge, die Sie als nicht genehmigt definiert haben, und geben Sie Warnungen dafür aus. |
| Außerhalb der normalen Anmeldezeiten | Hoch | Azure AD-Anmeldeprotokoll | Status = Erfolg<br>- und -<br>Standort =<br>- und -<br>Zeit = außerhalb der Arbeitszeiten | Überwachen und warnen Sie, wenn Anmeldungen außerhalb der erwarteten Zeiten erfolgen. Es ist wichtig, das normale Arbeitsmuster für jedes privilegierte Konto zu ermitteln und eine Warnung auszugeben, wenn ungeplante Änderungen außerhalb der normalen Arbeitszeiten vorgenommen werden. Anmeldungen außerhalb der normalen Arbeitszeiten können auf eine Gefährdung oder auf mögliche Insiderbedrohungen hinweisen. | 
| Identitätsschutzrisiken | Hoch | Identity Protection-Protokolle | Risikozustand = gefährdet<br>- und -<br>Risikostufe = niedrig/mittel/hoch<br>- und -<br>Aktivität = Unbekannte Anmeldung/TOR usw. | Dies deutet darauf hin, dass bei der Anmeldung für das Konto eine gewisse Anomalie erkannt wurde und eine entsprechende Warnung ausgegeben werden sollte. | 
| Kennwortänderung | Hoch | Azure AD-Überwachungsprotokolle | Aktivität, Akteur = Administrator/Self-Service<br>- und -<br>Ziel = Benutzer<br>- und -<br>Status = Erfolg/Fehler | Geben Sie Warnungen bei Änderungen von Administratorkontenkennwörtern aus. Dies gilt insbesondere für Konten von globalen Administratoren, Benutzeradministratoren und Abonnementadministratoren sowie für Konten für den Notfallzugriff. Schreiben Sie eine Abfrage für alle privilegierten Konten. | 
| Änderung des Legacyauthentifizierungsprotokolls | Hoch | Azure AD-Anmeldeprotokoll | Client-App = Anderer Client, IMAP, POP3, MAPI, SMTP usw.<br>- und -<br>Benutzername = UPN<br>- und -<br>Anwendung = Exchange (Beispiel) | Bei vielen Angriffen wird die Legacyauthentifizierung verwendet. Daher kann eine Änderung des Authentifizierungsprotokolls für den Benutzer ein Hinweis auf einen Angriff sein. |
| Neues Gerät oder neuer Standort | Hoch | Azure AD-Anmeldeprotokoll | Geräteinformationen = Geräte-ID<br>- und -<br>Browser<br>- und -<br>Betriebssystem<br>- und -<br>Kompatibel/verwaltet<br>- und -<br>Ziel = Benutzer<br>- und -<br>Standort | Die meisten Administratoraktivitäten sollten auf Geräten mit [privilegiertem Zugriff](/security/compass/privileged-access-devices) ausgeführt werden und von einer begrenzten Anzahl von Standorten aus erfolgen. Geben Sie daher Warnungen bei neuen Geräten oder Standorten aus. |
| Die Einstellung für Überwachungswarnungen wurde geändert. | Hoch | Azure AD-Überwachungsprotokolle | Dienst = PIM<br>- und -<br>Kategorie = Role Management (Rollenverwaltung)<br>- und -<br>Aktivität = PIM-Warnung deaktivieren<br>- und -<br>Status = Erfolg | Bei unerwarteten Änderungen an einer wichtigen Warnung sollte eine Warnmeldung ausgegeben werden. |


## <a name="changes-by-privileged-accounts"></a>Änderungen durch privilegierte Konten 

Überwachen Sie alle abgeschlossenen und versuchten Änderungen durch ein privilegiertes Konto. Dadurch können Sie festlegen, welche Aktivitäten für jedes privilegierte Konto normal sind, und Warnungen bei Aktivitäten ausgeben, die vom erwarteten Muster abweichen. Die Azure AD-Überwachungsprotokolle werden zum Aufzeichnen dieses Ereignistyps verwendet. Weitere Informationen zu Azure AD-Überwachungsprotokollen finden Sie unter [Überwachungsprotokolle in Azure Active Directory](../reports-monitoring/concept-audit-logs.md). 

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

Privilegierte Konten, denen Berechtigungen in Azure Active Directory Domain Services zugewiesen wurden, können Aufgaben für Azure AD Domain Services ausführen, die sich auf den Sicherheitsstatus Ihrer von Azure gehosteten virtuellen Computer (Virtual Machines, VMs) auswirken, die Azure AD DS verwenden. Aktivieren Sie Sicherheitsüberwachungen auf VMs, und überwachen Sie die Protokolle. Weitere Informationen zum Aktivieren von Azure AD DS-Überwachungen und eine Liste der als sensibel geltenden Rechte finden Sie in den folgenden Ressourcen.

* [Aktivieren von Sicherheitsüberwachungen für Azure AD Domain Services](../../active-directory-domain-services/security-audit-events.md)

* [Sensible Verwendung von Rechten überwachen](/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)

| Zu überwachende Elemente                                                         | Risikostufe | Hierbei gilt:               | Filter/Unterfilter                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | Notizen                                                                                                                                                                                                                                                                                                                                                                                                                             |
|-------------------------------------------------------------------------|------------|---------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Versuchte und abgeschlossene Änderungen                                  | Hoch       | Azure AD-Überwachungsprotokolle | Datum und Uhrzeit<br>- und -<br>Dienst<br>- und -<br>Kategorie und Name der Aktivität (Was)<br>- und -<br>Status = Erfolg oder Fehler<br>- und -<br>Ziel<br>- und -<br>Initiator/Akteur (Entität)                                                                                                                                                                                                                                                                                                | Für alle ungeplanten Änderungen sollten sofort Warnungen ausgegeben werden. Diese Protokolle sollten aufbewahrt werden, damit sie bei Untersuchungen herangezogen werden können. Alle Änderungen auf Mandantenebene sollten sofort untersucht werden (Link zum Infrastrukturdokument). Änderungen, die den Sicherheitsstatus Ihres Mandanten verringern würden. Beispiel: Ausschließen von Konten von MFA oder bedingtem Zugriff. Geben Sie Warnungen bei [Ergänzungen zu oder Änderungen an Anwendungen](security-operations-applications.md) aus. |
| **BEISPIEL**<br>Versuchte oder abgeschlossene Änderung an wertvollen Apps oder Diensten | Hoch       | Überwachungsprotokoll           | Dienst<br>- und -<br>Kategorie und Name der Aktivität                                                                                                                                                                                                                                                                                                                                                                                                                                | <li>Datum und Uhrzeit <li>Dienst <li>Kategorie und Name der Aktivität <li>Status = Erfolg oder Fehler <li>Ziel <li>Initiator/Akteur (Entität)                                                                                                                                                                                                                                                                                        |
| Privilegierte Änderungen in Azure AD DS                                             | Hoch       | AD DS               | Suchen nach dem Ereignis [4673](/windows/security/threat-protection/auditing/event-4673) | [Aktivieren von Sicherheitsüberwachungen für Azure AD Domain Services](../../active-directory-domain-services/security-audit-events.md)<br>[Sensible Verwendung von Rechten überwachen](/windows/security/threat-protection/auditing/audit-sensitive-privilege-use): In diesem Artikel finden Sie eine Liste aller privilegierten Ereignisse.                                                                                                                            |


## <a name="changes-to-privileged-accounts"></a>Änderungen an privilegierten Konten

Untersuchen Sie Änderungen an den Authentifizierungsregeln und -berechtigungen privilegierter Konten, insbesondere dann, wenn durch die Änderung mehr Berechtigungen eingeräumt werden oder Aufgaben in Ihrer Azure AD-Umgebung ausgeführt werden dürfen. 

| Zu überwachende Elemente| Risikostufe| Hierbei gilt:| Filter/Unterfilter| Notizen |
| - | - | - | - | - |
| Erstellung privilegierter Konten.| Medium| Azure AD-Überwachungsprotokolle| Dienst = Azure AD-Kerndienst<br>- und -<br>Kategorie = Benutzerverwaltung<br>- und -<br>Aktivitätstyp = Benutzer hinzufügen<br>– Korrelation mit –<br>Kategorietyp = Rollenverwaltung<br>- und -<br>Aktivitätstyp = Mitglied zu Rolle hinzufügen<br>- und -<br>Geänderte Eigenschaften = Role.DisplayName| Überwachen Sie die Erstellung privilegierter Konten. Achten Sie auf eine Korrelation einer kurzen Zeitspanne zwischen der Erstellung und Löschung von Konten. |
| Änderungen an Authentifizierungsmethoden.| Hoch| Azure AD-Überwachungsprotokolle| Dienst = Authentifizierungsmethode<br>- und -<br>Aktivitätstyp = Vom Benutzer registrierte Sicherheitsinformationen<br>- und -<br>Kategorie = Benutzerverwaltung| Dies könnte ein Hinweis darauf sein, dass ein Angreifer dem Konto eine Authentifizierungsmethode hinzugefügt hat, um weiterhin Zugriff zu haben. |
| Warnung bei Änderungen an Berechtigungen privilegierter Konten.| Hoch| Azure AD-Überwachungsprotokolle| Kategorie = Role Management (Rollenverwaltung)<br>- und -<br>Aktivitätstyp – Add eligible member (permanent) (Berechtigtes Mitglied hinzufügen (dauerhaft))<br>- und -<br>Aktivitätstyp – Add eligible member (eligible) (Berechtigtes Mitglied hinzufügen (berechtigt))<br>- und -<br>Status = Erfolg/Fehler<br>- und -<br>Geänderte Eigenschaften = Role.DisplayName| Dies gilt insbesondere für Konten, denen Rollen zugewiesen werden, die nicht bekannt sind oder außerhalb ihrer normalen Zuständigkeiten liegen. |
| Nicht verwendete privilegierte Konten.| Medium| Azure AD-Zugriffsüberprüfungen| | Führen Sie für inaktive privilegierte Benutzerkonten eine monatliche Überprüfung durch. |
| Konten, die von Richtlinien für bedingten Zugriff ausgenommen sind| Hoch| Azure Monitor-Protokolle<br>Oder<br>Zugriffsüberprüfungen| Bedingter Zugriff: Erkenntnisse und Berichterstellung| Alle Konten, die von Richtlinien für bedingten Zugriff ausgenommen sind, umgehen höchstwahrscheinlich Sicherheitskontrollen und sind anfälliger für Kompromittierung. Notfallkonten sind davon ausgenommen. Informationen zum Überwachen von Notfallkonten finden Sie in einem nachfolgenden Abschnitt dieses Artikels.|


Weitere Informationen zum Überwachen von Ausnahmen von Richtlinien für bedingten Zugriff finden Sie unter [Erkenntnisse und Berichterstellung zum bedingten Zugriff](../conditional-access/howto-conditional-access-insights-reporting.md).

Weitere Informationen zum Ermitteln nicht verwendeter privilegierter Konten finden Sie unter [Erstellen einer Zugriffsüberprüfung für Azure AD-Rollen in Privileged Identity Management](../privileged-identity-management/pim-create-azure-ad-roles-and-resource-roles-review.md).

 
## <a name="assignment-and-elevation"></a>Zuweisung und Rechteerweiterungen

Privilegierte Konten, die permanent über erhöhte Rechte verfügen, können die Angriffsfläche und das Risiko für Ihre Sicherheitsgrenzen vergrößern. Verwenden Sie stattdessen einen Just-in-Time-Zugriff mit einem Rechteerweiterungsverfahren. Mit dieser Art von System können Sie Berechtigungen für privilegierte Rollen zuweisen, und Administratoren können ihre Berechtigungen nur dann auf diese Rollen erweitern, wenn sie Aufgaben ausführen, für die diese Berechtigungen erforderlich sind. Mithilfe eines Rechteerweiterungsverfahrens können Sie Rechteerweiterungen und die Nichtverwendung privilegierter Konten überwachen. 

### <a name="establish-a-baseline"></a>Einrichten einer Baseline

Um Ausnahmen überwachen zu können, müssen Sie zunächst eine Baseline erstellen. Legen Sie Folgendes fest:

* Administratorkonten 

   * Ihre Strategie für privilegierte Konten

   * Verwendung von lokalen Konten zum Verwalten lokaler Ressourcen

   * Verwendung von cloudbasierten Konten zum Verwalten cloudbasierter Ressourcen

   * Ansatz zum Trennen und Überwachen von Administratorberechtigungen für lokale und cloudbasierte Ressourcen.

* Schutz privilegierter Rollen 

   * Schutzstrategie für Rollen mit Administratorberechtigungen

   * Organisationsrichtlinie für die Verwendung privilegierter Konten

   * Strategie und Prinzipien für die Beibehaltung dauerhafter Berechtigungen im Vergleich zur Bereitstellung von zeitgebundenem und genehmigtem Zugriff

 

Die folgenden Konzepte und Informationen unterstützen Sie bei der Festlegung von Richtlinien.

* Just-in-Time-Verwaltungsprinzipien: Verwenden Sie die Azure AD-Protokolle, um Informationen zum Ausführen von administrativen Aufgaben zu erfassen, die in Ihrer Umgebung üblich sind. Bestimmen Sie den typischen erforderlichen Zeitaufwand für die Ausführung der Aufgaben. 

* Just-Enough-Verwaltungsprinzipien: [Bestimmen Sie die am wenigsten privilegierte Rolle](../roles/delegate-by-task.md), die für administrative Aufgaben erforderlich ist. Dabei kann es sich um eine benutzerdefinierte Rolle handeln. 

* Erstellen Sie eine Richtlinie für Rechteerweiterungen: Sobald Sie wissen, welche Art von erhöhten Rechten erforderlich sind und wie viel Zeit für jede Aufgabe benötigt wird, können Sie Richtlinien erstellen, die die Verwendung von erhöhten Rechten in Ihrer Umgebung widerspiegeln. Definieren Sie beispielsweise eine Richtlinie, die den Zugriff globaler Administratoren auf eine Stunde beschränkt.

 Nachdem Sie Ihre Baseline eingerichtet und die Richtlinie festgelegt haben, können Sie die Überwachung so konfigurieren, dass eine von der Richtlinie abweichende Verwendung erkannt und eine Warnung ausgegeben wird. 

### <a name="discovery"></a>Ermittlung

Es wird empfohlen, besonders auf Änderungen bei der Zuweisung und bei Rechteerweiterungen zu achten und diese zu untersuchen. 

### <a name="things-to-monitor"></a>Zu überwachende Ereignisse

Sie können Änderungen an privilegierten Konten mithilfe von Azure AD-Überwachungsprotokollen und Azure Monitor-Protokollen überwachen. Insbesondere empfiehlt es sich, Folgendes in Ihren Überwachungsprozess einzubeziehen. 

| Zu überwachende Elemente| Risikostufe| Hierbei gilt:| Filter/Unterfilter| Notizen |
| - | - | - | - | - |
| Der berechtigten privilegierten Rolle hinzugefügt.| Hoch| Azure AD-Überwachungsprotokolle| Dienst = PIM<br>- und -<br>Kategorie = Role Management (Rollenverwaltung)<br>- und -<br>Aktivitätstyp: Hinzufügen eines Mitglieds zur Rolle abgeschlossen (berechtigt)<br>- und -<br>Status = Erfolg/Fehler<br>- und -<br>Geänderte Eigenschaften = Role.DisplayName| Jedem Konto, das für eine Rolle berechtigt ist, wird jetzt privilegierter Zugriff gewährt. Wenn die Zuweisung unerwartet ist oder für eine Rolle erfolgt, die nicht in der Zuständigkeit des Kontoinhabers liegt, untersuchen Sie dies. |
| Außerhalb von PIM zugewiesene Rollen.| Hoch| Azure AD-Überwachungsprotokolle| Dienst = PIM<br>- und -<br>Kategorie = Role Management (Rollenverwaltung)<br>- und -<br>Aktivitätstyp = Mitglied zu Rolle hinzufügen (dauerhaft)<br>- und -<br>Status = Erfolg/Fehler<br>- und -<br>Geänderte Eigenschaften = Role.DisplayName| Diese sollten genau überwacht werden, und es sollte eine Warnung ausgegeben werden. Benutzern sollten nach Möglichkeit keine Rollen außerhalb von PIM zugewiesen werden. |
| Höhenangaben| Medium| Azure AD-Überwachungsprotokolle| Dienst = PIM<br>- und -<br>Kategorie = Role Management (Rollenverwaltung)<br>- und -<br>Aktivitätstyp: Hinzufügen eines Mitglieds zur Rolle abgeschlossen (PIM-Aktivierung)<br>- und -<br>Status = Erfolg/Fehler<br>- und -<br>Geänderte Eigenschaften = Role.DisplayName| Nach der Erhöhung der Rechte kann ein privilegiertes Konto Änderungen vornehmen, die sich auf die Sicherheit Ihres Mandanten auswirken können. Alle Rechteerweiterungen sollten protokolliert werden, und wenn sie außerhalb des Standardmusters für diesen Benutzer erfolgen, sollte eine Warnung ausgegeben werden, und sie sollten untersucht werden, falls dies nicht geplant ist. |
| Genehmigungen und Verweigern von Rechteerweiterungen| Niedrig| Azure AD-Überwachungsprotokolle| Dienst = Zugriffsüberprüfung<br>- und -<br>Kategorie = Benutzerverwaltung<br>- und -<br>Aktivitätstyp = Anforderung genehmigt/abgelehnt<br>- und -<br>Initiierender Akteur = UPN| Überwachen Sie alle Rechteerweiterungen, da dies einen eindeutigen Hinweis auf die Zeitachse für einen Angriff liefern könnte. |
| Änderungen an PIM-Einstellungen| Hoch| Azure AD-Überwachungsprotokolle| Dienst = PIM<br>- und -<br>Kategorie = Role Management (Rollenverwaltung)<br>- und -<br>Aktivitätstyp = Rolleneinstellung in PIM aktualisieren<br>- und -<br>Statusgrund = MFA bei Aktivierung deaktiviert (Beispiel)| Eine dieser Aktionen könnte die Sicherheit der PIM-Rechteerweiterungen verringern und Angreifern den Erwerb eines privilegierten Kontos erleichtern. |
| Keine Rechteerweiterungen auf SAW/PAW| Hoch| Azure AD-Anmeldeprotokolle| Geräte-ID<br>- und -<br>Browser<br>- und -<br>Betriebssystem<br>- und -<br>Kompatibel/verwaltet<br>Korrelation mit:<br>Dienst = PIM<br>- und -<br>Kategorie = Role Management (Rollenverwaltung)<br>- und -<br>Aktivitätstyp: Hinzufügen eines Mitglieds zur Rolle abgeschlossen (PIM-Aktivierung)<br>- und -<br>Status = Erfolg/Fehler<br>- und -<br>Geänderte Eigenschaften = Role.DisplayName| Wenn dies konfiguriert ist, sollte jeder Versuch, die Rechte auf einem Nicht-PAW-/Nicht-SAW-Gerät zu erhöhen, sofort untersucht werden, da dies auf einen Angreifer hinweisen könnte, der versucht, das Konto zu verwenden. |
| Rechteerweiterungen zum Verwalten aller Azure-Abonnements| Hoch| Azure Monitor| Registerkarte „Aktivitätsprotokoll“ <br>Registerkarte „Verzeichnisaktivität“ <br> Vorgangsname = Weist den Aufrufer der Rolle „Benutzerzugriffsadministrator“ zu <br> - und - <br> Ereigniskategorie = Administrativ <br> - und -<br>Status = Erfolgreich, Start, Fehler<br>- und -<br>Ereignis initiiert von| Dies sollte sofort untersucht werden, wenn es sich nicht um eine geplante Änderung handelt. Diese Einstellung könnte einem Angreifer Zugriff auf Azure-Abonnements in Ihrer Umgebung ermöglichen. |


Weitere Informationen zum Verwalten von Rechteerweiterungen finden Sie unter [Erhöhen der Zugriffsrechte zum Verwalten aller Azure-Abonnements und Verwaltungsgruppen](../../role-based-access-control/elevate-access-global-admin.md). Informationen zum Überwachen von Rechteerweiterungen mithilfe der in den Azure AD-Protokollen verfügbaren Daten finden Sie unter [Azure-Aktivitätsprotokoll](../../azure-monitor/essentials/activity-log.md) in der Azure Monitor-Dokumentation.

Informationen zum Konfigurieren von Warnungen für Azure-Rollen finden Sie unter [Konfigurieren von Sicherheitswarnungen für Azure-Ressourcenrollen in Privileged Identity Management](../privileged-identity-management/pim-resource-roles-configure-alerts.md). 

 ## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln im Leitfaden zu Sicherheitsvorgängen:

[Azure AD: Übersicht über Sicherheitsvorgänge](security-operations-introduction.md)

[Sicherheitsvorgänge für Benutzerkonten](security-operations-user-accounts.md)

[Sicherheitsvorgänge für privilegierte Konten](security-operations-privileged-accounts.md)

[Sicherheitsvorgänge für Privileged Identity Management](security-operations-privileged-identity-management.md)

[Sicherheitsvorgänge für Anwendungen](security-operations-applications.md)

[Sicherheitsvorgänge für Geräte](security-operations-devices.md)

 
[Sicherheitsvorgänge für die Infrastruktur](security-operations-infrastructure.md)
