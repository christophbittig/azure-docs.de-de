---
title: Sichern von Dateidaten mit MABS
description: Sie können Dateidaten mit MABS auf Server- und Clientcomputern sichern.
ms.topic: conceptual
ms.date: 08/19/2021
ms.openlocfilehash: 71b12c98713dff2340bdda1baf486dcd5cfdb70a
ms.sourcegitcommit: deb5717df5a3c952115e452f206052737366df46
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122681666"
---
# <a name="back-up-file-data-with-mabs"></a>Sichern von Dateidaten mit MABS

Sie können Microsoft Azure Backup Server (MABS) verwenden, um Dateidaten auf Server- und Clientcomputern zu sichern.

## <a name="before-you-start"></a>Vorbereitung

1. **Bereitstellen von MABS:** Stellen Sie sicher, dass MABS ordnungsgemäß installiert und bereitgestellt wurde. Sie sollten die folgenden Artikel lesen:

   - [Installieren von MABS](backup-azure-microsoft-azure-backup.md)

   - [Was kann MABS sichern?](backup-mabs-protection-matrix.md)

   - [Neuerungen in MABS](backup-mabs-whats-new-mabs.md)

   - [MABS-Versionshinweise](backup-mabs-release-notes-v3.md)

1. **Einrichten von Speicher**: Sie können die gesicherten Daten auf Datenträger, auf Band und mit Azure in der Cloud speichern. Weitere Informationen finden Sie unter [Vorbereiten des Datenspeichers](/system-center/dpm/plan-long-and-short-term-data-storage?view=sc-dpm-2019&preserve-view=trus).

1. **Einrichten des MABS-Schutz-Agents:** Sie müssen den MABS-Schutz-Agent auf jedem Computer installieren, den Sie sichern möchten. Lesen Sie dazu [Bereitstellen des MABS-Schutz-Agents](backup-azure-microsoft-azure-backup.md).

## <a name="back-up-file-data"></a>Sichern von Dateidaten

Nachdem Sie Ihre MABS-Infrastruktur eingerichtet haben, können Sie Schutzcomputer aktivieren, die Dateidaten enthalten, die Sie sichern möchten.

1. Um eine Schutzgruppe zu erstellen, klicken Sie auf **Schutz** > **Aktionen** > **Schutzgruppe erstellen**, um den **Assistenten zum Erstellen einer neuen Schutzgruppe** in der MABS-Konsole zu öffnen.

1. Wählen Sie in **Schutzgruppentyp auswählen** die Option **Server** aus.

1. In **Gruppenmitglieder** fügen Sie die Computer hinzu, für die Sie Dateidaten in der Schutzgruppe sichern möchten. Auf diesen Computern wählen Sie die Speicherorte, Freigaben und Ordner aus, die Sie schützen möchten.  [Stellen Sie Schutzgruppen bereit](backup-support-matrix-mabs-dpm.md). Sie können verschiedene Typen von Ordnern (z. B. „Desktop“), verschiedene Dateien oder das gesamte Volume auswählen. Sie können auch bestimmte Speicherorte vom Schutz ausschließen.

   >[!NOTE]
   > Wenn Sie das Volume schützen, auf dem die Deduplizierung aktiviert ist, stellen Sie sicher, dass die Serverrolle [Datendeduplizierung](/windows-server/storage/data-deduplication/install-enable) auf dem MABS-Server installiert ist. Ausführliche Informationen zur Deduplizierung finden Sie in der [Unterstützungsmatrix](backup-support-matrix-mabs-dpm.md#deduplicated-volumes-support).   

1. Geben Sie in **Datenschutzmethode auswählen** an, wie Sie die kurz- und langfristige Sicherung handhaben möchten. Die kurzfristige Sicherung erfolgt immer zunächst auf die Festplatte und umfasst die Option zum Sichern von der Festplatte in der Azure-Cloud mit Azure Backup (kurz- oder langfristig). Als Alternative zur langfristigen Sicherung in der Cloud können Sie auch die langfristige Sicherung auf einem eigenständigen Bandgerät oder in einer Bandbibliothek konfigurieren, die mit dem MABS-Server verbunden ist.

1. Geben Sie in **Kurzfristige Ziele auswählen** an, wie die Sicherung auf dem Datenträger im kurzfristigen Speicher erfolgen soll.  Geben Sie unter **Beibehaltungsdauer** an, wie lange die Daten auf dem Datenträger beibehalten werden sollen. Geben Sie in **Synchronisierungsfrequenz** an, wie oft eine inkrementelle Sicherung auf dem Datenträger ausgeführt werden soll. Wenn Sie kein Sicherungsintervall festlegen möchten, können Sie **Direkt vor einem Wiederherstellungspunkt** aktivieren, damit MABS unmittelbar vor jedem geplanten Wiederherstellungspunkt eine schnelle vollständige Sicherung ausführt.

1. Wenn Sie Daten zur langfristigen Speicherung auf Band speichern möchten, geben Sie in **Langfristige Ziele angeben** an, wie lange die Banddaten (1 – 99 Jahre) erhalten bleiben sollen. Geben Sie in „Sicherungshäufigkeit“ an, wie oft Sicherungen auf Band ausgeführt werden sollen. Die Häufigkeit basiert auf der angegebenen Beibehaltungsdauer:

   - Wenn die Beibehaltungsdauer 1 – 99 Jahre beträgt, können Sie festlegen, dass Sicherungen täglich, wöchentlich, alle zwei Wochen, monatlich, vierteljährlich, halbjährlich oder jährlich erfolgen sollen.

   - Wenn die Beibehaltungsdauer 1 – 11 Monate beträgt, können Sie festlegen, dass Sicherungen täglich, wöchentlich, alle zwei Wochen oder monatlich erfolgen sollen.

   - Wenn die Beibehaltungsdauer 1 – 4 Wochen beträgt, können Sie festlegen, dass Sicherungen täglich oder wöchentlich erfolgen sollen.

   Auf einem eigenständigen Bandlaufwerk für eine einzelne Schutzgruppe verwendet MABS so lange dasselbe Band für tägliche Sicherungen, bis der Speicherplatz auf dem Band nicht mehr ausreicht. Sie können auch Daten aus verschiedenen Schutzgruppen auf Band zusammenstellen.

   Geben Sie auf der Seite für **Band- und Bibliothekdetails angeben** das zu verwendende Band bzw. die zu verwendende Bibliothek an. Geben Sie zudem an, ob die Daten auf Band komprimiert und verschlüsselt werden sollen.

1. Überprüfen Sie in **Datenträgerzuordnung überprüfen** den für die Schutzgruppe zugeordneten Speicherplatz des Speicherpools.

   **Gesamtdatengröße** ist die Größe der Daten, die Sie sichern möchten. **Bereitzustellender Speicherplatz für MABS** ist der für die Schutzgruppe von MABS empfohlene Speicherplatz. MABS wählt auf Grundlage der Einstellungen das ideale Sicherungsvolume. Sie können jedoch in **Details zur Datenträgerzuordnung** das gewählte Sicherungsvolume ändern. Wählen Sie für die Workloads im Dropdownmenü den bevorzugten Speicher aus. Durch Ihre Änderungen verändern sich im Bereich **Verfügbarer Speicherplatz** die Werte für **Gesamtspeicher** und **Freier Speicher**. Nicht ausreichend bereitgestellter Speicherplatz ist die Menge an Speicher, die laut Vorschlag von MABS zum Sicherstellen künftiger reibungsloser Sicherungen dem Volume hinzugefügt werden sollte.

1. Wählen Sie in **Replikaterstellungsmethode auswählen** aus, wie Sie die erste vollständige Datenreplikation handhaben möchten.  Wenn Sie über das Netzwerk replizieren, wird empfohlen, dass Sie eine Zeit außerhalb der Spitzenzeiten auswählen. Ziehen Sie bei großen Datenmengen oder nicht optimalen Netzwerkbedingungen die Offlinereplikation der Daten mit Wechselmedien in Betracht.

1. Legen Sie unter **Konsistenzprüfungsoptionen auswählen** fest, wie Konsistenzprüfungen automatisiert werden sollen. Sie können eine Überprüfung aktivieren, die nur bei inkonsistenten Replikatdaten oder gemäß einem festgelegten Zeitplan ausgeführt wird. Wenn Sie keine automatische Konsistenzprüfung konfigurieren möchten, können Sie jederzeit eine manuelle Überprüfung ausführen, indem Sie im Bereich **Schutz** der MABS-Konsole mit der rechten Maustaste auf die Schutzgruppe klicken und **Konsistenzprüfung ausführen** auswählen.

1. Wenn Sie die Sicherung in die Cloud mit Azure Backup ausgewählt haben, vergewissern Sie sich, dass auf der Seite **Online zu schützende Daten angeben** die Workloads ausgewählt sind, die Sie in Azure sichern möchten.

1. Geben Sie in **Online-Sicherungszeitplan angeben** an, wie häufig inkrementelle Sicherungen in Azure erfolgen sollen. Sie können tägliche, wöchentliche, monatliche und jährliche Sicherungen planen und Datum und Uhrzeit ihrer Ausführung festlegen. Sicherungen können bis zu zweimal täglich erfolgen. Bei jeder Ausführung einer Sicherung wird ein Datenwiederherstellungspunkt in Azure anhand der gesicherten Daten erstellt, die auf dem MABS-Datenträger gespeichert sind.

1. In **Online-Aufbewahrungsrichtlinie angeben** können Sie angeben, wie die aus den täglichen/wöchentlichen/monatlichen/jährlichen Sicherungen erstellten Wiederherstellungspunkte in Azure beibehalten werden.

1. Geben Sie in **Onlinereplikation auswählen** an, wie die erste vollständige Replikation der Daten erfolgt. Sie können eine Replikation über das Netzwerk wählen oder eine Offlinesicherung (sog. Offlineseeding) durchführen. Die Offlinesicherung erfolgt mithilfe der Importfunktion von Azure. [Weitere Informationen](/azure/backup/backup-azure-backup-import-export).

1. Überprüfen Sie auf der Seite **Zusammenfassung** die Einstellungen. Nachdem Sie auf **Gruppe erstellen** geklickt haben, erfolgt die erste Replikation der Daten. Nach Fertigstellung wird auf der Seite **Status** der Schutzgruppenstatus **OK** angezeigt. Die Sicherung wird anschließend unter Berücksichtigung der Schutzgruppeneinstellungen durchgeführt.

## <a name="recover-backed-up-file-data"></a>Wiederherstellen gesicherter Dateidaten

Sie stellen Daten mithilfe des Wiederherstellungs-Assistenten wieder her. Wenn Sie im Bereich **Geschützte Daten** des Assistenten auf ein geschütztes Volume doppelklicken, werden die Daten zu diesem Volume im Ergebnisbereich von MABS angezeigt. Sie können die Namen der geschützten Server alphabetisch filtern, indem Sie auf **Filter** klicken. Nachdem Sie eine wiederherzustellende Datenquelle in der Strukturansicht ausgewählt haben, können Sie einen bestimmten Wiederherstellungspunkt auswählen. Klicken Sie dazu auf ein fett dargestelltes Datum im Kalender. Wenn Sie im **Aktionsbereich** auf **Wiederherstellen** klicken, startet MABS den Wiederherstellungsauftrag.

## <a name="recover-data"></a>Daten wiederherstellen
Gehen Sie wie folgt vor, um Daten an der MABS-Verwaltungskonsole wiederherzustellen:

1. Klicken Sie in der MABS-Konsole auf der Navigationsleiste auf **Wiederherstellung**. Navigieren Sie dann zu den wiederherzustellenden Daten. Wählen Sie die Daten im Ergebnisbereich aus.

1. Verfügbare Wiederherstellungspunkte werden im Kalender des Wiederherstellungspunktebereichs durch Fettschrift hervorgehoben. Wählen Sie das fett dargestellte Datum für den Wiederherstellungspunkt aus, der wiederhergestellt werden soll.

1. Wählen Sie im Bereich **Wiederherstellbares Element** durch Klicken das Element aus, das Sie wiederherstellen möchten.

1. Klicken Sie im Bereich **Aktionen** auf **Wiederherstellen**, um den Wiederherstellungs-Assistenten zu öffnen.

1. Sie können Daten wie folgt wiederherstellen:

   1. **Am ursprünglichen Speicherort wiederherstellen:** Beachten Sie, dass dies nicht funktioniert, wenn der Clientcomputer über VPN verbunden ist. In diesem Fall verwenden Sie einen anderen Speicherort und kopieren dann die Daten von diesem Speicherort.

   1. **An anderem Speicherort wiederherstellen:**

   1. **Auf Band kopieren:** Mit dieser Option wird das Volume, das die ausgewählten Daten enthält, auf ein Band in einer MABS-Bibliothek kopiert. Sie können hier auch angeben, ob die Daten auf dem Band komprimiert oder verschlüsselt werden sollen.

1. Geben Sie die gewünschten Wiederherstellungsoptionen an:

   1. **Wiederherstellungsverhalten für vorhandene Versionen:** Wählen Sie **Kopie erstellen**, **Überspringen** oder **Überschreiben** aus. Diese Option ist nur aktiviert, wenn die Wiederherstellung am ursprünglichen Speicherort erfolgt.

   1. **Sicherheit wiederherstellen:** Wählen Sie **Sicherheitseinstellungen des Zielcomputers anwenden** oder **Sicherheitseinstellungen der Wiederherstellungspunktversion anwenden** aus.

   1. **Netzwerk-Bandbreiteneinschränkung**. Klicken Sie auf **Ändern** , um die Netzwerk-Bandbreiteneinschränkung zu aktivieren.

   1. **SAN-basierte Wiederherstellung mithilfe von Hardwaresnapshots aktivieren:** Wählen Sie diese Option aus, um SAN-basierte Hardwaresnapshots für eine schnellere Wiederherstellung zu verwenden.
      
      Diese Option ist nur dann gültig, wenn beim verwendeten Speichernetzwerk (SAN) die Funktion für Hardwaremomentaufnahmen aktiviert ist, ein Klon im SAN erstellt und geteilt werden kann, um ihn beschreibbar zu machen, und der geschützte Computer und der MABS-Server mit demselben SAN verbunden sind.

   1. **Benachrichtigung:** Klicken Sie auf **Eine E-Mail senden, wenn diese Wiederherstellung abgeschlossen ist**, und geben Sie dann die Empfänger an, die eine Benachrichtigung erhalten sollen. Trennen Sie die E-Mail-Adressen durch Kommas.

1. Klicken Sie auf **Weiter** , nachdem Sie die zuvor beschriebenen Optionen ausgewählt haben.

1. Überprüfen Sie die Wiederherstellungseinstellungen, und klicken Sie auf **Wiederherstellen**. Beachten Sie, dass alle Synchronisierungsaufträge für das ausgewählte Wiederherstellungselement abgebrochen werden, während die Wiederherstellung ausgeführt wird.

Wenn Sie Modern Backup Storage (MBS) verwenden, wird die Wiederherstellung durch Endbenutzer für Dateiserver nicht unterstützt. Die Wiederherstellung durch Endbenutzer mithilfe eines Dateiservers ist vom Volumeschattenkopie-Dienst (VSS) abhängig, der keinen Modern Backup Storage verwendet. Wenn die Wiederherstellung durch Endbenutzer aktiviert ist, können Sie die Daten wie folgt wiederherstellen:

1. Navigieren Sie zu der geschützten Datendatei. Klicken Sie mit der rechten Maustaste auf den Dateinamen, und wählen Sie **Eigenschaften** aus.

1. Wählen Sie unter **Eigenschaften** > **Vorherige Version** die Version aus, die Sie wiederherstellen möchten.
