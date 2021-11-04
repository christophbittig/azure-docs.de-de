---
title: Rehydrierung von Klecksen aus der Archivebene
description: Während ein Blob sich auf der Archivzugriffsebene befindet, wird es als offline betrachtet und kann nicht gelesen oder geändert werden. Wenn Daten in einem archivierten Blob gelesen oder geändert werden sollen, muss das Blob zunächst auf einer Onlineebene (heiße oder kalte Ebene) aktiviert werden.
services: storage
author: tamram
ms.author: tamram
ms.date: 09/29/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: fryu
ms.openlocfilehash: 195238c6ef4191266a0f4b5dd481fbf24b70528f
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129271819"
---
# <a name="blob-rehydration-from-the-archive-tier"></a>Rehydrierung von Klecksen aus der Archivebene

Während ein Blob sich auf der Archivzugriffsebene befindet, wird es als offline betrachtet und kann nicht gelesen oder geändert werden. Wenn Daten in einem archivierten Blob gelesen oder geändert werden sollen, muss das Blob zunächst auf einer Onlineebene (heiße oder kalte Ebene) aktiviert werden. Es gibt zwei Möglichkeiten, ein auf der Archivebene gespeichertes Blob zu aktivieren:

- [Kopieren eines archivierten Blobs auf eine Onlineebene](#copy-an-archived-blob-to-an-online-tier): Sie können ein archiviertes Blob mithilfe des Vorgangs [Copy Blob](/rest/api/storageservices/copy-blob) oder [Copy Blob from URL](/rest/api/storageservices/copy-blob-from-url) in ein neues Blob auf der heißen oder kalten Ebene kopieren. Microsoft empfiehlt diese Option für die meisten Szenarios.

- [Ändern der Zugriffsebene eines Blobs in eine Onlineebene](#change-a-blobs-access-tier-to-an-online-tier): Sie können ein archiviertes Blob auf der heißen oder kalten Ebene aktivieren, indem Sie die Ebene mithilfe des Vorgangs [Blobebene festlegen](/rest/api/storageservices/set-blob-tier) ändern.

Die Aktivierung eines Blobs aus der Archivebene kann mehrere Stunden dauern. Microsoft empfiehlt, größere Blobs zu aktivieren, um eine optimale Leistung zu erzielen. Es kann länger dauern, wenn mehrere kleine Blobs gleichzeitig aktiviert werden.

Sie können [Azure Event Grid](../../event-grid/overview.md) so konfigurieren, dass ein Ereignis ausgelöst wird, wenn ein Blob aus der Archivebene in einer Onlineebene aktiviert wird, und dass das Ereignis an einen Ereignishandler gesendet wird. Weitere Informationen hierzu finden Sie unter [Behandeln eines Ereignisses bei der Blobaktivierung](#handle-an-event-on-blob-rehydration).

Weitere Informationen über Zugriffsebenen in Azure Storage finden Sie unter [Heiße, kühle und Archiv-Zugriffsebenen für Blobdaten](access-tiers-overview.md).

## <a name="rehydration-priority"></a>Aktivierungspriorität

Wenn Sie ein Blob aktivieren, können Sie die Priorität für den Aktivierungsvorgang über den optionalen *x-ms-rehydrate-priority*-Header bei den Vorgängen [Set Blob Tier](/rest/api/storageservices/set-blob-tier) oder [Copy Blob](/rest/api/storageservices/copy-blob)/[Copy Blob from URL](/rest/api/storageservices/copy-blob-from-url) festlegen. Optionen für Aktivierungspriorität:

- **Standardpriorität**: Die Aktivierungsanforderung gemäß der Eingangsreihenfolge verarbeitet und kann bis zu 15 Stunden dauern.
- **Hohe Priorität**: Die Aktivierungsanforderung hat Vorrang vor Anforderungen mit Standardpriorität und kann bei Objekten mit einer Größe von weniger als 10 GB in weniger als einer Stunde verarbeitet werden.

Wenn Sie die Aktivierungspriorität während des Aktivierungsvorgangs prüfen möchten, rufen Sie [Get Blob Properties](/rest/api/storageservices/get-blob-properties) auf, sodass der Wert des `x-ms-rehydrate-priority`-Headers zurückgegeben wird. Für die Eigenschaft für die Aktivierungspriorität wird *Standard* oder *Hoch* zurückgegeben.

Die Standardpriorität ist die Standardaktivierungsoption. Die Aktivierung mit hoher Priorität ist eine schnellere Option, kostet aber auch mehr als die Aktivierung mit Standardpriorität. Die Aktivierung mit hoher Priorität kann je nach Blobgröße und aktueller Auslastung länger als eine Stunde dauern. Microsoft empfiehlt, die Aktivierung mit hoher Priorität nur in Notfallsituationen zu verwenden, in denen Daten dringend wiederhergestellt werden müssen.

Weitere Informationen zu den Preisunterschieden zwischen Aktivierungsanforderungen mit Standardpriorität und hoher Priorität finden Sie unter [Preise für Azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Kopieren eines archivierten Blobs auf eine Onlineebene

Die erste Möglichkeit, ein Blob aus der Archivebene in eine Onlineebene zu verschieben, besteht darin, das archivierte Blob in ein neues Zielblob, also die heiße oder kalte Ebene, zu kopieren. Zum Kopieren des Blobs kann der Vorgang [Copy Blob](/rest/api/storageservices/copy-blob) oder [Copy Blob from URL](/rest/api/storageservices/copy-blob-from-url) verwendet werden. Wenn ein archiviertes Blob in ein neues Blob auf der Onlineebene kopiert wird, bleibt das Quellblob auf der Archivebene unverändert.

Das archivierte Blob muss in ein neues Blob mit einem anderen Namen oder in einen anderen Container kopiert werden. Das Quellblob kann nicht durch Kopieren in dasselbe Blob überschrieben werden.

Microsoft empfiehlt in den meisten Fällen, in denen ein Blob aus der Archivebene in eine Onlineebene verschoben werden muss, aus folgenden Gründen einen Kopiervorgang durchzuführen:

- Durch einen Kopiervorgang wird die Gebühr für vorzeitiges Löschen vermieden, die erhoben wird, wenn die Ebene für das Blob aus der Archivebene vor Ablauf der vorgesehenen Frist von 180 Tagen gewechselt wird. Weitere Informationen finden Sie unter [Zugriffsebene „Archiv“](access-tiers-overview.md#archive-access-tier).
- Wenn für das Speicherkonto eine Lebenszyklusverwaltungsrichtlinie gilt, kann die Aktivierung eines Blobs mit [Set Blob Tier](/rest/api/storageservices/set-blob-tier) dazu führen, dass das Blob aufgrund der Lebenszyklusrichtlinie nach der Aktivierung auf die Archivebene zurück verschoben wird, weil der Zeitpunkt der letzten Änderung nach dem für die Richtlinie festgelegten Schwellenwert liegt. Bei einem Kopiervorgang bleibt das Quellblob auf der Archivebene und es wird ein neues Blob mit einem anderen Namen und einem neuen Wert für den Zeitpunkt der letzten Änderung erstellt, sodass keine Gefahr besteht, dass das aktivierte Blob aufgrund der Lebenszyklusrichtlinie auf die Archivebene zurück verschoben wird.

Das Kopieren eines Blobs aus dem Archiv kann je nach ausgewählter Aktivierungspriorität mehrere Stunden dauern. Im Hintergrund wird beim Vorgang „Copy Blob“ das archivierte Quellblob gelesen und so auf der ausgewählten Zielebene ein neues Onlineblob erstellt. Das neue Blob wird möglicherweise bereits vor Abschluss der Aktivierung in der Liste der Blobs im übergeordneten Container angezeigt, wobei die Ebene des Blobs jedoch auf Archiv festgelegt ist. Die Daten sind erst verfügbar, wenn der Lesevorgang aus dem Quellblob in die Archivebene abgeschlossen ist und der Inhalt des Blobs in das Zielblob auf einer Onlineebene geschrieben wurde. Beim neuen Blob handelt es sich um eine unabhängige Kopie, sodass es keine Auswirkungen auf das Quellblob auf der Archivebene hat, wenn das neue Blob geändert oder gelöscht wird.

Informationen zum Aktivieren eines Blobs durch Kopieren in eine Onlineebene finden Sie unter [Aktivieren eines Blobs mit einem Kopiervorgang](archive-rehydrate-to-online-tier.md#rehydrate-a-blob-with-a-copy-operation).

> [!IMPORTANT]
> Löschen Sie das Quellblob erst, nachdem die Aktivierung erfolgreich durchgeführt wurde. Wenn das Quellblob gelöscht wird, kann der Kopiervorgang für das Zielblob möglicherweise nicht abgeschlossen werden. Sie können das Ereignis behandeln, das nach Abschluss des Kopiervorgangs ausgelöst wird, um zu ermitteln, wann das Quellblob bedenkenlos gelöscht werden kann. Weitere Informationen hierzu finden Sie unter [Behandeln eines Ereignisses bei der Blobaktivierung](#handle-an-event-on-blob-rehydration).

Das Kopieren eines archivierten Blobs auf eine Onlinezielebene wird nur innerhalb eines Speicherkontos unterstützt. Ein archiviertes Blob kann nicht in ein Zielblob kopiert werden, das sich auch auf der Archivebene befindet.

In der folgenden Tabelle ist das Verhalten eines Blobkopiervorgangs abhängig von den Ebenen des Quell- und des Zielblobs dargestellt.

|  | **Quelle: Heiße Ebene** | **Quelle: Kalte Ebene** | **Quelle: Archivebene** |
|--|--|--|--|
| **Ziel: Heiße Ebene** | Unterstützt | Unterstützt | Im selben Konto unterstützt Erfordert eine Blobaktivierung |
| **Ziel: Kalte Ebene** | Unterstützt | Unterstützt | Im selben Konto unterstützt Erfordert eine Blobaktivierung |
| **Ziel: Archivebene** | Unterstützt | Unterstützt | Nicht unterstützt |

## <a name="change-a-blobs-access-tier-to-an-online-tier"></a>Ändern der Zugriffsebene eines Blobs in eine Onlineebene

Die zweite Möglichkeit, ein Blob aus der Archivebene in einer Onlineebene zu aktivieren, besteht darin, die Ebene des Blobs durch den Aufruf von [Set Blob Tier](/rest/api/storageservices/set-blob-tier) zu wechseln. Mit diesem Vorgang kann die Ebene des archivierten Blobs in heiß oder kalt geändert werden.

Sobald eine [Set Blob Tier](/rest/api/storageservices/set-blob-tier)-Anforderung initiiert wurde, kann sie nicht mehr abgebrochen werden. Während des Aktivierungsvorgangs wird die Zugriffsebene des Blobs weiterhin als archiviert angezeigt, bis der Aktivierungsprozess abgeschlossen ist. Nach Abschluss des Aktivierungsvorgangs wird die Zugriffsebeneneigenschaft des Blobs aktualisiert, sodass die neue Ebene angezeigt wird.

Informationen zum Aktivieren eines Blobs durch Ändern seiner Ebene in eine Onlineebene finden Sie unter [Aktivieren eines Blobs durch Ändern seiner Ebene](archive-rehydrate-to-online-tier.md#rehydrate-a-blob-by-changing-its-tier).

> [!CAUTION]
> Wenn Sie die Ebene eines Blobs ändern, hat das keine Auswirkungen auf den Zeitpunkt der letzten Änderung. Wenn für das Speicherkonto eine [Lebenszyklusverwaltungsrichtlinie](./lifecycle-management-overview.md) gilt, kann die Aktivierung eines Blobs mit **Set Blob Tier** dazu führen, dass das Blob aufgrund der Lebenszyklusrichtlinie nach der Aktivierung auf die Archivebene zurück verschoben wird, weil der Zeitpunkt der letzten Änderung nach dem für die Richtlinie festgelegten Schwellenwert liegt.
>
> Wenn Sie das vermeiden möchten, aktivieren Sie das archivierte Blob, indem Sie es stattdessen wie in der Beschreibung im Abschnitt [Kopieren eines archivierten Blobs auf eine Onlineebene](#copy-an-archived-blob-to-an-online-tier) kopieren. Bei einem Kopiervorgang wird eine neue Instanz des Blobs mit einem aktualisierten Zeitpunkt der letzten Änderung erstellt, sodass die Lebenszyklusverwaltungsrichtlinie nicht ausgelöst wird.

## <a name="check-the-status-of-a-blob-rehydration-operation"></a>Überprüfen des Status eines Blob-Aktivierungsvorgangs

Während des Blog-Aktivierungsvorgangs können Sie den Vorgang [Get Blob Properties](/rest/api/storageservices/get-blob-properties) aufrufen, um den Status zu überprüfen. Informationen zum Prüfen des Status eines Aktivierungsvorgangs finden Sie unter [Überprüfen des Status eines Aktivierungsvorgangs](archive-rehydrate-to-online-tier.md#check-the-status-of-a-rehydration-operation).

## <a name="handle-an-event-on-blob-rehydration"></a>Behandeln eines Ereignisses bei der Blobaktivierung

Die Aktivierung eines archivierten Blobs kann bis zu 15 Stunden dauern, und das wiederholte Aufrufen von **Get Blob Properties**, um festzustellen, ob die Aktivierung abgeschlossen ist, ist ineffizient. Die Verwendung von [Azure Event Grid](../../event-grid/overview.md) zur Erfassung des Ereignisses, das bei Abschluss der Aktivierung ausgelöst wird, bietet eine bessere Leistungs- und Kostenoptimierung.

Je nachdem, welcher Vorgang zum Aktivieren des Blobs verwendet wurde, wird bei der Blobaktivierung durch Azure Event Grid eines der beiden Ereignisse ausgelöst:

- Das Ereignis **Microsoft.Storage.BlobCreated** wird ausgelöst, wenn ein Blob erstellt wird. Im Kontext einer Blobaktivierung wird dieses Ereignis ausgelöst, wenn bei einem [Copy Blob](/rest/api/storageservices/copy-blob)- oder [Copy Blob from URL](/rest/api/storageservices/copy-blob-from-url)-Vorgang in der heißen oder kalten Ebene ein neues Zielblob erstellt wird und die Daten des Blobs aus der Archivebene vollständig aktiviert werden.
- Das Ereignis **Microsoft.Storage.BlobTierChanged** wird ausgelöst, wenn sich die Ebene des Blobs ändert. Im Kontext einer Blobaktivierung wird dieses Ereignis ausgelöst, wenn bei einem [Set Blob Tier](/rest/api/storageservices/set-blob-tier)-Vorgang die Ebene eines archivierten Blobs erfolgreich in die heiße oder kalte Ebene geändert wird.

Informationen zum Aufzeichnen eines Ereignisses bei der Aktivierung und zum Senden des Ereignisses an einen Ereignishandler einer Azure-Funktion finden Sie unter [Ausführen einer Azure-Funktion als Reaktion auf ein Blob-Aktivierungsereignis](archive-rehydrate-handle-event.md).

Weitere Informationen zur Behandlung von Ereignissen in Blob Storage finden Sie unter [Reagieren auf Azure Blob Storage-Ereignisse](storage-blob-event-overview.md) und unter [Azure Blob Storage als Event Grid-Quelle](../../event-grid/event-schema-blob-storage.md).

## <a name="pricing-and-billing"></a>Preise und Abrechnung

Ein Aktivierungsvorgang mit [Set Blob Tier](/rest/api/storageservices/set-blob-tier) wird pro Datenlesetransaktion und je nach Datenabrufgröße berechnet. Eine Aktivierung mit hoher Priorität verursachen höhere Vorgangs- und Datenabrufkosten als Vorgänge mit Standardpriorität. Aktivierungsvorgänge mit hoher Priorität werden auf Ihrer Rechnung als separater Posten ausgewiesen. Wenn eine Anforderung zum Abruf eines einige Gigabytes großen archivierten Blobs mit hoher Priorität über fünf Stunden dauert, werden Ihnen nicht die Gebühren für einen Abruf mit hoher Priorität berechnet. Stattdessen werden die Standardgebühren für Abrufvorgänge berechnet.

Das Kopieren eines archivierten Blobs in eine Onlineebene mit [Copy Blob](/rest/api/storageservices/copy-blob) oder [Copy Blob from URL](/rest/api/storageservices/copy-blob-from-url) wird pro Datenlesetransaktion und je nach Datenabrufgröße berechnet. Das Erstellen des Zielblobs in einer Onlineebene wird pro Datenschreibtransaktion berechnet. Gebühren für frühes Löschen fallen beim Kopieren in ein Onlineblob nicht an, weil das Quellblob auf der Archivzugriffsebene unverändert bleibt. Für einen Abruf mit hoher Priorität werden Gebühren berechnet, sofern dieser ausgewählt ist.

Blobs auf Archivzugriffsebene müssen mindestens 180 Tage lang gespeichert werden. Für das Löschen oder Ändern der Ebene eines archivierten Blobs vor Ablauf der 180-Tage-Frist wird eine Gebühr für vorzeitiges Löschen berechnet. Weitere Informationen finden Sie unter [Zugriffsebene „Archiv“](access-tiers-overview.md#archive-access-tier).

Weitere Informationen zu den Preisen für Blockblobs und Datenaktivierung finden Sie unter [Preise für Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/). Weitere Informationen zu den Kosten für ausgehende Datenübertragungen finden Sie unter [Datenübertragungen – Preisdetails](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="see-also"></a>Weitere Informationen

- [Hot-, Cool- und Archivzugriffsebenen für Blobdaten](access-tiers-overview.md).
- [Aktivieren eines archivierten Blobs auf einer Onlineebene](archive-rehydrate-to-online-tier.md)
- [Ausführen einer Azure-Funktion als Reaktion auf ein Blob-Aktivierungsereignis](archive-rehydrate-handle-event.md)
- [Reacting to Blob storage events (preview)](storage-blob-event-overview.md) (Reagieren auf Blob Storage-Ereignisse (Vorschauversion))
