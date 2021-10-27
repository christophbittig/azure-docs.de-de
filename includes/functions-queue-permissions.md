---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: e9897a9fb28f3ea9489e80b0dd4bb814c836b23b
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129992730"
---
Sie müssen eine Rollenzuweisung erstellen, die zur Laufzeit Zugriff auf Ihre Warteschlange ermöglicht. Verwaltungsrollen wie [Besitzer](../articles/role-based-access-control/built-in-roles.md#owner) sind nicht ausreichend. Die folgende Tabelle zeigt integrierte Rollen, die für den normalen Betrieb mit der Queue Storage-Erweiterung empfohlen werden. Ihre Anwendung erfordert möglicherweise zusätzliche Berechtigungen basierend auf dem von Ihnen geschriebenen Code.

| Bindungstyp   | Integrierte Beispielrollen                                                |
|----------------|-----------------------------------------------------------------------|
| Trigger        | [Storage-Warteschlangendatenleser], [Verarbeiter von Speicherwarteschlangen-Datennachrichten]   |
| Ausgabebindung | [Mitwirkender an Storage-Warteschlangendaten], [Absender der Speicherwarteschlangen-Datennachricht] |

[Storage-Warteschlangendatenleser]: ../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader
[Verarbeiter von Speicherwarteschlangen-Datennachrichten]: ../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor
[Absender der Speicherwarteschlangen-Datennachricht]: ../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender
[Mitwirkender an Storage-Warteschlangendaten]: ../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor
