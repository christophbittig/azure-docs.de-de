---
title: 'Tutorial: Warnung, wenn Azure Virtual nicht verfügbar ist'
description: Erstellen Sie eine Warnungsregel in Azure Monitor, um Sie proaktiv benachrichtigt zu werden, wenn ein virtueller Computer nicht verfügbar ist.
ms.service: azure-monitor
ms.topic: article
ms.custom: subject-monitoring
ms.date: 11/04/2021
ms.openlocfilehash: af60844e6aced51989cbee07a787deba01a379f9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350413"
---
# <a name="tutorial-create-alert-when-azure-virtual-machine-is-unavailable"></a>Tutorial: Erstellen einer Warnung, wenn der virtuelle Azure-Computer nicht verfügbar ist
Eine der häufigsten Warnungsbedingungen für einen virtuellen Computer ist, ob sie ausgeführt wird. Nachdem Sie die Überwachung mit VM-Erkenntnissen in Azure Monitor für den virtuellen Computer aktiviert haben, wird jede Minute ein Heartbeat an Azure Monitor gesendet. Sie können eine Warnungsregel für Protokollabfragen erstellen, die eine Warnung sendet, wenn kein Heartbeat erkannt wird. Diese Methode warnt nicht nur, wenn der virtuelle Computer nicht ausgeführt wird, sondern auch, wenn sie nicht reagiert.


In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Anzeigen von Protokolldaten, die von VM-Erkenntnissen in Azure Monitor für einen virtuellen Computer gesammelt wurden.
> * Erstellen Sie eine Warnungsregel aus Protokolldaten, die Sie proaktiv benachrichtigt, wenn der virtuelle Computer nicht verfügbar ist.

## <a name="prerequisites"></a>Voraussetzungen
Für dieses Tutorial benötigen Sie Folgendes: 

- Ein virtueller Computer zur Überwachung.
- Überwachung mit VM-Erkenntnissen, die für den virtuellen Computer aktiviert sind. Siehe [Tutorial: Aktivieren der Überwachung für virtuelle Azure-Computer](tutorial-monitor-vm-enable.md).



## <a name="create-a-heartbeat-query"></a>Erstellen einer Heartbeat-Abfrage
Es gibt mehrere Möglichkeiten, eine Warnungsregel für Protokollabfragen zu erstellen. Für dieses Tutorial beginnen wir mit der Registerkarte **Protokollereignisse** in der **Kartenansicht.** Dadurch erhalten Sie eine Zusammenfassung der Protokolldaten, die für den virtuellen Computer gesammelt wurden. 

:::image type="content" source="media/tutorial-monitor-vm/map-logs.png" lightbox="media/tutorial-monitor-vm/map-logs.png" alt-text="Kartenansicht mit Registerkarte „Protokollereignisse“":::

Klicken Sie auf **Heartbeat**. Dadurch wird die Protokollanalyse geöffnet, das primäre Tool zum Analysieren der vom virtuellen Computer gesammelten Protokolldaten mit einer einfachen Abfrage für Heartbeat-Ereignisse. Wenn Sie auf **TimeGenerated** klicken, um nach dieser Spalte zu sortieren, können Sie sehen, dass jede Minute ein Heartbeat erstellt wird.

:::image type="content" source="media/tutorial-monitor-vm/log-query-heartbeat.png" lightbox="media/tutorial-monitor-vm/log-query-heartbeat.png" alt-text="Protokollabfragewarnung mit Heartbeat-Ereignissen.":::


Für die Warnung möchten Sie nur Heartbeat-Datensätze in den letzten 5 Minuten zurückgeben. Wenn keine Datensätze zurückgegeben werden, können Sie davon ausgehen, dass der virtuelle Computer nicht mehr ausgeführt wird.

Fügen Sie der Abfrage eine Zeile hinzu, um die Ergebnisse nur nach Datensätzen zu filtern, die in den letzten 5 Minuten erstellt wurden. Dabei wird die [Vor-Funktion](/azure/data-explorer/kusto/query/agofunction) verwendet, die eine bestimmte Zeitspanne von der aktuellen Zeit subtrahiert.

```
Heartbeat
| where Computer == 'computer-name'
| where TimeGenerated > ago(5m)
```

Klicken Sie auf **Ausführen**, um die Ergebnisse dieser Abfrage zu sehen, die jetzt nur die Heartbeats der letzten 5 Minuten enthalten sollte.

:::image type="content" source="media/tutorial-monitor-vm/log-query-heartbeat-time-filter.png" lightbox="media/tutorial-monitor-vm/log-query-heartbeat-time-filter.png" alt-text="Protokollieren von Abfragewarnungen mit Heartbeat-Ereignissen mithilfe eines Zeitfilters.":::

## <a name="create-alert-rule"></a>Erstellen einer Warnungsregel
Nachdem Sie nun über die Protokollabfrage verfügen, können Sie eine Warnungsregel erstellen, die eine Warnung sendet, wenn diese Abfrage keine Datensätze zurück gibt. Wenn in den letzten 5 Minuten keine Heartbeat-Datensätze zurückgegeben werden, können wir davon ausgehen, dass der Computer in diesem Zeitraum nicht reagiert hat. 

Wählen Sie **Neue Warnungsregel** aus, um eine Regel mit der aktuellen Abfrage zu erstellen.

:::image type="content" source="media/tutorial-monitor-vm/new-alert-rule.png" lightbox="media/tutorial-monitor-vm/new-alert-rule.png" alt-text="Neue Warnungsregel.":::


In der Warnungsregel ist die **Protokollabfrage** bereits ausgefüllt. Die **Messung** ist ebenfalls bereits richtig, da wir die Anzahl der von der Abfrage zurückgegebenen Tabellenzeilen zählen möchten. Wenn die Anzahl der Zeilen 0 (null) ist, möchten wir eine Warnung erstellen.

:::image type="content" source="media/tutorial-monitor-vm/alert-rule-01.png" lightbox="media/tutorial-monitor-vm/alert-rule-01.png" alt-text="Warnungsregelbedingung 1.":::

Scrollen Sie nach unten zu **Warnungslogik**, ändern Sie **Operator** in **Gleich**, und geben Sie den **Schwellenwert** **0** an. Dies bedeutet, dass wir eine Warnung erstellen möchten, wenn keine Datensätze zurückgegeben werden oder wenn die Datensatzanzahl aus der Abfrage 0 (null) ist.

:::image type="content" source="media/tutorial-monitor-vm/alert-rule-02.png" lightbox="media/tutorial-monitor-vm/alert-rule-02.png" alt-text="Warnungsregelbedingung 2.":::

## <a name="configure-action-group"></a>Konfigurieren einer Aktionsgruppe
Auf der Seite **Aktionen** können Sie der Warnungsregel eine oder mehrere [Aktionsgruppen](../alerts/action-groups.md) hinzufügen. Aktionsgruppen definieren eine Reihe von Aktionen, die beim Auslösen einer Warnung durchgeführt werden sollen, etwa Senden einer E-Mail oder einer SMS-Nachricht.

Wenn Sie bereits über eine Aktionsgruppe verfügen, klicken Sie auf **Aktionsgruppe hinzufügen**, um der Warnungsregel eine vorhandene Gruppe hinzuzufügen.

:::image type="content" source="media/tutorial-monitor-vm/alert-rule-actions.png" lightbox="media/tutorial-monitor-vm/alert-rule-actions.png" alt-text="Hinzufügen einer Aktionsgruppe zur Warnungsregel.":::

Ist in Ihrem Abonnement noch keine Aktionsgruppe zum Auswählen enthalten, klicken Sie auf **Aktionsgruppe erstellen**, um eine neue Aktionsgruppe zu erstellen. Wählen Sie ein **Abonnement** und eine **Ressourcengruppe** für die Aktionsgruppe aus, geben Sie unter **Name der Aktionsgruppe** einen Namen ein, der im Portal angezeigt wird, und geben Sie unter **Anzeigename** einen Namen ein, der in E-Mail- und SMS-Benachrichtigungen angezeigt wird.

:::image type="content" source="media/tutorial-monitor-vm/action-group-basics.png" lightbox="./media/tutorial-monitor-vm/action-group-basics.png" alt-text="Grundlagen für Aktionsgruppen":::

Wählen Sie **Benachrichtigungen** aus, und fügen Sie eine oder mehrere Methoden hinzu, um die entsprechenden Personen zu benachrichtigen, wenn die Warnung ausgelöst wird.

:::image type="content" source="media/tutorial-monitor-vm/action-group-notifications.png" lightbox="./media/tutorial-monitor-vm/action-group-notifications.png" alt-text="Benachrichtigungen für Aktionsgruppen":::



## <a name="configure-details"></a>Konfigurieren der Details
Auf der Seite **Details** können Sie verschiedene Einstellungen für die Warnungsregel konfigurieren.

- **Abonnement** und **Ressourcengruppe**, in denen die Warnungsregel gespeichert wird. Hierbei muss es sich nicht um dieselbe Ressourcengruppe handeln, in der sich die überwachte Ressource befindet.
- **Schweregrad** für die Warnung. Mit dem Schweregrad können Warnungen mit einer ähnlichen relativen Wichtigkeit gruppiert werden. Der Schweregrad **Fehler** ist für einen nicht reagierenden virtuellen Computer geeignet.
- Lassen Sie das Kontrollkästchen **Enable alert upon creation** (Warnung bei Erstellung aktivieren) aktiviert.
- Lassen Sie das Kontrollkästchen **Warnungen automatisch auflösen** aktiviert. Dadurch wird die Warnung automatisch behoben, wenn der virtuelle Computer wieder online ist und Heartbeat-Datensätze erneut angezeigt werden.

:::image type="content" source="media/tutorial-monitor-vm/alert-rule-details.png" lightbox="media/tutorial-monitor-vm/alert-rule-details.png" alt-text="Warnungsregeldetails.":::

Klicken Sie zum Erstellen der Warnungsregel auf **Überprüfen und erstellen**.

## <a name="view-the-alert"></a>Anzeigen der Warnung
Beenden Sie den virtuellen Computer, um die Warnungsregel zu testen. Wenn Sie eine Benachrichtigung in Ihrer Aktionsgruppe konfiguriert haben, sollten Sie diese Benachrichtigung innerhalb weniger Minuten erhalten. Außerdem wird eine Warnung angezeigt, die in der Zusammenfassung angezeigt wird, die auf der Seite **Warnungen** für den virtuellen Computer angezeigt wird.

:::image type="content" source="media/tutorial-monitor-vm/alerts-summary.png" lightbox="media/tutorial-monitor-vm/alerts-summary.png" alt-text="Zusammenfassung von Warnungen":::

Klicken Sie auf den **Schweregrad**, um die Liste dieser Warnungen zu sehen. Klicken Sie auf die Warnung selbst, um die Details anzuzeigen.

:::image type="content" source="media/tutorial-monitor-vm/alerts-summary.png" lightbox="media/tutorial-monitor-vm/alerts-summary.png" alt-text="Liste der Warnungen":::

## <a name="next-steps"></a>Nächste Schritte
Da Sie nun wissen, wie Sie eine Warnung aus Protokolldaten erstellen, sammeln Sie zusätzliche Protokolle und Leistungsdaten vom virtuellen Computer mit einer Datensammlungsregel.

> [!div class="nextstepaction"]
> [Tutorial: Sammeln von Gastprotokollen und Metriken von einem virtuellen Azure-Computer](tutorial-monitor-vm-guest.md)

