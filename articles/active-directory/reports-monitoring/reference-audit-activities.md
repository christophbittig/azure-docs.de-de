---
title: Referenz zu Überwachungsaktivitäten von Azure Active Directory (Azure AD) | Microsoft-Dokumentation
description: Hier finden Sie eine Übersicht über die Überwachungsaktivitäten, die in Ihren Überwachungsprotokollen in Azure Active Directory (Azure AD) protokolliert werden können.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/24/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49e40ec9fd5b17ff740bd914d37cbac00c6a640a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131462744"
---
# <a name="azure-ad-audit-activity-reference"></a>Referenz zu Überwachungsaktivitäten von Azure AD

Mit Azure AD-Berichten (Azure Active Directory) können Sie alle Informationen abrufen, die Sie zum Ermitteln des Zustands Ihrer Umgebung benötigen.

Die Architektur für die Berichterstellung in Azure AD umfasst die folgenden Komponenten:

- **Aktivitätsberichte** 
    - [Anmeldungen](concept-sign-ins.md): Bietet Informationen zur Nutzung von verwalteten Anwendungen und Aktivitäten der Benutzeranmeldung.
    - [Überwachungsprotokolle:](concept-audit-logs.md) Ermöglichen die Nachverfolgung sämtlicher Änderungen, die von verschiedenen Features in Azure AD vorgenommen wurden. 
    
- **Sicherheitsberichte** 
    - [Riskante Anmeldungen:](../identity-protection/overview-identity-protection.md) Eine riskante Anmeldung ist ein Indikator für einen Anmeldeversuch von einem Benutzer, der nicht der rechtmäßige Besitzer eines Benutzerkontos ist. 
    - [Benutzer mit Risikomarkierung:](../identity-protection/overview-identity-protection.md) Ein Benutzer mit Risikomarkierung ist ein Indikator für ein möglicherweise kompromittiertes Benutzerkonto. 

Dieser Artikel enthält die Überwachungsaktivitäten, die in Ihren Überwachungsprotokollen protokolliert werden können:

## <a name="access-reviews"></a>Zugriffsüberprüfungen

|Überwachungskategorie|Aktivität|
|---|---|
|Zugriffsüberprüfungen|Zugriffsüberprüfung beendet|
|Zugriffsüberprüfungen|Genehmigende Person zu Anforderungsgenehmigung hinzufügen|
|Zugriffsüberprüfungen|Reviewer zu Zugriffsüberprüfung hinzufügen|
|Zugriffsüberprüfungen|Zugriffsüberprüfung anwenden|
|Zugriffsüberprüfungen|Zugriffsüberprüfung erstellen|
|Zugriffsüberprüfungen|Programm erstellen|
|Zugriffsüberprüfungen|Anforderungsgenehmigung erstellen|
|Zugriffsüberprüfungen|Zugriffsüberprüfung löschen|
|Zugriffsüberprüfungen|Programm löschen|
|Zugriffsüberprüfungen|Programmsteuerung verknüpfen|
|Zugriffsüberprüfungen|Integration in Azure AD-Zugriffsüberprüfungen durchführen|
|Zugriffsüberprüfungen|Reviewer aus Zugriffsüberprüfung entfernen|
|Zugriffsüberprüfungen|Überprüfungsbeendigung anfordern|
|Zugriffsüberprüfungen|Anwenden der Überprüfungsergebnisse anfordern|
|Zugriffsüberprüfungen|RBAC-Rollenmitgliedschaft überprüfen|
|Zugriffsüberprüfungen|App-Zuweisung überprüfen|
|Zugriffsüberprüfungen|Gruppenmitgliedschaft überprüfen|
|Zugriffsüberprüfungen|Genehmigungsanforderung überprüfen|
|Zugriffsüberprüfungen|Verknüpfung der Programmsteuerung aufheben|
|Zugriffsüberprüfungen|Zugriffsüberprüfung aktualisieren|
|Zugriffsüberprüfungen|Onboardingstatus von Azure AD-Zugriffsüberprüfungen aktualisieren|
|Zugriffsüberprüfungen|Einstellungen für E-Mail-Benachrichtigungen für Zugriffsüberprüfung aktualisieren|
|Zugriffsüberprüfungen|Einstellung für die Wiederholungsanzahl der Zugriffsüberprüfungen aktualisieren|
|Zugriffsüberprüfungen|Einstellung für die Wiederholungsdauer der Zugriffsüberprüfungen in Tagen aktualisieren|
|Zugriffsüberprüfungen|Einstellung für den Wiederholungsendtyp der Zugriffsüberprüfungen aktualisieren|
|Zugriffsüberprüfungen|Einstellung für den Wiederholungstyp der Zugriffsüberprüfungen aktualisieren|
|Zugriffsüberprüfungen|Einstellungen für Erinnerungen für Zugriffsüberprüfung aktualisieren|
|Zugriffsüberprüfungen|Programm aktualisieren|
|Zugriffsüberprüfungen|Anforderungsgenehmigung aktualisieren|
|Zugriffsüberprüfungen|Benutzer deaktiviert|

## <a name="account-provisioning"></a>Kontobereitstellung

|Überwachungskategorie|Aktivität|
|---|---|
|Anwendungsverwaltung|Retrieve V2 application permissions grants (Berechtigungszuweisungen für V2-Anwendung abrufen)|
|Anwendungsverwaltung|Retrieve V2 application service principals in the current tenant (Dienstprinzipale der V2-Anwendung im aktuellen Mandanten abrufen)|
|Anwendungsverwaltung|Update V1 application (V1-Anwendung aktualisieren)|
|Anwendungsverwaltung|Update V2 application (V2-Anwendung aktualisieren)|
|Anwendungsverwaltung|Update V2 application permission grant (Berechtigungszuweisung für V2-Anwendung aktualisieren)|
|Anwendungsverwaltung|Add OAuth2PermissionGrant (OAuth2PermissionGrant hinzufügen)|
|Anwendungsverwaltung|Add app role assignment to service principal (App-Rollenzuweisung zu Dienstprinzipal hinzufügen)|

## <a name="application-proxy"></a>Anwendungsproxy

|Überwachungskategorie|Aktivität|
|---|---|
|Anwendungsverwaltung|Anwendung hinzufügen|
|Anwendungsverwaltung|Besitzer der Anwendung hinzufügen|
|Anwendungsverwaltung|Add owner to service principal (Besitzer zu Dienstprinzipal hinzufügen)|
|Anwendungsverwaltung|Richtlinie dem Dienstprinzipal hinzufügen|
|Verzeichnisverwaltung|Dienstprinzipal hinzufügen|
|Verzeichnisverwaltung|Anmeldeinformationen für Dienstprinzipal hinzufügen|
|Verzeichnisverwaltung|Consent to application (Anwendung zustimmen)|
|Verzeichnisverwaltung|Löschen der Anwendung|
|Verzeichnisverwaltung|Hard Delete application (Anwendung endgültig löschen)|
|Verzeichnisverwaltung|Remove OAuth2PermissionGrant (OAuth2PermissionGrant entfernen)|
|Verzeichnisverwaltung|Remove app role assignment from service principal (App-Rollenzuweisung von Dienstprinzipal entfernen)|
|Verzeichnisverwaltung|Besitzer aus Anwendung entfernen|
|Resource|Remove owner from service principal (Besitzer aus Dienstprinzipal entfernen)|
|Resource|Richtlinie aus Dienstprinzipal entfernen|
|Resource|Dienstprinzipal entfernen|


## <a name="automated-password-rollover"></a>Automated password rollover (Automatisiertes Kennwortrollover)

|Überwachungskategorie|Aktivität|
|---|---|
|Anwendungsverwaltung|Anmeldeinformationen für Dienstprinzipal entfernen|


## <a name="b2c"></a>B2C

|Überwachungskategorie|Aktivität|
|---|---|
|Anwendungsverwaltung|Anwendung wiederherstellen|
|Anwendungsverwaltung|Zustimmung widerrufen|
|Anwendungsverwaltung|Aktualisieren der Anwendung|
|Anwendungsverwaltung|Externe geheime Schlüssel aktualisieren|
|Anwendungsverwaltung|Update service principal (Dienstprinzipal aktualisieren)|
|Anwendungsverwaltung|Issue an access token to the application (Zugriffstoken für die Anwendung ausstellen)|
|Anwendungsverwaltung|Issue an authorization code to the application (Autorisierungscode für die Anwendung ausstellen)|
|Anwendungsverwaltung|Issue an id_token to the application (ID-Token für die Anwendung ausstellen)|
|Anwendungsverwaltung|Validate local account credentials (Anmeldeinformationen des lokalen Kontos überprüfen)|
|Anwendungsverwaltung|Validate user authentication (Benutzerauthentifizierung überprüfen)|
|Anwendungsverwaltung|Add V2 application permissions (V2-Anwendungsberechtigungen hinzufügen)|
|Anwendungsverwaltung|Add a key based on ASCII secret to a CPIM key container (Schlüssel basierend auf einem ASCII-Geheimnis zu einem CPIM-Schlüsselcontainer hinzufügen)|
|Anwendungsverwaltung|Add a key to a CPIM key container (Schlüssel zu einem CPIM-Schlüsselcontainer hinzufügen)|
|Anwendungsverwaltung|AdminPolicyDatas-SetResources|
|Anwendungsverwaltung|AdminUserJourneys-GetResources|
|Anwendungsverwaltung|AdminUserJourneys-RemoveResources|
|Authentifizierung|AdminUserJourneys-SetResources|
|Authentifizierung|Create IdentityProvider (IdentityProvider-Element erstellen)|
|Authentifizierung|Create V1 application (V1-Anwendung erstellen)|
|Authentifizierung|Create V2 application (V2-Anwendung erstellen)|
|Authentifizierung|Create a custom domains in the tenant (Benutzerdefinierte Domäne im Mandanten erstellen)|
|Authorization|Create a new AdminUserJourney (Neues AdminUserJourney-Element erstellen)|
|Authorization|Create localized resource json (Lokalisierten JSON-Ressourcencode erstellen)|
|Authorization|Create new Custom IDP (Neuen benutzerdefinierten IDP erstellen)|
|Authorization|Create new IDP (Neuen IDP erstellen)|
|Authorization|Create or update a B2C directory resource (B2C-Verzeichnisressourcen erstellen oder aktualisieren)|
|Authorization|Richtlinie erstellen|
|Authorization|Create trustFramework policy (Vertrauensframework-Richtlinie erstellen)|
|Authorization|Create trustFramework policy with configurable prefix (Vertrauensframework-Richtlinie mit konfigurierbarem Präfix erstellen)|
|Authorization|Create user attribute (Benutzerattribut erstellen)|
|Authorization|CreateTrustFrameworkPolicy|
|Authorization|Neues AdminUserJourney-Element erstellen oder aktualisieren|
|Authorization|Identitätsanbieter löschen|
|Authorization|Delete IdentityProvider (IdentityProvider-Element löschen)|
|Authorization|Delete V1 application (V1-Anwendung löschen)|
|Authorization|Delete V2 application (V2-Anwendung löschen)|
|Authorization|Delete V2 application permission grant (Berechtigungszuweisung für V2-Anwendung löschen)|
|Authorization|Delete a B2C directory resource (B2C-Verzeichnisressource löschen)|
|Authorization|Delete a CPIM key container (CPIM-Schlüsselcontainer löschen)|
|Authorization|Delete trustFramework policy (Vertrauensframework-Richtlinie löschen)|
|Authorization|Benutzerattribut löschen|
|Authorization|Enable B2C feature (B2C-Feature aktivieren)|
|Authorization|Get B2C directory resources in a subscription (B2C-Verzeichnisressourcen in einem Abonnement abrufen)|
|Authorization|Get Custom IDP (Benutzerdefinierten IDP abrufen)|
|Authorization|Get IDP (IDP abrufen)|
|Authorization|Get V1 and V2 applications (V1- und V2-Anwendungen abrufen)|
|Authorization|Get V1 application (V1-Anwendung abrufen)|
|Authorization|Get V1 applications (V1-Anwendungen abrufen)|
|Authorization|Get V2 application (V2-Anwendung abrufen)|
|Authorization|Get V2 applications (V2-Anwendungen abrufen)|
|Authorization|B2C-Verzeichnisressource abrufen|
|Authorization|Get a list of custom domains in the tenant (Liste der benutzerdefinierten Domänen im Mandanten abrufen)|
|Authorization|Get a user journey (User Journey abrufen)|
|Authorization|Get allowed application claims for user journey (Zulässige Anwendungsansprüche für User Journey abrufen)|
|Authorization|Get allowed self-asserted claims for user journey (Zulässige selbstbestätigte Ansprüche für User Journey abrufen)|
|Authorization|Get allowed self-asserted claims of policy (Zulässige selbstbestätigte Ansprüche der Richtlinie abrufen)|
|Authorization|Get available output claims list (Liste der verfügbaren Ausgabeansprüche abrufen)|
|Authorization|Get content definitions for user journey (Inhaltsdefinitionen für User Journey abrufen)|
|Authorization|Get idps for a specific admin flow (IDPs für einen bestimmten Verwaltungsflow abrufen)|
|Authorization|Get key container active key metadata in JWK (Schlüsselcontainermetadaten zu aktiven Schlüsseln abrufen)|
|Authorization|Get list of all admin flows (Liste aller Verwaltungsflows abrufen)|
|Authorization|Get list of tags for all admin flows for all users (Liste der Tags für alle Verwaltungsflows für alle Benutzer abrufen)|
|Authorization|Get list of tenants for a user (Liste der Mandanten für einen Benutzer abrufen)|
|Authorization|Get local accounts' self-asserted claims (Selbstbestätigte Ansprüche der lokalen Konten abrufen)|
|Authorization|Get localized resource json (Lokalisierten JSON-Ressourcencode abrufen)|
|Authorization|Get operations of Microsoft.AzureActiveDirectory resource provider (Vorgänge des Microsoft.AzureActiveDirectory-Ressourcenanbieters abrufen)|
|Authorization|Get policies (Richtlinien abrufen)|
|Authorization|Get policy (Richtlinie abrufen)|
|Authorization|Get resource properties of a tenant (Ressourceneigenschaften eines Mandanten abrufen)|
|Authorization|Get supported IDP list (Liste der unterstützten IDPs abrufen)|
|Authorization|Get supported IDP list of the user journey (Liste der unterstützten IDPs der User Journey abrufen)|
|Authorization|Get tenant Info (Abrufen der Mandanteninformationen)|
|Authorization|Get tenant allowed features (Vom Mandanten zugelassene Features abrufen)|
|Authorization|Get tenant defined Custom IDP list (Vom Mandanten festgelegte Liste der benutzerdefinierten IDPs abrufen)|
|Authorization|Get tenant defined IDP list (Vom Mandanten festgelegte IDP-Liste)|
|Authorization|Get tenant defined local IDP list (Vom Mandanten festgelegte Liste der lokalen IDPs)|
|Authorization|Get tenant details for a user for resource creation (Mandantendetails für einen Benutzer zur Ressourcenerstellung abrufen)|
|Authorization|Get tenant list (Mandantenliste abrufen)|
|Authorization|Get tenantDomains (tenantDomains-Element abrufen)|
|Authorization|Get the default supported culture for CPIM (Standardmäßige unterstützte Kultur für CPIM abrufen)|
|Authorization|Get the details of an admin flow (Details eines Verwaltungsflows abrufen)|
|Authorization|Get the list of UserJourneys for this tenant (UserJourneys-Liste für diesen Mandanten abrufen)|
|Authorization|Get the set of available supported cultures for CPIM (Satz der verfügbaren unterstützten Kulturen für CPIM abrufen)|
|Authorization|Get trustFramework policy (Vertrauensframework-Richtlinie abrufen)|
|Authorization|Get trustFramework policy as xml (Vertrauensframework-Richtlinie als XML abrufen)|
|Authorization|Get user attribute (Benutzerattribut abrufen)|
|Authorization|Get user attributes (Benutzerattribute abrufen)|
|Authorization|Get user journey list (User Journey-Liste abrufen)|
|Authorization|GetIEFPolicies|
|Authorization|GetIdentityProviders|
|Authorization|GetTrustFrameworkPolicy|
|Authorization|Gets a CPIM key container in jwk format (CPIM-Schlüsselcontainer im JWK-Format abrufen)|
|Authorization|Gets list of key containers in the tenant (Liste der Schlüsselcontainer im Mandanten abrufen)|
|Authorization|Gets the type of tenant (Mandantentyp abrufen)|
|Authorization|MigrateTenantMetadata|
|Authorization|Patch IdentityProvider (IdentityProvider patchen)|
|Authorization|PutTrustFrameworkPolicy|
|Authorization|PutTrustFrameworkpolicy|
|Authorization|User Journey entfernen|
|Authorization|Restore a CPIM key container backup (Sicherung eines CPIM-Schlüsselcontainers wiederherstellen)|
|Authorization|Retrieve V2 application permissions grants (Berechtigungszuweisungen für V2-Anwendung abrufen)|
|Authorization|Retrieve V2 application service principals in the current tenant (Dienstprinzipale der V2-Anwendung im aktuellen Mandanten abrufen)|
|Authorization|Update Custom IDP (Benutzerdefinierten IDP aktualisieren)|
|Authorization|Update IDP (IDP aktualisieren)|
|Authorization|Lokalen IDP aktualisieren|
|Authorization|Update V1 application (V1-Anwendung aktualisieren)|
|Authorization|Update V2 application (V2-Anwendung aktualisieren)|
|Authorization|Update V2 application permission grant (Berechtigungszuweisung für V2-Anwendung aktualisieren)|
|Authorization|Aktualisieren von Richtlinien|
|Authorization|Update user attribute (Benutzerattribut aktualisieren)|
|Authorization|Upload a CPIM encrypted key (Verschlüsselten CPIM-Schlüssel hochladen)|
|Authorization|Benutzerautorisierung: Die API ist für die Mandantenfeatures deaktiviert.|
|Authorization|Benutzerautorisierung: Dem Benutzer wurde Zugriff als „Mandantenadministrator“ gewährt.|
|Authorization|Benutzerautorisierung: Dem Benutzer wurden Zugriffsrechte vom Typ „Authentifizierte Benutzer“ gewährt.|
|Authorization|Verify if B2C feature is enabled (Überprüfen, ob das B2C-Feature aktiviert ist)|
|Authorization|Überprüfen, ob das Feature aktiviert ist|
|Authorization|Programm erstellen|
|Authorization|Programm löschen|
|Authorization|Programmsteuerung verknüpfen|
|Authorization|Integration in Azure AD-Zugriffsüberprüfungen durchführen|
|Authorization|Verknüpfung der Programmsteuerung aufheben|
|Authorization|Programm aktualisieren|
|Authorization|Disable Desktop Sso (Desktop-SSO deaktivieren)|
|Authorization|Disable Desktop Sso for a specific domain (Desktop-SSO für eine bestimmte Domäne deaktivieren)|
|Authorization|Anwendungsproxy deaktivieren|
|Authorization|Passthrough-Authentifizierung deaktivieren|
|Authorization|Enable Desktop Sso (Desktop-SSO aktivieren)|
|Verzeichnisverwaltung|Enable Desktop Sso for a specific domain (Desktop-SSO für eine bestimmte Domäne aktivieren)|
|Verzeichnisverwaltung|Anwendungsproxy aktivieren|
|Verzeichnisverwaltung|Passthrough-Authentifizierung aktivieren|
|Verzeichnisverwaltung|Create a custom domains in the tenant (Benutzerdefinierte Domäne im Mandanten erstellen)|
|Verzeichnisverwaltung|Enable B2C feature (B2C-Feature aktivieren)|
|Verzeichnisverwaltung|Get a list of custom domains in the tenant (Liste der benutzerdefinierten Domänen im Mandanten abrufen)|
|Verzeichnisverwaltung|Get resource properties of a tenant (Ressourceneigenschaften eines Mandanten abrufen)|
|Verzeichnisverwaltung|Get tenant Info (Abrufen der Mandanteninformationen)|
|Verzeichnisverwaltung|Get tenant allowed features (Vom Mandanten zugelassene Features abrufen)|
|Verzeichnisverwaltung|Get tenantDomains (tenantDomains-Element abrufen)|
|Schlüssel|Gets the type of tenant (Mandantentyp abrufen)|
|Schlüssel|Verify if B2C feature is enabled (Überprüfen, ob das B2C-Feature aktiviert ist)|
|Schlüssel|Überprüfen, ob das Feature aktiviert ist|
|Schlüssel|Partner zu Unternehmen hinzufügen|
|Schlüssel|Add inverified domain (Nicht überprüfte Domäne hinzufügen)|
|Schlüssel|Add verified domain (Überprüfte Domäne hinzufügen)|
|Schlüssel|Unternehmen erstellen|
|Schlüssel|Unternehmenseinstellungen erstellen|
|Schlüssel|Unternehmenseinstellungen löschen|
|Schlüssel|Partner tiefer stufen|
|Schlüssel|Directory deleted (Verzeichnis gelöscht)|
|Andere|Directory deleted permanently (Verzeichnis unwiderruflich gelöscht)|
|Andere|Directory scheduled for deletion (Löschen des Verzeichnisses geplant)|
|Resource|Promote company to partner (Unternehmen zu Partner heraufstufen)|
|Resource|Rights Management-Eigenschaften bereinigen|
|Resource|Partner aus Unternehmen entfernen|
|Resource|Remove unverified domain (Nicht überprüfte Domäne entfernen)|
|Resource|Remove verified domain (Überprüfte Domäne entfernen)|
|Resource|Unternehmensinformationen festlegen|
|Resource|DirSync-Funktion festlegen|
|Resource|DirSyncEnabled-Flag festlegen|
|Resource|Set Partnership (Partnerschaft festlegen)|
|Resource|Schwellenwert für versehentliches Löschen festlegen|
|Resource|Zulässigen Datenspeicherort für Unternehmen festlegen|
|Resource|Funktion für multinationales Unternehmen auf „Aktiviert“ festlegen|
|Resource|Verzeichnisfunktion für Mandanten festlegen|
|Resource|Domänenauthentifizierung festlegen|
|Resource|Verbundeinstellungen für Domäne festlegen|
|Resource|Kennwortrichtlinie festlegen|
|Resource|Eigenschaften für Rights Management festlegen|
|Resource|Update company (Unternehmen aktualisieren)|
|Resource|Unternehmenseinstellungen aktualisieren|
|Resource|Domäne aktualisieren|
|Resource|Domäne überprüfen|
|Resource|Per E-Mail verifizierte Domäne überprüfen|
|Resource|Onboarding|
|Resource|Warnungseinstellungen aktualisieren|
|Resource|Update weekly digest settings (Einstellungen für wöchentliche Übersicht aktualisieren)|
|Resource|Disable password writeback for directory (Kennwortrückschreiben für Verzeichnis deaktivieren)|
|Resource|Enable password writeback for directory (Kennwortrückschreiben für Verzeichnis aktivieren)|
|Resource|Add app role assignment to group (App-Rollenzuweisung zu Gruppe hinzufügen)|
|Resource|Gruppe hinzufügen|
|Resource|Mitglied zur Gruppe hinzufügen|
|Resource|Add owner to group (Besitzer zu Gruppe hinzufügen)|
|Resource|Create group settings (Gruppeneinstellungen erstellen)|
|Resource|Gruppe löschen|
|Resource|Delete group settings (Gruppeneinstellungen löschen)|
|Resource|Finish applying group based license to users (Gruppenbasierte Lizenzzuweisung zu Benutzern fertig stellen)|
|Resource|Hard Delete group (Gruppe endgültig löschen)|
|Resource|Remove app role assignment from group (App-Rollenzuweisung von Gruppe entfernen)|
|Resource|Mitglied aus Gruppe entfernen|
|Resource|Remove owner from group (Besitzer aus Gruppe entfernen)|
|Resource|Gruppe wiederherstellen|
|Resource|Set group license (Gruppenlizenz festlegen)|
|Resource|Gruppe für Verwaltung durch Benutzer festgelegt|
|Resource|Start applying group based license to users (Starten der gruppenbasierten Lizenzzuweisung zu Benutzern)|
|Resource|Trigger group license recalculation (Neuberechnung der Gruppenlizenzen auslösen)|
|Resource|Gruppe aktualisieren|
|Resource|Update group settings (Gruppeneinstellungen aktualisieren)|
|Resource|Mitglied hinzufügen|
|Resource|Erstellen einer Gruppe|
|Resource|Gruppe löschen|
|Resource|Mitglied entfernen|
|Resource|Gruppe aktualisieren|
|Resource|Approve a pending request to join a group (Ausstehende Gruppenbeitrittsanforderung genehmigen)|
|Resource|Cancel a pending request to join a group (Ausstehende Gruppenbeitrittsanforderung abbrechen)|
|Resource|Create lifecycle management policy (Richtlinie für Lebenszyklusverwaltung erstellen)|
|Resource|Delete a pending request to join a group (Ausstehende Gruppenbeitrittsanforderung löschen)|
|Resource|Reject a pending request to join a group (Ausstehende Gruppenbeitrittsanforderung ablehnen)|
|Resource|Gruppe verlängern|
|Resource|Request to join a group (Gruppenbeitritt anfordern)|
|Resource|Set dynamic group properties (Dynamische Gruppeneigenschaften festlegen)|
|Resource|Update lifecycle management policy (Richtlinie für Lebenszyklusverwaltung aktualisieren)|
|Resource|Add a key based on ASCII secret to a CPIM key container (Schlüssel basierend auf einem ASCII-Geheimnis zu einem CPIM-Schlüsselcontainer hinzufügen)|
|Resource|Add a key to a CPIM key container (Schlüssel zu einem CPIM-Schlüsselcontainer hinzufügen)|
|Resource|Delete a CPIM key container (CPIM-Schlüsselcontainer löschen)|
|Resource|Delete key container (Schlüsselcontainer löschen)|
|Resource|Get key container active key metadata in JWK (Schlüsselcontainermetadaten zu aktiven Schlüsseln abrufen)|
|Resource|Get key container metadata (Metadaten des Schlüsselcontainers abrufen)|
|Resource|Gets a CPIM key container in jwk format (CPIM-Schlüsselcontainer im JWK-Format abrufen)|
|Resource|Gets list of key containers in the tenant (Liste der Schlüsselcontainer im Mandanten abrufen)|
|Resource|Restore a CPIM key container backup (Sicherung eines CPIM-Schlüsselcontainers wiederherstellen)|
|Resource|Save key container (Schlüsselcontainer speichern)|
|Resource|Upload a CPIM encrypted key (Verschlüsselten CPIM-Schlüssel hochladen)|
|Resource|Issue an authorization code to the application (Autorisierungscode für die Anwendung ausstellen)|
|Resource|Issue an id_token to the application (ID-Token für die Anwendung ausstellen)|


## <a name="core-directory"></a>Core directory (Kernverzeichnis)

|Überwachungskategorie|Aktivität|
|---|---|
|Verwaltung administrativer Einheiten|Download a single risk detection type (Einzelnen Risikoerkennungstyp herunterladen)|
|Verwaltung administrativer Einheiten|Download admins and status of weekly digest opt-in (Administratoren und Status der Aktivierung der wöchentlichen Übersicht herunterladen)|
|Verwaltung administrativer Einheiten|Download all risk detection types (Alle Risikoerkennungstypen herunterladen)|
|Verwaltung administrativer Einheiten|Download free user risk detections (Kostenlose Benutzerrisikoerkennungen herunterladen)|
|Verwaltung administrativer Einheiten|Download users flagged for risk (Benutzer herunterladen, für die ein Risiko angezeigt wird)|
|Anwendungsverwaltung|Verarbeitete Batch-Einladungen|
|Anwendungsverwaltung|Batch-Einladungen hochgeladen|
|Anwendungsverwaltung|Add owner to policy (Besitzer zu Richtlinie hinzufügen)|
|Anwendungsverwaltung|Richtlinie hinzufügen|
|Anwendungsverwaltung|Löschen von Richtlinien|
|Anwendungsverwaltung|Richtlinien-Anmeldeinformationen entfernen|
|Anwendungsverwaltung|Aktualisieren von Richtlinien|
|Anwendungsverwaltung|Set MFA registration policy (MFA-Registrierungsrichtlinie festlegen)|
|Anwendungsverwaltung|Set sign-in risk policy (Richtlinie zum Anmelderisiko festlegen)|
|Anwendungsverwaltung|Set user risk policy (Richtlinie zum Benutzerrisiko festlegen)|
|Anwendungsverwaltung|Accept Terms Of Use (Nutzungsbedingungen akzeptieren)|
|Anwendungsverwaltung|Create Terms Of Use (Nutzungsbedingungen erstellen)|
|Anwendungsverwaltung|Decline Terms Of Use (Nutzungsbedingungen ablehnen)|
|Anwendungsverwaltung|Delete Terms Of Use (Nutzungsbedingungen löschen)|
|Anwendungsverwaltung|Edit Terms Of Use (Nutzungsbedingungen bearbeiten)|
|Anwendungsverwaltung|Publish Terms Of Use (Nutzungsbedingungen veröffentlichen)|
|Anwendungsverwaltung|Unpublish Terms Of Use (Veröffentlichung der Nutzungsbedingungen aufheben)|
|Anwendungsverwaltung|TLS-/SSL-Zertifikat der Anwendung hinzufügen|
|Anwendungsverwaltung|TLS-Bindung löschen|
|Anwendungsverwaltung|Register connector (Connector registrieren)|
|Anwendungsverwaltung|AdminPolicyDatas-RemoveResources|
|Anwendungsverwaltung|AdminPolicyDatas-SetResources|
|Anwendungsverwaltung|AdminUserJourneys-GetResources|
|Verzeichnisverwaltung|AdminUserJourneys-RemoveResources|
|Verzeichnisverwaltung|AdminUserJourneys-SetResources|
|Verzeichnisverwaltung|Create IdentityProvider (IdentityProvider-Element erstellen)|
|Verzeichnisverwaltung|Create a new AdminUserJourney (Neues AdminUserJourney-Element erstellen)|
|Verzeichnisverwaltung|Create localized resource json (Lokalisierten JSON-Ressourcencode erstellen)|
|Verzeichnisverwaltung|Create new Custom IDP (Neuen benutzerdefinierten IDP erstellen)|
|Verzeichnisverwaltung|Create new IDP (Neuen IDP erstellen)|
|Verzeichnisverwaltung|Create or update a B2C directory resource (B2C-Verzeichnisressourcen erstellen oder aktualisieren)|
|Verzeichnisverwaltung|Richtlinie erstellen|
|Verzeichnisverwaltung|Create trustFramework policy (Vertrauensframework-Richtlinie erstellen)|
|Verzeichnisverwaltung|Create trustFramework policy with configurable prefix (Vertrauensframework-Richtlinie mit konfigurierbarem Präfix erstellen)|
|Verzeichnisverwaltung|Create user attribute (Benutzerattribut erstellen)|
|Verzeichnisverwaltung|CreateTrustFrameworkPolicy|
|Verzeichnisverwaltung|Identitätsanbieter löschen|
|Verzeichnisverwaltung|Delete IdentityProvider (IdentityProvider-Element löschen)|
|Verzeichnisverwaltung|Delete a B2C directory resource (B2C-Verzeichnisressource löschen)|
|Verzeichnisverwaltung|Delete trustFramework policy (Vertrauensframework-Richtlinie löschen)|
|Verzeichnisverwaltung|Benutzerattribut löschen|
|Verzeichnisverwaltung|Get B2C directory resources in a resource group (B2C-Verzeichnisressourcen in einer Ressourcengruppe abrufen)|
|Verzeichnisverwaltung|Get B2C directory resources in a subscription (B2C-Verzeichnisressourcen in einem Abonnement abrufen)|
|Verzeichnisverwaltung|Get Custom IDP (Benutzerdefinierten IDP abrufen)|
|Verzeichnisverwaltung|Get IDP (IDP abrufen)|
|Verzeichnisverwaltung|B2C-Verzeichnisressource abrufen|
|Verzeichnisverwaltung|Get a user journey (User Journey abrufen)|
|Verzeichnisverwaltung|Get allowed application claims for user journey (Zulässige Anwendungsansprüche für User Journey abrufen)|
|Verzeichnisverwaltung|Get allowed self-asserted claims for user journey (Zulässige selbstbestätigte Ansprüche für User Journey abrufen)|
|Verzeichnisverwaltung|Get allowed self-asserted claims of policy (Zulässige selbstbestätigte Ansprüche der Richtlinie abrufen)|
|Verzeichnisverwaltung|Get available output claims list (Liste der verfügbaren Ausgabeansprüche abrufen)|
|Verzeichnisverwaltung|Get content definitions for user journey (Inhaltsdefinitionen für User Journey abrufen)|
|Verzeichnisverwaltung|Get idps for a specific admin flow (IDPs für einen bestimmten Verwaltungsflow abrufen)|
|Verzeichnisverwaltung|Get list of all admin flows (Liste aller Verwaltungsflows abrufen)|
|Verzeichnisverwaltung|Get list of tags for all admin flows for all users (Liste der Tags für alle Verwaltungsflows für alle Benutzer abrufen)|
|Gruppenverwaltung|Massendownload von Gruppenmitgliedern – gestartet|
|Gruppenverwaltung|Massendownload von Gruppenmitgliedern – abgeschlossen|
|Gruppenverwaltung|Massenimport von Gruppenmitgliedern – gestartet|
|Gruppenverwaltung|Massenimport von Gruppenmitgliedern – abgeschlossen|
|Gruppenverwaltung|Massenentfernen von Gruppenmitgliedern – gestartet|
|Gruppenverwaltung|Massenentfernen von Gruppenmitgliedern – abgeschlossen|
|Gruppenverwaltung|Massendownload von Gruppen – gestartet|
|Gruppenverwaltung|Massendownload von Gruppen – abgeschlossen|
|Gruppenverwaltung|Get list of tenants for a user (Liste der Mandanten für einen Benutzer abrufen)|
|Gruppenverwaltung|Get local accounts' self-asserted claims (Selbstbestätigte Ansprüche der lokalen Konten abrufen)|
|Gruppenverwaltung|Get localized resource json (Lokalisierten JSON-Ressourcencode abrufen)|
|Gruppenverwaltung|Get operations of Microsoft.AzureActiveDirectory resource provider (Vorgänge des Microsoft.AzureActiveDirectory-Ressourcenanbieters abrufen)|
|Gruppenverwaltung|Get policies (Richtlinien abrufen)|
|Gruppenverwaltung|Get policy (Richtlinie abrufen)|
|Gruppenverwaltung|Get supported IDP list (Liste der unterstützten IDPs abrufen)|
|Gruppenverwaltung|Get supported IDP list of the user journey (Liste der unterstützten IDPs der User Journey abrufen)|
|Gruppenverwaltung|Get tenant defined Custom IDP list (Vom Mandanten festgelegte Liste der benutzerdefinierten IDPs abrufen)|
|Gruppenverwaltung|Get tenant defined IDP list (Vom Mandanten festgelegte IDP-Liste)|
|Gruppenverwaltung|Get tenant defined local IDP list (Vom Mandanten festgelegte Liste der lokalen IDPs)|
|Gruppenverwaltung|Get tenant details for a user for resource creation (Mandantendetails für einen Benutzer zur Ressourcenerstellung abrufen)|
|Gruppenverwaltung|Get the default supported culture for CPIM (Standardmäßige unterstützte Kultur für CPIM abrufen)|
|Gruppenverwaltung|Get the details of an admin flow (Details eines Verwaltungsflows abrufen)|
|Gruppenverwaltung|Get the list of UserJourneys for this tenant (UserJourneys-Liste für diesen Mandanten abrufen)|
|Gruppenverwaltung|Get the set of available supported cultures for CPIM (Satz der verfügbaren unterstützten Kulturen für CPIM abrufen)|
|Gruppenverwaltung|Get trustFramework policy (Vertrauensframework-Richtlinie abrufen)|
|Gruppenverwaltung|Get trustFramework policy as xml (Vertrauensframework-Richtlinie als XML abrufen)|
|Gruppenverwaltung|Get user attribute (Benutzerattribut abrufen)|
|Richtlinienverwaltung|Get user attributes (Benutzerattribute abrufen)|
|Richtlinienverwaltung|Get user journey list (User Journey-Liste abrufen)|
|Richtlinienverwaltung|GetIEFPolicies|
|Richtlinienverwaltung|GetIdentityProviders|
|Richtlinienverwaltung|GetTrustFrameworkPolicy|
|Resource|MigrateTenantMetadata|
|Resource|Verschieben von Ressourcen|
|Resource|Patch IdentityProvider (IdentityProvider patchen)|
|Resource|PutTrustFrameworkPolicy|
|Resource|PutTrustFrameworkpolicy|
|Resource|User Journey entfernen|
|Resource|Update Custom IDP (Benutzerdefinierten IDP aktualisieren)|
|Resource|Update IDP (IDP aktualisieren)|
|Resource|Lokalen IDP aktualisieren|
|Resource|Update a B2C directory resource (B2C-Verzeichnisressource aktualisieren)|
|Resource|Aktualisieren von Richtlinien|
|Resource|Update subscription status (Abonnementstatus aktualisieren)|
|Rollenverwaltung|Update user attribute (Benutzerattribut aktualisieren)|
|Rollenverwaltung|Validate move resources (Verschiebung von Ressourcen überprüfen)|
|Rollenverwaltung|Gerät hinzufügen|
|Rollenverwaltung|Add device configuration (Gerätekonfiguration hinzufügen)|
|Rollenverwaltung|Add registered owner to device (Registrierten Besitzer zu Gerät hinzufügen)|
|Rollenverwaltung|Add registered users to device (Registrierte Benutzer zu Gerät hinzufügen)|
|Rollenverwaltung|Löschen des Geräts|
|Rollenverwaltung|Löschen der Gerätekonfiguration|
|Rollenverwaltung|Device no longer compliant (Gerät nicht mehr konform)|
|Rollenverwaltung|Device no longer managed (Gerät nicht mehr verwaltet)|
|Benutzerverwaltung|AccessReview_Review|
|Benutzerverwaltung|AccessReview_Update|
|Benutzerverwaltung|ActivationAborted|
|Benutzerverwaltung|ActivationApproved|
|Benutzerverwaltung|ActivationCanceled|
|Benutzerverwaltung|ActivationRequested|
|Benutzerverwaltung|Add eligible member to role (Berechtigtes Mitglied zur Rolle hinzufügen)|
|Benutzerverwaltung|Add member to role (Mitglied zu Rolle hinzufügen)|
|Benutzerverwaltung|Add role assignment to role definition (Rollenzuweisung zu Rollendefinition hinzufügen)|
|Benutzerverwaltung|Add role from template (Rolle aus Vorlage hinzufügen)|
|Benutzerverwaltung|Add scoped member to role (Zugeordnetes Mitglied zu Rolle hinzufügen)|
|Benutzerverwaltung|Hinzugefügt|
|Benutzerverwaltung|Zuweisen|
|Benutzerverwaltung|Massenerstellung von Benutzern – gestartet|
|Benutzerverwaltung|Massenerstellung von Benutzern – abgeschlossen|
|Benutzerverwaltung|Massenlöschung von Benutzern – gestartet|
|Benutzerverwaltung|Massenlöschung von Benutzern – abgeschlossen|
|Benutzerverwaltung|Massendownload von Benutzern – gestartet|
|Benutzerverwaltung|Massendownload von Benutzern – abgeschlossen|
|Benutzerverwaltung|Massenwiederherstellung von Benutzern – gestartet|
|Benutzerverwaltung|Massenwiederherstellung von Benutzern – abgeschlossen|
|Benutzerverwaltung|Masseneinladung von Benutzern – gestartet|
|Benutzerverwaltung|Masseneinladung von Benutzern – abgeschlossen|
|Benutzerverwaltung|Registrierten Besitzer aus Gerät entfernen|
|Benutzerverwaltung|Registrierte Benutzer aus Gerät entfernen|
|Benutzerverwaltung|Remove eligible member from role (Berechtigtes Mitglied aus Rolle entfernen)|
|Benutzerverwaltung|Remove member from role (Mitglied aus Rolle entfernen)|
|Benutzerverwaltung|Remove role assignment from role definition (Rollenzuweisung aus Rollendefinition entfernen)|
|Benutzerverwaltung|Remove scoped member from role (Zugeordnetes Mitglied aus Rolle entfernen)|
|Benutzerverwaltung|Gerät aktualisieren|
|Benutzerverwaltung|Update device configuration (Gerätekonfiguration aktualisieren)|
|Benutzerverwaltung|Rolle aktualisieren|

## <a name="entitlement-management"></a>Berechtigungsverwaltung

|Überwachungskategorie|Aktivität|
|---|---|
|Berechtigungsverwaltung|Hinzufügen der Rollenzuweisung „Berechtigungsverwaltung“|
|Berechtigungsverwaltung|Administrator weist dem Benutzer direkt das Zugriffspaket zu|       
|Berechtigungsverwaltung|Administrator entfernt die Zuweisung von Benutzerzugriffspaketen direkt|
|Berechtigungsverwaltung|Genehmigen der Zugriffspaketzuweisungsanforderung|
|Berechtigungsverwaltung|Zuweisen des Benutzers als externer Sponsor|
|Berechtigungsverwaltung|Zuweisen des Benutzers als interner Sponsor|
|Berechtigungsverwaltung|Automatisches Genehmigen der Zugriffspaketzuweisungsanforderung|
|Berechtigungsverwaltung|Abbrechen der Zugriffspaketzuweisungsanforderung|
|Berechtigungsverwaltung|Erstellen des Zugriffspakets|
|Berechtigungsverwaltung|Erstellen der Zuweisungsrichtlinie für Zugriffspakete|
|Berechtigungsverwaltung|Erstellen einer Benutzeraktualisierungsanforderung für die Zugriffspaketzuweisung|   
|Berechtigungsverwaltung|Erstellen eines Katalogs für Zugriffspakete|
|Berechtigungsverwaltung|Erstellen einer verbundenen Organisation|  
|Berechtigungsverwaltung|Erstellen einer benutzerdefinierten Aktion|
|Berechtigungsverwaltung|Erstellen einer Anforderung zum Entfernen von Ressourcen|
|Berechtigungsverwaltung|Erstellen einer Ressourcenanforderung|
|Berechtigungsverwaltung|Löschen eines Zugriffspakets|
|Berechtigungsverwaltung|Löschen der Zuweisungsrichtlinie für Zugriffspakete|
|Berechtigungsverwaltung|Löschen des Katalogs für Zugriffspakete|
|Berechtigungsverwaltung|Löschen einer verbundenen Organisation|
|Berechtigungsverwaltung|Verweigern von Zugriffspaketzuweisungsanforderung|
|Berechtigungsverwaltung|Berechtigungsverwaltung entfernt Zugriffspaketzuweisungsanforderung für Benutzer|
|Berechtigungsverwaltung|Ausführen einer benutzerdefinierten Aktion|
|Berechtigungsverwaltung|Erweitern der Zugriffspaketzuweisung|
|Berechtigungsverwaltung|Fehlerhafte Zugriffspaketzuweisungsanforderung|
|Berechtigungsverwaltung|Erfüllen der Zugriffspaketzuweisungsanforderung|
|Berechtigungsverwaltung|Erfüllen der Ressourcenzuweisung für Zugriffspakete| 
|Berechtigungsverwaltung|Teilweises Erfüllen der Zugriffspaketzuweisungsanforderung|
|Berechtigungsverwaltung|Bereit zum Erfüllen der Zugriffspaketzuweisungsanforderung|
|Berechtigungsverwaltung|Entfernen der Rollenzuweisung „Berechtigungsverwaltung“|
|Berechtigungsverwaltung|Entfernen der Ressourcenzuweisung für Zugriffspakete|
|Berechtigungsverwaltung|Entfernen des Benutzers als externer Sponsor|
|Berechtigungsverwaltung|Entfernen des Benutzers als interner Sponsor|
|Berechtigungsverwaltung|Planen einer zukünftigen Zugriffspaketzuweisung|
|Berechtigungsverwaltung|Aktualisieren des Zugriffspakets|
|Berechtigungsverwaltung|Aktualisieren der Zuweisungsrichtlinie für Zugriffspakete|
|Berechtigungsverwaltung|Aktualisieren des Katalogs für Zugriffspakete|
|Berechtigungsverwaltung|Aktualisieren der Katalogressource für Zugriffspakete|
|Berechtigungsverwaltung|Aktualisieren einer verbundenen Organisation|
|Berechtigungsverwaltung|Aktualisieren einer benutzerdefinierten Aktion|
|Berechtigungsverwaltung|Benutzeranforderung einer Zugriffspaketzuweisung|
|Berechtigungsverwaltung|Benutzeranforderung einer Zugriffspaketzuweisung im Namen des Dienstprinzipals|
|Berechtigungsverwaltung|Benutzeranforderungen zum Erweitern der Zugriffspaketzuweisung|
|Berechtigungsverwaltung|Benutzeranforderungen zum Entfernen der Zugriffspaketzuweisung|




## <a name="identity-protection"></a>Schutz der Identität (Identity Protection)

|Überwachungskategorie|Aktivität|
|---|---|
|Verzeichnisverwaltung|Erhöhen|
|Verzeichnisverwaltung|Entfernt|
|Verzeichnisverwaltung|Änderungen für Rolleneinstellung|
|Andere|ScanAlertsNow|
|Andere|Signup|
|Andere|Deautorisieren|
|Andere|UpdateAlertSettings|
|Andere|UpdateCurrentState|
|Richtlinienverwaltung|Zugriffsüberprüfung beendet|
|Richtlinienverwaltung|Genehmigende Person zu Anforderungsgenehmigung hinzufügen|
|Richtlinienverwaltung|Reviewer zu Zugriffsüberprüfung hinzufügen|
|Benutzerverwaltung|Zugriffsüberprüfung anwenden|
|Benutzerverwaltung|Zugriffsüberprüfung erstellen|


## <a name="invited-users"></a>Invited Users (Eingeladene Benutzer)

|Überwachungskategorie|Aktivität|
|---|---|
|Invited Users (Eingeladene Benutzer)|Löschen eines externen Benutzers|
|Invited Users (Eingeladene Benutzer)|Email not sent, user unsubscribed (E-Mail nicht gesendet, Abonnement des Benutzers gekündigt)|
|Invited Users (Eingeladene Benutzer)|Abonnierte E-Mail|
|Invited Users (Eingeladene Benutzer)|E-Mail-Abonnement gekündigt|
|Invited Users (Eingeladene Benutzer)|Einladungs-E-Mail|
|Invited Users (Eingeladene Benutzer)|Externen Benutzer einladen|
|Invited Users (Eingeladene Benutzer)|Einladen von externem Benutzer mit zurückgesetztem Einladungsstatus|
|Invited Users (Eingeladene Benutzer)|Einladen von internem Benutzer zur B2B-Zusammenarbeit|
|Invited Users (Eingeladene Benutzer)|Einlösen einer externen Benutzereinladung|
|Invited Users (Eingeladene Benutzer)|Erstellen eines viralen Mandanten|
|Invited Users (Eingeladene Benutzer)|Erstellen eines viralen Benutzers|


## <a name="microsoft-identity-manager-mim"></a>Microsoft Identity Manager (MIM)

|Überwachungskategorie|Aktivität|
|---|---|
|Gruppenverwaltung|Genehmigungsanforderung überprüfen|
|Gruppenverwaltung|Zugriffsüberprüfung aktualisieren|
|Gruppenverwaltung|Einstellungen für E-Mail-Benachrichtigungen für Zugriffsüberprüfung aktualisieren|
|Gruppenverwaltung|Einstellung für die Wiederholungsanzahl der Zugriffsüberprüfungen aktualisieren|
|Gruppenverwaltung|Einstellung für die Wiederholungsdauer der Zugriffsüberprüfungen in Tagen aktualisieren|
|Benutzerverwaltung|Einstellung für den Wiederholungsendtyp der Zugriffsüberprüfungen aktualisieren|
|Benutzerverwaltung|Einstellung für den Wiederholungstyp der Zugriffsüberprüfungen aktualisieren|



## <a name="privileged-identity-management"></a>Privileged Identity Management

|Überwachungskategorie|Aktivität|
|---|---|
|PIM|ActivationAborted|
|PIM|ActivationApproved|
|PIM|ActivationCanceled|
|PIM|ActivationDenied|
|PIM|ActivationRequested|
|PIM|Hinzugefügt|
|PIM|AddedOutsidePIM|
|PIM|Zuweisen|
|PIM|DismissAlert|
|PIM|Erhöhen|
|PIM|ReactivateAlert|
|PIM|Entfernt|
|PIM|AddedOutsidePIM|
|PIM|Überprüfungsbeendigung anfordern|
|PIM|Änderungen für Rolleneinstellung|
|PIM|ScanAlertsNow|
|PIM|Signup|
|PIM|Zuweisung aufheben|
|PIM|Deautorisieren|
|PIM|UpdateAlertSettings|
|PIM|UpdateCurrentState|


## <a name="self-service-group-management"></a>Self-Service-Gruppenverwaltung

|Überwachungskategorie|Aktivität|
|---|---|
|Gruppenverwaltung|Benutzerkennwort zurücksetzen|
|Gruppenverwaltung|Benutzer wiederherstellen|
|Gruppenverwaltung|Änderung des Benutzerkennworts erzwingen|
|Gruppenverwaltung|Set user manager (Benutzer-Manager festlegen)|
|Gruppenverwaltung|Set users oath token metadata enabled (OATH-Tokenmetadaten der Benutzer auf „Aktiviert“ festlegen)|
|Gruppenverwaltung|Update StsRefreshTokenValidFrom Timestamp (Zeitstempel StsRefreshTokenValidFrom aktualisieren)|
|Gruppenverwaltung|Externe geheime Schlüssel aktualisieren|
|Gruppenverwaltung|Benutzer aktualisieren|
|Gruppenverwaltung|Admin generates a temporary password (Administrator generiert ein temporäres Kennwort.)|


## <a name="self-service-password-management"></a>Self-service password management (Self-Service-Kennwortverwaltung)

|Überwachungskategorie|Aktivität|
|---|---|
|Verzeichnisverwaltung|Admins requires the user to reset their password (Administratoren fordern Benutzer zum Zurücksetzen ihres Kennworts auf.)|
|Verzeichnisverwaltung|Externen Benutzer einer Anwendung zuweisen|
|Benutzerverwaltung|Email not sent, user unsubscribed (E-Mail nicht gesendet, Abonnement des Benutzers gekündigt)|
|Benutzerverwaltung|Externen Benutzer einladen|
|Benutzerverwaltung|Einlösen einer externen Benutzereinladung|
|Benutzerverwaltung|Erstellen eines viralen Mandanten|
|Benutzerverwaltung|Erstellen eines viralen Benutzers|
|Benutzerverwaltung|User Password Registration (Benutzerkennwortregistrierung)|
|Benutzerverwaltung|User Password Reset (Benutzerkennwortzurücksetzung)|
|Benutzerverwaltung|Blocked from self-service password reset (Von Self-Service-Kennwortzurücksetzung ausgeschlossen)|


## <a name="terms-of-use"></a>Nutzungsbedingungen

|Überwachungskategorie|Aktivität|
|---|---|
|Nutzungsbedingungen|Accept Terms Of Use (Nutzungsbedingungen akzeptieren)|
|Nutzungsbedingungen|Create Terms Of Use (Nutzungsbedingungen erstellen)|
|Nutzungsbedingungen|Decline Terms Of Use (Nutzungsbedingungen ablehnen)|
|Nutzungsbedingungen|Zustimmung löschen|
|Nutzungsbedingungen|Delete Terms Of Use (Nutzungsbedingungen löschen)|
|Nutzungsbedingungen|Edit Terms Of Use (Nutzungsbedingungen bearbeiten)|
|Nutzungsbedingungen|Ablauf von Nutzungsbedingungen|
|Nutzungsbedingungen|Nutzungsbedingungen endgültig löschen|
|Nutzungsbedingungen|Publish Terms Of Use (Nutzungsbedingungen veröffentlichen)|
|Nutzungsbedingungen|Unpublish Terms Of Use (Veröffentlichung der Nutzungsbedingungen aufheben)|


## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über Azure AD-Berichte](overview-reports.md)
- [Bericht „Überwachungsprotokolle“](concept-audit-logs.md) 
- [Programmgesteuerter Zugriff auf Azure AD-Berichte](concept-reporting-api.md)
