---
title: Übersicht über Azure Disk Backup
description: Erfahren Sie mehr über die Azure Disk Backup-Lösung.
ms.topic: conceptual
ms.date: 05/27/2021
ms.openlocfilehash: f1c27241e0b61cc4ae491f7bf4a4281d24cd09b6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355013"
---
# <a name="overview-of-azure-disk-backup"></a>Übersicht über Azure Disk Backup

Azure Disk Backup ist eine native, cloudbasierte Sicherungslösung, die Ihre Daten auf verwalteten Datenträgern schützt. Es handelt sich um eine einfache, sichere und kostengünstige Lösung, mit der Sie den Schutz für verwaltete Datenträger in wenigen Schritten konfigurieren können. Damit wird sichergestellt, dass Sie Ihre Daten in einem Notfallszenario wiederherstellen können.

Azure Disk Backup bietet eine schlüsselfertige Lösung, die eine Verwaltung des Lebenszyklus von Momentaufnahmen für verwaltete Datenträger durch Automatisierung einer periodischen Erstellung von Momentaufnahmen und Aufbewahrung der Momentaufnahmen für eine konfigurierte Dauer unter Verwendung der Sicherungsrichtlinie bereitstellt. Sie können die Datenträgermomentaufnahmen ohne Infrastrukturkosten und ohne die Notwendigkeit von benutzerdefinierten Skripts oder jeglichem Verwaltungsaufwand verwalten. Es handelt sich um eine absturzsichere Sicherungslösung, die eine zeitpunktgenaue Sicherung eines verwalteten Datenträgers mit [inkrementellen Momentaufnahmen](../virtual-machines/disks-incremental-snapshots.md) mit Unterstützung für mehrere Sicherungen pro Tag vornimmt. Es handelt sich außerdem um eine Lösung ohne Agent, die die Leistung der Produktionsanwendung nicht beeinträchtigt. Sie unterstützt die Sicherung und Wiederherstellung der Betriebssystem- und regulären Datenträger (einschließlich gemeinsam genutzter Datenträger), unabhängig davon, ob sie derzeit mit einem ausgeführten virtuellen Azure-Computer verbunden sind oder nicht.

Wenn Sie eine anwendungskonsistente Sicherung des virtuellen Computers einschließlich der Datenträger oder eine Option zur Wiederherstellung einer gesamten VM aus der Sicherung, zur Wiederherstellung einer Datei oder eines Ordners oder zur Wiederherstellung in einer sekundären Region benötigen, dann verwenden Sie die Lösung [Azure VM Backup](backup-azure-vms-introduction.md). Azure Backup bietet parallele Unterstützung für die Sicherung verwalteter Datenträger mithilfe von Disk Backup zusätzlich zu [Azure VM Backup](./backup-azure-vms-introduction.md). Dies ist hilfreich, wenn Sie tägliche anwendungskonsistente Sicherungen von VMs und gleichzeitig häufigere absturzkonsistente Sicherungen des Betriebssystemdatenträgers oder eines bestimmten anderen Datenträgers durchführen möchten, ohne die Leistung der Produktionsanwendung zu beeinträchtigen.

Azure Disk Backup ist in das Backup Center integriert, wodurch eine **zentralisierte einheitliche Verwaltungsumgebung** in Azure bereitgestellt wird, mit der Unternehmen Sicherungen in großem Maßstab steuern, überwachen, betreiben und analysieren können.

## <a name="key-benefits-of-disk-backup"></a>Die wichtigsten Vorteile von Disk Backup

Azure Disk Backup ist eine absturzsichere Lösung ohne Agent, die [inkrementelle Momentaufnahmen](../virtual-machines/disks-incremental-snapshots.md) verwendet und die folgenden Vorteile bietet:

- Häufigere und schnellere Sicherungen ohne Unterbrechung des virtuellen Computers.
- Hat keinen Einfluss auf die Leistung der Produktionsanwendung.
- Keine Sicherheitsbedenken, da keine benutzerdefinierten Skripte ausgeführt oder Agents installiert werden müssen.
- Eine kostengünstige Lösung zur Sicherung bestimmter Datenträger im Vergleich zur Sicherung des gesamten virtuellen Computers.

Die Azure Disk Backup-Lösung ist in den folgenden Szenarien nützlich:

- Bedarf an häufigen Sicherungen pro Tag, ohne dass die Anwendung stillsteht.
- Apps, die in einem Clusterszenario ausgeführt werden: sowohl Windows Server-Failovercluster als auch Linux-Cluster schreiben auf freigegebene Datenträger.
- Spezifischer Bedarf an einer Sicherung ohne Agent aufgrund von Sicherheits- oder Leistungsbedenken bei der Anwendung.
- Eine anwendungskonsistente Sicherung des virtuellen Computers ist nicht möglich, da Branchen-Apps keinen Volumeschattenkopie-Dienst (VSS) unterstützen.

Erwägen Sie Azure Disk Backup in Szenarien, in denen:

- Eine geschäftskritische Anwendung auf einem virtuellen Azure-Computer ausgeführt wird, für den mehrere Sicherungen pro Tag erforderlich sind, um das RPO (Recovery Point Objective) zu erreichen, ohne jedoch die Produktionsumgebung oder die Anwendungsleistung zu beeinträchtigen.
- Ihre Organisations- oder Branchenregelungen die Installation von Agents aufgrund von Sicherheitsbedenken einschränken.
- Das Ausführen von benutzerdefinierten Pre- und Post-Skripts und das Aufrufen von Skripts zum Einfrieren und Reaktivieren auf virtuellen Linux-Computern zum Erstellen anwendungskonsistenter Sicherungen die Verfügbarkeit der Produktionsworkload durch übermäßigen Mehraufwand beeinträchtigt.
- Containerisierte Anwendungen, die auf Azure Kubernetes Service (AKS-Cluster) ausgeführt werden, verwaltete Datenträger als persistenten Speicher verwenden. Heutzutage müssen Sie den verwalteten Datenträger über Automatisierungsskripts sichern, die schwer zu verwalten sind.
- Ein verwalteter Datenträger kritische Geschäftsdaten enthält, als Dateifreigabe verwendet wird oder Datenbanksicherungsdateien enthält, und Sie die Sicherungskosten optimieren möchten, indem Sie nicht in Azure VM-Backup investieren.
- Sie über viele virtuelle Linux- und Windows-Computer mit einem einzelnen Datenträger (d. h. ein virtueller Computer mit nur einem Betriebssystem-Datenträger und ohne weitere angeschlossene Datenträger) verfügen, auf dem Webserver oder zustandslose Computer gehostet werden oder der als Stagingumgebung mit Anwendungskonfigurationseinstellungen dient, und Sie eine kostengünstige Sicherungslösung zum Schutz des Betriebssystem-Datenträgers benötigen, beispielsweise, um eine schnelle bedarfsgesteuerte Sicherung auszulösen, bevor ein Upgrade oder Patching der virtuellen Maschine durchgeführt wird.
- Auf einem virtuellen Computer eine Betriebssystemkonfiguration ausgeführt wird, die von der Azure-VM-Sicherungslösung (z. B. Windows 2008 32-Bit-Server) nicht unterstützt wird.

## <a name="how-the-backup-and-restore-process-works"></a>Funktionsweise des Sicherungs- und Wiederherstellungsprozesses

- Der erste Schritt beim Konfigurieren einer Sicherung für Azure Managed Disks ist das Erstellen eines [Sicherungstresors](backup-vault-overview.md). Der Tresor bietet eine konsolidierte Ansicht der Sicherungen, die für unterschiedliche Workloads konfiguriert wurden. Azure Disk Backup unterstützt nur Sicherungen auf Betriebsebene. Das Kopieren von Sicherungen auf die Tresorspeicherebene wird nicht unterstützt. Daher gilt die Speicherredundanzeinstellung des Sicherungstresors (LRS/GRS) nicht für auf Betriebsebene gespeicherte Sicherungen.

- Erstellen Sie dann eine Sicherungsrichtlinie, mit der Sie die Sicherungshäufigkeit und die Aufbewahrungsdauer konfigurieren können.

- Um die Sicherung zu konfigurieren, wechseln Sie zum Sicherungstresor, weisen Sie eine Sicherungsrichtlinie zu, wählen Sie den verwalteten Datenträger aus, der gesichert werden muss, und geben Sie eine Ressourcengruppe an, in der die Momentaufnahmen gespeichert und verwaltet werden sollen. Azure Backup löst automatisch geplante Sicherungsaufträge aus, die entsprechend der Sicherungshäufigkeit inkrementelle Momentaufnahmen des Datenträgers erstellen. Ältere Momentaufnahmen werden entsprechend der in der Sicherungsrichtlinie festgelegten Aufbewahrungsdauer gelöscht.

- Azure Backup verwendet [inkrementelle Momentaufnahmen](../virtual-machines/disks-incremental-snapshots.md#restrictions) des verwalteten Datenträgers. Inkrementelle Momentaufnahmen sind eine kostengünstige, zeitpunktbezogene Sicherung von verwalteten Datenträgern, bei der die Delta-Änderungen am Datenträger seit der letzten Momentaufnahme berechnet werden. Diese werden immer auf dem kostengünstigsten Speicher, dem Standard-HDD-Speicher, gespeichert, unabhängig vom Speichertyp der übergeordneten Datenträger. Die erste Momentaufnahme des Datenträgers nimmt die genutzte Größe des Datenträgers ein, und die darauf folgenden inkrementellen Momentaufnahmen speichern Delta-Änderungen des Datenträgers seit der letzten Momentaufnahme. Den von Azure Backup erstellten Momentaufnahmen wird automatisch ein Tag zugewiesen, um sie eindeutig zu identifizieren. 

- Sobald Sie die Sicherung eines verwalteten Datenträgers konfigurieren, wird eine Sicherungsinstanz im Sicherungstresor erstellt. Mit der Sicherungsinstanz können Sie den Zustand von Sicherungsvorgängen feststellen, bedarfsgesteuerte Sicherungen auslösen und Wiederherstellungsvorgänge durchführen. Sie können auch die Sicherungsintegrität über mehrere Tresore und Sicherungsinstanzen hinweg mithilfe von Backup Center anzeigen, das eine Ansicht in einer zentralisierten Benutzeroberfläche bietet.

- Zum Wiederherstellen wählen Sie einfach den Wiederherstellungspunkt aus, aus dem Sie den Datenträger wiederherstellen möchten. Geben Sie die Ressourcengruppe an, in der der wiederhergestellte Datenträger aus der Momentaufnahme erstellt werden soll. Azure Backup ermöglicht eine sofortige Wiederherstellung, da die Momentaufnahmen lokal in Ihrem Abonnement gespeichert werden.

- Backup Vault verwendet die verwaltete Identität für den Zugriff auf andere Azure-Ressourcen. Zum Konfigurieren der Sicherung eines verwalteten Datenträgers und zum Wiederherstellen aus einer vergangenen Sicherung benötigt die verwaltete Identität von Backup Vault eine Reihe von Berechtigungen für den Quelldatenträger, die Momentaufnahme-Ressourcengruppe, in der Momentaufnahmen erstellt und verwaltet werden, sowie die Zielressourcengruppe, in der Sie die Sicherung wiederherstellen möchten. Sie können der verwalteten Identität mithilfe der rollenbasierten Zugriffssteuerung von Azure (Azure RBAC) Berechtigungen erteilen. Eine verwaltete Identität ist ein spezieller Dienstprinzipal, der nur zusammen mit Azure-Ressourcen verwendet werden kann. Informieren Sie sich ausführlicher über [verwaltete Identitäten](../active-directory/managed-identities-azure-resources/overview.md).

- Derzeit unterstützt Azure Disk Backup operative Sicherungen von verwalteten Datenträgern, und kopiert die Sicherungen nicht in den Speicher des Sicherungstresors. Eine ausführliche Liste der unterstützten und nicht unterstützten Szenarien und regionalen Verfügbarkeit finden Sie in der [Supportmatrix](disk-backup-support-matrix.md).

## <a name="pricing"></a>Preise

Azure Backup verwendet [inkrementelle Momentaufnahmen](../virtual-machines/disks-incremental-snapshots.md) des verwalteten Datenträgers. Inkrementelle Momentaufnahmen werden pro GiB des Speichers abgerechnet, der von den Deltaänderungen seit der letzten Momentaufnahme beansprucht wird. Wenn Sie also beispielsweise einen verwalteten Datenträger mit einer bereitgestellten Größe von 128 GiB verwenden und 100 GiB davon nutzen, wird für die erste inkrementelle Momentaufnahme nur die genutzte Größe von 100 GiB in Rechnung gestellt. Vor der Erstellung der zweiten Momentaufnahme kommen auf dem Datenträger 20 GiB Daten hinzu. Somit werden für die zweite inkrementelle Momentaufnahme nur 20 GiB in Rechnung gestellt. 

Inkrementelle Momentaufnahmen werden immer in Standardspeicher gespeichert (unabhängig vom Speichertyp der übergeordneten verwalteten Datenträger) und entsprechend der Preise für Standardspeicher abgerechnet. Inkrementelle Momentaufnahmen eines verwalteten SSD Premium-Datenträgers werden beispielsweise in Standardspeicher gespeichert. In Regionen mit ZRS-Unterstützung werden sie standardmäßig in ZRS gespeichert. Andernfalls werden sie in lokal redundantem Speicher (LRS) gespeichert. Die Preise pro GiB sind für beide Optionen (LRS und ZRS) identisch. 

Die von Azure Backup erstellten Momentaufnahmen werden in der Ressourcengruppe innerhalb Ihres Azure-Abonnements gespeichert und verursachen Gebühren für die Momentaufnahmenspeicherung. Weitere Details zu den Preisen für Momentaufnahmen finden Sie unter [Managed Disks – Preise](https://azure.microsoft.com/pricing/details/managed-disks/). Da die Momentaufnahmen nicht in den Sicherungstresor kopiert werden, erhebt Azure Backup keine Gebühr für geschützte Instanzen, und es fallen keine Kosten für den Sicherungsspeicher an. 

Im Rahmen eines Sicherungsvorgangs erstellt der Azure Backup-Dienst ein Speicherkonto in der Momentaufnahme-Ressourcengruppe, in der die Momentaufnahmen gespeichert werden. Inkrementelle Momentaufnahmen von verwalteten Datenträgern sind ARM-Ressourcen, die in der Ressourcengruppe und nicht in einem Speicherkonto erstellt werden. 

Das Speicherkonto wird verwendet, um Metadaten für jeden Wiederherstellungspunkt zu speichern. Azure Backup Dienst erstellt einen Blobcontainer pro Datenträgersicherungsinstanz. Für jeden Wiederherstellungspunkt wird ein Blockblob erstellt, um Metadateninformationen zu speichern, die den Wiederherstellungspunkt beschreiben (z. B. Abonnement, Datenträger-ID, Datenträgerattribute usw.) und kaum Speicherplatz (wenige KiBs) belegen. 

Das Speicherkonto wird als RA-GZRS erstellt, wenn die Region Zonenredundanz unterstützt. Unterstützt die Region keine Zonenredundanz, wird das Speicherkonto als RA-GRS erstellt. Falls Ihre vorhandene Richtlinie die Erstellung von Speicherkonten für das Abonnement oder die Ressourcengruppe mit GRS-Redundanz verhindert, wird das Speicherkonto als LRS erstellt. Bei dem erstellten Speicherkonto handelt es sich um ein universelles Speicherkonto (v2), bei dem Blockblobs auf der heißen Ebene im Blobcontainer gespeichert werden. Die Kosten für das Speicherkonto hängen von der Redundanz des Speicherkontos ab. Die Gebühren richten sich nach der Größe der Blockblobs. Hierbei handelt es sich allerdings um einen minimalen Betrag, da nur Metadaten (also nur wenige KiBs pro Wiederherstellungspunkt) gespeichert werden. 

Die Anzahl von Wiederherstellungspunkten wird durch die Sicherungsrichtlinie bestimmt, die zum Konfigurieren von Sicherungen der Datenträgersicherungsinstanzen verwendet wird. Ältere Blockblobs werden gemäß dem Garbage Collection-Prozess gelöscht, wenn die entsprechenden älteren Wiederherstellungspunkte gelöscht werden.

## <a name="next-steps"></a>Nächste Schritte

[Supportmatrix für die Azure Disk-Sicherung](disk-backup-support-matrix.md)
