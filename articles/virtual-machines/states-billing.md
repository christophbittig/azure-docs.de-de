---
title: Zustandswerte und Abrechnung von Azure-VMs
description: Übersicht über die verschiedenen Zustandswerte einer VM und den Zeitpunkt der Abrechnung für Benutzer.
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/8/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.openlocfilehash: b671ae2c4c1f67ea8593d7564cb91b6057e96f3c
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695408"
---
# <a name="states-and-billing-of-azure-virtual-machines"></a>Zustandswerte und Abrechnung von Azure-VMs

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Azure-VMs durchlaufen verschiedene Zustände, die sich in die Kategorien *Bereitstellungszustände* und *Betriebszustände* unterteilen lassen. Im vorliegenden Artikel werden diese Zustände beschrieben, und zudem enthält er genaue Angaben dazu, wann die Instanznutzung Kunden in Rechnung gestellt wird. 

## <a name="get-states-using-instance-view"></a>Abrufen von Zustandswerten über die Instanzansicht

Die Instanzenansicht-API bietet Informationen zum Ausführungszustand der VM. Weitere Informationen finden Sie in der API-Dokumentation unter [Virtual Machines – Instance View](/rest/api/compute/virtualmachines/instanceview) (VMs – Instanzenansicht).

Der Azure-Ressourcen-Explorer bietet eine einfache Benutzeroberfläche zum Anzeigen des Ausführungszustands des virtuellen Computers: [Ressourcen-Explorer](https://resources.azure.com/).

Bereitstellungszustände werden in den VM-Eigenschaften und in der Instanzenansicht angezeigt. Betriebszustände sind in der Instanzenansicht der VM verfügbar.

Verwenden Sie die [API zum Auflisten aller virtuellen Computer](/rest/api/compute/virtualmachines/listall), und setzen Sie dabei den Parameter **statusOnly** auf *true*, um den Energiezustand aller virtuellen Computer in Ihrem Abonnement abzurufen.

> [!NOTE]
> Über die [API zum Auflisten aller VMs](/rest/api/compute/virtualmachines/listall) mit dem auf „true“ festgelegten **statusOnly**-Parameter wird der Betriebszustand aller VMs in einem Abonnement abgerufen. In einigen seltenen Fällen ist der Betriebszustand aufgrund zeitweiliger Probleme beim Abrufvorgang jedoch möglicherweise nicht verfügbar. In diesen Fällen wird empfohlen, es mit der gleichen API erneut zu versuchen oder [Azure Resource Health](../service-health/resource-health-overview.md) oder [Azure Resource Graph](..//governance/resource-graph/overview.md) zu verwenden, um den Betriebszustand der VMs zu überprüfen.
 
## <a name="power-states-and-billing"></a>Betriebszustand und Abrechnung

Der Betriebszustand stellt den letzten bekannten Zustand der VM dar.

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/vm-power-states.png" alt-text="Diagramm der möglichen Betriebszustandswerte einer VM":::

Die folgende Tabelle enthält eine Beschreibung der einzelnen Instanzzustände und Angaben dazu, ob die Instanznutzung in Rechnung gestellt wird.

| Betriebszustand | Beschreibung | Abrechnung |  
|---|---|---|
| Wird gestartet| Die VM wird hochgefahren. | In Rechnung gestellt | 
| Wird ausgeführt | Die VM ist vollständig hochgefahren. Dies ist der standardmäßige Arbeitszustand. | In Rechnung gestellt | 
| Wird beendet | Dies ist ein Übergangszustand zwischen „Wird ausgeführt“ und „Beendet“. | In Rechnung gestellt| 
|Beendet | Die VM wurde einem Host zugeordnet, aber nicht ausgeführt. Dieser Zustand wird auch als PoweredOff-Status oder als *Beendet (zugeordnet)* bezeichnet. Dieses Problem kann durch das Aufrufen des PowerOff-API-Vorgangs oder durch das Initiieren des Herunterfahrens von innerhalb des Gastbetriebssystems entstehen. Der Status „Beendet“ kann auch kurz während der VM-Erstellung oder beim Starten einer VM mit dem Status „Zuordnung aufgehoben“ angezeigt werden.  | In Rechnung gestellt | 
| Zuordnung wird aufgehoben | Dies ist der Übergangszustand zwischen „Wird ausgeführt“ und „Zuordnung aufgehoben“. | Nicht in Rechnung gestellt* | 
| Zuordnung aufgehoben | Die Lease für die zugrunde liegende Hardware auf der VM wurde freigegeben. Die VM ist vollständig ausgeschaltet. Dieser Zustand wird auch als *Beendet (Zuordnung aufgehoben)* bezeichnet. | Nicht in Rechnung gestellt* | 

&#42; Für einige Azure-Ressourcen, z. B. [Datenträger](https://azure.microsoft.com/pricing/details/managed-disks) und [Netzwerk](https://azure.microsoft.com/pricing/details/bandwidth/), fallen weiterhin Kosten an.


## <a name="provisioning-states"></a>Bereitstellungszustände

Ein Bereitstellungzustand ist der Status eines vom Benutzer initiierten Vorgangs der Steuerungsebene auf der VM. Diese Zustände sind unabhängig vom Betriebszustand einer VM.

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/vm-provisioning-states.png" alt-text="Abbildung der möglichen Bereitstellungsstatus einer VM":::

| Bereitstellungsstatus | Beschreibung | Betriebszustand | Abrechnung | 
|---|---|---|---|
| Erstellen | Erstellung der VM | Wird gestartet | Nicht in Rechnung gestellt* | 
| Aktualisieren | Aktualisierung des Modells für eine vorhandene VM. Einige Änderungen an einer VM, die nicht das Modell betreffen (z. B. Start oder Neustart), fallen ebenfalls unter „Aktualisieren“. | Wird ausgeführt | In Rechnung gestellt | 
| Löschen | Löschen der VM | Zuordnung wird aufgehoben | Nicht in Rechnung gestellt* |
| Zuordnung aufheben | Die VM wurde vollständig beendet und vom zugrunde liegenden Host entfernt. Das Aufheben der Zuordnung einer VM gilt als Aktualisierung. Daher werden ähnliche Bereitstellungsstatus wie bei der Aktualisierung angezeigt. | Zuordnung wird aufgehoben | Nicht in Rechnung gestellt* | 

&#42; Für einige Azure-Ressourcen, z. B. [Datenträger](https://azure.microsoft.com/pricing/details/managed-disks) und [Netzwerk](https://azure.microsoft.com/pricing/details/bandwidth/), fallen weiterhin Kosten an.

## <a name="os-provisioning-states"></a>Bereitstellungsstatus des Betriebssystems
Die Bereitstellungsstatus des Betriebssystems gelten nur für VMs, die mit einem Betriebssystemimage erstellt wurden. In spezialisierten Images werden diese Status nicht angezeigt. 

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/os-provisioning-states.png" alt-text="Abbildung der möglichen Bereitstellungsstatus des Betriebssystems einer VM":::

| Bereitstellungsstatus des Betriebssystems | Beschreibung | Betriebszustand | Abrechnung | 
|---|---|---|---|
| OSProvisioningInProgress | Die VM wird ausgeführt, und das Gastbetriebssystem wird installiert. | Wird ausgeführt | In Rechnung gestellt | 
| OSProvisioningComplete | Dies ist ein Zustand von kurzer Dauer. Der Status der VM geht schnell in **Erfolgreich** über. Wenn noch Erweiterungen installiert werden, wird dieser Status angezeigt, bis sie fertig sind. | Wird ausgeführt | In Rechnung gestellt | 
| Erfolgreich | Die vom Benutzer initiierten Aktionen wurden abgeschlossen. | Wird ausgeführt | In Rechnung gestellt | 
| Fehler | Stellt einen fehlerhaften Vorgang dar. Weitere Informationen und mögliche Lösungen können Sie den Fehlercodes entnehmen. | Wird ausgeführt  | In Rechnung gestellt | 


## <a name="next-steps"></a>Nächste Schritte
- [Dokumentation zu Azure Cost Management and Billing](../cost-management-billing/index.yml)
- Planen Ihrer Bereitstellungen mit dem [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/)
- Weitere Informationen zum Überwachen einer VM finden Sie unter[Überwachen von VMs in Azure](../azure-monitor/vm/monitor-vm-azure.md).
