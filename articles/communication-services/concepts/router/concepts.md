---
title: Übersicht über den Auftragsrouter für Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Hier finden Sie Informationen zum Auftragsrouter von Azure Communication Services.
author: jasonshave
manager: phans
services: azure-communication-services
ms.author: jassha
ms.date: 10/14/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 86902147b68272fb0ff2ef5873ad6cca80654843
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312398"
---
# <a name="job-router-concepts"></a>Auftragsrouter – Konzepte

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

Der Auftragsrouter von Azure Communication Services löst das Problem des Abgleichs eines abstrakten Angebots mit einem abstrakten Bedarf auf einem System. Der mit Azure Event Grid Integrierte Auftragsrouter stellt Ihnen nahezu in Echtzeit Benachrichtigungen zur Verfügung, sodass Sie reaktive Anwendungen erstellen können, um das Verhalten Ihrer Auftragsrouterinstanz zu steuern.

## <a name="job-router-overview"></a>Auftragsrouter – Übersicht

Die Auftragsrouter-SDKs können verwendet werden, um verschiedene Geschäftsszenarios zu erstellen, in denen Sie eine Arbeitseinheit mit einer bestimmten Ressource abgleichen müssen. Die Arbeit kann beispielsweise als eine Reihe von Telefonanrufen mit vielen potenziellen Contact Center-Agents oder als Webchatanforderung mit einem Live-Agent definiert werden, der mehrere gleichzeitige Sitzungen mit anderen Personen abarbeitet. Die Notwendigkeit, eine abstrakte Arbeitseinheit an eine verfügbare Ressource weiterzuleiten, erfordert, dass Sie die Arbeit definieren, die als [Auftrag](#job) bezeichnet wird, eine [Warteschlange](#queue), den [Worker](#worker) und eine Reihe von [Richtlinien](#policies), die die Verhaltensaspekte der Interaktion dieser Komponenten miteinander definieren.

## <a name="job-router-architecture"></a>Auftragsrouter – Architektur

Der Auftragsrouter von Azure Communication Services verwendet Ereignisse, um Ihre Anwendungen über Aktionen innerhalb des Diensts zu benachrichtigen. Die folgenden Diagramme veranschaulichen einen vereinfachten Flow, der für den Auftragsrouter üblich ist: Übermitteln eines Auftrags, Registrieren eines Workers, Behandeln des Auftragsangebots.

### <a name="job-submission-flow"></a>Flow für Auftragsübermittlung

1. Die Contoso-Anwendung übermittelt einen Auftrag an den Auftragsrouter in der Azure Communication Services-Instanz.
2. Der Auftrag wird klassifiziert, und es wird ein Ereignis namens **RouterJobClassified** ausgelöst, das alle Informationen zum Auftrag und dazu enthält, wie der Klassifizierungsprozess seine Eigenschaften möglicherweise geändert hat.
 
    :::image type="content" source="../media/router/acs-router-job-submission.png" alt-text="Diagramm: Auftragsrouter von Communication Services, der einen Auftrag übermittelt":::

### <a name="worker-registration-flow"></a>Flow für Workerregistrierung

1. Wenn ein Worker bereit ist, einen Auftrag zu akzeptieren, registriert er sich über die Contoso-Anwendung beim Auftragsrouter.
2. Der Auftragsrouter sendet dann ein **RouterWorkerRegistered** zurück.

    :::image type="content" source="../media/router/acs-router-worker-registration.png" alt-text="Diagramm: Workerregistrierung des Auftragsrouters von Communication Services":::

### <a name="matching-and-accepting-a-job-flow"></a>Abgleichen und Akzeptieren eines Auftragsflows

1. Wenn der Auftragsrouter einen passenden Worker für einen Auftrag findet, bietet er die Arbeit an, indem er ein **RouterWorkerOfferIssued** sendet, das die Contoso-Anwendung empfängt; sie sendet ein Signal an den verbundenen Benutzer über eine Plattform wie Azure SignalR Service.
2. Der Worker akzeptiert das Angebot.
3. Der Auftragsrouter sendet ein **RouterWorkerOfferAccepted**, das der Contoso-Anwendung signalisiert, dass der Worker dem Auftrag zugewiesen ist.

    :::image type="content" source="../media/router/acs-router-accept-offer.png" alt-text="Diagramm: Auftragsrouter von Communication Services, der ein Angebot akzeptiert":::

## <a name="real-time-notifications"></a>Echtzeitbenachrichtigungen

Azure Communication Services basiert auf der Messagingplattform von Event Grid, um Benachrichtigungen darüber zu senden, welche Aktionen der Auftragsrouter für die von Ihnen gesendete Workload ausführt. Der Auftragsrouter sendet Nachrichten in Form von Ereignissen, wenn eine wichtige Aktion eintritt, z. B. Ereignisse im Auftragslebenszyklus, einschließlich Auftragserstellung, Abschluss, Angebotsakzeptanz und vieles mehr.

## <a name="job"></a>Auftrag

Ein Auftrag stellt die Arbeitseinheit dar, die an einen verfügbaren Worker weitergeleitet werden muss. Aufträge werden mithilfe der Auftragsrouter-SDKs von Azure Communication Services oder durch Übermitteln einer authentifizierten Anforderung an die REST-API definiert. Aufträge enthalten häufig einen Verweis auf einen eindeutigen Bezeichner, z. B. eine Anruf-ID oder eine Ticketnummer, zusammen mit den Merkmalen der ausgeführten Arbeit.

## <a name="queue"></a>Warteschlange

Wenn ein Auftrag erstellt wird, wird er einer Warteschlange zugewiesen, entweder statisch zum Zeitpunkt der Übermittlung oder dynamisch über die Anwendung einer Klassifizierungsrichtlinie. Aufträge werden nach ihrer zugewiesenen Warteschlange gruppiert und können unterschiedliche Merkmale aufweisen, je nachdem, wie Sie die Workload verteilen möchten. Warteschlangen erfordern eine **Verteilungsrichtlinie**, um zu bestimmen, wie Aufträge berechtigten Workern angeboten werden.

Warteschlangen im Auftragsrouter können auch Ausnahmerichtlinien enthalten, die das Verhalten von Aufträgen bestimmen, wenn bestimmte Bedingungen auftreten. Beispielsweise kann es sein, dass Sie einen Auftrag in eine andere Warteschlange verschieben, die Priorität erhöhen oder beide Aktionen basierend auf einem Timer oder einer anderen Bedingung ausführen möchten.

## <a name="worker"></a>Worker

Ein Worker stellt das Angebot dar, das zum Verarbeiten eines Auftrags für eine bestimmte Warteschlange verfügbar ist. Jeder Worker, der beim Auftragsrouter registriert ist, verfügt über eine Reihe von **Bezeichnungen**, die zugeordneten **Warteschlangen**, **Kanalkonfigurationen** und eine **Gesamtkapazitätsbewertung**. Der Auftragsrouter verwendet diese Faktoren, um zu bestimmen, wann und wie Aufträge in Echtzeit an einen Worker weitergeleitet werden.

Der Auftragsrouter von Azure Communication Services verwaltet und verwendet den Status eines Workers mit den einfachen Angaben **Aktiv**, **Inaktiv** und **Leeren**, um zu bestimmen, wann verfügbare Aufträge mit einem Worker abgeglichen werden können. Zusammen mit dem Status, der Kanalkonfiguration und der Gesamtkapazitätsbewertung berechnet der Auftragsrouter geeignete Worker und gibt Angebote im Zusammenhang mit dem Auftrag aus.

## <a name="policies"></a>Richtlinien

Der Auftragsrouter von Azure Communication Services wendet flexible Richtlinien an, um dynamisches Verhalten an verschiedene Aspekte des Systems anzufügen. Abhängig von der Richtlinie können die Bezeichnungen eines Auftrags genutzt und ausgewertet werden, um die Priorität eines Auftrags, die Warteschlange, in der er sich befinden soll, und vieles mehr zu ändern. Bestimmte Richtlinien im Auftragsrouter bieten eine Inlinefunktionsverarbeitung mithilfe von PowerFx oder für komplexere Szenarios einen Rückruf auf eine Azure-Funktion.

**Klassifizierungsrichtlinie**: Eine Klassifizierungsrichtlinie hilft dem Auftragsrouter, die Warteschlange und die Priorität zu definieren, und kann die Workerselektoren ändern, wenn der Absender diese Parameter zum Zeitpunkt der Übermittlung nicht kennt oder nicht kennen kann. Weitere Informationen zur Klassifizierung finden Sie auf der Seite [Klassifizierungskonzepte](classification-concepts.md).

**Verteilungsrichtlinie**: Wenn der Auftragsrouter einen neuen Auftrag empfängt, wird die Verteilungsrichtlinie verwendet, um einen geeigneten Worker zu finden und die Auftragsangebote zu verwalten. Worker werden mit unterschiedlichen **Modi** ausgewählt, und basierend auf der Richtlinie kann der Auftragsrouter einen oder mehrere Worker gleichzeitig benachrichtigen.

**Ausnahmerichtlinie**: Eine Ausnahmerichtlinie steuert das Verhalten eines Auftrags basierend auf einem Trigger und führt eine gewünschte Aktion aus. Die Ausnahmerichtlinie wird an eine Warteschlange angefügt, damit sie das Verhalten von Aufträgen in der Warteschlange steuern kann.

## <a name="next-steps"></a>Nächste Schritte

- [Routerregelkonzepte](router-rule-concepts.md)
- [Klassifizierungskonzepte](classification-concepts.md)
- [Verteilungskonzepte](distribution-concepts.md)
- [Leitfaden für den Schnellstart](../../quickstarts/router/get-started-router.md)
- [Warteschlangen verwalten](../../how-tos/router-sdk/manage-queue.md)
- [Klassifizieren eines Auftrags](../../how-tos/router-sdk/job-classification.md)
- [Eskalieren eines Auftrags](../../how-tos/router-sdk/escalate-job.md)
- [Abonnieren von Ereignissen](../../how-tos/router-sdk/subscribe-events.md)
