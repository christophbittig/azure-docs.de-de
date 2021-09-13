---
title: Ausmusterung der NV-Serie
description: Die Ausmusterung der NV-Serie beginnt am 1. September 2021
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 01/12/2020
ms.author: vikancha
ms.openlocfilehash: fcd0d460837195817018882d92b94b0012a14a6f
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122350928"
---
# <a name="migrate-your-nv-and-nv_promo-series-virtual-machines-by-august-31-2022"></a>Migrieren Sie Ihre VM der NV- und NV_Promo-Serie bis zum 31. August 2022
Da wir mithilfe der neuesten Innovationen im Bereich Datencentertechnologien kontinuierlich neue, modernere und optimierte VM-Instanzen in Azure bereitstellen, müssen wir die Ausmusterung der veralteten Hardware sorgfältig planen.
Vor diesem Hintergrund stellen wir die Azure VM-Größen aus der NV-Serie am 1. September 2022 ein.

## <a name="how-does-the-nv-series-migration-affect-me"></a>Wie wirkt sich die Migration der NV-Serie auf mich aus?  

Nach dem 1. September 2022 wird für alle in Ihrem Abonnement verbleibenden VMs mit NV- und NV_Promo-Größe der Status „Zuordnung aufgehoben“ eingestellt. Diese VMs werden beendet und vom Host entfernt. Im Zustand „Zuordnung aufgehoben“ werden diese VMs nicht mehr abgerechnet. 

Die aktuelle Ausmusterung von VM-Größen wirkt sich nur auf die VM-Größen in der [NV-Serie](nv-series.md) aus. Dies hat keinen Einfluss auf die VMs der [NVv3-](nvv3-series.md) und der [NVv4-](nvv4-series.md)Serie. 

## <a name="what-actions-should-i-take"></a>Welche Aktionen zieht das für mich nach sich?  

Sie müssen die Größe Ihrer VMs der NV-Serie ändern oder ihre Zuordnung aufheben. Es wird empfohlen, Ihre GPU-Visualisierungs-/Grafikworkloads in eine andere [VM-Größe mit GPU-Beschleunigung zu verschieben.](sizes-gpu.md)

[Weitere Informationen](nv-series-migration-guide.md) über das Migrieren Ihrer Workloads zu anderen Azure VM-Größen mit GPU-Beschleunigung. 

Sollten Sie Fragen haben, wenden Sie sich an unseren Kundensupport.
