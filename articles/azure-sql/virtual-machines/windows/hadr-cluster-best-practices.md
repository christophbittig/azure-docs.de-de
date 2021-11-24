---
title: Bewährte Methoden der HADR-Konfiguration
description: Erfahren Sie mehr über die unterstützten Clusterkonfigurationen beim Konfigurieren von Hochverfügbarkeit und Notfallwiederherstellung (HADR) für SQL Server auf Azure Virtual Machines, z. B. unterstützte Quoren oder Verbindungsroutingoptionen.
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
ms.openlocfilehash: 8be0dc33d580314fd6b5e6472ed1cf41c5be2d4e
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158432"
---
# <a name="hadr-configuration-best-practices-sql-server-on-azure-vms"></a>Bewährte Methoden der HADR-Konfiguration (SQL Server auf Azure-VMs)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ein [Windows Server-Failovercluster](hadr-windows-server-failover-cluster-overview.md) wird für Hochverfügbarkeit und Notfallwiederherstellung (HADR) mit SQL Server auf Azure Virtual Machines (VMs) verwendet. 

Dieser Artikel enthält bewährte Methoden für die Clusterkonfiguration für [Failoverclusterinstanzen (FCIs)](failover-cluster-instance-overview.md) sowie [Verfügbarkeitsgruppen](availability-group-overview.md), wenn Sie diese mit SQL Server auf Azure-VMs verwenden. 

Weitere Informationen finden Sie in den anderen Artikeln dieser Reihe: [Prüfliste](performance-guidelines-best-practices-checklist.md), [VM-Größe](performance-guidelines-best-practices-vm-size.md), [Speicher](performance-guidelines-best-practices-storage.md), [Sicherheit](security-considerations-best-practices.md), [HADR-Konfiguration](hadr-cluster-best-practices.md) und [Baseline auswählen](performance-guidelines-best-practices-collect-baseline.md). 

## <a name="checklist"></a>Checkliste

In der folgenden Prüfliste finden Sie eine kurze Übersicht über die bewährten Methoden zu HADR, die im weiteren Verlauf des Artikels ausführlicher behandelt werden. 

Erwägen Sie für Ihren Windows-Cluster die folgenden bewährten Methoden: 

* Stellen Sie Ihre SQL Server-VMs nach Möglichkeit in mehreren Subnetzen bereit, um die Abhängigkeit von einem Azure Load Balancer oder einem verteilten Netzwerknamen (DNN) zur Weiterleitung des Datenverkehrs an Ihre HADR-Lösung zu vermeiden. 
* Ändern Sie den Cluster in weniger aggressive Parameter, um unerwartete Ausfälle durch vorübergehende Netzwerkfehler oder Wartung der Azure-Plattform zu vermeiden. Weitere Informationen finden Sie unter [Heartbeat- und Schwellenwerteinstellungen](#heartbeat-and-threshold). Verwenden Sie für Windows Server 2012 und höher die folgenden Werte: 
   - **SameSubnetDelay**: 1 Sekunde
   - **SameSubnetThreshold**: 40 Heartbeats
   - **CrossSubnetDelay**: 1 Sekunde
   - **CrossSubnetThreshold**: 40 Heartbeats
* Platzieren Sie Ihre VMs in einer Verfügbarkeitsgruppe oder verschiedenen Verfügbarkeitszonen.  Weitere Informationen finden Sie unter [VM-Verfügbarkeitseinstellungen](#vm-availability-settings). 
* Verwenden Sie eine einzelne NIC pro Clusterknoten. 
* Konfigurieren Sie die [Clusterquorumabstimmung](#quorum-voting) so, dass eine ungerade Anzahl von mindestens drei Stimmen verwendet wird. Weisen Sie keine Stimmen zu DR-Regionen zu. 
* Überwachen Sie [Ressourcengrenzwerte](#resource-limits) sorgfältig, um unerwartete Neustarts oder Failover aufgrund von Ressourceneinschränkungen zu vermeiden.
   - Stellen Sie sicher, dass Betriebssystem, Treiber und SQL Server den neuesten Builds entsprechen. 
   - Optimieren Sie die Leistung für SQL Server auf Azure-VMs. Weitere Informationen finden Sie in den anderen Abschnitten dieses Artikels. 
   - Reduzieren oder verteilen Sie die Workload, um Ressourcengrenzwerte zu vermeiden. 
   - Wechseln Sie zu einer VM oder einem Datenträger mit höheren Grenzwerten, um Einschränkungen zu vermeiden. 

Erwägen Sie für die SQL Server Verfügbarkeitsgruppe oder Failoverclusterinstanz die folgenden bewährten Methoden: 

* Wenn häufig unerwartete Fehler auftreten, befolgen Sie die leistungsbezogenen bewährten Methoden, die im restlichen Teil dieses Artikels beschrieben werden. 
* Falls sich die unerwarteten Failover durch Optimierung der Leistung der SQL Server-VMs nicht beheben lassen, erwägen Sie eine [Lockerung der Überwachung](#relaxed-monitoring) für die Verfügbarkeitsgruppe oder Failoverclusterinstanz. Dadurch wird jedoch möglicherweise nicht die zugrunde liegende Ursache des Problems behoben, und durch Verringerung der Fehlerwahrscheinlichkeit können Symptome maskiert werden. Möglicherweise müssen Sie die zugrunde liegende Ursache dennoch untersuchen und beheben. Verwenden Sie für Windows Server 2012 oder höher die folgenden empfohlenen Werte: 
   - **Leasetimeout**:Verwenden Sie diese Gleichung, um den maximalen Leasetimeoutwert zu berechnen:   
   `Lease timeout < (2 * SameSubnetThreshold * SameSubnetDelay)`.   
   Beginnen Sie mit 40 Sekunden. Wenn Sie die zuvor empfohlenen gelockerten `SameSubnetThreshold`- und `SameSubnetDelay`-Werte verwenden, darf der Leasetimeoutwert 80 Sekunden nicht überschreiten.   
   - **Maximale Fehler in einem angegebenen Zeitraum**: Legen Sie diesen Wert auf 6 fest. 
* Wenn Sie den virtuellen Netzwerknamen (VNN) und einen Azure Load Balancer verwenden, um sich mit Ihrer HADR-Lösung zu verbinden, geben Sie `MultiSubnetFailover = true` in der Verbindungszeichenfolge an, auch wenn sich Ihr Cluster nur über ein Subnetz erstreckt. 
   - Wenn der Client `MultiSubnetFailover = True` nicht unterstützt, müssen Sie möglicherweise `RegisterAllProvidersIP = 0` und `HostRecordTTL = 300` festlegen, um Clientanmeldeinformationen für kürzere Zeiträume zwischenzuspeichern. Dies kann jedoch zu zusätzlichen Abfragen an den DNS-Server führen. 
- Beim Herstellen einer Verbindung mit der HADR-Lösung mithilfe des Namens des verteilten Netzwerks (Distributed Network Name, DNN) ist Folgendes zu beachten:
   - Sie müssen einen Clienttreiber verwenden, der `MultiSubnetFailover = True` unterstützt, und dieser Parameter muss in der Verbindungszeichenfolge enthalten sein. 
   - Verwenden Sie einen eindeutigen DNN-Port in der Verbindungszeichenfolge, wenn Sie eine Verbindung mit dem DNN-Listener für eine Verfügbarkeitsgruppe herstellen. 
- Verwenden Sie eine Verbindungszeichenfolge für Datenbankspiegelung für eine Basic-Verfügbarkeitsgruppe, um die Notwendigkeit eines Lastenausgleichs oder DNN zu umgehen. 
- Überprüfen Sie die Sektorgröße Ihrer VHDs, bevor Sie Ihre Hochverfügbarkeitslösung bereitstellen, um falsch ausgerichtete E/A zu vermeiden. Weitere Informationen finden Sie unter [KB3009974](https://support.microsoft.com/topic/kb3009974-fix-slow-synchronization-when-disks-have-different-sector-sizes-for-primary-and-secondary-replica-log-files-in-sql-server-ag-and-logshipping-environments-ed181bf3-ce80-b6d0-f268-34135711043c). 


## <a name="vm-availability-settings"></a>VM-Verfügbarkeitseinstellungen

Berücksichtigen Sie die folgenden VM-Einstellungen für die beste Verfügbarkeit, um die Auswirkungen von Ausfallzeiten zu reduzieren: 

* Verwenden Sie für die niedrigste Latenz Näherungsplatzierungsgruppen zusammen mit beschleunigtem Netzwerkbetrieb.
* Platzieren Sie Clusterknoten virtueller Computer zum Schutz vor Ausfällen auf Rechenzentrumsebene oder in einer einzelnen Verfügbarkeitsgruppe in separaten Verfügbarkeitszonen, um Redundanz mit geringerer Latenz innerhalb desselben Rechenzentrums zu erreichen.
* Verwenden Sie für VMs in einer Verfügbarkeitsgruppe verwaltete Premium-Datenträger für Betriebssystem und Daten.
* Konfigurieren Sie die einzelnen Logikschichten in separate Verfügbarkeitsgruppen.

## <a name="quorum"></a>Quorum

Ein Cluster mit zwei Knoten funktioniert zwar ohne eine [Quorumressource](/windows-server/storage/storage-spaces/understand-quorum), Kunden müssen jedoch unbedingt eine Quorumressource verwenden, um Unterstützung für die Produktion zu erhalten. Ein Cluster ohne Quorumressource besteht die Clustervalidierung nicht. 

Technisch gesehen kann ein Cluster mit drei Knoten den Verlust eines einzelnen Knotens (bei zwei verbleibenden Knoten) ohne Quorumressource überstehen. Nachdem der Cluster jedoch auf zwei Knoten ausgefallen ist, besteht das Risiko, dass die Clusterressourcen im Fall eines Knotenausfalls oder eines Kommunikationsfehlers offline geschaltet werden, um ein Split-Brain-Szenario zu verhindern. Durch das Konfigurieren einer Quorumressource kann der Cluster mit nur einem Knoten online bleiben.

Der Datenträgerzeuge ist die resilienteste Quorumoption. Um jedoch einen Datenträgerzeugen für SQL Server auf einer Azure-VM zu verwenden, benötigen Sie einen freigegebenen Azure-Datenträger, der der Hochverfügbarkeitslösung einige Einschränkungen auferlegt. Verwenden Sie daher einen Datenträgerzeugen, wenn Sie Ihre Failoverclusterinstanz mit freigegebenen Azure-Datenträgern konfigurieren. Nutzen Sie andernfalls nach Möglichkeit einen Cloudzeugen. 

In der folgenden Tabelle sind die für SQL Server auf Azure-VMs verfügbaren Quorumoptionen aufgeführt: 

|  |[Cloudzeuge](/windows-server/failover-clustering/deploy-cloud-witness) |[Datenträgerzeuge](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum) |[Dateifreigabenzeuge](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |
|---------|---------|---------|---------|
|**Unterstütztes Betriebssystem**| Windows Server 2016+ |All | Alle|

- Der **Cloudzeuge** eignet sich ideal für Bereitstellungen an mehreren Standorten, in mehreren Zonen und mehreren Regionen. Nutzen Sie nach Möglichkeit einen Cloudzeugen, es sei denn, Sie haben eine Clusterlösung mit gemeinsam genutztem Speicher.
- Der **Datenträgerzeuge** ist die resilienteste Quorumoption und wird für Cluster bevorzugt, die freigegebene Azure-Datenträger verwenden (oder eine Lösung für freigegebene Datenträger wie freigegebenes SCSI, iSCSI oder Fibre Channel-SAN).  Ein freigegebenes Clustervolume kann nicht als Datenträgerzeuge verwendet werden. 
- Der **Dateifreigabenzeuge** eignet sich für den Zeitpunkt, zu dem der Datenträgerzeuge und der Cloudzeuge nicht verfügbar sind. 

Informationen zu den ersten Schritte finden Sie unter [Konfigurieren des Clusterquorums](hadr-cluster-quorum-configure-how-to.md). 

## <a name="quorum-voting"></a>Quorumabstimmung

Es ist möglich, die Quorumabstimmung (Stimme) eines Knotens zu ändern, der an einem Windows Server-Failovercluster beteiligt ist. 

Wenn Sie die Einstellungen für die Knotenabstimmung (Stimme) ändern, befolgen Sie diese Richtlinien: 

| Richtlinien zur Quorumabstimmung |
|-|
| Beginnen Sie damit, dass jeder Knoten standardmäßig keine Stimme besitzt. Jeder Knoten sollte nur über eine Stimme verfügen, wenn es dafür eine explizite Begründung gibt.|
| Aktivieren Sie Stimmen für Clusterknoten, die das primäre Replikat einer Verfügbarkeitsgruppe hosten, oder für die bevorzugten Besitzer einer Failoverclusterinstanz. |
| Aktivieren Sie Stimmen für Besitzer des automatischen Failovers. Jeder Knoten, der nach einem automatischen Failover zu einem Host eines primären Replikats oder einer FCI werden kann, sollte eine Stimme haben. | 
| Wenn eine Verfügbarkeitsgruppe über mehrere sekundäre Replikate verfügt, aktivieren Sie nur Stimmen für die Replikate, die über ein automatisches Failover verfügen. | 
| Deaktivieren Sie die Stimmen für Knoten, die sich an sekundären Standorten für die Notfallwiederherstellung befinden. Knoten an sekundären Standorten sollten nicht zur Entscheidung beitragen, einen Cluster offline zu schalten, wenn mit dem primären Standort alles in Ordnung ist. | 
| Sie verfügen über eine ungerade Anzahl von Stimmen mit mindestens drei Quorumstimmen. Fügen Sie bei Bedarf einen [Quorumzeugen](hadr-cluster-quorum-configure-how-to.md) für eine zusätzliche Stimme in einem Cluster mit zwei Knoten hinzu. | 
| Bewerten Sie die Stimmenzuweisungen nach einem Failover neu. Es ist nicht wünschenswert, ein Failover in eine Clusterkonfiguration auszuführen, die kein fehlerfreies Quorum unterstützt. |


## <a name="connectivity"></a>Verbindung

Um eine Verbindung zu Ihrem Verfügbarkeitsgruppen-Listener oder Ihrer Failover-Cluster-Instanz wie vor Ort herzustellen, stellen Sie Ihre SQL Server-VMs in mehreren Subnetzen innerhalb desselben virtuellen Netzwerks bereit. Mit mehreren Subnetzen entfällt die zusätzliche Abhängigkeit von einem Azure Load Balancer oder einem verteilten Netzwerknamen, um Ihren Datenverkehr an Ihren Listener zu leiten.  

Um Ihre HADR-Lösung zu vereinfachen, sollten Sie Ihre SQL Server-VMs nach Möglichkeit in mehreren Subnetzen einsetzen.  Weitere Informationen finden Sie unter [Multi-Subnetz AG](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md) und [Multi-Subnetz FCI](failover-cluster-instance-prepare-vm.md#subnets). 

Wenn sich Ihre SQL Server-VMs in einem einzigen Subnetz befinden, ist es möglich, entweder einen virtuellen Netzwerknamen (VNN) und einen Azure Load Balancer oder einen verteilten Netzwerknamen (DNN) sowohl für Failover-Cluster-Instanzen als auch für Verfügbarkeitsgruppen-Listener zu konfigurieren. 

Der Name eines verteilten Netzwerks ist die empfohlene Konnektivitätsoption, falls verfügbar: 
- Die End-to-End-Lösung ist stabiler, da Sie die Lastenausgleichsressource nicht mehr pflegen müssen. 
- Wenn Sie die Lastenausgleichstests weglassen, wird die Failoverdauer minimiert. 
- Der DNN vereinfacht die Bereitstellung und Verwaltung der Failoverclusterinstanz oder des Verfügbarkeitsgruppenlisteners mit SQL Server auf virtuellen Azure-Computern. 

Beachten Sie die folgenden Einschränkungen: 
- Der Client-Treiber muss den Parameter `MultiSubnetFailover=True` unterstützen. 
- Die DNN-Funktion ist ab [SQL Server 2016 SP3](https://support.microsoft.com/topic/kb5003279-sql-server-2016-service-pack-3-release-information-46ab9543-5cf9-464d-bd63-796279591c31), [SQL Server 2017 CU25](https://support.microsoft.com/topic/kb5003830-cumulative-update-25-for-sql-server-2017-357b80dc-43b5-447c-b544-7503eee189e9) und [SQL Server 2019 CU8](https://support.microsoft.com/topic/cumulative-update-8-for-sql-server-2019-ed7f79d9-a3f0-a5c2-0bef-d0b7961d2d72) auf Windows Server 2016 und höher verfügbar.

Weitere Informationen finden Sie in der [Übersicht über Windows Server-Failovercluster](hadr-windows-server-failover-cluster-overview.md#virtual-network-name-vnn). 

Informationen zum Konfigurieren der Konnektivität finden Sie in den folgenden Artikeln:
- Verfügbarkeitsgruppe: [Konfigurieren eines DNN-Listeners für eine Verfügbarkeitsgruppe](availability-group-distributed-network-name-dnn-listener-configure.md), [Konfigurieren von Load Balancer für einen AG-VNN-Listener](availability-group-vnn-azure-load-balancer-configure.md)
- Failoverclusterinstanz: [Konfigurieren eines DNN-Listeners für eine Verfügbarkeitsgruppe](failover-cluster-instance-distributed-network-name-dnn-configure.md), [Konfigurieren von Load Balancer für einen AG-VNN-Listener](failover-cluster-instance-vnn-azure-load-balancer-configure.md). 

Bei Verwendung des DNN funktionieren die meisten SQL Server-Features transparent mit FCI und Verfügbarkeitsgruppen. Es gibt jedoch bestimmte Features, die ggf. besondere Aufmerksamkeit erfordern. Weitere Informationen finden Sie unter [Featureinteroperabilität mit SQL Server-FCI und DNN](failover-cluster-instance-dnn-interoperability.md) sowie unter [Featureinteroperabilität mit Verfügbarkeitsgruppe und DNN-Listener](availability-group-dnn-interoperability.md). 

>[!TIP]
> Legen Sie den Parameter MultiSubnetFailover = true in der Verbindungszeichenfolge fest, auch für HADR-Lösungen, die ein einzelnes Subnetz umfassen, um die künftige Überbrückung von Subnetzen zu unterstützen, ohne die Verbindungszeichenfolgen aktualisieren zu müssen.  

## <a name="heartbeat-and-threshold"></a>Takt und Schwellenwert 

Ändern Sie den Clustertakt und die Schwellenwerteinstellungen in gelockerte Einstellungen. Die standardmäßigen Clustereinstellungen für Takt und Schwellenwert sind für hochgradig optimierte lokale Netzwerke konzipiert und berücksichtigen nicht die Möglichkeit einer höheren Latenz in einer Cloudumgebung. Das Taktnetzwerk wird mit UDP 3343 verwaltet, das normalerweise weitaus weniger zuverlässig als TCP und anfälliger für unvollständige Konversationen ist.
 
Ändern Sie daher beim Ausführen von Clusterknoten für SQL Server auf Azure-VM-Hochverfügbarkeitslösungen die Clustereinstellungen in einen gelockerten Überwachungsstatus, um vorübergehende Ausfälle aufgrund der erhöhten Wahrscheinlichkeit von Netzwerklatenz oder -ausfall, Azure-Wartung oder Ressourcenengpässen zu vermeiden. 

Die Einstellungen für Verzögerung und Schwellenwert wirken sich kumulativ auf die gesamte Integritätserkennung aus. Wenn Sie z. B. *CrossSubnetDelay* so festlegen, dass alle 2 Sekunden ein Takt gesendet wird, und *CrossSubnetThreshold* auf 10 verpasste Takte, nach denen die Wiederherstellung durchgeführt wird, kann der Cluster eine gesamte Netzwerktoleranz von 20 Sekunden haben, bevor die Wiederherstellungsaktion ausgeführt wird. Im Allgemeinen wird bevorzugt, weiterhin häufige Takte zu senden, aber höhere Schwellenwerte zu verwenden. 

Um die Wiederherstellung während legitimer Ausfälle zu gewährleisten und gleichzeitig eine größere Toleranz für vorübergehende Probleme zu bieten, lockern Sie ihre Einstellungen für Verzögerung und Schwellenwert auf die empfohlenen Werte, die in der folgenden Tabelle beschrieben werden: 

| Einstellung | Windows Server 2012 oder höher | Windows Server 2008R2 |
|:---------------------|:----------------------------|:-----------------------|
| SameSubnetDelay      | 1 Sekunde                    | 2 Sekunden               |
| SameSubnetThreshold  | 40 Takte               | 10 Takte (max.)         |
| CrossSubnetDelay     | 1 Sekunde                    | 2 Sekunden               |  
| CrossSubnetThreshold | 40 Takte               | 20 Takte (max.)         |


Verwenden Sie PowerShell, um Ihre Clusterparameter zu ändern: 

# <a name="windows-server-2012-2019"></a>[Windows Server 2012-2019](#tab/windows2012)


```powershell
(get-cluster).SameSubnetThreshold = 40
(get-cluster).CrossSubnetThreshold = 40
```

# <a name="windows-server-2008r2"></a>[Windows Server 2008/R2](#tab/windows2008)


```powershell
(get-cluster).SameSubnetThreshold = 10
(get-cluster).CrossSubnetThreshold = 20 
(get-cluster).SameSubnetDelay = 2000
(get-cluster).CrossSubnetDelay = 2000
```

---

Verwenden Sie PowerShell, um Ihre Änderungen zu überprüfen: 

```powershell
get-cluster | fl *subnet*
```

Beachten Sie Folgendes: 

* Diese Änderung erfolgt sofort, und es ist kein Neustart des Clusters oder sonstiger Ressourcen erforderlich. 
* Subnetzwerte dürfen nicht größer als die gleichen subnetzübergreifenden Werte sein. 
* SameSubnetThreshold <= CrossSubnetThreshold
* SameSubnetDelay <= CrossSubnetDelay

Legen Sie abhängig von Ihrer Anwendung, Ihren Geschäftsanforderungen und Ihrer Umgebung fest, wie viel Ausfallzeit tolerierbar ist, und wieviel Zeit verstreichen soll, bis eine Korrekturmaßnahme erfolgen sollte, und wählen Sie dafür gelockerte Werte aus. Wenn Sie die Standardwerte von Windows Server 2019 nicht überschreiten können, versuchen Sie nach Möglichkeit zumindest, sie zu erreichen: 

Als Referenz finden Sie die Standardwerte in der folgenden Tabelle: 


| Einstellung | Windows Server 2019 |  Windows Server 2016 |    Windows Server 2008 – 2012 R2 |
|:---------------------|:----------------|   ------------|:----------------------------|
| SameSubnetDelay      | 1 Sekunde        | 1 Sekunde       | 1 Sekunde                    |
| SameSubnetThreshold  | 20 Takte   | 10 Takte  | 5 Takte               |
| CrossSubnetDelay     | 1 Sekunde        | 1 Sekunde     | 1 Sekunde                    |
| CrossSubnetThreshold | 20 Takte   | 10 Takte   | 5 Takte               |


Weitere Informationen finden Sie unter [Optimieren von Failovercluster-Netzwerkschwellenwerten](/windows-server/troubleshoot/iaas-sql-failover-cluster).

## <a name="relaxed-monitoring"></a>Gelockerte Überwachung

Wenn die empfohlene Optimierung des Clustertakts und der Schwellenwerteinstellungen zu wenig Toleranz aufweist und weiterhin Fehler aufgrund vorübergehender Probleme anstelle echter Ausfälle auftreten, können Sie die Überwachung Ihrer AG oder FCI gelockerter konfigurieren. In einigen Szenarien kann es vorteilhaft sein, die Überwachung je nach Aktivitätsgrad für einen bestimmten Zeitraum vorübergehend zu lockern. Beispielsweise können Sie die Überwachung lockern, wenn Sie E/A-intensive Workloads wie Datenbanksicherungen, Indexwartung, DBCC CHECKDB usw. durchführen. Sobald die Aktivität abgeschlossen ist, legen Sie die Überwachung auf weniger gelockerte Werte fest. 

> [!WARNING]
> Das Ändern dieser Einstellungen kann ein zugrunde liegendes Problem maskieren und sollte als temporäre Lösung verwendet werden, um die Wahrscheinlichkeit eines Fehlers zu verringern, anstatt sie zu beseitigen. Zugrunde liegende Probleme sollten weiterhin untersucht und behoben werden. 

Erhöhen Sie zunächst die folgenden Parameter von ihren Standardwerten ausgehend für die gelockerte Überwachung, und passen Sie sie nach Bedarf an: 


|Parameter |Standardwert  |Gelockerter Wert  |BESCHREIBUNG  |
|---------|---------|---------|---------|
|**HealthCheck-Timeout**|30.000 |60000 |Bestimmt die Integrität des primären Replikats oder Knotens. Die Clusterresourcen-DLL sp_server_diagnostics gibt Ergebnisse in einem Intervall zurück, das 1/3 des Schwellenwerts für das Integritätsprüfungstimeout entspricht. Wenn sp_server_diagnostics langsam ist oder keine Informationen zurückgibt, wartet die Ressourcen-DLL das gesamte Intervall des durch den Schwellenwert definierten Integritätsprüfungstimeouts ab, bevor festgestellt wird, dass die Ressource nicht reagiert, und bei entsprechender Konfiguration ein automatisches Failover initiiert wird. |
|**Fehlerbedingungsebene** |  3  |   2  |Bedingungen, die ein automatisches Failover auslösen. Es gibt fünf Fehlerbedingungsebenen, die von der Ebene mit den wenigsten Einschränkungen (Ebene 1) bis zur Ebene mit den meisten Einschränkungen (Ebene 5) reichen.  |

Verwenden Sie Transact-SQL (T-SQL), um die Integritätsprüfungs- und Fehlerbedingungen für AGs und FCIs zu ändern. 

Für Verfügbarkeitsgruppen: 

```sql
ALTER AVAILABILITY GROUP AG1 SET (HEALTH_CHECK_TIMEOUT =60000);
ALTER AVAILABILITY GROUP AG1 SET (FAILURE_CONDITION_LEVEL = 2);
```

Für Failoverclusterinstanzen: 

```sql
ALTER SERVER CONFIGURATION SET FAILOVER CLUSTER PROPERTY HealthCheckTimeout = 60000;
ALTER SERVER CONFIGURATION SET FAILOVER CLUSTER PROPERTY FailureConditionLevel = 2; 
```

Spezifisch für **Verfügbarkeitsgruppen**: Beginnen Sie mit den folgenden empfohlenen Parametern, und passen Sie sie nach Bedarf an: 

|Parameter |Standardwert  |Gelockerter Wert  |BESCHREIBUNG  |
|---------|---------|---------|---------|
|**Timeout für Lease**|20000|40.000|Verhindert Split Brain. |
|**Sitzungstimeout**|10000 |20000|Überprüft Kommunikationsprobleme zwischen Replikaten. Das Sitzungstimeout ist eine Replikateigenschaft, die steuert, wie lange ein Verfügbarkeitsreplikat auf eine Pingantwort von einem verbundenen Replikat wartet, bevor die Verbindung als fehlerhaft betrachtet wird. Standardmäßig wartet ein Replikat 10 Sekunden auf eine Pingantwort. Diese Replikateigenschaft gilt nur für die Verbindung zwischen einem angegebenen sekundären Replikat und dem primären Replikat der Verfügbarkeitsgruppe. |
| **Maximale Fehler im angegebenen Zeitraum** | 2 | 6 |Wird verwendet, um unbegrenzte Bewegungen einer gruppierten Ressource innerhalb mehrerer Knotenausfälle zu vermeiden. Ein zu niedriger Wert kann dazu führen, dass sich die Verfügbarkeitsgruppe in einem fehlerhaften Zustand befindet. Erhöhen Sie den Wert, um kurze Unterbrechungen aufgrund von Leistungsproblemen zu verhindern, da ein zu niedriger Wert dazu führen kann, dass sich die AG in einem fehlerhaften Zustand befindet. | 

Bevor Sie Änderungen vornehmen, sollten Sie Folgendes berücksichtigen: 
- Legen Sie keine Timeoutwerte fest, die unterhalb der Standardwerte liegen. 
- Verwenden Sie diese Gleichung, um den maximalen Leasetimeoutwert zu berechnen:   
 `Lease timeout < (2 * SameSubnetThreshold * SameSubnetDelay)`.   
  Beginnen Sie mit 40 Sekunden. Wenn Sie die zuvor empfohlenen gelockerten `SameSubnetThreshold`- und `SameSubnetDelay`-Werte verwenden, darf der Leasetimeoutwert 80 Sekunden nicht überschreiten.    
- Bei Replikaten mit synchronem Commit kann das Ändern des Sitzungstimeouts in einen hohen Wert zu einer Erhöhung der HADR_sync_commit-Wartezeiten führen.

**Timeout für Lease** 

Ändern Sie mit dem **Failovercluster-Manager** die **Leasetimeout**-Einstellungen für Ihre Verfügbarkeitsgruppe. Ausführliche Schritte finden Sie in der SQL Server-Dokumentation unter [Timeout für Lease](/sql/database-engine/availability-groups/windows/availability-group-lease-healthcheck-timeout#lease-timeout).

**Sitzungstimeout** 

Ändern Sie mit Transact-SQL (T-SQL) das **Sitzungstimeout** für eine Verfügbarkeitsgruppe: 

```sql
ALTER AVAILABILITY GROUP AG1
MODIFY REPLICA ON 'INSTANCE01' WITH (SESSION_TIMEOUT = 15);
```

**Maximale Fehler im angegebenen Zeitraum**

Ändern Sie mit dem Failovercluster-Manager den Wert von **Maximale Fehler im angegebenen Zeitraum**: 
1. Wählen Sie im Navigationsbereich **Rollen** aus.
1. Klicken Sie unter **Rollen** mit der rechten Maustaste auf die gruppierte Ressource, und wählen Sie **Eigenschaften** aus. 
1. Wählen Sie die Registerkarte **Failover** aus, und erhöhen Sie den Wert **Maximale Fehler im angegebenen Zeitraum** wie gewünscht. 

## <a name="resource-limits"></a>Ressourceneinschränkungen

Grenzwerte für virtuelle Computer oder Datenträger können zu einem Ressourcenengpass führen, der sich auf die Integrität des Clusters auswirkt und die Integritätsprüfung beeinträchtigt. Wenn Probleme mit Ressourcengrenzwerten auftreten, berücksichtigen Sie Folgendes: 

* Stellen Sie sicher, dass Betriebssystem, Treiber und SQL Server den neuesten Builds entsprechen.
* Optimieren Sie SQL Server in der Azure-VM-Umgebung, wie in den [Leistungsrichtlinien](performance-guidelines-best-practices-checklist.md) für SQL Server in Azure Virtual Machines beschrieben.
* Reduzieren oder verteilen Sie die Workload, um die Auslastung zu reduzieren, ohne Ressourcengrenzwerte zu überschreiten.
* Optimieren Sie nach Möglichkeit die SQL Server-Workload, z. B.
    * Fügen Sie Indizes hinzu, bzw. optimieren Sie sie.
    * Aktualisieren Sie ggf. Statistiken und nach Möglichkeit mit vollständiger Überprüfung.  
    * Verwenden Sie Features wie den Resource Governor (ab SQL Server 2014, nur Enterprise), um die Ressourcenauslastung während bestimmter Workloads wie Sicherungen oder Indexwartung zu begrenzen. 
* Wechseln Sie zu einem virtuellen Computer oder Datenträger mit höheren Grenzwerten, um die Anforderungen Ihrer Workload zu erfüllen oder zu überschreiten. 

## <a name="networking"></a>Netzwerk

Stellen Sie Ihre SQL Server-VMs nach Möglichkeit in mehreren Subnetzen bereit, um die Abhängigkeit von einem Azure Load Balancer oder einem verteilten Netzwerknamen (DNN) zur Weiterleitung des Datenverkehrs an Ihre HADR-Lösung zu vermeiden.

Verwenden Sie eine einzelne NIC pro Server (Clusterknoten). Azure-Netzwerktechnologie bietet physische Redundanz, die zusätzliche Netzwerkkarten (NICs) in einem Azure-VM-Gastcluster überflüssig macht. Der Clusterüberprüfungsbericht weist Sie darauf hin, dass die Knoten nur in einem einzigen Netzwerk erreichbar sind. Diese Warnung kann für Azure-VM-Gastfailovercluster ignoriert werden. 

Die Bandbreitenlimits für einen bestimmten virtuellen Computer werden von NICs gemeinsam genutzt, und das Hinzufügen einer zusätzlichen NIC verbessert die Verfügbarkeitsgruppenleistung für SQL Server auf virtuellen Azure-Computern nicht. Daher ist es nicht erforderlich, eine zweite NIC hinzuzufügen. 

Der nicht RFC-kompatible DHCP-Dienst in Azure kann dazu führen, dass die Erstellung bestimmter Failoverclusterkonfigurationen fehlschlägt. Dieser Fehler tritt auf, weil dem Clusternetzwerknamen eine doppelte IP-Adresse zugewiesen wird, z. B. die gleiche IP-Adresse wie einer der Clusterknoten. Dies stellt bei der Verwendung von Verfügbarkeitsgruppen ein Problem dar, da diese vom Windows-Failoverclusterfeature abhängen.

Beachten Sie folgendes Szenario, wenn ein Cluster mit zwei Knoten erstellt und online geschaltet wird:

1. Der Cluster wird online geschaltet, und KNOTEN1 fordert eine dynamisch zugewiesene IP-Adresse als Netzwerknamen des Clusters an.
2. Der DHCP-Dienst weist ausschließlich die IP-Adresse von KNOTEN1 zu, da der DHCP-Dienst erkennt, dass die Anforderung von KNOTEN1 selbst stammt.
3. Windows erkennt, dass KNOTEN1 und dem Netzwerknamendes Failoverclusters dieselbe IP-Adresse zugewiesen wurde, und die Standardclustergruppe kann nicht online geschaltet werden.
4. Die Standardclustergruppe wechselt zu KNOTEN2. KNOTEN2 behandelt die IP-Adresse von KNOTEN1 als Cluster-IP-Adresse und schaltet die Standardclustergruppe online.
5. Wenn KNOTEN2 versucht, eine Verbindung mit KNOTEN1 herzustellen, verlassen für KNOTEN1 bestimmten Pakete niemals KNOTEN2, da die IP-Adresse von KNOTEN1 in sich selbst aufgelöst wird. KNOTEN2 kann keine Verbindung mit KNOTEN1 herstellen, verliert daraufhin das Quorum und fährt den Cluster herunter.
6. KNOTEN1 kann Pakete an KNOTEN2 senden, aber KNOTEN2 kann nicht antworten. KNOTEN1 verliert das Quorum und fährt den Cluster herunter.

Sie können dieses Szenario vermeiden, indem Sie dem Clusternetzwerknamen eine unbenutzte statische IP-Adresse zuweisen, um den Clusternetzwerknamen online zu bringen und die IP-Adresse zu [Azure Load Balancer](availability-group-load-balancer-portal-configure.md) hinzuzufügen.

## <a name="known-issues"></a>Bekannte Probleme

Überprüfen Sie die Lösungen für einige häufig auftretende Probleme und Fehler: 

**Aus Mitgliedschaft entfernter Clusterknoten**


Wenn die [Takt- und Schwellenwerteinstellungen für Windows-Cluster](#heartbeat-and-threshold) für Ihre Umgebung zu aggressiv sind, wird möglicherweise häufig die folgende Meldung im Systemereignisprotokoll angezeigt. 

```
Error 1135
Cluster node 'Node1' was removed from the active failover cluster membership. 
The Cluster service on this node may have stopped. This could also be due to the node having 
lost communication with other active nodes in the failover cluster. Run the Validate a 
Configuration Wizard to check your network configuration. If the condition persists, check 
for hardware or software errors related to the network adapters on this node. Also check for 
failures in any other network components to which the node is connected such as hubs, switches, or bridges.
```


Weitere Informationen finden Sie unter [Problembehandlung bei einem Clusterproblem mit Ereignis-ID 1135](/windows-server/troubleshoot/troubleshooting-cluster-event-id-1135).


**Lease ist abgelaufen** / **Lease ist nicht mehr gültig**


Wenn die [Überwachung](#relaxed-monitoring) für Ihre Umgebung zu aggressiv ist, kann es bei AG oder FCI zu häufigen Neustarts, Fehlern oder Failovern kommen. Darüber hinaus werden für Verfügbarkeitsgruppen möglicherweise die folgenden Meldungen im SQL Server-Fehlerprotokoll angezeigt: 

```
Error 19407: The lease between availability group 'PRODAG' and the Windows Server Failover Cluster has expired. 
A connectivity issue occurred between the instance of SQL Server and the Windows Server Failover Cluster. 
To determine whether the availability group is failing over correctly, check the corresponding availability group 
resource in the Windows Server Failover Cluster
```

```
Error 19419: The renewal of the lease between availability group '%.*ls' and the Windows Server Failover Cluster 
failed because the existing lease is no longer valid. 
``` 

**Verbindungstimeout**

Wenn das **Sitzungstimeout** für Ihre Verfügbarkeitsgruppenumgebung zu aggressiv ist, werden möglicherweise häufig folgende Meldungen angezeigt:

```
Error 35201: A connection timeout has occurred while attempting to establish a connection to availability 
replica 'replicaname' with ID [availability_group_id]. Either a networking or firewall issue exists, 
or the endpoint address provided for the replica is not the database mirroring endpoint of the host server instance.
```

```
Error 35206
A connection timeout has occurred on a previously established connection to availability 
replica 'replicaname' with ID [availability_group_id]. Either a networking or a firewall issue 
exists, or the availability replica has transitioned to the resolving role. 
```

**Es wird kein Failover der Gruppe ausgeführt**



Wenn der Wert **Maximal zulässige Fehler im angegebenen Zeitraum** zu niedrig ist und zeitweilige Fehler aufgrund vorübergehender Probleme auftreten, kann Ihre Verfügbarkeitsgruppe in einem fehlerhaften Zustand enden. Erhöhen Sie diesen Wert, um weitere vorübergehende Fehler zu tolerieren. 

```
Not failing over group <Resource name>, failoverCount 3, failoverThresholdSetting <Number>, computedFailoverThreshold 2. 
```


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter:

- [HADR-Einstellungen für SQL Server auf Azure-VMs](hadr-cluster-best-practices.md)
- [Windows Server-Failovercluster mit SQL Server auf Azure-VMs](hadr-windows-server-failover-cluster-overview.md)
- [Always On-Verfügbarkeitsgruppen mit SQL Server auf Azure-VMs](availability-group-overview.md)
- [Windows Server-Failovercluster mit SQL Server auf Azure-VMs](hadr-windows-server-failover-cluster-overview.md)
- [Failoverclusterinstanzen mit SQL Server auf Azure-VMs](failover-cluster-instance-overview.md)
- [AlwaysOn-Failoverclusterinstanzen (SQL Server)](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
