---
title: Einrichten einer Verbindung mit einem Speicherkonto mithilfe einer verwalteten Identität
titleSuffix: Azure Cognitive Search
description: Hier erfahren Sie, wie Sie mithilfe einer verwalteten Identität eine Indexerverbindung mit einem Azure Storage-Konto einrichten.
author: gmndrg
ms.author: gimondra
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: d714c4cd1b507a8722bb6407fb991a2b158b72e6
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894372"
---
# <a name="set-up-a-connection-to-an-azure-storage-account-using-a-managed-identity"></a>Einrichten einer Verbindung mit einem Azure Storage-Konto mithilfe einer verwalteten Identität

Auf dieser Seite wird beschrieben, wie Sie eine Indexerverbindung mit einer Azure-Speicherkonto mithilfe einer verwalteten Identität einrichten, anstatt Anmeldeinformationen in der Verbindungszeichenfolge des Datenquellenobjekts anzugeben.

Sie können eine systemseitig zugewiesene verwaltete Identität oder eine benutzerseitig zugewiesene verwaltete Identität (Vorschau) verwenden.

In diesem Artikel wird davon ausgegangen, dass Sie mit den Konzepten und der Konfiguration von Indexern vertraut sind. Wenn Sie noch nicht mit Indexern gearbeitet haben, beginnen Sie mit den folgenden Links:

* [Indexer (Übersicht)](search-indexer-overview.md)
* [Azure-Blobindexer](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage Gen2-Indexer](search-howto-index-azure-data-lake-storage.md)
* [Azure-Tabellenindexer](search-howto-indexing-azure-tables.md)

## <a name="1---set-up-a-managed-identity"></a>1: Einrichten einer verwalteten Identität

Richten Sie die [verwaltete Identität](../active-directory/managed-identities-azure-resources/overview.md) für einen Azure Cognitive Search-Dienst mit einer der folgenden Optionen ein. 

Der Suchdienst muss Basic-Stufe oder höher sein.

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

* **type** ist der Typ der Identität. Gültige Werte sind „SystemAssigned“, „UserAssigned“ oder „SystemAssigned, UserAssigned“ für beide. Der Wert „None“ löscht alle zuvor zugewiesenen Identitäten aus dem Suchdienst.

* **userAssignedIdentities** enthält die Details der benutzerseitig zugewiesenen verwalteten Identität. Das Format lautet:

  ```bash
    /subscriptions/<your-subscription-ID>/resourcegroups/<your-resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<your-managed-identity-name>
  ```

Beispiel für eine Zuweisung einer benutzerseitig zugewiesenen verwalteten Identität:

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

In diesem Schritt erteilen Sie entweder dem Azure Cognitive Search-Dienst oder der benutzerseitig zugewiesenen verwalteten Identität die Berechtigung, Daten aus Ihrem Speicherkonto zu lesen.

1. Navigieren Sie im Azure-Portal zu dem Speicherkonto, in dem die Daten gespeichert werden, die indiziert werden sollen.

2. Wählen Sie **Zugriffssteuerung (IAM)** aus.

3. Klicken Sie auf **Hinzufügen** und dann auf **Rollenzuweisung hinzufügen**.

    ![Hinzufügen der Rollenzuweisung](./media/search-managed-identities/add-role-assignment-storage.png "Rollenzuweisung hinzufügen")

4. Wählen Sie die entsprechenden Rollen je nach Speicherkontotyp aus, den Sie indizieren möchten:

    * Bei Azure Blob Storage müssen Sie Ihren Suchdienst der Rolle **Storage-Blobdatenleser** hinzufügen.
    * Azure Data Lake Storage Gen2 erfordert, dass Sie Ihren Suchdienst zur Rolle **Storage-Blobdatenleser** hinzufügen.
    * Bei Azure Table Storage müssen Sie Ihren Suchdienst der Rolle **Lese- und Datenzugriff** hinzufügen.

5. Behalten Sie unter **Zugriff zuweisen zu** die Option **Azure AD-Benutzer, -Gruppe oder -Dienstprinzipal** bei.

6. Wenn Sie eine systemseitig zugewiesene verwaltete Identität verwenden, suchen Sie nach Ihrem Suchdienst, und wählen Sie ihn aus. Wenn Sie eine benutzerseitig zugewiesene verwaltete Identität verwenden, suchen Sie nach dem Namen der benutzerseitig zugewiesenen verwalteten Identität, und wählen Sie sie aus. Wählen Sie **Speichern** aus.

    Beispiel für Azure Blob Storage und Azure Data Lake Storage Gen2 unter Verwendung einer vom System zugewiesenen verwalteten Identität:

    ![Hinzufügen der Rollenzuweisung „Storage-Blobdatenleser“](./media/search-managed-identities/add-role-assignment-storage-blob-data-reader.png "Hinzufügen der Rollenzuweisung „Storage-Blobdatenleser“")

    Beispiel für Azure Table Storage unter Verwendung einer vom System zugewiesenen verwalteten Identität:

    ![Hinzufügen der Rollenzuweisung „Lese- und Datenzugriff“](./media/search-managed-identities/add-role-assignment-reader-and-data-access.png "Hinzufügen der Rollenzuweisung „Lese- und Datenzugriff“")

Codebeispiele in C# finden Sie unter [Index Data Lake Gen2 using Azure AD](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/data-lake-gen2-acl-indexing/README.md) auf GitHub.

## <a name="3---create-the-data-source"></a>3\. Erstellen der Datenquelle

Erstellen Sie die Datenquelle, und stellen Sie entweder eine systemseitig zugewiesene verwaltete Identität oder eine benutzerseitig zugewiesene verwaltete Identität (Vorschau) bereit. Beachten Sie, dass Sie die Verwaltungs-REST-API in den folgenden Schritten nicht mehr verwenden.

### <a name="option-1---create-the-data-source-with-a-system-assigned-managed-identity"></a>Option 1: Erstellen der Datenquelle mit einer systemseitig zugewiesenen verwalteten Identität

Die Verwendung einer systemseitig zugewiesenen verwalteten Identität wird von der [REST-API](/rest/api/searchservice/create-data-source), dem Azure-Portal und dem [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) unterstützt. Im Folgenden finden Sie ein Beispiel für das Erstellen einer Datenquelle zum Indizieren von Daten aus einem Speicherkonto mithilfe der [REST-API](/rest/api/searchservice/create-data-source) und einer Verbindungszeichenfolge für verwaltete Identitäten. Das Format der Verbindungszeichenfolge für verwaltete Identitäten ist für die REST-API, das .NET SDK und das Azure-Portal identisch.

Bei der Indizierung über ein Speicherkonto muss die Datenquelle über die folgenden erforderlichen Eigenschaften verfügen:

* **name** ist der eindeutige Name der Datenquelle im Suchdienst.
* **type**
    * Azure Blob Storage: `azureblob`
    * Azure-Tabellenspeicher: `azuretable`
    * Azure Data Lake Storage Gen2: `adlsgen2`
* **credentials**
    * Beim Verwenden einer verwalteten Identität zur Authentifizierung unterscheidet sich das Format **credentials** von dem für die Authentifizierung ohne verwaltete Identität. Hier geben Sie eine Ressourcen-ID an, der kein Kontoschlüssel und kein Kennwort zugewiesen ist. Die Ressourcen-ID muss die Abonnement-ID des Speicherkontos, die Ressourcengruppe des Speicherkontos und den Speicherkontonamen enthalten.
    * Format für verwaltete Identitäten: 
        * *ResourceId=/subscriptions/**Ihre Abonnement-ID**/resourceGroups/**Ihr Ressourcengruppenname**/providers/Microsoft.Storage/storageAccounts/**Ihr Speicherkontoname**/;*
* Mit **container** wird ein Container oder Tabellenname in Ihrem Speicherkonto festgelegt. Standardmäßig können alle Blobs im Container abgerufen werden. Wenn Sie nur Blobs in einem bestimmten virtuellen Verzeichnis indizieren möchten, können Sie dieses Verzeichnis mit dem optionalen **query**-Parameter angeben.

Beispiel zum Erstellen eines Blob-Datenquellenobjekts mithilfe der [REST-API](/rest/api/searchservice/create-data-source):

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "blob-datasource",
    "type" : "azureblob",
    "credentials" : { 
        "connectionString" : "ResourceId=/subscriptions/[subscription ID]/resourceGroups/[resource group name]/providers/Microsoft.Storage/storageAccounts/[storage account name]/;" 
    },
    "container" : { 
        "name" : "my-container", "query" : "<optional-virtual-directory-name>" 
    }
}   
```

### <a name="option-2---create-the-data-source-with-a-user-assigned-managed-identity"></a>Option 2: Erstellen der Datenquelle mit einer benutzerseitig zugewiesenen verwalteten Identität

Die REST-API 2021-04-30-preview unterstützt die benutzerseitig zugewiesene verwaltete Identität. Im Folgenden finden Sie ein Beispiel für das Erstellen einer Datenquelle zum Indizieren von Daten aus einem Speicherkonto mithilfe der [REST-API](/rest/api/searchservice/create-data-source), einer Verbindungszeichenfolge für verwaltete Identitäten und der benutzerseitig zugewiesenen verwalteten Identität.

Bei der Indizierung über ein Speicherkonto muss die Datenquelle über die folgenden erforderlichen Eigenschaften verfügen:

* **name** ist der eindeutige Name der Datenquelle im Suchdienst.
* **type**
    * Azure Blob Storage: `azureblob`
    * Azure-Tabellenspeicher: `azuretable`
    * Azure Data Lake Storage Gen2: `adlsgen2`
* **credentials**
    * Beim Verwenden einer verwalteten Identität zur Authentifizierung unterscheidet sich das Format **credentials** von dem für die Authentifizierung ohne verwaltete Identität. Hier geben Sie eine Ressourcen-ID an, der kein Kontoschlüssel und kein Kennwort zugewiesen ist. Die Ressourcen-ID muss die Abonnement-ID des Speicherkontos, die Ressourcengruppe des Speicherkontos und den Speicherkontonamen enthalten.
    * Format für verwaltete Identitäten: 
        * *ResourceId=/subscriptions/**Ihre Abonnement-ID**/resourceGroups/**Ihr Ressourcengruppenname**/providers/Microsoft.Storage/storageAccounts/**Ihr Speicherkontoname**/;*
* Mit **container** wird ein Container oder Tabellenname in Ihrem Speicherkonto festgelegt. Standardmäßig können alle Blobs im Container abgerufen werden. Wenn Sie nur Blobs in einem bestimmten virtuellen Verzeichnis indizieren möchten, können Sie dieses Verzeichnis mit dem optionalen **query**-Parameter angeben.
* **identity** enthält die Auflistung der benutzerseitig zugewiesenen verwalteten Identitäten. Beim Erstellen der Datenquelle sollte nur eine benutzerseitig zugewiesene verwaltete Identität bereitgestellt werden.
    * **userAssignedIdentities** enthält die Details der benutzerseitig zugewiesenen verwalteten Identität.
        * Format der benutzerseitig zugewiesenen verwalteten Identität: 
            * /subscriptions/**Abonnement-ID**/resourcegroups/**Name der Ressourcengruppe**/providers/Microsoft.ManagedIdentity/userAssignedIdentities/**Name der verwalteten Identität**

Beispiel zum Erstellen eines Blob-Datenquellenobjekts mithilfe der [REST-API](/rest/api/searchservice/create-data-source):

```http
POST https://[service name].search.windows.net/datasources?api-version=2021-04-30-preview
Content-Type: application/json
api-key: [admin key]

{
    "name" : "blob-datasource",
    "type" : "azureblob",
    "credentials" : { 
        "connectionString" : "ResourceId=/subscriptions/[subscription ID]/resourceGroups/[resource group name]/providers/Microsoft.Storage/storageAccounts/[storage account name]/;" 
    },
    "container" : { 
        "name" : "my-container", "query" : "<optional-virtual-directory-name>" 
    },
    "identity" : { 
        "@odata.type": "#Microsoft.Azure.Search.DataUserAssignedIdentity",
        "userAssignedIdentity" : "/subscriptions/[subscription ID]/resourcegroups/[resource group name]/providers/Microsoft.ManagedIdentity/userAssignedIdentities/[managed identity name]" 
    }
}   
```

## <a name="4---create-the-index"></a>4\. Erstellen des Index

Mit dem Index werden die Felder in einem Dokument, Attribute und andere Konstrukte für die Suchoberfläche angegeben.

Hier sehen Sie, wie Sie einen Index mit einem durchsuchbaren `content`-Feld zum Speichern des aus Blobs extrahierten Texts erstellen:   

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```

Weitere Informationen zum Erstellen von Indizes finden Sie unter [Create Index](/rest/api/searchservice/create-index) (Index erstellen).

## <a name="5---create-the-indexer"></a>5\. Erstellen des Indexers

Ein Indexer verbindet eine Datenquelle mit einem Zielsuchindex und stellt einen Zeitplan zur Automatisierung der Datenaktualisierung bereit.

Nach der Erstellung von Index und Datenquelle können Sie den Indexer erstellen.

Beispielindexerdefinition für einen Blobindexer:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

Dieser Indexer wird alle zwei Stunden ausgeführt (das Planungsintervall wird auf „PT2H“ festgelegt). Um einen Indexer alle 30 Minuten auszuführen, legen Sie das Intervall auf „PT30M“ fest. Das kürzeste unterstützte Intervall beträgt fünf Minuten. Der Zeitplan ist optional. Ohne Zeitplan wird ein Indexer nur einmal bei seiner Erstellung ausgeführt. Allerdings können Sie ein Indexer bei Bedarf jederzeit ausführen.   

Weitere Informationen zur API zum Erstellen eines Indexers finden Sie unter [Erstellen eines Indexers](/rest/api/searchservice/create-indexer).

Weitere Informationen zum Definieren von Indexerzeitplänen finden Sie unter [Festlegen eines Zeitplans für Indexer in der kognitiven Azure-Suche](search-howto-schedule-indexers.md).

## <a name="accessing-network-secured-data-in-storage-accounts"></a>Zugreifen auf im Netzwerk gesicherte Daten in Speicherkonten

Azure-Speicherkonten können mithilfe von Firewalls und virtuellen Netzwerken weiter geschützt werden. Wenn Sie Inhalte aus einem Blobspeicherkonto oder Data Lake Gen2-Speicherkonto indizieren möchten, das mit einer Firewall oder einem virtuellen Netzwerk geschützt wird, befolgen Sie die Anweisungen unter [Sicheres Zugreifen auf Daten in Speicherkonten über eine Ausnahme für einen vertrauenswürdigen Dienst](search-indexer-howto-access-trusted-service-exception.md).

## <a name="see-also"></a>Weitere Informationen

* [Azure-Blobindexer](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage Gen2-Indexer](search-howto-index-azure-data-lake-storage.md)
* [Azure-Tabellenindexer](search-howto-indexing-azure-tables.md)
* [C#-Beispiel: Index Data Lake Gen2 using Azure AD (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/data-lake-gen2-acl-indexing/README.md)