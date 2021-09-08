---
title: Konfigurieren Ihrer Metrics Advisor-Instanz mithilfe des Webportals
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie Ihre Metrics Advisor-Instanz konfigurieren und die Ergebnisse der Anomalieerkennung optimieren.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 3190976b5b5261331d2b0adec333b3530ec45ea7
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2021
ms.locfileid: "114341632"
---
# <a name="how-to-configure-metrics-and-fine-tune-detection-configuration"></a>Konfigurieren von Metriken und Optimieren der Erkennungskonfiguration

Beginnen Sie mithilfe dieses Artikels damit, Ihre Metrics Advisor-Instanz über das Webportal zu konfigurieren. Um die Metriken nach einem bestimmten Datenfeed zu durchsuchen, wechseln Sie zur Seite **Datenfeeds**, und wählen Sie einen der Feeds aus. Dadurch wird eine Liste der zugeordneten Metriken angezeigt.

:::image type="content" source="../media/metrics/select-metric.png" alt-text="Metrik auswählen" lightbox="../media/metrics/select-metric.png":::

Wählen Sie einen der Metriknamen aus, um seine Details anzuzeigen. In dieser detaillierten Ansicht können Sie mithilfe der Dropdownliste rechts oben auf dem Bildschirm zu einer anderen Metrik im gleichen Datenfeed wechseln.

Beim ersten Anzeigen der Details einer Metrik können Sie eine Zeitreihe laden, indem Metrics Advisor eine Auswahl für Sie trifft oder Werte angeben, die für jede Dimension eingeschlossen werden sollen. 

Sie können auch Zeitbereiche auswählen und das Layout der Seite ändern.

> [!NOTE]
> - Die Startzeit ist eingeschlossen.
> - Die Endzeit ist ausgeschlossen. 

Klicken Sie auf die Registerkarte **Incidents**, um Anomalien anzuzeigen und einen Link zum [Incident hub](diagnose-an-incident.md) (Incidenthub) zu suchen.

## <a name="tune-the-detection-configuration"></a>Optimieren der Erkennungskonfiguration

Eine Metrik kann eine oder mehrere Erkennungskonfigurationen anwenden. Es gibt eine Standardkonfiguration für jede Metrik, die Sie je nach Ihren Überwachungsanforderungen bearbeiten oder hinzufügen können.

### <a name="tune-the-configuration-for-all-series-in-current-metric"></a>Optimieren der Konfiguration für alle Reihen in der aktuellen Metrik

Diese Konfiguration wird auf alle Reihen in dieser Metrik angewendet, mit Ausnahme der Reihen mit einer separaten Konfiguration. Standardmäßig wird eine Konfiguration auf Metrikebene angewendet, wenn Daten integriert werden. Diese wird im linken Bereich angezeigt. Benutzer können die Metrikebenenkonfiguration auf der Metrikseite direkt bearbeiten. 

Es gibt zusätzliche Parameter wie **Richtung** und **Valid anomaly** (Gültige Anomalie), die zur weiteren Optimierung der Konfiguration verwendet werden können. Sie können auch verschiedene Erkennungsmethoden kombinieren. 

:::image type="content" source="../media/configuration-combination.png" alt-text="Kombination von Konfigurationen" lightbox="../media/configuration-combination.png":::

### <a name="tune-the-configuration-for-a-specific-series-or-group"></a>Optimieren der Konfiguration für eine bestimmte Reihe oder Gruppe

Klicken Sie unter den Konfigurationsoptionen für die Metrikebene auf **Erweiterte Konfiguration**, um die Konfiguration auf Gruppenebene anzuzeigen. Sie können eine Konfiguration für eine einzelne Reihe oder eine Gruppe von Reihen hinzufügen, indem Sie auf das Symbol **+** in diesem Fenster klicken. Die Parameter ähneln den Konfigurationsparametern auf Metrikebene. Unter Umständen müssen Sie jedoch mindestens einen Dimensionswert für eine Konfiguration auf Gruppenebene angeben, um eine Gruppe von Reihen zu identifizieren. Geben Sie alle Dimensionswerte für die Konfiguration auf Reihenebene an, um eine bestimmte Reihe zu identifizieren. 

Diese Konfiguration wird anstelle der Konfiguration auf Metrikebene auf die Gruppe von Reihen oder bestimmte Reihen angewendet. Wenn Sie die Bedingungen für diese Gruppe festgelegt haben, speichern Sie sie.

:::image type="content" source="../media/advanced-configuration.png" alt-text="Erweiterte Konfiguration" lightbox="../media/advanced-configuration.png":::

### <a name="anomaly-detection-methods"></a>Anomalieerkennungsmethoden

Metrics Advisor bietet mehrere Methoden zur Anomalieerkennung: **Harter Schwellenwert, Intelligente Erkennung, Schwellenwert ändern**. Sie können eine dieser Methoden verwenden oder diese mithilfe logischer Operatoren kombinieren, indem Sie auf die Schaltfläche **+** klicken. 

**Hard threshold** (Harter Schwellenwert)

 Der harte Schwellenwert ist eine grundlegende Methode zur Anomalieerkennung. Sie können eine obere und/oder untere Grenze festlegen, um den erwarteten Wertebereich zu bestimmen. Alle Punkte außerhalb der Grenze werden als Anomalien identifiziert. 

**Intelligente Erkennung** 

Die intelligente Erkennung basiert auf maschinellem Lernen, das Muster in Verlaufsdaten erkennt und diese für die spätere Erkennung verwendet. Wenn diese Methode verwendet wird, ist die **Empfindlichkeit** der wichtigste Parameter zum Optimieren der Erkennungsergebnisse. Sie können ihn auf einen kleineren oder größeren Wert ziehen, um die Visualisierung rechts auf der Seite zu beeinflussen. Wählen Sie einen Wert aus, der zu Ihren Daten passt, und speichern Sie ihn. 


Im intelligenten Erkennungsmodus werden die Parameter für Empfindlichkeit und Grenzversion verwendet, um die Ergebnisse der Anomalieerkennung zu optimieren.

Die Empfindlichkeit kann sich auf die Breite des erwarteten Wertebereichs der einzelnen Punkte auswirken. Wenn Sie den Wert erhöhen, wird der erwartete Wertebereich enger, und es werden mehr Anomalien gemeldet:

:::image type="content" source="../media/metrics/smart-detection-high-sensitivity.png" alt-text="Intelligente Erkennung mit hoher Empfindlichkeit":::

Wenn Sie die Empfindlichkeit verringern, wird der erwartete Wertebereich breiter, und es werden weniger Anomalien gemeldet:

:::image type="content" source="../media/metrics/smart-detection-low-sensitivity.png" alt-text="Intelligente Erkennung mit niedriger Empfindlichkeit":::

**Change threshold** (Änderungsschwellenwert) 

Der Änderungsschwellenwert wird normalerweise verwendet, wenn Metrikdaten in der Regel in einem bestimmten Bereich liegen. Der Schwellenwert wird dem **Change percentage** (Änderungsprozentsatz) entsprechend festgelegt. Der Modus **Change threshold** (Änderungsschwellenwert) kann Anomalien in den Szenarien erkennen:

* Ihre Daten sind in der Regel stabil. Sie möchten benachrichtigt werden, wenn Schwankungen auftreten.
* Ihre Daten sind normalerweise recht instabil und schwanken stark. Sie möchten benachrichtigt werden, wenn sie zu stabil werden oder keine Schwankungen mehr auftreten.

Führen Sie die folgenden Schritte aus, um diesen Modus zu verwenden:

1. Wählen Sie **Change threshold** (Änderungsschwellenwert) als Anomalieerkennungsmethode aus, wenn Sie die Konfigurationen der Anomalieerkennung für Ihre Metriken oder Zeitreihen festlegen.
    
    :::image type="content" source="../media/metrics/change-threshold.png" alt-text="Change Threshold (Änderungsschwellenwert)":::

2. Wählen Sie die Parameter **out of the range** (Außerhalb des Bereichs) oder **in the range** (Innerhalb des Bereichs) ausgehend von Ihrem Szenario aus.

    Wenn Sie Schwankungen erkennen möchten, wählen Sie **out of the range** (Außerhalb des Bereichs) aus. Mit den unten angegebenen Einstellungen werden beispielsweise alle Datenpunkte, die sich im Vergleich zur vorherigen Einstellung um mehr als 10 % ändern, als Ausreißer erkannt.
    :::image type="content" source="../media/metrics/out-of-the-range.png" alt-text="Parameter „out of range“ (Außerhalb des Bereichs)":::

    Wenn Sie Daten erkennen möchten, bei denen keine Schwankungen auftreten, wählen Sie **in the range** (Innerhalb des Bereichs) aus. Mit den unten angegebenen Einstellungen werden beispielsweise alle Datenpunkte, die sich im Vergleich zur vorherigen Einstellung um bis zu 0,01 % ändern, als Ausreißer erkannt. Da der Schwellenwert so klein ist (0,01 %), erkennt er fehlende Schwankungen bei den Daten als Ausreißer.

    :::image type="content" source="../media/metrics/in-the-range.png" alt-text="Parameter „In range“ (Innerhalb des Bereichs)":::

3. Legen Sie den Prozentsatz der Änderung fest, der als Anomalie gilt, und welche zuvor erfassten Datenpunkte für den Vergleich verwendet werden. Dieser Vergleich erfolgt immer zwischen dem aktuellen Datenpunkt und einem einzelnen Datenpunkt N Punkte davor.
    
    **Richtung** ist nur gültig, wenn Sie den Modus **out of the range** (Außerhalb des Bereichs) verwenden:
    
    * Mit **Nach oben** wird die Erkennung so konfiguriert, dass Anomalien nur dann erkannt werden, wenn (aktueller Datenpunkt) - (Vergleichsdatenpunkt) > **+** Prozentsatz des Schwellenwerts.
    * Mit **Nach unten** wird die Erkennung so konfiguriert, dass Anomalien nur dann erkannt werden, wenn (aktueller Datenpunkt) - (Vergleichsdatenpunkt) < **-** Prozentsatz des Schwellenwerts.



## <a name="preset-events"></a>Vordefinierte Ereignisse

Manchmal werden durch erwartete oder regelmäßig auftretende Ereignisse (z. B. Feiertage) anormale Daten generiert. Mithilfe von vordefinierten Ereignissen können Sie der Anomalieerkennungsausgabe während festgelegter Zeiten Flags hinzufügen. Konfigurieren Sie diese Funktion nach dem Onboarding Ihres Datenfeeds. Jede Metrik kann über nur eine vordefinierte Ereigniskonfiguration verfügen.

> [!Note]
> Bei der voreingestellten Ereigniskonfiguration werden Feiertage bei der Anomalieerkennung berücksichtigt, und Ihre Ergebnisse werden unter Umständen geändert. Dies wird auf die Datenpunkte angewendet, die nach dem Speichern der Konfiguration erfasst werden. 

Klicken Sie auf jeder Metrikdetailseite neben der Dropdownliste für Metriken auf die Schaltfläche **Configure Preset Event** (Vordefiniertes Ereignis konfigurieren).
 
:::image type="content" source="../media/metrics/preset-event-button.png" alt-text="Schaltfläche „Preset event“ (Vordefiniertes Ereignis)":::

Konfigurieren Sie die Optionen im angezeigten Fenster Ihrer Nutzung entsprechend. Stellen Sie sicher, dass **Enable holiday event** (Feiertagsereignis aktivieren) ausgewählt ist, um die Konfiguration zu verwenden. 

Im Abschnitt **Holiday event** (Feiertagsereignis) können Sie unnötige Anomalien unterdrücken, die während Feiertagen erkannt werden. Es gibt zwei Optionen für die Option **Strategie**, die Sie anwenden können:

* **Suppress holiday** (Feiertag unterdrücken): Unterdrückt alle Anomalien und Warnungen in den Anomalieerkennungsergebnissen während Feiertagen.
* **Holiday as weekend** (Feiertag als Wochenende): Berechnet die durchschnittlichen erwarteten Werte mehrerer entsprechender Wochenenden vor dem Feiertag und basiert den Anomaliestatus auf diesen Werten.

Sie können verschiedene weitere Werte konfigurieren:

|Option  |Beschreibung  |
|---------|---------|
|**Choose one dimension as country** (Eine Dimension als Land auswählen)     | Wählen Sie eine Dimension aus, die Länderinformationen enthält. Dies könnte beispielsweise ein Ländercode sein.         |
|**Country code mapping** (Ländercodezuordnung)     | Die Zuordnung zwischen einem [Standardländercode](https://wikipedia.org/wiki/ISO_3166-1_alpha-2) und den Länderdaten der ausgewählten Dimension.        |
|**Holiday options** (Feiertagsoptionen)    | Gibt an, ob alle freien Tage, nur bezahlte Urlaubstage oder nur unbezahlte freie Tage berücksichtigt werden sollen.         |
|**Days to expand** (Tage zum Erweitern)    |  Die betroffenen Tage vor und nach einem Feiertag.        |


Der Abschnitt **Cycle event** (Zyklisches Ereignis) kann in einigen Szenarien verwendet werden, um unnötige Warnungen mithilfe von zyklischen Mustern in den Daten zu reduzieren. Zum Beispiel: 

- Metriken, die über mehrere Muster oder Zyklen verfügen, wie etwa ein wöchentliches und monatliches Muster 
- Metriken, die über kein klares Muster verfügen, aber über vergleichbare Daten wie „Year over Year“ (YoY, Jahr für Jahr), „Month over Month“ (MoM, Monat für Monat), „Week Over Week“ (WoW, Woche für Woche) oder „Day Over Day“ (DoD, Tag für Tag).
 
Nicht alle Optionen können für jede Granularität ausgewählt werden. Die verfügbaren Optionen pro Granularität sind unten aufgeführt (✔ für verfügbar, X für nicht verfügbar):

| Granularität | YoY | MoM | WoW | DoD |
|:-|:-|:-|:-|:-|
| Jährlich | X | X | X | X |
| Monatlich | X | X | X | X |
| Wöchentlich | ✔ | X | X | X |
| Täglich | ✔ | ✔ | ✔ | X |
| Stündlich | ✔ | ✔ | ✔ | ✔ |
| Minütlich | X | X | X | X |
| Sekündlich | X | X | X | X |
| Benutzerdefiniert* | ✔ | ✔ | ✔ | ✔ |

  
 Wenn eine benutzerdefinierte Granularität in Sekunden verwendet wird, ist dies nur verfügbar, wenn die Metrik länger als eine Stunde und kürzer als ein Tag ist.

Das zyklische Ereignis wird verwendet, um Anomalien zu reduzieren, wenn diese einem zyklischen Muster folgen. Es wird jedoch eine Anomalie gemeldet, wenn mehrere Datenpunkte dem Muster nicht folgen. Der **Strict-Modus** wird zum Aktivieren der Anomalieberichterstattung verwendet, wenn ein Datenpunkt dem Muster nicht folgt. 

:::image type="content" source="../media/metrics/preset-events.png" alt-text="Konfiguration für vordefinierte Ereignisse":::

## <a name="view-recent-incidents"></a>Anzeigen der letzten Incidents

Metrics Advisor erkennt Anomalien bei all Ihren Zeitreihendaten, während diese erfasst werden. Allerdings müssen nicht alle Anomalien eskaliert werden, da sie möglicherweise keine großen Auswirkungen haben. Die Aggregation wird für Anomalien durchgeführt, um zusammengehörige Anomalien in Incidents zu gruppieren. Sie können diese Incidents auf der Registerkarte **Incident** auf der Metrikdetailseite anzeigen. 

Klicken Sie auf einen Incident, um zur Seite **Incidents analysis** (Incidentanalyse) zu gelangen, auf der Sie weitere Details finden. Klicken Sie auf **Manage incidents in new Incident hub** (Incidents in neuem Incidenthub verwalten), um die Seite [Incident hub](diagnose-an-incident.md) (Incidenthub) zu finden, auf der Sie alle Vorfälle unter der jeweiligen Metrik sehen. 

## <a name="subscribe-anomalies-for-notification"></a>Abonnieren von Anomalien für Benachrichtigungen

Wenn Sie benachrichtigt werden möchten, wenn eine Anomalie erkannt wird, können Sie Warnungen für die Metrik mithilfe eines Hooks abonnieren. Unter [Konfigurieren von Warnungen und Erhalten von Benachrichtigungen per Hook](alerts.md) finden Sie weitere Informationen.


## <a name="next-steps"></a>Nächste Schritte 
- [Konfigurieren von Warnungen und Erhalten von Benachrichtigungen per Hook](alerts.md)
- [Anpassen der Anomalieerkennung anhand von Feedback](anomaly-feedback.md)
- [Diagnostizieren eines Incidents](diagnose-an-incident.md)

