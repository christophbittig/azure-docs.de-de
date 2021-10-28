---
title: Verwalten und Überwachen von MARS-Agent-Sicherungen
description: Erfahren Sie etwas über das Verwalten und Überwachen von MARS-Agent-Sicherungen (Microsoft Azure Recovery Services) mit dem Azure Backup-Dienst.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/05/2021
ms.openlocfilehash: db7bd057cc2f781b1a55b62f28170fe462b11f39
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130233561"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Verwalten von MARS-Agent-Sicherungen (Microsoft Azure Recovery Services) mit dem Azure Backup-Dienst

In diesem Artikel wird beschrieben, wie Sie Dateien und Ordner verwalten, die mit Microsoft Azure Recovery Services Agent gesichert wurden.

## <a name="modify-a-backup-policy"></a>Ändern einer Sicherungsrichtlinie

Wenn Sie die Sicherungsrichtlinie ändern, können Sie neue Elemente hinzufügen, vorhandene Elemente aus der Sicherung entfernen oder mithilfe von Ausschlusseinstellungen Dateien aus der Sicherung ausschließen.

- **Elemente hinzufügen**: Verwenden Sie diese Option nur, um neue zu sichernde Elemente hinzuzufügen. Um vorhandene Elemente zu entfernen, verwenden Sie die Option **Elemente entfernen** oder **Ausschlusseinstellungen**.  
- **Elemente entfernen**: Verwenden Sie diese Option, um Elemente aus der Sicherung auszuschließen.
  - Verwenden Sie **Ausschlusseinstellungen** statt **Elemente entfernen**, um alle Elemente in einem Volume zu entfernen.
  - Das Deaktivieren aller Auswahloptionen in einem Volume bewirkt, dass alte Sicherungen der Elemente gemäß den Aufbewahrungseinstellungen zum Zeitpunkt der letzten Sicherung aufbewahrt werden, ohne dass Änderungen vorgenommen werden können.
  - Wenn Sie diese Optionen erneut aktivieren, führt dies zu einer ersten vollständigen Sicherung, und neue Richtlinienänderungen werden nicht auf alte Sicherungen angewendet.
  - Durch das Aufheben der Auswahl des gesamten Volumes wird die letzte Sicherung aufbewahrt, ohne dass an der Aufbewahrungsrichtlinie Änderungen vorgenommen werden können.
- **Ausschlusseinstellungen**: Verwenden Sie diese Option, um bestimmte Elemente aus der Sicherung auszuschließen.

### <a name="add-new-items-to-existing-policy"></a>Hinzufügen neuer Elemente zu einer vorhandenen Richtlinie

1. Wählen Sie in **Aktionen** die Option **Sicherung planen** aus.

    ![Planen einer Windows Server-Sicherung](./media/backup-configure-vault/schedule-first-backup.png)

2. Wählen Sie auf der Registerkarte **Wählen Sie das Richtlinienelement aus** die Option **Ändern Sie einen Sicherungszeitplan für Ihre Dateien und Ordner** und dann **Weiter** aus.

    ![Hinzufügen von Richtlinienelementen](./media/backup-azure-manage-mars/select-policy-items.png)

3. Wählen Sie auf der Registerkarte **Geplante Sicherung ändern oder beenden** die Option **Sicherungselemente oder -zeiten ändern** und dann **Weiter** aus.

    ![Ändern oder Planen der Sicherung](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. Wählen Sie auf der Registerkarte **Elemente für die Sicherung auswählen** die Option **Elemente hinzufügen** aus, um die Elemente hinzuzufügen, die Sie sichern möchten.

    ![Ändern oder Planen der Sicherung – Elemente hinzufügen](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. Wählen Sie im Fenster **Elemente auswählen** Dateien oder Ordner aus, die Sie hinzufügen möchten, und wählen Sie dann **OK** aus.

    ![Auswählen der Elemente](./media/backup-azure-manage-mars/select-item.png)

6. Führen Sie die nachfolgenden Schritte aus, und wählen Sie zum Abschließen des Vorgangs **Fertig stellen** aus.

### <a name="add-exclusion-rules-to-existing-policy"></a>Hinzufügen von Ausschlussregeln zu einer vorhandenen Richtlinie

Sie können Ausschlussregeln hinzufügen, um Dateien und Ordner zu überspringen, die nicht gesichert werden sollen. Sie können dies beim Definieren einer neuen Richtlinie oder Ändern einer vorhandenen Richtlinie durchführen.

1. Wählen Sie im Bereich „Aktionen“ die Option **Sicherung planen** aus. Wechseln Sie zu **Elemente für die Sicherung auswählen**, und wählen Sie **Ausschlusseinstellungen** aus.

    ![Ausschlusseinstellungen](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. Wählen Sie in **Ausschlusseinstellungen** die Option **Ausschluss hinzufügen** aus.

    ![Hinzufügen des Ausschlusses](./media/backup-azure-manage-mars/add-exclusion.png)

3. Durchsuchen Sie in **Auszuschließende Elemente auswählen** die Dateien und Ordnern, die Sie ausschließen möchten, wählen Sie Elemente aus, die Sie ausschließen möchten, und wählen Sie **OK** aus.

    ![Auswählen der auszuschließenden Elemente](./media/backup-azure-manage-mars/select-items-exclude.png)

4. Standardmäßig werden alle **Unterordner** in den ausgewählten Ordnern ausgeschlossen. Sie können dies ändern, indem Sie **Ja** oder **Nein** auswählen. Sie können die auszuschließenden Dateitypen wie unten dargestellt bearbeiten und angeben:

    ![Auswählen der Unterordnertypen](./media/backup-azure-manage-mars/subfolders-type.png)

5. Führen Sie die nachfolgenden Schritte aus, und wählen Sie zum Abschließen des Vorgangs **Fertig stellen** aus.

### <a name="remove-items-from-existing-policy"></a>Entfernen von Elementen aus vorhandener Richtlinie

1. Wählen Sie im Bereich „Aktionen“ die Option **Sicherung planen** aus. Wechseln Sie zu **Elemente für die Sicherung auswählen**. Wählen Sie in der Liste die Dateien und Ordner aus, die Sie aus dem Sicherungszeitplan entfernen möchten, und wählen Sie **Elemente entfernen** aus.

    ![Auswählen der zu entfernenden Elemente](./media/backup-azure-manage-mars/select-items-remove.png)

    > [!NOTE]
    > Gehen Sie mit Bedacht vor, wenn Sie ein Volume vollständig aus der Richtlinie entfernen.  Wenn Sie es erneut hinzufügen müssen, wird es als neues Volume behandelt. Die nächste geplante Sicherung wird als Erstsicherung (vollständige Sicherung) und nicht als inkrementellen Sicherung ausgeführt. Wenn Sie zu einem späteren Zeitpunkt Elemente temporär entfernen und hinzufügen müssen, sollten Sie **Ausschlusseinstellungen** anstelle von **Elemente entfernen** verwenden, damit statt einer vollständigen Sicherung eine inkrementelle Sicherung erfolgt.

2. Führen Sie die nachfolgenden Schritte aus, und wählen Sie zum Abschließen des Vorgangs **Fertig stellen** aus.

## <a name="stop-protecting-files-and-folder-backup"></a>Beenden des Schutzes von Dateien und Ordnern durch Sicherungen

Sie haben zwei Möglichkeiten, den Schutz von Dateien und Ordnern durch Sicherungen zu beenden:

- **Schutz beenden und Sicherungsdaten beibehalten**.
  - Mit dieser Option werden alle zukünftigen Sicherungsaufträge beendet.
  - Der Azure Backup-Dienst behält alle vorhandenen Wiederherstellungspunkte weiterhin bei.  
  - Sie können die gesicherten Daten für nicht abgelaufene Wiederherstellungspunkte wiederherstellen.
  - Mit der Option *Aktivieren Sie den Sicherungszeitplan erneut* können Sie bei Bedarf den Schutz fortsetzen. Anschließend werden die Daten basierend auf der neuen Aufbewahrungsrichtlinie aufbewahrt.
- **Schutz beenden und Sicherungsdaten löschen**.
  - Mit dieser Option werden alle zukünftigen Sicherungsaufträge zum Schutz Ihrer Daten alle beendet. Wenn die Tresorsicherheitsfeatures nicht aktiviert sind, werden alle Wiederherstellungspunkte sofort gelöscht.<br>Wenn die Sicherheitsfeatures aktiviert sind, wird die Löschung um 14 Tage verzögert, und Sie erhalten eine Warn-E-Mail mit dem Inhalt *Die Sicherungsdaten für dieses Sicherungselement wurden gelöscht. Gelöschte Daten werden 14 Tage lang aufbewahrt und anschließend dauerhaft gelöscht* und der empfohlenen Aktion *Schützen Sie das Sicherungselement innerhalb von 14 Tagen erneut, um die Daten wiederherzustellen*.<br>In diesem Zustand wird die Aufbewahrungsrichtlinie weiterhin angewendet, und die Sicherungsdaten bleiben abrechenbar. [Erfahren Sie mehr](backup-azure-security-feature.md#enable-security-features) über das Aktivieren von Tresorsicherheitsfeatures.
  - Um den Schutz fortzusetzen, stellen Sie den Server innerhalb von 14 Tagen nach dem Löschvorgang wieder her. Innerhalb dieses Zeitraums können Sie die Daten auch auf einem anderen Server wiederherstellen.

### <a name="stop-protection-and-retain-backup-data"></a>Schutz beenden und Sicherungsdaten beibehalten

1. Öffnen Sie die MARS-Verwaltungskonsole, navigieren Sie zum Bereich **Aktionen**, und wählen Sie **Sicherung planen** aus.

    ![Auswählen eines Sicherungszeitplans](./media/backup-azure-manage-mars/mars-actions.png)
1. Wählen Sie auf der Seite **Wählen Sie das Richtlinienelement aus** die Option **Ändern Sie einen Sicherungszeitplan für Ihre Dateien und Ordner** aus, und wählen Sie **Weiter** aus.

    ![Auswählen des Richtlinienelements](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. Wählen Sie auf der Seite **Geplante Sicherung ändern oder beenden** die Option **Stop using this backup schedule, but keep the stored backups until a schedule is activated again** (Diesen Sicherungszeitplan nicht länger verwenden, die gespeicherten Sicherungen jedoch beibehalten, bis erneut ein Sicherungszeitplan aktiviert wird) aus. Klicken Sie anschließend auf **Weiter**.

    ![Beenden einer geplanten Sicherung](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. Überprüfen Sie die Informationen in **Geplante Sicherung anhalten**, und wählen Sie **Fertig stellen** aus.

    ![Anhalten einer geplanten Sicherung.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. Überprüfen Sie in **Sicherungsstatus ändern**, ob der Status des Anhaltens einer geplanten Sicherung „Erfolgreich“ ist, und wählen Sie **Schließen** aus, um den Vorgang abzuschließen.

### <a name="stop-protection-and-delete-backup-data"></a>Schutz beenden und Sicherungsdaten löschen

1. Öffnen Sie die MARS-Verwaltungskonsole, navigieren Sie zum Bereich **Aktionen**, und wählen Sie **Sicherung planen** aus.
2. Wählen Sie auf der Seite **Geplante Sicherung ändern oder beenden** die Option **Sicherungszeitplan nicht länger verwenden und alle gespeicherten Sicherungen löschen** aus. Klicken Sie anschließend auf **Weiter**.

    ![Ändern oder Beenden einer geplanten Sicherung](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Wählen Sie auf der Seite **Geplante Sicherung beenden** die Option **Fertig stellen** aus.

    ![Stoppen einer geplanten Sicherung und Auswahl von „Fertig stellen“](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. Sie werden aufgefordert, eine Sicherheits-PIN (persönliche Identifikationsnummer) einzugeben, die Sie manuell generieren müssen. Dazu melden Sie sich zunächst beim Azure-Portal an.
5. Navigieren Sie zu **Recovery Services-Tresor** > **Einstellungen** > **Eigenschaften**.
6. Wählen Sie unter **Sicherheits-PIN** die Option **Generieren** aus. Kopieren Sie diese PIN. Die PIN ist nur fünf Minuten lang gültig.
7. Fügen Sie die PIN in der Verwaltungskonsole ein, und klicken Sie anschließend auf **OK**.

    ![Generieren einer Sicherheits-PIN](./media/backup-azure-delete-vault/security-pin.png)

8. Auf der Seite **Sicherungsstatus ändern** wird die folgende Meldung angezeigt: *Gelöschte Sicherungsdaten werden 14 Tage lang aufbewahrt. Danach werden Sicherungsdaten dauerhaft gelöscht.*  

    ![Ändern des Sicherungsstatus](./media/backup-azure-delete-vault/deleted-backup-data.png)

Nachdem Sie die lokalen Sicherungselemente gelöscht haben, führen Sie die nächsten Schritte im Portal aus.

## <a name="re-enable-protection"></a>Erneutes Aktivieren des Schutzes

Wenn Sie den Schutz beendet haben, die Daten jedoch aufbewahrt wurden, und Sie den Schutz fortsetzen möchten, können Sie durch Ändern der Sicherungsrichtlinie den Sicherungszeitplan erneut aktivieren.

1. Wählen Sie in **Aktionen** die Option **Sicherung planen** aus.
1. Wählen Sie **Aktivieren Sie den Sicherungszeitplan erneut. Sie können Sicherungselemente oder -zeiten auch ändern** und **Weiter** auswählen.<br>

    ![Erneutes Aktivieren des Sicherungszeitplans](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. Wählen Sie in **Elemente für Sicherung auswählen** die Option **Weiter** aus.

    ![Elemente für die Sicherung auswählen](./media/backup-azure-manage-mars/re-enable-next.png)
1. Geben Sie in **Sicherungszeitplan angeben** den Sicherungszeitplan an, und wählen Sie **Weiter** aus.
1. Geben Sie in **Aufbewahrungsrichtlinie auswählen** die Aufbewahrungsdauer an, und wählen Sie **Weiter** aus.
1. Überprüfen Sie abschließend im Bildschirm **Bestätigung** die Richtliniendetails, und wählen Sie **Fertig stellen** aus.

## <a name="re-generate-passphrase"></a>Passphrase neu generieren

Eine Passphrase wird zum Verschlüsseln und Entschlüsseln von Daten während der Sicherung oder Wiederherstellung Ihres lokalen Computers mithilfe des MARS-Agents nach oder aus Azure verwendet. Wenn Sie die Passphrase verloren oder vergessen haben, können Sie sie neu generieren (vorausgesetzt, der Computer ist noch beim Recovery Services-Tresor registriert, und die Sicherung wurde konfiguriert). Führen Sie dazu die folgenden Schritte aus:

1. Wechseln Sie von der MARS-Agent-Konsole zu **Aktionsbereich** > **Eigenschaften ändern** >. Wechseln Sie dann zur **Registerkarte „Verschlüsselung“** .<br>
1. Aktivieren Sie das Kontrollkästchen **Passphrase ändern**.<br>
1. Geben Sie eine neue Passphrase ein, oder wählen Sie **Passphrase generieren** aus.
1. Wählen Sie **Durchsuchen** aus, um die neue Passphrase zu speichern.

    ![Generieren Sie die Passphrase.](./media/backup-azure-manage-mars/passphrase.png)

1. Wählen Sie **OK** aus, um die Änderungen zu übernehmen.  Wenn im Azure-Portal das [Sicherheitsfeature](./backup-azure-security-feature.md#enable-security-features) für den Recovery Services-Tresor aktiviert ist, werden Sie aufgefordert, die Sicherheits-PIN einzugeben. Führen Sie die Schritte in diesem [Artikel](./backup-azure-security-feature.md#authentication-to-perform-critical-operations) aus, um die PIN zu erhalten.<br>
1. Fügen Sie die Sicherheits-PIN aus dem Portal ein, und wählen Sie **OK** aus, um die Änderungen anzuwenden.<br>

    ![Einfügen der Sicherheits-PIN](./media/backup-azure-manage-mars/passphrase2.png)
1. Stellen Sie sicher, dass die Passphrase an einem alternativen Speicherort (nicht auf dem Quellcomputer) sicher gespeichert wird, vorzugsweise im Azure Key Vault. Halten Sie alle Passphrasen nach, wenn Sie über mehrere mit den MARS-Agents gesicherte Computer verfügen.

## <a name="validate-passphrase"></a>Überprüfen der Passphrase

Ab MARS-Agent-Version 2.0.9190.0 müssen Sie Ihre Passphrase überprüfen, um sicherzustellen, dass sie den [aktualisierten Anforderungen](./backup-azure-file-folder-backup-faq.yml#what-characters-are-allowed-for-the-passphrase-) entspricht.

Führen Sie die folgenden Schritte aus, um Ihre Passphrase zu überprüfen:

1. Öffnen Sie die MARS-Konsole.

   Am oberen Rand wird eine Meldung angezeigt, die Sie zum Überprüfen der Passphrase auffordert. 

1. Klicken Sie auf **Überprüfen**.

   :::image type="content" source="./media/backup-azure-manage-mars/validate-passphrase-prompt-inline.png" alt-text="Screenshot: Aufforderung zum Überprüfen der Passphrase" lightbox="./media/backup-azure-manage-mars/validate-passphrase-prompt-expanded.png":::

   Die Passphrasenüberprüfung wird geöffnet und fordert Sie zur Eingabe der aktuellen Passphrase auf. Wenn die Passphrase den aktualisierten Anforderungen nicht entspricht, wird eine Option zum erneuten Generieren der Passphrase angezeigt.

1. Generieren Sie die Passphrase mit den folgenden Details:

   - Neue Passphrase, die den Anforderungen entspricht
   - Sicherheits-PIN (siehe [Schritte zum Generieren der Sicherheits-PIN](#generate-security-pin))
   - Sicherer Speicherort auf dem Server zum Speichern der neu generierten Passphrase

   :::image type="content" source="./media/backup-azure-manage-mars/generate-passphrase.png" alt-text="Screenshot: Prozess zum Generieren der Passphrase mit den erforderlichen Details":::

### <a name="validate-passphrase-for-dpmmabs-agent"></a>Überprüfen der Passphrase für den DPM/MABS-Agent

Für DPM/MABS führen Sie das Tool zur Überprüfung der Passphrase über eine Eingabeaufforderung mit erhöhten Rechten aus.
   
Sie finden das Tool an einem der folgenden Speicherorte:

- **System Center Data Protection Manager**
     
  %ProgramFiles%\Microsoft Azure Recovery Services Agent\bin\PassphraseValidator.exe

- **Microsoft Azure Backup Server**
      
  %ProgramFiles%\Microsoft Azure Backup Server\DPM\MARS\Microsoft Azure Recovery Services Agent\bin\PassphraseValidator.exe

Die Passphrasenüberprüfung wird geöffnet und fordert Sie zur Eingabe der aktuellen Passphrase auf. Wenn die Passphrase den aktualisierten Anforderungen nicht entspricht, generieren Sie die Passphrase erneut.
   
:::image type="content" source="./media/backup-azure-manage-mars/passphrase-validator-prompts-for-current-passphrase.png" alt-text="Screenshot: Aufforderung der Passphrasenüberprüfung zur Eingabe der aktuellen Passphrase":::

Führen Sie die folgenden Schritte durch:

1. Navigieren Sie in der Verwaltungskonsole zur Registerkarte **Verwaltung**, und wählen Sie **Online** -> **Konfigurieren** aus.
1. Folgen Sie den Anweisungen im **Assistenten zum Konfigurieren von Abonnementeinstellungen**,und geben Sie im Schritt **Verschlüsselungseinstellung** die aktualisierte Passphrase an.

:::image type="content" source="./media/backup-azure-manage-mars/configure-subscription-settings-wizard.png" alt-text="Screenshot: Prozess zum Bereitstellen der Passphrase mithilfe des Assistenten zum Konfigurieren von Abonnementeinstellungen":::

## <a name="generate-security-pin"></a>Generieren der Sicherheits-PIN

1. Navigieren Sie zu **Recovery Services-Tresor** -> **Einstellungen** -> **Eigenschaften**.
1. Wählen Sie unter **Sicherheits-PIN** die Option **Generieren** aus.
 
Kopieren Sie die PIN. Die PIN ist nur fünf Minuten lang gültig.

## <a name="managing-backup-data-for-unavailable-machines"></a>Verwalten von Sicherungsdaten für nicht verfügbare Computer

In diesem Abschnitt wird ein Szenario besprochen, in dem Ihr Quellcomputer, der mit MARS geschützt war, nicht mehr verfügbar ist, weil er gelöscht, beschädigt, mit Malware/Bransomware infiziert oder außer Betrieb genommen wurde.

Für diese Computer stellt der Azure Backup-Dienst sicher, dass der aktuellste Wiederherstellungspunkt gemäß den in der Sicherungsrichtlinie festgelegten Aufbewahrungsregeln nicht abläuft (d. h. nicht gelöscht wird). Daher ist eine sichere Wiederherstellung des Computers möglich.  Beachten Sie die folgenden Szenarien, die Sie für die gesicherten Daten ausführen können:

### <a name="scenario-1-the-source-machine-is-unavailable-and-you-no-longer-need-to-retain-backup-data"></a>Szenario 1: Der Quellcomputer ist nicht verfügbar, und Sie müssen die Sicherungsdaten nicht mehr beibehalten.

- Mithilfe der in [diesem Artikel](backup-azure-delete-vault.md#delete-protected-items-on-premises) aufgeführten Schritte können Sie die gesicherten Daten aus dem Azure-Portal löschen.

### <a name="scenario-2-the-source-machine-is-unavailable-and-you-need-to-retain-backup-data"></a>Szenario 2: Der Quellcomputer ist nicht verfügbar, und Sie müssen die Sicherungsdaten beibehalten.

Das Verwalten der Sicherungsrichtlinie für MARS erfolgt über die MARS-Konsole und nicht über das Portal. Wenn Sie die Aufbewahrungseinstellungen für vorhandene Wiederherstellungspunkte verlängern müssen, bevor diese ablaufen, müssen Sie den Computer wiederherstellen, die MARS-Konsole installieren und die Richtlinie verlängern.

- Um den Computer wiederherzustellen, führen Sie die folgenden Schritte aus:
  1. [Wiederherstellen des virtuellen Computers auf einem alternativen Zielcomputer](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)
  1. Erstellen Sie den Zielcomputer mit dem gleichen Hostnamen wie der Quellcomputer neu.
  1. Installieren Sie den Agent, und registrieren Sie sich erneut beim gleichen Tresor und mit der gleichen Passphrase.
  1. Starten Sie den MARS-Client, um die Aufbewahrungsdauer entsprechend Ihren Anforderungen zu verlängern.
- Der neu wiederhergestellte Computer, der mit MARS geschützt wird, führt weiter Sicherungen durch.  

## <a name="configuring-antivirus-for-the-mars-agent"></a>Konfigurieren des Antivirenprogramms für den MARS-Agent

Wir empfehlen die folgende Konfiguration für Ihre Antivirussoftware, um Konflikte mit dem Betrieb des MARS-Agent zu vermeiden.

1. **Hinzufügen von Pfadausschlüssen**: Um eine Beeinträchtigung der Leistung und mögliche Konflikte zu vermeiden, schließen Sie die folgenden Pfade von der Echtzeitüberwachung durch die Antivirensoftware aus:
    1. `%ProgramFiles%\Microsoft Azure Recovery Services Agent` und Unterordner
    1. **Ablageordner**: Wenn sich der Ablageordner nicht am Standardspeicherort befindet, fügen Sie ihn ebenfalls den Ausschlüssen hinzu.  [Hier finden Sie die Schritte](backup-azure-file-folder-backup-faq.yml#how-to-check-if-scratch-folder-is-valid-and-accessible-), mit denen Sie den Speicherort des Ablageordners bestimmen.
1. **Hinzufügen von Binärausschlüssen**: Um die Beeinträchtigung von Sicherungs- und Konsolenaktivitäten zu vermeiden, schließen Sie Prozesse für die folgenden Binärdateien von der Echtzeitüberwachung durch die Antivirussoftware aus:
    1. `%ProgramFiles%\Microsoft Azure Recovery Services Agent\bin\cbengine.exe`

>[!NOTE]
>Der Ausschluss dieser Pfade reicht zwar für die meisten Antivirenprogramme aus, aber einige können weiterhin den MARS-Agent-Betrieb stören. Wenn unerwartete Fehler auftreten, deinstallieren Sie die Antivirensoftware vorübergehend, und beobachten Sie, ob das Problem beseitigt ist. Wenn das Problem dadurch behoben wird, informieren Sie sich beim Hersteller der Antivirussoftware über die richtige Konfiguration seines Produkts.

## <a name="monitor-using-backup-reports"></a>Überwachen mit Sicherungsberichten

Azure Backup bietet eine Berichterstellungslösung, bei der Azure Monitor-Protokolle und Azure-Arbeitsmappen verwendet werden. Für den Einstieg müssen Sie für Ihren Tresor [Sicherungsberichte konfiguriert](configure-reports.md) haben. Nach der Konfiguration werden Daten in den Arbeitsbereich übertragen und können mithilfe der Sicherungsberichte abgefragt werden.

Führen Sie die folgenden Schritte aus, um die Nutzung von Sicherungsdaten und die tägliche Änderungsrate zu überwachen:

1. Navigieren Sie zum Bereich **Übersicht** des Tresors, und klicken Sie auf **Sicherungsberichte**.

1. Wählen Sie auf dem Blatt **Sicherungsbericht** im Abschnitt **Übersicht** den konfigurierten Log Analytics-Arbeitsbereich aus. 

1. Legen Sie den Berichtsfilter **Sicherungslösung** auf **Azure Backup-Agent** fest, um nur MARS-Agent-Sicherungen anzuzeigen. 

   Legen Sie **Abonnementname**, **Tresorspeicherort** und **Tresorname** nach Bedarf fest.
 
    ![Berichtsfilter „Sicherungslösung“ festlegen.](./media/backup-azure-manage-mars/set-report-filter-backup-solution.png)

1. Navigieren Sie zur Registerkarte **Verbrauch**, um den Verbrauch nach abgerechneter Entität anzuzeigen. 

   Die Gesamtzahl der abgerechneten geschützten Instanzen und die Speichernutzungsdaten werden angezeigt. Sie können auch die Trendinformationen anzeigen.
 
    ![Verbrauch nach berechneter Entität anzeigen.](./media/backup-azure-manage-mars/view-usage-by-billed-entity.png)

1. Navigieren Sie zur Registerkarte **Aufträge,** um die durchschnittlichen Sicherungsdaten anzuzeigen, die von Sicherungsaufträgen für jedes Volume auf dem geschützten Server hinzugefügt wurden. 
 
    ![Durchschnittliche Sicherungsdaten anzeigen.](./media/backup-azure-manage-mars/view-average-backup-data.png)

Erfahren Sie mehr über [andere Berichtsregisterkarten](configure-reports.md) und den Empfang dieser [Berichte per E-Mail](backup-reports-email.md).

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu unterstützten Szenarien und Einschränkungen finden Sie in der [Supportmatrix für den MARS-Agent](./backup-support-matrix-mars-agent.md).
- Erfahren Sie mehr über die [Aufbewahrung bei Richtlinien für bedarfsgesteuerte Sicherungen](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior).
- Weitere häufig gestellte Fragen finden Sie unter [Häufig gestellte Fragen – Microsoft Azure Recovery Services-Agent (MARS)](backup-azure-file-folder-backup-faq.yml).