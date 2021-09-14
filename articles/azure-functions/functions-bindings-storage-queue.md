---
title: Azure Queue Storage-Trigger und -Bindungen für Azure Functions – Übersicht
description: Hier erfahren Sie, wie Sie den Azure Queue Storage-Trigger und die entsprechende Ausgabebindung in Azure Functions verwenden.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: ea5f9511cd9ae6d569d833ef8f950f1391c30f38
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "113112183"
---
# <a name="azure-queue-storage-trigger-and-bindings-for-azure-functions-overview"></a>Azure Queue Storage-Trigger und -Bindungen für Azure Functions – Übersicht

Azure Functions kann ausgeführt werden, wenn neue Azure Queue Storage-Nachrichten erstellt werden, und Warteschlangennachrichten in eine Funktion schreiben.

| Aktion | type |
|---------|---------|
| Ausführen einer Funktion, wenn sich Queue Storage-Daten ändern | [Trigger](./functions-bindings-storage-queue-trigger.md) |
| Schreiben von Queue Storage-Nachrichten |[Ausgabebindung](./functions-bindings-storage-queue-output.md) |

## <a name="add-to-your-functions-app"></a>Hinzufügen zu Ihrer Funktions-App

### <a name="functions-2x-and-higher"></a>Functions 2.x und höher

Das Arbeiten mit Triggern und Bindungen erfordert, dass Sie auf das entsprechende Paket verweisen. Das NuGet-Paket wird für .NET-Klassenbibliotheken verwendet, während das Erweiterungspaket für alle anderen Anwendungstypen verwendet wird.

| Sprache                                        | Hinzufügen nach...                                   | Bemerkungen 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installieren des [NuGet-Paket], Version 3.x | |
| C#-Skript, Java, JavaScript, Python, PowerShell | Registrieren des [Erweiterungspaket]          | Die [Erweiterung für Azure-Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) wird zur Verwendung mit Visual Studio Code empfohlen. |
| C#-Skript (nur online im Azure-Portal)         | Hinzufügen einer Bindung                            | Informationen zum Aktualisieren vorhandener Bindungserweiterungen, ohne Ihre Funktions-App erneut veröffentlichen zu müssen, finden Sie unter [Aktualisieren Ihrer Erweiterungen]. |

#### <a name="storage-extension-5x-and-higher"></a>Storage-Erweiterung 5.x und höher

Eine neue Version der Storage-Bindungserweiterung ist als [NuGet-Vorschaupaket](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/5.0.0-beta.3) verfügbar. Diese Vorschau bietet die Möglichkeit, eine [Verbindung mit einer Identität anstelle eines Geheimnisses](./functions-reference.md#configure-an-identity-based-connection) herzustellen. Für .NET-Anwendungen werden auch die Typen geändert, mit denen eine Bindung erfolgen kann. Dabei werden die Typen aus `WindowsAzure.Storage` und `Microsoft.Azure.Storage` durch neuere Typen aus [Azure.Storage.Queues](/dotnet/api/azure.storage.queues) ersetzt.

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

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Einstellungen für „host.json“

In diesem Abschnitt werden die verfügbaren globalen Konfigurationseinstellungen für diese Bindung in Version 2.x und höheren Versionen beschrieben. Die unten gezeigte Beispieldatei *host.json* enthält nur die Einstellungen ab Version 2.x für diese Bindung. Weitere Informationen zu globalen Konfigurationseinstellungen in Version 2.x und höheren Versionen finden Sie unter [host.json-Referenz für Azure Functions 2.x](functions-host-json.md).

> [!NOTE]
> Eine Referenz für „host.json“ in Functions 1.x finden Sie unter [host.json-Referenz für Azure Functions 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8,
            "messageEncoding": "base64"
        }
    }
}
```

|Eigenschaft  |Standard | BESCHREIBUNG |
|---------|---------|---------|
|maxPollingInterval|00:01:00|Das maximale Intervall zwischen Warteschlangenabfragen. Der Minimalintervall ist 00:00:00,100 (100 ms). Intervalle werden bis `maxPollingInterval` erhöht. Der Standardwert von `maxPollingInterval` lautet 00:01:00 (1 min). `maxPollingInterval` darf nicht unter 00:00:00,100 (100 ms) liegen. In Functions 2.x und höher ist der Datentyp eine `TimeSpan`-Struktur. In Functions 1.x wird diese in Millisekunden angegeben.|
|visibilityTimeout|00:00:00|Das Zeitintervall zwischen Wiederholungsversuchen, wenn bei der Verarbeitung einer Nachricht ein Fehler auftritt. |
|batchSize|16|Die Anzahl der Warteschlangennachrichten, die die Functions-Runtime gleichzeitig abruft und parallel verarbeitet. Wenn die zu verarbeitende Anzahl `newBatchThreshold` erreicht, ruft die Runtime einen weiteren Batch ab und beginnt mit der Verarbeitung dieser Nachrichten. Aus diesem Grund beträgt die maximale Anzahl der pro Funktion verarbeiteten Nachrichten `batchSize` plus `newBatchThreshold`. Dieser Grenzwert gilt separat für jede Funktion, die durch die Warteschlange ausgelöst wird. <br><br>Wenn Sie eine parallele Ausführung für in einer Warteschlange empfangene Nachrichten vermeiden möchten, können Sie `batchSize` auf „1“ festlegen. Durch diese Einstellung wird jedoch Parallelität verhindert, solange Ihre Funktions-App nur auf einem einzelnen virtuellen Computer (virtual machine, VM) ausgeführt wird. Wenn die Funktions-App horizontal auf mehrere virtuelle Computer hochskaliert wird, kann jeder virtuelle Computer eine Instanz jeder durch die Warteschlange ausgelösten Funktion ausführen.<br><br>Die maximale `batchSize` beträgt 32. |
|maxDequeueCount|5|Die Anzahl der Versuche zum Verarbeiten einer Nachricht, bevor diese in die Warteschlange für nicht verarbeitete Nachrichten verschoben wird.|
|newBatchThreshold|N*batchSize/2|Wenn die Anzahl der gleichzeitig verarbeiteten Nachrichten auf diesen Wert sinkt, ruft die Runtime einen weiteren Batch ab.<br><br>`N` stellt die Anzahl der beim Ausführen in App Service oder Premium-Plänen verfügbaren vCPUs dar. Der zugehörige Wert für den Verbrauchsplan ist `1`.|
|messageEncoding|base64| Diese Einstellung ist erst [ab der Erweiterungsversion 5.0.0](#storage-extension-5x-and-higher) verfügbar. Sie stellt das Codierungsformat für Nachrichten dar. Gültige Werte sind `base64` und `none`.|

## <a name="next-steps"></a>Nächste Schritte

- [Ausführen einer Funktion, wenn sich Queue Storage-Daten ändern (Trigger)](./functions-bindings-storage-queue-trigger.md)
- [Schreiben von Queue Storage-Nachrichten (Ausgabebindung)](./functions-bindings-storage-queue-output.md)
