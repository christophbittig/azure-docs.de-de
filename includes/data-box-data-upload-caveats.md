---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 11/10/2021
ms.author: alkohli
ms.openlocfilehash: d02eb129dd66234cf7c8522ca8204fb0026da092
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132353887"
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
- Wenn Sie sowohl das SMB- als auch das NFS-Protokoll für Datenkopien verwenden, wird Folgendes empfohlen:
  - Verwenden Sie verschiedene Speicherkonten für SMB und NFS.
  - Kopieren Sie nicht dieselben Daten mit SMB und NFS in dasselbe Endziel in Azure. In diesen Fällen lässt sich das endgültige Ergebnis nicht im Vorhinein bestimmen.
  - Zwar kann das parallele Kopieren über SMB und NFS funktionieren, doch wird davon abgeraten, da es anfällig für menschliche Fehler ist. Warten Sie, bis die SMB-Datenkopie abgeschlossen ist, bevor Sie eine NFS-Datenkopie starten.
- Uploadverwaltung: 
  - Zur Leistungsverbesserung während Datenuploads empfehlen wir, dass Sie [große Dateifreigaben für das Speicherkonto aktivieren und die Freigabekapazität auf 100 TiB erhöhen](../articles/storage/files/storage-how-to-create-file-share.md#enable-large-files-shares-on-an-existing-account). Große Dateifreigaben werden nur bei Speicherkonten mit lokal redundantem Speicher (LRS) unterstützt.
  - Wenn beim Hochladen von Daten in Azure Fehler auftreten, wird im Zielspeicherkonto ein Fehlerprotokoll erstellt. Der Pfad zu diesem Fehlerprotokoll ist verfügbar, sobald das Hochladen abgeschlossen ist, und Sie können das Protokoll überprüfen, um Korrekturmaßnahmen zu ergreifen. Löschen Sie keine Daten aus der Quelle, ohne die hochgeladenen Daten zu überprüfen.
  - Dateimetadaten und NTFS-Berechtigungen können beim Hochladen der Daten in Azure Files beibehalten werden. Siehe hierzu die Anleitung unter [Beibehalten von ACLs, Attributen und Zeitstempeln für Dateien mit Azure Data Box](../articles/databox/data-box-file-acls-preservation.md).
  - Die Hierarchie der Dateien wird beim Hochladen in die Cloud sowohl für Blobs als auch für Azure Files beibehalten. Beispiel: Sie haben eine Datei in diesen Pfad kopiert: `<container folder>\A\B\C.txt`. Dann wird diese Datei wird in den gleichen virtuellen Pfad in der Cloud hochgeladen.
  
