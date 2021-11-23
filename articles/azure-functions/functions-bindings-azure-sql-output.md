---
title: Azure SQL-Ausgabebindung für Functions
description: Erfahren Sie, wie Sie die Azure SQL-Ausgabebindung in Azure Functions verwenden.
author: dzsquared
ms.topic: reference
ms.date: 11/12/2021
ms.author: drskwier
ms.reviewer: cachai
ms.openlocfilehash: 37a9601f30c64108b079ec8573553a05088c8629
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493265"
---
# <a name="azure-sql-output-binding-for-azure-functions-preview"></a>Azure SQL-Ausgabebindung für Azure Functions (Vorschau)

Mit der Azure SQL-Ausgabebindung können Sie in eine Datenbank schreiben.

Informationen zu Setup- und Konfigurationsdetails finden Sie in der [Übersicht](./functions-bindings-azure-sql.md).


<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Dieser Abschnitt enthält folgende Beispiele:

* [HTTP-Trigger: Schreiben eines Datensatzes](#http-trigger-write-one-record-c)
* [HTTP-Trigger: Schreiben von Datensätzen mit IAsyncCollector](#http-trigger-write-records-using-iasynccollector-c)

Die Beispiele beziehen sich auf einen `ToDoItem`-Typ und eine entsprechende Datenbanktabelle:

```cs
namespace AzureSQLSamples
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public int Priority { get; set; }
        public string Description { get; set; }
    }
}
```

```sql
CREATE TABLE dbo.ToDo (
    [Id] int primary key,
    [Priority] int null,
    [Description] nvarchar(200) not null
)
```

<a id="http-trigger-write-one-record-c"></a>

### <a name="http-trigger-write-one-record"></a>HTTP-Trigger: Schreiben eines Datensatzes

Das folgende Beispiel zeigt eine [ C#-Funktion](functions-dotnet-class-library.md), die einer Datenbank ein Dokument hinzufügt und dazu die Daten aus der Meldung vom Warteschlangenspeicher verwendet.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;

namespace AzureSQLSamples
{
    public static class WriteOneRecord
    {
        [FunctionName("WriteOneRecord")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "addtodo")] HttpRequest req,
            ILogger log,
            [Sql("dbo.ToDo", ConnectionStringSetting = "SqlConnectionString")] out ToDoItem newItem)
        {
            newItem = new ToDoItem
            {
                Id = req.Query["id"],
                Description =req.Query["desc"]
            };

            log.LogInformation($"C# HTTP trigger function inserted one row");
            return new CreatedResult($"/api/addtodo", newItem);
        }
    }
}
```

<a id="http-trigger-write-records-using-iasynccollector-c"></a>

### <a name="http-trigger-write-records-using-iasynccollector"></a>HTTP-Trigger: Schreiben von Datensätzen mit IAsyncCollector

Das folgende Beispiel zeigt eine [ C#-Funktion](functions-dotnet-class-library.md), die einer Datenbank eine Sammlung von Datensätzen hinzufügt und dabei Daten aus dem HTTP POST-Text einer JSON-Datei verwendet.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Newtonsoft.Json;
using System.IO;
using System.Threading.Tasks;

namespace AzureSQLSamples
{
    public static class WriteRecordsAsync
    {
        [FunctionName("WriteRecordsAsync")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "post", Route = "addtodo-asynccollector")]
            HttpRequest req,
            [Sql("dbo.Products", ConnectionStringSetting = "SqlConnectionString")] IAsyncCollector<ToDoItem> newItems)
        {
            string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
            var incomingItems = JsonConvert.DeserializeObject<ToDoItem[]>(requestBody);
            foreach (ToDoItem newItem in incomingItems)
            {
                await newItems.AddAsync(newItem);
            }
            // Rows are upserted here
            await newItems.FlushAsync();

            return new CreatedResult($"/api/addtodo-asynccollector", "done");
        }
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Die Azure SQL-Bindung für Azure Functions unterstützt derzeit JavaScript nicht.

# <a name="python"></a>[Python](#tab/python)

Die Azure SQL-Bindung für Azure Functions unterstützt derzeit Python nicht.

---

## <a name="attributes-and-annotations"></a>Attribute und Anmerkungen

# <a name="c"></a>[C#](#tab/csharp)

Verwenden Sie in [C#-Klassenbibliotheken](functions-dotnet-class-library.md) das [Sql](https://github.com/Azure/azure-functions-sql-extension/blob/main/src/SqlAttribute.cs)-Attribut.

Der Konstruktor des Attributs nimmt den SQL-Befehlstext und den Namen der Verbindungszeichenfolgeeinstellung an. Bei einer Ausgabebindung ist die SQL-Befehlszeichenfolge der Name einer Tabelle, in der die Daten gespeichert werden sollen. Der Name der Verbindungszeichenfolgeneinstellung entspricht der Anwendungseinstellung (in `local.settings.json` für lokale Entwicklung), die die [Verbindungszeichenfolge](/dotnet/api/microsoft.data.sqlclient.sqlconnection.connectionstring?view=sqlclient-dotnet-core-3.0&preserve-view=true#Microsoft_Data_SqlClient_SqlConnection_ConnectionString) für die Azure SQL- oder SQL Server-Instanz enthält.

Hier ist ein Beispiel für ein `Sql`-Attribut in einer Methodensignatur:

```csharp
    [FunctionName("HTTPtoSQL")]
    public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "addtodo")] HttpRequest req,
            [Sql("dbo.ToDo", ConnectionStringSetting = "SqlConnectionString")] out ToDoItem newItem)
    {
        ...
    }
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Die Azure SQL-Bindung für Azure Functions unterstützt derzeit JavaScript nicht.

# <a name="python"></a>[Python](#tab/python)

Die Azure SQL-Bindung für Azure Functions unterstützt derzeit Python nicht.

---


[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]



## <a name="next-steps"></a>Nächste Schritte

- [Lesen von Daten aus einer Datenbank (Eingabebindung)](./functions-bindings-azure-sql-input.md)
