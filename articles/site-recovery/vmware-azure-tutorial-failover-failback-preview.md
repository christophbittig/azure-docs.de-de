---
title: Ausführen eines Failovers für VMware-VMs auf Azure mit Site Recovery – Vorschau
description: Hier erfahren Sie, wie Sie ein Failover für VMware-VMs auf Azure in Azure Site Recovery ausführen – Vorschau.
ms.service: site-recovery
ms.topic: tutorial
ms.date: 08/19/2021
ms.custom: MVC
ms.openlocfilehash: 42ccd96287fefed0a6c7ef4cee8ddf776541c8ba
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122447060"
---
# <a name="fail-over-vmware-vms---preview"></a>Ausführen eines Failovers für VMware-VMs – Vorschau

In diesem Artikel erfahren Sie, wie Sie für einen lokalen virtuellen VMware-Computer (VM) mit [Azure Site Recovery](site-recovery-overview.md) ein Failover auf Azure ausführen – Vorschau.

Informationen zum Failover in klassischen Releases finden Sie in [diesem Artikel](vmware-azure-tutorial-failover-failback.md).

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
> * Überprüfen der VMware-VM-Eigenschaften, um die Konformität mit Azure-Anforderungen zu bestätigen
> * Ausführen eines Failovers auf Azure für bestimmte VMs

> [!NOTE]
> In den Tutorials wird der einfachste Bereitstellungspfad für ein Szenario erläutert. Sie verwenden nach Möglichkeit Standardoptionen und zeigen nicht alle möglichen Einstellungen und Pfade. Wenn Sie sich detailliert über Failover informieren möchten, lesen Sie [Ausführen eines Failovers für virtuelle Computer und physische Server](site-recovery-failover.md).

[Erfahren Sie mehr](failover-failback-overview.md#types-of-failover) über verschiedene Failovertypen. Wenn Sie ein Failover für mehrere VMs in einem Wiederherstellungsplan ausführen möchten, lesen Sie [diesen Artikel](site-recovery-failover.md).

## <a name="before-you-start"></a>Vorbereitung

Absolvieren Sie die vorherigen Tutorials:

1. Vergewissern Sie sich, dass Sie die Schritte zum [Einrichten von Azure](tutorial-prepare-azure.md) für die lokale Notfallwiederherstellung von VMware-VMs ausgeführt haben.
2. Bereiten Sie Ihre lokale [VMware](vmware-azure-tutorial-prepare-on-premises.md)-Umgebung für die Notfallwiederherstellung vor.
3. Richten Sie die Notfallwiederherstellung für [VMware-VMs](vmware-azure-set-up-replication-tutorial-preview.md) ein.
4. Führen Sie ein [Notfallwiederherstellungsverfahren](tutorial-dr-drill-azure.md) durch, um zu überprüfen, ob alles wie erwartet funktioniert.

## <a name="verify-vm-properties"></a>Überprüfen von VM-Eigenschaften

Überprüfen Sie vor dem Ausführen eines Failovers die VM-Eigenschaften, um sich zu vergewissern, dass die VMs den [Azure-Anforderungen](vmware-physical-azure-support-matrix.md#replicated-machines) entsprechen.

Gehen Sie zum Überprüfen der Eigenschaften wie folgt vor:

1. Wählen Sie in **Geschützte Elemente****Replizierte Elemente** aus, und wählen Sie dann die VM aus, die Sie überprüfen möchten.

2. Im Bereich **Repliziertes Element** finden Sie eine Zusammenfassung der Informationen zu virtuellen Computern, den Integritätsstatus sowie die neuesten verfügbaren Wiederherstellungspunkte. Wählen Sie **Eigenschaften** aus, um weitere Details anzuzeigen.

3. Unter **Compute und Netzwerk** können Sie diese Einstellungen nach Bedarf ändern:
    * Azure-Name
    * Resource group
    * Zielgröße
    * Einstellungen für verwaltete Datenträger

4. Sie können Netzwerkeinstellungen anzeigen und ändern, darunter:

    * Das Netzwerk und Subnetz, in dem die Azure-VM nach einem Failover gespeichert werden soll.
    * Die IP-Adresse, die ihr zugewiesen wird.

5. Unter **Datenträger** finden Sie Informationen über das Betriebssystem und die Datenträger auf dem virtuellen Computer.

## <a name="run-a-failover-to-azure"></a>Ausführen eines Failovers auf Azure

1. Wählen Sie unter **Einstellungen** > **Replizierte Elemente** den virtuellen Computer aus, für den das Failover ausgeführt werden soll, und wählen Sie dann **Failover** aus.
2. Wählen Sie unter **Failover** einen **Wiederherstellungspunkt** für das Failover aus. Sie können eine der folgenden Optionen auswählen:
   * **Neueste**: Mit dieser Option werden zuerst alle an Site Recovery gesendeten Daten verarbeitet. Sie bietet die niedrigste RPO (Recovery Point Objective), da die nach dem Failover erstellte Azure-VM über alle Daten verfügt, die bei Auslösung des Failovers zu Site Recovery repliziert wurden.
   * **Letzte Verarbeitung**: Mit dieser Option wird das Failover des virtuellen Computers auf den letzten Wiederherstellungspunkt ausgeführt, der von Site Recovery verarbeitet wurde. Diese Option bietet eine niedrige Recovery Time Objective (RTO), da keine Zeit für die Verarbeitung unverarbeiteter Daten aufgewendet wird.
   * **Letzter anwendungskonsistenter Zeitpunkt**: Mit dieser Option wird ein Failover des virtuellen Computers auf den letzten anwendungskonsistenten Wiederherstellungspunkt ausgeführt, der von Site Recovery verarbeitet wurde.
   * **Benutzerdefiniert**: Diese Option gestattet Ihnen das Angeben eines Wiederherstellungspunkts.

3. Wählen Sie **Der Computer wird vor Beginn des Failovers heruntergefahren** aus, um zu versuchen, virtuelle Quellcomputer herunterzufahren, bevor das Failover ausgelöst wird. Das Failover wird auch dann fortgesetzt, wenn das Herunterfahren nicht erfolgreich ist. Der Fortschritt des Failovers wird auf der Seite **Aufträge** angezeigt.

  In einigen Szenarien erfordert ein Failover zusätzliche Verarbeitungsschritte, die etwa 8 bis 10 Minuten dauern können. Bei folgenden Computern kann das Testfailover länger dauern:

  * VMware-Linux-VMs
  * Virtuelle VMware-Computer mit nicht aktiviertem DHCP-Dienst
  * Virtuelle VMware-Computer ohne die folgenden Starttreiber: storvsc, vmbus, storflt, intelide, atapi

  > [!WARNING]
  > Brechen Sie ein Failover in Bearbeitung nicht ab. Bevor das Failover gestartet wird, wird die VM-Replikation beendet. Wenn Sie ein Failover in Bearbeitung abbrechen, wird das Failover beendet, die Replikation der VM wird jedoch nicht erneut durchgeführt.

## <a name="connect-to-failed-over-vm"></a>Herstellen einer Verbindung mit einer VM nach dem Failover

1. Wenn Sie Verbindungen mit Azure-VMs nach dem Failover mithilfe von RDP (Remote Desktop Protocol) und SSH (Secure Shell) herstellen möchten, [überprüfen Sie, ob die Anforderungen erfüllt wurden](failover-failback-overview.md#connect-to-azure-after-failover).
2. Navigieren Sie nach einem Failover zu dem virtuellen Computer, und stellen Sie zur Überprüfung eine [Verbindung](../virtual-machines/windows/connect-logon.md) her.
3. Verwenden Sie **Wiederherstellungspunkt ändern**, falls Sie nach dem Failover einen anderen Wiederherstellungspunkt verwenden möchten. Wenn Sie das Failover im nächsten Schritt committen, steht diese Option nicht mehr zur Verfügung.
4. Wählen Sie nach der Überprüfung **Committen** aus, um den Wiederherstellungspunkt des virtuellen Computers nach dem Failover fertig zu stellen.
5. Nach dem Committen werden alle anderen verfügbaren Wiederherstellungspunkte gelöscht. Mit diesem Schritt wird das Failover abgeschlossen.

>[!TIP]
> Sollten nach dem Failover Verbindungsprobleme auftreten, lesen Sie das [Handbuch zur Problembehandlung](site-recovery-failover-to-azure-troubleshoot.md).

## <a name="planned-failover-from-azure-to-on-premises"></a>Geplantes Failover von Azure auf den lokalen Standort

Sie können ein geplantes Failover von Azure zu einem lokalen Standort durchführen. Da es sich um eine geplante Failoveraktivität handelt, wird der Wiederherstellungspunkt generiert, nachdem der geplante Failoverauftrag ausgelöst wurde.

>[!NOTE]
> Stellen Sie vor dem Fortfahren sicher, dass die Replikationsintegrität des Computers „fehlerfrei“ lautet. Stellen Sie außerdem sicher, dass die Appliance und alle ihre Komponenten ebenfalls fehlerfrei sind.

Wenn das geplante Failover ausgelöst wird, werden ausstehende Änderungen an den lokalen Standort kopiert, es wird ein aktueller Wiederherstellungspunkt des virtuellen Computers generiert, und der virtuelle Azure-Computer wird heruntergefahren. Danach ist der lokale Computer eingeschaltet.

Nach einem erfolgreichen geplanten Failover ist der Computer in Ihrer lokalen Umgebung aktiv.

> [!NOTE]
> Wenn der geschützte Computer über iSCSI-Datenträger verfügt, wird die Konfiguration bei einem Failover in Azure beibehalten. Nach einem geplanten Failover von Azure auf den lokalen Standort kann die iSCSI-Konfiguration nicht beibehalten werden. Daher werden vmdk-Datenträger auf dem lokalen Computer erstellt. Um doppelte Datenträger zu entfernen, löschen Sie den iSCSI-Datenträger, wenn die Daten durch vmdk-Datenträger ersetzt werden.


### <a name="failed-over-vm-to-azure---requirements"></a>Failover für VM zu Azure – Anforderungen

Vergewissern Sie sich, dass für den virtuellen Computer nach dem Ausführen eines Failovers zu Azure Folgendes gilt:

1. Der virtuelle Computer in Azure sollte immer eingeschaltet sein.
2. Stellen Sie sicher, dass die Mobilitäts-Agent-Dienste *Dienst 1* und *Dienst 2* auf dem virtuellen Computer ausgeführt werden. Dadurch wird sichergestellt, dass der Mobilitäts-Agent auf dem virtuellen Computer mit Azure Site Recovery-Diensten in Azure kommunizieren kann.
3. Auf die [hier](vmware-azure-architecture-preview.md#set-up-outbound-network-connectivity) genannten URLs kann vom virtuellen Computer aus zugegriffen werden.

## <a name="cancel-planned-failover"></a>Abbrechen eines geplanten Failovers

Wenn Ihre lokale Umgebung nicht bereit ist oder Probleme auftreten sollten, können Sie das geplante Failover abbrechen. Sie können ein geplantes Failover jederzeit später durchführen, sobald Ihre lokalen Bedingungen geeignet sind.

**So brechen Sie ein geplantes Failover ab**

1. Navigieren Sie zum Computer im Recovery Services-Tresor, und wählen Sie **Failover abbrechen** aus.
2. Klicken Sie auf **OK**.
3. Vergewissern Sie sich, dass Sie die Informationen über den Ablauf des *Abbruchs des Failovervorgangs* lesen.

Wenn es Probleme gibt, die Azure Site Recovery daran hindern, den fehlerhaften Auftrag erfolgreich abzubrechen, befolgen Sie die empfohlenen Schritte, die im Auftrag angegeben sind. Führen Sie die empfohlenen Maßnahmen durch und versuchen Sie erneut, den Auftrag abzubrechen.

Der vorherige geplante Failovervorgang wird abgebrochen. Der Computer in Azure wird in den Zustand kurz vor dem Auslösen des *geplantes Failovers* zurückgesetzt.

Für ein geplantes Failover erstellen wir nach dem Trennen der VM-Datenträger von der Appliance eine Momentaufnahme, bevor sie eingeschaltet wird.

Wenn der virtuelle Computer oder eine Anwendung nicht ordnungsgemäß gestartet wird oder Sie sich aus irgendeinem Grund entschließen, das geplante Failover abzubrechen und es erneut zu versuchen, gehen Sie wie folgt vor:

1. Wir würden alle vorgenommenen Änderungen rückgängig machen.

2. Versetzen Sie die Datenträger mithilfe der zuvor erstellten Momentaufnahmen wieder in den Zustand, in dem sie sich vor dem Einschalten befunden haben.

3. Fügen Sie schließlich die Datenträger wieder an die Appliance an, und setzen Sie die Replikation fort.

Dieses Verhalten ist anders als in der Legacy-/klassischen Architektur.

- Mithilfe der Vorschau können Sie den Failbackvorgang zu einem späteren Zeitpunkt erneut ausführen.

- In der Legacyarchitektur können Sie das Failback nicht abbrechen und wiederholen, wenn der virtuelle Computer oder die Anwendung nicht gestartet wird oder ein anderer Grund vorliegt.  


> [!NOTE]
> Nur ein geplantes Failover von Azure auf einen lokalen Standort kann abgebrochen werden. Ein Failover vom lokalen Standort zu Azure kann nicht abgebrochen werden.

### <a name="planned-failover---failure"></a>Geplantes Failover – Fehler

Wenn beim geplanten Failover ein Fehler auftritt, startet Azure Site Recovery automatisch einen Auftrag, um den fehlerhaften Auftrag abzubrechen, und ruft den Zustand des Computers zum Zeitpunkt unmittelbar vor dem geplanten Failover ab.

Wenn beim Abbruch des letzten geplanten Failoverauftrags ein Fehler auftritt, werden Sie von Azure Site Recovery aufgefordert, den Abbruch manuell zu starten.

Diese Informationen werden im Rahmen eines fehlerhaften geplanten Failovervorgangs und als Integritätsproblem des replizierten Elements bereitgestellt.

Wenn das Problem weiterhin besteht, wenden Sie sich an den Microsoft-Support. Deaktivieren Sie die Replikation **nicht**.

## <a name="re-protect-the-on-premises-machine-to-azure-after-successful-planned-failover"></a>Erneutes Schützen des lokalen Computers in Azure nach einem erfolgreichen geplanten Failover

Nach einem erfolgreichen geplanten Failover ist der Computer am lokalen Standort aktiv. Um Ihren Computer in Zukunft zu schützen, stellen Sie sicher, dass der Computer in Azure repliziert (erneut geschützt) wird.

Wechseln Sie dazu zum Computer, wählen Sie **Erneut schützen** sowie die gewünschte Appliance und dann die Replikationsrichtlinie aus. Setzen Sie dann den Vorgang fort.

Nach der erfolgreichen Aktivierung der Replikation und der anfänglichen Replikation werden Wiederherstellungspunkte generiert, um Geschäftskontinuität bei unerwünschten Unterbrechungen zu bieten.

## <a name="next-steps"></a>Nächste Schritte

Nach dem Failover müssen die virtuellen Azure-Computer in der lokalen Umgebung erneut geschützt werden. Nachdem die virtuellen Computer am lokalen Standort erneut geschützt und repliziert sind, können Sie ein Failback von Azure durchführen, wenn Sie bereit sind.

> [!div class="nextstepaction"]
> [Erneutes Schützen virtueller Azure-Computer](vmware-azure-reprotect.md)
> [Failback von Azure](vmware-azure-failback.md)
