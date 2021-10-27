---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: 32184e2b836aa881dc3175bb0790fc9cbbca1df7
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129996430"
---
Sie müssen eine Rollenzuweisung erstellen, die zur Laufzeit Zugriff auf Ihren Blobcontainer ermöglicht. Verwaltungsrollen wie [Besitzer](../articles/role-based-access-control/built-in-roles.md#owner) sind nicht ausreichend. Die folgende Tabelle zeigt integrierte Rollen, die für den normalen Betrieb mit der Blob Storage-Erweiterung empfohlen werden. Ihre Anwendung erfordert möglicherweise zusätzliche Berechtigungen basierend auf dem von Ihnen geschriebenen Code.

| Bindungstyp   | Integrierte Beispielrollen                |
|----------------|---------------------------------------|
| Trigger        | [Besitzer von Speicherblobdaten]<sup>1</sup> |
| Eingabebindung  | [Leser von Speicherblobdaten]            |
| Ausgabebindung | [Besitzer von Speicherblobdaten]             |

<sup>1</sup> In einigen Konfigurationen erfordert ein Blobtrigger möglicherweise zusätzlich die Rolle [Mitwirkender an Storage-Warteschlangendaten](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor).

[Leser von Speicherblobdaten]: ../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader
[Besitzer von Speicherblobdaten]: ../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner
