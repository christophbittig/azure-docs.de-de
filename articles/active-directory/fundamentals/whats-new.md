---
title: Neuigkeiten Versionshinweise – Azure Active Directory | Microsoft-Dokumentation
description: Hier finden Sie Informationen zu den Neuerungen in Azure Active Directory, z. B. aktuelle Versionshinweise, bekannte Probleme, Fehlerbehebungen, veraltete Funktionen und anstehende Änderungen.
author: ajburnle
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 10/31/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee25c39514146ddf9e7489868c24b656211115a6
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398419"
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
## <a name="october-2021"></a>Oktober 2021
 
### <a name="limits-on-the-number-of-configured-api-permissions-for-an-application-registration-will-be-enforced-starting-in-october-2021"></a>Ab Oktober 2021 werden Grenzwerte für die Anzahl von konfigurierten API-Berechtigungen für eine Anwendungsregistrierung erzwungen

**Typ:** Plan für Änderung  
**Dienstkategorie:** Sonstige  
**Produktfunktion:** Entwickleroberfläche
 
Anwendungsentwickler*innen konfigurieren ihre Apps manchmal so, dass sie mehr Berechtigungen benötigen als gewährt werden können. Damit dies verhindert wird, erzwingen wir einen Grenzwert für die Gesamtzahl der erforderlichen Berechtigungen, die für eine App-Registrierung konfiguriert werden können.

Die Gesamtzahl der erforderlichen Berechtigungen für eine einzelne Anwendungsregistrierung darf 400 Berechtigungen (für alle APIs) nicht überschreiten. Die Änderung zum Erzwingen dieses Grenzwerts wird ab Mitte Oktober 2021 eingeführt. Bei Anwendungen, die den Grenzwert überschreiten, kann die Anzahl von Berechtigungen nicht erhöht werden, für die sie konfiguriert sind. Der vorhandene Grenzwert für die Anzahl von unterschiedlichen APIs, für die Berechtigungen erforderlich sind, bleibt unverändert und darf 50 APIs nicht überschreiten.

Im Azure-Portal sind die erforderlichen Berechtigungen unter API-Berechtigungen für die Anwendung aufgeführt, die Sie konfigurieren möchten. Bei Verwendung von Microsoft Graph oder Microsoft Graph PowerShell sind die erforderlichen Berechtigungen in der Eigenschaft „requiredResourceAccess“ einer [Anwendungs](/graph/api/resources/application?view=graph-rest-1.0)entität aufgeführt. [Weitere Informationen](../enterprise-users/directory-service-limits-restrictions.md)
 
---

### <a name="email-one-time-passcode-on-by-default-change-beginning-rollout-in-november-2021"></a>Standardmäßiger E-Mail-Passcode ab Rollout im November 2021

**Typ:** Plan für Änderung  
**Dienstkategorie:** B2B  
**Produktfunktion:** B2B/B2C
 
Wir hatten zuvor angekündigt, dass ab dem 31. Oktober 2021 die [Authentifizierung mit Einmalkennung per E-Mail](../external-identities/one-time-passcode.md) von Microsoft Azure Active Directory zur Standardmethode für Konto- und Mandanteneinladungen bei B2B-Kollaborationsszenarien wird. Aufgrund von Bereitstellungszeitplänen beginnen wir jedoch am 1. November 2021 mit der Einführung. Um Unterbrechungen während Feiertagen und Bereitstellungssperren zu minimieren, findet das Rollout der Änderungen für die meisten Mandanten im Januar 2022 statt. Microsoft lässt nach dieser Änderung keine Einlösungen von Einladungen über nicht verwaltete Azure Active Directory-Konten mehr zu. [Weitere Informationen](../external-identities/one-time-passcode.md#frequently-asked-questions)
 
---

### <a name="conditional-access-guest-access-blocking-screen"></a>Sperrbildschirm des Gastzugriffs für bedingten Zugriff

**Typ:** Korrigiert  
**Dienstkategorie**: Bedingter Zugriff  
**Produktfunktion:** Endbenutzerumgebungen
 
Wenn zwischen einem Home- und einem Ressourcenmandanten keine Vertrauensstellung besteht, wäre ein Gastbenutzer zuvor aufgefordert worden, sein Gerät erneut zu registrieren, was die vorherige Registrierung unterbrechen würde. Der Benutzer würde sich jedoch letztendlich in einer Registrierungsschleife befinden, da nur die Geräteregistrierung des Home-Mandanten unterstützt wird. In diesem speziellen Szenario haben wir anstelle dieser Schleife eine neue Seite zum Blockieren des bedingten Zugriffs erstellt. Die Seite teilt dem Endbenutzer mit, dass er als Gastbenutzer keinen Zugriff auf geschützte Ressourcen mit bedingtem Zugriff erhalten kann. [Weitere Informationen](https://docs.microsoft.com/azure/active-directory/external-identities/b2b-quickstart-add-guest-users-portal#prerequisites)
 
---

### <a name="50105-errors-will-now-result-in-a-ux-error-message-instead-of-an-error-response-to-the-application"></a>50105-Fehler führen jetzt zu einer UX-Fehlermeldung anstelle einer Fehlerantwort an die Anwendung.

**Typ:** Korrigiert  
**Dienstkategorie**: Authentifizierungen (Anmeldungen)  
**Produktfunktion**: Entwickleroberfläche
 
Azure AD hat einen Fehler in einer Fehlerantwort behoben, der auftritt, wenn ein Benutzer einer App nicht zugewiesen ist, die eine Benutzerzuweisung erfordert. Zuvor hatte Azure AD den Fehler 50105 mit dem OIDC-Fehlercode "interaction_required" auch während der interaktiven Authentifizierung angezeigt. Dies würde dazu führen, dass gut codierte Anwendungen unbegrenzt eine Schleife durchführen, da sie die interaktive Authentifizierung durchführen und einen Fehler erhalten, der sie anweist, die interaktive Authentifizierung durchzuführen, was sie dann tun würden.  

Der Fehler wurde behoben, sodass während der nicht interaktiven Authentifizierung weiterhin ein "interaction_required"-Fehler zurückgegeben wird. Während der interaktiven Authentifizierung wird dem Benutzer außerdem direkt eine Fehlerseite angezeigt.  

Ausführlichere Informationen finden Sie in den Änderungshinweisen für [Azure AD Protokolle](../develop/reference-breaking-changes.md#error-50105-has-been-fixed-to-not-return-interaction_required-during-interactive-authentication). 

---

### <a name="public-preview---new-claims-transformation-capabilities"></a>Public Preview: Neue Funktionen für die Anspruchstransformation

**Typ:** Neues Feature  
**Dienstkategorie:** Unternehmens-Apps  
**Produktfunktion:** SSO
 
Die folgenden neuen Funktionen wurden den Anspruchstransformationen hinzugefügt, die zum Bearbeiten von Ansprüchen in Token verfügbar sind, die von Azure AD ausgestellt wurden:
 
- Join() für NameID. War auf das Verknüpfen einer E-Mail-Formatadresse mit einer überprüften Domäne beschränkt. Jetzt kann Join() für den NameID-Anspruch auf die gleiche Weise wie jeder andere Anspruch verwendet werden, sodass NameID-Transformationen verwendet werden können, um Windows Kontostil-NameIDs oder eine andere Zeichenfolge zu erstellen. Wenn das Ergebnis vorerst eine E-Mail-Adresse ist, überprüft der Azure AD weiterhin, ob es sich bei der Domäne um eine Domäne handelt, die im Mandanten überprüft wird.
- Substring(). Eine neue Transformation in der Benutzeroberfläche der Anspruchskonfiguration ermöglicht die Extraktion von definierten Positionsteilzeichenfolgen, z. B. fünf Zeichen, beginnend mit Zeichen 3 – Substring(3,5)
- Transformationen von Ansprüchen. Diese Transformationen können jetzt für mehrwertige Attribute ausgeführt werden und mehrwertige Ansprüche ausgeben. Microsoft Graph kann jetzt verwendet werden, um mehrwertige Verzeichnisschema-Erweiterungsattribute zu lesen/zu schreiben. [Weitere Informationen](../develop/active-directory-saml-claims-customization.md)

---

### <a name="public-preview--flagged-sign-ins"></a>Public Preview – gekennzeichnete Anmeldungen  

**Typ:** Neues Feature  
**Dienstkategorie**: Berichterstellung  
**Produktfunktion:** Überwachung und Berichterstellung
 
Gekennzeichnete Anmeldedaten sind ein Feature, das das Signal-zu-Rausch-Verhältnis für Benutzeranmeldungen erhöht, bei denen Benutzer Hilfe benötigen. Die Funktionalität soll es Benutzern ermöglichen, das Bewusstsein für Anmeldefehler zu erhöhen, bei denen sie Hilfe benötigen. Außerdem können mithilfe dieser Funktion Administratoren und Helpdeskmitarbeiter schnell und effizient die richtigen Anmeldeereignisse finden. [Weitere Informationen](../reports-monitoring/overview-flagged-sign-ins.md)

---

### <a name="public-preview---device-overview"></a>Public Preview – Geräteübersicht

**Typ:** Neues Feature  
**Dienstkategorie:** Geräteregistrierung und -verwaltung  
**Produktfunktion:** Lebenszyklusverwaltung für Geräte
 
Die neue Funktion Geräteübersicht bietet umsetzbare Erkenntnisse zu Geräten in Ihrem Mandanten. [Weitere Informationen](https://docs.microsoft.com/azure/active-directory/devices/device-management-azure-portal)
 
---

### <a name="public-preview---azure-active-directory-workload-identity-federation"></a>Public Preview: Azure Active Directory-Workload-Identitätsverbund

**Typ:** Neues Feature  
**Dienstkategorie:** Unternehmens-Apps  
**Produktfunktion:** Entwickleroberfläche
 
Azure AD Workload-Identitätsverbund ist eine neue Funktion, die sich in Public Preview befindet. So müssen Entwickler keine Anwendungsgeheimnisse oder Zertifikate verarbeiten. Dies schließt Geheimnisse in Szenarien ein, z. B. die Verwendung von GitHub Actions und das Erstellen von Anwendungen in Kubernetes. Anstatt ein Anwendungsgeheimnis zu erstellen und zu verwenden, um Token für diese Anwendung abzurufen, können Entwickler stattdessen Token verwenden, die von den jeweiligen Plattformen wie GitHub und Kubernetes bereitgestellt werden, ohne geheimnisse manuell verwalten zu müssen.[Weitere Informationen](../develop/workload-identity-federation.md).

---

### <a name="public-preview---updates-to-sign-in-diagnostic"></a>Public Preview: Updates der Anmeldedatendiagnose

**Typ:** Geändertes Feature  
**Dienstkategorie**: Berichterstellung  
**Produktfunktion:** Überwachung und Berichterstellung
 
Mit diesem Update deckt die Diagnose mehr Szenarien ab und wird Administratoren leichter zur Verfügung gestellt.

Neue Szenarien, die bei Verwendung der Anmeldedatendiagnose behandelt werden:
- Fehler bei der Anmeldung per Passthrough-Authentifizierung
- Nahtloses Single-Sign On: Anmeldefehler
 
Zu den anderen Änderungen gehören:
- Gekennzeichnete Anmeldedaten werden automatisch zur Untersuchung angezeigt, wenn die Anmeldedatendiagnose von Diagnose und Problembehandlung verwendet wird.
- Die Anmeldedatendiagnose ist jetzt auf dem Blatt Enterprise Apps Diagnose und Problembehandlung verfügbar.
- Die Anmeldedatendiagnose ist jetzt auf der Registerkarte Grundlegende Informationen der Ereignisansicht des Anmeldeprotokolls für alle Anmeldeereignisse verfügbar. [Weitere Informationen](../reports-monitoring/concept-sign-in-diagnostics-scenarios.md#supported-scenarios)

---

### <a name="general-availability---privileged-role-administrators-can-now-create-azure-ad-access-reviews-on-role-assignable-groups"></a>Allgemeine Verfügbarkeit: Administratoren für privilegierte Rollen können Azure AD Zugriffsüberprüfungen für Gruppen erstellen, denen Rollen zugewiesen werden können

**Typ:** Korrigiert  
**Dienstkategorie**: Zugriffsüberprüfungen  
**Produktfunktion:** Identity Governance
 
Administratoren für privilegierte Rollen können jetzt Azure AD Zugriffsüberprüfungen für Azure AD-Gruppen erstellen, denen Rollen zugewiesen werden können, sowie Azure AD-Rollen. [Weitere Informationen](../governance/deploy-access-reviews.md#who-will-create-and-manage-access-reviews)
 
---

### <a name="general-availability---azure-ad-single-sign-on-and-device-based-conditional-access-support-in-firefox-on-windows-1011"></a>Allgemeine Verfügbarkeit – Azure AD Single Sign on und gerätebasierte Unterstützung für bedingten Zugriff in Firefox unter Windows 10/11

**Typ:** Neues Feature  
**Dienstkategorie:** Authentifizierungen (Anmeldungen)  
**Produktfunktion:** SSO
 
Wir unterstützen jetzt native SSO-Unterstützung (Single Sign-On) und gerätebasierten bedingten Zugriff auf den Firefox-Browser unter Windows 10 und Windows Server 2019 ab Firefox Version 91. [Weitere Informationen](../conditional-access/require-managed-devices.md#prerequisites)
 
---

### <a name="general-availability---new-app-indicator-in-my-apps"></a>Allgemeine Verfügbarkeit: Neuer App-Indikator in Meine Apps

**Typ:** Neues Feature  
**Dienstkategorie:** Meine Apps  
**Produktfunktion:** Endbenutzerumgebungen
 
Apps, die dem Benutzer kürzlich zugewiesen wurden, werden mit dem Indikator „neu“ angezeigt. Wenn die App gestartet oder die Seite aktualisiert wird, wird dieser Indikator nicht mehr angezeigt. [Weitere Informationen](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)
 
---

### <a name="general-availability---custom-domain-support-in-azure-ad-b2c"></a>Allgemeine Verfügbarkeit: Unterstützung benutzerdefinierter Domänen in Azure AD B2C

**Typ:** Neues Feature  
**Dienstkategorie**: B2C – Kundenidentitätsverwaltung  
**Produktfunktion:** B2B/B2C
 
Azure AD B2C-Kunden können jetzt benutzerdefinierte Domänen aktivieren, damit ihre Endbenutzer zur Authentifizierung an eine benutzerdefinierte URL-Domäne umgeleitet werden. Dies erfolgt über die Integration in Azure Front Door-Funktionen für benutzerdefinierte Domänen. [Weitere Informationen](https://docs.microsoft.com/azure/active-directory-b2c/custom-domain?pivots=b2c-user-flow)
 
---

### <a name="general-availability---edge-administrator-built-in-role"></a>Allgemeine Verfügbarkeit: Integrierte Rolle „Edge-Administrator“

**Typ:** Neues Feature  
**Dienstkategorie:** RBAC  
**Produktfunktion**: Zugriffssteuerung
 

Benutzer mit dieser Rolle können die Unternehmenswebsiteliste erstellen und verwalten, die für den Internet Explorer-Modus in Microsoft Edge erforderlich ist. Diese Rolle gewährt Berechtigungen zum Erstellen, Bearbeiten und Veröffentlichen der Websiteliste und ermöglicht darüber hinaus den Zugriff auf die Verwaltung von Supporttickets. [Weitere Informationen](https://docs.microsoft.com/deployedge/edge-ie-mode-cloud-site-list-mgmt)
 
---

### <a name="general-availability---windows-365-administrator-built-in-role"></a>Allgemeine Verfügbarkeit: Integrierte Rolle „Windows 365-Administrator“

**Typ:** Neues Feature  
**Dienstkategorie:** RBAC  
**Produktfunktion**: Zugriffssteuerung
 
Benutzer mit dieser Rolle verfügen über globale Berechtigungen für Windows 365-Ressourcen, wenn der Dienst vorhanden ist. Darüber hinaus beinhaltet diese Rolle die Möglichkeit, Benutzer und Geräte zum Zuordnen einer Richtlinie zu verwalten sowie Gruppen zu erstellen und zu verwalten. [Weitere Informationen](../roles/permissions-reference.md)
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---october-2021"></a>Neue Verbund-Apps im Azure AD-Anwendungskatalog verfügbar – Oktober 2021

**Typ:** Neues Feature  
**Dienstkategorie:** Unternehmens-Apps  
**Produktfunktion:** Integration von Drittanbieterprodukten
 
Im Oktober 2021 haben wir die folgenden 10 neuen Anwendungen mit Verbundunterstützung in unseren App-Katalog aufgenommen:

[Adaptive Shield](../saas-apps/adaptive-shield-tutorial.md), [SocialChorus Search](https://socialchorus.com/), [Hiretual-SSO](../saas-apps/hiretual-tutorial.md), [TeamSticker by Communitio](../saas-apps/teamsticker-by-communitio-tutorial.md), [embed signage](../saas-apps/embed-signage-tutorial.md), [JoinedUp](../saas-apps/joinedup-tutorial.md), [VECOS Releezme Locker-Verwaltungssystem](../saas-apps/vecos-releezme-locker-management-system-tutorial.md), [Altoura](../saas-apps/altoura-tutorial.md), [Dagster Cloud](../saas-apps/dagster-cloud-tutorial.md), [Qualaroo](../saas-apps/qualaroo-tutorial.md)

Die Dokumentation zu allen Anwendungen finden Sie auch hier: https://aka.ms/AppsTutorial

Informationen zum Auflisten Ihrer Anwendung im Azure AD-App-Katalog finden Sie im folgenden Artikel: https://aka.ms/AzureADAppRequest

---

### <a name="continuous-access-evaluation-migration-with-conditional-access"></a>Migration der fortlaufenden Zugriffsevaluierung mit bedingtem Zugriff

**Typ:** Geändertes Feature  
**Dienstkategorie**: Bedingter Zugriff  
**Produktfunktion:** Benutzerauthentifizierung
 
Für unsere CAE-Mandanten ist eine neue Benutzeroberfläche verfügbar. Mandanten greifen jetzt im Rahmen des bedingten Zugriffs auf CAE zu. Alle Mandanten, die zuvor CAE für einige (aber nicht alle) Benutzerkonten unter der alten Benutzeroberfläche verwendet oder zuvor die alte CAE-Benutzeroberfläche deaktiviert hatten, müssen jetzt eine einmalige Migration durchlaufen.[Weitere Informationen](../conditional-access/concept-continuous-access-evaluation.md#migration).
 
---

###  <a name="improved-group-list-blade"></a>Blatt "Gruppenliste“ verbessert

**Typ:** Geändertes Feature  
**Dienstkategorie**: Gruppenverwaltung  
**Produktfunktion**: Verzeichnis
 
Das neue Blatt „Gruppenliste“ bietet mehr Sortier- und Filterfunktionen, unendliches Scrollen und eine bessere Leistung. [Weitere Informationen](../enterprise-users/groups-members-owners-search.md)
 
---

### <a name="general-availability---google-deprecation-of-gmail-sign-in-support-on-embedded-webviews-on-september-30-2021"></a>Allgemeine Verfügbarkeit: Google hat die Unterstützung für die Gmail-Anmeldung für eingebettete Webansichten am 30. September 2021 als veraltet angezeigt.

**Typ:** Geändertes Feature  
**Dienstkategorie:** B2B  
**Produktfunktion:** B2B/B2C
 
Google hat Die Gmail-Anmeldedaten bei mobilen und benutzerdefinierten Microsoft Teams-Apps, die Gmail-Authentifizierungen auf eingebetteten Webansichten ausführen, am 30. September 2021 als veraltet angezeigt.

Wenn Sie eine Verlängerung anfordern möchten, sollten Kunden mit betroffenen OAuth-Client-IDs eine E-Mail von Google Developers mit den folgenden Informationen zu einer einmaligen Verlängerung der Richtlinienerzwingung erhalten haben, die bis zum 31. Januar 2022 abgeschlossen sein muss:

Damit sich Ihre Gmail-Benutzer weiterhin anmelden und einlösen können, wird dringend empfohlen, in der MSAL.NET-Dokumentation auf  [Eingebettete und Systemweb](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui)-Benutzeroberfläche zu verweisen und Ihre Apps so zu ändern, dass sie den Systembrowser für die Anmeldung verwenden. Alle MSAL-SDKs verwenden standardmäßig die Systemwebansicht. 

Als Problemumgehung stellen wir den Flow für die Geräteanmeldung bis zum 8. Oktober zur Anwendung. Von heute bis dahin ist es wahrscheinlich, dass er möglicherweise noch nicht in allen Regionen bereitgestellt wird (in diesem Fall wird Endbenutzern ein Fehlerbildschirm angezeigt, bis er in Ihrer Region bereitgestellt wird). 

Weitere Informationen zum Ablauf der Geräteanmeldung und Details zum Anfordern einer Erweiterung für Google finden Sie unter [Hinzufügen von Google als Identitätsanbieter für B2B-Gastbenutzer](../external-identities/google-federation.md#deprecation-of-web-view-sign-in-support).
 
---

### <a name="identity-governance-administrator-can-create-and-manage-azure-ad-access-reviews-of-groups-and-applications"></a>Identity Governance-Administrator kann Azure AD-Zugriffsüberprüfungen für Gruppen und Anwendungen erstellen und verwalten

**Typ:** Geändertes Feature  
**Dienstkategorie**: Zugriffsüberprüfungen  
**Produktfunktion:** Identity Governance
 
Der Identity Governance-Administrator kann Azure AD-Zugriffsüberprüfungen für Gruppen und Anwendungen erstellen und verwalten. [Weitere Informationen](../governance/deploy-access-reviews.md#who-will-create-and-manage-access-reviews)
 
---

## <a name="september-2021"></a>September 2021

### <a name="limits-on-the-number-of-configured-api-permissions-for-an-application-registration-will-be-enforced-starting-in-october-2021"></a>Ab Oktober 2021 werden Grenzwerte für die Anzahl von konfigurierten API-Berechtigungen für eine Anwendungsregistrierung erzwungen

**Typ:** Plan für Änderung  
**Dienstkategorie:** Sonstige  
**Produktfunktion:** Entwickleroberfläche
 
Anwendungsentwickler*innen konfigurieren ihre Apps mitunter so, dass sie mehr Berechtigungen benötigen als gewährt werden können. Damit dies verhindert wird, erzwingen wir einen Grenzwert für die Gesamtzahl der erforderlichen Berechtigungen, die für eine App-Registrierung konfiguriert werden können.

Die Gesamtzahl der erforderlichen Berechtigungen für eine einzelne Anwendungsregistrierung darf 400 Berechtigungen (für alle APIs) nicht überschreiten. Die Änderung zum Erzwingen dieses Grenzwerts wird ab Mitte Oktober 2021 eingeführt. Bei Anwendungen, die den Grenzwert überschreiten, kann die Anzahl von Berechtigungen nicht erhöht werden, für die sie konfiguriert sind. Der vorhandene Grenzwert für die Anzahl von unterschiedlichen APIs, für die Berechtigungen erforderlich sind, bleibt unverändert und darf 50 APIs nicht überschreiten.

Die erforderlichen Berechtigungen sind im Azure-Portal unter „Azure Active Directory“ > „Anwendungsregistrierungen“ > (Anwendung auswählen) > „API-Berechtigungen“ aufgelistet. Bei Verwendung von Microsoft Graph oder Microsoft Graph PowerShell sind die erforderlichen Berechtigungen in der Eigenschaft „requiredResourceAccess“ einer Anwendungsentität aufgeführt. [Weitere Informationen](../enterprise-users/directory-service-limits-restrictions.md).

---

###  <a name="my-apps-performance-improvements"></a>Leistungsverbesserungen bei „Meine Apps“

**Typ:** Korrigiert  
**Dienstkategorie:** Meine Apps  
**Produktfunktion:** Endbenutzerumgebungen
 
Die Ladezeit von „Meine Apps“ wurde verbessert. Für Benutzer*innen, die myapps.microsoft.com aufrufen, wird „Meine Apps“ direkt geladen. Sie werden nicht mehr über einen anderen Dienst umgeleitet. [Weitere Informationen](../user-help/my-apps-portal-end-user-access.md).

---

### <a name="single-page-apps-using-the-spa-redirect-uri-type-must-use-a-cors-enabled-browser-for-auth"></a>Single-Page-Apps, die den Umleitungs-URI-Typ `spa` verwenden, müssen einen CORS-fähigen Browser für die Authentifizierung verwenden

**Typ:** Bekanntes Problem  
**Dienstkategorie**: Authentifizierungen (Anmeldungen)  
**Produktfunktion**: Entwickleroberfläche
 
Der moderne Edge-Browser ist jetzt in der Anforderung enthalten, dass beim Einlösen eines [Autorisierungscodes für eine Single-Page-App](../develop/v2-oauth2-auth-code-flow.md#redirect-uri-setup-required-for-single-page-apps) ein `Origin`-Header angegeben wird. Durch einen Kompatibilitätsfix wurde der moderne Edge-Browser versehentlich von CORS-Steuerelementen ausgenommen. Dieses Problem wird im Oktober behoben. Bei einigen Anwendungen musste CORS im Browser deaktiviert sein, wodurch der `Origin`-Header im Datenverkehr entfernt wurde. Diese Konfiguration wird bei Verwendung von Azure AD nicht unterstützt. Als Problemumgehung für die Sicherheit kann der moderne Edge-Browser bei diesen Apps, bei denen CORS deaktiviert werden muss, nicht mehr verwendet werden.  Alle modernen Browser müssen den `Origin`-Header jetzt gemäß HTTP-Spezifikation enthalten, damit CORS erzwungen wird. [Weitere Informationen](../develop/reference-breaking-changes.md#the-device-code-flow-ux-will-now-include-an-app-confirmation-prompt). 

---

### <a name="general-availability---on-the-my-apps-portal-users-can-choose-to-view-their-apps-in-a-list"></a>Allgemeine Verfügbarkeit: Benutzer*innen können im „Meine Apps“-Portal eine Liste ihrer Apps anzeigen

**Typ:** Neues Feature  
**Dienstkategorie:** Meine Apps  
**Produktfunktion:** Endbenutzerumgebungen
 
Standardmäßig werden die Apps in „Meine Apps“ in einer Rasteransicht angezeigt. Benutzer*innen können jetzt in „Meine Apps“ zu einer Listenansicht wechseln. [Weitere Informationen](../user-help/my-apps-portal-end-user-access.md).
 
---

### <a name="general-availability---new-and-enhanced-device-related-audit-logs"></a>Allgemeine Verfügbarkeit: neue und erweiterte gerätebezogene Überwachungsprotokolle

**Typ:** Neues Feature  
**Dienstkategorie:** Audit  
**Produktfunktion:** Lebenszyklusverwaltung für Geräte
 
Administrator*innen stehen jetzt verschiedene neue und verbesserte gerätebezogene Überwachungsprotokolle zur Verfügung. Die neuen Überwachungsprotokolle umfassen das Erstellen und Löschen von kennwortlosen Anmeldeinformationen (Anmeldung per Telefon, FIDO2-Schlüssel und Windows Hello for Business), das Registrieren/Aufheben der Registrierung von Geräten sowie das Voraberstellen/Löschen von vorab erstellten Geräten. Darüber hinaus wurden kleinere Verbesserungen an vorhandenen gerätebezogenen Überwachungsprotokollen vorgenommen (z. B. das Hinzufügen weiterer Gerätedetails). [Weitere Informationen](../reports-monitoring/concept-audit-logs.md).

---

### <a name="general-availability---azure-ad-users-can-now-view-and-report-suspicious-sign-ins-and-manage-their-accounts-within-microsoft-authenticator"></a>Allgemeine Verfügbarkeit: Azure AD-Benutzer*innen können nun verdächtige Anmeldungen anzeigen und melden und ihre Konten in Microsoft Authenticator verwalten

**Typ:** Neues Feature  
**Dienstkategorie:** Microsoft Authenticator-App  
**Produktfunktion:** Identitätssicherheit und -schutz
 
Mit diesem Feature können Azure AD-Benutzer*innen ihre Geschäfts-, Schul- oder Unikonten innerhalb der Microsoft Authenticator-App verwalten. Mit den Verwaltungsfunktionen können Benutzer*innen den Anmeldeverlauf und die Anmeldeaktivität anzeigen. Bei Bedarf können sie basierend auf dem Anmeldeverlauf und der Anmeldeaktivität verdächtige oder unbekannte Aktivitäten melden. Zudem können Benutzer*innen die Kennwörter für ihre Azure AD-Konten ändern und die Sicherheitsinformationen der Konten aktualisieren. [Weitere Informationen](../user-help/my-account-portal-sign-ins-page.md).
 
---

### <a name="general-availability---new-ms-graph-apis-for-role-management"></a>Allgemeine Verfügbarkeit: neue MS Graph-APIs für die Rollenverwaltung

**Typ:** Neues Feature  
**Dienstkategorie:** RBAC  
**Produktfunktion:** Zugriffssteuerung
 
Neue APIs für die Rollenverwaltung für den MS Graph v1.0-Endpunkt sind allgemein verfügbar. Verwenden Sie anstelle der alten [Verzeichnisrollen](/graph/api/resources/directoryrole?view=graph-rest-1.0&preserve-view=true) nun [unifiedRoleDefinition](/graph/api/resources/unifiedroledefinition?view=graph-rest-1.0&preserve-view=true) und [unifiedRoleAssignment](/graph/api/resources/unifiedroleassignment?view=graph-rest-1.0&preserve-view=true).
 
---

### <a name="general-availability---access-packages-can-expire-after-number-of-hours"></a>Allgemeine Verfügbarkeit: Zugriffspakete können nach einigen Stunden ablaufen

**Typ:** Neues Feature  
**Dienstkategorie:** Benutzerzugriffsverwaltung  
**Produktfunktion:** Berechtigungsverwaltung

In der Berechtigungsverwaltung kann jetzt ein Zugriffspaket konfiguriert werden, das nach einigen Stunden abläuft. In der Vergangenheit konnte der Ablauf nach einigen Tagen bzw. an einem bestimmten Datum festgelegt werden. Diese Einstellungen werden weiterhin unterstützt. [Weitere Informationen](../governance/entitlement-management-access-package-create.md#lifecycle).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---september-2021"></a>Neue Bereitstellungsconnectors im Azure AD-Anwendungskatalog – September 2021

**Typ:** Neues Feature  
**Dienstkategorie:** App-Bereitstellung  
**Produktfunktion:** Integration von Drittanbieterprodukten
 
Sie können ab sofort das Erstellen, Aktualisieren und Löschen von Benutzerkonten für diese neu integrierten Apps automatisieren:

- [BLDNG APP](../saas-apps/bldng-app-provisioning-tutorial.md)
- [Cato Networks](../saas-apps/cato-networks-provisioning-tutorial.md)
- [Rouse Sales](../saas-apps/rouse-sales-provisioning-tutorial.md)
- [SchoolStream ASA](../saas-apps/schoolstream-asa-provisioning-tutorial.md)
- [Taskize Connect](../saas-apps/taskize-connect-provisioning-tutorial.md)

Weitere Informationen dazu, wie Sie Ihre Organisation durch die automatisierte Bereitstellung von Benutzerkonten besser schützen können, finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../manage-apps/user-provisioning.md).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---september-2021"></a>Neue Verbund-Apps im Azure AD-Anwendungskatalog verfügbar – September 2021

**Typ:** Neues Feature  
**Dienstkategorie:** Unternehmens-Apps  
**Produktfunktion:** Integration von Drittanbieterprodukten
 
Im September 2021 haben wir die folgenden 44 neuen Anwendungen mit Verbundunterstützung in unseren App-Katalog aufgenommen.

[Studybugs](https://studybugs.com/signin), [Yello](https://yello.co/yello-for-microsoft-teams/), [LawVu](../saas-apps/lawvu-tutorial.md), [Formate eVo Mail](https://www.document-genetics.co.uk/formate-evo-erp-output-management), [Revenue Grid](https://app.revenuegrid.com/login), [Orbit for Office 365](https://azuremarketplace.microsoft.com/marketplace/apps/aad.orbitforoffice365?tab=overview), [Upmarket](https://app.upmarket.ai/), [Alinto Protect](https://protect.alinto.net/), [Cloud Concinnity](https://cloudconcinnity.com/), [Matlantis](https://matlantis.com/), [ModelGen for Visio (MG4V)](https://crecy.com.au/model-gen/), [NetRef: Classroom Management](https://oauth.net-ref.com/microsoft/sso), [VergeSense](../saas-apps/vergesense-tutorial.md), [iAuditor](../saas-apps/iauditor-tutorial.md), [Secutraq](https://secutraq.net/login), [Active & Thriving](../saas-apps/active-and-thriving-tutorial.md), [Inova](https://login.microsoftonline.com/organizations/oauth2/v2.0/authorize?client_id=1bacdba3-7a3b-410b-8753-5cc0b8125f81&response_type=code&redirect_uri=https:%2f%2fbroker.partneringplace.com%2fpartner-companion%2f&code_challenge_method=S256&code_challenge=YZabcdefghijklmanopqrstuvwxyz0123456789._-~&scope=1bacdba3-7a3b-410b-8753-5cc0b8125f81/.default), [TerraTrue](../saas-apps/terratrue-tutorial.md), [Facebook Work Accounts](../saas-apps/facebook-work-accounts-tutorial.md), [Beyond Identity Admin Console](../saas-apps/beyond-identity-admin-console-tutorial.md), [Visult](https://app.visult.io/), [ENGAGE TAG](https://app.engagetag.com/), [Appaegis Isolation Access Cloud](../saas-apps/appaegis-isolation-access-cloud-tutorial.md), [CrowdStrike Falcon Platform](../saas-apps/crowdstrike-falcon-platform-tutorial.md), [MY Emergency Control](https://my-emergency.co.uk/app/auth/login), [AlexisHR](../saas-apps/alexishr-tutorial.md), [Teachme Biz](../saas-apps/teachme-biz-tutorial.md), [Zero Networks](../saas-apps/zero-networks-tutorial.md), [Mavim iMprove](https://improve.mavimcloud.com/), [Azumuta](https://app.azumuta.com/login?microsoft=true), [Frankli](https://beta.frankli.io/login), [Amazon Managed Grafana](../saas-apps/amazon-managed-grafana-tutorial.md), [Productive](../saas-apps/productive-tutorial.md), [Create!Webフロー](../saas-apps/createweb-tutorial.md), [Evercate](https://evercate.com/us/sign-up/), [Ezra Coaching](../saas-apps/ezra-coaching-tutorial.md), [Baldwin Safety & Compliance](../saas-apps/baldwin-safety-&-compliance-tutorial.md), [Nulab Pass (Backlog,Cacoo,Typetalk)](../saas-apps/nulab-pass-tutorial.md), [Metatask](../saas-apps/metatask-tutorial.md), [Contrast Security](../saas-apps/contrast-security-tutorial.md), [Animaker](../saas-apps/animaker-tutorial.md), [Traction Guest](../saas-apps/traction-guest-tutorial.md), [True Office Learning – LIO](../saas-apps/true-office-learning-lio-tutorial.md), [Qiita Team](../saas-apps/qiita-team-tutorial.md)

Die Dokumentation zu allen Anwendungen finden Sie auch hier: https://aka.ms/AppsTutorial

Informationen zum Auflisten Ihrer Anwendung im Azure AD-App-Katalog finden Sie hier: https://aka.ms/AzureADAppRequest

---

###  <a name="gmail-users-signing-in-on-microsoft-teams-mobile-and-desktop-clients-will-sign-in-with-device-login-flow-starting-september-30-2021"></a>Gmail-Benutzer*innen, die sich bei mobilen Microsoft Teams-Clients und Microsoft Teams-Desktopclients anmelden, melden sich ab dem 30. September 2021 über den Geräteanmeldeflow an

**Typ:** Geändertes Feature  
**Dienstkategorie:** B2B  
**Produktfunktion:** B2B/B2C
 
Ab dem 30. September 2021 muss von Azure AD B2B-Gästen und Azure AD B2C-Kund*innen, die sich mit ihren per Self-Service-Registrierung registrierten oder eingelösten Gmail-Konten anmelden, ein zusätzlicher Anmeldeschritt ausgeführt werden. Benutzer*innen werden jetzt in einem separaten Browserfenster zur Eingabe eines Codes aufgefordert, um die Anmeldung bei mobilen Microsoft Teams-Clients und Microsoft Teams-Desktopclients abzuschließen. Sofern Sie dies noch nicht getan haben, ändern Sie Ihre Apps so, dass sie den Systembrowser für die Anmeldung verwenden. Weitere Informationen finden Sie in der MSAL.NET-Dokumentation unter  [Eingebettete Webbenutzeroberfläche im Vergleich zur System-Webbenutzeroberfläche](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui). Alle MSAL-SDKs verwenden standardmäßig die Systemwebansicht. 

Da der Geräteanmeldeflow ab dem 30. September 2021 schrittweise eingeführt wird, ist er in Ihrer Region möglicherweise nicht umgehend verfügbar. Solange der Flow in Ihrer Region noch nicht verfügbar ist, sehen Endbenutzer*innen gegebenenfalls den in der Dokumentation gezeigten Fehlerbildschirm. Weitere Informationen zum Geräteanmeldeflow und zum Beantragen einer Verlängerung für Google finden Sie unter [Hinzufügen von Google als Identitätsanbieter für B2B-Gastbenutzer](../external-identities/google-federation.md#deprecation-of-web-view-sign-in-support).
 
---

### <a name="improved-conditional-access-messaging-for-non-compliant-device"></a>Verbessertes Messaging für bedingten Zugriff für nicht konforme Geräte

**Typ:** Geändertes Feature  
**Dienstkategorie**: Bedingter Zugriff  
**Produktfunktion:** Endbenutzerumgebungen
 
Der Text und das Design des Bildschirms zum Blockieren bei bedingtem Zugriff wurden aktualisiert. Dieser Bildschirm wird Benutzer*innen angezeigt, wenn ihr Gerät als nicht konform markiert ist. Benutzer*innen werden solange blockiert, bis sie die erforderlichen Maßnahmen ergreifen, um die Gerätekonformitätsrichtlinien ihres Unternehmens zu erfüllen. Darüber hinaus wurde der Flow optimiert, über den Benutzer*innen das Geräteverwaltungsportal öffnen. Diese Verbesserungen gelten für alle Betriebssystemplattformen, die vom bedingten Zugriff unterstützt werden. [Weitere Informationen](https://support.microsoft.com/account-billing/troubleshooting-the-you-can-t-get-there-from-here-error-message-479a9c42-d9d1-4e44-9e90-24bbad96c251) 

---
 
## <a name="august-2021"></a>August 2021

### <a name="new-major-version-of-aadconnect-available"></a>Eine neue Hauptversion von AADConnect ist verfügbar

**Typ:** Korrigiert  
**Dienstkategorie**: AD Connect  
**Produktfunktion:** Identity Lifecycle Management
 
Wir haben eine neue Hauptversion von Azure Active Directory Connect veröffentlicht. Diese Version enthält mehrere Aktualisierungen grundlegender Komponenten auf die neuesten Versionen und wird für alle Kunden empfohlen, die Azure AD Connect verwenden. [Weitere Informationen](../hybrid/whatis-azure-ad-connect-v2.md)
 
---

### <a name="public-preview---azure-ad-single-sign-on-and-device-based-conditional-access-support-in-firefox-on-windows-10"></a>Öffentliche Vorschau - Azure AD Single Sign on und gerätebasierte Unterstützung für bedingten Zugriff in Firefox unter Windows 10

**Typ:** Neues Feature  
**Dienstkategorie:** Authentifizierungen (Anmeldungen)  
**Produktfunktion:** SSO
 

Wir unterstützen jetzt native SSO-Unterstützung (Single Sign-On) und gerätebasierten bedingten Zugriff auf den Firefox-Browser unter Windows 10 und Windows Server 2019. Die Unterstützung ist in Firefox Version 91 verfügbar. [Weitere Informationen](../conditional-access/require-managed-devices.md#prerequisites)
 
---

### <a name="public-preview---beta-ms-graph-apis-for-azure-ad-access-reviews-returns-list-of-contacted-reviewer-names"></a>Öffentliche Vorschau - Beta MS Graph APIs für Azure AD-Zugriffsüberprüfungen liefert eine Liste der Namen der kontaktierten Überprüfer

**Typ:** Neue Funktion  
**Dienstkategorie**: Zugriffsüberprüfungen  
**Produktfunktion:** Identity Governance
 

Wir haben die Beta-Version der MS Graph-API für Azure AD-Zugriffsüberprüfungen veröffentlicht. Die API verfügt über Methoden, um neben dem Gutachtertyp auch eine Liste der Namen der kontaktierten Gutachter zurückzugeben. [Weitere Informationen](/graph/api/resources/accessreviewinstance?view=graph-rest-beta&preserve-view=true)
 
---

### <a name="general-availability---register-or-join-devices-user-action-in-conditional-access"></a>Allgemeine Verfügbarkeit - Benutzeraktion "Geräte registrieren oder beitreten" in Conditional Access

**Typ:** Neues Feature  
**Dienstkategorie**: Bedingter Zugriff  
**Produktfunktion:** Identitätssicherheit und -schutz
 

Die Benutzeraktion "Geräte anmelden oder beitreten" ist generell im bedingten Zugriff verfügbar. Mit dieser Benutzeraktion können Sie Richtlinien für die Multifaktor-Authentifizierung (MFA) für die Azure Active Directory (AD)-Geräteregistrierung steuern. Derzeit können Sie mit dieser Benutzeraktion die MFA nur als Steuerelement aktivieren, wenn Benutzer Geräte registrieren oder zu Azure AD hinzufügen. Andere Steuerelemente, die von der Azure AD-Geräteregistrierung abhängen oder darauf nicht anwendbar sind, werden mit dieser Benutzeraktion weiterhin deaktiviert. [Weitere Informationen](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions)

---

### <a name="general-availability---customers-can-scope-reviews-of-privileged-roles-to-eligible-or-permanent-assignments"></a>Allgemeine Verfügbarkeit - Kunden können Überprüfungen von privilegierten Rollen auf berechtigte oder permanente Zuweisungen ausweiten

**Typ:** Neue Funktion  
**Dienstkategorie**: Zugriffsüberprüfungen  
**Produktfunktion:** Identity Governance
 
Administratoren können jetzt Zugriffsüberprüfungen erstellen, die nur dauerhafte oder zulässige Zuweisungen zu privilegierten Azure AD- oder Azure-Ressourcenrollen enthalten. [Weitere Informationen](../privileged-identity-management/pim-create-azure-ad-roles-and-resource-roles-review.md)
 
--- 

### <a name="general-availability---assign-roles-to-azure-active-directory-ad-groups"></a>Allgemeine Verfügbarkeit - Zuweisung von Rollen zu Azure Active Directory (AD) Gruppen

**Typ:** Neues Feature  
**Dienstkategorie:** RBAC  
**Produktfunktion**: Zugriffssteuerung
 

Die Zuweisung von Rollen zu Azure AD-Gruppen ist jetzt allgemein verfügbar. Diese Funktion kann die Verwaltung von Rollenzuweisungen in Azure AD für globale Administratoren und Administratoren mit privilegierten Rollen vereinfachen. [Weitere Informationen](../roles/groups-concept.md) 
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---aug-2021"></a>Neue Federated Apps in der Azure AD Application Gallery verfügbar - Aug 2021

**Typ:** Neues Feature  
**Dienstkategorie:** Unternehmens-Apps  
**Produktfunktion:** Integration von Drittanbieterprodukten
 
Im August 2021 haben wir die folgenden 46 neuen Anwendungen in unsere App-Galerie mit Federation-Unterstützung aufgenommen:

[Siriux Customer Dashboard](https://portal.siriux.tech/login), [STRUXI](https://struxi.app/), [Autodesk Construction Cloud - Meetings](https://acc.autodesk.com/), [Eccentex AppBase für Azure](../saas-apps/eccentex-appbase-for-azure-tutorial.md), [Bookado](https://adminportal.bookado.io/), [FilingRamp](https://app.filingramp.com/login), [BenQ IAM](../saas-apps/benq-iam-tutorial.md), [Rhombus Systems](../saas-apps/rhombus-systems-tutorial.md), [CorporateExperience](../saas-apps/corporateexperience-tutorial.md), [TutorOcean](../saas-apps/tutorocean-tutorial.md), [Bookado Device](https://adminportal.bookado.io/), [HiFives-AD-SSO](https://app.hifives.in/login/azure), [Darzin](https://au.darzin.com/), [Simply Stakeholders](https://au.simplystakeholders.com/), [KACTUS HCM - Smart People](https://kactusspc.digitalware.co/), [Five9 UC Adapter für Microsoft Teams V2](https://uc.five9.net/?vendor=msteams), [Automation Center](https://automationcenter.cognizantgoc.com/portal/boot/signon), [Cirrus Identity Bridge für Azure AD](../saas-apps/cirrus-identity-bridge-for-azure-ad-tutorial.md), [ShiftWizard SAML](../saas-apps/shiftwizard-saml-tutorial.md), [Safesend Returns](https://www.safesendwebsites.com/), [Brushup](../saas-apps/brushup-tutorial.md), [directprint. io Cloud Print Administration](../saas-apps/directprint-io-cloud-print-administration-tutorial.md), [plain-x](https://app.plain-x.com/#/login),[X-point Cloud](../saas-apps/x-point-cloud-tutorial.md), [SmartHub INFER](../saas-apps/smarthub-infer-tutorial.md), [Fresh Relevance](../saas-apps/fresh-relevance-tutorial.md), [FluentPro G.A. Suite](https://gas.fluentpro.com/Account/SSOLogin?provider=Microsoft), [Clockwork Recruiting](../saas-apps/clockwork-recruiting-tutorial.md), [WalkMe SAML2. 0](../saas-apps/walkme-saml-tutorial.md), [Sideways 6](https://app.sideways6.com/account/login?ReturnUrl=/), [Kronos Workforce Dimensions](../saas-apps/kronos-workforce-dimensions-tutorial.md), [SysTrack Cloud Edition](https://cloud.lakesidesoftware.com/Cloud/Account/Login), [mailworx Dynamics CRM Connector](https://www.mailworx.info/), [Palo Alto Networks Cloud Identity Engine - Cloud Authentication Service](../saas-apps/palo-alto-networks-cloud-identity-engine---cloud-authentication-service-tutorial.md), [Peripass](https://accounts.peripass.app/v1/sso/challenge), [JobDiva](https://www.jobssos.com/index_azad.jsp?SSO=AZURE&ID=1), [Sanebox für Office365](https://sanebox.com/login), [Tulip](../saas-apps/tulip-tutorial.md), [HP Wolf Security](https://bec-pocda37b439.bromium-online.com/gui/), [Genesys Engage cloud Email](https://login.microsoftonline.com/common/oauth2/authorize?prompt=consent&accessType=offline&state=07e035a7-6fb0-4411-afd9-efa46c9602f9&resource=https://graph.microsoft.com/&response_type=code&redirect_uri=https://iwd.api01-westus2.dev.genazure.com/iwd/v3/emails/oauth2/microsoft/callback&client_id=36cd21ab-862f-47c8-abb6-79facad09dda), [Meta Wiki](https://meta.dunkel.eu/), [Palo Alto Networks Cloud Identity Engine Directory Sync](https://directory-sync.us.paloaltonetworks.com/directory?instance=L2qoLVONpBHgdJp1M5K9S08Z7NBXlpi54pW1y3DDu2gQqdwKbyUGA11EgeaDfZ1dGwn397S8eP7EwQW3uyE4XL), [Valarea](https://www.valarea.com/en/download), [LanSchool Air](../saas-apps/lanschool-air-tutorial.md), [Catalyst](https://www.catalyst.org/sso-login/), [Webcargo](../saas-apps/webcargo-tutorial.md)

Die Dokumentation zu allen Anwendungen finden Sie auch hier: https://aka.ms/AppsTutorial

Informationen zum Auflisten Ihrer Anwendung im Azure AD-App-Katalog finden Sie hier: https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---august-2021"></a>Neue Konnektoren für die Bereitstellung in der Azure AD Application Gallery - August 2021

**Typ:** Neues Feature  
**Dienstkategorie:** App-Bereitstellung  
**Produktfunktion:** Integration von Drittanbieterprodukten
 
Sie können ab sofort das Erstellen, Aktualisieren und Löschen von Benutzerkonten für diese neu integrierten Apps automatisieren:

- [Chatwork](../saas-apps/chatwork-provisioning-tutorial.md)
- [FreshService](../saas-apps/freshservice-provisioning-tutorial.md)
- [InviteDesk](../saas-apps/invitedesk-provisioning-tutorial.md)
- [Maptician](../saas-apps/maptician-provisioning-tutorial.md)

Weitere Informationen darüber, wie Sie Ihr Unternehmen durch die automatische Bereitstellung von Benutzerkonten besser schützen können, finden Sie unter Automatisierte Bereitstellung von Benutzern für SaaS-Anwendungen mit Azure AD.
 
---

### <a name="multifactor-mfa-fraud-report--new-audit-event"></a>Multi-Faktor-Betrugsbericht (MFA) – neues Prüfungsereignis

**Typ:** Geändertes Feature  
**Dienstkategorie:** MFA  
**Produktfunktion:** Identitätssicherheit und -schutz
 

Damit Administratoren erkennen können, dass Benutzer aufgrund eines Betrugsberichts für die MFA gesperrt sind, haben wir ein neues Überwachungsereignis hinzugefügt. Dieses Audit-Ereignis wird nachverfolgt, wenn der Benutzer Betrug meldet. Das Audit-Protokoll ist zusätzlich zu den bestehenden Informationen in den Anmeldeprotokollen über Betrugsmeldungen verfügbar. Wie Sie den Prüfbericht erhalten, erfahren Sie unter [Multifaktor-Authentifizierung Betrugsalarm](../authentication/howto-mfa-mfasettings.md#fraud-alert).

---

### <a name="improved-low-risk-detections"></a>Verbesserte Low-Risk-Detektionen

**Typ:** Geändertes Feature  
**Dienstkategorie:** Schutz der Identität (Identity Protection)  
**Produktfunktion:** Identitätssicherheit und -schutz

Um die Qualität der von Identity Protection ausgegebenen Warnmeldungen bei niedrigem Risiko zu verbessern, haben wir den Algorithmus so geändert, dass weniger riskante Anmeldungen ausgegeben werden. Unternehmen werden möglicherweise einen erheblichen Rückgang der Anmeldungen mit niedrigem Risiko in ihrer Umgebung feststellen. [Weitere Informationen](../identity-protection/concept-identity-protection-risks.md)
 
---

### <a name="non-interactive-risky-sign-ins"></a>Nicht-interaktive riskante Anmeldungen

**Typ:** Geändertes Feature  
**Dienstkategorie:** Schutz der Identität (Identity Protection)  
**Produktfunktion:** Identitätssicherheit und -schutz
 
Identitätsschutz gibt jetzt riskante Sign-Ins bei nicht-interaktiven Sign-Ins aus. Admins können diese riskanten Sign-Ins mit dem Filter **Sign-In-Typ** im Bericht über riskante Sign-Ins finden. [Weitere Informationen](../identity-protection/howto-identity-protection-investigate-risk.md)
 
---

### <a name="change-from-user-administrator-to-identity-governance-administrator-in-entitlement-management"></a>Wechsel vom Benutzeradministrator zum Identity Governance Administrator in der Berechtigungsverwaltung 

**Typ:** Geändertes Feature  
**Dienstkategorie:** Rollen  
**Produktfunktion:** Identity Governance
 
Die Zuweisung von Berechtigungen für die Verwaltung von Zugriffspaketen und anderen Ressourcen in der Berechtigungsverwaltung wird von der Rolle des Benutzeradministrators auf die Rolle des Identity Governance-Administrators übertragen. 

Benutzer, denen die Rolle des Benutzeradministrators zugewiesen wurde, können keine Kataloge mehr erstellen oder Zugangspakete in einem Katalog verwalten, dessen Eigentümer sie nicht sind. Wenn Benutzern in Ihrer Organisation die Rolle Benutzeradministrator zugewiesen wurde, um Kataloge, Zugriffspakete oder Richtlinien in der Berechtigungsverwaltung zu konfigurieren, benötigen sie eine neue Zuweisung. Stattdessen sollten Sie diesen Benutzern die Rolle des Identity Governance-Administrators zuweisen. [Weitere Informationen](../governance/entitlement-management-delegate.md)

---

### <a name="windows-azure-active-directory-connector-is-deprecated"></a>Windows Azure Active Directory-Konnektor ist veraltet

**Typ:** Als veraltet markiert  
**Dienstkategorie:** Microsoft Identity Manager  
**Produktfunktion:** Identity Lifecycle Management
 
Der Windows Azure AD Connector für FIM ist eingefroren und wird nicht mehr weiterentwickelt. Die Lösung, FIM und den Azure AD Connector zu verwenden, wurde ersetzt. Bestehende Bereitstellungen sollten auf [Azure AD Connect](../hybrid/whatis-hybrid-identity.md), Azure AD Connect Sync oder den [Microsoft Graph Connector](/microsoft-identity-manager/microsoft-identity-manager-2016-connector-graph) migrieren, da die vom Azure AD Connector für FIM verwendeten internen Schnittstellen aus Azure AD entfernt werden. [Weitere Informationen](/microsoft-identity-manager/microsoft-identity-manager-2016-deprecated-features)

---

### <a name="retirement-of-older-azure-ad-connect-versions"></a>Ausmusterung älterer Azure AD Connect-Versionen

**Typ:** Als veraltet markiert  
**Dienstkategorie**: AD Connect  
**Produktfunktion:** Benutzerverwaltung
 
Ab dem 31. August 2022 werden alle V1-Versionen von Azure AD Connect außer Betrieb genommen. Wenn Sie dies noch nicht getan haben, müssen Sie Ihren Server auf Azure AD Connect V2.0 aktualisieren. Sie müssen sicherstellen, dass Sie eine aktuelle Version von Azure AD Connect verwenden, um einen optimalen Support zu erhalten.

Wenn Sie eine veraltete Version von Azure AD Connect verwenden, kann es sein, dass sie unerwartet nicht mehr funktioniert. Möglicherweise verfügen Sie auch nicht über die neuesten Sicherheitskorrekturen, Leistungsverbesserungen, Fehlerbehebungs- und Diagnosetools sowie Serviceerweiterungen. Auch wenn Sie Unterstützung benötigen, können wir Ihnen nicht den Service bieten, den Ihr Unternehmen benötigt.

Siehe [Azure Active Directory Connect V2.0](../hybrid/whatis-azure-ad-connect-v2.md), was sich in V2.0 geändert hat und wie sich diese Änderung auf Sie auswirkt.

---

### <a name="retirement-of-support-for-installing-mim-on-windows-server-2008-r2-or-sql-server-2008-r2"></a>Abschaffung der Unterstützung für die Installation von MIM auf Windows Server 2008 R2 oder SQL Server 2008 R2

**Typ:** Als veraltet markiert  
**Dienstkategorie:** Microsoft Identity Manager  
**Produktfunktion:** Identity Lifecycle Management
 
Die Bereitstellung von MIM Sync, Service, Portal oder CM auf Windows Server 2008 R2 oder die Verwendung von SQL Server 2008 R2 als zugrunde liegende Datenbank wird nicht mehr empfohlen, da diese Plattformen nicht mehr unterstützt werden. Die Installation von MIM Sync und anderen Komponenten auf Windows Server 2016 oder höher und mit SQL Server 2016 oder höher wird empfohlen.

Der Einsatz von MIM für Privileged Access Management mit einem Windows Server 2012 R2-Domänencontroller in der PRIV-Gesamtstruktur ist veraltet. Verwenden Sie Windows Server 2016 oder höher Active Directory mit der Funktionsebene Windows Server 2016 für Ihre PRIV-Gesamtstruktur-Domäne. Die Funktionsstufe Windows Server 2012 R2 ist für die Domäne einer CORP-Gesamtstruktur weiterhin zulässig. [Weitere Informationen](/microsoft-identity-manager/microsoft-identity-manager-2016-supported-platforms)

---

## <a name="july-2021"></a>Juli 2021

### <a name="new-google-sign-in-integration-for-azure-ad-b2c-and-b2b-self-service-sign-up-and-invited-external-users-will-stop-working-starting-july-12-2021"></a>Die neue Google-Anmeldeintegration für die Azure AD B2C- und Azure AD B2B-Self-Service-Registrierung und eingeladene externe Benutzer funktioniert ab dem 12. Juli 2021 nicht mehr

**Typ:** Plan für Änderung  
**Dienstkategorie:** B2B  
**Produktfunktion:** B2B/B2C
 

Wir haben bereits angekündigt, dass [die Ausnahme für eingebettete Webansichten für die Gmail-Authentifizierung in der zweiten Jahreshälfte 2021 abläuft](https://www.yammer.com/cepartners/threads/1188371962232832).

Am 7. Juli 2021 haben wir von Google erfahren, dass einige dieser Einschränkungen ab dem **12. Juli 2021** gelten. Für Azure AD B2B- und Azure AD B2C-Kunden, die in ihren benutzerdefinierten Anwendungen oder Branchenanwendungen eine neue Google-ID-Anmeldung eingerichtet haben, um externe Benutzer einzuladen oder die Self-Service-Registrierung zu aktivieren, gelten die Einschränkungen sofort. Daher wird Endbenutzern eine Fehlermeldung angezeigt, die ihre Gmail-Anmeldung blockiert, wenn keine Systemwebansicht für die Authentifizierung verwendet wird. Einzelheiten hierzu finden Sie in den unten verlinkten Dokumenten. 

Die meisten Apps verwenden standardmäßig die Systemwebansicht und sind von dieser Änderung nicht betroffen. Dies gilt nur für Kunden, die eingebettete Webviews verwenden (die nicht standardmäßige Einstellung). Wir empfehlen Kunden, die Authentifizierung ihrer Anwendung stattdessen auf Systembrowser zu verlagern, bevor sie neue Google-Integrationen erstellen. Informationen zum Umstellen von Gmail-Authentifizierungen auf Systembrowser finden Sie im Abschnitt „Eingebettete Webbenutzeroberfläche im Vergleich zur System-Webbenutzeroberfläche“ in der Dokumentation [Verwenden von Webbrowsern (MSAL.NET)](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui). Alle MSAL-SDKs verwenden standardmäßig die Systemwebansicht. [Weitere Informationen](../external-identities/google-federation.md#deprecation-of-web-view-sign-in-support)

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

Weitere Informationen zu „Meine Apps“ finden Sie unter [Anmelden beim Portal „Meine Apps“ und Starten von Apps über dieses](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

---

### <a name="public-preview----application-authentication-method-policies"></a>Öffentliche Vorschau: Richtlinien für Anwendungsauthentifizierungsmethoden

**Typ:** Neues Feature  
**Dienstkategorie:** MS Graph  
**Produktfunktion:** Entwickleroberfläche
 
Richtlinien für Anwendungsauthentifizierungsmethoden in Microsoft Graph, mit denen IT-Administratoren die Gültigkeitsdauer von Anmeldeinformationen für geheime Anwendungskennwörter erzwingen oder die Verwendung von Geheimnissen vollständig blockieren können. Die Richtlinien können für einen gesamten Mandanten als Standardkonfiguration erzwungen und auf bestimmte Anwendungen oder Dienstprinzipale festgelegt werden. [Weitere Informationen](/graph/api/resources/policy-overview?view=graph-rest-beta&preserve-view=true)
 
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
 
In der Azure AD-Berechtigungsverwaltung kann ein Administrator definieren, dass ein Zugriffspaket mit einem anderen Zugriffspaket oder einer Gruppe inkompatibel ist. Benutzer mit inkompatiblen Mitgliedschaften können dann keinen weiteren Zugriff anfordern. [Weitere Informationen](../governance/entitlement-management-access-package-request-policy.md#prevent-requests-from-users-with-incompatible-access-preview)
 
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
 
Der Ressourcentyp „onPremisesPublishing“ enthält jetzt die Eigenschaft „isBackendCertificateValidationEnabled“, die angibt, ob die Back-End-SSL-Zertifikatüberprüfung für die Anwendung aktiviert ist. Für alle neuen Anwendungsproxy-Apps wird die Eigenschaft standardmäßig auf „true“ festgelegt. Für alle vorhandenen Apps wird die Eigenschaft auf „false“ festgelegt. Weitere Informationen finden Sie unter [onPremisesPublishing-Ressourcentyp](/graph/api/resources/onpremisespublishing?view=graph-rest-beta&preserve-view=true).
 
---

### <a name="general-availability---improved-authenticator-setup-experience-for-add-azure-ad-account-in-microsoft-authenticator-app-by-directly-signing-into-the-app"></a>Allgemeine Verfügbarkeit: Verbesserte Authenticator-Einrichtungsfunktion zum Hinzufügen eines Azure AD-Kontos in der Microsoft Authenticator-App durch die Möglichkeit der direkten Anmeldung bei der App.

**Typ:** Neues Feature  
**Dienstkategorie:** Microsoft Authenticator-App  
**Produktfunktion:** Benutzerauthentifizierung
 
Benutzer können sich jetzt mit ihren vorhandenen Authentifizierungsmethoden direkt bei der Microsoft Authenticator-App anmelden, um ihre Anmeldeinformationen einzurichten. Benutzer müssen keinen QR-Code mehr scannen und können einen befristeten Zugriffspass (Temporary Access Pass, TAP) oder Kennwort + SMS (oder eine andere Authentifizierungsmethode) verwenden, um ihr Konto in der Authenticator-App zu konfigurieren.

Dadurch wird der Prozess zum Bereitstellen von Benutzeranmeldeinformationen für die Microsoft Authenticator-App verbessert, und der Endbenutzer kann eine Self-Service-Methode zum Bereitstellen der App verwenden. [Weitere Informationen](https://support.microsoft.com/account-billing/add-your-work-or-school-account-to-the-microsoft-authenticator-app-43a73ab5-b4e8-446d-9e54-2a4cb8e4e93c#sign-in-with-your-credentials)
 
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
 
Für bedingten Zugriff ist jetzt die Benutzeraktion zum Registrieren oder Hinzufügen von Geräten allgemein verfügbar. Mit dieser Benutzeraktion können Sie MFA-Richtlinien (Multi-Factor Authentication) für die Azure AD-Geräteregistrierung steuern. 

Derzeit können Sie mit dieser Benutzeraktion die MFA nur als Steuerelement aktivieren, wenn Benutzer Geräte registrieren oder zu Azure AD hinzufügen. Andere Steuerelemente, die von der Azure AD-Geräteregistrierung abhängen oder darauf nicht anwendbar sind, werden mit dieser Benutzeraktion weiterhin deaktiviert. [Weitere Informationen](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions) 

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

**Typ:** Geändertes Feature  
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
 
 Sie können Azure AD-Zugriffsüberprüfungen verwenden, um den Zugriff von Dienstprinzipalen auf privilegierte Azure AD- und Azure-Ressourcenrollen zu überprüfen. [Weitere Informationen](../privileged-identity-management/pim-create-azure-ad-roles-and-resource-roles-review.md#create-access-reviews)
 
---

### <a name="public-preview----group-owners-in-azure-ad-can-create-and-manage-azure-ad-access-reviews-for-their-groups"></a>Öffentliche Vorschau: Gruppenbesitzer in Azure AD können Azure AD-Zugriffsüberprüfungen für ihre Gruppen erstellen und verwalten

**Typ:** Neue Funktion  
**Dienstkategorie**: Zugriffsüberprüfungen  
**Produktfunktion:** Identity Governance
 
Gruppenbesitzer in Azure AD können jetzt Azure AD-Zugriffsüberprüfungen für ihre Gruppen erstellen und verwalten. Diese Funktion ist standardmäßig deaktiviert und kann von Mandantenadministratoren über Azure AD-Zugriffsüberprüfungseinstellungen aktiviert werden. [Weitere Informationen](../governance/create-access-review.md#allow-group-owners-to-create-and-manage-access-reviews-of-their-groups-preview)
 
---

### <a name="public-preview----customers-can-scope-access-reviews-of-privileged-roles-to-just-users-with-eligible-or-active-access"></a>Öffentliche Vorschau: Kunden können Zugriffsüberprüfungen privilegierter Rollen auf Benutzer mit berechtigtem oder aktivem Zugriff einschränken

**Typ:** Neue Funktion  
**Dienstkategorie**: Zugriffsüberprüfungen  
**Produktfunktion:** Identity Governance
 
Wenn Administratoren Zugriffsüberprüfungen von Zuweisungen privilegierter Rollen erstellen, können sie festlegen, dass die Überprüfungen nur für berechtigte zugewiesene Benutzer oder nur für aktiv zugewiesene Benutzer durchgeführt werden. [Weitere Informationen](../privileged-identity-management/pim-create-azure-ad-roles-and-resource-roles-review.md)
 
---

### <a name="public-preview----microsoft-graph-apis-for-mobility-mdmmam-management-policies"></a>Öffentliche Vorschau: Microsoft Graph-APIs für Verwaltungsrichtlinien für Mobilität (MDM/MAM)

**Typ:** Neues Feature  
**Dienstkategorie:** Sonstige  
**Produktfunktion:** Lebenszyklusverwaltung für Geräte
 
Die Microsoft Graph-Unterstützung für die Konfiguration von Mobility (MDM/MAM) in Azure AD befindet sich in der öffentlichen Vorschau. Administratoren können den Benutzerbereich und URLs für MDM-Anwendungen wie Intune mithilfe von Microsoft Graph v1.0 konfigurieren. Weitere Informationen finden Sie unter [mobilityManagementPolicy-Ressourcentyp](/graph/api/resources/mobilitymanagementpolicy?view=graph-rest-beta&preserve-view=true).

---

### <a name="general-availability---custom-questions-in-access-package-request-flow-in-azure-active-directory-entitlement-management"></a>Allgemeine Verfügbarkeit: Benutzerdefinierte Fragen im Zugriffspaket-Anforderungsflow in der Azure Active Directory-Berechtigungsverwaltung

**Typ:** Neues Feature  
**Dienstkategorie:** Benutzerzugriffsverwaltung  
**Produktfunktion:** Berechtigungsverwaltung
 
Die Azure AD-Berechtigungsverwaltung unterstützt jetzt die Erstellung benutzerdefinierter Fragen im Zugriffspaket-Anforderungsflow. Mit diesem Feature können Sie benutzerdefinierte Fragen in der Zugriffspaketrichtlinie konfigurieren. Diese Fragen werden Anforderern angezeigt, die ihre Antworten im Rahmen des Zugriffsanforderungsprozesses eingeben können. Diese Antworten werden dann den genehmigenden Personen angezeigt, sodass sie hilfreiche Informationen erhalten, durch die sie bessere Entscheidungen über die Zugriffsanforderung treffen können. [Weitere Informationen](../governance/entitlement-management-access-package-create.md)

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
 
Microsoft Graph-APIs für Azure Active Directory-Zugriffsüberprüfungen sind jetzt in Version 1.0 enthalten und unterstützen vollständig konfigurierbare Funktionen für Zugriffsüberprüfungen. [Weitere Informationen](/graph/api/resources/accessreviewsv2-root?view=graph-rest-1.0&preserve-view=true)
 
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
**Dienstkategorie:** Unternehmens-Apps  
**Produktfunktion:** Integration von Drittanbieterprodukten
 
Im Juni 2021 haben wir die folgenden 42 neuen Anwendungen mit Verbundunterstützung in unseren App-Katalog aufgenommen:

[Taksel](https://help.ubuntu.com/community/Tasksel), [IDrive360](../saas-apps/idrive360-tutorial.md), [VIDA](../saas-apps/vida-tutorial.md), [ProProfs Classroom](../saas-apps/proprofs-classroom-tutorial.md), [WAN-Sign](../saas-apps/wan-sign-tutorial.md), [Citrix Cloud SAML SSO](../saas-apps/citrix-cloud-saml-sso-tutorial.md), [Fabric](../saas-apps/fabric-tutorial.md), [DssAD](https://cloudlicensing.deepseedsolutions.com/), [RICOH Creative Collaboration RICC](https://www.ricoh-europe.com/products/software-apps/collaboration-board-software/ricc/), [Styleflow](../saas-apps/styleflow-tutorial.md), [Chaos](https://accounts.chaosgroup.com/corporate_login), [Traced Connector](https://control.traced.app/signup), [Squarespace](https://account.squarespace.com/org/azure), [MX3 Diagnostics Connector](https://mx3www.playground.dynuddns.com/signin-oidc), [Ten Spot](https://tenspot.co/api/v1/sso/azure/login/), [Finvari](../saas-apps/finvari-tutorial.md), [Mobile4ERP](https://play.google.com/store/apps/details?id=com.negevsoft.mobile4erp), [WalkMe US OpenID Connect](https://www.walkme.com/), [Neustar UltraDNS](../saas-apps/neustar-ultradns-tutorial.md), [cloudtamer.io](../saas-apps/cloudtamer-io-tutorial.md), [A Cloud Guru](../saas-apps/a-cloud-guru-tutorial.md), [PetroVue](../saas-apps/petrovue-tutorial.md), [Postman](../saas-apps/postman-tutorial.md), [ReadCube Papers](../saas-apps/readcube-papers-tutorial.md), [Peklostroj](https://app.peklostroj.cz/), [SynCloud](https://onboard.syncloud.io/), [Polymerhq.io](https://www.polymerhq.io/), [Bonos](../saas-apps/bonos-tutorial.md), [Astra Schedule](../saas-apps/astra-schedule-tutorial.md), [Draup](../saas-apps/draup-inc-tutorial.md), [Inc](../saas-apps/draup-inc-tutorial.md), [Applied Mental Health](../saas-apps/applied-mental-health-tutorial.md), [iHASCO Training](../saas-apps/ihasco-training-tutorial.md), [Nexsure](../saas-apps/nexsure-tutorial.md), [XEOX](https://login.xeox.com/), [Plandisc](https://create.plandisc.com/account/logon), [foundU](../saas-apps/foundu-tutorial.md), [Standard for Success Accreditation](../saas-apps/standard-for-success-accreditation-tutorial.md), [Penji Teams](https://web.penjiapp.com/), [CheckPoint Infinity Portal](../saas-apps/checkpoint-infinity-portal-tutorial.md), [Teamgo](../saas-apps/teamgo-tutorial.md), [Hopsworks.ai](../saas-apps/hopsworks-ai-tutorial.md) und [HoloMeeting 2](https://backend2.holomeeting.io/)

Die Dokumentation zu allen Anwendungen finden Sie auch hier: https://aka.ms/AppsTutorial

Informationen zum Auflisten Ihrer Anwendung im Azure AD-App-Katalog finden Sie hier: https://aka.ms/AzureADAppRequest
 
---

### <a name="device-code-flow-now-includes-an-app-verification-prompt"></a>Der Gerätecodeflow enthält jetzt eine Aufforderung zur App-Überprüfung

**Typ:** Geändertes Feature  
**Dienstkategorie:** Authentifizierungen (Anmeldungen)  
**Produktfunktion:** Benutzerauthentifizierung
 
Der [Gerätecodeflow](../develop/v2-oauth2-device-code.md) wurde aktualisiert und umfasst jetzt eine zusätzliche Benutzereingabeaufforderung. Während der Anmeldung wird der Benutzer aufgefordert, die App zu überprüfen, bei der er sich anmeldet.  Mit der Eingabeaufforderung wird sichergestellt, dass die Benutzer keinem Phishingangriff ausgesetzt werden. [Weitere Informationen](../develop/reference-breaking-changes.md#the-device-code-flow-ux-will-now-include-an-app-confirmation-prompt)
 
---

### <a name="user-last-sign-in-date-and-time-is-now-available-on-azure-portal"></a>Datum und Uhrzeit der letzten Benutzeranmeldung jetzt im Azure-Portal verfügbar

**Typ:** Geändertes Feature  
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

Um Phishing-Angriffe zu verhindern, bei denen ein Angreifer den Benutzer dazu verleitet, sich bei einer bösartigen Anwendung anzumelden, wird die folgende Aufforderung hinzugefügt: "Versuchen Sie, sich bei [Anzeigename der Anwendung] anzumelden?". Diese Aufforderung wird allen Benutzern beim Anmelden mit dem Gerätecodeflow angezeigt. Da es sich um eine Sicherheitsmaßnahme handelt, kann sie nicht entfernt oder umgangen werden. [Weitere Informationen](../develop/reference-breaking-changes.md#the-device-code-flow-ux-will-now-include-an-app-confirmation-prompt)

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
**Dienstkategorie:** Unternehmens-Apps  
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
**Dienstkategorie:** Unternehmens-Apps  
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

- "Helfen Sie uns, Ihr Gerät sicher zu halten" hat sich geändert in "Richten Sie Ihr Gerät ein, um Zugriff zu erhalten"
- "Ihre Anmeldung war erfolgreich, aber Ihr Administrator verlangt, dass Ihr Gerät von Microsoft verwaltet wird, um auf diese Ressource zugreifen zu können." zu "[Name der Organisation] erfordert, dass Sie dieses Gerät sichern, bevor Sie auf E-Mails, Dateien und Daten von [Name der Organisation] zugreifen können." 
- Von "Jetzt anmelden" zu "fortfahren"

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
 
Eine aktualisierte Version der PIM-API für Azure-Ressourcenrollen und Azure AD-Rollen wurde veröffentlicht. Die PIM-API für Azure-Ressourcenrollen wird jetzt unter dem ARM-API-Standard veröffentlicht, der auf die Rollenverwaltungs-API für die reguläre Azure-Rollenzuweisung ausgerichtet ist. Andererseits wird die PIM-API für Azure AD-Rollen auch unter der Graph-API veröffentlicht, die auf die unifiedRoleManagement-APIs ausgerichtet ist. Diese Änderung bietet unter anderem die folgenden Vorteile:

- Ausrichtung der PIM-API auf Objekte in ARM und Graph für die Rollenverwaltung; Verringerung der Notwendigkeit, PIM für das Onboarding neuer Azure-Ressourcen aufzurufen. 
- Alle Azure-Ressourcen funktionieren automatisch mit der neuen PIM-API.
- Verringerung der Notwendigkeit, PIM für die Rollendefinition aufzurufen oder eine PIM-Ressourcen-ID beizubehalten
- Unterstützung von Nur-App-API-Berechtigungen in PIM für Azure AD- und Azure Resource-Rollen

Die frühere Version der PIM-API unter `/privilegedaccess` funktioniert weiterhin, aber es wird empfohlen, in Zukunft zu dieser neuen API zu wechseln. [Weitere Informationen](../privileged-identity-management/pim-apis.md)
 
---

### <a name="revision-of-roles-in-azure-ad-entitlement-management"></a>Revision von Rollen in der Azure AD-Berechtigungsverwaltung

**Typ:** Geändertes Feature  
**Dienstkategorie:** Rollen  
**Produktfunktion:** Berechtigungsverwaltung
 
Vor Kurzem wurde die neue Rolle „Identity Governance-Administrator“ eingeführt. Diese Rolle ersetzt die Rolle „Benutzeradministrator“ beim Verwalten von Katalogen und Zugriffspaketen in der Azure AD-Berechtigungsverwaltung. Wenn Sie Administratoren die Rolle „Benutzeradministrator“ zugewiesen haben oder diese Rolle zum Verwalten von Zugriffspaketen in der Azure AD-Berechtigungsverwaltung aktivieren lassen, verwenden Sie stattdessen die Rolle „Identity Governance-Administrator“. Die Rolle „Benutzeradministrator“ stellt keine Administratorrechte mehr für Kataloge oder Zugriffspakete bereit. [Weitere Informationen](../governance/identity-governance-overview.md#appendix---least-privileged-roles-for-managing-in-identity-governance-features)

---

