---
title: 'Tutorial: Überwachen eines Hybridcomputers mit Azure Monitor VM Insights'
description: Hier erfahren Sie, wie Sie Daten von einem Hybridcomputer in Azure Monitor sammeln und analysieren.
ms.topic: tutorial
ms.date: 04/21/2021
ms.openlocfilehash: 5489e459bc4d407b818c1f55afe0f7fa3c9acbb8
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124807523"
---
# <a name="tutorial-monitor-a-hybrid-machine-with-vm-insights"></a>Tutorial: Überwachen eines Hybridcomputers mit VM Insights

[Azure Monitor](../../../azure-monitor/overview.md) kann Daten direkt von Ihren Hybridcomputern zur detaillierten Analyse und Korrelation in einem Log Analytics-Arbeitsbereich sammeln. Normalerweise müssten Sie dazu den [Log Analytics-Agent](../../../azure-monitor/agents/agents-overview.md#log-analytics-agent) mithilfe eines Skripts, manuell oder mit einer automatisierten Methode gemäß Ihren Standards zur Konfigurationsverwaltung auf dem Computer installieren. Für Server mit Azure Arc-Unterstützung wurde vor Kurzem Unterstützung für die Installation der Log Analytics- und Dependency-Agent-[VM-Erweiterungen](../manage-vm-extensions.md) für Windows und Linux eingeführt. Dadurch kann [VM Insights](../../../azure-monitor/vm/vminsights-overview.md) Daten von Ihren Nicht-Azure-VMs sammeln.

In diesem Tutorial erfahren Sie, wie Sie die Konfiguration vornehmen und Daten von Ihren Linux- oder Windows-Computern sammeln, indem Sie VM Insights mithilfe vereinfachter Schritte aktivieren, die den Prozess optimieren und beschleunigen.  

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

* Die Funktionalität der VM-Erweiterungen ist nur in den [unterstützten Regionen](../overview.md#supported-regions) aus der Liste verfügbar.

* Vergewissern Sie sich unter [Unterstützte Betriebssysteme](../../../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems), dass das Serverbetriebssystem, das Sie aktivieren, von VM Insights unterstützt wird.

* Überprüfen Sie die Firewallanforderungen für den Log Analytics-Agent in der [Übersicht über den Log Analytics-Agent](../../../azure-monitor/agents/log-analytics-agent.md#network-requirements). Der Dependency-Agent für das Zuordnungsfeature von VM Insights überträgt selbst keine Daten und erfordert keine Änderungen an Firewalls oder Ports.

## <a name="sign-in-to-azure-portal"></a>Anmelden beim Azure-Portal

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

## <a name="enable-vm-insights"></a>Aktivieren von VM Insights

1. Starten Sie den Azure Arc-Dienst im Azure-Portal, indem Sie auf **Alle Dienste** klicken und dann nach **Computer – Azure Arc** suchen und diese Option auswählen.

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="Suchen nach Servern mit Azure Arc-Unterstützung unter „Alle Dienste“" border="false":::

1. Wählen Sie auf der Seite **Computer – Azure Arc** den verbundenen Computer aus, den Sie im [Schnellstart](quick-enable-hybrid-vm.md) erstellt haben.

1. Wählen Sie im linken Bereich unter dem Abschnitt **Überwachung** die Option **Insights** und dann **Aktivieren** aus.

    :::image type="content" source="./media/tutorial-enable-vm-insights/insights-option.png" alt-text="Auswählen der Option „Insights“ im Menü auf der linken Seite" border="false":::

1. Auf der Seite **Onboarding von Insights** von Azure Monitor werden Sie aufgefordert, einen Arbeitsbereich zu erstellen. Für dieses Tutorial raten wir davon ab, einen vorhandenen Log Analytics-Arbeitsbereich auszuwählen. Wählen Sie die Standardeinstellung aus, d. h. einen Arbeitsbereich mit einem eindeutigen Namen, der sich in der gleichen Region befindet wie Ihr registrierter verbundener Computer. Dieser Arbeitsbereich wird für Sie erstellt und konfiguriert.

    :::image type="content" source="./media/tutorial-enable-vm-insights/enable-vm-insights.png" alt-text="Seite zum Aktivieren von VM Insights" border="false":::

1. Während der Konfiguration werden Statusmeldungen angezeigt. Dieser Prozess dauert einige Minuten, da Erweiterungen auf dem verbundenen Computer installiert werden.

    :::image type="content" source="./media/tutorial-enable-vm-insights/onboard-vminsights-vm-portal-status.png" alt-text="Statusmeldung zum Aktivierungsstatus von VM Insights" border="false":::

    Nach Abschluss der Konfiguration erhalten Sie eine Meldung, dass das Onboarding des Computers und die Bereitstellung von Insights erfolgreich durchgeführt wurden.

## <a name="view-data-collected"></a>Anzeigen gesammelter Daten

Wählen Sie nach Abschluss der Bereitstellung und Konfiguration die Option **Insights** und dann die Registerkarte **Leistung** aus. Auf der Registerkarte „Leistung“ wird eine Gruppe ausgewählter Leistungsindikatoren angezeigt, die vom Gastbetriebssystem Ihres Computers erfasst werden. Scrollen Sie nach unten, um weitere Indikatoren anzuzeigen, und zeigen Sie mit dem Mauszeiger auf ein Diagramm, um den Durchschnittswert und die Perzentile anzuzeigen, die seit der Installation der Log Analytics-VM-Erweiterung auf dem Computer erfasst wurden.

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-performance-charts.png" alt-text="VM Insights-Leistungsdiagramme für ausgewählten Computer" border="false":::

Wählen Sie **Zuordnung** aus, um das Zuordnungsfeature zu öffnen, das die auf dem Computer ausgeführten Prozesse und deren Abhängigkeiten anzeigt. Wählen Sie **Eigenschaften** aus, um den Eigenschaftenbereich zu öffnen, falls er noch nicht geöffnet ist.

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-map.png" alt-text="VM Insights-Zuordnung für ausgewählten Computer" border="false":::

Erweitern Sie die Prozesse für Ihren Computer. Wählen Sie einen der Prozesse aus, um seine Details anzuzeigen und die zugehörigen Abhängigkeiten hervorzuheben.

Wählen Sie erneut Ihren Computer und anschließend **Protokollereignisse** aus. Es wird eine Liste mit den Tabellen angezeigt, die im Log Analytics-Arbeitsbereich für den Computer gespeichert sind. Der Inhalt dieser Liste hängt davon ab, ob Sie einen Windows-Computer oder einen Linux-Computer verwenden. Wählen Sie die Tabelle **Ereignis** aus. Die Tabelle **Ereignis** enthält alle Ereignisse aus dem Windows-Ereignisprotokoll. Log Analytics wird mit einer einfachen Abfrage zum Abrufen der erfassten Ereignisprotokolleinträge geöffnet.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Monitor finden Sie im folgenden Artikel:

> [!div class="nextstepaction"]
> [Azure Monitor – Übersicht](../../../azure-monitor/overview.md)
