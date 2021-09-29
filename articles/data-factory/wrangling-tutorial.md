---
title: Erste Schritte mit Wranglingdatenflüssen in Azure Data Factory
description: Tutorial zum Vorbereiten von Daten in Azure Data Factory mithilfe von Wranglingdatenflüssen
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 06/08/2021
ms.openlocfilehash: 1df90b5707c741303b3c3edcd4033b92e0f8f242
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124732958"
---
# <a name="prepare-data-with-data-wrangling"></a>Vorbereiten von Daten mit Datenwrangling

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Mittels Datenwrangling in Data Factory können Sie interaktive Power Query-Mash-ups nativ in ADF erstellen und dann im großen Stil in einer ADF-Pipeline ausführen.

> [!NOTE]
> Power Query-Aktivität in ADF ist derzeit in der öffentlichen Vorschau verfügbar

## <a name="create-a-power-query-activity"></a>Erstellen einer Power Query-Aktivität

Eine Power Query kann in Azure Data Factory auf zwei Arten erstellt werden. Bei der einen Methode klicken Sie auf das Plussymbol und wählen im Bereich mit den Factory-Ressourcen die Option **Datenfluss** aus.

> [!NOTE]
> Zuvor befand sich das Datenwranglingfeature im Datenflussworkflow. Nun erstellen Sie Ihr Datenwrangling-Mash-Up aus ```New > Power query```.

:::image type="content" source="media/data-flow/power-query-wrangling.png" alt-text="Screenshot: Power Query im Bereich „Factoryressourcen“.":::

Die andere Möglichkeit befindet sich im Bereich „Aktivitäten“ der Pipelinecanvas. Öffnen Sie das Accordion-Element **Power Query**, und ziehen Sie die **Power Query**-Aktivität auf die Canvas.

:::image type="content" source="media/data-flow/power-query-activity.png" alt-text="Screenshot: Hervorhebung der Datenwranglingoption.":::

## <a name="author-a-power-query-data-wrangling-activity"></a>Erstellen einer Power Query-Datenwranglingaktivität

Fügen Sie ein **Quelldataset** für Ihr Power Query-Mash-Up hinzu. Wählen Sie entweder ein vorhandenes Dataset aus, oder erstellen Sie ein neues. Nachdem Sie Ihr Mashup gespeichert haben, können Sie die Power Query-Datenwranglingaktivität zu Ihrer Pipeline hinzufügen und ein Senkendataset auswählen, um ADF mitzuteilen, wohin Ihre Daten gelangen sollen. Sie können zwar ein oder mehrere Quelldatasets auswählen, doch ist derzeit nur eine Senke zulässig. Die Auswahl eines Senkendatasets ist optional, aber es ist mindestens ein Quelldataset erforderlich.

:::image type="content" source="media/wrangling-data-flow/tutorial4.png" alt-text="Wrangling":::

Klicken Sie auf **Erstellen**, um den Power Query Online-Mashup-Editor zu öffnen.

Zunächst wählen Sie eine Datasetquelle für den Mashup-Editor aus.

:::image type="content" source="media/wrangling-data-flow/pq-new-source.png" alt-text="Power Query-Quelle.":::

Nachdem Sie Ihre Power Query erstellt haben, können Sie sie speichern und das Mashup als Aktivität zu Ihrer Pipeline hinzufügen. Dann legen Sie die Eigenschaften des Senkendatasets fest.

:::image type="content" source="media/wrangling-data-flow/pq-new-sink.png" alt-text="Power Query-Senke.":::

Erstellen Sie Ihre Wrangling-Power Query mithilfe der codefreien Datenvorbereitung. Die Liste der verfügbaren Funktionen finden Sie unter [Transformationsfunktionen](wrangling-functions.md). ADF übersetzt das M-Skript in ein Datenflussskript, sodass Sie Ihre Power Query im großen Stil mithilfe der Azure Data Factory-Datenfluss-Spark-Umgebung ausführen können.

:::image type="content" source="media/wrangling-data-flow/tutorial6.png" alt-text="Screenshot: Prozess zum Erstellen Ihrer Datenwrangling-Power Query.":::

## <a name="running-and-monitoring-a-power-query-data-wrangling-activity"></a>Ausführen und Überwachen einer Power Query-Datenwranglingaktivität

Um eine Ausführung einer Power Query-Aktivität zum Debuggen einer Pipeline zu starten, klicken Sie in der Pipelinecanvas auf **Debuggen**. Nachdem Sie Ihre Pipeline veröffentlicht haben, können Sie mit **Jetzt auslösen** eine bedarfsgesteuerte Ausführung der letzten veröffentlichten Pipeline durchführen. Power Query-Pipelines können mit allen vorhandenen Azure Data Factory-Triggern geplant werden.

:::image type="content" source="media/data-flow/pq-activity-001.png" alt-text="Screenshot: Hinzufügen einer Power Query-Datenwranglingaktivität.":::

Wechseln Sie zur Registerkarte **Überwachen**, um die Ausgabe einer ausgelösten Power Query-Aktivität zu visualisieren.

:::image type="content" source="media/wrangling-data-flow/tutorial2.png" alt-text="Screenshot: Ausgabe einer ausgelösten Wrangling-Power Query-Aktivitätsausführung.":::

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [einen Zuordnungsdatenfluss erstellen](tutorial-data-flow.md).
