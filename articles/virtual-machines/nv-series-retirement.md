---
title: Ausmusterung der NV-Serie
description: Die Ausmusterung der NV-Serie beginnt am 1. September 2021
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 01/12/2020
ms.author: vikancha
ms.openlocfilehash: 666b681d33c41482bcf33947c38be33aeee29783
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123436444"
---
# <a name="migrate-your-nv-and-nv_promo-series-virtual-machines-by-august-31-2022"></a>Migrieren Sie Ihre VM der NV- und NV_Promo-Serie bis zum 31. August 2022

Wir führen weiterhin moderne und optimierte VM-Instanzen in Azure ein, indem wir die neuesten Innovationen im Bereich der Rechenzentrumstechnologien nutzen. Während wir innovativ sind, planen wir auch sorgfältig, wie wir veraltete Hardware ausmustern. Vor diesem Hintergrund werden wir unsere Azure-VM-Größen der NV-Serie am 1. September 2022 einstellen.

## <a name="how-does-the-nv-series-migration-affect-me"></a>Wie wirkt sich die Migration der NV-Serie auf mich aus?

Nach dem 1. September 2022 wird für alle in Ihrem Abonnement verbleibenden VMs mit NV- und NV_Promo-Größe der Status „Zuordnung aufgehoben“ festgelegt. Diese VMs werden beendet und vom Host entfernt. Im Zustand „Zuordnung aufgehoben“ werden diese VMs nicht mehr abgerechnet. 

Die aktuelle Ausmusterung von VM-Größen wirkt sich nur auf die VM-Größen in der [NV-Serie](nv-series.md) aus. Diese Ausmusterung betrifft nicht die VMs der Serien [NVv3](nvv3-series.md) und [NVv4](nvv4-series.md). 

## <a name="what-actions-should-i-take"></a>Welche Aktionen zieht das für mich nach sich?

Sie müssen die Größe Ihrer NV-VMs ändern oder deren Zuordnung aufheben. Es wird empfohlen, Ihre GPU-Visualisierungs-/Grafikworkloads in eine andere [VM-Größe mit GPU-Beschleunigung zu verschieben.](sizes-gpu.md)

[Weitere Informationen](nv-series-migration-guide.md) zum das Migrieren Ihrer Workloads zu anderen Azure VM-Größen mit GPU-Beschleunigung. 

Sollten Sie Fragen haben, wenden Sie sich an unseren Kundensupport.
