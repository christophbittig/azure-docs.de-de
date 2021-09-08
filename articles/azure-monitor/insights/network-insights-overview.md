---
title: Azure Monitor Netzwerk-Insights
description: Ein Überblick über Azure Monitor Network Insights, das eine umfassende Ansicht des Zustands und der Metriken für alle bereitgestellten Netzwerkressourcen ohne jegliche Konfiguration bietet.
ms.topic: conceptual
author: KumudD
ms.author: kumud
ms.date: 11/25/2020
ms.openlocfilehash: a4b433ed7a2eb0a2a9367f921e63091a823df37d
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122356128"
---
# <a name="azure-monitor-network-insights"></a>Azure Monitor Netzwerk-Insights

Azure Monitor Network Insights bietet eine umfassende Übersicht der [Integrität](../../service-health/resource-health-checks-resource-types.md) und [Metriken](../essentials/metrics-supported.md) für alle bereitgestellten Netzwerkressourcen, ohne dass eine Konfiguration erforderlich ist. Außerdem erhalten Sie Zugriff auf Netzwerküberwachungsfunktionen wie [Verbindungsmonitor](../../network-watcher/connection-monitor-overview.md), [Datenflussprotokollierung für Netzwerksicherheitsgruppen (NSGs)](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) und [Traffic Analytics](../../network-watcher/traffic-analytics.md). Zudem werden weitere Features für die [Netzwerkdiagnose](../../network-watcher/network-watcher-monitoring-overview.md#diagnostics) bereitgestellt.

Azure Monitor Network Insights ist um diese Schlüsselkomponenten der Überwachung herum aufgebaut:
- [Netzwerkintegrität und Metriken](#networkhealth)
- [Konnektivität](#connectivity)
- [Verkehr](#traffic)
- [Diagnosetoolkit](#diagnostictoolkit)

## <a name="network-health-and-metrics"></a><a name="networkhealth"></a>Netzwerkintegrität und Metriken

Die **Übersichtsseite** von Azure Monitor Network Insights bietet eine einfache Möglichkeit, das Inventar Ihrer Netzwerkressourcen zusammen mit dem Status der Ressourcen und Warnungen zu visualisieren. Sie enthält vier Hauptfunktionsbereiche: „Suchen und Filtern“, „Ressourcenintegrität und Metriken“, „Warnungen“ und „Abhängigkeitsansicht“.

[![Screenshot: Seite „Übersicht“](media/network-insights-overview/overview.png)](media/network-insights-overview/overview.png#lightbox)

### <a name="search-and-filtering"></a>Suchen und Filtern
Sie können die Ansicht für Ressourcenintegrität und Warnungen mithilfe von Filtern wie **Abonnement**, **Ressourcengruppe** und **Typ** anpassen.

Mit dem Suchfeld können Sie nach Ressourcen und deren zugeordneten Ressourcen suchen. Beispielsweise ist einem Anwendungsgateway eine öffentliche IP-Adresse zugeordnet. Bei einer Suche nach dem DNS-Namen der öffentlichen IP-Adresse werden sowohl die öffentliche IP-Adresse als auch das zugeordnete Anwendungsgateway zurückgegeben:

[![Screenshot, der die Suchergebnisse von Azure Monitor Network Insights anzeigt.](media/network-insights-overview/search.png)](media/network-insights-overview/search.png#lightbox)


### <a name="resource-health-and-metrics"></a>Ressourcenintegrität und Metriken
Im folgenden Beispiel stellt jede Kachel einen Ressourcentyp dar. Die Kachel zeigt die Anzahl der Instanzen dieses Ressourcentyps, die in allen ausgewählten Abonnements bereitgestellt werden. Außerdem wird der Integritätsstatus der Ressource angezeigt. In diesem Beispiel werden 105 ER-und VPN-Verbindungen bereitgestellt. Davon sind 103 fehlerfrei und 2 nicht verfügbar.

![Screenshot, der den Ressourcenstatus und die Metriken in Azure Monitor Network Insights anzeigt.](media/network-insights-overview/resource-health.png)

Wenn Sie die nicht verfügbaren ER- und VPN-Verbindungen auswählen, wird eine Metrikansicht angezeigt: 

![Screenshot, der die Metrik-Ansicht in Azure Monitor Network Insights anzeigt.](media/network-insights-overview/metric-view.png)

Sie können ein beliebiges Element in der Rasteransicht auswählen. Wählen Sie das Symbol in der Spalte **Integrität** aus, um die Ressourcenintegrität für diese Verbindung abzurufen. Wählen Sie den Wert in der Spalte **Warnung** aus, um zur Seite mit Warnungen und Metriken für die Verbindung zu gelangen. 

### <a name="alerts"></a>Warnungen
Das Feld **Warnung** rechts auf der Seite bietet eine Übersicht über alle Warnungen, die für die ausgewählten Ressourcen in allen Abonnements generiert wurden. Wählen Sie die Warnungsanzahl aus, um zu einer Detailseite für die Warnungen zu gelangen.

### <a name="dependency-view"></a>Abhängigkeitsansicht
Mithilfe der Abhängigkeitsansicht können Sie die Konfiguration einer Ressource visuell darstellen. Die Abhängigkeitsansicht ist derzeit für Azure Application Gateway, Azure Virtual WAN und Azure Load Balancer verfügbar. Im Fall von Application Gateway können Sie beispielsweise auf die Abhängigkeitsansicht zugreifen, indem Sie in der Rasteransicht der Metriken den Namen der Application Gateway-Ressource auswählen. Das Gleiche gilt für Virtual WAN und Load Balancer.

![Screenshot, der die Anwendungs-Gateway-Ansicht in Azure Monitor Network Insights anzeigt.](media/network-insights-overview/application-gateway.png)

Die Abhängigkeitsansicht für Application Gateway bietet eine vereinfachte Übersicht darüber, wie die Front-End-IP-Adressen mit den Listenern, den Regeln und dem Back-End-Pool verbunden sind. Die Verbindungslinien sind farbcodiert und stellen zusätzliche Details basierend auf der Integrität des Back-End-Pools bereit. Die Ansicht bietet auch eine detaillierte Übersicht über Application Gateway-Metriken und Metriken für alle zugehörigen Back-End-Pools wie VM-Skalierungsgruppen und VM-Instanzen.

[![Screenshot, der die Abhängigkeitenansicht in Azure Monitor Network Insights anzeigt.](media/network-insights-overview/dependency-view.png)](media/network-insights-overview/dependency-view.png#lightbox)

Das Abhängigkeitsdiagramm bietet eine einfache Navigation zu Konfigurationseinstellungen. Klicken Sie mit der rechten Maustaste auf einen Back-End-Pool, um auf weitere Informationen zuzugreifen. Wenn es sich beim Back-End-Pool beispielsweise um eine VM handelt, können Sie direkt auf VM Insights und die Fehlerbehebung für Azure Network Watcher-Verbindungen zugreifen, um Verbindungsprobleme zu ermitteln:

![Screenshot, der das Menü der Abhängigkeitsansicht in Azure Monitor Network Insights anzeigt.](media/network-insights-overview/dependency-view-menu.png)

Die Such- und Filterleiste in der Abhängigkeitsansicht bietet eine einfache Möglichkeit, das Diagramm zu durchsuchen. Wenn Sie z. B. im vorherigen Beispiel nach **AppGWTestRule** suchen, wird die Ansicht auf alle über AppGWTestRule verbundenen Knoten eingegrenzt:

![Screenshot, das ein Beispiel für eine Suche in Azure Monitor Network Insights anzeigt.](media/network-insights-overview/search-example.png)

Mithilfe verschiedener Filter können Sie die Ansicht auf einen bestimmten Pfad und Zustand eingrenzen. Wählen Sie z. B. in der Liste **Integritätsstatus** nur **Fehlerhaft** aus, um alle Edge-Geräte anzuzeigen, die den Status „fehlerhaft“ aufweisen.

Wählen Sie **Detaillierte Metriken anzeigen** aus, um eine vorkonfigurierte Arbeitsmappe zu öffnen, die detaillierte Metriken für das Anwendungsgateway, alle Back-End-Poolressourcen und Front-End-IP-Adressen enthält. 

## <a name="connectivity"></a><a name="connectivity"></a>Konnektivität

Auf der Registerkarte **Konnektivität** können Sie auf einfache Weise alle über [Verbindungsmonitor](../../network-watcher/connection-monitor-overview.md) und Verbindungsmonitor (klassisch) konfigurierten Tests für den ausgewählten Satz von Abonnements visualisieren.

![Screenshot, der die Registerkarte Konnektivität in Azure Monitor Network Insights anzeigt.](media/network-insights-overview/azure-monitor-for-networks-connectivity-tab.png)

Die Tests sind in den Kacheln **Quellen** und **Ziele** gruppiert und zeigen den Status der Erreichbarkeit für jeden Test an. Erreichbarkeitseinstellungen bieten einfachen Zugriff auf Konfigurationen für die Erreichbarkeitskriterien auf Grundlage der Überprüfungen mit Fehlern (%) und Roundtripzeit (ms). Nachdem Sie die Werte festgelegt haben, wird der Status für jeden Test basierend auf den Auswahlkriterien aktualisiert.

[![Screenshot, der Konnektivitätstests in Azure Monitor Network Insights anzeigt.](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png)](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png#lightbox)

Sie können eine beliebige Quell- oder Zielkachel auswählen, um eine Metrikansicht zu öffnen:

[![Screenshot, der Konnektivitätsmetriken in Azure Monitor Network Insights anzeigt.](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png)](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png#lightbox)


Sie können ein beliebiges Element in der Rasteransicht auswählen. Wählen Sie das Symbol in der Spalte **Erreichbarkeit** aus, um zur Portalseite „Verbindungsmonitor“ zu gelangen und die Hop-by-Hop-Topologie sowie die identifizierten Konnektivitätsprobleme anzuzeigen. Wählen Sie den Wert in der Spalte **Warnung** aus, um zu Warnungen zu gelangen. Wählen Sie die Diagramme in den Spalten **Fehler bei Überprüfungen in Prozent** und **Roundtripzeit (ms)** aus, um zur Seite mit Metriken für den ausgewählten Verbindungsmonitor zu wechseln.

Das Feld  **Warnung** rechts auf der Seite bietet eine Übersicht über alle Warnungen, die für die konfigurierten Konnektivitätstests in allen Abonnements generiert wurden. Wählen Sie die Warnungsanzahl aus, um zu einer Detailseite für die Warnungen zu gelangen.

## <a name="traffic"></a><a name="traffic"></a>Verkehr
Die Registerkarte **Datenverkehr** ermöglicht den Zugriff auf alle für [NSG-Datenflussprotokolle](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) und [Traffic Analytics](../../network-watcher/traffic-analytics.md) konfigurierten NSGs für den ausgewählten Satz von Abonnements gruppiert nach Standort. Die Suchfunktion auf dieser Registerkarte ermöglicht Ihnen die Identifizierung der NSGs, die für die gesuchte IP-Adresse konfiguriert sind. Sie können eine beliebige IP-Adresse in Ihrer Umgebung suchen. In der regionalen Kachelansicht werden alle NSGs zusammen mit den NSG-Datenflussprotokollen und dem Konfigurationsstatus von Traffic Analytics angezeigt.

[![Screenshot, der die Registerkarte „Traffic" in Azure Monitor Network Insights anzeigt.](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png)](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png#lightbox)

Wenn Sie eine Regionskachel auswählen, wird eine Rasteransicht angezeigt. Das Raster bietet NSG-Datenflussprotokolle und Traffic Analytics in einer Ansicht, die leicht zu lesen und zu konfigurieren ist:  

[![Screenshot, der die Ansicht der Verkehrsregion in Azure Monitor Network Insights anzeigt.](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png)](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png#lightbox)

Sie können ein beliebiges Element in der Rasteransicht auswählen. Wählen Sie das Symbol in der Spalte **Datenflussprotokoll-Konfigurationsstatus** aus, um das NSG-Datenflussprotokoll und die Traffic Analytics-Konfiguration zu bearbeiten. Wählen Sie den Wert in der Spalte **Warnung** aus, um zu den für die ausgewählte NSG konfigurierten Datenverkehrswarnungen zu gelangen. Auf ähnliche Weise können Sie die Traffic Analytics-Ansicht aufrufen, indem Sie den **Traffic Analytics-Arbeitsbereich** auswählen.  

Das Feld  **Warnung** rechts auf der Seite bietet eine Übersicht über alle auf dem Traffic Analytics-Arbeitsbereich basierenden Warnungen für alle Abonnements. Wählen Sie die Warnungsanzahl aus, um zu einer Detailseite für die Warnungen zu gelangen.

## <a name="diagnostic-toolkit"></a><a name="diagnostictoolkit"></a> Diagnosetoolkit
Das Diagnosetoolkit bietet Zugriff auf alle Diagnosefeatures, die für das Behandeln von Netzwerkproblemen verfügbar sind. Mithilfe dieser Dropdownliste können Sie auf Features wie [Paketerfassung](../../network-watcher/network-watcher-packet-capture-overview.md), [VPN-Problembehandlung](../../network-watcher/network-watcher-troubleshoot-overview.md), [Problembehandlung für Verbindungen](../../network-watcher/network-watcher-connectivity-overview.md), [Nächster Hop](../../network-watcher/network-watcher-next-hop-overview.md) und [IP-Datenflussüberprüfung](../../network-watcher/network-watcher-ip-flow-verify-overview.md) zugreifen:

![Screenshot der Registerkarte „Diagnosetoolkit“](media/network-insights-overview/azure-monitor-for-networks-diagnostic-toolkit.png)

## <a name="availability-of-resources"></a>Verfügbarkeit von Ressourcen 

Standardmäßig sind alle Netzwerkressourcen in Network Insights sichtbar. Kunden können auf den Ressourcentyp klicken, um Ressourcenintegrität und Metriken (falls verfügbar), Abonnementdetails, Standort usw. anzuzeigen. Eine Teilmenge der Netzwerkressourcen wurde _integriert_. Bei integrierten Ressourcen haben Kunden Zugriff auf eine ressourcenspezifische Topologieansicht und eine integrierte Arbeitsmappe mit Metriken. Diese sofort einsatzbereiten Funktionen erleichtern das Untersuchen von Ressourcenmetriken und das Beheben von Problemen.  

Die folgenden Ressourcen wurden integriert: 
* Virtuelles WAN
* Application Gateway
* Load Balancer
* ExpressRoute
* Private Link
* NAT Gateway
* Öffentliche IP-Adresse
* NIC

## <a name="troubleshooting"></a>Problembehandlung 
Allgemeine Anleitungen zur Problembehandlung finden Sie im Artikel zur [Problembehandlung für arbeitsmappenbasierte Erkenntnisse](troubleshoot-workbooks.md).
Dieser Abschnitt hilft Ihnen bei der Diagnose und Fehlerbehebung einiger häufiger Probleme, die bei der Verwendung von Azure Monitor Network Insights auftreten können. 

### <a name="how-do-i-resolve-performance-problems-or-failures"></a>Wie behebe ich Leistungsprobleme oder Fehler?

Informationen zur Fehlerbehebung von netzwerkbezogenen Problemen, die Sie mit Azure Monitor Network Insights identifizieren, finden Sie in der Fehlerbehebungsdokumentation der fehlerhaft funktionierenden Ressource. 

Nachfolgend sind einige Links zu Artikeln für die Problembehandlung bei häufig verwendeten Diensten aufgelistet. Weitere Artikel zur Problembehandlung für diese Dienste finden Sie in den anderen Artikeln, die im Abschnitt „Problembehandlung“ im Inhaltsverzeichnis für den jeweiligen Dienst aufgeführt sind.
* [Azure Virtual Network](../../virtual-network/virtual-network-troubleshoot-peering-issues.md)
* [Azure Application Gateway](../../application-gateway/create-gateway-internal-load-balancer-app-service-environment.md)
* [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-troubleshoot.md)
* [Azure ExpressRoute](../../expressroute/expressroute-troubleshooting-expressroute-overview.md) 
* [Azure-Lastenausgleich](../../load-balancer/load-balancer-troubleshoot.md) 

### <a name="why-dont-i-see-the-resources-for-all-the-subscriptions-ive-selected"></a>Warum werden nicht die Ressourcen für alle Abonnements angezeigt, die ich ausgewählt habe?

Azure Monitor Network Insights kann Ressourcen nur für fünf Abonnements gleichzeitig anzeigen. 

### <a name="how-do-i-make-changes-or-add-visualizations-to-azure-monitor-network-insights"></a>Wie kann ich Änderungen an Azure Monitor Network Insights vornehmen oder Visualisierungen hinzufügen?

Wenn Sie Änderungen vornehmen möchten, wählen Sie **Bearbeitungsmodus** aus, um die Arbeitsmappe zu ändern. Anschließend können Sie die Änderungen als neue Arbeitsmappe speichern, die an ein bestimmtes Abonnement und eine bestimmte Ressourcengruppe gebunden ist.

### <a name="whats-the-time-grain-after-i-pin-any-part-of-the-workbooks"></a>Welches Aggregationsintervall wird verwendet, nachdem ich einen Teil der Arbeitsmappen angeheftet habe?

Azure Monitor Network Insights verwendet das **Auto** - Zeitkorn, d. h., das Zeitkorn basiert auf dem ausgewählten Zeitbereich.

### <a name="whats-the-time-range-when-any-part-of-a-workbook-is-pinned"></a>Welcher Zeitbereich wird verwendet, wenn ein Teil einer Arbeitsmappe angeheftet wird?

Der Zeitbereich hängt von den Dashboardeinstellungen ab.

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-azure-monitor-network-insights"></a>Was kann ich tun, wenn ich andere Daten anzeigen oder eigene Visualisierungen erstellen möchte? Wie kann ich Änderungen an Azure Monitor Network Insights vornehmen?

Sie können die Arbeitsmappe, die in einem Seitenbereich und der Ansicht mit detaillierten Metriken angezeigt wird, mithilfe des Bearbeitungsmodus bearbeiten. Anschließend können Sie die Änderungen als eine neue Arbeitsmappe speichern.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die Netzwerküberwachung: [Was ist Azure Network Watcher?](../../network-watcher/network-watcher-monitoring-overview.md)
- Informieren Sie sich über die Szenarien, die Arbeitsmappen unterstützen sollen, wie Sie Berichte erstellen und vorhandene Berichte anpassen können und vieles mehr: [Erstellen interaktiver Berichte mit Azure Monitor-Arbeitsmappen](../visualize/workbooks-overview.md)
