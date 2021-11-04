---
title: Versionsverwaltung in Durable Functions – Azure
description: Es wird beschrieben, wie Sie die Versionsverwaltung in der Erweiterung „Durable Functions“ für Azure Functions implementieren.
author: cgillum
ms.topic: conceptual
ms.date: 05/12/2021
ms.author: azfuncdf
ms.openlocfilehash: 16de5f5950cf305115de4c2849b3ebbbc4cf2536
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131507719"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Versionsverwaltung in Durable Functions (Azure Functions)

Es ist unvermeidbar, dass Funktionen während der Lebensdauer einer Anwendung hinzugefügt, entfernt und geändert werden. Mithilfe von [Durable Functions](durable-functions-overview.md) (Langlebige Funktionen) können Funktionen auf eine Art und Weise verkettet werden, die vorher nicht möglich war. Diese Verkettung wirkt sich darauf aus, wie Sie die Versionsverwaltung nutzen können.

## <a name="how-to-handle-breaking-changes"></a>Behandeln von wichtigen Änderungen

Es gibt einige Beispiele für wichtige Änderungen, mit denen Sie vertraut sein sollten. In diesem Artikel werden die gängigsten Änderungen beschrieben. Für alle Änderungen gilt der Grundsatz, dass sich sowohl durch neue als auch durch vorhandene Funktionsorchestrierungen Auswirkungen auf den Funktionscode ergeben.

### <a name="changing-activity-or-entity-function-signatures"></a>Ändern der Signaturen von Aktivitäts- oder Entitätsfunktionen

Eine Signaturänderung bezieht sich auf eine Änderung des Namens, der Eingabe oder der Ausgabe einer Funktion. Wenn an einer Aktivitäts- oder Entitätsfunktion diese Art von Änderung vorgenommen wird, kann dies für jegliche davon abhängige Orchestratorfunktion zu einem Fehler führen. Wenn Sie die Orchestratorfunktion aktualisieren, um diese Änderung zu berücksichtigen, kann dies wiederum zu Fehlern bei vorhandenen ausgeführten Instanzen führen.

Angenommen, Sie verfügen beispielsweise über die folgende C#-Orchestratorfunktion.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Bei dieser einfachen Funktion werden die Ergebnisse von **Foo** an **Bar** übergeben. Angenommen, wir müssen den Rückgabewert von **Foo** von `bool` in `string` ändern, um mehr Ergebniswerte zu unterstützen. Das Ergebnis sieht wie folgt aus:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string result = await context.CallActivityAsync<string>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Diese Änderung funktioniert für alle neuen Instanzen der Orchestratorfunktion ohne Probleme, aber für ausgeführte Instanzen treten Fehler auf. Nehmen Sie beispielsweise den Fall, dass eine Orchestrierungsinstanz eine Funktion namens `Foo` aufruft, einen booleschen Wert zurückerhält und dann einen Prüfpunkt einrichtet. Wenn die Signaturänderung an diesem Punkt bereitgestellt wird, tritt für die Instanz mit dem Prüfpunkt sofort ein Fehler auf, wenn der Vorgang fortgesetzt und der Aufruf von `Foo` wiedergegeben wird. Dieser Fehler tritt auf, weil das Ergebnis in der Verlaufstabelle `bool` ist, aber der Code versucht, es in `string` zu deserialisieren, was zu einer Runtime-Ausnahme für typsichere Sprachen führt.

Dieses Beispiel zeigt nur eine vieler verschiedener Möglichkeiten dafür, wie eine Funktionssignaturänderung zu Fehlern bei vorhandenen Instanzen führen kann. Wenn ein Orchestrator die Vorgehensweise zum Aufrufen einer Funktion ändern muss, ist die Änderung im Allgemeinen problematisch.

### <a name="changing-orchestrator-logic"></a>Ändern der Orchestratorlogik

Die andere Klasse der Versionsverwaltungsprobleme basiert auf der Änderung des Orchestratorfunktionscodes auf einer Weise, durch die der Ausführungspfad für ausgeführte Instanzen geändert wird.

Betrachten Sie die folgende C#-Orchestratorfunktion:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Angenommen, Sie nehmen eine Änderung vor, um einen neuen Funktionsaufruf zwischen den zwei vorhandenen Funktionsaufrufen hinzuzufügen.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    if (result)
    {
        await context.CallActivityAsync("SendNotification");
    }

    await context.CallActivityAsync("Bar", result);
}
```

Mit dieser Änderung wird zwischen *Foo* und *Bar* ein neuer Funktionsaufruf von *SendNotification* hinzugefügt. Es werden keine Signaturänderungen vorgenommen. Das Problem tritt auf, wenn die Ausführung einer vorhandenen Instanz nach dem Aufruf von *Bar* fortgesetzt wird. Wenn für den ursprünglichen Aufruf von *Foo* das Ergebnis `true` zurückgegeben wurde, wird bei der Wiedergabe des Orchestrators das *SendNotification*-Element aufgerufen, das im dazugehörigen Ausführungsverlauf nicht enthalten ist. Das Durable Task Framework erkennt diese Inkonsistenz und löst eine `NonDeterministicOrchestrationException`-Ausnahme aus, da ein Aufruf von *SendNotification* ermittelt wurde, obwohl ein Aufruf von *Bar* erwartet wurde. Die gleiche Art von Problem kann beim Hinzufügen von API-Aufrufen zu anderen dauerhaften Vorgängen auftreten, z. B. beim Erstellen von dauerhaften Timern, beim Warten auf externe Ereignisse, beim Aufrufen untergeordneter Orchestrierungen und mehr.

## <a name="mitigation-strategies"></a>Lösungsstrategien

Hier sind einige Strategien zum Umgang mit Problemen bei der Versionsverwaltung angegeben:

* Keine Maßnahmen ergreifen (nicht empfohlen)
* Beendigung aller ausgeführten Instanzen
* Parallele Bereitstellungen

### <a name="do-nothing"></a>Keine Maßnahmen

Der naive Ansatz für die Versionsverwaltung besteht darin, keine Maßnahmen zu ergreifen und den Ausfall aktiver Orchestrierungsinstanzen zuzulassen. Je nach Art von Änderung können die folgenden Fehlertypen auftreten.

* Orchestrierungen können mit einem `NonDeterministicOrchestrationException`-Fehler fehlschlagen.
* Orchestrierungen reagieren möglicherweise auf unbestimmte Zeit nicht und melden den Status `Running`.
* Wenn eine Funktion entfernt wird, führt jede Funktion, die versucht, diese aufzurufen, zu einem Fehler.
* Wenn eine Funktion entfernt wird, nachdem ihre Ausführung geplant wurde, können Runtime-Fehler auf niedriger Ebene in der Durable Task Framework-Engine auftreten, was möglicherweise zu schwerwiegenden Leistungsbeeinträchtigungen führt.

Aufgrund dieser möglicher Fehler wird von der Strategie, keine Maßnahmen zu ergreifen, dringend abgeraten.

### <a name="stop-all-in-flight-instances"></a>Beendigung aller ausgeführten Instanzen

Eine andere Möglichkeit besteht darin, alle ausgeführten Instanzen zu beenden. Wenn Sie den [Azure Storage-Standardanbieter für Durable Functions](durable-functions-storage-providers.md#azure-storage) verwenden, können Sie alle Instanzen beenden, indem Sie die Inhalte der internen Warteschlangen **control-queue** und **workitem-queue** löschen. Alternativ können Sie die Funktions-App beenden, diese Warteschlangen löschen und die App neu starten. Die Warteschlangen werden automatisch neu erstellt, sobald die App neu gestartet wird. Die vorherigen Orchestrierungsinstanzen können auf unbegrenzte Zeit den Status „Running“ (Wird ausgeführt) aufweisen, allerdings überladen diese Ihre Protokolle nicht mit Fehlermeldungen und beeinträchtigen Ihre App nicht. Dieser Ansatz eignet sich bestens für die schnelle Prototypentwicklung sowie für die lokale Entwicklung.

> [!NOTE]
> Für diesen Ansatz ist Direktzugriff auf die zugrunde liegenden Speicherressourcen erforderlich. Er eignet sich womöglich nicht für alle Speicheranbieter, die von Durable Functions unterstützt werden.

### <a name="side-by-side-deployments"></a>Parallele Bereitstellungen

Die sicherste Möglichkeit zum Sicherstellen, dass wichtige Änderungen auf sichere Weise bereitgestellt werden, ist die parallele Bereitstellung mit Ihren älteren Versionen. Hierfür können Sie die folgenden Verfahren verwenden:

* Bereitstellen aller Updates als völlig neue Funktionen, wobei bestehende Funktionen unverändert bleiben. Dies wird im Allgemeinen aufgrund der Komplexität rekursiver Updates für die Aufrufer neuer Funktionsversionen nicht empfohlen.
* Bereitstellen aller Updates als neue Funktionen-App mit einem anderen Speicherkonto
* Stellen Sie eine neue Kopie der Funktions-App mit demselben Speicherkonto, aber mit einem aktualisierten Namen für den [Aufgabenhub](durable-functions-task-hubs.md) bereit. Dies führt zur Erstellung neuer Speicherartefakte, die von der neuen Version Ihrer App verwendet werden können. Die alte Version Ihrer App wird weiterhin mit den vorherigen Speicherartefakten ausgeführt.

Für die Bereitstellung neuer Versionen Ihrer Funktions-Apps wird die parallele Bereitstellung empfohlen.

> [!NOTE]
> In dieser Anleitung zur parallelen Bereitstellungsstrategie werden Begriffe verwendet, die sich spezifisch auf Azure Storage beziehen, sie gilt jedoch allgemein für alle unterstützten [Durable Functions-Speicheranbieter](durable-functions-storage-providers.md).

### <a name="deployment-slots"></a>Bereitstellungsslots

Für die Durchführung paralleler Bereitstellungen in Azure Functions oder Azure App Service wird empfohlen, dass Sie die neue Version der Funktions-App in einem neuen [Bereitstellungsslot](../functions-deployment-slots.md) bereitstellen. Mit Bereitstellungsslots können Sie mehrere Kopien Ihrer Funktionen-App parallel ausführen, wobei nur eine davon als aktiver *Produktionsslot* dient. Wenn alles bereit ist, um die neue Orchestrierungslogik für Ihre vorhandene Infrastruktur verfügbar zu machen, kann dies ein einfacher Vorgang sein, weil ggf. nur die neue Version in den Produktionsslot eingefügt werden muss.

> [!NOTE]
> Diese Strategie eignet sich am besten, wenn Sie HTTP und Webhooktrigger für Orchestratorfunktionen verwenden. Für andere Trigger als HTTP-Trigger, z.B. Warteschlangen oder Event Hubs, sollte die Triggerdefinition [von einer App-Einstellung abgeleitet sein](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings), die im Rahmen des Austauschvorgangs aktualisiert wird.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Informationen zum Umgang mit Leistungs- und Skalierungsproblemen](durable-functions-perf-and-scale.md)
