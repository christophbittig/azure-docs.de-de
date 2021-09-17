---
title: Verwalten von Verbindungen in Azure Functions
description: Erfahren Sie, wie Sie durch die Verwendung statischer Verbindungsclients Leistungsprobleme in Azure Functions vermeiden.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/23/2021
ms.openlocfilehash: 3a7f0f707957b4b3cfd7dc66efe9d2d011d58982
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123105632"
---
# <a name="manage-connections-in-azure-functions"></a>Verwalten von Verbindungen in Azure Functions

Funktionen innerhalb einer Funktions-App nutzen Ressourcen gemeinsam. Unter diesen geteilten Ressourcen bestehen Verbindungen: HTTP-Verbindungen, Datenbankverbindungen und Verbindungen mit Diensten, wie etwa Azure Storage. Werden viele Funktionen gleichzeitig in einem Verbrauchsplan ausgeführt, sind möglicherweise nicht mehr ausreichend verfügbare Verbindungen vorhanden. In diesem Artikel erfahren Sie, wie Sie Ihre Funktionen codieren, um zu vermeiden, dass Sie mehr Verbindungen als erforderlich verwenden.

> [!NOTE]
> Die in diesem Artikel beschriebenen Verbindungsgrenzwerte gelten nur bei der Ausführung im Rahmen eines [Verbrauchsplans](consumption-plan.md). Die hier beschriebenen Techniken können jedoch bei der Ausführung im Rahmen eines beliebigen Plans von Vorteil sein.

## <a name="connection-limit"></a>Verbindungsgrenzwert

Die Anzahl der verfügbaren Verbindungen in einem Verbrauchsplan ist teilweise begrenzt, da eine Funktions-App im Rahmen dieses Plans in einer [Sandboxumgebung](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) ausgeführt wird. Eine Einschränkung, die die Sandbox Ihrem Code auferlegt, ist eine Grenze für die Anzahl der ausgehenden Verbindungen, die derzeit 600 aktive (insgesamt 1.200) Verbindungen pro Instanz beträgt. Wenn diese Grenze erreicht ist, schreibt die Functions-Runtime die folgende Meldung in die Protokolle: `Host thresholds exceeded: Connections`. Weitere Informationen finden Sie unter [Funktions-Apps: Diensteinschränkungen](functions-scale.md#service-limits).

Dieser Grenzwert gilt pro Instanz. Wenn der [Skalierungscontroller Funktions-App-Instanzen hinzufügt](event-driven-scaling.md), um mehr Anforderungen zu verarbeiten, weist jede Instanz einen unabhängigen Verbindungsgrenzwert auf. Das heißt, es gibt keinen globalen Verbindungsgrenzwert, um sie können über alle aktiven Instanzen weit mehr als 600 aktive Verbindungen verwenden.

Stellen Sie bei der Problembehandlung sicher, dass Sie Application Insights für Ihre Funktions-App aktiviert haben. Mit Application Insights können Sie Metriken für Ihre Funktions-Apps wie Ausführungen anzeigen. Weitere Informationen finden Sie unter [Anzeigen von Telemetriedaten in Application Insights](analyze-telemetry-data.md#view-telemetry-in-application-insights).  

## <a name="static-clients"></a>Statische Clients

Um zu vermeiden, dass mehr Verbindungen als nötig gehalten werden, erstellen Sie bei jedem Funktionsaufruf keine neuen Instanzen, sondern verwenden die Clientinstanzen erneut. Wir empfehlen, Clientverbindungen für jede Sprache, in der Sie Ihre Funktion erstellen, wiederzuverwenden. Beispielsweise können .NET-Clients wie [HttpClient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1&preserve-view=true) und [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient) sowie Azure Storage-Clients Verbindungen verwalten, wenn Sie einen einzelnen, statischen Client verwenden.

Es folgen einige Richtlinien, die zu beachten sind, wenn Sie einen dienstspezifischen Client in einer Azure Functions-Anwendung verwenden:

- *NEIN* Erstellen Sie keinen neuen Client bei jedem Funktionsaufruf.
- *JA* Erstellen Sie einen einzelnen, statischen Client, der von jedem Funktionsaufruf verwendet werden kann.
- *VIELLEICHT* Denken Sie über die Erstellung eines einzelnen, statischen Clients in einer gemeinsamen Hilfsprogrammklasse nach, wenn verschiedene Funktionen den gleichen Dienst verwenden.

## <a name="client-code-examples"></a>Clientcodebeispiele

Dieser Abschnitt veranschaulicht Best Practices für die Erstellung und Verwendung von Clients über den Funktionscode.

### <a name="http-requests"></a>HTTP-Anforderungen

# <a name="c"></a>[C#](#tab/csharp)
Es folgt ein Beispiel für einen C#-Funktionscode, der eine statische [HttpClient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1&preserve-view=true)-Instanz erstellt:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

Eine häufig gestellte Frage zum [HttpClient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1&preserve-view=true) in .NET lautet: „Soll ich meinen Client löschen?“. Im Allgemeinen löschen Sie Objekte, die `IDisposable` implementieren, wenn Sie sie nicht mehr verwenden. Sie löschen jedoch keinen statischen Client, da dessen Verwendung mit dem Funktionsende nicht abgeschlossen ist. Der statische Client soll für die Dauer der Anwendung gültig sein.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Da die native [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent)-Klasse bessere Optionen für die Verbindungsverwaltung bietet, sollten Sie diese anstelle von nicht nativen Methoden wie etwa dem `node-fetch`-Modul verwenden. Verbindungsparameter werden mithilfe von Optionen in der `http.agent`-Klasse konfiguriert. Die genauen Optionen, die für den HTTP-Agent verfügbar sind, finden Sie unter [new Agent(\[options\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options).

Für die globale `http.globalAgent`-Klasse, die von `http.request()` verwendet wird, sind all diese Werte auf ihre jeweiligen Standardwerte festgelegt. Die empfohlene Vorgehensweise zum Konfigurieren von Verbindungsbeschränkungen in Functions besteht darin, global eine maximale Anzahl festzulegen. Das folgende Beispiel legt die maximale Anzahl von Sockets für die Funktions-App fest:

```js
http.globalAgent.maxSockets = 200;
```

 Das folgende Beispiel erstellt eine neue HTTP-Anforderung mit einem benutzerdefinierten HTTP-Agent nur für diese Anforderung:

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
const options = { agent: httpAgent };
http.request(options, onResponseCallback);
```

---

### <a name="azure-cosmos-db-clients"></a>Azure Cosmos DB-Clients 

# <a name="c"></a>[C#](#tab/csharp)

[DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient) stellt eine Verbindung mit einer Azure Cosmos DB-Instanz her. In der Azure Cosmos DB-Dokumentation wird empfohlen, dass Sie [einen Singleton-Azure Cosmos DB-Client für die Lebensdauer der Anwendung verwenden](../cosmos-db/performance-tips.md#sdk-usage). Im folgenden Beispiel wird ein Muster dafür in einer Funktion gezeigt:

```cs
#r "Microsoft.Azure.Documents.Client"
using Microsoft.Azure.Documents.Client;

private static Lazy<DocumentClient> lazyClient = new Lazy<DocumentClient>(InitializeDocumentClient);
private static DocumentClient documentClient => lazyClient.Value;

private static DocumentClient InitializeDocumentClient()
{
    // Perform any initialization here
    var uri = new Uri("example");
    var authKey = "authKey";
    
    return new DocumentClient(uri, authKey);
}

public static async Task Run(string input)
{
    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("database", "collection");
    object document = new { Data = "example" };
    await documentClient.UpsertDocumentAsync(collectionUri, document);
    
    // Rest of function
}
```
Wenn Sie Functions v3.x verwenden, benötigen Sie einen Verweis auf Microsoft.Azure.DocumentDB.Core. Fügen Sie einen Verweis im Code hinzu:

```cs
#r "Microsoft.Azure.DocumentDB.Core"
```
Erstellen Sie außerdem eine Datei mit dem Namen „function.proj“ für Ihren Trigger, und fügen Sie den folgenden Inhalt hinzu:

```cs

<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netcoreapp3.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.Azure.DocumentDB.Core" Version="2.12.0" />
    </ItemGroup>
</Project>

```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) stellt eine Verbindung mit einer Azure Cosmos DB-Instanz her. In der Azure Cosmos DB-Dokumentation wird empfohlen, dass Sie [einen Singleton-Azure Cosmos DB-Client für die Lebensdauer der Anwendung verwenden](../cosmos-db/performance-tips.md#sdk-usage). Im folgenden Beispiel wird ein Muster dafür in einer Funktion gezeigt:

```javascript
const cosmos = require('@azure/cosmos');
const endpoint = process.env.COSMOS_API_URL;
const key = process.env.COSMOS_API_KEY;
const { CosmosClient } = cosmos;

const client = new CosmosClient({ endpoint, key });
// All function invocations also reference the same database and container.
const container = client.database("MyDatabaseName").container("MyContainerName");

module.exports = async function (context) {
    const { resources: itemArray } = await container.items.readAll().fetchAll();
    context.log(itemArray);
}
```

---

## <a name="sqlclient-connections"></a>SqlClient-Verbindungen

Funktionscode kann den .NET Framework-Datenanbieter für SQL Server ([SqlClient](/dotnet/api/system.data.sqlclient)) verwenden, um Verbindungen zu einer relationalen SQL-Datenbank herzustellen. Dies ist auch der zugrunde liegende Anbieter für Daten-Frameworks, die ADO.NET verwenden, z.B. [Entity Framework](/ef/ef6/). Im Gegensatz zu [HttpClient](/dotnet/api/system.net.http.httpclient)- und [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient)-Verbindungen implementiert ADO.NET standardmäßig das Verbindungspooling. Da jedoch noch immer nicht genügend Verbindungen verfügbar sein können, sollten Sie die Verbindungen mit der Datenbank optimieren. Weitere Informationen finden Sie unter [SQL Server-Verbindungspooling (ADO.NET)](/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Einige Daten-Frameworks, z.B. Entity Framework, rufen Verbindungszeichenfolgen üblicherweise aus dem Abschnitt **ConnectionStrings** einer Konfigurationsdatei ab. In diesem Fall müssen Sie der Sammlung **Verbindungszeichenfolgen** der Funktions-App-Einstellungen und der Datei [local.settings.json](functions-develop-local.md#local-settings-file) im lokalen Projekt explizit SQL-Datenbank-Verbindungszeichenfolgen hinzufügen. Bei der Erstellung einer Instanz von [SqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection) in Ihrem Funktionscode sollten Sie den Verbindungszeichenfolgenwert zusammen mit den anderen Verbindungen in den **Anwendungseinstellungen** speichern.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Empfehlung von statischen Clients finden Sie unter [Antimuster für ungeeignete Instanziierung](/azure/architecture/antipatterns/improper-instantiation/).

Weitere Tipps zur Leistungssteigerung von Azure Functions finden Sie unter [Optimieren der Leistung und Zuverlässigkeit von Azure Functions](functions-best-practices.md).
