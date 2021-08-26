---
title: Erstellen eines RESTful-Endpunkts
description: In diesem Tutorial wird gezeigt, wie Sie einen RESTful-Endpunkt für benutzerdefinierte Anbieter erstellen. Sie erfahren ausführlich, wie Sie Anforderungen und Antworten für die unterstützten RESTful-HTTP-Methoden behandeln.
author: jjbfour
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jobreen
ms.openlocfilehash: adb139c7a94c9c915ae1d05f67f22fe720f6c940
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114441407"
---
# <a name="author-a-restful-endpoint-for-custom-providers"></a>Erstellen eines RESTful-Endpunkts für benutzerdefinierte Anbieter

Ein benutzerdefinierter Anbieter ist ein Vertrag zwischen Azure und einem Endpunkt. Mit benutzerdefinierten Anbietern können Sie Workflows in Azure anpassen. In diesem Tutorial wird gezeigt, wie Sie einen RESTful-Endpunkt für benutzerdefinierte Anbieter erstellen. Sollten Sie noch nicht mit benutzerdefinierten Azure-Anbietern vertraut sein, sehen Sie sich die [Übersicht über benutzerdefinierte Ressourcenanbieter](overview.md) an.

> [!NOTE]
> Dieses Tutorial baut auf dem Tutorial [Einrichten von Azure-Funktionen für benutzerdefinierte Azure-Anbieter](./tutorial-custom-providers-function-setup.md) auf. Einige der Schritte in diesem Tutorial funktionieren nur, wenn in Azure Functions eine Funktions-App für die Nutzung benutzerdefinierter Anbieter eingerichtet wurde.

## <a name="work-with-custom-actions-and-custom-resources"></a>Verwenden von benutzerdefinierten Aktionen und Ressourcen

In diesem Tutorial aktualisieren Sie die Funktions-App, sodass sie als RESTful-Endpunkt für Ihren benutzerdefinierten Anbieter verwendet werden kann. Ressourcen und Aktionen in Azure werden basierend auf der grundlegenden RESTful-Spezifikation modelliert:

- **PUT**: Erstellen einer neuen Ressource
- **GET (Instanz)** : Abrufen einer vorhandenen Ressource
- **DELETE**: Entfernen einer vorhandenen Ressource
- **POST**: Auslösen einer Aktion
- **GET (Sammlung)** : Auflisten aller vorhandenen Ressourcen

 In diesem Tutorial wird Azure-Tabellenspeicher verwendet. Es kann aber auch ein anderer Datenbank- oder Speicherdienst verwendet werden.

## <a name="partition-custom-resources-in-storage"></a>Partitionieren benutzerdefinierter Ressourcen im Speicher

Da Sie einen RESTful-Dienst erstellen, müssen die erstellten Ressourcen gespeichert werden. Bei Verwendung von Azure-Tabellenspeicher müssen Partitions- und Zeilenschlüssel für Ihre Daten generiert werden. Für benutzerdefinierte Anbieter sollten Daten entsprechend partitioniert werden. Wenn eine eingehende Anforderung an den benutzerdefinierten Anbieter gesendet wird, fügt dieser den ausgehenden Anforderungen, die an den Endpunkt gesendet werden, den Header `x-ms-customproviders-requestpath` hinzu.

Das folgende Beispiel zeigt einen Header vom Typ `x-ms-customproviders-requestpath` für eine benutzerdefinierte Ressource:

```
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{myResourceType}/{myResourceName}
```

Auf der Grundlage des Headers `x-ms-customproviders-requestpath` aus dem Beispiel können Sie die Parameter *partitionKey* und *rowKey* für Ihren Speicher erstellen, wie in der folgenden Tabelle gezeigt:

Parameter | Vorlage | BESCHREIBUNG
---|---|---
*partitionKey* | `{subscriptionId}:{resourceGroupName}:{resourceProviderName}` | Der Parameter *partitionKey* gibt die Partitionierung der Daten an. In der Regel werden die Daten durch die Instanz des benutzerdefinierten Anbieters partitioniert.
*rowKey* | `{myResourceType}:{myResourceName}` | Der Parameter *rowKey* gibt den individuellen Bezeichner für die Daten an. Der Bezeichner ist in der Regel der Name der Ressource.

Sie müssen auch eine neue Klasse erstellen, um Ihre benutzerdefinierte Ressource zu modellieren. In diesem Tutorial fügen Sie Ihrer Funktions-App die folgende Klasse vom Typ **CustomResource** hinzu:

```csharp
// Custom Resource Table Entity
public class CustomResource : ITableEntity
{
    public string Data { get; set; }

    public string PartitionKey { get; set; }

    public string RowKey { get; set; }

    public DateTimeOffset? Timestamp { get; set; }

    public ETag ETag { get; set; }
}
```
**CustomResource** ist eine einfache generische Klasse, die beliebige Eingabedaten akzeptiert. Sie basiert auf **ITableEntity** (dient zum Speichern von Daten). Die Klasse **CustomResource** implementiert alle Eigenschaften der Schnittstelle **ITableEntity**: **timestamp**, **eTag**, **partitionKey**, und **rowKey**.

## <a name="support-custom-provider-restful-methods"></a>Ermöglichen der Unterstützung von RESTful-Methoden für benutzerdefinierte Anbieter

> [!NOTE]
> Wenn Sie den Code nicht direkt aus dem Tutorial kopieren, muss es sich beim Antwortinhalt um gültigen JSON-Code handeln, durch den der Header `Content-Type` auf `application/json` festgelegt wird.

Nachdem die Datenpartitionierung nun eingerichtet ist, muss als Nächstes das Gerüst für die grundlegenden CRUD- und Triggermethoden für benutzerdefinierte Ressourcen und Aktionen erstellt werden. Da benutzerdefinierte Anbieter als Proxys fungieren, muss der RESTful-Endpunkt die Anforderung und die Antwort modellieren und behandeln. In den folgenden Codeausschnitten wird die Behandlung der grundlegenden RESTful-Vorgänge gezeigt.

### <a name="trigger-a-custom-action"></a>Auslösen einer benutzerdefinierten Aktion

Bei benutzerdefinierten Anbietern wird eine benutzerdefinierte Aktion mithilfe von POST-Anforderungen ausgelöst. Eine benutzerdefinierte Aktion kann optional einen Anforderungstext akzeptieren, der eine Reihe von Eingabeparametern enthält. Die Aktion gibt dann eine Antwort zurück, die das Ergebnis der Aktion sowie den Erfolgsstatus signalisiert.

Fügen Sie Ihrer Funktions-App die folgende Methode vom Typ **TriggerCustomAction** hinzu:

```csharp
/// <summary>
/// Triggers a custom action with some side effects.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <returns>The HTTP response result of the custom action.</returns>
public static async Task<HttpResponseMessage> TriggerCustomAction(HttpRequestMessage requestMessage)
{
    var myCustomActionRequest = await requestMessage.Content.ReadAsStringAsync();

    var actionResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    actionResponse.Content = myCustomActionRequest != string.Empty ? 
        new StringContent(JObject.Parse(myCustomActionRequest).ToString(), System.Text.Encoding.UTF8, "application/json") :
        null;
    return actionResponse;
}
```

Die Methode **TriggerCustomAction** akzeptiert eine eingehende Anforderung und gibt ganz einfach eine identische Antwort mit einem Statuscode zurück.

### <a name="create-a-custom-resource"></a>Erstellen einer benutzerdefinierten Ressource

Bei benutzerdefinierten Anbietern wird eine benutzerdefinierte Ressource mithilfe von PUT-Anforderungen erstellt. Der benutzerdefinierte Anbieter akzeptiert einen JSON-Anforderungstext, der eine Reihe von Eigenschaften für die benutzerdefinierte Ressource enthält. Ressourcen in Azure liegt ein RESTful-Modell zugrunde. Eine Ressource kann unter Verwendung der gleichen Anforderungs-URL erstellt, abgerufen oder gelöscht werden.

Fügen Sie die folgende Methode vom Typ **CreateCustomResource** hinzu, um neue Ressourcen zu erstellen:

```csharp
/// <summary>
/// Creates a custom resource and saves it to table storage.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableClient">The client that allows you to interact with Azure Tables hosted in either Azure storage accounts or Azure Cosmos DB table API.</param>
/// <param name="azureResourceId">The parsed Azure resource ID.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the created custom resource.</returns>
public static async Task<HttpResponseMessage> CreateCustomResource(HttpRequestMessage requestMessage, TableClient tableClient, ResourceId azureResourceId, string partitionKey, string rowKey)
{
    // Adds the Azure top-level properties.
    var myCustomResource = JObject.Parse(await requestMessage.Content.ReadAsStringAsync());
    myCustomResource["name"] = azureResourceId.Name;
    myCustomResource["type"] = azureResourceId.FullResourceType;
    myCustomResource["id"] = azureResourceId.Id;

    // Save the resource into storage.
    var customEntity =  new CustomResource
    {
        PartitionKey = partitionKey,
        RowKey = rowKey,
        Data = myCustomResource.ToString(),
    });
    await tableClient.AddEntity(customEntity);

    var createResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    createResponse.Content = new StringContent(myCustomResource.ToString(), System.Text.Encoding.UTF8, "application/json");
    return createResponse;
}
```

Die Methode **CreateCustomResource** aktualisiert die eingehende Anforderung so, dass sie die Azure-spezifischen Felder **id**, **name** und **type** enthält. Bei diesen Feldern handelt es sich um die Eigenschaften der obersten Ebene, die von Diensten innerhalb von Azure verwendet werden. Sie ermöglichen es dem benutzerdefinierten Anbieter, mit anderen Diensten wie Azure Policy, Azure Resource Manager-Vorlagen und dem Azure-Aktivitätsprotokoll zu interagieren.

Eigenschaft | Beispiel | Beschreibung
---|---|---
**name** | {myCustomResourceName} | Der Name der benutzerdefinierten Ressource.
**type** | Microsoft.CustomProviders/resourceProviders/{resourceTypeName} | Der Namespace des Ressourcentyps.
**id** | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>{resourceTypeName}/{myCustomResourceName} | Die Ressourcen-ID.

Neben dem Hinzufügen der Eigenschaften haben Sie auch das JSON-Dokument in Azure-Tabellenspeicher gespeichert.

### <a name="retrieve-a-custom-resource"></a>Abrufen einer benutzerdefinierten Ressource

Bei benutzerdefinierten Anbietern wird eine benutzerdefinierte Ressource mithilfe von GET-Anforderungen abgerufen. Benutzerdefinierte Anbieter akzeptieren *keinen* JSON-Anforderungstext. Im Falle von GET-Anforderungen verwendet der Endpunkt den Header `x-ms-customproviders-requestpath`, um die bereits erstellte Ressource zurückzugeben.

Fügen Sie die folgende Methode vom Typ **RetrieveCustomResource** hinzu, um vorhandene Ressourcen abzurufen:

```csharp
/// <summary>
/// Retrieves a custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableClient">The client that allows you to interact with Azure Tables hosted in either Azure storage accounts or Azure Cosmos DB table API.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the existing custom resource.</returns>
public static async Task<HttpResponseMessage> RetrieveCustomResource(HttpRequestMessage requestMessage, TableClient tableClient, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var queryResult = tableClient.GetEntityAsync<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)queryResult.Result;

    var retrieveResponse = requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NotFound);

    retrieveResponse.Content = existingCustomResource != null ?
            new StringContent(existingCustomResource.Data, System.Text.Encoding.UTF8, "application/json"):
            null;
    return retrieveResponse;
}
```

In Azure basieren Ressourcen auf einem RESTful-Modell. Über die Anforderungs-URL, mit der die Ressource erstellt wurde, wird diese bei Verwendung einer GET-Anforderung auch zurückgegeben.

### <a name="remove-a-custom-resource"></a>Entfernen einer benutzerdefinierten Ressource

Bei benutzerdefinierten Anbietern wird eine benutzerdefinierte Ressource mithilfe von DELETE-Anforderungen entfernt. Benutzerdefinierte Anbieter akzeptieren *keinen* JSON-Anforderungstext. Im Falle einer DELETE-Anforderung verwendet der Endpunkt den Header `x-ms-customproviders-requestpath`, um die bereits erstellte Ressource zu löschen.

Fügen Sie die folgende Methode vom Typ **RemoveCustomResource** hinzu, um vorhandene Ressourcen zu entfernen:

```csharp
/// <summary>
/// Removes an existing custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableClient">The client that allows you to interact with Azure Tables hosted in either Azure storage accounts or Azure Cosmos DB table API.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the result of the deletion.</returns>
public static async Task<HttpResponseMessage> RemoveCustomResource(HttpRequestMessage requestMessage, TableClient tableClient, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var queryResult = tableClient.GetEntityAsync<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)queryResult.Result;

    if (existingCustomResource != null) {
        await tableClient.DeleteEntity(deleteEntity.PartitionKey, deleteEntity.RowKey);
    }

    return requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NoContent);
}
```

In Azure basieren Ressourcen auf einem RESTful-Modell. Über die Anforderungs-URL, mit der die Ressource erstellt wurde, wird diese bei Verwendung einer DELETE-Anforderung auch gelöscht.

### <a name="list-all-custom-resources"></a>Auflisten aller benutzerdefinierten Ressourcen

Bei benutzerdefinierten Anbietern können Sie mithilfe einer Sammlung von GET-Anforderungen eine Liste mit vorhandenen benutzerdefinierten Ressourcen aufzählen. Benutzerdefinierte Anbieter akzeptieren *keinen* JSON-Anforderungstext. Bei einer Sammlung von GET-Anforderungen verwendet der Endpunkt den Header `x-ms-customproviders-requestpath`, um die bereits erstellten Ressourcen aufzuzählen.

Fügen Sie die folgende Methode vom Typ **EnumerateAllCustomResources** hinzu, um die vorhandenen Ressourcen aufzuzählen:

```csharp
/// <summary>
/// Enumerates all the stored custom resources for a given type.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableClient">The client that allows you to interact with Azure Tables hosted in either Azure storage accounts or Azure Cosmos DB table API.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="resourceType">The resource type of the enumeration.</param>
/// <returns>The HTTP response containing a list of resources stored under 'value'.</returns>
public static async Task<HttpResponseMessage> EnumerateAllCustomResources(HttpRequestMessage requestMessage, TableClient tableClient, string partitionKey, string resourceType)
{
    // Generate upper bound of the query.
    var rowKeyUpperBound = new StringBuilder(resourceType);
    rowKeyUpperBound[rowKeyUpperBound.Length - 1]++;

    // Create the enumeration query.
    var queryResultsFilter = tableClient.Query<CustomResource>(filter: $"PartitionKey eq '{partitionKey}' and RowKey lt '{rowKeyUpperBound.ToString()}' and RowKey ge '{resourceType}'")
    
    var customResources = await queryResultsFilter.ToList().Select(customResource => JToken.Parse(customResource.Data));

    var enumerationResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    enumerationResponse.Content = new StringContent(new JObject(new JProperty("value", customResources)).ToString(), System.Text.Encoding.UTF8, "application/json");
    return enumerationResponse;
}
```

> [!NOTE]
> Bei „RowKey QueryComparisons.GreaterThan“ und „QueryComparisons.LessThan“ handelt es sich um Azure-Tabellensyntax zum Ausführen einer Abfrage vom Typ „startswith“ für Zeichenfolgen.

Wenn Sie alle vorhandenen Ressourcen auflisten möchten, generieren Sie eine Azure-Tabellenspeicherabfrage, die sicherstellt, dass die Ressourcen unter Ihrer Partition mit dem benutzerdefinierten Anbieter vorhanden sind. Die Abfrage prüft anschließend, ob der Zeilenschlüssel mit dem gleichen Wert vom Typ `{myResourceType}` beginnt.

## <a name="integrate-restful-operations"></a>Integrieren von RESTful-Vorgängen

Nachdem der Funktion alle RESTful-Methoden hinzugefügt wurden, aktualisieren Sie die Hauptmethode vom Typ **Run**, die die Funktionen aufruft, damit die verschiedenen REST-Anforderungen behandelt werden:

```csharp
/// <summary>
/// Entry point for the function app webhook that acts as the service behind a custom provider.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="log">The logger.</param>
/// <param name="tableClient">The client that allows you to interact with Azure Tables hosted in either Azure storage accounts or Azure Cosmos DB table API.</param>
/// <returns>The HTTP response for the custom Azure API.</returns>
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log, TableClient tableClient)
{
    // Get the unique Azure request path from request headers.
    var requestPath = req.Headers.GetValues("x-ms-customproviders-requestpath").FirstOrDefault();

    if (requestPath == null)
    {
        var missingHeaderResponse = req.CreateResponse(HttpStatusCode.BadRequest);
        missingHeaderResponse.Content = new StringContent(
            new JObject(new JProperty("error", "missing 'x-ms-customproviders-requestpath' header")).ToString(),
            System.Text.Encoding.UTF8, 
            "application/json");
    }

    log.LogInformation($"The Custom Provider Function received a request '{req.Method}' for resource '{requestPath}'.");

    // Determines if it is a collection level call or action.
    var isResourceRequest = requestPath.Split('/').Length % 2 == 1;
    var azureResourceId = isResourceRequest ? 
        ResourceId.FromString(requestPath) :
        ResourceId.FromString($"{requestPath}/");

    // Create the Partition Key and Row Key
    var partitionKey = $"{azureResourceId.SubscriptionId}:{azureResourceId.ResourceGroupName}:{azureResourceId.Parent.Name}";
    var rowKey = $"{azureResourceId.FullResourceType.Replace('/', ':')}:{azureResourceId.Name}";

    switch (req.Method)
    {
        // Action request for a custom action.
        case HttpMethod m when m == HttpMethod.Post && !isResourceRequest:
            return await TriggerCustomAction(
                requestMessage: req);

        // Enumerate request for all custom resources.
        case HttpMethod m when m == HttpMethod.Get && !isResourceRequest:
            return await EnumerateAllCustomResources(
                requestMessage: req,
                tableClient: tableClient,
                partitionKey: partitionKey,
                resourceType: rowKey);

        // Retrieve request for a custom resource.
        case HttpMethod m when m == HttpMethod.Get && isResourceRequest:
            return await RetrieveCustomResource(
                requestMessage: req,
                tableClient: tableClient,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Create request for a custom resource.
        case HttpMethod m when m == HttpMethod.Put && isResourceRequest:
            return await CreateCustomResource(
                requestMessage: req,
                tableClient: tableClient,
                azureResourceId: azureResourceId,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Remove request for a custom resource.
        case HttpMethod m when m == HttpMethod.Delete && isResourceRequest:
            return await RemoveCustomResource(
                requestMessage: req,
                tableClient: tableClient,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Invalid request received.
        default:
            return req.CreateResponse(HttpStatusCode.BadRequest);
    }
}
```

Die aktualisierte Methode vom Typ **Run** enthält nun die Eingabebindung *tableClient*, die Sie für den Azure-Tabellenspeicher hinzugefügt haben. Im ersten Teil der Methode wird der Header `x-ms-customproviders-requestpath` gelesen und die Bibliothek `Microsoft.Azure.Management.ResourceManager.Fluent` verwendet, um den Wert als Ressourcen-ID zu analysieren. Der Header `x-ms-customproviders-requestpath` wird vom benutzerdefinierten Anbieter gesendet und gibt den Pfad der eingehenden Anforderung an.

Mithilfe der analysierten Ressourcen-ID können Sie die Werte **partitionKey** und **rowKey** für die Daten generieren, um nach benutzerdefinierten Ressourcen zu suchen oder diese zu speichern.

Nachdem Sie die Methoden und Klassen hinzugefügt haben, müssen Sie die Methoden vom Typ **using** für die Funktions-App aktualisieren. Fügen Sie am Anfang der C#-Datei den folgenden Code hinzu:

```csharp
#r "Newtonsoft.Json"
#r "Microsoft.WindowsAzure.Storage"
#r "../bin/Microsoft.Azure.Management.ResourceManager.Fluent"

using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Configuration;
using System.Text;
using System.Threading;
using System.Globalization;
using System.Collections.Generic;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Azure.Data.Table;
using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Sollten Sie an einem Punkt in diesem Tutorial nicht mehr weiterkommen, finden Sie das vollständige Codebeispiel in der [Referenz zu C#-RESTful-Endpunkten für benutzerdefinierte Anbieter](./reference-custom-providers-csharp-endpoint.md). Speichern Sie nach Fertigstellung der Funktions-App die Funktions-App-URL. Sie kann verwendet werden, um die Funktions-App in späteren Tutorials auszulösen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie einen RESTful-Endpunkt erstellt, der als Azure-Endpunkt für einen benutzerdefinierten Anbieter verwendet werden kann. Informationen zum Erstellen eines benutzerdefinierten Anbieters finden Sie im Artikel [Erstellen und Verwenden eines benutzerdefinierten Anbieters](./tutorial-custom-providers-create.md).
