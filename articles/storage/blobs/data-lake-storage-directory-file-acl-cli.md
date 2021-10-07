---
title: Verwenden der Azure CLI zum Verwalten von Daten (Azure Data Lake Storage Gen2)
description: Verwenden Sie die Azure CLI, um Verzeichnisse und Dateien in Speicherkonten zu verwalten, die über einen hierarchischen Namespace verfügen.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8dec2e939c6f39169d4c8fbbdfbcdf08d4d42599
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128651577"
---
# <a name="use-azure-cli-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Verwenden der Azure CLI zum Verwalten von Verzeichnissen und Dateien in Azure Data Lake Storage Gen2

In diesem Artikel erfahren Sie, wie Sie die [Azure-Befehlszeilenschnittstelle (CLI)](/cli/azure/) zum Erstellen und Verwalten von Verzeichnissen und Dateien in Speicherkonten verwenden, die über einen hierarchischen Namespace verfügen.

Informationen zum Abrufen, Festlegen und Aktualisieren der Zugriffssteuerungslisten (Access Control Lists, ACLs) von Verzeichnissen und Dateien finden Sie unter [Verwenden der Azure CLI zum Verwalten von Zugriffssteuerungslisten in Azure Data Lake Storage Gen2](data-lake-storage-acl-cli.md).

[Beispiele](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md) | [Feedback](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. Weitere Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

- Ein Speicherkonto, für das der hierarchische Namespace aktiviert ist. Befolgen Sie [diese Anleitung](create-data-lake-storage-account.md) für die Erstellung.

- Azure CLI, Version `2.6.0` oder höher.

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>Stellen Sie sicher, dass die korrekte Version der Azure CLI installiert ist.

1. Öffnen Sie [Azure Cloud Shell](../../cloud-shell/overview.md), oder falls Sie die Azure-Befehlszeilenschnittstelle lokal [installiert](/cli/azure/install-azure-cli) haben, öffnen Sie eine Befehlskonsolenanwendung wie Windows PowerShell.

2. Überprüfen Sie mit dem folgenden Befehl, ob die installierte Version der Azure-Befehlszeilenschnittstelle `2.6.0` oder höher ist.

   ```azurecli
    az --version
   ```

   Wenn Ihre Version der Azure-Befehlszeilenschnittstelle kleiner als `2.6.0` ist, dann installieren Sie eine neuere Version. Weitere Informationen finden Sie unter [Installieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli).

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

## <a name="create-a-container"></a>Erstellen eines Containers

Ein Container fungiert als Dateisystem für Ihre Dateien. Mit dem Befehl `az storage fs create` können Sie einen erstellen.

In diesem Beispiel wird ein Container namens `my-file-system` erstellt.

```azurecli
az storage fs create -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-container-properties"></a>Anzeigen der Containereigenschaften

Sie können die Eigenschaften eines Containers mithilfe des Befehls `az storage fs show` in der Konsole ausgeben.

```azurecli
az storage fs show -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="list-container-contents"></a>Auflisten von Containerinhalten

Listen Sie den Inhalt eines Verzeichnisses mit dem Befehl `az storage fs file list` auf.

In diesem Beispiel wird der Inhalt des Containers `my-file-system` aufgelistet.

```azurecli
az storage fs file list -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-container"></a>Löschen eines Containers

Mit dem Befehl `az storage fs delete` können Sie einen Container löschen.

In diesem Beispiel wird der Container `my-file-system` gelöscht.

```azurecli
az storage fs delete -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="create-a-directory"></a>Erstellen eines Verzeichnisses

Erstellen Sie eine Verzeichnisreferenz mit dem Befehl `az storage fs directory create`.

In diesem Beispiel wird dem Container `my-file-system`, der sich in einem Konto namens `mystorageaccount` befindet, ein Verzeichnis namens `my-directory` hinzugefügt.

```azurecli
az storage fs directory create -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-directory-properties"></a>Anzeigen von Verzeichniseigenschaften

Sie können die Eigenschaften eines Verzeichnisses auf der Konsole ausgeben, indem Sie den Befehl `az storage fs directory show` verwenden.

```azurecli
az storage fs directory show -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-directory"></a>Umbenennen oder Verschieben eines Verzeichnisses

Mit dem Befehl `az storage fs directory move` können Sie ein Verzeichnis umbenennen oder verschieben.

In diesem Beispiel wird ein Verzeichnis innerhalb des gleichen Containers von `my-directory` in `my-new-directory` umbenannt.

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

In diesem Beispiel wird ein Verzeichnis in den Container `my-second-file-system` verschoben.

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-second-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-directory"></a>Löschen eines Verzeichnisses

Löschen Sie ein Verzeichnis mit dem Befehl `az storage fs directory delete`.

In diesem Beispiel wird das Verzeichnis `my-directory` gelöscht.

```azurecli
az storage fs directory delete -n my-directory -f my-file-system  --account-name mystorageaccount --auth-mode login
```

## <a name="check-if-a-directory-exists"></a>Überprüfen, ob ein Verzeichnis vorhanden ist

Ermitteln Sie mit dem Befehl `az storage fs directory exists`, ob ein bestimmtes Verzeichnis im Container vorhanden ist.

Dieses Beispiel zeigt, ob im Container `my-file-system` ein Verzeichnis mit dem Namen `my-directory` vorhanden ist.

```azurecli
az storage fs directory exists -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="download-from-a-directory"></a>Herunterladen aus einem Verzeichnis

Laden Sie eine Datei mit dem Befehl `az storage fs file download` aus einem Verzeichnis herunter.

In diesem Beispiel wird eine Datei mit dem Namen `upload.txt` aus einem Verzeichnis mit dem Namen `my-directory` heruntergeladen.

```azurecli
az storage fs file download -p my-directory/upload.txt -f my-file-system -d "C:\myFolder\download.txt" --account-name mystorageaccount --auth-mode login
```

## <a name="list-directory-contents"></a>Auflisten des Verzeichnisinhalts

Listen Sie den Inhalt eines Verzeichnisses mit dem Befehl `az storage fs file list` auf.

In diesem Beispiel wird der Inhalt eines Verzeichnisses namens `my-directory` aufgelistet, das sich im Container `my-file-system` eines Speicherkontos mit dem Namen `mystorageaccount` befindet.

```azurecli
az storage fs file list -f my-file-system --path my-directory --account-name mystorageaccount --auth-mode login
```

## <a name="upload-a-file-to-a-directory"></a>Hochladen einer Datei in ein Verzeichnis

Laden Sie eine Datei mit dem Befehl `az storage fs file upload` in ein Verzeichnis hoch.

In diesem Beispiel wird die Datei `upload.txt` in das Verzeichnis `my-directory` hochgeladen.

```azurecli
az storage fs file upload -s "C:\myFolder\upload.txt" -p my-directory/upload.txt  -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-properties"></a>Anzeigen von Dateieigenschaften

Sie können die Eigenschaften einer Datei mit dem Befehl `az storage fs file show` auf der Konsole ausgeben.

```azurecli
az storage fs file show -p my-file.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-file"></a>Umbenennen oder Verschieben einer Datei

Mit dem Befehl `az storage fs file move` können Sie eine Datei umbenennen oder verschieben.

In diesem Beispiel wird eine Datei mit dem Namen `my-file.txt` in `my-file-renamed.txt` umbenannt.

```azurecli
az storage fs file move -p my-file.txt -f my-file-system --new-path my-file-system/my-file-renamed.txt --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file"></a>Löschen von Dateien

Löschen Sie eine Datei mit dem Befehl `az storage fs file delete`.

In diesem Beispiel wird eine Datei namens `my-file.txt` gelöscht.

```azurecli
az storage fs file delete -p my-directory/my-file.txt -f my-file-system  --account-name mystorageaccount --auth-mode login
```

## <a name="see-also"></a>Weitere Informationen

- [Beispiele](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
- [Geben Sie uns Feedback](https://github.com/Azure/azure-cli-extensions/issues)
- [Bekannte Probleme](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Verwenden der Azure CLI zum Verwalten von Zugriffssteuerungslisten in Azure Data Lake Storage Gen2](data-lake-storage-acl-cli.md)
