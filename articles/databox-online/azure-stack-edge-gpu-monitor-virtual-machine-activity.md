---
title: Überwachen der VM-Aktivität auf einem Azure Stack Edge Pro GPU-Gerät
description: Erfahren Sie, wie Sie die VM-Aktivität auf einem Azure Stack Edge Pro GPU-Gerät im Azure-Portal überwachen.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/03/2021
ms.author: alkohli
ms.openlocfilehash: aa1974347caa04c314e51b33daba47d7835026db
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124757608"
---
# <a name="monitor-vm-activity-on-your-azure-stack-edge-pro-gpu-device"></a>Überwachen der VM-Aktivität auf Ihrem Azure Stack Edge Pro GPU-Gerät

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In diesem Artikel wird beschrieben, wie Sie Aktivitätsprotokolle im Azure-Portal für virtuelle Computer auf Ihrem Azure Stack Edge Pro GPU-Gerät anzeigen.

> [!NOTE]
> Auf der Registerkarte **Metriken** für einen virtuellen Computer können Sie die CPU- und Arbeitsspeicherauslastung einer VM während der Aktivitätszeiträume genauer betrachten. Weitere Informationen finden Sie unter [Überwachen von VM-Metriken](azure-stack-edge-gpu-monitor-virtual-machine-metrics.md).

## <a name="view-activity-logs"></a>Anzeigen von Aktivitätsprotokollen

Führen Sie folgende Schritte aus, um Aktivitätsprotokolle für die virtuellen Computer auf Ihrem Azure Stack Edge Pro GPU-Gerät anzuzeigen:

1. Wechseln Sie zu dem Gerät und dann zu **Virtuelle Computer**. Wählen Sie **Aktivitätsprotokoll** aus.

    ![Screenshot des Bereichs „Aktivitätsprotokoll“ für virtuelle Computer auf einem Azure Stack Edge Gerät. Die Option „Aktivitätsprotokoll“ ist im Navigationsbereich hervorgehoben.](./media/azure-stack-edge-gpu-monitor-virtual-machine-activity/activity-log-01.png)

    Die VM-Gastprotokolle für virtuelle Computer auf dem Gerät werden angezeigt.

1. Verwenden Sie die Filter oberhalb der Liste, um die Anzeige auf die Aktivität zu begrenzen, die Sie sehen müssen.

    ![Screenshot der Optionen für „Zeitspanne“ auf der Seite „Aktivitätsprotokoll“ für virtuelle Computer. Die ausgewählte Zeitspanne ist hervorgehoben.](./media/azure-stack-edge-gpu-monitor-virtual-machine-activity/activity-log-02.png)<!--Reshoot to remove pointer. Lightbox treatment?-->

1. Klicken Sie neben einem Vorgangsnamen auf den Pfeil nach unten, um die zugehörige Aktivität anzuzeigen.

    ![Screenshot der Seite „Aktivitätsprotokoll“ für virtuelle Computer auf einem Azure Stack Edge Gerät. Ein erweiterter Vorgang ist in der Liste hervorgehoben.](./media/azure-stack-edge-gpu-monitor-virtual-machine-activity/activity-log-03.png)<!--Reshoot to remove pointer. May be able to replace drop-down only.-->

In jedem Bereich **Aktivitätsprotokoll** in Azure können Sie Aktivitäten filtern und sortieren, anzuzeigende Spalten auswählen, Details zu einer bestimmten Aktivität anzeigen und **Quick Insights** für Fehler, fehlerhafte Bereitstellungen, Warnungen, Dienstintegrität und Sicherheitsänderungen in den letzten 24 Stunden abrufen. Weitere Informationen zu den Protokollen und den Filteroptionen finden Sie unter [Anzeigen von Aktivitätsprotokollen](../azure-monitor/essentials/activity-log.md).

## <a name="next-steps"></a>Nächste Schritte

- [Problembehandlung bei der VM-Bereitstellung](azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning.md)
- [Erfassen von VM-Gastprotokollen in einem Supportpaket](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md)