---
title: Handhabung der Eingabeverzögerung in Microsoft Sentinel | Microsoft Docs
description: Behandlung der Eingangsverzögerung in den geplanten Analyseregeln von Microsoft Sentinel.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/25/2021
ms.author: bagol
ms.openlocfilehash: 41143675d54e4353f7ea5402a4503c78a74a12e2
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522933"
---
# <a name="handle-ingestion-delay-in-scheduled-analytics-rules"></a>Behandlung der Eingabeverzögerung in geplanten Analyseregeln

Obwohl Microsoft Sentinel Daten aus [verschiedenen Quellen](connect-data-sources.md) aufnehmen kann, kann die Aufnahmezeit für jede Datenquelle unter verschiedenen Umständen unterschiedlich sein.

In diesem Artikel wird beschrieben, wie sich eine Verzögerung bei der Datenaufnahme auf Ihre geplanten Analyseregeln auswirken kann und wie Sie diese Lücken schließen können.

## <a name="why-delay-is-significant"></a>Warum Verzögerungen wichtig sind

Sie könnten zum Beispiel eine benutzerdefinierte Erkennungsregel schreiben, die **Abfrage alle** ausführen und **Daten aus den letzten** Feldern nachschlagen, damit die Regel alle fünf Minuten ausgeführt wird und Daten aus den letzten fünf Minuten nachschlägt:

:::image type="content" source="media/ingestion-delay/create-rule.png" alt-text=" Screenshot des Analytics-Regel-Assistenten - Fenster Neue Regel erstellen " border="false":::

Die **Lookup-Daten aus dem letzten** Feld definieren eine Einstellung, die als *look-back* Periode bekannt ist. Im Idealfall, wenn es keine Verzögerung gibt, entgeht dieser Erkennung kein Ereignis, wie im folgenden Diagramm dargestellt:

:::image type="content" source="media/ingestion-delay/look-back.png" alt-text="Diagramm mit einem fünfminütigen Rückblicksfenster " border="false":::.

Das Ereignis tritt ein, sobald es erzeugt wird, und wird in den Zeitraum *lookback* einbezogen.

Angenommen, es gibt eine Verzögerung für Ihre Datenquelle. Nehmen wir für dieses Beispiel an, dass das Ereignis zwei Minuten nach seiner *Generierung* *aufgenommen* wurde. Die Verzögerung beträgt zwei Minuten:

:::image type="content" source="media/ingestion-delay/look-back-delay.png" alt-text=" Diagramm mit fünfminütigen Look-Back-Fenstern mit einer Verzögerung von zwei Minuten. " border="false":::

Das Ereignis wird innerhalb des ersten Lookback-Zeitraums generiert, aber nicht bei der ersten Ausführung in Ihren Microsoft Sentinel-Arbeitsbereich aufgenommen. Wenn die geplante Abfrage das nächste Mal ausgeführt wird, wird das Ereignis aufgenommen, aber der zeitgenerierte Filter entfernt das Ereignis, da es mehr als fünf Minuten zurückliegt. In diesem Fall  **löst die Regel keinen Alarm** aus.

## <a name="how-to-handle-delay"></a>Wie man mit Verzögerungen umgeht

Um das Problem zu lösen, müssen Sie die Verzögerung für Ihren Datentyp kennen. In diesem Beispiel wissen Sie bereits, dass die Verzögerung zwei Minuten beträgt. 

Für Ihre eigenen Daten können Sie die Verzögerung verstehen, indem Sie die Kusto-Funktion `ingestion_time()` verwenden und die Differenz zwischen **TimeGenerated** und der Ingestion-Zeit berechnen. Weitere Informationen finden Sie unter [Berechnung der Ingestion-Verzögerung](#calculate-ingestion-delay).

Nachdem Sie die Verzögerung ermittelt haben, können Sie das Problem wie folgt beheben:

- **Verlängern Sie den Look-Back Zeitraum**. Die grundlegende Intuition sagt Ihnen, dass eine Vergrößerung des Look-back-Zeitraums helfen wird. Da Ihr Look-back-Zeitraum fünf Minuten und Ihre Verzögerung zwei Minuten beträgt, können Sie den Look-back-Zeitraum auf *sieben* Minuten einstellen, um dieses Problem zu lösen. Zum Beispiel in Ihren Regeleinstellungen:

    :::image type="content" source="media/ingestion-delay/set-look-back.png" alt-text="Screenshot, der die Einstellung des Look-back-Fensters auf sieben Minuten zeigt.":::

    Das folgende Diagramm zeigt, dass der Look-back-Zeitraum nun das verpasste Ereignis enthält:

    :::image type="content" source="media/ingestion-delay/longer-look-back.png" alt-text="Diagramm, das siebenminütige Look-back-Fenster mit einer Verzögerung von zwei Minuten zeigt." border="false":::

- **Behandeln Sie die Duplizierung**. Lediglich die Erhöhung des Look-back-Zeitraums kann zu Doppelungen führen, da sich die Look-back-Fenster nun überschneiden. Ein anderes Ereignis kann zum Beispiel wie im folgenden Diagramm dargestellt aussehen:

    :::image type="content" source="media/ingestion-delay/overlapping-look-back.png" alt-text="Diagramm, das zeigt, wie überlappende Look-Back-Fenster zu Duplikaten führen." border="false":::

    Da der Wert des Ereignisses **TimeGenerated** in beiden Look-back-zeiträumen gefunden wird, löst das Ereignis zwei Alarme aus. Sie müssen einen Weg finden, die Duplikation zu lösen.

- **Zuordnung des Ereignisses zu einem bestimmten Look-back-zeitraum**. Im ersten Beispiel haben Sie Ereignisse verpasst, weil Ihre Daten nicht aufgenommen wurden, als die geplante Abfrage ausgeführt wurde. Sie haben den Look-back auf das Ereignis ausgedehnt, was jedoch zu Doppelungen füharte. Sie müssen das Ereignis mit dem Fenster verknüpfen, das Sie erweitert haben, um es zu enthalten.

    Dazu setzen Sie `ingestion_time() > ago(5m)` anstelle der ursprünglichen Regel `look-back = 5m`. Mit dieser Einstellung wird das Ereignis mit dem ersten Look-back-Fenster verknüpft. Beispiel:

    :::image type="content" source="media/ingestion-delay/ago-restriction.png" alt-text="Diagramm, das zeigt, wie die Einstellung der ago-Beschränkung Duplikation vermeidet." border="false":::

    Die Zeitbeschränkung für die Einnahme wird nun um die zusätzlichen zwei Minuten gekürzt, die Sie zum Look-back-Zeitraum hinzugefügt haben. Und für das erste Beispiel erfasst der zweite Look-back-zeitraum nun das Ereignis:

    :::image type="content" source="media/ingestion-delay/ago-restriction-capture.png" alt-text="Diagramm, das zeigt, wie die Einstellung der ago-Beschränkung das Ereignis erfasst " border="false":::

Die folgende Beispielabfrage fasst die Lösung für die Behebung von Verzögerungen bei der Datenaufnahme zusammen:

```kusto
let ingestion_delay = 2min;
let rule_look_back = 5min;
CommonSecurityLog
| where TimeGenerated >= ago(ingestion_delay + rule_look_back)
| where ingestion_time() > ago(rule_look_back)
```


## <a name="calculate-ingestion-delay"></a>Berechnung der Ingestion-Verzögerung

Standardmäßig sind die Regeln für geplante Microsoft Sentinel-Warnungen so konfiguriert, dass sie eine Look-back-zeit von 5 Minuten haben. Allerdings kann jede Datenquelle ihre eigene, individuelle Aufnahmeverzögerung haben. Wenn Sie mehrere Datentypen miteinander verbinden, müssen Sie die unterschiedlichen Verzögerungen für jeden Datentyp kennen, um den Look-back-Zeitraum korrekt konfigurieren zu können.

Der **Workspace-Nutzungsbericht**, der standardmäßig in Microsoft Sentinel bereitgestellt wird, enthält ein Dashboard, das die Latenz und Verzögerungen für die verschiedenen Datentypen anzeigt, die in Ihren Workspace fließen.

Beispiel:

:::image type="content" source="media/ingestion-delay/end-to-end-latency.png" alt-text="Screenshot des Arbeitsraumnutzungsberichts mit Darstellung der End-to-End-Latenz nach Tabelle":::


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter

- [Erstellen benutzerdefinierter Analyseregeln zum Erkennen von Bedrohungen](detect-threats-custom.md)
- [Anpassen von Warnungsdetails in Azure Sentinel](customize-alert-details.md)
- [Verwalten von Vorlagenversionen für Ihre geplanten Analyseregeln in Azure Sentinel](manage-analytics-rule-templates.md)