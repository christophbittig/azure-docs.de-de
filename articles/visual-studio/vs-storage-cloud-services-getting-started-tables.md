---
title: Erste Schritte mit Tabellenspeicher mit Visual Studio (Clouddienste)
description: Erfahren Sie etwas über die ersten Schritte mit Azure-Tabellenspeicher in einem Clouddienstprojekt in Visual Studio, nachdem Sie mithilfe von verbundenen Visual Studio-Diensten eine Verbindung mit einem Speicherkonto hergestellt haben.
services: storage
author: ghogen
manager: jillfra
ms.assetid: a3a11ed8-ba7f-4193-912b-e555f5b72184
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure, devx-track-csharp
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 3cad09a5359bd5d9bd2041eb92d0e994adf80080
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124823085"
---
# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Erste Schritte mit Azure-Tabellenspeicher und verbundenen Visual Studio-Diensten (Clouddienstprojekte)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Übersicht

[!INCLUDE [Cloud Services (classic) deprecation announcement](../cloud-services/includes/deprecation-announcement.md)]


Dieser Artikel beschreibt, wie die ersten Schritte des Verwendens von Azure-Tabellenspeicher in Visual Studio aussehen, nachdem Sie über das Visual Studio-Dialogfeld **Verbundene Dienste hinzufügen** in einem Clouddienst-Projekt ein Azure-Speicherkonto erstellt oder auf ein solches Konto verwiesen haben. Beim Vorgang **Verbundene Dienste hinzufügen** werden die entsprechenden NuGet-Pakete installiert, um auf Azure-Speicher in Ihrem Projekt zuzugreifen, und Ihren Projektkonfigurationsdateien die Verbindungszeichenfolge für das Speicherkonto hinzugefügt.

Der Azure-Tabellenspeicherdienst ermöglicht das Speichern großer Mengen von strukturierten Daten. Der Dienst ist ein NoSQL-Datenspeicher, der authentifizierte Aufrufe von innerhalb und außerhalb der Azure-Cloud akzeptiert. Azure-Tabellen sind hervorragend zur Speicherung strukturierter nicht relationaler Daten geeignet.

Damit Sie beginnen können, müssen Sie zuerst eine Tabelle in Ihrem Speicherkonto erstellen. Es wird gezeigt, wie Sie eine Azure-Tabelle in Code erstellen, und außerdem, wie Sie grundlegende Tabellen- und Entitätsvorgänge, etwa Hinzufügen, Ändern und Lesen von Tabellenentitäten, ausführen. Die Beispiele sind in C\# geschrieben und greifen auf die [Microsoft Azure Storage-Clientbibliothek für .NET](/previous-versions/azure/dn261237(v=azure.100)) zurück.

**HINWEIS** : Einige der APIs, die Aufrufe an den Azure-Speicher ausführen, arbeiten asynchron. Unter [Asynchrone Programmierung mit Async und Await](/previous-versions/hh191443(v=vs.140)) finden Sie weitere Informationen. Im folgenden Code wird die Programmierung mit Async-Methoden angenommen.

* Weitere Informationen zum programmgesteuerten Bearbeiten von Tabellen finden Sie unter [Erste Schritte mit Azure Table Storage mit .NET](../cosmos-db/tutorial-develop-table-dotnet.md) .
* Allgemeine Informationen zum Azure-Speicher finden Sie unter [Speicherdokumentation](https://azure.microsoft.com/documentation/services/storage/) .
* Allgemeine Informationen zu Azure-Clouddiensten finden Sie unter [Cloud Services-Dokumentation](https://azure.microsoft.com/documentation/services/cloud-services/) .
* Weitere Informationen über das Programmieren von ASP.NET-Anwendungen finden Sie unter [ASP.NET](https://www.asp.net) .

## <a name="access-tables-in-code"></a>Zugreifen auf Tabellen im Code
Um auf Tabellen in Clouddienst-Projekten zuzugreifen, müssen Sie die folgenden Elemente zu jeder C#-Quelldatei hinzufügen, in der auf Azure-Tabellenspeicher zugegriffen wird.

1. Vergewissern Sie sich, dass die Namespacedeklarationen am Anfang der C#-Datei diese **using** -Anweisungen enthalten.
   
    ```csharp
    using Microsoft.Framework.Configuration;
    using Azure.Data.Table;
    using System.Collections.Generic
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```
2. Rufen Sie ein **AzureStorageConnectionString**-Objekt zum Erstellen eines **TableServiceClient** ab, der Vorgänge auf Kontoebene wie das Erstellen und Löschen von Tabellen ausführt.
   
    ```csharp
    string storageConnString = "_AzureStorageConnectionString"
    ```

   > [!NOTE]
   > Verwenden Sie den gesamten obigen Code vor dem Code in den folgenden Beispielen.
   
3. Rufen Sie ein **TableServiceClient**-Objekt ab, um auf die Tabellenobjekte in Ihrem Speicherkonto zu verweisen.
   
    ```csharp
    // Create the table service client.
    TableServiceClient tableServiceClient = new TableServiceClient(storageConnString);
    ```

4. Rufen Sie ein **TableClient**-Verweisobjekt ab, um auf eine bestimmte Tabelle und bestimmte Entitäten zu verweisen.
   
    ```csharp
    // Get a reference to a table named "peopleTable".
    TableClient peopleTable = tableServiceClient.GetTableClient("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Erstellen einer Tabelle in Code
Um die Azure-Tabelle zu erstellen, fügen Sie einfach einen Aufruf von **CreateIfNotExistsAsync** hinzu, nachdem Sie wie im Abschnitt „Zugriff auf Tabellen in Code“ beschrieben ein **TableClient**-Objekt abgerufen haben.

```csharp
// Create the TableClient if it does not exist.
await peopleTable.CreateIfNotExistsAsync();
```

## <a name="add-an-entity-to-a-table"></a>Hinzufügen einer Entität zu einer Tabelle
Erstellen Sie eine Klasse, mit der die Eigenschaften der Entität definiert werden, um eine Entität zu einer Tabelle hinzuzufügen. Der folgenden Code definiert eine Entitätsklasse namens **CustomerEntity** , die den Vornamen des Kunden als Zeilenschlüssel und den Nachnamen als Partitionsschlüssel verwendet.

```csharp
public class CustomerEntity : ITableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

AddEntity-Vorgänge, die Entitäten betreffen, werden mit dem **TableClient**-Objekt ausgeführt, das Sie zuvor unter „Zugreifen auf Tabellen im Code“ erstellt haben. Die Methode **AddEntity** repräsentiert den abzuschließenden Vorgang. Das folgende Codebeispiel zeigt das Erstellen eines **TableClient**-Objekts und eines **CustomerEntity**-Objekts. Zur Vorbereitung des Vorgangs wird die Kundenentität mit **AddEntity** in die Tabelle eingefügt.

```csharp
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Inserts the customer entity.
peopleTable.AddEntity(customer1)
```


## <a name="insert-a-batch-of-entities"></a>Einfügen eines Entitätsbatchs
Sie können mehrere Entitäten mit einem Schreibvorgang in eine Tabelle einfügen. Das folgende Codebeispiel erstellt zwei Entitätsobjekte („Jeff Smith“ und „Ben Smith“), fügt sie mit der AddRange-Methode zu einem **addEntitiesBatch**-Objekt hinzu und startet dann den Vorgang durch Aufruf von **TableClient.SubmitTransactionAsync**.

```csharp
// Create a list of 2 entities with the same partition key.
List<CustomerEntity> entityList = new List<CustomerEntity>
{
    new CustomerEntity("Smith", "Jeff")
    {
        { "Email", "Jeff@contoso.com" },
        { "PhoneNumber", "425-555-0104" }
    },
    new CustomerEntity("Smith", "Ben")
    {
        { "Email", "Ben@contoso.com" },
        { "PhoneNumber", "425-555-0102" }
    },
};

// Create the batch.
List<TableTransactionAction> addEntitiesBatch = new List<TableTransactionAction>();

// Add the entities to be added to the batch.
addEntitiesBatch.AddRange(entityList.Select(e => new TableTransactionAction(TableTransactionActionType.Add, e)));

// Submit the batch.
Response<IReadOnlyList<Response>> response = await peopleTable.SubmitTransactionAsync(addEntitiesBatch).ConfigureAwait(false);
```

## <a name="get-all-of-the-entities-in-a-partition"></a>Abrufen aller Entitäten in einer Partition
Verwenden Sie ein **Abfrage**-Methode, um für eine Tabelle alle Entitäten einer Partition abzufragen. Im folgenden Codebeispiel wird ein Filter für Entitäten erstellt, wobei "Smith" der Partitionsschlüssel ist. In diesem Beispiel werden die Felder der einzelnen Entitäten in den Abfrageergebnissen an die Konsole ausgegeben.

```csharp
Pageable<CustomerEntity> queryResultsFilter = peopleTable.Query<CustomerEntity>(filter: "PartitionKey eq 'Smith'");

// Print the fields for each customer.
foreach (CustomerEntity qEntity in queryResultsFilter)
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", qEntity.PartitionKey, qEntity.RowKey, qEntity.Email, qEntity.PhoneNumber);
}
```


## <a name="get-a-single-entity"></a>Abrufen einer einzelnen Entität
Sie können eine Abfrage schreiben, um eine bestimmte Entität abzurufen. Der folgende Code verwendet eine **GetEntityAsync**-Methode, um einen Kunden namens „Ben Smith“ anzugeben. Diese Methode gibt anstelle einer Sammlung nur eine einzelne Entität zurück, und bei dem zurückgegebenen Wert in **GetEntityAsync.Result** handelt es sich um ein **CustomerEntity**-Objekt. Die Angabe beider Schlüssel (für Partition und Zeile) in einer Abfrage ist die schnellste Möglichkeit, um eine einzelne Entität aus dem **Tabellenspeicherdienst** abzurufen.

```csharp
var singleResult = peopleTable.GetEntityAsync<CustomerEntity>("Smith", "Ben");

// Print the phone number of the result.
if (singleResult.Result != null)
    Console.WriteLine(((CustomerEntity)singleResult.Result).PhoneNumber);
else
    Console.WriteLine("The phone number could not be retrieved.");
```

## <a name="delete-an-entity"></a>Löschen einer Entität
Sie können eine Entität nach dem Abrufen löschen. Der folgende Code sucht nach einer Kundenentität namens "Ben Smith". Wenn diese gefunden wird, wird sie gelöscht.

```csharp
var singleResult = peopleTable.GetEntityAsync<CustomerEntity>("Smith", "Ben");

CustomerEntity deleteEntity = (CustomerEntity)singleResult.Result;

// Delete the entity given the partition and row key.
if (deleteEntity != null)
{
    await peopleTable.DeleteEntity(deleteEntity.PartitionKey, deleteEntity.RowKey);

    Console.WriteLine("Entity deleted.");
}

else
    Console.WriteLine("Couldn't delete the entity.");
```

## <a name="next-steps"></a>Nächste Schritte
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]
