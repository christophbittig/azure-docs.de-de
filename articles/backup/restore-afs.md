---
title: Wiederherstellen von Azure-Dateifreigaben
description: Erfahren Sie, wie Sie das Azure-Portal verwenden, um eine vollständige Dateifreigabe oder bestimmte Dateien von einem von Azure Backup erstellten Wiederherstellungspunkt wiederherstellen.
ms.topic: conceptual
ms.date: 11/03/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 464c9927f901a373310ad6d1c0812a5a5de1eaaa
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846828"
---
# <a name="restore-azure-file-shares"></a>Wiederherstellen von Azure-Dateifreigaben

In diesem Artikel wird erläutert, wie Sie das Azure-Portal verwenden, um eine vollständige Dateifreigabe oder bestimmte Dateien von einem von [Azure Backup](./backup-overview.md) erstellten Wiederherstellungspunkt wiederherstellen.

In diesem Artikel lernen Sie Folgendes:

* Wiederherstellen einer vollständigen Azure-Dateifreigabe.
* Wiederherstellen einzelner Dateien oder Ordner.
* Nachverfolgen des Status des Wiederherstellungsvorgangs.

## <a name="steps-to-perform-a-restore-operation"></a>Schritte zum Ausführen eines Wiederherstellungsvorgangs

Führen Sie die folgenden Schritte aus, um einen Wiederherstellungsvorgang auszuführen.

### <a name="select-the-file-share-to-restore"></a>Auswählen der Dateifreigabe, die wiederhergestellt werden soll

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com/) zu **Backup Center**, und klicken Sie auf **Wiederherstellen**.

   :::image type="content" source="./media/restore-afs/backup-center-restore-inline.png" alt-text="Screenshot: Starten des Wiederherstellungsprozesses für die Azure-Dateifreigabe." lightbox="./media/restore-afs/backup-center-restore-expanded.png":::

1. Wählen Sie **Azure Files (Azure Storage)** als Datenquellentyp aus, wählen Sie die Dateifreigabe aus, die Sie wiederherstellen möchten, und klicken Sie dann auf **Weiter**.

   :::image type="content" source="./media/restore-afs/azure-file-share-select-instance.png" alt-text="Screenshot: Auswählen von Sicherungselementen.":::

### <a name="full-share-recovery"></a>Wiederherstellung der vollständigen Freigabe

Sie können diese Wiederherstellungsoption verwenden, um die vollständige Dateifreigabe am ursprünglichen oder einem alternativen Speicherort wiederherzustellen.

1. Nachdem Sie im vorherigen Schritt **Weiter** ausgewählt haben, wird der Bereich **Wiederherstellen** geöffnet. Um den Wiederherstellungspunkt auszuwählen, den Sie für den Wiederherstellungsvorgang nutzen möchten, wählen Sie unter dem Textfeld **Wiederherstellungspunkt** den Linktext **Auswählen** aus.

    ![Wiederherstellungspunkt mit „Auswählen“ auswählen](./media/restore-afs/select-restore-point.png)

1. Der Kontextbereich **Wiederherstellungspunkt auswählen** wird auf der rechten Seite geöffnet und listet die für die ausgewählte Dateifreigabe verfügbaren Wiederherstellungspunkte auf. Wählen Sie den Wiederherstellungspunkt aus, den Sie für die Wiederherstellung verwenden möchten, und wählen Sie **OK** aus.

   :::image type="content" source="./media/restore-afs/azure-file-share-select-restore-point-inline.png" alt-text="Screenshot: Auswählen des Wiederherstellungspunkts." lightbox="./media/restore-afs/azure-file-share-select-restore-point-expanded.png":::

    >[!NOTE]
    >Standardmäßig werden im Bereich **Wiederherstellungspunkt a** Wiederherstellungspunkte der letzten 30 Tage aufgeführt. Wenn Sie sich die Wiederherstellungspunkte ansehen möchten, die während eines bestimmten Zeitraums erstellt wurden, geben Sie den Bereich an, indem Sie die entsprechende **Startzeit** und **Endzeit** und dann die Schaltfläche **Aktualisieren** auswählen.

1. Der nächste Schritt ist das Wählen des **Speicherorts für die Wiederherstellung**. Geben Sie im Abschnitt **Wiederherstellungsziel** an, wo oder wie die Daten wiederhergestellt werden sollen. Wählen Sie eine der beiden folgenden Optionen mithilfe der Umschaltfläche aus:

    * **Ursprünglicher Speicherort**: Die gesamte Dateifreigabe wird am gleichen Speicherort wie die ursprüngliche Quelle wieder hergestellt.
    * **Alternativer Speicherort:** Die gesamte Dateifreigabe wird an einem alternativen Speicherort wiederhergestellt und die ursprüngliche Dateifreigabe unverändert beibehalten.

#### <a name="restore-to-the-original-location-full-share-recovery"></a>Wiederherstellen am ursprünglichen Speicherort (vollständige Freigabe)

1. Wählen Sie **Ursprünglicher Speicherort** als **Wiederherstellungsziel** aus, und entscheiden Sie dann, ob Daten bei Konflikten übersprungen oder überschrieben werden sollen, indem Sie in der Dropdownliste **Bei Konflikten** die gewünschte Option wählen.

1. Wählen Sie **Wiederherstellen** aus, um den Wiederherstellungsvorgang zu starten.

   :::image type="content" source="./media/restore-afs/azure-file-share-original-location-recovery.png" alt-text="Screenshot: Auswählen von „Wiederherstellen“, um zu starten.":::

#### <a name="restore-to-an-alternate-location-full-share-recovery"></a>Wiederherstellen an einem alternativen Speicherort (vollständige Freigabe)

1. Wählen Sie **Alternativer Speicherort** als **Wiederherstellungsziel** aus.
1. Wählen Sie aus der Dropdownliste **Speicherkonto** das Zielspeicherkonto aus, in dem Sie den gesicherten Inhalt wiederherstellen möchten.
1. In der Dropdownliste **Dateifreigabe auswählen** werden die Dateifreigaben angezeigt, die in dem Speicherkonto vorhanden sind, das Sie in Schritt 2 ausgewählt haben. Wählen Sie die Dateifreigabe aus, in der Sie die gesicherten Inhalte wiederherstellen möchten.
1. Geben Sie im Feld **Ordnername** den Namen eines Ordners ein, den Sie in der Zieldateifreigabe mit den wiederhergestellten Inhalten erstellen möchten.
1. Wählen Sie aus, ob Daten bei Konflikten übersprungen oder überschrieben werden sollen.
1. Nachdem Sie die passenden Werte in alle Felder eingegeben haben, wählen Sie **Wiederherstellen** aus, um den Wiederherstellungsvorgang zu starten.

   :::image type="content" source="./media/restore-afs/azure-file-share-alternate-location-recovery.png" alt-text="Screenshot: Auswählen eines alternativen Speicherorts.":::

### <a name="item-level-recovery"></a>Wiederherstellung auf Elementebene

Sie können diese Wiederherstellungsoption verwenden, um einzelne Dateien oder Ordner am ursprünglichen oder einem alternativen Speicherort wiederherzustellen.

1. Wechseln Sie zum **Backup Center**, und wählen Sie im Menü die Option **Sicherungsinstanzen** aus, wobei als Datenquellentyp **Azure Storage (Azure Files)** ausgewählt ist.
1. Wählen Sie die Dateifreigabe aus, für die Sie eine Wiederherstellung auf Elementebene durchführen möchten.

   Das Menü „Sicherungselement“ wird mit einer Option **Dateiwiederherstellung** angezeigt.

    ![„Dateiwiederherstellung“ auswählen](./media/restore-afs/file-recovery.png)

1. Nachdem Sie **Dateiwiederherstellung** ausgewählt haben, wird der Bereich **Wiederherstellung** geöffnet. Um den Wiederherstellungspunkt auszuwählen, den Sie für den Wiederherstellungsvorgang verwenden möchten, wählen Sie unter dem Textfeld **Wiederherstellungspunkt** den Linktext **Auswählen** aus.

    ![Wiederherstellungspunkt mit dem Link „Auswählen“ auswählen](./media/restore-afs/select-restore-point.png)

1. Der Kontextbereich **Wiederherstellungspunkt auswählen** wird auf der rechten Seite geöffnet und listet die für die ausgewählte Dateifreigabe verfügbaren Wiederherstellungspunkte auf. Wählen Sie den Wiederherstellungspunkt aus, den Sie für die Wiederherstellung verwenden möchten, und wählen Sie **OK** aus.

    ![Auswählen eines Wiederherstellungspunkts](./media/restore-afs/restore-point.png)

1. Der nächste Schritt ist das Wählen des **Speicherorts für die Wiederherstellung**. Geben Sie im Abschnitt **Wiederherstellungsziel** an, wo oder wie die Daten wiederhergestellt werden sollen. Wählen Sie eine der beiden folgenden Optionen mithilfe der Umschaltfläche aus:

    * **Ursprünglicher Speicherort**: Die ausgewählten Dateien oder Ordner werden in derselben Dateifreigabe wie die ursprüngliche Quelle wiederhergestellt.
    * **Alternativer Standort:** Die ausgewählten Dateien oder Ordner werden an einem alternativen Speicherort wiederhergestellt und der ursprüngliche Inhalt der Dateifreigabe bleibt unverändert erhalten.

#### <a name="restore-to-the-original-location-item-level-recovery"></a>Wiederherstellen am ursprünglichen Speicherort (auf Elementebene)

1. Wählen Sie **Ursprünglicher Speicherort** als **Wiederherstellungsziel** aus, und entscheiden Sie dann, ob Daten bei Konflikten übersprungen oder überschrieben werden sollen, indem Sie in der Dropdownliste **Bei Konflikten** die gewünschte Option wählen.

    ![Ursprünglicher Speicherort für Wiederherstellung auf Elementebene](./media/restore-afs/original-location-item-level.png)

1. Wählen Sie die Schaltfläche **Datei auswählen** aus, um die Dateien oder Ordner auszuwählen, die Sie wiederherstellen möchten. Dadurch wird rechts ein Kontextbereich geöffnet, in dem der Inhalt des Wiederherstellungspunkts der Dateifreigabe angezeigt wird, den Sie für die Wiederherstellung ausgewählt haben.

    ![„Datei hinzufügen“ wählen](./media/restore-afs/add-file.png)

1. Aktivieren Sie das Kontrollkästchen für die Datei oder den Ordner, die bzw. den Sie wiederherstellen möchten, und wählen Sie **Auswählen** aus.

    ![Datei oder Ordner auswählen](./media/restore-afs/select-file-folder.png)

1. Wiederholen Sie die Schritte 2 bis 4, um mehrere Dateien oder Ordner für die Wiederherstellung auszuwählen.
1. Nachdem Sie alle Elemente ausgewählt haben, die Sie wiederherstellen möchten, wählen Sie **Wiederherstellen** aus, um den Wiederherstellungsvorgang zu starten.

    ![Zum Starten „Wiederherstellen“ auswählen](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location-item-level-recovery"></a>Wiederherstellen an einem alternativen Speicherort (auf Elementebene)

1. Wählen Sie **Alternativer Speicherort** als **Wiederherstellungsziel** aus.
1. Wählen Sie aus der Dropdownliste **Speicherkonto** das Zielspeicherkonto aus, in dem Sie den gesicherten Inhalt wiederherstellen möchten.
1. In der Dropdownliste **Dateifreigabe auswählen** werden die Dateifreigaben angezeigt, die in dem Speicherkonto vorhanden sind, das Sie in Schritt 2 ausgewählt haben. Wählen Sie die Dateifreigabe aus, in der Sie die gesicherten Inhalte wiederherstellen möchten.
1. Geben Sie im Feld **Ordnername** den Namen eines Ordners ein, den Sie in der Zieldateifreigabe mit den wiederhergestellten Inhalten erstellen möchten.
1. Wählen Sie aus, ob Daten bei Konflikten übersprungen oder überschrieben werden sollen.
1. Wählen Sie die Schaltfläche **Datei auswählen** aus, um die Dateien oder Ordner auszuwählen, die Sie wiederherstellen möchten. Dadurch wird rechts ein Kontextbereich geöffnet, in dem der Inhalt des Wiederherstellungspunkts der Dateifreigabe angezeigt wird, den Sie für die Wiederherstellung ausgewählt haben.

    ![Elemente zum Wiederherstellen an einem alternativen Speicherort auswählen](./media/restore-afs/restore-to-alternate-location.png)

1. Aktivieren Sie das Kontrollkästchen für die Datei oder den Ordner, die bzw. den Sie wiederherstellen möchten, und wählen Sie **Auswählen** aus.

    ![Wiederherstellungsziel auswählen](./media/restore-afs/recovery-destination.png)

1. Wiederholen Sie die Schritte 6 bis 8, um mehrere Dateien oder Ordner für die Wiederherstellung auszuwählen.
1. Nachdem Sie alle Elemente ausgewählt haben, die Sie wiederherstellen möchten, wählen Sie **Wiederherstellen** aus, um den Wiederherstellungsvorgang zu starten.

    ![Nachdem Sie alle Dateien ausgewählt haben, wählen Sie „OK“ aus.](./media/restore-afs/after-selecting-all-items.png)

## <a name="track-a-restore-operation"></a>Wiederherstellungsvorgang nachverfolgen

Nachdem Sie den Wiederherstellungsvorgang ausgelöst haben, erstellt der Sicherungsdienst einen Auftrag zum Nachverfolgen. Azure Backup zeigt Benachrichtigungen zum Auftrag im Portal an. Um die Vorgänge für den Auftrag anzuzeigen, wählen Sie den Hyperlink für Benachrichtigungen aus.

![Benachrichtigungslink auswählen](./media/restore-afs/notifications-link.png)

Sie können den Fortschritt der Wiederherstellung auch über den Recovery Services-Tresor überwachen:

1. Klicken Sie im **Backup Center** im Menü auf **Sicherungsaufträge**.
1. Filtern Sie nach Aufträgen mit dem erforderlichen Datenquellentyp und Auftragsstatus.

   :::image type="content" source="./media/restore-afs/backup-center-jobs-inline.png" alt-text="Screenshot: Auswählen von Sicherungsaufträgen." lightbox="./media/restore-afs/backup-center-jobs-expanded.png":::

1. Wählen Sie den Namen der Workload aus, die Ihrer Dateifreigabe entspricht, um weitere Details über den Wiederherstellungsvorgang anzuzeigen, wie z. B. **übertragene Daten**, **Anzahl der wiederhergestellten Dateien** usw.

    ![Details zur Wiederherstellung anzeigen](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>Nächste Schritte

* [Verwalten der Sicherungen von Azure-Dateifreigaben](manage-afs-backup.md).
