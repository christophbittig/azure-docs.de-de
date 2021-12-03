---
title: Verwalten der Sicherungen von Azure-Dateifreigaben
description: In diesem Artikel werden allgemeine Aufgaben zur Verwaltung und Überwachung der Azure-Dateifreigaben beschrieben, die durch Azure Backup gesichert werden.
ms.topic: conceptual
ms.date: 11/03/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 465331a39f5fc05d81a4ff06cd58c3962b301831
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132332039"
---
# <a name="manage-azure-file-share-backups"></a>Verwalten der Sicherungen von Azure-Dateifreigaben

In diesem Artikel werden allgemeine Aufgaben zur Verwaltung und Überwachung der Azure-Dateifreigaben beschrieben, die durch [Azure Backup](./backup-overview.md) gesichert werden. Sie lernen, wie Sie Verwaltungsaufgaben im **Backup Center** ausführen.

## <a name="monitor-jobs"></a>Überwachen von Aufträgen

Wenn Sie einen Sicherungs- oder Wiederherstellungsvorgang auslösen, erstellt der Sicherungsdienst einen Auftrag zum Nachverfolgen. Sie können den Status aller Aufträge auf der Seite **Sicherungsaufträgen** überwachen.

So öffnen Sie die Seite **Sicherungsaufträge**

1. Wählen Sie im **Backup Center** im Abschnitt **Überwachung** die Option **Sicherungsaufträge** aus.

   :::image type="content" source="./media/manage-afs-backup/backup-center-jobs-list-inline.png" alt-text="Screenshot: Sicherungsaufträge im Abschnitt „Überwachung“." lightbox="./media/manage-afs-backup/backup-center-jobs-list-expanded.png":::

   Im Bereich **Sicherungsaufträge** wird der Status aller Aufträge aufgelistet.

1. Wählen Sie **Azure Files (Azure Storage)** als Datenquellentyp und dann eine beliebige Zeile aus, um Details zum jeweiligen Auftrag anzuzeigen.

   :::image type="content" source="./media/manage-afs-backup/backup-center-jobs-inline.png" alt-text="Screenshot: Liste der Aufträge." lightbox="./media/manage-afs-backup/backup-center-jobs-expanded.png":::
   
    >[!NOTE]
    >Da keine Daten in den Datenspeicher übertragen werden, sind die übertragenen Daten in MB bei Sicherungsaufträgen, die Azure-Dateien entsprechen, 0.

## <a name="monitor-using-azure-backup-reports"></a>Überwachen mit Azure Backup-Berichten

Azure Backup bietet eine Berichtslösung, die [Azure Monitor Protokolle](../azure-monitor/logs/log-analytics-tutorial.md) und [Azure Arbeitsmappen](../azure-monitor/visualize/workbooks-overview.md) verwendet. Mit diesen Ressourcen erhalten Sie umfassende Einblicke in Ihre Backups. Anhand dieser Berichte können Sie Einblicke in Azure Files-Sicherungselemente, Aufträge auf Elementebene und Details aktiver Richtlinien erhalten. Mit dem in Sicherungsberichten verfügbaren Feature Bericht per E-Mail senden können Sie automatisierte Aufgaben zum Empfangen regelmäßiger Berichte per E-Mail erstellen. [Erfahren Sie](./configure-reports.md#get-started), wie Sie Azure Backup-Berichte konfigurieren und anzeigen.

## <a name="create-a-new-policy"></a>Erstellen einer neuen Richtlinie

Sie können im **Backup Center** im Abschnitt **Sicherungsrichtlinien** eine neue Richtlinie zum Sichern von Azure-Dateifreigaben erstellen. Für alle beim Konfigurieren der Sicherung für Dateifreigaben erstellten Richtlinien wird als **Richtlinientyp** der Wert **Azure-Dateifreigabe** angezeigt.

Gehen Sie wie folgt vor, um eine neue Sicherungsrichtlinie zu erstellen:

1. Wählen Sie im Fenster **Sicherungsrichtlinien** im **Backup Center** die Option **+Hinzufügen** aus.

   :::image type="content" source="./media/manage-afs-backup/backup-center-add-policy-inline.png" alt-text="Screenshot zeigt die Option zum Starten der Erstellung einer neuen Sicherungsrichtlinie." lightbox="./media/manage-afs-backup/backup-center-add-policy-expanded.png":::

1. Wählen Sie **Azure Files (Azure Storage)** als Datenquellentyp und anschließend den Tresor aus, unter dem die Richtlinie erstellt werden soll, und klicken Sie dann auf **Weiter**.

   :::image type="content" source="./media/manage-afs-backup/azure-file-share-select-vault-for-policy.png" alt-text="Screenshot: Auswählen einer Azure-Dateifreigabe als Richtlinientyp.":::

1. Wenn sich das Fenster **Backup-Richtlinie** für **Azure File Share** öffnet, geben Sie den Namen der Richtlinie an.

1. Wählen Sie in **Backup-Zeitplan** eine geeignete Häufigkeit für die Backups - **Täglich** oder **Stündlich**.

   :::image type="content" source="./media/manage-afs-backup/backup-frequency-types.png" alt-text="Screenshot mit den Häufigkeitstypen für Backups.":::

   - **Täglich**: Löst eine Sicherung pro Tag aus. Für die tägliche Häufigkeit wählen Sie die entsprechenden Werte für:

     - **Zeit**: Der Zeitstempel, zu dem der Sicherungsauftrag ausgelöst werden muss.
     - **Zeitzone**: Die entsprechende Zeitzone für den Sicherungsauftrag.

   - **Stündlich**: Löst mehrere Backups pro Tag aus. Für die stündliche Häufigkeit wählen Sie die entsprechenden Werte für:
   
     - **Zeitplan**: Das Zeitintervall (in Stunden) zwischen den aufeinanderfolgenden Backups.
     - **Startzeit**: Die Uhrzeit, zu der das erste Backup des Tages ausgelöst werden muss.
     - **Dauer**: Stellt das Sicherungsfenster (in Stunden) dar, d. h. die Zeitspanne, in der die Sicherungsaufträge gemäß dem ausgewählten Zeitplan ausgelöst werden müssen.
     - **Zeitzone**: Die entsprechende Zeitzone für den Sicherungsauftrag.
     
     Ein Beispiel: Sie haben eine RPO-Anforderung (Recovery Point Objective) von 4 Stunden und Ihre Arbeitszeiten sind von 9 Uhr morgens bis 9 [21:00] Uhr abends. Um diese Anforderungen zu erfüllen, wäre die Konfiguration für den Sicherungsplan wie folgt:
    
     - Zeitplan: Alle 4 Stunden
     - Startzeit: 9 AM 
     - Dauer: 12 Stunden 
     
     :::image type="content" source="./media/manage-afs-backup/hourly-backup-frequency-values-scenario.png" alt-text="Screenshot mit einem Beispiel für die stündliche Sicherungshäufigkeit ":::.

     Basierend auf Ihrer Auswahl werden die Details des Sicherungsauftrags (die Zeitstempel, zu denen der Sicherungsauftrag ausgelöst wird) auf dem Sicherungsrichtlinien-Blade angezeigt.

1. Legen Sie unter **Aufbewahrungsbereich** geeignete Aufbewahrungswerte für Backups fest - gekennzeichnet als täglich, wöchentlich, monatlich oder jährlich.

1. Nachdem Sie alle Attribute der Richtlinie definiert haben, klicken Sie auf **Erstellen**.
  
### <a name="view-policy"></a>Anzeigen der Richtlinie

So zeigen Sie die vorhandenen Sicherungsrichtlinien an

1. Wählen Sie im **Backup Center** im Abschnitt **Verwalten** die Option **Sicherungsrichtlinien** aus.

   Alle Sicherungsrichtlinien, die für Ihren Tresor konfiguriert sind, werden angezeigt.

   :::image type="content" source="./media/manage-afs-backup/backup-center-policies-list-inline.png" alt-text="Screenshot mit allen Sicherungsrichtlinien." lightbox="./media/manage-afs-backup/backup-center-policies-list-expanded.png":::

1. Um für **Azure Files (Azure Storage)** spezifische Richtlinien anzuzeigen, wählen Sie **Azure-Dateifreigabe** als Datenquellentyp aus.

## <a name="modify-policy"></a>Ändern der Richtlinie

Sie können eine Sicherungsrichtlinie bearbeiten, um die Sicherungshäufigkeit oder die Aufbewahrungsdauer zu ändern.

So ändern Sie eine Richtlinie

1. Wählen Sie im **Backup Center** im Abschnitt **Verwalten** die Option **Sicherungsrichtlinien** aus.

   Alle Sicherungsrichtlinien, die für Ihre Tresore konfiguriert sind, werden angezeigt.

   :::image type="content" source="./media/manage-afs-backup/backup-center-policies-list-inline.png" alt-text="Screenshot aller Sicherungsrichtlinien im Tresor." lightbox="./media/manage-afs-backup/backup-center-policies-list-expanded.png":::

1. Um die Richtlinien für eine bestimmte Azure-Dateifreigabe anzuzeigen, wählen Sie **Azure Files (Azure Storage)** spezifische als Datenquellentyp aus.

   Klicken Sie auf die Richtlinie, die Sie aktualisieren möchten.

1. Der Bereich **Zeitplan** wird geöffnet. Bearbeiten Sie **Sicherungszeitplan** und **Aufbewahrungszeitraum** wie gewünscht, und wählen Sie **Speichern** aus.

   Im Bereich wird eine Meldung _Update wird ausgeführt_ angezeigt. Nachdem die Richtlinienänderungen erfolgreich aktualisiert wurden, wird die Meldung _Die Sicherungsrichtlinie wurde erfolgreich aktualisiert_ angezeigt.

   ![Geänderte Richtlinie speichern](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>Beenden des Schutzes für eine Dateifreigabe

Der Schutz für Azure-Dateifreigaben kann auf zwei Arten beendet werden:

* Beenden aller zukünftigen Sicherungsaufträge und *Löschen* aller Wiederherstellungspunkte.
* Beenden aller zukünftigen Sicherungsaufträge unter *Beibehaltung* der Wiederherstellungspunkte.

Unter Umständen fallen für die Aufbewahrung der Wiederherstellungspunkte im Speicher Gebühren an, da die von Azure Backup erstellten zugrundeliegenden Momentaufnahmen beibehalten werden. Der Vorteil der Beibehaltung von Wiederherstellungspunkten ist, dass Sie die Dateifreigabe später wiederherstellen können. Informationen zu den Kosten, die durch die Beibehaltung der Wiederherstellungspunkte entstehen, finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/backup/). Wenn Sie alle Wiederherstellungspunkte löschen, kann die Dateifreigabe nicht mehr wiederhergestellt werden.

So beenden Sie den Schutz für eine Azure-Dateifreigabe

1. Wechseln Sie zum **Backup Center**, wählen Sie im Menü die Option **Sicherungsinstanzen** und dann **Azure Storage (Azure Files)** als Datenquellentyp aus.

   :::image type="content" source="./media/manage-afs-backup/azure-file-share-backup-instances-inline.png" alt-text="Screenshot: Auswählen von „Azure Files“ als Datentyp." lightbox="./media/manage-afs-backup/azure-file-share-backup-instances-expanded.png":::

1. Wählen Sie das Sicherungselement aus, für das Sie den Schutz beenden möchten.

1. Wählen Sie die Option **Sicherung beenden** aus.

   ![Auswählen von „Sicherung beenden“](./media/manage-afs-backup/stop-backup.png)

1. Wählen Sie im Bereich **Sicherung beenden** die Option **Sicherungsdaten beibehalten** oder **Sicherungsdaten löschen** aus. Wählen Sie dann **Sicherung beenden** aus.

    ![„Sicherungsdaten beibehalten“ oder „Sicherungsdaten löschen“ auswählen](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>Fortsetzen des Schutzes für eine Dateifreigabe

Wenn beim Beenden des Schutzes für die Dateifreigabe die Option **Sicherungsdaten beibehalten** ausgewählt wurde, kann der Schutz fortgesetzt werden. Wenn die Option **Sicherungsdaten löschen** ausgewählt wurde, kann der Schutz für die Dateifreigabe nicht fortgesetzt werden.

So setzen Sie den Schutz für eine Azure-Dateifreigabe fort

1. Wechseln Sie zum **Backup Center**, wählen Sie im Menü die Option **Sicherungsinstanzen** und dann **Azure Storage (Azure Files)** als Datenquellentyp aus.

   :::image type="content" source="./media/manage-afs-backup/azure-file-share-backup-instances-inline.png" alt-text="Screenshot: Auswählen von „Azure Files“ als Datenquellentyp." lightbox="./media/manage-afs-backup/azure-file-share-backup-instances-expanded.png":::

1. Wählen Sie das Sicherungselement aus, für das Sie den Schutz fortsetzen möchten.

1. Wählen Sie die Option **Sicherung fortsetzen** aus.

   ![„Sicherung fortsetzen“ auswählen](./media/manage-afs-backup/resume-backup.png)

1. Der Bereich **Sicherungsrichtlinie** wird geöffnet. Wählen Sie eine Richtlinie Ihrer Wahl aus, um die Sicherung fortzusetzen.

1. Nachdem Sie eine Sicherungsrichtlinie ausgewählt haben, wählen Sie **Speichern** aus.

   Im Portal wird eine Meldung _Update wird ausgeführt_ angezeigt. Nachdem die Sicherung erfolgreich fortgesetzt wurde, wird die Meldung _Die Sicherungsrichtlinie wurde für die geschützte Azure-Dateifreigabe erfolgreich aktualisiert_ angezeigt.

   ![Die Sicherungsrichtlinie wurde erfolgreich aktualisiert](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>Löschen von Sicherungsdaten

Sie können die Sicherung einer Dateifreigabe während des Auftrags **Sicherung anhalten** oder jederzeit nach Beendigung des Schutzes löschen. Möglicherweise ist es von Vorteil, mit dem Löschen der Wiederherstellungspunkte mehrere Tage oder sogar Wochen zu warten. Sie können beim Löschen von Sicherungsdaten keine bestimmten Wiederherstellungspunkte auswählen, die gelöscht werden sollen. Wenn Sie sich für die Löschung Ihrer Sicherungsdaten entscheiden, werden alle der Dateifreigabe zugeordneten Wiederherstellungspunkte gelöscht.

Bei der folgenden Vorgehensweise wird davon ausgegangen, dass der Schutz für die Dateifreigabe beendet wurde.

So löschen Sie Sicherungsdaten für die Azure-Dateifreigabe

1. Nach dem Beenden des Sicherungsauftrags sind die Optionen **Sicherung fortsetzen** und **Sicherungsdaten löschen** auf dem Dashboard **Sicherungselement** verfügbar. Wählen Sie die Option **Sicherungsdaten löschen** aus.

   ![Löschen von Sicherungsdaten](./media/manage-afs-backup/delete-backup-data.png)

1. Der Bereich **Sicherungsdaten löschen** wird geöffnet. Geben Sie den Namen der Dateifreigabe ein, um den Löschvorgang zu bestätigen. Geben Sie optional weitere Informationen in die Felder **Grund** oder **Kommentare** ein. Wenn Sie sicher sind, dass die Sicherungsdaten gelöscht werden sollen, wählen Sie **Löschen** aus.

   ![Datenlöschung bestätigen](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-a-storage-account"></a>Aufheben der Registrierung eines Speicherkontos

Wenn Sie Ihre Dateifreigaben in einem bestimmten Speicherkonto unter Verwendung eines anderen Recovery Services-Tresors schützen möchten, [beenden Sie zunächst den Schutz für alle Dateifreigaben](#stop-protection-on-a-file-share) in diesem Speicherkonto. Heben Sie dann die Registrierung des Kontos im aktuellen zum Schutz verwendeten Recovery Services-Tresor auf.

Bei der folgenden Vorgehensweise wird davon ausgegangen, dass der Schutz für alle Dateifreigaben in dem Speicherkonto, dessen Registrierung Sie aufheben möchten, beendet wurde.

So heben Sie die Registrierung eines Speicherkontos auf

1. Öffnen Sie den Recovery Services-Tresor, bei dem Ihr Speicherkonto registriert ist.
1. Wählen Sie im Abschnitt **Verwalten** des Bereichs **Übersicht** die Option **Sicherungsinfrastruktur** aus.

   ![Auswählen von „Sicherungsinfrastruktur“](./media/manage-afs-backup/backup-infrastructure.png)

1. Der Bereich **Sicherungsinfrastruktur** wird geöffnet. Wählen Sie im Abschnitt **Azure Storage-Konten** die Option **Speicherkonten** aus.

   ![„Speicherkonten“ auswählen](./media/manage-afs-backup/storage-accounts.png)

1. Nachdem Sie **Speicherkonten** ausgewählt haben, wird eine Liste der beim Tresor registrierten Speicherkonten angezeigt.
1. Klicken Sie mit der rechten Maustaste auf das Speicherkonto, dessen Registrierung Sie aufheben möchten, und wählen Sie **Registrierung aufheben** aus.

   ![„Registrierung aufheben“ auswählen](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Behandeln von Problemen beim Sichern von Azure-Dateifreigaben](./troubleshoot-azure-files.md).
