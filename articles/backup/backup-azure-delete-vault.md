---
title: Löschen eines Microsoft Azure Recovery Services-Tresors
description: In diesem Artikel erfahren Sie, wie Sie die Abhängigkeiten eines Azure Backup-Recovery Services-Tresors aufheben und ihn dann löschen.
ms.topic: conceptual
ms.date: 06/07/2021
ms.openlocfilehash: 0eb5e7c48bf15583e96a5bf30149ba01ee7c2e33
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122340103"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>Löschen eines Azure Backup-Recovery Services-Tresors

In diesem Artikel wird beschrieben, wie Sie einen Recovery Services-Tresor von [Azure Backup](backup-overview.md) löschen. Außerdem enthält er Anweisungen zum Entfernen von Abhängigkeiten und anschließenden Löschen eines Tresors.

## <a name="before-you-start"></a>Vorbereitung

Es ist nicht möglich, einen Recovery Services-Tresor zu löschen, der über die folgenden Abhängigkeiten verfügt:

- Es ist nicht möglich, einen Tresor zu löschen, der geschützte Datenquellen (z. B. IaaS-VMs, SQL-Datenbanken, Azure-Dateifreigaben) enthält.
- Es ist nicht möglich, einen Tresor zu löschen, der Sicherungsdaten enthält. Nachdem die Sicherungsdaten gelöscht wurden, werden sie in den vorläufig gelöschten Zustand versetzt.
- Es ist nicht möglich, einen Tresor zu löschen, der Sicherungsdaten im vorläufig gelöschten Zustand enthält.
- Es ist nicht möglich, einen Tresor zu löschen, der über registrierte Speicherkonten verfügt.

Wenn Sie versuchen, den Tresor zu löschen, ohne die Abhängigkeiten zu entfernen, wird eine der folgenden Fehlermeldungen angezeigt:

- Der Tresor kann nicht gelöscht werden, weil darin Ressourcen vorhanden sind. Stellen Sie sicher, dass diesem Tresor keine Sicherungselemente, geschützten Server oder Sicherungsverwaltungsserver zugeordnet sind. Heben Sie die Registrierung der folgenden Container auf, die diesem Tresor zugeordnet sind, bevor Sie mit dem Löschen fortfahren.

- Der Recovery Services-Tresor kann nicht gelöscht werden, da sich darin vorläufig gelöschte Sicherungselemente befinden. Die vorläufig gelöschten Elemente werden 14 Tage nach dem ersten Löschvorgang endgültig gelöscht. Versuchen Sie erst, den Tresor zu löschen, nachdem die Sicherungselemente endgültig gelöscht wurden und im Tresor kein Element mit dem Status „Vorläufig gelöscht“ mehr vorhanden ist. Weitere Informationen finden Sie unter [Vorläufiges Löschen für Azure Backup](./backup-azure-security-feature-cloud.md).

## <a name="proper-way-to-delete-a-vault"></a>So Löschen Sie einen Tresor ordnungsgemäß

>[!WARNING]
>Der folgende Vorgang ist destruktiv und kann nicht rückgängig gemacht werden. Alle Sicherungsdaten und Sicherungselemente, die dem geschützten Server zugeordnet sind, werden dauerhaft gelöscht. Gehen Sie daher mit Bedacht vor.

Zum ordnungsgemäßen Löschen eines Tresors müssen Sie die Schritte in dieser Reihenfolge ausführen:

- **Schritt 1:** Deaktivieren Sie das Feature für vorläufiges Löschen. [Hier](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) finden Sie die Schritte zum Deaktivieren des vorläufigen Löschens.

- **Schritt 2:** Nachdem Sie das vorläufige Löschen deaktiviert haben, überprüfen Sie, ob Elemente im vorläufig gelöschten Zustand verbleiben. Wenn es Elemente gibt, die sich im vorläufig gelöschten Zustand befinden, müssen Sie den *Löschvorgang rückgängig machen* und die Elemente erneut *löschen*. [Befolgen Sie diese Schritte](./backup-azure-security-feature-cloud.md#permanently-deleting-soft-deleted-backup-items), um Elemente im vorläufig gelöschten Zustand zu finden und dauerhaft zu löschen.

- **Schritt 3:** Überprüfen Sie, ob an den folgenden drei Stellen geschützte Elemente vorhanden sind:

  - **In der Cloud geschützte Elemente**: Wählen Sie im Dashbordmenü des Tresors die Option **Sicherungselemente** aus. Alle hier aufgelisteten Elemente müssen mit **Sicherung abbrechen** oder **Sicherungsdaten löschen** zusammen mit den zugehörigen Sicherungsdaten gelöscht werden.  [Führen Sie die folgenden Schritte aus](#delete-protected-items-in-the-cloud), um diese Elemente zu entfernen.
  - **SQL Server-Instanz:** Wählen Sie im Dashbordmenü des Tresors die Option **Sicherungsinfrastruktur** > **Geschützte Server** aus. Wählen Sie unter Geschützte Server den Server aus, dessen Registrierung Sie aufheben möchten. Sie müssen die Registrierung aller Server aufheben, um den Tresor zu löschen. Klicken Sie mit der rechten Maustaste auf den geschützten Server und dann auf **Registrierung aufheben**.
  - **MARS-geschützte Server**: Wählen Sie im Dashbordmenü des Tresors die Option **Sicherungsinfrastruktur** > **Geschützte Server** aus. Wenn Sie über Mars-geschützte Server verfügen, müssen alle hier aufgeführten Elemente zusammen mit den zugehörigen Sicherungsdaten gelöscht werden. [Führen Sie die folgenden Schritte aus](#delete-protected-items-on-premises), um MARS-geschützten Server zu löschen.
  - **MABS- oder DPM-Verwaltungs Server**: Wählen Sie im Dashbordmenü des Tresors die Option **Sicherungsinfrastruktur** > **Server für die Sicherungsverwaltung** aus. Wenn Sie über DPM- oder Azure Backup Server (MABS) verfügen, müssen alle hier aufgelisteten Elemente zusammen mit den zugehörigen Sicherungsdaten gelöscht werden bzw deren Registrierung muss aufgehoben werden. [Führen Sie die folgenden Schritte aus](#delete-protected-items-on-premises), um die Verwaltungsserver zu löschen.

- **Schritt 4:** Sie müssen sicherstellen, dass alle registrierten Speicherkonten gelöscht werden. Wählen Sie im Dashbordmenü des Tresors die Option **Sicherungsinfrastruktur** > **Speicherkonten** aus. Wenn hier Speicherkonten aufgeführt sind, müssen Sie die Registrierung für alle diese Kosten aufheben. Weitere Informationen zum Aufheben der Registrierung des Kontos finden Sie unter [Aufheben der Registrierung eines Speicherkontos](manage-afs-backup.md#unregister-a-storage-account).
- **Schritt 5**: Stellen Sie sicher, dass keine privaten Endpunkte für den Tresor erstellt wurden. Wechseln Sie zum Menü „Tresordashboard“ > „Einstellungen“ > **Private Endpunktverbindungen**. Wenn für den Tresor private Endpunktverbindungen erstellt oder zu erstellen versucht wurden, stellen Sie sicher, dass sie entfernt wurden, bevor Sie den Tresor löschen. 

Nachdem Sie diese Schritte abgeschlossen haben, können Sie mit dem [Löschen des Tresors](#delete-the-recovery-services-vault) fortfahren.

Führen Sie die unter [**Löschen des Recovery Services-Tresors mit Azure Resource Manager**](#delete-the-recovery-services-vault-by-using-azure-resource-manager) beschriebenen Schritte aus, wenn Sie den **Tresor ohne Abhängigkeiten noch immer nicht löschen können**.

## <a name="delete-protected-items-in-the-cloud"></a>Löschen von geschützten Elementen in der Cloud

Lesen Sie zuerst den Abschnitt **[Vorbereitung](#before-you-start)** , um sich mit den Abhängigkeiten und dem Prozess zum Löschen des Tresors vertraut zu machen.

Führen Sie die folgenden Schritte aus, um den Schutz zu beenden und die Sicherungsdaten zu löschen:

1. Navigieren Sie im Portal zu **Recovery Services-Tresor** und dann zu **Sicherungselemente**. Wählen Sie anschließend in der Liste **Sicherungsverwaltungstyp** die geschützten Elemente in der Cloud aus (beispielsweise Azure Virtual Machines, Azure Storage [den Azure Files-Dienst] oder SQL Server auf Azure-VMs).

    ![Auswählen des Sicherungstyps](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. Es wird eine Liste mit allen Elementen für die Kategorie angezeigt. Klicken Sie mit der rechten Maustaste, um das Sicherungselement auszuwählen. Je nachdem, ob das Sicherungselement geschützt ist oder nicht, wird im Menü entweder der Bereich **Sicherung beenden** oder der Bereich **Sicherungsdaten löschen** angezeigt.

    - Wenn der Bereich **Sicherung beenden** angezeigt wird, wählen Sie im Dropdownmenü die Option **Sicherungsdaten löschen** aus. Geben Sie den Namen des Sicherungselements ein (bei diesem Feld muss die Groß-/Kleinschreibung beachtet werden), und wählen Sie dann im Dropdownmenü einen Grund aus. Geben Sie ggf. Ihre Kommentare ein. Wählen Sie dann **Sicherung beenden** aus.

        ![Bereich „Sicherung beenden“](./media/backup-azure-delete-vault/stop-backup-item.png)

    - Wenn der Bereich **Sicherungsdaten löschen** angezeigt wird, geben Sie den Namen des Sicherungselements ein (bei diesem Feld muss die Groß-/Kleinschreibung beachtet werden), und wählen Sie dann im Dropdownmenü einen Grund aus. Geben Sie ggf. Ihre Kommentare ein. Wählen Sie dann **Löschen** aus.

         ![Bereich „Sicherungsdaten löschen“](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

   Mit dieser Option werden geplante und bedarfsgesteuerte Sicherungen gelöscht.
3. Überprüfen Sie das Symbol **Benachrichtigung**: ![Benachrichtigungssymbol](./media/backup-azure-delete-vault/messages.png) Nachdem der Vorgang abgeschlossen ist, zeigt der Dienst die folgende Meldung an: *Sicherung beenden und Sicherungsdaten für „* Sicherungselement *“ löschen*. *Der Vorgang wurde erfolgreich abgeschlossen.*
4. Wählen Sie im Menü **Sicherungselemente** die Option **Aktualisieren** aus, um sich zu vergewissern, dass das Sicherungselement gelöscht wurde.

      ![Seite „Sicherungselemente löschen“](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Löschen von geschützten lokalen Elementen

Lesen Sie zuerst den Abschnitt **[Vorbereitung](#before-you-start)** , um sich mit den Abhängigkeiten und dem Prozess zum Löschen des Tresors vertraut zu machen.

1. Wählen Sie im Menü des Tresordashboards die Option **Sicherungsinfrastruktur** aus.
2. Wählen Sie je nach lokalem Szenario eine der folgenden Optionen aus:

      - Wählen Sie für MARS die Option **Geschützte Server** und dann **Azure Backup-Agent** aus. Wählen Sie anschließend den Server aus, den Sie löschen möchten.

        ![MARS: Auswählen des Tresors zum Öffnen des zugehörigen Dashboards](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - Wählen Sie für MABS oder DPM die Option **Sicherungsverwaltungsserver** aus. Wählen Sie anschließend den Server aus, den Sie löschen möchten.

          ![MABS oder DPM: Auswählen des Tresors zum Öffnen des zugehörigen Dashboards.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Der Bereich **Löschen** wird geöffnet und zeigt eine Warnmeldung an.

     ![Bereich „Löschen“](./media/backup-azure-delete-vault/delete-protected-server.png)

     Lesen Sie die Warnmeldung und die Anweisungen im Kontrollkästchen für die Zustimmung.
    > [!NOTE]
    >
    >- Wenn der geschützte Server mit Azure-Diensten synchronisiert wird und Sicherungselemente vorhanden sind, werden im Kontrollkästchen für die Zustimmung die Anzahl von abhängigen Sicherungselementen und der Link angezeigt, unter dem die Sicherungselemente angezeigt werden können.
    >- Wenn der geschützte Server nicht mit Azure-Diensten synchronisiert wird und Sicherungselemente vorhanden sind, wird im Kontrollkästchen für die Zustimmung nur die Anzahl von Sicherungselementen angezeigt.
    >- Wenn keine Sicherungselemente vorhanden sind, zeigt das Kontrollkästchen für die Zustimmung eine Aufforderung zum Löschen an.

4. Aktivieren Sie das Kontrollkästchen für die Zustimmung, und klicken Sie dann auf **Löschen**.

5. Überprüfen Sie das Symbol **Benachrichtigung**![Sicherungsdaten löschen](./media/backup-azure-delete-vault/messages.png). Nach Abschluss des Vorgangs zeigt der Dienst die folgende Meldung an: *Sicherung beenden und Sicherungsdaten für „Sicherungselement“ löschen.* *Der Vorgang wurde erfolgreich abgeschlossen.*
6. Wählen Sie im Menü **Sicherungselemente** die Option **Aktualisieren** aus, um sich zu vergewissern, dass das Sicherungselement gelöscht wurde.

>[!NOTE]
>Wenn Sie ein lokales geschütztes Element, das Abhängigkeiten aufweist, aus einem Portal löschen, wird eine Warnung angezeigt, die besagt, dass das Löschen der Serverregistrierung ein destruktiver Vorgang ist und nicht rückgängig gemacht werden kann, und dass alle Sicherungsdaten (zum Wiederherstellen der Daten benötigte Wiederherstellungspunkte) und Sicherungselemente, die dem geschützten Server zugeordnet sind, dauerhaft gelöscht werden.

Nach Abschluss dieses Vorgangs können Sie die Sicherungselemente über die Verwaltungskonsole löschen:

- [Löschen von Sicherungselementen über die MARS-Verwaltungskonsole](#delete-backup-items-from-the-mars-management-console)
- [Löschen von Sicherungselementen über die MABS- oder DPM-Verwaltungskonsole](#delete-backup-items-from-the-mabs-or-dpm-management-console)

### <a name="delete-backup-items-from-the-mars-management-console"></a>Löschen von Sicherungselementen über die MARS-Verwaltungskonsole

>[!NOTE]
>Wenn Sie den Quellcomputer gelöscht oder verloren haben, ohne die Sicherung zu beenden, tritt bei der nächsten geplanten Sicherung ein Fehler auf. Der alte Wiederherstellungspunkt läuft gemäß der Richtlinie ab. Der letzte Einzelwiederherstellungspunkt wird jedoch immer beibehalten, bis Sie die Sicherung beenden und die Daten löschen. Dazu müssen Sie die Schritte in [diesem Abschnitt](#delete-protected-items-on-premises) ausführen.

1. Öffnen Sie die MARS-Verwaltungskonsole, navigieren Sie zum Bereich **Aktionen**, und wählen Sie **Sicherung planen** aus.
2. Wählen Sie auf der Seite **Geplante Sicherung ändern oder beenden** die Option **Sicherungszeitplan nicht länger verwenden und alle gespeicherten Sicherungen löschen** aus. Klicken Sie anschließend auf **Weiter**.

    ![Ändern oder Beenden einer geplanten Sicherung](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Wählen Sie auf der Seite **Geplante Sicherung beenden** die Option **Fertig stellen** aus.

    ![Beenden einer geplanten Sicherung](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. Sie werden aufgefordert, eine Sicherheits-PIN (persönliche Identifikationsnummer) einzugeben, die Sie manuell generieren müssen. Dazu melden Sie sich zunächst beim Azure-Portal an.
5. Navigieren Sie zu **Recovery Services-Tresor** > **Einstellungen** > **Eigenschaften**.
6. Wählen Sie unter **Sicherheits-PIN** die Option **Generieren** aus. Kopieren Sie diese PIN. Die PIN ist nur fünf Minuten lang gültig.
7. Fügen Sie die PIN in der Verwaltungskonsole ein, und klicken Sie anschließend auf **OK**.

    ![Generieren einer Sicherheits-PIN](./media/backup-azure-delete-vault/security-pin.png)

8. Auf der Seite **Sicherungsstatus ändern** wird die folgende Meldung angezeigt: *Gelöschte Sicherungsdaten werden 14 Tage lang aufbewahrt. Danach werden Sicherungsdaten dauerhaft gelöscht.*  

    ![Löschen der Sicherungsinfrastruktur](./media/backup-azure-delete-vault/deleted-backup-data.png)

Nachdem Sie die lokalen Sicherungselemente gelöscht haben, führen Sie die nächsten Schritte im Portal aus.

### <a name="delete-backup-items-from-the-mabs-or-dpm-management-console"></a>Löschen von Sicherungselementen über die MABS- oder DPM-Verwaltungskonsole

>[!NOTE]
>Wenn Sie den Ursprungscomputer gelöscht oder verloren haben, ohne die geplante Sicherung zu beenden, schlägt die nächste geplante Sicherung fehl. Der alte Wiederherstellungspunkt läuft gemäß der Richtlinie ab. Der letzte Einzelwiederherstellungspunkt wird jedoch immer beibehalten, bis Sie die Sicherung beenden und die Daten löschen. Dazu müssen Sie die Schritte in [diesem Abschnitt](#delete-protected-items-on-premises) ausführen.

Es gibt zwei Methoden zum Löschen von Sicherungselementen über die MABS- oder DPM-Verwaltungskonsole.

#### <a name="method-1"></a>Methode 1

Führen Sie die folgenden Schritte aus, um den Schutz zu beenden und die Sicherungsdaten zu löschen:

1. Öffnen Sie die DPM-Verwaltungskonsole, und wählen Sie dann auf der Navigationsleiste die Option **Schutz** aus.
2. Wählen Sie im Anzeigebereich das Schutzgruppenmitglied aus, das Sie entfernen möchten. Klicken Sie mit der rechten Maustaste, und wählen Sie **Schutz von Gruppenmitgliedern beenden** aus.
3. Aktivieren Sie im Dialogfeld **Schutz beenden** das Kontrollkästchen **Geschützte Daten löschen** und anschließend das Kontrollkästchen **Speicher online löschen**. Wählen Sie dann **Schutz beenden** aus.

    ![Auswählen von „Geschützte Daten löschen“ im Bereich „Schutz beenden“](./media/backup-azure-delete-vault/delete-storage-online.png)

    Für die folgenden Versionen werden Sie aufgefordert, eine Sicherheits-PIN (persönliche Identifikationsnummer) einzugeben, die Sie manuell generieren müssen.
    

    - DPM 2019 UR1 und höher
    - DPM 2016 UR9 und höher
    - MABS V3 UR1 und höher
    
    Führen Sie zum Generieren einer PIN die folgenden Schritte aus:
    
    1. Melden Sie sich beim Azure-Portal an.
    1. Navigieren Sie zu **Recovery Services-Tresor** > **Einstellungen** > **Eigenschaften**.
    1. Wählen Sie unter **Sicherheits-PIN** die Option **Generieren** aus.
    1. Kopieren Sie diese PIN. 
       >[!NOTE]
       >Die PIN ist nur fünf Minuten lang gültig.
    1. Fügen Sie die PIN in der Verwaltungskonsole ein, und klicken Sie anschließend auf **Senden**.
       ![Geben Sie den Sicherheits-PIN zum Löschen von Sicherungsartikeln aus der MABS- und DPM-Verwaltungskonsole ein](./media/backup-azure-delete-vault/enter-security-pin.png)

4. Wenn Sie zuvor im Dialogfeld **Schutz beenden** die Option **Onlinespeicher löschen** ausgewählt haben, ignorieren Sie diesen Schritt. Klicken Sie mit der rechten Maustaste auf die inaktive Schutzgruppe, und wählen Sie **Inaktiven Schutz entfernen** aus.

    ![Entfernen des inaktiven Schutzes](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. Aktivieren Sie im Fenster **Inaktiven Schutz entfernen** das Kontrollkästchen **Speicher online löschen**, und klicken Sie dann auf **OK**.

    ![Löschen des Onlinespeichers](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

    Für die folgenden Versionen werden Sie aufgefordert, eine Sicherheits-PIN (persönliche Identifikationsnummer) einzugeben, die Sie manuell generieren müssen.
    

    - DPM 2019 UR1 und höher
    - DPM 2016 UR9 und höher
    - MABS V3 UR1 und höher
    
    Führen Sie zum Generieren einer PIN die folgenden Schritte aus:
    
    1. Melden Sie sich beim Azure-Portal an.
    1. Navigieren Sie zu **Recovery Services-Tresor** > **Einstellungen** > **Eigenschaften**.
    1. Wählen Sie unter **Sicherheits-PIN** die Option **Generieren** aus.
    1. Kopieren Sie diese PIN. 
       >[!NOTE]
       >Die PIN ist nur fünf Minuten lang gültig.
    1. Fügen Sie die PIN in der Verwaltungskonsole ein, und klicken Sie anschließend auf **Senden**.
       ![Geben Sie den Sicherheits-PIN zum Löschen von Sicherungsartikeln aus der MABS- und DPM-Verwaltungskonsole ein](./media/backup-azure-delete-vault/enter-security-pin.png)
 
     Der Status des geschützten Mitglieds ändert sich in *Inaktives Replikat verfügbar*.

#### <a name="method-2"></a>Methode 2

Öffnen Sie die **MABS-Verwaltungskonsole** oder die **DPM-Verwaltungskonsole**. Deaktivieren Sie unter **Datenschutzmethode auswählen** das Kontrollkästchen **Ich möchte Onlineschutz**.

  ![Wählen Sie die Methoden zum Schützen der Daten aus.](./media/backup-azure-delete-vault/data-protection-method.png)

Nachdem Sie die lokalen Sicherungselemente gelöscht haben, führen Sie die nächsten Schritte im Portal aus.

## <a name="delete-the-recovery-services-vault"></a>Löschen des Recovery Services-Tresors

1. Nachdem alle Abhängigkeiten entfernt wurden, scrollen Sie im Tresormenü zum Bereich **Zusammenfassung**.
2. Vergewissern Sie sich, dass keine Sicherungselemente, Sicherungsverwaltungsserver oder replizierte Elemente aufgelistet sind. Wenn weiterhin Elemente im Tresor angezeigt werden, lesen Sie die Informationen im Abschnitt [Vorbereitung](#before-you-start).

3. Wenn sich keine Elemente mehr im Tresor befinden, klicken Sie im Tresordashboard auf **Löschen**.

    ![Auswählen von „Löschen“ auf dem Tresordashboard](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Wählen Sie **Ja** aus, um das Löschen des Tresors zu bestätigen. Der Tresor wird gelöscht. Sie werden im Portal zum Dienstmenü **Neu** zurückgeführt.

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>Löschen des Recovery Services-Tresors mithilfe von PowerShell

Lesen Sie zuerst den Abschnitt **[Vorbereitung](#before-you-start)** , um sich mit den Abhängigkeiten und dem Prozess zum Löschen des Tresors vertraut zu machen.

Schutz beenden und Sicherungsdaten löschen:

- Wenn Sie SQL für die Sicherung von virtuellen Azure-Computern verwenden und den automatischen Schutz für SQL-Instanzen aktiviert haben, deaktivieren Sie zuerst den automatischen Schutz.

    ```PowerShell
        Disable-AzRecoveryServicesBackupAutoProtection
           [-InputItem] <ProtectableItemBase>
           [-BackupManagementType] <BackupManagementType>
           [-WorkloadType] <WorkloadType>
           [-PassThru]
           [-VaultId <String>]
           [-DefaultProfile <IAzureContextContainer>]
           [-WhatIf]
           [-Confirm]
           [<CommonParameters>]
    ```

  [Weitere Informationen](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection) zum Deaktivieren des Schutzes für ein mit Azure Backup geschütztes Element

- Beenden Sie den Schutz, und löschen Sie Daten für alle von der Sicherung geschützten Elemente in der Cloud (z. B. laaS VM, Azure-Dateifreigabe usw.):

    ```PowerShell
       Disable-AzRecoveryServicesBackupProtection
       [-Item] <ItemBase>
       [-RemoveRecoveryPoints]
       [-Force]
       [-VaultId <String>]
       [-DefaultProfile <IAzureContextContainer>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
    ```

    [Weitere Informationen](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection)  zum Deaktivieren des Schutzes für ein mit Backup geschütztes Element.

- Verwenden Sie den folgenden PowerShell-Befehl, um lokale Dateien und Ordner, die mithilfe des Azure Backup-Agent (MARS) in Azure gesichert werden, die gesicherten Daten von den einzelnen MARS-PowerShell-Modulen zu löschen:

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Anschließend wird die folgende Eingabeaufforderung angezeigt:

    *Microsoft Azure Backup: Möchten Sie diese Sicherungsrichtlinie wirklich entfernen? Gelöschte Sicherungsdaten werden 14 Tage lang aufbewahrt. Nach diesem Zeitpunkt werden Sicherungsdaten dauerhaft gelöscht. <br/> [J] Ja [A] Ja, alle [N] Nein [L] Nein für alle [S] Anhalten [?] Hilfe (Standard ist „J“):*

- Für lokale Computer, die per MABS (Microsoft Azure Backup Server) oder DPM (System Center Data Protection Manager) in Azure geschützt sind, verwenden Sie den folgenden Befehl, um die gesicherten Daten in Azure zu löschen.

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Anschließend wird die folgende Eingabeaufforderung angezeigt:

   *Microsoft Azure Backup:* Möchten Sie diese Sicherungsrichtlinie wirklich entfernen? Gelöschte Sicherungsdaten werden 14 Tage lang aufbewahrt. Danach werden Sicherungsdaten dauerhaft gelöscht. <br/>
   [J] Ja [A] Ja, alle [N] Nein [L] Nein für alle [S] Anhalten [?] Hilfe (Standardeinstellung ist „J“):*

Nachdem Sie die gesicherten Daten gelöscht haben, heben Sie die Registrierung für lokale Container und Verwaltungsserver auf.

- Für lokale Dateien und Ordner, die per Azure Backup-Agent (MARS) geschützt sind und in Azure gesichert werden:

    ```PowerShell
    Unregister-AzRecoveryServicesBackupContainer
              [-Container] <ContainerBase>
              [-PassThru]
              [-VaultId <String>]
              [-DefaultProfile <IAzureContextContainer>]
              [-WhatIf]
              [-Confirm]
              [<CommonParameters>]
    ```

    [Weitere Informationen](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) zum Aufheben der Registrierung eines Windows-Servers oder eines anderen Containers aus dem Tresor.

- Für lokale Computer, die per MABS (Microsoft Azure Backup Server) oder DPM in Azure (System Center Data Protection Manager) geschützt sind:

    ```PowerShell
        Unregister-AzRecoveryServicesBackupManagementServer
          [-AzureRmBackupManagementServer] <BackupEngineBase>
          [-PassThru]
          [-VaultId <String>]
          [-DefaultProfile <IAzureContextContainer>]
          [-WhatIf]
          [-Confirm]
          [<CommonParameters>]
    ```

    [Weitere Informationen](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) zum Aufheben der Registrierung eines Sicherungsverwaltungscontainers aus dem Tresor.

Nachdem Sie die gesicherten Daten dauerhaft gelöscht und die Registrierung aller Container aufgehoben haben, fahren Sie mit dem Löschen des Tresors fort.

So löschen Sie einen Recovery Services-Tresor:

   ```PowerShell
       Remove-AzRecoveryServicesVault
      -Vault <ARSVault>
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
   ```

[Erfahren Sie mehr](/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault) über das Löschen eines Recovery Services-Tresors.

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>Löschen des Recovery Services-Tresors mithilfe der CLI

Lesen Sie zuerst den Abschnitt **[Vorbereitung](#before-you-start)** , um sich mit den Abhängigkeiten und dem Prozess zum Löschen des Tresors vertraut zu machen.

> [!NOTE]
> Zurzeit unterstützt Azure Backup CLI nur die Verwaltung von Azure-VM-Sicherungen, sodass der folgende Befehl zum Löschen des Tresors nur dann funktioniert, wenn der Tresor Sicherungen virtueller Azure-Computer enthält. Sie können einen Tresor nicht mithilfe der Azure Backup-Befehlszeilenschnittstelle löschen, wenn der Tresor ein Sicherungselement eines anderen Typs als Azure-VMs enthält.

Führen Sie die folgenden Schritte aus, um den vorhandenen Recovery Services-Tresor zu löschen:

- Schutz beenden und Sicherungsdaten löschen

    ```azurecli
    az backup protection disable --container-name
                             --item-name
                             [--delete-backup-data {false, true}]
                             [--ids]
                             [--resource-group]
                             [--subscription]
                             [--vault-name]
                             [--yes]
    ```

    Weitere Informationen finden Sie in  [diesem Artikel](/cli/azure/backup/protection#az_backup_protection_disable).

- Löschen eines vorhandenen Recovery Services-Tresors:

    ```azurecli
    az backup vault delete [--force]
                       [--ids]
                       [--name]
                       [--resource-group]
                       [--subscription]
                       [--yes]
    ```

    Weitere Informationen finden Sie in  [diesem Artikel](/cli/azure/backup/vault).

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>Löschen des Recovery Services-Tresors mit Azure Resource Manager

Diese Option zum Löschen des Recovery Services-Tresors wird nur empfohlen, wenn alle Abhängigkeiten entfernt wurden und Sie den *Fehler beim Löschen von Tresor* trotzdem erhalten. Probieren Sie einen oder alle der folgenden Tipps aus:

- Vergewissern Sie sich im Bereich **Zusammenfassung** im Tresormenü, dass keine Sicherungselemente, Sicherungsverwaltungsserver oder replizierten Elemente aufgelistet sind. Wenn Sicherungselemente vorhanden sind, lesen Sie die Informationen im Abschnitt [Vorbereitung](#before-you-start).
- Versuchen Sie erneut, [den Tresor über das Portal zu löschen](#delete-the-recovery-services-vault).
- Wenn alle Abhängigkeiten entfernt wurden und der *Fehler beim Löschen von Tresor* weiterhin auftritt, verwenden Sie das ARMClient-Tool zum Ausführen der folgenden Schritte (nach dem Hinweis).

1. Wechseln Sie zu [chocolatey.org](https://chocolatey.org/), um Chocolatey herunterzuladen und zu installieren. Dann installieren Sie ARMClient, indem Sie den folgenden Befehl ausführen:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Melden Sie sich bei Ihrem Azure-Konto an, und führen Sie anschließend den folgenden Befehl aus:

    `ARMClient.exe login [environment name]`

3. Suchen Sie im Azure-Portal nach der Abonnement-ID und dem Ressourcengruppennamen für den Tresor, den Sie löschen möchten.

Weitere Informationen zum ARMClient-Befehl finden Sie unter [ARMClient README](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>Löschen eines Recovery Services-Tresors mit dem Azure Resource Manager-Client

1. Führen Sie den folgenden Befehl mit Ihrer Abonnement-ID, dem Namen der Ressourcengruppe und dem Namen des Tresors aus. Wenn keine Abhängigkeiten vorliegen, wird der Tresor beim Ausführen des folgenden Befehls gelöscht:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<Recovery Services vault name>?api-version=2015-03-15
   ```

2. Ist der Tresor nicht leer, erhalten Sie die folgende Fehlermeldung: *Der Tresor kann nicht gelöscht werden, weil sich im Tresor vorhandene Ressourcen befinden.* Führen Sie den folgenden Befehl aus, um in einem Tresor enthaltene geschützte Elemente oder Container zu entfernen:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<Recovery Services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Vergewissern Sie sich im Azure-Portal, dass der Tresor gelöscht wurde.

## <a name="next-steps"></a>Nächste Schritte

[Übersicht über Recovery Services-Tresore](backup-azure-recovery-services-vault-overview.md)
[Überwachen und Verwalten von Recovery Services-Tresoren](backup-azure-manage-windows-server.md)
