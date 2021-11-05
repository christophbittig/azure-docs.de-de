---
title: Überwachen und Betreiben von Sicherungen mit Backup Center
description: In diesem Artikel wird erläutert, wie Sie mit Backup Center Sicherungen im gewünschten Umfang überwachen und betreiben.
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: 45e190fdd495cd5ea074386f601eb6296a90a14a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131035925"
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

## <a name="metrics"></a>Metriken

Azure Backup bietet jetzt über Azure Monitor eine Reihe von integrierten Metriken, mit denen Sie den Zustand Ihrer Sicherungen überwachen können. Um Metriken anzuzeigen, wechseln Sie zum **Backup Center**, und wählen Sie im Menü **Metriken** aus.

:::image type="content" source="./media/metrics-overview/metrics-chart-inline.png" alt-text="Screenshot: Integrierte Metriken für die Überwachung der Integrität von Sicherungen" lightbox="./media/metrics-overview/metrics-chart-expanded.png":::

Azure Backup bietet die folgenden Hauptfunktionen:

* Die Möglichkeit, sofort einsatzbereite Metriken in Bezug auf den Sicherungs- und Wiederherstellungszustand Ihrer Sicherungselemente sowie die damit verbundenen Trends anzuzeigen
* Die Möglichkeit, benutzerdefinierte Warnregeln für diese Metriken zu erstellen, um den Zustand Ihrer Sicherungselemente effizient zu überwachen
* Die Möglichkeit, ausgelöste Metrikwarnungen an verschiedene Benachrichtigungskanäle weiterzuleiten, die von Azure Monitor unterstützt werden, z. B. E-Mail, ITSM, Webhook und Logik-Apps.

[Weitere Informationen zu den Metriken für Azure Backup](metrics-overview.md)

## <a name="alerts"></a>Alerts

Um Warnungen anzuzeigen, wechseln Sie zum **Backup Center**, und wählen Sie im Menü **Warnungen** aus.

Auf dem Dashboard **Übersicht** wird eine Zusammenfassung der offenen Warnungen der letzten 24 Stunden angezeigt.

:::image type="content" source="./media/backup-azure-monitoring-laworkspace/backup-center-azure-monitor-alert-inline.png" alt-text="Screenshot: Warnungen im Backup Center" lightbox="./media/backup-azure-monitoring-laworkspace/backup-center-azure-monitor-alert-expanded.png":::

Derzeit werden die folgenden Arten von Warnungen im Backup Center angezeigt:

* **Azure Monitor-Standardwarnungen für Azure Backup (Vorschauversion):** Diese Option schließt integrierte Sicherheitswarnungen und konfigurierte Warnungen ein, die Azure Backup über Azure Monitor bereitstellt. [Hier finden Sie weitere Informationen zu den von dieser Lösung unterstützten Warnungsszenarios.](backup-azure-monitoring-built-in-monitor.md#azure-monitor-alerts-for-azure-backup-preview)
* **Metrikwarnungen für Azure Backup (Vorschauversion):** Diese Option schließt Warnungen ein, die basierend auf den von Ihnen erstellten Metrikwarnungsregeln ausgelöst wurden. [Weitere Informationen zu den Metrikwarnungen für Azure Backup](metrics-overview.md)

>[!NOTE]
>- Derzeit zeigt das Backup Center nur Warnungen für Azure-basierte Workloads an. Wenn Sie Warnungen zu lokalen Ressourcen anzeigen möchten, müssen Sie zum Recovery Services-Tresor wechseln und im Menü **Warnungen** auswählen.
>- Im Backup Center werden nur Azure Monitor-Warnungen angezeigt. Warnungen, die von der älteren Warnungslösung (auf der Registerkarte [Sicherungswarnungen](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) im Recovery Services-Tresor) ausgelöst wurden, werden im Backup Center nicht angezeigt.

Weitere Informationen zu Azure Monitor-Warnungen finden Sie unter [Überblick über Warnungen in Azure](../azure-monitor/alerts/alerts-overview.md).

### <a name="datasource-and-global-alerts"></a>Datenquellenwarnungen und globale Warnungen

Die folgenden Arten von Warnungen werden angezeigt:

* **Datenquellenwarnungen:** Warnungen, die an eine bestimmte Datenquelle gebunden sind, die gerade gesichert wird (z. B. Sicherungs- oder Wiederherstellungsfehler auf einer VM, Löschen von Sicherungsdaten einer Datenbank), werden im Abschnitt **Datenquellenwarnungen** angezeigt. Für Metrikwarnungen gilt: Wenn der Warnung eine Dimension der Datenquellen-ID zugeordnet ist, wird die ausgelöste Warnung unter **Datenquellenwarnungen** angezeigt.
* **Globale Warnungen:** Warnungen, die nicht an eine bestimmte Datenquelle gebunden sind (z. B. das Deaktivieren der Funktion eines Tresors für das vorläufige Löschen), werden im Abschnitt **Global Alerts** (Globale Warnungen) angezeigt. Für Metrikwarnungen gilt: Wenn der ausgelösten Warnung keine Datenquellen-ID zugeordnet ist, wird sie unter **Globale Warnungen** angezeigt.

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

Wenn Ihre Organisation mehrere Ressourcen in einem gemeinsamen Tresor sichert und alle Ressourcenbesitzer*innen nur Sicherungsinformationen der Ressourcen anzeigen möchten, die sie jeweils besitzen, können Sie die ressourcenbezogene Ansicht im Backup Center verwenden. Zum Verwenden der ressourcenbezogenen Ansicht aktivieren Sie das Kontrollkästchen „Nur Informationen zu Datenquellen anzeigen, auf die ich Zugriff habe“. Diese Option wird derzeit für die folgenden Registerkarten unterstützt: **Übersicht**, **Sicherungsinstanzen**, **Aufträge**, **Warnungen**. Die unterstützten Workloads sind Azure-VMs, SQL in Azure-VMs, SAP HANA in Azure-VMs, Azure-Blobs, Azure-Datenträger.

> [!NOTE]
> Benutzer*innen müssen weiterhin über die erforderlichen RBAC-Berechtigungen für den Tresor verfügen, auch wenn sie die ressourcenbezogene Ansicht verwenden. Mit dieser Ansicht sollen einzelne Benutzern die Anzeige von Informationen zu Ressourcen (z. B. VMs) vermeiden können, die sie nicht besitzen.

## <a name="next-steps"></a>Nächste Schritte

* [Steuern Ihres Sicherungsbestands](backup-center-govern-environment.md)
* [Ausführen von Aktionen mithilfe von Backup Center](backup-center-actions.md)
* [Gewinnen von Einblicken in Ihre Sicherungen](backup-center-obtain-insights.md)
