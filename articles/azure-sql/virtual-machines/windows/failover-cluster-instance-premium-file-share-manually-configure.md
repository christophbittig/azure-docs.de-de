---
title: Erstellen einer FCI mit einer Premium-Dateifreigabe
description: Erstellen Sie auf virtuellen Azure-Computern eine Failoverclusterinstanz (FCI) mit SQL Server mithilfe einer Premium-Dateifreigabe.
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
ms.openlocfilehash: 8d9e94ae12600223c6f54fb75cbc6c9648ecf7f9
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132156957"
---
# <a name="create-an-fci-with-a-premium-file-share-sql-server-on-azure-vms"></a>Erstellen einer FCI mit einer Premium-Dateifreigabe (SQL Server auf Azure-VMs)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!TIP]
> Eliminieren Sie die Notwendigkeit eines Azure Load Balancer oder eines verteilten Netzwerknamens (DNN) für Ihre Failover-Cluster-Instanz, indem Sie Ihre SQL Server-VMs in [mehreren Subnetzen](failover-cluster-instance-prepare-vm.md#subnets) innerhalb desselben virtuellen Azure-Netzwerks erstellen.


In diesem Artikel wird erläutert, wie Sie auf virtuellen Azure-Computern eine SQL Server-Failoverclusterinstanz (FCI) mit SQL Server mithilfe einer [Premium-Dateifreigabe](../../../storage/files/storage-how-to-create-file-share.md) erstellen.

Premium-Dateifreigaben sind SSD-gesichert und bieten durchgängig Dateifreigaben mit niedriger Latenz, die vollständig für die Verwendung mit Failover-Cluster-Instanzen für SQL Server 2012 oder höher auf Windows Server 2012 oder höher unterstützt werden. Premium-Dateifreigaben bieten Ihnen mehr Flexibilität, sodass Sie ohne Ausfallzeiten die Größe der Dateifreigabe ändern und diese skalieren können.

Weitere Informationen finden Sie in der Übersicht zu [FCI mit SQL Server auf Azure-VMs](failover-cluster-instance-overview.md) und über [Bewährte Methoden für Cluster](hadr-cluster-best-practices.md). 

> [!NOTE]
> Sie können Ihre Failoverclusterinstanzlösung jetzt mithilfe von Azure Migrate per Lift-und-Shift-Verfahren zu SQL Server auf Azure-VMs verschieben. Weitere Informationen finden Sie unter [Migrieren einer Failoverclusterinstanz](../../migration-guides/virtual-machines/sql-server-failover-cluster-instance-to-sql-on-azure-vm.md). 

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie die in diesem Artikel aufgeführten Anweisungen ausführen, sollten Sie über Folgendes verfügen:

- Ein Azure-Abonnement.
- Ein Konto mit Berechtigungen zum Erstellen von Objekten auf virtuellen Azure-Computern und in Active Directory
- [Mindestens zwei vorbereitete virtuelle Azure-Computer mit Windows](failover-cluster-instance-prepare-vm.md) in einer [Verfügbarkeitsgruppe](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) oder unterschiedlichen [Verfügbarkeitszonen](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address).
- Eine [Premium-Dateifreigabe ](../../../storage/files/storage-how-to-create-file-share.md) basierend auf dem Speicherkontingent Ihrer Datenbank für Ihre Datendateien, die als Clusterlaufwerk verwendet werden kann.
- Die neueste Version von [PowerShell](/powershell/azure/install-az-ps). 

## <a name="mount-premium-file-share"></a>Einbinden der Premium-Dateifreigabe

Gehen Sie folgendermaßen vor, um Ihre Premium-Dateifreigabe einzubinden: 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wechseln Sie dann zu Ihrem Speicherkonto.
1. Navigieren Sie unter **Datenspeicher** zu **Dateifreigaben**, und wählen Sie die Premium-Dateifreigabe aus, die Sie als SQL-Speicher verwenden möchten.
1. Klicken Sie auf **Verbinden**, um die Verbindungszeichenfolge für Ihre Dateifreigabe anzuzeigen.
1. Wählen Sie in der Dropdownliste den Laufwerkbuchstaben aus, den Sie verwenden möchten, wählen Sie **Speicherkontoschlüssel** als Authentifizierungsmethode aus, und kopieren Sie dann den Codeblock in einen Text-Editor, z. B. Editor.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/premium-file-storage-commands.png" alt-text="Kopieren des PowerShell-Befehls aus dem Portal zum Herstellen der Verbindung mit der Dateifreigabe":::

1. Verwenden Sie das Remote Desktop Protocol (RDP), um eine Verbindung zur SQL Server-VM mit dem **Konto herzustellen, das Ihr SQL Server FCI für das Dienstkonto** verwenden wird.
1. Öffnen Sie eine administrative PowerShell-Befehlskonsole.
1. Führen Sie den Befehl aus, den Sie zuvor aus dem Dateifreigabeportal in den Text-Editor kopiert haben.
1. Navigieren Sie über den Datei-Explorer oder das Dialogfeld **Ausführen** (WINDOWS+R auf der Tastatur) zur Freigabe. Verwenden Sie den Netzwerkpfad `\\storageaccountname.file.core.windows.net\filesharename`. Zum Beispiel, `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`
1. Erstellen Sie in der neu verbundenen Dateifreigabe mindestens einen Ordner, in dem Sie Ihre SQL-Datendateien ablegen können.
1. Wiederholen Sie diese Schritte auf jeder SQL Server-VM, die zum Cluster gehört.

  > [!IMPORTANT]
  > Erwägen Sie den Einsatz einer gesonderten Dateifreigabe für Sicherungsdateien, um die IOPS- (Input/Output Operations Per Second, Ein-/Ausgabevorgänge pro Sekunde) und Größenkapazität dieser Freigabe für Daten- und Protokolldateien zu reservieren. Sie können für Sicherungsdateien eine Dateifreigaben des Typs Standard oder Premium verwenden.

## <a name="create-windows-failover-cluster"></a>Windows Failover Cluster erstellen

Die Schritte zur Erstellung Ihres Windows Server Failover-Clusters hängen davon ab, ob Sie Ihre SQL Server-VMs in einem einzelnen Subnetz oder in mehreren Subnetzen bereitgestellt haben. Um Ihren Cluster zu erstellen, folgen Sie den Schritten in der Anleitung entweder für ein [Multi-Subnetz-Szenario](availability-group-manually-configure-tutorial-multi-subnet.md#add-failover-cluster-feature) oder ein [Einzel-Subnetz-Szenario](availability-group-manually-configure-tutorial-single-subnet.md#create-the-cluster). Obwohl sich diese Anleitungen auf die Erstellung einer Verfügbarkeitsgruppe beziehen, sind die Schritte zur Erstellung des Clusters die gleichen. 


## <a name="configure-quorum"></a>Konfigurieren des Quorums

Der Cloud Witness ist die empfohlene Quorum-Lösung für diese Art von Clusterkonfiguration für SQL Server auf Azure-VMs.  

Wenn Sie im Cluster über eine gerade Anzahl von Stimmen verfügen, konfigurieren Sie die [Quorumlösung](hadr-cluster-quorum-configure-how-to.md), die Ihren Geschäftsanforderungen am besten entspricht. Weitere Informationen finden Sie unter [Quorum mit SQL Server-VMs](hadr-windows-server-failover-cluster-overview.md#quorum). 

## <a name="validate-cluster"></a>Validieren des Clusters

Validieren Sie den Cluster auf einer der virtuellen Maschinen mithilfe der Failover Cluster Manager-Benutzeroberfläche oder PowerShell.

Um den Cluster über die Benutzeroberfläche zu validieren, gehen Sie auf einem der beiden virtuellen Computer folgendermaßen vor:

1. Wählen Sie unter **Server-Manager** die Option **Tools** aus, und wählen Sie dann **Failovercluster-Manager** aus.
1. Wählen Sie unter **Failovercluster-Manager** die Option **Aktion** aus, und wählen Sie dann **Konfiguration überprüfen** aus.
1. Wählen Sie **Weiter** aus.
1. Geben Sie unter **Server oder Cluster auswählen** die Namen der beiden virtuellen Computer ein.
1. Wählen Sie unter **Testoptionen** die Option **Nur ausgewählte Tests ausführen** aus. 
1. Wählen Sie **Weiter** aus.
1. Wählen Sie unter **Testauswahl** alle Tests aus, ausgenommen **Speicher** und **Direkte Speicherplätze**, wie hier gezeigt:

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/cluster-validation.png" alt-text="Auswählen von Tests zur Überprüfung des Clusters":::

1. Wählen Sie **Weiter** aus.
1. Wählen Sie unter **Bestätigung** die Option **Weiter** aus. Der **Konfigurationsüberprüfungs-Assistent** führt die Validierungstests aus.


Führen Sie zum Validieren des Clusters mit PowerShell das folgende Skript in einer PowerShell-Administratorsitzung auf einem der virtuellen Computer aus:

```powershell
Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
```



## <a name="test-cluster-failover"></a>Testen des Failovers des Clusters

Testen Sie das Failover Ihres Clusters. Klicken Sie im **Failovercluster-Manager** mit der rechten Maustaste auf den Cluster, und wählen Sie **Weitere Aktionen** > **Hauptclusterressource verschieben** > **Knoten auswählen** und dann den anderen Knoten des Clusters aus. Verschieben Sie die Hauptclusterressource auf alle Knoten des Clusters und dann zurück auf den primären Knoten. Wenn Sie den Cluster erfolgreich auf jeden Knoten verschieben können, können Sie SQL Server installieren.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Testen des Clusterfailovers durch Verschieben der Hauptressource auf die anderen Knoten":::


## <a name="create-sql-server-fci"></a>Erstellen der SQL Server-FCI

Nachdem Sie den Failovercluster konfiguriert haben, können Sie die SQL Server-FCI erstellen.

1. Stellen Sie die Verbindung mit dem ersten virtuellen Computer per RDP her.

1. Stellen Sie im **Failovercluster-Manager** sicher, dass sich alle Kernressourcen des Clusters auf dem ersten virtuellen Computer befinden. Verschieben Sie alle Ressourcen auf diesen virtuellen Computer, falls dies erforderlich ist.

1. Suchen Sie nach den Installationsmedien. Wenn für den virtuellen Computer eines der Azure Marketplace-Images verwendet wird, befinden sich die Medien unter `C:\SQLServer_<version number>_Full`. 

1. Wählen Sie **Setup** aus.

1. Wählen Sie im **SQL Server-Installationscenter** die Option **Installation** aus.

1. Wählen Sie **Neue SQL Server-Failoverclusterinstallation** aus, und befolgen Sie dann die Anweisungen im Assistenten, um die SQL Server-FCI zu installieren.

1. Auf der Seite **Cluster-Netzwerkkonfiguration** variiert die IP, die Sie angeben, je nachdem, ob Ihre SQL Server-VMs in einem einzelnen Subnetz oder in mehreren Subnetzen bereitgestellt wurden. 

   1. Für eine **Einzelsubnetz-Umgebung** geben Sie die IP-Adresse an, die Sie dem [Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) hinzufügen möchten.
   1. Für eine **Multi-Subnetz-Umgebung**, geben Sie die sekundäre IP-Adresse im Subnetz der _ersten_ SQL Server-VM an, die Sie zuvor als [IP-Adresse des Netzwerknamens der Failover-Cluster-Instanz](failover-cluster-instance-prepare-vm.md#assign-secondary-ip-addresses) festgelegt haben:

   :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/sql-install-cluster-network-secondary-ip-vm-1.png" alt-text="Bieten Sie die sekundäre IP-Adresse im Subnetz der ersten SQL Server-VM an, die Sie zuvor als IP-Adresse des Netzwerknamens der Failover-Cluster-Instanz festgelegt haben":::

1. In **Database Engine Configuration** müssen sich die Datenverzeichnisse auf der Premium-Dateifreigabe befinden. Geben Sie den vollständigen Pfad der Freigabe im folgenden Format ein: `\\storageaccountname.file.core.windows.net\filesharename\foldername`. Es erscheint eine Warnung, die Sie darauf hinweist, dass Sie einen Dateiserver als Datenverzeichnis angegeben haben. Diese Warnung ist erwartungsgemäß. Vergewissern Sie sich zur Vermeidung möglicher Fehler, dass das Benutzerkonto, mit dem Sie beim persistenten Speichern der Dateifreigabe über RDP auf die VM zugegriffen haben, dasselbe Konto ist, das der SQL Server-Dienst verwendet.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/use-file-share-as-data-directories.png" alt-text="Verwenden der Dateifreigabe als SQL-Datenverzeichnisse":::

1. Nachdem Sie die Schritte im Assistenten abgeschlossen haben, installiert Setup eine SQL Server FCI auf dem ersten Knoten.

1. Nachdem die FCI-Installation auf dem ersten Knoten erfolgreich war, verbinden Sie sich über RDP mit dem zweiten Knoten.

1. Öffnen Sie das **SQL Server-Installationscenter**, und wählen Sie **Installation** aus.

1. Wählen Sie **Knoten einem SQL Server-Failovercluster hinzufügen** aus. Folgen Sie den Anweisungen des Assistenten, um SQL Server zu installieren und den Knoten zur FCI hinzuzufügen.

1. Für ein Szenario mit mehreren Subnetzen geben Sie in **Cluster-Netzwerkkonfiguration** die sekundäre IP-Adresse im Subnetz der _zweiten_ SQL Server-VM ein, die Sie zuvor als [IP-Adresse des Netzwerknamens der Failover-Clusterinstanz](failover-cluster-instance-prepare-vm.md#assign-secondary-ip-addresses)

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
   -LicenseType ???? -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Konfigurieren von Konnektivität

Wenn Sie Ihre SQL Server-VMs in mehreren Subnetzen bereitgestellt haben, überspringen Sie diesen Schritt. Wenn Sie Ihre SQL Server-VMs in einem einzigen Subnetz bereitgestellt haben, müssen Sie eine zusätzliche Komponente konfigurieren, um den Datenverkehr zu Ihrer FCI zu leiten. Sie können einen virtuellen Netzwerknamen (VNN) mit einem Azure Load Balancer oder einen verteilten Netzwerknamen für eine Failover-Cluster-Instanz konfigurieren. [Überprüfen Sie die Unterschiede zwischen den beiden](hadr-windows-server-failover-cluster-overview.md#virtual-network-name-vnn) und stellen Sie dann entweder einen [verteilten Netzwerknamen](failover-cluster-instance-distributed-network-name-dnn-configure.md) oder einen [virtuellen Netzwerknamen und Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) für Ihre Failover-Cluster-Instanz bereit.  

## <a name="limitations"></a>Einschränkungen

- MS DTC-Transaktionen (Microsoft Distributed Transaction Coordinator) werden unter Windows Server 2016 und früheren Versionen nicht unterstützt. 
- Filestream wird für einen Failovercluster mit einer Premium-Dateifreigabe nicht unterstützt. Um Filestream zu verwenden, stellen Sie den Cluster stattdessen mithilfe von [Direkte Speicherplätze](failover-cluster-instance-storage-spaces-direct-manually-configure.md) oder von [freigegebenen Azure-Datenträgern](failover-cluster-instance-azure-shared-disks-manually-configure.md) bereit.
- Nur die Registrierung mit der SQL-IaaS-Agent-Erweiterung im [Verwaltungsmodus „Lightweight“](sql-server-iaas-agent-extension-automate-management.md#management-modes) wird unterstützt. 
- Datenbankmomentaufnahmen werden [bei Azure Files aufgrund von Einschränkungen für Sparsedateien](/rest/api/storageservices/features-not-supported-by-the-azure-file-service)derzeit nicht unterstützt.
- Da Datenbankmomentaufnahmen nicht unterstützt werden, greift CHECKDB für Benutzerdatenbanken auf CHECKDB WITH TABLOCK zurück. TABLOCK beschränkt die ausgeführten Überprüfungen. DBCC CHECKCATALOG wird nicht für die Datenbank ausgeführt, und die Service Broker-Daten werden nicht überprüft.
- CHECKDB für die MASTER- und MSDB-Datenbank wird nicht unterstützt. 
- Datenbanken, die das In-Memory-OLTP-Feature verwenden, werden auf einer Failoverclusterinstanz, die mit einer Premium-Dateifreigabe bereitgestellt wird, nicht unterstützt. Wenn Ihr Unternehmen In-Memory-OLTP erfordert, sollten Sie stattdessen die Bereitstellung Ihrer FCI mit [freigegebenen Azure-Datenträgern](failover-cluster-instance-azure-shared-disks-manually-configure.md) oder [direkten Speicherplätzen](failover-cluster-instance-storage-spaces-direct-manually-configure.md) in Betracht ziehen.

## <a name="next-steps"></a>Nächste Schritte

Wenn Premium-Dateifreigaben nicht die richtige FCI-Speicherlösung für Sie sind, können Sie Ihre FCI mithilfe von [freigegebenen Azure-Datenträgern](failover-cluster-instance-azure-shared-disks-manually-configure.md) oder von [Direkte Speicherplätze](failover-cluster-instance-storage-spaces-direct-manually-configure.md) erstellen. 

Weitere Informationen finden Sie unter:

- [Windows Server-Failovercluster mit SQL Server auf Azure-VMs](hadr-windows-server-failover-cluster-overview.md)
- [Failoverclusterinstanzen mit SQL Server auf Azure-VMs](failover-cluster-instance-overview.md)
- [AlwaysOn-Failoverclusterinstanzen (SQL Server)](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
- [HADR-Einstellungen für SQL Server auf Azure-VMs](hadr-cluster-best-practices.md)

