---
title: Erweiterte Funktionen des Azure-Metrik-Explorers
description: Erfahren Sie mehr über die erweiterte Nutzung des Azure-Metrik-Explorers.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: vitalyg
ms.openlocfilehash: bd0fef05913c66f767cb81e3c999fda4edaa8c21
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114456522"
---
# <a name="advanced-features-of-the-azure-metrics-explorer"></a>Erweiterte Funktionen des Azure-Metrik-Explorers

> [!NOTE]
> In diesem Artikel wird davon ausgegangen, dass Sie mit grundlegenden Funktionen des Azure-Metrik-Explorers in Azure Monitor vertraut sind. Wenn Sie ein neuer Benutzer sind und erfahren möchten, wie Sie Ihr erstes Metrikdiagramm erstellen können, lesen Sie [Erste Schritte mit dem Azure-Metrik-Explorer](./metrics-getting-started.md).

In Azure Monitor sind [Metriken](data-platform-metrics.md) eine Reihe von Messwerten und Zahlen, die im Lauf der Zeit gesammelt und gespeichert werden. Bei Metriken kann es sich um Standardmetriken (auch „Plattformmetriken“ genannt) oder benutzerdefinierte Metriken handeln. 

Standardmetriken werden von der Azure-Plattform bereitgestellt. Sie stellen die Integritäts- und Nutzungsstatistik Ihrer Azure-Ressourcen dar. 

## <a name="resource-scope-picker"></a>Auswahl des Ressourcenbereichs
Mit der Auswahl des Ressourcenbereichs können Sie Metriken für einzelne und mehrere Ressourcen anzeigen. In den folgenden Abschnitten wird erläutert, wie Sie die Auswahl des Ressourcenbereichs verwenden. 

### <a name="select-a-single-resource"></a>Auswählen einer einzelnen Ressource
Wählen Sie im Menü **Azure Monitor** oder im Abschnitt **Überwachung** des Menüs einer Ressource die Option **Metriken** aus. Wählen Sie dann **Bereich auswählen** aus, um die Bereichsauswahl zu öffnen. 

Wählen Sie mithilfe der Bereichsauswahl die Ressourcen aus, deren Metriken Sie anzeigen möchten. Wenn Sie den Azure-Metrik-Explorer über das Menü einer Ressource geöffnet haben, sollte der Bereich bereits angegeben sein. 

![Screenshot zum Öffnen der Auswahl des Ressourcenbereichs](./media/metrics-charts/scope-picker.png)

Bei einigen Ressourcen können Sie jeweils nur die Metriken einer einzelnen Ressource anzeigen. Im Menü **Ressourcentypen** befinden sich diese Ressourcen im Abschnitt **Alle Ressourcentypen**.

![Screenshot mit einer einzelnen Ressource](./media/metrics-charts/single-resource-scope.png)

Nachdem Sie eine Ressource ausgewählt haben, werden alle Abonnements und Ressourcengruppen angezeigt, die diese Ressource enthalten.

![Screenshot mit verfügbaren Ressourcen](./media/metrics-charts/available-single-resource.png)

> [!TIP]
> Wenn Sie die Möglichkeit haben möchten, die Metriken für mehrere Ressourcen gleichzeitig oder Metriken für ein Abonnement oder eine Ressourcengruppe anzuzeigen, wählen Sie **Aufwerten** aus.

Wenn Sie mit der Auswahl zufrieden sind, wählen Sie **Anwenden** aus.

### <a name="view-metrics-across-multiple-resources"></a>Anzeigen von Metriken für mehrere Ressourcen
Einige Ressourcentypen können Metriken für mehrere Ressourcen abfragen. Die Ressourcen müssen sich im selben Abonnement und am selben Standort befinden. Diese Ressourcentypen finden Sie oben im Menü **Ressourcentypen**. 

Weitere Informationen finden Sie unter [Auswählen mehrerer Ressourcen](./metrics-dynamic-scope.md#select-multiple-resources).

![Screenshot mit ressourcenübergreifenden Typen](./media/metrics-charts/multi-resource-scope.png)

Bei Typen, die mit mehreren Ressourcen kompatibel sind, können Sie Metriken für ein Abonnement oder mehrere Ressourcengruppen abfragen. Weitere Informationen finden Sie unter [Auswählen einer Ressourcengruppe oder eines Abonnements](./metrics-dynamic-scope.md#select-a-resource-group-or-subscription).

## <a name="multiple-metric-lines-and-charts"></a>Mehrere Metriklinien und -diagramme

Im Azure-Metrik-Explorer können Sie Diagramme erstellen, in denen mehrere Metriklinien gezeichnet oder mehrere Metrikdiagramme gleichzeitig angezeigt werden. Diese Funktionalität bietet Ihnen folgende Möglichkeiten:

- Korrelieren verwandter Metriken im gleichen Diagramm, um zu sehen, in welcher Beziehung ein Wert zu einem anderen steht
- Anzeigen von Metriken mit unterschiedlichen Maßeinheiten in unmittelbarer Nähe zueinander
- Visuelles Aggregieren und Vergleichen von Metriken aus mehreren Ressourcen

Stellen Sie sich beispielsweise vor, Sie haben fünf Speicherkonten und möchten wissen, wie viel Gesamtspeicherplatz diese belegen. Sie können ein (gestapeltes) Flächendiagramm erstellen, das die einzelnen Werte und die Summe aller Werte zu bestimmten Zeitpunkten anzeigt.

### <a name="multiple-metrics-on-the-same-chart"></a>Mehrere Metriken in demselben Diagramm

Wenn Sie mehrere Metriken in demselben Diagramm anzeigen möchten, [erstellen Sie zuerst ein neues Diagramm](./metrics-getting-started.md#create-your-first-metric-chart). Wählen Sie dann **Metrik hinzufügen** aus. Wiederholen Sie diesen Schritt, um eine weitere Metrik im gleichen Diagramm hinzuzufügen.

> [!NOTE]
> In der Regel sollten in Ihren Diagrammen keine Metriken mit unterschiedlichen Maßeinheiten gemeinsam verwendet werden. Vermeiden Sie z. B. das Kombinieren einer Metrik, die Millisekunden verwendet, mit einer anderen, die Kilobytes verwendet. Vermeiden Sie auch das Kombinieren von Metriken mit deutlich abweichenden Skalierungen. 
>
> In diesen Fällen sollten Sie stattdessen mehrere Diagramme verwenden. Wählen Sie im Metrik-Explorer **Neues Diagramm** aus, um ein neues Diagramm zu erstellen.

![Screenshot: Mehrere Metriken.](./media/metrics-charts/multiple-metrics-chart.png)

### <a name="multiple-charts"></a>Mehrere Diagramme

Zum Erstellen eines weiteren Diagramms mit einer anderen Metrik wählen Sie **Neues Diagramm** aus.

Wenn Sie mehrere Diagramme neu anordnen oder löschen möchten, wählen Sie die Schaltfläche mit den Auslassungszeichen ( **...** ) aus, um das Diagrammmenü zu öffnen. Wählen Sie dann **Nach oben**, **Nach unten** oder **Löschen** aus.

![Screenshot: Mehrere Diagramme.](./media/metrics-charts/multiple-charts.png)

## <a name="time-range-controls"></a>Zeitbereichssteuerungen

Zusätzlich zum Ändern des Zeitraums mithilfe des [Zeitauswahlbereichs](metrics-getting-started.md#select-a-time-range) können Sie auch die Steuerelemente im Diagrammbereich zum Schwenken und Zoomen verwenden.
### <a name="pan"></a>Schwenken

Zum Schwenken klicken Sie am Rand des Diagramms auf die Pfeile nach links und rechts.  Dadurch wird der ausgewählte Zeitbereich um die halbe Zeitspanne des Diagramms vor und zurück verschoben.  Wenn Sie z. B. die letzten 24 Stunden anzeigen und auf den Pfeil nach links klicken, wird der Zeitbereich so verschoben, dass er den Zeitraum vor anderthalb Tagen bis vor 12 Stunden umfasst.

Die meisten Metriken unterstützen eine Aufbewahrungsdauer von 93 Tagen, doch können jeweils nur 30 Tage auf einmal angezeigt werden.  Mithilfe der Steuerelemente zum Schwenken können Sie sich die letzten 30 Tage ansehen und dann jeweils um 15 Tage zurückgehen, um den restlichen Aufbewahrungszeitraum anzuzeigen.

![Animiertes GIF mit den Steuerelementen zum Schwenken nach links und rechts](./media/metrics-charts/metrics-pan-controls.gif)

### <a name="zoom"></a>Zoom

Sie können auf das Diagramm klicken und ziehen, um einen Abschnitt des Diagramms zu vergrößern.  Durch Zoomen wird der Zeitbereich des Diagramms so aktualisiert, dass er Ihre Auswahl umfasst. Außerdem wird ein kleineres Aggregationsintervall ausgewählt, sofern das Intervall auf „Automatisch“ festgelegt ist.  Der neue Zeitbereich gilt für alle Diagramme in den Metriken.

![Animiertes GIF mit der Funktion zum Zoomen von Metriken](./media/metrics-charts/metrics-zoom-control.gif)

## <a name="aggregation"></a>Aggregation

Wenn Sie einem Diagramm eine Metrik hinzufügen, wendet der Metrik-Explorer automatisch eine Standardaggregation an. Die Standardeinstellung ist in grundlegenden Szenarien sinnvoll. Sie können jedoch eine andere Aggregation verwenden, um weitere Einblicke in die Metrik zu erhalten. 

Bevor Sie verschiedene Aggregationen in einem Diagramm verwenden, sollten Sie wissen, wie diese vom Metrik-Explorer verarbeitet werden. Bei Metriken handelt es sich um eine Reihe von Messwerten (oder „Metrikwerte“), die über einen Zeitraum erfasst werden. Wenn Sie ein Diagramm zeichnen, werden die Werte der ausgewählten Metrik separat über das *Aggregationsintervall* aggregiert. 

Sie wählen die Dauer des Aggregationsintervalls mithilfe des [Zeitauswahlbereichs](./metrics-getting-started.md#select-a-time-range) im Metrik-Explorer aus. Wenn Sie das Aggregationsintervall nicht explizit auswählen, wird standardmäßig der aktuell ausgewählte Zeitraum verwendet. Nach Festlegen des Aggregationsintervalls werden die Metrikwerte, die während jedes Aggregationsintervalls erfasst wurden, im Diagramm aggregiert (jeweils ein Datenpunkt pro Aggregationsintervall).

Angenommen, ein Diagramm zeigt die Metrik *Serverantwortzeit*. Es verwendet den Aggregationstyp *Average* (Durchschnitt) über den Zeitraum *letzte 24 Stunden*. In diesem Beispiel:

- Wenn die Zeitgranularität auf 30 Minuten festlegt ist, wird das Diagramm aus 48 aggregierten Datenpunkten gezeichnet. Das heißt, das Liniendiagramm verbindet 48 Punkte in der Diagrammzeichnungsfläche (24 Stunden x 2 Datenpunkte pro Stunde). Jeder Datenpunkt stellt den *Durchschnitt* aller erfassten Antwortzeiten für Serveranforderungen dar, die während des jeweils relevanten 30-Minuten-Zeitraums aufgetreten sind.
- Wenn Sie die Zeitgranularität auf 15 Minuten festlegen, erhalten Sie 96 aggregierte Datenpunkte.  Das heißt, Sie erhalten 24 Stunden x 4 Datenpunkte pro Stunde.

Der Metrik-Explorer verfügt über fünf grundlegende statistische Aggregationstypen: „Sum“, „Count“, „Min“, „Max“ und „Average“. Der Aggregationstyp *Sum* wird manchmal auch *Total* genannt. Bei vielen Metriken blendet der Metrik-Explorer die Aggregationen aus, die irrelevant sind und nicht verwendet werden können. 

Eine eingehendere Erläuterung der Funktionsweise der Metrikaggregation finden Sie unter [Funktionsweise der Aggregation](metrics-aggregation-explained.md).

* **Sum**: Die Summe aller Werte, die während des Aggregationsintervalls erfasst wurden.

    ![Screenshot einer Anforderung vom Typ „Sum“](./media/metrics-charts/request-sum.png)

* **Count**: Die Anzahl der Messwerte, die während des Aggregationsintervalls erfasst wurden. 
    
    Wenn die Metrik immer mit dem Wert 1 erfasst wird, entspricht der Aggregationstyp „Count“ dem Typ „Sum“. Dies ist ein übliches Szenario, wenn die Metrik die Anzahl unterschiedlicher Ereignisse nachverfolgt und jede Messung ein Ereignis darstellt. Der Code gibt bei jedem Eintreffen einer neuen Anforderung einen Metrikdatensatz aus.

    ![Screenshot einer Anforderung vom Typ „Count“](./media/metrics-charts/request-count.png)

* **Average**: Der Durchschnitt der Metrikwerte, die während des Aggregationsintervalls erfasst wurden.

    ![Screenshot einer Anforderung vom Typ „Average“](./media/metrics-charts/request-avg.png)

* **Min**: Der niedrigste Wert, der während des Aggregationsintervalls erfasst wurde.

    ![Screenshot einer Anforderung vom Typ „Min“](./media/metrics-charts/request-min.png)

* **Max**: Der höchste Wert, der während des Aggregationsintervalls erfasst wurde.

    ![Screenshot einer Anforderung vom Typ „Max“](./media/metrics-charts/request-max.png)

## <a name="filters"></a>Filter

Sie können Filter auf Diagramme anwenden, die Metriken mit Dimensionen aufweisen. Stellen Sie sich beispielsweise eine Metrik „Transaktionsanzahl“ mit einer Dimension „Antworttyp“ vor. Diese Dimension gibt an, ob die Antwort von Transaktionen erfolgreich war oder nicht. Wenn Sie nach dieser Dimension filtern, wird eine Diagrammlinie nur für erfolgreiche (bzw. nur für fehlerhafte) Transaktionen angezeigt. 

### <a name="add-a-filter"></a>Hinzufügen eines Filters

1. Wählen Sie oberhalb des Diagramms **Filter hinzufügen** aus.

2. Wählen Sie eine Dimension (Eigenschaft) aus, nach der gefiltert werden soll.

   ![Screenshot der Eigenschaften, nach denen gefiltert werden kann](./media/metrics-charts/028.png)

3. Wählen Sie den Operator aus, den Sie auf die Dimension anwenden möchten (Eigenschaft). Der Standardoperator ist „=“ (ist gleich).

   ![Screenshot des Operators, den Sie mit dem Filter verwenden können](./media/metrics-charts/filter-operator.png)

4. Wählen Sie beim Zeichnen des Diagramms aus, welche Dimensionswerte auf den Filter angewandt werden sollen (in diesem Beispiel werden die Filterergebnisse erfolgreicher Speichertransaktionen gezeigt):

   ![Screenshot des Herausfilterns erfolgreicher Speichertransaktionen](./media/metrics-charts/029.png)

5. Klicken Sie nach der Auswahl der Filterwerte außerhalb des Filterselektors, um ihn zu schließen. Nun zeigt das Diagramm an, wie viele Speichertransaktionen fehlerhaft sind:

   ![Screenshot der Anzahl der fehlerhaften Speichertransaktionen](./media/metrics-charts/030.png)

6. Sie können die Schritte 1 bis 5 wiederholen, um mehrere Filter auf dieselben Diagramme anzuwenden.


## <a name="metric-splitting"></a>Teilen von Metriken

Sie können eine Metrik nach Dimension teilen, um die verschiedenen Segmente der Metrik gegenüberzustellen. Das Teilen kann Ihnen auch beim Identifizieren der äußeren Segmente einer Dimension helfen.

### <a name="apply-splitting"></a>Anwenden des Teilens

1. Wählen Sie oberhalb des Diagramms **Teilung anwenden** aus.
 
   > [!NOTE]
   > Für Diagramme mit mehreren Metriken kann die Teilungsfunktion nicht verwendet werden. Zudem kann ein Diagramm zwar mehrere Filter, aber nur eine Teilungsdimension aufweisen.

2. Wählen Sie eine Dimension aus, in der Ihr Diagramm segmentiert werden soll:

   ![Screenshot der ausgewählten Dimension, in der das Diagramm segmentiert werden soll](./media/metrics-charts/031.png)

   Das Diagramm zeigt nun mehrere Linien an, eine Linie für jedes Dimensionssegment:

   ![Screenshot, der mehrere Zeilen anzeigt: eine Zeile für jedes Segment der Dimension.](./media/metrics-charts/segment-dimension.png)
   
3. Wählen Sie einen Grenzwert für die Anzahl der Werte aus, die nach der Aufteilung nach der ausgewählten Dimension angezeigt werden sollen. Der Standardgrenzwert ist 10, wie im Diagramm oben gezeigt. Der Grenzwert kann zwischen 1 und 50 liegen.
   
   ![Screenshot des Aufteilungsgrenzwerts zum Einschränken der Anzahl der Werte nach dem Aufteilen](./media/metrics-charts/segment-dimension-limit.png)
   
4. Wählen Sie die Sortierreihenfolge für Segmente aus: „Aufsteigend“ oder „Absteigend“. Die Standardauswahl ist „Absteigend“.
   
   ![Screenshot der Sortierreihenfolge für die aufgeteilten Werte](./media/metrics-charts/segment-dimension-sort.png)

5. Klicken Sie außerhalb des **Gruppierungsselektors**, um ihn zu schließen.
   

   > [!NOTE]
   > Um Segmente auszublenden, die für Ihr Szenario nicht relevant sind, und um Ihre Diagramme übersichtlicher zu gestalten, verwenden Sie sowohl das Filtern als auch das Teilen für dieselbe Dimension.

## <a name="locking-the-range-of-the-y-axis"></a>Festlegen des Bereichs der Y-Achse

Bei Diagrammen, die geringfügige Schwankungen größerer Werte enthalten, ist es wichtig, den Bereich der Wertachse (Y) festzulegen. 

So kann beispielsweise ein Rückgang der Menge an erfolgreichen Anforderungen von 99,99 Prozent auf 99,5 Prozent eine deutliche Verringerung der Servicequalität bedeuten. Bei Verwendung der Standardeinstellungen des Diagramms ist eine solche geringfügige Schwankung jedoch nur sehr schwer oder sogar überhaupt nicht erkennbar. In diesem Fall können Sie die Untergrenze des Diagramms auf 99 Prozent festlegen, damit ein geringfügiger Rückgang leichter zu erkennen ist. 

Ein weiteres Beispiel sind Schwankungen beim verfügbaren Arbeitsspeicher. In diesem Szenario erreicht der Wert aus technischen Gründen niemals Null. Wenn Sie hier den Bereich auf einen höheren Wert festlegen, ist eine geringere Arbeitsspeicherverfügbarkeit leichter zu erkennen. 

Zum Steuern des Bereichs der Y-Achse öffnen Sie das Diagrammmenü ( **...** ). Wählen Sie dann **Diagrammeinstellungen** aus, um auf die erweiterten Diagrammeinstellungen zuzugreifen.

![Screenshot mit hervorgehobener Auswahl der Diagrammeinstellungen](./media/metrics-charts/033.png)

Ändern Sie die Werte im Abschnitt **Bereich der Y-Achse**, oder wählen Sie **Automatisch** aus, um die Standardwerte wiederherzustellen.
 
 ![Screenshot mit hervorgehobenem Abschnitt für den Bereich der Y-Achse](./media/metrics-charts/034.png)

> [!WARNING]
> Wenn Sie die Grenzen der Y-Achse für Diagramme festlegen müssen, mit denen die Anzahlwerte oder Summen für einen Zeitraum nachverfolgt werden (mithilfe von Count-, Sum-, Min- oder Max-Aggregationen), sollten Sie normalerweise eine feste Zeitgranularität angeben. In diesem Fall sollten Sie nicht auf die automatischen Standardwerte zurückgreifen. 
>
> Sie wählen eine feste Zeitgranularität aus, da sich die Diagrammwerte ändern, wenn nach dem Ändern der Größe des Browserfensters oder der Bildschirmauflösung durch den Benutzer die Zeitgranularität automatisch geändert wird. Die resultierende Änderung der Zeitgranularität wirkt sich auf die Darstellung des Diagramms aus und macht die aktuelle Auswahl des Y-Achsenbereichs ungültig.

## <a name="line-colors"></a>Linienfarben

Nachdem Sie die Diagramme konfiguriert haben, werden den Diagrammlinien automatisch Farben aus einer Standardpalette zugewiesen. Sie können diese Farben ändern.

Um die Farbe einer Diagrammlinie zu ändern, wählen Sie den farbigen Balken in der zum Diagramm gehörenden Legende aus. Das Dialogfeld für die Farbauswahl wird geöffnet. Verwenden Sie die Farbauswahl, um die Linienfarbe zu konfigurieren.

![Screenshot zum Ändern der Farbe](./media/metrics-charts/035.png)

Ihre angepassten Farben bleiben erhalten, wenn Sie das Diagramm an ein Dashboard anheften. Im folgenden Abschnitt wird erläutert, wie Sie ein Diagramm anheften.

## <a name="pinning-to-dashboards"></a>Anheften an Dashboards 

Nachdem Sie ein Diagramm konfiguriert haben, können Sie es einem Dashboard hinzufügen. Durch Anheften eines Diagramms an ein Dashboard können Sie es für Ihr Team zugänglich machen. Sie können auch Einblicke gewinnen, indem Sie es im Kontext anderer Überwachungstelemetriedaten anzeigen.

Zum Anheften eines konfigurierten Diagramms an ein Dashboard wählen Sie rechts oben im Diagramm **An Dashboard anheften** aus.

![Screenshot zum Anheften eines Diagramms an ein Dashboard](./media/metrics-charts/036.png)

## <a name="alert-rules"></a>Warnungsregeln

Sie können Ihre Visualisierungskriterien verwenden, um eine metrikbasierte Warnungsregel zu erstellen. Die neue Warnungsregel enthält die Dimensionen für Zielressource, Metrik, Teilung und Filter aus dem Diagramm. Diese Einstellungen können Sie im Erstellungsbereich für Warnungsregeln ändern.

Wählen Sie zuerst **Neue Warnungsregel** aus.

![Screenshot der rot hervorgehobenen Schaltfläche „Neue Warnungsregel“](./media/metrics-charts/042.png)

Der Erstellungsbereich für Warnungsregeln wird geöffnet. In diesem Bereich werden die Metrikdimensionen des Diagramms angezeigt. Die Felder im Bereich sind bereits aufgefüllt, um Ihnen beim Anpassen der Regel zu helfen.

![Screenshot des Erstellungsbereichs für Regeln](./media/metrics-charts/041.png)

Weitere Informationen finden Sie unter [Erstellen, Anzeigen und Verwalten von Metrikwarnungen](../alerts/alerts-metric.md).

## <a name="correlate-metrics-to-logs"></a>Korrelieren von Metriken mit Protokollen
„Drilldown in Protokolle ausführen“ wurde entwickelt, damit Kunden die Grundursache von Anomalien leichter anhand des Metrikdiagramms diagnostizieren können. Mithilfe von „Drilldown in Protokolle ausführen“ können Kunden Spitzen im Metrikdiagramm mit Protokollen und Abfragen korrelieren. 

Bevor näher auf die Benutzeroberfläche eingegangen wird, werden zunächst die verschiedenen Typen von Protokollen und Abfragen vorgestellt. 

| Begriff             | Definition  | 
|------------------|-------------|
| Aktivitätsprotokolle    | Bietet Einblicke in die Vorgänge für jede Azure-Ressource im Abonnement von außen (die Verwaltungsebene) sowie Aktualisierungen zu Service Health-Ereignissen. Über das Aktivitätsprotokoll können Sie für jeden Schreibvorgang (PUT, POST, DELETE), der für die Ressourcen Ihres Abonnements durchgeführt wurde, ermitteln, welcher Benutzer welche Aktion zu welchem Zeitpunkt durchgeführt hat. Es gibt jeweils ein Aktivitätsprotokoll für jedes Azure-Abonnement.  |   
| Diagnoseprotokoll   | Dieses Protokoll bietet einen Einblick in Vorgänge, die innerhalb einer Azure-Ressource (der Datenebene) ausgeführt wurden, z. B. das Abrufen eines Geheimnisses aus einem Schlüsseltresor oder das Senden einer Anforderung an eine Datenbank. Der Inhalt dieser Protokolle variiert je nach Azure-Dienst und -Ressourcentyp. **Hinweis:** Das Diagnoseprotokoll muss vom Dienst bereitgestellt und vom Kunden aktiviert werden.  | 
| Empfohlenes Protokoll | Szenariobasierte Abfragen, mit denen Kunden Anomalien im Metrik-Explorer untersuchen können  |

Derzeit ist „Drilldown in Protokolle ausführen“ für ausgewählte Ressourcenanbieter verfügbar. Für folgende Ressourcenanbieter wird die vollständige Funktionalität „Drilldown in Protokolle ausführen“ angeboten: 

* Application Insights 
* Autoscale 
* App-Dienste  
* Storage  

Nachstehend finden Sie einen Beispielscreenshot der Benutzeroberfläche für den Ressourcenanbieter „Application Insights“.

![Spitze bei Fehlern auf dem Blatt mit Application Insights-Metriken](./media/metrics-charts/drill-into-log-ai.png)

Wenn Sie eine Diagnose für die Spitze in den fehlerhaften Anforderungen durchführen möchten, klicken Sie auf „Drilldown in Protokolle ausführen“.

![Screenshot des Dropdownmenüs „Drilldown für Protokolle ausführen“](./media/metrics-charts/drill-into-logs-dropdown.png)

Wenn Sie auf die Fehleroption klicken, werden Sie zu einem benutzerdefinierten Fehlerblatt weitergeleitet, auf dem die fehlgeschlagenen Vorgänge, die häufigsten Ausnahmetypen und die Abhängigkeiten angezeigt werden. 

![Screenshot des Fehlerblatts für Application Insights](./media/metrics-charts/ai-failure-blade.png)

### <a name="common-problems-with-drill-into-logs"></a>Häufige Probleme mit „Drilldown für Protokolle ausführen“

* Protokoll und Abfragen sind deaktiviert: Damit empfohlene Protokolle und Abfragen angezeigt werden, müssen Sie Ihre Diagnoseprotokolle an Log Analytics weiterleiten. [In diesem Artikel](./diagnostic-settings.md) erfahren Sie, wie dies funktioniert. 
* Aktivitätsprotokolle werden nur bereitgestellt: Das Feature „Drilldown für Protokolle ausführen“ ist nur für ausgewählte Ressourcenanbieter verfügbar. Aktivitätsprotokolle werden standardmäßig bereitgestellt. 

 
## <a name="troubleshooting"></a>Problembehandlung

Wenn im Diagramm keine Daten angezeigt werden, sehen Sie sich die folgenden Informationen zur Problembehandlung an:

* Filter gelten für alle Diagramme im Bereich. Stellen Sie beim Arbeiten mit einem Diagramm sicher, dass Sie keinen Filter festlegen, mit dem alle Daten eines anderen Diagramms ausgeschlossen werden.

* Um verschiedene Filter für verschiedene Diagramme festzulegen, erstellen Sie die Diagramme in unterschiedlichen Blättern. Speichern Sie die Diagramme dann als separate Favoriten. Auf Wunsch können Sie die Diagramme an das Dashboard anheften, um sie zusammen anzuzeigen.

* Wenn Sie ein Diagramm nach einer Eigenschaft segmentieren, die nicht in der Metrik definiert ist, werden im Diagramm keine Inhalte angezeigt. Versuchen Sie, die Segmentierung (Aufteilung) zu löschen, oder wählen Sie eine andere Eigenschaft aus.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Erstellen von Dashboards mit ausführbaren Aktionen mithilfe von Metriken finden Sie unter [Erstellen von benutzerdefinierten KPI-Dashboards](../app/tutorial-app-dashboards.md).
