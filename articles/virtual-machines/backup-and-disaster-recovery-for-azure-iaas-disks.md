---
title: Sicherung und Notfallwiederherstellung für verwaltete Datenträger auf Azure-VMs
description: In diesem Artikel wird beschrieben, wie Sie die Sicherung und Notfallwiederherstellung von IaaS-VMs und verwalteten Datenträgern in Azure planen.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 08/26/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: a2c060362a74400a1356d96fb85a4e62d20cac57
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123101798"
---
# <a name="backup-and-disaster-recovery-for-azure-managed-disks"></a>Sicherung und Notfallwiederherstellung für verwaltete Azure-Datenträger

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen

In diesem Artikel wird beschrieben, wie Sie die Sicherung und Notfallwiederherstellung für verwaltete Azure-Datenträger planen.

Zuerst werden die integrierten Fehlertoleranzfunktionen der Azure-Plattform behandelt, die Schutz vor lokalen Ausfällen bieten. Dann werden die Notfallszenarien behandelt, die durch die integrierten Funktionen nicht vollständig abgedeckt werden. Außerdem werden einige Beispiele für Workloadszenarien gezeigt, in denen möglicherweise unterschiedliche Überlegungen zu Sicherung und Notfallwiederherstellung zu berücksichtigen sind. Darüber hinaus werden einige Notfallwiederherstellungslösungen für verwaltete Datenträger vorgestellt.

## <a name="introduction"></a>Einführung

Azure nutzt verschiedene Methoden zur Bereitstellung von Redundanz und Fehlertoleranz, um Kunden vor lokalen Hardwareausfällen zu schützen. Lokale Ausfälle können beispielsweise durch Probleme mit einem Azure Storage-Servercomputer verursacht werden, die einen Teil der Daten für eine virtuelle Festplatte speichern, oder durch fehlerhafte Solid-State-Laufwerke (SSDs) oder Festplattenlaufwerke (HDDs) auf diesem Server ausgelöst werden. Vereinzelte Ausfälle von Hardwarekomponenten können während des normalen Betriebs auftreten.

Azure ist so konzipiert, dass es gegen diese Fehler resilient ist. Schwerwiegende Störungen können zu Ausfällen oder dazu führen, dass zahlreiche Speicherserver oder sogar ein gesamtes Rechenzentrum nicht erreichbar ist. Obwohl Ihre VMs und Datenträger im Normalfall vor lokalen Ausfällen geschützt sind, sind zusätzliche Schritte erforderlich, um Ihre Workload vor schwerwiegenden Störungen ganzer Regionen (z. B. im Katastrophenfall) zu schützen, die Auswirkungen auf Ihre VMs und Datenträger haben können.

Neben möglichen Ausfällen der Plattform können auch Probleme mit der Kundenanwendung oder mit Daten auftreten. Eine neue Version Ihrer Anwendung kann beispielsweise unabsichtlich zu einer Änderung der Daten führen, die sie beschädigt. In diesem Fall sollten Sie eine vorherige Version der Anwendung und der Daten wiederherstellen, die den letzten bekannten fehlerfreien Status enthält. Dies erfordert die Durchführung von regelmäßigen Sicherungsvorgängen.

Sie müssen Ihre IaaS-VM-Datenträger (Infrastructure-as-a-Service) in einer anderen Region sichern, um eine regionale Notfallwiederherstellung zu ermöglichen. 

Bevor wir uns mit den Optionen für die Sicherung und Notfallwiederherstellung befassen, werden nachfolgend einige der verfügbaren Methoden zum Umgang mit lokalen Ausfällen zusammengefasst.

### <a name="azure-iaas-resiliency"></a>Azure IaaS-Resilienz

Resilienz ist die Toleranz in Bezug auf normale Ausfälle, die für Hardwarekomponenten auftreten. Bei der Resilienz geht es um die Möglichkeit, nach Ausfällen eine Wiederherstellung durchzuführen und die Betriebsbereitschaft sicherzustellen. Es geht nicht um das Vermeiden von Ausfällen, sondern um das Reagieren auf Ausfälle, um Ausfallzeiten oder Datenverluste zu vermeiden. Das Ziel der Resilienz besteht darin, die volle Funktionsfähigkeit der Anwendung nach einem Ausfall wiederherzustellen. Azure-VMs und verwaltete Datenträger sind so konzipiert, dass sie gegen häufig auftretende Hardwareausfälle resilient sind. Wir betrachten nun, wie diese Resilienz von der Azure IaaS-Plattform bereitgestellt wird.

Eine VM besteht hauptsächlich aus zwei Teilen: einem Computeserver und den persistenten Datenträgern. Beides wirkt sich auf die Fehlertoleranz einer VM aus.

Wenn bei dem Azure-Computehostserver, auf dem sich Ihre VM-Benutzeroberflächen befinden, ein Hardwarefehler auftritt (seltener Fall), wird die VM von Azure automatisch auf einem anderen Server wiederhergestellt. In diesem Szenario startet der Computer neu, und die VM wird nach einiger Zeit wieder hochgefahren. Hardwareausfälle dieser Art werden von Azure automatisch erkannt, und es wird eine Wiederherstellung eingeleitet, damit die Kunden-VM so schnell wie möglich wieder verfügbar ist.

In Bezug auf Ihre verwalteten Datenträger ist die Dauerhaftigkeit von Daten für eine persistente Speicherplattform von entscheidender Bedeutung. Azure-Kunden nutzen wichtige Geschäftsanwendungen, die unter IaaS ausgeführt werden und von der Persistenz der Daten abhängig sind. In Azure wird der Schutz für diese IaaS-Datenträger mit drei lokal gespeicherten redundanten Kopien der Daten erreicht. Diese Kopien sorgen für hohe Widerstandsfähigkeit gegenüber lokalen Ausfällen. Falls eine der Hardwarekomponenten ausfällt, auf der sich Ihr Datenträger befindet, hat dies keine Auswirkungen auf Ihre VM. Der Grund ist, dass zwei zusätzliche Kopien zur Unterstützung von Datenträgeranforderungen vorhanden sind. Dies funktioniert auch dann gut, wenn zwei unterschiedliche Hardwarekomponenten, die einen Datenträger unterstützen, gleichzeitig ausfallen (seltener Fall). 

Damit sichergestellt ist, dass immer drei Replikate vorgehalten werden, erzeugt Azure im Hintergrund automatisch eine neue Kopie der Daten für den Fall, dass eine der drei Kopien nicht mehr verfügbar ist. Daher ist es normalerweise nicht erforderlich, für Azure-Datenträger zur Erzielung der Fehlertoleranz RAID zu verwenden. Eine einfache RAID 0-Konfiguration sollte ausreichen, um bei Bedarf für die Datenträger das Striping durchzuführen, damit größere Volumes erstellt werden können.

Aufgrund dieser Architektur konnte Azure für IaaS-Datenträger durchgängig eine Dauerhaftigkeit auf Unternehmensniveau bereitstellen, mit einer branchenweit führenden [auf das Jahr umgerechneten Fehlerrate](https://en.wikipedia.org/wiki/Annualized_failure_rate) von null (0) Prozent.

Lokale Hardwareausfälle auf dem Computehost oder auf der Speicherplattform können in bestimmten Fällen zu einer vorübergehenden Nichtverfügbarkeit der VM führen. Dies ist durch die [Azure-Vereinbarung zum Servicelevel (SLA)](https://azure.microsoft.com/support/legal/sla/virtual-machines/) für die VM-Verfügbarkeit abgedeckt. Für Azure wird auch eine branchenführende SLA für einzelne VM-Instanzen bereitgestellt, die Azure-Premium-SSD-Datenträger nutzen.

Um Anwendungsworkloads vor Ausfallzeiten aufgrund der vorübergehenden Nichtverfügbarkeit eines Datenträgers oder einer VM zu schützen, können Kunden [Verfügbarkeitsgruppen](./availability.md) nutzen. Zwei oder mehr virtuelle Computer in einer Verfügbarkeitsgruppe sorgen für die Redundanz der Anwendung. Azure erstellt diese VMs und Datenträger dann in separaten Fehlerdomänen mit unterschiedlichen Stromversorgungs-, Netzwerk- und Serverkomponenten.

Wegen dieser separaten Fehlerdomänen wirken sich lokale Hardwareausfälle in der Regel nicht gleichzeitig auf mehrere VMs in der Gruppe aus. Separate Fehlerdomänen gewährleisten Hochverfügbarkeit Ihrer Anwendung. Eine bewährte Methode besteht darin, Verfügbarkeitsgruppen zu verwenden, wenn Hochverfügbarkeit erforderlich ist.

### <a name="backup-and-disaster-recovery"></a>Backup und Notfallwiederherstellung

Wiederherstellung nach einem Notfall ist die Fähigkeit zur Wiederherstellung bei seltenen, aber gravierenden Vorfällen. Zu diesen Vorfällen gehören dauerhafte, weitreichende Störungen wie zum Beispiel ein Dienstausfall, der eine ganze Region betrifft. Die Notfallwiederherstellung umfasst die Datensicherung und -archivierung und kann auch einen manuellen Eingriff beinhalten, z.B. das Wiederherstellen einer Datenbank aus einer Sicherung.

Der integrierte Schutz der Azure-Plattform vor lokalen Ausfällen reicht für die VMs/Datenträger unter Umständen nicht aus, falls es zu größeren Katastrophen kommt, die umfassende Ausfälle nach sich ziehen. Zu diesen schwerwiegenden Ausfällen gehören Katastrophenereignisse, wenn z. B. ein Rechenzentrum von einem Wirbelsturm, einem Erdbeben oder einem Brand betroffen ist, oder wenn es zu einem weitreichenden Ausfall von Hardwareeinheiten kommt. Darüber hinaus kann es auch aufgrund von Anwendungs- oder Datenproblemen zu Ausfällen kommen.

Um Ihre IaaS-Workloads vor Ausfällen zu schützen, sollten Sie Redundanz- und Sicherungsmaßnahmen planen, damit eine Wiederherstellung möglich ist. Für die Notfallwiederherstellung sollten Sie Sicherungen an einem anderen geografischen Standort durchführen, der vom primären Standort weiter entfernt ist. Durch dieses Konzept wird sichergestellt, dass Ihre Sicherung nicht von demselben Ereignis beeinträchtigt wird, das sich ursprünglich auf die VM oder die Datenträger ausgewirkt hat. Weitere Informationen finden Sie unter [Disaster recovery for Azure applications](/azure/architecture/resiliency/disaster-recovery-azure-applications) (Notfallwiederherstellung für Azure-Anwendungen).

Für Ihre Planung der Notfallwiederherstellung können die folgenden Aspekte wichtig sein:

- Hochverfügbarkeit: die Möglichkeit, dass die Anwendung ohne signifikante Ausfallzeit in einem fehlerfreien Zustand weiterbetrieben werden kann. Mit fehlerfreier Zustand ist gemeint, dass die Anwendung reagiert und Benutzer eine Verbindung mit der Anwendung herstellen und mit ihr interagieren können. Bestimmte unternehmenskritische Anwendungen und Datenbanken müssen ggf. auch dann immer verfügbar sein, wenn es bei der Plattform zu Ausfällen kommt. Für diese Workloads müssen Sie ggf. die Redundanz für die Anwendung und für die Daten planen.

- Dauerhaftigkeit von Daten: In einigen Fällen muss vor allem sichergestellt werden, dass die Daten im Katastrophenfall nicht verloren gehen. Daher kann es erforderlich sein, dass Sie über eine Sicherung Ihrer Daten an einem anderen Standort verfügen. Für Workloads dieser Art ist unter Umständen keine vollständige Redundanz für die Anwendung erforderlich, sondern nur eine regelmäßige Sicherung der Datenträger.

## <a name="backup-and-disaster-recovery-scenarios"></a>Szenarien für Sicherung und Notfallwiederherstellung

Wir sehen uns nun einige typische Beispiele für Anwendungsworkload-Szenarien und die wichtigen Aspekte der Planung für die Notfallwiederherstellung an.

### <a name="scenario-1-major-database-solutions"></a>Szenario 1: Größere Datenbanklösungen

Angenommen, Sie verwenden einen Produktionsdatenbankserver wie SQL Server oder Oracle, der Hochverfügbarkeit unterstützen kann. Von dieser Datenbank sind wichtige Produktionsanwendungen und Benutzer abhängig. Der Notfallwiederherstellungs-Plan für dieses System muss ggf. die folgenden Anforderungen unterstützen:

- Die Daten müssen geschützt und wiederherstellbar sein.
- Der Server muss für die Nutzung verfügbar sein.

Der Notfallwiederherstellungs-Plan erfordert ggf. das Vorhalten eines Replikats der Datenbank in einer anderen Region als Sicherung. Je nach den Anforderungen für die Serververfügbarkeit und Datenwiederherstellung kann die Lösung von einem Aktiv-Aktiv- oder Aktiv-Passiv-Replikatstandort bis zu regelmäßigen Offlinesicherungen der Daten reichen. Relationale Datenbanken, z.B. SQL Server und Oracle, bieten unterschiedliche Optionen für die Replikation. Nutzen Sie für SQL Server [SQL Server AlwaysOn-Verfügbarkeitsgruppen](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server), um Hochverfügbarkeit zu erzielen.

NoSQL-Datenbanken, z.B. MongoDB, unterstützen ebenfalls [Replikate](https://docs.mongodb.com/manual/replication/), um Redundanz zu erzielen. Die Replikate für Hochverfügbarkeit werden genutzt.

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>Szenario 2: Cluster mit redundanten VMs

Stellen Sie sich eine Workload vor, die von einem Cluster mit VMs verarbeitet wird, mit denen Redundanz und ein Lastenausgleich erzielt wird. Ein Beispiel hierfür ist ein Cassandra-Cluster, der in einer Region bereitgestellt wird. Mit dieser Art von Architektur wird in der Region bereits eine hohe Redundanzebene erreicht. Um die Workload vor einem Ausfall auf regionaler Ebene zu schützen, sollten Sie erwägen, den Cluster auf zwei Regionen zu verteilen oder regelmäßig Sicherungen in einer anderen Region zu erstellen.

### <a name="scenario-3-iaas-application-workload"></a>Szenario 3: IaaS-Anwendungsworkload

Wir betrachten nun die IaaS-Anwendungsworkload. Eine solche Anwendung könnte z.B. eine typische Produktionsworkload sein, die auf einer Azure-VM ausgeführt wird. Es kann sich um einen Webserver oder Dateiserver handeln, der den Inhalt und andere Ressourcen eines Standorts enthält. Es kann sich auch um eine benutzerdefinierte Geschäftsanwendung handeln, die auf einer VM ausgeführt wird, auf der die dazugehörigen Daten, Ressourcen und der Anwendungszustand auf den VM-Datenträgern gespeichert sind. In diesem Fall muss sichergestellt werden, dass Sie regelmäßig Sicherungen erstellen. Die Sicherungshäufigkeit sollte sich nach der Art der VM-Workload richten. Wenn die Anwendung beispielsweise jeden Tag ausgeführt wird und Daten ändert, sollte einmal pro Stunde eine Sicherung erstellt werden.

Ein weiteres Beispiel ist ein Berichtsserver, der Daten aus anderen Quellen abruft und aggregierte Berichte generiert. Der Verlust dieser VM bzw. Datenträger könnte zum Verlust der Berichte führen. Es kann aber möglich sein, den Berichterstellungsvorgang erneut durchzuführen und die Ausgabe neu zu generieren. In diesem Fall haben Sie auch dann keinen Datenverlust, wenn der Berichtsserver von einem Notfall betroffen ist. Daher sollten Sie ein höheres Maß an Fehlertoleranz für den Verlust eines Teils der Daten auf dem Berichtsserver haben. Weniger häufige Sicherungen sind hierbei eine Option, um die Kosten zu reduzieren.

### <a name="scenario-4-iaas-application-data-issues"></a>Szenario 4: Datenprobleme von IaaS-Anwendungen

Datenprobleme von IaaS-Anwendungen sind eine weitere Möglichkeit. Stellen Sie sich vor, Sie würden über eine Anwendung verfügen, die wichtige Geschäftsdaten verarbeitet, verwaltet und bereitstellt, z.B. Preisinformationen. In einer neuen Version Ihrer Anwendung war ein Softwarefehler enthalten, durch den die Preise falsch berechnet und die vorhandenen Geschäftsdaten, die von der Plattform bereitgestellt wurden, beschädigt wurden. Hier besteht die beste Vorgehensweise darin, die frühere Version der Anwendung und der Daten wiederherzustellen. Erstellen Sie regelmäßige Sicherungen Ihres Systems, um dies zu ermöglichen.

## <a name="disaster-recovery-solution-azure-disk-backup"></a>Lösung für die Notfallwiederherstellung: Azure Backup 

Azure Disk Backup ist eine native, cloudbasierte Sicherungslösung, die Ihre Daten auf verwalteten Datenträgern schützt. Es handelt sich um eine einfache, sichere und kostengünstige Lösung, mit der Sie den Schutz für verwaltete Datenträger in wenigen Schritten konfigurieren können. Damit wird sichergestellt, dass Sie Ihre Daten in einem Notfallszenario wiederherstellen können.

Azure Disk Backup bietet eine schlüsselfertige Lösung, die eine Verwaltung des Lebenszyklus von Momentaufnahmen für verwaltete Datenträger durch Automatisierung einer periodischen Erstellung von Momentaufnahmen und Aufbewahrung der Momentaufnahmen für eine konfigurierte Dauer unter Verwendung der Sicherungsrichtlinie bereitstellt. Sie können die Datenträgermomentaufnahmen ohne Infrastrukturkosten und ohne die Notwendigkeit von benutzerdefinierten Skripts oder jeglichem Verwaltungsaufwand verwalten. Es handelt sich um eine absturzsichere Sicherungslösung, die eine zeitpunktgenaue Sicherung eines verwalteten Datenträgers mit inkrementellen Momentaufnahmen mit Unterstützung für mehrere Sicherungen pro Tag vornimmt. Es handelt sich außerdem um eine Lösung ohne Agent, die die Leistung der Produktionsanwendung nicht beeinträchtigt. Sie unterstützt die Sicherung und Wiederherstellung der Betriebssystem- und regulären Datenträger (einschließlich gemeinsam genutzter Datenträger), unabhängig davon, ob diese derzeit mit einer ausgeführten Azure-VM verbunden sind oder nicht.

Ausführliche Informationen zu Azure Disk Backup finden Sie unter [Übersicht über Azure Disk Backup](../backup/disk-backup-overview.md).

## <a name="alternative-solution-consistent-snapshots"></a>Alternativlösung: Konsistente Momentaufnahmen

Wenn Sie Azure Backup nicht nutzen können, können Sie Ihren eigenen Sicherungsmechanismus mithilfe von Momentaufnahmen implementieren. Es ist kompliziert, konsistente Momentaufnahmen für alle Datenträger zu erstellen, die von einer VM verwendet werden, und diese Momentaufnahmen dann in einer anderen Region zu replizieren. Aus diesem Grund ist die Nutzung des Backup-Diensts für Azure eine bessere Option als das Erstellen einer benutzerdefinierten Lösung.

Wenn Sie lokal redundanten Speicher für Datenträger nutzen, müssen Sie die Daten selbst replizieren.

Eine Momentaufnahme ist eine Darstellung eines Objekts zu einem bestimmten Zeitpunkt. Eine Momentaufnahme führt dazu, dass Kosten für die inkrementelle Größe der enthaltenen Daten berechnet werden. Weitere Informationen finden Sie unter [Erstellen einer inkrementellen Momentaufnahme für verwaltete Datenträger](disks-incremental-snapshots.md).

### <a name="create-snapshots-while-the-vm-is-running"></a>Erstellen von Momentaufnahmen während der VM-Ausführung

Sie können zwar jederzeit eine Momentaufnahme erstellen, aber wenn die VM ausgeführt wird, werden weiter Daten auf die Datenträger gestreamt. Die Momentaufnahmen enthalten ggf. Teilvorgänge, die gerade aktiv waren. Falls mehrere Datenträger beteiligt sind, wurden die Momentaufnahmen der unterschiedlichen Datenträger außerdem ggf. zu verschiedenen Zeiten erstellt. Diese Szenarien können dazu führen, dass die Momentaufnahmen unkoordiniert sind. Diese mangelnde Koordination ist besonders für Stripesetvolumes problematisch, in denen Dateien beschädigt werden können, falls während der Sicherung Änderungen vorgenommen werden.

Für den Sicherungsvorgang müssen die folgenden Schritte implementiert werden, um diese Situation zu vermeiden:

1.  Einfrieren aller Datenträger.

1.  Leeren aller ausstehenden Schreibvorgänge.

1.  [Erstellen einer inkrementellen Momentaufnahme für verwaltete Datenträger](disks-incremental-snapshots.md) für alle Datenträger.

Einige Windows-Anwendungen, z.B. SQL Server, stellen einen Mechanismus für koordinierte Sicherungen per Volumeschattenkopie-Dienst bereit, um anwendungskonsistente Sicherungen zu erstellen. Unter Linux können Sie ein Tool wie *fsfreeze* zur Koordinierung der Datenträger verwenden. Dieses Tool bietet dateikonsistente Sicherungen, aber nicht anwendungskonsistente Momentaufnahmen. Dieser Prozess ist komplex, deshalb ist es ratsam, [Azure Disk Backup](../backup/disk-backup-overview.md) oder eine Sicherungslösung eines Drittanbieters zu verwenden, bei der dieses Verfahren bereits implementiert ist.

Der obige Prozess führt zu einer Sammlung mit koordinierten Momentaufnahmen für alle VM-Datenträger und stellt eine VM-Ansicht für einen bestimmten Zeitpunkt dar. Dies ist ein Sicherungswiederherstellungspunkt für die VM. Sie können den Prozess in geplanten Intervallen wiederholen, um regelmäßige Sicherungen zu erstellen. Unter [Kopieren der Momentaufnahmen in eine andere Region](#copy-the-snapshots-to-another-region) finden Sie Schritte zum Kopieren der Momentaufnahmen zur Notfallwiederherstellung in einer anderen Region.

### <a name="create-snapshots-while-the-vm-is-offline"></a>Erstellen von Momentaufnahmen, während die VM offline ist

Eine weitere Option zum Erstellen von konsistenten Sicherungen besteht darin, die VM herunterzufahren und eine Momentaufnahme für jeden Datenträger zu erstellen. Das Erstellen von Offlinemomentaufnahmen ist einfacher als das Koordinieren von Momentaufnahmen einer zurzeit ausgeführten VM, hierbei kommt es jedoch zu einer Downtime von einigen Minuten.

### <a name="copy-the-snapshots-to-another-region"></a>Kopieren der Momentaufnahmen in eine andere Region

Die Erstellung der Momentaufnahmen allein ist für die Notfallwiederherstellung ggf. nicht ausreichend. Sie müssen die Momentaufnahmen auch in eine andere Region kopieren. Lesen Sie die Anweisungen unter [Kopieren von Sicherungen verwalteter Azure-Datenträger in eine andere Region mit der differenziellen Funktion für inkrementelle Momentaufnahmen](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).

## <a name="other-options"></a>Weitere Optionen

### <a name="sql-server"></a>SQL Server

Wenn SQL Server auf einer VM ausgeführt wird, sind eigene Funktionen zum Sichern Ihrer SQL Server-Datenbank in Azure Blob Storage oder auf einer Dateifreigabe vorhanden. Weitere Informationen finden Sie unter [Sicherung und Wiederherstellung für SQL Server auf virtuellen Azure-Computern](../azure-sql/virtual-machines/windows/azure-storage-sql-server-backup-restore-use.md). Zusätzlich zum Sichern und Wiederherstellen können [SQL Server AlwaysOn-Verfügbarkeitsgruppen](../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md) sekundäre Replikate von Datenbanken verwalten. Diese Möglichkeit reduziert die Wiederherstellungszeit nach einem Notfall erheblich.

## <a name="next-steps"></a>Nächste Schritte

Siehe [Sichern nicht verwalteter Azure-VM-Datenträger mithilfe inkrementeller Momentaufnahmen](linux/incremental-snapshots.md).

[1]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png