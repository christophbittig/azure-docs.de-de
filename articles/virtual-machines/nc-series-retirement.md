---
title: Einstellung der NC-Serie
description: 'NC-Serie: Läuft am 31. August 2022 aus'
author: sherrywangms
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 09/01/2021
ms.author: sherrywang
ms.openlocfilehash: e95c32903f601a883b62a06eb1b1901c15042dbb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700453"
---
# <a name="migrate-your-nc-and-nc_promo-series-virtual-machines-by-august-31-2022"></a>Migrieren Sie Ihre VM der NC- und NC_Promo-Serie bis zum 31. August 2022
Da wir mithilfe der neuesten Innovationen im Bereich Rechenzentrumstechnologien kontinuierlich neue, modernere und optimierte VM-Instanzen in Azure bereitstellen, müssen wir die Außerbetriebnahme der veralteten Hardware sorgfältig planen. Aus diesem Grund stellen wir unsere NC (v1) GPU-VM-Größen ein, diese werden am 31. August 2022 von NVIDIA Tesla K80-GPUs betrieben. 

## <a name="how-does-the-nc-series-migration-affect-me"></a>Wie wirkt sich die Migration der NC-Serie auf mich aus?  

Nach dem 31. August 2022 werden alle in Ihrem Abonnement verbleibenden VMs der NC-Größe in den Zustand „Zuordnung aufgehoben“ versetzt. Diese VMs werden beendet und vom Host entfernt. Im Zustand „Zuordnung aufgehoben“ werden diese VMs nicht mehr abgerechnet. 

Das aktuelle Auslaufen von VM-Größen wirkt sich nur auf die VM-Größen in der [NC-Serie](nc-series.md) aus. Dies hat keinen Einfluss auf die neueren VM der [NCv3](ncv3-series.md), [NC T4 v3](nct4-v3-series.md), und [ND v2](ndv2-series.md)-Serien. 


## <a name="what-actions-should-i-take"></a>Welche Aktionen zieht das für mich nach sich?  
Sie müssen die Größe Ihrer VMs der NC-Serie ändern oder ihre Zuordnung aufheben. Es wird empfohlen, Ihre GPU-Workloads in eine andere GPU-VM-Größe zu verschieben. Erfahren Sie mehr über das Migrieren Ihrer Workloads in eine andere [GPU-beschleunigte VM-Größe](sizes-gpu.md).

## <a name="next-steps"></a>Nächste Schritte

[Weitere Informationen](n-series-migration.md) über das Migrieren Ihrer Workloads zu anderen Azure VM-Größen mit GPU-Beschleunigung. 

Sollten Sie Fragen haben, wenden Sie sich an unseren Kundensupport.
