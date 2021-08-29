---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 08/02/2021
ms.author: alkohli
ms.openlocfilehash: 1f026d0a8d038e532b9b0ec070a4849ebc8e8530
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121802545"
---
Dies sind die Grenzwerte für die Größe der Daten, die in ein Speicherkonto kopiert werden. Stellen Sie sicher, dass die von Ihnen hochgeladenen Daten diesen Grenzwerten entsprechen. Aktuelle Informationen zu diesen Grenzwerten finden Sie unter [Skalierbarkeits- und Leistungsziele für Blob Storage](../articles/storage/blobs/scalability-targets.md) und [Skalierbarkeits- und Leistungsziele für Azure Files](../articles/storage/files/storage-files-scale-targets.md). 

| Größe der in das Azure-Speicherkonto kopierten Daten                      | Standardlimit          |
|---------------------------------------------------------------------|------------------------|
| Blockblob und Seitenblob                                            | Der maximale Grenzwert ist identisch mit dem [Speicherlimit, das für das Azure-Abonnement definiert ist](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits), und enthält Daten aus allen Quellen(einschließlich Data Box).   |
| Azure Files                                                          | <ul><li>Data Box unterstützt große Dateifreigaben (100 TiB), wenn dies vor Erstellung des Data Box-Auftrags aktiviert wurde.<ul><li>Wenn große Dateifreigaben vor der Auftragserstellung nicht aktiviert wurden, werden Dateifreigaben von maximal 5 TiB unterstützt.</li><li>Zur Leistungsverbesserung während Datenuploads empfehlen wir, dass Sie [große Dateifreigaben für das Speicherkonto aktivieren und die Freigabekapazität auf 100 TiB erhöhen](../articles/storage/files/storage-how-to-create-file-share.md#enable-large-files-shares-on-an-existing-account). Große Dateifreigaben werden nur bei Speicherkonten mit lokal redundantem Speicher (LRS) unterstützt.</li></ul><li>Data Box unterstützt Azure Premium-Dateifreigaben, die insgesamt 100 TiB für alle Freigaben im Speicherkonto zulassen.<ul><li>Die maximal nutzbare Kapazität ist aufgrund des von Kopier- und Überwachungsprotokollen verwendeten Speicherplatzes etwas geringer. Mindestens jeweils 100 GiB wird für das Kopier- und das Überwachungsprotokoll reserviert. Weitere Informationen finden Sie unter [Überwachungsprotokolle für Azure Data Box und Azure Data Box Heavy](../articles/databox/data-box-audit-logs.md).</li><li>Alle Ordner unter *StorageAccount_AzFile* müssen diesen Grenzwert einhalten. Weitere Informationen finden Sie unter [Erstellen einer Azure-Dateifreigabe](../articles/storage/files/storage-how-to-create-file-share.md).</li></ul></li></ul> |
