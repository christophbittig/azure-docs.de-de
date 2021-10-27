---
title: Azure Communication Services als Event Grid-Quelle – Übersicht
description: In diesem Artikel wird beschrieben, wie Azure Communication Services als Event Grid-Ereignisquelle verwendet wird.
ms.topic: conceptual
ms.date: 06/11/2021
ms.author: mikben
ms.openlocfilehash: 590e0a53ca701a6b293dd52b2f7c30c5e873df30
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130129489"
---
# <a name="event-handling-in-azure-communication-services"></a>Ereignisbehandlung in Azure Communication Services

Azure Communication Services ist in [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) integriert, um Echtzeit-Ereignisbenachrichtigungen zuverlässig, skalierbar und sicher bereitzustellen. In diesem Artikel erfahren Sie, wie Sie Ihre Anwendungen so konfigurieren, dass sie auf Communication Services-Ereignisse lauschen. So können Sie beispielsweise eine Datenbank aktualisieren, eine Arbeitsaufgabe erstellen und eine Pushbenachrichtigung senden, wenn eine SMS-Nachricht von einer Telefonnummer eingeht, die Ihrer Communication Services-Ressource zugeordnet ist.

Azure Event Grid ist ein vollständig verwalteter Ereignisroutingdienst, der ein Veröffentlichen-Abonnieren-Modell verwendet. Event Grid verfügt über eine integrierte Unterstützung für Azure-Dienste wie [Azure Functions](../azure-functions/functions-overview.md) und [Azure Logic Apps](../azure-functions/functions-overview.md). Mithilfe von Webhooks können Ereigniswarnungen an Azure-fremde Dienste übertragen werden. Eine vollständige Liste der Ereignishandler, die Event Grid unterstützt, finden Sie unter [Einführung in Azure Event Grid](overview.md).

:::image type="content" source="./media/overview/functional-model.png" alt-text="Diagramm: Ereignismodell von Azure Event Grid":::

> [!NOTE]
> Weitere Informationen zur Datenresidenz in Bezug auf die Ereignisbehandlung finden Sie in der [konzeptionellen Dokumentation zu Datenresidenz](../communication-services/concepts/privacy.md).

## <a name="events-types"></a>Ereignistypen

Event Grid verwendet [Ereignisabonnements](concepts.md#event-subscriptions) zum Weiterleiten von Ereignisnachrichten an Abonnenten.

Von Azure Communication Services werden folgende Ereignistypen ausgegeben:

* [Chatereignisse](./communication-services-chat-events.md)
* [Ereignisse für Telefonie und SMS](./communication-services-telephony-sms-events.md)
* [Ereignisse für Sprach- und Videoanrufe](./communication-services-voice-video-events.md)
* [Präsenzereignisse](./communication-services-presence-events.md)

Sie können das Azure-Portal oder die Azure CLI verwenden, um Ereignisse zu abonnieren, die von Ihrer Communication Services-Ressource ausgegeben werden. 

## <a name="event-subjects"></a>Ereignisbetreff

Das Feld `subject` gibt bei allen Communication Services-Ereignissen Aufschluss über den Benutzer, die Telefonnummer oder die Entität, an den bzw. an die sich das Ereignis richtet. Zur Ermöglichung einer einfachen [Event Grid-Filterung](event-filtering.md) werden gängige Präfixe verwendet.

| Betreffpräfix                              | Communication Services-Entität |
| ------------------------------------------- | ---------------------------- |
| `phonenumber/`                              | PSTN-Telefonnummer            |
| `user/`                                     | Communication Services-Benutzer  |
| `thread/`                                   | Chatthread                 |

Das folgende Beispiel zeigt einen Filter für alle SMS-Nachrichten und Zustellberichte, die an alle zu einer Communication Services-Ressource gehörenden Telefonnummern mit der Vorwahl „555“ gesendet werden:

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Communication.SMSReceived",
    "Microsoft.Communication.SMSDeliveryReportReceived"
  ],
  "subjectBeginsWith": "phonenumber/1555",
}
```

## <a name="next-steps"></a>Nächste Schritte

* Eine Einführung zu Azure Event Grid finden Sie unter [Einführung in Azure Event Grid](./overview.md).
* Eine Einführung in Azure Event Grid-Konzepte finden Sie [hier](./concepts.md).
* Eine Einführung in Azure Event Grid-Systemthemen finden Sie [hier](./system-topics.md).
