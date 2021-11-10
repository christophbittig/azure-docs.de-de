---
title: Einrichten einer Verbindung mit einem Cosmos DB-Konto mithilfe einer verwalteten Identität
titleSuffix: Azure Cognitive Search
description: Hier erfahren Sie, wie Sie mithilfe einer verwalteten Identität eine Indexerverbindung mit einem Cosmos DB-Konto einrichten.
author: gmndrg
ms.author: gimondra
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/02/2021
ms.openlocfilehash: 6fbd0e851055271d8c5fcf42a970f085ede015c2
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894334"
---
# <a name="set-up-an-indexer-connection-to-a-cosmos-db-database-using-a-managed-identity"></a>Einrichten einer Indexerverbindung mit einer Cosmos DB-Datenbank mithilfe einer verwalteten Identität

Auf dieser Seite wird beschrieben, wie Sie eine Indexerverbindung mit einer Cosmos DB-Datenbank mithilfe einer verwalteten Identität einrichten, anstatt Anmeldeinformationen in der Verbindungszeichenfolge des Datenquellenobjekts anzugeben.

Sie können eine systemseitig zugewiesene verwaltete Identität oder eine benutzerseitig zugewiesene verwaltete Identität (Vorschau) verwenden.

Bevor Sie mehr über diese Funktion erfahren, sollten Sie wissen, was ein Indexer ist, und wie Sie einen Indexer für Ihre Datenquelle einrichten. Weitere Informationen finden Sie unter den folgenden Links:

* [Indexer (Übersicht)](search-indexer-overview.md)
* [Azure Cosmos DB-Indexer](search-howto-index-cosmosdb.md)

## <a name="1---set-up-a-managed-identity"></a>1: Einrichten einer verwalteten Identität

Richten Sie die [verwaltete Identität](../active-directory/managed-identities-azure-resources/overview.md) mit einer der folgenden Optionen ein:

### <a name="option-1---turn-on-system-assigned-managed-identity"></a>Option 1: Aktivieren einer systemseitig zugewiesenen verwalteten Identität

Wenn eine systemseitig zugewiesene verwaltete Identität aktiviert ist, wird in Azure eine Identität für den Suchdienst erstellt, die für die Authentifizierung bei anderen Azure-Diensten innerhalb desselben Mandanten und desselben Abonnements verwendet werden kann. Anschließend können Sie diese Identität in Azure RBAC-Zuweisungen (rollenbasierte Zugriffssteuerung) verwenden, die den Zugriff auf Daten während der Indizierung ermöglichen.

![Aktivieren einer systemseitig zugewiesenen verwalteten Identität](./media/search-managed-identities/turn-on-system-assigned-identity.png "Aktivieren einer systemseitig zugewiesenen verwalteten Identität")

Nach dem Auswählen von **Speichern** wird eine Objekt-ID angezeigt, die dem Suchdienst zugewiesen wurde.

![Objekt-ID](./media/search-managed-identities/system-assigned-identity-object-id.png "ObjectID")
 
### <a name="option-2---assign-a-user-assigned-managed-identity-to-the-search-service-preview"></a>Option 2: Zuweisen einer benutzerseitig zugewiesenen verwalteten Identität zum Suchdienst (Vorschau)

Wenn Sie noch keine benutzerseitig zugewiesene verwaltete Identität erstellt haben, müssen Sie sie erstellen. Eine benutzerseitig verwaltete Identität ist eine Azure-Ressource.

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com/) an.
1. Wählen Sie **+ Ressource erstellen**.
1. Suchen Sie in der Suchleiste „Dienste und Marketplace durchsuchen“ nach „Benutzerseitig zugewiesene verwaltete Identität“, und wählen Sie dann **Erstellen** aus.
1. Geben Sie einen beschreibenden Namen für die Identität ein.

Weisen Sie dann dem Suchdienst eine benutzerseitig zugewiesene verwaltete Identität zu. Dies kann mithilfe der [Verwaltungs-API 2021-04-01-preview](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update) erfolgen.

Die Identitätseigenschaft verwendet einen Typ und mindestens eine vollqualifizierte vom Benutzer zugewiesene Identität:

* **type** ist der Typ der Identität. Gültige Werte sind „SystemAssigned“, „UserAssigned“ oder „SystemAssigned, UserAssigned“, wenn Sie beides verwenden möchten. Der Wert „None“ löscht alle zuvor zugewiesenen Identitäten aus dem Suchdienst.
* **userAssignedIdentities** enthält die Details der benutzerseitig zugewiesenen verwalteten Identität.
    * Format der benutzerseitig zugewiesenen verwalteten Identität: 
        * /subscriptions/**Abonnement-ID**/resourcegroups/**Name der Ressourcengruppe**/providers/Microsoft.ManagedIdentity/userAssignedIdentities/**Name der verwalteten Identität**

Beispiel für das Zuweisen einer benutzerseitig zugewiesenen verwalteten Identität zu einem Suchdienst:

```http
PUT https://management.azure.com/subscriptions/[subscription ID]/resourceGroups/[resource group name]/providers/Microsoft.Search/searchServices/[search service name]?api-version=2021-04-01-preview
Content-Type: application/json

{
  "location": "[region]",
  "sku": {
    "name": "[sku]"
  },
  "properties": {
    "replicaCount": [replica count],
    "partitionCount": [partition count],
    "hostingMode": "default"
  },
  "identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/[subscription ID]/resourcegroups/[resource group name]/providers/Microsoft.ManagedIdentity/userAssignedIdentities/[name of managed identity]": {}
    }
  }
} 
```
 
## <a name="2---add-a-role-assignment"></a>2\. Hinzufügen einer Rollenzuweisung

In diesem Schritt erteilen Sie entweder dem Azure Cognitive Search-Dienst oder der benutzerseitig zugewiesenen verwalteten Identität die Berechtigung, Daten aus Ihrer Cosmos DB-Datenbank zu lesen.

1. Navigieren Sie im Azure-Portal zu dem Cosmos DB-Konto, in dem die Daten gespeichert werden, die indiziert werden sollen.
2. Wählen Sie **Zugriffssteuerung (IAM)** aus.
3. Klicken Sie auf **Hinzufügen** und dann auf **Rollenzuweisung hinzufügen**.

    ![Hinzufügen der Rollenzuweisung](./media/search-managed-identities/add-role-assignment-cosmos-db.png "Rollenzuweisung hinzufügen")

4. Wählen Sie die Cosmos DB-Rolle **Kontoleser** aus.
5. Behalten Sie unter **Zugriff zuweisen zu** die Option **Azure AD-Benutzer, -Gruppe oder -Dienstprinzipal** bei.
6. Wenn Sie eine systemseitig zugewiesene verwaltete Identität verwenden, suchen Sie nach Ihrem Suchdienst, und wählen Sie ihn aus. Wenn Sie eine benutzerseitig zugewiesene verwaltete Identität verwenden, suchen Sie nach dem Namen der benutzerseitig zugewiesenen verwalteten Identität, und wählen Sie sie aus. Wählen Sie **Speichern** aus.

    Beispiel für Cosmos DB mit einer systemseitig zugewiesenen verwalteten Identität:

    ![Hinzufügen der Rollenzuweisung „Lese- und Datenzugriff“](./media/search-managed-identities/add-role-assignment-cosmos-db-account-reader-role.png "Hinzufügen der Rollenzuweisung „Lese- und Datenzugriff“")

## <a name="3---create-the-data-source"></a>3\. Erstellen der Datenquelle

Erstellen Sie die Datenquelle, und stellen Sie entweder eine systemseitig zugewiesene verwaltete Identität oder eine benutzerseitig zugewiesene verwaltete Identität (Vorschau) bereit. Beachten Sie, dass Sie die Verwaltungs-REST-API in den folgenden Schritten nicht mehr verwenden.

### <a name="option-1---create-the-data-source-with-a-system-assigned-managed-identity"></a>Option 1: Erstellen der Datenquelle mit einer systemseitig zugewiesenen verwalteten Identität

Die Verwendung einer systemseitig zugewiesenen verwalteten Identität wird von der [REST-API](/rest/api/searchservice/create-data-source), dem Azure-Portal und dem [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) unterstützt. Im Folgenden finden Sie ein Beispiel für das Erstellen einer Datenquelle zum Indizieren von Daten aus Cosmos DB mithilfe der [REST-API](/rest/api/searchservice/create-data-source) und einer Verbindungszeichenfolge für verwaltete Identitäten. Das Format der Verbindungszeichenfolge für verwaltete Identitäten ist für die REST-API, das .NET SDK und das Azure-Portal identisch.

Wenn Sie verwaltete Identitäten für die Authentifizierung verwenden, enthalten die **Anmeldeinformationen** keinen Kontoschlüssel.

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [Search service admin key]

{
    "name": "cosmos-db-datasource",
    "type": "cosmosdb",
    "credentials": {
        "connectionString": "Database=sql-test-db;ResourceId=/subscriptions/[subscription ID]/resourceGroups/[resource group name]/providers/Microsoft.DocumentDB/databaseAccounts/[Cosmos DB account name]/;"
    },
    "container": { "name": "myCollection", "query": null },
    "dataChangeDetectionPolicy": {
        "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName": "_ts"
    }
}
```    

Der Anforderungstext umfasst die Datenquellendefinition, welche die folgenden Felder enthalten sollte:

| Feld   | BESCHREIBUNG |
|---------|-------------|
| **name** | Erforderlich. Wählen Sie für Ihr Datenquellenobjekt einen beliebigen Namen aus. |
|**type**| Erforderlich. Muss `cosmosdb`lauten. |
|**credentials** | Erforderlich. <br/><br/>Beim Herstellen einer Verbindung mit einer verwalteten Identität sollten die **Anmeldeinformationen** das folgende Format aufweisen: *Database=[database-name];ResourceId=[resource-id-string];(ApiKind=[api-kind];)*<br/> <br/>Format von ResourceId: *ResourceId=/subscriptions/**Abonnement-ID**/resourceGroups/**Name der Ressourcengruppe**/providers/Microsoft.DocumentDB/databaseAccounts/**Name des Cosmos DB-Kontos**/;*<br/><br/>Für SQL-Sammlungen ist für die Verbindungszeichenfolge keine ApiKind erforderlich.<br/><br/>Bei MongoDB-Sammlungen fügen Sie der Verbindungszeichenfolge **ApiKind=MongoDb** hinzu. <br/><br/>Registrieren Sie sich bei Verwendung von Gremlin-Graphen für die [geschlossene Indexer-Vorschau](https://aka.ms/azure-cognitive-search/indexer-preview), um Zugriff auf die Vorschauversion sowie Informationen zur Formatierung der Anmeldeinformationen zu erhalten.<br/>|
| **container** | Enthält die folgenden Elemente: <br/>**name:** Erforderlich. Geben Sie die ID der zu indizierenden Datenbanksammlung an.<br/>**Abfrage**: Optional. Sie können eine Abfrage angeben, um ein beliebiges JSON-Dokument in einem Flatfile-Schema zu vereinfachen, das in der kognitiven Azure-Suche indiziert werden kann.<br/>Für die MongoDB-API und die Gremlin-API werden keine Abfragen unterstützt. |
| **dataChangeDetectionPolicy** | Empfohlen |
|**dataDeletionDetectionPolicy** | Optional |

### <a name="option-2---create-the-data-source-with-a-user-assigned-managed-identity"></a>Option 2: Erstellen der Datenquelle mit einer benutzerseitig zugewiesenen verwalteten Identität

Die REST-API 2021-04-30-preview unterstützt die benutzerseitig zugewiesene verwaltete Identität. Im Folgenden finden Sie ein Beispiel für das Erstellen einer Datenquelle zum Indizieren von Daten aus einem Speicherkonto mithilfe der [REST-API](/rest/api/searchservice/create-data-source), einer Verbindungszeichenfolge für verwaltete Identitäten und der benutzerseitig zugewiesenen verwalteten Identität.

```http
POST https://[service name].search.windows.net/datasources?api-version=2021-04-30-preview
Content-Type: application/json
api-key: [Search service admin key]

{
    "name": "cosmos-db-datasource",
    "type": "cosmosdb",
    "credentials": {
        "connectionString": "Database=sql-test-db;ResourceId=/subscriptions/[subscription ID]/resourceGroups/[resource group name]/providers/Microsoft.DocumentDB/databaseAccounts/[Cosmos DB account name]/;"
    },
    "container": { 
        "name": "myCollection", "query": null 
    },
    "identity" : { 
        "@odata.type": "#Microsoft.Azure.Search.DataUserAssignedIdentity",
        "userAssignedIdentity" : "/subscriptions/[subscription ID]/resourcegroups/[resource group name]/providers/Microsoft.ManagedIdentity/userAssignedIdentities/[managed identity name]" 
    },
    "dataChangeDetectionPolicy": {
        "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName": "_ts"
    }
}
```    

Der Anforderungstext umfasst die Datenquellendefinition, welche die folgenden Felder enthalten sollte:

| Feld   | BESCHREIBUNG |
|---------|-------------|
| **name** | Erforderlich. Wählen Sie für Ihr Datenquellenobjekt einen beliebigen Namen aus. |
|**type**| Erforderlich. Muss `cosmosdb`lauten. |
|**credentials** | Erforderlich. <br/><br/>Beim Herstellen einer Verbindung mit einer verwalteten Identität sollten die **Anmeldeinformationen** das folgende Format aufweisen: *Database=[database-name];ResourceId=[resource-id-string];(ApiKind=[api-kind];)*<br/> <br/>Format von ResourceId: *ResourceId=/subscriptions/**Abonnement-ID**/resourceGroups/**Name der Ressourcengruppe**/providers/Microsoft.DocumentDB/databaseAccounts/**Name des Cosmos DB-Kontos**/;*<br/><br/>Für SQL-Sammlungen ist für die Verbindungszeichenfolge keine ApiKind erforderlich.<br/><br/>Bei MongoDB-Sammlungen fügen Sie der Verbindungszeichenfolge **ApiKind=MongoDb** hinzu. <br/><br/>Registrieren Sie sich bei Verwendung von Gremlin-Graphen für die [geschlossene Indexer-Vorschau](https://aka.ms/azure-cognitive-search/indexer-preview), um Zugriff auf die Vorschauversion sowie Informationen zur Formatierung der Anmeldeinformationen zu erhalten.<br/>|
| **container** | Enthält die folgenden Elemente: <br/>**name:** Erforderlich. Geben Sie die ID der zu indizierenden Datenbanksammlung an.<br/>**Abfrage**: Optional. Sie können eine Abfrage angeben, um ein beliebiges JSON-Dokument in einem Flatfile-Schema zu vereinfachen, das in der kognitiven Azure-Suche indiziert werden kann.<br/>Für die MongoDB-API und die Gremlin-API werden keine Abfragen unterstützt. |
| **Identität** | Enthält die Sammlung der benutzerseitig zugewiesenen verwalteten Identitäten Beim Erstellen der Datenquelle sollte nur eine benutzerseitig zugewiesene verwaltete Identität bereitgestellt werden. Enthält die folgenden Elemente: <br/>**userAssignedIdentities** enthält die Details der benutzerseitig zugewiesenen verwalteten Identität.<br/><br/>Format der benutzerseitig zugewiesenen verwalteten Identität: /subscriptions/**Abonnement-ID**/resourcegroups/**Name der Ressourcengruppe**/providers/Microsoft.ManagedIdentity/userAssignedIdentities/**Name der verwalteten Identität**|
| **dataChangeDetectionPolicy** | Empfohlen |
|**dataDeletionDetectionPolicy** | Optional |

## <a name="4---create-the-index"></a>4\. Erstellen des Index

Mit dem Index werden die Felder in einem Dokument, Attribute und andere Konstrukte für die Suchoberfläche angegeben.

So erstellen Sie einen Index mit einem durchsuchbaren `booktitle`-Feld

```
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-target-index",
    "fields": [
    { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    { "name": "booktitle", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}
```

Weitere Informationen zum Erstellen von Indizes finden Sie unter [Create Index](/rest/api/searchservice/create-index) (Index erstellen).

## <a name="5---create-the-indexer"></a>5\. Erstellen des Indexers

Ein Indexer verbindet eine Datenquelle mit einem Zielsuchindex und stellt einen Zeitplan zur Automatisierung der Datenaktualisierung bereit.

Nach der Erstellung von Index und Datenquelle können Sie den Indexer erstellen.

Beispiel für die Indexerdefinition:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "cosmos-db-indexer",
      "dataSourceName" : "cosmos-db-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

Dieser Indexer wird alle zwei Stunden ausgeführt (das Planungsintervall wird auf „PT2H“ festgelegt). Um einen Indexer alle 30 Minuten auszuführen, legen Sie das Intervall auf „PT30M“ fest. Das kürzeste unterstützte Intervall beträgt fünf Minuten. Der Zeitplan ist optional. Ohne Zeitplan wird ein Indexer nur einmal bei seiner Erstellung ausgeführt. Allerdings können Sie ein Indexer bei Bedarf jederzeit ausführen.   

Weitere Informationen zur API zum Erstellen eines Indexers finden Sie unter [Erstellen eines Indexers](/rest/api/searchservice/create-indexer).

Weitere Informationen zum Definieren von Indexerzeitplänen finden Sie unter [Festlegen eines Zeitplans für Indexer in der kognitiven Azure-Suche](search-howto-schedule-indexers.md).

## <a name="troubleshooting"></a>Problembehandlung

Wenn Sie feststellen, dass Sie keine Daten aus Cosmos DB indizieren können, sollten Sie Folgendes beachten:

1. Wenn die Cosmos DB-Kontoschlüssel vor Kurzem rotiert wurden, müssen Sie bis zu 15 Minuten warten, bis die Verbindungszeichenfolge für verwaltete Identitäten funktioniert.

1. Überprüfen Sie, ob der Zugriff des Cosmos DB-Kontos auf ausgewählte Netzwerke beschränkt ist. Falls ja, lesen Sie die Informationen unter [Indexerzugriff auf Datenquellen mit Azure-Netzwerksicherheitsfeatures](search-indexer-securing-resources.md).

## <a name="next-steps"></a>Nächste Schritte

* [Azure Cosmos DB-Indexer](search-howto-index-cosmosdb.md)