---
title: Verwenden der Azure CLI zum Verwalten von Zugriffssteuerungslisten in Azure Data Lake Storage Gen2
description: Verwenden Sie die Azure-Befehlszeilenschnittstelle, um Zugriffssteuerungslisten (Access Control Lists, ACLs) in Speicherkonten zu verwalten, die über einen hierarchischen Namespace verfügen.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurecli
ms.openlocfilehash: fe26f8f61acbbf454cd2eb2928fdcab0f0bc7e47
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866967"
---
# <a name="use-azure-cli-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Verwenden der Azure CLI zum Verwalten von Zugriffssteuerungslisten in Azure Data Lake Storage Gen2

In diesem Artikel erfahren Sie, wie Sie mithilfe der [Azure-Befehlszeilenschnittstelle (Azure Command-Line Interface, CLI)](/cli/azure/) die Zugriffssteuerungslisten von Verzeichnissen und Dateien abrufen, festlegen und aktualisieren.

Die Vererbung von Zugriffssteuerungslisten (ACLs) ist für neue untergeordnete Elemente, die unter einem übergeordneten Verzeichnis erstellt werden, bereits verfügbar. Sie können Zugriffssteuerungslisten aber auch rekursiv für die vorhandenen untergeordneten Elemente eines übergeordneten Verzeichnisses hinzufügen, aktualisieren und entfernen, ohne diese Änderungen für jedes untergeordnete Element einzeln vornehmen zu müssen.

[Referenz](/cli/azure/storage/fs/access) | [Beispiele](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md) | [Feedback geben](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

- Ein Speicherkonto, für das der hierarchische Namespace aktiviert ist. Befolgen Sie [diese Anleitung](create-data-lake-storage-account.md) für die Erstellung.

- Azure CLI, Version `2.14.0` oder höher.

- Eine der folgenden Sicherheitsberechtigungen:

  - Ein bereitgestellter [Sicherheitsprinzipal](../../role-based-access-control/overview.md#security-principal) für Azure Active Directory (Azure AD), dem die Rolle [Speicherblob-Datenbesitzer](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) im Bereich des Zielcontainers, der übergeordneten Ressourcengruppe oder des Abonnements zugewiesen wurde  

  - Der Benutzer muss Besitzer des Zielcontainers oder -verzeichnisses sein, in dem Sie die ACL-Einstellungen anwenden möchten. Beim rekursiven Festlegen von ACLs schließt dies alle untergeordneten Elemente im Zielcontainer oder -verzeichnis ein.
  
  - Speicherkontoschlüssel

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>Stellen Sie sicher, dass die korrekte Version der Azure CLI installiert ist.

1. Öffnen Sie [Azure Cloud Shell](../../cloud-shell/overview.md), oder falls Sie die Azure-Befehlszeilenschnittstelle lokal [installiert](/cli/azure/install-azure-cli) haben, öffnen Sie eine Befehlskonsolenanwendung wie Windows PowerShell.

2. Überprüfen Sie mit dem folgenden Befehl, ob die installierte Version der Azure-Befehlszeilenschnittstelle `2.14.0` oder höher ist.

   ```azurecli
    az --version
   ```

   Wenn Ihre Version der Azure-Befehlszeilenschnittstelle kleiner als `2.14.0` ist, dann installieren Sie eine neuere Version. Weitere Informationen finden Sie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

## <a name="connect-to-the-account"></a>Herstellen einer Verbindung mit dem Konto

1. Wenn Sie die Azure-Befehlszeilenschnittstelle lokal verwenden, führen Sie den Anmeldebefehl aus.

   ```azurecli
   az login
   ```

   Die CLI öffnet Ihren Standardbrowser, sofern sie dazu in der Lage ist, und lädt eine Azure-Anmeldeseite.

   Öffnen Sie andernfalls die Browserseite [https://aka.ms/devicelogin](https://aka.ms/devicelogin), und geben Sie den in Ihrem Terminal angezeigten Autorisierungscode ein. Melden Sie sich dann im Browser mit Ihren Anmeldeinformationen an.

   Weitere Informationen zu verschiedenen Authentifizierungsmethoden finden Sie unter [Autorisieren des Zugriffs auf Blob- oder Warteschlangendaten mit der Azure CLI](./authorize-data-operations-cli.md).

2. Wenn Ihre Identität mehreren Abonnements zugeordnet ist, legen Sie das aktive Abonnement auf das Abonnement des Speicherkontos fest, auf dem Ihre statische Website gehostet wird.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Ersetzen Sie den Platzhalterwert `<subscription-id>` durch die ID Ihres Abonnements.

> [!NOTE]
> Das in diesem Artikel dargestellte Beispiel zeigt die Azure AD-Autorisierung (Azure AD). Weitere Informationen zu Autorisierungsmethoden finden Sie unter [Autorisieren des Zugriffs auf Blob- oder Warteschlangendaten mit der Azure CLI](./authorize-data-operations-cli.md).

## <a name="get-acls"></a>Abrufen von ACLs

Rufen Sie die Zugriffssteuerungsliste (ACL) für ein **Verzeichnis** mit dem Befehl [az storage fs access show](/cli/azure/storage/fs#az_storage_fs_show) ab.

In diesem Beispiel wird die Zugriffssteuerungsliste eines Verzeichnisses abgerufen und dann auf der Konsole ausgegeben.

```azurecli
az storage fs access show -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Rufen Sie die Berechtigungen für eine **Datei** mit dem Befehl [az storage fs access show](/cli/azure/storage/fs#az_storage_fs_show) ab. 

In diesem Beispiel wird die Zugriffssteuerungsliste einer Datei abgerufen und dann auf der Konsole ausgegeben.

```azurecli
az storage fs access show -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

Die folgende Abbildung zeigt die Ausgabe nach dem Abrufen der Zugriffssteuerungsliste eines Verzeichnisses.

![Abrufen der Ausgabe der Zugriffssteuerungsliste](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

In diesem Beispiel verfügt der zuständige Benutzer über Berechtigungen zum Lesen, Schreiben und Ausführen. Die zuständige Gruppe verfügt lediglich über Berechtigungen zum Lesen und Ausführen. Weitere Informationen zu Zugriffssteuerungslisten finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

## <a name="set-acls"></a>Festlegen von ACLs

Wenn Sie eine Zugriffssteuerungsliste *festlegen*, **ersetzen** Sie die gesamte Zugriffssteuerungsliste einschließlich aller Einträge. Wenn Sie die Berechtigungsebene eines Sicherheitsprinzipals ändern oder der Zugriffssteuerungsliste einen neuen Sicherheitsprinzipal hinzufügen möchten, ohne dass sich dies auf bereits vorhandene Einträge auswirkt, sollten Sie die Zugriffssteuerungsliste stattdessen *aktualisieren*. Informationen dazu, wie eine Zugriffssteuerungsliste aktualisiert statt ersetzt wird, finden Sie im Abschnitt [Aktualisieren von ACLs](#update-acls) in diesem Artikel.  

Wenn Sie die Zugriffssteuerungsliste *festlegen*, müssen Sie einen Eintrag für den besitzenden Benutzer, einen Eintrag für die besitzende Gruppe und einen Eintrag für alle anderen Benutzer hinzufügen. Weitere Informationen zum besitzenden Benutzer, zur besitzenden Gruppe und zu allen anderen Benutzern finden Sie unter [Benutzer und Identitäten](data-lake-storage-access-control.md#users-and-identities).

In diesem Abschnitt erfahren Sie Folgendes:

- Festlegen einer Zugriffssteuerungsliste
- Rekursives Festlegen von ACLs

### <a name="set-an-acl"></a>Festlegen einer Zugriffssteuerungsliste

Verwenden Sie den Befehl [az storage fs access set](/cli/azure/storage/fs/access#az_storage_fs_access_set), um die Zugriffssteuerungsliste für ein **Verzeichnis** festzulegen. 

In diesem Beispiel wird die Zugriffssteuerungsliste auf ein Verzeichnis für den zuständigen Benutzer, die zuständige Gruppe oder andere Benutzer festgelegt und dann auf der Konsole ausgegeben.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

In diesem Beispiel wird die *Standard*-Zugriffssteuerungsliste auf ein Verzeichnis für den zuständigen Benutzer, die zuständige Gruppe oder andere Benutzer festgelegt und dann in der Konsole ausgegeben.

```azurecli
az storage fs access set --acl "default:user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Verwenden Sie den Befehl [az storage fs access set](/cli/azure/storage/fs/access#az_storage_fs_access_set), um die Zugriffssteuerungsliste für eine **Datei** festzulegen. 

In diesem Beispiel wird die Zugriffssteuerungsliste auf eine Datei für den zuständigen Benutzer, die zuständige Gruppe oder andere Benutzer festgelegt und anschließend auf der Konsole ausgegeben.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Verwenden Sie die entsprechenden Objekt-IDs, um die Zugriffssteuerungsliste einer spezifischen Gruppe oder eines spezifischen Benutzers festzulegen. Zum Beispiel: `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` oder `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

Die folgende Abbildung zeigt die Ausgabe nach dem Festlegen der Zugriffssteuerungsliste einer Datei.

![Abrufen der Ausgabe der Zugriffssteuerungsliste 2](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

In diesem Beispiel verfügen der zuständige Benutzer und die zuständige Gruppe nur über Berechtigungen zum Lesen und Schreiben. Alle anderen Benutzer verfügen über Berechtigungen zum Schreiben und Ausführen. Weitere Informationen zu Zugriffssteuerungslisten finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-acls-recursively"></a>Rekursives Festlegen von ACLs

Zum rekursiven Festlegen von Zugriffssteuerungslisten verwenden Sie den Befehl [az storage fs access set-recursive](/cli/azure/storage/fs/access#az_storage_fs_access_set_recursive).

In diesem Beispiel wird die ACL des Verzeichnisses `my-parent-directory` festgelegt. Mit diesen Einträgen werden dem zuständigen Benutzer Lese-, Schreib- und Ausführungsberechtigungen und der zuständigen Gruppe nur Lese- und Ausführungsberechtigungen gewährt, während allen anderen kein Zugriff gewährt wird. Der letzte ACL-Eintrag in diesem Beispiel gewährt einem bestimmten Benutzer mit der Objekt-ID „xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx“ Lese- und Ausführungsberechtigungen.

```azurecli
az storage fs access set-recursive --acl "user::rwx,group::r-x,other::---,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Wenn Sie einen **standardmäßigen** ACL-Eintrag festlegen möchten, fügen Sie jedem Eintrag das Präfix `default:` hinzu. Zum Beispiel: `default:user::rwx` oder `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`.

## <a name="update-acls"></a>Aktualisieren von ACLs

Wenn Sie eine Zugriffssteuerungsliste *aktualisieren*, ändern Sie die Zugriffssteuerungsliste, anstatt sie zu ersetzen. Beispielsweise können Sie der Zugriffssteuerungsliste einen neuen Sicherheitsprinzipal hinzufügen, ohne dass sich dies auf andere Sicherheitsprinzipale auswirkt, die in der Zugriffssteuerungsliste aufgelistet sind.  Informationen dazu, wie eine Zugriffssteuerungsliste ersetzt statt aktualisiert wird, finden Sie im Abschnitt [Festlegen von Zugriffssteuerungslisten](#set-acls) in diesem Artikel.

Um eine Zugriffssteuerungsliste zu aktualisieren, erstellen Sie ein neues Zugriffssteuerungsliste-Objekt mit dem Zugriffssteuerungslisteneintrag, den Sie aktualisieren möchten, und verwenden Sie dieses Objekt dann im Aktualisierungsvorgang der Zugriffssteuerungsliste. Rufen Sie nicht die vorhandene Zugriffssteuerungsliste ab, stellen Sie lediglich die zu aktualisierenden Zugriffssteuerungslisteneinträge bereit.

In diesem Abschnitt erfahren Sie Folgendes:

- Aktualisieren einer Zugriffssteuerungsliste (ACL)
- Rekursives Aktualisieren von Zugriffssteuerungslisten

### <a name="update-an-acl"></a>Aktualisieren einer Zugriffssteuerungsliste (ACL)

Eine weitere Möglichkeit, diese Berechtigung festzulegen, ist die Verwendung des Befehls [az storage fs access set](/cli/azure/storage/fs/access#az_storage_fs_access_set). 

Aktualisieren Sie die Zugriffssteuerungsliste eines Verzeichnisses oder einer Datei, indem Sie den Parameter `-permissions` auf die Kurzform einer Zugriffssteuerungsliste festlegen.

In diesem Beispiel wird die Zugriffssteuerungsliste eines **Verzeichnisses** aktualisiert.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

In diesem Beispiel wird die Zugriffssteuerungsliste einer **Datei** aktualisiert.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Verwenden Sie die entsprechenden Objekt-IDs, um ein Update an der Zugriffssteuerungsliste einer spezifischen Gruppe oder eines spezifischen Benutzers durchzuführen. Zum Beispiel: `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` oder `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

Sie können den zuständigen Benutzer und die Gruppe eines Verzeichnisses oder einer Datei auch aktualisieren, indem Sie die Parameter `--owner` oder `group` auf die Entitäts-ID oder den Benutzerprinzipalnamen (UPN) eines Benutzers festlegen.

In diesem Beispiel wird der Besitzer eines Verzeichnisses geändert.

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

In diesem Beispiel wird der Besitzer einer Datei geändert. 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login

```

### <a name="update-acls-recursively"></a>Rekursives Aktualisieren von Zugriffssteuerungslisten

Zum rekursiver Aktualisieren von Zugriffssteuerungslisten verwenden Sie den Befehl [az storage fs access update-recursive](/cli/azure/storage/fs/access#az_storage_fs_access_update_recursive).

Dieses Beispiel aktualisiert einen ACL-Eintrag mit Schreibberechtigung.

```azurecli
az storage fs access update-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Wenn Sie einen **standardmäßigen** ACL-Eintrag aktualisieren möchten, fügen Sie jedem Eintrag das Präfix `default:` hinzu. Beispiel: `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`.

## <a name="remove-acl-entries-recursively"></a>Rekursives Entfernen von ACL-Einträgen

Sie können einen oder mehrere ACL-Einträge rekursiv entfernen. Um einen ACL-Eintrag zu entfernen, erstellen Sie ein neues ACL-Objekt für den zu entfernenden ACL-Eintrag, und verwenden Sie dieses Objekt dann im Entfernungsvorgang der ACL. Rufen Sie nicht die vorhandene ACL ab, stellen Sie lediglich die zu entfernenden ACL-Einträge bereit.

Zum Entfernen von ACL-Einträgen verwenden Sie den Befehl [az storage fs access remove-recursive](/cli/azure/storage/fs/access#az_storage_fs_access_remove_recursive).

In diesem Beispiel wird ein ACL-Eintrag aus dem Stammverzeichnis des Containers entfernt.  

```azurecli
az storage fs access remove-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Wenn Sie einen **standardmäßigen** ACL-Eintrag entfernen möchten, fügen Sie jedem Eintrag das Präfix `default:` hinzu. Beispiel: `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

## <a name="recover-from-failures"></a>Wiederherstellen nach Fehlern

Beim rekursiven Ändern von Zugriffssteuerungslisten können Laufzeit- oder Berechtigungsfehler auftreten. Starten Sie den Prozess bei Laufzeitfehlern vom Anfang neu. Berechtigungsfehler können auftreten, wenn der Sicherheitsprinzipal nicht über ausreichende Berechtigungen verfügt, um die Zugriffssteuerungsliste eines Verzeichnisses oder einer Datei zu ändern, die sich in der geänderten Verzeichnishierarchie befindet. Beheben Sie das Berechtigungsproblem, und setzen Sie dann den Prozess ab dem Zeitpunkt des Fehlers mithilfe eines Fortsetzungstokens fort, oder starten Sie den Prozess vom Anfang neu. Sie müssen das Fortsetzungstoken nicht verwenden, wenn Sie vom Anfang neu starten. Sie können ACL-Einträge ohne negative Auswirkung erneut anwenden.

Im Fall eines Fehlers können Sie ein Fortsetzungstoken zurückgeben, indem Sie den Parameter `--continue-on-failure` auf `false` festlegen. Nachdem Sie die Fehler behoben haben, können Sie den Prozess ab dem Fehlerzeitpunkt fortsetzen. Dazu führen Sie den Befehl erneut aus, und legen Sie dann den Parameter `--continuation` auf das Fortsetzungstoken fest.

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure false --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

Wenn Sie möchten, dass der Prozess ohne Unterbrechung durch Berechtigungsfehler durchgeführt wird, können Sie dies angeben.

Um sicherzustellen, dass der Prozess ohne Unterbrechung durchgeführt wird, legen Sie den Parameter `--continue-on-failure` auf `true` fest.

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure true --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Weitere Informationen

- [Beispiele](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
- [Geben Sie uns Feedback](https://github.com/Azure/azure-cli-extensions/issues)
- [Bekannte Probleme](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Zugriffssteuerungsmodell in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Zugriffssteuerungslisten (ACLs) in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)