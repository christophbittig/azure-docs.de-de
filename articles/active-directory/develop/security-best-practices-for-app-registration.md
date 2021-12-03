---
title: Bewährte Methoden für die Konfiguration der Azure AD-Anwendungsregistrierung – Microsoft Identity Platform
description: Erfahren Sie mehr über bewährte Methoden und allgemeine Anleitungen zur Konfiguration der Azure AD-Anwendungsregistrierung.
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/8/2021
ms.custom: template-concept
ms.author: cchiedo
ms.reviewer: saumadan, marsma
ms.openlocfilehash: cd03531e35d51bafeceb2df913e17fc4814e911c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131050570"
---
# <a name="azure-ad-application-registration-security-best-practices"></a>Bewährte Methoden für die Sicherheit bei der Azure AD-Anwendungsregistrierung

Die Azure Active Directory (Azure AD)-Anwendungsregistrierung ist ein wichtiger Bestandteil Ihrer Geschäftsanwendung. Jede Fehlkonfiguration oder unsaubere Codierung ihrer Anwendung kann zu Ausfallzeiten oder Kompromittierung führen.

Sie müssen unbedingt verstehen, dass die Anwendungsregistrierung aufgrund ihres Umfangs größere Auswirkungen als die Geschäftsanwendung hat. Abhängig von den Berechtigungen, die Ihrer Anwendung hinzugefügt wurden, kann eine kompromittierte App organisationsweite Auswirkungen haben.
Da eine Anwendungsregistrierung für die Anmeldung Ihrer Benutzer von entscheidender Bedeutung ist, kann sich jeder Ausfall auf Ihr Unternehmen oder einen kritischen Dienst auswirken, von dem Ihr Unternehmen abhängt. Daher ist es wichtig, Zeit und Ressourcen vorzusehen, damit Ihre Anwendungsregistrierung immer in einem fehlerfreien Zustand bleibt. Es wird empfohlen, eine regelmäßige Sicherheits- und Integritätsbewertung Ihrer Anwendungen durchzuführen, ähnlich wie eine Bewertung des Sicherheitsbedrohungsmodells für Ihren Code. Einen umfassenderen Überblick über die Sicherheit für Organisationen finden Sie unter [Security Development Lifecycle](https://www.microsoft.com/securityengineering/sdl) (SDL).

In diesem Artikel werden bewährte Sicherheitsmethoden für die folgenden Anwendungsregistrierungseigenschaften beschrieben.

- Umleitungs-URI
- Impliziter Genehmigungsflow für Zugriffstoken
- Anmeldeinformationen
- AppId-URI
- Anwendungsbesitz
- Checkliste

## <a name="redirect-uri-configuration"></a>Konfiguration des Umleitungs-URI

Die Umleitungs-URIs Ihrer Anwendung müssen immer den aktuellen Stand aufweisen. Ein fehlerhafter Besitz einer der Umleitungs-URIs kann zu einer Kompromittierung der Anwendung führen. Stellen Sie sicher, dass alle DNS-Einträge in regelmäßigen Abständen aktualisiert und auf Änderungen überwacht werden. Verwenden Sie zusätzlich zur Aufrechterhaltung des Besitzes aller URIs keine Antwort-URLs mit Platzhaltern oder unsichere URI-Schemas wie HTTP oder URN.

![Umleitungs-URI](media/active-directory-application-registration-best-practices/redirect-uri.png)

### <a name="redirect-uri-summary"></a>Umleitungs-URI – Zusammenfassung

| Empfohlen                                    | Nicht empfohlen          |
| ------------------------------------- | -------------- |
| Aufrechterhalten des Besitzes aller URIs        | Verwenden von Platzhaltern  |
| Ständige DNS-Aktualisierung                   | Verwenden des URN-Schemas |
| Kleine Liste                   |   -----        |
| Kürzen aller unnötigen URIs             |   -----        |
| Aktualisieren von URLs von HTTP in HTTPS |   -----        |

## <a name="implicit-flow-token-configuration"></a>Implizite Flowtokenkonfiguration

Szenarien, die einen **impliziten Flow** erfordern, können jetzt den **Auth-Codeflow** verwenden, um das Risiko einer Kompromittierung durch Missbrauch des impliziten Genehmigungsflows zu reduzieren. Wenn Sie Ihre Anwendungsregistrierung so konfiguriert haben, dass Zugriffstoken über implizite Flows angefordert werden, diese aber nicht aktiv verwendet werden, wird empfohlen, die Einstellung zum Schutz vor Missbrauch zu deaktivieren.

![Zugriffstoken, die für implizite Flows verwendet werden](media/active-directory-application-registration-best-practices/implict-grant-flow.png)

### <a name="implicit-grant-flow-summary"></a>Impliziter Genehmigungsflow – Zusammenfassung

| Empfohlen                                                                    | Nicht empfohlen                                                                  |
| --------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| Feststellen, ob [ein impliziter Flow erforderlich ist](./v2-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant) | Verwenden impliziter Flows, es sei denn, dies ist [explizit erforderlich](./v2-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant) |
| Separate App-Registrierung für (gültige) implizite Flowszenarien |   -----                                                |
| Deaktivieren nicht verwendeter impliziter Flows | -----              |

## <a name="credential-configuration"></a>Konfiguration von Anmeldeinformationen

Anmeldeinformationen sind ein wichtiger Bestandteil einer Anwendungsregistrierung, wenn Ihre Anwendung als vertraulicher Client verwendet wird. Wenn Ihre App-Registrierung lediglich als öffentliche Client-App verwendet wird (sodass sich Benutzer mit einem öffentlichen Endpunkt anmelden können), muss sicher gestellt werden, dass in Ihrem Anwendungsobjekt keine Anmeldeinformationen vorhanden sind. Überprüfen Sie die in Ihren Anwendungen verwendeten Anmeldeinformationen auf Aktualität und deren Ablauf. Nicht verwendete Anmeldeinformationen für eine Anwendung können zu Sicherheitsverletzungen führen.
Es ist zwar praktisch, Kennwortgeheimnisse als Anmeldeinformationen zu verwenden, es wird jedoch dringend empfohlen, x509-Zertifikate als einzigen Anmeldeinformationstyp zum Abrufen von Token für Ihre Anwendung zu verwenden. Überwachen Sie Ihre Produktionspipelines, um sicherzustellen, dass in Coderepositorys niemals irgendwelche Anmeldeinformationen verfügbar sind. Bei Verwendung von Azure wird dringend empfohlen, Managed Identity zu verwenden, damit Anwendungsanmeldeinformationen automatisch verwaltet werden. Weitere Informationen finden Sie in der [Dokumentation zu verwalteten Identitäten](../managed-identities-azure-resources/overview.md). [Credential Scanner](../../security/develop/security-code-analysis-overview.md#credential-scanner) ist ein statisches Analysetool, mit dem Sie Anmeldeinformationen (und andere vertrauliche Inhalte) im Quellcode und in der Buildausgabe erkennen können.

![Zertifikate und Geheimnisse im Azure-Portal](media/active-directory-application-registration-best-practices/credentials.png)

| Empfohlen                                                                     | Nicht empfohlen                             |
| ---------------------------------------------------------------------- | --------------------------------- |
| Verwendung von [Zertifikatanmeldeinformationen](./active-directory-certificate-credentials.md)              | Verwenden von Kennwortanmeldeinformationen          |
| Verwenden von Key Vault mit [verwalteten Identitäten](../managed-identities-azure-resources/overview.md) | App-übergreifende Freigabe von Anmeldeinformationen     |
| Häufiges Rollover                                                    | Verwendung vieler Anmeldeinformationen für eine App  |
|     -----                                                              | Verfügbarkeit veralteter Anmeldeinformationen |
|     -----                                                              | Verfügbarkeit von Anmeldeinformationen im Code        |

## <a name="appid-uri-configuration"></a>AppId-URI-Konfiguration

Bestimmte Anwendungen können Ressourcen (über WebAPI) verfügbar machen und müssen daher einen AppId-URI definieren, der die Ressource in einem Mandanten eindeutig identifiziert. Es wird empfohlen, API oder HTTPS als URI-Schema zu verwenden und den AppId-URI in den folgenden Formaten festzulegen, um URI-Konflikte in Ihrer Organisation zu vermeiden.
Der AppId-URI dient als Präfix für die Bereiche, auf die im API-Code verwiesen wird, und muss eine verifizierte kundeneigene Domäne verwenden. Bei mandantenfähigen Anwendungen muss der Wert auch global eindeutig sein.

[!INCLUDE [active-directory-identifierUri](../../../includes/active-directory-identifier-uri-patterns.md)]

![Anwendungs-ID-URI](media/active-directory-application-registration-best-practices/app-id-uri.png)

### <a name="appid-uri-summary"></a>AppId-URI – Zusammenfassung

| Empfohlen                                           | Nicht empfohlen                  |
| -------------------------------------------- | ---------------------- |
| Vermeiden von Konflikten durch Verwendung von gültigen URI-Formaten | Verwenden von AppId-URIs mit Platzhaltern |
| Verwenden einer überprüften Domäne in branchenspezifischen Apps | Fehlerhaft formatierter URI    |
| Inventarisieren Ihrer AppId-URIs                    |      -----             |
| Verwenden Sie den AppId-URI, um WebApi in Ihrer Organisation zu veröffentlichen.| Verwenden Sie nicht den AppId-URI, um die Anwendung zu identifizieren, verwenden Sie stattdessen die appId-Eigenschaft.|

## <a name="app-ownership-configuration"></a>Konfiguration des App-Besitzes

Stellen Sie sicher, dass der App-Besitz auf eine minimale Gruppe von Personen innerhalb der Organisation festgelegt ist. Es wird empfohlen, die Liste der Besitzer alle paar Monate durchzugehen, um sicherzustellen, dass Besitzer weiterhin Teil der Organisation sind und ihre Stellung im Unternehmen den Besitz der Anwendungsregistrierung rechtfertigt. Weitere Informationen finden Sie unter [Azure AD-Zugriffsüberprüfungen](../governance/access-reviews-overview.md).

![Benutzerbereitstellungsdienst – Besitzer](media/active-directory-application-registration-best-practices/app-ownership.png)

### <a name="app-ownership-summary"></a>App-Besitz – Zusammenfassung

| Empfohlen                  | Nicht empfohlen |
| ------------------- | ----- |
| Kleine Anzahl von Besitzern       | ----- |
| Überwachen der Liste der Besitzer | ----- |

## <a name="checklist"></a>Checkliste

App-Entwickler können die im Azure-Portal verfügbare _Checkliste_ verwenden, um sicherzustellen, dass ihre App-Registrierung eine hohe Qualität aufweist und Richtlinien zur sicheren Integration umfasst. Der Integrations-Assistent hebt bewährte Methoden und Empfehlungen hervor, die dazu beitragen, häufige unabsichtliche Fehler bei der Integration in Microsoft Identity Platform zu vermeiden.

![Checkliste des Integrations-Assistenten im Azure-Portal](media/active-directory-application-registration-best-practices/checklist.png)

### <a name="checklist-summary"></a>Checkliste – Zusammenfassung

| Empfohlen                                                 | Nicht empfohlen |
| -------------------------------------------------- | ----- |
| Verwenden der Checkliste zum Ermitteln szenariobasierter Empfehlungen | ----- |
| Deep-Linking zu App-Registrierungsblades             | ----- |


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Auth-Codeflow finden Sie unter [OAuth 2.0-Autorisierungscodeflow](./v2-oauth2-auth-code-flow.md).
