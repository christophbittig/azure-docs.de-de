---
title: Erstellen der FCI mit „Direkte Speicherplätze“
description: Verwenden Sie „Direkte Speicherplätze“, um eine Failoverclusterinstanz (FCI) mit SQL Server auf Azure-VMs zu erstellen.
services: virtual-machines
documentationCenter: na
author: rajeshsetlem
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.custom: na, devx-track-azurepowershell
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/10/2021
ms.author: rsetlem
ms.reviewer: mathoma
ms.openlocfilehash: 56a939e11c9daabeb44da7fa79b6e05841401ab3
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158877"
---
# <a name="create-an-fci-with-storage-spaces-direct-sql-server-on-azure-vms"></a>Erstellen einer FCI mit „Direkte Speicherplätze“ (SQL-Server auf Azure-VMs)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!TIP]
> Eliminieren Sie die Notwendigkeit eines Azure Load Balancer oder eines verteilten Netzwerknamens (DNN) für Ihre Failover-Cluster-Instanz, indem Sie Ihre SQL Server-VMs in [mehreren Subnetzen](failover-cluster-instance-prepare-vm.md#subnets) innerhalb desselben virtuellen Azure-Netzwerks erstellen.

In diesem Artikel wird erläutert, wie Sie mit SQL-Server auf virtuellen Azure-Computern (VMs) eine Failoverclusterinstanz (FCI) mit [Direkte Speicherplätze](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) erstellen. „Direkte Speicherplätze“ fungiert hierbei als softwarebasiertes virtuelles Storage Area Network (VSAN), in dem der Speicher (Datenträger) zwischen den Knoten (Azure-VMs) in einem Windows-Cluster synchronisiert wird. 

Weitere Informationen finden Sie in der Übersicht zu [FCI mit SQL Server auf Azure-VMs](failover-cluster-instance-overview.md) und über [Bewährte Methoden für Cluster](hadr-cluster-best-practices.md). 

> [!NOTE]
> Sie können Ihre Failoverclusterinstanzlösung jetzt mithilfe von Azure Migrate per Lift-und-Shift-Verfahren zu SQL Server auf Azure-VMs verschieben. Weitere Informationen finden Sie unter [Migrieren einer Failoverclusterinstanz](../../migration-guides/virtual-machines/sql-server-failover-cluster-instance-to-sql-on-azure-vm.md). 


## <a name="overview"></a>Übersicht 

[Direkte Speicherplätze (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) unterstützt zwei Arten von Architekturen: „konvergent“ und „hyperkonvergent“. In einer hyperkonvergenten Infrastruktur wird der Speicher auf denselben Servern angeordnet, auf denen die gruppierte Anwendung gehostet wird, sodass sich auf jedem SQL-Server-FCI-Knoten Speicher befindet. 

Im folgenden Diagramm wird die vollständige Lösung veranschaulicht, wobei hyperkonvergentes „Direkte Speicherplätze“ mit SQL Server auf Azure-VMs verwendet wird: 

![Diagramm der vollständigen Lösung mit hyperkonvergentem „Direkte Speicherplätze“](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/00-sql-fci-s2d-complete-solution.png)

Das obige Diagramm zeigt die folgenden Ressourcen in derselben Ressourcengruppe:

- Zwei virtuelle Computer in einem Windows Server-Failovercluster. Wenn sich ein virtueller Computer in einem Failovercluster befindet, wird dieser auch als *Clusterknoten* oder *Knoten* bezeichnet.
- Jeder virtuelle Computer verfügt mindestens über zwei Datenträger für Daten.
- Direkte Speicherplätze synchronisiert die Daten auf dem Datenträger und stellt den synchronisierten Speicher als Speicherpool dar.
- Der Speicherpool stellt ein freigegebenes Clustervolume (CSV) für den Failovercluster dar.
- Die SQL Server-FCI-Clusterrolle verwendet das CSV für die Datenträger für Daten.
- Ein Azure Load Balancer, um die IP-Adresse für die SQL Server FCI für ein einzelnes Subnetz-Szenario zu halten.
- Eine Azure-Verfügbarkeitsgruppe enthält alle Ressourcen.

 > [!NOTE]
> Sie können diese ganze Lösung in Azure über eine Vorlage erstellen. Ein Beispiel für eine Vorlage ist auf der GitHub-Seite [Azure-Schnellstartvorlagen](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad) verfügbar. Dieses Beispiel wurde nicht für eine bestimmte Workload entworfen und auch nicht entsprechend getestet. Sie können die Vorlage ausführen, um eine SQL Server-FCI mit einer Verbindung für „Direkte Speicherplätze“-Speicher mit Ihrer Domäne zu erstellen. Sie können die Vorlage auswerten und für Ihre Zwecke anpassen.


## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie die in diesem Artikel aufgeführten Anweisungen ausführen, sollten Sie über Folgendes verfügen:

- Ein Azure-Abonnement. [Kostenlos](https://azure.microsoft.com/free/) einsteigen. 
- [Mindestens zwei vorbereitete Windows Azure-VMs](failover-cluster-instance-prepare-vm.md) in einer [Verfügbarkeitsgruppe](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set)
- Ein Konto mit Berechtigungen zum Erstellen von Objekten auf virtuellen Azure-Computern und in Active Directory
- Die neueste Version von [PowerShell](/powershell/azure/install-az-ps). 

## <a name="create-windows-failover-cluster"></a>Windows Failover Cluster erstellen

Die Schritte zur Erstellung Ihres Windows Server Failover-Clusters hängen davon ab, ob Sie Ihre SQL Server-VMs in einem einzelnen Subnetz oder in mehreren Subnetzen bereitgestellt haben. Um Ihren Cluster zu erstellen, befolgen Sie die Schritte in der Anleitung entweder für ein [Multi-Subnetz-Szenario](availability-group-manually-configure-tutorial-multi-subnet.md#add-failover-cluster-feature) oder für ein [Einzel-Subnetz-Szenario](availability-group-manually-configure-tutorial-single-subnet.md#create-the-cluster). Obwohl sich diese Anleitungen auf die Erstellung einer Verfügbarkeitsgruppe beziehen, sind die Schritte zur Erstellung des Clusters die gleichen. 

## <a name="configure-quorum"></a>Konfigurieren des Quorums

Obwohl der Datenträgerzeuge die resilienteste Option für das Quorum ist, wird er für Failoverclusterinstanzen, die mit direkten Speicherplätzen konfiguriert sind, nicht unterstützt. Daher ist der Cloudzeuge die empfohlene Quorumlösung für eine derartige Clusterkonfiguration für SQL Server auf Azure-VMs.

Wenn Sie im Cluster über eine gerade Anzahl von Stimmen verfügen, konfigurieren Sie die [Quorumlösung](hadr-cluster-quorum-configure-how-to.md), die Ihren Geschäftsanforderungen am besten entspricht. Weitere Informationen finden Sie unter [Quorum mit SQL Server-VMs](hadr-windows-server-failover-cluster-overview.md#quorum). 

## <a name="validate-the-cluster"></a>Überprüfen des Clusters

Validieren Sie den Cluster in der Failover Cluster Manager-Benutzeroberfläche oder mithilfe von PowerShell.

Um den Cluster über die Benutzeroberfläche zu validieren, gehen Sie auf einem der beiden virtuellen Computer folgendermaßen vor:

1. Wählen Sie unter **Server-Manager** die Option **Tools** aus, und wählen Sie dann **Failovercluster-Manager** aus.
1. Wählen Sie unter **Failovercluster-Manager** die Option **Aktion** aus, und wählen Sie dann **Konfiguration überprüfen** aus.
1. Wählen Sie **Weiter** aus.
1. Geben Sie unter **Server oder Cluster auswählen** die Namen der beiden virtuellen Computer ein.
1. Wählen Sie unter **Testoptionen** die Option **Nur ausgewählte Tests ausführen** aus. 
1. Wählen Sie **Weiter** aus.
1. Wählen Sie unter **Testauswahl** alle Tests aus, ausgenommen **Speicher** (siehe folgende Abbildung):

   ![Auswählen von Tests zur Überprüfung des Clusters](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/10-validate-cluster-test.png)

1. Wählen Sie **Weiter** aus.
1. Wählen Sie unter **Bestätigung** die Option **Weiter** aus.

    Der **Konfigurationsüberprüfungs-Assistent** führt die Validierungstests aus.

Führen Sie zum Validieren des Clusters mit PowerShell das folgende Skript in einer PowerShell-Administratorsitzung auf einem der virtuellen Computer aus:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```


## <a name="add-storage"></a>Hinzufügen von Speicher

Die Datenträger für „Direkte Speicherplätze“ müssen leer sein. Sie dürfen keine Partitionen oder andere Daten enthalten. Befolgen Sie die Anweisungen unter [Bereitstellen von „Direkte Speicherplätze“](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-31-clean-drives), um die Datenträger zu bereinigen.

1. [Aktivieren von „Direkte Speicherplätze“](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   Mit dem folgenden PowerShell-Skript wird „Direkte Speicherplätze“ aktiviert:  

   ```powershell
   Enable-ClusterS2D
   ```

   Im **Failovercluster-Manager** wird jetzt der Speicherpool angezeigt.

1. [Erstellen eines Volumes](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-36-create-volumes)

   „Direkte Speicherplätze“ erstellt automatisch einen Speicherpool, wenn Sie „Direkte Speicherplätze“ aktivieren. Sie können jetzt ein Volume erstellen. Das PowerShell-Cmdlet `New-Volume` automatisiert den Volumeerstellungsprozess. Dieser Prozess umfasst Formatieren, Hinzufügen des Volumes zum Cluster und das Erstellen eines CSV. In diesem Beispiel wird ein CSV mit 800 Gigabyte (GB) erstellt:

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Wenn Sie den obigen Befehl ausgeführt haben, wird ein Volume mit 800 GB als Clusterressource bereitgestellt. Das Volume befindet sich unter `C:\ClusterStorage\Volume1\`.

   Dieser Screenshot zeigt ein CSV mit „Direkte Speicherplätze“:

   ![Screenshot eines freigegebenen Clustervolumes mit „Direkte Speicherplätze“](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/15-cluster-shared-volume.png)



## <a name="test-cluster-failover"></a>Testen des Failovers des Clusters

Testen Sie das Failover Ihres Clusters. Klicken Sie im **Failovercluster-Manager** mit der rechten Maustaste auf den Cluster, und wählen Sie **Weitere Aktionen** > **Hauptclusterressource verschieben** > **Knoten auswählen** und dann den anderen Knoten des Clusters aus. Verschieben Sie die Hauptclusterressource auf alle Knoten des Clusters und dann zurück auf den primären Knoten. Wenn Sie den Cluster erfolgreich auf jeden Knoten verschieben können, können Sie SQL Server installieren.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Testen des Clusterfailovers durch Verschieben der Hauptressource auf die anderen Knoten":::

## <a name="create-sql-server-fci"></a>Erstellen der SQL Server-FCI

Nachdem Sie den Failovercluster und alle Clusterkomponenten einschließlich Speicher konfiguriert haben, können Sie die SQL Server-FCI erstellen.

1. Stellen Sie die Verbindung mit dem ersten virtuellen Computer per RDP her.

1. Stellen Sie im **Failovercluster-Manager** sicher, dass sich alle Kernressourcen des Clusters auf dem ersten virtuellen Computer befinden. Verschieben Sie alle Ressourcen auf diesen virtuellen Computer, falls dies erforderlich ist.

1. Suchen Sie nach den Installationsmedien. Wenn für den virtuellen Computer eines der Azure Marketplace-Images verwendet wird, befinden sich die Medien unter `C:\SQLServer_<version number>_Full`. Wählen Sie **Setup** aus.

1. Wählen Sie im **SQL Server-Installationscenter** die Option **Installation** aus.

1. Wählen Sie **Neue SQL Server-Failoverclusterinstallation** aus. Befolgen Sie im Assistenten die Anleitung zum Installieren der SQL Server-FCI.

1. Auf der Seite **Cluster-Netzwerkkonfiguration** variiert die IP, die Sie angeben, je nachdem, ob Ihre SQL Server-VMs in einem einzelnen Subnetz oder in mehreren Subnetzen bereitgestellt wurden. 

   1. Für eine **Einzelsubnetzumgebung** geben Sie die IP-Adresse an, die Sie dem [Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) hinzufügen möchten.
   1. Für eine **Multi-Subnetz-Umgebung**, geben Sie die sekundäre IP-Adresse im Subnetz der _ersten_ SQL Server-VM an, die Sie zuvor als [IP-Adresse des Netzwerknamens der Failover-Cluster-Instanz](failover-cluster-instance-prepare-vm.md#assign-secondary-ip-addresses) festgelegt haben:

   :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/sql-install-cluster-network-secondary-ip-vm-1.png" alt-text="Bieten Sie die sekundäre IP-Adresse im Subnetz der ersten SQL Server-VM an, die Sie zuvor als IP-Adresse des Netzwerknamens der Failover-Cluster-Instanz festgelegt haben":::

1. In **Datenbank-Engine-Konfiguration** müssen sich die FCI-Datenverzeichnisse auf einem geclusterten Speicher befinden. Bei „Direkte Speicherplätze“ handelt es sich nicht um einen freigegebenen Datenträger, sondern um einen Bereitstellungspunkt für ein Volume auf jedem Server. „Direkte Speicherplätze“ synchronisiert das Volume zwischen beiden Knoten. Das Volume wird dem Cluster als CSV angezeigt. Verwenden Sie den CSV-Bereitstellungspunkt für die Datenverzeichnisse.

   ![Datenverzeichnisse](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/20-data-dicrectories.png)

1. Nach Abschluss der Schritte des Assistenten wird vom Setup eine SQL Server-FCI auf dem ersten Knoten installiert.

1. Nachdem die FCI-Installation auf dem ersten Knoten erfolgreich war, verbinden Sie sich über RDP mit dem zweiten Knoten.

1. Öffnen Sie das **SQL Server-Installationscenter**. Wählen Sie **Installation** aus.

1. Wählen Sie **Knoten einem SQL Server-Failovercluster hinzufügen** aus. Folgen Sie den Anweisungen des Assistenten, um SQL Server zu installieren und den Knoten zur FCI hinzuzufügen.

1. Für ein Multi-Subnetz-Szenario geben Sie in **Cluster-Netzwerkkonfiguration** die sekundäre IP-Adresse im Subnetz der _zweiten_ SQL Server-VM ein, die Sie zuvor als [IP-Adresse des Netzwerknamens der Failover-Clusterinstanz](failover-cluster-instance-prepare-vm.md#assign-secondary-ip-addresses)

    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/sql-install-cluster-network-secondary-ip-vm-2.png" alt-text="Geben Sie die sekundäre IP-Adresse im Subnetz des zweiten SQL Server VM-Subnetzes ein, das Sie zuvor als IP-Adresse des Netzwerknamens der Failover-Cluster-Instanz festgelegt haben":::

    Nach Auswahl von **Weiter** in **Cluster-Netzwerkkonfiguration** zeigt das Setup ein Dialogfeld an, das anzeigt, dass das SQL Server-Setup mehrere Subnetze erkannt hat, wie im Beispielbild.  Klicken Sie auf **Ja**, um zu bestätigen. 

    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/sql-install-multi-subnet-confirmation.png" alt-text="Multi-Subnetz-Bestätigung":::

1. Nachdem Sie die Anweisungen des Assistenten ausgeführt haben, fügt das Setup den zweiten SQL Server FCI-Knoten hinzu. 

1. Wiederholen Sie diese Schritte auf allen anderen Knoten, die Sie der SQL Server-Failoverclusterinstanz hinzufügen möchten. 


>[!NOTE]
> Die Galeriebilder des Azure Marketplace werden mit installiertem SQL Server Management Studio geliefert. Wenn Sie kein Marketplace-Image verwendet haben [Laden Sie SQL Server Management Studio (SSMS) herunter ](/sql/ssms/ownload-sql-server-management-studio-ssms).


## <a name="register-with-sql-iaas-extension"></a>Registrieren mit SQL-IaaS-Erweiterung 

Um Ihre SQL Server-VM über das Portal zu verwalten, registrieren Sie sie mit der SQL IaaS Agent-Erweiterung im [leichtgewichtigen Verwaltungsmodus](sql-agent-extension-manually-register-single-vm.md#lightweight-mode), dem derzeit einzigen Modus, der mit FCI und SQL Server auf Azure-VMs unterstützt wird. 


Registrieren Sie eine SQL Server-VM im Modus „Lightweight“ mit PowerShell (-LicenseType `PAYG` oder `AHUB`):

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType PAYG -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Konfigurieren von Konnektivität

Wenn Sie Ihre SQL Server-VMs in mehreren Subnetzen bereitgestellt haben, überspringen Sie diesen Schritt. Wenn Sie Ihre SQL Server-VMs in einem einzigen Subnetz bereitgestellt haben, müssen Sie eine zusätzliche Komponente konfigurieren, um den Datenverkehr zu Ihrer FCI zu leiten. Sie können einen virtuellen Netzwerknamen (VNN) mit einem Azure Load Balancer oder einen verteilten Netzwerknamen für eine Failover-Cluster-Instanz konfigurieren. [Überprüfen Sie die Unterschiede zwischen den beiden](hadr-windows-server-failover-cluster-overview.md#virtual-network-name-vnn) und stellen Sie dann entweder einen [verteilten Netzwerknamen](failover-cluster-instance-distributed-network-name-dnn-configure.md) oder einen [virtuellen Netzwerknamen und Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) für Ihre Failover-Cluster-Instanz bereit.  


## <a name="limitations"></a>Einschränkungen

- Virtuelle Azure-Computer unterstützen Microsoft Distributed Transaction Coordinator (MSDTC) auf Windows Server 2019 mit Speicher auf CSVs und einen [Standardlastenausgleich](../../../load-balancer/load-balancer-overview.md). MSDTC wird unter Windows Server 2016 und früher nicht unterstützt. 
- Datenträger, die als NTFS-formatierte Datenträger angefügt wurden, können nur dann mit „Direkte Speicherplätze“ verwendet werden, wenn die Option für die Datenträgerberechtigung beim Hinzufügen von Speicher zum Cluster deaktiviert ist oder wird. 
- Nur die Registrierung mit der SQL-IaaS-Agent-Erweiterung im [Verwaltungsmodus „Lightweight“](sql-server-iaas-agent-extension-automate-management.md#management-modes) wird unterstützt.
- Failoverclusterinstanzen, die direkte Speicherplätze als freigegebenen Speicher verwenden, unterstützen die Verwendung eines Datenträgerzeugen für das Quorum des Clusters nicht. Verwenden Sie stattdessen einen Cloudzeugen. 

## <a name="next-steps"></a>Nächste Schritte

Wenn „Direkte Speicherplätze“ nicht die richtige FCI-Speicherlösung für Sie ist, können Sie Ihre FCI stattdessen mithilfe von [freigegebenen Azure-Datenträgern](failover-cluster-instance-azure-shared-disks-manually-configure.md) oder [Premium-Dateifreigaben](failover-cluster-instance-premium-file-share-manually-configure.md) erstellen. 

Weitere Informationen finden Sie unter:

- [Windows Server-Failovercluster mit SQL Server auf Azure-VMs](hadr-windows-server-failover-cluster-overview.md)
- [Failoverclusterinstanzen mit SQL Server auf Azure-VMs](failover-cluster-instance-overview.md)
- [AlwaysOn-Failoverclusterinstanzen (SQL Server)](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
- [HADR-Einstellungen für SQL Server auf Azure-VMs](hadr-cluster-best-practices.md)
