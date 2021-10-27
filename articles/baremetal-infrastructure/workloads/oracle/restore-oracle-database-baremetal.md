---
title: Wiederherstellen von Oracle Database
description: Hier erfahren Sie, wie Sie Ihre Oracle Database in der BareMetal-Infrastruktur mithilfe von SnapCenter wiederherstellen.
ms.topic: how-to
ms.subservice: baremetal-oracle
ms.date: 10/12/2021
ms.openlocfilehash: e4acd2b0c438ebee80571360b540e235ccb2c0f8
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130001540"
---
# <a name="restore-oracle-database"></a>Wiederherstellen von Oracle Database

In diesem Artikel werden die Schritte zum Wiederherstellen Ihrer Oracle Database in der BareMetal-Infrastruktur mithilfe von SnapCenter beschrieben.

Sie haben mehrere Möglichkeiten, Ihre Oracle Database in der BareMetal-Infrastruktur wiederherzustellen. Es wird empfohlen, die [Oracle Restore Decision Matrix](oracle-high-availability-recovery.md#choose-your-method-of-recovery) zu konsultieren, bevor Sie die Wiederherstellung durchführen. Diese Matrix kann Ihnen helfen, die am besten geeignete Wiederherstellungsmethode zu wählen, wenn die Wiederherstellungszeit und der potenzielle Datenverlust gegeben sind. 

In der Regel stellen Sie die aktuellsten Momentaufnahmen für Daten- und Archivprotokollvolumes wieder her, da das Ziel darin besteht, die Datenbank auf den letzten bekannten Wiederherstellungspunkt wiederherzustellen. Das Wiederherstellen von Momentaufnahmen ist ein permanenter Prozess. 

>[!IMPORTANT]
>Achten Sie darauf, dass die entsprechende Momentaufnahme wiederhergestellt wird. Beim Wiederherstellen aus einer Momentaufnahme werden alle anderen Momentaufnahmen und die zugehörigen Daten gelöscht. Dies umfasst noch mehr aktuelle Momentaufnahmen als die Momentaufnahmen, die Sie für die Wiederherstellung auswählen. Daher wird empfohlen, beim Wiederherstellungsprozess vorsichtig vorzugehen. Wenn Sie sich nicht sicher sind, welcher Snapshot wiederhergestellt werden soll, verwenden Sie lieber einen aktuelleren Snapshot.

## <a name="restore-database-locally-with-restored-archive-logs"></a>Lokales Wiederherstellen der Datenbank mit wiederhergestellten Archivprotokollen

Vor dem Wiederherstellungsversuch muss die Datenbank offline geschaltet werden, sofern dies noch nicht geschehen ist. Nachdem Sie sich vergewissert haben, dass die Datenbank auf keinen Knoten in den Oracle Real Application Clusters (RAC) ausgeführt wird, können Sie beginnen. Zuerst stellen Sie die Archivprotokolle wieder her. 

1. Identifizieren Sie die verfügbaren Sicherungen. Wählen Sie in SnapCenter im linken Menü **Resources** (Ressourcen) aus.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-database-available-backups.png" alt-text="Screenshot: Ansicht „Database“ (Datenbank)":::

2. Wählen Sie aus der Liste die Datenbank aus, die Sie wiederherstellen möchten. Die Datenbank enthält die Liste der erstellten Ressourcengruppen und die zugehörigen Richtlinien.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/available-backups-list.png" alt-text="Screenshot: vollständige Liste der verfügbaren Sicherungen für die Wiederherstellung":::

3. Anschließend wird eine Liste der primären Sicherungen angezeigt. Die Sicherungen werden anhand ihres Sicherungsnamens und ihres Typs identifiziert: Protokoll oder Daten. Befassen Sie sich zuerst mit der Protokollwiederherstellung, da SnapCenter nicht für die direkte Wiederherstellung von Archivprotokollvolumes konzipiert ist. Identifizieren Sie das Archivprotokollvolume, das wiederhergestellt werden muss. In der Regel ist das wiederherzustellende Volume die neueste Sicherung, da die meisten Wiederherstellungen ein Rollforward aller Archivprotokolle aus der letzten als gut bekannten Datendateisicherung ausführen.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/primary-backups-list.png" alt-text="Screenshot: Liste der primären Sicherungen":::

4. Nachdem Sie das Archivprotokollvolume ausgewählt haben, wird die Option „Mount“ (Bereitstellen) in der oberen rechten Ecke der Sicherungsliste aktiviert. Wählen Sie **Einbinden** aus. Wählen Sie auf der Seite „Mount backups“ (Sicherungen bereitstellen) aus der Dropdownliste den Host aus, der die Sicherung bereitstellt. Jeder RAC-Knoten kann ausgewählt werden, aber Archivprotokolle und Datendateien müssen auf demselben Host wiederhergestellt werden. Kopieren Sie den Bereitstellungspfad, da er im nächsten Schritt zum Wiederherstellen der Datendateien verwendet wird. Wählen Sie **Einbinden** aus.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-database-mount-backups.png" alt-text="Screenshot: bereitzustellende Sicherungen":::

5. Im Gegensatz zu Sicherungsaufträgen zeigt die Auftragsanzeige auf dieser Seite nicht den Status des Bereitstellungsprozesses an. Wählen Sie im linken Menü **Monitor** (Überwachen) aus, um den Status der Bereitstellung anzuzeigen. Anschließend wird der Status aller ausgeführten Aufträge hervorgehoben. Der Bereitstellungsvorgang sollte der erste Eintrag sein. Sie können auch zum kopierten Bereitstellungspfad navigieren, um das Vorhandensein von Archivprotokollen zu überprüfen.

    Der ausgewählte Host für die Bereitstellung dieses Dateisystems erhält auch einen Eintrag in „/etc/fstab“ mit dem obigen Bereitstellungspfad. Er kann nach Abschluss der Wiederherstellung entfernt werden.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-database-mount-output.png" alt-text="Screenshot: Bereitstellungsausgabe":::

6. Stellen Sie als Nächstes die Datendateien und Steuerdateien wieder her. Wählen Sie wie zuvor im linken Menü **Resources** (Ressourcen) und dann die wiederherzustellende Datenbank aus. Wählen Sie die wiederherzustellende Datensicherung aus. Auch hier ist die wiederhergestellte Sicherung in der Regel die neueste. Wenn Sie dieses Mal die Datensicherung auswählen, ist die Wiederherstellungsoption aktiviert. Wählen Sie **Wiederherstellen** aus.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-database-selected-data-backup.png" alt-text="Screenshot: wiederherzustellende Datensicherung":::

7. Auf der Registerkarte **Restore Scope** (Wiederherstellungsbereich): Wählen Sie aus der Dropdownliste den Host aus, den Sie zuvor zum Bereitstellen der Protokolldateien ausgewählt haben. Stellen Sie sicher, dass **All Datafiles** (Alle Datendateien) ausgewählt ist, und aktivieren Sie das Kontrollkästchen für **Control files** (Steuerungsdateien). Wählen Sie **Weiter** aus.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-database-recovery-scope.png" alt-text="Screenshot: Details zum Wiederherstellungsbereich für Datendateien":::

8. Auf der Registerkarte **Recovery Scope** (Wiederherstellungsbereich): Die Systemänderungsnummer (SCN) aus der bereitgestellten Protokollsicherung wird eingegeben. **Until SCN** (Bis SCN) ist jetzt ebenfalls ausgewählt. 
 
    Die SCN befindet sich für jede Sicherung in der Liste aller Sicherungen. Der zuvor kopierte Ort des Bereitstellungspunkts wird auch unter **Specify external archive log files locations** (Speicherorte externer Archivprotokolldateien angeben) eingegeben. Es geht derzeit über den Rahmen dieses Dokuments hinaus, sich mit mehreren Speicherorten für die Wiederherstellung des Archivprotokolls zu befassen. Wählen Sie **Weiter** aus.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-database-choose-recovery-scope.png" alt-text="Screenshot: Optionen für den Wiederherstellungsbereich":::

9. Auf der Registerkarte **PreOps:** Im Rahmen des Prozesses vor der Wiederherstellung werden derzeit keine Skripts empfohlen. Wählen Sie **Weiter** aus.

10. Auf der Registerkarte „PostOps“: Auch hier werden keine Skripts im Rahmen des Prozesses nach der Wiederherstellung unterstützt. Aktivieren Sie das Kontrollkästchen **Open the database or container database in READ-WRITE mode after recovery** (Datenbank oder Containerdatenbank nach der Wiederherstellung im READ-WRITE-Modus öffnen). Wählen Sie **Weiter** aus.

11. Auf der Registerkarte **Notification** (Benachrichtigung): Wenn ein SMTP-Server verfügbar ist und Sie eine E-Mail-Benachrichtigung über den Wiederherstellungsprozess wünschen, geben Sie die E-Mail-Einstellungen ein. Wählen Sie **Weiter** aus.

12. Auf der Registerkarte **Summary** (Zusammenfassung): Überprüfen Sie, ob alle Details richtig sind. Wählen Sie **Fertig stellen** aus.

13. Sie können den Wiederherstellungsstatus anzeigen, indem Sie den Wiederherstellungsauftrag im unteren Bildschirm **Activity** (Aktivität) auswählen. Verfolgen Sie den Fortschritt, indem Sie auf die grünen Pfeile klicken, um die einzelnen Wiederherstellungsunterabschnitte und deren Fortschritt anzuzeigen.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-job-details.png" alt-text="Screenshot: Details des Wiederherstellungsauftrags":::

    Nach Abschluss der Wiederherstellung werden alle Schritte in Häkchen geändert. Klicken Sie auf **Schließen**.

    Die entfernten Zeilen in der erstellten einfachen Datenbank werden jetzt als wiederhergestellt bestätigt.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/output-of-restored-files.png" alt-text="Screenshot: Ausgabeüberprüfung der wiederhergestellten Dateien":::

## <a name="clone-database-locally-or-remotely"></a>Lokales oder remotes Klonen einer Datenbank

Das Klonen der Datenbank ähnelt dem Wiederherstellungsprozess. Der Vorgang zum Klonen der Datenbank kann je nach gewünschtem Ergebnis entweder lokal oder remote verwendet werden. Das Klonen der Datenbank am Speicherort der Notfallwiederherstellung ist für verschiedene Zwecke nützlich, einschließlich Failovertests für die Notfallwiederherstellung und QA-Staging von Produktionsdaten. Dies setzt voraus, dass der Speicherort der Notfallwiederherstellung für Tests, Entwicklung, Qualitätssicherung usw. verwendet wird.

### <a name="create-a-clone"></a>Erstellen eines Klons

SnapCenter ermöglicht es Ihnen, einen Klon zu erstellen, so dass Sie einen Snapshot als Point-in-Time-Referenz verwenden können. Sie können dann mit Hilfe von Zeigern einen ähnlichen Datensatz zwischen dem übergeordneten Volume und dem geklonten Volume erfassen. Der geklonte Datenträger ist lesend beschreibbar und wird nur durch Schreibvorgänge erweitert. Lesevorgänge finden jedoch weiterhin auf dem übergeordneten Volume statt. Mit diesem Feature können Sie ein „Duplikat“ der Daten erstellen, die für einen Host verfügbar sind, ohne die Daten zu beeinträchtigen, die auf dem übergeordneten Volume vorhanden sind. 

Diese Funktion ist besonders nützlich für Disaster-Recovery-Tests. Ein temporäres Dateisystem kann auf der Grundlage der gleichen Snapshots eingerichtet werden, die bei einer tatsächlichen Wiederherstellung verwendet werden. Sie können die Daten überprüfen und sicherstellen, dass Anwendungen wie erwartet funktionieren, und dann den Notfallwiederherstellungstest herunterfahren, ohne die Notfallwiederherstellungsvolumes oder die Replikation zu beeinträchtigen.

So wird eine Datenbank geklont: 

1. Wählen Sie in SnapCenter **Resources** (Ressourcen) und dann die zu klonende Datenbank aus. Wenn Sie den Klon lokal erstellen, fahren Sie mit dem nächsten Schritt fort. Wenn Sie den Klon am sekundären Speicherort wiederherstellen möchten, wählen Sie Sicherungen über dem Feld „Mirror Copies“ (Spiegelkopien) aus.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/clone-database-manage-copies.png" alt-text="Diagramm zum Klonen verwalteter Datenbankkopien":::

2. Wählen Sie die entsprechende Datensicherung aus der Liste der bereitgestellten Sicherungen aus. Wählen Sie **Klonen** aus.

    >[!NOTE]
    >Die Datensicherung muss vor dem Zeitstempel oder der Systemänderungsnummer (SCN) erstellt worden sein, wenn ein Zeitstempelklon erforderlich ist. 

3. Auf der Registerkarte **Name**: Geben Sie den Namen der **SID** für den Klon ein. Stellen Sie sicher, dass **Source Volume** (Quellvolume) und **Destination Volume** (Zielvolume) für Daten- und Archivprotokolle mit den Microsoft-Vorgängen für die Zuordnung der Notfallwiederherstellung übereinstimmen. Wählen Sie **Weiter** aus.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/provide-clone-database-sid.png" alt-text="Screenshot: Eingabe der Datenbank-SID":::

4. Auf der Registerkarte **Locations** (Speicherorte): An den Speicherorten der Datendateien, Steuerungsdateien und Wiederholungsprotokolle werden durch den Klonvorgang die erforderlichen Dateisysteme für die Wiederherstellung erstellt. Es wird empfohlen, diese unverändert zu lassen. Die Suche nach Alternativen geht über den Rahmen dieses Dokuments hinaus. Stellen Sie sicher, dass der entsprechende Host für den Speicherort der Wiederherstellung ausgewählt ist. Wählen Sie **Weiter** aus.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/clone-database-select-host.png" alt-text="Screenshot: Auswählen eines Hosts für das Klonen":::

5. Auf der Registerkarte **Credentials** (Anmeldeinformationen): Die Werte für Anmeldeinformationen und „Oracle Home Settings“ (Oracle Home-Einstellungen) werden vom vorhandenen Produktionsspeicherort aus gepullt. Es wird empfohlen, diese Werte nicht zu ändern, es sei denn, Sie wissen, dass der sekundäre Standort andere Werte hat als der primäre Standort. Wählen Sie **Weiter** aus.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/clone-database-credentials.png" alt-text="Screenshot: Eingabe der Datenbankanmeldeinformationen für den Klon":::

6. Auf der Registerkarte **PreOps:** Derzeit werden keine Skripts vor der Wiederherstellung unterstützt. Wählen Sie **Weiter** aus.

7. Auf der Registerkarte **PostOps:** Wenn die Datenbank nur bis zu einem bestimmten Zeitpunkt oder einer bestimmten SCN wiederhergestellt wird, wählen Sie das entsprechende Optionsfeld aus, und fügen Sie entweder den Zeitstempel oder die SCN hinzu. SnapCenter stellt die Datenbank bis zu diesem Punkt wieder her. Lassen Sie andernfalls **Until Cancel** (Bis Abbrechen) ausgewählt. Wählen Sie **Weiter** aus.

8. Auf der Registerkarte **Notification** (Benachrichtigung): Geben Sie die erforderlichen SMTP-Informationen ein, wenn Sie nach Abschluss des Klonens eine Benachrichtigungs-E-Mail senden möchten. Wählen Sie **Weiter** aus.

9. Auf der Registerkarte **Summary** (Zusammenfassung): Überprüfen Sie, ob die entsprechende **Clone-SID** (Klon-SID) eingegeben und der richtige Host ausgewählt wurde. Scrollen Sie nach unten, um sicherzustellen, dass der entsprechende Wiederherstellungsbereich eingegeben wurde. Wählen Sie **Fertig stellen** aus.

10. Der Klonauftrag wird im aktiven Popupfenster am unteren Bildschirmrand angezeigt. Wählen Sie die Klonaktivität aus, um die Auftragsdetails anzuzeigen. Sobald die Aktivität abgeschlossen ist, werden auf der Seite „Job Details“ (Auftragsdetails) ausschließlich grüne Häkchen angezeigt, und die Abschlusszeit wird angegeben. Das Klonen dauert in der Regel etwa 7 bis 10 Minuten.

11. Wechseln Sie nach Abschluss des Auftrags zu dem Host, der als Ziel für den Klon verwendet wird, und überprüfen Sie die Bereitstellungspunkte mit „cat /etc/fstab“. Diese Überprüfung stellt sicher, dass die entsprechenden Einhängepunkte für die im Klon-Assistenten angegebene Datenbank vorhanden sind. Sie hebt auch die im Assistenten eingegebene Datenbank-SID hervor. Im folgenden Beispiel ist die SID „dbsc4“, wie von den Bereitstellungspunkten auf dem Host angegeben.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/clone-database-switch-to-target-host.png" alt-text="Screenshot: Befehl zum Wechseln zum Zielhost":::

12. Geben Sie auf dem Host **oraenv** ein, und drücken Sie die **EINGABETASTE**. Geben Sie die neu erstellte SID ein.

    Sie müssen sicherstellen, dass die Datenbank ordnungsgemäß wiederhergestellt wird. Die folgenden zusätzlichen Schritte basieren jedoch auf der Datenbank, die von einem anderen Benutzer als Oracle erstellt wurde. 

13. Geben Sie **sqlplus / as sysdba** ein. Da die Tabelle unter einem anderen Benutzer erstellt wurde, werden der ursprüngliche, ungültige Benutzername und das Kennwort automatisch eingegeben. Geben Sie den richtigen Benutzernamen und das Kennwort ein. Die SQL>-Eingabeaufforderung wird angezeigt, sobald die Anmeldung erfolgreich war.

Geben Sie eine Abfrage der Basisdatenbank ein, um zu überprüfen, ob die entsprechenden Daten empfangen werden. Im folgenden Beispiel haben wir die Archivprotokolle verwendet, um ein Rollforward für die Datenbank auszuführen. Die folgende Abbildung zeigt, dass die Archivprotokolle ordnungsgemäß verwendet wurden, da der Eintrag für den Klontest nach der Erstellung der Datensicherung erstellt wurde. Würden die Archivierungsprotokolle also nicht fortgeschrieben, wäre dieser Eintrag nicht aufgeführt.

```sql
SQL> select * from acolvin.t;

COL1
-----------------------------------------
COL2
---------------
first insert
17-DEC-20

log restore
17-DEC-20

clonetest
18-DEC-20

COL1
-----------------------------------------
COL2
---------------
after first insert
17-DEC-20

next insert
17-DEC-20

final insert
18-DEC-20

COL1
-----------------------------------------
COL2
---------------
BILLY
17-DEC-20

7 rows selected.

```

### <a name="delete-a-clone"></a>Löschen eines Klons

Es ist wichtig, einen Klon zu löschen, wenn Sie diesen nicht mehr benötigen. Wenn Sie den Klon weiterhin verwenden möchten, sollten Sie ihn teilen. Ein Snapshot, der als übergeordneter Snapshot für einen Klon dient, kann nicht gelöscht werden und wird im Rahmen der Aufbewahrungszählung übersprungen. Wenn Sie Klone, die Sie nicht mehr benötigen, nicht löschen, könnte die Anzahl der von Ihnen beibehaltenen Momentaufnahmen übermäßig viel Speicher belegen. 

Ein geteilter Klon kopiert die Daten aus dem vorhandenen Volume und die Momentaufnahme in ein neues Volume. Bei diesem Prozess wird die Beziehung zwischen dem Klon und der übergeordneten Momentaufnahme entfernt, sodass die Momentaufnahme gelöscht werden kann, wenn die Aufbewahrungsnummer erreicht ist. Wenn die Aufbewahrungsnummer bereits erreicht wurde, wird die Momentaufnahme bei der nächsten Momentaufnahme gelöscht. Trotzdem entstehen bei einem geteilten Klon auch Speicherkosten.

Wenn ein Klon erstellt wird, listet die Registerkarte „Resource“ (Ressource) für diese Datenbank einen Klon auf, der lokal oder remote vorhanden ist. So löschen Sie den Klon:

1. Auf der Registerkarte **Resource** (Ressource): Wählen Sie das Feld mit dem Klon aus, den Sie löschen möchten.

2. Auf der Seite „Secondary Mirror Clone(s)“ (Sekundäre Spiegelklone) wird der Klon angezeigt. In diesem Beispiel befindet sich der Klon am sekundären Speicherort. Klicken Sie in der rechten oberen Ecke der Klonliste auf **Delete** (Löschen).

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/delete-clone-secondary-mirror-clones.png" alt-text="Screenshot: sekundäre Spiegelklone":::

3. Stellen Sie sicher, dass Sie SQLPLUS verlassen haben, bevor Sie den Vorgang ausführen. Klicken Sie auf **OK**.

4. Klicken Sie im linken Menü auf **Jobs** (Aufträge), um den Auftragsfortschritt zu sehen. Wenn der Klon gelöscht wurde, wird ein grünes Häkchen angezeigt.

5. Nachdem der Klon gelöscht wurde, kann es auch nützlich sein, die Bereitstellung der Archivprotokolle, die als Teil des Klonprozesses verwendet wurden, ggf. aufzuheben. In der entsprechenden Sicherungsliste (von der Erstellung des Klons) kann die Liste der Sicherungen danach sortiert werden, ob eine Sicherung bereitgestellt wurde. Der Sortieralgorithmus ist nicht perfekt und sortiert nicht immer alle Elemente zusammen, sortiert aber im Allgemeinen in die richtige Richtung. Das folgende Volume wurde zuvor bereitgestellt. Wählen Sie **Unmount** (Bereitstellung aufheben) aus.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/delete-clone-unmount-backup.png" alt-text="Screenshot: Liste der Sicherungen":::

6. Klicken Sie auf **OK**. Sie können den Status des Auftrags zum Aufheben der Bereitstellung anzeigen, indem Sie im linken Menü **Monitor** (Überwachen) auswählen. Wenn die Bereitstellung der Sicherung aufgehoben wurde, wird ein grünes Häkchen angezeigt.

### <a name="split-a-clone"></a>Teilen eines Klons

Die Aufteilung eines Klons erstellt eine Kopie des übergeordneten Volumes. Dabei werden alle Daten auf dem übergeordneten Volume bis zu dem Punkt repliziert, an dem der zur Erstellung des Klons verwendete Snapshot erstellt wurde. Dadurch wird das übergeordnete Volume vom Klon-Volume getrennt und die Sperrung des Snapshots, der zur Erstellung des Klon-Volumes verwendet wurde, aufgehoben. Die Momentaufnahme kann dann im Rahmen der Aufbewahrungsrichtlinie gelöscht werden.

Das Teilen eines Klons ist nützlich, um Daten entweder in der Produktionsumgebung oder in der Notfallwiederherstellungsumgebung aufzufüllen. Dadurch können die neuen Datenträger unabhängig vom übergeordneten Datenträger funktionieren.

>[!NOTE]
>Der Prozess zum Teilen des Klons kann nicht rückgängig gemacht oder abgebrochen werden.

So teilen Sie einen Klon:

1. Wählen Sie die Datenbank aus, die bereits einen Klon enthält.

2. Sobald der Speicherort des Klons ausgewählt ist, wird er in der Liste auf der Seite „Secondary Mirror Clone(s)“ (Sekundäre Spiegelklone) angezeigt.

3. Wählen Sie direkt über der Liste der Klone die Option **Clone Split** (Klon teilen) aus. SnapCenter lässt keine Änderungen im Rahmen des Teilungsprozesses zu, sodass auf der nächsten Seite einfach angezeigt wird, welche Änderungen vorgenommen werden. Wählen Sie **Starten** aus.

    >[!NOTE]
    >Je nachdem, wie viele Daten kopiert werden müssen, je nach Layout der Datenbank im Speicher und der Aktivitätsebene des Speichers kann der Teilungsprozess sehr lange dauern.

Nach Abschluss des Vorgangs wird der geteilte Klon aus der Liste der Backups entfernt. Der Snapshot, der mit dem Klon verknüpft ist, kann nun im Rahmen des normalen Aufbewahrungsplans auf SnapCenter entfernt werden.

## <a name="restore-database-remotely-after-disaster-recovery-event"></a>Remotewiederherstellung der Datenbank nach einem Notfallwiederherstellungsereignis

SnapCenter ist derzeit nicht für die Automatisierung des Failoverprozesses konzipiert. Wenn ein Notfallwiederherstellungsereignis auftritt, sind entweder Microsoft Operations- oder REST-Wiederherstellungsskripts erforderlich, um die Datenbank am sekundären Speicherort wiederherzustellen. Es geht derzeit über den Rahmen dieses Dokuments hinaus, den Prozess der Ausführung der REST-Wiederherstellungsskripts zu beschreiben.

## <a name="restart-all-rac-nodes-after-restore"></a>Neustarten aller RAC-Knoten nach der Wiederherstellung

Nachdem Sie die Datenbank in SnapCenter wiederhergestellt haben, ist sie wie unten dargestellt nur auf dem RAC-Knoten aktiv, den Sie beim Wiederherstellen der Datenbank ausgewählt haben. In diesem Beispiel haben wir die Datenbank auf bn6sc2 wiederhergestellt, um zu zeigen, dass Sie einen beliebigen RAC-Knoten für die Wiederherstellung auswählen können.

:::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-database-example-output-rac-node.png" alt-text="Screenshot: auf „bn6sc2“ wiederhergestellte Datenbank":::

Starten Sie die verbleibenden RAC-Knoten mithilfe des Befehls _srvctl start database_. Überprüfen Sie nach Abschluss des Befehls, ob alle RAC-Knoten teilnehmen.

:::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-database-srvctl-start-database-command.png" alt-text="Screenshot: Befehl „srvctl start database“ zum Neustarten der verbleibenden RAC-Knoten":::

## <a name="unmount-log-archive-volume"></a>Aufheben der Bereitstellung des Protokollarchivvolumes

Nach dem Klonen oder Wiederherstellen einer Datenbank sollte die Bereitstellung des Protokollarchivvolumes aufgehoben werden, da es nur zum Wiederherstellen der Datenbank und dann nicht mehr verwendet wird. 

1. Wählen Sie auf der Registerkarte **Resources** (Ressourcen) die entsprechende Sicherungsliste für die Datenbank aus, indem Sie entweder die lokale Liste oder die Remoteliste auswählen.

2. Nachdem Sie die entsprechende Sicherungsliste ausgewählt haben, sortieren Sie die Liste der Sicherungen danach, ob eine Sicherung bereitgestellt wurde. Der Sortieralgorithmus ist nicht perfekt und sortiert nicht immer alle Elemente zusammen, liefert jedoch nutzbare Ergebnisse. 
 
3. Sobald Sie das Volume finden, das Sie zuvor bereitgestellt haben, wählen Sie **Unmount** (Bereitstellung aufheben) aus. Klicken Sie auf **OK**.

4. Sie können den Status des Auftrags zum Aufheben der Bereitstellung anzeigen, indem Sie im linken Menü **Monitor** (Überwachen) auswählen. Wenn die Bereitstellung des Volumes aufgehoben wurde, wird ein grünes Häkchen angezeigt.

>[!NOTE]
>Wenn Sie unmounten wählen, wird der Eintrag aus der /etc/fstab auf dem Rechner, auf dem er eingehängt ist, entfernt. Durch das Aufheben der Bereitstellung wird die Sicherung nach Bedarf im Rahmen der in der Schutzrichtlinie festgelegten Aufbewahrungsrichtlinie zur Löschung freigegeben.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Hochverfügbarkeit und Notfallwiederherstellung für Oracle in der Bare-Metal-Infrastruktur:

> [!div class="nextstepaction"]
> [Hochverfügbarkeit und Notfallwiederherstellung für Oracle in der Bare-Metal-Infrastruktur](concepts-oracle-high-availability.md)
