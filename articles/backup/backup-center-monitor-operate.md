---
title: Überwachen und Betreiben von Sicherungen mit Backup Center
description: In diesem Artikel wird erläutert, wie Sie mit Backup Center Sicherungen im gewünschten Umfang überwachen und betreiben.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: cab9e710cfe4bf43b0d225d64e8f64b16c09e3a6
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129659847"
---
# <a name="monitor-and-operate-backups-using-backup-center"></a>Überwachen und Verwalten von Sicherungen mit Backup Center

Als Sicherungsadministrator können Sie Backup Center als zentrale Benutzeroberfläche zum Überwachen Ihrer Aufträge und täglichen Sichern des Bestands verwenden. Mit Backup Center können Sie auch reguläre Vorgänge ausführen, z. B. auf Anforderungen der bedarfsgesteuerten Sicherung reagieren, Sicherungen wiederherstellen, Sicherungsrichtlinien erstellen usw.

## <a name="supported-scenarios"></a>Unterstützte Szenarios

* Backup Center wird derzeit für Sicherungen folgender Produkte unterstützt: Azure-VMs, SQL auf Azure-VMs, SAP HANA auf Azure-VMs, Azure Files, Azure Blobs, Azure Managed Disks und Azure Database for PostgreSQL-Server.
* Eine ausführliche Liste der unterstützten und nicht unterstützten Szenarien finden Sie in der [Supportmatrix](backup-center-support-matrix.md).

## <a name="backup-instances"></a>Sicherungsinstanzen

Backup Center ermöglicht das problemlose Suchen und Auffinden von Sicherungsinstanzen in Ihrem Sicherungsbestand.

Wenn Sie in Backup Center die Registerkarte **Sicherungsinstanzen** auswählen, können Sie Details zu allen Sicherungsinstanzen anzeigen, auf die Sie Zugriff haben.

 Sie können die folgenden Informationen zu den einzelnen Sicherungsinstanzen anzeigen:

* Abonnement der Datenquelle
* Ressourcengruppe der Datenquelle
* Letzter Wiederherstellungspunkt
* Der Sicherungsinstanz zugeordneter Tresor
* Schutzstatus

 Sie können die Liste der Sicherungsinstanzen auch nach den folgenden Parametern filtern:

* Abonnement der Datenquelle
* Ressourcengruppe der Datenquelle
* Standort der Datenquelle
* Datenquellentyp
* Tresor
* Schutzstatus
* Datenquellentags

Wenn Sie mit der rechten Maustaste auf eines der Elemente im Raster klicken, können Sie Aktionen für die angegebene Sicherungsinstanz durchführen, z. B. zur Ressource navigieren, bedarfsgesteuerte Sicherungen und Wiederherstellungen auslösen oder die Sicherung beenden.

![Backup Center – Instanzen](./media/backup-center-monitor-operate/backup-center-instances.png)

## <a name="backup-jobs"></a>Sicherungsaufträge

Mithilfe von Backup Center können Sie detaillierte Informationen zu allen Aufträgen anzeigen, die in Ihrem Sicherungsbestand erstellt wurden, und geeignete Maßnahmen für fehlerhafte Aufträge ausführen.

Wenn Sie in Backup Center das Menüelement **Sicherungsaufträge** auswählen, erhalten Sie eine Übersicht über alle Aufträge. Jeder Auftrag umfasst die folgenden Informationen:

* Dem Auftrag zugeordnete Sicherungsinstanz
* Abonnement der Datenquelle
* Ressourcengruppe der Datenquelle
* Standort der Datenquelle
* Auftragsvorgang
* Auftragsstatus
* Startzeit des Auftrags.
* Auftragsdauer

Wenn Sie ein Element im Raster auswählen, können Sie weitere Details zum angegebenen Auftrag anzeigen. Durch Klicken mit der rechten Maustaste auf ein Element können Sie zur Ressource navigieren, um erforderliche Maßnahmen zu ergreifen.

![Backup Center – Aufträge](./media/backup-center-monitor-operate/backup-center-jobs.png)

Mit der Registerkarte **Sicherungsaufträge** können Sie Aufträge maximal für die letzten sieben Tage anzeigen. Zum Anzeigen älterer Aufträge verwenden Sie [Sicherungsberichte](backup-center-obtain-insights.md).

## <a name="vaults"></a>Tresore

Wenn Sie in Backup Center das Menüelement **Tresore** auswählen, können Sie eine Liste aller [Recovery Services-Tresore](backup-azure-recovery-services-vault-overview.md) und [Sicherungstresore](backup-vault-overview.md) anzeigen, auf die Sie Zugriff haben. Sie können die Liste anhand der folgenden Parameter filtern:

* Tresorabonnement
* Tresorressourcengruppe
* Name des Tresors
* Der Richtlinie zugeordneter Datenquellentyp

Durch Auswahl eines Elements in der Liste können Sie zu einem angegebenen Tresor navigieren.

![Backup Center – Tresore](./media/backup-center-monitor-operate/backup-center-vaults.png)

## <a name="backup-policies"></a>Sicherungsrichtlinien

Mit Backup Center können Sie wichtige Informationen für alle Ihre Sicherungsrichtlinien anzeigen und bearbeiten.

Wenn Sie das Menüelement **Sicherungsrichtlinien** auswählen, können Sie alle Richtlinien anzeigen, die Sie in Ihrem Sicherungsbestand erstellt haben. Sie können die Liste nach Tresorabonnement, Ressourcengruppe, Datenquellentyp und Tresor filtern. Wenn Sie mit der rechten Maustaste auf ein Element im Raster klicken, können Sie zugehörige Elemente für diese Richtlinie anzeigen, die Richtlinie bearbeiten oder sie bei Bedarf sogar löschen.

![Backup Center – Richtlinien](./media/backup-center-monitor-operate/backup-center-policies.png)


## <a name="resource-centric-views"></a>Ressourcenbezogene Ansichten

Wenn Ihre Organisation mehrere Ressourcen in einem gemeinsamen Tresor sichert und alle Ressourcenbesitzer nur Sicherungsinformationen der Ressourcen anzeigen möchten, die sie jeweils besitzen, können Sie die ressourcenbezogene Ansicht im Backup Center verwenden. Zum Verwenden der ressourcenbezogenen Ansicht aktivieren Sie das Kontrollkästchen "Nur Informationen zu Datenquellen anzeigen, auf die ich Zugriff habe". Diese Option wird derzeit für die folgenden Registerkarten unterstützt: **Übersicht**, **Sicherungsinstanzen**, **Aufträge**, **Warnungen**. Die unterstützten Workloads sind Azure-VMs, SQL in Azure-VMs, SAP HANA in Azure-VMs, Azure-Blobs, Azure-Datenträger.

> [!NOTE]
> Benutzer müssen weiterhin über die erforderlichen RBAC-Berechtigungen für den Tresor verfügen, auch wenn sie die ressourcenbezogene Ansicht verwenden. Mit dieser Ansicht sollen einzelne Benutzern die Anzeige von Informationen zu Ressourcen (z. B. VMs) vermeiden können, die sie nicht besitzen.

## <a name="next-steps"></a>Nächste Schritte

* [Steuern Ihres Sicherungsbestands](backup-center-govern-environment.md)
* [Ausführen von Aktionen mithilfe von Backup Center](backup-center-actions.md)
* [Gewinnen von Einblicken in Ihre Sicherungen](backup-center-obtain-insights.md)
