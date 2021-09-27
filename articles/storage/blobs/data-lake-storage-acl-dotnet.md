---
title: Verwenden von .NET zum Verwalten von Zugriffssteuerungslisten in Azure Data Lake Storage Gen2
description: Verwenden Sie .NET, um Zugriffssteuerungslisten (Access Control Lists, ACLs) in Speicherkonten zu verwalten, für die der hierarchische Namespace (HNS) aktiviert ist.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: c3f0d0dd82201a3a339f05929ffefa988e1c8692
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128564940"
---
# <a name="use-net-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Verwenden von .NET zum Verwalten von Zugriffssteuerungslisten in Azure Data Lake Storage Gen2

In diesem Artikel erfahren Sie, wie Sie mithilfe von .NET die Zugriffssteuerungslisten von Verzeichnissen und Dateien abrufen, festlegen und aktualisieren.

Die Vererbung von Zugriffssteuerungslisten (ACLs) ist für neue untergeordnete Elemente, die unter einem übergeordneten Verzeichnis erstellt werden, bereits verfügbar. Sie können Zugriffssteuerungslisten aber auch rekursiv für die vorhandenen untergeordneten Elemente eines übergeordneten Verzeichnisses hinzufügen, aktualisieren und entfernen, ohne diese Änderungen für jedes untergeordnete Element einzeln vornehmen zu müssen.

[Paket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake) | [Beispiele](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake) | [Rekursives ACL-Beispiel](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0) | [API-Referenz](/dotnet/api/azure.storage.files.datalake) | [Zuordnung von Gen1 zu Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md) | [Feedback geben](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

- Ein Speicherkonto, für das der hierarchische Namespace aktiviert ist. Befolgen Sie [diese Anleitung](create-data-lake-storage-account.md) für die Erstellung.

- Azure CLI, Version `2.6.0` oder höher.

- Eine der folgenden Sicherheitsberechtigungen:

  - Ein bereitgestellter [Sicherheitsprinzipal](../../role-based-access-control/overview.md#security-principal) für Azure Active Directory (AD), dem die Rolle [Speicherblob-Datenbesitzer](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) im Bereich des Zielcontainers, der übergeordneten Ressourcengruppe oder des Abonnements zugewiesen wurde

  - Der Benutzer muss Besitzer des Zielcontainers oder -verzeichnisses sein, in dem Sie die ACL-Einstellungen anwenden möchten. Beim rekursiven Festlegen von ACLs schließt dies alle untergeordneten Elemente im Zielcontainer oder -verzeichnis ein.

  - Speicherkontoschlüssel

## <a name="set-up-your-project"></a>Einrichten des Projekts

Installieren Sie zunächst das NuGet-Paket [Azure.Storage.Files.DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/).

1. Öffnen Sie ein Befehlsfenster (z. B. Windows PowerShell).

2. Installieren Sie das Vorschaupaket „Azure.Storage.Files.DataLake“ aus Ihrem Projektverzeichnis mit dem Befehl `dotnet add package`.

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v 12.6.0 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

   Fügen Sie dann die folgenden using-Anweisungen am Anfang Ihrer Codedatei ein.

   ```csharp
   using Azure;
   using Azure.Core;
   using Azure.Storage;
   using Azure.Storage.Files.DataLake;
   using Azure.Storage.Files.DataLake.Models;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   ```

## <a name="connect-to-the-account"></a>Herstellen einer Verbindung mit dem Konto

Wenn Sie die Codeausschnitte in diesem Artikel verwenden möchten, müssen Sie eine [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient)-Instanz erstellen, die das Speicherkonto darstellt.

### <a name="connect-by-using-azure-active-directory-ad"></a>Herstellen einer Verbindung mit Azure Active Directory (AD)

> [!NOTE]
> Wenn Sie Azure Active Directory (Azure AD) verwenden, um den Zugriff zu autorisieren, sollten Sie sicherstellen, dass Ihrem Sicherheitsprinzipal die Rolle [Besitzer von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) zugewiesen wurde. Weitere Informationen dazu, wie ACL-Berechtigungen angewendet werden und wie sich Änderungen daran auswirken, finden Sie unter [Zugriffssteuerungsmodell in Azure Data Lake Storage Gen2](./data-lake-storage-access-control-model.md).

Sie können die [Azure-Identitätsclientbibliothek für .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) verwenden, um Ihre Anwendung bei Azure AD zu authentifizieren.

Nachdem Sie das Paket installiert haben, fügen Sie am Anfang der Codedatei diese using-Anweisung hinzu.

```csharp
using Azure.Identity;
```

Rufen Sie die Client-ID, ein Clientgeheimnis und die Mandanten-ID ab. Informationen zur Vorgehensweise finden Sie unter [Abrufen eines Tokens von Azure AD zum Autorisieren von Anforderungen von einer Clientanwendung](../common/storage-auth-aad-app.md). Im Rahmen dieses Prozesses müssen Sie dem Sicherheitsprinzipal eine der folgenden [Azure RBAC-Rollen](../../role-based-access-control/overview.md) (Azure Role-Based Access Control) zuweisen.

|Role|ACL-Einstellungsfunktion|
|--|--|
|[Besitzer von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Alle Verzeichnisse und Dateien im Konto|
|[Mitwirkender an Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Nur Verzeichnisse und Dateien im Besitz des Sicherheitsprinzipals|

In diesem Beispiel wird eine [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient)-Instanz mithilfe einer Client-ID, eines Clientgeheimnisses und einer Mandanten-ID erstellt.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Weitere Beispiele finden Sie in der Dokumentation zur [Azure-Identitätsclientbibliothek für .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity).

### <a name="connect-by-using-an-account-key"></a>Herstellen einer Verbindung per Kontoschlüssel

Dies ist die einfachste Möglichkeit, eine Verbindung mit einem Konto herzustellen.

In diesem Beispiel wird eine [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient)-Instanz mithilfe eines Kontoschlüssels erstellt.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithKey":::

## <a name="set-acls"></a>Festlegen von ACLs

Wenn Sie eine Zugriffssteuerungsliste *festlegen*, **ersetzen** Sie die gesamte Zugriffssteuerungsliste einschließlich aller Einträge. Wenn Sie die Berechtigungsebene eines Sicherheitsprinzipals ändern oder der Zugriffssteuerungsliste einen neuen Sicherheitsprinzipal hinzufügen möchten, ohne dass sich dies auf bereits vorhandene Einträge auswirkt, sollten Sie die Zugriffssteuerungsliste stattdessen *aktualisieren*. Informationen dazu, wie eine Zugriffssteuerungsliste aktualisiert statt ersetzt wird, finden Sie im Abschnitt [Aktualisieren von ACLs](#update-acls) in diesem Artikel.

Wenn Sie die Zugriffssteuerungsliste *festlegen*, müssen Sie einen Eintrag für den besitzenden Benutzer, einen Eintrag für die besitzende Gruppe und einen Eintrag für alle anderen Benutzer hinzufügen. Weitere Informationen zum besitzenden Benutzer, zur besitzenden Gruppe und zu allen anderen Benutzern finden Sie unter [Benutzer und Identitäten](data-lake-storage-access-control.md#users-and-identities).

In diesem Abschnitt erfahren Sie Folgendes:

- Festlegen der ACL eines Verzeichnisses
- Festlegen der ACL einer Datei
- Rekursives Festlegen von ACLs

### <a name="set-the-acl-of-a-directory"></a>Festlegen der ACL eines Verzeichnisses

Rufen Sie die Zugriffssteuerungsliste (Access Control List, ACL) eines Verzeichnisses ab, indem Sie die Methode [DataLakeDirectoryClient.GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) aufrufen, und legen Sie die ACL durch Aufrufen der Methode [DataLakeDirectoryClient.SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) fest.

In diesem Beispiel wird die ACL des Verzeichnisses `my-directory` abgerufen und dann festgelegt. Mit der Zeichenfolge `user::rwx,group::r-x,other::rw-` werden dem zuständigen Benutzer Lese-, Schreib- und Ausführungsberechtigungen und der zuständigen Gruppe nur Lese- und Ausführungsberechtigungen gewährt, während allen anderen lediglich Lese- und Schreibzugriff gewährt wird.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ACLDirectory":::

Sie können auch die ACL des Stammverzeichnisses eines Containers abrufen und festlegen. Übergeben Sie zum Abrufen des Stammverzeichnisses eine leere Zeichenfolge (`""`) an die Methode [DataLakeFileSystemClient.GetDirectoryClient](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getdirectoryclient).

### <a name="set-the-acl-of-a-file"></a>Festlegen der ACL einer Datei

Rufen Sie die Zugriffssteuerungsliste (Access Control List, ACL) einer Datei ab, indem Sie die Methode [DataLakeFileClient.GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) aufrufen, und legen Sie die ACL durch Aufrufen der Methode [DataLakeFileClient.SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist) fest.

Im folgenden Beispiel wird die ACL der Datei `my-file.txt` abgerufen und dann festgelegt. Mit der Zeichenfolge `user::rwx,group::r-x,other::rw-` werden dem zuständigen Benutzer Lese-, Schreib- und Ausführungsberechtigungen und der zuständigen Gruppe nur Lese- und Ausführungsberechtigungen gewährt, während allen anderen lediglich Lese- und Schreibzugriff gewährt wird.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_FileACL":::

### <a name="set-acls-recursively"></a>Rekursives Festlegen von ACLs

Sie legen Zugriffssteuerungslisten rekursiv fest, indem Sie die **DataLakeDirectoryClient.SetAccessControlRecursiveAsync**-Methode aufrufen. Übergeben Sie dieser Methode eine [Liste](/dotnet/api/system.collections.generic.list-1) der [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Jedes [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)-Element definiert einen ACL-Eintrag.

Wenn Sie einen **standardmäßigen** ACL-Eintrag festlegen möchten, dann können Sie die Eigenschaft [PathAccessControlItem.DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) der Eigenschaft [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) auf **true** festlegen.

In diesem Beispiel wird die ACL des Verzeichnisses `my-parent-directory` festgelegt. Diese Methode akzeptiert einen booleschen Parameter namens `isDefaultScope`, der angibt, ob die Standard-ACL festgelegt werden soll. Dieser Parameter wird im Konstruktor von [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) verwendet. Mit den Einträgen der ACL werden dem zuständigen Benutzer Lese-, Schreib- und Ausführungsberechtigungen und der zuständigen Gruppe nur Lese- und Ausführungsberechtigungen gewährt, während allen anderen kein Zugriff gewährt wird. Der letzte ACL-Eintrag in diesem Beispiel gewährt einem bestimmten Benutzer mit der Objekt-ID „xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx“ Lese- und Ausführungsberechtigungen.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_SetACLRecursively":::

Ein Beispiel, das Zugriffssteuerungslisten durch Angabe einer Batchgröße rekursiv in Batches festlegt, finden Sie im [.NET-Beispiel](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

## <a name="update-acls"></a>Aktualisieren von ACLs

Wenn Sie eine Zugriffssteuerungsliste *aktualisieren*, ändern Sie die Zugriffssteuerungsliste, anstatt sie zu ersetzen. Beispielsweise können Sie der Zugriffssteuerungsliste einen neuen Sicherheitsprinzipal hinzufügen, ohne dass sich dies auf andere Sicherheitsprinzipale auswirkt, die in der Zugriffssteuerungsliste aufgelistet sind.  Informationen dazu, wie eine Zugriffssteuerungsliste ersetzt statt aktualisiert wird, finden Sie im Abschnitt [Festlegen von Zugriffssteuerungslisten](#set-acls) in diesem Artikel.

In diesem Abschnitt erfahren Sie Folgendes:

- Aktualisieren einer Zugriffssteuerungsliste (ACL)
- Rekursives Aktualisieren von Zugriffssteuerungslisten

### <a name="update-an-acl"></a>Aktualisieren einer Zugriffssteuerungsliste (ACL)

Rufen Sie zuerst die Zugriffssteuerungsliste eines Verzeichnisses ab, indem Sie die [DataLakeDirectoryClient.GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync)-Methode aufrufen. Kopieren Sie die Liste der ACL-Einträge in eine neue [Liste](/dotnet/api/system.collections.generic.list-1) von [PathAccessControl](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrol)-Objekten. Suchen Sie dann den Eintrag, den Sie aktualisieren möchten, und ersetzen Sie ihn in der Liste. Legen Sie die Zugriffssteuerungsliste fest, indem Sie die [DataLakeDirectoryClient.SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist)-Methode aufrufen.

In diesem Beispiel wird die Stamm-ACL eines Containers aktualisiert, indem der ACL-Eintrag für alle anderen Benutzer ersetzt wird.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_UpdateACL":::

### <a name="update-acls-recursively"></a>Rekursives Aktualisieren von Zugriffssteuerungslisten

Um eine Zugriffssteuerungsliste rekursiv zu aktualisieren, erstellen Sie ein neues ACL-Objekt mit dem ACL-Eintrag, den Sie aktualisieren möchten, und verwenden Sie dieses Objekt dann im Aktualisierungsvorgang der Zugriffssteuerungsliste. Rufen Sie nicht die vorhandene Zugriffssteuerungsliste ab, stellen Sie lediglich die zu aktualisierenden Zugriffssteuerungslisteneinträge bereit.

Sie aktualisieren eine Zugriffssteuerungsliste rekursiv, indem Sie die **DataLakeDirectoryClient.UpdateAccessControlRecursiveAsync**-Methode aufrufen.  Übergeben Sie dieser Methode eine [Liste](/dotnet/api/system.collections.generic.list-1) der [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Jedes [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)-Element definiert einen ACL-Eintrag.

Wenn Sie einen **standardmäßigen** ACL-Eintrag aktualisieren möchten, dann können Sie die Eigenschaft [PathAccessControlItem.DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) der Eigenschaft [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) auf **true** festlegen.

Dieses Beispiel aktualisiert einen ACL-Eintrag mit Schreibberechtigung. Diese Methode akzeptiert einen booleschen Parameter namens `isDefaultScope`, der angibt, ob die Standard-ACL aktualisiert werden soll. Dieser Parameter wird im Konstruktor von [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) verwendet.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_UpdateACLsRecursively":::

Ein Beispiel, das Zugriffssteuerungslisten durch Angabe einer Batchgröße rekursiv in Batches aktualisiert, finden Sie im [.NET-Beispiel](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

## <a name="remove-acl-entries"></a>Entfernen von ACL-Einträgen

Sie können einen oder mehrere ACL-Einträge entfernen. In diesem Abschnitt erfahren Sie Folgendes:

- Entfernen eines ACL-Eintrags
- Rekursives Entfernen von ACL-Einträgen

### <a name="remove-an-acl-entry"></a>Entfernen eines ACL-Eintrags

Rufen Sie zuerst die Zugriffssteuerungsliste eines Verzeichnisses ab, indem Sie die [DataLakeDirectoryClient.GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync)-Methode aufrufen. Kopieren Sie die Liste der ACL-Einträge in eine neue [Liste](/dotnet/api/system.collections.generic.list-1) von [PathAccessControl](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrol)-Objekten. Suchen Sie anschließend den Eintrag, den Sie entfernen möchten, und rufen Sie die [Remove](/dotnet/api/system.collections.ilist.remove)-Methode der Auflistung auf. Legen Sie die aktualisierte Zugriffssteuerungsliste fest, indem Sie die [DataLakeDirectoryClient.SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist)-Methode aufrufen.

In diesem Beispiel wird die Stamm-ACL eines Containers aktualisiert, indem der ACL-Eintrag für alle anderen Benutzer ersetzt wird.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_RemoveACLEntry":::

### <a name="remove-acl-entries-recursively"></a>Rekursives Entfernen von ACL-Einträgen

Um ACL-Einträge rekursiv zu entfernen, erstellen Sie ein neues ACL-Objekt für den zu entfernenden ACL-Eintrag, und verwenden Sie dieses Objekt dann im Entfernungsvorgang der ACL. Rufen Sie nicht die vorhandene ACL ab, stellen Sie lediglich die zu entfernenden ACL-Einträge bereit.

Sie entfernen ACL-Einträge, indem Sie die **DataLakeDirectoryClient.RemoveAccessControlRecursiveAsync**-Methode aufrufen. Übergeben Sie dieser Methode eine [Liste](/dotnet/api/system.collections.generic.list-1) der [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Jedes [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)-Element definiert einen ACL-Eintrag.

Wenn Sie einen **standardmäßigen** ACL-Eintrag entfernen möchten, dann können Sie die Eigenschaft [PathAccessControlItem.DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) der Eigenschaft [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) auf **true** festlegen.

In diesem Beispiel wird ein ACL-Eintrag aus der Zugriffssteuerungsliste des Verzeichnisses `my-parent-directory` entfernt. Diese Methode akzeptiert einen booleschen Parameter namens `isDefaultScope`, der angibt, ob der Eintrag aus der Standard-ACL entfernt werden soll. Dieser Parameter wird im Konstruktor von [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) verwendet.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_RemoveACLRecursively":::

Ein Beispiel, das Zugriffssteuerungslisten durch Angabe einer Batchgröße rekursiv in Batches entfernt, finden Sie im [.NET-Beispiel](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

## <a name="recover-from-failures"></a>Wiederherstellen nach Fehlern

Beim rekursiven Ändern von Zugriffssteuerungslisten können Laufzeit- oder Berechtigungsfehler auftreten. Starten Sie den Prozess bei Laufzeitfehlern vom Anfang neu. Berechtigungsfehler können auftreten, wenn der Sicherheitsprinzipal nicht über ausreichende Berechtigungen verfügt, um die Zugriffssteuerungsliste eines Verzeichnisses oder einer Datei zu ändern, die sich in der geänderten Verzeichnishierarchie befindet. Beheben Sie das Berechtigungsproblem, und setzen Sie dann den Prozess ab dem Zeitpunkt des Fehlers mithilfe eines Fortsetzungstokens fort, oder starten Sie den Prozess vom Anfang neu. Sie müssen das Fortsetzungstoken nicht verwenden, wenn Sie vom Anfang neu starten. Sie können ACL-Einträge ohne negative Auswirkung erneut anwenden.

In diesem Beispiel wird im Fall eines Fehlers ein Fortsetzungstoken zurückgegeben. Die Anwendung kann diese Beispielmethode erneut aufrufen, nachdem der Fehler behoben wurde, und das Fortsetzungstoken übergeben. Wenn diese Beispielmethode zum ersten Mal aufgerufen wird, kann die Anwendung den Wert `null` für den Fortsetzungstokenparameter übergeben.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ResumeContinuationToken":::

Ein Beispiel, das Zugriffssteuerungslisten durch Angabe einer Batchgröße rekursiv in Batches festlegt, finden Sie im [.NET-Beispiel](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

Wenn Sie möchten, dass der Prozess ohne Unterbrechung durch Berechtigungsfehler durchgeführt wird, können Sie dies angeben.

Um sicherzustellen, dass der Prozess ohne Unterbrechung durchgeführt wird, übergeben Sie ein **AccessControlChangedOptions**-Objekt, und legen Sie die Eigenschaft **ContinueOnFailure** dieses Objekts auf ``true`` fest.

In diesem Beispiel werden ACL-Einträge rekursiv festgelegt. Wenn dieser Code auf einen Berechtigungsfehler stößt, wird dieser Fehler aufgezeichnet und die Ausführung fortgesetzt. In diesem Beispiel wird die Anzahl der Fehler an die Konsole ausgegeben.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ContinueOnFailure":::

Ein Beispiel, das Zugriffssteuerungslisten durch Angabe einer Batchgröße rekursiv in Batches festlegt, finden Sie im [.NET-Beispiel](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Weitere Informationen

- [API-Referenzdokumentation](/dotnet/api/azure.storage.files.datalake)
- [Paket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
- [Beispiele](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
- [Zuordnung von Gen1 zu Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
- [Bekannte Probleme](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Feedback geben](https://github.com/Azure/azure-sdk-for-net/issues)
- [Zugriffssteuerungsmodell in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Zugriffssteuerungslisten (ACLs) in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
