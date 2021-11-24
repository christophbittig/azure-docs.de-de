---
title: Übersicht über Windows Server-Failovercluster
description: 'Lernen Sie die Unterschiede bei der Windows Server-Failoverclustertechnologie bei Verwendung mit SQL Server auf Azure-VMs kennen, z. B. Verfügbarkeitsgruppen und Failoverclusterinstanzen. '
services: virtual-machines
documentationCenter: na
author: rajeshsetlem
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/10/2021
ms.author: rsetlem
ms.reviewer: mathoma
ms.openlocfilehash: 66899b7b4c5a9cb77b7545d671ac27433f927d5a
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132156879"
---
# <a name="windows-server-failover-cluster-with-sql-server-on-azure-vms"></a>Windows Server-Failovercluster mit SQL Server auf Azure-VMs
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In diesem Artikel werden die Unterschiede bei der Verwendung des Features „Windows Server-Failovercluster“ mit SQL Server auf Azure-VMs für Hochverfügbarkeit und Notfallwiederherstellung (HADR) beschrieben, z. B. für Always On-Verfügbarkeitsgruppen oder Failoverclusterinstanzen. 

Weitere Informationen zum Windows-Feature selbst finden Sie in der [Dokumentation zum Windows Server-Failovercluster](/windows-server/failover-clustering/failover-clustering-overview).

## <a name="overview"></a>Übersicht

SQL Server-Hochverfügbarkeitslösungen unter Windows, z. B. Always On-Verfügbarkeitsgruppen oder Failoverclusterinstanzen, basieren auf dem zugrunde liegenden WSFC-Dienst (Windows Server Failover Clustering).

Der Clusterdienst überwacht Netzwerkverbindungen und die Integrität der Knoten im Cluster. Diese Überwachung erfolgt zusätzlich zu den Integritätsprüfungen, die SQL Server im Rahmen der Features „Verfügbarkeitsgruppe“ oder „Failoverclusterinstanz“ durchführt. Wenn der Clusterdienst den Knoten nicht erreichen kann oder Rolle „Verfügbarkeitsgruppe“ oder „Failoverclusterinstanz“ im Cluster fehlerhaft wird, leitet der Clusterdienst entsprechende Wiederherstellungsaktionen ein, um Anwendungen und Dienste wiederherzustellen und online zu schalten – und zwar entweder auf demselben oder einem anderen Knoten im Cluster.

## <a name="cluster-health-monitoring"></a>Systemüberwachung des Clusters

Um Hochverfügbarkeit zu gewährleisten, muss der Cluster die Integrität der verschiedenen Komponenten sicherstellen, aus denen die gruppierte Lösung besteht. Der Clusterdienst überwacht die Integrität des Clusters basierend auf einer Reihe von System- und Netzwerkparametern, um Fehler zu erkennen und darauf zu reagieren. 

Das Festlegen des Schwellenwerts für das Deklarieren eines Fehlers ist wichtig, um ein Gleichgewicht zwischen der umgehenden Reaktion auf einen Fehler und der Vermeidung vermeintlicher Fehler zu erzielen.

Es gibt zwei Strategien für die Überwachung:

| Überwachung  | BESCHREIBUNG |
|-|-|
| Aggressive | Ermöglicht eine schnelle Fehlererkennung und Wiederherstellung nach schwerwiegenden Fehlern, wodurch höchste Verfügbarkeitsgrade erzielt werden. Sowohl der Clusterdienst als auch SQL Server sind weniger tolerant gegenüber vorübergehenden Ausfällen und können in bestimmten Situationen vorzeitig ein Failover für Ressourcen einleiten, wenn es zu vorübergehenden Ausfällen kommt. Sobald ein Fehler erkannt wird, können die darauf folgenden Korrekturmaßnahmen zusätzliche Zeit in Anspruch nehmen. |
| Gelockert | Bietet eine tolerantere Fehlererkennung mit größerer Toleranz gegenüber kurzen, vorübergehenden Netzwerkproblemen. Vermeidet vorübergehende Fehler, führt aber auch zum Risiko, dass die Erkennung eines tatsächlichen Fehlers verzögert wird. |

Aggressive Einstellungen in einer Clusterumgebung in der Cloud können zu vorzeitigen Fehlern und längeren Ausfällen führen. Daher wird auf Azure-VMs eine gelockerte Überwachungsstrategie für Failovercluster empfohlen. Weitere Informationen zum Anpassen von Schwellenwerteinstellungen finden Sie unter [Bewährte Methoden für Cluster](hadr-cluster-best-practices.md#relaxed-monitoring). 

## <a name="cluster-heartbeat"></a>Clustertakt

Die primären Einstellungen, die sich auf die Takt- und Integritätserkennung des Clusters zwischen Knoten auswirken:

| Einstellung | BESCHREIBUNG |
|-|-|
| Verzögern | Bestimmt die Häufigkeit, mit der Clustertakte zwischen Knoten gesendet werden. Die Verzögerung ist die Anzahl der Sekunden vor Senden des nächsten Takts. Innerhalb desselben Clusters können unterschiedliche Verzögerungseinstellungen zwischen Knoten im gleichen Subnetz und zwischen Knoten in unterschiedlichen Subnetzen konfiguriert werden. |
| Schwellenwert | Der Schwellenwert ist die Anzahl der Takte, die verpasst werden dürfen, bevor der Cluster Wiederherstellungsmaßnahmen ergreift. Innerhalb desselben Clusters können unterschiedliche Schwellenwerteinstellungen zwischen Knoten im gleichen Subnetz und zwischen Knoten in unterschiedlichen Subnetzen konfiguriert werden. |

Die Standardwerte für diese Einstellungen sind für Cloudumgebungen möglicherweise zu niedrig und können aufgrund vorübergehender Netzwerkprobleme zu unnötigen Fehlern führen. Wählen Sie für mehr Toleranz für Failovercluster in Azure-VMs gelockerte Schwellenwerteinstellungen. Weitere Informationen finden Sie unter [Bewährte Methoden für Cluster](hadr-cluster-best-practices.md#heartbeat-and-threshold). 

## <a name="quorum"></a>Quorum

Ein Cluster mit zwei Knoten funktioniert zwar ohne eine [Quorumressource](/windows-server/storage/storage-spaces/understand-quorum), Kunden müssen jedoch unbedingt eine Quorumressource verwenden, um Unterstützung für die Produktion zu erhalten. Ein Cluster ohne Quorumressource besteht die Clustervalidierung nicht. 

Technisch gesehen kann ein Cluster mit drei Knoten den Verlust eines einzelnen Knotens (bei zwei verbleibenden Knoten) ohne Quorumressource überstehen. Aber wenn der Cluster nur noch aus zwei Knoten besteht, dann besteht das Risiko, dass die gruppierten Ressourcen offline gehen, um ein Split-Brain-Szenario zu verhindern, wenn ein Knoten ausfällt oder ein Kommunikationsfehler zwischen den Knoten auftritt. Durch Konfigurieren einer Quorumressource können die Clusterressourcen online bleiben, auch wenn nur ein Knoten online ist.

Der Datenträgerzeuge ist die resilienteste Quorumoption. Um jedoch einen Datenträgerzeugen für SQL Server auf einer Azure-VM zu verwenden, benötigen Sie einen freigegebenen Azure-Datenträger, der der Hochverfügbarkeitslösung einige Einschränkungen auferlegt. Verwenden Sie daher einen Datenträgerzeugen, wenn Sie Ihre Failoverclusterinstanz mit freigegebenen Azure-Datenträgern konfigurieren. Nutzen Sie andernfalls nach Möglichkeit einen Cloudzeugen. 

In der folgenden Tabelle sind die für SQL Server auf Azure-VMs verfügbaren Quorumoptionen aufgeführt: 

|  |[Cloudzeuge](/windows-server/failover-clustering/deploy-cloud-witness) |[Datenträgerzeuge](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum) |[Dateifreigabenzeuge](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |
|---------|---------|---------|---------|
|**Unterstütztes Betriebssystem**| Windows Server 2016+ |All | Alle|
| **Beschreibung** | Ein Cloudzeuge ist ein Quorumzeugentyp für Failovercluster, der Microsoft Azure zum Bereitstellen einer Stimme für das Clusterquorum verwendet. Die Standardgröße beträgt ungefähr 1 MB, und nur der Zeitstempel ist enthalten. Ein Cloudzeuge eignet sich ideal für Bereitstellungen an mehreren Standorten, in mehreren Zonen und in mehreren Regionen. Nutzen Sie nach Möglichkeit einen Cloudzeugen, es sei denn, Sie haben eine Failoverclusterlösung mit gemeinsam genutztem Speicher. | Ein Datenträgerzeuge ist ein kleiner Clusterdatenträger in der Gruppe „Verfügbarer Clusterspeicher“. Dieser Datenträger ist hochverfügbar und unterstützt ein Failover zwischen Knoten. Er enthält eine Kopie der Clusterdatenbank mit einer Standardgröße von unter 1 GB. Der Datenträgerzeuge ist die bevorzugte Quorumoption für Cluster, die freigegebene Azure-Datenträger verwenden (oder eine Lösung für freigegebene Datenträger wie freigegebenes SCSI, iSCSI oder Fibre Channel-SAN).  Ein freigegebenes Clustervolume kann nicht als Datenträgerzeuge verwendet werden. Konfigurieren Sie einen freigegebenen Azure-Datenträger als Datenträgerzeuge.  | Ein Dateifreigabezeuge ist eine SMB-Dateifreigabe, die in der Regel auf einem Dateiserver mit Windows Server konfiguriert ist. Dieser Zeuge verwaltet Clusteringinformationen in einer Datei „witness.log“, speichert aber keine Kopie der Clusterdatenbank. In Azure können Sie im selben virtuellen Netzwerk eine Dateifreigabe auf einem separaten virtuellen Computer konfigurieren. Nutzen Sie einen Dateifreigabenzeugen, wenn ein Datenträger- oder Cloudzeuge in Ihrer Umgebung nicht verfügbar ist. | 

Informationen zu den ersten Schritte finden Sie unter [Konfigurieren des Clusterquorums](hadr-cluster-quorum-configure-how-to.md). 


## <a name="virtual-network-name-vnn"></a>Name des virtuellen Netzwerks (Virtual Network Name, VNN)

Um die Verbindung zu Ihrem Verfügbarkeitsgruppen-Listener oder Ihrer Failover-Cluster-Instanz wie vor Ort zu gestalten, stellen Sie Ihre SQL Server-VMs in mehreren Subnetzen innerhalb desselben virtuellen Netzwerks bereit. Mit mehreren Subnetzen entfällt die zusätzliche Abhängigkeit von einem Azure Load Balancer, der den Datenverkehr zu Ihrer HADR-Lösung leitet.  Weitere Informationen finden Sie unter [Multi-Subnetz AG](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md), und [Multi-Subnetz FCI](failover-cluster-instance-prepare-vm.md#subnets). 

In einer herkömmlichen lokalen Umgebung verwenden Clusterressourcen wie Failoverclusterinstanzen oder Always On-Verfügbarkeitsgruppen den Namen des virtuellen Netzwerks, um Datenverkehr an das entsprechende Ziel weiterzuleiten – entweder an die Failoverclusterinstanz oder den Listener der Always On-Verfügbarkeitsgruppe. Der Name des virtuellen Netzwerks bindet die IP-Adresse in DNS, und Clients können entweder den virtuellen Namen oder die IP-Adresse verwenden, um eine Verbindung mit ihrem Hochverfügbarkeitsziel herzustellen, und zwar unabhängig davon, welcher Knoten die Ressource derzeit besitzt. Der Name des virtuellen Netzwerks ist ein Netzwerkname und eine Adresse, die vom Cluster verwaltet werden. Der Clusterdienst verschiebt die Netzwerkadresse während eines Failoverereignisses von Knoten zu Knoten. Bei einem Ausfall wird die Adresse auf dem ursprünglichen primären Replikat offline geschaltet und auf dem neuen primären Replikat online geschaltet.

Bei virtuellen Azure-Maschinen in einem einzelnen Subnetz ist eine zusätzliche Komponente erforderlich, um den Datenverkehr vom Client zum virtuellen Netzwerknamen der geclusterten Ressource (Failover-Cluster-Instanz oder Listener einer Verfügbarkeitsgruppe) zu leiten. In Azure enthält ein Lastenausgleich die IP-Adresse für den Namen des virtuellen Netzwerks, den die gruppierten SQL Server-Ressourcen verwenden. Sie ist erforderlich, um Datenverkehr an das entsprechende Hochverfügbarkeitsziel weiterzuleiten. Der Lastenausgleich erkennt auch Fehler bei den Netzwerkkomponenten und verschiebt die Adresse auf einen neuen Host. 

Der Lastenausgleich verteilt eingehende Flows, die am Front-End eintreffen, und leitet diesen Datenverkehr dann an die durch den Back-End-Pool definierten Instanzen weiter. Sie konfigurieren den Datenverkehrsfluss mithilfe von Lastenausgleichsregeln und Integritätstests. Mit einer SQL Server-Failoverclusterinstanz sind die Back-End-Poolinstanzen die virtuellen Azure-Computer, auf denen SQL Server ausgeführt wird, und bei Verfügbarkeitsgruppen ist der Back-End-Pool der Listener. Bei Verwendung des Lastenausgleichs kommt es zu einer geringfügigen Failoververzögerung, da der Integritätstest standardmäßig alle 10 Sekunden Aktivitätsprüfungen durchführt. 

Informieren Sie sich zunächst darüber, wie Sie Azure Load Balancer für eine [Failoverclusterinstanz](failover-cluster-instance-vnn-azure-load-balancer-configure.md) oder [Verfügbarkeitsgruppe](availability-group-vnn-azure-load-balancer-configure.md) konfigurieren. 

**Unterstütztes Betriebssystem**: All   
**Unterstützte SQL-Version**: All   
**Unterstützte HADR-Lösung**: Failoverclusterinstanz und Verfügbarkeitsgruppe   

Die Konfiguration des Namen des virtuellen Netzwerks kann umständlich sein, ist eine zusätzliche Fehlerquelle und kann zu einer Verzögerung bei der Fehlererkennung führen. Zudem sind mit der Verwaltung der zusätzlichen Ressource Mehraufwand und Kosten verbunden. Um einige dieser Einschränkungen zu beseitigen, hat SQL Server die Unterstützung für die Funktion "Verteilter Netzwerkname" eingeführt. 

## <a name="distributed-network-name-dnn"></a>Name des verteilten Netzwerks (Distributed Network Name, DNN)

Um die Verbindung zu Ihrem Verfügbarkeitsgruppen-Listener oder Ihrer Failover-Cluster-Instanz wie vor Ort zu gestalten, stellen Sie Ihre SQL Server-VMs in mehreren Subnetzen innerhalb desselben virtuellen Netzwerks bereit. Mit mehreren Subnetzen entfällt die zusätzliche Abhängigkeit von einem DNN, das den Datenverkehr zu Ihrer HADR-Lösung leitet. Weitere Informationen finden Sie unter [Multi-Subnetz AG](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md), und [Multi-Subnetz FCI](failover-cluster-instance-prepare-vm.md#subnets). 

Für SQL Server-VMs, die in einem einzelnen Subnetz bereitgestellt werden, bietet die Funktion für verteilte Netzwerknamen eine alternative Möglichkeit für SQL Server-Clients, sich mit der SQL Server-Failover-Clusterinstanz oder dem Verfügbarkeitsgruppen-Listener zu verbinden, ohne einen Load Balancer zu verwenden. Die DNN-Funktion ist ab [SQL Server 2016 SP3](https://support.microsoft.com/topic/kb5003279-sql-server-2016-service-pack-3-release-information-46ab9543-5cf9-464d-bd63-796279591c31), [SQL Server 2017 CU25](https://support.microsoft.com/topic/kb5003830-cumulative-update-25-for-sql-server-2017-357b80dc-43b5-447c-b544-7503eee189e9), [SQL Server 2019 CU8](https://support.microsoft.com/topic/cumulative-update-8-for-sql-server-2019-ed7f79d9-a3f0-a5c2-0bef-d0b7961d2d72), auf Windows Server 2016 und höher verfügbar.

Wenn eine DNN-Ressource erstellt wird, bindet der Cluster den DNS-Namen an die IP-Adressen aller Knoten im Cluster. Der Client versucht, eine Verbindung mit den einzelnen IP-Adressen in dieser Liste herzustellen, um zu ermitteln, mit welcher Ressource eine Verbindung hergestellt werden soll. Sie können diesen Prozess beschleunigen, indem Sie `MultiSubnetFailover=True` in der Verbindungszeichenfolge angeben. Diese Einstellung weist den Anbieter an, alle IP-Adressen parallel zu testen, sodass der Client sofort eine Verbindung mit der FCI oder dem Listener herstellen kann. 

Wenn möglich, wird der Name eines verteiltes Netzwerks über einen Lastenausgleich empfohlen: 
- Die End-to-End-Lösung ist stabiler, da Sie die Lastenausgleichsressource nicht mehr pflegen müssen. 
- Wenn Sie die Lastenausgleichstests weglassen, wird die Failoverdauer minimiert. 
- Der DNN vereinfacht die Bereitstellung und Verwaltung der Failoverclusterinstanz oder des Verfügbarkeitsgruppenlisteners mit SQL Server auf virtuellen Azure-Computern. 

Bei Verwendung des DNN funktionieren die meisten SQL Server-Features transparent mit FCI und Verfügbarkeitsgruppen. Es gibt jedoch bestimmte Features, die ggf. besondere Aufmerksamkeit erfordern. 

**Unterstütztes Betriebssystem**: Windows Server 2016 oder höher   
**Unterstützte SQL-Version**: SQL Server 2019 CU2 (FCI) und SQL Server 2019 CU8 (Verfügbarkeitsgruppe)   
**Unterstützte HADR-Lösung**: Failoverclusterinstanz und Verfügbarkeitsgruppe   

Informieren Sie sich zunächst darüber, wie Sie eine DNN-Ressource für eine [Failoverclusterinstanz](failover-cluster-instance-distributed-network-name-dnn-configure.md) oder [Verfügbarkeitsgruppe](availability-group-distributed-network-name-dnn-listener-configure.md) konfigurieren.

Es gibt weitere Überlegungen, wenn DNN mit anderen SQL Server-Features verwendet werden. Weitere Informationen finden Sie unter [Featureinteroperabilität mit SQL Server-FCI und DNN](failover-cluster-instance-dnn-interoperability.md) sowie unter [Featureinteroperabilität mit Verfügbarkeitsgruppe und DNN-Listener](availability-group-dnn-interoperability.md). 

## <a name="recovery-actions"></a>Wiederherstellungsaktionen

Der Clusterdienst führt Korrekturmaßnahmen aus, wenn ein Fehler erkannt wird. Dies kann ein Neustart der Ressource auf einem vorhandenen Knoten oder ein Failover der Ressource auf einen anderen Knoten sein. Sobald Korrekturmaßnahmen eingeleitet wurden, nehmen sie einige Zeit in Anspruch. 

Beispielsweise wird eine neu gestartete Verfügbarkeitsgruppe gemäß der folgenden Sequenz online geschaltet: 

1. IP-Adresse des Listeners wird online geschaltet
1. Name des Listenernetzwerks wird online geschaltet
1. Verfügbarkeitsgruppe wird online geschaltet
1. Einzelne Datenbanken durchlaufen eine Wiederherstellung, die abhängig von verschiedenen Faktoren, wie z. B. Länge des Wiederholungsprotokolls, einige Zeit dauern kann. Verbindungen werden vom Listener erst geroutet, nachdem die Datenbank vollständig wiederhergestellt wurde. Weitere Informationen finden Sie unter [Einschätzen der Failoverzeit (RTO)](/sql/database-engine/availability-groups/windows/monitor-performance-for-always-on-availability-groups). 

Da die Wiederherstellung einige Zeit in Anspruch nehmen kann, kann eine aggressive Überwachung zur Erkennung eines Fehlers in 20 Sekunden zu einem minutenlangen Ausfall führen, wenn ein vorübergehendes Ereignis auftritt (z. B. eine den Arbeitsspeicher erhaltende [Wartung von Azure-VMs](#azure-platform-maintenance)). Wenn Sie die Überwachung auf einen gelockerteren Wert von 40 Sekunden festlegen, kann eine längere Dienstunterbrechung vermieden werden. 

Weitere Informationen zum Anpassen von Schwellenwerteinstellungen finden Sie unter [Bewährte Methoden für Cluster](hadr-cluster-best-practices.md). 


## <a name="node-location"></a>Knotenposition

Die Knoten in einem Windows-Cluster auf virtuellen Computern in Azure können sich physisch getrennt innerhalb derselben Azure-Region oder in verschiedenen Regionen befinden. Durch die Entfernung kann es zu Netzwerklatenz kommen, ähnlich wie bei Clusterknoten, die auf verschiedene Standorte in Ihren eigenen Anlagen verteilt sind. In Cloudumgebungen besteht der Unterschied darin, dass Sie innerhalb einer Region den Abstand zwischen Knoten möglicherweise nicht kennen.  Darüber hinaus können einige andere Faktoren wie physische und virtuelle Komponenten, die Anzahl von Hops usw. auch zu einer höheren Latenz beitragen. Wenn die Latenz zwischen den Knoten ein Problem ist, sollten Sie die Knoten des Clusters einer [Näherungsplatzierungsgruppe](../../../virtual-machines/co-location.md) hinzufügen, um Netzwerknähe zu gewährleisten.

## <a name="resource-limits"></a>Ressourceneinschränkungen

Wenn Sie eine Azure-VM konfigurieren, bestimmen Sie die Grenzwerte für CPU-, Arbeitsspeicher- und E/A-Ressourcen. Workloads, die mehr Ressourcen benötigen, als die erworbene Azure-VM zu bieten hat, oder Datenträgergrenzwerte können zu Leistungsproblemen bei VMs führen. Leistungsbeeinträchtigungen können zu einer fehlgeschlagenen Integritätsprüfung für den Clusterdienst oder das SQL Server-Feature für Hochverfügbarkeit führen. Ressourcenengpässe können bewirken, dass der Knoten oder die Ressource im Cluster oder in SQL Server als ausgefallen angezeigt wird. 

Intensive SQL-E/A-Vorgänge oder Wartungsvorgänge wie Sicherungen, Index- oder Statistikwartung können dazu führen, dass die VM oder der Datenträger *IOPS*- oder *MBit/s*-bezogene Durchsatzgrenzwerte erreicht, was dazu führen kann, dass SQL Server nicht auf eine *IsAlive/LooksAlive*-Überprüfung reagiert. 

Wenn bei Ihrer SQL Server-Instanz unerwartete Failover auftreten, überprüfen Sie, ob Sie alle [bewährten Methoden für die Leistung](performance-guidelines-best-practices-checklist.md) befolgen, und überwachen Sie den Server auf Begrenzungen auf Datenträger- oder VM-Ebene. 

## <a name="azure-platform-maintenance"></a>Wartung der Azure-Plattform

Wie alle anderen Clouddienste aktualisiert Azure seine Plattform regelmäßig, um Zuverlässigkeit, Leistung und Sicherheit der Hostinfrastruktur für virtuelle Computer zu verbessern. Der Zweck dieser Updates reicht von Patches für Softwarekomponenten in der Hostumgebung über Upgrades für Netzwerkkomponenten bis hin zur Außerbetriebsetzung von Hardware.

Die meisten Plattformupdates wirken sich nicht auf Kunden-VMs aus. Ist es nicht möglich, ein Update ohne Auswirkungen durchzuführen, wählt Azure den Updatemechanismus aus, der die geringsten Auswirkungen auf die Kunden-VMs hat. Bei der Wartung mit den geringsten Auswirkungen wird der virtuelle Computer für weniger als 10 Sekunden angehalten. In bestimmten Fällen verwendet Azure Wartungsmechanismen, die den Arbeitsspeicherinhalt beibehalten. Diese Mechanismen halten den virtuellen Computer bis zu 30 Sekunden lang an und behalten den Inhalt des RAM-Speichers bei. Anschließend wird der Betrieb des virtuellen Computers fortgesetzt, und seine Uhr wird automatisch synchronisiert.

Wartung mit Speicherbeibehaltung funktioniert für mehr als 90% der virtuellen Azure-Computer. Ausnahmen sind lediglich die G-, M-, N- und H-Serie. Azure verwendet in zunehmendem Maß Technologien zur Livemigration und bessere Mechanismen zur Erhaltung des Arbeitsspeichers, um die Anhaltedauer zu verringern. Wenn der virtuelle Computer live zu einem anderen Host migriert wird, ist einige Minuten vor dem Anhalten der VM bei einigen empfindlichen Workloads wie SQL Server unter Umständen eine geringfügige Leistungsbeeinträchtigung feststellbar.

Ein Ressourcenengpass während der Plattformwartung kann bewirken, dass die Verfügbarkeitsgruppe oder Failoverclusterinstanz im Clusterdienst als ausgefallen angezeigt wird. Weitere Informationen finden Sie im Abschnitt [Ressourceneinschränkungen](#resource-limits) dieses Artikels. 

Bei einer aggressiven Clusterüberwachung kann ein längeres Anhalten der VM ein Failover auslösen. Ein Failover verursacht häufig mehr Ausfallzeiten als die Wartungspause. Daher wird empfohlen, eine gelockerte Überwachung zu wählen, um zu vermeiden, dass ein Failover ausgelöst wird, während der virtuelle Computer für die Wartung angehalten ist. Weitere Informationen zum Festlegen von Clusterschwellenwerten in Azure-VMs finden Sie unter [Bewährte Methoden für Cluster](hadr-cluster-best-practices.md).

## <a name="limitations"></a>Einschränkungen

Beachten Sie die folgenden Einschränkungen, wenn Sie mit FCIs oder Verfügbarkeitsgruppen und SQL Server auf Azure Virtual Machines arbeiten. 

### <a name="msdtc"></a>MSDTC 

Azure Virtual Machines unterstützt Microsoft Distributed Transaction Coordinator (MSDTC) unter Windows Server 2019 mit Speicher auf freigegebenen Clustervolumes (CSV) und mit [Azure Load Balancer Standard](../../../load-balancer/load-balancer-overview.md) oder auf SQL Server-VMs, die freigegebene Azure-Datenträger verwenden. 

In Azure Virtual Machines wird MSDTC unter Windows Server 2016 und früheren Versionen mit freigegebenen Clustervolumes aus den folgenden Gründen nicht unterstützt:

- Die MSDTC-Clusterressource kann nicht für die Verwendung von freigegebenem Speicher konfiguriert werden. Wenn Sie unter Windows Server 2016 eine MSDTC-Ressource erstellen, wird kein freigegebener Speicher für die Verwendung angezeigt, selbst wenn der Speicher verfügbar ist. Dieses Problem wurde in Windows Server 2019 behoben.
- Der einfache Lastenausgleich verarbeitet keine RPC-Ports.



## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit den Unterschieden bei der Verwendung eines Windows-Failoverclusters mit SQL Server auf Azure-VMs vertraut gemacht haben, erfahren Sie mehr über die Hochverfügbarkeitsfeatures [Verfügbarkeitsgruppen](availability-group-overview.md) oder [Failoverclusterinstanzen](failover-cluster-instance-overview.md). Wenn Sie die ersten Schritte unternehmen möchten, sollten Sie sich in den [bewährten Methoden](hadr-cluster-best-practices.md) die Konfigurationsempfehlungen ansehen. 
