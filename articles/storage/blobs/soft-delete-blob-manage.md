---
title: Verwalten und Wiederherstellen vorläufig gelöschter Blobs
titleSuffix: Azure Storage
description: Verwalten und Wiederherstellen vorläufig gelöschter Blobs und Momentaufnahmen mit dem Azure-Portal oder mithilfe der Azure Storage-Clientbibliotheken.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/23/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c66b970a99ad6dd69b9336d1638b2c156868324
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627936"
---
# <a name="manage-and-restore-soft-deleted-blobs"></a>Verwalten und Wiederherstellen vorläufig gelöschter Blobs

Das Feature für das vorläufige Löschen von Blobs schützt einzelne Blobs und die zugehörigen Versionen, Momentaufnahmen und Metadaten vor einer versehentlichen Löschung oder Überschreibung, weil die gelöschten Daten für einen angegebenen Zeitraum im System beibehalten werden. Während der Beibehaltungsdauer kann das Blob in dem Zustand wiederhergestellt werden, in dem es sich zum Zeitpunkt der Löschung befand. Nach Ablauf der Beibehaltungsdauer wird das Blob endgültig gelöscht. Weitere Informationen zum vorläufigen Löschen von Blobs finden Sie unter [Vorläufiges Löschen für Blobs](soft-delete-blob-overview.md).

Das vorläufige Löschen von Blobs ist Teil einer umfassenden Datenschutzstrategie für Blobdaten. Weitere Informationen zu den Datenschutzempfehlungen von Microsoft finden Sie in der [Übersicht zum Datenschutz](data-protection-overview.md).

## <a name="manage-soft-deleted-blobs"></a>Verwalten von vorläufig gelöschten Blobs

### <a name="manage-soft-deleted-blobs-with-the-azure-portal"></a>Verwalten von vorläufig gelöschten Blobs mit dem Azure-Portal

Sie können das Azure-Portal verwenden, um vorläufig gelöschte Blobs und Momentaufnahmen anzuzeigen und wiederherzustellen.

#### <a name="view-deleted-blobs"></a>Anzeigen gelöschter Blobs

Wenn Blobs vorläufig gelöscht werden, sind sie im Azure-Portal standardmäßig nicht sichtbar. Um vorläufig gelöschte Blobs anzuzeigen, navigieren Sie zur Seite **Übersicht** für den Container, und aktivieren Sie die Einstellung **Gelöschte Blobs anzeigen**. Vorläufig gelöschte Blobs werden mit dem Status **Gelöscht** angezeigt.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blobs-list-portal.png" alt-text="Screenshot: Vorgehensweise zum Auflisten vorläufig gelöschter Blobs im Azure-Portal":::

Wählen Sie als Nächstes das gelöschte Blob in der Liste aus, um seine Eigenschaften anzuzeigen. Beachten Sie, dass auf der Registerkarte **Übersicht** der Blobstatus auf **Gelöscht** festgelegt ist. Das Portal zeigt außerdem an, in wie viel Tagen das Blob endgültig gelöscht wird.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-properties-portal.png" alt-text="Screenshot: Eigenschaften eines vorläufig gelöschten Blobs im Azure-Portal":::

#### <a name="view-deleted-snapshots"></a>Anzeigen gelöschter Momentaufnahmen

Beim Löschen eines Blobs werden auch alle dem Blob zugeordnete Momentaufnahmen gelöscht. Wenn ein vorläufig gelöschtes Blob über Momentaufnahmen verfügt, können die gelöschten Momentaufnahmen ebenfalls im Portal angezeigt werden. Zeigen Sie die Eigenschaften des vorläufig gelöschten Blobs an, und navigieren Sie dann zur Registerkarte **Momentaufnahmen**. Aktivieren Sie dort die Option **Gelöschte Momentaufnahmen anzeigen**.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-snapshots-portal.png" alt-text="Screenshot":::

#### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>Wiederherstellen vorläufig gelöschter Objekte bei deaktivierter Versionsverwaltung

Um ein vorläufig gelöschtes Blob im Azure-Portal wiederherzustellen, wenn die Blobversionsverwaltung deaktiviert ist, zeigen Sie zunächst die Eigenschaften des Blobs an und klicken dann auf der Registerkarte **Übersicht** auf die Schaltfläche **Wiederherstellen**. Durch das Wiederherstellen eines Blobs werden auch alle Momentaufnahmen wiederhergestellt, die während der Beibehaltungsdauer für vorläufiges Löschen gelöscht wurden.

:::image type="content" source="media/soft-delete-blob-manage/undelete-soft-deleted-blob-portal.png" alt-text="Screenshot: Wiederherstellen eines vorläufig gelöschten Blobs im Azure-Portal":::

Um eine vorläufig gelöschte Momentaufnahme zum Basisblob hochzustufen, müssen Sie zunächst sicherstellen, dass die vorläufig gelöschten Momentaufnahmen des Blobs wiederhergestellt wurden. Klicken Sie auf die Schaltfläche **Wiederherstellen**, um die vorläufig gelöschten Momentaufnahmen des Blobs wiederherzustellen. Dies gilt selbst dann, wenn das Basisblob selbst nicht vorläufig gelöscht wurde. Als Nächstes wählen Sie die Momentaufnahme auf, die höher gestuft werden soll. Klicken Sie auf die Schaltfläche **Momentaufnahme höher stufen**, um das Basisblob mit den Inhalten der Momentaufnahme zu überschreiben.

:::image type="content" source="media/soft-delete-blob-manage/promote-snapshot.png" alt-text="Screenshot: Vorgehensweise zum Höherstufen einer Momentaufnahme zum Basisblob":::

#### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>Wiederherstellen vorläufig gelöschter Objekte bei aktivierter Versionsverwaltung

Um ein vorläufig gelöschtes Blob im Azure-Portal wiederherzustellen, wenn die Versionsverwaltung aktiviert ist, zeigen Sie zunächst die Eigenschaften des vorläufig gelöschten Blobs an und wählen dann die Registerkarte **Versionen** aus. Wählen Sie die Version aus, die Sie zur aktuellen Version hochstufen möchten, und klicken Sie dann auf **Als aktuelle Version festlegen**.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-promote-version-portal.png" alt-text="Screenshot: Vorgehensweise zum Höherstufen einer Version zum Wiederherstellen eines Blobs im Azure-Portal":::

Um vorläufig gelöschte Versionen oder Momentaufnahmen wiederherzustellen, wenn die Versionsverwaltung aktiviert ist, zeigen Sie die Eigenschaften des Blobs an und klicken auf der Registerkarte **Übersicht** auf die Schaltfläche **Wiederherstellen**.

> [!NOTE]
> Bei aktivierter Versionsverwaltung werden durch das Klicken auf die Schaltfläche **Wiederherstellen** für ein gelöschtes Blob alle vorläufig gelöschten Versionen oder Momentaufnahmen wiederhergestellt, nicht jedoch das Basisblob. Um das Basisblob wiederherzustellen, müssen Sie eine vorherige Version höher stufen.

### <a name="manage-soft-deleted-blobs-with-code"></a>Verwalten vorläufig gelöschter Blobs mithilfe von Code

Sie können die Azure Storage-Clientbibliotheken verwenden, um vorläufig gelöschte Blobs oder Momentaufnahmen wiederherzustellen. Die folgenden Beispiele zeigen die Verwendung der .NET-Clientbibliothek.

#### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>Wiederherstellen vorläufig gelöschter Objekte bei deaktivierter Versionsverwaltung

##### <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

Für das Wiederherstellen gelöschter Blobs bei aktivierter Versionsverwaltung rufen Sie den Vorgang [Undelete Blobs](/rest/api/storageservices/undelete-blob) für diese Blobs auf. Der Vorgang **Undelete Blobs** stellt vorläufig gelöschte Blobs und alle diesem Blob zugeordneten vorläufig gelöschten Momentaufnahmen wieder her.

Der Aufruf von **Undelete Blob** für einen Blob, der nicht gelöscht wurde, hat keinerlei Auswirkung. Das folgende Beispiel ruft **Undelete Blob** für alle Blobs in einem Container auf und stellt die vorläufig gelöschten Blobs und die zugehörigen Momentaufnahmen wieder her:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverDeletedBlobs":::

Wenn Sie eine spezifische Momentaufnahme wiederherstellen möchten, rufen Sie den Vorgang **Undelete Blob** zunächst für das Basisblob auf, und kopieren Sie die gewünschte Momentaufnahme über das Basisblob. Im folgenden Beispiel wird die zuletzt generierte Momentaufnahme eines Blockblobs wiederhergestellt:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverSpecificBlobSnapshot":::

##### <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

Für das Wiederherstellen gelöschter Blobs bei aktivierter Versionsverwaltung rufen Sie den Vorgang [Undelete Blobs](/rest/api/storageservices/undelete-blob) für diese Blobs auf. Der Vorgang **Undelete Blobs** stellt vorläufig gelöschte Blobs und alle diesem Blob zugeordneten vorläufig gelöschten Momentaufnahmen wieder her.

Der Aufruf von **Undelete Blob** für einen Blob, der nicht gelöscht wurde, hat keinerlei Auswirkung. Das folgende Beispiel ruft **Undelete Blob** für alle Blobs in einem Container auf und stellt die vorläufig gelöschten Blobs und die zugehörigen Momentaufnahmen wieder her:

```csharp
// Restore all blobs in a container.
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Wenn Sie eine spezifische Momentaufnahme wiederherstellen möchten, rufen Sie den Vorgang **Undelete Blob** zunächst für das Basisblob auf, und kopieren Sie die gewünschte Momentaufnahme über das Basisblob. Im folgenden Beispiel wird die zuletzt generierte Momentaufnahme eines Blockblobs wiederhergestellt:

```csharp
// Restore the block blob.
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container, prefixed by the blob name.
IEnumerable<IListBlobItem> allBlobSnapshots = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Copy the most recently generated snapshot to the base blob.
CloudBlockBlob copySource = allBlobSnapshots.First(snapshot => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)snapshot).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```

---

#### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>Wiederherstellen vorläufig gelöschter Objekte bei aktivierter Versionsverwaltung

Um ein vorläufig gelöschtes Blob bei aktivierter Versionsverwaltung wiederherzustellen, kopieren Sie mithilfe des Vorgangs [Copy Blob](/rest/api/storageservices/copy-blob) oder [Copy Blob From URL](/rest/api/storageservices/copy-blob-from-url) eine vorherige Version über das Basisblob.

##### <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RestorePreviousVersion":::

##### <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

Nicht zutreffend Die Blobversionsverwaltung wird nur in den Azure Storage-Clientbibliotheken der Version 12.x und höher unterstützt.

---

## <a name="manage-soft-deleted-blobs-and-directories-hierarchical-namespace"></a>Verwalten von vorläufig gelöschten Blobs und Verzeichnissen (hierarchischer Namespace)

Sie können vorläufig gelöschte Blobs und Verzeichnisse in Konten wiederherstellen, die über einen hierarchischen Namespace verfügen.

> [!IMPORTANT]
> Das vorläufige Löschen in Konten, für die die hierarchische Namespacefunktion aktiviert ist, befindet sich derzeit in der VORSCHAUPHASE und ist global in allen Azure-Regionen verfügbar.
> Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten rechtliche Bedingungen. Sie gelten für diejenigen Azure-Features, die sich in der Beta- oder Vorschauversion befinden oder aber anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
>
>
> Wie Sie sich für die Vorschau registrieren, erfahren Sie in [diesem Formular](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4mEEwKhLjlBjU3ziDwLH-pUOVRVOUpDRUtHVUtDUUtMVTZUR0tUMjZWNy4u).

### <a name="manage-soft-deleted-blobs-with-the-azure-portal"></a>Verwalten von vorläufig gelöschten Blobs mit dem Azure-Portal

Im Azure-Portal können Sie vorläufig gelöschte Blobs und Verzeichnisse anzeigen und wiederherstellen.

#### <a name="view-deleted-blobs-and-directories"></a>Anzeigen von gelöschten Blobs und Verzeichnissen

Wenn Blobs oder Verzeichnisse vorläufig gelöscht werden, sind sie im Azure-Portal standardmäßig nicht sichtbar. Um vorläufig gelöschte Blobs und Verzeichnisse anzuzeigen, navigieren Sie zur Seite **Übersicht** für den Container, und aktivieren Sie die Einstellung **Gelöschte Blobs anzeigen**. Vorläufig gelöschte Blobs und Verzeichnisse werden mit dem Status **Gelöscht** angezeigt. Die folgende Abbildung zeigt ein vorläufig gelöschtes Verzeichnis.

> [!div class="mx-imgBorder"]
> ![Screenshot zum Auflisten vorläufig gelöschter Blobs im Azure-Portal (Konten mit aktiviertem hierarchischem Namespace).](media/soft-delete-blob-manage/soft-deleted-blobs-list-portal-hns.png)

> [!NOTE]
> Wenn Sie ein Verzeichnis umbenennen, das vorläufig gelöschte Elemente (Unterverzeichnisse und Blobs) enthält, werden diese vorläufig gelöschten Elemente vom Verzeichnis getrennt, sodass sie im Azure-Portal nicht angezeigt werden, wenn Sie die Einstellung **Gelöschte Blobs anzeigen** umschalten. Wenn Sie sie im Azure-Portal anzeigen möchten, müssen Sie den Namen des Verzeichnisses wieder auf seinen ursprünglichen Namen zurücksetzen oder ein separates Verzeichnis mit dem ursprünglichen Verzeichnisnamen erstellen.

Wählen Sie als Nächstes das gelöschte Verzeichnis oder Blob in der Liste aus, um seine Eigenschaften anzuzeigen. Beachten Sie, dass auf der Registerkarte **Übersicht** der Status auf **Gelöscht** festgelegt ist. Das Portal zeigt außerdem an, in wie viel Tagen das Blob endgültig gelöscht wird.

> [!div class="mx-imgBorder"]
> ![Screenshot der Eigenschaften vorläufig gelöschter Blobs im Azure-Portal (Konten mit aktiviertem hierarchischem Namespace).](media/soft-delete-blob-manage/soft-deleted-blob-properties-portal-hns.png)

#### <a name="restore-soft-delete-blobs-and-directories"></a>Wiederherstellen von vorläufig gelöschten Blobs und Verzeichnissen

Um ein vorläufig gelöschtes Blob oder Verzeichnis im Azure-Portal wiederherzustellen, zeigen Sie zunächst die Eigenschaften des Blobs oder Verzeichnisses an, und wählen Sie dann auf der Registerkarte **Übersicht** die Schaltfläche **Wiederherstellen** aus. Die folgende Abbildung zeigt die Schaltfläche „Wiederherstellen“ für ein vorläufig gelöschtes Verzeichnis.

> [!div class="mx-imgBorder"]
> ![Screenshot zum Wiederherstellen eines vorläufig gelöschten Blobs im Azure-Portal (Konten mit aktiviertem hierarchischem Namespace).](media/soft-delete-blob-manage/undelete-soft-deleted-blob-portal-hns.png)

### <a name="restore-soft-deleted-blobs-and-directories-by-using-powershell"></a>Wiederherstellen vorläufig gelöschter Blobs und Verzeichnisse mithilfe von PowerShell

> [!IMPORTANT]
> Dieser Abschnitt gilt nur für Konten mit einem hierarchischen Namespace.

1. Stellen Sie sicher, dass das Vorschaumodul **Az.Storage** installiert ist. Weitere Informationen finden Sie unter [Aktivieren des vorläufigen Blob-Löschens über PowerShell](soft-delete-blob-enable.md?tabs=azure-powershell#enable-blob-soft-delete-hierarchical-namespace).

2. Abrufen der Speicherkontoautorisierung mithilfe eines Speicherkontoschlüssels, einer Verbindungszeichenfolge oder mit Azure Active Directory (Azure AD). Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit dem Konto](data-lake-storage-directory-file-acl-powershell.md#connect-to-the-account).

   Im folgenden Beispiel wird die Autorisierung mithilfe eines Speicherkontoschlüssels abgerufen.

   ```powershell
   $ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -StorageAccountKey '<storage-account-key>'
   ```

3. Verwenden Sie zum Wiederherstellen von vorläufig gelöschten Elementen den Befehl `Restore-AzDataLakeGen2DeletedItem`.

   ```powershell
   $filesystemName = "my-file-system"
   $dirName="my-directory"
   $deletedItems = Get-AzDataLakeGen2DeletedItem -Context $ctx -FileSystem $filesystemName -Path $dirName
   $deletedItems | Restore-AzDataLakeGen2DeletedItem
   ```

   Wenn Sie das Verzeichnis umbenennen, das die vorläufig gelöschten Elemente enthält, werden diese Elemente vom Verzeichnis getrennt. Wenn Sie diese Elemente wiederherstellen möchten, müssen Sie den Namen des Verzeichnisses wieder auf seinen ursprünglichen Namen zurücksetzen oder ein separates Verzeichnis mit dem ursprünglichen Verzeichnisnamen erstellen. Andernfalls erhalten Sie einen Fehler, wenn Sie versuchen, diese vorläufig gelöschten Elemente wiederherzustellen.

### <a name="restore-soft-deleted-blobs-and-directories-by-using-azure-cli"></a>Wiederherstellen vorläufig gelöschter Blobs und Verzeichnisse mithilfe der Azure-Befehlszeilenschnittstelle

> [!IMPORTANT]
> Dieser Abschnitt gilt nur für Konten mit einem hierarchischen Namespace.

1. Stellen Sie sicher, dass die Erweiterung `storage-preview` installiert ist. Weitere Informationen finden Sie unter [Aktivieren des vorläufigen Blob-Löschens über PowerShell](soft-delete-blob-enable.md?tabs=azure-CLI#enable-blob-soft-delete-hierarchical-namespace).

2. Rufen Sie die Liste der gelöschten Elemente ab.

   ```azurecli
   $filesystemName = "my-file-system"
   az storage fs list-deleted-path -f $filesystemName --auth-mode login
   ```

3. Verwenden Sie zum Wiederherstellen eines Elements den Befehl `az storage fs undelete-path`.

   ```azurecli
   $dirName="my-directory"
   az storage fs undelete-path -f $filesystemName --deleted-path-name $dirName -deletion-id "<deletionId>" --auth-mode login
   ```

   Wenn Sie das Verzeichnis umbenennen, das die vorläufig gelöschten Elemente enthält, werden diese Elemente vom Verzeichnis getrennt. Wenn Sie diese Elemente wiederherstellen möchten, müssen Sie den Namen des Verzeichnisses wieder auf seinen ursprünglichen Namen zurücksetzen oder ein separates Verzeichnis mit dem ursprünglichen Verzeichnisnamen erstellen. Andernfalls erhalten Sie einen Fehler, wenn Sie versuchen, diese vorläufig gelöschten Elemente wiederherzustellen.

### <a name="restore-soft-deleted-blobs-and-directories-by-using-net"></a>Wiederherstellen vorläufig gelöschter Blobs und Verzeichnisse mithilfe von .NET

> [!IMPORTANT]
> Dieser Abschnitt gilt nur für Konten mit einem hierarchischen Namespace.

1. Öffnen Sie eine Eingabeaufforderung, und wechseln Sie mit dem Befehl `cd` in Ihren Projektordner. Beispiel:

   ```console
   cd myProject
   ```

2. Installieren Sie Version `Azure.Storage.Files.DataLake -v 12.7.0` des NuGet-Pakets [Azure.Storage.Files.DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) mit dem Befehl `dotnet add package`.

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v -v 12.7.0 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

3. Fügen Sie dann die folgenden using-Anweisungen am Anfang Ihrer Codedatei ein.

    ```csharp
    using Azure;
    using Azure.Storage;
    using Azure.Storage.Files.DataLake;
    using Azure.Storage.Files.DataLake.Models;
    using NUnit.Framework;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    ```

4. Mit dem folgenden Code wird ein Verzeichnis gelöscht und anschließend ein vorläufig gelöschtes Verzeichnis wiederhergestellt.

   Bei dieser Methode wird davon ausgegangen, dass Sie eine [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient)-Instanz erstellt haben. Informationen zum Erstellen einer [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient)-Instanz finden Sie unter [Herstellen einer Verbindung mit dem Konto](data-lake-storage-directory-file-acl-dotnet.md#connect-to-the-account).

   ```csharp
      public void RestoreDirectory(DataLakeServiceClient serviceClient)
      {
          DataLakeFileSystemClient fileSystemClient =
             serviceClient.GetFileSystemClient("my-container");

          DataLakeDirectoryClient directory =
              fileSystem.GetDirectoryClient("my-directory");

          // Delete the Directory
          await directory.DeleteAsync();

          // List Deleted Paths
          List<PathHierarchyDeletedItem> deletedItems = new List<PathHierarchyDeletedItem>();
          await foreach (PathHierarchyDeletedItem deletedItem in fileSystemClient.GetDeletedPathsAsync())
          {
            deletedItems.Add(deletedItem);
          }

          Assert.AreEqual(1, deletedItems.Count);
          Assert.AreEqual("my-directory", deletedItems[0].Path.Name);
          Assert.IsTrue(deletedItems[0].IsPath);

          // Restore deleted directory.
          Response<DataLakePathClient> restoreResponse = await fileSystemClient.RestorePathAsync(
          deletedItems[0].Path.Name,
          deletedItems[0].Path.DeletionId);

      }

   ```

   Wenn Sie das Verzeichnis umbenennen, das die vorläufig gelöschten Elemente enthält, werden diese Elemente vom Verzeichnis getrennt. Wenn Sie diese Elemente wiederherstellen möchten, müssen Sie den Namen des Verzeichnisses wieder auf seinen ursprünglichen Namen zurücksetzen oder ein separates Verzeichnis mit dem ursprünglichen Verzeichnisnamen erstellen. Andernfalls erhalten Sie einen Fehler, wenn Sie versuchen, diese vorläufig gelöschten Elemente wiederherzustellen.

### <a name="restore-soft-deleted-blobs-and-directories-by-using-java"></a>Wiederherstellen vorläufig gelöschter Blobs und Verzeichnisse mithilfe von Java

> [!IMPORTANT]
> Dieser Abschnitt gilt nur für Konten mit einem hierarchischen Namespace.

1. Öffnen Sie zunächst die Datei *pom.xml* in Ihrem Text-Editor. Fügen Sie der Gruppe „dependencies“ das folgende Abhängigkeitselement hinzu.

   ```xml
   <dependency>
     <groupId>com.azure</groupId>
     <artifactId>azure-storage-file-datalake</artifactId>
     <version>12.6.0</version>
   </dependency>
   ```

2. Fügen Sie dann die folgenden Import-Anweisungen zu Ihrer Codedatei hinzu.

   ```java
   Put imports here
   ```

3. Mit dem folgenden Codeausschnitt wird eine vorläufig gelöschte Datei mit dem Namen `my-file` wiederhergestellt.

   Bei dieser Methode wird davon ausgegangen, dass Sie eine **DataLakeServiceClient**-Instanz erstellt haben. Informationen zum Erstellen einer **DataLakeServiceClient**-Instanz finden Sie unter [Herstellen einer Verbindung mit dem Konto](data-lake-storage-directory-file-acl-java.md#connect-to-the-account).

   ```java

   public void RestoreFile(DataLakeServiceClient serviceClient){

       DataLakeFileSystemClient fileSystemClient =
           serviceClient.getFileSystemClient("my-container");

       DataLakeFileClient fileClient =
           fileSystemClient.getFileClient("my-file");

       String deletionId = null;

       for (PathDeletedItem item : fileSystemClient.listDeletedPaths()) {

           if (item.getName().equals(fileClient.getFilePath())) {
              deletionId = item.getDeletionId();
           }
       }

       fileSystemClient.restorePath(fileClient.getFilePath(), deletionId);
    }

   ```

   Wenn Sie das Verzeichnis umbenennen, das die vorläufig gelöschten Elemente enthält, werden diese Elemente vom Verzeichnis getrennt. Wenn Sie diese Elemente wiederherstellen möchten, müssen Sie den Namen des Verzeichnisses wieder auf seinen ursprünglichen Namen zurücksetzen oder ein separates Verzeichnis mit dem ursprünglichen Verzeichnisnamen erstellen. Andernfalls erhalten Sie einen Fehler, wenn Sie versuchen, diese vorläufig gelöschten Elemente wiederherzustellen.

### <a name="restore-soft-deleted-blobs-and-directories-by-using-python"></a>Wiederherstellen vorläufig gelöschter Blobs und Verzeichnisse mithilfe von Python

> [!IMPORTANT]
> Dieser Abschnitt gilt nur für Konten mit einem hierarchischen Namespace.

1. Installieren Sie Version `12.4.0` oder höher der Azure Data Lake Storage-Clientbibliothek für Python mithilfe von [pip](https://pypi.org/project/pip/). Mit diesem Befehl wird die neueste Version der Azure Data Lake Storage-Clientbibliothek für Python installiert.

   ```
   pip install azure-storage-file-datalake
   ```

2. Fügen Sie diese Importanweisungen am Anfang Ihrer Codedatei hinzu.

   ```python
   import os, uuid, sys
   from azure.storage.filedatalake import DataLakeServiceClient
   from azure.storage.filedatalake import FileSystemClient
   ```

3. Mit dem folgenden Code wird ein Verzeichnis gelöscht und anschließend ein vorläufig gelöschtes Verzeichnis wiederhergestellt.

   Das folgende Codebeispiel enthält ein Objekt mit dem Namen `service_client` vom Typ **DataLakeServiceClient**. Beispiele für das Erstellen einer **DataLakeServiceClient**-Instanz finden Sie unter [Herstellen einer Verbindung mit dem Konto](data-lake-storage-directory-file-acl-python.md#connect-to-the-account).

    ```python
    def restoreDirectory():

        try:
            global file_system_client

            file_system_client = service_client.create_file_system(file_system="my-file-system")

            directory_path = 'my-directory'
            directory_client = file_system_client.create_directory(directory_path)
            resp = directory_client.delete_directory()

            restored_directory_client = file_system_client.undelete_path(directory_client, resp['deletion_id'])
            props = restored_directory_client.get_directory_properties()

            print(props)

        except Exception as e:
            print(e)

    ```

   Wenn Sie das Verzeichnis umbenennen, das die vorläufig gelöschten Elemente enthält, werden diese Elemente vom Verzeichnis getrennt. Wenn Sie diese Elemente wiederherstellen möchten, müssen Sie den Namen des Verzeichnisses wieder auf seinen ursprünglichen Namen zurücksetzen oder ein separates Verzeichnis mit dem ursprünglichen Verzeichnisnamen erstellen. Andernfalls erhalten Sie einen Fehler, wenn Sie versuchen, diese vorläufig gelöschten Elemente wiederherzustellen.

## <a name="next-steps"></a>Nächste Schritte

- [Vorläufiges Löschen für Blobspeicher](soft-delete-blob-overview.md)
- [Aktivieren von „Vorläufiges Löschen“ für Blobs](soft-delete-blob-enable.md)
- [Blobversionsverwaltung](versioning-overview.md)
