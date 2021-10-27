---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: b0451d7a3f14002ec53cdb410144c6e69f1337a9
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130137363"
---
Sie müssen eine Rollenzuweisung erstellen, die zur Laufzeit Zugriff auf Ihren Event Hub ermöglicht. Der Bereich der Rollenzuweisung muss für einen Event Hubs-Namespace und nicht für den Event Hub selbst gelten. Verwaltungsrollen wie [Besitzer](../articles/role-based-access-control/built-in-roles.md#owner) sind nicht ausreichend. Die folgende Tabelle zeigt integrierte Rollen, die für die Event Hubs-Erweiterung im Normalbetrieb empfohlen werden. Ihre Anwendung erfordert möglicherweise zusätzliche Berechtigungen basierend auf dem von Ihnen geschriebenen Code.

| Bindungstyp   | Integrierte Beispielrollen                                          |
|----------------|-----------------------------------------------------------------|
| Trigger        | [Azure Event Hubs-Datenempfänger], [Azure Event Hubs-Datenbesitzer] |
| Ausgabebindung | [Azure Event Hubs-Datensender]                                  |

[Azure Event Hubs-Datenempfänger]: ../articles/role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver
[Azure Event Hubs-Datensender]: ../articles/role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender
[Azure Event Hubs-Datenbesitzer]: ../articles/role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner