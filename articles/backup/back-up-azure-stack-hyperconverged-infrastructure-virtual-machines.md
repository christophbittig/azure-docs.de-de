---
title: Sichern von Azure Stack HCI-VMs mit Azure Backup Server
description: Dieser Artikel enthält die Verfahren zum Sichern und Wiederherstellen virtueller Computer mit Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 07/27/2021
ms.openlocfilehash: e7a653f008541db548b4468a70c429fe72701797
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/27/2021
ms.locfileid: "114722616"
---
# <a name="back-up-azure-stack-hci-virtual-machines-with-azure-backup-server"></a>Sichern von Azure Stack HCI-VMs mit Azure Backup Server

In diesem Artikel wird das Sichern von VMs in Azure Stack HCI mit Microsoft Azure Backup Server (MABS) beschrieben.

## <a name="supported-scenarios"></a>Unterstützte Szenarios

MABS kann Azure Stack HCI-VMs in den folgenden Szenarien sichern:

- **Azure Stack HCI-Host**: Sie können den Systemstatus/BMR des Azure Stack HCI-Hosts sichern und wiederherstellen. Der MABS-Schutz-Agent muss auf dem Host installiert sein.

- **VMs im Cluster mit lokalem oder direkt angefügtem Speicher**: Sichern Sie virtuelle Gastcomputer in einem Cluster, der über lokalen oder direkt angefügten Speicher verfügt. Beispiel: Festplatte, SAN- (Storage Area Network) oder NAS-Gerät (Network Attached Storage).

- **VMs in einem Cluster mit CSV-Speicher**: Sichern Sie virtuelle Computer, die in einem Azure Stack HCI-Cluster mit freigegebenem Clustervolumespeicher (Cluster Shared Volume, CSV) gehostet werden. Der MABS-Schutz-Agent wird auf jedem Clusterknoten installiert.

- **Verschieben von VMs innerhalb eines Clusters**: Wenn VMs innerhalb eines Stretched- bzw. normalen Clusters verschoben werden, schützt MABS die VMs weiterhin, solange der MABS-Schutz-Agent auf dem Azure Stack HCI-Host installiert ist. Wie MABS die virtuellen Computer schützt, hängt von der Art der Livemigration ab. Beim Verschieben einer VM innerhalb eines Clusters wird die Migration von MABS erkannt und die VM vom neuen Clusterknoten aus gesichert, ohne dass der Benutzer eingreifen muss. Da sich der Speicherort nicht geändert hat, fährt MABS mit einer vollständige Schnellsicherung fort. 

- **Verschieben einer VM in einen anderen Stretched bzw. normalen Cluster**: Das Verschieben einer VM in einen anderen Stretched bzw. normalen Cluster wird nicht unterstützt.

## <a name="host-versus-guest-backup"></a>Sicherung auf Host- und Gastebene im Vergleich

MABS kann in Azure Stack HCI eine Sicherung von VMs auf Host- oder Gastebene durchführen. Auf Hostebene ist der MABS-Schutz-Agent auf dem Azure Stack HCI-Hostserver oder im Cluster installiert und schützt die gesamten VMs und Datendateien, die auf dem jeweiligen Host ausgeführt werden.   Auf Gastebene wird der Agent auf allen virtuellen Computern installiert und schützt somit die auf der jeweiligen VM vorhandene Workload.

Beide Methoden haben Vor- und Nachteile:

- Sicherungen auf Hostebene sind flexibel, da Sie unabhängig von der Art des Betriebssystems auf den Gastcomputern arbeiten, und es ist nicht erforderlich, dass der MABS-Schutz-Agent auf jeder VM installiert ist. Wenn Sie Sicherungen auf Hostebene bereitstellen, stellen Sie einen kompletten virtuellen Computer oder Dateien und Ordner wieder her (Wiederherstellung auf Elementebene).

- Die Sicherung auf Gastebene ist für den Schutz bestimmter Workloads auf einem virtuellen Computer von Vorteil. Auf Hostebene können Sie eine gesamte VM oder bestimmte Dateien wiederherstellen, jedoch keine Daten im Kontext einer bestimmten Anwendung. Um beispielsweise bestimmte SharePoint-Elemente von einer gesicherten VM wiederherzustellen, sollten Sie eine Sicherung auf Gastebene dieser VM durchführen. Wenn Sie auf Pass-Through-Datenträgern gespeicherte Daten schützen möchten, müssen Sie die Sicherung auf Gastebene wählen. Die Pass-Through-Technologie ermöglicht dem virtuellen Computer den Direktzugriff auf das Speichergerät und speichert keine virtuellen Volumedaten in einer VHD-Datei.

## <a name="backup-prerequisites"></a>Voraussetzungen für eine Sicherung

Dies sind die Voraussetzungen für die Sicherung von VMs mit MABS:

| Voraussetzung | Details |
| ------------ | ------- |
| MABS-Voraussetzungen | <ul> <li>Wenn Sie eine Wiederherstellung auf Elementebene für VMs (Dateien, Ordner, Volumes) durchführen möchten, müssen Sie auf dem MABS-Server die Hyper-V-Rolle installieren. Wenn Sie eine Wiederherstellung auf Elementebene für virtuelle Computer (Dateien, Ordner, Volumes) durchführen möchten, müssen Sie die Hyper-V-Rolle auf dem MABS-Server installieren.</li> <li>Sie können bis zu 800 virtuelle Computer mit je 100 GB auf einem MABS-Server schützen und mehrere MABS-Server zulassen, die größere Cluster unterstützen.</li> <li>MABS schließt die Auslagerungsdatei von inkrementellen Sicherungen aus, um die Sicherungsleistung von VMs zu verbessern.</li> <li>MABS kann einen Server oder Cluster in der gleichen Domäne sichern, in der sich auch der MABS-Server befindet, oder in einer Unter- oder vertrauenswürdigen Domäne. Wenn Sie VMs in einer Arbeitsgruppe oder einer nicht vertrauenswürdigen Domäne sichern möchten, müssen Sie eine Authentifizierung einrichten. Für einen einzelnen Server können Sie NTLM- oder Zertifikatauthentifizierung verwenden. Bei einem Cluster können Sie nur die Zertifikatauthentifizierung verwenden.</li> <li>Die Verwendung von Sicherungen auf Hostebene zum Sichern von VM-Daten auf Pass-Through-Datenträgern wird nicht unterstützt. In diesem Szenario empfehlen wir Ihnen, zum Sichern von VHD-Dateien eine Sicherung auf Hostebene und zum Sichern der anderen Daten, die auf dem Host nicht sichtbar sind, eine Sicherung auf Gastebene durchzuführen.</li> <li>Sie können VMs sichern, die auf deduplizierten Volumes gespeichert sind.</li> </ul> |
| VM | <ul> <li> Die Version der Integrationskomponenten, die auf der VM ausgeführt wird, muss mit der Version des Azure Stack HCI-Hosts identisch sein. </li> <li> Für jede VM-Sicherung benötigen Sie freien Speicherplatz auf dem Volume, auf dem sich die VHD-Dateien befinden, damit während der Sicherung ausreichend Platz für differenzierende Datenträger (AVHD) vorhanden ist. Der Speicherplatz muss mindestens der berechneten Fensterzeit Anfängliche Datenträgergröße *Änderungsrate* Sicherung entsprechen. Wenn Sie mehrere Sicherungen auf einem Cluster ausführen, benötigen Sie genügend Speicherkapazität für die AVHDs für jeden virtuellen Computer, der diese Berechnung verwendet. </li> </ul> |
| Linux-Voraussetzungen | <ul><li> Sie können Linux-VMs mithilfe von MABS sichern. Es werden nur dateikonsistente Momentaufnahmen unterstützt.</li></ul> |

## <a name="back-up-virtual-machines"></a>Sichern virtueller Computer

1. Richten Sie Ihren [MABS-Server](backup-azure-microsoft-azure-backup.md) und [Ihren Speicher](backup-mabs-add-storage.md) ein. Verwenden Sie bei der Einrichtung Ihres Speichers diese Richtlinien zur Speicherkapazität.

   - Durchschnittliche VM-Größe: 100 GB
   - Anzahl virtueller Computer pro MABS-Server: 800
   - Gesamtgröße von 800 VMs: 80 TB
   - Erforderlicher Speicherplatz für den Sicherungsspeicher: 80 TB

2. Richten Sie den MABS-Schutz-Agent auf dem Server oder jedem Clusterknoten ein.

3. Wählen Sie in der MABS-Administratorkonsole **Schutz** > **Schutzgruppe erstellen** aus, um den Assistenten **Neue Schutzgruppe erstellen** zu öffnen.

4. Wählen Sie auf der Seite **Gruppenmitglieder auswählen** die VMs aus, die Sie vor den Hostservern schützen möchten, auf denen sie sich befinden. Wir empfehlen Ihnen, alle VMs, die die gleiche Schutzrichtlinie verwenden, in einer Schutzgruppe zusammenzufassen. Um den Speicherplatz effizient zu nutzen, aktivieren Sie die Zusammenstellung. Mit der Zusammenstellung können Sie Daten aus verschiedenen Schutzgruppen auf demselben Datenträger oder Bandspeicher suchen, sodass mehrere Datenquellen ein einzelnes Replikat- und Wiederherstellungspunkt-Volume verwenden.

5. Geben Sie auf der Seite **Methode für die Datensicherheit auswählen** einen Namen für die Schutzgruppe ein. Wählen Sie **Ich möchte einen kurzfristigen Schutz mit Datenträger** und dann **Ich möchte Onlineschutz** aus, wenn Sie Daten mit dem Azure Backup-Dienst in Azure sichern möchten.

6. Geben Sie unter **Kurzfristige Ziele angeben** > **Aufbewahrungsdauer** an, wie lange Sie Datenträgerdaten aufbewahren möchten. Geben Sie unter **Synchronisierungsfrequenz** an, wie oft inkrementelle Sicherungen der Daten durchgeführt werden sollen. Alternativ können Sie anstelle der Auswahl eines Intervalls für inkrementelle Sicherungen auch **Direkt vor einen Wiederherstellungspunkt** aktivieren. Mit dieser Einstellung führt MABS eine vollständige Schnellsicherung unmittelbar vor jedem geplanten Wiederherstellungspunkt aus.

    > [!NOTE]
    >Wenn Sie Anwendungsworkloads schützen, werden Wiederherstellungspunkte gemäß der Synchronisierungsfrequenz erstellt, sofern die Anwendung inkrementelle Sicherungen unterstützt. Wenn dies nicht der Fall ist, führt MABS anstelle einer inkrementellen Sicherung eine vollständige Schnellsicherung durch und erstellt Wiederherstellungspunkte gemäß dem Zeitplan für die Schnellsicherung.<br></br>Die Prüfpunkte, die virtuellen Computer zugeordnet sind, werden nicht gesichert.

7. Überprüfen Sie auf der Seite **Datenträgerzuordnungen überprüfen** den Speicherplatz im Speicherpool, der der Schutzgruppe zugeordnet ist.

   **Gesamtdatengröße** ist die Größe der Daten, die Sie sichern möchten. **Bereitzustellender Speicherplatz für MABS** ist der für die Schutzgruppe von MABS empfohlene Speicherplatz. MABS wählt auf Grundlage der Einstellungen das ideale Sicherungsvolume. Sie können jedoch in **Details zur Datenträgerzuordnung** das gewählte Sicherungsvolume ändern. Wählen Sie für die Workloads im Dropdownmenü den bevorzugten Speicher aus. Durch Ihre Änderungen verändern sich im Bereich **Verfügbarer Speicherplatz** die Werte für **Gesamtspeicher** und **Freier Speicher**. Nicht ausreichend bereitgestellter Speicherplatz ist die Menge an Speicher, die laut Vorschlag von MABS zum Sicherstellen künftiger reibungsloser Sicherungen dem Volume hinzugefügt werden sollte.

8. Geben Sie auf der Seite **Replikaterstellungsmethode auswählen** an, wie die erste Replikation der Daten in der Schutzgruppe ausgeführt werden soll. Wenn Sie sich für die **automatische Replikation über das Netzwerk** entscheiden, sollten Sie hierfür eine Nebenzeit auswählen. Ziehen Sie bei großen Datenmengen oder nicht optimalen Netzwerkbedingungen die Auswahl der Option **Manuell** in Betracht, die Offlinereplikation der Daten mit Wechselmedien erfordert.

9. Legen Sie auf der Seite **Konsistenzprüfungsoptionen auswählen** fest, wie Konsistenzprüfungen automatisiert werden sollen. Sie können eine Überprüfung aktivieren, die nur bei inkonsistenten Replikatdaten oder gemäß einem festgelegten Zeitplan ausgeführt wird. Wenn Sie keine automatischen Konsistenzprüfungen konfigurieren möchten, können Sie jederzeit eine manuelle Überprüfung ausführen, indem Sie mit der rechten Maustaste auf die Schutzgruppe klicken und **Konsistenzprüfung ausführen** auswählen.

    Nachdem Sie die Schutzgruppe erstellt haben, erfolgt die erste Replikation der Daten in Übereinstimmung mit der von Ihnen ausgewählten Methode. Nach der ersten Replikation erfolgt jede Sicherung entsprechend den Einstellungen der Schutzgruppe. Für die Wiederherstellung gesicherter Daten müssen Sie Folgendes beachten:

## <a name="back-up-replica-virtual-machines"></a>Sichern von virtuellen Replikatcomputern

Wenn MABS unter Windows Server 2012 R2 oder höher ausgeführt wird, können Sie virtuelle Replikatcomputer sichern. Dies kann aus verschiedenen Gründen nützlich sein:

**Verringert die Auswirkungen von Sicherungen auf die laufende Workload**: Das Erstellen einer Sicherung eines virtuellen Computers verursacht zusätzlichen Aufwand, wenn eine Momentaufnahme erstellt wird. Durch das Abladen des Sicherungsvorgangs an einen sekundären Remotestandort wird die laufende Workload nicht mehr durch den Sicherungsvorgang beeinträchtigt. Dies gilt nur für Bereitstellungen, bei denen die Sicherungskopie an einem Remotestandort gespeichert ist. Beispielsweise können Sie tägliche Sicherungen erstellen und Daten lokal speichern, um schnelle Wiederherstellungszeiten zu gewährleisten, aber monatliche oder vierteljährliche Sicherungen von virtuellen Replikaten durchführen, die zur langfristigen Aufbewahrung remote gespeichert sind.

**Spart Bandbreite**: In einer typischen Bereitstellung mit Remotezweigstelle/Zentrale benötigen Sie eine angemessene Menge an bereitgestellter Bandbreite, um Sicherungsdaten zwischen Standorten zu übertragen. Wenn Sie zusätzlich zu Ihrer Datensicherungsstrategie eine Replikations- und Failoverstrategie entwickeln, können Sie die Menge der über das Netzwerk gesendeten redundanten Daten reduzieren. Indem Sie die Daten des virtuellen Replikatcomputers anstelle der Daten des primären Computers sichern, sparen Sie den Aufwand für das Senden der gesicherten Daten über das Netzwerk.

**Ermöglicht Hostersicherung**: Sie können ein gehostetes Rechenzentrum als Replikatstandort verwenden und benötigen kein sekundäres Rechenzentrum. In diesem Fall erfordert die SLA des Hosters eine konsistente Sicherung von virtuellen Replikatcomputern.

Eine virtueller Replikatcomputer wird deaktiviert, bis ein Failover eingeleitet wird, und VSS kann keine anwendungskonsistente Sicherung für einen virtuelle Replikatcomputer garantieren. Daher ist die Sicherung einer virtuellen Replikatcomputers nur absturzkonsistent. Wenn die Absturzkonsistenz nicht gewährleistet werden kann, schlägt die Sicherung fehl. Dies kann bei einer Reihe von Bedingungen passieren:

- Der virtuelle Replikatcomputer ist nicht fehlerfrei und befindet sich in einem kritischen Zustand.

- Der virtuelle Replikatcomputer wird neu synchronisiert (im Status „Neusynchronisierung wird ausgeführt“ oder „Neusynchronisierung erforderlich“).

- Die erste Replikation zwischen dem primären und dem sekundären Standort wird ausgeführt oder steht für den virtuellen Computer aus.

- HRL-Protokolle werden auf den virtuellen Replikatcomputer angewendet, oder eine vorherige Aktion zum Anwenden der HRL-Protokolle auf den virtuellen Datenträger ist fehlgeschlagen oder wurde abgebrochen oder unterbrochen.

- Migration oder Failover des virtuellen Replikatcomputers ist im Gang.

## <a name="recover-backed-up-virtual-machines"></a>Wiederherstellen von gesicherten virtuellen Computern

Wenn Sie einen gesicherten virtuellen Computer wiederherstellen können, wählen Sie den virtuellen Computer und den jeweiligen Wiederherstellungspunkt über den Wiederherstellungsassistenten aus. So öffnen Sie den Wiederherstellungs-Assistenten und stellen einen virtuellen Computer wieder her:

1. Geben Sie in der MABS-Administratorkonsole den Namen des virtuellen Computers ein, oder erweitern Sie die Liste der geschützten Elemente, navigieren Sie zu **Alle geschützten HyperV-Daten**, und wählen Sie den virtuellen Computer aus, den Sie wiederherstellen möchten.

2. Wählen Sie im Bereich **Wiederherstellungspunkte für** im Kalender ein beliebiges Datum aus, um die verfügbaren Wiederherstellungspunkte anzuzeigen. Wählen Sie dann im Bereich **Pfad** den Wiederherstellungspunkt aus, den Sie im Wiederherstellungs-Assistenten verwenden möchten.

3. Wählen Sie im Menü **Aktionen** die Option **Wiederherstellen** aus, um den Wiederherstellungs-Assistenten zu öffnen.

    Die ausgewählte VM und der ausgewählte Wiederherstellungspunkt werden auf dem Bildschirm **Wiederherstellungsauswahl überprüfen** angezeigt. Wählen Sie **Weiter** aus.

4. Wählen Sie auf dem Bildschirm **Wiederherstellungstyp** aus, wo Sie die Daten wiederherstellen möchten, und wählen Sie dann **Weiter** aus.

    - **In ursprünglicher Instanz wiederherstellen:** Wenn die Wiederherstellung in die ursprüngliche Instanz erfolgt, werden die ursprüngliche VHD und alle zugehören Prüfpunkte gelöscht. MABS stellt die VHD und andere Konfigurationsdateien mithilfe von Hyper-V-VSS Writer am ursprünglichen Speicherort wieder her. Am Ende des Wiederherstellungsprozesses sind virtuelle Computer weiterhin hoch verfügbar.
        Die Ressourcengruppe muss für der Wiederherstellung vorhanden sein. Wenn Sie nicht verfügbar ist, führen Sie die Wiederherstellung an einem anderen Speicherort durch, und machen Sie den virtuellen Computer anschließend hoch verfügbar.

    - **Wiederherstellen als VM auf einem beliebigen Host:** MABS unterstützt die Wiederherstellung an einem alternativen Speicherort (Alternate Location Recovery, ALR), die eine nahtlose Wiederherstellung einer geschützten Azure Stack HCI-VM auf einem anderen Host innerhalb desselben Clusters ermöglicht, und zwar unabhängig von der Prozessorarchitektur. Azure Stack HCI-VMs, die auf einem Clusterknoten wiederhergestellt werden, sind nicht hoch verfügbar. Wenn Sie diese Option auswählen, zeigt der Wiederherstellungs-Assistent einen zusätzlichen Bildschirm zum Angeben des Ziel und des Zielpfads an.
    
        >[!NOTE]
        >Wenn Sie den ursprünglichen Host auswählen, entspricht das Verhalten dem beim **Wiederherstellen in der ursprünglichen Instanz**. Die ursprüngliche VHD und alle zugehörigen Prüfpunkte werden gelöscht.

    - **In einen Netzwerkordner kopieren**: MABS unterstützt die Wiederherstellung auf Elementebene (Item-level Recovery, ILR), mit der Sie Dateien, Ordner, Volumes und virtuelle Festplatten (VHDs) auf Elementebene von einer Sicherung auf Hostebene von Azure Stack HCI-VMs in einer Netzwerkfreigabe oder ein Volume auf einem mit MABS geschützten Server wiederherstellen können. Der MABS-Schutz-Agent muss für eine Wiederherstellung auf Elementebene nicht auf dem Gastbetriebssystem installiert sein. Wenn Sie diese Option auswählen, zeigt der Wiederherstellungs-Assistent einen zusätzlichen Bildschirm zum Angeben des Ziel und des Zielpfads an.

5. Konfigurieren Sie unter **Wiederherstellungsoptionen angeben** die Wiederherstellungsoptionen, und wählen Sie **Weiter** aus:

    - Wählen Sie für **Netzwerk-Bandbreiteneinschränkung** die Option **Ändern** aus, wenn Sie eine VM über eine geringe Bandbreite wiederherstellen. Nachdem Sie die Einschränkungsoption aktiviert haben, können Sie die Menge an Bandbreite, die Sie zur Verfügung stellen möchten, und den Zeitpunkt angeben, zu dem diese Bandbreite verfügbar ist.
    - Wählen Sie **SAN-basierte Wiederherstellung mithilfe von Hardwaremomentaufnahmen aktivieren** aus, wenn Sie Ihr Netzwerk konfiguriert haben.
    - Wählen Sie **Eine E-Mail senden, wenn die Wiederherstellung abgeschlossen ist**, und geben Sie dann die E-Mail-Adressen an, wenn nach Abschluss des Wiederherstellungsprozesses E-Mail-Benachrichtigungen gesendet werden sollen.

6. Vergewissern Sie sich auf dem Bildschirm „Zusammenfassung“, dass alle Details richtig sind. Wenn die Details nicht richtig sind oder Sie eine Änderung vornehmen möchten, wählen Sie **Zurück** aus. Wenn Sie mit den Einstellungen zufrieden sind, wählen Sie **Wiederherstellen** aus, um den Wiederherstellungsprozess zu starten.

7. Der Bildschirm **Wiederherstellungsstatus** enthält Informationen zum Wiederherstellungsauftrag.

## <a name="next-steps"></a>Nächste Schritte

[Wiederherstellen von Daten von Azure Backup Server](./backup-azure-alternate-dpm-server.md)
