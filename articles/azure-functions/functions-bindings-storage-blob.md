---
title: Azure Blob Storage-Trigger und -Bindungen für Azure Functions
description: Erfahren Sie, wie Sie Azure Blob Storage-Trigger und -Bindungen in Azure Functions verwenden.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: af8e6a64e94530d6cca1494aff2c3b249186a42e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339619"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Übersicht zu Azure Blob Storage-Bindungen für Azure Functions

Azure Functions integriert sich in [Azure Storage](../storage/index.yml) mittels [Triggern und Bindungen](./functions-triggers-bindings.md). Durch die Integration in Blob Storage können Sie Funktionen erstellen, die auf Änderungen in Blobdaten sowie auf Lese- und Schreib-Werte reagieren.

| Aktion | type |
|---------|---------|
| Ausführen einer Funktion, wenn sich Blob Storage-Daten ändern | [Trigger](./functions-bindings-storage-blob-trigger.md) |
| Lesen von Blob Storage-Daten in eine Funktion | [Eingabebindung](./functions-bindings-storage-blob-input.md) |
| Einer Funktion das Schreiben von Blob Storage-Daten gestatten |[Ausgabebindung](./functions-bindings-storage-blob-output.md) |

## <a name="add-to-your-functions-app"></a>Hinzufügen zu Ihrer Funktions-App

### <a name="functions-2x-and-higher"></a>Functions 2.x und höher

Das Arbeiten mit Triggern und Bindungen erfordert, dass Sie auf das entsprechende Paket verweisen. Das NuGet-Paket wird für .NET-Klassenbibliotheken verwendet, während das Erweiterungspaket für alle anderen Anwendungstypen verwendet wird.

| Sprache                                        | Hinzufügen nach...                                   | Bemerkungen 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installieren des [NuGet-Paket], Version 3.x | |
| C#-Skript, Java, JavaScript, Python, PowerShell | Registrieren des [Erweiterungspaket]          | Die [Erweiterung für Azure-Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) wird zur Verwendung mit Visual Studio Code empfohlen. |
| C#-Skript (nur online im Azure-Portal)         | Hinzufügen einer Bindung                            | Informationen zum Aktualisieren vorhandener Bindungserweiterungen, ohne Ihre Funktions-App erneut veröffentlichen zu müssen, finden Sie unter [Aktualisieren Ihrer Erweiterungen]. |

#### <a name="storage-extension-5x-and-higher"></a>Storage-Erweiterung 5.x und höher

Eine neue Version der Storage-Bindungserweiterung ist als [NuGet-Vorschaupaket](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/5.0.0-beta.3) verfügbar. Diese Vorschau bietet die Möglichkeit, eine [Verbindung mit einer Identität anstelle eines Geheimnisses](./functions-reference.md#configure-an-identity-based-connection) herzustellen. Für .NET-Anwendungen werden auch die Typen geändert, mit denen eine Bindung erfolgen kann. Dabei werden die Typen aus `WindowsAzure.Storage` und `Microsoft.Azure.Storage` durch neuere Typen aus [Azure.Storage.Blobs](/dotnet/api/azure.storage.blobs) ersetzt. Erfahren Sie im [Migrationsleitfaden für Azure.Storage.Blobs](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/storage/Azure.Storage.Blobs/AzureStorageNetMigrationV12.md) mehr über diese neuen Typen, wie sie sich unterscheiden und wie zu ihnen migrieren.

> [!NOTE]
> Das Vorschaupaket ist nicht in einem Erweiterungspaket enthalten und muss manuell installiert werden. Fügen Sie für .NET-Apps einen Verweis auf das Paket hinzu. Informationen zu allen anderen App-Typen finden Sie unter [Aktualisieren Ihrer Erweiterungen].

[core tools]: ./functions-run-local.md
[Erweiterungspaket]: ./functions-bindings-register.md#extension-bundles
[NuGet-Paket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Aktualisieren Ihrer Erweiterungen]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1.x-Apps enthalten automatisch einen Verweis auf das NuGet-Paket, Version 2.x, [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs).

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="hostjson-settings"></a>Einstellungen für „host.json“

> [!NOTE]
> Dieser Abschnitt gilt nicht für Erweiterungsversionen vor 5.0.0. Für diese Versionen gibt es keine globalen Konfigurationseinstellungen für Blobs.

In diesem Abschnitt werden die globalen Konfigurationseinstellungen beschrieben, die für diese Bindung verfügbar sind, wenn [Erweiterungsversion 5.0.0 und höher](#storage-extension-5x-and-higher) verwendet wird. Die unten gezeigte Beispieldatei *host.json* enthält nur die Einstellungen für Version 2.x oder höher Versionen für diese Bindung. Weitere Informationen zu globalen Konfigurationseinstellungen in Functions-Version 2.x oder höher finden Sie unter [host.json-Referenz für Azure Functions](functions-host-json.md).

```json
{
    "version": "2.0",
    "extensions": {
        "blobs": {
            "maxDegreeOfParallelism": "4"
        }
    }
}
```

|Eigenschaft  |Standard | BESCHREIBUNG |
|---------|---------|---------|
|maxDegreeOfParallelism|8 * (Anzahl verfügbarer Kerne)|Die ganzzahlige Anzahl gleichzeitiger Aufrufe, die für jede durch ein Blob ausgelöste Funktion zulässig sind. Der minimal zulässige Wert ist 1.|

## <a name="next-steps"></a>Nächste Schritte

- [Ausführen einer Funktion, wenn sich Blob Storage-Daten ändern](./functions-bindings-storage-blob-trigger.md)
- [Lesen von Blob Storage-Daten, wenn eine Funktion ausgeführt wird](./functions-bindings-storage-blob-input.md)
- [Schreiben von Blob Storage-Daten aus einer Funktion](./functions-bindings-storage-blob-output.md)
