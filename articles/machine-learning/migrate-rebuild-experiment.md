---
title: 'ML Studio (Classic): Migrieren zu Azure Machine Learning – Neuerstellen von Experimenten'
description: Neuerstellen von Studio (Classic)-Experimenten im Azure Machine Learning-Designer
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 10/21/2021
ms.openlocfilehash: fd0bb1394ce2ce8b8cf8d58ece5834f08e538eaa
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556196"
---
# <a name="rebuild-a-studio-classic-experiment-in-azure-machine-learning"></a>Neuerstellen eines Studio (Classic)-Experiments in Azure Machine Learning

[!INCLUDE [ML Studio (classic) retirement](../../includes/machine-learning-studio-classic-deprecation.md)]

In diesem Artikel erfahren Sie, wie Sie ein Experiment von ML Studio (klassisch) in Azure Machine Learning neu erstellen. Weitere Informationen zur Migration aus Studio (Classic) finden Sie im Artikel mit der [Übersicht über die Migration](migrate-overview.md).

**Experimente** in Studio (Classic) sind vergleichbar mit **Pipelines** in Azure Machine Learning. In Azure Machine Learning werden Pipelines jedoch auf dem gleichen Back-End erstellt, auf dem auch das SDK läuft. Das bedeutet, dass Sie zwei Optionen für die Entwicklung beim maschinellen Lernen haben: den Drag & Drop-Designer oder Code First-SDKs.

Weitere Informationen zum Erstellen von Pipelines mit dem SDK finden Sie unter [Was sind Azure Machine Learning-Pipelines?](concept-ml-pipelines.md#building-pipelines-with-the-python-sdk).


## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Ein Azure Machine Learning-Arbeitsbereich. [Erstellen Sie einen Azure Machine Learning-Arbeitsbereich](how-to-manage-workspace.md#create-a-workspace).
- Ein zu migrierendes Studio (Classic)-Experiment.
- [Laden Sie das Dataset](migrate-register-dataset.md) in Azure Machine Learning hoch.

## <a name="rebuild-the-pipeline"></a>Neuerstellen der Pipeline

Nachdem Sie [das Dataset zu Azure Machine Learning migriert haben](migrate-register-dataset.md), können Sie das Experiment neu erstellen.

In Azure Machine Learning wird das visuelle Diagramm als **Pipelineentwurf** bezeichnet. In diesem Abschnitt erstellen Sie ein klassisches Experiment als Pipelineentwurf neu.

1. Wechseln Sie zu Azure Machine Learning Studio ([ml.azure.com](https://ml.azure.com)).
1. Wählen Sie im linken Navigationsbereich die Optionen **Designer** > **Easy-to-use prebuilt modules** (Benutzerfreundliche vordefinierte Module) aus. ![Screenshot der Erstellung eines neuen Pipelineentwurfs](./media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. Erstellen Sie das Experiment manuell mit Designerkomponenten neu.
    
    Suchen Sie in der [Modulzuordnungstabelle](migrate-overview.md#studio-classic-and-designer-component-mapping) nach Ersatzmodulen. Für viele der am häufigsten verwendeten Module von Studio (Classic) gibt es identische Versionen im Designer.

    > [!Important]
    > Wenn in Ihrem Experiment das Modul „R-Skript ausführen“ verwendet wird, müssen Sie für die Migration des Skripts zusätzliche Schritte ausführen. Weitere Informationen finden Sie unter [Migrieren von „R-Skript ausführen“-Modulen](migrate-execute-r-script.md).

1. Passen Sie die Parameter an.
    
    Wählen Sie jedes Modul aus, und passen Sie die Parameter im Bereich „Moduleinstellungen“ rechts an. Anhand der Parameter können Sie die Funktionen des Studio (Classic)-Experiments neu erstellen. Weitere Informationen zu den einzelnen Modulen finden Sie in der [Modulreferenz](./component-reference/component-reference.md).

## <a name="submit-a-run-and-check-results"></a>Übermitteln einer Ausführung und Überprüfen der Ergebnisse

Nach dem Neuerstellen des Studio (Classic)-Experiments können Sie eine **Pipelineausführung** übermitteln.

Eine Pipelineausführung erfolgt auf einem **Computeziel**, das an Ihren Arbeitsbereich angefügt ist. Sie können ein Standardcomputeziel für die gesamte Pipeline festlegen oder jeweils ein Computeziel pro Modul angeben.

Nachdem Sie eine Ausführung aus einem Pipelineentwurf übermittelt haben, wird er zu einer **Pipelineausführung**. Jede Pipelineausführung wird in Azure Machine Learning aufgezeichnet und protokolliert.

So legen Sie ein Standardcomputeziel für die gesamte Pipeline fest
1. Wählen Sie das **Zahnradsymbol** ![Zahnradsymbol im Designer](./media/tutorial-designer-automobile-price-train-score/gear-icon.png) neben dem Pipelinenamen aus.
1. Wählen Sie **Computeziel auswählen** aus.
1. Wählen Sie ein vorhandenes Computeziel aus, oder erstellen Sie ein neues Computeziel durch Befolgen der Anweisungen auf dem Bildschirm.

Nachdem das Computeziel festgelegt ist, können Sie eine Pipelineausführung übermitteln:

1. Wählen Sie im oberen Bereich der Canvas die Option **Senden** aus.
1. Wählen Sie **Neu erstellen** aus, um ein neues Experiment zu erstellen.
    
    In Experimenten werden ähnliche Pipelineausführungen zusammengefasst. Wenn Sie eine Pipeline mehrmals ausführen, können Sie für weitere Ausführungen das gleiche Experiment auswählen. Dies ist nützlich für die Protokollierung und Nachverfolgung.
1. Geben Sie einen Experimentnamen ein. Klicken Sie dann auf **Senden**.

    Die erste Ausführung kann bis zu 20 Minuten dauern. In den Standardcomputeeinstellungen ist eine minimale Knotengröße von 0 festgelegt. Das bedeutet, dass Ressourcen nach dem Leerlauf im Designer zugeordnet werden müssen. Nachfolgende Ausführungen werden schneller abgeschlossen, da die Knoten bereits zugeordnet sind. Um die Ausführungszeit zu beschleunigen, können Sie Computeressourcen mit einer minimalen Knotengröße von 1 oder höher erstellen.

Nach Abschluss der Ausführung können Sie die Ergebnisse der einzelnen Module überprüfen:

1. Klicken Sie mit der rechten Maustaste auf das Modul, dessen Ausgabe angezeigt werden soll.
1. Wählen Sie entweder **Visualisieren**, **Ausgabe anzeigen** oder **Protokoll anzeigen** aus.

    - **Visualisieren:** Das Ergebnisdataset wird in einer Vorschau angezeigt.
    - **Ausgabe anzeigen:** Ein Link zum Ausgabespeicherort wird geöffnet. Verwenden Sie diese Option, um die Ausgabe zu untersuchen oder herunterzuladen. 
    - **Protokoll anzeigen:** Treiber- und Systemprotokolle werden angezeigt. Öffnen Sie die Datei **70_driver_log**, um Informationen zum vom Benutzer übermittelten Skript anzuzeigen, z. B. Fehler und Ausnahmen.

> [!IMPORTANT]
> In Designerkomponenten werden Open-Source-Pakete in Python verwendet, in Studio (Classic) dagegen C#-Pakete. Daher kann die Modulausgabe im Designer von der in Studio (Classic) abweichen. 


## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie ein Studio (Classic)-Experiment in Azure Machine Learning neu erstellen. Im nächsten Schritt werden [Webdienste in Azure Machine Learning neu erstellt](migrate-rebuild-web-service.md).


Weitere Informationen finden Sie in den anderen Artikeln der Reihe zur Migration von Studio (Classic):

1. [Übersicht über die Migration](migrate-overview.md).
1. [Migrieren eines Datasets](migrate-register-dataset.md)
1. **Neuerstellen einer Studio (Classic)-Trainingspipeline**
1. [Neuerstellen eines Studio (Classic)-Webdiensts](migrate-rebuild-web-service.md)
1. [Integrieren eines Azure Machine Learning-Webdiensts in Clientanwendungen](migrate-rebuild-integrate-with-client-app.md)
1. [Migrieren von „R-Skript ausführen“-Modulen](migrate-execute-r-script.md)