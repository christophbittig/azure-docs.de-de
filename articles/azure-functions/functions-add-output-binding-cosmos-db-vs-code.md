---
title: Verbinden von Azure Functions mit Azure Cosmos DB mithilfe von Visual Studio Code
description: Erfahren Sie, wie Sie Azure Functions mit einem Azure Cosmos DB-Konto verbinden können, indem Sie eine Ausgabebindung zu Ihrem Visual Studio Code-Projekt hinzufügen.
author: ThomasWeiss
ms.date: 08/17/2021
ms.topic: quickstart
ms.author: thweiss
zone_pivot_groups: programming-languages-set-functions-temp
ms.openlocfilehash: 9fbc67f14d6d67393b62129f2b4e9269fb6c6f52
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446249"
---
# <a name="connect-azure-functions-to-azure-cosmos-db-using-visual-studio-code"></a>Verbinden von Azure Functions mit Azure Cosmos DB mithilfe von Visual Studio Code

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

In diesem Artikel wird gezeigt, wie Sie Visual Studio Code verwenden, um [Azure Cosmos DB](../cosmos-db/introduction.md) mit der Funktion zu verbinden, die Sie im vorherigen Schnellstartartikel erstellt haben. Die Ausgabebindung, die Sie dieser Funktion hinzufügen, schreibt Daten aus der HTTP-Anforderung in ein JSON-Dokument, das in einem Azure Cosmos DB-Container gespeichert ist. 

::: zone pivot="programming-language-csharp"
Bevor Sie beginnen, müssen Sie die Schritte unter [Schnellstart: Erstellen einer C#-Funktion in Azure mit Visual Studio Code](create-first-function-vs-code-csharp.md) ausführen. Falls Sie die Ressourcen am Ende dieses Artikels bereits bereinigt haben, sollten Sie die Schritte erneut ausführen, um die Funktions-App und die zugehörigen Ressourcen neu zu erstellen.
::: zone-end
::: zone pivot="programming-language-javascript"  
Bevor Sie beginnen, müssen Sie die Schritte unter [Schnellstart: Erstellen einer Java-Funktion in Azure mit Visual Studio Code](create-first-function-vs-code-node.md) ausführen. Falls Sie die Ressourcen am Ende dieses Artikels bereits bereinigt haben, sollten Sie die Schritte erneut ausführen, um die Funktions-App und die zugehörigen Ressourcen neu zu erstellen.  
::: zone-end

## <a name="configure-your-environment"></a>Konfigurieren Ihrer Umgebung

Installieren Sie die [Erweiterung „Azure-Datenbanken“](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb) für Visual Studio Code, bevor Sie beginnen.

## <a name="create-your-azure-cosmos-db-account"></a>Erstellen des Azure Cosmos DB-Kontos

> [!IMPORTANT]
> [Azure Cosmos DB serverlos](../cosmos-db/serverless.md) ist jetzt allgemein verfügbar. Dieser verbrauchsbasierte Modus macht Azure Cosmos DB zu einer starken Option für serverlose Workloads. Wenn Sie Azure Cosmos DB im serverlosen Modus verwenden möchten, wählen Sie beim Erstellen Ihres Kontos **Serverlos** als **Kapazitätsmodus** aus.

1. Klicken Sie in Visual Studio Code mit der rechten Maustaste auf das Azure-Abonnement, in dem Sie im [vorherigen Artikel](./create-first-function-vs-code-csharp.md) Ihre Funktions-App erstellt haben, und wählen Sie **Server erstellen...** aus.

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/create-account.png" alt-text="Erstellen eines neuen Azure Cosmos DB-Kontos über Visual Studio Code" border="true":::

1. Geben Sie nach entsprechender Aufforderung Folgendes ein:

    + **Select an Azure Database Server** (Wählen Sie einen Azure-Datenbankserver aus.): Wählen Sie `Core (SQL)` aus, um eine Dokumentdatenbank zu erstellen, die Sie mit einer SQL-Syntax abfragen können. [Erfahren Sie mehr über die SQL-API von Azure Cosmos DB](../cosmos-db/introduction.md).

    + **Account name** (Kontoname): Geben Sie einen eindeutigen Namen ein, der Ihr Azure Cosmos DB-Konto identifiziert. Der Kontoname darf nur Kleinbuchstaben, Ziffern und Bindestriche (-) enthalten und muss zwischen 3 und 31 Zeichen lang sein.

    + **Select a capacity model** (Wählen Sie ein Kapazitätsmodell aus.): Wählen Sie **Serverlos** aus, um ein Konto im Modus [Serverlos](../cosmos-db/serverless.md) zu erstellen. Wählen Sie **Bereitgestellter Durchsatz** aus, um ein Konto im Modus [Bereitgestellter Durchsatz](../cosmos-db/set-throughput.md) zu erstellen. Es wird empfohlen, **Serverlos** auszuwählen, wenn Sie erste Schritte mit Azure Cosmos DB ausführen.

    + **Select a resource group for new resources** (Wählen Sie eine Ressourcengruppe für neue Ressourcen aus.): Wählen Sie die Ressourcengruppe aus, in der Sie im [vorherigen Artikel](./create-first-function-vs-code-csharp.md) Ihre Funktions-App erstellt haben.

    + **Select a location for new resources** (Wählen Sie einen Standort für neue Ressourcen aus.): Wählen Sie einen geografischen Standort aus, an dem Ihr Azure Cosmos DB-Konto gehostet werden soll. Verwenden Sie einen Standort, der Ihnen oder Ihren Benutzern am nächsten liegt, um einen schnellen Zugriff auf die Daten zu ermöglichen.

## <a name="create-an-azure-cosmos-db-database-and-container"></a>Erstellen einer Datenbank und eines Containers in Azure Cosmos DB

1. Klicken Sie nach der Erstellung Ihres neuen Azure Cosmos DB-Kontos mit der rechten Maustaste auf den Namen, und wählen Sie **Datenbank erstellen...** aus.

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/create-database.png" alt-text="Erstellen einer neuen Azure Cosmos DB-Datenbank über Visual Studio Code" border="true":::

1. Geben Sie bei entsprechender Aufforderung `my-database` als **Datenbanknamen** ein.

1. Klicken Sie nach der Erstellung der Datenbank mit der rechten Maustaste auf ihren Namen, und wählen Sie **Sammlung erstellen...** aus.

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/create-container.png" alt-text="Erstellen eines neuen Azure Cosmos DB-Containers über Visual Studio Code" border="true":::

1. Geben Sie nach entsprechender Aufforderung Folgendes ein:

    + **Enter an id for your Collection** (Geben Sie eine ID für Ihre Sammlung ein.): `my-container`

    + **Enter the [partition key](../cosmos-db/partitioning-overview.md) for the collection** (Geben Sie den Partitionsschlüssel für die Sammlung ein.): `id`

## <a name="update-your-function-app-settings"></a>Aktualisieren der Einstellungen Ihrer Funktions-App

Im [vorherigen Schnellstartartikel](./create-first-function-vs-code-csharp.md) haben Sie eine Funktions-App in Azure erstellt. In diesem Artikel aktualisieren Sie Ihre Funktions-App, um JSON-Dokumente in den Azure Cosmos DB-Container zu schreiben, den Sie zuvor erstellt haben. Zum Herstellen einer Verbindung mit Ihrem Azure Cosmos DB-Konto müssen Sie die zugehörige Verbindungszeichenfolge den App-Einstellungen hinzufügen. Anschließend laden Sie die neue Einstellung in Ihre Datei „local.settings.js“ herunter, damit Sie bei der lokalen Ausführung eine Verbindung mit Ihrem Azure Cosmos DB-Konto herstellen können.

1. Klicken Sie in Visual Studio Code mit der rechten Maustaste auf das Azure Cosmos DB-Konto, und wählen Sie **Verbindungszeichenfolge kopieren** aus.

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/copy-connection-string.png" alt-text="Kopieren der Azure Cosmos DB-Verbindungszeichenfolge" border="true":::

1. Drücken Sie <kbd>F1</kbd>, um die Befehlspalette zu öffnen. Suchen Sie dann den Befehl `Azure Functions: Add New Setting...`, und führen Sie ihn aus.

1. Wählen Sie die im vorherigen Artikel erstellte Funktions-App aus. Geben Sie nach entsprechender Aufforderung Folgendes ein:

    + **Eingeben des Namens der neuen App-Einstellung**: Geben Sie`CosmosDbConnectionString` ein.

    + **Eingeben des Wert für „CosmosDbConnectionString“** : Fügen Sie die Verbindungszeichenfolge Ihres Azure Cosmos DB-Kontos ein, die zuvor kopiert haben.

1. Drücken Sie erneut <kbd>F1</kbd>, um die Befehlspalette zu öffnen. Suchen Sie dann den Befehl `Azure Functions: Download Remote Settings...`, und führen Sie ihn aus. 

1. Wählen Sie die im vorherigen Artikel erstellte Funktions-App aus. Wählen Sie **Ja, alle** aus, um die vorhandenen lokalen Einstellungen zu überschreiben. 

## <a name="register-binding-extensions"></a>Registrieren von Bindungserweiterungen

Da Sie eine Azure Cosmos DB-Ausgabebindung verwenden, müssen Sie vor dem Ausführen des Projekts die entsprechende Bindungserweiterung installieren. 

::: zone pivot="programming-language-csharp"

Mit Ausnahme von HTTP- und Timertriggern werden Bindungen als Erweiterungspakete implementiert. Führen Sie den folgenden [dotnet add package](/dotnet/core/tools/dotnet-add-package)-Befehl im Terminalfenster aus, um Ihrem Projekt das Storage-Erweiterungspaket hinzuzufügen.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.CosmosDB
```

::: zone-end

::: zone pivot="programming-language-javascript"

Ihr Projekt wurde für die Verwendung von [Erweiterungsbündeln](functions-bindings-register.md#extension-bundles) konfiguriert, wodurch automatisch ein vordefinierter Satz von Erweiterungspaketen installiert wird. 

Die Nutzung von Erweiterungspaketen ist in der Datei „host.json“ im Stammverzeichnis des Projekts aktiviert. Dies sieht wie folgt aus:

:::code language="json" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/host.json":::

::: zone-end

Jetzt können Sie die Azure Cosmos DB-Ausgabebindung Ihrem Projekt hinzufügen.

## <a name="add-an-output-binding"></a>Hinzufügen einer Ausgabebindung

In Functions muss für jeden Typ von Bindung eine `direction`, ein `type` und ein eindeutiger `name` in der Datei „function.json“ definiert werden. Wie Sie diese Attribute definieren, hängt von der Sprache der Funktions-App ab.

::: zone pivot="programming-language-csharp"

In einem C#-Klassenbibliotheksprojekt werden die Bindungen als Bindungsattribute der Funktionsmethode definiert. Die von Functions benötigte Datei *function.json* wird basierend auf diesen Attributen automatisch generiert.

Öffnen Sie die Projektdatei *HttpExample.cs*, und fügen Sie der Definition der Methode `Run` den folgenden Parameter hinzu:

```csharp
[CosmosDB(
    databaseName: "my-database",
    collectionName: "my-container",
    ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
```

Der `documentsOut`-Parameter ist ein `IAsyncCollector<T>`-Typ, der eine Sammlung von JSON-Dokumenten darstellt, die in Ihren Azure Cosmos DB-Container geschrieben werden, wenn die Funktion abgeschlossen ist. Bestimmte Attribute geben den Namen des Containers und den Namen seiner übergeordneten Datenbank an. Die Verbindungszeichenfolge für Ihr Azure Cosmos DB-Konto wird durch `ConnectionStringSettingAttribute` festgelegt.

Die Run-Methodendefinition sollte nun wie folgt aussehen:  

```csharp
[FunctionName("HttpExample")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    [CosmosDB(
        databaseName: "my-database",
        collectionName: "my-container",
        ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
    ILogger log)
```

::: zone-end

::: zone pivot="programming-language-javascript"

Bindungsattribute werden direkt in der Datei „function.json“ definiert. Abhängig vom Bindungstyp sind möglicherweise zusätzliche Eigenschaften erforderlich. Die [Azure Cosmos DB-Ausgabekonfiguration](./functions-bindings-cosmosdb-v2-output.md#configuration) beschreibt die für eine Azure Cosmos DB-Ausgabebindung erforderlichen Felder. Mit der Erweiterung können Bindungen einfach in der Datei „function.json“ hinzugefügt werden. 

Klicken Sie zum Erstellen einer Bindung im Ordner „HttpTrigger“ mit der rechten Maustaste (STRG+Klicken unter macOS) auf die Datei `function.json`, und wählen Sie **Bindung hinzufügen...** aus. Befolgen Sie die Anweisungen, um die folgenden Bindungseigenschaften für die neue Bindung zu definieren:

| Prompt | Wert | BESCHREIBUNG |
| -------- | ----- | ----------- |
| **Select binding direction** (Wählen Sie die Bindungsrichtung aus) | `out` | Die Bindung ist eine Ausgabebindung. |
| **Bindung mit ausgehender Richtung auswählen** | `Azure Cosmos DB` | Die Bindung ist eine Azure Cosmos DB Bindung. |
| **Der Name, der zum Identifizieren dieser Bindung in Ihrem Code verwendet wird** | `outputDocument` | Name, der den Bindungsparameter identifiziert, auf den in Ihrem Code verwiesen wird. |
| **Die Cosmos DB-Datenbank, in die die Daten geschrieben werden** | `my-database` | Der Name der Azure Cosmos DB-Datenbank, die den Zielcontainer enthält. |
| **Datenbanksammlung, in die Daten geschrieben werden** | `my-container` | Der Name des Azure Cosmos DB-Containers, in den die JSON-Dokumente geschrieben werden. |
| **If true, creates the Cosmos DB database and collection** (Erstellt die Cosmos DB-Datenbank und -Sammlung, falls zutreffend) | `false` | Die Zieldatenbank und der Zielcontainer sind bereits vorhanden. |
| **Select setting from "local.setting.json"** (Wählen Sie eine Einstellung aus „local.setting.json“ aus) | `CosmosDbConnectionString` | Der Name einer Anwendungseinstellung, die die Verbindungszeichenfolge für das Azure Cosmos DB-Konto enthält. |
| **Partitionsschlüssel (optional)** | *nicht ausfüllen* | Nur erforderlich, wenn die Ausgabebindung den Container erstellt. |
| **Sammlungsdurchsatz (optional)** | *nicht ausfüllen* | Nur erforderlich, wenn die Ausgabebindung den Container erstellt. |

Eine Bindung wird dem Array `bindings` in der Datei „function.json“ hinzugefügt, die wie folgt aussehen sollte:

```json
{
    "type": "cosmosDB",
    "direction": "out",
    "name": "outputDocument",
    "databaseName": "my-database",
    "collectionName": "my-container",
    "createIfNotExists": "false",
    "connectionStringSetting": "CosmosDbConnectionString"
}
```

::: zone-end

## <a name="add-code-that-uses-the-output-binding"></a>Hinzufügen von Code, der die Ausgabebindung verwendet

::: zone pivot="programming-language-csharp"  

Fügen Sie Code hinzu, der das Ausgabebindungsobjekt `documentsOut` verwendet, um ein JSON-Dokument zu erstellen. Fügen Sie diesen Code vor der Rückgabe der Methode hinzu.

```csharp
if (!string.IsNullOrEmpty(name))
{
    // Add a JSON document to the output container.
    await documentsOut.AddAsync(new
    {
        // create a random ID
        id = System.Guid.NewGuid().ToString(),
        name = name
    });
}
```

Die Funktion sollte nun wie folgt aussehen:

```csharp
[FunctionName("HttpExample")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    [CosmosDB(
        databaseName: "my-database",
        collectionName: "my-container",
        ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
    ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    if (!string.IsNullOrEmpty(name))
    {
        // Add a JSON document to the output container.
        await documentsOut.AddAsync(new
        {
            // create a random ID
            id = System.Guid.NewGuid().ToString(),
            name = name
        });
    }

    string responseMessage = string.IsNullOrEmpty(name)
        ? "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response."
        : $"Hello, {name}. This HTTP triggered function executed successfully.";

    return new OkObjectResult(responseMessage);
}
```

::: zone-end

::: zone pivot="programming-language-javascript"  

Fügen Sie Code hinzu, der das Ausgabebindungsobjekt `outputDocument` für `context.bindings` verwendet, um ein JSON-Dokument zu erstellen. Fügen Sie diesen Code vor der `context.res`-Anweisung hinzu.

```javascript
if (name) {
    context.bindings.outputDocument = JSON.stringify({
        // create a random ID
        id: new Date().toISOString() + Math.random().toString().substr(2,8),
        name: name
    });
}
```

Die Funktion sollte nun wie folgt aussehen:

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    if (name) {
        context.bindings.outputDocument = JSON.stringify({
            // create a random ID
            id: new Date().toISOString() + Math.random().toString().substr(2,8),
            name: name
        });
    }

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };
}
```

::: zone-end  

## <a name="run-the-function-locally"></a>Lokales Ausführen der Funktion

1. Drücken Sie wie im vorherigen Artikel die <kbd>F5</kbd>-TASTE, um das Funktions-App-Projekt und die Core Tools zu starten. 

1. Navigieren Sie bei ausgeführten Core Tools zum Bereich **Azure: Functions**. Erweitern Sie unter **Functions** die Option **Lokales Projekt** > **Functions**. Klicken Sie mit der rechten Maustaste (unter macOS STRG+Mausklick) auf die Funktion `HttpExample`, und wählen Sie **Funktion jetzt ausführen...** aus.

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="„Funktion jetzt ausführen“ in Visual Studio Code":::

1. Unter **Enter request body** (Anforderungstext eingeben) wird `{ "name": "Azure" }` als Wert für den Text der Anforderungsnachricht angezeigt. Drücken Sie die EINGABETASTE, um diese Anforderungsnachricht an Ihre Funktion zu senden.  
 
1. Drücken Sie nach der Rückgabe einer Antwort <kbd>STRG+C</kbd>, um Core Tools zu beenden.

### <a name="verify-that-a-json-document-has-been-created"></a>Überprüfen, ob ein JSON-Dokument erstellt wurde

1. Wechseln Sie im Azure-Portal wieder zu Ihrem Azure Cosmos DB-Konto, und wählen Sie **Daten-Explorer** aus.

1. Erweitern Sie die Datenbank und den Container, und wählen Sie die Option **Elemente** aus, um die in Ihrem Container erstellten Dokumente aufzulisten.

1. Überprüfen Sie, ob ein neues JSON-Dokument von der Ausgabebindung erstellt wurde.

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/verify-output.png" alt-text="Überprüfen, ob im Azure Cosmos DB-Container ein neues Dokument erstellt wurde" border="true":::

## <a name="redeploy-and-verify-the-updated-app"></a>Erneutes Bereitstellen und Überprüfen der aktualisierten App

1. Drücken Sie in Visual Studio Code die F1-Taste, um die Befehlspalette zu öffnen. Suchen Sie in der Befehlspalette den Befehl `Azure Functions: Deploy to function app...`, und wählen Sie ihn aus.

1. Wählen Sie die im ersten Artikel erstellte Funktions-App aus. Da Sie das Projekt für die gleiche App erneut bereitstellen, wählen Sie **Bereitstellen** aus, um die Warnung zum Überschreiben von Dateien zu schließen.

1. Nach Abschluss des Bereitstellungsvorgangs können Sie das Feature **Funktion jetzt ausführen...** erneut verwenden, um die Funktion in Azure auszulösen.

1. [Überprüfen Sie erneut die Dokumente, die im Azure Cosmos DB Container erstellt wurden](#verify-that-a-json-document-has-been-created), um sicherzustellen, dass die Ausgabebindung erneut ein neues JSON-Dokument generiert.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In Azure wird die Bezeichnung *Ressourcen* für Funktions-Apps, Funktionen, Speicherkonten usw. verwendet. Sie werden in *Ressourcengruppen* zusammengefasst, und sämtliche Inhalte einer Gruppe können durch das Löschen der Gruppe gelöscht werden.

Im Rahmen dieser Schnellstartanleitungen haben Sie Ressourcen erstellt. Für diese Ressourcen fallen je nach [Kontostatus](https://azure.microsoft.com/account/) und [Dienstpreisen](https://azure.microsoft.com/pricing/) unter Umständen Kosten an. Nicht mehr benötigte Ressourcen können wie folgt gelöscht werden:

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

## <a name="next-steps"></a>Nächste Schritte

Sie haben Ihre durch HTTP ausgelöste Funktion so aktualisiert, dass sie JSON-Dokumente in einen Azure Cosmos DB-Container schreibt. Nun können Sie sich ausführlicher über die Entwicklung von Funktionen mit Visual Studio Code informieren:

+ [Entwickeln von Azure Functions mithilfe von Visual Studio Code](functions-develop-vs-code.md)

+ [Konzepte für Azure Functions-Trigger und -Bindungen](functions-triggers-bindings.md)
::: zone pivot="programming-language-csharp"  
+ [Beispiele für vollständige Functions-Projekte in C#](/samples/browse/?products=azure-functions&languages=csharp).

+ [C#-Entwicklerreferenz zu Azure Functions](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Beispiele für vollständige Functions-Projekte in JavaScript](/samples/browse/?products=azure-functions&languages=javascript).

+ [JavaScript-Entwicklerhandbuch für Azure Functions](functions-reference-node.md)  
::: zone-end  