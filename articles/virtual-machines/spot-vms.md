---
title: Verwenden von Azure-Spot-VMs
description: Erfahren Sie, wie Sie mithilfe von Azure Spot-VMs Kosten sparen.
author: JagVeerappan
ms.author: jagaveer
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/05/2020
ms.reviewer: cynthn
ms.openlocfilehash: ce20b7815c89a45a2677029a28fd205499751e30
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695399"
---
# <a name="use-azure-spot-virtual-machines"></a>Verwenden von Azure-Spot-VMs 

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Wenn Sie Azure Spot-VMs verwenden, profitieren Sie von unserer ungenutzten Kapazität und erzielen erhebliche Kosteneinsparungen. Wenn die Kapazität von Azure wieder benötigt wird, werden die Azure-Spot-VMs durch die Azure-Infrastruktur entfernt. Aus diesem Grund eignen sich Azure-Spot-VMs hervorragend für Workloads, die Unterbrechungen tolerieren, z. B. Batchverarbeitungsaufträge, Dev/Test-Umgebungen, umfangreiche Computeworkloads und mehr.

Die verfügbare Kapazität kann abhängig von der Größe, Region, Tageszeit usw. variieren. Beim Bereitstellen von Azure-Spot-VMs weist Azure die VMs zu, wenn Kapazität verfügbar ist, aber es gibt keine SLA für diese VMs. Eine Azure-Spot-VM bietet keine Garantien für Hochverfügbarkeit. Wenn die Kapazität von Azure wieder benötigt wird, werden die Azure-Spot-VMs nach einer Benachrichtigung innerhalb von 30 Sekunden durch die Azure-Infrastruktur entfernt. 


## <a name="eviction-policy"></a>Entfernungsrichtlinie

VMs können basierend auf der Kapazität oder dem von Ihnen festgelegten maximalen Preis entfernt werden. Wenn Sie eine Azure-Spot-VM erstellen, können Sie die Entfernungsrichtlinie auf *Zuordnung aufheben* (Standardeinstellung) oder *Löschen* festlegen. 

Die Richtlinie *Zuordnung aufheben* versetzt Ihre VM in den Zustand „beendet/Zuordnung aufgehoben“, sodass Sie sie später erneut bereitstellen können. Es gibt jedoch keine Garantie dafür, dass die Zuordnung erfolgreich ist. Die virtuellen Computer, deren Zuordnung aufgehoben wurde, werden auf Ihr Kontingent angerechnet, und die Speicherkosten für die zugrunde liegenden Datenträger werden Ihnen in Rechnung gestellt. 

Wenn Sie möchten, dass Ihr virtueller Computer beim Entfernen gelöscht wird, können Sie die Entfernungsrichtlinie auf *Löschen* festlegen. Die entfernten VMs werden zusammen mit ihren zugrunde liegenden Datenträgern gelöscht, und darum fallen weiter keine Kosten für ihre Speicherung an. 

Sie können sich für den Empfang von Benachrichtigungen in der VM über [Azure Scheduled Events](./linux/scheduled-events.md) anmelden. Dadurch werden Sie benachrichtigt, wenn Ihre virtuellen Computer entfernt werden, und Sie haben vor dem Entfernen 30 Sekunden Zeit, Aufträge abzuschließen und die VMs herunterzufahren. 


| Option | Ergebnis |
|--------|---------|
| Der maximale Preis ist auf >= dem aktuellen Preis festgelegt. | Wenn Kapazität und Kontingent verfügbar sind, wird die VM bereitgestellt. |
| Der maximale Preis ist auf < der aktuelle Preis festgelegt. | Die VM wird nicht bereitgestellt. Sie erhalten eine Fehlermeldung, die besagt, dass der maximale Preis auf >= dem aktuellen Preis festgelegt werden muss. |
| Neustarten eines virtuellen Computers, der beendet/entfernt wurde, wenn der maximale Preis > = dem aktuellen Preis ist | Wenn Kapazität und Kontingent verfügbar sind, wird die VM bereitgestellt. |
| Neustarten eines virtuellen Computers, der beendet/entfernt wurde, wenn der maximale Preis < als der aktuellen Preis ist | Sie erhalten eine Fehlermeldung, die besagt, dass der maximale Preis auf >= dem aktuellen Preis festgelegt werden muss. | 
| Der Preis für den virtuellen Computer wurde überschritten und ist nun > der maximale Preis. | Die VM wird entfernt. Sie erhalten 30 Sekunden vor der Entfernung eine Benachrichtigung. | 
| Nach der Entfernung wechselt der Preis für den virtuellen Computer zurück zu < der maximale Preis. | Die VM wird nicht automatisch neu gestartet. Sie können die VM selbst neu starten. Sie wird dann zum aktuellen Preis in Rechnung gestellt. |
| Wenn der maximale Preis auf `-1` festgelegt ist | Die VM wird nicht aus Preisgründen entfernt. Der maximale Preis ist der aktuelle Preis bis hin zum Preis für Standard-VMs. Ihnen wird niemals mehr als der Standardpreis in Rechnung gestellt.| 
| Ändern des maximalen Preises | Sie müssen die Zuordnung des virtuellen Computers aufheben, um den maximalen Preis zu ändern. Heben Sie die Zuordnung der VM auf, legen Sie einen neuen maximalen Preis fest, und aktualisieren Sie dann die VM. |


## <a name="limitations"></a>Einschränkungen

Die folgenden VM-Größen werden für Azure-Spot-VMs nicht unterstützt:
 - B-Serie
 - Promoversionen beliebiger Größe (z. B. Promogrößen Dv2, NV, NC, H)

Mit Ausnahme von Microsoft Azure China 21ViaNet können Azure-Spot-VMs in jeder beliebigen Region bereitgestellt werden.

<a name="channel"></a>

Folgende [Angebotstypen](https://azure.microsoft.com/support/legal/offer-details/) werden derzeit unterstützt:

-   Enterprise Agreement 
-   Angebotscode für nutzungsbasierte Bezahlung (003P)
-   Gesponsert (0036P und 0136P)
- Wenden Sie sich hinsichtlich des Cloud-Dienstanbieters (CSP) an Ihren Partner.


## <a name="pricing"></a>Preise

Die Preise für Azure-Spot-VMs variieren je nach Region und SKU. Weitere Informationen finden Sie unter den VM-Preisen für [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) und [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

Sie können auch Preisinformationen abfragen, indem Sie die [Azure-Einzelhandelspreis-API](/rest/api/cost-management/retail-prices/azure-retail-prices) verwenden, um Informationen zu Preisen von Spot-VMs abzufragen. `meterName` und `skuName` enthalten beide `Spot`.

Bei der variablen Preisgestaltung können Sie einen maximalen Preis in US-Dollar (USD) mit bis zu 5 Dezimalstellen festlegen. Der Wert `0.98765` würde beispielsweise einem maximalen Preis von 0,98765 US-Dollar pro Stunde entsprechen. Wenn Sie den maximalen Preis auf `-1` festlegen, wird die VM nicht basierend auf dem Preis entfernt. Der Preis für die VM entspricht dem aktuellen Preis für Spot-VMs oder dem Preis für eine Standard-VM, je nachdem, welcher Preis niedriger ist, solange Kapazitäten und Kontingente verfügbar sind.

## <a name="pricing-and-eviction-history"></a>Preise und Entfernungsverlauf

Sie können den Preisverlauf und Entfernungsraten pro Größe in einer Region im Portal anzeigen. Wählen Sie **Preisverlauf anzeigen und Preise in Regionen in der Nähe vergleichen** aus, um eine Tabelle oder ein Diagramm der Preise für eine bestimmte Größe anzuzeigen.  Die Preis- und Entfernungsraten in den folgenden Abbildungen sind nur Beispiele. 

**Diagramm**:

:::image type="content" source="./media/spot-chart.png" alt-text="Screenshot: Regionsoptionen mit Unterschied in den Preisen und Entfernungsraten als Diagramm.":::

**Tabelle**:

:::image type="content" source="./media/spot-table.png" alt-text="Screenshot: Regionsoptionen mit Unterschied in den Preisen und Entfernungsraten als Tabelle.":::



##  <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

**F:** Ist eine Azure-Spot-VM nach der Erstellung identisch mit einer Standard-VM?

**A:** Ja, mit der Ausnahme, dass für Azure-Spot-VMs keine SLA besteht und dass sie jederzeit entfernt werden können.


**F:** Was kann ich tun, wenn ich nach dem Entfernen immer noch Kapazität benötige?

**A:** Es wird empfohlen, anstelle von Azure-Spot-VMs Standard-VMs zu verwenden, wenn Sie die Kapazität sofort benötigen.


**F:** Wie wird das Kontingent für Azure-Spot-VMs verwaltet?

**A:** Azure-Spot-VMs verfügen über einen separaten Kontingentpool. Das Spotkontingent wird von virtuellen Computern und Skalierungsgruppeninstanzen gemeinsam genutzt. Weitere Informationen finden Sie unter [Grenzwerte für Azure-Abonnements, -Dienste und -Kontingente sowie allgemeine Beschränkungen](../azure-resource-manager/management/azure-subscription-service-limits.md).


**F:** Kann ich für Azure-Spot-VMs zusätzliches Kontingent anfordern?

**A:** Ja, Sie können eine Anforderung zur Erhöhung Ihres Kontingents für Azure-Spot-VMs über den [Standardprozess zur Kontingentanforderung](../azure-portal/supportability/per-vm-quota-requests.md) übermitteln.


**F:** Wo kann ich Fragen stellen?

**A:** Sie können Ihre Frage in [Q&A](/answers/topics/azure-spot.html) veröffentlichen und mit `azure-spot` markieren. 


**F:** Wie kann ich den maximalen Preis für eine Spot-VM ändern?

**A:** Bevor Sie den maximalen Preis ändern können, müssen Sie die Zuordnung der VM aufheben. Dann können Sie den Preis im Portal im Abschnitt **Konfiguration** für die VM ändern. 

## <a name="next-steps"></a>Nächste Schritte
Verwenden Sie die [Befehlszeilenschnittstelle](./linux/spot-cli.md), das [Portal](spot-portal.md), [ARM-Vorlagen](./linux/spot-template.md) oder [PowerShell](./windows/spot-powershell.md), um Azure-Spot-VMs bereitzustellen.

Sie können auch eine [Skalierungsgruppe mit Azure-Spot-VM-Instanzen](../virtual-machine-scale-sets/use-spot.md) bereitstellen.

Informationen zu eventuell auftretenden Fehlern finden Sie unter [Fehlercodes](./error-codes-spot.md).
