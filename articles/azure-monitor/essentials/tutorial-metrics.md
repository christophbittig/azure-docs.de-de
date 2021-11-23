---
title: 'Tutorial: Analysieren von Metriken für eine Azure-Ressource'
description: Hier erfahren Sie, wie Sie Metriken für eine Azure-Ressource mithilfe des Metrik-Explorers in Azure Monitor analysieren.
author: bwren
ms.author: bwren
ms.topic: tutorial
ms.date: 11/08/2021
ms.openlocfilehash: fc996a635caea212d185b54c5493bf781d098f57
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350866"
---
# <a name="tutorial-analyze-metrics-for-an-azure-resource"></a>Tutorial: Analysieren von Metriken für eine Azure-Ressource
Metriken sind numerische Werte, die automatisch in regelmäßigen Abständen erfasst werden und einen Aspekt einer Ressource beschreiben. Eine Metrik kann Ihnen beispielsweise die Prozessorauslastung eines virtuellen Computers, den freien Speicherplatz in einem Speicherkonto oder den eingehenden Datenverkehr für ein virtuelles Netzwerk mitteilen. Mit dem Metrik-Explorer – einem Azure Monitor-Feature im Azure-Portal – können Sie auf der Grundlage von Metrikwerten Diagramme erstellen, Trends visuell korrelieren sowie Spitzen und Einbrüche bei Metrikwerten untersuchen. Verwenden Sie den Metrik-Explorer, um Diagramme auf der Grundlage von Metriken zu zeichnen, die durch Ihre Azure-Ressourcen erstellt werden, und um ihre Integrität und Auslastung zu untersuchen. 

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Öffnen des Metrik-Explorers für eine Azure-Ressource
> * Auswählen einer Metrik, die in einem Diagramm dargestellt werden soll
> * Durchführen verschiedener Aggregationen von Metrikwerten
> * Ändern des Zeitbereichs und der Granularität für das Diagramm


Im folgenden Video wird ein ausführlicheres Szenario als das in diesem Artikel beschriebene Verfahren gezeigt. Wenn Sie mit Metriken noch nicht vertraut sind, sollten Sie zuerst diesen Artikel lesen und sich dann das Video mit ausführlicheren Informationen ansehen. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4qO59]

## <a name="prerequisites"></a>Voraussetzungen
Für dieses Tutorial benötigen Sie Folgendes: 

- Eine zu überwachende Azure-Ressource. Sie können eine beliebige Ressource in Ihrem Azure-Abonnement verwenden, die Metriken unterstützt. Ob eine Ressource Metriken unterstützt, kann wie folgt überprüft werden: Navigieren Sie im Azure-Portal zum Menü der Ressource, und vergewissern Sie sich, dass im Abschnitt **Überwachung** des Menüs eine Option vom Typ **Metrik** vorhanden ist.


## <a name="open-metrics-explorer"></a>Öffnen des Metrik-Explorers 
Wählen Sie im Menü Ihrer Ressource im Abschnitt **Überwachung** die Option **Metriken** aus. Der Bereich ist bereits mit Ihrer Ressource aufgefüllt. Das folgende Beispiel gilt für ein Speicherkonto, es sieht für andere Azure-Dienste aber ähnlich aus.

:::image type="content" source="media/tutorial-metrics/metrics-menu.png" lightbox="media/tutorial-metrics/metrics-menu.png" alt-text="Metrikmenüelement":::

Wählen Sie unter **Namespace** einen Namespace aus, falls der Bereich mehrere Namespaces umfasst. Der Namespace dient lediglich der Organisation der Metriken, damit Sie sich mühelos finden können. Speicherkonten verfügen beispielsweise über separate Namespaces zum Speichern von Metriken zu Dateien, Tabellen, Blobs und Warteschlangen. Viele Ressourcentypen verfügen über nur einen Namespace.

Wählen Sie für den ausgewählten Bereich und Namespace aus einer Liste mit verfügbaren Metriken eine Metrik aus.

:::image type="content" source="media/tutorial-metrics/metric-picker.png" lightbox="media/tutorial-metrics/metric-picker.png" alt-text="Auswählen von Namespace und Metrik":::

Ändern Sie optional unter **Aggregation** die Metrikaggregation. Dadurch wird definiert, wie die Metrikwerte im Rahmen der Zeitgranularität für das Diagramm aggregiert werden. Wenn die Zeitgranularität also beispielsweise auf 15 Minuten und die Aggregation auf „Summe“ festgelegt ist, ist jeder Punkt im Diagramm die Summe aller gesammelten Werte innerhalb des jeweiligen 15-minütigen Segments.

:::image type="content" source="media/tutorial-metrics/chart.png" lightbox="media/tutorial-metrics/chart.png" alt-text="Screenshot: Diagramm mit dem Titel „Sum Ingress for contosoretailweb“ (Eingangssumme für contosoretailweb)":::


Klicken Sie auf **Metrik hinzufügen**, und wiederholen Sie diese Schritte, wenn mehrere Metriken im gleichen Diagramm angezeigt werden sollen. Wählen Sie die Schaltfläche **Neues Diagramm** aus, wenn Sie mehrere Diagramme in einer einzelnen Ansicht erhalten möchten.

## <a name="select-a-time-range-and-granularity"></a>Auswählen von Zeitbereich und -granularität

Das Diagramm zeigt standardmäßig die Metrikdaten der letzten 24 Stunden an. Verwenden Sie die Zeitauswahl, um den **Zeitbereich** für das Diagramm oder die **Zeitgranularität** zu ändern, die den Zeitbereich für jeden Datenpunkt definiert. Das Diagramm verwendet die angegebene Aggregation, um alle als Stichprobe entnommenen Werte innerhalb der angegebenen Zeitgranularität zu berechnen.

:::image type="content" source="media/tutorial-metrics/time-picker.png" lightbox="media/tutorial-metrics/time-picker.png" alt-text="Bereich zum Ändern des Zeitraums":::

Verwenden Sie **Time Brush**, um einen interessanten Bereich des Diagramms (beispielsweise eine Spitze oder einen Einbruch) zu untersuchen. Platzieren Sie den Mauszeiger am Anfang des Bereichs, halten Sie die linke Maustaste gedrückt, ziehen Sie den Mauszeiger zur anderen Seite des Bereichs, und lassen Sie die Maustaste wieder los. Im Diagramm wird dieser Zeitbereich vergrößert. 

:::image type="content" source="media/tutorial-metrics/time-brush.png" lightbox="media/tutorial-metrics/time-brush.png" alt-text="Time Brush":::

## <a name="apply-dimension-filters-and-splitting"></a>Anwenden von Dimensionsfiltern und Aufteilung
Die folgenden Referenzen enthalten Informationen zu erweiterten Features, mit denen Sie Ihre Metriken weiter analysieren und potenzielle Ausreißer in Ihren Daten ermitteln können.

- Durch das [Filtern](../essentials/metrics-charts.md#filters) können Sie entscheiden, welche Dimensionswerte im Diagramm enthalten sind. So können Sie beispielsweise erreichen, dass in einem Diagramm für eine Metrik vom Typ *Serverantwortzeit* nur erfolgreiche Anforderungen angezeigt werden. 

- Durch das [Aufteilen](../essentials/metrics-charts.md#apply-splitting) können Sie steuern, ob das Diagramm separate Linien für jeden Wert einer Dimension anzeigt oder die Werte in eine Linie zusammenfügt. So können Sie beispielsweise eine einzelne Linie für die durchschnittliche Antwortzeit aller Serverinstanzen oder separate Linien für jeden Server anzeigen. 

In den [Beispielen für Metrikdiagramme](../essentials/metric-chart-samples.md) können Sie sich Beispiele mit angewandter Filterung und Aufteilung ansehen.

## <a name="advanced-chart-settings"></a>Erweiterte Diagrammeinstellungen

Sie können die Formatierung des Diagramms, den Titel und erweiterte Diagrammeinstellungen anpassen. Wenn Sie Ihre Anpassungen vorgenommen haben, heften Sie das Diagramm an ein Dashboard an, um Ihr Werk zu speichern. Sie können auch Metrikwarnungen konfigurieren. Unter [Erweiterte Funktionen von Azure Metrik-Explorer](../essentials/metrics-charts.md#locking-the-range-of-the-y-axis) finden Sie Informationen zu diesen und anderen erweiterten Features des Azure Monitor-Metrik-Explorers.


## <a name="next-steps"></a>Nächste Schritte
Hier wurde erläutert, wie Sie mit Metriken in Azure Monitor arbeiten. Erfahren Sie nun, wie Sie eine Metrikwarnungsregel erstellen, um benachrichtigt zu werden, wenn ein Metrikwert auf ein potenzielles Problem hinweist.

> [!div class="nextstepaction"]
> [Erstellen einer Metrikwarnung in Azure Monitor](../alerts/tutorial-metric-alert.md)

