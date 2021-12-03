---
title: Begrenzungen für Azure Data Box-Datenträger | Microsoft-Dokumentation
description: Beschreibt Systembegrenzungen und empfohlene Größen für den Microsoft Azure Data Box-Datenträger.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 09/28/2021
ms.author: alkohli
ms.openlocfilehash: ce8cc86727c92f8c0091abafa8f5d91ee1ab21ad
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347630"
---
# <a name="azure-data-box-disk-limits"></a>Begrenzungen für Azure Data Box Disk


Berücksichtigen Sie die folgenden Einschränkungen beim Bereitstellen und Betreiben Ihrer Microsoft Azure Data Box-Datenträgerlösung.

## <a name="data-box-service-limits"></a>Data Box-Diensteinschränkungen

 - Der Data Box-Dienst ist in den unter [Verfügbarkeit nach Region](data-box-disk-overview.md#region-availability) aufgeführten Regionen verfügbar.
 - Ein einzelnes Speicherkonto wird mit dem Data Box-Datenträger unterstützt.

## <a name="data-box-disk-performance"></a>Leistung des Data Box-Datenträgers

Beim Testen mit Datenträgern, die über USB 3.0 verbunden waren, betrug die Datenträgerleistung bis zu 430 MB/s. Die tatsächlichen Zahlen variieren abhängig von der Größe der verwendeten Datei. Bei kleineren Dateien ist die Leistung unter Umständen etwas reduziert.

## <a name="azure-storage-limits"></a>Speichergrenzwerte für Azure

In diesem Abschnitt werden die Grenzwerte für den Azure Storage-Dienst und die erforderlichen Benennungskonventionen für Azure Files, Azure-Blockblobs und Azure-Seitenblobs beschrieben, sofern mit dem Data Box-Dienst verfügbar. Machen Sie sich sorgfältig mit den Speichergrenzwerten vertraut, und beachten Sie alle Empfehlungen.

Aktuelle Informationen zu Grenzwerten für den Azure Storage-Dienst und bewährte Vorgehensweisen für die Benennung von Freigaben, Containern und Dateien finden Sie hier:

- [Benennen und Referenzieren von Containern, Blobs und Metadaten](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Benennen und Referenzieren von Freigaben, Verzeichnissen, Dateien und Metadaten](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Verstehen von Blockblobs, Anfügeblobs und Seitenblobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Wenn Dateien oder Verzeichnisse die Grenzwerte des Azure Storage-Diensts überschreiten oder nicht den Benennungskonventionen für Azure Files/-Blobs entsprechen, werden diese Dateien oder Verzeichnisse nicht über den Data Box-Dienst in Azure Storage erfasst.

## <a name="data-upload-caveats"></a>Einschränkungen beim Hochladen von Daten

- Kopieren Sie Daten nicht direkt auf die Datenträger. Kopieren Sie Daten in die zuvor erstellten Ordner *BlockBlob*, *PageBlob* und *AzureFile*.
- Ein Ordner unter *BlockBlob* und *PageBlob* ist ein Container. Container werden beispielsweise als *BlockBlob/container* und *PageBlob/container* erstellt.
- Wenn ein Ordner denselben Namen wie ein vorhandener Container hat, wird der Inhalt des Ordners mit dem Inhalt des Containers zusammengeführt. Dateien oder Blobs, die sich noch nicht in der Cloud befinden, werden dem Container hinzugefügt. Wenn eine Datei oder ein Blob denselben Namen wie eine Datei oder ein Blob hat, die bzw. das sich bereits im Container befindet, wird die vorhandene Datei oder das Blob überschrieben.
- Jede Datei, die in die Freigaben *BlockBlob* und *PageBlob* geschrieben wird, wird als Blockblob bzw. Seitenblob hochgeladen. 
- Die Hierarchie der Dateien wird beim Hochladen in die Cloud sowohl für Blobs als auch für Azure Files beibehalten. Beispiel: Sie haben eine Datei in diesen Pfad kopiert: `<container folder>\A\B\C.txt`. Dann wird diese Datei wird in den gleichen virtuellen Pfad in der Cloud hochgeladen.
- Eine unter den Ordnern *BlockBlob* und *PageBlob* erstellte leere Verzeichnishierarchie (ohne jegliche Dateien) wird nicht hochgeladen.
- Zur Leistungsverbesserung während Datenuploads empfehlen wir, dass Sie [große Dateifreigaben für das Speicherkonto aktivieren und die Freigabekapazität auf 100 TiB erhöhen](../../articles/storage/files/storage-how-to-create-file-share.md#enable-large-files-shares-on-an-existing-account). Große Dateifreigaben werden nur bei Speicherkonten mit lokal redundantem Speicher (LRS) unterstützt.
- Wenn beim Hochladen von Daten in Azure Fehler auftreten, wird im Zielspeicherkonto ein Fehlerprotokoll erstellt. Der Pfad zu diesem Fehlerprotokoll ist im Portal verfügbar, wenn der Upload abgeschlossen ist, und Sie können das Protokoll überprüfen, um Korrekturmaßnahmen zu ergreifen. Löschen Sie keine Daten aus der Quelle, ohne die hochgeladenen Daten zu überprüfen.
- Dateimetadaten und NTFS-Berechtigungen werden beim Hochladen der Daten in Azure Files nicht beibehalten. Beispielsweise wird das Attribut *Letzte Änderung* der Dateien beim Kopieren der Daten nicht beibehalten.
- Wenn Sie verwaltete Datenträger in der Bestellung angegeben haben, beachten Sie die folgenden zusätzlichen Punkte:

    - Der Name eines verwalteten Datenträgers muss in einer Ressourcengruppe in allen vorab erstellten Ordner und der gesamten Data Box Disk-Instanz eindeutig sein. Die VHDs, die in die vorab erstellten Ordner hochgeladen werden, müssen also jeweils einen eindeutigen Namen aufweisen. Achten Sie darauf, dass der Name nicht mit dem Namen eines bereits vorhandenen verwalteten Datenträgers in einer Ressourcengruppe identisch ist. Wenn VHDs denselben Namen haben, wird nur eine VHD in einen verwalteten Datenträger mit diesem Namen konvertiert. Die anderen VHDs werden als Seitenblobs in das Stagingspeicherkonto hochgeladen.
    - Kopieren Sie die VHDs immer in einen der vorab erstellten Ordner. Wenn Sie die VHDs an einen Ort außerhalb dieser Ordner oder in einen selbst erstellten Ordner kopieren, werden sie nicht als verwaltete Datenträger, sondern als Seitenblobs in das Azure Storage-Konto hochgeladen.
    - Zur Erstellung von verwalteten Datenträgern können nur feste VHDs hochgeladen werden. Dynamische VHDs, differenzierende VHDs oder VHDX-Dateien werden nicht unterstützt.
    - Nicht-VHD-Dateien, die in die vorher erstellten Ordner für verwaltete Datenträger kopiert werden, werden nicht in einen verwalteten Datenträger konvertiert.

## <a name="azure-storage-account-size-limits"></a>Größenbeschränkungen für das Azure-Speicherkonto

Dies sind die Grenzwerte für die Datenmenge, die in ein Speicherkonto kopiert werden kann. Stellen Sie sicher, dass die von Ihnen hochgeladenen Daten diesen Grenzwerten entsprechen. 

| Datentyp             | Standardlimit          |
|--------------------------|------------------------|
| Blockblob, Seitenblob    | Aktuelle Informationen zu diesen Grenzwerten finden Sie unter [Skalierbarkeitsziele für Azure Blob Storage](../storage/blobs/scalability-targets.md#scale-targets-for-blob-storage), [Skalierbarkeitsziele für Azure-Standardspeicher](../storage/common/scalability-targets-standard-account.md#scale-targets-for-standard-storage-accounts) und [Skalierbarkeitsziele für Azure Files](../storage/files/storage-files-scale-targets.md). <br /><br /> Die Grenzwerte schließen Daten aus allen Quellen ein, einschließlich Data Box Disk.|


## <a name="azure-object-size-limits"></a>Größenbeschränkungen für das Azure-Objekt

Hier sind die Größen der Azure-Objekte, die geschrieben werden können. Stellen Sie sicher, dass alle Dateien, die hochgeladen werden, diesen Einschränkungen entsprechen.

| Azure-Objekttyp | Standardlimit                                             |
|-------------------|-----------------------------------------------------------|
| Blockblob        | Ca. 4,75 TiB                                                 |
| Seitenblob         | 8 TiB <br> (Jede Datei, die im Seitenblobformat hochgeladen wird, muss einem ganzzahligen Vielfachen von 512 Bytes entsprechen. Andernfalls misslingt das Hochladen. <br> VHD- und VHDX-Dateien entsprechen einem ganzzahligen Vielfachen von 512 Bytes.) |
|Azure Files        | 1 TiB <br> Maximal Die Größe der Freigabe ist 5 TiB     |
| Verwaltete Datenträger     |4 TiB <br> Weitere Informationen zu den Größen und Grenzwerten finden Sie unter: <li>[Skalierbarkeitsziele für verwaltete Datenträger](../virtual-machines/disks-scalability-targets.md#managed-virtual-machine-disks)</li>|


## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Benennungskonventionen für Azure-Blockblobs, -Seitenblobs und -Dateien

| Entität                                       | Konventionen                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Containernamen für Blockblob und Seitenblob <br> Freigabenamen für Azure Files | Es muss sich um einen gültigen DNS-Namen mit einer Länge von 3 bis 63 Zeichen handeln. <br>  Sie muss mit einem Buchstaben oder einer Zahl beginnen. <br> Nur Kleinbuchstaben, Zahlen und Bindestriche (-) sind zulässig. <br> Vor und nach jedem Bindestrich (-) muss unmittelbar ein Buchstabe oder eine Ziffer stehen. <br> Aufeinanderfolgende Bindestriche sind in Namen nicht zulässig. |
| Verzeichnis- und Dateinamen für Azure-Dateien     |<li> Groß-/Kleinschreibung wird beibehalten, aber nicht berücksichtigt, maximal 255 Zeichen. </li><li> Schrägstrich (/) als letztes Zeichen nicht erlaubt. </li><li>Falls eingegeben, erfolgt eine automatische Entfernung. </li><li> Folgende Zeichen sind nicht zulässig: <code>" \\ / : \| < > * ?</code></li><li> Reservierte URL-Zeichen müssen korrekt mit Escapezeichen versehen sein. </li><li> Ungültige Zeichen im URL-Pfad sind unzulässig. Codepunkte wie \\uE000 sind keine gültigen Unicode-Zeichen. Einige ASCII- und Unicode-Zeichen, z.B. Steuerzeichen (0x00 to 0x1F, \\u0081 usw.), sind ebenfalls unzulässig. Regeln für Unicode-Zeichenfolgen in HTTP/1.1 finden Sie unter „RFC 2616, Section 2.2: Basic Rules“ und „RFC 3987“. </li><li> Die folgenden Dateinamen sind unzulässig: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, Punkt (.) und zwei Punkte (..).</li>|
| Blobnamen für Blockblobs und Seitenblobs      | Für Blobnamen wird die Groß-/Kleinschreibung beachtet, und sie können eine beliebige Kombination von Zeichen enthalten. <br> Ein Blobname muss zwischen 1 und 1.024 Zeichen lang sein. <br> Reservierte URL-Zeichen müssen korrekt mit Escapezeichen versehen sein. <br>Die Anzahl von Pfadsegmenten, aus denen der Blobname besteht, darf 254 nicht überschreiten. Ein Pfadsegment ist die Zeichenfolge zwischen aufeinanderfolgenden Trennzeichen (z.B. der Schrägstrich (/)), die für den Namen eines virtuellen Verzeichnisses steht. |

## <a name="managed-disk-naming-conventions"></a>Benennungskonventionen für verwaltete Datenträger

| Entität | Konventionen                                             |
|-------------------|-----------------------------------------------------------|
| Namen für verwaltete Datenträger       | <li> Der Name muss aus 1 bis 80 Zeichen bestehen. </li><li> Der Name muss mit einem Buchstaben oder einer Zahl beginnen und mit einem Buchstaben, einer Zahl oder einem Unterstrich enden. </li><li> Der Name darf nur Buchstaben, Ziffern, Unterstriche, Punkte und Bindestriche enthalten. </li><li>     Der Name darf kein Leerzeichen oder `/` enthalten.                     |

## <a name="next-steps"></a>Nächste Schritte

- Überprüfen der [Systemanforderungen für Data Box Disk](data-box-disk-system-requirements.md)
