---
title: Beibehalten von ACLs, Attributen und Zeitstempeln für Dateien mit Azure Data Box
description: In diesem Artikel erfahren Sie, wie ACLs, Zeitstempel und Attribute für Datenkopien über SMB in Azure Data Box beibehalten werden. Außerdem erfahren Sie, wie Metadaten mit Tools für das Kopieren von Daten unter Windows und Linux kopiert werden.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: conceptual
ms.date: 07/16/2021
ms.author: alkohli
ms.openlocfilehash: 96d3957a7626e393728d4a309bc56ecaa19d4e83
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2021
ms.locfileid: "114400919"
---
# <a name="preserving-file-acls-attributes-and-timestamps-with-azure-data-box"></a>Beibehalten von ACLs, Attributen und Zeitstempeln für Dateien mit Azure Data Box

Azure Data Box ermöglicht es Ihnen, Zugriffssteuerungslisten (Access Control Lists, ACLs), Zeitstempel und Dateiattribute beim Senden von Daten an Azure beizubehalten. In diesem Artikel werden die Metadaten beschrieben, die Sie beim Kopieren von Daten zu Data Box über Server Message Block (SMB) für den Upload zu Azure Files übertragen können. 

Außerdem finden Sie Informationen zu den einzelnen Schritten für das Kopieren von Metadaten mithilfe von Tools für das Kopieren von Daten unter Windows und Linux. Metadaten werden beim Übertragen von Daten in Blobspeicher nicht beibehalten.

In diesem Artikel werden die ACLs, Zeitstempel und Dateiattribute, die übertragen werden, zusammengefasst als *Metadaten* bezeichnet.

## <a name="transferred-metadata"></a>Übertragene Metadaten

Die folgenden Metadaten werden übertragen, wenn Daten aus der Data Box in Azure Files hochgeladen werden.

#### <a name="timestamps"></a>Zeitstempel

Die folgenden Zeitstempel werden übertragen:
- CreationTime
- LastWriteTime

Die folgenden Zeitstempel werden nicht übertragen:
- LastAccessTime
  
#### <a name="file-attributes"></a>Dateiattribute

Dateiattribute sowohl von Dateien als auch von Verzeichnissen werden übertragen, sofern dies nicht anders angegeben wird.

Die folgenden Dateiattribute werden übertragen:
- FILE_ATTRIBUTE_READONLY (nur für Dateien)
- FILE_ATTRIBUTE_HIDDEN
- FILE_ATTRIBUTE_SYSTEM
- FILE_ATTRIBUTE_DIRECTORY (nur für Verzeichnisse)
- FILE_ATTRIBUTE_ARCHIVE
- FILE_ATTRIBUTE_TEMPORARY (nur für Dateien)
- FILE_ATTRIBUTE_NO_SCRUB_DATA

Die folgenden Dateiattribute werden nicht übertragen:
- FILE_ATTRIBUTE_OFFLINE
- FILE_ATTRIBUTE_NOT_CONTENT_INDEXED
  
Nur-Lese-Attribute in Verzeichnissen werden nicht übertragen.

#### <a name="acls"></a>ACLs

Alle ACLs für Verzeichnisse und Dateien, die Sie in Ihre Data Box über SMB kopieren, werden kopiert und übertragen. Übertragungen schließen sowohl besitzerverwaltete ACLs (DACLs) als auch System-ACLs (SACLs) ein. Unter Linux werden nur Windows-NT-ACLs übertragen.

ACLs werden für Datenkopien über NTS (Network File System) nicht übertragen und wenn Sie den Dienst für Datenkopien verwenden, um Ihre Daten zu übertragen. Der Dienst für Datenkopien liest die Daten direkt aus Ihren Freigaben und kann ACLs nicht lesen.

Selbst wenn Ihr Tool zum Kopieren von Daten ACLs nicht kopiert, werden die Standard-ACLs in Verzeichnissen und Dateien zu Azure Files übertragen. Die Standard-ACLs verfügen über Berechtigungen für das integrierte Administratorkonto, das SYSTEM-Konto und das Benutzerkonto der SMB-Freigabe, das verwendet wurde, um die Daten in der Data Box einzubinden und zu kopieren.

Die ACLs enthalten Sicherheitsbeschreibungen mit den folgenden Eigenschaften: ACLs, Owner (Besitzer), Group (Gruppe), SACL.

Die Übertragung von ACLs ist standardmäßig aktiviert. Sie sollten diese Einstellung auf der lokalen Webbenutzeroberfläche in Ihrer Data Box deaktivieren. Weitere Informationen finden Sie unter [Verwalten von Data Box und Data Box Heavy über die lokale Webbenutzeroberfläche](./data-box-local-web-ui-admin.md).

> [!NOTE]
> Dateien mit ACLs, die Zeichenfolgen des Eintrags für die Steuerung des bedingten Zugriffs (Access Control Entry, ACE) enthalten, werden nicht kopiert. Dies ist ein bekanntes Problem. Als Problemumgehung können Sie diese Dateien manuell in die Azure Files-Freigabe kopieren, indem Sie die Freigabe einbinden und dann ein Tool zum Kopieren der Daten verwenden, das das Kopieren von ACLs unterstützt.

## <a name="copying-data-and-metadata"></a>Kopieren von Daten und Metadaten

Verwenden Sie zum Übertragen der ACLs, Zeitstempel und Attribute für Ihre Daten die folgenden Prozeduren, um Daten in die Data Box zu kopieren. 

### <a name="windows-data-copy-tool"></a>Tool zum Kopieren von Daten unter Windows

Verwenden Sie zum Kopieren von Daten in Ihre Data Box über SMB ein SMB-kompatibles Dateikopiertool wie `robocopy`. Der folgende Beispielbefehl kopiert alle Dateien und Verzeichnisse und überträgt dabei die Metadaten zusammen mit den Daten.

Wenn die Option `/copyall` oder `/dcopy:DAT` verwendet wird, sollten Sie dafür sorgen, dass die erforderlichen Sicherungsoperatorberechtigungen nicht deaktiviert sind. Weitere Informationen finden Sie unter [Verwalten von Data Box und Data Box Heavy über die lokale Webbenutzeroberfläche](./data-box-local-web-ui-admin.md). 

```console
robocopy <Source> <Target> * /copyall /e /dcopy:DAT /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /log+:<LogFile>
```

where

|Option |Beschreibung |
|------------------- | ----- |
|`/copyall` |Hiermit werden alle Attribute kopiert.|
|`/e`      |Hiermit werden Unterverzeichnisse einschließlich leerer Verzeichnisse kopiert.         |
|`/dcopy:DAT`  |Hiermit werden Daten, Attribute und Zeitstempel kopiert. Hinweis: Die /dcopy:DAT-Option muss verwendet werden, um `CreationTime` in Verzeichnissen zu übertragen. |
|`/r:3`    |Hiermit werden drei Wiederholungsversuche für Kopien angegeben, wenn ein Fehler beim Kopiervorgang aufgetreten ist.         |
|`/w:60`   |Hiermit wird eine Wartezeit von 60 Sekunden angegeben, bevor ein Wiederholungsversuch gestartet wird.         |
|`/is`     |Schließt die gleichen Dateien ein.         |
|`/nfl`    |Hierbei werden Dateinamen nicht protokolliert.         |
|`/ndl`    |Hierbei werden Verzeichnisnamen nicht protokolliert.        |
|`/np`     |Hierbei wird der Fortschritt des Kopiervorgangs nicht angezeigt.         |
|`/MT:32 or 64`  |Hierbei wird Multithreading mit 32 oder 64 Threads verwendet.           |
|`/fft`    |Hierbei wird die Zeitstempelgranularität für ein beliebiges Dateisystem reduziert.        |
|`/log+:<LogFile>`  |Fügt die Ausgabe an die vorhandene Protokolldatei an.|

Weitere Informationen zu diesen `robocopy`-Parametern finden Sie unter [Tutorial: Kopieren von Daten in eine Azure Data Box über SMB](./data-box-deploy-copy-data.md).

> [!NOTE]
> Wenn Sie `/copyall` zum Kopieren Ihrer Daten verwenden, werden die Quell-ACLs für Verzeichnisse und Dateien an Azure Files übertragen. Wenn Sie nur Lesezugriff auf Ihre Quelldaten hatten und die Quelldaten nicht ändern konnten, haben Sie nur Lesezugriff auf die Daten in Data Box. Verwenden Sie `/copyall` nur, wenn Sie beabsichtigen, alle ACLs für die Verzeichnisse und Dateien zusammen mit den Daten zu kopieren.

#### <a name="use-robocopy-to-list-copy-modify-files-on-data-box"></a>Verwenden von Robocopy zum Auflisten, Kopieren und Ändern von Dateien in Data Box

Im Folgenden finden Sie einige der gängigen Szenarien beim Kopieren von Daten mit `robocopy`.

- **Nur Daten in Data Box kopieren, keine ACLs für Verzeichnisse und Dateien**

    Wählen Sie die Option `/dcopy:DAT`, um nur Daten, Attribute und Zeitstempel zu kopieren. ACLs für Verzeichnisse und Dateien werden nicht kopiert.

- **Daten und ACLs für Verzeichnisse und Dateien in Data Box kopieren**

    Wählen Sie `/copyall`, um alle Quelldaten einschließlich aller ACLs für Verzeichnisse und Dateien zu kopieren.

- **Dateisystem in Data Box mit robocopy auflisten**

    Listen Sie mit diesem Befehl den Inhalt von Verzeichnissen auf:

    `robocopy <source-dir> NULL /l /s /xx /njh /njs /fp /B`

    Beachten Sie, dass Sie diese Dateien im Datei-Explorer nicht auflisten können.
    
- **Ordner und Dateien in Data Box kopieren oder löschen**

    Kopieren Sie mit diesem Befehl eine einzelne Datei:

    `robocopy <source-dir> <destination-dir> <file-name> /B`

    Löschen Sie mit diesem Befehl eine einzelne Datei:

    `robocopy <source-dir> <destination-dir> <file-name> /purge /B`

    Im obigen Befehl darf `<source-dir>` nicht die Datei `<file-name>` enthalten. Anschließend synchronisiert der obige Befehl das Ziel mit der Quelle, was dazu führt, dass die Datei aus dem Ziel entfernt wird.

    Beachten Sie, dass der Datei-Explorer die oben genannten Vorgänge möglicherweise nicht zulässt.

Weitere Informationen finden Sie unter [Verwenden von robocopy-Befehlen](/windows-server/administration/windows-commands/robocopy).

### <a name="linux-data-copy-tool"></a>Tool zum Kopieren von Daten unter Linux

Das Übertragen von Metadaten unter Linux erfolgt in zwei Schritten. Zuerst kopieren Sie die Quelldaten mithilfe eines Tools wie `rsync`, wobei die Metadaten nicht kopiert werden. Nachdem Sie die Daten kopiert haben, können Sie die Metadaten mithilfe eines Tools wie `smbcacls` oder `cifsacl` kopieren. 

Die folgenden Beispielbefehle führen den ersten Schritt aus, indem die Daten mithilfe von `rsync` kopiert werden. 

```console
cp -aR /etc /opt/ 
rsync -avP /etc /opt (-a copies a directory)
```

## <a name="next-steps"></a>Nächste Schritte

- [Kopieren von Daten in eine Azure Data Box über SMB](./data-box-deploy-copy-data.md)
