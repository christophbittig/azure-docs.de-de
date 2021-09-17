---
title: 'ML Studio (Classic): Migrieren zu Azure Machine Learning – Neuerstellen von Webdiensten'
description: Neuerstellen von Studio (Classic)-Webdiensten als Pipelineendpunkte in Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: 8de2f380e9e33ee67233206a7d8dbfcbe705bdc1
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122699118"
---
# <a name="rebuild-a-studio-classic-web-service-in-azure-machine-learning"></a>Neuerstellen eines Studio (Classic)-Webdiensts in Azure Machine Learning

[!INCLUDE [ML Studio (classic) retirement](../../includes/machine-learning-studio-classic-deprecation.md)]

In diesem Artikel erfahren Sie, wie Sie einen ML Studio (Classic)-Webdienst als **Endpunkt** in Azure Machine Learning neu erstellen.

Mithilfe von Azure Machine Learning-Pipelineendpunkten können Sie Vorhersagen treffen, Modelle erneut trainieren oder generische Pipelines ausführen. Über den REST-Endpunkt können Sie Pipelines auf jeder beliebigen Plattform ausführen. 

Dieser Artikel ist Teil der Reihe zur Migration von Studio (Classic) zu Azure Machine Learning. Weitere Informationen zur Migration zu Azure Machine Learning finden Sie im Artikel mit der [allgemeinen Übersicht über die Migration](migrate-overview.md).

> [!NOTE]
> Diese Reihe zur Migration befasst sich mit dem Drag & Drop-Designer. Weitere Informationen zum programmgesteuerten Bereitstellen von Modellen finden Sie unter [Bereitstellen von Machine Learning-Modellen in Azure](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Ein Azure Machine Learning-Arbeitsbereich. [Erstellen Sie einen Azure Machine Learning-Arbeitsbereich](how-to-manage-workspace.md#create-a-workspace).
- Eine Azure Machine Learning-Trainingspipeline. Weitere Informationen finden Sie unter [Neuerstellen eines Studio (Classic)-Experiments in Azure Machine Learning](migrate-rebuild-experiment.md).

## <a name="real-time-endpoint-vs-pipeline-endpoint"></a>Echtzeitendpunkt und Pipelineendpunkt im Vergleich

Die Webdienste von Studio (Classic) wurden in Azure Machine Learning durch **Endpunkte** ersetzt. Anhand der folgenden Tabelle können Sie den zu verwendenden Endpunkttyp auswählen:

|Studio (Classic)-Webdienst| Azure Machine Learning-Ersatz
|---|---|
|Anforderung/Antwort-Webdienst (Echtzeitvorhersage)|Echtzeitendpunkt|
|Batchwebdienst (Batchvorhersage)|Pipelineendpunkt|
|Webdienst zum erneuten Trainieren (erneutes Trainieren)|Pipelineendpunkt| 


## <a name="deploy-a-real-time-endpoint"></a>Bereitstellen eines Echtzeitendpunkts

In Studio (Classic) haben Sie einen **ANFORDERUNG/ANTWORT-Webdienst** verwendet, um ein Modell für Echtzeitvorhersagen bereitzustellen. In Azure Machine Learning wird stattdessen ein **Echtzeitendpunkt** verwendet.

Ein Modell kann in Azure Machine Learning auf verschiedene Weise bereitgestellt werden. Eine der einfachsten Möglichkeiten ist die Verwendung des Designers zur Automatisierung des Bereitstellungsprozesses. Führen Sie die folgenden Schritte aus, um ein Modell als Echtzeitendpunkt bereitzustellen:

1. Führen Sie die abgeschlossene Trainingspipeline mindestens einmal aus.
1. Wählen Sie nach Abschluss der Ausführung im oberen Bereich der Canvas die Optionen **Rückschlusspipeline erstellen** > **Echtzeit-Rückschlusspipeline** aus.

    ![Erstellen einer Echtzeit-Rückschlusspipeline](./media/migrate-rebuild-web-service/create-inference-pipeline.png)
        
    Der Designer konvertiert die Trainingspipeline in eine Echtzeit-Rückschlusspipeline. Auch in Studio (Classic) wird eine ähnliche Konvertierung durchgeführt.

    Im Designer wird beim Konvertierungsschritt außerdem [das trainierte Modell in Ihrem Azure Machine Learning-Arbeitsbereich registriert](how-to-deploy-and-where.md#registermodel).

1. Wählen Sie **Übermitteln** aus, um die Echtzeit-Rückschlusspipeline auszuführen, und überprüfen Sie, ob sie erfolgreich ausgeführt wird.

1. Wählen Sie nach dem Überprüfen der Rückschlusspipeline die Option **Bereitstellen** aus.

1. Geben Sie einen Namen für den Endpunkt und einen Computetyp ein.

    In der folgenden Tabelle sind die Computeoptionen für die Bereitstellung im Designer beschrieben:

    | Computeziel | Syntaxelemente | BESCHREIBUNG | Erstellung |
    | ----- |  ----- | ----- | -----  |
    |[Azure Kubernetes Service (AKS)](how-to-deploy-azure-kubernetes-service.md) |Echtzeitrückschluss|Umfangreiche Produktionsbereitstellungen Schnelle Reaktionszeit und automatische Skalierung von Diensten| Vom Benutzer erstellt Weitere Informationen finden Sie unter [Erstellen von Computezielen](how-to-create-attach-compute-studio.md#inference-clusters). |
    |[Azure Container Instances](how-to-deploy-azure-container-instance.md)|Testen oder Entwicklung | Kleine CPU-basierte Workloads, die weniger als 48 GB Arbeitsspeicher erfordern.| Automatische Erstellung in Azure Machine Learning.

### <a name="test-the-real-time-endpoint"></a>Testen des Echtzeitendpunkts

Nach Abschluss der Bereitstellung können Sie weitere Details anzeigen und den Endpunkt testen:

1. Wechseln Sie zur Registerkarte **Endpunkte**.
1. Wählen Sie den Endpunkt aus.
1. Wählen Sie die Registerkarte **Testen** aus.
    
    ![Screenshot der Registerkarte „Endpunkte“ mit der Schaltfläche „Testen“ für den Endpunkt](./media/migrate-rebuild-web-service/test-realtime-endpoint.png)

## <a name="publish-a-pipeline-endpoint-for-batch-prediction-or-retraining"></a>Veröffentlichen eines Pipelineendpunkts für die Batchvorhersage oder das erneute Trainieren

Sie können die Trainingspipeline auch zum Erstellen eines **Pipelineendpunkts** anstelle eines Echtzeitendpunkts verwenden. Verwenden Sie **Pipelineendpunkte**, um eine Batchvorhersage oder ein erneutes Training auszuführen.

Pipelineendpunkte ersetzen die **Batchausführungsendpunkte** und **Webdienste zum erneuten Trainieren** in Studio (Classic).

### <a name="publish-a-pipeline-endpoint-for-batch-prediction"></a>Veröffentlichen eines Pipelineendpunkts für die Batchvorhersage

Das Veröffentlichen eines Batchvorhersageendpunkts ähnelt dem des Echtzeitendpunkts.

Führen Sie die folgenden Schritte aus, um einen Pipelineendpunkt für die Batchvorhersage zu veröffentlichen:

1. Führen Sie die abgeschlossene Trainingspipeline mindestens einmal aus.

1. Wählen Sie nach Abschluss der Ausführung im oberen Bereich der Canvas die Optionen **Rückschlusspipeline erstellen** > **Batch-Rückschlusspipeline** aus.

    ![Screenshot der Schaltfläche „Rückschlusspipeline erstellen“ in einer Trainingspipeline](./media/migrate-rebuild-web-service/create-inference-pipeline.png)
        
    Der Designer konvertiert die Trainingspipeline in eine Batch-Rückschlusspipeline. Auch in Studio (Classic) wird eine ähnliche Konvertierung durchgeführt.

    Im Designer wird bei diesem Schritt außerdem [das trainierte Modell in Ihrem Azure Machine Learning-Arbeitsbereich registriert](how-to-deploy-and-where.md#registermodel).

1. Wählen Sie **Übermitteln** aus, um die Batch-Rückschlusspipeline auszuführen, und überprüfen Sie, ob sie erfolgreich abgeschlossen wird.

1. Wählen Sie nach dem Überprüfen der Rückschlusspipeline die Option **Veröffentlichen** aus.
 
1. Erstellen Sie einen neuen Pipelineendpunkt, oder wählen Sie einen vorhandenen Pipelineendpunkt aus.
    
    Mit einem neuen Pipelineendpunkt wird ein neuer REST-Endpunkt für die Pipeline erstellt. 

    Wenn Sie einen vorhandenen Pipelineendpunkt auswählen, wird die vorhandene Pipeline nicht überschrieben. Stattdessen werden in Azure Machine Learning alle Pipelines im Endpunkt versioniert. Sie können im REST-Aufruf angeben, welche Version ausgeführt werden soll. Sie müssen außerdem eine Standardpipeline festlegen, wenn im REST-Aufruf keine Version angegeben ist.


 ### <a name="publish-a-pipeline-endpoint-for-retraining"></a>Veröffentlichen eines Pipelineendpunkts für das erneute Trainieren

Zum Veröffentlichen eines Pipelineendpunkts für das erneute Trainieren müssen Sie bereits über einen Pipelineentwurf verfügen, mit dem ein Modell trainiert wird. Weitere Informationen zum Erstellen einer Trainingspipeline finden Sie unter [Neuerstellen eines Studio (Classic)-Experiments](migrate-rebuild-experiment.md).

Um den Pipelineendpunkt für das erneute Trainieren wiederzuverwenden, müssen Sie einen **Pipelineparameter** für das Eingabedataset erstellen. Auf diese Weise können Sie das Trainingsdataset dynamisch festlegen, damit Sie das Modell erneut trainieren können.

Führen Sie die folgenden Schritte aus, um den Pipelineendpunkt für das erneute Trainieren zu veröffentlichen:

1. Führen Sie die Trainingspipeline mindestens einmal aus.
1. Wählen Sie nach Abschluss der Ausführung das Datasetmodul aus.
1. Wählen Sie im Bereich „Moduldetails“ die Option **Als Pipelineparameter festlegen** aus.
1. Geben Sie einen aussagekräftigen Namen an, z. B. „InputDataset“.    

    ![Screenshot der Erstellung eines Pipelineparameters](./media/migrate-rebuild-web-service/create-pipeline-parameter.png)

    Dadurch wird ein Pipelineparameter für das Eingabedataset erstellt. Beim Aufrufen des Pipelineendpunkts für das Trainieren können Sie ein neues Dataset zum erneuten Trainieren des Modells angeben.

1. Wählen Sie **Veröffentlichen**.

    ![Screenshot mit hervorgehobener Schaltfläche „Veröffentlichen“ in einer Trainingspipeline](./media/migrate-rebuild-web-service/create-retraining-pipeline.png)


## <a name="call-your-pipeline-endpoint-from-the-studio"></a>Aufrufen des Pipelineendpunkts über Studio

Nachdem Sie den Pipelineendpunkt für den Batch-Rückschluss oder das erneute Trainieren erstellt haben, können Sie den Endpunkt direkt über den Browser aufrufen.

1. Wechseln Sie zur Registerkarte **Pipelines**, und wählen Sie **Pipelineendpunkte** aus.
1. Wählen Sie den Pipelineendpunkt aus, der ausgeführt werden soll.
1. Klicken Sie auf **Senden**.

    Nach der Auswahl von **Übermitteln** können Sie Pipelineparameter angeben.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie einen Studio (Classic)-Webdienst in Azure Machine Learning neu erstellen. Im nächsten Schritt [integrieren Sie den Webdienst in Clientanwendungen](migrate-rebuild-integrate-with-client-app.md).


Weitere Informationen finden Sie in den anderen Artikeln der Reihe zur Migration von Studio (Classic):

1. [Übersicht über die Migration](migrate-overview.md).
1. [Migrieren eines Datasets](migrate-register-dataset.md)
1. [Neuerstellen einer Studio (Classic)-Trainingspipeline](migrate-rebuild-experiment.md)
1. **Neuerstellen eines Studio (Classic)-Webdiensts**
1. [Integrieren eines Azure Machine Learning-Webdiensts in Clientanwendungen](migrate-rebuild-integrate-with-client-app.md)
1. [Migrieren von „R-Skript ausführen“-Modulen](migrate-execute-r-script.md)
