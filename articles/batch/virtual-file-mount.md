---
title: Einbinden eines virtuellen Dateisystems in einen Pool
description: Erfahren Sie, wie Sie ein virtuelles Dateisystem in einen Batch-Pool einbinden.
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 11/11/2021
ms.openlocfilehash: ed83f5771a451f92c69ba5f80de7bfa7d8388778
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132369415"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>Einbinden eines virtuellen Dateisystems in einen Batch-Pool

Azure Batch unterstützt die Einbindung von Cloudspeicher oder eines externen Dateisystems auf Windows- oder Linux-Computeknoten in Ihren Batch-Pools. Wenn ein Computeknoten einem Pool beitritt, wird das virtuelle Dateisystem eingebunden und als lokales Laufwerk auf diesem Knoten behandelt. 

Sie können Dateisysteme einbinden, z. B.: 

- Azure Files
- Azure Blob Storage
- Network File System (NFS), einschließlich eines [Avere vFXT Caches](../avere-vfxt/avere-vfxt-overview.md)
- Common Internet File System (CIFS)

In diesem Artikel erfahren Sie, wie Sie ein virtuelles Dateisystem mithilfe der [Azure Batch-Verwaltungsbibliothek für .NET](/dotnet/api/overview/azure/batch) in einen Pool von Computeknoten einbinden.

> [!NOTE]
> Das Einbinden eines virtuellen Dateisystems wird nur in Batch-Pools unterstützt, die am oder nach dem 8. August 2019 erstellt wurden. Batch-Pools, die vor diesem Datum erstellt wurden, unterstützen dieses Feature nicht.

## <a name="benefits-of-mounting-on-a-pool"></a>Vorteile der Einbindung in einen Pool

Wenn Sie das Dateisystem in den Pool einbinden, statt Vorgänge eigene Daten aus einem großen Dataset abrufen zu lassen, können Aufgaben einfacher und effizienter auf die erforderlichen Daten zugreifen.

Stellen Sie sich ein Szenario vor, in dem mehrere Aufgaben auf einen gemeinsamen Satz von Daten zugreifen müssen. Ein Beispiel hierfür ist das Rendern eines Films. Jede Aufgabe rendert jeweils ein oder mehrere Frames aus den Szenendateien. Durch die Einbindung eines Laufwerks, das die Szenendateien enthält, können Computeknoten einfacher auf freigegebene Daten zugreifen.

Darüber hinaus kann das zugrunde liegende Dateisystem basierend auf der Leistung und Skalierung (Durchsatz und IOPS) unabhängig ausgewählt und skaliert werden, die für die Anzahl von Computeknoten erforderlich ist, die gleichzeitig auf die Daten zugreifen. Beispielsweise kann ein verteilter speicherinterner [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md)-Cache verwendet werden, um große Renderings im Kinofilmmaßstab mit Tausenden von gleichzeitigen Renderknoten zu unterstützen, die auf lokal gespeicherte Quelldaten zugreifen. Für Daten, die sich bereits in einem cloudbasierten Blobspeicher befinden, kann alternativ [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) verwendet werden, um diese Daten als lokales Dateisystem einzubinden. Blobfuse ist nur auf Linux-Knoten verfügbar. [Azure Files](../storage/files/storage-files-introduction.md) bietet jedoch einen ähnlichen Workflow und ist sowohl unter Windows als auch unter Linux verfügbar.

## <a name="mount-a-virtual-file-system-on-a-pool"></a>Einbinden eines virtuellen Dateisystems in einen Pool  

Durch die Einbindung eines virtuellen Dateisystems in einen Pool wird das Dateisystem für jeden Computeknoten im Pool verfügbar. Die Konfiguration für das Dateisystem erfolgt, wenn ein Computerknoten einem Pool beitritt, neu gestartet wird oder ein Erneutes Image erstellt wird.

Um ein Dateisystem in einen Pool einzubinden, erstellen Sie ein `MountConfiguration`-Objekt. Wählen Sie das Objekt aus, das Ihrem virtuellen Dateisystem entspricht: `AzureBlobFileSystemConfiguration`, `AzureFileShareConfiguration`, `NfsMountConfiguration` oder `CifsMountConfiguration`.

Für alle MountConfiguration-Objekte sind die folgenden Basisparameter erforderlich. Einige Einbindungskonfigurationen weisen Parameter auf, die für das verwendete Dateisystem spezifisch sind. Diese werden in den Codebeispielen ausführlicher erläutert.

- **Kontoname oder Kontoquelle**: Zum Einbinden einer virtuellen Dateifreigabe benötigen Sie den Namen des Speicherkontos oder die entsprechende Quelle.
- **Relativer Einhängepfad oder Quelle**: Der Speicherort des auf dem Rechenknoten eingehängten Dateisystems, relativ zum Standardverzeichnis `fsmounts`, das auf dem Knoten über `AZ_BATCH_NODE_MOUNTS_DIR` zugänglich ist. Der genaue Speicherort hängt von dem auf dem Knoten verwendeten Betriebssystem ab. Beispielsweise wird der physische Speicherort auf einem Ubuntu-Knoten`mnt\batch\tasks\fsmounts`zugeordnet. Auf einem CentOS-Knoten wird der Speicherort `mnt\resources\batch\tasks\fsmounts`zugeordnet.
- **Einbindungsoptionen oder blobfuse-Optionen**: Diese Optionen beschreiben bestimmte Parameter für die Einbindung eines Dateisystems.

Nachdem das `MountConfiguration`-Objekt erstellt wurde, können Sie dem Objekt die `MountConfigurationList`-Eigenschaft zuweisen, wenn Sie den Pool erstellen. Die Konfiguration für das Dateisystem erfolgt, wenn ein Computerknoten einem Pool beitritt, neu gestartet wird oder ein Erneutes Image erstellt wird.

Beim Einbinden des Dateisystems wird eine Umgebungsvariable vom Typ `AZ_BATCH_NODE_MOUNTS_DIR` erstellt, die auf den Speicherort der eingebundenen Dateisysteme sowie auf Protokolldateien verweist, die für die Problembehandlung und das Debuggen hilfreich sind. Protokolldateien werden im Abschnitt [Diagnostizieren von Einbindungsfehlern](#diagnose-mount-errors) ausführlicher erläutert.  

> [!IMPORTANT]
> Die maximale Anzahl eingebundener Dateisysteme in einem Pool beträgt 10. Ausführliche Informationen und andere Grenzwerte finden Sie unter [Batch-Dienst – Kontingente und Limits](batch-quota-limit.md#other-limits).

## <a name="mount-azure-file-share-with-powershell"></a>Einbinden der Azure-Dateifreigabe mithilfe von PowerShell

Sie können eine Azure-Dateifreigabe in einen Batch-Pool einbinden, indem Sie [Azure PowerShell](/powershell/) oder [Azure Cloud Shell](../cloud-shell/overview.md)verwenden.

# <a name="windows"></a>[Windows](#tab/windows)

1. Melden Sie sich bei Ihrem Azure-Abonnement an.

    ```powershell
    Connect-AzAccount -Subscription "<subscription-ID>"
    ```

1. Abrufen des Kontexts für Ihr Batch-Konto.
    
    ```powershell
    $context = Get-AzBatchAccount -AccountName <batch-account-name>
    ```

1. Erstellen Sie einen Batch-Pool mit den folgenden Einstellungen. Ersetzen Sie die Beispielwerte nach Bedarf durch Ihre eigenen Informationen.

    ```powershell
    $fileShareConfig = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSAzureFileShareConfiguration" -ArgumentList @("<Storage-Account-name>", https://<Storage-Account-name>.file.core.windows.net/batchfileshare1, "S", "Storage-Account-key")
    
    $mountConfig = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSMountConfiguration" -ArgumentList @($fileShareConfig)
    
    $imageReference = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("WindowsServer", "MicrosoftWindowsServer", "2016-Datacenter", "latest")
    
    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageReference, "batch.node.windows amd64")
    
    New-AzBatchPool -Id "<Pool-Name>" -VirtualMachineSize "STANDARD_D2_V2" -VirtualMachineConfiguration $configuration -TargetDedicatedComputeNodes 1 -MountConfiguration @($mountConfig) -BatchContext $Context
    ```

1. Greifen Sie über den direkten Pfad Ihres Laufwerks auf die Bereitstellungsdateien zu. Beispiel:

    ```powershell
    cmd /c "more S:\folder1\out.txt & timeout /t 90 > NULL"
    ```

1. Überprüfen Sie, ob die Ausgabedatei korrekt ist.

1. Wenn Sie Remotedesktopprotokoll (RDP) oder SSH verwenden, fügen Sie Anmeldeinformationen hinzu, um direkt auf das `S` Laufwerk zuzugreifen. Der Azure Batch-Agent gewährt nur Zugriff auf Azure Batch Aufgaben in Windows. Wenn Sie über RDP eine Verbindung mit dem Knoten herstellen, hat Ihr Benutzerkonto keinen automatischen Zugriff auf das Einbindungslaufwerk. 

    Hinzufügen `cmdkey` Ihrer Anmeldeinformationen. Ersetzen Sie die Beispielwerte nach Bedarf durch Ihre eigenen Informationen.

    ```powershell
    cmdkey /add:"<storage-account-name>.file.core.windows.net" /user:"Azure\<storage-account-name>" /pass:"<storage-account-key>"
    ```

# <a name="linux"></a>[Linux](#tab/linux)

1. Melden Sie sich bei Ihrem Azure-Abonnement an.

    ```powershell
    Connect-AzAccount -Subscription "<subscription-ID>"
    ```

1. Abrufen des Kontexts für Ihr Batch-Konto.

    ```powershell
    $context = Get-AzBatchAccount -AccountName <batch-account-name>
    ```

1. Erstellen Sie einen Batch-Pool mit den folgenden Einstellungen. Ersetzen Sie die Beispielwerte nach Bedarf durch Ihre eigenen Informationen.

    ```powershell
    $fileShareConfig = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSAzureFileShareConfiguration" -ArgumentList @("<Storage-Account-name>", https://<Storage-Account-name>.file.core.windows.net/batchfileshare1, "S", "<Storage-Account-key>", "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp")
    
    $mountConfig = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSMountConfiguration" -ArgumentList @($fileShareConfig)
    
    $imageReference = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("ubuntuserver", "canonical", "20.04-lts", "latest")
    
    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageReference, "batch.node.ubuntu 20.04")
    
    New-AzBatchPool -Id "<Pool-Name>" -VirtualMachineSize "STANDARD_D2_V2" -VirtualMachineConfiguration $configuration -TargetDedicatedComputeNodes 1 -MountConfiguration @($mountConfig) -BatchContext $Context
     
    ```

1. Greifen Sie mithilfe der Umgebungsvariablen auf die Bereitstellungsdateien `AZ_BATCH_NODE_MOUNTS_DIR`zu. Beispiel:

    ```bash
    /bin/bash -c 'more $AZ_BATCH_NODE_MOUNTS_DIR/S/folder1/out.txt; sleep 20s'
    ```

    Optional können Sie auch über den direkten Pfad auf die Bereitstellungsdateien zugreifen.

1. Überprüfen Sie, ob die Ausgabedatei korrekt ist.

1. Wenn Sie RDP oder SSH verwenden, können Sie manuell direkt auf das `S` Laufwerk zugreifen. Verwenden Sie den Pfad `/mnt/batch/tasks/fsmounts/S`.

---

### <a name="troubleshoot-powershell-mounting"></a>Problembehandlung bei der PowerShell-Einbindung

Wenn Sie eine Azure-Dateifreigabe mit PowerShell oder Cloud Shell in einen Batch-Pool einbinden, wird möglicherweise der folgende Fehler angezeigt:

```text
Mount Configuration Error | An error was encountered while configuring specified mount(s)
Message: System error (out of memory, cannot fork, no more loop devices)
MountConfigurationPath: S
```

Wenn dieser Fehler auftritt, stellen Sie eine RDP- oder SSH-Verbindung mit dem Knoten her, um die zugehörigen Protokolldateien zu überprüfen. Der Batch-Agent implementiert die Einbindung auf Windows und Linux unterschiedlich. Unter Linux installiert Batch das Paket `cifs-utils`. Anschließend gibt Batch den Einhängebefehl aus. Auf Windows verwendet Batch `cmdkey` um Ihre Batch-Kontoanmeldeinformationen hinzuzufügen. Dann gibt Batch den Bereitstellungsbefehl über `net use` aus. Beispiel:

```powershell
net use S: \\<storage-account-name>.file.core.windows.net\<fileshare> /u:AZURE\<storage-account-name> <storage-account-key>
```

# <a name="windows"></a>[Windows](#tab/windows)

1. Verbinden über RDP zum Knoten.

1. Öffnen Sie die Protokolldatei, `fshare-S.log`. Der Dateipfad ist `D:\batch\tasks\fsmounts`.

1. Überprüfen Sie die Fehlermeldungen. Beispiel:

    ```text
    CMDKEY: Credential added successfully.
    
    System error 86 has occurred.
    
    The specified network password is not correct.
    ```

1. Beheben Sie das Problem mithilfe von [Troubleshoot Azure Files Problems in Windows Server Message Block (SMB)](../storage/files/storage-troubleshoot-windows-file-connection-problems.md).

# <a name="linux"></a>[Linux](#tab/linux)

1. Verbinden über SSH zum Knoten.

1. Öffnen Sie die Protokolldatei, `fshare-S.log`. Der Dateipfad ist `/mnt/batch/tasks/fsmounts`.

1. Überprüfen Sie die Fehlermeldungen. Beispiel: `mount error(13): Permission denied`.

1. Beheben Sie das Problem mit [Problembehandlung von Problemen mit Azure-Dateien unter Linux (SMB)](../storage/files/storage-troubleshoot-linux-file-connection-problems.md).

---

Wenn Sie RDP oder SSH nicht verwenden können, um die Protokolldateien auf dem Knoten zu überprüfen, überprüfen Sie die Batch-Protokolle direkt. Verwenden Sie diese Methode sowohl für Windows- als auch für Linux-Protokolle.

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com) an.

1. Geben Sie in die Suchleiste ein und wählen Sie **Batch-Konten**.

1. Wählen Sie auf der Seite **Batch-Konten** das Konto mit Ihrem Batch-Pool aus.

1. Wählen Sie im Menü der Seite "Sammelkonto" unter **Funktionen** die Option **Pools**.

1. Wählen Sie den Namen des Pools aus.

1. Wählen Sie im Menü der Batchpool-Seite unter **Allgemein** die Option **Pool**.

1. Wählen Sie den Namen des Pools aus.

1. Wählen Sie auf der Seite **Übersicht** über den Pool **Hochladen der Batchprotokolle** aus.

1. Wählen Sie im **Bereich Hochladen der Batchprotokolle** Ihren Azure Storage Container aus. Wählen Sie dann **Speichercontainer** auswählen.

1. Wählen Sie die Protokolldateien aus, und laden Sie sie aus dem Speichercontainer herunter. 

1. Öffnen Sie `agent-debug.log`.

1. Überprüfen Sie die Fehlermeldungen. Beispiel: 

    ```text
    ..20210322T113107.448Z.00000000-0000-0000-0000-000000000000.ERROR.agent.mount.filesystems.basefilesystem.basefilesystem.py.run_cmd_persist_output_async.59.2912.MainThread.3580.Mount command failed with exit code: 2, output:
    
    CMDKEY: Credential added successfully.
    
    System error 86 has occurred.
    
    The specified network password is not correct.
    ```

1. Beheben Sie das Problem mit [Beseitigung von Problemen mit Azure-Dateien unter Windows (SMB)](../storage/files/storage-troubleshoot-windows-file-connection-problems.md) oder [Beseitigung von Problemen mit Azure-Dateien unter Linux (SMB)](../storage/files/storage-troubleshoot-linux-file-connection-problems.md).

Wenn Sie die Ursache des Fehlers immer noch nicht finden können, können Sie die Dateifreigabe stattdessen [manuell mit PowerShell einbinden.](#manually-mount-file-share-with-powershell)

### <a name="manually-mount-file-share-with-powershell"></a>Manuelles Einbinden einer Dateifreigabe mit PowerShell

Wenn Sie Einbindungsfehler nicht mit PowerShell diagnostizieren oder beheben können, können Sie die Dateifreigabe manuell einbinden.

# <a name="windows"></a>[Windows](#tab/windows)

1. Erstellen Sie einen Pool ohne Einbindungskonfiguration. Beispiel:

    ```powershell
    $imageReference = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("WindowsServer", "MicrosoftWindowsServer", "2016-Datacenter", "latest")
    
    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageReference, "batch.node.windows amd64")
    
    New-AzBatchPool -Id "<Pool-Name>" -VirtualMachineSize "STANDARD_D2_V2" -VirtualMachineConfiguration $configuration -TargetDedicatedComputeNodes 1  -BatchContext $Context
    ```

1. Warten Sie, bis sich der Knoten im **Leerlaufzustand** befindet.

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com) an.

1. Geben Sie in die Suchleiste die **Batch-Konten** ein und wählen sie.

1. Wählen Sie den Namen des Speicherkontos mit Ihrer Dateifreigabe aus.

1. Wählen Sie im Navigationsmenü des Speicherkontos unter **Datenspeicher** die Option **Container** aus.

1. Wählen Sie auf der Seite **Dateifreigaben** die Option Dateifreigabe aus.

1. Wählen Sie auf der Seite **Übersicht** die Option **Verbinden** aus.

1. Wählen Sie im **Bereich Verbinden** die Registerkarte **Windows** aus.

1. Geben Sie unter **Laufwerkbuchstabe** das Laufwerk ein, das Sie verwenden möchten. Der Standardwert lautet `Z`.

1. Wählen Sie unter **Authentifizierungsmethode** aus, wie Sie eine Verbindung mit der Dateifreigabe herstellen möchten.

1. Kopieren Sie den PowerShell-Befehl zum Einbinden der Dateifreigabe.

1. Verbinden über RDP zum Knoten.

1. Führen Sie den Befehl aus, den Sie zum Einbinden der Dateifreigabe kopiert haben.

1. Beachten Sie alle Fehlermeldungen in der Ausgabe. Verwenden Sie diese Informationen, um netzwerkbezogene Probleme zu beheben.

# <a name="linux"></a>[Linux](#tab/linux)


1. Erstellen Sie einen Pool ohne Einbindungskonfiguration. Beispiel:

    ```bash
    $imageReference = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("ubuntuserver", "canonical", "20.04-lts", "latest")
    
    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageReference, "batch.node.ubuntu 20.04")
    
    New-AzBatchPool -Id "<Pool-Name>" -VirtualMachineSize "STANDARD_D2_V2" -VirtualMachineConfiguration $configuration -TargetDedicatedComputeNodes 1 -BatchContext $Context
    ```

1. Warten Sie, bis sich der Knoten im **Leerlaufzustand** befindet.

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com) an.

1. Geben Sie in die Suchleiste die **Batch-Konten** ein und wählen sie.

1. Wählen Sie den Namen des Speicherkontos mit Ihrer Dateifreigabe aus.

1. Wählen Sie im Navigationsmenü des Speicherkontos unter **Datenspeicher** die Option **Container** aus.

1. Wählen Sie auf der Seite **Dateifreigaben** die Option Dateifreigabe aus.

1. Wählen Sie auf der Seite **Übersicht** die Option **Verbinden** aus.

1. Wählen Sie im **Bereich Verbinden** die Registerkarte **Windows** aus.

1. Geben Sie den **Einhängepunkt** ein, den Sie verwenden möchten.

1. Kopieren Sie den PowerShell-Befehl zum Einhängen der Dateifreigabe.

1. Verbinden über SSH zum Knoten.

1. Führen Sie den Befehl aus, den Sie zum Einbinden der Dateifreigabe kopiert haben.

1. Beachten Sie alle Fehlermeldungen in der Ausgabe. Verwenden Sie diese Informationen, um netzwerkbezogene Probleme zu beheben.


---

## <a name="examples"></a>Beispiele

In den folgenden Codebeispielen wird das Einhängen verschiedener Dateifreigaben in einem Pool von Computerknoten veranschaulicht.

### <a name="azure-files-share"></a>Azure Files-Freigabe

Azure Files ist das Standardangebot für Azure-Clouddateisysteme. Informationen zu den Parametern im Codebeispiel finden Sie unter [Verwendung einer Azure-Dateifreigabe - SMB](../storage/files/storage-how-to-use-files-windows.md) oder [Verwendung einer Azure-Dateifreigabe mit - NFS](../storage/files/storage-files-how-to-create-nfs-shares.md).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureFileShareConfiguration = new AzureFileShareConfiguration
            {
                AccountName = "{storage-account-name}",
                AzureFileUrl = "https://{storage-account-name}.file.core.windows.net/{file-share-name}",
                AccountKey = "{storage-account-key}",
                RelativeMountPath = "S",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp"
            },
        }
    }
}
```

### <a name="azure-blob-container"></a>Azure-Blobcontainer

Eine andere Option ist die Verwendung von Azure-Blobspeicher über [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md). Zum Einhängen eines Blobdateisystems ist ein `AccountKey`, ein `SasKey` oder eine `Managed Identity` mit Zugriff auf Ihr Speicherkonto erforderlich.

Informationen zum Abrufen dieser Schlüssel finden Sie unter: 

- [Verwalten von Zugriffsschlüsseln für Speicherkonten](../storage/common/storage-account-keys-manage.md)
- [Gewähren von eingeschränktem Zugriff auf Azure Storage-Ressourcen mithilfe von SAS (Shared Access Signature)](../storage/common/storage-sas-overview.md)
- [Konfigurieren Sie verwaltete Identitäten in Batch-Pools](managed-identity-pools.md). 

Weitere Informationen und Tipps zur Verwendung von blobfuse erhalten Sie beim [blobfuse-Projekt](https://github.com/Azure/azure-storage-fuse).

Um Standardzugriff auf das über blobfuse eingebundene Verzeichnis zu erhalten, müssen Sie die Aufgabe als **Administrator** ausführen. Blobfuse bindet das Verzeichnis im Benutzerbereich ein, und bei der Poolerstellung wird es als Stamm eingebunden. Unter Linux sind alle **Administratoraufgaben** stammbasiert. Eine Beschreibung aller Optionen für das FUSE-Modul finden Sie auf der [FUSE-Referenzseite](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html).

Weitere Informationen und Tipps zur Verwendung von blobfuse finden Sie in den [häufig gestellten Fragen zur Behandlung von Problemen](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ) mit blobfuse. Informationen zu aktuellen blobfuse-Problemen und deren Lösungen finden Sie auch im Artikel zu [GitHub-Problemen im blobfuse-Repository](https://github.com/Azure/azure-storage-fuse/issues).

> [!NOTE]
> Das folgende Beispiel zeigt einen `AccountKey`, einen `SasKey` und eine `IdentityReference`, die sich aber gegenseitig ausschließen. Es kann nur eine dieser Optionen angegeben werden.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureBlobFileSystemConfiguration = new AzureBlobFileSystemConfiguration
            {
                AccountName = "StorageAccountName",
                ContainerName = "containerName",
                AccountKey = "StorageAccountKey",
                SasKey = "SasKey",
                IdentityReference = new ComputeNodeIdentityReference("/subscriptions/SUB/resourceGroups/RG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity-name"),
                RelativeMountPath = "RelativeMountPath",
                BlobfuseOptions = "-o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120 "
            },
        }
    }
}
```

> [!TIP]
>Wenn Sie eine verwaltete Identität verwenden, stellen Sie sicher, dass die Identität [dem Pool zugewiesen](managed-identity-pools.md) wurde, damit sie auf der VM verfügbar ist, die die Einbindung vornimmt. Die Identität muss über die Rolle `Storage Blob Data Contributor` verfügen, damit sie ordnungsgemäß funktioniert.

### <a name="network-file-system"></a>Network File System

Network File System (NFS) kann auf Poolknoten eingebunden werden, sodass Azure Batch auf herkömmliche Dateisysteme zugreifen kann. Bei dieser Einrichtung kann es sich um einen einzelnen NFS-Server handeln, der in der Cloud bereitgestellt wird, oder um einen NFS-Server vor Ort, auf den über ein virtuelles Netzwerk zugegriffen wird. NFS Einhängepunkte unterstützen [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md). Avere vFXT ist eine verteilte In-Memory-Cachelösung für datenintensive HPC-Aufgaben (High Performance Computing) und andere NFS-kompatible Standardschnittstellen. Zum Beispiel, [NFS für Azure Blob](../storage/blobs/network-file-system-protocol-support.md) und [NFS für Azure Files](../storage/files/storage-files-how-to-mount-nfs-shares.md).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            NfsMountConfiguration = new NFSMountConfiguration
            {
                Source = "source",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "options ver=3.0"
            },
        }
    }
}
```

### <a name="common-internet-file-system"></a>Common Internet File System

Das Einbinden von [Common Internet File Systems (CIFS)](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) auf Poolknoten ist eine weitere Möglichkeit, um Zugriff auf herkömmliche Dateisysteme zu ermöglichen. CIFS ist ein Dateifreigabeprotokoll, das einen offenen und plattformübergreifenden Mechanismus zum Anfordern von Netzwerkserverdateien und -diensten bietet. CIFS basiert auf der erweiterten Version des [SMB-Protokolls](/windows-server/storage/file-server/file-server-smb-overview), das für die gemeinsame Nutzung von Dateien im Internet und Intranet gedacht ist.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            CifsMountConfiguration = new CIFSMountConfiguration
            {
                Username = "StorageAccountName",
                RelativeMountPath = "cifsmountpoint",
                Source = "source",
                Password = "StorageAccountKey",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,serverino"
            },
        }
    }
}
```

## <a name="diagnose-mount-errors"></a>Diagnostizieren von Einbindungsfehlern

Wenn bei einer Einbindungskonfiguration ein Fehler auftritt, führt dies zu einem Fehler des Computeknotens im Pool, und der Knotenstatus wird auf `unusable` festgelegt. Überprüfen Sie zum Diagnostizieren eines Einbindungskonfigurationsfehlers die [`ComputeNodeError`](/rest/api/batchservice/computenode/get#computenodeerror)-Eigenschaft auf Details zum Fehler.

Wenn Sie die Protokolldateien für das Debuggen erhalten möchten, verwenden Sie [OutputFiles](batch-task-output-files.md), um die Protokolldateien (`*.log`) hochzuladen. Die Protokolldateien (`*.log`) enthalten Informationen zur Dateisystemeinbindung am Speicherort `AZ_BATCH_NODE_MOUNTS_DIR`. Einbindungsprotokolldateien weisen für jede Einbindung das Format `<type>-<mountDirOrDrive>.log` auf. Die zugehörige Einbindungsprotokolldatei einer `cifs`-Einbindung im Einbindungsverzeichnis `test` heißt beispielsweise `cifs-test.log`.

## <a name="support-matrix"></a>Unterstützungsmatrix

Azure Batch unterstützt die folgenden virtuellen Dateisystemtypen für Knoten-Agents, die für die jeweiligen Herausgeber und Angebote erstellt werden.

| Betriebssystemtyp | Azure Files-Freigabe | Azure-Blobcontainer | NFS-Einbindung | CIFS-Einbindung |
|---|---|---|---|---|
| Linux | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Windows | :heavy_check_mark: | :x: | :x: | :x: |

## <a name="networking-requirements"></a>Netzwerkanforderungen

Beachten Sie die folgenden Anforderungen, wenn Sie virtuelle Dateibereitstellungen mit [Azure Batch-Pools in einem virtuellen Netzwerk](batch-virtual-network.md) verwenden, und achten Sie darauf, dass kein erforderlicher Datenverkehr blockiert wird.

- **Azure-Dateifreigaben**:
  - Der TCP-Port 445 muss für Datenverkehr zum/vom Diensttag „Storage“ geöffnet sein. Weitere Informationen finden Sie unter [Verwenden einer Azure-Dateifreigabe mit Windows](../storage/files/storage-how-to-use-files-windows.md#prerequisites).
- **Azure-Blobcontainer**:
  - Der TCP-Port 443 muss für Datenverkehr zum/vom Diensttag „Storage“ geöffnet sein.
  - VMs müssen auf https://packages.microsoft.com zugreifen können, um blobfuse- und gpg-Pakete herunterzuladen. Abhängig von der Konfiguration benötigen Sie u. U. auch Zugriff auf andere URLs, um weitere Pakete herunterzuladen.
- **Network File System (NFS)** :
  - Erfordert Zugriff auf Port 2049 (Standardeinstellung; für Ihre Konfiguration gelten möglicherweise andere Anforderungen).
  - VMs müssen Zugriff auf den entsprechenden Paket-Manager haben, um das Paket „nfs-common“ (für Debian oder Ubuntu) oder „nfs-utils“ (für CentOS) herunterzuladen. Diese URL kann je nach Betriebssystemversion variieren. Abhängig von der Konfiguration benötigen Sie u. U. auch Zugriff auf andere URLs, um weitere Pakete herunterzuladen.
  - Das Einbinden von Azure Blob oder Azure Files über NFS kann mehr Netzwerkanforderungen haben. Beispielsweise benötigen Sie möglicherweise Computerknoten, die das gleiche Subnetz eines virtuellen Netzwerks wie das Speicherkonto verwenden.
- **Common Internet File System (CIFS)** :
  - Erfordert Zugriff auf den TCP-Port 445.
  - VMs müssen Zugriff auf den entsprechenden Paket-Manager haben, um das Paket „cifs-utils“ herunterzuladen. Diese URL kann je nach Betriebssystemversion variieren.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das Einbinden einer Azure Files-Freigabe mit [Windows](../storage/files/storage-how-to-use-files-windows.md) oder [Linux](../storage/files/storage-how-to-use-files-linux.md).
- Erfahren Sie mehr über das Verwenden und Einbinden virtueller [blobfuse](https://github.com/Azure/azure-storage-fuse)-Dateisysteme.
- Lesen Sie die [Übersicht über Network File System](/windows-server/storage/nfs/nfs-overview), um mehr über NFS und die zugehörigen Anwendungen zu erfahren.
- Lesen Sie die [Übersicht über das Microsoft-SMB-Protokoll und das CIFS-Protokoll](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview), um mehr über CIFS zu erfahren.