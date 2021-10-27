---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: b4ce7e128304aa1befd7029ed2a692b1d7f391f3
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130137364"
---
Sie müssen eine Rollenzuweisung erstellen, die zur Laufzeit Zugriff auf Ihre Themen und Warteschlangen ermöglicht. Der Umfang der Rollenzuweisung muss für einen Service Bus-Namespace gelten und darf nicht auf eine bestimmte Warteschlange oder ein Thema beschränkt sein. Verwaltungsrollen wie [Besitzer](../articles/role-based-access-control/built-in-roles.md#owner) sind nicht ausreichend. Die folgende Tabelle zeigt integrierte Rollen, die für die Service Bus-Erweiterung im Normalbetrieb empfohlen werden. Ihre Anwendung erfordert möglicherweise zusätzliche Berechtigungen basierend auf dem von Ihnen geschriebenen Code.

| Bindungstyp   | Integrierte Beispielrollen                                            |
|----------------|-------------------------------------------------------------------|
| Trigger        | [Azure Service Bus-Datenempfänger], [Azure Service Bus-Datenbesitzer] |
| Ausgabebindung | [Azure Service Bus-Datensender]                                   |

[Azure Service Bus-Datenempfänger]: ../articles/role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver
[Azure Service Bus-Datensender]: ../articles/role-based-access-control/built-in-roles.md#azure-service-bus-data-sender
[Azure Service Bus-Datenbesitzer]: ../articles/role-based-access-control/built-in-roles.md#azure-service-bus-data-owner
