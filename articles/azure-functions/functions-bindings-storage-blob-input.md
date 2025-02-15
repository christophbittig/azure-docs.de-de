---
title: Azure Blob Storage-Eingabebindung für Azure Functions
description: Erfahren Sie, wie Sie Azure Blob Storage-Eingabebindungsdaten für eine Azure-Funktion bereitstellen.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 24db395c33f50fc7638ee00e9406db3709db5366
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130004006"
---
# <a name="azure-blob-storage-input-binding-for-azure-functions"></a>Azure Blob Storage-Eingabebindung für Azure Functions

Die Eingabebindung ermöglicht Ihnen das Lesen von Blob Storage-Daten als Eingabe für eine Azure-Funktion.

Informationen zu Setup- und Konfigurationsdetails finden Sie in der [Übersicht](./functions-bindings-storage-blob.md).

## <a name="example"></a>Beispiel

# <a name="c"></a>[C#](#tab/csharp)

Das folgende Beispiel enthält eine [C#-Funktion](functions-dotnet-class-library.md), bei der ein Warteschlangentrigger und eine Eingabeblobbindung verwendet werden. Die Warteschlangennachricht enthält den Namen des Blobs, und die Funktion protokolliert die Größe des Blobs.

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

<!--Same example for input and output. -->

Das folgende Beispiel zeigt Blobeingabe- und Blobausgabebindungen in einer Datei vom Typ *function.json* sowie Code vom Typ [C#-Skript (.csx)](functions-reference-csharp.md), in dem diese Bindungen verwendet werden. Die Funktion erstellt eine Kopie eines Textblobs. Sie wird durch eine Warteschlangennachricht ausgelöst, die den Namen des zu kopierenden Blobs enthält. Der Name des neuen Blobs lautet *{Name des Originalblobs}-Copy*.

In der Datei *function.json* wird die Metadateneigenschaft `queueTrigger` verwendet, um den Blobnamen in den Eigenschaften vom Typ `path` anzugeben:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#configuration).

Der C#-Skriptcode sieht wie folgt aus:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="java"></a>[Java](#tab/java)

Dieser Abschnitt enthält folgende Beispiele:

* [HTTP-Trigger: Suchen des Blobnamens in einer Abfragezeichenfolge](#http-trigger-look-up-blob-name-from-query-string)
* [Warteschlangentrigger: Empfangen des Blobnamens aus einer Warteschlangennachricht](#queue-trigger-receive-blob-name-from-queue-message)

#### <a name="http-trigger-look-up-blob-name-from-query-string"></a>HTTP-Trigger: Suchen des Blobnamens in einer Abfragezeichenfolge

 Das folgende Beispiel zeigt eine Java-Funktion, die mithilfe der Anmerkung `HttpTrigger` einen Parameter mit dem Namen einer Datei in einem Blob Storage-Container empfängt. Die Anmerkung `BlobInput` liest dann die Datei und übergibt ihren Inhalt als `byte[]` an die Funktion.

```java
  @FunctionName("getBlobSizeHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage blobSize(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    final ExecutionContext context) {
      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-receive-blob-name-from-queue-message"></a>Warteschlangentrigger: Empfangen des Blobnamens aus einer Warteschlangennachricht

 Das folgende Beispiel zeigt eine Java-Funktion, die mithilfe der `QueueTrigger`-Anmerkung eine Nachricht mit dem Namen einer Datei in einem Blob Storage-Container empfängt. Die Anmerkung `BlobInput` liest dann die Datei und übergibt ihren Inhalt als `byte[]` an die Funktion.

```java
  @FunctionName("getBlobSize")
  @StorageAccount("Storage_Account_Connection_String")
  public void blobSize(
    @QueueTrigger(
      name = "filename", 
      queueName = "myqueue-items-sample") 
    String filename,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{queueTrigger}") 
    byte[] content,
    final ExecutionContext context) {
      context.getLogger().info("The size of \"" + filename + "\" is: " + content.length + " bytes");
  }
```

Verwenden Sie die `@BlobInput`-Anmerkung in der [Laufzeitbibliothek für Java-Funktionen](/java/api/overview/azure/functions/runtime) für Parameter, deren Wert aus einem Blob empfangen wird.  Diese Anmerkung kann mit nativen Java-Typen, POJOs oder Werten mit `Optional<T>`, die NULL-Werte annehmen können, verwendet werden.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

Das folgende Beispiel zeigt Blobeingabe- und -ausgabebindungen in einer Datei *function.json* sowie [JavaScript-Skriptcode](functions-reference-node.md), der diese Bindungen verwendet. Die Funktion erstellt eine Kopie eines Blobs. Sie wird durch eine Warteschlangennachricht ausgelöst, die den Namen des zu kopierenden Blobs enthält. Der Name des neuen Blobs lautet *{Name des Originalblobs}-Copy*.

In der Datei *function.json* wird die Metadateneigenschaft `queueTrigger` verwendet, um den Blobnamen in den Eigenschaften vom Typ `path` anzugeben:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#configuration).

Der JavaScript-Code sieht wie folgt aus:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Das folgende Beispiel zeigt eine in der Datei _function.json_ definierte Blobeingabebindung, die die eingehenden Blobdaten für die [PowerShell](functions-reference-powershell.md)-Funktion verfügbar macht.

Die Konfiguration in der JSON-Datei lautet wie folgt:

```json
{
  "bindings": [
    {
      "name": "InputBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "source/{name}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

Der Funktionscode lautet wie folgt:

```powershell
# Input bindings are passed in via param block.
param([byte[]] $InputBlob, $TriggerMetadata)

Write-Host "PowerShell Blob trigger: Name: $($TriggerMetadata.Name) Size: $($InputBlob.Length) bytes"
```

# <a name="python"></a>[Python](#tab/python)

<!--Same example for input and output. -->

Das folgende Beispiel zeigt Blobeingabe- und -ausgabebindungen in einer Datei *function.json* sowie [Python-Code](functions-reference-python.md), der die Bindungen verwendet. Die Funktion erstellt eine Kopie eines Blobs. Sie wird durch eine Warteschlangennachricht ausgelöst, die den Namen des zu kopierenden Blobs enthält. Der Name des neuen Blobs lautet *{Name des Originalblobs}-Copy*.

In der Datei *function.json* wird die Metadateneigenschaft `queueTrigger` verwendet, um den Blobnamen in den Eigenschaften vom Typ `path` anzugeben:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "queuemsg",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "inputblob",
      "type": "blob",
      "dataType": "binary",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#configuration).

Die `dataType`-Eigenschaft legt fest, welche Bindung verwendet wird. Die folgenden Werte stehen zur Unterstützung unterschiedlicher Bindungsstrategien zur Verfügung:

| Bindungswert | Standard | BESCHREIBUNG | Beispiel |
| --- | --- | --- | --- |
| `string` | N | Verwendet eine generische Bindung und wandelt den Eingabetyp in `string` um | `def main(input: str)` |
| `binary` | N | Verwendet eine generische Bindung und wandelt den Eingabetyp in ein `bytes`-Python-Objekt um | `def main(input: bytes)` |

Wenn die `dataType`-Eigenschaft nicht in „function.json“ definiert ist, ist der Standardwert `string`.

Dies ist der Python-Code:

```python
import logging
import azure.functions as func


# The input binding field inputblob can either be 'bytes' or 'str' depends
# on dataType in function.json, 'binary' or 'string'.
def main(queuemsg: func.QueueMessage, inputblob: bytes) -> bytes:
    logging.info(f'Python Queue trigger function processed {len(inputblob)} bytes')
    return inputblob
```

---

## <a name="attributes-and-annotations"></a>Attribute und Anmerkungen

# <a name="c"></a>[C#](#tab/csharp)

Verwenden Sie in [C#-Klassenbibliotheken](functions-dotnet-class-library.md) das Attribut [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Der Konstruktor des Attributs akzeptiert den Pfad des Blobs sowie einen Parameter vom Typ `FileAccess`, der angibt, ob es sich um einen Lesevorgang oder um einen Schreibvorgang handelt. Beispiel:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}

```

Durch Festlegen der Eigenschaft `Connection` können Sie das zu verwendende Speicherkonto angeben, wie im folgenden Beispiel zu sehen:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read, Connection = "StorageConnectionAppSetting")] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

Mit dem Attribut `StorageAccount` können Sie das Speicherkonto auf Klassen-, Methoden- oder Parameterebene angeben. Weitere Informationen finden Sie unter [Trigger: Attribute und Anmerkungen](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations).

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Attribute werden von C#-Skript nicht unterstützt.

# <a name="java"></a>[Java](#tab/java)

Das `@BlobInput`-Attribut gewährt Ihnen Zugriff auf das Blob, das die Funktion ausgelöst hat. Wenn Sie ein Bytearray mit dem Attribut verwenden, legen Sie `dataType` auf `binary` fest. Weitere Detailinformationen finden Sie im [Eingabebeispiel](#example).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribute werden von JavaScript nicht unterstützt.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Attribute werden von PowerShell nicht unterstützt.

# <a name="python"></a>[Python](#tab/python)

Attribute werden von Python nicht unterstützt.

---

## <a name="configuration"></a>Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `Blob` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**type** | – | Muss auf `blob` festgelegt sein. |
|**direction** | – | Muss auf `in` festgelegt sein. Ausnahmen sind im Abschnitt [Verwendung](#usage) angegeben. |
|**name** | – | Der Name der Variablen, die das Blob im Funktionscode darstellt.|
|**path** |**BlobPath** | Der Pfad des Blobs. |
|**connection** |**Connection**| Der Name einer App-Einstellung oder -Einstellungssammlung, die angibt, wie eine Verbindung mit Azure Blobs hergestellt wird. Siehe [Verbindungen](#connections).|
|**dataType**| – | Gibt für dynamisch typisierte Sprachen den zugrunde liegenden Datentyp an. Mögliche Werte sind `string`, `binary` oder `stream`. Weitere Details finden Sie in den [Konzepten für Trigger und Bindungen](functions-triggers-bindings.md?tabs=python#trigger-and-binding-definitions). |
|– | **zugreifen** | Gibt an, ob Sie einen Lesevorgang oder einen Schreibvorgang ausführen möchten. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

[!INCLUDE [functions-storage-blob-connections](../../includes/functions-storage-blob-connections.md)]

## <a name="usage"></a>Verwendung

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="java"></a>[Java](#tab/java)

Das `@BlobInput`-Attribut gewährt Ihnen Zugriff auf das Blob, das die Funktion ausgelöst hat. Wenn Sie ein Bytearray mit dem Attribut verwenden, legen Sie `dataType` auf `binary` fest. Weitere Detailinformationen finden Sie im [Eingabebeispiel](#example).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Greifen Sie über `context.bindings.<NAME>` auf Blobdaten zu, wobei `<NAME>` mit dem in *function.json* definierten Wert übereinstimmt.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Greifen Sie auf die Blobdaten über einen Parameter zu, der mit dem Namen übereinstimmt, der durch den name-Parameter der Bindung in der Datei _function.json_ festgelegt ist.

# <a name="python"></a>[Python](#tab/python)

Greifen Sie über den als [InputStream](/python/api/azure-functions/azure.functions.inputstream) typisierten Parameter auf Blob-Daten zu. Weitere Detailinformationen finden Sie im [Eingabebeispiel](#example).

---

## <a name="next-steps"></a>Nächste Schritte

- [Ausführen einer Funktion, wenn sich Blob Storage-Daten ändern](./functions-bindings-storage-blob-trigger.md)
- [Schreiben von Blob Storage-Daten aus einer Funktion](./functions-bindings-storage-blob-output.md)
