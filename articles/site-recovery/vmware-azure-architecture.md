---
title: Architektur der Notfallwiederherstellung für VMware-VMs in Azure Site Recovery (klassisch)
description: Dieser Artikel enthält eine Übersicht über die Komponenten und Architektur, die beim Einrichten der Notfallwiederherstellung von lokalen virtuellen VMware-Computern in Azure mit Azure Site Recovery (klassisch) verwendet werden.
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/19/2021
ms.openlocfilehash: 87bfbad2993bb1eee4c20990082a892cf8e46fd1
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122445405"
---
# <a name="vmware-to-azure-disaster-recovery-architecture---classic"></a>Architektur der Notfallwiederherstellung von VMware zu Azure (klassisch)

Dieser Artikel beschreibt die Architektur und Prozesse, die mithilfe des Diensts [Azure Site Recovery](site-recovery-overview.md) (klassisch) beim Bereitstellen der Replikation für die Notfallwiederherstellung, bei der Ausführung eines Failovers und beim Wiederherstellen von virtuellen VMware-Computern (VMs) zwischen einem lokalen VMware-Standort und Azure verwendet werden.

Informationen zu Architekturdetails in der Vorschau [finden Sie in diesem Artikel](vmware-azure-architecture-preview.md).


## <a name="architectural-components"></a>Komponenten der Architektur

Die folgende Tabelle und Grafik enthält eine Übersicht über die Komponenten, die für die Notfallwiederherstellung für VMware-VMs/physische Computer in Azure verwendet werden.

**Komponente** | **Anforderung** | **Details**
--- | --- | ---
**Azure** | Ein Azure-Abonnement, ein Azure Storage-Konto für die Zwischenspeicherung, einen verwalteten Datenträger und ein Azure-Netzwerk. | Replizierte Daten von lokalen VMs werden in Azure Storage gespeichert. Azure-VMs werden mit den replizierten Daten erstellt, wenn Sie ein Failover von lokalen VMs in Azure ausführen. Für die Azure-VMs wird eine Verbindung mit dem virtuellen Azure-Netzwerk hergestellt, wenn diese erstellt werden.
**Konfigurationsservercomputer** | Ein einzelner lokaler Computer. Es wird empfohlen, diesen als eine VMware-VM auszuführen, die aus einer heruntergeladenen OVF-Vorlage bereitgestellt werden kann.<br/><br/> Auf dem Computer werden alle lokalen Site Recovery-Komponenten ausgeführt, einschließlich Konfigurationsserver, Prozessserver und Masterzielserver. | **Konfigurationsserver**: Koordiniert die Kommunikation zwischen der lokalen Umgebung und Azure und verwaltet die Datenreplikation.<br/><br/> **Prozessserver** Wird standardmäßig auf dem Konfigurationsserver installiert. Er empfängt Replikationsdaten, optimiert sie durch Zwischenspeicherung, Komprimierung und Verschlüsselung und sendet sie an Azure Storage. Der Prozessserver installiert auch Azure Site Recovery Mobility Service auf VMs, die Sie replizieren möchten, und führt auf lokalen Computern eine automatische Ermittlung durch. Bei zunehmender Größe der Bereitstellung können Sie zusätzlich separate Prozessserver hinzufügen, um größere Mengen von Replikationsdatenverkehr zu bewältigen.<br/><br/> **Masterzielserver**: Wird standardmäßig auf dem Konfigurationsserver installiert. Er verarbeitet die Replikationsdaten während des Failbacks von Azure. Bei größeren Bereitstellungen können Sie einen zusätzlichen, separaten Masterzielserver für das Failback hinzufügen.
**VMware-Server** | VMware-Server werden auf lokalen vSphere ESXi-Servern gehostet. Zum Verwalten der Hosts wird ein vCenter-Server empfohlen. | Sie fügen während der Bereitstellung von Site Recovery VMware-Server in den Recovery Services-Tresor hinzu.
**Replizierte Computer** | Mobility Service wird auf jedem virtuellen VMware-Computer installiert, den Sie replizieren. | Es wird empfohlen, dass Sie die automatische Installation vom Prozessserver zulassen. Alternativ können Sie den Dienst manuell installieren oder eine automatisierte Bereitstellungsmethode wie Configuration Manager verwenden.

![Diagramm der Replikationsarchitekturbeziehungen zwischen VMware und Azure](./media/vmware-azure-architecture/arch-enhanced.png)

## <a name="set-up-outbound-network-connectivity"></a>Einrichten der ausgehenden Netzwerkkonnektivität

Damit Site Recovery erwartungsgemäß funktioniert, müssen Sie die ausgehende Netzwerkkonnektivität ändern, um Ihrer Umgebung das Replizieren zu ermöglichen.

> [!NOTE]
> Site Recovery unterstützt die Verwendung eines Authentifizierungsproxys zur Steuerung der Netzwerkkonnektivität nicht.

### <a name="outbound-connectivity-for-urls"></a>Ausgehende Konnektivität für URLs

Lassen Sie den Zugriff auf die folgenden URLs zu, wenn Sie einen URL-basierten Firewallproxy zum Steuern der ausgehenden Konnektivität verwenden:

| **Name**                  | **Kommerziell**                               | **Behörden**                                 | **Beschreibung** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Storage                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | Ermöglicht das Schreiben von Daten aus der VM in das Cachespeicherkonto in der Quellregion |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Stellt die Autorisierung und Authentifizierung für Site Recovery-Dienst-URLs bereit. |
| Replikation               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.us`   | Ermöglicht die Kommunikation der VM mit Site Recovery |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Ermöglicht es der VM, die Site Recovery-Überwachung und -Diagnosedaten zu schreiben |

Eine vollständige Liste der URLs, die für die Kommunikation zwischen der lokalen Azure Site Recovery-Infrastruktur und den Azure-Diensten gefiltert werden müssen, finden Sie im Abschnitt [Netzwerkanforderungen im Artikel zu den Voraussetzungen](vmware-azure-deploy-configuration-server.md#prerequisites).

## <a name="replication-process"></a>Replikationsprozess

1. Wenn Sie die Replikation für einen virtuellen Computer aktivieren, beginnt die erste Replikation in Azure Storage mithilfe der angegebenen Replikationsrichtlinie. Beachten Sie Folgendes:
    - Bei VMware-VMs erfolgt die Replikation auf Blockebene und nahezu kontinuierlich mithilfe des Mobility Service-Agents, der auf dem virtuellen Computer ausgeführt wird.
    - Es werden alle festgelegten Replikationsrichtlinieneinstellungen angewendet:
        - **RPO-Schwellenwert**: Diese Einstellung hat keine Auswirkungen auf die Replikation. Sie unterstützt bei der Überwachung. Ein Ereignis wird ausgelöst, und optional wird eine E-Mail gesendet, wenn das aktuelle RPO den von Ihnen angegebenen Schwellenwert überschreitet.
        - **Aufbewahrung des Wiederherstellungspunkts**. Diese Einstellung gibt an, wie weit Sie zurückgehen möchten, wenn es zu einer Unterbrechung kommt. Bei Premium-Speicher beträgt die maximale Aufbewahrungsdauer 24 Stunden. Bei Standardspeicher beträgt sie 72 Stunden.
        - **App-konsistente Momentaufnahmen**: Abhängig von Ihren App-Anforderungen können alle 1 bis 12 Stunden App-konsistente Momentaufnahmen erstellt werden. Bei den Momentaufnahmen handelt es sich um Azure-Blob-Standardmomentaufnahmen. Der auf einem virtuellen Computer ausgeführte Mobility Service-Agent fordert eine VSS-Momentaufnahme in Übereinstimmung mit dieser Einstellung an und markiert diesen Zeitpunkt als anwendungskonsistenten Punkt im Replikationsstrom.

2. Der Datenverkehr wird über das Internet auf öffentliche Endpunkte von Azure Storage repliziert. Alternativ hierzu können Sie Azure ExpressRoute mit [Microsoft-Peering](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) verwenden. Das Replizieren von Datenverkehr über ein virtuelles privates Site-to-Site-Netzwerk (VPN) von einem lokalen Standort nach Azure wird nicht unterstützt.
3. Der erste Replikationsvorgang stellt sicher, dass die gesamten Daten auf dem Computer zum Zeitpunkt der Replikation an Azure gesendet werden. Die Replikation von Deltaänderungen in Azure beginnt, nachdem die erste Replikation abgeschlossen wurde. Nachverfolgte Änderungen für einen Computer werden an den Prozessserver gesendet.
4. Die Kommunikation erfolgt folgendermaßen:

    - Virtuelle Computer kommunizieren mit dem lokalen Konfigurationsserver über den Port HTTPS 443 für eingehenden Datenverkehr, um die Replikationsverwaltung auszuführen.
    - Der Konfigurationsserver orchestriert die Replikation mit Azure über Port HTTPS 443 für ausgehenden Datenverkehr.
    - Virtuelle Computer senden Replikationsdaten an den Prozessserver (der auf dem Konfigurationsserver ausgeführt wird) über Port HTTPS 9443 für eingehenden Datenverkehr. Dieser Port kann geändert werden.
    - Der Prozessserver empfängt Replikationsdaten, optimiert und verschlüsselt sie und sendet sie über den ausgehenden Port 443 an Azure Storage.
5. Die Replikationsdatenprotokolle werden zunächst in einem Cachespeicherkonto in Azure gespeichert. Diese Protokolle werden dann verarbeitet, und die Daten werden auf einem (als Azure Site Recovery-Seed-Datenträger bezeichneten) verwalteten Azure-Datenträger gespeichert. Die Wiederherstellungspunkte werden auf diesem Datenträger erstellt.

![Diagramm des Replikationsvorgangs von VMware zu Azure](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="resynchronization-process"></a>Neusynchronisierungsprozess

1. Manchmal können bei der ersten Replikation oder beim Übertragen von Deltaänderungen Probleme bei der Netzwerkkonnektivität zwischen dem Quellcomputer und dem Prozessserver oder zwischen dem Prozessserver und Azure auftreten. Beide Arten von Problemen können vorübergehend zu Fehlern bei der Datenübertragung an Azure führen.
2. Um Probleme bei der Datenintegrität zu vermeiden und die Datenübertragungskosten zu minimieren, markiert Site Recovery einen Computer für die erneute Synchronisierung.
3. Ein Computer kann auch in folgenden Situationen für die erneute Synchronisierung markiert werden, um die Konsistenz zwischen dem Quellcomputer und den in Azure gespeicherten Daten aufrechtzuerhalten.
    - Wenn das Herunterfahren eines Computers erzwungen wird
    - Wenn auf einem Computer Konfigurationsänderungen durchgeführt werden, z. B. eine Änderung der Datenträgergröße (Ändern der Größe des Datenträgers von 2 TB in 4 TB)
4. Bei der Neusynchronisierung werden nur Deltadaten an Azure gesendet. Die Datenübertragung zwischen lokalen Systemen und Azure wird durch die Berechnung von Prüfsummen der Daten auf dem Quellcomputer und der in Azure gespeicherten Daten minimiert.
5. Standardmäßig ist die Neusynchronisierung so geplant, dass sie automatisch außerhalb der Geschäftszeiten durchgeführt wird. Wenn Sie nicht auf die Standardneusynchronisierung außerhalb der Geschäftszeiten warten möchten, können Sie eine VM manuell neu synchronisieren. Rufen Sie dafür das Azure-Portal auf, und klicken Sie auf „VM“ > **Resynchronisieren**.
6. Wenn die standardmäßige Neusynchronisierung außerhalb der Geschäftszeiten fehlschlägt und ein manueller Eingriff erforderlich ist, wird ein Fehler auf dem jeweiligen Computer im Azure-Portal generiert. Sie können den Fehler beheben und die Neusynchronisierung manuell auslösen.
7. Nach Abschluss der Neusynchronisierung wird die Replikation der Deltaänderungen fortgesetzt.

## <a name="replication-policy"></a>Replikationsrichtlinie

Wenn Sie die Replikation von Azure-VMs aktivieren, erstellt Site Recovery standardmäßig eine neue Replikationsrichtlinie mit den Standardeinstellungen, die in der Tabelle zusammengefasst sind.

**Richtlinieneinstellung** | **Details** | **Standard**
--- | --- | ---
**Aufbewahrungszeitraum des Wiederherstellungspunkts** | Gibt an, wie lange Site Recovery Wiederherstellungspunkte beibehält | 24 Stunden
**App-konsistente Momentaufnahmenhäufigkeit** | Gibt an, wie oft Site Recovery eine App-konsistente Momentaufnahme erstellt. | Alle vier Stunden

### <a name="managing-replication-policies"></a>Verwalten von Replikationsrichtlinien

Sie können die Standardrichtlinien für die Replikation wie folgt verwalten und ändern:
- Sie können die Einstellungen ändern, während Sie die Replikation aktivieren.
- Sie können jederzeit eine Replikationsrichtlinie erstellen und diese dann anwenden, wenn Sie die Replikation aktivieren.

### <a name="multi-vm-consistency"></a>Multi-VM-Konsistenz

Wenn Sie virtuelle Computer gemeinsam replizieren möchten und beim Failover über gemeinsame absturz- und App-konsistente Wiederherstellungspunkte verfügen, können Sie sie in einer Replikationsgruppe zusammenfassen. Multi-VM-Konsistenz wirkt sich auf die Leistung einer Workload aus und sollte nur für virtuelle Computer mit Workloads verwendet werden, bei denen Konsistenz auf sämtlichen Computern erforderlich ist.



## <a name="snapshots-and-recovery-points"></a>Momentaufnahmen und Wiederherstellungspunkte

Wiederherstellungspunkte werden aus Momentaufnahmen von VM-Datenträgern zu einem bestimmten Zeitpunkt erstellt. Wenn Sie ein Failover eines virtuellen Computers ausführen, verwenden Sie einen Wiederherstellungspunkt, um die VM am Zielstandort wiederherzustellen.

Bei einem Failover soll in der Regel sichergestellt werden, dass der virtuelle Computer ohne Beschädigung oder Verlust von Daten gestartet wird und dass die VM-Daten für das Betriebssystem und die Apps, die auf dem virtuellen Computer ausgeführt werden, konsistent sind. Dies hängt vom Typ der erstellten Momentaufnahmen ab.

Site Recovery verwendet Momentaufnahmen wie folgt:

1. Site Recovery verwendet absturzkonsistente Momentaufnahmen von Daten standardmäßig und App-konsistente Momentaufnahmen, wenn Sie für diese eine Häufigkeit angeben.
2. Wiederherstellungspunkte werden aus Momentaufnahmen erstellt und gemäß den Aufbewahrungseinstellungen in der Replikationsrichtlinie gespeichert.

### <a name="consistency"></a>Konsistenz

In der folgenden Tabelle werden die verschiedenen Konsistenztypen erläutert.

### <a name="crash-consistent"></a>Absturzkonsistent

**Beschreibung** | **Details** | **Empfehlung**
--- | --- | ---
Eine absturzkonsistente Momentaufnahme erfasst Daten, die sich zum Zeitpunkt der Erstellung der Momentaufnahme auf dem Datenträger befunden haben. Sie enthält keine Daten aus dem Arbeitsspeicher.<br/><br/> Sie enthält die Entsprechung der Daten auf dem Datenträger, wenn zum Zeitpunkt der Momentaufnahme der virtuelle Computer abgestürzt oder das Verbindungskabel zum Server getrennt worden wäre.<br/><br/> Absturzkonsistenz garantiert keine Datenkonsistenz für das Betriebssystem oder die Apps auf der VM. | Site Recovery erstellt standardmäßig alle fünf Minuten einen absturzkonsistenten Wiederherstellungspunkt. Diese Einstellung kann nicht geändert werden.<br/><br/>  | Heutzutage können die meisten Apps gut aus absturzkonsistenten Wiederherstellungspunkten wiederhergestellt werden.<br/><br/> Absturzkonsistente Wiederherstellungspunkte reichen in der Regel für die Replikation von Betriebssystemen und Apps wie DHCP-Server und Druckserver aus.

### <a name="app-consistent"></a>App-konsistent

**Beschreibung** | **Details** | **Empfehlung**
--- | --- | ---
App-konsistente Wiederherstellungspunkte werden aus App-konsistenten Momentaufnahmen erstellt.<br/><br/> Eine App-konsistente Momentaufnahme enthält alle Informationen in einer absturzkonsistenten Momentaufnahme sowie darüber hinaus alle Daten im Arbeitsspeicher und alle gerade bearbeiteten Transaktionen. | App-konsistente Momentaufnahmen verwenden den Volumeschattenkopie-Dienst (Volume Shadow Copy Service, VSS):<br/><br/>   1) Azure Site Recovery nutzt die Methode „Kopiesicherung“ (VSS_BT_COPY), bei der Uhrzeit und Sequenznummer der Microsoft SQL-Transaktionsprotokollsicherung nicht geändert werden. </br></br> 2) Wenn eine Momentaufnahme initiiert wird, führt VSS einen COW-Vorgang (Copy-On-Write) auf dem Volume aus.<br/><br/>   3) Vor der Ausführung des COW-Vorgangs informiert VSS jede App auf dem Computer darüber, dass die speicherresidenten Daten auf den Datenträger übertragen werden müssen.<br/><br/>   4) VSS erlaubt dann der Sicherungs-/Notfallwiederherstellungs-App (in diesem Fall Site Recovery) das Lesen der Momentaufnahmedaten und das Fortsetzen des Vorgangs. | App-konsistente Momentaufnahmen werden mit der von Ihnen angegebenen Häufigkeit erstellt. Diese Häufigkeit sollte immer kleiner sein als der Wert für die Beibehaltung von Wiederherstellungspunkten. Wenn Sie beispielsweise Wiederherstellungspunkte gemäß der Standardeinstellung von 24 Stunden beibehalten, sollten Sie die Häufigkeit auf weniger als 24 Stunden festlegen.<br/><br/>Sie sind komplexer und dauern daher länger als absturzkonsistente Momentaufnahmen.<br/><br/> Sie haben auch Auswirkungen auf die Leistung von Apps, die auf einem virtuellen Computer, für den die Replikation aktiviert wurde, ausgeführt werden.

## <a name="failover-and-failback-process"></a>Failover- und Failbackprozesse

Nachdem die Replikation eingerichtet ist und Sie einen Notfallwiederherstellungsdrill (Testfailover) ausgeführt haben, um zu überprüfen, ob alles wie erwartet funktioniert, können Sie bei Bedarf ein Failover und ein Failback ausführen.

1. Sie können ein Failover für einen einzelnen Computer ausführen oder Wiederherstellungspläne erstellen, um ein Failover für mehrere virtuelle Computer gleichzeitig auszuführen. Ein Wiederherstellungsplan besitzt gegenüber dem Failover eines einzelnen Computers die folgenden Vorteile:
    - Sie können App-Abhängigkeiten modellieren, indem Sie alle virtuellen Computer in der App in einen einzelnen Wiederherstellungsplan einschließen.
    - Sie können Skripts und Azure-Runbooks hinzufügen und den Vorgang für manuelle Aktionen anhalten.
2. Nachdem das erste Failover ausgelöst wird, committen Sie es, um von der Azure-VM auf die Workload zuzugreifen.
3. Wenn Ihr primärer lokaler Standort wieder verfügbar ist, können Sie sich auf ein Failback vorbereiten. Damit das Failback ausgeführt werden kann, müssen Sie eine Failbackinfrastruktur festlegen, die Folgendes enthält:

    * **Temporärer Prozessserver in Azure:** Sie legen für ein Failback von Azure eine Azure-VM fest, die als Prozessserver fungiert, um die Replikation von Azure zu verarbeiten. Nach Beendigung des Failbacks können Sie diese VM löschen.
    * **VPN-Verbindung:** Für das Failback benötigen Sie eine VPN-Verbindung (oder ExpressRoute-Verbindung) zwischen dem Azure-Netzwerk und dem lokalen Standort.
    * **Separater Masterzielserver:** Der lokale Masterzielserver, der mit dem Konfigurationsserver in der lokalen VMware-VM installiert wurde, führt standardmäßig das Failback aus. Wenn Sie ein Failback für große Volumen von Datenverkehr ausführen müssen, richten Sie einen separaten lokalen Masterzielserver ein.
    * **Failbackrichtlinie:** Für die Replikation zurück an den lokalen Standort benötigen Sie eine Failbackrichtlinie. Diese Richtlinie wird automatisch erstellt, wenn Sie eine Replikationsrichtlinie aus lokalen VMs in Azure erstellen.
4. Nachdem die Komponenten vorhanden sind, erfolgt das Failback in drei Phasen:

    - Phase 1: Schützen Sie die Azure-VMs erneut, sodass sie die Replikation von Azure zurück zu den lokalen VMware-VMs durchführen.
    -  Phase 2: Führen Sie ein Failover zum lokalen Standort aus.
    - Phase 3: Nachdem für Workloads ein Failback ausgeführt wurde, aktivieren Sie erneut die Replikation für die lokalen VMs.



![Diagramm des VMware-Failbacks von Azure](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Nächste Schritte

Absolvieren Sie [dieses Tutorial](vmware-azure-tutorial.md), um eine VMware-zu-Azure-Replikation zu ermöglichen.
