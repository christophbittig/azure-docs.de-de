---
title: Tutorial - Sammeln von Gastprotokollen und Metriken von einem virtuellen Azure-Computer
description: Tutorial - Sammeln von Gastprotokollen und Metriken von einem virtuellen Azure-Computer.
ms.service: azure-monitor
ms.topic: article
ms.custom: subject-monitoring
ms.date: 11/08/2021
ms.openlocfilehash: 5bd3b557bf7bc9ada4e75979e593c7fb0c338dd4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350318"
---
# <a name="tutorial-collect-guest-logs-and-metrics-from-azure-virtual-machine"></a>Tutorial - Sammeln von Gastprotokollen und Metriken von einem virtuellen Azure-Computer
Wenn Sie [die Überwachung mit VM Insights](tutorial-monitor-vm-enable.md) aktivieren, werden Leistungsdaten mithilfe des Log Analytics-Agenten erfasst. Um Protokolle vom Gastbetriebssystem zu sammeln und Leistungsdaten an Azure Monitor Metrics zu senden, installieren Sie den [Azure Monitor-Agent](../agents/azure-monitor-agent-overview.md)m und erstellen Sie eine [Datensammlungsregel ](../agents/data-collection-rule-overview.md)(DCR), welche die zu sammelnden Daten definiert und wohin sie gesendet werden. 

> [!NOTE]
> Vor dem Azure Monitor-Agent wurden Gastmetriken für virtuelle [Azure-Computer](../agents/diagnostics-extension-overview.md) mit der Azure-Diagnoseerweiterung für Windows (WAD) und Linux (LAD) erfasst. Diese Agenten sind immer noch verfügbar und können über den Menüpunkt **Diagnoseeinstellungen** für den virtuellen Computer konfiguriert werden, aber sie werden derzeit durch den Azure Monitor-Agenten ersetzt.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen Sie eine Datensammlungsregel, die Gastleistungsdaten an Azure Monitor und Protokollereignisse an Azure Monitor sendet. 
> * Gastmetriken im Metrik-Explorer anzeigen.
> * Anzeigen von Gastprotokollen in Log Analytics.

## <a name="prerequisites"></a>Voraussetzungen
Für dieses Tutorial benötigen Sie Folgendes: 

- Ein virtueller Azure-Computer zur Überwachung.


## <a name="create-data-collection-rule"></a>Erstellen einer Datensammlungsregel
[Datensammlungsregeln](../agents/data-collection-rule-overview.md) in Azure Monitor definieren zu sammelnde Daten und den Ort, an den sie gesendet werden sollen. Wenn Sie die Datensammlungsregel mithilfe des Azure-Portals definieren, geben Sie die virtuellen Computer an, auf die sie angewendet werden soll. Der Azure Monitor-Agent wird auf virtuellen Computern installiert, auf denen dies noch nicht durchgeführt wurde.

> [!NOTE]
> Sie müssen derzeit den Azure Monitor-Agenten über das Menü **Monitor** im Azure-Portal installieren. Diese Funktionalität ist im Menü des virtuellen Computers noch nicht verfügbar. 

Wählen Sie im Menü **Überwachen** im Azure-Portal **Datensammlungsregeln** und dann **Erstellen** aus, um eine neue Datensammlungsregel zu erstellen.

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-create.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-create.png" alt-text="Erstellen einer Datensammlungsregel":::

Geben Sie auf der Registerkarte **Grundlagen** einen **Regelnamen** an, bei dem es sich um den Namen der Regel handelt, die im Azure-Portal. Wählen Sie **ein Abonnement**, **eine Ressourcengruppe** und eine **Region**  aus, in der die DCR und ihre Zuordnungen gespeichert werden. Diese können sich von den entsprechenden Angaben für die zu überwachende Ressource unterscheiden. Der **Plattformtyp** definiert die Optionen, die verfügbar sind, wenn Sie den Rest der DCR definieren. Wählen Sie *Windows* oder *Linux* aus, wenn nur diese Ressourcen zugeordnet werden, oder *Benutzerdefiniert*, wenn beide Typen zugeordnet werden.

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-basics.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-basics.png" alt-text="Grundlagen von Datensammlungsregeln":::

## <a name="select-resources"></a>Wählen Sie die Ressourcen aus
Identifizieren Sie auf der Registerkarte **Ressourcen** mindestens einen virtuellen Computer, auf den die Datensammlungsregel angewendet werden soll. Der Azure Monitor-Agent wird auf allen installiert, die ihn noch nicht haben. Klicken Sie auf **Ressourcen hinzufügen**, und wählen Sie entweder Ihre virtuellen Computer oder die Ressourcengruppe oder das Abonnement aus, in der sich Ihr virtueller Computer befindet. Die Datensammlungsregel gilt für alle virtuellen Computer im ausgewählten Bereich.

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-resources.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-resources.png" alt-text="Ressourcen für Datenerhebungsregeln":::

## <a name="select-data-sources"></a>Datenquellen auswählen
Eine einzelne Datensammlungsregel kann mehrere Datenquellen haben. In diesem Tutorial verwenden wir dieselbe Regel, um sowohl Gastmetriken als auch Gastprotokolle zu erfassen. Wir senden Metriken sowohl an Azure Monitor-Metriken als auch an Azure Monitor-Protokolle, damit sie sowohl mit dem Metrik-Explorer als auch mit Log Analytics analysiert werden können.

Klicken Sie auf der Registerkarte **Sammeln und Liefern** auf **Datenquelle hinzufügen**. Wählen Sie für den **Datenquellentyp** die Option **Leistungszähler**. Belassen Sie die Einstellung **Basic**, und wählen Sie die Leistungsindikatoren aus, die Sie erfassen möchten. Mit **Custom** können Sie einzelne Metrikwerte auswählen. 

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-data-source-metric.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-data-source-metric.png" alt-text="Metrische Datenquelle für Datenerhebungsregeln":::

Wählen Sie die Registerkarte **Ziel** aus. **Azure Monitor Metriken** sollten bereits aufgeführt sein. Klicken Sie **auf Ziel hinzufügen**, um ein weiteres hinzuzufügen. Wählen Sie **Azure Monitor Protokolle** als **Zieltyp** aus. Wählen Sie Ihren Log Analytics-Arbeitsbereich für das **Konto oder den Namensraum**. Klicken Sie auf **Datenquelle hinzufügen**, um die Datenquelle zu speichern.

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-destination-metric.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-destination-metric.png" alt-text="Ziel der Datensammlungsregel":::

Klicken Sie erneut auf **Datenquelle hinzufügen**, um der Datensammlungsregel Protokolle hinzuzufügen. Wählen Sie als **Datenquellentyp** die Option **Windows Ereignisprotokolle** oder **Linux syslog**. Wählen Sie die Protokolldatentypen aus, die Sie sammeln möchten. 

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-data-source-logs-windows.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-data-source-logs-windows.png" alt-text="Datensammlungsregel Windows Protokolldatenquelle":::

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-data-source-logs-linux.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-data-source-logs-linux.png" alt-text="Datensammlungsregel Windows Protokolldatenquelle":::

Wählen Sie die **Registerkarte Ziel** aus. **Azure Monitor Protokolle** sollte bereits als **Zieltyp** ausgewählt sein. Wählen Sie Ihren Log Analytics-Arbeitsbereich für das **Konto oder den Namensraum**. Wenn Sie noch nicht über einen Arbeitsbereich verfügen, können Sie den Standardarbeitsbereich für Ihr Abonnement auswählen, der automatisch erstellt wird. Klicken Sie auf **Datenquelle hinzufügen**, um die Datenquelle zu speichern.

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-destination-logs.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-destination-logs.png" alt-text="Protokollziel der Datensammlungsregel":::

Klicken Sie auf **Überprüfen + Erstellen**,  um die Datensammlungsregel zu erstellen und den Azure Monitor-Agenten auf den ausgewählten virtuellen Computern zu installieren.

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-save.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-save.png" alt-text="Datenerfassungsregel speichern":::

## <a name="viewing-logs"></a>Anzeigen von Protokollen
Die Daten werden mithilfe einer in Kusto Query Language (KQL) geschriebenen Protokollabfrage aus einem Log Analytics-Arbeitsbereich abgerufen. Während eine Reihe von vorab erstellten Abfragen für virtuelle Computer verfügbar ist, verwenden wir eine einfache Abfrage, um einen Blick auf die Ereignisse zu erhalten, die wir sammeln. 

Wählen Sie im Menü Ihrer virtuellen Computer die Option **Protokolle** aus. Log Analytics wird mit einem leeren Abfragefenster geöffnet, wobei der Bereich auf diesen Computer festgelegt ist. Alle Abfragen enthalten nur Datensätze, die von diesem Computer gesammelt wurden. 

> [!NOTE]
> Das Fenster **Abfragen wird** möglicherweise geöffnet, wenn Sie Log Analytics öffnen. Dies schließt vorab erstellte Abfragen ein, die Sie verwenden können. Schließen Sie dieses Fenster vorerst, da sie manuell eine einfache Abfrage erstellen.

:::image type="content" source="media/tutorial-monitor-vm/log-analytics.png" lightbox="media/tutorial-monitor-vm/log-analytics.png" alt-text="Log Analytics":::


Geben Sie im leeren Abfragefenster entweder `Event` oder `Syslog` ein, je nachdem, ob Ihr Rechner unter Windows oder Linux läuft, und klicken Sie dann auf **Ausführen**. Die im **Zeitbereich** gesammelten Ereignisse werden angezeigt.

> [!NOTE]
> Wenn die Abfrage keine Daten zurück gibt, müssen Sie möglicherweise einige Minuten warten, bis Ereignisse auf dem virtuellen Computer erstellt werden, um gesammelt zu werden. Möglicherweise müssen Sie auch die Datenquelle in der Datensammlungsregel so ändern, dass sie zusätzliche Ereigniskategorien enthält.

:::image type="content" source="media/tutorial-monitor-vm/log-analytics-query.png" lightbox="media/tutorial-monitor-vm/log-analytics-query.png" alt-text="Log Analytics mit Abfrageergebnissen":::

Ein Tutorial zur Verwendung von Log Analytics zum Analysieren von Protokolldaten finden Sie im [Log Analytics-Tutorial](../logs/log-analytics-tutorial.md). Ein Tutorial zum Erstellen von Warnungsregeln aus Protokolldaten finden Sie unter [Tutorial: Erstellen einer Protokollabfragewarnung für eine Azure-Ressource](../alerts/tutorial-log-alert.md).

## <a name="view-guest-metrics"></a>Anzeigen von Gastmetriken
Sie können Metriken für Ihren virtuellen Hostcomputer mit dem Metrik-Explorer ohne Datensammlungsregel wie jede [andere Azure-Ressource anzeigen](../essentials/tutorial-metrics.md). Mit der Datensammlungsregel können Sie jedoch den Metrik-Explorer verwenden, um zusätzlich zu den Hostmetriken auch Gastmetriken anzuzeigen.

Wählen Sie **Metriken** im Menü Ihrer virtuellen Computer aus. Der Metrik-Explorer wird geöffnet, und der Bereich ist auf Ihren virtuellen Computer festgelegt. Klicken Sie auf **Metrik-Namensraum** und wählen Sie **Gast für Virtuelle Computer** aus. 

> [!NOTE]
> Wenn Sie **Gast für Virtuelle Computer** nicht sehen, müssen Sie möglicherweise noch ein paar Minuten warten, bis der Agent bereitgestellt wurde und die Datenerfassung beginnt.


:::image type="content" source="media/tutorial-monitor-vm/metrics-explorer.png" lightbox="media/tutorial-monitor-vm/metrics-explorer.png" alt-text="Metrik-Explorer":::

Die verfügbaren Gastmetriken werden angezeigt. Wählen Sie eine **Metrik aus,** die dem Diagramm hinzugefügt werden soll.

:::image type="content" source="media/tutorial-monitor-vm/metrics-explorer-guest-metrics.png" lightbox="media/tutorial-monitor-vm/metrics-explorer-guest-metrics.png" alt-text="Metrik-Explorer mit Gastmetriken":::

Ein vollständiges Tutorial zum Anzeigen und Analysieren von Metrikdaten mit dem Metrik-Explorer finden Sie unter [Tutorial:{2} Analysieren von Metriken für eine Azure-Ressource](../essentials/tutorial-metrics.md) und Erstellen von Metrikwarnungen im [Tutorial: Erstellen einer Metrikwarnung für eine Azure-Ressource](../alerts/tutorial-metric-alert.md). 



## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie nun Gastmetriken für den virtuellen Computer gesammelt haben, können Sie Metrikwarnungen basierend auf Gastmetriken wie verfügbarem Arbeitsspeicher und logischem Speicherplatz erstellen.

> [!div class="nextstepaction"]
> [Erstellen einer Metrikwarnung in Azure Monitor](../alerts/tutorial-metric-alert.md)


