---
title: 'Übersicht: Wartungssteuerung für Betriebssystemimage-Upgrades in Azure-VM-Skalierungsgruppen'
description: Es wird beschrieben, wie Sie steuern können, wann automatische Betriebssystemimage-Upgrades in Ihren Azure-VM-Skalierungsgruppen per Wartungssteuerung per Rollout bereitgestellt werden.
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/11/2020
ms.author: jushiman
ms.openlocfilehash: 60922ca7b2e910c883d39a89bebcd6de3c4b4ec8
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695749"
---
# <a name="maintenance-control-for-azure-virtual-machine-scale-sets"></a>Wartungssteuerung für Azure-VM-Skalierungsgruppen 

**Gilt für:** :heavy_check_mark: Einheitliche Skalierungsgruppen

Verwalten Sie [automatische Betriebssystemimage-Upgrades](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) für Ihre VM-Skalierungsgruppen per Wartungssteuerung.

Bei der Wartungssteuerung können Sie in einer einfacheren und besser vorhersagbaren Umgebung entscheiden, wann Updates auf Betriebssystemdatenträger in Ihren VM-Skalierungsgruppen angewendet werden sollen. 

Wartungskonfigurationen funktionieren abonnement- und ressourcengruppenübergreifend.

Der gesamte Workflow besteht aus den folgenden Schritten: 
- Erstellen einer Wartungskonfiguration
- Zuordnen einer VM-Skalierungsgruppe zu einer Wartungskonfiguration
- Aktivieren von automatischen Betriebssystemupgrades


## <a name="limitations"></a>Einschränkungen

- Die VMs müssen in einer Skalierungsgruppe enthalten sein.
- Der Benutzer muss über einen **Ressourcenmitwirkender**-Zugriff verfügen.
- Die Wartungsdauer in der Wartungskonfiguration muss mindestens fünf Stunden betragen.
- Die Wartungswiederholung muss in der Wartungskonfiguration auf „Day“ (Tag) festgelegt werden.


## <a name="management-options"></a>Verwaltungsoptionen

Wartungskonfigurationen können mithilfe einer der folgenden Optionen erstellt und verwaltet werden:

- [Azure PowerShell](virtual-machine-scale-sets-maintenance-control-powershell.md)
- [Azure-Befehlszeilenschnittstelle](virtual-machine-scale-sets-maintenance-control-cli.md)
- [Azure portal](virtual-machine-scale-sets-maintenance-control-portal.md)


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Steuerung der Wartung von VM-Skalierungsgruppen mit PowerShell](virtual-machine-scale-sets-maintenance-control-powershell.md)
