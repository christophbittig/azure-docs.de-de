---
title: 'Tutorial: Aktivieren der Überwachung für virtuelle Azure-Computer'
description: Aktivieren Sie die Überwachung mit Erkenntnissen der virtuellen Computer in Azure Monitor, um einen virtuellen Azure-Computer zu überwachen.
ms.service: azure-monitor
ms.topic: article
ms.custom: subject-monitoring
ms.date: 11/04/2021
ms.openlocfilehash: 4156685d707a6e4fd6319a7130750838434f1efa
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350380"
---
# <a name="tutorial-enable-monitoring-for-azure-virtual-machine"></a>Tutorial: Aktivieren der Überwachung für virtuelle Azure-Computer
Um die Integrität und Leistung eines virtuellen Azure-Computers zu überwachen, müssen Sie einen Agent installieren, um Daten von seinem Gastbetriebssystem zu sammeln. VM Insights ist eine Funktion von Azure Monitor zur Überwachung des Gastbetriebssystems und von Workloads, die auf virtuellen Azure-Computern ausgeführt werden. Wenn Sie die Überwachung für einen virtuellen Azure-Computer aktivieren, werden die erforderlichen Agents installiert und es wird mit dem Sammeln von Leistungs-, Prozess- und Abhängigkeitsinformationen vom Gastbetriebssystem begonnen. 

> [!NOTE]
> Wenn Sie noch vollkommen unbedarft bei Azure Monitor sind, sollten Sie mit dem [Tutorial: Überwachen von Azure-Ressourcen mit Azure Monitor](../essentials/monitor-azure-resource.md) starten. Virtuelle Azure-Computer generieren ähnliche Überwachungsdaten wie andere Azure-Ressourcen wie Plattform-Metriken und Aktivitätsprotokolle. In diesem Tutorial wird beschrieben, wie Sie eine zusätzliche Überwachung aktivieren, die für virtuelle Computer eindeutig ist.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen Sie einen Log-Analytics-Arbeitsbereich, um Leistungs- und Protokolldaten vom virtuellen Computer zu sammeln.
> * Aktivieren Sie VM-Erkenntnisse für den virtuellen Computer (VM), der die erforderlichen Agents installiert und mit der Datensammlung beginnt. 
> * Untersuchen Sie Diagramme, in denen die vom virtuellen Computer gesammelten Leistungsdaten analysiert werden. 
> * Überprüfen Sie die Karte, die Prozesse, die auf dem virtuellen Computer ausgeführt werden, und Abhängigkeiten mit anderen Systemen zeigt.


> [!NOTE]
> VM Insights installiert den Log-Analytics-Agent, der Leistungsdaten vom Gastbetriebssystem virtueller Computer sammelt. Es sammelt keine Protokolle vom Gastbetriebssystem und sendet keine Leistungsdaten an Azure Monitor Metriken. Weitere Informationen in Bezug auf diese Funktion finden Sie unter [Tutorial: Sammeln von Gastprotokollen und -Metriken von einem virtuellen Azure-Computer](tutorial-monitor-vm-guest.md).

## <a name="prerequisites"></a>Voraussetzungen
Für dieses Tutorial benötigen Sie Folgendes: 

- Ein virtueller Azure-Computer zur Überwachung.



## <a name="create-a-log-analytics-workspace"></a>Erstellen eines Log Analytics-Arbeitsbereichs
[!INCLUDE [Create workspace](../../../includes/azure-monitor-tutorial-workspace.md)]


## <a name="enable-monitoring"></a>Aktivieren der Überwachung
Wählen Sie im Azure-Portal im Menü Ihres virtuellen Computers die Option **Erkenntnisse** aus. Wenn VM-Erkenntnisse noch nicht aktiviert wurde, sollte ein Bildschirm ähnlich dem folgenden angezeigt werden, auf dem Sie die Überwachung aktivieren können. Klicken Sie auf **Aktivieren**.

> [!NOTE]
> Wenn Sie beim Erstellen Ihres virtuellen Computers die Option **Detaillierte Überwachung aktivieren** ausgewählt haben, sind VM-Erkenntnisse möglicherweise bereits aktiviert. Wählen Sie Ihren Arbeitsbereich aus und klicken Sie erneut auf **Aktivieren**. Dies ist der Arbeitsbereich, an den von VM-Erkenntnissen gesammelte Daten gesendet werden.

:::image type="content" source="media/tutorial-monitor-vm/enable-vminsights-02.png" lightbox="media/tutorial-monitor-vm/enable-vminsights-02.png" alt-text="Aktivieren von VM-Erkenntnissen mit dem Arbeitsbereich":::

Es wird eine Meldung mit dem Text angezeigt, dass die Überwachung aktiviert ist. Es kann einige Minuten dauern, bis der Agent installiert ist und mit der Datensammlung begonnen wird. 

> [!NOTE]
> Möglicherweise erhalten Sie eine Meldung darüber, dass ein Upgrade für VM-Erkenntnisse verfügbar ist. Falls ja, wählen Sie die Option aus, das Upgrade durchzuführen, bevor Sie fortfahren.

## <a name="view-performance"></a>Anzeigen der Leistung
Wenn die Bereitstellung abgeschlossen ist, werden auf der Registerkarte **Leistung** in VM Insights Ansichten mit Leistungsdaten für den Computer angezeigt. Hier werden die Werte der wichtigsten Gastmetriken im Laufe der Zeit gezeigt. 

:::image type="content" source="media/tutorial-monitor-vm/performance.png" lightbox="media/tutorial-monitor-vm/performance.png" alt-text="VM Insights-Leistungsansicht":::

## <a name="view-processes-and-dependencies"></a>Anzeigen von Prozessen und Abhängigkeiten
Wählen Sie die Registerkarte **Karten** (Maps) aus, um Prozesse und Abhängigkeiten für den virtuellen Computer anzeigen zu lassen. Der aktuelle Computer befindet sich in der Mitte der Ansicht. Zeigen Sie die darauf ausgeführten Prozesse an, indem Sie **Prozesse** erweitern.

:::image type="content" source="media/tutorial-monitor-vm/map-processes.png" lightbox="media/tutorial-monitor-vm/map-processes.png" alt-text="Kartenansicht für VM-Erkenntnisse mit Prozessen":::


## <a name="view-machine-details"></a>Angaben zum Computer
Die **Karten**-Ansicht enthält verschiedene Registerkarten mit Informationen, die über den virtuellen Computer gesammelt wurden. Klicken Sie sich durch die Registerkarten, um zu sehen, was verfügbar ist.

:::image type="content" source="media/tutorial-monitor-vm/map-details.png" lightbox="media/tutorial-monitor-vm/map-details.png" alt-text="VM Insights Kartenansicht mit Angaben zum Computer":::

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie nun Daten vom virtuellen Computer gesammelt haben, können Sie diese Daten verwenden, um Warnungen zu erstellen, damit Sie proaktiv benachrichtigt werden können, wenn Probleme erkannt werden.

> [!div class="nextstepaction"]
> [Erstellen einer Warnung, wenn der virtuelle Azure-Computer nicht verfügbar ist](tutorial-monitor-vm-alert.md)

