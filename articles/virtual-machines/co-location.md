---
title: Näherungsplatzierungsgruppen
description: Erfahren Sie mehr über die Verwendung von Näherungsplatzierungsgruppen in Azure.
author: cynthn
ms.author: cynthn
ms.service: virtual-machines
ms.subservice: proximity-placement-groups
ms.topic: conceptual
ms.date: 3/07/2021
ms.reviewer: zivr
ms.openlocfilehash: 7693bc23e68880346c09d3c55c84858283c735ba
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696597"
---
# <a name="proximity-placement-groups"></a>Näherungsplatzierungsgruppen

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Wenn Sie VMs in einer einzelnen Region anordnen, verringert sich der physische Abstand zwischen den Instanzen. Wenn Sie sie in einer einzelnen Verfügbarkeitszone anordnen, verringert sich ebenfalls ihr physischer Abstand. Wenn jedoch Ihre Ressourcen in Azure zunehmen, kann eine einzige Verfügbarkeitszone auch mehrere physische Rechenzentren umspannen. Dies kann zu Netzwerklatenzen führen, die sich auf die Anwendung auswirken. 

Um den Abstand zwischen den VMs so stark wie möglich zu verringern und somit die geringstmögliche Latenz zu erzielen, sollten Sie sie in einer Näherungsplatzierungsgruppe bereitstellen.

Eine Näherungsplatzierungsgruppe ist eine logische Gruppierung, mit der ein möglichst geringer Abstand zwischen Azure-Compute-Ressourcen sichergestellt wird. Näherungsplatzierungsgruppen sind für Workloads hilfreich, die eine geringe Latenz erfordern.

- Geringe Latenz zwischen eigenständigen VMs.
- Geringe Latenz zwischen VMs in einer einzelnen Verfügbarkeitsgruppe oder VM-Skalierungsgruppe. 
- Geringe Latenz zwischen eigenständigen VMs, VMs in mehreren Verfügbarkeitsgruppen oder mehreren Skalierungsgruppen. Sie können mehrere Computeressourcen in einer einzelnen Platzierungsgruppe zusammenfassen, um eine Multi-Tier-Anwendung zusammenzuführen. 
- Geringe Latenz zwischen mehreren Logikschichten, die unterschiedliche Hardwaretypen verwenden. Beispiel: Sie führen in einer einzelnen Näherungsplatzierungsgruppe das Back-End mithilfe der M-Serie in einer Verfügbarkeitsgruppe und das Front-End in einer Instanz der D-Serie in einer Skalierungsgruppe aus.

![Grafik für Näherungsplatzierungsgruppen](./media/virtual-machines-common-ppg/ppg.png)

## <a name="using-proximity-placement-groups"></a>Verwenden von Näherungsplatzierungsgruppen 

Eine Näherungsplatzierungsgruppe ist eine Ressource in Azure. Sie müssen eine erstellen, bevor Sie sie mit anderen Ressourcen verwenden können. Einmal erstellt, kann sie mit virtuellen Computern, Verfügbarkeitsgruppen oder VM-Skalierungsgruppen verwendet werden. Sie geben bei der Erstellung von Computeressourcen, die die Näherungsplatzierungsgruppe-ID bereitstellen, eine Näherungsplatzierungsgruppe an. 

Sie können auch eine vorhandene Ressource in eine Näherungsplatzierungsgruppe verschieben. Wenn Sie eine Ressource in eine Näherungsplatzierungsgruppe verschieben, sollten Sie die Ressource zuerst beenden (freigeben), da sie möglicherweise in einem anderen Rechenzentrum in der Region neu bereitgestellt wird, um der Zusammenstellungseinschränkung zu entsprechen. 

Im Falle von Verfügbarkeitsgruppen und VM-Skalierungsgruppen sollten Sie die Näherungsplatzierungsgruppe auf Ressourcenebene und nicht auf der Ebene der einzelnen virtuellen Computer festlegen. 

Eine Näherungsplatzierungsgruppe ist eher eine Zusammenstellungseinschränkung als ein Anheftmechanismus. Sie wird an ein bestimmtes Rechenzentrum angeheftet, wobei die erste von ihr verwendete Ressource bereitgestellt wird. Sobald alle Ressourcen, die die Näherungsplatzierungsgruppe verwenden, beendet (Zuordnung aufgehoben) oder gelöscht wurden, wird sie nicht mehr angeheftet. Daher ist es bei der Verwendung einer Näherungsplatzierungsgruppe mit mehreren VM-Serien wichtig, möglichst alle erforderlichen Typen im Voraus in einer Vorlage anzugeben oder einer Bereitstellungssequenz zu folgen, die Ihre Chancen auf eine erfolgreiche Bereitstellung verbessert. Wenn bei der Bereitstellung ein Fehler auftritt, starten Sie die Bereitstellung mit der VM-Größe neu, bei der die erste Bereitstellung fehlgeschlagen ist.

## <a name="what-to-expect-when-using-proximity-placement-groups"></a>Erwartung bei der Verwendung von Näherungsplatzierungsgruppen 
Näherungsplatzierungsgruppen bieten eine Zusammenstellung in demselben Rechenzentrum. Da jedoch Näherungsplatzierungsgruppen eine zusätzliche Bereitstellungseinschränkung darstellen, kann es zu Zuordnungsfehlern kommen. Es gibt nur wenige Anwendungsfälle, in denen Sie bei der Verwendung von Näherungsplatzierungsgruppen Zuordnungsfehler feststellen können:

- Wenn Sie nach dem ersten virtuellen Computer in der Näherungsplatzierungsgruppe fragen, wird das Rechenzentrum automatisch ausgewählt. In einigen Fällen kann eine zweite Anforderung der SKU für einen anderen virtuellen Computer fehlschlagen, wenn sie in diesem Rechenzentrum nicht vorhanden ist. In diesem Fall wird der Fehler **OverconstrainedAllocationRequest** zurückgegeben. Um dies zu vermeiden, versuchen Sie, die Reihenfolge zu ändern, in der Sie Ihre SKUs bereitstellen, oder lassen Sie beide Ressourcen mit einer einzelnen ARM-Vorlage bereitstellen.
-   Im Fall von flexiblen Workloads, bei denen Sie VM-Instanzen hinzufügen und entfernen, kann das Vorhandensein einer Einschränkung für Näherungsplatzierungsgruppen für Ihre Bereitstellung dazu führen, dass die Anforderung nicht erfüllt wird, was zu einem **AllocationFailure**-Fehler führt. 
- Das bedarfsgesteuerte Beenden (Freigeben) und Starten Ihrer virtuellen Computer ist eine weitere Möglichkeit, Flexibilität zu erreichen. Da die Kapazität nicht erhalten bleibt, wenn Sie einen virtuellen Computer beenden (freigeben), kann ein erneuter Start zu einem **AllocationFailure**-Fehler führen.

## <a name="planned-maintenance-and-proximity-placement-groups"></a>Geplante Wartung und Näherungsplatzierungsgruppen

Geplante Wartungsereignisse, wie z. B. die Außerbetriebnahme von Hardware in einem Azure-Rechenzentrum, können potenziell Auswirkungen auf die Ausrichtung von Ressourcen in Näherungsplatzierungsgruppen haben. Ressourcen werden möglicherweise in ein anderes Rechenzentrum verschoben, wodurch die Erwartungen an Zusammenstellung und Latenz, die mit Näherungsplatzierungsgruppen einhergehen, hinfällig werden.

### <a name="check-the-alignment-status"></a>Überprüfen des Ausrichtungsstatus

Sie können wie folgt vorgehen, um den Ausrichtungsstatus Ihrer Näherungsplatzierungsgruppen zu überprüfen.

- Der Zusammenstellungsstatus von Näherungsplatzierungsgruppen kann über das Portal, die Befehlszeilenschnittstelle und PowerShell angezeigt werden.

    -   Mit PowerShell kann der Zusammenstellungsstatus durch Ausführen des Cmdlets „Get-AzProximityPlacementGroup“ mit dem optionalen Parameter „-ColocationStatus“ abgerufen werden.

    -   An der Befehlszeilenschnittstelle kann der Zusammenstellungsstatus durch Verwenden von `az ppg show` und Einschließen des optionalen Parameters „--include-colocation-status“ abgerufen werden.

- Für jede Näherungsplatzierungsgruppe stellt eine Eigenschaft **colocation status** (Zusammenstellungsstatus) die Zusammenfassung des aktuellen Ausrichtungsstatus der gruppierten Ressourcen zur Verfügung. 

    - **Ausgerichtet**: Die Ressource befindet sich innerhalb der Latenzhüllkurve der Näherungsplatzierungsgruppe.

    - **Unbekannt**: Mindestens eine der VM-Ressourcen ist nicht zugeordnet. Nach deren erfolgreichem Neustart sollte der Status wieder zu **Ausgerichtet** zurückkehren.

    - **Nicht ausgerichtet**: Mindestens eine VM-Ressource ist nicht an der Näherungsplatzierungsgruppe ausgerichtet. Die spezifischen Ressourcen, die nicht ausgerichtet sind, werden darüber hinaus separat im Mitgliedschaftsabschnitt hervorgehoben.

- Für Verfügbarkeitsgruppen finden Sie Informationen zur Ausrichtung einzelner VMs auf der Seite „Übersicht über Verfügbarkeitsgruppen“.

- Für Skalierungsgruppen werden Informationen über die Ausrichtung einzelner Instanzen auf der Registerkarte **Instanzen** der Seite **Übersicht** für die Skalierungsgruppe angezeigt. 

### <a name="re-align-resources"></a>Erneutes Ausrichten von Ressourcen 

Wenn eine Näherungsplatzierungsgruppe `Not Aligned` ist, können Sie die betroffenen Ressourcen beenden bzw. ihre Zuordnung aufheben und sie dann neu starten. Wenn sich die VM in einer Verfügbarkeitsgruppe oder Skalierungsgruppe befindet, müssen vor dem Neustart zuerst alle VMs in der Verfügbarkeitsgruppe oder Skalierungsgruppe beendet werden bzw. muss ihre Zuordnung aufgehoben werden.

Wenn es aufgrund von Einschränkungen der Bereitstellung zu Zuordnungsfehlern kommt, müssen Sie möglicherweise alle Ressourcen (einschließlich der ausgerichteten Ressourcen) in der betroffenen Näherungsplatzierungsgruppe beenden bzw. ihre Zuordnung aufheben und sie dann erneut starten, um die Ausrichtung wiederherzustellen.

## <a name="best-practices"></a>Bewährte Methoden 
- Verwenden Sie für die niedrigste Latenz Näherungsplatzierungsgruppen zusammen mit beschleunigtem Netzwerkbetrieb. Weitere Informationen finden Sie unter [Erstellen eines virtuellen Linux-Computers mit beschleunigtem Netzwerkbetrieb](../virtual-network/create-vm-accelerated-networking-cli.md) oder [Erstellen eines virtuellen Windows-Computers mit beschleunigtem Netzwerkbetrieb](../virtual-network/create-vm-accelerated-networking-powershell.md).
- Stellen Sie alle VM-Größen in einer einzelnen Vorlage bereit. Um die Verwendung von Hardware zu vermeiden, die nicht alle erforderlichen VM-SKUs und -Größen unterstützt, schließen Sie alle Logikschichten in eine einzelne Vorlage ein, damit diese alle gleichzeitig bereitgestellt werden.
- Wenn Sie mit PowerShell, der CLI oder dem SDK ein Bereitstellungsskript erstellen, erhalten Sie möglicherweise den Zuordnungsfehler `OverconstrainedAllocationRequest`. In diesem Fall sollten Sie alle vorhandenen VMs beenden bzw. ihre Zuordnung aufheben und die Sequenz im Bereitstellungsskript so ändern, dass es mit den fehlgeschlagenen VM-SKUs/-Größen beginnt. 
- Wenn Sie eine vorhandene Platzierungsgruppe wiederverwenden, aus der VMs gelöscht wurden, warten Sie, bis der Löschvorgang vollständig durchgeführt wurde, bevor Sie der Gruppe VMs hinzufügen.
- Wenn Latenz oberste Priorität hat, fügen Sie die VMs in eine Näherungsplatzierungsgruppe und die gesamte Lösung in eine Verfügbarkeitszone ein. Wenn jedoch Resilienz oberste Priorität hat, verteilen Sie die Instanzen auf mehrere Verfügbarkeitszonen (eine einzelne Näherungsplatzierungsgruppe kann nicht mehrere Zonen umfassen).

## <a name="next-steps"></a>Nächste Schritte

- Stellen Sie mithilfe der [Azure-Befehlszeilenschnittstelle](./linux/proximity-placement-groups.md) oder [PowerShell](./windows/proximity-placement-groups.md) eine VM in einer Näherungsplatzierungsgruppe bereit.
- Erfahren Sie, wie Sie [die Netzwerklatenz testen](../virtual-network/virtual-network-test-latency.md).
- Erfahren Sie, wie Sie [den Netzwerkdurchsatz optimieren](../virtual-network/virtual-network-optimize-network-bandwidth.md).
- Erfahren Sie, wie Sie [Näherungsplatzierungsgruppen mit SAP-Anwendungen verwenden](./workloads/sap/sap-proximity-placement-scenarios.md).
