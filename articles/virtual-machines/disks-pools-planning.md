---
title: Optimieren der Leistung Ihrer Azure-Datenträgerpools (Vorschau)
description: Erfahren Sie, wie Sie die maximale Leistung aus einem Azure-Datenträgerpool herausholen können.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: ignite-fall-2021
ms.openlocfilehash: 801930dc58993a81c29969f602da64f201919bd0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131428005"
---
# <a name="azure-disk-pools-preview-planning-guide"></a>Planungshandbuch für Azure-Datenträgerpools (Vorschau)

Es ist wichtig, die Leistungsanforderungen Ihrer Workload zu verstehen, bevor Sie einen Azure-Datenträgerpool (Vorschau) bereitstellen. Wenn Sie Ihre Anforderungen im Voraus bestimmen, können Sie die beste Leistung für Ihren Datenträgerpool erzielen. Die Leistung eines Datenträgerpools wird von drei Hauptfaktoren bestimmt, deren Anpassung die Leistung Ihres Datenträgerpools verbessert:

- Skalierbarkeitsziel des Datenträgerpools
- Die Skalierbarkeitsziele der einzelnen Datenträger des Datenträgerpools.
- Die Netzwerkverbindung zwischen den Clientcomputern und dem Datenträgerpool.

## <a name="optimize-for-low-latency"></a>Optimieren für geringe Wartezeit

Wenn Sie eine niedrige Wartezeit anstreben, sollten Sie Ihrem Datenträgerpool Ultra Disks hinzufügen. Ultra Disks bieten Datenträger mit einer Wartezeit von weniger als einer Millisekunde. Um die geringstmögliche Wartezeit zu erreichen, müssen Sie auch Ihre Netzwerkkonfiguration überprüfen und sicherstellen, dass sie den optimalsten Pfad verwendet. Erwägen Sie die Verwendung von [ExpressRoute FastPath](../expressroute/about-fastpath.md), um die Wartezeit im Netzwerk zu minimieren.

## <a name="optimize-for-high-throughput"></a>Optimieren für hohen Durchsatz

Wenn Sie Durchsatz priorisieren, sollten Sie zunächst die Leistungsziele der verschiedenen Datenträgerpool-SKUs sowie die Anzahl der Datenträgerpools auswerten, die erforderlich sind, um Ihre Durchsatzziele zu erreichen. Wenn Ihre Leistungsanforderungen die Möglichkeiten eines Premium-Datenträgerpools übersteigen, können Sie Ihre Bereitstellung auf mehrere Datenträgerpools aufteilen. Dann können Sie entscheiden, wie Sie die Leistung eines Datenträgerpools zwischen den einzelnen Datenträgern und deren Typen am besten ausschöpfen. In einem Datenträgerpool können Sie entweder Premium- und Standard-SSD-Datenträger kombinieren oder nur Ultra-Datenträger verwenden. Ultra-Datenträger können nicht zusammen mit Premium- oder Standard-SSD-Datenträgern verwendet werden. Wählen Sie den Datenträgertyp aus, der Ihre Anforderungen am besten erfüllt. Vergewissern Sie sich außerdem, dass die Netzwerkverbindung zwischen Ihren Clients und dem Datenträgerpool keinen Engpass darstellt, insbesondere was den Durchsatz betrifft.


## <a name="use-cases"></a>Anwendungsfälle

In der folgenden Tabelle sind einige typische Anwendungsfälle für Datenträgerpools mit Azure VMware Solution und eine empfohlene Konfiguration aufgeführt.


|Anwendungsfälle für Azure VMware Solution  |Vorgeschlagener Datenträgertyp  |Vorgeschlagene Datenträgerpool-SKU  |Vorgeschlagene Netzwerkkonfiguration  |
|---------|---------|---------|---------|
|Blockspeicher für aktive Arbeitssätze, wie eine Erweiterung der Azure VMware Solution vSAN.     |Ultra Disks         |Premium         |Verwenden Sie das ExpressRoute-Gateway für virtuelle Netzwerke: Ultra Performance (Höchstleistung) oder ErGw3AZ (10 GBit/s), um das virtuelle Netzwerk des Datenträgerpools mit der Azure VMware Solution-Cloud zu verbinden und FastPath zu aktivieren, um die Netzwerklatenz zu minimieren.         |
|Tiering: Daten, auf die nur selten zugegriffen wird, von der Azure VMware Solution vSAN in den Datenträgerpool verschieben.     |SSD Premium, SDD Standard         |Standard         |Verwenden Sie das ExpressRoute-Gateway für virtuelle Netzwerke: Standard (1 GBit/s) oder High Performance (Hochleistung, 2 GBit/s), um das virtuelle Netzwerk des Datenträgerpools mit der Azure VMware Solution-Cloud zu verbinden.         |
|Datenspeicher für den Standort für die Notfallwiederherstellung in Azure VMware Solution: Replizieren Sie Daten aus der lokalen oder primären VMware-Umgebung in den Datenträgerpool als sekundären Standort.     |SSD Premium, SDD Standard         |Standard, Basic         |Verwenden Sie das ExpressRoute-Gateway für virtuelle Netzwerke: Standard (1 GBit/s) oder High Performance (Hochleistung, 2 GBit/s), um das virtuelle Netzwerk des Datenträgerpools mit der Azure VMware Solution-Cloud zu verbinden.         |


Informationen zum Planen Ihrer Netzwerkeinrichtung sowie weitere Überlegungen zu Azure VMware Solution finden Sie in der [Checkliste für die Netzwerkplanung für Azure VMware Solution](../azure-vmware/tutorial-network-checklist.md).

## <a name="disk-pool-scalability-and-performance-targets"></a>Skalierbarkeits- und Leistungsziele für den Datenträgerpool

|Resource  |Basis-Datenträgerpool  |Standard-Datenträgerpool  |Premium-Datenträgerpool  |
|---------|---------|---------|---------|
|Maximale Anzahl von Datenträgern pro Datenträgerpool     |16         |32         |32         |
|Maximale IOPS pro Datenträgerpool     |12.800         |25.600         |51.200         |
|Maximale MBit/s pro Datenträgerpool     |192         |384         |768         |

Das folgende Beispiel sollte Ihnen einen Überblick darüber geben, wie die verschiedenen Leistungsfaktoren zusammenarbeiten:

Wenn Sie z. B. zwei SSD Premium-Datenträger mit 1 TiB (P30, mit einer Zielvorgabe von 5.000 IOPS und 200 MBit/s) zu einem Standard-Datenträgerpool hinzufügen, können Sie 2 x 5000 = 10.000 IOPS erreichen. Der Durchsatz würde jedoch vom Datenträgerpool auf 384 MBit/s begrenzt werden. Um diesen Grenzwert von 384 MBit/s zu überschreiten, können Sie weitere Datenträgerpools bereitstellen, um für zusätzlichen Durchsatz eine Aufskalierung zu erreichen. Ihr Netzwerkdurchsatz schränkt die Effektivität der Aufskalierung ein.

Datenträgerpools, die ohne Angabe der SKU in der REST-API erstellt werden, entsprechen standardmäßig dem Standard-Datenträgerpool.

## <a name="availability"></a>Verfügbarkeit

Datenträgerpools befinden sich derzeit in der Vorschauphase und dürfen nicht für Produktionsworkloads verwendet werden. Standardmäßig unterstützt ein Datenträgerpool nur Premium- und Standard-SSD-Datenträger. Sie können die Unterstützung für Ultra-Datenträger für einen Datenträgerpool aktivieren, aber ein Datenträgerpool mit Ultra-Datenträgern ist nicht mit Premium- oder Standard-SSD-Datenträgern kompatibel.

Datenträgerpools mit Unterstützung für Premium- und Standard-SSD-Datenträgern basieren auf einer hoch verfügbaren Architektur mit mehreren Datenträgern, die den iSCSI-Endpunkt hosten. Datenträgerpools mit Unterstützung für Ultra-Datenträger werden in einer Einzelinstanzbereitstellung gehostet.

Wenn Ihr Datenträgerpool aus irgendeinem Grund für Ihre Azure VMware Solution-Cloud nicht mehr zugänglich ist, treten folgende Probleme auf:

- Auf alle Datenspeicher, die dem Datenträgerpool zugeordnet sind, kann nicht mehr zugegriffen werden.
- Alle VMware-VMs, die in der Azure VMware Solution-Cloud gehostet werden und die betroffenen Datenspeicher verwenden, weisen einen fehlerhaften Zustand auf.
- Die Integrität von Clustern in der Azure VMware Solution-Cloud wird nicht beeinträchtigt, mit Ausnahme eines Vorgangs: Sie können einen Host nicht in den Wartungsmodus versetzen. Azure VMware Solution behandelt diesen Fehler und versucht eine Wiederherstellung, indem die betroffenen Datenspeicher getrennt werden.

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen eines Datenträgerpools](disks-pools-deploy.md)
- Informationen zur Integration von Datenträgerpools mittels Azure VMware Solutions finden Sie unter [Anfügen von Datenträgerpools an Azure VMware Solution-Hosts (Vorschau)](../azure-vmware/attach-disk-pools-to-azure-vmware-solution-hosts.md).
