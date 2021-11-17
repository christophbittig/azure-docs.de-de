---
title: Konfigurieren der rollenbasierten Zugriffssteuerung für Ihr Azure Cosmos DB-Konto mit Azure AD
description: Hier erfahren Sie, wie Sie die rollenbasierte Zugriffssteuerung mit Azure Active Directory für Ihr Azure Cosmos DB-Konto konfigurieren.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/21/2021
ms.author: thweiss
ms.openlocfilehash: a659b4cdd404e9090fee177d383f26733aa4496b
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132371845"
---
# <a name="configure-role-based-access-control-with-azure-active-directory-for-your-azure-cosmos-db-account"></a>Konfigurieren der rollenbasierten Zugriffssteuerung mit Azure Active Directory für Ihr Azure Cosmos DB-Konto
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!NOTE]
> In diesem Artikel geht es um die rollenbasierte Zugriffssteuerung für Vorgänge auf Datenebene in Azure Cosmos DB. Falls Sie Vorgänge auf Verwaltungsebene verwenden, finden Sie weitere Informationen in dem Artikel zur [rollenbasierten Zugriffssteuerung](role-based-access-control.md), die für Vorgänge auf Verwaltungsebene angewendet wird.

Azure Cosmos DB bietet ein integriertes System für die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC), das folgende Möglichkeiten umfasst:

- Authentifizieren Ihrer Datenanforderungen mit einer Azure AD-Identität (Azure Active Directory)
- Autorisieren Ihrer Datenanforderungen mit einem differenzierten, rollenbasierten Berechtigungsmodell

## <a name="concepts"></a>Konzepte

RBAC auf Datenebene in Azure Cosmos DB basiert auf Konzepten, die in anderen RBAC-Systemen wie [Azure RBAC](../role-based-access-control/overview.md) üblich sind:

- Das [Berechtigungsmodell](#permission-model) besteht aus einer Reihe von **Aktionen**, die jeweils einem oder mehreren Datenbankvorgängen zugeordnet sind. Einige Beispiele für Aktionen sind das Lesen eines Elements, das Schreiben eines Elements oder das Ausführen einer Abfrage.
- Azure Cosmos DB-Benutzer erstellen **[Rollendefinitionen](#role-definitions)** , die eine Liste zulässiger Aktionen enthalten.
- Rollendefinitionen werden mithilfe von **[Rollenzuweisungen](#role-assignments)** bestimmten Azure AD-Identitäten zugewiesen. Eine Rollenzuweisung definiert auch den Bereich, auf den die Rollendefinition angewendet wird. Derzeit gibt es drei Bereiche:
    - Azure Cosmos DB-Konto
    - Azure Cosmos DB-Datenbank
    - Azure Cosmos DB-Container

  :::image type="content" source="./media/how-to-setup-rbac/concepts.png" alt-text="RBAC-Konzepte":::

## <a name="permission-model"></a><a id="permission-model"></a> Berechtigungsmodell

> [!IMPORTANT]
> Dieses Berechtigungsmodell deckt nur Datenbankvorgänge ab, die das Lesen und Schreiben von Daten umfassen. Es deckt *keine* Verwaltungsvorgänge für Verwaltungsressourcen ab, z. B.:
> - Erstellen/Ersetzen/Löschen von Datenbanken
> - Erstellen/Ersetzen/Löschen von Containern
> - Ersetzen des Containerdurchsatzes
> - Erstellen/Ersetzen/Löschen/Lesen gespeicherter Prozeduren
> - Erstellen/Ersetzen/Löschen/Lesen von Triggern
> - Erstellen/Ersetzen/Löschen/Lesen benutzerdefinierter Funktionen
>
> *Sie können kein SDK auf Azure Cosmos DB-Datenebene verwenden*, um Verwaltungsvorgänge mit einer Azure AD-Identität zu authentifizieren. Stattdessen müssen Sie [Azure RBAC](role-based-access-control.md) über eine der folgenden Optionen verwenden:
> - [Azure Resource Manager-Vorlagen (ARM-Vorlagen)](./sql/manage-with-templates.md)
> - [Azure PowerShell-Skripts](./sql/manage-with-powershell.md)
> - [Azure CLI-Skripts](./sql/manage-with-cli.md)
> - Azure-Verwaltungsbibliotheken, verfügbar in:
>   - [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.CosmosDB/)
>   - [Java](https://search.maven.org/artifact/com.azure.resourcemanager/azure-resourcemanager-cosmos)
>   - [Python](https://pypi.org/project/azure-mgmt-cosmosdb/)
>   
> „Datenbank lesen“ und „Container lesen“ werden als [Metadatenanforderungen](#metadata-requests) betrachtet. Der Zugriff auf diese Vorgänge kann wie im folgenden Abschnitt beschrieben gewährt werden.

In der folgenden Tabelle sind alle Aktionen aufgeführt, die vom Berechtigungsmodell bereitgestellt werden.

| Name | Entsprechende Datenbankvorgänge |
|---|---|
| `Microsoft.DocumentDB/databaseAccounts/readMetadata` | Lesen von Kontometadaten. Ausführliche Informationen finden Sie unter [Metadatenanforderungen](#metadata-requests). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/create` | Erstellen eines neuen Elements. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read` | Lesen eines einzelnen Elements anhand seiner ID und seines Partitionsschlüssels (Punktlesevorgang). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/replace` | Ersetzen eines vorhandenen Elements. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/upsert` | „Upsert“ eines Elements, d. h. Erstellen des Elements, wenn es nicht vorhanden ist, oder Ersetzen des Elements, wenn es vorhanden ist. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/delete` | Löschen eines Elements. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery` | Ausführen einer [SQL-Abfrage](sql-query-getting-started.md). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed` | Lesen aus dem [Änderungsfeed](read-change-feed.md) des Containers. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeStoredProcedure` | Ausführen einer [gespeicherten Prozedur](stored-procedures-triggers-udfs.md). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/manageConflicts` | Verwalten von [Konflikten](conflict-resolution-policies.md) für Konten mit mehreren Schreibregionen (d. h. Auflisten und Löschen von Elementen aus dem Konfliktfeed). |

Platzhalter werden sowohl auf *Containerebene* als auch auf *Elementebene* unterstützt:

- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*`
- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*`

### <a name="metadata-requests"></a><a id="metadata-requests"></a> Metadatenanforderungen

Bei Verwendung von Azure Cosmos DB-SDKs geben diese SDKs schreibgeschützte Metadatenanforderungen während der Initialisierung und zur Verarbeitung bestimmter Datenanforderungen aus. Diese Metadatenanforderungen rufen verschiedene Konfigurationsdetails ab. Dazu gehören beispielsweise: 

- Die globale Konfiguration Ihres Kontos, einschließlich der Azure-Regionen, in denen das Konto verfügbar ist
- Der Partitionsschlüssel Ihrer Container oder deren Indizierungsrichtlinie
- Die Liste der physischen Partitionen, die einen Container und seine Adressen bilden

Es werden *keine* Daten abgerufen, die Sie in Ihrem Konto gespeichert haben.

Um bestmögliche Transparenz des Berechtigungsmodells sicherzustellen, werden diese Metadatenanforderungen explizit durch die Aktion `Microsoft.DocumentDB/databaseAccounts/readMetadata` abgedeckt. Diese Aktion sollte in jeder Situation zulässig sein, in der über eines der Azure Cosmos DB-SDKs auf Ihr Azure Cosmos DB-Konto zugegriffen wird. Sie kann auf jeder Ebene in der Azure Cosmos DB-Hierarchie (d. h. Konto, Datenbank oder Container) zugewiesen werden (über eine Rollenzuweisung).

Die tatsächlich von der Aktion `Microsoft.DocumentDB/databaseAccounts/readMetadata` zugelassenen Metadatenanforderungen richten sich nach dem Bereich, dem die Aktion zugewiesen ist:

| Bereich | Von der Aktion zugelassene Anforderungen |
|---|---|
| Konto | – Auflisten der Datenbanken unter dem Konto<br>– Für jede Datenbank unter dem Konto: die zulässigen Aktionen im Datenbankbereich |
| Datenbank | – Lesen von Datenbankmetadaten<br>– Auflisten der Container unter der Datenbank<br>– Für jeden Container unter der Datenbank: die zulässigen Aktionen im Containerbereich |
| Container | – Lesen von Containermetadaten<br>– Auflisten physischer Partitionen unter dem Container<br>– Auflösen der Adresse der einzelnen physischen Partitionen |

## <a name="built-in-role-definitions"></a>Definitionen integrierter Rollen

Azure Cosmos DB macht zwei integrierte Rollendefinitionen verfügbar:

| id | Name | Eingeschlossene Aktionen |
|---|---|---|
| 00000000-0000-0000-0000-000000000001 | Integrierter Cosmos DB-Datenleser | `Microsoft.DocumentDB/databaseAccounts/readMetadata`<br>`Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read`<br>`Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery`<br>`Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed` |
| 00000000-0000-0000-0000-000000000002 | Integrierter Mitwirkender an Cosmos DB-Daten | `Microsoft.DocumentDB/databaseAccounts/readMetadata`<br>`Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*`<br>`Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*` |

## <a name="create-custom-role-definitions"></a><a id="role-definitions"></a> Erstellen benutzerdefinierter Rollendefinitionen

Beim Erstellen einer benutzerdefinierten Rollendefinition müssen Sie Folgendes angeben:

- Den Namen Ihres Azure Cosmos DB-Kontos.
- Die Ressourcengruppe, die Ihr Konto enthält.
- Den Typ der Rollendefinition: `CustomRole`
- Den Namen der Rollendefinition.
- Eine Liste von [Aktionen](#permission-model), die von der Rolle zugelassen werden sollen.
- Mindestens einen Bereich, in dem die Rollendefinition zugewiesen werden kann. Die folgende Bereiche werden unterstützt:
    - `/` (Kontoebene)
    - `/dbs/<database-name>` (Datenbankebene)
    - `/dbs/<database-name>/colls/<container-name>` (Containerebene)

> [!NOTE]
> Die unten beschriebenen Vorgänge sind verfügbar in:
> - Azure PowerShell: [Az.CosmosDB, Version 1.2.0](https://www.powershellgallery.com/packages/Az.CosmosDB/1.2.0) oder höher
> - [Azure CLI](/cli/azure/install-azure-cli): Version 2.24.0 oder höher

### <a name="using-azure-powershell"></a>Verwenden von Azure PowerShell

Erstellen Sie eine Rolle mit dem Namen *MyReadOnlyRole*, die nur Leseaktionen enthält:

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>"
New-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -Type CustomRole -RoleName MyReadOnlyRole `
    -DataAction @( `
        'Microsoft.DocumentDB/databaseAccounts/readMetadata',
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed') `
    -AssignableScope "/"
```

Erstellen Sie eine Rolle mit dem Namen *MyReadWriteRole*, die alle Aktionen enthält:

```powershell
New-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -Type CustomRole -RoleName MyReadWriteRole `
    -DataAction @( `
        'Microsoft.DocumentDB/databaseAccounts/readMetadata',
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*') `
    -AssignableScope "/"
```

Listen Sie die Rollendefinitionen auf, die Sie zum Abrufen der jeweiligen IDs erstellt haben:

```powershell
Get-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName
```

```
RoleName         : MyReadWriteRole
Id               : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAcc
                   ounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>
Type             : CustomRole
Permissions      : {Microsoft.Azure.Management.CosmosDB.Models.Permission}
AssignableScopes : {/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAc
                   counts/<myCosmosAccount>}

RoleName         : MyReadOnlyRole
Id               : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAcc
                   ounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>
Type             : CustomRole
Permissions      : {Microsoft.Azure.Management.CosmosDB.Models.Permission}
AssignableScopes : {/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAc
                   counts/<myCosmosAccount>}
```

### <a name="using-the-azure-cli"></a>Verwenden der Azure CLI

Erstellen Sie eine Rolle mit dem Namen *MyReadOnlyRole*, die nur Leseaktionen enthält:

```json
// role-definition-ro.json
{
    "RoleName": "MyReadOnlyRole",
    "Type": "CustomRole",
    "AssignableScopes": ["/"],
    "Permissions": [{
        "DataActions": [
            "Microsoft.DocumentDB/databaseAccounts/readMetadata",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed"
        ]
    }]
}
```

```azurecli
resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
az cosmosdb sql role definition create --account-name $accountName --resource-group $resourceGroupName --body @role-definition-ro.json
```

Erstellen Sie eine Rolle mit dem Namen *MyReadWriteRole*, die alle Aktionen enthält:

```json
// role-definition-rw.json
{
    "RoleName": "MyReadWriteRole",
    "Type": "CustomRole",
    "AssignableScopes": ["/"],
    "Permissions": [{
        "DataActions": [
            "Microsoft.DocumentDB/databaseAccounts/readMetadata",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*"
        ]
    }]
}
```

```azurecli
az cosmosdb sql role definition create --account-name $accountName --resource-group $resourceGroupName --body @role-definition-rw.json
```

Listen Sie die Rollendefinitionen auf, die Sie zum Abrufen der jeweiligen IDs erstellt haben:

```azurecli
az cosmosdb sql role definition list --account-name $accountName --resource-group $resourceGroupName
```

```
[
  {
    "assignableScopes": [
      "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>"
    ],
    "id": "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>",
    "name": "<roleDefinitionId>",
    "permissions": [
      {
        "dataActions": [
          "Microsoft.DocumentDB/databaseAccounts/readMetadata",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*"
        ],
        "notDataActions": []
      }
    ],
    "resourceGroup": "<myResourceGroup>",
    "roleName": "MyReadWriteRole",
    "sqlRoleDefinitionGetResultsType": "CustomRole",
    "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions"
  },
  {
    "assignableScopes": [
      "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>"
    ],
    "id": "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>",
    "name": "<roleDefinitionId>",
    "permissions": [
      {
        "dataActions": [
          "Microsoft.DocumentDB/databaseAccounts/readMetadata",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed"
        ],
        "notDataActions": []
      }
    ],
    "resourceGroup": "<myResourceGroup>",
    "roleName": "MyReadOnlyRole",
    "sqlRoleDefinitionGetResultsType": "CustomRole",
    "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions"
  }
]
```

### <a name="using-azure-resource-manager-templates"></a>Verwenden von Azure-Ressourcen-Manager-Vorlagen

Auf [dieser Seite](/rest/api/cosmos-db-resource-provider/2021-04-15/sql-resources/create-update-sql-role-definition) finden Sie eine Referenz und Beispiele für die Verwendung Azure Resource Manager-Vorlagen zum Erstellen von Rollendefinitionen.

## <a name="create-role-assignments"></a><a id="role-assignments"></a> Erstellen von Rollenzuweisungen

Sie können Ihren Azure AD-Identitäten integrierte oder benutzerdefinierte Rollendefinitionen zuordnen. Beim Erstellen einer Rollenzuweisung müssen Sie Folgendes angeben:

- Den Namen Ihres Azure Cosmos DB-Kontos.
- Die Ressourcengruppe, die Ihr Konto enthält.
- Die ID der zuzuweisenden Rollendefinition.
- Die Prinzipal-ID der Identität, der die Rollendefinition zugewiesen werden soll.
- Den Bereich der Rollenzuweisung. Folgende Bereiche werden unterstützt:
    - `/` (Kontoebene)
    - `/dbs/<database-name>` (Datenbankebene)
    - `/dbs/<database-name>/colls/<container-name>` (Containerebene)

  Der Bereich muss einem der Bereiche entsprechen, die in der Rollendefinition zugewiesen werden können, oder ein Unterbereich davon sein.

> [!NOTE]
> Wenn Sie eine Rollenzuweisung für einen Dienstprinzipal erstellen möchten, stellen Sie sicher, dass die zugehörige **Objekt-ID** wie im Abschnitt **Unternehmensanwendungen** auf dem Blatt **Azure Active Directory** des Portals verwendet wird.

> [!NOTE]
> Die unten beschriebenen Vorgänge sind verfügbar in:
> - Azure PowerShell: [Az.CosmosDB, Version 1.2.0](https://www.powershellgallery.com/packages/Az.CosmosDB/1.2.0) oder höher
> - [Azure CLI](/cli/azure/install-azure-cli): Version 2.24.0 oder höher

### <a name="using-azure-powershell"></a>Verwenden von Azure PowerShell

Weisen Sie einer Identität eine Rolle zu:

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>"
$readOnlyRoleDefinitionId = "<roleDefinitionId>" # as fetched above
$principalId = "<aadPrincipalId>"
New-AzCosmosDBSqlRoleAssignment -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -RoleDefinitionId $readOnlyRoleDefinitionId `
    -Scope "/" `
    -PrincipalId $principalId
```

### <a name="using-the-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle

Weisen Sie einer Identität eine Rolle zu:

```azurecli
resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
readOnlyRoleDefinitionId = '<roleDefinitionId>' # as fetched above
principalId = '<aadPrincipalId>'
az cosmosdb sql role assignment create --account-name $accountName --resource-group $resourceGroupName --scope "/" --principal-id $principalId --role-definition-id $readOnlyRoleDefinitionId
```

### <a name="using-azure-resource-manager-templates"></a>Verwenden von Azure-Ressourcen-Manager-Vorlagen

Auf [dieser Seite](/rest/api/cosmos-db-resource-provider/2021-04-15/sql-resources/create-update-sql-role-assignment) finden Sie eine Referenz und Beispiele für die Verwendung Azure Resource Manager-Vorlagen zum Erstellen von Rollenzuweisungen.

## <a name="initialize-the-sdk-with-azure-ad"></a>Initialisieren des SDK mit Azure AD

Wenn Sie Azure Cosmos DB-RBAC in Ihrer Anwendung verwenden möchten, müssen Sie die Methode zur Initialisierung des Azure Cosmos DB-SDK aktualisieren. Anstatt den Primärschlüssel Ihres Kontos zu übergeben, müssen Sie eine Instanz einer `TokenCredential`-Klasse übergeben. Diese Instanz stellt dem Azure Cosmos DB-SDK den Kontext bereit, der zum Abrufen eines AAD-Tokens im Namen der zu verwendenden Identität erforderlich ist.

Die Methode zur Erstellung einer `TokenCredential`-Instanz wird in diesem Artikel nicht behandelt. Es gibt viele Möglichkeiten, eine solche Instanz zu erstellen, die vom Typ der zu verwendenden AAD-Identität abhängen (Benutzerprinzipal, Dienstprinzipal, Gruppe usw.). Am wichtigsten ist, dass Ihre `TokenCredential`-Instanz in die Identität (Prinzipal-ID) aufgelöst werden muss, der Sie die Rollen zugewiesen haben. Beispiele für das Erstellen einer `TokenCredential`-Klasse finden Sie hier:

- [In .NET](/dotnet/api/overview/azure/identity-readme#credential-classes)
- [In Java](/java/api/overview/azure/identity-readme#credential-classes)
- [In JavaScript](/javascript/api/overview/azure/identity-readme#credential-classes)

In den folgenden Beispielen wird ein Dienstprinzipal mit einer `ClientSecretCredential`-Instanz verwendet.

### <a name="in-net"></a>In .NET

Azure Cosmos DB RBAC wird derzeit vom [.NET SDK V3](sql-api-sdk-dotnet-standard.md) unterstützt.

```csharp
TokenCredential servicePrincipal = new ClientSecretCredential(
    "<azure-ad-tenant-id>",
    "<client-application-id>",
    "<client-application-secret>");
CosmosClient client = new CosmosClient("<account-endpoint>", servicePrincipal);
```

### <a name="in-java"></a>In Java

Azure Cosmos DB RBAC wird derzeit vom [Java SDK V4](sql-api-sdk-java-v4.md) unterstützt.

```java
TokenCredential ServicePrincipal = new ClientSecretCredentialBuilder()
    .authorityHost("https://login.microsoftonline.com")
    .tenantId("<azure-ad-tenant-id>")
    .clientId("<client-application-id>")
    .clientSecret("<client-application-secret>")
    .build();
CosmosAsyncClient Client = new CosmosClientBuilder()
    .endpoint("<account-endpoint>")
    .credential(ServicePrincipal)
    .build();
```

### <a name="in-javascript"></a>In JavaScript

Azure Cosmos DB RBAC wird derzeit vom [JavaScript SDK V3](sql-api-sdk-node.md) unterstützt.

```javascript
const servicePrincipal = new ClientSecretCredential(
    "<azure-ad-tenant-id>",
    "<client-application-id>",
    "<client-application-secret>");
const client = new CosmosClient({
    "<account-endpoint>",
    aadCredentials: servicePrincipal
});
```

## <a name="authenticate-requests-on-the-rest-api"></a>Authentifizieren von Anforderungen an die REST-API

Legen Sie beim Erstellen des [REST-API-Autorisierungsheaders](/rest/api/cosmos-db/access-control-on-cosmosdb-resources) den Parameter **type** auf **aad** und die Hashsignatur **(sig)** auf das **OAuth-Token** fest, wie im folgenden Beispiel gezeigt:

`type=aad&ver=1.0&sig=<token-from-oauth>`

## <a name="use-data-explorer"></a>Verwenden des Daten-Explorers

> [!NOTE]
> Der im Azure-Portal verfügbare Daten-Explorer unterstützt die rollenbasierte Zugriffssteuerung (RBAC) von Azure Cosmos DB noch nicht. Um Ihre Azure AD-Identität beim Erkunden Ihrer Daten zu verwenden, müssen Sie stattdessen den [Azure Cosmos DB-Explorer](https://cosmos.azure.com/?feature.enableAadDataPlane=true) verwenden.

Wenn Sie mit dem speziellen Abfrageparameter `?feature.enableAadDataPlane=true` auf [Azure Cosmos DB-Explorer](https://cosmos.azure.com/?feature.enableAadDataPlane=true) zugreifen und sich anmelden, wird die folgende Logik für den Zugriff auf doe Daten verwendet:

1. Im Namen der angemeldeten Identität wird eine Anforderung erstellt, um den Primärschlüssels des Kontos abzurufen. Wenn diese Anforderung erfolgreich ist, wird der Primärschlüssel für den Zugriff auf die Daten des Kontos verwendet.
1. Wenn die angemeldete Identität den Primärschlüssel des Kontos nicht abrufen darf, wird diese Identität direkt für die Authentifizierung des Datenzugriffs verwendet. In diesem Modus müssen der Identität die [richtigen Rollendefinitionen zugewiesen](#role-assignments) werden, um den Datenzugriff sicherzustellen.

## <a name="audit-data-requests"></a>Überwachen von Datenanforderungen

Bei Verwendung von Azure Cosmos DB-RBAC werden [Diagnoseprotokolle](cosmosdb-monitor-resource-logs.md) durch Identitäts- und Autorisierungsinformationen für jeden Datenvorgang erweitert. Dies ermöglicht eine detaillierte Überwachung und das Abrufen der AAD-Identität, die für jede an Ihr Azure Cosmos DB-Konto gesendete Datenanforderung verwendet wurde.

Diese zusätzlichen Informationen fließen in die Protokollkategorie **DataPlaneRequests** ein und umfassen zwei zusätzliche Spalten:

- `aadPrincipalId_g` gibt die Prinzipal-ID der AAD-Identität an, die zum Authentifizieren der Anforderung verwendet wurde.
- `aadAppliedRoleAssignmentId_g` gibt die [Rollenzuweisung](#role-assignments) an, die beim Autorisieren der Anforderung berücksichtigt wurde.

## <a name="enforcing-rbac-as-the-only-authentication-method"></a><a id="disable-local-auth"></a> Erzwingen von RBAC als einzige Authentifizierungsmethode

In Situationen, in denen Clients ausschließlich über RBAC eine Verbindung mit Azure Cosmos DB herstellen sollen, haben Sie die Möglichkeit, die primären/sekundären Schlüssel des Kontos zu deaktivieren. Daraufhin werden alle eingehenden Anforderungen, bei denen entweder ein primärer oder sekundärer Schlüssel oder ein Ressourcentoken verwendet wird, aktiv abgelehnt.

### <a name="using-azure-resource-manager-templates"></a>Verwenden von Azure-Ressourcen-Manager-Vorlagen

Legen Sie die Eigenschaft `disableLocalAuth` auf `true` fest, wenn Sie Ihr Azure Cosmos DB-Konto mithilfe von Azure Resource Manager-Vorlagen erstellen oder aktualisieren:

```json
"resources": [
    {
        "type": " Microsoft.DocumentDB/databaseAccounts",
        "properties": {
            "disableLocalAuth": true,
            // ...
        },
        // ...
    },
    // ...
 ]
```

## <a name="limits"></a>Einschränkungen

- Sie können bis zu 100 Rollendefinitionen und 2.000 Rollenzuweisungen pro Azure Cosmos DB-Konto erstellen.
- Sie können Rollendefinitionen nur Azure AD-Identitäten zuweisen, die demselben Azure AD-Anker wie Ihr Azure Cosmos DB-Konto angehören.
- Die Azure AD-Gruppenauflösung wird derzeit für Identitäten, die zu mehr als 200 Gruppen gehören, nicht unterstützt.
- Das Azure AD-Token wird zurzeit mit jeder einzelnen an den Azure Cosmos DB-Dienst gesendeten Anforderung als Header übergeben und erhöht so die Gesamtnutzlastgröße.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="which-azure-cosmos-db-apis-are-supported-by-rbac"></a>Welche Azure Cosmos DB-APIs werden von RBAC unterstützt?

Derzeit wird nur die SQL-API unterstützt.

### <a name="is-it-possible-to-manage-role-definitions-and-role-assignments-from-the-azure-portal"></a>Können Rollendefinitionen und Rollenzuweisungen über das Azure-Portal verwaltet werden?

Eine Unterstützung für die Rollenverwaltung ist im Azure-Portal noch nicht verfügbar.

### <a name="which-sdks-in-azure-cosmos-db-sql-api-support-rbac"></a>Welche SDKs in der SQL-API von Azure Cosmos DB unterstützen RBAC?

Die SDKs für [.NET v3](sql-api-sdk-dotnet-standard.md), [Java v4](sql-api-sdk-java-v4.md) und [JavaScript v3](sql-api-sdk-node.md) werden derzeit unterstützt.

### <a name="is-the-azure-ad-token-automatically-refreshed-by-the-azure-cosmos-db-sdks-when-it-expires"></a>Wird das Azure AD-Token bei Ablauf automatisch von den Azure Cosmos DB-SDKs aktualisiert?

Ja.

### <a name="is-it-possible-to-disable-the-usage-of-the-account-primarysecondary-keys-when-using-rbac"></a>Kann die Verwendung der Primär-/Sekundärschlüssel für das Konto bei Nutzung von RBAC deaktiviert werden?

Ja. Weitere Informationen finden Sie unter [Erzwingen von RBAC als einzige Authentifizierungsmethode](#disable-local-auth).

## <a name="next-steps"></a>Nächste Schritte

- Überblick über [sicheren Zugriff auf Daten in Cosmos DB](secure-access-to-data.md).
- Erfahren Sie mehr über [RBAC für die Azure Cosmos DB-Verwaltung](role-based-access-control.md).
