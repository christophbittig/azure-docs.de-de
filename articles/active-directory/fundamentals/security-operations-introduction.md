---
title: 'Azure Active Directory: Leitfaden zu Sicherheitsvorgängen'
description: Hier erfahren Sie, wie Sie Sicherheitsprobleme mit Konten, Anwendungen, Geräten und Infrastruktur überwachen und erkennen sowie entsprechende Warnungen ausgeben.
services: active-directory
author: BarbaraSelden
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e9921204a60f4364b2365139e2a8b07b4b8e7f0
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132300822"
---
# <a name="azure-active-directory-security-operations-guide"></a>Azure Active Directory: Leitfaden zu Sicherheitsvorgängen

Microsoft verfolgt einen erfolgreichen und bewährten Ansatz der [Zero-Trust-Sicherheit](https://aka.ms/Zero-Trust) unter Verwendung von Prinzipien der [tiefgehenden Verteidigung](https://us-cert.cisa.gov/bsi/articles/knowledge/principles/defense-in-depth), bei denen die Identität als Steuerungsebene dient. In dem Maße, in dem Organisationen sich aus Gründen der Skalierbarkeit, Kostensenkung und Sicherheit einer hybriden Workloadumgebung zuwenden, gewinnt Azure Active Directory (Azure AD) für Ihre Identitätsverwaltungsstrategie an Bedeutung. Nachrichten aus der letzten Zeit zu Angriffen auf Identität und Sicherheit haben IT-Abteilungen in Unternehmen zunehmend veranlasst, ihren Identitätssicherheitsstatus als Gradmesser ihres Erfolgs bei der defensiven Sicherheit zu überdenken.

Organisationen sehen sich immer mehr mit einer Kombination aus lokalen und Cloudanwendungen konfrontiert, auf die Benutzer sowohl mit lokalen als auch mit ausschließlich cloudbasierten Konten zugreifen können. Szenarien, in denen Benutzer, Anwendungen und Geräte sowohl lokal als auch in der Cloud verwaltet werden, stellen eine Herausforderung dar.

Azure Active Directory bildet eine gemeinsame Benutzeridentität für die Authentifizierung und Autorisierung bei allen Ressourcen, unabhängig vom Standort. Wir bezeichnen dies als Hybrididentität. 

Um Hybrididentität in Azure AD zu erreichen, kann je nach Szenario eine von drei Authentifizierungsmethoden verwendet werden. Die drei Methoden sind:

* [Kennworthashsynchronisierung (Password hash synchronization, PHS)](../hybrid/whatis-phs.md)

* [Passthrough-Authentifizierung (PTA)](../hybrid/how-to-connect-pta.md)

* [Verbund (AD FS)](../hybrid/whatis-fed.md)

Während Sie Ihre aktuellen Sicherheitsvorgänge überprüfen oder Sicherheitsvorgänge für Ihre Azure-Umgebung einrichten, berücksichtigen Sie unsere folgenden Empfehlungen:

* Lesen Sie bestimmte Teile des Microsoft-Sicherheitsleitfadens, um sich ein Grundwissen zur Sicherheit Ihrer cloudbasierten oder hybriden Azure-Umgebung anzueignen.

* Überprüfen Sie Ihre Konto- und Kennwortstrategie sowie Authentifizierungsmethoden, um die gängigsten Angriffsvektoren abzuschwächen.

* Erstellen Sie eine Strategie für die kontinuierliche Überwachung und Benachrichtigung bei Aktivitäten, die auf eine Sicherheitsbedrohung hindeuten können.

## <a name="audience"></a>Zielgruppe

Der Azure AD-SecOps-Leitfaden richtet sich an IT-Identitäts- und Sicherheitsbetriebsteams von Unternehmen sowie an Anbieter verwalteter Dienste, die sich durch eine bessere Identitätssicherheitskonfiguration und Überwachungsprofile vor Bedrohungen schützen müssen. Dieser Leitfaden ist besonders für IT-Administratoren und Identitätsarchitekten relevant, die Security Operations Center-Teams (SOC) bei defensiven und Penetrationstests beraten, um ihren Identitätssicherheitsstatus zu verbessern und aufrechtzuerhalten. 

## <a name="scope"></a>`Scope`

Diese Einführung enthält Empfehlungen für die vorbereitende Lektüre sowie zur Kennwortüberwachung und Strategie. Dieser Artikel bietet auch eine Übersicht über die Tools, die für Hybrid- und vollständig cloudbasierte Azure-Umgebungen verfügbar sind. Schließlich stellen wir eine Liste der Datenquellen zur Verfügung, die Sie für die Überwachung und Benachrichtigung sowie bei der Konfiguration Ihrer SIEM-Strategie und -Umgebung (Security Information and Event Management) verwenden können. Gegen Ende des Leitfadens werden Überwachungs- und Warnungsstrategien in den folgenden Bereichen vorgestellt:

* [Benutzerkonten:](security-operations-user-accounts.md) Empfehlungen speziell zu nicht privilegierten Benutzerkonten ohne Administratorrechte, einschließlich anomaler Kontoerstellung und -nutzung sowie ungewöhnlicher Anmeldungen.

* [Privilegierte Konten:](security-operations-privileged-accounts.md) Empfehlungen speziell zu privilegierten Benutzerkonten mit erhöhten Berechtigungen zum Ausführen administrativer Aufgaben, einschließlich Azure AD-Rollenzuweisungen, Azure-Ressourcenrollenzuweisungen und Zugriffsverwaltung für Azure-Ressourcen und -Abonnements.

* [Privileged Identity Management (PIM):](security-operations-privileged-identity-management.md) Empfehlungen speziell zur Verwendung von PIM zum Verwalten, Steuern und Überwachen des Zugriffs auf Ressourcen. 

* [Anwendungen:](security-operations-applications.md) Empfehlungen speziell zu Konten, die der Authentifizierung von Anwendungen dienen. 

* [Geräte:](security-operations-devices.md) Empfehlungen speziell zur Überwachung von Geräten, die nicht gemäß den Richtlinien registriert oder eingebunden wurden, von nicht konformer Nutzung, von Geräteverwaltungsrollen und von Anmeldungen bei virtuellen Computern sowie zu entsprechenden Benachrichtigungen.

*  [Infrastruktur:](security-operations-infrastructure.md) Empfehlungen speziell für die Überwachung und Benachrichtigung bei Bedrohungen hybrider oder rein cloudbasierter Umgebungen.

## <a name="important-reference-content"></a>Wichtige Referenzinhalte

Microsoft bietet viele Produkte und Dienste, mit denen Sie Ihre IT-Umgebung an Ihre Anforderungen anpassen können. Es wird empfohlen, im Rahmen Ihrer Überwachungs- und Warnungsstrategie die Leitfäden zu lesen, die für Ihre Betriebsumgebung jeweils relevant sind:

* Windows-Betriebssysteme

   * [Referenz zur Sicherheitsüberprüfung und -überwachung für Windows 10 und Windows Server 2016](https://www.microsoft.com/download/details.aspx?id=52630)

   * [Sicherheitsbaseline (endgültige Version) für Windows 10 v1909 und Windows Server v1909](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/security-baseline-final-for-windows-10-v1909-and-windows-server/ba-p/1023093)

   * [Sicherheitsbaseline für Windows 11](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/windows-11-security-baseline/ba-p/2810772)
   
   * [Sicherheitsbaseline für Windows Server 2022](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/windows-server-2022-security-baseline/ba-p/2724685)
   
* Lokale Umgebungen

   * [Microsoft Defender for Identity-Architektur](/defender-for-identity/architecture)

   * [Schnellstart: Herstellen einer Verbindung zwischen Microsoft Defender for Identity und Active Directory](/defender-for-identity/install-step2)

   * [Azure-Sicherheitsbaseline für Microsoft Defender for Identity](/defender-for-identity/security-baseline)

   * [Überwachen von Active Directory auf Anzeichen einer Sicherheitsgefährdung](/windows-server/identity/ad-ds/plan/security-best-practices/monitoring-active-directory-for-signs-of-compromise)

* Cloudbasierte Azure-Umgebungen


   * [Überwachen von Anmeldungen mit dem Azure AD-Anmeldeprotokoll](../reports-monitoring/concept-all-sign-ins.md)

   * [Berichte zu Überwachungsaktivitäten im Azure Active Directory-Portal](../reports-monitoring/concept-audit-logs.md)

   * [Untersuchen eines Risikos mit Azure Active Directory Identity Protection](../identity-protection/howto-identity-protection-investigate-risk.md) 

   * [Verknüpfen von Azure AD Identity Protection-Daten mit Microsoft Sentinel](../../sentinel/data-connectors-reference.md#azure-active-directory-identity-protection)

* Active Directory Domain Services (AD DS)

   * [Empfehlungen zu Überwachungsrichtlinien](/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations)

* Active Directory-Verbunddienste (Active Directory Federation Services, AD FS)

   * [AD FS-Problembehandlung: Überwachen von Ereignissen und Protokollierung](/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging)

## <a name="data-sources"></a>Datenquellen 

Zur Untersuchung und Überwachung verwenden Sie die folgenden Protokolldateien:

* [Azure AD-Überwachungsprotokolle](../reports-monitoring/concept-audit-logs.md)

* [Anmeldeprotokolle](../reports-monitoring/concept-all-sign-ins.md)

* [Microsoft 365-Überwachungsprotokolle](/microsoft-365/compliance/auditing-solutions-overview)

* [Azure Key Vault-Protokolle](../../key-vault/general/logging.md?tabs=Vault)

Im Azure-Portal können Sie die Azure AD-Überwachungsprotokolle anzeigen und als CSV- (durch Trennzeichen getrennte Werte) oder JSON-Dateien (JavaScript Object Notation) herunterladen. Das Azure-Portal bietet mehrere Möglichkeiten zur Integration von Azure AD-Protokollen in andere Tools, die eine umfassendere Automatisierung von Überwachung und Benachrichtigungen ermöglichen:

* **[Microsoft Sentinel](../../sentinel/overview.md)** : Ermöglicht intelligente Sicherheitsanalysen auf Unternehmensebene, indem SIEM-Funktionen (Security Information and Event Management) zur Verfügung gestellt werden. 

* **[Azure Monitor](../../azure-monitor/overview.md)** : ermöglicht die automatisierte Überwachung verschiedener Bedingungen und entsprechende Warnungen. Damit können Arbeitsmappen erstellt oder verwendet werden, um Daten aus verschiedenen Quellen zu kombinieren.

* **[Azure Event Hubs](../../event-hubs/event-hubs-about.md) mit Integration in ein SIEM-System**- [Azure AD-Protokolle können über die Azure Event Hub-Integration in andere SIEM-Systeme wie Splunk, ArcSight, QRadar und Sumo Logic integriert](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) werden.

* **[Microsoft Defender für Cloud Apps](/cloud-app-security/what-is-cloud-app-security)** ermöglichen Ihnen die Erkennung und Verwaltung von Apps, die Steuerung von Apps und Ressourcen sowie die Überprüfung der Compliance Ihrer Cloud-Apps.

Ein Großteil der Überwachung und zugehörigen Warnungen hängt von den Auswirkungen Ihrer Richtlinien für bedingten Zugriff ab. Sie können die Arbeitsmappe [Erkenntnisse und Berichterstellung zum bedingten Zugriff](../conditional-access/howto-conditional-access-insights-reporting.md) verwenden, um die Auswirkungen einer oder mehrerer Richtlinien für bedingten Zugriff auf Ihre Anmeldungen sowie die Ergebnisse von Richtlinien, einschließlich Gerätestatus, zu untersuchen. Diese Arbeitsmappe bietet Ihnen die Möglichkeit, eine Zusammenfassung der Auswirkungen anzuzeigen und die Auswirkungen über einen bestimmten Zeitraum zu ermitteln. Sie können mithilfe der Arbeitsmappe auch die Anmeldungen eines bestimmten Benutzers untersuchen. 

Im restlichen Teil dieses Artikels wird beschrieben, was wir Ihnen zur Überwachung und für zugehörige Warnungen empfehlen, und zwar gegliedert nach der Art der Bedrohung. Wo es spezielle vordefinierte Lösungen gibt, verweisen wir auf diese oder stellen Beispiele im Anschluss an die Tabelle zur Verfügung. Andernfalls können Sie Warnungen mithilfe der oben genannten Tools erstellen. 

* **[Identity Protection:](../identity-protection/overview-identity-protection.md)** Generiert drei wichtige Berichte, die Sie bei der Untersuchung verwenden können:

   * **Riskante Benutzer:** Enthält Informationen darüber, welche Benutzer gefährdet sind, Details zu Erkennungen, den Verlauf aller riskante Anmeldungen und den Risikoverlauf.

   * **Riskante Anmeldungen:** Enthält Informationen zu den Umständen einer Anmeldung, die auf verdächtige Situationen hinweisen können. Weitere Informationen zum Untersuchen von Informationen aus diesem Bericht finden Sie unter [Untersuchen von Risiken](../identity-protection/howto-identity-protection-investigate-risk.md). 

   *  **Risikoerkennungen:** Enthält Informationen zu Risikosignalen, die von Azure AD Identity Protection erkannt werden und die Grundlage für die Bestimmung des Anmelde- und Benutzerrisikos sind. Weitere Informationen finden Sie im [Azure AD-Leitfaden zu Sicherheitsvorgängen für Benutzerkonten](security-operations-user-accounts.md).

### <a name="data-sources-for-domain-controller-monitoring"></a>Datenquellen für die Domänencontrollerüberwachung

Um die besten Ergebnisse zu erzielen, wird empfohlen, dass Sie Ihre Domänencontroller mithilfe von Microsoft Defender for Identity überwachen. So profitieren Sie von den besten Erkennungs- und Automatisierungsfunktionen. Orientieren Sie sich an den folgenden Leitfäden:

* [Microsoft Defender for Identity-Architektur](/defender-for-identity/architecture)

* [Schnellstart: Herstellen einer Verbindung zwischen Microsoft Defender for Identity und Active Directory](/defender-for-identity/install-step2)

Wenn Sie nicht beabsichtigen, Microsoft Defender for Identity zu verwenden, können Sie Ihre [Domänencontroller über Ereignisprotokollmeldungen überwachen](/windows-server/identity/ad-ds/plan/security-best-practices/monitoring-active-directory-for-signs-of-compromise) oder durch [Ausführen von PowerShell-Cmdlets](/windows-server/identity/ad-ds/deploy/troubleshooting-domain-controller-deployment). 

## <a name="components-of-hybrid-authentication"></a>Komponenten der Hybridauthentifizierung

Im Rahmen einer Azure-Hybridumgebung sollte Folgendes das Minimum für eine Überwachungs- und Warnungsstrategie bilden. 

* **PTA-Agent:** Der Passthrough-Authentifizierungs-Agent wird zum Aktivieren der Passthrough-Authentifizierung verwendet und lokal installiert. Informationen zum Überprüfen Ihrer Agent-Version und zu den nächsten Schritten finden Sie unter [Azure AD-Passthrough-Authentifizierung: Versionsverlauf des Agents](../hybrid/reference-connect-pta-version-history.md). 

* **AD FS/WAP:** Azure Active Directory-Verbunddienste (Azure AD FS) und Webanwendungsproxy (WAP) ermöglichen die sichere Freigabe von digitalen Identitäten und Zugriffsrechten über Ihre Sicherheits- und Unternehmensgrenzen hinweg. Informationen über bewährte Sicherheitspraktiken finden Sie unter [Best practices for securing Active Directory Federation Services](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs). 

* **Azure AD Connect Health-Agent:** Dieser Agent stellt eine Kommunikationsverbindung mit Azure AD Connect Health bereit. Informationen zur Installation des Agents finden Sie unter [Installieren der Azure AD Connect Health-Agents](../hybrid/how-to-connect-health-agent-install.md). 

* **Azure AD Connect-Synchronisierungsmodul:** Die lokale Komponente, die auch als Synchronisierungsmodul bezeichnet wird. Informationen zu diesem Feature finden Sie unter [Features des Azure AD Connect-Synchronisierungsdiensts](../hybrid/how-to-connect-syncservice-features.md).

* **Kennwortschutz-DC-Agent:** Der DC-Agent für den Azure-Kennwortschutz unterstützt bei der Überwachung und dem Reporting von Ereignisprotokollmeldungen. Weitere Informationen finden Sie unter [Erzwingen des lokalen Azure AD-Kennwortschutzes für Active Directory Domain Services](../authentication/concept-password-ban-bad-on-premises.md). 

* **Kennwortfilter-DLL:** Die Kennwortfilter-DLL des DC-Agents empfängt Anforderungen zur Kennwortüberprüfung vom Betriebssystem. Der Filter leitet diese an den DC-Agent-Dienst weiter, der lokal auf dem Domänencontroller ausgeführt wird. Informationen zur Verwendung der DLL finden Sie unter [Erzwingen des lokalen Azure AD-Kennwortschutzes für Active Directory Domain Services](../authentication/concept-password-ban-bad-on-premises.md). 

* **Kennwortrückschreibungs-Agent**: Kennwortrückschreiben ist eine Funktion, die mit [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) aktiviert wurde und es ermöglicht, Kennwortänderungen in der Cloud in Echtzeit in ein vorhandenes lokales Verzeichnis zurückzuschreiben. Weitere Informationen zu diesem Feature finden Sie unter [Funktionsweise des Rückschreibens von Self-Service-Kennwortzurücksetzungen in Azure Active Directory](../authentication/concept-sspr-writeback.md).

* **Azure AD-Anwendungsproxyconnector:** Einfache Agents, die lokal eingerichtet werden und die ausgehende Verbindung mit dem Anwendungsproxydienst vereinfachen. Weitere Informationen finden Sie unter [Grundlegendes zu Azure AD-Anwendungsproxyconnectors](../app-proxy/application-proxy-connectors.md). 

## <a name="components-of-cloud-based-authentication"></a>Komponenten der cloudbasierten Authentifizierung

Im Rahmen einer cloudbasierten Azure-Umgebung sollte Folgendes das Minimum für eine Überwachungs- und Warnungsstrategie bilden.

* **Azure Active Directory-Anwendungsproxy:** Dieser Clouddienst ermöglicht den sicheren Remotezugriff auf lokal gehostete Webanwendungen. Weitere Informationen finden Sie unter [Remotezugriff auf lokale Anwendungen über den Azure AD-Anwendungsproxy](../app-proxy/application-proxy-connectors.md). 

* **Azure AD Connect:** Für eine Azure AD Connect-Lösung verwendete Dienste. Weitere Informationen finden Sie unter [Was ist Azure AD Connect?](../hybrid/whatis-azure-ad-connect.md)

* **Azure AD Connect Health:** Service Health bietet Ihnen ein anpassbares Dashboard, mit dem Sie die Integrität Ihrer Azure-Dienste in den Regionen nachverfolgen, in denen Sie sie verwenden. Weitere Informationen finden Sie unter [Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md).

* **Azure MFA:** Bei der Azure AD Multi-Factor Authentication müssen Benutzer zur Authentifizierung mehr als einen Identitätsnachweis erbringen. Dies kann einen proaktiven ersten Schritt zum Schützen Ihrer Umgebung bilden. Weitere Informationen finden Sie unter [So funktioniert’s: Azure AD Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md).

* **Dynamische Gruppen:** Dynamische Konfiguration der Sicherheitsgruppenmitgliedschaft für Azure Active Directory (Azure AD). Administratoren können Regeln festlegen, nach denen Gruppen aufgefüllt werden, die in Azure AD auf der Grundlage von Benutzerattributen erstellt werden. Weitere Informationen finden Sie unter [Dynamische Gruppen und Azure Active Directory B2B-Zusammenarbeit](../external-identities/use-dynamic-groups.md).

* **Bedingter Zugriff:** Der bedingte Zugriff ist das Tool, das von Azure Active Directory verwendet wird, um Signale zusammenzuführen, Entscheidungen zu treffen und Organisationsrichtlinien zu erzwingen. Der bedingte Zugriff ist der Kern der neuen identitätsbasierten Steuerungsebene. Weitere Informationen finden Sie unter [Was ist bedingter Zugriff?](../conditional-access/overview.md)

* **Identity Protection:** Ein Tool, mit dem Organisationen die Erkennung und Behebung identitätsbasierter Risiken automatisieren, Risiken anhand von Daten im Portal untersuchen und Risikoerkennungsdaten an Ihr SIEM exportieren können. Weitere Informationen finden Sie unter [Was ist Identity Protection?](../identity-protection/overview-identity-protection.md)

* **Gruppenbasierte Lizenzierung:** Lizenzen können Gruppen statt Benutzern direkt zugewiesen werden. Azure AD speichert Informationen zum Lizenzzuweisungsstatus für Benutzer. 

* **Bereitstellungsdienst:** Die Bereitstellung bezieht sich auf das Erstellen von Benutzeridentitäten und -rollen in den Cloudanwendungen, auf die Benutzer Zugriff benötigen. Zusätzlich zur Erstellung von Benutzeridentitäten umfasst die automatische Bereitstellung auch die Wartung und Entfernung von Benutzeridentitäten, wenn sich der Status oder die Rollen ändern. Weitere Informationen finden Sie unter [Funktionsweise der Anwendungsbereitstellung in Azure Active Directory](../app-provisioning/how-provisioning-works.md).

* **Graph-API:** Die Microsoft Graph-API ist eine RESTful-Web-API, die Ihnen den Zugriff auf Microsoft Cloud-Dienstressourcen ermöglicht. Nachdem Sie Ihre App registriert und Authentifizierungstoken für einen Benutzer oder Dienst abgerufen haben, können Sie Anforderungen an die Microsoft Graph-API senden. Weitere Informationen finden Sie in der [Übersicht zu Microsoft Graph](/graph/overview).

* **Domain Services:** Azure Active Directory Domain Services stellt verwaltete Domänendienste wie Domänenbeitritt und Gruppenrichtlinien bereit. Weitere Informationen finden Sie unter [Was ist Azure Active Directory Domain Services?](../../active-directory-domain-services/overview.md)

* **Azure Resource Manager :** Azure Resource Manager ist der Bereitstellungs- und Verwaltungsdienst für Azure. Er bietet eine Verwaltungsebene, die das Erstellen, Aktualisieren und Löschen von Ressourcen in Ihrem Azure-Konto ermöglicht. Weitere Informationen finden Sie unter [Was ist Azure Resource Manager?](../../azure-resource-manager/management/overview.md)

* **Verwaltete Identität:** Dank verwalteter Identitäten müssen Entwickler keine Anmeldeinformationen mehr verwalten. Verwaltete Identitäten stellen eine Identität bereit, die Anwendungen beim Herstellen einer Verbindung mit Ressourcen verwenden, die Azure Active Directory-Authentifizierung (Azure AD) unterstützen. Weitere Informationen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../managed-identities-azure-resources/overview.md).

* **Privileged Identity Management:** Privileged Identity Management (PIM) ist ein Dienst in Azure Active Directory (Azure AD), mit dem Sie den Zugriff auf wichtige Ressourcen innerhalb Ihrer Organisation verwalten, steuern und überwachen können. Weitere Informationen finden Sie unter [Was ist Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md).

* **Zugriffsüberprüfungen:** Mithilfe von Azure Active Directory-Zugriffsüberprüfungen (Azure AD-Zugriffsüberprüfungen) können Organisationen Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen und Rollenzuweisungen effizient verwalten. Der Benutzerzugriff kann regelmäßig überprüft werden, um sicherzustellen, dass nur die richtigen Personen weiterhin Zugriff haben. Weitere Informationen finden Sie unter [Was sind Azure AD-Zugriffsüberprüfungen?](../governance/access-reviews-overview.md)

* **Berechtigungsverwaltung:** Die Berechtigungsverwaltung von Azure Active Directory (Azure AD) ist ein [Identitätsgovernance](../governance/identity-governance-overview.md)-Feature, mit dem Organisationen Identitäten und Zugriffszyklus im großen Stil verwalten können, indem sie Zugriffsanforderungsworkflows, Zugriffszuweisungen, Überprüfungen und Ablaufzeiten automatisieren. Weitere Informationen finden Sie unter [Was ist die Azure AD-Berechtigungsverwaltung?](../governance/entitlement-management-overview.md).

* **Aktivitätsprotokolle:** Das Aktivitätsprotokoll ist ein [Plattformprotokoll](../../azure-monitor/essentials/platform-logs-overview.md) in Azure, das einen Einblick in Ereignisse auf Abonnementebene ermöglicht. Dies sind beispielsweise Informationen wie das Ändern einer Ressource oder das Starten eines virtuellen Computers. Weitere Informationen finden Sie unter [Azure-Aktivitätsprotokoll](../../azure-monitor/essentials/activity-log.md).

* **Self-Service-Kennwortzurücksetzungsdienst:** Mit der Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) von Azure Active Directory (Azure AD) können Benutzer ihr Kennwort ohne Beteiligung eines Administrators oder des Helpdesks ändern oder zurücksetzen. Weitere Informationen finden Sie unter [Vorgehensweise: Self-Service-Kennwortzurücksetzung in Azure AD](../authentication/concept-sspr-howitworks.md).

* **Gerätedienste:** Die Geräteidentitätsverwaltung stellt die Grundlage für [gerätebasierten bedingten Zugriff](../conditional-access/require-managed-devices.md) dar. Mit Richtlinien für gerätebasierten bedingten Zugriff können Sie sicherstellen, dass nur mit verwalteten Geräten auf Ressourcen in Ihrer Umgebung zugegriffen werden kann. Weitere Informationen finden Sie unter [Was ist eine Geräteidentität?](../devices/overview.md)

* **Self-Service-Gruppenverwaltung:** Sie können Benutzern die Erstellung und Verwaltung ihrer eigenen Sicherheitsgruppen oder Microsoft 365-Gruppen in Azure Active Directory (Azure AD) ermöglichen. Der Besitzer der Gruppe kann Mitgliedschaftsanforderungen genehmigen oder ablehnen sowie die Steuerung der Gruppenmitgliedschaft delegieren. Self-Service-Funktionen zur Gruppenverwaltung sind nicht für E-Mail-aktivierte Sicherheitsgruppen oder Verteilerlisten verfügbar. Weitere Informationen finden Sie unter [Einrichten der Self-Service-Gruppenverwaltung in Azure Active Directory](../enterprise-users/groups-self-service-management.md).

* **Risikoerkennungen** enthalten Informationen zu anderen Risiken, die bei Erkennung eines Risikos ausgelöst werden, sowie andere relevante Informationen wie den Anmeldeort und Details von Microsoft Defender für Cloud Apps.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln im Leitfaden zu Sicherheitsvorgängen:

[Azure AD: Übersicht über Sicherheitsvorgänge](security-operations-introduction.md)

[Sicherheitsvorgänge für Benutzerkonten](security-operations-user-accounts.md)

[Sicherheitsvorgänge für privilegierte Konten](security-operations-privileged-accounts.md)

[Sicherheitsvorgänge für Privileged Identity Management](security-operations-privileged-identity-management.md)

[Sicherheitsvorgänge für Anwendungen](security-operations-applications.md)

[Sicherheitsvorgänge für Geräte](security-operations-devices.md)

 
[Sicherheitsvorgänge für die Infrastruktur](security-operations-infrastructure.md)
