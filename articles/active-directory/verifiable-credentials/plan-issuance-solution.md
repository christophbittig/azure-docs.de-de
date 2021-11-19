---
title: Planen Ihrer Lösung für die Ausstellung von Azure Active Directory-Nachweisen (Vorschau)
description: Hier erfahren Sie, wie Sie Ihre End-to-End-Ausstellungslösung planen.
documentationCenter: ''
author: barbaraselden
manager: martinco
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 07/20/2021
ms.author: baselden
ms.custom: references_regions
ms.openlocfilehash: fdf6ef5910e31dc21929cf484b2b1ad580badef7
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319718"
---
# <a name="plan-your-azure-active-directory-verifiable-credentials-issuance-solution-preview"></a>Planen Ihrer Lösung für die Ausstellung von Azure Active Directory-Nachweisen (Vorschau)

 >[!IMPORTANT]
> Überprüfbare Anmeldeinformationen für Azure Active Directory-befinden sich derzeit in der öffentlichen Vorschauphase. Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie in den [**zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Es ist wichtig, Ihre Ausstellungslösung so zu planen, dass Sie nicht nur Anmeldeinformationen/Nachweise ausstellen können, sondern auch einen umfassenden Überblick über die Architektur und die geschäftlichen Auswirkungen Ihrer Lösung haben. Wenn Sie dies noch nicht getan haben, empfiehlt es sich, die [Übersicht über die Architektur von Azure Active Directory-Nachweisen](introduction-to-verifiable-credentials-architecture.md) zu lesen. Darin finden Sie grundlegende Informationen dazu.

## <a name="scope-of-guidance"></a>Umfang der Leitfäden

In diesem Artikel werden die technischen Aspekte der Planung einer Lösung für die Ausstellung von Nachweisen beschrieben, bei der Microsoft-Produkte für die Interoperabilität mit dem Identity Overlay Network (ION) verwendet werden. Die Microsoft-Lösung für Nachweise folgt den vom World Wide Web Consortium (W3C) entwickelten Standards [Verifiable Credentials Data Model 1.0](https://www.w3.org/TR/vc-data-model/) und [Decentralized Identifiers (DIDs) v1.0](https://www.w3.org/TR/did-core/), sodass die Interoperabilität mit Nicht-Microsoft-Diensten ermöglicht wird. Die Beispiele in diesem Inhalt spiegeln jedoch das Microsoft-Lösungspaket für Nachweise wider. 

Außerhalb des Rahmens dieses Inhalts fallen Themen, die unterstützende Technologien behandeln, die nicht spezifisch für Ausstellungslösungen sind. So werden beispielsweise Websites in einer Lösung für die Ausstellung von Nachweisen verwendet, aber die Planung einer Websitebereitstellung wird nicht ausführlich behandelt.

## <a name="components-of-the-solution"></a>Komponenten der Lösung

Als Teil Ihres Plans für eine Ausstellungslösung müssen Sie eine Lösung entwerfen, die Interaktionen zwischen dem Aussteller, dem Benutzer und dem Prüfer ermöglicht. Sie können mehrere Nachweise ausstellen. Im folgenden Diagramm sind die Komponenten einer Ausstellungsarchitektur dargestellt.

### <a name="microsoft-vc-issuance-solution-architecture"></a>Architektur der Microsoft-Lösung für die Ausstellung von Nachweisen

![Komponenten einer Ausstellungslösung](media/plan-issuance-solution/plan-issuance-solution-architecture.png)


### <a name="azure-active-directory-tenant"></a>Azure Active Directory-Mandant

Eine Voraussetzung für die Ausführung des Azure AD-Nachweisdiensts ist, dass er in einem Azure Active Directory-Mandanten (Azure AD-Mandanten) gehostet wird. Der Azure AD-Mandant stellt eine Steuerungsebene für die Identitäts- und Zugriffsverwaltung (Identity & Access Management, IAM) für die Azure-Ressourcen bereit, die Teil der Lösung sind.

Jeder Mandant verfügt über eine einzelne Instanz des Azure AD-Nachweisdiensts und einen dezentralisierten Bezeichner (Decentralized Identifier, DID). Der DID ist der Nachweis dafür, dass der Aussteller der Eigentümer der im DID enthaltenen Domäne ist. Der DID wird vom Antragsteller und vom Prüfer zum Überprüfen des Ausstellers verwendet. 

### <a name="microsoft-azure-services"></a>Microsoft Azure-Dienste

![Komponenten einer Ausstellungslösung mit Schwerpunkt auf Azure-Diensten](media/plan-issuance-solution/plan-issuance-solution-azure-services.png)

Der Dienst **Azure Key Vault** speichert Ihre Ausstellerschlüssel, die generiert werden, wenn Sie den Dienst zur Ausstellung von Azure AD-Nachweisen initiieren. Die Schlüssel und Metadaten werden verwendet, um Vorgänge zur Verwaltung von Anmeldeinformationen/Nachweisen auszuführen und Nachrichtensicherheit bereitzustellen.

Jeder Aussteller verfügt über einen Schlüsselsatz, der zum Signieren, Aktualisieren und Wiederherstellen verwendet wird. Dieser Schlüsselsatz wird für jede Ausstellung der von Ihnen erstellten Nachweise verwendet. 

**Azure Storage** wird zum Speichern von Metadaten und Definitionen von Anmeldeinformationen/Nachweisen verwendet, insbesondere der Regeln und Anzeigedateien für Ihre Nachweise. 

* Anzeigedateien bestimmen, welche Ansprüche im Nachweis gespeichert werden und wie sie im Wallet des Besitzers angezeigt werden. Die Anzeigedatei enthält auch Branding und andere Elemente. Regeldateien sind auf eine Größe von 50 KB beschränkt, während Anzeigedateien auf 150 KB beschränkt sind. Weitere Informationen finden Sie unter [Gewusst wie: Anpassen Ihrer Nachweise (Vorschau)](../verifiable-credentials/credential-design.md).

* Regeln sind ein vom Aussteller definiertes Modell, das die erforderlichen Eingaben für einen Nachweis, die vertrauenswürdigen Quellen der Eingaben und die Zuordnung von Eingabeansprüchen zu Ausgabeansprüchen beschreibt. 

   * **Eingabe**: Dabei handelt es sich um eine Teilmenge des Modells in der Regeldatei für die Clientnutzung. Die Teilmenge muss den Eingabesatz, die Quelle zum Abrufen der Eingaben und den aufzurufenden Endpunkt zum Abrufen eines Nachweises beschreiben.

* Regeln und Anzeigedateien für verschiedene Anmeldeinformationen/Nachweise können für die Verwendung verschiedener Container, Abonnements und Speicher konfiguriert werden. Sie können beispielsweise Berechtigungen an verschiedene Teams delegieren, die für die Verwaltung bestimmter Nachweise zuständig sind. 

### <a name="azure-ad-verifiable-credentials-service"></a>Azure AD-Nachweisdienst

![Microsoft Azure AD-Nachweisdienst](media/plan-issuance-solution/plan-issuance-azure-active-directory-verifiable-credential-services.png)

Mit dem Azure AD-Nachweisdienst können Sie Nachweise basierend auf Ihrer Konfiguration ausstellen und widerrufen. Der Dienst:

* Stellt den dezentralisierten Bezeichner (Decentralized Identifier, DID) bereit und schreibt das DID-Dokument in ION, wo es von Antragstellern und Prüfern verwendet werden kann. Jeder Aussteller verfügt über einen einzigen DID pro Mandant. 

* Stellt Schlüsselsätze für Key Vault bereit. 

* Speichert die Konfigurationsmetadaten, die vom Ausstellungsdienst und Microsoft Authenticator verwendet werden.

### <a name="ion"></a>ION

![ION](media/plan-issuance-solution/plan-issuance-solution-ion.png)

Microsoft verwendet das [Identity Overlay Network (ION)](https://identity.foundation/ion/), [ein Sidetree-basiertes Netzwerk](https://identity.foundation/sidetree/spec/), das die Bitcoin-Blockchain für die Implementierung dezentralisierter Bezeichner (DIDs) verwendet. Das DID-Dokument des Ausstellers wird in ION gespeichert und von den Parteien der Transaktion zum Durchführen von kryptografischen Signaturprüfungen verwendet.

### <a name="microsoft-authenticator-application"></a>Microsoft Authenticator-Anwendung

![Microsoft Authenticator-Anwendung](media/plan-issuance-solution/plan-issuance-solution-authenticator.png)

Microsoft Authenticator ist die mobile Anwendung, die die Interaktionen zwischen dem Benutzer, dem Azure AD-Nachweisdienst und den Abhängigkeiten orchestriert, die im Vertrag zum Ausstellen von Nachweisen beschrieben sind. Sie fungiert als digitales Wallet, in dem der Besitzer des Nachweises den Nachweis einschließlich des privaten Schlüssels des Antragstellers des Nachweises speichert. Microsoft Authenticator ist auch der Mechanismus, der verwendet wird, um Nachweise für die Überprüfung vorzulegen.

### <a name="issuance-business-logic"></a>Ausstellungsgeschäftslogik 

![Ausstellungsgeschäftslogik](media/plan-issuance-solution/plan-issuance-solution-business-logic.png)

Ihre Ausstellungslösung umfasst ein Web-Front-End, über das Benutzer einen Nachweis anfordern, einen Identitätsspeicher und/oder einen anderen Attributspeicher zum Abrufen von Werten für Ansprüche über den Antragsteller und andere Back-End-Dienste. 

Ein Web-Front-End übermittelt Ausstellungsanforderungen an das Wallet des Antragstellers, indem Deep-Links oder QR-Codes generiert werden. Je nach Konfiguration des Vertrags sind möglicherweise andere Komponenten erforderlich, um die Anforderungen zum Erstellen eines Nachweises zu erfüllen.

Diese Dienste stellen unterstützende Rollen bereit, die nicht unbedingt in das ION oder in den Azure AD-Nachweisdienst integriert werden müssen. Diese Ebene umfasst in der Regel Folgendes:

* **Mit Open ID Connect (OIDC) kompatible Dienste** werden zum Abrufen von ID-Token verwendet, die zum Ausstellen des Nachweises erforderlich sind. Vorhandene Identitätssysteme wie Azure AD oder Azure AD B2C können den OIDC-kompatiblen Dienst bereitstellen, ebenso wie benutzerdefinierte Lösungen wie Identity Server.

* **Attributspeicher**: Diese Speicher können sich außerhalb von Verzeichnisdiensten befinden und stellen Attribute bereit, die zum Ausstellen eines Nachweises erforderlich sind. Beispiel: Ein Studenteninformationssystem kann Ansprüche zu erworbenen Abschlüssen bereitstellen. 

* **Zusätzliche Dienste der mittleren Ebene**, die Geschäftsregeln für Suchvorgänge, Überprüfungen, Abrechnungen und andere Laufzeitüberprüfungen und Workflows enthalten, die zum Ausstellen von Anmeldeinformationen/Nachweisen erforderlich sind.

Weitere Informationen zum Einrichten Ihres Web-Front-Ends finden Sie im Tutorial [Konfigurieren von Azure Active Directory für das Ausstellen von Nachweisen](../verifiable-credentials/enable-your-tenant-verifiable-credentials.md). 

## <a name="credential-design-considerations"></a>Nachweisentwurfsüberlegungen

Der Nachweisentwurf richtet sich nach Ihren spezifischen Anwendungsfällen. Der Anwendungsfall bestimmt Folgendes:

* Die Interoperabilitätsanforderungen

* Die Art und Weise, wie Benutzer ihre Identität nachweisen müssen, um ihren Nachweis zu erhalten

* Die in den Anmeldeinformationen/Nachweisen erforderlichen Ansprüche

* Ob Anmeldeinformationen/Nachweise jemals widerrufen werden müssen

 

### <a name="credential-use-cases"></a>Anwendungsfälle für Nachweise

Im Folgenden werden die gängigsten Anwendungsfälle für Azure AD-Nachweise aufgeführt:

**Identitätsüberprüfung**: Ein Nachweis wird basierend auf mehreren Kriterien ausgestellt. Dazu gehören beispielsweise die Überprüfung der Echtheit von Dokumenten, die von Behörden ausgestellt wurden, wie ein Pass oder ein Führerschein, und die Korrelation der Informationen in diesem Dokument mit anderen Informationen wie:

* ein Selfie eines Benutzers 

* Lebendigkeitsprüfung

Diese Art von Nachweis eignet sich gut für Onboardingszenarien für neue Mitarbeiter, Partner, Dienstanbieter, Studenten und andere Fälle, in denen eine Identitätsüberprüfung von entscheidender Bedeutung ist.

 

![Anwendungsfall: Identitätsüberprüfung](media/plan-issuance-solution/plan-issuance-solution-identity-verification-use-cases.png)

**Nachweis der Anstellung/Mitgliedschaft**: Ein Nachweis wird ausgestellt, um eine Beziehung zwischen dem Benutzer und einer Einrichtung zu bestätigen. Diese Art von Nachweis eignet sich gut für den Zugriff auf lose gekoppelte Business-to-Business-Anwendungen, z. B. Einzelhändler, die Rabatte für Mitarbeiter oder Studenten anbieten. Ein wesentlicher Wert von Nachweisen ist die Portabilität: Nach der Ausstellung kann der Benutzer den Nachweis in vielen Szenarien verwenden. 

![Anwendungsfall: Anstellungsnachweis](media/plan-issuance-solution/plan-issuance-solution-employment-proof-use-cases.png)

Weitere Anwendungsfälle für Nachweise finden Sie unter [Verifiable Credentials Use Cases (w3.org)](https://www.w3.org/TR/vc-use-cases/).

### <a name="credential-interoperability"></a>Interoperabilität von Nachweisen

Untersuchen Sie im Rahmen des Entwurfsprozesses branchenspezifische Schemas, Namespaces und Bezeichner, an denen Sie sich orientieren können, um die Interoperabilität und Nutzung zu maximieren. Beispiele finden Sie auf den Websites [Schema.org](https://schema.org/) und [DIF – Claims and Credentials Working Group](https://identity.foundation/working-groups/claims-credentials.html).

Beachten Sie, dass allgemeine Schemas ein Bereich sind, in dem Standards noch weiterentwickelt werden. Ein Beispiel für diese Bemühungen ist die [Verifiable Credentials for Education Task Force](https://github.com/w3c-ccg/vc-ed). Wir empfehlen Ihnen, neue Standards in der Branche Ihrer Organisation zu untersuchen und zu deren Entwicklung beizutragen.

### <a name="credential-attributes"></a>Nachweisattribute 

Nachdem Sie den Anwendungsfall für einen Nachweis festgelegt haben, müssen Sie entscheiden, welche Attribute der Nachweis enthalten soll. Prüfer können die Ansprüche in den von den Benutzern vorgelegten Nachweisen lesen.

Zusätzlich zu den branchenspezifischen Standards und Schemas, die für Ihre Szenarien gelten könnten, sollten Sie die folgenden Aspekte berücksichtigen:

* **Private Informationen minimieren**: Erfüllen Sie die Anwendungsfälle unter Angabe der Mindestmenge an privaten Informationen, die erforderlich sind. Beispielsweise kann ein Nachweis, der für E-Commerce-Websites verwendet wird, die Rabatte für Mitarbeiter und Ehemalige bieten, erfüllt werden, indem der Nachweis nur mit den Vor- und Nachnamensansprüchen vorgelegt wird. Zusätzliche Informationen wie Einstellungsdatum, Position, Abteilung usw. sind nicht erforderlich.

* **Abstrakte Ansprüche bevorzugen**: Jeder Anspruch sollte die Anforderungen erfüllen und gleichzeitig möglichst wenige Details enthalten. Beispielsweise ist ein Anspruch mit dem Namen „Älter als“ mit diskreten Werten wie „13“, „21“, „60“ abstrakter als ein Geburtsdatumsanspruch.

* **Widerrufbarkeit einplanen**: Wir empfehlen Ihnen, einen Indexanspruch zu definieren, um Mechanismen zum Suchen und Widerrufen von Nachweisen zu ermöglichen. Pro Vertrag kann nur ein Indexanspruch definiert werden. Es ist wichtig zu beachten, dass Werte für indizierte Ansprüche nicht im Back-End gespeichert werden. Es wird nur ein Hash des Anspruchswerts gespeichert. Weitere Informationen finden Sie unter [Widerrufen eines ausgestellten Nachweises](../verifiable-credentials/how-to-issuer-revoke.md).

Weitere Überlegungen zu Nachweisattributen finden Sie in der Spezifikation [Verifiable Credentials Data Model 1.0 (w3.org)](https://www.w3.org/TR/vc-data-model/).

## <a name="plan-quality-attributes"></a>Planen von Qualitätsattributen

### <a name="plan-for-performance"></a>Planen der Leistung

Wie bei jeder Lösung müssen Sie Aspekte im Hinblick auf die Leistung planen. Die wichtigsten Bereiche, auf die Sie sich konzentrieren sollten, sind Latenz, Durchsatz, Speicher und Skalierbarkeit. In den Anfangsphasen eines Releasezyklus sollte die Leistung kein Problem sein. Wenn die Einführung Ihrer Ausstellungslösung jedoch dazu führt, dass viele Nachweise ausgestellt werden, kann die Leistungsplanung zu einem wichtigen Bestandteil Ihrer Lösung werden.

Im Folgenden werden die bei der Leistungsplanung zu berücksichtigenden Bereiche aufgeführt:

* Der Dienst zur Ausstellung von Azure AD-Nachweisen wird in den Azure-Regionen „Europa, Westen“, „Europa, Norden“, „USA, Westen 2“ und „USA, Westen-Mitte“ bereitgestellt. Sie wählen keine Region aus, in der der Dienst bereitgestellt werden soll. 

* Um die Latenz zu begrenzen, stellen Sie Ihre Front-End-Ausstellungswebsite, den Schlüsseltresor und den Speicher in der oben aufgeführten Region bereit, die dem erwarteten Ursprungsort der Anforderungen am nächsten ist.

Auf Durchsatz basierendes Modell:
* Für den Ausstellerdienst gelten die [Grenzwerte des Azure Key Vault-Diensts](../../key-vault/general/service-limits.md). 

*  Für Azure Key Vault sind drei Signaturvorgänge an jeder Nachweisausstellung beteiligt:

      * Einer für die Ausstellungsanforderung von der Website

      * Einer für den erstellten Nachweis

      * Einer für den Vertragsdownload

* Die maximale Signierungsleistung eines Schlüsseltresors beträgt 2.000 Signaturvorgänge pro ca. 10 Sekunden. Das sind etwa 12.000 Signaturvorgänge pro Minute. Dies bedeutet, dass Ihre Lösung bis zu 4.000 Nachweisausstellungen pro Minute unterstützen kann.

* Sie können die Drosselung nicht steuern. Wir empfehlen Ihnen jedoch, die [Anleitung zur Drosselung von Azure Key Vault](../../key-vault/general/overview-throttling.md) zu lesen. 

* Wenn Sie eine sehr große Anzahl von Nachweisen einführen und integrieren möchten, sollten Sie die Batcherstellung in Betracht ziehen, um sicherzustellen, dass Sie die Grenzwerte nicht überschreiten.

* Für den Ausstellungsdienst gelten Azure-Speichergrenzwerte. In typischen Anwendungsfällen sollte der Speicher kein Problem darstellen. Wenn Sie jedoch vermuten, dass Sie die Speichergrenzwerte überschreiten könnten oder der Speicher ein Engpass sein könnte, sollten Sie Folgendes berücksichtigen: 

   * Wir empfehlen Ihnen, den Artikel [Skalierbarkeits- und Leistungsziele für Blob Storage](../../storage/blobs/scalability-targets.md) im Rahmen Ihres Planungsprozesses zu lesen. Der Dienst zur Ausstellung von Azure AD-Nachweisen liest Regeln und zeigt Dateien an, und die Ergebnisse werden vom Dienst zwischengespeichert.

   * Außerdem empfiehlt es sich, den Artikel [Checkliste zu Leistung und Skalierbarkeit für Blob Storage: Azure Storage](../../storage/blobs/storage-performance-checklist.md) zu lesen. 

Legen Sie im Rahmen Ihrer Leistungsplanung fest, was überwacht werden soll, um die Leistung der Lösung besser zu verstehen. Berücksichtigen Sie zusätzlich zur Websiteüberwachung auf Anwendungsebene Folgendes, wenn Sie Ihre Strategie zur Überwachung der Nachweisausstellung definieren:

Ziehen Sie in Bezug auf die Skalierbarkeit die Implementierung von Metriken für Folgendes in Betracht:

   * Definieren Sie die logischen Phasen Ihres Ausstellungsprozesses. Zum Beispiel:

   * Erste Anforderung 

   * Verwaltung des QR-Codes oder Deep-Links

   * Attributsuche

   * Aufrufe des Diensts zur Ausstellung von Azure AD-Nachweisen

   * Ausgestellte Nachweise

   * Definieren Sie die Metriken basierend auf den Phasen:

      * Gesamtanzahl der Anforderungen (Menge)

      * Anforderungen pro Zeiteinheit (Durchsatz)

      * Zeitaufwand (Latenz)

* Überwachen Sie Azure Key Vault und Azure Storage gemäß den folgenden Anleitungen:

   * [Überwachung und Warnungen für Azure Key Vault](../../key-vault/general/alert.md)

   * [Überwachen von Azure Blob Storage](../../storage/blobs/monitor-blob-storage.md)

* Überwachen Sie die für Ihre Geschäftslogikebene verwendeten Komponenten. 

### <a name="plan-for-reliability"></a>Planen der Zuverlässigkeit

Zur Planung der Zuverlässigkeit empfehlen wir Folgendes:

* Nachdem Sie Ihre Verfügbarkeits- und Redundanzziele definiert haben, lesen Sie die folgenden Leitfäden, um zu verstehen, wie Sie Ihre Ziele erreichen können:

   * [Azure Key Vault: Verfügbarkeit und Redundanz – Azure Key Vault](../../key-vault/general/disaster-recovery-guidance.md)

   * [Notfallwiederherstellung und Speicherkontofailover – Azure Storage](../../storage/common/storage-disaster-recovery-guidance.md)

* Für die Front-End- und Geschäftsebene kann sich Ihre Lösung mit einer unbegrenzten Anzahl von Möglichkeiten manifestieren. Wie bei jeder Lösung müssen Sie für die von Ihnen identifizierten Abhängigkeiten sicherstellen, dass die Abhängigkeiten resilient sind und überwacht werden. 

Wenn der seltene Fall eintritt, dass der Dienst zur Ausstellung von Azure AD-Nachweisen, Azure Key Vault oder Azure Storage ausfällt, ist die gesamte Lösung nicht mehr verfügbar.

### <a name="plan-for-compliance"></a>Planen der Compliance

Für Ihre Organisation können bestimmte Complianceanforderungen in Bezug auf Ihre Branche, die Art der Transaktionen oder das Land gelten, in dem Ihre Organisation tätig ist. 

**Datenresidenz**: Der Dienst zur Ausstellung von Azure AD-Nachweisen wird nur in einigen Azure-Regionen bereitgestellt. Der Dienst wird nur für Computefunktionen verwendet. Werte von Nachweisen werden in Microsoft-Systemen nicht gespeichert. Im Rahmen des Ausstellungsprozesses werden jedoch personenbezogene Daten gesendet und beim Ausstellen von Nachweisen verwendet. Die Verwendung des Nachweisdiensts sollte keine Auswirkungen auf die Anforderungen an die Datenresidenz (den Datenaufbewahrungsort) haben. Wenn Sie im Rahmen der Identitätsüberprüfung persönliche Informationen speichern, sollten diese in einer Art und Weise und in einer Region gespeichert werden, die Ihren Complianceanforderungen entspricht. Azure-bezogene Anleitungen finden Sie auf der Microsoft Trust Center-Website. 

**Widerrufen von Nachweisen**: Legen Sie fest, ob Ihre Organisation Nachweise widerrufen muss. Beispielsweise muss ein Administrator möglicherweise Nachweise widerrufen, wenn ein Mitarbeiter das Unternehmen verlässt. Oder wenn ein Nachweis für einen Führerschein ausgestellt wird und der Inhaber bei einer Handlung erwischt wird, die den Entzug des Führerscheins zur Folge hätte, muss der Nachweis möglicherweise widerrufen werden. Weitere Informationen finden Sie unter [Widerrufen eines ausgestellten Nachweises](how-to-issuer-revoke.md).

**Ablaufende Nachweise**: Legen Sie fest, ob und unter welchen Umständen Nachweise ablaufen sollen. Wenn Sie beispielsweise einen Nachweis als Bestätigung für den Besitz eines Führerscheins ausstellen, kann dieser nach einigen Jahren ablaufen. Wenn Sie einen Nachweis als Überprüfung einer Zuordnung zu einem Benutzer ausstellen, sollten Sie ihn jährlich ablaufen lassen, um sicherzustellen, dass die Benutzer jährlich die aktualisierte Version des Nachweises abrufen müssen.

## <a name="plan-for-operations"></a>Planen von Vorgängen

Beim Planen von Vorgängen ist es wichtig, dass Sie ein Schema entwickeln, das für die Problembehandlung, Berichterstellung und Unterscheidung verschiedener Kunden verwendet werden kann, die Sie unterstützen. Darüber hinaus muss dieser Prozess definiert werden, wenn das Betriebsteam für die Ausführung des Nachweiswiderrufs zuständig ist. Jeder Schritt des Prozesses sollte korreliert werden, damit Sie bestimmen können, welche Protokolleinträge jeder eindeutigen Ausstellungsanforderung zugeordnet werden können. Für die Überwachung wird empfohlen, jeden Versuch der Ausstellung von Nachweisen einzeln zu erfassen. Dies gilt insbesondere in folgenden Fällen:

* Generieren Sie eindeutige Transaktions-IDs, auf die Kunden und Supporttechniker bei Bedarf verweisen können.

* Entwickeln Sie einen Mechanismus, um die Protokolle von Azure Key Vault-Transaktionen mit den Transaktions-IDs des Ausstellungsteils der Lösung zu korrelieren.

* Wenn Sie Anbieter eines Identitätsüberprüfungsdiensts sind, der Nachweise im Auftrag mehrerer Kunden ausstellt, sollten Sie die Überwachung und Problembehandlung/Risikominimierung nach Kunden- oder Vertrags-ID für die kundenorientierte Berichterstellung und Abrechnung durchführen.

* Wenn Sie Anbieter eines Identitätsüberprüfungsdiensts sind, der Nachweise im Auftrag mehrerer Kunden ausstellt, verwenden Sie die Kunden- oder Vertrags-ID für die kundenorientierte Berichterstellung und Abrechnung, Überwachung und Problembehandlung/Risikominimierung. 

## <a name="plan-for-security"></a>Planen der Sicherheit

Im Rahmen Ihrer Entwurfsüberlegungen, die sich auf die Sicherheit konzentrieren, empfehlen wir Folgendes:

* Für die Schlüsselverwaltung:

   * Erstellen Sie einen dedizierten Schlüsseltresor für die Nachweisausstellung. Beschränken Sie die Azure Key Vault-Berechtigungen auf den Dienst zur Ausstellung von Azure AD-Nachweisen und den Dienstprinzipal der Front-End-Website des Ausstellungsdiensts. 

   * Behandeln Sie Azure Key Vault als System mit hohen Berechtigungen. Azure Key Vault stellt Nachweise für Kunden aus. Wir raten davon ab, menschlichen Identitäten ständige Berechtigungen für den Azure Key Vault-Dienst zuzuweisen. Administratoren sollten nur über Just-in-Time-Zugriff auf Key Vault verfügen. Weitere bewährte Methoden für die Verwendung von Azure Key Vault finden Sie unter [Azure-Sicherheitsbaseline für Key Vault](/security/benchmark/azure/baselines/key-vault-security-baseline).

* Für den Dienstprinzipal, der die Front-End-Ausstellungswebsite darstellt:

   * Definieren Sie einen dedizierten Dienstprinzipal, um den Zugriff auf Azure Key Vault zu autorisieren. Wenn Ihre Website in Azure gehostet wird, empfiehlt es sich, eine [verwaltete Azure-Identität](../managed-identities-azure-resources/overview.md) zu verwenden. 

   * Behandeln Sie den Dienstprinzipal, der die Website darstellt, und den Benutzer als eine einzige Vertrauensstellungsgrenze. Es ist zwar möglich, mehrere Websites zu erstellen, es gibt jedoch nur einen Schlüsselsatz für die Ausstellungslösung. 

Für die Sicherheitsprotokollierung und -überwachung empfehlen wir Folgendes:

* Aktivieren Sie die Protokollierung und Warnungen für Azure Key Vault, um Nachweisausstellungsvorgänge, Schlüsselextraktionsversuche und Berechtigungsänderungen nachzuverfolgen und Konfigurationsänderungen zu überwachen und entsprechende Warnungen zu senden. Weitere Informationen finden Sie unter [Aktivieren der Protokollierung in Key Vault](../../key-vault/general/howto-logging.md). 

* Aktivieren Sie die Protokollierung Ihres Azure Storage-Kontos, um Konfigurationsänderungen zu überwachen und entsprechende Warnungen zu senden. Weitere Informationen finden Sie unter [Überwachen von Azure Blob Storage](../../storage/blobs/monitor-blob-storage.md).

* Archivieren Sie Protokolle in einem SIEM-System (Security Information & Event Management) wie [Microsoft Sentinel](https://azure.microsoft.com/services/azure-sentinel) zur Langzeitaufbewahrung.

* Minimieren von Spoofingrisiken mithilfe der folgenden Möglichkeiten

   * DNS-Überprüfung, um Kunden bei der Identifizierung des Ausstellerbrandings zu unterstützen.

   * Für Endbenutzer sinnvolle/aussagekräftige Domänennamen.

   * Vertrauenswürdiges Branding, das der Endbenutzer erkennt.

* Minimieren Sie Risiken verteilter Denial-of-Service-Angriffe (Distributed Denial of Service, DDoS) und Azure Key Vault-Ressourcenauslastungsrisiken. Jede Anforderung, die eine Nachweisausstellungsanforderung auslöst, generiert Key Vault-Signaturvorgänge, die sich auf Dienstgrenzwerte auswirken. Es wird empfohlen, den Datenverkehr zu schützen, indem Sie vor dem Generieren von Ausstellungsanforderungen eine Authentifizierung oder ein Captcha integrieren.

Informationen zum Verwalten Ihrer Azure-Umgebung finden Sie unter [Azure-Sicherheitsvergleichstest](/security/benchmark/azure/) und [Schützen von Azure-Umgebungen mit Azure Active Directory](https://aka.ms/AzureADSecuredAzure). Diese Leitfäden enthalten bewährte Methoden für die Verwaltung der zugrunde liegenden Azure-Ressourcen, einschließlich Azure Key Vault, Azure Storage, Websites und anderer Azure-bezogener Dienste und Funktionen.

## <a name="additional-considerations"></a>Weitere Überlegungen

Wenn Sie Ihren POC abschließen, sammeln Sie alle generierten Informationen und Dokumentationen, und erwägen Sie, die Ausstellerkonfiguration zu beenden. Dadurch können Sie vermeiden, dass nach Ablauf Ihres POC-Zeitrahmens Nachweise ausgestellt werden. 

Weitere Informationen zur Implementierung und Verwendung von Azure Key Vault finden Sie unter [Bewährte Methoden zum Verwenden von Key Vault](../../key-vault/general/best-practices.md). Weitere Informationen dazu, wie Sie Azure-Umgebungen mit Active Directory schützen können, finden Sie unter [Schützen von Azure-Umgebungen mit Azure Active Directory](https://aka.ms/AzureADSecuredAzure). 

## <a name="next-steps"></a>Nächste Schritte

[Lesen der Übersicht über die Architektur](introduction-to-verifiable-credentials-architecture.md)

[Planen Ihrer Überprüfungslösung](plan-verification-solution.md)

[Erste Schritte: Nachweise](get-started-verifiable-credentials.md)
