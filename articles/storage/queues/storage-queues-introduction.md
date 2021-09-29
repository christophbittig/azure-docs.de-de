---
title: Einführung in Azure Queue Storage – Azure Storage
description: Hier finden Sie eine Einführung in Azure Queue Storage, einem Dienst zum Speichern sehr vieler Nachrichten. Ein Queue Storage-Dienst enthält ein URL-Format, ein Speicherkonto, eine Warteschlange und eine Nachricht.
author: normesta
ms.author: normesta
ms.reviewer: dineshm
ms.date: 03/18/2020
ms.topic: overview
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 1da3781b3dac4b79d8c8dc49decc669bfa7efde6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128551734"
---
# <a name="what-is-azure-queue-storage"></a>Was ist Azure Queue Storage?

Azure Queue Storage ist ein Dienst für die Speicherung großer Nachrichtenmengen. Sie können überall auf der Welt über authentifizierte Aufrufe mithilfe von HTTP oder HTTPS auf Nachrichten zugreifen. Eine Warteschlangennachricht kann bis zu 64 KB groß sein. Eine Warteschlange kann Millionen Nachrichten enthalten, bis die maximale Kapazität eines Speicherkontos erreicht ist. Warteschlangen werden häufig verwendet, um ein Arbeits-Backlog zur asynchronen Verarbeitung zu erstellen.

## <a name="queue-storage-concepts"></a>Queue Storage-Konzepte

Queue Storage enthält die folgenden Komponenten:

![Diagramm, das die Beziehung zwischen einem Speicherkonto, Warteschlangen und Nachrichten zeigt.](./media/storage-queues-introduction/queue1.png)

- **URL-Format**: Warteschlangen sind über das folgende URL-Format adressierbar:

  `https://<storage account>.queue.core.windows.net/<queue>`

  Mit der folgenden URL kann eine der Warteschlangen im Diagramm adressiert werden:

  `https://myaccount.queue.core.windows.net/images-to-download`

- **Speicherkonto:** Alle Zugriffe auf den Azure-Speicher erfolgen über ein Speicherkonto. Weitere Informationen zur Speicherkontokapazität finden Sie unter [Skalierbarkeits- und Leistungsziele für Standardspeicherkonten](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

- **Warteschlange:** Eine Warteschlange enthält einen Satz von Nachrichten. Der Warteschlangenname **muss** ausschließlich aus Kleinbuchstaben bestehen. Informationen zum Benennen von Warteschlangen finden Sie unter [Benennen von Warteschlangen und Metadaten](/rest/api/storageservices/naming-queues-and-metadata).

- **Nachricht:** Eine Nachricht in einem beliebigen Format und mit einer Größe von bis zu 64 KB. Vor Version 2017-07-29 beträgt die maximale Gültigkeitsdauer sieben Tage. Für Version 2017-07-29 oder höhere Versionen kann die maximale Gültigkeitsdauer eine beliebige positive Zahl sein. Mit -1 wird angegeben, dass die Nachricht nicht abläuft. Wird dieser Parameter ausgelassen, beträgt die Standardgültigkeitsdauer sieben Tage.

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen eines Speicherkontos](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Erste Schritte mit Queue Storage mit .NET](storage-dotnet-how-to-use-queues.md)
- [Erste Schritte mit Queue Storage mit Java](storage-java-how-to-use-queue-storage.md)
- [Erste Schritte mit Queue Storage mit Python](storage-python-how-to-use-queue-storage.md)
- [Erste Schritte mit Queue Storage mit Node.js](storage-nodejs-how-to-use-queues.md)
