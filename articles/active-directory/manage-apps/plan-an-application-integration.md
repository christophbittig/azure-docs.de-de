---
title: Erste Schritte mit der Integration von Azure Active Directory mit Apps
description: In diesem Artikel sind die ersten Schritte für die Integration von lokalen Anwendungen und Cloudanwendungen in Azure Active Directory (AD)  aufgeführt.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 04/05/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.openlocfilehash: a19a0cebe3323e16b8f5cc7744c9e4db79393073
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2021
ms.locfileid: "129615769"
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Erste Schritte zur Integration von Anwendungen in Azure Active Directory

In diesem Thema wird der Prozess zur Integration von Anwendungen mit Azure Active Directory (AD) zusammengefasst. Jeder der folgenden Abschnitte enthält eine kurze Zusammenfassung eines ausführlicheren Themas, damit Sie feststellen können, welche Teile dieses Erste-Schritte-Leitfadens für Sie relevant sind.

Detaillierte Bereitstellungspläne finden Sie unter [Nächste Schritte](#next-steps).

## <a name="take-inventory"></a>Ermitteln des Bestands

Vor der Integration von Anwendungen in Azure AD sollten Sie unbedingt den aktuellen Stand ermitteln und festlegen, was Sie erreichen möchten.  Die folgenden Fragen helfen Ihnen bei Ihren Überlegungen zu Ihrem Azure AD-Projekt zur Anwendungsintegration.

### <a name="application-inventory"></a>Anwendungsbestand

* Wo befinden sich all Ihre Anwendungen? Wer ist der Eigentümer?
* Welche Art der Authentifizierung ist für Ihre Anwendungen erforderlich?
* Wer benötigt Zugriff auf welche Anwendungen?
* Möchten Sie eine neue Anwendung bereitstellen?
  * Wird diese innerbetrieblich erstellt und auf einer Azure Compute-Instanz bereitgestellt?
  * Möchten Sie eine Anwendung verwenden, die im Azure-Anwendungskatalog verfügbar ist?

### <a name="user-and-group-inventory"></a>Benutzer- und Gruppenbestand

* Wo befinden sich Ihre Benutzerkonten?
  * Lokales Active Directory
  * Azure AD
  * In einer separaten Anwendungsdatenbank, die Sie besitzen
  * In ungenehmigten Anwendungen
  * Alle oben genannten Möglichkeiten
* Welche Berechtigungen und Rollenzuweisungen gelten derzeit für die einzelnen Benutzer? Möchten Sie den Zugriff überprüfen, oder sind Sie sicher, dass die aktuellen Zugriffs- und Rollenzuweisungen angemessen sind?
* Sind in Ihrem lokalen Active Directory bereits Gruppen eingerichtet?
  * Wie sind Ihre Gruppen organisiert?
  * Wer sind die Mitglieder der Gruppen?
  * Welche Berechtigungen/Rollenzuweisungen gelten derzeit für die Gruppen?
* Müssen Sie vor der Integration Benutzer-/Gruppendatenbanken bereinigen?  (Dies ist eine wichtige Frage. Bei einer fehlerhaften Ausgangslage lässt sich kein optimales Ergebnis erzielen – „Garbage In, Garbage Out“.)

### <a name="access-management-inventory"></a>Bestand der Zugriffsverwaltung

* Wie verwalten Sie derzeit den Benutzerzugriff auf Anwendungen? Muss daran etwas geändert werden?  Haben Sie andere Methoden zum Verwalten des Zugriffs erwogen, beispielsweise [Azure RBAC](../../role-based-access-control/role-assignments-portal.md)?
* Wer muss worauf zugreifen?

Vielleicht können Sie nicht alle Fragen im Voraus beantworten, aber das ist kein Problem.  Dieser Leitfaden hilft Ihnen, einige dieser Fragen zu beantworten und einige fundierte Entscheidungen zu treffen.

### <a name="find-unsanctioned-cloud-applications-with-cloud-discovery"></a>Suchen nach nicht sanktionierten Cloudanwendungen per Cloud Discovery

Wie oben erwähnt sind möglicherweise Anwendungen im Einsatz, die bis jetzt von Ihrer Organisation noch nicht verwaltet wurden.  Im Rahmen der Bestandsaufnahme können Sie nicht genehmigte Cloudanwendungen finden. Weitere Informationen finden Sie unter [Einrichten von Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="integrating-applications-with-azure-ad"></a>Integrieren von Anwendungen in Azure Active Directory

In den folgenden Artikeln werden die verschiedenen Methoden zur Integration von Anwendungen in Azure AD erläutert und Anleitungen bereitgestellt.

* [Bestimmen des zu verwendenden Active Directory-Verzeichnisses](../fundamentals/active-directory-whatis.md)
* [Verwenden von Anwendungen im Azure-Anwendungskatalog](what-is-single-sign-on.md)
* [Liste der Tutorials zur Integration von SaaS-Anwendungen](../saas-apps/tutorial-list.md)

## <a name="capabilities-for-apps-not-listed-in-the-azure-ad-gallery"></a>Funktionen für Apps, die nicht im Azure AD-Katalog aufgeführt sind

Sie können in Ihrer Organisation bereits vorhandene Anwendungen oder Anwendungen eines Anbieters hinzufügen, der noch nicht im Azure AD-Katalog gelistet ist. Abhängig von Ihrem [Lizenzvertrag](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing) sind die folgenden Funktionen verfügbar:

* Self-Service-Integration von Anwendungen, die [Security Assertion Markup Language (SAML) 2.0](https://wikipedia.org/wiki/SAML_2.0)-Identitätsanbieter unterstützen (vom Dienstanbieter (SP) oder vom Identitätsanbieter (IdP) initiiert)
* Self-Service-Integration für Webanwendungen, die über eine HTML-basierte Anmeldeseite mit [kennwortbasierter einmaliger Anmeldung (Single Sign-On, SSO)](sso-options.md#password-based-sso)
* Self-Service-Verbindung für Anwendungen, die das [SCIM-Protokoll (System for Cross-Domain Identity Management) verwenden](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* Möglichkeit zum Hinzufügen von Links zu Anwendungen im [Office 365-App-Startfeld](https://support.microsoft.com/office/meet-the-microsoft-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a) oder in [Meine Apps](https://myapplications.microsoft.com/)

Wenn Sie nach einer Anleitung für Entwickler zum Integrieren von benutzerdefinierten Apps in Azure AD suchen, finden Sie diese unter [Authentifizierungsszenarios für Azure AD](../develop/authentication-vs-authorization.md). Wenn Sie eine App entwickeln, die ein modernes Protokoll wie [OpenID Connect/OAuth](../develop/active-directory-v2-protocols.md) zum Authentifizieren von Benutzern verwendet, können Sie die App im Azure-Portal über die Oberfläche für [App-Registrierungen](../develop/quickstart-register-app.md) bei der Microsoft Identity Platform registrieren.

### <a name="authentication-types"></a>Authentifizierungstypen

Für jede Ihrer Anwendungen gelten möglicherweise unterschiedliche Authentifizierungsanforderungen. Bei Azure AD können Signaturzertifikate mit Anwendungen verwendet werden, die die Protokolle SAML 2.0, WS-Verbund oder OpenID Connect sowie das einmalige Anmelden per Kennwort verwenden. Weitere Informationen zu Anwendungsauthentifizierungstypen finden Sie unter [Verwalten von Zertifikaten für die einmalige Verbundanmeldung in Azure Active Directory](manage-certificates-for-federated-single-sign-on.md) und unter [Kennwortbasierte einmalige Anmeldung](what-is-single-sign-on.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Aktivieren von SSO mit Azure AD-App-Proxy

Mit dem Microsoft Azure AD-Anwendungsproxy können Sie sicheren Zugriff von jedem Ort und mit jedem Gerät auf Anwendungen bereitstellen, die sich in Ihrem privaten Netzwerk befinden. Nachdem Sie einen Anwendungsproxy-Connector in Ihrer Umgebung installiert haben, können Sie ihn mit Azure AD leicht konfigurieren.

### <a name="integrating-custom-applications"></a>Integrieren benutzerdefinierter Anwendungen

Wenn Sie Ihre benutzerdefinierte Anwendung zum Azure-Anwendungskatalog hinzufügen möchten, lesen Sie [Veröffentlichen Ihrer App im Azure AD-App-Katalog](../develop/v2-howto-app-gallery-listing.md).

## <a name="managing-access-to-applications"></a>Verwalten des Zugriffs auf Anwendungen

In den folgenden Artikeln werden Möglichkeiten zur Verwaltung des Zugriffs auf Anwendungen beschrieben, nachdem sie mithilfe von Azure AD-Connectors und Azure AD in Azure AD integriert wurden.

* [Verwalten des Zugriffs auf Apps mit Azure AD](what-is-access-management.md)
* [Automatisieren mit Azure AD-Connectors](../app-provisioning/user-provisioning.md)
* [Zuweisen von Benutzern zu einer Anwendung](./assign-user-or-group-access-portal.md)
* [Zuweisen von Gruppen zu einer Anwendung](./assign-user-or-group-access-portal.md)
* [Gemeinsames Verwenden von Konten](../enterprise-users/users-sharing-accounts.md)

## <a name="next-steps"></a>Nächste Schritte

Für ausführliche Informationen können Sie Azure Active Directory-Bereitstellungspläne von [GitHub](../fundamentals/active-directory-deployment-plans.md) herunterladen. Für Kataloganwendungen können Sie Bereitstellungspläne für einmaliges Anmelden, den bedingten Zugriff und die Benutzerbereitstellung über das [Azure-Portal](https://portal.azure.com) herunterladen.

So laden Sie einen Bereitstellungsplan im Azure-Portal herunter

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie **Unternehmensanwendungen** | **Eine App auswählen** | **Bereitstellungsplan** aus.
