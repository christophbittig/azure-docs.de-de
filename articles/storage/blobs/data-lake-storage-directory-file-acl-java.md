---
title: Verwenden von Java zum Verwalten von Daten in Azure Data Lake Storage Gen2
description: Verwenden Sie die Azure Storage-Bibliotheken für Java, um Verzeichnisse und Dateien in Speicherkonten zu verwalten, für die der hierarchische Namespace aktiviert ist.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.custom: devx-track-java
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 25002640a81b260ced61c25e2d157a100d6dbc2a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593399"
---
# <a name="use-java-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Verwenden von Java zum Verwalten von Verzeichnissen und Dateien in Azure Data Lake Storage Gen2

In diesem Artikel erfahren Sie, wie Sie mithilfe von Java Verzeichnisse und Dateien in Speicherkonten erstellen und verwalten, die über einen hierarchischen Namespace verfügen.

Informationen zum Abrufen, Festlegen und Aktualisieren der Zugriffssteuerungslisten (Access Control Lists, ACLs) von Verzeichnissen und Dateien finden Sie unter [Verwenden von Java zum Verwalten von Zugriffssteuerungslisten in Azure Data Lake Storage Gen2](data-lake-storage-acl-java.md).

[Maven-Paket](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) | [Beispiele](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake) | [API-Referenz](/java/api/overview/azure/storage-file-datalake-readme) | [Zuordnung von Gen1 zu Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [Feedback geben](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. Weitere Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

- Ein Speicherkonto, für das der hierarchische Namespace aktiviert ist. Befolgen Sie [diese Anleitung](create-data-lake-storage-account.md) für die Erstellung.

## <a name="set-up-your-project"></a>Einrichten des Projekts

Öffnen Sie zunächst [diese Seite](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake), und suchen Sie die neueste Version der Java-Bibliothek. Öffnen Sie dann die Datei *pom.xml* in Ihrem Text-Editor. Fügen Sie ein Abhängigkeitselement hinzu, das auf diese Version verweist.

Wenn Sie beabsichtigen, die Clientanwendung mithilfe von Azure Active Directory (Azure AD) zu authentifizieren, fügen Sie der Azure-Geheimnisclientbibliothek eine Abhängigkeit hinzu. Informationen dazu finden Sie unter [Hinzufügen des Pakets für die Geheimnisclientbibliothek zu Ihrem Projekt](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project).

Fügen Sie als Nächstes Ihrer Codedatei die folgenden Import-Anweisungen hinzu.

```java
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.AccessControlChangeCounters;
import com.azure.storage.file.datalake.models.AccessControlChangeResult;
import com.azure.storage.file.datalake.models.AccessControlType;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.PathRemoveAccessControlEntry;
import com.azure.storage.file.datalake.models.RolePermissions;
import com.azure.storage.file.datalake.options.PathSetAccessControlRecursiveOptions;
```

## <a name="connect-to-the-account"></a>Herstellen einer Verbindung mit dem Konto

Wenn Sie die Codeausschnitte in diesem Artikel verwenden möchten, müssen Sie eine **DataLakeServiceClient**-Instanz erstellen, die das Speicherkonto darstellt.

### <a name="connect-by-using-an-account-key"></a>Herstellen einer Verbindung per Kontoschlüssel

Dies ist die einfachste Möglichkeit, eine Verbindung mit einem Konto herzustellen.

In diesem Beispiel wird eine **DataLakeServiceClient**-Instanz mithilfe eines Kontoschlüssels erstellt.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithKey":::

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Herstellen einer Verbindung mit Azure Active Directory (Azure AD)

Sie können die [Azure-Identitätsclientbibliothek für Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) verwenden, um Ihre Anwendung bei Azure AD zu authentifizieren.

In diesem Beispiel wird eine **DataLakeServiceClient**-Instanz mithilfe einer Client-ID, eines Clientgeheimnisses und einer Mandanten-ID erstellt. Informationen zum Abrufen dieser Werte finden Sie unter [Abrufen eines Tokens von Azure AD zum Autorisieren von Anforderungen von einer Clientanwendung](../common/storage-auth-aad-app.md).

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithAzureAD":::

> [!NOTE]
> Weitere Beispiele finden Sie in der Dokumentation zur [Azure-Identitätsclientbibliothek für Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity).

## <a name="create-a-container"></a>Erstellen eines Containers

Ein Container fungiert als Dateisystem für Ihre Dateien. Sie können eines erstellen, indem Sie die Methode **DataLakeServiceClient.createFileSystem** aufrufen.

In diesem Beispiel wird ein Container namens `my-file-system` erstellt.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_CreateFileSystem":::

## <a name="create-a-directory"></a>Erstellen eines Verzeichnisses

Erstellen Sie eine Verzeichnisreferenz, indem Sie die Methode **DataLakeFileSystemClient.createDirectory** aufrufen.

In diesem Beispiel wird einem Container ein Verzeichnis namens `my-directory` und anschließend ein Unterverzeichnis namens `my-subdirectory` hinzugefügt.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>Umbenennen oder Verschieben eines Verzeichnisses

Rufen Sie die Methode **DataLakeDirectoryClient.rename** auf, um ein Verzeichnis umzubenennen oder zu verschieben. Übergeben Sie den Pfad des gewünschten Verzeichnisses als Parameter.

Im folgenden Beispiel wird ein Unterverzeichnis in `my-subdirectory-renamed` umbenannt.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_RenameDirectory":::

Im folgenden Beispiel wird ein Verzeichnis namens `my-subdirectory-renamed` in ein Unterverzeichnis eines Verzeichnisses namens `my-directory-2` verschoben.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_MoveDirectory":::

## <a name="delete-a-directory"></a>Löschen eines Verzeichnisses

Sie können ein Verzeichnis löschen, indem Sie die Methode **DataLakeDirectoryClient.deleteWithResponse** aufrufen.

In diesem Beispiel wird das Verzeichnis `my-directory` gelöscht.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>Hochladen einer Datei in ein Verzeichnis

Erstellen Sie zunächst eine Dateireferenz im Zielverzeichnis, in dem Sie eine Instanz der Klasse **DataLakeFileClient** erstellen. Laden Sie eine Datei hoch, indem Sie die Methode **DataLakeFileClient.append** aufrufen. Stellen Sie sicher, dass Sie den Uploadvorgang abschließen, indem Sie die Methode **DataLakeFileClient.FlushAsync** aufrufen.

In diesem Beispiel wird eine Textdatei in das Verzeichnis `my-directory` hochgeladen.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_UploadFile":::

> [!TIP]
> Wenn Ihre Datei groß ist, muss Ihr Code mehrere Aufrufe der **DataLakeFileClient.append**-Methode durchführen. Erwägen Sie stattdessen die Verwendung der **DataLakeFileClient.uploadFromFile**-Methode. Auf diese Weise können Sie die gesamte Datei in einem einzigen Aufruf hochladen.
>
> Ein Beispiel finden Sie im nächsten Abschnitt.

## <a name="upload-a-large-file-to-a-directory"></a>Hochladen einer großen Datei in ein Verzeichnis

Verwenden Sie die **DataLakeFileClient.uploadFromFile**-Methode, um große Dateien hochzuladen, ohne die **DataLakeFileClient.append**-Methode mehrfach aufrufen zu müssen.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>Herunterladen aus einem Verzeichnis

Erstellen Sie zunächst eine **DataLakeFileClient**-Instanz, die die herunterzuladende Datei darstellt. Verwenden Sie die Methode **DataLakeFileClient.read**, um die Datei zu lesen. Verwenden Sie eine beliebige .NET-API für die Dateiverarbeitung, um Bytes aus dem Datenstrom in einer Datei zu speichern.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_DownloadFile":::

## <a name="list-directory-contents"></a>Auflisten des Verzeichnisinhalts

Im folgenden Beispiel werden die Namen aller Dateien ausgegeben, die sich in einem Verzeichnis namens `my-directory` befinden.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_ListFilesInDirectory":::

## <a name="see-also"></a>Weitere Informationen

- [API-Referenzdokumentation](/java/api/overview/azure/storage-file-datalake-readme)
- [Maven-Paket](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
- [Beispiele](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
- [Zuordnung von Gen1 zu Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [Bekannte Probleme](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Feedback geben](https://github.com/Azure/azure-sdk-for-java/issues)
