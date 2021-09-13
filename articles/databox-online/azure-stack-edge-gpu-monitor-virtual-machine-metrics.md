---
title: Überwachen von CPU und Arbeitsspeicher für virtuelle Computer auf Azure Stack Edge Pro GPU-Gerät
description: Hier erfahren Sie, wie Sie CPU- und Arbeitsspeichermetriken für VMs auf Azure Stack Edge Pro GPU-Geräten im Azure-Portal überwachen.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/03/2021
ms.author: alkohli
ms.openlocfilehash: c58b7fd1cf4ab08dd8d645e4fc5f537d9ee937e3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339730"
---
# <a name="monitor-vm-metrics-for-cpu-memory-on-azure-stack-edge-pro-gpu"></a>Überwachen von VM-Metriken für CPU, Arbeitsspeicher auf Azure Stack Edge Pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In diesem Artikel wird beschrieben, wie CPU- und Arbeitsspeichermetriken für einen virtuellen Computer auf Ihrem Azure Stack Edge Pro GPU-Gerät überwacht werden.

## <a name="about-vm-metrics"></a>Informationen zu VM-Metriken

Auf der Registerkarte **Metriken** für einen virtuellen Computer können Sie CPU- und Arbeitsspeichermetriken anzeigen, den Zeitraum anpassen und die gewünschten Zeiträume vergrößern.

Die VM-Metriken basieren auf CPU- und Arbeitsspeicherauslastungsdaten, die vom Gastbetriebssystem des virtuellen Computers gesammelt wurden. Die Ressourcennutzung wird einmal pro Minute erfasst.

Wenn ein Gerät getrennt wird, werden Metriken auf dem Gerät zwischengespeichert. Wenn das Gerät wieder verbunden wird, werden die Metriken per Push aus dem Cache übertragen, und die VM-**Metriken** werden aktualisiert.

## <a name="monitor-cpu-and-memory-metrics"></a>Überwachen von Metriken für CPU und Arbeitsspeicher

1. Öffnen Sie das Gerät im Azure-Portal, und navigieren Sie zu **Virtuelle Computer**. Wählen Sie den virtuellen Computer und **Metriken** aus.

    ![Screenshot: Registerkarte „Metriken“ für einen virtuellen Computer auf einem Azure Stack Edge Gerät Hervorgehobene Registerkarte „Metriken“](media/azure-stack-edge-gpu-monitor-virtual-machine-metrics/metrics-01.png)

2. Standardmäßig zeigen die Diagramme die durchschnittliche CPU- und Arbeitsspeicherauslastung für die vorherige Stunde an. Wählen Sie eine andere Option neben **Daten für letzte anzeigen** aus, um Daten für einen anderen Zeitraum anzuzeigen.

    ![Screenshot: Registerkarte „Metriken“ für einen virtuellen Computer auf einem Azure Stack Edge Gerät Die Option „Daten für letzte anzeigen“ und der ausgewählte Wert sind hervorgehoben.](./media/azure-stack-edge-gpu-monitor-virtual-machine-metrics/metrics-02.png)

3. Zeigen Sie mit der Maus auf eine beliebige Stelle in einem Diagramm, um eine vertikale Linie mit einer Hand anzuzeigen, die Sie nach links oder rechts verschieben können, um eine frühere oder spätere Datenstrichprobe anzuzeigen. Klicken Sie auf einen Bereich, um eine Detailansicht für diesen Zeitraum zu öffnen.

    ![Screenshot der Registerkarte „Metriken“ für einen virtuellen Computer Der Zeiger, der angezeigt wird, wenn Sie mit dem Mauszeiger auf einen Bereich eines Diagramms zeigen, ist hervorgehoben.](./media/azure-stack-edge-gpu-monitor-virtual-machine-metrics/metrics-03.png)


## <a name="next-steps"></a>Nächste Schritte

- [Überwachen der VM-Aktivität auf Ihrem Gerät](azure-stack-edge-gpu-monitor-virtual-machine-activity.md)
- [Erfassen von VM-Gastprotokollen in einem Supportpaket](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md)
