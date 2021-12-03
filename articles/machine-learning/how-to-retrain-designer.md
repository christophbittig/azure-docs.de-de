---
title: Verwenden von Pipelineparametern zum erneuten Trainieren von Modellen im Designer
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie Modelle mit veröffentlichten Pipelines und Pipelineparametern im Azure Machine Learning-Designer erneut trainieren.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.author: keli19
author: likebupt
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: designer
ms.openlocfilehash: 6b79b80f666019cb5a8a59901bf12e9277287a12
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557412"
---
# <a name="use-pipeline-parameters-to-retrain-models-in-the-designer"></a>Verwenden von Pipelineparametern zum erneuten Trainieren von Modellen im Designer


In dieser Anleitung erfahren Sie, wie Sie Azure Machine Learning-Designer zum erneuten Trainieren eines Machine Learning-Modells mithilfe von Pipelineparametern verwenden. Sie werden veröffentlichte Pipelines verwenden, um Ihren Workflow zu automatisieren und Parameter festzulegen, um Ihr Modell anhand neuer Daten zu trainieren. Mithilfe von Pipelineparametern können Sie vorhandene Pipelines für verschiedene Aufträge wiederverwenden.  

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Trainieren eines Machine Learning-Modells
> * Erstellen eines Pipelineparameters
> * Veröffentlichen Ihrer Trainingspipeline
> * Trainieren Sie Ihr Modell mit neuen Parametern erneut.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure Machine Learning-Arbeitsbereich
* Schließen Sie den ersten Teil dieser Anleitungsreihe [Transformieren von Daten im Designer](how-to-designer-transform-data.md) ab.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

In diesem Artikel wird auch davon ausgegangen, dass Sie Kenntnisse zur Erstellung einer einfachen Pipeline im Designer haben. Als Einführung können Sie das [Tutorial](tutorial-designer-automobile-price-train-score.md) durchführen. 

### <a name="sample-pipeline"></a>Beispiel-Pipeline

Die in diesem Artikel verwendete Pipeline ist eine geänderte Version einer [Einkommensvorhersage](samples-designer.md#classification) einer Beispielpipeline auf der Designer-Homepage. Die Pipeline verwendet die Komponente [Daten importieren](algorithm-module-reference/import-data.md) anstelle des Beispieldatasets, um Ihnen zu zeigen, wie Sie Modelle mit Ihren eigenen Daten trainieren können.

![Screenshot der geänderten Beispielpipeline mit einem Feld, in dem die Komponente „Daten importieren“ hervorgehoben ist](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="create-a-pipeline-parameter"></a>Erstellen eines Pipelineparameters

Pipelineparameter dienen zum Erstellen vielseitiger Pipelines, die später mit variierenden Parameterwerten erneut übermittelt werden können. Einige typische Szenarien sind die Aktualisierung von Datasets oder einiger Hyperparameter für das erneute Training. Erstellen Sie Pipelineparameter, um Variablen zur Laufzeit dynamisch festzulegen. 

Pipelineparameter können in einer Pipeline Datenquellen- oder Komponentenparametern hinzugefügt werden. Wenn die Pipeline erneut übermittelt wird, können die Werte dieser Parameter angegeben werden.

In diesem Beispiel werden Sie den Trainingsdatenpfad von einem festen Wert in einen Parameter ändern, sodass Sie Ihr Modell anhand anderer Daten neu trainieren können. Sie können auch je nach Anwendungsfall weitere Komponentenparameter als Pipelineparameter hinzufügen.

1. Wählen Sie das Modul **Daten importieren** aus.

    > [!NOTE]
    > In diesem Beispiel wird die Komponente „Daten importieren“ verwendet, um auf Daten in einem registrierten Datenspeicher zuzugreifen. Sie können jedoch ähnliche Schritte durchführen, wenn Sie alternative Datenzugriffsmuster verwenden.

1. Wählen Sie im Komponentendetailbereich rechts neben der Canvas Ihre Datenquelle aus.

1. Geben Sie den Pfad zu Ihren Daten ein. Sie können auch **Pfad durchsuchen** auswählen, um durch Ihre Dateistruktur zu navigieren. 

1. Bewegen Sie den Mauszeiger über das Feld **Pfad**, und wählen Sie die Auslassungspunkte über dem Feld **Pfad** aus, die angezeigt werden.

1. Wählen Sie **Zu Pipelineparameter hinzufügen** aus.

1. Geben Sie einen Parameternamen und einen Standardwert an.

   ![Screenshot, der zeigt, wie ein Pipelineparameter erstellt wird](media/how-to-retrain-designer/add-pipeline-parameter.png)

1. Wählen Sie **Speichern** aus.

   > [!NOTE]
   > Sie können auch im Bereich mit den Komponentendetails (vergleichbar mit dem Hinzufügen von Pipelineparametern) einen Komponentenparameter von einem Pipelineparameter trennen.
   >
   > Über das Zahnradsymbol **Einstellungen** neben dem Titel Ihres Pipelineentwurfs können Sie die Pipelineparameter überprüfen und bearbeiten. 
   >    - Nach dem Trennen können Sie den Pipelineparameter im Bereich **Einstellungen** löschen.
   >    - Sie können auch einen Pipelineparameter im Bereich **Einstellungen** hinzufügen und ihn dann auf einen Komponentenparameter anwenden.

1. Übermitteln Sie die Pipelineausführung.

## <a name="publish-a-training-pipeline"></a>Veröffentlichen einer Trainingspipeline

Veröffentlichen Sie eine Pipeline an einem Pipelineendpunkt, um Ihre Pipelines in Zukunft einfach wiederverwenden zu können. Ein Pipelineendpunkt erstellt einen REST-Endpunkt, um die Pipeline zukünftig aufzurufen. In diesem Beispiel können Sie mit Ihrem Pipelineendpunkt Ihre Pipeline wiederverwenden, um ein Modell anhand anderer Daten neu zu trainieren.

1. Wählen Sie **Veröffentlichen** über der Designer-Canvas aus.
1. Wählen Sie einen Pipelineendpunkt aus, oder erstellen Sie ihn.

   > [!NOTE]
   > Sie können mehrere Pipelines für einen einzelnen Endpunkt veröffentlichen. Jede Pipeline in einem bestimmten Endpunkt erhält eine Versionsnummer, die Sie beim Aufruf des Pipelineendpunkts angeben können.

1. Wählen Sie **Veröffentlichen**.

## <a name="retrain-your-model"></a>Erneutes Trainieren Ihres Modells

Nachdem Sie nun eine Trainingspipeline veröffentlicht haben, können Sie diese verwenden, um Ihr Modell anhand neuer Daten neu zu trainieren. Sie können Ausführungen von einem Pipelineendpunkt aus dem Studio-Arbeitsbereich oder programmgesteuert übermitteln.

### <a name="submit-runs-by-using-the-studio-portal"></a>Übermitteln von Ausführungen mit dem Studio-Portal

Verwenden Sie die folgenden Schritte, um die Ausführung eines parametrisierten Pipelineendpunkts über das Studio-Portal zu übermitteln:

1. Rufen Sie die Seite **Endpunkte** in Ihrem Studio-Arbeitsbereich auf.
1. Wählen Sie die Registerkarte **Pipelineendpunkte** aus. Wählen Sie dann Ihren Pipelineendpunkt aus.
1. Wählen Sie die Registerkarte **Veröffentlichte Pipelines** aus. Wählen Sie dann die Pipelineversion aus, die Sie ausführen möchten.
1. Klicken Sie auf **Submit** (Senden).
1. Im Dialogfeld für die Einrichtung können Sie die Parameterwerte für die Ausführung angeben. Aktualisieren Sie in diesem Beispiel den Dateipfad, um das Modell mit einem Nicht-US-Dataset zu trainieren.

![Screenshot, der zeigt, wie Sie im Designer eine parametrisierte Pipelineausführung einrichten](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>Übermitteln von Ausführungen durch Code

Den REST-Endpunkt einer veröffentlichten Pipeline finden Sie im Übersichtsbereich. Durch den Aufruf des Endpunkts können Sie die veröffentlichte Pipeline neu trainieren.

Sie benötigen einen OAuth 2.0-Authentifizierungsheader vom Typ Bearer, um einen REST-Aufruf auszuführen. Informationen zum Einrichten der Authentifizierung für Ihren Arbeitsbereich und zum Ausführen eines parametrisierten REST-Aufrufs finden Sie unter [Erstellen einer Azure Machine Learning-Pipeline für die Batchbewertung](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint).

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie mit dem Designer einen parametrisierten Trainingspipelineendpunkt erstellen können.

Eine vollständige exemplarische Vorgehensweise, wie Sie ein Modell bereitstellen können, um Vorhersagen zu treffen, finden Sie im [Designer-Tutorial](tutorial-designer-automobile-price-train-score.md) zum Trainieren und Bereitstellen eines Regressionsmodells.

Informationen zum Veröffentlichen und übermitteln eines Run-to-Pipeline-Endpunkts mithilfe des SDK finden Sie in [diesem Artikel](how-to-deploy-pipelines.md).
