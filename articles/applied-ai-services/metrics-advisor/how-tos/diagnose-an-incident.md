---
title: Diagnostizieren eines Incidents mit Metrics Advisor
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie mit Metrics Advisor Incidents diagnostizieren und detaillierte Ansichten zu Anomalien in Ihren Daten erhalten.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 04/15/2021
ms.author: mbullwin
ms.openlocfilehash: da5a73ba4abb2ce696ac833021a96008f73d7f2f
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2021
ms.locfileid: "114341524"
---
# <a name="diagnose-an-incident-using-metrics-advisor"></a>Diagnostizieren eines Incidents mit Metrics Advisor

## <a name="what-is-an-incident"></a>Was ist ein Incident? 

Wenn innerhalb einer Metrik zu einem bestimmten Zeitpunkt Anomalien in mehreren Zeitreihen festgestellt werden, fasst Metrics Advisor Anomalien mit **der gleichen Ursache** automatisch zu einem Incident zusammen. Ein Incident ist in der Regel ein Hinweis auf ein echtes Problem. Metrics Advisor führt weitere Analysen durch und liefert automatisch Erkenntnisse aus der Grundursachenanalyse. 

Dies erspart Kunden die Mühe, sich jede einzelne Anomalie anzusehen, weil schnell der wichtigste Faktor gefunden wird, der zu einem Problem beiträgt. 

Eine von Metrics Advisor generierte Warnung kann mehrere Incidents enthalten. Jeder Incident kann mehrere Anomalien enthalten, die in verschiedenen Zeitreihen mit dem gleichen Zeitstempel erfasst wurden. 

## <a name="paths-to-diagnose-an-incident"></a>Pfade zum Diagnostizieren eines Incidents

- **Diagnose anhand einer Warnungsbenachrichtigung**

    Wenn Sie einen Hook des Typs „E-Mail/Teams“ konfiguriert und mindestens eine Konfiguration für Warnungen eingerichtet haben, erhalten Sie kontinuierlich Warnungen zu Incidents, die von Metrics Advisor analysiert werden. Then you will receive continuous alert notifications escalating incidents that are analyzed by Metrics Advisor. In der Benachrichtigung sind eine Incidentliste und Kurzbeschreibung enthalten. Für jeden Incident gibt es die Schaltfläche **Diagnose**. Wenn Sie diese auswählen, gelangen Sie auf die Detailseite des Incidents, auf der Sie die Erkenntnisse der Diagnose einsehen können. 

    :::image type="content" source="../media/diagnostics/alert-notification.png" alt-text="Diagnose anhand einer Warnungsbenachrichtigung":::

- **Diagnose anhand eines Incidents im „Incident-Hub“**

    Es gibt eine Zentrale in Metrics Advisor, in der alle erfassten Incidents gesammelt werden und die die Nachverfolgung laufender Probleme erleichtert. Wenn Sie auf der linken Navigationsleiste die Registerkarte **Incident-Hub** auswählen, werden alle Incidents in den ausgewählten Metriken aufgeführt. Wählen Sie in der Incidentliste eine dieser Informationen aus, um detaillierte Diagnoseerkenntnisse anzuzeigen. 

    :::image type="content" source="../media/diagnostics/incident-list.png" alt-text="Diagnose anhand eines Incidents im Incident-Hub":::

- **Diagnose anhand eines Incidents auf der Seite „Metriken“**

    Auf der Detailseite von Metriken befindet sich eine Registerkarte mit dem Namen **Incidents**, auf der die neuesten Incidents aufgeführt sind, die für diese Metrik erfasst wurden. Die Liste kann nach Schweregrad der Incidents oder dem Dimensionswert der Metriken gefiltert werden. 

    Wenn Sie in der Liste einen Incident auswählen, gelangen Sie zu dessen Detailseite, auf der Sie Erkenntnisse der Diagnose einsehen können. 

    :::image type="content" source="../media/diagnostics/incident-in-metrics.png" alt-text="Diagnose anhand eines Incidents auf der Seite „Metriken“":::

## <a name="typical-diagnostic-flow"></a>Typischer Ablauf einer Diagnose

Nachdem Sie zur Detailseite des Incidents weitergeleitet wurden, können Sie die Erkenntnisse nutzen, die automatisch von Metrics Advisor analysiert werden, um schnell die Grundursache eines Problems zu finden, oder mit dem Analysetool die Auswirkungen des Problems weiter auswerten. Die Seite mit den Incidentdetails enthält drei Abschnitte, die den drei Hauptschritten zur Diagnose eines Incidents entsprechen. 

### <a name="step-1-check-summary-of-current-incident"></a>Schritt 1: Überprüfen der Zusammenfassung des aktuellen Incidents

Der erste Abschnitt enthält eine Zusammenfassung des aktuellen Incidents, einschließlich grundlegender Informationen, Aktionen und Ablaufverfolgungen und einer analysierten Grundursache. 

- Es werden grundlegende Informationen für die Bereiche „Wichtigste betroffene Reihen“ (mit Diagramm), „Start-/Endzeit der Beeinträchtigung“, „Schweregrad des Incidents“ und „Gesamte Anomalien“ angezeigt. Anhand dieser Informationen erhalten Sie ein grundlegendes Verständnis eines laufenden Problems und seiner Auswirkungen. 
- Aktionen und Ablaufverfolgungen werden verwendet, um die Zusammenarbeit des Teams bei einem laufenden Incident zu erleichtern. Mitunter muss ein Incident von Mitgliedern mehrerer Teams analysiert und behoben werden. Jeder Benutzer mit der Berechtigung zum Anzeigen des Incidents kann eine Aktion oder ein Ablaufverfolgungsereignis hinzufügen. 

    Nach der Diagnose des Incidents und der Ermittlung der Grundursache kann ein Mitarbeiter beispielsweise einen Suchbegriff mit dem Typ „angepasst“ hinzufügen und die Grundursache in den Abschnitt für Kommentare eingeben. Belassen Sie den Status auf „Aktiv“. Dann können andere Teamkollegen die gleichen Informationen teilen und wissen, dass jemand an der Behebung arbeitet. Sie können auch ein Azure DevOps-Element hinzufügen, um den Incident mit einer bestimmten Aufgabe oder einem bestimmten Programmfehler nachzuverfolgen. 


- Bei der analysierten Grundursache handelt es sich um ein automatisch analysiertes Ergebnis. Von Metrics Advisor wird eine Analyse aller Anomalien durchgeführt, die für eine Zeitreihe innerhalb einer Metrik mit verschiedenen Dimensionswerten mit demselben Zeitstempel erfasst wurden. Anschließend wird eine Korrelation vorgenommen, um verwandte Anomalien zu gruppieren. Außerdem werden Informationen zur Grundursache generiert. 

:::image type="content" source="../media/diagnostics/incident-summary.png" alt-text="Incidentzusammenfassung":::                                                                                                                                                                                   
Bei Metriken mit mehreren Dimensionen werden häufig mehrere Anomalien gleichzeitig erkannt. Diese Anomalien können jedoch dieselbe Grundursache haben. Anstatt alle Anomalien einzeln zu analysieren, sollte die Nutzung der **analysierten Grundursache** der effizienteste Weg zur Diagnose eines aktuellen Incidents sein.  


### <a name="step-2-view-cross-dimension-diagnostic-insights"></a>Schritt 2: Anzeigen dimensionsübergreifender Diagnoseerkenntnisse

Nachdem Sie die grundlegenden Informationen und die Erkenntnisse der automatischen Analyse erhalten haben, können Sie umfassende ausführlichere Informationen zum Anomaliestatus für andere Dimensionen unter derselben Metrik erhalten, indem Sie die **Diagnosestruktur** verwenden.

Bei Metriken mit mehreren Dimensionen wird die Zeitreihe von Metrics Advisor als Hierarchie kategorisiert, die als **Diagnosestruktur** bezeichnet wird. Es kann beispielsweise sein, dass die Metrik „Umsatz“ anhand von zwei Dimensionen überwacht wird: „Region“ und „Kategorie“. Auch wenn konkrete Dimensionswerte verwendet werden, muss ein **aggregierter** Dimensionswert vorhanden sein, z. B. **SUM**. Die Zeitreihen „Region“ = **SUM** und „Kategorie“ = **SUM** werden dann als Stammknoten der Struktur kategorisiert. Immer wenn unter der Dimension **SUM** eine Anomalie erfasst wird, kann hierfür eine Detailanalyse erfolgen, um zu ermitteln, welcher spezifische Dimensionswert am meisten zur Anomalie des übergeordneten Knotens beigetragen hat. Wählen Sie die einzelnen Knoten aus, um sie aufzuklappen und detaillierte Informationen anzuzeigen. 

:::image type="content" source="../media/diagnostics/cross-dimension-diagnostic.png" alt-text="Dimensionsübergreifende Diagnose mithilfe der Diagnosestruktur"::: 

- So aktivieren Sie einen „aggregierten“ Dimensionswert in Ihren Metriken

    Metrics Advisor unterstützt Rollups für Dimensionen, um einen „aggregierten“ Dimensionswert zu berechnen. Die Diagnosestruktur unterstützt die Diagnose der Aggregationen **SUM, AVG, MAX, MIN und COUNT**. Um einen „aggregierten“ Dimensionswert zu aktivieren, können Sie die Funktion „Rollup“ während der Datenerfassung aktivieren. Stellen Sie sicher, dass Ihre Metriken **mathematisch berechenbar** sind und dass die aggregierte Dimension einen echten geschäftlichen Nutzen hat. 

    :::image type="content" source="../media/diagnostics/automatic-roll-up.png" alt-text="Rollupeinstellungen":::  

- Wenn Ihre Metriken keinen „aggregierten“ Dimensionswert enthalten

    Wenn es in Ihren Metriken keinen „aggregierten“ Dimensionswert gibt und die „Rollup“-Funktion bei der Datenerfassung nicht aktiviert ist. Für die „aggregierte“ Dimension wird kein Metrikwert berechnet. Sie wird als grauer Knoten in der Struktur angezeigt und kann aufgeklappt werden, um ihre Unterknoten anzuzeigen. 

#### <a name="legend-of-diagnostic-tree"></a>Legende der Diagnosestruktur

Die Diagnosestruktur enthält drei Arten von Knoten: 
- **Blauer Knoten**: entspricht einer Zeitreihe mit realem Metrikwert. 
- **Grauer Knoten**: entspricht einer virtuellen Zeitreihe ohne Metrikwert und ist ein logischer Knoten. 
- **Roter Knoten**: entspricht der Zeitreihe mit den weitreichendsten Auswirkungen des aktuellen Incidents.

Bei jedem Knoten wird der anormale Zustand von der Farbe des Knotenrahmens beschrieben.
- **Roter Rahmen** bedeutet, dass eine Anomalie in der Zeitreihe erfasst wurde, die dem Zeitstempel des Incidents entspricht.
- **Nicht roter Rahmen** bedeutet, dass keine Anomalie in der Zeitreihe erfasst wurde, die dem Zeitstempel des Incidents entspricht.

#### <a name="display-mode"></a>Anzeigemodus

Es gibt zwei Anzeigemodi für eine Diagnosestruktur: nur Anomaliereihen oder Hauptanteile anzeigen. 

- Der Modus **Nur Anomalienreihen anzeigen** ermöglicht Kunden, sich auf aktuelle Anomalien zu konzentrieren, die in verschiedenen Zeitreihen erfasst wurden, und die Grundursache der am stärksten betroffenen Zeitreihen zu diagnostizieren. 
- Im Modus **Hauptanteile anzeigen** kann der Kunde den anormalen Zustand von Hauptanteilen der Zeitreihe mit der höchsten Auswirkung überprüfen. In diesem Modus zeigt die Struktur sowohl Reihen mit erkannter Anomalie als auch Reihen ohne Anomalie. Konzentrieren Sie sich jedoch mehr auf wichtige Reihen. 

#### <a name="analyze-options"></a>Analyseoptionen

- **Deltaverhältnis anzeigen**

    „Deltaverhältnis“ ist der Prozentsatz des aktuellen Knotendeltas im Vergleich zum Delta des übergeordneten Knotens. Die zugehörige Formel sieht so aus: 

    (Realer Wert des aktuellen Knotens - erwarteter Wert des aktuellen Knotens) : (realer Wert des übergeordneten Knotens - erwarteter Wert des übergeordneten Knotens) x 100 %

    Diese Formel wird verwendet, um den Hauptanteil des Deltas des übergeordneten Knotens zu analysieren. 

- **Wertanteil anzeigen**

    „Wertanteil“ ist der Prozentsatz des aktuellen Knotenwerts im Vergleich zum Wert des übergeordneten Knotens. Die zugehörige Formel sieht so aus: 

    (Realer Wert des aktuellen Knotens : Realer Wert des übergeordneten Knotens) x 100 %

    Diese Formel wird verwendet, um den Anteil des aktuellen Knotens am Ganzen zu bewerten. 

Mithilfe der „Diagnosestruktur“ können Kunden die Grundursache des aktuellen Incidents in einer bestimmten Dimension ermitteln. Damit entfällt für den Kunden der Aufwand, jede einzelne Anomalie zu betrachten oder verschiedene Dimensionen zu durchforsten, um den Hauptbeitrag zur Anomalie zu finden.  

### <a name="step-3-view-cross-metrics-diagnostic-insights-using-metrics-graph"></a>Schritt 3: Anzeigen metrikübergreifender Diagnoseerkenntnisse per „Metrikgraph“

Da es mitunter schwierig sein kann, ein Problem durch Überprüfung des Anomaliestatus von nur einer Metrik zu analysieren, müssen Sie ggf. mehrere Metriken korrelieren. Kunden können einen **Metrikgraphen** konfigurieren, mit dem die Beziehungen zwischen Metriken angegeben werden. Informationen zu den ersten Schritte finden Sie unter [Erstellen eines Metrikgraphen](metrics-graph.md).

#### <a name="check-anomaly-status-on-root-cause-dimension-within-metrics-graph"></a>Überprüfen des Anomaliestatus für die Dimension der Grundursache im „Metrikgraphen“

Indem das obige Ergebnis der dimensionsübergreifenden Diagnose verwendet wird, ist die Grundursache auf einen bestimmten Dimensionswert beschränkt. Verwenden Sie dann den „Metrikgraphen“, und filtern Sie nach der Grundursache der analysierten Dimension, um den Anomaliestatus für andere Metriken zu überprüfen. 

Angenommen, es gibt einen Incident, der in den Metriken zum Umsatz erfasst wird. Die am stärksten betroffene Reihe befindet sich in der globalen Region mit „region“ = SUM. Mithilfe der dimensionsübergreifenden Diagnose wurde die Grundursache in „region“ = „Karachi“ gefunden. Es gibt einen vorkonfigurierten Metrikgraphen, einschließlich der Metriken „revenue“ (Umsatz), „cost“ (Kosten), „DAU“ (Aktive Benutzer pro Tag), „PLT (Page Load Time [Seitenladezeit])“ und „CHR (Cache Hit Rate, [Cachetrefferquote])“. 

Metrics Advisor filtert den Metrikgraphen automatisch nach der Grundursachendimension „region“ = „Karachi“ und zeigt den Anomaliestatus für jede Metrik an. Durch die Analyse des Verhältnisses zwischen Metriken und Anomaliestatus können Kunden weitere Erkenntnisse darüber gewinnen, was die letztendliche Grundursache ist. 

:::image type="content" source="../media/diagnostics/cross-metrics-analysis.png" alt-text="Metrikübergreifende Analyse":::

#### <a name="auto-related-anomalies"></a>Automatisch verknüpfte Anomalien

Durch Anwendung des Filters für die Grundursachendimension auf den Metrikgraphen werden Anomalien bei jeder Metrik mit dem Zeitstempel des aktuellen Incidents automatisch verknüpft. Diese Anomalien sollten mit der ermittelten Grundursache des aktuellen Incidents in Zusammenhang stehen. 

:::image type="content" source="../media/diagnostics/auto-related-anomalies.png" alt-text="Automatisch verknüpfte Anomalien":::

## <a name="next-steps"></a>Nächste Schritte 

- [Anpassen der Anomalieerkennung anhand von Feedback](anomaly-feedback.md)
- [Konfigurieren von Metriken und Optimieren der Erkennungskonfiguration](configure-metrics.md)