---
title: Integrierte Rollen in Azure AD – Azure Active Directory
description: Beschreibung der in Azure Active Directory integrierten Rollen und Berechtigungen.
services: active-directory
author: rolyon
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 11/10/2021
ms.author: rolyon
ms.reviewer: abhijeetsinha
ms.custom: generated, it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: e936e457c03c56ee64edfec8177bb7a565d0fb98
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132371655"
---
# <a name="azure-ad-built-in-roles"></a>Integrierte Rollen in Azure AD

Wenn ein anderer Administrator oder ein Benutzer ohne Administratorrechte Azure AD-Ressourcen in Azure Active Directory (Azure AD) verwalten muss, müssen Sie ihm einer Azure AD-Rolle zuweisen, die die erforderlichen Berechtigungen bereitstellt. Beispielsweise können Sie Rollen zuweisen, die das Hinzufügen oder Ändern von Benutzern, das Zurücksetzen von Benutzerkennwörtern, das Verwalten von Benutzerlizenzen oder das Verwalten von Domänennamen erlauben.

In diesem Artikel werden die in Azure AD integrierten Rollen aufgelistet, die Sie zuweisen können, um die Verwaltung von Azure AD-Ressourcen zuzulassen. Weitere Informationen zum Zuweisen von Rollen finden Sie unter [Zuweisen von Azure AD-Rollen zu Benutzern](manage-roles-portal.md).

## <a name="all-roles"></a>Alle Rollen

> [!div class="mx-tableFixed"]
> | Role | BESCHREIBUNG | Vorlagen-ID |
> | --- | --- | --- |
> | [Anwendungsadministrator](#application-administrator) | Kann alle Aspekte von App-Registrierungen und Enterprise-Apps erstellen und verwalten. | 9b895d92-2cd3-44c7-9d02-a6ac2d5ea5c3 |
> | [Anwendungsentwickler](#application-developer) | Erstellen von Anwendungsregistrierungen unabhängig von der Einstellung „Benutzer können Anwendungen registrieren“. | cf1c38e5-3621-4004-a7cb-879624dced7c |
> | [Autor der Angriffsnutzdaten](#attack-payload-author) | Kann Angriffsnutzdaten erstellen, die ein Administrator später initiieren kann. | 9c6df0f2-1e7c-4dc3-b195-66dfbd24aa8f |
> | [Administrator für Angriffssimulation](#attack-simulation-administrator) | Kann alle Aspekte von Angriffssimulationskampagnen erstellen und verwalten. | c430b396-e693-46cc-96f3-db01bf8bb62a |
> | [Authentifizierungsadministrator](#authentication-administrator) | Hat Zugriff zum Anzeigen, Festlegen und Zurücksetzen von Informationen zu Authentifizierungsmethoden für alle Benutzer ohne Administratorrechte. | c4e39bd9-1100-46d3-8c65-fb160da0071f |
> | [Authentifizierungsrichtlinienadministrator](#authentication-policy-administrator) | Kann die Authentifizierungsmethodenrichtlinie, mandantenweite MFA-Einstellungen, die Kennwortschutzrichtlinie und überprüfbare Anmeldeinformationen erstellen und verwalten. | 0526716b-113d-4c15-b2c8-68e3c22b9f80 |
> | [Lokaler Administrator für in Azure AD eingebundenes Gerät](#azure-ad-joined-device-local-administrator) | Benutzer, die dieser Rolle zugewiesen wurden, werden der lokalen Administratorgruppe auf in Azure AD eingebundenen Geräten hinzugefügt. | 9f06204d-73c1-4d4c-880a-6edb90606fd8 |
> | [Azure DevOps-Administrator](#azure-devops-administrator) | Kann Richtlinien und Einstellungen für die Azure DevOps-Organisation verwalten. | e3973bdf-4987-49ae-837a-ba8e231c7286 |
> | [Azure Information Protection-Administrator](#azure-information-protection-administrator) | Verwalten sämtlicher Aspekte des Produkts Azure Information Protection. | 7495fdc4-34c4-4d15-a289-98788ce399fd |
> | [B2C-IEF-Schlüsselsatzadministrator](#b2c-ief-keyset-administrator) | Kann Geheimnisse für Verbund und Verschlüsselung im Identity Experience Framework (IEF) verwalten. | aaf43236-0c0d-4d5f-883a-6955382ac081 |
> | [B2C-IEF-Richtlinienadministrator](#b2c-ief-policy-administrator) | Kann Vertrauensframeworkrichtlinien im Identity Experience Framework (IEF) erstellen und verwalten. | 3edaf663-341e-4475-9f94-5c398ef6c070 |
> | [Rechnungsadministrator](#billing-administrator) | Ausführen von allgemeinen Abrechnungsaufgaben wie der Aktualisierung der Zahlungsinformationen. | b0f54661-2d74-4c50-afa3-1ec803f12efe |
> | [Cloud App Security-Administrator](#cloud-app-security-administrator) | Kann alle Aspekte des Cloud App Security-Produkts verwalten. | 892c5842-a9a6-463a-8041-72aa08ca3cf6 |
> | [Cloudanwendungsadministrator](#cloud-application-administrator) | Erstellen und Verwalten sämtlicher Aspekte von App-Registrierungen und Enterprise-Apps außer App-Proxy. | 158c047a-c907-4556-b7ef-446551a6b5f7 |
> | [Cloudgeräteadministrator](#cloud-device-administrator) | Hat eingeschränkten Zugriff auf die Verwaltung von Geräten in Azure AD. | 7698a772-787b-4ac8-901f-60d6b08affd2 |
> | [Complianceadministrator](#compliance-administrator) | Lesen und Verwalten der Konformitätskonfiguration und der zugehörigen Berichte in Azure AD und Microsoft 365. | 17315797-102d-40b4-93e0-432062caca18 |
> | [Compliancedatenadministrator](#compliance-data-administrator) | Erstellt und verwaltet Complianceinhalte. | e6d1a23a-da11-4be4-9570-befc86d067a7 |
> | [Administrator für bedingten Zugriff](#conditional-access-administrator) | Verwalten von Funktionen zum bedingten Zugriff. | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9 |
> | [Genehmigende Person für den LockBox-Kundenzugriff](#customer-lockbox-access-approver) | Kann Microsoft-Supportanfragen zum Zugriff auf Benutzerorganisationsdaten genehmigen. | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91 |
> | [Desktop Analytics-Administrator](#desktop-analytics-administrator) | Kann auf Tools und Dienste zur Desktopverwaltung zugreifen und diese verwalten. | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4 |
> | [Verzeichnisleseberechtigte](#directory-readers) | Lesen von grundlegenden Verzeichnisinformationen. Wird häufig verwendet, um Anwendungen und Gästen Lesezugriff für das Verzeichnis zu erteilen. | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b |
> | [Verzeichnissynchronisierungskonten](#directory-synchronization-accounts) | Nur vom Azure AD Connect-Dienst verwendet. | d29b2b05-8046-44ba-8758-1e26182fcf32 |
> | [Verzeichnisschreibberechtigte](#directory-writers) | Kann grundlegende Verzeichnisinformationen lesen und schreiben. Die Rolle gewährt Zugriff auf Anwendungen und ist nicht für Benutzer vorgesehen. | 9360feb5-f418-4baa-8175-e2a00bac4301 |
> | [Domänennamenadministrator](#domain-name-administrator) | Verwalten von Domänennamen lokal und in der Cloud. | 8329153b-31d0-4727-b945-745eb3bc5f31 |
> | [Dynamics 365-Administrator](#dynamics-365-administrator) | Verwalten sämtlicher Aspekte des Produkts Dynamics 365. | 44367163-eba1-44c3-98af-f5787879f96a |
> | [Edgeadministrator](#edge-administrator) | Verwalten sämtlicher Aspekte von Microsoft Edge. | 3f1acade-1e04-4fbc-9b69-f0302cd84aef |
> | [Exchange-Administrator](#exchange-administrator) | Verwalten sämtlicher Aspekte des Produkts Exchange. | 29232cdf-9323-42fd-ade2-1d097af3e4de |
> | [Administrator für Exchange-Empfänger](#exchange-recipient-administrator) | Erstellen oder Aktualisieren von Exchange Online-Empfängern in der Exchange Online-Organisation. | 31392ffb-586c-42d1-9346-e59415a2cc4e |
> | [Administrator für Benutzerflows mit externer ID](#external-id-user-flow-administrator) | Kann alle Aspekte von Benutzerflows erstellen und verwalten. | 6e591065-9bad-43ed-90f3-e9424366d2f0 |
> | [Administrator für Benutzerflowattribute mit externer ID](#external-id-user-flow-attribute-administrator) | Kann das für alle Benutzerflows verfügbare Attributschema erstellen und verwalten. | 0f971eea-41eb-4569-a71e-57bb8a3eff1e |
> | [Externer Identitätsanbieteradministrator](#external-identity-provider-administrator) | Kann Identitätsanbieter für die Verwendung in einem direkten Verbund konfigurieren. | be2f45a1-457d-42af-a067-6ec1fa63bc45 |
> | [Globaler Administrator](#global-administrator) | Verwalten sämtlicher Aspekte von Azure AD und Microsoft-Diensten, die Azure AD-Identitäten verwenden. | 62e90394-69f5-4237-9190-012177145e10 |
> | [Globaler Leser](#global-reader) | Hat die gleichen Leseberechtigungen wie ein globaler Administrator, kann jedoch keine Aktualisierungen durchführen. | f2ef992c-3afb-46b9-b7cf-a126ee74c451 |
> | [Gruppenadministrator](#groups-administrator) | Mitglieder dieser Rolle können Gruppen erstellen/verwalten, Gruppeneinstellungen wie Benennungs- und Ablaufrichtlinien erstellen und verwalten sowie Aktivitäts- und Überwachungsberichte von Gruppen anzeigen. | fdd7a751-b60b-444a-984c-02652fe8fa1c |
> | [Gasteinladender](#guest-inviter) | Einladen von Gastbenutzernunabhängig von der Einstellung „Mitglieder können Gäste einladen“. | 95e79109-95c0-4d8e-aee3-d01accf2d47b |
> | [Helpdeskadministrator](#helpdesk-administrator) | Zurücksetzen von Kennwörtern für Nicht-Administratoren und Helpdeskadministratoren. | 729827e3-9c14-49f7-bb1b-9608f156bbb8 |
> | [Hybrididentitätsadministrator](#hybrid-identity-administrator) | Verwalten der Cloudbereitstellung zwischen AD und Azure AD sowie von Azure AD Connect und Verbundeinstellungen. | 8ac3fc64-6eca-42ea-9e69-59f4c7b60eb2 |
> | [Identity Governance-Administrator](#identity-governance-administrator) | Verwaltet den Zugriff mithilfe von Azure AD für Identity Governance-Szenarien. | 45d8d3c5-c802-45c6-b32a-1d70b5e1e86e |
> | [Insights Administrator](#insights-administrator) | Administratorzugriff in der Microsoft 365 Insights-App. | eb1f4a8d-243a-41f0-9fbd-c7cdf6c5ef7c |
> | [Insights Business Leader](#insights-business-leader) | Kann Dashboards und Erkenntnisse über die Microsoft 365 Insights-App anzeigen und freigeben. | 31e939ad-9672-4796-9c2e-873181342d2d |
> | [Intune-Administrator](#intune-administrator) | Verwalten sämtlicher Aspekte des Produkts Intune. | 3a2c62db-5318-420d-8d74-23affee5d9d5 |
> | [Kaizala-Administrator](#kaizala-administrator) | Kann Einstellungen für Microsoft Kaizala verwalten. | 74ef975b-6605-40af-a5d2-b9539d836353 |
> | [Wissensadministrator](#knowledge-administrator) | Kann Wissens-, Lern- und andere intelligente Features konfigurieren. | b5a8dcf3-09d5-43a9-a639-8e29ef291470 |
> | [Wissens-Manager](#knowledge-manager) | Kann Themen und Wissen organisieren, erstellen, verwalten und bewerben. | 744ec460-397e-42ad-a462-8b3f9747a02c |
> | [Lizenzadministrator](#license-administrator) | Kann Produktlizenzen für Benutzer und Gruppen verwalten. | 4d6ac14f-3453-41d0-bef9-a3e0c569773a |
> | [Nachrichtencenter-Datenschutzleseberechtigter](#message-center-privacy-reader) | Kann Sicherheitsnachrichten und -updates nur im Office 365-Nachrichtencenter lesen. | ac16e43d-7b2d-40e0-ac05-243ff356ab5b |
> | [Nachrichtencenter-Leseberechtigter](#message-center-reader) | Lesen von Nachrichten und Updates für die Organisation ausschließlich im Office 365-Nachrichtencenter. | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b |
> | [Modern Commerce User](#modern-commerce-user) | Kann kommerzielle Käufe für ein Unternehmen, eine Abteilung oder ein Team verwalten. | d24aef57-1500-4070-84db-2666f29cf966 |
> | [Netzwerkadministrator](#network-administrator) | Verwalten von Netzwerkstandorten und überprüfen von Erkenntnissen zum Entwurf des Unternehmensnetzwerks für Microsoft 365-SaaS-Anwendungen (Software-as-a-Service). | d37c8bed-0711-4417-ba38-b4abe66ce4c2 |
> | [Office-Apps-Administrator](#office-apps-administrator) | Kann Clouddienste für Office-Apps (einschließlich Richtlinien- und Einstellungsverwaltung) sowie Funktionen zum Auswählen, Aufheben der Auswahl und Veröffentlichen von Inhalten zu neuen Features auf Endbenutzergeräten verwalten. | 2b745bdf-0803-4d80-aa65-822c4493daac |
> | [Partnersupport der Ebene 1](#partner-tier1-support) | Verwenden Sie diese Rolle nicht – sie ist nicht zur allgemeinen Verwendung vorgesehen. | 4ba39ca4-527c-499a-b93d-d9b492c50246 |
> | [Partnersupport der Ebene 2](#partner-tier2-support) | Verwenden Sie diese Rolle nicht – sie ist nicht zur allgemeinen Verwendung vorgesehen. | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8 |
> | [Kennwortadministrator](#password-administrator) | Kann Kennwörter für Nicht-Administratoren und Kennwortadministratoren zurücksetzen. | 966707d0-3269-4727-9be2-8c3a10f19b9d |
> | [Power BI-Administrator](#power-bi-administrator) | Verwalten sämtlicher Aspekte des Produkts Power BI. | a9ea8996-122f-4c74-9520-8edcd192826c |
> | [Power Platform-Administrator](#power-platform-administrator) | Kann alle Aspekte von Microsoft Dynamics 365, Power Apps und Power Automate erstellen und verwalten. | 11648597-926c-4cf3-9c36-bcebb0ba8dcc |
> | [Druckeradministrator](#printer-administrator) | Verwalten sämtlicher Aspekte von Druckern und Druckerconnectors. | 644ef478-e28f-4e28-b9dc-3fdde9aa0b1f |
> | [Druckertechniker](#printer-technician) | Registrieren von Druckern, Aufheben ihrer Registrierung und Aktualisieren des Druckerstatus. | e8cef6f1-e4bd-4ea8-bc07-4b8d950f4477 |
> | [Privilegierter Authentifizierungsadministrator](#privileged-authentication-administrator) | Hat Zugriff zum Anzeigen, Festlegen und Zurücksetzen von Informationen zu Authentifizierungsmethoden für alle Benutzer (mit und ohne Administratorrechte). | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13 |
> | [Administrator für privilegierte Rollen](#privileged-role-administrator) | Kann Rollenzuweisungen in Azure AD sowie sämtliche Aspekte von Privileged Identity Management (PIM) verwalten. | e8611ab8-c189-46e8-94e1-60213ab1f814 |
> | [Berichtleseberechtigter](#reports-reader) | Lesen von Anmeldungs- und Überwachungsberichten. | 4a5d8f65-41da-4de4-8968-e035b65339cf |
> | [Suchadministrator](#search-administrator) | Kann alle Aspekte der Microsoft Search-Einstellungen erstellen und verwalten. | 0964bb5e-9bdb-4d7b-ac29-58e794862a40 |
> | [Such-Editor](#search-editor) | Kann redaktionelle Inhalte wie Lesezeichen, Fragen und Antworten, Standorte und Grundrisse erstellen und verwalten. | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9 |
> | [Sicherheitsadministrator](#security-administrator) | Kann Sicherheitsinformationen und -berichte lesen und die Konfiguration in Azure AD und Office 365 verwalten. | 194ae4cb-b126-40b2-bd5b-6091b380977d |
> | [Sicherheitsoperator](#security-operator) | Erstellt und verwaltet Sicherheitsereignisse. | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f |
> | [Sicherheitsleseberechtigter](#security-reader) | Lesen von Sicherheitsinformationen und Berichten in Azure AD und Office 365. | 5d6b6bb7-de71-4623-b4af-96380a352509 |
> | [Dienstsupportadministrator](#service-support-administrator) | Lesen von Service Health-Informationen und Verwalten von Supporttickets. | f023fd81-a637-4b56-95fd-791ac0226033 |
> | [SharePoint-Administrator](#sharepoint-administrator) | Verwalten sämtlicher Aspekte des SharePoint-Diensts. | f28a1f50-f6e7-4571-818b-6a12f2af6b6c |
> | [Skype for Business-Administrator](#skype-for-business-administrator) | Verwalten sämtlicher Aspekte des Produkts Skype for Business. | 75941009-915a-4869-abe7-691bff18279e |
> | [Teams-Administrator](#teams-administrator) | Kann den Microsoft Teams-Dienst verwalten. | 69091246-20e8-4a56-aa4d-066075b2a7a8 |
> | [Teams-Kommunikationsadministrator](#teams-communications-administrator) | Kann Anruf- und Besprechungsfunktionen im Microsoft Teams-Dienst verwalten. | baf37b3a-610e-45da-9e62-d9d1e5e8914b |
> | [Supporttechniker für die Teams-Kommunikation](#teams-communications-support-engineer) | Kann Kommunikationsprobleme in Teams mithilfe von erweiterten Tools behandeln. | f70938a0-fc10-4177-9e90-2178f8765737 |
> | [Supportfachmann für die Teams-Kommunikation](#teams-communications-support-specialist) | Kann Kommunikationsprobleme in Teams mithilfe von allgemeinen Tools behandeln. | fcf91098-03e3-41a9-b5ba-6f0ec8188a12 |
> | [Teams-Geräteadministrator](#teams-devices-administrator) | Berechtigung für verwaltungsbezogene Aufgaben auf zertifizierten Teams-Geräten. | 3d762c5a-1b6c-493f-843e-55a3b42923d4 |
> | [Leseberechtigter für Berichte mit Nutzungszusammenfassung](#usage-summary-reports-reader) | Kann in der Microsoft 365-Nutzungsanalyse und -Produktivitätsbewertung nur Aggregate auf Mandantenebene anzeigen. | 75934031-6c7e-415a-99d7-48dbd49e875e |
> | [Benutzeradministrator](#user-administrator) | Dieser Administrator kann alle Aspekte von Benutzern und Gruppen verwalten, einschließlich der Kennwortzurücksetzung für eingeschränkte Administratoren. | fe930be7-5e62-47db-91af-98c3a49a38b1 |
> | [Windows 365-Administrator](#windows-365-administrator) | Kann alle Aspekte von Cloud-PCs bereitstellen und verwalten. | 11451d60-acb2-45eb-a7d6-43d0f0125c13 |
> | [Windows Update-Bereitstellungsadministrator](#windows-update-deployment-administrator) | Benutzer mit dieser Rolle können alle Aspekte von Windows Update-Bereitstellungen über den Windows Update for Business-Bereitstellungsdienst erstellen und verwalten. | 32696413-001a-46ae-978c-ce0f6b3620d2 |

## <a name="application-administrator"></a>Anwendungsadministrator

Benutzer mit dieser Rolle können alle Aspekte von Unternehmensanwendungen, Anwendungsregistrierungen und Anwendungsproxyeinstellungen erstellen und verwalten. Beachten Sie, dass Benutzer, denen diese Rolle zugewiesen wurde, bei der Erstellung neuer Anwendungsregistrierungen oder Unternehmensanwendungen nicht als Besitzer hinzugefügt werden.

Diese Rolle ermöglicht auch die Zustimmung zu delegierten Berechtigungen und Anwendungsberechtigungen (mit Ausnahme von Anwendungsberechtigungen für Microsoft Graph und Azure AD Graph).

> [!IMPORTANT]
> Aufgrund dieser Ausnahme können Sie immer noch Berechtigungen für _andere_ Apps (z. B. nicht von Microsoft stammende Apps oder von Ihnen registrierte Apps) zustimmen. Sie können diese Berechtigungen weiterhin im Rahmen der App-Registrierung _anfordern_. Für das _Erteilen_ dieser Berechtigungen (d. h. die Zustimmung) ist jedoch ein Administrator mit höheren Rechten (z. B. ein globaler Administrator) erforderlich.
>
>Diese Rolle ermöglicht die Verwaltung von Anmeldeinformationen für Anwendungen. Benutzer, die dieser Rolle zugewiesen sind, können einer Anwendung Anmeldeinformationen hinzufügen und diese Anmeldeinformationen verwenden, um die Anwendung zu imitieren. Wenn der Identität der Anwendung der Zugriff auf eine Ressource gewährt wurde, z. B. die Berechtigung, Benutzer oder andere Objekte zu erstellen oder zu aktualisieren, kann ein dieser Rolle zugewiesener Benutzer diese Aktionen ausführen, während er die Identität der Anwendung annimmt. Diese Fähigkeit, die Identität der Anwendung anzunehmen, bedeutet ggf. eine Rechteerweiterung im Vergleich zu den Rollenzuweisungen des Benutzers. Beachten Sie, dass das Zuweisen eines Benutzers zur Anwendungsadministratorrolle ihm die Möglichkeit gibt, die Identität einer Anwendung anzunehmen.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/applications/create | Erstellen aller Anwendungstypen |
> | microsoft.directory/applications/delete | Löschen aller Anwendungstypen |
> | microsoft.directory/applications/applicationProxy/read | Lesen aller Anwendungsproxyeigenschaften |
> | microsoft.directory/applications/applicationProxy/update | Aktualisieren aller Anwendungsproxyeigenschaften |
> | microsoft.directory/applications/applicationProxyAuthentication/update | Aktualisieren der Authentifizierung für alle Anwendungstypen |
> | microsoft.directory/applications/applicationProxySslCertificate/update | Aktualisieren der SSL-Zertifikateinstellungen für den Anwendungsproxy |
> | microsoft.directory/applications/applicationProxyUrlSettings/update | Aktualisieren der URL-Einstellungen für den Anwendungsproxy |
> | microsoft.directory/applications/appRoles/update | Aktualisieren der appRoles-Eigenschaft für alle Anwendungstypen |
> | microsoft.directory/applications/audience/update | Aktualisieren der audience-Eigenschaft für Anwendungen |
> | microsoft.directory/applications/authentication/update | Aktualisieren der Authentifizierung für alle Anwendungstypen |
> | microsoft.directory/applications/basic/update | Aktualisieren grundlegender Eigenschaften für Anwendungen |
> | microsoft.directory/applications/credentials/update | Aktualisieren von Anwendungsanmeldeinformationen |
> | microsoft.directory/applications/extensionProperties/update | Aktualisieren von Erweiterungseigenschaften von Anwendungen |
> | microsoft.directory/applications/notes/update | Aktualisieren von Anwendungshinweisen |
> | microsoft.directory/applications/owners/update | Aktualisieren von Anwendungsbesitzern |
> | microsoft.directory/applications/permissions/update | Aktualisieren von verfügbar gemachten und erforderlichen Berechtigungen für alle Anwendungstypen |
> | microsoft.directory/applications/policies/update | Aktualisieren von Anwendungsrichtlinien |
> | microsoft.directory/applications/tag/update | Aktualisieren von Anwendungstags |
> | microsoft.directory/applications/verification/update | Aktualisieren der applicationsverification-Eigenschaft |
> | microsoft.directory/applications/synchronization/standard/read | Lesen von Bereitstellungseinstellungen, die dem Anwendungsobjekt zugeordnet sind |
> | microsoft.directory/applicationTemplates/instantiate | Instanziieren von Kataloganwendungen über Anwendungsvorlagen |
> | microsoft.directory/auditLogs/allProperties/read | Lesen aller Eigenschaften von Überwachungsprotokollen (einschließlich privilegierter Eigenschaften) |
> | microsoft.directory/connectors/create | Erstellen von Anwendungsproxyconnectors |
> | microsoft.directory/connectors/allProperties/read | Lesen sämtlicher Eigenschaften von Anwendungsproxyconnectors |
> | microsoft.directory/connectorGroups/create | Erstellen von Anwendungsproxy-Connectorgruppen |
> | microsoft.directory/connectorGroups/delete | Löschen von Anwendungsproxy-Connectorgruppen |
> | microsoft.directory/connectorGroups/allProperties/read | Lesen sämtlicher Eigenschaften von Anwendungsproxy-Connectorgruppen |
> | microsoft.directory/connectorGroups/allProperties/update | Aktualisieren sämtlicher Eigenschaften von Anwendungsproxy-Connectorgruppen |
> | microsoft.directory/deletedItems.applications/delete | Dauerhaftes Löschen von Anwendungen, die nicht mehr wiederhergestellt werden können |
> | microsoft.directory/deletedItems.applications/restore | Wiederherstellen vorläufig gelöschter Anwendungen in ihrem ursprünglichen Zustand |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Erstellen und Löschen von OAuth 2.0-Berechtigungszuweisungen und Lesen und Aktualisieren aller Eigenschaften |
> | microsoft.directory/applicationPolicies/create | Erstellen von Anwendungsrichtlinien |
> | microsoft.directory/applicationPolicies/delete | Löschen von Anwendungsrichtlinien |
> | microsoft.directory/applicationPolicies/standard/read | Lesen der Standardeigenschaften von Anwendungsrichtlinien |
> | microsoft.directory/applicationPolicies/owners/read | Lesen der Besitzer von Anwendungsrichtlinien |
> | microsoft.directory/applicationPolicies/policyAppliedTo/read | Lesen der Liste der auf Objekte angewendeten Anwendungsrichtlinien |
> | microsoft.directory/applicationPolicies/basic/update | Aktualisieren der Standardeigenschaften von Anwendungsrichtlinien |
> | microsoft.directory/applicationPolicies/owners/update | Aktualisieren der owner-Eigenschaft von Anwendungsrichtlinien |
> | microsoft.directory/provisioningLogs/allProperties/read | Lesen aller Eigenschaften von Bereitstellungsprotokollen |
> | microsoft.directory/servicePrincipals/create | Erstellen von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/delete | Löschen von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/disable | Deaktivieren von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/enable | Aktivieren von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | Verwalten von Anmeldeinformationen für das einmalige Anmelden per Kennwort für Dienstprinzipale |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Verwalten der Geheimnisse und Anmeldeinformationen für die Anwendungsbereitstellung |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | Starten, Neustarten und Anhalten von Synchronisierungsaufträgen für die Anwendungsbereitstellung |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | Erstellen und Verwalten von Synchronisierungsaufträgen und -schemas für die Anwendungsbereitstellung |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | Lesen von Anmeldeinformationen für das einmalige Anmelden per Kennwort für Dienstprinzipale |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-application-admin | Erteilen der Zustimmung zu Anwendungsberechtigungen und delegierten Berechtigungen für einen oder alle Benutzer (mit Ausnahme von Anwendungsberechtigungen für Microsoft Graph und Azure AD Graph) |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aktualisieren von Rollenzuweisungen für Dienstprinzipale |
> | microsoft.directory/servicePrincipals/audience/update | Aktualisieren der Zielgruppeneigenschaften für Dienstprinzipale |
> | microsoft.directory/servicePrincipals/authentication/update | Aktualisieren der Authentifizierungseigenschaften für Dienstprinzipale |
> | microsoft.directory/servicePrincipals/basic/update | Aktualisieren grundlegender Eigenschaften für Dienstprinzipale |
> | microsoft.directory/servicePrincipals/credentials/update | Aktualisieren der Anmeldeinformationen von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/notes/update | Aktualisieren von Hinweisen zu Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/owners/update | Aktualisieren der Besitzer von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/permissions/update | Aktualisieren der Berechtigungen von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/policies/update | Aktualisieren der Richtlinien für Dienstprinzipale |
> | microsoft.directory/servicePrincipals/tag/update | Aktualisieren der tag-Eigenschaft für Dienstprinzipale |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | Lesen von Bereitstellungseinstellungen, die Ihrem Dienstprinzipal zugeordnet sind |
> | microsoft.directory/signInReports/allProperties/read | Lesen sämtlicher Eigenschaften für Anmeldeberichte (einschließlich privilegierter Eigenschaften) |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Service Health im Microsoft 365 Admin Center |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="application-developer"></a>Anwendungsentwickler

Benutzer mit dieser Rolle können Anwendungsregistrierungen erstellen, wenn die Einstellung „Benutzer können Anwendungen registrieren“ auf „Nein“ festgelegt ist. Diese Rolle ermöglicht auch die Berechtigung, im eigenen Namen zuzustimmen, wenn die Einstellung „Benutzer können Apps zustimmen, die in ihrem Namen auf Unternehmensdaten zugreifen“ auf „Nein“ festgelegt ist. Benutzer, denen diese Rolle zugewiesen wurde, werden bei der Erstellung neuer Anwendungsregistrierungen als Besitzer hinzugefügt.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/applications/createAsOwner | Erstellen aller Anwendungstypen (Ersteller wird als erster Besitzer hinzugefügt) |
> | microsoft.directory/oAuth2PermissionGrants/createAsOwner | Erstellen von OAuth 2.0-Berechtigungszuweisungen (Ersteller wird als erster Besitzer hinzugefügt) |
> | microsoft.directory/servicePrincipals/createAsOwner | Erstellen von Dienstprinzipalen (Ersteller wird als erster Besitzer hinzugefügt) |

## <a name="attack-payload-author"></a>Autor der Angriffsnutzdaten

Benutzer in dieser Rolle können Angriffsnutzdaten erstellen, diese jedoch nicht tatsächlich starten oder planen. Die Angriffsnutzdaten stehen dann allen Administratoren im Mandanten zur Verfügung und können von diesen zum Erstellen einer Simulation verwendet werden.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/allTasks | Erstellen und Verwalten von Angriffsnutzdaten im Angriffssimulator |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | Lesen von Berichten zu Reaktionen auf Angriffssimulationen und zugehörigen Schulungsunterlagen |

## <a name="attack-simulation-administrator"></a>Administrator für Angriffssimulation

Benutzer mit dieser Rolle können alle Aspekte der Angriffssimulationserstellung, des Starts und der Planung einer Simulation und der Überprüfung der Simulationsergebnisse erstellen und verwalten. Mitglieder dieser Rolle besitzen diesen Zugriff für alle Simulationen im Mandanten.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/allTasks | Erstellen und Verwalten von Angriffsnutzdaten im Angriffssimulator |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | Lesen von Berichten zu Reaktionen auf Angriffssimulationen und zugehörigen Schulungsunterlagen |
> | microsoft.office365.protectionCenter/attackSimulator/simulation/allProperties/allTasks | Erstellen und Verwalten von Angriffssimulationsvorlagen im Angriffssimulator |

## <a name="authentication-administrator"></a>Authentifizierungsadministrator

Benutzer mit dieser Rolle können alle Authentifizierungsmethoden (einschließlich Kennwörter) für Nicht-Administratoren und einige andere Rollen festlegen oder zurücksetzen. Authentifizierungsadministratoren können Benutzer, die keine Administratoren sind oder die Rollen zugewiesen sind, auffordern, ihre vorhandenen Anmeldeinformationen ohne Kennwort (z. B. MFA oder FIDO) erneut zu registrieren, und sie können auch **Speichern der MFA auf dem Gerät** widerrufen. Dann werden Benutzer bei der nächsten Anmeldung zur Eingabe ihrer MFA-Anmeldeinformationen aufgefordert. Eine Liste der Rollen, die ein Authentifizierungsadministrator lesen oder deren Authentifizierungsmethoden er aktualisieren kann, finden Sie unter [Berechtigungen für die Kennwortzurücksetzung](#password-reset-permissions).

Die Rolle [Privilegierter Authentifizierungsadministrator](#privileged-authentication-administrator) verfügt über die Berechtigung zum Erzwingen einer erneuten Registrierung und der mehrstufigen Authentifizierung (Multi-Factor Authentication, MFA) für alle Benutzer.

Die Rolle [Authentifizierungsrichtlinienadministrator](#authentication-policy-administrator) verfügt über Berechtigungen zum Festlegen der Authentifizierungsmethodenrichtlinie des Mandanten, mit der bestimmt wird, welche Methoden die einzelnen Benutzer registrieren und verwenden können.

| Role | Verwalten der Authentifizierungsmethoden des Benutzers | Aktivieren der Multi-Factor Authentication (MFA) pro Benutzer | Verwalten der MFA-Einstellungen | Verwalten der Authentifizierungsmethodenrichtlinie | Verwalten der Kennwortschutzrichtlinie |
| ---- | ---- | ---- | ---- | ---- | ---- |
| Authentifizierungsadministrator | Ja, für einige Benutzer (siehe oben) | Ja, für einige Benutzer (siehe oben) | Nein | Nein | Nein |
| Privilegierter Authentifizierungsadministrator| Ja, für alle Benutzer | Ja, für alle Benutzer | Nein | Nein | Nein |
| Authentifizierungsrichtlinienadministrator | Nein |Nein | Ja | Ja | Ja |

> [!IMPORTANT]
> Benutzer mit dieser Rolle können Anmeldeinformationen für Benutzer ändern, die Zugriff auf vertrauliche oder private Informationen bzw. kritische Konfigurationen innerhalb und außerhalb von Azure Active Directory haben. Das bedeutet, dass Benutzer, die Anmeldeinformationen ändern können, ggf. auch die Identität und die Berechtigungen des betreffenden Benutzers annehmen können. Beispiel:
>
>* Besitzer von Anwendungsregistrierungen und Unternehmensanwendungen, die Anmeldeinformationen von Apps verwalten können, die sie besitzen. Diese Apps können über höhere Berechtigungen in Azure AD und in anderen Diensten verfügen, die Authentifizierungsadministratoren nicht gewährt werden. Auf diesem Weg kann ein Authentifizierungsadministrator die Identität eines Anwendungsbesitzers annehmen und dann durch Aktualisieren der Anmeldeinformationen für die Anwendung die Identität einer privilegierten Anwendung annehmen.
>* Besitzer von Azure-Abonnements, die ggf. auf vertrauliche oder private Informationen bzw. kritische Konfigurationen in Azure zugreifen können.
>* Besitzer von Sicherheitsgruppen und Microsoft 365-Gruppen, die die Gruppenmitgliedschaft verwalten können. Diese Gruppen können Zugriff auf vertrauliche oder private Informationen bzw. kritische Konfigurationen in Azure AD und in anderen Diensten gewähren.
>* Administratoren in anderen Diensten außerhalb von Azure AD wie Exchange Online, Office 365 Security & Compliance Center und Personalsystemen.
>* Nichtadministratoren wie Führungskräfte, Rechtsberater und Mitarbeiter der Personalabteilung mit Zugriff auf vertrauliche oder private Informationen.

> [!IMPORTANT]
> Diese Rolle kann die MFA-Einstellungen im Legacy-MFA-Verwaltungsportal oder Hardware-OATH-Token nicht verwalten. Die gleichen Funktionen können mithilfe des Cmdlets [Set-MsolUser](/powershell/module/msonline/set-msoluser) im Azure AD PowerShell-Modul erreicht werden.

> [!div class="mx-tableFixed"]
> | Aktionen | Beschreibung |
> | --- | --- |
> | microsoft.directory/users/authenticationMethods/create | Erstellen von Authentifizierungsmethoden für Benutzer |
> | microsoft.directory/users/authenticationMethods/delete | Löschen von Authentifizierungsmethoden für Benutzer |
> | microsoft.directory/users/authenticationMethods/standard/restrictedRead | Lesen der Standardeigenschaften von Authentifizierungsmethoden, die keine personenbezogenen Informationen für Benutzer enthalten |
> | microsoft.directory/users/authenticationMethods/basic/update | Aktualisieren der grundlegenden Eigenschaften von Authentifizierungsmethoden für Benutzer |
> | microsoft.directory/users/invalidateAllRefreshTokens | Erzwingen der Abmeldung von Benutzern durch Ungültigmachen des Aktualisierungstokens |
> | microsoft.directory/users/password/update | Zurücksetzen der Kennwörter für alle Benutzer |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Service Health im Microsoft 365 Admin Center |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="authentication-policy-administrator"></a>Authentifizierungsrichtlinienadministrator

Benutzer mit dieser Rolle können die Authentifizierungsmethodenrichtlinie, mandantenweite MFA-Einstellungen und die Kennwortschutzrichtlinie konfigurieren. Diese Rolle gewährt die Berechtigung zum Verwalten von Kennwortschutzeinstellungen: Smart Lockout-Konfigurationen und Aktualisieren der Liste der benutzerdefinierten gesperrten Kennwörter.

Die Rollen [Authentifizierungsadministrator](#authentication-administrator) und [Privilegierter Authentifizierungsadministrator](#privileged-authentication-administrator) verfügen über die Berechtigung zum Verwalten registrierter Authentifizierungsmethoden für Benutzer und können für alle Benutzer eine erneute Registrierung und Multi-Factor Authentication erzwingen.

| Role | Verwalten der Authentifizierungsmethoden des Benutzers | Aktivieren der Multi-Factor Authentication (MFA) pro Benutzer | Verwalten der MFA-Einstellungen | Verwalten der Authentifizierungsmethodenrichtlinie | Verwalten der Kennwortschutzrichtlinie |
| ---- | ---- | ---- | ---- | ---- | ---- |
| Authentifizierungsadministrator | Ja, für einige Benutzer (siehe oben) | Ja, für einige Benutzer (siehe oben) | Nein | Nein | Nein |
| Privilegierter Authentifizierungsadministrator| Ja, für alle Benutzer | Ja, für alle Benutzer | Nein | Nein | Nein |
| Authentifizierungsrichtlinienadministrator | Nein | Nein | Ja | Ja | Ja |

> [!IMPORTANT]
> Diese Rolle kann die MFA-Einstellungen im Legacy-MFA-Verwaltungsportal oder Hardware-OATH-Token nicht verwalten.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/organization/strongAuthentication/allTasks | Verwalten aller Aspekte von Eigenschaften für eine sichere Authentifizierung in einer Organisation |
> | microsoft.directory/userCredentialPolicies/create | Erstellen von Anmeldeinformationsrichtlinien für Benutzer |
> | microsoft.directory/userCredentialPolicies/delete | Löschen von Anmeldeinformationsrichtlinien für Benutzer |
> | microsoft.directory/userCredentialPolicies/standard/read | Lesen der Standardeigenschaften von Anmeldeinformationsrichtlinien für Benutzer |
> | microsoft.directory/userCredentialPolicies/owners/read | Lesen der Besitzer von Anmeldeinformationsrichtlinien für Benutzer |
> | microsoft.directory/userCredentialPolicies/policyAppliedTo/read | Lesen des Navigationslinks „policy.appliesTo“ |
> | microsoft.directory/userCredentialPolicies/basic/update | Aktualisieren grundlegender Richtlinien für Benutzer |
> | microsoft.directory/userCredentialPolicies/owners/update | Aktualisieren der Besitzer von Anmeldeinformationsrichtlinien für Benutzer |
> | microsoft.directory/userCredentialPolicies/tenantDefault/update | Aktualisieren der policy.isOrganizationDefault-Eigenschaft |
> | microsoft.directory/verifiableCredentials/configuration/contracts/cards/allProperties/read | Lesen einer Karte mit überprüfbaren Anmeldeinformationen |
> | microsoft.directory/verifiableCredentials/configuration/contracts/cards/revoke | Widerrufen einer Karte mit überprüfbaren Anmeldeinformationen |
> | microsoft.directory/verifiableCredentials/configuration/contracts/create | Erstellen eines Vertrags mit überprüfbaren Anmeldeinformationen |
> | microsoft.directory/verifiableCredentials/configuration/contracts/allProperties/read | Lesen eines Vertrags mit überprüfbaren Anmeldeinformationen |
> | microsoft.directory/verifiableCredentials/configuration/contracts/allProperties/update | Aktualisieren eines Vertrags mit überprüfbaren Anmeldeinformationen |
> | microsoft.directory/verifiableCredentials/configuration/create | Erstellen der erforderlichen Konfiguration zum Erstellen und Verwalten von überprüfbaren Anmeldeinformationen |
> | microsoft.directory/verifiableCredentials/configuration/delete | Löschen der erforderlichen Konfiguration zum Erstellen und Verwalten von überprüfbaren Anmeldeinformationen sowie zum Löschen aller ihrer überprüfbaren Anmeldeinformationen |
> | microsoft.directory/verifiableCredentials/configuration/allProperties/read | Lesen der erforderlichen Konfiguration zum Erstellen und Verwalten von überprüfbaren Anmeldeinformationen |
> | microsoft.directory/verifiableCredentials/configuration/allProperties/update | Aktualisieren der erforderlichen Konfiguration zum Erstellen und Verwalten von überprüfbaren Anmeldeinformationen |
> | microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |

## <a name="azure-ad-joined-device-local-administrator"></a>Lokaler Administrator für in Azure AD eingebundenes Gerät

Diese Rolle kann nur als zusätzlicher lokaler Administrator in den [Geräteeinstellungen](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/) zugewiesen werden. Benutzer mit dieser Rolle werden auf allen Windows 10-Geräten, die in Azure Active Directory eingebunden sind, als Administratoren für den lokalen Computer festgelegt. Sie haben nicht die Möglichkeit zum Verwalten von Geräteobjekten in Azure Active Directory.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/groupSettings/standard/read | Lesen grundlegender Eigenschaften für Gruppeneinstellungen |
> | microsoft.directory/groupSettingTemplates/standard/read | Lesen grundlegender Eigenschaften von Vorlagen für Gruppeneinstellungen |

## <a name="azure-devops-administrator"></a>Azure DevOps-Administrator

Benutzer mit dieser Rolle können die Azure DevOps-Richtlinie verwalten, um die Erstellung einer neuen Azure DevOps-Organisation auf eine Gruppe von konfigurierbaren Benutzern oder Gruppen zu beschränken. Benutzer mit dieser Rolle können diese Richtlinie über jede Azure DevOps-Organisation verwalten, die von der Azure AD-Organisation des Unternehmens unterstützt wird. Diese Rolle gewährt keine anderen Azure DevOps-spezifischen Berechtigungen (z. B. Projektauflistungsadministratoren) in Azure DevOps-Organisationen, die von der Azure AD-Organisation des Unternehmens unterstützt werden.

Alle Azure DevOps-Richtlinien des Unternehmens können von Benutzern mit dieser Rolle verwaltet werden.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.azure.devOps/allEntities/allTasks | Lesen und Konfigurieren von Azure DevOps |

## <a name="azure-information-protection-administrator"></a>Azure Information Protection-Administrator

Benutzer mit dieser Rolle besitzen alle Berechtigungen für den Azure Information Protection-Dienst. Sie können Bezeichnungen für die Azure Information Protection-Richtlinie konfigurieren, Schutzvorlagen verwalten und den Schutz aktivieren. Diese Rolle gewährt keine Berechtigungen für Identity Protection Center, Privileged Identity Management, das Überwachen des Microsoft 365-Dienststatus oder Office 365 Security & Compliance Center.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/authorizationPolicy/standard/read | Lesen von Standardeigenschaften von Autorisierungsrichtlinien |
> | microsoft.azure.informationProtection/allEntities/allTasks | Verwalten sämtlicher Aspekte von Azure Information Protection |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Service Health im Microsoft 365 Admin Center |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="b2c-ief-keyset-administrator"></a>B2C-IEF-Schlüsselsatzadministrator

Benutzer können die Richtlinienschlüssel und -geheimnisse für Tokenverschlüsselung, Tokensignaturen und Verschlüsselung/Entschlüsselung von Ansprüchen erstellen und verwalten. Durch das Hinzufügen neuer Schlüssel zu vorhandenen Schlüsselcontainern kann dieser Administrator mit eingeschränkten Rechten bei Bedarf Rollover für Geheimnisse ausführen, ohne dass dies Auswirkungen auf vorhandene Anwendungen hat.  Diese Benutzer können den vollständigen Inhalt dieser Geheimnisse und deren Ablaufdaten anzeigen – auch nach deren Erstellung.

> [!IMPORTANT]
> Dies ist eine sensible Rolle.  Die Administratorrolle für Schlüsselsätze muss sorgfältig überwacht und mit Bedacht für Präproduktion und Produktion zugewiesen werden.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/b2cTrustFrameworkKeySet/allProperties/allTasks | Lesen und Aktualisieren sämtlicher Eigenschaften von Autorisierungsrichtlinien |

## <a name="b2c-ief-policy-administrator"></a>B2C-IEF-Richtlinienadministrator

Benutzer mit dieser Rolle können alle benutzerdefinierten Richtlinien in Azure AD B2C erstellen, lesen, aktualisieren und löschen und haben daher vollständige Kontrolle über das Identity Experience Framework in der zugehörigen Azure AD B2C-Organisation. Durch das Bearbeiten von Richtlinien können diese Benutzer einen direkten Verbund mit externen Identitätsanbietern einrichten, das Verzeichnisschema und alle benutzerseitigen Inhalte (HTML, CSS, JavaScript) sowie die Anforderungen für das Abschließen einer Authentifizierung ändern, neue Benutzer erstellen, Benutzerdaten an externe Systeme senden (einschließlich einer vollständigen Migration) und alle Benutzerinformationen bearbeiten (einschließlich vertraulicher Felder wie Kennwörter und Telefonnummern). Andererseits kann diese Rolle keine Verschlüsselungsschlüssel ändern oder Geheimnisse für den Verbund in der Organisation bearbeiten.

> [!IMPORTANT]
> Der B2-IEF-Richtlinienadministrator ist eine streng vertrauliche Rolle, die nur sehr wenigen Benutzern für Organisationen in der Produktion zugewiesen werden sollte.  Aktivitäten dieser Benutzer sollten streng überwacht werden. Dies gilt vor allem für Organisationen in der Produktion.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/b2cTrustFrameworkPolicy/allProperties/allTasks | Lesen und Konfigurieren von Schlüsselsätzen in Azure Active Directory B2C |

## <a name="billing-administrator"></a>Abrechnungsadministrator

Tätigt Käufe, verwaltet Abonnements und Supporttickets und überwacht die Dienstintegrität.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/organization/basic/update | Aktualisieren grundlegender Eigenschaften für Organisationen |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
> | microsoft.commerce.billing/allEntities/allTasks | Verwalten sämtlicher Aspekte der Office 365-Abrechnung |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Service Health im Microsoft 365 Admin Center |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="cloud-app-security-administrator"></a>Cloud App Security-Administrator

Benutzer mit dieser Rolle verfügen über vollständige Berechtigungen in Cloud App Security. Sie können Administratoren, MCAS-Richtlinien (Microsoft Cloud App Security) und -Einstellungen hinzufügen, Protokolle hochladen und Governanceaktionen ausführen.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/cloudAppSecurity/allProperties/allTasks | Erstellen und Löschen aller Ressourcen sowie Lesen und Aktualisieren von Standardeigenschaften in Microsoft Cloud App Security |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="cloud-application-administrator"></a>Cloudanwendungsadministrator

Benutzer mit dieser Rolle haben die gleichen Berechtigungen wie die Rolle des Anwendungsadministrators, mit Ausnahme der Möglichkeit, den Anwendungsproxy zu verwalten. Diese Rolle ermöglicht die Erstellung und Verwaltung aller Aspekte von Unternehmensanwendungen und Anwendungsregistrierungen. Benutzer, denen diese Rolle zugewiesen wurde, werden bei der Erstellung neuer Anwendungsregistrierungen oder Unternehmensanwendungen nicht als Besitzer hinzugefügt.

Diese Rolle ermöglicht auch die Zustimmung zu delegierten Berechtigungen und Anwendungsberechtigungen (mit Ausnahme von Anwendungsberechtigungen für Microsoft Graph und Azure AD Graph).

> [!IMPORTANT]
> Aufgrund dieser Ausnahme können Sie immer noch Berechtigungen für _andere_ Apps (z. B. nicht von Microsoft stammende Apps oder von Ihnen registrierte Apps) zustimmen. Sie können diese Berechtigungen weiterhin im Rahmen der App-Registrierung _anfordern_. Für das _Erteilen_ dieser Berechtigungen (d. h. die Zustimmung) ist jedoch ein Administrator mit höheren Rechten (z. B. ein globaler Administrator) erforderlich.
>
>Diese Rolle ermöglicht die Verwaltung von Anmeldeinformationen für Anwendungen. Benutzer, die dieser Rolle zugewiesen sind, können einer Anwendung Anmeldeinformationen hinzufügen und diese Anmeldeinformationen verwenden, um die Anwendung zu imitieren. Wenn der Identität der Anwendung der Zugriff auf eine Ressource gewährt wurde, z. B. die Berechtigung, Benutzer oder andere Objekte zu erstellen oder zu aktualisieren, kann ein dieser Rolle zugewiesener Benutzer diese Aktionen ausführen, während er die Identität der Anwendung annimmt. Diese Fähigkeit, die Identität der Anwendung anzunehmen, bedeutet ggf. eine Rechteerweiterung im Vergleich zu den Rollenzuweisungen des Benutzers. Beachten Sie, dass das Zuweisen eines Benutzers zur Anwendungsadministratorrolle ihm die Möglichkeit gibt, die Identität einer Anwendung anzunehmen.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/applications/create | Erstellen aller Anwendungstypen |
> | microsoft.directory/applications/delete | Löschen aller Anwendungstypen |
> | microsoft.directory/applications/appRoles/update | Aktualisieren der appRoles-Eigenschaft für alle Anwendungstypen |
> | microsoft.directory/applications/audience/update | Aktualisieren der audience-Eigenschaft für Anwendungen |
> | microsoft.directory/applications/authentication/update | Aktualisieren der Authentifizierung für alle Anwendungstypen |
> | microsoft.directory/applications/basic/update | Aktualisieren grundlegender Eigenschaften für Anwendungen |
> | microsoft.directory/applications/credentials/update | Aktualisieren von Anwendungsanmeldeinformationen |
> | microsoft.directory/applications/extensionProperties/update | Aktualisieren von Erweiterungseigenschaften von Anwendungen |
> | microsoft.directory/applications/notes/update | Aktualisieren von Anwendungshinweisen |
> | microsoft.directory/applications/owners/update | Aktualisieren von Anwendungsbesitzern |
> | microsoft.directory/applications/permissions/update | Aktualisieren von verfügbar gemachten und erforderlichen Berechtigungen für alle Anwendungstypen |
> | microsoft.directory/applications/policies/update | Aktualisieren von Anwendungsrichtlinien |
> | microsoft.directory/applications/tag/update | Aktualisieren von Anwendungstags |
> | microsoft.directory/applications/verification/update | Aktualisieren der applicationsverification-Eigenschaft |
> | microsoft.directory/applications/synchronization/standard/read | Lesen von Bereitstellungseinstellungen, die dem Anwendungsobjekt zugeordnet sind |
> | microsoft.directory/applicationTemplates/instantiate | Instanziieren von Kataloganwendungen über Anwendungsvorlagen |
> | microsoft.directory/auditLogs/allProperties/read | Lesen sämtlicher Eigenschaften von Überwachungsprotokollen (einschließlich privilegierter Eigenschaften) |
> | microsoft.directory/deletedItems.applications/delete | Dauerhaftes Löschen von Anwendungen, die nicht mehr wiederhergestellt werden können |
> | microsoft.directory/deletedItems.applications/restore | Wiederherstellen vorläufig gelöschter Anwendungen in ihrem ursprünglichen Zustand |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Erstellen und Löschen von OAuth 2.0-Berechtigungszuweisungen und Lesen und Aktualisieren aller Eigenschaften |
> | microsoft.directory/applicationPolicies/create | Erstellen von Anwendungsrichtlinien |
> | microsoft.directory/applicationPolicies/delete | Löschen von Anwendungsrichtlinien |
> | microsoft.directory/applicationPolicies/standard/read | Lesen der Standardeigenschaften von Anwendungsrichtlinien |
> | microsoft.directory/applicationPolicies/owners/read | Lesen der Besitzer von Anwendungsrichtlinien |
> | microsoft.directory/applicationPolicies/policyAppliedTo/read | Lesen der Liste der auf Objekte angewendeten Anwendungsrichtlinien |
> | microsoft.directory/applicationPolicies/basic/update | Aktualisieren der Standardeigenschaften von Anwendungsrichtlinien |
> | microsoft.directory/applicationPolicies/owners/update | Aktualisieren der owner-Eigenschaft von Anwendungsrichtlinien |
> | microsoft.directory/provisioningLogs/allProperties/read | Lesen aller Eigenschaften von Bereitstellungsprotokollen |
> | microsoft.directory/servicePrincipals/create | Erstellen von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/delete | Löschen von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/disable | Deaktivieren von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/enable | Aktivieren von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | Verwalten von Anmeldeinformationen für das einmalige Anmelden per Kennwort für Dienstprinzipale |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Verwalten der Geheimnisse und Anmeldeinformationen für die Anwendungsbereitstellung |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | Starten, Neustarten und Anhalten von Synchronisierungsaufträgen für die Anwendungsbereitstellung |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | Erstellen und Verwalten von Synchronisierungsaufträgen und -schemas für die Anwendungsbereitstellung |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | Lesen von Anmeldeinformationen für das einmalige Anmelden per Kennwort für Dienstprinzipale |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-application-admin | Erteilen der Zustimmung zu Anwendungsberechtigungen und delegierten Berechtigungen für einen oder alle Benutzer (mit Ausnahme von Anwendungsberechtigungen für Microsoft Graph und Azure AD Graph) |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aktualisieren von Rollenzuweisungen für Dienstprinzipale |
> | microsoft.directory/servicePrincipals/audience/update | Aktualisieren der Zielgruppeneigenschaften für Dienstprinzipale |
> | microsoft.directory/servicePrincipals/authentication/update | Aktualisieren der Authentifizierungseigenschaften für Dienstprinzipale |
> | microsoft.directory/servicePrincipals/basic/update | Aktualisieren grundlegender Eigenschaften für Dienstprinzipale |
> | microsoft.directory/servicePrincipals/credentials/update | Aktualisieren der Anmeldeinformationen von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/notes/update | Aktualisieren von Hinweisen zu Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/owners/update | Aktualisieren der Besitzer von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/permissions/update | Aktualisieren der Berechtigungen von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/policies/update | Aktualisieren der Richtlinien für Dienstprinzipale |
> | microsoft.directory/servicePrincipals/tag/update | Aktualisieren der tag-Eigenschaft für Dienstprinzipale |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | Lesen von Bereitstellungseinstellungen, die Ihrem Dienstprinzipal zugeordnet sind |
> | microsoft.directory/signInReports/allProperties/read | Lesen sämtlicher Eigenschaften für Anmeldeberichte (einschließlich privilegierter Eigenschaften) |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Service Health im Microsoft 365 Admin Center |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="cloud-device-administrator"></a>Cloudgeräteadministrator

Benutzer mit dieser Rolle können Geräte in Azure AD aktivieren, deaktivieren und löschen sowie Windows 10-BitLocker-Schlüssel (falls vorhanden) im Azure-Portal lesen. Die Rolle gewährt keine Berechtigungen für die Verwaltung anderer Eigenschaften auf dem Gerät.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | Lesen sämtlicher Eigenschaften von Überwachungsprotokollen (einschließlich privilegierter Eigenschaften) |
> | microsoft.directory/authorizationPolicy/standard/read | Lesen von Standardeigenschaften von Autorisierungsrichtlinien |
> | microsoft.directory/bitlockerKeys/key/read | Lesen von BitLocker-Metadaten und -Schlüsseln auf Geräten |
> | microsoft.directory/devices/delete | Löschen von Geräten aus Azure AD |
> | microsoft.directory/devices/disable | Deaktivieren von Geräten in Azure AD |
> | microsoft.directory/devices/enable | Aktivieren von Geräten in Azure AD |
> | microsoft.directory/deviceManagementPolicies/standard/read | Lesen der Standardeigenschaften von Anwendungsrichtlinien zur Geräteverwaltung |
> | microsoft.directory/deviceManagementPolicies/basic/update | Aktualisieren grundlegender Eigenschaften für Anwendungsrichtlinien der Geräteverwaltung |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | Lesen der Standardeigenschaften von Richtlinien zur Geräteregistrierung |
> | microsoft.directory/deviceRegistrationPolicy/basic/update | Aktualisieren grundlegender Eigenschaften von Richtlinien zur Geräteregistrierung |
> | microsoft.directory/signInReports/allProperties/read | Lesen sämtlicher Eigenschaften für Anmeldeberichte (einschließlich privilegierter Eigenschaften) |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Service Health im Microsoft 365 Admin Center |

## <a name="compliance-administrator"></a>Complianceadministrator

Benutzer mit dieser Rolle verfügen über Berechtigungen zum Verwalten von Compliancefeatures im Microsoft 365 Compliance Center, Microsoft 365 Admin Center, Azure, und Office 365 Security & Compliance Center. Zugewiesene Personen können auch alle Features im Exchange Admin Center sowie im Teams und Skype for Business Admin Center verwalten und Supporttickets für Azure und Microsoft 365 erstellen. Weitere Informationen finden Sie unter [Informationen zu Administratorrollen von Microsoft 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

Geben Sie in | Möglich
----- | ----------
[Microsoft 365 Compliance Center](https://protection.office.com) | Schützen und Verwalten Ihrer Organisationsdaten für Microsoft 365-Dienste<br>Verwalten von Konformitätwarnungen
[Compliance Manager](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Nachverfolgen, Zuweisen und Überprüfen der Einhaltung gesetzlicher Vorschriften durch Ihre Organisation
[Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Verwalten der Datengovernance<br>Durchführen von Untersuchung zu rechtlichen Aspekten und von Daten<br>Verwalten von DRS-Anforderungen<br><br>Diese Rolle verfügt über die gleichen Berechtigungen wie die Rollengruppe [Complianceadministrator](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) der rollenbasierten Zugriffssteuerung im Office 365 Security & Compliance Center.
[Intune](/intune/role-based-access-control) | Anzeigen aller Intune-Überwachungsdaten
[Cloud App Security](/cloud-app-security/manage-admins) | Verfügt über schreibgeschützten Zugriff und kann Warnungen verwalten<br>Kann Dateirichtlinien erstellen und ändern und Dateigovernanceaktionen zulassen<br>Kann alle unter „Datenverwaltung“ integrierten Berichte anzeigen

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
> | microsoft.directory/entitlementManagement/allProperties/read | Lesen sämtlicher Eigenschaften in der Azure AD-Berechtigungsverwaltung |
> | microsoft.office365.complianceManager/allEntities/allTasks | Verwalten sämtlicher Aspekte von Office 365 Compliance-Manager |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Service Health im Microsoft 365 Admin Center |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="compliance-data-administrator"></a>Administrator für Konformitätsdaten

Benutzer mit dieser Rolle verfügen über Berechtigungen zum Nachverfolgen von Daten im Microsoft 365 Compliance Center, Microsoft 365 Admin Center und in Azure. Die Benutzer können auch Compliancedaten im Exchange Admin Center, in Compliance-Manager sowie im Teams und Skype for Business Admin Center nachverfolgen und Supporttickets für Azure und Microsoft 365 erstellen. [In dieser Dokumentation](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) finden Sie ausführliche Informationen zu den Unterschieden zwischen Complianceadministrator und Konformitätsdatenadministrator.

Geben Sie in | Möglich
----- | ----------
[Microsoft 365 Compliance Center](https://protection.office.com) | Überwachen von compliancerelevanten Richtlinien in Microsoft 365-Diensten<br>Verwalten von Konformitätwarnungen
[Compliance Manager](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Nachverfolgen, Zuweisen und Überprüfen der Einhaltung gesetzlicher Vorschriften durch Ihre Organisation
[Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Verwalten der Datengovernance<br>Durchführen von Untersuchung zu rechtlichen Aspekten und von Daten<br>Verwalten von DRS-Anforderungen<br><br>Diese Rolle verfügt über die gleichen Berechtigungen wie die Rollengruppe [Compliancedatenadministrator](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) der rollenbasierten Zugriffssteuerung im Office 365 Security & Compliance Center.
[Intune](/intune/role-based-access-control) | Anzeigen aller Intune-Überwachungsdaten
[Cloud App Security](/cloud-app-security/manage-admins) | Verfügt über schreibgeschützten Zugriff und kann Warnungen verwalten<br>Kann Dateirichtlinien erstellen und ändern und Dateigovernanceaktionen zulassen<br>Kann alle unter „Datenverwaltung“ integrierten Berichte anzeigen

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/authorizationPolicy/standard/read | Lesen von Standardeigenschaften von Autorisierungsrichtlinien |
> | microsoft.directory/cloudAppSecurity/allProperties/allTasks | Erstellen und Löschen aller Ressourcen sowie Lesen und Aktualisieren von Standardeigenschaften in Microsoft Cloud App Security |
> | microsoft.azure.informationProtection/allEntities/allTasks | Verwalten sämtlicher Aspekte von Azure Information Protection |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
> | microsoft.office365.complianceManager/allEntities/allTasks | Verwalten sämtlicher Aspekte von Office 365 Compliance-Manager |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Service Health im Microsoft 365 Admin Center |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="conditional-access-administrator"></a>Administrator für den bedingten Zugriff

Benutzer mit dieser Rolle können Azure Active Directory-Einstellungen für den bedingten Zugriff verwalten.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/conditionalAccessPolicies/create | Erstellen von Richtlinien für den bedingten Zugriff |
> | microsoft.directory/conditionalAccessPolicies/delete | Löschen von Richtlinien für den bedingten Zugriff |
> | microsoft.directory/conditionalAccessPolicies/standard/read | Lesen des bedingten Zugriffs für Richtlinien |
> | microsoft.directory/conditionalAccessPolicies/owners/read | Lesen der Besitzer von Richtlinien für den bedingten Zugriff |
> | microsoft.directory/conditionalAccessPolicies/policyAppliedTo/read | Lesen der Eigenschaft „Angewendet auf“ für Richtlinien für den bedingten Zugriff |
> | microsoft.directory/conditionalAccessPolicies/basic/update | Aktualisieren grundlegender Eigenschaften der Richtlinien für den bedingten Zugriff |
> | microsoft.directory/conditionalAccessPolicies/owners/update | Aktualisieren der Besitzer von Richtlinien für den bedingten Zugriff |
> | microsoft.directory/conditionalAccessPolicies/tenantDefault/update | Aktualisieren des Standardmandanten für Richtlinien für den bedingten Zugriff |
> | microsoft.directory/crossTenantAccessPolicies/create | Erstellen von mandantenübergreifenden Zugriffsrichtlinien |
> | microsoft.directory/crossTenantAccessPolicies/delete | Löschen von mandantenübergreifenden Zugriffsrichtlinien |
> | microsoft.directory/crossTenantAccessPolicies/standard/read | Lesen grundlegender Eigenschaften von mandantenübergreifenden Zugriffsrichtlinien |
> | microsoft.directory/crossTenantAccessPolicies/owners/read | Lesen der Besitzer von mandantenübergreifenden Zugriffsrichtlinien |
> | microsoft.directory/crossTenantAccessPolicies/policyAppliedTo/read | Lesen der policyAppliedTo-Eigenschaft von mandantenübergreifenden Zugriffsrichtlinien |
> | microsoft.directory/crossTenantAccessPolicies/basic/update | Aktualisieren grundlegender Eigenschaften von mandantenübergreifenden Zugriffsrichtlinien |
> | microsoft.directory/crossTenantAccessPolicies/owners/update | Aktualisieren der Besitzer von mandantenübergreifenden Zugriffsrichtlinien |
> | microsoft.directory/crossTenantAccessPolicies/tenantDefault/update | Aktualisieren des Standardmandanten für mandantenübergreifende Zugriffsrichtlinien |

## <a name="customer-lockbox-access-approver"></a>Genehmigende Person für den LockBox-Kundenzugriff

Verwaltet [Kunden-Lockbox-Anforderungen](/office365/admin/manage/customer-lockbox-requests) in Ihrer Organisation. Sie erhalten E-Mail-Benachrichtigungen für Kunden-Lockbox-Anforderungen und können Anforderungen über das Microsoft 365 Admin Center genehmigen und ablehnen. Außerdem können sie das Feature Kunden-Lockbox aktivieren und deaktivieren. Nur globale Administratoren können die Kennwörter von Personen zurücksetzen, die dieser Rolle zugewiesen sind.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.office365.lockbox/allEntities/allTasks | Verwalten sämtlicher Aspekte der Kunden-Lockbox |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="desktop-analytics-administrator"></a>Desktop Analytics-Administrator

Benutzer in dieser Rolle können den Desktop Analytics-Dienst verwalten. Dies umfasst die Möglichkeit zum Anzeigen des Assetbestands, Erstellen von Bereitstellungsplänen sowie zum Anzeigen des Bereitstellungs- und Integritätsstatus.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/authorizationPolicy/standard/read | Lesen von Standardeigenschaften von Autorisierungsrichtlinien |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
> | microsoft.office365.desktopAnalytics/allEntities/allTasks | Verwalten sämtlicher Aspekte von Desktop Analytics |

## <a name="directory-readers"></a>Rolle „Verzeichnis lesen“

Benutzer in dieser Rolle können grundlegende Verzeichnisinformationen lesen. Diese Rolle sollte für folgende Zwecke verwendet werden:

* Gewähren von Lesezugriff für eine bestimmte Gruppe von Gastbenutzern statt für alle Gastbenutzer.
* Gewähren von Zugriff auf das Azure-Portal für eine bestimmten Gruppe von Benutzern ohne Administratorberechtigung, wenn „Zugriff auf Azure AD-Portal auf Administratoren beschränken“ auf „Ja“ festgelegt ist.
* Gewähren von Zugriff auf das Verzeichnis für Dienstprinzipale, wenn „Directory.Read.All“ keine Option darstellt.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/administrativeUnits/standard/read | Lesen grundlegender Eigenschaften für Verwaltungseinheiten |
> | microsoft.directory/administrativeUnits/members/read | Lesen der Mitglieder von Verwaltungseinheiten |
> | microsoft.directory/applications/standard/read | Lesen der Standardeigenschaften von Anwendungen |
> | microsoft.directory/applications/owners/read | Lesen der Besitzer von Anwendungen |
> | microsoft.directory/applications/policies/read | Lesen der Richtlinien von Anwendungen |
> | microsoft.directory/contacts/standard/read | Lesen grundlegender Eigenschaften für Kontakte in Azure AD |
> | microsoft.directory/contacts/memberOf/read | Lesen der Gruppenmitgliedschaft für alle Kontakte in Azure AD |
> | microsoft.directory/contracts/standard/read | Lesen grundlegender Eigenschaften für Partnerverträge |
> | microsoft.directory/devices/standard/read | Lesen grundlegender Eigenschaften für Geräte |
> | microsoft.directory/devices/memberOf/read | Lesen von Gerätemitgliedschaften |
> | microsoft.directory/devices/registeredOwners/read | Lesen von registrierten Besitzern von Geräten |
> | microsoft.directory/devices/registeredUsers/read | Lesen von registrierten Benutzern von Geräten |
> | microsoft.directory/directoryRoles/standard/read | Aktualisieren grundlegender Eigenschaften in Azure AD-Rollen |
> | microsoft.directory/directoryRoles/eligibleMembers/read | Lesen von berechtigten Mitgliedern von Azure AD-Rollen |
> | microsoft.directory/directoryRoles/members/read | Lesen sämtlicher Mitglieder von Azure AD-Rollen |
> | microsoft.directory/domains/standard/read | Lesen grundlegender Eigenschaften für Domänen |
> | microsoft.directory/groups/standard/read | Lesen der Standardeigenschaften von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen eingeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/appRoleAssignments/read | Lesen von Anwendungsrollenzuweisungen von Gruppen |
> | microsoft.directory/groups/memberOf/read | Lesen der memberOf-Eigenschaft von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen eingeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/members/read | Lesen der Mitglieder von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen eingeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/owners/read | Lesen der Besitzer von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen eingeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/settings/read | Lesen der Einstellungen von Gruppen |
> | microsoft.directory/groupSettings/standard/read | Lesen grundlegender Eigenschaften für Gruppeneinstellungen |
> | microsoft.directory/groupSettingTemplates/standard/read | Lesen grundlegender Eigenschaften von Vorlagen für Gruppeneinstellungen |
> | microsoft.directory/oAuth2PermissionGrants/standard/read | Lesen grundlegender Eigenschaften für OAuth 2.0-Berechtigungszuweisungen |
> | microsoft.directory/organization/standard/read | Lesen grundlegender Eigenschaften für Organisationen |
> | microsoft.directory/organization/trustedCAsForPasswordlessAuth/read | Lesen von vertrauenswürdigen Zertifizierungsstellen für die kennwortlose Authentifizierung |
> | microsoft.directory/applicationPolicies/standard/read | Lesen der Standardeigenschaften von Anwendungsrichtlinien |
> | microsoft.directory/roleAssignments/standard/read | Lesen grundlegender Eigenschaften für Rollenzuweisungen |
> | microsoft.directory/roleDefinitions/standard/read | Lesen grundlegender Eigenschaften für Rollendefinitionen |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Lesen der Rollenzuweisungen von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/appRoleAssignments/read | Lesen der Rollenzuweisungen, die Dienstprinzipalen zugewiesen sind |
> | microsoft.directory/servicePrincipals/standard/read | Lesen sämtlicher Eigenschaften von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/memberOf/read | Lesen von Gruppenmitgliedschaften für Dienstprinzipale |
> | microsoft.directory/servicePrincipals/oAuth2PermissionGrants/read | Lesen delegierter Berechtigungsgewährungen für Dienstprinzipale |
> | microsoft.directory/servicePrincipals/owners/read | Lesen der Besitzer von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/ownedObjects/read | Lesen der Objekte im Besitz von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/policies/read | Lesen der Richtlinien von Dienstprinzipalen |
> | microsoft.directory/subscribedSkus/standard/read | Lesen grundlegender Eigenschaften für Abonnements |
> | microsoft.directory/users/standard/read | Lesen grundlegender Eigenschaften für Benutzer |
> | microsoft.directory/users/appRoleAssignments/read | Lesen von Anwendungsrollenzuweisungen für Benutzer |
> | microsoft.directory/users/deviceForResourceAccount/read | Lesen von deviceForResourceAccount von Benutzern |
> | microsoft.directory/users/directReports/read | Lesen von direkten Berichten für Benutzer |
> | microsoft.directory/users/licenseDetails/read | Lesen von Lizenzdetails von Benutzern |
> | microsoft.directory/users/manager/read | Lesen der Manager von Benutzern |
> | microsoft.directory/users/memberOf/read | Lesen von Gruppenmitgliedschaften von Benutzern |
> | microsoft.directory/users/oAuth2PermissionGrants/read | Lesen delegierter Berechtigungszuweisungen für Benutzer |
> | microsoft.directory/users/ownedDevices/read | Lesen von Geräten im Besitz von Benutzern |
> | microsoft.directory/users/ownedObjects/read | Lesen von Objekten im Besitz von Benutzern |
> | microsoft.directory/users/photo/read | Lesen des Fotos von Benutzern |
> | microsoft.directory/users/registeredDevices/read | Lesen von registrierten Geräten von Benutzern |
> | microsoft.directory/users/scopedRoleMemberOf/read | Lesen der Benutzermitgliedschaft einer Azure AD Rolle, die für eine Verwaltungseinheit gilt |

## <a name="directory-synchronization-accounts"></a>Konten zur Verzeichnissynchronisierung

Darf nicht verwendet werden. Diese Rolle wird automatisch dem Azure AD Connect-Dienst zugewiesen und ist weder für eine andere Verwendung vorgesehen, noch wird eine andere Verwendung unterstützt.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/applications/create | Erstellen aller Anwendungstypen |
> | microsoft.directory/applications/delete | Löschen aller Anwendungstypen |
> | microsoft.directory/applications/appRoles/update | Aktualisieren der appRoles-Eigenschaft für alle Anwendungstypen |
> | microsoft.directory/applications/audience/update | Aktualisieren der audience-Eigenschaft für Anwendungen |
> | microsoft.directory/applications/authentication/update | Aktualisieren der Authentifizierung für alle Anwendungstypen |
> | microsoft.directory/applications/basic/update | Aktualisieren grundlegender Eigenschaften für Anwendungen |
> | microsoft.directory/applications/credentials/update | Aktualisieren von Anwendungsanmeldeinformationen |
> | microsoft.directory/applications/notes/update | Aktualisieren von Anwendungshinweisen |
> | microsoft.directory/applications/owners/update | Aktualisieren von Anwendungsbesitzern |
> | microsoft.directory/applications/permissions/update | Aktualisieren von verfügbar gemachten und erforderlichen Berechtigungen für alle Anwendungstypen |
> | microsoft.directory/applications/policies/update | Aktualisieren von Anwendungsrichtlinien |
> | microsoft.directory/applications/tag/update | Aktualisieren von Anwendungstags |
> | microsoft.directory/authorizationPolicy/standard/read | Lesen von Standardeigenschaften von Autorisierungsrichtlinien |
> | microsoft.directory/organization/dirSync/update | Aktualisieren der Synchronisierungseigenschaft für das Organisationsverzeichnis |
> | microsoft.directory/policies/create | Erstellen von Richtlinien in Azure AD |
> | microsoft.directory/policies/delete | Löschen von Richtlinien in Azure AD |
> | microsoft.directory/policies/standard/read | Lesen grundlegender Eigenschaften für Richtlinien |
> | microsoft.directory/policies/owners/read | Lesen der Besitzer von Richtlinien |
> | microsoft.directory/policies/policyAppliedTo/read | Lesen der policies.policyAppliedTo-Eigenschaft |
> | microsoft.directory/policies/basic/update | Aktualisieren grundlegender Eigenschaften für Richtlinien |
> | microsoft.directory/policies/owners/update | Aktualisieren der Besitzer von Richtlinien |
> | microsoft.directory/policies/tenantDefault/update | Aktualisieren von Standardorganisationsrichtlinien |
> | microsoft.directory/servicePrincipals/create | Erstellen von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/delete | Löschen von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/enable | Aktivieren von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/disable | Deaktivieren von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | Verwalten von Anmeldeinformationen für das einmalige Anmelden per Kennwort für Dienstprinzipale |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | Lesen von Anmeldeinformationen für das einmalige Anmelden per Kennwort für Dienstprinzipale |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Lesen der Rollenzuweisungen von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/appRoleAssignments/read | Lesen der Rollenzuweisungen, die Dienstprinzipalen zugewiesen sind |
> | microsoft.directory/servicePrincipals/standard/read | Lesen sämtlicher Eigenschaften von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/memberOf/read | Lesen von Gruppenmitgliedschaften für Dienstprinzipale |
> | microsoft.directory/servicePrincipals/oAuth2PermissionGrants/read | Lesen delegierter Berechtigungsgewährungen für Dienstprinzipale |
> | microsoft.directory/servicePrincipals/owners/read | Lesen der Besitzer von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/ownedObjects/read | Lesen der Objekte im Besitz von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/policies/read | Lesen der Richtlinien von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aktualisieren von Rollenzuweisungen für Dienstprinzipale |
> | microsoft.directory/servicePrincipals/audience/update | Aktualisieren der Zielgruppeneigenschaften für Dienstprinzipale |
> | microsoft.directory/servicePrincipals/authentication/update | Aktualisieren der Authentifizierungseigenschaften für Dienstprinzipale |
> | microsoft.directory/servicePrincipals/basic/update | Aktualisieren grundlegender Eigenschaften für Dienstprinzipale |
> | microsoft.directory/servicePrincipals/credentials/update | Aktualisieren der Anmeldeinformationen von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/notes/update | Aktualisieren von Hinweisen zu Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/owners/update | Aktualisieren der Besitzer von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/permissions/update | Aktualisieren der Berechtigungen von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/policies/update | Aktualisieren der Richtlinien für Dienstprinzipale |
> | microsoft.directory/servicePrincipals/tag/update | Aktualisieren der tag-Eigenschaft für Dienstprinzipale |

## <a name="directory-writers"></a>Verzeichnis schreiben

Benutzer mit dieser Rolle können grundlegende Informationen von Benutzern, Gruppen und Dienstprinzipalen lesen und aktualisieren. Weisen Sie diese Rolle nur Anwendungen zu, die das [Zustimmungsframework](../develop/quickstart-register-app.md) nicht unterstützen. Diese Rolle sollte keinem Benutzer zugewiesen werden.

> [!div class="mx-tableFixed"]
> | Aktionen | Beschreibung |
> | --- | --- |
> | microsoft.directory/applications/extensionProperties/update | Aktualisieren von Erweiterungseigenschaften von Anwendungen |
> | microsoft.directory/groups/assignLicense | Zuweisen von Produktlizenzen zu Gruppen für die gruppenbasierte Lizenzierung |
> | microsoft.directory/groups/create | Erstellen von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/reprocessLicenseAssignment | Erneutes Verarbeiten von Lizenzzuweisungen für die gruppenbasierte Lizenzierung |
> | microsoft.directory/groups/basic/update | Aktualisieren grundlegender Eigenschaften von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/classification/update | Aktualisieren der Klassifizierungseigenschaft von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/dynamicMembershipRule/update | Aktualisieren der Regel für eine dynamische Mitgliedschaft für Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/groupType/update | Aktualisieren von Eigenschaften, die sich auf den Gruppentyp von Sicherheitsgruppen und Microsoft 365-Gruppen auswirken (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/members/update | Aktualisieren der Mitglieder von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/onPremWriteBack/update | Aktualisieren von Azure Active Directory-Gruppen, die mit Azure AD Connect in die lokale Umgebung zurückgeschrieben werden sollen |
> | microsoft.directory/groups/owners/update | Aktualisieren der Besitzer von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/settings/update | Aktualisieren von Gruppeneinstellungen |
> | microsoft.directory/groups/visibility/update | Aktualisieren der visibility-Eigenschaft von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groupSettings/create | Create group settings (Gruppeneinstellungen erstellen) |
> | microsoft.directory/groupSettings/delete | Delete group settings (Gruppeneinstellungen löschen) |
> | microsoft.directory/groupSettings/basic/update | Aktualisieren grundlegender Eigenschaften für Gruppeneinstellungen |
> | microsoft.directory/oAuth2PermissionGrants/create | Erstellen von OAuth 2.0-Berechtigungszuweisungen |
> | microsoft.directory/oAuth2PermissionGrants/basic/update | Aktualisieren von OAuth 2.0-Berechtigungszuweisungen |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Verwalten der Geheimnisse und Anmeldeinformationen für die Anwendungsbereitstellung |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | Starten, Neustarten und Anhalten von Synchronisierungsaufträgen für die Anwendungsbereitstellung |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | Erstellen und Verwalten von Synchronisierungsaufträgen und -schemas für die Anwendungsbereitstellung |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | Gewähren des direkten Zugriffs auf Gruppendaten für einen Dienstprinzipal |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aktualisieren von Rollenzuweisungen für Dienstprinzipale |
> | microsoft.directory/users/assignLicense | Verwalten von Benutzerlizenzen |
> | microsoft.directory/users/create | Hinzufügen von Benutzern |
> | microsoft.directory/users/disable | Deaktivieren von Benutzern |
> | microsoft.directory/users/enable | Aktivieren von Benutzern |
> | microsoft.directory/users/invalidateAllRefreshTokens | Erzwingen der Abmeldung von Benutzern durch Ungültigmachen des Aktualisierungstokens |
> | microsoft.directory/users/inviteGuest | Einladen von Gastbenutzern |
> | microsoft.directory/users/reprocessLicenseAssignment | Erneutes Verarbeiten von Lizenzzuweisungen für Benutzer |
> | microsoft.directory/users/basic/update | Aktualisieren grundlegender Eigenschaften für Benutzer |
> | microsoft.directory/users/manager/update | Aktualisieren der Manager für Benutzer |
> | microsoft.directory/users/photo/update | Aktualisieren des Fotos von Benutzern |
> | microsoft.directory/users/userPrincipalName/update | Aktualisieren des Benutzerprinzipalnamens von Benutzern |

## <a name="domain-name-administrator"></a>Domänennamenadministrator

Benutzer mit dieser Rolle können Domänennamen verwalten (lesen, hinzufügen, überprüfen, aktualisieren und löschen). Sie können auch Verzeichnisinformationen zu Benutzern, Gruppen und Anwendungen lesen, da diese Objekte Domänenabhängigkeiten besitzen. Bei lokalen Umgebungen können Benutzer mit dieser Rolle Domänennamen für den Verbund konfigurieren, sodass zugeordnete Benutzer immer lokal authentifiziert werden. Diese Benutzer können sich dann über einmaliges Anmelden (Single Sign-On, SSO) bei Azure AD-basierten Diensten mit ihren lokalen Kennwörtern anmelden. Verbundeinstellungen müssen über Azure AD Connect synchronisiert werden, damit Benutzer auch über Berechtigungen zum Verwalten von Azure AD Connect verfügen.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/domains/allProperties/allTasks | Erstellen und Löschen von Domänen sowie Lesen und Aktualisieren aller Eigenschaften |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="dynamics-365-administrator"></a>Dynamics 365-Administrator

Benutzer mit dieser Rolle besitzen globale Berechtigungen innerhalb von Microsoft Dynamics 365 Online, wenn der Dienst verfügbar ist, und können Supporttickets verwalten und die Dienstintegrität überwachen. Weitere Informationen finden Sie unter [Verwenden der Dienstadministratorrolle zum Verwalten Ihrer Azure AD-Organisation](/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

> [!NOTE]
> In Microsoft Graph-API und Azure AD PowerShell wird diese Rolle als „Dynamics 365-Dienstadministrator“ bezeichnet. Im [Azure-Portal](https://portal.azure.com) lautet sie „Dynamics 365-Administrator“.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
> | microsoft.dynamics365/allEntities/allTasks | Verwalten sämtlicher Aspekte von Dynamics 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Service Health im Microsoft 365 Admin Center |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="edge-administrator"></a>Edgeadministrator

Benutzer mit dieser Rolle können die Unternehmenswebsiteliste erstellen und verwalten, die für den Internet Explorer-Modus in Microsoft Edge erforderlich ist. Diese Rolle gewährt Berechtigungen zum Erstellen, Bearbeiten und Veröffentlichen der Websiteliste und ermöglicht darüber hinaus den Zugriff auf die Verwaltung von Supporttickets. [Weitere Informationen](https://go.microsoft.com/fwlink/?linkid=2165707)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.edge/allEntities/allProperties/allTasks | Verwalten sämtlicher Aspekte von Microsoft Edge |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="exchange-administrator"></a>Exchange-Administrator

Benutzer mit dieser Rolle besitzen globale Berechtigungen in Microsoft Exchange Online, wenn der Dienst verfügbar ist. Außerdem haben sie die Möglichkeit zum Erstellen und Verwalten aller Microsoft 365-Gruppen, Verwalten von Supporttickets und Überwachen der Dienstintegrität. Weitere Informationen finden Sie unter [Informationen zu Administratorrollen von Microsoft 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> In Microsoft Graph-API und Azure AD PowerShell wird diese Rolle als „Exchange-Dienstadministrator“ bezeichnet. Im [Azure-Portal](https://portal.azure.com) lautet sie „Exchange-Administrator“. Im [Exchange Admin Center](https://go.microsoft.com/fwlink/p/?LinkID=529144) lautet sie „Exchange Online-Administrator“.

> [!div class="mx-tableFixed"]
> | Aktionen | Beschreibung |
> | --- | --- |
> | microsoft.directory/deletedItems.groups/delete | Dauerhaftes Löschen von Gruppen, die nicht mehr wiederhergestellt werden können. |
> | microsoft.directory/deletedItems.groups/restore | Wiederherstellen des ursprünglichen Zustands von soft deleted-Gruppen |
> | microsoft.directory/groups/hiddenMembers/read | Lesen der ausgeblendeten Mitglieder von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen eingeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.unified/create | Erstellen von Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.unified/delete | Löschen von Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.unified/restore | Wiederherstellen von Microsoft 365-Gruppen |
> | microsoft.directory/groups.unified/basic/update | Aktualisieren grundlegender Eigenschaften von Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.unified/members/update | Aktualisieren der Mitglieder von Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.unified/owners/update | Aktualisieren der Besitzer von Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
> | microsoft.office365.exchange/allEntities/basic/allTasks | Verwalten sämtlicher Aspekte von Exchange Online |
> | microsoft.office365.network/performance/allProperties/read | Lesen aller Netzwerkleistungseigenschaften im Microsoft 365 Admin Center |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Service Health im Microsoft 365 Admin Center |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Lesen von Office 365-Nutzungsberichten |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="exchange-recipient-administrator"></a>Administrator für Exchange-Empfänger

Benutzer mit dieser Rolle haben Lesezugriff auf Empfänger und Schreibzugriff auf die Attribute dieser Empfänger in Exchange Online. Weitere Informationen finden Sie unter [Exchange-Empfänger](/exchange/recipients/recipients).

> [!div class="mx-tableFixed"]
> | Aktionen | Beschreibung |
> | --- | --- |
> | microsoft.office365.exchange/allRecipients/allProperties/allTasks | Erstellen und Löschen aller Empfänger sowie Lesen und Aktualisieren aller Eigenschaften von Empfängern in Exchange Online |
> | microsoft.office365.exchange/messageTracking/allProperties/allTasks | Verwalten aller Aufgaben in der Nachrichtennachverfolgung in Exchange Online |
> | microsoft.office365.exchange/migration/allProperties/allTasks | Verwalten aller Aufgaben im Zusammenhang mit der Migration von Empfängern in Exchange Online |

## <a name="external-id-user-flow-administrator"></a>Administrator für Benutzerflows mit externer ID

Benutzer mit dieser Rolle können Benutzerflows (auch als „integrierte“ Richtlinien bezeichnet) im Azure-Portal erstellen und verwalten. Sie können HTML-/CSS-/JavaScript-Inhalte anpassen, MFA-Anforderungen ändern, Ansprüche im Token auswählen, API-Connectors verwalten und Sitzungseinstellungen für alle Benutzerflows in der Azure AD-Organisation konfigurieren. Auf der anderen Seite bietet diese Rolle nicht die Möglichkeit, Benutzerdaten zu überprüfen oder Änderungen an Attributen vorzunehmen, die im Organisationsschema enthalten sind. Änderungen an Richtlinien des Identity Experience Framework (auch „benutzerdefinierte Richtlinien“ genannt) gehören ebenfalls nicht zum Berechtigungsumfang dieser Rolle.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/b2cUserFlow/allProperties/allTasks | Lesen und Konfigurieren von Benutzerattributen in Azure Active Directory B2C |

## <a name="external-id-user-flow-attribute-administrator"></a>Administrator für Benutzerflowattribute mit externer ID

Benutzer mit dieser Rolle können benutzerdefinierte Attribute, die für alle Benutzerflows in der Azure AD-Organisation verfügbar sind, hinzufügen und löschen.  Daher können Benutzer mit dieser Rolle dem Benutzerschema neue Elemente hinzufügen und diese ändern und haben damit Einfluss auf das Verhalten aller Benutzerflows. Indirekt kann dies auch ändern, welche Daten von Benutzern abgefragt und letztendlich als Ansprüche an Anwendungen gesendet werden.  Diese Rolle kann keine Benutzerflows bearbeiten.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/b2cUserAttribute/allProperties/allTasks | Lesen und Konfigurieren benutzerdefinierter Richtlinien in Azure Active Directory B2C |

## <a name="external-identity-provider-administrator"></a>Externer Identitätsanbieteradministrator

Dieser Administrator verwaltet den Verbund zwischen Azure AD-Organisationen und externen Identitätsanbietern.  Benutzer mit dieser Rolle können neue Identitätsanbieter hinzufügen und alle verfügbaren Einstellungen (z.B. Authentifizierungspfad, Dienst-ID, zugewiesene Schlüsselcontainer) konfigurieren.  Diese Benutzer können festlegen, dass die Azure AD-Organisation Authentifizierungen von externen Identitätsanbietern vertraut.  Die resultierenden Auswirkungen auf die Benutzerumgebung hängt vom Typ der Organisation ab:

* Azure AD-Organisationen für Mitarbeiter und Partner: Das Hinzufügen eines Verbunds (z. B. mit Gmail) hat sofortige Auswirkungen auf alle Gasteinladungen, die noch nicht eingelöst wurden. Weitere Informationen finden Sie unter [Hinzufügen von Google als Identitätsanbieter für B2B-Gastbenutzer](../external-identities/google-federation.md).
* Azure Active Directory B2C-Organisationen: Das Hinzufügen eines Verbunds (z. B. bei Facebook oder einer anderen Azure AD-Organisation) wirkt sich nicht sofort auf die Endbenutzerflows aus, sondern erst, wenn der Identitätsanbieter in einem Benutzerflow als Option hinzugefügt wird (auch bezeichnet als „integrierte Richtlinie“). Ein Beispiel finden Sie unter [Konfigurieren eines Microsoft-Kontos als Identitätsanbieter](../../active-directory-b2c/identity-provider-microsoft-account.md).  Um Benutzerflows zu ändern, ist die Rolle „B2C-Benutzerflowadministrator“ mit eingeschränkten Rechen erforderlich.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/identityProviders/allProperties/allTasks | Lesen und Konfigurieren von Identitätsanbietern in Azure Active Directory B2C |

## <a name="global-administrator"></a>Globaler Administrator

Benutzer mit dieser Rolle haben Zugriff auf alle Verwaltungsfunktionen in Azure Active Directory sowie auf Dienste, die Azure Active Directory Identitäten wie das Microsoft 365 Defender-Portal verwenden. Microsoft 365 Compliance Center, Exchange Online, SharePoint Online und Skype for Business Online. Zudem können globale Administratoren ihre [Zugriffsrechte erhöhen](../../role-based-access-control/elevate-access-global-admin.md), um alle Azure-Abonnements und Verwaltungsgruppen zu verwalten. Dadurch erhalten sie mithilfe des entsprechenden Azure AD-Mandanten Vollzugriff auf alle Azure-Ressourcen. Die Person, die die Anmeldung für die Azure AD-Organisation vornimmt, wird ein globaler Administrator. In Ihrem Unternehmen können mehrere globale Administratoren vorhanden sein. Globale Administratoren können das Kennwort für alle Benutzer und alle anderen Administratoren zurücksetzen.

> [!NOTE]
> Als bewährte Methode empfiehlt Microsoft, dass Sie die Rolle "Globaler Administrator" weniger als fünf Personen in Ihrer Organisation zuweisen. Weitere Informationen finden Sie unter [Bewährte Methoden für Azure AD-Rollen](best-practices.md).

> [!div class="mx-tableFixed"]
> | Aktionen | Beschreibung |
> | --- | --- |
> | microsoft.directory/accessReviews/allProperties/allTasks | Erstellen und Löschen von Zugriffsüberprüfungen, Lesen und Aktualisieren aller Eigenschaften von Zugriffsüberprüfungen und Verwalten von Zugriffsüberprüfungen von Gruppen in Azure AD |
> | microsoft.directory/administrativeUnits/allProperties/allTasks | Erstellen und Verwalten von Verwaltungseinheiten (einschließlich Mitgliedern). |
> | microsoft.directory/applications/allProperties/allTasks | Erstellen und Löschen von Anwendungen sowie Lesen und Aktualisieren aller Eigenschaften |
> | microsoft.directory/applications/synchronization/standard/read | Lesen von Bereitstellungseinstellungen, die dem Anwendungsobjekt zugeordnet sind |
> | microsoft.directory/applicationTemplates/instantiate | Instanziieren von Kataloganwendungen über Anwendungsvorlagen |
> | microsoft.directory/auditLogs/allProperties/read | Lesen sämtlicher Eigenschaften von Überwachungsprotokollen (einschließlich privilegierter Eigenschaften) |
> | microsoft.directory/users/authenticationMethods/create | Erstellen von Authentifizierungsmethoden für Benutzer |
> | microsoft.directory/users/authenticationMethods/delete | Löschen von Authentifizierungsmethoden für Benutzer |
> | microsoft.directory/users/authenticationMethods/standard/read | Lesen der Standardeigenschaften von Authentifizierungsmethoden für Benutzer |
> | microsoft.directory/users/authenticationMethods/basic/update | Aktualisieren der grundlegenden Eigenschaften von Authentifizierungsmethoden für Benutzer |
> | microsoft.directory/authorizationPolicy/allProperties/allTasks | Verwalten sämtlicher Aspekte von Autorisierungsrichtlinien |
> | microsoft.directory/bitlockerKeys/key/read | Lesen von BitLocker-Metadaten und -Schlüsseln auf Geräten |
> | microsoft.directory/cloudAppSecurity/allProperties/allTasks | Erstellen und Löschen aller Ressourcen sowie Lesen und Aktualisieren von Standardeigenschaften in Microsoft Cloud App Security |
> | microsoft.directory/connectors/create | Erstellen von Anwendungsproxyconnectors |
> | microsoft.directory/connectors/allProperties/read | Lesen sämtlicher Eigenschaften von Anwendungsproxyconnectors |
> | microsoft.directory/connectorGroups/create | Erstellen von Anwendungsproxy-Connectorgruppen |
> | microsoft.directory/connectorGroups/delete | Löschen von Anwendungsproxy-Connectorgruppen |
> | microsoft.directory/connectorGroups/allProperties/read | Lesen sämtlicher Eigenschaften von Anwendungsproxy-Connectorgruppen |
> | microsoft.directory/connectorGroups/allProperties/update | Aktualisieren sämtlicher Eigenschaften von Anwendungsproxy-Connectorgruppen |
> | microsoft.directory/contacts/allProperties/allTasks | Erstellen und Löschen von Kontakten sowie Lesen und Aktualisieren aller Eigenschaften |
> | microsoft.directory/contracts/allProperties/allTasks | Erstellen und Löschen von Partnerkontakten sowie Lesen und Aktualisieren aller Eigenschaften |
> | microsoft.directory/deletedItems/delete | Dauerhaftes Löschen von Objekten, die nicht mehr wiederhergestellt werden können |
> | microsoft.directory/deletedItems/restore | Wiederherstellen vorläufig gelöschter Objekte in ihrem ursprünglichen Zustand |
> | microsoft.directory/devices/allProperties/allTasks | Erstellen und Löschen von Geräten sowie Lesen und Aktualisieren aller Eigenschaften |
> | microsoft.directory/deviceManagementPolicies/standard/read | Lesen der Standardeigenschaften von Anwendungsrichtlinien zur Geräteverwaltung |
> | microsoft.directory/deviceManagementPolicies/basic/update | Aktualisieren grundlegender Eigenschaften für Anwendungsrichtlinien der Geräteverwaltung |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | Lesen der Standardeigenschaften von Richtlinien zur Geräteregistrierung |
> | microsoft.directory/deviceRegistrationPolicy/basic/update | Aktualisieren grundlegender Eigenschaften von Richtlinien zur Geräteregistrierung |
> | microsoft.directory/directoryRoles/allProperties/allTasks | Erstellen und Löschen von Verzeichnisrollen sowie Lesen und Aktualisieren aller Eigenschaften |
> | microsoft.directory/directoryRoleTemplates/allProperties/allTasks | Erstellen und Löschen von Azure AD-Rollenvorlagen sowie Lesen und Aktualisieren aller Eigenschaften |
> | microsoft.directory/domains/allProperties/allTasks | Erstellen und Löschen von Domänen sowie Lesen und Aktualisieren aller Eigenschaften |
> | microsoft.directory/entitlementManagement/allProperties/allTasks | Erstellen und Löschen von Ressourcen sowie Lesen und Aktualisieren aller Eigenschaften in der Azure AD-Berechtigungsverwaltung |
> | microsoft.directory/groups/allProperties/allTasks | Erstellen und Löschen von Gruppen sowie Lesen und Aktualisieren aller Eigenschaften |
> | microsoft.directory/groupsAssignableToRoles/create | Erstellen einer Gruppe, der Rollen zugeordnet werden können |
> | microsoft.directory/groupsAssignableToRoles/delete | Löschen von Gruppen, denen Rollen zugewiesen werden können |
> | microsoft.directory/groupsAssignableToRoles/restore | Wiederherstellen von Gruppen, denen Rollen zugewiesen werden können |
> | microsoft.directory/groupsAssignableToRoles/allProperties/update | Aktualisieren von Gruppen, denen Rollen zugeordnet werden können |
> | microsoft.directory/groupSettings/allProperties/allTasks | Erstellen und Löschen von Gruppeneinstellungen sowie Lesen und Aktualisieren aller Eigenschaften |
> | microsoft.directory/groupSettingTemplates/allProperties/allTasks | Erstellen und Löschen von Vorlagen für Gruppeneinstellungen sowie Lesen und Aktualisieren aller Eigenschaften |
> | microsoft.directory/identityProtection/allProperties/allTasks | Erstellen und Löschen sämtlicher Ressourcen sowie Lesen und Aktualisieren der Standardeigenschaften in Azure AD Identity Protection |
> | microsoft.directory/loginOrganizationBranding/allProperties/allTasks | Erstellen und Löschen von „loginTenantBranding“ sowie Lesen und Aktualisieren aller Eigenschaften |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Erstellen und Löschen von OAuth 2.0-Berechtigungszuweisungen und Lesen und Aktualisieren aller Eigenschaften |
> | microsoft.directory/organization/allProperties/allTasks | Lesen und Aktualisieren aller Eigenschaften einer Organisation |
> | microsoft.directory/policies/allProperties/allTasks | Erstellen und Löschen von Richtlinien sowie Lesen und Aktualisieren aller Eigenschaften |
> | microsoft.directory/conditionalAccessPolicies/allProperties/allTasks | Verwalten sämtlicher Richtlinieneigenschaften für den bedingten Zugriff |
> | microsoft.directory/crossTenantAccessPolicies/allProperties/allTasks | Verwalten mandantenübergreifender Zugriffsrichtlinien |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | Lesen aller Ressourcen in Privileged Identity Management |
> | microsoft.directory/provisioningLogs/allProperties/read | Lesen aller Eigenschaften von Bereitstellungsprotokollen |
> | microsoft.directory/roleAssignments/allProperties/allTasks | Erstellen und Löschen von Rollenzuweisungen und Lesen und Aktualisieren aller Rollenzuweisungseigenschaften |
> | microsoft.directory/roleDefinitions/allProperties/allTasks | Erstellen und Löschen von Rollendefinitionen und Lesen und Aktualisieren aller Eigenschaften |
> | microsoft.directory/scopedRoleMemberships/allProperties/allTasks | Erstellen und Löschen von scopedRoleMemberships und Lesen und Aktualisieren aller Eigenschaften |
> | microsoft.directory/serviceAction/activateService | Ausführen der Aktion „Dienst aktivieren“ für einen Dienst |
> | microsoft.directory/serviceAction/disableDirectoryFeature | Ausführen der Dienstaktion „Verzeichnisfunktion deaktivieren“ |
> | microsoft.directory/serviceAction/enableDirectoryFeature | Ausführen der Dienstaktion „Verzeichnisfunktion aktivieren“ |
> | microsoft.directory/serviceAction/getAvailableExtentionProperties | Ausführen der Dienstaktion „getAvailableExtentionProperties“ |
> | microsoft.directory/servicePrincipals/allProperties/allTasks | Erstellen und Löschen von Dienstprinzipalen sowie Lesen und Aktualisieren aller Eigenschaften |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-company-admin | Erteilen der Zustimmung zu einer beliebigen Berechtigung für eine beliebige Anwendung |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | Gewähren des direkten Zugriffs auf Gruppendaten für einen Dienstprinzipal |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | Lesen von Bereitstellungseinstellungen, die Ihrem Dienstprinzipal zugeordnet sind |
> | microsoft.directory/signInReports/allProperties/read | Lesen sämtlicher Eigenschaften für Anmeldeberichte (einschließlich privilegierter Eigenschaften) |
> | microsoft.directory/subscribedSkus/allProperties/allTasks | Erwerben und Verwalten von Abonnements sowie Löschen von Abonnements |
> | microsoft.directory/users/allProperties/allTasks | Erstellen und Löschen von Benutzern sowie Lesen und Aktualisieren aller Eigenschaften |
> | microsoft.directory/permissionGrantPolicies/create | Erstellen von Richtlinien für die Berechtigungszuweisung |
> | microsoft.directory/permissionGrantPolicies/delete | Löschen von Richtlinien für die Berechtigungszuweisung |
> | microsoft.directory/permissionGrantPolicies/standard/read | Lesen der Standardeigenschaften von Richtlinien für die Berechtigungszuweisung |
> | microsoft.directory/permissionGrantPolicies/basic/update | Aktualisieren grundlegender Eigenschaften von Richtlinien für die Berechtigungszuweisung |
> | microsoft.directory/servicePrincipalCreationPolicies/create | Erstellen von Erstellungsrichtlinien für Dienstprinzipale |
> | microsoft.directory/servicePrincipalCreationPolicies/delete | Löschen von Erstellungsrichtlinien für Dienstprinzipale |
> | microsoft.directory/servicePrincipalCreationPolicies/standard/read | Lesen von Standardeigenschaften von Erstellungsrichtlinien für Dienstprinzipale |
> | microsoft.directory/servicePrincipalCreationPolicies/basic/update | Aktualisieren grundlegender Eigenschaften von Erstellungsrichtlinien für Dienstprinzipale |
> | microsoft.directory/verifiableCredentials/configuration/contracts/cards/allProperties/read | Lesen einer Karte mit überprüfbaren Anmeldeinformationen |
> | microsoft.directory/verifiableCredentials/configuration/contracts/cards/revoke | Widerrufen einer Karte mit überprüfbaren Anmeldeinformationen |
> | microsoft.directory/verifiableCredentials/configuration/contracts/create | Erstellen eines Vertrags mit überprüfbaren Anmeldeinformationen |
> | microsoft.directory/verifiableCredentials/configuration/contracts/allProperties/read | Lesen eines Vertrags mit überprüfbaren Anmeldeinformationen |
> | microsoft.directory/verifiableCredentials/configuration/contracts/allProperties/update | Aktualisieren eines Vertrags mit überprüfbaren Anmeldeinformationen |
> | microsoft.directory/verifiableCredentials/configuration/create | Erstellen der erforderlichen Konfiguration zum Erstellen und Verwalten von überprüfbaren Anmeldeinformationen |
> | microsoft.directory/verifiableCredentials/configuration/delete | Löschen der erforderlichen Konfiguration zum Erstellen und Verwalten von überprüfbaren Anmeldeinformationen sowie zum Löschen aller ihrer überprüfbaren Anmeldeinformationen |
> | microsoft.directory/verifiableCredentials/configuration/allProperties/read | Lesen der erforderlichen Konfiguration zum Erstellen und Verwalten von überprüfbaren Anmeldeinformationen |
> | microsoft.directory/verifiableCredentials/configuration/allProperties/update | Aktualisieren der erforderlichen Konfiguration zum Erstellen und Verwalten von überprüfbaren Anmeldeinformationen |
> | microsoft.azure.advancedThreatProtection/allEntities/allTasks | Verwalten sämtlicher Aspekte von Azure Advanced Threat Protection |
> | microsoft.azure.informationProtection/allEntities/allTasks | Verwalten sämtlicher Aspekte von Azure Information Protection |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
> | microsoft.cloudPC/allEntities/allProperties/allTasks | Verwalten sämtlicher Aspekte von Windows 365 |
> | microsoft.commerce.billing/allEntities/allTasks | Verwalten sämtlicher Aspekte der Office 365-Abrechnung |
> | microsoft.dynamics365/allEntities/allTasks | Verwalten sämtlicher Aspekte von Dynamics 365 |
> | microsoft.edge/allEntities/allProperties/allTasks | Verwalten sämtlicher Aspekte von Microsoft Edge |
> | microsoft.flow/allEntities/allTasks | Verwalten sämtlicher Aspekte von Microsoft Power Automate |
> | microsoft.intune/allEntities/allTasks | Verwalten sämtlicher Aspekte von Microsoft Intune |
> | microsoft.office365.complianceManager/allEntities/allTasks | Verwalten sämtlicher Aspekte von Office 365 Compliance-Manager |
> | microsoft.office365.desktopAnalytics/allEntities/allTasks | Verwalten sämtlicher Aspekte von Desktop Analytics |
> | microsoft.office365.exchange/allEntities/basic/allTasks | Verwalten sämtlicher Aspekte von Exchange Online |
> | microsoft.office365.knowledge/contentUnderstanding/allProperties/allTasks | Lesen und Aktualisieren aller Eigenschaften von Content Understanding in Microsoft 365 Admin Center |
> | microsoft.office365.knowledge/contentUnderstanding/analytics/allProperties/read | Lesen von Analyseberichten zum Inhaltsverständnis im Microsoft 365 Admin Center |
> | microsoft.office365.knowledge/knowledgeNetwork/allProperties/allTasks | Lesen und Aktualisieren aller Eigenschaften des Wissensnetzwerks in Microsoft 365 Admin Center |
> | microsoft.office365.knowledge/knowledgeNetwork/topicVisibility/allProperties/allTasks | Verwalten der Themensichtbarkeit des Wissensnetzwerks im Microsoft 365 Admin Center |
> | microsoft.office365.knowledge/learningSources/allProperties/allTasks | Verwalten von Lernquellen und allen zugehörigen Eigenschaften in der Learning-App. |
> | microsoft.office365.lockbox/allEntities/allTasks | Verwalten sämtlicher Aspekte der Kunden-Lockbox |
> | microsoft.office365.messageCenter/messages/read | Lesen von Nachrichten im Nachrichtencenter im Microsoft 365 Admin Center (mit Ausnahme von Sicherheitsmeldungen) |
> | microsoft.office365.messageCenter/securityMessages/read | Lesen von Sicherheitsmeldungen im Nachrichtencenter im Microsoft 365 Admin Center |
> | microsoft.office365.network/performance/allProperties/read | Lesen aller Netzwerkleistungseigenschaften im Microsoft 365 Admin Center |
> | microsoft.office365.protectionCenter/allEntities/allProperties/allTasks | Verwalten aller Aspekte der Security & Compliance Center |
> | microsoft.office365.search/content/manage | Erstellen und Löschen von Inhalten sowie Lesen und Aktualisieren aller Eigenschaften in Microsoft Search |
> | microsoft.office365.securityComplianceCenter/allEntities/allTasks | Erstellen und Löschen aller Ressourcen und Lesen und Aktualisieren von Standardeigenschaften im Office 365 Security & Compliance Center. |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Service Health im Microsoft 365 Admin Center |
> | microsoft.office365.sharePoint/allEntities/allTasks | Erstellen und Löschen aller Ressourcen sowie Lesen und Aktualisieren der Standardeigenschaften in SharePoint |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Verwalten sämtlicher Aspekte von Skype for Business Online |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Lesen von Office 365-Nutzungsberichten |
> | microsoft.office365.userCommunication/allEntities/allTasks | Lesen und Aktualisieren der Sichtbarkeit von Meldungen zu neuen Features |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |
> | microsoft.powerApps/allEntities/allTasks | Verwalten sämtlicher Aspekte von Power Apps |
> | microsoft.powerApps.powerBI/allEntities/allTasks | Verwalten sämtlicher Aspekte von Power BI |
> | microsoft.azure.print/allEntities/allProperties/allTasks | Verwalten aller Ressourcen in Teams |
> | microsoft.windows.defenderAdvancedThreatProtection/allEntities/allTasks | Verwalten sämtlicher Aspekte von Microsoft Defender für Endpunkt |
> | microsoft.windows.updatesDeployments/allEntities/allProperties/allTasks | Lesen und Konfigurieren aller Aspekte des Windows Update-Diensts |

## <a name="global-reader"></a>Globaler Leser

Benutzer in dieser Rolle können in Microsoft 365-Diensten Einstellungen und administrative Informationen lesen, aber keine Verwaltungsaktionen ausführen. Die Rolle „Globaler Leser“ ist das Gegenstück zu „Globaler Administrator“, hat jedoch nur Leseberechtigungen. Weisen Sie die Rolle „Globaler Leser“ anstelle der Rolle „Globaler Administrator“ für Planungen, Audits oder Untersuchungen zu. Kombinieren Sie die Rolle „Globaler Leser“ mit anderen eingeschränkten Administratorrollen (z. B. Exchange-Administrator), um die Arbeit zu vereinfachen, ohne die Rolle „Globaler Administrator“ verwenden zu müssen. Die Rolle „Globaler Leser“ funktioniert mit Microsoft 365 Admin Center, Exchange Admin Center, SharePoint Admin Center, Teams Admin Center, Security Center, Compliance Center, Azure AD Admin Center und Device Management Admin Center.

> [!NOTE]
> Die Rolle „Globaler Leser“ weist zurzeit einige Einschränkungen auf:
>
>- [OneDrive Admin Center:](https://admin.onedrive.com/) OneDrive Admin Center unterstützt die Rolle „Globaler Leser“ nicht.
>- [Microsoft 365 Admin Center:](https://admin.microsoft.com/Adminportal/Home#/homepage) Die Rolle „Globaler Leser“ kann integrierte Apps nicht lesen. Die Registerkarte **Integrierte Apps** wird im linken Bereich des Microsoft 365 Admin Centers unter **Einstellungen** nicht angezeigt.
>- [Office Security & Compliance Center:](https://sip.protection.office.com/homepage) Die Rolle „Globaler Leser“ kann weder SCC-Überwachungsprotokolle lesen, noch eine Inhaltssuche durchführen oder die Sicherheitsbewertung anzeigen.
>- [Teams Admin Center:](https://admin.teams.microsoft.com) Die Rolle „Globaler Leser“ kann den **Teams-Lebenszyklus**, **Analysen und Berichte**, die **IP-Telefon-Geräteverwaltung** und den **App-Katalog** nicht lesen.
>- [Privileged Access Management (PAM)](/office365/securitycompliance/privileged-access-management-overview) unterstützt die Rolle „Globaler Leser“ nicht.
>- [Azure Information Protection:](/azure/information-protection/what-is-information-protection) Die Rolle „Globaler Leser“ wird nur für die [zentrale Berichterstellung](/azure/information-protection/reports-aip) unterstützt, wenn sich Ihre Azure AD-Organisation nicht auf der [Plattform für einheitliche Bezeichnungen](/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform) befindet.
> - [SharePoint:](https://admin.microsoft.com/sharepoint) „Globaler Leser“ kann derzeit nicht per PowerShell auf SharePoint zugreifen.
>
> Diese Features befinden sich zurzeit in der Entwicklung.
>

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/accessReviews/allProperties/read | Lesen aller Eigenschaften von Zugriffsüberprüfungen |
> | microsoft.directory/administrativeUnits/allProperties/read | Lesen aller Eigenschaften von Verwaltungseinheiten |
> | microsoft.directory/applications/allProperties/read | Lesen aller Eigenschaften (einschließlich privilegierter Eigenschaften) aller Anwendungstypen |
> | microsoft.directory/applications/synchronization/standard/read | Lesen von Bereitstellungseinstellungen, die dem Anwendungsobjekt zugeordnet sind |
> | microsoft.directory/auditLogs/allProperties/read | Lesen sämtlicher Eigenschaften von Überwachungsprotokollen (einschließlich privilegierter Eigenschaften) |
> | microsoft.directory/users/authenticationMethods/standard/restrictedRead | Lesen der Standardeigenschaften von Authentifizierungsmethoden, die keine personenbezogenen Informationen für Benutzer enthalten |
> | microsoft.directory/authorizationPolicy/standard/read | Lesen von Standardeigenschaften von Autorisierungsrichtlinien |
> | microsoft.directory/bitlockerKeys/key/read | Lesen von BitLocker-Metadaten und -Schlüsseln auf Geräten |
> | microsoft.directory/cloudAppSecurity/allProperties/read | Lesen sämtlicher Eigenschaften für Cloud App Security |
> | microsoft.directory/connectors/allProperties/read | Lesen sämtlicher Eigenschaften von Anwendungsproxyconnectors |
> | microsoft.directory/connectorGroups/allProperties/read | Lesen sämtlicher Eigenschaften von Anwendungsproxy-Connectorgruppen |
> | microsoft.directory/contacts/allProperties/read | Lesen sämtlicher Eigenschaften für Kontakte |
> | microsoft.directory/devices/allProperties/read | Lesen sämtlicher Eigenschaften auf Geräten |
> | microsoft.directory/directoryRoles/allProperties/read | Lesen sämtlicher Eigenschaften für Azure AD-Rollen |
> | microsoft.directory/directoryRoleTemplates/allProperties/read | Lesen sämtlicher Eigenschaften für Rollenvorlagen |
> | microsoft.directory/domains/allProperties/read | Lesen sämtlicher Eigenschaften von Domänen |
> | microsoft.directory/entitlementManagement/allProperties/read | Lesen sämtlicher Eigenschaften in der Azure AD-Berechtigungsverwaltung |
> | microsoft.directory/groups/allProperties/read | Lesen aller Eigenschaften (einschließlich privilegierter Eigenschaften) von Sicherheits- und Microsoft 365-Gruppen (einschließlich Gruppen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groupSettings/allProperties/read | Lesen sämtlicher Eigenschaften von Gruppeneinstellungen |
> | microsoft.directory/groupSettingTemplates/allProperties/read | Lesen sämtlicher Eigenschaften von Vorlagen für Gruppeneinstellungen |
> | microsoft.directory/identityProtection/allProperties/read | Lesen aller Ressourcen in Azure AD Identity Protection |
> | microsoft.directory/loginOrganizationBranding/allProperties/read | Lesen sämtlicher Eigenschaften für die Anmeldeseite Ihrer Organisation mit Branding |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/read | Lesen sämtlicher Eigenschaften von OAuth 2.0-Berechtigungszuweisungen |
> | microsoft.directory/organization/allProperties/read | Lesen sämtlicher Eigenschaften für eine Organisation |
> | microsoft.directory/permissionGrantPolicies/standard/read | Lesen der Standardeigenschaften von Richtlinien für die Berechtigungszuweisung |
> | microsoft.directory/policies/allProperties/read | Alle Eigenschaften von Richtlinien lesen |
> | microsoft.directory/conditionalAccessPolicies/allProperties/read | Lesen sämtlicher Richtlinieneigenschaften für den bedingten Zugriff |
> | microsoft.directory/crossTenantAccessPolicies/allProperties/read | Lesen sämtlicher Eigenschaften von mandantenübergreifenden Richtlinien |
> | microsoft.directory/deviceManagementPolicies/standard/read | Lesen der Standardeigenschaften von Anwendungsrichtlinien zur Geräteverwaltung |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | Lesen der Standardeigenschaften von Richtlinien zur Geräteregistrierung |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | Lesen aller Ressourcen in Privileged Identity Management |
> | microsoft.directory/provisioningLogs/allProperties/read | Lesen aller Eigenschaften von Bereitstellungsprotokollen |
> | microsoft.directory/roleAssignments/allProperties/read | Lesen aller Eigenschaften von Rollenzuweisungen |
> | microsoft.directory/roleDefinitions/allProperties/read | Lesen aller Eigenschaften von Rollendefinitionen |
> | microsoft.directory/scopedRoleMemberships/allProperties/read | Anzeigen der Mitglieder von Verwaltungseinheiten |
> | microsoft.directory/serviceAction/getAvailableExtentionProperties | Ausführen der Dienstaktion „getAvailableExtentionProperties“ |
> | microsoft.directory/servicePrincipals/allProperties/read | Lesen sämtlicher Eigenschaften (einschließlich privilegierter Eigenschaften) von servicePrincipals |
> | microsoft.directory/servicePrincipalCreationPolicies/standard/read | Lesen von Standardeigenschaften von Erstellungsrichtlinien für Dienstprinzipale |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | Lesen von Bereitstellungseinstellungen, die Ihrem Dienstprinzipal zugeordnet sind |
> | microsoft.directory/signInReports/allProperties/read | Lesen sämtlicher Eigenschaften für Anmeldeberichte (einschließlich privilegierter Eigenschaften) |
> | microsoft.directory/subscribedSkus/allProperties/read | Lesen sämtlicher Eigenschaften von Produktabonnements |
> | microsoft.directory/users/allProperties/read | Lesen sämtlicher Eigenschaften von Benutzern |
> | microsoft.directory/verifiableCredentials/configuration/contracts/cards/allProperties/read | Lesen einer Karte mit überprüfbaren Anmeldeinformationen |
> | microsoft.directory/verifiableCredentials/configuration/contracts/allProperties/read | Lesen eines Vertrags mit überprüfbaren Anmeldeinformationen |
> | microsoft.directory/verifiableCredentials/configuration/allProperties/read | Lesen der erforderlichen Konfiguration zum Erstellen und Verwalten von überprüfbaren Anmeldeinformationen |
> | microsoft.cloudPC/allEntities/allProperties/read | Lesen sämtlicher Aspekte von Windows 365 |
> | microsoft.commerce.billing/allEntities/read | Lesen sämtlicher Ressourcen der Office 365-Abrechnung |
> | microsoft.edge/allEntities/allProperties/read | Lesen sämtlicher Aspekte von Microsoft Edge |
> | microsoft.office365.exchange/allEntities/standard/read | Lesen sämtlicher Ressourcen von Exchange Online |
> | microsoft.office365.messageCenter/messages/read | Lesen von Nachrichten im Nachrichtencenter im Microsoft 365 Admin Center (mit Ausnahme von Sicherheitsmeldungen) |
> | microsoft.office365.messageCenter/securityMessages/read | Lesen von Sicherheitsmeldungen im Nachrichtencenter im Microsoft 365 Admin Center |
> | microsoft.office365.network/performance/allProperties/read | Lesen aller Netzwerkleistungseigenschaften im Microsoft 365 Admin Center |
> | microsoft.office365.protectionCenter/allEntities/allProperties/read | Lesen aller Eigenschaften im Security & Compliance Center |
> | microsoft.office365.securityComplianceCenter/allEntities/read | Lesen von Standardeigenschaften im Microsoft 365 Security and Compliance Center |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Lesen von Office 365-Nutzungsberichten |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |
> | microsoft.teams/allEntities/allProperties/read | Lesen aller Aspekte Microsoft Teams |
> | microsoft.windows.updatesDeployments/allEntities/allProperties/read | Lesen aller Aspekte des Windows Update-Diensts |

## <a name="groups-administrator"></a>Gruppenadministrator

Benutzer mit dieser Rolle können Gruppen und die zugehörigen Einstellungen wie Benennungs- und Ablaufrichtlinien erstellen und verwalten. Es ist wichtig zu verstehen, dass der Benutzer durch die Zuweisung dieser Rolle alle Gruppen in der Organisation nicht nur in Outlook, sondern in verschiedenen Workloads wie Teams, SharePoint und Yammer verwalten kann. Außerdem kann der Benutzer die verschiedenen Gruppeneinstellungen in verschiedenen Verwaltungsportalen wie Microsoft Admin Center und dem Azure-Portal sowie den workloadspezifischen Portalen wie Teams Admin Center und SharePoint Admin Center verwalten.

> [!div class="mx-tableFixed"]
> | Aktionen | Beschreibung |
> | --- | --- |
> | microsoft.directory/deletedItems.groups/delete | Dauerhaftes Löschen von Gruppen, die nicht mehr wiederhergestellt werden können. |
> | microsoft.directory/deletedItems.groups/restore | Wiederherstellen des ursprünglichen Zustands von soft deleted-Gruppen |
> | microsoft.directory/groups/assignLicense | Zuweisen von Produktlizenzen zu Gruppen für die gruppenbasierte Lizenzierung |
> | microsoft.directory/groups/create | Erstellen von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/delete | Löschen von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/hiddenMembers/read | Lesen der ausgeblendeten Mitglieder von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen eingeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/reprocessLicenseAssignment | Erneutes Verarbeiten von Lizenzzuweisungen für die gruppenbasierte Lizenzierung |
> | microsoft.directory/groups/restore | Wiederherstellen gelöschter Gruppen |
> | microsoft.directory/groups/basic/update | Aktualisieren grundlegender Eigenschaften von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/classification/update | Aktualisieren der Klassifizierungseigenschaft von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/dynamicMembershipRule/update | Aktualisieren der Regel für eine dynamische Mitgliedschaft für Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/groupType/update | Aktualisieren von Eigenschaften, die sich auf den Gruppentyp von Sicherheitsgruppen und Microsoft 365-Gruppen auswirken (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/members/update | Aktualisieren der Mitglieder von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/onPremWriteBack/update | Aktualisieren von Azure Active Directory-Gruppen, die mit Azure AD Connect in die lokale Umgebung zurückgeschrieben werden sollen |
> | microsoft.directory/groups/owners/update | Aktualisieren der Besitzer von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/settings/update | Aktualisieren von Gruppeneinstellungen |
> | microsoft.directory/groups/visibility/update | Aktualisieren der visibility-Eigenschaft von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | Gewähren des direkten Zugriffs auf Gruppendaten für einen Dienstprinzipal |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Service Health im Microsoft 365 Admin Center |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="guest-inviter"></a>Gasteinladender

Wenn die Benutzereinstellung **Mitglieder können einladen** auf „Nein“ festgelegt ist, können Benutzer in dieser Rolle Azure Active Directory B2B-Gastbenutzereinladungen verwalten. Weitere Informationen zur B2B-Zusammenarbeit finden Sie unter [Was ist die Azure AD B2B-Zusammenarbeit?](../external-identities/what-is-b2b.md). Es sind keine anderen Berechtigungen eingeschlossen.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/users/inviteGuest | Einladen von Gastbenutzern |
> | microsoft.directory/users/standard/read | Lesen grundlegender Eigenschaften für Benutzer |
> | microsoft.directory/users/appRoleAssignments/read | Lesen von Anwendungsrollenzuweisungen für Benutzer |
> | microsoft.directory/users/deviceForResourceAccount/read | Lesen von deviceForResourceAccount von Benutzern |
> | microsoft.directory/users/directReports/read | Lesen von direkten Berichten für Benutzer |
> | microsoft.directory/users/licenseDetails/read | Lesen von Lizenzdetails von Benutzern |
> | microsoft.directory/users/manager/read | Lesen der Manager von Benutzern |
> | microsoft.directory/users/memberOf/read | Lesen von Gruppenmitgliedschaften von Benutzern |
> | microsoft.directory/users/oAuth2PermissionGrants/read | Lesen delegierter Berechtigungszuweisungen für Benutzer |
> | microsoft.directory/users/ownedDevices/read | Lesen von Geräten im Besitz von Benutzern |
> | microsoft.directory/users/ownedObjects/read | Lesen von Objekten im Besitz von Benutzern |
> | microsoft.directory/users/photo/read | Lesen des Fotos von Benutzern |
> | microsoft.directory/users/registeredDevices/read | Lesen von registrierten Geräten von Benutzern |
> | microsoft.directory/users/scopedRoleMemberOf/read | Lesen der Benutzermitgliedschaft einer Azure AD Rolle, die für eine Verwaltungseinheit gilt |

## <a name="helpdesk-administrator"></a>Helpdeskadministrator

Benutzer mit dieser Rolle können Kennwörter ändern, Aktualisierungstoken für ungültig erklären, Dienstanforderungen verwalten und die Dienstintegrität überwachen. Wenn ein Aktualisierungstoken für ungültig erklärt wird, muss sich der Benutzer erneut anmelden. Ob ein Helpdeskadministrator das Kennwort eines Benutzers zurücksetzen und Aktualisierungstoken ungültig machen kann, hängt von der Rolle ab, die dem Benutzer zugewiesen ist. Eine Liste der Rollen, für die ein Helpdeskadministrator Kennwörter zurücksetzen und Aktualisierungstoken ungültig machen kann, finden Sie unter [Berechtigungen für die Kennwortzurücksetzung](#password-reset-permissions).

> [!IMPORTANT]
> Benutzer mit dieser Rolle können Kennwörter für Benutzer ändern, die Zugriff auf vertrauliche oder private Informationen bzw. kritische Konfigurationen innerhalb und außerhalb von Azure Active Directory haben. Benutzer, die Kennwörter ändern können, können ggf. auch die Identität und die Berechtigungen des betreffenden Benutzers annehmen. Beispiel:
>
>- Besitzer von Anwendungsregistrierungen und Unternehmensanwendungen, die Anmeldeinformationen von Apps verwalten können, die sie besitzen. Diese Apps können über höhere Berechtigungen in Azure AD und in anderen Diensten verfügen, die Helpdeskadministratoren nicht gewährt werden. So kann ein Helpdeskadministrator die Identität eines Anwendungsbesitzers annehmen und dann die Identität einer privilegierten Anwendung durch Aktualisieren der Anmeldeinformationen für die Anwendung annehmen.
>- Besitzer von Azure-Abonnements, die möglicherweise auf vertrauliche oder private Informationen oder kritische Konfigurationen in Azure zugreifen können.
>- Besitzer von Sicherheitsgruppen und Microsoft 365-Gruppen, die die Gruppenmitgliedschaft verwalten können. Diese Gruppen können Zugriff auf vertrauliche oder private Informationen bzw. kritische Konfigurationen in Azure AD und in anderen Diensten gewähren.
>- Administratoren in anderen Diensten außerhalb von Azure AD wie Exchange Online, Office Security and Compliance Center und Personalwesen.
>- Nichtadministratoren wie Führungskräfte, Rechtsberater und Mitarbeiter der Personalabteilung mit Zugriff auf vertrauliche oder private Informationen.

Das Delegieren von administrativen Berechtigungen für Teilmengen von Benutzern und das Anwenden von Richtlinien auf eine Teilmenge der Benutzer kann über [Verwaltungseinheiten](administrative-units.md) erfolgen.

Im [Azure-Portal](https://portal.azure.com/) hieß diese Rolle früher „Kennwortadministrator“. Der Name „Helpdeskadministrator“ in Azure AD entspricht jetzt dem Namen in Azure AD PowerShell und Microsoft Graph-API.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/bitlockerKeys/key/read | Lesen von BitLocker-Metadaten und -Schlüsseln auf Geräten |
> | microsoft.directory/users/invalidateAllRefreshTokens | Erzwingen der Abmeldung von Benutzern durch Ungültigmachen des Aktualisierungstokens |
> | microsoft.directory/users/password/update | Zurücksetzen der Kennwörter für alle Benutzer |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Service Health im Microsoft 365 Admin Center |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="hybrid-identity-administrator"></a>Hybrididentitätsadministrator

Benutzer mit dieser Rolle können die Konfigurationseinrichtung bei Bereitstellungen zwischen AD und Azure AD mithilfe der Cloudbereitstellung erstellen, verwalten und bereitstellen sowie Azure AD Connect und Verbundeinstellungen verwalten. Mit dieser Rolle können Benutzer auch Probleme beheben und Protokolle überwachen.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/applications/create | Erstellen aller Anwendungstypen |
> | microsoft.directory/applications/delete | Löschen aller Anwendungstypen |
> | microsoft.directory/applications/appRoles/update | Aktualisieren der appRoles-Eigenschaft für alle Anwendungstypen |
> | microsoft.directory/applications/audience/update | Aktualisieren der audience-Eigenschaft für Anwendungen |
> | microsoft.directory/applications/authentication/update | Aktualisieren der Authentifizierung für alle Anwendungstypen |
> | microsoft.directory/applications/basic/update | Aktualisieren grundlegender Eigenschaften für Anwendungen |
> | microsoft.directory/applications/notes/update | Aktualisieren von Anwendungshinweisen |
> | microsoft.directory/applications/owners/update | Aktualisieren von Anwendungsbesitzern |
> | microsoft.directory/applications/permissions/update | Aktualisieren von verfügbar gemachten und erforderlichen Berechtigungen für alle Anwendungstypen |
> | microsoft.directory/applications/policies/update | Aktualisieren von Anwendungsrichtlinien |
> | microsoft.directory/applications/tag/update | Aktualisieren von Anwendungstags |
> | microsoft.directory/applications/synchronization/standard/read | Lesen von Bereitstellungseinstellungen, die dem Anwendungsobjekt zugeordnet sind |
> | microsoft.directory/applicationTemplates/instantiate | Instanziieren von Kataloganwendungen über Anwendungsvorlagen |
> | microsoft.directory/auditLogs/allProperties/read | Lesen sämtlicher Eigenschaften von Überwachungsprotokollen (einschließlich privilegierter Eigenschaften) |
> | microsoft.directory/cloudProvisioning/allProperties/allTasks | Lesen und Konfigurieren aller Eigenschaften des Azure AD-Cloudbereitstellungsdiensts |
> | microsoft.directory/deletedItems.applications/delete | Dauerhaftes Löschen von Anwendungen, die nicht mehr wiederhergestellt werden können |
> | microsoft.directory/deletedItems.applications/restore | Wiederherstellen vorläufig gelöschter Anwendungen in ihrem ursprünglichen Zustand |
> | microsoft.directory/domains/allProperties/read | Lesen sämtlicher Eigenschaften von Domänen |
> | microsoft.directory/domains/federation/update | Aktualisieren der Verbundeigenschaft von Domänen |
> | microsoft.directory/organization/dirSync/update | Aktualisieren der Synchronisierungseigenschaft für das Organisationsverzeichnis |
> | microsoft.directory/provisioningLogs/allProperties/read | Lesen aller Eigenschaften von Bereitstellungsprotokollen |
> | microsoft.directory/servicePrincipals/create | Erstellen von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/delete | Löschen von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/disable | Deaktivieren von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/enable | Aktivieren von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Verwalten der Geheimnisse und Anmeldeinformationen für die Anwendungsbereitstellung |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | Starten, Neustarten und Anhalten von Synchronisierungsaufträgen für die Anwendungsbereitstellung |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | Erstellen und Verwalten von Synchronisierungsaufträgen und -schemas für die Anwendungsbereitstellung |
> | microsoft.directory/servicePrincipals/audience/update | Aktualisieren der Zielgruppeneigenschaften für Dienstprinzipale |
> | microsoft.directory/servicePrincipals/authentication/update | Aktualisieren der Authentifizierungseigenschaften für Dienstprinzipale |
> | microsoft.directory/servicePrincipals/basic/update | Aktualisieren grundlegender Eigenschaften für Dienstprinzipale |
> | microsoft.directory/servicePrincipals/notes/update | Aktualisieren von Hinweisen zu Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/owners/update | Aktualisieren der Besitzer von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/permissions/update | Aktualisieren der Berechtigungen von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/policies/update | Aktualisieren der Richtlinien für Dienstprinzipale |
> | microsoft.directory/servicePrincipals/tag/update | Aktualisieren der tag-Eigenschaft für Dienstprinzipale |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | Lesen von Bereitstellungseinstellungen, die Ihrem Dienstprinzipal zugeordnet sind |
> | microsoft.directory/signInReports/allProperties/read | Lesen sämtlicher Eigenschaften für Anmeldeberichte (einschließlich privilegierter Eigenschaften) |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
> | microsoft.office365.messageCenter/messages/read | Lesen von Nachrichten im Nachrichtencenter im Microsoft 365 Admin Center (mit Ausnahme von Sicherheitsmeldungen) |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Service Health im Microsoft 365 Admin Center |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="identity-governance-administrator"></a>Identity Governance-Administrator

Benutzer mit dieser Rolle können die Azure AD Identity Governance-Konfiguration verwalten. Dies schließt die Verwaltung von Zugriffspaketen, Zugriffsüberprüfungen, Katalogen und Richtlinien ein. So kann sichergestellt werden, dass der Zugriff genehmigt und überprüft wurde und Gastbenutzer entfernt werden, wenn sie keinen Zugriff mehr benötigen.

> [!div class="mx-tableFixed"]
> | Aktionen | Beschreibung |
> | --- | --- |
> | microsoft.directory/accessReviews/allProperties/allTasks | Erstellen und Löschen von Zugriffsüberprüfungen, Lesen und Aktualisieren aller Eigenschaften von Zugriffsüberprüfungen und Verwalten von Zugriffsüberprüfungen von Gruppen in Azure AD |
> | microsoft.directory/entitlementManagement/allProperties/allTasks | Erstellen und Löschen von Ressourcen sowie Lesen und Aktualisieren aller Eigenschaften in der Azure AD-Berechtigungsverwaltung |
> | microsoft.directory/groups/members/update | Aktualisieren der Mitglieder von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aktualisieren von Rollenzuweisungen für Dienstprinzipale |

## <a name="insights-administrator"></a>Insights Administrator

Benutzer mit dieser Rolle können auf alle administrativen Funktionen in der [Microsoft 365 Insights-Anwendung](https://go.microsoft.com/fwlink/?linkid=2129521) zugreifen. Diese Rolle kann Verzeichnisinformationen lesen, die Dienstintegrität überwachen, Supporttickets einordnen und auf die Einstellungsaspekte für Insights-Administratoren zugreifen.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
> | microsoft.insights/allEntities/allTasks | Verwalten sämtlicher Aspekte der Insights-App |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Service Health im Microsoft 365 Admin Center |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="insights-business-leader"></a>Insights Business Leader

Benutzer mit dieser Rolle können über die [Microsoft 365 Insights-Anwendung](https://go.microsoft.com/fwlink/?linkid=2129521) auf eine Gruppe von Dashboards und Erkenntnisse zugreifen. Dies umfasst Vollzugriff auf alle Dashboards und die dargestellten Funktionen für Erkenntnisse sowie das Durchsuchen von Daten. Benutzer mit dieser Rolle haben keinen Zugriff auf die Einstellungen für die Produktkonfiguration, für die die Rolle „Insights-Administrator“ zuständig ist.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.insights/reports/read | Anzeigen von Berichten und Dashboards in der Insights-App |
> | microsoft.insights/programs/update | Bereitstellen und Verwalten von Programmen in der Insights-App |

## <a name="intune-administrator"></a>Intune-Administrator

Benutzer mit dieser Rolle besitzen globale Berechtigungen in Microsoft Intune Online, wenn der Dienst verfügbar ist. Darüber hinaus beinhaltet diese Rolle die Möglichkeit, Benutzer und Geräte zum Zuordnen von Richtlinien zu verwalten sowie Gruppen zu erstellen und zu verwalten. Weitere Informationen finden Sie unter [Rollenbasierte Zugriffssteuerung mit Microsoft Intune](/intune/role-based-access-control).

Mit dieser Rolle können alle Sicherheitsgruppen erstellt und verwaltet werden. Der Intune-Administrator besitzt jedoch keine Administratorrechte für Office-Gruppen. Der Administrator kann also keine Besitzer oder Mitgliedschaften aller Office-Gruppen in der Organisation aktualisieren. Im Rahmen seiner Endbenutzerberechtigungen kann er allerdings die von ihm erstellte Office-Gruppe verwalten. Daher zählt jede von ihm erstellte Office-Gruppe (nicht Sicherheitsgruppe) zu seinem Kontingent von 250 Stück.

> [!NOTE]
> In Azure AD PowerShell und der Microsoft Graph-API wird diese Rolle als „Intune-Dienstadministrator“ bezeichnet. Im [Azure-Portal](https://portal.azure.com) lautet sie „Intune-Administrator“.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/bitlockerKeys/key/read | Lesen von BitLocker-Metadaten und -Schlüsseln auf Geräten |
> | microsoft.directory/contacts/create | Erstellen von Kontakten |
> | microsoft.directory/contacts/delete | Löschen von Kontakten |
> | microsoft.directory/contacts/basic/update | Aktualisieren grundlegender Eigenschaften für Kontakte |
> | microsoft.directory/devices/create | Erstellen von Geräten (bei Azure AD registrieren) |
> | microsoft.directory/devices/delete | Löschen von Geräten aus Azure AD |
> | microsoft.directory/devices/disable | Deaktivieren von Geräten in Azure AD |
> | microsoft.directory/devices/enable | Aktivieren von Geräten in Azure AD |
> | microsoft.directory/devices/basic/update | Aktualisieren grundlegender Eigenschaften für Geräte |
> | microsoft.directory/devices/extensionAttributeSet1/update | Aktualisieren der Eigenschaften „extensionAttribute1“ bis „extensionAttribute5“ auf Geräten |
> | microsoft.directory/devices/extensionAttributeSet2/update | Aktualisieren der Eigenschaften „extensionAttribute6“ bis „extensionAttribute10“ auf Geräten |
> | microsoft.directory/devices/extensionAttributeSet3/update | Aktualisieren der Eigenschaften „extensionAttribute11“ bis „extensionAttribute15“ auf Geräten |
> | microsoft.directory/devices/registeredOwners/update | Lesen der registrierten Besitzer von Geräten |
> | microsoft.directory/devices/registeredUsers/update | Aktualisieren der registrierten Besitzer von Geräten |
> | microsoft.directory/deviceManagementPolicies/standard/read | Lesen der Standardeigenschaften von Anwendungsrichtlinien zur Geräteverwaltung |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | Lesen der Standardeigenschaften von Richtlinien zur Geräteregistrierung |
> | microsoft.directory/groups/hiddenMembers/read | Lesen der ausgeblendeten Mitglieder von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen eingeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security/create | Erstellen von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security/delete | Löschen von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security/basic/update | Aktualisieren grundlegender Eigenschaften von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security/classification/update | Aktualisieren der classification-Eigenschaft von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security/dynamicMembershipRule/update | Aktualisieren der Regel für die dynamische Mitgliedschaft von Sicherheitsgruppen (mit Ausnahme der Gruppen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security/members/update | Aktualisieren der Mitglieder von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security/owners/update | Aktualisieren der Besitzer von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security/visibility/update | Aktualisieren der visibility-Eigenschaft von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/users/basic/update | Aktualisieren grundlegender Eigenschaften für Benutzer |
> | microsoft.directory/users/manager/update | Aktualisieren der Manager für Benutzer |
> | microsoft.directory/users/photo/update | Aktualisieren des Fotos von Benutzern |
> | microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
> | microsoft.cloudPC/allEntities/allProperties/allTasks | Verwalten sämtlicher Aspekte von Windows 365 |
> | microsoft.intune/allEntities/allTasks | Verwalten sämtlicher Aspekte von Microsoft Intune |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="kaizala-administrator"></a>Kaizala-Administrator

Benutzer mit dieser Rolle besitzen globale Berechtigungen zum Verwalten von Einstellungen in Microsoft Kaizala, wenn der Dienst verfügbar ist, und können Supporttickets verwalten und die Dienstintegrität überwachen. Darüber hinaus können diese Benutzer auf Berichte zur Einführung und Nutzung von Kaizala durch Mitglieder der Organisation sowie auf Geschäftsberichte zugreifen, die mithilfe von Kaizala-Aktionen generiert wurden.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/authorizationPolicy/standard/read | Lesen von Standardeigenschaften von Autorisierungsrichtlinien |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Service Health im Microsoft 365 Admin Center |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="knowledge-administrator"></a>Wissensadministrator

Benutzer mit dieser Rolle haben Vollzugriff auf alle Einstellungen für Wissens-, Lern- und intelligenten Features im Microsoft 365 Admin Center. Sie verfügen über ein allgemeines Verständnis der Produktsuite und der Lizenzierungsdetails und sind für die Zugriffssteuerung verantwortlich. Der Wissensadministrator kann Inhalte wie Themen, Akronyme und Lernressourcen erstellen und verwalten. Darüber hinaus können diese Benutzer Inhaltscenter erstellen, die Dienstintegrität überwachen und Service Requests erstellen.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/groups.security/create | Erstellen von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security/createAsOwner | Erstellen von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) Der Ersteller wird als erster Besitzer hinzugefügt. |
> | microsoft.directory/groups.security/delete | Löschen von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security/basic/update | Aktualisieren grundlegender Eigenschaften von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security/members/update | Aktualisieren der Mitglieder von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security/owners/update | Aktualisieren der Besitzer von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.office365.knowledge/contentUnderstanding/allProperties/allTasks | Lesen und Aktualisieren aller Eigenschaften von Content Understanding in Microsoft 365 Admin Center |
> | microsoft.office365.knowledge/knowledgeNetwork/allProperties/allTasks | Lesen und Aktualisieren aller Eigenschaften des Wissensnetzwerks in Microsoft 365 Admin Center |
> | microsoft.office365.knowledge/learningSources/allProperties/allTasks | Verwalten von Lernquellen und allen zugehörigen Eigenschaften in der Learning-App. |
> | microsoft.office365.protectionCenter/sensitivityLabels/allProperties/read | Lesen aller Eigenschaften von Vertraulichkeitsbezeichnungen im Security and Compliance Center |
> | microsoft.office365.sharePoint/allEntities/allTasks | Erstellen und Löschen aller Ressourcen sowie Lesen und Aktualisieren der Standardeigenschaften in SharePoint |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="knowledge-manager"></a>Wissens-Manager

Benutzer in dieser Rolle können Inhalte wie Themen, Akronyme und Lerninhalte erstellen und verwalten. Diese Benutzer sind in erster Linie für die Qualität und Struktur von Wissen zuständig. Dieser Benutzer hat vollständige Rechte für Themenverwaltungsaktionen zum Bestätigen eines Themas, Genehmigen von Bearbeitungen oder Löschen eines Themas. Diese Rolle kann auch Taxonomien im Rahmen des Begriffs „Speicherverwaltungstool“ verwalten und Inhaltscenter erstellen.

> [!div class="mx-tableFixed"]
> | Aktionen | Beschreibung |
> | --- | --- |
> | microsoft.directory/groups.security/create | Erstellen von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security/createAsOwner | Erstellen von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) Der Ersteller wird als erster Besitzer hinzugefügt. |
> | microsoft.directory/groups.security/delete | Löschen von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security/basic/update | Aktualisieren grundlegender Eigenschaften von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security/members/update | Aktualisieren der Mitglieder von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security/owners/update | Aktualisieren der Besitzer von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.office365.knowledge/contentUnderstanding/analytics/allProperties/read | Lesen von Analyseberichten zum Inhaltsverständnis im Microsoft 365 Admin Center |
> | microsoft.office365.knowledge/knowledgeNetwork/topicVisibility/allProperties/allTasks | Verwalten der Themensichtbarkeit des Wissensnetzwerks im Microsoft 365 Admin Center |
> | microsoft.office365.sharePoint/allEntities/allTasks | Erstellen und Löschen aller Ressourcen sowie Lesen und Aktualisieren der Standardeigenschaften in SharePoint |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="license-administrator"></a>Lizenzadministrator

Benutzer mit dieser Rolle können Lizenzzuweisungen für Benutzer und Gruppen (unter Verwendung der gruppenbasierten Lizenzierung) hinzufügen, entfernen und aktualisieren sowie den Verwendungsstandort für Benutzer verwalten. Mit dieser Rolle ist es nicht möglich, Abonnements zu erwerben oder zu verwalten, Gruppen zu erstellen oder zu verwalten oder Benutzer zu erstellen oder zu verwalten (mit Ausnahme des Verwendungsstandorts). Diese Rolle kann keine Supporttickets anzeigen, erstellen oder verwalten.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/authorizationPolicy/standard/read | Lesen von Standardeigenschaften von Autorisierungsrichtlinien |
> | microsoft.directory/groups/assignLicense | Zuweisen von Produktlizenzen zu Gruppen für die gruppenbasierte Lizenzierung |
> | microsoft.directory/groups/reprocessLicenseAssignment | Erneutes Verarbeiten von Lizenzzuweisungen für die gruppenbasierte Lizenzierung |
> | microsoft.directory/users/assignLicense | Verwalten von Benutzerlizenzen |
> | microsoft.directory/users/reprocessLicenseAssignment | Erneutes Verarbeiten von Lizenzzuweisungen für Benutzer |
> | microsoft.directory/users/usageLocation/update | Aktualisieren des Verwendungsstandorts von Benutzern |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Service Health im Microsoft 365 Admin Center |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="message-center-privacy-reader"></a>Nachrichtencenter-Datenschutzleseberechtigter

Benutzer mit dieser Rolle können alle Benachrichtigungen im Nachrichtencenter überwachen, einschließlich Nachrichten zum Datenschutz. Nachrichtencenter-Datenschutzleseberechtigte erhalten E-Mail-Benachrichtigungen, einschließlich Nachrichten zum Datenschutz, und können Benachrichtigungen mithilfe der Einstellungen im Nachrichtencenter abbestellen. Nur der globale Administrator und Nachrichtencenter-Datenschutzleseberechtigte können Nachrichten zum Datenschutz lesen. Darüber hinaus umfasst diese Rolle die Berechtigung zum Anzeigen von Gruppen, Domänen und Abonnements. Diese Rolle umfasst keine Berechtigung zum Anzeigen, Erstellen oder Verwalten von Service Requests.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Lesen von Nachrichten im Nachrichtencenter im Microsoft 365 Admin Center (mit Ausnahme von Sicherheitsmeldungen) |
> | microsoft.office365.messageCenter/securityMessages/read | Lesen von Sicherheitsmeldungen im Nachrichtencenter im Microsoft 365 Admin Center |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="message-center-reader"></a>Nachrichtencenter-Leser

Benutzer mit dieser Rolle können Benachrichtigungen und empfohlene Integritätsupdates für ihre Organisation und die konfigurierten Dienste wie Exchange, Intune und Microsoft Teams im [Nachrichtencenter](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) überwachen. Nachrichtencenter-Leser erhalten eine wöchentliche E-Mail-Übersicht der Beiträge und Updates und können Beiträge in Microsoft 365 teilen. In Azure AD haben Benutzer mit dieser Rolle nur schreibgeschützten Zugriff auf Azure AD-Dienste wie Benutzer und Gruppen. Diese Rolle kann keine Supporttickets anzeigen, erstellen oder verwalten.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Lesen von Nachrichten im Nachrichtencenter im Microsoft 365 Admin Center (mit Ausnahme von Sicherheitsmeldungen) |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="modern-commerce-user"></a>Modern Commerce User

Darf nicht verwendet werden. Diese Rolle wird automatisch von Commerce zugewiesen und ist weder für eine andere Verwendung vorgesehen, noch wird eine andere Verwendung unterstützt. Die Details hierzu finden Sie unten.

Mit der Rolle „Modern Commerce User“ sind bestimmte Benutzer berechtigt, auf Microsoft 365 Admin Center zuzugreifen und die Navigationseinträge links für **Home**, **Abrechnung** und **Support** anzuzeigen. Der in diesen Bereichen verfügbare Inhalt wird von [commercespezifischen Rollen gesteuert](../../cost-management-billing/manage/understand-mca-roles.md), die Benutzern zugewiesen werden, um Produkte zu verwalten, die sie für sich selbst oder für Ihre Organisation gekauft haben. Dies kann Aufgaben wie das Bezahlen von Rechnungen oder den Zugriff auf Abrechnungskonten und Abrechnungsprofile umfassen.

Benutzer mit der Rolle „Modern Commerce-Benutzer“ verfügen in der Regel über administrative Berechtigungen in anderen Microsoft-Einkaufssystemen, jedoch nicht über die Rollen „Globaler Administrator“ oder „Abrechnungsadministrator“, die für den Zugriff auf Admin Center verwendet werden.

**Wann wird die Rolle „Modern Commerce User“ zugewiesen?**

* **Self-Service-Käufe in Microsoft 365 Admin Center**: Self-Service-Käufe bieten Benutzern die Möglichkeit, neue Produkte zu testen, indem sie sich diese Produkte selbst kaufen oder sich dafür registrieren. Diese Produkte werden in Admin Center verwaltet. Benutzern, die einen Self-Service-Kauf tätigen, werden eine Rolle im Commercesystem und die Rolle „Modern Commerce User“ zugewiesen, damit sie ihre Käufe in Admin Center verwalten können. Administratoren können über [PowerShell](/microsoft-365/commerce/subscriptions/allowselfservicepurchase-powershell) Self-Service-Käufe (für Power BI, Power Apps, Power Automate) blockieren. Weitere Informationen finden Sie unter [Häufig gestellte Fragen zu Self-Service-Einkäufen](/microsoft-365/commerce/subscriptions/self-service-purchase-faq).
* **Käufe über den kommerziellen Microsoft Marketplace:** Wenn ein Benutzer ein Produkt oder einen Dienst von Microsoft AppSource oder im Azure Marketplace kauft, wird ihm ähnlich wie beim Self-Service-Kauf die Rolle „Modern Commerce-Benutzer“ zugewiesen, sofern er nicht über die Rolle „Globaler Administrator“ oder „Abrechnungsadministrator“ verfügt. In einigen Fällen werden Benutzer möglicherweise daran gehindert, diese Käufe durchzuführen. Weitere Informationen finden Sie unter [Kommerzieller Microsoft Marketplace](../../marketplace/marketplace-faq-publisher-guide.yml#what-could-block-a-customer-from-completing-a-purchase-).
* **Vorschläge von Microsoft**: Ein Vorschlag ist ein formales Angebot von Microsoft für Ihre Organisation für den Kauf von Microsoft-Produkten und -Diensten. Wenn die Person, die den Vorschlag annimmt, nicht in Azure AD über eine der Rollen „Globaler Administrator“ oder „Abrechnungsadministrator“ verfügt, wird ihr eine commercespezifische Rolle zugewiesen, um den Vorschlag abzuschließen, sowie die Rolle „Modern Commerce-Benutzer“ für den Zugriff auf Admin Center. Wenn diese Person auf Admin Center zugreifen, kann sie nur Funktionen verwenden, die von ihrer commercespezifischen Rolle autorisiert werden.
* **Commercespezifische Rollen**: Einigen Benutzern werden commercespezifische Rollen zugewiesen. Wenn ein Benutzer kein globaler Administrator oder Abrechnungsadministrator ist, erhält er die Rolle „Modern Commerce-Benutzer“, damit er auf Admin Center zugreifen kann.

Wenn die Zuweisung der Rolle „Modern Commerce User“ für einen Benutzer aufgehoben wird, verliert er den Zugriff auf Microsoft 365 Admin Center. Wenn er Produkte entweder für sich selbst oder für Ihre Organisation verwaltet hat, kann er die Verwaltung nicht fortsetzen. Dies kann das Zuweisen von Lizenzen, das Ändern von Zahlungsmethoden, das Bezahlen von Rechnungen oder andere Aufgaben zum Verwalten von Abonnements umfassen.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.commerce.billing/partners/read | Lesen der Partnereigenschaft der Microsoft 365-Abrechnung |
> | microsoft.commerce.volumeLicenseServiceCenter/allEntities/allTasks | Verwalten sämtlicher Aspekte des Volume Licensing Service Center |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.webPortal/allEntities/basic/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="network-administrator"></a>Netzwerkadministrator

Benutzer mit dieser Rolle können Empfehlungen zur Netzwerkumkreisarchitektur von Microsoft überprüfen, die auf Netzwerktelemetriedaten von ihren Benutzerstandorten basieren. Die Netzwerkleistung für Microsoft 365 basiert auf einer sorgfältigen Netzwerkumkreisarchitektur für Unternehmenskunden, die im Allgemeinen für den Benutzerstandort spezifisch ist. Diese Rolle ermöglicht das Bearbeiten von ermittelten Benutzerstandorten und das Konfigurieren von Netzwerkparametern für diese Standorte, um verbesserte Telemetriemessungen und Entwurfsempfehlungen zu ermöglichen.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.office365.network/locations/allProperties/allTasks | Verwalten sämtlicher Aspekte von Netzwerkstandorten |
> | microsoft.office365.network/performance/allProperties/read | Lesen aller Netzwerkleistungseigenschaften im Microsoft 365 Admin Center |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="office-apps-administrator"></a>Office-Apps-Administrator

Benutzer mit dieser Rolle können die Cloudeinstellungen von Microsoft 365-Apps verwalten. Dazu gehören die Verwaltung von Cloudrichtlinien, die Self-Service-Downloadverwaltung und die Möglichkeit, Office-Apps-bezogene Berichte anzuzeigen. Diese Rolle ermöglicht es außerdem, Supporttickets zu verwalten und die Dienstintegrität im Haupt-Admin Center zu überwachen. Benutzer, denen diese Rolle zugewiesen ist, können außerdem Mitteilungen zu neuen Features in Office-Apps verwalten.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
> | microsoft.office365.messageCenter/messages/read | Lesen von Nachrichten im Nachrichtencenter im Microsoft 365 Admin Center (mit Ausnahme von Sicherheitsmeldungen) |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Service Health im Microsoft 365 Admin Center |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.userCommunication/allEntities/allTasks | Lesen und Aktualisieren der Sichtbarkeit von Meldungen zu neuen Features |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="partner-tier1-support"></a>Partnersupport der Ebene 1

Darf nicht verwendet werden. Diese Rolle wurde als veraltet markiert und wird aus Azure AD entfernt. Diese Rolle ist für einige wenige Wiederverkaufspartner von Microsoft und nicht zur allgemeinen Verwendung vorgesehen.

> [!IMPORTANT]
> Diese Rolle kann nur für Benutzer ohne Administratorrechte Kennwörter zurücksetzen und Aktualisierungstoken ungültig machen. Diese Rolle sollte nicht verwendet werden, da sie veraltet ist und in der API nicht mehr zurückgegeben wird.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/applications/appRoles/update | Aktualisieren der appRoles-Eigenschaft für alle Anwendungstypen |
> | microsoft.directory/applications/audience/update | Aktualisieren der audience-Eigenschaft für Anwendungen |
> | microsoft.directory/applications/authentication/update | Aktualisieren der Authentifizierung für alle Anwendungstypen |
> | microsoft.directory/applications/basic/update | Aktualisieren grundlegender Eigenschaften für Anwendungen |
> | microsoft.directory/applications/credentials/update | Aktualisieren von Anwendungsanmeldeinformationen |
> | microsoft.directory/applications/notes/update | Aktualisieren von Anwendungshinweisen |
> | microsoft.directory/applications/owners/update | Aktualisieren von Anwendungsbesitzern |
> | microsoft.directory/applications/permissions/update | Aktualisieren von verfügbar gemachten und erforderlichen Berechtigungen für alle Anwendungstypen |
> | microsoft.directory/applications/policies/update | Aktualisieren von Anwendungsrichtlinien |
> | microsoft.directory/applications/tag/update | Aktualisieren von Anwendungstags |
> | microsoft.directory/contacts/create | Erstellen von Kontakten |
> | microsoft.directory/contacts/delete | Löschen von Kontakten |
> | microsoft.directory/contacts/basic/update | Aktualisieren grundlegender Eigenschaften für Kontakte |
> | microsoft.directory/deletedItems.groups/restore | Wiederherstellen des ursprünglichen Zustands von soft deleted-Gruppen |
> | microsoft.directory/groups/create | Erstellen von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/delete | Löschen von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/restore | Wiederherstellen gelöschter Gruppen |
> | microsoft.directory/groups/members/update | Aktualisieren der Mitglieder von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/owners/update | Aktualisieren der Besitzer von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Erstellen und Löschen von OAuth 2.0-Berechtigungszuweisungen und Lesen und Aktualisieren aller Eigenschaften |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aktualisieren von Rollenzuweisungen für Dienstprinzipale |
> | microsoft.directory/users/assignLicense | Verwalten von Benutzerlizenzen |
> | microsoft.directory/users/create | Hinzufügen von Benutzern |
> | microsoft.directory/users/delete | Löschen von Benutzern |
> | microsoft.directory/users/disable | Deaktivieren von Benutzern |
> | microsoft.directory/users/enable | Aktivieren von Benutzern |
> | microsoft.directory/users/invalidateAllRefreshTokens | Erzwingen der Abmeldung von Benutzern durch Ungültigmachen des Aktualisierungstokens |
> | microsoft.directory/users/restore | Wiederherstellen gelöschter Benutzer |
> | microsoft.directory/users/basic/update | Aktualisieren grundlegender Eigenschaften für Benutzer |
> | microsoft.directory/users/manager/update | Aktualisieren der Manager für Benutzer |
> | microsoft.directory/users/password/update | Zurücksetzen der Kennwörter für alle Benutzer |
> | microsoft.directory/users/photo/update | Aktualisieren des Fotos von Benutzern |
> | microsoft.directory/users/userPrincipalName/update | Aktualisieren des Benutzerprinzipalnamens von Benutzern |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Service Health im Microsoft 365 Admin Center |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="partner-tier2-support"></a>Partnersupport der Ebene 2

Darf nicht verwendet werden. Diese Rolle wurde als veraltet markiert und wird aus Azure AD entfernt. Diese Rolle ist für einige wenige Wiederverkaufspartner von Microsoft und nicht zur allgemeinen Verwendung vorgesehen.

> [!IMPORTANT]
> Diese Rolle kann für Benutzer mit und ohne Administratorrechte (einschließlich globale Administratoren) Kennwörter zurücksetzen und Aktualisierungstoken ungültig machen. Diese Rolle sollte nicht verwendet werden, da sie veraltet ist und in der API nicht mehr zurückgegeben wird.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/applications/appRoles/update | Aktualisieren der appRoles-Eigenschaft für alle Anwendungstypen |
> | microsoft.directory/applications/audience/update | Aktualisieren der audience-Eigenschaft für Anwendungen |
> | microsoft.directory/applications/authentication/update | Aktualisieren der Authentifizierung für alle Anwendungstypen |
> | microsoft.directory/applications/basic/update | Aktualisieren grundlegender Eigenschaften für Anwendungen |
> | microsoft.directory/applications/credentials/update | Aktualisieren von Anwendungsanmeldeinformationen |
> | microsoft.directory/applications/notes/update | Aktualisieren von Anwendungshinweisen |
> | microsoft.directory/applications/owners/update | Aktualisieren von Anwendungsbesitzern |
> | microsoft.directory/applications/permissions/update | Aktualisieren von verfügbar gemachten und erforderlichen Berechtigungen für alle Anwendungstypen |
> | microsoft.directory/applications/policies/update | Aktualisieren von Anwendungsrichtlinien |
> | microsoft.directory/applications/tag/update | Aktualisieren von Anwendungstags |
> | microsoft.directory/contacts/create | Erstellen von Kontakten |
> | microsoft.directory/contacts/delete | Löschen von Kontakten |
> | microsoft.directory/contacts/basic/update | Aktualisieren grundlegender Eigenschaften für Kontakte |
> | microsoft.directory/deletedItems.groups/restore | Wiederherstellen des ursprünglichen Zustands von soft deleted-Gruppen |
> | microsoft.directory/domains/allProperties/allTasks | Erstellen und Löschen von Domänen sowie Lesen und Aktualisieren aller Eigenschaften |
> | microsoft.directory/groups/create | Erstellen von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/delete | Löschen von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/restore | Wiederherstellen gelöschter Gruppen |
> | microsoft.directory/groups/members/update | Aktualisieren der Mitglieder von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/owners/update | Aktualisieren der Besitzer von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Erstellen und Löschen von OAuth 2.0-Berechtigungszuweisungen und Lesen und Aktualisieren aller Eigenschaften |
> | microsoft.directory/organization/basic/update | Aktualisieren grundlegender Eigenschaften für Organisationen |
> | microsoft.directory/roleAssignments/allProperties/allTasks | Erstellen und Löschen von Rollenzuweisungen und Lesen und Aktualisieren aller Rollenzuweisungseigenschaften |
> | microsoft.directory/roleDefinitions/allProperties/allTasks | Erstellen und Löschen von Rollendefinitionen und Lesen und Aktualisieren aller Eigenschaften |
> | microsoft.directory/scopedRoleMemberships/allProperties/allTasks | Erstellen und Löschen von scopedRoleMemberships und Lesen und Aktualisieren aller Eigenschaften |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aktualisieren von Rollenzuweisungen für Dienstprinzipale |
> | microsoft.directory/subscribedSkus/standard/read | Lesen grundlegender Eigenschaften für Abonnements |
> | microsoft.directory/users/assignLicense | Verwalten von Benutzerlizenzen |
> | microsoft.directory/users/create | Hinzufügen von Benutzern |
> | microsoft.directory/users/delete | Löschen von Benutzern |
> | microsoft.directory/users/disable | Deaktivieren von Benutzern |
> | microsoft.directory/users/enable | Aktivieren von Benutzern |
> | microsoft.directory/users/invalidateAllRefreshTokens | Erzwingen der Abmeldung von Benutzern durch Ungültigmachen des Aktualisierungstokens |
> | microsoft.directory/users/restore | Wiederherstellen gelöschter Benutzer |
> | microsoft.directory/users/basic/update | Aktualisieren grundlegender Eigenschaften für Benutzer |
> | microsoft.directory/users/manager/update | Aktualisieren der Manager für Benutzer |
> | microsoft.directory/users/password/update | Zurücksetzen der Kennwörter für alle Benutzer |
> | microsoft.directory/users/photo/update | Aktualisieren des Fotos von Benutzern |
> | microsoft.directory/users/userPrincipalName/update | Aktualisieren des Benutzerprinzipalnamens von Benutzern |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Service Health im Microsoft 365 Admin Center |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="password-administrator"></a>Kennwortadministrator

Benutzer mit dieser Rolle haben eingeschränkte Möglichkeiten zum Verwalten von Kennwörtern. Mit dieser Rolle werden keine Berechtigungen zum Verwalten von Dienstanforderungen oder zum Überwachen der Dienstintegrität gewährt. Ob ein Kennwortadministrator das Kennwort eines Benutzers zurücksetzen kann, hängt von der Rolle ab, die dem Benutzer zugewiesen ist. Eine Liste der Rollen, für die ein Kennwortadministrator Kennwörter zurücksetzen kann, finden Sie unter [Berechtigungen für die Kennwortzurücksetzung](#password-reset-permissions).

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/users/password/update | Zurücksetzen der Kennwörter für alle Benutzer |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="power-bi-administrator"></a>Power BI-Administrator

Benutzer mit dieser Rolle besitzen globale Berechtigungen innerhalb von Microsoft Power BI, wenn der Dienst verfügbar ist, und können Supporttickets verwalten und die Dienstintegrität überwachen. Weitere Informationen finden Sie unter [Grundlegendes zur Power BI-Administratorrolle](/power-bi/service-admin-role).

> [!NOTE]
> In Microsoft Graph-API und Azure AD PowerShell wird diese Rolle als „Power BI-Dienstadministrator“ bezeichnet. Im [Azure-Portal](https://portal.azure.com) lautet sie „Power BI-Administrator“.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Service Health im Microsoft 365 Admin Center |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |
> | microsoft.powerApps.powerBI/allEntities/allTasks | Verwalten sämtlicher Aspekte von Power BI |

## <a name="power-platform-administrator"></a>Power Platform-Administrator

Benutzer in dieser Rolle können alle Aspekte von Umgebungen, Power Apps, Flows und Richtlinien zur Verhinderung von Datenverlust erstellen und verwalten. Darüber hinaus verfügen Benutzer mit dieser Rolle über die Möglichkeit, Supporttickets zu verwalten und die Dienstintegrität zu überwachen.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
> | microsoft.dynamics365/allEntities/allTasks | Verwalten sämtlicher Aspekte von Dynamics 365 |
> | microsoft.flow/allEntities/allTasks | Verwalten sämtlicher Aspekte von Microsoft Power Automate |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Service Health im Microsoft 365 Admin Center |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |
> | microsoft.powerApps/allEntities/allTasks | Verwalten sämtlicher Aspekte von Power Apps |

## <a name="printer-administrator"></a>Druckeradministrator

Benutzer mit dieser Rolle können Drucker registrieren und alle Aspekte sämtlicher Druckerkonfigurationen in der Microsoft-Lösung für universelles Drucken verwalten, einschließlich der Connectoreinstellungen für universelles Drucken. Sie können in alle delegierten Druckberechtigungsanforderungen einwilligen. Druckeradministratoren haben außerdem Zugriff auf Druckberichte.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.azure.print/allEntities/allProperties/allTasks | Erstellen und Löschen von Druckern und Connectors sowie Lesen und Aktualisieren aller Eigenschaften in Microsoft Print |

## <a name="printer-technician"></a>Druckertechniker

Benutzer mit dieser Rolle können Drucker registrieren und den Druckerstatus in der Microsoft-Lösung für universelles Drucken verwalten. Sie können außerdem alle Connectorinformationen lesen. Zu den Hauptaufgaben, die ein Druckertechniker nicht ausführen kann, gehören das Festlegen von Benutzerberechtigungen für Drucker sowie das Freigeben von Druckern.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.azure.print/connectors/allProperties/read | Lesen aller Eigenschaften von Connectors in Microsoft Print |
> | microsoft.azure.print/printers/allProperties/read | Lesen aller Eigenschaften von Druckern in Microsoft Print |
> | microsoft.azure.print/printers/register | Registrieren von Druckern in Microsoft Print |
> | microsoft.azure.print/printers/unregister | Aufheben der Registrierung von Druckern in Microsoft Print |
> | microsoft.azure.print/printers/basic/update | Aktualisieren grundlegender Eigenschaften von Druckern in Microsoft Print |

## <a name="privileged-authentication-administrator"></a>Privilegierter Authentifizierungsadministrator

Benutzer mit dieser Rolle können alle Authentifizierungsmethoden (einschließlich Kennwörter) für jeden Benutzer (einschließlich globale Administratoren) festlegen oder zurücksetzen. Privilegierte Authentifizierungsadministratoren können eine erneute Registrierung von Benutzern anhand von vorhandenen Anmeldeinformationen ohne Kennwort (z. B. MFA oder FIDO) erzwingen und die Einstellung „Speichern der MFA auf dem Gerät“ widerrufen, sodass alle Benutzer beim nächsten Anmeldevorgang zur mehrstufigen Authentifizierung aufgefordert werden.

Die Rolle [Authentifizierungsadministrator](#authentication-administrator) verfügt über die Berechtigung zum Erzwingen einer erneuten Registrierung und der mehrstufigen Authentifizierung für Standardbenutzer und Benutzer mit einigen Administratorrollen.

Die Rolle [Authentifizierungsrichtlinienadministrator](#authentication-policy-administrator) verfügt über Berechtigungen zum Festlegen der Authentifizierungsmethodenrichtlinie des Mandanten, mit der bestimmt wird, welche Methoden die einzelnen Benutzer registrieren und verwenden können.

| Role | Verwalten der Authentifizierungsmethoden des Benutzers | Aktivieren der Multi-Factor Authentication (MFA) pro Benutzer | Verwalten der MFA-Einstellungen | Verwalten der Authentifizierungsmethodenrichtlinie | Verwalten der Kennwortschutzrichtlinie |
| ---- | ---- | ---- | ---- | ---- | ---- |
| Authentifizierungsadministrator | Ja, für einige Benutzer (siehe oben) | Ja, für einige Benutzer (siehe oben) | Nein | Nein | Nein |
| Privilegierter Authentifizierungsadministrator| Ja, für alle Benutzer | Ja, für alle Benutzer | Nein | Nein | Nein |
| Authentifizierungsrichtlinienadministrator | Nein | Nein | Ja | Ja | Ja |

> [!IMPORTANT]
> Benutzer mit dieser Rolle können Anmeldeinformationen für Benutzer ändern, die Zugriff auf vertrauliche oder private Informationen bzw. kritische Konfigurationen innerhalb und außerhalb von Azure Active Directory haben. Das bedeutet, dass Benutzer, die Anmeldeinformationen ändern können, ggf. auch die Identität und die Berechtigungen des betreffenden Benutzers annehmen können. Beispiel:
>
>* Besitzer von Anwendungsregistrierungen und Unternehmensanwendungen, die Anmeldeinformationen von Apps verwalten können, die sie besitzen. Diese Apps können über höhere Berechtigungen in Azure AD und in anderen Diensten verfügen, die Authentifizierungsadministratoren nicht gewährt werden. Auf diesem Weg kann ein Authentifizierungsadministrator die Identität eines Anwendungsbesitzers annehmen und dann durch Aktualisieren der Anmeldeinformationen für die Anwendung die Identität einer privilegierten Anwendung annehmen.
>* Besitzer von Azure-Abonnements, die ggf. auf vertrauliche oder private Informationen bzw. kritische Konfigurationen in Azure zugreifen können.
>* Besitzer von Sicherheitsgruppen und Microsoft 365-Gruppen, die die Gruppenmitgliedschaft verwalten können. Diese Gruppen können Zugriff auf vertrauliche oder private Informationen bzw. kritische Konfigurationen in Azure AD und in anderen Diensten gewähren.
>* Administratoren in anderen Diensten außerhalb von Azure AD wie Exchange Online, Office Security and Compliance Center und Personalwesen.
>* Nichtadministratoren wie Führungskräfte, Rechtsberater und Mitarbeiter der Personalabteilung mit Zugriff auf vertrauliche oder private Informationen.


> [!IMPORTANT]
> Diese Rolle kann derzeit die Multi-Factor Authentication (MFA) pro Benutzer im alten MFA-Verwaltungsportal nicht verwalten. Die gleichen Funktionen können mithilfe des Cmdlets [Set-MsolUser](/powershell/module/msonline/set-msoluser) im Azure AD PowerShell-Modul erreicht werden.

> [!div class="mx-tableFixed"]
> | Aktionen | Beschreibung |
> | --- | --- |
> | microsoft.directory/users/authenticationMethods/create | Erstellen von Authentifizierungsmethoden für Benutzer |
> | microsoft.directory/users/authenticationMethods/delete | Löschen von Authentifizierungsmethoden für Benutzer |
> | microsoft.directory/users/authenticationMethods/standard/read | Lesen der Standardeigenschaften von Authentifizierungsmethoden für Benutzer |
> | microsoft.directory/users/authenticationMethods/basic/update | Aktualisieren der grundlegenden Eigenschaften von Authentifizierungsmethoden für Benutzer |
> | microsoft.directory/users/invalidateAllRefreshTokens | Erzwingen der Abmeldung von Benutzern durch Ungültigmachen des Aktualisierungstokens |
> | microsoft.directory/users/password/update | Zurücksetzen der Kennwörter für alle Benutzer |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Service Health im Microsoft 365 Admin Center |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="privileged-role-administrator"></a>Administrator für privilegierte Rollen

Benutzer mit dieser Rolle können Rollenzuweisungen in Azure Active Directory und Azure AD Privileged Identity Management verwalten. Sie können Gruppen erstellen und verwalten, die Azure AD-Rollen zugewiesen werden können. Überdies ermöglicht diese Rolle Verwaltung aller Aspekte von Privileged Identity Management und administrativer Einheiten.

> [!IMPORTANT]
> Diese Rolle ermöglicht die Verwaltung von Zuweisungen für alle Azure AD-Rollen, einschließlich der globalen Administratorrolle. Diese Rolle umfasst keine anderen privilegierten Funktionen in Azure AD wie das Erstellen oder Aktualisieren von Benutzern. Benutzer, die dieser Rolle zugewiesen sind, können sich selbst oder anderen jedoch zusätzliche Berechtigungen gewähren, indem sie zusätzliche Rollen zuweisen.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/administrativeUnits/allProperties/allTasks | Erstellen und Verwalten von Verwaltungseinheiten (einschließlich Mitgliedern). |
> | microsoft.directory/authorizationPolicy/allProperties/allTasks | Verwalten sämtlicher Aspekte von Autorisierungsrichtlinien |
> | microsoft.directory/directoryRoles/allProperties/allTasks | Erstellen und Löschen von Verzeichnisrollen sowie Lesen und Aktualisieren aller Eigenschaften |
> | microsoft.directory/groupsAssignableToRoles/create | Erstellen einer Gruppe, der Rollen zugeordnet werden können |
> | microsoft.directory/groupsAssignableToRoles/delete | Löschen von Gruppen, denen Rollen zugewiesen werden können |
> | microsoft.directory/groupsAssignableToRoles/restore | Wiederherstellen von Gruppen, denen Rollen zugewiesen werden können |
> | microsoft.directory/groupsAssignableToRoles/allProperties/update | Aktualisieren von Gruppen, denen Rollen zugeordnet werden können |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Erstellen und Löschen von OAuth 2.0-Berechtigungszuweisungen und Lesen und Aktualisieren aller Eigenschaften |
> | microsoft.directory/privilegedIdentityManagement/allProperties/allTasks | Erstellen und Löschen aller Ressourcen sowie Lesen und Aktualisieren von Standardeigenschaften in Privileged Identity Management |
> | microsoft.directory/roleAssignments/allProperties/allTasks | Erstellen und Löschen von Rollenzuweisungen und Lesen und Aktualisieren aller Rollenzuweisungseigenschaften |
> | microsoft.directory/roleDefinitions/allProperties/allTasks | Erstellen und Löschen von Rollendefinitionen und Lesen und Aktualisieren aller Eigenschaften |
> | microsoft.directory/scopedRoleMemberships/allProperties/allTasks | Erstellen und Löschen von scopedRoleMemberships und Lesen und Aktualisieren aller Eigenschaften |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aktualisieren von Rollenzuweisungen für Dienstprinzipale |
> | microsoft.directory/servicePrincipals/permissions/update | Aktualisieren der Berechtigungen von Dienstprinzipalen |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-company-admin | Erteilen der Zustimmung zu einer beliebigen Berechtigung für eine beliebige Anwendung |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="reports-reader"></a>Meldet Reader

Benutzer mit dieser Rolle können Nutzungsberichtsdaten und das Berichtsdashboard im Microsoft 365 Admin Center sowie das Einführungskontextpaket in Power BI anzeigen. Darüber hinaus stellt die Rolle Zugriff auf Anmeldeberichte und -aktivitäten in Azure AD und von der Microsoft Graph-Berichterstellungs-API zurückgegebene Daten zur Verfügung. Ein Benutzer, dem die Rolle „Meldet Reader“ zugewiesen ist, kann nur auf relevante Nutzungs- und Anpassungsmetriken zugreifen. Sie verfügen nicht über Administratorrechte, um Einstellungen zu konfigurieren oder auf produktspezifische Verwaltungskonsolen wie das Exchange Admin Center zuzugreifen. Diese Rolle kann keine Supporttickets anzeigen, erstellen oder verwalten.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | Lesen sämtlicher Eigenschaften von Überwachungsprotokollen (einschließlich privilegierter Eigenschaften) |
> | microsoft.directory/provisioningLogs/allProperties/read | Lesen aller Eigenschaften von Bereitstellungsprotokollen |
> | microsoft.directory/signInReports/allProperties/read | Lesen sämtlicher Eigenschaften für Anmeldeberichte (einschließlich privilegierter Eigenschaften) |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
> | microsoft.office365.network/performance/allProperties/read | Lesen aller Netzwerkleistungseigenschaften im Microsoft 365 Admin Center |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Lesen von Office 365-Nutzungsberichten |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="search-administrator"></a>Suchadministrator

Benutzer mit dieser Rolle haben Vollzugriff auf alle Microsoft Search-Verwaltungsfunktionen im Microsoft 365 Admin Center. Darüber hinaus können diese Benutzer das Nachrichtencenter anzeigen, die Dienstintegrität überwachen und Service Requests erstellen.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Lesen von Nachrichten im Nachrichtencenter im Microsoft 365 Admin Center (mit Ausnahme von Sicherheitsmeldungen) |
> | microsoft.office365.search/content/manage | Erstellen und Löschen von Inhalten sowie Lesen und Aktualisieren aller Eigenschaften in Microsoft Search |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Service Health im Microsoft 365 Admin Center |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="search-editor"></a>Such-Editor

Benutzer mit dieser Rolle können Inhalte für Microsoft Search im Microsoft 365 Admin Center erstellen, verwalten und löschen. Hierzu gehören Inhalte wie Lesezeichen, Fragen und Antworten sowie Standorte.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Lesen von Nachrichten im Nachrichtencenter im Microsoft 365 Admin Center (mit Ausnahme von Sicherheitsmeldungen) |
> | microsoft.office365.search/content/manage | Erstellen und Löschen von Inhalten sowie Lesen und Aktualisieren aller Eigenschaften in Microsoft Search |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="security-administrator"></a>Sicherheitsadministrator

Benutzer mit dieser Rolle verfügen über Berechtigungen zum Verwalten sicherheitsbezogener Features im Microsoft 365 Defender-Portal, Azure Active Directory Identity Protection, Azure Active Directory-Authentifizierung, Azure Information Protection und Office 365 Security & Compliance Center. Weitere Informationen zu Office 365-Berechtigungen finden Sie unter [Berechtigungen im Security & Compliance Center](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

Geben Sie in | Möglich
--- | ---
[Microsoft 365 Security Center](https://protection.office.com) | Überwachen von sicherheitsrelevanten Richtlinien in Microsoft 365-Diensten<br>Verwalten von Sicherheitsbedrohungen und Warnungen<br>Berichte anzeigen
Identity Protection Center | Alle Berechtigungen der Rolle „Sicherheitsleseberechtigter“<br>Darüber hinaus die Möglichkeit, alle Vorgänge im Identity Protection Center außer des Zurücksetzens von Kennwörtern auszuführen
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Alle Berechtigungen der Rolle „Sicherheitsleseberechtigter“<br>**Kann keine** Azure AD-Rollenzuweisungen oder -Einstellungen verwalten
[Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Verwalten von Sicherheitsrichtlinien<br>Anzeigen, Untersuchen und Reagieren auf Sicherheitsbedrohungen<br>Berichte anzeigen
Azure Advanced Threat Protection | Überwachen und Reagieren auf verdächtige Sicherheitsaktivitäten
Windows Defender ATP und EDR | Zuweisen von Rollen<br>Verwalten von Computergruppen<br>Konfigurieren der Endpunkt-Bedrohungserkennung und der automatisierten Korrektur<br>Anzeigen, Untersuchen und Reagieren auf Warnungen
[Intune](/intune/role-based-access-control) | Anzeigen von Benutzern, Geräten, Registrierung, Konfiguration und Anwendungsinformationen<br>Kann keine Änderungen an Intune vornehmen
[Cloud App Security](/cloud-app-security/manage-admins) | Hinzufügen von Administratoren, Richtlinien und Einstellungen, Hochladen von Protokollen und Ausführen von Governanceaktionen
[Microsoft 365-Dienststatus](/office365/enterprise/view-service-health) | Anzeigen des Status von Microsoft 365-Diensten
[Smart Lockout](../authentication/howto-password-smart-lockout.md) | Hiermit werden der Schwellenwert und die Dauer für Sperren definiert, wenn fehlerhafte Anmeldeereignisse auftreten.
[Kennwortschutz](../authentication/concept-password-ban-bad.md) | Konfigurieren Sie die benutzerdefinierte Liste der gesperrten Kennwörter oder lokalen Kennwortschutz.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/applications/policies/update | Aktualisieren von Anwendungsrichtlinien |
> | microsoft.directory/auditLogs/allProperties/read | Lesen sämtlicher Eigenschaften von Überwachungsprotokollen (einschließlich privilegierter Eigenschaften) |
> | microsoft.directory/authorizationPolicy/standard/read | Lesen von Standardeigenschaften von Autorisierungsrichtlinien |
> | microsoft.directory/bitlockerKeys/key/read | Lesen von BitLocker-Metadaten und -Schlüsseln auf Geräten |
> | microsoft.directory/entitlementManagement/allProperties/read | Lesen sämtlicher Eigenschaften in der Azure AD-Berechtigungsverwaltung |
> | microsoft.directory/identityProtection/allProperties/read | Lesen aller Ressourcen in Azure AD Identity Protection |
> | microsoft.directory/identityProtection/allProperties/update | Aktualisieren aller Ressourcen in Azure AD Identity Protection |
> | microsoft.directory/policies/create | Erstellen von Richtlinien in Azure AD |
> | microsoft.directory/policies/delete | Löschen von Richtlinien in Azure AD |
> | microsoft.directory/policies/basic/update | Aktualisieren grundlegender Eigenschaften für Richtlinien |
> | microsoft.directory/policies/owners/update | Aktualisieren der Besitzer von Richtlinien |
> | microsoft.directory/policies/tenantDefault/update | Aktualisieren von Standardorganisationsrichtlinien |
> | microsoft.directory/conditionalAccessPolicies/create | Erstellen von Richtlinien für den bedingten Zugriff |
> | microsoft.directory/conditionalAccessPolicies/delete | Löschen von Richtlinien für den bedingten Zugriff |
> | microsoft.directory/conditionalAccessPolicies/standard/read | Lesen des bedingten Zugriffs für Richtlinien |
> | microsoft.directory/conditionalAccessPolicies/owners/read | Lesen der Besitzer von Richtlinien für den bedingten Zugriff |
> | microsoft.directory/conditionalAccessPolicies/policyAppliedTo/read | Lesen der Eigenschaft „Angewendet auf“ für Richtlinien für den bedingten Zugriff |
> | microsoft.directory/conditionalAccessPolicies/basic/update | Aktualisieren grundlegender Eigenschaften der Richtlinien für den bedingten Zugriff |
> | microsoft.directory/conditionalAccessPolicies/owners/update | Aktualisieren der Besitzer von Richtlinien für den bedingten Zugriff |
> | microsoft.directory/conditionalAccessPolicies/tenantDefault/update | Aktualisieren des Standardmandanten für Richtlinien für den bedingten Zugriff |
> | microsoft.directory/crossTenantAccessPolicies/create | Erstellen von mandantenübergreifenden Zugriffsrichtlinien |
> | microsoft.directory/crossTenantAccessPolicies/delete | Löschen von mandantenübergreifenden Zugriffsrichtlinien |
> | microsoft.directory/crossTenantAccessPolicies/standard/read | Lesen grundlegender Eigenschaften von mandantenübergreifenden Zugriffsrichtlinien |
> | microsoft.directory/crossTenantAccessPolicies/owners/read | Lesen der Besitzer von mandantenübergreifenden Zugriffsrichtlinien |
> | microsoft.directory/crossTenantAccessPolicies/policyAppliedTo/read | Lesen der policyAppliedTo-Eigenschaft von mandantenübergreifenden Zugriffsrichtlinien |
> | microsoft.directory/crossTenantAccessPolicies/basic/update | Aktualisieren grundlegender Eigenschaften von mandantenübergreifenden Zugriffsrichtlinien |
> | microsoft.directory/crossTenantAccessPolicies/owners/update | Aktualisieren der Besitzer von mandantenübergreifenden Zugriffsrichtlinien |
> | microsoft.directory/crossTenantAccessPolicies/tenantDefault/update | Aktualisieren des Standardmandanten für mandantenübergreifende Zugriffsrichtlinien |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | Lesen aller Ressourcen in Privileged Identity Management |
> | microsoft.directory/provisioningLogs/allProperties/read | Lesen aller Eigenschaften von Bereitstellungsprotokollen |
> | microsoft.directory/servicePrincipals/policies/update | Aktualisieren der Richtlinien für Dienstprinzipale |
> | microsoft.directory/signInReports/allProperties/read | Lesen sämtlicher Eigenschaften für Anmeldeberichte (einschließlich privilegierter Eigenschaften) |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
> | microsoft.office365.protectionCenter/allEntities/standard/read | Lesen der Standardeigenschaften aller Ressourcen im Security & Compliance Center |
> | microsoft.office365.protectionCenter/allEntities/basic/update | Aktualisieren der grundlegenden Eigenschaften aller Ressourcen im Security & Compliance Center |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/allTasks | Erstellen und Verwalten von Angriffsnutzdaten im Angriffssimulator |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | Lesen von Berichten zu Reaktionen auf Angriffssimulationen und zugehörigen Schulungsunterlagen |
> | microsoft.office365.protectionCenter/attackSimulator/simulation/allProperties/allTasks | Erstellen und Verwalten von Angriffssimulationsvorlagen im Angriffssimulator |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Service Health im Microsoft 365 Admin Center |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="security-operator"></a>Sicherheitsoperator

Benutzer mit dieser Rolle können Warnungen verwalten und besitzen globalen schreibgeschützten Zugriff auf sicherheitsbezogene Features. Dies schließt sämtliche Informationen in Microsoft 365 Security Center, Azure Active Directory, Identity Protection, Privileged Identity Management und Office 365 Security & Compliance Center ein. Weitere Informationen zu Office 365-Berechtigungen finden Sie unter [Berechtigungen im Security & Compliance Center](/office365/securitycompliance/permissions-in-the-security-and-compliance-center).

| Geben Sie in | Möglich |
| --- | --- |
| [Microsoft 365 Security Center](https://protection.office.com) | Alle Berechtigungen der Rolle „Sicherheitsleseberechtigter“<br/>Anzeigen und Untersuchen von sowie Reagieren auf Warnungen zu Sicherheitsbedrohungen<br/>Verwalten von Sicherheitseinstellungen in Security Center |
| [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) | Alle Berechtigungen der Rolle „Sicherheitsleseberechtigter“<br>Darüber hinaus die Möglichkeit, alle Vorgänge im Identity Protection Center auszuführen, mit Ausnahme des Zurücksetzens von Kennwörtern und des Konfigurierens von Benachrichtigungs-E-Mails. |
| [Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Alle Berechtigungen der Rolle „Sicherheitsleseberechtigter“ |
| [Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Alle Berechtigungen der Rolle „Sicherheitsleseberechtigter“<br>Anzeigen und Untersuchen von sowie Reagieren auf Sicherheitswarnungen |
| Windows Defender ATP und EDR | Alle Berechtigungen der Rolle „Sicherheitsleseberechtigter“<br>Anzeigen und Untersuchen von sowie Reagieren auf Sicherheitswarnungen |
| [Intune](/intune/role-based-access-control) | Alle Berechtigungen der Rolle „Sicherheitsleseberechtigter“ |
| [Cloud App Security](/cloud-app-security/manage-admins) | Alle Berechtigungen der Rolle „Sicherheitsleseberechtigter“ |
| [Microsoft 365-Dienststatus](/microsoft-365/enterprise/view-service-health) | Anzeigen des Status von Microsoft 365-Diensten |

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | Lesen sämtlicher Eigenschaften von Überwachungsprotokollen (einschließlich privilegierter Eigenschaften) |
> | microsoft.directory/authorizationPolicy/standard/read | Lesen von Standardeigenschaften von Autorisierungsrichtlinien |
> | microsoft.directory/cloudAppSecurity/allProperties/allTasks | Erstellen und Löschen aller Ressourcen sowie Lesen und Aktualisieren von Standardeigenschaften in Microsoft Cloud App Security |
> | microsoft.directory/identityProtection/allProperties/allTasks | Erstellen und Löschen sämtlicher Ressourcen sowie Lesen und Aktualisieren der Standardeigenschaften in Azure AD Identity Protection |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | Lesen aller Ressourcen in Privileged Identity Management |
> | microsoft.directory/provisioningLogs/allProperties/read | Lesen aller Eigenschaften von Bereitstellungsprotokollen |
> | microsoft.directory/signInReports/allProperties/read | Lesen sämtlicher Eigenschaften für Anmeldeberichte (einschließlich privilegierter Eigenschaften) |
> | microsoft.azure.advancedThreatProtection/allEntities/allTasks | Verwalten sämtlicher Aspekte von Azure Advanced Threat Protection |
> | microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
> | microsoft.intune/allEntities/read | Lesen aller Ressourcen in Microsoft Intune |
> | microsoft.office365.securityComplianceCenter/allEntities/allTasks | Erstellen und Löschen aller Ressourcen und Lesen und Aktualisieren von Standardeigenschaften im Office 365 Security & Compliance Center. |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.windows.defenderAdvancedThreatProtection/allEntities/allTasks | Verwalten sämtlicher Aspekte von Microsoft Defender für Endpunkt |

## <a name="security-reader"></a>Sicherheitsleseberechtigter

Benutzer mit dieser Rolle besitzen globalen schreibgeschützten Zugriff auf sicherheitsbezogene Features, einschließlich alle Informationen in Microsoft 365 Security Center, Azure Active Directory, Identity Protection, und Privileged Identity Management, sowie die Möglichkeit zum Lesen von Azure Active Directory-Anmeldeberichten und Überwachungsprotokollen und für das Office 365 Security & Compliance Center. Weitere Informationen zu Office 365-Berechtigungen finden Sie unter [Berechtigungen im Security & Compliance Center](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

Geben Sie in | Möglich
--- | ---
[Microsoft 365 Security Center](https://protection.office.com) | Anzeigen von sicherheitsrelevanten Richtlinien in Microsoft 365-Diensten<br>Anzeigen von Sicherheitsbedrohungen und Warnungen<br>Berichte anzeigen
Identity Protection Center | Lesen von allen Sicherheitsberichten und Einstellungsinformationen für die Sicherheitsfunktionen<br><ul><li>Antispam<li>Verschlüsselung<li>Verhindern von Datenverlusten<li>Antischadsoftware<li>Erweiterter Schutz vor Bedrohungen<li>Antiphishing<li>Regeln für den Nachrichtenfluss
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Verfügt über schreibgeschützten Zugriff auf alle eingeblendeten Informationen in Azure AD Privileged Identity Management: Richtlinien und Berichte für Azure AD-Rollenzuweisungen und Sicherheitsüberprüfungen.<br>Kann sich **nicht** für Azure AD Privileged Identity Management registrieren oder Änderungen daran vornehmen. Im Privileged Identity Management-Portal oder über PowerShell können Personen mit dieser Rolle zusätzliche Rollen (z. B. „Globaler Administrator“ oder „Administrator für privilegierte Rollen“) aktivieren, wenn der Benutzer dazu berechtigt ist.
[Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Anzeigen von Sicherheitsrichtlinien<br>Anzeigen und Untersuchen von Sicherheitsbedrohungen<br>Berichte anzeigen
Windows Defender ATP und EDR | Anzeigen und Untersuchen von Warnungen. Wenn Sie in Windows Defender ATP die rollenbasierte Zugriffssteuerung aktivieren, verlieren Benutzer mit reinen Leseberechtigungen (wie die Rolle „Azure AD-Sicherheitsleseberechtigter“) den Zugriff, bis ihnen eine Windows Defender ATP-Rolle zugewiesen wird.
[Intune](/intune/role-based-access-control) | Anzeigen von Benutzern, Geräten, Registrierung, Konfiguration und Anwendungsinformationen Kann keine Änderungen an Intune vornehmen
[Cloud App Security](/cloud-app-security/manage-admins) | Verfügt über Leseberechtigungen und kann Warnungen verwalten
[Microsoft 365-Dienststatus](/office365/enterprise/view-service-health) | Anzeigen des Status von Microsoft 365-Diensten

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | Lesen sämtlicher Eigenschaften von Überwachungsprotokollen (einschließlich privilegierter Eigenschaften) |
> | microsoft.directory/authorizationPolicy/standard/read | Lesen von Standardeigenschaften von Autorisierungsrichtlinien |
> | microsoft.directory/bitlockerKeys/key/read | Lesen von BitLocker-Metadaten und -Schlüsseln auf Geräten |
> | microsoft.directory/entitlementManagement/allProperties/read | Lesen sämtlicher Eigenschaften in der Azure AD-Berechtigungsverwaltung |
> | microsoft.directory/identityProtection/allProperties/read | Lesen aller Ressourcen in Azure AD Identity Protection |
> | microsoft.directory/policies/standard/read | Lesen grundlegender Eigenschaften für Richtlinien |
> | microsoft.directory/policies/owners/read | Lesen der Besitzer von Richtlinien |
> | microsoft.directory/policies/policyAppliedTo/read | Lesen der policies.policyAppliedTo-Eigenschaft |
> | microsoft.directory/conditionalAccessPolicies/standard/read | Lesen des bedingten Zugriffs für Richtlinien |
> | microsoft.directory/conditionalAccessPolicies/owners/read | Lesen der Besitzer von Richtlinien für den bedingten Zugriff |
> | microsoft.directory/conditionalAccessPolicies/policyAppliedTo/read | Lesen der Eigenschaft „Angewendet auf“ für Richtlinien für den bedingten Zugriff |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | Lesen aller Ressourcen in Privileged Identity Management |
> | microsoft.directory/provisioningLogs/allProperties/read | Lesen aller Eigenschaften von Bereitstellungsprotokollen |
> | microsoft.directory/signInReports/allProperties/read | Lesen sämtlicher Eigenschaften für Anmeldeberichte (einschließlich privilegierter Eigenschaften) |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
> | microsoft.office365.protectionCenter/allEntities/standard/read | Lesen der Standardeigenschaften aller Ressourcen im Security & Compliance Center |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/read | Lesen aller Eigenschaften von Angriffsnutzdaten im Angriffssimulator |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | Lesen von Berichten zu Reaktionen auf Angriffssimulationen und zugehörigen Schulungsunterlagen |
> | microsoft.office365.protectionCenter/attackSimulator/simulation/allProperties/read | Lesen aller Eigenschaften von Angriffssimulationsvorlagen im Angriffssimulator |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Service Health im Microsoft 365 Admin Center |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="service-support-administrator"></a>Dienstunterstützungsadministrator

Benutzer mit dieser Rolle können bei Microsoft Supportanfragen für Azure- und Microsoft 365-Dienste öffnen sowie das Dienstdashboard und Nachrichtencenter im [Azure-Portal](https://portal.azure.com) und im [Microsoft 365 Admin Center](https://admin.microsoft.com) anzeigen. Weitere Informationen finden Sie unter [Informationen zu Administratorrollen](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> Bisher wurde diese Rolle im [Azure-Portal](https://portal.azure.com) und [Microsoft 365 Admin Center](https://admin.microsoft.com) als „Dienstadministrator“ bezeichnet. Wir haben diese Rolle umbenannt in „Dienstsupportadministrator“, um sie der bereits in der Microsoft Graph-API, der Azure AD Graph-API und in Azure AD PowerShell vorhandenen Bezeichnung anzupassen.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
> | microsoft.office365.network/performance/allProperties/read | Lesen aller Netzwerkleistungseigenschaften im Microsoft 365 Admin Center |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Service Health im Microsoft 365 Admin Center |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="sharepoint-administrator"></a>SharePoint-Administrator

Benutzer mit dieser Rolle besitzen globale Berechtigungen innerhalb von Microsoft SharePoint Online, wenn der Dienst verfügbar ist, und sie können alle Microsoft 365-Gruppen erstellen und verwalten, Supporttickets verwalten sowie die Dienstintegrität überwachen. Weitere Informationen finden Sie unter [Informationen zu Administratorrollen](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> In Microsoft Graph-API und Azure AD PowerShell wird diese Rolle als „SharePoint-Dienstadministrator“ bezeichnet. Im [Azure-Portal](https://portal.azure.com) lautet sie „SharePoint-Administrator“.

> [!NOTE]
> Diese Rolle gewährt auch der Microsoft Graph-API bereichsbezogene Berechtigungen für Microsoft Intune, um die Verwaltung und Konfiguration von Richtlinien für SharePoint- und OneDrive-Ressourcen zu ermöglichen.

> [!div class="mx-tableFixed"]
> | Aktionen | Beschreibung |
> | --- | --- |
> | microsoft.directory/deletedItems.groups/delete | Dauerhaftes Löschen von Gruppen, die nicht mehr wiederhergestellt werden können. |
> | microsoft.directory/deletedItems.groups/restore | Wiederherstellen des ursprünglichen Zustands von soft deleted-Gruppen |
> | microsoft.directory/groups.unified/create | Erstellen von Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.unified/delete | Löschen von Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.unified/restore | Wiederherstellen von Microsoft 365-Gruppen |
> | microsoft.directory/groups.unified/basic/update | Aktualisieren grundlegender Eigenschaften von Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.unified/members/update | Aktualisieren der Mitglieder von Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.unified/owners/update | Aktualisieren der Besitzer von Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
> | microsoft.office365.network/performance/allProperties/read | Lesen aller Netzwerkleistungseigenschaften im Microsoft 365 Admin Center |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Service Health im Microsoft 365 Admin Center |
> | microsoft.office365.sharePoint/allEntities/allTasks | Erstellen und Löschen aller Ressourcen sowie Lesen und Aktualisieren der Standardeigenschaften in SharePoint |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Lesen von Office 365-Nutzungsberichten |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="skype-for-business-administrator"></a>Skype for Business-Administrator

Benutzer mit dieser Rolle besitzen globale Berechtigungen in Microsoft Skype for Business, wenn der Dienst vorhanden ist, sowie die Berechtigung zur Verwaltung von Skype-spezifischen Benutzerattributen in Azure Active Directory. Darüber hinaus bietet diese Rolle die Möglichkeit, Supporttickets zu verwalten und die Dienstintegrität zu überwachen, sowie auf die Admin Center von Teams und Skype for Business zuzugreifen. Das Konto muss auch für Teams lizenziert sein, andernfalls kann es keine PowerShell-Cmdlets von Teams ausführen. Weitere Informationen dazu finden Sie unter [Skype for Business Online-Administrator](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) und Informationen zur Teams-Lizenzierung unter [Add-On-Lizenzierung für Skype for Business und Microsoft Teams](/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing).

> [!NOTE]
> In Microsoft Graph-API und Azure AD PowerShell wird diese Rolle als „Lync-Dienstadministrator“ bezeichnet. Im [Azure-Portal](https://portal.azure.com/) lautet sie „Skype for Business-Administrator“.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Service Health im Microsoft 365 Admin Center |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Verwalten sämtlicher Aspekte von Skype for Business Online |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Lesen von Office 365-Nutzungsberichten |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="teams-administrator"></a>Teams-Administrator

Benutzer mit dieser Rolle können alle Aspekte der Microsoft Teams-Workload über das Admin Center von Microsoft Teams und Skype for Business und die entsprechenden PowerShell-Module verwalten. Dazu zählen unter anderem alle Verwaltungstools im Zusammenhang mit Telefonie, Messaging, Besprechungen und den Teams selbst. Außerdem bietet diese Rolle die Möglichkeit zum Erstellen und Verwalten aller Microsoft 365-Gruppen, Verwalten von Supporttickets und Überwachen der Dienstintegrität.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/authorizationPolicy/standard/read | Lesen von Standardeigenschaften von Autorisierungsrichtlinien |
> | microsoft.directory/deletedItems.groups/delete | Dauerhaftes Löschen von Gruppen, die nicht mehr wiederhergestellt werden können. |
> | microsoft.directory/deletedItems.groups/restore | Wiederherstellen des ursprünglichen Zustands von soft deleted-Gruppen |
> | microsoft.directory/groups/hiddenMembers/read | Lesen der ausgeblendeten Mitglieder von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen eingeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.unified/create | Erstellen von Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.unified/delete | Löschen von Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.unified/restore | Wiederherstellen von Microsoft 365-Gruppen |
> | microsoft.directory/groups.unified/basic/update | Aktualisieren grundlegender Eigenschaften von Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.unified/members/update | Aktualisieren der Mitglieder von Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.unified/owners/update | Aktualisieren der Besitzer von Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | Gewähren des direkten Zugriffs auf Gruppendaten für einen Dienstprinzipal |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
> | microsoft.office365.network/performance/allProperties/read | Lesen aller Netzwerkleistungseigenschaften im Microsoft 365 Admin Center |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Service Health im Microsoft 365 Admin Center |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Verwalten sämtlicher Aspekte von Skype for Business Online |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Lesen von Office 365-Nutzungsberichten |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |
> | microsoft.azure.print/allEntities/allProperties/allTasks | Verwalten aller Ressourcen in Teams |

## <a name="teams-communications-administrator"></a>Teams-Kommunikationsadministrator

Benutzer mit dieser Rolle können Aspekte der Microsoft Teams-Workload im Zusammenhang mit Sprache und Telefonie verwalten. Dazu gehören die Verwaltungstools für die Telefonnummernzuweisung, Sprach- und Besprechungsrichtlinien und der uneingeschränkte Zugriff auf das Toolset zur Anrufanalyse.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/authorizationPolicy/standard/read | Lesen von Standardeigenschaften von Autorisierungsrichtlinien |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Service Health im Microsoft 365 Admin Center |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Verwalten sämtlicher Aspekte von Skype for Business Online |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Lesen von Office 365-Nutzungsberichten |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |
> | microsoft.teams/callQuality/allProperties/read | Lesen sämtlicher Daten im Anrufsqualitäts-Dashboard |
> | microsoft.teams/meetings/allProperties/allTasks | Verwalten von Besprechungen (einschließlich Besprechungsrichtlinien, Konfigurationen und Konferenzbrücken) |
> | microsoft.teams/voice/allProperties/allTasks | Verwalten von Sprachanrufen (einschließlich Anrufrichtlinien sowie Verwalten und Zuweisen von Telefonnummern) |

## <a name="teams-communications-support-engineer"></a>Teams-Kommunikationssupporttechniker

Benutzer in dieser Rolle können Kommunikationsprobleme innerhalb von Microsoft Teams und Skype for Business mithilfe der Problembehandlungstools für Benutzeranrufe im Admin Center für Microsoft Teams und Skype for Business behandeln. Benutzer in dieser Rolle können vollständige Anrufdatensatzinformationen für alle Teilnehmer anzeigen. Diese Rolle kann keine Supporttickets anzeigen, erstellen oder verwalten.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/authorizationPolicy/standard/read | Lesen von Standardeigenschaften von Autorisierungsrichtlinien |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Service Health im Microsoft 365 Admin Center |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Verwalten sämtlicher Aspekte von Skype for Business Online |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |
> | microsoft.teams/callQuality/allProperties/read | Lesen sämtlicher Daten im Anrufqualitäts-Dashboard |

## <a name="teams-communications-support-specialist"></a>Teams-Kommunikationssupportspezialist

Benutzer in dieser Rolle können Kommunikationsprobleme innerhalb von Microsoft Teams und Skype for Business mithilfe der Problembehandlungstools für Benutzeranrufe im Admin Center für Microsoft Teams und Skype for Business behandeln. Benutzer in dieser Rolle können Benutzerdetails im Anruf nur für den bestimmten Benutzer anzeigen, nach dem sie gesucht haben. Diese Rolle kann keine Supporttickets anzeigen, erstellen oder verwalten.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/authorizationPolicy/standard/read | Lesen von Standardeigenschaften von Autorisierungsrichtlinien |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Service Health im Microsoft 365 Admin Center |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Verwalten sämtlicher Aspekte von Skype for Business Online |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |
> | microsoft.teams/callQuality/standard/read | Lesen grundlegender Daten im Anrufqualitäts-Dashboard |

## <a name="teams-devices-administrator"></a>Teams-Geräteadministrator

Benutzer mit dieser Rolle können im Teams Admin Center [für Teams zertifizierte Geräte](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) verwalten. Diese Rolle bietet eine Übersicht über alle Geräte, mit der Möglichkeit, Geräte zu suchen und zu filtern. Der Benutzer kann Details zu jedem Gerät überprüfen, darunter das angemeldete Konto sowie Marke und Modell des Geräts. Der Benutzer kann die Einstellungen auf dem Gerät ändern und die Softwareversionen aktualisieren. Diese Rolle gewährt keine Berechtigungen zum Überprüfen der Teams-Aktivität und der Anrufqualität des Geräts.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |
> | microsoft.teams/devices/standard/read | Verwalten sämtlicher Aspekte von in Teams zertifizierten Geräten (einschließlich Konfigurationsrichtlinien) |

## <a name="usage-summary-reports-reader"></a>Leseberechtigter für Berichte mit Nutzungszusammenfassung

Benutzer mit dieser Rolle können zur Nutzungs- und Produktivitätsbewertung im Microsoft 365 Admin Center auf aggregierte Daten sowie zugehörige Erkenntnisse auf Mandantenebene, aber nicht auf Details oder Erkenntnisse auf Benutzerebene zugreifen. Im Microsoft 365 Admin Center für die beiden Berichte wird zwischen aggregierten Daten auf Mandantenebene und Details auf Benutzerebene unterschieden. Diese Rolle bietet eine zusätzliche Sicherheitsebene für personenbezogene Daten, die von Kunden und Rechtsabteilungen angefordert werden.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.office365.network/performance/allProperties/read | Lesen aller Netzwerkleistungseigenschaften im Microsoft 365 Admin Center |
> | microsoft.office365.usageReports/allEntities/standard/read | Lesen aggregierter Office 365-Nutzungsberichte auf Mandantenebene |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="user-administrator"></a>Benutzeradministrator

Benutzer mit dieser Rolle können Benutzer erstellen und alle Aspekte von Benutzern verwalten (mit einigen Einschränkungen, siehe Tabelle). Außerdem können sie Richtlinien für den Kennwortablauf aktualisieren. Außerdem können Benutzer mit dieser Rolle Gruppen erstellen und verwalten. Zudem haben sie die Möglichkeit, Benutzeransichten und Supporttickets zu verwalten sowie die Dienstintegrität zu überwachen. Benutzeradministratoren haben keine Berechtigungen zum Verwalten bestimmter Benutzereigenschaften für Benutzer in den meisten Administratorrollen. Administratoren mit dieser Rolle haben keine Berechtigungen zum Verwalten der mehrstufigen Authentifizierung (MFA) oder von freigegebenen Postfächern. Die Rollen, die von dieser Einschränkung ausgenommen sind, sind in der folgenden Tabelle aufgeführt.

| Berechtigung von Benutzeradministratoren | Notizen |
| --- | --- |
| Erstellen von Benutzern und Gruppen<br/>Erstellen und Verwalten von Benutzeransichten<br/>Verwalten von Office-Supporttickets<br/>Aktualisieren von Richtlinien für den Kennwortablauf |  |
| Verwalten von Lizenzen<br/>Verwalten aller Benutzereigenschaften mit Ausnahme des Benutzerprinzipalnamens | Gilt für alle Benutzer einschließlich aller Administratoren |
| Löschen und Wiederherstellen<br/>Deaktivieren und Aktivieren<br/>Verwalten aller Benutzereigenschaften einschließlich des Benutzerprinzipalnamens<br/>Aktualisieren von Geräteschlüsseln (FIDO) | Gilt nur für Benutzer, die keine Administratoren sind und keine der folgenden Rollen haben:<ul><li>Helpdeskadministrator</li><li>Benutzer ohne Rolle</li><li>Benutzeradministrator</li></ul> |
| Annullieren von Aktualisierungstoken<br/>Kennwort zurücksetzen | Eine Liste der Rollen, für die ein Benutzeradministrator Kennwörter zurücksetzen und Aktualisierungstoken ungültig machen kann, finden Sie unter [Berechtigungen für die Kennwortzurücksetzung](#password-reset-permissions). |

> [!IMPORTANT]
> Benutzer mit dieser Rolle können Kennwörter für Benutzer ändern, die Zugriff auf vertrauliche oder private Informationen bzw. kritische Konfigurationen innerhalb und außerhalb von Azure Active Directory haben. Benutzer, die Kennwörter ändern können, können ggf. auch die Identität und die Berechtigungen des betreffenden Benutzers annehmen. Beispiel:
>
>- Besitzer von Anwendungsregistrierungen und Unternehmensanwendungen, die Anmeldeinformationen von Apps verwalten können, die sie besitzen. Diese Apps können über höhere Berechtigungen in Azure AD und in anderen Diensten verfügen, die Benutzeradministratoren nicht gewährt werden. So kann ein Benutzeradministrator die Identität eines Anwendungsbesitzers annehmen und dann die Identität einer privilegierten Anwendung durch Aktualisieren der Anmeldeinformationen für die Anwendung annehmen.
>- Besitzer von Azure-Abonnements, die ggf. auf vertrauliche oder private Informationen bzw. kritische Konfigurationen in Azure zugreifen können.
>- Besitzer von Sicherheitsgruppen und Microsoft 365-Gruppen, die die Gruppenmitgliedschaft verwalten können. Diese Gruppen können Zugriff auf vertrauliche oder private Informationen bzw. kritische Konfigurationen in Azure AD und in anderen Diensten gewähren.
>- Administratoren in anderen Diensten außerhalb von Azure AD wie Exchange Online, Office Security and Compliance Center und Personalwesen.
>- Nichtadministratoren wie Führungskräfte, Rechtsberater und Mitarbeiter der Personalabteilung mit Zugriff auf vertrauliche oder private Informationen.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.directory/contacts/create | Erstellen von Kontakten |
> | microsoft.directory/contacts/delete | Löschen von Kontakten |
> | microsoft.directory/contacts/basic/update | Aktualisieren grundlegender Eigenschaften für Kontakte |
> | microsoft.directory/deletedItems.groups/restore | Wiederherstellen des ursprünglichen Zustands von soft deleted-Gruppen |
> | microsoft.directory/entitlementManagement/allProperties/allTasks | Erstellen und Löschen von Ressourcen sowie Lesen und Aktualisieren aller Eigenschaften in der Azure AD-Berechtigungsverwaltung |
> | microsoft.directory/groups/assignLicense | Zuweisen von Produktlizenzen zu Gruppen für die gruppenbasierte Lizenzierung |
> | microsoft.directory/groups/create | Erstellen von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/delete | Löschen von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/hiddenMembers/read | Lesen der ausgeblendeten Mitglieder von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen eingeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/reprocessLicenseAssignment | Erneutes Verarbeiten von Lizenzzuweisungen für die gruppenbasierte Lizenzierung |
> | microsoft.directory/groups/restore | Wiederherstellen gelöschter Gruppen |
> | microsoft.directory/groups/basic/update | Aktualisieren grundlegender Eigenschaften von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/classification/update | Aktualisieren der Klassifizierungseigenschaft von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/dynamicMembershipRule/update | Aktualisieren der Regel für eine dynamische Mitgliedschaft für Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/groupType/update | Aktualisieren von Eigenschaften, die sich auf den Gruppentyp von Sicherheitsgruppen und Microsoft 365-Gruppen auswirken (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/members/update | Aktualisieren der Mitglieder von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/onPremWriteBack/update | Aktualisieren von Azure Active Directory-Gruppen, die mit Azure AD Connect in die lokale Umgebung zurückgeschrieben werden sollen |
> | microsoft.directory/groups/owners/update | Aktualisieren der Besitzer von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups/settings/update | Aktualisieren von Gruppeneinstellungen |
> | microsoft.directory/groups/visibility/update | Aktualisieren der visibility-Eigenschaft von Sicherheitsgruppen und Microsoft 365-Gruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Erstellen und Löschen von OAuth 2.0-Berechtigungszuweisungen und Lesen und Aktualisieren aller Eigenschaften |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aktualisieren von Rollenzuweisungen für Dienstprinzipale |
> | microsoft.directory/users/assignLicense | Verwalten von Benutzerlizenzen |
> | microsoft.directory/users/create | Hinzufügen von Benutzern |
> | microsoft.directory/users/delete | Löschen von Benutzern |
> | microsoft.directory/users/disable | Deaktivieren von Benutzern |
> | microsoft.directory/users/enable | Aktivieren von Benutzern |
> | microsoft.directory/users/inviteGuest | Einladen von Gastbenutzern |
> | microsoft.directory/users/invalidateAllRefreshTokens | Erzwingen der Abmeldung von Benutzern durch Ungültigmachen des Aktualisierungstokens |
> | microsoft.directory/users/reprocessLicenseAssignment | Erneutes Verarbeiten von Lizenzzuweisungen für Benutzer |
> | microsoft.directory/users/restore | Wiederherstellen gelöschter Benutzer |
> | microsoft.directory/users/basic/update | Aktualisieren grundlegender Eigenschaften für Benutzer |
> | microsoft.directory/users/manager/update | Aktualisieren der Manager für Benutzer |
> | microsoft.directory/users/password/update | Zurücksetzen der Kennwörter für alle Benutzer |
> | microsoft.directory/users/photo/update | Aktualisieren des Fotos von Benutzern |
> | microsoft.directory/users/userPrincipalName/update | Aktualisieren des Benutzerprinzipalnamens von Benutzern |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Service Health im Microsoft 365 Admin Center |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="windows-365-administrator"></a>Windows 365-Administrator

Benutzer mit dieser Rolle verfügen über globale Berechtigungen für Windows 365-Ressourcen, wenn der Dienst vorhanden ist. Darüber hinaus beinhaltet diese Rolle die Möglichkeit, Benutzer und Geräte zum Zuordnen von Richtlinien zu verwalten sowie Gruppen zu erstellen und zu verwalten.

Diese Rolle kann Sicherheitsgruppen erstellen und verwalten, verfügt jedoch nicht über Administratorrechte für Microsoft 365-Gruppen. Das bedeutet, dass Administratoren Besitzer oder Mitgliedschaften von Microsoft 365-Gruppen in der Organisation nicht aktualisieren können. Sie können jedoch die Microsoft 365-Gruppe verwalten, die sie erstellen. Dies ist ein Teil ihrer Endbenutzerberechtigungen. Daher werden alle Microsoft 365-Gruppen (keine Sicherheitsgruppe), die sie erstellen, ihrem Kontingent von 250 angerechnet.

Weisen Sie Benutzern, die die folgenden Aufgaben ausführen müssen, die Rolle Windows 365-Administrator zu:

- Verwalten von Windows 365 Cloud-PCs in Microsoft Endpoint Manager
- Registrieren und Verwalten von Geräten in Azure AD, einschließlich Zuweisen von Benutzern und Richtlinien
- Erstellen und Verwalten von Sicherheitsgruppen, aber nicht von Rollen zuweisbaren Gruppen
- Anzeigen grundlegender Eigenschaften im Microsoft 365 Admin Center
- Lesen von Nutzungsberichten im Microsoft 365 Admin Center
- Erstellen und Verwalten von Supporttickets in Azure AD und im Microsoft 365 Admin Center

> [!div class="mx-tableFixed"]
> | Aktionen | Beschreibung |
> | --- | --- |
> | microsoft.directory/devices/create | Erstellen von Geräten (bei Azure AD registrieren) |
> | microsoft.directory/devices/delete | Löschen von Geräten aus Azure AD |
> | microsoft.directory/devices/disable | Deaktivieren von Geräten in Azure AD |
> | microsoft.directory/devices/enable | Aktivieren von Geräten in Azure AD |
> | microsoft.directory/devices/basic/update | Aktualisieren grundlegender Eigenschaften für Geräte |
> | microsoft.directory/devices/extensionAttributeSet1/update | Aktualisieren der Eigenschaften „extensionAttribute1“ bis „extensionAttribute5“ auf Geräten |
> | microsoft.directory/devices/extensionAttributeSet2/update | Aktualisieren der Eigenschaften „extensionAttribute6“ bis „extensionAttribute10“ auf Geräten |
> | microsoft.directory/devices/extensionAttributeSet3/update | Aktualisieren der Eigenschaften „extensionAttribute11“ bis „extensionAttribute15“ auf Geräten |
> | microsoft.directory/devices/registeredOwners/update | Lesen der registrierten Besitzer von Geräten |
> | microsoft.directory/devices/registeredUsers/update | Aktualisieren der registrierten Besitzer von Geräten |
> | microsoft.directory/groups.security/create | Erstellen von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security/delete | Löschen von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security/basic/update | Aktualisieren grundlegender Eigenschaften von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security/classification/update | Aktualisieren der classification-Eigenschaft von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security/dynamicMembershipRule/update | Aktualisieren der Regel für die dynamische Mitgliedschaft von Sicherheitsgruppen (mit Ausnahme der Gruppen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security/members/update | Aktualisieren der Mitglieder von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security/owners/update | Aktualisieren der Besitzer von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/groups.security/visibility/update | Aktualisieren der visibility-Eigenschaft von Sicherheitsgruppen (Gruppen ausgeschlossen, denen Rollen zugewiesen werden können) |
> | microsoft.directory/deviceManagementPolicies/standard/read | Lesen der Standardeigenschaften von Anwendungsrichtlinien zur Geräteverwaltung |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | Lesen der Standardeigenschaften von Richtlinien zur Geräteregistrierung |
> | microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
> | microsoft.cloudPC/allEntities/allProperties/allTasks | Verwalten sämtlicher Aspekte von Windows 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Microsoft 365-Serviceanforderungen |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Lesen von Office 365-Nutzungsberichten |
> | microsoft.office365.webPortal/allEntities/standard/read | Lesen grundlegender Eigenschaften für alle Ressourcen im Microsoft 365 Admin Center |

## <a name="windows-update-deployment-administrator"></a>Windows Update-Bereitstellungsadministrator

Benutzer mit dieser Rolle können alle Aspekte von Windows Update-Bereitstellungen über den Windows Update for Business-Bereitstellungsdienst erstellen und verwalten. Mit dem Bereitstellungsdienst können Benutzer festlegen, wann und wie Updates bereitgestellt werden, und angeben, welche Updates für Gruppen von Geräten in ihrem Mandanten angeboten werden. Darüber hinaus können Benutzer den Updatefortschritt überwachen.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | microsoft.windows.updatesDeployments/allEntities/allProperties/allTasks | Lesen und Konfigurieren aller Aspekte des Windows Update-Diensts |

## <a name="how-to-understand-role-permissions"></a>Grundlegendes zu Rollenberechtigungen

Das Schema für Berechtigungen folgt im Wesentlichen dem REST-Format von Microsoft Graph:

`<namespace>/<entity>/<propertySet>/<action>`

Beispiel:

`microsoft.directory/applications/credentials/update`

| Berechtigungselement | BESCHREIBUNG |
| --- | --- |
| Namespace | Produkt oder Dienst zum Verfügbarmachen der Aufgabe, Präfix: `microsoft`. Beispielsweise verwenden alle Aufgaben in Azure AD den Namespace `microsoft.directory`. |
| Entität | Logische Funktion oder Komponente, die vom Dienst in Microsoft Graph verfügbar gemacht wird. Beispielsweise macht Azure AD Benutzer und Gruppen verfügbar, OneNote macht Notizen verfügbar, und Exchange macht Postfächer und Kalender verfügbar. Es gibt ein spezielles Schlüsselwort `allEntities` zum Angeben aller Entitäten in einem Namespace. Dieses wird häufig in Rollen verwendet, die Zugriff auf ein gesamtes Produkt gewähren. |
| propertySet | Bestimmte Eigenschaften oder Aspekte der Entität, für die der Zugriff gewährt wird. Beispielsweise bietet `microsoft.directory/applications/authentication/read` die Möglichkeit, die Antwort-URL, die Abmelde-URL und eine implizite Floweigenschaft für das Anwendungsobjekt in Azure AD zu lesen.<ul><li>`allProperties` legt alle Eigenschaften der Entität fest, einschließlich privilegierter Eigenschaften.</li><li>`standard` legt allgemeine Eigenschaften fest, schließt jedoch privilegierte Eigenschaften im Zusammenhang mit der Aktion `read` aus. Beispielsweise bietet `microsoft.directory/user/standard/read` die Möglichkeit, Standardeigenschaften wie die öffentliche Telefonnummer und E-Mail-Adresse zu lesen, aber nicht die private sekundäre Telefonnummer oder E-Mail-Adresse, die für die mehrstufige Authentifizierung verwendet wird.</li><li>`basic` legt allgemeine Eigenschaften fest, schließt jedoch privilegierte Eigenschaften im Zusammenhang mit der Aktion `update` aus. Die Eigenschaften, die Sie lesen können, unterscheiden sich möglicherweise von denen, die Sie aktualisieren können. Um dies abzubilden, gibt es die Schlüsselwörter `standard` und `basic`.</li></ul> |
| action | Der gewährte Vorgang, in der Regel CRUD (Create, Read, Update, Delete). Es gibt ein spezielles Schlüsselwort `allTasks` zum Angeben aller oben genannten Möglichkeiten (Create, Read, Update, Delete). |

## <a name="deprecated-roles"></a>Veraltete Rollen

Die folgenden Rollen sollten nicht verwendet werden. Sie sind veraltet und werden aus Azure AD entfernt.

* Ad-hoc-Lizenzadministrator
* Geräteeinbindung
* Geräte-Manager
* Gerätebenutzer
* Benutzererstellung mit E-Mail-Überprüfung
* Postfachadministrator
* Geräteeinbindung am Arbeitsplatz

## <a name="roles-not-shown-in-the-portal"></a>Im Portal nicht angezeigte Rollen

Nicht jede Rolle, die von PowerShell oder der MS Graph-API zurückgegeben wird, wird Azure-Portal angezeigt. Diese Unterschiede sind in der folgenden Tabelle aufgelistet.

API-Name | Name im Azure-Portal | Notizen
-------- | ------------------- | -------------
Geräteeinbindung | Als veraltet markiert | [Dokumentation zu veralteten Rollen](#deprecated-roles)
Geräte-Manager | Als veraltet markiert | [Dokumentation zu veralteten Rollen](#deprecated-roles)
Gerätebenutzer | Als veraltet markiert | [Dokumentation zu veralteten Rollen](#deprecated-roles)
Konten zur Verzeichnissynchronisierung | Nicht angezeigt, da keine Verwendung erfolgen soll | [Dokumentation zu Konten für die Verzeichnissynchronisierung](#directory-synchronization-accounts)
Gastbenutzer | Nicht angezeigt, weil keine Verwendung erfolgen kann | Nicht verfügbar
Partnersupport der Ebene 1 | Nicht angezeigt, da keine Verwendung erfolgen soll | [Dokumentation zum Partnersupport der Ebene 1](#partner-tier1-support)
Partnersupport der Ebene 2 | Nicht angezeigt, da keine Verwendung erfolgen soll | [Dokumentation zum Partnersupport der Ebene 2](#partner-tier2-support)
Eingeschränkter Gastbenutzer | Nicht angezeigt, weil keine Verwendung erfolgen kann | Nicht verfügbar
Benutzer | Nicht angezeigt, weil keine Verwendung erfolgen kann | Nicht verfügbar
Geräteeinbindung am Arbeitsplatz | Als veraltet markiert | [Dokumentation zu veralteten Rollen](#deprecated-roles)

## <a name="password-reset-permissions"></a>Berechtigungen für die Kennwortzurücksetzung

Spaltenüberschriften stellen die Rollen dar, die Kennwörter zurücksetzen können. Tabellenzeilen enthalten die Rollen, für die das Kennwort zurückgesetzt werden kann.

Kennwort kann zurückgesetzt werden | Kennwortadministrator | Helpdeskadministrator | Authentifizierungsadministrator | Benutzeradministrator | Privilegierter Authentifizierungsadministrator | Globaler Administrator
------ | ------ | ------ | ------ | ------ | ------ | ------
Authentifizierungsadministrator | &nbsp; | &nbsp; | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Rolle „Verzeichnis lesen“ | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Globaler Administrator | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:\*
Gruppenadministrator | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Gasteinladender | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Helpdeskadministrator | &nbsp; | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Nachrichtencenter-Leser | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Kennwortadministrator | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Privilegierter Authentifizierungsadministrator | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Administrator für privilegierte Rollen | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Meldet Reader | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Benutzer<br/>(keine Administratorrolle) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Benutzer<br/>(keine Administratorrolle, aber Mitglied einer Gruppe, der Rollen zugewiesen werden können) | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Benutzeradministrator | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Leseberechtigter für Berichte mit Nutzungszusammenfassung | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:

\* Ein globaler Administrator kann seine eigene Zuweisung als globaler Administrator nicht aufheben. Dadurch wird verhindert, dass eine Organisation über keine globalen Administratoren verfügt.

## <a name="next-steps"></a>Nächste Schritte

- [Zuweisen von Azure AD-Rollen zu Gruppen](groups-assign-role.md)
- [Grundlegendes zu den verschiedenen Rollen](../../role-based-access-control/rbac-and-directory-admin-roles.md)
- [Zuweisen von Administratorzugriff für ein Azure-Abonnement](../../role-based-access-control/role-assignments-portal-subscription-admin.md)
