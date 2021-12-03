---
title: Planen einer Bereitstellung für einmaliges Anmelden
description: Planen der Bereitstellung von Single Sign-On in Azure Active Directory.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 8f8140362a7b2709aaf1ea635e61a43e84eb503a
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546574"
---
# <a name="plan-a-single-sign-on-deployment"></a>Planen einer Bereitstellung für einmaliges Anmelden

Dieser Artikel enthält Informationen, die Sie für die Planung Ihrer [SSO-Bereitstellung (Single Sign-On)](what-is-single-sign-on.md) in Azure Active Directory (Azure AD) verwenden können. Wenn Sie Ihre SSO-Bereitstellung mit Ihren Anwendungen in Azure AD planen, müssen Sie diese Fragen berücksichtigen:

- Welche administrativen Rollen sind für die Verwaltung der Anwendung erforderlich?
- Muss das Zertifikat erneuert werden?
- Wer muss über Änderungen im Zusammenhang mit der Einführung von SSO informiert werden?
- Welche Lizenzen sind erforderlich, um eine effektive Verwaltung der Anwendung zu gewährleisten?
- Werden gemeinsame Benutzerkonten für den Zugriff auf die Anwendung verwendet?
- Verstehe ich die Optionen für die SSO-Bereitstellung?

## <a name="administrative-roles"></a>Administrative Rollen

Verwenden Sie immer die Rolle mit den wenigsten verfügbaren Berechtigungen, um die erforderliche Aufgabe in Azure AD zu erfüllen. Prüfen Sie die verschiedenen Rollen, die zur Verfügung stehen, und wählen Sie die richtige aus, um Ihre Anforderungen für jede Persona in der Anwendung zu erfüllen. Einige Rollen müssen unter Umständen nur vorübergehend angewendet und können nach Abschluss der Bereitstellung wieder entfernt werden.

| Persona | Rollen | Azure AD-Rolle (sofern erforderlich) |
| ------- | ----- | --------------------------- |
| Helpdesk-Administrator | Support der Ebene 1 | Keine |
| Identitätsadministrator | Konfigurieren und Debuggen, wenn Probleme mit Azure AD auftreten | Globaler Administrator |
| Anwendungsadministrator | Benutzernachweis in Anwendung, Konfiguration für Benutzer mit Berechtigungen | Keine |
| Infrastrukturadministratoren | Inhaber des Rollover-Zertifikats | Globaler Administrator |
| Geschäftsbesitzer/Beteiligter | Benutzernachweis in Anwendung, Konfiguration für Benutzer mit Berechtigungen | Keine |

Weitere Informationen zu Azure AD Administratorrollen finden Sie unter [Azure AD integrierten Rollen](../users-groups-roles/directory-assign-admin-roles.md).

## <a name="certificates"></a>Zertifikate

Wenn Sie föderiertes SSO für Ihre Anwendung aktivieren, erstellt Azure AD ein Zertifikat, das standardmäßig drei Jahre gültig ist. Sie können das Ablaufdatum für dieses Zertifikat bei Bedarf anpassen. Stellen Sie sicher, dass Sie über Prozesse zur Erneuerung von Zertifikaten vor deren Ablauf verfügen. 

Sie können die Zertifikatsdauer im Azure-Portal ändern. Achten Sie darauf, den Ablauf zu dokumentieren, und planen Sie, wie Sie die Zertifikatverlängerung verwalten. Es ist wichtig, die richtigen Rollen und E-Mail-Verteilerlisten zu identifizieren, die an der Verwaltung des Lebenszyklus des Signaturzertifikats beteiligt sind. Die folgenden Rollen werden empfohlen:

- Verantwortlicher für die Aktualisierung der Benutzereigenschaften in der Anwendung
- Bereitschaftsdienst des Eigentümers zur Unterstützung bei Anwendungsproblemen
- Eng überwachte E-Mail-Verteilerliste für Änderungsbenachrichtigungen im Zusammenhang mit Zertifikaten

Sie können die folgenden Methoden zur Verwaltung Ihrer Zertifikate verwenden:

- **Automatischer Zertifikatrollover** - Der Rollover von Signierschlüsseln wird in Azure AD unterstützt. Die bevorzugte Methode zur Verwaltung von Zertifikaten ist das Rollover von Signierschlüsseln, doch nicht alle Anwendungen unterstützen dieses Szenario.

- **Manuelles Update** - Jede Anwendung hat ihr eigenes Zertifikat, das je nach Definition abläuft. Erstellen Sie ein neues Zertifikat, bevor das Zertifikat der Anwendung abläuft, und senden Sie es an den Anbieter der Anwendung. Diese Informationen können aus den Verbundmetadaten entnommen werden. Weitere Informationen finden Sie unter [Verbundmetadaten](../azuread-dev/azure-ad-federation-metadata.md). 

Richten Sie einen Prozess ein, wie Sie eine Zertifikatsänderung zwischen Azure AD und Ihrer Anwendung handhaben werden. Mit diesem Verfahren können Sie Ausfälle aufgrund eines ablaufenden Zertifikats oder eines erzwungenen Zertifikatwechsels verhindern oder minimieren. Weitere Informationen finden Sie unter [Verwalten von Zertifikaten für die einmalige Verbundanmeldung in Azure Active Directory](manage-certificates-for-federated-single-sign-on.md).

## <a name="communications"></a>Kommunikation

Kommunikation ist ein kritischer Faktor für den Erfolg jedes neuen Diensts. Kommunizieren Sie proaktiv mit Ihren Nutzern, wie sich ihre Erfahrungen ändern werden. Teilen Sie mit, wann sich die Situation ändern wird und wie Sie Unterstützung erhalten, falls Probleme auftreten. Prüfen Sie die Optionen, wie Benutzer auf ihre SSO-fähigen Anwendungen zugreifen werden, und gestalten Sie Ihre Kommunikation entsprechend Ihrer Auswahl.

Implementieren Sie Ihren Kommunikationsplan. Stellen Sie sicher, dass Ihre Nutzer wissen, dass eine Änderung bevorsteht, wann sie eingetroffen ist und was sie jetzt tun müssen. Stellen Sie außerdem sicher, dass Sie Informationen darüber bereitstellen, wie Sie Hilfe erhalten können.

## <a name="licensing"></a>Lizenzierung

Vergewissern Sie sich, dass die Anwendung den folgenden Zulassungsanforderungen entspricht:

- **Azure AD-Lizenzierung** - SSO für vorintegrierte Unternehmensanwendungen ist kostenlos. Die Anzahl der Objekte in Ihrem Verzeichnis und die Funktionen, die Sie einsetzen möchten, können jedoch mehr Lizenzen erfordern. Eine vollständige Liste der Lizenzanforderungen finden Sie unter [Azure Active Directory-Preise](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing).

- **Anwendungslizenzen** - Sie benötigen die entsprechenden Lizenzen für Ihre Anwendungen, um die Anforderungen Ihres Unternehmens zu erfüllen. Arbeiten Sie mit dem Besitzer der Anwendung zusammen, um festzustellen, ob die der Anwendung zugeordneten Benutzer über die entsprechenden Lizenzen für ihre Rollen innerhalb der Anwendung verfügen. Wenn Azure AD die automatische Bereitstellung auf der Grundlage von Rollen verwaltet, müssen die in Azure AD zugewiesenen Rollen mit der Anzahl der Lizenzen in der Anwendung übereinstimmen. Eine unzureichende Anzahl von Lizenzen in der Anwendung kann zu Fehlern bei der Bereitstellung oder Aktualisierung eines Benutzerkontos führen.

## <a name="shared-accounts"></a>Gemeinsam genutzte Konten

Aus Sicht der Anmeldung unterscheiden sich Anwendungen mit gemeinsam genutzten Konten nicht von Unternehmensanwendungen, die Passwort-SSO für einzelne Benutzer verwenden. Bei der Planung und Konfiguration einer Anwendung, die gemeinsame Konten verwenden soll, sind jedoch mehr Schritte erforderlich.
- Arbeiten Sie mit den Benutzern zusammen, um die folgenden Informationen zu dokumentieren:
    - Die Gruppe der Benutzer in der Organisation, die die Anwendung verwenden werden.
    - Der bestehende Satz von Anmeldeinformationen in der Anwendung, der mit dem Satz von Benutzern verbunden ist.
- Erstellen Sie für jede Kombination aus Benutzergruppe und Anmeldeinformationen basierend auf Ihren Anforderungen eine Sicherheitsgruppe in der Cloud oder lokal.
- Setzen Sie die gemeinsam genutzten Anmeldeinformationen zurück. Nachdem die Anwendung in Azure AD bereitgestellt wurde, benötigen Einzelpersonen das Kennwort des gemeinsamen Kontos nicht mehr. Azure AD speichert das Kennwort, und Sie sollten darauf achten, dass es lang und komplex ist.
- Konfigurieren Sie automatischen Rollover des Kennworts, wenn die Anwendung dies unterstützt. Auf diese Weise kennt nicht einmal der Administrator, der die Ersteinrichtung vorgenommen hat, das Kennwort des gemeinsamen Kontos.

## <a name="single-sign-on-options"></a>Optionen für einmaliges Anmelden

Es gibt mehrere Möglichkeiten, wie Sie eine Anwendung für SSO konfigurieren können. Die Auswahl einer Methode für einmaliges Anmelden hängt davon ab, wie die Anwendung für Authentifizierung konfiguriert ist.
- Cloud-Anwendungen können OpenID Connect, OAuth, SAML, passwortbasiert oder verknüpft für SSO verwenden. Single Sign-On kann auch deaktiviert werden.
- Lokale Anwendungen können passwortbasierte, integrierte Windows-Authentifizierung, Header-basierte, verknüpfte für SSO verwenden. Die Auswahlmöglichkeiten vor Ort funktionieren, wenn Anwendungen für [Anwendungsproxy](../app-proxy/what-is-application-proxy.md) konfiguriert sind.

Dieses Flussdiagramm kann Ihnen bei der Entscheidung helfen, welche SSO-Methode für Ihre Situation am besten geeignet ist.

![Flussdiagramm zum Treffen einer Entscheidung für eine Methode zum einmaligen Anmelden](./media/plan-sso-deployment/single-sign-on-options.png)
 
Die folgenden SSO-Protokolle können verwendet werden:

- **OpenID Connect und OAuth** - Wählen Sie OpenID Connect und OAuth 2.0, wenn die Anwendung, mit der Sie sich verbinden möchten, dies unterstützt. Weitere Informationen finden Sie unter [OAuth 2.0 und OpenID Connect-Protokolle auf der Microsoft Identity Platform](../develop/active-directory-v2-protocols.md). Schritte zur Implementierung von OpenID Connect SSO finden Sie unter [Einrichten von OIDC-basiertem Single Sign-On für eine Anwendung in Azure Active Directory](add-application-portal-setup-oidc-sso.md).

- **SAML** - Wählen Sie SAML wann immer möglich für bestehende Anwendungen, die nicht OpenID Connect oder OAuth verwenden. Weitere Informationen finden Sie unter [Single Sign-On SAML-Protokoll](../develop/single-sign-on-saml-protocol.md). Eine kurze Einführung in die Implementierung von SAML SSO finden Sie unter[Quickstart: Einrichten von SAML-basiertem Single Sign-On für eine Anwendung in Azure Active Directory](add-application-portal-setup-sso.md). 

- **Passwortbasiert** - Wählen Sie passwortbasiert, wenn die Anwendung eine HTML-Anmeldeseite hat. Das kennwortbasierte einmalige Anmelden wird auch als „Password Vaulting“ oder „Kennworttresore“ bezeichnet. Das kennwortbasierte einmalige Anmelden bietet Ihnen die Möglichkeit, den Benutzerzugriff und die Kennwörter für Webanwendungen zu verwalten, die keinen Identitätsverbund unterstützen. Das Feature ist auch nützlich, wenn mehrere Benutzer ein Konto gemeinsam verwenden müssen (z. B. die Konten für Social Media-Apps in Ihrer Organisation). 

    Passwortbasiertes SSO unterstützt Anwendungen, die mehrere Anmeldefelder für Anwendungen erfordern, die mehr als nur Benutzername und Passwort für die Anmeldung benötigen. Sie können die Beschriftung der Felder für den Benutzernamen und das Kennwort anpassen, die Ihre Benutzer bei der Eingabe ihrer Anmeldedaten in „Meine Apps“ sehen. Schritte zur Implementierung von kennwortbasiertem SSO finden Sie unter [Kennwortbasiertes Single Sign-On](configure-password-single-sign-on-non-gallery-applications.md).

- **Verknüpft** - Wählen Sie Verknüpft, wenn die Anwendung für SSO in einem anderen Identitätsanbieterdienst konfiguriert ist. Mit der Option „Verknüpft“ können Sie den Zielort konfigurieren, wenn ein Benutzer die Anwendung in den Portalen Ihrer Organisation auswählt. Sie können einen Link zu einer benutzerdefinierten Webanwendung hinzufügen, die derzeit Federation verwendet, z. B. Active Directory Federation Services (AD FS). 

    Sie können auch Links zu bestimmten Webseiten hinzufügen, die in den Zugangsbereichen Ihrer Benutzer angezeigt werden sollen, sowie zu einer App, die keine Authentifizierung erfordert. Mit der Option Verknüpft werden keine Anmeldefunktionen über Azure AD-Anmeldeinformationen bereitgestellt. Schritte zur Implementierung von Linked SSO finden Sie unter [Linked Single Sign-On](configure-linked-sign-on.md).

- **Deaktiviert** - Wählen Sie „SSO deaktiviert“, wenn die Anwendung nicht für SSO konfiguriert werden kann.

- **Integrierte Windows-Authentifizierung (IWA)** - Wählen Sie IWA-Einzelanmeldung für Anwendungen, die IWA verwenden, oder für anspruchssensitive Anwendungen. Weitere Informationen finden Sie unter [Kerberos Constrained Delegation für die einmalige Anmeldung bei Ihren Anwendungen mit Application Proxy](../app-proxy/application-proxy-configure-single-sign-on-with-kcd.md).

- **Header-basiert** - Wählen Sie Header-basiertes Single Sign-On, wenn die Anwendung „Header“ für die Authentifizierung verwendet. Weitere Informationen finden Sie unter [Header-basiertes SSO](../app-proxy/application-proxy-configure-single-sign-on-with-headers.md).

## <a name="next-steps"></a>Nächste Schritte
- [Verwalten des Zugriffs auf Apps](what-is-access-management.md)
