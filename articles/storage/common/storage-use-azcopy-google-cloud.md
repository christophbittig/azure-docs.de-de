---
title: Kopieren aus Google Cloud Storage in Azure Storage mit AzCopy | Microsoft-Dokumentation
description: Verwenden von AzCopy zum Kopieren von Daten aus Google Cloud Storage in Azure Storage. AzCopy ist ein Befehlszeilenhilfsprogramm, das Sie verwenden können, um Blobs oder Dateien in oder aus einem Speicherkonto zu kopieren.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 8e592f8783bb0659a453c8d7d54cddf921f5b1e1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128606929"
---
# <a name="copy-data-from-google-cloud-storage-to-azure-storage-by-using-azcopy-preview"></a>Kopieren von Daten aus Google Cloud Storage in Azure Storage mithilfe von AzCopy (Vorschau)

AzCopy ist ein Befehlszeilenhilfsprogramm, das Sie verwenden können, um Blobs oder Dateien in oder aus einem Speicherkonto zu kopieren. Dieser Artikel enthält Informationen zum Kopieren von Objekten, Verzeichnissen und Buckets aus Google Cloud Storage in Azure Blob Storage mithilfe von AzCopy.

> [!IMPORTANT]
> Das Kopieren von Daten aus Google Cloud Storage in Azure Storage ist derzeit als öffentliche Vorschau verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="choose-how-youll-provide-authorization-credentials"></a>Auswählen, wie Sie die Autorisierungsanmeldeinformationen bereitstellen

- Nutzen Sie für die Azure Storage-Autorisierung Azure Active Directory (AD) oder ein SAS-Token (Shared Access Signature).

- Nutzen Sie für die Google Cloud Storage-Autorisierung einen Dienstkontoschlüssel.

### <a name="authorize-with-azure-storage"></a>Autorisieren mit Azure Storage

Lesen Sie den Artikel [Erste Schritte mit AzCopy](storage-use-azcopy-v10.md), um AzCopy herunterzuladen und zu erfahren, wie Sie dem Speicherdienst Autorisierungsanmeldeinformationen bereitstellen können.

> [!NOTE]
> In den Beispielen in diesem Artikel wird davon ausgegangen, dass Sie Anmeldeinformationen für Autorisierung mithilfe von Azure Active Directory (Azure AD) bereitgestellt haben.
>
> Wenn Sie lieber ein SAS-Token für die Autorisierung des Zugriffs auf Blobdaten verwenden möchten, können Sie dieses Token in jedem AzCopy-Befehl an die Ressourcen-URL anfügen. Beispiel: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

### <a name="authorize-with-google-cloud-storage"></a>Autorisierung mit Google Cloud Storage

Für die Autorisierung mit Google Cloud Storage verwenden Sie einen Dienstkontoschlüssel. Informationen zum Erstellen eines Dienstkontoschlüssels finden Sie unter [Dienstkontoschlüssel erstellen und verwalten](https://cloud.google.com/iam/docs/creating-managing-service-account-keys).

Nachdem Sie einen Dienstschlüssel abgerufen haben, legen Sie die Umgebungsvariable `GOOGLE_APPLICATION_CREDENTIALS` auf den absoluten Pfad zur Datei mit dem Dienstkontoschlüssel fest:

| Betriebssystem | Get-Help  |
|--------|-----------|
| **Windows** | `set GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **Linux** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **macOS** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |

## <a name="copy-objects-directories-and-buckets"></a>Kopieren von Objekten, Verzeichnisse und Buckets

Da für AzCopy die API [Put Block From URL](/rest/api/storageservices/put-block-from-url) verwendet wird, werden Daten direkt zwischen Google Cloud Storage und Speicherservern kopiert. Für diese Kopiervorgänge wird nicht die Netzwerkbandbreite Ihres Computers genutzt.

> [!TIP]
> In den Beispielen in diesem Abschnitt werden Pfadargumente in einfache Anführungszeichen ('') eingeschlossen. Verwenden Sie in allen Befehlsshells außer der Windows-Befehlszeile (cmd.exe) einfache Anführungszeichen. Wenn Sie eine Windows-Befehlszeile (cmd.exe) verwenden, müssen Sie Pfadargumente in doppelte Anführungszeichen ("") anstelle von einfachen Anführungszeichen ('') einschließen.

 Diese Beispiele können auch für Konten mit einem hierarchischen Namespace verwendet werden. [Multiprotokollzugriff für Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) ermöglicht es Ihnen, dieselbe URL-Syntax (`blob.core.windows.net`) für diese Konten zu verwenden.

### <a name="copy-an-object"></a>Kopieren eines Objekts

Verwenden Sie dieselbe URL-Syntax (`blob.core.windows.net`) für Konten mit einem hierarchischen Namespace.

**Syntax**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'`

**Beispiel**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'
```

### <a name="copy-a-directory"></a>Kopieren eines Verzeichnisses

Verwenden Sie dieselbe URL-Syntax (`blob.core.windows.net`) für Konten mit einem hierarchischen Namespace.

**Syntax**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**Beispiel**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

> [!NOTE]
> In diesem Beispiel wird das Flag `--recursive` angehängt, um Dateien in allen Unterverzeichnissen zu kopieren.

### <a name="copy-the-contents-of-a-directory"></a>Kopieren des Inhalts eines Verzeichnisses

Mithilfe des Platzhaltersymbols (*) können Sie die Inhalte eines Verzeichnisses kopieren, ohne das Verzeichnis selbst zu kopieren.

**Syntax**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>/*' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**Beispiel**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

### <a name="copy-a-cloud-storage-bucket"></a>Kopieren eines Cloud Storage-Buckets

Verwenden Sie dieselbe URL-Syntax (`blob.core.windows.net`) für Konten mit einem hierarchischen Namespace.

**Syntax**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Beispiel**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

### <a name="copy-all-buckets-in-a-google-cloud-project"></a>Kopieren aller Buckets in einem Google Cloud-Projekt

Legen Sie zunächst `GOOGLE_CLOUD_PROJECT` auf die Projekt-ID des Google Cloud-Projekts fest.

Verwenden Sie dieselbe URL-Syntax (`blob.core.windows.net`) für Konten mit einem hierarchischen Namespace.

**Syntax**

`azcopy copy 'https://storage.cloud.google.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Beispiel**

```azcopy
azcopy copy 'https://storage.cloud.google.com/' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

### <a name="copy-a-subset-of-buckets-in-a-google-cloud-project"></a>Kopieren einer Teilmenge der Buckets in ein Google Cloud-Projekt

Legen Sie zunächst `GOOGLE_CLOUD_PROJECT` auf die Projekt-ID des Google Cloud-Projekts fest.

Kopieren einer Teilmenge der Buckets unter Verwendung eines Platzhaltersymbols (*) im Bucketnamen. Verwenden Sie dieselbe URL-Syntax (`blob.core.windows.net`) für Konten mit einem hierarchischen Namespace.

**Syntax**

`azcopy copy 'https://storage.cloud.google.com/<bucket*name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Beispiel**

```azcopy
azcopy copy 'https://storage.cloud.google.com/my*bucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

## <a name="handle-differences-in-bucket-naming-rules"></a>Behandeln von Unterschieden in den Benennungsregeln für Buckets

Google Cloud Storage verfügt über andere Namenskonventionen für Bucketnamen als Azure-Blobcontainer. Informationen zu diesem Thema finden Sie [hier](https://cloud.google.com/storage/docs/naming-buckets). Wenn Sie die Entscheidung treffen, eine Gruppe mit Buckets in ein Azure-Speicherkonto zu kopieren, tritt für den Kopiervorgang ggf. ein Fehler aufgrund von Unterschieden bei der Benennung auf.

Mit AzCopy werden drei der häufigsten Probleme behandelt, die auftreten können: Buckets, die Punkte enthalten, Buckets, die aufeinanderfolgende Bindestriche enthalten, und Buckets, die Unterstriche enthalten. Namen von Google Cloud Storage-Buckets können Punkte und aufeinanderfolgende Bindestriche enthalten, aber bei Containern in Azure ist dies nicht möglich. Mit AzCopy werden Zeiträume durch Bindestriche und aufeinander folgende Bindestriche durch eine Zahl ersetzt, die für die Anzahl von aufeinander folgenden Bindestriche steht (Beispiel: ein Bucket mit dem Namen `my----bucket` wird zu `my-4-bucket`. Wenn der Bucketname einen Unterstrich (`_`) enthält, ersetzt AzCopy den Unterstrich durch einen Bindestrich. Ein Bucket mit dem Namen `my_bucket` wird z. B zu `my-bucket`.

## <a name="handle-differences-in-object-naming-rules"></a>Unterschiede in Benennungsregeln für Objekte

Google Cloud Storage verfügt über andere Namenskonventionen für Objektnamen als Azure-Blobs. Informationen zu diesem Thema finden Sie [hier](https://cloud.google.com/storage/docs/naming-objects).

In Azure Storage dürfen Objektnamen (oder Segmente im virtuellen Verzeichnispfad) nicht auf Punkte enden (z. B. `my-bucket...`). Nachgestellte Punkte werden beim Kopiervorgang entfernt.

## <a name="handle-differences-in-object-metadata"></a>Behandeln von Unterschieden in Objektmetadaten

Bei Google Cloud Storage und Azure sind in den Namen von Objektschlüsseln unterschiedliche Zeichen zulässig. Informationen zu Metadaten in Google Cloud Storage finden Sie [hier](https://cloud.google.com/storage/docs/metadata). Auf Azure-Seite unterliegen Blobobjektschlüssel den Benennungsregeln für [C#-Bezeichner](/dotnet/csharp/language-reference/).

Im Rahmen des AzCopy-Befehls `copy` können Sie einen Wert für das optionale Flag `s2s-handle-invalid-metadata` angeben. Hiermit wird festgelegt, wie Dateien verarbeitet werden sollen, bei denen die Metadaten der Datei inkompatible Schlüsselnamen enthalten. In der folgenden Tabelle werden die einzelnen Flagwerte beschrieben.

| Flagwert | BESCHREIBUNG  |
|--------|-----------|
| **ExcludeIfInvalid** | (Standardoption) Die Metadaten sind nicht im übertragenen Objekt enthalten. AzCopy protokolliert eine Warnung. |
| **FailIfInvalid** | Objekte werden nicht kopiert. AzCopy protokolliert einen Fehler und fügt diese Fehler der Fehleranzahl hinzu, die in der Zusammenfassung der Übertragung angezeigt wird.  |
| **RenameIfInvalid**  | AzCopy löst den ungültigen Metadatenschlüssel auf und kopiert das Objekt in Azure, indem das aufgelöste Schlüssel-Wert-Paar aus den Metadaten verwendet wird. Informationen zu den genauen Schritten, die von AzCopy zum Umbenennen von Objektschlüsseln ausgeführt werden, finden Sie unten im Abschnitt [Umbenennen von Objektschlüsseln durch AzCopy](#rename-logic). Falls der Schlüssel von AzCopy nicht umbenannt werden kann, wird das Objekt nicht kopiert. |

<a id="rename-logic"></a>

### <a name="how-azcopy-renames-object-keys"></a>Umbenennen von Objektschlüsseln durch AzCopy

AzCopy führt die folgenden Schritte aus:

1. Ersetzt ungültige Zeichen durch „_“.

2. Fügt am Anfang eines neuen gültigen Schlüssels die Zeichenfolge `rename_` hinzu.

   Dieser Schlüssel wird verwendet, um den ursprünglichen **Wert** aus den Metadaten zu speichern.

3. Fügt am Anfang eines neuen gültigen Schlüssels die Zeichenfolge `rename_key_` hinzu.
   Dieser Schlüssel wird verwendet, um den ursprünglichen ungültigen **Schlüssel** aus den Metadaten zu speichern.
   Sie können mit diesem Schlüssel versuchen, die Metadaten auf Azure-Seite wiederherzustellen, weil der Metadatenschlüssel als Wert im Blobspeicherdienst gespeichert wird.

## <a name="next-steps"></a>Nächste Schritte

Weitere Beispiele finden Sie in diesen Artikeln:

- [Beispiele: Hochladen](storage-use-azcopy-blobs-upload.md)
- [Beispiele: Herunterladen](storage-use-azcopy-blobs-download.md)
- [Beispiele: Kopieren zwischen Konten](storage-use-azcopy-blobs-copy.md)
- [Beispiele: Synchronisieren](storage-use-azcopy-blobs-synchronize.md)
- [Beispiele: Amazon S3-Buckets](storage-use-azcopy-s3.md)
- [Beispiele: Azure Files](storage-use-azcopy-files.md)
- [Tutorial: Migrieren von lokalen Daten zum Cloudspeicher mithilfe von AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

Lesen Sie diese Artikel, um Einstellungen zu konfigurieren, die Leistung zu optimieren und Probleme zu beheben:

- [AzCopy v10-Konfigurationseinstellungen (Azure Storage)](storage-ref-azcopy-configuration-settings.md)
- [Optimieren der Leistung von AzCopy mit Azure Storage](storage-use-azcopy-optimize.md)
- [Konfigurieren, Optimieren und Problembehandlung in AzCopy](storage-use-azcopy-configure.md)
