---
title: Azure Table Storage-Ausgabebindungen für Azure Functions
description: Hier erfahren Sie, wie Sie Azure Table Storage-Ausgabebindungen in Azure Functions verwenden.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: b30fbbca1bfabfdbf9836e00ee2b2d585308774a
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2021
ms.locfileid: "129611765"
---
# <a name="azure-table-storage-output-bindings-for-azure-functions"></a>Azure Table Storage-Ausgabebindungen für Azure Functions

Verwenden Sie eine Azure Table Storage-Ausgabebindung, um Entitäten in eine Tabelle in einem Azure Storage-Konto zu schreiben.

> [!NOTE]
> Das Aktualisieren vorhandener Entitäten wird von dieser Ausgabebindung nicht unterstützt. Verwenden Sie den `TableOperation.Replace`-Vorgang [aus dem Azure Storage-SDK](../cosmos-db/table/table-support.md) zum Aktualisieren einer vorhandenen Entität.

## <a name="example"></a>Beispiel

# <a name="c"></a>[C#](#tab/csharp)

Das folgende Beispiel zeigt eine [C#-Funktion](functions-dotnet-class-library.md) mit einem HTTP-Trigger zum Schreiben einer einzelnen Tabellenzeile.

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, ILogger log)
    {
        log.LogInformation($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Das folgende Beispiel zeigt eine Tabellenausgabebindung in einer Datei vom Typ *function.json* sowie [C#-Skriptcode](functions-reference-csharp.md), der die Bindung verwendet. Die Funktion schreibt mehrere Tabellenentitäten.

Die Datei *function.json* sieht wie folgt aus:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#configuration).

Der C#-Skriptcode sieht wie folgt aus:

```csharp
public static void Run(string input, ICollector<Person> tableBinding, ILogger log)
{
    for (int i = 1; i < 10; i++)
        {
            log.LogInformation($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

# <a name="java"></a>[Java](#tab/java)

Das folgende Beispiel zeigt eine Java-Funktion, die einen HTTP-Trigger zum Schreiben einer einzelnen Tabellenzeile verwendet.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPerson {

    @FunctionName("addPerson")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPerson", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}/{rowKey}") HttpRequestMessage<Optional<Person>> request,
            @BindingName("partitionKey") String partitionKey,
            @BindingName("rowKey") String rowKey,
            @TableOutput(name="person", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person> person,
            final ExecutionContext context) {

        Person outPerson = new Person();
        outPerson.setPartitionKey(partitionKey);
        outPerson.setRowKey(rowKey);
        outPerson.setName(request.getBody().get().getName());

        person.setValue(outPerson);

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(outPerson)
                        .build();
    }
}
```

Das folgende Beispiel zeigt eine Java-Funktion, die einen HTTP-Trigger zum Schreiben mehrerer Tabellenzeilen verwendet.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPersons {

    @FunctionName("addPersons")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPersons", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/") HttpRequestMessage<Optional<Person[]>> request,
            @TableOutput(name="person", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person[]> persons,
            final ExecutionContext context) {

        persons.setValue(request.getBody().get());

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(request.getBody().get())
                        .build();
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Das folgende Beispiel zeigt eine Tabellenausgabebindung in einer Datei vom Typ *function.json* sowie eine [JavaScript-Funktion](functions-reference-node.md), die die Bindung verwendet. Die Funktion schreibt mehrere Tabellenentitäten.

Die Datei *function.json* sieht wie folgt aus:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#configuration).

Der JavaScript-Code sieht wie folgt aus:

```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }

    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Das folgende Beispiel veranschaulicht, wie mehrere Entitäten aus einer Funktion in eine Tabelle geschrieben werden.

Bindungskonfiguration in _function.json_:

```json
{
  "bindings": [
    {
      "name": "InputData",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "TableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

PowerShell-Code in _run.ps1_:

```powershell
param($InputData, $TriggerMetadata)
  
foreach ($i in 1..10) {
    Push-OutputBinding -Name TableBinding -Value @{
        PartitionKey = 'Test'
        RowKey = "$i"
        Name = "Name $i"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Das folgende Beispiel zeigt, wie Sie die Tabellenspeicherausgabe-Bindung verwenden. Die `table`-Bindung ist in *function.json* konfiguriert, indem `name`, `tableName`, `partitionKey` und `connection` Werte zugewiesen werden:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "message",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "connection": "AzureWebJobsStorage",
      "direction": "out"
    },
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Die folgende Funktion generiert eine eindeutige UUI für den `rowKey`-Wert und speichert die Nachricht dauerhaft im Tabellenspeicher.

```python
import logging
import uuid
import json

import azure.functions as func

def main(req: func.HttpRequest, message: func.Out[str]) -> func.HttpResponse:

    rowKey = str(uuid.uuid4())

    data = {
        "Name": "Output binding message",
        "PartitionKey": "message",
        "RowKey": rowKey
    }

    message.set(json.dumps(data))

    return func.HttpResponse(f"Message created with the rowKey: {rowKey}")
```

---

## <a name="attributes-and-annotations"></a>Attribute und Anmerkungen

# <a name="c"></a>[C#](#tab/csharp)

In [C#-Klassenbibliotheken](functions-dotnet-class-library.md) verwenden Sie die [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs).

Der Konstruktor des Attributs akzeptiert den Tabellennamen. Das Attribut kann für einen `out`-Parameter oder für den Rückgabewert der Funktion verwendet werden, wie im folgenden Beispiel zu sehen:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Durch Festlegen der Eigenschaft `Connection` können Sie das zu verwendende Speicherkonto angeben, wie im folgenden Beispiel zu sehen:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Ein vollständiges Beispiel finden Sie unter [C#-Beispiel](#example).

Mit dem Attribut `StorageAccount` können Sie das Speicherkonto auf Klassen-, Methoden- oder Parameterebene angeben. Weitere Informationen finden Sie unter [Eingabe: Attribute](./functions-bindings-storage-table-input.md#attributes-and-annotations).

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Attribute werden von C#-Skript nicht unterstützt.

# <a name="java"></a>[Java](#tab/java)

Verwenden Sie in der [Java-Funktionslaufzeitbibliothek](/java/api/overview/azure/functions/runtime) die [TableOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/)-Anmerkung bei Parametern, um Werte in den Tabellenspeicher zu schreiben.

Weitere Details finden Sie in dem [Beispiel](#example).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribute werden von JavaScript nicht unterstützt.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Attribute werden von PowerShell nicht unterstützt.

# <a name="python"></a>[Python](#tab/python)

Attribute werden von Python nicht unterstützt.

---

## <a name="configuration"></a>Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `Table` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**type** | – | Muss auf `table` festgelegt sein. Diese Eigenschaft wird automatisch festgelegt, wenn Sie die Bindung im Azure Portal erstellen.|
|**direction** | – | Muss auf `out` festgelegt sein. Diese Eigenschaft wird automatisch festgelegt, wenn Sie die Bindung im Azure Portal erstellen. |
|**name** | – | Der Variablenname, der in Funktionscode zur Darstellung der Tabelle oder Entität verwendet wird. Legen Sie diesen Wert auf `$return` fest, um auf den Rückgabewert der Funktion zu verweisen.| 
|**tableName** |**TableName** | Der Name der Tabelle.| 
|**partitionKey** |**PartitionKey** | Der Partitionsschlüssel der zu schreibenden Tabellenentität. Informationen zur Verwendung dieser Eigenschaft finden Sie im Abschnitt [Verwendung](#usage).| 
|**rowKey** |**Zeilenschlüssel** | Der Zeilenschlüssel der zu schreibenden Tabellenentität. Informationen zur Verwendung dieser Eigenschaft finden Sie im Abschnitt [Verwendung](#usage).| 
|**connection** |**Connection** | Der Name einer App-Einstellung, die die Storage-Verbindungszeichenfolge für diese Bindung enthält. Falls der Name der App-Einstellung mit „AzureWebJobs“ beginnt, können Sie hier nur den Rest des Namens angeben. Wenn Sie `connection` also beispielsweise auf „MyStorage“ festlegen, sucht die Functions-Runtime nach einer App-Einstellung namens „MyStorage“. Ohne Angabe für `connection` verwendet die Functions-Laufzeit die standardmäßige Storage-Verbindungszeichenfolge aus der App-Einstellung `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Verwendung

# <a name="c"></a>[C#](#tab/csharp)

Greifen Sie mithilfe eines der Methodenparameter `ICollector<T> paramName` oder `IAsyncCollector<T> paramName` auf die Ausgabetabellenentität zu, wobei `T` die Eigenschaften `PartitionKey` und `RowKey` umfasst. Diese Eigenschaften gehen häufig mit der Implementierung von `ITableEntity` oder der Vererbung von `TableEntity` einher.

Alternativ können Sie einen `CloudTable`-Methodenparameter verwenden, um unter Verwendung des Azure Storage SDKs in die Tabelle zu schreiben. Wenn Sie versuchen, eine Bindung an `CloudTable` herzustellen, und eine Fehlermeldung erhalten, stellen Sie sicher, dass ein Verweis auf [die richtige Storage SDK-Version](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x) vorliegt.

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Greifen Sie mithilfe eines der Methodenparameter `ICollector<T> paramName` oder `IAsyncCollector<T> paramName` auf die Ausgabetabellenentität zu, wobei `T` die Eigenschaften `PartitionKey` und `RowKey` umfasst. Diese Eigenschaften gehen häufig mit der Implementierung von `ITableEntity` oder der Vererbung von `TableEntity` einher. Der Wert `paramName` wird in der Eigenschaft `name` von *function.json* angegeben.

Alternativ können Sie einen `CloudTable`-Methodenparameter verwenden, um unter Verwendung des Azure Storage SDKs in die Tabelle zu schreiben. Wenn Sie versuchen, eine Bindung an `CloudTable` herzustellen, und eine Fehlermeldung erhalten, stellen Sie sicher, dass ein Verweis auf [die richtige Storage SDK-Version](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x) vorliegt.

# <a name="java"></a>[Java](#tab/java)

Es gibt zwei Optionen für das Ausgeben einer Tabellenspeicherzeile aus einer Funktion mittels der [TableStorageOutput](/java/api/com.microsoft.azure.functions.annotation.tableoutput)-Anmerkung:

- **Rückgabewert**: Wenn Sie die Anmerkung auf die Funktion selbst anwenden, wird der Rückgabewert der Funktion als Tabellenspeicherzeile beibehalten.

- **Imperativ**: Um den Nachrichtenwert explizit festzulegen, wenden Sie die Anmerkung auf einen bestimmten Parameter des Typs [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) an, wobei `T` die Eigenschaften `PartitionKey` und `RowKey` enthält. Diese Eigenschaften gehen häufig mit der Implementierung von `ITableEntity` oder der Vererbung von `TableEntity` einher.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Greifen Sie mithilfe von `context.bindings.<name>` auf das Ausgabeereignis zu, wobei `<name>` der in der Eigenschaft `name` von *function.json* angegebene Wert ist.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Um in Tabellendaten zu schreiben, verwenden Sie das Cmdlet `Push-OutputBinding`, und legen Sie die Parameter `-Name TableBinding` und `-Value` entsprechend den Zeilendaten fest. Weitere Details finden Sie im [PowerShell-Beispiel](#example).

# <a name="python"></a>[Python](#tab/python)

Es gibt zwei Optionen für das Ausgeben einer Tabellenspeicherzeilen-Nachricht aus einer Funktion:

- **Rückgabewert**: Legen Sie die Eigenschaft `name` in *function.json* auf `$return` fest. Mit dieser Konfiguration wird der Rückgabewert der Funktion als Tabellenspeicherzeile beibehalten.

- **Imperativ**: Übergeben Sie einen Wert an die [set](/python/api/azure-functions/azure.functions.out#set-val--t-----none)-Methode des Parameters, der als [Out](/python/api/azure-functions/azure.functions.out)-Typ deklariert ist. Der an `set` übergebene Wert wird als Event Hub-Nachricht beibehalten.

---

## <a name="exceptions-and-return-codes"></a>Ausnahmen und Rückgabecodes

| Bindung | Verweis |
|---|---|
| Tabelle | [Tabellenfehlercodes](/rest/api/storageservices/fileservices/table-service-error-codes) |
| Blob, Tabelle, Warteschlange | [Speicherfehlercodes](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Tabelle, Warteschlange | [Problembehandlung](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Konzepte für Azure Functions-Trigger und -Bindungen](functions-triggers-bindings.md)
