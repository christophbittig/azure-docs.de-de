---
title: Azure Event Grid-Bindungen für Azure Functions
description: Behandlung von Event Grid-Ereignissen in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: f0f42b37c40a3a3a2ecda4115ebc7729fc686671
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131473963"
---
# <a name="azure-event-grid-bindings-for-azure-functions"></a>Azure Event Grid-Bindungen für Azure Functions

In dieser Referenz wird die Behandlung von [Event Grid](../event-grid/overview.md)-Ereignissen in Azure Functions erläutert. Ausführliche Informationen zur Behandlung von Event Grid-Nachrichten an einem HTTP-Endpunkt finden Sie unter [Empfangen von Ereignissen an einem HTTP-Endpunkt](../event-grid/receive-events.md).

Event Grid ist ein Azure-Dienst, mit dem HTTP-Anforderungen gesendet werden, um Sie über Ereignisse zu benachrichtigen, die in *Herausgebern* erfolgen. Ein Herausgeber ist der Dienst oder die Ressource, von dem bzw. der das Ereignis stammt. Ein Azure Blob Storage-Konto ist beispielsweise ein Herausgeber, [der Upload oder die Löschung eines Blobs ist dagegen ein Ereignis](../storage/blobs/storage-blob-event-overview.md). Einige [Azure-Dienste bieten eine integrierte Unterstützung zum Veröffentlichen von Ereignissen in Event Grid](../event-grid/overview.md#event-sources).

*Ereignishandler* empfangen und verarbeiten Ereignisse. Azure Functions ist einer von mehreren [Azure-Diensten, die eine integrierte Unterstützung für die Behandlung von Event Grid-Ereignissen bieten](../event-grid/overview.md#event-handlers). In dieser Referenz erfahren Sie, wie Sie mit einem Event Grid-Trigger eine Funktion aufrufen, wenn ein Ereignis von Event Grid empfangen wird, und mithilfe der Ausgabebindung Ereignisse an ein [benutzerdefiniertes Event Grid-Thema](../event-grid/post-to-custom-topic.md) senden.

Bei Bedarf können Sie einen HTTP-Trigger zur Behandlung von Event Grid-Ereignissen verwenden. Siehe hierzu [Empfangen von Ereignissen an einem HTTP-Endpunkt](../event-grid/receive-events.md). Momentan kann für eine Azure Functions-App kein Event Grid-Trigger verwendet werden, wenn das Ereignis im [CloudEvents-Schema](../event-grid/cloudevents-schema.md#azure-functions) übermittelt wird. Verwenden Sie stattdessen einen HTTP-Trigger.

| Aktion | type |
|---------|---------|
| Ausführen einer Funktion, wenn ein Event Grid-Ereignis ausgegeben wird | [Trigger](./functions-bindings-event-grid-trigger.md) |
| Senden eines Event Grid-Ereignisses |[Ausgabebindung](./functions-bindings-event-grid-output.md) |

Der Code in dieser Referenz nutzt standardmäßig .NET Core-Syntax, die in Functions ab Version 2.x verwendet wird. Informationen zur 1.x-Syntax finden Sie in den [1.x-Functions-Vorlagen](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="add-to-your-functions-app"></a>Hinzufügen zu Ihrer Funktions-App

### <a name="functions-2x-and-higher"></a>Functions 2.x und höher

Das Arbeiten mit Triggern und Bindungen erfordert, dass Sie auf das entsprechende Paket verweisen. Das NuGet-Paket wird für .NET-Klassenbibliotheken verwendet, während das Erweiterungspaket für alle anderen Anwendungstypen verwendet wird.

| Sprache | Hinzufügen nach... | Bemerkungen |
|---|---|---|
| C# | Installieren von Version 2.x des [NuGet-Paket] | |
| C#-Skript, Java, JavaScript, Python, PowerShell | Registrieren des [Erweiterungspaket] | Die [Erweiterung für Azure-Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) wird zur Verwendung mit Visual Studio Code empfohlen. |
| C#-Skript (nur online im Azure-Portal) | Hinzufügen einer Bindung | Informationen zum Aktualisieren vorhandener Bindungserweiterungen, ohne Ihre Funktions-App erneut veröffentlichen zu müssen, finden Sie unter [Aktualisieren Ihrer Erweiterungen]. |

[core tools]: ./functions-run-local.md
[Erweiterungspaket]: ./functions-bindings-register.md#extension-bundles
[NuGet-Paket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid
[Aktualisieren Ihrer Erweiterungen]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

#### <a name="event-grid-extension-3x-and-higher"></a>Event Grid-Erweiterung 3.x und höher

Eine neue Version der Event Grid-Bindungserweiterung ist jetzt verfügbar. Für .NET-Anwendungen werden die Typen geändert, mit denen eine Bindung erfolgen kann. Dabei werden die Typen aus `Microsoft.Azure.EventGrid.Models` durch neuere Typen aus [Azure.Messaging.EventGrid](/dotnet/api/azure.messaging.eventgrid) ersetzt. [Cloudereignisse](/dotnet/api/azure.messaging.cloudevent) werden auch in der neuen Event Grid-Erweiterung unterstützt.

Diese Erweiterungsversion ist nach der Installation des [NuGet-Pakets] Version 3.x verfügbar oder kann aus dem Erweiterungspaket v3 hinzugefügt werden, indem Sie Folgendes in Ihrer Datei `host.json` hinzufügen:

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

### <a name="functions-1x"></a>Functions 1.x

Functions 1.x-Apps enthalten automatisch einen Verweis auf das NuGet-Paket, Version 2.x, [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs).

## <a name="next-steps"></a>Nächste Schritte
* [Ausführen einer Funktion, wenn ein Event Grid-Ereignis ausgegeben wird](./functions-bindings-event-grid-trigger.md)
* [Versenden eines Event Grid-Ereignisses](./functions-bindings-event-grid-trigger.md)
