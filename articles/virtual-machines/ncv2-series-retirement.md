---
title: Einstellung der NCv2-Serie
description: 'NCv2-Serie: Läuft am 31. August 2022 aus'
author: sherrywangms
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 09/01/2021
ms.author: sherrywang
ms.openlocfilehash: 7ed24690ff287c01b5b9c0e404a5f71349a021f5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700145"
---
# <a name="migrate-your-ncv2-series-virtual-machines-by-august-31-2022"></a>Migrieren Sie Ihre virtuellen Computer der NCv2-Serie bis zum 31. August 2022
Da wir mithilfe der neuesten Innovationen im Bereich Datencentertechnologien kontinuierlich neue, modernere und optimierte VM-Instanzen in Azure bereitstellen, müssen wir die Ausmusterung der veralteten Hardware sorgfältig planen. Aus diesem Grund stellen wir unsere NC (v2) GPU-VM-Größen ein, diese werden am 31. August 2022 von NVIDIA Tesla P100-GPUs betrieben. 

## <a name="how-does-the-ncv2-series-migration-affect-me"></a>Wie wirkt sich die Migration der NCv2-Serie auf mich aus?  

Nach dem 31. August 2022 werden alle in Ihrem Abonnement verbleibenden VMs der NCV2-Größe auf Status „Zuordnung aufgehoben“ gestellt. Diese VMs werden beendet und vom Host entfernt. Im Zustand „Zuordnung aufgehoben“ werden diese VMs nicht mehr abgerechnet. 

Das Auslaufen der VM-Größen betrifft nur die VM-Größen der [NCv2-Serien](ncv2-series.md). Dies hat keinen Einfluss auf die neueren VM der [NCv3](ncv3-series.md), [NC T4 v3](nct4-v3-series.md), und [ND v2](ndv2-series.md)-Serien. 

## <a name="what-actions-should-i-take"></a>Welche Aktionen zieht das für mich nach sich?  
Sie müssen die Größe Ihrer VMs der NC-Serie ändern oder ihre Zuordnung aufheben. Es wird empfohlen, Ihre GPU-Workloads in eine andere GPU-VM-Größe zu verschieben. Erfahren Sie mehr über das Migrieren Ihrer Workloads in eine andere [GPU-beschleunigte VM-Größe](sizes-gpu.md).

## <a name="next-steps"></a>Nächste Schritte

[Weitere Informationen](n-series-migration.md) über das Migrieren Ihrer Workloads zu anderen Azure VM-Größen mit GPU-Beschleunigung. 

Sollten Sie Fragen haben, wenden Sie sich an unseren Kundensupport.
