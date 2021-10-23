---
title: Application Insights-Nutzung (Impact) – Azure Monitor
description: Analysieren Sie, wie verschiedene Eigenschaften potenziell die Konvertierungsraten für Teile Ihrer Apps beeinflussen.
ms.topic: conceptual
author: mattmccleary
ms.author: mmcc
ms.date: 07/30/2021
ms.openlocfilehash: 0d09f93c5a0a541929169130feb921a8e65144be
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130133232"
---
# <a name="impact-analysis-with-application-insights"></a>Auswirkungsanalyse mit Application Insights

Impact analysiert, wie Ladezeiten und andere Eigenschaften Konvertierungsraten für verschiedene Teile Ihrer App beeinflussen. Genauer gesagt, wird ermittelt, wie **eine beliebige Dimension** einer **Seitenansicht**, eines **benutzerdefinierten Ereignisses** oder einer **Anforderung** sich auf die Verwendung anderer **Seitenansichten** oder **benutzerdefinierter Ereignisse** auswirkt. 

## <a name="still-not-sure-what-impact-does"></a>Sie wissen noch nicht, was Impact macht?

Betrachten Sie Impact als ultimatives Tool zur Beilegung der Meinungsverschiedenheit mit jemand aus Ihrem Team in der Frage, wie die Langsamkeit Ihrer Website in einem bestimmten Punkt darauf Einfluss hat, ob Benutzer auf der Website bleiben. Benutzer tolerieren Langsamkeit vielleicht in gewissem Maße, aber mit Impact können Sie bestimmen, wie Sie Optimierung und Leistung am besten in Einklang bringen, um die Benutzerkonvertierung zu maximieren.

Die Leistungsanalyse ist jedoch nur ein Teilbereich der Funktionen von Impact. Da Impact benutzerdefinierte Ereignisse und Dimensionen unterstützt, werden Fragen der Art „Wie korreliert die Browserauswahl des Benutzers mit unterschiedlichen Konvertierungsraten?“ mit ein paar Mausklicks beantwortet.

> [!NOTE]
> Ihre Application Insights-Ressource muss Seitenaufrufe oder benutzerdefinierte Ereignisse enthalten, damit die Impact-Analysearbeitsmappe genutzt werden kann. [Informieren Sie sich darüber, wie Sie Ihre App so einrichten können, dass Seitenansichten automatisch mit dem Application Insights-JavaScript-SDK erfasst werden](./javascript.md). Bedenken Sie auch, dass die Beispielgröße von Bedeutung ist, weil Sie die Korrelation analysieren.

## <a name="impact-analytics-workbook"></a>Impact-Analysearbeitsmappe 

Navigieren Sie in Ihren Application Insights-Ressourcen zu **Verwendung** > **Auswirkung**, und klicken Sie auf **Impact Analysis Workbook** (Impact-Analysearbeitsmappe). Alternativ können Sie auf der Registerkarte **Arbeitsmappen** auf **Öffentliche Vorlagen** und dann unter *Verwendung* auf **Analyse der Benutzerauswirkungen** klicken.

:::image type="content" source="./media/usage-impact/workbooks-gallery.png" alt-text="Screenshot: Arbeitsmappenkatalog für öffentliche Vorlagen" lightbox="./media/usage-impact/workbooks-gallery.png":::


### <a name="using-the-workbook"></a>Verwenden der Arbeitsmappe

:::image type="content" source="./media/usage-impact/selected-event.png" alt-text="Screenshot der Auswahloptionen für eine erste Seitenansicht, ein benutzerdefiniertes Ereignis oder eine Anforderung" lightbox="./media/usage-impact/selected-event.png":::

1. Wählen Sie ein Ereignis aus dem Dropdownmenü **Ausgewähltes Ereignis** aus.
2. Wählen Sie eine Metrik aus dem Dropdownmenü **Analysieren der Auswirkungen von** aus.
3. Wählen Sie ein Ereignis aus dem Dropdownmenü **Impacting event** (Beeinflussendes Ereignis) aus. 
1. Wenn Sie einen Filter hinzufügen möchten, nutzen Sie die Funktion **Add selected event filters** (Ausgewählte Filter hinzufügen) und/oder **Add impacting event filters** (Beeinflussende Ereignisfilter hinzufügen).


## <a name="is-page-load-time-impacting-how-many-people-convert-on-my-page"></a>Beeinflusst die Seitenladezeit, wie viele Personen zu meiner Seite konvertieren?

Beginnen Sie, Fragen mit der Impact-Arbeitsmappe zu beantworten, indem Sie eine erste Seitenansicht, ein benutzerdefiniertes Ereignis oder eine Anforderung auswählen.

1. Wählen Sie ein Ereignis aus dem Dropdownmenü **Ausgewähltes Ereignis** aus.
2. Behalten Sie für die Dropdownliste **Analysieren der Auswirkungen von** die Standardauswahl **Dauer** bei (in diesem Kontext ist **Dauer** ein Alias für **Seitenladezeit**).
3. Wählen Sie im Dropdownmenü **Impacting event** (Beeinflussendes Ereignis) ein benutzerdefiniertes Ereignis aus. Dieses Ereignis sollte einem Element der Benutzeroberfläche auf der Seitenansicht entsprechen, die Sie in Schritt 1 ausgewählt haben.


:::image type="content" source="./media/usage-impact/impact.png" alt-text="Screenshot: Beispiel mit ausgewähltem Ereignis als Startseite, analysiert nach Dauer" lightbox="./media/usage-impact/impact.png":::

## <a name="what-if-im-tracking-page-views-or-load-times-in-custom-ways"></a>Kann ich Seitenansichten oder Ladezeiten auf benutzerdefinierte Weise nachverfolgen?

Impact unterstützt sowohl standardmäßige als auch benutzerdefinierte Eigenschaften und Messungen. Sie haben die Wahl. Verwenden Sie anstelle der Dauer Filter zu primären und sekundären Ereignissen, um genauere Ergebnisse zu erzielen.

## <a name="do-users-from-different-countries-or-regions-convert-at-different-rates"></a>Unterscheiden sich die Konvertierungsraten von Benutzern aus unterschiedlichen Ländern oder Regionen?

1. Wählen Sie im Dropdownmenü **Ausgewähltes Ereignis** ein Ereignis aus.
2. Wählen Sie in der Dropdownliste **Analysieren der Auswirkungen von** „Land oder Region“.
3. Wählen Sie für die Dropdownliste **Beeinflussendes Ereignis** ein benutzerdefiniertes Ereignis aus, das einem Element der Benutzeroberfläche in der Seitenansicht entspricht, die Sie in Schritt 1 ausgewählt haben.

:::image type="content" source="./media/usage-impact/regions.png" alt-text="Screenshot: Beispiel mit ausgewähltem Ereignis als GET, analysiert nach Land und Region" lightbox="./media/usage-impact/regions.png":::

## <a name="how-does-the-impact-analysis-workbook-calculate-these-conversion-rates"></a>Wie berechnet die Impact-Analysearbeitsmappe diese Konvertierungsraten?

Die Impact-Analysearbeitsmappe ist unter der Haube abhängig vom [Pearson-Korrelationskoeffizienten](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient). Ergebnisse werden zwischen -1 und 1 berechnet, wobei -1 eine negative lineare Korrelation und 1 eine positive lineare Korrelation darstellt.

Die grundlegende Aufschlüsselung der Funktionsweise der Auswirkungsanalyse lautet wie folgt:

_A_ = Hauptseitenansicht/benutzerdefiniertes Ereignis/Anforderung Ihrer Wahl in der ersten Dropdownliste. **(Ausgewähltes Ereignis)**

_B_ = sekundäre Seitenansicht/sekundäres benutzerdefiniertes Ereignis Ihrer Wahl (**auf die Verwendung von**).

Impact untersucht ein Beispiel für alle Sitzungen von Benutzern in dem ausgewählten Zeitraum. Für jede Sitzung wird nach jedem Auftreten von _A_ gesucht.

Sitzungen werden dann aufgrund einer von zwei Bedingungen in zwei unterschiedliche Arten von _untergeordneten Sitzungen_ unterteilt:

- Eine konvertierte untergeordnete Sitzung besteht aus einer Sitzung, die mit einem _B_-Ereignis endet, und umfasst alle _A_-Ereignisse, die vor _B_ auftreten.
- Eine nicht konvertierte untergeordnete Sitzung liegt vor, wenn alle _A_ s ohne ein abschließendes _B_ auftreten.

Wie die Auswirkung letztlich berechnet wird, hängt davon ab, ob wir nach Metrik oder Dimension analysieren. Für Metriken wird für alle _A_ s in einer untergeordneten Sitzung der Mittelwert gebildet. Für Dimensionen hingegen trägt der Wert von jedem einzelnen _A_ _1/N_ zu dem Wert bei, der _B_ zugewiesen wird, wobei _N_ die Anzahl der _A_ s in der untergeordneten Sitzung ist.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Arbeitsmappen finden Sie in der [Übersicht über Arbeitsmappen](../visualize/workbooks-overview.md).
- Um mit der Nutzung zu beginnen, senden Sie [benutzerdefinierte Ereignisse](./api-custom-events-metrics.md#trackevent) oder [Seitenansichten](./api-custom-events-metrics.md#page-views).
- Wenn Sie bereits benutzerdefinierte Ereignisse oder Seitenansichten senden, finden Sie mithilfe der Nutzungstools heraus, wie Benutzer den Dienst verwenden.
    - [Trichter](usage-funnels.md)
    - [Vermerkdauer](usage-retention.md)
    - [Benutzerabläufe](usage-flows.md)
    - [Arbeitsmappen](../visualize/workbooks-overview.md)
    - [Hinzufügen von Benutzerkontext](./usage-overview.md)