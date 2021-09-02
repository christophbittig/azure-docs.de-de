---
title: Anzeigen und Konfigurieren von Warnungen für die Azure ExpressRoute-Leitungswartung
description: Erfahren Sie, wie Sie benutzerdefinierte Warnungen für die Wartung von ExpressRoute-Leitungen auf der Seite „Dienststatus“ im Azure-Portal konfigurieren.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: how-to
ms.date: 05/10/2021
ms.author: mialdrid
ms.openlocfilehash: 93335c6f163e016c619fd4803a595ffcc3418a9d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339478"
---
# <a name="how-to-view-and-configure-alerts-for-azure-expressroute-circuit-maintenance"></a>Anzeigen und Konfigurieren von Warnungen für die Azure ExpressRoute-Leitungswartung

ExpressRoute verwendet Azure Service Health, um Sie über geplante und bevorstehende Wartungen von ExpressRoute-Leitungen zu informieren. Mit Service Health können Sie geplante und vergangene Wartungen im Azure-Portal anzeigen und Warnungen und Benachrichtigungen so konfigurieren, dass sie Ihren Anforderungen am besten entsprechen. Weitere Informationen zu Azure Service Health finden Sie unter [Was ist Azure Service Health?](../service-health/overview.md)

> [!NOTE]
> * Während einer Wartungsaktivität oder bei ungeplanten Ereignissen, die sich auf eine der Verbindungen auswirken, bevorzugt Microsoft die AS-Pfadpräfigierung, um Datenverkehr zum Ausgleich auf die fehlerfreie Verbindung umzuleiten. Sie müssen sicherstellen, dass der Datenverkehr zum fehlerfreien Pfad umgeleitet werden kann, wenn Microsoft die Pfadpräfigierung konfiguriert hat, und dass erforderliche Umleitungsankündigungen entsprechend konfiguriert sind, um Dienstunterbrechungen zu vermeiden. 
> * Das Beenden von ExpressRoute-BGP-Verbindungen auf zustandsbehafteten Geräten kann bei geplanten oder ungeplanten Wartungen durch Microsoft oder Ihren ExpressRoute-Anbieter zu Problemen beim Failover führen. Sie sollten Ihre Einrichtung testen, um das ordnungsgemäße Failover Ihres Datenverkehrs sicherzustellen, und nach Möglichkeit BGP-Sitzungen auf zustandslosen Geräten beenden.
>

## <a name="view-planned-maintenance"></a>Anzeigen einer geplanten Wartung

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Navigieren Sie zur Seite „Dienststatus“. 

    :::image type="content" source="./media/maintenance-alerts/search-service-health.png" alt-text="Screenshot: Suche nach der Seite „Dienststatus“"::: 

1. Wählen Sie im linken Seitenbereich unter *Aktive Ereignisse* die Option **Geplante Wartung** aus. Auf dieser Seite können Sie einzelne Wartungsereignisse anzeigen, indem Sie nach einem Zielabonnement, einer Azure-Region und einem Azure-Dienst filtern.

    :::image type="content" source="./media/maintenance-alerts/view-maintenance.png" alt-text="Screenshot: Seite „Geplante Wartung“ in Service Health" lightbox="./media/maintenance-alerts/view-maintenance-expanded.png"::: 

1. Wählen Sie in der Dropdownliste *Dienste* die Option **ExpressRoute** aus, um nur ExpressRoute-bezogene Wartungen anzuzeigen. Wählen Sie dann ein Problem aus der Liste aus, um die Ereigniszusammenfassung anzuzeigen. Klicken Sie auf die Registerkarte **Problemupdates** aus, um weitere Details zu einer laufenden Wartung anzuzeigen.

    :::image type="content" source="./media/maintenance-alerts/summary.png" alt-text="Screenshot: ExpressRoute-Wartungszusammenfassung" lightbox="./media/maintenance-alerts/summary-expanded.png":::

## <a name="view-past-maintenance"></a>Anzeigen einer vergangenen Wartung

1. Wählen Sie zum Anzeigen früherer Wartungsereignisse unter dem Abschnitt *Verlauf* im linken Seitenbereich die Option **Integritätsverlauf** aus. 

    :::image type="content" source="./media/maintenance-alerts/health-history.png" alt-text="Screenshot: Auswählen des Integritätsverlaufs in Service Health" lightbox="./media/maintenance-alerts/health-history-expanded.png"::: 

1. Auf dieser Seite können Sie einzelne Wartungsereignisse anzeigen, indem Sie nach einem Zielabonnement und einer Azure-Region filtern. Um den Bereich von Integritätsverlaufsereignissen weiter einzugrenzen, können Sie den Typ des Integritätsereignisses auswählen und einen vergangenen Zeitraum definieren. Wenn Sie nach geplanten Wartungen für die ExpressRoute-Leitung filtern möchten, legen Sie den Typ des Integritätsereignisses auf **Geplante Wartung** fest.

    :::image type="content" source="./media/maintenance-alerts/past-maintenance.png" alt-text="Screenshot: Vergangene Wartung auf der Seite „Integritätsverlauf“" lightbox="./media/maintenance-alerts/past-maintenance-expanded.png"::: 

## <a name="create-alerts-and-notifications-for-maintenance-events"></a>Erstellen von Warnungen und Benachrichtigungen für Wartungsereignisse

1. Azure Service Health unterstützt benutzerdefinierte Warnungen für Wartungsereignisse. Navigieren Sie im linken Seitenbereich unter dem Abschnitt *Warnungen* zu **Integritätswarnungen**, um eine Warnung für die Wartung von ExpressRoute-Leitungen zu konfigurieren. Hier sehen Sie eine Tabelle mit zuvor konfigurierten Warnungen.

1.  Klicken Sie oben auf der Seite auf **Service Health-Warnung hinzufügen**, um eine neue Warnung zu erstellen.

    :::image type="content" source="./media/maintenance-alerts/health-alerts.png" alt-text="Screenshot: Auswählen von Integritätswarnungen in Service Health" lightbox="./media/maintenance-alerts/health-alerts-expanded.png"::: 

1. Wählen Sie die folgenden Informationen aus, oder geben Sie sie ein, um eine Warnungsregel zu erstellen.

    | Category | Einstellungen | Wert | 
    | --- | -------- | ----- |
    | **Condition** | Abonnement | Wählen Sie das Zielabonnement aus. |
    |               | Dienst(e) | *ExpressRoute \ ExpressRoute-Leitungen* |
    |               | Region(en) | Wählen Sie eine Region aus, oder behalten Sie die Einstellung *Global* bei, um Warnungen zu Integritätsereignissen für alle Regionen zu erhalten.
    |               | Ereignistyp | Wählen Sie *Geplante Wartung* aus. |
    | **Aktionen** | Aktionsgruppenname | Die *Aktionsgruppe* bestimmt den Benachrichtigungstyp und definiert die Zielgruppen, an die die Benachrichtigung gesendet wird. Unterstützung beim Erstellen und Verwalten der Aktionsgruppe finden Sie unter [Erstellen und Verwalten von Aktionsgruppen im Azure-Portal](../azure-monitor/alerts/action-groups.md). |
    | **Warnungsregeldetails** | Name der Warnungsregel | Geben Sie im Feld *Name* einen Wert ein, um Ihre Warnungsregel zu identifizieren. |
    |                        | BESCHREIBUNG | Geben Sie eine Beschreibung für die Funktion dieser Warnungsregel an. | 
    |                        | Warnungsregel in Ressourcengruppe speichern | Wählen Sie eine *Ressourcengruppe* aus, in der diese Warnungsregel erstellt werden soll. |
    |                        | Warnungsregel beim Erstellen aktivieren | Aktivieren Sie dieses Kontrollkästchen, um diese Warnungsregel zu aktivieren, sobald sie erstellt wurde. |

1. Wählen Sie **Warnungsregel erstellen** aus, um Ihre Konfiguration zu speichern.

    :::image type="content" source="./media/maintenance-alerts/create-alert-rule.png" alt-text="Screenshot der Seite &quot;Warnregel erstellen&quot;."::: 

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [Azure ExpressRoute](expressroute-introduction.md), [Network Insights](../azure-monitor/insights/network-insights-overview.md) und [Network Watcher](../network-watcher/network-watcher-monitoring-overview.md).
* [Anpassen Ihrer Metriken](expressroute-monitoring-metrics-alerts.md) und Erstellen eines [Verbindungsmonitors](../network-watcher/connection-monitor-overview.md)

