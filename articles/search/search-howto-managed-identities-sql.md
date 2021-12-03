---
title: Einrichten einer Verbindung mit Azure SQL-Datenbank mithilfe einer verwalteten Identität
titleSuffix: Azure Cognitive Search
description: Erfahren Sie, wie Sie eine Indexerverbindung mit Azure SQL-Datenbank mithilfe einer verwalteten Identität einrichten.
author: gmndrg
ms.author: gimondra
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/02/2021
ms.openlocfilehash: 1a975d8eeebe86bbdf09870ab6c450727bdd545b
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894405"
---
# <a name="set-up-an-indexer-connection-to-azure-sql-database-using-a-managed-identity"></a>Einrichten einer Indexerverbindung mit Azure SQL-Datenbank mithilfe einer verwalteten Identität

Auf dieser Seite wird beschrieben, wie Sie eine Indexerverbindung mit Azure SQL-Datenbank mithilfe einer verwalteten Identität einrichten, anstatt Anmeldeinformationen in der Verbindungszeichenfolge des Datenquellenobjekts anzugeben.

Sie können eine systemseitig zugewiesene verwaltete Identität oder eine benutzerseitig zugewiesene verwaltete Identität (Vorschau) verwenden.

Bevor Sie mehr über diese Funktion erfahren, sollten Sie wissen, was ein Indexer ist, und wie Sie einen Indexer für Ihre Datenquelle einrichten. Weitere Informationen finden Sie unter den folgenden Links:

* [Indexer (Übersicht)](search-indexer-overview.md)
* [Azure SQL-Indexer](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

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

## <a name="2---provision-azure-active-directory-admin-for-sql-server"></a>2\. Bereitstellen eines Azure Active Directory-Administrators für SQL Server

Beim Herstellen einer Verbindung mit der Datenbank im nächsten Schritt müssen Sie eine Verbindung mit einem Azure Active Directory-Konto (Azure AD-Konto) herstellen, das über Administratorzugriff für die Datenbank verfügt, damit dem Suchdienst die Berechtigung zum Zugriff auf die Datenbank erteilt wird.

Unter [Konfigurieren und Verwalten der Azure Active Directory-Authentifizierung mit Azure SQL](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell) erfahren Sie, wie Sie Ihrem Azure AD-Kontoadministrator Zugriff auf die Datenbank geben.

## <a name="3---assign-permissions-to-read-the-database"></a>3\. Zuweisen von Berechtigungen zum Lesen der Datenbank

Führen Sie die folgenden Schritte aus, um dem Suchdienst oder der benutzerseitig zugewiesenen verwalteten Identität die Berechtigung zum Lesen der Datenbank zuzuweisen.

1. Herstellen der Verbindung mit Visual Studio

    ![Herstellen der Verbindung mit Visual Studio](./media/search-managed-identities/connect-with-visual-studio.png "Herstellen der Verbindung mit Visual Studio")

2. Authentifizierung mit dem Azure AD-Konto

    ![Authentifizieren](./media/search-managed-identities/visual-studio-authenticate.png "Authenticate")

3. Führen Sie die folgenden Befehle aus:

    Geben Sie die Klammern um den Namen Ihres Suchdiensts oder der benutzerseitig zugewiesenen verwalteten Identität mit ein.
    
    ```
    CREATE USER [insert your search service name here or user-assigned managed identity name] FROM EXTERNAL PROVIDER;
    EXEC sp_addrolemember 'db_datareader', [insert your search service name here or user-assigned managed identity name];
    ```

    ![Neue Abfrage](./media/search-managed-identities/visual-studio-new-query.png "Neue Abfrage")

    ![Ausführen der Abfrage](./media/search-managed-identities/visual-studio-execute-query.png "Abfrage ausführen")

>[!NOTE]
> Wenn die Identität des Suchdiensts oder die benutzerseitig zugewiesene Identität aus Schritt 1 nach dem Ausführen dieses Schritts geändert wird, müssen Sie die Rollenmitgliedschaft und den Benutzer in der SQL-Datenbank entfernen und dann durch erneutes Ausführen von Schritt 3 die Berechtigungen erneut hinzufügen.
> Die Rollenmitgliedschaft und der Benutzer können durch Ausführen der folgenden Befehle entfernt werden:
> ```
> sp_droprolemember 'db_datareader', [insert your search service name or user-assigned managed identity name];
> DROP USER IF EXISTS [insert your search service name or user-assigned managed identity name];
> ```

## <a name="4---add-a-role-assignment"></a>4\. Hinzufügen einer Rollenzuweisung

In diesem Schritt erteilen Sie dem Azure Cognitive Search-Dienst die Berechtigung, Daten von Ihrem SQL Server zu lesen.

1. Navigieren Sie im Azure-Portal zur Seite des Azure SQL Servers.
2. Wählen Sie **Zugriffssteuerung (IAM)** aus.
3. Klicken Sie auf **Hinzufügen** und dann auf **Rollenzuweisung hinzufügen**.

    ![Hinzufügen der Rollenzuweisung](./media/search-managed-identities/add-role-assignment-sql-server.png "Rollenzuweisung hinzufügen")

4. Wählen Sie die entsprechende Rolle **Leser** aus.
5. Behalten Sie unter **Zugriff zuweisen zu** die Option **Azure AD-Benutzer, -Gruppe oder -Dienstprinzipal** bei.
6. Wenn Sie eine systemseitig zugewiesene verwaltete Identität verwenden, suchen Sie nach Ihrem Suchdienst, und wählen Sie ihn aus. Wenn Sie eine benutzerseitig zugewiesene verwaltete Identität verwenden, suchen Sie nach dem Namen der benutzerseitig zugewiesenen verwalteten Identität, und wählen Sie sie aus. Wählen Sie **Speichern** aus.

    Beispiel für Azure SQL mit einer systemseitig zugewiesenen verwalteten Identität:

    ![Hinzufügen der Rollenzuweisung für „Leser“](./media/search-managed-identities/add-role-assignment-sql-server-reader-role.png "Hinzufügen der Rollenzuweisung für „Leser“")

## <a name="5---create-the-data-source"></a>5\. Erstellen der Datenquelle

Erstellen Sie die Datenquelle, und stellen Sie entweder eine systemseitig zugewiesene verwaltete Identität oder eine benutzerseitig zugewiesene verwaltete Identität (Vorschau) bereit. Beachten Sie, dass Sie die Verwaltungs-REST-API in den folgenden Schritten nicht mehr verwenden.

### <a name="option-1---create-the-data-source-with-a-system-assigned-managed-identity"></a>Option 1: Erstellen der Datenquelle mit einer systemseitig zugewiesenen verwalteten Identität

Die systemseitig zugewiesene verwaltete Identität wird von der [REST-API](/rest/api/searchservice/create-data-source), dem Azure-Portal und dem [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) unterstützt. Im Folgenden finden Sie ein Beispiel für das Erstellen einer Datenquelle zum Indizieren von Daten aus einer Azure SQL-Datenbank mithilfe der [REST-API](/rest/api/searchservice/create-data-source) und einer Verbindungszeichenfolge für verwaltete Identitäten. Das Format der Verbindungszeichenfolge für verwaltete Identitäten ist für die REST-API, das .NET SDK und das Azure-Portal identisch.

Beim Erstellen einer Datenquelle mit der [REST-API](/rest/api/searchservice/create-data-source) muss die Datenquelle die folgenden erforderlichen Eigenschaften aufweisen:

* **name** ist der eindeutige Name der Datenquelle im Suchdienst.
* **type** ist `azuresql`.
* **credentials**
    * Beim Verwenden einer verwalteten Identität zur Authentifizierung unterscheidet sich das Format für **credentials** von dem bei der Authentifizierung ohne verwaltete Identität. Hier geben Sie den Namen für „Initial Catalog“ oder „Initial Database“ und eine „ResourceId“ an, der kein Kontoschlüssel und kein Kennwort zugewiesen ist. Die „ResourceId“ muss die Abonnement-ID von Azure SQL-Datenbank, die Ressourcengruppe der SQL-Datenbank und den Namen der SQL-Datenbank enthalten. 
    * Format der Verbindungszeichenfolge für verwaltete Identitäten:
        * *Initial Catalog|Database=**Datenbankname**;ResourceId=/subscriptions/**Ihre Abonnement-ID**/resourceGroups/**Name Ihrer Ressourcengruppe**/providers/Microsoft.Sql/servers/**Name Ihres SQL-Servers**/;Connection Timeout=**Dauer des Verbindungstimeouts**;*
* **container** gibt den Namen der Tabelle oder Ansicht an, die indiziert werden soll.

Beispiel für die Erstellung eines Azure SQL-Datenquellenobjekts über die [REST-API](/rest/api/searchservice/create-data-source):

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-datasource",
    "type" : "azuresql",
    "credentials" : { 
        "connectionString" : "Database=[SQL database name];ResourceId=/subscriptions/[subscription ID]/resourceGroups/[resource group name]/providers/Microsoft.Sql/servers/[SQL Server name];Connection Timeout=30;"
    },
    "container" : { 
        "name" : "my-table" 
    }
} 
```

### <a name="option-2---create-the-data-source-with-a-user-assigned-managed-identity"></a>Option 2: Erstellen der Datenquelle mit einer benutzerseitig zugewiesenen verwalteten Identität

Die REST-API 2021-04-30-preview unterstützt die benutzerseitig zugewiesene verwaltete Identität. Im Folgenden finden Sie ein Beispiel für das Erstellen einer Datenquelle zum Indizieren von Daten aus einem Speicherkonto mithilfe der [REST-API](/rest/api/searchservice/create-data-source), einer Verbindungszeichenfolge für verwaltete Identitäten und der benutzerseitig zugewiesenen verwalteten Identität.

Die Datenquelle muss über die folgenden erforderlichen Eigenschaften verfügen:

* **name** ist der eindeutige Name der Datenquelle im Suchdienst.
* **type** ist `azuresql`.
* **credentials**
    * Beim Verwenden einer verwalteten Identität zur Authentifizierung unterscheidet sich das Format für **credentials** von dem bei der Authentifizierung ohne verwaltete Identität. Hier geben Sie den Namen für „Initial Catalog“ oder „Initial Database“ und eine „ResourceId“ an, der kein Kontoschlüssel und kein Kennwort zugewiesen ist. Die „ResourceId“ muss die Abonnement-ID von Azure SQL-Datenbank, die Ressourcengruppe der SQL-Datenbank und den Namen der SQL-Datenbank enthalten. 
    * Format der Verbindungszeichenfolge für verwaltete Identitäten:
        * *Initial Catalog|Database=**Datenbankname**;ResourceId=/subscriptions/**Ihre Abonnement-ID**/resourceGroups/**Name Ihrer Ressourcengruppe**/providers/Microsoft.Sql/servers/**Name Ihres SQL-Servers**/;Connection Timeout=**Dauer des Verbindungstimeouts**;*
* **container** gibt den Namen der Tabelle oder Ansicht an, die indiziert werden soll.
* **identity** enthält die Sammlung der benutzerseitig zugewiesenen verwalteten Identitäten. Beim Erstellen der Datenquelle sollte nur eine benutzerseitig zugewiesene verwaltete Identität bereitgestellt werden.
    * **userAssignedIdentities** enthält die Details der benutzerseitig zugewiesenen verwalteten Identität.
        * Format der benutzerseitig zugewiesenen verwalteten Identität: 
            * /subscriptions/**Abonnement-ID**/resourcegroups/**Name der Ressourcengruppe**/providers/Microsoft.ManagedIdentity/userAssignedIdentities/**Name der verwalteten Identität**

Beispiel zum Erstellen eines Blob-Datenquellenobjekts mithilfe der [REST-API](/rest/api/searchservice/create-data-source):

```http
POST https://[service name].search.windows.net/datasources?api-version=2021-04-30-preview
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-datasource",
    "type" : "azuresql",
    "credentials" : { 
        "connectionString" : "Database=[SQL database name];ResourceId=/subscriptions/[subscription ID]/resourceGroups/[resource group name]/providers/Microsoft.Sql/servers/[SQL Server name];Connection Timeout=30;"
    },
    "container" : { 
        "name" : "my-table" 
    },
    "identity" : { 
        "@odata.type": "#Microsoft.Azure.Search.DataUserAssignedIdentity",
        "userAssignedIdentity" : "/subscriptions/[subscription ID]/resourcegroups/[resource group name]/providers/Microsoft.ManagedIdentity/userAssignedIdentities/[managed identity name]"
    }
}   
```

## <a name="6---create-the-index"></a>6\. Erstellen des Index

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

## <a name="7---create-the-indexer"></a>7\. Erstellen des Indexers

Ein Indexer verbindet eine Datenquelle mit einem Zielsuchindex und stellt einen Zeitplan zur Automatisierung der Datenaktualisierung bereit.

Nach der Erstellung von Index und Datenquelle können Sie den Indexer erstellen.

Beispiel für die Indexerdefinition eines Azure SQL-Indexers:

```
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-indexer",
    "dataSourceName" : "sql-datasource",
    "targetIndexName" : "my-target-index",
    "schedule" : { "interval" : "PT2H" }
}
```    

Dieser Indexer wird alle zwei Stunden ausgeführt (das Planungsintervall wird auf „PT2H“ festgelegt). Um einen Indexer alle 30 Minuten auszuführen, legen Sie das Intervall auf „PT30M“ fest. Das kürzeste unterstützte Intervall beträgt fünf Minuten. Der Zeitplan ist optional. Ohne Zeitplan wird ein Indexer nur einmal bei seiner Erstellung ausgeführt. Allerdings können Sie ein Indexer bei Bedarf jederzeit ausführen.   

Weitere Informationen zur API zum Erstellen eines Indexers finden Sie unter [Erstellen eines Indexers](/rest/api/searchservice/create-indexer).

Weitere Informationen zum Definieren von Indexerzeitplänen finden Sie unter [Festlegen eines Zeitplans für Indexer in der kognitiven Azure-Suche](search-howto-schedule-indexers.md).

## <a name="troubleshooting"></a>Problembehandlung

Wenn beim Versuch des Indexers, eine Verbindung mit der Datenquelle herzustellen, eine Fehlermeldung angezeigt wird, in der angegeben wird, dass der Zugriff des Clients auf den Server nicht zulässig ist, finden Sie entsprechende Informationen unter den [häufigen Fehlern bei Indexern](./search-indexer-troubleshooting.md).

## <a name="see-also"></a>Weitere Informationen

Weitere Informationen zum Azure SQL-Indexer:
* [Azure SQL-Indexer](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)