---
title: Datei einfügen
description: include file
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 6/01/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 9d8d983b5ac7082ff3037d465f43ea9a2ad47fb4
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129300390"
---
# <a name="portal"></a>[Portal](#tab/azure-portal)
Um einen Serverendpunkt hinzuzufügen, wechseln Sie zu der neu erstellten Synchronisierungsgruppe, und wählen Sie dann **Serverendpunkt hinzufügen** aus.

![Hinzufügen eines neuen Serverendpunkts im Bereich „Synchronisierungsgruppe“](media/storage-files-sync-create-server-endpoint/add-server-endpoint.png)

Geben Sie im Bereich **Serverendpunkt hinzufügen** die folgenden Informationen ein, um einen Serverendpunkt zu erstellen:

- **Registrierter Server**: Der Name des Servers oder Clusters, auf bzw. in dem Sie den Serverendpunkt erstellen möchten.
- **Pfad**: Der Windows Server-Pfad, der als Teil der Synchronisierungsgruppe synchronisiert werden soll.
- **Cloudtiering**: Ein Schalter, mit dem Cloudtiering aktiviert oder deaktiviert wird. Mit Cloudtiering kann für selten verwendete oder selten einem Zugriff ausgesetzte Dateien Tiering nach Azure Files festgelegt werden.
- **Freier Speicherplatz auf Volume**: Die Menge an freiem Speicherplatz auf dem Volume, auf dem sich der Serverendpunkt befindet, die reserviert werden soll. Wenn z. B. für ein Volume mit einem einzigen Serverendpunkt „Freier Volumespeicherplatz“ auf 50 % festgelegt ist, wird für ungefähr die Hälfte der Daten ein Tiering nach Azure Files durchgeführt. Die Azure-Dateifreigabe enthält immer eine vollständige Kopie der Daten in der Synchronisierungsgruppe, unabhängig davon, ob Cloudtiering aktiviert ist.
- **Anfänglicher Downloadmodus**: Dies ist eine optionale Auswahl, die hilfreich sein kann, wenn Dateien in der Azure-Dateifreigabe, aber nicht auf dem Server vorhanden sind. Eine solche Situation kann beispielsweise auftreten, wenn Sie einen Serverendpunkt erstellen, um einer Synchronisierungsgruppe einen weiteren Zweigstellenserver hinzuzufügen, oder wenn Sie die Notfallwiederherstellung für einen ausgefallenen Server durchführen. Wenn das Cloudtiering aktiviert ist, wird standardmäßig nur der Namespace abgerufen, anfangs kein Dateiinhalt. Dies ist hilfreich, wenn Sie der Ansicht sind, dass eher Benutzerzugriffsanforderungen entscheiden sollen, welche Dateiinhalte auf den Server abgerufen werden. Wenn das Cloudtiering deaktiviert ist, ist der Standard, dass der Namespace zuerst heruntergeladen wird, und anschließend werden Dateien auf Grundlage des Zeitstempels der letzten Änderung abgerufen, bis die lokale Kapazität erreicht ist. Sie können den anfänglichen Downloadmodus jedoch in „Nur Namespace“ ändern. Ein dritter Modus kann nur verwendet werden, wenn das Cloudtiering für diesen Serverendpunkt deaktiviert ist. In diesem Modus wird vermieden, den Namespace zuerst abzurufen. Dateien werden nur auf dem lokalen Server angezeigt, wenn Sie vollständig heruntergeladen werden konnten. Dieser Modus ist nützlich, wenn eine Anwendung beispielsweise das Vorhandensein vollständiger Dateien erfordert und keine mehrstufige Dateien in ihrem Namespace tolerieren kann.

Wählen Sie **Erstellen** aus, um den Serverendpunkt hinzuzufügen. Ihre Dateien bleiben jetzt zwischen der Azure-Dateifreigabe und Windows Server synchron. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Führen Sie die folgenden PowerShell-Befehle aus, um den Serverendpunkt zu erstellen, und achten Sie darauf, dass Sie `<your-server-endpoint-path>` und `<your-volume-free-space>` durch die gewünschten Werte ersetzen und die Einstellungen für die optionalen Richtlinien für [Anfänglicher Download](../articles/storage/file-sync/file-sync-server-endpoint-create.md#initial-download-section) und [Anfänglicher Upload](../articles/storage/file-sync/file-sync-server-endpoint-create.md#initial-sync-section) überprüfen.

```powershell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>
# Optional property. Choose from: [NamespaceOnly] default when cloud tiering is enabled. [NamespaceThenModifiedFiles] default when cloud tiering is disabled. [AvoidTieredFiles] only available when cloud tiering is disabled.
$initialDownloadPolicy = "NamespaceOnly"
$initialUploadPolicy = "Merge"
# Optional property. Choose from: [Merge] default for all new server endpoints. Content from the server and the cloud merge. This is the right choice if one location is empty or other server endpoints already exist in the sync group. [ServerAuthoritative] This is the right choice when you seeded the Azure file share (e.g. with Data Box) AND you are connecting the server location you seeded from. This enables you to catch up the Azure file share with the changes that happened on the local server since the seeding.

if ($cloudTieringDesired) {
    # Ensure endpoint path is not the system volume
    $directoryRoot = [System.IO.Directory]::GetDirectoryRoot($serverEndpointPath)
    $osVolume = "$($env:SystemDrive)\"
    if ($directoryRoot -eq $osVolume) {
        throw [System.Exception]::new("Cloud tiering cannot be enabled on the system volume")
    }

    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage `
        -InitialDownloadPolicy $initialDownloadPolicy `
        -InitialUploadPolicy $initialUploadPolicy
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -InitialDownloadPolicy $initialDownloadPolicy
}
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Mit dem Befehl [`az storagesync sync-group server-endpoint`](/cli/azure/storagesync/sync-group/server-endpoint#az_storagesync_sync_group_server_endpoint_create) können Sie einen neuen Serverendpunkt erstellen.

```azurecli
# Create a new sync group server endpoint 
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --name myNewServerEndpointName
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0
                                                 --server-local-path d:\myPath
                                                 --storage-sync-service myStorageSyncServiceNAme
                                                 --sync-group-name mySyncGroupName

# Create a new sync group server endpoint with additional optional parameters
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --storage-sync-service myStorageSyncServiceName \
                                                 --sync-group-name mySyncGroupName \
                                                 --name myNewServerEndpointName \
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0 \
                                                 --server-local-path d:\myPath \
                                                 --cloud-tiering on \
                                                 --volume-free-space-percent 85 \
                                                 --tier-files-older-than-days 15 \
                                                 --initial-download-policy NamespaceOnly [OR] NamespaceThenModifiedFiles [OR] AvoidTieredFiles
                                                 --initial-upload-policy Merge [OR] ServerAuthoritative

```

---
