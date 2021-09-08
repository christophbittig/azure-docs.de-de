---
title: Datenträgermetriken
description: Beispiele für Datenträgerbursting-Metriken
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 461d8c7ae29c56a80b8863b2900c051158add918
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689378"
---
# <a name="disk-performance-metrics"></a>Leistungsmetriken für Datenträger

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Azure bietet im Azure-Portal Metriken, die Aufschluss über die Leistung Ihrer virtuellen Computer (VM) und Datenträger geben. Die Metriken können auch über einen API-Aufruf abgerufen werden. Dieser Artikel ist in drei Unterabschnitte unterteilt:

- **Metriken für Datenträger-E/A, Durchsatz und Warteschlangentiefe**: Diese Metriken liefern Ihnen Informationen zur Speicherleistung eines Datenträgers und eines virtuellen Computers.
- **Metriken für Datenträgerbursting**: Dies sind die Metriken, die Einblick in das [Burstingfeature](disk-bursting.md) auf unseren Premium-Datenträgern bieten.
- **Auslastungsmetriken für Speicher-E/A**: Diese Metriken unterstützen die Diagnose von Engpässen bei der Speicherleistung von Datenträgern. 

Alle Metriken werden einmal pro Minute ausgegeben, mit Ausnahme der Metriken für Guthaben für Burst-Bit/s in Prozent und Guthaben für Burst-E/A in Prozent, die alle fünf Minuten ausgegeben werden.

## <a name="disk-io-throughput-and-queue-depth-metrics"></a>Metriken für Datenträger-E/A, Durchsatz und Warteschlangentiefe
Die folgenden Metriken sind verfügbar, um einen Einblick in die VM- und Datenträger-E/A, den Durchsatz und die Warteschlangentiefe zu erhalten:

- **Warteschlangentiefe für Betriebssystemdatenträger**: Die Anzahl der aktuell ausstehenden E/A-Anforderungen, die darauf warten, vom Betriebssystemdatenträger gelesen oder auf diesen geschrieben zu werden.
- **Vom Betriebssystemdatenträger gelesene Bytes/Sek.** : Die Anzahl der Bytes, die in einer Sekunde vom Betriebssystemdatenträger gelesen werden.
- **Lesevorgänge auf Betriebssystemdatenträger/Sek.** : Die Anzahl der Eingabevorgänge, die in einer Sekunde vom Betriebssystemdatenträger gelesen werden.
- **Auf den Betriebssystemdatenträger geschriebene Bytes/Sek.** : Die Anzahl der Bytes, die in einer Sekunde auf den Betriebssystemdatenträger geschrieben werden.
- **Schreibvorgänge auf Betriebssystemdatenträger/Sek.** : Die Anzahl der Ausgabevorgänge, die in einer Sekunde auf den Betriebssystemdatenträger geschrieben werden.
- **Warteschlangentiefe für Datenträger**: Die Anzahl der aktuell ausstehenden E/A-Anforderungen, die darauf warten, von Datenträgern gelesen oder auf diese geschrieben zu werden.
- **Vom Datenträger gelesene Bytes/Sek.** : Die Anzahl der Bytes, die in einer Sekunde von Datenträgern für Daten gelesen werden.
- **Datenträgerlesevorgänge/Sek.** : Die Anzahl der Eingabevorgänge, die in einer Sekunde von Datenträgern gelesen werden.
- **Auf den Datenträger geschriebene Bytes/Sek.** : Die Anzahl der Bytes, die in einer Sekunde auf Datenträger für Daten geschrieben werden.
- **Datenträgerschreibvorgänge/Sek.** : Die Anzahl der Ausgabevorgänge, die in einer Sekunde auf Datenträger für Daten geschrieben werden.
- **Vom Datenträger gelesene Bytes/Sek.** : Die Anzahl der gesamten Bytes, die in einer Sekunde von allen an einen virtuellen Computer angeschlossenen Datenträgern gelesen werden.
- **Datenträgerlesevorgänge/Sek.** : Die Anzahl der Eingabevorgänge, die in einer Sekunde von allen an einen virtuellen Computer angeschlossenen Datenträgern gelesen werden.
- **Byte geschrieben/Sek.** : Die Anzahl der Bytes, die in einer Sekunde von allen an einen virtuellen Computer angeschlossenen Datenträgern geschrieben werden.
- **Datenträgerschreibvorgänge/Sek.** : Die Anzahl der Ausgabevorgänge, die in einer Sekunde von allen an einen virtuellen Computer angeschlossenen Datenträgern geschrieben werden.

## <a name="bursting-metrics"></a>Burstingmetriken
Die folgenden Metriken bieten Einblick in das [Burstingfeature](disk-bursting.md) auf unseren Premium-Datenträgern:

- **Maximale Burstbandbreite für Datenträger**: Der Durchsatzgrenzwert für das Bursting von Datenträgern.
- **Maximale Burstbandbreite für Betriebssystemdatenträger**: Der Durchsatzgrenzwert für das Bursting des Betriebssystemdatenträgers.
- **Maximale Burst-IOPS für Datenträger**: Der IOPS-Grenzwert für das Bursting von Datenträgern.
- **Maximale Burst-IOPS für Betriebssystemdatenträger**: Der IOPS-Grenzwert für das Bursting des Betriebssystemdatenträgers.
- **Zielbandbreite für Datenträger**: Der höchste Durchsatz, der von Datenträgern ohne Bursting erzielt werden kann.
- **Zielbandbreite für Betriebssystemdatenträger**: Der höchste Durchsatz, der vom Betriebssystemdatenträger ohne Bursting erzielt werden kann.
- **Ziel-IOPS für Datenträger**: Die höchste IOPS, die von Datenträgern ohne Bursting erzielt werden kann.
- **Ziel-IOPS für Betriebssystemdatenträger**: Die höchste IOPS, die vom Betriebssystemdatenträger ohne Bursting erzielt werden kann.
- **Verwendetes Datenträgerguthaben für Burst-Bit/s in Prozent**: Der kumulierte Prozentsatz des verwendeten Burstdurchsatzes für die Datenträger. Wird in einem Intervall von 5 Minuten ausgegeben.
- **Verwendetes Betriebssystemdatenträger-Guthaben für Burst-E/A in Prozent**: Der kumulierte Prozentsatz des verwendeten Burstdurchsatzes für den Betriebssystemdatenträger. Wird in einem Intervall von 5 Minuten ausgegeben.
- **Verwendetes Datenträgerguthaben für Burst-E/A in Prozent**: Der kumulierte Prozentsatz der verwendeten Burst-IOPS für die Datenträger. Wird in einem Intervall von 5 Minuten ausgegeben.
- **Verwendetes Betriebssystemdatenträger-Guthaben für Burst-E/A in Prozent**: Der kumulierte Prozentsatz der verwendeten Burst-IOPS für den Betriebssystemdatenträger. Wird in einem Intervall von 5 Minuten ausgegeben.

## <a name="storage-io-utilization-metrics"></a>Auslastungsmetriken für Speicher-E/A
Die folgenden Metriken helfen bei der Diagnose von Engpässen Ihrer Kombination aus VM und Datenträger. Diese Metriken sind nur bei der folgenden Konfiguration verfügbar:
- Nur für VM-Serien mit Unterstützung von Storage Premium verfügbar.
- Nicht für Disk Ultra-Datenträger verfügbar. Alle anderen Datenträgertypen dieser VM-Serie können diese Metriken verwenden.

Metriken, die bei der Diagnose der E/A-Begrenzung von Datenträgern helfen:

- **Beanspruchte Datenträger-IOPS in Prozent**: Der Prozentsatz, der über die abgeschlossenen Datenträger-IOPS im Vergleich zu den bereitgestellten Datenträger-IOPs berechnet wird. Wenn dieser Betrag bei 100 % liegt, wird Ihre aktive Anwendung vom IOPS-Grenzwert Ihres Datenträgers begrenzt.
- **Beanspruchte Datenträgerbandbreite in Prozent**: Der Prozentsatz, der über den abgeschlossenen Datenträgerdurchsatz im Vergleich zum bereitgestellten Datenträgerdurchsatz berechnet wird. Wenn dieser Betrag bei 100 % liegt, wird Ihre aktive Anwendung vom Bandbreitengrenzwert Ihres Datenträgers begrenzt.
- **Beanspruchte Betriebssystemdatenträger-IOPS in Prozent**: Der Prozentsatz, der über die abgeschlossenen Betriebssystemdatenträger-IOPS im Vergleich zu den bereitgestellten Betriebssystemdatenträger-IOPs berechnet wird. Wenn dieser Betrag bei 100 % liegt, wird Ihre aktive Anwendung vom IOPS-Grenzwert Ihres Betriebssystemdatenträgers begrenzt.
- **Beanspruchte Betriebssystem-Datenträgerbandbreite in Prozent**: Der Prozentsatz, der über den abgeschlossenen Betriebssystemdatenträgerdurchsatz im Vergleich zum bereitgestellten Betriebssystemdatenträgerdurchsatz berechnet wird. Wenn dieser Betrag bei 100 % liegt, wird Ihre aktive Anwendung vom Bandbreitengrenzwert Ihres Betriebssystemdatenträgers begrenzt.

Metriken, die bei der Diagnose der E/A-Begrenzung von virtuellen Computern helfen:

- **Verbrauchte von der VM zwischengespeicherte IOPS in Prozent**: Der Prozentsatz der gesamten abgeschlossenen IOPS gemessen am oberen Grenzwert für zwischengespeicherte IOPS auf dem virtuellen Computer. Wenn dieser Betrag bei 100 % liegt, wird Ihre aktive Anwendung vom Grenzwert für zwischengespeicherte IOPS Ihrer VM begrenzt.
- **Prozentsatz der von der VM beanspruchten zwischengespeicherten Bandbreite**: Der Prozentsatz des gesamten abgeschlossenen Durchsatzes gemessen am oberen Grenzwert für zwischengespeicherten Durchsatz auf dem virtuellen Computer. Wenn dieser Betrag bei 100 % liegt, wird Ihre aktive Anwendung vom Grenzwert der Bandbreite Ihrer VM für die Zwischenspeicherung begrenzt.
- **Verbrauchte von der VM nicht zwischengespeicherte IOPS in Prozent**: Der Prozentsatz der gesamten abgeschlossenen IOPS auf einem virtuellen Computer gemessen am oberen Grenzwert für nicht zwischengespeicherte IOPS auf dem virtuellen Computer. Wenn dieser Betrag bei 100 % liegt, wird Ihre aktive Anwendung vom Grenzwert für nicht zwischengespeicherte IOPS Ihrer VM begrenzt.
- **Prozentsatz der von der VM beanspruchten nicht zwischengespeicherten Bandbreite**: Der Prozentsatz des gesamten abgeschlossenen Durchsatzes auf einem virtuellen Computer gemessen am oberen Grenzwert für bereitgestellten Durchsatz auf dem virtuellen Computer. Wenn dieser Betrag bei 100 % liegt, wird Ihre aktive Anwendung vom Grenzwert der Bandbreite Ihrer VM für nicht zwischengespeicherte Daten begrenzt.

## <a name="storage-io-metrics-example"></a>Beispiel für Speicher-E/A-Metriken

Sehen wir uns ein Beispiel zur Verwendung dieser neuen Metriken für die Speicher-E/A-Auslastung an, die uns beim Debuggen von Engpässen im System unterstützen können. Das Systemsetup ist dasselbe wie im vorherigen Beispiel, außer dass diesmal der angefügte Datenträger des Betriebssystems *nicht* zwischengespeichert wird.

**Setup:**

- Standard_D8s_v3
  - Zwischengespeicherte IOPS: 16.000
  - Nicht zwischengespeicherte IOPS: 12.800
- P30-Betriebssystemdatenträger
  - IOPS: 5.000
  - Hostzwischenspeicherung: **Deaktiviert**
- Zwei P30-Betriebssystemdatenträger × 2
  - IOPS: 5.000
  - Hostzwischenspeicherung: **Lesen/Schreiben**
- Zwei P30-Betriebssystemdatenträger × 2
  - IOPS: 5.000
  - Hostzwischenspeicherung: **Deaktiviert**

Lassen Sie uns einen Benchmarktest mit dieser Kombination aus virtuellem Computer und Datenträger durchführen, die E/A-Aktivität erzeugt. Weitere Informationen zu Benchmarktests der Speicher-E/A in Azure finden Sie unter [Benchmarktests für Ihre Anwendung in Azure Disk Storage](disks-benchmarks.md). Im Benchmarktool können Sie ersehen, dass die Kombination aus VM und Datenträger 22.800 IOPS erreichen kann:

![Screenshot zur f i o-Ausgabe zeigt „r=22,8k“ hervorgehoben an.](media/disks-metrics/utilization-metrics-example/fio-output.jpg)



Standard_D8s_v3 kann insgesamt 28.600 IOPS erreichen. Mithilfe der Metriken wollen wir untersuchen, was vor sich geht und unseren Speicher-E/A-Engpass identifizieren. Wählen Sie im linken Bereich **Metriken** aus:

![Screenshot mit hervorgehobener Option „Metriken“ im linken Bereich.](media/disks-metrics/utilization-metrics-example/metrics-menu.jpg)

Sehen wir uns zunächst die Metrik **VM Cached IOPS Consumed Percentage** (Verbrauchte von der VM zwischengespeicherte IOPS in Prozent) an:

![Der Screenshot zeigt „VM Cached IOPS Consumed Percentage“ (Verbrauchte von der VM zwischengespeicherte IOPS in Prozent) an.](media/disks-metrics/utilization-metrics-example/vm-cached.jpg)

Diese Metrik teilt uns mit, dass 61 % der 16.000 IOPS, die in der VM für zwischengespeicherte IOPS zugeteilt sind, verwendet werden. Dieser Prozentsatz bedeutet, dass der Engpass bei der Speicher-E/A nicht bei den zwischengespeicherten Datenträgern liegt, da er die 100 % nicht erreicht. Sehen wir uns nun die Metrik **VM Uncached IOPS Consumed Percentage** (Verbrauchte von der VM nicht zwischengespeicherte IOPS in Prozent) an:

![Der Screenshot zeigt „VM Uncached IOPS Consumed Percentage“ (Verbrauchte von der VM nicht zwischengespeicherte IOPS in Prozent) an.](media/disks-metrics/utilization-metrics-example/vm-uncached.jpg)

Diese Metrik liegt bei 100 %. Sie gibt an, dass alle 12.800 IOPS, die der VM für nicht zwischengespeicherte IOPS zugeteilt sind, verwendet werden. Eine Möglichkeit, dieses Problem zu beheben, besteht darin, unsere VM so zu vergrößern, dass sie die zusätzliche E/A verarbeiten kann. Aber bevor wir das erledigen, sollten wir uns den angefügten Datenträger ansehen, um herauszufinden, wie viele IOPS sie erfahren. Prüfen Sie den Betriebssystemdatenträger, indem Sie die Metrik **Beanspruchte Betriebssystem-Datenträger-IOPS in Prozent** betrachten:

![Der Screenshot zeigt „Beanspruchte Betriebssystem-Datenträger-IOPS in Prozent“ an.](media/disks-metrics/utilization-metrics-example/os-disk.jpg)

Diese Metrik gibt an, dass annähernd 90 % von den für diesen P30-Betriebssystem-Datenträger bereitgestellten 5.000 IOPS verwendet werden. Dieser Prozentsatz bedeutet, dass kein Engpass auf dem Betriebssystemdatenträger vorliegt. Prüfen wir nun die an die VM angefügten Datenträger in Form der Metrik **Beanspruchte Datenträger-IOPS in Prozent**:

![Der Screenshot zeigt „Beanspruchte Datenträger-IOPS in Prozent“ an.](media/disks-metrics/utilization-metrics-example/data-disks-no-splitting.jpg)

Diese Metrik teilt uns mit, dass der durchschnittlich verbrauchte Prozentsatz an IOPS über alle Datenträger bei ungefähr 42 % liegt. Dieser Prozentsatz wird auf der Grundlage der von den Datenträgern verwendeten IOPS berechnet, die nicht aus dem Hostcache bedient werden. Sehen wir uns diese Metrik genauer an, indem wir *Aufteilung* auf diese Metriken anwenden und nach dem LUN-Wert aufteilen:

![Der Screenshot zeigt „Beanspruchte Datenträger-IOPS in Prozent“ mit Aufteilung an.](media/disks-metrics/utilization-metrics-example/data-disks-splitting.jpg)

Diese Metrik informiert uns, dass die an LUN 3 und 2 angefügten Datenträger ungefähr 85 % ihrer bereitgestellten IOPS verbrauchen. Dieses Diagramm zeigt, wie die E/A aus dem Blickwinkel der VM- und Datenträgerarchitektur aussieht:

![Diagramm für das Beispiel „Speicher-E/A-Metriken“.](media/disks-metrics/utilization-metrics-example/metrics-diagram.jpg)

## <a name="next-steps"></a>Nächste Schritte

- [Überblick über Metriken in Azure Monitor](../azure-monitor/essentials/data-platform-metrics.md)
- [Erläuterung der Metrikaggregation](../azure-monitor/essentials/metrics-aggregation-explained.md)
- [Erstellen, Anzeigen und Verwalten von Metrikwarnungen mit Azure Monitor](../azure-monitor/alerts/alerts-metric.md)