---
title: Rollenbasierte Autorisierung
titleSuffix: Azure Cognitive Search
description: Verwenden Sie die rollenbasierte Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC) zum Einrichten differenzierter Berechtigungen für Dienstverwaltungs- und Inhaltsaufgaben.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/04/2021
ms.openlocfilehash: b26334c655332810ad1f67ae6799c3919fda4bb4
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517811"
---
# <a name="use-role-based-authorization-in-azure-cognitive-search"></a>Verwenden der rollenbasierten Autorisierung in Azure Cognitive Search

Azure bietet ein [Autorisierungssystem mit rollenbasierter Zugriffssteuerung](../role-based-access-control/role-assignments-portal.md) (Role-Based Access Control, RBAC) für alle Dienste, die auf der Plattform ausgeführt werden. In Cognitive Search können Sie Rollen folgendermaßen verwenden:

+ Verwenden Sie für die Dienstverwaltung allgemein verfügbare Rollen.

+ Verwenden Sie für die Inhaltsverwaltung (Erstellen und Verwalten von Indizes und anderen Objekten der obersten Ebene) neue Vorschaurollen, [**die unter Vorschau verfügbar sind**](#step-1-preview-sign-up).

> [!NOTE]
> „Suchdienstmitwirkender“ ist eine allgemein verfügbare Rolle mit Vorschaumöglichkeiten. Es ist die einzige Rolle, die eine echte Hybridlösung für Dienst- und Inhaltsverwaltungsaufgaben unterstützt und alle Vorgänge für einen bestimmten Suchdienst zulässt. Um in den Genuss der Vorschaufunktionen bei der Inhaltsverwaltung für diese Rolle zu kommen, [**registrieren Sie sich für die Vorschauversion**](#step-1-preview-sign-up).

Einige RBAC-Szenarien werden **nicht** unterstützt bzw. nicht in diesem Artikel behandelt:

+ Ausgehende Indexerverbindungen sind unter [Einrichten einer Indexerverbindung mit einer Datenquelle mithilfe einer verwalteten Identität](search-howto-managed-identities-data-sources.md) dokumentiert. Wenn einem Suchdienst eine verwaltete Identität zugewiesen ist, können Sie Rollenzuweisungen erstellen, die externen Datendiensten wie z. B. Azure Blob Storage Lesezugriff auf Blobs durch Ihren vertrauenswürdigen Suchdienst erlauben.

+ Zugriff auf Benutzeridentitäten über Suchergebnisse (zuweilen als Sicherheit auf Zeilen- oder Dokumentebene bezeichnet) wird nicht unterstützt. Bei der Sicherheit auf Dokumentebene besteht ein Workaround darin, [Sicherheitsfilter](search-security-trimming-for-azure-search.md) zu verwenden, um die Ergebnisse basierend auf der Benutzeridentität einzuschränken, sodass Dokumente entfernt werden, auf die der Anfragende keinen Zugriff haben sollte.

## <a name="built-in-roles-used-in-search"></a>Bei der Suche verwendete integrierte Rollen

Zu den integrierten Rollen in Cognitive Search gehören allgemein verfügbare und Vorschaurollen, denen Azure Active Directory-Benutzer und -Gruppen als Mitglieder zugewiesen sind.

Rollenzuweisungen sind kumulativ und gelten für alle Tools und Clientbibliotheken, die zum Erstellen oder Verwalten eines Suchdiensts verwendet werden. Zu diesen Clients gehören das Azure-Portal, die Verwaltungs-REST-API, Azure PowerShell, die Azure CLI und die Verwaltungsclientbibliothek der Azure SDKs.

Rollen gelten für den Suchdienst als Ganzes und müssen von einem Besitzer zugewiesen werden. Sie können bestimmten Indizes oder anderen Objekten der obersten Ebene keine Rollen zuweisen.

Es gibt keine regions-, ebenen- oder preisbezogenen Einschränkungen für die Nutzung von RBAC in Azure Cognitive Search, aber Ihr Dienst muss sich in der öffentlichen Azure-Cloud befinden.

| Role | Gilt für: | BESCHREIBUNG |
| ---- | ---------- | ----------- |
| [Besitzer](../role-based-access-control/built-in-roles.md#owner) | Dienstbetrieb (allgemein verfügbar) | Vollzugriff auf die Suchressource, einschließlich Fähigkeit zum Zuweisen von Azure-Rollen. Abonnementadministratoren sind standardmäßig Mitglieder. |
| [Mitwirkender](../role-based-access-control/built-in-roles.md#contributor) | Dienstbetrieb (allgemein verfügbar) | Gleiche Zugriffsebene wie Besitzer, jedoch ohne die Fähigkeit, Rollen zuzuweisen oder Autorisierungsoptionen zu ändern. |
| [Leser](../role-based-access-control/built-in-roles.md#reader) | Dienstbetrieb (allgemein verfügbar) | Eingeschränkter Zugriff auf partielle Dienstinformationen. Im Portal kann die Rolle „Leser“ auf der Seite „Übersicht“ des Diensts im Abschnitt „Zusammenfassung“ und auf der Registerkarte „Monitor“ auf Informationen zugreifen. Der Zugriff auf alle anderen Registerkarten und Seiten ist nicht möglich. </br></br>Diese Rolle hat Zugriff auf Dienstinformationen: Ressourcengruppe, Dienststatus, Abonnementname und -ID, Tags, URL, Tarif, Replikate, Partitionen und Sucheinheiten. </br></br>Diese Rolle hat auch Zugriff auf Dienstmetriken: Suchlatenz, Prozentsatz der gedrosselten Anforderungen, durchschnittliche Abfragen pro Sekunde. </br></br>Sie hat keinen Zugriff auf API-Schlüssel, Rollenzuweisungen, Inhalte (Indizes oder Synonymzuordnungen) oder Inhaltsmetriken (verbrauchter Speicher, Anzahl von Objekten). |
| [Mitwirkender von Suchdienst](../role-based-access-control/built-in-roles.md#search-service-contributor) | Dienstbetrieb (allgemein verfügbar) und Objekte der obersten Ebene (Vorschau) | Diese Rolle ist eine Kombination aus den Berechtigungen für „Mitwirkender“ auf Dienstebene und dem Vollzugriff auf alle Aktionen für Indizes, Synonymzuordnungen, Indexer, Datenquellen und Skillsets über [`Microsoft.Search/searchServices/*`](../role-based-access-control/resource-provider-operations.md#microsoftsearch). Diese Rolle ist für Suchdienstadministratoren vorgesehen, die die Möglichkeit haben müssen, den Dienst vollständig zu verwalten. </br></br>Ebenso wie Mitwirkende können Mitglieder dieser Rolle keine Rollenzuweisungen vornehmen oder verwalten oder Autorisierungsoptionen ändern. |
| [Mitwirkender an Suchindexdaten](../role-based-access-control/built-in-roles.md#search-index-data-contributor) | Dokumentensammlung (Vorschau) | Bietet Vollzugriff auf Inhalte in allen Indizes des Suchdiensts. Diese Rolle ist für Entwickler oder Indexbesitzer gedacht, die die Dokumentsammlung eines Index importieren, aktualisieren oder abfragen müssen. |
| [Suchindexdatenleser](../role-based-access-control/built-in-roles.md#search-index-data-reader) | Dokumentsammlung (Vorschau) | Bietet schreibgeschützten Zugriff auf Suchindizes im Suchdienst. Diese Rolle ist für Apps und Benutzer gedacht, die Abfragen ausführen. |

> [!NOTE]
> Bei Azure-Ressourcen gibt es für Vorgänge die Kategorien [Steuerungsebene und Datenebene](../azure-resource-manager/management/control-plane-and-data-plane.md). In Cognitive Search bezieht sich „Steuerungsebene“ auf alle Vorgänge, die von der [Verwaltungs-REST-API](/rest/api/searchmanagement/) oder entsprechenden Clientbibliotheken unterstützt werden. Die „Datenebene“ bezieht sich auf Vorgänge für den Suchdienstendpunkt, z. B. Indizierung oder Abfragen, oder auf andere Vorgänge, die in der [REST-API für die Suche](/rest/api/searchservice/) oder entsprechenden Clientbibliotheken angegeben sind. Die meisten Rollen gelten bloß auf einer Ebene. Ausnahme ist die Rolle „Suchdienstmitwirkender“, die Aktionen auf beiden Ebenen unterstützt.

## <a name="step-1-preview-sign-up"></a>Schritt 1: Vorschau der Registrierung

**Gilt für**: Mitwirkender an Suchindexdaten, Suchindexdatenleser, Suchdienstmitwirkender

Überspringen Sie diesen Schritt, wenn Sie allgemein verfügbare Rollen (Besitzer, Mitwirkender, Leser) oder nur die Aktionen auf Dienstebene von „Suchdienstmitwirkender“ verwenden.

Neue integrierte Vorschaurollen bieten einen präzisen Satz von Berechtigungen für Inhalte im Suchdienst. Obwohl integrierte Rollen stets im Azure-Portal sichtbar sind, ist eine Dienstregistrierung erforderlich, um sie betriebsbereit zu machen.

So fügen Sie Ihr Abonnement zu der Vorschau hinzu:

1. Navigieren Sie zu Ihrem Suchdienst im [Azure-Portal](https://portal.azure.com/).
1. Wählen Sie auf der linken Seite die Option **Schlüssel** aus.
1. Wählen Sie im blauen Banner, in dem die Vorschau erwähnt wird, **Registrieren** aus, um das Feature Ihrem Abonnement hinzuzufügen.

![Screenshot: Registrieren für die RBAC-Vorschau im Portal](media/search-howto-aad/rbac-signup-portal.png)

Sie können sich auch für die Vorschau registrieren, indem Sie Azure Feature Exposure Control (AFEC) verwenden und nach *Rollenbasierte Zugriffssteuerung für Suchdienst (Vorschau)* suchen. Weitere Informationen zum Hinzufügen von Vorschaufeatures finden Sie unter [Einrichten von Vorschaufeatures im Azure-Abonnement](../azure-resource-manager/management/preview-features.md?tabs=azure-portal).

> [!NOTE]
> Nachdem Sie Die Vorschau zu Ihrem Abonnement hinzugefügt haben, werden alle Dienste im Abonnement dauerhaft in der Vorschau registriert. Wenn Sie die rollenbasierte Zugriffssteuerung (RBAC) für einen bestimmten Dienst nicht verwenden möchten, können Sie RBAC für Vorgänge auf Datenebene deaktivieren, wie im nächsten Schritt gezeigt.

## <a name="step-2-preview-configuration"></a>Schritt 2: Vorschau auf die Konfiguration

**Gilt für**: Mitwirkender an Suchindexdaten, Suchindexdatenleser, Suchdienstmitwirkender

Überspringen Sie diesen Schritt, wenn Sie allgemein verfügbare Rollen (Besitzer, Mitwirkender, Leser) oder nur die Aktionen auf Dienstebene von „Suchdienstmitwirkender“ verwenden.

In diesem Schritt konfigurieren Sie Ihren Dienst so, dass er einen **Autorisierungsheader** bei Datenanforderungen erkennt, die ein OAuth2-Token für den Zugriff enthalten.

### <a name="azure-portal"></a>[**Azure-Portal**](#tab/config-svc-portal)

1. Öffnen Sie das [Azure-Portal](https://ms.portal.azure.com).

1. Navigieren Sie zu Ihrem Suchdienst.

1. Wählen Sie im linken Navigationsbereich **Schlüssel** aus.

1. Wählen Sie einen Mechanismus für die **API-Zugriffssteuerung**. 

   | Option | Status | BESCHREIBUNG |
   |--------|--------|-------------|
   | API-Schlüssel | Allgemein verfügbar (Standardwert) | Erfordert zur Autorisierung einen [Administrator- oder Abfrage-API-Schlüssel](search-security-api-keys.md) im Anforderungsheader. Es werden keine Rollen verwendet. |
   | Rollenbasierte Zugriffssteuerung | Vorschau | Zur Ausführung der Aufgabe ist die Mitgliedschaft in einer Rollenzuweisung erforderlich, wie im nächsten Schritt beschrieben. Außerdem ist ein Autorisierungsheader erforderlich. Die Auswahl dieser Option schränkt Sie auf Clients ein, die die REST-API „2021-04-30-preview“ verwenden. |
   | Beide | Vorschau | Anforderungen sind entweder mit einem API-Schlüssel oder einem Autorisierungstoken gültig. |

Wenn diese Optionen nicht angezeigt werden, prüfen Sie die Portal-URL.

Wenn Sie Ihre Auswahl nicht speichern können oder diese Fehlermeldung erhalten: „API-Zugriffssteuerung konnte den Suchdienst `<name>` nicht aktualisieren. DisableLocalAuth ist in der Vorschauphase und nicht für dieses Abonnement aktiviert“, wurde Ihre Abonnementregistrierung nicht eingeleitet oder verarbeitet.

### <a name="rest-api"></a>[**REST-API**](#tab/config-svc-rest)

Verwenden Sie die Verwaltungs-REST-API-Version „2021-04-01-Preview“, [Erstellen oder Aktualisieren des Dienst](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update), um Ihren Dienst zu konfigurieren.

Wenn Sie Postman oder ein anderes Webtesttool verwenden, finden Sie im folgenden Tipp Hilfe zum Einrichten der Anforderung.

1. Legen Sie [AuthOptions](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#dataplaneauthoptions) auf aadOrApiKey fest.

   Legen Sie optional [AadAuthFailureMode](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#aadauthfailuremode) fest, um anzugeben, ob 401 anstelle von 403 zurückgegeben wird, wenn die Authentifizierung fehlschlägt. Der Standardwert von disableLocalAuth ist FALSE, sodass Sie ihn nicht festlegen müssen. Im Folgenden wird jedoch hervorgehoben, dass er immer dann FALSE sein muss, wenn authOptions festgelegt ist.

    ```http
    PUT https://management.azure.com/subscriptions/{{subscriptionId}}/resourcegroups/{{resource-group}}/providers/Microsoft.Search/searchServices/{{search-service-name}}?api-version=2021-04-01-Preview
    {
      "location": "{{region}}",
      "sku": {
        "name": "standard"
      },
      "properties": {
        "disableLocalAuth": false,
        "authOptions": {
          "aadOrApiKey": {
            "aadAuthFailureMode": "http401WithBearerChallenge"
          }
        }
      }
   }
    ```

1. [Weisen Sie dem Dienst Rollen zu](#assign-roles), und überprüfen Sie, ob sie auf Datenebene korrekt funktionieren.

> [!TIP]
> Aufrufe der Verwaltungs-REST-API werden von Azure Active Directory authentifiziert. Anleitungen zum Einrichten eines Sicherheitsprinzipals und einer Anforderung finden Sie in diesem Blogbeitrag zu [Azure-REST-APIs mit Postman (2021)](https://blog.jongallant.com/2021/02/azure-rest-apis-postman-2021/). Das vorherige Beispiel wurde anhand der Anweisungen und der Postman-Sammlung im Blogbeitrag getestet.

---

<a name="assign-roles"></a>

## <a name="step-3-assign-roles"></a>Schritt 3: Zuweisen von Rollen

Rollen können mit einer der in der Dokumentation zur rollenbasierten Zugriffssteuerung in Azure beschriebenen [unterstützten Methode](../role-based-access-control/role-assignments-steps.md) zugewiesen werden.

Sie müssen **Besitzer** sein oder über die Berechtigung [Microsoft.Authorization/roleAssignments/write](/azure/templates/microsoft.authorization/roleassignments) verfügen, um Rollenzuweisungen zu verwalten.

### <a name="azure-portal"></a>[**Azure-Portal**](#tab/roles-portal)

1. Öffnen Sie das [Azure-Portal](https://ms.portal.azure.com).

1. Navigieren Sie zu Ihrem Suchdienst.

1. Wählen Sie im linken Navigationsbereich **Access Control (IAM)** aus.

1. Wählen Sie auf der rechten Seite unter **Zugriff auf diese Ressource gewähren** die Option **Rollenzuweisung hinzufügen** aus.

1. Suchen Sie eine passende Rolle, und weisen Sie dann eine Azure Active Directory-Benutzer- oder Gruppenidentität zu:

   + Besitzer
   + Mitwirkender
   + Leser
   + Mitwirkender von Suchdienst
   + Mitwirkender an Suchindexdaten (Vorschau)
   + Suchindexdatenleser (Vorschau)

### <a name="powershell"></a>[**PowerShell**](#tab/roles-powershell)

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

## <a name="step-4-test"></a>Schritt 4: Testen

### <a name="azure-portal"></a>[**Azure-Portal**](#tab/test-portal)

1. Öffnen Sie das [Azure-Portal](https://ms.portal.azure.com).

1. Navigieren Sie zu Ihrem Suchdienst.

1. Wählen Sie auf der Seite „Übersicht“ die Registerkarte **Indizes** aus:

   + Mitglieder der Rolle „Suchindexdatenleser“ fragen den Index über den Such-Explorer ab. Sie können jede API-Version verwenden, um den Zugriff zu überprüfen. Sie sollten Abfragen durchführen und Ergebnisse anzeigen können, aber Sie können die Indexdefinition nicht anzeigen.

   + Mitglieder der Rolle „Mitwirkender an Suchindexdaten“ können **Neuer Index** auswählen, um einen neuen Index zu erstellen. Durch Speichern eines neuen Index wird der Schreibzugriff auf den Dienst überprüft.

### <a name="rest-api"></a>[**REST-API**](#tab/test-rest)

+ Registrieren Sie Ihre Anwendung mit Azure Active Directory.

+ Überarbeiten Sie Ihren Code so, dass eine [Such-REST-API](/rest/api/searchservice/) (jede unterstützte Version) verwendet wird. Legen Sie den **Autorisierungsheader** für Anforderungen fest, und ersetzen Sie den Header **api-key**.

  :::image type="content" source="media/search-security-rbac/rest-authorization-header.png" alt-text="Screenshot einer HTTP-Anforderung mit Autorisierungsheader" border="true":::

Weitere Informationen zum Abrufen eines Tokens für eine bestimmte Umgebung finden Sie unter [Microsoft Identity Platform-Authentifizierungsbibliotheken](../active-directory/develop/reference-v2-libraries.md).

### <a name="net-sdk"></a>[**.NET SDK**](#tab/test-dotnet)

Das Azure SDK für .NET unterstützt einen Autorisierungsheader im Paket [NuGet-Katalog | Azure.Search.Documents 11.4.0-beta.2](https://www.nuget.org/packages/Azure.Search.Documents/11.4.0-beta.2).

Möglicherweise ist eine zusätzliche Konfiguration erforderlich, um eine Anwendung bei Azure Active Directory zu registrieren oder Autorisierungstoken abzurufen und zu übergeben.

+ Beim Abrufen des OAuth-Tokens ist der Bereich https://search.azure.com/.default. Das SDK erfordert, dass die Zielgruppe https://search.azure.com ist. „.default“ ist eine Azure AD-Konvention.

+ Das SDK überprüft, ob der Benutzer über den Bereich „user_impersonation“ verfügt, der von Ihrer App gewährt werden muss, aber das SDK selbst fragt lediglich nach https://search.azure.com/.default.

Beispiel der Verwendung von [Anmeldeinformationen für geheimen Clientschlüssel](/dotnet/api/azure.core.tokencredential):

```csharp
var tokenCredential =  new ClientSecretCredential(aadTenantId, aadClientId, aadSecret);
SearchClient srchclient = new SearchClient(serviceEndpoint, indexName, tokenCredential);
```

Weitere Informationen zur Verwendung der [AAD-Authentifizierung mit dem Azure SDK für .NET](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/identity/Azure.Identity) finden Sie im GitHub-Repository des SDK.

> [!NOTE]
> Wenn Sie den Fehler 403 erhalten, prüfen Sie, ob Ihr Suchdienst beim Vorschauprogramm registriert und ob Ihr Dienst für Zuweisungen von Vorschaurollen konfiguriert ist.

---

## <a name="disable-api-key-authentication"></a>Deaktivieren der API-Schlüssel-Authentifizierung

API-Schlüssel können nicht gelöscht werden, aber Sie können sie in Ihrem Dienst deaktivieren. Wenn Sie die Rollen „Suchdienstmitwirkender“, „Mitwirkender an Suchindexdaten“ und „Suchindexdatenleser“ sowie die Azure AD-Authentifizierung verwenden, können Sie API-Schlüssel deaktivieren. Dadurch lehnt der Suchdienst alle datenbezogenen Anforderungen ab, die im Header einen API-Schlüssel für inhaltsbezogene Anforderungen übergeben.

Deaktivieren Sie die [schlüsselbasierte Authentifizierung](search-security-api-keys.md) mithilfe der Verwaltungs-REST-API-Version 2021-04-01-Preview, und senden Sie zwei aufeinanderfolgende Anforderungen an den [Updatedienst](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update).

Zum Deaktivieren von Features sind die Berechtigungen „Besitzer“ oder „Mitwirkender“ erforderlich. Führen Sie die folgenden Schritte in Postman oder einem anderen Webtesttool aus (siehe folgenden Tipp):

1. Legen Sie für die erste Anforderung [AuthOptions](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#dataplaneauthoptions) auf aadOrApiKey fest, um die Azure AD-Authentifizierung zu aktivieren. Beachten Sie, dass die Option angibt, dass eine der beiden Methoden möglich ist: Azure AD oder die nativen API-Schlüssel.

    ```http
    PUT https://management.azure.com/subscriptions/{{subscriptionId}}/resourcegroups/{{resource-group}}/providers/Microsoft.Search/searchServices/{{search-service-name}}?api-version=2021-04-01-Preview
    {
      "location": "{{region}}",
      "sku": {
        "name": "standard"
      },
      "properties": {
        "authOptions": {
          "aadOrApiKey": {
            "aadAuthFailureMode": "http401WithBearerChallenge"
          }
        }
      }
   }
    ```

1. Legen Sie bei der zweiten Anforderung [disableLocalAuth](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#request-body) auf TRUE fest. In diesem Schritt wird der API-Schlüsselteil der Option aadOrApiKey deaktiviert, sodass Ihnen nur die Azure AD-Authentifizierung bleibt.

    ```http
    PUT https://management.azure.com/subscriptions/{{subscriptionId}}/resourcegroups/{{resource-group}}/providers/Microsoft.Search/searchServices/{{search-service-name}}?api-version=2021-04-01-Preview
    {
      "location": "{{region}}",
      "sku": {
        "name": "standard"
      },
      "properties": {
        "disableLocalAuth": true
      }
    }
    ```

Sie können die Schritte 1 und 2 nicht kombinieren. In Schritt 1 muss disableLocalAuth auf FALSE festgelegt sein, um die Anforderungen zum Festlegen von AuthOptions zu erfüllen. In Schritt 2 wird dieser Wert in TRUE geändert.

Um die Schlüsselauthentifizierung erneut zu aktivieren, müssen Sie die letzte Anforderung erneut ausführen und disableLocalAuth auf FALSE festlegen. Der Suchdienst akzeptiert API-Schlüssel in Anforderungen automatisch erneut (vorausgesetzt, diese Schlüssel sind angegeben).

> [!TIP]
> Aufrufe der Verwaltungs-REST-API werden von Azure Active Directory authentifiziert. Anleitungen zum Einrichten eines Sicherheitsprinzipals und einer Anforderung finden Sie in diesem Blogbeitrag zu [Azure-REST-APIs mit Postman (2021)](https://blog.jongallant.com/2021/02/azure-rest-apis-postman-2021/). Das vorherige Beispiel wurde anhand der Anweisungen und der Postman-Sammlung im Blogbeitrag getestet.

## <a name="conditional-access"></a>Bedingter Zugriff

Mit dem Tool [Bedingter Zugriff](../active-directory/conditional-access/overview.md) erzwingt Azure Active Directory Organisationsrichtlinien. Mithilfe von Richtlinien für den bedingten Zugriff können Sie bei Bedarf die richtigen Zugriffssteuerungen anwenden, um die Sicherheit Ihrer Organisation zu gewährleisten. Beim Zugriff auf einen Azure Cognitive Search-Dienst mithilfe der rollenbasierten Zugriffssteuerung kann der bedingte Zugriff Organisationsrichtlinien erzwingen.

Führen Sie die folgenden Schritte aus, um eine Richtlinie für bedingten Zugriff für Azure Cognitive Search zu aktivieren:
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Bedingter Azure AD-Zugriff**.
1. Wählen Sie **Richtlinien** aus.
1. Wählen Sie **+ Neue Richtlinie** aus.
1. Fügen Sie im Abschnitt **Cloud-Apps oder Aktionen** der Richtlinie **Azure Cognitive Search** als Cloud-App hinzu, je nachdem, wie Sie Ihre Richtlinie einrichten möchten.
1. Aktualisieren Sie die verbleibenden Parameter der Richtlinie. Geben Sie beispielsweise an, für welche Benutzer und Gruppen diese Richtlinie gilt. 
1. Speichern Sie die Richtlinie.

> [!IMPORTANT]
> Wenn Ihrem Suchdienst eine verwaltete Identität zugewiesen ist, wird der spezifische Suchdienst als Cloud-App angezeigt, die als Teil der Richtlinie für bedingten Zugriff eingeschlossen oder ausgeschlossen werden kann. Richtlinien für bedingten Zugriff können nicht für einen bestimmten Suchdienst erzwungen werden. Stellen Sie stattdessen sicher, dass Sie die allgemeine Cloud-App für **Azure Cognitive Search** auswählen.
