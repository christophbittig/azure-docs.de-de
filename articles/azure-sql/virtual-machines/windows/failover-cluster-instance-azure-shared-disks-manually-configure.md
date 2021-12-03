---
title: Erstellen einer FCI mit freigegebenen Azure-Datenträgern
description: Verwenden Sie freigegebene Azure-Datenträger, um eine Failoverclusterinstanz (FCI) mit SQL Server in Azure Virtual Machines zu erstellen.
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
ms.openlocfilehash: 142c84fa2006e6a5a99fc2997d62f79376758339
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158554"
---
# <a name="create-an-fci-with-azure-shared-disks-sql-server-on-azure-vms"></a>Erstellen einer FCI mit freigegebenen Azure-Datenträgern (SQL Server auf Azure-VMs)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!TIP]
> Eliminieren Sie die Notwendigkeit eines Azure Load Balancer oder eines verteilten Netzwerknamens (DNN) für Ihre Failover-Cluster-Instanz, indem Sie Ihre SQL Server-VMs in [mehreren Subnetzen](failover-cluster-instance-prepare-vm.md#subnets) innerhalb desselben virtuellen Azure-Netzwerks erstellen.

In diesem Artikel wird erläutert, wie Sie auf virtuellen Azure-Computern (Azure Virtual Machines) eine SQL Server-Failoverclusterinstanz (FCI) mit SQL Server mithilfe freigegebener Azure-Datenträger erstellen. 

Weitere Informationen finden Sie in der Übersicht zu [FCI mit SQL Server auf Azure-VMs](failover-cluster-instance-overview.md) und über [Bewährte Methoden für Cluster](hadr-cluster-best-practices.md). 

> [!NOTE]
> Sie können Ihre Failoverclusterinstanzlösung jetzt mithilfe von Azure Migrate per Lift-und-Shift-Verfahren zu SQL Server auf Azure-VMs verschieben. Weitere Informationen finden Sie unter [Migrieren einer Failoverclusterinstanz](../../migration-guides/virtual-machines/sql-server-failover-cluster-instance-to-sql-on-azure-vm.md). 

## <a name="prerequisites"></a>Voraussetzungen 

Bevor Sie die in diesem Artikel aufgeführten Anweisungen ausführen, sollten Sie über Folgendes verfügen:

- Ein Azure-Abonnement. [Kostenlos](https://azure.microsoft.com/free/) einsteigen. 
- [Zwei oder mehr vorbereitete virtuelle Windows Azure-Maschinen](failover-cluster-instance-prepare-vm.md) in einer Verfügbarkeitsgruppe oder Verfügbarkeitszone. 
- Ein Konto mit Berechtigungen zum Erstellen von Objekten auf virtuellen Azure-Computern und in Active Directory
- Die neueste Version von [Azure PowerShell](/powershell/azure/install-az-ps) 

## <a name="add-azure-shared-disk"></a>Hinzufügen eines freigegebenen Azure-Datenträgers

[Einrichten einer verwalteten SSD-Premium-Datenträger mit aktivierter gemeinsamer Festplattenfunktion](../../../virtual-machines/disks-shared-enable.md#deploy-a-premium-ssd-as-a-shared-disk). Legen Sie `maxShares` auf den Wert fest, der **mit der Anzahl der Clusterknoten übereinstimmt**, damit der Datenträger für alle FCI-Knoten freigegeben werden kann. 

## <a name="attach-shared-disk-to-vms"></a>Gemeinsame Festplatte an VMs anhängen

Sobald Sie eine gemeinsam genutzte Festplatte mit maxShares > 1 bereitgestellt haben, können Sie die Festplatte in die VMs einhängen, die als Knoten am Cluster teilnehmen werden. 

Führen Sie die folgenden Schritte aus, um die gemeinsam genutzte Festplatte mit Ihren SQL Server-VMs zu verbinden: 

1. Wählen Sie im Azure-Portal die VM aus, an die Sie die gemeinsame Festplatte anhängen möchten. 
1. Wählen Sie **Laufwerke** im Fenster **Einstellungen**. 
1. Wählen Sie **Vorhandene Festplatten anhängen**, um die gemeinsame Festplatte an die VM anzuhängen. 
1. Wählen Sie in der Dropdown-Liste **Datenträgername** die gemeinsame Festplatte aus. 
1. Wählen Sie **Speichern** aus.
1. Wiederholen Sie diese Schritte für jeden Clusterknoten SQL Server VM. 

Nach einigen Augenblicken ist die gemeinsam genutzte Datenfestplatte an die VM angeschlossen und erscheint in der Liste der Datenfestplatten für diese VM.

## <a name="initialize-shared-disk"></a>Gemeinsame Festplatte initialisieren

Sobald die gemeinsame Festplatte an alle VMs angeschlossen ist, können Sie die Festplatten der VMs initialisieren, die als Knoten am Cluster teilnehmen werden. Initialisieren Sie die Festplatten auf **allen** der VMs. 


Führen Sie die folgenden Schritte aus, um die Festplatten für Ihre SQL Server-VM zu initialisieren: 
 
1. Verbinden Sie sich mit einer der VMs.
2. Öffnen Sie in der VM das Menü **Start** und geben Sie **diskmgmt.msc** in das Suchfeld ein, um die Konsole **Disk Management** zu öffnen.
3. Die Datenträgerverwaltung erkennt, dass ein neuer nicht initialisierter Datenträger vorhanden ist, und deshalb wird das Fenster **Datenträger initialisieren** angezeigt.
4. Vergewissern Sie sich, dass der neue Datenträger ausgewählt ist, und wählen Sie dann **OK** aus, um ihn zu initialisieren.
5. Der neue Datenträger wird als **Nicht zugeordnet** angezeigt. Klicken Sie mit der rechten Maustaste auf den Datenträger, und wählen **Neues einfaches Volume** aus. Das Fenster **Assistent zum Erstellen neuer einfacher Volumes** wird geöffnet.
6. Führen Sie die Schritte des Assistenten aus, und übernehmen Sie dabei alle Standardeinstellungen. Wählen Sie abschließend **Fertig stellen** aus.
7. Schließen Sie die **Datenträgerverwaltung**.
8. Ein Popupfenster wird angezeigt, in dem Sie darüber informiert werden, dass Sie den neuen Datenträger formatieren müssen, bevor Sie ihn verwenden können. Wählen Sie **Datenträger formatieren** aus.
9. Überprüfen Sie im Fenster **Neuen Datenträger formatieren** die Einstellungen, und wählen Sie dann **Starten** aus.
10. Es wird eine Warnung angezeigt, die Sie darüber informiert, dass bei der Formatierung des Datenträgers alle Daten gelöscht werden. Klicken Sie auf **OK**.
11. Wählen Sie nach Abschluss der Formatierung die Option **OK** aus.
12. Wiederholen Sie diese Schritte auf jeder SQL Server-VM, die an der FCI teilnehmen soll. 

## <a name="create-windows-failover-cluster"></a>Windows Failover Cluster erstellen

Die Schritte zur Erstellung Ihres Windows Server Failover-Clusters hängen davon ab, ob Sie Ihre SQL Server-VMs in einem einzelnen Subnetz oder in mehreren Subnetzen bereitgestellt haben. Um Ihren Cluster zu erstellen, befolgen Sie die Schritte in der Anleitung entweder für ein [Multi-Subnetz-Szenario](availability-group-manually-configure-tutorial-multi-subnet.md#add-failover-cluster-feature) oder für ein [Einzel-Subnetz-Szenario](availability-group-manually-configure-tutorial-single-subnet.md#create-the-cluster). Obwohl sich diese Anleitungen auf die Erstellung einer Verfügbarkeitsgruppe beziehen, sind die Schritte zur Erstellung des Clusters die gleichen. 

## <a name="configure-quorum"></a>Konfigurieren des Quorums

Da der Datenträgerzeuge die resilienteste Quorumoption ist und die FCI-Lösung freigegebene Azure-Datenträger verwendet, wird empfohlen, einen Datenträgerzeugen als Quorumlösung zu konfigurieren. 

Wenn Sie im Cluster über eine gerade Anzahl von Stimmen verfügen, konfigurieren Sie die [Quorumlösung](hadr-cluster-quorum-configure-how-to.md), die Ihren Geschäftsanforderungen am besten entspricht. Weitere Informationen finden Sie unter [Quorum mit SQL Server-VMs](hadr-windows-server-failover-cluster-overview.md#quorum). 

## <a name="validate-cluster"></a>Validieren des Clusters

Validieren Sie den Cluster auf einer der virtuellen Maschinen mithilfe der Failover Cluster Manager-Benutzeroberfläche oder PowerShell. 

Führen Sie die folgenden Schritte aus, um den Cluster über die Benutzeroberfläche zu validieren: 

1. Wählen Sie unter **Server-Manager** die Option **Tools** aus, und wählen Sie dann **Failovercluster-Manager** aus.
1. Wählen Sie unter **Failovercluster-Manager** die Option **Aktion** aus, und wählen Sie dann **Konfiguration überprüfen** aus.
1. Wählen Sie **Weiter** aus.
1. Geben Sie unter **Server oder Cluster auswählen** die Namen der beiden virtuellen Computer ein.
1. Wählen Sie unter **Testoptionen** die Option **Nur ausgewählte Tests ausführen** aus. 
1. Wählen Sie **Weiter** aus.
1. Wählen Sie unter **Testauswahl** alle Tests *außer* **Speicherung**.
1. Wählen Sie **Weiter** aus.
1. Wählen Sie unter **Bestätigung** die Option **Weiter** aus.  Der **Konfigurationsüberprüfungs-Assistent** führt die Validierungstests aus.


Führen Sie zum Validieren des Clusters mit PowerShell das folgende Skript in einer PowerShell-Administratorsitzung auf einem der virtuellen Computer aus:

```powershell
Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
```

## <a name="test-cluster-failover"></a>Testen des Failovers des Clusters

Testen Sie das Failover Ihres Clusters. Klicken Sie im **Failovercluster-Manager** mit der rechten Maustaste auf den Cluster, und wählen Sie **Weitere Aktionen** > **Hauptclusterressource verschieben** > **Knoten auswählen** und dann den anderen Knoten des Clusters aus. Verschieben Sie die Hauptclusterressource auf alle Knoten des Clusters und dann zurück auf den primären Knoten. Stellen Sie sicher, dass Sie den Cluster erfolgreich auf jeden Knoten verschieben können, bevor Sie SQL Server installieren.

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Testen des Clusterfailovers durch Verschieben der Hauptressource auf die anderen Knoten":::

## <a name="add-shared-disks-to-cluster"></a>Gemeinsame Datenträger zum Cluster hinzufügen

Verwenden Sie den Failover Cluster Manager, um die angeschlossenen Azure gemeinsame Datenträger zu dem Cluster hinzuzufügen. 

Gehen Sie folgendermaßen vor, um Datenträger zu Ihrem Cluster hinzuzufügen: 

1. Wählen Sie im Dashboard **Server-Manager** die Option **Tools** aus, und klicken Sie dann auf **Failovercluster-Manager**.
1. Wählen Sie den Cluster aus und erweitern Sie ihn im Navigationsbereich. 
1. Wählen Sie **Speicher** und dann **Datenträger**. 
1. Klicken Sie mit der rechten Maustaste auf **Datenträger** und wählen Sie **Datenträger** hinzufügen: 

    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/cluster-add-disk.png" alt-text="Datenträger hinzufügen":::

1. Wählen Sie den gemeinsamen Azure-Datenträger im Fenster **Datenträger zu einem Cluster hinzufügen**.  Klicken Sie auf **OK**.
 
    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/cluster-select-shared-disk.png" alt-text="Datenträger auswählen":::

1. Nachdem der gemeinsame Datenträger dem Cluster hinzugefügt wurde, wird er im Failover Cluster Manager angezeigt.

    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/cluster-shared-disk.png" alt-text="Clusterdatenträger":::



## <a name="create-sql-server-fci"></a>Erstellen der SQL Server-FCI

Nachdem Sie den Failovercluster und alle Clusterkomponenten einschließlich Speicher konfiguriert haben, können Sie die SQL Server-FCI erstellen.

1. Stellen Sie mithilfe von RDP (Remotedesktopprotokoll) eine Verbindung mit dem ersten virtuellen Computer her.

1. Stellen Sie im **Failovercluster-Manager** sicher, dass sich alle Kernressourcen des Clusters auf dem ersten virtuellen Computer befinden. Verschieben Sie ggf. die Datenträger in diese virtuelle Maschine.

1. Suchen Sie nach den Installationsmedien. Wenn für den virtuellen Computer eines der Azure Marketplace-Images verwendet wird, befinden sich die Medien unter `C:\SQLServer_<version number>_Full`. 

1. Wählen Sie **Setup** aus.

1. Wählen Sie im **SQL Server-Installationscenter** die Option **Installation** aus.

1. Wählen Sie **Neue SQL Server-Failoverclusterinstallation** aus. Befolgen Sie im Assistenten die Anleitung zum Installieren der SQL Server-FCI.

1. Wählen Sie auf der Seite **Auswahl der Cluster-Datenträger** alle gemeinsam genutzten Datenträger aus, die mit der VM verbunden waren. 

    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/sql-install-cluster-disk-selection.png" alt-text="Datenträgerauswahl für Cluster":::

1. Auf der Seite **Cluster-Netzwerkkonfiguration** variiert die IP, die Sie angeben, je nachdem, ob Ihre SQL Server-VMs in einem einzelnen Subnetz oder in mehreren Subnetzen bereitgestellt wurden. 

   1. Für eine **Einzelsubnetzumgebung** geben Sie die IP-Adresse an, die Sie dem [Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) hinzufügen möchten.
   1. Für eine **Multi-Subnetz-Umgebung**, geben Sie die sekundäre IP-Adresse im Subnetz der _ersten_ SQL Server-VM an, die Sie zuvor als [IP-Adresse des Netzwerknamens der Failover-Cluster-Instanz](failover-cluster-instance-prepare-vm.md#assign-secondary-ip-addresses) festgelegt haben:

   :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/sql-install-cluster-network-secondary-ip-vm-1.png" alt-text="Bieten Sie die sekundäre IP-Adresse im Subnetz der ersten SQL Server-VM an, die Sie zuvor als IP-Adresse des Netzwerknamens der Failover-Cluster-Instanz festgelegt haben":::

1. Stellen Sie auf der Seite **Konfiguration der Datenbank-Engine** sicher, dass sich die Datenbankverzeichnisse auf dem/den gemeinsam genutzten Datenträger(n) von Azure befinden. 

1. Nachdem Sie die Anweisungen des Assistenten ausgeführt haben, installiert das Setup die SQL Server FCI auf dem ersten Knoten.

1. Nachdem die FCI-Installation auf dem ersten Knoten erfolgreich war, verbinden Sie sich über RDP mit dem zweiten Knoten.

1. Öffnen Sie das **SQL Server-Installationscenter**, und wählen Sie **Installation** aus.

1. Wählen Sie **Knoten einem SQL Server-Failovercluster hinzufügen** aus. Folgen Sie den Anweisungen des Assistenten, um SQL Server zu installieren und den Knoten zur FCI hinzuzufügen.

1. Für ein Multi-Subnetz-Szenario geben Sie in **Cluster-Netzwerkkonfiguration** die sekundäre IP-Adresse im Subnetz des _zweiten_ SQL Server VM-Subnetzes ein, das Sie zuvor als [IP-Adresse des Netzwerknamens der Failover-Clusterinstanz](failover-cluster-instance-prepare-vm.md#assign-secondary-ip-addresses)

    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/sql-install-cluster-network-secondary-ip-vm-2.png" alt-text="Geben Sie die sekundäre IP-Adresse im Subnetz des zweiten SQL Server VM-Subnetzes ein, das Sie zuvor als IP-Adresse des Netzwerknamens der Failover-Cluster-Instanz festgelegt haben":::

    Nach Auswahl von **Weiter** in **Cluster-Netzwerkkonfiguration** zeigt das Setup ein Dialogfeld an, das anzeigt, dass das SQL Server-Setup mehrere Subnetze erkannt hat, wie im Beispielbild.  Klicken Sie auf **Ja**, um zu bestätigen. 

    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/sql-install-multi-subnet-confirmation.png" alt-text="Multi-Subnetz-Bestätigung":::

1. Nachdem Sie die Anweisungen des Assistenten ausgeführt haben, fügt das Setup den zweiten SQL Server FCI-Knoten hinzu. 

1. Wiederholen Sie diese Schritte auf allen anderen SQL Server-VMs, die an der SQL Server-Failover-Cluster-Instanz teilnehmen sollen. 


>[!NOTE]
> Die Galeriebilder des Azure Marketplace werden mit installiertem SQL Server Management Studio geliefert. Wenn Sie kein Marketplace-Image verwendet haben [Laden Sie SQL Server Management Studio (SSMS) herunter ](/sql/ssms/ownload-sql-server-management-studio-ssms).


## <a name="register-with-sql-iaas-extension"></a>Registrieren mit SQL-IaaS-Erweiterung 

Um Ihre SQL Server-VM über das Portal zu verwalten, registrieren Sie sie mit der SQL IaaS Agent-Erweiterung im [leichtgewichtigen Verwaltungsmodus](sql-agent-extension-manually-register-single-vm.md#lightweight-mode), dem derzeit einzigen Modus, der von FCI und SQL Server auf Azure-VMs unterstützt wird. 

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
- Nur die Registrierung mit der SQL-IaaS-Agent-Erweiterung im [Verwaltungsmodus „Lightweight“](sql-server-iaas-agent-extension-automate-management.md#management-modes) wird unterstützt.

## <a name="next-steps"></a>Nächste Schritte

Wenn freigegebene Azure-Datenträger nicht die richtige FCI-Speicherlösung für Sie sind, können Sie Ihre FCI stattdessen mithilfe von [Premium-Dateifreigaben](failover-cluster-instance-premium-file-share-manually-configure.md) oder [Direkte Speicherplätze](failover-cluster-instance-storage-spaces-direct-manually-configure.md) erstellen. 


Weitere Informationen finden Sie unter:

- [Windows Server-Failovercluster mit SQL Server auf Azure-VMs](hadr-windows-server-failover-cluster-overview.md)
- [Failoverclusterinstanzen mit SQL Server auf Azure-VMs](failover-cluster-instance-overview.md)
- [AlwaysOn-Failoverclusterinstanzen (SQL Server)](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
- [HADR-Einstellungen für SQL Server auf Azure-VMs](hadr-cluster-best-practices.md)
