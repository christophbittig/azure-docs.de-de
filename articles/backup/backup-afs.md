---
title: Sichern von Azure-Dateifreigaben im Azure-Portal
description: Erfahren Sie, wie Sie das Azure-Portal zum Sichern von Azure-Dateifreigaben im Recovery Services-Tresor verwenden.
ms.topic: conceptual
ms.date: 11/03/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 46068722385e9cf89c5c85d37a72a0528479ab8a
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131849728"
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

Führen Sie die folgenden Schritte aus, um die Sicherung für mehrere Dateifreigaben im Bereich des Recovery Services-Tresors zu konfigurieren:

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com/) zu **Backup Center**, und klicken Sie auf **+Sichern**.

   :::image type="content" source="./media/backup-afs/backup-center-configure-inline.png" alt-text="Screenshot: Konfigurieren der Sicherung für Azure Files." lightbox="./media/backup-afs/backup-center-configure-expanded.png":::

1. Wählen Sie **Azure Files (Azure Storage)** als Datenquellentyp aus, wählen Sie den Tresor aus, mit dem Sie die Dateifreigaben schützen möchten, und klicken Sie dann auf **Weiter**.

   :::image type="content" source="./media/backup-afs/azure-file-share-select-vault.png" alt-text="Screenshot: Auswählen von „Azure Files“.":::

1. Klicken Sie auf **Auswählen**, um das Speicherkonto auszuwählen, das die zu sichernden Dateifreigaben enthält.

   Der Bereich **Speicherkonto auswählen**  wird auf der rechten Seite geöffnet, in dem eine Reihe erkannter unterstützter Speicherkonten aufgeführt ist. Sie sind entweder diesem Tresor zugeordnet oder befinden sich in derselben Region wie der Tresor, sind aber noch keinem Recovery Services-Tresor zugeordnet.

   :::image type="content" source="./media/backup-afs/azure-file-share-select-storage-account-inline.png" alt-text="Screenshot: Auswählen eines Speicherkontos." lightbox="./media/backup-afs/azure-file-share-select-storage-account-expanded.png":::

1. Wählen Sie aus der Liste der ermittelten Speicherkonten ein Speicherkonto und dann **OK** aus.

   :::image type="content" source="./media/backup-afs/azure-file-share-confirm-storage-account-inline.png" alt-text="Screenshot: Auswählen eines der ermittelten Speicherkonten." lightbox="./media/backup-afs/azure-file-share-confirm-storage-account-expanded.png":::
   
   >[!NOTE]
   >Wenn ein Speicherkonto in einer anderen Region als der Tresor vorhanden ist, ist es nicht in der Liste der ermittelten Speicherkonten enthalten.

1. Der nächste Schritt ist das Auswählen der zu sichernden Dateifreigaben. Wählen Sie im Abschnitt **FileShares to Backup** (Zu sichernde Dateifreigaben) die Schaltfläche **Hinzufügen** aus.

   :::image type="content" source="./media/backup-afs/azure-select-file-share-inline.png" alt-text="Screenshot: Auswählen der zu sichernden Dateifreigabe." lightbox="./media/backup-afs/azure-select-file-share-expanded.png":::

1. Der Kontextbereich **Dateifreigaben auswählen** wird auf der rechten Seite geöffnet. Azure durchsucht das Speicherkonto nach Dateifreigaben, die gesichert werden können. Falls Sie die Dateifreigaben erst kürzlich hinzugefügt haben und diese nicht in der Liste angezeigt werden, warten Sie einen Moment, bis die Dateifreigaben angezeigt werden.

1. Wählen Sie in der Liste **Dateifreigaben auswählen** mindestens eine zu sichernde Dateifreigabe aus. Klicken Sie auf **OK**.

1. Für die Auswahl einer Sicherungsrichtlinie für Ihre Dateifreigabe gibt es drei Optionen:

   * Die Standardrichtlinie wählen.<br>
   Diese Option ermöglicht Ihnen, eine tägliche Sicherung mit einer Aufbewahrung von 30 Tagen zu aktivieren. Wenn keine Sicherungsrichtlinie im Tresor vorhanden ist, wird der Sicherungsbereich mit den Standardrichtlinieneinstellungen geöffnet. Wenn Sie die Standardeinstellungen verwenden möchten, können Sie direkt **Sicherung aktivieren** auswählen.

   * Erstellen einer neuen Richtlinie <br>

      1. Um eine neue Sicherungsrichtlinie für Ihre Dateifreigabe zu erstellen, wählen Sie im Abschnitt **Sicherungsrichtlinie** den Linktext unter der Dropdownliste aus.<br>

         :::image type="content" source="./media/backup-afs/azure-file-share-edit-policy-inline.png" alt-text="Screenshot: Erstellen einer neuen Richtlinie." lightbox="./media/backup-afs/azure-file-share-edit-policy-expanded.png":::

      1. Führen Sie die Schritte 3–7 im Abschnitt [Erstellen einer neuen Richtlinie](manage-afs-backup.md#create-a-new-policy) aus.

      1. Klicken Sie nach dem Definieren aller Attribute der Richtlinie auf **OK**.

         :::image type="content" source="./media/backup-afs/azure-file-share-policy-parameters-inline.png" alt-text="Screenshot: Angeben des Richtliniennamens und der Werte für die Aufbewahrung." lightbox="./media/backup-afs/azure-file-share-policy-parameters-expanded.png":::

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

         2. Führen Sie die Schritte 3–7 im Abschnitt [Erstellen einer neuen Richtlinie](manage-afs-backup.md#create-a-new-policy) aus.

         3. Klicken Sie nach dem Definieren aller Attribute der Richtlinie auf **OK**.

            ![Erstellen einer neuen Sicherungsrichtlinie](./media/backup-afs/create-new-backup-policy.png)

1. Wählen Sie **Sicherung aktivieren** aus, um den Schutz der Dateifreigabe zu aktivieren.

   ![Auswählen von „Sicherung aktivieren“](./media/backup-afs/select-enable-backup.png)

1. Sie können den Konfigurationsfortschritt in den Portalbenachrichtigungen nachverfolgen oder die Sicherungsaufträge unter dem Tresor überwachen, den Sie zum Schutz der Dateifreigabe verwenden.

   ![Portalbenachrichtigungen](./media/backup-afs/portal-notifications.png)

1. Wählen Sie nach Abschluss des Konfigurationsvorgangs für die Sicherung im Bereich der Dateifreigabe unter **Vorgänge** die Option **Sichern** aus. Der Kontextbereich, in dem **Vault Essentials** (Grundlegende Tresorvorgänge) aufgeführt ist, wird auf der rechten Seite geladen. Dort können Sie bedarfsgesteuerte Sicherungs- und Wiederherstellungsvorgänge auslösen.

   ![Grundlegende Tresorvorgänge](./media/backup-afs/vault-essentials.png)

## <a name="run-an-on-demand-backup-job"></a>Ausführen eines bedarfsgesteuerten Sicherungsauftrag

Gelegentlich empfiehlt es sich, eine Sicherungsmomentaufnahme oder einen Wiederherstellungspunkt außerhalb der geplanten Zeiten in der Sicherungsrichtlinie zu erstellen. Häufig ist es sinnvoll, eine bedarfsgesteuerte Sicherung direkt nach dem Konfigurieren der Sicherungsrichtlinie zu erstellen. Basierend auf den Zeitplan in der Sicherungsrichtlinie, kann es Stunden oder Tage dauern, bis eine Momentaufnahme erstellt wird. Initiieren Sie eine bedarfsgesteuerte Sicherung, um Ihre Daten zu schützen, bevor die Sicherungsrichtlinie in Kraft tritt. Die Erstellung einer bedarfsgesteuerten Sicherung ist häufig erforderlich, bevor Sie geplante Änderungen an den Dateifreigaben vornehmen.

### <a name="from-backup-center"></a>Aus dem Backup Center

1. Klicken Sie im **Backup Center** im Menü auf **Sicherungsinstanzen**.

   Filtern Sie nach **Azure Files (Azure Storage)** als Datenquellentyp.

   :::image type="content" source="./media/backup-afs/azure-file-share-backup-instances-inline.png" alt-text="Screenshot: Auswählen von Sicherungsinstanzen." lightbox="./media/backup-afs/azure-file-share-backup-instances-expanded.png":::

1. Wählen Sie das Element aus, für das Sie einen bedarfsgesteuerten Sicherungsauftrag ausführen möchten.

1. Wählen Sie im Menü **Sicherungselement** die Option **Jetzt sichern** aus. Da es sich um einen bedarfsgesteuerten Sicherungsauftrag handelt, ist dem Wiederherstellungspunkt keine Aufbewahrungsrichtlinie zugeordnet.

   :::image type="content" source="./media/backup-afs/azure-file-share-backup-now-inline.png" alt-text="Screenshot: Auswählen von „Jetzt sichern“." lightbox="./media/backup-afs/azure-file-share-backup-now-expanded.png":::

1. Der Bereich **Jetzt sichern** wird geöffnet. Geben Sie den Tag an, bis zu dem der Wiederherstellungspunkt aufbewahrt werden soll. Bedarfsgesteuerte Sicherung können maximale 10 Jahre aufbewahrt werden.

   :::image type="content" source="./media/backup-afs/azure-file-share-on-demand-backup-retention.png" alt-text="Screenshot: Auswählen des Aufbewahrungsdatums.":::

1. Wählen Sie **OK** aus, um die Ausführung des bedarfsgesteuerten Sicherungsauftrags zu bestätigen.

1. Überwachen Sie die Portalbenachrichtigungen, um den Abschluss der Ausführung des Sicherungsauftrags zu verfolgen.

   Um den Auftragsfortschritt im **Backup Center**-Dashboard zu überwachen, wählen Sie **Backup Center** -> **Sicherungsaufträge** -> **In Bearbeitung** aus.

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
