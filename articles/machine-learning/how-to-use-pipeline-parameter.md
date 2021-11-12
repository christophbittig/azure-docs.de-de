---
title: Verwendung von Pipeline-Parametern zur Erstellung vielseitiger Pipelines
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie Pipelineparameter im Azure Machine Learning-Designer verwenden können.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.author: keli19
author: likebupt
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: designer
ms.openlocfilehash: d600f7ad651b58f3cf194c8f5dace8f6dc4c7695
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560338"
---
# <a name="use-pipeline-parameters-in-the-designer-to-build-versatile-pipelines"></a>Verwenden von Pipelineparametern im Designer zum Erstellen vielseitiger Pipelines

Verwenden Sie Pipelineparameter zum Erstellen flexibler Pipelines im Designer. Mit Pipelineparametern können Sie Werte zur Laufzeit dynamisch festlegen, um die Pipelinelogik zu kapseln und Ressourcen wiederzuverwenden.

Pipelineparameter sind besonders nützlich, wenn Sie eine Pipelineausführung erneut übermitteln, [Modelle erneut trainieren](how-to-retrain-designer.md) oder [Batchvorhersagen durchführen](how-to-run-batch-predictions-designer.md).

In diesem Artikel lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen von Pipelineparametern
> * Löschen und Verwalten von Pipelineparametern
> * Auslösen von Pipelineausführungen beim Anpassen von Pipelineparametern

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure Machine Learning-Arbeitsbereich. Weitere Informationen finden Sie unter [Erstellen eines Azure Machine Learning-Arbeitsbereichs](how-to-manage-workspace.md).

* Eine Einführung in den Designer erhalten Sie im [Designertutorial](tutorial-designer-automobile-price-train-score.md). 

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-pipeline-parameter"></a>Erstellen von Pipelineparametern

Es gibt drei Möglichkeiten, einen Pipelineparameter im Designer zu erstellen:
- Erstellen Sie im Einstellungsfenster einen Pipeline-Parameter, und binden Sie ihn an eine Komponente.
- Einen Komponentenparameter in einen Pipeline-Parameter umwandeln.
- Höherstufen eines Datasets zu einem Pipelineparameter

> [!NOTE]
> Pipelineparameter unterstützen nur grundlegende Datentypen wie `int`, `float` und `string`.

### <a name="option-1-create-a-pipeline-parameter-in-the-settings-panel"></a>Option 1: Erstellen eines Pipelineparameters im Einstellungsbereich

In diesem Abschnitt erstellen Sie einen Pipelineparameter im Einstellungsbereich.

In diesem Beispiel erstellen Sie einen Pipeline-Parameter, der definiert, wie eine Pipeline fehlende Daten mit der Komponente **Fehlende Daten bereinigen** auffüllt.

1. Wählen Sie neben dem Namen des Pipelineentwurfs das **Zahnradsymbol** aus, um den Bereich **Einstellungen** zu öffnen.

1. Wählen Sie im Abschnitt **Pipelineparameter** das **+** -Symbol aus.

1.  Geben Sie einen Namen für den Parameter und einen Standardwert ein. 

    Geben Sie z. B. `replace-missing-value` als Parameternamen und `0` als Standardwert ein.

   ![Screenshot, der zeigt, wie ein Pipelineparameter erstellt wird](media/how-to-use-pipeline-parameter/create-pipeline-parameter.png)


Nachdem Sie einen Pipeline-Parameter erstellt haben, müssen Sie ihn [ mit dem Komponentenparameter](#attach-component-parameter-to-pipeline-parameter) verbinden, den Sie dynamisch einstellen wollen.

### <a name="option-2-promote-a-component-parameter"></a>Option 2: Heraufsetzen eines Komponentenparameters

Die einfachste Möglichkeit, einen Pipeline-Parameter für einen Komponentenwert zu erstellen, besteht darin, einen Komponentenparameter zu fördern. Gehen Sie wie folgt vor, um einen Komponentenparameter zu einem Pipeline-Parameter zu machen:

1. Wählen Sie die Komponente aus, der Sie einen Pipeline-Parameter zuordnen möchten.
1. Bewegen Sie den Mauszeiger im Detailfenster der Komponente auf den Parameter, den Sie angeben möchten.
1. Wählen Sie die angezeigten Auslassungspunkte ( **...** ) aus.
1. Wählen Sie **Zu Pipelineparameter hinzufügen** aus.

    ![Bildschirmbild, das zeigt, wie man Komponentenparameter zu Pipeline-Parameter1 macht](media/how-to-use-pipeline-parameter/promote-module-para-to-pipeline-para.png)

1. Geben Sie einen Parameternamen und einen Standardwert ein.
1. Wählen Sie **Speichern** aus.

Sie können jetzt jederzeit neue Werte für diesen Parameter angeben, wenn Sie diese Pipeline übermitteln.

### <a name="option-3-promote-a-dataset-to-a-pipeline-parameter"></a>Option 3: Höherstufen eines Datasets zu einem Pipelineparameter

Wenn Sie Ihre Pipeline mit Variablendatasets übermitteln möchten, müssen Sie das Dataset zu einem Pipelineparameter heraufstufen:

1. Wählen Sie das Dataset aus, das Sie in einen Pipelineparameter umwandeln möchten.

1. Aktivieren Sie im Detailbereich des Datasets die Option **Set as pipeline parameter** (Als Pipelineparameter festlegen).

   ![Screenshot, der zeigt, wie ein Dataset als Pipelineparameter festgelegt wird](media/how-to-use-pipeline-parameter/set-dataset-as-pipeline-parameter.png)

Wenn Sie die Pipeline das nächste Mal ausführen, können Sie mit dem Pipelineparameter nun ein anderes Dataset angeben.

## <a name="attach-and-detach-component-parameter-to-pipeline-parameter"></a>Anhängen und Abkoppeln von Komponentenparametern an Pipeline-Parameter 

In diesem Abschnitt erfahren Sie, wie Sie Komponentenparameter an Pipeline-Parameter anhängen und von ihnen trennen können.

### <a name="attach-component-parameter-to-pipeline-parameter"></a>Komponentenparameter an Pipeline-Parameter anhängen

Sie können dieselben Komponentenparameter von duplizierten Komponenten an denselben Pipelineparameter anhängen, wenn Sie den Wert beim Auslösen des Pipelinelaufs auf einmal ändern möchten.

Das folgende Beispiel hat die Komponente **Fehlende Daten bereinigen** dupliziert. Fügen Sie für jede Komponente **Fehlende Daten bereinigen** den **Ersatzwert** dem Pipeline-Parameter **Ersatzwert** zu:

1. Wählen Sie die Komponente **Fehlende Daten bereinigen**.

1. Setzen Sie in der Komponentendetailansicht rechts vom Canvas den **Bereinigungsmodus** auf "Benutzerdefinierter Substitutionswert".

1. Setzen Sie den Mauszeiger über das Feld **Replacement value**.

1. Wählen Sie die angezeigten Auslassungspunkte ( **...** ) aus.

1. Wählen Sie den Pipelineparameter `replace-missing-value` aus.

   ![Screenshot, der das Anfügen an einen Pipelineparameter zeigt](media/how-to-use-pipeline-parameter/attach-replace-value-to-pipeline-parameter.png)

Sie haben das Feld **Replacement value** erfolgreich Ihrem Pipelineparameter angefügt. 


### <a name="detach-component-parameter-to-pipeline-parameter"></a>Komponentenparameter an Pipeline-Parameter abkoppeln

Nachdem Sie den **Replacement value** an den Pipelineparameter angefügt haben, ist er nicht aktionsfähig.

Sie können Komponentenparameter von Pipeline-Parametern abkoppeln, indem Sie auf die Ellipsen ( **...** ) neben dem Komponentenparameter klicken und **Abkoppeln von Pipeline-Parameter** wählen.

 ![Screenshot, der den nicht aktionsfähigen „Replacement value“ nach dem Anfügen an den Pipelineparameter zeigt](media/how-to-use-pipeline-parameter/non-actionable-module-parameter.png)

## <a name="update-and-delete-pipeline-parameters"></a>Aktualisieren und Löschen von Pipelineparametern

In diesem Abschnitt erfahren Sie, wie Sie Pipelineparameter aktualisieren und löschen.

### <a name="update-pipeline-parameters"></a>Aktualisieren von Pipelineparametern

Gehen Sie wie folgt vor, um einen Komponenten-Pipeline-Parameter zu aktualisieren:

1. Wählen Sie im oberen Bereich der Canvas das Zahnradsymbol aus.
1. Im Abschnitt **Pipeline parameters** können Sie den Namen und den Standardwert für alle Ihre Pipelineparameter anzeigen und aktualisieren.

### <a name="delete-a-dataset-pipeline-parameter"></a>Löschen eines Dataset-Pipelineparameters

Einen Dataset-Pipelineparameter können Sie mit den folgenden Schritten löschen:

1. Wählen Sie die Komponente "Datensatz".
1. Deaktivieren Sie die Option **Set as pipeline parameter** (Als Pipelineparameter festlegen).


### <a name="delete-component-pipeline-parameters"></a>Komponenten-Pipeline-Parameter löschen

Gehen Sie wie folgt vor, um einen Komponenten-Pipeline-Parameter zu löschen:

1. Wählen Sie im oberen Bereich der Canvas das Zahnradsymbol aus.

1. Wählen Sie die Auslassungspunkte ( **...** ) neben dem Pipelineparameter aus.

    In dieser Ansicht sehen Sie, mit welchen Komponenten der Pipeline-Parameter verbunden ist.

    ![Screenshot, der den aktuellen Pipeline-Parameter zeigt, der auf eine Komponente angewendet wird](media/how-to-use-pipeline-parameter/delete-pipeline-parameter2.png)

1. Wählen Sie **Delete parameter** (Parameter löschen) aus, um den Pipelineparameter zu löschen.

    > [!NOTE]
    > Das Löschen eines Pipeline-Parameters führt dazu, dass alle angehängten Komponentenparameter abgetrennt werden und der Wert der abgetrennten Komponentenparameter den aktuellen Pipeline-Parameterwert behält.     

## <a name="trigger-a-pipeline-run-with-pipeline-parameters"></a>Auslösen einer Pipelineausführung mit Pipelineparametern 

In diesem Abschnitt erfahren Sie, wie Sie eine Pipelineausführung beim Festlegen von Pipelineparametern übermitteln.

### <a name="resubmit-a-pipeline-run"></a>Erneutes Übermitteln einer Pipelineausführung

Nachdem Sie eine Pipeline mit Pipelineparametern übermittelt haben, können Sie eine Pipelineausführung mit unterschiedlichen Parametern erneut übermitteln:

1. Wechseln Sie zur Pipelinedetailsseite. Im Fenster **Pipeline run overview** (Übersicht über die Pipelineausführung) können Sie die aktuellen Pipelineparameter und -werte überprüfen.

1. Wählen Sie **Resubmit** (Erneut übermitteln) aus.
1. Geben Sie in **Setup pipeline run** (Pipelineausführung einrichten) Ihre neuen Pipelineparameter an. 

![Screenshot, der das erneute Übermitteln der Pipeline mit Pipelineparametern zeigt](media/how-to-use-pipeline-parameter/resubmit-pipeline-run.png)

### <a name="use-published-pipelines"></a>Verwenden veröffentlichter Pipelines

Sie können eine Pipeline auch veröffentlichen, um ihre Pipelineparameter zu verwenden. Eine **veröffentlichte Pipeline** ist eine Pipeline, die für eine Computeressource bereitgestellt wurde, die Clientanwendungen über einen REST-Endpunkt aufrufen können.

Veröffentlichte Endpunkte sind besonders nützlich für Trainings- und Batchvorhersageszenarios. Weitere Informationen finden Sie unter [Verwenden von Pipelineparametern zum erneuten Trainieren von Modellen im Designer](how-to-retrain-designer.md) oder [Ausführen von Batchvorhersagen mit dem Azure Machine Learning-Designer](how-to-run-batch-predictions-designer.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie Pipelineparameter im Designer erstellen. Als Nächstes erfahren Sie, wie Sie mit Pipelineparametern [Modelle erneut trainieren](how-to-retrain-designer.md) oder [Batchvorhersagen](how-to-run-batch-predictions-designer.md) ausführen können.

Sie können auch erfahren, wie Sie [Pipelines programmgesteuert mit dem SDK verwenden](how-to-deploy-pipelines.md) können.
