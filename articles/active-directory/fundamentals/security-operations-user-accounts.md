---
title: Azure Active Directory-Sicherheitsvorgänge für Benutzerkonten
description: Leitfaden zum Einrichten von Baselines und zum Überwachen auf und Warnen bei potenziellen Sicherheitsproblemen mit Benutzerkonten.
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
ms.openlocfilehash: e386add0cab29ed0570f3b0b72d6e34cbb363849
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292559"
---
# <a name="azure-active-directory-security-operations-for-user-accounts"></a>Azure Active Directory-Sicherheitsvorgänge für Benutzerkonten

Benutzeridentität ist einer der wichtigsten Aspekte beim Schutz Ihrer Organisation und Daten. Dieser Artikel bietet einen Leitfaden zum Überwachen der Erstellung, Löschung und Nutzung von Konten. Im ersten Teil wird die Überwachung auf ungewöhnliche Kontoerstellung und -löschung behandelt. Der zweite Teil befasst sich mit der Überwachung ungewöhnlicher Kontonutzung. 

Wenn Sie die [Übersicht über Azure Active Directory-Sicherheitsvorgänge](security-operations-introduction.md) noch nicht gelesen haben, sollten Sie dies tun, ehe Sie fortfahren.

In diesem Artikel werden allgemeine Benutzerkonten behandelt. Informationen zu privilegierten Konten finden Sie unter „Sicherheitsvorgänge: privilegierte Konten“.

## <a name="define-a-baseline"></a>Definieren einer Baseline

Um anomales Verhalten zu ermitteln, müssen Sie zunächst definieren, wie normales und erwartetes Verhalten aussieht. Das Definieren des erwarteten Verhaltens für Ihre Organisation hilft Ihnen bei der Ermittlung, sobald unerwartetes Verhalten auftritt. Die Definition trägt auch dazu bei, die Menge falsch positiver Ergebnisse bei der Überwachungs- und Warnungsaktivität zu verringern. 

Nachdem Sie definiert haben, was Sie erwarten, führen Sie eine Baselineüberwachung durch, um Ihre Erwartungen zu überprüfen. Mit diesen Informationen können Sie die Protokolle auf alles überwachen, was außerhalb der von Ihnen festgelegten Toleranzen liegt. 

Verwenden Sie die Azure AD-Überwachungsprotokolle, Azure AD-Anmeldeprotokolle und -Verzeichnisattribute als Datenquellen für Konten, die außerhalb normaler Prozesse angelegt wurden. Die folgenden Vorschläge sollen Ihnen eine Hilfestellung bei der Definition von „normal“ für Ihre Organisation geben.

* **Erstellung eines Benutzerkontos**: Prüfen Sie Folgendes:

   * Strategie und Prinzipien für Tools und Prozesse, die zum Erstellen und Verwalten von Benutzerkonten verwendet werden. Gibt es beispielsweise Standardattribute, d. h. Formate, die auf Benutzerkontenattribute angewendet werden? 

  * Genehmigte Quellen für die Kontoerstellung. Beispielsweise Active Directory (AD), Azure Active Directory oder Personalmanagementsysteme wie Workday.

  * Warnungsstrategie für Konten, die außerhalb genehmigter Quellen erstellt wurden. Gibt es eine kontrollierte Liste der Organisationen, mit denen Ihre Organisation zusammenarbeitet?

  * Bereitstellung von Gastkonten und Warnungsparametern für Konten, die außerhalb der Berechtigungsverwaltung oder anderer normaler Prozesse erstellt wurden.

  * Strategie- und Warnungsparameter für Konten, die von einem Konto erstellt, geändert oder deaktiviert wurden, das keinem genehmigten Benutzeradministrator gehört.

  * Überwachungs- und Warnungsstrategie für Konten ohne Standardattribute, z. B. Mitarbeiter-ID oder Nichtbefolgung der Benennungskonventionen der Organisation.

  * Strategie, Prinzipien und Prozess zum Löschen und Aufbewahren von Konten.

* **Lokale Benutzerkonten**: Prüfen Sie Folgendes für Konten, die mit Azure AD Connect synchronisiert wurden:

  * Die Gesamtstrukturen, Domänen und Organisationseinheiten im Geltungsbereich der Synchronisierung. Wer sind die genehmigten Administratoren, die diese Einstellungen ändern können, und wie oft wird der Geltungsbereich überprüft?

  * Die Typen synchronisierter Konten. Beispiel: Benutzerkonten und/oder Dienstkonten. 

  * Der Prozess zum Erstellen privilegierter lokaler Konten und zum Steuern der Synchronisierung dieses Kontotyps.

  * Der Prozess zum Erstellen lokaler Konten und zum Verwalten der Synchronisierung dieses Kontotyps.

Weitere Informationen zum Absichern und Überwachen lokaler Konten finden Sie unter [Schützen von Microsoft 365 vor lokalen Angriffen](protect-m365-from-on-premises-attacks.md).

* **Cloudbenutzerkonten**: Prüfen Sie Folgendes:

  * Prozess zum Bereitstellen und Verwalten von Cloudkonten direkt in Azure AD.

  * Prozess zum Bestimmen der Benutzertypen, für die Azure AD-Cloudkonten bereitgestellt werden. Lassen Sie beispielsweise nur privilegierte Konten oder auch Benutzerkonten zu?

  * Prozess zum Erstellen und Verwalten einer Liste vertrauenswürdiger Personen und Prozesse, von denen das Erstellen und Verwalten von Cloudbenutzerkonten erwartet wird.

  * Prozess zum Erstellen und Verwalten einer Warnungsstrategie für nicht genehmigte Cloudkonten. 

## <a name="where-to-look"></a>Zu untersuchende Protokolle

Zur Untersuchung und Überwachung verwenden Sie die folgenden Protokolldateien: 

* [Azure AD-Überwachungsprotokolle](../reports-monitoring/concept-audit-logs.md)

* [Anmeldeprotokolle](../reports-monitoring/concept-all-sign-ins.md)

* [Microsoft 365-Überwachungsprotokolle](/microsoft-365/compliance/auditing-solutions-overview) 

* [Azure Key Vault-Protokolle](../../key-vault/general/logging.md?tabs=Vault)

* [Protokoll zu Risikobenutzern](../identity-protection/howto-identity-protection-investigate-risk.md)

* [UserRiskEvents-Protokoll](../identity-protection/howto-identity-protection-investigate-risk.md)

Im Azure-Portal können Sie die Azure AD-Überwachungsprotokolle anzeigen und als CSV- (durch Trennzeichen getrennte Werte) oder JSON-Dateien (JavaScript Object Notation) herunterladen. Das Azure-Portal bietet mehrere Möglichkeiten zur Integration von Azure AD-Protokollen in andere Tools, die eine umfassendere Automatisierung von Überwachung und Benachrichtigungen ermöglichen:

* **[Microsoft Sentinel](../../sentinel/overview.md)** : Ermöglicht intelligente Sicherheitsanalysen auf Unternehmensebene, indem SIEM-Funktionen (Security Information and Event Management) zur Verfügung gestellt werden. 

* **[Azure Monitor](../../azure-monitor/overview.md)** : ermöglicht die automatisierte Überwachung verschiedener Bedingungen und entsprechende Warnungen. Damit können Arbeitsmappen erstellt oder verwendet werden, um Daten aus verschiedenen Quellen zu kombinieren.

* **[Azure Event Hubs](../../event-hubs/event-hubs-about.md) mit Integration in ein SIEM-System**- [Azure AD-Protokolle können über die Azure Event Hub-Integration in andere SIEM-Systeme wie Splunk, ArcSight, QRadar und Sumo Logic integriert](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) werden.

* **[Microsoft Defender-für-Cloud-Apps](/cloud-app-security/what-is-cloud-app-security)** : Ermöglichen Ihnen die Erkennung und Verwaltung von Apps, die Steuerung von Apps und Ressourcen sowie die Überprüfung der Konformität Ihrer Cloud-Apps. 

Ein Großteil der Überwachung und zugehörigen Warnungen hängt von den Auswirkungen Ihrer Richtlinien für bedingten Zugriff ab. Sie können die Arbeitsmappe [Erkenntnisse und Berichterstellung zum bedingten Zugriff](../conditional-access/howto-conditional-access-insights-reporting.md) verwenden, um die Auswirkungen einer oder mehrerer Richtlinien für bedingten Zugriff auf Ihre Anmeldungen sowie die Ergebnisse von Richtlinien, einschließlich Gerätestatus, zu untersuchen. Diese Arbeitsmappe bietet Ihnen die Möglichkeit, eine Zusammenfassung der Auswirkungen anzuzeigen und die Auswirkungen über einen bestimmten Zeitraum zu ermitteln. Sie können mithilfe der Arbeitsmappe auch die Anmeldungen eines bestimmten Benutzers untersuchen. 

 Im restlichen Teil dieses Artikels wird beschrieben, was wir Ihnen zur Überwachung und für zugehörige Warnungen empfehlen, und zwar gegliedert nach der Art der Bedrohung. Wo es spezielle vordefinierte Lösungen gibt, verweisen wir auf diese oder stellen Beispiele im Anschluss an die Tabelle zur Verfügung. Andernfalls können Sie Warnungen mithilfe der oben genannten Tools erstellen. 

## <a name="account-creation"></a>Kontoerstellung

Eine anomale Kontoerstellung kann auf ein Sicherheitsproblem hinweisen. Kurzlebige, nicht den Benennungsstandards entsprechende und außerhalb normaler Prozesse erstellte Konten müssen untersucht werden.

### <a name="short-lived-accounts"></a>Kurzlebige Konten

Das Erstellen und Löschen von Konten außerhalb der normalen Identitätsverwaltungsprozesse muss in Azure AD überwacht werden. Kurzlebige Konten sind Konten, die innerhalb einer kurzen Zeitspanne erstellt und gelöscht werden. Diese Art der Kontoerstellung und des schnellen Löschens kann bedeuten, dass ein böswilliger Akteur versucht, die Erkennung zu vermeiden, indem er Konten erstellt, einsetzt und dann löscht.

Muster kurzlebiger Konten können darauf hindeuten, dass nicht genehmigte Personen oder Prozesse das Recht haben, Konten zu erstellen und zu löschen, die nicht den festgelegten Prozessen und Richtlinien entsprechen. Bei dieser Art von Verhalten werden sichtbare Marker aus dem Verzeichnis entfernt. 

Wenn der Datenpfad der Kontoerstellung und -löschung nicht schnell ermittelt wird, sind die für die Untersuchung eines Incidents erforderlichen Informationen möglicherweise nicht mehr vorhanden. Beispielsweise können Konten erst gelöscht und dann endgültig aus dem Papierkorb gelöscht werden. Aktivitätsprotokolle werden 30 Tage aufbewahrt. Sie können Ihre Protokolle jedoch zur längerfristigen Aufbewahrung in Azure Monitor oder eine SIEM-Lösung (Security Information and Event Management) exportieren. 

| Zu überwachende Elemente                                                  | Risikostufe | Hierbei gilt:               | Filter/Unterfilter                                                                                                                                         | Notizen                                                                                                                               |
|------------------------------------------------------------------|------------|---------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Ereignisse zur Erstellung und Löschung von Konten innerhalb eines engen Zeitrahmens.  | Hoch       | Azure AD-Überwachungsprotokolle | Aktivität: Benutzer hinzufügen<br>Status = Erfolg<br>- und -<br>Aktivität: Benutzer löschen<br>Status = Erfolg<br>                                                          | Suchen Sie nach UPN-bezogenen Ereignissen (User Principal Name, Benutzerprinzipalname). Achten Sie auf Konten, die in weniger als 24 Stunden erstellt und dann gelöscht werden.                          |
| Konten, die von nicht genehmigten Benutzern oder Prozessen erstellt und gelöscht wurden. | Medium     | Azure AD-Überwachungsprotokolle | Von (Akteur) initiiert: BENUTZERPRINZIPALNAME<br>- und -<br>Aktivität: Benutzer hinzufügen<br>Status = Erfolg<br>und/oder<br>Aktivität: Benutzer löschen<br>Status = Erfolg      | Wenn es sich bei dem Akteur um einen nicht genehmigten Benutzer handelt, konfigurieren Sie das Senden einer Warnung.                                                                    |
| Konten aus nicht genehmigten Quellen.                              | Medium     | Azure AD-Überwachungsprotokolle | Aktivität: Benutzer hinzufügen<br>Status = Erfolg<br>Ziele = BENUTZERPRINZIPALNAME                                                                                 | Wenn der Eintrag nicht aus einer genehmigten Domäne stammt oder zu einer bekannten blockierten Domäne gehört, konfigurieren Sie das Senden einer Warnung.                               |
| Konten, die einer privilegierten Rolle zugewiesen sind.                          | Hoch       | Azure AD-Überwachungsprotokolle | Aktivität: Benutzer hinzufügen<br>Status = Erfolg<br>- und -<br>Aktivität: Benutzer löschen<br>Status = Erfolg<br>- und -<br>Aktivität: Mitglied zu Rolle hinzufügen<br>Status = Erfolg | Wenn das Konto einer Azure AD-Rolle, einer Azure-Rolle oder einer privilegierten Gruppe zugewiesen ist, geben Sie eine Warnung aus, und priorisieren Sie die Untersuchung. |

Sowohl privilegierte als auch nicht privilegierte Konten müssen überwacht und mit entsprechenden Warnungen versehen werden. Da privilegierte Konten jedoch über administrative Berechtigungen verfügen, müssen sie in Ihren Überwachungs-, Warn- und Reaktionsprozessen höhere Priorität haben. 

### <a name="accounts-not-following-naming-policies"></a>Konten, die Benennungsrichtlinien nicht befolgen

Benutzerkonten, die Benennungsrichtlinien nicht befolgen, wurden möglicherweise unter Umgehung von Organisationsrichtlinien erstellt. 

Eine bewährte Methode ist eine Benennungsrichtlinie für Benutzerobjekte. Eine Benennungsrichtlinie erleichtert die Verwaltung und sorgt für Konsistenz. Die Richtlinie kann auch helfen zu ermitteln, wann Benutzer außerhalb genehmigter Prozesse erstellt wurden. Ein böswilliger Akteur ist sich möglicherweise nicht über Ihre Namensstandards im Klaren, was die Erkennung eines Kontos, das außerhalb Ihrer Organisationsprozesse bereitgestellt wurde, erleichtern kann.

In Organisationen gibt es in der Regel bestimmte Formate und Attribute, die für die Erstellung von Benutzer- oder privilegierten Konten verwendet werden. Zum Beispiel:

* UPN von Administratorkonten = ADM_firstname.lastname@tenant.onmicrosoft.com

* UPN von Benutzerkonten = Firstname.Lastname@contoso.com

Benutzerkonten verfügen häufig auch über ein Attribut, das einen realen Benutzer identifiziert. Beispiel: EMPID = XXXNNN. Die folgenden Vorschläge sollen Ihnen bei der Definition dessen helfen, was für Ihre Organisation normal ist, und was Sie bei der Festlegung Ihrer Baseline für Protokolleinträge berücksichtigen sollten, wenn Konten nicht der Benennungskonvention Ihrer Organisation entsprechen:

* Konten, die nicht der Benennungskonvention entsprechen. Beispielsweise `nnnnnnn@contoso.com` im Vergleich zu `firstname.lastname@contoso.com`. 

* Konten, deren Standardattribute nicht ausgefüllt sind oder die nicht das richtige Format haben. Beispielsweise fehlende gültige Mitarbeiter-ID.

| Zu überwachende Elemente| Risikostufe| Hierbei gilt:| Filter/Unterfilter| Notizen |
| - | - | - | - | - |
| Benutzerkonten, bei denen erwartete Attribute nicht definiert sind.| Niedrig| Azure AD-Überwachungsprotokolle| Aktivität: Benutzer hinzufügen<br>Status = Erfolg| Suchen Sie nach Konten, bei denen Ihre Standardattribute entweder NULL sind oder das falsche Format haben. Beispiel: EmployeeID |
| Benutzerkonten, die mit einem falschen Benennungsformat erstellt wurden.| Niedrig| Azure AD-Überwachungsprotokolle| Aktivität: Benutzer hinzufügen<br>Status = Erfolg| Suchen Sie nach Konten mit einem UPN, der nicht Ihrer Benennungsrichtlinie entspricht. |
| Privilegierte Konten, die nicht der Benennungsrichtlinie folgen.| Hoch| Azure-Abonnement| [Auflisten von Azure-Rollenzuweisungen mithilfe des Azure-Portals – Azure RBAC](../../role-based-access-control/role-assignments-list-portal.md)| Listen Sie Rollenzuweisungen für Abonnements auf, und warnen Sie, wenn der Anmeldename nicht dem Format Ihrer Organisation entspricht. Beispiel: ADM_ als Präfix. |
| Privilegierte Konten, die nicht der Benennungsrichtlinie folgen.| Hoch| Azure AD-Verzeichnis| [Auflisten von Azure AD-Rollenzuweisungen](../roles/view-assignments.md)| Listen Sie Zuweisungen für Azure AD-Rollen auf, und warnen Sie, wenn der UPN nicht mit dem Format Ihrer Organisation übereinstimmt. Beispiel: ADM_ als Präfix. |



Weitere Informationen zur Analyse finden Sie unter:

* Für Azure AD-Überwachungsprotokolle: [Analysieren von Textdaten in Azure Monitor-Protokollen](../../azure-monitor/logs/parse-text.md)

* Für Azure-Abonnements: [Auflisten von Azure-Rollenzuweisungen mit Azure PowerShell ](../../role-based-access-control/role-assignments-list-powershell.md)

* Für Azure Active Directory: [Auflisten von Azure AD-Rollenzuweisungen](../roles/view-assignments.md) 

### <a name="accounts-created-outside-normal-processes"></a>Außerhalb normaler Prozesse erstellte Konten

Standardprozesse zum Erstellen von Benutzern und privilegierten Konten sind wichtig, damit Sie den Lebenszyklus von Identitäten sicher steuern können. Wenn Benutzer außerhalb eingerichteter Prozesse bereitgestellt werden bzw. ihre Bereitstellung aufgehoben wird, kann dies zu Sicherheitsrisiken führen. Ein Betrieb außerhalb etablierter Prozesse kann auch Probleme bei der Identitätsverwaltung verursachen. Mögliche Risiken:

* Benutzer- und privilegierte Konten unterliegen möglicherweise nicht den Richtlinien der Organisation. Dies kann zu einer größeren Angriffsfläche für Konten führen, die nicht ordnungsgemäß verwaltet werden.

* Es wird schwieriger zu erkennen, wenn böswillige Akteure Konten für böswillige Zwecke erstellen. Wenn gültige Konten außerhalb etablierter Verfahren erstellt werden, wird es schwieriger zu erkennen, wann für böswillige Zwecke Konten erstellt oder Berechtigungen geändert werden. 

Es wird empfohlen, Benutzer- und privilegierte Konten nur gemäß Ihren Organisationsrichtlinien zu erstellen. Beispielsweise sollte ein Konto gemäß den ordnungsgemäßen Benennungsstandards, Organisationsinformationen und im Rahmen der entsprechenden Identitätsgovernance erstellt werden. Organisationen sollten streng kontrollieren, wer Rechte zum Erstellen, Verwalten und Löschen von Identitäten hat. Rollen zur Erstellung dieser Konten sollten streng geregelt werden, und die Rechte sollten nur unter Befolgung eines festgelegten Workflows zur Genehmigung und zum Bezug dieser Berechtigungen verfügbar sein.

| Zu überwachende Elemente| Risikostufe| Hierbei gilt:| Filter/Unterfilter| Notizen |
| - | - | - | - | - |
| Benutzerkonten, die von nicht genehmigten Benutzern oder Prozessen erstellt und gelöscht wurden.| Medium| Azure AD-Überwachungsprotokolle| Aktivität: Benutzer hinzufügen<br>Status = Erfolg<br>und/oder<br>Aktivität: Benutzer löschen<br>Status = Erfolg<br>- und -<br>Von (Akteur) initiiert: BENUTZERPRINZIPALNAME| Warnung bei Konten, die von nicht genehmigten Benutzern oder Prozessen erstellt wurden. Priorisieren Sie Konten, die mit erhöhten Berechtigungen erstellt wurden. |
| Benutzerkonten, die von nicht genehmigten Quellen erstellt oder gelöscht wurden.| Medium| Azure AD-Überwachungsprotokolle| Aktivität: Benutzer hinzufügen<br>Status = Erfolg<br>Oder<br>Aktivität: Benutzer löschen<br>Status = Erfolg<br>- und -<br>Ziele = BENUTZERPRINZIPALNAME| Warnung, wenn die Domäne nicht genehmigt oder als blockierte Domäne bekannt ist. |


## <a name="unusual-sign-ins"></a>Ungewöhnliche Anmeldungen

Fehler bei der Benutzerauthentifizierung sind normal. Aber das Erkennen von Mustern oder Häufungen von Fehlern kann ein Hinweis darauf sein, dass mit der Identität eines Benutzers etwas nicht stimmt. Dies kann beispielsweise aufgrund von Kennwortspray- oder Brute-Force-Angriffen oder kompromittierten Benutzerkonten der Fall sein. Eine entsprechende Überwachung und Warnung ist bei Auftreten von Mustern unbedingt erforderlich. Dadurch wird sichergestellt, dass Sie die Benutzer und Daten in Ihrer Organisation schützen können. 

Eine Erfolgsmeldung scheint zu bedeuten, dass alles in Ordnung ist. Sie kann jedoch auch bedeuten, dass ein böswilliger Akteur erfolgreich auf einen Dienst zugegriffen hat. Die Überwachung erfolgreicher Anmeldungen hilft Ihnen dabei, Benutzerkonten aufzuspüren, die zwar Zugriff erhalten, aber eigentlich keinen Zugriff haben sollten. Erfolgreiche Benutzerauthentifizierungen sind normale Einträge in Azure AD-Anmeldeprotokollen. Wir empfehlen eine Überwachung auf und Warnung bei auftretenden Mustern. Dadurch wird sichergestellt, dass Sie die Benutzerkonten und Daten in Ihrer Organisation schützen können. 


Sobald Sie eine Protokollüberwachungs- und Warnstrategie entwerfen und umsetzen möchten, sollten Sie die Tools im Azure-Portal berücksichtigen. Mit Azure AD Identity Protection können Sie Erkennung, Schutz und Korrektur identitätsbasierter Risiken automatisieren. Identity Protection nutzt mit Daten gefütterte Machine Learning- und heuristische Systeme, um Risiken zu erkennen und Benutzern und Anmeldungen einen Risikopunktwert zuzuweisen. Kunden können Richtlinien auf Grundlage einer Risikostufe konfigurieren, um Zugriff zuzulassen oder zu verweigern oder dem Benutzer die Möglichkeit zu geben, ein Risiko auf sichere Weise selbst zu beseitigen. Die folgenden Identity Protection-Risikoerkennungen informieren derzeit über Risikostufen:

| Zu überwachende Elemente | Risikostufe | Hierbei gilt: | Filter/Unterfilter | Notizen |
| - | - | - | - | - |
| Erkennung von Benutzerrisiken: kompromittierte Anmeldeinformationen| Hoch| Azure AD-Risikoerkennungsprotokolle| Benutzererfahrung: kompromittierte Anmeldeinformationen <br><br>API: Weitere Informationen finden Sie unter [Ressourcentyp riskDetection: Microsoft Graph-Betaversion](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Weitere Informationen finden Sie unter [Was ist Risiko? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Azure AD Threat Intelligence: Erkennung von Benutzerrisiken| Hoch| Azure AD-Risikoerkennungsprotokolle| Benutzererfahrung: Azure AD Threat Intelligence <br><br>API: Weitere Informationen finden Sie unter [Ressourcentyp riskDetection: Microsoft Graph-Betaversion](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Weitere Informationen finden Sie unter [Was ist Risiko? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Risikoerkennung: Anmeldung mit anonymer IP-Adresse| Varies| Azure AD-Risikoerkennungsprotokolle| Benutzererfahrung: anonyme IP-Adresse <br><br>API: Weitere Informationen finden Sie unter [Ressourcentyp riskDetection: Microsoft Graph-Betaversion](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Weitere Informationen finden Sie unter [Was ist Risiko? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Risikoerkennung: ungewöhnlicher Ortswechsel bei der Anmeldung| Varies| Azure AD-Risikoerkennungsprotokolle| Benutzererfahrung: ungewöhnlicher Ortswechsel <br><br>API: Weitere Informationen finden Sie unter [Ressourcentyp riskDetection: Microsoft Graph-Betaversion](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Weitere Informationen finden Sie unter [Was ist Risiko? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Anomales Token| Varies| Azure AD-Risikoerkennungsprotokolle| Benutzererfahrung: anomales Token <br><br>API: Weitere Informationen finden Sie unter [Ressourcentyp riskDetection: Microsoft Graph-Betaversion](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Weitere Informationen finden Sie unter [Was ist Risiko? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Risikoerkennung: mit Schadsoftware verknüpfte IP-Adresse für Anmeldung| Varies| Azure AD-Risikoerkennungsprotokolle| Benutzererfahrung: mit Schadsoftware verknüpfte IP-Adresse <br><br>API: Weitere Informationen finden Sie unter [Ressourcentyp riskDetection: Microsoft Graph-Betaversion](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Weitere Informationen finden Sie unter [Was ist Risiko? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Risikoerkennung: Anmeldung bei verdächtigem Browser| Varies| Azure AD-Risikoerkennungsprotokolle| Benutzererfahrung: verdächtiger Browser <br><br>API: Weitere Informationen finden Sie unter [Ressourcentyp riskDetection: Microsoft Graph-Betaversion](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Weitere Informationen finden Sie unter [Was ist Risiko? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Risikoerkennung: ungewöhnliche Anmeldeeigenschaften bei der Anmeldung| Varies| Azure AD-Risikoerkennungsprotokolle| Benutzererfahrung: ungewöhnliche Anmeldeeigenschaften <br><br>API: Weitere Informationen finden Sie unter [Ressourcentyp riskDetection: Microsoft Graph-Betaversion](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Weitere Informationen finden Sie unter [Was ist Risiko? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Risikoerkennung: Anmeldung mit schädlicher IP-Adresse| Varies| Azure AD-Risikoerkennungsprotokolle| Benutzererfahrung: schädliche IP-Adresse<br><br>API: Weitere Informationen finden Sie unter [Ressourcentyp riskDetection: Microsoft Graph-Betaversion](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Weitere Informationen finden Sie unter [Was ist Risiko? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Risikoerkennung: verdächtige Regeln zur Posteingangsänderung| Varies| Azure AD-Risikoerkennungsprotokolle| Benutzererfahrung: verdächtige Regeln zur Posteingangsänderung<br><br>API: Weitere Informationen finden Sie unter [Ressourcentyp riskDetection: Microsoft Graph-Betaversion](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Weitere Informationen finden Sie unter [Was ist Risiko? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Risikoerkennung: Kennwort-Spray-Anmeldung| Hoch| Azure AD-Risikoerkennungsprotokolle| Benutzererfahrung: Kennwortspray<br><br>API: Weitere Informationen finden Sie unter [Ressourcentyp riskDetection: Microsoft Graph-Betaversion](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Weitere Informationen finden Sie unter [Was ist Risiko? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Risikoerkennung: unmöglicher Ortswechsel bei der Anmeldung| Varies| Azure AD-Risikoerkennungsprotokolle| Benutzererfahrung: unmöglicher Ortswechsel<br><br>API: Weitere Informationen finden Sie unter [Ressourcentyp riskDetection: Microsoft Graph-Betaversion](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Weitere Informationen finden Sie unter [Was ist Risiko? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Risikoerkennung: Anmeldung aus neuem Land/neuer Region| Varies| Azure AD-Risikoerkennungsprotokolle| Benutzererfahrung: neues Land/neue Region<br><br>API: Weitere Informationen finden Sie unter [Ressourcentyp riskDetection: Microsoft Graph-Betaversion](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Weitere Informationen finden Sie unter [Was ist Risiko? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Risikoerkennung: Aktivität über anonyme IP-Adresse| Varies| Azure AD-Risikoerkennungsprotokolle| Benutzererfahrung: Aktivität über anonyme IP-Adresse<br><br>API: Weitere Informationen finden Sie unter [Ressourcentyp riskDetection: Microsoft Graph-Betaversion](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Weitere Informationen finden Sie unter [Was ist Risiko? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Risikoerkennung: verdächtige Weiterleitung des Posteingangs| Varies| Azure AD-Risikoerkennungsprotokolle| Benutzererfahrung: verdächtige Weiterleitung des Posteingangs<br><br>API: Weitere Informationen finden Sie unter [Ressourcentyp riskDetection: Microsoft Graph-Betaversion](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Weitere Informationen finden Sie unter [Was ist Risiko? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Risikoerkennung: Azure AD Threat Intelligence-Anmeldung| Hoch| Azure AD-Risikoerkennungsprotokolle| Benutzererfahrung: Azure AD Threat Intelligence<br>API: Weitere Informationen finden Sie unter [Ressourcentyp riskDetection: Microsoft Graph-Betaversion](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Weitere Informationen finden Sie unter [Was ist Risiko? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |

Weitere Informationen finden Sie unter [Was ist Identity Protection?](../identity-protection/overview-identity-protection.md). 


### <a name="what-to-look-for"></a>Suchschwerpunkte

Konfigurieren Sie die Überwachung der Daten in den Azure AD-Anmeldeprotokollen, um sicherzustellen, dass Warnungen ausgegeben und die Sicherheitsrichtlinien Ihrer Organisation eingehalten werden. Einige Beispiele:

* **Fehlgeschlagene Authentifizierungen**: Wie alle Menschen machen wir auch einmal Fehler bei der Kennworteingabe. Viele fehlgeschlagene Authentifizierungen können jedoch darauf hindeuten, dass ein böswilliger Akteur versucht, sich Zugriff zu verschaffen. Angriffe sind unterschiedlich intensiv und können von wenigen Versuchen pro Stunde bis hin zu einer deutlich höheren Rate reichen. Bei einem Kennwort-Spray-Angriff wird versucht, sich mit den meistverwendeten Kennwörtern bei vielen verschiedenen Konten anzumelden, während bei einem Brute-Force-Angriff sehr viele Kennwörter für gezielt ausgewählte Konten ausprobiert werden. 

* **Unterbrochene Authentifizierungen**: Eine Unterbrechung in Azure AD stellt eine Einschaltung eines zusätzlichen Prozesses zur Erfüllung der Authentifizierungsanforderungen dar, z. B. bei Erzwingung einer Kontrolle in einer Zertifizierungsstellenrichtlinie. Dies ist ein normales Ereignis und kann vorkommen, wenn Anwendungen nicht ordnungsgemäß konfiguriert sind. Wenn jedoch viele Unterbrechungen für ein Benutzerkonto erkennbar werden, kann dies darauf hindeuten, dass mit diesem Konto etwas nicht stimmt. 

  * Wenn Sie beispielsweise in den Anmeldeprotokollen nach einem Benutzer gefiltert haben und eine große Anzahl von Anmeldungen mit „Status = Unterbrochen“ und „Bedingter Zugriff = Fehler“ sehen. Bei genauerer Betrachtung könnten die Authentifizierungsdetails zeigen, dass das Kennwort zwar stimmt, aber eine strenge Authentifizierung erforderlich ist. Dies kann bedeuten, dass der Benutzer die mehrstufige Authentifizierung (MFA) nicht abschließt, was wiederum bedeuten könnte, dass das Kennwort des Benutzers kompromittiert ist und der böswillige Akteur nicht in der Lage ist, die MFA abzuschließen.

* **Intelligente Sperrung**: Azure AD bietet einen intelligenten Sperrdienst, mit dem das Konzept vertrauter und nicht vertrauter Orte in den Authentifizierungsprozess eingeführt wurde. Ein Benutzerkonto, das einen vertrauten Ort besucht, kann sich erfolgreich authentifizieren, während ein böswilliger Akteur, der am selben Ort nicht vertraut ist, nach mehreren Versuchen gesperrt wird. Suchen Sie nach Konten, die gesperrt wurden, und untersuchen Sie sie weiter. 

* **Änderungen an IP-Adressen**: Es ist normal, dass Benutzeraktivitäten von unterschiedlichen IP-Adressen stammen. Der Zero-Trust-Ansatz steht jedoch unter dem Motto „Niemals vertrauen, immer überprüfen“. Das Erkennen einer großen Anzahl von IP-Adressen und fehlgeschlagener Anmeldungen kann ein Hinweis auf einen Eindringversuch sein. Suchen Sie nach einem Muster vieler fehlgeschlagener Authentifizierungen, die von mehreren IP-Adressen stammen. Beachten Sie, dass VPN-Verbindungen (virtuelles privates Netzwerk) falsch positive Ergebnisse verursachen können. Unabhängig von den Herausforderungen empfehlen wir Ihnen, auf Änderungen an IP-Adressen zu achten und nach Möglichkeit Azure AD Identity Protection einzusetzen, um diese Risiken automatisch zu erkennen und zu entschärfen.

* **Orte**: Im Allgemeinen erwarten Sie, dass sich ein Benutzerkonto am gleichen geografischen Ort befindet. Sie erwarten auch Anmeldungen von Orten, an denen Sie Mitarbeiter oder Geschäftsbeziehungen haben. Wenn die Anmeldung des Benutzerkontos von einem anderen internationalen Ort aus in kürzerer Zeit erfolgt, als für die Reise dorthin erforderlich wäre, kann dies ein Hinweis auf Missbrauch des Benutzerkontos sein. Beachten Sie, dass VPNs falsch positive Ergebnisse verursachen können. Es wird empfohlen, die Anmeldung von Benutzerkonten von geografisch entfernten Orten aus zu überwachen und nach Möglichkeit Azure AD Identity Protection einzusetzen, um diese Risiken automatisch zu erkennen und zu entschärfen.

Für diesen Risikobereich empfehlen wir, sowohl Standardbenutzerkonten als auch privilegierte Konten zu überwachen, wobei die Untersuchung privilegierter Konten Vorrang hat. Privilegierte Konten sind die wichtigsten Konten in jedem Azure AD-Mandanten. Einen spezifischen Leitfaden für privilegierte Konten finden Sie unter „Sicherheitsvorgänge: privilegierte Konten“. 

### <a name="how-to-detect"></a>Vorgehensweise zum Erkennen eines Ausfalls von Azure Storage

Sie nutzen Azure Identity Protection und die Azure AD-Anmeldeprotokolle, um Bedrohungen zu erkennen, die durch ungewöhnliche Anmeldungsmerkmale gekennzeichnet sind. Informationen zu Identity Protection finden Sie unter [Was ist Identity Protection?](../identity-protection/overview-identity-protection.md). Sie können die Daten auch zu Überwachungs- und Warnungszwecken in Azure Monitor oder ein SIEM-System replizieren. Um den Normalzustand für Ihre Umgebung zu definieren und eine Baseline festzulegen, bestimmen Sie Folgendes:

* die Parameter, die Sie für Ihren Benutzerstamm als normal betrachten.

* die durchschnittliche Anzahl von Versuchen der Kennworteingabe über einen Zeitraum, bevor der Benutzer sich an den Helpdesk wendet oder eine Self-Service-Kennwortzurücksetzung durchführt.

* die Anzahl fehlgeschlagener Versuche, die Sie vor einer Warnung zulassen möchten, und ob diese für Benutzerkonten und privilegierte Konten unterschiedlich sein soll.

* die Anzahl von MFA-Versuchen, die Sie vor einer Warnung zulassen möchten, und ob diese für Benutzerkonten und privilegierte Konten unterschiedlich sein soll.

* ob eine Legacyauthentifizierung aktiviert ist und Ihre Roadmap für die Einstellung ihrer Nutzung. 

* ob die bekannten ausgehenden IP-Adressen zu Ihrer Organisation gehören.

* die Länder, in denen Ihre Benutzer arbeiten.

* ob es Gruppen von Benutzern gibt, die fest an einem Netzstandort oder in einem Land verbleiben.

* Geben Sie alle anderen Indikatoren für ungewöhnliche Anmeldungen an, die für Ihre Organisation spezifisch sind. Beispiele hierfür sind Tage oder Zeiten der Woche oder des Jahres, an denen der Betrieb in Ihrer Organisation ruht.

Sobald Sie festgelegt haben, was für die verschiedenen Arten von Konten in Ihrer Umgebung normal ist, sollten Sie die folgenden Punkte berücksichtigen, um zu bestimmen, welche Szenarien Sie überwachen und bei denen Sie eine Warnung ausgeben möchten, und um Ihre Warnungen zu optimieren.

* Ist eine Überwachung und Warnung erforderlich, wenn Identity Protection konfiguriert ist?

* Gelten strengere Bedingungen für privilegierte Konten, die Sie zur Überwachung und Warnung nutzen können? Beispielsweise kann die Anforderung privilegierter Konten nur von vertrauenswürdigen IP-Adressen erfolgen.

* Sind die von Ihnen festgelegten Baselines zu aggressiv? Zu viele Warnungen können dazu führen, dass Warnungen ignoriert oder übersehen werden.

Konfigurieren Sie Identity Protection, um sicherzustellen, dass Schutz so besteht, dass die Richtlinien Ihrer Sicherheitsbaseline erfüllt werden. Beispiel: Blockieren von Benutzern, wenn das Risiko hoch ist. Diese Risikostufe gibt mit einem hohen Maß an Konfidenz an, dass ein Benutzerkonto kompromittiert ist. Weitere Informationen zum Einrichten von Richtlinien für Anmelde- und Benutzerrisiken finden Sie unter [Identity Protection-Richtlinien](../identity-protection/concept-identity-protection-policies.md). Weitere Informationen zum Einrichten des bedingten Zugriffs finden Sie unter [Bedingter Zugriff: Risikobasierter bedingter Zugriff beim Anmelden](../conditional-access/howto-conditional-access-policy-risk.md).

Die folgenden Einträge sind nach Auswirkung und Schweregrad in der Reihenfolge ihrer Bedeutung aufgeführt.

### <a name="monitoring-for-failed-unusual-sign-ins"></a>Überwachung auf fehlgeschlagene ungewöhnliche Anmeldungen

| Zu überwachende Elemente| Risikostufe| Hierbei gilt:| Filter/Unterfilter| Notizen |
| - |- |- |- |- |
| Fehlgeschlagene Anmeldeversuche.| Mittel, falls isolierter Incident<br>Hoch, wenn eine Reihe von Konten das gleiche Muster oder eine VIP aufweist.| Azure AD-Anmeldungsprotokoll| Status = fehlgeschlagen<br>- und -<br>Anmeldefehlercode 50126: <br>Fehler beim Überprüfen der Anmeldeinformationen aufgrund eines ungültigen Benutzernamens oder Kennworts.| Legen Sie einen Basisschwellenwert fest, den Sie dann überwachen und entsprechend dem Verhalten Ihres Unternehmens anpassen können, um zu verhindern, dass Fehlwarnungen generiert werden. |
| Intelligente Sperrereignisse.| Mittel, falls isolierter Incident<br>Hoch, wenn eine Reihe von Konten das gleiche Muster oder eine VIP aufweist.| Azure AD-Anmeldungsprotokoll| Status = fehlgeschlagen<br>- und -<br>Anmeldefehlercode = 50053: IdsLocked| Legen Sie einen Basisschwellenwert fest, den Sie dann überwachen und entsprechend dem Verhalten Ihres Unternehmens anpassen können, um zu verhindern, dass Fehlwarnungen generiert werden. |
| Interrupts| Mittel, falls isolierter Incident<br>Hoch, wenn eine Reihe von Konten das gleiche Muster oder eine VIP aufweist.| Azure AD-Anmeldungsprotokoll| 500121, Fehler bei der Authentifizierung während Anforderung einer strengen Authentifizierung. <br>Oder<br>50097, Geräteauthentifizierung erforderlich, oder 50074, Strenge Authentifizierung erforderlich. <br>Oder<br>50155, DeviceAuthenticationFailed<br>Oder<br>50158, ExternalSecurityChallenge:Externe Sicherheitsabfrage nicht erfüllt<br>Oder<br>53003 und Fehlergrund = von Zertifizierungsstelle blockiert| Überwachen und Senden von Warnungen bei Unterbrechungen<br>Legen Sie einen Basisschwellenwert fest, den Sie dann überwachen und entsprechend dem Verhalten Ihres Unternehmens anpassen können, um zu verhindern, dass Fehlwarnungen generiert werden. |


Die folgenden Einträge sind nach Auswirkung und Schweregrad in der Reihenfolge ihrer Bedeutung aufgeführt.

| Zu überwachende Elemente| Risikostufe| Hierbei gilt:| Filter/Unterfilter| Notizen |
| - |- |- |- |- |
| Betrugswarnungen bei mehrstufiger Authentifizierung (Multi-Factor Authentication, MFA).| Hoch| Azure AD-Anmeldungsprotokoll| Status = fehlgeschlagen<br>- und -<br>Details = MFA verweigert<br>| Überwachen und bei jedem Eintrag warnen. |
| Fehlgeschlagene Authentifizierungen aus Ländern, in denen Sie nicht tätig sind.| Medium| Azure AD-Anmeldungsprotokoll| Ort = \<unapproved location\>| Überwachen und bei jedem Eintrag warnen. |
| Fehlgeschlagene Authentifizierungen für Legacy- oder nicht verwendete Protokolle.| Medium| Azure AD-Anmeldeprotokoll| Status = Fehler<br>- und -<br>Client-App = andere Clients, POP, IMAP, MAPI, SMTP, ActiveSync| Überwachen und bei jedem Eintrag warnen. |
| Fehler aufgrund von Blockierung durch Zertifizierungsstelle.| Medium| Azure AD-Anmeldungsprotokoll| Fehlercode = 53003 <br>- und -<br>Fehlergrund = von Zertifizierungsstelle blockiert| Überwachen und bei jedem Eintrag warnen. |
| Vermehrte fehlgeschlagene Authentifizierungen beliebiger Art.| Medium| Azure AD-Anmeldungsprotokoll| Erfassen Sie die Zunahme von Fehlern in allen Bereichen. Beispiel: die Gesamtanzahl der Fehler heute ist >10 % als am gleichen Tag der Vorwoche.| Wenn Sie keinen Schwellenwert festgelegt haben, überwachen und warnen Sie, wenn Fehler um 10 % oder mehr zunehmen. |
| Die Authentifizierung erfolgt zu Zeiten und Tagen der Woche, an denen in Ländern kein normaler Geschäftsbetrieb herrscht.| Niedrig| Azure AD-Anmeldungsprotokoll| Erfassen Sie die interaktive Authentifizierung, die außerhalb der normalen Geschäftstage/-zeiten erfolgt. <br>Status = Erfolg<br>- und -<br>Ort = \<location\><br>- und -<br>Tag/Uhrzeit = \<not normal working hours\>| Überwachen und bei jedem Eintrag warnen. |
| Konto für Anmeldungen deaktiviert/blockiert| Niedrig| Azure AD-Anmeldeprotokoll| Status = Fehler<br>- und -<br>Fehlercode = 50057, Das Benutzerkonto ist deaktiviert.| Dies kann darauf hindeuten, dass jemand versucht, sich Zugriff auf ein Konto zu verschaffen, nachdem er eine Organisation verlassen hat. Obwohl das Konto blockiert ist, ist es dennoch wichtig, diese Aktivität zu protokollieren und eine Warnung auszugeben. |


### <a name="monitoring-for-successful-unusual-sign-ins"></a>Überwachung auf erfolgreiche ungewöhnliche Anmeldungen

 | Zu überwachende Elemente| Risikostufe| Hierbei gilt:| Filter/Unterfilter| Notizen |
| - |- |- |- |- |
| Authentifizierungen privilegierter Konten außerhalb der erwarteten Kontrollmechanismen.| Hoch| Azure AD-Anmeldungsprotokoll| Status = Erfolg<br>- und -<br>UserPricipalName = \<Admin account\><br>- und -<br>Ort = \<unapproved location\><br>- und -<br>IP-Adresse = \<unapproved IP\><br>Geräteinformationen = \<unapproved Browser, Operating System\><br>| Überwachen und warnen Sie bei erfolgreicher Authentifizierung für privilegierte Konten außerhalb der erwarteten Kontrollmechanismen. Drei allgemeine Kontrollmechanismen sind aufgeführt. |
| Wenn nur eine einstufige Authentifizierung erforderlich ist.| Niedrig| Azure AD-Anmeldungsprotokoll| Status = Erfolg<br>Authentifizierungsanforderung = einstufige Authentifizierung| Überwachen Sie dies in regelmäßigen Abständen, und stellen Sie sicher, dass dies das erwartete Verhalten ist. |
| Ermitteln Sie privilegierte Konten, die nicht für MFA registriert sind.| Hoch| Azure Graph-API| Abfrage von IsMFARegistered ist für Administratorkonten FALSE. <br>[Auflisten von credentialUserRegistrationDetails_ Microsoft Graph-Betaversion | Microsoft Docs](/graph/api/reportroot-list-credentialuserregistrationdetails?view=graph-rest-beta&preserve-view=true&tabs=http)| Überwachen und untersuchen Sie, um zu ermitteln, ob Absicht oder ein Versehen vorliegt. |
| Erfolgreiche Authentifizierungen aus Ländern, in denen Ihre Organisation nicht tätig ist.| Medium| Azure AD-Anmeldungsprotokoll| Status = Erfolg<br>Ort = \<unapproved country\>| Überwachen und warnen Sie bei Einträgen, die nicht den von Ihnen angegebenen Ortsnamen entsprechen. |
| Erfolgreiche Authentifizierung, Sitzung durch Zertifizierungsstelle blockiert.| Medium| Azure AD-Anmeldungsprotokoll| Status = Erfolg<br>- und -<br>Fehlercode = 53003 – Fehlergrund, von Zertifizierungsstelle blockiert| Überwachen und untersuchen Sie, wenn die Authentifizierung zwar erfolgreich ist, aber die Sitzung von der Zertifizierungsstelle blockiert wird. |
| Erfolgreiche Authentifizierung, nachdem Sie die Legacyauthentifizierung deaktiviert haben.| Medium| Azure AD-Anmeldungsprotokoll| Status = Erfolg <br>- und -<br>Client-App = andere Clients, POP, IMAP, MAPI, SMTP, ActiveSync| Wenn Ihre Organisation die Legacyauthentifizierung deaktiviert hat, überwachen und warnen Sie, wenn eine erfolgreiche Legacyauthentifizierung erfolgt ist. |


Wir empfehlen Ihnen, regelmäßig die Authentifizierungen für Anwendungen mit mittleren (MBI) und erheblichen Geschäftsauswirkungen (HBI) zu überprüfen, bei denen nur eine einstufige Authentifizierung erforderlich ist. Stellen Sie jeweils fest, ob eine einstufige Authentifizierung erwartet wurde oder nicht. Überprüfen Sie außerdem basierend auf dem Ort, ob die Authentifizierung erfolgreich oder zu unerwarteten Zeiten erfolgreich war. 

| Zu überwachende Elemente| Risikostufe| Hierbei gilt:| Filter/Unterfilter| Notizen |
| - | - |- |- |- |
| Authentifizierungen bei MBI- und HBI-Anwendungen mit einstufiger Authentifizierung.| Niedrig| Azure AD-Anmeldungsprotokoll| Status = Erfolg<br>- und -<br>Anwendungs-ID = \<HBI app\> <br>- und -<br>Authentifizierungsanforderung = einstufige Authentifizierung.| Überprüfen Sie, ob diese Konfiguration beabsichtigt ist. |
| Authentifizierung zu Zeiten und Tagen der Woche oder des Jahres, an denen in Ländern kein normaler Geschäftsbetrieb herrscht.| Niedrig| Azure AD-Anmeldungsprotokoll| Erfassen Sie die interaktive Authentifizierung, die außerhalb der normalen Geschäftstage/-zeiten erfolgt. <br>Status = Erfolg<br>Ort = \<location\><br>Datum/Uhrzeit = \<not normal working hours\>| Überwachen und warnen Sie bei Authentifizierungen zu Zeiten und Tagen der Woche oder des Jahres, an denen in Ländern kein normaler Geschäftsbetrieb herrscht. |
| Messbarer Anstieg erfolgreicher Anmeldungen.| Niedrig| Azure AD-Anmeldungsprotokoll| Erfassen Sie eine Zunahme der erfolgreichen Authentifizierung in allen Bereichen. Beispiel: Die Gesamtanzahl erfolgreicher Authentifizierungen heute ist >10 % als am gleichen Tag der Vorwoche.| Wenn Sie keinen Schwellenwert festgelegt haben, überwachen und warnen Sie, wenn erfolgreiche Authentifizierungen um 10 % oder mehr zunehmen. |

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in den folgenden Artikeln im Leitfaden zu Sicherheitsvorgängen:

[Azure AD: Übersicht über Sicherheitsvorgänge](security-operations-introduction.md)

[Sicherheitsvorgänge für Benutzerkonten](security-operations-user-accounts.md)

[Sicherheitsvorgänge für privilegierte Konten](security-operations-privileged-accounts.md)

[Sicherheitsvorgänge für Privileged Identity Management](security-operations-privileged-identity-management.md)

[Sicherheitsvorgänge für Anwendungen](security-operations-applications.md)

[Sicherheitsvorgänge für Geräte](security-operations-devices.md)
 
[Sicherheitsvorgänge für die Infrastruktur](security-operations-infrastructure.md)
