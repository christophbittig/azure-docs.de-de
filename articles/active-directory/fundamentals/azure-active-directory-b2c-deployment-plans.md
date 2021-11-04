---
title: Azure AD B2C-Bereitstellung
description: Azure Active Directory B2C-Bereitstellungsanleitung
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 7/12/2021
ms.author: gasinh
author: gargi-sinha
manager: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9935fb243b6c2824633e67ac1eed26a80b67bc9d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131068130"
---
# <a name="azure-active-directory-b2c-deployment-plans"></a>Azure Active Directory B2C-Bereitstellungspläne

Azure Active Directory B2C ist eine skalierbare Lösung für die Identitäts- und Zugriffsverwaltung. Die hohe Flexibilität zum Erfüllen Ihrer geschäftlichen Erwartungen und die reibungslose Integration in die vorhandene Infrastruktur ermöglichen eine weitere Digitalisierung.

Um Organisationen dabei zu unterstützen, die geschäftlichen Anforderungen zu verstehen und Compliancegrenzen zu berücksichtigen, wird ein Schritt-für-Schritt-Ansatz für eine Azure Active Directory (Azure AD) B2C-Bereitstellung empfohlen.

| Funktion | BESCHREIBUNG |
|:-----|:------|
| [Planen](#plan-an-azure-ad-b2c-deployment) | Bereiten Sie Azure AD B2C-Projekte für die Bereitstellung vor. Identifizieren Sie zunächst die Projektbeteiligten, und definieren Sie danach eine Projektzeitachse. |
| [Implementieren](#implement-an-azure-ad-b2c-deployment) | Aktivieren Sie zunächst Authentifizierung und Autorisierung, und führen Sie danach ein vollständiges Anwendungsonboarding durch. |
| [Überwachen](#monitor-an-azure-ad-b2c-solution) | Aktivieren Sie Protokollierung, Überwachung und Berichterstellung, sobald eine Azure AD B2C-Lösung aktiv ist. |

## <a name="plan-an-azure-ad-b2c-deployment"></a>Planen einer Azure AD B2C-Bereitstellung

Diese Phase umfasst die folgenden Funktionen:

| Funktion | BESCHREIBUNG |
|:------------|:------------|
|[Überprüfen der geschäftlichen Anforderungen](#business-requirements-review) | Beurteilen von Status und Erwartungen Ihrer Organisation |
| [Projektbeteiligte](#stakeholders) |Erstellen des Projektteams |
|[Kommunikation](#communication) | Kommunikation mit dem Team über das Projekt |
| [Zeitachse](#timeline) | Erinnerung an wichtige Projektmeilensteine  |

### <a name="business-requirements-review"></a>Überprüfen der geschäftlichen Anforderungen

- Beurteilen Sie den Hauptgrund für das Außerbetriebnahme vorhandener Systeme und den [Wechsel zu Azure AD B2C](../../active-directory-b2c/overview.md).

- [Planen und Entwerfen](../../active-directory-b2c/best-practices.md#planning-and-design) Sie das CIAM-System (Customer Identity Access Management) für eine neue Anwendung.

- Ermitteln Sie den Standort des Kunden, und [erstellen Sie einen Mandanten im entsprechenden Rechenzentrum](../../active-directory-b2c/tutorial-create-tenant.md).

- Überprüfen Sie den Typ der vorhandenen Anwendungen.
  - Überprüfen Sie die derzeit unterstützten Plattformen: [MSAL](../develop/msal-overview.md) oder [Open Source](https://azure.microsoft.com/free/open-source/search/?OCID=AID2200277_SEM_f63bcafc4d5f1d7378bfaa2085b249f9:G:s&ef_id=f63bcafc4d5f1d7378bfaa2085b249f9:G:s&msclkid=f63bcafc4d5f1d7378bfaa2085b249f9).
  - Verwenden Sie für Back-End-Dienste den [Flow für Clientanmeldeinformationen](../develop/msal-authentication-flows.md#client-credentials).

- Wenn Sie beabsichtigen, von einem vorhandenen Identitätsanbieter (IdP) zu migrieren:

  - Erwägen Sie, den [nahtlosen Migrationsansatz](../../active-directory-b2c/user-migration.md#seamless-migration) zu verwenden.
  - Informieren Sie sich, [wie die vorhandenen Anwendungen migriert werden](https://github.com/azure-ad-b2c/user-migration).
  - Stellen Sie die gleichzeitige Koexistenz mehrerer Lösungen sicher.

- Bestimmen Sie die Protokolle, die Sie verwenden möchten.

  - [Migrieren Sie Ihre Anwendungen und gestalten Sie diese um](https://www.bing.com/videos/search?q=application+migration+in+azure+ad+b2c&docid=608034225244808069&mid=E21B87D02347A8260128E21B87D02347A8260128&view=detail&FORM=VIRE), wenn Sie derzeit Kerberos, NTLM und WS-Fed verwenden. Nach der Migration können Ihre Anwendungen moderne Identitätsprotokolle wie OAuth 2.0 und OpenID Connect (OIDC) unterstützen, um weitergehenden Identitätsschutz und mehr Sicherheit zu ermöglichen.

### <a name="stakeholders"></a>Projektbeteiligte

Wenn Technologieprojekte nicht gelingen, ist dies in der Regel auf nicht erfüllte Erwartungen auf den Gebieten Auswirkungen, Ergebnisse und Zuständigkeiten zurückzuführen. Um diese Fallstricke zu vermeiden, stellen Sie sicher, dass Sie die [richtigen Beteiligten hinzuziehen](./active-directory-deployment-plans.md#include-the-right-stakeholders) und dass die Beteiligten ihre Rollen verstehen.

- Bestimmen Sie den primären Architekten, den Projektleiter und den Besitzer der Anwendung.

- Erwägen Sie, eine Verteilerliste zur Verfügung zu stellen. Mithilfe dieser Verteilerliste können Sie Produktprobleme mit dem Microsoft-Konto-Team oder mit der Technik kommunizieren. Sie können Fragen stellen und wichtige Benachrichtigungen erhalten.

- Ermitteln Sie einen Partner oder eine Ressource außerhalb Ihrer Organisation, der/die Sie unterstützen kann.

### <a name="communication"></a>Kommunikation

Kommunikation ist ein kritischer Faktor für den Erfolg jedes neuen Diensts. Informieren Sie Ihre Benutzer proaktiv über die Änderung. Informieren Sie die Benutzer rechtzeitig darüber, wie und wann sich die Funktionalität ändert und wie sie Unterstützung erhalten, wenn Probleme auftreten.

### <a name="timeline"></a>Zeitachse

Definieren Sie klare Erwartungen und Folgepläne, um wichtige Meilensteine zu erfüllen:

- Erwartetes Pilotdatum

- Erwartetes Startdatum

- Alle Termine, die sich auf das Bereitstellungsdatum des Projekts auswirken können

## <a name="implement-an-azure-ad-b2c-deployment"></a>Implementieren einer Azure AD B2C-Bereitstellung

Diese Phase umfasst die folgenden Funktionen:

| Funktion | BESCHREIBUNG |
|:-------------|:--------------|
| [Bereitstellen von Authentifizierung und Autorisierung](#deploy-authentication-and-authorization) | Verstehen der Szenarien für [Authentifizierung und Autorisierung](../develop/authentication-vs-authorization.md) |
| [Bereitstellen von Anwendungen und Benutzeridentitäten](#deploy-applications-and-user-identities) | Planen der Bereitstellung von Clientanwendungen und der Migration von Benutzeridentitäten  |
| [Onboarding von Clientanwendungen und Ergebnisse](#client-application-onboarding-and-deliverables) | Onboarding der Clientanwendung und Testen der Lösung |
| [Security](#security) | Verbessern der Sicherheit Ihrer Identitätslösung |
|[Compliance](#compliance) | Berücksichtigen gesetzlicher Anforderungen |
|[Benutzerfreundlichkeit](#user-experience) | Ermöglichen eines benutzerfreundlichen Diensts |

### <a name="deploy-authentication-and-authorization"></a>Bereitstellen von Authentifizierung und Autorisierung

- Beginnen Sie mit der [Einrichtung eines Azure AD B2C-Mandaten](../../active-directory-b2c/tutorial-create-tenant.md).

- Verwenden Sie für die geschäftsorientierte Autorisierung die [Beispiele für Azure AD B2C Identity Experience Framework (IEF) User Journeys](https://github.com/azure-ad-b2c/samples#local-account-policy-enhancements).

- Probieren Sie [Open Policy Agent](https://www.openpolicyagent.org/) aus.

Weitere Informationen zu Azure AD B2C finden Sie in [diesem Entwicklerkurs](https://aka.ms/learnaadb2c).

Berücksichtigen Sie diese Beispielcheckliste, um weitergehende Hilfestellung zu erhalten:

- Identifizieren Sie die verschiedenen Personas, die Zugriff auf Ihre Anwendung benötigen.  

- Definieren Sie, wie Sie Zugriffsberechtigungen und Berechtigungen in Ihrem bestehenden System derzeit verwalten und wie Sie dies zukünftig planen.

- Überprüfen Sie, ob Sie über einen Berechtigungsspeicher verfügen und ob Berechtigungen vorhanden sind, die zum Verzeichnis hinzugefügt werden müssen.

- Definieren Sie, wie eine ggf. erforderlich delegierte Verwaltung gelöst werden soll, beispielsweise die Kundenverwaltung Ihrer Kunden.

- Überprüfen Sie, ob Ihre Anwendung direkt einen API-Manager (APIM) aufruft. Möglicherweise muss der Aufruf ausgehend vom IdP erfolgen, bevor ein Token für die Anwendung ausgestellt wird.

### <a name="deploy-applications-and-user-identities"></a>Bereitstellen von Anwendungen und Benutzeridentitäten

Alle Azure AD B2C-Projekte beginnen mit mindestens einer Clientanwendung, die unterschiedliche Geschäftsziele haben kann.

1. [Erstellen oder konfigurieren Sie die Clientanwendungen](../../active-directory-b2c/app-registrations-training-guide.md). Informationen zur Implementierung finden Sie in diesen [Codebeispielen](../../active-directory-b2c/integrate-with-app-code-samples.md).

2. Richten Sie als Nächstes Ihre User Journey basierend auf vordefinierten oder benutzerdefinierten Benutzerflows ein. [Erfahren Sie, wann Benutzerflows und wann benutzerdefinierte Richtlinien verwendet werden](../../active-directory-b2c/user-flow-overview.md#comparing-user-flows-and-custom-policies).

3. Richten Sie IdPs basierend auf Ihren geschäftlichen Anforderungen ein. [Erfahren Sie, wie Sie Azure Active Directory B2C als IdP hinzufügen](../../active-directory-b2c/add-identity-provider.md).

4. Migrieren Sie Ihre Benutzer. [Erfahren Sie mehr über Benutzermigrationsansätze](../../active-directory-b2c/user-migration.md). Erweiterte Szenarien finden Sie in den [Beispielen für Azure AD B2C IEF User Journeys](https://github.com/azure-ad-b2c/samples).  

Berücksichtigen Sie diese Beispielscheckliste, wenn Sie **Ihre Anwendungen bereitstellen**:

- Ermitteln Sie die Anzahl der Anwendungen, die sich im Bereich der CIAM-Bereitstellung befinden.

- Überprüfen Sie den Typ der verwendeten Anwendungen, z. B. herkömmliche Webanwendungen, APIs, Single-Page-Webanwendungen (SPA) oder native mobile Anwendungen.

- Überprüfen Sie die Art der verwendeten Authentifizierung, z. B. formularbasierte Authentifizierung, Verbundauthentifizierung mit SAML oder Verbundauthentifizierung mit OIDC.
  - Ermitteln Sie bei Verwendung von OIDC den Antworttyp: „code“ oder „id_token“.

- Überprüfen Sie, ob alle Front-End- und Back-End-Anwendungen lokal, in der Cloud oder in hybriden Clouds gehostet werden.

- Ermitteln Sie die verwendeten Plattformen/Sprachen wie [ASP.NET](../../active-directory-b2c/quickstart-web-app-dotnet.md), Java und Node.js.

- Überprüfen Sie, wo die aktuellen Benutzerattribute gespeichert sind, z. B. in LDAP (Lightweight Directory Access Protocol) oder in Datenbanken.

Berücksichtigen Sie diese Beispielscheckliste, wenn Sie **Benutzeridentitäten bereitstellen**:

- Ermitteln Sie die Anzahl der Benutzer, die auf die Anwendungen zugreifen.

- Überprüfen Sie den Typ der benötigten IdPs, z. B. Facebook, lokales Konto und [Active Directory-Verbunddienste (AD FS)](/windows-server/identity/active-directory-federation-services).

- Skizzieren Sie das erforderliche Anspruchsschema für Ihre Anwendung, [Azure AD B2C](../../active-directory-b2c/claimsschema.md) und ggf. Ihre IdPs.

- Skizzieren Sie die Informationen, die in einem [Anmelde-/Registrierungsflow](../../active-directory-b2c/add-sign-up-and-sign-in-policy.md?pivots=b2c-user-flow) erfasst werden müssen.

### <a name="client-application-onboarding-and-deliverables"></a>Onboarding von Clientanwendungen und Ergebnisse

Berücksichtigen Sie diese Beispielscheckliste, wenn Sie **das Onboarding für Anwendungen durchführen**:

|  Aufgabe | BESCHREIBUNG |
|:--------------------|:----------------------|
| Definieren der Zielgruppe der Anwendung | Überprüfen Sie, ob es sich bei dieser Anwendung um eine Endbenutzeranwendung, eine Geschäftskundenanwendung oder einen digitalen Dienst handelt. Überprüfen Sie, ob eine Mitarbeiteranmeldung notwendig ist. |
| Ermitteln des geschäftlichen Nutzens hinter einer Anwendung | Machen Sie sich mit dem vollständigen Geschäftsfall hinter einer Anwendung vertraut, um die optimale Azure AD B2C-Lösung und Integration in weitere Clientanwendungen zu finden.|
| Ermitteln der vorhandenen Identitätsgruppen | Fassen Sie Identitäten in verschiedenen Gruppen mit verschiedenartigen Anforderungen zusammen, z. B. **Business to Customer** (B2C) für Endkunden und Geschäftskunden, **Business to Business** (B2B) für Partner und Lieferanten, **Business to Employee** (B2E) für interne und externe Mitarbeiter, **Business to Machine** (B2M) für Anmeldungen von IoT-Geräten und für Dienstkonten.|
| Ermitteln des erforderlichen Identitätsanbieters für Ihre geschäftlichen Anforderungen und Prozesse | Azure AD B2C [unterstützt verschiedenartige IdPs](../../active-directory-b2c/add-identity-provider.md#select-an-identity-provider), sodass abhängig vom jeweiligen Anwendungsfall der richtige IdP ausgewählt werden muss. Beispielsweise ist für eine mobile Customer-to-Customer-Anwendung eine schnelle und einfache Benutzeranmeldung erforderlich. In einem anderen Anwendungsfall für eine Business-to-Customer-Anwendung mit digitalen Diensten müssen zusätzliche Complianceanforderungen berücksichtigt werden. Der Benutzer muss sich möglicherweise mit seiner Geschäftsidentität anmelden, z. B. mit seiner E-Mail-Adresse. |
| Überprüfen der gesetzlichen Einschränkungen | Überprüfen Sie, ob es Gründe für Remoteprofile oder bestimmte Datenschutzrichtlinien gibt.  |
| Entwerfen des Anmelde- und Registrierungsflows | Entscheiden Sie, ob eine E-Mail-Überprüfung oder eine E-Mail-Überprüfung in Registrierungen, z. B. beim ersten Check-Out-Prozess in einem Shopsystem, oder [Azure AD Multi-Factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md) erforderlich ist oder nicht. Sehen Sie sich [dieses Video](https://www.youtube.com/watch?v=c8rN1ZaR7wk&list=PL3ZTgFEc7LyuJ8YRSGXBUVItCPnQz3YX0&index=4) an. |
| Überprüfen des Typs der Anwendung und des Authentifizierungsprotokolls, das verwendet oder implementiert wird | Informationsaustausch über die Implementierung von Clientanwendungen, z. B. Webanwendung, SPA oder native Anwendung. Als Authentifizierungsprotokolle für Clientanwendungen und Azure AD B2C kommen u. a. OAuth, OIDC und SAML infrage. Sehen Sie sich [dieses Video](https://www.youtube.com/watch?v=r2TIVBCm7v4&list=PL3ZTgFEc7LyuJ8YRSGXBUVItCPnQz3YX0&index=9) an.|
| Planen der Benutzermigration | Erörtern Sie die Möglichkeiten der [Benutzermigration mit Azure AD B2C](../../active-directory-b2c/user-migration.md). Es sind mehrere Szenarien möglich, z. B. Just-In-Times-Migration (JIT) und Massenimport/-export. Sehen Sie sich [dieses Video](https://www.youtube.com/watch?v=lCWR6PGUgz0&list=PL3ZTgFEc7LyuJ8YRSGXBUVItCPnQz3YX0&index=2) an. Sie können auch die Verwendung der [Microsoft Graph-API](https://www.youtube.com/watch?v=9BRXBtkBzL4&list=PL3ZTgFEc7LyuJ8YRSGXBUVItCPnQz3YX0&index=3) für die Benutzermigration erwägen.|

Berücksichtigen Sie diese Beispielscheckliste bei der **Bereitstellung**.

| Funktion | BESCHREIBUNG |
|:-----|:-------|
|Protokollinformationen| Erfassen Sie Basispfad, Richtlinien und Metadaten-URL beider Varianten. Geben Sie abhängig von der Clientanwendung die Attribute an, z. B. Beispielanmeldung, Clientanwendungs-ID, Geheimnisse und Umleitungen.|
| Anwendungsbeispiele | Beispiele finden Sie in den bereitgestellten [Beispielcodes](../../active-directory-b2c/integrate-with-app-code-samples.md). |
|Penetrationstest | Informieren Sie ihr Betriebsteam vor den Tests über die Penetrationstests, und testen Sie dann alle Benutzerflows, einschließlich OAuth-Implementierung. Erfahren Sie mehr über [Penetrationstests](../../security/fundamentals/pen-testing.md) und die [einheitlichen Einsatzregeln der Microsoft Cloud für Penetrationtests](https://www.microsoft.com/msrc/pentest-rules-of-engagement).
| Komponententest  | Führen Sie Komponententests durch, und generieren Sie Token mit [ROPC-Flows (Resource Owner Password Credential)](../develop/v2-oauth-ropc.md). [Wenden Sie sich an das Supportteam](../../active-directory-b2c/support-options.md), wenn Sie das Azure AD B2C-Tokenlimit erreicht haben. Verwenden Sie Token wieder, um den Untersuchungsaufwand für Ihre Infrastruktur zu reduzieren. [Richten Sie einen ROPC-Flow ein](../../active-directory-b2c/add-ropc-policy.md?pivots=b2c-user-flow&tabs=app-reg-ga).|
| Auslastungstests | Gehen Sie davon aus, dass die Azure AD B2C-[Diensteinschränkungen](../../active-directory-b2c/service-limits.md) erreicht werden. Schätzen Sie die erwartete Anzahl von Authentifizierungen pro Monat für Ihren Dienst ab. Schätzen Sie die erwartete durchschnittliche Anzahl von Benutzeranmeldungen pro Monat ab. Veranschlagen Sie die erwartete Dauer von Datenverkehr mit hoher Auslastung und geschäftliche Randbedingungen wie Feiertage, Migrationen und Veranstaltungen. Schätzen Sie die erwartete maximale Registrierungsrate ab, z. B. die Anzahl der Anforderungen pro Sekunde. Schätzen Sie die erwartete maximale Datenverkehrsrate mit MFA ab, z. B. Anforderungen pro Sekunde. Schätzen Sie die erwartete geografische Verteilung des Datenverkehrs und die jeweiligen Spitzenraten ab.

### <a name="security"></a>Sicherheit

Berücksichtigen Sie diese Beispielcheckliste, um die Sicherheit Ihrer Anwendung abhängig von den jeweiligen Geschäftsanforderungen zu verbessern:

- Überprüfen Sie, ob eine strenge Authentifizierungsmethode wie [MFA](../authentication/concept-mfa-howitworks.md) erforderlich ist. Für Benutzer, die hochwertige Transaktionen oder andere Risikoereignisse auslösen, wird die Verwendung von MFA empfohlen. Dies gilt beispielsweise für Bank- und Finanzanwendungen sowie für Onlineshops beim ersten Check-Out-Prozess.

- Überprüfen Sie, ob MFA erforderlich ist. [Überprüfen Sie die verfügbaren Methoden für MFA](../authentication/concept-authentication-methods.md), z. B. SMS/Telefon, E-Mail und Dienste von Drittanbietern.

- Überprüfen Sie, ob derzeit ein Antibotmechanismus für Ihre Anwendungen verwendet wird.

- Beurteilen Sie das Risiko von Versuchen, betrügerische Konten und Anmeldungen zu erstellen. Verwenden Sie die [Microsoft Dynamics 365 Fraud Protection-Bewertung](../../active-directory-b2c/partner-dynamics-365-fraud-protection.md), um verdächtige Versuche zum Erstellen neuer gefälschter Konten oder zum Kompromittieren vorhandener Konten zu blockieren oder abzulehnen.  

- Überprüfen Sie, ob besondere vorbehaltliche geistige Einstellungen im Rahmen der Anmeldung oder Registrierung von Konten für Ihre Anwendung angewendet werden müssen.

>[!NOTE]
>Sie können [Regeln für bedingten Zugriff](../conditional-access/overview.md) verwenden, um einen geeigneten Kompromiss zwischen Benutzerfreundlichkeit und Sicherheit basierend auf Ihren Geschäftszielen zu erreichen.

Weitere Informationen finden Sie unter [Identity Protection und bedingter Zugriff in Azure AD B2C](../../active-directory-b2c/conditional-access-identity-protection-overview.md).

### <a name="compliance"></a>Compliance

Zum Erfüllen bestimmter gesetzlicher Anforderungen können Sie die Verwendung von vNets, IP-Einschränkungen, Web Application Firewall (WAF) und ähnliche Dienste in Betracht ziehen, um die Sicherheit Ihrer Back-End-Systeme zu verbessern.

Berücksichtigen Sie folgende Punkte, um grundlegende Complianceanforderungen zu erfüllen:

- Ermitteln Sie die jeweiligen Anforderungen in Bezug auf die Einhaltung gesetzlicher Bestimmungen, z. B. PCI-DSS, die Sie unterstützen müssen.

- Überprüfen Sie, ob Daten in einem separaten Datenbankspeicher gespeichert werden müssen. Überprüfen Sie, ob diese Informationen niemals in das Verzeichnis geschrieben werden dürfen, wenn dies der Fall ist.

### <a name="user-experience"></a>Benutzererfahrung

Berücksichtigen Sie die Beispielprüfliste, um die Anforderungen an die Benutzererfahrung zu definieren:

- Identifizieren Sie die erforderlichen Integrationen, um die [CIAM-Funktionen zu erweitern und nahtlose Endbenutzererfahrungen zu schaffen](../../active-directory-b2c/partner-gallery.md).

- Stellen Sie Screenshots und User Storys zur Verfügung, um die Endbenutzererfahrung für die vorhandene Anwendung zu veranschaulichen. Stellen Sie beispielsweise Screenshots für Anmeldung, Registrierung, kombinierte Registrierung/Anmeldung (SUSI), Profilbearbeitung und Kennwortzurücksetzung zur Verfügung.

- Suchen Sie mithilfe von queryString-Parametern in Ihrer aktuellen CIAM-Lösung nach vorhandenen Hinweisen, die durchgelassen wurden.

- Wenn Sie eine hohe Anpassung der Benutzererfahrung erwarten, z. B. Pixel zu Pixel, benötigen Sie möglicherweise einen Front-End-Entwickler, der Ihnen hilft.

## <a name="monitor-an-azure-ad-b2c-solution"></a>Überwachen einer Azure AD B2C-Lösung

Diese Phase umfasst die folgenden Funktionen:

| Funktion | BESCHREIBUNG |
|:---------|:----------|
|  Überwachung  |[Überwachen Sie Azure AD B2C mit Azure Monitor](../../active-directory-b2c/azure-monitor.md). Sehen Sie sich [dieses Video](https://www.youtube.com/watch?v=Mu9GQy-CbXI&list=PL3ZTgFEc7LyuJ8YRSGXBUVItCPnQz3YX0&index=1) an.|
| Überwachung und Protokollierung | [Zugreifen auf und Überprüfen von Überwachungsprotokollen](../../active-directory-b2c/view-audit-logs.md)

## <a name="more-information"></a>Weitere Informationen

Weitere Informationen zum Beschleunigen der Azure AD B2C-Bereitstellungen und der Überwachung des Diensts im großen Stil finden Sie in folgenden Artikeln:

- [Verwalten von Azure AD B2C mit Microsoft Graph](../../active-directory-b2c/microsoft-graph-get-started.md)

- [Verwalten von Azure AD B2C-Benutzerkonten mit Microsoft Graph](../../active-directory-b2c/microsoft-graph-operations.md)

- [Bereitstellen benutzerdefinierter Richtlinien mit Azure Pipelines](../../active-directory-b2c/deploy-custom-policies-devops.md)

- [Verwalten von benutzerdefinierten Azure AD B2C-Richtlinien mit Azure PowerShell](../../active-directory-b2c/manage-custom-policies-powershell.md)

- [Überwachen von Azure AD B2C mit Azure Monitor](../../active-directory-b2c/azure-monitor.md)

## <a name="next-steps"></a>Nächste Schritte

- [Bewährte Methoden für Azure AD B2C](../../active-directory-b2c/best-practices.md)

- [Azure AD B2C-Diensteinschränkungen](../../active-directory-b2c/service-limits.md)
