---
title: Verwalten von Azure Managed Disks
description: In diesem Artikel erhalten Sie Informieren über die Verwaltung von Azure Managed Disks über das Azure-Portal.
ms.topic: conceptual
ms.date: 09/23/2021
ms.openlocfilehash: 38f131d9656f604ee4d53b5d1de0f16ffa56b9ba
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130215989"
---
# <a name="manage-azure-managed-disks"></a>Verwalten von Azure Managed Disks

In diesem Artikel wird beschrieben, wie Sie Azure Managed Disks über das Azure-Portal verwalten.

## <a name="stop-protection-preview"></a>Schutz beenden (Vorschau)


Es gibt drei Möglichkeiten, den Schutz eines Azure-Datenträgers zu beenden:

- **Schutz beenden und Daten beibehalten (dauerhaft beibehalten)** : Mit dieser Option können Sie verhindern, dass alle zukünftigen Sicherungsaufträge Ihren Datenträger schützen. Der Azure Backup-Dienst behält aber die Wiederherstellungspunkte bei, die dauerhaft gesichert werden. Die Aufbewahrung der Wiederherstellungspunkte im Tresor wird Ihnen in Rechnung gestellt (Einzelheiten finden Sie unter [Azure Backup – Preise](https://azure.microsoft.com/pricing/details/backup/)). Sie können den Datenträger bei Bedarf wiederherstellen. Verwenden Sie die Option **Sicherung fortsetzen**, um den Datenträgerschutz fortzusetzen.

- **Schutz beenden und Daten beibehalten (gemäß Richtlinie beibehalten)** : Mit dieser Option können Sie verhindern, dass alle zukünftigen Sicherungsaufträge Ihren Datenträger schützen. Die Wiederherstellungspunkte werden gemäß der Richtlinie beibehalten und gemäß den [Azure Backup-Preisen](https://azure.microsoft.com/pricing/details/backup/) abgerechnet. Der letzte Wiederherstellungspunkt wird jedoch dauerhaft beibehalten.

- **Schutz beenden und Daten löschen**: Mit dieser Option können Sie verhindern, dass alle zukünftigen Sicherungsaufträge Ihre Datenträger schützen, und alle Wiederherstellungspunkte löschen. Sie können den Datenträger nicht wiederherstellen und auch nicht die Option **Sicherung fortsetzen** verwenden.

### <a name="stop-protection-and-retain-data"></a>Beenden des Schutzes und Beibehalten der Daten

1. Wechseln Sie zum **Backup Center** und wählen Sie **Azure Disks** aus.

1. Wählen Sie in der Liste der Datenträgersicherungsinstanzen die Instanz aus, die Sie beibehalten möchten.

1. Wählen Sie **Backup beenden (Vorschau)** aus.

   :::image type="content" source="./media/manage-azure-managed-disks/select-disk-backup-instance-to-stop-inline.png" alt-text="Screenshot: Auswahl der Azure-Datenträgersicherungsinstanz, die beendet werden soll" lightbox="./media/manage-azure-managed-disks/select-disk-backup-instance-to-stop-expanded.png":::
 
1. Wählen Sie eine der folgenden Optionen für die Datenaufbewahrung aus:

   1. Dauerhaft aufbewahren
   1. Gemäß Richtlinie beibehalten
 
   :::image type="content" source="./media/manage-azure-managed-disks/data-retention-options-for-disk-inline.png" alt-text="Screenshot: Optionen zum Beenden des Schutzes von Datenträgersicherungsinstanzen" lightbox="./media/manage-azure-managed-disks/data-retention-options-for-disk-expanded.png":::

   Sie können auch den Grund für das Beenden von Sicherungen in der Dropdownliste auswählen.

1. Klicken Sie auf **Sicherung beenden**.

1. Wählen Sie **Bestätigen** aus, um den Schutz von Daten zu beenden.

   :::image type="content" source="./media/manage-azure-managed-disks/confirm-stopping-disk-backup-inline.png" alt-text="Screenshot: Optionen für die Aufbewahrung von Datenträgersicherungsinstanzen, die ausgewählt werden sollen" lightbox="./media/manage-azure-managed-disks/confirm-stopping-disk-backup-expanded.png":::

### <a name="stop-protection-and-delete-data"></a>Beenden des Schutzes und Löschen der Daten

1. Wechseln Sie zum **Backup Center** und wählen Sie **Azure Disks** aus.

1. Wählen Sie in der Liste der Datenträgersicherungsinstanzen die Instanz aus, die Sie löschen möchten.

1. Klicken Sie auf **Backup beenden (Vorschau)** .

1. Wählen Sie **Sicherungsdaten löschen** aus.

   Geben Sie den Namen der Sicherungsinstanz, den Grund für den Löschvorgang und alle anderen Kommentare an.

   :::image type="content" source="./media/manage-azure-managed-disks/details-to-stop-disk-backup-inline.png" alt-text="Screenshot: Bestätigung des Beendens der Datenträgersicherung" lightbox="./media/manage-azure-managed-disks/details-to-stop-disk-backup-expanded.png":::

1. Wählen Sie **Sicherung beenden** aus.

1. Wählen Sie **Bestätigen** aus, um den Schutz von Daten zu beenden.

   :::image type="content" source="./media/manage-azure-managed-disks/confirm-stopping-disk-backup-inline.png" alt-text="Screenshot: Optionen für die Aufbewahrung von Datenträgersicherungsinstanzen, die ausgewählt werden sollen" lightbox="./media/manage-azure-managed-disks/confirm-stopping-disk-backup-expanded.png":::

## <a name="resume-protection"></a>Fortsetzen des Schutzes

Wenn Sie die Option **Schutz beenden und Daten beibehalten** ausgewählt haben, können Sie den Schutz für Ihre Datenträger fortsetzen.

>[!Note]
>Wenn Sie mit dem Schützen eines Datenträgers beginnen, wird die Sicherungsrichtlinie auch auf die beibehaltenen Daten angewendet. Die Wiederherstellungspunkte, die gemäß der Richtlinie abgelaufen sind, werden bereinigt.

Führen Sie die folgenden Schritte durch:

1. Wechseln Sie zum **Backup Center** und wählen Sie **Azure Disks** aus.

1. Wählen Sie in der Liste der Datenträgersicherungsinstanzen die Instanz aus, die Sie fortsetzen möchten.

1. Wählen Sie **Backup fortsetzen (Vorschau)** aus.

   :::image type="content" source="./media/manage-azure-managed-disks/resume-disk-protection-inline.png" alt-text="Screenshot: Option zum Fortsetzen des Datenträgerschutzes" lightbox="./media/manage-azure-managed-disks/resume-disk-protection-expanded.png":::

1. Wählen Sie **Sicherung fortsetzen** aus.

   :::image type="content" source="./media/manage-azure-managed-disks/resume-disk-backup-inline.png" alt-text="Screenshot: Option zum Fortsetzen der Datenträgersicherung" lightbox="./media/manage-azure-managed-disks/resume-disk-backup-expanded.png":::

## <a name="delete-backup-instance"></a>Löschen einer Sicherungsinstanz

Wenn Sie alle geplanten Sicherungsaufträge beenden und alle vorhandenen Sicherungen löschen möchten, verwenden Sie **Sicherungsinstanz löschen**.

>[!Note]
>Das Löschen einer Sicherungsinstanz schlägt fehl, wenn die Momentaufnahme-Ressourcengruppe manuell gelöscht wird oder die Berechtigung für die verwaltete Identität des Sicherungstresors aufgehoben wird. Erstellen Sie in solchen Fehlerfällen vorübergehend die Momentaufnahme-Ressourcengruppe (mit dem gleichen Namen), und stellen Sie der verwalteten Identität des Sicherungstresors die erforderlichen Rollenberechtigungen bereit, wie [hier](./backup-managed-disks-ps.md#assign-permissions) dokumentiert. Sie finden den Namen der Momentaufnahme-Ressourcengruppe auf der Registerkarte **Essentials** des Bildschirms **Sicherungsinstanz**. 

Gehen Sie zum Löschen einer Datenträgersicherungsinstanz wie folgt vor:

1. Klicken Sie auf dem Bildschirm der Sicherungsinstanz auf **Löschen**.

   :::image type="content" source="./media/manage-azure-managed-disks/initiate-deleting-backup-instance-inline.png" alt-text="Screenshot: Prozess zum Löschen einer Sicherungsinstanz" lightbox="./media/manage-azure-managed-disks/initiate-deleting-backup-instance-expanded.png":::

1. Geben Sie Bestätigungsdetails an, einschließlich des Namens der Sicherungsinstanz, des Grunds für den Löschvorgang und zusätzlicher Kommentare.

   :::image type="content" source="./media/manage-azure-managed-disks/confirm-deleting-backup-instance-inline.png" alt-text="Screenshot: Bestätigen des Löschens von Sicherungsinstanzen" lightbox="./media/manage-azure-managed-disks/confirm-deleting-backup-instance-expanded.png":::

1. Klicken Sie auf **Löschen**, um das Löschen der Sicherungsinstanz zu bestätigen und damit fortzufahren.

## <a name="next-steps"></a>Nächste Schritte

[Übersicht über Sicherungstresore](backup-vault-overview.md)