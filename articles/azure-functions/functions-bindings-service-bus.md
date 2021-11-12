---
title: Azure Service Bus-Bindungen für Azure Functions
description: Erfahren Sie, wie Azure Service Bus-Trigger und -Bindungen in Azure Functions verwendet werden.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 124e1f104bf7607b63eac916754c86b7b28cd8a2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131470431"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Service Bus-Bindungen für Azure Functions

Azure Functions wird über [Trigger und Bindungen](./functions-triggers-bindings.md) in [Azure Service Bus](https://azure.microsoft.com/services/service-bus) integriert. Durch die Integration mit Service Bus können Sie Funktionen erstellen, die auf Warteschlangen- oder Themennachrichten reagieren und diese senden.

| Aktion | type |
|---------|---------|
| Ausführen einer Funktion, wenn eine Warteschlangen- oder Themennachricht von Service Bus empfangen wird | [Trigger](./functions-bindings-service-bus-trigger.md) |
| Senden von Azure Service Bus-Nachrichten |[Ausgabebindung](./functions-bindings-service-bus-output.md) |

## <a name="add-to-your-functions-app"></a>Hinzufügen zu Ihrer Funktions-App

### <a name="functions-2x-and-higher"></a>Functions 2.x und höher

Das Arbeiten mit Triggern und Bindungen erfordert, dass Sie auf das entsprechende Paket verweisen. Das NuGet-Paket wird für .NET-Klassenbibliotheken verwendet, während das Erweiterungspaket für alle anderen Anwendungstypen verwendet wird.

| Sprache                                        | Hinzufügen nach...                                   | Bemerkungen 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installieren der Version 4.x des [NuGet-Paket] | |
| C#-Skript, Java, JavaScript, Python, PowerShell | Registrieren des [Erweiterungspaket]          | Die [Erweiterung für Azure-Tools] wird zur Verwendung mit Visual Studio Code empfohlen. |
| C#-Skript (nur online im Azure-Portal)         | Hinzufügen einer Bindung                            | Informationen zum Aktualisieren vorhandener Bindungserweiterungen, ohne Ihre Funktions-App erneut veröffentlichen zu müssen, finden Sie unter [Aktualisieren Ihrer Erweiterungen]. |

[NuGet-Paket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/
[core tools]: ./functions-run-local.md
[Erweiterungspaket]: ./functions-bindings-register.md#extension-bundles
[Aktualisieren Ihrer Erweiterungen]: ./functions-bindings-register.md
[Azure-Tools-Erweiterung]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

#### <a name="service-bus-extension-5x-and-higher"></a>Service Bus-Erweiterung 5.x und höher

Eine neue Version der Service Bus-Bindungserweiterung ist nun verfügbar. Sie führt die Möglichkeit ein, [sich mit einer Identität anstelle eines Geheimnisses](./functions-reference.md#configure-an-identity-based-connection) zu verbinden. Ein Tutorial zum Konfigurieren Ihrer Funktions-Apps mit verwalteten Identitäten finden Sie im [Tutorial zum Erstellen einer Funktions-App mit identitätsbasierten Verbindungen](./functions-identity-based-connections-tutorial.md). Für .NET-Anwendungen gilt auch, dass die neue Version der Erweiterung die Typen ändert, mit denen eine Bindung erfolgen kann. Dabei werden die Typen aus `Microsoft.ServiceBus.Messaging` und `Microsoft.Azure.ServiceBus` durch neuere Typen aus [Azure.Messaging.ServiceBus](/dotnet/api/azure.messaging.servicebus) ersetzt.

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
[NuGet-Paket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus
[Aktualisieren Ihrer Erweiterungen]: ./functions-bindings-register.md
[Azure-Tools-Erweiterung]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1.x-Apps enthalten automatisch einen Verweis auf das NuGet-Paket, Version 2.x, [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs).


<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Einstellungen für „host.json“

[!INCLUDE [functions-host-json-section-intro](../../includes/functions-host-json-section-intro.md)]

> [!NOTE]
> Eine Referenz für „host.json“ in Functions 1.x finden Sie unter [host.json-Referenz für Azure Functions 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": true,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:05:00"
            },
            "sessionHandlerOptions": {
                "autoComplete": false,
                "messageWaitTimeout": "00:00:30",
                "maxAutoRenewDuration": "00:55:00",
                "maxConcurrentSessions": 16
            },
            "batchOptions": {
                "maxMessageCount": 1000,
                "operationTimeout": "00:01:00",
                "autoComplete": true
            }
        }
    }
}
```

Wenn Sie `isSessionsEnabled` auf `true` festgelegt haben, werden die `sessionHandlerOptions` berücksichtigt.  Wenn Sie `isSessionsEnabled` auf `false` festgelegt haben, werden die `messageHandlerOptions` berücksichtigt.

|Eigenschaft  |Standard | BESCHREIBUNG |
|---------|---------|---------|
|prefetchCount|0|Ruft die Anzahl der Nachrichten ab, die der Nachrichtenempfänger gleichzeitig anfordern kann, oder legt sie fest.|
|messageHandlerOptions.maxAutoRenewDuration|00:05:00|Die maximale Zeitspanne, in der die Nachrichtensperre automatisch erneuert wird.|
|messageHandlerOptions.autoComplete|true|Gibt an, ob der Trigger nach der Verarbeitung automatisch „complete“ aufrufen soll oder ob der Funktionscode „complete“ manuell aufruft.<br><br>Das Festlegen auf `false` wird nur in C# unterstützt.<br><br>Wenn der Wert auf `true` festgelegt ist, wird die Nachricht automatisch durch den Triggervorgang abgeschlossen, sofern die Ausführung der Funktion erfolgreich war, andernfalls wird die Meldung verworfen.<br><br>Wenn der Wert auf `false` festgelegt ist, müssen Sie selbst die [MessageReceiver](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver)-Methoden aufrufen, um die Nachricht abzuschließen, zu verwerfen oder in die Warteschlange für unzustellbare Nachrichten zu verschieben. Wenn eine Ausnahme ausgelöst wird (und keine der `MessageReceiver`-Methoden aufgerufen wird), bleibt die Sperre erhalten. Nachdem die Sperre abgelaufen ist, wird die Nachricht erneut in die Warteschlange eingereiht, wobei `DeliveryCount` erhöht und die Sperre automatisch erneuert wird.<br><br>Bei Nicht-C#-Funktionen führen Ausnahmen in der Funktion dazu, dass die Runtime `abandonAsync` im Hintergrund aufruft. Wenn keine Ausnahme auftritt, wird `completeAsync` im Hintergrund aufgerufen. |
|messageHandlerOptions.maxConcurrentCalls|16|Die maximale Anzahl gleichzeitiger Aufrufe für den Rückruf, der vom Nachrichtensystem pro skalierter Instanz initiiert werden soll. Die Functions-Runtime verarbeitet standardmäßig mehrere Nachrichten gleichzeitig.|
|sessionHandlerOptions.maxConcurrentSessions|2000|Die maximale Anzahl von Sitzungen, die parallel pro skalierter Instanz verarbeitet werden können.|
|batchOptions.maxMessageCount|1000| Die maximale Anzahl von Nachrichten, die beim Auslösen an die Funktion gesendet werden. |
|batchOptions.operationTimeout|00:01:00| Ein In `hh:mm:ss` ausgedrückter Zeitraumwert. |
|batchOptions.autoComplete|true| Siehe die obige Beschreibung für `messageHandlerOptions.autoComplete`. |

### <a name="additional-settings-for-version-5x"></a>Zusätzliche Einstellungen für Version 5.x und höher

Das Beispieldatei host.json unten enthält nur die Einstellungen für Version 5.0.0 und höher der Service Bus-Erweiterung.

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "clientRetryOptions":{
                "mode": "exponential",
                "tryTimeout": "00:01:00",
                "delay": "00:00:00.80",
                "maxDelay": "00:01:00",
                "maxRetries": 3
            },
            "prefetchCount": 0,
            "autoCompleteMessages": true,
            "maxAutoLockRenewalDuration": "00:05:00",
            "maxConcurrentCalls": 16,
            "maxConcurrentSessions": 8,
            "maxMessages": 1000,
            "sessionIdleTimeout": "00:01:00"
        }
    }
}
```

Wenn Sie die Service Bus-Erweiterung in Version 5.x und höher benutzen, werden zusätzlich zu den 2.x-Einstellungen in `ServiceBusOptions` die folgenden globalen Konfigurationseinstellungen unterstützt.

|Eigenschaft  |Standard | BESCHREIBUNG |
|---------|---------|---------|
|prefetchCount|0|Ruft die Anzahl der Nachrichten ab, die der Nachrichtenempfänger gleichzeitig anfordern kann, oder legt sie fest.|
|autoCompleteMessages|true|Bestimmt, ob Nachrichten nach erfolgreicher Ausführung der Funktion automatisch abgeschlossen werden sollen, und sollte statt der `autoComplete`-Konfigurationseinstellung verwendet werden.|
|maxAutoLockRenewalDuration|00:05:00|Solle anstelle von `maxAutoRenewDuration` verwendet werden|
|maxConcurrentCalls|16|Die maximale Anzahl gleichzeitiger Aufrufe für den Rückruf, der vom Nachrichtensystem pro skalierter Instanz initiiert werden soll. Die Functions-Runtime verarbeitet standardmäßig mehrere Nachrichten gleichzeitig.|
|maxConcurrentSessions|8|Die maximale Anzahl von Sitzungen, die parallel pro skalierter Instanz verarbeitet werden können.|
|maxMessages|1000|Die maximale Anzahl von Nachrichten, die an jeden Funktionsaufruf übergeben werden. Dies gilt nur für Funktionen, die einen Nachrichtenbatch empfangen.|
|sessionIdleTimeout|–|Die maximale Wartezeit für das Empfangen einer Nachricht für die derzeit aktive Sitzung. Nach Ablauf dieser Zeit schließt der Prozessor die Sitzung, und versucht, eine weitere Sitzung zu verarbeiten.|

### <a name="retry-settings"></a>Wiederholungseinstellungen

Zusätzlich zu den oben genannten Konfigurationseigenschaften können Sie bei Verwendung von Version 5.x und höher der Service Bus-Erweiterung auch `RetryOptions` innerhalb von `ServiceBusOptions` konfigurieren. Diese Einstellungen bestimmen, ob ein fehlgeschlagener Vorgang wiederholt werden soll, und, falls ja, die Wartezeit zwischen den Wiederholungsversuchen. Die Optionen steuern auch die Zeit, die für das Empfangen von Nachrichten und andere Interaktionen mit dem Service Bus-Dienst zugelassen ist.

|Eigenschaft  |Standard | BESCHREIBUNG |
|---------|---------|---------|
|Modus|Exponentiell|Die Methode, die zum Berechnen von Wiederholungsverzögerungen verwendet werden soll Im standardmäßigen exponentiellen Modus werden Wiederholungsversuche mit einer Verzögerung basierend auf einer Wartezeitstrategie ausgeführt, bei der jeder Versuch die Wartezeit erhöht, bevor ein neuer Versuch beginnt. Im Modus `Fixed` werden Versuche in festen Abständen wiederholt, und jede Verzögerung hat die gleiche Dauer.|
|tryTimeout|00:01:00|Die maximale Dauer, die pro Versuch auf einen Vorgang gewartet werden soll|
|delay|00:00:00.80|Der Verzögerungs- oder Wartezeitfaktor, der zwischen Wiederholungsversuchen angewendet werden soll|
|maxDelay|00:01:00|Die maximale zugelassene Verzögerung zwischen Wiederholungsversuchen|
|maxRetries|3|Die maximale Anzahl von Wiederholungsversuchen, bevor der zugeordnete Vorgang als fehlgeschlagen angesehen wird|

---

## <a name="next-steps"></a>Nächste Schritte

- [Ausführen einer Funktion, wenn eine Warteschlangen- oder Themennachricht von Service Bus empfangen wird (Trigger)](./functions-bindings-service-bus-trigger.md)
- [Senden von Azure Service Bus-Nachrichten mit Azure Functions (Ausgabebindung)](./functions-bindings-service-bus-output.md)
