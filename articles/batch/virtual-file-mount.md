---
title: Einbinden eines virtuellen Dateisystems in einen Pool
description: Erfahren Sie, wie Sie ein virtuelles Dateisystem in einen Batch-Pool einbinden.
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 08/18/2021
ms.openlocfilehash: 7057a982fb3a4b59b8716a373c2ed5172e392cb2
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444063"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>Einbinden eines virtuellen Dateisystems in einen Batch-Pool

Azure Batch unterstützt die Einbindung von Cloudspeicher oder eines externen Dateisystems auf Windows- oder Linux-Computeknoten in Ihren Batch-Pools. Wenn ein Computeknoten einem Pool beitritt, wird das virtuelle Dateisystem eingebunden und als lokales Laufwerk auf diesem Knoten behandelt. Sie können Dateisysteme wie Azure Files, Azure Blob Storage, Network File System (NFS) einschließlich eines [Avere vFXT-Caches](../avere-vfxt/avere-vfxt-overview.md) oder Common Internet File System (CIFS) einbinden.

In diesem Artikel erfahren Sie, wie Sie ein virtuelles Dateisystem mithilfe der [Azure Batch-Verwaltungsbibliothek für .NET](/dotnet/api/overview/azure/batch) in einen Pool von Computeknoten einbinden.

> [!NOTE]
> Das Einbinden eines virtuellen Dateisystems wird nur in Batch-Pools unterstützt, die am oder nach dem 8. August 2019 erstellt wurden. Batch-Pools, die vor diesem Datum erstellt wurden, unterstützen dieses Feature nicht.

## <a name="benefits-of-mounting-on-a-pool"></a>Vorteile der Einbindung in einen Pool

Wenn Sie das Dateisystem in den Pool einbinden, statt Vorgänge eigene Daten aus einem großen Dataset abrufen zu lassen, können Aufgaben einfacher und effizienter auf die erforderlichen Daten zugreifen.

Stellen Sie sich ein Szenario vor, in dem mehrere Aufgaben auf einen gemeinsamen Satz von Daten zugreifen müssen. Ein Beispiel hierfür ist das Rendern eines Films. Jede Aufgabe rendert jeweils ein oder mehrere Frames aus den Szenendateien. Durch die Einbindung eines Laufwerks, das die Szenendateien enthält, können Computeknoten einfacher auf freigegebene Daten zugreifen.

Darüber hinaus kann das zugrunde liegende Dateisystem basierend auf der Leistung und Skalierung (Durchsatz und IOPS) unabhängig ausgewählt und skaliert werden, die für die Anzahl von Computeknoten erforderlich ist, die gleichzeitig auf die Daten zugreifen. Beispielsweise kann ein verteilter speicherinterner [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md)-Cache verwendet werden, um große Renderings im Kinofilmmaßstab mit Tausenden von gleichzeitigen Renderknoten zu unterstützen, die auf lokal gespeicherte Quelldaten zugreifen. Für Daten, die sich bereits in einem cloudbasierten Blobspeicher befinden, kann alternativ [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) verwendet werden, um diese Daten als lokales Dateisystem einzubinden. Blobfuse ist nur auf Linux-Knoten verfügbar. [Azure Files](../storage/files/storage-files-introduction.md) bietet jedoch einen ähnlichen Workflow und ist sowohl unter Windows als auch unter Linux verfügbar.

## <a name="mount-a-virtual-file-system-on-a-pool"></a>Einbinden eines virtuellen Dateisystems in einen Pool  

Durch die Einbindung eines virtuellen Dateisystems in einen Pool wird das Dateisystem für jeden Computeknoten im Pool verfügbar. Das Dateisystem wird entweder konfiguriert, wenn ein Computeknoten einem Pool beitritt (hinzugefügt wird) oder wenn der Knoten neu gestartet oder ein Reimaging durchgeführt wird.

Um ein Dateisystem in einen Pool einzubinden, erstellen Sie ein `MountConfiguration`-Objekt. Wählen Sie das Objekt aus, das Ihrem virtuellen Dateisystem entspricht: `AzureBlobFileSystemConfiguration`, `AzureFileShareConfiguration`, `NfsMountConfiguration` oder `CifsMountConfiguration`.

Für alle MountConfiguration-Objekte sind die folgenden Basisparameter erforderlich. Einige Einbindungskonfigurationen weisen Parameter auf, die für das verwendete Dateisystem spezifisch sind. Diese werden in den Codebeispielen ausführlicher erläutert.

- **Kontoname oder Kontoquelle**: Zum Einbinden einer virtuellen Dateifreigabe benötigen Sie den Namen des Speicherkontos oder die entsprechende Quelle.
- **Relativer Einbindungspfad oder Quelle**: Der Speicherort des auf dem Computeknoten eingebundenen Dateisystems, relativ zu dem `fsmounts`-Standardverzeichnis, auf das auf dem Knoten über `AZ_BATCH_NODE_MOUNTS_DIR` zugegriffen werden kann. Der genaue Speicherort hängt von dem auf dem Knoten verwendeten Betriebssystem ab. Beispielsweise ist der physische Speicherort auf einem Ubuntu-Knoten `mnt\batch\tasks\fsmounts` zugeordnet, und auf einem CentOS-Knoten ist er `mnt\resources\batch\tasks\fsmounts` zugeordnet.
- **Einbindungsoptionen oder blobfuse-Optionen**: Diese Optionen beschreiben bestimmte Parameter für die Einbindung eines Dateisystems.

Nachdem das `MountConfiguration`-Objekt erstellt wurde, können Sie dem Objekt die `MountConfigurationList`-Eigenschaft zuweisen, wenn Sie den Pool erstellen. Das Dateisystem wird entweder eingebunden, wenn ein Knoten einem Pool beitritt (hinzugefügt wird) oder wenn der Knoten neu gestartet oder ein Reimaging durchgeführt wird.

Beim Einbinden des Dateisystems wird eine Umgebungsvariable vom Typ `AZ_BATCH_NODE_MOUNTS_DIR` erstellt, die auf den Speicherort der eingebundenen Dateisysteme sowie auf Protokolldateien verweist, die für die Problembehandlung und das Debuggen hilfreich sind. Protokolldateien werden im Abschnitt [Diagnostizieren von Einbindungsfehlern](#diagnose-mount-errors) ausführlicher erläutert.  

> [!IMPORTANT]
> Die maximale Anzahl eingebundener Dateisysteme in einem Pool beträgt 10. Ausführliche Informationen und andere Grenzwerte finden Sie unter [Batch-Dienst – Kontingente und Limits](batch-quota-limit.md#other-limits).

## <a name="examples"></a>Beispiele

In den folgenden Codebeispielen wird das Einbinden verschiedener Dateifreigaben in einen Pool von Computeknoten veranschaulicht.

### <a name="azure-files-share"></a>Azure Files-Freigabe

Azure Files ist das Standardangebot für Azure-Clouddateisysteme. Weitere Informationen zum Abrufen von Parametern im Codebeispiel für die Bereitstellungskonfiguration finden Sie unter [Verwenden einer Azure Files-Freigabe – SMB](../storage/files/storage-how-to-use-files-windows.md) oder [Verwenden einer Azure Files-Freigabe mit – NFS](../storage/files/storage-files-how-to-create-nfs-shares.md).

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

Eine andere Option ist die Verwendung von Azure-Blobspeicher über [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md). Zum Einbinden eines Blobdateisystems ist ein `AccountKey`, ein `SasKey` oder eine `Managed Identity` mit Zugriff auf Ihr Speicherkonto erforderlich.

Informationen zum Abrufen dieser Schlüssel finden Sie unter [Verwalten von Speicherkonto-Zugriffsschlüsseln](../storage/common/storage-account-keys-manage.md), [Gewähren von eingeschränktem Zugriff auf Azure Storage-Ressourcen mithilfe von SAS (Shared Access Signature)](../storage/common/storage-sas-overview.md) und [Konfigurieren verwalteter Identitäten in Azure Batch-Pools](managed-identity-pools.md). Weitere Informationen und Tipps zur Verwendung von blobfuse erhalten Sie beim [blobfuse-Projekt](https://github.com/Azure/azure-storage-fuse).

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

Network File System (NFS) kann auf Poolknoten eingebunden werden, sodass Azure Batch auf herkömmliche Dateisysteme zugreifen kann. Dabei kann es sich um einen einzelnen in der Cloud bereitgestellten NFS-Server oder einen lokalen NFS-Server handeln, auf den über ein virtuelles Netzwerk zugegriffen wird. NFS-Bereitstellungen unterstützen eine verteilte [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md)-In-Memory-Cachelösung für datenintensive HPC-Aufgaben (High Performance Computing) sowie andere NFS-kompatible Standardschnittstellen wie [NFS für Azure Blob](../storage/blobs/network-file-system-protocol-support.md) und [NFS für Azure Files](../storage/files/storage-files-how-to-mount-nfs-shares.md).

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

Das Einbinden von [Common Internet File Systems (CIFS)](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) auf Poolknoten ist eine weitere Möglichkeit, um Zugriff auf herkömmliche Dateisysteme zu ermöglichen. CIFS ist ein Dateifreigabeprotokoll, das einen offenen und plattformübergreifenden Mechanismus zum Anfordern von Netzwerkserverdateien und -diensten bietet. CIFS basiert auf der erweiterten Version des [SMB-Protokolls (Server Message Block)](/windows-server/storage/file-server/file-server-smb-overview) für die Internet- und Intranetdateifreigabe.

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
  - Für die Bereitstellung von Azure Blob oder Azure Files über NFS müssen möglicherweise zusätzliche Netzwerkanforderungen erfüllt sein, z. B. Computeknoten, die das gleiche designierte Subnetz eines virtuellen Netzwerks wie das Speicherkonto verwenden.
- **Common Internet File System (CIFS)** :
  - Erfordert Zugriff auf den TCP-Port 445.
  - VMs müssen Zugriff auf den entsprechenden Paket-Manager haben, um das Paket „cifs-utils“ herunterzuladen. Diese URL kann je nach Betriebssystemversion variieren.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das Einbinden einer Azure Files-Freigabe mit [Windows](../storage/files/storage-how-to-use-files-windows.md) oder [Linux](../storage/files/storage-how-to-use-files-linux.md).
- Erfahren Sie mehr über das Verwenden und Einbinden virtueller [blobfuse](https://github.com/Azure/azure-storage-fuse)-Dateisysteme.
- Lesen Sie die [Übersicht über Network File System](/windows-server/storage/nfs/nfs-overview), um mehr über NFS und die zugehörigen Anwendungen zu erfahren.
- Lesen Sie die [Übersicht über das Microsoft-SMB-Protokoll und das CIFS-Protokoll](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview), um mehr über CIFS zu erfahren.