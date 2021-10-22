---
title: Analysieren der Benutzerbindung für Web-Apps mit Application Insights
description: Wie viele Benutzer kehren zu Ihrer App zurück?
ms.topic: conceptual
ms.date: 07/30/2021
ms.openlocfilehash: e99467f43f3a5ce1e9dd300918414a9968bd2045
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130131902"
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Benutzerbindungsanalyse für Webanwendungen mit Azure Application Insights

Mithilfe des Bindungsfeatures in [Application Insights](./app-insights-overview.md) können Sie analysieren, wie viele Benutzer zu Ihrer App zurückkehren und wie oft sie bestimmte Aufgaben durchführen oder Ziele erreichen. Beispiel: Wenn Sie eine Spielewebsite betreiben, können Sie die Anzahl der Benutzer, die nach einem verlorenen Spiel zurückkehren, mit der Anzahl vergleichen, die nach einem gewonnenen Spiel zurückkehren. Mit diesem Wissen können Sie sowohl die Benutzererfahrung als auch Ihre Geschäftsstrategie verbessern.

## <a name="get-started"></a>Erste Schritte

Wenn im Vermerkdauer-Tool des Application Insights-Portals noch keine Daten zu sehen sind, informieren Sie sich [hier](usage-overview.md) über die ersten Schritte mit den Nutzungstools.

## <a name="the-retention-workbook"></a>Die Arbeitsmappe „Bindung“

Um die Arbeitsmappe „Bindung“ zu verwenden, navigieren Sie in Ihren Application Insights-Ressourcen zu **Verwendung** > **Bindung**, und wählen Sie **Bindungsanalyse-Arbeitsmappe** aus. Oder wählen Sie auf der Registerkarte **Arbeitsmappen** die Option **Öffentliche Vorlagen** und dann unter *Verwendung* die Option **Benutzerbindungsanalyse** aus.

:::image type="content" source="./media/usage-retention/workbooks-gallery.png" alt-text="Screenshot des Arbeitsmappenkatalogs zu öffentlichen Vorlagen." lightbox="./media/usage-retention/workbooks-gallery.png":::



### <a name="using-the-workbook"></a>Verwenden der Arbeitsmappe

:::image type="content" source="./media/usage-retention/retention.png" alt-text="Screenshot der „Bindung“-Arbeitsmappe mit einem Liniendiagramm." lightbox="./media/usage-retention/retention.png":::

- Standardmäßig werden im Bericht zur Vermerkdauer alle Benutzer aufgeführt, die einen bestimmten Vorgang durchgeführt haben, zu dem Tool zurückgekehrt sind und dann einen anderen Vorgang durchgeführt haben. Sie können Ereignisse in verschiedenen Kombinationen auswählen, um den Schwerpunkt auf bestimmte Benutzeraktivitäten zu setzen.
- Fügen Sie einen oder mehrere Filter für Eigenschaften hinzu, indem Sie **Filter hinzufügen** auswählen. Beispielsweise können Sie sich auf Benutzer in einem bestimmten Land oder einer bestimmten Region konzentrieren. 
- Das Diagramm mit der gesamten Vermerkdauer zeigt eine Zusammenfassung der Vermerkdauer für die Benutzer im ausgewählten Zeitraum an. 
- Das Raster zeigt die Anzahl der gebundenen Benutzer an. Jede Zeile stellt eine Kohorte von Benutzern dar, die ein Ereignis während des angezeigten Zeitraums ausgeführt haben. Jede Zelle in der Zeile zeigt an, wie viele Benutzer dieser Kohorte mindestens einmal in einem späteren Zeitraum zurückgekehrt sind. Einige Benutzer kehren möglicherweise in mehreren Zeiträumen zurück. 
- Die Application Insights-Karten zeigen die fünf wichtigsten Initiierungsereignisse sowie die fünf wichtigsten zurückgegebenen Ereignisse an, um Benutzern einen besseren Überblick über ihren Bericht zur Vermerkdauer zu verschaffen. 

    :::image type="content" source="./media/usage-retention/retention-2.png" alt-text="Screenshot der „Bindung“-Arbeitsmappe, die ein Diagramm der Rückkehr von Benutzern nach Anzahl von Wochen zeigt." lightbox="./media/usage-retention/retention-2.png":::

## <a name="use-business-events-to-track-retention"></a>Nachverfolgen der Vermerkdauer mithilfe von Geschäftsereignissen

Um die nützlichste Vermerkdaueranalyse zu erhalten, messen Sie Ereignisse, die wichtige Geschäftsaktivitäten darstellen. 

Beispielsweise können viele Benutzer eine Seite in Ihrer App öffnen, ohne das darauf angezeigte Spiel zu spielen. Die Nachverfolgung der Seitenansichten an sich würde daher eine ungenaue Schätzung der Anzahl der Personen ergeben, die zurückkehren, um das Spiel erneut zu spielen, weil es ihnen gefallen hat. Um ein klares Bild der zurückkehrenden Spieler zu erhalten, sollte Ihre App ein benutzerdefiniertes Ereignis senden, wenn ein Benutzer tatsächlich spielt.  

Es empfiehlt sich, benutzerdefinierte Ereignisse zu codieren, die wichtige geschäftliche Aktionen darstellen, und diese für die Vermerkdaueranalyse zu verwenden. Zum Erfassen des Spielergebnisses müssen Sie eine Codezeile zum Senden eines benutzerdefinierten Ereignisses an Application Insights schreiben. Wenn Sie diese im Webseitencode oder in Node.JS schreiben, sieht sie wie folgt aus:

```JavaScript
    appinsights.trackEvent("won game");
```

Oder im ASP.NET-Servercode:

```csharp
   telemetry.TrackEvent("won game");
```

[Erfahren Sie mehr über das Schreiben von benutzerdefinierten Ereignissen](./api-custom-events-metrics.md#trackevent).


## <a name="next-steps"></a>Nächste Schritte
- - Weitere Informationen zu Arbeitsmappen finden Sie in der [Übersicht über Arbeitsmappen](../visualize/workbooks-overview.md).
- Um mit der Nutzung zu beginnen, senden Sie [benutzerdefinierte Ereignisse](./api-custom-events-metrics.md#trackevent) oder [Seitenansichten](./api-custom-events-metrics.md#page-views).
- Wenn Sie bereits benutzerdefinierte Ereignisse oder Seitenansichten senden, finden Sie mithilfe der Nutzungstools heraus, wie Benutzer den Dienst verwenden.
    - [Benutzer, Sitzungen, Ereignisse](usage-segmentation.md)
    - [Trichter](usage-funnels.md)
    - [Benutzerabläufe](usage-flows.md)
    - [Arbeitsmappen](../visualize/workbooks-overview.md)
    - [Hinzufügen von Benutzerkontext](./usage-overview.md)