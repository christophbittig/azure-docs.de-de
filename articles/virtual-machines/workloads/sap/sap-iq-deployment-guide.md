---
title: Implementieren von SAP-NLS/SDA mit SAP IQ in Azure | Microsoft-Dokumentation
description: Planen, Bereitstellen und Konfigurieren einer SAP-NLS/SDA-Lösung mit SAP IQ in Azure.
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
ms.openlocfilehash: a0641f6c00712e0d3dd32ff04d7bff9695dc4ce1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122349724"
---
# <a name="sap-bw-near-line-storage-nls-implementation-guide-with-sap-iq-on-azure"></a>Implementierungsleitfaden für SAP BW-Near-Line Storage (NLS) mit SAP IQ in Azure

## <a name="overview"></a>Übersicht

Im Laufe der Jahre erkennt ein Kunde, der ein SAP BW-System ausführt, ein exponentielles Wachstum der Datenbankgröße, das zu einem Anstieg der Computekosten führt. Damit ein optimales Gleichgewicht zwischen Kosten und Leistung erzielt wird, kann der Kunden Near-Line Storage (NLS) verwenden, um Verlaufsdaten zu migrieren. Die NLS-Implementierung, die auf SAP IQ basiert, ist die Standardmethode von SAP, mit der Verlaufsdaten aus der primären Datenbank (SAP HANA oder AnyDB) verschoben werden. Der Adapter von SAP IQ als Near-Line-Lösung wird mit dem SAP BW-System geliefert. Die Integration von SAP IQ ermöglicht es, häufig abgerufene Daten von selten abgerufenen Daten zu trennen. Dadurch sinkt der Ressourcenbedarf auf dem SAP BW-System. 

In diesem Leitfaden finden Sie die Richtlinien für die Planung, Bereitstellung und Konfiguration von SAP BW-Near-Line Storage (NLS) mit SAP IQ in Azure. In diesem Leitfaden sollen allgemeine Azure-Dienste und -Features behandelt werden, die für die SAP IQ-NLS-Bereitstellung relevant sind; NLS-Partnerlösungen sind nicht Gegenstand des Leitfadens. Dieser Leitfaden soll die Standarddokumentation von SAP zur NLS-Bereitstellung mit SAP IQ nicht ersetzen. Vielmehr ergänzt er die offiziellen Dokumentationsinhalte für Installation und Verwaltung. 

## <a name="solution-overview"></a>Lösungsübersicht

In einem operativen BW-System nimmt die Datenmenge ständig zu, und diese Daten sind aufgrund der geschäftlichen und rechtlichen Anforderungen für einen längeren Zeitraum erforderlich. Die große Datenmenge kann sich auf die Leistung des Systems auswirken und den Verwaltungsaufwand erhöhen. Dies führt dazu, dass eine Strategie zur Datenalterung implementiert werden muss. Wenn Sie die Datenmenge in Ihrem SAP BW-System behalten möchten, ohne sie zu löschen, können Sie die Datenarchivierung verwenden. Die Daten werden zuerst in den Archiv- oder Near-Line-Speicher verschoben und dann aus dem BW-System gelöscht. Sie können entweder direkt auf die Daten zugreifen oder die Daten ganz nach Bedarf wieder laden, je nachdem, wie die Daten archiviert wurden. 

Benutzer von SAP BW können SAP IQ als Near-Line Storage-Lösung (NLS) verwenden. Der Adapter für SAP IQ als Near-Line-Lösung wird mit dem SAP BW-System geliefert. Wenn NLS implementiert ist, werden häufig verwendete Daten in einer SAP BW-Onlinedatenbank (SAP HANA oder AnyDB) gespeichert, während Daten, auf die nur selten zugegriffen wird, in SAP IQ gespeichert werden. Dies reduziert die Kosten für die Verwaltung von Daten und verbessert die Leistung des SAP BW-Systems. Damit die Konsistenz zwischen Onlinedaten und Near-Line-Daten gewährleistet ist, sind die archivierten Partitionen gesperrt und schreibgeschützt. 

SAP IQ unterstützt zwei Architekturtypen: Simplex und Multiplex. In der Simplex-Architektur wird eine einzelne Instanz des SAP IQ-Servers auf einem einzelnen virtuellen Computer ausgeführt, und Dateien können sich auf einem Hostcomputer oder auf einem Netzwerkspeichergerät befinden. 

> [!Important]
> Für die SAP-NLS-Lösung ist nur die Simplex-Architektur verfügbar bzw. wird nur die Simplex-Architektur von SAP ausgewertet.

![SAP IQ-Lösungsübersicht](media/sap-iq-deployment-guide/sap-iq-solution-overview.png)

In Azure muss der SAP IQ-Server auf einem separaten virtuellen Computer implementiert werden. Es wird nicht empfohlen, SAP IQ-Software auf einem vorhandenen Server zu installieren, auf dem bereits eine andere Datenbankinstanz ausgeführt wird, da von SAP IQ CPU und Arbeitsspeicher vollständig für die eigene Nutzung verwendet werden. Ein einziger SAP IQ-Server kann für mehrere SAP-NLS-Implementierungen verwendet werden. 

## <a name="support-matrix"></a>Unterstützungsmatrix

In diesem Abschnitt wird eine Übersicht über die Unterstützungsmatrix für die SAP IQ-NLS-Lösung beschrieben, die in diesem Dokument ausführlicher behandelt wird. Überprüfen Sie außerdem die [Produktverfügbarkeitsmatrix (Product Availability Matrix, PAM)](https://userapps.support.sap.com/sap/support/pam), um auf Grundlage Ihres SAP IQ-Release aktuellere Informationen zu erhalten. 

- **Betriebssystem**: SAP IQ ist nur auf Betriebssystemebene zertifiziert. Sie können ein SAP IQ-zertifiziertes Betriebssystem in einer Azure-Umgebung ausführen, solange es für die Ausführung in der Azure-Infrastruktur kompatibel ist. Weitere Informationen finden Sie im SAP-Hinweis [2133194](https://launchpad.support.sap.com/#/notes/2133194).

- **SAP BW-Kompatibilität**: Der Near-Line-Speicher für SAP IQ wird nur für SAP BW-Systeme veröffentlicht, die bereits unter Unicode ausgeführt werden. Befolgen Sie den SAP-Hinweis [1796393](https://launchpad.support.sap.com/#/notes/1796393), der Informationen zu SAP BW enthält.

- **Storage**: In Azure unterstützt SAP IQ verwaltete Premium-Datenträger (Windows/Linux), freigegebene Azure-Datenträger (nur Windows) und Azure NetApp Files (NFS – nur Linux). 

## <a name="sizing"></a>Festlegen der Größe

Die Dimensionierung von SAP IQ ist auf CPU, Arbeitsspeicher und Speicher beschränkt. Die allgemeinen Dimensionierungsrichtlinien für SAP IQ in Azure finden Sie im SAP-Hinweis [1951789](https://launchpad.support.sap.com/#/notes/1951789). Die Dimensionierungsempfehlung, die Sie erhalten, indem Sie die Richtlinien befolgen, muss zertifizierten Azure-VM-Typen für SAP zugeordnet werden. Im SAP-Hinweis [1928533](https://launchpad.support.sap.com/#/notes/1928533) finden Sie die Liste der unterstützten SAP-Produkte und Azure-VM-Typen. 

> [!Tip]
>
> Für produktive Systeme wird empfohlen, aufgrund des Verhältnisses von Kern zu Arbeitsspeicher virtuelle Computer der E-Serie zu verwenden. 

Die im SAP-Hinweis [1951789](https://launchpad.support.sap.com/#/notes/1951789) erwähnten Dokumente des Dimensionierungsleitfadens und des Arbeitsblatts zur Dimensionierung für SAP IQ wurden für die native Verwendung der SAP IQ-Datenbank entwickelt und spiegeln nicht die Ressourcen für die Planung der <SID>IQ-Datenbank wider. Möglicherweise erhalten Sie ungenutzte Ressourcen für SAP-NLS.

## <a name="azure-resources"></a>Azure-Ressourcen

### <a name="choosing-regions"></a>Auswählen von Regionen

Wenn Sie Ihre SAP-Systeme bereits in Azure ausführen, haben Sie wahrscheinlich Ihre Azure-Region identifiziert. Die SAP IQ-Bereitstellung muss sich in derselben Region wie die des SAP BW-Systems befinden, für das Sie die NLS-Lösung implementieren. Sie müssen jedoch untersuchen, ob die für SAP IQ erforderlichen Dienste wie Azure NetApp Files (NFS – nur Linux) in diesen Regionen verfügbar sind, um die Architektur von SAP IQ zu bestimmen. Zum Überprüfen der Dienstverfügbarkeit in Ihrer Region können Sie die Website [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/) überprüfen.

### <a name="availability-sets"></a>Verfügbarkeitsgruppen 

Zum Erzielen von Redundanz von SAP-Systemen in der Azure-Infrastruktur muss die Anwendung entweder in Verfügbarkeitsgruppen oder Verfügbarkeitszonen bereitgestellt werden. Technisch gesehen kann SAP IQ-Hochverfügbarkeit mit der IQ-Multiplex-Architektur erreicht werden, aber die Multiplex-Architektur erfüllt nicht die Anforderung der NLS-Lösung. Zum Erreichen von Hochverfügbarkeit für die SAP IQ-Simplex-Architektur muss ein Cluster mit zwei Knoten mit einer benutzerdefinierten Lösung konfiguriert werden. Der SAP IQ-Cluster mit zwei Knoten kann in Verfügbarkeitsgruppen oder Verfügbarkeitszonen bereitgestellt werden, aber der Azure-Speicher, der an die Knoten angefügt wird, gibt die Bereitstellungsmethode vor. Derzeit unterstützen freigegebene Azure-Premium-Datenträger und Azure NetApp Files keine Bereitstellung in Zonen, sodass nur die Option der SAP IQ-Bereitstellung in Verfügbarkeitsgruppen infrage kommt. 

### <a name="virtual-machines"></a>Virtuelle Computer

Basierend auf der SAP IQ-Dimensionierung müssen Sie Ihre Anforderung den virtuellen Azure-Computern zuordnen, die in Azure für das SAP-Produkt unterstützt werden. SAP-Hinweis [1928533](https://launchpad.support.sap.com/#/notes/1928533) ist ein guter Startpunkt, der die unterstützten Azure-VM-Typen für SAP-Produkte unter Windows und Linux auflistet. Außerdem ist zu beachten, dass über die Auswahl der ausschließlich unterstützten VM-Typen hinaus auch zu prüfen ist, ob diese VM-Typen in einer bestimmten Region verfügbar sind. Sie können die Verfügbarkeit des VM-Typs auf der Seite [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/) prüfen. Informationen zum Auswählen des Preismodells finden Sie unter [Virtuelle Azure-Computer für SAP-Workloads](planning-guide.md#azure-virtual-machines-for-sap-workload). 

Für produktive Systeme wird empfohlen, aufgrund des Verhältnisses von Kern zu Arbeitsspeicher virtuelle Computer der E-Serie zu verwenden.

### <a name="storage"></a>Speicher

Azure Storage stellt Kunden verschiedene Speichertypen zur Verfügung; Informationen dazu finden Sie im Artikel [Welche Datenträgertypen stehen in Azure zur Verfügung?](../../disks-types.md). Einige der Speichertypen sind für SAP-Szenarios nur eingeschränkt verwendbar. Dagegen sind verschiedene Azure Storage-Typen für bestimmte SAP-Workloadszenarien gut geeignet und optimiert. Weitere Informationen finden Sie im Leitfaden [Azure-Speichertypen für SAP-Workloads](planning-guide-storage.md), in dem verschiedene Speicheroptionen hervorgehoben werden, die für SAP geeignet sind. Für SAP IQ in Azure kann der folgende Azure-Speicher basierend auf dem Betriebssystem (Windows oder Linux) und der Bereitstellungsmethode (eigenständig oder hoch verfügbar) verwendet werden.

- Verwaltete Azure-Datenträger

  Dabei handelt es sich um ein Speichervolume auf Blockebene, das von Azure verwaltet wird. Sie können verwaltete Azure-Datenträger für die SAP IQ-Simplex-Bereitstellung verwenden. Es sind verschiedene Arten von [verwalteten Azure-Datenträgern](../../managed-disks-overview.md) verfügbar, aber es wird empfohlen, [SSD Premium](../../disks-types.md#premium-ssd) für SAP IQ zu verwenden. 

- Freigegebene Azure-Datenträger

  [Freigegebene Azure-Datenträger](../../disks-shared.md) sind ein neues Feature für verwaltete Azure-Datenträger, mit dem Sie einen verwalteten Datenträger gleichzeitig an mehrere virtuelle Computer anfügen können. Freigegebene verwaltete Datenträger bieten nativ kein vollständig verwaltetes Dateisystem, auf das über SMB/NFS zugegriffen werden kann. Sie müssen einen Cluster-Manager wie [Windows Server-Failovercluster](https://github.com/MicrosoftDocs/windowsserverdocs/blob/master/WindowsServerDocs/failover-clustering/failover-clustering-overview.md) (WSFC) verwenden, der Clusterknotenkommunikation und Schreibsperren verarbeitet. Zum Bereitstellen einer hoch verfügbaren Lösung für die SAP IQ-Simplex-Architektur unter Windows können Sie freigegebene Azure-Datenträger zwischen zwei Knoten verwenden, die von WSFC verwaltet werden. Die SAP IQ-Bereitstellungsarchitektur mit freigegebenen Azure-Datenträgern wird im Artikel [Bereitstellen einer SAP IQ-NLS-HA-Lösung mit freigegebenen Azure-Datenträgern unter Windows Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-shared-disk-on-windows/ba-p/2433089) erläutert.

- Azure NetApp Files

  Die SAP IQ-Bereitstellung unter Linux kann [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) als Dateisystem (NFS-Protokoll) verwenden, um als eigenständige oder hoch verfügbare Lösung installiert zu werden. Da dieses Speicherangebot nicht in allen Regionen verfügbar ist, finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/) aktuelle Informationen. Die SAP IQ-Bereitstellungsarchitektur mit Azure NetApp Files wird im Artikel [Bereitstellen einer SAP IQ-NLS-HA-Lösung mit Azure NetApp Files unter SUSE Linux Enterprise Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-netapp-files-on-suse/ba-p/1651172) erläutert.

In der folgenden Tabelle sind die Empfehlungen der einzelnen Speichertypen aufgeführt, die auf dem jeweiligen Betriebssystem basieren. 

| Speichertyp        | Windows | Linux |
| ------------------- | ------- | ----- |
| Verwaltete Azure-Datenträger | Ja     | Ja   |
| Freigegebene Azure-Datenträger  | Ja     | Nein    |
| Azure NetApp Files  | Nein      | Ja   |

### <a name="networking"></a>Netzwerk

Azure bietet eine Netzwerkinfrastruktur, die die Zuordnung aller Szenarios ermöglicht, die für SAP BW-Systeme, die SAP IQ als Near-Line-Speicher nutzen, realisiert werden können, z. B. die Verbindung mit lokalen Systemen, Systemen in verschiedenen virtuellen Netzwerken und andere. Weitere Informationen finden Sie unter [Microsoft Azure-Netzwerke für SAP-Workloads](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#microsoft-azure-networking).

## <a name="deploy-sap-iq-on-windows"></a>Bereitstellen von SAP IQ unter Windows

### <a name="server-preparation-and-installation"></a>Server: Vorbereitung und Installation

Befolgen Sie den aktuellen Leitfaden von SAP, um Server für die NLS-Implementierung mit SAP IQ unter Windows vorzubereiten. Aktuelle Informationen finden Sie im ersten von SAP veröffentlichten Leitfaden, den Sie im SAP-Hinweis [2780668 – SAP First Guidance – BW NLS Implementation with SAP IQ](https://launchpad.support.sap.com/#/notes/0002780668) (Englisch) finden. Es werden umfassende Informationen zu den Voraussetzungen für SAP BW-Systeme, IQ-Dateisystemlayout, Installation, Nachkonfiguration und BW-NLS-Integration mit IQ behandelt.

### <a name="high-availability-deployment"></a>Bereitstellung mit hoher Verfügbarkeit

SAP IQ unterstützt sowohl eine Simplex- als auch eine Multiplex-Architektur. Für die NLS-Lösung ist nur die Simplex-Serverarchitektur verfügbar und ausgewertet. Der Simplex-Architekturtyp bezeichnet eine einzelne Instanz eines SAP IQ-Servers, der auf einem einzelnen virtuellen Computer ausgeführt wird. Technisch gesehen kann SAP IQ-Hochverfügbarkeit mit der Multiplex-Serverarchitektur erreicht werden, aber die Multiplex-Architektur erfüllt nicht die Anforderung der NLS-Lösung. Für die Simplex-Serverarchitektur bietet SAP keine Features oder Verfahren zum Ausführen von SAP IQ in einer Hochverfügbarkeitskonfiguration. 

Zum Einrichten von SAP IQ-Hochverfügbarkeit unter Windows für die Simplex-Serverarchitektur müssen Sie eine benutzerdefinierte Lösung einrichten, die eine zusätzliche Konfiguration erfordert, z. B. Microsoft Windows Server-Failovercluster, freigegebener Datenträger usw. Eine solche benutzerdefinierte Lösung für SAP IQ unter Windows wird ausführlich im Blog [Bereitstellen einer SAP IQ-NLS-HA-Lösung mit freigegebenen Azure-Datenträgern unter Windows Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-shared-disk-on-windows/ba-p/2433089) beschrieben.

### <a name="back-up-and-restore"></a>Sichern und Wiederherstellen

In Azure können Sie die SAP IQ-Datenbanksicherung wie von SAP unter [IQ Administration: Backup, Restore and Data Recovery](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/5b8309b37f4e46b089465e380c24df59.html) (Englisch) beschrieben planen. SAP IQ bietet verschiedene Arten von Datenbanksicherungen; Details zu den einzelnen Sicherungstypen finden Sie unter [Backup Scenarios](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880dc1f84f21015af84f1a6b629dd7a.html) (Englisch).

- Vollständige Sicherung: Es wird eine vollständige Kopie der Datenbank erstellt. 
- Inkrementelle Sicherung: Es werden alle Transaktionen seit der letzten Sicherung eines beliebigen Typs kopiert. 
- Inkrementell seit vollständiger Sicherung: Alle seit der letzten vollständigen Sicherung vorgenommenen Änderungen an der Datenbank werden gesichert.
- Virtuelle Sicherung: Die gesamte Datenbank mit Ausnahme der Tabellen- und Metadaten wird aus dem IQ-Speicher kopiert.

Abhängig von Ihrer IQ-Datenbankgröße können Sie die Datenbanksicherung ausgehend von jedem der Sicherungsszenarios planen. Wenn Sie jedoch SAP IQ mit der NLS-Schnittstelle verwenden, die von SAP bereitgestellt wird, empfiehlt es sich, den Sicherungsprozess für die IQ-Datenbank zu automatisieren. Durch die Automatisierung wird sichergestellt, dass die SAP IQ-Datenbank immer in einem konsistenten Zustand ohne Datenverlust in Bezug auf die Datenverschiebungsprozesse zwischen der primären Datenbank und der SAP IQ-Datenbank wiederhergestellt werden kann. Weitere Informationen hierzu finden Sie im SAP-Hinweis [2741824 – How to setup backup automation for SAP IQ Cold Store/Near-line Storage](https://launchpad.support.sap.com/#/notes/2741824) (Englisch). In diesem Hinweis werden Einzelheiten zum Einrichten der Automatisierung für SAP IQ-NLS (Near-Line Storage) erläutert. 

Für eine große IQ-Datenbank können Sie die virtuelle Sicherung in SAP IQ verwenden. Weitere Informationen zur virtuellen Sicherung finden Sie unter [Virtual Backups](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880672184f21015a08dceedc7d19776.html), [Introduction Virtual Backup in SAP Sybase IQ](https://wiki.scn.sap.com/wiki/display/SYBIQ/Introduction+Virtual+BackUp+(+general++back+up+method+)+in+SAP+Sybase+IQ) und im SAP-Hinweis [2461985 – How to Backup Large SAP IQ Database](https://launchpad.support.sap.com/#/notes/0002461985) (Englisch).

Wenn Sie das Netzwerklaufwerk (SMB-Protokoll) zum Sichern und Wiederherstellen des SAP IQ-Servers unter Windows verwenden, stellen Sie sicher, dass Sie den UNC-Pfad für die Sicherung verwenden. Wenn Sie den UNC-Pfad für die Sicherung/Wiederherstellung verwenden, sind drei umgekehrte Schrägstriche (\\\\\) erforderlich.

```sql
BACKUP DATABASE FULL TO '\\\sapiq.internal.contoso.net\sapiq-backup\backup\data\<filename>'
```

## <a name="deploy-sap-iq-on-linux"></a>Bereitstellen von SAP IQ unter Linux

### <a name="server-preparation-and-installation"></a>Server: Vorbereitung und Installation

Befolgen Sie den aktuellen Leitfaden von SAP, um Server für die NLS-Implementierung mit SAP IQ unter Linux vorzubereiten. Aktuelle Informationen finden Sie im ersten von SAP veröffentlichten Leitfaden, den Sie im SAP-Hinweis [2780668 – SAP First Guidance – BW NLS Implementation with SAP IQ](https://launchpad.support.sap.com/#/notes/0002780668) (Englisch) finden. Es werden umfassende Informationen zu den Voraussetzungen für SAP BW-Systeme, IQ-Dateisystemlayout, Installation, Nachkonfiguration und BW-NLS-Integration mit IQ behandelt.

### <a name="high-availability-deployment"></a>Bereitstellung mit hoher Verfügbarkeit

SAP IQ unterstützt sowohl eine Simplex- als auch eine Multiplex-Architektur. Für die NLS-Lösung ist nur die Simplex-Serverarchitektur verfügbar und ausgewertet. Der Simplex-Architekturtyp bezeichnet eine einzelne Instanz eines SAP IQ-Servers, der auf einem einzelnen virtuellen Computer ausgeführt wird. Technisch gesehen kann SAP IQ-Hochverfügbarkeit mit der Multiplex-Serverarchitektur erreicht werden, aber die Multiplex-Architektur erfüllt nicht die Anforderung der NLS-Lösung. Für die Simplex-Serverarchitektur bietet SAP keine Features oder Verfahren zum Ausführen von SAP IQ in einer Hochverfügbarkeitskonfiguration. 

Zum Einrichten von SAP IQ-Hochverfügbarkeit unter Linux für die Simplex-Serverarchitektur müssen Sie eine benutzerdefinierte Lösung einrichten, die eine zusätzliche Konfiguration wie Pacemaker erfordert. Eine solche benutzerdefinierte Lösung für SAP IQ unter Linux wird ausführlich im Blog [Bereitstellen einer SAP IQ-NLS-HA-Lösung mit Azure NetApp Files unter SUSE Linux Enterprise Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-netapp-files-on-suse/ba-p/1651172) beschrieben.

### <a name="back-up-and-restore"></a>Sichern und Wiederherstellen

In Azure können Sie die SAP IQ-Datenbanksicherung wie von SAP unter [IQ Administration: Backup, Restore and Data Recovery](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/5b8309b37f4e46b089465e380c24df59.html) (Englisch) beschrieben planen. SAP IQ bietet verschiedene Arten von Datenbanksicherungen; Details zu den einzelnen Sicherungstypen finden Sie unter [Backup Scenarios](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880dc1f84f21015af84f1a6b629dd7a.html) (Englisch).

- Vollständige Sicherung: Es wird eine vollständige Kopie der Datenbank erstellt. 
- Inkrementelle Sicherung: Es werden alle Transaktionen seit der letzten Sicherung eines beliebigen Typs kopiert. 
- Inkrementell seit vollständiger Sicherung: Alle seit der letzten vollständigen Sicherung vorgenommenen Änderungen an der Datenbank werden gesichert.
- Virtuelle Sicherung: Die gesamte Datenbank mit Ausnahme der Tabellen- und Metadaten wird aus dem IQ-Speicher kopiert.

Je nach IQ-Datenbankgröße können Sie die Datenbanksicherung entsprechend planen. Wenn Sie jedoch SAP IQ mit der NLS-Schnittstelle verwenden, die von SAP bereitgestellt wird, empfiehlt es sich, den Sicherungsprozess für die IQ-Datenbank zu automatisieren. Durch die Automatisierung wird sichergestellt, dass die SAP IQ-Datenbank immer in einem Zustand ohne Datenverlust in Bezug auf die Datenverschiebungsprozesse zwischen der primären Datenbank und der SAP IQ-Datenbank wiederhergestellt werden kann. Weitere Informationen hierzu finden Sie im SAP-Hinweis [2741824 – How to setup backup automation for SAP IQ Cold Store/Near-line Storage](https://launchpad.support.sap.com/#/notes/2741824) (Englisch). In diesem Hinweis werden Einzelheiten zum Einrichten der Automatisierung für SAP IQ-NLS (Near-Line Storage) erläutert. 

Für eine große IQ-Datenbank können Sie die virtuelle Sicherung in SAP IQ verwenden. Weitere Informationen zur virtuellen Sicherung finden Sie unter [Virtual Backups](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880672184f21015a08dceedc7d19776.html), [Introduction Virtual Backup in SAP Sybase IQ](https://wiki.scn.sap.com/wiki/display/SYBIQ/Introduction+Virtual+BackUp+(+general++back+up+method+)+in+SAP+Sybase+IQ) und im SAP-Hinweis [2461985 – How to Backup Large SAP IQ Database](https://launchpad.support.sap.com/#/notes/0002461985) (Englisch).

## <a name="disaster-recovery"></a>Notfallwiederherstellung

In diesem Abschnitt wird die Strategie zum Bereitstellen von Notfallwiederherstellungsschutz für die SAP IQ-NLS-Lösung erläutert. Dies ist eine Ergänzung des Dokuments [Einrichten der Notfallwiederherstellung für die Bereitstellung einer SAP NetWeaver-App mit mehreren Ebenen](../../../site-recovery/site-recovery-sap.md), in dem die wichtigsten Ressourcen für einen allgemeinen Ansatz zur Notfallwiederherstellung für SAP beschrieben sind. Der in diesem Dokument beschriebene Prozess wird auf abstrakter Ebene dargestellt. Sie müssen jedoch die genauen Schritte überprüfen und Ihre Strategie für die Notfallwiederherstellung gründlich testen. 

Informationen zu SAP IQ finden Sie im SAP-Hinweis [2566083](https://launchpad.support.sap.com/#/notes/0002566083), in dem Methoden zum sicheren Implementieren einer Umgebung für die Notfallwiederherstellung beschrieben werden. In Azure können Sie auch [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) für die Notfallwiederherstellungsstrategie für SAP IQ verwenden. Die Strategie für die SAP IQ-Notfallwiederherstellung hängt von der Art der Bereitstellung in Azure ab und sollte auch mit Ihrem SAP BW-System im Einklang stehen. 

- Eigenständige Bereitstellung von SAP IQ

  Wenn Sie SAP IQ als eigenständiges System installiert haben, das keine Redundanz oder Hochverfügbarkeit auf Anwendungsebene aufweist, aber bei dem für das Unternehmen ein Setup für die Notfallwiederherstellung erforderlich ist, sind alle an den virtuellen Computer angefügten Datenträger (von Azure verwalteten Datenträger) lokal. [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) kann dazu verwendet werden, den eigenständigen virtuellen SAP IQ-Computer in der sekundären Region zu replizieren. Hierbei werden die Server und alle angefügten verwalteten Datenträger in der sekundären Region repliziert, damit Sie bei Katastrophen oder Ausfällen leicht ein Failover in Ihre replizierte Umgebung ausführen und weiterarbeiten können. Befolgen Sie die Anleitung zum [Replizieren eines virtuellen Computers in Azure](../../../site-recovery/azure-to-azure-tutorial-enable-replication.md), um mit dem Replizieren der virtuellen SAP IQ-Computer in der Azure-Region für die Notfallwiederherstellung zu beginnen. 

- SAP IQ-Bereitstellung mit Hochverfügbarkeit

  Wenn Sie SAP IQ als hoch verfügbares System installiert haben, auf dem sich IQ-Binärdateien und Datenbankdateien auf einem freigegebenen Azure-Datenträger (nur Windows) oder auf dem Netzlaufwerk wie Azure NetApp Files (nur Linux) befinden, müssen Sie ermitteln, ob Sie ein ebenso hoch verfügbares SAP IQ-System am Notfallwiederherstellungsstandort benötigen oder ob eine eigenständige SAP IQ-Instanz Ihre geschäftlichen Anforderungen erfüllt. Falls Sie eine eigenständige SAP IQ-Instanz am Notfallwiederherstellungsstandort benötigen, können Sie [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) dazu verwenden, den primären virtuellen SAP IQ-Computer in der sekundären Region zu replizieren. Dabei werden die Server und alle lokalen angefügten verwalteten Datenträger in der sekundären Region repliziert, aber es wird kein freigegebener Azure-Datenträger bzw. kein freigegebenes Netzlaufwerk wie Azure NetApp Files repliziert. Zum Kopieren von Daten von einem freigegebenen Azure-Datenträger oder Netzlaufwerk können Sie ein beliebiges Dateibasis-Kopiertool verwenden, um Daten zwischen Azure-Regionen zu replizieren. Weitere Informationen zum Kopieren eines Azure NetApp Files-Volumes in eine andere Region finden Sie unter [Häufig gestellte Fragen zu Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region).

## <a name="next-steps"></a>Nächste Schritte

- [Einrichten der Notfallwiederherstellung für die Bereitstellung einer SAP-App mit mehreren Ebenen](../../../site-recovery/site-recovery-sap.md)
- [Azure Virtual Machines – Planung und Implementierung für SAP](planning-guide.md)
- [Azure Virtual Machines – Bereitstellung für SAP](deployment-guide.md)
