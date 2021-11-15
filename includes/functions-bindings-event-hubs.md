---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 70544909ae419cdf215bdad88c3ac19968f6e4f0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520831"
---
## <a name="add-to-your-functions-app"></a>Hinzufügen zu Ihrer Funktions-App

### <a name="functions-2x-and-higher"></a>Functions 2.x und höher

Das Arbeiten mit Triggern und Bindungen erfordert, dass Sie auf das entsprechende Paket verweisen. Das NuGet-Paket wird für .NET-Klassenbibliotheken verwendet, während das Erweiterungspaket für alle anderen Anwendungstypen verwendet wird.

| Sprache                                        | Hinzufügen nach...                                   | Bemerkungen 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installieren des [NuGet-Paket], Version 3.x | |
| C#-Skript, Java, JavaScript, Python, PowerShell | Registrieren des [Erweiterungspaket]          | Die [Erweiterung für Azure-Tools] wird zur Verwendung mit Visual Studio Code empfohlen. |
| C#-Skript (nur online im Azure-Portal)         | Hinzufügen einer Bindung                            | Informationen zum Aktualisieren vorhandener Bindungserweiterungen, ohne Ihre Funktions-App erneut veröffentlichen zu müssen, finden Sie unter [Aktualisieren Ihrer Erweiterungen]. |

[NuGet-Paket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs
[core tools]: ../articles/azure-functions/functions-run-local.md
[Erweiterungspaket]: ../articles/azure-functions/functions-bindings-register.md#extension-bundles
[Aktualisieren Ihrer Erweiterungen]: ../articles/azure-functions/functions-bindings-register.md
[Azure-Tools-Erweiterung]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="event-hubs-extension-5x-and-higher"></a>Event Hubs-Erweiterung 5.x und höher

Eine neue Version der Event Hubs-Bindungserweiterung ist nun verfügbar. Sie führt die Möglichkeit ein, [sich mit einer Identität anstelle eines Geheimnisses](../articles/azure-functions/functions-reference.md#configure-an-identity-based-connection) zu verbinden. Ein Tutorial zum Konfigurieren Ihrer Funktions-Apps mit verwalteten Identitäten finden Sie im [Tutorial zum Erstellen einer Funktions-App mit identitätsbasierten Verbindungen](../articles/azure-functions/functions-identity-based-connections-tutorial.md). Für .NET-Anwendungen werden auch die Typen geändert, mit denen eine Bindung erfolgen kann. Dabei werden die Typen aus `Microsoft.Azure.EventHubs` durch neuere Typen aus [Azure.Messaging.EventHubs](/dotnet/api/azure.messaging.eventhubs) ersetzt.

Diese Erweiterungsversion ist nach der Installation des [NuGet-Pakets] Version 5.x verfügbar oder kann aus dem Erweiterungspaket v3 hinzugefügt werden, indem Sie Folgendes in Ihrer Datei `host.json` hinzufügen:

```json
{
  "version": "2.0",
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle",
    "version": "[3.3.0, 4.0.0)"
  }
}
```

Weitere Informationen finden Sie unter [Aktualisierung Ihrer Erweiterungen].

[core tools]: ./functions-run-local.md
[Erweiterungspaket]: ./functions-bindings-register.md#extension-bundles
[NuGet-Paket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs/
[Aktualisieren Ihrer Erweiterungen]: ./functions-bindings-register.md
[Azure-Tools-Erweiterung]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1.x-Apps enthalten automatisch einen Verweis auf das NuGet-Paket, Version 2.x, [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs).

## <a name="hostjson-settings"></a>Einstellungen für „host.json“
<a name="host-json"></a>

Die Datei [host.json](../articles/azure-functions/functions-host-json.md#eventhub) enthält Einstellungen, die das Verhalten für Event Hubs-Trigger steuern. Die Konfiguration unterscheidet sich abhängig von der Azure Functions-Version.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]