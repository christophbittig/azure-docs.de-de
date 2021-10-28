---
title: Implementieren von SAP BW-NLS mit SAP IQ in Azure | Microsoft-Dokumentation
description: Planen, Bereitstellen und Konfigurieren einer SAP BW-NLS-Lösung mit SAP IQ in Azure.
services: virtual-machines,virtual-machines-windows,virtual-machines-linux,virtual-network,storage,azure-netapp-files,azure-shared-disk,managed-disk
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/11/2021
ms.author: depadia
ms.openlocfilehash: b4bdebe8ce06ba402df9d798e346a43dfefd89a9
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130261403"
---
# <a name="sap-bw-nls-implementation-guide-with-sap-iq-on-azure"></a>Leitfaden zur SAP BW-NLS-Implementierung mit SAP IQ in Azure

Im Laufe der Jahre beobachten Kunden, die das SAP Business Warehouse-System (BW) ausführen, ein exponentielles Wachstum der Datenbankgröße, wodurch sich die Computekosten erhöhen. Damit das richtige Gleichgewicht zwischen Kosten und Leistung erzielt wird, können Kunden Nearline-Speicher (NLS) zum Migrieren historischer Daten einsetzen. 

Die auf SAP IQ basierende NLS-Implementierung ist die Standardmethode von SAP, mit der historische Daten aus einer primären Datenbank (SAP HANA oder AnyDB) verschoben werden. Die Integration von SAP IQ ermöglicht es, häufig abgerufene Daten von selten abgerufenen Daten zu trennen. Dadurch sinkt der Ressourcenbedarf auf dem SAP BW-System. 

Dieser Leitfaden enthält Richtlinien für die Planung, Bereitstellung und Konfiguration von SAP BW-NLS mit SAP IQ in Azure. In diesem Leitfaden werden allgemeine Azure-Dienste und -Features behandelt, die für die SAP IQ-NLS-Bereitstellung relevant sind. NLS-Partnerlösungen sind nicht Gegenstand des Leitfadens. 

Dieser Leitfaden ersetzt nicht die SAP-Standarddokumentation zur NLS-Bereitstellung über SAP IQ. Stattdessen ergänzt er die offizielle Dokumentation für Installation und Verwaltung. 

## <a name="solution-overview"></a>Lösungsübersicht

In einem operativen SAP BW-System steigt die Datenmenge aufgrund geschäftlicher und rechtlicher Anforderungen kontinuierlich an. Die große Datenmenge kann sich auf die Leistung des Systems auswirken und den Verwaltungsaufwand erhöhen. Dies führt dazu, dass eine Strategie zur Datenalterung implementiert werden muss. 

Wenn Sie die Datenmenge in Ihrem SAP BW-System behalten möchten, ohne sie zu löschen, können Sie die Datenarchivierung verwenden. Die Daten werden zuerst in den Archiv- oder Nearline-Speicher verschoben und dann aus dem SAP BW-System gelöscht. Sie können entweder direkt auf die Daten zugreifen oder die Daten ganz nach Bedarf zurückladen, je nachdem, wie die Daten archiviert wurden. 

Benutzer von SAP BW können SAP IQ als Nearline-Speicherlösung verwenden. Der Adapter für SAP IQ als Nearline-Speicherlösung wird mit dem SAP BW-System geliefert. Wenn NLS implementiert wird, werden häufig verwendete Daten in einer SAP BW-Onlinedatenbank (SAP HANA oder AnyDB) gespeichert. Selten genutzte Daten werden in SAP IQ gespeichert, wodurch sich die Kosten für die Verwaltung von Daten reduzieren und die Leistung des SAP BW-Systems verbessert wird. Damit die Konsistenz zwischen Onlinedaten und Near-Line-Daten gewährleistet ist, sind die archivierten Partitionen gesperrt und schreibgeschützt. 

SAP IQ unterstützt zwei Architekturtypen: Simplex und Multiplex. In einer Simplex-Architektur wird eine einzige Instanz eines SAP IQ-Servers auf einer einzigen VM ausgeführt. Dateien können sich auf einem Hostcomputer oder auf einem Netzwerkspeichergerät befinden. 

> [!Important]
> Für die SAP-NLS-Lösung wird nur die Simplex-Architektur bereitgestellt und von SAP ausgewertet.

![Diagramm mit einer Übersicht über die SAP IQ-Lösung](media/sap-iq-deployment-guide/sap-iq-solution-overview.png)

In Azure muss der SAP IQ-Server auf einer separaten VM implementiert werden. Es wird davon abgeraten, SAP IQ-Software auf einem vorhandenen Server zu installieren, auf dem bereits eine andere Datenbankinstanz ausgeführt wird, weil CPU und Arbeitsspeicher von SAP IQ vollständig für die eigene Nutzung verwendet werden. Ein einziger SAP IQ-Server kann für mehrere SAP-NLS-Implementierungen verwendet werden. 

## <a name="support-matrix"></a>Unterstützungsmatrix

Die Unterstützungsmatrix für eine SAP IQ-NLS-Lösung umfasst Folgendes:

- **Betriebssystem**: SAP IQ ist nur auf Betriebssystemebene zertifiziert. Sie können ein SAP IQ-zertifiziertes Betriebssystem in einer Azure-Umgebung ausführen, solange es für die Ausführung in der Azure-Infrastruktur kompatibel ist. Weitere Informationen finden Sie im [SAP-Hinweis 2133194](https://launchpad.support.sap.com/#/notes/2133194).

- **SAP BW-Kompatibilität**: Der Near-Line-Speicher für SAP IQ wird nur für SAP BW-Systeme veröffentlicht, die bereits unter Unicode ausgeführt werden. [SAP-Hinweis 1796393](https://launchpad.support.sap.com/#/notes/1796393) enthält Informationen zu SAP BW.

- **Speicher**: In Azure unterstützt SAP IQ verwaltete Premium-Datenträger (Windows und Linux), freigegebene Azure-Datenträger (nur Windows) und Azure NetApp Files (nur Linux). 

Aktuellere Informationen für Ihr jeweiliges SAP IQ-Release finden Sie in der [Produktverfügbarkeitsmatrix](https://userapps.support.sap.com/sap/support/pam). 

## <a name="sizing"></a>Festlegen der Größe

Die Dimensionierung von SAP IQ ist auf CPU, Arbeitsspeicher und Speicher beschränkt. Allgemeine Dimensionierungsrichtlinien für SAP IQ in Azure finden Sie im [SAP-Hinweis 1951789](https://launchpad.support.sap.com/#/notes/1951789). Die Dimensionierungsempfehlung, die Sie durch Befolgen der Richtlinien erhalten, muss zertifizierten Azure-VM-Typen für SAP zugeordnet werden. Im [SAP-Hinweis 1928533](https://launchpad.support.sap.com/#/notes/1928533) finden Sie die Liste der unterstützten SAP-Produkte und Azure-VM-Typen. 

Der SAP IQ-Dimensionierungsleitfaden und das Arbeitsblatt zur Dimensionierung, die im [SAP-Hinweis 1951789](https://launchpad.support.sap.com/#/notes/1951789) erwähnt werden, wurden für die native Verwendung einer SAP IQ-Datenbank entwickelt. Da sie nicht die Ressourcen für die Planung einer SAP IQ-Datenbank widerspiegeln, werden letztlich möglicherweise nicht verwendete Ressourcen für SAP-NLS eingesetzt.

## <a name="azure-resources"></a>Azure-Ressourcen

### <a name="regions"></a>Regions

Wenn Sie Ihre SAP-Systeme bereits in Azure ausführen, haben Sie Ihre Azure-Region wahrscheinlich schon festgelegt. Die SAP IQ-Bereitstellung muss sich in derselben Region wie die des SAP BW-Systems befinden, für das Sie die NLS-Lösung implementieren. 

Um die Architektur von SAP IQ festzulegen, müssen Sie sicherstellen, dass die für SAP IQ erforderlichen Dienste wie Azure NetApp Files (NFS nur für Linux) in dieser Region verfügbar sind. Zum Überprüfen der Dienstverfügbarkeit in Ihrer Region sehen Sie sich die Webseite [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/) an.

### <a name="availability-sets"></a>Verfügbarkeitsgruppen 

Um Redundanz von SAP-Systemen in einer Azure-Infrastruktur zu erzielen, muss Ihre Anwendung entweder in Verfügbarkeitsgruppen oder in Verfügbarkeitszonen bereitgestellt werden. Sie können SAP IQ-Hochverfügbarkeit zwar mithilfe der SAP IQ-Multiplex-Architektur erreichen, aber die Multiplex-Architektur erfüllt nicht die Anforderungen der NLS-Lösung. 

Um Hochverfügbarkeit für die SAP IQ-Simplex-Architektur zu erzielen, müssen Sie einen Cluster mit zwei Knoten mit einer benutzerdefinierten Lösung konfigurieren. Der SAP IQ-Cluster mit zwei Knoten kann in Verfügbarkeitsgruppen oder Verfügbarkeitszonen bereitgestellt werden, aber der Azure-Speichertyp, der an die Knoten angefügt wird, gibt die Bereitstellungsmethode vor. Derzeit unterstützen freigegebene Azure-Premium-Datenträger und Azure NetApp Files keine zonengebundene Bereitstellung. Dadurch bleibt nur die Option der SAP IQ-Bereitstellung in Verfügbarkeitsgruppen. 

### <a name="virtual-machines"></a>Virtuelle Computer

Basierend auf der SAP IQ-Größe müssen Sie Ihre Anforderungen virtuellen Azure-Computern zuordnen. Dieser Ansatz wird in Azure für SAP-Produkte unterstützt. [SAP-Hinweis 1928533](https://launchpad.support.sap.com/#/notes/1928533) ist ein guter Ausgangspunkt, weil darin die unterstützten Azure-VM-Typen für SAP-Produkte unter Windows und Linux aufgelistet werden. 

Neben der Auswahl ausschließlich unterstützter VM-Typen ist auch zu prüfen, ob diese VM-Typen in bestimmten Regionen verfügbar sind. Sie können die Verfügbarkeit von VM-Typen auf der Webseite [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/) feststellen. Informationen zum Auswählen des Preismodells finden Sie unter [Virtuelle Azure-Computer für SAP-Workloads](planning-guide.md#azure-virtual-machines-for-sap-workload). 

> [!TIP]
> Für Produktionssysteme wird empfohlen, VMs der E-Serie aufgrund ihres Verhältnisses zwischen Kernen und Arbeitsspeicher zu verwenden.

### <a name="storage"></a>Storage

Azure Storage stellt den Kunden verschiedene Speichertypen zur Verfügung. Weitere Details zu diesem Thema finden Sie im Artikel [Welche Datenträgertypen stehen in Azure zur Verfügung](../../disks-types.md). 

Einige der Speichertypen in Azure sind nur eingeschränkt für SAP-Szenarien geeignet. Andere Typen hingegen eignen sich ideal für bestimmte SAP-Workloadszenarien oder wurden für diese optimiert. Weitere Informationen finden Sie im Leitfaden [Azure Storage-Typen für die SAP-Workload](planning-guide-storage.md). Darin werden die Speicheroptionen hervorgehoben, die für SAP geeignet sind. 

Für SAP IQ in Azure können Sie die folgenden Azure-Speichertypen verwenden. Die Auswahl hängt von Ihrem Betriebssystem (Windows oder Linux) und der Bereitstellungsmethode (eigenständig oder hoch verfügbar) ab.

- Verwaltete Azure-Datenträger

  Ein [verwalteter Datenträger](../../managed-disks-overview.md) ist ein Speichervolume auf Blockebene, das von Azure verwaltet wird. Sie können verwaltete Datenträger für die SAP IQ-Simplex-Bereitstellung verwenden. Verschiedene Arten verwalteter Datenträger stehen zur Verfügung, für SAP IQ werden jedoch [SSD Premium-Datenträger](../../disks-types.md#premium-ssds) empfohlen. 

- Freigegebene Azure-Datenträger

  [Freigegebene Datenträger](../../disks-shared.md) sind ein neues Feature für verwaltete Azure-Datenträger, mit dem Sie einen verwalteten Datenträger gleichzeitig an mehrere VMs anfügen können. Freigegebene verwaltete Datenträger bieten nativ kein vollständig verwaltetes Dateisystem, auf das über SMB oder NFS zugegriffen werden kann. Sie müssen einen Cluster-Manager wie z. B. einen [Windows Server-Failovercluster](https://github.com/MicrosoftDocs/windowsserverdocs/blob/master/WindowsServerDocs/failover-clustering/failover-clustering-overview.md) (WSFC) verwenden, der die Clusterknotenkommunikation und Schreibsperren verarbeitet. 
  
  Zum Bereitstellen einer hoch verfügbaren Lösung für eine SAP IQ-Simplex-Architektur unter Windows können Sie freigegebene Azure-Datenträger zwischen zwei Knoten verwenden, die von WSFC verwaltet werden. Die SAP IQ-Bereitstellungsarchitektur mit freigegebenen Azure-Datenträgern wird im Artikel [Bereitstellen einer SAP IQ-NLS-Hochverfügbarkeitslösung mit freigegebenen Azure-Datenträgern unter Windows Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-shared-disk-on-windows/ba-p/2433089) erläutert.

- Azure NetApp Files

  Die SAP IQ-Bereitstellung unter Linux kann [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) als Dateisystem (NFS-Protokoll) verwenden, um eine eigenständige oder hoch verfügbare Lösung zu installieren. Dieses Speicherangebot ist nicht in allen Regionen verfügbar. Aktuelle Informationen finden Sie auf der Webseite [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/). Die SAP IQ-Bereitstellungsarchitektur mit Azure NetApp Files wird im Artikel [Bereitstellen einer SAP IQ-NLS-Hochverfügbarkeitslösung mit Azure NetApp Files unter SUSE Linux Enterprise Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-netapp-files-on-suse/ba-p/1651172) erläutert.

In der folgenden Tabelle sind die Empfehlungen für die einzelnen Speichertypen basierend auf dem jeweiligen Betriebssystem aufgeführt: 

| Speichertyp        | Windows | Linux |
| ------------------- | ------- | ----- |
| Verwaltete Azure-Datenträger | Ja     | Ja   |
| Freigegebene Azure-Datenträger  | Ja     | Nein    |
| Azure NetApp Files  | Nein      | Ja   |

### <a name="networking"></a>Netzwerk

Azure bietet eine Netzwerkinfrastruktur, die die Zuordnung aller Szenarien ermöglicht, die für ein SAP BW-System realisiert werden können, das SAP IQ als Nearline-Speicher verwendet. Zu diesen Szenarien gehört beispielsweise das Herstellen einer Verbindung mit lokalen Systemen oder das Herstellen einer Verbindung mit Systemen in verschiedenen virtuellen Netzwerken. Weitere Informationen finden Sie unter [Microsoft Azure-Netzwerke für SAP-Workloads](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#microsoft-azure-networking).

## <a name="deploy-sap-iq-on-windows"></a>Bereitstellen von SAP IQ unter Windows

### <a name="server-preparation-and-installation"></a>Server: Vorbereitung und Installation

Die neuesten Informationen zum Vorbereiten von Servern für die NLS-Implementierung mit SAP IQ unter Windows finden Sie im [SAP-Hinweis 2780668 – SAP-Leitfaden – BW-NLS-Implementierung mit SAP IQ](https://launchpad.support.sap.com/#/notes/0002780668). Dieser enthält umfassende Informationen zu den Voraussetzungen für SAP BW-Systeme, zum SAP IQ-Dateisystemlayout, zur Installation, zu Aufgaben nach der Konfiguration und zur SAP BW-NLS-Integration in SAP IQ.

### <a name="high-availability-deployment"></a>Bereitstellung mit hoher Verfügbarkeit

SAP IQ unterstützt sowohl eine Simplex- als auch eine Multiplex-Architektur. Für die NLS-Lösung wird nur die Simplex-Serverarchitektur bereitgestellt und ausgewertet. Der Simplex-Architekturtyp bezeichnet eine einzelne Instanz eines SAP IQ-Servers, der auf einem einzelnen virtuellen Computer ausgeführt wird. 

Technisch gesehen können Sie SAP IQ-Hochverfügbarkeit zwar mithilfe einer Multiplex-Serverarchitektur erreichen, aber die Multiplex-Architektur erfüllt nicht die Anforderungen der NLS-Lösung. Für die Simplex-Serverarchitektur bietet SAP keine Features oder Verfahren zum Ausführen von SAP IQ in einer Hochverfügbarkeitskonfiguration. 

Zum Einrichten von SAP IQ-Hochverfügbarkeit unter Windows für die Simplex-Serverarchitektur müssen Sie eine benutzerdefinierte Lösung einrichten, die eine zusätzliche Konfiguration erfordert, z. B. einen Windows Server-Failovercluster und freigegebene Datenträger. Eine solche benutzerdefinierte Lösung für SAP IQ unter Windows wird ausführlich unter [Bereitstellen einer SAP IQ-NLS-Hochverfügbarkeitslösung mit freigegebenen Azure-Datenträgern unter Windows Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-shared-disk-on-windows/ba-p/2433089) beschrieben.

### <a name="backup-and-restore"></a>Sichern und Wiederherstellen

In Azure können Sie die SAP IQ-Datenbanksicherung gemäß der Beschreibung unter [SAP IQ-Verwaltung: Sicherung, Wiederherstellung und Datenwiederherstellung](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/5b8309b37f4e46b089465e380c24df59.html) planen. SAP IQ stellt die folgenden Arten von Datenbanksicherungen zur Verfügung. Details zu den einzelnen Sicherungstypen finden Sie unter [Sicherungsszenarien](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880dc1f84f21015af84f1a6b629dd7a.html).

- **Vollständige Sicherung**: Es wird eine vollständige Kopie der Datenbank erstellt. 
- **Inkrementelle Sicherung**: Es werden alle Transaktionen seit der letzten Sicherung eines beliebigen Typs kopiert. 
- **Inkrementell seit vollständiger Sicherung**: Alle seit der letzten vollständigen Sicherung vorgenommenen Änderungen an der Datenbank werden gesichert.
- **Virtuelle Sicherung**: Die gesamte Datenbank mit Ausnahme der Tabellen- und Metadaten wird aus dem SAP IQ-Speicher kopiert.

Abhängig von Ihrer SAP IQ-Datenbankgröße können Sie die Datenbanksicherung ausgehend von jedem der Sicherungsszenarien planen. Wenn Sie jedoch SAP IQ mit der von SAP bereitgestellten NLS-Schnittstelle verwenden, sollten Sie den Sicherungsprozess für eine SAP IQ-Datenbank automatisieren. Die Automatisierung stellt sicher, dass die SAP IQ-Datenbank immer in einem konsistenten Zustand wiederhergestellt werden kann, ohne dass die zwischen der primären Datenbank und der SAP IQ-Datenbank verschobenen Daten verloren gehen. Weitere Informationen zum Einrichten der Automatisierung für SAP IQ-NLS (Nearline-Speicher) finden Sie im [SAP-Hinweis 2741824 – Einrichten der Sicherungsautomatisierung für Cold Storage und Nearline-Speicher in SAP IQ](https://launchpad.support.sap.com/#/notes/2741824). 

Für eine große SAP IQ-Datenbank können Sie virtuelle Sicherungen verwenden. Weitere Informationen finden Sie unter [Virtuelle Sicherungen](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880672184f21015a08dceedc7d19776.html), Einführung zur virtuellen Sicherung in [SAP Sybase IQ](https://wiki.scn.sap.com/wiki/display/SYBIQ/Introduction+Virtual+BackUp+(+general++back+up+method+)+in+SAP+Sybase+IQ). Lesen Sie auch [SAP-Hinweis 2461985 – Sichern umfangreicher SAP IQ-Datenbanken](https://launchpad.support.sap.com/#/notes/0002461985).

Wenn Sie ein Netzwerklaufwerk (SMB-Protokoll) zum Sichern und Wiederherstellen eines SAP IQ-Servers unter Windows verwenden, stellen Sie sicher, dass Sie den UNC-Pfad für die Sicherung verwenden. Bei Verwendung eines UNC-Pfads für die Sicherung und Wiederherstellung sind drei umgekehrte Schrägstriche (`\\\`) erforderlich:

```sql
BACKUP DATABASE FULL TO '\\\sapiq.internal.contoso.net\sapiq-backup\backup\data\<filename>'
```

## <a name="deploy-sap-iq-on-linux"></a>Bereitstellen von SAP IQ unter Linux

### <a name="server-preparation-and-installation"></a>Server: Vorbereitung und Installation

Die neuesten Informationen zum Vorbereiten von Servern für die NLS-Implementierung mit SAP IQ unter Linux finden Sie im [SAP-Hinweis 2780668 – SAP-Leitfaden – BW-NLS-Implementierung mit SAP IQ](https://launchpad.support.sap.com/#/notes/0002780668). Dieser enthält umfassende Informationen zu den Voraussetzungen für SAP BW-Systeme, zum SAP IQ-Dateisystemlayout, zur Installation, zu Aufgaben nach der Konfiguration und zur SAP BW-NLS-Integration in SAP IQ.

### <a name="high-availability-deployment"></a>Bereitstellung mit hoher Verfügbarkeit

SAP IQ unterstützt sowohl eine Simplex- als auch eine Multiplex-Architektur. Für die NLS-Lösung wird nur die Simplex-Serverarchitektur bereitgestellt und ausgewertet. Der Simplex-Architekturtyp bezeichnet eine einzelne Instanz eines SAP IQ-Servers, der auf einem einzelnen virtuellen Computer ausgeführt wird. 

Technisch gesehen können Sie SAP IQ-Hochverfügbarkeit zwar mithilfe einer Multiplex-Serverarchitektur erreichen, aber die Multiplex-Architektur erfüllt nicht die Anforderungen der NLS-Lösung. Für die Simplex-Serverarchitektur bietet SAP keine Features oder Verfahren zum Ausführen von SAP IQ in einer Hochverfügbarkeitskonfiguration.

Zum Einrichten der SAP IQ-Hochverfügbarkeit unter Linux für die Simplex-Serverarchitektur müssen Sie eine benutzerdefinierte Lösung einrichten, die eine zusätzliche Konfiguration wie Pacemaker erfordert. Eine solche benutzerdefinierte Lösung für SAP IQ unter Linux wird ausführlich unter [Bereitstellen einer SAP IQ-NLS-Hochverfügbarkeitslösung mit Azure NetApp Files unter SUSE Linux Enterprise Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-netapp-files-on-suse/ba-p/1651172) beschrieben.

### <a name="backup-and-restore"></a>Sichern und Wiederherstellen

In Azure können Sie die SAP IQ-Datenbanksicherung gemäß der Beschreibung unter [SAP IQ-Verwaltung: Sicherung, Wiederherstellung und Datenwiederherstellung](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/5b8309b37f4e46b089465e380c24df59.html) planen. SAP IQ stellt die folgenden Arten von Datenbanksicherungen zur Verfügung. Details zu den einzelnen Sicherungstypen finden Sie unter [Sicherungsszenarien](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880dc1f84f21015af84f1a6b629dd7a.html).

- **Vollständige Sicherung**: Es wird eine vollständige Kopie der Datenbank erstellt. 
- **Inkrementelle Sicherung**: Es werden alle Transaktionen seit der letzten Sicherung eines beliebigen Typs kopiert. 
- **Inkrementell seit vollständiger Sicherung**: Alle seit der letzten vollständigen Sicherung vorgenommenen Änderungen an der Datenbank werden gesichert.
- **Virtuelle Sicherung**: Die gesamte Datenbank mit Ausnahme der Tabellen- und Metadaten wird aus dem SAP IQ-Speicher kopiert.

Abhängig von Ihrer SAP IQ-Datenbankgröße können Sie die Datenbanksicherung ausgehend von jedem der Sicherungsszenarien planen. Wenn Sie jedoch SAP IQ mit der von SAP bereitgestellten NLS-Schnittstelle verwenden, sollten Sie den Sicherungsprozess für eine SAP IQ-Datenbank automatisieren. Die Automatisierung stellt sicher, dass die SAP IQ-Datenbank immer in einem konsistenten Zustand wiederhergestellt werden kann, ohne dass die zwischen der primären Datenbank und der SAP IQ-Datenbank verschobenen Daten verloren gehen. Weitere Informationen zum Einrichten der Automatisierung für SAP IQ-NLS (Nearline-Speicher) finden Sie im [SAP-Hinweis 2741824 – Einrichten der Sicherungsautomatisierung für Cold Storage und Nearline-Speicher in SAP IQ](https://launchpad.support.sap.com/#/notes/2741824). 

Für eine große SAP IQ-Datenbank können Sie virtuelle Sicherungen verwenden. Weitere Informationen finden Sie unter [Virtuelle Sicherungen](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880672184f21015a08dceedc7d19776.html), Einführung zur virtuellen Sicherung in [SAP Sybase IQ](https://wiki.scn.sap.com/wiki/display/SYBIQ/Introduction+Virtual+BackUp+(+general++back+up+method+)+in+SAP+Sybase+IQ). Lesen Sie auch [SAP-Hinweis 2461985 – Sichern umfangreicher SAP IQ-Datenbanken](https://launchpad.support.sap.com/#/notes/0002461985).

## <a name="disaster-recovery"></a>Notfallwiederherstellung

In diesem Abschnitt wird die Strategie zum Bereitstellen von Notfallwiederherstellungsschutz für die SAP IQ-NLS-Lösung erläutert. Dies ist eine Ergänzung des Artikels [Einrichten der Notfallwiederherstellung für die Bereitstellung einer SAP NetWeaver-App mit mehreren Ebenen](../../../site-recovery/site-recovery-sap.md), in dem die wichtigsten Ressourcen für einen allgemeinen Ansatz zur Notfallwiederherstellung für SAP beschrieben sind. Der in diesem Artikel beschriebene Prozess wird als Übersicht dargestellt. Sie müssen die genauen Schritte überprüfen und Ihre Strategie für die Notfallwiederherstellung gründlich testen. 

Informationen zu SAP IQ finden Sie im [SAP-Hinweis 2566083](https://launchpad.support.sap.com/#/notes/0002566083), in dem Methoden zum sicheren Implementieren einer Umgebung für die Notfallwiederherstellung beschrieben werden. In Azure können Sie auch [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) für die Strategie zur SAP IQ-Notfallwiederherstellung verwenden. Die Strategie für die SAP IQ-Notfallwiederherstellung hängt von der Art der Bereitstellung in Azure ab und sollte auch mit Ihrem SAP BW-System im Einklang stehen. 

### <a name="standalone-deployment-of-sap-iq"></a>Eigenständige Bereitstellung von SAP IQ

Wenn Sie SAP IQ als eigenständiges System ohne Redundanz oder Hochverfügbarkeit auf Anwendungsebene installiert haben, für das Unternehmen jedoch eine Notfallwiederherstellung eingerichtet werden muss, sind alle an die VM angefügten (von Azure verwalteten) Datenträger lokale Datenträger. 
  
[Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) kann dazu verwendet werden, die eigenständige SAP IQ-VM in der sekundären Region zu replizieren. Hierbei werden die Server und alle angefügten verwalteten Datenträger in der sekundären Region repliziert, damit Sie bei Katastrophen oder Ausfällen leicht ein Failover zu Ihrer replizierten Umgebung ausführen und weiterarbeiten können. Befolgen Sie die Anleitung zum [Replizieren eines virtuellen Computers in Azure](../../../site-recovery/azure-to-azure-tutorial-enable-replication.md), um mit dem Replizieren der virtuellen SAP IQ-Computer in der Azure-Region für die Notfallwiederherstellung zu beginnen. 

### <a name="highly-available-deployment-of-sap-iq"></a>SAP IQ-Bereitstellung mit Hochverfügbarkeit

Wenn Sie SAP IQ als hoch verfügbares System installiert haben, auf dem sich SAP IQ-Binärdateien und Datenbankdateien auf einem freigegebenen Azure-Datenträger (nur Windows) oder auf einem Netzlaufwerk wie Azure NetApp Files (nur Linux) befinden, müssen Sie Folgendes ermitteln:

- Benötigen Sie das gleiche hoch verfügbare SAP IQ-System am Notfallwiederherstellungsstandort?
- Genügt eine eigenständige SAP IQ-Instanz Ihren geschäftlichen Anforderungen? 
  
Falls Sie an einem Notfallwiederherstellungsstandort eine eigenständige SAP IQ-Instanz benötigen, können Sie die primäre SAP IQ-VM mithilfe von [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) in der sekundären Region replizieren. Dabei werden die Server und alle lokal angefügten verwalteten Datenträger in der sekundären Region repliziert, aber es wird kein freigegebener Azure-Datenträger bzw. kein freigegebenes Netzlaufwerk wie Azure NetApp Files repliziert. 
  
Um Daten von einem freigegebenen Azure-Datenträger oder Netzlaufwerk zu kopieren, können Sie ein beliebiges dateibasiertes Kopiertool zum Replizieren von Daten zwischen Azure-Regionen verwenden. Weitere Informationen zum Kopieren eines Azure NetApp Files-Volumes in eine andere Region finden Sie unter [Häufig gestellte Fragen zu Azure NetApp Files](../../../azure-netapp-files/faq-data-migration-protection.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region).

## <a name="next-steps"></a>Nächste Schritte

- [Einrichten der Notfallwiederherstellung für die Bereitstellung einer SAP-App mit mehreren Ebenen](../../../site-recovery/site-recovery-sap.md)
- [Azure Virtual Machines – Planung und Implementierung für SAP](planning-guide.md)
- [Azure Virtual Machines – Bereitstellung für SAP](deployment-guide.md)
