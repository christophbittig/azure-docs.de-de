---
title: Verwalten von Azure Database for PostgreSQL-Servern
description: Hier erfahren Sie, wie Sie Azure Database for PostgreSQL-Server verwalten.
ms.topic: conceptual
ms.date: 09/21/2021
ms.openlocfilehash: 2546279f22df9a39f454b926d9960f38cf42f2fc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631241"
---
# <a name="manage-azure-database-for-postgresql-server"></a>Verwalten von Azure Database for PostgreSQL-Servern

In diesem Artikel wird beschrieben, wie Sie Azure Database for PostgreSQL-Server verwalten, die mit dem Azure Backup-Dienst gesichert werden.

## <a name="change-policy"></a>Ändern einer Richtlinie

Sie können die zugehörige Richtlinie mit einer Sicherungsinstanz ändern.

1. Wählen Sie **Sicherungsinstanz** -> **Richtlinie ändern** aus.


   :::image type="content" source="./media/manage-azure-database-postgresql/change-policy.png" alt-text="Screenshot: Option zum Ändern einer Richtlinie.":::
   
1. Wählen Sie die neue Richtlinie aus, die Sie auf die Datenbank anwenden möchten.

   :::image type="content" source="./media/manage-azure-database-postgresql/reassign-policy.png" alt-text="Screenshot: Option zum erneuten Zuweisen einer Richtlinie.":::

## <a name="stop-protection-preview"></a>Schutz beenden (Vorschau)

Es gibt drei Möglichkeiten, wie Sie den Schutz eines Azure Database for PostgreSQL-Servers beenden können.

- **Schutz beenden und Daten aufbewahren (für immer aufbewahren)** : Mit dieser Option können Sie verhindern, dass zukünftige Sicherungsaufträge Ihren Azure Database for PostgreSQL-Server schützen. Der Azure Backup-Dienst behält aber die Wiederherstellungspunkte bei, die dauerhaft gesichert werden. Die Aufbewahrung der Wiederherstellungspunkte im Tresor wird Ihnen in Rechnung gestellt (Einzelheiten finden Sie unter [Azure Backup – Preise](https://azure.microsoft.com/pricing/details/backup/)). Bei Bedarf können Sie aus diesen Wiederherstellungspunkten eine Wiederherstellung durchführen. Verwenden Sie die Option **Sicherung fortsetzen**, um den Schutz fortzusetzen.

- **Schutz beenden und Daten aufbewahren (gemäß Richtlinie aufbewahren)** : Mit dieser Option können Sie verhindern, dass zukünftige Sicherungsaufträge Ihren Azure Database for PostgreSQL-Server schützen. Die Wiederherstellungspunkte werden gemäß der Richtlinie beibehalten und entsprechend den [Azure Backup-Preisen](https://azure.microsoft.com/pricing/details/backup/) abgerechnet. Der letzte Wiederherstellungspunkt wird jedoch dauerhaft beibehalten.

- **Schutz beenden und Daten löschen**: Mit dieser Option können Sie verhindern, dass zukünftige Sicherungsaufträge Ihren Azure Database for PostgreSQL-Server schützen, und alle Wiederherstellungspunkte löschen. Sie können die Datenbank nicht wiederherstellen und auch nicht die Option **Sicherung fortsetzen** verwenden.

### <a name="stop-protection-and-retain-data"></a>Schutz beenden und Daten aufbewahren

1. Öffnen Sie das **Backup Center**, und wählen Sie **Azure Database for PostgreSQL-Server (Vorschau)** aus.

1. Wählen Sie aus der Liste der Serversicherungsinstanzen die Instanz aus, die Sie aufbewahren möchten.

1. Wählen Sie **Sicherung beenden (Vorschau)** aus.

   :::image type="content" source="./media/manage-azure-database-postgresql/select-postgresql-server-backup-instance-to-delete-inline.png" alt-text="Screenshot: Auswahl der Azure Database for PostgreSQL-Serversicherungsinstanz, die beendet werden soll." lightbox="./media/manage-azure-database-postgresql/select-postgresql-server-backup-instance-to-delete-expanded.png":::

1. Wählen Sie eine der folgenden Optionen für die Datenaufbewahrung aus:

   1. Dauerhaft aufbewahren
   1. Gemäß Richtlinie aufbewahren
   
   :::image type="content" source="./media/manage-azure-database-postgresql/data-retention-options-inline.png" alt-text="Screenshot: Optionen für die Datenaufbewahrung." lightbox="./media/manage-azure-database-postgresql/data-retention-options-expanded.png":::

   Sie können auch aus einer Dropdownliste den Grund für das Beenden der Sicherung auswählen.

1. Klicken Sie auf **Sicherung beenden**.

1. Wählen Sie **Bestätigen** aus, um die Sicherung zu beenden.

   :::image type="content" source="./media/manage-azure-database-postgresql/confirmation-to-stop-backup-inline.png" alt-text="Screenshot: Bestätigung des Beendens der Sicherung." lightbox="./media/manage-azure-database-postgresql/confirmation-to-stop-backup-expanded.png":::

### <a name="stop-protection-and-delete-data"></a>Schutz beenden und Daten löschen

1. Öffnen Sie das **Backup Center**, und wählen Sie **Azure Database for PostgreSQL-Server (Vorschau)** aus.

1.  Wählen Sie aus der Liste der Serversicherungsinstanzen die Instanz aus, die Sie löschen möchten.

1. Wählen Sie **Sicherung beenden (Vorschau)** aus.

1. Wählen Sie **Sicherungsdaten löschen** aus.

   Geben Sie den Namen der Sicherungsinstanz, den Grund für den Löschvorgang sowie ggf. weitere Kommentare an.

   :::image type="content" source="./media/manage-azure-database-postgresql/delete-backup-data-and-provide-details-inline.png" alt-text="Screenshot: Option zum Löschen von Sicherungsdaten und Details, die hinzugefügt werden müssen." lightbox="./media/manage-azure-database-postgresql/delete-backup-data-and-provide-details-expanded.png":::

1. Wählen Sie **Sicherung beenden** aus.

1. Wählen Sie **Bestätigen** aus, um die Sicherung zu beenden.

   :::image type="content" source="./media/manage-azure-database-postgresql/confirmation-to-stop-backup-inline.png" alt-text="Screenshot: Bestätigung des Beendens der Sicherung." lightbox="./media/manage-azure-database-postgresql/confirmation-to-stop-backup-expanded.png":::

## <a name="resume-protection"></a>Schutz fortsetzen

Wenn Sie beim Beenden der Datensicherung die Option **Schutz beenden und Daten aufbewahren** ausgewählt haben, können Sie den Schutz für Ihren Azure Database for PostgreSQL-Server fortsetzen.

>[!Note]
>Wenn Sie den Schutz einer Datenbank starten, wird die Sicherungsrichtlinie auch auf die aufbewahrten Daten angewendet. Die Wiederherstellungspunkte, die gemäß der Richtlinie abgelaufen sind, werden bereinigt.

Führen Sie die folgenden Schritte durch:

1. Öffnen Sie das **Backup Center**, und wählen Sie **Azure Database for PostgreSQL-Server (Vorschau)** aus.

1. Wählen Sie aus der Liste der Serversicherungsinstanzen die Instanz aus, deren Schutz Sie fortsetzen möchten.

1. Wählen Sie **Sicherung fortsetzen (Vorschau)** aus.

   :::image type="content" source="./media/manage-azure-database-postgresql/resume-data-protection-inline.png" alt-text="Screenshot: Option zum Fortsetzen des Datenschutzes." lightbox="./media/manage-azure-database-postgresql/resume-data-protection-expanded.png":::

1. Wählen Sie **Sicherung fortsetzen** aus.

   :::image type="content" source="./media/manage-azure-database-postgresql/resume-data-backup-inline.png" alt-text="Screenshot: Option zum Fortsetzen der Datensicherung." lightbox="./media/manage-azure-database-postgresql/resume-data-backup-expanded.png":::

## <a name="next-steps"></a>Nächste Schritte

[Übersicht über Sicherungstresore](backup-vault-overview.md)
