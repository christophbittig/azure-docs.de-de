---
title: Worum handelt es sich bei der Anwendungsverwaltung?
description: Eine Übersicht über die Verwaltung des Lebenszyklus einer Anwendung in Azure Active Directory.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 10/22/2021
ms.author: davidmu
ms.reviewer: sureshja, napuri
ms.openlocfilehash: 3b1e814def005283fb08ab7eeb3c23a8a38e3ee2
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130257391"
---
# <a name="what-is-application-management-in-azure-active-directory"></a>Was ist die Anwendungsverwaltung in Azure Active Directory?

Die Anwendungsverwaltung in Azure Active Directory (Azure AD) umfasst das Erstellen, Konfigurieren, Verwalten und Überwachen von Anwendungen in der Cloud. Wenn eine [Anwendung](../develop/app-objects-and-service-principals.md) in einem Azure AD-Mandanten registriert ist, können zugewiesene Benutzer sicher darauf zugreifen. In Azure AD können viele Arten von Anwendungen registriert werden. Weitere Informationen finden Sie unter [Anwendungstypen für die Microsoft Identity Platform](../develop/v2-app-types.md).

In diesem Artikel werden folgende wichtige Aspekte der Anwendungslebenszyklusverwaltung vermittelt:

- **Entwickeln, Hinzufügen oder Verbinden:** Ihre Vorgehensweise hängt davon ab, ob Sie Ihre eigene Anwendung entwickeln, eine vorab integrierte Anwendung verwenden oder eine Verbindung mit einer lokalen Anwendung herstellen möchten.
- **Verwalten des Zugriffs:** Sie können einmaliges Anmelden (Single Sign-On, SSO) verwenden, Ressourcen zuweisen, die Art der Zugriffsgewährung und Einwilligung definieren sowie die automatisierte Bereitstellung verwenden, um den Zugriff zu verwalten.
- **Konfigurieren von Eigenschaften:** Konfigurieren Sie die Anforderungen für die Anmeldung bei der Anwendung sowie die Darstellung der Anwendung in Benutzerportalen.
- **Schützen der Anwendung:** Verwalten Sie die Konfiguration von Berechtigungen, mehrstufiger Authentifizierung (Multifactor Authentication, MFA), bedingtem Zugriff, Token und Zertifikaten.
- **Steuern und Überwachen:** Verwenden Sie die Berechtigungsverwaltung sowie Berichterstellungs- und Überwachungsressourcen, um Interaktionen zu verwalten und Aktivitäten zu überprüfen.
- **Bereinigen:** Wenn Ihre Anwendung nicht mehr benötigt wird, bereinigen Sie Ihren Mandanten, indem Sie den Zugriff entfernen und die Anwendung löschen.

## <a name="develop-add-or-connect"></a>Entwickeln, Hinzufügen oder Verbinden

Anwendungen können in Azure AD auf verschiedene Arten verwaltet werden. Die einfachste Möglichkeit für den Einstieg in die Anwendungsverwaltung ist die Verwendung einer vorab integrierten Anwendung aus dem Azure AD-Katalog. Sie können aber auch eine eigene Anwendung entwickeln und in Azure AD registrieren oder weiterhin eine lokale Anwendung verwenden.

Die folgende Abbildung zeigt die Interaktion dieser Anwendungen mit Azure AD:

:::image type="content" source="media/what-is-application-management/app-management-overview.png" alt-text="Diagramm: Verwendung selbst entwickelter Apps, vorab integrierter Apps und lokaler Apps als Unternehmens-Apps":::

### <a name="pre-integrated-applications"></a>Vorab integrierte Anwendungen

Viele Anwendungen sind bereits vorab integriert (in der Abbildung oben als Cloudanwendungen dargestellt) und können mit minimalem Aufwand eingerichtet werden. Für jede Anwendung im Azure AD-Katalog steht ein Artikel zur Verfügung, in dem die erforderlichen Schritte zum [Konfigurieren der Anwendung](../saas-apps/tutorial-list.md) beschrieben werden. Ein einfaches Beispiel für das Hinzufügen einer Anwendung aus dem Katalog zu einem Azure AD-Mandanten finden Sie unter [Schnellstart: Hinzufügen einer Unternehmensanwendung in Azure Active Directory](add-application-portal.md).

### <a name="your-own-applications"></a>Eigene Anwendungen

Wenn Sie eine eigene Geschäftsanwendung entwickeln, können Sie sie bei Azure AD registrieren, um von den Sicherheitsfeatures des Mandanten zu profitieren. Sie können Ihre Anwendung in **App-Registrierungen** registrieren oder den Link **Eigene Anwendung erstellen** verwenden, wenn Sie eine neue Anwendung in **Unternehmensanwendungen** hinzufügen. Machen Sie sich Gedanken zur Implementierung der [Authentifizierung](../develop/authentication-vs-authorization.md) in Ihrer Anwendung für die Integration in Azure AD. 

Wenn Sie Ihre Anwendung über den Katalog verfügbar machen möchten, können Sie die [Aufnahme Ihrer Anwendung in den Katalog beantragen](../develop/v2-howto-app-gallery-listing.md).


### <a name="on-premises-applications"></a>Lokale Anwendungen

Wenn Sie weiterhin eine lokale Anwendung verwenden, aber auch von den Vorteilen von Azure AD profitieren möchten, können Sie die Anwendung per [Azure AD-Anwendungsproxy](../app-proxy/application-proxy.md) mit Azure AD verbinden. Durch Implementieren des Anwendungsproxys können lokale Anwendungen extern veröffentlicht werden. Remotebenutzer, die Zugriff auf interne Anwendungen benötigen, können dann auf sichere Weise auf sie zugreifen.

## <a name="manage-access"></a>Verwalten des Zugriffs

Im Zusammenhang mit der [Verwaltung des Zugriffs](what-is-access-management.md) für eine Anwendung müssen folgende Fragen beantwortet werden:

- Wie werden Zugriffsgewährung und Einwilligung für die Anwendung gehandhabt?
- Unterstützt die Anwendung SSO?
- Welche Benutzer, Gruppen und Besitzer sollen der Anwendung zugewiesen werden? 
- Gibt es andere Identitätsanbieter, die die Anwendung unterstützen?
- Ist es sinnvoll, die Bereitstellung von Benutzeridentitäten und Rollen zu automatisieren?

### <a name="access-and-consent"></a>Zugriff und Einwilligung

Sie können [Einstellungen für die Benutzereinwilligung verwalten](configure-user-consent.md), um zu entscheiden, ob Benutzer einer Anwendung oder einem Dienst Zugriff auf Benutzerprofile und Organisationsdaten gewähren können. Wenn Anwendungen Zugriff gewährt wird, können sich Benutzer bei in Azure AD integrierten Anwendungen anmelden, und die Anwendung kann auf die Daten Ihrer Organisation zugreifen, um umfassende datengesteuerte Erfahrungen zu bieten.

Benutzer können häufig keine Einwilligung für die von einer Anwendung angeforderten Berechtigungen erteilen. Konfigurieren Sie den [Workflow für die Administratoreinwilligung](configure-admin-consent-workflow.md), damit Benutzer eine Begründung angeben und die Überprüfung und Genehmigung einer Anwendung durch einen Administrator anfordern können.

Administratoren können einer Anwendung eine [mandantenweite Administratoreinwilligung erteilen](grant-admin-consent.md). Die mandantenweite Administratoreinwilligung ist erforderlich, wenn eine Anwendung Berechtigungen erfordert, die von regulären Benutzern nicht gewährt werden dürfen, und sie ermöglicht es Organisationen, eigene Überprüfungsprozesse zu implementieren. Überprüfen Sie vor dem Erteilen einer Einwilligung immer sorgfältig die Berechtigungen, die von der Anwendung angefordert werden. Wenn für eine Anwendung eine mandantenweite Administratoreinwilligung erteilt wurde, können sich alle Benutzer bei der Anwendung anmelden – es sei denn, in der Konfiguration wurde festgelegt, dass eine Benutzerzuweisung erforderlich ist.

### <a name="single-sign-on"></a>Einmaliges Anmelden

Erwägen Sie die Implementierung von SSO in Ihrer Anwendung. Die meisten Anwendungen können manuell für SSO konfiguriert werden. Die gängigsten Optionen in Azure AD sind [SAML-basiertes SSO und OpenID Connect-basiertes SSO](../develop/active-directory-v2-protocols.md). Machen Sie sich zunächst mit den Anforderungen für SSO und mit der [Bereitstellungsplanung](plan-sso-deployment.md) vertraut. Unter [Schnellstart: Aktivieren des einmaligen Anmeldens für eine Unternehmensanwendungen in Azure Active Directory](add-application-portal-setup-sso.md) finden Sie ein einfaches Beispiel, in dem SAML-basiertes einmaliges Anmelden für eine Unternehmensanwendung in einem Azure AD-Mandanten konfiguriert wird.

### <a name="user-group-and-owner-assignment"></a>Benutzer-, Gruppen- und Besitzerzuweisung

Standardmäßig können alle Benutzer auf Ihre Unternehmensanwendungen zugreifen, ohne diesen zugewiesen zu sein. Wenn Sie die Anwendung jedoch einer Gruppe von Benutzern zuweisen möchten, ist eine Benutzerzuweisung erforderlich. Ein einfaches Beispiel für die Erstellung und Zuweisung eines Benutzerkontos zu einer Anwendung finden Sie unter [Schnellstart: Erstellen und Zuweisen eines Benutzerkontos in Azure Active Directory](add-application-portal-assign-users.md). 

Sofern diese Option in Ihrem Abonnement enthalten ist, können Sie [einer Anwendung Gruppen zuweisen](assign-user-or-group-access-portal.md), um die laufende Zugriffsverwaltung an den Gruppenbesitzer zu delegieren. 

Durch [Zuweisen von Besitzern](assign-app-owners.md) können Sie ganz einfach die Verwaltung sämtlicher Aspekte der Azure AD-Konfiguration für eine Anwendung ermöglichen. Als Besitzer kann ein Benutzer die organisationsspezifische Konfiguration der Anwendung verwalten.

### <a name="automate-provisioning"></a>Automatisieren der Bereitstellung

[Anwendungsbereitstellung](../app-provisioning/user-provisioning.md) bezieht sich auf die automatische Erstellung von Benutzeridentitäten und Rollen in den Anwendungen, auf die Benutzer Zugriff benötigen. Zusätzlich zur Erstellung von Benutzeridentitäten umfasst die automatische Bereitstellung auch die Wartung und Entfernung von Benutzeridentitäten, wenn sich der Status oder die Rollen ändern.

### <a name="identity-providers"></a>Identitätsanbieter

Verfügen Sie über einen Identitätsanbieter, mit dem Azure AD interagieren soll? [Home Realm Discovery](home-realm-discovery-policy.md) bietet eine Konfiguration, mit der Azure AD bestimmen kann, bei welchem Identitätsanbieter sich ein Benutzer bei der Anmeldung authentifizieren muss.

### <a name="user-portals"></a>Benutzerportale

Azure AD bietet anpassbare Möglichkeiten, um Anwendungen für Benutzer in Ihrer Organisation bereitzustellen: Hierzu zählen etwa das [Portal „Meine Apps“ und das Microsoft 365-Anwendungsstartprogramm](end-user-experiences.md). „Meine Apps“ ist ein zentraler Ort, an dem Benutzer mit ihrer Arbeit beginnen können und alle Anwendungen finden, auf die sie Zugriff haben. Als Administrator einer Anwendung sollten Sie [planen, wie die Benutzer in Ihrer Organisation „Meine Apps“ nutzen](my-apps-deployment-plan.md).

## <a name="configure-properties"></a>Konfigurieren von Eigenschaften

Wenn Sie Ihrem Azure AD-Mandanten eine Anwendung hinzufügen, können Sie [Eigenschaften konfigurieren](add-application-portal-configure.md), die sich auf die Anmeldemöglichkeiten von Benutzern auswirken. Sie können die Möglichkeit zur Anmeldung aktivieren oder deaktivieren sowie die Benutzerzuweisung erforderlich machen. Außerdem können Sie die Sichtbarkeit und das Logo der Anwendung sowie ggf. Hinweise zur Anwendung konfigurieren.

## <a name="secure-the-application"></a>Schützen der Anwendung

Es stehen mehrere hilfreiche Methoden für den Schutz Ihrer Unternehmensanwendungen zur Verfügung. So können Sie beispielsweise den [Zugriff auf einen Mandanten einschränken](tenant-restrictions.md), [Sichtbarkeit, Daten und Analysen verwalten](cloud-app-security.md) und ggf. [Hybridzugriff](secure-hybrid-access.md) bereitstellen. Zum Schutz Ihrer Unternehmensanwendungen gehört auch die Verwaltung der Konfiguration von Berechtigungen, MFA, bedingtem Zugriff, Token und Zertifikaten.

### <a name="permissions"></a>Berechtigungen

Die Berechtigungen, die einer Anwendung oder einem Dienst erteilt wurden, müssen regelmäßig überprüft und bei Bedarf [verwaltet](manage-application-permissions.md) werden. Überprüfen Sie regelmäßig, ob verdächtige Aktivitäten vorhanden sind, um sicherzustellen, dass Sie nur angemessenen Zugriff auf Ihre Anwendungen zulassen.

Mit [Berechtigungsklassifizierungen](configure-permission-classifications.md) können Sie die Auswirkungen unterschiedlicher Berechtigungen gemäß den Richtlinien und Risikobewertungen Ihres Unternehmens ermitteln. Beispielsweise können Sie Berechtigungsklassifizierungen in Einwilligungsrichtlinien verwenden, um den Berechtigungssatz zu identifizieren, dem Benutzer zustimmen dürfen.

### <a name="multifactor-authentication-and-conditional-access"></a>Mehrstufige Authentifizierung und bedingter Zugriff

Azure Active Directory Multifactor Authentication (Azure AD MFA) trägt durch die Verwendung einer zweiten Authentifizierungsform zum Schutz des Zugriffs auf Daten und Anwendungen bei. Es gibt viele Methoden, die für eine zweistufige Authentifizierung verwendet werden können. Bevor Sie beginnen, sollten Sie in Ihrer Organisation die [Bereitstellung der MFA für Ihre Anwendung planen](../authentication/howto-mfa-getstarted.md).

Organisationen können die MFA mit [bedingtem Zugriff](../conditional-access/overview.md) aktivieren, um die Lösung an ihre speziellen Anforderungen anzupassen. Richtlinien für bedingten Zugriff ermöglichen Administratoren das Zuweisen von Steuerungen zu [Anwendungen, Aktionen oder Authentifizierungskontext](../conditional-access/concept-conditional-access-cloud-apps.md).

### <a name="tokens-and-certificates"></a>Token und Zertifikate

In einem Authentifizierungsflow in Azure AD werden je nach verwendetem Protokoll verschiedene Arten von Sicherheitstoken verwendet. Für das SAML-Protokoll werden beispielsweise [SAML-Token](../develop/reference-saml-tokens.md) verwendet. Beim OpenID Connect-Protokoll sind es [ID-Token](../develop/id-tokens.md) und [Zugriffstoken](../develop/access-tokens.md). Token werden mit dem eindeutigen Zertifikat, das in Azure AD generiert wird, und durch spezifische Standardalgorithmen signiert. 

Durch [Verschlüsseln des Tokens](howto-saml-token-encryption.md) kann die Sicherheit noch weiter erhöht werden. Sie können auch die Informationen in einem Token verwalten – einschließlich der [zulässigen Rollen](../develop/howto-add-app-roles-in-azure-ad-apps.md) für die Anwendung.

Azure AD verwendet zum Signieren der SAML-Antwort standardmäßig den [SHA-256-Algorithmus](certificate-signing-options.md). Verwenden Sie SHA-256, es sei denn, die Anwendung erfordert SHA-1. Richten Sie einen Prozess zum [Verwalten der Lebensdauer des Zertifikats](manage-certificates-for-federated-single-sign-on.md) ein. Die maximale Lebensdauer eines Signaturzertifikats beträgt drei Jahre. Um Ausfälle aufgrund eines ablaufenden Zertifikats zu verhindern oder zu minimieren, verwenden Sie Rollen und E-Mail-Verteilerlisten, damit sichergestellt werden kann, dass zertifikatbezogene Änderungsbenachrichtigungen genau überwacht werden. 

## <a name="govern-and-monitor"></a>Steuern und Überwachen

Mit der [Berechtigungsverwaltung](../governance/entitlement-management-scenarios.md) in Azure AD können Sie die Interaktion zwischen Anwendungen und Administratoren, Katalogbesitzern, Zugriffspaket-Managern, genehmigenden Personen und Anforderern verwalten.

Ihre Azure AD-Lösung für die Berichterstellung und Überwachung hängt von gesetzlichen, betrieblichen und sicherheitstechnischen Anforderungen sowie von Ihrer Umgebung und Ihren Prozessen ab. In Azure AD stehen diverse Protokolle zur Verfügung, und Sie sollten die [Bereitstellung für Berichterstellung und Überwachung planen](../reports-monitoring/plan-monitoring-and-reporting.md), um für Ihre Anwendung die bestmögliche Erfahrung zu gewährleisten.

## <a name="clean-up"></a>Bereinigung

Sie können den Zugriff auf Anwendungen bereinigen, indem Sie beispielsweise den [Zugriff eines Benutzers entfernen](methods-for-removing-user-access.md). Sie können auch das [Anmeldeverfahren eines Benutzers deaktivieren](disable-user-sign-in-portal.md). Und Sie können die Anwendung löschen, wenn sie für die Organisation nicht mehr benötigt wird. Unter [Schnellstart: Löschen einer Unternehmensanwendung in Azure Active Directory](delete-application-portal.md) finden Sie ein einfaches Beispiel für die Löschung einer Unternehmensanwendung aus einem Azure AD-Mandanten.

## <a name="next-steps"></a>Nächste Schritte

- Unter [Schnellstart: Hinzufügen einer Unternehmensanwendung in Azure Active Directory](add-application-portal.md) erfahren Sie, wie Sie Ihre erste Unternehmensanwendung hinzufügen.