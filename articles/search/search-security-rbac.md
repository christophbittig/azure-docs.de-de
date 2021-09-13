---
title: Rollenbasierte Autorisierung
titleSuffix: Azure Cognitive Search
description: Verwenden Sie die rollenbasierte Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC) zum Einrichten differenzierter Berechtigungen für Dienstverwaltungs- und Inhaltsaufgaben.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/23/2021
ms.openlocfilehash: 6ffad57e87b61b9198102ddf8ae4ec8f1a9002ac
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122356229"
---
# <a name="use-role-based-authorization-in-azure-cognitive-search"></a>Verwenden der rollenbasierten Autorisierung in Azure Cognitive Search

Azure bietet ein [Autorisierungssystem mit rollenbasierter Zugriffssteuerung](../role-based-access-control/role-assignments-portal.md) (Role-Based Access Control, RBAC) für alle Dienste, die auf der Plattform ausgeführt werden. In Cognitive Search können Sie die rollenbasierte Autorisierung folgendermaßen verwenden:

+ Über die Rollen „Besitzer“, „Mitwirkender“ und „Leser“ können Sie im Dienst selbst den Zugriff auf Vorgänge auf Steuerungsebene zulassen, z. B. zum Hinzufügen von Kapazität oder Rotieren von Schlüsseln.

+ Sie können den Zugriff auf Vorgänge auf Datenebene zulassen, z. B. zum Erstellen oder Abfragen von Indizes. Diese Funktion ist derzeit ([auf Nachfrage](https://aka.ms/azure-cognitive-search/rbac-preview)) in der öffentlichen Vorschau verfügbar. Nach dem Onboarding Ihres Abonnements befolgen Sie die Anweisung in diesem Artikel, um das Feature zu verwenden.

+ Sie können zulassen, dass ausgehende Indizierungsverbindungen [mithilfe einer verwalteten Identität](search-howto-managed-identities-data-sources.md) hergestellt werden. Wenn einem Suchdienst eine verwaltete Identität zugewiesen ist, können Sie Rollenzuweisungen erstellen, die externe Datendienste wie z. B. Azure Blob Storage erweitern, um Lesezugriff auf Blobs durch Ihren vertrauenswürdigen Suchdienst zuzulassen.

Dieser Artikel konzentriert sich auf die ersten beiden Punkte: Rollen für die Steuerungsebene und Vorgänge auf Datenebene. Wenn Sie weitere Informationen zu ausgehenden Indizierungsaufrufen wünschen, beginnen Sie mit [Konfigurieren einer verwalteten Identität](search-howto-managed-identities-data-sources.md).

Einige RBAC-Szenarien werden **nicht** direkt unterstützt. Dazu gehören die folgenden:

+ [Benutzerdefinierte Rollen](../role-based-access-control/custom-roles.md)

+ Zugriff auf Benutzeridentitäten über Suchergebnisse (zuweilen als Sicherheit auf Zeilenebene oder Sicherheit auf Dokumentebene bezeichnet)

  > [!Tip]
  > Bei der Sicherheit auf Dokumentebene besteht ein Workaround darin, [Sicherheitsfilter](search-security-trimming-for-azure-search.md) zu verwenden, um die Ergebnisse basierend auf der Benutzeridentität einzuschränken, sodass Dokumente entfernt werden, auf die der Anfragende keinen Zugriff haben sollte.
  >

## <a name="roles-used-in-search"></a>Bei der Suche verwendete Rollen

Zu den integrierten Rollen gehören allgemein verfügbare und vorschauberechtigte Rollen, denen Azure Active Directory-Benutzer und -Gruppen als Mitglieder zugewiesen sind.

Rollenzuweisungen sind kumulativ und gelten für alle Tools und Clientbibliotheken, die zum Erstellen oder Verwalten eines Suchdiensts verwendet werden. Zu den Clients gehören das Azure-Portal, die Verwaltungs-REST-API, Azure PowerShell, die Azure CLI und die Verwaltungsclientbibliothek der Azure SDKs. 

Es gibt keine regions-, ebenen- oder preisbezogenen Einschränkungen für die Nutzung von RBAC in Azure Cognitive Search, aber Ihr Dienst muss sich in der öffentlichen Azure-Cloud befinden.

| Role | Status | Gilt für: | BESCHREIBUNG |
| ---- | -------| ---------- | ----------- |
| [Besitzer](../role-based-access-control/built-in-roles.md#owner) | Stable | Steuerungsebene | Vollzugriff auf die Ressource, einschließlich der Fähigkeit zum Zuweisen von Azure-Rollen. Abonnementadministratoren sind standardmäßig Mitglieder. |
| [Mitwirkender](../role-based-access-control/built-in-roles.md#contributor) | Stable | Steuerungsebene | Dieselbe Zugriffsebene wie ein Besitzer, ohne die Fähigkeit zum Zuweisen von Rollen. |
| [Leser](../role-based-access-control/built-in-roles.md#reader) | Stable | Steuerungsebene | Eingeschränkter Zugriff auf partielle Dienstinformationen. Im Portal kann die Rolle „Leser“ auf der Seite „Übersicht“ des Diensts im Abschnitt „Zusammenfassung“ und auf der Registerkarte „Monitor“ auf Informationen zugreifen. Der Zugriff auf alle anderen Registerkarten und Seiten ist nicht möglich. </br></br>Diese Rolle hat Zugriff auf Dienstinformationen: Ressourcengruppe, Dienststatus, Abonnementname und -ID, Tags, URL, Tarif, Replikate, Partitionen und Sucheinheiten. </br></br>Diese Rolle hat auch Zugriff auf Dienstmetriken: Suchlatenz, Prozentsatz der gedrosselten Anforderungen, durchschnittliche Abfragen pro Sekunde. </br></br>Sie hat keinen Zugriff auf API-Schlüssel, Rollenzuweisungen, Inhalte (Indizes oder Synonymzuordnungen) oder Inhaltsmetriken (verbrauchter Speicher, Anzahl von Objekten). |
| [Mitwirkender von Suchdienst](../role-based-access-control/built-in-roles.md#search-service-contributor) | Vorschau | Steuerungsebene | Bietet Vollzugriff auf Suchdienst und Objektdefinitionen, aber keinen Zugriff auf indizierte Daten. Diese Rolle ist für Dienstadministratoren gedacht, die mehr Informationen benötigen, als die Rolle „Leser“ bietet, aber keinen Zugriff auf Index- oder Synonymzuordnungsinhalte haben sollten.|
| [Mitwirkender an Suchindexdaten](../role-based-access-control/built-in-roles.md#search-index-data-contributor) | Vorschau | Datenebene | Bietet Vollzugriff auf Indexdaten, aber auch sonst nichts. Diese Rolle ist für Entwickler oder Indexbesitzer geeignet, die für das Erstellen und Laden von Inhalten zuständig sind, aber keinen Zugriff auf Suchdienstinformationen haben sollten. Der Geltungsbereich umfasst alle Ressourcen des Suchdiensts auf oberster Ebene (Indizes, Synonymzuordnungen, Indizierungsfunktionen, Datenquellen, Skillsets). |
| [Suchindexdatenleser](../role-based-access-control/built-in-roles.md#search-index-data-reader) | Vorschau | Datenebene | Bietet schreibgeschützten Zugriff auf Indexdaten. Diese Rolle ist für Benutzer gedacht, die Abfragen in einem Index ausführen. Der Geltungsbereich umfasst alle Ressourcen des Suchdiensts auf oberster Ebene (Indizes, Synonymzuordnungen, Indizierungsfunktionen, Datenquellen, Skillsets). |

## <a name="scope-control-plane-and-data-plane"></a>Geltungsbereich: Steuerungsebene und Datenebene

Bei Azure-Ressourcen gibt es für Vorgänge die Kategorien [Steuerungsebene und Datenebene](../azure-resource-manager/management/control-plane-and-data-plane.md). Die integrierten Rollen für Cognitive Search gelten entweder für die eine oder für die andere Ebene.

| Category | Operationen (Operations) |
|----------|------------|
| Steuerungsebene | Folgende Vorgänge können ausgeführt werden: Erstellen, Aktualisieren und Löschen von Diensten, Verwalten von API-Schlüsseln, Anpassen von Partitionen und Replikaten usw. Die [Verwaltungs-REST-API](/rest/api/searchmanagement/) und gleichwertige Clientbibliotheken definieren die Vorgänge, die auf Steuerungsebene gelten. |
| Datenebene | Vorgänge im Suchdienstendpunkt – dies umfasst sämtliche im Dienst gehosteten Objekte und Daten. Indizierung, Abfrage und alle zugehörigen Aktionen zielen auf die Datenebene ab, auf die über die [Such-REST-API](/rest/api/searchservice/) und gleichwertige Clientbibliotheken zugegriffen wird. </br></br>Derzeit können Sie Rollenzuweisungen nicht verwenden, um den Zugriff auf einzelne Indizes, Synonymzuordnungen, Indizierungsfunktionen, Datenquellen oder Skillsets zu beschränken. |

## <a name="configure-search-for-data-plane-authentication"></a>Konfigurieren der Suche für die Authentifizierung auf Datenebene

Wenn Sie eine der in der Vorschau befindlichen Rollen auf Datenebene (Mitwirkender an Suchindexdaten und Suchindexdatenleser) und die Azure AD-Authentifizierung verwenden, muss Ihr Suchdienst so konfiguriert sein, dass er einen **Autorisierungsheader** in Datenanforderungen erkennt, der ein OAuth2-Zugriffstoken bereitstellt. In diesem Abschnitt wird erläutert, wie Sie Ihren Suchdienst konfigurieren. Wenn Sie Rollen auf Steuerungsebene (Besitzer, Mitwirkender, Leser) verwenden, können Sie diesen Schritt überspringen.

Bevor Sie beginnen, [registrieren](https://aka.ms/azure-cognitive-search/rbac-preview) Sie sich für die RBAC-Vorschau. Ihr Abonnement muss bei dem Programm registriert sein, bevor Sie dieses Feature verwenden können. Die Verarbeitung von Registrierungsanfragen kann bis zu zwei Werktage in Anspruch nehmen. Sie erhalten eine E-Mail, wenn Ihr Dienst einsatzbereit ist.

### <a name="azure-portal"></a>[**Azure-Portal**](#tab/config-svc-portal)

1. Öffnen Sie das Portal mit dieser Syntax: [https://ms.portal.azure.com/?feature.enableRbac=true](https://ms.portal.azure.com/?feature.enableRbac=true).

1. Navigieren Sie zu Ihrem Suchdienst.

1. Wählen Sie im linken Navigationsbereich **Schlüssel** aus.

1. Wählen Sie einen Mechanismus für die **API-Zugriffssteuerung**. Wenn diese Optionen nicht angezeigt werden, überprüfen Sie die Portal-URL. Wenn Sie Ihre Auswahl nicht speichern können, gibt es ein Problem mit der Registrierung Ihres Abonnements. 

   | Option | Status | BESCHREIBUNG |
   |--------|--------|-------------|
   | API-Schlüssel | Allgemein verfügbar (Standardwert) | Erfordert zur Autorisierung einen [Administrator- oder Abfrage-API-Schlüssel](search-security-api-keys.md) im Anforderungsheader. Es werden keine Rollen verwendet. |
   | Rollenbasierte Zugriffssteuerung | Vorschau | Zur Ausführung der Aufgabe ist die Mitgliedschaft in einer Rollenzuweisung erforderlich, wie im nächsten Schritt beschrieben. Außerdem ist ein Autorisierungsheader erforderlich. Die Auswahl dieser Option schränkt Sie auf Clients ein, die die REST-API „2021-04-30-preview“ verwenden. |
   | Beide | Vorschau | Anforderungen sind entweder mit einem API-Schlüssel oder einem Autorisierungstoken gültig. |

Sobald Ihr Suchdienst RBAC-fähig ist, erfordert das Portal das Featureflag in der URL, um Rollen zuzuweisen und Inhalte anzuzeigen. **Inhalte wie Indizes und Indizierungsfunktionen sind im Portal nur sichtbar, wenn Sie es mit dem entsprechenden Featureflag öffnen.** Wenn Sie zu einem späteren Zeitpunkt das Standardverhalten wiederherstellen möchten, legen Sie die Auswahl der API-Schlüssel wieder auf **API-Schlüssel** fest.

### <a name="rest-api"></a>[**REST-API**](#tab/config-svc-rest)

Verwenden Sie die Verwaltungs-REST-API in Version 2021-04-01-Preview, um Ihren Dienst zu konfigurieren.

1. Rufen Sie [Dienst erstellen oder aktualisieren](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update) auf.

1. Legen Sie [DataPlaneAuthOptions](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#dataplaneauthoptions) auf `aadOrApiKey` fest. Sehen Sie sich die Syntax in [diesem Beispiel](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#searchcreateorupdateserviceauthoptions) an.

1. Legen Sie [AadAuthFailureMode](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#aadauthfailuremode) fest, um anzugeben, ob bei Authentifizierungsfehlern 401- oder 403-Fehler zurückgegeben werden.

---

## <a name="assign-roles"></a>Zuweisen von Rollen

Rollen können mit einer der in der Dokumentation zur rollenbasierten Zugriffssteuerung in Azure beschriebenen [unterstützten Methode](../role-based-access-control/role-assignments-steps.md) zugewiesen werden.

Sie müssen Besitzer sein oder über die Berechtigung [Microsoft.Authorization/roleAssignments/write](/azure/templates/microsoft.authorization/roleassignments) verfügen, um Rollenzuweisungen zu verwalten.

### <a name="azure-portal"></a>[**Azure-Portal**](#tab/rbac-portal)

Legen Sie das Featureflag in der Portal-URL so fest, dass sie für die Vorschaurollen funktioniert: Suchdienstmitwirkender, Mitwirkender an Suchindexdaten und Suchindexdatenleser.

1. Öffnen Sie das Portal mit dieser Syntax: [https://ms.portal.azure.com/?feature.enableRbac=true](https://ms.portal.azure.com/?feature.enableRbac=true). Die URL sollte `feature.enableRbac=true` enthalten.

1. Navigieren Sie zu Ihrem Suchdienst.

1. Wählen Sie im linken Navigationsbereich **Access Control (IAM)** aus.

1. Wählen Sie auf der rechten Seite unter **Zugriff auf diese Ressource gewähren** die Option **Rollenzuweisung hinzufügen** aus.

1. Suchen Sie eine geeignete Rolle (Besitzer, Mitwirkender, Leser, Suchdienstmitwirkender, Mitwirkender an Suchindexdaten, Suchindexdatenleser), und weisen Sie dieser eine Azure Active Directory-Benutzer- oder -Gruppenidentität zu.

### <a name="powershell"></a>[**PowerShell**](#tab/rbac-powershell)

Wenn Sie [PowerShell zum Zuweisen von Rollen verwenden](../role-based-access-control/role-assignments-powershell.md), rufen Sie [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) auf, und geben Sie den Namen des Azure-Benutzers oder der Azure-Gruppe sowie den Geltungsbereich der Zuweisung an.

Stellen Sie vor Beginn sicher, dass Sie die Azure- und Azure AD-Module geladen und eine Verbindung mit Azure hergestellt haben:

```powershell
Import-Module -Name Az
Import-Module -Name AzureAD
Connect-AzAccount
```

Wenn Sie das Feature auf einen Dienst beschränken möchten, sollte Ihre Syntax so ähnlich wie das folgende Beispiel aussehen:

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Search Index Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Search/searchServices/<search-service>"
```

Bei Beschränkung auf einen einzelnen Index sieht die Syntax in etwa so aus:

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Search Index Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Search/searchServices/<search-service>/indexes/<index-name>"
```

Denken Sie daran, dass Sie den Zugriff nur auf Ressourcen der obersten Ebene beschränken können, wie z. B. Indizes, Synonymzuordnungen, Indizierungsfunktionen, Datenquellen und Skillsets. Mit Azure-Rollen können Sie den Zugriff auf Suchdokumente (Indexinhalte) nicht beschränken.

---

## <a name="configure-requests-and-test"></a>Konfigurieren von Anforderungen und Testen

Zum Durchführen programmgesteuerter Tests überprüfen Sie, ob Ihr Code eine Such-REST-API (beliebige unterstützte Version) verwendet, und legen Sie den Autorisierungsheader auf „requests“ fest. Wenn Sie eins der Azure-SDKs verwenden, überprüfen Sie die Betaversionen, um zu ermitteln, ob der Autorisierungsheader verfügbar ist. 

Je nach Anwendung ist möglicherweise eine zusätzliche Konfiguration erforderlich, um eine Anwendung bei Azure Active Directory zu registrieren oder um Autorisierungstoken abzurufen und zu übergeben.

Alternativ dazu können Sie zum Testen das Azure-Portal verwenden und die Rollen sich selbst zuweisen:

1. Öffnen Sie das Portal mit dieser Syntax: [https://ms.portal.azure.com/?feature.enableRbac=true](https://ms.portal.azure.com/?feature.enableRbac=true). 

   Auch wenn Sie RBAC in einem vorherigen Schritt für Ihren Dienst aktiviert haben, erfordert das Portal das Featureflag, um RBAC-Verhaltensweisen aufrufen zu können. **Inhalte wie Indizes und Indizierungsfunktionen sind im Portal nur sichtbar, wenn Sie es mit dem entsprechenden Featureflag öffnen.** Wenn Sie das Standardverhalten wiederherstellen möchten, legen Sie die Auswahl der API-Schlüssel wieder auf **API-Schlüssel** fest.

1. Navigieren Sie zu Ihrem Suchdienst.

1. Wählen Sie auf der Seite „Übersicht“ die Registerkarte **Indizes** aus:

   + Im Fall der Mitgliedschaft in der Rolle „Suchindexdatenleser“ verwenden Sie den Such-Explorer, um den Index abzufragen. Sie können jede API-Version verwenden, um den Zugriff zu überprüfen.

   + Im Fall der Rolle „Mitwirkender an Suchindexdaten“ wählen Sie **Neuer Index** aus, um einen neuen Index zu erstellen. Durch Speichern eines neuen Index wird der Schreibzugriff auf den Dienst überprüft.

## <a name="disable-api-key-authentication"></a>Deaktivieren der API-Schlüssel-Authentifizierung

API-Schlüssel können nicht gelöscht werden, aber Sie können sie in Ihrem Dienst deaktivieren. Wenn Sie die Rollen „Mitwirkender an Suchindexdaten“ und „Suchindexdatenleser“ sowie die Azure AD-Authentifizierung verwenden, können Sie API-Schlüssel deaktivieren. Dadurch lehnt der Suchdienst alle datenbezogenen Anforderungen ab, bei denen ein Schlüssel übergeben wird.

Verwenden Sie für diese Aufgabe die folgende Vorschauversion der Verwaltungs-REST-API: 2021-04-01-preview.

1. Legen Sie [DataPlaneAuthOptions](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#dataplaneauthoptions) auf `aadOrApiKey` fest.

1. [Weisen Sie Rollen zu](#assign-roles), und überprüfen Sie, ob sie ordnungsgemäß funktionieren.

1. Legen Sie `disableLocalAuth` auf **true** fest.

Wenn Sie den letzten Schritt rückgängig machen und dann `disableLocalAuth` auf **false** festlegen, akzeptiert der Suchdienst API-Schlüssel in Anforderungen automatisch wieder (vorausgesetzt, diese Schlüssel sind angegeben).