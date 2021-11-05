---
title: Supportmatrix für Backup Center
description: Dieser Artikel enthält eine Zusammenfassung der Szenarien, die Backup Center für jeden Workloadtyp unterstützt.
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: 602884f3cf077e53061bd02dc5235ddbe3073075
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131033549"
---
# <a name="support-matrix-for-backup-center"></a>Supportmatrix für Backup Center

Backup Center bietet eine zentralisierte Benutzeroberfläche, mit der Unternehmen [Sicherungen in großem Maßstab steuern, überwachen, betreiben und analysieren](backup-center-overview.md) können. Dieser Artikel enthält eine Zusammenfassung der Szenarien, die Backup Center für jeden Workloadtyp unterstützt.

## <a name="supported-scenarios"></a>Unterstützte Szenarien

| **Kategorie** | **Szenario**  | **Unterstützte Workloads**  | **Einschränkungen** |
| -------------| ------------- | ----------------------- |------------|
| Überwachung   | Anzeigen aller Aufträge | <li> Virtueller Azure-Computer <br><br> <li> Azure Database for PostgreSQL-Server <br><br> <li> SQL Server in Azure-VM <br><br> <li> SAP HANA in Azure-VM <br><br> <li> Azure Files<br/><br/> <li>Azure-Blobs<br/><br/> <li>Azure Managed Disks | <li> Aufträge im Umfang von 7 Tagen sind standardmäßig verfügbar. <br> <li> Jeder Filter bzw. jedes Dropdownelement unterstützt maximal 1000 Elemente. Das Backup Center kann also zum Überwachen von maximal 1000 Abonnements und 1000 Tresoren über Mandanten hinweg verwendet werden. |
| Überwachung | Anzeigen aller Sicherungsinstanzen | <li> Virtueller Azure-Computer <br><br> <li> Azure Database for PostgreSQL-Server <br><br> <li> SQL Server in Azure-VM <br><br> <li> SAP HANA in Azure-VM <br><br> <li> Azure Files<br/><br/> <li>Azure-Blobs<br/><br/> <li>Azure Managed Disks | Wie oben |
| Überwachung | Anzeigen aller Sicherungsrichtlinien | <li> Virtueller Azure-Computer <br><br> <li> Azure Database for PostgreSQL-Server <br><br> <li> SQL Server in Azure-VM <br><br> <li> SAP HANA in Azure-VM <br><br> <li> Azure Files<br/><br/> <li>Azure-Blobs<br/><br/> <li>Azure Managed Disks | Wie oben |
| Überwachung | Anzeigen aller Tresore | <li> Virtueller Azure-Computer <br><br> <li> Azure Database for PostgreSQL-Server <br><br> <li> SQL Server in Azure-VM <br><br> <li> SAP HANA in Azure-VM <br><br> <li> Azure Files<br/><br/> <li>Azure-Blobs<br/><br/> <li>Azure Managed Disks | Wie oben |
| Überwachung | Anzeigen von Azure Monitor-Warnungen im großen Stil | <li> Virtueller Azure-Computer <br><br> <li> Azure Database for PostgreSQL-Server <br><br> <li> SQL Server in Azure-VM <br><br> <li> SAP HANA in Azure-VM <br><br> <li> Azure Files<br/><br/> <li>Azure-Blobs<br/><br/> <li>Azure Managed Disks | Ziehen Sie die Dokumentation zu [Warnungen](./backup-azure-monitoring-built-in-monitor.md#azure-monitor-alerts-for-azure-backup-preview) zurate. |
| Überwachung     |   Azure Backup Metriken anzeigen und Metrikwarnungsregeln schreiben | <li>Azure VM   </li><li>SQL Server in Azure-VM </li><li> SAP HANA in Azure-VM</li><li>Azure Files </li> |   Sie können Metriken für alle Recovery Services-Tresore für eine Region und ein Abonnement gleichzeitig anzeigen. Das Anzeigen von Metriken für einen größeren Bereich im Azure-Portal wird derzeit nicht unterstützt. Die gleichen Grenzwerte gelten auch für die Konfiguration von Metrikwarnungsregeln. Ausführlichere Informationen finden Sie unter [Metriken im Azure-Portal anzeigen](metrics-overview.md#view-metrics-in-the-azure-portal).|
| Aktionen | Konfigurieren der Sicherung | <li> Virtueller Azure-Computer <br><br> <li> Azure Database for PostgreSQL-Server <br><br> <li> SQL Server in Azure-VM <br><br> <li> SAP HANA in Azure-VM <br><br> <li> Azure Files<br/><br/> <li>Azure-Blobs<br/><br/> <li>Azure Managed Disks | In den Supportmatrizen finden Sie Informationen zu [Sicherungen von virtuellen Azure-Computern](./backup-support-matrix-iaas.md) und [Azure Database for PostgreSQL-Servern](backup-azure-database-postgresql-overview.md#support-matrix). |
| Aktionen | Wiederherstellen von Sicherungsinstanzen | <li> Virtueller Azure-Computer <br><br> <li> Azure Database for PostgreSQL-Server <br><br> <li> SQL Server in Azure-VM <br><br> <li> SAP HANA in Azure-VM <br><br> <li> Azure Files<br/><br/> <li>Azure-Blobs<br/><br/> <li>Azure Managed Disks | In den Supportmatrizen finden Sie Informationen zu [Sicherungen von virtuellen Azure-Computern](./backup-support-matrix-iaas.md) und [Azure Database for PostgreSQL-Servern](backup-azure-database-postgresql-overview.md#support-matrix). |
| Aktionen | Tresor erstellen | <li> Virtueller Azure-Computer <br><br> <li> Azure Database for PostgreSQL-Server <br><br> <li> SQL Server in Azure-VM <br><br> <li> SAP HANA in Azure-VM <br><br> <li> Azure Files<br/><br/> <li>Azure-Blobs<br/><br/> <li>Azure Managed Disks | In den Supportmatrizen finden Sie Informationen zum [Recovery Services-Tresor](./backup-support-matrix.md#vault-support). |
| Aktionen | Erstellen der Sicherungsrichtlinie | <li> Virtueller Azure-Computer <br><br> <li> Azure Database for PostgreSQL-Server <br><br> <li> SQL Server in Azure-VM <br><br> <li> SAP HANA in Azure-VM <br><br> <li> Azure Files<br/><br/> <li>Azure-Blobs<br/><br/> <li>Azure Managed Disks | In den Supportmatrizen finden Sie Informationen zum [Recovery Services-Tresor](./backup-support-matrix.md#vault-support). |
| Aktionen | Ausführen bedarfsgesteuerter Sicherung für eine Sicherungsinstanz | <li> Virtueller Azure-Computer <br><br> <li> Azure Database for PostgreSQL-Server <br><br> <li> SQL Server in Azure-VM <br><br> <li> SAP HANA in Azure-VM <br><br> <li> Azure Files<br/><br/> <li>Azure-Blobs<br/><br/> <li>Azure Managed Disks | In den Supportmatrizen finden Sie Informationen zu [Sicherungen von virtuellen Azure-Computern](./backup-support-matrix-iaas.md) und [Azure Database for PostgreSQL-Servern](backup-azure-database-postgresql-overview.md#support-matrix). |
| Aktionen | Beenden der Sicherung für eine Sicherungsinstanz | <li> Virtueller Azure-Computer <br><br> <li> Azure Database for PostgreSQL-Server <br><br> <li> SQL Server in Azure-VM <br><br> <li> SAP HANA in Azure-VM <br><br> <li> Azure Files<br/><br/> <li>Azure-Blobs<br/><br/> <li>Azure Managed Disks | In den Supportmatrizen finden Sie Informationen zu [Sicherungen von virtuellen Azure-Computern](./backup-support-matrix-iaas.md) und [Azure Database for PostgreSQL-Servern](backup-azure-database-postgresql-overview.md#support-matrix). |
| Aktionen | Ausführen eines regionsübergreifenden Wiederherstellungsauftrags von Backup Center aus | <li> Virtueller Azure-Computer <br><br> <li> SQL Server in Azure-VM <br><br> <li> SAP HANA in Azure-VM | Ziehen Sie die Dokumentation zur [regionsübergreifenden Wiederherstellung](./backup-create-rs-vault.md#set-cross-region-restore) zurate. |
| Einblicke | Anzeigen von Sicherungsberichten | <li> Virtueller Azure-Computer <br><br> <li> SQL in Azure Virtual Machine <br><br> <li> SAP HANA in Azure Virtual Machine <br><br> <li> Azure Files <br><br> <li> System Center Data Protection Manager <br><br> <li> Azure Backup-Agent (MARS) <br><br> <li> Azure Backup Server (MABS) | Weitere Informationen finden Sie in den [unterstützten Szenarien für Sicherungsberichte](./configure-reports.md#supported-scenarios) |
| Governance | Anzeigen und Zuweisen integrierter und benutzerdefinierter Azure-Richtlinien unter der Kategorie „Sicherung“ | – | – |
| Governance | Anzeigen von Datenquellen, die nicht für die Sicherung konfiguriert sind | <li> Virtueller Azure-Computer <br><br> <li> Azure Database for PostgreSQL-Server | – |

## <a name="unsupported-scenarios"></a>Nicht unterstützte Szenarien

| **Kategorie** | **Szenario**  |
|--------------|---------------|
| Aktionen | Das Konfigurieren von Tresoreinstellungen im großen Stil wird vom Azure Backup Center derzeit nicht unterstützt. |

## <a name="next-steps"></a>Nächste Schritte

* Sehen Sie sich die [Supportmatrix für Azure Backup](./backup-support-matrix.md) an.
* Sehen Sie sich die [Unterstützungsmatrix für die Sicherung virtueller Azure-Computer](./backup-support-matrix-iaas.md) an.
* Sehen Sie sich die [Supportmatrix für die Sicherung des Azure Database for PostgreSQL-Servers](backup-azure-database-postgresql-overview.md#support-matrix) an.