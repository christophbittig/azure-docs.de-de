---
title: Azure Active Directory-Sicherheitsvorgänge für die Infrastruktur
description: Erfahren Sie, wie Sie Infrastrukturkomponenten überwachen und Warnungen ausgeben, um Sicherheitsbedrohungen zu identifizieren.
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
ms.openlocfilehash: c96362b522100d27757618e330f45514e4d442df
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132349241"
---
# <a name="security-operations-for-infrastructure"></a>Sicherheitsvorgänge für die Infrastruktur

Die Infrastruktur verfügt über viele Komponenten, bei denen Sicherheitsrisiken auftreten können, wenn sie nicht ordnungsgemäß konfiguriert sind. Sie überwachen im Rahmen Ihrer Überwachungs- und Warnungsstrategie für die Infrastruktur auf Ereignisse in den folgenden Bereichen und erstellen entsprechende Warnungen:

* Authentifizierung und Autorisierung

* Hybridauthentifizierungskomponenten, einschließlich Verbundserver

* Richtlinien 

* Abonnements

Die Überwachung der Komponenten Ihrer Authentifizierungsinfrastruktur und das Ausgeben entsprechender Warnungen sind von entscheidender Bedeutung. Jede Kompromittierung kann zu einer vollständigen Kompromittierung der gesamten Umgebung führen. Viele Unternehmen, die Azure AD verwenden, arbeiten in einer Hybridauthentifizierungsumgebung. Dies bedeutet, dass sowohl Cloudkomponenten als auch lokale Komponenten in Ihre Überwachungs- und Warnungsstrategie einbezogen werden sollten. Eine Hybridauthentifizierungsumgebung bringt einen weiteren Angriffsvektor für Ihre Umgebung mit sich.

Es wird empfohlen, alle Komponenten sowie die Konten zu ihrer Verwaltung als Ressourcen der Steuerungsebene/Ebene 0 zu betrachten. Einen Leitfaden zum Entwerfen und Implementieren Ihrer Umgebung finden Sie unter [Schützen des privilegierten Zugriffs](/security/compass/overview). Dieser Leitfaden enthält Empfehlungen für jede Hybridauthentifizierungskomponente, die möglicherweise für einen Azure AD-Mandanten verwendet werden kann.

Ein erster Schritt für die Erkennung unerwarteter Ereignisse und potenzieller Angriffe ist die Einrichtung einer Baseline. Informationen zu allen lokalen Komponenten, die in diesem Artikel aufgeführt sind, finden Sie unter [Bereitstellung einer Lösung für privilegierten Zugriff](/security/compass/privileged-access-deployment) im Leitfaden zum Schützen des privilegierten Zugriffs.

## <a name="where-to-look"></a>Zu verwendende Ressourcen

Zur Untersuchung und Überwachung verwenden Sie die folgenden Protokolldateien: 

* [Azure AD-Überwachungsprotokolle](../reports-monitoring/concept-audit-logs.md)

* [Anmeldeprotokolle](../reports-monitoring/concept-all-sign-ins.md)

* [Microsoft 365-Überwachungsprotokolle](/microsoft-365/compliance/auditing-solutions-overview) 

* [Azure Key Vault-Protokolle](../../key-vault/general/logging.md?tabs=Vault)

Im Azure-Portal können Sie die Azure AD-Überwachungsprotokolle anzeigen und als CSV- (durch Trennzeichen getrennte Werte) oder JSON-Dateien (JavaScript Object Notation) herunterladen. Das Azure-Portal bietet mehrere Möglichkeiten zur Integration von Azure AD-Protokollen in andere Tools, die eine umfassendere Automatisierung von Überwachung und Benachrichtigungen ermöglichen:

* [Microsoft Sentinel](../../sentinel/overview.md): Ermöglicht intelligente Sicherheitsanalysen auf Unternehmensebene, indem SIEM-Funktionen (Security Information and Event Management) zur Verfügung gestellt werden. 

* [Azure Monitor](../../azure-monitor/overview.md): Ermöglicht die automatisierte Überwachung von und Warnung vor verschiedenen Bedingungen. Damit können Arbeitsmappen erstellt oder verwendet werden, um Daten aus verschiedenen Quellen zu kombinieren.

* [Azure Event Hubs](../../event-hubs/event-hubs-about.md) mit Integration in ein SIEM-System: [Azure AD-Protokolle können über die Azure Event Hub-Integration in andere SIEM-Systeme integriert werden](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md), z. B. in Splunk, ArcSight, QRadar und Sumo Logic.

* [Microsoft Defender-für-Cloud-Apps](/cloud-app-security/what-is-cloud-app-security): Ermöglichen Ihnen die Erkennung und Verwaltung von Apps, die Steuerung von Apps und Ressourcen sowie die Überprüfung der Kompatibilität Ihrer Cloud-Apps. 

Im restlichen Teil dieses Artikels wird beschrieben, welche Ereignisse Sie überwachen und wofür Sie Warnungen erstellen sollten, wobei die Inhalte nach der Art der Bedrohung organisiert sind. Wenn es spezielle vorgefertigte Lösungen gibt, finden Sie im Anschluss an die Tabelle entsprechende Links. Andernfalls können Sie Warnungen mithilfe der oben genannten Tools erstellen.

## <a name="authentication-infrastructure"></a>Authentifizierungsinfrastruktur

In Hybridumgebungen, die sowohl lokale als auch cloudbasierte Ressourcen und Konten enthalten, ist die Active Directory-Infrastruktur ein wichtiger Bestandteil des Authentifizierungsstapels. Der Stapel ist auch ein Ziel für Angriffe. Daher muss er für die Aufrechterhaltung einer sicheren Umgebung konfiguriert und ordnungsgemäß überwacht werden. Beispiele für aktuelle Arten von Angriffen auf Ihre Authentifizierungsinfrastruktur sind Kennwortspray- und Solorigate-Angriffe. Im Folgenden finden Sie Links zu empfohlenen Artikeln:

* [Schützen des privilegierten Zugriffs](/security/compass/overview): Dieser Artikel bietet eine Übersicht über die aktuellen Verfahren, die Zero Trust-Methoden verwenden, um sicheren privilegierten Zugriff zu erstellen und aufrechtzuerhalten.

* [Von Microsoft Defender for Identity überwachte Aktivitäten](/defender-for-identity/monitored-activities): Dieser Artikel enthält eine umfassende Liste der Aktivitäten, für die Sie Warnungen überwachen und festlegen können. 

* [Microsoft Defender for Identity: Grundlegendes zu Sicherheitswarnungen](/defender-for-identity/understanding-security-alerts): Dieser Artikel enthält Anleitungen zum Entwerfen und Implementieren einer Strategie für Sicherheitswarnungen.

Im Folgenden finden Sie Links zu speziellen Artikeln, die sich auf die Überwachung Ihrer Authentifizierungsinfrastruktur und das Erstellen entsprechender Warnungen konzentrieren:

* [Lateral Movement-Pfade (LMPs) für Microsoft Defender for Identity](/defender-for-identity/use-case-lateral-movement-path): In diesem Artikel werden Erkennungstechniken beschrieben, mit denen Sie ermitteln können, wann nicht sensible Konten verwendet werden, um Zugriff auf sensible Konten im gesamten Netzwerk zu erhalten.

* [Arbeiten mit Sicherheitswarnungen in Microsoft Defender for Identity](/defender-for-identity/working-with-suspicious-activities): In diesem Artikel wird beschrieben, wie Warnungen nach der Protokollierung überprüft und verwaltet werden. 

 Auf folgende Punkte sollte geachtet werden:

| Zu überwachende Elemente| Risikostufe| Hierbei gilt:| Notizen |
| - | - | - | - |
| Extranetsperrtrends| Hoch| Azure AD Connect Health| Informationen zu den Tools und Verfahren zum Erkennen von Extranetsperrtrends finden Sie unter [Überwachen von AD FS mithilfe von Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md). |
| Fehlerhafte Anmeldungen|Hoch | Connect Health-Portal| Exportieren Sie den Bericht über riskante IP-Adressen, oder laden Sie ihn herunter, und befolgen Sie für die nächsten Schritte die Anleitung unter [Bericht über riskante IP-Adressen (öffentliche Vorschauversion)](../hybrid/how-to-connect-health-adfs-risky-ip.md). |
| Schutz der Privatsphäre| Niedrig| Azure AD Connect Health| Ziehen Sie den Artikel [Datenschutz und Azure AD Connect Health](../hybrid/reference-connect-health-user-privacy.md) zurate, um Azure AD Connect Health für die Deaktivierung der Datensammlung und Überwachung zu konfigurieren. |
| Potenzieller Brute-Force-Angriff auf LDAP| Medium| Microsoft Defender for Identity| Verwenden Sie den Sensor, um potenzielle Brute-Force-Angriffe auf LDAP zu erkennen. |
| Reconnaissance mithilfe von Kontoenumeration| Medium| Microsoft Defender for Identity| Verwenden Sie den Sensor, um die Reconnaissance mithilfe von Kontoenumeration durchzuführen. |
| Allgemeine Korrelation zwischen Azure AD und Azure AD FS|Medium | Microsoft Defender for Identity| Verwenden Sie Funktionen zum Korrelieren von Aktivitäten zwischen der Azure AD- und Azure AD FS-Umgebung. |


 

### <a name="pass-through-authentication-monitoring"></a>Überwachung der Passthrough-Authentifizierung 

Benutzer werden bei der Azure Active Directory (Azure AD) Passthrough-Authentifizierung angemeldet, indem sie ihre Kennwörter direkt für die lokale Active Directory-Instanz angeben. 

Auf folgende Punkte sollte geachtet werden:

| Zu überwachende Elemente| Risikostufe| Hierbei gilt:| Filter/Unterfilter| Notizen |
| - | - | - | - | - |
| Fehler bei der Azure AD-Passthrough-Authentifizierung|Medium | Anwendungs- und Dienstprotokolle\Microsoft\AzureAdConnect\AuthenticationAgent\Admin| AADSTS80001 – Verbindung mit Active Directory kann nicht hergestellt werden.| Stellen Sie sicher, dass die Agent-Server Mitglieder derselben AD-Gesamtstruktur wie die Benutzer sind, deren Kennwörter überprüft werden müssen, und dass sie eine Verbindung mit Active Directory herstellen können. |
| Fehler bei der Azure AD-Passthrough-Authentifizierung| Medium| Anwendungs- und Dienstprotokolle\Microsoft\AzureAdConnect\AuthenticationAgent\Admin| AADSTS8002 – Bei der Verbindung mit Active Directory ist ein Timeout aufgetreten.| Check to ensure that Active Directory is available and is responding to requests from the agents. (Überprüfen Sie, ob Active Directory verfügbar ist und auf Anforderungen der Agents antwortet.) |
| Fehler bei der Azure AD-Passthrough-Authentifizierung|Medium | Anwendungs- und Dienstprotokolle\Microsoft\AzureAdConnect\AuthenticationAgent\Admin| AADSTS80004 – Der an den Agent übergebene Benutzername war ungültig.| Stellen Sie sicher, dass der Benutzer den richtigen Benutzernamen für die Anmeldung verwendet. |
| Fehler bei der Azure AD-Passthrough-Authentifizierung|Medium | Anwendungs- und Dienstprotokolle\Microsoft\AzureAdConnect\AuthenticationAgent\Admin| AADSTS80005 – Bei der Überprüfung ist eine unvorhersehbare WebException aufgetreten.| A transient error. (Vorübergehender Fehler.) Wiederholen Sie die Anforderung. Sollte der Fehler weiterhin auftreten, wenden Sie sich an den Microsoft-Support. |
| Fehler bei der Azure AD-Passthrough-Authentifizierung| Medium| Anwendungs- und Dienstprotokolle\Microsoft\AzureAdConnect\AuthenticationAgent\Admin| AADSTS80007 – Bei der Kommunikation mit Active Directory ist ein Fehler aufgetreten.| Suchen Sie in den Agent-Protokollen nach weiteren Informationen, und überprüfen Sie, ob Active Directory erwartungsgemäß funktioniert. |
| Fehler bei der Azure AD-Passthrough-Authentifizierung|Hoch | Win32-LogonUserA-Funktions-API| Anmeldeereignisse 4624(s): Ein Konto wurde erfolgreich angemeldet.<br>– Korrelation mit –<br>4625(F): Fehler beim Anmelden eines Kontos.| Verwenden Sie dies mit den vermuteten Benutzernamen auf dem Domänencontroller, der Anforderungen authentifiziert. Eine Anleitung finden Sie unter [LogonUserA function (winbase.h)](/windows/win32/api/winbase/nf-winbase-logonusera) (LogonUserA-Funktion (winbase.h), in englischer Sprache). |
| Fehler bei der Azure AD-Passthrough-Authentifizierung| Medium| PowerShell-Skript des Domänencontrollers| Siehe die Abfrage nach der Tabelle. | Zusätzliche Anleitungen finden Sie unter [Behandlung von Problemen bei der Azure Active Directory-Passthrough-Authentifizierung](../hybrid/tshoot-connect-pass-through-authentication.md). |

```Kusto

<QueryList>

<Query Id="0" Path="Security">

<Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft Azure AD Connect Authentication Agent\AzureADConnectAuthenticationAgentService.exe')]]</Select>

</Query>

</QueryList>
```

### <a name="appproxy-connector"></a>AppProxy-Connector

Azure AD und der Azure AD-Anwendungsproxy ermöglichen Remotebenutzern einmaliges Anmelden (Single Sign-On, SSO). Benutzer stellen ohne ein virtuelles privates Netzwerk (VPN), ohne Server mit zwei Netzwerkumgebungen und ohne Firewallregeln eine sichere Verbindung mit lokalen Apps her. Wenn Ihr Azure AD-Anwendungsproxy-Connectorserver kompromittiert ist, können Angreifer die SSO-Erfahrung oder den Zugriff auf veröffentlichte Anwendungen ändern. 

Informationen zum Konfigurieren der Überwachung für den Anwendungsproxy finden Sie unter [Beheben von Problemen mit Anwendungsproxys und Fehlermeldungen](../app-proxy/application-proxy-troubleshoot.md). Die Datendatei, in der Informationen protokolliert werden, finden Sie unter „Anwendungs- und Dienstprotokolle\Microsoft\AadApplicationProxy\Connector\Admin“. Ein vollständiges Referenzhandbuch zu Überwachungsaktivitäten finden Sie unter [Referenz zu Überwachungsaktivitäten von Azure AD](../reports-monitoring/reference-audit-activities.md). Zu überwachende Punkte:

| Zu überwachende Elemente| Risikostufe| Hierbei gilt:| Filter/Unterfilter| Notizen |
| - | - | - | - | - |
| Kerberos-Fehler| Medium | Verschiedene Tools| Medium | Anleitungen zu Fehlern bei der Kerberos-Authentifizierung finden Sie in [Beheben von Problemen mit Anwendungsproxys und Fehlermeldungen](../app-proxy/application-proxy-troubleshoot.md) unter „Kerberos-Fehler“. |
| DC-Sicherheitsprobleme| Hoch| Überwachungsprotokolle zur DC-Sicherheit| Ereignis-ID 4742(s): Ein Computerkonto wurde geändert.<br>- und -<br>Flag: Für Delegierungszwecke vertraut<br>Oder<br>Flag : Trusted to Authenticate for Delegation (Vertrauenswürdig für die Authentifizierung für die Delegierung)| Untersuchen Sie alle Flagänderungen. |
| Pass-the-Ticket-ähnliche Angriffe| Hoch| | | Befolgen Sie die Anleitung in:<li>[Sicherheitsprinzipalreconnaissance (LDAP) (externe ID 2038)](/defender-for-identity/reconnaissance-alerts)<li>[Tutorial: Warnungen zu kompromittierten Anmeldeinformationen](/defender-for-identity/compromised-credentials-alerts)<li> [Verstehen und Verwenden von Lateral Movement-Pfaden mit Microsoft Defender for Identity](/defender-for-identity/use-case-lateral-movement-path)<li> [Grundlegendes zu Entitätsprofilen](/defender-for-identity/entity-profiles) |


### <a name="legacy-authentication-settings"></a>Einstellungen für die Legacyauthentifizierung

Damit die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) wirksam wird, müssen Sie auch die Legacyauthentifizierung blockieren. Anschließend müssen Sie Ihre Umgebung überwachen und bei jeder Verwendung der Legacyauthentifizierung eine Warnung ausgeben. Der Grund dafür ist, dass Legacyauthentifizierungsprotokolle, z. B. POP, SMTP, IMAP und MAPI, keine MFA erzwingen können. Dadurch werden diese Protokolle zu bevorzugten Zugangspunkten für Angreifer Ihrer Organisation. Weitere Informationen zu Tools, mit denen Sie die Legacyauthentifizierung blockieren können, finden Sie unter [New tools to block legacy authentication in your organization](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302) (Neue Tools zum Blockieren der Legacyauthentifizierung in Ihrer Organisation, in englischer Sprache). 

Die Legacyauthentifizierung wird im Protokoll der Azure AD-Anmeldungen als Ereignisdetail erfasst. Sie können mithilfe der Azure Monitor-Arbeitsmappe die Verwendung der Legacyauthentifizierung identifizieren. Weitere Informationen finden Sie unter [Anmeldungen mit Legacyauthentifizierung](../reports-monitoring/howto-use-azure-monitor-workbooks.md) in [Verwenden von Azure Monitor-Arbeitsmappen für Azure Active Directory-Berichte](../reports-monitoring/howto-use-azure-monitor-workbooks.md). Sie können auch die Arbeitsmappe für unsichere Protokolle für Microsoft Sentinel verwenden. Weitere Informationen finden Sie unter [Leitfaden zur Implementierung der Arbeitsmappe für unsichere Protokolle mit Microsoft Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-insecure-protocols-workbook-implementation-guide/ba-p/1197564). Zu überwachende Aktivitäten:

| Zu überwachende Elemente| Risikostufe| Hierbei gilt:| Filter/Unterfilter| Notizen |
| - | - | - | - | - |
| Legacyauthentifizierungen|Hoch | Azure AD-Anmeldeprotokoll| ClientApp : POP<br>ClientApp : IMAP<br>ClientApp : MAPI<br>ClientApp: SMTP<br>ClientApp : ActiveSync go to EXO<br>Andere Clients = SharePoint und EWS| In Verbunddomänenumgebungen werden fehlgeschlagene Authentifizierungen nicht aufgezeichnet, sodass sie nicht im Protokoll angezeigt werden. |


## <a name="azure-ad-connect"></a>Azure AD Connect

In Azure AD Connect lässt sich die Konto- und Attributsynchronisierung zwischen der lokalen und cloudbasierten Azure AD-Umgebung durchführen. Das Microsoft-Tool Azure AD Connect wurde entwickelt, um Sie beim Erreichen Ihrer Hybrididentitätsziele zu unterstützen. Er zeichnet sich durch Folgendes aus:

* [Kennworthashsynchronisierung](../hybrid/whatis-phs.md): Eine Anmeldemethode, die einen Hash für das lokale AD-Kennwort eines Benutzers mit Azure AD synchronisiert.

* [Synchronisierung](../hybrid/how-to-connect-sync-whatis.md): Dieser Prozess ist verantwortlich für das Erstellen von Benutzern, Gruppen und anderen Objekten. Er stellt auch sicher, dass Identitätsinformationen für Ihre lokalen Benutzer und Gruppen denen in der Cloud entsprechen. Diese Synchronisierung umfasst auch Kennworthashes.

* [Systemüberwachung](../hybrid/whatis-azure-ad-connect.md): Azure AD Connect Health bietet eine stabile Überwachung und einen zentralen Speicherort im Azure-Portal, um diese Aktivität anzuzeigen.

Durch die Synchronisierung der Identität zwischen der lokalen Umgebung und der Cloudumgebung wird eine neue Angriffsfläche für die lokale und cloudbasierte Umgebung geschaffen. Es wird Folgendes empfohlen:

* Sie behandeln den primären Azure AD Connect-Server und den Azure AD Connect-Stagingserver als Ebene 0-Systeme auf der Steuerungsebene. 

* Sie befolgen einen Standardsatz von Richtlinien, die jeden Kontotyp und dessen Nutzung in Ihrer Umgebung steuern.

*  Sie installieren Azure AD Connect und Connect Health. Diese stellen in erster Linie operative Daten für die Umgebung bereit. 

Die Protokollierung von Azure AD Connect-Vorgängen erfolgt auf unterschiedliche Weise:

* Der Azure AD Connect-Assistent protokolliert Daten unter „\ProgramData\AADConnect“. Bei jedem Aufruf des Assistenten wird eine Ablaufverfolgungsprotokolldatei mit Zeitstempel erstellt. Das Ablaufverfolgungsprotokoll kann zur Analyse in Sentinel oder in andere SIEM-Tools (Security Information and Event Management) von Drittanbietern importiert werden<sup data-htmlnode=""></sup>.

* Einige Vorgänge initiieren ein PowerShell-Skript, um Protokollierungsinformationen zu erfassen. Um diese Daten zu sammeln, müssen Sie sicherstellen, dass die Skriptblockprotokollierung aktiviert ist.

### <a name="monitoring-configuration-changes"></a>Überwachen von Konfigurationsänderungen

Azure AD verwendet die Microsoft SQL Server-Datenbank-Engine oder SQL, um Azure AD Connect-Konfigurationsinformationen zu speichern. Daher sollte die Überwachung der Konfigurationsprotokolldateien in Ihre Überwachungsstrategie einbezogen werden. Schließen Sie insbesondere die folgenden Tabellen in Ihre Überwachungs- und Warnungsstrategie ein.

| Zu überwachende Elemente| Hierbei gilt:| Notizen |
| - | - | - |
| mms_management_agent| SQL Server-Überwachungsdatensätze| Siehe [SQL Server Audit-Datensätze](/sql/relational-databases/security/auditing/sql-server-audit-records) |
| mms_partition| SQL Server-Überwachungsdatensätze| Siehe [SQL Server Audit-Datensätze](/sql/relational-databases/security/auditing/sql-server-audit-records) |
| mms_run_profile| SQL Server-Überwachungsdatensätze| Siehe [SQL Server Audit-Datensätze](/sql/relational-databases/security/auditing/sql-server-audit-records) |
| mms_server_configuration| SQL Server-Überwachungsdatensätze| Siehe [SQL Server Audit-Datensätze](/sql/relational-databases/security/auditing/sql-server-audit-records) |
| mms_synchronization_rule| SQL Server-Überwachungsdatensätze| Siehe [SQL Server Audit-Datensätze](/sql/relational-databases/security/auditing/sql-server-audit-records) |


Informationen zum Überwachen von Konfigurationsinformationen:

* Für SQL Server siehe [SQL Server Audit-Datensätze](/sql/relational-databases/security/auditing/sql-server-audit-records)

* Weiter Informationen zu Microsoft Sentinel finden Sie unter [Herstellen einer Verbindung mit Windows-Servern, um Sicherheitsereignissen zu sammeln](/sql/relational-databases/security/auditing/sql-server-audit-records). 

* Informationen zum Konfigurieren und Verwenden von Azure AD Connect finden Sie unter [Was ist Azure AD Connect?](../hybrid/whatis-azure-ad-connect.md)

### <a name="monitoring-and-troubleshooting-synchronization"></a>Überwachen der Synchronisierung und Behandeln von Problemen bei der Synchronisierung

 Eine Funktion von Azure AD Connect ist die Synchronisierung der Hashsynchronisierung zwischen dem lokalen Kennwort eines Benutzers und Azure AD. Wenn Kennwörter nicht wie erwartet synchronisiert werden, kann sich dies auf einen Teil der Benutzer oder auf alle Benutzer auswirken. Informationen zum Überprüfen der ordnungsgemäßen Ausführung und zur Problembehandlung finden Sie in den folgenden Dokumenten:

* Informationen zur Überprüfung der Hashsynchronisierung und zur Problembehandlung finden Sie unter [Problembehandlung für die Kennworthashsynchronisierung mit der Azure AD Connect-Synchronisierung](../hybrid/tshoot-connect-password-hash-synchronization.md). 

* Informationen zu Änderungen an den Connectorbereichen finden Sie unter [End-to-End-Problembehandlung bei Azure AD Connect-Objekten und -Attributen](/troubleshoot/azure/active-directory/troubleshoot-aad-connect-objects-attributes). 

**Wichtige Ressourcen für die Überwachung**

| Zu überwachende Elemente | Ressourcen |
| - | - |
| Überprüfung der Hashsynchronisierung|Siehe [Problembehandlung für die Kennworthashsynchronisierung mit der Azure AD Connect-Synchronisierung](../hybrid/tshoot-connect-password-hash-synchronization.md) |
 Änderungen an den Connectorbereichen|Siehe [End-to-End-Problembehandlung bei Azure AD Connect-Objekten und -Attributen](/troubleshoot/azure/active-directory/troubleshoot-aad-connect-objects-attributes) |
| Änderungen an den von Ihnen konfigurierten Regeln| Überwachen Sie insbesondere Filteränderungen, Änderungen an Domänen und Organisationseinheiten, Attributänderungen und gruppenbasierte Änderungen. |
| SQL- und MSDE-Änderungen | Änderungen an Protokollierungsparametern und Hinzufügung von benutzerdefinierten Funktionen |

**Überwachen Sie Folgendes**: 

| Zu überwachende Elemente| Risikostufe| Hierbei gilt:| Filter/Unterfilter| Notizen |
| - | - | - | - | - |
| Scheduler-Änderungen|Hoch | PowerShell| Set-ADSyncScheduler| Suchen Sie nach Änderungen am Zeitplan. |
| Änderungen an geplanten Aufgaben| Hoch | Azure AD-Überwachungsprotokolle| Aktivität = 4699(S): Eine geplante Aufgabe wurde gelöscht.<br>Oder<br>Aktivität = 4701(s): Eine geplante Aufgabe wurde deaktiviert.<br>Oder<br>Aktivität = 4701(s): Eine geplante Aufgabe wurde aktualisiert.| Überwachen Sie alle. |



* Weitere Informationen zum Protokollieren von PowerShell-Skriptvorgängen finden Sie unter [Aktivieren der Skriptblockprotokollierung](/powershell/module/microsoft.powershell.core/about/about_logging_windows) in der PowerShell-Referenzdokumentation.

* Weitere Informationen zum Konfigurieren der PowerShell-Protokollierung für die Analyse durch Splunk finden Sie unter [Get Data into Splunk User Behavior Analytics](https://docs.splunk.com/Documentation/UBA/5.0.4.1/GetDataIn/AddPowerShell) (Hinzufügen von Daten in Splunk User Behavior Analytics, in englischer Sprache).

### <a name="monitoring-seamless-single-sign-on"></a>Überwachen von nahtlosem einmaligem Anmelden

Mit dem nahtlosen einmaligen Anmelden von Azure Active Directory (Azure AD Seamless Single Sign-On) werden Benutzer automatisch angemeldet, wenn sie an ihren mit dem Unternehmensnetzwerk verbundenen Unternehmens-Desktops arbeiten. Nahtloses SSO ermöglicht Ihren Benutzern einen einfachen Zugriff auf Ihre cloudbasierten Anwendungen, ohne dass zusätzliche lokale Komponenten erforderlich sind. SSO verwendet die von Azure AD Connect bereitgestellten Funktionen für Passthrough-Authentifizierung und Kennworthashsynchronisierung.

Die Überwachung des einmaligen Anmeldens und der Kerberos-Aktivität kann Ihnen helfen, allgemeine Muster von Angriffen zum Diebstahl von Anmeldeinformationen zu erkennen. Verwenden Sie für die Überwachung die folgenden Informationen:

| Zu überwachende Elemente| Risikostufe| Hierbei gilt:| Filter/Unterfilter| Notizen |
| - | - | - | - | - |
| Fehler im Zusammenhang mit SSO- und Kerberos-Validierungsfehlern|Medium | Azure AD-Anmeldeprotokoll| | Eine Liste der Fehlercodes für einmaliges Anmelden finden Sie unter [Problembehandlung beim nahtlosen einmaligen Anmelden mit Azure Active Directory](../hybrid/tshoot-connect-sso.md). |
| Abfrage von Fehlern für die Problembehandlung|Medium | PowerShell| Siehe die Abfrage nach der Tabelle. Überprüfen Sie in jeder Gesamtstruktur mit aktiviertem SSO.| Überprüfen Sie in jeder Gesamtstruktur mit aktiviertem SSO. |
| Kerberos-Ereignisse|Hoch | Überwachung von Microsoft Defender for Identity| | Siehe den Leitfaden unter [Lateral Movement-Pfade (LMPs) für Microsoft Defender for Identity](/defender-for-identity/use-case-lateral-movement-path) |

```kusto
<QueryList>

<Query Id="0" Path="Security">

<Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>

</Query>

</QueryList>
```
## <a name="password-protection-policies"></a>Kennwortschutzrichtlinien

Wenn Sie Azure AD-Kennwortschutz bereitstellen, sind Überwachung und Berichterstattung wichtige Aufgaben. Die folgenden Links bieten detaillierte Informationen zum besseren Verständnis verschiedener Überwachungstechniken, einschließlich der Speicherorte, an denen die einzelnen Dienste Informationen protokollieren, und der Methoden zum Berichten über die Verwendung des Azure AD-Kennwortschutzes. 

Ereignisprotokollmeldungen werden sowohl vom Domänencontroller-Agent (DC) als auch von Proxydiensten protokolliert. Alle nachfolgend beschriebenen PowerShell-Cmdlets sind nur auf dem Proxyserver verfügbar (siehe PowerShell-Modul „AzureADPasswordProtection“). Die DC-Agent-Software installiert kein PowerShell-Modul.

Ausführliche Informationen zur Planung und Implementierung des lokalen Kennwortschutzes finden Sie unter [Planen und Bereitstellen des lokalen Azure AD-Kennwortschutzes](../authentication/howto-password-ban-bad-on-premises-deploy.md). Ausführliche Informationen zur Überwachung finden Sie unter [Überwachen und Überprüfen der Protokolle für lokale Umgebungen mit Azure AD-Kennwortschutz](../authentication/howto-password-ban-bad-on-premises-monitor.md). Die DC-Agent-Dienstsoftware schreibt auf jedem Domänencontroller die Ergebnisse der einzelnen Kennwortüberprüfungsvorgänge (und andere Status) in das folgende lokale Ereignisprotokoll:

* \Anwendungs- und Dienstprotokolle\Microsoft\AzureADPasswordProtection\DCAgent\Admin

* \Anwendungs- und Dienstprotokolle\Microsoft\AzureADPasswordProtection\DCAgent\Operational

* \Anwendungs- und Dienstprotokolle\Microsoft\AzureADPasswordProtection\DCAgent\Trace

Das DC-Agent-Administratorprotokoll ist die Hauptquelle für Informationen zum Verhalten der Software. Standardmäßig ist das Ablaufverfolgungsprotokoll deaktiviert und muss aktiviert werden, bevor Daten protokolliert werden. Ausführliche Informationen zur Behandlung von Anwendungsproxyproblemen und Fehlermeldungen finden Sie unter [Beheben von Problemen mit Anwendungsproxys und Fehlermeldungen](../app-proxy/application-proxy-troubleshoot.md). Informationen zu diesen Ereignissen werden in den folgenden Verzeichnissen protokolliert:

* Anwendungs- und Dienstprotokolle\Microsoft\AadApplicationProxy\Connector\Admin

* Azure AD-Überwachungsprotokoll, Kategorie „Anwendungsproxy“

Eine vollständige Referenz für Azure AD-Überwachungsaktivitäten finden Sie unter [Referenz zu Überwachungsaktivitäten von Azure AD](../reports-monitoring/reference-audit-activities.md). 

## <a name="next-steps"></a>Nächste Schritte


Weitere Informationen finden Sie in den folgenden Artikeln im Leitfaden zu Sicherheitsvorgängen:

[Azure AD: Übersicht über Sicherheitsvorgänge](security-operations-introduction.md)

[Sicherheitsvorgänge für Benutzerkonten](security-operations-user-accounts.md)

[Sicherheitsvorgänge für privilegierte Konten](security-operations-privileged-accounts.md)

[Sicherheitsvorgänge für Privileged Identity Management](security-operations-privileged-identity-management.md)

[Sicherheitsvorgänge für Anwendungen](security-operations-applications.md)

[Sicherheitsvorgänge für Geräte](security-operations-devices.md)
 
[Sicherheitsvorgänge für die Infrastruktur](security-operations-infrastructure.md)


 

  
‎
