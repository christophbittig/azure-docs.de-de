---
title: Neuerungen in Azure Backup
description: Erfahren Sie mehr über die neuen Features in Azure Backup.
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: 78d6b8cee1ad2442278497c5ca3e282b19d1beb6
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997246"
---
# <a name="whats-new-in-azure-backup"></a>Neuerungen in Azure Backup

Azure Backup wird fortlaufend verbessert und um neue Features ergänzt, die den Schutz Ihrer Daten in Azure optimieren. Diese neuen Features erweitern den Datenschutz auf neue Workloadtypen, erhöhen die Sicherheit und verbessern die Verfügbarkeit der Sicherungsdaten. Außerdem werden neue Verwaltungs-, Überwachungs- und Automatisierungsfunktionen hinzugefügt.

Weitere Informationen zu den neuen Releases erhalten Sie, indem Sie diese Seite als Lesezeichen speichern oder [hier Updates abonnieren](https://azure.microsoft.com/updates/?query=backup).

## <a name="updates-summary"></a>Übersicht über Updates

- Oktober 2021
  - [Mehrere Sicherungen pro Tag für Azure Files (in der Vorschau)](#multiple-backups-per-day-for-azure-files-in-preview)
  - [Azure Backup Metrics und Metrics Alerts (in der Vorschau)](#azure-backup-metrics-and-metrics-alerts-in-preview)
- Juli 2021
  - [Unterstützung der Archivebene für SQL Server in Azure VM for Azure Backup jetzt allgemein verfügbar](#archive-tier-support-for-sql-server-in-azure-vm-for-azure-backup-is-now-generally-available)
- Mai 2021
  - [Sicherung für Azure-Blobs ist jetzt allgemein verfügbar](#backup-for-azure-blobs-is-now-generally-available)
- April 2021
  - [Verbesserungen bei der Verschlüsselung mit kundenseitig verwalteten Schlüsseln für Azure Backup (in der Vorschau)](#enhancements-to-encryption-using-customer-managed-keys-for-azure-backup-in-preview)
- März 2021
  - [Azure Disk Backup ist nun allgemein verfügbar](#azure-disk-backup-is-now-generally-available)
  - [Backup Center ist nun allgemein verfügbar](#backup-center-is-now-generally-available)
  - [Unterstützung der Zugriffsebene „Archiv“ für Azure Backup (Vorschau)](#archive-tier-support-for-azure-backup-in-preview)
- Februar 2021
  - [Sicherung für Azure-Blobs (Vorschau)](#backup-for-azure-blobs-in-preview)
- Januar 2021
  - [Azure Disk Backup (Vorschau)](#azure-disk-backup-in-preview)
  - [Verschlüsselung ruhender Daten mit von Kunden verwalteten Schlüsseln (allgemeine Verfügbarkeit)](#encryption-at-rest-using-customer-managed-keys)
- November 2020
  - [Azure Resource Manager-Vorlage für die Sicherung der Azure-Dateifreigabe](#azure-resource-manager-template-for-afs-backup)
  - [Inkrementelle Sicherungen für SAP HANA-Datenbanken auf Azure-VMs (in der Vorschau)](#incremental-backups-for-sap-hana-databases-in-preview)
- September 2020
  - [Backup Center (in der Vorschau)](#backup-center-in-preview)
  - [Sichern von Azure Database for PostgreSQL (in der Vorschau)](#back-up-azure-database-for-postgresql-in-preview)
  - [Selektive Datenträgersicherung und -wiederherstellung](#selective-disk-backup-and-restore)
  - [Regionsübergreifende Wiederherstellung für SQL Server- und SAP HANA-Datenbanken auf Azure-VMs (in der Vorschau)](#cross-region-restore-for-sql-server-and-sap-hana-in-preview)
  - [Unterstützung für die Sicherung von VMs mit bis zu 32 Datenträgern (allgemeine Verfügbarkeit)](#support-for-backup-of-vms-with-up-to-32-disks)
  - [Vereinfachte Konfiguration von Sicherungen für SQL auf Azure-VMs](#simpler-backup-configuration-for-sql-in-azure-vms)
  - [Sichern von SAP HANA in Azure Virtual Machines unter RHEL (in der Vorschau)](#back-up-sap-hana-in-rhel-azure-virtual-machines-in-preview)
  - [Zonenredundanter Speicher (ZRS) für Sicherungsdaten (in der Vorschau)](#zone-redundant-storage-zrs-for-backup-data-in-preview)
  - [Vorläufiges Löschen für SQL Server- und SAP HANA-Workloads auf Azure-VMs](#soft-delete-for-sql-server-and-sap-hana-workloads)

## <a name="multiple-backups-per-day-for-azure-files-in-preview"></a>Mehrere Sicherungen pro Tag für Azure Files (in der Vorschau)

Ein niedriges RPO (Recovery Point Objective) ist eine wichtige Anforderung für Azure Files, das die häufig aktualisierten, unternehmenskritischen Daten enthält. Um minimalen Datenverlust im Falle eines Notfalls oder unerwünschter Änderungen an Dateifreigabeinhalten zu gewährleisten, sollten Sie Sicherungen häufiger als einmal täglich erstellen.

Mit Azure Backup können Sie jetzt eine Sicherungsrichtlinie erstellen oder eine vorhandene Sicherungsrichtlinie ändern, um mehrere Momentaufnahmen an einem Tag zu erstellen. Mit dieser Funktion können Sie auch die Dauer definieren, in der Ihre Sicherungsaufträge ausgelöst werden. Mit dieser Funktion können Sie Ihren Sicherungszeitplan an den Arbeitszeiten ausrichten, in denen Azure Files-Inhalte häufig aktualisiert werden.

Weitere Informationen finden Sie unter [Konfigurieren mehrerer Sicherungen pro Tag über die Sicherungsrichtlinie](/azure/backup/manage-afs-backup#create-a-new-policy).

## <a name="azure-backup-metrics-and-metrics-alerts-in-preview"></a>Azure Backup-Metriken und Metrik-Warnungen (in der Vorschau)

Azure Backup bietet jetzt über [Azure Monitor](/azure/azure-monitor/essentials/data-platform-metrics) eine Reihe von integrierten Metriken, mit denen Sie den Zustand Ihrer Backups überwachen können. Sie können auch Warnregeln konfigurieren, die Warnungen auslösen, wenn die Metriken die festgelegten Schwellenwerte überschreiten.

Azure Backup bietet die folgenden Hauptfunktionen:
 
- Die Möglichkeit, sofort einsatzbereite Metriken in Bezug auf den Sicherungs- und Wiederherstellungszustand Ihrer Sicherungselemente sowie die damit verbundenen Trends anzuzeigen.
- Die Möglichkeit, benutzerdefinierte Warnregeln für diese Metriken zu erstellen, um den Zustand Ihrer Sicherungselemente effizient zu überwachen.
- Möglichkeit, ausgelöste Metrikwarnungen an verschiedene von Azure Monitor unterstützte Benachrichtigungskanäle weiterzuleiten, wie z. B. E-Mail, ITSM, Webhook, Logik-Apps und so weiter.
 
Derzeit unterstützt Azure Backup integrierte Metriken für die folgenden Workload-Typen:

- Azure VM
- SQL-Datenbanken in Azure VM
- SAP HANA-Datenbanken in Azure VM
- Azure-Dateien.

Weitere Einzelheiten finden Sie unter [Überwachen Sie den Zustand Ihrer Backups mit Azure Backup Metrics (Vorschau)](metrics-overview.md).

## <a name="archive-tier-support-for-sql-server-in-azure-vm-for-azure-backup-is-now-generally-available"></a>Unterstützung der Archivebene für SQL Server in Azure VM for Azure Backup jetzt allgemein verfügbar

Mit Azure Backup können Sie Ihre langfristigen Aufbewahrungspunkte für Azure Virtual Machines und SQL Server in Azure Virtual Machines auf die kostengünstige Archivebene verschieben. Sie können auch von den Wiederherstellungspunkten der Tresorarchivebene wiederherstellen.

Zusätzlich zur Möglichkeit, die Wiederherstellungspunkte zu verschieben:

- Azure Backup bietet Empfehlungen zum Verschieben einer bestimmten Gruppe von Wiederherstellungspunkten für Azure Virtual Machine-Sicherungen, die Kosteneinsparungen ermöglichen.
- Mithilfe von Beispielskripts können Sie alle Wiederherstellungspunkte für eine bestimmte Sicherung gleichzeitig verschieben.
- Sie können die Nutzung des Archivspeichers auf dem Tresordashboard anzeigen.

Weitere Informationen finden Sie unter [Unterstützung der Zugriffsebene „Archiv“](./archive-tier-support.md).

## <a name="backup-for-azure-blobs-is-now-generally-available"></a>Sicherung für Azure-Blobs ist jetzt allgemein verfügbar

Die operative Sicherung für Azure Blobs ist eine verwaltete Datensicherungslösung, mit der Sie Ihre Blockblobdaten vor verschiedenen Datenverlustszenarien wie Beschädigungen, Bloblöschungen und dem versehentlichen Löschen von Speicherkonten schützen können.

Da es sich um eine operative Sicherungslösung handelt, werden die Sicherungsdaten lokal im Quellspeicherkonto gespeichert und können von einem ausgewählten Zeitpunkt wiederhergestellt werden, was eine einfache und kostengünstige Möglichkeit zum Schutz Ihrer Blobdaten darstellt. Dazu nutzt die Lösung die Blobspeicherfunktion zur Zeitpunktwiederherstellung.

Die operative Sicherung für Blobs ist in Azure Backup-Verwaltungstools integriert, einschließlich Backup Center, um Sie bei der Verwaltung des Schutzes Ihrer Blobdaten effektiv und in großem Umfang zu unterstützen. Zusätzlich zu den zuvor verfügbaren Funktionen können Sie jetzt operative Sicherungen für Blobs mithilfe der Ansicht **Datenschutz** der Speicherkonten (auch [über PowerShell](backup-blobs-storage-account-ps.md)) konfigurieren und verwalten. Darüber hinaus bietet Azure Backup jetzt eine erweiterte Benutzeroberfläche für die Verwaltung von Rollenzuweisungen, die zum Konfigurieren operativer Sicherungen erforderlich sind.

Weitere Informationen finden Sie unter [Übersicht über operative Sicherungen für Azure-Blobs](blob-backup-overview.md).

## <a name="azure-disk-backup-is-now-generally-available"></a>Azure Disk Backup ist nun allgemein verfügbar

Azure Backup bietet für Azure Managed Disks eine Lebenszyklusverwaltung für Momentaufnahmen durch eine automatisierte regelmäßige Erstellung von Momentaufnahmen und deren Aufbewahrung für konfigurierte Zeiträume gemäß einer Sicherungsrichtlinie.

Weitere Informationen finden Sie unter [Übersicht über Azure Disk Backup](disk-backup-overview.md).

## <a name="backup-center-is-now-generally-available"></a>Backup Center ist nun allgemein verfügbar

Mit Backup Center können Sie den Schutz umfangreicher Datenmengen jetzt einfacher verwalten, weil Sie die Sicherungsverwaltung über eine einzige zentrale Konsole ermitteln, steuern, überwachen, betreiben und optimieren können.

Weitere Informationen finden Sie in der [Übersicht über Backup Center](backup-center-overview.md).

## <a name="archive-tier-support-for-azure-backup-in-preview"></a>Unterstützung der Zugriffsebene „Archiv“ für Azure Backup (Vorschau)

Durch die Verfügbarkeit der Zugriffsebene „Archiv“ für virtuelle Azure-Computer und SQL Server auf virtuellen Azure-Computern können Sie in Azure Backup jetzt die Kosten für Sicherungen mit langfristiger Aufbewahrung verringern.

Weitere Informationen finden Sie unter [Unterstützung der Zugriffsebene „Archiv“ (Vorschau)](archive-tier-support.md).

## <a name="backup-for-azure-blobs-in-preview"></a>Sicherung für Azure-Blobs (Vorschau)

Die operative Sicherung für Blobs ist eine verwaltete, lokale Datensicherungslösung, mit der Sie Ihre Blockblobs vor verschiedenen Datenverlustszenarien wie Beschädigungen, Bloblöschungen und dem versehentlichen Löschen von Speicherkonten schützen können. Die Daten werden lokal im Quellspeicherkonto selbst gespeichert und können bei Bedarf zu einem ausgewählten Zeitpunkt wiederhergestellt werden. Die operative Sicherung bietet also eine einfache, sichere und kostengünstige Möglichkeit, Ihre Blobs zu schützen.

Die operative Sicherung für Blobs lässt sich neben anderen Sicherungsverwaltungsfunktionen in Backup Center integrieren und bietet so eine zentralisierte Benutzeroberfläche, mit der Sie Sicherungen im großen Maßstab steuern, überwachen, betreiben und analysieren können.

Weitere Informationen finden Sie unter [Übersicht über operative Sicherungen für Azure-Blobs (Vorschau)](blob-backup-overview.md).

## <a name="azure-disk-backup-in-preview"></a>Azure Disk Backup (Vorschau)

Azure Disk Backup bietet eine schlüsselfertige Lösung, die eine Verwaltung des Lebenszyklus von Momentaufnahmen für [Azure Managed Disks](../virtual-machines/managed-disks-overview.md) durch Automatisierung einer periodischen Erstellung von Momentaufnahmen und Aufbewahrung der Momentaufnahmen für eine konfigurierte Dauer unter Verwendung der Sicherungsrichtlinie bereitstellt. Sie können die Datenträgermomentaufnahmen ohne Infrastrukturkosten und ohne die Notwendigkeit von benutzerdefinierten Skripts oder jeglichem Verwaltungsaufwand verwalten. Es handelt sich um eine absturzsichere Sicherungslösung, die eine zeitpunktgenaue Sicherung eines verwalteten Datenträgers mit [inkrementellen Momentaufnahmen](../virtual-machines/disks-incremental-snapshots.md) mit Unterstützung für mehrere Sicherungen pro Tag vornimmt. Es handelt sich außerdem um eine Lösung ohne Agent, die die Leistung der Produktionsanwendung nicht beeinträchtigt. Sie unterstützt die Sicherung und Wiederherstellung der Betriebssystem- und regulären Datenträger (einschließlich gemeinsam genutzter Datenträger), unabhängig davon, ob sie derzeit mit einem ausgeführten virtuellen Azure-Computer verbunden sind oder nicht.

Weitere Informationen finden Sie unter [Azure Disk Backup (Vorschau)](disk-backup-overview.md).

## <a name="encryption-at-rest-using-customer-managed-keys"></a>Verschlüsselung im Ruhezustand mithilfe von Kunden verwalteter Schlüssel

Unterstützung für die Verschlüsselung ruhender Daten mit von Kunden verwalteten Schlüsseln ist jetzt allgemein verfügbar. Dies bietet Ihnen die Möglichkeit, die Sicherungsdaten in Ihren Recovery Services-Tresoren mit Ihren eigenen, in Azure Key Vaults gespeicherten Schlüsseln zu verschlüsseln. Der Verschlüsselungsschlüssel, der zum Verschlüsseln von Sicherungen im Recovery Services-Tresor verwendet wird, kann sich von den für die Verschlüsselung der Quelle verwendeten Verschlüsselungsschlüssel unterscheiden. Die Daten werden mithilfe eines AES-256 basierten Datenverschlüsselungsschlüssels (DEK) geschützt, der wiederum mit Ihren im Schlüsseltresor gespeicherten Schlüsseln geschützt wird. Im Vergleich zur Verschlüsselung mit plattformverwalteten Schlüsseln (die standardmäßig verfügbar ist), haben Sie so mehr Kontrolle über Ihre Schlüssel und können Ihre Complianceanforderungen besser erfüllen.

Weitere Informationen finden Sie unter [Verschlüsselung von Sicherungsdaten mit von Kunden verwalteten Schlüsseln](encryption-at-rest-with-cmk.md).

## <a name="azure-resource-manager-template-for-afs-backup"></a>Azure Resource Manager-Vorlage für die Sicherung der Azure-Dateifreigabe

Azure Backup unterstützt jetzt das Konfigurieren von Sicherungen für bestehende Azure-Dateifreigaben mithilfe einer Azure Resource Manager-Vorlage (ARM-Vorlage). Mit der Vorlage wird der Schutz für eine vorhandene Azure-Dateifreigabe konfiguriert, indem entsprechende Details für den Recovery Services-Tresor und die Sicherungsrichtlinie angegeben werden. Optional erstellt sie einen neuen Recovery Services-Tresor und eine neue Sicherungsrichtlinie und registriert das Speicherkonto, das die Dateifreigabe enthält, im Recovery Services-Tresor.

Weitere Informationen finden Sie unter [Azure Resource Manager-Vorlagen für Azure Backup](backup-rm-template-samples.md).

## <a name="incremental-backups-for-sap-hana-databases-in-preview"></a>Inkrementelle Sicherungen für SAP HANA-Datenbanken (in der Vorschau)

Azure Backup unterstützt jetzt inkrementelle Sicherungen für SAP HANA-Datenbanken, die auf Azure-VMs gehostet werden. Dies ermöglicht schnellere und kostengünstigere Sicherungen Ihrer SAP HANA-Daten.

Weitere Informationen finden Sie unter den [verschiedenen Optionen, die während der Erstellung einer Sicherungsrichtlinie verfügbar sind](./sap-hana-faq-backup-azure-vm.yml), und unter [Erstellen einer Sicherungsrichtlinie für SAP HANA-Datenbanken](tutorial-backup-sap-hana-db.md#creating-a-backup-policy).

## <a name="backup-center-in-preview"></a>Backup Center (in der Vorschau)

Azure Backup bietet eine neue native Verwaltungsfunktion, mit der Sie die gesamte Sicherungsumgebung über eine zentrale Konsole verwalten können. Backup Center bietet Ihnen die Möglichkeit, den Schutz von Daten im großen Stil und auf einheitliche Weise zu überwachen, auszuführen, zu steuern und zu optimieren, und zwar konsistent mit den nativen Verwaltungsfunktionen von Azure.

Mit Backup Center erhalten Sie eine aggregierte Ansicht Ihres Bestands über Abonnements, Standorte, Ressourcengruppen, Tresore und mit Azure Lighthouse sogar über Mandanten hinweg. Backup Center ermöglicht auch das zentrale Auslösen von Sicherungsaktivitäten, z. B. das Konfigurieren von Sicherung und Wiederherstellung oder dem Erstellen von Richtlinien und Tresoren. Außerdem können Sie dank der nahtlosen Integration mit Azure Policy jetzt Ihre Umgebung steuern und die Compliance aus Sicherungsperspektive verfolgen. Integrierte Azure-Richtlinien, die speziell für Azure Backup gelten, ermöglichen ebenfalls das Konfigurieren von Sicherungen im großen Stil.

Weitere Informationen finden Sie in der [Übersicht über Backup Center](backup-center-overview.md).

## <a name="back-up-azure-database-for-postgresql-in-preview"></a>Sichern von Azure Database for PostgreSQL (in der Vorschau)

Azure Backup und die Azure-Datenbankdienste wurden zusammengeführt, um eine Sicherungslösung für Unternehmen mit Azure PostgreSQL zu entwickeln (jetzt in der Vorschauphase). Jetzt können Sie Ihre Datenschutz- und Complianceanforderungen mit einer vom Kunden gesteuerten Sicherungsrichtlinie erfüllen, die die Beibehaltung von Sicherungen für bis zu 10 Jahre ermöglicht. Damit können Sie die Sicherungs- und Wiederherstellungsvorgänge auf der Ebene der einzelnen Datenbanken präzise steuern. Ebenso können Sie problemlos Wiederherstellungen verschiedener PostgreSQL-Versionen oder Wiederherstellungen in Blob Storage durchführen.

Weitere Informationen finden Sie unter [Sicherung von Azure Database for PostgreSQL](backup-azure-database-postgresql.md).

## <a name="selective-disk-backup-and-restore"></a>Selektive Datenträgersicherung und -wiederherstellung

Azure Backup unterstützt die Sicherung aller Datenträger (Betriebssystem und Daten) einer VM mithilfe der VM-Sicherungslösung. Mithilfe der Funktionalität der selektiven Datenträgersicherung und -wiederherstellung können Sie nun eine Teilmenge der Datenträger auf einem virtuellen Computer sichern. Dies stellt eine effiziente und kostengünstige Lösung für Ihre Sicherungs- und Wiederherstellungsanforderungen dar. Jeder Wiederherstellungspunkt enthält nur die Datenträger, die im Sicherungsvorgang enthalten sind.

Weitere Informationen finden Sie unter [Selektives Sichern und Wiederherstellen von Datenträgern auf Azure-VMs](selective-disk-backup-restore.md).

## <a name="cross-region-restore-for-sql-server-and-sap-hana-in-preview"></a>Regionsübergreifende Wiederherstellung für SQL Server und SAP HANA (in der Vorschau)

Mit der Einführung der regionsübergreifenden Wiederherstellung können Sie nun beliebig Wiederherstellungen in einer sekundären Region initiieren. So können Sie tatsächliche Probleme aufgrund von Ausfällen in einer primären Region für Ihre Umgebung verringern. Dadurch werden die Wiederherstellungen in der sekundären Region vollständig vom Kunden gesteuert. Azure Backup verwendet für solche Wiederherstellungen die gesicherten Daten, die in die sekundäre Region repliziert wurden.

Zusätzlich zur Unterstützung regionsübergreifender Wiederherstellungen für Azure-VMs wurde das Feature auch erweitert, sodass nun auch SQL- und SAP HANA-Datenbanken auf Azure-VMs wiederhergestellt werden können.

Weitere Informationen finden Sie unter [Regionsübergreifende Wiederherstellung für SQL-Datenbanken](restore-sql-database-azure-vm.md#cross-region-restore) und [Regionsübergreifende Wiederherstellung für SAP HANA-Datenbanken](sap-hana-db-restore.md#cross-region-restore).

## <a name="support-for-backup-of-vms-with-up-to-32-disks"></a>Unterstützung für die Sicherung von VMs mit bis zu 32 Datenträgern

Bisher unterstützte Azure Backup 16 verwaltete Datenträger pro VM. Nun unterstützt Azure Backup die Sicherung von bis zu 32 verwalteten Datenträgern pro VM.

Weitere Informationen finden Sie in der [Unterstützungsmatrix für VM-Speicher](backup-support-matrix-iaas.md#vm-storage-support).

## <a name="simpler-backup-configuration-for-sql-in-azure-vms"></a>Einfachere Sicherungskonfiguration für SQL auf Azure-VMs

Das Konfigurieren von Sicherungen für Ihre SQL Server-Instanzen auf Azure-VMs ist jetzt noch einfacher, da die Konfiguration von Inlinesicherungen in den VM-Bereich des Azure-Portals integriert wurde. In nur wenigen Schritten können Sie die Sicherung Ihrer SQL Server-Instanzen aktivieren, um alle vorhandenen Datenbanken sowie die in Zukunft hinzugefügten Datenbanken zu schützen.

Weitere Informationen finden Sie unter [Sichern einer SQL Server-Instanz über den VM-Bereich](backup-sql-server-vm-from-vm-pane.md).

## <a name="back-up-sap-hana-in-rhel-azure-virtual-machines-in-preview"></a>Sichern von SAP HANA in Azure Virtual Machines unter RHEL (in der Vorschau)

Azure Backup ist die native Sicherungslösung für Azure und von SAP als BackInt zertifiziert. Azure Backup bietet jetzt Unterstützung für Red Hat Enterprise Linux (RHEL), eines der am häufigsten verwendeten Linux-Betriebssysteme, auf denen SAP HANA ausgeführt wird.

Weitere Informationen finden Sie in der [Unterstützungsmatrix für die Sicherung von SAP HANA-Datenbanken auf virtuellen Azure-Computern](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="zone-redundant-storage-zrs-for-backup-data-in-preview"></a>Zonenredundanter Speicher (ZRS) für Sicherungsdaten (in der Vorschau)

Azure Storage bietet über verschiedene Redundanzoptionen eine hervorragende Balance zwischen hoher Leistung, Hochverfügbarkeit und hoher Datenresilienz. Mit Azure Backup können Sie diese Vorteile auch auf die Sicherungsdaten ausdehnen. Sie haben die Möglichkeit, Ihre Sicherungen in lokal redundantem Speicher (LRS) und georedundantem Speicher (GRS) zu speichern. Darüber hinaus gibt es nun zusätzliche Dauerhaftigkeitsoptionen mit zusätzlicher Unterstützung für zonenredundanten Speicher (ZRS).

Weitere Informationen finden Sie unter [Festlegen der Speicherredundanz für den Recovery Services-Tresor](backup-create-rs-vault.md#set-storage-redundancy).

## <a name="soft-delete-for-sql-server-and-sap-hana-workloads"></a>Vorläufiges Löschen für SQL Server- und SAP HANA-Workloads

Die Sorgen bezüglich Sicherheitsproblemen wie Schadsoftware, Ransomware und Eindringlingen werden immer größer. Diese Sicherheitsprobleme können erhebliche Daten- und finanzielle Verluste mit sich bringen. Zum Schutz gegen solche Angriffe verfügt Azure Backup über Sicherheitsfeatures für den Schutz von Sicherungsdaten auch nach dem Löschen.

Eines dieser Features ist das vorläufige Löschen. Beim vorläufigen Löschen werden die Sicherungsdaten 14 Tage länger aufbewahrt, damit das jeweilige Sicherungselement auch dann ohne Datenverluste wiederhergestellt werden kann, wenn ein böswilliger Akteur eine Sicherung löscht oder die Sicherungsdaten versehentlich gelöscht werden. Für die zusätzlichen 14 Tage der Aufbewahrung von Sicherungsdaten mit dem Status „Vorläufiges Löschen“ fallen für Sie keine Kosten an.

Zusätzlich zur Unterstützung für vorläufiges Löschen von Azure-VMs werden nun auch SQL Server- und SAP HANA-Workloads auf Azure-VMs durch vorläufiges Löschen geschützt.

Weitere Informationen finden Sie unter [Vorläufiges Löschen für SQL Server auf Azure-VMs und SAP HANA in Azure-VM-Workloads](soft-delete-sql-saphana-in-azure-vm.md).

## <a name="enhancements-to-encryption-using-customer-managed-keys-for-azure-backup-in-preview"></a>Verbesserungen bei der Verschlüsselung mit kundenseitig verwalteten Schlüsseln für Azure Backup (in der Vorschau)

Azure Backup bietet jetzt erweiterte Funktionen (in der Vorschau) zur Verwaltung der Verschlüsselung mit kundenseitig verwalteten Schlüsseln. In Azure Backup können Sie Ihre eigenen Schlüssel zum Verschlüsseln der Sicherungsdaten in den Recovery Services-Tresoren verwenden und erhalten so eine noch bessere Kontrolle.

- Unterstützt benutzerseitig zugewiesene verwaltete Identitäten, um den Schlüsseln Berechtigungen zum Verwalten der Datenverschlüsselung im Recovery Services-Tresor zu gewähren.
- Ermöglicht die Verschlüsselung mit kundenseitig verwalteten Schlüsseln beim Erstellen eines Recovery Services-Tresors.
  >[!NOTE]
  >Dieses Feature befindet sich zurzeit in der eingeschränkten Vorschau. Zum Anmelden füllen Sie [dieses Formular](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURDNTVVhGOUxXSVBZMEwxUU5FNDkyQkU4Ny4u) aus, und schreiben Sie uns an [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com).
- Ermöglicht die Verwendung von Azure-Richtlinien zum Überwachen und Erzwingen der Verschlüsselung mit kundenseitig verwalteten Schlüsseln.
>[!NOTE]
>- Die oben genannten Funktionen werden nur über die Azure-Portal unterstützt. PowerShell wird derzeit nicht unterstützt.<br>Wenn Sie PowerShell zum Verwalten von Verschlüsselungsschlüsseln für die Sicherung verwenden, empfiehlt es sich nicht, die Schlüssel über das Portal zu aktualisieren.<br>Wenn Sie den Schlüssel über das Portal aktualisieren, können Sie PowerShell nicht verwenden, um den Verschlüsselungsschlüssel weiter zu aktualisieren, bis ein PowerShell-Update zur Unterstützung des neuen Modells verfügbar ist. Sie können jedoch weiterhin den Schlüssel aus der Azure-Portal aktualisieren.
>- Sie können die Überwachungsrichtlinie für die Überwachung von Tresoren mit Verschlüsselung verwenden, indem Sie kundenseitig verwaltete Schlüssel verwenden, die nach dem 01.04.2021 aktiviert wurden.  
>- Bei Tresoren, bei denen die CMK-Verschlüsselung vor diesem Datum aktiviert wurde, kann die Richtlinie möglicherweise nicht angewendet werden oder falsch negative Ergebnisse anzeigen (d. h. diese Tresore werden möglicherweise als nicht konform gemeldet, obwohl die CMK-Verschlüsselung aktiviert ist). [Weitere Informationen](encryption-at-rest-with-cmk.md#using-azure-policies-for-auditing-and-enforcing-encryption-utilizing-customer-managed-keys-in-preview)

Weitere Informationen finden Sie unter [Verschlüsselung für Azure Backup mit kundenseitig verwalteten Schlüsseln](encryption-at-rest-with-cmk.md). 

## <a name="next-steps"></a>Nächste Schritte

- [Azure Backup: Leitfaden und bewährte Methoden](guidance-best-practices.md)
