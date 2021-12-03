---
title: Übersicht über SQL Server Always On-Verfügbarkeitsgruppen
description: Dieser Artikel bietet eine Einführung in SQL Server Always On-Verfügbarkeitsgruppen auf Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: rajeshsetlem
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/10/2021
ms.author: rsetlem
ms.custom: seo-lt-2019
ms.reviewer: mathoma
ms.openlocfilehash: 21aef2227768d49da9a5eab5f4e772441c9f15b0
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158669"
---
# <a name="always-on-availability-group-on-sql-server-on-azure-vms"></a>Always On-Verfügbarkeitsgruppe für SQL Server auf Azure-VMs
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Dieser Artikel bietet eine Einführung in Always On-Verfügbarkeitsgruppen für SQL Server auf Azure Virtual Machines (VMs). 

## <a name="overview"></a>Übersicht

Always On-Verfügbarkeitsgruppen in Azure Virtual Machines ähneln [lokalen Always On-Verfügbarkeitsgruppen](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) und basieren auf dem zugrunde liegenden [Windows Server-Failovercluster](hadr-windows-server-failover-cluster-overview.md). Da die virtuellen Computer jedoch in Azure gehostet werden, gibt es auch einige zusätzliche Überlegungen, wie z. B. VM-Redundanz und das Routing von Datenverkehr im Azure-Netzwerk. 

Das folgende Diagramm veranschaulicht eine Verfügbarkeitsgruppe für SQL Server auf Azure-VMs:

![Verfügbarkeitsgruppe](./media/availability-group-overview/00-EndstateSampleNoELB.png)

> [!NOTE]
> Sie können Ihre Verfügbarkeitsgruppenlösung jetzt mithilfe von Azure Migrate per Lift & Shift zu SQL Server auf Azure-VMs migrieren. Weitere Informationen finden Sie unter [Migrieren von Verfügbarkeitsgruppen zu SQL Server auf Azure-VMs](../../migration-guides/virtual-machines/sql-server-availability-group-to-sql-on-azure-vm.md). 

## <a name="vm-redundancy"></a>VM-Redundanz 

Zur Verbesserung der Redundanz und Verfügbarkeit sollten sich die virtuellen SQL Server-Computer entweder in der gleichen [Verfügbarkeitsgruppe](../../../virtual-machines/availability-set-overview.md) oder in unterschiedlichen [Verfügbarkeitszonen](../../../availability-zones/az-overview.md) befinden.

Die Platzierung mehrerer virtueller Computer in der gleichen Verfügbarkeitsgruppe dient zum Schutz vor Ausfällen in einem Rechenzentrum infolge von Hardwarefehlern, da virtuelle Computer in einer Verfügbarkeitsgruppe nicht die gleichen Ressourcen verwenden, sowie vor Ausfällen infolge von Updates, da virtuelle Computer innerhalb einer Verfügbarkeitsgruppe nicht gleichzeitig aktualisiert werden. 

Verfügbarkeitszonen schützen vor dem Ausfall eines gesamten Rechenzentrums. Die einzelnen Zonen stellen jeweils eine Gruppe von Rechenzentren innerhalb einer Region dar.  Durch die Platzierung von Ressourcen in unterschiedlichen Verfügbarkeitszonen wird sichergestellt, dass durch einen Ausfall auf Rechenzentrumsebene nicht alle Ihre virtuellen Computer offline geschaltet werden.

Bei der VM-Erstellung müssen Sie sich entscheiden, ob Sie Verfügbarkeitsgruppen oder Verfügbarkeitszonen konfigurieren möchten.  Ein virtueller Azure-Computer kann nicht mit beidem konfiguriert werden. 

Obwohl Verfügbarkeitszonen gegenüber Verfügbarkeitsgruppen eine höhere Verfügbarkeit bieten können (99,99 % im Vergleich zu 99,95 %), sollte auch die Leistung berücksichtigt werden. Virtuelle Computer innerhalb einer Verfügbarkeitsgruppe können in einer [Näherungsplatzierungsgruppe](../../../virtual-machines/co-location.md) platziert werden, um sicherzustellen, dass sie nah beieinander liegen, wodurch die Netzwerklatenz zwischen ihnen minimiert wird. Virtuelle Computer in unterschiedlichen Verfügbarkeitszonen verfügen über eine höhere Netzwerklatenz zwischen ihnen, wodurch sich die Zeit erhöhen kann, die zum Synchronisieren von Daten zwischen den primären und sekundären Replikaten benötigt wird. Dies kann zu Verzögerungen auf dem primären Replikat führen und die Wahrscheinlichkeit eines Datenverlusts im Falle eines ungeplanten Failovers erhöhen. Es ist wichtig, die vorgeschlagene Lösung unter Last zu testen und sicherzustellen, dass sie die SLAs für Leistung und Verfügbarkeit erfüllt.

## <a name="connectivity"></a>Konnektivität

Um die Verbindung mit Ihrem Verfügbarkeitsgruppenlistener wie in der lokalen Umgebung herzustellen, stellen Sie Ihre SQL Server-VMs in [mehreren Subnetzen](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md) innerhalb desselben virtuellen Netzwerks bereit. Bei der Verwendung mehrerer Subnetze entfällt die Notwendigkeit einer zusätzlichen Abhängigkeit von einer Azure Load Balancer-Instanz oder von einem verteilten Netzwerknamen (Distributed Network Name, DNN), um Ihren Datenverkehr an Ihren Listener weiterzuleiten. 

Wenn Sie Ihre SQL Server-VMs in einem einzelnen Subnetz bereitstellen, können Sie einen virtuellen Netzwerknamen (VNN) und eine Azure Load Balancer-Instanz oder einen verteilten Netzwerknamen (Distributed Network Name, DNN) konfigurieren, um Datenverkehr an Ihren Verfügbarkeitsgruppenlistener weiterzuleiten. [Überprüfen Sie die Unterschiede zwischen den beiden](hadr-windows-server-failover-cluster-overview.md), und stellen Sie dann entweder einen [Namen eines verteilten Netzwerks (Distributed Network Name, DNN)](availability-group-distributed-network-name-dnn-listener-configure.md) oder einen [Namen eines virtuellen Netzwerks (Virtual Network Name, VNN)](availability-group-vnn-azure-load-balancer-configure.md) für Ihre Verfügbarkeitsgruppe bereit. 

Bei Verwendung des DNN funktionieren die meisten SQL Server-Features transparent mit Verfügbarkeitsgruppen. Es gibt jedoch bestimmte Features, die ggf. besondere Aufmerksamkeit erfordern. Weitere Informationen finden Sie unter [Interoperabilität zwischen Verfügbarkeitsgruppe und DNN](availability-group-dnn-interoperability.md). 

Darüber hinaus gibt es einige Verhaltensunterschiede zwischen der Funktionalität des VNN-Listeners und des DNN-Listeners, die zu beachten sind: 

- **Failoverzeit**: Die Failoverzeit ist bei Verwendung eines DNN-Listeners schneller, da nicht gewartet werden muss, bis der Netzwerklastenausgleich das Fehlerereignis erkennt und das Routing ändert. 
- **Vorhandene Verbindungen**: Verbindungen, die mit einer *bestimmten Datenbank* innerhalb einer Verfügbarkeitsgruppe hergestellt werden, für die ein Failover ausgeführt wird, werden geschlossen, aber andere Verbindungen mit dem primären Replikat bleiben geöffnet, da der DNN während des Failoverprozesses online bleibt. Dies ist anders als in einer herkömmlichen VNN-Umgebung, in der alle Verbindungen mit dem primären Replikat normalerweise geschlossen werden, wenn für die Verfügbarkeitsgruppe ein Failover ausgeführt wird, der Listener offline geht und das primäre Replikat in die sekundäre Rolle übergeht. Wenn Sie einen DNN-Listener verwenden, müssen Sie möglicherweise Anwendungsverbindungszeichenfolgen anpassen, um sicherzustellen, dass Verbindungen beim Failover an das neue primäre Replikat umgeleitet werden.
- **Offene Transaktionen:** Offene Transaktionen für eine Datenbank in einer Verfügbarkeitsgruppe, für die ein Failover ausgeführt wird, werden geschlossen und es wird ein Rollback durchgeführt. Zudem müssen Sie die Verbindung *manuell* wiederherstellen. Schließen Sie z. B. in SQL Server Management Studio das Abfragefenster, und öffnen Sie ein neues. 

Zum Einrichten eines VNN-Listeners in Azure ist ein Lastenausgleich erforderlich. Es gibt zwei Hauptoptionen für einen Lastenausgleich in Azure: extern (öffentlich) oder intern. Der externe (öffentliche) Lastenausgleich verfügt über Internetzugriff und ist einer öffentlichen virtuellen IP-Adresse zugeordnet, auf die über das Internet zugegriffen werden kann. Ein interner Lastenausgleich unterstützt nur Clients innerhalb desselben virtuellen Netzwerks. Für beide Lastenausgleichstypen müssen Sie [Direct Server Return](../../../load-balancer/load-balancer-multivip-overview.md#rule-type-2-backend-port-reuse-by-using-floating-ip) aktivieren. 

Sie können weiterhin separate Verbindungen mit den einzelnen Verfügbarkeitsreplikaten herstellen, indem Sie eine direkte Verbindung mit der Serverinstanz herstellen. Da Verfügbarkeitsgruppen abwärtskompatibel mit Clients für die Datenbankspiegelung sind, können Sie außerdem Verbindungen mit Verfügbarkeitsreplikaten wie Datenbankspiegelungspartnern herstellen, sofern die Replikate ähnlich wie die Datenbankspiegelung konfiguriert sind:

* Es gibt ein primäres Replikat und ein sekundäres Replikat.
* Das sekundäre Replikat ist als nicht lesbar konfiguriert (Option **Lesbare sekundäre Rolle** ist auf **Nein** festgelegt).

Im Anschluss sehen Sie ein Beispiel für eine Clientverbindungszeichenfolge für eine einer Datenbankspiegelung ähnlichen Konfiguration mithilfe von ADO.NET oder SQL Server Native Client:

```console
Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;
```

Weitere Informationen zur Clientkonnektivität finden Sie unter:

* [Verwenden von Schlüsselwörtern für Verbindungszeichenfolgen mit SQL Server Native Client](/sql/relational-databases/native-client/applications/using-connection-string-keywords-with-sql-server-native-client)
* [Verbinden von Clients mit einer Datenbank-Spiegelungssitzung (SQL Server)](/sql/database-engine/database-mirroring/connect-clients-to-a-database-mirroring-session-sql-server)
* [Connecting to Availability Group Listener in Hybrid IT (in englischer Sprache)](/archive/blogs/sqlalwayson/connecting-to-availability-group-listener-in-hybrid-it)
* [Verfügbarkeitsgruppenlistener, Clientkonnektivität und Anwendungsfailover (SQL Server)](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover)
* [Verwenden von Verbindungszeichenfolgen für die Datenbankspiegelung mit Verfügbarkeitsgruppen](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover)

## <a name="lease-mechanism"></a>Leasemechanismus 

Bei SQL Server bestimmt die Ressourcen-DLL der Verfügbarkeitsgruppe den Zustand der Gruppe auf Grundlage des jeweiligen Leasemechanismus und der Always On-Integritätserkennung. Die Ressourcen-DLL der Verfügbarkeitsgruppe stellt die Ressourcenintegrität über den Vorgang *IsAlive* bereit. Der Ressourcenmonitor ruft „IsAlive“ im Heartbeatintervall des Clusters ab, das über die clusterweiten Werte **CrossSubnetDelay** und **SameSubnetDelay** festgelegt wird. Auf einem primären Knoten initiiert der Clusterdienst immer dann ein Failover, wenn der IsAlive-Aufruf an die Ressourcen-DLL zurückgibt, dass die Verfügbarkeitsgruppe nicht fehlerfrei ist.

Die Ressourcen-DLL der Verfügbarkeitsgruppe überwacht den Status interner SQL Server-Komponenten. „Sp_server_diagnostics“ meldet die Integrität dieser Komponenten in einem Intervall an SQL Server, das von **HealthCheckTimeout** gesteuert wird.

Im Gegensatz zu anderen Failovermechanismen spielt die SQL Server-Instanz beim Leasemechanismus eine aktive Rolle. Der Leasemechanismus wird als *LooksAlive*-Validierung zwischen dem Clusterressourcenhost und dem SQL Server-Prozess verwendet. Der Mechanismus dient der Sicherstellung, dass die beiden Seiten (Cluster- und SQL Server-Dienst) in häufigem Kontakt stehen, den Zustand des jeweils anderen überprüfen und schließlich ein Split-Brain-Szenario verhindern.

Beim Konfigurieren einer Verfügbarkeitsgruppe in Azure-VMs müssen diese Schwellenwerte häufig anders konfiguriert werden als in einer lokalen Umgebung. Informationen zum Konfigurieren von Schwellenwerteinstellungen gemäß bewährten Methoden für Azure-VMs finden Sie unter [Bewährte Methoden für Cluster](hadr-cluster-best-practices.md). 


## <a name="network-configuration"></a>Netzwerkkonfiguration  

Stellen Sie Ihre SQL Server-VMs nach Möglichkeit in mehreren Subnetzen bereit, um die Abhängigkeit von einer Azure Load Balancer-Instanz oder von einem verteilten Netzwerknamen (Distributed Network Name, DNN) zum Routen von Datenverkehr an Ihren Verfügbarkeitsgruppenlistener zu vermeiden. 

In einem Azure-VM-Failovercluster wird ein einzelner Netzwerkadapter pro Server (Clusterknoten) empfohlen. Azure-Netzwerktechnologie bietet physische Redundanz, die zusätzliche Netzwerkadapter in einem Azure-VM-Failovercluster überflüssig macht. Obwohl im Clusterprüfbericht die Warnung ausgegeben wird, dass die Knoten nur in einem einzigen Netzwerk erreichbar sind, kann diese Warnung für Azure-VM-Failovercluster einfach ignoriert werden.

## <a name="basic-availability-group"></a>Basis-Verfügbarkeitsgruppe

Da die Basis-Verfügbarkeitsgruppe nicht mehr als ein sekundäres Replikat zulässt und kein Lesezugriff auf das sekundäre Replikat besteht, können Sie die Verbindungszeichenfolgen für die Datenbankspiegelung für Basis-Verfügbarkeitsgruppen verwenden. Durch die Verwendung der Verbindungszeichenfolge sind keine Listener erforderlich. Das Entfernen der Listenerabhängigkeit ist für Verfügbarkeitsgruppen auf Azure-VMs hilfreich, da es die Notwendigkeit eines Lastenausgleichs oder das Hinzufügen zusätzlicher IP-Adressen zum Lastenausgleich eliminiert, wenn Sie über mehrere Listener für zusätzliche Datenbanken verfügen. 

Wenn Sie z. B. explizit über TCP/IP eine Verbindung mit der Verfügbarkeitsgruppendatenbank „AdventureWorks“ auf „Replica_A“ oder „Replica_B“ einer Basis-Verfügbarkeitsgruppe (oder einer Verfügbarkeitsdatenbank, die nur über ein sekundäres Replikat verfügt und der Lesezugriff im sekundären Replikat nicht zulässig ist) herstellen möchten, kann eine Clientanwendung die folgende Verbindungszeichenfolge für die Datenbankspiegelung angeben, um erfolgreich eine Verbindung mit der Verfügbarkeitsgruppe herzustellen.

`Server=Replica_A; Failover_Partner=Replica_B; Database=AdventureWorks; Network=dbmssocn`


## <a name="deployment-options"></a>Bereitstellungsoptionen

Es gibt mehrere Optionen für die Bereitstellung einer Verfügbarkeitsgruppe für SQL Server auf Azure-VMS. Dabei verfügen einige über einen höheren Automatisierungsgrad als andere. 

Die folgende Tabelle bietet einen Vergleich der verfügbaren Optionen:

| | [Azure-Portal](availability-group-azure-portal-configure.md), | [Azure CLI/PowerShell](./availability-group-az-commandline-configure.md) | [Schnellstartvorlagen](availability-group-quickstart-template-configure.md) | [Manuell (einzelnes Subnetz)](availability-group-manually-configure-prerequisites-tutorial-single-subnet.md) | [Manuell (mehrere Subnetze)](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md)
|---------|---------|---------|---------|---------|
|**SQL Server-Version** |2016 + |2016 +|2016 +|2012 +|2012 +| 
|**SQL Server-Edition** |Enterprise |Enterprise |Enterprise |Enterprise, Standard|Enterprise, Standard|
|**Windows Server-Version**| 2016 + | 2016 + | 2016 + | All| All|
|**Der Cluster wird für Sie erstellt.**|Ja|Ja | Ja |Nein| Nein| 
|**Die Verfügbarkeitsgruppe wird für Sie erstellt.** |Ja |Nein|Nein|Nein| Nein| 
|**Der Listener und Lastenausgleich werden unabhängig voneinander erstellt.** |Nein|Nein|Nein|Ja|–|
|**Kann mit dieser Methode ein DNN-Listener erstellt werden?**|Nein|Nein|Nein|Ja|–|
|**WSFC-Quorumkonfiguration**|Cloudzeuge|Cloudzeuge|Cloudzeuge|All|All|
|**DR mit mehreren Regionen** |Nein|Nein|Nein|Ja|Ja|
|**Unterstützung mehrerer Subnetze** |Nein|Nein|Nein|–|Ja|
|**Unterstützung für ein vorhandenes AD**|Ja|Ja|Ja|Ja|Ja|
|**DR mit mehreren Zonen in derselben Region**|Ja|Ja|Ja|Ja|Ja|
|**Verteilte AG ohne AD**|Nein|Nein|Nein|Ja| Ja| 
|**Verteilte AG ohne Cluster** |Nein|Nein|Nein|Ja|Ja|
|**Lastenausgleich oder DNN erforderlich**| Ja | Ja | Ja | Ja | Nein|

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über die [Best Practices für HADR](hadr-cluster-best-practices.md), und beginnen Sie dann mit der Bereitstellung Ihrer Verfügbarkeitsgruppe über das [Azure-Portal](availability-group-azure-portal-configure.md), die [Azure CLI oder PowerShell](./availability-group-az-commandline-configure.md), [Schnellstartvorlagen](availability-group-quickstart-template-configure.md) oder die [manuelle](availability-group-manually-configure-prerequisites-tutorial-single-subnet.md) Bereitstellung.

Alternativ können Sie eine [Verfügbarkeitsgruppe ohne Cluster](availability-group-clusterless-workgroup-configure.md) oder eine Verfügbarkeitsgruppe [in mehreren Regionen](availability-group-manually-configure-multiple-regions.md) bereitstellen.

Weitere Informationen finden Sie unter:

- [Windows Server-Failovercluster mit SQL Server auf Azure-VMs](hadr-windows-server-failover-cluster-overview.md)
- [Übersicht über Always On-Verfügbarkeitsgruppen](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
