---
title: Azure Cosmos DB-Bindungen für Functions 2.x oder höher
description: Grundlegendes zur Verwendung von Azure Cosmos DB-Triggern und -Bindungen in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/01/2021
ms.author: cshoe
ms.openlocfilehash: 87318f887078a07783ec68af4fb6819f227eb38c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130248524"
---
# <a name="azure-cosmos-db-trigger-and-bindings-for-azure-functions-2x-and-higher-overview"></a>Übersicht über Azure Cosmos DB-Trigger und -Bindungen für Azure Functions 2.x oder höher

> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version der Azure Functions-Runtime aus: "]
> * [Version 1](functions-bindings-cosmosdb.md)
> * [Version 2 oder höher](functions-bindings-cosmosdb-v2.md)

In dieser Gruppe von Artikeln wird die Verwendung von [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md)-Bindungen in Azure Functions 2.x oder höher erläutert. Azure Functions unterstützt Trigger sowie Ein- und Ausgabebindungen für Azure Cosmos DB.

| Aktion | type |
|---------|---------|
| Ausführen einer Funktion, wenn ein Azure Cosmos DB-Dokument erstellt oder geändert wird | [Trigger](./functions-bindings-cosmosdb-v2-trigger.md) |
| Lesen eines Azure Cosmos DB-Dokuments | [Eingabebindung](./functions-bindings-cosmosdb-v2-input.md) |
| Speichern der Änderungen an einem Azure Cosmos DB-Dokument  |[Ausgabebindung](./functions-bindings-cosmosdb-v2-output.md) |

> [!NOTE]
> Dieser Verweis gilt für [Azure Functions Version 2.x oder höher](functions-versions.md).  Informationen zur Verwendung dieser Bindungen in Functions 1.x finden Sie unter [Azure Cosmos DB-Bindungen für Azure Functions](functions-bindings-cosmosdb.md).
>
> Diese Bindung hatte ursprünglich die Bezeichnung „DocumentDB“. In der Functions-Version 2.x oder höher wurden der Trigger, die Bindungen und das Paket jeweils mit dem Namen „Cosmos DB“ versehen.

## <a name="supported-apis"></a>Unterstützte APIs

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="add-to-your-functions-app"></a>Hinzufügen zu Ihrer Funktions-App

### <a name="functions-2x-and-higher"></a>Functions 2.x und höher

Das Arbeiten mit Triggern und Bindungen erfordert, dass Sie auf das entsprechende Paket verweisen. Das NuGet-Paket wird für .NET-Klassenbibliotheken verwendet, während das Erweiterungspaket für alle anderen Anwendungstypen verwendet wird.

| Sprache                                        | Hinzufügen nach...                                   | Bemerkungen 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installieren des [NuGet-Paket], Version 3.x | |
| C#-Skript, Java, JavaScript, Python, PowerShell | Registrieren des [Erweiterungspaket]          | Die [Erweiterung für Azure-Tools] wird zur Verwendung mit Visual Studio Code empfohlen. |
| C#-Skript (nur online im Azure-Portal)         | Hinzufügen einer Bindung                            | Informationen zum Aktualisieren vorhandener Bindungserweiterungen, ohne Ihre Funktions-App erneut veröffentlichen zu müssen, finden Sie unter [Aktualisieren Ihrer Erweiterungen]. |

[NuGet-Paket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB
[core tools]: ./functions-run-local.md
[Erweiterungspaket]: ./functions-bindings-register.md#extension-bundles
[Aktualisieren Ihrer Erweiterungen]: ./functions-bindings-register.md
[Azure-Tools-Erweiterung]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="cosmos-db-extension-4x-and-higher"></a>Cosmos DB-Erweiterung 4.x oder höher

Eine neue Version der Cosmos DB-Bindungserweiterung ist als Vorschau verfügbar. Sie bietet die Möglichkeit, eine [Verbindung mit einer Identität anstelle eines Geheimnisses](./functions-reference.md#configure-an-identity-based-connection) herzustellen. Ein Tutorial zum Konfigurieren Ihrer Funktions-Apps mit verwalteten Identitäten finden Sie unter [Tutorial zum Erstellen einer Funktions-App mit identitätsbasierten Verbindungen](./functions-identity-based-connections-tutorial.md). Für .NET-Anwendungen ändert die neue Erweiterungsversion auch die Typen, mit denen eine Bindung erfolgen kann. Dabei werden die Typen aus den v2 SDK `Microsoft.Azure.DocumentDB` durch neuere Typen aus dem v3 SDK [Microsoft.Azure.Cosmos](../cosmos-db/sql/sql-api-sdk-dotnet-standard.md) ersetzt. Weitere Informationen zu den Unterschieden zwischen diesen neuen Typen und zur Migration zu ihnen finden Sie im [SDK-Migrationsleitfaden](../cosmos-db/sql/migrate-dotnet-v3.md) sowie in den Beispielen zu [Triggern](./functions-bindings-cosmosdb-v2-trigger.md), [Eingabebindungen](./functions-bindings-cosmosdb-v2-input.md) und [Ausgabebindungen](./functions-bindings-cosmosdb-v2-output.md).

Diese Bindungsversion ist als [NuGet-Vorschaupaket] verfügbar. Weitere Informationen finden Sie unter [Aktualisieren Ihrer Erweiterungen].

[NuGet-Vorschaupaket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB/4.0.0-preview2

> [!NOTE]
> Derzeit ist Authentifizierung mit einer Identität anstelle eines Geheimnisses mithilfe der Vorschauerweiterung 4.x nur für Pläne für Elastic Premium verfügbar. 

### <a name="functions-1x"></a>Functions 1.x

Functions 1.x-Apps enthalten automatisch einen Verweis auf das NuGet-Paket, Version 2.x, [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs).

## <a name="exceptions-and-return-codes"></a>Ausnahmen und Rückgabecodes

| Bindung | Verweis |
|---|---|
| CosmosDB | [CosmosDB-Fehlercodes](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>Einstellungen für „host.json“

[!INCLUDE [functions-host-json-section-intro](../../includes/functions-host-json-section-intro.md)]

```json
{
    "version": "2.0",
    "extensions": {
        "cosmosDB": {
            "connectionMode": "Gateway",
            "protocol": "Https",
            "leaseOptions": {
                "leasePrefix": "prefix1"
            }
        }
    }
}
```

|Eigenschaft  |Standard |BESCHREIBUNG |
|----------|--------|------------|
|GatewayMode|Gateway|Der von der Funktion zum Herstellen von Verbindungen mit dem Azure Cosmos DB-Dienst verwendete Verbindungsmodus. Die Optionen sind `Direct` und `Gateway`.|
|Protocol|HTTPS|Das von der Funktion zum Herstellen von Verbindungen mit dem Azure Cosmos DB-Dienst verwendete Verbindungsprotokoll. Hier finden Sie [eine Erläuterung der beiden Modi](../cosmos-db/performance-tips.md#networking). <br><br> Diese Einstellung ist in [Version 4.x der Erweiterung](#cosmos-db-extension-4x-and-higher) nicht verfügbar. |
|leasePrefix|–|Lease-Präfix, das durchgängig für alle Funktionen in einer App verwendet wird. <br><br> Diese Einstellung ist in [Version 4.x der Erweiterung](#cosmos-db-extension-4x-and-higher) nicht verfügbar.|

## <a name="next-steps"></a>Nächste Schritte

- [Ausführen einer Funktion, wenn ein Azure Cosmos DB-Dokument erstellt oder geändert wird (Trigger)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Lesen eines Azure Cosmos DB-Dokuments (Eingabebindung)](./functions-bindings-cosmosdb-v2-input.md)
- [Speichern der Änderungen an einem Azure Cosmos DB-Dokument (Ausgabebindung)](./functions-bindings-cosmosdb-v2-output.md)