---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2021
ms.author: mahender
ms.openlocfilehash: 15f68c6339135139f6f6fcbf335306f6edf2a3c6
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130257124"
---
Sie müssen eine Rollenzuweisung erstellen, die zur Laufzeit Zugriff auf Ihr Datenbankkonto ermöglicht. Verwaltungsrollen wie [Besitzer](../articles/role-based-access-control/built-in-roles.md#owner) sind nicht ausreichend. Die folgende Tabelle zeigt integrierte Rollen, die für den normalen Betrieb mit der Cosmos DB-Erweiterung empfohlen werden. Ihre Anwendung erfordert möglicherweise zusätzliche Berechtigungen basierend auf dem von Ihnen geschriebenen Code.

| Bindungstyp   | Integrierte Beispielrollen                |
|----------------|---------------------------------------|
| Trigger        | [Integrierter Mitwirkender an Cosmos DB-Daten] |
| Eingabebindung  | [Integrierter Cosmos DB-Datenleser]      |
| Ausgabebindung | [Integrierter Mitwirkender an Cosmos DB-Daten] |


[Integrierter Cosmos DB-Datenleser]: ../articles/cosmos-db/how-to-setup-rbac.md#built-in-role-definitions
[Integrierter Mitwirkender an Cosmos DB-Daten]: ../articles/cosmos-db/how-to-setup-rbac.md#built-in-role-definitions