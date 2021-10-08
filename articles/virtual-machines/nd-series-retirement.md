---
title: Einstellung der ND-Serie
description: 'ND-Serie: Läuft am 31. August 2022 aus'
author: sherrywangms
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 09/01/2021
ms.author: sherrywang
ms.openlocfilehash: 7111af4f275f97ff35e4a0cf9846eb6b68a807f9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700451"
---
# <a name="migrate-your-nd-series-virtual-machines-by-august-31-2022"></a>Migrieren Sie Ihre virtuellen Computer der ND-Serie bis zum 31. August 2022
Da wir mithilfe der neuesten Innovationen im Bereich Rechenzentrumstechnologien kontinuierlich neue, modernere und optimierte VM-Instanzen in Azure bereitstellen, müssen wir die Außerbetriebnahme der veralteten Hardware sorgfältig planen. Aus diesem Grund stellen wir unsere ND GPU-VM-Größen ein, diese werden am 31. August 2022 von NVIDIA Tesla P40-GPUs betrieben. 

## <a name="how-does-the-nd-series-migration-affect-me"></a>Wie wirkt sich die Migration der ND-Serie auf mich aus?  

Nach dem 31. August 2022 werden alle in Ihrem Abonnement verbleibenden VMs der ND-Größe in den Zustand „Zuordnung aufgehoben“ versetzt. Diese VMs werden beendet und vom Host entfernt. Im Zustand „Zuordnung aufgehoben“ werden diese VMs nicht mehr abgerechnet. 

Das aktuelle Auslaufen von VM-Größen wirkt sich nur auf die VM-Größen in der [ND-Serie](nd-series.md) aus. Dies hat keinen Einfluss auf die neueren VM der [NCv3](ncv3-series.md), [NC T4 v3](nct4-v3-series.md), und [ND v2](ndv2-series.md)-Serien. 

## <a name="what-actions-should-i-take"></a>Welche Aktionen zieht das für mich nach sich?  
Sie müssen die Größe Ihrer VMs der NC-Serie ändern oder ihre Zuordnung aufheben. Es wird empfohlen, Ihre GPU-Workloads in eine andere GPU-VM-Größe zu verschieben. Erfahren Sie mehr über das Migrieren Ihrer Workloads in eine andere [GPU-beschleunigte VM-Größe](sizes-gpu.md).

## <a name="next-steps"></a>Nächste Schritte
[Weitere Informationen](n-series-migration.md) über das Migrieren Ihrer Workloads zu anderen Azure VM-Größen mit GPU-Beschleunigung. 

Sollten Sie Fragen haben, wenden Sie sich an unseren Kundensupport.
