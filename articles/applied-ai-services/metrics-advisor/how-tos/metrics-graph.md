---
title: Metrikgraph von Metrics Advisor
titleSuffix: Azure Cognitive Services
description: Es wird beschrieben, wie Sie Ihren Metrikgraphen konfigurieren und zugehörige Anomalien in Ihren Daten visualisieren.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mbullwin
ms.openlocfilehash: 7842cdd6d5336e90346d666d8c2d31d20c19caa0
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2021
ms.locfileid: "114341512"
---
# <a name="how-to-build-a-metrics-graph-to-analyze-related-metrics"></a>Gewusst wie: Erstellen eines Metrikgraphen zum Analysieren von verwandten Metriken

Jede Zeitreihe in Metrics Advisor wird separat von einem Modell überwacht, das aus Verlaufsdaten lernen kann, um künftige Trends vorherzusagen. Anomalien werden erkannt, wenn ein Datenpunkt nicht in das Verlaufsmuster passt. In einigen Fällen können jedoch mehrere Metriken miteinander in Beziehung stehen, und Anomalien müssen anhand mehrerer Metriken analysiert werden. Der **Metrikgraph** ist das Tool, das Ihnen dabei hilft. 

Wenn es beispielsweise mehrere Metriken gibt, die Ihr Unternehmen aus verschiedenen Blickwinkeln überwachen, wird die Erkennung von Anomalien entsprechend umgesetzt. In realen Geschäftssituationen können jedoch Anomalien, die bei mehreren Metriken festgestellt werden, miteinander in Beziehung stehen. Die Entdeckung dieser Beziehungen und die darauf basierende Analyse der Grundursache wäre hilfreich, wenn es darum geht, reale Probleme anzugehen. Der Metrikgraph hilft bei der automatischen Korrelation von Anomalien, die bei zusammengehörigen Metriken entdeckt werden, um die Problembehandlung zu beschleunigen. 

## <a name="select-a-metric-to-put-the-first-node-to-the-graph"></a>Auswählen einer Metrik zum Einfügen des ersten Knotens in den Graphen

Klicken Sie auf der Navigationsleiste auf die Registerkarte **Metrikgraph**. Der erste Schritt beim Erstellen eines Metrikgraphen besteht darin, einen Knoten in den Graphen einzufügen. Wählen Sie oben auf der Seite einen Datenfeed und eine Metrik aus. Im unteren Bereich wird ein Knoten angezeigt. 

:::image type="content" source="../media/graph/metrics-graph-select.png" alt-text="Metrik auswählen":::

## <a name="add-a-noderelation-on-existing-node"></a>Hinzufügen eines Knotens bzw. einer Beziehung im vorhandenen Knoten

Als Nächstes müssen Sie einen weiteren Knoten hinzufügen und eine Beziehung mit einem oder mehreren vorhandenen Knoten angeben. Wählen Sie einen vorhandenen Knoten aus, und klicken Sie mit der rechten Maustaste darauf. Ein Kontextmenü mit mehreren Optionen wird angezeigt. 

Wählen Sie **Beziehung hinzufügen** aus. Sie können eine weitere Metrik auswählen und den Typ der Beziehung zwischen den beiden Knoten angeben. Sie können auch bestimmte Dimensionsfilter anwenden. 

:::image type="content" source="../media/graph/metrics-graph-node-action.png" alt-text="Hinzufügen eines Knotens und einer Beziehung":::

Nachdem Sie die obigen Schritte wiederholt haben, verfügen Sie über einen Metrikgraphen, mit dem die Beziehungen zwischen allen verwandten Metriken beschrieben werden.

Im Graphen können Sie weitere Aktionen durchführen: 
1.  Knoten löschen
2.  Zu Metriken wechseln
3.  Zum Incidenthub wechseln
4.  Expand
5.  Beziehung löschen

## <a name="legend-of-metrics-graph"></a>Legende des Metrikgraphen

Jeder Knoten im Graphen stellt eine Metrik dar. Im Metrikgraphen gibt es vier Arten von Knoten:

-  **Grüner Knoten**: der Knoten, der darstellt, dass der aktuelle Incidentschweregrad der Metrik „Niedrig“ ist.
- **Orangefarbener Knoten**: der Knoten, der darstellt, dass der aktuelle Incidentschweregrad der Metrik „Mittel“ ist.
- **Roter Knoten**: der Knoten, der darstellt, dass der aktuelle Incidentschweregrad der Metrik „Hoch“ ist.
- **Blauer Knoten**: der Knoten ohne Anomalieschweregrad.


## <a name="view-related-metrics-anomaly-status-in-incident-hub"></a>Anzeigen des Anomaliestatus für verwandte Metriken im Incidenthub

Beim Erstellen des Metrikgraphen können Sie bei jeder Erkennung einer Anomalie für die Metriken im Graphen den zugehörigen Anomaliestatus anzeigen und eine allgemeine Ansicht des Incidents aufrufen. 

Klicken Sie im Graphen in einen Incident, und scrollen Sie nach unten zur Option **Cross Metrics Analysis** (Metrikübergreifende Analyse), die sich unterhalb der Diagnoseinformationen befindet.

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="Anzeigen verwandter Metriken und Anomalien":::

## <a name="next-steps"></a>Nächste Schritte

- [Anpassen der Anomalieerkennung anhand von Feedback](anomaly-feedback.md)
- [Diagnostizieren eines Incidents](diagnose-an-incident.md)
- [Konfigurieren von Metriken und Optimieren der Erkennungskonfiguration](configure-metrics.md)