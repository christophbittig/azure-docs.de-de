---
title: Planen Ihrer Überprüfungslösung für Azure Active Directory-Nachweise (Vorschau)
description: Grundlegende Informationen zum Planen und Entwerfen Ihrer Überprüfungslösung
documentationCenter: ''
author: barbaraselden
manager: martinco
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 07/20/2021
ms.author: baselden
ms.custom: references_regions
ms.openlocfilehash: 36eb4676c63a539004aba9c000702ba964f05d3c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132332419"
---
# <a name="plan-your-azure-active-directory-verifiable-credentials--verification-solution-preview"></a>Planen einer Überprüfungslösung für Azure Active Directory-Nachweise (Vorschau)

>[!IMPORTANT]
> Nachweise für Azure Active Directory befinden sich derzeit in der öffentlichen Vorschauphase. Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mit dem Dienst „Azure Active Directory-Nachweise“ (Azure AD VC) von Microsoft können Sie den Nachweis der Benutzeridentität vertrauen, ohne Ihre Vertrauensgrenze zu erweitern, indem Sie Konten oder einen Verbund mit einem anderen Identitätsanbieter erstellen. Durch die Verwendung von Nachweisen (überprüfbaren Anmeldeinformationen), die auf einem offenen Standard basieren, ermöglicht ein Überprüfungsaustausch Anwendungen, Anmeldeinformationen anzufordern, die nicht an eine bestimmte Domäne gebunden sind. Dies erleichtert das Anfordern und Überprüfen von Anmeldeinformationen im großen Stil.

Sie sollten sich, falls noch nicht geschehen, die [Übersicht zur Azure AD-Nachweise-Architektur](introduction-to-verifiable-credentials-architecture.md) anschauen. Sie können sich auch die Informationen unter [Planen ihrer Lösung zur Ausstellung von Azure AD-Nachweisen](plan-issuance-solution.md) durchlesen.

## <a name="scope-of-guidance"></a>Umfang der Leitfäden

Dieser Inhalt behandelt die technischen Aspekte der Planung einer Lösung zur Überprüfung von Nachweisen, die Microsoft-Produkte und -Dienste verwendet. Die Lösung hat eine Schnittstelle zum Identity Overlay Network (ION), das als dezentrale Public Key Infrastructure (DPKI) fungiert. 

Unterstützende Technologien, die nicht spezifisch für Überprüfungslösungen sind, gehen über den Umfang dieses Leitfadens hinaus. Websites werden beispielsweise in einer Lösung zur Überprüfung von Nachweisen verwendet, aber die Planung einer Websitebereitstellung wird nicht ausführlich behandelt.

Beim Planen Ihrer Überprüfungslösung müssen Sie berücksichtigen, welche Geschäftsfunktionen hinzugefügt oder geändert werden und welche IT-Funktionen genutzt werden können oder hinzugefügt werden müssen, um die Lösung zu erstellen. Sie müssen auch berücksichtigen, welche Schulungen für die am Geschäftsprozess beteiligten Personen sowie für die Personen erforderlich sind, die die Endbenutzer und Mitarbeiter der Lösung unterstützen. Diese Themen werden hier nicht behandelt. Wir empfehlen die Lektüre von [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/) für Informationen zu diesen Themen.

## <a name="components-of-the-solution"></a>Komponenten der Lösung

Im Rahmen Ihrer Planung für eine Überprüfungslösung müssen Sie die Interaktionen zwischen Überprüfer, Antragsteller und Aussteller aktivieren. In diesem Artikel werden die Begriffe vertrauende Partei und Überprüfer synonym verwendet. Das folgende Diagramm zeigt die Komponenten Ihrer Überprüfungsarchitektur.

![Komponenten einer Überprüfungslösung](media/plan-verification-solution/verification-solution-architecture.png)


### <a name="azure-ad-verifiable-credentials-service"></a>Azure AD-Nachweisdienst

Im Kontext einer Prüflösung ist der Azure AD-Nachweisdienst die Schnittstelle zwischen den Microsoft-Komponenten der Lösung und ION. Der Dienst stellt dem Schlüsseltresor den Schlüsselsatz bereit, gibt den dezentralisierten Bezeichner (DID) an und schreibt das DID-Dokument in ION, wo es von Antragstellern und Ausstellern verwendet werden kann. 

### <a name="azure-active-directory-tenant"></a>Azure Active Directory-Mandant 

Der Dienst erfordert einen Azure AD-Mandanten, der eine Steuerungsebene für die Identitäts- und Zugriffsverwaltung (Identity and Access Management, IAM) für die Azure-Ressourcen bereitstellt, die Teil der Lösung sind. Es gibt eine einzelne Instanz des Azure AD-Nachweis-Diensts innerhalb eines Mandanten und gibt ein einzelnes DID-Dokument aus, das den Überprüfer darstellt. Wenn Ihr Überprüfungsdienst von mehreren vertrauenden Seiten verwendet wird, verwenden alle denselben Überprüfer-DID. Der Überprüfer-DID stellt Zeiger auf den öffentlichen Schlüssel bereit, mit denen Antragsteller und Aussteller Nachrichten überprüfen können, die von der vertrauenden Partei stammen.

### <a name="azure-key-vault"></a>Azure Key Vault

![Azure Key Vault](./media/plan-verification-solution/verification-solution-key-vault.png)

Der Azure Key Vault-Dienst speichert Ihre Prüfschlüssel, die generiert werden, wenn Sie den Dienst zur Ausstellung von Azure AD-Nachweisen aktivieren. Die Schlüssel werden verwendet, um Nachrichtensicherheit zu gewährleisten. Jeder Überprüfer verfügt über einen einzelnen Schlüsselsatz, der zum Signieren, Aktualisieren und Wiederherstellen von Nachweisen verwendet wird. Dieser Schlüsselsatz wird jedes Mal verwendet, wenn Sie eine Überprüfungsanforderung bedienen. Der Microsoft-Schlüsselsatz verwendet derzeit ECC (Elliptic Curve Cryptography) [SECP256k1](https://en.bitcoin.it/wiki/Secp256k1). Wir untersuchen andere kryptografische Signaturschemas, die von der umfassenderen DID-Community übernommen werden.

### <a name="azure-ad-vc-apis-and-sdks"></a>Azure AD VC-APIs und SDKs

![Azure AD VC-APIs und SDKs](./media/plan-verification-solution/verification-solution-tools.png)

Anwendungsprogrammierschnittstellen (APIs) und ein Software Developer Kit (SDK) bieten Entwicklern eine Methode zum Abstrahieren von Interaktionen zwischen Komponenten der Lösung, um Überprüfungsvorgänge auszuführen. 

### <a name="ion"></a>ION 

![Azure AD VC ION](./media/plan-verification-solution/verification-solution-ion.png)

Nachweis-Lösungen verwenden ein dezentralisiertes Ledgersystem, um Transaktionen zu erfassen. Microsoft verwendet das [Identity Overlay Network (ION)](https://identity.foundation/ion/), [ein Sidetree-basiertes Netzwerk](https://identity.foundation/sidetree/spec/), das Bitcoin als Blockchain-ähnlichen Ledger für die Implementierung dezentraler Bezeichner (DID) verwendet. Das DID-Dokument des Ausstellers wird in ION gespeichert und von den Parteien der Transaktion verwendet, um kryptografische Signaturprüfungen durchzuführen.

### <a name="microsoft-authenticator-application"></a>Microsoft Authenticator-Anwendung

![Microsoft Authenticator-Anwendung](media/plan-verification-solution/verification-solution-authenticator.png)

Microsoft Authenticator ist die mobile Anwendung, die die Interaktionen zwischen der vertrauenden Partei, dem Benutzer, dem Dienst zur Ausstellung von Azure AD-Nachweisen und den Abhängigkeiten orchestriert, die im Vertrag zum Ausstellen von Nachweisen beschrieben sind. Sie fungiert als digitales Wallet, in dem der Besitzer des Nachweises diesen speichert. Sie ist auch der Mechanismus, der verwendet wird, um Nachweise für die Überprüfung zu präsentieren.

### <a name="relying-party-rp"></a>Vertrauende Seite 

![Komponenten der vertrauenden Seite](media/plan-verification-solution/verification-solution-relying-party.png)

#### <a name="web-front-end"></a>Web-Front-End

Das Web-Front-End der vertrauenden Seite verwendet die Azure AD VC-APIs oder das SDK, um Nachweise zu überprüfen, indem DeepLinks oder QR-Codes generiert werden, die vom Wallet des Antragstellers verwendet werden. Je nach Szenario kann das Front-End eine öffentlich zugängliche oder eine interne Website sein, um Endbenutzererfahrungen zu ermöglichen, die eine Überprüfung erfordern. Die Endpunkte, auf die das Wallet zutritt, müssen jedoch öffentlich zugänglich sein. Insbesondere wird die Umleitung an das Wallet mit bestimmten Anforderungsparametern kontrolliert. Dies wird mithilfe der von Microsoft bereitgestellten APIs und des SDK erreicht.

#### <a name="business-logic"></a>Geschäftslogik 

Sie können neue Logik erstellen oder vorhandene Logik verwenden, die spezifisch für die vertrauende Partei ist, und diese Logik mit der Präsentation von Nachweisen erweitern.

## <a name="scenario-specific-designs"></a>Szenariospezifische Entwürfe

Im Folgenden finden Sie Beispiele für Entwürfe für bestimmte Anwendungsfälle. Das erste Beispiel zeigt das Onboarding von Konten, um Zeit, Kosten und Risiken im Zusammenhang mit dem Onboarding neuer Mitarbeiter zu reduzieren. Die zweite Beispiel veranschaulicht die Kontowiederherstellung, die es einem Endbenutzer ermöglicht, sein Konto mithilfe eines Self-Service-Mechanismus wiederherzustellen oder zu entsperren. Die dritte ist Beispiel beschreibt den Zugriff auf hochwertige Anwendungen und Ressourcen, insbesondere für Business-to-Business-Anwendungsfälle, in denen Personen, die für andere Unternehmen arbeiten, Zugriff erteilt wird. 

### <a name="account-onboarding"></a>Kontenonboarding

Nachweise können auch verwendet werden, um ein schnelleres Onboarding zu ermöglichen, indem sie einige Benutzerinteraktionen ersetzen. Nachweise können verwendet werden, um Mitarbeitern, Studenten, Bürgern oder anderen Personen per Onboarding den Zugriff auf Dienste zu ermöglichen. Anstatt dass ein Mitarbeiter beispielsweise zu einer Zentrale wechseln muss, um einen Mitarbeiterbadge zu aktivieren, kann er seine Identität mithilfe eines Nachweises überprüfen lassen, um einen Badge zu aktivieren, der remote an ihn übermittelt wird. Anstatt einem Bürger einen Code zuzusenden, den er einlösen muss, um auf Behördendienste zu zugreifen, kann er mithilfe eines Nachweises seine Identität nachweisen und Zugriff erhalten. 

![Onboardingszenario für Konten](media/plan-verification-solution/verification-solution-onboarding.png)

#### <a name="additional-elements"></a>Zusätzliche Elemente 

**Onboardingportal**: Dies ist ein Web-Front-End, das die Aufrufe von Azure AD VC-APIs/SDKs zur Präsentation und Validierung von Nachweisen sowie die Logik für das Onboarding von Konten orchestriert.

**Benutzerdefinierte Logik/Workflows**: Spezielle Logik mit organisationsspezifischen Schritten vor und nach dem Aktualisieren des Benutzerkontos. Dies kann Genehmigungsworkflows, zusätzliche Überprüfungen, Protokollierung, Benachrichtigungen usw. umfassen.

**Zielidentitätssysteme**: Organisationsspezifische Identitätsrepositorys, mit denen das Onboardingportal beim Onboarding von Antragstellern interagieren muss. Die zu integrierenden Systeme werden basierend auf den Arten von Identitäten bestimmt, die Sie mit der Nachweisüberprüfung integrieren möchten. Häufige Szenarien zur Identitätsüberprüfung für das Onboarding sind:

* Externe Identitäten, z. B. Anbieter, Partner, Lieferanten und Kunden, die in zentralisierten Identitätssystemen in Azure AD integriert werden, indem APIs zum Ausstellen von B2B-Einladungen (Business-to-Business) oder Berechtigungsverwaltungszuweisungen zu Paketen verwendet werden. 

* Mitarbeiteridentitäten, deren Onboarding in zentralisierten Identitätssystemen bereits über Personalsysteme erfolgt ist. In diesem Fall kann die Identitätsüberprüfung als Teil vorhandener Phasen von Personalworkflows integriert werden. 

#### <a name="design-considerations"></a>Entwurfsaspekte

* **Aussteller**: Das Konto-Onboarding ist eine gute Wahl für einen externen Identitätsnachweisdienst als Aussteller der Nachweise. Beispiele für Überprüfungen für das Onboarding sind: Livetest, Überprüfung durch von der Regierung ausgestellte Dokumente, Nachweis von Adresse oder Telefonnummer usw.

* **Speichern von Nachweisattributen**: Speichern Sie nach Möglichkeit keine Attribute von Nachweisen in Ihrem App-spezifischen Speicher. Seien Sie besonders vorsichtig mit personenbezogenen Daten. Wenn diese Informationen für bestimmte Flows in Ihren Anwendungen erforderlich sind, sollten Sie nach dem Nachweis bitten, um die Ansprüche bei Bedarf abzurufen. 

* **Korrelation von Nachweisattributen mit Back-End-Systemen** Richten Sie beim Definieren der Attribute des Nachweises mit dem Aussteller einen Mechanismus ein, um Informationen im Back-End-System zu korrelieren, nachdem der Benutzer den Nachweis präsentiert hat. Dabei wird in der Regel ein zeitgebundener, eindeutiger Bezeichner im Kontext Ihrer vertrauenden Seite in Kombination mit den empfangenen Ansprüchen verwendet. Einige Beispiele:

   * **Neuer Mitarbeiter**: Wenn der Personalworkflow den Punkt erreicht, an dem Identitätsnachweise erforderlich sind, kann die vertrauende Seite einen Link mit einem zeitgebundenen eindeutigen Bezeichner generieren und an die E-Mail-Adresse des Kandidaten im Personalsystem senden. Dieser eindeutige Bezeichner sollte ausreichen, um Informationen wie Vorname, Nachname aus der Nachweis-Überprüfungsanforderung mit dem Personaldatensatz oder den zugrunde liegenden Daten zu korrelieren. Die Attribute im Nachweis können verwendet werden, um Benutzerattribute im Personalsystem zu vervollständigen oder die Genauigkeit der Benutzerattribute über den Mitarbeiter zu überprüfen.

   * **Externe Identitäten** – Einladung: Wenn ein vorhandener Benutzer in Ihrer Organisation einen externen Benutzer zum Onboarding in das Zielsystem einlädt, kann die vertrauende Seite einen Link mit einem eindeutigen Bezeichner generieren, der die Einladungstransaktion darstellt, und ihn an die E-Mail-Adresse des externen Benutzers senden. Dieser eindeutige Bezeichner sollte ausreichen, um die Nachweis-Überprüfungsanforderung mit dem Einladungsdatensatz oder den zugrunde liegenden Daten zu korrelieren und mit dem Bereitstellungsworkflow fortzufahren. Die Attribute im Nachweis können verwendet werden, um die Attribute externer Benutzer zu überprüfen oder zu vervollständigen.

   * **Externe Identitäten** – Self-Service: Wenn sich externe Identitäten über Self-Service (z. B. eine B2C-Anwendung) beim Zielsystem registrieren, können die Attribute im Nachweis verwendet werden, um die anfänglichen Attribute des Benutzerkontos zu füllen. Anhand der Nachweisattribute kann auch herausgefunden werden, ob ein Profil bereits vorhanden ist.

* **Interaktion mit Zielidentitätssystemen**: Die Dienst-zu-Dienst-Kommunikation zwischen dem Web-Front-End und Ihren Zielidentitätssystemen muss als stark privilegiertes System geschützt werden, da Konten erstellt werden können. Gewähren Sie dem Web-Front-End die Rollen mit den geringsten Berechtigungen. Beispiele hierfür sind:

   * Um einen neuen Benutzer in Azure AD zu erstellen, kann die Website der vertrauenden Seite einen Dienstprinzipal verwenden, dem der MS Graph-Bereich „User.ReadWrite.All“ zum Erstellen von Benutzern und der Bereich „UserAuthenticationMethod.ReadWrite.All“ zum Zurücksetzen der Authentifizierungsmethode gewährt wird.

   * Um Benutzer über B2B-Zusammenarbeit zu Azure AD einzuladen, kann die Website der vertrauenden Seite einen Dienstprinzipal verwenden, dem der MS Graph-Bereich „User.Invite.All“ gewährt wird, um Einladungen zu erstellen.

   * Wenn Ihre vertrauende Seite in Azure ausgeführt wird, verwenden Sie verwaltete Identitäten, um Microsoft Graph aufzurufen. Dadurch werden die Risiken der Verwaltung von Dienstprinzipal-Anmeldeinformationen in Code- oder Konfigurationsdateien beseitigt. Weitere Informationen zu verwalteten Identitäten finden Sie unter [Verwaltete Identitäten für Azure-Ressourcen](../managed-identities-azure-resources/overview.md).

### <a name="accessing-high-value-applications-inside-organizations"></a>Zugriff auf Anwendungen von großem Wert innerhalb von Organisationen 

Nachweise können für den Zugriff auf sensible Anwendungen innerhalb der Organisation als zusätzlicher Beweis verwendet werden. Nachweise können beispielsweise auch verwendet werden, um Mitarbeitern den Zugriff auf Branchenanwendungen basierend auf bestimmten Kriterien wie z. B. einer Zertifizierung zu ermöglichen.

![Zugriff innerhalb der Vertrauensgrenze](media/plan-verification-solution/inside-trust-boundary-access.png)

#### <a name="additional-elements"></a>Zusätzliche Elemente 

**Web-Front-End der vertrauenden Seite**: Dies ist das Web-Front-End der Anwendung, erweitert durch das Azure AD-Nachweis-SDK oder API-Aufrufe zur Vorlage und Validierung von Nachweisen, basierend auf Ihren Geschäftsanforderungen.

**Autorisierungslogik für den Benutzerzugriff**: Logikebene in der Anwendung, die den Benutzerzugriff autorisiert. Sie kann erweitert werden, um anhand der Benutzerattribute innerhalb des Nachweises Autorisierungsentscheidungen zu treffen. 

**Andere Back-End-Dienste und Abhängigkeiten**: Stellt den Rest der Anwendungslogik dar. Diese bleibt durch die Einbeziehung der Identitätsnachweise über Nachweise in der Regel unverändert.

#### <a name="design-considerations"></a>Entwurfsaspekte

* **Ziel**: Das Ziel des Szenarios bestimmt, welche Art von Anmeldeinformationen und Aussteller erforderlich ist. Zu den typischen Szenarien gehören:

   * **Autorisierung**: In diesem Szenario legt der Benutzer den Nachweis vor, damit eine Autorisierungsentscheidung getroffen werden kann. Nachweise, die den Abschluss eines Trainings oder den Erwerb einer bestimmten Zertifizierung nachweisen, sind für dieses Szenario gut geeignet. Die Nachweisattribute sollten detaillierte Informationen enthalten, die Autorisierungsentscheidungen und die Überwachung ermöglichen. Wenn der Nachweis beispielsweise verwendet wird, um zu zertifizieren, dass die Person geschult wurde und auf sensible Finanz-Apps zugreifen darf, kann die App-Logik den Abteilungsanspruch auf eine differenzierte Autorisierung überprüfen und die Mitarbeiter-ID zu Überwachungszwecken verwenden. 

   * **Bestätigung der Identitätsüberprüfung**: In diesem Szenario soll bestätigt werden, dass es sich bei der Person, für die das Onboarding ursprünglich erfolgte, tatsächlich um die Person handelt, die versucht, auf die Anwendung von hohem Wert zuzugreifen. Anmeldeinformationen von einem Aussteller für die Identitätsüberprüfung sind gut geeignet, und die Anwendungslogik sollte überprüfen, ob die Attribute aus dem Nachweis mit dem Benutzer übereinstimmen, der sich bei der Anwendung angemeldet hat. 

* **Sperrung überprüfen**: Wenn Sie Nachweise für den Zugriff auf vertrauliche Ressourcen verwenden, wird häufig der Status des virtuellen Nachweises beim ursprünglichen Aussteller überprüft und der Zugriff für gesperrte Nachweise verweigert. Stellen Sie bei der Arbeit mit den Ausstellern sicher, dass Sie im Rahmen des Entwurfs Ihres Szenarios explizit die Sperrung erläutern. 

* **Benutzererfahrung**: Wenn Sie Nachweise für den Zugriff auf vertrauliche Ressourcen verwenden, können Sie zwei Muster in Betracht ziehen. 

   * **Schrittweise Authentifizierung**: Benutzer starten die Sitzung mit der Anwendung mit vorhandenen Authentifizierungsmechanismen. Benutzer müssen für bestimmte Vorgänge von hohem Wert innerhalb der Anwendung, z. B. Genehmigungen von Geschäftsworkflows, einen Nachweis präsentieren. Dies ist eine gute Möglichkeit für Szenarien, in denen solche Vorgänge von hohem Wert innerhalb der Anwendungsflüsse leicht zu identifizieren und zu aktualisieren sind.

   * **Sitzungseinrichtung**: Benutzer müssen einen Nachweis als Teil der Initiierung der Sitzung bei der Anwendung präsentieren. Diese Option ist gut geeignet, wenn das Wesen der gesamten Anwendung einen hohen Wert hat. 

### <a name="accessing-applications-outside-organization-boundaries"></a>Zugriff auf Anwendungen außerhalb von Organisationsgrenzen

Nachweise können auch von vertrauenden Parteien verwendet werden, die Zugriff oder Vorteile basierend auf der Mitgliedschaft oder dem Angestelltenverhältnis bei einer anderen Organisation gewähren möchten. Ein E-Commerce-Portal kann beispielsweise Vorteile wie Rabatte für Mitarbeiter eines bestimmten Unternehmens, Studenten einer bestimmten Einrichtung usw. bieten. 

Die dezentralisierte Natur von Nachweisen ermöglicht dieses Szenario, ohne Verbundbeziehungen zu erstellen. 

![Zugriff außerhalb der Vertrauensgrenze](media/plan-verification-solution/outside-trust-boundary-access.png)

#### <a name="additional-elements"></a>Zusätzliche Elemente 

**Web-Front-End der vertrauenden Seite**: Dies ist das Web-Front-End der Anwendung, erweitert durch das Azure AD-Nachweis-SDK oder API-Aufrufe zur Vorlage und Validierung von Nachweisen, basierend auf Ihren Geschäftsanforderungen.

**Autorisierungslogik für den Benutzerzugriff**: Logikebene in der Anwendung, die den Benutzerzugriff autorisiert. Sie kann erweitert werden, um anhand der Benutzerattribute innerhalb des Nachweises Autorisierungsentscheidungen zu treffen.

**Andere Back-End-Dienste und Abhängigkeiten**: Stellt den Rest der Anwendungslogik dar. Diese bleibt durch die Einbeziehung der Identitätsnachweise über Nachweise in der Regel unverändert.

#### <a name="design-considerations"></a>Entwurfsaspekte

* **Ziel**: Das Ziel des Szenarios bestimmt, welche Art von Anmeldeinformationen und Aussteller erforderlich ist. Zu den typischen Szenarien gehören:

   * **Authentifizierung**: In diesem Szenario muss ein Benutzer im Besitz eines Nachweises sein, um das Arbeitsverhältnis oder die Beziehung zu bestimmten Organisationen nachzuweisen. In diesem Fall sollte die vertrauende Seite so konfiguriert werden, dass sie von den Zielorganisationen ausgestellte Nachweise akzeptiert. 

   * **Autorisierung**: Basierend auf den Anwendungsanforderungen können die Anwendungen die Nachweisattribute für differenzierte Autorisierungsentscheidungen und die Überwachung nutzen. Wenn eine E-Commerce-Website z. B. Rabatte für Mitarbeiter der Organisationen an einem bestimmten Standort anbietet, können sie dies anhand des Länderanspruchs im Nachweis überprüfen (sofern vorhanden).

* **Sperrung überprüfen**: Wenn Sie Nachweise für den Zugriff auf vertrauliche Ressourcen verwenden, wird häufig der Status des virtuellen Nachweises beim ursprünglichen Aussteller überprüft und der Zugriff für gesperrte Nachweise verweigert. Stellen Sie bei der Arbeit mit den Ausstellern sicher, dass Sie im Rahmen des Entwurfs Ihres Szenarios explizit die Sperrung erläutern. 

* **Benutzererfahrung**: Benutzer können einen Nachweis als Teil der Initiierung der Sitzung bei der Anwendung präsentieren. In der Regel bieten Anwendungen auch eine alternative Methode zum Starten der Sitzung, um Fälle zu unterstützen, in denen Benutzer nicht über Nachweise verfügen. 


### <a name="account-recovery"></a>Kontowiederherstellung

Nachweise können als Ansatz für die Kontowiederherstellung verwendet werden. Wenn ein Benutzer z. B. sein Konto wiederherstellen muss, kann er auf eine Website zugreifen, die erfordert, dass er einen Nachweis vorlegt, und eine Zurücksetzung von Azure AD-Anmeldeinformationen initiieren, indem er MS Graph-APIs aufruft, wie im folgenden Diagramm dargestellt.

Hinweis: Das in diesem Abschnitt beschriebene Szenario bezieht sich zwar speziell auf die Wiederherstellung von Azure AD-Konten, dieser Ansatz kann aber auch zum Wiederherstellen von Konten in anderen Systemen verwendet werden.

![Kontowiederherstellungslösung](media/plan-verification-solution/account-recovery.png)

#### <a name="additional-elements"></a>Zusätzliche Elemente

**Kontoportal**: Dies ist ein Web-Front-End, das die API- oder SDK-Aufrufe für das Vorlegen und Überprüfen des Nachweises orchestriert. Diese Orchestrierung kann Microsoft Graph-Aufrufe zum Wiederherstellen von Konten in Azure AD umfassen.

**Benutzerdefinierte Logik oder Workflows**: Logik mit organisationsspezifischen Schritten vor und nach dem Aktualisieren des Benutzerkontos. Dies kann Genehmigungsworkflows, zusätzliche Überprüfungen, Protokollierung, Benachrichtigungen usw. umfassen.

**Microsoft Graph**: Stellt REST-APIs (Representational State Transfer) und Clientbibliotheken für den Zugriff auf zur Kontowiederherstellung verwendeten Azure AD-Daten zur Verfügung. 

**Azure AD-Unternehmensverzeichnis**: Dies ist der Azure AD-Mandant, der die Konten enthält, die über das Kontoportal erstellt oder aktualisiert werden.

#### <a name="design-considerations"></a>Überlegungen zum Entwurf

**Korrelation von Nachweisattributen mit Azure AD**: Richten Sie beim Definieren der Attribute des Nachweises mit dem Aussteller einen Mechanismus ein, um Informationen bei internen Systemen basierend auf den Ansprüchen im Nachweis und der Benutzereingabe zu korrelieren. Wenn Sie beispielsweise über einen Identitätsüberprüfungsanbieter (Identity Verification Provider, IDV) verfügen, um die Identität vor dem Onboarding von Mitarbeitern zu überprüfen, stellen Sie sicher, dass der ausgestellte Nachweis Ansprüche enthält, die auch in einem internen System wie einem Personalsystem für die Korrelation vorhanden wären. Dies kann eine Telefonnummer, eine Adresse oder ein Geburtsdatum sein. Zusätzlich zu den Ansprüchen in der Nachweisen kann die vertrauende Seite im Rahmen dieses Prozesses einige Informationen wie die letzten vier Ziffern der Sozialversicherungsnummer (SSN) erfragen.

**Rolle von Nachweisen mit vorhandenen Azure AD-Zurücksetzungsfunktionen für Anmeldeinformationen**: Azure AD verfügt über eine integrierte Self-Service-Funktion zur Kennwortzurücksetzung (Self-Service Password Reset, SSPR). Nachweise können verwendet werden, um eine zusätzliche Möglichkeit zur Wiederherstellung bereitzustellen, insbesondere in Fällen, in denen Benutzer nicht mehr auf die SSPR-Methode zugreifen können oder die Kontrolle darüber verloren haben, z. B. wenn sowohl der Computer als auch die mobilen Geräte verloren gegangen sind. In diesem Szenario kann der Benutzer einen Nachweis von einem Identitätsnachweisaussteller erneut abrufen und zur Wiederherstellung des Kontos präsentieren. 

Auf ähnliche Weise können Sie einen Nachweis verwenden, um einen temporären Zugriffspass zu generieren, mit dem Benutzer ihre MFA-Authentifizierungsmethoden ohne Kennwort zurücksetzen können. 

**Autorisierung**: Erstellen Sie einen Autorisierungsmechanismus, z. B. eine Sicherheitsgruppe, die die vertrauende Seite überprüft, bevor sie mit der Wiederherstellung der Anmeldeinformationen fortfahren kann. Beispielsweise können nur Benutzer in bestimmten Gruppen berechtigt sein, ein Konto mit einem Nachweis wiederherzustellen.

**Interaktion mit Azure AD**: Die Dienst-zu-Dienst-Kommunikation zwischen dem Web-Front-End und Azure AD muss als stark privilegiertes System geschützt werden, da die Anmeldeinformationen der Mitarbeiter zurückgesetzt werden können. Gewähren Sie dem Web-Front-End die Rollen mit den geringsten Berechtigungen. Beispiele hierfür sind:

* Gewähren Sie der Website der vertrauenden Seite die Möglichkeit, einen Dienstprinzipal zu verwenden, dem der MS Graph-Bereich „UserAuthenticationMethod.ReadWrite.All“ zum Zurücksetzen von Authentifizierungsmethoden gewährt wurde. Gewähren Sie keine „User.ReadWrite.All“-Berechtigung, die das Erstellen und Löschen von Benutzern ermöglicht.

* Wenn Ihre vertrauende Seite in Azure ausgeführt wird, verwenden Sie verwaltete Identitäten, um Microsoft Graph aufzurufen. Dadurch werden die Risiken im Zusammenhang mit der Verwaltung von Dienstprinzipal-Anmeldeinformationen in Code- oder Konfigurationsdateien beseitigt. Weitere Informationen finden Sie unter [Verwaltete Identitäten für Azure-Ressourcen](../managed-identities-azure-resources/overview.md).

## <a name="plan-for-identity-management"></a>Planen der Identitätsverwaltung 

Im Folgenden finden Sie einige Überlegungen zu IAM bei der Integration von Nachweisen in vertrauende Seiten. Vertrauende Seiten sind in der Regel Anwendungen. 

### <a name="authentication"></a>Authentifizierung

* Der Antragsteller eines Nachweises muss ein Mensch sein. 

* Die Präsentation von Nachweisen muss interaktiv von einem menschlichen Nachweisbesitzer durchgeführt werden, der den Nachweis in seinem Wallet gespeichert hat. Nicht interaktive Flows wie „Im Auftrag von“ werden nicht unterstützt. 

### <a name="authorization"></a>Autorisierung 

* Eine erfolgreiche Präsentation des Nachweises an sich kann als undifferenziertes Autorisierungsgate betrachtet werden. Die Nachweisattribute können auch für differenzierte Autorisierungsentscheidungen verwendet werden.

* Bestimmen Sie, ob ein abgelaufener Nachweis in Ihrer Anwendung eine Bedeutung hat. Wenn ja, überprüfen Sie den Wert des Anspruchs „exp“ (die Ablaufzeit) des Nachweises im Rahmen der Autorisierungsüberprüfungen. Ein Beispiel, bei dem der Ablauf nicht relevant ist, ist die Anforderung, dass ein von der Regierung ausgestelltes Dokument verwendet werden muss, z. B. ein Führerschein, um zu überprüfen, ob der Antragsteller älter als 18 ist. Das Geburtsdatum ist gültig, auch wenn der Nachweis abgelaufen ist. 

* Bestimmen Sie, ob ein widerrufener Nachweis für Ihre Autorisierungsentscheidung eine Bedeutung hat. 

   * Wenn er nicht relevant ist, überspringen Sie den Aufruf der Statusüberprüfungs-API (standardmäßig aktiviert). 

   * Wenn er relevant ist, fügen Sie die ordnungsgemäße Behandlung von Ausnahmen in Ihrer Anwendung hinzu.

### <a name="user-profiles"></a>Benutzerprofile

Sie können Informationen in den dargestellten Nachweisen verwenden, um ein Benutzerprofil zu erstellen. Wenn Sie Attribute nutzen möchten, um ein Profil zu erstellen, beachten Sie Folgendes.

* Wenn der Nachweis ausgegeben wird, enthält er eine Momentaufnahme von Attributen zum Zeitpunkt der Ausstellung. Nachweise können über lange Gültigkeitsdauern verfügen. Sie müssen daher bestimmen, wie alt die Attribute maximal sein dürfen, um sie als Teil des Profils zu verwenden. 

* Wenn der Antragsteller zum Starten einer Sitzung mit der vertrauenden Seite jedes Mal einen Nachweis vorlegen werden muss, sollten Sie in Betracht ziehen, die Ausgabe der Nachweis-Präsentation zu verwenden, um ein nicht persistentes Benutzerprofil mit den Attributen zu erstellen. Dies trägt dazu bei, datenschutzbedingte Risiken im Zusammenhang mit dem Speichern Benutzereigenschaften in ruhenden Daten verringern. Wenn die Attribute des Antragstellers lokal von der Anwendung beibehalten werden müssen, speichern Sie nur den minimalen Satz von Ansprüchen, die für Ihre Anwendung erforderlich sind (und nicht den gesamten Inhalt des Nachweises).

* Wenn die Anwendung einen persistenten Benutzerprofilspeicher erfordert:

   * Erwägen Sie, den Anspruch „sub“ als unveränderlichen Bezeichner des Benutzers zu verwenden. Dies ist ein nicht transparentes eindeutiges Attribut, das für ein bestimmtes Paar aus Antragsteller und vertrauender Seite konstant ist. 

   * Definieren Sie einen Mechanismus zum Deaktivieren der Bereitstellung des Benutzerprofils aus der Anwendung. Aufgrund der dezentralisierten Natur des Azure AD-Systems für Nachweise gibt es keinen Lebenszyklus für die Bereitstellung von Anwendungsbenutzern.

   * Speichern Sie keine persönlichen Datenansprüche, die im Nachweistoken zurückgegeben werden.

   * Speichern Sie nur Ansprüche, die für die Logik der vertrauenden Seite erforderlich sind. 

## <a name="plan-for-performance"></a>Leistungsplanung

Wie bei jeder Lösung müssen Sie Aspekte im Hinblick auf die Leistung planen. Zu den Schwerpunktbereichen gehören Latenz, Durchsatz, Speicher und Skalierbarkeit. In den Anfangsphasen eines Releasezyklus sollte die Leistung kein Problem sein. Wenn die Einführung Ihrer Ausstellungslösung jedoch dazu führt, dass viele Nachweise ausgestellt werden, kann die Leistungsplanung zu einem wichtigen Bestandteil Ihrer Lösung werden.

Im Folgenden werden die bei der Leistungsplanung zu berücksichtigenden Bereiche aufgeführt:

* Dienst zur Ausstellung von Azure AD-Nachweisen wird in den Azure-Regionen „Europa, Westen“, „Europa, Norden“, „USA, Westen 2“ und „USA, Westen-Mitte“ bereitgestellt. Um die Latenz zu begrenzen, stellen Sie Ihr Überprüfungs-Front-End (Website) und Ihren Schlüsseltresor in der oben aufgeführten Region bereit, die dem Ursprung der Anforderungen am nächsten liegt.

* Auf Durchsatz basierendes Modell:

   * Die Nachweisüberprüfungskapazität unterliegt den [Grenzwerten des Azure Key Vault-Diensts](../../key-vault/general/service-limits.md). 

   * Jede Überprüfung eines Nachweises erfordert einen Schlüsseltresors-Signaturvorgang.

   * Die maximale Signierungsleistung eines Schlüsseltresors beträgt 2.000 Signaturen pro ca. 10 Sekunden. Dies bedeutet, dass Ihre Lösung bis zu 12.000-Anforderungen zur Nachweisüberprüfung pro Minute unterstützen kann.

   * Sie können die Drosselung nicht steuern. Es wird jedoch empfohlen, die [Anleitung zur Azure Key Vault-Drosselung](../../key-vault/general/overview-throttling.md) zu lesen, damit Sie verstehen, wie sich die Drosselung auf die Leistung auswirken kann. 

## <a name="plan-for-reliability"></a>Planen der Zuverlässigkeit

Um eine optimale Planung im Hinblick auf Hochverfügbarkeit und Notfallwiederherstellung zu erzielen, empfehlen wir Folgendes:

* Der Azure AD-Nachweisdienst wird in den Azure-Regionen „Europa, Westen“, „Europa, Norden“, „USA, Westen 2“ und „USA, Westen-Mitte“ bereitgestellt. Erwägen Sie die Bereitstellung Ihrer unterstützenden Webserver und Anwendungen in einer dieser Regionen, insbesondere in den Regionen, von denen Sie erwarten, dass der größte Teil Ihres Überprüfungsdatenverkehrs stammt. 

* Überprüfen und integrieren Sie bewährte Methoden aus [Azure Key Vault-Verfügbarkeit und -Redundanz](../../key-vault/general/disaster-recovery-guidance.md) bei der Planung Ihrer Verfügbarkeits- und Redundanzziele.

## <a name="plan-for-security"></a>Planen der Sicherheit

Berücksichtigen Sie beim Entwerfen der Sicherheitsaspekte Folgendes:

* Alle vertrauenden Seiten in einem einzelnen Mandanten verfügen über die gleiche Vertrauensgrenze, da sie den gleichen DID verwenden.

* Definieren Sie einen dedizierten Dienstprinzipal für eine Website, die auf den Schlüsseltresor zugreift.

* Nur der Azure AD-Nachweisdienst und die Websitedienstprinzipale sollten über Berechtigungen verfügen, den Schlüsseltresor zum Signieren von Nachrichten mit dem privaten Schlüssel zu verwenden. 

* Weisen Sie dem Schlüsseltresor keine administrativen Berechtigungen für menschliche Identitäten zu. Weitere Informationen zu bewährten Methoden für den Schlüsseltresor finden Sie unter [Sicherheitsbaseline für Azure Key Vault](../../key-vault/general/security-baseline.md).

* Unter [Schützen von Azure-Umgebungen mit Azure Active Directory](https://azure.microsoft.com/resources/securing-azure-environments-with-azure-active-directory/) finden Sie bewährte Methoden für die Verwaltung der unterstützenden Dienste für Ihre Lösung. 

* Mindern Sie Spoofingrisiken durch:

   * Implementieren der DNS-Überprüfung, um Kunden bei der Identifizierung des Ausstellerbrandings zu unterstützen.

   * Verwenden von Domänen, die für Endbenutzer von Bedeutung sind.

* Minimieren von Risiken verteilter Denial-of-Service-Angriffe (DDoS) und Azure Key Vault-Ressourcendrosselungsrisiken. Jede Anforderung zur Nachweispräsentation generiert Schlüsseltresor-Signierungsvorgänge, die sich auf Dienstgrenzwerte auswirken. Es wird empfohlen, den Datenverkehr zu schützen, indem Sie vor dem Generieren von Ausstellungsanforderungen eine alternative Authentifizierung oder ein Captcha integrieren.

## <a name="plan-for-operations"></a>Planen von Vorgängen

Bei der Planung von Vorgängen empfiehlt es sich, jeden Versuch der Überprüfung von Anmeldeinformationen im Rahmen der Überwachung zu erfassen. Verwenden Sie diese Informationen für die Überwachung und Problembehandlung. Erwägen Sie außerdem, eindeutige Transaktionsbezeichner (IDs) zu generieren, auf die Kunden und Supporttechniker bei Bedarf verweisen können. 

Ziehen Sie im Rahmen Ihrer operativen Planung die Überwachung der folgenden Aspekte in Betracht:

* **Skalierbarkeit**:

   * Überwachen Sie fehlgeschlagene Überprüfungen von Nachweisen als Teil der End-to-End-Sicherheitsmetriken von Anwendungen.

   * Überwachen Sie die End-to-End-Latenz der Überprüfung von Nachweisen.

* **Zuverlässigkeit und Abhängigkeiten**:

   * Überwachen Sie die zugrunde liegenden Abhängigkeiten, die von der Überprüfungslösung verwendet werden. 

   * Folgen Sie [Überwachung und Warnungen für Azure Key Vault](../../key-vault/general/alert.md).

* **Sicherheit**:

   * Aktivieren Sie die Protokollierung für Azure Key Vault, um Signaturvorgänge nachzuverfolgen sowie Konfigurationsänderungen zu überwachen und entsprechende Warnungen zu generieren. Weitere Informationen finden Sie unter [Aktivieren der Azure Key Vault-Protokollierung](../../key-vault/general/howto-logging.md).

   * Archivieren Sie Protokolle in einem SIEM-System (Security Information & Event Management) wie [Microsoft Sentinel](https://azure.microsoft.com/services/azure-sentinel/) zur Langzeitaufbewahrung.

 

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die Architektur von Nachweislösungen.

   * [Übersicht über den Azure AD-Nachweisdienst](introduction-to-verifiable-credentials-architecture.md)

   * [Planen ihrer Lösung zur Ausstellung von Azure AD-Nachweisen](plan-issuance-solution.md)

Implementieren von Nachweisen

   * [Einführung in Azure Active Directory-Nachweise](decentralized-identifier-overview.md) 

   * [Erste Schritte mit Nachweisen](get-started-verifiable-credentials.md)

[Häufig gestellte Fragen (FAQs)](verifiable-credentials-faq.md)
