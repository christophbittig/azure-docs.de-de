---
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.openlocfilehash: 4fc2131e57d89a99b44a8ba345b901af5bc8890a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "126056515"
---
- **Verbindung:** Eine Verbindung (auch Client oder Clientverbindung genannt) stellt eine einzelne WebSocket-Verbindung mit dem Web PubSub-Dienst dar. Wenn die Verbindung erfolgreich hergestellt wurde, wird ihr vom Web PubSub-Dienst eine eindeutige Verbindungs-ID zugewiesen.

- **Hub:** Ein Hub ist ein logisches Konzept für eine Gruppe von Clientverbindungen. In der Regel wird jeweils ein einzelner Hub für einen einzelnen Zweck verwendet – beispielsweise ein *Chathub* oder ein *Benachrichtigungshub*. Eine Clientverbindung wird mit einem Hub hergestellt und gehört während ihrer Lebensdauer zu diesem Hub. Von verschiedenen Anwendungen können unterschiedliche Hubnamen verwendet werden, um gemeinsam einen einzelnen Azure Web PubSub-Dienst zu nutzen.

- **Gruppe:** Eine Gruppe ist eine Teilmenge der Verbindungen mit dem Hub. Sie können einer Gruppe eine Clientverbindung hinzufügen und sie jederzeit wieder aus der Gruppe entfernen. Beispiel: Wenn ein Client einem Chatroom beitritt oder wenn ein Client den Chatroom verlässt, kann dieser Chatroom als Gruppe betrachtet werden. Ein Client kann mehreren Gruppen beitreten, und eine Gruppe kann mehrere Clients enthalten.

- **Benutzer:** Verbindungen mit Web PubSub können zu einem einzelnen Benutzer gehören. Ein Benutzer kann über mehrere Verbindungen verfügen, etwa, wenn ein einzelner Benutzer über mehrere Geräte oder mehrere Browsertabs verbunden ist.

- **Nachricht:** Wenn der Client verbunden ist, kann er Nachrichten an die Upstreamanwendung senden oder Nachrichten von der Upstreamanwendung über die WebSocket-Verbindung empfangen.
