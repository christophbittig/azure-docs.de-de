---
title: Sichern von Azure-Dateifreigaben im Azure-Portal
description: Erfahren Sie, wie Sie das Azure-Portal zum Sichern von Azure-Dateifreigaben im Recovery Services-Tresor verwenden.
ms.topic: conceptual
ms.date: 05/07/2021
ms.openlocfilehash: 807a173112d0bb85c008403028ab76000a3096db
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/04/2021
ms.locfileid: "129427968"
---
# <a name="back-up-azure-file-shares"></a>Sichern von Azure-Dateifreigaben

In diesem Artikel wird beschrieben, wie Sie mithilfe des Azure-Portals [Azure-Dateifreigaben](../storage/files/storage-files-introduction.md) sichern.

In diesem Artikel lernen Sie Folgendes:

* Erstellen Sie einen Recovery Services-Tresor.
* Konfigurieren der Sicherung über den Recovery Services-Tresor
* Konfigurieren der Sicherung über den Bereich „Dateifreigabe“
* Ausführen eines bedarfsbasierten Sicherungsauftrags zum Erstellen eines Wiederherstellungspunkts

## <a name="prerequisites"></a>Voraussetzungen

* [Erfahren Sie etwas](azure-file-share-backup-overview.md) über die auf Momentaufnahmen basierende Sicherungslösung für Azure-Dateifreigaben.
* Stellen Sie sicher, dass sich die Dateifreigabe in einem der [unterstützten Speicherkontotypen](azure-file-share-support-matrix.md) befindet.
* Bestimmen oder erstellen Sie einen [Recovery Services-Tresor](#create-a-recovery-services-vault) in derselben Region und im selben Abonnement wie das Speicherkonto, das die Dateifreigabe hostet.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="configure-backup-from-the-recovery-services-vault"></a>Konfigurieren der Sicherung über den Recovery Services-Tresor

In den folgenden Schritten wird erläutert, wie Sie die Sicherung für mehrere Dateifreigaben im Bereich „Recovery Services-Tresor“ konfigurieren können:

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com/) den Recovery Services-Tresor, den Sie zur Konfiguration der Sicherung für die Dateifreigabe verwenden möchten.

1. Wählen Sie im Bereich **Recovery Services-Tresor** im Menü oben die Option **+Sicherung** aus.

   ![Recovery Services-Tresor](./media/backup-afs/recovery-services-vault.png)

    1. Legen Sie im Bereich **Sicherungsziel** die Option **Wo wird Ihre Workload ausgeführt?** auf **Azure** fest, indem Sie in der Dropdownliste **Azure** auswählen.

          ![Azure als Workload wählen](./media/backup-afs/backup-goal.png)

    2. Wählen Sie unter **Was möchten Sie sichern?** aus der Dropdownliste die Option **Azure-Dateifreigabe** aus.

          ![Azure-Dateifreigabe auswählen](./media/backup-afs/select-azure-file-share.png)

    3. Wählen Sie **Sicherung** aus, um die Azure-Dateifreigabeerweiterung im Tresor zu registrieren.

          ![Wählen Sie „Sicherung“ aus, um die Azure-Dateifreigabe mit dem Tresor zu verknüpfen.](./media/backup-afs/register-extension.png)

1. Nachdem Sie **Sicherung** ausgewählt haben, wird der Bereich **Sicherung** geöffnet. Um das Speicherkonto auszuwählen, in dem die zu schützende Dateifreigabe gehostet wird, wählen Sie unter dem Textfeld **Speicherkonto** den Linktext **Auswählen** aus.

   ![Klicken auf den Link „Auswählen“](./media/backup-afs/choose-select-link.png)

1. Der Bereich **Speicherkonto auswählen**  wird auf der rechten Seite geöffnet, in dem eine Reihe erkannter unterstützter Speicherkonten aufgeführt ist. Sie sind entweder diesem Tresor zugeordnet oder befinden sich in derselben Region wie der Tresor, sind aber noch keinem Recovery Services-Tresor zugeordnet.

1. Wählen Sie aus der Liste der ermittelten Speicherkonten ein Speicherkonto und dann **OK** aus.

   ![In den erkannten Speicherkonten eine Wahl treffen](./media/backup-afs/select-discovered-storage-account.png)
   
   >[!NOTE]
   > Wenn ein Speicherkonto in einer anderen Region als der Tresor vorhanden ist, ist es nicht in der Liste der ermittelten Speicherkonten enthalten.

1. Der nächste Schritt ist das Auswählen der zu sichernden Dateifreigaben. Wählen Sie im Abschnitt **FileShares to Backup** (Zu sichernde Dateifreigaben) die Schaltfläche **Hinzufügen** aus.

   ![Auswählen der zu sichernden Dateifreigaben](./media/backup-afs/select-file-shares-to-back-up.png)

1. Der Kontextbereich **Dateifreigaben auswählen** wird auf der rechten Seite geöffnet. Azure durchsucht das Speicherkonto nach Dateifreigaben, die gesichert werden können. Falls Sie die Dateifreigaben erst kürzlich hinzugefügt haben und diese nicht in der Liste angezeigt werden, warten Sie einen Moment, bis die Dateifreigaben angezeigt werden.

1. Wählen Sie in der Liste **Dateifreigaben auswählen** mindestens eine zu sichernde Dateifreigabe aus. Klicken Sie auf **OK**.

   ![Auswählen der Dateifreigaben](./media/backup-afs/select-file-shares.png)

1. Für die Auswahl einer Sicherungsrichtlinie für Ihre Dateifreigabe gibt es drei Optionen:

   * Die Standardrichtlinie wählen.<br>
   Diese Option ermöglicht Ihnen, eine tägliche Sicherung mit einer Aufbewahrung von 30 Tagen zu aktivieren. Wenn keine Sicherungsrichtlinie im Tresor vorhanden ist, wird der Sicherungsbereich mit den Standardrichtlinieneinstellungen geöffnet. Wenn Sie die Standardeinstellungen verwenden möchten, können Sie direkt **Sicherung aktivieren** auswählen.

   * Erstellen einer neuen Richtlinie <br>

      1. Um eine neue Sicherungsrichtlinie für Ihre Dateifreigabe zu erstellen, wählen Sie im Abschnitt **Sicherungsrichtlinie** den Linktext unter der Dropdownliste aus.<br>

         ![Erstellen Sie eine neue Richtlinie.](./media/backup-afs/create-new-policy.png)

      1. Der Kontextbereich **Sicherungsrichtlinie** wird auf der rechten Seite geöffnet. Geben Sie einen Richtliniennamen in das Textfeld ein, und wählen Sie den Aufbewahrungszeitraum entsprechend Ihren Anforderungen. Nur die Aufbewahrungsoption „Täglich“ ist standardmäßig aktiviert. Wenn Sie eine wöchentliche, monatliche oder jährliche Aufbewahrung wünschen, aktivieren Sie das entsprechende Kontrollkästchen, und geben Sie die gewünschte Aufbewahrungsdauer an.

      1. Wählen Sie **OK** aus, nachdem Sie die Aufbewahrungsdauer und einen gültigen Richtliniennamen angegeben haben.<br>

         ![Richtliniennamen und Aufbewahrungsdauer angeben](./media/backup-afs/policy-name.png)

   * Eine der vorhandenen Sicherungsrichtlinien wählen <br>

      Um eine der vorhandenen Sicherungsrichtlinien für die Konfiguration des Schutzes zu wählen, wählen Sie die gewünschte Richtlinie in der Dropdownliste **Sicherungsrichtlinie** aus.<br>

      ![Vorhandene Richtlinie wählen](./media/backup-afs/choose-existing-policy.png)

1. Wählen Sie **Sicherung aktivieren** aus, um den Schutz der Dateifreigabe zu aktivieren.

   ![„Sicherung aktivieren“ wählen](./media/backup-afs/enable-backup.png)

Nachdem Sie eine Sicherungsrichtlinie festgelegt haben, wird zum geplanten Zeitpunkt eine Momentaufnahme der Dateifreigaben erstellt. Der Wiederherstellungspunkt wird ebenfalls für den gewählten Zeitraum beibehalten.


## <a name="configure-backup-from-the-file-share-pane"></a>Konfigurieren der Sicherung über den Bereich „Dateifreigabe“

In den folgenden Schritten wird erläutert, wie Sie die Sicherung für einzelne Dateifreigaben über den Bereich der jeweiligen Dateifreigabe konfigurieren können:

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com/) das Speicherkonto, in dem die zu sichernde Dateifreigabe gehostet wird.

1. Wählen Sie im Speicherkonto die Kachel mit der Bezeichnung **Dateifreigaben** aus. Sie können auch über das Inhaltsverzeichnis für das Speicherkonto zu **Dateifreigaben** navigieren.

   ![Speicherkonto](./media/backup-afs/storage-account.png)

1. In der Liste der Dateifreigaben sollten alle Dateifreigaben angezeigt werden, die im Speicherkonto vorhanden sind. Wählen Sie die zu sichernde Dateifreigabe aus.

   ![Liste der Dateifreigaben](./media/backup-afs/file-shares-list.png)

1. Wählen Sie im Bereich der Dateifreigabe im Bereich **Vorgänge** die Option **Sichern** aus. Der Bereich **Sicherung konfigurieren** wird auf der rechten Seite geladen.

   ![Bereich „Sicherung konfigurieren“](./media/backup-afs/configure-backup.png)

1. Führen Sie für die Auswahl des Recovery Services-Tresors einen der folgenden Schritte aus:

    * Wenn Sie bereits über einen Tresor verfügen, wählen Sie das Optionsfeld **Vorhandenen auswählen** für den Recovery Services-Tresor aus, und wählen Sie im Dropdownmenü **Tresorname** einen der vorhandenen Tresore aus.

       ![Auswählen eines vorhandenen Tresors](./media/backup-afs/select-existing-vault.png)

    * Wenn Sie nicht über einen Tresor verfügen, wählen Sie das Optionsfeld **Neu erstellen** für den Recovery Services-Tresor aus. Geben Sie einen Namen für den Tresor an. Er wird in derselben Region wie die Dateifreigabe erstellt. Standardmäßig wird der Tresor in derselben Ressourcengruppe wie die Dateifreigabe erstellt. Wenn Sie eine andere Ressourcengruppe auswählen möchten, wählen Sie unter der Dropdownliste **Ressourcentyp** die Option **Neu erstellen** aus, und geben Sie einen Namen für die Ressourcengruppe an. Klicken Sie auf **OK** , um fortzufahren.

       ![Erstellen eines neuen Tresors](./media/backup-afs/create-new-vault.png)

      >[!IMPORTANT]
      >Wenn das Speicherkonto bei einem Tresor registriert ist oder in dem Speicherkonto, in dem die zu schützende Dateifreigabe gehostet wird, nur wenige geschützte Freigaben vorhanden sind, wird der Name des Recovery Services-Tresors bereits ausgefüllt, und Sie können ihn nicht mehr bearbeiten. [Weitere Informationen finden Sie hier](backup-azure-files-faq.yml#why-can-t-i-change-the-vault-to-configure-backup-for-the-file-share-).

1. Führen Sie unter **Sicherungsrichtlinie** einen der folgenden Schritte aus:

    * Übernehmen Sie die Standardrichtlinie. Dadurch werden tägliche Sicherungen mit einer Aufbewahrungsdauer von 30 Tagen geplant.

    * Wählen Sie eine vorhandene Sicherungsrichtlinie im Dropdownmenü **Sicherungsrichtlinie** aus (sofern vorhanden).

       ![Auswählen der Sicherungsrichtlinie](./media/backup-afs/choose-backup-policy.png)

    * Erstellen Sie eine neue Richtlinie mit einer Aufbewahrungsdauer von Tagen/Wochen/Monaten/Jahren, entsprechend Ihren Anforderungen.  

         1. Wählen Sie den Linktext **Neue Richtlinie erstellen** aus.

         2. Der Kontextbereich **Sicherungsrichtlinie** wird auf der rechten Seite geöffnet. Geben Sie einen Richtliniennamen in das Textfeld ein, und wählen Sie den Aufbewahrungszeitraum entsprechend Ihren Anforderungen. Nur die Aufbewahrungsoption „Täglich“ ist standardmäßig aktiviert. Wenn Sie eine wöchentliche, monatliche oder jährliche Aufbewahrung wünschen, aktivieren Sie das entsprechende Kontrollkästchen, und geben Sie die gewünschte Aufbewahrungsdauer an.

         3. Wählen Sie **OK** aus, nachdem Sie die Aufbewahrungsdauer und einen gültigen Richtliniennamen angegeben haben.

            ![Erstellen einer neuen Sicherungsrichtlinie](./media/backup-afs/create-new-backup-policy.png)

1. Wählen Sie **Sicherung aktivieren** aus, um den Schutz der Dateifreigabe zu aktivieren.

   ![Auswählen von „Sicherung aktivieren“](./media/backup-afs/select-enable-backup.png)

1. Sie können den Konfigurationsfortschritt in den Portalbenachrichtigungen nachverfolgen oder die Sicherungsaufträge unter dem Tresor überwachen, den Sie zum Schutz der Dateifreigabe verwenden.

   ![Portalbenachrichtigungen](./media/backup-afs/portal-notifications.png)

1. Wählen Sie nach Abschluss des Konfigurationsvorgangs für die Sicherung im Bereich der Dateifreigabe unter **Vorgänge** die Option **Sichern** aus. Der Kontextbereich, in dem **Vault Essentials** (Grundlegende Tresorvorgänge) aufgeführt ist, wird auf der rechten Seite geladen. Dort können Sie bedarfsgesteuerte Sicherungs- und Wiederherstellungsvorgänge auslösen.

   ![Grundlegende Tresorvorgänge](./media/backup-afs/vault-essentials.png)

## <a name="run-an-on-demand-backup-job"></a>Ausführen eines bedarfsgesteuerten Sicherungsauftrag

Gelegentlich empfiehlt es sich, eine Sicherungsmomentaufnahme oder einen Wiederherstellungspunkt außerhalb der geplanten Zeiten in der Sicherungsrichtlinie zu erstellen. Häufig ist es sinnvoll, eine bedarfsgesteuerte Sicherung direkt nach dem Konfigurieren der Sicherungsrichtlinie zu erstellen. Basierend auf den Zeitplan in der Sicherungsrichtlinie, kann es Stunden oder Tage dauern, bis eine Momentaufnahme erstellt wird. Initiieren Sie eine bedarfsgesteuerte Sicherung, um Ihre Daten zu schützen, bevor die Sicherungsrichtlinie in Kraft tritt. Die Erstellung einer bedarfsgesteuerten Sicherung ist häufig erforderlich, bevor Sie geplante Änderungen an den Dateifreigaben vornehmen.

### <a name="from-the-recovery-services-vault"></a>Über den Recovery Services-Tresor

1. Öffnen Sie den Recovery Services-Tresor, den Sie zum Sichern Ihrer Dateifreigaben verwendet haben. Wahlen Sie auf dem Blatt **Übersicht** im Abschnitt **Geschützte Elemente** die Option **Sicherungselemente** aus.

   ![Sicherungselemente auswählen](./media/backup-afs/backup-items.png)

1. Nachdem Sie **Sicherungselemente** ausgewählt haben, wird neben dem Bereich **Übersicht** ein neuer Bereich angezeigt, in dem alle **Sicherungsverwaltungstypen** aufgelistet sind.

   ![Liste mit Sicherungsverwaltungstypen](./media/backup-afs/backup-management-types.png)

1. Wählen Sie aus der Liste der **Sicherungsverwaltungstypen** den Eintrag **Azure Storage (Azure Files)** aus. Angezeigt wird eine Liste aller Dateifreigaben und der entsprechenden Speicherkonten, die mit diesem Tresor gesichert werden.

   ![Azure Storage (Azure Files)-Sicherungselemente](./media/backup-afs/azure-files-backup-items.png)

1. Wählen Sie in der Liste der Azure-Dateifreigaben die gewünschte Dateifreigabe aus. Einzelheiten zum **Sicherungselement** werden angezeigt. Wählen Sie im Menü **Sicherungselement** die Option **Jetzt sichern** aus. Da es sich um einen bedarfsgesteuerten Sicherungsauftrag handelt, ist dem Wiederherstellungspunkt keine Aufbewahrungsrichtlinie zugeordnet.

   ![„Jetzt sichern“ auswählen](./media/backup-afs/backup-now.png)

1. Der Bereich **Jetzt sichern** wird geöffnet. Geben Sie den Tag an, bis zu dem der Wiederherstellungspunkt aufbewahrt werden soll. Bedarfsgesteuerte Sicherung können maximale 10 Jahre aufbewahrt werden.

   ![Wählen Sie das Aufbewahrungsdatum aus.](./media/backup-afs/retention-date.png)

1. Wählen Sie **OK** aus, um die Ausführung des bedarfsgesteuerten Sicherungsauftrags zu bestätigen.

1. Überwachen Sie die Portalbenachrichtigungen, um den Abschluss der Ausführung des Sicherungsauftrags zu verfolgen. Sie können den Auftragsstatus im Dashboard des Tresors überwachen. Wählen Sie dazu **Sicherungsaufträge** > **In Bearbeitung** aus.

### <a name="from-the-file-share-pane"></a>Über den Bereich der Dateifreigabe

1. Öffnen Sie den Bereich **Übersicht** für die Dateifreigabe, für die Sie eine bedarfsgesteuerte Sicherung erstellen möchten.

1. Wählen Sie im Abschnitt **Vorgänge** die Option **Sichern** aus. Der Kontextbereich, in dem **Vault Essentials** (Grundlegende Tresorvorgänge) aufgeführt ist, wird auf der rechten Seite geladen. Wählen Sie **Jetzt sichern** aus, um eine bedarfsgesteuerte Sicherung zu erstellen.

   ![Auswählen von „Jetzt sichern“](./media/backup-afs/select-backup-now.png)

1. Der Bereich **Jetzt sichern** wird geöffnet. Geben Sie die Aufbewahrungsdauer für den Wiederherstellungspunkt an. Bedarfsgesteuerte Sicherung können maximale 10 Jahre aufbewahrt werden.

   ![Datum für das Beibehalten der Sicherung](./media/backup-afs/retain-backup-date.png)

1. Wählen Sie zum Fortzufahren **OK** aus.

>[!NOTE]
>Azure Backup sperrt das Speicherkonto, wenn Sie Schutz für eine beliebige Dateifreigabe im entsprechenden Konto konfigurieren. Dies bietet Schutz vor dem versehentlichen Löschen eines Speicherkontos mit gesicherten Dateifreigaben.

## <a name="best-practices"></a>Bewährte Methoden

* Löschen Sie keine Momentaufnahmen, die mit Azure Backup erstellt wurden. Das Löschen von Momentaufnahmen kann zum Verlust von Wiederherstellungspunkten bzw. zu Wiederherstellungsfehlern führen.

* Entfernen Sie die von Azure Backup gesetzte Sperre für das Speicherkonto nicht. Wenn Sie die Sperre löschen, ist Ihr Speicherkonto für versehentliches Löschen anfällig, und wenn es gelöscht wird, gehen Ihre Momentaufnahmen oder Sicherungen verloren.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel werden folgende Themen erläutert:

* [Wiederherstellen von Azure-Dateifreigaben](restore-afs.md)
* [Verwalten der Sicherungen von Azure-Dateifreigaben](manage-afs-backup.md)
