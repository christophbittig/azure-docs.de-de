---
title: Vorbereiten virtueller Computer für eine Failoverclusterinstanz
description: Bereiten Sie Ihre virtuellen Azure-Computer für die Verwendung mit einer Failoverclusterinstanz (FCI) und SQL Server unter Azure Virtual Machines vor.
services: virtual-machines
documentationCenter: na
author: rajeshsetlem
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/10/2021
ms.author: rsetlem
ms.reviewer: mathoma
ms.openlocfilehash: ac7b5617cf81b2845701360cf1d7af3fbc4858c0
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132156088"
---
# <a name="prepare-virtual-machines-for-an-fci-sql-server-on-azure-vms"></a>Vorbereiten virtueller Computer für eine FCI (SQL Server auf Azure-VMs)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In diesem Artikel wird beschrieben, wie Sie virtuelle Azure-Computer (VMs) für den Einsatz mit einer SQL Server-Failoverclusterinstanz (FCI) vorbereiten können. Die Konfigurationseinstellungen hängen von der FCI-Speicherlösung ab. Vergewissern Sie sich daher, dass Sie die für Ihre Umgebung und Ihr Unternehmen richtige Konfiguration wählen. 

Weitere Informationen finden Sie in der Übersicht zu [FCI mit SQL Server auf Azure-VMs](failover-cluster-instance-overview.md) und über [Bewährte Methoden für Cluster](hadr-cluster-best-practices.md). 

> [!NOTE]
> Sie können Ihre Failoverclusterinstanzlösung jetzt mithilfe von Azure Migrate per Lift-und-Shift-Verfahren zu SQL Server auf Azure-VMs verschieben. Weitere Informationen finden Sie unter [Migrieren einer Failoverclusterinstanz](../../migration-guides/virtual-machines/sql-server-failover-cluster-instance-to-sql-on-azure-vm.md). 

## <a name="prerequisites"></a>Voraussetzungen 

- Ein Microsoft Azure-Abonnement [Kostenlos](https://azure.microsoft.com/free/) einsteigen 
- Eine Windows-Domäne auf virtuellen Azure-Maschinen oder ein lokales aktives Verzeichnis, das auf Azure mit virtueller Netzwerkkopplung erweitert wurde.
- Ein Konto mit Berechtigungen zum Erstellen von Objekten auf virtuellen Azure-Computern und in Active Directory
- Ein virtuelles Azure-Netzwerk und ein oder mehrere Subnetze mit ausreichend IP-Adressraum für diese Komponenten:
   - Beide virtuellen Computer
   - Eine IP-Adresse für den Windows-Failover-Cluster
   - Eine IP-Adresse für jede FCI
- DNS-Konfiguration im Azure-Netzwerk mit Verweis auf die Domänencontroller



## <a name="choose-an-fci-storage-option"></a>Wählen einer FCI-Speicheroption

Die Konfigurationseinstellungen für Ihren virtuellen Computer variieren je nach der Speicheroption, die Sie für Ihre SQL Server-Failoverclusterinstanz nutzen möchten. Bevor Sie den virtuellen Computer vorbereiten, prüfen Sie die [verfügbaren FCI-Speicheroptionen](failover-cluster-instance-overview.md#storage). Wählen Sie die Option, die am besten zu Ihrer Umgebung und Ihren Geschäftsanforderungen passt. Wählen Sie dann im Verlauf dieses Artikels auf Grundlage Ihrer Speicherwahl die passenden VM-Konfigurationsoptionen sorgfältig aus. 

## <a name="choose-vm-availability"></a>VM-Verfügbarkeit auswählen 

Für das Feature „Failovercluster“ müssen virtuelle Computer in einer [Verfügbarkeitsgruppe](../../../virtual-machines/linux/tutorial-availability-sets.md) oder [Verfügbarkeitszone](../../../availability-zones/az-overview.md#availability-zones) platziert werden.

Wählen Sie die VM-Verfügbarkeitsoption sorgfältig aus, die Ihrer beabsichtigten Clusterkonfiguration entspricht: 

- **Azure freigegebene Datenträger**: Die Verfügbarkeitsoption variiert, wenn Sie SSD Premium oder UltraDisk verwenden:
   - **SSD Premium Zone Redundant Storage (ZRS)** : [Verfügbarkeitszone](../../../availability-zones/az-overview.md#availability-zones) in verschiedenen Zonen. [Premium SSD ZRS](../../../virtual-machines/disks-redundancy.md#zone-redundant-storage-for-managed-disks) repliziert Ihren in Azure verwalteten Datenträger synchron über drei Azure-Verfügbarkeitszonen in der ausgewählten Region. VMs, die Teil eines Failover-Clusters sind, können in verschiedenen Verfügbarkeitszonen platziert werden. So können Sie eine zonenredundante SQL Server FCI erreichen, die ein VM-Verfügbarkeits-SLA von 99,99 % bietet. Die Latenzzeit der Datenträger für ZRS ist aufgrund der zonenübergreifenden Kopie der Daten höher.
   - **SSD PremiumLocally Redundant Storage (LRS)** : [Availability Set](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) in verschiedenen Fehler-/Aktualisierungsdomänen für [Premium SSD LRS](../../../virtual-machines/disks-redundancy.md#locally-redundant-storage-for-managed-disks). Sie können die VMs auch in einer [Nachbarschaftsplatzierungsgruppe](../../../virtual-machines/windows/proximity-placement-groups-portal.md) platzieren, um sie näher beieinander zu platzieren. Die Kombination aus Availability Set und Proximity Placement Group bietet die niedrigste Latenz für gemeinsam genutzte Datenträger, da die Daten lokal in einem Rechenzentrum repliziert werden, und ermöglicht eine VM-Verfügbarkeits-SLA von 99,95 %.
   - **Ultra Disk Locally Redundant Storage (LRS)** : [Verfügbarkeitszone](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address), aber die VMs müssen sich in der gleichen Verfügbarkeitszone befinden. [Ultra-Datenträger](../../../virtual-machines/disks-enable-ultra-ssd.md) bieten die geringste Datenträger-Latenz und eignen sich am besten für IO-intensive Workloads. Da alle VMs, die Teil der FCI sind, in derselben Verfügbarkeitszone liegen müssen, beträgt die Verfügbarkeit der VMs nur 99,9%. 
- **Premium-Dateifreigaben**: [Verfügbarkeitsgruppe](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) oder [Verfügbarkeitszone](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address).
- **Direkte Speicherplätze**: [Verfügbarkeitsmenge](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set).

> [!IMPORTANT]
> Sie können die Verfügbarkeitsgruppe nicht mehr festlegen oder ändern, nachdem Sie einen virtuellen Computer erstellt haben.

## <a name="subnets"></a>Subnetze 

Für SQL Server auf Azure-VMs haben Sie die Möglichkeit, Ihre SQL Server-VMs in einem einzigen Subnetz oder in mehreren Subnetzen bereitzustellen. 

Die Bereitstellung Ihrer VMs in mehreren Subnetzen nutzt die Cluster-OR-Abhängigkeit für IP-Adressen und entspricht der Erfahrung vor Ort bei der Verbindung mit Ihrer Failover-Cluster-Instanz. Der Multi-Subnetz-Ansatz wird für SQL Server auf Azure-VMs empfohlen, um die Verwaltung zu vereinfachen und die Failover-Zeiten zu verkürzen. 

Die Bereitstellung Ihrer VMs in einem einzelnen Subnetz erfordert eine zusätzliche Abhängigkeit von einem Azure Load Balancer oder einem verteilten Netzwerknamen (DNN), um den Datenverkehr zu Ihrer FCI zu leiten. 

Wenn Sie Ihre SQL Server-VMs in mehreren Subnetzen bereitstellen, befolgen Sie die Schritte in diesem Abschnitt, um Ihre virtuellen Netzwerke mit zusätzlichen Subnetzen zu erstellen, und weisen Sie den VMs nach der Erstellung der SQL Server-VMs sekundäre IP-Adressen [ innerhalb dieser Subnetze zu](#assign-secondary-ip-addresses). Wenn Sie Ihre SQL Server-VMs in einem einzigen Subnetz bereitstellen, ist keine zusätzliche Netzwerkkonfiguration erforderlich. 

# <a name="single-subnet"></a>[Ein Subnetz](#tab/single-subnet)

Platzieren Sie beide virtuellen Maschinen in einem einzigen Subnetz, das über genügend IP-Adressen für beide virtuellen Maschinen und alle FCIs verfügt, die Sie eventuell in dem Cluster installieren werden. Dieser Ansatz erfordert eine zusätzliche Komponente, um Verbindungen zu Ihrer FCI zu leiten, wie z. B. einen Azure Load Balancer oder einen verteilten Netzwerknamen (DNN). 

Wenn Sie sich dafür entscheiden, Ihre SQL Server-VMs in einem einzigen Subnetz einzusetzen [, prüfen Sie die Unterschiede zwischen den Azure Load Balancer- und DNN-Konnektivitätsoptionen](hadr-windows-server-failover-cluster-overview.md#distributed-network-name-dnn) und entscheiden Sie, welche Option für Sie am besten geeignet ist, bevor Sie den Rest Ihrer Umgebung für Ihre FCI vorbereiten.

Wenn Sie Ihre SQL Server-VMs in einem einzigen Subnetz bereitstellen, ist keine zusätzliche Netzwerkkonfiguration erforderlich. 

# <a name="multi-subnet"></a>[Multi-Subnetz](#tab/multi-subnet)

Wenn Sie Verbindungen direkt zu Ihrem SQL Server FCI leiten möchten, platzieren Sie beide virtuellen Maschinen in getrennten Subnetzen innerhalb eines virtuellen Netzwerks. Weisen Sie der SQL Server-VM für die Failover-Cluster-Instanz eine sekundäre IP-Adresse zu - und, wenn Sie Windows Server 2016 und darunter verwenden, weisen Sie auch eine zusätzliche sekundäre IP-Adresse für den Windows Server Failover Cluster zu. Windows Server 2019 und höher verwendet einen verteilten Netzwerknamen (DNN) für den Clusternamen, sodass eine sekundäre IP-Adresse für den Cluster nicht erforderlich ist. 

Dieser Ansatz macht einen Azure Load Balancer oder einen verteilten Netzwerknamen (DNN) bei der Verbindung mit Ihrer SQL Server FCI überflüssig. 

Wenn Sie Ihre SQL Server-VMs in mehreren Subnetzen bereitstellen möchten, müssen Sie zunächst das virtuelle Netzwerk mit zwei zusätzlichen Subnetzen erstellen. Sobald Ihre SQL Server-VMs erstellt sind, [weisen Sie den VMs sekundäre IP-Adressen zu ](#assign-secondary-ip-addresses).  Weitere Informationen finden Sie unter [Übersicht über virtuelle Netzwerke](../../../virtual-network/virtual-networks-overview.md). Die Subnetznamen und IP-Adressen in diesem Abschnitt dienen nur als Beispiel und können in Ihrer Umgebung variieren. Gehen Sie folgendermaßen vor, um das virtuelle Netzwerk im Azure-Portal zu erstellen:

1. Gehen Sie zu Ihrer Ressourcengruppe im [Azure-Portal](https://portal.azure.com) und wählen Sie **+ Erstellen**

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/02-create-resource-rg.png" alt-text="Neue Ressource in Ihrer Ressourcengruppe anlegen":::

1. Suchen Sie nach **virtuellem Netzwerk** im Suchfeld **Marktplatz** und wählen Sie die Kachel **virtuelles Netzwerk** von Microsoft. Wählen Sie **Erstellen** auf der Seite **Virtuelles Netzwerk**.  
1. Geben Sie auf der Seite **Virtuelles Netzwerk erstellen** auf der Registerkarte **Grundlagen** die folgenden Informationen ein: 
   1. Wählen Sie unter **Projektdetails** das entsprechende Azure **Abonnement** und die **Ressourcengruppe**, in der Sie Ihre SQL Server-VMs bereitstellen möchten. 
   1. Geben Sie unter **Instanzdetails** einen Namen für Ihr virtuelles Netzwerk ein und wählen Sie in der Dropdown-Liste die gleiche Region wie Ihre Ressourcengruppe.

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/03-create-vnet-basics.png" alt-text="Wählen Sie die Ressourcengruppe, die Sie zuvor erstellt haben, und geben Sie dann einen Namen für Ihr virtuelles Netzwerk ein":::

1. Wählen Sie auf der Registerkarte **IP-Adressen** die Option **+ Subnetz hinzufügen**, um ein zusätzliches Subnetz für Ihre erste SQL Server-VM hinzuzufügen, und geben Sie die folgenden Werte ein: 
   1. Geben Sie einen Wert für den **Subnetznamen** an, z. B. **SQL-Subnetz-1**. 
   1. Stellen Sie einen eindeutigen Subnetzadressbereich innerhalb des virtuellen Netzwerkadressraums bereit. Zum Beispiel können Sie das dritte Oktett des DC-Subnetz-Adressbereichs um 1 iterieren. 
      - Wenn Ihr **Standard**-Bereich beispielsweise *10.38.0.0/24* ist, geben Sie den IP-Adressbereich `10.38.1.0/24` für **SQL-Subnetz-1** ein. 
      - Ebenso, wenn Ihr **Standard** IP-Bereich *10.5.0.0/24* ist, dann geben Sie `10.5.1.0/24` für das neue Subnetz ein. 
   1. Wählen Sie **Hinzufügen**, um Ihr neues Subnetz hinzuzufügen. 

     :::image type="content" source="./media/failover-cluster-instance-prepare-vm/05-create-vnet-ip-address-add-sql-subnet-1.png" alt-text="Benennen Sie Ihr erstes Subnetz, z. B. sql-subnet-1, und iterieren Sie dann das dritte Oktett um 1, so dass, wenn Ihre IP-Adresse im DC-Subnetz 10.5.0.0 lautet, Ihr neues Teilnetz 10.5.1.0 sein sollte":::

1. Wiederholen Sie den vorherigen Schritt, um einen zusätzlichen eindeutigen Subnetzbereich für Ihre zweite SQL Server-VM mit einem Namen wie **SQL-Subnetz-2** hinzuzufügen. Sie können das dritte Oktett noch einmal um eins durchgehen. 
   - Wenn zum Beispiel Ihr **Standard** IP-Bereich *10.38.0.0/24* ist, und Ihr **SQL-Subnetz-1** *10.38.1.0/24* ist, dann geben Sie `10.38.2.0/24` für das neue Subnetz ein
   - Ebenso, wenn Ihr **Standard** IP-Bereich *10.5.0.0/24* ist, und Ihr **SQL-Subnetz-1** *10.5.1.0/24* ist, dann geben Sie den IP-Adressbereich `10.5.2.0/24` für **SQL-Subnetz-2** . 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/06-create-vnet-ip-address-add-sql-subnet-2.png" alt-text="Benennen Sie Ihr zweites Subnetz, z. B. sql-subnet-2, und iterieren Sie dann das dritte Oktett um 2, so dass Ihr neues Subnetz 10.38.2.0/24 heißen sollte, wenn die IP-Adresse Ihres DC-Subnetzes 10.38.0.0/24 lautet":::

1. Nachdem Sie das zweite Subnetz hinzugefügt haben, überprüfen Sie Ihre Subnetznamen und -bereiche (Ihre IP-Adressbereiche können von der Abbildung abweichen). Wenn alles korrekt aussieht, wählen Sie **Überprüfen + Erstellen**, dann **Erstellen**, um Ihr neues virtuelles Netzwerk zu erstellen. 

   :::image type="content" source="./media/failover-cluster-instance-prepare-vm/07-create-vnet-ip-address.png" alt-text="Nachdem Sie das zweite Subnetz hinzugefügt haben, überprüfen Sie Ihre Subnetznamen und -bereiche wie im Beispiel auf dem Bild (auch wenn Ihre IP-Adressen möglicherweise anders sind). Wenn alles korrekt aussieht, wählen Sie Überprüfen + Erstellen und dann Erstellen, um Ihr neues virtuelles Netzwerk zu erstellen.":::

   Azure zeigt wieder das Portaldashboard an und benachrichtigt Sie, wenn das neue Netzwerk erstellt wurde.

---

## <a name="configure-dns"></a>Konfigurieren des DNS

Konfigurieren Sie Ihr virtuelles Netzwerk für die Verwendung Ihres DNS-Servers. Ermitteln Sie zunächst die DNS-IP-Adresse und fügen Sie sie dann zu Ihrem virtuellen Netzwerk hinzu. 

### <a name="identify-dns-ip-address"></a>DNS-IP-Adresse ermitteln

Ermitteln Sie die IP-Adresse des DNS-Servers, und fügen Sie ihn der Konfiguration des virtuellen Netzwerks hinzu. In diesem Abschnitt wird gezeigt, wie Sie die DNS-IP-Adresse identifizieren, wenn sich der DNS-Server auf einer virtuellen Maschine in Azure befindet. 

Gehen Sie folgendermaßen vor, um die IP-Adresse der DNS-Server-VM im Azure-Portal zu ermitteln: 

1. Gehen Sie zu Ihrer Ressourcengruppe im [Azure-Portal](https://portal.azure.com) und wählen Sie die DNS-Server-VM. 
1. Wählen Sie auf der Seite VM die Option **Netzwerke** im Bereich **Einstellungen**. 
1. Beachten Sie die Adresse **NIC Private IP**, da dies die IP-Adresse des DNS-Servers ist. Im Beispielbild lautet die private IP-Adresse **10.38.0.4**. 

:::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/12-dc-vm-1-private-ip.png" alt-text="Wählen Sie auf der Seite DC-VM-1 im Bereich Einstellungen die Option Netzwerk, und notieren Sie sich die private IP-Adresse der Netzwerkkarte. Verwenden Sie diese IP-Adresse als DNS-Server. ":::

### <a name="configure-virtual-network-dns"></a>Virtuelles Netzwerk DNS konfigurieren

Konfigurieren Sie das virtuelle Netzwerk so, dass es die IP-Adresse des DNS-Servers verwendet. 

Führen Sie die folgenden Schritte aus, um Ihr virtuelles Netzwerk für DNS zu konfigurieren: 

1. Gehen Sie zu Ihrer Ressourcengruppe im [Azure-Portal](https://portal.azure.com) und wählen Sie Ihr virtuelles Netzwerk. 
1. Wählen Sie **DNS-Server** unter dem Fenster **Einstellungen** und dann **Benutzerdefiniert**. 
1. Geben Sie die private IP-Adresse, die Sie zuvor ermittelt haben, in das Feld **IP-Adresse** ein, z. B. `10.38.0.4`, oder geben Sie die interne IP-Adresse Ihres internen DNS-Servers an. 
1. Wählen Sie **Speichern** aus. 

:::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/12-identify-dns-ip-address.png" alt-text=" Wählen Sie im Bereich Einstellungen die Option DNS-Server und dann die Option Benutzerdefiniert. Geben Sie die private IP-Adresse, die Sie zuvor ermittelt haben, in das Feld IP-Adresse ein, z. B. 10.38.0.4. ":::

## <a name="create-the-virtual-machines"></a>Erstellen der virtuellen Computer

Nachdem Sie Ihr virtuelles VM-Netzwerk konfiguriert und die VM-Verfügbarkeit ausgewählt haben, können Sie Ihre virtuellen Maschinen erstellen. Sie können wählen, ob Sie ein Azure Marketplace-Image einsetzen möchten, bei dem SQL Server bereits installiert ist oder nicht. Wenn Sie jedoch ein Image für SQL Server auf Azure-VMs wählen, müssen Sie SQL Server vom virtuellen Computer deinstallieren, ehe Sie die Failoverclusterinstanz konfigurieren können. 

### <a name="nic-considerations"></a>NIC-Überlegungen

Bei einem Azure VM-Gast-Failover-Cluster empfehlen wir eine einzelne NIC pro Server (Clusterknoten). Azure-Netzwerke verfügen über physische Redundanz, was zusätzliche Netzwerkkarten in einem Azure IaaS VM-Gastcluster überflüssig macht. Obwohl im Clustervalidierungsbericht eine Warnung ausgegeben wird, dass die Knoten nur in einem einzigen Netzwerk erreichbar sind, kann diese Warnung für Azure IaaS-VM-Gast-Failovercluster einfach ignoriert werden.

Platzieren Sie beide virtuellen Computer wie folgt:

- In derselben Azure-Ressourcengruppe wie Ihre Verfügbarkeitsgruppe, wenn Sie mit Verfügbarkeitsgruppen arbeiten.
- Im selben virtuellen Netzwerk wie Ihr Domänencontroller und DNS-Server oder in einem virtuellen Netzwerk, das über eine geeignete Verbindung zu Ihrem Domänencontroller verfügt.
- In der Azure-Verfügbarkeitsgruppe oder- Verfügbarkeitszone.

Sie können einen virtuellen Azure-Computer erstellen, indem Sie ein Image [mit](sql-vm-create-portal-quickstart.md) oder [ohne](../../../virtual-machines/windows/quick-create-portal.md) vorinstalliertes SQL Server verwenden. Wenn Sie das SQL Server-Image wählen, müssen Sie die SQL Server-Instanz manuell deinstallieren, bevor Sie die Failoverclusterinstanz installieren.  

### <a name="assign-secondary-ip-addresses"></a>Sekundäre IP-Adressen zuweisen

Wenn Sie Ihre SQL Server-VMs in einem einzigen Subnetz bereitgestellt haben, überspringen Sie diesen Schritt. Wenn Sie Ihre SQL Server-VMs auf mehrere Subnetze verteilt haben, um die Konnektivität mit Ihrer FCI zu verbessern, müssen Sie jeder VM die sekundären IP-Adressen zuweisen. 

Weisen Sie jeder SQL Server-VM sekundäre IP-Adressen zu, die für den Netzwerknamen der Failover-Cluster-Instanz verwendet werden sollen, und für Windows Server 2016 und früher weisen Sie jeder SQL Server-VM auch sekundäre IP-Adressen für den Clusternetzwerknamen zu. Dadurch entfällt die Notwendigkeit eines Azure Load Balancer, wie er in einer Umgebung mit einem einzigen Subnetz erforderlich ist.  

Unter Windows Server 2016 und früher müssen Sie jeder SQL Server-VM eine zusätzliche sekundäre IP-Adresse zuweisen, die für die Windows-Cluster-IP verwendet wird, da der Cluster den **Cluster-Netzwerknamen** anstelle des standardmäßigen verteilten Netzwerknamens (DNN) verwendet, der in Windows Server 2019 eingeführt wurde. Bei einem DNN wird das Clusternamensobjekt (CNO) automatisch mit den IP-Adressen für alle Knoten des Clusters registriert, so dass keine spezielle Windows-Cluster-IP-Adresse erforderlich ist.

Wenn Sie Windows Server 2016 und früher verwenden, befolgen Sie die Schritte in diesem Abschnitt, um jeder SQL Server-VM eine sekundäre IP-Adresse für *den FCI-Netzwerknamen* und *den Cluster* zuzuweisen. 

Wenn Sie Windows Server 2019 oder höher verwenden, weisen Sie nur eine sekundäre IP-Adresse für den FCI-Netzwerknamen zu und überspringen Sie die Schritte zur Zuweisung einer Windows-Cluster-IP, es sei denn, Sie planen, Ihren Cluster mit einem virtuellen Netzwerknamen (VNN) zu konfigurieren. In diesem Fall weisen Sie jeder SQL Server-VM beide IP-Adressen zu, wie Sie es bei Windows Server 2016 tun würden. 

Um den VMs zusätzliche sekundäre IPs zuzuweisen, gehen Sie wie folgt vor: 

1. Gehen Sie zu Ihrer Ressourcengruppe im [Azure-Portal](https://portal.azure.com/) und wählen Sie die erste SQL Server-VM. 
1. Wählen Sie **Netzwerk** in der Karteikarte **Einstellungen** und dann die **Netzwerkschnittstelle**: 

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/19-sql-vm-network-interface.png" alt-text="Wählen Sie im Bereich Einstellungen die Option Netzwerke und dann die Netzwerkschnittstelle":::

1. Wählen Sie auf der Seite **Netzwerkschnittstelle** die Option **IP-Konfigurationen** im Bereich **Einstellungen** und wählen Sie dann **+ Hinzufügen**, um eine zusätzliche IP-Adresse hinzuzufügen: 

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/20-ip-configurations-add.png" alt-text="IP-Konfigurationen":::

1. Gehen Sie auf der Seite **IP-Konfiguration hinzufügen** wie folgt vor: 
   1. Geben Sie den **Name** für die Windows-Cluster-IP-Adresse an, beispielsweise **windows-cluster-ip** für Windows 2016 und früher. Überspringen Sie diesen Schritt, wenn Sie mit Windows Server 2019 oder höher arbeiten. 
   1. Setzen Sie die **Zuweisung** auf **Statisch**.
   1. Geben Sie eine unbenutzte **IP-Adresse** im selben Subnetz (**SQL-Subnetz-1**) wie die SQL Server-VM ein, z. B. `10.38.1.10`. 
   1. Belassen Sie die **Öffentliche IP-Adresse** auf dem Standardwert von **Disassoziieren**. 
   1. Wählen Sie **OK**, um das Hinzufügen der IP-Konfiguration abzuschließen. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/21-add-ip-windows-cluster.png" alt-text=" Fügen Sie eine Cluster-IP hinzu, indem Sie eine verwendete IP-Adresse im Subnetz der ersten SQL Server-VM eingeben ":::

1. Wählen Sie erneut **+ Hinzufügen**, um eine zusätzliche IP-Adresse für den FCI-Netznamen zu konfigurieren (mit einem Namen wie **FCI-Netzname**), wobei Sie wiederum eine nicht verwendete IP-Adresse in **SQL-Subnetz-1** wie `10.38.1.11` angeben: 

   :::image type="content" source="./media/failover-cluster-instance-prepare-vm/22-add-fci-ip-address.png" alt-text="Wählen Sie erneut + Hinzufügen, um eine zusätzliche IP-Adresse für den Verfügbarkeitsgruppen-Listener zu konfigurieren (mit einem Namen wie availability-group-listener), wiederum unter Verwendung einer unbenutzten IP-Adresse im SQL-Subnetz-1 wie 10.31.1.11":::

1. Wiederholen Sie diese Schritte auch für die zweite SQL Server-VM. Weisen Sie zwei ungenutzte sekundäre IP-Adressen im **SQL-Subnetz-2** zu. Verwenden Sie die Werte aus der folgenden Tabelle, um die IP-Konfiguration hinzuzufügen (die IP-Adressen sind jedoch nur Beispiele, Ihre können abweichen): 

   
    | **Feld** | Eingabe | Eingabe | 
    | --- | --- | --- |
    | **Name** |windows-cluster-ip | FCI-Netz-Name |
    | **Speicherbelegung** | statischen | statischen |
    | **IP-Adresse** | 10.38.2.10 | 10.38.2.11 | 
    |  |  |  |



## <a name="uninstall-sql-server"></a>Deinstallieren von SQL Server

Im Rahmen des Prozesses der FCI-Erstellung installieren Sie SQL Server als Clusterinstanz im Failovercluster. *Wenn Sie einen virtuellen Computer mit Azure Marketplace-Image ohne SQL Server bereitgestellt haben, können Sie diesen Schritt überspringen.* Wenn Sie ein Image mit vorinstallierter SQL Server-Instanz bereitgestellt haben, müssen Sie die Registrierung der SQL-IaaS-Agent-Erweiterung für die SQL Server-VM aufheben und SQL Server anschließend deinstallieren. 

### <a name="unregister-from-the-sql-iaas-agent-extension"></a>Aufheben der Registrierung der SQL-IaaS-Agent-Erweiterung für die VM

SQL Server-VM-Images aus Azure Marketplace werden automatisch mit der SQL-IaaS-Agent-Erweiterung registriert. Bevor Sie die vorinstallierte SQL Server-Instanz deinstallieren, müssen Sie zuerst [die Registrierung der SQL-IaaS-Agent-Erweiterung für jede SQL Server-VM aufheben](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension). 

### <a name="uninstall-sql-server"></a>Deinstallieren von SQL Server

Nachdem Sie die Registrierung der Erweiterung aufgehoben haben, können Sie SQL Server deinstallieren. Befolgen Sie dazu auf jedem virtuellen Computer diese Schritte: 

1. Verbindung mit diesem virtuellen Computer über RDP herstellen Wenn Sie die Verbindung mit einem virtuellen Computer per RDP zum ersten Mal herstellen, wird gefragt, ob Sie zulassen möchten, dass dieser PC über das Netzwerk ermittelt werden kann. Wählen Sie **Ja** aus.
1. Öffnen Sie **Programme und Funktionen** in der **Systemsteuerung**. 
1. Klicken Sie unter **Programme und Features** mit der rechten Maustaste auf **Microsoft SQL Server 201_ (64-Bit)** , und wählen Sie dann **Deinstallieren/Ändern** aus.
1. Wählen Sie **Entfernen**.
1. Wählen Sie die Standardinstanz aus.
1. Entfernen Sie alle Funktionen unter **Database Engine Services**, **Analysis Services** und **Reporting Services - Native**. Entfernen Sie nichts unter **SharedFeatures**. Sie sehen dann etwas wie den folgenden Screenshot: ![Merkmale auswählen](./media/failover-cluster-instance-prepare-vm/remove-features-updated.png)
1. Wählen Sie **Weiter** und anschließend **Entfernen** aus.
1. Nachdem die Instanz erfolgreich entfernt wurde, starten Sie den virtuellen Computer neu. 

## <a name="open-the-firewall"></a>Öffnen der Firewall 

Öffnen Sie auf jedem virtuellen Computer den von SQL Server verwendeten TCP-Port in der Windows-Firewall. Standardmäßig verwendet SQL Server Port 1433. Wenn Sie dies in Ihrer Umgebung geändert haben, öffnen Sie den Port, für den Sie Ihre SQL Server-Instanz konfiguriert haben. Port 1433 ist automatisch für SQL Server-Images geöffnet, die über den Azure Marketplace bereitgestellt werden. 

Wenn Sie einen [Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) für ein einzelnes Subnetz-Szenario verwenden, müssen Sie auch den Port öffnen, den die Health Probe verwendet. Standardmäßig verwendet der Health Probe den Port 59999, aber es kann jeder TCP-Port sein, den Sie bei der Erstellung des Load Balancers angeben. 

In dieser Tabelle sind die Ports aufgeführt, die Sie je nach Ihrer FCI-Konfiguration möglicherweise öffnen müssen: 

   | Zweck | Port | Notizen
   | ------ | ------ | ------
   | SQL Server | TCP 1433 | Dies ist der normale Port für Standardinstanzen von SQL Server. Falls Sie ein Image aus dem Katalog verwendet haben, ist dieser Port automatisch geöffnet. </br> </br> **Verwendet von**: Allen FCI-Konfigurationen. |
   | Integritätstest | TCP 59999 | Beliebiger geöffneter TCP-Port. Konfigurieren Sie diesen Port für den [Integritätstest](failover-cluster-instance-vnn-azure-load-balancer-configure.md#configure-health-probe) des Lastenausgleichs und den Cluster. </br> </br> **Verwendet von**: FCI mit Load Balancer in einem einzigen Subnetz-Szenario. |
   | Dateifreigabe | UDP 445 | Der vom Dateifreigabedienst verwendete Port. </br> </br> **Verwendet von**: FCI mit Premium-Dateifreigabe. |

## <a name="join-the-domain"></a>Beitreten zur Domäne

Außerdem müssen Ihre virtuellen Computer der Domäne beitreten. Nutzen Sie hierfür eine [Schnellstartvorlage](../../../active-directory-domain-services/join-windows-vm-template.md#join-an-existing-windows-server-vm-to-a-managed-domain). 

## <a name="review-storage-configuration"></a>Überprüfen der Speicherkonfiguration

Über Azure Marketplace erstellte virtuelle Computer werden mit angefügtem Speicher bereitgestellt. Wenn Sie planen, Ihren FCI-Speicher unter Verwendung von Premium-Dateifreigaben oder freigegebenen Azure-Datenträgern zu konfigurieren, können Sie den angefügten Speicher entfernen, um Kosten zu sparen, da lokaler Speicher nicht für die Failoverclusterinstanz genutzt wird. Es ist jedoch möglich, den angefügten Speicher für FCI-Lösungen mit „Direkte Speicherplätze“ zu verwenden, sodass es in diesem Fall nicht unbedingt sinnvoll ist, ihn zu entfernen. Überprüfen Sie Ihre FCI-Speicherlösung, um herauszufinden, ob das Entfernen von angefügtem Speicher optimal ist, um Kosten zu sparen. 


## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Umgebung mit virtuellen Computern vorbereitet haben, können Sie nun Ihre Failoverclusterinstanz konfigurieren. 

Befolgen Sie einen der folgenden Leitfäden, um die für Ihr Unternehmen geeignete FCI-Umgebung zu konfigurieren: 
- [Konfigurieren der FCI mit freigegebenen Azure-Datenträgern](failover-cluster-instance-azure-shared-disks-manually-configure.md)
- [Konfigurieren der FCI mit einer Premium-Dateifreigabe](failover-cluster-instance-premium-file-share-manually-configure.md)
- [Konfigurieren der FCI mit „Direkte Speicherplätze“](failover-cluster-instance-storage-spaces-direct-manually-configure.md)


Weitere Informationen finden Sie unter:

- [Windows Server-Failovercluster mit SQL Server auf Azure-VMs](hadr-windows-server-failover-cluster-overview.md)
- [Failoverclusterinstanzen mit SQL Server auf Azure-VMs](failover-cluster-instance-overview.md)
- [AlwaysOn-Failoverclusterinstanzen (SQL Server)](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
- [HADR-Einstellungen für SQL Server auf Azure-VMs](hadr-cluster-best-practices.md)
