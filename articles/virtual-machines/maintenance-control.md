---
title: Übersicht über die Wartungssteuerung für virtuelle Azure-Computer unter Verwendung des Azure-Portals
description: Es wird beschrieben, wie Sie die Wartung steuern, wenn diese mithilfe der Wartungssteuerung auf Ihre virtuellen Azure-Computer angewendet wird.
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 11/19/2020
ms.author: cynthn
ms.openlocfilehash: b85614bce6ddaee98a271e3bf7f34fd3e0301580
ms.sourcegitcommit: 4f185f97599da236cbed0b5daef27ec95a2bb85f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/19/2021
ms.locfileid: "112369427"
---
# <a name="managing-platform-updates-with-maintenance-control"></a>Verwalten von Plattformupdates mit der Wartungssteuerung 

Verwalten Sie mit der Wartungssteuerung Plattformupdates, die keinen Neustart erfordern. Azure aktualisiert die eigene Infrastruktur häufig, um die Zuverlässigkeit, Leistung und Sicherheit zu verbessern oder neue Features zu integrieren. Die meisten Updates sind für Benutzer transparent. Einige empfindliche Workloads wie Gaming, Medienstreaming und Finanztransaktionen können nicht einmal wenige Sekunden tolerieren, in denen eine VM zur Wartung eingefroren oder getrennt wird. Die Wartungssteuerung bietet Ihnen die Möglichkeit, auf Plattformupdates zu warten und diese innerhalb eines 35-tägigen rollierenden Zeitfensters anzuwenden. 

Mit der Wartungssteuerung können Sie entscheiden, wann Sie Updates auf Ihre isolierten VMs und dedizierten Azure-Hosts anwenden.

Mit der Wartungssteuerung können Sie folgende Aktionen ausführen:
- Batchupdates in ein Updatepaket
- Warten von bis zu 35 Tagen bis zur Anwendung der Updates 
- Automatisieren von Plattformupdates durch Konfigurieren eines Wartungszeitplans
- Wartungskonfigurationen funktionieren abonnement- und ressourcengruppenübergreifend. 

## <a name="limitations"></a>Einschränkungen

- Virtuelle Computer müssen sich auf einem [dedizierten Host](./dedicated-hosts.md) befinden oder mithilfe einer [isolierten VM-Größe](isolation.md) erstellt werden.
- Die Dauer des Wartungsfensters muss mindestens zwei Stunden betragen. Dabei handelt es sich um die Zeit zwischen dem Initiieren des Updates durch den Kunden und dem Abschluss des Updates. 
- Nach 35 Tagen wird automatisch ein Update angewendet.
- Der Benutzer muss über einen **Ressourcenmitwirkender**-Zugriff verfügen.

## <a name="management-options"></a>Verwaltungsoptionen

Wartungskonfigurationen können mithilfe einer der folgenden Optionen erstellt und verwaltet werden:

- [Azure-Befehlszeilenschnittstelle](maintenance-control-cli.md)
- [Azure PowerShell](maintenance-control-powershell.md)
- [Azure portal](maintenance-control-portal.md)

Ein Azure Functions-Beispiel finden Sie unter [Planen von Wartungsupdates mit der Wartungssteuerung und Azure Functions](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Wartung und Updates](maintenance-and-updates.md).
