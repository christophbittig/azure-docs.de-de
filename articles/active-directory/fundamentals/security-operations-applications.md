---
title: Azure Active Directory-Sicherheitsvorgänge für Anwendungen
description: Erfahren Sie, wie Sie Anwendungen auf Sicherheitsbedrohungen überwachen und Warnungen ausgeben können.
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
ms.openlocfilehash: a70d01ddd2387e30ef854acff1412fac2bd0bf65
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132346475"
---
# <a name="azure-active-directory-security-operations-guide-for-applications"></a>Leitfaden für Azure Active Directory-Sicherheitsvorgänge für Anwendungen

Anwendungen bieten Sicherheitsverletzungen eine Angriffsfläche und müssen daher überwacht werden. Auch wenn sie nicht so häufig wie Benutzerkonten angegriffen werden, kann es zu Verletzungen kommen. Da Anwendungen häufig ohne menschliches Eingreifen ausgeführt werden, sind die Angriffe möglicherweise schwieriger zu erkennen.

Dieser Artikel enthält Anleitungen zum Überwachen auf und Warnen bei Anwendungsereignissen. Er wird regelmäßig aktualisiert, um sicherzustellen, dass Sie folgende Aufgaben erledigen können:

* Verhindern, dass schädliche Anwendungen unberechtigten Zugriff auf Daten erhalten

* Verhindern, dass vorhandene Anwendungen von böswilligen Akteuren kompromittiert werden

* Gewinnen von Erkenntnissen, mit denen Sie neue Anwendungen sicherer erstellen und konfigurieren können

Wenn Sie mit der Funktionsweise von Anwendungen in Azure Active Directory (Azure AD) nicht vertraut sind, finden Sie unter [Apps und Dienstprinzipale in Azure AD](../develop/app-objects-and-service-principals.md) weitere Informationen.

> [!NOTE]
> Wenn Sie die Übersicht über Azure Active Directory-[Sicherheitsvorgänge](security-operations-introduction.md) noch nicht gelesen haben, sollten Sie dies jetzt in Erwägung ziehen.

## <a name="what-to-look-for"></a>Suchschwerpunkte

Wenn Sie Ihre Anwendungen auf sicherheitsbezogene Incidents überwachen, sollten Sie die folgenden Punkte beachten, um normale von schädlichen Aktivitäten unterscheiden zu können. Die folgenden Ereignisse können Hinweise auf Sicherheitsmängel geben und werden jeweils im weiteren Verlauf des Artikels behandelt.

* Alle Änderungen, die außerhalb der normalen Geschäftsprozesse und Zeitpläne auftreten

* Änderungen von Anmeldeinformationen für Anwendungen

* Anwendungsberechtigungen

   * Zuweisung eines Dienstprinzipals zu einer Azure AD- oder Azure RBAC-Rolle

   * Anwendungen, denen sehr privilegierte Berechtigungen erteilt werden

   * Azure Key Vault-Änderungen

   * Endbenutzer, der eine Einwilligung für Anwendungen erteilt

   * Aufheben der Einwilligung des Endbenutzers basierend auf dem Risikograd

* Änderungen an der Anwendungskonfiguration

   * Änderungen des URI (Uniform Resource Identifier), auch abweichend vom Standard

   * Änderungen an Anwendungsbesitzern

   * Änderungen von Abmelde-URLs

## <a name="where-to-look"></a>Zu untersuchende Protokolle

Zur Untersuchung und Überwachung verwenden Sie die folgenden Protokolldateien:

* [Azure AD-Überwachungsprotokolle](../reports-monitoring/concept-audit-logs.md)

* [Anmeldeprotokolle](../reports-monitoring/concept-all-sign-ins.md)

* [Microsoft 365-Überwachungsprotokolle](/microsoft-365/compliance/auditing-solutions-overview)

* [Azure Key Vault-Protokolle](../../key-vault/general/logging.md)

Im Azure-Portal können Sie die Azure AD-Überwachungsprotokolle anzeigen und als CSV- oder JSON-Dateien (Comma-Separated Value, JavaScript Object Notation) herunterladen. Das Azure-Portal bietet mehrere Möglichkeiten zur Integration von Azure AD-Protokollen in andere Tools, die eine umfassendere Automatisierung von Überwachung und Warnmeldungen ermöglichen:

* **[Microsoft Sentinel](../../sentinel/overview.md)** : Ermöglicht intelligente Sicherheitsanalysen auf Unternehmensebene, indem SIEM-Funktionen (Security Information and Event Management) zur Verfügung gestellt werden. 

* **[Azure Monitor](../../azure-monitor/overview.md)** : ermöglicht die automatisierte Überwachung verschiedener Bedingungen und entsprechende Warnungen. Mit dem Tool können Arbeitsmappen erstellt werden, die sich zum Kombinieren von Daten aus verschiedenen Quellen eignen.

* **[Azure Event Hubs](../../event-hubs/event-hubs-about.md) mit Integration in ein SIEM-System**- [Azure AD-Protokolle können über die Azure Event Hub-Integration in andere SIEM-Systeme wie Splunk, ArcSight, QRadar und Sumo Logic integriert](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) werden.

* **[Microsoft Defender für Cloud-Apps](/cloud-app-security/what-is-cloud-app-security)** : Ermöglichen Ihnen die Erkennung und Verwaltung von Apps, die Steuerung von Apps und Ressourcen sowie die Überprüfung der Compliance Ihrer Cloud-Apps.

Ein Großteil der Überwachung und zugehörigen Warnungen hängt von den Auswirkungen Ihrer Richtlinien für bedingten Zugriff ab. Sie können die Arbeitsmappe [Erkenntnisse und Berichterstellung zum bedingten Zugriff](../conditional-access/howto-conditional-access-insights-reporting.md) verwenden, um die Auswirkungen einer oder mehrerer Richtlinien für bedingten Zugriff auf Ihre Anmeldungen sowie die Ergebnisse von Richtlinien, einschließlich Gerätestatus, zu untersuchen. Diese Arbeitsmappe bietet Ihnen die Möglichkeit, eine Zusammenfassung der Auswirkungen anzuzeigen und die Auswirkungen über einen bestimmten Zeitraum zu ermitteln. Sie können mithilfe der Arbeitsmappe auch die Anmeldungen eines bestimmten Benutzers untersuchen. 

 Im restlichen Teil dieses Artikels wird beschrieben, was wir Ihnen zur Überwachung und für zugehörige Warnungen empfehlen, und zwar gegliedert nach der Art der Bedrohung. Wo es spezielle vordefinierte Lösungen gibt, verweisen wir auf diese oder stellen Beispiele im Anschluss an die Tabelle zur Verfügung. Andernfalls können Sie Warnungen mithilfe der oben genannten Tools erstellen. 

## <a name="application-credentials"></a>Anmeldeinformationen für Anwendungen

Viele Anwendungen verwenden Anmeldeinformationen für die Authentifizierung bei Azure AD. Alle zusätzlichen Anmeldeinformationen, die außerhalb der erwarteten Prozesse hinzugefügt werden, können zu einem böswilligen Akteur gehören, der diese Anmeldeinformationen einsetzt. Wir empfehlen nachdrücklich die Verwendung von X509-Zertifikaten, die von vertrauenswürdigen Stellen oder verwalteten Identitäten ausgestellt wurden, anstatt geheime Clientschlüssel zu verwenden. Wenn Sie jedoch geheime Clientgeheimnisse benötigen, befolgen Sie bewährte Methoden, um Anwendungen sicher zu halten. Hinweis: Aktualisierungen von Anwendungen und Dienstprinzipalen werden im Überwachungsprotokoll als zwei Einträge aufgezeichnet. 

* Überwachen Sie Anwendungen, um diejenigen mit langen Ablaufzeiten für Anmeldeinformationen zu ermitteln.

* Ersetzen Sie langfristige Anmeldeinformationen durch kurzfristige. Ergreifen Sie Maßnahmen, um sicherzustellen, dass Anmeldeinformationen abgesichert gespeichert und nicht in Coderepositorys übertragen werden.


| Zu überwachende Elemente| Risikostufe| Hierbei gilt:| Filter/Unterfilter| Notizen |
| -|-|-|-|-|
| Hinzugefügte Anmeldeinformationen zu vorhandenen Anwendungen| Hoch| Azure AD-Überwachungsprotokolle| Dienst – Kernverzeichnis, Kategorie: ApplicationManagement <br>Aktivität: Anwendungszertifikate und Geheimnisverwaltung aktualisieren<br>- und -<br>Aktivität: Dienstprinzipal/Anwendung aktualisieren| Warnung, wenn für Anmeldeinformationen Folgendes gilt:<li> außerhalb der normalen Geschäftszeiten oder Workflows hinzugefügt<li> nicht in Ihrer Umgebung verwendeter Typ<li> wurden einem Nicht-SAML-Flow hinzugefügt, der den Dienstprinzipal unterstützt |
| Anmeldeinformationen mit einer längeren Gültigkeitsdauer als Ihre Richtlinien zulassen| Medium| Microsoft Graph| Start- und Enddatum der Anmeldeinformationen für Anwendungsschlüssel<br>- und -<br>Anmeldeinformationen für Anwendungskennwort| Sie können mithilfe der Microsoft Graph-API das Start- und Enddatum von Anmeldeinformationen ermitteln und diejenigen auswerten, deren Gültigkeitsdauer länger als zulässig ist. Siehe das PowerShell-Skript im Anschluss an die Tabelle. |

 Die folgenden vordefinierten Überwachungen und Warnungen sind verfügbar.

* Microsoft Sentinel: [Warnung, wenn neue Anmeldeinformationen für die App oder den Dienstprinzipal hinzugefügt wurden](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/AuditLogs/NewAppOrServicePrincipalCredential.yaml) 

* Azure Monitor: [Azure AD Arbeitsmappe, um Sie bei der Bewertung des Solorigate-Risikos zu unterstützen – Microsoft Tech Community](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/azure-ad-workbook-to-help-you-assess-solorigate-risk/ba-p/2010718)

* Defender für Cloud-Apps – [Defender für Cloud-Apps-Leitfaden zur Untersuchung von Warnungen zur Anomalieerkennung](/cloud-app-security/investigate-anomaly-alerts)

* PowerShell: [PowerShell-Beispielskript zum Ermitteln der Gültigkeitsdauer von Anmeldeinformationen](https://github.com/madansr7/appCredAge)

## <a name="application-permissions"></a>Anwendungsberechtigungen

Wie bei einem Administratorkonto können Anwendungen privilegierte Rollen zugewiesen werden. Apps können Azure AD-Rollen zugewiesen werden, z. B. globaler Administrator, oder Azure RBAC-Rollen, z. B. Abonnementbesitzer. Da sie ohne einen anwesenden Benutzer und als Hintergrunddienst ausgeführt werden können, überwachen sie genau, wann immer einer Anwendung eine Rolle oder Berechtigung mit hohen Privilegien gewährt wird. 

### <a name="service-principal-assigned-to-a-role"></a>Einer Rolle zugewiesener Dienstprinzipal


| Zu überwachende Elemente| Risikostufe| Hierbei gilt:| Filter/Unterfilter| Notizen |
|-|-|-|-|-|
| App, die einer Azure RBAC- oder Azure AD-Rolle zugewiesen ist| Hoch bis mittel| Azure AD-Überwachungsprotokolle| Typ: Dienstprinzipal<br>Aktivität: „Mitglied zur Rolle hinzufügen“ oder „Berechtigtes Mitglied zur Rolle hinzufügen“<br>Oder<br>„Zugeordnetes Mitglied zu Rolle hinzufügen“| Bei Rollen mit hohen Berechtigungen, z  B. Globaler Administrator, ist das Risiko hoch. Bei Rollen mit niedrigeren Berechtigungen ist das Risiko mittel. Warnung auslösen, wenn eine Anwendung einer Azure-Rolle oder Azure AD-Rolle außerhalb der normalen Änderungs- oder Konfigurationsverfahren zugewiesen wird. |

### <a name="application-granted-highly-privileged-permissions"></a>Anwendungen, denen sehr privilegierte Berechtigungen erteilt werden

Anwendungen sollten außerdem das Prinzip der geringsten Rechte befolgen. Untersuchen Sie Anwendungsberechtigungen, um sicherzustellen, ob sie wirklich nötig sind. Sie können einen Bericht [zur Zuweisung der App-Einwilligung](https://aka.ms/getazureadpermissions) erstellen, um vorhandene Anwendungen zu identifizieren und privilegierte Berechtigungen hervorzuheben.

| Zu überwachende Elemente|Risikostufe|Hierbei gilt:| Filter/Unterfilter| Notizen|
|-|-|-|-|-|
| App mit sehr privilegierten Berechtigungen, z. B. Berechtigungen mit „ *.All“ (Directory.ReadWrite.All) oder weitreichenden Berechtigungen (Mail.* )| Hoch |Azure AD-Überwachungsprotokolle| „App-Rollenzuweisung zu Dienstprinzipal hinzufügen“ <br>Dabei gilt:<br> Ziele identifizieren eine API mit sensiblen Daten (z. B. Microsoft Graph) <br>- und -<br>AppRole.Value kennzeichnet eine besonders privilegierte Anwendungsberechtigung (App-Rolle).| Apps mit weitreichenden erteilten Berechtigungen wie „ *.All“ (Directory.ReadWrite.All) oder weitreichenden Berechtigungen (Mail.* ) |
| Administrator, der entweder Berechtigungen für Anwendungen (App-Rollen) oder besonders privilegierte delegierte Berechtigungen erteilt |Hoch| Microsoft 365-Portal| „App-Rollenzuweisung zu Dienstprinzipal hinzufügen“ <br>Dabei gilt:<br>Ziele identifizieren eine API mit sensiblen Daten (z. B. Microsoft Graph)<br>„Erteilung delegierter Berechtigungen hinzufügen“ <br>Dabei gilt:<br>Ziele identifizieren eine API mit sensiblen Daten (z. B. Microsoft Graph) <br>- und -<br>DelegatedPermissionGrant.Scope enthält besonders privilegierte Berechtigungen.| Warnung auslösen, wenn ein globaler Administrator, Anwendungsadministrator oder Cloudanwendungsadministrator seine Einwilligung für eine Anwendung gibt. Achten Sie insbesondere auf Einwilligungen außerhalb der normalen Aktivitäts- und Änderungsverfahren. |
| Der Anwendung werden Berechtigungen für Microsoft Graph, Exchange, SharePoint oder Azure AD gewährt. |Hoch| Azure AD-Überwachungsprotokolle| „Erteilung delegierter Berechtigungen hinzufügen“ <br>Oder<br>„App-Rollenzuweisung zu Dienstprinzipal hinzufügen“ <br>Dabei gilt:<br>Ziele identifizieren eine API mit sensiblen Daten (z. B. Microsoft Graph, Exchange Online usw.)| Warnung wie in der vorherigen Zeile auslösen. |
| Anwendungsberechtigungen (App-Rollen) für andere APIs werden erteilt. |Medium| Azure AD-Überwachungsprotokolle| „App-Rollenzuweisung zu Dienstprinzipal hinzufügen“ <br>Dabei gilt:<br>Ziele identifizieren beliebige andere APIs.| Warnung wie in der vorherigen Zeile auslösen. |
| Besonders privilegierte delegierte Berechtigungen werden im Namen aller Benutzer erteilt |Hoch| Azure AD-Überwachungsprotokolle| „Erteilung delegierter Berechtigungen hinzufügen“, wobei Ziele eine API mit sensiblen Daten identifizieren (z. B. Microsoft Graph) <br> DelegatedPermissionGrant.Scope enthält besonders privilegierte Berechtigungen <br>- und -<br>DelegatedPermissionGrant.ConsentType ist „AllPrincipals“.| Warnung wie in der vorherigen Zeile auslösen. |

Weitere Informationen zum Überwachen von App-Berechtigungen finden Sie in diesem Tutorial: [Tutorial zum Untersuchen und Korrigieren risikobehafteter OAuth-Apps](/cloud-app-security/investigate-risky-oauth).

### <a name="azure-key-vault"></a>Azure Key Vault

Azure Key Vault kann zum Speichern der Geheimnisse Ihres Mandanten verwendet werden. Es wird empfohlen, alle Änderungen an der Konfiguration und den Aktivitäten von Key Vault mit besonderer Aufmerksamkeit zu verfolgen. 

| Zu überwachende Elemente| Risikostufe| Hierbei gilt:| Filter/Unterfilter| Notizen |
|-|-|-|-|-|
| Wann und von wem auf Ihre Schlüsseltresore zugegriffen wurde| Medium| [Azure Key Vault-Protokolle](../../key-vault/general/logging.md?tabs=Vault)| Ressourcentyp: Key Vault-Instanzen| Suchen nach <li> Jeglichem Zugriff auf Key Vault außerhalb regulärer Prozesse und Zeiten. <li> Änderungen an der ACL für Key Vault. |

Nachdem Sie Azure Key Vault eingerichtet haben, sollten Sie unbedingt die [Protokollierung aktivieren](../../key-vault/general/howto-logging.md?tabs=azure-cli), die zeigt, [wie und wann auf Ihre Key Vault-Instanzen zugegriffen wird](../../key-vault/general/logging.md?tabs=Vault). Außerdem sollten Sie [Warnungen für Key Vault konfigurieren](../../key-vault/general/alert.md), um zugewiesene Benutzer oder Verteilerlisten per E-Mail, Telefonanruf, Textnachricht oder [Event Grid](../../key-vault/general/event-grid-overview.md) zu benachrichtigen, wenn die Integrität beeinträchtigt ist. Wenn Sie außerdem eine [Überwachung](../../key-vault/general/alert.md) mit Key Vault-Erkenntnissen einrichten, erhalten Sie eine Momentaufnahme der Anforderungen, Leistung, Ausfälle und Latenzzeiten von Key Vault. [Log Analytics](../../azure-monitor/logs/log-analytics-overview.md) bietet auch einige [Beispielabfragen](../../azure-monitor/logs/queries.md) für Azure Key Vault, auf die Sie zugreifen können, nachdem Sie Ihre Key Vault-Instanz ausgewählt und dann unter „Überwachung“ die Option „Protokolle“ ausgewählt haben.

### <a name="end-user-consent"></a>Endbenutzereinwilligung

| Zu überwachende Elemente| Risikostufe| Hierbei gilt:| Filter/Unterfilter| Notizen |
|-|-|-|-|-|
| Endbenutzereinwilligung in Anwendung| Niedrig| Azure AD-Überwachungsprotokolle| Aktivität: Einwilligung in Anwendung geben /ConsentContext.IsAdminConsent = false| Suchen nach: <li>Profil mit hohen Berechtigungen oder besonders privilegierte Konten<li> App fordert Berechtigungen mit hohem Risiko an<li>Apps mit verdächtigen Namen, z. B. generisch, falsch geschrieben usw. |


Der Vorgang der Einwilligung in eine Anwendung ist an sich nicht schädlich. Untersuchen Sie jedoch neue Einwilligungen von Endbenutzern in verdächtige Anwendungen. Sie können [Benutzereinwilligungsvorgänge einschränken](../../security/fundamentals/steps-secure-identity.md).

Weitere Informationen zu Einwilligungsvorgängen finden Sie in den folgenden Ressourcen:

* [Verwalten der Einwilligung zu Anwendungen und Auswerten von Einwilligungsanforderungen in Azure Active Directory](../manage-apps/manage-consent-requests.md)

* [Ermitteln und Beheben unrechtmäßiger Zuweisungen von Einwilligungen: Office 365](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants)

* [Playbook zur Reaktion auf Vorfälle: Untersuchung der Zuweisung der App-Einwilligung](/security/compass/incident-response-playbook-app-consent)

### <a name="end-user-stopped-due-to-risk-based-consent"></a>Endbenutzereinwilligung aufgrund risikobasierter Zustimmung ausgesetzt 

| Zu überwachende Elemente| Risikostufe| Hierbei gilt:| Filter/Unterfilter| Notizen |
|-|-|-|-|-|
| Endbenutzereinwilligung aufgrund risikobasierter Zustimmung ausgesetzt| Medium| Azure AD-Überwachungsprotokolle| Kernverzeichnis/ApplicationManagement/In Anwendung einwilligen<br> Fehlerstatusursache = Microsoft.online.Security.userConsent<br>BlockedForRiskyAppsExceptions| Überwachen und analysieren Sie jedes Mal, wenn die Einwilligung aufgrund eines Risikos ausgesetzt wird. Suchen nach:<li>Profil mit hohen Berechtigungen oder besonders privilegierte Konten<li> App fordert Berechtigungen mit hohem Risiko an<li>Apps mit verdächtigen Namen, z. B. generisch, falsch geschrieben usw. |

## <a name="application-configuration-changes"></a>Änderungen an der Anwendungskonfiguration

Überwachen Sie Änderungen an der Konfiguration einer beliebigen Anwendung. Insbesondere Änderungen an der Konfiguration von URI (Uniform Resource Identifier), Besitz und Abmelde-URL.

### <a name="dangling-uri-and-redirect-uri-changes"></a>Änderungen an verwaistem URI und Umleitungs-URI 

| Zu überwachende Elemente| Risikostufe| Hierbei gilt:| Filter/Unterfilter| Notizen |
|-|-|-|-|-|
| Verwaister URI| Hoch| Azure AD-Protokolle und Anwendungsregistrierung| Dienst – Kernverzeichnis, Kategorie: ApplicationManagement<br>Aktivität: Anwendung aktualisieren<br>Erfolg: Eigenschaftenname, AppAddress| Achten Sie zum Beispiel auf verwaiste URIs, die auf eine Domäne verweisen, die nicht mehr vorhanden ist oder die Sie nicht explizit besitzen. |
| Änderungen an der Konfiguration des Umleitungs-URI| Hoch| Azure AD-Protokolle| Dienst – Kernverzeichnis, Kategorie: ApplicationManagement<br>Aktivität: Anwendung aktualisieren<br>Erfolg: Eigenschaftenname, AppAddress| Achten Sie auf URIs ohne HTTPS*, URIs mit Platzhaltern am Ende oder der Domäne der URL, URIs, die für die Anwendung NICHT eindeutig sind, und URIs, die auf eine Domäne verweisen, die Sie nicht kontrollieren. |

Warnung auslösen, wenn diese Änderungen erkannt werden.

### <a name="appid-uri-added-modified-or-removed"></a>AppID-URI hinzugefügt, geändert oder entfernt


| Zu überwachende Elemente| Risikostufe| Hierbei gilt:| Filter/Unterfilter| Notizen |
|-|-|-|-|-|
| Änderungen am AppID-URI| Hoch| Azure AD-Protokolle| Dienst – Kernverzeichnis, Kategorie: ApplicationManagement<br>Aktivität: Aktualisierung<br>Application<br>Aktivität: Dienstprinzipal aktualisieren| Suchen Sie nach Änderungen des AppID-URI, z. B. Hinzufügen, Ändern oder Entfernen des URI. |


Warnung auslösen, wenn diese Änderungen außerhalb der genehmigten Change Management-Verfahren entdeckt werden.

### <a name="new-owner"></a>Neuer Besitzer


| Zu überwachende Elemente| Risikostufe| Hierbei gilt:| Filter/Unterfilter| Notizen |
|-|-|-|-|-|
| Änderungen am Anwendungsbesitz| Medium| Azure AD-Protokolle| Dienst – Kernverzeichnis, Kategorie: ApplicationManagement<br>Aktivität: Besitzer der Anwendung hinzufügen| Suchen Sie nach einer beliebigen Instanz eines Benutzers, der außerhalb der normalen Change Management-Aktivitäten als Anwendungsbesitzer hinzugefügt wird. |

### <a name="logout-url-modified-or-removed"></a>Geänderte oder entfernte Abmelde-URL

| Zu überwachende Elemente| Risikostufe| Hierbei gilt:| Filter/Unterfilter| Notizen |
|-|-|-|-|-|
| Änderungen an der Abmelde-URL| Niedrig| Azure AD-Protokolle| Dienst – Kernverzeichnis, Kategorie: ApplicationManagement<br>Aktivität: Anwendung aktualisieren<br>- und -<br>Aktivität: Dienstprinzipal aktualisieren| Suchen Sie nach Änderungen an einer Abmelde-URL. Leere Einträge oder Einträge zu nicht existierenden Speicherorten würden einen Benutzer am Beenden einer Sitzung hindern. |

## <a name="additional-resources"></a>Weitere Ressourcen

Es folgen Links zu nützlichen Ressourcen:

* Azure AD-Toolkit auf GitHub: [https://github.com/microsoft/AzureADToolkit](https://github.com/microsoft/AzureADToolkit)

* Übersicht und Leitfaden zur Sicherheit von Azure Key Vault: [Azure Key Vault – Sicherheit](../../key-vault/general/security-features.md)

* Solorgate – Information zum Risiko und Tools: [Azure AD-Arbeitsmappe zum Bewerten des Solorigate-Risikos](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/azure-ad-workbook-to-help-you-assess-solorigate-risk/ba-p/2010718)

* Leitfaden zur Erkennung von OAuth-Angriffen: [Ungewöhnliches Hinzufügen von Anmeldeinformationen zu einer OAuth-App](/cloud-app-security/investigate-anomaly-alerts)

Informationen zur Konfiguration der Azure AD-Überwachung für SIEM-Systeme: [Partnertools mit Azure Monitor-Integration](../..//azure-monitor/essentials/stream-monitoring-data-event-hubs.md)

 ## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln im Leitfaden zu Sicherheitsvorgängen:

[Azure AD: Übersicht über Sicherheitsvorgänge](security-operations-introduction.md)

[Sicherheitsvorgänge für Benutzerkonten](security-operations-user-accounts.md)

[Sicherheitsvorgänge für privilegierte Konten](security-operations-privileged-accounts.md)

[Sicherheitsvorgänge für Privileged Identity Management](security-operations-privileged-identity-management.md)

[Sicherheitsvorgänge für Anwendungen](security-operations-applications.md)

[Sicherheitsvorgänge für Geräte](security-operations-devices.md)

 
[Sicherheitsvorgänge für die Infrastruktur](security-operations-infrastructure.md)
