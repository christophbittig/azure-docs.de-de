---
title: Überwachen von Abfragen
titleSuffix: Azure Cognitive Search
description: Überwachen Sie die Abfragemetriken im Hinblick auf Leistung und Durchsatz. Erfassen und analysieren Sie die eingegebenen Abfragezeichenfolgen in Ressourcenprotokollen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: e3932ee9a0683689bd74fb2d912d98cd29715da6
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132343039"
---
# <a name="monitor-query-requests-in-azure-cognitive-search"></a>Überwachen von Abfrageanforderungen in Azure Cognitive Search

In diesem Artikel erfahren Sie, wie Sie Abfrageleistung und -volumen mit Metriken und Ressourcenprotokollierung messen können. Außerdem wird erläutert, wie Sie die in Abfragen verwendeten Eingabebegriffe sammeln – notwendige Informationen, wenn Sie das den Nutzen und die Effektivität Ihres Suchkorpus beurteilen müssen.

Im Azure-Portal werden grundlegende Metriken zur Abfragelatenz, Abfragelast (Queries Per Second, QPS) und Drosselung angezeigt. Verlaufsdaten, die in diese Metriken einfließen, werden 30 Tage lang aufbewahrt. Zur längeren Aufbewahrung oder zur Erstellung von Berichten für operative Daten und Abfragezeichenfolgen müssen Sie eine [Diagnoseeinstellung](search-monitor-logs.md) aktivieren, mit der festgelegt wird, dass protokollierte Ereignisse und Metriken beibehalten werden.

Zu den Bedingungen, die die Integrität der Datenmessung maximieren, gehören:

+ Verwenden Sie einen abrechenbaren Dienst (einen Dienst, der entweder im Basic- oder Standard-Tarif erstellt wurde). Der kostenlose Tarif (Free) wird von mehreren Abonnenten gemeinsam genutzt, wodurch eine gewisse Volatilität durch wechselnde Auslastungen entsteht.

+ Verwenden Sie, wenn möglich, ein einzelnes Replikat und eine Partition, um eine eigenständige und isolierte Umgebung zu erstellen. Wenn Sie mehrere Replikate verwenden, werden Abfragemetriken als Durchschnitt mehrerer Knoten angegeben, wodurch die Genauigkeit der Ergebnisse sinken kann. Gleichermaßen bedeuten mehrere Partitionen, dass Daten aufgeteilt werden, wodurch einige Partitionen möglicherweise unterschiedliche Daten aufweisen, wenn gleichzeitig die Indizierung ausgeführt wird. Zur Optimierung der Abfrageleistung stellen ein einzelner Knoten und eine Partition eine stabilere Umgebung für Testzwecke bereit.

> [!Tip]
> Mit zusätzlichem clientseitigem Code und Application Insights können Sie auch Durchklick-Daten (Clickthrough) erfassen, um einen tieferen Einblick in die Interessen Ihrer Anwendungsbenutzer zu erhalten. Weitere Informationen finden Sie unter [Datenverkehrsanalyse durchsuchen](search-traffic-analytics.md).

## <a name="query-volume-qps"></a>Abfragevolumen (QPS)

Das Volumen wird als **Suchabfragen pro Sekunde** (QPS) gemessen, einer integrierten Metrik, die als Durchschnitt, Anzahl, Minimal- oder Maximalwerte für Abfragen angegeben werden kann, die innerhalb eines einminütigen Zeitfensters ausgeführt werden. Einminütige Intervalle (TimeGrain = „PT1M“) für Metriken sind im Systems festgelegt.

Es ist üblich, dass Abfragen in Millisekunden ausgeführt werden, sodass nur Abfragen, die als Sekunden gemessen werden, in Metriken angezeigt werden.

| Aggregationstyp | BESCHREIBUNG |
|------------------|-------------|
| Average | Die durchschnittliche Anzahl von Sekunden innerhalb einer Minute, in der die Abfrageausführung erfolgt ist.|
| Anzahl | Anzahl der Metriken, die innerhalb des Ein-Minuten-Intervalls an das Protokoll ausgegeben werden. |
| Maximum | Die höchste Anzahl von Suchabfragen pro Sekunde, die während einer Minute erfasst wurden. |
| Minimum | Die niedrigste Anzahl von Suchabfragen pro Sekunde, die während einer Minute erfasst wurden.  |
| SUM | Die Summe aller Abfragen, die innerhalb der Minute ausgeführt wurden.  |

Innerhalb einer Minute kann beispielsweise folgendes Muster vorliegen: Für eine Sekunde tritt eine sehr hohe Last auf (dies ist der Höchstwert für „SearchQueriesPerSecond“), gefolgt von 58 Sekunden mit mittlerer Last sowie einer Sekunde mit nur einer Abfrage, was der Mindestwert ist.

Ein weiteres Beispiel: Wenn ein Knoten 100 Metriken ausgibt, bei denen der Wert jeder Metrik 40 beträgt, weist „Count“ den Wert „100“, „Sum“ den Wert „4000“, „Average“ den Wert „40“ und „Max“ den Wert „40“ auf.

## <a name="query-performance"></a>Abfrageleistung

Die dienstweite Abfrageleistung wird als Suchlatenz (wie lange eine Abfrage dauert) und gedrosselte Abfragen, die aufgrund von Ressourcenkonflikten gelöscht wurden, gemessen.

### <a name="search-latency"></a>Suchlatenz

| Aggregationstyp | Latency | 
|------------------|---------|
| Average | Durchschnittliche Abfragedauer in Millisekunden. | 
| Anzahl | Anzahl der Metriken, die innerhalb des Ein-Minuten-Intervalls an das Protokoll ausgegeben werden. |
| Maximum | Die Abfrage mit der längsten Ausführungszeit in der Stichprobe. | 
| Minimum | Die Abfrage mit der kürzesten Ausführungszeit in der Stichprobe.  | 
| Gesamt | Gesamtausführungszeit aller Abfragen in der Stichprobe, die innerhalb des Intervalls (eine Minute) ausgeführt werden.  |

Betrachten Sie das folgende Beispiel für die Metrik **Suchlatenz**: Bei einer Stichprobe von 86 Abfragen betrug die durchschnittliche Dauer 23,26 Millisekunden. Ein Minimalwert von 0 gibt an, dass einige Abfragen gelöscht wurden. Die längste Abfrage hat 1000 Millisekunden gedauert. Die Gesamtausführungszeit betrug 2 Sekunden.

![Latenzaggregationen](./media/search-monitor-usage/metrics-latency.png "Latenzaggregationen")

### <a name="throttled-queries"></a>Gedrosselte Abfragen

„Gedrosselte Abfragen“ bezieht sich auf Abfragen, die gelöscht statt verarbeitet werden. In den meisten Fällen ist die Drosselung ein normaler Bestandteil der Dienstausführung.  Es ist nicht unbedingt ein Hinweis darauf, dass etwas nicht stimmt.

Drosselung tritt auf, wenn die Anzahl der aktuell verarbeiteten Anforderungen die verfügbaren Ressourcen überschreitet. Wenn ein Replikat aus der Rotation herausgenommen wird, oder während der Indizierung kann es zu einer Erhöhung der gedrosselten Anforderungen kommen. Abfrage- und Indizierungsanforderungen werden vom selben Ressourcensatz verarbeitet.

Der Dienst bestimmt, ob Anforderungen basierend auf der Ressourcennutzung gelöscht werden. Der Prozentsatz der für Arbeitsspeicher, CPU und Datenträger-E/AE genutzten Ressourcen wird als Durchschnittswert über einen bestimmten Zeitraum berechnet. Wenn dieser Prozentsatz einen Schwellenwert überschreitet, werden alle Anforderungen an den Index gedrosselt, bis die Anzahl der Anforderungen reduziert wird. 

Abhängig von Ihrem Client kann eine gedrosselte Anforderung auf folgende Weise angegeben werden:

+ Ein Dienst gibt einen Fehler zurück: „Sie senden zu viele Anforderungen. Versuchen Sie es später noch mal.“ 
+ Ein Dienst gibt einen 503-Fehlercode zurück, der angibt, dass der Dienst derzeit nicht verfügbar ist. 
+ Wenn Sie das Portal (z. B. den Suchexplorer) verwenden, wird die Abfrage automatisch gelöscht, und Sie müssen erneut auf „Suchen“ klicken.

Um gedrosselte Abfragen zu bestätigen, verwenden Sie die Metrik **Gedrosselte Suchabfragen**. Sie können Metriken im Portal untersuchen oder eine Warnungsmetrik erstellen, wie in diesem Artikel beschrieben. Verwenden Sie für Abfragen, die innerhalb des Stichprobenintervalls gelöscht wurden, *Gesamt*, um den Prozentsatz der Abfragen zu erhalten, die nicht ausgeführt wurden.

| Aggregationstyp | Drosselung |
|------------------|-----------|
| Average | Prozentsatz der innerhalb des Intervalls gelöschten Abfragen. |
| Anzahl | Anzahl der Metriken, die innerhalb des Ein-Minuten-Intervalls an das Protokoll ausgegeben werden. |
| Maximum | Prozentsatz der innerhalb des Intervalls gelöschten Abfragen.|
| Minimum | Prozentsatz der innerhalb des Intervalls gelöschten Abfragen. |
| Gesamt | Prozentsatz der innerhalb des Intervalls gelöschten Abfragen. |

Für **Gedrosselte Suchabfragen in Prozent** entsprechen der Mindest-, Höchst-, Durchschnitts- und Gesamtwert demselben Wert, nämlich dem Prozentsatz von Suchabfragen, die gedrosselt wurden, basierend auf der Gesamtanzahl von Suchabfragen während einer Minute.

Im folgenden Screenshot gibt die erste Zahl die Anzahl (bzw. die Anzahl der an das Protokoll gesendeten Metriken) an. Zusätzliche Aggregationen, die oben angezeigt werden, oder erscheinen, wenn Sie auf die Metrik zeigen, sind Durchschnitts-, Höchst- und Gesamtwert. In diesem Beispiel wurden keine Anforderungen gelöscht.

![Gedrosselte Aggregationen](./media/search-monitor-usage/metrics-throttle.png "Gedrosselte Aggregationen")

## <a name="explore-metrics-in-the-portal"></a>Untersuchen der Metriken im Portal

Um einen schnellen Überblick über die aktuellen Zahlen zu erhalten, werden auf der Registerkarte **Überwachung** auf der Seite „Übersicht“ des Diensts drei Metriken (**Suchlatenz**, **Suchabfragen pro Sekunde (pro Sucheinheit)** , **Gedrosselte Suchabfragen in Prozent**) in bestimmten Intervallen angezeigt, gemessen in Stunden, Tagen und Wochen, mit der Option, den Aggregationstyp zu ändern.

Zur tiefer gehenden Untersuchung öffnen Sie den Metrik-Explorer aus dem Menü **Überwachung**, um Daten überlagern, vergrößern und visualisieren und so Trends oder Anomalien untersuchen zu können. Weitere Informationen zum Metrik-Explorer erhalten Sie in diesem [Tutorial zum Erstellen eines Metrikdiagramms](../azure-monitor/essentials/tutorial-metrics.md).

1. Wählen Sie im Abschnitt „Überwachung“ die Option **Metriken** aus, um den Metrik-Explorer mit dem auf Ihren Suchdienst festgelegt Bereich zu öffnen.

2. Wählen Sie unter „Metrik“ eine Metrik aus der Dropdownliste aus, und überprüfen Sie die Liste der verfügbaren Aggregationen für einen bevorzugten Typ. Die Aggregation definiert, wie die Werte für jedes Zeitintervall erfasst werden.

   ![Metrik-Explorer für QPS-Metrik](./media/search-monitor-usage/metrics-explorer-qps.png "Metrik-Explorer für QPS-Metrik")

3. Legen Sie in der oberen rechten Ecke das Zeitintervall fest.

4. Wählen Sie eine Visualisierung aus. Der Standardwert ist ein Liniendiagramm.

5. Überlagern Sie zusätzliche Aggregationen, indem Sie **Metrik hinzufügen** und unterschiedliche Aggregationen auswählen.

6. Vergrößern Sie einen relevanten Bereich im Liniendiagramm. Platzieren Sie den Mauszeiger am Anfang des Bereichs, halten Sie die linke Maustaste gedrückt, ziehen Sie den Mauszeiger zur anderen Seite des Bereichs, und lassen Sie die Maustaste wieder los. Im Diagramm wird dieser Zeitbereich vergrößert.

## <a name="return-query-strings-entered-by-users"></a>Zurückgeben von Abfragezeichenfolgen, die von Benutzern eingegeben werden

Wenn Sie die Ressourcenprotokollierung aktivieren, erfasst das System Abfrageanforderungen in der Tabelle **AzureDiagnostics**. Als Voraussetzung müssen Sie die [Ressourcenprotokollierung](search-monitor-logs.md) bereits aktiviert und einen Log Analytics-Arbeitsbereich oder eine andere Speicheroption angegeben haben.

1. Wählen Sie im Abschnitt „Überwachung“ die Option **Protokolle** aus, um in Log Analytics ein leeres Abfragefenster zu öffnen.

1. Führen Sie den folgenden Ausdruck aus, um Query.Search-Vorgänge zu suchen und ein tabellarisches Resultset zurückzugeben, das aus dem Vorgangsnamen, der Abfragezeichenfolge, dem abgefragten Index und der Anzahl der gefundenen Dokumente besteht. Die letzten beiden Anweisungen schließen aus einer leeren oder nicht angegebenen Suche bestehende Abfragezeichenfolgen über einen Beispielindex aus, wodurch unnötige Informationen in den Ergebnissen reduziert werden.

   ```kusto
      AzureDiagnostics
   | project OperationName, Query_s, IndexName_s, Documents_d
   | where OperationName == "Query.Search"
   | where Query_s != "?api-version=2020-06-30&search=*"
   | where IndexName_s != "realestate-us-sample-index"
   ```

1. Legen Sie optional einen Spaltenfilter auf *Query_s* fest, um eine bestimmte Syntax oder Zeichenfolge zu durchsuchen. Beispielsweise können Sie nach *ist gleich* `?api-version=2020-06-30&search=*&%24filter=HotelName`) filtern.

   ![Protokollierte Abfragezeichenfolgen](./media/search-monitor-usage/log-query-strings.png "Protokollierte Abfragezeichenfolgen")

Diese Technik funktioniert zwar für die Ad-hoc-Untersuchung, durch Erstellen eines Berichts können Sie die Abfragezeichenfolgen jedoch in einem Layout konsolidieren und darstellen, das für die Analyse besser geeignet ist.

## <a name="identify-long-running-queries"></a>Ermitteln von Abfragen mit langer Ausführungsdauer

Fügen Sie eine Spalte für die Dauer hinzu, um die Zahlen für alle Abfragen zu erhalten, nicht nur für die, die als Metrik abgefragt werden. Durch Sortieren dieser Daten können Sie erkennen, welche Abfragen am längsten dauern.

1. Wählen Sie im Abschnitt „Überwachung“ die Option **Protokolle** aus, um nach Protokollinformationen abzufragen.

1. Führen Sie die folgende grundlegende Abfrage aus, um Abfragen sortiert nach Dauer in Millisekunden zurückzugeben. Die längsten Abfragen stehen oben.

   ```Kusto
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search"
   | sort by DurationMs
   ```

   ![Sortieren von Abfragen nach Dauer](./media/search-monitor-usage/azurediagnostics-table-sortby-duration.png "Sortieren von Abfragen nach Dauer")

## <a name="create-a-metric-alert"></a>Erstellen einer Metrikwarnung

Eine Metrikwarnung legt einen Schwellenwert fest, bei dem Sie entweder eine Benachrichtigung erhalten oder eine von Ihnen im Voraus festgelegte Korrekturmaßnahme ausgelöst wird. 

Bei einem Suchdienst ist es üblich, eine Metrikwarnung für Suchlatenzen und gedrosselte Abfragen zu erstellen. Wenn Sie wissen, wann Abfragen gelöscht werden, können Sie nach Abhilfemaßnahmen suchen, die die Auslastung verringern oder die Kapazität erhöhen. Wenn z. B. gedrosselte Abfragen während der Indizierung zunehmen, können Sie diese verschieben, bis die Abfrageaktivität nachlässt.

Wenn die Grenzen einer bestimmten Replikat-Partition-Konfiguration überschritten werden, ist auch das Einrichten von Warnmeldungen für Abfragevolumen-Schwellenwerte (Query Volume Thresholds, QPS) hilfreich.

1. Wählen Sie im Abschnitt „Überwachung“ die Option **Warnungen** aus, und klicken Sie dann auf **+ Neue Warnungsregel**. Stellen Sie sicher, dass Ihr Suchdienst als Ressource ausgewählt ist.

1. Klicken Sie unter „Bedingung“ auf **Hinzufügen**.

1. Konfigurieren Sie die Signallogik. Wählen Sie als Signaltyp **Metriken** und dann das Signal aus.

1. Nachdem Sie das Signal ausgewählt haben, können Sie ein Diagramm zur Visualisierung von Verlaufsdaten verwenden, um eine fundierte Entscheidung über das weitere Vorgehen bei der Einrichtung von Bedingungen zu treffen.

1. Scrollen Sie als nächstes nach unten zur Warnungslogik. Als „Proof-of-Concept“ könnten Sie für Testzwecke einen künstlich niedrigen Wert angeben.

   ![Warnungslogik](./media/search-monitor-usage/alert-logic-qps.png "Warnungslogik")

1. Geben Sie als nächstes eine Aktionsgruppe an, oder erstellen Sie eine. Dies ist die Antwort, die aufgerufen wird, wenn der Schwellenwert erreicht ist. Es kann eine Pushbenachrichtigung oder eine automatische Antwort sein.

1. Zuletzt geben Sie die Warnungsdetails an. Benennen und beschreiben Sie die Warnung, weisen Sie einen Schweregrad zu, und geben Sie an, ob die Regel in einem aktivierten oder deaktivierten Zustand erstellt werden soll.

   ![Warnungsdetails](./media/search-monitor-usage/alert-details.png "Warnungsdetails")

Wenn Sie eine E-Mail-Benachrichtigung angegeben haben, erhalten Sie eine E-Mail von „Microsoft Azure“ mit der Betreffzeile „Azure: Aktivierter Schweregrad: 3 `<your rule name>`.

## <a name="next-steps"></a>Nächste Schritte

Fall noch nicht geschehen, überprüfen Sie die Grundlagen der Überwachung von Suchdiensten, um mehr über die gesamte Palette an Überwachungsfunktionen zu erfahren.

> [!div class="nextstepaction"]
> [Überwachen von Vorgängen und Aktivitäten von Azure Cognitive Search](search-monitor-usage.md)