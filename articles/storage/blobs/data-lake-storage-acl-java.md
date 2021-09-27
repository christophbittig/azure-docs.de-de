---
title: Verwenden von Java zum Verwalten von Zugriffssteuerungslisten in Azure Data Lake Storage Gen2
description: Verwenden Sie Azure Storage-Bibliotheken für Java, um Zugriffssteuerungslisten (Access Control Lists, ACLs) in Speicherkonten zu verwalten, für die der hierarchische Namespace aktiviert ist.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.custom: devx-track-java
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 6414db11bac11cb9b6faab59e67980b8ee8a2a78
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128555506"
---
# <a name="use-java-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Verwenden von Java zum Verwalten von Zugriffssteuerungslisten in Azure Data Lake Storage Gen2

In diesem Artikel erfahren Sie, wie Sie mithilfe von Java die Zugriffssteuerungslisten von Verzeichnissen und Dateien abrufen, festlegen und aktualisieren.

Die Vererbung von Zugriffssteuerungslisten (ACLs) ist für neue untergeordnete Elemente, die unter einem übergeordneten Verzeichnis erstellt werden, bereits verfügbar. Sie können Zugriffssteuerungslisten aber auch rekursiv für die vorhandenen untergeordneten Elemente eines übergeordneten Verzeichnisses hinzufügen, aktualisieren und entfernen, ohne diese Änderungen für jedes untergeordnete Element einzeln vornehmen zu müssen.

[Maven-Paket](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) | [Beispiele](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake) | [API-Referenz](/java/api/overview/azure/storage-file-datalake-readme) | [Zuordnung von Gen1 zu Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [Feedback geben](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

- Ein Speicherkonto, für das der hierarchische Namespace aktiviert ist. Befolgen Sie [diese Anleitung](create-data-lake-storage-account.md) für die Erstellung.

- Azure CLI, Version `2.6.0` oder höher.

- Eine der folgenden Sicherheitsberechtigungen:

  - Ein bereitgestellter [Sicherheitsprinzipal](../../role-based-access-control/overview.md#security-principal) für Azure Active Directory (AD), dem die Rolle [Speicherblob-Datenbesitzer](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) im Bereich des Zielcontainers, der übergeordneten Ressourcengruppe oder des Abonnements zugewiesen wurde

  - Der Benutzer muss Besitzer des Zielcontainers oder -verzeichnisses sein, in dem Sie die ACL-Einstellungen anwenden möchten. Beim rekursiven Festlegen von ACLs schließt dies alle untergeordneten Elemente im Zielcontainer oder -verzeichnis ein.

  - Speicherkontoschlüssel

## <a name="set-up-your-project"></a>Einrichten des Projekts

Öffnen Sie zunächst [diese Seite](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake), und suchen Sie die neueste Version der Java-Bibliothek. Öffnen Sie dann die Datei *pom.xml* in Ihrem Text-Editor. Fügen Sie ein Abhängigkeitselement hinzu, das auf diese Version verweist.

Wenn Sie beabsichtigen, die Clientanwendung mithilfe von Azure Active Directory (AD) zu authentifizieren, fügen Sie der Azure-Geheimnisclientbibliothek eine Abhängigkeit hinzu. Informationen dazu finden Sie unter [Hinzufügen des Pakets für die Geheimnisclientbibliothek zu Ihrem Projekt](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project).

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

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Herstellen einer Verbindung mit Azure Active Directory (Azure AD)

Sie können die [Azure-Identitätsclientbibliothek für Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) verwenden, um Ihre Anwendung bei Azure AD zu authentifizieren.

Rufen Sie die Client-ID, ein Clientgeheimnis und die Mandanten-ID ab. Informationen zur Vorgehensweise finden Sie unter [Abrufen eines Tokens von Azure AD zum Autorisieren von Anforderungen von einer Clientanwendung](../common/storage-auth-aad-app.md). Im Rahmen dieses Prozesses müssen Sie dem Sicherheitsprinzipal eine der folgenden [Azure RBAC-Rollen](../../role-based-access-control/overview.md) (Azure Role-Based Access Control) zuweisen.

|Role|ACL-Einstellungsfunktion|
|--|--|
|[Besitzer von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Alle Verzeichnisse und Dateien im Konto|
|[Mitwirkender an Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Nur Verzeichnisse und Dateien im Besitz des Sicherheitsprinzipals|

In diesem Beispiel wird eine **DataLakeServiceClient**-Instanz mithilfe einer Client-ID, eines Clientgeheimnisses und einer Mandanten-ID erstellt.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithAzureAD":::

> [!NOTE]
> Weitere Beispiele finden Sie in der Dokumentation zur [Azure-Identitätsclientbibliothek für Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity).

### <a name="connect-by-using-an-account-key"></a>Herstellen einer Verbindung per Kontoschlüssel

Dies ist die einfachste Möglichkeit, eine Verbindung mit einem Konto herzustellen.

In diesem Beispiel wird eine **DataLakeServiceClient**-Instanz mithilfe eines Kontoschlüssels erstellt.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithKey":::

## <a name="set-acls"></a>Festlegen von ACLs

Wenn Sie eine Zugriffssteuerungsliste *festlegen*, **ersetzen** Sie die gesamte Zugriffssteuerungsliste einschließlich aller Einträge. Wenn Sie die Berechtigungsebene eines Sicherheitsprinzipals ändern oder der Zugriffssteuerungsliste einen neuen Sicherheitsprinzipal hinzufügen möchten, ohne dass sich dies auf bereits vorhandene Einträge auswirkt, sollten Sie die Zugriffssteuerungsliste stattdessen *aktualisieren*. Informationen dazu, wie eine Zugriffssteuerungsliste aktualisiert statt ersetzt wird, finden Sie im Abschnitt [Aktualisieren von ACLs](#update-acls) in diesem Artikel.

Wenn Sie die Zugriffssteuerungsliste *festlegen*, müssen Sie einen Eintrag für den besitzenden Benutzer, einen Eintrag für die besitzende Gruppe und einen Eintrag für alle anderen Benutzer hinzufügen. Weitere Informationen zum besitzenden Benutzer, zur besitzenden Gruppe und zu allen anderen Benutzern finden Sie unter [Benutzer und Identitäten](data-lake-storage-access-control.md#users-and-identities).

In diesem Abschnitt erfahren Sie Folgendes:

- Festlegen der ACL eines Verzeichnisses
- Festlegen der ACL einer Datei
- Rekursives Festlegen von ACLs

### <a name="set-the-acl-of-a-directory"></a>Festlegen der ACL eines Verzeichnisses

Im folgenden Beispiel wird die ACL (Zugriffssteuerungsliste) eines Verzeichnisses namens `my-directory` abgerufen und dann festgelegt. Im folgenden Beispiel werden dem zuständigen Benutzer Lese-, Schreib- und Ausführungsberechtigungen und der zuständigen Gruppe nur Lese- und Ausführungsberechtigungen gewährt, während allen anderen lediglich Lesezugriff gewährt wird.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageDirectoryACLs":::

Sie können auch die ACL des Stammverzeichnisses eines Containers abrufen und festlegen. Übergeben Sie zum Abrufen des Stammverzeichnisses eine leere Zeichenfolge (`""`) an die Methode **DataLakeFileSystemClient.getDirectoryClient**.

### <a name="set-the-acl-of-a-file"></a>Festlegen der ACL einer Datei

Im folgenden Beispiel wird die ACL einer Datei namens `upload-file.txt` abgerufen und dann festgelegt. Im folgenden Beispiel werden dem zuständigen Benutzer Lese-, Schreib- und Ausführungsberechtigungen und der zuständigen Gruppe nur Lese- und Ausführungsberechtigungen gewährt, während allen anderen lediglich Lesezugriff gewährt wird.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageFileACLs":::

### <a name="set-acls-recursively"></a>Rekursives Festlegen von ACLs

Legen Sie Zugriffssteuerungslisten rekursiv fest, indem Sie die Methode **DataLakeDirectoryClient.setAccessControlRecursive** aufrufen. Übergeben Sie dieser Methode eine [Liste](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) der [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html)-Objekte. Jedes [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html)-Element definiert einen ACL-Eintrag.

Wenn Sie einen **ACL-Standardeintrag** festlegen möchten, rufen Sie die **setDefaultScope**-Methode der [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html)-Klasse auf und übergeben den Wert **true**.

In diesem Beispiel wird die ACL des Verzeichnisses `my-parent-directory` festgelegt. Diese Methode akzeptiert einen booleschen Parameter namens `isDefaultScope`, der angibt, ob die Standard-ACL festgelegt werden soll. Dieser Parameter wird bei jedem Aufruf der **setDefaultScope**-Methode der [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html)-Klasse verwendet. Mit den Einträgen der ACL werden dem zuständigen Benutzer Lese-, Schreib- und Ausführungsberechtigungen und der zuständigen Gruppe nur Lese- und Ausführungsberechtigungen gewährt, während allen anderen kein Zugriff gewährt wird. Der letzte ACL-Eintrag in diesem Beispiel gewährt einem bestimmten Benutzer mit der Objekt-ID „xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx“ Lese- und Ausführungsberechtigungen.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_SetACLRecursively":::

## <a name="update-acls"></a>Aktualisieren von ACLs

Wenn Sie eine Zugriffssteuerungsliste *aktualisieren*, ändern Sie die Zugriffssteuerungsliste, anstatt sie zu ersetzen. Beispielsweise können Sie der Zugriffssteuerungsliste einen neuen Sicherheitsprinzipal hinzufügen, ohne dass sich dies auf andere Sicherheitsprinzipale auswirkt, die in der Zugriffssteuerungsliste aufgelistet sind.  Informationen dazu, wie eine Zugriffssteuerungsliste ersetzt statt aktualisiert wird, finden Sie im Abschnitt [Festlegen von Zugriffssteuerungslisten](#set-acls) in diesem Artikel.

In diesem Abschnitt erfahren Sie Folgendes:

- Aktualisieren einer Zugriffssteuerungsliste (ACL)
- Rekursives Aktualisieren von Zugriffssteuerungslisten

### <a name="update-an-acl"></a>Aktualisieren einer Zugriffssteuerungsliste (ACL)

Rufen Sie zuerst die Zugriffssteuerungsliste eines Verzeichnisses ab, indem Sie die [PathAccessControl.getAccessControlList](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrol.getaccesscontrollist)-Methode aufrufen. Kopieren Sie die Liste der ACL-Einträge in ein neues **List**-Objekt vom Typ [PathAccessControlListEntry](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrolentry). Suchen Sie dann den Eintrag, den Sie aktualisieren möchten, und ersetzen Sie ihn in der Liste. Legen Sie die Zugriffssteuerungsliste fest, indem Sie die [DataLakeDirectoryClient.setAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist)-Methode aufrufen.

In diesem Beispiel wird die Zugriffssteuerungsliste des Verzeichnisses `my-parent-directory` aktualisiert, indem der Eintrag für alle anderen Benutzer ersetzt wird.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_UpdateACL":::

Sie können auch die ACL des Stammverzeichnisses eines Containers abrufen und festlegen. Übergeben Sie zum Abrufen des Stammverzeichnisses eine leere Zeichenfolge (`""`) an die Methode **DataLakeFileSystemClient.getDirectoryClient**.

### <a name="update-acls-recursively"></a>Rekursives Aktualisieren von Zugriffssteuerungslisten

Um eine Zugriffssteuerungsliste rekursiv zu aktualisieren, erstellen Sie ein neues ACL-Objekt mit dem ACL-Eintrag, den Sie aktualisieren möchten, und verwenden Sie dieses Objekt dann im Aktualisierungsvorgang der Zugriffssteuerungsliste. Rufen Sie nicht die vorhandene Zugriffssteuerungsliste ab, stellen Sie lediglich die zu aktualisierenden Zugriffssteuerungslisteneinträge bereit.

Aktualisieren Sie Zugriffssteuerungslisten rekursiv, indem Sie die Methode **DataLakeDirectoryClient.updateAccessControlRecursive** aufrufen.  Übergeben Sie dieser Methode eine [Liste](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) der [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html)-Objekte. Jedes [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html)-Element definiert einen ACL-Eintrag.

Wenn Sie einen **ACL-Standardeintrag** aktualisieren möchten, können Sie die **setDefaultScope**-Methode der [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html)-Klasse aufrufen und den Wert **true** übergeben.

Dieses Beispiel aktualisiert einen ACL-Eintrag mit Schreibberechtigung. Diese Methode akzeptiert einen booleschen Parameter namens `isDefaultScope`, der angibt, ob die Standard-ACL aktualisiert werden soll. Dieser Parameter wird beim Aufruf der **setDefaultScope**-Methode der [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html)-Klasse verwendet.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_UpdateACLRecursively":::

## <a name="remove-acl-entries"></a>Entfernen von ACL-Einträgen

Sie können einen oder mehrere ACL-Einträge entfernen. In diesem Abschnitt erfahren Sie Folgendes:

- Entfernen eines ACL-Eintrags
- Rekursives Entfernen von ACL-Einträgen

### <a name="remove-an-acl-entry"></a>Entfernen eines ACL-Eintrags

Rufen Sie zuerst die Zugriffssteuerungsliste eines Verzeichnisses ab, indem Sie die [PathAccessControl.getAccessControlList](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrol.getaccesscontrollist)-Methode aufrufen. Kopieren Sie die Liste der ACL-Einträge in ein neues **List**-Objekt vom Typ [PathAccessControlListEntry](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrolentry). Suchen Sie anschließend den Eintrag, den Sie entfernen möchten, und rufen Sie die **Remove**-Methode des **List**-Objekts auf. Legen Sie die aktualisierte Zugriffssteuerungsliste fest, indem Sie die [DataLakeDirectoryClient.setAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist)-Methode aufrufen.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_RemoveACLEntry":::

### <a name="remove-acl-entries-recursively"></a>Rekursives Entfernen von ACL-Einträgen

Um ACL-Einträge rekursiv zu entfernen, erstellen Sie ein neues ACL-Objekt für den zu entfernenden ACL-Eintrag, und verwenden Sie dieses Objekt dann im Entfernungsvorgang der ACL. Rufen Sie nicht die vorhandene ACL ab, stellen Sie lediglich die zu entfernenden ACL-Einträge bereit.

Entfernen Sie ACL-Einträge, indem Sie die Methode **DataLakeDirectoryClient.removeAccessControlRecursive** aufrufen. Übergeben Sie dieser Methode eine [Liste](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) der [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html)-Objekte. Jedes [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html)-Element definiert einen ACL-Eintrag.

Wenn Sie einen **ACL-Standardeintrag** entfernen möchten, rufen Sie die **setDefaultScope**-Methode der [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html)-Klasse auf und übergeben den Wert **true**.

In diesem Beispiel wird ein ACL-Eintrag aus der Zugriffssteuerungsliste des Verzeichnisses `my-parent-directory` entfernt. Diese Methode akzeptiert einen booleschen Parameter namens `isDefaultScope`, der angibt, ob der Eintrag aus der Standard-ACL entfernt werden soll. Dieser Parameter wird beim Aufruf der **setDefaultScope**-Methode der [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html)-Klasse verwendet.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_RemoveACLRecursively":::

## <a name="recover-from-failures"></a>Wiederherstellen nach Fehlern

Es können Laufzeit- oder Berechtigungsfehler auftreten. Starten Sie den Prozess bei Laufzeitfehlern vom Anfang neu. Berechtigungsfehler können auftreten, wenn der Sicherheitsprinzipal nicht über ausreichende Berechtigungen verfügt, um die Zugriffssteuerungsliste eines Verzeichnisses oder einer Datei zu ändern, die sich in der geänderten Verzeichnishierarchie befindet. Beheben Sie das Berechtigungsproblem, und setzen Sie dann den Prozess ab dem Zeitpunkt des Fehlers mithilfe eines Fortsetzungstokens fort, oder starten Sie den Prozess vom Anfang neu. Sie müssen das Fortsetzungstoken nicht verwenden, wenn Sie vom Anfang neu starten. Sie können ACL-Einträge ohne negative Auswirkung erneut anwenden.

In diesem Beispiel wird im Fall eines Fehlers ein Fortsetzungstoken zurückgegeben. Die Anwendung kann diese Beispielmethode erneut aufrufen, nachdem der Fehler behoben wurde, und das Fortsetzungstoken übergeben. Wenn diese Beispielmethode zum ersten Mal aufgerufen wird, kann die Anwendung den Wert `null` für den Fortsetzungstokenparameter übergeben.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ResumeSetACLRecursively":::

Wenn Sie möchten, dass der Prozess ohne Unterbrechung durch Berechtigungsfehler durchgeführt wird, können Sie dies angeben.

Um sicherzustellen, dass der Prozess ohne Unterbrechung durchgeführt wird, rufen Sie die **setContinueOnFailure**-Methode eines [PathSetAccessControlRecursiveOptions](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html)-Objekts auf, und übergeben Sie den Wert **true**.

In diesem Beispiel werden ACL-Einträge rekursiv festgelegt. Wenn dieser Code auf einen Berechtigungsfehler stößt, wird dieser Fehler aufgezeichnet und die Ausführung fortgesetzt. In diesem Beispiel wird die Anzahl der Fehler an die Konsole ausgegeben.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ContinueOnFailure":::

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Weitere Informationen

- [API-Referenzdokumentation](/java/api/overview/azure/storage-file-datalake-readme)
- [Maven-Paket](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
- [Beispiele](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
- [Zuordnung von Gen1 zu Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [Bekannte Probleme](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Feedback geben](https://github.com/Azure/azure-sdk-for-java/issues)
- [Zugriffssteuerungsmodell in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Zugriffssteuerungslisten (ACLs) in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
