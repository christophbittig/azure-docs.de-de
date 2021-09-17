---
title: Log Analytics-Tutorial
description: In diesem Tutorial erfahren Sie, wie Sie mit den Features von Log Analytics in Azure Monitor eine Protokollabfrage erstellen und ausführen und die Ergebnisse im Azure-Portal analysieren.
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 06/28/2021
ms.openlocfilehash: bf19056cbde9c719c8d8665d2fc9a954e7a943b6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729344"
---
# <a name="log-analytics-tutorial"></a>Log Analytics-Tutorial
Log Analytics ist ein Tool im Azure-Portal, mit dem Protokollabfragen aus den von Azure Monitor-Protokollen erfassten Daten bearbeitet und ausgeführt sowie deren Ergebnisse interaktiv analysiert werden können. Mit Log Analytics-Abfragen können Sie Datensätze abrufen, die bestimmte Kriterien erfüllen, Trends identifizieren, Muster analysieren und eine Vielzahl von Erkenntnissen aus Ihren Daten gewinnen. 

In diesem Tutorial werden die Log Analytics-Benutzeroberfläche, die ersten Schritte mit einigen einfachen Abfragen und die Möglichkeiten zur Verwendung der Ergebnisse erläutert. Sie lernen Folgendes:

> [!div class="checklist"]
> * Das Schema von Protokolldaten nachvollziehen
> * Einfache Abfragen schreiben und ausführen sowie den Zeitbereich für Abfragen ändern
> * Abfrageergebnisse filtern, sortieren und gruppieren
> * Visuelle Elemente der Abfrageergebnisse anzeigen, ändern und teilen
> * Abfragen und Ergebnisse laden, exportieren und kopieren

> [!IMPORTANT]
> In diesem Tutorial nutzen Sie Log Analytics-Features, um eine Abfrage zu erstellen, und verwenden eine weitere Beispielabfrage. Wenn Sie sich über die Syntax von Abfragen informieren und direkt mit dem Bearbeiten der Abfrage selbst beginnen möchten, lesen Sie das [Tutorial zur Kusto-Abfragesprache](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor). In diesem Tutorial werden Sie durch Beispielabfragen geführt, die Sie in Log Analytics bearbeiten und ausführen können. Es werden einige der Features verwendet, die Sie in diesem Tutorial kennen lernen werden.


## <a name="prerequisites"></a>Voraussetzungen
In diesem Tutorial wird die [Demoumgebung von Log Analytics](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade) verwendet, die zahlreiche Beispieldaten zum Ausführen der Beispielabfragen enthält. Sie können auch Ihr eigenes Azure-Abonnement verwenden, verfügen dann aber möglicherweise nicht über Daten in denselben Tabellen.

## <a name="open-log-analytics"></a>Öffnen von Log Analytics
Öffnen Sie die [Demoumgebung von Log Analytics](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade), oder wählen Sie im Azure Monitor-Menü in Ihrem Abonnement die Option **Protokolle** aus. Mit diesem Schritt wird der Anfangsbereich auf einen Log Analytics-Arbeitsbereich festgelegt, sodass Ihre Abfrage die Auswahl auf Grundlage aller Daten in diesem Arbeitsbereich trifft. Wenn Sie die Option **Protokolle** im Menü einer Azure-Ressource auswählen, wird der Bereich nur auf Datensätze in dieser Ressource festgelegt. Ausführliche Informationen zum Bereich finden Sie unter [Protokollabfragebereich](./scope.md).

Sie können den Bereich in der oberen linken Ecke des Bildschirms anzeigen. Wenn Sie Ihre eigene Umgebung verwenden, wird eine Option zum Auswählen eines anderen Bereichs angezeigt. Diese Option ist in der Demoumgebung nicht verfügbar.

:::image type="content" source="media/log-analytics-tutorial/scope.png" alt-text="Screenshot des Log Analytics-Bereichs für die Demo." lightbox="media/log-analytics-tutorial/scope.png":::

## <a name="view-table-information"></a>Anzeigen von Tabelleninformationen
Auf der linken Seite des Bildschirms finden Sie die Registerkarte **Tabellen**, auf der Sie die im aktuellen Bereich verfügbaren Tabellen untersuchen können. Diese Tabellen sind standardmäßig nach **Lösung** gruppiert, Sie können die Gruppierung jedoch ändern oder die Tabellen filtern. 

Erweitern Sie die Projektmappe **Protokollverwaltung**, und suchen Sie nach der Tabelle **AppRequests**. Sie können die Tabelle erweitern, um ihr Schema anzuzeigen, oder auf ihren Namen zeigen, um weitere Informationen zu erhalten. 

:::image type="content" source="media/log-analytics-tutorial/table-details.png" alt-text="Screenshot der Tabellenansicht." lightbox="media/log-analytics-tutorial/table-details.png":::

Klicken Sie auf den Link unterhalb von **Nützliche Links**, um zur Tabellenreferenz zu wechseln, in der die einzelnen Tabellen mit den zugehörigen Spalten dokumentiert sind. Wählen Sie **Datenvorschau** aus, um einen kurzen Blick auf einige der aktuellen Datensätze in der Tabelle zu werfen. Mit dieser Vorschau können Sie sicherstellen, dass es sich hierbei um die erwarteten Daten handelt, bevor Sie damit eine Abfrage ausführen.

:::image type="content" source="media/log-analytics-tutorial/sample-data.png" alt-text="Screenshot von Beispieldaten." lightbox="media/log-analytics-tutorial/sample-data.png":::

## <a name="write-a-query"></a>Schreiben Sie eine Abfrage.
Schreiben wir nun eine Abfrage unter Verwendung der Tabelle **AppRequests**. Doppelklicken Sie auf den Namen der Tabelle, um ihn dem Abfragefenster hinzuzufügen. Sie können ihn auch direkt in das Fenster eingeben. Sie können sogar IntelliSense nutzen, um die Namen von Tabellen im aktuellen Bereich und KQL-Befehle (Kusto Query Language) vervollständigen zu lassen.

Dies ist die einfachste Abfrage, die wir schreiben können. Sie gibt lediglich alle Datensätze in einer Tabelle zurück. Führen Sie die Abfrage aus, indem Sie die Schaltfläche **Ausführen** auswählen oder den Cursor an einer beliebigen Stelle im Abfragetext positionieren und UMSCHALT+EINGABETASTE drücken.

:::image type="content" source="media/log-analytics-tutorial/query-results.png" alt-text="Screenshot von Abfrageergebnissen." lightbox="media/log-analytics-tutorial/query-results.png":::

Wie Sie sehen, werden Ergebnisse angezeigt. Die Anzahl von Datensätzen, die von der Abfrage zurückgegeben wurden, wird in der unteren rechten Ecke angezeigt. 

## <a name="filter-query-results"></a>Filtern von Abfrageergebnissen

Nun fügen Sie der Abfrage einen Filter hinzu, um die Anzahl zurückgegebener Datensätze zu verringern. Wählen Sie im linken Bereich die Registerkarte **Filter** aus. Auf dieser Registerkarte werden verschiedene Spalten in den Abfrageergebnissen angezeigt, die Sie zum Filtern der Ergebnisse verwenden können. Die obersten Werte in den Spalten werden mit der Anzahl von Datensätzen angezeigt, die diesen Wert enthalten. Wählen Sie unter **ResultCode** den Wert **200** und dann **Anwenden und ausführen** aus. 

:::image type="content" source="media/log-analytics-tutorial/query-pane.png" alt-text="Screenshot des Abfragebereichs." lightbox="media/log-analytics-tutorial/query-pane.png":::

Der Abfrage wird eine **where**-Anweisung mit dem von Ihnen ausgewählten Wert hinzugefügt. Die Ergebnisse enthalten jetzt nur die Datensätze mit diesem Wert, und die Anzahl von Datensätzen ist entsprechend geringer.

:::image type="content" source="media/log-analytics-tutorial/query-results-filter-01.png" alt-text="Screenshot von gefilterten Abfrageergebnissen." lightbox="media/log-analytics-tutorial/query-results-filter-01.png":::


### <a name="time-range"></a>Uhrzeitbereich
Alle Tabellen in einem Log Analytics-Arbeitsbereich verfügen über eine Spalte **TimeGenerated**, die den Zeitpunkt der Erstellung des Datensatzes angibt. Alle Abfragen weisen einen Zeitbereich auf, der die Ergebnisse auf Datensätze mit einem **TimeGenerated**-Wert innerhalb dieses Bereichs beschränkt. Sie können den Zeitbereich in der Abfrage oder mithilfe des Selektors oben auf dem Bildschirm festlegen.

Standardmäßig gibt die Abfrage mindestens die Datensätze der letzten 24 Stunden zurück. Sie sollten hier eine Meldung sehen, dass uns nicht alle Ergebnisse angezeigt werden. Dies liegt daran, dass Log Analytics maximal 30.000 Datensätze zurückgeben kann und unsere Abfrage mehr Datensätze zurückgegeben hat. Wählen Sie die Dropdownliste **Zeitbereich** aus, und ändern Sie den Wert in **12 Stunden**. Wählen Sie erneut **Ausführen** aus, um die Ergebnisse zurückzugeben. 

:::image type="content" source="media/log-analytics-tutorial/query-results-max.png" alt-text="Screenshot des Zeitbereichs." lightbox="media/log-analytics-tutorial/query-results-max.png":::


### <a name="multiple-query-conditions"></a>Mehrere Abfragebedingungen
Jetzt reduzieren Sie die Ergebnisse nochmals, indem Sie eine weitere Filterbedingung hinzufügen. Eine Abfrage kann beliebig viele Filter enthalten, um genau die gewünschte Gruppe von Datensätzen als Ziel festzulegen. Wählen Sie unter **Name** die Option **Get Home/Index** aus, und klicken Sie auf **Anwenden und ausführen**. 

:::image type="content" source="media/log-analytics-tutorial/query-results-filter-02.png" alt-text="Screenshot von Abfrageergebnissen mit mehreren Filtern." lightbox="media/log-analytics-tutorial/query-results-filter-02.png":::


## <a name="analyze-results"></a>Ergebnisanalysen
Zusätzlich zur Unterstützung beim Schreiben und Ausführen von Abfragen bietet Log Analytics Features, die Ihnen die Arbeit mit den Ergebnissen erleichtern. Erweitern Sie zunächst einen Datensatz, um die Werte für alle zugehörigen Spalten anzuzeigen.

:::image type="content" source="media/log-analytics-tutorial/expand-record.png" alt-text="Screenshot des Erweiterns eines Datensatzes." lightbox="media/log-analytics-tutorial/expand-record.png":::

Wählen Sie den Namen einer beliebigen Spalte aus, um die Ergebnisse nach dieser Spalte zu sortieren. Wählen Sie das Filtersymbol neben der Spalte aus, um eine Filterbedingung anzugeben. Dies ist mit dem Hinzufügen einer Filterbedingung zur Abfrage selbst vergleichbar. Der einzige Unterschied besteht darin, dass dieser Filter bei der erneuten Ausführung der Abfrage gelöscht wird. Verwenden Sie diese Methode, wenn Sie eine Gruppe von Datensätzen im Rahmen der interaktiven Analyse schnell analysieren möchten.

Legen Sie beispielsweise einen Filter für die Spalte **DurationMs** fest, um die Datensätze zu beschränken, die mehr als **100** Millisekunden lang gedauert haben. 

:::image type="content" source="media/log-analytics-tutorial/query-results-filter.png" alt-text="Screenshot eines Filters für Abfrageergebnisse." lightbox="media/log-analytics-tutorial/query-results-filter.png":::

Anstatt die Ergebnisse zu filtern, können Sie Datensätze auch nach einer bestimmten Spalte gruppieren. Löschen Sie den soeben erstellten Filter, und aktivieren Sie dann die Umschaltfläche **Spalten gruppieren**. 

:::image type="content" source="media/log-analytics-tutorial/query-results-group-columns.png" alt-text="Screenshot des Aktivierens der Gruppierung von Spalten." lightbox="media/log-analytics-tutorial/query-results-group-columns.png":::

Ziehen Sie die Spalte **URL** in die Gruppierungszeile. Die Ergebnisse werden nun nach dieser Spalte organisiert, und Sie können die einzelnen Gruppen zu Analysezwecken reduzieren.

:::image type="content" source="media/log-analytics-tutorial/query-results-grouped.png" alt-text="Screenshot von gruppierten Abfrageergebnissen." lightbox="media/log-analytics-tutorial/query-results-grouped.png":::

## <a name="work-with-charts"></a>Arbeiten mit Diagrammen
Sehen wir uns nun eine Abfrage mit numerischen Daten an, die in einem Diagramm angezeigt werden können. Anstatt eine Abfrage zu erstellen, wählen wir eine Beispielabfrage aus.

Wählen Sie im linken Bereich **Abfragen** aus. Dieser Bereich enthält Beispielabfragen, die Sie dem Abfragefenster hinzufügen können. Wenn Sie Ihren eigenen Arbeitsbereich verwenden, sollten Sie über eine Reihe verschiedener Abfragen in mehreren Kategorien verfügen. Wenn Sie die Demoumgebung verwenden, wird möglicherweise nur eine einzelne Kategorie für **Log Analytics-Arbeitsbereiche** angezeigt. Erweitern Sie diese Kategorie, um die darin enthaltenen Abfragen anzuzeigen.

Wählen Sie in der Kategorie **Anwendungen** die Abfrage **Funktionsfehlerrate** aus. Durch diesen Schritt wird die Abfrage dem Abfragefenster hinzugefügt. Beachten Sie, dass die neue Abfrage durch eine leere Zeile von der anderen Abfrage getrennt ist. Eine Abfrage in KQL wird beendet, wenn sie auf eine leere Zeile trifft, weshalb sie als separate Abfragen betrachtet wird. 

:::image type="content" source="media/log-analytics-tutorial/example-query.png" alt-text="Screenshot einer neuen Abfrage." lightbox="media/log-analytics-tutorial/example-query.png":::

Die aktuelle Abfrage ist die, auf der der Cursor positioniert ist. Die erste Abfrage ist hervorgehoben, was bedeutet, dass dies die aktuelle Abfrage ist. Klicken Sie an einer beliebigen Stelle in der neuen Abfrage, um sie auszuwählen, und klicken Sie dann auf die Schaltfläche **Ausführen**, um sie auszuführen.

:::image type="content" source="media/log-analytics-tutorial/example-query-output-table.png" alt-text="Screenshot, der die Tabelle mit Abfrageergebnissen zeigt." lightbox="media/log-analytics-tutorial/example-query-output-table.png":::

Um die Ergebnisse in einem Diagramm anzuzeigen, wählen Sie im Ergebnisbereich **Diagramm** aus.  Zum Arbeiten mit dem Diagramm stehen Ihnen verschiedene Optionen zur Verfügung, Sie können es z. B. in einen anderen Typ ändern.

:::image type="content" source="media/log-analytics-tutorial/example-query-output-chart.png" alt-text="Screenshot des Diagramms mit Abfrageergebnissen." lightbox="media/log-analytics-tutorial/example-query-output-chart.png":::


## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich nun mit Log Analytics vertraut gemacht haben, können Sie mit dem Tutorial zur Verwendung von Protokollabfragen fortfahren:
> [!div class="nextstepaction"]
> [Schreiben von Azure Monitor-Protokollabfragen](get-started-queries.md)
