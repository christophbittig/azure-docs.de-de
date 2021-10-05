---
title: Verwenden von JavaScript (Node.js) zum Verwalten von ACLs in Azure Data Lake Storage Gen2
description: Verwenden Sie die Azure Storage Data Lake-Clientbibliothek für JavaScript, um Zugriffssteuerungslisten (Access Control Lists, ACLs) in Speicherkonten zu verwalten, für die der hierarchische Namespace (HNS) aktiviert ist.
author: normesta
ms.service: storage
ms.date: 03/19/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-js
ms.openlocfilehash: cf09d9956a2cc7cea6831b52df45abc7f0fbb1b1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128555449"
---
# <a name="use-javascript-sdk-in-nodejs-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Verwenden von JavaScript SDK in Node.js zum Verwalten von Zugriffssteuerungslisten in Azure Data Lake Storage Gen2

In diesem Artikel erfahren Sie, wie Sie mithilfe von Node.js die Zugriffssteuerungslisten von Verzeichnissen und Dateien abrufen, festlegen und aktualisieren.

[Paket (Node Package Manager)](https://www.npmjs.com/package/@azure/storage-file-datalake) | [Beispiele](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples) | [Feedback geben](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. Weitere Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

- Ein Speicherkonto, für das der hierarchische Namespace aktiviert ist. Befolgen Sie [diese Anleitung](create-data-lake-storage-account.md) für die Erstellung.

- Azure CLI, Version `2.6.0` oder höher.

- Eine der folgenden Sicherheitsberechtigungen:

  - Ein bereitgestellter [Sicherheitsprinzipal](../../role-based-access-control/overview.md#security-principal) für Azure Active Directory (AD), dem die Rolle [Speicherblob-Datenbesitzer](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) im Bereich des Zielcontainers, der übergeordneten Ressourcengruppe oder des Abonnements zugewiesen wurde

  - Der Benutzer muss Besitzer des Zielcontainers oder -verzeichnisses sein, in dem Sie die ACL-Einstellungen anwenden möchten. Beim rekursiven Festlegen von ACLs schließt dies alle untergeordneten Elemente im Zielcontainer oder -verzeichnis ein.

  - Speicherkontoschlüssel

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

### <a name="connect-by-using-azure-active-directory-ad"></a>Herstellen einer Verbindung mit Azure Active Directory (AD)

> [!NOTE]
> Wenn Sie Azure Active Directory (Azure AD) verwenden, um den Zugriff zu autorisieren, sollten Sie sicherstellen, dass Ihrem Sicherheitsprinzipal die Rolle [Besitzer von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) zugewiesen wurde. Weitere Informationen dazu, wie ACL-Berechtigungen angewendet werden und wie sich Änderungen daran auswirken, finden Sie unter [Zugriffssteuerungsmodell in Azure Data Lake Storage Gen2](./data-lake-storage-access-control-model.md).

Sie können die [Azure-Identitätsclientbibliothek für JS](https://www.npmjs.com/package/@azure/identity) verwenden, um Ihre Anwendung bei Azure AD zu authentifizieren.

Rufen Sie die Client-ID, ein Clientgeheimnis und die Mandanten-ID ab. Informationen zur Vorgehensweise finden Sie unter [Abrufen eines Tokens von Azure AD zum Autorisieren von Anforderungen von einer Clientanwendung](../common/storage-auth-aad-app.md). Im Rahmen dieses Prozesses müssen Sie dem Sicherheitsprinzipal eine der folgenden [Azure RBAC-Rollen](../../role-based-access-control/overview.md) (Azure Role-Based Access Control) zuweisen.

|Role|ACL-Einstellungsfunktion|
|--|--|
|[Besitzer von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Alle Verzeichnisse und Dateien im Konto|
|[Mitwirkender an Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Nur Verzeichnisse und Dateien im Besitz des Sicherheitsprinzipals|

In diesem Beispiel wird eine **DataLakeServiceClient**-Instanz mithilfe einer Client-ID, eines Clientgeheimnisses und einer Mandanten-ID erstellt.

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
> Diese Autorisierungsmethode funktioniert nur für Node.js-Anwendungen. Wenn Sie Ihren Code in einem Browser ausführen möchten, kann die Autorisierung über Azure Active Directory (AD) erfolgen.

## <a name="get-and-set-a-directory-acl"></a>Abrufen und Festlegen einer Verzeichnis-ACL

Im folgenden Beispiel wird die ACL (Zugriffssteuerungsliste) eines Verzeichnisses namens `my-directory` abgerufen und dann festgelegt. Im folgenden Beispiel werden dem zuständigen Benutzer Lese-, Schreib- und Ausführungsberechtigungen und der zuständigen Gruppe nur Lese- und Ausführungsberechtigungen gewährt, während allen anderen lediglich Lesezugriff gewährt wird.

> [!NOTE]
> Wenn in Ihrer Anwendung der Zugriff mithilfe von Azure Active Directory (Azure AD) autorisiert wird, müssen Sie sicherstellen, dass dem Sicherheitsprinzipal, der in der Anwendung zum Autorisieren des Zugriffs verwendet wird, die [Rolle „Besitzer von Speicherblobdaten“](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) zugewiesen ist. Weitere Informationen dazu, wie ACL-Berechtigungen angewandt werden und wie sich Änderungen daran auswirken, finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

```javascript
async function ManageDirectoryACLs(fileSystemClient) {

    const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
    const permissions = await directoryClient.getAccessControl();

    console.log(permissions.acl);

    const acl = [
    {
      accessControlType: "user",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: true
      }
    },
    {
      accessControlType: "group",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: false,
        execute: true
      }
    },
    {
      accessControlType: "other",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: false
      }

    }

  ];

  await directoryClient.setAccessControl(acl);
}
```

Sie können auch die ACL des Stammverzeichnisses eines Containers abrufen und festlegen. Übergeben Sie zum Abrufen des Stammverzeichnisses eine leere Zeichenfolge (`/`) an die Methode **DataLakeFileSystemClient.getDirectoryClient**.

### <a name="get-and-set-a-file-acl"></a>Abrufen und Festlegen einer Datei-ACL

Im folgenden Beispiel wird die ACL einer Datei namens `upload-file.txt` abgerufen und dann festgelegt. Im folgenden Beispiel werden dem zuständigen Benutzer Lese-, Schreib- und Ausführungsberechtigungen und der zuständigen Gruppe nur Lese- und Ausführungsberechtigungen gewährt, während allen anderen lediglich Lesezugriff gewährt wird.

> [!NOTE]
> Wenn in Ihrer Anwendung der Zugriff mithilfe von Azure Active Directory (Azure AD) autorisiert wird, müssen Sie sicherstellen, dass dem Sicherheitsprinzipal, der in der Anwendung zum Autorisieren des Zugriffs verwendet wird, die [Rolle „Besitzer von Speicherblobdaten“](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) zugewiesen ist. Weitere Informationen dazu, wie ACL-Berechtigungen angewandt werden und wie sich Änderungen daran auswirken, finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

```javascript
async function ManageFileACLs(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");
  const permissions = await fileClient.getAccessControl();

  console.log(permissions.acl);

  const acl = [
  {
    accessControlType: "user",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: true
    }
  },
  {
    accessControlType: "group",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: false,
      execute: true
    }
  },
  {
    accessControlType: "other",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: false
    }

  }

];

await fileClient.setAccessControl(acl);
}
```

## <a name="see-also"></a>Weitere Informationen

- [Paket (Node Package Manager)](https://www.npmjs.com/package/@azure/storage-file-datalake)
- [Beispiele](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
- [Feedback geben](https://github.com/Azure/azure-sdk-for-java/issues)
- [Zugriffssteuerungsmodell in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Zugriffssteuerungslisten (ACLs) in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
