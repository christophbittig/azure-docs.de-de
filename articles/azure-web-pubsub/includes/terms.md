---
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.openlocfilehash: 2356b3af08577063326da7dbb3cf8141d52cea07
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122597197"
---
- **Verbindung:** Eine Verbindung (auch Client oder Clientverbindung genannt) stellt eine einzelne WebSocket-Verbindung mit dem Web PubSub-Dienst dar. Wenn die Verbindung erfolgreich hergestellt wurde, wird ihr vom Web PubSub-Dienst eine eindeutige Verbindungs-ID zugewiesen.

- **Hub:** Ein logisches Konzept für eine Gruppe von Clientverbindungen. In der Regel wird jeweils ein einzelner Hub für einen einzelnen Zweck verwendet – beispielsweise ein *Chathub* oder ein *Benachrichtigungshub*. Eine Clientverbindung wird mit einem Hub hergestellt und gehört während ihrer Lebensdauer zu diesem Hub. Von verschiedenen Anwendungen können unterschiedliche Hubnamen verwendet werden, um gemeinsam einen einzelnen Azure Web PubSub-Dienst zu nutzen.

- **Gruppe:** Eine Teilmenge der Verbindungen mit dem Hub. Eine Gruppe ist ein relativ einfaches Element. Sie können einer Gruppe eine Clientverbindung hinzufügen und sie jederzeit wieder aus der Gruppe entfernen. Beispiel: Ein Client tritt einem Chatroom bei und verlässt ihn wieder. In diesem Fall kann der Chatroom eine Gruppe sein. Ein Client kann mehreren Gruppen beitreten, und eine Gruppe kann mehrere Clients enthalten.

- **Benutzer:** Verbindungen mit Web PubSub können zu einem einzelnen Benutzer gehören. Ein Benutzer kann über mehrere Verbindungen verfügen, etwa, wenn ein einzelner Benutzer über mehrere Geräte oder mehrere Browsertabs verbunden ist.

- **Nachricht:** Wenn der Client verbunden ist, kann er Nachrichten an Upstreamkomponenten senden oder Nachrichten von Upstreamkomponenten über die WebSocket-Verbindung empfangen.
