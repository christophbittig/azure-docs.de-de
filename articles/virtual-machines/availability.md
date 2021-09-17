---
title: Verfügbarkeitsoptionen für virtuelle Azure-Computer
description: Hier erfahren Sie mehr über die Verfügbarkeitsfunktionen für die Ausführung virtueller Computer in Azure.
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 03/08/2021
ms.reviewer: cynthn
ms.openlocfilehash: 77e2ef4a5af250f19e505dfb9f725aa7674f691f
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694476"
---
# <a name="availability-options-for-azure-virtual-machines"></a>Verfügbarkeitsoptionen für virtuelle Azure-Computer

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Dieser Artikel bietet eine Übersicht über die Verfügbarkeitsoptionen für virtuelle Azure-Computer (VMs).

## <a name="availability-zones"></a>Verfügbarkeitszonen

Mit [Verfügbarkeitszonen](../availability-zones/az-overview.md?context=/azure/virtual-machines/context/context) erhalten Sie mehr Kontrolle beim Aufrechterhalten der Verfügbarkeit von Anwendungen und Daten auf Ihren virtuellen Computern. Eine Verfügbarkeitszone ist eine physisch getrennte Zone in einer Azure-Region. Pro unterstützter Azure-Region sind drei Verfügbarkeitszonen vorhanden. 

Jede Verfügbarkeitszone verfügt über eine eigene Stromquelle, ein Netzwerk und eine Kühlung. Indem Sie Ihre Lösungen für die Verwendung von replizierten VMs in Zonen gestalten, können Sie Ihre Apps und Daten beim Ausfall eines Rechenzentrums schützen. Wenn eine Zone kompromittiert ist, sind replizierte Apps und Daten sofort in einer anderen Zone verfügbar. 

## <a name="availability-sets"></a>Verfügbarkeitsgruppen
Eine [Verfügbarkeitsgruppe](availability-set-overview.md) ist eine logische Gruppierung von virtuellen Computern, die es Azure ermöglicht zu verstehen, wie Ihre Anwendung erstellt ist, um Redundanz und Verfügbarkeit zu bieten. Es empfiehlt sich, mindestens zwei virtuelle Computer in einer Verfügbarkeitsgruppe zu erstellen, um eine Anwendung mit hoher Verfügbarkeit zu erhalten und die [Azure-SLA von 99,95 Prozent](https://azure.microsoft.com/support/legal/sla/virtual-machines/) zu erfüllen. Für die Verfügbarkeitsgruppe selbst entstehen keine Kosten. Sie bezahlen nur für die einzelnen VM-Instanzen, die Sie erstellen.


## <a name="virtual-machines-scale-sets"></a>Skalierungsgruppen für virtuelle Computer 

Mit [Azure-VM-Skalierungsgruppen](../virtual-machine-scale-sets/overview.md?context=/azure/virtual-machines/context/context) können Sie eine Gruppe von VMs mit Lastenausgleich erstellen und verwalten. Die Anzahl von VM-Instanzen kann automatisch erhöht oder verringert werden, wenn sich der Bedarf ändert, oder es kann ein Zeitplan festgelegt werden. Skalierungsgruppen ermöglichen Hochverfügbarkeit für Ihre Anwendungen und das zentrale Verwalten, Konfigurieren und Aktualisieren zahlreicher VMs. Es empfiehlt sich, mindestens zwei VMs in einer Skalierungsgruppe zu erstellen, um eine Anwendung mit hoher Verfügbarkeit zu erhalten und die [Azure-SLA von 99,95 Prozent](https://azure.microsoft.com/support/legal/sla/virtual-machines/) zu erfüllen. Für die Skalierungssgruppe selbst entstehen keine Kosten. Sie bezahlen nur für die einzelnen VM-Instanzen, die Sie erstellen.

VMs in einer Skalierungsgruppe können auch in einer einzelnen Verfügbarkeitszone oder regional bereitgestellt werden. Die Bereitstellungsoptionen für Verfügbarkeitszonen können je nach [Orchestrierungsmodus ](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md?context=/azure/virtual-machines/context/context) variieren.

## <a name="load-balancer"></a>Load Balancer
Kombinieren Sie den Azure-Lastenausgleich ([Azure Load Balancer](../load-balancer/load-balancer-overview.md)) mit einer Verfügbarkeitszone oder -gruppe, um höchste Anwendungsresilienz zu erzielen. Der Azure-Lastenausgleich verteilt den Datenverkehr auf mehrere virtuelle Computer. In die virtuellen Computer der Standardebene ist der Azure-Lastenausgleich bereits integriert. Der Azure Load Balancer ist aber nicht auf allen Ebenen des virtuellen Computers verfügbar. Weitere Informationen zum Lastenausgleich zwischen virtuellen Computern finden Sie unter **Lastenausgleich zwischen virtuellen Computern** für [Linux](linux/tutorial-load-balancer.md) oder [Windows](windows/tutorial-load-balancer.md).


## <a name="azure-storage-redundancy"></a>Azure Storage-Redundanz
Azure Storage speichert immer mehrere Kopien Ihrer Daten, damit sie vor geplanten und ungeplanten Ereignissen geschützt sind – von vorübergehend auftretenden Hardwarefehlern und Netzwerk- oder Stromausfällen bis zu schweren Naturkatastrophen usw. Redundanz stellt sicher, dass Ihr Speicherkonto seine Ziele für Verfügbarkeit und Dauerhaftigkeit selbst bei Ausfällen erfüllt.

Berücksichtigen Sie bei der Entscheidung, welche Redundanzoption für Ihr Szenario am besten geeignet ist, die Kompromisse zwischen geringeren Kosten und höherer Verfügbarkeit. Anhand der folgenden Faktoren können Sie bestimmen, welche Redundanzoption Sie auswählen sollten:
- Wie werden Ihre Daten in der primären Region repliziert?
- Werden Ihre Daten in eine zweite Region repliziert, die geografisch von der primären Region entfernt ist, um Schutz vor regionalen Ausfällen zu erreichen?
- Benötigt Ihre Anwendung Lesezugriff auf die replizierten Daten in der sekundären Region, falls die primäre Region aus irgendeinem Grund nicht verfügbar ist?

Weitere Informationen finden Sie unter [Azure Storage-Redundanz](../storage/common/storage-redundancy.md).

## <a name="azure-site-recovery"></a>Azure Site Recovery
Organisationen benötigen eine BCDR-Strategie (Business Continuity and Disaster Recovery, Geschäftskontinuität und Notfallwiederherstellung), die den Schutz ihrer Daten sowie den Onlinezustand ihrer Apps und Workloads gewährleistet, wenn geplante oder ungeplante Ausfälle auftreten.

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) sorgt dafür, dass Geschäftsanwendungen und Workloads während Ausfällen weiter ausgeführt werden, und trägt so zur Aufrechterhaltung der Geschäftskontinuität bei. Site Recovery repliziert Workloads, die auf physischen und virtuellen Computern ausgeführt werden, von einem primären Standort an einen sekundären Standort. Im Falle eines Ausfalls am primären Standort können Sie ein Failover auf den sekundären Standort ausführen und von dort aus auf die Apps zugreifen. Wenn der primäre Standort dann wieder verfügbar ist, können Sie ein Failback ausführen.

Site Recovery kann die Replikation für folgende Bereiche verwalten:
- Replikation von virtuellen Azure-Computern zwischen Azure-Regionen
- Lokale virtuelle Computer, virtuelle Azure Stack-Computer und physische Server

## <a name="next-steps"></a>Nächste Schritte
- [Erstellen eines virtuellen Computers in einer Verfügbarkeitszone](./linux/create-cli-availability-zone.md)
- [Erstellen eines virtuellen Computers in einer Verfügbarkeitsgruppe](./linux/tutorial-availability-sets.md)
- [Erstellen einer VM-Skalierungsgruppe](../virtual-machine-scale-sets/quick-create-portal.md)