---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: e8b274cd3d9398694661b06a5a1c9e13e42c6c89
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130137362"
---
Sie müssen eine Rollenzuweisung erstellen, die den Zugriff auf das Speicherkonto für "AzureWebJobsStorage" zur Laufzeit ermöglicht. Verwaltungsrollen wie [Owner](../articles/role-based-access-control/built-in-roles.md#owner) sind nicht ausreichend. Die Rolle [Storage Blob Data Owner] deckt die grundlegenden Anforderungen von Functions Host Storage ab - die Laufzeit benötigt sowohl Lese- als auch Schreibzugriff auf Blobs und die Möglichkeit, Container zu erstellen. Es gibt einige Situationen, wie z.B. die Verwendung des Blob-Triggers, in denen auch [Storage Queue Data Contributor] benötigt werden kann. Möglicherweise benötigen Sie zusätzliche Berechtigungen, wenn Sie „AzureWebJobsStorage“ für andere Zwecke verwenden.

[Besitzer von Speicherblobdaten]: ../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner
[Mitwirkender an Storage-Warteschlangendaten]: ../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor