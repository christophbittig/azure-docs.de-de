---
author: mattchenderson
ms.service: app-service-web
ms.topic: include
ms.date: 06/11/2021
ms.author: mahender
ms.openlocfilehash: 9b05a4d16cbf6d3b9677c894bffb6e47018b5cff
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122264364"
---
Azure Blob Storage kann für die [Autorisierung von Anforderungen mit Azure AD](../articles/storage/blobs/authorize-access-azure-active-directory.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) konfiguriert werden. Das bedeutet, dass Sie keinen SAS-Schlüssel mit Ablauf generieren müssen, sondern stattdessen die [verwaltete Identität](../articles/app-service/overview-managed-identity.md) der Anwendung verwenden können. Standardmäßig wird die systemseitig zugewiesene Identität der App verwendet. Wenn Sie eine vom Benutzer zugewiesene Identität angeben möchten, können Sie die App-Einstellung `WEBSITE_RUN_FROM_PACKAGE_BLOB_MI_RESOURCE_ID` auf die Ressourcen-ID dieser Identität festlegen. Für die Einstellung wird zwar auch „SystemAssigned“ (Systemseitig zugewiesen) als Wert akzeptiert, dies hat jedoch das gleiche Ergebnis wie das Weglassen der Einstellung.

So ermöglichen Sie das Abrufen des Pakets unter Verwendung der Identität:

1. Vergewissern Sie sich, dass das Blob [für privaten Zugriff konfiguriert](../articles/storage/blobs/anonymous-read-access-configure.md#set-the-public-access-level-for-a-container) ist.

1. Erteilen Sie der Identität die Rolle [Storage-Blobdatenleser](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader) mit dem Bereich für das Paketblob. Ausführliche Informationen zum Erstellen der Rollenzuweisung finden Sie unter [Zuweisen einer Azure-Rolle für den Zugriff auf Blobdaten](../articles/storage/blobs/assign-azure-role-data-access.md).

1. Legen Sie die Anwendungseinstellung `WEBSITE_RUN_FROM_PACKAGE` auf die Blob-URL des Pakets fest. Diese hat wahrscheinlich das Format „https://{Speicherkontoname}.blob.core.windows.net/{Containername}/{Pfad zum Paket}“ oder ein ähnliches Format.