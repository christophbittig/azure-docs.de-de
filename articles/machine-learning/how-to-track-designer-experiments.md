---
title: Protokollmetriken im Designer
titleSuffix: Azure Machine Learning
description: Überwachen Sie Ihre Azure Machine Learning-Designerexperimente. Aktivieren Sie die Protokollierung mit der Komponente Python-Skript ausführen und sehen Sie sich die protokollierten Ergebnisse im Studio an.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: designer
ms.openlocfilehash: ca78f86734c29e0aa6104e43ae759b666df6d290
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560737"
---
# <a name="enable-logging-in-azure-machine-learning-designer-pipelines"></a>Aktivieren der Protokollierung in Azure Machine Learning-Designerpipelines


In diesem Artikel erfahren Sie, wie Sie Protokollierungscode zu Designerpipelines hinzufügen. Außerdem wird erläutert, wie Sie diese Protokolle mit dem Azure Machine Learning Studio-Webportal anzeigen.

Weitere Informationen zum Protokollieren von Metriken mithilfe der SDK-Erstellung finden Sie unter [Überwachen von Azure ML-Experimentausführungen und -metriken](how-to-log-view-metrics.md).

## <a name="enable-logging-with-execute-python-script"></a>Aktivieren der Protokollierung mit „Execute Python Script“ (Ausführen eines Python-Skripts)

Verwenden Sie die Komponente [Pythonskript ausführen](./algorithm-module-reference/execute-python-script.md), um die Protokollierung in Designer-Pipelines zu aktivieren. Obwohl Sie mit diesem Workflow jeden Wert protokollieren können, ist es besonders nützlich, Metriken aus der Komponente __Modell auswerten__ zu protokollieren, um die Modellleistung über mehrere Läufe hinweg zu verfolgen.

Das folgende Beispiel zeigt Ihnen, wie Sie den mittleren quadratischen Fehler von zwei trainierten Modellen mit Hilfe der Komponenten Modell auswerten und Python-Skript ausführen protokollieren können.

1. Verbinden Sie eine Komponente __Pythonskript ausführen__ mit der Ausgabe der Komponente __Modell auswerten__.

    ![Verbinden Sie die Komponente Execute Python Script mit der Komponente Evaluate Model](./media/how-to-log-view-metrics/designer-logging-pipeline.png)

1. Fügen Sie den folgenden Code in den Code-Editor des Moduls __Execute Python Script__ (Ausführen eines Python-Skripts) ein, um die mittlere absolute Abweichung für Ihr trainiertes Modell zu protokollieren. Sie können ein ähnliches Muster verwenden, um einen beliebigen anderen Wert im Designer zu protokollieren:

    ```python
    # dataframe1 contains the values from Evaluate Model
    def azureml_main(dataframe1=None, dataframe2=None):
        print(f'Input pandas.DataFrame #1: {dataframe1}')
    
        from azureml.core import Run
    
        run = Run.get_context()
    
        # Log the mean absolute error to the parent run to see the metric in the run details page.
        # Note: 'run.parent.log()' should not be called multiple times because of performance issues.
        # If repeated calls are necessary, cache 'run.parent' as a local variable and call 'log()' on that variable.
        parent_run = Run.get_context().parent
        
        # Log left output port result of Evaluate Model. This also works when evaluate only 1 model.
        parent_run.log(name='Mean_Absolute_Error (left port)', value=dataframe1['Mean_Absolute_Error'][0])
        # Log right output port result of Evaluate Model. The following line should be deleted if you only connect one Score component to the` left port of Evaluate Model component.
        parent_run.log(name='Mean_Absolute_Error (right port)', value=dataframe1['Mean_Absolute_Error'][1])

        return dataframe1,
    ```
    
In diesem Code wird das Python-SDK für Azure Machine Learning zum Protokollieren von Werten verwendet. Es verwendet „Run.get_context()“, um den Kontext des aktuellen Testlaufs abzurufen. Anschließend werden mit der Methode „run.parent.log()“ Werte in diesem Kontext protokolliert. Es verwendet `parent`, um Werte im übergeordneten Pipelinelauf und nicht im Komponentenlauf zu protokollieren.

Weitere Informationen zur Verwendung des Python-SDK zum Protokollieren von Werten finden Sie unter [Überwachen von Azure ML-Experimentausführungen und -metriken](how-to-log-view-metrics.md).

## <a name="view-logs"></a>Anzeigen von Protokollen

Nachdem die Pipelineausführung abgeschlossen ist, wird der Fehler *Mean_Absolute_Error* auf der Seite „Experimente“ angezeigt.

1. Navigieren Sie zum Abschnitt **Experimente**.
1. Wählen Sie das Experiment aus.
1. Wählen Sie die Ausführung in Ihrem Experiment aus, die Sie anzeigen möchten.
1. Klicken Sie auf **Metriken**.

    ![Anzeigen von Ausführungsmetriken in Studio](./media/how-to-log-view-metrics/experiment-page-metrics-across-runs.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie Protokolle im Designer verwenden. Informationen zu den nächsten Schritten finden Sie in den folgenden Artikeln:


* Informationen zum Beheben von Problemen mit Designerpipelines im Artikel [Debuggen und Problembehandlung für Machine Learning-Pipelines](how-to-debug-pipelines.md#azure-machine-learning-designer)
* Informationen zum Verwenden des Python-SDK zum Protokollieren von Metriken in der SDK-Erstellungsfunktion im Artikel [Überwachen von Azure ML-Experimentausführungen und -metriken](how-to-log-view-metrics.md)
* Erfahren Sie, wie Sie [Execute Python Script](./algorithm-module-reference/execute-python-script.md) im Designer verwenden.
