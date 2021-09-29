---
title: Behandeln von Problemen bei Bewertungen in Azure Migrate
description: Hilfreiche Informationen zu Bewertungen in Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 07/28/2021
ms.openlocfilehash: eca62eccca6f53935f29a16bca0afc382896212c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124810368"
---
# <a name="troubleshoot-assessment"></a>Behandeln von Problemen bei der Bewertung

Dieser Artikel hilft Ihnen bei der Problembehandlung bei der Bewertungs- und Abhängigkeitsvisualisierung mit dem [Azure Migrate-Tool für die Ermittlung und Bewertung](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool).

## <a name="assessment-readiness-issues"></a>Probleme mit der Bewertungsbereitschaft

Diese Tabelle enthält Hilfe zum Beheben der folgenden Probleme bei der Bewertungsbereitschaft.

**Problem** | **Behebung**
--- | ---
Nicht unterstützter Starttyp | Azure unterstützt keine virtuellen Computer mit dem Starttyp „EFI“. Vor einer Migration muss der Starttyp in „BIOS“ konvertiert werden. <br/><br/>Sie können die Azure Migrate-Servermigration verwenden, um die Migration solcher virtuellen Computer zu verarbeiten. Sie ändert den Starttyp des virtuellen Computers im Rahmen der Migration in „BIOS“.
Bedingt unterstütztes Windows-Betriebssystem | Der Unterstützungszeitraum für das Betriebssystem ist abgelaufen. Für die [Unterstützung in Azure](/troubleshoot/azure/virtual-machines/server-software-support) wird eine benutzerdefinierte Supportvereinbarung benötigt. Ziehen Sie vor der Migration zu Azure ein Upgrade in Erwägung. Lesen Sie die Informationen zum [Vorbereiten von Servern auf Windows Server 2003-Computern](prepare-windows-server-2003-migration.md) für die Migration zu Azure.
Nicht unterstütztes Windows-Betriebssystem | Azure unterstützt nur [ausgewählte Windows-Betriebssystemversionen](/troubleshoot/azure/virtual-machines/server-software-support). Ziehen Sie vor der Migration zu Azure ein Upgrade des Servers in Erwägung.
Bedingt unterstütztes Linux-Betriebssystem | Azure unterstützt nur [ausgewählte Linux-Betriebssystemversionen](../virtual-machines/linux/endorsed-distros.md). Ziehen Sie vor der Migration zu Azure ein Upgrade des Servers in Erwägung. Weitere Informationen finden Sie auf [dieser Website](#linux-vms-are-conditionally-ready-in-an-azure-vm-assessment).
Nicht unterstütztes Linux-Betriebssystem | Der Server startet möglicherweise in Azure, aber Azure bietet keine Betriebssystemunterstützung. Ziehen Sie vor der Migration zu Azure ein Upgrade auf eine [unterstützte Linux-Version](../virtual-machines/linux/endorsed-distros.md) in Erwägung.
Unbekanntes Betriebssystem | Das Betriebssystem der VM wurde in vCenter Server als „Sonstige“ angegeben. Dieses Verhalten hindert Azure Migrate daran, die Azure-Bereitschaft der VM zu überprüfen. Stellen Sie sicher, dass das Betriebssystem von Azure [unterstützt](./migrate-support-matrix-vmware-migration.md#azure-vm-requirements) wird, bevor Sie den Server migrieren.
Nicht unterstützte Bitversion | Virtuelle Computer mit einem 32-Bit-Betriebssystem starten zwar unter Umständen in Azure, es empfiehlt sich jedoch, vor der Migration zu Azure ein Upgrade auf 64-Bit durchzuführen.
Erfordert ein Microsoft Visual Studio-Abonnement. | Auf dem Server wird ein Windows-Clientbetriebssystem ausgeführt, das nur mithilfe eines Visual Studio-Abonnements unterstützt wird.
Kein virtueller Computer für erforderliche Speicherleistung gefunden. | Die erforderliche Speicherleistung (Ein-/Ausgabevorgänge pro Sekunde (IOPS) und Durchsatz) für den Server überschreitet die von virtuellen Azure-Computern unterstützte Leistung. Reduzieren Sie vor der Migration die Speicheranforderungen für den Server.
Kein virtueller Computer für erforderliche Netzwerkleistung gefunden. | Die erforderliche Netzwerkleistung (eingehend bzw. ausgehend) für den Server überschreitet die von virtuellen Azure-Computern unterstützte Leistung. Reduzieren Sie die Netzwerkanforderungen für den Server.
Kein virtueller Computer am angegebenen Speicherort gefunden. | Geben Sie vor der Migration einen anderen Zielort an.
Mindestens ein Datenträger ist ungeeignet. | Mindestens ein mit der VM verbundener Datenträger erfüllt nicht die Azure-Anforderungen.<br/><br/> Die Funktion „Azure Migrate: Ermittlung und Bewertung“ bewertet die Datenträger basierend auf den Datenträgergrenzwerten für Disk Ultra-Datenträger (64 TB).<br/><br/> Stellen Sie für jeden an den virtuellen Computer angeschlossenen Datenträger sicher, dass die Größe des Datenträgers kleiner als 64 TB (von SSD Ultra-Datenträgern unterstützt) ist.<br/><br/> Wenn dies nicht der Fall ist, reduzieren Sie die Datenträgergröße vor der Migration zu Azure, oder verwenden Sie in Azure mehrere Datenträger, und [kombinieren Sie sie in Stripesets](../virtual-machines/premium-storage-performance.md#disk-striping), um höhere Speichergrenzwerte zu erzielen. Stellen Sie sicher, dass die von jedem Datenträger benötigte Leistung (IOPS und Durchsatz) von [verwalteten Azure-Datenträgern virtueller Computer](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits) unterstützt wird.
Mindestens ein Netzwerkadapter ist ungeeignet. | Entfernen Sie nicht verwendete Netzwerkadapter vor der Migration vom Server.
Anzahl der Datenträger überschreitet den Grenzwert | Entfernen Sie nicht verwendete Datenträger vor der Migration vom Server.
Datenträgergröße überschreitet den Grenzwert | Die Funktion „Azure Migrate: Ermittlung und Bewertung“ unterstützt Datenträger mit einer Größe von bis zu 64 TB (Disk Ultra-Datenträger). Reduzieren Sie die Datenträgergröße vor der Migration auf weniger als 64 TB, oder verwenden Sie mehrere Datenträger in Azure, und [kombinieren Sie sie in Stripesets](../virtual-machines/premium-storage-performance.md#disk-striping), um höhere Speichergrenzwerte zu erzielen.
Datenträger am angegebenen Speicherort nicht verfügbar | Stellen Sie vor der Migration sicher, dass sich der Datenträger am Zielspeicherort befindet.
Datenträger für die angegebene Redundanz nicht verfügbar | Der Datenträger muss den in den Bewertungseinstellungen definierten Redundanzspeichertyp verwenden (standardmäßig LRS).
Datenträgereignung konnte aufgrund eines internen Fehlers nicht ermittelt werden. | Versuchen Sie, eine neue Bewertung für die Gruppe zu erstellen.
Kein virtueller Computer gefunden, der die Kern- und Arbeitsspeicheranforderungen erfüllt | Azure konnte keinen geeigneten VM-Typ finden. Reduzieren Sie vor der Migration den Arbeitsspeicher und die Anzahl der Kerne auf dem lokalen Server.
VM-Eignung konnte aufgrund eines internen Fehlers nicht ermittelt werden. | Versuchen Sie, eine neue Bewertung für die Gruppe zu erstellen.
Eignung konnte für mindestens einen Datenträger aufgrund eines internen Fehlers nicht ermittelt werden. | Versuchen Sie, eine neue Bewertung für die Gruppe zu erstellen.
Eignung konnte für mindestens einen Netzwerkadapter aufgrund eines internen Fehlers nicht ermittelt werden. | Versuchen Sie, eine neue Bewertung für die Gruppe zu erstellen.
Es wurde keine VM-Größe für die Angebot/Währung/reservierte Instanz (RI) gefunden. | Der Server ist als „Nicht geeignet“ gekennzeichnet, weil die VM-Größe für die ausgewählte Kombination aus RI, Angebot und Währung nicht gefunden wurde. Bearbeiten Sie die Bewertungseigenschaften, um die gültigen Kombinationen auszuwählen und die Bewertung neu zu berechnen. 
Internetprotokoll mit bedingter Bereitschaft | Nur verfügbar für Bewertungen vom Typ „Azure VMware Solution“. Azure VMware Solution unterstützt keine IPv6-Internetadressen. Wenden Sie sich an das Team für Azure VMware Solution, um eine Anleitung zur Abhilfe zu erhalten, falls Ihr Server mit IPv6 erkannt wird.

## <a name="suggested-migration-tool-in-an-import-based-azure-vmware-solution-assessment-is-unknown"></a>Empfohlenes Migrationstool in einer importbasierten Azure VMware Solution Bewertung ist unbekannt.

Für Server, die über eine CSV-Datei importiert wurden, ist das Standardmigrationstool in einer Azure VMware Solution-Bewertung unbekannt. Für Server in einer VMware-Umgebung sollten Sie die VMware HCX-Lösung (Hybrid Cloud Extension) verwenden. [Weitere Informationen](../azure-vmware/configure-vmware-hcx.md)

## <a name="linux-vms-are-conditionally-ready-in-an-azure-vm-assessment"></a>Linux-VMs sind in einer Azure-VM-Bewertung „bedingt bereit“

Im Fall von VMware- und Hyper-V-VMs werden virtuelle Linux-Computer von der Azure-VM-Bewertung aufgrund einer bekannten Lücke als „bedingt bereit“ gekennzeichnet. 

- Die Lücke verhindert die Erkennung der Nebenversion des Linux-Betriebssystems, das auf den lokalen virtuellen Computern installiert ist.
- Beispielsweise erkennt die Azure-VM-Bewertung bei RHEL 6.10 derzeit nur RHEL 6 als Betriebssystemversion. Dies liegt daran, dass die vCenter Server-Instanz und der Hyper-V-Host die Kernel-Version für Linux-VM-Betriebssysteme nicht bereitstellen.
- Da Azure nur bestimmte Versionen von Linux unterstützt, werden die virtuellen Linux-Computer im Rahmen der Azure-VM-Bewertung derzeit als „bedingt bereit“ markiert.
- Sie können ermitteln, ob das Linux-Betriebssystem des lokalen virtuellen Computers für Azure geeignet ist. Informationen hierzu finden Sie unter [Linux-Unterstützung von Azure](../virtual-machines/linux/endorsed-distros.md).
- Nachdem Sie die unterstützte Distribution überprüft haben, können Sie diese Warnung ignorieren.

Diese Lücke kann behoben werden, indem Sie [Anwendungsermittlung-](./how-to-discover-applications.md) auf den VMware-VMs aktivieren. Bei einer Azure-VM-Bewertung wird das von der VM erkannte Betriebssystem mit den bereitgestellten Gastanmeldeinformationen verwendet. Mit diesen Betriebssystemdaten werden die richtigen Betriebssysteminformationen im Fall von virtuellen Windows- und Linux-Computern identifiziert.

## <a name="operating-system-version-not-available"></a>Betriebssystemversion nicht verfügbar

Bei physischen Servern sollten Informationen zur Nebenversion des Betriebssystems verfügbar sein. Wenn dies nicht der Fall ist, wenden Sie sich an den Microsoft-Support. Für Server in einer VMware-Umgebung verwendet Azure Migrate die in vCenter Server für die VM angegebenen Betriebssysteminformationen. Allerdings gibt vCenter Server nicht die Nebenversion für Betriebssysteme an. Zum Ermitteln der Nebenversion richten Sie die [Anwendungsermittlung](./how-to-discover-applications.md) ein. Bei Hyper-V-VMs wird die Ermittlung der Betriebssystem-Nebenversion nicht unterstützt. 

## <a name="azure-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Azure-SKUs größer als lokal in einer Azure-VM-Bewertung

Je nach Art der Bewertung empfiehlt eine Azure-VM-Bewertung möglicherweise Azure-VM-SKUs mit mehr Kernen und Arbeitsspeicher als bei der aktuellen lokalen Zuordnung:

- Die Empfehlung für VM-SKUs richtet sich nach den Bewertungseigenschaften.
- Diese Empfehlung hängt von der Art der Bewertung ab, die Sie in einer Azure VM-Bewertung ausführen: Es gibt zwei Arten: **Leistungsbasiert** oder **Wie lokal**.
- Bei Bewertungen vom Typ „Leistungsbasiert“ berücksichtigt die Azure-VM-Bewertung die Nutzungsdaten der lokalen VMs (CPU-, Speicher-, Datenträger- und Netzwerkauslastung), um die richtige Ziel-VM-SKU für Ihre lokalen virtuellen Computer zu bestimmen. Außerdem wird bei der Bestimmung der effektiven Auslastung ein Komfortfaktor hinzugefügt.
- Bei der lokalen Dimensionierung werden Leistungsdaten nicht berücksichtigt, und die Ziel-SKU wird auf der Grundlage der lokalen Zuordnung empfohlen.

Sehen wir uns eine Beispielempfehlung an.

Wir haben einen lokalen virtuellen Computer mit vier Kernen und 8 GB Arbeitsspeicher, mit einer CPU- und Speicherauslastung von jeweils 50 % und einem angegebenen Komfortfaktor von 1,3.

- Beim Bewertungstyp **Wie lokal** wird eine Azure-VM-SKU mit vier Kernen und 8 GB Arbeitsspeicher empfohlen.
- Bei der **leistungsbasierten** Bewertung würde auf Basis der effektiven CPU- und Speicherauslastung (50 % von 4 Kernen * 1,3 = 2,6 Kerne und 50 % von 8 GB Arbeitsspeicher * 1,3 = 5,3 GB Arbeitsspeicher) die günstigste VM-SKU mit vier Kernen (nächste unterstützte Kernanzahl) und 8 GB Arbeitsspeichergröße (nächste unterstützte Arbeitsspeichergröße) empfohlen.
- Weitere Informationen zur Dimensionierung bei Bewertungen finden Sie [hier](concepts-assessment-calculation.md#types-of-assessments).

## <a name="why-is-the-recommended-azure-disk-sku-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Warum sind die empfohlenen Azure-Datenträger-SKUs größer als lokal in einer Azure-VM-Bewertung?

Die Azure-VM-Bewertung empfiehlt möglicherweise je nach Art der Bewertung einen größeren Datenträger:

- Die Größenanpassung der Datenträger hängt von zwei Bewertungseigenschaften ab: dem Größenkriterium und dem Speichertyp.
- Wenn das Dimensionierungskriterium **Leistungsbasiert** und der Speichertyp **Automatisch** ist, werden bei der Ermittlung des Zieldatenträgertyps (HDD Standard, SSD Standard, Premium oder Disk Ultra) die IOPS- und Durchsatzwerte des Datenträgers berücksichtigt. Eine Datenträger-SKU dieses Datenträgertyps wird dann unter Berücksichtigung der Größenanforderungen des lokalen Datenträgers empfohlen.
- Wenn das Dimensionierungskriterium **Leistungsbasiert** ist und der Speichertyp **Premium** lautet, wird eine Premium-Datenträger-SKU in Azure basierend auf den IOPS, dem Durchsatz und den Größenanforderungen des lokalen Datenträgers empfohlen. Die gleiche Logik wird für die Datenträgerdimensionierung verwendet, wenn das Dimensionierungskriterium **Wie lokal** und der Speichertyp **HDD Standard**, **SSD Standard**, **Premium** oder **Disk Ultra** ist.

Das ist zum Beispiel der Fall, wenn Sie einen lokalen Datenträger mit 32 GB Speicher haben, der aber einen aggregierten Lese- und Schreibwert von 800 IOPS aufweist. Die Azure-VM-Bewertung empfiehlt aufgrund der höheren IOPS-Anforderungen einen Premium-Datenträger. Außerdem wird eine Datenträger-SKU empfohlen, die die erforderlichen IOPS und die erforderliche Größe bereitstellen kann. Die nächstliegende Übereinstimmung in diesem Beispiel wäre P15 (256 GB, 1100 IOPS). Für den lokalen Datenträger ist also nur eine Größe von 32 GB erforderlich, von der Azure-VM-Bewertung wurde jedoch aufgrund der hohen IOPS-Anforderungen des lokalen Datenträgers ein größerer Datenträger empfohlen.

## <a name="why-is-performance-data-missing-for-some-or-all-vms-in-my-assessment-report"></a>Warum fehlen in meinem Bewertungsbericht die Leistungsdaten für einige oder alle VMs?

Für eine Bewertung vom Typ **Leistungsbasiert** enthält der Bewertungsbericht die Angabe „PercentageOfCoresUtilizedMissing“ oder „PercentageOfMemoryUtilizedMissing“, wenn die Azure Migrate-Appliance keine Leistungsdaten für die lokalen virtuellen Computer sammeln kann. Überprüfen Sie Folgendes:

- Sind die VMs für den Zeitraum, in dem Sie die Bewertung erstellen, eingeschaltet?
- Falls nur Arbeitsspeicher-Leistungsindikatoren fehlen und Sie versuchen, virtuelle Hyper-V-Computer zu bewerten, überprüfen Sie, ob auf diesen virtuellen Computern dynamischer Arbeitsspeicher aktiviert ist. Die Azure Migrate-Appliance kann derzeit aufgrund eines bekannten Problems keine Arbeitsspeicherauslastung für virtuelle Computer dieser Art erfassen.
- Wenn alle Leistungsindikatoren fehlen, stellen Sie sicher, dass die Portzugriffsanforderungen für die Bewertung erfüllt sind. Informieren Sie sich weiter über die Portzugriffsanforderungen für die Bewertung von [VMware-](./migrate-support-matrix-vmware.md#port-access-requirements) und [Hyper-V-Servern](./migrate-support-matrix-hyper-v.md#port-access) sowie [physischen](./migrate-support-matrix-physical.md#port-access) Servern.
Falls einer der Leistungsindikatoren fehlt, greift das Azure Migrate-Tool für die Ermittlung und Bewertung auf die lokalen zugeordneten Kerne bzw. auf den lokalen zugeordneten Arbeitsspeicher zurück und empfiehlt eine entsprechende VM-Größe.

## <a name="why-is-performance-data-missing-for-some-or-all-servers-in-my-azure-vm-or-azure-vmware-solution-assessment-report"></a>Warum fehlen Leistungsdaten für einige oder alle Server in meinem Azure-VM- oder Azure VMware Solution-Bewertungsbericht?

Für eine Bewertung vom Typ **Leistungsbasiert** enthält der Bewertungsbericht die Angabe „PercentageOfCoresUtilizedMissing“ oder „PercentageOfMemoryUtilizedMissing“, wenn die Azure Migrate-Appliance keine Leistungsdaten für die lokalen Server sammeln kann. Überprüfen Sie Folgendes:

- Sind die Server für den Zeitraum, in dem Sie die Bewertung erstellen, eingeschaltet?
- Fehlen nur Leistungsindikatoren für den Arbeitsspeicher? Versuchen Sie, die Server in einer Hyper-V-Umgebung zu bewerten? Aktivieren Sie in diesem Szenario dynamischen Arbeitsspeicher auf den Servern, und führen Sie die erneute Berechnung der Bewertung durch, um die neuesten Änderungen widerzuspiegeln. Die Appliance kann Werte für die Arbeitsspeicherauslastung für Server in Hyper-V-Umgebungen nur dann sammeln, wenn der dynamische Arbeitsspeicher für den Server aktiviert ist.
- Falls alle Leistungsindikatoren fehlen, sollten Sie sicherstellen, dass ausgehende Verbindungen über Port 443 (HTTPS) zulässig sind.

    > [!Note]
    > Falls einer der Leistungsindikatoren fehlt, greift das Azure Migrate-Tool für die Ermittlung und Bewertung auf die lokalen zugeordneten Kerne bzw. auf den lokalen zugeordneten Arbeitsspeicher zurück und empfiehlt eine entsprechende VM-Größe.

## <a name="why-is-performance-data-missing-for-some-or-all-sql-instances-or-databases-in-my-azure-sql-assessment"></a>Warum fehlen in meiner Azure SQL-Bewertung Leistungsdaten für einige oder alle SQL-Instanzen oder -Datenbanken?

Überprüfen Sie Folgendes, um sicherzustellen, dass Leistungsdaten erfasst werden:

- Sind die SQL Server-Instanzen für den Zeitraum, in dem Sie die Bewertung erstellen, eingeschaltet?
- Weist der SQL-Agent in Azure Migrate den Verbindungsstatus **Verbunden** auf? Überprüfen Sie auch den letzten Heartbeat. 
- Lautet der Azure Migrate-Verbindungsstatus für alle SQL-Instanzen im Bereich der ermittelten SQL-Instanz **Verbunden**?
- Falls alle Leistungsindikatoren fehlen, sollten Sie sicherstellen, dass ausgehende Verbindungen über Port 443 (HTTPS) zulässig sind.

Wenn einer der Leistungsindikatoren fehlt, empfiehlt die Azure SQL-Bewertung die kleinste Azure SQL-Konfiguration für die Instanz bzw. Datenbank.

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>Weshalb weist meine Bewertung eine niedrige Zuverlässigkeitsstufe auf?

Die Zuverlässigkeitsstufe wird für **leistungsbasierte** Bewertungen anhand des Prozentsatzes von [verfügbaren Datenpunkten](./concepts-assessment-calculation.md#ratings) berechnet, die zum Berechnen der Bewertung benötigt werden. Eine Bewertung kann aus folgenden Gründen eine niedrige Zuverlässigkeitsstufe erhalten:

- Sie haben für den Zeitraum, für den Sie die Bewertung erstellen, kein Profil der Umgebung erstellt. Wenn Sie beispielsweise eine Bewertung mit einer auf eine Woche festgelegten Leistungsdauer erstellen, müssen Sie nach dem Start der Ermittlung mindestens eine Woche warten, damit alle Datenpunkte erfasst werden können. Falls Sie nicht so lange warten können, ändern Sie die Leistungsdauer in einen kürzeren Zeitraum und führen eine Neuberechnung der Bewertung durch.
- Die Bewertung kann die Leistungsdaten für einige oder alle Server im Bewertungszeitraum nicht erfassen. Für eine hohe Zuverlässigkeitsstufe stellen Sie Folgendes sicher: 
    - Die Server sind für die Dauer der Bewertung eingeschaltet.
    - Ausgehende Verbindungen am Port 443 sind zugelassen.
    - Für Hyper-V-Server ist der dynamische Arbeitsspeicher aktiviert.
    - Der Verbindungsstatus von Agents in Azure Migrate ist „Verbunden“. Überprüfen Sie auch den letzten Heartbeat.
    - In Azure SQL-Bewertungen lautet der Azure Migrate-Verbindungsstatus für alle SQL-Instanzen im Bereich der ermittelten SQL-Instanz „Verbunden“.

    Führen Sie die erneute Berechnung der Bewertung mit der Option Neu berechnen durch, um die neuesten Änderungen an der Zuverlässigkeitsstufe widerzuspiegeln.

- Für Azure-VM- und Azure VMware Solution-Bewertungen: Nach dem Start der Ermittlung wurden einige Server erstellt. Beispiel: Angenommen, Sie erstellen eine Bewertung für den Leistungsverlauf des letzten Monats, einige Server in der Umgebung wurden jedoch erst letzte Woche erstellt. In diesem Fall stehen für die gesamte Dauer keine Leistungsdaten für die neuen Server zur Verfügung, und die Zuverlässigkeitsstufe wäre gering. [Weitere Informationen](./concepts-assessment-calculation.md#confidence-ratings-performance-based)
- Bei Azure SQL-Bewertungen wurden einige SQL-Instanzen oder -Datenbanken nach dem Start der Ermittlung erstellt. Beispiel: Angenommen, Sie erstellen eine Bewertung für den Leistungsverlauf des letzten Monats, einige SQL-Instanzen oder -Datenbanken in der Umgebung wurden jedoch erst letzte Woche erstellt. In diesem Fall stehen für die gesamte Dauer keine Leistungsdaten für die neuen Server zur Verfügung, und die Zuverlässigkeitsstufe wäre gering. [Weitere Informationen](./concepts-azure-sql-assessment-calculation.md#confidence-ratings)

## <a name="is-the-operating-system-license-included-in-an-azure-vm-assessment"></a>Ist die Betriebssystemlizenz in einer Azure-VM-Bewertung inbegriffen?

Eine Azure-VM-Bewertung berücksichtigt zurzeit nur die Kosten einer Betriebssystemlizenz für Windows-Computer. Die Lizenzkosten für Linux-Server werden derzeit nicht berücksichtigt.

## <a name="how-does-performance-based-sizing-work-in-an-azure-vm-assessment"></a>Wie funktioniert die leistungsbasierte Größenanpassung in einer Azure-VM-Bewertung?

Eine Azure-VM-Bewertung sammelt fortlaufend Leistungsdaten von lokalen Servern und nutzt diese Daten, um die VM-SKU und die Datenträger-SKU in Azure zu empfehlen. [Erfahren Sie](concepts-assessment-calculation.md#calculate-sizing-performance-based), wie leistungsbasierten Daten gesammelt werden.

## <a name="can-i-migrate-my-disks-to-an-ultra-disk-by-using-azure-migrate"></a>Kann ich meine Datenträger mithilfe von Azure Migrate zu einem Disk Ultra-Datenträger migrieren?

Nein. Derzeit unterstützen sowohl Azure Migrate als auch Azure Site Recovery keine Migration zu Disk Ultra-Datenträgern. Die Schritte zum Bereitstellen eines Disk Ultra-Datenträgers finden Sie [auf dieser Website](../virtual-machines/disks-enable-ultra-ssd.md?tabs=azure-portal#deploy-an-ultra-disk).

## <a name="why-are-the-provisioned-iops-and-throughput-in-my-ultra-disk-more-than-my-on-premises-iops-and-throughput"></a>Warum sind die bereitgestellten IOPS und der Durchsatz meines Disk Ultra-Datenträgers höher als die IOPS und der Durchsatz in meiner lokalen Umgebung?

Gemäß der [offiziellen Preisseite](https://azure.microsoft.com/pricing/details/managed-disks/) wird Disk Ultra auf Grundlage der bereitgestellten Größe und IOPS sowie des bereitgestellten Durchsatzes abgerechnet. Wenn Sie beispielsweise einen 200 GiB Disk Ultra-Datenträger mit 20.000 IOPS und 1.000 MB/Sekunde bereitgestellt und nach 20 Stunden gelöscht haben, wird er dem Datenträgergrößenangebot von 256 GiB zugeordnet. Ihnen werden 256 GiB, 20.000 IOPS und 1.000 MB/Sekunde für 20 Stunden in Rechnung gestellt.

Bereitzustellende IOPS = (ermittelter Durchsatz) x 1024 : 256

## <a name="does-the-ultra-disk-recommendation-consider-latency"></a>Wird bei der Disk Ultra-Empfehlung Latenz berücksichtigt?

Nein. Derzeit werden nur die Datenträgergröße, der Gesamtdurchsatz und IOPS-Gesamtwert für die Größen- und Kostenkalkulation verwendet.

## <a name="i-can-see-m-series-supports-ultra-disk-but-in-my-assessment-where-ultra-disk-was-recommended-it-says-no-vm-found-for-this-location"></a>Ich sehe, dass die M-Serie Disk Ultra-Datenträger unterstützt, aber in meiner Bewertung, in der Disk Ultra-Datenträger empfohlen wurden, heißt es „Keine VM für diese Standort gefunden“?

Dieses Ergebnis ist möglich, da nicht alle VM-Größen, die Disk Ultra unterstützen, in allen Regionen mit Unterstützung von Disk Ultra angeboten werden. Ändern Sie die Zielbewertungsregion, um die VM-Größe für diesen Server abzurufen.

## <a name="why-is-my-assessment-showing-a-warning-that-it-was-created-with-an-invalid-combination-of-reserved-instances-vm-uptime-and-discount-"></a>Warum zeigt meine Bewertung eine Warnung an, dass sie mit einer ungültigen Kombination von reservierten Instanzen, VM-Betriebszeit und Rabatt (%) erstellt wurde?

Wenn Sie **Reservierte Instanzen** auswählen, sind die **Rabatt (%)** - und **VM-Betriebszeit**-Eigenschaften nicht anwendbar. Da Ihre Bewertung mit einer ungültigen Kombination dieser Eigenschaften erstellt wurde, sind die Schaltflächen **Bearbeiten** und **Neu berechnen** deaktiviert. Erstellen Sie eine neue Bewertung. [Weitere Informationen](./concepts-assessment-calculation.md#whats-an-assessment)

## <a name="i-dont-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>Ich sehe keine Leistungsdaten für einige Netzwerkadapter auf meinen physischen Servern.

Dieses Problem kann vorkommen, wenn die Hyper-V-Virtualisierung auf dem physischen Server aktiviert ist. Auf diesen Servern werden aufgrund einer Produktlücke von Azure Migrate derzeit sowohl der physische als auch der virtuelle Netzwerkadapter ermittelt. Der Netzwerkdurchsatz wird nur auf den erkannten virtuellen Netzwerkadaptern aufgezeichnet.

## <a name="the-recommended-azure-vm-sku-for-my-physical-server-is-oversized"></a>Die empfohlene Azure-VM-SKU für meinen physischen Server ist überdimensioniert

Dieses Problem kann vorkommen, wenn die Hyper-V-Virtualisierung auf dem physischen Server aktiviert ist. Auf diesen Servern werden von Azure Migrate derzeit sowohl der physische als auch der virtuelle Netzwerkadapter ermittelt. Daher ist die Anzahl der gefundenen Netzwerkadapter höher als die tatsächliche Anzahl. Die Azure-VM-Bewertung wählt eine Azure-VM, die die erforderliche Anzahl von Netzwerkadaptern unterstützen kann, was möglicherweise zu einer überdimensionierten VM führen kann. [Weitere Informationen](./concepts-assessment-calculation.md#calculating-sizing) über den Einfluss der Anzahl der Netzwerkadapter auf die Dimensionierung Diese Produktlücke wird in Zukunft behoben.

## <a name="the-readiness-category-is-marked-not-ready-for-my-physical-server"></a>Die Bereitschaftskategorie für meinen physischen Server ist mit „Nicht bereit“ angegeben.

Die Bereitschaftskategorie wird im Falle eines physischen Servers mit aktivierter Hyper-V-Virtualisierung möglicherweise fälschlicherweise als „Nicht bereit“ gekennzeichnet. Auf diesen Servern werden aufgrund einer Produktlücke von Azure Migrate derzeit sowohl der physische als auch der virtuelle Adapter ermittelt. Daher ist die Anzahl der gefundenen Netzwerkadapter höher als die tatsächliche Anzahl. Sowohl bei **lokalen** als auch bei **leistungsbezogenen** Bewertungen wählt die Azure-VM-Bewertung eine Azure-VM aus, die die erforderliche Anzahl von Netzwerkadaptern unterstützen kann. Wenn festgestellt wird, dass die Anzahl der Netzwerkadapter höher als die maximale Anzahl von NICs ist, die von Azure VMs unterstützt werden (32), wird der Server als „Nicht bereit“ gekennzeichnet. [Weitere Informationen](./concepts-assessment-calculation.md#calculating-sizing) über den Einfluss der Anzahl der NICs auf die Dimensionierung

## <a name="the-number-of-discovered-nics-is-higher-than-actual-for-physical-servers"></a>Die Anzahl der ermittelten NICs ist höher als die tatsächliche Anzahl der physischen Server.

Dieses Problem kann vorkommen, wenn die Hyper-V-Virtualisierung auf dem physischen Server aktiviert ist. Auf diesen Servern werden von Azure Migrate derzeit sowohl der physische als auch der virtuelle Adapter ermittelt. Daher ist die Anzahl der gefundenen NICs höher als die tatsächliche Anzahl.

## <a name="capture-network-traffic"></a>Erfassen des Netzwerkdatenverkehrs

So sammeln Sie Protokolle zum Netzwerkdatenverkehr:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Drücken Sie F12, um die Entwicklertools zu starten. Deaktivieren Sie ggf. die Einstellung **Einträge beim Navigieren löschen**.
1. Wählen Sie die Registerkarte **Netzwerk** aus, und starten Sie die Erfassung von Netzwerkdatenverkehr:
   - Wählen Sie in Chrome die Option **Protokoll speichern**. Die Aufzeichnung sollte automatisch gestartet werden. Ein roter Kreis gibt an, dass der Datenverkehr erfasst wird. Wenn der rote Kreis nicht angezeigt wird, wählen Sie zum Starten den schwarzen Kreis aus.
   - In Microsoft Edge und Internet Explorer sollte die Aufzeichnung automatisch gestartet werden. Ist dies nicht der Fall, wählen Sie die grüne Wiedergabeschaltfläche aus.
1. Versuchen Sie, den Fehler zu reproduzieren.
1. Nachdem der Fehler während der Aufzeichnung aufgetreten ist, beenden Sie die Aufzeichnung, und speichern Sie eine Kopie der aufgezeichneten Aktivität:
   - Klicken Sie in Chrome mit der rechten Maustaste, und wählen Sie **Als HAR mit Inhalt speichern** aus. Durch diese Aktion werden die Protokolle komprimiert und als HAR-Datei exportiert.
   - Wählen Sie in Microsoft Edge oder Internet Explorer das Symbol **Aufgezeichneten Datenverkehr exportieren** aus. Durch diese Aktion werden die Protokolle komprimiert und exportiert.
1. Wählen Sie die Registerkarte **Konsole** aus, um sie auf Warnungen oder Fehler zu überprüfen. So speichern Sie das Konsolenprotokoll:
   - Klicken Sie in Chrome mit der rechten Maustaste auf eine beliebige Stelle im Konsolenprotokoll. Wählen Sie **Speichern unter** aus, um das Protokoll zu exportieren und zu komprimieren.
   - Klicken Sie in Microsoft Edge oder Internet Explorer mit der rechten Maustaste auf die Fehler, und wählen Sie **Alle kopieren** aus.
1. Schließen Sie die Entwicklertools.

## <a name="where-is-the-operating-system-data-in-my-assessment-discovered-from"></a>Woher werden die Betriebssystemdaten in meiner Bewertung ermittelt?

- Bei VMware-VMs handelt es sich standardmäßig um die vom vCenter Server bereitgestellten Betriebssystemdaten.
   - Bei VMware-Linux-VMs werden die Betriebssystemdetails von der Gast-VM abgerufen, wenn die Anwendungsermittlung aktiviert ist. Navigieren Sie zur Ansicht **Ermittelte Server**, und fahren Sie mit der Maus über den Wert in der Spalte **Betriebssystem**, um zu überprüfen, welche Betriebssystemdetails in der Bewertung enthalten sind. In dem Text, der angezeigt wird, können Sie sehen, ob die Betriebssystemdaten von der vCenter Server-Instanz oder der Gast-VM unter Verwendung der VM-Anmeldeinformationen gesammelt werden.
   - Bei virtuellen Windows-Computern werden die Betriebssystemdetails immer von der vCenter Server-Instanz abgerufen.
- Bei Hyper-V-VMS werden die Betriebssystemdaten vom Hyper-V-Host erfasst.
- Für physische Server werden sie vom Server abgerufen.

## <a name="common-web-apps-discovery-errors"></a>Häufige Fehler bei der Ermittlung von Web-Apps

Azure Migrate bietet die Möglichkeit, ermittelte ASP.NET-Web-Apps für die Migration zu Azure App Service mithilfe des Tools „Azure Migrate: Ermittlung und Bewertung“ zu bewerten. Informationen zu den ersten Schritten finden Sie im Tutorial zur [Bewertung](tutorial-assess-webapps.md).

Typische App Service-Bewertungsfehler sind in der Tabelle zusammengefasst.

| **Fehler** | **Ursache** | **Empfohlene Maßnahme** |
|--|--|--|
|**Überprüfung des Anwendungspools**|Die IIS-Website verwendet die folgenden Anwendungspools: {0}.|Azure App Service unterstützt pro App Service-Anwendung nicht mehr als eine Anwendungspoolkonfiguration. Verschieben Sie die Workloads in einen einzelnen Anwendungspool, und entfernen Sie andere Anwendungspools.|
|**Überprüfung der Anwendungspoolidentität**|Der Anwendungspool der Website wird als nicht unterstützter Benutzeridentitätstyp ausgeführt: {0}.|App Service unterstützt für Anwendungspools nicht die Identitätstypen LocalSystem oder SpecificUser. Legen Sie fest, dass der Anwendungspool als ApplicationPoolIdentity ausgeführt wird.|
|**Autorisierungsprüfung**|Die folgenden nicht unterstützten Authentifizierungstypen wurden gefunden: {0}.|Die von App Service unterstützten Authentifizierungstypen und Konfigurationen unterscheiden sich von denen von lokalem IIS. Deaktivieren Sie die nicht unterstützten Authentifizierungstypen für die Website. Nach Abschluss der Migration kann die Website mit einem der von App Service unterstützten Authentifizierungstypen konfiguriert werden.|
|**Autorisierungsprüfung unbekannt**|Aktivierte Authentifizierungstypen können nicht für die gesamte Websitekonfiguration bestimmt werden.|Authentifizierungstypen können nicht bestimmt werden. Beheben Sie alle Konfigurationsfehler, und vergewissern Sie sich, dass die Gruppe der Administratoren auf alle Inhaltsspeicherorte der Website zugreifen kann.|
|**Überprüfung auf Konfigurationsfehler**|Die folgenden Konfigurationsfehler wurden gefunden: {0}.|Die Migrationsbereitschaft kann nicht ermittelt werden, ohne alle anwendbaren Konfigurationen zu lesen. Beheben Sie alle Konfigurationsfehler. Stellen Sie sicher, dass die Konfiguration gültig und zugänglich ist.|
|**Überprüfung der Inhaltsgröße**|Der Inhalt der Website scheint größer zu sein als die maximal zulässige Größe für eine erfolgreiche Migration von 2 GB.|Für eine erfolgreiche Migration muss der Websiteinhalt kleiner als 2 GB sein. Prüfen Sie, ob die Website auf nicht dateisystembasierte Speicheroptionen für statische Inhalte umgestellt werden kann, z. B. Azure Storage.|
|**Überprüfung der Inhaltsgröße unbekannt**|Die Dateiinhaltsgröße konnte nicht bestimmt werden, was in der Regel auf ein Zugriffsproblem hindeutet.|Der Inhalt muss zugänglich sein, damit die Website migriert werden kann. Vergewissern Sie sich, dass die Website keine UNC-Freigaben für Inhalte verwendet und dass die Gruppe der Administratoren Zugriff auf alle Inhalte der Website hat.|
|**Überprüfung globaler Module**|Die folgenden nicht unterstützten globalen Module wurden erkannt: {0}.|App Service bietet eingeschränkte Unterstützung für globale Module. Entfernen Sie die nicht unterstützten Module zusammen mit der zugehörigen Konfiguration aus dem Abschnitt GlobalModules.|
|**ISAPI-Filterüberprüfung**|Die folgenden nicht unterstützten ISAPI-Filter wurden erkannt: {0}.|Die automatische Konfiguration benutzerdefinierter ISAPI-Filter wird nicht unterstützt. Entfernen Sie die nicht unterstützten ISAPI-Filter.|
|**ISAPI-Filterüberprüfung unbekannt**|Für die gesamte Websitekonfiguration konnten keine ISAPI-Filter ermittelt werden.|Die automatische Konfiguration benutzerdefinierter ISAPI-Filter wird nicht unterstützt. Beheben Sie alle Konfigurationsfehler, und vergewissern Sie sich, dass die Gruppe der Administratoren auf alle Inhaltsspeicherorte der Website zugreifen kann.|
|**Überprüfung von Speicherorttags**|Die folgenden Speicherpfade wurden in der Datei „applicationHost.config“ gefunden: {0}.|Die Migrationsmethode unterstützt nicht das Verschieben der Speicherpfadkonfiguration in „applicationHost.config“. Verschieben Sie die Speicherpfadkonfiguration entweder in die Datei „web.config“ des Stammverzeichnisses der Website oder in die Datei „web.config“, die der jeweiligen Anwendung zugeordnet ist, für die sie gilt.|
|**Protokollüberprüfung**|Bindungen wurden mithilfe der folgenden nicht unterstützten Protokolle gefunden: {0}.|App Service unterstützt nur die Protokolle HTTP und HTTPS. Entfernen Sie die Bindungen mit Protokollen, die nicht HTTP oder HTTPS sind.|
|**Überprüfung virtueller Verzeichnisse**|Die folgenden virtuellen Verzeichnisse werden auf UNC-Freigaben gehostet: {0}.|Die Migration unterstützt nicht das Migrieren von Websiteinhalten, die auf UNC-Freigaben gehostet werden. Verschieben Sie den Inhalt in einen lokalen Dateipfad, oder erwägen Sie, auf eine nicht dateisystembasierte Speicheroption wie Azure Storage umzustellen. Wenn Sie die freigegebene Konfiguration verwenden, deaktivieren Sie sie für den Server, bevor Sie die Inhaltspfade ändern.|
|**Prüfung auf HTTPS-Bindungen**|Die Anwendung verwendet HTTPS.|Für die Konfiguration von HTTPS in App Service sind weitere manuelle Schritte erforderlich. Weitere Schritte nach der Migration sind erforderlich, um dem App Service-Standort Zertifikate zuordnen.|
|**Überprüfung der TCP-Ports**|Bindungen wurden an den folgenden nicht unterstützten Ports gefunden: {0}.|App Services unterstützt nur die Ports 80 und 443. Clients, die Anforderungen an die Website richten, müssen den Port in ihren Anforderungen in 80 oder 443 ändern.|
|**Frameworküberprüfung**|Die folgenden Nicht-.NET-Frameworks oder nicht unterstützte .NET Framework-Versionen wurden als möglicherweise von dieser Website verwendet erkannt: {0}.|Bei der Migration wird das Framework nicht auf Nicht-.NET-Websites überprüft. App Service unterstützt mehrere Frameworks, für die es jedoch unterschiedliche Migrationsoptionen gibt. Vergewissern Sie sich, dass die Nicht-.NET-Frameworks nicht von der Website verwendet werden, oder erwägen Sie eine andere Migrationsoption.|

## <a name="next-steps"></a>Nächste Schritte

[Erstellen](how-to-create-assessment.md) Sie eine Bewertung oder [passen](how-to-modify-assessment.md) Sie sie an.