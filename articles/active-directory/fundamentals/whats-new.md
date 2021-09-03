---
title: Neuigkeiten Versionshinweise – Azure Active Directory | Microsoft-Dokumentation
description: Hier finden Sie Informationen zu den Neuerungen in Azure Active Directory, z. B. aktuelle Versionshinweise, bekannte Probleme, Fehlerbehebungen, veraltete Funktionen und anstehende Änderungen.
services: active-directory
author: ajburnle
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 7/30/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c9b954776c33fedc3a770bc0aab0185ad595aff
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123219751"
---
# <a name="whats-new-in-azure-active-directory"></a>Neuerungen in Azure Active Directory

>Lassen Sie sich über Updates auf dieser Seite benachrichtigen, indem Sie die URL `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` kopieren und in Ihren ![RSS-Symbol](./media/whats-new/feed-icon-16x16.png)-Feedreader einfügen.

Es werden fortlaufend Verbesserungen an Azure AD vorgenommen. Damit Sie bezüglich der neuesten Entwicklungen auf dem neuesten Stand bleiben, bietet dieser Artikel Informationen zu Folgendem:

- Neueste Versionen
- Bekannte Probleme
- Behebung von Programmfehlern
- Veraltete Funktionen
- Pläne für Änderungen

Besuchen Sie regelmäßig diese Seite. Diese wird monatlich aktualisiert. Wenn Sie nach Elementen suchen, die älter als sechs Monate sind, können Sie sie im [Archiv zu den Neuerungen in Azure Active Directory](whats-new-archive.md) finden.

---
## <a name="july-2021"></a>Juli 2021

### <a name="new-google-sign-in-integration-for-azure-ad-b2c-and-b2b-self-service-sign-up-and-invited-external-users-will-stop-working-starting-july-12-2021"></a>Die neue Google-Anmeldeintegration für die Azure AD B2C- und Azure AD B2B-Self-Service-Registrierung und eingeladene externe Benutzer funktioniert ab dem 12. Juli 2021 nicht mehr

**Typ:** Plan für Änderung  
**Dienstkategorie:** B2B  
**Produktfunktion:** B2B/B2C
 

Wir haben bereits angekündigt, dass [die Ausnahme für eingebettete Webansichten für die Gmail-Authentifizierung in der zweiten Jahreshälfte 2021 abläuft](https://www.yammer.com/cepartners/threads/1188371962232832).

Am 7. Juli 2021 haben wir von Google erfahren, dass einige dieser Einschränkungen ab dem **12. Juli 2021** gelten. Für Azure AD B2B- und Azure AD B2C-Kunden, die in ihren benutzerdefinierten Anwendungen oder Branchenanwendungen eine neue Google-ID-Anmeldung eingerichtet haben, um externe Benutzer einzuladen oder die Self-Service-Registrierung zu aktivieren, gelten die Einschränkungen sofort. Daher wird Endbenutzern eine Fehlermeldung angezeigt, die ihre Gmail-Anmeldung blockiert, wenn keine Systemwebansicht für die Authentifizierung verwendet wird. Einzelheiten hierzu finden Sie in den unten verlinkten Dokumenten. 

Die meisten Apps verwenden standardmäßig die Systemwebansicht und sind von dieser Änderung nicht betroffen. Dies gilt nur für Kunden, die eingebettete Webansichten (die nicht standardmäßige Einstellung) verwenden. Wir empfehlen Kunden, die Authentifizierung ihrer Anwendung stattdessen auf Systembrowser zu verlagern, bevor sie neue Google-Integrationen erstellen. Informationen zum Umstellen von Gmail-Authentifizierungen auf Systembrowser finden Sie im Abschnitt „Eingebettete Webbenutzeroberfläche im Vergleich zur System-Webbenutzeroberfläche“ in der Dokumentation [Verwenden von Webbrowsern (MSAL.NET)](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui). Alle MSAL-SDKs verwenden standardmäßig die Systemwebansicht. [Weitere Informationen](../external-identities/google-federation.md#deprecation-of-web-view-sign-in-support)

---

### <a name="google-sign-in-on-embedded-web-views-expiring-september-30-2021"></a>Google-Anmeldung in eingebetteten Webansichten läuft am 30. September 2021 ab

**Typ:** Plan für Änderung  
**Dienstkategorie:** B2B  
**Produktfunktion:** B2B/B2C
 

Vor ungefähr zwei Monaten haben wir angekündigt, dass die Ausnahme für eingebettete Webansichten für die Gmail-Authentifizierung in der zweiten Jahreshälfte 2021 abläuft. 

Kürzlich hat Google das Datum auf den **30. September 2021** festgelegt. 

Ab dem 30. September 2021 werden Azure AD B2B-Gäste, die sich mit ihren Gmail-Konten anmelden, aufgefordert, einen Code in einem separaten Browserfenster einzugeben, um die Anmeldung auf mobilen Microsoft Teams- und -Desktopclients abzuschließen. Dies gilt sowohl für eingeladene Gäste als auch für Gäste, die sich mithilfe der Self-Service-Registrierung registriert haben. 

Azure AD B2C-Kunden, die in ihren benutzerdefinierten/Branchen-Apps eingebettete Webansichten für Gmail-Authentifizierungen eingerichtet haben oder vorhandene Google-Integrationen verwenden, können ihren Benutzern keine Anmeldung mit Gmail-Konten mehr ermöglichen. Um dieses Problem zu vermeiden, müssen Sie Ihre Apps so ändern, dass sie den Systembrowser für die Anmeldung verwenden. Weitere Informationen finden Sie im Abschnitt „Eingebettete Webbenutzeroberfläche im Vergleich zur System-Webbenutzeroberfläche“ in der Dokumentation [Verwenden von Webbrowsern (MSAL.NET)](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui). Alle MSAL-SDKs verwenden standardmäßig die Systemwebansicht. 

Da am 30. September 2021 mit der Bereitstellung des Flows für die Geräteanmeldung begonnen wird, ist es wahrscheinlich, dass er noch nicht in Ihrer Region bereitgestellt wird (in diesem Fall wird Ihren Endbenutzern die in der Dokumentation dargestellte Fehlermeldung angezeigt, bis die Bereitstellung in Ihrer Region erfolgt ist). 

Weitere Informationen zu bekannten, betroffenen Szenarien sowie zu den zu erwartenden Benutzererfahrungen finden Sie unter [Hinzufügen von Google als Identitätsanbieter für B2B-Gastbenutzer](../external-identities/google-federation.md#deprecation-of-web-view-sign-in-support).

---

### <a name="bug-fixes-in-my-apps"></a>Fehlerbehebungen in „Meine Apps“

**Typ:** Korrigiert  
**Dienstkategorie:** Meine Apps  
**Produktfunktion:** Endbenutzerumgebungen
 
- Zuvor führte das Vorhandensein des Banners, in dem die Verwendung von Sammlungen empfohlen wurde, dazu, dass der Inhalt hinter dem Header verschwand. Dieses Problem wurde behoben. 
- Zuvor bestand ein weiteres Problem beim Hinzufügen von Apps zu einer Sammlung. Die Reihenfolge der Apps in der Sammlung „Alle Apps“ wurde nach dem Zufallsprinzip neu angeordnet. Dieses Problem wurde ebenfalls behoben. 

Weitere Informationen zu „Meine Apps“ finden Sie unter [Anmelden beim Portal „Meine Apps“ und Starten von Apps über dieses](../user-help/my-apps-portal-end-user-access.md).

---

### <a name="public-preview----application-authentication-method-policies"></a>Öffentliche Vorschau: Richtlinien für Anwendungsauthentifizierungsmethoden

**Typ:** Neues Feature  
**Dienstkategorie:** MS Graph  
**Produktfunktion:** Entwickleroberfläche
 
Richtlinien für Anwendungsauthentifizierungsmethoden in Microsoft Graph, mit denen IT-Administratoren die Gültigkeitsdauer von Anmeldeinformationen für geheime Anwendungskennwörter erzwingen oder die Verwendung von Geheimnissen vollständig blockieren können. Die Richtlinien können für einen gesamten Mandanten als Standardkonfiguration erzwungen und auf bestimmte Anwendungen oder Dienstprinzipale festgelegt werden. [Weitere Informationen](/graph/api/resources/policy-overview?view=graph-rest-1.0)
 
---

### <a name="public-preview----authentication-methods-nudge-to-download-microsoft-authenticator"></a>Öffentliche Vorschau: Authentifizierungsmethoden mit Aufforderung zum Herunterladen von Microsoft Authenticator

**Typ:** Neues Feature  
**Dienstkategorie:** Microsoft Authenticator-App  
**Produktfunktion:** Benutzerauthentifizierung
 
Die Authenticator-Aufforderungsrichtlinie unterstützt Administratoren dabei, die Sicherheit ihrer Organisationen zu erhöhen, indem sie Benutzer auffordern, die Microsoft Authenticator-App zu verwenden. Vor diesem Feature gab es keine Möglichkeit für einen Administrator, die Benutzer dazu zu bringen, die Authenticator-App einzurichten. 

Die Aufforderung bietet einem Administrator die Möglichkeit, Benutzer und Gruppen in den Geltungsbereich der Aufforderung einzuschließen oder davon auszuschließen, um eine reibungslose Übernahme in der gesamten Organisation sicherzustellen. [Weitere Informationen](../authentication/how-to-nudge-authenticator-app.md)
 
---

### <a name="public-preview----separation-of-duties-check"></a>Öffentliche Vorschau: Überprüfung der Aufgabentrennung 

**Typ:** Neues Feature  
**Dienstkategorie:** Benutzerzugriffsverwaltung  
**Produktfunktion:** Berechtigungsverwaltung
 
In der Azure AD-Berechtigungsverwaltung kann ein Administrator definieren, dass ein Zugriffspaket mit einem anderen Zugriffspaket oder einer Gruppe inkompatibel ist.  Benutzer mit inkompatiblen Mitgliedschaften können dann keinen zusätzlichen Zugriff anfordern. [Weitere Informationen](../governance/entitlement-management-access-package-request-policy.md#prevent-requests-from-users-with-incompatible-access-preview)
 
---

### <a name="public-preview----identity-protection-logs-in-log-analytics-storage-accounts-and-event-hubs"></a>Öffentliche Vorschau: Identity Protection-Protokolle in Log Analytics, Storage-Konten und Event Hubs

**Typ:** Neues Feature  
**Dienstkategorie:** Schutz der Identität (Identity Protection)  
**Produktfunktion:** Identitätssicherheit und -schutz
 
Sie können jetzt die Protokolle für Risikobenutzer und Risikoerkennungen an Azure Monitor, Storage-Konten oder Log Analytics senden, indem Sie auf dem Azure AD-Blatt die Diagnoseeinstellungen verwenden. [Weitere Informationen](../identity-protection/howto-export-risk-data.md)
 
---

### <a name="public-preview----application-proxy-api-addition-for-backend-ssl-certificate-validation"></a>Öffentliche Vorschau: Anwendungsproxy-API-Ergänzung für die Überprüfung des Back-End-SSL-Zertifikats

**Typ:** Neue Funktion  
**Dienstkategorie:** Anwendungsproxy  
**Produktfunktion:** Zugriffssteuerung
 
Der Ressourcentyp „onPremisesPublishing“ enthält jetzt die Eigenschaft „isBackendCertificateValidationEnabled“, die angibt, ob die Back-End-SSL-Zertifikatüberprüfung für die Anwendung aktiviert ist. Für alle neuen Anwendungsproxy-Apps wird die Eigenschaft standardmäßig auf „true“ festgelegt. Für alle vorhandenen Apps wird die Eigenschaft auf „false“ festgelegt. Weitere Informationen finden Sie unter [onPremisesPublishing-Ressourcentyp](/graph/api/resources/onpremisespublishing?view=graph-rest-beta).
 
---

### <a name="general-availability---improved-authenticator-setup-experience-for-add-azure-ad-account-in-microsoft-authenticator-app-by-directly-signing-into-the-app"></a>Allgemeine Verfügbarkeit: Verbesserte Authenticator-Einrichtungsfunktion zum Hinzufügen eines Azure AD-Kontos in der Microsoft Authenticator-App durch die Möglichkeit der direkten Anmeldung bei der App.

**Typ:** Neues Feature  
**Dienstkategorie:** Microsoft Authenticator-App  
**Produktfunktion:** Benutzerauthentifizierung
 
Benutzer können sich jetzt mit ihren vorhandenen Authentifizierungsmethoden direkt bei der Microsoft Authenticator-App anmelden, um ihre Anmeldeinformationen einzurichten. Benutzer müssen keinen QR-Code mehr scannen und können einen befristeten Zugriffspass (Temporary Access Pass, TAP) oder Kennwort + SMS (oder eine andere Authentifizierungsmethode) verwenden, um ihr Konto in der Authenticator-App zu konfigurieren.

Dadurch wird der Prozess zum Bereitstellen von Benutzeranmeldeinformationen für die Microsoft Authenticator-App verbessert, und der Endbenutzer kann eine Self-Service-Methode zum Bereitstellen der App verwenden. [Weitere Informationen](../user-help/user-help-auth-app-add-work-school-account.md#sign-in-with-your-credentials)
 
---

### <a name="general-availability---set-manager-as-reviewer-in-azure-ad-entitlement-management-access-packages"></a>Allgemeine Verfügbarkeit: Festlegen des Vorgesetzten als Prüfer in Zugriffspaketen in der Azure AD-Berechtigungsverwaltung

**Typ:** Neues Feature  
**Dienstkategorie:** Benutzerzugriffsverwaltung  
**Produktfunktion:** Berechtigungsverwaltung
 
Zugriffspakete in der Azure AD-Berechtigungsverwaltung unterstützen jetzt das Festlegen des Vorgesetzten des Benutzers als Prüfer bei regelmäßig stattfindenden Zugriffsüberprüfungen. [Weitere Informationen](../governance/entitlement-management-access-reviews-create.md)

---

### <a name="general-availability---enable-external-users-to-self-service-sign-up-in-aad-using-msa-accounts"></a>Allgemeine Verfügbarkeit: Möglichkeit der Self-Service-Registrierung für externe Benutzer in AAD mit MSA-Konten

**Typ:** Neues Feature  
**Dienstkategorie:** B2B  
**Produktfunktion:** B2B/B2C
 
Benutzer können externen Benutzern jetzt die Self-Service-Registrierung in Azure Active Directory mit Microsoft-Konten ermöglichen. [Weitere Informationen](../external-identities/microsoft-account.md)
 
---
 
### <a name="general-availability---external-identities-self-service-sign-up-with-email-one-time-passcode"></a>Allgemeine Verfügbarkeit: Self-Service-Registrierung für External Identities mit Einmalkennung per E-Mail

**Typ:** Neues Feature  
**Dienstkategorie:** B2B  
**Produktfunktion:** B2B/B2C
 

Benutzer können jetzt externen Benutzern die Self-Service-Registrierung in Azure Active Directory per E-Mail und Einmalkennung ermöglichen. [Weitere Informationen](../external-identities/one-time-passcode.md)
 
---

### <a name="general-availability---anomalous-token"></a>Allgemeine Verfügbarkeit: Anomales Token

**Typ:** Neues Feature  
**Dienstkategorie:** Schutz der Identität (Identity Protection)  
**Produktfunktion:** Identitätssicherheit und -schutz
 
Die Erkennung anomaler Token ist jetzt in Identity Protection verfügbar. Dieses Feature kann ungewöhnliche Merkmale des Tokens erkennen, wie z. B. die aktive Zeit und die Authentifizierung über eine unbekannte IP-Adresse. [Weitere Informationen](../identity-protection/concept-identity-protection-risks.md#sign-in-risk)
 
---

### <a name="general-availability---register-or-join-devices-in-conditional-access"></a>Allgemeine Verfügbarkeit: Registrieren oder Hinzufügen von Geräten für bedingten Zugriff

**Typ:** Neues Feature  
**Dienstkategorie**: Bedingter Zugriff  
**Produktfunktion:** Identitätssicherheit und -schutz
 
Für bedingten Zugriff ist jetzt die Benutzeraktion zum Registrieren oder Hinzufügen von Geräten allgemein verfügbar. Mit dieser Benutzeraktion können Sie Richtlinien für die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) für die Azure AD-Geräteregistrierung steuern. 

Derzeit können Sie mit dieser Benutzeraktion MFA nur als Steuerelement aktivieren, wenn Benutzer Geräte registrieren oder zu Azure AD hinzufügen. Andere Steuerelemente, die von der Azure AD-Geräteregistrierung abhängen oder darauf nicht anwendbar sind, werden mit dieser Benutzeraktion weiterhin deaktiviert. [Weitere Informationen](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions) 

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---july-2021"></a>Neue Bereitstellungsconnectors im Azure AD-Anwendungskatalog – Juli 2021

**Typ:** Neues Feature  
**Dienstkategorie:** App-Bereitstellung  
**Produktfunktion:** Integration von Drittanbieterprodukten
 
Sie können ab sofort das Erstellen, Aktualisieren und Löschen von Benutzerkonten für diese neu integrierten Apps automatisieren:

- [Clebex](../saas-apps/clebex-provisioning-tutorial.md)
- [Exium](../saas-apps/exium-provisioning-tutorial.md)
- [SoSafe](../saas-apps/sosafe-provisioning-tutorial.md)
- [Talentech](../saas-apps/talentech-provisioning-tutorial.md)
- [Thrive LXP](../saas-apps/thrive-lxp-provisioning-tutorial.md)
- [Vonage](../saas-apps/vonage-provisioning-tutorial.md)
- [Zip](../saas-apps/zip-provisioning-tutorial.md)
- [TimeClock 365](../saas-apps/timeclock-365-provisioning-tutorial.md)

Weitere Informationen dazu, wie Sie Ihre Organisation durch die automatisierte Bereitstellung von Benutzerkonten besser schützen können, finden Sie unter [Automatisieren der Benutzerbereitstellung für SaaS-Anwendungen mit Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="changes-to-security-and-microsoft-365-group-settings-in-azure-portal"></a>Änderungen an Sicherheitsgruppen- und Microsoft 365-Gruppeneinstellungen im Azure-Portal

**Typ:** Geändertes Feature  
**Dienstkategorie**: Gruppenverwaltung  
**Produktfunktion**: Verzeichnis
 

In der Vergangenheit konnten Benutzer Sicherheitsgruppen und Microsoft 365-Gruppen im Azure-Portal erstellen. Jetzt haben Benutzer die Möglichkeit, Gruppen in Azure-Portalen sowie mit PowerShell und der API zu erstellen. Kunden müssen die neuen für ihre Organisation konfigurierten Einstellungen überprüfen und aktualisieren. [Weitere Informationen](../enterprise-users/groups-self-service-management.md#group-settings).
 
---

### <a name="all-apps-collection-has-been-renamed-to-apps"></a>Die Sammlung „Alle Apps“ wurde in „Apps“ umbenannt

**Typ:** Geänderte Funktion  
**Dienstkategorie:** Meine Apps  
**Produktfunktion:** Endbenutzerumgebungen
 
Im Portal „Meine Apps“ wurde die Sammlung mit dem Namen „Alle Apps“ in „Apps“ umbenannt. Angesichts der Weiterentwicklung des Produkts ist „Apps“ ein passenderer Name für diese Standardsammlung. [Weitere Informationen](../manage-apps/my-apps-deployment-plan.md#plan-the-user-experience)
 
---
 
## <a name="june-2021"></a>Juni 2021

### <a name="context-panes-to-display-risk-details-in-identity-protection-reports"></a>Kontextbereiche zum Anzeigen von Risikodetails in Identity Protection-Berichten

**Typ:** Plan für Änderung  
**Dienstkategorie:** Schutz der Identität (Identity Protection)  
**Produktfunktion:** Identitätssicherheit und -schutz
 
Für die Identity Protection-Berichte „Riskante Benutzer“, „Riskante Anmeldungen“ und „Risikoerkennungen“ werden die Risikodetails eines ausgewählten Eintrags in einem Kontextbereich angezeigt, der rechts auf der Seite platziert ist – Juli 2021. Die Änderung wirkt sich nur auf die Benutzeroberfläche und nicht auf vorhandene Funktionen aus. Weitere Informationen zur Funktionalität dieser Features finden Sie unter [Anleitung: Untersuchen eines Risikos](../identity-protection/howto-identity-protection-investigate-risk.md).
 
---

### <a name="public-preview----create-azure-ad-access-reviews-of-service-principals-that-are-assigned-to-privileged-roles"></a>Öffentliche Vorschau: Erstellen von Azure AD-Zugriffsüberprüfungen von Dienstprinzipalen, die privilegierten Rollen zugewiesen sind

**Typ:** Neue Funktion  
**Dienstkategorie**: Zugriffsüberprüfungen  
**Produktfunktion:** Identity Governance
 
 Sie können Azure AD-Zugriffsüberprüfungen verwenden, um den Zugriff von Dienstprinzipalen auf privilegierte Azure AD- und Azure-Ressourcenrollen zu überprüfen. [Weitere Informationen](../privileged-identity-management/pim-how-to-start-security-review.md#open-access-reviews)
 
---

### <a name="public-preview----group-owners-in-azure-ad-can-create-and-manage-azure-ad-access-reviews-for-their-groups"></a>Öffentliche Vorschau: Gruppenbesitzer in Azure AD können Azure AD-Zugriffsüberprüfungen für ihre Gruppen erstellen und verwalten

**Typ:** Neue Funktion  
**Dienstkategorie**: Zugriffsüberprüfungen  
**Produktfunktion:** Identity Governance
 
Gruppenbesitzer in Azure AD können jetzt Azure AD-Zugriffsüberprüfungen für ihre Gruppen erstellen und verwalten. Diese Funktion ist standardmäßig deaktiviert und kann von Mandantenadministratoren über Azure AD-Zugriffsüberprüfungseinstellungen aktiviert werden. [Weitere Informationen](../governance/create-access-review.md#allow--group-owners-to-create-and-manage-access-reviews-preview)
 
---

### <a name="public-preview----customers-can-scope-access-reviews-of-privileged-roles-to-just-users-with-eligible-or-active-access"></a>Öffentliche Vorschau: Kunden können Zugriffsüberprüfungen privilegierter Rollen auf Benutzer mit berechtigtem oder aktivem Zugriff einschränken

**Typ:** Neue Funktion  
**Dienstkategorie**: Zugriffsüberprüfungen  
**Produktfunktion:** Identity Governance
 
Wenn Administratoren Zugriffsüberprüfungen von Zuweisungen privilegierter Rollen erstellen, können sie festlegen, dass die Überprüfungen nur für berechtigte zugewiesene Benutzer oder nur für aktiv zugewiesene Benutzer durchgeführt werden. [Weitere Informationen](../privileged-identity-management/pim-how-to-start-security-review.md)
 
---

### <a name="public-preview----microsoft-graph-apis-for-mobility-mdmmam-management-policies"></a>Öffentliche Vorschau: Microsoft Graph-APIs für Verwaltungsrichtlinien für Mobilität (MDM/MAM)

**Typ:** Neues Feature  
**Dienstkategorie:** Sonstige  
**Produktfunktion:** Lebenszyklusverwaltung für Geräte
 
Die Microsoft Graph-Unterstützung für die Konfiguration von Mobility (MDM/MAM) in Azure AD befindet sich in der öffentlichen Vorschau. Administratoren können den Benutzerbereich und URLs für MDM-Anwendungen wie Intune mithilfe von Microsoft Graph v1.0 konfigurieren. Weitere Informationen finden Sie unter [mobilityManagementPolicy-Ressourcentyp](/graph/api/resources/mobilitymanagementpolicy?view=graph-rest-beta).

---

### <a name="general-availability---custom-questions-in-access-package-request-flow-in-azure-active-directory-entitlement-management"></a>Allgemeine Verfügbarkeit: Benutzerdefinierte Fragen im Zugriffspaket-Anforderungsflow in der Azure Active Directory-Berechtigungsverwaltung

**Typ:** Neues Feature  
**Dienstkategorie:** Benutzerzugriffsverwaltung  
**Produktfunktion:** Berechtigungsverwaltung
 
Die Azure AD-Berechtigungsverwaltung unterstützt jetzt die Erstellung benutzerdefinierter Fragen im Zugriffspaket-Anforderungsflow. Mit diesem Feature können Sie benutzerdefinierte Fragen in der Zugriffspaketrichtlinie konfigurieren. Diese Fragen werden Anforderern angezeigt, die ihre Antworten im Rahmen des Zugriffsanforderungsprozesses eingeben können. Diese Antworten werden dann den genehmigenden Personen angezeigt, sodass sie hilfreiche Informationen erhalten, durch die sie bessere Entscheidungen über die Zugriffsanforderung treffen können. [Weitere Informationen](../governance/entitlement-management-access-package-create.md#add-requestor-information-to-an-access-package)

---

### <a name="general-availability---multi-geo-sharepoint-sites-as-resources-in-entitlement-management-access-packages"></a>Allgemeine Verfügbarkeit: SharePoint Multi-Geo-Websites als Ressourcen in Zugriffspaketen in der Berechtigungsverwaltung

**Typ:** Neues Feature  
**Dienstkategorie:** Benutzerzugriffsverwaltung  
**Produktfunktion:** Berechtigungsverwaltung
 
Zugriffspakete in der Berechtigungsverwaltung unterstützen jetzt SharePoint Multi-Geo-Websites für Kunden, die die Multi-Geo-Funktionen in SharePoint Online verwenden. [Weitere Informationen](../governance/entitlement-management-catalog-create.md#add-a-multi-geo-sharepoint-site)
 
---

### <a name="general-availability---knowledge-admin-and-knowledge-manager-built-in-roles"></a>Allgemeine Verfügbarkeit: Integrierte Rollen „Wissensadministrator“ und „Wissens-Manager“

**Typ:** Neues Feature  
**Dienstkategorie:** RBAC  
**Produktfunktion:** Zugriffssteuerung
 
Zwei neue Rollen („Wissensadministrator“ und „Wissens-Manager“) sind jetzt allgemein verfügbar.

- Benutzer mit der Rolle „Wissensadministrator“ haben Vollzugriff auf alle Organisationswissenseinstellungen im Microsoft 365 Admin Center. Sie können Inhalte wie Themen und Akronyme erstellen und verwalten. Darüber hinaus können diese Benutzer Inhaltscenter erstellen, die Dienstintegrität überwachen und Service Requests erstellen. [Weitere Informationen](../roles/permissions-reference.md#knowledge-administrator)
- Benutzer mit der Rolle „Wissens-Manager“ können Inhalte erstellen und verwalten und sind in erster Linie für die Qualität und Struktur von Wissen verantwortlich. Sie verfügen über vollständige Rechte für Themenverwaltungsaktionen zum Bestätigen eines Themas, zum Genehmigen von Bearbeitungen oder zum Löschen eines Themas. Diese Rolle kann auch Taxonomien im Rahmen des Begriffs „Speicherverwaltungstool“ verwalten und Inhaltscenter erstellen. [Weitere Informationen](../roles/permissions-reference.md#knowledge-manager)

---

### <a name="general-availability---cloud-app-security-administrator-built-in-role"></a>Allgemeine Verfügbarkeit: Integrierte Rolle „Cloud App Security-Administrator“

**Typ:** Neues Feature  
**Dienstkategorie:** RBAC  
**Produktfunktion:** Zugriffssteuerung
 
 Benutzer mit dieser Rolle verfügen über vollständige Berechtigungen in Cloud App Security. Sie können Administratoren hinzufügen, Richtlinien und Einstellungen von Microsoft Cloud App Security (MCAS) hinzufügen, Protokolle hochladen und Governanceaktionen ausführen. [Weitere Informationen](../roles/permissions-reference.md#cloud-app-security-administrator)
 
---

### <a name="general-availability---windows-update-deployment-administrator"></a>Allgemeine Verfügbarkeit: Windows Update-Bereitstellungsadministrator

**Typ:** Neues Feature  
**Dienstkategorie:** RBAC  
**Produktfunktion:** Zugriffssteuerung
 

 Benutzer mit dieser Rolle können alle Aspekte von Windows Update-Bereitstellungen über den Windows Update for Business-Bereitstellungsdienst erstellen und verwalten. Der Bereitstellungsdienst ermöglicht Benutzern das Definieren von Einstellungen dafür, wann und wie Updates bereitgestellt werden. Außerdem können Benutzer angeben, welche Updates Gruppen von Geräten in ihrem Mandanten angeboten werden. Darüber hinaus können Benutzer den Updatefortschritt überwachen. [Weitere Informationen](../roles/permissions-reference.md#windows-update-deployment-administrator)
 
---

### <a name="general-availability---multi-camera-support-for-windows-hello"></a>Allgemeine Verfügbarkeit: Unterstützung mehrerer Kameras für Windows Hello

**Typ:** Neues Feature  
**Dienstkategorie:** Authentifizierungen (Anmeldungen)  
**Produktfunktion:** Benutzerauthentifizierung
 
Mit dem Windows 10 21H1-Update unterstützt Windows Hello jetzt mehrere Kameras. Das Update enthält Standardeinstellungen für die Verwendung der externen Kamera, wenn sowohl integrierte als auch externe Kameras vorhanden sind. [Weitere Informationen](https://techcommunity.microsoft.com/t5/windows-it-pro-blog/it-tools-to-support-windows-10-version-21h1/ba-p/2365103)

---
 
### <a name="general-availability---access-reviews-ms-graph-apis-now-in-v10"></a>Allgemeine Verfügbarkeit: Microsoft Graph-APIs für Zugriffsüberprüfungen jetzt in Version 1.0 enthalten

**Typ:** Neue Funktion  
**Dienstkategorie**: Zugriffsüberprüfungen  
**Produktfunktion:** Identity Governance
 
Microsoft Graph-APIs für Azure Active Directory-Zugriffsüberprüfungen sind jetzt in Version 1.0 enthalten und unterstützen vollständig konfigurierbare Funktionen für Zugriffsüberprüfungen. [Weitere Informationen](/graph/api/resources/accessreviewsv2-root?view=graph-rest-1.0)
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---june-2021"></a>Neue Bereitstellungsconnectors im Azure AD-Anwendungskatalog – Juni 2021

**Typ:** Neues Feature  
**Dienstkategorie:** App-Bereitstellung  
**Produktfunktion:** Integration von Drittanbieterprodukten
 
Sie können ab sofort das Erstellen, Aktualisieren und Löschen von Benutzerkonten für diese neu integrierten Apps automatisieren:

- [askSpoke](../saas-apps/askspoke-provisioning-tutorial.md)
- [Cloud Academy - SSO](../saas-apps/cloud-academy-sso-provisioning-tutorial.md)
- [CheckProof](../saas-apps/checkproof-provisioning-tutorial.md)
- [GoLinks](../saas-apps/golinks-provisioning-tutorial.md)
- [Holmes Cloud](../saas-apps/holmes-cloud-provisioning-tutorial.md)
- [H5mag](../saas-apps/h5mag-provisioning-tutorial.md)
- [LimbleCMMS](../saas-apps/limblecmms-provisioning-tutorial.md)
- [LogMeIn](../saas-apps/logmein-provisioning-tutorial.md)
- [SECURE DELIVER](../saas-apps/secure-deliver-provisioning-tutorial.md)
- [Sigma Computing](../saas-apps/sigma-computing-provisioning-tutorial.md)
- [Smallstep SSH](../saas-apps/smallstep-ssh-provisioning-tutorial.md)
- [Tribeloo](../saas-apps/tribeloo-provisioning-tutorial.md)
- [Twingate](../saas-apps/twingate-provisioning-tutorial.md)

Weitere Informationen finden Sie unter [Automatisieren der Bereitstellung von Benutzern für SaaS-Anwendungen mit Azure AD](../app-provisioning/user-provisioning.md).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2021"></a>Neue Verbund-Apps im Azure AD-Anwendungskatalog verfügbar – Juni 2021

**Typ:** Neues Feature  
**Dienstkategorie**: Unternehmens-Apps  
**Produktfunktion:** Integration von Drittanbieterprodukten
 
Im Juni 2021 haben wir die folgenden 42 neuen Anwendungen mit Verbundunterstützung in unseren App-Katalog aufgenommen:

[Taksel](https://app.taksel.it/admin/integrations), [IDrive360](../saas-apps/idrive360-tutorial.md), [VIDA](../saas-apps/vida-tutorial.md), [ProProfs Classroom](../saas-apps/proprofs-classroom-tutorial.md), [WAN-Sign](../saas-apps/wan-sign-tutorial.md), [Citrix Cloud SAML SSO](../saas-apps/citrix-cloud-saml-sso-tutorial.md), [Fabric](../saas-apps/fabric-tutorial.md), [DssAD](https://cloudlicensing.deepseedsolutions.com/), [RICOH Creative Collaboration RICC](https://www.ricoh-europe.com/products/software-apps/collaboration-board-software/ricc/), [Styleflow](../saas-apps/styleflow-tutorial.md), [Chaos](https://accounts.chaosgroup.com/corporate_login), [Traced Connector](https://control.traced.app/signup), [Squarespace](https://account.squarespace.com/org/azure), [MX3 Diagnostics Connector](https://mx3www.playground.dynuddns.com/signin-oidc), [Ten Spot](https://tenspot.co/api/v1/sso/azure/login/), [Finvari](../saas-apps/finvari-tutorial.md), [Mobile4ERP](https://play.google.com/store/apps/details?id=com.negevsoft.mobile4erp), [WalkMe US OpenID Connect](https://www.walkme.com/), [Neustar UltraDNS](../saas-apps/neustar-ultradns-tutorial.md), [cloudtamer.io](../saas-apps/cloudtamer-io-tutorial.md), [A Cloud Guru](../saas-apps/a-cloud-guru-tutorial.md), [PetroVue](../saas-apps/petrovue-tutorial.md), [Postman](../saas-apps/postman-tutorial.md), [ReadCube Papers](../saas-apps/readcube-papers-tutorial.md), [Peklostroj](https://app.peklostroj.cz/), [SynCloud](https://onboard.syncloud.io/), [Polymerhq.io](https://www.polymerhq.io/), [Bonos](../saas-apps/bonos-tutorial.md), [Astra Schedule](../saas-apps/astra-schedule-tutorial.md), [Draup](../saas-apps/draup-inc-tutorial.md), [Inc](../saas-apps/draup-inc-tutorial.md), [Applied Mental Health](../saas-apps/applied-mental-health-tutorial.md), [iHASCO Training](../saas-apps/ihasco-training-tutorial.md), [Nexsure](../saas-apps/nexsure-tutorial.md), [XEOX](https://login.xeox.com/), [Plandisc](https://create.plandisc.com/account/logon), [foundU](../saas-apps/foundu-tutorial.md), [Standard for Success Accreditation](../saas-apps/standard-for-success-accreditation-tutorial.md), [Penji Teams](https://web.penjiapp.com/), [CheckPoint Infinity Portal](../saas-apps/checkpoint-infinity-portal-tutorial.md), [Teamgo](../saas-apps/teamgo-tutorial.md), [Hopsworks.ai](../saas-apps/hopsworks-ai-tutorial.md) und [HoloMeeting 2](https://backend2.holomeeting.io/)

Die Dokumentation zu allen Anwendungen finden Sie auch hier: https://aka.ms/AppsTutorial

Informationen zum Auflisten Ihrer Anwendung im Azure AD-App-Katalog finden Sie hier: https://aka.ms/AzureADAppRequest
 
---

### <a name="device-code-flow-now-includes-an-app-verification-prompt"></a>Der Gerätecodeflow enthält jetzt eine Aufforderung zur App-Überprüfung

**Typ:** Geänderte Funktion  
**Dienstkategorie:** Authentifizierungen (Anmeldungen)  
**Produktfunktion:** Benutzerauthentifizierung
 
Der [Gerätecodeflow](../develop/v2-oauth2-device-code.md) wurde aktualisiert und umfasst jetzt eine zusätzliche Benutzereingabeaufforderung. Während der Anmeldung wird der Benutzer aufgefordert, die App zu überprüfen, bei der er sich anmeldet.  Mit der Eingabeaufforderung wird sichergestellt, dass die Benutzer keinem Phishingangriff ausgesetzt werden. [Weitere Informationen](../develop/reference-breaking-changes.md#the-device-code-flow-ux-will-now-include-an-app-confirmation-prompt)
 
---

### <a name="user-last-sign-in-date-and-time-is-now-available-on-azure-portal"></a>Datum und Uhrzeit der letzten Benutzeranmeldung jetzt im Azure-Portal verfügbar

**Typ:** Geänderte Funktion  
**Dienstkategorie:** Benutzerverwaltung  
**Produktfunktion:** Benutzerverwaltung
 
Sie können jetzt den Zeitstempel mit Datum und Uhrzeit der letzten Anmeldung Ihrer Benutzer im Azure-Portal anzeigen. Die Informationen sind für jeden Benutzer auf der Benutzerprofilseite verfügbar. Anhand dieser Informationen können Sie inaktive Benutzer einfach identifizieren und riskante Ereignisse effektiv verwalten. [Weitere Informationen](./active-directory-users-profile-azure-portal.md?context=%2fazure%2factive-directory%2fenterprise-users%2fcontext%2fugr-context)
 
---

### <a name="mim-bhold-suite-impact-of-end-of-support-for-microsoft-silverlight"></a>MIM BHOLD Suite: Auswirkungen des Ablaufs des Supports für Microsoft Silverlight

**Typ:** Geändertes Feature  
**Dienstkategorie:** Microsoft Identity Manager  
**Produktfunktion:** Identity Governance
 
Der Support für Microsoft Silverlight wird am 12. Oktober 2021 eingestellt. Diese Änderung wirkt sich nur auf Kunden aus, die die Microsoft BHOLD Suite verwenden. Andere Microsoft Identity Manager-Szenarien sind davon nicht betroffen. Weitere Informationen finden Sie unter [Supportende für Silverlight](https://support.microsoft.com/windows/silverlight-end-of-support-0a3be3c7-bead-e203-2dfd-74f0a64f1788).  

Benutzer, die Microsoft Silverlight nicht in ihrem Browser installiert haben, können die BHOLD Suite-Module nicht verwenden, die Silverlight erfordern. Dazu gehören der BHOLD-Modellgenerator, die BHOLD FIM-Self-Service-Integration und BHOLD Analytics.  Kunden mit einer vorhandenen BHOLD-Bereitstellung eines oder mehrerer dieser Module sollten planen, diese Module bis Oktober 2021 von ihren BHOLD-Servercomputern zu deinstallieren. Außerdem sollten diese Kunden planen, Silverlight von allen Benutzercomputern zu deinstallieren, die zuvor mit dieser BHOLD-Bereitstellung interagiert haben.
 
---

### <a name="my-experiences-end-of-support-for-internet-explorer-11"></a>Meine*-Features: Ende des Supports für Internet Explorer 11

**Typ:** Als veraltet markiert  
**Dienstkategorie:** Meine Apps  
**Produktfunktion:** Endbenutzerumgebungen
 

Für Microsoft 365 und andere Apps wird der Support für Internet Explorer 11 am 21. August 2021 eingestellt. Dies gilt auch für die Meine*-Features. Für die Meine*-Features, auf die der Zugriff über Internet Explorer erfolgt, werden keine Fehlerbehebungen oder Updates mehr bereitgestellt, was zu Problemen führen kann. Diese Datumsangaben werden vom Edge-Team gesteuert und können geändert werden. [Weitere Informationen](https://blogs.windows.com/windowsexperience/2021/05/19/the-future-of-internet-explorer-on-windows-10-is-in-microsoft-edge/)
 
---

### <a name="planned-deprecation---malware-linked-ip-address-detection-in-identity-protection"></a>Geplante Einstellung: Erkennung von mit Schadsoftware verknüpften IP-Adressen in Identity Protection

**Typ:** Als veraltet markiert  
**Dienstkategorie:** Schutz der Identität (Identity Protection)  
**Produktfunktion:** Identitätssicherheit und -schutz
 
Ab dem 1. Oktober 2021 wird die Erkennung „Mit Schadsoftware verknüpfte IP-Adresse“ von Azure AD Identity Protection nicht mehr generiert. Es ist keine Aktion erforderlich, und Kunden bleiben durch die anderen von Identity Protection bereitgestellten Erkennungen geschützt. Weitere Informationen zu Schutzrichtlinien finden Sie unter [Richtlinien für den Identitätsschutz](../identity-protection/concept-identity-protection-policies.md).
 
---
 
## <a name="may-2021"></a>Mai 2021

### <a name="public-preview----azure-ad-verifiable-credentials"></a>Öffentliche Vorschau: Azure AD-Nachweise

**Typ:** Neues Feature  
**Dienstkategorie:** Sonstige  
**Produktfunktion:** Benutzerauthentifizierung
 
Azure AD-Kunden können jetzt problemlos Nachweise entwerfen und ausgeben. Nachweise können verwendet werden, um Beschäftigungs-, Bildungs- oder sonstige Nachweise darzustellen, ohne die Privatsphäre zu verletzen. Überprüfen Sie alle Informationen zu jeder Person und jedem Unternehmen digital. [Weitere Informationen](../verifiable-credentials/index.yml)

---

### <a name="public-preview----device-code-flow-now-includes-an-app-verification-prompt"></a>Öffentliche Vorschau: Der Gerätecodeflow enthält jetzt eine Aufforderung zur App-Überprüfung

**Typ:** Neues Feature  
**Dienstkategorie:** Benutzerauthentifizierung  
**Produktfunktion:** Authentifizierungen (Anmeldungen)
 
Zur Verbesserung der Sicherheit wurde der [Gerätecodeflow](../develop/v2-oauth2-device-code.md) aktualisiert und um eine weitere Eingabeaufforderung ergänzt, mit der überprüft wird, ob sich der Benutzer bei der erwarteten App anmeldet. Der Rollout soll im Juni beginnen und voraussichtlich bis zum 30. Juni abgeschlossen sein.

Zur Vermeidung von Phishingangriffen, bei denen ein Angreifer den Benutzer dazu verleitet, sich bei einer schädlichen Anwendung anzumelden, wird die folgende Eingabeaufforderung hinzugefügt: „Möchten Sie sich bei [Anzeigename der Anwendung] anmelden?". Diese Aufforderung wird allen Benutzern beim Anmelden mit dem Gerätecodeflow angezeigt. Da es sich um eine Sicherheitsmaßnahme handelt, kann sie nicht entfernt oder umgangen werden. [Weitere Informationen](../develop/reference-breaking-changes.md#the-device-code-flow-ux-will-now-include-an-app-confirmation-prompt)

---

### <a name="public-preview----build-and-test-expressions-for-user-provisioning"></a>Öffentliche Vorschau: Erstellen und Testen von Ausdrücken für die Benutzerbereitstellung

**Typ:** Neues Feature  
**Dienstkategorie:** App-Bereitstellung  
**Produktfunktion:** Identity Lifecycle Management
 
Mit dem Ausdrucks-Generator können Sie Ausdrücke erstellen und testen, ohne den vollständigen Synchronisierungszyklus abwarten zu müssen. [Weitere Informationen](../app-provisioning/functions-for-customizing-application-data.md) 

---

### <a name="public-preview----enhanced-audit-logs-for-conditional-access-policy-changes"></a>Öffentliche Vorschau: Erweiterte Überwachungsprotokolle für Änderungen an der Richtlinie für bedingten Zugriff

**Typ:** Neues Feature  
**Dienstkategorie**: Bedingter Zugriff  
**Produktfunktion:** Identitätssicherheit und -schutz
 
Ein wichtiger Aspekt bei der Verwaltung des bedingten Zugriffs ist, die Änderungen zu verstehen, die im Laufe der Zeit an den Richtlinien erfolgen. Richtlinienänderungen können zu Unterbrechungen für Ihre Endbenutzer führen. Daher ist es wichtig, über die Änderungen Protokoll zu führen und es Administratoren zu ermöglichen, vorherige Richtlinienversionen wiederherzustellen. 

Neben der Angabe, von wem und wann eine Richtlinienänderung vorgenommen wurde, enthalten die Überwachungsprotokolle jetzt auch einen geänderten Eigenschaftenwert. Diese Änderung bietet Administratoren eine größere Transparenz der geänderten Zuweisungen, Bedingungen oder Steuerelemente. Wenn Sie eine frühere Version einer Richtlinie wiederherstellen möchten, können Sie die JSON-Darstellung der alten Version kopieren und die APIs für bedingten Zugriff verwenden, um den vorherigen Zustand der Richtlinie wiederherzustellen. [Weitere Informationen](../conditional-access/concept-conditional-access-policies.md)

---

### <a name="public-preview----sign-in-logs-include-authentication-methods-used-during-sign-in"></a>Öffentliche Vorschau: Anmeldeprotokolle enthalten Authentifizierungsmethoden, die während der Anmeldung verwendet werden

**Typ:** Neues Feature  
**Dienstkategorie:** MFA  
**Produktfunktion:** Überwachung und Berichterstellung
 

Administratoren können nun die sequenziellen Schritte sehen, die Benutzer bei der Anmeldung ausgeführt haben, einschließlich der bei der Anmeldung verwendeten Authentifizierungsmethoden. 

Um auf diese Details zuzugreifen, navigieren Sie zu den Azure AD-Anmeldeprotokollen, wählen Sie eine Anmeldung aus, und navigieren Sie dann zur Registerkarte „Details zur Authentifizierungsmethode“. Hier wurden Informationen wie die verwendete Methode, Details zur Methode (z. B. Telefonnummer, Telefonname), erfüllte Authentifizierungsanforderungen und Ergebnisdetails eingeschlossen. [Weitere Informationen](../reports-monitoring/concept-sign-ins.md)

---

### <a name="public-preview----pim-adds-support-for-abac-conditions-in-azure-storage-roles"></a>Öffentliche Vorschau: PIM fügt Unterstützung für ABAC-Bedingungen in Azure Storage-Rollen hinzu

**Typ:** Neues Feature  
**Dienstkategorie:** Privileged Identity Management  
**Produktfunktion**: Privileged Identity Management
 
Neben dem Public Preview der attributbasierten Zugriffssteuerung für eine bestimmte Azure RBAC-Rolle können Sie auch ABAC-Bedingungen in Privileged Identity Management für Ihre berechtigten Zuweisungen hinzufügen. [Weitere Informationen](../../role-based-access-control/conditions-overview.md#conditions-and-privileged-identity-management-pim)

---

### <a name="general-availability---conditional-access-and-identity-protection-reports-in-b2c"></a>Allgemeine Verfügbarkeit: Berichte zu bedingtem Zugriff und Identity Protection in B2C

**Typ:** Neues Feature  
**Dienstkategorie**: B2C – Kundenidentitätsverwaltung  
**Produktfunktion:** B2B/B2C

B2C unterstützt jetzt bedingten Zugriff und Identity Protection für Business-to-Consumer(B2C)-Apps und Benutzer. Dadurch können Kunden ihre Benutzer mit präzisen risiko- und standortbasierten Zugriffssteuerungen schützen. Mit diesen Features können Kunden jetzt die Signale betrachten und eine Richtlinie erstellen, um ihren Kunden mehr Sicherheit und Zugriff zu bieten. [Weitere Informationen](../../active-directory-b2c/conditional-access-identity-protection-overview.md)

---

### <a name="general-availability---kmsi-and-password-reset-now-in-next-generation-of-user-flows"></a>Allgemeine Verfügbarkeit: „Angemeldet bleiben“ und Kennwortrücksetzung jetzt in der nächsten Generation von Benutzerflows

**Typ:** Neues Feature  
**Dienstkategorie**: B2C – Kundenidentitätsverwaltung  
**Produktfunktion:** B2B/B2C

Für die nächste Generation der B2C-Benutzerflows wird jetzt die Option [Angemeldet bleiben](../../active-directory-b2c/session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) unterstützt. Hierbei können Kunden die Lebensdauer der Sitzung für die Benutzer ihrer Web- und nativen Anwendungen verlängern, indem sie ein beständiges Cookie verwenden. Durch dieses Feature bleibt die Sitzung auch dann aktiv, wenn der Benutzer den Browser schließt und erneut öffnet. Die Sitzung wird widerrufen, wenn sich der Benutzer abmeldet. Mit der Kennwortzurücksetzung können Benutzer ihr Kennwort über den Link „Kennwort vergessen“ zurücksetzen. Dadurch kann der Administrator auch das Zurücksetzen des abgelaufenen Kennworts des Benutzers im Azure AD B2C erzwingen. [Weitere Informationen](../../active-directory-b2c/add-password-reset-policy.md?pivots=b2c-user-flow)
 
---

### <a name="general-availability---new-log-analytics-workbook-application-role-assignment-activity"></a>Allgemeine Verfügbarkeit: Neue Aktivität für Protokollanalysen-Arbeitsmappe zur Zuweisung von Anwendungsrollen

**Typ:** Neues Feature  
**Dienstkategorie:** Benutzerzugriffsverwaltung  
**Produktfunktion:** Berechtigungsverwaltung
 
Es wurde eine neue Arbeitsmappe zum Anzeigen von Überwachungsereignissen bei Änderungen an der Zuweisung von Anwendungsrollen hinzugefügt. [Weitere Informationen](../governance/entitlement-management-logs-and-reporting.md)

---

### <a name="general-availability---next-generation-azure-ad-b2c-user-flows"></a>Allgemeine Verfügbarkeit: Azure AD B2C-Benutzerflows der nächsten Generation 

**Typ:** Neues Feature  
**Dienstkategorie**: B2C – Kundenidentitätsverwaltung  
**Produktfunktion:** B2B/B2C
 
Die neue vereinfachte Oberfläche für Benutzerflows bietet Featureparität mit Previewfunktionen und ist der Ort, an dem alle neuen Features bereitgestellt werden. Benutzer können innerhalb eines einzigen Benutzerflows neue Features aktivieren, sodass nicht bei jedem neuen Featurerelease mehrere Versionen erstellt werden müssen. Außerdem vereinfacht die neue benutzerfreundliche Oberfläche die Auswahl und Erstellung von Benutzerflows. Einen Leitfaden zur Verwendung dieses Features finden Sie unter [Erstellen von Benutzerflows in Azure AD B2C](../../active-directory-b2c/tutorial-create-user-flows.md?pivots=b2c-user-flow). [Weitere Informationen](../../active-directory-b2c/user-flow-versions.md)

---

### <a name="general-availability---azure-active-directory-threat-intelligence-for-sign-in-risk"></a>Allgemeine Verfügbarkeit: Azure Active Directory Threat Intelligence für Anmelderisiko

**Typ:** Neues Feature  
**Dienstkategorie:** Schutz der Identität (Identity Protection)  
**Produktfunktion:** Identitätssicherheit und -schutz
 
Diese neue Erkennung dient als Ad-hoc-Methode für unsere Sicherheitsteams, um Sie im Angriffsfall zu benachrichtigen und Ihre Benutzer zu schützen: Bei Beobachtung eines Angriffs wird das Sitzungsrisiko der Benutzer auf „Hoch“ gesetzt. Außerdem werden die betroffenen Anmeldungen als riskant gekennzeichnet. Diese Erkennung folgt auf die Azure Active Directory Threat Intelligence für Benutzerrisikoerkennung, um eine volle Abdeckung der verschiedenen von Microsoft-Sicherheitsteams beobachteten Angriffe zu ermöglichen. [Weitere Informationen](../identity-protection/concept-identity-protection-risks.md#user-linked-detections)
 
---

### <a name="general-availability---conditional-access-named-locations-improvements"></a>Allgemeine Verfügbarkeit: Verbesserungen bei bedingtem Zugriff mit benannten Standorten

**Typ:** Neues Feature  
**Dienstkategorie**: Bedingter Zugriff  
**Produktfunktion:** Identitätssicherheit und -schutz
 
IPv6-Unterstützung an benannten Standorten ist jetzt allgemein verfügbar. Updates umfassen:

- Hinzufügung einer Funktion zum Definieren von IPv6-Adressbereichen
- Erhöhung des Grenzwerts für benannte Standorte von 90 auf 195
- Erhöhung des Limits für IP-Adressbereiche pro benanntem Standort von 1.200 auf 2.000
- Hinzufügung von Funktionen zum Suchen und Sortieren benannter Orte sowie zum Filtern nach Standorttyp und Vertrauenstyp
- Ergänzung der Anmeldeprotokolle durch benannte Standorte, zu denen eine Anmeldung gehört
 
Um zu verhindern, dass Administratoren problematisch benannte Standorte definieren, wurden darüber hinaus zusätzliche Überprüfungen hinzugefügt, wodurch das Risiko von Fehlkonfigurationen verringert wird. [Weitere Informationen](../conditional-access/location-condition.md)

---

### <a name="general-availability---restricted-guest-access-permissions-in-azure-ad"></a>Allgemeine Verfügbarkeit: Eingeschränkte Gastzugriffsberechtigungen in Azure AD

**Typ:** Neues Feature  
**Dienstkategorie:** Benutzerverwaltung  
**Produktfunktion**: Verzeichnis
 
Berechtigungen auf Verzeichnisebene für Gastbenutzer wurden aktualisiert. Durch diese Berechtigungen können Administratoren zusätzliche Einschränkungen und Kontrollen für den Zugriff externer Gastbenutzer verlangen.

Administratoren können jetzt weitere Einschränkungen für den Zugriff externer Gäste auf Profil- und Mitgliedschaftsinformationen von Benutzern und Gruppen hinzufügen. Außerdem können Kunden den externen Benutzerzugriff durch Ausblenden von Gruppenmitgliedschaften bedarfsorientiert verwalten. Dies schließt auch die Einschränkung von Gastbenutzern beim Anzeigen von Mitgliedschaften der Gruppe(n) ein, in der/denen sie sich gerade befinden. Weitere Informationen finden Sie unter [Einschränken von Gastzugriffsberechtigungen in Azure Active Directory](../enterprise-users/users-restrict-guest-permissions.md).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2021"></a>Neue Verbund-Apps im Azure AD-Anwendungskatalog verfügbar – Mai 2021

**Typ:** Neues Feature  
**Dienstkategorie**: Unternehmens-Apps  
**Produktfunktion:** Integration von Drittanbieterprodukten
 
Sie können ab sofort das Erstellen, Aktualisieren und Löschen von Benutzerkonten für diese neu integrierten Apps automatisieren:

- [AuditBoard](../saas-apps/auditboard-provisioning-tutorial.md)
- [Cisco Umbrella User Management](../saas-apps/cisco-umbrella-user-management-provisioning-tutorial.md)
- [Insite LMS](../saas-apps/insite-lms-provisioning-tutorial.md)
- [kpifire](../saas-apps/kpifire-provisioning-tutorial.md)
- [UNIFI](../saas-apps/unifi-provisioning-tutorial.md)

Weitere Informationen dazu, wie Sie Ihre Organisation durch automatisierte Bereitstellung von Benutzerkonten besser schützen können, finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2021"></a>Neue Verbund-Apps im Azure AD-Anwendungskatalog verfügbar – Mai 2021

**Typ:** Neues Feature  
**Dienstkategorie**: Unternehmens-Apps  
**Produktfunktion:** Integration von Drittanbieterprodukten
 
Im Mai 2021 haben wir die folgenden 29 neuen Anwendungen mit Verbundunterstützung in unseren App-Katalog aufgenommen:

[InviteDesk](https://app.invitedesk.com/login), [Webrecruit ATS](https://id-test.webrecruit.co.uk/), [Workshop](../saas-apps/workshop-tutorial.md), [Gravity Sketch](https://landingpad.me/), [JustLogin](../saas-apps/justlogin-tutorial.md), [Custellence](https://custellence.com/sso/), [WEVO](https://hello.wevoconversion.com/login), [AppTec360 MDM](https://www.apptec360.com/ms/autopilot.html), [Filemail](https://www.filemail.com/login),[Ardoq](../saas-apps/ardoq-tutorial.md), [Leadfamly](../saas-apps/leadfamly-tutorial.md), [Documo](../saas-apps/documo-tutorial.md), [Autodesk SSO](../saas-apps/autodesk-sso-tutorial.md), [Check Point Harmony Connect](../saas-apps/check-point-harmony-connect-tutorial.md), [BrightHire](https://app.brighthire.ai/), [Rescana](../saas-apps/rescana-tutorial.md), [Bluewhale](https://cloud.bluewhale.dk/), [AlacrityLaw](../saas-apps/alacritylaw-tutorial.md), [Equisolve](../saas-apps/equisolve-tutorial.md), [Zip](../saas-apps/zip-tutorial.md), [Cognician](../saas-apps/cognician-tutorial.md), [Acra](https://www.acrasuite.com/), [VaultMe](https://app.vaultme.com/#/signIn), [TAP App Security](../saas-apps/tap-app-security-tutorial.md), [Cavelo Office365 Cloud Connector](https://dashboard.prod.cavelodata.com/), [Clebex](../saas-apps/clebex-tutorial.md), [Banyan Command Center](../saas-apps/banyan-command-center-tutorial.md), [Check Point Remote Access VPN](../saas-apps/check-point-remote-access-vpn-tutorial.md), [LogMeIn](../saas-apps/logmein-tutorial.md)

Die Dokumentation zu allen Anwendungen finden Sie auch hier: https://aka.ms/AppsTutorial.

Informationen zum Auflisten Ihrer Anwendung im Azure AD-App-Katalog finden Sie hier: https://aka.ms/AzureADAppRequest.

---

### <a name="improved-conditional-access-messaging-for-android-and-ios"></a>Verbessertes Messaging für bedingten Zugriff für Android und iOS

**Typ:** Geändertes Feature  
**Dienstkategorie:** Geräteregistrierung und -verwaltung  
**Produktfunktion:** Endbenutzerumgebungen
 

Die Formulierung auf dem Bildschirm für bedingten Zugriff wurde aktualisiert, der Benutzern angezeigt wird, wenn ihr Zugriff auf Unternehmensressourcen blockiert wird. Die Benutzer werden blockiert, bis sie ihr Gerät bei der Verwaltung mobiler Geräte (Mobile Device Management, MDM) registrieren. Diese Verbesserungen gelten für die Android- und iOS-/iPadOS-Plattformen. Die folgenden Änderungen wurden vorgenommen:

- „Help us keep your device secure“ wurde in „Set up your device to get access“ geändert.
- „Your sign-in was successful but your admin requires your device to be managed by Microsoft to access this resource.“ wurde in „[Name der Organisation] requires you to secure this device before you can access [organization’s name] email, files, and data.“ geändert. 
- „Enroll Now“ wurde in „Continue“ geändert.

Die Informationen unter [Registrieren Ihres Android Enterprise-Geräts](https://support.microsoft.com/topic/enroll-your-android-enterprise-device-d661c82d-fa28-5dfd-b711-6dff41ae83bb) sind veraltet.

---

### <a name="azure-information-protection-service-will-begin-asking-for-consent"></a>Aufforderung zur Einwilligung durch Information Protection-Dienst

**Typ:** Geändertes Feature  
**Dienstkategorie:** Authentifizierungen (Anmeldungen)  
**Produktfunktion:** Benutzerauthentifizierung
 
Der Azure Information Protection-Dienst meldet Benutzer bei dem Mandanten an, der das Dokument bei Bereitstellung des Zugriffs auf das Dokument verschlüsselt hat.  Ab Juni wird der Benutzer von Azure AD aufgefordert, seine Einwilligung zu geben, wenn dieser Zugriff organisationsübergreifend gewährt wird.  Dadurch wird sichergestellt, dass der Benutzer erfährt, dass die Organisation, die das Dokument besitzt, im Rahmen des Dokumentzugriffs einige Informationen über den Benutzer erfasst. [Weitere Informationen](/azure/information-protection/known-issues#sharing-external-doc-types-across-tenants)
 
---

### <a name="provisioning-logs-schema-change-impacting-graph-api-and-azure-monitor-integration"></a>Beeinträchtigung von Graph-API und Azure Monitor-Integration durch Änderung des Bereitstellungsprotokollschemas

**Typ:** Geändertes Feature  
**Dienstkategorie:** App-Bereitstellung  
**Produktfunktion:** Überwachung und Berichterstellung
 

Die Attribute „Action“ und „statusInfo“ werden in „provisioningAction“ und „provisoiningStatusInfo“ geändert. Aktualisieren Sie alle Skripts, die Sie mithilfe der [Graph-API für Bereitstellungsprotokolle](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta&preserve-view=true) oder mithilfe von [Azure Monitor-Integrationen](../app-provisioning/application-provisioning-log-analytics.md) erstellt haben. 
 

---

### <a name="new-arm-api-to-manage-pim-for-azure-resources-and-azure-ad-roles"></a>Neue ARM-API zum Verwalten von PIM für Azure-Ressourcen und Azure AD Rollen

**Typ:** Geändertes Feature  
**Dienstkategorie:** Privileged Identity Management  
**Produktfunktion**: Privileged Identity Management
 
Eine aktualisierte Version der PIM-API für Azure Resource- und Azure AD-Rollen wurde veröffentlicht. Die PIM-API für Azure-Ressourcenrollen wird jetzt unter dem ARM-API-Standard veröffentlicht, der auf die Rollenverwaltungs-API für die reguläre Azure-Rollenzuweisung ausgerichtet ist. Andererseits wird die PIM-API für Azure AD-Rollen auch unter der Graph-API veröffentlicht, die auf die unifiedRoleManagement-APIs ausgerichtet ist. Diese Änderung bietet unter anderem die folgenden Vorteile:

- Ausrichtung der PIM-API auf Objekte in ARM und Graph für die Rollenverwaltung; Verringerung der Notwendigkeit, PIM für das Onboarding neuer Azure-Ressourcen aufzurufen. 
- Alle Azure-Ressourcen funktionieren automatisch mit der neuen PIM-API.
- Verringerung der Notwendigkeit, PIM für die Rollendefinition aufzurufen oder eine PIM-Ressourcen-ID beizubehalten
- Unterstützung von Nur-App-API-Berechtigungen in PIM für Azure AD- und Azure Resource-Rollen

Die vorherige Version der PIM-API unter „/privilegedaccess“ funktioniert weiterhin, aber es wird empfohlen, in Zukunft zu dieser neuen API zu wechseln. [Weitere Informationen](../privileged-identity-management/pim-apis.md)
 
---

### <a name="revision-of-roles-in-azure-ad-entitlement-management"></a>Revision von Rollen in der Azure AD-Berechtigungsverwaltung

**Typ:** Geändertes Feature  
**Dienstkategorie:** Rollen  
**Produktfunktion:** Berechtigungsverwaltung
 
Vor Kurzem wurde die neue Rolle „Identity Governance-Administrator“ eingeführt. Diese Rolle ersetzt die Rolle „Benutzeradministrator“ beim Verwalten von Katalogen und Zugriffspaketen in der Azure AD-Berechtigungsverwaltung. Wenn Sie Administratoren die Rolle „Benutzeradministrator“ zugewiesen haben oder diese Rolle zum Verwalten von Zugriffspaketen in der Azure AD-Berechtigungsverwaltung aktivieren lassen, verwenden Sie stattdessen die Rolle „Identity Governance-Administrator“. Die Rolle „Benutzeradministrator“ stellt keine Administratorrechte mehr für Kataloge oder Zugriffspakete bereit. [Weitere Informationen](../governance/identity-governance-overview.md#appendix---least-privileged-roles-for-managing-in-identity-governance-features)

---

## <a name="april-2021"></a>April 2021

### <a name="bug-fixed---azure-ad-will-no-longer-double-encode-the-state-parameter-in-responses"></a>Fehler behoben: Zustandsparameter in Antworten werden von Azure AD nicht mehr doppelt codiert

**Typ:** Korrigiert  
**Dienstkategorie:** Authentifizierungen (Anmeldungen)  
**Produktfunktion:** Benutzerauthentifizierung
 
In Azure AD wurde eine Fehlerbehebung für einen Fehler in der `/authorize`-Antwort auf eine Clientanwendung ermittelt, getestet und veröffentlicht.  Der `state`-Parameter wurde von Azure AD beim Senden von Antworten an den Client fälschlicherweise zweimal URL-codiert.  Dies kann dazu führen, dass eine Clientanwendung die Anforderung aufgrund eines Konflikts in den Zustandsparametern zurückweist. [Weitere Informationen](../develop/reference-breaking-changes.md#bug-fix-azure-ad-will-no-longer-url-encode-the-state-parameter-twice) 

---

### <a name="users-can-only-create-security-and-microsoft-365-groups-in-azure-portal-being-deprecated"></a>Benutzer können Sicherheits- und Microsoft 365-Gruppen nicht mehr nur im Azure-Portal erstellen

**Typ:** Plan für Änderung  
**Dienstkategorie**: Gruppenverwaltung  
**Produktfunktion**: Verzeichnis
 
Benutzer sind bei der Erstellung von Sicherheits- und Microsoft 365-Gruppen nicht mehr auf das Azure-Portal beschränkt. Mit der neuen Einstellung können Benutzer Sicherheitsgruppen im Azure-Portal, in PowerShell und in der API erstellen. Benutzer müssen die neue Einstellung überprüfen und aktualisieren. [Weitere Informationen](../enterprise-users/groups-self-service-management.md)

---

### <a name="public-preview----external-identities-self-service-sign-up-in-aad-using-email-one-time-passcode-accounts"></a>Öffentliche Vorschau: Self-Service-Registrierung für External Identities in AAD mit E-Mail-Konten mit Einmalkennung

**Typ:** Neues Feature  
**Dienstkategorie:** B2B  
**Produktfunktion:** B2B/B2C
 
Externe Benutzer können jetzt Konten mit Einmalkennung per E-Mail verwenden, um sich bei Azure AD-Erstanbieter- und branchenspezifischen Apps anzumelden. [Weitere Informationen](../external-identities/one-time-passcode.md)

---

### <a name="general-availability---external-identities-self-service-sign-up"></a>Allgemeine Verfügbarkeit: Self-Service-Registrierung für External Identities

**Typ:** Neues Feature  
**Dienstkategorie:** B2B  
**Produktfunktion:** B2B/B2C
 
Self-Service-Registrierung für externe Benutzer ist jetzt allgemein verfügbar. Mit diesem neuen Feature können sich externe Benutzer jetzt per Self-Service-Registrierung bei einer Anwendung registrieren. 

Sie können benutzerdefinierte Erfahrungen für diese externen Benutzer erstellen. Dazu gehört u. a., während des Registrierungsprozesses Informationen zu Ihren Benutzern zu erfassen und externe Identitätsanbieter wie Facebook und Google zuzulassen. Sie können auch Clouddrittanbieter für verschiedene Funktionen wie Identitätsüberprüfung oder Genehmigung von Benutzern integrieren. [Weitere Informationen](../external-identities/self-service-sign-up-overview.md)
 
---

### <a name="general-availability---azure-ad-b2c-phone-sign-up-and-sign-in-using-built-in-policy"></a>Allgemeine Verfügbarkeit: Telefonregistrierung und -anmeldung mit integrierten Richtlinien in Azure AD B2C

**Typ:** Neues Feature  
**Dienstkategorie**: B2C – Kundenidentitätsverwaltung  
**Produktfunktion:** B2B/B2C
 
Mit der integrierten B2C-Richtlinie für Telefonregistrierung und -anmeldung können IT-Administratoren und Entwickler einer Organisation es ihren Endbenutzern ermöglichen, sich in Benutzerflows mithilfe einer Telefonnummer anzumelden und zu registrieren. Mit diesem Feature können Links zu Haftungsausschlüssen wie Datenschutzrichtlinien und Nutzungsbedingungen angepasst und auf der Seite angezeigt werden, bevor der Endbenutzer mit dem Empfang der Einmalkennung per SMS fortfährt. [Weitere Informationen](../../active-directory-b2c/phone-authentication-user-flows.md)
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---april-2021"></a>Neue Verbund-Apps im Azure AD-Anwendungskatalog verfügbar – April 2021

**Typ:** Neues Feature  
**Dienstkategorie**: Unternehmens-Apps  
**Produktfunktion:** Integration von Drittanbieterprodukten

Im April 2021 wurden die folgenden 31 neuen Anwendungen mit Verbundunterstützung in unseren App-Katalog aufgenommen:

[Zii Travel Azure AD Connect](http://ziitravel.com/), [Cerby](../saas-apps/cerby-tutorial.md), [Selflessly](https://app.selflessly.io/sign-in), [Apollo CX](https://apollo.cxlabs.de/sso/aad), [Pedagoo](https://account.pedagoo.com/), [Measureup](https://account.measureup.com/), [Wistec Education](https://wisteceducation.fi/login/index.php), [ProcessUnity](../saas-apps/processunity-tutorial.md), [Cisco Intersight](../saas-apps/cisco-intersight-tutorial.md), [Codility](../saas-apps/codility-tutorial.md), [H5mag](https://account.h5mag.com/auth/request-access/ms365), [Check Point Identity Awareness](../saas-apps/check-point-identity-awareness-tutorial.md), [Jarvis](https://jarvis.live/login), [desknet's NEO](../saas-apps/desknets-neo-tutorial.md), [SDS & Chemical Information Management](../saas-apps/sds-chemical-information-management-tutorial.md), [Wúru App](../saas-apps/wuru-app-tutorial.md), [Holmes](../saas-apps/holmes-tutorial.md), [Tide Multi Tenant](https://gallery.tideapp.co.uk/), [Telenor](https://admin.smartansatt.telenor.no/), [Yooz US](https://us1.getyooz.com/?kc_idp_hint=microsoft), [Mooncamp](https://app.mooncamp.com/#/login), [inwise SSO](https://app.inwise.com/defaultsso.aspx), [Ecolab Digital Solutions](https://ecolabb2c.b2clogin.com/account.ecolab.com/oauth2/v2.0/authorize?p=B2C_1A_Connect_OIDC_SignIn&client_id=01281626-dbed-4405-a430-66457825d361&nonce=defaultNonce&redirect_uri=https://jwt.ms&scope=openid&response_type=id_token&prompt=login), [Taguchi Digital Marketing System](https://login.taguchi.com.au/), [XpressDox EU Cloud](https://test.xpressdox.com/Authentication/Login.aspx), [EZSSH](https://docs.keytos.io/getting-started/registering-a-new-tenant/registering_app_in_tenant/), [EZSSH Client](https://portal.ezssh.io/signup), [Verto 365](https://www.vertocloud.com/Login/), [KPN Grip](https://www.grip-on-it.com/), [AddressLook](https://portal.bbsonlineservices.net/Manage/AddressLook), [Cornerstone Single Sign-On](../saas-apps/cornerstone-ondemand-tutorial.md)

Die Dokumentation zu allen Anwendungen finden Sie auch hier: https://aka.ms/AppsTutorial

Informationen zum Auflisten Ihrer Anwendung im Azure AD-App-Katalog finden Sie hier: https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---april-2021"></a>Neue Bereitstellungsconnectors im Azure AD-Anwendungskatalog – April 2021

**Typ:** Neues Feature  
**Dienstkategorie:** App-Bereitstellung  
**Produktfunktion:** Integration von Drittanbieterprodukten
 
Sie können ab sofort das Erstellen, Aktualisieren und Löschen von Benutzerkonten für diese neu integrierten Apps automatisieren:

- [Bentley – Automatic User Provisioning](../saas-apps/bentley-automatic-user-provisioning-tutorial.md)
- [Boxcryptor](../saas-apps/boxcryptor-provisioning-tutorial.md)
- [BrowserStack Single Sign-on](../saas-apps/browserstack-single-sign-on-provisioning-tutorial.md)
- [Eletive](../saas-apps/eletive-provisioning-tutorial.md)
- [Jostle](../saas-apps/jostle-provisioning-tutorial.md)
- [Olfeo SAAS](../saas-apps/olfeo-saas-provisioning-tutorial.md)
- [Proware](../saas-apps/proware-provisioning-tutorial.md)
- [Segment](../saas-apps/segment-provisioning-tutorial.md)

Weitere Informationen dazu, wie Sie Ihre Organisation mit automatisierter Bereitstellung von Benutzerkonten besser sichern können, finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
 
---

### <a name="introducing-new-versions-of-page-layouts-for-b2c"></a>Einführung neuer Versionen von Seitenlayouts für B2C

**Typ:** Geändertes Feature  
**Dienstkategorie**: B2C – Kundenidentitätsverwaltung  
**Produktfunktion:** B2B/B2C
 
Die [Seitenlayouts](../../active-directory-b2c/page-layout.md) für B2C-Szenarien in Azure AD B2C wurden aktualisiert, um Sicherheitsrisiken durch Einführung der neuen Versionen von jQuery und Handlebars JS zu verringern.
 
---

### <a name="updates-to-sign-in-diagnostic"></a>Updates für die Anmeldediagnose

**Typ:** Geändertes Feature  
**Dienstkategorie:** Berichterstellung  
**Produktfunktion:** Überwachung und Berichterstellung
 
Der Abdeckungsbereich des Anmeldediagnosetools wurde um weitere Szenarien erweitert. 

Mit diesem Update werden nun die folgenden ereignisbezogenen Szenarien in die Ergebnisse der Anmeldediagnose einbezogen: 
- Ereignisse aufgrund von Problemen bei der Konfiguration von Unternehmensanwendungen
- Dienstanbieterbezogene (anwendungsseitige) Ereignisse bei Unternehmensanwendungen
- Ereignisse aufgrund falscher Anmeldeinformationen 

Diese Ergebnisse zeigen kontextbezogene und relevante Details zum Ereignis und den zu ergreifenden Abhilfemaßnahmen an. Außerdem liefert die Anmeldediagnose bei Szenarien, in denen keine umfassende kontextbezogene Diagnose vorhanden ist, aussagekräftigeren Inhalt zum Fehlerereignis.

Weitere Informationen finden Sie unter [Welche Funktion hat die Anmeldediagnose in Azure AD?](../reports-monitoring/overview-sign-in-diagnostics.md)

---
### <a name="azure-ad-connect-cloud-sync-general-availability-refresh"></a>Aktualisierung der allgemeinen Verfügbarkeit der Azure AD Connect-Cloudsynchronisierung 
**Typ:** Geändertes Feature  
**Dienstkategorie:** Azure AD Connect-Cloudsynchronisierung **Produktfunktion:** Verzeichnis

Die Azure AD Connect-Cloudsynchronisierung verfügt jetzt über einen aktualisierten Agent (Versions-Nr. – 1.1.359). Weitere Informationen zu Agent-Updates, einschließlich Fehlerbehebungen, finden Sie im [Versionsverlauf](../cloud-sync/reference-version-history.md). Mit dem aktualisierten Agent können Cloudsynchronisierungskunden ihre gMSA Berechtigung mithilfe von GMSA-Cmdlets differenziert festlegen und zurücksetzen. Darüber hinaus wurde der Grenzwert für die Synchronisierung von Mitgliedern mithilfe der Gruppenbereichsfilterung von 1.499 auf 50.000 (50.000) Mitglieder erhöht. 

Sehen Sie sich den neu verfügbaren [Ausdrucks-Generator](../cloud-sync/how-to-expression-builder.md#deploy-the-expression) für die Cloudsynchronisierung an, mit dem Sie komplexe Ausdrücke sowie einfache Ausdrücke erstellen können, wenn Sie Attributwerte mithilfe von Attributzuordnung von AD in Azure AD transformieren.

---

## <a name="march-2021"></a>März 2021

### <a name="guidance-on-how-to-enable-support-for-tls-12-in-your-environment-in-preparation-for-upcoming-azure-ad-tls-1011-deprecation"></a>Anleitung zum Aktivieren der Unterstützung für TLS 1.2 in Ihrer Umgebung, da TLS 1.0/1.1 in Azure AD demnächst eingestellt wird

**Typ:** Plan für Änderung  
**Dienstkategorie:** –  
**Produktfunktion:** Standards

Die folgenden Protokolle werden ab dem 30. Juni 2021 weltweit in allen Azure Active Directory-Regionen als veraltet markiert:


- TLS 1.0
- TLS 1.1
- 3DES-Verschlüsselungssammlung (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Betroffene Umgebungen:

- Kommerzielle Azure-Cloud
- Office 365 GCC und WW

Weitere Informationen finden Sie im Artikel [Aktivieren der Unterstützung für TLS 1.2 in Ihrer Umgebung für das veraltete TLS 1.1 und 1.0 in Azure AD](/troubleshoot/azure/active-directory/enable-support-tls-environment).

---

### <a name="public-preview----azure-ad-entitlement-management-now-supports-multi-geo-sharepoint-online"></a>Öffentliche Vorschau: Azure AD-Berechtigungsverwaltung unterstützt jetzt SharePoint Online Multi-Geo

**Typ:** Neues Feature  
**Dienstkategorie:** Sonstige  
**Produktfunktion:** Berechtigungsverwaltung
 
Für Organisationen, die SharePoint Online mit mehreren geografischen Standorten verwenden, können Sie jetzt Websites in bestimmten Umgebungen mit mehreren geografischen Standorten zu Ihren Zugriffspaketen mit Berechtigungsverwaltung hinzufügen. [Weitere Informationen](../governance/entitlement-management-catalog-create.md#add-a-multi-geo-sharepoint-site)

---

### <a name="public-preview----restore-deleted-apps-from-app-registrations"></a>Öffentliche Vorschau: Wiederherstellen gelöschter Apps aus App-Registrierungen

**Typ:** Neues Feature  
**Dienstkategorie:** Sonstige  
**Produktfunktion:** Entwickleroberfläche
 
Kunden können jetzt gelöschte App-Registrierungen im Azure-Portal anzeigen, wiederherstellen und endgültig entfernen. Dies gilt nur für Anwendungen, die einem Verzeichnis zugeordnet sind, nicht für Anwendungen eines persönlichen Microsoft-Kontos. [Weitere Informationen](../develop/howto-restore-app.md)
 
---

### <a name="public-preview----new-user-action-in-conditional-access-for-registering-or-joining-devices"></a>Öffentliche Vorschau: Neue „Benutzeraktion“ für bedingten Zugriff zum Registrieren oder Hinzufügen von Geräten

**Typ:** Neues Feature  
**Dienstkategorie**: Bedingter Zugriff  
**Produktfunktion:** Identitätssicherheit und -schutz
 
 Für bedingten Zugriff ist eine neue Benutzeraktion zum Registrieren oder Hinzufügen von Geräten verfügbar. Mit dieser Benutzeraktion können Sie Richtlinien für die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) für die Azure AD-Geräteregistrierung steuern. 

Derzeit können Sie mit dieser Benutzeraktion MFA nur als Steuerelement aktivieren, wenn Benutzer Geräte registrieren oder zu Azure AD hinzufügen. Andere Steuerelemente, die von der Azure AD-Geräteregistrierung abhängen oder darauf nicht anwendbar sind, werden mit dieser Benutzeraktion deaktiviert. [Weitere Informationen](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions) 
 
---

### <a name="public-preview----optimize-connector-groups-to-use-the-closest-application-proxy-cloud-service"></a>Öffentliche Vorschau: Optimieren von Connectorgruppen zur Verwendung des nächstgelegenen Anwendungsproxy-Clouddiensts

**Typ:** Neue Funktion  
**Dienstkategorie:** Anwendungsproxy  
**Produktfunktion:** Zugriffssteuerung
 
Mit dieser neuen Funktion können Connectorgruppen dem nächstgelegenen regionalen Anwendungsproxydienst zugewiesen werden, in dem eine Anwendung gehostet wird. Dadurch kann die App-Leistung in Szenarien verbessert werden, in denen Apps in anderen Regionen als der Region des Basismandanten gehostet werden. [Weitere Informationen](../app-proxy/application-proxy-network-topology.md#optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview) 
 
---

### <a name="public-preview----external-identities-self-service-sign-up-in-aad-using-email-one-time-passcode-accounts"></a>Öffentliche Vorschau: Self-Service-Registrierung für External Identities in AAD mit E-Mail-Konten mit Einmalkennung

**Typ:** Neues Feature  
**Dienstkategorie:** B2B  
**Produktfunktion:** B2B/B2C

Externe Benutzer können jetzt Konten mit Einmalkennung per E-Mail verwenden, um sich bei Azure AD-Erstanbieteranwendungen und -Branchenanwendungen anzumelden. [Weitere Informationen](../external-identities/one-time-passcode.md)

---

### <a name="public-preview----availability-of-ad-fs-sign-ins-in-azure-ad"></a>Öffentliche Vorschau: Verfügbarkeit von AD FS-Anmeldedaten in Azure AD

**Typ:** Neues Feature  
**Dienstkategorie:** Authentifizierungen (Anmeldungen)  
**Produktfunktion:** Überwachung und Berichterstellung
 
AD FS-Anmeldeaktivitäten können jetzt in Azure AD-Aktivitätsberichte integriert werden, um eine einheitliche Ansicht einer hybriden Identitätsinfrastruktur zu ermöglichen. Mit dem Bericht für Azure AD-Anmeldungen, Log Analytics und Azure Monitor Workbooks ist eine tiefgehende Analyse von AAD- und AD FS-Anmeldeszenarien wie AD FS-Kontosperrungen, ungültige Kennworteingaben und hohe Anzahl unerwarteter Anmeldeversuche möglich.

Weitere Informationen finden Sie unter [AD FS-Anmeldungen in Azure AD mit Connect Health](../hybrid/how-to-connect-health-ad-fs-sign-in.md).

---

### <a name="general-availability---staged-rollout-to-cloud-authentication"></a>Allgemeine Verfügbarkeit: Gestaffelter Rollout der Cloudauthentifizierung

**Typ:** Neues Feature  
**Dienstkategorie**: AD Connect  
**Produktfunktion:** Benutzerauthentifizierung
 
Der gestaffelte Rollout der Cloudauthentifizierung ist jetzt allgemein verfügbar. Mit dem Feature für gestaffelte Rollouts können Sie Gruppen von Benutzern selektiv mit Cloudauthentifizierungsmethoden wie Passthrough-Authentifizierung (PTA) oder Kennworthashsynchronisierung (PHS) testen. In der Zwischenzeit verwenden alle anderen Benutzer in den Verbunddomänen weiterhin Verbunddienste wie AD FS oder andere Verbunddienste, um Benutzer zu authentifizieren. [Weitere Informationen](../hybrid/how-to-connect-staged-rollout.md)

---

### <a name="general-availability---user-type-attribute-can-now-be-updated-in-the-azure-admin-portal"></a>Allgemeine Verfügbarkeit: Benutzertypattribut kann jetzt im Azure-Verwaltungsportal aktualisiert werden

**Typ:** Neues Feature  
**Dienstkategorie:** Benutzeroberfläche und Benutzerverwaltung  
**Produktfunktion:** Benutzerverwaltung
 
Kunden können nun den Benutzertyp von Azure AD-Benutzern aktualisieren, wenn sie ihre Benutzerprofilinformationen im Azure-Verwaltungsportal aktualisieren. Der Benutzertyp kann auch in Microsoft Graph aktualisiert werden. Weitere Informationen finden Sie unter [Hinzufügen oder Aktualisieren von Benutzerprofilinformationen](active-directory-users-profile-azure-portal.md).
 
---

### <a name="general-availability---replica-sets-for-azure-active-directory-domain-services"></a>Allgemeine Verfügbarkeit: Replikatgruppen für Azure Active Directory Domain Services

**Typ:** Neues Feature  
**Dienstkategorie**: Azure AD Domain Services  
**Produktfunktion**: Azure AD Domain Services
 
Die Funktionalität von Replikatgruppen in Azure AD DS ist jetzt allgemein verfügbar. [Weitere Informationen](../../active-directory-domain-services/concepts-replica-sets.md)
 
---

### <a name="general-availability---collaborate-with-your-partners-using-email-one-time-passcode-in-the-azure-government-cloud"></a>Allgemeine Verfügbarkeit: Zusammenarbeit mit ihren Partnern mit Konten mit Einmalkennung per E-Mail in der Azure Government-Cloud

**Typ:** Neues Feature  
**Dienstkategorie:** B2B  
**Produktfunktion:** B2B/B2C
 
Organisationen in der Microsoft Azure Government-Cloud können Ihren Gästen jetzt ermöglichen, Einladungen mit Einmalkennung per E-Mail einzulösen. Dadurch wird sichergestellt, dass alle Gastbenutzer ohne Azure AD-, Microsoft- oder Gmail-Konto in der Azure Government Cloud weiterhin mit ihren Partnern zusammenarbeiten können, indem sie einen temporären Code anfordern und eingeben, um sich bei gemeinsam genutzten Ressourcen anzumelden. [Weitere Informationen](../external-identities/one-time-passcode.md#note-for-azure-us-government-customers)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---march-2021"></a>Neue Verbund-Apps im Azure AD-Anwendungskatalog verfügbar – März 2021

**Typ:** Neues Feature  
**Dienstkategorie**: Unternehmens-Apps  
**Produktfunktion:** Integration von Drittanbieterprodukten
 
Im März 2021 wurden die folgenden 37 neuen Anwendungen mit Verbundunterstützung in unseren App-Katalog aufgenommen:

[Bambuser Live Video Shopping](https://lcx.bambuser.com/), [DeepDyve Inc](https://www.deepdyve.com/azure-sso), [Moqups](../saas-apps/moqups-tutorial.md), [RICOH Spaces Mobile](https://ricohspaces.app/welcome), [Flipgrid](https://auth.flipgrid.com/), [hCaptcha Enterprise](../saas-apps/hcaptcha-enterprise-tutorial.md), [SchoolStream ASA](https://jsd.schoolstreamk12.com/ASA/ASAlogin.aspx), [TransPerfect GlobalLink Dashboard](../saas-apps/transperfect-globallink-dashboard-tutorial.md), [SimplificaCI](https://app.simplificaci.com.br/), [Thrive LXP](../saas-apps/thrive-lxp-tutorial.md), [Lexonis TalentScape](../saas-apps/lexonis-talentscape-tutorial.md), [Exium](../saas-apps/exium-tutorial.md), [Sapient](../saas-apps/sapient-tutorial.md), [TrueChoice](../saas-apps/truechoice-tutorial.md), [RICOH Spaces](https://ricohspaces.app/welcome), [Saba Cloud](../saas-apps/learning-at-work-tutorial.md), [Acunetix 360](../saas-apps/acunetix-360-tutorial.md), [Exceed.ai](../saas-apps/exceed-ai-tutorial.md), [GitHub Enterprise Managed User](../saas-apps/github-enterprise-managed-user-tutorial.md), [Enterprise Vault.cloud for Outlook](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=openid%20profile%20User.Read&client_id=7176efe5-e954-4aed-b5c8-f5c85a980d3a&nonce=4b9e1981-1bcb-4938-a283-86f6931dc8cb), [Smartlook](../saas-apps/smartlook-tutorial.md), [Accenture Academy](../saas-apps/accenture-academy-tutorial.md), [Onshape](../saas-apps/onshape-tutorial.md), [Tradeshift](../saas-apps/tradeshift-tutorial.md), [JuriBlox](../saas-apps/juriblox-tutorial.md), [SecurityStudio](../saas-apps/securitystudio-tutorial.md), [ClicData](https://app.clicdata.com/), [Evergreen](../saas-apps/evergreen-tutorial.md), [Patchdeck](https://patchdeck.com/ad_auth/authenticate/), [FAX.PLUS](../saas-apps/fax-plus-tutorial.md), [ValidSign](../saas-apps/validsign-tutorial.md), [AWS Single Sign-on](../saas-apps/aws-single-sign-on-tutorial.md), [Nura Space](https://dashboard.nuraspace.com/login), [Broadcom DX SaaS](../saas-apps/broadcom-dx-saas-tutorial.md), [Interplay Learning](https://skilledtrades.interplaylearning.com/#login), [SendPro Enterprise](../saas-apps/sendpro-enterprise-tutorial.md), [FortiSASE SIA](../saas-apps/fortisase-sia-tutorial.md)

Die Dokumentation zu allen Anwendungen finden Sie auch hier: https://aka.ms/AppsTutorial

Informationen zum Auflisten Ihrer Anwendung im Azure AD-App-Katalog finden Sie hier: https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---march-2021"></a>Neue Bereitstellungsconnectors im Azure AD-Anwendungskatalog – März 2021

**Typ:** Neues Feature  
**Dienstkategorie:** App-Bereitstellung  
**Produktfunktion:** Integration von Drittanbieterprodukten

Sie können ab sofort das Erstellen, Aktualisieren und Löschen von Benutzerkonten für diese neu integrierten Apps automatisieren:

- [AWS Single Sign-On](../saas-apps/aws-single-sign-on-provisioning-tutorial.md)
- [Bpanda](../saas-apps/bpanda-provisioning-tutorial.md)
- [Britive](../saas-apps/britive-provisioning-tutorial.md)
- [GitHub Enterprise Managed User](../saas-apps/github-enterprise-managed-user-provisioning-tutorial.md)
- [Grammarly](../saas-apps/grammarly-provisioning-tutorial.md)
- [LogicGate](../saas-apps/logicgate-provisioning-tutorial.md)
- [SecureLogin](../saas-apps/secure-login-provisioning-tutorial.md)
- [TravelPerk](../saas-apps/travelperk-provisioning-tutorial.md)

Weitere Informationen dazu, wie Sie Ihre Organisation durch die automatisierte Bereitstellung von Benutzerkonten besser schützen können, finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
 
---

### <a name="introducing-ms-graph-api-for-company-branding"></a>Einführung der MS Graph-API für Unternehmensbranding

**Typ:** Geändertes Feature  
**Dienstkategorie:** MS Graph  
**Produktfunktion:** B2B/B2C

Für die Azure AD- oder Microsoft 365-Anmeldung ist die [MS Graph-API für das Unternehmensbranding](/graph/api/resources/organizationalbrandingproperties), damit die Brandingparameter programmgesteuert verwaltet werden können.

---

### <a name="general-availability---header-based-authentication-sso-with-application-proxy"></a>Allgemeine Verfügbarkeit: SSO mit headerbasierter Authentifizierung mit Anwendungsproxy

**Typ:** Geändertes Feature  
**Dienstkategorie:** Anwendungsproxy  
**Produktfunktion:** Zugriffssteuerung
 
Die native Unterstützung des Azure AD-Anwendungsproxys für die headerbasierte Authentifizierung ist jetzt allgemein verfügbar. Mit diesem Feature können Sie die Benutzerattribute, die als HTTP-Header für die Anwendung erforderlich sind, ohne zusätzliche Komponenten zu konfigurieren, die bereitgestellt werden müssen. [Weitere Informationen](../app-proxy/application-proxy-configure-single-sign-on-with-headers.md)

---

### <a name="two-way-sms-for-mfa-server-is-no-longer-supported"></a>Bidirektionale SMS für MFA-Server wird nicht mehr unterstützt

**Typ**: Veraltet  
**Dienstkategorie:** MFA  
**Produktfunktion:** Identitätssicherheit und -schutz
 

Bidirektionale SMS für MFA-Server wurde ursprünglich 2018 als veraltet markiert, und wird nach dem 24. Februar 2021 nicht mehr unterstützt. Administratoren sollten eine andere Methode für Benutzer aktivieren, die immer noch die bidirektionale SMS verwenden.

E-Mail-Benachrichtigungen und Service Health-Benachrichtigungen des Azure-Portals wurden am 8. Dezember 2020 und 28. Januar 2021 an betroffene Administratoren gesendet. Die Warnungen wurden an die RBAC-Rollen „Besitzer“, „Mitbesitzer“, „Administrator“ und „Dienstadministrator“ gesendet. [Weitere Informationen](../authentication/how-to-authentication-two-way-sms-unsupported.md)
 
---
 
## <a name="february-2021"></a>Februar 2021

### <a name="email-one-time-passcode-authentication-on-by-default-starting-october-2021"></a>Authentifizierung mit Einmalkennung per E-Mail standardmäßig aktiviert ab Oktober 2021

**Typ:** Plan für Änderung  
**Dienstkategorie:** B2B  
**Produktfunktion:** B2B/B2C
 

Ab dem 31. Oktober 2021 wird die [Authentifizierung mit Einmalkennung per E-Mail](../external-identities/one-time-passcode.md) von Microsoft Azure Active Directory zur Standardmethode für Konto- und Mandanteneinladungen bei B2B-Kollaborationsszenarien. Microsoft lässt dann keine Einlösungen von Einladungen über nicht verwaltete Azure Active Directory-Konten mehr zu. 

---

### <a name="unrequested-but-consented-permissions-will-no-longer-be-added-to-tokens-if-they-would-trigger-conditional-access"></a>Berechtigungen, die nicht angefordert wurden, aber für die eine Einwilligung erteilt wurde, werden Token nicht mehr hinzugefügt, falls hierdurch der bedingte Zugriff ausgelöst wird.

**Typ:** Plan für Änderung  
**Dienstkategorie**: Authentifizierungen (Anmeldungen)  
**Produktfunktion**: Plattform
 
Derzeit erhalten Anwendungen, für die [dynamische Berechtigungen](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent) genutzt werden, alle Berechtigungen, für die die Einwilligung für den Zugriff vorhanden ist. Hierzu gehören auch Anwendungen, die nicht angefordert wurden (auch wenn dies zur Auslösung des bedingten Zugriffs führt). Beispielsweise kann dies dazu führen, dass eine App, die nur `user.read` anfordert, während gleichzeitig die Einwilligung für `files.read` vorhanden ist, dazu gezwungen wird, den für die Berechtigung `files.read` zugewiesenen bedingten Zugriff zu übergeben. 

Um die Anzahl von unnötigen Eingabeaufforderungen in Bezug auf den bedingten Zugriff zu reduzieren, wird für Azure AD eine geänderte Vorgehensweise beim Bereitstellen von nicht angeforderten Bereichen für Anwendungen genutzt. Von Apps wird der bedingte Zugriff nur für Berechtigungen ausgelöst, die diese explizit anfordern. Weitere Informationen finden Sie unter [Neuerungen bei der Authentifizierung](../develop/reference-breaking-changes.md#conditional-access-will-only-trigger-for-explicitly-requested-scopes).
 
---
 
### <a name="public-preview----use-a-temporary-access-pass-to-register-passwordless-credentials"></a>Öffentliche Vorschau: Verwenden eines befristeten Zugriffspasses zum Registrieren von kennwortlosen Anmeldeinformationen

**Typ:** Neues Feature  
**Dienstkategorie:** MFA  
**Produktfunktion:** Identitätssicherheit und -schutz

Der befristete Zugriffspass ist ein Passcode mit zeitlicher Begrenzung. Er dient als sichere Anmeldeinformation und ermöglicht das Onboarding für kennwortlose Anmeldeinformationen und die Wiederherstellung, wenn ein Benutzer seine App mit sicherem Authentifizierungsfaktor (z. B. FIDO2-Sicherheitsschlüssel oder Microsoft Authenticator) verloren oder vergessen hat und sich anmelden muss, um neue Methoden für die sichere Authentifizierung zu registrieren. [Weitere Informationen](../authentication/howto-authentication-temporary-access-pass.md)

---

### <a name="public-preview----keep-me-signed-in-kmsi-in-next-generation-of-user-flows"></a>Öffentliche Vorschau: Option „Angemeldet bleiben“ für Benutzerflows der nächsten Generation

**Typ:** Neues Feature  
**Dienstkategorie**: B2C – Kundenidentitätsverwaltung  
**Produktfunktion:** B2B/B2C

Für die nächste Generation der B2C-Benutzerflows wird jetzt die Option [„Angemeldet bleiben“](../../active-directory-b2c/session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) unterstützt. Hierbei können Kunden die Lebensdauer der Sitzung für die Benutzer ihrer Web- und nativen Anwendungen verlängern, indem sie ein beständiges Cookie verwenden.  Bei diesem Feature bleibt die Sitzung auch dann aktiv, wenn der Benutzer den Browser schließt und dann wieder öffnet. Sie wird widerrufen, wenn sich der Benutzer abmeldet.

---

### <a name="public-preview----reset-redemption-status-for-a-guest-user"></a>Öffentliche Vorschau: Zurücksetzen des Einlösestatus für einen Gastbenutzer

**Typ:** Neues Feature  
**Dienstkategorie:** B2B  
**Produktfunktion:** B2B/B2C
 
Kunden können jetzt vorhandene externe Gastbenutzer erneut einladen, um ihren Einlösestatus zurückzusetzen. Auf diese Weise kann das Konto des Gastbenutzers beibehalten werden, ohne dass der Zugriff verloren geht. [Weitere Informationen](../external-identities/reset-redemption-status.md)
 
---

### <a name="public-preview----synchronization-provisioning-apis-now-support-application-permissions"></a>Öffentliche Vorschau: Für APIs für die Synchronisierung (Bereitstellung) werden jetzt Anwendungsberechtigungen unterstützt

**Typ:** Neues Feature  
**Dienstkategorie:** App-Bereitstellung  
**Produktfunktion:** Identity Lifecycle Management
 
Kunden können jetzt „application.readwrite.ownedby“ als Anwendungsberechtigung zum Aufrufen der Synchronisierungs-APIs verwenden. Beachten Sie, dass dies nur für die Bereitstellung aus Azure AD in Drittanbieteranwendungen (z. B. AWS, Databricks usw.) unterstützt wird. Für die HR-Bereitstellung (Workday/SuccessFactors) oder die Cloudsynchronisierung (AD zu Azure AD) wird dies derzeit nicht unterstützt. [Weitere Informationen](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta&preserve-view=true)
 
---

### <a name="general-availability---authentication-policy-administrator-built-in-role"></a>Allgemeine Verfügbarkeit: Integrierte Rolle „Authentifizierungsrichtlinienadministrator“

**Typ:** Neues Feature  
**Dienstkategorie:** RBAC  
**Produktfunktion:** Zugriffssteuerung
 
Benutzer mit dieser Rolle können die Authentifizierungsmethodenrichtlinie, mandantenweite MFA-Einstellungen und die Kennwortschutzrichtlinie konfigurieren. Diese Rolle gewährt die Berechtigung zum Verwalten von Kennwortschutzeinstellungen: Smart Lockout-Konfigurationen und Aktualisieren der Liste der benutzerdefinierten gesperrten Kennwörter. [Weitere Informationen](../roles/permissions-reference.md#authentication-policy-administrator)

---

### <a name="general-availability---user-collections-on-my-apps-are-available-now"></a>Allgemeine Verfügbarkeit: In „Meine Apps“ sind jetzt Benutzersammlungen verfügbar!

**Typ:** Neues Feature  
**Dienstkategorie:** Meine Apps  
**Produktfunktion:** Endbenutzerumgebungen
 
Benutzer können im App-Startprogramm von „Meine Apps“ jetzt ihre eigenen Gruppierungen für Apps erstellen. Darüber hinaus können sie Sammlungen, die vom Administrator freigegeben werden, auch neu sortieren und ausblenden. [Weitere Informationen](../user-help/my-apps-portal-user-collections.md)

---

### <a name="general-availability---autofill-in-authenticator"></a>Allgemeine Verfügbarkeit: Automatisches Ausfüllen in Authenticator

**Typ:** Neues Feature  
**Dienstkategorie:** Microsoft Authenticator-App  
**Produktfunktion:** Identitätssicherheit und -schutz
 
Microsoft Authenticator unterstützt die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) und bietet Kontoverwaltungsfunktionen. Zusätzlich wird jetzt auch das automatische Ausfüllen von Kennwortfeldern auf Websites und in Apps unterstützt, auf die Benutzer mit ihren mobilen Geräten (iOS und Android) zugreifen. 

Für die Nutzung der Funktion zum automatischen Ausfüllen in Authenticator müssen Benutzer ihr persönliches Microsoft-Konto in Authenticator hinzufügen und für die Synchronisierung mit ihren Kennwörtern verwenden. Geschäfts-, Schul- oder Unikonten können derzeit nicht verwendet werden, um Kennwörter zu synchronisieren. [Weitere Informationen](../user-help/user-help-auth-app-faq.md#autofill-for-it-admins)

---

### <a name="general-availability---invite-internal-users-to-b2b-collaboration"></a>Allgemeine Verfügbarkeit: Einladen von internen Benutzern zur B2B-Kollaboration

**Typ:** Neues Feature  
**Dienstkategorie:** B2B  
**Produktfunktion:** B2B/B2C
 
Kunden können jetzt interne Gäste zur Nutzung der B2B-Kollaboration einladen, anstatt eine Einladung an ein vorhandenes internes Konto zu senden. Dies ermöglicht es Kunden, für den Benutzer die Objekt-ID, den UPN, die Gruppenmitgliedschaften und die App-Zuweisungen beizubehalten. [Weitere Informationen](../external-identities/invite-internal-users.md)

---

### <a name="general-availability---domain-name-administrator-built-in-role"></a>Allgemeine Verfügbarkeit: Integrierte Rolle „Domänennamenadministrator“

**Typ:** Neues Feature  
**Dienstkategorie:** RBAC  
**Produktfunktion:** Zugriffssteuerung
 
Benutzer mit dieser Rolle können Domänennamen verwalten (lesen, hinzufügen, überprüfen, aktualisieren und löschen). Darüber hinaus können sie auch Verzeichnisinformationen zu Benutzern, Gruppen und Anwendungen lesen, da diese Objekte über Domänenabhängigkeiten verfügen. 

Bei lokalen Umgebungen können Benutzer mit dieser Rolle Domänennamen für den Verbund konfigurieren, sodass zugeordnete Benutzer immer lokal authentifiziert werden. Diese Benutzer können sich dann über einmaliges Anmelden (Single Sign-On, SSO) bei Azure AD-basierten Diensten mit ihren lokalen Kennwörtern anmelden. Verbundeinstellungen müssen über Azure AD Connect synchronisiert werden, damit Benutzer auch über Berechtigungen zum Verwalten von Azure AD Connect verfügen. [Weitere Informationen](../roles/permissions-reference.md#domain-name-administrator)
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---february-2021"></a>Neue Verbund-Apps im Azure AD-Anwendungskatalog verfügbar: Februar 2021

**Typ:** Neues Feature  
**Dienstkategorie**: Unternehmens-Apps  
**Produktfunktion:** Integration von Drittanbieterprodukten
 
Im Februar 2021 haben wir die folgenden 37 neuen Anwendungen mit Verbundunterstützung in unseren App-Katalog aufgenommen:

[Loop Messenger-Erweiterung](https://loopworks.com/loop-flow-messenger/), [Silverfort-Azure AD-Adapter](http://www.silverfort.com/), [Interplay Learning](https://skilledtrades.interplaylearning.com/#login), [Nura Space](https://dashboard.nuraspace.com/login), [Yooz EU](https://eu1.getyooz.com/?kc_idp_hint=microsoft), [UXPressia](https://uxpressia.com/users/sign-in), [introDus-Plattform für Vorbereitung und Onboarding](http://app.introdus.dk/login), [Happybot](https://login.microsoftonline.com/organizations/oauth2/v2.0/authorize?client_id=34353e1e-dfe5-4d2f-bb09-2a5e376270c8&response_type=code&redirect_uri=https://api.happyteams.io/microsoft/integrate&response_mode=query&scope=offline_access%20User.Read%20User.Read.All), [LeaksID](https://app.leaksid.com/), [ShiftWizard](http://www.shiftwizard.com/), [PingFlow SSO](https://app.pingview.io/), [Swiftlane](https://admin.swiftlane.com/login), [Quasydoc SSO](https://www.quasydoc.eu/login), [Fenwick Gold Account](https://businesscentral.dynamics.com/), [SeamlessDesk](https://www.seamlessdesk.com/login), [Learnsoft LMS & TMS](http://www.learnsoft.com/), [P-TH+](https://p-th.jp/), [myViewBoard](https://api.myviewboard.com/auth/microsoft/), [Tartabit IoT Bridge](https://bridge-us.tartabit.com/), [AKASHI](../saas-apps/akashi-tutorial.md), [Rewatch](../saas-apps/rewatch-tutorial.md), [Zuddl](../saas-apps/zuddl-tutorial.md), [Parkalot (Parkplatzmanagement)](../saas-apps/parkalot-car-park-management-tutorial.md), [HSB ThoughtSpot](../saas-apps/hsb-thoughtspot-tutorial.md), [IBMid](../saas-apps/ibmid-tutorial.md), [SharingCloud](../saas-apps/sharingcloud-tutorial.md), [PoolParty Semantic Suite](../saas-apps/poolparty-semantic-suite-tutorial.md), [GlobeSmart](../saas-apps/globesmart-tutorial.md), [Samsung Knox & Business Services](../saas-apps/samsung-knox-and-business-services-tutorial.md), [Penji](../saas-apps/penji-tutorial.md), [Kendis Scaling Agile Platform](../saas-apps/kendis-scaling-agile-platform-tutorial.md), [Maptician](../saas-apps/maptician-tutorial.md), [Olfeo SAAS](../saas-apps/olfeo-saas-tutorial.md), [Sigma Computing](../saas-apps/sigma-computing-tutorial.md), [CloudKnox Permissions Management Platform](../saas-apps/cloudknox-permissions-management-platform-tutorial.md), [Klaxoon SAML](../saas-apps/klaxoon-saml-tutorial.md), [Enablon](../saas-apps/enablon-tutorial.md)

Die Dokumentation zu allen Anwendungen finden Sie auch hier: https://aka.ms/AppsTutorial

Informationen zum Auflisten Ihrer Anwendung im Azure AD-App-Katalog finden Sie hier: https://aka.ms/AzureADAppRequest

--- 

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2021"></a>Neue Bereitstellungsconnectors im Azure AD-Anwendungskatalog: Februar 2021

**Typ:** Neues Feature  
**Dienstkategorie:** App-Bereitstellung  
**Produktfunktion:** Integration von Drittanbieterprodukten
 

Sie können ab sofort das Erstellen, Aktualisieren und Löschen von Benutzerkonten für diese neu integrierten Apps automatisieren:

- [Atea](../saas-apps/atea-provisioning-tutorial.md)
- [Getabstract](../saas-apps/getabstract-provisioning-tutorial.md)
- [HelloID](../saas-apps/helloid-provisioning-tutorial.md)
- [Hoxhunt](../saas-apps/hoxhunt-provisioning-tutorial.md)
- [Iris Intranet](../saas-apps/iris-intranet-provisioning-tutorial.md)
- [Preciate](../saas-apps/preciate-provisioning-tutorial.md)

Weitere Informationen finden Sie unter [Automatisieren der Bereitstellung von Benutzern für SaaS-Anwendungen mit Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="general-availability---10-azure-active-directory-roles-now-renamed"></a>Allgemeine Verfügbarkeit: 10 Azure Active Directory-Rollen wurden umbenannt

**Typ:** Geänderte Funktion  
**Dienstkategorie:** RBAC  
**Produktfunktion:** Zugriffssteuerung
 
Zehn integrierte Azure AD-Rollen wurden umbenannt, um diese für [Microsoft 365 Admin Center](/microsoft-365/admin/microsoft-365-admin-center-preview), das [Azure AD-Portal](https://portal.azure.com/) und [Microsoft Graph](https://developer.microsoft.com/graph/) zu vereinheitlichen. Weitere Informationen zu den neuen Rollen finden Sie unter [Berechtigungen der Administratorrolle in Azure Active Directory](../roles/permissions-reference.md#all-roles).

![Tabelle, in der die Rollennamen in der MS Graph-API und im Azure-Portal sowie der vorgeschlagene endgültige Name über API, Azure-Portal und Mac hinweg angezeigt werden.](media/whats-new/roles-table-rbac.png)

---

### <a name="new-company-branding-in-mfasspr-combined-registration"></a>Neues Unternehmensbranding für kombinierte MFA/SSPR-Registrierung

**Typ:** Geändertes Feature  
**Dienstkategorie:** Benutzeroberfläche und Benutzerverwaltung  
**Produktfunktion:** Endbenutzerumgebungen
 
In der Vergangenheit wurden auf den Anmeldeseiten von Azure Active Directory keine Unternehmenslogos verwendet. Das Unternehmensbranding ist jetzt oben links von der kombinierten MFA/SSPR-Registrierung angeordnet. Darüber hinaus ist das Unternehmensbranding auch auf den Seiten „Meine Anmeldungen“ und „Sicherheitsinformation“ enthalten. [Weitere Informationen](../fundamentals/customize-branding.md)

---

### <a name="general-availability---second-level-manager-can-be-set-as-alternate-approver"></a>Allgemeine Verfügbarkeit: Mitglied der zweiten Führungsebene kann als alternative genehmigende Person festgelegt werden

**Typ:** Geändertes Feature  
**Dienstkategorie:** Benutzerzugriffsverwaltung  
**Produktfunktion:** Berechtigungsverwaltung
 
In der Berechtigungsverwaltung steht jetzt bei der Auswahl von genehmigenden Personen eine zusätzliche Option zur Verfügung. Wenn Sie als erste genehmigende Person „Vorgesetzter als genehmigende Person“ auswählen, steht im Feld „Alternative genehmigende Person“ zusätzlich die Option „Mitglied der zweiten Führungsebene als alternative genehmigende Person“ zur Verfügung. Wenn Sie diese Option auswählen, müssen Sie eine stellvertretende genehmigende Person hinzufügen, an die die Anforderung weitergeleitet wird, wenn das System das Mitglied der zweiten Führungsebene nicht finden kann. [Weitere Informationen](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)
 
---

### <a name="authentication-methods-activity-dashboard"></a>Dashboard: Aktivität für Authentifizierungsmethoden

**Typ:** Geändertes Feature  
**Dienstkategorie:** Berichterstellung  
**Produktfunktion:** Überwachung und Berichterstellung
 

Im aktualisierten Dashboard zu den Aktivitäten für Authentifizierungsmethoden erhalten Administratoren eine Übersicht über die Registrierung von Authentifizierungsmethoden und die Nutzungsaktivitäten auf ihrem Mandanten. Im Bericht ist die Anzahl von Benutzern zusammengefasst, die für die einzelnen Methoden registriert sind, und es ist auch angegeben, welche Methoden beim Anmelden und beim Zurücksetzen des Kennworts verwendet werden. [Weitere Informationen](../authentication/howto-authentication-methods-activity.md)
 
---

### <a name="refresh-and-session-token-lifetimes-configurability-in-configurable-token-lifetime-ctl-are-retired"></a>Aktualisierung und Konfigurierbarkeit der Lebensdauer von Sitzungstoken (Konfigurierbare Tokengültigkeitsdauer) ausgesondert

**Typ:** Als veraltet markiert  
**Dienstkategorie:** Sonstige  
**Produktfunktion:** Benutzerauthentifizierung
 
Die Funktionen für die Aktualisierung und die Konfigurierbarkeit der Lebensdauer von Sitzungstoken (Konfigurierbare Tokengültigkeitsdauer) wurden ausgesondert. Azure Active Directory berücksichtigt die Konfigurationen von Aktualisierungs- und Sitzungstoken in vorhandenen Richtlinien nicht mehr. [Weitere Informationen](../develop/active-directory-configurable-token-lifetimes.md#token-lifetime-policies-for-refresh-tokens-and-session-tokens)
 
---