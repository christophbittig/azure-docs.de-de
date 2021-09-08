---
title: 'Schnellstart: Erstellen eines Blobs mit PowerShell'
titleSuffix: Azure Storage
description: In dieser Schnellstartanleitung verwenden Sie Azure PowerShell im Objektspeicher (Blobspeicher). Anschließend verwenden Sie PowerShell, um ein Blob in Azure Storage hochzuladen, ein Blob herunterzuladen und die Blobs in einem Container aufzulisten.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 03/31/2020
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 46b36452926b02ea524b35727d54535a4ffa3f27
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123467734"
---
# <a name="quickstart-upload-download-and-list-blobs-with-powershell"></a>Schnellstart: Hochladen, Herunterladen und Auflisten von Blobs mit PowerShell

Verwenden Sie das Azure PowerShell-Modul, um Azure-Ressourcen zu erstellen und zu verwalten. Die Erstellung und Verwaltung von Azure-Ressourcen kann über die PowerShell-Befehlszeile oder mithilfe von Skripts durchgeführt werden. In dieser Anleitung erfahren Sie, wie Sie Dateien mit PowerShell zwischen der lokalen Festplatte und Azure Blob Storage übertragen.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen ein Azure-Abonnement, um auf Azure Storage zuzugreifen. Wenn Sie noch kein Abonnement haben, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

Sie benötigen zudem die Rolle „Mitwirkender an Storage-Blobdaten“, um Lese- und Schreibvorgänge für Azure Storage-Container und -Blobs auszuführen und diese zu löschen.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Für diesen Schnellstart ist das Azure PowerShell-Modul Az, Version 0.7 oder höher, erforderlich. Führen Sie `Get-InstalledModule -Name Az -AllVersions | select Name,Version` aus, um die Version zu ermitteln. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie unter [Install and configure Azure PowerShell](/powershell/azure/install-az-ps) (Installieren des Azure PowerShell-Moduls) Informationen dazu.

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Erstellen eines Containers

Blobs werden immer in einen Container hochgeladen. Sie können Gruppen von Blobs ähnlich wie Dateien in Ordnern auf Ihrem Computer organisieren.

Legen Sie den Containernamen fest, und erstellen Sie den Container dann mithilfe von [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Legen Sie die Berechtigungen auf `blob` fest, um öffentlichen Zugriff auf die Dateien zu ermöglichen. Der Containername in diesem Beispiel lautet *quickstartblobs*.

```powershell
$containerName = "quickstartblobs"
New-AzStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-to-the-container"></a>Hochladen von Blobs in den Container

Blobspeicher unterstützt Block-, Anfüge- und Seitenblobs. VHD-Dateien, die IaaS-VMs zugrunde liegen, sind Seitenblobs. Anfügeblobs dienen zur Protokollierung und werden beispielsweise verwendet, wenn Sie etwas in eine Datei schreiben und ihr nach und nach weitere Informationen hinzufügen möchten. Die meisten Dateien, die im Blob Storage gespeichert werden, sind allerdings Blockblobs. 

Rufen Sie zum Hochladen einer Datei in ein Blockblob zuerst einen Containerverweis und anschließend einen Verweis auf das Blockblob in diesem Container auf. Nachdem Sie den Blobverweis abgerufen haben, können Sie mithilfe von [Set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent) Daten in das Blockblob hochladen. Bei diesem Vorgang wird das Blob erstellt, falls es nicht vorhanden ist, oder überschrieben, falls es vorhanden ist.

In den folgenden Beispielen werden die Dateien *Image001.jpg* und *Image002.png* aus dem Ordner *D:\\_TestImages* des lokalen Datenträgers in den erstellten Container hochgeladen.

```powershell
# upload a file to the default account (inferred) access tier
Set-AzStorageBlobContent -File "D:\_TestImages\Image000.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 

# upload a file to the Hot access tier
Set-AzStorageBlobContent -File "D:\_TestImages\Image001.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx `
  -StandardBlobTier Hot

# upload another file to the Cool access tier
Set-AzStorageBlobContent -File "D:\_TestImages\Image002.png" `
  -Container $containerName `
  -Blob "Image002.png" `
  -Context $ctx `
  -StandardBlobTier Cool

# upload a file to a folder to the Archive access tier
Set-AzStorageBlobContent -File "D:\_TestImages\foldername\Image003.jpg" `
  -Container $containerName `
  -Blob "Foldername/Image003.jpg" `
  -Context $ctx `
  -StandardBlobTier Archive
```

Laden Sie beliebig viele Dateien hoch, bevor Sie fortfahren.

## <a name="list-the-blobs-in-a-container"></a>Auflisten der Blobs in einem Container

Verwenden Sie [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob), um eine Liste der Blobs im Container abzurufen. In diesem Beispiel werden nur die Namen der hochgeladenen Blobs angezeigt.

```powershell
Get-AzStorageBlob -Container $ContainerName -Context $ctx | select Name
```

## <a name="download-blobs"></a>Herunterladen von Blobs

Laden Sie die Blobs auf Ihren lokalen Datenträger herunter. Legen Sie für jedes Blob, das heruntergeladen werden soll, den Namen fest, und rufen Sie [Get-AzStorageBlobContent](/powershell/module/az.storage/get-azstorageblobcontent) zum Herunterladen des Blobs auf.

Im folgenden Beispiel werden die Blobs in das Verzeichnis *D:\\_TestImages\Downloads* auf dem lokalen Datenträger heruntergeladen. 

```powershell
# download first blob
Get-AzStorageBlobContent -Blob "Image001.jpg" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 

# download another blob
Get-AzStorageBlobContent -Blob "Image002.png" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx
```

## <a name="data-transfer-with-azcopy"></a>Übertragen von Daten mit AzCopy

Das Befehlszeilenprogramm AzCopy bietet skriptfähige Hochleistungsdatenübertragung für Azure Storage. Mit AzCopy können Daten zwischen Blobspeicher und Azure Files übertragen werden. Weitere Informationen zu AzCopy v10 (der aktuellen Version von AzCopy) finden Sie unter [Erste Schritte mit AzCopy](../common/storage-use-azcopy-v10.md). Informationen zur Verwendung von AzCopy V10 mit Blobspeicher finden Sie unter [Übertragen von Daten mit AzCopy und Blob Storage](../common/storage-use-azcopy-v10.md#transfer-data).

Im folgenden Beispiel wird AzCopy verwendet, um eine lokale Datei in ein Blob hochzuladen. Denken Sie daran, die Beispielwerte durch Ihre eigenen Werte zu ersetzen:

```powershell
azcopy login
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Entfernen Sie alle Objekte, die Sie erstellt haben. Die einfachste Methode zum Entfernen der Ressourcen ist die Löschung der Ressourcengruppe. Beim Entfernen der Ressourcengruppe werden auch alle in der Gruppe enthaltenen Ressourcen gelöscht. Im folgenden Beispiel wird durch Entfernen der Ressourcengruppe das Speicherkonto zusammen mit der Ressourcengruppe entfernt.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie Dateien zwischen einem lokalen Dateisystem und Azure Blob Storage übertragen. Weitere Informationen zur Verwendung des Blobspeichers mit PowerShell finden Sie im Artikel mit den Azure PowerShell-Beispielen für Blobspeicher.

> [!div class="nextstepaction"]
> [Azure PowerShell-Beispiele für Azure Blob Storage](storage-samples-blobs-powershell.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

### <a name="microsoft-azure-powershell-storage-cmdlets-reference"></a>Referenz zu Microsoft Azure PowerShell Storage-Cmdlets

* [Storage PowerShell-Cmdlets](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage-Explorer

* Beim [Microsoft Azure Storage-Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) handelt es sich um eine kostenlose eigenständige App von Microsoft, über die Sie ganz einfach visuell mit Azure Storage-Daten arbeiten können – unter Windows, MacOS und Linux.
