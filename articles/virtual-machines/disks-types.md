---
title: 'Auswählen eines Datenträgertyps für virtuelle Azure IaaS-Computer: verwaltete Datenträger'
description: Erhalten Sie Informationen über die verfügbaren Azure-Datenträgertypen für virtuelle Computer, einschließlich Ultra-Datenträgern, SSD Premium, SSD Standard und Standard-HDDs.
author: roygara
ms.author: rogarana
ms.date: 11/03/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 5e4eb581f8cf9b95e9a8ba4dffd442efc6c055ef
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131505439"
---
# <a name="azure-managed-disk-types"></a>Azure verwaltete Festplattentypen

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Azure Managed Disks bietet derzeit vier Festplattentypen an, die jeweils für ein bestimmtes Kundenszenario gedacht sind:

- Ultra Disks
- Hochwertige SSDs (Solid-State-Laufwerke)
- SSD Standard-Datenträger
- Standard HDDs (hard disk drives)

## <a name="disk-type-comparison"></a>Vergleich der Datenträgerarten

In der folgenden Tabelle finden Sie einen Vergleich der vier Datenträgerarten, der Ihnen bei der Entscheidung helfen soll, welche Sie verwenden möchten.

|         | Ultra-Datenträger | SSD Premium | SSD Standard | <nobr>HDD Standard</nobr> |
| ------- | ---------- | ----------- | ------------ | ------------ |
| **Datenträgertyp** | SSD | SSD | SSD | Festplattenlaufwerk |
| **Szenario**  | E/A-intensive Workloads wie [SAP HANA](workloads/sap/hana-vm-operations-storage.md), führende Datenbanken (z. B. SQL, Oracle) und andere Workloads mit vielen Transaktionen. | Produktionsworkloads und leistungsabhängige Workloads | Webserver, wenig genutzte Unternehmensanwendungen und Dev/Test | Sicherung, nicht kritisch, sporadischer Zugriff |
| **Maximale Datenträgergröße** | 65.536 Gibibyte (GiB) | 32767 GiB | 32767 GiB | 32767 GiB |
| **Max. Durchsatz** | 4\.000 MB/s | 900 MB/s | 750 MB/s | 500 MB/s |
| **Max. IOPS** | 160.000 | 20.000 | 6\.000 | 2\.000 |

## <a name="ultra-disks"></a>Ultra Disks

Ultra-Datenträger von Azure sind die leistungsstärkste Speicheroption für virtuelle Maschinen (VMs) von Azure. Sie können die Leistungsparameter eines Ultra-Datenträgers ändern, ohne dass Sie Ihre VMs neu starten müssen. Ultra-Datenträger eignen sich für datenintensive Workloads wie SAP HANA, Top-Tier-Datenbanken und transaktionsintensive Workloads.

Ultra-Datenträger müssen als Daten-Datenträger verwendet werden und können nur als leere Datenträger erstellt werden. Microsoft empfiehlt die Verwendung hochwertiger Solid-State-Laufwerke (SSDs) als Betriebssystemfestplatten.

### <a name="ultra-disk-size"></a>Größe der Ultra-Datenträger

Ultra Disks bieten in Azure pro Region und Abonnement standardmäßig bis zu 32 TiB, auf Anfrage aber auch mehr Kapazität. Wenn Sie mehr Kapazität wünschen, beantragen Sie eine Kontingenterhöhung, oder wenden Sie sich an den Azure-Support.

Die folgende Tabelle enthält einen Vergleich der Festplattengrößen und Leistungsobergrenzen, um Ihnen die Entscheidung zu erleichtern, welche Sie verwenden sollten.

|Datenträgergröße (GiB)  |IOPS-Limit  |Durchsatzlimit (MB/s)  |
|---------|---------|---------|
|4     |1\.200         |300         |
|8     |2\.400         |600         |
|16     |4\.800         |1\.200         |
|32     |9\.600         |2\.400         |
|64     |19.200         |4\.000         |
|128     |38.400         |4\.000         |
|256     |76.800         |4\.000         |
|512     |153.600         |4\.000         |
|1\.024–65.536 (Größen in diesem Bereich erhöhen sich in Schritten von 1 TiB)     |160.000         |4\.000         |

Ultra-Datenträger sind so konzipiert, dass sie in 99,99 % der Fälle Latenzzeiten im Submillisekundenbereich und die in der vorstehenden Tabelle beschriebenen Zielwerte für IOPS und Durchsatz erreichen.

### <a name="ultra-disk-performance"></a>Ultra-Datenträger Leistung

Ultra-Datenträger verfügen über ein flexibles Leistungskonfigurationsmodell, mit dem Sie IOPS und Durchsatz sowohl vor als auch nach der Bereitstellung der Festplatte unabhängig voneinander konfigurieren können. Ultra-Datenträger sind in verschiedenen festen Größen erhältlich, von 4 GiB bis zu 64 TiB.

### <a name="ultra-disk-iops"></a>Ultra-Datenträger IOPS

Ultra-Datenträger unterstützen IOPS-Grenzwerte von 300 IOPS/GiB, bis zu einem Maximum von 160.000 IOPS pro Datenträger. Um die angestrebten IOPS für die Festplatte zu erreichen, müssen Sie sicherstellen, dass die IOPS der ausgewählten Festplatte unter dem IOPS-Limit der VM liegen.

Der aktuelle Höchstwert für IOPS für einen einzelnen virtuellen Computer in allgemein verfügbaren Größen beträgt 80.000. Ultra Disks mit größeren IOPS können als freigegebene Datenträger verwendet werden, um mehrere VMs zu unterstützen.

Der garantierte Mindestwert für die IOPS pro Festplatte beträgt 1 IOPS/GiB, wobei der Mindestwert für die Basislinie insgesamt 100 IOPS beträgt. Wenn Sie z. B. einen Ultra-Datenträger mit 4 GiB bereitstellen, beträgt das Minimum an IOPS für diesen Datenträger 100 anstelle von acht.

Weitere Informationen zu IOPS finden Sie unter [Virtuelle Maschine und Festplattenleistung](disks-performance.md).

### <a name="ultra-disk-throughput"></a>Ultra-Datenträger-Durchsatz

Der Durchsatzgrenzwert eines einzelnen Ultra-Datenträgers beträgt 256 KiB/s für jeden bereitgestellten IOPS, bis zu einem Maximum von 4.000 MB/s pro Datenträger (wobei MB/s = 10^6 Bytes pro Sekunde). Der garantierte Mindestdurchsatz pro Datenträger beträgt 4 KiB/s für jeden bereitgestellten IOPS-Wert mit einem Gesamtmindestwert von 1 MB/s.

Sie können die IOPS- und Durchsatzleistung von Ultra-Datenträgern während der Laufzeit anpassen, ohne den Datenträger von der virtuellen Maschine zu trennen. Nachdem eine Größenanpassung auf einer Festplatte vorgenommen wurde, kann es bis zu einer Stunde dauern, bis die Änderung wirksam wird. Innerhalb eines Zeitfensters von 24 Stunden sind bis zu vier Größenanpassungsvorgänge zulässig.

Es ist möglich, dass ein Größenänderungsvorgang aufgrund mangelnder Leistungsbandbreitenkapazität fehlschlägt.

### <a name="ultra-disk-limitations"></a>Einschränkungen für Ultra-Datenträger

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](../../includes/managed-disks-ultra-disks-GA-scope-and-limitations.md)]

Wenn Sie mit der Verwendung von Ultra-Datenträgern beginnen möchten, lesen Sie den Artikel über [die Verwendung von Azure Ultra-Datenträgern](disks-enable-ultra-ssd.md).

## <a name="premium-ssds"></a>SSD Premium

Azure SSD Premium bietet Datenträgerunterstützung mit hoher Leistung und geringer Wartezeit für virtuelle Computer (VMs) mit E/A-intensiven Workloads. Um die Vorteile der Geschwindigkeit und Leistung von Premium-SSDs zu nutzen, können Sie vorhandene VM-Festplatten auf Premium-SSDs migrieren. Premium-SSDs sind für geschäftskritische Produktionsanwendungen geeignet, können aber nur mit kompatiblen VM-Serien verwendet werden.

Weitere Informationen zu den einzelnen Azure-VM-Typen und -Größen für Windows oder Linux, einschließlich der Größenkompatibilität für Premium-Speicher, finden Sie unter [Größen für virtuelle Maschinen in Azure](sizes.md). Sie müssen jeden einzelnen Artikel zur VM-Größe überprüfen, um festzustellen, ob sie mit Premium Storage kompatibel ist.

### <a name="premium-ssd-size"></a>Premium SSD-Größe
[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

Kapazität, IOPS und Durchsatz werden bei der Bereitstellung eines Premium-Speicherlaufwerks garantiert. Wenn Sie beispielsweise einen P50-Datenträger erstellen, stellt Azure eine Speicherkapazität von 4.095 GB, 7.500 IOPS und einen Durchsatz von 250 MB/s für diesen Datenträger bereit. Die Anwendung kann die Kapazität und Leistung ganz oder teilweise nutzen. Premium-SSDs sind so konzipiert, dass sie in 99,9 % der Fälle die in der vorangegangenen Tabelle beschriebenen Latenzzeiten im einstelligen Millisekundenbereich, Ziel-IOPS und Durchsatz liefern.

### <a name="premium-ssd-bursting"></a>SSD Premium-Bursting

Premium-SSDs bieten Disk Bursting, das eine bessere Toleranz gegenüber unvorhersehbaren Änderungen der IO-Muster bietet. Disk Bursting ist vor allem beim Booten des Betriebssystems und bei Anwendungen mit starkem Datenverkehr nützlich. Weitere Informationen zur Funktionsweise von Bursting für Azure-Datenträger finden Sie unter [Bursting auf Datenträgerebene](disk-bursting.md#disk-level-bursting).

### <a name="premium-ssd-transactions"></a>Premium-SSD-Transaktionen

Für SSD Premium gilt jeder E/A-Vorgang kleiner oder gleich 256 KiB Durchsatz als einzelner E/A-Vorgang. E/A-Vorgänge mit einem Durchsatz größer als 256 KiB gelten als mehrere Ein-bzw. Ausgabevorgänge der Größe 256 KiB.

## <a name="standard-ssds"></a>SSD Standard-Datenträger

Azure-Standard-SSDs sind für Arbeitslasten optimiert, die eine konstante Leistung bei niedrigeren IOPS-Werten erfordern. Sie sind eine besonders gute Wahl für Kunden mit unterschiedlichen Arbeitslasten, die von lokalen Festplattenlösungen (HDD) unterstützt werden. Im Vergleich zu HDD Standard bieten SSD Standard-Datenträger eine bessere Verfügbarkeit, Konsistenz, Zuverlässigkeit und Latenz. Standard-SSDs eignen sich für Webserver, Anwendungsserver mit geringer IOPS, wenig genutzte Unternehmensanwendungen und nicht produktive Arbeitslasten. Wie HDD Standard-Datenträger sind SSD Standard-Datenträger für alle Azure-VMs verfügbar.

### <a name="standard-ssd-size"></a>Standard-SSD-Größe

[!INCLUDE [disk-storage-standard-ssd-sizes](../../includes/disk-storage-standard-ssd-sizes.md)]

Standard-SSDs sind dafür ausgelegt, 99 % der Zeit Wartezeiten im einstelligen Millisekundenbereich sowie IOPS und Durchsätze bis zu den in der vorherigen Tabelle genannten Grenzwerten bereitzustellen. Die Istwerte für IOPS und Durchsätze können in einigen Fällen je nach Datenverkehrsmuster variieren. Standard-SSDs stellen eine konstantere Leistung und geringere Latenzen als HDD-Datenträger bereit.

### <a name="standard-ssd-transactions"></a>Standard-SSD-Transaktionen

Für SSD Standard gilt jeder E/A-Vorgang kleiner oder gleich 256 KiB Durchsatz als ein einzelner E/A-Vorgang. E/A-Vorgänge mit einem Durchsatz größer als 256 KiB gelten als mehrere Ein-bzw. Ausgabevorgänge der Größe 256 KiB. Für diese Transaktionen werden Kosten in Rechnung gestellt.

### <a name="standard-ssd-bursting"></a>Standard-SSD-Bursting

Standard-SSDs bieten Datenträger-Bursting, das eine bessere Toleranz gegenüber unvorhersehbaren IO-Musteränderungen bietet. Betriebssystem-Boot-Disks und Anwendungen, die für Verkehrsspitzen anfällig sind, profitieren beide vom Datenträger-Bursting. Weitere Informationen zur Funktionsweise von Bursting für Azure-Datenträger finden Sie unter [Bursting auf Datenträgerebene](disk-bursting.md#disk-level-bursting).

## <a name="standard-hdds"></a>HDD Standard-Datenträger

Azure Standard-HDDs bieten zuverlässige, kostengünstige Festplattenunterstützung für VMs, die latenztolerante Arbeitslasten ausführen. Bei Standard-Storage werden Ihre Daten auf HDDs gespeichert, und die Leistung kann stärker schwanken als bei SSD-basierten Festplatten. Standard-HDDs sind so konzipiert, dass sie für die meisten IO-Vorgänge Schreiblatenzen von weniger als 10 ms und Leselatenzen von weniger als 20 ms bieten. Die tatsächliche Leistung kann jedoch je nach IO-Größe und Arbeitslastmuster variieren. Wenn Sie mit virtuellen Computern arbeiten, können Sie und HDD Standard-Datenträger für Dev/Test-Szenarien sowie weniger kritische Workloads verwenden. HDD Standard-Datenträger sind in allen Azure-Regionen verfügbar und können mit allen Azure-VMs verwendet werden.

### <a name="standard-hdd-size"></a>Standard-HDD-Größe
[!INCLUDE [disk-storage-standard-hdd-sizes](../../includes/disk-storage-standard-hdd-sizes.md)]

### <a name="standard-hdd-transactions"></a>Standard-HDD-Transaktionen

Bei Standard-Festplatten wird jeder E/A-Vorgang unabhängig von der E/A-Größe als eine einzige Transaktion betrachtet. Diese Transaktionen besitzen Auswirkungen auf die Abrechnung.

## <a name="billing"></a>Abrechnung

Bei der Verwendung verwalteter Datenträger gelten die folgenden Abrechnungsaspekte:

- Datenträgertyp
- Verwaltete Datenträger Größe
- Momentaufnahmen
- Ausgehende Datenübertragungen
- Anzahl von Transaktionen

**Größe des verwalteten Datenträgers**: Verwaltete Datenträger werden entsprechend ihrer bereitgestellten Größe abgerechnet. Azure ordnet die bereitgestellte Größe (aufgerundet) der nächsthöheren angebotenen Datenträgergröße zu. Details zu den angebotenen Datenträgergrößen finden Sie in den obigen Tabellen. Jede Festplatte wird einem unterstützten Angebot für die Festplattengröße zugeordnet und entsprechend abgerechnet. Wenn Sie z. B. eine Standard-SSD mit 200 GiB bereitstellen, wird sie dem Festplattengrößenangebot E15 (256 GiB) zugeordnet. Die Abrechnung für bereitgestellte Datenträger erfolgt anteilig auf Stundenbasis unter Verwendung des monatlichen Preises für das Speicherangebot. Ein Beispiel: Sie stellen eine E10-Festplatte bereit und löschen sie nach 20 Stunden Nutzung. In diesem Fall wird Ihnen das E10-Angebot anteilig für 20 Stunden in Rechnung gestellt, unabhängig von der Datenmenge, die auf den Datenträger geschrieben wurde.

**Momentaufnahmen**: Momentaufnahmen werden auf Basis der verwendeten Größe in Rechnung gestellt. Sie erstellen zum Beispiel einen Snapshot eines verwalteten Datenträgers mit einer bereitgestellten Kapazität von 64 GiB und einer tatsächlich genutzten Datengröße von 10 GiB. In diesem Fall wird der Snapshot nur für die genutzte Datengröße von 10 GiB in Rechnung gestellt.

Weitere Informationen zu Momentaufnahmen finden Sie im Abschnitt zu Momentaufnahmen in der [Übersicht verwalteter Datenträgern](managed-disks-overview.md#managed-disk-snapshots).

**Ausgehende Datenübertragungen:**[Ausgehende Datenübertragungen](https://azure.microsoft.com/pricing/details/bandwidth/) (Daten, die von den Azure-Rechenzentren ausgehen) verursachen Kosten bei der Bandbreitenverwendung.

**Transaktionen**: Die Anzahl der Transaktionen, die auf einem verwalteten Standarddatenträger durchgeführt wurden, wird Ihnen in Rechnung gestellt. Für SSD Standard gilt jeder E/A-Vorgang kleiner oder gleich 256 KiB Durchsatz als ein einzelner E/A-Vorgang. E/A-Vorgänge mit einem Durchsatz größer als 256 KiB gelten als mehrere Ein-bzw. Ausgabevorgänge der Größe 256 KiB. Bei Standard-Festplatten wird jede E/A-Operation als eine einzige Transaktion betrachtet, unabhängig von der Größe der E/A.

Ausführliche Informationen zu den Preisen für verwaltete Datenträger (einschließlich Transaktionskosten) finden Sie unter [Preise für verwaltete Datenträger](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disks-vm-reservation-fee"></a>Ultra-Datenträger VM-Reservierungsgebühr

Virtuelle Azure-Computer können angeben, ob sie mit Ultra-Datenträgern kompatibel sind. Eine Ultra-Datenträger-kompatible VM weist dedizierte Bandbreitenkapazität zwischen der Compute-VM-Instanz und der Blockspeicher-Skalierungseinheit zu, um die Leistung zu optimieren und die Latenz zu verringern. Wenn Sie diese Fähigkeit auf der VM hinzufügen, führt dies zu einer Reservierungsgebühr. Die Reservierungsgebühr wird nur erhoben, wenn Sie die Ultra-Datenträger-Fähigkeit auf der VM ohne einen angeschlossenen Ultra-Datenträger aktiviert haben. Wenn ein Ultra-Datenträger an die Ultra-Datenträger-kompatible VM angeschlossen ist, wird die Reservierungsgebühr nicht erhoben. Diese Gebühr fällt pro auf der VM bereitgestellter vCPU an.

> [!Note]
> Für [VM-Größen mit eingeschränkter Anzahl der Kerne](constrained-vcpu.md) ergibt sich die Reservierungsgebühr anhand der tatsächlichen Anzahl der vCPUs und nicht anhand der eingeschränkten Kerne. Für Standard_E32-8s_v3 basiert die Reservierungsgebühr auf 32 Kernen.

Preisdetails für Ultra-Datenträger finden Sie auf der [Seite mit der Preisübersicht für Azure-Datenträger](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="azure-disk-reservation"></a>Azure-Datenträgerreservierung

Mit der Festplattenreservierung erhalten Sie einen Rabatt auf den Vorabkauf von Festplattenspeicher für ein Jahr, wodurch sich Ihre Gesamtkosten verringern. Wenn Sie eine Festplattenreservierung kaufen, wählen Sie eine bestimmte Festplatten-SKU in einer Zielregion. Sie können zum Beispiel fünf P30 (1 TiB) Premium-SSDs in der Region Central US für ein Jahr auswählen. Die Festplattenreservierung ähnelt den reservierten VM-Instanzen in Azure. Sie können VM- und Datenträgerreservierungen bündeln, um die Einsparungen zu maximieren. Zurzeit bietet Azure Disks Reservation einen einjährigen Verpflichtungsplan für Premium-SSD-SKUs von P30 (1 TiB) bis P80 (32 TiB) in allen Produktionsregionen. Weitere Einzelheiten zu den Preisen für reservierte Festplatten finden Sie auf der Seite [Preise für Azure-Festplatten](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="next-steps"></a>Nächste Schritte

Als Einstieg sehen Sie sich den Artikel [Verwaltete Datenträger – Preise](https://azure.microsoft.com/pricing/details/managed-disks/) an.
