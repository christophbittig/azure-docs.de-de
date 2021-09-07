---
title: Leistung und Skalierbarkeit in Durable Functions – Azure
description: Erfahren Sie mehr über die einmaligen Skalierungsmerkmale der Durable Functions-Erweiterung für Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 05/13/2021
ms.author: azfuncdf
ms.openlocfilehash: 179eb4c5c380a65374143cf24b2f960458fe62b5
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/16/2021
ms.locfileid: "112240450"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Leistung und Skalierbarkeit in Durable Functions (Azure Functions)

Um die Leistung und Skalierbarkeit zu optimieren, ist es wichtig die eindeutigen Merkmale von [Durable Functions](durable-functions-overview.md) zu verstehen.

## <a name="azure-storage-provider"></a>Azure Storage-Anbieter

Die Standardkonfiguration für Durable Functions speichert diesen Laufzeitstatus in einem Azure Storage-Konto (klassisch). Die gesamte Funktionsausführung wird durch Azure Storage Warteschlangen gesteuert. Orchestrierungs- und Entitätsstatus und -verlauf werden in Azure-Tabellen gespeichert. Azure-Blobs und Blob-Leases werden verwendet, um Orchestrierungsinstanzen und Entitäten auf mehrere App-Instanzen (auch als *Worker* oder einfach *VMs* bezeichnet) zu verteilen. In diesem Abschnitt werden die verschiedenen Azure Storage Artefakte und deren Auswirkungen auf Leistung und Skalierbarkeit ausführlicher erläutert.

> [!NOTE]
> Dieses Dokument konzentriert sich in erster Linie auf die Leistung und Skalierbarkeitsmerkmale von Durable Functions unter Verwendung des Standardanbieters Azure Storage. Es sind jedoch auch andere Speicheranbieter verfügbar. Weitere Informationen zu den unterstützten Speicheranbietern für Durable Functions und deren Vergleich finden Sie unter [Durable Functions-Speicheranbieter](durable-functions-storage-providers.md)-Dokumentation.

### <a name="history-table"></a>Verlaufstabelle

Die **Verlaufstabelle** ist eine Azure Storage-Tabelle, die die Verlaufsereignisse für alle Orchestrierungsinstanzen innerhalb eines Aufgabenhubs enthält. Der Name dieser Tabelle hat das Format „*Aufgabenhubname* History“. Während Instanzen ausgeführt werden, werden dieser Tabelle neue Zeilen hinzugefügt. Der Partitionsschlüssel dieser Tabelle wird von der Instanzen-ID der Orchestrierung abgeleitet. Instanz-IDs sind standardmäßig zufällig und stellen eine optimale Verteilung interner Partitionen in Azure Storage sicher. Der Zeilenschlüssel für diese Tabelle ist eine Sequenznummer, die zum Sortieren der Verlaufsereignisse verwendet wird.

Wenn eine Orchestrierungsinstanz ausgeführt werden muss, werden die entsprechenden Zeilen der Verlaufstabelle mithilfe einer Bereichsabfrage innerhalb einer einzelnen Tabellenpartition in den Arbeitsspeicher geladen. Diese *Verlaufsereignisse* werden dann im Orchestratorfunktionscode erneut wiedergegeben, um diesen wieder in den Zustand des vorherigen Prüfpunkts zu versetzen. Die Verwendung des Ausführungsverlaufs zum Neuerstellen des Zustands auf diese Weise hängt vom [Ereignissourcingmuster](/azure/architecture/patterns/event-sourcing) ab.

> [!TIP]
> In der Tabelle Verlauf gespeicherte Daten der Orchestrierung enthalten Ausgabenutzlasten von Aktivitäts- und Unterorchestrierungsfunktionen. Nutzlasten von externen Ereignissen werden auch in der Verlaufstabelle gespeichert. Da der vollständige Verlauf jedes Mal in den Arbeitsspeicher geladen wird, wenn ein Orchestrator ausgeführt werden muss, kann ein ausreichend großer Verlauf zu einer erheblichen Arbeitsspeicherauslastung auf einem bestimmten virtuellen Computer führen. Die Länge und Größe des Orchestrierungsverlaufs kann reduziert werden, indem große Orchestrationen in mehrere Sub-Orchestrationen aufgeteilt werden oder indem die Größe der Ausgaben reduziert wird, die von der Aktivität und den von ihr aufgerufenen Sub-Orchestratorfunktionen zurückgegeben werden. Alternativ können Sie die Speicherauslastung verringern, indem Sie die [Parallelitätsdrosselungen](#concurrency-throttles) pro virtuellem Computer verringern, um zu begrenzen, wie viele Orchestrierungen gleichzeitig in den Arbeitsspeicher geladen werden.

### <a name="instances-table"></a>Instanzentabellen

Die **Instanzentabelle** enthält die Statuswerte aller Orchestrierungs- und Entitätsinstanzen innerhalb eines Aufgabenhubs. Während der Erstellung von Instanzen werden dieser Tabelle neue Zeilen hinzugefügt. Der Partitionsschlüssel dieser Tabelle ist die Orchestrierungsinstanz-ID oder der Entitätsschlüssel, und der Zeilenschlüssel ist eine leere Zeichenfolge. Es gibt eine Zeile pro Orchestrierungs- oder Entitätsinstanz.

Diese Tabelle wird verwendet, um [Instanzabfrageanforderungen vom Code](durable-functions-instance-management.md#query-instances) sowie die [HTTP-Statusabfrage-API](durable-functions-http-api.md#get-instance-status) zu erfüllen. Sie wird letztendlich konsistent mit dem Inhalt der oben genannten **Verlaufstabelle** gehalten. Die Verwendung einer separaten Azure Storage-Tabelle zur wirksamen Erfüllung von Instanzabfragevorgängen auf diese Weise hängt vom [CQRS-Muster (Command and Query Responsibility Segregation)](/azure/architecture/patterns/cqrs) ab.

> [!TIP]
> Durch die Partitionierung der *Instanztabelle* können Millionen von Orchestrierungsinstanzen gespeichert werden, ohne dass sich dies auf die Laufzeitleistung oder -skalierung auswirken kann. Die Anzahl der Instanzen kann sich jedoch erheblich auf die Leistung von [Abfragen mit mehreren Instanzen](durable-functions-instance-management.md#query-all-instances) auswirken. Um die Menge der in diesen Tabellen gespeicherten Daten zu steuern, sollten Sie in regelmäßigen Abständen alte [Instanzdaten bereinigen](durable-functions-instance-management.md#purge-instance-history).

### <a name="internal-queue-triggers"></a>Interne Warteschlangentrigger

Sowohl Orchestrator-, Entitäts- als auch Aktivitätsfunktionen werden durch interne Warteschlangen im Aufgabenhub der Funktions-App ausgelöst. Diese Verwendung von Warteschlangen bietet eine zuverlässige „At-Least-Once“-Garantie bei der Nachrichtenübermittlung. Es gibt zwei Arten von Warteschlangen in Durable Functions: die **Steuerelement-Warteschlange** ( control queue) und die **Warteschlange für Arbeitsaufgaben** (work-item queue).

#### <a name="the-work-item-queue"></a>Warteschlange für Arbeitsaufgaben

In Durable Functions gibt es eine Warteschlange für Arbeitsaufgaben pro Aufgabenhub. Dies ist eine grundlegende Warteschlange, und sie verhält sich ähnlich wie jede andere `queueTrigger`-Warteschlange in Azure Functions. Diese Warteschlange wird verwendet, um zustandslose *Aktivitätsfunktionen* auszulösen. Dabei wird jeweils eine einzelne Nachricht aus der Warteschlange entfernt. Jede dieser Nachrichten enthält Eingaben der Aktivitätsfunktionen und zusätzliche Metadaten, z.B. welche Funktion auszuführen ist. Wenn eine Durable Functions-Anwendung für mehrere virtuelle Computer horizontal hochskaliert wird, konkurrieren alle diese virtuellen Computer miteinander, um Arbeitsaufgaben aus der Warteschlange für Arbeitsaufgaben abzurufen.

#### <a name="control-queues"></a>Steuerelement-Warteschlange(n)

In Durable Functions gibt es mehrere *Steuerelement-Warteschlangen* pro Aufgabenhub. Eine *Steuerelement-Warteschlange* ist komplexer als die einfachere Warteschlange für Arbeitsaufgaben. Steuerwarteschlangen werden verwendet, um zustandsbehaftete Orchestrator- und Entitätsfunktionen auszulösen. Da es sich bei den Orchestrator- und Entitätsfunktionsinstanzen um zustandsbehaftete Singletons handelt, ist es wichtig, dass jede Orchestrierung oder Entität nur von einem Worker gleichzeitig verarbeitet wird. Um diese Einschränkung zu erreichen, wird jede Orchestrierungsinstanz oder -entität einer einzelnen Steuerungswarteschlange zugewiesen. Für diese Steuerungswarteschlangen wird ein Lastenausgleich über Worker hinweg vorgenommen, um sicherzustellen, dass jede Warteschlange jeweils nur von einem Worker verarbeitet wird. Weitere Informationen zu diesem Verhalten finden Sie in den folgenden Abschnitten.

Steuerelement-Warteschlangen enthalten eine Vielzahl von Orchestrierung-Lebenszyklus-Nachrichtentypen. Beispiele hierfür sind [orchestrator control messages](durable-functions-instance-management.md) (Orchestratorsteuerungsmeldungen), Aktivitätsfunktion-*Antwort*-Nachrichten und Timernachrichten. Bis zu 32 Nachrichten werden in einer einzelnen Abfrage aus einer Steuerelement-Warteschlange entfernt. Diese Nachrichten enthalten Nutzlastdaten sowie Metadaten, einschließlich der Orchestrierungsinstanz, für die eine Nachricht jeweils vorgesehen ist. Wenn mehrere aus der Warteschlange entfernte Nachrichten für dieselbe Orchestrierungsinstanz vorgesehen sind, werden sie als Batch verarbeitet.

Steuerungswarteschlangennachrichten werden ständig mithilfe eines Hintergrund-Threads abgefragt. Die Batchgröße jeder Warteschlangen-Umfrage wird durch die Einstellung `controlQueueBatchSize` in host.json gesteuert und hat den Standardwert 32 (der von Azure-Warteschlangen unterstützte Höchstwert). Die maximale Anzahl vorab abgerufener Nachrichten der Steuerelementwarteschlange, die im Arbeitsspeicher gepuffert werden, wird durch die Einstellung `controlQueueBufferThreshold` in host.json gesteuert. Der Standardwert für `controlQueueBufferThreshold` variiert abhängig von einer Vielzahl von Faktoren, einschließlich der Art des Hosting-Plans. Weitere Informationen zu diesen Einstellungen finden Sie in der Dokumentation [host.json Schema](../functions-host-json.md#durabletask).

> [!TIP]
> Durch Erhöhen des Werts für `controlQueueBufferThreshold` kann eine einzelne Orchestrierung oder Entität Ereignisse schneller verarbeiten. Die Erhöhung dieses Werts kann jedoch auch zu einer höheren Speicherauslastung führen. Die höhere Speicherauslastung ist zum Teil darauf zurückzuführen, dass mehr Nachrichten aus der Warteschlange abgerufen werden und zum Teil darauf, dass mehr Orchestrierungsverläufe in den Arbeitsspeicher abgerufen werden. Das Reduzieren des Werts für `controlQueueBufferThreshold` kann daher eine effektive Möglichkeit sein, die Speicherauslastung zu reduzieren.

#### <a name="queue-polling"></a>Abrufen von Warteschlangen

Die Durable Task-Erweiterung implementiert einen zufälligen exponentiellen Backoffalgorithmus, um die Auswirkungen des Abrufs von Warteschlangen im Leerlauf auf Speichertransaktionskosten zu reduzieren. Wenn eine Nachricht gefunden wird, überprüft die Laufzeit sofort ob eine andere Nachricht vorhanden ist. Wenn keine Nachricht gefunden wird, wartet sie für eine Zeit, bevor der Versuch wiederholt wird. Nach aufeinander folgenden fehlerhaften Versuchen, eine Warteschlangennachricht abzurufen, erhöht sich die Wartezeit immer mehr, bis die maximale Wartezeit (standardmäßig eine 30 Sekunden) erreicht ist.

Die maximale Abrufverzögerung kann über die Eigenschaft `maxQueuePollingInterval` in der Datei [host.json](../functions-host-json.md#durabletask) konfiguriert werden. Die Festlegung dieser Eigenschaft auf einen höheren Wert kann zu höherer Latenz bei der Nachrichtenverarbeitung führen. Eine höhere Latenz wird nur nach Zeiträumen ohne Aktivität erwartet. Die Festlegung dieser Eigenschaft auf einen niedrigeren Wert kann aufgrund vermehrter Speichertransaktionen zu [höheren Speicherkosten](durable-functions-billing.md#azure-storage-transactions) führen.

> [!NOTE]
> Bei der Ausführung in Verbrauchs- und Premium-Tarifen von Azure Functions ruft der [Azure Functions-Skalierungscontroller](../event-driven-scaling.md) alle Steuerelement- und Arbeitselement-Warteschlangen einmal alle 10 Sekunden ab. Dieser zusätzliche Abruf ist erforderlich, um zu ermitteln, wann die Instanzen der Funktions-Apps aktiviert und Skalierungsentscheidungen getroffen werden sollen. Zum Zeitpunkt der Erstellung dieses Artikels ist dieses Intervall von 10 Sekunden konstant und kann nicht konfiguriert werden.

#### <a name="orchestration-start-delays"></a>Verzögerungen beim Starten der Orchestrierung
Orchestrierungsinstanzen werden gestartet, indem eine `ExecutionStarted`-Nachricht zu einer der Steuerungswarteschlangen des Taskhubs hinzugefügt wird. Unter bestimmten Umständen sind möglicherweise Verzögerungen von mehreren Sekunden zwischen der geplanten Ausführung einer Orchestrierung und der tatsächlichen Ausführung zu beobachten. Während dieses Zeitraums verbleibt die Orchestrierungsinstanz im Zustand `Pending`. Für diese Verzögerungen gibt es zwei mögliche Gründe:

1. **Steuerungswarteschlangen im Rückstand:** Wenn die Steuerungswarteschlange für diese Instanz eine große Anzahl von Nachrichten enthält, kann es eine Weile dauern, bis die `ExecutionStarted`-Nachricht von der Runtime empfangen und verarbeitet wird. Nachrichtenrückstände können entstehen, wenn Orchestrierungen viele Ereignisse gleichzeitig verarbeiten. Ereignisse, die zur Steuerungswarteschlange hinzugefügt werden, umfassen Startereignisse für Orchestrierungen, den Abschluss von Aktivitäten, permanente Timer, die Beendigung und externe Ereignisse. Wenn diese Verzögerungen unter normalen Umständen auftreten, sollten Sie das Erstellen eines neuen Taskhubs mit einer größeren Anzahl von Partitionen in Erwägung ziehen. Das Konfigurieren von mehr Partitionen bewirkt, dass die Runtime weitere Steuerungswarteschlangen für die Lastverteilung erstellt. Jede Partition entspricht 1:1 einer Steuerungswarteschlange mit maximal 16 Partitionen.

2. **Verzögerungen durch Backoffabruf:** Eine weitere häufige Ursache für Verzögerungen bei Orchestrierungen ist das [zuvor beschriebene Backoffabrufverhalten für Steuerungswarteschlangen](#queue-polling). Diese Verzögerung wird jedoch nur erwartet, wenn eine App auf zwei oder mehr Instanzen aufskaliert wird. Wenn nur eine App-Instanz vorhanden ist oder die App-Instanz, die die Orchestrierung startet, die Instanz ist, die auch die Zielsteuerungswarteschlange abruft, entstehen bei Warteschlangenabrufen keine Verzögerungen. Sie können Verzögerungen durch Backoffabruf reduzieren, indem Sie die Einstellungen für **host.json** wie zuvor beschrieben aktualisieren.

### <a name="storage-account-selection"></a>Auswahl des Speicherkontos

Die in Durable Functions verwendeten Warteschlangen, Tabellen und Blobs werden in einem konfigurierten Azure Storage-Konto erstellt. Das zu verwendende Konto kann über die Einstellung `durableTask/storageProvider/connectionStringName` (oder die Einstellung `durableTask/azureStorageConnectionStringName` in Durable Functions 1.x) in der Datei **host.json** angegeben werden.

#### <a name="durable-functions-2x"></a>Durable Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "connectionStringName": "MyStorageAccountAppSetting"
      }
    }
  }
}
```

#### <a name="durable-functions-1x"></a>Durable Functions 1.x

```json
{
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
    }
  }
}
```

Wenn kein Konto angegeben ist, wird das Standardspeicherkonto `AzureWebJobsStorage` verwendet. Bei leistungsabhängigen Workloads wird jedoch die Konfiguration eines nicht standardmäßigen Speicherkontos empfohlen. In Durable Functions wird Azure Storage stark ausgelastet. Durch die Nutzung eines dedizierten Speicherkontos wird die Speicherverwendung durch Durable Functions von der internen Verwendung durch den Azure Functions-Host getrennt.

> [!NOTE]
> Für die universelle Verwendung des Azure Storage-Anbieters sind allgemeine Azure Storage-Standardkonten erforderlich. Alle anderen Speicherkontotypen werden nicht unterstützt. Es wird dringend empfohlen, universelle Legacy-v1-Speicherkonten für Durable Functions zu verwenden. Die neueren v2-Speicherkonten können für Durable Functions Workloads erheblich teurer sein. Weitere Informationen zu Azure Storage-Kontotypen finden Sie in der Dokumentation unter [Speicherkontoübersicht](../../storage/common/storage-account-overview.md).

### <a name="orchestrator-scale-out"></a>Horizontales Skalieren des Orchestrators

Obwohl Aktivitätsfunktionen unendlich aufskaliert werden können, indem weitere VMs elastisch hinzugefügt werden, sind einzelne Orchestratorinstanzen und -entitäten auf eine einzelne Partition beschränkt, und die maximale Anzahl von Partitionen wird durch die `partitionCount`-Einstellung in Ihrer Datei `host.json` begrenzt. 

> [!NOTE]
> Im Allgemeinen sollten Orchestratorfunktionen einfach sein und keine große Rechenleistung in Anspruch nehmen. Daher ist es nicht erforderlich, eine große Anzahl von Steuerwarteschlange-Partitionen zu erstellen, um einen erhöhten Durchsatz für Orchestrierungen zu erzielen. Der größte Teil der intensiven Vorgänge sollte in zustandslosen Aktivitätsfunktionen erfolgen, die unbegrenzt horizontal hochskaliert werden können.

Die Anzahl der Steuerelement-Warteschlangen wird in der Datei **host.json** definiert. Mit dem folgenden Beispielcodeausschnitt in „host.json“ wird die `durableTask/storageProvider/partitionCount`-Eigenschaft (oder `durableTask/partitionCount` in Durable Functions 1.x) auf `3` festgelegt. Beachten Sie, dass es so viele Steuerungswarteschlangen gibt, wie Partitionen vorhanden sind.

#### <a name="durable-functions-2x"></a>Durable Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "partitionCount": 3
      }
    }
  }
}
```

#### <a name="durable-functions-1x"></a>Durable Functions 1.x

```json
{
  "extensions": {
    "durableTask": {
      "partitionCount": 3
    }
  }
}
```

Ein Aufgabenhub kann mit 1 bis 16 Partitionen konfiguriert werden. Wenn keine Anzahl angegeben ist, werden standardmäßig **4** Partitionen verwendet.

Bei Szenarios mit geringem Datenverkehr wird Ihre Anwendung abskaliert, sodass Partitionen von einer geringen Anzahl von Workern verwaltet werden. Sehen Sie sich als Beispiel das folgende Diagramm an.

![Diagramm der Abskalierung von Orchestrierungen](./media/durable-functions-perf-and-scale/scale-progression-1.png)

Im vorherigen Diagramm ist zu sehen, dass bei den Orchestratoren 1 bis 6 über Partitionen hinweg ein Lastenausgleich erfolgt. Ebenso erfolgt bei Partitionen, ähnlich wie für Aktivitäten, über Worker hinweg ein Lastenausgleich. Der Lastenausgleich bei Partitionen über Worker hinweg erfolgt unabhängig von der Anzahl von Orchestratoren, die gestartet werden.

Wenn Sie den Verbrauchstarif oder den Tarif „Elastisch Premium“ von Azure Functions verwenden, oder wenn Sie Lastenausgleich mit automatischer Skalierung konfiguriert haben, werden mit zunehmendem Datenverkehr mehr Worker zugeordnet, und es erfolgt schließlich ein Lastenausgleich für die Partitionen über alle Worker hinweg. Wenn wir weiterhin aufskalieren, wird schließlich jede Partition von einem einzelnen Worker verwaltet. Für Aktivitäten erfolgt hingegen weiterhin der Lastenausgleich über alle Worker hinweg. Dies ist in der folgenden Abbildung zu sehen.

![Erstes Diagramm aufskalierter Orchestrierungen](./media/durable-functions-perf-and-scale/scale-progression-2.png)

Die Obergrenze der maximalen Anzahl gleichzeitiger _aktiver_ Orchestrierungen *zu jedem Zeitpunkt* ist gleich der Anzahl der Ihrer Anwendung zugeordneten Worker, _multipliziert mit_ Ihrem Wert für `maxConcurrentOrchestratorFunctions`. Diese Obergrenze kann präziser bestimmt werden, wenn Ihre Partitionen vollständig über die Worker hinweg aufskaliert sind. Bei vollständiger Aufskalierung, und weil jeder Worker nur über eine einzelne Functions-Hostinstanz verfügt, entspricht die maximale Anzahl _aktiver_ gleichzeitiger Orchestratorinstanzen Ihrer Anzahl von Partitionen, _multipliziert mit_ Ihrem Wert für `maxConcurrentOrchestratorFunctions`.

> [!NOTE]
> In diesem Kontext bedeutet *aktiv*, dass eine Orchestrierung oder Entität in den Arbeitsspeicher geladen wird und *neue Ereignisse* verarbeitet. Wenn die Orchestrierung oder Entität auf weitere Ereignisse wartet, z. B. den Rückgabewert einer Aktivitätsfunktion, wird sie aus dem Arbeitsspeicher entladen und nicht mehr als *aktiv* betrachtet. Orchestrierungen und Entitäten werden anschließend nur dann erneut in den Arbeitsspeicher geladen, wenn neue Ereignisse verarbeitet werden müssen. Es gibt praktisch keine *totale* maximale Anzahl von Gesamtorchestrierungen oder Entitäten, die auf einem einzelnen virtuellen Computer ausgeführt werden können, auch wenn sie sich alle im Status „Wird ausgeführt“ befinden. Die einzige Einschränkung ist die Anzahl *gleichzeitig aktiver* Orchestrierungs- oder Entitätsinstanzen.

Die folgende Abbildung veranschaulicht ein vollständig aufskaliertes Szenario, in dem weitere Orchestratoren hinzugefügt werden, wovon aber einige inaktiv sind (grau dargestellt).

![Zweites Diagramm aufskalierter Orchestrierungen](./media/durable-functions-perf-and-scale/scale-progression-3.png)

Bei der horizontalen Skalierung werden Steuerungswarteschlangen-Leases möglicherweise neu über Functions-Host-Instanzen verteilt, um sicherzustellen, dass Partitionen gleichmäßig verteilt werden. Diese Leases werden intern als Blob Storage-Leases implementiert und stellen sicher, dass jede einzelne Orchestrierungsinstanz oder -entität jeweils nur in einer einzelnen Hostinstanz ausgeführt wird. Wenn ein Aufgabenhub mit drei Partitionen (und somit drei Steuerungswarteschlangen) konfiguriert ist, kann der Lastenausgleich für die Orchestrierungsinstanzen und -entitäten über alle drei Lease-Hostinstanzen ausgeführt werden. Zusätzliche virtuelle Computer können zum Erhöhen der Kapazität für die Ausführung der Aktivitätsfunktion hinzugefügt werden.

Das folgende Diagramm veranschaulicht, wie der Azure Functions-Host mit den Speicherentitäten in einer horizontal skalierten Umgebung interagiert.

![Skalierungsdiagramm](./media/durable-functions-perf-and-scale/scale-interactions-diagram.png)

Wie im Diagramm oben dargestellt, konkurrieren alle virtuellen Computer um Nachrichten in der Warteschlange für Arbeitsaufgaben. Allerdings können nur drei virtuelle Computer Nachrichten aus Steuerelement-Warteschlangen abrufen, und jeder virtuelle Computer sperrt eine einzige Steuerelement-Warteschlange.

Orchestrierungsinstanzen und Entitäten sind auf alle Steuerwarteschlangeninstanzen verteilt. Die Verteilung erfolgt durch Ausführen einer Hashfunktion mit der Instanz-ID der Orchestrierung oder des Name-Schlüssel-Paars der Entität. Orchestrierungsinstanz-IDs sind standardmäßig zufällige GUIDs, sodass sichergestellt ist, dass Instanzen gleichmäßig auf alle Steuerearteschlangen verteilt werden.

Im Allgemeinen sollten Orchestratorfunktionen einfach sein und keine große Rechenleistung in Anspruch nehmen. Daher ist es nicht erforderlich, eine große Anzahl von Steuerwarteschlange-Partitionen zu erstellen, um einen erhöhten Durchsatz für Orchestrierungen zu erzielen. Der größte Teil der intensiven Vorgänge sollte in zustandslosen Aktivitätsfunktionen erfolgen, die unbegrenzt horizontal hochskaliert werden können.

### <a name="auto-scale"></a>Automatische Skalierung

Wie bei allen Azure Functions-Vorgängen, die im Nutzungsplan oder Tarifen des Typs „Elastisch Premium“ ausgeführt werden, unterstützt Durable Functions die automatische Skalierung über den [Azure Functions-Skalierungscontroller](../event-driven-scaling.md#runtime-scaling). Der Skalierungscontroller überwacht die Latenz aller Warteschlangen, indem regelmäßig _Peek_-Befehle ausgegeben werden. Basierend auf den Latenzen der eingesehenen Nachrichten legt der Skalierungscontroller fest, ob virtuelle Computer hinzugefügt oder entfernt werden.

Wenn der Skalierungscontroller bestimmt, dass die Latenzen der Nachrichten der Steuerelement-Warteschlangen zu hoch sind, werden VM-Instanzen hinzugefügt, bis die Nachrichtenlatenz auf ein annehmbares Maß verringert ist oder bis die Partitionsanzahl der Steuerelement-Warteschlangen erreicht ist. Auf ähnliche Weise fügt der Skalierungscontroller kontinuierlich VM-Instanzen hinzu, wenn Latenzen der Warteschlangen für Arbeitsaufgaben hoch sind, jedoch unabhängig von der Partitionsanzahl.

> [!NOTE]
> Ab Durable Functions 2.0 können Funktions-Apps so konfiguriert werden, dass Sie in VNET-geschützten Dienstendpunkten im Tarif „Elastisch Premium“ ausgeführt werden. In dieser Konfiguration initiieren die Durable Functions-Trigger Skalierungsanforderungen anstelle des Skalierungscontrollers. Weitere Informationen finden Sie unter Überwachung der [Laufzeitskala](../functions-networking-options.md#premium-plan-with-virtual-network-triggers).

## <a name="thread-usage"></a>Verwendung von Threads

Orchestratorfunktionen werden in einem einzelnen Thread ausgeführt, um sicherzustellen, dass die Ausführung in vielen Wiedergaben deterministisch sein kann. Aufgrund dieser Singlethreadausführung ist es wichtig, dass Orchestratorfunktionsthreads auf keinen Fall CPU-intensive Aufgaben, E/A-Vorgänge oder Blockierungsvorgänge ausführen. Alle Vorgänge, die möglicherweise E/A, die Blockierung oder mehrere Threads erfordern, sollten in die Aktivitätsfunktion verschoben werden.

Aktivitätsfunktionen haben alle dasselbe Verhalten, als reguläre, durch Warteschlangen ausgelöste Funktionen. Sie können problemlos E/A-Vorgänge und CPU-intensive Vorgänge ausführen sowie mehrere Threads verwenden. Da Aktivitätsauslöser zustandslos sind, können sie jederzeit auf eine unbegrenzte Anzahl von virtuellen Computern aufskaliert werden.

Entitätsfunktionen werden ebenfalls in einem einzelnen Thread ausgeführt, und Vorgänge werden nacheinander verarbeitet. Für Entitätsfunktionen gibt es jedoch keine Einschränkungen für den Typ des Codes, der ausgeführt werden kann.

## <a name="concurrency-throttles"></a>Drosselungen der Parallelität

In Azure Functions können mehrere Funktionen gleichzeitig in einer einzelnen App-Instanz ausgeführt werden. Diese gleichzeitige Ausführung trägt zur Erhöhung der Parallelität und Verringerung der Anzahl von „Kaltstarts“ bei, die bei einer typischen App mit der Zeit auftreten. Hohe Parallelität kann jedoch Systemressourcen pro VM (z.B. Netzwerkverbindungen oder den verfügbaren Arbeitsspeicher) erschöpfen. Je nach den Anforderungen der Funktions-App kann es erforderlich sein, die Parallelität pro Instanz zu drosseln, um zu verhindern, dass in Situationen mit hohen Auslastungen nicht genügend Speicher vorhanden ist.

Parallelitätslimits für Aktivitäts-, Orchestrator- und Entitätsfunktionen können in der Datei **host.json** konfiguriert werden. Die relevanten Einstellungen sind `durableTask/maxConcurrentActivityFunctions` für Aktivitätsfunktionen und `durableTask/maxConcurrentOrchestratorFunctions` sowohl für Orchestrator- als auch für Entitätsfunktionen. Diese Einstellungen steuern die maximale Anzahl von Orchestrator-, Entitäts- oder Aktivitätsfunktionen, die gleichzeitig in den Arbeitsspeicher geladen werden können.

### <a name="functions-20"></a>Functions 2.0

```json
{
  "extensions": {
    "durableTask": {
      "maxConcurrentActivityFunctions": 10,
      "maxConcurrentOrchestratorFunctions": 10
    }
  }
}
```

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10
  }
}
```

Im vorherigen Beispiel können maximal 10 Orchestrator- oder Entitätsfunktionen und 10 Aktivitätsfunktionen gleichzeitig auf einem einzelnen virtuellen Computer ausgeführt werden. Wenn kein Wert angegeben wird, ist die Anzahl der gleichzeitigen Ausführungen von Orchestrator- oder Aktivitätsfunktionen auf das Zehnfache der Anzahl von Kernen auf dem virtuellen Computer begrenzt.

Wenn die maximale Anzahl von Aktivitäten oder Orchestrierungen/Entitäten auf einem virtuellen Worker-Computer erreicht ist, wartet der Durable-Trigger, bis alle ausgeführten Funktionen abgeschlossen oder entladen werden, bevor neue Funktionsausführungen gestartet werden.

> [!NOTE]
> Diese Einstellungen sind nützlich bei der Verwaltung der Speicher- und CPU-Auslastung auf einem einzelnen virtuellen Computer. Bei der horizontalen Hochskalierung auf mehrere VMs sind jedoch für jede VM jeweils eigene Limits festgelegt. Mit diesen Einstellungen kann die Parallelität nicht auf globaler Ebene gesteuert werden.

> [!NOTE]
> Orchestrierungen und Entitäten werden nur in den Arbeitsspeicher geladen, wenn sie aktiv Ereignisse oder Vorgänge verarbeiten. Nachdem sie ihre Logik ausgeführt und abgewartet haben (d. h. eine `await` (C#)- oder `yield` (JavaScript, Python)-Anweisung im Funktionscode des Orchestrators getroffen haben), werden sie aus dem Speicher entladen. Orchestrierungen und Entitäten, die aus dem Arbeitsspeicher entladen werden, werden nicht auf die `maxConcurrentOrchestratorFunctions`-Drosselung angerechnet. Auch wenn Millionen von Orchestrierungen oder Entitäten den Status „Wird ausgeführt“ aufweisen, werden diese nur dann in Bezug auf die Drosselungsgrenze angerechnet, wenn sie in den aktiven Speicher geladen werden. Eine Orchestrierung, die eine Aktivitätsfunktion auf ähnliche Weise plant, zählt nicht zur Drosselung, wenn die Orchestrierung auf den Abschluss der Ausführung der Aktivität wartet.

### <a name="language-runtime-considerations"></a>Sprachlaufzeitüberlegungen

Die von Ihnen ausgewählte Sprachlaufzeit kann strenge Parallelitätseinschränkungen oder Ihre Funktionen erzwingen. Beispielsweise unterstützen Durable Function-Apps, die in Python oder PowerShell geschrieben sind, nur die gleichzeitige Ausführung einer einzelnen Funktion auf einem einzelnen virtuellen Computer. Dies kann zu erheblichen Leistungsproblemen führen, wenn sie nicht sorgfältig berücksichtigt werden. Wenn z. B. ein Orchestrator auf 10 Aktivitäten auffächert, aber die Sprachlaufzeit die Gleichzeitigkeit auf nur eine Funktion beschränkt, dann bleiben 9 der 10 Aktivitätsfunktionen hängen und warten auf eine Gelegenheit zur Ausführung. Darüber hinaus kann für diese 9 hängen gebliebenen Aktivitäten kein Lastenausgleich für andere Arbeitsauslastungen ausgeführt werden, da die Durable Functions Laufzeit sie bereits in den Arbeitsspeicher geladen hat. Dies wird besonders problematisch, wenn die Aktivitätsfunktionen lange ausgeführt werden.

Wenn die von Ihnen verwendete Sprachlaufzeit eine Einschränkung der Parallelität vorsieht, sollten Sie die Durable Functions-Parallelitätseinstellungen so aktualisieren, dass sie mit den Parallelitätseinstellungen Ihrer Sprachlaufzeit übereinstimmen. Dadurch wird sichergestellt, dass die Durable Functions-Laufzeit nicht versucht, mehr Funktionen gleichzeitig auszuführen, als von der Sprachlaufzeit zugelassen wird, sodass für alle ausstehenden Aktivitäten ein Lastenausgleich auf andere virtuelle Computer möglich ist. Wenn Sie beispielsweise über eine Python-App verfügen, die die Parallelität auf 4 Funktionen einschränkt (möglicherweise nur mit 4 Threads in einem einzelnen Spracharbeitsprozess oder 1 Thread in 4 Spracharbeitsprozess konfiguriert), sollten Sie sowohl `maxConcurrentOrchestratorFunctions` als auch `maxConcurrentActivityFunctions` auf 4 konfigurieren.

Weitere Informationen und Leistungsempfehlungen für Python finden Sie unter [Verbessern der Durchsatzleistung von Python-Apps in Azure Functions](../python-scale-performance-reference.md). Die in dieser Python-Entwicklerreferenzdokumentation erwähnten Techniken können erhebliche Auswirkungen auf die Durable Functions Leistung und Skalierbarkeit haben.

## <a name="extended-sessions"></a>Erweiterte Sitzungen

„Erweiterte Sitzungen“ ist eine Einstellung, die Orchestrierungen und Entitäten auch im Speicher beibehält, nachdem die Verarbeitung von Nachrichten abgeschlossen wurde. Die typische Auswirkung der Aktivierung von erweiterten Sitzungen sind reduzierte E/A-Vorgänge gegen den zugrunde liegenden dauerhaften Speicher und ein insgesamt verbesserter Durchsatz.

Sie können erweiterte Sitzungen aktivieren, indem `durableTask/extendedSessionsEnabled` in der Datei **host.json** auf `true` festgelegt wird. Mit der Einstellung `durableTask/extendedSessionIdleTimeoutInSeconds` kann gesteuert werden, wie lange eine Leerlaufsitzung im Arbeitsspeicher verbleibt:

**Functions 2.0**
```json
{
  "extensions": {
    "durableTask": {
      "extendedSessionsEnabled": true,
      "extendedSessionIdleTimeoutInSeconds": 30
    }
  }
}
```

**Functions 1.0**
```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

Es gibt zwei mögliche Nachteile dieser Einstellung, die Sie beachten sollten:

1. Die Arbeitsspeicherauslastung der Funktions-App steigt insgesamt, da Instanzen im Leerlauf nicht so schnell aus dem Arbeitsspeicher entladen werden.
2. Es kann zu einer Verringerung des Gesamtdurchsatzes kommen, wenn es viele gleichzeitige, unterschiedliche, kurzlebige Orchestrator- oder Entity-Funktionsausführungen gibt.

Wenn `durableTask/extendedSessionIdleTimeoutInSeconds` beispielsweise auf 30 Sekunden festgelegt ist, belegt eine Orchestrator- oder Entitätsfunktionsfolge mit kurzer Dauer, die in weniger als 1 Sekunde ausgeführt wird, dennoch 30 Sekunden lang den Arbeitsspeicher. Zudem wird sie auf das zuvor erwähnte Kontingent `durableTask/maxConcurrentOrchestratorFunctions` angerechnet, sodass möglicherweise verhindert wird, dass andere Orchestrator- oder Entitätsfunktionen ausgeführt werden.

Die spezifischen Auswirkungen von erweiterten Sitzungen auf Orchestrator- und Entitätsfunktionen werden in den nächsten Abschnitten beschrieben.

> [!NOTE]
> Erweiterte Sitzungen werden derzeit nur in .NET-Programmiersprachen wie C# oder F# unterstützt. Wenn Sie `extendedSessionsEnabled` für andere Plattformen auf `true` festlegen, kann dies zu Laufzeitproblemen führen. So kann z. B. die Ausführung von durch Aktivitäten und Orchestrierung ausgelöste Funktionen ohne Meldung Fehler verursachen.

> [!NOTE]
> Die Unterstützung für erweiterte Sitzungen kann je nach Ihrem verwendeten [Durable Functions-Speicheranbieter](durable-functions-storage-providers.md) variieren. In der Dokumentation zu Speicheranbietern erfahren Sie, ob erweiterte Sitzungen unterstützt werden.

### <a name="orchestrator-function-replay"></a>Wiedergabe von Orchestratorfunktionen

Wie zuvor erwähnt, werden Orchestratorfunktionen unter Verwendung der Inhalte der **Verlaufstabelle** wiedergegeben. Standardmäßig wird der Orchestratorfunktionscode immer dann wiedergegeben, wenn ein Batch von Nachrichten aus einer Steuerelement-Warteschlange entfernt wird. Auch wenn Sie das Muster „Auffächern nach außen/innen“ verwenden und den Abschluss aller Aufgaben erwarten (z. B. mit `Task.WhenAll()` in .NET, `context.df.Task.all()` in JavaScript, oder `context.task_all()` in Python) wird es zu Wiedergaben kommen, die auftreten, wenn Batches von Aufgaben im Laufe der Zeit verarbeitet werden. Wenn erweiterte Sitzungen aktiviert sind, verbleiben Orchestratorfunktionsinstanzen länger im Arbeitsspeicher, und neue Nachrichten können ohne eine vollständige Verlaufswiedergabe verarbeitet werden.

Die Leistungsverbesserung erweiterter Sitzungen wird in den folgenden Situationen am häufigsten beobachtet:

* Wenn eine begrenzte Anzahl von Orchestrierungsinstanzen gleichzeitig ausgeführt wird.
* Wenn Orchestrierungen eine große Anzahl sequenzieller Aktionen (z.B. Hunderte von Aktivitätsfunktionsaufrufen) aufweisen, die schnell abgeschlossen werden.
* Wenn Orchestrierungen eine große Anzahl von Aktionen nach außen und innen auffächern, die ungefähr zur gleichen Zeit abgeschlossen werden.
* Wenn Orchestratorfunktionen große Nachrichten verarbeiten oder CPU-intensive Datenverarbeitung ausführen müssen.

In allen anderen Fällen gibt es in der Regel keine beobachtbare Leistungsverbesserung für Orchestratorfunktionen.

> [!NOTE]
> Diese Einstellungen sollten nur verwendet werden, nachdem eine Orchestratorfunktion vollständig entwickelt und getestet wurde. Das standardmäßige aggressive Wiedergabeverhalten kann nützlich sein, um Verstöße gegen die [Einschränkungen des Orchestratorfunktionscodes](durable-functions-code-constraints.md) zur Entwicklungszeit zu erkennen, und ist daher standardmäßig deaktiviert.

### <a name="entity-function-unloading"></a>Entladen von Entitätsfunktionen

Entitätsfunktionen verarbeiten bis zu 20 Vorgänge in einem einzelnen Batch. Sobald eine Entität die Verarbeitung eines Batches von Vorgängen beendet, behält sie ihren Zustand bei und wird aus dem Arbeitsspeicher entladen. Mithilfe der Einstellung für erweiterte Sitzungen können Sie das Entladen von Entitäten aus dem Arbeitsspeicher verzögern. Entitäten behalten ihre Zustandsänderungen weiterhin wie zuvor bei, verbleiben jedoch für den konfigurierten Zeitraum im Arbeitsspeicher, um die Anzahl der Ladevorgänge aus Azure Storage zu verringern. Durch diese Verringerung der Ladevorgänge aus Azure Storage kann der Gesamtdurchsatz von häufig verwendeten Entitäten verbessert werden.

## <a name="performance-targets"></a>Leistungsziele

Bei der Planung der Verwendung von Durable Functions für eine Produktionsanwendung müssen die Leistungsanforderungen frühzeitig im Planungsprozess berücksichtigt werden. In diesem Abschnitt werden einige allgemeine Verwendungsszenarien und die erwarteten maximalen Durchsatzzahlen behandelt.

* **Sequenzielle Aktivitätsausführung:** Dieses Szenario beschreibt eine Orchestratorfunktion, mit der nacheinander eine Reihe von Aktivitätsfunktionen ausgeführt wird. Am ehesten ähnelt es dem Beispiel zur [Funktionsverkettung](durable-functions-sequence.md).
* **Parallele Aktivitätsausführung:** Dieses Szenario beschreibt eine Orchestratorfunktion, mit der mithilfe des Musters [Auffächern nach außen/innen](durable-functions-cloud-backup.md) viele Aktivitätsfunktionen parallel ausgeführt werden.
* **Parallele Antwortverarbeitung:** Dieses Szenario stellt die zweite Hälfte des Musters [Auffächern nach außen/innen](durable-functions-cloud-backup.md) dar. Es konzentriert sich auf die Leistung der Auffächerung nach innen. Es muss beachtet werden, dass im Unterschied zur Auffächerung nach außen die Auffächerung nach innen durch eine einzelne Orchestratorfunktionsinstanz erfolgt und daher nur auf einem einzelnen virtuellen Computer ausgeführt werden kann.
* **Externe Ereignisverarbeitung:** Dieses Szenario stellt eine einzelne Orchestratorfunktionsinstanz dar, die nacheinander auf [externe Ereignisse](durable-functions-external-events.md) wartet.
* **Verarbeitung von Entitätsvorgängen**: In diesem Szenario wird getestet, wie schnell eine _einzelne_ [Indikatorentität](durable-functions-entities.md) einen konstanten Stream von Vorgängen verarbeiten kann.

> [!TIP]
> Im Gegensatz zur Auffächerung nach außen sind Vorgänge zur Auffächerung nach innen auf einen einzelnen virtuellen Computer beschränkt. Wenn in Ihrer Anwendung das Muster „Auffächern nach außen/innen“ verwendet wird und Sie die Leistung im Hinblick auf die Auffächerung nach innen optimieren möchten, können Sie die Auffächerung der Aktivitätsfunktionen nach außen in mehrere [untergeordnete Orchestrierungen](durable-functions-sub-orchestrations.md) unterteilen.

### <a name="azure-storage-performance-targets"></a>Azure Storage-Leistungsziele

Die folgende Tabelle zeigt die erwarteten *maximalen* Durchsatzzahlen für die zuvor beschriebenen Szenarien bei Verwendung des Standardanbieters [Azure Storage für Durable Functions](durable-functions-storage-providers.md#azure-storage). „Instanz“ bezieht sich auf eine einzelne Instanz einer Orchestratorfunktion, die auf einem einzelnen kleinen ([A1](../../virtual-machines/sizes-previous-gen.md)) virtuellen Computer in Azure App Service ausgeführt wird. In allen Fällen wird angenommen, dass [erweiterte Sitzungen](#orchestrator-function-replay) aktiviert sind. Die tatsächlichen Ergebnisse können je nach den durch den Funktionscode ausgeführten CPU- oder E/A-Vorgängen variieren.

| Szenario | Maximaler Durchsatz |
|-|-|
| Sequenzielle Aktivitätsausführung | 5 Aktivitäten pro Sekunde pro Instanz |
| Parallele Aktivitätsausführung (Auffächern nach außen) | 100 Aktivitäten pro Sekunde pro Instanz |
| Parallele Antwortverarbeitung (Auffächern nach innen) | 150 Antworten pro Sekunde pro Instanz |
| Externe Ereignisverarbeitung | 50 Ereignisse pro Sekunde pro Instanz |
| Verarbeitung von Entitätsvorgängen | 64 Vorgänge pro Sekunde |

Wenn Sie nicht die erwarteten Durchsatzzahlen erreichen und die CPU- und die Speicherauslastung fehlerfrei zu sein scheinen, überprüfen Sie, ob die Ursache auf die [Integrität Ihres Speicherkontos](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance) zurückzuführen ist. Die Durable Functions-Erweiterung kann eine beträchtliche Auslastung eines Azure Storage-Kontos verursachen, und entsprechend hohe Auslastungen können zu einer Drosselung des Speicherkontos führen.

> [!TIP]
> In einigen Fällen können Sie den Durchsatz externer Ereignisse, Aktivitätsauffächerung und Entitätsvorgänge erheblich erhöhen, indem Sie den Wert der `controlQueueBufferThreshold`-Einstellung in **host.json** erhöhen. Das Erhöhen dieses Werts über den Standardwert hinaus bewirkt, dass der Durable Task Framework-Speicheranbieter mehr Arbeitsspeicher verwendet, um diese Ereignisse aggressiver vorabzurufen, wodurch Verzögerungen reduziert werden, die mit dem Löschen von Nachrichten aus der Azure Storage Steuerwarteschlangen verbunden sind. Weitere Informationen finden Sie in der Referenzdokumentation zu [host.json](durable-functions-bindings.md#host-json).

### <a name="high-throughput-processing"></a>Verarbeitung mit hohem Durchsatz

Die Architektur des Azure Storage Back-Ends unterliegt bestimmten Einschränkungen hinsichtlich der maximalen theoretischen Leistung und Skalierbarkeit von Durable Functions. Wenn Ihre Tests zeigen, dass Durable Functions auf Azure Storage Ihre Durchsatzanforderungen nicht erfüllen, sollten Sie stattdessen in Erwägung ziehen den [Netherite-Speicheranbieter für Durable Functions](durable-functions-storage-providers.md#netherite)verwenden.

Das Netherite-Speicher-Back-End wurde von [Microsoft Research](https://www.microsoft.com/research) entworfen und entwickelt. Dabei werden, zusätzlich zu [Azure-Seiten-Blobs](../../storage/blobs/storage-blob-pageblob-overview.md), [Azure Event Hubs](../../event-hubs/event-hubs-about.md) sowie die [FASTER](https://www.microsoft.com/research/project/faster/)-Datenbanktechnologie verwendet. Das Design von Netherite ermöglicht im Vergleich zu anderen Anbietern eine Verarbeitung von Orchestrierungen und Entitäten mit deutlich höherem Durchsatz. In einigen Benchmark-Szenarien wurde deutlich, dass der Durchsatz im Vergleich zum Azure Storage-Standardanbieter um mehr als eine Größenordnung zunimmt.

Weitere Informationen zu den unterstützten Speicheranbietern für Durable Functions und deren Vergleich finden Sie unter [Durable Functions-Speicheranbieter](durable-functions-storage-providers.md)-Dokumentation.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erfahren Sie mehr über Notfallwiederherstellung und geografische Verteilung](durable-functions-disaster-recovery-geo-distribution.md)
