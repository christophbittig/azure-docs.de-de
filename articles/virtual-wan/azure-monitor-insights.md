---
title: Überwachen von Virtual WAN mithilfe von Azure Monitor Insights
description: Hier erfahren Sie mehr über die Überwachung von Azure Virtual WAN mithilfe von Azure Monitor Insights.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/22/2021
ms.author: cherylmc
ms.openlocfilehash: 73209f93e2b7fd96dc6ebb540d62624249db5be8
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2021
ms.locfileid: "112539230"
---
# <a name="azure-monitor-insights-for-virtual-wan"></a>Azure Monitor-Erkenntnisse für Virtual WAN

[Azure Monitor Insights](../azure-monitor/insights/network-insights-overview.md) für Azure Virtual WAN bietet Benutzern und Operatoren die Möglichkeit, den Zustand und Status eines virtuellen WAN über ein automatisch ermitteltes topologisches Diagramm anzuzeigen. Zustand und Status von Ressourcen werden im Diagramm überlagert, sodass Sie eine Momentaufnahmenansicht der Gesamtintegrität des virtuellen WAN erhalten. Sie können zwischen den Ressourcen im Diagramm mittels Ein-Klick-Zugriff auf die Seiten mit der Ressourcenkonfiguration im Virtual WAN-Portal navigieren.

Die Virtual WAN-Metriken auf Ressourcenebene werden mithilfe einer vorkonfigurierten Virtual WAN-Metrikarbeitsmappe gesammelt und dargestellt. Die Arbeitsmappe zeigt die Metriken für die Ebenen des virtuellen WAN, des Hubs, des Gateways und der Verbindungen. In diesem Artikel erfahren Sie, wie Sie mit Azure Monitor Insights für Virtual WAN Virtual WAN-Topologien und -Metriken an einer zentralen Stelle anzeigen können.

> [!NOTE]
> Die Menüoption **Erkenntnisse** finden Sie im Virtual WAN-Portal unter **Überwachung**. Sie können auch mithilfe von Azure Monitor für Netzwerke auf die Virtual WAN-Topologie und die Metrikarbeitsmappe zugreifen. Weitere Informationen finden Sie unter [Azure Monitor für Netzwerke](../azure-monitor/insights/network-insights-overview.md). 
>

## <a name="before-you-begin"></a>Vorbereitung

Zum Ausführen der in diesem Artikel beschriebenen Schritte benötigen Sie ein virtuelles WAN mit einem oder mehreren Hubs. Folgen Sie den Schritten in diesen Artikeln, um ein virtuelles WAN und einen Hub zu erstellen:

* [Erstellen eines virtuellen WAN](virtual-wan-site-to-site-portal.md#openvwan)
* [Erstellen eines Hubs](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-vwan-topology"></a><a name="topology"></a>Anzeigen einer VWAN-Topologie

Navigieren Sie zu **Azure-Portal** > **Virtual WAN**. Wählen Sie im Menü **Überwachen** auf der linken Seite **Insights (Vorschau)** aus. Die Ansicht **Insights** wird angezeigt. Sie zeigt das Virtual WAN-Abhängigkeitsdiagramm und die allgemeinen **Metrik**-Miniarbeitsmappen.

**Abbildung 1: Menü „Überwachen: Insights“**

:::image type="content" source="./media/azure-monitor-insights/monitor-menu.png" alt-text="Screenshot der Ansicht „Insights (Vorschau)“." lightbox="./media/azure-monitor-insights/monitor-menu.png":::

In der Ansicht **Insights-** können Sie die automatisch ermittelten Ressourcen des Virtual WAN anzeigen. Zu diesen Ressourcen zählen Hubs, Gateways, Firewalls, Verbindungen und virtuelle Spoke-Netzwerke, NVAs von Drittanbietern und Verzweigungen in einem virtuellen End-to-End-WAN. Ein Beispiel finden Sie in **Abbildung 2**.

Der Ressourcenzustand und der Ressourcenstatus sind farbcodiert und in den Ressourcensymbolen im Diagramm überlagert. Allgemeine Virtual WAN-Metriken, wie Hubkapazitäten und Gatewaynutzung, werden auf der rechten Seite des Fensters in einer Miniarbeitsmappe angezeigt.

**Abbildung 2: Insights-Ansicht**

:::image type="content" source="./media/azure-monitor-insights/insights-view.png" alt-text="Screenshot der Insights-Ansicht." lightbox="./media/azure-monitor-insights/insights-view.png":::

## <a name="dependency-view"></a><a name="dependency"></a>Abhängigkeitsansicht

Mit der **Abhängigkeitsansicht** für Virtual WAN kann die vernetzte Ansicht aller in einer Hub-und-Spoke-Architektur strukturierten Virtual WAN-Ressourcen besser visualisiert werden.

**Abbildung 3: Ansicht der VWAN-Abhängigkeiten**

:::image type="content" source="./media/azure-monitor-insights/dependency-map.png" alt-text="Screenshot der Abhängigkeitsansicht." lightbox="./media/azure-monitor-insights/dependency-map.png":::

In der Ansicht mit dem **Abhängigkeitsdiagramm** werden die folgenden Ressourcen als verbundenes Diagramm angezeigt:

* Virtual WAN-Hubs in den verschiedenen Azure-Regionen
* Virtuelle Spoke-Netzwerke, die direkt mit dem Hub verbunden sind.
* VPN- und Azure ExpressRoute-Zweigstellen und P2S-Benutzer, die mit den einzelnen Hubs über die jeweiligen ExpressRoute-, S2S- und P2S-Verbindungen und Gateways für virtuelle Netzwerke verbunden sind.
* In einem Hub (geschützten Hub) bereitgestellte Azure Firewalls (wie Firewallproxys von Drittanbietern).
* In einem virtuellen Spoke-Netzwerk bereitgestellte NVAs (Network Virtual Appliances) von Drittanbietern.

Im Abhängigkeitsdiagramm werden auch indirekt verbundene virtuelle Netzwerke angezeigt (d. h. virtuelle Netzwerke, die als Peer mit einem Virtual WAN virtuellen Spoke-Netzwerk verbunden sind).

Das Abhängigkeitsdiagramm ermöglicht eine einfache Navigation zu den Konfigurationseinstellungen der einzelnen Ressourcen. Beispielsweise können Sie mit dem Mauszeiger auf die Hubressource zeigen, um die grundlegende Ressourcenkonfiguration etwa von Hubregion und Hubpräfix anzuzeigen. Klicken Sie mit der rechten Maustaste, um auf die Azure-Portal-Seite der Hubressource zuzugreifen.

**Abbildung 4: Navigieren zu ressourcenspezifischen Informationen**

:::image type="content" source="./media/azure-monitor-insights/resource-information.png" alt-text="Screenshot, der die Navigation zu ressourcenspezifischen Informationen zeigt.":::

Die Such- und Filterleiste in der **Abhängigkeitsansicht** bietet eine einfache Möglichkeit, das Diagramm zu durchsuchen. Verschiedene Filter bieten Hilfe beim Eingrenzen der Suche auf einen bestimmten Pfad und Zustand.

**Abbildung 5: Suchen und Filtern**

:::image type="content" source="./media/azure-monitor-insights/search-filter.png" alt-text="Screenshot der Such- und Filterleiste." lightbox="./media/azure-monitor-insights/search-filter.png":::

## <a name="detailed-metrics"></a><a name="detailed"></a>Detaillierte Metriken

Sie können **Detaillierte Metriken anzeigen** auswählen, um die Seite mit den detaillierten **Metriken** aufzurufen. Die Seite **Metriken** ist ein Dashboard, das mit separaten Registerkarten vorkonfiguriert ist. Diese Registerkarten bieten Einblick in die Ressourcenkapazität, Leistung und Verwendung Ihres virtuellen WAN auf der Ebene des virtuellen WAN, des Hubs und der einzelnen Verbindungen.

**Abbildung 6: Dashboard „Detaillierte Metriken“**

:::image type="content" source="./media/azure-monitor-insights/detailed-metrics.png" alt-text="Screenshot des Dashboards mit der Metrik-Detaildarstellung" lightbox="./media/azure-monitor-insights/detailed-metrics.png":::.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen finden Sie unter [Metriken in Azure Monitor](../azure-monitor/essentials/data-platform-metrics.md).
* Eine umfassende Beschreibung aller Virtual WAN-Metriken finden Sie unter [Virtual WAN-Überwachung](monitor-virtual-wan.md).
