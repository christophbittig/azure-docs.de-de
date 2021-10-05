---
title: Verwenden von JavaScript (Node.js) zum Verwalten von Daten in Azure Data Lake Storage Gen2
description: Verwenden Sie die Azure Storage Data Lake-Clientbibliothek für JavaScript, um Verzeichnisse und Dateien in Speicherkonten zu verwalten, für die der hierarchische Namespace aktiviert ist.
author: normesta
ms.service: storage
ms.date: 03/19/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-js
ms.openlocfilehash: b7e753f248e9de1c2812cabed5c1cf432e726f59
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593209"
---
# <a name="use-javascript-sdk-in-nodejs-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Verwenden des JavaScript-SDK in Node.js zum Verwalten von Verzeichnissen und Dateien in Azure Data Lake Storage Gen2

In diesem Artikel erfahren Sie, wie Sie mithilfe von Node.js Verzeichnisse und Dateien in Speicherkonten erstellen und verwalten, die über einen hierarchischen Namespace verfügen.

Informationen zum Abrufen, Festlegen und Aktualisieren der Zugriffssteuerungslisten (Access Control Lists, ACLs) von Verzeichnissen und Dateien finden Sie unter [Verwenden von JavaScript SDK in Node.js zum Verwalten von Zugriffssteuerungslisten in Azure Data Lake Storage Gen2](data-lake-storage-acl-javascript.md).

[Paket (Node Package Manager)](https://www.npmjs.com/package/@azure/storage-file-datalake) | [Beispiele](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples) | [Feedback geben](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. Weitere Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

- Ein Speicherkonto, für das der hierarchische Namespace aktiviert ist. Befolgen Sie [diese Anleitung](create-data-lake-storage-account.md) für die Erstellung.

- Wenn Sie dieses Paket in einer Node.js-Anwendung verwenden, benötigen Sie Node.js 8.0.0 oder höher.

## <a name="set-up-your-project"></a>Einrichten des Projekts

Installieren Sie die Data Lake-Clientbibliothek für JavaScript, indem Sie ein Terminalfenster öffnen und den folgenden Befehl eingeben.

```javascript
npm install @azure/storage-file-datalake
```

Importieren Sie das `storage-file-datalake`-Paket, indem Sie die folgende Anweisung oben in Ihrer Codedatei einfügen.

```javascript
const {
AzureStorageDataLake,
DataLakeServiceClient,
StorageSharedKeyCredential
} = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>Herstellen einer Verbindung mit dem Konto

Wenn Sie die Codeausschnitte in diesem Artikel verwenden möchten, müssen Sie eine **DataLakeServiceClient**-Instanz erstellen, die das Speicherkonto darstellt.

### <a name="connect-by-using-an-account-key"></a>Herstellen einer Verbindung per Kontoschlüssel

Dies ist die einfachste Möglichkeit, eine Verbindung mit einem Konto herzustellen.

In diesem Beispiel wird eine **DataLakeServiceClient**-Instanz mithilfe eines Kontoschlüssels erstellt.

```javascript

function GetDataLakeServiceClient(accountName, accountKey) {

  const sharedKeyCredential =
     new StorageSharedKeyCredential(accountName, accountKey);

  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, sharedKeyCredential);

  return datalakeServiceClient;
}

```

> [!NOTE]
> Diese Autorisierungsmethode funktioniert nur für Node.js-Anwendungen. Wenn Sie Ihren Code in einem Browser ausführen möchten, kann die Autorisierung über Azure Active Directory (Azure AD) erfolgen.

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Herstellen einer Verbindung mit Azure Active Directory (Azure AD)

Sie können die [Azure-Identitätsclientbibliothek für JS](https://www.npmjs.com/package/@azure/identity) verwenden, um Ihre Anwendung bei Azure AD zu authentifizieren.

In diesem Beispiel wird eine **DataLakeServiceClient**-Instanz mithilfe einer Client-ID, eines Clientgeheimnisses und einer Mandanten-ID erstellt. Informationen zum Abrufen dieser Werte finden Sie unter [Abrufen eines Tokens von Azure AD zum Autorisieren von Anforderungen von einer Clientanwendung](../common/storage-auth-aad-app.md).

```javascript
function GetDataLakeServiceClientAD(accountName, clientID, clientSecret, tenantID) {

  const credential = new ClientSecretCredential(tenantID, clientID, clientSecret);

  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, credential);

  return datalakeServiceClient;
}
```

> [!NOTE]
> Weitere Beispiele finden Sie in der Dokumentation zur [Azure-Identitätsclientbibliothek für JS](https://www.npmjs.com/package/@azure/identity).

## <a name="create-a-container"></a>Erstellen eines Containers

Ein Container fungiert als Dateisystem für Ihre Dateien. Sie können ein Dateisystem erstellen, indem Sie eine **FileSystemClient**-Instanz abrufen und dann die Methode **FileSystemClient.Create** aufrufen.

In diesem Beispiel wird ein Container namens `my-file-system` erstellt.

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";

  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();

}
```

## <a name="create-a-directory"></a>Erstellen eines Verzeichnisses

Erstellen Sie einen Verzeichnisverweis, indem Sie eine **DirectoryClient**-Instanz abrufen und dann die Methode **DirectoryClient.create** aufrufen.

In diesem Beispiel wird einem Container das Verzeichnis `my-directory` hinzugefügt.

```javascript
async function CreateDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");

  await directoryClient.create();

}
```

## <a name="rename-or-move-a-directory"></a>Umbenennen oder Verschieben eines Verzeichnisses

Rufen Sie die Methode **DirectoryClient.rename** auf, um ein Verzeichnis umzubenennen oder zu verschieben. Übergeben Sie den Pfad des gewünschten Verzeichnisses als Parameter.

Im folgenden Beispiel wird ein Unterverzeichnis in `my-directory-renamed` umbenannt.

```javascript
async function RenameDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  await directoryClient.move("my-directory-renamed");

}
```

Im folgenden Beispiel wird ein Verzeichnis namens `my-directory-renamed` in ein Unterverzeichnis eines Verzeichnisses namens `my-directory-2` verschoben.

```javascript
async function MoveDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory-renamed");
  await directoryClient.move("my-directory-2/my-directory-renamed");

}
```

## <a name="delete-a-directory"></a>Löschen eines Verzeichnisses

Löschen Sie ein Verzeichnis, indem Sie die Methode **DirectoryClient.delete** aufrufen.

In diesem Beispiel wird das Verzeichnis `my-directory` gelöscht.

```javascript
async function DeleteDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  await directoryClient.delete();

}
```

## <a name="upload-a-file-to-a-directory"></a>Hochladen einer Datei in ein Verzeichnis

Lesen Sie zunächst eine Datei. Dieses Beispiel verwendet das Node.js-Modul `fs`. Erstellen Sie dann einen Dateiverweis im Zielverzeichnis, indem Sie eine **FileClient**-Instanz erstellen und dann die Methode **FileClient.create** aufrufen. Laden Sie eine Datei hoch, indem Sie die Methode **FileClient.append** aufrufen. Schließen Sie den Uploadvorgang durch Aufrufen der Methode **FileClient.flush** ab.

Im folgenden Beispiel wird eine Textdatei in ein Verzeichnis namens `my-directory` hochgeladen.

```javascript
async function UploadFile(fileSystemClient) {

  const fs = require('fs')

  var content = "";

  fs.readFile('mytestfile.txt', (err, data) => {
      if (err) throw err;

      content = data.toString();

  })

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");
  await fileClient.create();
  await fileClient.append(content, 0, content.length);
  await fileClient.flush(content.length);

}
```

## <a name="download-from-a-directory"></a>Herunterladen aus einem Verzeichnis

Erstellen Sie zunächst eine **FileSystemClient**-Instanz, die die herunterzuladende Datei repräsentiert. Verwenden Sie die Methode **FileSystemClient.read**, um die Datei zu lesen. Schreiben Sie dann die Datei. Dieses Beispiel verwendet hierfür das Node.js-Modul `fs`.

> [!NOTE]
> Diese Methode zum Herunterladen einer Datei funktioniert nur für Node.js-Anwendungen. Wenn Sie Ihren Code in einem Browser ausführen möchten, finden Sie ein Beispiel dafür in der Readme-Datei [Azure Storage File Data Lake client library for JavaScript](https://www.npmjs.com/package/@azure/storage-file-datalake) (Azure Data Lake Storage-Clientbibliothek für JavaScript).

```javascript
async function DownloadFile(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");

  const downloadResponse = await fileClient.read();

  const downloaded = await streamToString(downloadResponse.readableStreamBody);

  async function streamToString(readableStream) {
    return new Promise((resolve, reject) => {
      const chunks = [];
      readableStream.on("data", (data) => {
        chunks.push(data.toString());
      });
      readableStream.on("end", () => {
        resolve(chunks.join(""));
      });
      readableStream.on("error", reject);
    });
  }

  const fs = require('fs');

  fs.writeFile('mytestfiledownloaded.txt', downloaded, (err) => {
    if (err) throw err;
  });
}

```

## <a name="list-directory-contents"></a>Auflisten des Verzeichnisinhalts

Dieses Beispiel gibt die Namen aller Verzeichnisse und Dateien aus, die im Verzeichnis `my-directory` gespeichert sind.

```javascript
async function ListFilesInDirectory(fileSystemClient) {

  let i = 1;

  let iter = await fileSystemClient.listPaths({path: "my-directory", recursive: true});

  for await (const path of iter) {

    console.log(`Path ${i++}: ${path.name}, is directory: ${path.isDirectory}`);
  }

}
```

## <a name="see-also"></a>Weitere Informationen

- [Paket (Node Package Manager)](https://www.npmjs.com/package/@azure/storage-file-datalake)
- [Beispiele](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
- [Feedback geben](https://github.com/Azure/azure-sdk-for-java/issues)
