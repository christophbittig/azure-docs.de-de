---
title: Parallelität in Azure Functions
description: Eine Übersicht über die Funktion für dynamische Parallelität in Azure Functions.
author: cachai2
ms.topic: conceptual
ms.date: 9/24/2021
ms.author: cachai
ms.openlocfilehash: 21702bf5bdc8eeee014305c106006a18640c001f
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131847658"
---
# <a name="concurrency-in-azure-functions"></a>Parallelität in Azure Functions

In diesem Artikel wird das Parallelverhalten von ereignisgesteuerten Triggern in Azure Functions beschrieben. Es wird außerdem ein neues dynamisches Modell zum Optimieren des Parallelitätsverhaltens beschrieben. 

>[!NOTE]
>Das dynamische Parallelitätsmodell befindet sich derzeit in der Vorschau. Unterstützung für dynamische Parallelität ist auf bestimmte Bindungserweiterungen beschränkt.

Das Hostingmodell für Azure Functions ermöglicht die gleichzeitige Ausführung mehrerer Funktionsaufrufe auf einer einzelnen Compute-Instanz. Stellen Sie sich beispielsweise einen Fall vor, in dem Ihre Funktions-App drei verschiedene Funktionen aufskaliert und auf mehreren Instanzen ausgeführt wird. In diesem Szenario verarbeitet jede Funktion Aufrufe auf jeder Instanz des virtuellen Computers, auf der Ihre Funktions-App ausgeführt wird. Die Funktionsaufrufe auf einer einzelnen Instanz teilen sich die gleichen Computeressourcen des virtuellen Computers, wie den Arbeitsspeicher, die CPU und die Verbindungen. Wenn Ihre App in einem dynamischen Plan (Verbrauch oder Premium) gehostet wird, skaliert die Plattform die Anzahl der Funktions-App-Instanzen basierend auf der Anzahl der eingehenden Ereignisse hoch oder herunter. Weitere Informationen finden Sie unter [Ereignisgesteuerte Skalierung](./Event-Driven-Scaling.md). Wenn Sie Ihre Funktionen in einem dedizierten Plan (App Service) hosten, konfigurieren Sie Ihre Instanzen manuell oder [richten ein Schema für die automatische Skalierung ein](dedicated-plan.md#scaling).

Da mehrere Funktionsaufrufe gleichzeitig auf jeder Instanz ausgeführt werden können, muss jede Funktion eine Möglichkeit haben, die Anzahl gleichzeitiger Aufrufe zu drosseln, die sie zu einem bestimmten Zeitpunkt verarbeitet.

## <a name="static-concurrency"></a>Statische Parallelität

Viele der Trigger unterstützen ein statisches Konfigurationsmodell auf der Hostebene, das verwendet wird, um instanzspezifische Parallelität für diesen Triggertyp anzugeben. Beispielsweise stellt der [Service Bus-Trigger](./functions-bindings-service-bus-trigger.md) sowohl eine `MaxConcurrentCalls` als auch eine `MaxConcurrentSessions` Einstellung in der Datei [host.json](functions-host-json.md) zur Verfügung. Diese Einstellungen steuern zusammen die maximale Anzahl von Nachrichten, die jede Funktion gleichzeitig auf jeder Instanz verarbeitet. Andere Triggertypen verfügen über integrierte Mechanismen für den Instanzübergreifenden Lastenausgleich von Aufrufen. Beispielsweise verwenden Event Hubs und Azure Cosmos DB ein partitionsbasiertes Schema. 

Für Triggertypen, die die Parallelitätskonfiguration unterstützen, gibt es ein Standardverhalten, das Sie in der Datei „host.json“ für Ihre Funktions-App überschreiben können. Mit diesen Einstellungen, die für alle ausgeführten Instanzen gelten, können Sie die maximale Parallelität für Ihre Funktionen auf jeder Instanz steuern. Wenn Ihre Funktion beispielsweise CPU- oder ressourcenintensiv ist, können Sie die Parallelität einschränken, um Ihre Instanzen fehlerfrei zu halten. Sie sollten gegebenenfalls auch beim Senden von Anforderungen an einen Downstreamdienst, der gedrosselt wird, die Parallelität einschränken. 

Mit solchen Parallelitätskonfigurationen können Sie zwar bestimmte Triggerverhalten steuern, z. B. die Drosselung Ihrer Funktionen, aber es kann schwierig sein, die optimalen Werte für diese Einstellungen zu bestimmen. In der Regel müssen Sie über einen Test- und Fehlerprozess zu akzeptablen Werten der Auslastungstests gelangen. Selbst wenn Sie eine Reihe von Werten festlegen, die für ein bestimmtes Lastprofil geeignet sind, kann sich die Anzahl der Ereignisse, die von den verbundenen Diensten eingehen, von Tag zu Tag ändern. Diese Variabilität bedeutet, dass Ihre App häufig mit suboptimalen Werten ausgeführt wird. Beispielsweise kann Ihre Funktions-App besonders anspruchsvolle Nachrichtennutzlasten am letzten Tag der Woche verarbeiten. Daher müssen Sie die Parallelität drosseln. Während der restlichen Woche sind die Nachrichtennutzlasten jedoch einfacher, was bedeutet, dass Sie für den Rest der Woche eine höhere Parallelitätsstufe verwenden können. 

Im Idealfall soll das System es den Instanzen ermöglichen, so viel Arbeit wie möglich zu verarbeiten, während jede Instanz fehlerfrei bleibt und die Latenzen gering bleiben.Das ist wofür die dynamischen Parallelität entwickelt wurde.

## <a name="dynamic-concurrency-preview"></a>Dynamische Parallelität (Vorschau)

Functions bietet jetzt ein dynamisches Parallelitätsmodell, das die Konfiguration der Parallelität für alle Funktions-Apps vereinfacht, die im gleichen Plan ausgeführt werden. 

> [!NOTE]
> Dynamische Parallelität wird derzeit nur für Azure-Blob-, Azure Queue- und Service Bus-Trigger unterstützt und erfordert, dass Sie die Versionen verwenden, die im [Abschnitt zur Erweiterungsunterstützung unten](#extension-support) aufgeführt werden.

### <a name="benefits"></a>Vorteile

Die Verwendung der dynamischen Parallelität bietet die folgenden Vorteile: 

- Eine **vereinfachte Konfiguration**: Sie müssen die Parallelitätseinstellungen pro Trigger nicht mehr manuell bestimmen. Das System lernt im Laufe der Zeit die optimalen Werte für Ihre Workload. 
- **Dynamische Anpassungen**: Parallelität wird dynamisch in Echtzeit nach oben oder unten angepasst. Dadurch kann das System im Laufe der Zeit die sich ändernden Auslastungsmuster übernehmen. 
- Der **Instanz-Integritätsschutz**: Die Runtime schränkt die Parallelität auf Ebenen ein, die eine Funktions-App-Instanz bequem verarbeiten kann. Dies schützt die App davor, sich selbst zu überlasten, indem sie mehr Arbeit auf sich nimmt, als sie sollte. 
- Ein **Verbesserter Durchsatz**: Der Gesamtdurchsatz wird verbessert, da die einzelnen Instanzen nicht mehr Arbeit bewältigen müssen, als sie schnell verarbeiten können. Das ermöglicht einen effektiveren Lastenausgleich über Instanzen hinweg. Bei Funktionen, die höhere Auslastungen verarbeiten können, kann die Parallelität auf Werte erhöht werden, die über die Standardkonfigurationswerte hinausgehen. Das führt zu einem höheren Durchsatz.

### <a name="dynamic-concurrency-configuration"></a>Konfigurationen für die Dynamische Parallelität

Die Dynamische Parallelität kann auf der Hostebene in der Datei host.json aktiviert werden. Wenn sie aktiviert ist, passen alle Bindungserweiterungen, die von Ihrer Funktions-App verwendet werden und dynamische Parallelität unterstützen, die Parallelität bei Bedarf dynamisch an. Die Dynamischen Parallelitätseinstellungen überschreiben alle manuell konfigurierten Parallelitätseinstellungen für Trigger, die dynamische Parallelität unterstützen. 

Standardmäßig ist die dynamische Parallelität deaktiviert. Bei aktivierter dynamischer Parallelität beginnt die Parallelität für jede Funktion bei 1 und wird auf einen optimalen Wert angepasst, der vom Host bestimmt wird.

Sie können die dynamische Parallelität in Ihrer Funktions-App aktivieren, indem Sie die folgenden Einstellungen der Datei „host.json“ hinzufügen: 

```json
    { 
        "version": "2.0", 
        "concurrency": { 
            "dynamicConcurrencyEnabled": true, 
            "snapshotPersistenceEnabled": true 
        } 
    } 
```

 Wenn der Standardwert `SnapshotPersistenceEnabled` `true` entspricht, werden die gelernten Parallelitätswerte in regelmäßigen Abständen gespeichert. Somit können die neuen Instanzen mit diesen Werten beginnen, anstatt bei 1 und das Lernen wiederholen zu müssen. 

### <a name="concurrency-manager"></a>Parallelitäts-Manager 

Wenn die dynamische Parallelität aktiviert ist, läuft hinter den Kulissen ein Parallelitäts-Verwaltungsprozess im Hintergrund. Dieser Verwalter überwacht ständig die Integritätsmetriken für Instanzen, wie z. B. die CPU- und Threadauslastung und ändert die Drosselungen bei Bedarf. Wenn eine oder mehrere Drosselungen aktiviert sind, wird die Funktionsparallelität nach unten angepasst, bis der Host wieder fehlerfrei ist. Wenn Drosselungen deaktiviert sind, kann die Parallelität erhöht werden. Verschiedene Heuristiken werden verwendet, um die Parallelität basierend auf diesen Drosselungen intelligent nach oben oder unten anzupassen. Im Laufe der Zeit wird die Parallelität für jede Funktion auf eine bestimmte Ebene stabilisiert. 

Die Parallelitätsebenen werden für jede einzelne Funktion verwaltet. Daher wird das System zwischen ressourcenintensiven Funktionen, die eine geringe Parallelität erfordern und einfacheren Funktionen, die eine höhere Parallelität verarbeiten können, ausgeglichen. Das Gleichgewicht der Parallelität für jede Funktion hilft, die allgemeine Integrität der Funktions-App-Instanz zu erhalten.  

Wenn die dynamische Parallelität aktiviert ist, werden die dynamischen Parallelitätsentscheidungen in Ihren Protokollen angezeigt. Beispielsweise werden Protokolle angezeigt, wenn verschiedene Drosselungen aktiviert sind und wann immer die Parallelität für jede Funktion nach oben oder unten angepasst wird. Diese Protokolle werden in der Ablaufverfolgungstabelle unter der Protokollkategorie **Host.Concurrency** geschrieben. 

### <a name="extension-support"></a>Unterstützung für Erweiterung 

Die dynamische Parallelität ist für eine Funktions-App auf der Hostebene aktiviert und alle Erweiterungen, die die dynamische Parallelität unterstützen, werden in diesem Modus ausgeführt. Die dynamische Parallelität erfordert die Zusammenarbeit zwischen dem Host und den einzelnen Triggererweiterungen. Für die Vorschauversion unterstützen nur die aufgeführten Versionen der folgenden Erweiterungen dynamische Parallelität.

#### <a name="azure-queues"></a>Azure-Warteschlangen

Der Azure Queue Storage-Trigger verfügt über eine eigene Schleife zum Abfragen von Nachrichten. Bei Verwendung der statischen Konfiguration wird Parallelität durch die `BatchSize`/`NewBatchThreshold`-Konfigurationsoption bestimmt. Bei Verwendung dynamischer Parallelität werden diese Konfigurationswerte ignoriert. Dynamische Parallelität ist in die Nachrichtenschleife integriert, sodass die Anzahl der pro Iteration abgerufenen Nachrichten dynamisch angepasst wird. Wenn Drosselung aktiviert ist (Host ist überlastet), wird die Nachrichtenverarbeitung angehalten, bis Drosselung deaktiviert wird. Wenn Drosselungen deaktiviert sind, kann die Parallelität erhöht werden.

Um dynamische Parallelität für Warteschlangen zu verwenden, müssen Sie [Version 5.x](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) der Speichererweiterung verwenden.

#### <a name="azure-blobs"></a>Azure-Blobs

Intern verwendet der Azure Blob Storage-Trigger die gleiche Infrastruktur wie der Azure-Warteschlangentrigger. Wenn neue/aktualisierte Blobs verarbeitet werden müssen, werden Nachrichten in eine durch die Plattform verwaltete Steuerungswarteschlange geschrieben, und diese Warteschlange wird mit der gleichen Logik verarbeitet, die auch für QueueTrigger verwendet wird. Wenn dynamische Parallelität aktiviert ist, wird Parallelität für die Verarbeitung dieser Steuerungswarteschlange dynamisch verwaltet.

Um dynamische Parallelität für Blobs zu verwenden, müssen Sie [Version 5.x](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) der Speichererweiterung verwenden.


#### <a name="service-bus"></a>Service Bus 

Der Service Bus Trigger unterstützt derzeit drei Ausführungsmodelle. Die dynamische Parallelität wirkt sich wie folgt auf diese Ausführungsmodelle aus:
 
- **Verarbeitung einzelner Dispatch-Themen/Warteschlangen**: Jeder Aufruf Ihrer Funktion verarbeitet eine einzelne Nachricht. Bei Verwendung der statischen Konfiguration wird die Parallelität durch die Konfigurationsoption MaxConcurrentCalls bestimmt. Bei Verwendung der dynamischen Parallelität wird dieser Konfigurationswert ignoriert und die Parallelität wird dynamisch angepasst. 
- **Sitzungsbasierte Verarbeitung einzelner Dispatch-Themen/Warteschlangen**: Jeder Aufruf Ihrer Funktion verarbeitet eine einzelne Nachricht. Abhängig von der Anzahl der aktiven Sitzungen für Ihr Thema / Ihre Warteschlange least jede Instanz eine oder mehrere Sitzungen. Nachrichten in jeder Sitzung werden seriell verarbeitet, um die Reihenfolge in einer Sitzung zu gewährleisten. Wenn keine dynamische Parallelität verwendet wird, wird die Parallelität durch die `MaxConcurrentSessions`-Einstellung bestimmt. Wenn dynamische Parallelität aktiviert ist, wird `MaxConcurrentSessions` ignoriert, und die Anzahl der Sitzungen, die von jeder Instanz verarbeitet werden, wird dynamisch angepasst. 
- **Batchverarbeitung**: Bei jedem Aufruf Ihrer Funktion wird ein Nachrichtenbatch verarbeitet, der durch die Einstellung `MaxMessageCount` bestimmt wird. Da Batchaufrufe seriell sind, ist die Parallelität für Ihre durch den Batch ausgelöste Funktion immer eins und die dynamische Parallelität gilt nicht. 

Damit Ihr Service Bus-Trigger dynamische Parallelität verwenden kann, müssen Sie [Version 5.x](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus) der Service Bus-Erweiterung verwenden. 

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Bewährte Methoden für Azure Functions](functions-best-practices.md)
* [Entwicklerreferenz zu Azure Functions](functions-reference.md)
* [Trigger und Bindungen in Azure Functions](event-driven-scaling.md)
