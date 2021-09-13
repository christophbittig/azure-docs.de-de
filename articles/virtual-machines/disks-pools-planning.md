---
title: Optimieren der Leistung Ihrer Azure-Datenträgerpools (Vorschau)
description: Erfahren Sie, wie Sie die maximale Leistung aus einem Azure-Datenträgerpool herausholen können.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 74d50826811198811e6cea671641cae378d1235c
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/18/2021
ms.locfileid: "122419586"
---
# <a name="azure-disk-pools-preview-planning-guide"></a>Planungshandbuch für Azure-Datenträgerpools (Vorschau)

Es ist wichtig, die Leistungsanforderungen Ihrer Workload zu verstehen, bevor Sie einen Azure-Datenträgerpool (Vorschau) bereitstellen. Wenn Sie Ihre Anforderungen im Voraus bestimmen, können Sie die beste Leistung für Ihren Datenträgerpool erzielen. Die Leistung eines Datenträgerpools wird von drei Hauptfaktoren bestimmt, deren Anpassung die Leistung Ihres Datenträgerpools verbessert:

- Skalierbarkeitsziel des Datenträgerpools
- Die Skalierbarkeitsziele der einzelnen Datenträger des Datenträgerpools.
- Die Netzwerkverbindung zwischen den Clientcomputern und dem Datenträgerpool.

## <a name="optimize-for-low-latency"></a>Optimieren für geringe Wartezeit

Wenn Sie eine niedrige Wartezeit anstreben, sollten Sie Ihrem Datenträgerpool Ultra Disks hinzufügen. Ultra Disks bieten Datenträger mit einer Wartezeit von weniger als einer Millisekunde. Um die geringstmögliche Wartezeit zu erreichen, müssen Sie auch Ihre Netzwerkkonfiguration überprüfen und sicherstellen, dass sie den optimalsten Pfad verwendet. Erwägen Sie die Verwendung von [ExpressRoute FastPath](../expressroute/about-fastpath.md), um die Wartezeit im Netzwerk zu minimieren.

## <a name="optimize-for-high-throughput"></a>Optimieren für hohen Durchsatz

Wenn Sie den Durchsatz priorisieren, bewerten Sie zunächst die Anzahl der Datenträgerpools, die zum Erreichen Ihrer Durchsatzziele erforderlich sind. Sobald Sie über die erforderlichen Ziele verfügen, können Sie sie auf die einzelnen Datenträger und ihre Typen aufteilen. Derzeit können zwei Datenträgertypen in einem Datenträgerpool verwendet werden: SSD Premium-Datenträger und Ultra Disks. SSD Premium-Datenträger können hohe Werte für IOPS und MBit/s bieten, die mit ihrer Speicherkapazität skaliert werden können, während Ultra Disks ihre Leistung unabhängig von ihrer Speicherkapazität skalieren können. Wählen Sie den Typ aus, der am besten zu Ihrem Kosten- und Leistungsverhältnis passt. Vergewissern Sie sich außerdem, dass die Netzwerkverbindung zwischen Ihren Clients und dem Datenträgerpool keinen Engpass darstellt, insbesondere was den Durchsatz betrifft.


## <a name="use-cases"></a>Anwendungsfälle

In der folgenden Tabelle sind einige typische Anwendungsfälle für Datenträgerpools mit Azure VMware Solution und eine empfohlene Konfiguration aufgeführt.


|Anwendungsfälle für Azure VMware Solution  |Vorgeschlagener Datenträgertyp  |Vorgeschlagene Netzwerkkonfiguration  |
|---------|---------|---------|
|Blockspeicher für aktive Arbeitssätze, wie eine Erweiterung der Azure VMware Solution vSAN.     |Ultra Disks         |Verwenden Sie das ExpressRoute-Gateway für virtuelle Netzwerke: Ultra Performance (Höchstleistung) oder ErGw3AZ (10 GBit/s), um das virtuelle Netzwerk des Datenträgerpools mit der Azure VMware Solution-Cloud zu verbinden und FastPath zu aktivieren, um die Netzwerklatenz zu minimieren.         |
|Tiering: Daten, auf die nur selten zugegriffen wird, von der Azure VMware Solution vSAN in den Datenträgerpool verschieben.     |SSD Premium         |Verwenden Sie das ExpressRoute-Gateway für virtuelle Netzwerke: Standard (1 GBit/s) oder High Performance (Hochleistung, 2 GBit/s), um das virtuelle Netzwerk des Datenträgerpools mit der Azure VMware Solution-Cloud zu verbinden.         |
|Datenspeicher für den Standort für die Notfallwiederherstellung in Azure VMware Solution: Replizieren Sie Daten aus der lokalen oder primären VMware-Umgebung in den Datenträgerpool als sekundären Standort.     |SSD Premium         |Verwenden Sie das ExpressRoute-Gateway für virtuelle Netzwerke: Standard (1 GBit/s) oder High Performance (Hochleistung, 2 GBit/s), um das virtuelle Netzwerk des Datenträgerpools mit der Azure VMware Solution-Cloud zu verbinden.         |

Informationen zum Planen Ihrer Netzwerkeinrichtung sowie weitere Überlegungen zu Azure VMware Solution finden Sie in der [Checkliste für die Netzwerkplanung für Azure VMware Solution](../azure-vmware/tutorial-network-checklist.md).

## <a name="disk-pool-scalability-and-performance-targets"></a>Skalierbarkeits- und Leistungsziele für den Datenträgerpool

|Resource  |Begrenzung  |
|---------|---------|
|Maximale Anzahl von Datenträgern pro Datenträgerpool|8|
|Maximale IOPS pro Datenträgerpool|25.600|
|Maximale MBit/s pro Datenträgerpool|384|

Das folgende Beispiel sollte Ihnen einen Überblick darüber geben, wie die verschiedenen Leistungsfaktoren zusammenarbeiten:

Wenn Sie z. B. zwei SSD Premium-Datenträger mit 1 TiB (P30, mit einer Zielvorgabe von 5000 IOPS und 200 MBit/s) zu einem Datenträgerpool hinzufügen, können Sie 2 x 5000 = 10.000 IOPS erreichen. Der Durchsatz würde jedoch vom Datenträgerpool auf 384 MBit/s begrenzt werden. Um diesen Grenzwert von 384 MBit/s zu überschreiten, können Sie weitere Datenträgerpools bereitstellen, um für zusätzlichen Durchsatz eine Aufskalierung zu erreichen. Ihr Netzwerkdurchsatz schränkt die Effektivität der Aufskalierung ein.

## <a name="availability"></a>Verfügbarkeit

Datenträgerpools befinden sich derzeit in der Vorschauphase und dürfen nicht für Produktionsworkloads verwendet werden.

Wenn Ihr Datenträgerpool aus irgendeinem Grund für Ihre Azure VMware Solution-Cloud nicht mehr zugänglich ist, treten folgende Probleme auf:

- Auf alle Datenspeicher, die dem Datenträgerpool zugeordnet sind, kann nicht mehr zugegriffen werden.
- Alle virtuellen VMware-Computer, die in dieser Azure VMware Solution-Cloud gehostet werden, die die betroffenen Datenspeicher verwendet, weisen einen fehlerhaften Zustand auf.
- Die Integrität von Clustern in dieser Azure VMware Solution-Cloud wird nicht beeinträchtigt, mit Ausnahme eines Vorgangs: Sie werden nicht in der Lage sein, einen Host in den Wartungsmodus zu versetzen. Azure VMware Solution behandelt diesen Fehler und versucht eine Wiederherstellung, indem die betroffenen Datenspeicher getrennt werden.

## <a name="next-steps"></a>Nächste Schritte

[Bereitstellen eines Datenträgerpools](disks-pools-deploy.md)
