---
title: Sichern von virtuellen Hyper-V-Computern mit MABS
description: Dieser Artikel enthält die Verfahren zum Sichern und Wiederherstellen von virtuellen Computern mit Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 07/09/2021
ms.openlocfilehash: e10bab2af14cdf540b5c5ec9c670e6ccb2c8807a
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/09/2021
ms.locfileid: "113585406"
---
# <a name="back-up-hyper-v-virtual-machines-with-azure-backup-server"></a>Sichern von virtuellen Hyper-V-Computern mit Azure Backup Server

In diesem Artikel wird das Sichern von virtuellen Hyper-V-Computern mit Microsoft Azure Backup Server (MABS) beschrieben.

## <a name="supported-scenarios"></a>Unterstützte Szenarios

MABS kann virtuelle Computer, die auf Hyper-V-Hostservern ausgeführt werden, in den folgenden Szenarien sichern:

- **Virtuelle Computer mit lokalem oder direktem Speicher**: Sichern Sie virtuelle Computer, die auf eigenständigen Hyper-V-Hostservern gehostet werden und über lokale oder direkt angefügte Speicher verfügen. Beispiel: eine Festplatte, ein SAN- (Storage Area Network) oder ein NAS-Gerät (Network Attached Storage). Der MABS-Schutz-Agent muss auf allen Hosts installiert sein.

- **Virtuelle Computer in einem Cluster mit CSV-Speicher**: Sichern Sie virtuelle Computer, die auf einem Hyper-V-Cluster mit freigegebenem Clustervolumespeicher (CSV) gehostet werden. Der MABS-Schutz-Agent wird auf jedem Clusterknoten installiert.

## <a name="host-versus-guest-backup"></a>Sicherung auf Host- und Gastebene im Vergleich

MABS kann virtuelle Hyper-V-Computer auf Host- oder Gastebene sichern. Auf Hostebene ist der MABS-Schutz-Agent auf dem Hyper-V-Hostserver oder -Cluster installiert und schützt die gesamten VMs und Datendateien, die auf diesem Host ausgeführt werden.   Auf Gastebene wird der Agent auf allen virtuellen Computern installiert und schützt somit die auf der jeweiligen VM vorhandene Workload.

Beide Methoden haben Vor- und Nachteile:

- Sicherungen auf Hostebene sind flexibel, da Sie unabhängig von der Art des Betriebssystems auf den Gastcomputern arbeiten, und es ist nicht erforderlich, dass der MABS-Schutz-Agent auf jeder VM installiert ist. Wenn Sie Sicherungen auf Hostebene bereitstellen, stellen Sie einen kompletten virtuellen Computer oder Dateien und Ordner wieder her (Wiederherstellung auf Elementebene).

- Die Sicherung auf Gastebene ist für den Schutz bestimmter Workloads auf einem virtuellen Computer von Vorteil. Auf Hostebene können Sie eine gesamte VM oder bestimmte Dateien wiederherstellen, jedoch keine Daten im Kontext einer bestimmten Anwendung. Um beispielsweise bestimmte SharePoint-Elemente von einer gesicherten VM wiederherzustellen, sollten Sie eine Sicherung auf Gastebene dieser VM durchführen. Wenn Sie auf Pass-Through-Datenträgern gespeicherte Daten schützen möchten, müssen Sie die Sicherung auf Gastebene wählen. Die Pass-Through-Technologie ermöglicht dem virtuellen Computer den Direktzugriff auf das Speichergerät und speichert keine virtuellen Volumedaten in einer VHD-Datei.

## <a name="how-the-backup-process-works"></a>Funktionsweise des Sicherungsvorgangs

MABS führt die Sicherung mit VSS wie folgt aus. Die Schritte in dieser Beschreibung sind zur besseren Übersichtlichkeit nummeriert.

1. Die blockbasierte Synchronisierungs-Engine von MABS erstellt eine erste Kopie des geschützten virtuellen Computers und stellt sicher, dass diese Kopie vollständig und konsistent ist.

2. Nachdem die erste Kopie erstellt und verifiziert wurde, verwendet MABS den Hyper-V-VSS-Writer zum Erfassen von Sicherungen. Der VSS Writer bietet einen datenkonsistenten Satz von Datenträgerblöcken, die mit dem MABS-Server synchronisiert sind. Dieser Ansatz bietet den Vorteil einer „vollständigen Sicherung“ mit dem MABS-Server und minimiert gleichzeitig den Umfang der Sicherungsdaten, die über das Netzwerk übertragen werden müssen.

3. Der MABS-Schutz-Agent auf einem Server, auf dem Hyper-V ausgeführt wird, verwendet die vorhandenen Hyper-V-APIs, um festzustellen, ob ein geschützter virtueller Computer ebenfalls VSS unterstützt.

   - Wenn ein virtueller Computer die Anforderungen für eine Onlinesicherung erfüllt und die Hyper-V-Integrationsdienste installiert sind, leitet der Hyper-V-VSS Writer die VSS-Anforderungen rekursiv an alle VSS-fähigen Prozesse auf dem virtuellen Computer weiter. Dieser Vorgang wird ausgeführt, ohne dass der MABS-Schutz-Agent auf dem virtuellen Computer installiert ist. Mit der rekursiven VSS-Anforderung kann der Hyper-V-VSS Writer sicherstellen, dass die Schreibvorgänge auf dem Datenträger synchronisiert werden, sodass eine VSS-Momentaufnahme ohne Datenverlust erfasst wird.

     Die Hyper-V-Integrationsdienste rufen den Hyper-V-VSS Writer im Volumeschattenkopie-Dienste (VSS) auf virtuellen Computern auf, um sicherzustellen, dass sich ihre Anwendungsdaten in einem konsistenten Zustand befinden.

   - Wenn der virtuelle Computer nicht den Anforderungen für Onlinesicherungen entspricht, verwendet MABS automatisch die Hyper-V-APIs, um den virtuellen Computer anzuhalten, bevor Datendateien erfasst werden.

4. Nachdem die erste Baselinekopie des virtuellen Computers mit dem MABS-Server synchronisiert wurde, werden alle Änderungen, die an den Ressourcen des virtuellen Computers vorgenommen werden, in einem neuen Wiederherstellungspunkt erfasst. Der Wiederherstellungspunkt stellt den konsistenten Zustand des virtuellen Computers zu einem bestimmten Zeitpunkt dar. Wiederherstellungspunkte können mindestens einmal täglich erfasst werden. Wenn ein neuer Wiederherstellungspunkt erstellt wird, verwendet MABS die Replikation auf Blockebene in Verbindung mit dem Hyper-V-VSS-Writer, um festzustellen, welche Blöcke auf dem Server, auf dem Hyper-V ausgeführt wird, nach der Erstellung des letzten Wiederherstellungspunkts geändert wurden. Diese Datenblöcke werden dann an den MABS-Server übertragen und auf das Replikat der geschützten Daten angewendet.

5. Der MABS-Server verwendet VSS auf den Volumes, die Wiederherstellungsdaten hosten, sodass mehrere Schattenkopien verfügbar sind. Jede dieser Schattenkopien ermöglicht eine separate Wiederherstellung. VSS-Wiederherstellungspunkte werden auf dem MABS-Server gespeichert. Die temporäre Kopie, die auf dem Server mit Hyper-V erstellt wird, wird nur für die Dauer der MABS-Synchronisierung gespeichert.

>[!NOTE]
>
>Ab Windows Server 2016 verfügen virtuelle Hyper-V-Festplatten über eine integrierte Änderungsnachverfolgung, die als robuste Änderungsnachverfolgung (Resilient Change Tracking, RCT) bezeichnet wird. MABS verwendet RCT, die systemeigene Änderungsnachverfolgung in Hyper-V, wodurch zeitaufwändige Konsistenzprüfungen etwa bei VM-Abstürzen entfallen. RCT bietet eine bessere Resilienz als die Änderungsnachverfolgung, die von Sicherungen bereitgestellt wird, die auf VSS-Momentaufnahmen basieren. MABS V3 optimiert den Netzwerk- und Speicherverbrauch außerdem, indem bei Konsistenzprüfungen nur die geänderten Daten übertragen werden.

## <a name="backup-prerequisites"></a>Voraussetzungen für eine Sicherung

Dies sind die Voraussetzungen für die Sicherung virtueller Hyper-V-Computer mit MABS:

|Voraussetzung|Details|
|------------|-------|
|MABS-Voraussetzungen|– Wenn Sie die Wiederherstellung auf Elementebene für virtuelle Computer durchführen möchten (Dateien, Ordner, Volumes wiederherstellen), muss die Hyper-V-Rolle auf dem MABS-Server aktiviert sein (die Hyper-V-Rolle wird bei der Installation von MABS standardmäßig installiert). Wenn Sie eine Wiederherstellung auf Elementebene für virtuelle Computer (Dateien, Ordner, Volumes) durchführen möchten, müssen Sie die Hyper-V-Rolle auf dem MABS-Server installieren.<br />- Sie können bis zu 800 virtuelle Computer mit je 100 GB auf einem MABS-Server schützen und mehrere MABS-Server zulassen, die größere Cluster unterstützen.<br />- MABS schließt die Auslagerungsdatei von inkrementellen Sicherungen aus, um die Sicherungsleistung des virtuellen Computers zu verbessern.<br />- MABS kann einen Hyper-V-Server oder -Cluster in der gleichen Domäne sichern, in der sich auch der MABS-Server befindet, oder in einer Unter- oder vertrauenswürdigen Domäne. Wenn Sie Hyper-V in einer Arbeitsgruppe oder einer nicht vertrauenswürdigen Domäne sichern möchten, müssen Sie die Authentifizierung einrichten. Für einen einzelnen Hyper-V-Server können Sie NTLM oder die Zertifikatauthentifizierung verwenden. Bei einem Cluster können Sie nur die Zertifikatauthentifizierung verwenden.<br />- Die Verwendung von Sicherungen auf Hostebene zur Sicherung von Daten virtueller Computer auf Pass-Through-Datenträgern wird nicht unterstützt. In diesem Szenario empfehlen wir Ihnen, zum Sichern von VHD-Dateien eine Sicherung auf Hostebene und zum Sichern der anderen Daten, die auf dem Host nicht sichtbar sind, eine Sicherung auf Gastebene durchzuführen.<br />   \- Sie können virtuelle Computer sichern, die auf deduplizierten Volumes gespeichert sind.|
|Voraussetzungen für Hyper-V-VMs|– Die Version der Integrationskomponenten, die auf dem virtuellen Computer ausgeführt wird, muss mit der Version des Hyper-V-Hosts identisch sein. <br />- Für jede Sicherung der virtuellen Computer benötigen Sie freien Speicherplatz auf dem Volume, auf dem sich die Dateien der virtuellen Festplatten befinden, damit Hyper-V während der Sicherung ausreichend Platz für differenzierende Datenträger (AVHD) hat. Der Speicherplatz muss mindestens der berechneten Fensterzeit **Anfängliche Datenträgergröße\*Änderungsrate\*Sicherung** entsprechen. Wenn Sie mehrere Sicherungen auf einem Cluster ausführen, benötigen Sie genügend Speicherkapazität für die AVHDs für jeden virtuellen Computer, der diese Berechnung verwendet.<br />- Um virtuelle Computer zu sichern, die auf Hyper-V-Hostservern mit Windows Server 2012 R2 ausgeführt werden, sollte für den virtuellen Computer ein SCSI-Controller angegeben sein, auch wenn dieser nicht mit einer Komponente verbunden ist. (Bei der Sicherung von Windows Server 2012 R2 bindet der Hyper-V-Host eine neue VHD auf dem virtuellen Computer ein und hebt die Einbindung später wieder auf. Nur der SCSI-Controller kann dies unterstützen. Daher ist er für die Onlinesicherung des virtuellen Computers erforderlich.  Ohne diese Einstellung wird die Ereignis-ID 10103 ausgegeben, wenn Sie versuchen, den virtuellen Computer zu sichern.)|
|Linux-Voraussetzungen|– Sie können virtuelle Linux-Computer mithilfe von MABS sichern. Es werden nur dateikonsistente Momentaufnahmen unterstützt.|
|Sichern von VMs mit CSV-Speicher|-Installieren Sie bei einem CSV-Speicher den Hardwareanbieter für  den Volumeschattenkopie-Dienst (VSS) auf dem Hyper-V-Server. Wenden Sie sich für Informationen zum VSS-Hardwareanbieter an Ihren Storage Area Network-Anbieter.<br />- Wenn ein einzelner Knoten in einem CSV-Cluster unerwartet heruntergefahren wird, führt MABS eine Konsistenzprüfung für die virtuellen Computer durch, die auf diesem Knoten ausgeführt wurden.<br />Wenn Sie einen Hyper-V-Server mit aktivierter BitLocker-Laufwerkverschlüsselung auf dem CSV-Cluster neu starten müssen, müssen Sie eine Konsistenzprüfung für die virtuelle Hyper-V-Computer durchführen.|
|Sichern von VMs mit SMB-Speicher|– Aktivieren Sie auf dem Server, auf dem Hyper-V ausgeführt wird, die automatische Einbindung, um den Schutz für virtuelle Computer zu aktivieren.<br />   -Deaktivieren Sie die TCP Chimney Offload.<br />- Stellen Sie sicher, dass alle Hyper-V machine$-Konten über volle Berechtigungen für die spezifischen SMB-Remotedateifreigaben verfügen.<br />- Stellen Sie sicher, dass die Länge des Dateipfads für alle Komponenten des virtuellen Computers während der Wiederherstellung zum alternativen Speicherort weniger als 260 Zeichen beträgt. Andernfalls ist die Wiederherstellung möglicherweise erfolgreich, aber Hyper-V kann den virtuellen Computer nicht einbinden.<br />- Folgende Szenarios werden nicht unterstützt:<br />     Bereitstellungen, bei denen sich einige Komponenten des virtuellen Computers auf lokalen Volumes und einige Komponenten auf Remotevolumes befinden; eine IPv4- oder IPv6-Adresse für den Speicherort des Dateiservers und die Wiederherstellung eines virtuellen Computers auf einem Computer, der SMB-Remotefreigaben verwendet.<br />Sie müssen den Dateiserver-VSS-Agent-Dienst auf jedem SMB-Server aktivieren. Fügen Sie ihn in **Rollen und Funktionen hinzufügen** > **Serverrollen auswählen** > **Datei- und Speicherdienste** > **Dateidienste** > **Dateidienst** > **Dateiserver-VSS-Agenten-Dienst** hinzu.|

## <a name="back-up-virtual-machines"></a>Sichern virtueller Computer

1. Richten Sie Ihren [MABS-Server](backup-azure-microsoft-azure-backup.md) und [Ihren Speicher](backup-mabs-add-storage.md) ein. Verwenden Sie bei der Einrichtung Ihres Speichers diese Richtlinien zur Speicherkapazität.
   - Durchschnittliche VM-Größe: 100 GB
   - Anzahl virtueller Computer pro MABS-Server: 800
   - Gesamtgröße von 800 VMs: 80 TB
   - Erforderlicher Speicherplatz für den Sicherungsspeicher: 80 TB

2. Richten Sie den MABS-Schutz-Agent auf dem Hyper-V-Server oder den Hyper-V-Clusterknoten ein.

3. Wählen Sie in der MABS-Administratorkonsole **Schutz** > **Schutzgruppe erstellen** aus, um den Assistenten **Neue Schutzgruppe erstellen** zu öffnen.

4. Wählen Sie auf der Seite **Gruppenmitglieder auswählen** die VMs aus, die Sie vor den Hyper-V-Hostservern schützen möchten, auf denen sie sich befinden. Wir empfehlen Ihnen, alle VMs, die die gleiche Schutzrichtlinie verwenden, in einer Schutzgruppe zusammenzufassen. Um den Speicherplatz effizient zu nutzen, aktivieren Sie die Zusammenstellung. Mit der Zusammenstellung können Sie Daten aus verschiedenen Schutzgruppen auf demselben Datenträger oder Bandspeicher suchen, sodass mehrere Datenquellen ein einzelnes Replikat- und Wiederherstellungspunkt-Volume verwenden.

5. Geben Sie auf der Seite **Methode für die Datensicherheit auswählen** einen Namen für die Schutzgruppe ein. Wählen Sie **Ich möchte einen kurzfristigen Schutz mit Datenträger** und dann **Ich möchte Onlineschutz** aus, wenn Sie Daten mit dem Azure Backup-Dienst in Azure sichern möchten.

6. Geben Sie unter **Kurzfristige Ziele angeben** > **Aufbewahrungsdauer** an, wie lange Sie Datenträgerdaten aufbewahren möchten. Geben Sie unter **Synchronisierungsfrequenz** an, wie oft inkrementelle Sicherungen der Daten durchgeführt werden sollen. Alternativ können Sie anstelle der Auswahl eines Intervalls für inkrementelle Sicherungen auch **Direkt vor einen Wiederherstellungspunkt** aktivieren. Mit dieser Einstellung führt MABS eine vollständige Schnellsicherung unmittelbar vor jedem geplanten Wiederherstellungspunkt aus.

    > [!NOTE]
    >
    >Wenn Sie Anwendungsworkloads schützen, werden Wiederherstellungspunkte gemäß der Synchronisierungsfrequenz erstellt, sofern die Anwendung inkrementelle Sicherungen unterstützt. Wenn dies nicht der Fall ist, führt MABS anstelle einer inkrementellen Sicherung eine vollständige Schnellsicherung durch und erstellt Wiederherstellungspunkte gemäß dem Zeitplan für die Schnellsicherung.<br></br>Die Prüfpunkte, die virtuellen Computer zugeordnet sind, werden nicht gesichert.

7. Überprüfen Sie auf der Seite **Datenträgerzuordnungen überprüfen** den Speicherplatz im Speicherpool, der der Schutzgruppe zugeordnet ist.

   **Gesamtdatengröße** ist die Größe der Daten, die Sie sichern möchten. **Bereitzustellender Speicherplatz für MABS** ist der für die Schutzgruppe von MABS empfohlene Speicherplatz. MABS wählt auf Grundlage der Einstellungen das ideale Sicherungsvolume. Sie können jedoch in **Details zur Datenträgerzuordnung** das gewählte Sicherungsvolume ändern. Wählen Sie für die Workloads im Dropdownmenü den bevorzugten Speicher aus. Durch Ihre Änderungen verändern sich im Bereich **Verfügbarer Speicherplatz** die Werte für **Gesamtspeicher** und **Freier Speicher**. Nicht ausreichend bereitgestellter Speicherplatz ist die Menge an Speicher, die laut Vorschlag von MABS zum Sicherstellen künftiger reibungsloser Sicherungen dem Volume hinzugefügt werden sollte.

8. Geben Sie auf der Seite **Replikaterstellungsmethode auswählen** an, wie die erste Replikation der Daten in der Schutzgruppe ausgeführt werden soll. Wenn Sie sich für die **automatische Replikation über das Netzwerk** entscheiden, sollten Sie hierfür eine Nebenzeit auswählen. Ziehen Sie bei großen Datenmengen oder nicht optimalen Netzwerkbedingungen die Auswahl der Option **Manuell** in Betracht, die Offlinereplikation der Daten mit Wechselmedien erfordert.

9. Legen Sie auf der Seite **Konsistenzprüfungsoptionen auswählen** fest, wie Konsistenzprüfungen automatisiert werden sollen. Sie können eine Überprüfung aktivieren, die nur bei inkonsistenten Replikatdaten oder gemäß einem festgelegten Zeitplan ausgeführt wird. Wenn Sie keine automatischen Konsistenzprüfungen konfigurieren möchten, können Sie jederzeit eine manuelle Überprüfung ausführen, indem Sie mit der rechten Maustaste auf die Schutzgruppe klicken und **Konsistenzprüfung ausführen** auswählen.

    Nachdem Sie die Schutzgruppe erstellt haben, erfolgt die erste Replikation der Daten in Übereinstimmung mit der von Ihnen ausgewählten Methode. Nach der ersten Replikation erfolgt jede Sicherung entsprechend den Einstellungen der Schutzgruppe. Für die Wiederherstellung gesicherter Daten müssen Sie Folgendes beachten:

## <a name="back-up-replica-virtual-machines"></a>Sichern von virtuellen Replikatcomputern

Wenn MABS unter Windows Server 2012 R2 oder höher ausgeführt wird, können Sie virtuelle Replikatcomputer sichern. Dies kann aus verschiedenen Gründen nützlich sein:

**Verringert die Auswirkungen von Sicherungen auf die laufende Workload**: Das Erstellen einer Sicherung eines virtuellen Computers verursacht zusätzlichen Aufwand, wenn eine Momentaufnahme erstellt wird. Durch das Abladen des Sicherungsvorgangs an einen sekundären Remotestandort wird die laufende Workload nicht mehr durch den Sicherungsvorgang beeinträchtigt. Dies gilt nur für Bereitstellungen, bei denen die Sicherungskopie an einem Remotestandort gespeichert ist. Beispielsweise können Sie tägliche Sicherungen erstellen und Daten lokal speichern, um schnelle Wiederherstellungszeiten zu gewährleisten, aber monatliche oder vierteljährliche Sicherungen von virtuellen Replikaten durchführen, die zur langfristigen Aufbewahrung remote gespeichert sind.

**Spart Bandbreite**: In einer typischen Bereitstellung mit Remotezweigstelle/Zentrale benötigen Sie eine angemessene Menge an bereitgestellter Bandbreite, um Sicherungsdaten zwischen Standorten zu übertragen. Wenn Sie zusätzlich zu Ihrer Datensicherungsstrategie eine Replikations- und Failoverstrategie entwickeln, können Sie die Menge der über das Netzwerk gesendeten redundanten Daten reduzieren. Indem Sie die Daten des virtuellen Replikatcomputers anstelle der Daten des primären Computers sichern, sparen Sie den Aufwand für das Senden der gesicherten Daten über das Netzwerk.

**Ermöglicht Hostersicherung**: Sie können ein gehostetes Rechenzentrum als Replikatstandort verwenden und benötigen kein sekundäres Rechenzentrum. In diesem Fall erfordert die SLA des Hosters eine konsistente Sicherung von virtuellen Replikatcomputern.

Eine virtueller Replikatcomputer wird deaktiviert, bis ein Failover eingeleitet wird, und VSS kann keine anwendungskonsistente Sicherung für einen virtuelle Replikatcomputer garantieren. Daher ist die Sicherung einer virtuellen Replikatcomputers nur absturzkonsistent. Wenn die Absturzkonsistenz nicht gewährleistet werden kann, schlägt die Sicherung fehl. Dies kann bei einer Reihe von Bedingungen passieren:

- Der virtuelle Replikatcomputer ist nicht fehlerfrei und befindet sich in einem kritischen Zustand.

- Der virtuelle Replikatcomputer wird neu synchronisiert (im Status „Neusynchronisierung wird ausgeführt“ oder „Neusynchronisierung erforderlich“).

- Die erste Replikation zwischen dem primären und dem sekundären Standort wird ausgeführt oder steht für den virtuellen Computer aus.

- HRL-Protokolle werden auf den virtuellen Replikatcomputer angewendet, oder eine vorherige Aktion zum Anwenden der HRL-Protokolle auf den virtuellen Datenträger ist fehlgeschlagen oder wurde abgebrochen oder unterbrochen.

- Migration oder Failover des virtuellen Replikatcomputers wird ausgeführt.

## <a name="recover-backed-up-virtual-machines"></a>Wiederherstellen von gesicherten virtuellen Computern

Wenn Sie einen gesicherten virtuellen Computer wiederherstellen können, wählen Sie den virtuellen Computer und den jeweiligen Wiederherstellungspunkt über den Wiederherstellungsassistenten aus. So öffnen Sie den Wiederherstellungs-Assistenten und stellen einen virtuellen Computer wieder her:

1. Geben Sie in der MABS-Administratorkonsole den Namen des virtuellen Computers ein, oder erweitern Sie die Liste der geschützten Elemente, navigieren Sie zu **Alle geschützten HyperV-Daten**, und wählen Sie den virtuellen Computer aus, den Sie wiederherstellen möchten.

2. Wählen Sie im Bereich **Wiederherstellungspunkte für** im Kalender ein beliebiges Datum aus, um die verfügbaren Wiederherstellungspunkte anzuzeigen. Wählen Sie dann im Bereich **Pfad** den Wiederherstellungspunkt aus, den Sie im Wiederherstellungs-Assistenten verwenden möchten.

3. Wählen Sie im Menü **Aktionen** die Option **Wiederherstellen** aus, um den Wiederherstellungs-Assistenten zu öffnen.

    Die ausgewählte VM und der ausgewählte Wiederherstellungspunkt werden auf dem Bildschirm **Wiederherstellungsauswahl überprüfen** angezeigt. Wählen Sie **Weiter** aus.

4. Wählen Sie auf dem Bildschirm **Wiederherstellungstyp** aus, wo Sie die Daten wieder wiederherstellen möchten, und wählen Sie dann **Weiter** aus.

    - **In ursprünglicher Instanz wiederherstellen:** Wenn die Wiederherstellung in die ursprüngliche Instanz erfolgt, werden die ursprüngliche VHD und alle zugehören Prüfpunkte gelöscht. MABS stellt die VHD und andere Konfigurationsdateien mithilfe von Hyper-V-VSS Writer am ursprünglichen Speicherort wieder her. Am Ende des Wiederherstellungsprozesses sind virtuelle Computer weiterhin hoch verfügbar.
        Die Ressourcengruppe muss für der Wiederherstellung vorhanden sein. Wenn Sie nicht verfügbar ist, führen Sie die Wiederherstellung an einem anderen Speicherort durch, und machen Sie den virtuellen Computer anschließend hoch verfügbar.

    - **Als virtuellen Computer für beliebigen Host wiederherstellen**: MABS unterstützt die Wiederherstellung an einem alternativen Speicherort (Alternate Location Recovery, ALR), mit der ein geschützter virtueller Hyper-V-Computer unabhängig von der Prozessorarchitektur nahtlos auf einem anderen Hyper-V-Host wiederhergestellt wird. Virtuelle Hyper-V-Computer, die in einem Clusterknoten wiederhergestellt werden, sind nicht hoch verfügbar. Wenn Sie diese Option auswählen, zeigt der Wiederherstellungs-Assistent einen zusätzlichen Bildschirm zum Angeben des Ziel und des Zielpfads an.
    
        >[!NOTE]
        >Wenn Sie den ursprünglichen Host auswählen, entspricht das Verhalten dem beim **Wiederherstellen in der ursprünglichen Instanz**. Die ursprüngliche VHD und alle zugehörigen Prüfpunkte werden gelöscht.

    - **In einen Netzwerkordner kopieren**: MABS unterstützt die Wiederherstellung auf Elementebene (ILR), mit der Sie Dateien, Ordner, Volumes und virtuelle Festplatten (VHDs) auf Elementebene von einer Sicherung auf Hostebene von virtuellen Hyper-V-Computern auf eine Netzwerkfreigabe oder ein Volume auf einem mit MABS geschützten Server wiederherstellen können. Der MABS-Schutz-Agent muss für eine Wiederherstellung auf Elementebene nicht auf dem Gastbetriebssystem installiert sein. Wenn Sie diese Option auswählen, zeigt der Wiederherstellungs-Assistent einen zusätzlichen Bildschirm zum Angeben des Ziel und des Zielpfads an.

5. Konfigurieren Sie unter **Wiederherstellungsoptionen angeben** die Wiederherstellungsoptionen, und wählen Sie **Weiter** aus:

    - Wählen Sie für **Netzwerk-Bandbreiteneinschränkung** die Option **Ändern** aus, wenn Sie eine VM über eine geringe Bandbreite wiederherstellen. Nachdem Sie die Einschränkungsoption aktiviert haben, können Sie die Menge an Bandbreite, die Sie zur Verfügung stellen möchten, und den Zeitpunkt angeben, zu dem diese Bandbreite verfügbar ist.
    - Wählen Sie **SAN-basierte Wiederherstellung mithilfe von Hardwaremomentaufnahmen aktivieren** aus, wenn Sie Ihr Netzwerk konfiguriert haben.
    - Wählen Sie **Eine E-Mail senden, wenn die Wiederherstellung abgeschlossen ist**, und geben Sie dann die E-Mail-Adressen an, wenn nach Abschluss des Wiederherstellungsprozesses E-Mail-Benachrichtigungen gesendet werden sollen.

6. Vergewissern Sie sich auf dem Bildschirm „Zusammenfassung“, dass alle Details richtig sind. Wenn die Details nicht richtig sind oder Sie eine Änderung vornehmen möchten, wählen Sie **Zurück** aus. Wenn Sie mit den Einstellungen zufrieden sind, wählen Sie **Wiederherstellen** aus, um den Wiederherstellungsprozess zu starten.

7. Der Bildschirm **Wiederherstellungsstatus** enthält Informationen zum Wiederherstellungsauftrag.

## <a name="restore-an-individual-file-from-a-hyper-v-vm"></a>Wiederherstellen einer einzelnen Datei auf einer Hyper-V-VM 

Sie können einzelne Dateien über einen Wiederherstellungspunkt einer geschützten Hyper-V-VM wiederherstellen. Dieses Feature ist nur für Windows Server-VMs verfügbar. Die Wiederherstellung einzelner Dateien ähnelt der Wiederherstellung einer vollständigen VM. Allerdings müssen Sie zunächst zur VMDK navigieren und die gewünschten Dateien suchen, bevor Sie den Wiederherstellungsprozess starten. So stellen Sie eine einzelne Datei wieder her oder wählen Dateien von einer Windows Server-VM aus: 

>[!Note]
>Die Wiederherstellung einzelner Dateien auf einer Hyper-V-VM ist nur für Wiederherstellungspunkte von Windows-VMs und Datenträgern verfügbar. 

1. Wählen Sie in der MABS-Administratorkonsole die Ansicht **Wiederherstellung** aus. 

1. Suchen oder filtern Sie im Bereich **Durchsuchen**, um die wiederherzustellende VM zu ermitteln. Nachdem Sie eine Hyper-V-VM oder einen Ordner ausgewählt haben, zeigt der Bereich **Wiederherstellungspunkte für** die verfügbaren Wiederherstellungspunkte. 

    ![Bereich „Wiederherstellungspunkte für“ zum Wiederherstellen von Dateien auf einer Hyper-V-VM](./media/back-up-hyper-v-virtual-machines-mabs/hyper-v-vm-rp-disk.png)

1. Verwenden Sie im Bereich **Wiederherstellungspunkte für** den Kalender, um das Datum auszuwählen, das die gewünschten Wiederherstellungspunkte enthält. Je nach Konfiguration der Sicherungsrichtlinie können Daten ggf. über mehr als einen Wiederherstellungspunkt verfügen. Nachdem Sie den Tag ausgewählt haben, an dem der Wiederherstellungspunkt erstellt wurde, sollten Sie sicherstellen, dass Sie die richtige **Wiederherstellungszeit** angegeben haben. Falls das ausgewählte Datum über mehrere Wiederherstellungspunkte verfügt, sollten Sie Ihren Wiederherstellungspunkt über das Dropdownmenü „Wiederherstellungszeit“ auswählen. Nachdem Sie den Wiederherstellungspunkt ausgewählt haben, wird die Liste der wiederherstellbaren Elemente im Bereich „Pfad“ angezeigt. 

1. Doppelklicken Sie im Bereich **Pfad** auf das Element in der Spalte „Wiederherstellbares Element“, um es zu öffnen und die Dateien zu finden, die wiederhergestellt werden sollen. Wählen Sie die Dateien oder Ordner aus, die Sie wiederherstellen möchten. Um mehrere Elemente auszuwählen, drücken Sie die **STRG**-Taste, während Sie die einzelnen Elemente auswählen. Nutzen Sie den Bereich **Pfad**, um nach der Liste der Dateien oder Ordner zu suchen, die in der Spalte **Wiederherstellbares Element** angezeigt werden. Bei Verwendung der Option **Liste unten durchsuchen** wird nicht in Unterordnern gesucht. Doppelklicken Sie auf den Ordner, um die Unterordner zu durchsuchen. Verwenden Sie die Schaltfläche „Nach oben“, um von einem untergeordneten Ordner in den übergeordneten Ordner zu wechseln. Sie können mehrere Elemente (Dateien und Ordner) auswählen, aber diese müssen in demselben übergeordneten Ordner enthalten sein. Es ist nicht möglich, in einem einzigen Wiederherstellungsauftrag Elemente aus mehreren Ordnern wiederherzustellen. 

    ![Überprüfen der Wiederherstellungsauswahl für die Hyper-V-VM](./media/back-up-hyper-v-virtual-machines-mabs/hyper-v-vm-rp-disk-ilr-2.png) 

1. Wählen Sie nach dem Auswählen der wiederherzustellenden Elemente im Menüband der Administratorkonsole **Wiederherstellen** aus, um den **Wiederherstellungs-Assistenten** zu öffnen. Im Wiederherstellungs-Assistenten werden auf dem Bildschirm **Wiederherstellungsauswahl überprüfen** die zur Wiederherstellung ausgewählten Elemente angezeigt. 

1. Wählen Sie auf dem Bildschirm **Wiederherstellungsoptionen angeben** die Option **Ändern** aus, wenn Sie die Drosselung der Netzwerkbandbreite aktivieren möchten. Wenn die Netzwerk-Bandbreiteneinschränkung deaktiviert bleiben soll, wählen Sie **Weiter** aus. Keine anderen Optionen auf dieser Seite des Assistenten sind für VMware-VMs verfügbar. Wenn Sie die Netzwerk-Bandbreiteneinschränkung ändern möchten, klicken Sie im Dialogfeld „Drosselung“ auf **Netzwerk-Bandbreiteneinschränkung aktivieren**, um die Option zu aktivieren. Konfigurieren Sie nach der Aktivierung die Optionen **Einstellungen** und **Arbeitszeitplan**. 

1. Wählen Sie auf dem Bildschirm **Wiederherstellungstyp auswählen** die Option **Weiter** aus. Sie können die Dateien oder Ordner nur in einem Netzwerkordner wiederherstellen. 

1. Wählen Sie auf dem Bildschirm **Ziel angeben** die Option **Durchsuchen** aus, um eine Netzwerkadresse für Ihre Dateien bzw. Ordner auszuwählen. MABS erstellt einen Ordner, in den alle wiederhergestellten Elemente kopiert werden. Der Name des Ordners enthält das Präfix „MABS_day-month-year“. Wenn Sie einen Speicherort für die wiederhergestellten Dateien oder den Ordner auswählen, werden die Details für diesen Speicherort angegeben (Ziel, Zielpfad und verfügbarer Speicherplatz). 

    ![Angeben des Standorts für die Wiederherstellung der Dateien der Hyper-V-VM](./media/back-up-hyper-v-virtual-machines-mabs/hyper-v-vm-specify-destination.png) 

1. Wählen Sie auf dem Bildschirm **Wiederherstellungsoptionen angeben** aus, welche Sicherheitseinstellung angewendet werden soll. Sie können sich für eine Änderung der Drosselung der Netzwerkbandbreite entscheiden, aber die Drosselung ist standardmäßig deaktiviert. Auch die Optionen **SAN-Wiederherstellung** und **Benachrichtigung** sind nicht aktiviert. 

1. Überprüfen Sie Ihre Einstellungen auf dem Bildschirm **Zusammenfassung**, und wählen Sie **Wiederherstellen** aus, um den Wiederherstellungsprozess zu starten. Auf dem Bildschirm **Wiederherstellungsstatus** wird der Status des Wiederherstellungsvorgangs angezeigt. 

## <a name="next-steps"></a>Nächste Schritte

[Wiederherstellen von Daten von Azure Backup Server](./backup-azure-alternate-dpm-server.md)
