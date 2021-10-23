---
title: Hochverfügbarkeit, Notfallwiederherstellung, Geschäftskontinuität
description: Erfahren Sie mehr über die Optionen für Hochverfügbarkeit, Notfallwiederherstellung und Geschäftskontinuität, die für SQL Server auf Azure-VMs verfügbar sind, etwa Always On-Verfügbarkeitsgruppen, Failoverclusterinstanzen, Datenbankspiegelung, Protokollversand sowie Sicherung und Wiederherstellen in Azure Storage.
services: virtual-machines-windows
documentationcenter: na
author: rajeshsetlem
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2020
ms.author: rsetlem
ms.reviewer: mathoma
ms.openlocfilehash: f5dd02d8abf70d3812fe0d07baa8ddf08bdc8a7a
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130166071"
---
# <a name="business-continuity-and-hadr-for-sql-server-on-azure-virtual-machines"></a>Geschäftskontinuität und HADR für SQL Server in Azure Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Geschäftskontinuität bedeutet, dass Sie Ihr Geschäft trotz eines Notfalls fortsetzen, die Wiederherstellung planen und sicherstellen können, dass Ihre Daten hochverfügbar sind. SQL Server in Azure Virtual Machines kann die Kosten für Datenbanklösungen mit Hochverfügbarkeit und Notfallwiederherstellung (High Availability, Disaster Recovery – HADR) senken. 

Die meisten SQL Server-HADR-Lösungen werden auf virtuellen Computern (VMs) als reine Azure- und als Hybridlösungen unterstützt. In einer reinen Azure-Lösung wird das gesamte HADR-System in Azure ausgeführt. In einer Hybridkonfiguration wird ein Teil der Lösung in Azure und der andere Teil lokal in Ihrer Organisation ausgeführt. Die Flexibilität der Azure-Umgebung können Sie teilweise oder vollständig in Azure verschieben, um Budget- und HADR-Anforderungen der SQL Server-Datenbanksysteme zu erfüllen.

In diesem Artikel werden die Geschäftskontinuitätslösungen verglichen und gegenübergestellt, die für SQL Server auf Azure-VMs verfügbar sind. 

## <a name="overview"></a>Übersicht

Es ist Ihre Aufgabe sicherzustellen, dass Ihr Datenbanksystem über die gemäß der Vereinbarung zum Servicelevel (SLA) erforderlichen HADR-Funktionen verfügt. Die Tatsache, dass Azure Funktionen für Hochverfügbarkeit bereitstellt, z. B. die Selbstreparatur für Clouddienste und die Wiederherstellungserkennung für virtuelle Computer, gewährleistet noch nicht, dass Sie die Vereinbarung zum Servicelevel erfüllen können. Auch wenn diese Mechanismen Hochverfügbarkeit der virtuellen Computer sicherstellen, schützen Sie nicht die Verfügbarkeit der auf den virtuellen Computern ausgeführten SQL Server-Instanzen. 

Es ist möglich, dass eine SQL Server-Instanz ausfällt, obwohl die VM online und betriebsbereit ist. Und selbst die Funktionen für Hochverfügbarkeit von Azure lassen Ausfallzeiten von virtuellen Computern zu, z. B. für die Wiederherstellung nach Software- oder Hardwareausfällen und zur Ermöglichung von Betriebssystemupgrades.

Georedundanter Speicher (GRS) in Azure wird mit einer Funktion namens „Georeplikation“ implementiert. GRS ist möglicherweise keine geeignete Notfallwiederherstellungslösung für Ihre Datenbanken. Da bei der Georeplikation Daten asynchron gesendet werden, können die neuesten Updates bei einem Ausfall verloren gehen. Weitere Informationen zu Einschränkungen bei der Georeplikation finden Sie im Abschnitt [Georeplikationsunterstützung](#geo-replication-support).

> [!NOTE]
> Sie können Ihre [Failoverclusterinstanz](../../migration-guides/virtual-machines/sql-server-failover-cluster-instance-to-sql-on-azure-vm.md) und [Verfügbarkeitsgruppenlösung](../../migration-guides/virtual-machines/sql-server-availability-group-to-sql-on-azure-vm.md) jetzt mithilfe von Azure Migrate per Lift-und-Shift-Verfahren zu SQL Server auf Azure-VMs verschieben. 


## <a name="deployment-architectures"></a>Bereitstellungsarchitekturen
Azure unterstützt die folgenden SQL Server-Technologien für Geschäftskontinuität:

* [Always On-Verfügbarkeitsgruppen](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)
* [Always On-Failoverclusterinstanzen (FCIs)](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
* [Protokollversand](/sql/database-engine/log-shipping/about-log-shipping-sql-server)
* [SQL Server-Sicherung und -Wiederherstellung mit dem Microsoft Azure Blob Storage](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
* [Datenbankspiegelung](/sql/database-engine/database-mirroring/database-mirroring-sql-server): Seit SQL Server 2016 veraltet
* [Azure Site Recovery](../../../site-recovery/site-recovery-sql.md)

Sie können mehrere Technologie kombinieren, um eine SQL Server-Lösung zu implementieren, die über Funktionen für Hochverfügbarkeit und Notfallwiederherstellung verfügt. Abhängig von der verwendeten Technologie erfordert eine Hybridbereitstellung ggf. einen VPN-Tunnel mit dem virtuellen Azure-Netzwerk. In den folgenden Abschnitten werden einige Beispiele für Bereitstellungsarchitekturen vorgestellt.

## <a name="azure-only-high-availability-solutions"></a>Reines Azure: Lösungen mit hoher Verfügbarkeit

Mit Always On-Verfügbarkeitsgruppen können Sie eine Lösung mit Hochverfügbarkeit für SQL Server auf Datenbankebene einsetzen. Sie können auch eine Lösung mit Hochverfügbarkeit auf Instanzebene mit Always On-Failoverclusterinstanzen erstellen. Für zusätzlichen Schutz können Sie Redundanz auf beiden Ebenen erstellen, indem Sie Verfügbarkeitsgruppen für Failoverclusterinstanzen erstellen. 

| Technologie | Beispielarchitekturen |
| --- | --- |
| **Verfügbarkeitsgruppen** |Verfügbare Replikate, die auf Azure-VMs in derselben Region ausgeführt werden, bieten Hochverfügbarkeit. Sie müssen eine Domänencontroller-VM konfigurieren, da für das Windows-Failoverclustering eine Active Directory-Domäne erforderlich ist.<br/><br/> Für höhere Redundanz und Verfügbarkeit können die Azure-VMs in unterschiedlichen [Verfügbarkeitszonen](../../../availability-zones/az-overview.md) bereitgestellt werden, wie in der [Übersicht über Verfügbarkeitsgruppen](availability-group-overview.md) beschrieben. Wenn die SQL Server-VMs in einer Verfügbarkeitsgruppe in Verfügbarkeitszonen bereitgestellt werden, verwenden Sie [Azure Load Balancer Standard](../../../load-balancer/load-balancer-overview.md) für Listener, wie in den Artikeln [Befehlszeilenschnittstelle für virtuelle Azure SQL-Computer](./availability-group-az-commandline-configure.md) und [Azure-Schnellstartvorlagen](availability-group-quickstart-template-configure.md) beschrieben.<br/> ![Diagramm, das den „Domänencontroller“ über dem „WSFC-Cluster" zeigt, der aus dem „primären Replikat“, „sekundären Replikat“ und „Dateifreigabenzeugen“ besteht.](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-ha-always-on.png)<br/>Weitere Informationen finden Sie unter [Konfigurieren von Verfügbarkeitsgruppen in Azure (GUI)](./availability-group-quickstart-template-configure.md). |
| **Failoverclusterinstanzen** |Failoverclusterinstanzen werden auf SQL Server-VMs unterstützt. Da das FCI-Feature freigegebenen Speicher erfordert, funktionieren fünf Lösungen mit SQL Server auf Azure-VMs: <br/><br/> – Verwenden von [freigegebenen Azure-Datenträgern](failover-cluster-instance-azure-shared-disks-manually-configure.md) für Windows Server 2019. Freigegebene verwaltete Datenträger sind ein Azure-Produkt, das das gleichzeitige Anfügen eines verwalteten Datenträgers an mehrere virtuelle Computer zulässt. VMs im Cluster können von Ihrem angefügten Datenträger lesen oder auf ihn schreiben. Dies ist abhängig von der Reservierung, die von der gruppierten Anwendung über SCSI Persistent Reservations (SCSI PR) ausgewählt wurde. Bei SCSI PR handelt es sich um eine Speicherlösung nach Branchenstandard, der von Anwendungen im lokalen Storage Area Network (SAN) genutzt wird. Wenn Sie SCSI PR für einen verwalteten Datenträger aktivieren, können Sie diese Anwendungen unverändert zu Azure migrieren. <br/><br/>– Verwenden [direkter Speicherplätze \(S2D\)](failover-cluster-instance-storage-spaces-direct-manually-configure.md), um ein softwarebasiertes virtuelles SAN für Windows Server 2016 oder höher bereitzustellen.<br/><br/>– Verwenden einer [Premium-Dateifreigabe](failover-cluster-instance-premium-file-share-manually-configure.md) für Windows Server 2012 oder höher. Premium-Dateifreigaben sind SSD-gestützte Dateifreigaben mit konsistent geringer Latenz, die für die Verwendung mit der Failoverclusterinstanz vollständig unterstützt werden.<br/><br/>– Verwenden von Speicher, der von einer Partnerlösung für Clustering unterstützt wird. Ein spezifisches Beispiel mit SIOS DataKeeper finden Sie im Blogbeitrag [Failoverclustering und SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>– Verwenden von freigegebenem Blockspeicher für ein iSCSI-Remoteziel über Azure ExpressRoute. Beispielsweise macht NetApp Private Storage (NPS) ein iSCSI-Ziel über ExpressRoute mit Equinix für virtuelle Azure-Computer verfügbar.<br/><br/>Für gemeinsame Speichernutzungs- und Datenreplikationslösungen von Microsoft-Partnern sollten Sie sich an den Hersteller wenden, falls beim Failover Probleme in Bezug auf den Datenzugriff auftreten.<br/><br/>|

## <a name="azure-only-disaster-recovery-solutions"></a>Reines Azure: Notfallwiederherstellungslösungen
Sie können eine Notfallwiederherstellungslösung für Ihre SQL Server-Datenbanken in Azure mit Verfügbarkeitsgruppen, Datenbankspiegelung oder Sicherung und Wiederherstellung mit Speicherblobs umsetzen.

| Technologie | Beispielarchitekturen |
| --- | --- |
| **Verfügbarkeitsgruppen** |Die Replikate für die Verfügbarkeit werden in mehreren Rechenzentren auf virtuellen Azure-Computern für die Notfallwiederherstellung ausgeführt. Diese regionsübergreifende Lösung bietet Schutz vor dem vollständigen Ausfall einzelner Standorte. <br/> ![Diagramm, das zwei Regionen mit einem „primären Replikat“ und einem „sekundären Replikat“ zeigt, die durch einen asynchronen Commit verbunden sind.](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-alwayson.png)<br/>Alle Replikate innerhalb einer Region sollten sich in demselben Clouddienst und im gleichen virtuellen Netzwerk befinden. Da jede Region über ein separates virtuelles Netzwerk verfügt, erfordern diese Lösungen eine Verbindung zwischen den Netzwerken. Weitere Informationen finden Sie unter [Konfigurieren einer Verbindung zwischen Netzwerken mit dem Azure-Portal](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). Ausführliche Anweisungen finden Sie unter [Konfigurieren einer SQL Server Always On-Verfügbarkeitsgruppe in verschiedenen Azure-Regionen](availability-group-manually-configure-multiple-regions.md).|
| **Spiegeln von Datenbanken** |Prinzipale, Spiegelungen und Server werden in verschiedenen Rechenzentren für die Notfallwiederherstellung ausgeführt. Sie müssen diese mithilfe von Serverzertifikaten bereitstellen. SQL Server-Datenbankspiegelung wird für SQL Server 2008 und SQL Server 2008 R2 auf einer Azure-VM nicht unterstützt. <br/>![Diagramm, das den „Prinzipal“ in einer Region zeigt, der mit dem Spiegel in einer anderen Region mit „Hochleistung“ verbunden ist.](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-dbmirroring.png) |
| **Sicherung und Wiederherstellung mit Azure Blob Storage** |Die Produktionsdatenbanken werden direkt im Blobspeicher in einem anderen Rechenzentrum für die Notfallwiederherstellung gesichert.<br/>![Diagramm, das eine „Datenbank“ in einer Region zeigt, die Daten in „Blob Storage“ in einer anderen Region sichert.](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-backup-restore.png)<br/>Weitere Informationen finden Sie unter [Sicherung und Wiederherstellung für SQL Server auf Azure-VMs](../../../azure-sql/virtual-machines/windows/backup-restore.md). |
| **Replikation und Failover von SQL Server zu Azure mit Azure Site Recovery** |Die SQL Server-Produktionsinstanz in einem Azure-Rechenzentrum, die für die Notfallwiederherstellung direkt in Azure Storage in einem anderen Azure-Rechenzentrum repliziert wird.<br/>![Diagramm, das eine „Datenbank“ in einem Azure-Rechenzentrum unter Verwendung der ASR-Replikation für die Notfallwiederherstellung in einem anderen Rechenzentrum zeigt. ](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-standalone-sqlserver-asr.png)<br/>Weitere Informationen finden Sie unter [Schützen von SQL Server mit der Notfallwiederherstellung von SQL Server und Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="hybrid-it-disaster-recovery-solutions"></a>Hybrid-IT: Notfallwiederherstellungslösungen
Sie können eine Notfallwiederherstellungslösung für Ihre SQL Server-Datenbanken in einer IT-Hybridumgebung mit Verfügbarkeitsgruppen, Datenbankspiegelung, Protokollversand und Sicherung/Wiederherstellung mit Azure Blob Storage nutzen.

| Technologie | Beispielarchitekturen |
| --- | --- |
| **Verfügbarkeitsgruppen** |Einige Replikate für die Verfügbarkeit werden auf virtuellen Azure-Computern ausgeführt und andere lokal, um eine standortübergreifende Notfallwiederherstellung sicherzustellen. Der Produktionsstandort kann sich entweder vor Ort oder in einem Azure-Rechenzentrum befinden.<br/>![Verfügbarkeitsgruppen](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-alwayson.png)<br/>Da sich alle Verfügbarkeitsreplikate in demselben Failovercluster befinden müssen, muss der Cluster beide Netzwerke (ein Failovercluster in mehreren Subnetzen) einschließen. Diese Konfiguration erfordert eine VPN-Verbindung zwischen Azure und dem lokalen Netzwerk.<br/><br/>Für die erfolgreiche Notfallwiederherstellung der Datenbanken sollten Sie auch einen Replikatdomänencontroller am Standort für die Notfallwiederherstellung installieren.|
| **Spiegeln von Datenbanken** |Ein Partner wird auf einer Azure-VM und der andere lokal ausgeführt, um standortübergreifende Notfallwiederherstellung unter Verwendung von Serverzertifikaten zu gewährleisten. Die Partner müssen sich nicht in derselben Active Directory-Domäne befinden, und es ist keine VPN-Verbindung erforderlich.<br/>![Spiegeln von Datenbanken](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-dbmirroring.png)<br/>Ein anderes Szenario zur Datenbankspiegelung sieht einen Partner vor, der auf einem virtuellen Azure-Computer ausgeführt wird, während der andere lokal in derselben Active Directory-Domäne für die standortübergreifende Notfallwiederherstellung ausgeführt wird. Eine [VPN-Verbindung zwischen dem virtuellen Azure-Netzwerk und dem lokalen Netzwerk](../../../vpn-gateway/tutorial-site-to-site-portal.md) ist erforderlich.<br/><br/>Für die erfolgreiche Notfallwiederherstellung der Datenbanken sollten Sie auch einen Replikatdomänencontroller am Standort für die Notfallwiederherstellung installieren. SQL Server-Datenbankspiegelung wird für SQL Server 2008 und SQL Server 2008 R2 auf einer Azure-VM nicht unterstützt. |
| **Protokollversand** |Ein Server wird auf einem virtuellen Azure-Computer und der andere lokal für die standortübergreifende Notfallwiederherstellung ausgeführt. Für das Versenden von Protokollen ist der Windows-Dateiaustausch erforderlich, deshalb muss eine VPN-Verbindung zwischen dem virtuellen Azure-Netzwerk und dem lokalen Netzwerk bestehen.<br/>![Protokollversand](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-log-shipping.png)<br/>Für die erfolgreiche Notfallwiederherstellung der Datenbanken sollten Sie auch einen Replikatdomänencontroller am Standort für die Notfallwiederherstellung installieren. |
| **Sicherung und Wiederherstellung mit Azure Blob Storage** |Die lokalen Produktionsdatenbanken werden direkt in Azure Blob Storage für Notfallwiederherstellung gesichert.<br/>![Sichern und Wiederherstellen](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-backup-restore.png)<br/>Weitere Informationen finden Sie unter [Sicherung und Wiederherstellung für SQL Server in Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/backup-restore.md). |
| **Replikation und Failover von SQL Server zu Azure mit Azure Site Recovery** |Die lokale SQL Server-Produktionsinstanz wird für Notfallwiederherstellung direkt in Azure Storage repliziert.<br/>![Replikation mithilfe von Azure Site Recovery](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-standalone-sqlserver-asr.png)<br/>Weitere Informationen finden Sie unter [Schützen von SQL Server mit der Notfallwiederherstellung von SQL Server und Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="free-dr-replica-in-azure"></a>Kostenloses DR-Replikat in Azure

Wenn Sie über [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3) verfügen, können Sie hybride Notfallwiederherstellungspläne mit SQL Server implementieren, ohne dass zusätzliche Lizenzierungskosten für die passive Notfallwiederherstellungsinstanz anfallen.

Beispielsweise können Sie zwei kostenlose passive sekundäre Replikate haben, wenn alle drei Replikate in Azure gehostet werden: 

![Zwei kostenlose passive Replikate bei ausschließlichem Hosten in Azure](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/failover-with-primary-in-azure.png)

Alternativ können Sie eine Hybridfailoverumgebung mit einem lizenzierten primären lokalen Replikat, einem kostenlosen passiven Replikat für die Hochverfügbarkeit, einem kostenlosen passiven Replikat für die lokale Notfallwiederherstellung und einem kostenlosen passiven Replikat für die Notfallwiederherstellung in Azure konfigurieren:

![Drei kostenlose passive Replikate in Hybridumgebung mit einem primären lokalen Replikat](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-with-primary-on-prem.png)

Weitere Informationen finden Sie in den [Produktlizenzierungsbedingungen](https://www.microsoft.com/licensing/product-licensing/products). 

Navigieren Sie zu Ihrer [SQL Server-VM-Ressource](manage-sql-vm-portal.md#access-the-resource), um diesen Vorteil zu aktivieren. Wählen Sie **Konfigurieren** unter **Einstellungen** aus, und wählen Sie dann die Option **Notfallwiederherstellung** unter **SQL Server-Lizenz** aus. Aktivieren Sie das Kontrollkästchen, um zu bestätigen, dass diese SQL Server-VM als passives Replikat verwendet wird, und wählen Sie dann **Anwenden** aus, um die Einstellungen zu speichern. 

![Konfigurieren eines Notfallwiederherstellungsreplikats in Azure](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/dr-replica-in-portal.png)


## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Wichtige Überlegungen zu SQL Server-HADR in Azure
Für die virtuellen Azure-Computer, den Speicher und die Netzwerkressourcen gelten andere Betriebsbedingungen als für eine lokale, nicht virtualisierte IT-Infrastruktur. Für eine erfolgreiche Implementierung einer SQL Server-HADR-Lösung in Azure sollten Sie diese Unterschiede kennen und Ihre Lösung daran anpassen.

### <a name="high-availability-nodes-in-an-availability-set"></a>Hochverfügbarkeitsknoten in einer Verfügbarkeitsgruppe
Durch Verfügbarkeitsgruppen in Azure können Sie Hochverfügbarkeitsknoten in separaten Fehler- und Updatedomänen platzieren. Die Azure-Plattform weist jedem virtuellen Computer in Ihrer Verfügbarkeitsgruppe eine Updatedomäne und eine Fehlerdomäne zu. Durch diese Konfiguration in einem Datencenter wird sichergestellt, dass während eines geplanten oder ungeplanten Wartungsereignisses mindestens ein virtueller Computer verfügbar ist und die von der Azure-SLA zugesicherte Verfügbarkeit von 99,95 Prozent eingehalten wird. 

Um die Hochverfügbarkeitseinrichtung zu konfigurieren, platzieren Sie alle beteiligten virtuellen SQL Server-Computer in derselben Verfügbarkeitsgruppe, damit es bei einem Wartungsereignis nicht zu Anwendungs- oder Datenverlust kommt. Nur Knoten in demselben Clouddienst können Mitglieder derselben Verfügbarkeitsgruppe sein. Weitere Informationen finden Sie unter [Verwalten der Verfügbarkeit virtueller Computer](../../../virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="high-availability-nodes-in-an-availability-zone"></a>Hochverfügbarkeitsknoten in einer Verfügbarkeitszone
Verfügbarkeitszonen sind eindeutige physische Standorte in einer Azure-Region. Jede Zone besteht aus mindestens einem Datencenter mit eigener Stromversorgung, Kühlung und Netzwerk. Die physische Trennung von Verfügbarkeitszonen innerhalb einer Region schützt Anwendungen und Daten vor Rechenzentrumsausfällen, indem sichergestellt wird, dass mindestens ein virtueller Computer verfügbar ist. So wird eine Azure-SLA (Vereinbarung zum Servicelevel) von 99,99 Prozent erreicht. 

Um Hochverfügbarkeit zu konfigurieren, platzieren Sie die beteiligten virtuellen SQL Server-Computer verteilt auf die verfügbaren Verfügbarkeitszonen der Region. Es fallen zusätzliche Gebühren für Übertragungen zwischen Netzwerken zwischen Verfügbarkeitszonen an. Weitere Informationen finden Sie unter [Verfügbarkeitszonen](../../../availability-zones/az-overview.md). 

### <a name="network-latency-in-hybrid-it"></a>Netzwerklatenz bei Hybridlösungen
Stellen Sie die HADR-Lösung mit der Vorgabe bereit, dass möglicherweise Zeiträume mit hoher Netzwerklatenz zwischen dem lokalen Netzwerk und Azure auftreten. Wenn Sie Replikate in Azure bereitstellen, verwenden Sie als Synchronisierungsmodus asynchrone Commits anstelle von synchronen Commits. Beim Bereitstellen von Datenbankspiegelungsservern sollten Sie sowohl lokal als auch in Azure den Modus für hohe Leistung anstelle des Modus für hohe Sicherheit verwenden.

Weitere Informationen zu hilfreichen Cluster- und HADR-Einstellungen für die Cloudumgebung finden Sie in den [bewährten Methoden für die HADR-Konfiguration](hadr-cluster-best-practices.md). 

### <a name="geo-replication-support"></a>Georeplikationssupport
Die Georeplikation von Azure-Datenträgern unterstützt nicht das Speichern von Datendatei und Protokolldatei derselben Datenbank auf unterschiedlichen Datenträgern. Der GRS repliziert Änderungen auf jedem Datenträger unabhängig und asynchron. Durch dieses Vorgehen wird die Schreibreihenfolge auf einem einzelnen Datenträgers in der georeplizierten Kopie sichergestellt, aber nicht die von georeplizierten Kopien mehrerer Datenträger. Wenn Sie eine Datenbank zum Speichern von Datendatei und Protokolldatei auf separaten Datenträgern konfigurieren, enthalten die nach einem Notfall wiederhergestellten Datenträger möglicherweise eine aktuellere Kopie der Datendatei als die Protokolldatei. Dies kann das Write-Ahead-Protokoll in SQL Server und die ACID-Eigenschaften von Transaktionen beschädigen. 

Wenn Sie Georeplikation für das Speicherkonto nicht deaktivieren können, speichern Sie alle Daten- und Protokolldateien für eine Datenbank auf dem gleichen Datenträger. Wenn Sie aufgrund der Datenbankgröße mehrere Datenträger verwenden müssen, stellen Sie eine der weiter oben aufgeführten Notfallwiederherstellungslösungen bereit, um Datenredundanz zu gewährleisten.

## <a name="next-steps"></a>Nächste Schritte

Entscheiden Sie, ob eine [Verfügbarkeitsgruppe](availability-group-overview.md) oder eine [Failoverclusterinstanz](failover-cluster-instance-overview.md) die beste Geschäftskontinuitätslösung für Ihr Unternehmen ist. Machen Sie sich dann mit den [bewährten Methoden](hadr-cluster-best-practices.md) zum Konfigurieren Ihrer Umgebung für Hochverfügbarkeit und Notfallwiederherstellung vertraut.
