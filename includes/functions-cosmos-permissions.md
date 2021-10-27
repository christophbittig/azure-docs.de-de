---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: d95d50d6e5f69922856e6a3045dfccdf91ddbf1a
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129994321"
---
Sie müssen eine Rollenzuweisung erstellen, die zur Laufzeit Zugriff auf Ihr Datenbankkonto ermöglicht. Verwaltungsrollen wie [Besitzer](../articles/role-based-access-control/built-in-roles.md#owner) sind nicht ausreichend. Die folgende Tabelle zeigt integrierte Rollen, die für den normalen Betrieb mit der Cosmos DB-Erweiterung empfohlen werden. Ihre Anwendung erfordert möglicherweise zusätzliche Berechtigungen basierend auf dem von Ihnen geschriebenen Code.

| Bindungstyp   | Integrierte Beispielrollen                |
|----------------|---------------------------------------|
| Trigger        | [Integrierter Cosmos DB-Datenleser]      |
| Ausgabebindung | [Integrierter Mitwirkender an Cosmos DB-Daten] |


[Integrierter Cosmos DB-Datenleser]: ../articles/cosmos-db/how-to-setup-rbac.md#built-in-role-definitions
[Integrierter Mitwirkender an Cosmos DB-Daten]: ../articles/cosmos-db/how-to-setup-rbac.md#built-in-role-definitions