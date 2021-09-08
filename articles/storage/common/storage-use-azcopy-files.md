---
title: Übertragen von Daten nach oder aus Azure Files mithilfe von AzCopy v10 | Microsoft-Dokumentation
description: Übertragen von Daten mit AzCopy und Dateispeicher. AzCopy ist ein Befehlszeilentool zum Kopieren von Blobs oder Dateien in oder aus einem Speicherkonto. Verwenden Sie AzCopy mit Azure Files.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 91e771554c5ce1940243eaec4a0ea3cf965c3f8f
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114468568"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Übertragen von Daten mit AzCopy und Dateispeicher 

AzCopy ist ein Befehlszeilenhilfsprogramm, mit dem Sie Dateien in ein Speicherkonto oder daraus kopieren können. Dieser Artikel enthält Beispielbefehle, die mit Azure Files funktionieren.

Bevor Sie beginnen, lesen Sie den Artikel [Erste Schritte mit AzCopy](storage-use-azcopy-v10.md), um AzCopy herunterzuladen und sich mit dem Tool vertraut zu machen.

> [!TIP]
> In den Beispielen in diesem Artikel werden Pfadargumente in einfache Anführungszeichen ('') eingeschlossen. Verwenden Sie in allen Befehlsshells außer der Windows-Befehlszeile (cmd.exe) einfache Anführungszeichen. Wenn Sie eine Windows-Befehlszeile (cmd.exe) verwenden, müssen Sie Pfadargumente in doppelte Anführungszeichen ("") anstelle von einfachen Anführungszeichen ('') einschließen.

## <a name="create-file-shares"></a>Erstellen von Dateifreigaben

Sie können den Befehl [azcopy make](storage-ref-azcopy-make.md) verwenden, um eine Dateifreigabe zu erstellen. Mit dem Beispiel in diesem Abschnitt wird eine Dateifreigabe namens `myfileshare` erstellt.

**Syntax**

`azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>'`

**Beispiel**

```azcopy
azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'
```

Ausführliche Referenzdokumente finden Sie unter [azcopy make](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Hochladen von Dateien

Sie können den Befehl [azcopy copy](storage-ref-azcopy-copy.md) verwenden, um Dateien und Verzeichnisse von Ihrem lokalen Computer hochzuladen.

Dieser Abschnitt enthält folgende Beispiele:

> [!div class="checklist"]
> * Hochladen einer Datei
> * Hochladen eines Verzeichnisses
> * Hochladen der Inhalte eines Verzeichnisses
> * Hochladen einer bestimmten Datei

> [!TIP]
> Sie können den Uploadvorgang mit optionalen Flags optimieren. Hier sind einige Beispiele angegeben.  
>
> |Szenario|Flag|
> |---|---|
> |Kopieren von Zugriffssteuerungslisten (ACLs) zusammen mit den Dateien|**--preserve-smb-permissions**=\[true\|false\]|
> |Kopieren von SMB-Eigenschaftsinformationen zusammen mit den Dateien|**--preserve-smb-info**=\[true\|false\]|
> 
> Eine vollständige Liste finden Sie unter [Optionen](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> AzCopy berechnet und speichert den MD5-Hashcode der Datei nicht automatisch. Wenn AzCopy diese Vorgänge ausführen soll, fügen Sie das Flag `--put-md5` an jeden Kopierbefehl an. Auf diese Weise berechnet AzCopy beim Herunterladen der Datei einen MD5-Hash für heruntergeladene Daten und überprüft, ob der in der `Content-md5`-Eigenschaft der Datei gespeicherte MD5-Hash mit dem berechneten Hash übereinstimmt.

### <a name="upload-a-file"></a>Hochladen einer Datei

**Syntax**

`azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name><SAS-token>'`

**Beispiel**

```azcopy
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --preserve-smb-permissions=true --preserve-smb-info=true
```

Sie können eine Datei auch mit einem Platzhaltersymbol (*) an einer beliebigen Stelle im Dateipfad oder Dateinamen hochladen. Beispiel: `'C:\myDirectory\*.txt'` oder `C:\my*\*.txt`

### <a name="upload-a-directory"></a>Hochladen eines Verzeichnisses

Dieses Beispiel kopiert ein Verzeichnis (sowie alle in diesem Verzeichnis enthaltenen Dateien) in eine Dateifreigabe. Das Resultat ist ein gleichnamiges Verzeichnis in der Dateifreigabe.

**Syntax**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Beispiel**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

Um ein Verzeichnis innerhalb der Dateifreigabe zu kopieren, geben Sie einfach den Namen des Verzeichnisses in Ihrer Befehlszeichenfolge an.

**Beispiel**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

Wenn Sie den Namen eines Verzeichnisses angeben, das in der Dateifreigabe nicht vorhanden ist, erstellt AzCopy ein neues Verzeichnis dieses Namens.

### <a name="upload-the-contents-of-a-directory"></a>Hochladen der Inhalte eines Verzeichnisses

Mithilfe des Platzhaltersymbols (*) können Sie die Inhalte eines Verzeichnisses hochladen, ohne das Verzeichnis selbst zu kopieren.

**Syntax**

`azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>'`

**Beispiel**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --preserve-smb-permissions=true --preserve-smb-info=true
```

> [!NOTE]
> Fügen Sie das Flag `--recursive` an, um Dateien in allen Unterverzeichnissen hochzuladen.

### <a name="upload-specific-files"></a>Hochladen bestimmter Dateien

Sie können bestimmte Dateien mithilfe von vollständigen Dateinamen, partiellen Namen mit Platzhalterzeichen (*) oder mithilfe von Datums- und Uhrzeitwerten hochladen.

#### <a name="specify-multiple-complete-file-names"></a>Angeben mehrerer vollständiger Dateinamen

Verwenden Sie den Befehl [azcopy copy](storage-ref-azcopy-copy.md) mit der Option `--include-path`. Trennen Sie einzelne Dateinamen durch ein Semikolon (`;`).

**Syntax**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-path <semicolon-separated-file-list>`

**Beispiel**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt' --preserve-smb-permissions=true --preserve-smb-info=true
```

In diesem Beispiel überträgt AzCopy das Verzeichnis `C:\myDirectory\photos` und die Datei `C:\myDirectory\documents\myFile.txt`. Sie müssen die Option `--recursive` verwenden, um alle Dateien im Verzeichnis `C:\myDirectory\photos` zu übertragen.

Mit der Option `--exclude-path` können Sie auch Dateien ausschließen. Weitere Informationen finden Sie in den Referenzdokumenten zu [azcopy copy](storage-ref-azcopy-copy.md).

#### <a name="use-wildcard-characters"></a>Verwenden von Platzhalterzeichen

Verwenden Sie den Befehl [azcopy copy](storage-ref-azcopy-copy.md) mit der Option `--include-pattern`. Geben Sie Namen mithilfe von Platzhalterzeichen teilweise an. Trennen Sie die Namen durch Semikolons (`;`).

**Syntax**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>`

**Beispiel**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*' --preserve-smb-permissions=true --preserve-smb-info=true
```

Mit der Option `--exclude-pattern` können Sie auch Dateien ausschließen. Weitere Informationen finden Sie in den Referenzdokumenten zu [azcopy copy](storage-ref-azcopy-copy.md).

Die Optionen `--include-pattern` und `--exclude-pattern` gelten nur für Dateinamen und nicht für den Pfad.  Wenn Sie alle Textdateien in einer Verzeichnisstruktur kopieren möchten, verwenden Sie die Option `--recursive`, um die gesamte Verzeichnisstruktur abzurufen. Verwenden Sie dann `--include-pattern`, und geben Sie `*.txt` an, um alle Textdateien abzurufen.

#### <a name="upload-files-that-were-modified-after-a-date-and-time"></a>Hochladen von Dateien, die nach einem bestimmten Datum und einer bestimmten Uhrzeit geändert wurden 

Verwenden Sie den Befehl [azcopy copy](storage-ref-azcopy-copy.md) mit der Option `--include-after`. Geben Sie ein Datum und eine Uhrzeit im ISO 8601-Format an (z. B. `2020-08-19T15:04:00Z`). 

**Syntax**

`azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>'  --include-after <Date-Time-in-ISO-8601-format>`

**Beispiel**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-after '2020-08-19T15:04:00Z' --preserve-smb-permissions=true --preserve-smb-info=true
```

Ausführliche Informationen finden Sie in den [azcopy copy](storage-ref-azcopy-copy.md)-Referenzdokumenten.

## <a name="download-files"></a>Herunterladen von Dateien

Sie können den Befehl [azcopy copy](storage-ref-azcopy-copy.md) verwenden, um Dateien, Verzeichnisse und Dateifreigaben auf Ihren lokalen Computer herunterzuladen.

Dieser Abschnitt enthält folgende Beispiele:

> [!div class="checklist"]
> * Herunterladen einer Datei
> * Herunterladen eines Verzeichnisses
> * Herunterladen der Inhalte eines Verzeichnisses
> * Herunterladen bestimmter Dateien

> [!TIP]
> Sie können den Downloadvorgang mit optionalen Flags optimieren. Hier sind einige Beispiele angegeben.
>
> |Szenario|Flag|
> |---|---|
> |Kopieren von Zugriffssteuerungslisten (ACLs) zusammen mit den Dateien|**--preserve-smb-permissions**=\[true\|false\]|
> |Kopieren von SMB-Eigenschaftsinformationen zusammen mit den Dateien|**--preserve-smb-info**=\[true\|false\]|
> |Automatisches Dekomprimieren von Dateien|**DECOMPRESS**|
> 
> Eine vollständige Liste finden Sie unter [Optionen](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> Wenn der `Content-md5`-Eigenschaftswert einer Datei einen Hash enthält, berechnet AzCopy einen MD5-Hash für heruntergeladene Daten und überprüft, ob der in der `Content-md5`-Eigenschaft der Datei gespeicherte MD5-Hash mit dem berechneten Hash übereinstimmt. Wenn diese Werte nicht übereinstimmen, wird der Download nicht durchgeführt, es sei denn, Sie überschreiben dieses Verhalten, indem Sie `--check-md5=NoCheck` oder `--check-md5=LogOnly` an den Kopierbefehl anfügen.

### <a name="download-a-file"></a>Herunterladen einer Datei

**Syntax**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' '<local-file-path>'`

**Beispiel**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt' --preserve-smb-permissions=true --preserve-smb-info=true
```

### <a name="download-a-directory"></a>Herunterladen eines Verzeichnisses

**Syntax**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' '<local-directory-path>' --recursive`

**Beispiel**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

Dieses Beispiel erstellt ein Verzeichnis namens `C:\myDirectory\myFileShareDirectory`, das alle heruntergeladenen Dateien enthält.

### <a name="download-the-contents-of-a-directory"></a>Herunterladen der Inhalte eines Verzeichnisses

Mithilfe des Platzhaltersymbols (*) können Sie die Inhalte eines Verzeichnisses herunterladen, ohne das Verzeichnis selbst zu kopieren.

**Syntax**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*<SAS-token>' '<local-directory-path>/'`

**Beispiel**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory' --preserve-smb-permissions=true --preserve-smb-info=true
```

> [!NOTE]
> Fügen Sie das Flag `--recursive` an, um Dateien in allen Unterverzeichnissen herunterzuladen.

### <a name="download-specific-files"></a>Herunterladen bestimmter Dateien

Sie können bestimmte Dateien mithilfe von vollständigen Dateinamen, partiellen Namen mit Platzhalterzeichen (*) oder mithilfe von Datums- und Uhrzeitwerten herunterladen.

#### <a name="specify-multiple-complete-file-names"></a>Angeben mehrerer vollständiger Dateinamen

Verwenden Sie den Befehl [azcopy copy](storage-ref-azcopy-copy.md) mit der Option `--include-path`. Trennen Sie einzelne Dateinamen durch ein Semikolon (`;`).

**Syntax**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>`

**Beispiel**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

In diesem Beispiel überträgt AzCopy das Verzeichnis `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` und die Datei `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt`. Fügen Sie die Option `--recursive` ein, um alle Dateien im Verzeichnis `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` zu übertragen.

Mit der Option `--exclude-path` können Sie auch Dateien ausschließen. Weitere Informationen finden Sie in den Referenzdokumenten zu [azcopy copy](storage-ref-azcopy-copy.md).

#### <a name="use-wildcard-characters"></a>Verwenden von Platzhalterzeichen

Verwenden Sie den Befehl [azcopy copy](storage-ref-azcopy-copy.md) mit der Option `--include-pattern`. Geben Sie Namen mithilfe von Platzhalterzeichen teilweise an. Trennen Sie die Namen durch Semikolons (`;`).

**Syntax**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>`

**Beispiel**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*' --preserve-smb-permissions=true --preserve-smb-info=true
```

Mit der Option `--exclude-pattern` können Sie auch Dateien ausschließen. Weitere Informationen finden Sie in den Referenzdokumenten zu [azcopy copy](storage-ref-azcopy-copy.md).

Die Optionen `--include-pattern` und `--exclude-pattern` gelten nur für Dateinamen und nicht für den Pfad.  Wenn Sie alle Textdateien in einer Verzeichnisstruktur kopieren möchten, verwenden Sie die Option `--recursive`, um die gesamte Verzeichnisstruktur abzurufen. Verwenden Sie dann `--include-pattern`, und geben Sie `*.txt` an, um alle Textdateien abzurufen.

#### <a name="download-files-that-were-modified-after-a-date-and-time"></a>Herunterladen von Dateien, die nach einem bestimmten Datum und einer bestimmten Uhrzeit geändert wurden 

Verwenden Sie den Befehl [azcopy copy](storage-ref-azcopy-copy.md) mit der Option `--include-after`. Geben Sie ein Datum und eine Uhrzeit im ISO 8601-Format an (z. B. `2020-08-19T15:04:00Z`). 

**Syntax**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>/*<SAS-token>' '<local-directory-path>'  --include-after <Date-Time-in-ISO-8601-format>`

**Beispiel**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/*?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory' --include-after '2020-08-19T15:04:00Z' --preserve-smb-permissions=true --preserve-smb-info=true
```

Ausführliche Informationen finden Sie in den [azcopy copy](storage-ref-azcopy-copy.md)-Referenzdokumenten.

#### <a name="download-from-a-share-snapshot"></a>Herunterladen von einer Freigabemomentaufnahme

Sie können eine bestimmte Version einer Datei oder eines Verzeichnisses herunterladen, indem Sie auf den **DateTime**-Wert einer Freigabemomentaufnahme verweisen. Weitere Informationen zu Freigabemomentaufnahmen finden Sie unter [Übersicht über Freigabemomentaufnahmen für Azure Files](../files/storage-snapshots-files.md). 

**Syntax**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path-or-directory-name><SAS-token>&sharesnapshot=<DateTime-of-snapshot>' '<local-file-or-directory-path>'`

**Beispiel (Herunterladen einer Datei)**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt' --preserve-smb-permissions=true --preserve-smb-info=true
```

**Beispiel (Herunterladen eines Verzeichnisses)**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory'  --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

## <a name="copy-files-between-storage-accounts"></a>Kopieren von Dateien zwischen Speicherkonten

Sie können AzCopy verwenden, um Dateien in andere Speicherkonten zu kopieren. Der Kopiervorgang verläuft synchron – wenn also der Befehl zurückgegeben wird, bedeutet dies, dass alle Dateien kopiert wurden.

AzCopy verwendet die [Server-zu-Server](/rest/api/storageservices/put-block-from-url)-[APIs](/rest/api/storageservices/put-page-from-url), sodass Daten direkt zwischen Speicherservern kopiert werden. Für diese Kopiervorgänge wird nicht die Netzwerkbandbreite Ihres Computers genutzt. Sie können den Durchsatz dieser Vorgänge erhöhen, indem Sie den Wert der Umgebungsvariable `AZCOPY_CONCURRENCY_VALUE` festlegen. Weitere Informationen finden Sie unter [Erhöhen der Parallelität](storage-use-azcopy-optimize.md#increase-concurrency).

Sie können auch bestimmte Versionen von Dateien herunterladen, indem Sie auf den **DateTime**-Wert einer Freigabemomentaufnahme verweisen. Weitere Informationen zu Freigabemomentaufnahmen finden Sie unter [Übersicht über Freigabemomentaufnahmen für Azure Files](../files/storage-snapshots-files.md). 

Dieser Abschnitt enthält folgende Beispiele:

> [!div class="checklist"]
> * Kopieren einer Datei in ein anderes Speicherkonto
> * Kopieren eines Verzeichnisses in ein anderes Speicherkonto
> * Kopieren einer Dateifreigabe in ein anderes Speicherkonto
> * Kopieren aller Dateifreigaben, Verzeichnisse und Dateien in ein anderes Speicherkonto

> [!TIP]
> Sie können den Kopiervorgang mit optionalen Flags optimieren. Hier sind einige Beispiele angegeben.
>
> |Szenario|Flag|
> |---|---|
> |Kopieren von Zugriffssteuerungslisten (ACLs) zusammen mit den Dateien|**--preserve-smb-permissions**=\[true\|false\]|
> |Kopieren von SMB-Eigenschaftsinformationen zusammen mit den Dateien|**--preserve-smb-info**=\[true\|false\]|
> 
> Eine vollständige Liste finden Sie unter [Optionen](storage-ref-azcopy-copy.md#options).

### <a name="copy-a-file-to-another-storage-account"></a>Kopieren einer Datei in ein anderes Speicherkonto

**Syntax**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>'`

**Beispiel**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --preserve-smb-permissions=true --preserve-smb-info=true
```

**Beispiel (Freigabemomentaufnahme)**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --preserve-smb-permissions=true --preserve-smb-info=true
```

### <a name="copy-a-directory-to-another-storage-account"></a>Kopieren eines Verzeichnisses in ein anderes Speicherkonto

**Syntax**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Beispiel**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/myFileShare/myFileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

**Beispiel (Freigabemomentaufnahme)**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/myFileShare/myFileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

### <a name="copy-a-file-share-to-another-storage-account"></a>Kopieren einer Dateifreigabe in ein anderes Speicherkonto

**Syntax**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Beispiel**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D --preserve-smb-permissions=true --preserve-smb-info=true
```

**Beispiel (Freigabemomentaufnahme)**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>Kopieren aller Dateifreigaben, Verzeichnisse und Dateien in ein anderes Speicherkonto

**Syntax**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<SAS-token>' --recursive'`

**Beispiel**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

**Beispiel (Freigabemomentaufnahme)**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

## <a name="synchronize-files"></a>Synchronisieren von Dateien

Sie können den Inhalt eines lokalen Dateisystems mit einer Dateifreigabe oder den Inhalt einer Dateifreigabe mit einer anderen Dateifreigabe synchronisieren. Sie können auch den Inhalt eines Verzeichnisses in einer Dateifreigabe mit dem Inhalt eines Verzeichnisses synchronisieren, das sich in einer anderen Dateifreigabe befindet. Die Synchronisierung erfolgt unidirektional. Anders gesagt: Sie wählen aus, welcher der beiden Endpunkte die Quelle und welcher das Ziel ist. Bei der Synchronisierung werden auch Server-zu-Server-APIs verwendet.

> [!NOTE]
> Dieses Szenario wird derzeit nur für Konten unterstützt, die keinen hierarchischen Namespace besitzen. Mit der aktuellen Version von AzCopy wird keine Synchronisierung zwischen Azure Files und Blob Storage ausgeführt.

### <a name="guidelines"></a>Richtlinien

- Mit dem Befehl [sync](storage-ref-azcopy-sync.md) werden Dateinamen und die Zeitstempel der letzten Änderung verglichen. Legen Sie das optionale Flag `--delete-destination` auf den Wert `true` oder `prompt` fest, um Dateien im Zielverzeichnis zu löschen, wenn diese im Quellverzeichnis nicht mehr vorhanden sind.

- Wenn Sie das Flag `--delete-destination` auf `true` festlegen, löscht AzCopy Dateien, ohne zur Bestätigung aufzufordern. Wenn eine Bestätigungsaufforderung angezeigt werden soll, bevor AzCopy eine Datei löscht, legen Sie das `--delete-destination`-Flag auf `prompt` fest.

- Wenn Sie das Flag `--delete-destination` auf `prompt` oder `false` festlegen möchten, sollten Sie den Befehl [copy](storage-ref-azcopy-copy.md) anstelle des Befehls [sync](storage-ref-azcopy-sync.md) verwenden und den Parameter `--overwrite` auf `ifSourceNewer` festlegen. Der Befehl [copy](storage-ref-azcopy-copy.md) verbraucht weniger Arbeitsspeicher und verursacht weniger Abrechnungskosten, weil die Quelle oder das Ziel bei einem Kopiervorgang vor dem Verschieben von Dateien nicht indiziert werden muss. 

- Der Computer, auf dem Sie den Synchronisierungsbefehl ausführen, sollte über eine genaue Systemuhr verfügen, weil die Zeiten der letzten Änderung maßgeblich sind, um über die Übertragung einer Datei zu entscheiden. Wenn die Zeit Ihres Systems erheblich abweicht, sollten Sie Dateien am Ziel nicht zu nahe an dem Zeitpunkt ändern, zu dem Sie einen Synchronisierungsbefehl ausführen möchten.

> [!TIP]
> Sie können den Synchronisierungsvorgang mit optionalen Flags optimieren. Hier sind einige Beispiele angegeben.
>
> |Szenario|Flag|
> |---|---|
> |Kopieren von Zugriffssteuerungslisten (ACLs) zusammen mit den Dateien|**--preserve-smb-permissions**=\[true\|false\]|
> |Kopieren von SMB-Eigenschaftsinformationen zusammen mit den Dateien|**--preserve-smb-info**=\[true\|false\]|
> |Ausschließen von Dateien basierend auf einem Muster|**--exclude-path**|
> |Angeben, wie detailliert die synchronisierungsbezogenen Protokolleinträge sein sollen|**--log-level**=\[WARNING\|ERROR\|INFO\|NONE\]|
> 
> Eine vollständige Liste finden Sie unter [Optionen](storage-ref-azcopy-sync.md#options).

### <a name="update-a-file-share-with-changes-to-a-local-file-system"></a>Aktualisieren einer Dateifreigabe mit Änderungen an einem lokalen Dateisystem

In diesem Fall ist die Dateifreigabe das Ziel und das lokale Dateisystem die Quelle. 

> [!TIP]
> In diesem Beispiel werden Pfadargumente in einfache Anführungszeichen ('') eingeschlossen. Verwenden Sie in allen Befehlsshells außer der Windows-Befehlszeile (cmd.exe) einfache Anführungszeichen. Wenn Sie eine Windows-Befehlszeile (cmd.exe) verwenden, müssen Sie Pfadargumente in doppelte Anführungszeichen ("") anstelle von einfachen Anführungszeichen ('') einschließen.

**Syntax**

`azcopy sync '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Beispiel**

```azcopy
azcopy sync 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

### <a name="update-a-local-file-system-with-changes-to-a-file-share"></a>Aktualisieren eines lokalen Dateisystems mit Änderungen an einer Dateifreigabe

In diesem Fall ist das lokale Dateisystem das Ziel und die Dateifreigabe die Quelle.

> [!TIP]
> In diesem Beispiel werden Pfadargumente in einfache Anführungszeichen ('') eingeschlossen. Verwenden Sie in allen Befehlsshells außer der Windows-Befehlszeile (cmd.exe) einfache Anführungszeichen. Wenn Sie eine Windows-Befehlszeile (cmd.exe) verwenden, müssen Sie Pfadargumente in doppelte Anführungszeichen ("") anstelle von einfachen Anführungszeichen ('') einschließen.

**Syntax**

`azcopy sync 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'C:\myDirectory' --recursive`

**Beispiel**

```azcopy
azcopy sync 'https://mystorageaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory' --recursive
```

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>Aktualisieren einer Dateifreigabe mit Änderungen in einer anderen Dateifreigabe

Die erste Dateifreigabe in diesem Befehl ist die Quelle. Das zweite ist das Ziel.

**Syntax**

`azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Beispiel**

```azcopy
azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Aktualisieren eines Verzeichnisses mit Änderungen in einem Verzeichnis in einer anderen Dateifreigabe

Das erste Verzeichnis in diesem Befehl ist die Quelle. Das zweite ist das Ziel.

**Syntax**

`azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' --recursive`

**Beispiel**

```azcopy
azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

### <a name="update-a-file-share-to-match-the-contents-of-a-share-snapshot"></a>Aktualisieren einer Dateifreigabe zur Angleichung an den Inhalt einer Freigabemomentaufnahme

Die erste Dateifreigabe in diesem Befehl ist die Quelle. Fügen Sie am Ende des URI die Zeichenfolge `&sharesnapshot=` gefolgt vom Wert **DateTime** der Momentaufnahme an.

**Syntax**

`azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>&sharesnapsot<snapshot-ID>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Beispiel**

```azcopy
azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-03-03T20%3A24%3A13.0000000Z' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive --preserve-smb-permissions=true --preserve-smb-info=true
``` 

Weitere Informationen zu Freigabemomentaufnahmen finden Sie unter [Übersicht über Freigabemomentaufnahmen für Azure Files](../files/storage-snapshots-files.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Beispiele finden Sie in den folgenden Artikeln:

- [Erste Schritte mit AzCopy](storage-use-azcopy-v10.md)
- [Übertragen von Daten](storage-use-azcopy-v10.md#transfer-data)

Lesen Sie diese Artikel, um Einstellungen zu konfigurieren, die Leistung zu optimieren und Probleme zu beheben:

- [AzCopy v10-Konfigurationseinstellungen (Azure Storage)](storage-ref-azcopy-configuration-settings.md)
- [Optimieren der Leistung von AzCopy mit Azure Storage](storage-use-azcopy-optimize.md)
- [Ermitteln von Fehlern und Fortsetzen von Aufträgen mithilfe von Protokoll- und Plandateien in AzCopy](storage-use-azcopy-configure.md)

