---
title: Azure Backup-Glossar
description: In diesem Artikel werden Begriffe vorgestellt, die bei Nutzung von Azure Backup hilfreich sind.
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: 774fc63002677aa9fbb986d40a5d8fe2b3e99f7d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131465191"
---
# <a name="azure-backup-glossary"></a>Azure Backup-Glossar

Dieses Begriffsglossar kann bei Nutzung von Azure Backup hilfreich sind.

>[!NOTE]
>
> - Begriffe, die mit dem Präfix „(Workloadspezifischer Begriff)“ gekennzeichnet sind, verweisen auf Begriffe, die nur im Zusammenhang mit einer bestimmten Teilmenge von Workloads relevant sind, die Azure Backup unterstützt.
> - Für Begriffe, die in der Dokumentation von Azure Backup häufig verwendet werden, aber auf andere Azure-Dienste verweisen, gibt es einen direkten Link zur Dokumentation des entsprechenden Azure-Diensts.

## <a name="afs-azure-file-shares"></a>AFS (Azure-Dateifreigaben)

Weitere Informationen finden Sie in der [Dokumentation zu Azure Files](../storage/files/storage-files-introduction.md).

## <a name="alternate-location-recovery"></a>Wiederherstellung an einem alternativen Speicherort

Eine Wiederherstellung ab dem Wiederherstellungspunkt an einem anderen Speicherort als dem ursprünglichen, an dem die Sicherungen erstellt wurden. Bei einer Azure-VM-Sicherung bedeutet dies, dass die VM auf einem anderen Server als dem ursprünglichen Server, auf dem die Sicherungen erstellt wurden, wiederhergestellt wird. Bei einer Sicherung mittels Azure-Dateifreigabe bedeutet dies, dass die Daten in einer anderen Dateifreigabe wiederhergestellt werden als in der gesicherten Dateifreigabe.

## <a name="application-consistent-backup"></a>Anwendungskonsistente Sicherung

(Workloadspezifischer Begriff)

Anwendungskonsistente Sicherungen erfassen Speicherinhalte und ausstehende E/A-Vorgänge. Anwendungskonsistente Momentaufnahmen verwenden einen [VSS](#vss-windows-volume-shadow-copy-service) Writer (bzw. Pre-/Post-Skripts für Linux), um die Konsistenz der Anwendungsdaten zu gewährleisten, ehe eine Sicherung erfolgt. [Weitere Informationen](backup-azure-vms-introduction.md)

## <a name="azure-resource-manager-arm-templates"></a>Azure Resource Manager-Vorlagen

Weitere Informationen finden Sie in der [Dokumentation zu Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/overview.md).

## <a name="autoprotection-for-databases"></a>Automatischer Schutz (für Datenbanken)

(Workloadspezifischer Begriff)

„Automatischer Schutz“ ist eine Funktion, mit der Sie automatisch alle Datenbanken in einer eigenständigen SQL Server-Instanz oder einer SQL Server Always On-Verfügbarkeitsgruppe schützen können. Die Funktion ermöglicht nicht nur Sicherungen der vorhandenen Datenbanken, sondern schützt auch alle künftig hinzukommenden Datenbanken.

## <a name="availability-storage-replication-types"></a>Verfügbarkeit (Speicherreplikationsarten)

Azure Backup bietet zwei Replikationsarten, um Hochverfügbarkeit Ihres Speichers und Ihrer Daten sicherzustellen:

### <a name="lrs"></a>LRS

[Lokal redundanter Speicher](../storage/common/storage-redundancy.md#locally-redundant-storage) repliziert Ihre Sicherungsdaten in einer Speicherskalierungseinheit in einem Rechenzentrum dreimal (es werden also drei Kopien Ihrer Sicherungsdaten erstellt.) Alle Kopien der Sicherungsdaten befinden sich in derselben Region. LRS ist eine kostengünstige Möglichkeit, um Sicherungsdaten vor lokalen Hardwarefehlern zu schützen.

### <a name="grs"></a>GRS

[Geografisch redundanter Speicher](../storage/common/storage-redundancy.md#geo-redundant-storage) ist die standardmäßige und empfohlene Replikationsoption. GRS repliziert Ihre Sicherungsdaten in einer sekundären Region, die mehrere hundert Kilometer vom primären Speicherort der Quelldaten entfernt ist. GRS führt zu höheren Kosten als LRS, bietet aber eine höhere Dauerhaftigkeit Ihrer Daten (auch im Falle eines regionalen Ausfalls).

>[!NOTE]
>Bei GRS-Tresoren, bei denen das Feature für regionsübergreifende Wiederherstellung aktiviert ist, wird Sicherungsspeicher von GRS in RA-GRS (Read-Access Geo-Redundant Storage, georedundanter Speicher mit Lesezugriff) geändert.

### <a name="zrs"></a>ZRS

[Zonenredundanter Speicher (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage) repliziert Ihre Sicherungsdaten in [Verfügbarkeitszonen](../availability-zones/az-overview.md#availability-zones) und gewährleistet dadurch Sicherungsdatenresidenz und -resilienz in derselben Region. Somit können Ihre kritischen Workloads, die [Datenresidenz](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) erfordern, in ZRS gesichert werden.

## <a name="azure-command-line-interface-cli"></a>Azure-Befehlszeilenschnittstelle (CLI)

Weitere Informationen finden Sie in der [Dokumentation zur Azure CLI](/cli/azure/what-is-azure-cli).

## <a name="azure-policy"></a>Azure Policy

Weitere Informationen finden Sie in der [Dokumentation zu Azure Policy](../governance/policy/overview.md).

## <a name="azure-powershell"></a>Azure PowerShell

Weitere Informationen finden Sie in der [Dokumentation zu Azure PowerShell](/powershell/azure/).

## <a name="azure-resource-manager-arm"></a>Azure Resource Manager (ARM)

Weitere Informationen finden Sie in der [Dokumentation zu Azure Resource Manager](../azure-resource-manager/management/overview.md).

## <a name="azure-disk-encryption-ade"></a>Azure Disk Encryption (ADE)

Weitere Informationen finden Sie in der [Dokumentation zu Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md).

## <a name="backend-storage--cloud-storage--backup-storage"></a>Back-End-Speicher/Cloudspeicher/Sicherungsspeicher

Der tatsächliche von einer Sicherungsinstanz verwendete Speicher. Schließt die Größe aller Aufbewahrungspunkte ein, die für die Sicherungsinstanz vorhanden sind (wie in der Richtlinie für Sicherung und Aufbewahrung definiert).

## <a name="bare-metal-backup"></a>Bare-Metal-Sicherung

Sichert Betriebssystemdateien und alle Daten auf wichtigen Volumes mit Ausnahme von Benutzerdaten. Laut Definition umfasst eine Bare-Metal-Sicherung eine Systemstatussicherung. Sie bietet Schutz, wenn ein Computer nicht gestartet werden kann und Sie alles wiederherstellen müssen. [Weitere Informationen](backup-mabs-system-state-and-bmr.md)

## <a name="backup-extensions--vm-extensions"></a>Sicherungserweiterungen/VM-Erweiterungen

(Spezifisch für Workloadtypen von Azure-VMs)

Erweiterungen für virtuelle Azure-Computer sind kleine Anwendungen, die Konfigurations- und Automatisierungsaufgaben auf virtuellen Azure-Computern nach der Bereitstellung ermöglichen. Azure Backup sichert Azure-VMs durch die Installation einer Erweiterung für den Azure-VM-Agent auf dem Computer. Azure Backup verwaltet diese Erweiterungen automatisch, sodass Benutzer sie nicht manuell aktualisieren müssen.

## <a name="backup-instance--backup-item"></a>Sicherungsinstanz/Sicherungselement

Eine Datenquelle, die in einem Tresor mit einer bestimmten Sicherungs- und Aufbewahrungsrichtlinie gesichert wird, stellt eine Sicherungsinstanz oder ein Sicherungselement dar.

## <a name="backup-rule--backup-policy"></a>Sicherungsregel/Sicherungsrichtlinie

Eine Sicherungsregel ist eine benutzerdefinierte Regel, die angibt, wann und wie oft Sicherungen einer Datenquelle erfolgen sollen. Für einige Workloadtypen bietet die Sicherungsrichtlinie auch die Möglichkeit, die für die Datenquelle geltende Methode zum Erstellen von Momentaufnahmen anzugeben (vollständig, inkrementell, differenziell). Sicherungsrichtlinien werden oft als Kombination von Sicherungs- und Aufbewahrungsregeln definiert.

## <a name="backup-vault"></a>Sicherungstresor

Eine Azure Resource Manager-Ressource des Typs *Microsoft.DataProtection/BackupVaults*. Derzeit werden Azure Backup-Tresore zum Sichern von Azure Database for PostgreSQL-Servern verwendet. [Weitere Informationen zu Azure Backup-Tresoren](backup-azure-recovery-services-vault-overview.md).

## <a name="bcdr-business-continuity-and-disaster-recovery"></a>BCDR (Business Continuity & Disaster Recovery)

BCDR umfasst eine Reihe von Prozessen, die ein Unternehmen einführen muss, um sicherzustellen, dass Apps und Workloads während geplanter und ungeplanter Ausfälle von Diensten oder Azure mit minimaler Unterbrechung des Geschäftsbetriebs verfügbar sind und betrieben werden. [Erfahren Sie mehr](https://azure.microsoft.com/solutions/backup-and-disaster-recovery/#overview) zu den verschiedenen Diensten, die Azure bietet, um Ihnen beim Entwickeln einer soliden BCDR-Strategie zu helfen.

## <a name="churn"></a>Änderung

Die prozentuale Änderung in den zu sichernden Daten zwischen zwei aufeinanderfolgenden Sicherungen. Diese kann durch Hinzufügen neuer Daten oder durch Ändern oder Löschen vorhandener Daten verursacht werden.

## <a name="crash-consistent-backup"></a>Absturzkonsistente Sicherung

(Workloadspezifischer Begriff)

Absturzkonsistente Momentaufnahmen treten in der Regel auf, wenn eine Azure-VM zum Zeitpunkt der Sicherung heruntergefahren wird. Nur die Daten, die zum Zeitpunkt der Sicherung bereits auf dem Datenträger vorhanden sind, werden erfasst und gesichert. [Weitere Informationen](backup-azure-vms-introduction.md#snapshot-consistency)

## <a name="cross-region-restore-crr"></a>Regionsübergreifende Wiederherstellung (Cross Region Restore, CRR)

Als eine der [Wiederherstellungsoptionen](backup-azure-arm-restore-vms.md#restore-options) ermöglicht die regionsübergreifende Wiederherstellung die Wiederherstellung von Sicherungselementen in einer sekundären Region, bei der es sich um eine [gekoppelte Azure-Region](../best-practices-availability-paired-regions.md#what-are-paired-regions) handelt.

## <a name="data-box"></a>Data Box

Weitere Informationen finden Sie in der [Dokumentation zu Data Box](../databox/data-box-overview.md).

## <a name="datasource"></a>Datenquelle

Eine Ressource (Azure-, Proxy- oder lokale Ressource), die für die Sicherung in Frage kommt. Beispiele: Azure-VM oder Azure-Dateifreigabe.

## <a name="dpm-data-protection-manager"></a>DPM (Data Protection Manager)

(Workloadspezifischer Begriff)

Weitere Informationen finden Sie in der [Dokumentation zu DPM](/system-center/dpm/dpm-overview).

## <a name="expressroute"></a>ExpressRoute

Weitere Informationen finden Sie in der [Dokumentation zu ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="file-system-consistent-backup"></a>Dateisystemkonsistente Sicherung

(Workloadspezifischer Begriff)

Dateisystemkonsistente Sicherungen bieten Konsistenz, indem eine Momentaufnahme aller Dateien gleichzeitig erstellt wird. [Weitere Informationen](backup-azure-vms-introduction.md#snapshot-consistency)

## <a name="frontend-storage--source-size"></a>Front-End-Speicher/Quellgröße

Die Größe der Daten, die für eine Datenquelle gesichert werden sollen. Die Front-End-Größe einer Datenquelle bestimmt deren Anzahl [geschützter Instanzen](#protected-instance).

## <a name="full-backup"></a>Vollständige Sicherung

Bei vollständigen Sicherungen wird für jede Sicherung eine Kopie der gesamten Datenquelle gespeichert.

## <a name="gfs-backup-policy"></a>Sicherungsrichtlinie nach dem Generationenprinzip

Eine Sicherungsrichtlinie nach dem Generationenprinzip ermöglicht Ihnen, zusätzlich zum Zeitplan für die tägliche Sicherung Zeitpläne für wöchentliche, monatliche und jährliche Sicherungen zu definieren. Die wöchentlichen Sicherungen werden als „Söhne“, die monatlichen als „Väter“ und die jährlichen als „Großväter“ bezeichnet. Jeder dieser Sätze von Sicherungskopien kann so konfiguriert werden, dass er für unterschiedliche Zeiträume aufbewahrt wird, sodass die Aufbewahrungsoptionen für Sicherungskopien besser angepasst werden können. Richtlinien nach dem Generationenprinzip sind nützlich, um Sicherungen über einen längeren Zeitraum mit effizienterer Speichernutzung aufzubewahren.

## <a name="iaas-vms--azure-vms"></a>IaaS-VMs/Azure-VMs

Weitere Informationen finden Sie in der [Dokumentation zu Azure-VMs](../virtual-machines/index.yml).

## <a name="incremental-backup"></a>Inkrementelle Sicherung

Bei inkrementellen Sicherungen werden nur die Blöcke gespeichert, die sich seit der letzten Sicherung geändert haben.

## <a name="instant-restore"></a>Sofortige Wiederherstellung

(Workloadspezifischer Begriff) Bei der sofortigen Wiederherstellung wird ein Computer direkt aus seiner Momentaufnahme der Sicherung und nicht aus der Kopie der Momentaufnahme im Tresor wiederhergestellt. Sofortige Wiederherstellungen sind schneller als Wiederherstellungen aus einem Tresor. Die Anzahl der verfügbaren Sofortwiederherstellungspunkte hängt von der für Momentaufnahmen konfigurierten Aufbewahrungsdauer ab. Dies gilt zurzeit nur für die Azure-VM-Sicherung.

## <a name="iops"></a>IOPS

Ein-/Ausgabevorgänge pro Sekunde.

## <a name="item-level-restore"></a>Wiederherstellung auf Elementebene

(Workloadspezifischer Begriff)

Die Wiederherstellung einzelner Dateien oder Ordner innerhalb des Computers ab dem Wiederherstellungspunkt.

## <a name="job"></a>Auftrag

Eine sicherungsbezogene Aufgabe, die von einem Benutzer oder dem Azure Backup-Dienst erstellt wird. Aufträge können entweder geplant oder bedarfsgesteuert (ad hoc) sein. Es gibt verschiedene Arten von Aufträgen: Sicherung, Wiederherstellung, Schutzkonfiguration usw. [Erfahren Sie mehr zu Aufträgen](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-backup-center).

## <a name="mabs--azure-backup-server"></a>MABS/Azure Backup Server

(Workloadspezifischer Begriff)

Mit Azure Backup Server können Sie Anwendungsworkloads wie Hyper-V-VMs, Microsoft SQL Server, SharePoint Server, Microsoft Exchange und Windows-Clients über eine zentrale Konsole schützen. Die Lösung übernimmt einen Großteil der Funktionalität zur Sicherung von Workloads von DPM, jedoch gibt es einige Unterschiede. [Weitere Informationen](backup-azure-microsoft-azure-backup.md)

## <a name="managed-disks"></a>Verwaltete Datenträger

Weitere Informationen finden Sie in der [Dokumentation zu verwalteten Datenträgern](../virtual-machines/managed-disks-overview.md).

## <a name="mars-agent"></a>MARS-Agent

(Workloadspezifischer Begriff)

Der MARS-Agent (auch als **Azure Backup-Agent** oder **Recovery Services-Agent** bekannt) wird von Azure Backup verwendet, um Daten auf lokalen Computern und in Azure-VMs in einem Recovery Services-Sicherungstresor in Azure zu sichern. [Weitere Informationen](backup-support-matrix-mars-agent.md)

## <a name="nsg-network-security-group"></a>NSG (Netzwerksicherheitsgruppe)

Weitere Informationen finden Sie in der [Dokumentation zu NSG](../virtual-network/network-security-groups-overview.md).

## <a name="offline-seeding"></a>Offlineseeding

Offlineseeding bezieht sich auf den Vorgang, bei dem die erste (vollständige) Sicherung offline übertragen wird, ohne Netzwerkbandbreite zu nutzen. Es bietet einen Mechanismus zum Kopieren von Sicherungsdaten auf physische Speichergeräte, die anschließend in ein Azure-Rechenzentrum transportiert und in einen Recovery Services-Tresor hochgeladen werden. [Weitere Informationen](offline-backup-overview.md)

## <a name="on-demand-backup--ad-hoc-backup"></a>Bedarfsgesteuerte Sicherung/Ad-hoc-Sicherung

Ein Sicherungsauftrag, der von einem Benutzer bei Bedarf einmalig ausgelöst wird und nicht auf dem Sicherungszeitplan (samt Richtlinie) basiert, der für die Ressource konfiguriert wurde.

## <a name="original-location-recovery-olr"></a>Wiederherstellung am ursprünglichen Speicherort

Eine Wiederherstellung, die vom Wiederherstellungspunkt zum Quellspeicherort, an dem Sicherungen erstellt wurden, durchgeführt und durch den im Wiederherstellungspunkt gespeicherten Zustand ersetzt wird. Bei einer Azure-VM-Sicherung bedeutet dies, dass die VM auf dem ursprünglichen Server, auf dem die Sicherungen erstellt wurden, wiederhergestellt wird. Bei einer Sicherung über eine Azure-Dateifreigabe bedeutet dies, dass die Daten in der gesicherten Dateifreigabe wiederhergestellt werden.

## <a name="passphrase"></a>Passphrase

(Workloadspezifischer Begriff)

Eine Passphrase wird zum Verschlüsseln und Entschlüsseln von Daten während der Sicherung oder Wiederherstellung Ihres lokalen Computers mithilfe des MARS-Agents nach oder aus Azure verwendet.

## <a name="private-endpoint"></a>Privater Endpunkt

Weitere Informationen finden Sie in der [Dokumentation zu privaten Endpunkten](../private-link/private-endpoint-overview.md).

## <a name="protected-instance"></a>Geschützte Instanz

Bezieht sich auf den Computer, physischen oder virtuellen Server, den Sie zum Konfigurieren der Sicherung in Azure verwenden.  Aus Sicht der **Abrechnung** ist die Anzahl der geschützten Instanzen für einen Computer abhängig von seiner Front-End-Größe. Daher kann eine einzelne Sicherungsinstanz (z. B. eine in Azure gesicherte VM) je nach deren Front-End-Größe mehreren geschützten Instanzen entsprechen. [Weitere Informationen](https://azure.microsoft.com/pricing/details/backup/)

## <a name="rbac-role-based-access-control"></a>Rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC)

Weitere Informationen finden Sie in der [Dokumentation zu RBAC](../role-based-access-control/overview.md).

## <a name="recovery-point-restore-point-retention-point--point-in-time-pit"></a>Wiederherstellungspunkt/Aufbewahrungspunkt/Zeitpunkt (Point-in-time, PIT)

Eine Kopie der ursprünglichen Daten, die gesichert wurden. Ein Aufbewahrungspunkt ist mit einem Zeitstempel verknüpft, sodass Sie damit ein Element zu einem bestimmten Zeitpunkt wiederherstellen können.

## <a name="recovery-services-vault"></a>Recovery Services-Tresor

Eine Azure Resource Manager-Ressource des Typs *Microsoft.RecoveryServices/vaults*. Derzeit werden Recovery Services-Tresore für die Sicherung der folgenden Workloads verwendet: Azure-VMs, SQL in Azure-VMs, SAP HANA in Azure-VMs und Azure-Dateifreigaben. Sie dienen auch zum Sichern lokaler Workloads mithilfe von MARS, Azure Backup Server (MABS) und System Center DPM. [Erfahren Sie mehr zu Recovery Services-Tresoren](backup-azure-recovery-services-vault-overview.md).

## <a name="resource-group"></a>Ressourcengruppe

Weitere Informationen finden Sie in der [Dokumentation zu Azure Resource Manager](../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group).

## <a name="rest-api"></a>REST-API

Weitere Informationen finden Sie in der [Dokumentation zur REST-API von Azure](/rest/api/azure/).

## <a name="retention-rule"></a>Aufbewahrungsregel

Eine benutzerdefinierte Regel, die angibt, wie lange Sicherungen aufbewahrt werden sollen.

## <a name="rpo-recovery-point-objective"></a>RPO (Recovery Point Objective)

RPO gibt den maximalen Datenverlust an, der in einem Datenverlustszenario möglich ist. Dieser Wert wird von der Sicherungshäufigkeit bestimmt.

## <a name="rto-recovery-time-objective"></a>RTO (Recovery Time Objective)

RTO gibt die maximal mögliche Zeitspanne an, in der Daten nach einem Datenverlust zum letzten verfügbaren Zeitpunkt wiederhergestellt werden können.

## <a name="scheduled-backup"></a>Geplante Sicherung

Ein Sicherungsauftrag, der automatisch durch die für das angegebene Element konfigurierte Sicherungsrichtlinie ausgelöst wird.

## <a name="secondary-region--paired-region"></a>Sekundäre Region/Gekoppelte Region

Ein Regionspaar besteht aus zwei Regionen innerhalb desselben geografischen Gebiets. Eine ist die primäre, die andere die sekundäre Region. Gekoppelte Regionen werden von einigen Azure-Diensten (einschließlich Azure Backup mit GRS-Einstellungen) verwendet, um Geschäftskontinuität sicherzustellen und vor Datenverlust zu schützen. [Weitere Informationen](../best-practices-availability-paired-regions.md)

## <a name="soft-delete"></a>Vorläufiges Löschen

Die Funktion „Vorläufiges Löschen“ schützt vor versehentlichem Löschen von Sicherungsdaten. Beim vorläufigen Löschen werden die Sicherungsdaten einen längeren Zeitraum aufbewahrt, damit das jeweilige Sicherungselement auch dann ohne Datenverlust wiederhergestellt werden kann, wenn ein böswilliger Akteur eine Sicherung löscht oder die Sicherungsdaten versehentlich gelöscht werden. [Weitere Informationen](backup-azure-security-feature-cloud.md)

## <a name="snapshot"></a>Momentaufnahme

Eine Momentaufnahme ist eine vollständige, schreibgeschützte Kopie einer virtuellen Festplatte (Virtual Hard Drive, VHD) oder einer Azure-Dateifreigabe. Informieren Sie sich ausführlicher über [Datenträger-Momentaufnahmen](../virtual-machines/windows/snapshot-copy-managed-disk.md) und [Dateimomentaufnahmen](../storage/files/storage-snapshots-files.md).

## <a name="storage-account"></a>Speicherkonto

Weitere Informationen finden Sie in der [Dokumentation zum Speicherkonto](../storage/common/storage-account-overview.md).

## <a name="subscription"></a>Subscription

Ein Azure-Abonnement ist ein logischer Container zur Bereitstellung von Ressourcen in Azure. Es enthält die Details aller Ihrer Ressourcen, z. B. virtuelle Computer (VMs), Datenbanken usw.

## <a name="system-state-backup"></a>Systemstatussicherung

(Workloadspezifischer Begriff)

Sichert Betriebssystemdateien. Diese Sicherung ermöglicht die Wiederherstellung, wenn ein Computer gestartet wird, aber Systemdateien und die Registrierung fehlen. [Weitere Informationen](backup-mabs-system-state-and-bmr.md)

## <a name="tenant"></a>Tenant

Ein Mandant ist eine Darstellung einer Organisation. Dabei handelt es sich um eine dedizierte Instanz von Azure AD, die eine Organisation oder ein App-Entwickler erhalten, wenn sie eine Beziehung mit Microsoft erstellen, wie z. B. durch eine Anmeldung bei Azure, Microsoft Intune oder Microsoft 365.

## <a name="tier"></a>Tarif

Derzeit unterstützt Azure Backup die folgenden Sicherungsspeicherebenen:

### <a name="snapshot-tier"></a>Momentaufnahmenebene

(Workloadspezifischer Begriff) In der ersten Phase der VM-Sicherung wird die erstellte Momentaufnahme zusammen mit dem Datenträger gespeichert. Diese Form von Speicher wird als Momentaufnahmenebene bezeichnet. Wiederherstellungen auf Momentaufnahmenebene erfolgen schneller (als die Wiederherstellung aus dem Tresor), da sie die Wartezeit für das Kopieren von Momentaufnahmen in den Tresor entfällt, bevor die Wiederherstellung ausgelöst wird.

### <a name="vault-standard-tier"></a>Vault-Standard-Ebene

Sicherungsdaten für alle von Azure Backup unterstützten Workloads werden in Tresoren gespeichert, die Sicherungsspeicher enthalten, eine automatisch skalierende Gruppe von Speicherkonten, die von Azure Backup verwaltet werden. Der Vault-Standard-Ebene ist eine Onlinespeicherebene, mit der Sie eine isolierte Kopie der Sicherungsdaten in einem von Microsoft verwalteten Mandanten speichern und so eine zusätzliche Sicherheitsebene schaffen können. Für Workloads, bei denen die Momentaufnahmenebene unterstützt wird, befindet sich eine Kopie der Sicherungsdaten sowohl in der Momentaufnahmenebene als auch in der Vault-Standard-Ebene. Die Vault-Standard-Ebene stellt sicher, dass die Sicherungsdaten auch dann noch verfügbar sind, wenn die zu sichernde Datenquelle gelöscht wurde oder kompromittiert ist.

## <a name="unmanaged-disk"></a>Nicht verwalteter Datenträger

Weitere Informationen finden Sie in der [Dokumentation zu nicht verwalteten Datenträgern](../storage/common/storage-disaster-recovery-guidance.md#azure-unmanaged-disks).

## <a name="vault"></a>Tresor

Eine Speicherentität in Azure, die Sicherungsdaten enthält. Außerdem handelt es sich um eine RBAC- und Abrechnungseinheit. Derzeit gibt es zwei Arten von Tresoren: Recovery Services-Tresor und Azure Backup-Tresor.

## <a name="vault-credentials"></a>Tresoranmeldeinformationen

Die Datei mit Tresoranmeldeinformationen ist ein Zertifikat, das vom Portal für jeden Tresor generiert wird. Es wird während der Registrierung eines lokalen Servers beim Tresor verwendet. [Weitere Informationen](backup-azure-dpm-introduction.md)

## <a name="vnet-virtual-network"></a>Virtuelles Netzwerk (VNET)

Weitere Informationen finden Sie in der [Dokumentation zu VNET](../virtual-network/virtual-networks-overview.md).

## <a name="vss-windows-volume-shadow-copy-service"></a>VSS (Windows-Volumeschattenkopie-Dienst)

Weitere Informationen finden Sie in der [Dokumentation zu VSS](/windows-server/storage/file-server/volume-shadow-copy-service).

## <a name="next-steps"></a>Nächste Schritte

- [Azure Backup: Übersicht](backup-overview.md)
- [Azure Backup-Architektur und -Komponenten](backup-architecture.md)