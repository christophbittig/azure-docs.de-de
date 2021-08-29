---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 08/16/2021
ms.author: alkohli
ms.openlocfilehash: e73b49b92894d2871e474f42ad4ba31a7161677b
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/16/2021
ms.locfileid: "122264955"
---
- Container, Freigaben und Ordner:
  - Kopieren Sie Dateien nicht direkt in eine der vorab erstellten Freigaben. Sie müssen zunächst einen Ordner unter der Freigabe erstellen und dann Dateien in diesen Ordner kopieren.
  - Ein Ordner unter *StorageAccount_BlockBlob* und *StorageAccount_PageBlob* ist ein Container. Container werden beispielsweise als *StorageAccount_BlockBlob/container* und *StorageAccount_PageBlob/container* erstellt.
  - Jeder Ordner, der direkt unter *StorageAccount_AzureFile* erstellt wurde, wird in eine Azure-Dateifreigabe umgewandelt.
  - Azure Blob Storage unterstützt keine Verzeichnisse. Wenn Sie einen Ordner unter dem Ordner *StorageAccount_BlockBlob* erstellen, werden im Blobnamen virtuelle Ordner erstellt. Für Azure Files wird die tatsächliche Verzeichnisstruktur beibehalten.
- Zusammenführen von Ordnerinhalten:
  - Jede Datei, die in die Freigaben *StorageAccount_BlockBlob* und *StorageAccount_PageBlob* geschrieben wird, wird als Blockblob bzw. Seitenblob hochgeladen.
  - Wenn ein Ordner denselben Namen wie ein vorhandener Container hat, wird der Inhalt des Ordners mit dem Inhalt des Containers zusammengeführt. Dateien oder Blobs, die sich noch nicht in der Cloud befinden, werden dem Container hinzugefügt. Wenn eine Datei oder ein Blob denselben Namen wie eine Datei oder ein Blob hat, die bzw. das sich bereits im Container befindet, wird die vorhandene Datei oder das Blob überschrieben.
  - Eine unter den Ordnern *StorageAccount_BlockBlob* und *StorageAccount_PageBlob* erstellte leere Verzeichnishierarchie (ohne jegliche Dateien) wird nicht hochgeladen.
- Uploadverwaltung: 
  - Zur Leistungsverbesserung während Datenuploads empfehlen wir, dass Sie [große Dateifreigaben für das Speicherkonto aktivieren und die Freigabekapazität auf 100 TiB erhöhen](../articles/storage/files/storage-how-to-create-file-share.md#enable-large-files-shares-on-an-existing-account). Große Dateifreigaben werden nur bei Speicherkonten mit lokal redundantem Speicher (LRS) unterstützt.
  - Wenn beim Hochladen von Daten in Azure Fehler auftreten, wird im Zielspeicherkonto ein Fehlerprotokoll erstellt. Der Pfad zu diesem Fehlerprotokoll ist verfügbar, sobald das Hochladen abgeschlossen ist, und Sie können das Protokoll überprüfen, um Korrekturmaßnahmen zu ergreifen. Löschen Sie keine Daten aus der Quelle, ohne die hochgeladenen Daten zu überprüfen.
  - Dateimetadaten und NTFS-Berechtigungen können beim Hochladen der Daten in Azure Files beibehalten werden. Siehe hierzu die Anleitung unter [Beibehalten von ACLs, Attributen und Zeitstempeln für Dateien mit Azure Data Box](../articles/databox/data-box-file-acls-preservation.md).
  - Die Hierarchie der Dateien wird beim Hochladen in die Cloud beibehalten. Beispiel: Sie haben eine Datei in diesen Pfad kopiert: `<container folder>\A\B\C.txt`. Dann wird diese Datei wird in den gleichen virtuellen Pfad in der Cloud hochgeladen.
  
