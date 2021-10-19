---
title: Übersicht über die Wartungssteuerung für virtuelle Azure-Computer unter Verwendung des Azure-Portals
description: Es wird beschrieben, wie Sie die Wartung steuern, wenn diese mithilfe der Wartungssteuerung auf Ihre virtuellen Azure-Computer angewendet wird.
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/06/2021
ms.author: cynthn
ms.openlocfilehash: f04a1e776573ae8b335f1659057d89b2a144e051
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661234"
---
# <a name="managing-platform-updates-with-maintenance-control"></a>Verwalten von Plattformupdates mit der Wartungssteuerung 

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Verwalten Sie mit der Wartungssteuerung Plattformupdates, die keinen Neustart erfordern. Azure aktualisiert die eigene Infrastruktur häufig, um die Zuverlässigkeit, Leistung und Sicherheit zu verbessern oder neue Features zu integrieren. Die meisten Updates sind für Benutzer transparent. Einige empfindliche Workloads wie Gaming, Medienstreaming und Finanztransaktionen können nicht einmal wenige Sekunden tolerieren, in denen eine VM zur Wartung eingefroren oder getrennt wird. Die Wartungssteuerung bietet Ihnen die Möglichkeit, auf Plattformupdates zu warten und diese innerhalb eines 35-tägigen rollierenden Zeitfensters anzuwenden. 

Mit der Wartungssteuerung können Sie entscheiden, wann Sie Updates auf Ihre isolierten VMs und dedizierten Azure-Hosts anwenden.

Mit der Wartungssteuerung können Sie folgende Aktionen ausführen:
- Batchupdates in ein Updatepaket
- Warten von bis zu 35 Tagen bis zur Anwendung der Updates 
- Automatisieren von Plattformupdates durch Konfigurieren eines Wartungszeitplans
- Wartungskonfigurationen funktionieren abonnement- und ressourcengruppenübergreifend. 

## <a name="limitations"></a>Einschränkungen

- Virtuelle Computer müssen sich auf einem [dedizierten Host](./dedicated-hosts.md) befinden oder mithilfe einer [isolierten VM-Größe](isolation.md) erstellt werden.
- Die Dauer des Wartungsfensters kann monatlich variieren, und manchmal kann es bis zu zwei Stunden dauern, bis die ausstehenden Updates angewendet werden, nachdem sie vom Benutzer oder von der Benutzerin initiiert wurden.  
- Nach 35 Tagen wird automatisch ein Update angewendet.
- Die Wartung auf Rack-Ebene kann nicht über die Wartungskontrolle gesteuert werden.
- Der Benutzer muss über einen **Ressourcenmitwirkender**-Zugriff verfügen.

## <a name="management-options"></a>Verwaltungsoptionen

Wartungskonfigurationen können mithilfe einer der folgenden Optionen erstellt und verwaltet werden:

- [Azure-Befehlszeilenschnittstelle](maintenance-control-cli.md)
- [Azure PowerShell](maintenance-control-powershell.md)
- [Azure portal](maintenance-control-portal.md)

Ein Azure Functions-Beispiel finden Sie unter [Planen von Wartungsupdates mit der Wartungssteuerung und Azure Functions](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Wartung und Updates](maintenance-and-updates.md).
