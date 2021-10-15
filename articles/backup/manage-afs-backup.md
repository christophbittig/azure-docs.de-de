---
title: Verwalten der Sicherungen von Azure-Dateifreigaben
description: In diesem Artikel werden allgemeine Aufgaben zur Verwaltung und Überwachung der Azure-Dateifreigaben beschrieben, die durch Azure Backup gesichert werden.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 973c28b2c8caac4d2acda9e2cd976f9ceb8c387c
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129534045"
---
# <a name="manage-azure-file-share-backups"></a>Verwalten der Sicherungen von Azure-Dateifreigaben

In diesem Artikel werden allgemeine Aufgaben zur Verwaltung und Überwachung der Azure-Dateifreigaben beschrieben, die durch [Azure Backup](./backup-overview.md) gesichert werden. Sie lernen, wie Sie Verwaltungsaufgaben im Recovery Services-Tresor ausführen.

## <a name="monitor-jobs"></a>Überwachen von Aufträgen

Wenn Sie einen Sicherungs- oder Wiederherstellungsvorgang auslösen, erstellt der Sicherungsdienst einen Auftrag zum Nachverfolgen. Sie können den Status aller Aufträge auf der Seite **Sicherungsaufträgen** überwachen.

So öffnen Sie die Seite **Sicherungsaufträge**

1. Öffnen Sie den Recovery Services-Tresor, den Sie zur Konfiguration der Sicherung Ihrer Dateifreigaben verwendet haben. Wählen Sie im Bereich **Übersicht** im Abschnitt **Überwachung** die Option **Sicherungsaufträge**.

   ![Sicherungsaufträge im Abschnitt „Überwachung“](./media/manage-afs-backup/backup-jobs.png)

1. Nachdem Sie **OK** ausgewählt haben, wird im Bereich **Sicherungsaufträge** der Status aller Aufträge aufgelistet. Wählen Sie den Namen der Workload aus, die der Dateifreigabe entspricht, die Sie überwachen möchten.

   ![Workloadname](./media/manage-afs-backup/workload-name.png)

## <a name="monitor-using-azure-backup-reports"></a>Überwachen mit Azure Backup-Berichten

Azure Backup stellt eine Lösung zur Berichterstellung bereit, die [Azure Monitor-Protokolle](../azure-monitor/logs/log-analytics-tutorial.md) und [Azure-Arbeitsmappen](../azure-monitor/visualize/workbooks-overview.md) verwendet. Diese Ressourcen unterstützen Sie bei der Bereitstellung umfassender Erkenntnisse zu Ihren Sicherungen. Anhand dieser Berichte können Sie Einblicke in Azure Files-Sicherungselemente, Aufträge auf Elementebene und Details aktiver Richtlinien erhalten. Mit dem in Sicherungsberichten verfügbaren Feature „Bericht per E-Mail senden“ können Sie automatisierte Aufgaben zum Empfangen regelmäßiger Berichte per E-Mail erstellen. Weitere Informationen zum Konfigurieren und Anzeigen von Azure Backup-Berichten finden Sie [hier](/azure/backup/configure-reports#get-started).

## <a name="create-a-new-policy"></a>Erstellen einer neuen Richtlinie

Sie können im Recovery Services-Tresor im Abschnitt **Sicherungsrichtlinien** eine neue Richtlinie zum Sichern von Azure-Dateifreigaben erstellen. Für alle beim Konfigurieren der Sicherung für Dateifreigaben erstellten Richtlinien wird als **Richtlinientyp** der Wert **Azure-Dateifreigabe** angezeigt.

So zeigen Sie die vorhandenen Sicherungsrichtlinien an

1. Öffnen Sie den Recovery Services-Tresor, den Sie zur Konfiguration der Sicherung der Dateifreigabe verwendet haben. Wählen Sie im Menü „Recovery Services-Tresor“ im Abschnitt **Verwalten** die Option **Sicherungsrichtlinien** aus. Alle im Tresor konfigurierten Sicherungsrichtlinien werden angezeigt.

   ![Alle Sicherungsrichtlinien](./media/manage-afs-backup/all-backup-policies.png)

1. Um spezifische Richtlinien für **Azure-Dateifreigabe** anzuzeigen, wählen Sie **Azure-Dateifreigabe** aus der Dropdownliste oben rechts aus.

   ![Azure-Dateifreigabe auswählen](./media/manage-afs-backup/azure-file-share.png)

So erstellen Sie eine neue Sicherungsrichtlinie

1. Wählen Sie im Bereich **Sicherungsrichtlinien** die Option **+Hinzufügen** aus.

   ![Neue Sicherungsrichtlinie](./media/manage-afs-backup/new-backup-policy.png)

1. Wählen Sie im Bereich **Hinzufügen** die Option **Azure-Dateifreigabe** als **Richtlinientyp** aus. Der Bereich **Sicherungsrichtlinie** für **Azure-Dateifreigabe** wird geöffnet. Geben Sie den Richtliniennamen, die Sicherungshäufigkeit und die Aufbewahrungsdauer für die Wiederherstellungspunkte an. Nachdem Sie die Richtlinie definiert haben, wählen Sie **OK** aus.

   ![Sicherungsrichtlinie definieren](./media/manage-afs-backup/define-backup-policy.png)

## <a name="modify-policy"></a>Ändern der Richtlinie

Sie können eine Sicherungsrichtlinie bearbeiten, um die Sicherungshäufigkeit oder die Aufbewahrungsdauer zu ändern.

So ändern Sie eine Richtlinie

1. Öffnen Sie den Recovery Services-Tresor, den Sie zur Konfiguration der Sicherung der Dateifreigabe verwendet haben. Wählen Sie im Menü „Recovery Services-Tresor“ im Abschnitt **Verwalten** die Option **Sicherungsrichtlinien** aus. Alle im Tresor konfigurierten Sicherungsrichtlinien werden angezeigt.

   ![Alle Sicherungsrichtlinien im Tresor](./media/manage-afs-backup/all-backup-policies-modify.png)

1. Um spezifische Richtlinien für eine Azure-Dateifreigabe anzuzeigen, wählen Sie **Azure-Dateifreigabe** aus der Dropdownliste oben rechts aus. Wählen Sie die Sicherungsrichtlinie aus, die Sie ändern möchten.

   ![Azure-Dateifreigabe, die Sie ändern möchten](./media/manage-afs-backup/azure-file-share-modify.png)

1. Der Bereich **Zeitplan** wird geöffnet. Bearbeiten Sie **Sicherungszeitplan** und **Aufbewahrungszeitraum** wie gewünscht, und wählen Sie **Speichern** aus. Im Bereich wird eine Meldung „Update wird ausgeführt“ angezeigt. Nachdem die Richtlinienänderungen erfolgreich aktualisiert wurden, wird die Meldung „Die Sicherungsrichtlinie wurde erfolgreich aktualisiert“ angezeigt.

   ![Geänderte Richtlinie speichern](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>Beenden des Schutzes für eine Dateifreigabe

Der Schutz für Azure-Dateifreigaben kann auf zwei Arten beendet werden:

* Beenden aller zukünftigen Sicherungsaufträge und *Löschen* aller Wiederherstellungspunkte.
* Beenden aller zukünftigen Sicherungsaufträge unter *Beibehaltung* der Wiederherstellungspunkte.

Unter Umständen fallen für die Aufbewahrung der Wiederherstellungspunkte im Speicher Gebühren an, da die von Azure Backup erstellten zugrundeliegenden Momentaufnahmen beibehalten werden. Der Vorteil der Beibehaltung von Wiederherstellungspunkten ist, dass Sie die Dateifreigabe später wiederherstellen können. Informationen zu den Kosten, die durch die Beibehaltung der Wiederherstellungspunkte entstehen, finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/backup/). Wenn Sie alle Wiederherstellungspunkte löschen, kann die Dateifreigabe nicht mehr wiederhergestellt werden.

So beenden Sie den Schutz für eine Azure-Dateifreigabe

1. Öffnen Sie den Recovery Services-Tresor, der die Wiederherstellungspunkte der Dateifreigaben enthält. Wahlen Sie im Abschnitt **Geschützte Elemente** die Option **Sicherungselemente** aus. Die Liste der Sicherungselementtypen wird angezeigt.

   ![Sicherungselemente](./media/manage-afs-backup/backup-items.png)

1. Wählen Sie in der Liste **Backup Management Type** (Sicherungsverwaltungstyp) den Eintrag **Azure Storage (Azure Files)** aus. Die Liste der **Sicherungselemente für Azure Storage (Azure Files)** wird angezeigt.

   ![Azure Storage (Azure Files) auswählen](./media/manage-afs-backup/azure-storage-azure-files.png)

1. Wählen Sie in der Liste der **Sicherungselemente für Azure Storage (Azure Files)** das Sicherungselement aus, für das der Schutz beendet werden soll.

1. Wählen Sie die Option **Sicherung beenden** aus.

   ![Auswählen von „Sicherung beenden“](./media/manage-afs-backup/stop-backup.png)

1. Wählen Sie im Bereich **Sicherung beenden** die Option **Sicherungsdaten beibehalten** oder **Sicherungsdaten löschen** aus. Wählen Sie dann **Sicherung beenden** aus.

    ![„Sicherungsdaten beibehalten“ oder „Sicherungsdaten löschen“ auswählen](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>Fortsetzen des Schutzes für eine Dateifreigabe

Wenn beim Beenden des Schutzes für die Dateifreigabe die Option **Sicherungsdaten beibehalten** ausgewählt wurde, kann der Schutz fortgesetzt werden. Wenn die Option **Sicherungsdaten löschen** ausgewählt wurde, kann der Schutz für die Dateifreigabe nicht fortgesetzt werden.

So setzen Sie den Schutz für eine Azure-Dateifreigabe fort

1. Öffnen Sie den Recovery Services-Tresor, der die Wiederherstellungspunkte der Dateifreigaben enthält. Wahlen Sie im Abschnitt **Geschützte Elemente** die Option **Sicherungselemente** aus. Die Liste der Sicherungselementtypen wird angezeigt.

   ![Sicherungselemente für die Fortsetzung](./media/manage-afs-backup/backup-items-resume.png)

1. Wählen Sie in der Liste **Backup Management Type** (Sicherungsverwaltungstyp) den Eintrag **Azure Storage (Azure Files)** aus. Die Liste der **Sicherungselemente für Azure Storage (Azure Files)** wird angezeigt.

   ![Liste von Azure Storage (Azure Files)](./media/manage-afs-backup/azure-storage-azure-files.png)

1. Wählen Sie in der Liste der **Sicherungselemente für Azure Storage (Azure Files)** das Sicherungselement aus, für das der Schutz fortgesetzt werden soll.

1. Wählen Sie die Option **Sicherung fortsetzen** aus.

   ![„Sicherung fortsetzen“ auswählen](./media/manage-afs-backup/resume-backup.png)

1. Der Bereich **Sicherungsrichtlinie** wird geöffnet. Wählen Sie eine Richtlinie Ihrer Wahl aus, um die Sicherung fortzusetzen.

1. Nachdem Sie eine Sicherungsrichtlinie ausgewählt haben, wählen Sie **Speichern** aus. Im Portal wird eine Meldung „Update wird ausgeführt“ angezeigt. Nachdem die Sicherung erfolgreich fortgesetzt wurde, wird die Meldung „Die Sicherungsrichtlinie wurde für die geschützte Azure-Dateifreigabe erfolgreich aktualisiert“ angezeigt.

   ![Die Sicherungsrichtlinie wurde erfolgreich aktualisiert](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>Löschen von Sicherungsdaten

Sie können die Sicherung einer Dateifreigabe während des Auftrags zum **Beenden der Sicherung** oder jederzeit nach Beenden des Schutzes löschen. Möglicherweise ist es von Vorteil, mit dem Löschen der Wiederherstellungspunkte mehrere Tage oder sogar Wochen zu warten. Sie können beim Löschen von Sicherungsdaten keine bestimmten Wiederherstellungspunkte auswählen, die gelöscht werden sollen. Wenn Sie sich für die Löschung Ihrer Sicherungsdaten entscheiden, werden alle der Dateifreigabe zugeordneten Wiederherstellungspunkte gelöscht.

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
