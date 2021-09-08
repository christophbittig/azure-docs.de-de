---
title: Informationen zu Failover und Failback in Azure Site Recovery – Vorschau
description: Erfahren Sie mehr zu Failover und Failback in Azure Site Recovery – Vorschau
ms.topic: conceptual
ms.date: 08/19/2021
ms.openlocfilehash: d7e08623a1e297d618365a126f835f88e9dfb716
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446871"
---
# <a name="about-on-premises-disaster-recovery-failoverfailback---preview"></a>Informationen zum lokalen Failover und Failback bei der Notfallwiederherstellung – Vorschau

Dieser Artikel bietet eine Übersicht über Failover- und Failbackvorgänge bei der Notfallwiederherstellung von lokalen Computern in Azure mit [Azure Site Recovery](site-recovery-overview.md) – Vorschau.

Informationen zu Failover und Failback im Azure Site Recovery (klassisch)-Release finden Sie [in diesem Artikel](failover-failback-overview.md).

## <a name="recovery-stages"></a>Wiederherstellungsphasen

Failover und Failback in Site Recovery erfolgen in vier Phasen:

- **Phase 1: Failover vom lokalen Standort**: Nach dem Einrichten der Replikation in Azure für lokale Computer führen Sie ein Failover dieser Computer nach Azure durch, wenn Ihr lokaler Standort ausfällt. Nach dem Failover werden auf der Grundlage der replizierten Daten virtuelle Azure-Computer erstellt.
- **Phase 2: Erneutes Schützen der Azure-VMs**: In Azure schützen Sie die Azure-VMs erneut, damit sie wieder an den lokalen Standort repliziert werden können. Die lokale VM (sofern verfügbar) wird während der Anwendung des erneuten Schutzes deaktiviert, um die Konsistenz der Daten sicherzustellen.
- **Phase 3: Failover von Azure**: Wenn der Normalbetrieb Ihres lokalen Standorts wiederhergestellt ist, führen Sie erneut ein Failover aus, um ein Failback der Azure-VMs an Ihren lokalen Standort durchzuführen. Sie können das Failback zum ursprünglichen Speicherort durchführen – von dem aus Sie das Failover ausgeführt haben – oder einen anderen Speicherort verwenden. Diese Aktivität wird als *geplantes Failover* bezeichnet.
- **Phase 4: Erneutes Schützen der lokalen Computer**: Nach dem Failback aktivieren Sie die Replikation der lokalen Computer nach Azure erneut.

## <a name="failover"></a>Failover

Sie führen Failovervorgänge im Rahmen Ihrer Strategie für Geschäftskontinuität und Notfallwiederherstellung (Business Continuity and Disaster Recovery, BCDR) aus.

- Der erste Schritt Ihrer BCDR-Strategie besteht darin, Ihre lokalen Computer fortlaufend in Azure zu replizieren. Benutzer greifen auf Workloads und Apps zu, die auf den lokalen Quellcomputern ausgeführt werden.
- Im Notfall – z. B. beim Ausfall eines lokalen Standorts – führen Sie ein Failover der replizierenden Computer nach Azure durch. Azure-VMs werden mit den replizierten Daten erstellt.
- Um die Geschäftskontinuität sicherzustellen, können Benutzer weiterhin auf die Apps auf den Azure-VMs zugreifen.

Ein Failover erfolgt in zwei Phasen:

- **Failover**: Bei einem Failover wird eine Azure-VM erstellt und anhand des ausgewählten Wiederherstellungspunkts eingerichtet.
- **Commit**: Nach dem Failover überprüfen Sie die VM in Azure:
    - Dann können Sie das Failover auf den ausgewählten Wiederherstellungspunkt committen oder einen anderen Punkt für den Commit auswählen.
    - Nach dem Committen des Failovers kann der Wiederherstellungspunkt nicht mehr geändert werden.


## <a name="connect-to-azure-after-failover"></a>Herstellen einer Verbindung mit Azure nach einem Failover

Um nach einem Failover über RDP/SSH eine Verbindung mit den Azure-VMs herzustellen, müssen mehrere Anforderungen erfüllt werden.

**Failover** | **Location** | **Aktionen**
--- | --- | ---
**Virtueller Azure-Computer unter Windows** | Auf dem lokalen Computer vor dem Failover | **Zugriff über das Internet**: Aktivieren Sie RDP. Stellen Sie sicher, dass TCP- und UDP-Regeln für **Öffentlich** hinzugefügt werden und dass RDP unter **Windows-Firewall** > **Zugelassene Apps** für alle Profile zugelassen ist.<br/><br/> **Zugriff über Site-to-Site-VPN**: Aktivieren Sie RDP auf dem Computer. Überprüfen Sie, ob RDP unter **Windows-Firewall** -> **Zugelassene Apps und Feature** für **private und Domänennetzwerke** zugelassen ist.<br/><br/>  Achten Sie darauf, dass die SAN-Richtlinie des Betriebssystems auf **OnlineAll** festgelegt ist. [Weitere Informationen](https://support.microsoft.com/kb/3031135)<br/><br/> Stellen Sie sicher, dass auf der VM keine ausstehenden Windows-Updates vorhanden sind, wenn Sie ein Failover auslösen. Windows Update wird möglicherweise während des Failovers gestartet, und Sie können sich erst dann wieder bei der VM anmelden, nachdem die Updates fertig gestellt sind.
**Virtueller Azure-Computer unter Windows** | Auf der Azure-VM nach einem Failover |  Fügen Sie der VM eine [öffentliche IP-Adresse](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) hinzu.<br/><br/> In den Netzwerksicherheitsgruppen-Regeln auf der VM nach dem Failover (und im Azure-Subnetz, mit dem sie verbunden ist) müssen eingehende Verbindungen über den RDP-Port zulässig sein.<br/><br/> Aktivieren Sie die **Startdiagnose**, um einen Screenshot der VM anzuzeigen. Wenn Sie keine Verbindung herstellen können, überprüfen Sie, ob die VM ausgeführt wird, und sehen sich die [Tipps zur Problembehandlung](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx) an.
**Virtueller Azure-Computer unter Linux** | Auf dem lokalen Computer vor dem Failover | Stellen Sie sicher, dass der Secure Shell-Dienst auf der VM so festgelegt ist, dass er beim Systemstart automatisch gestartet wird.<br/><br/> Überprüfen Sie, ob die Firewallregeln eine SSH-Verbindung damit zulassen.
**Virtueller Azure-Computer unter Linux** | Auf der Azure-VM nach einem Failover | In den Netzwerksicherheitsgruppen-Regeln auf der VM nach dem Failover (und für das Azure-Subnetz, mit dem sie verbunden ist) müssen eingehende Verbindungen über den SSH-Port zulässig sein.<br/><br/> Fügen Sie der VM eine [öffentliche IP-Adresse](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) hinzu.<br/><br/> Aktivieren Sie die **Startdiagnose**, um einen Screenshot der VM anzuzeigen.<br/><br/>

## <a name="types-of-failover"></a>Failover-Arten

Site Recovery stellt verschiedene Failoveroptionen bereit.

**Failover** | **Details** | **Wiederherstellung** | **Workflow**
--- | --- | --- | ---
**Test-Failover** | Wird zum Ausführen einer Übung verwendet, mit der Ihre BCDR-Strategie überprüft wird – ohne Datenverluste oder Ausfallzeiten.| Erstellt eine Kopie der VM in Azure, ohne dass sich dies auf laufende Replikationsprozesse oder Ihre Produktionsumgebung auswirkt. | 1. Führen Sie ein Testfailover für eine einzelne VM oder mehrere VMs in einem Wiederherstellungsplan aus.<br/><br/> 2. Wählen Sie einen Wiederherstellungspunkt für das Testfailover aus.<br/><br/> 3. Wählen Sie ein Azure-Netzwerk aus, in dem die Azure-VM platziert wird, wenn sie nach dem Failover erstellt wird. Das Netzwerk wird nur für das Testfailover verwendet.<br/><br/> 4. Überprüfen Sie, ob die Übung wie erwartet funktioniert hat. Site Recovery bereinigt automatisch alle VMs, die während der Übung in Azure erstellt wurden.
**Geplantes Failover – Hyper-V**  | Wird in der Regel für geplante Ausfälle verwendet.<br/><br/> Die Quell-VMs werden heruntergefahren. Die neuesten Daten werden synchronisiert, bevor das Failover initiiert wird. | Für den geplanten Workflow tritt kein Datenverlust auf. | 1. Planen Sie ein Wartungsfenster für die Ausfallzeit, und benachrichtigen Sie die Benutzer.<br/><br/> 2. Schalten Sie Apps für Benutzer offline.<br/><br/> 3. Initiieren Sie ein geplantes Failover mit dem neuesten Wiederherstellungspunkt. Das Failover wird nicht ausgeführt, wenn der Computer nicht heruntergefahren wird oder wenn Fehler auftreten.<br/><br/> 4. Überprüfen Sie nach dem Failover, ob die Azure-Replikat-VM in Azure aktiv ist.<br/><br/> 5. Führen Sie ein Commit für das Failover aus, um den Vorgang abzuschließen. Die Commitaktion löscht alle Wiederherstellungspunkte.
**Failover – Hyper-V** | Wird in der Regel ausgeführt, wenn ein ungeplanter Ausfall auftritt oder der primäre Standort nicht verfügbar ist.<br/><br/> Optional können Sie die VM herunterfahren und letzte Änderungen synchronisieren, bevor Sie das Failover initiieren.  | Minimaler Datenverlust für Apps. | 1. Initiieren Sie Ihren BCDR-Plan. <br/><br/> 2. Initiieren Sie ein Failover. Legen Sie fest, ob Site Recovery die VM herunterfahren und die letzten Änderungen synchronisieren/replizieren soll, bevor das Failover ausgelöst wird.<br/><br/> 3. Sie können ein Failover auf eine Reihe unterschiedlicher Wiederherstellungspunkte ausführen. Eine Zusammenfassung finden Sie in der folgenden Tabelle.<br/><br/> Wenn Sie die Option zum Herunterfahren der VM nicht aktivieren oder Site Recovery die VM nicht herunterfahren kann, wird der letzte Wiederherstellungspunkt verwendet.<br/>Das Failover wird auch dann ausgeführt, wenn der Computer nicht heruntergefahren werden kann.<br/><br/> 4. Überprüfen Sie nach dem Failover, ob die Azure-Replikat-VM in Azure aktiv ist.<br/> Ggf. können Sie aus dem Aufbewahrungszeitraum, der 24 Stunden umfasst, einen anderen Wiederherstellungspunkt auswählen.<br/><br/> 5. Führen Sie ein Commit für das Failover aus, um den Vorgang abzuschließen. Die Commitaktion löscht alle verfügbaren Wiederherstellungspunkte.
**Failover – VMware** | Wird in der Regel ausgeführt, wenn ein ungeplanter Ausfall auftritt oder der primäre Standort nicht verfügbar ist.<br/><br/> Optional können Sie angeben, dass Site Recovery versuchen soll, das Herunterfahren der VM auszulösen und letzte Änderungen zu synchronisieren und zu replizieren, bevor das Failover initiiert wird.  | Minimaler Datenverlust für Apps. | 1. Initiieren Sie Ihren BCDR-Plan. <br/><br/> 2. Initiieren Sie ein Failover aus Site Recovery. Geben Sie an, ob Site Recovery versuchen soll, das Herunterfahren der VM auszulösen und eine Synchronisierung durchzuführen, bevor das Failover ausgeführt wird.<br/> Das Failover wird auch dann ausgeführt, wenn die Computer nicht heruntergefahren werden können.<br/><br/> 3. Überprüfen Sie nach dem Failover, ob die Azure-Replikat-VM in Azure aktiv ist. <br/>Ggf. können Sie aus dem Aufbewahrungszeitraum, der 72 Stunden umfasst, einen anderen Wiederherstellungspunkt auswählen.<br/><br/> 5. Führen Sie ein Commit für das Failover aus, um den Vorgang abzuschließen. Die Commitaktion löscht alle Wiederherstellungspunkte.<br/> Bei Windows-VMs deaktiviert Site Recovery die VMware-Tools während des Failovers.
**Geplantes Failover – VMware** | Sie können ein geplantes Failover von Azure zu einem lokalen Standort durchführen. | Da es sich um eine geplante Failoveraktivität handelt, wird der Wiederherstellungspunkt generiert, nachdem der geplante Failoverauftrag ausgelöst wurde. | Wenn das geplante Failover ausgelöst wird, werden ausstehende Änderungen an den lokalen Standort kopiert, es wird ein aktueller Wiederherstellungspunkt des virtuellen Computers generiert, und der virtuelle Azure-Computer wird heruntergefahren.<br/><br/> Befolgen Sie den Failoverprozess, wie [hier](vmware-azure-tutorial-failover-failback-preview.md#planned-failover-from-azure-to-on-premises) beschrieben. Danach ist der lokale Computer eingeschaltet. Nach einem erfolgreichen geplanten Failover ist der Computer in Ihrer lokalen Umgebung aktiv.

## <a name="failover-processing"></a>Failoververarbeitung

In einigen Szenarien erfordert ein Failover zusätzliche Verarbeitungsschritte, die etwa 8 bis 10 Minuten dauern können. Bei folgenden Computern kann das Testfailover länger dauern:

* Virtuelle VMware-Computer mit nicht aktiviertem DHCP-Dienst
* Virtuelle VMware-Computer ohne die folgenden Starttreiber: storvsc, vmbus, storflt, intelide, atapi

## <a name="recovery-point-options"></a>Optionen für Wiederherstellungspunkte

Während eines Failovers können Sie verschiedene Optionen für Wiederherstellungspunkte auswählen.

**Option** | **Details**
--- | ---
**Letzte (niedrigste RPO)** | Diese Option bietet die niedrigste RPO (Recovery Point Objective). Hierbei werden zuerst alle Daten verarbeitet, die an den Site Recovery-Dienst gesendet wurden, um vor dem Failover einen Wiederherstellungspunkt für jede VM zu erstellen. Bei diesem Wiederherstellungspunkt sind alle Daten in Site Recovery repliziert, wenn das Failover ausgelöst wird.
**Letzte Verarbeitung**  | Mit dieser Option wird ein Failover der VMs auf den letzten Wiederherstellungspunkt ausgeführt, der von Site Recovery verarbeitet wurde. Um den letzten Wiederherstellungspunkt für eine bestimmte VM zu finden, überprüfen Sie **Letzte Wiederherstellungspunkte** in den Einstellungen der VM. Diese Option bietet eine niedrige Recovery Time Objective (RTO), da keine Zeit für die Verarbeitung unverarbeiteter Daten aufgewendet wird.
**Letzte App-Konsistenz** |  Mit dieser Option wird ein Failover der VMs auf den letzten App-konsistenten Wiederherstellungspunkt ausgeführt, der von Site Recovery verarbeitet wurde. Dies gilt nur, wenn Wiederherstellungspunkte mit App-Konsistenz aktiviert sind. Überprüfen Sie den letzten Wiederherstellungspunkt in den VM-Einstellungen.
**Letzte Verarbeitung mit mehreren VMs** | Diese Option steht nur für Wiederherstellungspläne mit einer oder mehreren VMs zur Verfügung, bei denen Multi-VM-Konsistenz aktiviert ist. VMs, für die die Einstellung aktiviert ist, führen ein Failover auf den letzten allgemeinen Wiederherstellungspunkt mit Multi-VM-Konsistenz durch. Für alle weiteren VMs im Plan wird ein Failover auf den letzten verarbeiteten Wiederherstellungspunkt ausgeführt.
**Letzte App-Konsistenz mit mehreren VMs** |  Diese Option steht nur für Wiederherstellungspläne mit einer oder mehreren VMs zur Verfügung, bei denen Multi-VM-Konsistenz aktiviert ist. VMs, die Teil einer Replikationsgruppe sind, führen ein Failover auf den letzten allgemeinen Wiederherstellungspunkt mit Multi-VM-Anwendungskonsistenz durch. Andere VMs führen ein Failover auf ihren letzten anwendungskonsistenten Wiederherstellungspunkt durch.
**Benutzerdefiniert** | Verwenden Sie diese Option für ein Failover einer bestimmten VM auf einen bestimmten Wiederherstellungszeitpunkt. Diese Option ist für Wiederherstellungspläne nicht verfügbar.

> [!NOTE]
> Wiederherstellungspunkte können nicht zu einem anderen Recovery Services-Tresor migriert werden.

## <a name="reprotectionplanned-failover"></a>Erneutes Schützen/geplantes Failover

Nach dem Failover zu Azure sind die replizierten Azure-VMs nicht geschützt.

- Als ersten Schritt für das Failback an Ihren lokalen Standort müssen Sie damit beginnen, die Azure-VMs lokal zu replizieren. Der Prozess für den erneuten Schutz richtet sich nach der Art der Computer, für die Sie das Failover ausgeführt haben.
- Nach dem Einrichten der Replikation der Computer von Azure zum lokalen Standort können Sie ein Failover von Azure an Ihren lokalen Standort ausführen.
- Wenn die Computer wieder lokal ausgeführt werden, können Sie die Replikation aktivieren, sodass die Computer zum Zweck der Notfallwiederherstellung nach Azure repliziert werden.

**Ein geplantes Failover funktioniert wie folgt**:

- Damit ein Failback zurück in den lokalen Standort möglich ist, benötigt eine VM mindestens einen Wiederherstellungspunkt. In einem Wiederherstellungsplan benötigen alle VMs mindestens einen Wiederherstellungspunkt.
- Da dies eine geplante Failoveraktivität ist, haben Sie die Möglichkeit, den Typ des Wiederherstellungspunkts auszuwählen, auf den das Failback ausgeführt werden soll. Es wird empfohlen, einen absturzkonsistenten Punkt zu verwenden.
    - Es gibt auch eine Option für einen App-konsistenten Wiederherstellungspunkt. In diesem Fall wird eine einzelne VM auf den letzten verfügbaren Wiederherstellungspunkt mit App-Konsistenz wiederhergestellt. Bei einem Wiederherstellungsplan mit einer Replikationsgruppe erfolgt die Wiederherstellung für jede Replikationsgruppe auf den gemeinsam verfügbaren Wiederherstellungspunkt.
    - Wiederherstellungspunkte mit App-Konsistenz können zeitlich weiter zurück liegen, und es kann zu Datenverlusten kommen.
- Während das Failover von Azure an den lokalen Standort ausgeführt wird, fährt Site Recovery die Azure-VMs herunter. Wenn Sie ein Commit für das Failover ausführen, entfernt Site Recovery die Azure-Failback-VMs aus Azure.


## <a name="vmwarephysical-reprotectionfailback"></a>VMware/physischer erneuter Schutz/Failback

Um VMware-Computer und physische Server erneut zu schützen und ein Failback für sie von Azure zum lokalen Standort auszuführen, stellen Sie sicher, dass Sie über eine fehlerfreie Appliance verfügen.

**Applianceauswahl**

- Sie können jede der Azure Site Recovery-Replikationsappliances auswählen, die in einem Tresor registriert sind, um lokal erneut zu schützen. Sie benötigen keinen separaten Prozessserver in Azure für erneute Schutzvorgänge und keinen Masterzielserver mit horizontaler Skalierung für virtuelle Linux-Computer.
- Die Replikationsappliance erfordert während des Failbacks keine zusätzlichen Netzwerkverbindungen/Ports (im Vergleich zum Vorwärtsschutz). Dieselbe Appliance kann für Vorwärts- und Rückwärtsschutz verwendet werden, wenn sie sich in einem fehlerfreien Zustand befindet. Dies sollte sich nicht auf die Leistung der Replikationen auswirken.
- Stellen Sie sicher, dass der Datenspeicher oder der ausgewählte Host der Datenspeicher ist, in dem sich die Appliance befindet, und dass die ausgewählte Appliance darauf zugreifen kann.
  > [!NOTE]
  > Storage vMotion der Replikationsappliance wird nach einem Vorgang zum erneuten Schützen nicht unterstützt.


**Auftrag zum erneuten Schützen**

- Wenn dies ein neuer Vorgang zum erneuten Schützen ist, wird standardmäßig von Azure Site Recovery in der Zielregion ein neues Protokollspeicherkonto erstellt. Ein Aufbewahrungsdatenträger ist nicht erforderlich.
- Im Fall einer Wiederherstellung an einem alternativen Standort oder einer Wiederherstellung am ursprünglichen Standort werden die ursprünglichen Konfigurationen der Quellcomputer abgerufen.
  > [!NOTE]
  > - Statische IP-Adressen können im Falle von erneutem Schutz an alternativem Standort (ALR) oder erneutem Schutz am ursprünglichen Standort (OLR) nicht beibehalten werden.
  > - fstab, LVMconf würde geändert.


**Fehler**

- Jeder fehlgeschlagene Auftrag zum erneuten Schützen kann wiederholt werden. Während des Wiederholungsversuchs können Sie eine beliebige fehlerfreie Replikationsappliance auswählen.

Wenn Sie Azure-Computer am lokalen Standort erneut schützen, werden Sie benachrichtigt, dass ein Failback an den ursprünglichen Standort oder an einen alternativen Standort ausgeführt haben.

- **Wiederherstellung am ursprünglichen Speicherort**: Damit wird ein Failback von Azure zum selben lokalen Quellcomputer ausgeführt, sofern dieser vorhanden ist. In diesem Szenario werden nur Änderungen wieder an den lokalen Computer repliziert.
  - **Datenspeicherauswahl während OLR**: Der an den Quellcomputer angefügte Datenspeicher wird automatisch ausgewählt.
- **Wiederherstellung an einem anderen Speicherort**: Wenn der lokale Computer nicht vorhanden ist, können Sie ein Failback von Azure zu einem anderen Speicherort durchführen. Wenn Sie die Azure-VM im lokalen Speicherort erneut schützen, wird der lokale Computer erstellt. Von Azure zum lokalen Speicherort erfolgt eine vollständige Datenreplikation. [Informieren](concepts-types-of-failback.md) Sie sich über die Anforderungen und Einschränkungen für das Failback.
  - **Datenspeicherauswahl während ALR**: Jeder Datenspeicher, der von vCenter verwaltet wird, auf dem sich die Appliance befindet und auf den sie zugreifen (Lese- und Schreibberechtigungen) kann, kann ausgewählt werden (original/neu). Sie können das Cachespeicherkonto auswählen, das für den erneuten Schutz verwendet wird.

- Nach Abschluss des Failovers wird der Mobilitäts-Agent automatisch von den Site Recovery-Diensten auf der Azure-VM registriert. Wenn bei der Registrierung ein Fehler auftritt, wird auf dem virtuellen Computer, auf den das Failover ausgeführt wurde, ein kritisches Integritätsproblem ausgelöst. Nachdem das Problem behoben wurde, wird die Registrierung automatisch ausgelöst. Sie können die Registrierung manuell abschließen, nachdem Sie die Fehler behoben haben.


## <a name="cancel-failover"></a>Failover abbrechen

Wenn Ihre lokale Umgebung nicht bereit ist oder Sie vor Herausforderungen stehen, können Sie das Failover abbrechen.

Nachdem Sie das geplante Failover initiiert und erfolgreich abgeschlossen haben, steht Ihre lokale Umgebung für die Nutzung zur Verfügung. Wenn Sie jedoch nach Abschluss des Vorgangs ein Failover zu einem anderen Wiederherstellungspunkt ausführen möchten, können Sie das Failover abbrechen.


- Nur ein geplantes Failover kann abgebrochen werden.

- Sie können ein geplantes Failover über die Seite **Replizierte Elemente** in Ihrem Recovery Services-Tresor abbrechen.

- Nachdem das Failover abgebrochen wurde, werden Ihre Computer in Azure wieder aktiviert, und die Replikation beginnt erneut von Azure zum lokalen Standort.


## <a name="next-steps"></a>Nächste Schritte
- [Failover von VMware-VMs auf Azure (Vorschau)](vmware-azure-tutorial-failover-failback-preview.md#run-a-failover-to-azure)
- [Geplantes Failover (Vorschau)](vmware-azure-tutorial-failover-failback-preview.md#planned-failover-from-azure-to-on-premises)
- [Erneutes Schützen (Vorschau)](vmware-azure-tutorial-failover-failback-preview.md#re-protect-the-on-premises-machine-to-azure-after-successful-planned-failover)
- [Failover abbrechen (Vorschau)](vmware-azure-tutorial-failover-failback-preview.md#cancel-planned-failover)
