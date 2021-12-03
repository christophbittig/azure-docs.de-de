---
title: Verwenden der Azure Tables-Clientbibliothek für Java
description: Speichern Sie mithilfe der Azure Tables-Clientbibliothek für Java strukturierte Daten in der Cloud.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: Java
ms.topic: sample
ms.date: 12/10/2020
author: ThomasWeiss
ms.author: thweiss
ms.custom: devx-track-java
ms.openlocfilehash: e5eab216c40245aef19a7fc9ef8fd1a5bd8bc029
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487647"
---
# <a name="how-to-use-the-azure-tables-client-library-for-java"></a>Verwenden der Azure Tables-Clientbibliothek für Java

[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

In diesem Artikel erfahren Sie, wie Sie Tabellen erstellen, Daten speichern und CRUD-Vorgänge für diese Daten ausführen. Die Beispiele sind in Java geschrieben und basieren auf der [Azure Tables-Clientbibliothek für Java][Azure Tables client library for Java]. Die erläuterten Szenarien umfassten das **Erstellen**, **Auflisten** und **Löschen** von Tabellen sowie das **Einfügen**, **Abfragen**, **Ändern** und **Löschen** von Entitäten in einer Tabelle. Weitere Informationen zu Tabellen finden Sie im Abschnitt [Nächste Schritte](#next-steps) .

> [!IMPORTANT]
> Die letzte Version der Azure Tables-Clientbibliothek, die Table Storage und Cosmos DB-Tabellen unterstützt, ist [12+][Azure Tables client library for Java].

## <a name="create-an-azure-service-account"></a>Erstellen eines Azure-Dienstkontos

[!INCLUDE [cosmos-db-create-azure-service-account](../includes/cosmos-db-create-azure-service-account.md)]

**Erstellen eines Azure-Speicherkontos**

[!INCLUDE [cosmos-db-create-storage-account](../includes/cosmos-db-create-storage-account.md)]

**Erstellen eines Azure Cosmos DB-Kontos**

[!INCLUDE [cosmos-db-create-tableapi-account](../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-java-application"></a>Erstellen einer Java-Anwendung

So verwenden Sie die Beispiele in diesem Artikel
1. Installieren Sie das [Java Development Kit (JDK)](/azure/developer/java/fundamentals/java-support-on-azure#supported-java-versions-and-update-schedule).
2. Erstellen Sie ein Azure-Speicherkonto oder ein Azure Cosmos DB-Konto in Ihrem Azure-Abonnement.
3. Stellen Sie sicher, dass Ihr Entwicklungssystem die minimalen Anforderungen und Abhängigkeiten erfüllt, die im Repository [Azure Tables-Clientbibliothek für Java][Azure Tables client library for Java] auf GitHub aufgeführt sind.
4. Befolgen Sie die Anweisungen für das Herunterladen und Installieren der Azure Storage-Bibliotheken für Java auf Ihrem System von diesem Repository.
5. Erstellen Sie eine Java-App, die die Beispiele in diesem Artikel verwendet.

## <a name="configure-your-app-to-access-table-storage"></a>Konfigurieren Ihrer App für den Zugriff auf Table Storage

Fügen Sie im Abschnitt `dependencies` der Datei *pom.xml* den folgenden Eintrag hinzu:

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-data-tables</artifactId>
  <version>12.1.1</version>
</dependency>
```

Fügen Sie dann die folgenden `import`-Anweisungen am Anfang der Java-Datei dort ein, wo Azure Tables-APIs auf Tabellen zugreifen sollen:

```java
// Include the following imports to use table APIs
import com.azure.data.tables.TableClient;
import com.azure.data.tables.TableClientBuilder;
import com.azure.data.tables.TableServiceClient;
import com.azure.data.tables.TableServiceClientBuilder;
import com.azure.data.tables.models.ListEntitiesOptions;
import com.azure.data.tables.models.TableEntity;
import com.azure.data.tables.models.TableEntityUpdateMode;
import com.azure.data.tables.models.TableTransactionAction;
import com.azure.data.tables.models.TableTransactionActionType;
```

## <a name="add-your-connection-string"></a>Hinzufügen der Verbindungszeichenfolge

Sie können entweder eine Verbindung mit dem Azure-Speicherkonto oder dem Konto für die Azure Cosmos DB-Tabellen-API herstellen. Rufen Sie die Verbindungszeichenfolge basierend auf dem jeweils verwendeten Kontotyp ab.

### <a name="add-an-azure-storage-connection-string"></a>Hinzufügen einer Azure Storage-Verbindungszeichenfolge

Ein Azure Tables-Client kann eine Speicherverbindungszeichenfolge zum Speichern von Endpunkten und Anmeldeinformationen für den Zugriff auf Datenverwaltungsdienste verwenden. Bei der Ausführung in einer Client-App muss die Storage-Verbindungszeichenfolge in dem unten gezeigten Format angegeben werden. Dabei müssen der Name Ihres Storage-Kontos und der primäre Zugriffsschlüssel für das im [Azure-Portal](https://portal.azure.com) aufgeführte Storage-Konto als Werte für **AccountName** und **AccountKey** eingegeben werden.

Dieses Beispiel zeigt, wie Sie ein statisches Feld für die Verbindungszeichenfolge deklarieren:

```java
// Define the connection-string with your values.
public final String connectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=core.windows.net;
```

### <a name="add-an-azure-cosmos-db-table-api-connection-string"></a>Hinzufügen einer Verbindungszeichenfolge für die Azure Cosmos DB-Tabellen-API

Ein Azure Cosmos DB-Konto verwendet eine Verbindungszeichenfolge, um den Endpunkt für die Tabelle und Ihre Anmeldeinformationen zu speichern. Bei der Ausführung in einer Client-App muss die Azure Cosmos DB-Verbindungszeichenfolge in dem unten gezeigten Format angegeben werden. Dabei müssen der Name Ihres Azure Cosmos DB-Kontos und der primäre Zugriffsschlüssel für das im [Azure-Portal](https://portal.azure.com) aufgeführte Konto als Werte für **AccountName** und **AccountKey** eingegeben werden.

Dieses Beispiel zeigt, wie Sie ein statisches Feld für die Azure Cosmos DB-Verbindungszeichenfolge deklarieren:

```java
public final String connectionString =
    "DefaultEndpointsProtocol=https;" + 
    "AccountName=your_cosmosdb_account;" + 
    "AccountKey=your_account_key;" + 
    "TableEndpoint=https://your_endpoint;" ;
```

In einer App, die innerhalb einer Rolle in Azure ausgeführt wird, können Sie diese Zeichenfolge in der Dienstkonfigurationsdatei *ServiceConfiguration.cscfg* speichern. Sie können mit einem Aufruf der `System.getenv`-Methode darauf zugreifen. Das folgende Beispiel zeigt, wie die Verbindungszeichenfolge von einem **Setting**-Element mit der Bezeichnung *ConnectionString* in der Dienstkonfigurationsdatei abgerufen wird:

```java
// Retrieve storage account from connection-string.
String connectionString = System.getenv("ConnectionString");
```

Sie können die Verbindungszeichenfolge auch in der Datei „config.properties“ Ihres Projekts speichern:

```java
connectionString = DefaultEndpointsProtocol=https;AccountName=your_account;AccountKey=your_account_key;TableEndpoint=https://your_table_endpoint/
```

In den folgenden Beispielen wird davon ausgegangen, dass Sie eine dieser Methoden verwendet haben, um die Speicherverbindungszeichenfolge abzurufen.

## <a name="create-a-table"></a>Erstellen einer Tabelle

Ein `TableServiceClient`-Objekt ermöglicht die Interaktion mit dem Tables-Dienst, um Tabellen zu erstellen, auflisten und zu löschen. Der folgende Code erstellt ein `TableServiceClient`-Objekt und verwendet es zum Erstellen eines neuen `TableClient`-Objekts, das eine Tabelle mit der Bezeichnung `Employees` darstellt.

```java
try
{
    final String tableName = "Employees";

    // Create a TableServiceClient with a connection string.
    TableServiceClient tableServiceClient = new TableServiceClientBuilder()
        .connectionString(connectionString)
        .buildClient();

    // Create the table if it not exists.
    TableClient tableClient = tableServiceClient.createTableIfNotExists(tableName);

}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="list-the-tables"></a>Auflisten der Tabellen

Rufen Sie zum Abrufen einer Liste von Tabellen die Methode `TableServiceClient.listTables` auf, um eine wiederholbare Liste der Tabellennamen abzurufen.

```java
try
{
    // Create a TableServiceClient with a connection string.
    TableServiceClient tableServiceClient = new TableServiceClientBuilder()
        .connectionString(connectionString)
        .buildClient();

    // Loop through a collection of table names.
    tableServiceClient.listTables().forEach(tableItem -> 
        System.out.printf(tableItem.getName())
    );
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="add-an-entity-to-a-table"></a>Hinzufügen einer Entität zu einer Tabelle

Der folgende Code erstellt eine neue Instanz der `TableEntity`-Klasse mit einigen zu speichernden Kundendaten. Der Code ruft die `upsertEntity`-Methode für das `TableClient`-Objekt auf. Diese Methode fügt entweder die neue Kundenentität in die Tabelle `Employees` ein oder ersetzt die Entität, wenn sie bereits vorhanden ist.

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
     TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Create a new employee TableEntity.
    String partitionKey = "Sales";
    String rowKey = "0001";
    Map<String, Object> personalInfo= new HashMap<>();
    personalInfo.put("FirstName", "Walter");
    personalInfo.put("LastName", "Harp");
    personalInfo.put("Email", "Walter@contoso.com");
    personalInfo.put("PhoneNumber", "425-555-0101");
    TableEntity employee = new TableEntity(partitionKey, rowKey).setProperties(personalInfo);
        
    // Upsert the entity into the table
    tableClient.upsertEntity(employee);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="insert-a-batch-of-entities"></a>Einfügen eines Entitätsbatchs

Sie können einen Entitätsbatch in einem Schreibvorgang in den Tabellendienst einfügen. Mit dem folgenden Code wird ein `List<TableTransactionAction>`-Objekt erstellt, und anschließend werden dafür drei Upsertvorgänge hinzugefügt. Die einzelnen Vorgänge werden durch Erstellen eines neuen `TableEntity`-Objekts, Festlegen seiner Eigenschaften und Aufrufen der `submitTransaction`-Methode für das `TableClient`-Objekt hinzugefügt.

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    String partitionKey = "Sales";
    List<TableTransactionAction> tableTransactionActions = new ArrayList<>();
    
    Map<String, Object> personalInfo1 = new HashMap<>();
    personalInfo1.put("FirstName", "Jeff");
    personalInfo1.put("LastName", "Smith");
    personalInfo1.put("Email", "Jeff@contoso.com");
    personalInfo1.put("PhoneNumber", "425-555-0104");
    
    // Create an entity to add to the table.
    tableTransactionActions.add(new TableTransactionAction(
        TableTransactionActionType.UPSERT_MERGE,
        new TableEntity(partitionKey, "0001")
            .setProperties(personalInfo1)
    ));
    
    Map<String, Object> personalInfo2 = new HashMap<>();
    personalInfo2.put("FirstName", "Ben");
    personalInfo2.put("LastName", "Johnson");
    personalInfo2.put("Email", "Ben@contoso.com");
    personalInfo2.put("PhoneNumber", "425-555-0102");
    
    // Create another entity to add to the table.
    tableTransactionActions.add(new TableTransactionAction(
        TableTransactionActionType.UPSERT_MERGE,
        new TableEntity(partitionKey, "0002")
            .setProperties(personalInfo2)
    ));
    
    Map<String, Object> personalInfo3 = new HashMap<>();
    personalInfo3.put("FirstName", "Denise");
    personalInfo3.put("LastName", "Rivers");
    personalInfo3.put("Email", "Denise@contoso.com");
    personalInfo3.put("PhoneNumber", "425-555-0103");
    
    // Create a third entity to add to the table.
    tableTransactionActions.add(new TableTransactionAction(
        TableTransactionActionType.UPSERT_MERGE,
        new TableEntity(partitionKey, "0003")
            .setProperties(personalInfo3)
    ));

    // Submit transaction on the "Employees" table.
    tableClient.submitTransaction(tableTransactionActions);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

Beachten Sie im Zusammenhang mit Batchvorgängen Folgendes:

* Bis zu 100 Einfüge-, Lösch-, Zusammenführungs-, Ersetzungs-, Einfüge- oder Zusammenführungs- und Einfüge- oder Ersetzungsvorgänge können in beliebiger Kombination in einem einzelnen Batch ausgeführt werden.
* Ein Batchvorgang kann einen Abfragevorgang enthalten, allerdings nur als einzigen Vorgang in diesem Batch.
* Alle Entitäten in einem Batchvorgang müssen über denselben Partitionsschlüssel verfügen.
* Ein Batchvorgang ist auf eine Datennutzlast von 4 MB beschränkt.

## <a name="retrieve-all-entities-in-a-partition"></a>Abrufen aller Entitäten einer Partition

Zum Abrufen einer Tabelle für Entitäten in einer Partition können Sie ein `ListEntitiesOptions`-Objekt verwenden. Rufen Sie `ListEntitiesOptions.setFilter` auf, um eine Abfrage für eine bestimmten Tabelle zu erstellen, die einen bestimmen Ergebnistyp zurückgibt. Im folgenden Code wird ein Filter für Entitäten erstellt, wobei `Sales` der Partitionsschlüssel ist. Wenn die Abfrage mit einem Aufruf von `listEntities` für das `TableClient`-Objekt ausgeführt wird, wird ein `Iterator`-Element mit dem Wert `TableEntity` zurückgegeben. Anschließend können Sie den zurückgegebenen `Iterator` in einer „ForEach“-Schleife zum Einbinden der Ergebnisse verwenden. In diesem Code werden die Felder der einzelnen Entitäten in den Abfrageergebnissen an die Konsole ausgegeben.

```java
try
{
    // Define constants for filters.
    final String PARTITION_KEY = "PartitionKey";
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Create a filter condition where the partition key is "Sales".
    ListEntitiesOptions options = new ListEntitiesOptions().setFilter(PARTITION_KEY + " eq 'Sales'");

    // Loop through the results, displaying information about the entities.
    tableClient.listEntities(options, null, null).forEach(tableEntity -> {
        System.out.println(tableEntity.getPartitionKey() +
            " " + tableEntity.getRowKey() +
            "\t" + tableEntity.getProperty("FirstName") +
            "\t" + tableEntity.getProperty("LastName") +
            "\t" + tableEntity.getProperty("Email") +
            "\t" + tableEntity.getProperty("PhoneNumber"));
    });
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Abrufen eines Entitätsbereichs in einer Partition

Wenn Sie nicht alle Entitäten in einer Partition abrufen möchten, geben Sie mithilfe von Vergleichsoperatoren einen Bereich an. Der folgende Code kombiniert zwei Filter, um alle Entitäten in der Partition `Sales` mit einem Zeilenschlüssel zwischen „0001“ und „0004“ zu erhalten. Danach werden die Abfrageergebnisse ausgegeben. Wenn Sie die Entitäten verwenden, die im Abschnitt „Batch-Einfügungen“ zur Tabelle hinzugefügt wurden, werden nur zwei Entitäten zurückgegeben (Ben und Denise).

```java
try
{
    // Define constants for filters.
    final String PARTITION_KEY = "PartitionKey";
    final String ROW_KEY = "RowKey";
    final String tableName = "Employees";

    // Create a TableServiceClient with a connection string.
    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Create a filter condition where the partition key is "Sales".
    ListEntitiesOptions options = new ListEntitiesOptions().setFilter(PARTITION_KEY + " eq 'Sales' AND " + ROW_KEY + " lt '0004' AND ROW_KEY + " gt '0001'");
    
    // Loop through the results, displaying information about the entities.
    tableClient.listEntities(options, null, null).forEach(tableEntity -> {
        System.out.println(tableEntity.getPartitionKey() +
            " " + tableEntity.getRowKey() +
            "\t" + tableEntity.getProperty("FirstName") +
            "\t" + tableEntity.getProperty("LastName") +
            "\t" + tableEntity.getProperty("Email") +
            "\t" + tableEntity.getProperty("PhoneNumber"));
    });
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="retrieve-a-single-entity"></a>Abrufen einer einzelnen Entität

Sie können eine Abfrage schreiben, um eine einzelne bestimmte Entität abzurufen. Im folgenden Code wird die Entität für den Mitarbeiter „Jeff Smith“ nicht durch Erstellen eines `ListEntitiesOptions`-Elements und durch Verwendung von Filtern abgerufen, sondern durch Aufruf von `TableClient.getEntity` mit Partitionsschlüssel und Zeilenschlüsselparametern. Bei der Ausführung übergibt dieser Abfragevorgang nicht eine ganze Sammlung von Entitäten, sondern nur eine einzelne Entität. Wenn keine Entität mit exakter Partitions- und Zeilenschlüsselübereinstimmung existiert, wird ein `null`-Wert zurückgegeben. Die Angabe beider Schlüssel, Partition und Zeile, in einer Abfrage ist die schnellste Möglichkeit, um eine einzelne Entität aus dem Tabellenspeicherdienst abzurufen.

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Get the specific entity.
    TableEntity specificEntity = tableClient.getEntity("Sales", "0001");

    // Output the entity.
    if (specificEntity != null)
    {
        System.out.println(specificEntity.getPartitionKey() +
            " " + specificEntity.getRowKey() +
            "\t" + specificEntity.getProperty("FirstName") +
            "\t" + specificEntity.getProperty("LastName"));
            "\t" + specificEntity.getProperty("Email") +
            "\t" + specificEntity.getProperty("PhoneNumber"));
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="modify-an-entity"></a>Ändern einer Entität

Um eine Entität zu ändern, rufen Sie sie aus dem Tabellendienst ab, ändern Sie das Entitätsobjekt, und speichern Sie die Änderungen dann mit einem Ersetzungs- oder Zusammenführungsvorgang im Tabellendienst. Mit dem folgenden Code wird die Telefonnummer eines vorhandenen Kunden geändert. In diesem Fall wird nicht wie beim Einfügen `tableClient.upsertEntity`, sondern `tableClient.updateEntity` aufgerufen.

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Get the specific entity.
    TableEntity specificEntity = tableClient.getEntity("Sales", "0001");

    // Specify a new phone number
    specificEntity.getProperties().put("PhoneNumber", "425-555-0105");

    // Update the specific entity
    tableClient.updateEntity(specificEntity, TableEntityUpdateMode.REPLACE);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="query-a-subset-of-entity-properties"></a>Abfragen einer Teilmenge von Entitätseigenschaften

Mit einer Abfrage einer Tabelle können nur einige wenige Eigenschaften einer Entität aufgerufen werden. Mit dieser Technik, der sogenannten Projektion, wird die Bandbreite reduziert und die Abfrageleistung gesteigert, vor allem bei großen Entitäten. Für die Abfrage im folgenden Code wird die `ListEntitiesOptions.setSelect`-Methode verwendet, um nur die E-Mail-Adressen von Entitäten in der Tabelle zurückzugeben.

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Create a filter condition that retrieves only the Email property.
    List<String> attributesToRetrieve = new ArrayList<>();
    attributesToRetrieve.add("Email");
    
    ListEntitiesOptions options = new ListEntitiesOptions().setSelect(attributesToRetrieve);

    // Loop through the results, displaying the Email values.
    tableClient.listEntities(options, null, null).forEach(tableEntity -> {
        System.out.println(tableEntity.getProperty("Email"));
    });
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="insert-or-replace-an-entity"></a>Einfügen oder Ersetzen einer Entität

Es kommt häufig vor, dass Sie eine Entität in eine Tabelle einfügen möchten, ohne zuvor prüfen zu müssen, ob diese Entität bereits in der Tabelle vorhanden ist. Mit einem Einfügen-oder-Ersetzen-Vorgang kann eine einzelne Anforderung ausgeführt werden. Mit dieser Anforderung wird entweder die Entität eingefügt, wenn sie noch nicht vorhanden ist, oder die vorhandene Entität ersetzt. Ausgehend von vorherigen Beispielen wird durch diesen Code die Entität für "Walter Harp" eingefügt oder ersetzt. Nachdem eine neue Entität erstellt worden ist, ruft dieser Code die Methode `TableClient.upsertEntity` auf.

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Create a new table entity.
    Map<String, Object> properties = new HashMap<>();
    properties.put("FirstName", "Walter");
    properties.put("LastName", "Harp");
    properties.put("Email", "Walter@contoso.com");
    properties.put("PhoneNumber", "425-555-0101");
        
    TableEntity newEmployee = new TableEntity("Sales", "0004")
        .setProperties(properties);
        
    // Add the new customer to the Employees table.
    tableClient.upsertEntity(newEmployee);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-an-entity"></a>Löschen einer Entität

Sie können eine Entität löschen, indem Sie ihren Partitionsschlüssel und Zeilenschlüssel über `TableClient.deleteEntity` bereitstellen.

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    Delete the entity for partition key 'Sales' and row key '0001' from the table.
    tableClient.deleteEntity("Sales", "0001");
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-a-table"></a>Löschen einer Tabelle

Schließlich wird mit dem folgenden Code eine Tabelle aus einem Konto gelöscht. Etwa 40 Sekunden nach dem Löschen einer Tabelle können Sie sie nicht neu erstellen.

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Delete the table and all its data.
    tableClient.deleteTable();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="next-steps"></a>Nächste Schritte

* [Erste Schritte mit dem Azure-Tabellenspeicherdienst in Java](https://github.com/Azure-Samples/storage-table-java-getting-started)
* Beim [Microsoft Azure Storage-Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) handelt es sich um eine kostenlose eigenständige App von Microsoft, über die Sie ganz einfach visuell mit Azure Storage-Daten arbeiten können – unter Windows, MacOS und Linux.
* [Azure Tables-Clientbibliothek für Java][Azure Tables client library for Java]
* [Referenz zur Azure Tables-Clientbibliothek][Azure Tables client library reference documentation]
* [Azure Tables-REST-API][Azure Tables REST API]
* [Azure Tables-Teamblog][Azure Tables Team Blog]

Weitere Informationen finden Sie im Artikel [Azure für Java-Entwickler](/java/azure).

[Azure SDK for Java]: https://go.microsoft.com/fwlink/?LinkID=525671
[Azure Tables client library for Java]: https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/tables/azure-data-tables
[Azure Tables client library reference documentation]: https://azure.github.io/azure-sdk-for-java/tables.html
[Azure Tables REST API]: /azure/storage/tables/table-storage-overview
[Azure Tables Team Blog]: https://blogs.msdn.microsoft.com/windowsazurestorage/
