---
title: Entwickeln für Azure Files mit Python | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Python-Anwendungen und -Dienste entwickeln, die Azure Files zum Speichern von Dateidaten verwenden.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/08/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-python
ms.openlocfilehash: 51355be32464facc51193596b90eca23522d617b
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123251865"
---
# <a name="develop-for-azure-files-with-python"></a>Entwickeln für Azure Files mit Python

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Lernen Sie die Grundlagen der Verwendung von Python zur Entwicklung von Apps oder Diensten kennen, die Azure Files zum Speichern von Dateidaten verwenden. Erstellen Sie eine einfache Konsolen-App und erfahren Sie, wie Sie grundlegende Aktionen mit Python- und Azure-Dateien durchführen können:

- Erstellen von Azure-Dateifreigaben
- Erstellen von Verzeichnissen
- Auflisten von Dateien und Verzeichnissen in einer Azure-Dateifreigabe
- Hochladen, Herunterladen und Löschen einer Datei
- Erstellen von Dateifreigabesicherungen mithilfe von Momentaufnahmen

> [!NOTE]
> Da auf Azure Files über das SMB-Protokoll zugegriffen werden kann, können Sie unter Verwendung der standardmäßigen Python-E/A-Klassen und -Funktionen einfache Anwendungen mit Zugriff auf die Azure-Dateifreigabe schreiben. In diesem Artikel erfahren Sie, wie Sie Apps schreiben, die das Azure Files Storage Python SDK verwenden, das über die [Azure Files-REST-API](/rest/api/storageservices/file-service-rest-api) mit Azure Files kommuniziert.

## <a name="applies-to"></a>Gilt für:
| Dateifreigabetyp | SMB | NFS |
|-|:-:|:-:|
| Standard-Dateifreigaben (GPv2), LRS/ZRS | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) |
| Standard-Dateifreigaben (GPv2), GRS/GZRS | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) |
| Premium-Dateifreigaben (FileStorage), LRS/ZRS | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) |

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Herunterladen und Installieren des Azure Storage SDK für Python

> [!NOTE]
> Wenn Sie ein Upgrade aus dem Azure Storage SDK für Python Version 0.36 oder früher vornehmen, deinstallieren Sie das ältere SDK mit `pip uninstall azure-storage`, bevor Sie das neueste Paket installieren.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Für die [Azure Files-Clientbibliothek v12.x für Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share) wird Python 2.7 oder 3.6 oder höher benötigt.

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Für das [Azure Storage-SDK für Python](https://github.com/azure/azure-storage-python) wird Python 2.7 oder 3.6 oder höher benötigt.

---

## <a name="install-via-pypi"></a>Installieren über PyPI

Geben Sie für die Installation über Python Package Index (PyPI) folgenden Befehl ein:

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

```console
pip install azure-storage-file-share
```

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```console
pip install azure-storage-file
```

### <a name="view-the-sample-application"></a>Anzeigen der Beispielanwendung

Um eine Beispielanwendung anzuzeigen und auszuführen, die zeigt, wie Python mit Azure Files verwendet wird, lesen Sie [Azure Storage: Erste Schritte mit Azure Files in Python](https://github.com/Azure-Samples/storage-file-python-getting-started).

Um die Beispielanwendung auszuführen, stellen Sie sicher, dass Sie die `azure-storage-file`- und `azure-storage-common`-Pakete beide installiert haben.

---

## <a name="set-up-your-application-to-use-azure-files"></a>Einrichten der Anwendung für die Verwendung von Azure Files

Fügen Sie das Folgende nahe dem Anfang einer Python-Quelldatei hinzu, um die Codeausschnitte in diesem Artikel zu verwenden.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_Imports":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
from azure.storage.file import FileService
```

---

## <a name="set-up-a-connection-to-azure-files"></a>Einrichten der Verbindung mit Azure Files

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Mit [ShareServiceClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareserviceclient) können Sie mit Freigaben, Verzeichnissen und Dateien arbeiten. Der folgende Code erstellt mithilfe der Verbindungszeichenfolge für das Speicherkonto ein `ShareServiceClient`-Objekt.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateShareServiceClient":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Das [FileService](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true) -Objekt ermöglicht das Arbeiten mit Freigaben, Verzeichnissen und Dateien. Mit dem folgenden Code wird unter Verwendung des Speicherkontonamens und Kontoschlüssels ein `FileService`-Objekt erstellt. Ersetzen Sie `<myaccount>` und `<mykey>` durch Ihren Kontonamen und Schlüssel.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

---

## <a name="create-an-azure-file-share"></a>Erstellen einer Azure-Dateifreigabe

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Im folgenden Codebeispiel wird mithilfe eines [ShareClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient)-Objekts die Freigabe erstellt, wenn sie nicht vorhanden ist.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateFileShare":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Im folgenden Codebeispiel wird mithilfe eines [FileService](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true)-Objekts die Freigabe erstellt, wenn sie nicht vorhanden ist.

```python
file_service.create_share('myshare')
```

---

## <a name="create-a-directory"></a>Erstellen eines Verzeichnisses

Sie können den Speicher organisieren, indem Sie Dateien in Unterverzeichnissen ablegen, anstatt alle Dateien im Stammverzeichnis zu speichern.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Die folgende Methode erstellt ein Verzeichnis im Stammverzeichnis der angegebenen Dateifreigabe mithilfe eines [ShareDirectoryClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharedirectoryclient)-Objekts.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateDirectory":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Mit dem folgenden Code wird ein Unterverzeichnis mit dem Namen *sampledir* im Stammverzeichnis erstellt.

```python
file_service.create_directory('myshare', 'sampledir')
```

---

## <a name="upload-a-file"></a>Hochladen einer Datei

In diesem Abschnitt erfahren Sie, wie Sie eine Datei aus dem lokalen Speicher in Azure Files hochladen können.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Mit der folgenden Methode wird der Inhalt der angegebenen Datei in das angegebene Verzeichnis in der angegebenen Azure-Dateifreigabe hochgeladen.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_UploadFile":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Eine Azure-Dateifreigabe enthält mindestens ein Stammverzeichnis, in dem Dateien gespeichert werden können. Um eine Datei zu erstellen und Daten hochzuladen, verwenden Sie die Methoden [create_file_from_path](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-path-share-name--directory-name--file-name--local-file-path--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object---timeout-none-), [create_file_from_stream](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-stream-share-name--directory-name--file-name--stream--count--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--), [create_file_from_bytes](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-bytes-share-name--directory-name--file-name--file--index-0--count-none--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--) oder [create_file_from_text](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-text-share-name--directory-name--file-name--text--encoding--utf-8---content-settings-none--metadata-none--validate-content-false--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--). Dies sind allgemeine Methoden zur Durchführung der erforderlichen Teilung, wenn die Größe der Daten 64 MiB übersteigt.

`create_file_from_path` lädt den Inhalt einer Datei aus dem angegebenen Pfad, `create_file_from_stream` den Inhalt einer Datei aus einer bereits geöffneten Datei/einem bereits geöffneten Stream hoch. `create_file_from_bytes` lädt ein Byte-Array, `create_file_from_text` den angegebenen Textwert wie vorgegeben codiert hoch (Standardcodierung ist UTF-8).

Das folgende Beispiel lädt den Inhalt der Datei *sunset.png* in die Datei **myfile** hoch.

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None,  # We want to create this file in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

---

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Auflisten von Dateien und Verzeichnissen in einer Azure-Dateifreigabe

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Um die Dateien und Verzeichnisse in einem Unterverzeichnis aufzulisten, verwenden Sie die Methode [list_directories_and_files](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#list-directories-and-files-directory-name-none--name-starts-with-none--marker-none----kwargs-). Diese Methode liefert ein iterierbares Objekt für das automatische Paging zurück. Der folgende Code gibt den **Namen** der einzelnen Dateien und Unterverzeichnisse im angegebenen Verzeichnis an die Konsole aus.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_ListFilesAndDirs":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Verwenden Sie zum Auflisten der Dateien und Verzeichnisse in einer Freigabe die Methode [list_directories_and_files](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#list-directories-and-files-share-name--directory-name-none--num-results-none--marker-none--timeout-none--prefix-none--snapshot-none-). Diese Methode gibt einen Generator zurück. Der folgende Code gibt den **Namen** der einzelnen Dateien und Verzeichnisse in einer Freigabe an der Konsole aus.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

---

## <a name="download-a-file"></a>Herunterladen einer Datei

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Verwenden Sie zum Herunterladen von Daten aus einer Datei [download_file](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#download-file-offset-none--length-none----kwargs-).

Das folgende Beispiel veranschaulicht die Verwendung von `download_file`, um den Inhalt der angegebenen Datei abzurufen und lokal zu speichern, wobei dem Dateinamen **DOWNLOADED-** vorangestellt wird.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DownloadFile":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Verwenden Sie zum Herunterladen von Daten aus einer Datei die Methoden [get_file_to_path](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-path-share-name--directory-name--file-name--file-path--open-mode--wb---start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-), [get_file_to_stream](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-stream-share-name--directory-name--file-name--stream--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-), [get_file_to_bytes](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-bytes-share-name--directory-name--file-name--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-) oder [get_file_to_text](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-text-share-name--directory-name--file-name--encoding--utf-8---start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-). Dies sind allgemeine Methoden zur Durchführung der erforderlichen Teilung, wenn die Größe der Daten 64 MiB übersteigt.

Das folgende Beispiel verwendet `get_file_to_path`, um den Inhalt der Datei **myfile** herunterzuladen und in der Datei *out-sunset.png* zu speichern.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

---

## <a name="create-a-share-snapshot"></a>Erstellen einer Freigabemomentaufnahme

Sie können eine Zeitpunktkopie Ihrer gesamten Dateifreigabe erstellen.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateSnapshot":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Erstellen einer Freigabemomentaufnahme mit Metadaten**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

---

## <a name="list-shares-and-snapshots"></a>Auflisten von Freigaben und Momentaufnahmen

Sie können alle Momentaufnahmen für eine bestimmte Freigabe auflisten.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_ListSharesAndSnapshots":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

---

## <a name="browse-share-snapshot"></a>Durchsuchen einer Freigabemomentaufnahme

Sie können die einzelnen Freigabemomentaufnahmen durchsuchen, um Dateien und Verzeichnisse von diesem Zeitpunkt abzurufen.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_BrowseSnapshotDir":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
directories_and_files = list(
    file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

---

## <a name="get-file-from-share-snapshot"></a>Abrufen einer Datei aus einer Freigabemomentaufnahme

Sie können eine Datei von einer Freigabemomentaufnahme herunterladen. Auf diese Weise können Sie eine frühere Version einer Datei wiederherstellen.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DownloadSnapshotFile":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(
        share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

---

## <a name="delete-a-single-share-snapshot"></a>Löschen einer einzelnen Freigabemomentaufnahme
Sie können eine einzelne Freigabemomentaufnahme löschen.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteSnapshot":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

---

## <a name="delete-a-file"></a>Löschen von Dateien

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Rufen Sie [delete_file](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#delete-file---kwargs-) auf, um eine Datei zu löschen.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteFile":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Rufen Sie [delete_file](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#delete-file-share-name--directory-name--file-name--timeout-none-) auf, um eine Datei zu löschen.

```python
file_service.delete_file('myshare', None, 'myfile')
```

---

## <a name="delete-share-when-share-snapshots-exist"></a>Löschen einer Freigabe bei vorhandenen Freigabemomentaufnahmen

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Zum Löschen einer Freigabe, die Momentaufnahmen enthält, rufen Sie [delete_share](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#delete-share-delete-snapshots-false----kwargs-) mit `delete_snapshots=True` auf.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteShare":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Eine Freigabe, die Momentaufnahmen enthält, kann erst gelöscht werden, nachdem alle Momentaufnahmen gelöscht wurden.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

---

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit dem Bearbeiten von Azure Files mit Python vertraut gemacht haben, folgen Sie diesen Links, um mehr zu erfahren.

- [Python Developer Center](/azure/developer/python/)
- [REST-API für Azure-Speicherdienste](/rest/api/azure/)
- [Microsoft Azure Storage-SDK für Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage)
