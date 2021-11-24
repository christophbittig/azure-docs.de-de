---
title: Erhalten Sie Optimierungsempfehlungen für Ihre Analyseregeln in Microsoft Sentinel
description: Erfahren Sie, wie Sie Ihre Regeln zur Bedrohungserkennung in Microsoft Sentinel mithilfe automatisch generierter Empfehlungen optimieren, um Fehlalarme zu reduzieren und gleichzeitig die Abdeckung der Bedrohungserkennung beizubehalten.
author: yelevin
ms.author: yelevin
ms.service: microsoft-sentinel
ms.topic: how-to
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1b1d71f2c8a6a5f74d68d9480b918743a707cc83
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521508"
---
# <a name="get-fine-tuning-recommendations-for-your-analytics-rules-in-microsoft-sentinel"></a>Erhalten Sie Optimierungsempfehlungen für Ihre Analyseregeln in Microsoft Sentinel

> [!IMPORTANT]
>
> Die Erkennungsoptimierung befindet sich derzeit in der **VORSCHAU**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

Die Feinabstimmung von Regeln für die Bedrohungserkennung in Ihrem SIEM kann ein schwieriger, langwieriger und kontinuierlicher Prozess sein, bei dem ein Ausgleich zwischen der Maximierung Ihrer Bedrohungserkennung und der Minimierung der Fehlalarmrate erfolgt. Microsoft Sentinel vereinfacht und optimiert diesen Prozess mithilfe von Maschinen-Lernen, um Milliarden von Signalen aus Ihren Datenquellen sowie Ihre Reaktionen auf Vorfälle im Laufe der Zeit zu analysieren, Muster ableiten und Ihnen umsetzbare Empfehlungen und Erkenntnisse bereitzustellen, die Ihren Optimierungsaufwand erheblich verringern und ihnen ermöglichen, sich auf das Erkennen und Reagieren auf tatsächliche Bedrohungen zu konzentrieren.

Optimierungsempfehlungen und Erkenntnisse sind jetzt in Ihre Analyseregeln integriert. In diesem Artikel wird erläutert, was diese Erkenntnisse zeigen und wie Sie die Empfehlungen implementieren können.

## <a name="view-rule-insights-and-tuning-recommendations"></a>Anzeigen von Regeleinblicken und Optimierungsempfehlungen

Um festzustellen, ob Microsoft Sentinel Optimierungsempfehlungen für ihre Analyseregeln hat, wählen Sie im Navigationsmenü von Microsoft Sentinel **Analytics** aus.

Alle Regeln mit Empfehlungen zeigen ein Glühbirnensymbol an, wie hier gezeigt:

:::image type="content" source="media/detection-tuning/rule-list-with-insight.png" alt-text="Screenshot: Liste der Analyseregeln mit Empfehlungsindikator." lightbox="media/detection-tuning/rule-list-with-insight.png":::

Bearbeiten Sie die Regel, um die Empfehlungen zusammen mit den anderen Erkenntnissen anzuzeigen. Sie werden zusammen auf der Registerkarte **Regellogik festlegen** des Assistenten für Analyseregel unterhalb der Anzeige **Ergebnissimulation** angezeigt.

:::image type="content" source="media/detection-tuning/tuning-insights.png" alt-text="Screenshot der Optimierung von Erkenntnissen in der Analyseregel." lightbox="media/detection-tuning/tuning-insights.png":::

### <a name="types-of-insights"></a>Arten von Erkenntnissen

Die Ansicht **Optimierungserkenntnisse** besteht aus mehreren Bereichen, in denen Sie scrollen oder wischen können. Jeder Bereich zeigt Ihnen etwas anderes an. Der Zeitraum – 14 Tage – für den die Erkenntnisse angezeigt werden, findet sich oben im Rahmen.

1. Im ersten Erkenntnisbereich werden einige statistische Informationen angezeigt: die durchschnittliche Anzahl von Warnungen pro Vorfall, die Anzahl der offenen Vorfälle und die Anzahl geschlossener Vorfälle, gruppiert nach Klassifizierung (wirkliche Bedrohung/Fehlalarm). Diese Erkenntnis hilft Ihnen dabei, die Last für diese Regel zu erkennen und zu verstehen, ob eine Optimierung erforderlich ist, z. B. ob die Gruppierungseinstellungen angepasst werden sollten.

    :::image type="content" source="media/detection-tuning/rule-efficiency.png" alt-text="Screenshot: Einblicke in die Regeleffizienz.":::

    Diese Erkenntnis ist das Ergebnis einer Log Analytics-Abfrage. Wenn Sie **Durchschnittliche Warnungen pro Vorfall** auswählen, gelangen Sie zur Abfrage in Log Analytics, welche diese Erkenntnis erzeugt hat. Wenn Sie **Vorfälle Öffnen** auswählen, gelangen Sie zum Blatt **Vorfälle**.

1. Im zweiten Erkenntnisbereich wird empfohlen, eine Liste der auszuschließenden [Einheiten](entities-in-azure-sentinel.md) zu erstellen. Diese Einheiten sind in hohem Maße mit Vorfällen korreliert, die Sie geschlossen und als **Fehlalarm** klassifiziert haben. Wählen Sie das Pluszeichen neben jeder aufgelisteten Einheit aus, um sie bei zukünftigen Ausführungen dieser Regel aus der Abfrage auszuschließen. 

    :::image type="content" source="media/detection-tuning/entity-exclusion.png" alt-text="Screenshot der Empfehlung zum Ausschluss von Einheiten":::.

    Diese Empfehlung wird von den erweiterten Data Science- und Maschinen-Lernen-Modellen von Microsoft erstellt. Die Einbeziehung dieses Bereichs in die Anzeige **Optimierungserkenntnisse** hängt davon ab, ob irgendwelche Empfehlungen anzuzeigen sind.

1. Im dritten Erkenntnisbereich werden die vier am häufigsten angezeigten zugeordneten Einheiten für alle Warnungen angezeigt, die von dieser Regel erzeugt werden. Die Zuordnung der Einheiten muss für die Regel konfiguriert werden, damit diese Erkenntnis Ergebnisse liefert. Diese Erkenntnis kann Ihnen dabei helfen, sich aller Einheiten bewusst zu werden, die "das Rampenlicht für sich beanspruchen"" und die Aufmerksamkeit von anderen weg lenken. Möglicherweise möchten Sie diese Einheiten separat in einer anderen Regel behandeln, oder Sie entscheiden, dass es sich um Fehlalarm oder anderweitige Irritation handelt, und sie von der Regel ausschließen.

    :::image type="content" source="media/detection-tuning/top-entities.png" alt-text="Screenshot: Einblicke in die wichtigsten Einheiten.":::

    Diese Erkenntnis ist das Ergebnis einer Log Analytics-Abfrage. Wenn Sie eine der Einheiten auswählen, gelangen Sie zur Abfrage in Log Analytics, die den Einblick erzeugt hat.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter
- [Behandeln falsch positiver Ergebnisse in Microsoft Sentinel](false-positives.md)
- [Verwenden von UEBA-Daten zum Analysieren falsch positiver Ergebnisse](investigate-with-ueba.md#use-ueba-data-to-analyze-false-positives)
- [Erstellen benutzerdefinierter Analyseregeln zum Erkennen von Bedrohungen](detect-threats-custom.md)
