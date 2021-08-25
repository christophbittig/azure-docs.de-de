---
title: Azure Event Grid in Kubernetes – Features
description: In diesem Artikel werden die Features von Event Grid in Kubernetes mit Event Grid in Azure verglichen.
author: jfggdl
ms.subservice: kubernetes
ms.author: jafernan
ms.date: 05/25/2021
ms.topic: conceptual
ms.openlocfilehash: 3e8d2259c4ae41f5f0433151cef8c6f447e49418
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122768103"
---
# <a name="event-grid-on-kubernetes-with-azure-arc-features"></a>Features von Event Grid in Kubernetes mit Azure Arc
Event Grid in Kubernetes bietet eine Vielzahl von Features, mit denen Sie Ihre Kubernetes-Workloads integrieren und Hybridarchitekturen umsetzen können. Die Anwendung nutzt dieselbe [REST-API](/rest/api/eventgrid/version2021-06-01-preview/topics) (ab Version 2020-10-15-preview), die [Event Grid CLI](/cli/azure/eventgrid), die Benutzeroberfläche des Azure-Portals, [Verwaltungs-SDKs](../sdk-overview.md#management-sdks) und [SDKs der Datenebene](../sdk-overview.md#data-plane-sdks) mit Azure Event Grid, der anderen Edition desselben Diensts. Wenn Sie bereit sind, Ereignisse zu veröffentlichen, können Sie die [SDK-Beispiele der Datenebene in verschiedenen Sprachen](https://devblogs.microsoft.com/azure-sdk/event-grid-ga/) verwenden, die für beide Editionen von Event Grid funktionieren.

Obwohl Event Grid in Kubernetes und Azure Event Grid viele Funktionen gemeinsam sind und das Ziel darin besteht, die gleiche Benutzererfahrung zu bieten, gibt es einige Unterschiede angesichts der einzigartigen Anforderungen, die die beiden Editionen zu erfüllen versuchen, und des Stadiums, in dem sie sich in ihrem Softwarelebenszyklus befinden. Der einzige Typ von Thema, der in Event Grid für Kubernetes verfügbar ist, sind beispielsweise Event Grid-Themen, die manchmal auch als benutzerdefinierte Themen bezeichnet werden. Andere Thementypen (siehe unten) sind entweder nicht anwendbar, oder es ist noch keine Unterstützung für sie verfügbar. Die Hauptunterschiede zwischen den beiden Editionen von Event Grid werden in der folgenden Tabelle dargestellt.

[!INCLUDE [event-grid-preview-feature-note.md](../includes/event-grid-preview-feature-note.md)]


## <a name="event-grid-on-kubernetes-vs-event-grid-on-azure"></a>Event Grid in Kubernetes im Vergleich zu Event Grid in Azure

| Funktion | Event Grid in Kubernetes | Azure Event Grid |
|:--|:-:|:-:|
| [Event Grid-Themen](/rest/api/eventgrid/version2021-06-01-preview/topics) | ✔ | ✔ |
| [CNCF Cloud Events-Schema](https://github.com/cloudevents/spec/blob/master/spec.md) | ✔ | ✔ |
| Event Grid und benutzerdefinierte Schemas | ✘* | ✔ |
| Zuverlässige Übermittlung | ✔ | ✔ |
| Metriken  | ✔** | ✔ |
| Azure Monitor  | ✘ | ✔ |
| [Speicherort für unzustellbare Nachrichten](../manage-event-delivery.md#set-dead-letter-location) | ✘ | ✔ |
| [Weiterleiten von Ereignissen an ein anderes Event Grid-Thema](event-handlers.md#azure-event-grid) | ✔ | ✘ |
| [Systemthemen](../system-topics.md) | ✘ | ✔ |
| [Domänenthemen](../event-domains.md) | ✘ | ✔ |
| [Partnerereignisse](../partner-events-overview.md) | ✘ | ✔ |
| [Überprüfung des Zielendpunkts](../webhook-event-delivery.md#endpoint-validation-with-event-grid-events) | ✘ | ✔ |
| [Azure Event Grid-Trigger für Azure Functions](../../azure-functions/functions-bindings-event-grid-trigger.md) | ✘ | ✔ |
| Hybridverbindungen von Azure Relay als Ziel | ✘ | ✔ |
| [Erweiterte Filterung](filter-events.md) | ✔*** | ✔ |
| [Webhook AuthN/AuthZ mit AAD](../secure-webhook-delivery.md) | ✘ | ✔ |
| [Ereignisübermittlung mit Ressourcenidentität](/rest/api/eventgrid/version2021-06-01-preview/event-subscriptions/create-or-update) | ✘ | ✔ |
| Gleicher Satz von SDKs der Datenebene | ✔ | ✔ |
| Gleicher Satz von Verwaltungs-SDKs | ✔ | ✔ |
| Gleiche Event Grid CLI | ✔ | ✔ |

\* Das Cloud Events 1.0-Schema bietet einen Erweiterbarkeitsmechanismus und ist ein offener Standard. Diese Qualitäten oder Features werden nicht vom Event Grid- oder benutzerdefinierten Schemas bereitgestellt. Das Cloud Events  1.0-Schema ist eine Weiterentwicklung des Event Grid-Schemas.

\** Metriken für Themen und Ereignisabonnements werden im [Prometheus-Expositionsformat](https://prometheus.io/docs/instrumenting/exposition_formats/) bereitgestellt. Metriken oder andere Überwachungsfunktionen im Azure-Portal sind in der Vorschauversion derzeit nicht verfügbar.

\*** Event Grid in Kubernetes unterstützt wie Event Grid in Azure die erweiterte Filterung von Ereignissen basierend auf Werten in Ereignisdaten. Es gibt jedoch einige Features und Operatoren, die Event Grid in Kubernetes nicht unterstützt. Weitere Informationen finden Sie unter [Erweiterte Filterung](filter-events.md#filter-by-values-in-event-data).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Event Grid in Kubernetes finden Sie unter [Event Grid in Kubernetes mit Azure Arc (Vorschau) – Übersicht](overview.md).
