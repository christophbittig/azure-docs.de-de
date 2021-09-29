---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 09/10/2021
ms.author: spelluru
ms.openlocfilehash: 68b0eb42d111abbdf572926ffb506065a79fa316
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128664928"
---
## <a name="what-are-service-bus-queues"></a>Was sind Service Bus-Warteschlangen?
Service Bus-Warteschlangen unterstützen ein Kommunikationsmodell namens **Brokermessaging** . Bei der Verwendung von Warteschlangen kommunizieren die Komponenten einer verteilten Anwendung nicht direkt miteinander, sondern tauschen Nachrichten über eine Warteschlange aus, die als Zwischenstufe (Broker) fungiert. Ein Nachrichtenproducer (Absender) übergibt eine Nachricht an die Warteschlange und setzt seine Funktion fort. Ein Nachrichtenconsumer (Empfänger) ruft die Nachricht asynchron aus der Warteschlange ab und verarbeitet sie. Der Producer muss nicht auf eine Antwort vom Consumer warten, um seine Funktion fortzusetzen und weitere Nachrichten zu schicken. Warteschlangen liefern die Nachrichten im **First In, First Out (FIFO)** -Verfahren an einen oder mehrere Consumer. Die Nachrichten werden also normalerweise in der gleichen Reihenfolge von den Consumern empfangen und verarbeitet, wie sie in die Warteschlange übergeben wurden, und jede Nachricht wird nur von einem Consumer verarbeitet.

![Konzepte für Warteschlangen](./media/howto-service-bus-queues/sb-queues-08.png)

Service Bus-Warteschlangen sind eine Allzwecktechnologie für viele unterschiedliche Szenarien:

* Kommunikation zwischen Web- und Workerrollen in Azure-Anwendungen mit mehreren Ebenen
* Kommunikation zwischen lokalen Apps und von Azure gehosteten Apps in einer Hybridlösung
* Kommunikation zwischen Komponenten einer verteilten lokalen Anwendung, die in verschiedenen Organisationen oder Abteilungen einer Organisation laufen

Warteschlangen unterstützen Sie bei der einfacheren Skalierung Ihrer Anwendungen und führen zu einer robusteren Architektur.


