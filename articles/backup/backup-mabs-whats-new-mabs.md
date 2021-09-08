---
title: Neuerungen in Microsoft Azure Backup Server
description: Microsoft Azure Backup Server bietet Ihnen erweiterte Sicherungsfunktionen für den Schutz von u.a. virtuellen Computern, Dateien, Ordnern und Workloads.
ms.topic: conceptual
ms.date: 07/27/2021
ms.openlocfilehash: 8f7c0e8709cd899b9b6a285dcd8c556763869c65
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122338875"
---
# <a name="whats-new-in-microsoft-azure-backup-server-mabs"></a>Neuerungen in Microsoft Azure Backup Server (MABS)

## <a name="whats-new-in-mabs-v3-ur2"></a>Neuerungen in MABS v3 UR2

Die Microsoft Azure Backup Server-Version 3 UR2 (MABS) unterstützt die folgenden neuen Features/Featureupdates.

Informationen zu den UR2-Problemlösungen und Installationsanweisungen finden Sie im [KB-Artikel](https://support.microsoft.com/topic/update-rollup-2-for-microsoft-azure-backup-server-v3-350de164-0ae4-459a-8acf-7777dbb7fd73).

### <a name="support-for-azure-stack-hci"></a>Unterstützung für Azure Stack HCI

Mit MABS v3 UR2 können Sie VMs in Azure Stack HCI sichern. [Weitere Informationen](./back-up-azure-stack-hyperconverged-infrastructure-virtual-machines.md)

### <a name="support-for-vmware-70"></a>Unterstützung für VMware 7.0

Mit MABS v3 UR2 können Sie VMware 7.0-VMs sichern. [Weitere Informationen](./backup-azure-backup-server-vmware.md)

### <a name="support-for-sql-server-failover-cluster-instance-fci-using-cluster-shared-volume-csv"></a>Unterstützung der SQL Server-Failoverclusterinstanz (FCI) mit freigegebenem Clustervolume (Cluster Shared Volume, CSV)

Von MABS v3 UR2 wird die SQL Server-Failoverclusterinstanz (FCI) mit freigegebenem Clustervolume (Cluster Shared Volume, CSV) unterstützt. CSV trägt zur Vereinfachung der Verwaltung Ihrer SQL Server-Instanz bei. Dadurch können Sie den zugrunde liegenden Speicher von einem beliebigen Knoten aus verwalten, da abstrahiert wird, welcher Knoten als Besitzer des Datenträgers fungiert. [Weitere Informationen](./backup-azure-sql-mabs.md)

### <a name="optimized-volume-migration"></a>Optimierte Migration von Volumes

MABS v3 UR2 unterstützt die optimierte Migration von Volumes. Mit der optimierten Migration von Volumes können Datenquellen deutlich schneller auf das neue Volume migriert werden. Im Rahmen des erweiterten Migrationsprozesses wird lediglich die aktive Sicherungskopie (das aktive Replikat) auf das neuen Volume migriert. Alle neuen Wiederherstellungspunkte werden auf dem neuen Volume erstellt, während bereits vorhandene Wiederherstellungspunkte auf dem vorhandenen Volume verbleiben und gemäß der Aufbewahrungsrichtlinie bereinigt werden. [Weitere Informationen](/system-center/dpm/volume-to-volume-migration?view=sc-dpm-2019&preserve-view=true)

### <a name="offline-backup-using-azure-data-box"></a>Offlinesicherung mit Azure Data Box

Von MABS v3 UR2 wird die Offlinesicherung mit Azure Data Box unterstützt. Durch die Integration von Microsoft Azure Data Box können Sie problemlos mehrere Terabytes an Sicherungsdaten aus einer lokalen Umgebung zu Azure Storage migrieren. Dank Azure Data Box müssen Sie keine eigenen Azure-kompatiblen Datenträger und Connectors beschaffen oder temporären Speicher als Stagingspeicherort bereitstellen. Microsoft übernimmt zudem die End-to-End-Transportlogistik, die Sie über das Azure-Portal verfolgen können. [Weitere Informationen](./offline-backup-azure-data-box-dpm-mabs.md)

## <a name="whats-new-in-mabs-v3-ur1"></a>Neuerungen in MABS V3 UR1

Microsoft Azure Backup Server (MABS) Version 3 UR1 ist das neueste Update und enthält wichtige Fehlerbehebungen sowie weitere Features und Verbesserungen. Die Liste der behobenen Fehler und die Installationsanweisungen für MABS V3 UR1 finden Sie im KB-Artikel [4534062](https://support.microsoft.com/help/4534062).

>[!NOTE]
>Die Unterstützung für den 32-Bit-Schutz-Agent ist mit MABS V3 UR1 veraltet. Informationen dazu finden Sie unter [Eingestellte Unterstützung für den 32-Bit-Schutz-Agent](#32-bit-protection-agent-deprecation).

### <a name="faster-backups-with-tiered-storage-using-ssds"></a>Schnellere Sicherungen mit mehrstufigem Speicher unter Verwendung von SSDs

Mit MABS V2 wurde [Modern Backup Storage](backup-mabs-add-storage.md) (MBS) zur Verbesserung der Speichernutzung und -leistung eingeführt. MBS verwendet ReFS als zugrunde liegendes Dateisystem und ist darauf ausgelegt, Hybridspeicher wie z. B. mehrstufigen Speicher zu nutzen.

Um die Skalierung und Leistung von MBS zu erreichen, empfiehlt sich die Verwendung eines geringen Anteils (4 % des gesamten Speichers) des Flashspeichers (SSD) mit MABS V3 UR1 als mehrstufigem Volume in Verbindung mit dem DPM-HDD-Speicher. MABS V3 UR1 mit mehrstufigem Speicher bietet um 50–70 % schnellere Sicherungen. Die Schritte zum Konfigurieren des mehrstufigen Speichers finden Sie im DPM-Artikel unter [Einrichten von MBS mit mehrstufigem Speicher](/system-center/dpm/add-storage#set-up-mbs-with-tiered-storage).

### <a name="support-for-refs-volumes"></a>Unterstützung für ReFS-Volumes

Mit MABS V3 UR1 können Sie die ReFS-Volumes und die auf den ReFS-Volumes bereitgestellten Workloads sichern. Sie können die folgenden auf den ReFS-Volumes bereitgestellten Workloads sichern:

* Betriebssystem (64 Bit): Windows Server 2019, 2016, 2012 R2, 2012
* SQL Server: SQL Server 2019, SQL Server 2017, 2016
* Exchange: Exchange 2019, 2016
* SharePoint: SharePoint 2019, 2016 mit neuestem SP

>[!NOTE]
> Sicherungen von auf einem ReFS-Volume gespeicherten virtuellen Hyper-V-Computern werden mit MABS V3 unterstützt.

>[WICHTIG] Es wurden einige Probleme bei der Sicherung deduplizierter ReFS-Volumes festgestellt. Wir arbeiten daran, diese zu beheben, und aktualisieren diesen Abschnitt, sobald eine Korrektur verfügbar ist. Bis dahin wird die Sicherung deduplizierter ReFS-Volumes von MABSv3 UR1 nicht unterstützt.

### <a name="azure-vmware-solution-protection-support"></a>Unterstützung für den Schutz von Azure VMware Solution

Mit MABS V3 UR1 können Sie nun virtuelle Computer schützen, die in [Azure VMware Solution](../azure-vmware/index.yml) bereitgestellt werden.

### <a name="vmware-parallel-backups"></a>Parallele VMware-Sicherungen

Mit MABS V3 UR1 werden die Sicherungen aller VMware-VMs innerhalb einer Schutzgruppe parallel durchgeführt. Dies führt zu 25 % schnelleren VM-Sicherungen.
Bei früheren Versionen von MABS wurden parallele Sicherungen nur für Schutzgruppen durchgeführt. Mit MABS V3 UR1 werden alle VMware-Deltareplikationsaufträge parallel ausgeführt. Standardmäßig ist die Anzahl der parallel auszuführenden Aufträge auf „8“ festgelegt. Weitere Informationen zu [parallelen VMware-Sicherungen](backup-azure-backup-server-vmware.md#vmware-parallel-backups).

### <a name="disk-exclusion-for-vmware-vm-backup"></a>Ausschluss von Datenträgern für VMware-VM-Sicherungen

Mit MABS V3 UR1 können Sie spezifische Datenträger von einer VMware-VM-Sicherung ausschließen. Weitere Informationen zum [Ausschließen von Datenträgern von VMware-VM-Sicherungen](backup-azure-backup-server-vmware.md#exclude-disk-from-vmware-vm-backup).  

### <a name="support-for-additional-layer-of-authentication-to-delete-online-backup"></a>Unterstützung für zusätzliche Authentifizierungsebene zum Löschen der Onlinesicherung

Mit MABS V3 UR1 wird eine zusätzliche Authentifizierungsebene für kritische Vorgänge hinzugefügt. Sie werden aufgefordert, eine Sicherheits-PIN einzugeben, wenn Sie Vorgänge zum **Beenden des Schutzes inklusive Löschung der Daten** ausführen.

### <a name="offline-backup-improvements"></a>Verbesserte Offlinesicherung

Mit MABS V3 UR1 wird die Offlinesicherung unter Verwendung des Azure Import/Export-Diensts verbessert. Weitere Informationen finden Sie in den [hier](./backup-azure-backup-server-import-export.md) aktualisierten Schritten.

>[!NOTE]
>Von MABS v3 UR2 wird die Offlinesicherung mit Azure Data Box unterstützt. [Weitere Informationen](./offline-backup-azure-data-box-dpm-mabs.md)

### <a name="new-cmdlet-parameter"></a>Neuer Cmdlet-Parameter

MABS V3 UR1 enthält den neuen Parameter **-CheckReplicaFragmentation**. Der neue Parameter berechnet den Fragmentierungsprozentsatz für ein Replikat und ist im Cmdlet **Copy-DPMDatasourceReplica** enthalten.

### <a name="32-bit-protection-agent-deprecation"></a>Eingestellte Unterstützung für den 32-Bit-Schutz-Agent

Mit MABS V3 UR1 wird der 32-Bit-Schutz-Agent nicht mehr unterstützt. Nach dem Upgrade des MABS V3-Servers auf UR1 können 32-Bit-Workloads nicht mehr geschützt werden. Alle vorhandenen 32-Bit-Schutz-Agents sind deaktiviert. Geplante Sicherungen werden mit dem Fehler **agent is disabled** (Agent ist deaktiviert) nicht durchgeführt. Wenn Sie die Sicherungsdaten für diese Agents beibehalten möchten, können Sie den Schutz mit der Option „Daten beibehalten“ beenden. Andernfalls kann der Schutz-Agent entfernt werden.

>[!NOTE]
>Informationen zu den unterstützten Workloads für den Schutz mit MABS UR1 finden Sie in der [aktualisierten Schutzmatrix](./backup-mabs-protection-matrix.md).

## <a name="whats-new-in-mabs-v3-rtm"></a>Neuerungen in MABS V3 RTM

Microsoft Azure Backup Server Version 3 (MABS V3) enthält wichtige Fehlerbehebungen, Windows Server 2019-Unterstützung, SQL 2017-Unterstützung sowie weitere Funktionen und Verbesserungen. Die Liste der behobenen Fehler und die Installationsanweisungen für MABS V3 finden Sie im KB-Artikel [4457852](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3).

Die folgenden Features sind in MABS V3 enthalten:

### <a name="volume-to-volume-migration"></a>Volume-zu-Volume-Migration

Mit Modern Backup Storage (MBS) in MABS V2 wurde ein workloadbewusster Speicher veröffentlicht. Bestimmte Workloads lassen sich so konfigurieren, dass sie auf einem bestimmten Speicher gesichert werden, basierend auf den Speichereigenschaften. Nach der Konfiguration ist es jedoch ggf. erforderlich, Sicherungen bestimmter Datenquellen auf einen anderen Speicher zu verschieben, um die Ressourcenverwendung zu optimieren. MABS V3 bietet Ihnen die Möglichkeit, Ihre Sicherungen zu migrieren und so zu konfigurieren, dass sie in [3 Schritten](https://techcommunity.microsoft.com/t5/system-center-blog/sc-2016-dpm-ur4-migrate-backup-storage-in-3-simple-steps/ba-p/351842) auf einem anderen Volume gespeichert werden.

### <a name="prevent-unexpected-data-loss"></a>Vermeidung unerwarteter Kosten

In Unternehmen wird MABS von einem Team von Administratoren verwaltet. Es gibt zwar Speicherrichtlinien, die für Sicherungen gelten, doch wenn MABS das falsche Volume als Sicherungsspeicher zugewiesen wird, kann dies zum Verlust wichtiger Daten führen. Mit MABS V3 können Sie das verhindern, indem Sie mit [diesen PowerShell-Cmdlets](./backup-mabs-add-storage.md) die Volumes konfigurieren, die nicht für den Speicher verfügbar sind.

### <a name="custom-size-allocation"></a>Benutzerdefinierte Größenzuteilung

Modern Backup Storage verbraucht sehr wenig Speicherplatz, nämlich nur nach Bedarf. Dazu berechnet MABS die Größe der zu sichernden Daten, wenn sie für den Schutz konfiguriert sind. Wenn viele Dateien und Ordner gemeinsam gesichert werden – wie bei einem Dateiserver – kann die Größenberechnung lange dauern. Mit MABS V3 können Sie MABS so konfigurieren, dass die Komponente Volumegröße als Standard akzeptiert, anstatt die Größe jeder Datei zu berechnen. Dadurch sparen Sie Zeit.

### <a name="optimized-cc-for-rct-vms"></a>Optimierte Konsistenzprüfungen für VMs mit RCT

MABS verwendet RCT, die systemeigene Änderungsnachverfolgung in Hyper-V, wodurch zeitaufwändige Konsistenzprüfungen etwa bei VM-Abstürzen entfallen. RCT bietet eine bessere Resilienz als die Änderungsnachverfolgung, die von Sicherungen bereitgestellt wird, die auf VSS-Momentaufnahmen basieren. MABS V3 optimiert den Netzwerk- und Speicherverbrauch außerdem, indem bei Konsistenzprüfungen nur die geänderten Daten übertragen werden.

### <a name="support-to-tls-12"></a>Unterstützung von TLS 1.2

TLS 1.2 ist die von Microsoft empfohlene Datenübertragungsmethode mit der stärksten Verschlüsselung. MABS unterstützt nun die TLS 1.2-Kommunikation zwischen MABS und den geschützten Servern, für zertifikatsbasierte Authentifizierung und für Cloudsicherungen.

### <a name="vmware-vm-protection-support"></a>Unterstützung des VMware-VM-Schutzes

Die VMware-VM-Sicherung wird nun für Produktionsbereitstellungen unterstützt. MABS V3 bietet Folgendes für den VMware-VM-Schutz:

* Unterstützung für vCenter und ESXi 6.5, 5.5 und 6.0.
* Automatischer Schutz von VMware-VMs in der Cloud. Wenn neue VMware-VMs einem geschützten Ordner hinzugefügt werden, werden sie automatisch auf dem Datenträger und in der Cloud geschützt.
* Effizientere VMware-Wiederherstellungen an einem anderen Speicherort.

### <a name="sql-2017-support"></a>Unterstützung von SQL 2017

MABS V3 kann mit SQL 2017 als MABS-Datenbank installiert werden. Sie können SQL Server von SQL 2016 auf SQL 2017 aktualisieren oder das Upgrade neu installieren. Außerdem lassen sich mit MABS V3 SQL 2017-Workloads sowohl in gruppierten als auch in nicht gruppierten Umgebungen sichern.

### <a name="windows-server-2019-support"></a>Unterstützung für Windows Server 2019

MABS V3 kann auf Windows Server 2019 installiert werden. Um MABS V3 mit Windows Server 2019 zu verwenden, aktualisieren Sie Ihr Betriebssystem vor der Installation/dem Upgrade auf MABS V3 auf Windows Server 2019 oder nach der Installation/dem Upgrade auf MABS V3 unter Windows Server 2016.

MABS V3 ist eine Vollversion und kann direkt auf Windows Server 2016, Windows Server 2019 oder einem Upgrade von MABS V2 installiert werden. Bevor Sie Azure Backup Server V3 installieren oder ein Upgrade dafür ausführen, lesen Sie die Installationsvoraussetzungen.
[Hier](./backup-azure-microsoft-azure-backup.md#software-package) finden Sie weitere Informationen zur Installation und zum Upgrade von MABS.

> [!NOTE]
>
> MABS hat die gleiche Codebasis wie System Center Data Protection Manager. MABS V3 entspricht Data Protection Manager, Version 1807. MABS V3 UR1 entspricht Data Protection Manager 2019 UR1.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie Ihren Server vorbereiten oder mit dem Schutz einer Workload beginnen:

* [Vorbereiten von Backup Server-Workloads](backup-azure-microsoft-azure-backup.md)
* [Sichern eines VMware-Servers mit Backup Server](backup-azure-backup-server-vmware.md)
* [Sichern von SQL Server mit Backup Server](backup-azure-sql-mabs.md)
* [Verwenden von Modern Backup Storage mit Backup Server](backup-mabs-add-storage.md)