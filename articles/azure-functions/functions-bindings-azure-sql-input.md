---
title: Azure SQL-Eingabebindung für Functions
description: Erfahren Sie, wie Sie die Azure SQL-Eingabebindung in Azure Functions verwenden.
author: dzsquared
ms.topic: reference
ms.date: 11/12/2021
ms.author: drskwier
ms.reviewer: cachai
ms.openlocfilehash: 3c910ab85d3689eadfdf4ba32cc2702ce03ace65
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491087"
---
# <a name="azure-sql-input-binding-for-azure-functions-preview"></a>Azure SQL-Eingabebindung für Azure Functions (Vorschau)

Die Azure SQL-Eingabebindung ruft Daten aus einer Datenbank ab und übergibt sie an den Eingabeparameter der Funktion.

Informationen zu Setup- und Konfigurationsdetails finden Sie in der [Übersicht](./functions-bindings-azure-sql.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Dieser Abschnitt enthält folgende Beispiele:

* [HTTP-Trigger: Suchen der ID in einer Abfragezeichenfolge](#http-trigger-look-up-id-from-query-string-c)
* [HTTP-Trigger: Abrufen mehrerer Dokumente aus Routendaten](#http-trigger-get-multiple-items-from-route-data-c)

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

<a id="http-trigger-look-up-id-from-query-string-c"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-Trigger: Suchen der ID in einer Abfragezeichenfolge

Das folgende Beispiel zeigt eine [C#-Funktion](functions-dotnet-class-library.md), die einen einzelnen Datensatz abruft. Die Funktion wird durch eine HTTP-Anforderung ausgelöst, die eine Abfragezeichenfolge verwendet, um die ID anzugeben. Diese ID wird verwendet, um einen Datensatz `ToDoItem` mit der angegebenen Abfrage abzurufen.

> [!NOTE]
> Beim Wert für den HTTP-Abfragezeichenfolge-Parameter muss die Groß-/Kleinschreibung beachtet werden.
>

```cs
using System.Collections.Generic;
using System.Linq;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;

namespace AzureSQLSamples
{
    public static class GetToDoItem
    {
        [FunctionName("GetToDoItem")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = "gettodoitem")]
            HttpRequest req,
            [Sql("select * from dbo.ToDo where Id = @Id",
                CommandType = System.Data.CommandType.Text,
                Parameters = "@Id={Query.id}",
                ConnectionStringSetting = "SqlConnectionString")]
            IEnumerable<ToDoItem> toDoItem)
        {
            return new OkObjectResult(toDoItem.FirstOrDefault());
        }
    }
}
```

<a id="http-trigger-get-multiple-items-from-route-data-c"></a>

### <a name="http-trigger-get-multiple-items-from-route-data"></a>HTTP-Trigger: Abrufen mehrerer Elemente aus Routendaten

Das folgende Beispiel zeigt eine [C#-Funktion,](functions-dotnet-class-library.md) die von der Abfrage zurückgegebene Dokumente abruft. Die Funktion wird durch eine HTTP-Anforderung ausgelöst, die Routendaten verwendet, um den Wert eines Abfrageparameters anzugeben. Dieser Parameter wird verwendet, um die Datensätze `ToDoItem` in der angegebenen Abfrage zu filtern.

```cs
using System.Collections.Generic;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;

namespace AzureSQLSamples
{
    public static class GetToDoItems
    {
        [FunctionName("GetToDoItems")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = "gettodoitems/{priority}")]
            HttpRequest req,
            [Sql("select * from dbo.ToDo where [Priority] > @Priority",
                CommandType = System.Data.CommandType.Text,
                Parameters = "@Priority={priority}",
                ConnectionStringSetting = "SqlConnectionString")]
            IEnumerable<ToDoItem> toDoItems)
        {
            return new OkObjectResult(toDoItems);
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

Der Konstruktor des Attributs verwendet den SQL-Befehlstext, den Befehlstyp, Parameter und den Namen der Verbindungszeichenfolgeneinstellung. Der Befehl kann eine Transact-SQL-Abfrage (T-SQL) mit dem Befehlstyp `System.Data.CommandType.Text` oder dem Namen der gespeicherten Prozedur mit dem Befehlstyp `System.Data.CommandType.StoredProcedure` sein. Der Name der Verbindungszeichenfolgeneinstellung entspricht der Anwendungseinstellung (in `local.settings.json` für lokale Entwicklung), die die [Verbindungszeichenfolge](/dotnet/api/microsoft.data.sqlclient.sqlconnection.connectionstring?view=sqlclient-dotnet-core-3.0&preserve-view=true#Microsoft_Data_SqlClient_SqlConnection_ConnectionString) für die Azure SQL- oder SQL Server-Instanz enthält.

Hier ist ein Beispiel für ein `Sql`-Attribut in einer Methodensignatur:

```csharp
    [FunctionName("GetToDoItems")]
    public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = "gettodoitems/{priority}")]
            HttpRequest req,
            [Sql("select * from dbo.ToDo where [Priority] > @Priority",
                CommandType = System.Data.CommandType.Text,
                Parameters = "@Priority={priority}",
                ConnectionStringSetting = "SqlConnectionString")]
            IEnumerable<ToDoItem> toDoItems)
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

- [Speichern von Daten in einer Datenbank (Ausgabebindung)](./functions-bindings-azure-sql-output.md)
