---
title: Überprüfen der Anzahl der für einen Export mit Azure Import/Export benötigten Laufwerke | Microsoft-Dokumentation
description: Erfahren Sie, wie viele Laufwerke Sie für einen Export mit dem Azure Import/Export-Dienst benötigen.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 10/01/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: eda9ddd4fa6fca9e4d46c5b86a7f3ab8b7c8cb03
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716421"
---
# <a name="check-number-of-drives-needed-for-an-export-with-azure-importexport"></a>Überprüfen der Anzahl der für einen Export mit Azure Import/Export benötigten Laufwerke 

In diesem Artikel wird beschrieben, wie Sie bestimmen, wie viele physische Laufwerke für Ihren Azure Import/Export-Exportauftrag bereitgestellt werden müssen. Sie verwenden das Azure Import/Export-Tool. In diesem Artikel wird erläutert, wie Sie das Tool installieren und verwenden.

Version 1 des Azure Import/Export-Tools listet alle zu exportierenden Blobs auf, berechnet unter Berücksichtigung des notwendigen Mehraufwands, wie diese in Laufwerke der angegebenen Größe gepackt werden, und schätzt dann die erforderliche Anzahl von Laufwerken für die Blobs sowie Informationen zur Laufwerknutzung.

Sie können 2,5“-SSDs, 2,5"-HDDs oder 3,5"-HDDs mit Ihrem Exportauftrag verwenden. Weitere Informationen finden Sie unter [Unterstützte Datenträger](storage-import-export-requirements.md#supported-disks).


## <a name="prerequisite"></a>Voraussetzungen

* Sie müssen das Tool auf einem Windows-System mit einer [unterstützten Betriebssystemversion](storage-import-export-requirements.md#supported-operating-systems) ausführen.

## <a name="determine-how-many-drives-you-need"></a>Bestimmen der Anzahl benötigter Laufwerke

Führen Sie die folgenden Schritte aus, um herauszufinden, wie viele physische Datenträger Sie für Ihren Exportauftrag benötigen:

1. Laden Sie das aktuelle Release von Version 1 des Azure Import/Export-Tools (WAImportExportV1.zip) für Blobs auf das Windows-System herunter.
  1. [Laden Sie Version 1 von WAImportExport herunter](https://www.microsoft.com/download/details.aspx?id=42659). Die aktuelle Version ist 1.5.0.300.
  1. Entzippen Sie die Dateien in den Standardordner `waimportexportv1`. Beispiel: `C:\WaImportExportV1`.

2. Öffnen Sie ein PowerShell- oder Befehlszeilenfenster mit Administratorrechten. Um das Verzeichnis in den Ordner mit den entzippten Daten zu ändern, führen Sie den folgenden Befehl aus:

   `cd C:\WaImportExportV1`

3. Um die erforderliche Anzahl von Datenträgern für die ausgewählten Blobs zu überprüfen, führen Sie den folgenden Befehl aus:

   `WAImportExport.exe PreviewExport /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    Die Parameter werden in der folgenden Tabelle beschrieben:

    |Befehlszeilenparameter|BESCHREIBUNG|
    |--------------------------|-----------------|
    |**/logdir:**|Optional. Das Protokollverzeichnis In dieses Verzeichnis werden ausführliche Protokolldateien geschrieben. Ohne spezielle Angabe wird das aktuelle Verzeichnis als Protokollverzeichnis verwendet.|
    |**/ExportBlobListFile:**|Erforderlich. Pfad zur XML-Datei mit der Liste der Blobpfade oder Blobpfadpräfixe für die zu exportierenden Blobs. Das Dateiformat entspricht dem Format, das im Element `BlobListBlobPath` im [Put Job](/rest/api/storageimportexport/jobs)-Vorgang der REST-API des Import/Export-Diensts verwendet wird.|
    |**/DriveSize:**|Erforderlich. Die Größe der Laufwerke, die für einen Exportauftrag verwendet werden sollen, *beispielsweise* 500 GB oder 1,5 TB.|

    Weitere Informationen finden Sie unter [Beispiel für den Befehl PreviewExport](#example-of-previewexport-command).

4. Überprüfen Sie, ob Sie Lese-/Schreibzugriff auf die Laufwerke haben, die für den Exportauftrag versendet werden sollen.

## <a name="example-of-previewexport-command"></a>Beispiel für den Befehl PreviewExport

Das folgende Beispiel veranschaulicht die Verwendung des Befehls `PreviewExport`:

```powershell
    WAImportExport.exe PreviewExport /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB
```

Die Datei mit der Blobliste für den Export kann wie hier gezeigt Blobnamen und Blobpräfixe enthalten:

```xml
<?xml version="1.0" encoding="utf-8"?>
<BlobList>
<BlobPath>pictures/animals/koala.jpg</BlobPath>
<BlobPathPrefix>/vhds/</BlobPathPrefix>
<BlobPathPrefix>/movies/</BlobPathPrefix>
</BlobList>
```

### <a name="examples-of-valid-blob-paths"></a>Beispiele für gültige Blobpfade

Die folgende Tabelle enthält Beispiele für gültige Blobpfade und Blobpfadpräfixe, die mit den Tags &lt;BlobPath&gt; und &lt;BlobPathPrefix&gt; in der Bloblistendatei verwendet werden.

* Ein *Pfad* bietet die Möglichkeit, ein einzelnes Blob oder eine einzelne Datei auszuwählen und zu filtern.
* Ein *Präfix* bietet die Möglichkeit, mehrere Blobs oder Dateien auszuwählen und zu filtern.

   | Auswahl | Blobpfad/-präfix | BESCHREIBUNG |
   | --- | --- | --- |
   | Starts With |/ |Exportiert alle Blobs im Speicherkonto |
   | Starts With |/$root/ |Exportiert alle Blobs im Stammcontainer |
   | Starts With |/book |Exportiert alle Blobs in allen Containern mit dem Präfix **book** |
   | Starts With |/music/ |Exportiert alle Blobs im Container **music** |
   | Starts With |/music/love |Exportiert alle Blobs im Container **music**, die mit dem Präfix **love** beginnen |
   | Equal To |$root/logo.bmp |Exportiert das Blob **logo.bmp** im Stammcontainer |
   | Equal To |videos/story.mp4 |Exportiert das Blob **story.mp4** im Container **videos** |


### <a name="sample-output"></a>Beispielausgabe

Hier sehen Sie ein Beispiel der Ausgabe ohne Informationsprotokolle:

```powershell
Number of unique blob paths/prefixes:   3
Number of duplicate blob paths/prefixes:        0
Number of nonexistent blob paths/prefixes:      1

Drive size:     500.00 GB
Number of blobs that can be exported:   6
Number of blobs that cannot be exported:        2
Number of drives needed:        3
        Drive #1:       blobs = 1, occupied space = 454.74 GB
        Drive #2:       blobs = 3, occupied space = 441.37 GB
        Drive #3:       blobs = 2, occupied space = 131.28 GB
```

## <a name="next-steps"></a>Nächste Schritte

- [Exportieren von Blobs aus Azure Blob Storage](storage-import-export-data-from-blobs.md)
