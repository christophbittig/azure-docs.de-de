---
title: Konfigurieren von Speicher für SQL Server-VMs | Microsoft-Dokumentation
description: In diesem Thema wird beschrieben, wie Azure den Speicher für SQL Server-VMs während der Bereitstellung konfiguriert (Azure Resource Manager-Bereitstellungsmodell). Außerdem wird erläutert, wie Sie den Speicher für Ihre vorhandenen SQL Server-VMs konfigurieren können.
services: virtual-machines-windows
documentationcenter: na
author: bluefooted
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/26/2019
ms.author: pamela
ms.reviewer: mathoma
ms.openlocfilehash: 5a30cfbd95f660e470854977a63b4dc469a3f769
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130162450"
---
# <a name="configure-storage-for-sql-server-vms"></a>Konfigurieren von Speicher für SQL Server-VMs
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In diesem Artikel erfahren Sie, wie Sie den Speicher für Ihre SQL Server-Instanz auf Azure Virtual Machines (VMs) konfigurieren.

SQL Server-VMs, die über Marketplace-Images bereitgestellt werden, folgen automatisch den standardmäßigen [bewährten Methoden für die Speicherung](performance-guidelines-best-practices-storage.md), die während der Bereitstellung geändert werden können. Einige dieser Konfigurationseinstellungen können nach der Bereitstellung geändert werden.


## <a name="prerequisites"></a>Voraussetzungen

Zur Verwendung der Einstellungen für die automatische Speicherkonfiguration muss Ihr virtueller Computer über die folgenden Merkmale verfügen:

* Mit einem [SQL Server-Katalogimage](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo) bereitgestellt oder mit der [SQL IaaS-Erweiterung]() registriert.
* Verwendung des [Resource Manager-Bereitstellungsmodells](../../../azure-resource-manager/management/deployment-models.md)
* Verwendung von [SSD Premium](../../../virtual-machines/disks-types.md)

## <a name="new-vms"></a>Neue virtuelle Computer

In den folgenden Abschnitten wird beschrieben, wie Speicher für neue virtuelle SQL Server-Computer konfiguriert wird.

### <a name="azure-portal"></a>Azure-Portal

Wenn Sie eine Azure-VM mithilfe eines SQL Server-Katalogimages bereitstellen, wählen Sie **Konfiguration ändern** auf der Registerkarte **SQL Server-Einstellungen** aus, um die Seite „Leistungsoptimierte Speicherkonfiguration“ zu öffnen. Sie können die Standardeinstellungen der Werte übernehmen oder den Typ der Datenträgerkonfiguration ändern, der den Anforderungen für Ihre Workload am besten entspricht.

![Screenshot mit Hervorhebung der Registerkarte „SQL Server-Einstellungen“ und der Option „Konfiguration ändern“.](./media/storage-configuration/sql-vm-storage-configuration-provisioning.png)

Wählen Sie unter **Speicheroptimierung** den Typ der Workload aus, für den Sie SQL Server bereitstellen. Mit der Optimierungsoption **Allgemein** verfügen Sie standardmäßig über einen Datenträger mit maximal 5.000 IOPS, und Sie verwenden dasselbe Laufwerk für Ihre Daten, das Transaktionsprotokoll und den TempDB-Speicher.

Wenn Sie entweder **Transaktionale Verarbeitung** (OLTP) oder **Data Warehousing** auswählen, wird ein separater Datenträger für Daten, ein separater Datenträger für das Transaktionsprotokoll und eine lokale SSD für TempDB erstellt. Es gibt keine Speicherunterschiede zwischen **Transaktionale Verarbeitung** und **Datenlagerung**, aber die jeweilige Option ändert Ihre [Stripesetkonfiguration und Ablaufverfolgungsflags](#workload-optimization-settings). Wenn Sie Storage Premium auswählen, wird die Zwischenspeicherung auf *Schreibgeschützt* für das Datenlaufwerk und *Keine* für das Protokolllaufwerk festgelegt, wie unter [Bewährte Methoden für die SQL Server-VM-Leistung](./performance-guidelines-best-practices-checklist.md) beschrieben.

![SQL Server-VM-Speicherkonfiguration während der Bereitstellung](./media/storage-configuration/sql-vm-storage-configuration.png)

Die Datenträgerkonfiguration ist vollständig anpassbar, sodass Sie die Speichertopologie, den Datenträgertyp und den IOPs konfigurieren können, die Sie für Ihre SQL Server-VM-Workload benötigen. Sie haben auch die Möglichkeit, UltraSSD (Vorschau) als Option für den **Datenträgertyp** zu verwenden, wenn sich Ihre SQL Server-VM in einer der unterstützten Regionen (USA, Osten 2, Asien, Südosten und Europa, Norden) befindet und Sie [Ultra-Datenträger für Ihr Abonnement](../../../virtual-machines/disks-enable-ultra-ssd.md) aktiviert haben.

Zusätzlich haben Sie die Möglichkeit, die Zwischenspeicherung für die Datenträger festzulegen. Azure VMs verfügen über eine mehrschichtige Zwischenspeicherungstechnologie namens [Blobcache](../../../virtual-machines/premium-storage-performance.md#disk-caching), wenn sie mit [Premium-Datenträgern](../../../virtual-machines/disks-types.md#premium-ssds) verwendet werden. Blobcache verwendet für das Zwischenspeichern eine Kombination aus RAM des virtuellen Computers und lokalem SSD-Laufwerk.

Die Datenträgerzwischenspeicherung für SSD Premium kann die Werte *ReadOnly*, *ReadWrite* oder *None* aufweisen.

- Zwischenspeicherung vom Typ *ReadOnly* ist sehr vorteilhaft für SQL Server-Datendateien, die in Storage Premium gespeichert sind. Zwischenspeicherung vom Typ *ReadOnly* bringt niedrige Leselatenz, hohe Lese-IOPS und Durchsatz mit sich, da Lesezugriffe aus dem Cache erfolgen, der sich im VM-Arbeitsspeicher und auf der lokalen SSD befindet. Diese Lesezugriffe sind viel schneller als Lesezugriffe vom Datenträger, die aus Azure Blob Storage erfolgen. Storage Premium rechnet die aus dem Cache erfüllten Leseanforderungen nicht zur IOPS- und Durchsatzrate des Datenträgers. Aus diesem Grund kann Ihre Anwendung eine höhere Gesamtrate bei IOPS und Durchsatz erzielen.
- Die Cachekonfiguration *None* sollte für die Datenträger verwendet werden, auf denen sich die SQL Server-Protokolldatei befindet, da die Protokolldatei sequenziell geschrieben wird und nicht von *ReadOnly*-Zwischenspeicherung profitiert.
- Zwischenspeicherung vom Typ *ReadWrite* sollte nicht zum Hosten von SQL Server-Dateien verwendet werden, da SQL Server keine Datenkonsistenz mit dem *ReadWrite*-Cache unterstützt. Schreibvorgänge vergeuden die Kapazität des *ReadOnly*-Blobcaches, und die Latenzzeiten nehmen geringfügig zu, wenn die Schreibvorgänge durch *ReadOnly*-Blobcacheebenen erfolgen.


   > [!TIP]
   > Stellen Sie sicher, dass Ihre Speicherkonfiguration mit den Einschränkungen der ausgewählten VM-Größe übereinstimmt. Die Auswahl von Speicherparametern, die die Leistungsgrenze der VM-Größe überschreiten, führt zur Warnung `The desired performance might not be reached due to the maximum virtual machine disk performance cap`. Verringern Sie den IOPS durch Ändern des Datenträgertyps, oder erhöhen Sie die Leistungsbegrenzung durch Vergrößern der VM-Größe. Dadurch wird die Bereitstellung nicht unterbrochen.


Je nach Ihrer Auswahl führt Azure nach dem Erstellen der VM die folgenden Aufgaben der Speicherkonfiguration durch:

* Erstellt SSD Premium-Datenträger und fügt sie an die VM an.
* Konfiguriert die Datenträger so, dass sie für SQL Server zugänglich sind.
* Konfiguriert die Datenträger in einem Speicherpool basierend auf der angegebenen Größe und den Leistungsanforderungen (IOPS und Durchsatz).
* Ordnet dem Speicherpool ein neues Laufwerk auf dem virtuellen Computer zu.
* Optimiert dieses neue Laufwerk basierend auf Ihrem angegebenen Workloadtyp („Data Warehousing“, „Transaktionsverarbeitung“ oder „Allgemein“).

Eine vollständige exemplarische Vorgehensweise zur Erstellung einer SQL Server-VM im Azure-Portal finden Sie unter [Tutorial zur Bereitstellung](../../../azure-sql/virtual-machines/windows/create-sql-vm-portal.md).

### <a name="resource-manager-templates"></a>Resource Manager-Vorlagen

Wenn Sie die folgenden Resource Manager-Vorlagen verwenden, werden standardmäßig zwei Premium-Datenträger ohne Speicherpoolkonfiguration angefügt. Sie können diese Vorlagen aber anpassen, um die Anzahl von Premium-Datenträgern zu ändern, die an den virtuellen Computer angefügt sind.

* [Create VM with Automated Backup](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-sql-full-autobackup)
* [Create VM with Automated Patching](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-sql-full-autopatching)
* [Create VM with AKV Integration](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-sql-full-keyvault) (Erstellen eines virtuellen Computers mit AKV-Integration)

### <a name="quickstart-template"></a>Schnellstartvorlage

Sie können die folgende Schnellstartvorlage verwenden, um eine SQL Server-VM mithilfe von Speicheroptimierung bereitzustellen.

* [Erstellen einer VM mit Speicheroptimierung](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.sqlvirtualmachine/sql-vm-new-storage/)
* [Erstellen einer VM mit UltraSSD](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.sqlvirtualmachine/sql-vm-new-storage-ultrassd)

## <a name="existing-vms"></a>Vorhandene virtuelle Computer

Für vorhandene SQL Server-VMs können Sie im Azure-Portal einige Speichereinstellungen ändern. Öffnen Sie Ihre [SQL-VM-Ressource](manage-sql-vm-portal.md#access-the-resource), und wählen Sie **Übersicht** aus. Auf der Seite „SQL Server-Übersicht“ wird die aktuelle Speichernutzung Ihrer VM angezeigt. Alle Laufwerke, die auf Ihrer VM vorhanden sind, werden in diesem Diagramm angezeigt. Für jedes Laufwerk wird der Speicherplatz in vier Abschnitten angezeigt:

* SQL data
* SQL-Protokoll
* Andere (anderer Speicher als SQL-Speicher)
* Verfügbar

Wählen Sie zum Ändern der Speichereinstellungen **Konfigurieren** unter **Einstellungen** aus.

![Screenshot mit Hervorhebung der Option „Konfigurieren“ und des Abschnitts „Speichernutzung“.](./media/storage-configuration/sql-vm-storage-configuration-existing.png)

Sie können die Datenträgereinstellungen für die Laufwerke ändern, die während des Erstellungsprozesses der SQL Server-VM konfiguriert wurden. Durch die Auswahl von **Laufwerk erweitern** wird die Seite zur Änderung des Laufwerks geöffnet, auf der Sie den Datenträgertyp ändern und zusätzliche Datenträger hinzufügen können.

![Konfigurieren von Speicher für vorhandene SQL Server-VM](./media/storage-configuration/sql-vm-storage-extend-drive.png)


## <a name="automated-changes"></a>Automatisierte Änderungen

Dieser Abschnitt dient als Referenz für die Änderungen der Speicherkonfiguration, die von Azure automatisch vorgenommen werden, während die SQL Server-VM-Bereitstellung oder -Konfiguration im Azure-Portal durchgeführt wird.

* Azure konfiguriert einen Speicherpool aus dem ausgewählten Speicher Ihrer VM. Im nächsten Abschnitt dieses Themas sind Details zur Speicherpoolkonfiguration angegeben.
* Bei der automatischen Speicherkonfiguration werden immer P30-Datenträger vom Typ [SSD Premium](../../../virtual-machines/disks-types.md) verwendet. Daher besteht eine 1:1-Zuordnung zwischen der ausgewählten TB-Menge und der Anzahl von Datenträgern, die an die VM angefügt sind.

Preisinformationen finden Sie auf der Seite [Preise für Azure Storage](https://azure.microsoft.com/pricing/details/storage) auf der Registerkarte **Disk Storage** .

### <a name="creation-of-the-storage-pool"></a>Erstellung des Speicherpools

In Azure werden die folgenden Einstellungen verwendet, um den Speicherpool auf SQL Server-VMs zu erstellen:

| Einstellung | Wert |
| --- | --- |
| Stripegröße |256 KB (Data Warehousing), 64 KB (Transaktional) |
| Datenträgergrößen |1 TB jeder |
| Cache |Lesen |
| Zuordnungsgröße |Größe der NTFS-Zuordnungseinheit: 64 KB |
| Wiederherstellung | Einfache Wiederherstellung (keine Resilienz) |
| Anzahl von Spalten |Anzahl von Datenträgern für Daten bis zu 8<sup>1</sup> |


<sup>1</sup> Nach der Erstellung des Speicherpools können Sie die Anzahl von Spalten im Speicherpool nicht mehr ändern.


### <a name="workload-optimization-settings"></a>Einstellungen für die Workloadoptimierung

In der folgenden Tabelle sind die drei verfügbaren Optionen für den Workloadtyp und die entsprechenden Optimierungen beschrieben:

| Workloadtyp | BESCHREIBUNG | Optimierungen |
| --- | --- | --- |
| **Allgemein** |Standardeinstellung, die die meisten Workloads unterstützt |Keine |
| **Transaktionale Verarbeitung** |Optimiert den Speicher für herkömmliche OLTP-Datenbankworkloads |Ablaufverfolgungsflag 1117<br/>Ablaufverfolgungsflag 1118 |
| **Data Warehousing** |Optimiert den Speicher für Analyse- und Berichterstellungsworkloads |Ablaufverfolgungsflag 610<br/>Ablaufverfolgungsflag 1117 |

> [!NOTE]
> Beim Bereitstellen eines virtuellen SQL Server-Computers können Sie den Workloadtyp nur angeben, indem Sie ihn im Schritt für die Speicherkonfiguration auswählen.

## <a name="enable-caching"></a>Aktivieren der Zwischenspeicherung

Ändern Sie die Richtlinie für das Zwischenspeichern auf Datenträgerebene. Sie können dies über das Azure-Portal, [PowerShell](/powershell/module/az.compute/set-azvmdatadisk) oder die [Azure CLI](/cli/azure/vm/disk) erreichen.

Gehen Sie folgendermaßen vor, um Ihre Richtlinie für das Zwischenspeichern im Azure-Portal zu ändern:

1. Beenden Sie den SQL Server-Dienst.
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Navigieren Sie zu Ihrem virtuellen Computer, und wählen Sie **Datenträger** unter **Einstellungen** aus.

   ![Screenshot: Blatt „VM-Datenträgerkonfiguration“ im Azure-Portal](./media/storage-configuration/disk-in-portal.png)

1. Wählen Sie in der Dropdownliste die entsprechende Richtlinie für die Zwischenspeicherung für den Datenträger aus.

   ![Screenshot: Konfiguration der Richtlinie für die Zwischenspeicherung im Azure-Portal](./media/storage-configuration/azure-disk-config.png)

1. Nachdem die Änderung wirksam geworden ist, starten Sie die SQL Server-VM neu. Anschließend starten Sie den SQL Server-Dienst.


## <a name="enable-write-accelerator"></a>Aktivieren der Schreibbeschleunigung

Die Schreibbeschleunigung ist ein Datenträgerfeature, das nur für die virtuellen Computer (VMs) der M-Serie verfügbar ist. Der Zweck der Schreibbeschleunigung besteht darin, die E/A-Wartezeit von Schreibvorgängen für Azure Storage Premium zu verbessern, wenn Sie aufgrund von unternehmenskritischen OLTP-Workloads mit hohem Volumen oder Data Warehouse-Umgebungen eine einstellige E/A-Wartezeit benötigen.

Beenden Sie alle SQL Server-Aktivitäten, und fahren Sie den SQL Server-Dienst herunter, bevor Sie Änderungen an der Richtlinie für die Schreibbeschleunigung vornehmen.

Wenn Ihre Datenträger Stripesetdatenträger sind, aktivieren Sie die Schreibbeschleunigung für jeden Datenträger einzeln und Ihre Azure-VM sollte heruntergefahren sein, bevor Sie Änderungen vornehmen.

Gehen Sie folgendermaßen vor, um die Schreibbeschleunigung über das Azure-Portal zu aktivieren:

1. Beenden Sie den SQL Server-Dienst. Wenn Ihre Datenträger Stripesetdatenträger sind, fahren Sie den virtuellen Computer herunter.
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Navigieren Sie zu Ihrem virtuellen Computer, und wählen Sie **Datenträger** unter **Einstellungen** aus.

   ![Screenshot: Blatt „VM-Datenträgerkonfiguration“ im Azure-Portal](./media/storage-configuration/disk-in-portal.png)

1. Wählen Sie die Option zum Zwischenspeichern mit **Schreibbeschleunigung** für Ihren Datenträger im Dropdownmenü aus.

   ![Screenshot: Richtlinie für die Zwischenspeicherung für die Schreibbeschleunigung](./media/storage-configuration/write-accelerator.png)

1. Nachdem die Änderung wirksam geworden ist, starten Sie den virtuellen Computer und den SQL Server-Dienst.

## <a name="disk-striping"></a>Datenträgerstriping

Für einen höheren Durchsatz können Sie zusätzliche Datenträger für Daten hinzufügen und Datenträgerstriping verwenden. Um die Anzahl der Datenträger für Daten zu bestimmen, analysieren Sie den Durchsatz und die Bandbreite, die für Ihre SQL Server-Datendateien erforderlich sind, einschließlich Protokoll und tempdb. Die Grenzwerte für Durchsatz und Bandbreite variieren je nach VM-Größe. Weitere Informationen finden Sie unter [VM-Größe](../../../virtual-machines/sizes.md).


* Verwenden Sie [Speicherplätze](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11)) für Windows 8 und Windows Server 2012 oder höher mit den folgenden Richtlinien:

  1. Legen Sie die Überlappung (Stripesetgröße) auf 64 KB (65.536 Byte) fest, um Leistungseinbußen durch falsche Partitionsausrichtung zu vermeiden. Dies muss mit PowerShell festgelegt werden.

  2. Festgelegte Anzahl der Spalten = Anzahl der physischen Datenträger. Verwenden Sie PowerShell, wenn Sie mehr als 8 Datenträger (keine Server-Manager-Benutzeroberfläche) konfigurieren.

Mit PowerShell erstellen Sie z. B. wie folgt einen neuen Speicherpool mit einer Überlappungsgröße von 64 KB und einer Anzahl der Spalten, die der Anzahl der physischen Datenträger im Speicherpool entspricht:

# <a name="windows-server-2016-"></a>[Windows Server 2016 +](#tab/windows2016)

  ```powershell
  $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}
  
  New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Windows Storage on <VM Name>" `
      -PhysicalDisks $PhysicalDisks | New- VirtualDisk -FriendlyName "DataFiles" `
      -Interleave 65536 -NumberOfColumns $PhysicalDisks .Count -ResiliencySettingName simple `
      –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter `
      -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" `
      -AllocationUnitSize 65536 -Confirm:$false
  ```

In Windows Server 2016 und höher ist der Standardwert für `-StorageSubsystemFriendlyName` `Windows Storage on <VM Name>`.



# <a name="windows-server-2008---2012-r2"></a>[Windows Server 2008 – 2012 R2](#tab/windows2012)



  ```powershell
  $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}
  
  New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces on <VMName>" `
      -PhysicalDisks $PhysicalDisks | New- VirtualDisk -FriendlyName "DataFiles" `
      -Interleave 65536 -NumberOfColumns $PhysicalDisks .Count -ResiliencySettingName simple `
      –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter `
      -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" `
      -AllocationUnitSize 65536 -Confirm:$false 
  ```

In Windows Server 2008 bis 2012 R2 ist der Standardwert für `-StorageSubsystemFriendlyName` `Storage Spaces on <VMName>`. 

---


  * Für Windows 2008 R2 oder früher können Sie dynamische Datenträger (Betriebssystem-Stripesetvolumes) verwenden, und die Stripesetgröße ist immer 64 KB. Diese Option ist seit Windows 8/Windows Server 2012 veraltet. Informationen hierzu finden Sie in der Supporterklärung unter [Virtual Disk Service is transitioning to Windows Storage Management API](/windows/win32/w8cookbook/vds-is-transitioning-to-wmiv2-based-windows-storage-management-api)(Übergang des Diensts für virtuelle Datenträger in die Windows-Speicherverwaltungs-API, in englischer Sprache).

  * Bei Verwendung von [Direkte Speicherplätze](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) (Storage Spaces Direct, S2D) mit [SQL Server-Failoverclusterinstanzen](./failover-cluster-instance-storage-spaces-direct-manually-configure.md) müssen Sie einen einzelnen Pool konfigurieren. Auch wenn für einen einzelnen Pool unterschiedliche Volumes erstellt werden können, weisen diese alle die gleichen Merkmale (z.B. die gleiche Cacherichtlinie) auf.

  * Bestimmen Sie auf Basis der erwarteten Auslastung die Anzahl der jedem Speicherpool zugeordneten Datenträger. Bedenken Sie, dass verschiedene VM-Größen unterschiedlich viele angefügte Datenträger für Daten unterstützen. Weitere Informationen finden Sie unter [Größen für virtuelle Computer](../../../virtual-machines/sizes.md?toc=/azure/virtual-machines/windows/toc.json).


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Ausführen von SQL Server auf virtuellen Azure-Computern finden Sie unter [SQL Server auf virtuellen Azure-Computern](sql-server-on-azure-vm-iaas-what-is-overview.md).

