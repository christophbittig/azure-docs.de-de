---
title: Migrieren der Anwendungsauthentifizierung zu Azure Active Directory
description: In diesem Whitepaper werden die Planung und die Vorteile einer Migration Ihrer Anwendungsauthentifizierung zu Azure AD ausführlich erläutert.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 02/05/2021
ms.author: davidmu
ms.reviewer: alamaral
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f8ee443f12ce4c28e5bb883be686a4610eeec38
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122768904"
---
# <a name="migrate-application-authentication-to-azure-active-directory"></a>Migrieren der Anwendungsauthentifizierung zu Azure Active Directory

## <a name="about-this-paper"></a>Informationen zu diesem Whitepaper

In diesem Whitepaper werden die Planung und die Vorteile einer Migration Ihrer Anwendungsauthentifizierung zu Azure AD ausführlich erläutert. Es richtet sich an Azure-Administratoren und Identitätsexperten.

Der Prozess ist in vier Phasen unterteilt, die jeweils eine detaillierte Planung und Beendigungskriterien umfassen. Dies soll Ihnen helfen, Ihre Migrationsstrategie zu planen und zu verstehen, wie die Azure AD-Authentifizierung Ihre Organisationsziele unterstützt.

## <a name="introduction"></a>Einführung

Heutzutage benötigt Ihre Organisation eine Vielzahl von Anwendungen (Apps), damit die Benutzer ihre Arbeit erledigen können. Wahrscheinlich werden Sie jeden Tag weitere Apps hinzufügen, entwickeln oder außer Betrieb nehmen. Benutzer greifen auf diese Anwendungen über eine große Bandbreite von firmeneigenen und persönlichen Geräten sowie von verschiedenen Standorten aus zu. Sie öffnen Apps auf vielfältige Weise. Hier einige Beispiele:

- über eine Homepage oder ein Portal des Unternehmens

- mithilfe von Lesezeichen in ihren Browsern

- über die URL eines Anbieters für SaaS-Apps (Software-as-a-Service)

- mithilfe von Links, die über eine Lösung für die Verwaltung mobiler Geräte/Anwendungen (MDM/MAM) direkt auf die Desktops oder mobilen Geräte der Benutzer übermittelt werden

Bei Ihren Anwendungen werden wahrscheinlich die folgenden Authentifizierungstypen verwendet:

- Lokale Verbundlösungen (z. B. Active Directory-Verbunddienste (ADFS) und Ping)

- Active Directory (z. B. Kerberos-Authentifizierung und integrierte Windows-Authentifizierung)

- Andere cloudbasierte Lösungen für die Identitäts- und Zugriffsverwaltung (IAM) (z. B. Okta oder Oracle)

- Lokale Webinfrastruktur (z. B. IIS und Apache)

- In der Cloud gehostete Infrastruktur (z. B. Azure und AWS)

**Um sicherzustellen, dass die Benutzer problemlos und sicher auf Anwendungen zugreifen können, ist es Ihr Ziel, nur über einen einzelnen Satz von Zugriffssteuerungen und -richtlinien in Ihren lokalen und cloudbasierten Umgebungen zu verfügen.**

[Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) bietet eine universelle Identitätsplattform, die Ihren Mitarbeitern, Partnern und Kunden eine einzige Identität für den Zugriff auf die gewünschten Anwendungen und die Zusammenarbeit von beliebigen Plattformen und Geräten aus bereitstellt.

![Diagramm der Azure Active Directory-Konnektivität](media/migrating-application-authentication-to-azure-active-directory-1.jpg)

Azure AD verfügt über eine [vollständige Suite von Funktionen zur Identitätsverwaltung](../fundamentals/active-directory-whatis.md#which-features-work-in-azure-ad). Die Standardisierung der App-Authentifizierung und -Autorisierung auf Azure AD ermöglicht es Ihnen, die Vorteile dieser Funktionen zu nutzen.

Weitere Migrationsressourcen finden Sie unter [https://aka.ms/migrateapps](./migration-resources.md).

## <a name="benefits-of-migrating-app-authentication-to-azure-ad"></a>Vorteile einer Migration der App-Authentifizierung zu Azure AD

Durch das Verschieben der App-Authentifizierung zu Azure AD können Sie Risiken und Kosten verwalten, die Produktivität steigern sowie Compliance- und Governanceanforderungen erfüllen.

### <a name="manage-risk"></a>Risikomanagement

Zum Schutz Ihrer Apps müssen Sie eine vollständige Übersicht über alle Risikofaktoren haben. Durch die Migration Ihrer Apps zu Azure AD werden Ihre Sicherheitslösungen konsolidiert. Dies bietet folgende Möglichkeiten:

- Verbessern des sicheren Benutzerzugriffs auf Anwendungen und zugehörige Unternehmensdaten mithilfe von [Richtlinien für den bedingten Zugriff](../conditional-access/overview.md), [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md) und Technologien für risikobasierten [Identitätsschutz](../identity-protection/overview-identity-protection.md) in Echtzeit

- Schützen des Zugriffs privilegierter Benutzer auf Ihre Umgebung mit [Just-in-Time](../../azure-resource-manager/managed-applications/request-just-in-time-access.md)-Administratorzugriff

- Nutzen der [mehrinstanzenfähigen, geografisch verteilten Azure AD-Struktur mit Hochverfügbarkeit](https://cloudblogs.microsoft.com/enterprisemobility/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-distributed-cloud-directory/) für Ihre wichtigsten Geschäftsanforderungen

- Schützen Ihrer Legacyanwendungen mit einer unserer [Partnerintegrationen für sicheren Hybridzugriff](https://aka.ms/secure-hybrid-access), die Sie möglicherweise schon bereitgestellt haben

### <a name="manage-cost"></a>Kostenmanagement

Unter Umständen verfügt Ihre Organisation auch über mehrere Lösungen für die Identitäts- und Zugriffsverwaltung (Identity and Access Management, IAM). Die Migration zu einer Azure AD-Infrastruktur ist eine Möglichkeit zur Reduzierung der Abhängigkeiten von IAM-Lizenzen (lokal oder in der Cloud) und der Infrastrukturkosten. Wenn Sie mit Microsoft 365-Lizenzen beispielsweise bereits für Azure AD gezahlt haben, gibt es keinen Grund, die zusätzlichen Gebühren für eine weitere IAM-Lösung auszugeben.

**Mit Azure AD können Sie Infrastrukturkosten auf folgende Weise verringern:**

- Bereitstellen eines sicheren Remotezugriffs auf lokale Apps mit dem [Azure AD-Anwendungsproxy](../app-proxy/application-proxy.md)

- Entkoppeln der Apps von der Methode mit lokalen Anmeldeinformationen in Ihrem Mandanten durch [Einrichten von Azure AD als vertrauenswürdigen universellen Identitätsanbieter](../hybrid/plan-connect-user-signin.md#choosing-the-user-sign-in-method-for-your-organization)

### <a name="increase-productivity"></a>Steigerung der Produktivität

Aufgrund der Vorteile in Hinsicht auf Wirtschaftlichkeit und Sicherheit wird Azure AD von immer mehr Organisationen übernommen, doch sind eine vollständige Übernahme und Compliance wahrscheinlicher, wenn auch die Benutzer davon profitieren. Azure AD bietet Ihnen folgende Möglichkeiten:

- Verbesserung des [einmalige Anmeldens (Single Sign-On, SSO)](./what-is-single-sign-on.md) für Endbenutzer durch nahtlosen und sicheren Zugriff auf jede Anwendung von jedem Gerät und jedem Standort aus

- Nutzung der Self-Service-IAM-Funktionen wie [Self-Service-Kennwortzurücksetzungen](../authentication/concept-sspr-howitworks.md) und [Self-Service-Gruppenverwaltung](../enterprise-users/groups-self-service-management.md)

- Reduzierung des Verwaltungsaufwands durch Verwaltung nur einer einzigen Identität für jeden Benutzer in cloudbasierten und lokalen Umgebungen:

  - [Automatisierte Bereitstellung](../app-provisioning/user-provisioning.md) von Benutzerkonten (im [Azure AD-Katalog](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)) basierend auf Azure AD-Identitäten
  - Zugriff auf alle Apps über den Bereich „Meine Apps“ im [Azure-Portal](https://portal.azure.com/)

- Möglichkeit der Entwickler für den sicheren Zugriff auf ihre Apps und Verbesserung der Endbenutzererfahrung durch Verwendung der [Microsoft Identity Platform](../develop/v2-overview.md) mit der Microsoft Authentication Library (MSAL)

- Möglichkeit Ihrer Partner für den Zugriff auf Cloudressourcen mithilfe der [Azure AD B2B-Zusammenarbeit](../external-identities/what-is-b2b.md). Mit Cloudressourcen entfällt der Mehraufwand der Konfiguration eines Point-to-Point-Verbunds mit Ihren Partnern.

### <a name="address-compliance-and-governance"></a>Umsetzen von Compliance und Governance

Gewährleisten Sie die Einhaltung gesetzlicher Bestimmungen, indem Sie Richtlinien für den Unternehmenszugriff erzwingen und den Benutzerzugriff auf Anwendungen und zugehörige Daten mithilfe integrierter Überwachungstools und APIs überwachen. Mit Azure AD können Sie Anmeldungen bei Anwendungen durch Berichte überwachen, die [SIEM-Tools (Security Information & Event Monitoring)](../reports-monitoring/plan-monitoring-and-reporting.md) nutzen. Sie können über das Portal oder APIs auf die Berichte zugreifen und programmgesteuert überwachen, wer Zugriff auf Ihre Anwendungen hat, sowie den Zugriff für inaktive Benutzer durch Zugriffsüberprüfungen entfernen.

## <a name="plan-your-migration-phases-and-project-strategy"></a>Planen Ihrer Migrationsphasen und Projektstrategie

Wenn Technologieprojekte scheitern, liegt das oft an nicht übereinstimmenden Erwartungen, an der fehlenden Einbindung der richtigen Beteiligten oder an mangelnder Kommunikation. Gewährleisten Sie den Erfolg, indem Sie das Projekt selbst planen.

### <a name="the-phases-of-migration"></a>Migrationsphasen

Bevor wir uns mit den Tools befassen, sollten Sie verstehen, wie sich der Migrationsprozess aufbaut. Aufgrund mehrerer Workshops in direkter Zusammenarbeit mit Kunden empfehlen wir die folgenden vier Phasen:

![Diagramm der Migrationsphasen](media/migrating-application-authentication-to-azure-active-directory-2.jpg)

### <a name="assemble-the-project-team"></a>Zusammenstellen des Projektteams

Die Anwendungsmigration ist Teamarbeit, und Sie müssen sicherstellen, dass alle wichtigen Positionen besetzt sind. Die Unterstützung durch leitende Geschäftsführer ist wichtig. Stellen Sie sicher, dass Sie die richtige Gruppe von Führungskräften, Entscheidungsträgern und Fachexperten (SMEs) einbeziehen.

Während des Migrationsprojekts kann eine Person mehrere Rollen erfüllen, oder mehrere Personen können eine Rollen erfüllen, je nach Größe und Struktur Ihrer Organisation. Möglicherweise besteht auch eine Abhängigkeit von anderen Teams, die eine wichtige Rolle in Ihrer Sicherheitslandschaft spielen.

In der folgenden Tabelle sind die wichtigsten Rollen und deren Beiträge aufgeführt:

| Role          | Beiträge                                              |
| ------------- | ---------------------------------------------------------- |
| **Projektmanager** | Projektcoach, der für die Leitung des Projekts verantwortlich ist, einschließlich:<br /> – Gewinnen der Unterstützung durch die Geschäftsleitung<br /> – Einbeziehen von Projektbeteiligten<br /> – Verwalten von Zeitplänen, Dokumentation und Kommunikation |
| **Identitätsarchitekt/Azure AD-Anwendungsadministrator** | Diese sind für Folgendes verantwortlich:<br /> – Entwerfen der Lösung in Zusammenarbeit mit Projektbeteiligten<br /> – Dokumentieren des Lösungsentwurfs und der Betriebsverfahren für die Übergabe an das Betriebsteam<br /> – Verwalten der Präproduktions- und Produktionsumgebungen |
| **Lokales AD-Betriebsteam** | Die Organisation, die die verschiedenen lokalen Identitätsquellen verwaltet, z. B. AD-Gesamtstrukturen, LDAP-Verzeichnisse, Personalsysteme usw.<br /> – Ausführen erforderlicher Wartungsaufgaben vor der Synchronisierung<br /> – Bereitstellen der für die Synchronisierung erforderlichen Dienstkonten<br /> – Bereitstellen des Zugriffs zum Konfigurieren des Verbunds zu Azure AD |
| **IT-Supportmanager** | Ein Vertreter der IT-Supportorganisation, der zu den Unterstützungsmöglichkeiten dieser Änderung aus einer Helpdesk-Perspektive Stellung nehmen kann. |
| **Sicherheitsbesitzer**  | Ein Vertreter des Sicherheitsteams, der sicherstellen kann, dass der Plan die Sicherheitsanforderungen Ihrer Organisation erfüllt. |
| **Technische Anwendungsbesitzer** | Dies umfasst technische Besitzer der Apps und Dienste, die in Azure AD integriert werden. Sie stellen die Identitätsattribute der Anwendungen bereit, die beim Synchronisierungsvorgang enthalten sein sollten. Sie weisen in der Regel eine Beziehung zu CSV-Vertretern auf. |
| **Geschäftliche Anwendungsbesitzer** | Repräsentative Kollegen, die einen Beitrag zur Benutzererfahrung und Nützlichkeit dieser Änderung aus der Sicht eines Benutzers leisten können und im Besitz des gesamten geschäftlichen Aspekts der Anwendung sind, wozu auch die Verwaltung des Zugriffs gehören kann. |
| **Pilotgruppe von Benutzern** | Benutzer, die das Pilotprojekt im Rahmen ihrer täglichen Arbeit testen und Feedback bereitstellen, das als Richtschnur für den Rest der Bereitstellungen verwendet wird. |

### <a name="plan-communications"></a>Planen der Benachrichtigungen

Effektives geschäftliches Engagement und Kommunikation sind der Schlüssel zum Erfolg. Es ist wichtig, Projektbeteiligten und Endbenutzern eine Möglichkeit zu geben, Informationen zu erhalten und über Aktualisierungen des Zeitplans informiert zu werden. Informieren Sie alle Beteiligten über den Wert der Migration, den zu erwartenden Zeitrahmen und die Planung für vorübergehende Unterbrechungen des Geschäftsbetriebs. Nutzen Sie verschiedene Möglichkeiten wie Briefing-Sitzungen, E-Mails, persönliche Treffen, Banner und Townhall Meetings.

Je nach Kommunikationsstrategie, die Sie für die App gewählt haben, können Sie die Benutzer an die anstehende Ausfallzeit erinnern. Sie sollten außerdem sicherstellen, dass es keine kürzlichen Änderungen oder geschäftlichen Auswirkungen gibt, durch die ein Verschieben der Bereitstellung erforderlich wäre.

In der folgenden Tabelle finden Sie die empfohlenen Mindestanforderungen für die Kommunikation, um die Projektbeteiligten auf dem Laufenden zu halten:

**Planen der Phasen und Projektstrategie**:

| Kommunikation      | Zielgruppe                                          |
| ------------------ | ------------------------------------------------- |
| Bewusstmachung und geschäftlicher/technischer Wert des Projekts | Alle außer Endbenutzern |
| Anfrage für Pilot-Apps | – geschäftliche App-Besitzer<br />– technische App-Besitzer<br />– Architekten und Identitätsteam |

**Phase 1: Ermittlung und Bereichsfestlegung**:

| Kommunikation      | Zielgruppe                                          |
| ------------------ | ------------------------------------------------- |
| – Anfrage für Anwendungsinformationen<br />– Ergebnis der Bereichsdefinition | – technische App-Besitzer<br />– geschäftliche App-Besitzer |

**Phase 2: Klassifizieren von Apps und Planen des Pilotprojekts**:

| Kommunikation      | Zielgruppe                                          |
| ------------------ | ------------------------------------------------- |
| – Ergebnis der Klassifizierungen und deren Bedeutung für den Migrationszeitplan<br />– Vorläufiger Migrationszeitplan | – technische App-Besitzer<br /> – geschäftliche App-Besitzer |

**Phase 3: Planen von Migration und Tests**:

| Kommunikation      | Zielgruppe                                          |
| ------------------ | ------------------------------------------------- |
| – Ergebnisse von Tests der Anwendungsmigration | – technische App-Besitzer<br />– geschäftliche App-Besitzer |
| – Benachrichtigung über die bevorstehende Migration und Erläuterung der daraus resultierenden Endbenutzererfahrungen<br />– Bevorstehende Ausfallzeiten und umfassende Kommunikation, einschließlich erforderlicher Aufgaben, Feedback und Anfordern von Hilfe | – Endbenutzer (und alle anderen) |

**Phase 4: Verwalten und Gewinnen von Einblicken**:

| Kommunikation      | Zielgruppe                                          |
| ------------------ | ------------------------------------------------- |
| Verfügbare Analysen und Zugriffsmöglichkeiten | – technische App-Besitzer<br />– geschäftliche App-Besitzer |

### <a name="migration-states-communication-dashboard"></a>Dashboard mit Informationen zum Migrationsstatus

Informationen zum Gesamtstatus des Migrationsprojekts sind von entscheidender Bedeutung, da sie den Fortschritt anzeigen und App-Besitzern, deren Apps zur Migration anstehen, bei der Vorbereitung auf die Umstellung helfen. Sie können ein einfaches Dashboard mithilfe von Power BI oder anderen Berichtstools erstellen, um den Status von Anwendungen während der Migration sichtbar zu machen.

Sie können beispielsweise folgende Angaben zum Migrationsstatus verwenden:

| Migrationsstatus       | Maßnahmenplan                                   |
| ---------------------- | --------------------------------------------- |
| **Erste Anforderung** | Suchen der App und Kontaktaufnahme mit dem Besitzer, um weitere Informationen zu erhalten |
| **Bewertung abgeschlossen** | App-Besitzer bewertet die Anwendungsanforderungen und gibt den App-Fragebogen zurück</td>
| **Konfiguration wird durchgeführt** | Entwickeln der erforderlichen Änderungen zum Verwalten der Authentifizierung für Azure AD |
| **Testkonfiguration erfolgreich** | Bewerten der Änderungen und Authentifizieren der App für den Azure AD-Testmandanten in der Testumgebung |
| **Produktionskonfiguration erfolgreich** | Ändern der Konfigurationen für den AD-Produktionsmandanten und Bewerten der App-Authentifizierung in der Testumgebung |
| **Abschluss/Genehmigung** | Bereitstellen der Änderungen für die App in der Produktionsumgebung und Ausführen für den Azure AD-Produktionsmandanten |

Dadurch wird sichergestellt, dass App-Besitzern der Zeitplan für die Migration und das Testen bekannt ist, sobald ihre Apps zur Migration anstehen, und sie die Ergebnisse anderer Apps kennen, die bereits migriert wurden. Sie können auch die Bereitstellung von Links zur Bugtrackerdatenbank in Betracht ziehen, damit Besitzer Probleme für Apps, die gerade migriert werden, auflisten und anzeigen können.

### <a name="best-practices"></a>Bewährte Methoden

Nachfolgend sind Erfolgsgeschichten unserer Kunden und Partner sowie Vorschläge für bewährte Methoden aufgelistet:

- [Fünf Tipps zur Verbesserung des Migrationsprozesses zu Azure Active Directory](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Five-tips-to-improve-the-migration-process-to-Azure-Active/ba-p/445364) von Patriot Consulting, einem Mitglied unseres Partnernetzwerks, das sich darauf konzentriert, Kunden bei der sicheren Bereitstellung von Microsoft-Cloudlösungen zu unterstützen.

- [Entwickeln einer Risikomanagementstrategie für die Azure AD-Anwendungsmigration](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Develop-a-risk-management-strategy-for-your-Azure-AD-application/ba-p/566488) von Edgile, einem Partner, der sich auf IAM und Risikomanagementlösungen konzentriert.

## <a name="phase-1-discover-and-scope-apps"></a>Phase 1: Ermittlung und Bereichsfestlegung für Apps

**Die Anwendungsermittlung und -analyse bildet den Grundstein für einen guten Start.** Sie kennen möglicherweise noch nicht alle Details, also seien Sie darauf vorbereitet, unbekannte Apps einzubeziehen.

### <a name="find-your-apps"></a>Suchen der Apps

Die erste Entscheidung bei einer Anwendungsmigration ist die Festlegung, welche Apps migriert, welche ggf. beibehalten und welche als veraltet gekennzeichnet werden sollen. Es gibt immer eine Möglichkeit, die Apps als veraltet zu kennzeichnen, die in Ihrer Organisation nicht mehr verwendet werden. Sie können auf mehrere Arten nach Apps in Ihrer Organisation suchen. **Achten Sie bei der Ermittlung von Apps darauf, dass Sie auch in Entwicklung befindliche und geplante Apps einbeziehen. Verwenden Sie in allen zukünftigen Apps Azure AD für die Authentifizierung.**

**Verwenden von Active Directory-Verbunddiensten (ADFS) zum korrekten Erfassen des App-Bestands:**

- **Verwenden von Azure AD Connect Health.** Wenn Sie über eine Azure AD Premium-Lizenz verfügen, empfiehlt sich die Bereitstellung von [Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md), um die App-Nutzung in Ihrer lokalen Umgebung zu analysieren. Mithilfe des [ADFS-Anwendungsberichts](./migrate-adfs-application-activity.md) (Vorschau) können Sie ADFS-Anwendungen ermitteln, die migriert werden können, und die Bereitschaft der Anwendung für die Migration bewerten. Nachdem Sie die Migration abgeschlossen haben, stellen Sie [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery) bereit. Hiermit können Sie Schatten-IT in Ihrer Organisation fortlaufend überwachen, sobald Sie in der Cloud sind.

- **ADFS-Protokollanalyse.** Wenn Sie nicht über Azure AD Premium-Lizenzen verfügen, empfiehlt es sich, ADFS basierend auf [PowerShell](https://github.com/AzureAD/Deployment-Plans/tree/master/ADFS%20to%20AzureAD%20App%20Migration) für Azure AD-App-Migrationstools zu verwenden. Informationen dazu finden Sie unter [Lösungsanleitung](./migrate-adfs-apps-to-azure.md):

[Migrieren von Apps aus Active Directory-Verbunddiensten (ADFS) zu Azure AD](./migrate-adfs-apps-to-azure.md).

### <a name="using-other-identity-providers-idps"></a>Verwenden anderer Identitätsanbieter (IdPs)

Im Falle anderer Identitätsanbieter (z. B. Okta oder Ping) können Sie deren Tools zum Exportieren des Anwendungsbestands verwenden. Sie können sich die in Active Directory registrierten Dienstprinzipale ansehen, die den Web-Apps in Ihrer Organisation entsprechen.

### <a name="using-cloud-discovery-tools"></a>Verwenden von Cloud Discovery-Tools

In der Cloudumgebung benötigen Sie umfassende Transparenz, Kontrolle über den Datenverkehr sowie erweiterte Analysen, um Cyberbedrohungen für all Ihre Clouddienste zu erkennen und abzuwehren. Sie können Ihren Cloud-App-Bestand mit den folgenden Tools erfassen:

- **Cloud Access Security Broker (CASB)** : Ein [CASB](/cloud-app-security/) arbeitet in der Regel mit Ihrer Firewall zusammen, um Einblicke in die Nutzung von Cloudanwendungen durch Ihre Mitarbeiter zu erhalten, und hilft Ihnen, Ihre Unternehmensdaten vor Bedrohungen der Cybersicherheit zu schützen. Mithilfe des CASB-Berichts können Sie die am häufigsten verwendeten Apps in Ihrer Organisation und die ersten Ziele für die Migration zu Azure AD ermitteln.

- **Cloud Discovery**: Durch das Konfigurieren von [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery) erhalten Sie Einblicke in die Nutzung von Cloud-Apps und können nicht sanktionierte oder Schatten-IT-Apps ermitteln.

- **APIs**: Bei Apps, die mit der Cloudinfrastruktur verbunden sind, können Sie die APIs und Tools in diesen Systemen nutzen, um mit der Bestandsaufnahme der gehosteten Apps zu beginnen. In der Azure-Umgebung:

  - Verwenden Sie das Cmdlet [Get-AzureWebsite](/powershell/module/servicemanagement/azure.service/get-azurewebsite), um Informationen zu Azure-Websites abzurufen.

  - Verwenden Sie das Cmdlet [Get-AzureRMWebApp](/powershell/module/azurerm.websites/get-azurermwebapp), um Informationen zu Ihren Azure-Web-Apps abzurufen.
D
  - Mit [AppCmd.exe](/iis/get-started/getting-started-with-iis/getting-started-with-appcmdexe#working-with-sites-applications-virtual-directories-and-application-pools) können Sie alle Apps, die unter Microsoft IIS ausgeführt werden, über die Windows-Befehlszeile suchen.

  - Verwenden Sie [Anwendungen](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) und [Dienstprinzipale](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity), um Informationen zu einer App und App-Instanz in einem Verzeichnis in Azure AD zu erhalten.

### <a name="using-manual-processes"></a>Verwenden manueller Prozesse

Nachdem Sie die oben beschriebenen automatisierten Methoden durchgeführt haben, haben Sie Ihre Anwendungen gut im Griff. Sie sollten jedoch die folgenden Schritte in Erwägung ziehen, um eine gute Abdeckung aller Benutzerzugriffsbereiche zu erzielen:

- Nehmen Sie Kontakt mit den verschiedenen geschäftlichen Besitzern in Ihrer Organisation auf, um die in Ihrer Organisation verwendeten Anwendungen zu ermitteln.

- Führen Sie ein HTTP-Überprüfungstool auf Ihrem Proxyserver aus, oder analysieren Sie Proxyprotokolle, um festzustellen, wohin der Datenverkehr häufig geleitet wird.

- Überprüfen Sie Webprotokolle von beliebten Websites im Unternehmensportal, um zu sehen, auf welche Links die Benutzer am häufigsten zugreifen.

- Wenden Sie sich an Führungskräfte oder andere wichtige Mitarbeiter des Unternehmens, um sicherzustellen, dass Sie die geschäftskritischen Apps abgedeckt haben.

### <a name="type-of-apps-to-migrate"></a>Typ der zu migrierenden Apps

Nachdem Sie die Apps gefunden haben, identifizieren Sie die folgenden App-Typen in Ihrer Organisation:

- Apps, die bereits moderne Authentifizierungsprotokolle verwenden

- Apps, die ältere Authentifizierungsprotokolle verwenden, die Sie modernisieren möchten

- Apps, die ältere Authentifizierungsprotokolle verwenden, die Sie NICHT modernisieren möchten

- Neue LoB-Apps (Line-of-Business)

### <a name="apps-that-use-modern-authentication-already"></a>Apps, die bereits moderne Authentifizierung verwenden

Die bereits modernisierten Apps werden am wahrscheinlichsten zu Azure AD verschoben. Diese Apps verwenden bereits moderne Authentifizierungsprotokolle (z. B. SAML oder OpenID Connect) und können für die Authentifizierung über Azure AD neu konfiguriert werden.

Zusätzlich zu den Optionen im [Azure AD-App-Katalog](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) kann es sich hierbei um Apps handeln, die bereits in Ihrer Organisation vorhanden sind, oder um Drittanbieter-Apps von einem Anbieter, der nicht Teil des Azure AD-Katalogs ist ([nicht im Katalog vorhandene Anwendungen](./add-application-portal.md)).

Legacy-Apps, die Sie modernisieren möchten

Für Legacy-Apps, die Sie modernisieren möchten, werden durch die Umstellung auf Azure AD für die Kernauthentifizierung und -autorisierung der gesamte Leistungs- und Datenumfang freigegeben, den [Microsoft Graph](https://developer.microsoft.com/graph/gallery/?filterBy=Samples,SDKs) und [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence?rtc=1) zu bieten haben.

Für diese Anwendungen wird eine **Codeaktualisierung des Authentifizierungsstapels** vom Legacyprotokoll (z. B. integrierte Windows-Authentifizierung, eingeschränkte Kerberos-Delegierung, auf HTTP-Headern basierende Authentifizierung) auf ein modernes Protokoll (z. B. SAML oder OpenID Connect) empfohlen.

### <a name="legacy-apps-that-you-choose-not-to-modernize"></a>Legacy-Apps, die Sie NICHT modernisieren möchten

Für bestimmte Apps, die ältere Authentifizierungsprotokolle verwenden, ist das Modernisieren der Authentifizierung manchmal aus geschäftlichen Gründen nicht die richtige Lösung. Dazu zählen die folgenden App-Typen:

- Apps, die aus Compliance- oder Kontrollgründen lokal gespeichert sind

- Apps, die mit einer lokalen Identität oder einem lokalen Verbundanbieter verbunden sind, die bzw. den Sie nicht ändern möchten

- Apps, die mit lokalen Authentifizierungsstandards entwickelt wurden, die Sie nicht zu verschieben beabsichtigen

Azure AD kann für diese Legacy-Apps große Vorteile bedeuten, da Sie moderne Azure AD-Sicherheits- und Governancefeatures wie [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md), [bedingten Zugriff](../conditional-access/overview.md), [Identitätsschutz](../identity-protection/index.yml), [delegierten Anwendungszugriff](./access-panel-manage-self-service-access.md) und [Zugriffsüberprüfungen](../governance/manage-user-access-with-access-reviews.md#create-and-perform-an-access-review) für diese Apps aktivieren können, ohne sie bearbeiten zu müssen.

Beginnen Sie mit der **Erweiterung dieser Apps in die Cloud** mit dem [Azure AD-Anwendungsproxy](../app-proxy/application-proxy-configure-single-sign-on-password-vaulting.md). Dabei verwenden Sie einfache Authentifizierungsmethoden (z. B. Kennworttresore), um die Benutzer schnell zu migrieren, oder unsere [Partnerintegrationen](https://azure.microsoft.com/services/active-directory/sso/secure-hybrid-access/) mit Controllern für die Anwendungsbereitstellung, die Sie möglicherweise schon bereitgestellt haben.

### <a name="new-line-of-business-lob-apps"></a>Neue LoB-Apps (Line-of-Business)

Normalerweise entwickeln Sie LoB-Apps für die interne Verwendung in Ihrer Organisation. Wenn Sie über neue Apps in der Pipeline verfügen, empfiehlt es sich, die [Microsoft Identity Platform](../develop/v2-overview.md) zur Implementierung von OpenID Connect zu verwenden.

### <a name="apps-to-deprecate"></a>Als veraltet zu kennzeichnende Apps

Apps ohne eindeutige Besitzer und klare Wartung und Überwachung stellen ein Sicherheitsrisiko für Ihre Organisation dar. Ziehen Sie in folgenden Fällen eine Kennzeichnung von Anwendungen als veraltet in Betracht:

- die **Funktionalität ist mit anderen Systemen hochgradig redundant** • es gibt **keinen geschäftlichen Besitzer**

- es besteht eindeutig **keine Verwendung**

**Wichtige, geschäftskritische Anwendungen sollten nicht als veraltet erklärt werden.** Arbeiten Sie in diesen Fällen mit den geschäftlichen Besitzern zusammen, um die richtige Strategie zu ermitteln.

### <a name="exit-criteria"></a>Beendigungskriterien

Sie haben diese Phase erfolgreich abgeschlossen, wenn Sie über Folgendes verfügen:

- Ein gutes Verständnis der Systeme, die für die Migration in Frage kommen (die Sie nach dem Verschieben in Azure AD außer Betrieb nehmen können)

- Eine Liste von Apps, die folgende Angaben enthält:

  - mit welchen Systemen diese Apps Verbindungen herstellen können
  - von wo und von welchen Geräten aus Benutzer darauf zugreifen
  - ob sie migriert, als veraltet gekennzeichnet oder mit [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) verbunden werden

> [!NOTE]
> Sie können das [Arbeitsblatt für die Anwendungsermittlung](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx) herunterladen, um die Anwendungen zu erfassen, die Sie zur Azure AD-Authentifizierung migrieren möchten, sowie diejenigen, die sie unverändert lassen, jedoch über [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) verwalten möchten.

## <a name="phase-2-classify-apps-and-plan-pilot"></a>Phase 2: Klassifizieren von Apps und Planen des Pilotprojekts

Das Klassifizieren der Migration Ihrer Apps ist eine wichtige Aufgabe. Nicht jede App muss zur gleichen Zeit migriert und umgestellt werden. Nachdem Sie Informationen zu den einzelnen Apps gesammelt haben, können Sie überlegen, welche Apps zuerst migriert werden sollen und welche möglicherweise mehr Zeit benötigen.

### <a name="classify-in-scope-apps"></a>Klassifizieren im Umfang enthaltener Apps

Bei einem Überlegungsansatz sind die geschäftliche Bedeutung, die Nutzung und die Lebensdauer ausschlaggebend, die jeweils von mehreren Faktoren abhängen.

### <a name="business-criticality"></a>Geschäftliche Bedeutung

Die geschäftliche Bedeutung wird für jedes Unternehmen auf unterschiedliche Weise bemessen. Die beiden Messgrößen, die Sie jedoch berücksichtigen sollten, sind **Features und Funktionalität** sowie **Benutzerprofile**. Weisen Sie Apps mit einzigartiger Funktionalität einen höheren Punktwert als solchen mit redundanter oder veralteter Funktionalität zu.

![Diagramm des Spektrums von „Features und Funktionalität“ und „Benutzerprofile“](media/migrating-application-authentication-to-azure-active-directory-3.jpg)

### <a name="usage"></a>Nutzung

Anwendungen mit **hohen Nutzungszahlen** sollten einen höheren Wert erhalten als Apps mit geringer Nutzung. Weisen Sie Apps mit externen Benutzern sowie Benutzern aus den Bereichen Führungskräfte oder Sicherheitsteam einen höheren Wert zu. Führen Sie diese Bewertungen für jede App in Ihrem Migrationsportfolio durch.

![Diagramm des Spektrums von „Benutzervolumen“ und „Benutzerumfang“](media/migrating-application-authentication-to-azure-active-directory-4.jpg)

Nachdem Sie Werte für die geschäftliche Bedeutung und die Nutzung ermittelt haben, können Sie die **Lebensdauer der Anwendung** bestimmen und eine Prioritätsmatrix erstellen. Eine solche Matrix ist hier dargestellt:

![Dreiecksdiagramm der Beziehungen zwischen Nutzung, erwarteter Lebensdauer und geschäftlicher Bedeutung](media/migrating-application-authentication-to-azure-active-directory5.jpg)

### <a name="prioritize-apps-for-migration"></a>Priorisieren von Apps für die Migration

Je nach Anforderungen Ihrer Organisation können Sie die App-Migration entweder mit den Apps mit der niedrigsten Priorität oder den Apps mit der höchsten Priorität beginnen.

In einem Szenario, in dem Sie möglicherweise noch keine Erfahrung mit Azure AD und den Identitätsdiensten haben, sollten Sie überlegen, zuerst die **Apps mit der niedrigsten Priorität** in Azure AD zu verschieben. Dadurch können Sie die geschäftlichen Auswirkungen minimieren, und eine Eigendynamik schaffen. Nachdem Sie diese Apps erfolgreich verschoben und das Vertrauen der Beteiligten gewonnen haben, können Sie mit der Migration der anderen Apps fortfahren.

Wenn es keine eindeutige Priorität gibt, sollten Sie überlegen, zuerst die Apps zu verschieben, die sich im [Azure AD-Katalog](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) befinden und mehrere Identitätsanbieter (ADFS oder Okta) unterstützen, da diese leichter zu integrieren sind. Es ist wahrscheinlich, dass diese Apps die **Apps mit der höchsten Priorität** in Ihrer Organisation darstellen. Um Sie bei der Integration Ihrer SaaS-Anwendungen in Azure AD zu unterstützen, haben wir eine Sammlung von [Tutorials](../saas-apps/tutorial-list.md) entwickelt, in denen die Konfiguration erläutert wird.

Wenn eine Frist zum Migrieren der Apps besteht, stellen diese Apps mit der höchsten Priorität die größte Workload dar. Sie können schließlich noch die Apps mit niedrigerer Priorität auswählen, da sie keine Änderung der Kosten bewirken, auch wenn Sie den Termin verschoben haben. Selbst wenn Sie die Lizenz verlängern müssen, macht dies nur einen kleinen Betrag aus.

Zusätzlich zu dieser Klassifizierung und je nach Dringlichkeit der Migration können Sie auch einen **Migrationszeitplan** aufstellen, in dessen Zeitrahmen sich die App-Besitzer um eine Migration ihrer Apps bemühen müssen. Am Ende dieses Prozesses sollten Sie über eine Liste aller Anwendungen in priorisierten Buckets für die Migration verfügen.

### <a name="document-your-apps"></a>Dokumentieren Ihrer Apps

Sammeln Sie zunächst wichtige Details zu Ihren Anwendungen. Mithilfe des [Arbeitsblatts für die Anwendungsermittlung](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx) können Sie Migrationsentscheidungen schnell treffen und in kürzester Zeit eine Empfehlung an Ihre Geschäftsgruppe senden.

Wichtige Informationen für Migrationsentscheidungen umfassen Folgendes:

- **App-Name**: Unter welcher Bezeichnung ist die App im Unternehmen bekannt?

- **App-Typ:** Handelt es sich um eine SaaS-App eines Drittanbieters? Handelt es sich um eine benutzerdefinierte branchenspezifische Web-App? Eine API?

- **Geschäftliche Bedeutung**: Ist sie von großer Bedeutung? Geringer Bedeutung? Irgendwo dazwischen?

- **Benutzerzugriffsvolumen**: Greifen alle oder nur wenige Personen auf diese App zu?

- **Geplante Lebensdauer**: Wie lange wird diese App verwendet? Weniger als sechs Monate? Mehr als zwei Jahre?

- **Aktueller Identitätsanbieter**: Wer ist der primäre IdP für diese App? Oder basiert sie auf lokalem Speicher?

- **Authentifizierungsmethode**: Führt die App die Authentifizierung mithilfe offener Standards durch?

- **Angabe, ob der App-Code aktualisiert werden soll**: Befindet sich die App in einer geplanten oder aktiven Entwicklungsphase?

- **Ob die App lokal ausgeführt werden soll:** Möchten Sie die App langfristig in Ihrem Rechenzentrum behalten?

- **Angabe, ob die App von anderen Apps oder APIs abhängig ist**: Ruft die App derzeit andere Apps oder APIs auf?

- **Angabe, ob sich die App im Azure AD-Katalog befindet**: Ist die App derzeit bereits in den [Azure AD-Katalog](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) integriert?

Weitere Daten, die später hilfreich für Sie sein werden, die aber nicht für eine sofortige Migrationsentscheidung benötigt werden:

- **App-URL**: Wo können Benutzer auf die App zugreifen?

- **App-Beschreibung**: Wie kann die Funktionsweise der App kurz beschrieben werden?

- **App-Besitzer**: Wer im Unternehmen ist der Haupt-POC für die App?

- **Allgemeine Kommentare oder Hinweise**: Sonstige allgemeine Informationen über die App oder den geschäftlichen Besitz.

Sobald Sie die Anwendung klassifiziert und die Details dokumentiert haben, sollten Sie die Zustimmung des geschäftlichen Besitzers zur geplanten Migrationsstrategie gewinnen.

### <a name="plan-a-pilot"></a>Planen eines Pilotprojekts

Die Apps, die Sie für das Pilotprojekt auswählen, sollten die wichtigsten Identitäts- und Sicherheitsanforderungen Ihrer Organisation repräsentieren, und Sie müssen über eine eindeutige Zustimmung der Anwendungsbesitzer verfügen. Pilotprojekte werden in der Regel in einer separaten Testumgebung ausgeführt. Weitere Informationen finden Sie auf der Seite mit Bereitstellungsplänen unter [Bewährte Methoden für einen Pilotversuch](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot).

**Vergessen Sie nicht Ihre externen Partner.** Stellen Sie sicher, dass sie an Migrationszeitplänen und Tests beteiligt sind. Stellen Sie zuletzt sicher, dass Ihre Partner im Falle kritischer Probleme auf Ihren Helpdesk zugreifen können.

### <a name="plan-for-limitations"></a>Planen von Einschränkungen

Während einige Apps leicht zu migrieren sind, kann es bei anderen aufgrund mehrerer Server oder Instanzen länger dauern. Beispielsweise kann die SharePoint-Migration aufgrund benutzerdefinierter Anmeldeseiten mehr Zeit in Anspruch nehmen.

Viele Anbieter von SaaS-Apps erheben Gebühren für das Ändern der SSO-Verbindung. Fragen Sie bei den Anbietern nach, und planen Sie es ein.

Azure AD weist ebenfalls [Dienst- und andere Einschränkungen](../enterprise-users/directory-service-limits-restrictions.md) auf, die Sie beachten sollten.

### <a name="app-owner-sign-off"></a>Genehmigung des App-Besitzers

Geschäftskritische und universell genutzte Anwendungen erfordern möglicherweise eine Gruppe von Pilotbenutzern, um die App in der Pilotphase zu testen. Nachdem Sie eine App in der Präproduktions- oder Pilotumgebung getestet haben, stellen Sie sicher, dass die geschäftlichen App-Besitzer die Leistung genehmigen, bevor die App und alle Benutzer zur Nutzung von Azure AD für die Authentifizierung in der Produktionsumgebung migriert werden.

### <a name="plan-the-security-posture"></a>Planen des Sicherheitsstatus

Bevor Sie den Migrationsprozess einleiten, sollten Sie sich Zeit nehmen, um den Sicherheitsstatus, den Sie für das Identitätssystem des Unternehmens entwickeln möchten, umfassend zu bedenken. Die Grundlage dafür bilden die folgenden wertvollen Informationen: **Identitäten, Geräte und Standorte, die auf Ihre Daten zugreifen**.

### <a name="identities-and-data"></a>Identitäten und Daten

Die meisten Organisationen haben spezifische Anforderungen in Bezug auf Identitäten und Datenschutz, die je nach Branchensegment und Aufgabenbereich innerhalb von Organisationen variieren. Unter [Konfigurationen für den Identitäts- und Gerätezugriff](/microsoft-365/enterprise/microsoft-365-policies-configurations) finden Sie unsere Empfehlungen, einschließlich eines vorgeschriebenen Satzes von [Richtlinien für den bedingten Zugriff](../conditional-access/overview.md) und zugehörige Funktionen.

Anhand dieser Informationen können Sie den Zugriff auf alle in Azure AD integrierten Dienste schützen. Diese Empfehlungen sind an der Microsoft-Sicherheitsbewertung sowie der [Identitätsbewertung in Azure AD](../fundamentals/identity-secure-score.md) ausgerichtet. Anhand der Bewertung können Sie:

- Den Status Ihrer Identitätssicherheit objektiv messen

- Verbesserungen der Identitätssicherheit planen

- Den Erfolg Ihrer Verbesserungen überprüfen

Dies hilft Ihnen auch bei der Implementierung der [fünf Schritte zum Sichern Ihrer Identitätsinfrastruktur](../../security/fundamentals/steps-secure-identity.md). Verwenden Sie diesen Leitfaden als Ausgangspunkt für Ihre Organisation, und passen Sie die Richtlinien an die spezifischen Anforderungen Ihrer Organisation an.

### <a name="who-is-accessing-your-data"></a>Wer greift auf die Daten zu?

Es gibt zwei Hauptkategorien von Benutzern Ihrer Apps und Ressourcen, die von Azure AD unterstützt werden:

- **Intern:** Mitarbeiter, Auftragnehmer und Anbieter mit Konten innerhalb Ihres Identitätsanbieters. Hierfür sind möglicherweise weitere Pivotierungen mit unterschiedlichen Regeln für Manager oder Führungskräfte gegenüber anderen Mitarbeitern erforderlich.

- **Extern:** Anbieter, Lieferanten, Verteiler oder andere Geschäftspartner, die im regulären Geschäftsablauf im Rahmen der [Azure AD B2B-Zusammenarbeit](../external-identities/what-is-b2b.md) mit Ihrer Organisation interagieren.

Sie können Gruppen für diese Benutzer definieren und diese Gruppen auf unterschiedlichste Weise auffüllen. Sie können auswählen, dass ein Administrator manuell Mitglieder zu einer Gruppe hinzufügen muss, oder Sie können die Self-Service-Gruppenmitgliedschaft aktivieren. Es können Regeln eingerichtet werden, mit denen Mitglieder mithilfe [dynamischer Gruppen](../enterprise-users/groups-dynamic-membership.md) und basierend auf spezifischen Kriterien automatisch zu Gruppen hinzugefügt werden.

Externe Benutzer können auch Kunden sein. [Azure AD B2C](../../active-directory-b2c/overview.md), ein separates Produkt, unterstützt die Kundenauthentifizierung. Dies ist jedoch nicht Thema dieses Whitepapers.

### <a name="devicelocation-used-to-access-data"></a>Gerät/Standort für den Datenzugriff

Das Gerät und der Standort, von dem aus ein Benutzer auf eine App zugreift, sind ebenfalls wichtig. Geräte, die physisch mit Ihrem Unternehmensnetzwerk verbunden sind, sind sicherer. Verbindungen von außerhalb des Netzwerks über VPN müssen möglicherweise überprüft werden.

![Diagramm der Beziehung zwischen Benutzerstandort und Datenzugriff](media/migrating-application-authentication-to-azure-active-directory-6.jpg)

Unter Berücksichtigung dieser Aspekte von Ressource, Benutzer und Gerät können Sie die Funktionen für den [bedingten Zugriff mit Azure AD](../conditional-access/overview.md) verwenden. Der bedingte Zugriff geht über Benutzerberechtigungen hinaus. Er basiert auf einer Kombination von Faktoren wie der Identität eines Benutzers oder einer Gruppe, dem Netzwerk, mit dem der Benutzer verbunden ist, dem Gerät und der Anwendung, die verwendet werden, und dem Typ von Daten, auf die zugegriffen werden soll. Der Zugriff, der dem Benutzer gewährt wird, wird an diesen umfassenderen Satz von Bedingungen angepasst.

### <a name="exit-criteria"></a>Beendigungskriterien

Sie haben diese Phase erfolgreich abgeschlossen, wenn Sie über Folgendes verfügen:

- Kenntnisse über Ihre Apps
  - Vollständige Dokumentation der Apps, die Sie migrieren möchten
  - Priorisierung der Apps auf Grundlage von geschäftlicher Bedeutung, Nutzungsvolumen und Lebensdauer

- Ausgewählte Apps, die Ihre Anforderungen für ein Pilotprojekt repräsentierten

- Zustimmung des geschäftlichen Besitzers zur Priorisierung und Strategie

- Kenntnisse des erforderlichen Sicherheitsstatus und dessen Implementierung

## <a name="phase-3-plan-migration-and-testing"></a>Phase 3: Planen von Migration und Tests

Sobald Sie die geschäftliche Zustimmung erhalten haben, können Sie im nächsten Schritt damit beginnen, diese Anwendungen zur Azure AD-Authentifizierung zu migrieren.

### <a name="migration-tools-and-guidance"></a>Migrationstools und -anleitungen

Verwenden Sie die folgenden Tools und Anleitungen, um die exakten Schritte zum Migrieren Ihrer Anwendungen zu Azure AD auszuführen:

- **Allgemeine Anleitungen für die Migration**: Verwenden Sie das Whitepaper, die Tools, die E-Mail-Vorlagen und den Anwendungsfragebogen im [Toolkit für die Migration von Apps zu Azure AD](./migration-resources.md), um Ihre Apps zu ermitteln, zu klassifizieren und zu migrieren.

- **SaaS-Anwendungen**: Sehen Sie sich unsere Liste mit [Hunderten von SaaS-App-Tutorials](../saas-apps/tutorial-list.md) und den vollständigen [Azure AD-SSO-Bereitstellungsplan](https://aka.ms/ssodeploymentplan) an, um den End-to-End-Prozess zu durchlaufen.

- **Lokal ausgeführte Anwendungen**: Informieren Sie sich über den [Azure AD-Anwendungsproxy](../app-proxy/application-proxy.md), und verwenden Sie den vollständigen [Bereitstellungsplan für den Azure AD-Anwendungsproxy](https://aka.ms/AppProxyDPDownload), um schnell loslegen zu können.

- **Von Ihnen entwickelte Apps:** Lesen Sie unsere ausführlichen Anleitungen für die [Integration](../develop/quickstart-register-app.md) und [Registrierung](../develop/quickstart-register-app.md).

Nach der Migration können Sie die Benutzer über die erfolgreiche Bereitstellung informieren und an alle neuen Schritte erinnern, die sie durchführen müssen.

### <a name="plan-testing"></a>Planen von Tests

Während des Migrationsprozesses verfügt Ihre App möglicherweise bereits über eine Testumgebung, die bei regulären Bereitstellungen verwendet wird. Sie können diese Umgebung weiterhin für Migrationstests verwenden. Wenn zurzeit keine Testumgebung zur Verfügung steht, können Sie diese je nach Architektur der Anwendung möglicherweise mithilfe von Azure App Service oder Azure Virtual Machines einrichten. Sie können einen separaten Azure AD-Testmandanten einrichten, der bei der Entwicklung Ihrer App-Konfigurationen verwendet werden soll. Dieser Mandant wird in einem fehlerfreien Zustand gestartet und ist nicht für die Synchronisierung mit einem System konfiguriert.

Sie können die einzelnen Apps testen, indem Sie sich mit einem Testbenutzer anmelden und sicherstellen, dass alle Funktionen mit denen vor der Migration übereinstimmen. Wenn Sie während der Tests feststellen, dass Benutzer ihre [MFA](../authentication/howto-mfa-userstates.md)- oder [SSPR](../authentication/tutorial-enable-sspr.md)-Einstellungen aktualisieren müssen, oder wenn Sie diese Funktionalität während der Migration hinzufügen, sollten Sie daran denken, dies zu Ihrem Kommunikationsplan für Endbenutzer hinzuzufügen. Weitere Informationen finden Sie in den [MFA](https://aka.ms/mfatemplates)- und [SSPR](https://aka.ms/ssprtemplates)-Vorlagen für die Endbenutzerkommunikation.

Nachdem Sie die Apps migriert haben, wechseln Sie zum [Azure-Portal](https://aad.portal.azure.com/), um zu testen, ob die Migration erfolgreich war. Befolgen Sie die nachstehenden Anweisungen:

- Wählen Sie **Unternehmensanwendungen &gt; Alle Anwendungen** aus, und suchen Sie die App in der Liste.

- Wählen Sie **Verwalten &gt; Benutzer und Gruppen** aus, um der App mindestens einen Benutzer oder eine Gruppe zuzuweisen.

- Wählen Sie **Verwalten &gt; Bedingter Zugriff** aus. Überprüfen Sie die Liste der Richtlinien, und stellen Sie sicher, dass Sie den Zugriff auf die Anwendung nicht mit einer [Richtlinie für bedingten Zugriff](../conditional-access/overview.md) blockieren.

Vergewissern Sie sich, dass das einmalige Anmelden je nach Konfiguration Ihrer App ordnungsgemäß funktioniert.

| Authentifizierungsart      | Testen                                             |
| ------------------------ | --------------------------------------------------- |
| **OAuth und OpenID Connect** | Wählen Sie **Unternehmensanwendungen &gt; Berechtigungen** aus, und stellen Sie sicher, dass Sie in den Benutzereinstellungen für Ihre App zugestimmt haben, dass die Anwendung in Ihrer Organisation verwendet werden darf. |
| **SAML-basiertes SSO** | Verwenden Sie die Schaltfläche [SAML-Einstellungen testen](./debug-saml-sso-issues.md) unter **Einmaliges Anmelden**. |
| **Kennwortbasiertes SSO** | Laden Sie die [Erweiterung zur sicheren Anmeldung bei „Meine Apps“](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) herunter, und installieren Sie sie. Mit dieser Erweiterung können Sie alle Cloud-Apps Ihrer Organisation starten, bei denen Sie einen SSO-Prozess verwenden müssen. |

|  **[Anwendungsproxy](../app-proxy/application-proxy.md)** | Stellen Sie sicher, dass Ihr Connector ausgeführt wird und der Anwendung zugewiesen ist. Im Leitfaden zum [Beheben von Problemen mit Anwendungsproxys und Fehlermeldungen](../app-proxy/application-proxy-troubleshoot.md) finden Sie weitere Unterstützung. |

### <a name="troubleshoot"></a>Problembehandlung

Falls Probleme auftreten, finden Sie Hilfe in unserem [Leitfaden zum Beheben von Problemen mit Apps](../app-provisioning/isv-automatic-provisioning-multi-tenant-apps.md). Weitere Informationen finden Sie in den Artikeln zur Problembehandlung, z. B. [Probleme bei der Anmeldung bei SAML-basierten Apps mit konfigurierter einmaliger Anmeldung](/troubleshoot/azure/active-directory/troubleshoot-sign-in-saml-based-apps).

### <a name="plan-rollback"></a>Planen eines Rollbacks

Falls die Migration fehlschlägt, besteht die beste Strategie in einem Rollback und anschließenden Tests. Nachfolgend sind die Schritte angegeben, die Sie zum Beheben von Migrationsproblemen ausführen können:

- **Erstellen Sie Screenshots** der bestehenden Konfiguration Ihrer App. Wenn Sie die App erneut konfigurieren müssen, können Sie darauf zurückgreifen.

- Bei Problemen mit der Cloudauthentifizierung können Sie auch die **Bereitstellung von Links zur Legacyauthentifizierung** in Betracht ziehen.

- **Ändern Sie nicht die vorhandene Konfiguration** mit dem früheren Identitätsanbieter, bevor Sie die Migration abgeschlossen haben.

- Migrieren Sie zunächst **die Apps, die mehrere IdPs unterstützen**. Wenn etwas schief geht, können Sie jederzeit zur Konfiguration des bevorzugten IdP wechseln.

- Stellen Sie sicher, dass Ihre App für den Fall von Problemen über eine **Feedbackschaltfläche** oder einen Verweis auf Ihren **Helpdesk** verfügt.

### <a name="exit-criteria"></a>Beendigungskriterien

Sie haben diese Phase erfolgreich abgeschlossen, wenn Sie folgende Aufgaben ausgeführt haben:

- Festlegen, wie die einzelnen Apps migriert werden

- Überprüfen der Migrationstools

- Planen der Tests, einschließlich Testumgebungen und -gruppen

- Planen eines Rollbacks

## <a name="phase-4-plan-management-and-insights"></a>Phase 4: Planen der Verwaltung und Einblicke

Nachdem Apps migriert wurden, müssen Sie Folgendes sicherstellen:

- Benutzer können sicher auf die Apps zugreifen und sie verwalten.

- Sie erhalten die entsprechenden Einblicke in die Nutzung und den Integritätszustand der App.

Es wird empfohlen, die folgenden Maßnahmen entsprechend Ihrer Organisation durchzuführen.

### <a name="manage-your-users-app-access"></a>Verwalten des App-Zugriffs Ihrer Benutzer

Nachdem Sie die Apps migriert haben, können Sie die Benutzererfahrung auf viele Arten erweitern.

**Erkennbarmachen von Apps**

**Verweisen Sie die Benutzer** auf die Portalfunktion [Meine Apps](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension). Hier haben die Benutzer Zugriff auf alle cloudbasierten Apps, Apps, die Sie über [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) zur Verfügung stellen, und Apps, die den [Anwendungsproxy](../app-proxy/application-proxy.md) verwenden, sofern die Benutzer über Berechtigungen für den Zugriff auf diese Apps verfügen.

Sie können den Benutzern Hilfen zum Entdecken ihrer Apps bereitstellen:

- Verwenden Sie das Feature [Vorhandenes einmaliges Anmelden](./view-applications-portal.md), um **Ihre Benutzer mit einer beliebigen App zu verknüpfen**.

- Ermöglichen Sie den [Self-Service-Anwendungszugriff](./manage-self-service-access.md) auf eine App, und **lassen Sie Benutzer von Ihnen bereitgestellte Apps hinzufügen**.

- [Blenden Sie Anwendungen für Endbenutzer aus](./hide-application-from-user-portal.md) (standardmäßige Microsoft-Apps oder andere Apps), damit **die benötigten Apps besser erkennbar sind**.

### <a name="make-apps-accessible"></a>Verfügbarmachen von Apps

**Ermöglichen Sie Benutzern den Zugriff auf Apps von ihren mobilen Geräten.** Benutzer können auf Geräten mit [iOS](./hide-application-from-user-portal.md) 7.0 oder höher oder [auf Android](./hide-application-from-user-portal.md)-Geräten über Intune Managed Browser auf das Portal „Meine Apps“ zugreifen.

Benutzer können einen **Intune Managed Browser** herunterladen:

- **Für Android-Geräte** im [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.intune)

- **Für Apple-Geräte** im [Apple App Store](https://apps.apple.com/us/app/intune-company-portal/id719171358), oder sie können die [mobile App für „Meine Apps" für iOS](https://appadvice.com/app/my-apps-azure-active-directory/824048653) herunterladen

**Ermöglichen Sie Benutzern das Öffnen ihrer Apps über eine Browsererweiterung.**

Benutzer können die [Erweiterung zur sicheren Anmeldung bei „Meine Apps“](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) in [Chrome](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl) oder [Microsoft Edge](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) herunterladen und Apps direkt von ihrer Browserleiste aus zu folgenden Zwecken starten:

- **Suchen nach ihren Apps und Anzeigen der zuletzt verwendeten Apps**

- **Automatisches Konvertieren interner URLs**, die Sie im [Anwendungsproxy](../app-proxy/application-proxy.md) konfiguriert haben, in die entsprechenden externen URLs. Die Benutzer können jetzt mit den vertrauten Links arbeiten, unabhängig davon, wo sie sich befinden.

**Ermöglichen Sie Benutzern das Öffnen ihrer Apps über Office.com.**

Benutzer können auf [Office.com](https://www.office.com/) **nach ihren Apps suchen und sich ihre zuletzt verwendeten Apps** direkt von ihrem Arbeitsplatz aus anzeigen lassen.

### <a name="secure-app-access"></a>Sicherer App-Zugriff

Azure AD bietet einen zentralen Zugriffsort zur Verwaltung Ihrer migrierten Apps. Wechseln Sie zum [Azure-Portal](https://portal.azure.com/), und aktivieren Sie die folgenden Funktionen:

- **Sicherer Benutzerzugriff auf Apps.** Aktivieren Sie [Richtlinien für bedingten Zugriff](../conditional-access/overview.md) oder [Identitätsschutz](../identity-protection/overview-identity-protection.md), um den Benutzerzugriff auf Anwendungen basierend auf dem Gerätestatus, dem Standort usw. zu schützen.

- **Automatische Bereitstellung.** Sie können die [automatische Bereitstellung von Benutzern](../app-provisioning/user-provisioning.md) für eine Vielzahl von SaaS-Drittanbieter-Apps einrichten, auf die Benutzer zugreifen müssen. Zusätzlich zur Erstellung von Benutzeridentitäten umfasst dies auch die Wartung und Entfernung von Benutzeridentitäten, wenn sich der Status oder die Rollen ändern.

- **Delegieren der** **Benutzerzugriffsverwaltung.** Aktivieren Sie nach Bedarf den Self-Service-Anwendungszugriff auf Ihre Apps, und *weisen Sie eine genehmigende Person des Unternehmens zu, um den Zugriff auf diese Apps zu genehmigen*. Verwenden Sie die [Self-Service-Gruppenverwaltung](../enterprise-users/groups-self-service-management.md) für Gruppen, die Sammlungen von Apps zugewiesen sind.

- **Delegieren des Administratorzugriffs.** Verwenden Sie die **Verzeichnisrolle**, um dem Benutzer eine Administratorrolle (z. B. Anwendungsadministrator, Cloudanwendungsadministrator oder Anwendungsentwickler) zuzuweisen.

### <a name="audit-and-gain-insights-of-your-apps"></a>Überwachen und Gewinnen von Einblicken in Ihre Apps

Sie können das [Azure-Portal](https://portal.azure.com/) auch verwenden, um alle Ihre Apps von einem zentralen Ort aus zu überwachen:

- **App-Überwachung** mithilfe von **Unternehmensanwendungen, Überwachung** oder Zugriff auf die gleichen Informationen über die [Azure AD-Berichterstellungs-API](../reports-monitoring/concept-reporting-api.md), um eine Integration mit Ihren bevorzugten Tools durchzuführen.

- **Anzeigen der Berechtigungen für eine App** mithilfe von **Unternehmensanwendungen, Berechtigungen** für Apps über OAuth/OpenID Connect

- **Einblicke in Anmeldungen** mithilfe von **Unternehmensanwendungen, Anmeldungen** Zugreifen auf die gleichen Informationen über die [Azure AD-Berichterstellungs-API](../reports-monitoring/concept-reporting-api.md)

- **Visualisierung der Nutzung Ihrer App** über das [Power BI-Inhaltspaket für Azure AD](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

### <a name="exit-criteria"></a>Beendigungskriterien

Sie haben diese Phase erfolgreich abgeschlossen, wenn Sie folgende Aufgaben ausgeführt haben:

- Bereitstellen eines sicheren App-Zugriffs für Ihre Benutzer

- Überwachen und Erhalten von Einblicken in die migrierten Apps

### <a name="do-even-more-with-deployment-plans"></a>Weitere Möglichkeiten von Bereitstellungsplänen

Bereitstellungspläne führen Sie durch die Schritte für den geschäftlichen Nutzen, die Planung und Implementierung sowie die Verwaltung von Azure AD-Lösungen, einschließlich App-Migrationsszenarien. Darin ist alles zusammengefasst, was Sie benötigen, um mit der Bereitstellung und Nutzung von Azure AD-Funktionen zu beginnen. Die Bereitstellungsleitfäden umfassen Inhalte wie die von Microsoft empfohlenen bewährten Methoden, die Endbenutzerkommunikation, Planungsleitfäden, Implementierungsschritte, Testfälle usw.

Es steht Ihnen eine Vielzahl von [Bereitstellungsplänen](../fundamentals/active-directory-deployment-plans.md) zur Verwendung bereit, und es werden ständig neue entwickelt.

### <a name="contact-support"></a>Kontaktieren des Supports

Verwenden Sie die folgenden Supportlinks zum Erstellen oder Nachverfolgen von Supporttickets und zum Überwachen der Integrität.

- **Azure-Support:** Sie können sich an den [Microsoft-Support](https://azure.microsoft.com/support) wenden und ein Ticket für ein beliebiges Problem mit der Azure-

Identitätsbereitstellung öffnen, je nach Enterprise Agreement mit Microsoft.

- **FastTrack**: Wenn Sie EMS- (Enterprise Mobility and Security) oder Azure AD Premium-Lizenzen erworben haben, können Sie Unterstützung bei der Bereitstellung über das [FastTrack-Programm](/enterprise-mobility-security/solutions/enterprise-mobility-fasttrack-program) erhalten.

- **Einbinden des Produktengineering-Teams:** Wenn Sie an einer großen Kundenbereitstellung mit Millionen von Benutzern arbeiten, haben Sie Anspruch auf Unterstützung durch das Microsoft-Konto-Team oder Ihren Cloud Solutions Architect. Je nach Komplexität der Bereitstellung des Projekts können Sie direkt mit dem [Produktengineering-Team für Azure-Identitäten](https://aad.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/solutionProviders) zusammenarbeiten.

- **Blog zu Azure AD-Identitäten:** Abonnieren Sie den [Blog zu Azure AD-Identitäten](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/bg-p/Identity), um über alle aktuellen Produktankündigungen, ausführlichen Betrachtungen und Roadmapinformationen, die direkt vom Identitäten-Engineeringteam bereitgestellt werden, auf dem Laufenden zu bleiben.
