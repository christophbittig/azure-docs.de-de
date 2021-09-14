---
title: Architektur der Notfallwiederherstellung für VMware-VMs in Azure Site Recovery - Vorschau
description: Dieser Artikel enthält eine Übersicht über die Komponenten und Architektur, die beim Einrichten der Notfallwiederherstellung von lokalen virtuellen VMware-Computern in Azure mit Azure Site Recovery verwendet werden - Vorschau
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/19/2021
ms.openlocfilehash: 999c2c74c92c6e6106bc6e1e36ff4f62e29e6e1c
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123272785"
---
# <a name="vmware-to-azure-disaster-recovery-architecture---preview"></a>Architektur der Notfallwiederherstellung von VMware zu Azure - Vorschau

Dieser Artikel beschreibt die Architektur und Prozesse, die mithilfe des Diensts [Azure Site Recovery](site-recovery-overview.md) beim Bereitstellen der Replikation für die Notfallwiederherstellung, bei der Ausführung eines Failovers und beim Wiederherstellen von virtuellen VMware-Computern (VMs) zwischen einem lokalen VMware-Standort und Azure verwendet werden - Vorschau

>[!NOTE]
> Stellen Sie sicher, dass Sie einen neuen Recovery Services-Tresor zum Einrichten der Vorschauappliance erstellen. Verwenden Sie keinen vorhandenen Tresor.

Informationen zur Architektur Azure Site Recovery klassischen Architektur finden Sie in [diesem Artikel](vmware-azure-architecture.md).


## <a name="architectural-components"></a>Komponenten der Architektur

Die folgende Tabelle und Grafik enthält eine Übersicht über die Komponenten, die für die Notfallwiederherstellung für VMware VMs/physische Computer in Azure verwendet werden.

[![VMware-zu-Azure-Architektur](./media/vmware-azure-architecture-preview/architecture-preview.png)](./media/vmware-azure-architecture-preview/architecture-preview.png#lightbox)

**Komponente** | **Anforderung** | **Details**
--- | --- | ---
**Azure** | Ein Azure-Abonnement, ein Azure Storage-Konto für die Zwischenspeicherung, einen verwalteten Datenträger und ein Azure-Netzwerk. | Replizierte Daten von lokalen VMs werden in Azure Storage gespeichert. Azure-VMs werden mit den replizierten Daten erstellt, wenn Sie ein Failover von lokalen VMs in Azure ausführen. Für die Azure-VMs wird eine Verbindung mit dem virtuellen Azure-Netzwerk hergestellt, wenn diese erstellt werden.
**Erstellen der Azure Site Recovery-Replikationsappliance** |     Dies ist der grundlegende Baustein der gesamten lokalen Infrastruktur von Azure Site Recovery. <br/><br/> Alle Komponenten in der Appliance sind mit der Replikationsappliance abgestimmt. Dieser Dienst überwacht die gesamte End-to-End-Site Recovery, einschließlich der Überwachung der Integrität geschützter Computer, Datenreplikation, automatischer Updates usw. | Die Appliance hostet verschiedene wichtige Komponenten wie:<br/><br/>**Azure Site Recovery Proxyserver:** Diese Komponente fungiert als Proxykanal zwischen dem Mobilitäts-Agent und den Site Recovery-Diensten in der Cloud. Dadurch wird sichergestellt, dass keine zusätzliche Internetverbindung von Produktionsworkloads erforderlich ist, um Wiederherstellungspunkte zu generieren.<br/><br/>**Entdeckte Elemente:** Diese Komponente erfasst Informationen zu vCenter und ist mit dem Azure Site Recovery-Verwaltungsdienst in der Cloud abgestimmt.<br/><br/>**Server für erneuten Schutz:** Diese Komponente koordiniert die Vorgänge zwischen Azure und lokalen Computern im Zusammenhang mit dem erneuten Schützen und mit Failbacks.<br/><br/>**Azure Site Recovery-Prozessserver:** Diese Komponente wird für das Zwischenspeichern und die Komprimierung von Daten verwendet, bevor diese an Azure gesendet werden. <br/><br/> [Erfahren Sie mehr](switch-replication-appliance-preview.md) über die Replikationsappliance und wie Sie mehrere Replikationsappliances verwenden können.<br/><br/>**Recovery Services-Agent:** Diese Komponente wird zum Konfigurieren/Registrieren bei Site Recovery-Diensten und zum Überwachen der Integrität aller Komponenten verwendet.<br/><br/>**Site Recovery-Anbieter:** Diese Komponente wird verwendet, um den erneuten Schutz zu vereinfachen. Sie unterscheidet zwischen dem erneuten Schützen des alternativen Standorts und dem erneuten Schützen des ursprünglichen Standorts eines Quellcomputers. <br/><br/> **Replikationsdienst:** Diese Komponente wird zum Replizieren von Daten aus dem Quellspeicherort nach Azure verwendet.
**VMware-Server** | VMware-Server werden auf lokalen vSphere ESXi-Servern gehostet. Zum Verwalten der Hosts wird ein vCenter-Server empfohlen. | Sie fügen während der Bereitstellung von Site Recovery VMware-Server in den Recovery Services-Tresor hinzu.
**Replizierte Computer** | Mobility Service wird auf jedem virtuellen VMware-Computer installiert, den Sie replizieren. | Es wird empfohlen, die automatische Installation von Mobility Service zu erlauben. Alternativ können Sie den [Dienst manuell](vmware-physical-mobility-service-overview.md#install-the-mobility-service-using-ui-preview) installieren.


## <a name="set-up-outbound-network-connectivity"></a>Einrichten der ausgehenden Netzwerkkonnektivität

Damit Site Recovery erwartungsgemäß funktioniert, müssen Sie die ausgehende Netzwerkkonnektivität ändern, um Ihrer Umgebung das Replizieren zu ermöglichen.

> [!NOTE]
> Site Recovery unterstützt die Verwendung eines Authentifizierungsproxys zur Steuerung der Netzwerkkonnektivität nicht.

### <a name="outbound-connectivity-for-urls"></a>Ausgehende Konnektivität für URLs

Lassen Sie den Zugriff auf die folgenden URLs zu, wenn Sie einen URL-basierten Firewallproxy zum Steuern der ausgehenden Konnektivität verwenden:

| **URL**                  | **Details**                             |
| ------------------------- | -------------------------------------------|
| portal.azure.com          | Navigieren Sie zum Azure-Portal.              |
| `*.windows.net `<br>`*.msftauth.net`<br>`*.msauth.net`<br>`*.microsoft.com`<br>`*.live.com `<br>`*.office.com ` | So melden Sie sich bei Ihrem Azure-Abonnement an.  |
|`*.microsoftonline.com `|Erstellen von Azure Active Directory-Apps (AD) für die Kommunikation zwischen der Appliance und Azure Site Recovery. |
|management.azure.com |Erstellen von Azure AD-Apps, damit die Appliance mit dem Azure Site Recovery-Dienst kommunizieren kann. |
|`*.services.visualstudio.com `|Laden Sie App-Protokolle hoch, die für die interne Überwachung verwendet werden. |
|`*.vault.azure.net `|Verwalten von Geheimnissen in Azure Key Vault Hinweis: Stellen Sie sicher, dass die replizierten Computer darauf zugreifen können. |
|aka.ms |Zulassen des Zugriffs auf aka-Links Wird für Azure Site Recovery-Applianceupdates verwendet. |
|download.microsoft.com/download |Zulassen von Downloads von Microsoft Download Center |
|`*.servicebus.windows.net `|Kommunikation zwischen der Appliance und dem Azure Site Recovery-Dienst. |
|`*.discoverysrv.windowsazure.com `|Stellen Sie eine Verbindung mit der Azure Site Recovery-Ermittlungsdienst-URL her. |
|`*.hypervrecoverymanager.windowsazure.com `|Herstellen einer Verbindung mit Azure Site Recovery-Microservices-URLs  |
|`*.blob.core.windows.net `|Hochladen von Daten in Azure-Speicher, der zum Erstellen von Zieldatenträgern verwendet wird |
|`*.backup.windowsazure.com `|Schutzdienst-URL: Ein Microservice, der von Azure Site Recovery zum Verarbeiten und Erstellen replizierter Datenträger in Azure verwendet wird |



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

    - VMs kommunizieren mit der lokalen Appliance über den Port HTTPS 443 für eingehenden Datenverkehr, um die Replikationsverwaltung durchzuführen.
    - Die Appliance orchestriert die Replikation mit Azure über Port HTTPS 443 für ausgehenden Datenverkehr.
    - VMs senden Replikationsdaten über den HTTPS-Port 9443 für eingehenden Datenverkehr an den Prozessserver. Dieser Port kann geändert werden.
    - Der Prozessserver empfängt Replikationsdaten, optimiert und verschlüsselt sie und sendet sie über den ausgehenden Port 443 an Azure Storage.
5. Die Replikationsdatenprotokolle werden zunächst in einem Cachespeicherkonto in Azure gespeichert. Diese Protokolle werden dann verarbeitet, und die Daten werden auf einem verwalteten Azure-Datenträger (als *asr-Seed-Datenträger* bezeichnet) gespeichert. Die Wiederherstellungspunkte werden auf diesem Datenträger erstellt.

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
**Aufbewahrungszeitraum des Wiederherstellungspunkts** | Gibt an, wie lange Site Recovery Wiederherstellungspunkte beibehält | 72 Stunden
**App-konsistente Momentaufnahmenhäufigkeit** | Gibt an, wie oft Site Recovery eine App-konsistente Momentaufnahme erstellt. | Alle 4 Stunden

### <a name="managing-replication-policies"></a>Verwalten von Replikationsrichtlinien

Sie können die Standardrichtlinien für die Replikation wie folgt verwalten und ändern:
- Sie können die Einstellungen ändern, während Sie die Replikation aktivieren.
- Sie können eine neue Replikationsrichtlinie erstellen oder bearbeiten, während Sie versuchen, die Replikation zu aktivieren.

### <a name="multi-vm-consistency"></a>Multi-VM-Konsistenz

Wenn Sie virtuelle Computer gemeinsam replizieren möchten und beim Failover über gemeinsame absturz- und App-konsistente Wiederherstellungspunkte verfügen, können Sie sie in einer Replikationsgruppe zusammenfassen. Multi-VM-Konsistenz wirkt sich auf die Leistung einer Workload aus und sollte nur für VM-Workloads verwendet werden, bei denen Konsistenz auf sämtlichen Computern erforderlich ist.



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

1. Sie können ein Failover für einen einzelnen Computer ausführen oder einen Wiederherstellungsplan erstellen, um ein Failover für mehrere virtuelle Computer gleichzeitig auszuführen. Ein Wiederherstellungsplan besitzt gegenüber dem Failover eines einzelnen Computers die folgenden Vorteile:
    - Sie können App-Abhängigkeiten modellieren, indem Sie alle virtuellen Computer in der App in einen einzelnen Wiederherstellungsplan einschließen.
    - Sie können Skripts und Azure-Runbooks hinzufügen und den Vorgang für manuelle Aktionen anhalten.
2. Nachdem das erste Failover ausgelöst wird, committen Sie es, um von der Azure-VM auf die Workload zuzugreifen.
3. Wenn Ihr primärer lokaler Standort wieder verfügbar ist, können Sie sich auf ein Failback vorbereiten. Wenn Sie ein Failback für große Mengen von Datenverkehr benötigen, richten Sie eine neue Azure Site Recovery-Replikationsappliance ein.

    - Phase 1: Schützen Sie die Azure-VMs erneut, sodass sie die Replikation von Azure zurück zu den lokalen VMware-VMs durchführen.
    - Phase 2: Führen Sie ein Failover zum lokalen Standort aus.
    - Phase 3: Nachdem für Workloads ein Failback ausgeführt wurde, aktivieren Sie erneut die Replikation für die lokalen VMs.

## <a name="next-steps"></a>Nächste Schritte

Absolvieren Sie [dieses Tutorial](vmware-azure-tutorial.md), um eine VMware-zu-Azure-Replikation zu ermöglichen.
