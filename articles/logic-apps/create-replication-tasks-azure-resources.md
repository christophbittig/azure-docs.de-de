---
title: Erstellen von Replikationsaufgaben für Azure-Ressourcen
description: Erfahren Sie, wie Sie Azure-Ressourcen mithilfe von Vorlagen für Replikationsaufgaben basierend auf Workflows in Azure Logic Apps replizieren.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 42364c1c1fc0f9d2d220b4b9156a4a7ab4a410fa
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132337361"
---
# <a name="create-replication-tasks-for-azure-resources-using-azure-logic-apps-preview"></a>Erstellen von Replikationsaufgaben für Azure-Ressourcen mithilfe von Azure Logic Apps (Vorschau)

> [!IMPORTANT]
> Diese Funktion befindet sich in der Vorschauphase und unterliegt den [Zusätzlichen Nutzungsbedingungen für Microsoft Azure-Vorschauversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Maximale Verfügbarkeit und Zuverlässigkeit sind die wichtigsten betrieblichen Prioritäten für Azure-Dienste. Dennoch gibt es zahlreiche Gründe, aus denen die Kommunikation unterbrochen werden kann, z. B. Netzwerk- oder Namensauflösungsprobleme, Fehler oder vorübergehende Nichtreaktion. Solche Bedingungen sind nicht „katastrophal“ in dem Sinn, dass Sie die regionale Bereitstellung vollständig herunterfahren müssen, wie es bei einer Notfallwiederherstellung der Fall sein könnte. Das Geschäftsszenario für einige Apps kann jedoch durch Verfügbarkeitsereignisse beeinträchtigt werden, die nur einige Minuten oder sogar nur Sekunden dauern.

Um die Auswirkungen unvorhersehbarer Ereignisse auf Ihre Azure-Ressourcen in einer Azure-Region zu minimieren, können Sie die Inhalte dieser Ressourcen aus einer Region in eine andere Region replizieren und so Geschäftskontinuität gewährleisten. In Azure können Sie eine [*Replikationsaufgabe*](#replication-task) erstellen, die die Daten, Ereignisse oder Nachrichten aus einer Quelle in einer Region in ein Ziel in einer anderen Region verlagert. Auf diese Weise können Sie das Ziel sofort verfügbar machen, wenn die Quelle offline geschaltet werden und das Ziel übernehmen muss.

> [!NOTE]
> Sie können auch Replikationsaufgaben verwenden, um Inhalte zwischen Entitäten in ein und derselben Region zu verlagern. Wenn jedoch die gesamte Region nicht mehr verfügbar ist oder unterbrochen wird, sind sowohl die Quelle als auch das Ziel betroffen.

Dieser Artikel bietet eine Übersicht über Replikationsaufgaben, die von Azure Logic Apps unterstützt werden, und zeigt anhand eines Beispiels, wie Sie eine Replikationsaufgabe für Azure Service Bus-Warteschlangen erstellen. Wenn Sie noch keine Erfahrung mit Logik-Apps und Workflows haben, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](logic-apps-overview.md) und [Vergleich zwischen Umgebungen mit einem Mandanten und mehreren Mandanten bzw. Integrationsdienstumgebung für Azure Logic Apps](single-tenant-overview-compare.md).

<a name="replication-task"></a>

## <a name="what-is-a-replication-task"></a>Was ist eine Replikationsaufgabe?

Im Allgemeinen empfängt eine Replikationsaufgabe Daten, Ereignisse oder Meldungen von einer Quelle, verschiebt diese Inhalte in ein Ziel und löscht die Inhalte dann aus der Quelle. Ausnahme: Die Quelle ist eine Event Hubs-Entität. Die Replikationsaufgabe verschiebt die Inhalte in der Regel unverändert, aber von Azure Logic Apps unterstützte Replikationsaufgaben fügen auch [Replikationseigenschaften](#replication-properties) hinzu. Wenn sich die Quell- und Zielprotokolle unterscheiden, führen diese Aufgaben auch Zuordnungen zwischen Metadatenstrukturen durch. Replikationsaufgaben sind in der Regel zustandslos, was bedeutet, dass sie keine Zustände oder andere Nebeneffekte über parallele oder sequenzielle Ausführungen einer Aufgabe hinweg weitergeben.

Wenn Sie die verfügbaren Replikationsaufgabenvorlagen verwenden, verfügt jede Replikationsaufgabe, die Sie erstellen, über einen zugrunde liegenden [zustandslosen Workflow](single-tenant-overview-compare.md#stateful-stateless) in einer Ressource von **Logic Apps (Standard)** , der mehrere Workflows für Replikationsaufgaben enthalten kann. Diese Ressource wird in einer Azure Logic Apps-Instanz für Einzelmandanten gehostet. Dies ist eine skalierbare und zuverlässige Ausführungsumgebung zum Konfigurieren und Ausführen serverloser Anwendungen, einschließlich Replikations- und Verbundaufgaben. Die Azure Logic Apps-Runtime für Einzelmandanten verwendet auch das [Azure Functions-Erweiterbarkeitsmodell](../azure-functions/functions-bindings-register.md) und wird als Erweiterung der Azure Functions-Runtime gehostet. Dieser Entwurf bietet Portabilität, Flexibilität und eine höhere Leistung für Logik-App-Workflows sowie weitere Funktionen und Vorteile der Azure Functions-Plattform und des Azure App Service-Ökosystems.

Weitere Informationen zu Replikation und Verbund finden Sie in den folgenden Dokumenten:

- [Verbund: mehrere Standorte und mehrere Regionen](../event-hubs/event-hubs-federation-overview.md)
- [Muster für Ereignisreplikationsaufgaben](../event-hubs/event-hubs-federation-patterns.md)
- [Nachrichtenreplikation und regionsübergreifender Verbund](../service-bus-messaging/service-bus-federation-overview.md)
- [Muster für Nachrichtenreplikationsaufgaben](../service-bus-messaging/service-bus-federation-patterns.md)

<a name="replication-task-templates"></a>

## <a name="replication-task-templates"></a>Vorlagen für Replikationsaufgaben

Derzeit sind Replikationsaufgabenvorlagen für [Azure Event Hubs](../event-hubs/event-hubs-about.md) und [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) verfügbar. In der folgenden Tabelle sind die Replikationsaufgabenvorlagen aufgeführt, die zurzeit in dieser Vorschau verfügbar sind:

| Ressourcentyp | Replikationsquelle und -ziel |
|---------------|-------------------------------|
| Azure Event Hubs-Namespace | – Event Hubs-Instanz zu Event Hubs-Instanz <br>– Event Hubs-Instanz zu Service Bus-Warteschlange <br>– Event Hubs-Instanz zu Service Bus-Thema |
| Azure Service Bus-Namespace | – Service Bus-Warteschlange zu Service Bus-Warteschlange <br>– Service Bus-Warteschlange zu Service Bus-Thema <br>– Service Bus-Thema zu Service Bus Thema <br>– Service Bus-Warteschlange zu Event-Hubs-Instanz <br>– Service Bus-Thema zu Service Bus-Warteschlange <br>– Service Bus-Thema zu Event-Hubs-Instanz |
|||

### <a name="replication-topology-and-workflow"></a>Replikationstopologie und -workflow

Die folgenden Diagramme zeigen die Struktur einer Replikationsaufgabe sowie den Workflow für Event Hubs-Instanzen und Service Bus-Warteschlangen, sodass Sie sich die Funktionsweise einer von Azure Logic Apps (Standard) unterstützten Replikationsaufgabe besser vorstellen können.

#### <a name="replication-topology-for-event-hubs"></a>Replikationstopologie für Event Hubs

Das folgende Diagramm zeigt die Topologie und den Workflow einer Replikationsaufgabe zwischen Event Hubs-Instanzen:

![Konzeptionelles Diagramm, das die Topologie für eine Replikationsaufgabe zeigt, die von einem Workflow von Logic Apps (Standard) zwischen Event Hubs-Instanzen unterstützt wird.](media/create-replication-tasks-azure-resources/replication-topology-event-hubs.png)

Informationen zur Replikation und zum Verbund in Azure Event Hubs finden Sie in den folgenden Dokumenten:

- [Verbund: mehrere Standorte und mehrere Regionen](../event-hubs/event-hubs-federation-overview.md)
- [Muster für Ereignisreplikationsaufgaben](../event-hubs/event-hubs-federation-patterns.md)

#### <a name="replication-topology-for-service-bus"></a>Replikationstopologie für Service Bus

Das folgende Diagramm zeigt die Topologie und den Workflow einer Replikationsaufgabe zwischen Service Bus-Warteschlangen:

![Konzeptionelles Diagramm, das die Topologie für eine Replikationsaufgabe zeigt, die von einem Workflow von Logic Apps (Standard) zwischen Service Bus-Warteschlangen unterstützt wird.](media/create-replication-tasks-azure-resources/replication-topology-service-bus-queues.png)

Informationen zur Replikation und zum Verbund in Azure Service Bus finden Sie in den folgenden Dokumenten:

- [Nachrichtenreplikation und regionsübergreifender Verbund](../service-bus-messaging/service-bus-federation-overview.md)
- [Muster für Nachrichtenreplikationsaufgaben](../service-bus-messaging/service-bus-federation-patterns.md)

<a name="replication-properties"></a>

## <a name="metadata-and-property-mappings"></a>Metadaten- und Eigenschaftenzuordnungen

Bei Event-Hubs werden die folgenden Elemente aus dem Quell-Event-Hubs-Namensraum durch neue, dem Service zugewiesene Werte im Ziel-Event-Hubs-Namensraum ersetzt: dem Service zugewiesene Metadaten eines Ereignisses, ursprüngliche Uhrzeit der Einreihung in die Warteschlange, Sequenznummer und Offset. Für [Hilfsfunktionen](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) und die Replikationsaufgaben in den von Azure bereitgestellten Beispielen werden die ursprünglichen Werte jedoch in den Benutzereigenschaften beibehalten: `repl-enqueue-time` (ISO8601-Zeichenfolge), `repl-sequence` und `repl-offset`. Diese Eigenschaften weisen den Typ `string` auf und enthalten den in eine Zeichenfolge umgewandelten Wert der jeweiligen ursprünglichen Eigenschaften. Wenn das Ereignis mehrfach weitergeleitet wird, werden die vom Dienst zugewiesenen Metadaten der unmittelbaren Quelle an die bereits vorhandenen Eigenschaften angefügt, wobei die Werte durch Semikolon getrennt sind. Weitere Informationen finden Sie unter [Muster für Ereignisreplikationsaufgaben: Vom Dienst zugewiesene Metadaten](../event-hubs/event-hubs-federation-patterns.md#service-assigned-metadata).

Für Service Bus werden die folgenden Elemente, die aus der Service Bus-Quellwarteschlange oder dem Service Bus-Quellthema abgerufen wurden, durch neue vom Dienst zugewiesene Werte in der Service Bus-Zielwarteschlange oder im Service Bus-Zielthema ersetzt: vom Dienst zugewiesene Metadaten einer Nachricht, ursprüngliche Uhrzeit der Einreihung in die Warteschlange und Sequenznummer. Für die Standardreplikationsaufgaben in den von Azure bereitgestellten Beispielen werden die ursprünglichen Werte jedoch in den Benutzereigenschaften beibehalten: `repl-enqueue-time` (ISO8601-Zeichenfolge) und `repl-sequence`. Diese Eigenschaften weisen den Typ `string` auf und enthalten den in eine Zeichenfolge umgewandelten Wert der jeweiligen ursprünglichen Eigenschaften. Wenn die Meldung mehrfach weitergeleitet wird, werden die vom Dienst zugewiesenen Metadaten der unmittelbaren Quelle an die bereits vorhandenen Eigenschaften angefügt, wobei die Werte per Semikolon getrennt sind. Weitere Informationen finden Sie unter [Muster für Nachrichtenreplikationsaufgaben: Vom Dienst zugewiesene Metadaten](../service-bus-messaging/service-bus-federation-patterns.md#service-assigned-metadata).

Wenn eine Aufgabe von Service Bus zu Event Hubs repliziert wird, ordnet die Aufgabe nur die Eigenschaft `User Properties` der Eigenschaft `Properties` zu. Wenn die Aufgabe jedoch von Event Hubs zu Service Bus repliziert wird, ordnet sie die folgenden Eigenschaften zu:

| Aus Event Hubs | Zu Service Bus |
|-----------------|----------------|
| ContentType | ContentType |
| CorrelationId | CorrelationId |
| MessageId | MessageId |
| PartitionKey | PartitionKey SessionId |
| Eigenschaften | Benutzereigenschaften |
| ReplyTo | ReplyTo |
| ReplyToGroupName | ReplyToSessionId |
| Subject | Bezeichnung |
| Beschreibung | Beschreibung |
|||

<a name="order-preservation"></a>

## <a name="order-preservation"></a>Beibehaltung der Reihenfolge

Bei Event Hubs erstellt die Replikation 1:1-Klone zwischen der gleichen Anzahl von [Partitionen](../event-hubs/event-hubs-features.md#partitions) ohne Änderungen an den Ereignissen. Duplikate können enthalten sein. Wenn die Anzahl der Partitionen nicht gleich ist, wird bei der Replikation jedoch nur die relative Reihenfolge der Ereignisse basierend auf dem Partitionsschlüssel beibehalten. Auch hier können Duplikate enthalten sein. Weitere Informationen finden Sie unter [Datenströme und Beibehaltung der Reihenfolge](../event-hubs/event-hubs-federation-patterns.md#streams-and-order-preservation).

Für Service Bus müssen Sie Sitzungen aktivieren, damit aus der Quelle abgerufene Nachrichtensequenzen mit der gleichen Sitzungs-ID als Batch in der ursprünglichen Sequenz und mit derselben Sitzungs-ID an die Zielwarteschlange oder das Zielthema übermittelt werden. Weitere Informationen finden Sie unter [Sequenzen und Beibehaltung der Reihenfolge](../service-bus-messaging/service-bus-federation-patterns.md#sequences-and-order-preservation).

Weitere Informationen zu einem Verbund mit mehreren Standorten und Regionen für Azure-Dienste, in denen Sie Replikationsaufgaben erstellen können, finden Sie in den folgenden Dokumenten:

- [Verbund: mehrere Standorte und mehrere Regionen](../event-hubs/event-hubs-federation-overview.md)
- [Muster für Ereignisreplikationsaufgaben](../event-hubs/event-hubs-federation-patterns.md)
- [Nachrichtenreplikation und regionsübergreifender Verbund](../service-bus-messaging/service-bus-federation-overview.md)
- [Muster für Nachrichtenreplikationsaufgaben](../service-bus-messaging/service-bus-federation-patterns.md)

<a name="pricing"></a>

## <a name="pricing"></a>Preise

Im Hintergrund wird eine Replikationsaufgabe von einem zustandslosen Workflow in einer Ressource von **Logic Apps (Standard)** unterstützt, die in einer Azure Logic Apps-Instanz für Einzelmandanten gehostet wird. Wenn Sie diese Replikationsaufgabe erstellen, fallen sofort Gebühren an. Nutzung, Messung, Abrechnung und Preismodell folgen dem Hostingplan für [Standard (ein Mandant)](logic-apps-pricing.md#standard-pricing) gemäß den [Tarifen im Standardmodell](logic-apps-pricing.md#standard-pricing-tiers).

<a name="scale-up"></a>

Je nach Anzahl der Ereignisse, die Event Hubs empfängt, oder der Nachrichten, die Service Bus verarbeitet, kann Ihr Hosting-Plan nach oben oder unten skaliert werden, um eine minimale vCPU-Auslastung und eine niedrige Latenz während der aktiven Replikation zu gewährleisten. Dieses Verhalten erfordert, dass Sie bei der Erstellung einer Logic-App-Ressource, die Sie für Ihre Replikationsaufgabe verwenden möchten, [die entsprechende Preisstufe des Standard-Plans wählen](#scale-out), damit Azure Logic Apps die CPU-Nutzung nicht drosselt oder ausreizt und dennoch eine schnelle Replikationsgeschwindigkeit gewährleisten kann.

> [!NOTE]
> Wenn Ihre Anwendung mit einer Instanz des WS1-Plans beginnt und dann auf zwei Instanzen skaliert, sind die Kosten doppelt so hoch wie die des WS1-Plans, unter der Annahme, dass die Pläne den ganzen Tag laufen. Wenn Sie Ihre App auf den WS2-Plan hochskalieren und eine Instanz verwenden, sind die Kosten effektiv identisch mit denen von zwei WS1-Planinstanzen. Wenn Sie Ihre Anwendung auf den WS3-Plan skalieren und eine Instanz verwenden, sind die Kosten effektiv die gleichen wie bei zwei WS2-Plan-Instanzen oder vier WS1-Plan-Instanzen.

<a name="scale-out"></a>

Die folgenden Beispiele veranschaulichen den Tarif und die Konfigurationsoptionen des Hostingplans, die den besten Durchsatz und die besten Kosten für bestimmte Replikationsaufgabeszenarien bereitstellen, je nachdem, ob das Szenario Event Hubs oder Service Bus und verschiedene Konfigurationswerte ist.

> [!NOTE]
> Die Beispiele in den folgenden Abschnitten verwenden 800 als Standardwert für die Prefetch-Anzahl, die maximale Ereignisstapelgröße für Event-Hubs und die maximale Nachrichtenanzahl für den Service-Bus, wobei angenommen wird, dass die Ereignis- oder Nachrichtengröße 1 KB beträgt. Basierend auf Ihren Ereignisgrößen sollten Sie die Anzahl der Vorabrufe, die maximale Ereignisbatchgröße oder die maximale Nachrichtenanzahl anpassen. Wenn Ihre Ereignis- oder Nachrichtengröße beispielsweise über 1 KB liegt, sollten Sie die Werte für die Vorabrufanzahl und die maximale Ereignisbatchgröße oder Nachrichtenanzahl von 800 verringern.

### <a name="event-hubs-scale-out"></a>Event Hubs aufskalieren

Die folgenden Beispiele veranschaulichen die Preisstufe des Hosting-Plans und die Konfigurationsoptionen für eine Replikationsaufgabe zwischen zwei Event Hubs-Namespaces *in derselben Region*, basierend auf der Anzahl der [ Partitionen](../event-hubs/event-hubs-features.md#partitions), der Anzahl der Ereignisse pro Sekunde und anderen Konfigurationswerten.

In den Beispielen in diesem Abschnitt wird 800 als Standardwert für die Anzahl der Vorabrufe und die maximale Ereignisbatchgröße verwendet, vorausgesetzt, die Ereignisgröße beträgt 1 KB. Basierend auf Ihren Ereignisgrößen sollten Sie die Anzahl der Vorabrufe, die maximale Ereignisbatchgröße oder die maximale Nachrichtenanzahl anpassen. Wenn Ihre Ereignis- oder Nachrichtengröße beispielsweise über 1 KB liegt, sollten Sie die Werte für die Vorabrufanzahl und die maximale Ereignisbatchgröße oder Nachrichtenanzahl von 800 verringern.

| Tarif | Partitionsanzahl | Ereignisse pro Sekunde | Maximale Ausbrüche* | Jederzeit bereite Instanzen* | Anzahl der Vorabrufe* | Maximale Ereignisbatchgröße* |
|--------------|-----------------|-------------------|----------------|-------------------------|-----------------|-----------------|
| **WS1** | 1 | 1000 | 1 | 1 | 800 | 800 |
| **WS1** | 2 | 2000 | 1 | 1 | 800 | 800 |
| **WS2** | 4 | 4000 | 2 | 1 | 800 | 800 |
| **WS2** | 8 | 8.000 | 2 | 1 | 800 | 800 |
| **WS3** | 16 | 16000 | 2 | 1 | 800 | 800 |
| **WS3** | 32 | 32000 | 3 | 1 | 800 | 800 |
||||||||

\*Weitere Informationen zu den Werten, die Sie für jeden Tarif ändern können, finden Sie in der folgenden Tabelle:

| Wert | Beschreibung |
|-------|-------------|
| **Maximale Ausbrüche** | Die *maximale* Anzahl der elastischen Benutzer, die unter Last verkleinert werden. Wenn Ihre zugrunde liegende Anwendung Instanzen benötigt, die über die *immer bereiten Instanzen* in der nächsten Tabellenzeile hinausgehen, kann Ihre Anwendung weiter skalieren, bis die Anzahl der Instanzen die maximale Grenze an Ausbrüchen erreicht. Um diesen Wert zu ändern, lesen Sie bitte [Bearbeiten der Einstellungen für die Skalierung des Hostingplans](#edit-plan-scale-out-settings) weiter unten in diesem Artikel. <p>**Anmerkung**: Alle Instanzen, die über Ihren Plan hinausgehen, werden *nur* dann abgerechnet, wenn sie laufen und Ihnen auf einer sekundengenauen Basis zugewiesen werden. Die Plattform ist am besten dafür, Ihre App auf den definierten Höchstwert zu skalieren. <p>**Tipp**:Wählen Sie als Empfehlung einen Maximalwert aus, der höher ist, als Sie möglicherweise benötigen, damit die Plattform bei Bedarf aufskaliert werden kann, um eine größere Last zu bewältigen, da nicht verwendete Instanzen nicht in Rechnung gestellt werden. <p>Weitere Informationen finden Sie in der folgenden Dokumentation, da der Workflow-Standardplan einige Aspekte mit dem Azure Functions Premium gemeinsam hat: <p>- [Plan- und SKU-Einstellungen – Azure Functions Premium Plan](../azure-functions/functions-premium-plan.md#plan-and-sku-settings) <br>- [Was ist Cloudburstung](https://azure.microsoft.com/overview/what-is-cloud-bursting/)? |
| **Jederzeit bereite Instanzen** | Die Mindestanzahl von Instanzen, die stets bereit für das Hosten Ihrer App sind. Die Mindestanzahl ist immer 1. Um diesen Wert zu ändern, lesen Sie bitte [Bearbeiten der Einstellungen für die Skalierung des Hostingplans](#edit-plan-scale-out-settings) weiter unten in diesem Artikel. <p>**Anmerkung**: Alle Instanzen, die Ihre Plangröße überschreiten, werden in Rechnung gestellt, unabhängig davon, ob sie zum Zeitpunkt der Zuweisung an Sie *laufen oder nicht*. <p>Weitere Informationen finden Sie in der folgenden Dokumentation, da der Workflow Standard-Plan einige Aspekte mit dem Azure Functions Premium-Plan gemeinsam hat: [Immer bereite Instanzen - Azure Functions Premium Plan](../azure-functions/functions-premium-plan.md#always-ready-instances). |
| **Anzahl der Vorabrufe** | Der Standardwert für die `AzureFunctionsJobHost__extensions__eventHubs__eventProcessorOptions__prefetchCount` App-Einstellung in Ihrer Logik-App-Ressource, welche die von der zugrunde liegenden Klasse verwendete Vorabruf-Anzahl bestimmt`EventProcessorHost`. Informationen zum Hinzufügen oder Angeben eines anderen Werts für diese App-Einstellung finden Sie unter Verwalten von [App-Einstellungen – local.settings.json.](edit-app-settings-host-settings.md?tabs=azure-portal#manage-app-settings), zum Beispiel: <p>- **Name**: `AzureFunctionsJobHost__extensions__eventHubs__eventProcessorOptions__prefetchCount` <br>- **Wert**: `800` (keine Obergrenze) <p>Weitere Informationen über die Eigenschaft `prefetchCount` finden Sie in der folgenden Dokumentation: <p>- [host.json Einstellungen - Azure Event Hubs Trigger und Bindungen für Azure Funktionen](../azure-functions/functions-bindings-event-hubs.md#hostjson-settings) <br>- [EventProcessorOptions.PrefetchCount Eigenschaft](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount) <br>- [Ausgleichen der Partitionsauslastung über mehrere Instanzen der Anwendung hinweg](../event-hubs/event-processor-balance-partition-load.md). <br>- [Ereignisprozessorhost](../event-hubs/event-hubs-event-processor-host.md) <br>- [EventProcessorHost Klasse](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) |
| **Maximale Ereignisbatchgröße** | Der Standardwert für die `AzureFunctionsJobHost__extensions__eventHubs__eventProcessorOptions__maxBatchSize` App-Einstellung in Ihrer Logik-App-Ressource, der die maximale Ereignisanzahl bestimmt, die von jeder Empfangsschleife empfangen wird. Informationen zum Hinzufügen oder Angeben eines anderen Werts für diese App-Einstellung finden Sie unter Verwalten von [App-Einstellungen – local.settings.json.](edit-app-settings-host-settings.md?tabs=azure-portal#manage-app-settings), zum Beispiel: <p>- **Name**: `AzureFunctionsJobHost__extensions__eventHubs__eventProcessorOptions__maxBatchSize` <br>- **Wert**: `800` (keine Obergrenze) <p>Weitere Informationen über die Eigenschaft `maxBatchSize` finden Sie in der folgenden Dokumentation: <p>- [host.json Einstellungen - Azure Event Hubs Trigger und Bindungen für Azure Funktionen](../azure-functions/functions-bindings-event-hubs.md#hostjson-settings) <br>- [EventProcessorOptions.MaxBatchSize Eigenschaft](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize) <br>- [Ereignisprozessorhost](../event-hubs/event-hubs-event-processor-host.md) |
|||

### <a name="service-bus-scale-out"></a>Service Bus-Skalierung

Die folgenden Beispiele veranschaulichen die Preisstufe des Hosting-Plans und die Konfigurationsoptionen für eine Replikationsaufgabe zwischen zwei Service-Bus-Namespaces  *in derselben Region*, basierend auf der Anzahl der Nachrichten pro Sekunde und anderen Konfigurationswerten.

In den Beispielen in diesem Abschnitt wird 800 als Standardwert für die Anzahl der Vorabrufe und die maximale Ereignisbatchgröße verwendet, vorausgesetzt, die Ereignisgröße beträgt 1 KB. Basierend auf Ihren Ereignisgrößen sollten Sie die Anzahl der Vorabrufe, die maximale Ereignisbatchgröße oder die maximale Nachrichtenanzahl anpassen. Wenn Ihre Ereignis- oder Nachrichtengröße beispielsweise über 1 KB liegt, sollten Sie die Werte für die Vorabrufanzahl und die maximale Ereignisbatchgröße oder Nachrichtenanzahl von 800 verringern.

| Tarif | Nachrichten pro Sekunde | Maximale Ausbrüche* | Jederzeit bereite Instanzen* | Anzahl der Vorabrufe* | Maximale Nachrichtenanzahl* |
|--------------|---------------------|-----------------|-------------------------|-----------------|------------------------|
| **WS1** | 2000 | 1 | 1 | 800 | 800 |
| **WS2** | 2500 | 1 | 1 | 800 | 800 |
| **WS3** | 3500 | 1 | 1 | 800 | 800 |
|||||||

\*Weitere Informationen zu den Werten, die Sie für jeden Tarif ändern können, finden Sie in der folgenden Tabelle:

| Wert | Beschreibung |
|-------|-------------|
| **Maximale Ausbrüche** | Die *maximale* Anzahl der elastischen Benutzer, die unter Last verkleinert werden. Wenn Ihre zugrunde liegende Anwendung Instanzen benötigt, die über die *immer bereiten Instanzen* in der nächsten Tabellenzeile hinausgehen, kann Ihre Anwendung weiter skalieren, bis die Anzahl der Instanzen die maximale Grenze an Ausbrüchen erreicht. Um diesen Wert zu ändern, lesen Sie bitte [Bearbeiten der Einstellungen für die Skalierung des Hostingplans](#edit-plan-scale-out-settings) weiter unten in diesem Artikel. <p>**Anmerkung**: Alle Instanzen, die über Ihren Plan hinausgehen, werden *nur* dann abgerechnet, wenn sie laufen und Ihnen auf einer sekundengenauen Basis zugewiesen werden. Die Plattform ist am besten dafür, Ihre App auf den definierten Höchstwert zu skalieren. <p>**Tipp**:Wählen Sie als Empfehlung einen Maximalwert aus, der höher ist, als Sie möglicherweise benötigen, damit die Plattform bei Bedarf aufskaliert werden kann, um eine größere Last zu bewältigen, da nicht verwendete Instanzen nicht in Rechnung gestellt werden. <p>Weitere Informationen finden Sie in der folgenden Dokumentation, da der Workflow-Standardplan einige Aspekte mit dem Azure Functions Premium gemeinsam hat: <p>- [Plan- und SKU-Einstellungen – Azure Functions Premium Plan](../azure-functions/functions-premium-plan.md#plan-and-sku-settings) <br>- [Was ist Cloudburstung](https://azure.microsoft.com/overview/what-is-cloud-bursting/)? |
| **Jederzeit bereite Instanzen** | Die Mindestanzahl von Instanzen, die stets bereit für das Hosten Ihrer App sind. Die Mindestanzahl ist immer 1. Um diesen Wert zu ändern, lesen Sie bitte [Bearbeiten der Einstellungen für die Skalierung des Hostingplans](#edit-plan-scale-out-settings) weiter unten in diesem Artikel. <p>**Anmerkung**: Alle Instanzen, die Ihre Plangröße überschreiten, werden in Rechnung gestellt, unabhängig davon, ob sie zum Zeitpunkt der Zuweisung an Sie *laufen oder nicht*. <p>Weitere Informationen finden Sie in der folgenden Dokumentation, da der Workflow Standard-Plan einige Aspekte mit dem Azure Functions Premium-Plan gemeinsam hat: [Immer bereite Instanzen - Azure Functions Premium Plan](../azure-functions/functions-premium-plan.md#always-ready-instances). |
| **Anzahl der Vorabrufe** | Der Standardwert für die `AzureFunctionsJobHost__extensions__serviceBus__prefetchCount` App-Einstellung in Ihrer Logik-App-Ressource, welche die von der zugrunde liegenden `ServiceBusProcessor` Klasse verwendete Vorabrufanzahl bestimmt. Informationen zum Hinzufügen oder Angeben eines anderen Werts für diese App-Einstellung finden Sie unter Verwalten von [App-Einstellungen – local.settings.json.](edit-app-settings-host-settings.md?tabs=azure-portal#manage-app-settings), zum Beispiel: <p>- **Name**: `AzureFunctionsJobHost__extensions__eventHubs__eventProcessorOptions__prefetchCount` <br>- **Wert**: `800` (keine Obergrenze) <p>Weitere Informationen über die Eigenschaft `prefetchCount` finden Sie in der folgenden Dokumentation: <p>- [host.json Einstellungen - Azure Service Bus Bindungen für Azure Funktionen](../azure-functions/functions-bindings-service-bus.md#hostjson-settings) <br>- [ServiceBusProcessor.PrefetchCount Eigenschaft](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.prefetchcount) <br>- [ServiceBusProcessor-Klasse](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) |
| **Maximale Anzahl von Meldungen** | Der Standardwert für die `AzureFunctionsJobHost__extensions__serviceBus__batchOptions__maxMessageCount`App-Einstellung in Ihrer Logik-App-Ressource, welche die maximale Anzahl der zu sendenden Nachrichten bei Auslösung bestimmt. Um einen anderen Wert für diese App-Einstellung hinzuzufügen oder festzulegen, überprüfen Sie [App-Einstellungen Verwalten - local.settings.json](edit-app-settings-host-settings.md?tabs=azure-portal#manage-app-settings), zum Beispiel: <p>- **Name**: `AzureFunctionsJobHost__extensions__serviceBus__batchOptions__maxMessageCount` <br>- **Wert**: `800` (keine Obergrenze) <p>Weitere Informationen über die `maxMessageCount`Eigenschaft finden Sie in der folgenden Dokumentation: [host.json Einstellungen - Azure Event Hubs Bindungen für Azure Funktionen](../azure-functions/functions-bindings-service-bus.md#hostjson-settings).|
|||

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto und ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Die Quell- und Zielressourcen oder die Quell- und Zielentitäten, die in verschiedenen Azure-Regionen vorhanden sein sollten, damit Sie das Failoverszenario für die georedundante Notfallwiederherstellung testen können. Diese Entitäten können basierend auf der Aufgabenvorlage variieren, die Sie verwenden möchten. Das Beispiel in diesem Artikel verwendet zwei Service Bus-Warteschlangen, die sich in unterschiedlichen Namespaces und Azure-Regionen befinden.

- Eine Ressource von **Logic Apps (Standard)** , die Sie beim Erstellen des Replikationsaufgabe wiederverwenden können. Auf diese Weise können Sie diese Ressource speziell an Ihre Replikationsaufgabe anpassen, indem Sie zum Beispiel [den Hosting-Plan und Tarif](#pricing) auf der Grundlage der Anforderungen Ihres Replikationsszenarios, wie Kapazität, Durchsatz und Skalierung, auswählen. Sie können diese Ressource beim Erstellen des Replikationsaufgabe erstellen, aber Sie können weder Region noch Hostingplan oder Tarif ändern. Die folgende Liste enthält weitere Gründe und Best Practices für eine zuvor erstellte Logik-App-Ressource:

  - Sie können diese Logik-App-Ressource in einer Region erstellen, die sich von den Quell- und Zielentitäten in Ihrer Replikationsaufgabe unterscheidet.

    Dies gilt derzeit aufgrund der nativen Integration der Replikationsaufgabe in Azure-Ressourcen. Wenn Sie eine Replikationsaufgabe zwischen Entitäten erstellen und dabei eine neue Logik-App-Ressource erstellen, anstatt eine vorhandene zu verwenden, wird die *neue Logik-App in derselben Region wie die Quellentität erstellt*. Wenn die Quellregion nicht mehr verfügbar ist, kann der Replikationsvorgang auch nicht mehr funktionieren. In einem Failoverszenario kann die Aufgabe auch nicht mit dem Lesen von Daten aus der neuen Quelle (bzw. der Zielentität) beginnen, was das [Aktiv/Passiv-Replikationsmuster](../service-bus-messaging/service-bus-federation-overview.md#active-passive-replication) zu erreichen versucht.

  - Sie können diese Logik-App-Ressource vorab anpassen, indem Sie den Hostingplan und Tarif auswählen, anstatt die Standardattribute zu verwenden. Auf diese Weise kann Ihre Replikationsaufgabe mehr Ereignisse oder Nachrichten pro Sekunde verarbeiten und so die Replikation beschleunigen. Wenn Sie diese Ressource beim Erstellen der Replikationsaufgabe erstellen, werden diese Standardattribute korrigiert.

  - Sie können sicherstellen, dass diese Logik-App-Ressource *nur Workflows der Replikationsaufgabe* enthält, insbesondere dann, wenn Sie das Aktiv/Passiv-Replikationsmuster verwenden möchten. Wenn Sie die Replikationsaufgabe mithilfe einer vorhandenen Logik-App erstellen, fügt diese Option die Aufgabe (zustandsloser Workflow) zu dieser Logik-App-Ressource hinzu.

  Weitere Informationen finden Sie unter [Erstellen eines Integrationsworkflows mit der Azure Logic Apps-Einzelmandanteninstanz (Standard) im Azure-Portal](create-single-tenant-workflows-azure-portal.md).

- Optional: Verbindungszeichenfolge für den Zielnamespace. Mit dieser Option können Sie festlegen, dass das Ziel in einem anderen Abonnement vorhanden ist, sodass Sie die abonnementübergreifende Replikation einrichten können.

   Führen Sie die folgenden Schritte aus, um die Verbindungszeichenfolge für die Zielentität zu suchen:

   1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zum Zielnamespace.

   1. Wählen Sie im Navigationsmenü des Namespace unter **Einstellungen** die Option **SAS-Richtlinien** aus.

   1. Wählen Sie im geöffneten Bereich **SAS-Richtlinien** unter **Richtlinie** den Eintrag **RootManageSharedAccessKey** aus.

   1. Kopieren Sie im geöffneten Bereich **SAS-Richtlinie: RootManageSharedAccessKey** den Wert der **primären Verbindungszeichenfolge**.

   1. Speichern Sie die Verbindungszeichenfolge, damit Sie sie später zum Herstellen einer Verbindung mit dem Zielnamespace verwenden können.

<a name="naming"></a>

## <a name="naming-conventions"></a>Benennungskonventionen

Planen Sie die Benennungsstrategie sorgfältig, die Sie für Ihre Replikationsaufgaben und Entitäten verwenden, wenn Sie diese noch nicht erstellt haben. Stellen Sie sicher, dass die Namen leicht identifizierbar und unterscheidbar sind. Wenn Sie beispielsweise mit dem Event-Hubs-Namensraum arbeiten, repliziert die Replikationsaufgabe von jeder Event-Hubs-Instanz im Quellnamensraum. Wenn Sie mit Service Bus-Warteschlangen, finden Sie in der folgenden Tabelle ein Beispiel für die Benennung der Entitäten und der Replikationsaufgabe:

| Quellname | Beispiel | Replikations-App | Beispiel | Zielname | Beispiel |
|-------------|---------|-----------------|---------|-------------|---------|
| Namespace: `<name>-sb-<region>` | `fabrikam-sb-weu` | Logik-App: `<name-source-region-target-region>` | `fabrikam-rep-weu-wus` | Namespace: `<name>-sb-<region>` | `fabrikam-sb-wus` |
| Queue: `<name>` | `jobs-transfer` | Workflow: `<name>` | `jobs-transfer-workflow` | Queue: `<name>` | `jobs` |
|||||||

<a name="create-replication-task"></a>

## <a name="create-a-replication-task"></a>Erstellen eines Replikationsauftrags

Dieses Beispiel zeigt, wie Sie eine Replikationsaufgabe für Service Bus-Warteschlangen erstellen.

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) den Service Bus-Namespace, den Sie als Quelle verwenden möchten.

1. Wählen Sie im Navigationsmenü des Namespace im Abschnitt **Automatisierung** den Eintrag **Aufgaben (Vorschau)** aus.

   ![Screenshot: Azure-Portal und Menü des Azure Service Bus-Namespace mit ausgewählter Option „Aufgaben (Vorschau)“.](./media/create-replication-tasks-azure-resources/service-bus-automation-menu.png)

1. Wählen Sie im Bereich **Aufgaben** den Befehl **Aufgabe hinzufügen** aus, damit Sie eine Aufgabenvorlage auswählen können.

   ![Screenshot: Bereich „Aufgaben (Vorschau)“ mit ausgewählter Option „Aufgabe hinzufügen“.](./media/create-replication-tasks-azure-resources/add-replication-task.png)

1. Wählen Sie im Bereich **Aufgabe hinzufügen** unter **Vorlage auswählen** die Vorlage für den Replikationstask aus, die Sie erstellen möchten, und wählen Sie **Auswählen** aus. Wenn die nächste Seite nicht angezeigt wird, wählen Sie **Weiter: Authentifizierung** aus.

   In diesem Beispiel wird die Aufgabenvorlage **Replizieren von Service-Bus-Warteschlange zu Warteschlange** ausgewählt, die Inhalte zwischen Service Bus-Warteschlangen repliziert.

   ![Screenshot des Fensters "Aufgabe hinzufügen" mit der Vorlage "Replizieren von Service-Bus-Warteschlange zu Warteschlange".](./media/create-replication-tasks-azure-resources/select-replicate-service-bus-template.png)

1. Wählen Sie auf der Registerkarte **Authentifizierung** im Abschnitt **Verbindungen** für jede Verbindung, die in der Aufgabe angezeigt wird, die Option **Erstellen** aus, damit Sie für alle Verbindungen Anmeldeinformationen zur Authentifizierung angeben können. Die Verbindungstypen in den einzelnen Aufgaben variieren je nach Aufgabe.

   Dieses Beispiel zeigt die Aufforderung zum Erstellen der Verbindung mit dem Service Bus-Zielnamespace, in dem die Zielwarteschlange vorhanden ist. Die Verbindung ist für den Service Bus-Quellnamespace bereits vorhanden.

   ![Screenshot: ausgewählte Option „Erstellen“ für die Verbindung mit dem Service Bus-Zielnamespace.](./media/create-replication-tasks-azure-resources/create-authenticate-connections.png)

1. Geben Sie die erforderlichen Informationen zum Ziel an, und wählen Sie dann **Erstellen** aus.

   Geben Sie in diesem Beispiel einen Anzeigenamen für die Verbindung an, und wählen Sie dann den Service Bus-Namespace aus, in dem die Zielwarteschlange vorhanden ist.

   ![Screenshot: Bereich „Verbinden“ mit dem angegebenen Anzeigenamen für die Verbindung und dem ausgewähltem Service Bus-Namespace.](./media/create-replication-tasks-azure-resources/connect-target-service-bus-namespace.png)

   > [!TIP]
   > Sie können die Verbindung stattdessen auch mit einer Verbindungszeichenfolge erstellen. Mit dieser Option können Sie festlegen, dass das Ziel in einem anderen Abonnement vorhanden ist, sodass Sie die abonnementübergreifende Replikation einrichten können. Die Quelle basiert auf dem Ort, an dem Sie mit dem Erstellen des Replikationsaufgabe begonnen haben, und das Ziel wird dynamisch konfiguriert, sodass Sie nur die Verbindung mit dem Ziel herstellen müssen. Um eine Verbindungszeichenfolge zu verwenden, führen Sie die folgenden Schritte aus:
   >
   > 1. Wählen Sie im Bereich **Verbinden** die Option **Über Verbindungszeichenfolge verbinden** aus.
   >
   > 2. Geben Sie im Feld **Verbindungszeichenfolge** die Verbindungszeichenfolge für den Zielnamespace ein.

   Das folgende Beispiel zeigt die erfolgreich erstellte Verbindung:

   ![Screenshot: Bereich „Aufgabe hinzufügen“ mit erstellter Verbindung mit dem Service Bus-Namespace.](./media/create-replication-tasks-azure-resources/connected-service-bus-namespaces.png)

1. Wenn Sie alle Verbindungen erstellt haben, wählen Sie **Weiter: Konfigurieren** aus.

1. Geben Sie auf der Registerkarte **Konfigurieren** einen Namen für die Aufgabe und alle weiteren für die Aufgabe erforderlichen Informationen an.

   > [!NOTE]
   > Sie können den Namen der Aufgabe nach deren Erstellung nicht mehr ändern. Erwägen Sie daher einen Namen, der auch dann noch zutrifft, wenn Sie den [zugrunde liegenden Workflow bearbeiten](#edit-task-workflow). Änderungen, die Sie am zugrunde liegenden Workflow vornehmen, gelten nur für die Aufgabe, die Sie erstellt haben, nicht für die Aufgabenvorlage.
   >
   > Wenn Sie z. B. die Aufgabe `fabrikam-rep-weu-wus` nennen, den zugrunde liegenden Workflow jedoch später für einen anderen Zweck bearbeiten, können Sie den Aufgabennamen nicht entsprechend ändern.

   1. Um den Aufgabenworkflow einer vorhandenen Ressource für **Logic Apps (Standard)** hinzuzufügen, wählen Sie in der Liste **Logik-App** die vorhandene Logik-App aus. Wenn Sie stattdessen eine neue Ressource für **Logic Apps (Standard)** erstellen möchten, wählen Sie in der Liste **Logik-App** die Option **Neu erstellen** aus, und geben Sie den Namen an, der für die neue Logik-App verwendet werden soll.

      > [!NOTE]
      > Wenn Sie während der Erstellung der Replikationsaufgabe eine neue Logik-App-Ressource erstellen, wird diese App in *derselben Region wie die Quellentität erstellt*. Dies führt zu einem Problem, wenn die Quellregion nicht mehr verfügbar ist und in einem Failoverszenario nicht mehr funktioniert. Die Best Practice besteht in diesem Fall darin, eine Ressource für **Logic Apps (Standard)** in einer anderen Region als der Quelle zu erstellen. Wenn Sie die Replikationsaufgabe erstellen, wählen Sie dann stattdessen die vorhandene Logik-App aus, und fügen Sie dieser den zugrunde liegenden zustandslosen Workflow hinzu. Weitere Informationen finden Sie in den [Voraussetzungen](#prerequisites).

   1. Wählen Sie abschließend **Überprüfen + erstellen** aus.

   ![Screenshot: Bereich „Aufgabe hinzufügen“ mit Informationen zur Replikationsaufgabe, z. B. Aufgabenname, Namen der Quell- und Zielwarteschlangen und für die Logik-App-Ressource zu verwendender Name.](./media/create-replication-tasks-azure-resources/configure-replication-task.png)

1. Bestätigen Sie auf der Registerkarte **Überprüfen + erstellen** die Azure-Ressourcen, die von der Replikationsaufgabe benötigt werden.

   - Wenn Sie eine neue Logik-App-Ressource für die Replikationsaufgabe erstellen möchten, werden im Bereich die erforderlichen Azure-Ressourcen angezeigt, die die Replikationsaufgabe für den Vorgang erstellt. Zu diesen Ressourcen gehört u. a. ein Azure Storage-Konto, das Konfigurationsinformationen für die Logik-App-Ressource, den Workflow und andere Laufzeitvorgänge enthält. Für Event Hubs enthält dieses Speicherkonto Prüfpunktinformationen und die Position oder den *Offset* im Datenstrom, an dem die Quellentität angehalten wird, wenn die Quellregion unterbrochen wird oder nicht mehr verfügbar ist.

     Das folgende Beispiel zeigt die Registerkarte **Überprüfen + erstellen**, wenn Sie eine neue Logik-App erstellen möchten:

     ![Screenshot: Bereich „Überprüfen + erstellen“ mit Ressourceninformationen beim Erstellen einer neuen Logik-App.](./media/create-replication-tasks-azure-resources/validate-replication-task-new-logic-app.png)

   - Wenn Sie eine vorhandene Logik-App-Ressource für die Replikationsaufgabe wiederverwenden möchten, werden im Bereich die Azure-Ressourcen angezeigt, die von der Replikation für den Vorgang wiederverwendet werden.

     Das folgende Beispiel zeigt die Registerkarte **Überprüfen + erstellen**, wenn Sie eine vorhandene Logik-App wiederverwenden möchten:

     ![Screenshot: Bereich „Überprüfen + erstellen“ mit Ressourceninformationen bei Wiederverwendung einer vorhandenen Logik-App.](./media/create-replication-tasks-azure-resources/validate-replication-task-existing-logic-app.png)

   > [!NOTE]
   > Wenn sich die Quelle, das Ziel oder beide hinter einem virtuellen Netzwerk befinden, müssen Sie Berechtigungen und Zugriff einrichten, nachdem Sie die Aufgabe erstellt haben. In diesem Szenario sind Berechtigungen und Zugriff erforderlich, damit der Logik-App-Workflow die Replikationsaufgabe ausführen kann.

1. Wählen Sie **Erstellen** aus, wenn Sie fertig sind.

   Die Aufgabe, die Sie erstellt haben, die automatisch aktiv ist und ausgeführt wird, wird jetzt in der Liste **Aufgaben** angezeigt.

   > [!TIP]
   > Wenn die Aufgabe nicht sofort angezeigt wird, versuchen Sie, die Aufgabenliste zu aktualisieren, oder warten Sie ein wenig vor dem Aktualisieren. Wählen Sie auf der Symbolleiste **Aktualisieren** aus.

   ![Screenshot: Bereich „Aufgaben“ mit erstellter Replikationsaufgabe.](./media/create-replication-tasks-azure-resources/created-replication-task.png)

1. Wenn sich Ihre Ressourcen hinter einem virtuellen Netzwerk befinden, denken Sie daran, für die Logik-App-Ressource und den Workflow Berechtigungen zum Zugriff auf diese Ressourcen einzurichten.

## <a name="set-up-retry-policy"></a>Einrichten einer Wiederholungsrichtlinie

Um Datenverluste während eines Verfügbarkeitsereignisses auf einer der Seiten einer Replikationsbeziehung zu vermeiden, müssen Sie die Wiederholungsrichtlinie mit Fokus auf Stabilität konfigurieren. Informationen zum Konfigurieren der Wiederholungsrichtlinie für eine Replikationsaufgabe finden Sie in der [Dokumentation zu Wiederholungsrichtlinien in Azure Logic Apps](logic-apps-exception-handling.md#retry-policies) und den Schritten zum [Bearbeiten des zugrunde liegenden Workflows](#edit-task-workflow).

<a name="review-task-history"></a>

## <a name="review-task-history"></a>Überprüfen des Aufgabenverlaufs

Dieses Beispiel zeigt, wie Sie den Verlauf der Workflowausführungen einer Aufgabe zusammen mit ihren Status, Eingaben, Ausgaben und anderen Informationen anzeigen und das Beispiel für einen Replikationsaufgabe für eine Service Bus Warteschlange verwenden.

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach der Ressource oder Entität mit dem Aufgabenverlauf, den Sie überprüfen möchten.

   In diesem Beispiel ist diese Ressource ein Service Bus-Namespace.

1. Wählen Sie im Navigationsmenü der Ressource unter **Einstellungen** im Abschnitt **Automatisierung** den Eintrag **Aufgaben (Vorschau)** aus.

1. Suchen Sie im Bereich **Aufgaben** nach der Aufgabe, die Sie überprüfen möchten. Wählen Sie in der Spalte **Ausführungen** dieser Aufgabe **Ansicht** aus.

   ![Screenshot: Bereich „Tasks“ mit einer Replikationsaufgabe und der ausgewählten Option „Anzeigen“.](./media/create-replication-tasks-azure-resources/view-runs-for-task.png)

   In diesem Schritt wird der Bereich **Übersicht** für den zugrunde liegenden zustandslosen Workflow geöffnet, der in einer Ressource für Logic Apps (Standard) enthalten ist.

1. Um den Ausführungsverlauf für einen zustandslosen Workflow anzuzeigen, wählen Sie auf der Symbolleiste des Bereichs **Übersicht** die Option **Debugmodus aktivieren** aus.

   Auf der Registerkarte **Ausführungsverlauf** werden alle vorherigen, laufenden und wartenden Ausführungen für die Aufgabe zusammen mit ihren Bezeichnern, Status, Startzeiten und Ausführungszeiträumen angezeigt.

   ![Screenshot: Ausführungen einer Aufgabe, zugehörige Statuswerte und andere Informationen.](./media/create-replication-tasks-azure-resources/run-history-list.png)

   In der folgenden Tabelle werden die möglichen Status für eine Ausführung beschrieben:

   | Status-Label | BESCHREIBUNG |
   |--------------|-------------|
   | **Abgebrochen** | Die Aufgabe wurde während der Ausführung abgebrochen. |
   | **Fehler** | Die Aufgaben hat mindestens eine fehlgeschlagene Aktion, aber es gab keine nachfolgenden Aktionen, um den Fehler zu beheben. |
   | **Wird ausgeführt** | Die Aufgabe wird gerade ausgeführt. |
   | **Erfolgreich** | Alle Aktionen waren erfolgreich. Eine Aufgabe kann auch noch erfolgreich abgeschlossen werden, wenn eine Aktion fehlgeschlagen ist, aber eine nachfolgende Aktion vorhanden war, um den Fehler zu beheben. |
   | **Wartet** | Die Ausführung wurde noch nicht gestartet und ist angehalten, weil noch eine frühere Instanz der Aufgabe ausgeführt wird. |
   |||

1. Um die Statuswerte und anderen Informationen für jeden Schritt in einer Ausführung zu überprüfen, wählen Sie diese Ausführung aus.

   Der Bereich mit den Ausführungsdetails wird geöffnet und zeigt den zugrunde liegenden Workflow an, der ausgeführt wurde.

   - Ein Workflow beginnt immer mit einem [*Trigger*](../connectors/apis-list.md#triggers). Für diese Aufgabe startet der Workflow mit einem Service Bus-Trigger, der darauf wartet, dass Nachrichten in der Service Bus-Quellwarteschlange eintreffen.

   - Jeder Schritt zeigt seinen Status und die Ausführungsdauer an. Die Ausführung von Schritten mit einer Ausführungsdauer von 0 Sekunden war kürzer als 1 Sekunde.

   ![Screenshot: Ausführungsschritte, Status und Ausführungsdauer im Workflow.](./media/create-replication-tasks-azure-resources/run-history-details.png)

1. Um die Eingaben und Ausgaben für jeden Schritt zu überprüfen, wählen Sie den entsprechenden Schritt aus. Damit wird ein Bereich geöffnet, in dem die Eingaben, Ausgaben und Eigenschaftendetails für diesen Schritt angezeigt werden.

   Dieses Beispiel zeigt die Eingaben für den Service Bus-Trigger.

   ![Screenshot: Triggereingaben, Ausgaben und Eigenschaften.](./media/create-replication-tasks-azure-resources/view-trigger-inputs-outputs-properties.png)

Informationen dazu, wie Sie selbst automatisierte Workflows erstellen können, damit Sie Apps, Daten, Dienste und Systeme außerhalb des Kontextes von Replikationsaufgaben für Azure-Ressourcen integrieren können, finden Sie unter [Erstellen eines Integrationsworkflows mit der Azure Logic Apps-Einzelmandanteninstanz (Standard) im Azure-Portal](create-single-tenant-workflows-azure-portal.md).

<a name="monitor"></a>

## <a name="monitor-replication-tasks"></a>Überwachen von Replikationsaufgaben

Um die Leistung und Integrität Ihrer Replikationsaufgabe oder des zugrunde liegenden Logik-App-Workflows zu überprüfen, können Sie [Application Insights](../azure-monitor/app/app-insights-overview.md) verwenden, eine Funktion in Azure Monitor. Die [Application Insights-Anwendungsübersicht](../azure-monitor/app/app-map.md) ist ein nützliches visuelles Tool, mit dem Sie Replikationsaufgaben überwachen können. Diese Übersicht wird automatisch aus den erfassten Überwachungsinformationen generiert, sodass Sie die Leistung und Zuverlässigkeit der Quell- und Zielübertragungen einer Replikationsaufgabe untersuchen können. Um sofortige Diagnoseerkenntnisse und eine Visualisierung der Protokolldetails mit geringer Latenz zu erzielen, können Sie mit dem [Live Metrics](../azure-monitor/app/live-stream.md)-Portaltool arbeiten, das ebenfalls eine Funktion in Azure Monitor ist.

<a name="edit-task"></a>

## <a name="edit-the-task"></a>Bearbeiten der Aufgabe

Zum Ändern einer Aufgabe stehen Ihnen folgende Optionen zur Verfügung:

- [Bearbeiten der Aufgabe „inline“](#edit-task-inline), sodass Sie die Eigenschaften der Aufgabe ändern können, z. B. Verbindungs- oder Konfigurationsinformationen

- [Bearbeiten des zugrunde liegenden Workflows der Aufgabe](#edit-task-workflow) im Workflow-Designer

<a name="edit-task-inline"></a>

### <a name="edit-the-task-inline"></a>Bearbeiten der Aufgabe inline

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) die Ressource, die die Aufgabe besitzt, die Sie aktualisieren möchten.

1. Scrollen Sie im Navigationsmenü zum Abschnitt **Automatisierung**, und wählen Sie **Aufgaben (Vorschau)** aus.

1. Suchen Sie in der Aufgabenliste die Aufgabe, die Sie aktualisieren möchten. Öffnen Sie das Menü der Aktion mit den Auslassungspunkten ( **...** ), und wählen Sie **Inline bearbeiten** aus.

   ![Screenshot: geöffnetes Menü mit Auslassungspunkten und ausgewählte Option „Inline bearbeiten“.](./media/create-replication-tasks-azure-resources/edit-task-in-line.png)

   Standardmäßig wird die Registerkarte **Authentifizierung** angezeigt, auf der die vorhandenen Verbindungen angezeigt werden.

1. Um neue Anmeldeinformationen für die Authentifizierung hinzuzufügen oder andere vorhandene Anmeldeinformationen für die Authentifizierung einer Verbindung auswählen möchten, öffnen Sie das Menü mit den Auslassungspunkten ( **...** ) der Verbindung, und wählen Sie entweder **Neue Verbindung hinzufügen** oder, falls verfügbar, andere Anmeldeinformationen für die Authentifizierung aus.

   > [!NOTE]
   > Sie können nur die Zielverbindung bearbeiten, nicht die Quellverbindung.

   ![Screenshot: Registerkarte „Authentifizierung“, vorhandene Verbindungen und ausgewähltes Menü mit Auslassungspunkten.](./media/create-replication-tasks-azure-resources/edit-connections.png)

1. Um andere Aufgabeneigenschaften zu aktualisieren, wählen Sie **Weiter: Konfigurieren** aus.

   Für die Aufgabe in diesem Beispiel können Sie verschiedene Quell- und Zielwarteschlangen angeben. Der Aufgabenname, die zugrunde liegende Logik-App und der zugrunde liegende Workflow bleiben jedoch unverändert.

   ![Screenshot: Registerkarte „Konfigurieren“ und Eigenschaften, die bearbeitet werden können.](./media/create-replication-tasks-azure-resources/edit-task-configuration.png)

1. Klicken Sie auf **Speichern**, wenn Sie fertig sind.

<a name="edit-task-workflow"></a>

### <a name="edit-the-tasks-underlying-workflow"></a>Bearbeiten des der Aufgabe zugrunde liegenden Workflows

Sie können den zugrunde liegenden Workflow einer Replikationsaufgabe bearbeiten. Dadurch ändert sich die ursprüngliche Konfiguration für die erstellte Aufgabe, aber nicht die Aufgabenvorlage selbst. Nachdem Sie Änderungen vorgenommen und gespeichert haben, führt die bearbeitete Aufgabe nicht mehr die gleiche Funktion wie die ursprüngliche Aufgabe aus. Wenn Sie eine Aufgabe benötigen, die die ursprüngliche Funktion ausführt, müssen Sie möglicherweise eine neue Aufgabe mit derselben Vorlage erstellen. Wenn Sie die ursprüngliche Aufgabe nicht neu erstellen möchten, sollten Sie den Workflow der Aufgabe nicht mit dem Designer ändern. Erstellen Sie stattdessen einen zustandslosen Workflow für **Logic Apps (Standard)** , um Ihre Integrationsanforderungen zu erfüllen. Weitere Informationen finden Sie unter [Erstellen eines Integrationsworkflows mit der Azure Logic Apps-Einzelmandanteninstanz (Standard) im Azure-Portal](create-single-tenant-workflows-azure-portal.md).

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) die Ressource, die die Aufgabe besitzt, die Sie aktualisieren möchten.

1. Scrollen Sie im Navigationsmenü zum Abschnitt **Automatisierung**, und wählen Sie **Aufgaben** aus.

1. Suchen Sie in der Aufgabenliste die Aufgabe, die Sie aktualisieren möchten. Öffnen Sie das Menü der Aufgabe mit den Auslassungspunkten ( **...** ), und wählen Sie **In Logic Apps öffnen** aus.

   ![Screenshot: geöffnetes Menü mit Auslassungspunkten und ausgewählte Option „In Logic Apps öffnen“.](./media/create-replication-tasks-azure-resources/open-task-in-designer.png)

   Das Azure-Portal ändert den Kontext in „Designer“. Dort können Sie den Workflow jetzt bearbeiten.

   ![Screenshot: Designer und zugrunde liegender Workflow.](./media/create-replication-tasks-azure-resources/view-task-workflow-designer.png)

   Sie können jetzt den Trigger und die Aktionen des Workflows sowie die Eigenschaften für den Trigger und die Aktionen bearbeiten.

1. Um die Eigenschaften für den Trigger oder eine Aktion anzuzeigen, wählen Sie diesen Trigger oder die Aktion aus.

   ![Screenshot: Bereich mit Eigenschaften für den Service Bus-Trigger.](./media/create-replication-tasks-azure-resources/edit-service-bus-trigger.png)

   Für dieses Beispiel wird die Eigenschaft **IsSessionsEnabled** des Triggers in **Ja** geändert.

1. Um Ihre Änderungen zu speichern, wählen Sie auf der Symbolleiste des Designers **Speichern** aus.

   ![Screenshot: Symbolleiste des Designers und ausgewählter Befehl „Speichern“.](./media/create-replication-tasks-azure-resources/save-updated-workflow.png)

1. Um den aktualisierten Workflow zu testen und auszuführen, öffnen Sie die Logik-App-Ressource, die den aktualisierten Workflow enthält. Wählen Sie im Navigationsmenü des Workflows die Option **Übersicht** > **Trigger ausführen** > **Ausführen**.

   Nach Abschluss der Ausführung werden im Designer Ausführungsdetails des Workflows angezeigt. Um die Eingaben und Ausgaben für jeden Schritt zu überprüfen, wählen Sie den entsprechenden Schritt aus. Damit wird ein Bereich geöffnet, in dem die Eingaben, Ausgaben und Eigenschaftendetails für diesen Schritt angezeigt werden.

   Dieses Beispiel zeigt die ausgewählten Eingaben, Ausgaben und Eigenschaften des Service Bus-Triggers sowie den aktualisierten Triggereigenschaftswert.

   ![Screenshot: Ausführungsdetails des Workflows mit den Eingaben, Ausgaben und Eigenschaften des Triggers.](./media/create-replication-tasks-azure-resources/view-updated-run-details-trigger-inputs.png)

1. Um den Workflow zu deaktivieren, sodass die Aufgabe nicht weiter ausgeführt wird, wählen Sie auf der Symbolleiste **Übersicht** die Option **Deaktivieren** aus. Weitere Informationen finden Sie unter [Deaktivieren oder Aktivieren von Workflows](create-single-tenant-workflows-azure-portal.md#disable-or-enable-workflows).

<a name="failover"></a>

## <a name="set-up-failover-for-azure-event-hubs"></a>Einrichten des Failovers für Azure Event Hubs

Für eine Azure Event Hubs-Replikation zwischen den gleichen Entitätstypen erfordert die georedundante Notfallwiederherstellung ein Failover von der Quellentität zur Zielentität. Anschließend müssen alle betroffenen Ereignisconsumer und -producer darüber informiert werden, dass sie den Endpunkt für die Zielentität verwenden müssen, die zur neuen Quelle wird. Wenn also ein Notfall eintritt und für die Quellentität ein Failover ausgeführt wird, werden Consumer und Producer – einschließlich Ihrer Replikationsaufgabe – an die neue Quelle umgeleitet. Das von der Replikationsaufgabe erstellte Speicherkonto enthält Prüfpunktinformationen und die Position oder den Offset im Datenstrom, an dem die Quellentität angehalten wird, wenn die Quellregion unterbrochen wird oder nicht mehr verfügbar ist.

Um sicherzustellen, dass das Speicherkonto keine veralteten Informationen aus der ursprünglichen Quelle enthält und die Replikationsaufgabe mit dem Lesen und Replizieren von Ereignissen ab dem Start des neuen Quelldatenstroms beginnt, müssen Sie alle Altdaten aus der ursprünglichen Quelle manuell bereinigen und die Replikationsaufgabe neu konfigurieren.

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) die Logik-App-Ressource oder den zugrunde liegenden Workflow der Replikationsaufgabe.

   > [!NOTE]
   > Die Logik-App-Ressource sollte nur Workflows für die Replikationsaufgabe enthalten.

1. Wählen Sie im Navigationsmenü der Ressource oder des Workflows die **Übersicht** aus. Wählen Sie auf der Symbolleiste **Übersicht** entweder **Deaktivieren** für den Workflow oder **Beenden** für die Logik-App-Ressource aus.

1. Gehen Sie wie folgt vor, um das Speicherkonto zu finden, das von der der Replikationsaufgabe zugrundeliegenden Logic-App-Ressource verwendet wird, um die Checkpoint- und Stream-Offset-Informationen der Quell-Entität zu speichern:

   1. Klicken Sie im Logik-App-Menü unter **Einstellungen** auf **Konfiguration**.

   1. Wählen Sie im Bereich **Konfiguration** auf der **Registerkarte Anwendungseinstellungen** die **App-Einstellung AzureWebJobsStorage** aus.

      Diese Einstellung gibt die Verbindungszeichenfolge und das Speicherkonto an, die von der Logik-App-Ressource verwendet werden.

      > [!NOTE]
      > Wenn die App-Einstellung nicht in der Liste angezeigt wird, wählen Sie **Werte anzeigen** aus.

   1. Wählen Sie die **App-Einstellung AzureWebJobsStorage** aus, damit Sie den Namen des Speicherkontos anzeigen können.

   In diesem Beispiel wird gezeigt, wie Sie den Namen für dieses Speicherkonto finden, der hier `storagefabrikamreplb0c` lautet:

   ![Der Screenshot zeigt den Bereich "Konfiguration" der zugrunde liegenden Logik-App-Ressource mit der App-Einstellung "AzureWebJobsStorage" und dem Verbindungsstring mit dem Namen des Speicherkontos.](./media/create-replication-tasks-azure-resources/find-storage-account-name.png)

   1. Um zu bestätigen, dass die Speicherkontoressource vorhanden ist, geben Sie im Suchfeld Azure-Portal den Namen ein, und wählen Sie dann das Speicherkonto aus. Beispiel:

   ![Screenshot, der das Azure-Portal mit dem eingegebenen Speicherkontonamen zeigt.](./media/create-replication-tasks-azure-resources/find-storage-account.png)

1. Löschen Sie nun den Ordner, der die Prüfpunkt- und Offsetinformationen der Quellentität enthält, und zwar mithilfe der folgenden Schritte:

   1. Laden Sie den neuesten [Azure Storage Explorer Desktop-Client](https://azure.microsoft.com/features/storage-explorer/) herunter, installieren Sie ihn und öffnen Sie ihn, falls Sie noch nicht die neueste Version besitzen.

      > [!NOTE]
      > Für die Bereinigungsaufgabe "Löschen" müssen Sie derzeit den Azure Storage Explorer Client verwenden, *nicht* den Storage Explorer, Browser, Editor oder die Verwaltungserfahrung im Azure-Portal.
      >
      > Obwohl Sie Containerordner mit dem PowerShell-Befehl [`Remove-AzStorageDirectory` löschen können](/powershell/module/az.storage/remove-azstoragedirectory), funktioniert dieser Befehl nur bei *leeren* Ordnern.

   1. Falls noch nicht geschehen, melden Sie sich mit Ihrem Azure-Konto an, und stellen Sie sicher, dass Ihr Azure-Abonnement für Ihre Speicherkontoressource ausgewählt ist. Weitere Informationen finden Sie unter [Erste Schritte mit Storage-Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).

   1. Wechseln Sie im Explorer-Fenster unter ihrem Azure-Abonnementnamen zu **Storage Accounts** >  **{*your-storage-account-name*}**  > **Blob Containers** > **azure-webjobs-eventhub**.

      > [!NOTE]
      > Wenn der **Ordner azure-webjobs-eventhub** nicht vorhanden ist, wurde der Replikationsvorgang noch nicht ausgeführt. Der Ordner wird erst angezeigt, nachdem der Replikationsaufgabe mindestens einmal ausgeführt wurde.

      ![Screenshot, der den Azure Storage-Explorer mit geöffneten Speicherkonto und Blobcontainer zeigt, um den ausgewählten Ordner "azure-webjobs-eventhub" zu zeigen.](./media/create-replication-tasks-azure-resources/azure-webjobs-eventhub-storage-explorer.png)

   1. Wählen Sie im Bereich **azure-webjobs-eventhub** den Namensraumordner aus, dessen Name im folgenden Format angegeben ist: `<source-Event-Hubs-namespace-name>.servicebus.windows.net`.

   1. Nachdem der Namespace-Ordner geöffnet wurde, wählen Sie im Fenster **azure-webjobs-eventhub** den Ordner <*former-source-entity-name*> aus. Wählen Sie entweder in der Symbolleiste oder im Kontextmenü des Ordners die Option **Löschen**, zum Beispiel:

      ![Screenshot: Der frühere  Quell-Event-Hubs-Entitätenordner ist ausgewählt und die Schaltfläche "Löschen" ebenfalls.](./media/create-replication-tasks-azure-resources/delete-former-source-entity-folder-storage-explorer.png)

   1. Bestätigen Sie, dass Sie den Ordner löschen möchten.

1. Kehren Sie zu der Logik-App-Ressource bzw. zum zugrunde liegenden Workflow der Replikationsaufgabe zurück. Starten Sie die Logik-App neu, oder aktivieren Sie den Workflow erneut.

Damit Producer und Consumer den neuen Quellendpunkt verwenden können, müssen Sie die Informationen zur neuen Quellentität zur Verwendung und Suche an einem leicht erreichbaren und aktuellen Speicherort zur Verfügung stellen. Wenn Producer oder Consumer auf häufige oder anhaltende Fehler stoßen, sollten sie diesen Speicherort prüfen und ihre Konfiguration anpassen. Es gibt zahlreiche Möglichkeiten, diese Konfiguration freizugeben, beispielsweise über DNS oder Dateifreigaben.

Weitere Informationen zur georedundanten Notfallwiederherstellung finden Sie in den folgenden Dokumenten:

- [Azure Event Hubs: Georedundante Notfallwiederherstellung](../event-hubs/event-hubs-geo-dr.md)
- [Georedundante Notfallwiederherstellung in Azure Service Bus](../service-bus-messaging/service-bus-geo-dr.md)

<a name="edit-plan-scale-out-settings"></a>

## <a name="edit-hosting-plan-scale-out-settings"></a>Bearbeiten der Einstellungen für das Aufskalieren des Hostingplans

### <a name="portal"></a>[Portal](#tab/portal)

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) die Logik-App-Ressource oder den zugrunde liegenden Workflow der Replikationsaufgabe.

1. Wählen Sie im Ressourcenmenü der Logik-Anwendung unter **Einstellungen** die Option **Skalieren (App Service Plan)** aus.

   ![Screenshot, der die Hostingplaneinstellungen für maximale Ausbrüche, minimale Instanzen, immer einsatzbereite Instanzen und die Durchsetzung von Grenzwerten zeigt.](./media/create-replication-tasks-azure-resources/edit-app-service-plan-settings.png)

1. Ändern Sie je nach den Anforderungen Ihres Szenarios unter **Plan Scale out** und **App Scale out** die Werte für die maximalen Ausbruch- bzw. Immer bereit-Instanzen.

1. Wenn Sie fertig sind, klicken Sie in der Symbolleiste des Bereichs **Aufskalieren (App Service-Plan)** auf **Speichern**.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azurecli)

Sie können jederzeit bereite Instanzen für eine App auch mit der Azure CLI konfigurieren.

```azurecli-interactive
az resource update -g <resource_group> -n <logic-app-app-name>/config/web --set properties.minimumElasticInstanceCount=<desired_always_ready_count> --resource-type Microsoft.Web/sites
```

---

Weitere Informationen finden Sie in der folgenden Dokumentation, da der Workflow-Standardplan einige Aspekte mit dem Azure Funktionen Premium Plan gemeinsam hat:

- [Plan- und SKU-Einstellungen - Azure Funktionen Premium Plan](../azure-functions/functions-premium-plan.md#plan-and-sku-settings)
- [Was ist Cloudburstung](https://azure.microsoft.com/overview/what-is-cloud-bursting/)?
- [Immer bereite Instanzen – Azure Funktionen Premium Plan](../azure-functions/functions-premium-plan.md#always-ready-instances)

<a name="problems-failures"></a>

## <a name="replication-problems-and-failures"></a>Probleme und Fehler bei der Replikation

In diesem Abschnitt werden potenzielle Fehler oder Funktionsausfälle bei der Replikation beschrieben:

- Beschränkungen der Nachrichtengröße

  Stellen Sie sicher, dass die gesendeten Nachrichten kleiner als 1 MB sind, da die Replikationsaufgabe [Replikationseigenschaften](#replication-properties) hinzufügt. Wenn nach dem Hinzufügen von [Replikationseigenschaften](#replication-properties) durch die Replikationsaufgabe die Nachricht größer ist als die Ereignisgröße, die an eine Event Hubs-Entität gesendet werden kann, wird der Replikationsprozess nicht erfolgreich ausgeführt.

  Nehmen wir zum Beispiel an, die Nachricht ist 1 MB groß. Nachdem die Aufgabe Replikationseigenschaften hinzufügt hat, ist die Nachricht größer als 1 MB. Beim ausgehenden Aufruf, der versucht, die Nachricht zu senden, tritt ein Fehler auf.

- Partitionsschlüssel

  Wenn in den Ereignissen Partitionsschlüssel vorhanden sind, treten bei der Replikation zwischen Event Hubs-Instanzen Fehler auf, wenn diese Instanzen die gleiche Anzahl von Partitionen aufweisen.

## <a name="next-steps"></a>Nächste Schritte

- [Informationen zum Workflow-Designer in Einzelmandanten-Azure Logic Apps](designer-overview.md)
- [Bearbeiten von Einstellungen für Hosts und Apps für Logik-Apps in Azure Logic Apps-Instanzen mit einem einzelnen Mandanten](edit-app-settings-host-settings.md)
- [Erstellen von Parametern für die Verwendung in Workflows umgebungsübergreifend in Azure Logic Apps-Instanzen mit nur einem Mandanten](parameterize-workflow-app.md)
