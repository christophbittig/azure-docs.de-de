---
title: Was ist der Azure Machine Learning-Designer?
titleSuffix: Azure Machine Learning
description: Erfahren Sie, was der Azure Machine Learning-Designer ist und für welche Aufgaben Sie ihn verwenden können. Die Drag & Drop-Benutzeroberfläche ermöglicht das Modelltraining und die Modellimplementierung.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.date: 10/21/2021
ms.custom: designer, FY21Q4-aml-seo-hack, contperf-fy21q4
ms.openlocfilehash: 07132095c3f64aa4df3f6ec728894a625bac2b74
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561705"
---
# <a name="what-is-azure-machine-learning-designer"></a>Was ist der Azure Machine Learning-Designer? 

Azure Machine Learning-Designer ist eine Drag & Drop-Schnittstelle, die zum Trainieren und Bereitstellen von Modellen in Azure Machine Learning verwendet wird. In diesem Artikel werden die Aufgaben beschrieben, die Sie im Designer ausführen können.

 - Informationen zu den ersten Schritten mit dem Designer finden Sie unter [Tutorial: Trainieren eines Regressionsmodells ohne Code](tutorial-designer-automobile-price-train-score.md). 
 - Informationen zu den im Designer verfügbaren Komponenten finden Sie in der [Referenz zu Algorithmen und Komponenten](./algorithm-module-reference/module-reference.md).

![Azure Machine Learning-Designer: Beispiel](./media/concept-designer/designer-drag-and-drop.gif)

Der Designer verwendet Ihren Azure Machine Learning-[Arbeitsbereich](concept-workspace.md), um gemeinsame Ressourcen wie die folgenden zu organisieren:

+ [Pipelines](#pipeline)
+ [Datasets](#datasets)
+ [Computeressourcen](#compute)
+ [Registrierte Modelle](concept-azure-machine-learning-architecture.md#models)
+ [Veröffentliche Pipelines](#publish)
+ [Echtzeitendpunkte](#deploy)

## <a name="model-training-and-deployment"></a>Trainieren und Bereitstellen des Modells

Verwenden Sie eine visuelle Canvas, um einen End-to-End-Machine Learning-Workflow zu erstellen. Trainieren, Testen und Bereitstellen von Modellen im Designer:

+ Ziehen Sie [Datensätze](#datasets) und [Komponenten](#component) auf die Leinwand.
+ Verbinden Sie die Komponenten, um einen [Pipelineentwurf](#pipeline-draft) zu erstellen.
+ Übermitteln einer [Pipelineausführung](#pipeline-run) mithilfe der Computeressourcen in Ihrem Azure Machine Learning-Arbeitsbereich
+ Konvertieren Ihrer **Trainingspipelines** in die **Rückschlusspipelines**
+ [Veröffentlichen](#publish) Ihrer Pipelines an einem REST-basierten **Pipelineendpunkt**, um eine neue Pipeline zu übermitteln, die mit anderen Parametern und Datasets ausgeführt wird
    + Veröffentlichen einer **Trainingspipeline**, um beim Ändern von Parametern und Datasets eine einzelnen Pipeline zum Trainieren mehrerer Modelle nochmal zu verwenden
    + Veröffentlichen einer **Batchrückschlusspipeline**, um Vorhersage zu neuen Daten zu treffen, indem ein zuvor trainiertes Modell verwendet wird
1. [Bereitstellen](#deploy) einer **Echtzeit-Rückschlusspipeline** für einen Echtzeitendpunkt, um in Echtzeit Vorhersagen zu neuen Daten zu treffen

![Workflowdiagramm für Training, Batchrückschluss und Echtzeitrückschluss im Designer](./media/concept-designer/designer-workflow-diagram.png)

## <a name="pipeline"></a>Pipeline

Eine [Pipeline](concept-azure-machine-learning-architecture.md#ml-pipelines) besteht aus Datensätzen und analytischen Komponenten, die Sie miteinander verbinden. Pipelines haben viele Verwendungsmöglichkeiten: Sie können eine Pipeline erstellen, die ein einzelnes Modell trainiert, oder eine, die mehrere Modelle trainiert. Sie können eine Pipeline erstellen, die Vorhersagen in Echtzeit oder im Batch erstellt, oder eine, die nur Daten bereinigt. Mit Pipelines können Sie Ihre Arbeit wiederverwenden und Ihre Projekte organisieren.

### <a name="pipeline-draft"></a>Pipelineentwurf

Wenn Sie eine Pipeline im Designer bearbeiten, wird der Fortschritt als **Pipelineentwurf** gespeichert. Sie können einen Pipeline-Entwurf jederzeit bearbeiten, indem Sie Komponenten hinzufügen oder entfernen, Berechnungsziele konfigurieren, Parameter erstellen und so weiter.

Eine gültige Pipeline weist diese Merkmale auf:

* Datasets können nur mit Komponenten verbunden werden.
* Komponenten können nur entweder mit Datensätzen oder anderen Komponenten verbunden werden.
* Alle Eingangsanschlüsse für Komponenten müssen in irgendeiner Weise mit dem Datenfluss verbunden sein.
* Alle erforderlichen Parameter für jede Komponente müssen eingestellt werden.

Wenn Sie bereit sind, den Pipelineentwurf auszuführen, übermitteln Sie eine Pipelineausführung.

### <a name="pipeline-run"></a>Ausführen der Pipeline

Jedes Mal, wenn Sie eine Pipeline ausführen, werden die Konfiguration der Pipeline und ihre Ergebnisse in Ihrem Arbeitsbereich als **Pipelineausführung** gespeichert. Sie können zu jeder Pipelineausführung zurückkehren, um sie zur Problembehandlung oder zur Überwachung zu überprüfen. **Klonen** Sie eine Pipelineausführung, um einen neuen Pipelineentwurf zu erstellen, den Sie bearbeiten können.

Pipelineausführungen werden in [Experimenten](concept-azure-machine-learning-architecture.md#experiments) gruppiert, um den Ausführungsverlauf zu organisieren. Sie können das Experiment für jede Pipelineausführung festlegen. 

## <a name="datasets"></a>Datasets

Ein Azure Machine Learning-Dataset erleichtert Ihnen den Zugriff auf Ihre Daten und die Arbeit damit. Im Designer sind mehrere [Beispieldatasets](samples-designer.md#datasets) enthalten, mit denen Sie experimentieren können. Sie können bei Bedarf weitere Datasets [registrieren](how-to-create-register-datasets.md).

## <a name="component"></a>Komponente

Eine Komponente ist ein Algorithmus, den Sie auf Ihre Daten anwenden können. Der Designer besteht aus mehreren Komponenten, die von Dateneingabefunktionen bis hin zu Trainings-, Scoring- und Validierungsprozessen reichen.

Eine Komponente kann über eine Reihe von Parametern verfügen, die Sie zur Konfiguration der internen Algorithmen der Komponente verwenden können. Wenn Sie eine Komponente auf der Arbeitsfläche auswählen, werden die Parameter der Komponente in der Eigenschaftsleiste rechts von der Arbeitsfläche angezeigt. Sie können die Parameter in diesem Bereich zur Abstimmung Ihres Modells verändern. Sie können die Kompute ressourcen für einzelne Komponenten im Designer festlegen. 

:::image type="content" source="./media/concept-designer/properties.png" alt-text="Komponenteneigenschaften":::


Hilfe bei der Navigation durch die Bibliothek der verfügbaren Algorithmen für maschinelles Lernen finden Sie unter [Algorithmus- und Komponentenreferenzübersicht](component-reference/component-reference.md). Hilfe bei der Auswahl eines Algorithmus finden Sie unter [Azure Machine Learning – Cheat Sheet für Algorithmen](algorithm-cheat-sheet.md).

## <a name="compute-resources"></a><a name="compute"></a> Computeressourcen

Verwenden Sie Computeressourcen aus Ihrem Arbeitsbereich, um Ihre Pipeline auszuführen und Ihre bereitgestellten Modelle als Echtzeitendpunkte oder Pipelineendpunkte (für Batchrückschlüsse) zu hosten. Die unterstützten Computeziele lauten:

| Computeziel | Training | Bereitstellung |
| ---- |:----:|:----:|
| Azure Machine Learning Compute | ✓ | |
| Azure Kubernetes Service | | ✓ |

Computeziele sind an Ihren [Azure Machine Learning-Arbeitsbereich](concept-workspace.md) angefügt. Ihre Computeziele verwalten Sie in Ihrem Arbeitsbereich im [Azure Machine Learning-Studio](https://ml.azure.com).

## <a name="deploy"></a>Bereitstellen

Zum Ausführen von Rückschlüssen in Echtzeit müssen Sie eine Pipeline als **Echtzeitendpunkt** bereitstellen. Der Echtzeitendpunkt erstellt eine Schnittstelle zwischen einer externen Anwendung und Ihrem Bewertungsmodell. Durch den Aufruf eines Echtzeitendpunkts werden Vorhersageergebnisse in Echtzeit an die Anwendung zurückgegeben. Für einen Aufruf eines Echtzeitendpunkts übergeben Sie den API-Schlüssel, der beim Bereitstellen des Endpunkts erstellt wurde. Der Endpunkt basiert auf REST, einer gängigen Architektur für Webprogrammierungsprojekte.

Echtzeitendpunkte müssen in einem Azure Kubernetes Service-Cluster bereitgestellt werden.

Informationen dazu, wie Sie Ihr Modell bereitstellen, finden Sie unter [Tutorial: Bereitstellen eines Machine Learning-Modells mit dem Designer](tutorial-designer-automobile-price-deploy.md).

[!INCLUDE [endpoints-option](../../includes/machine-learning-endpoints-preview-note.md)]

## <a name="publish"></a>Veröffentlichen

Sie können eine Pipeline auch in einem **Pipelineendpunkt** veröffentlichen. Ähnlich wie bei einem Echtzeitendpunkt ermöglicht Ihnen ein Pipelineendpunkt das Übermitteln von neuen Pipelineausführungen aus externen Anwendungen mithilfe von REST-Aufrufen. Es ist jedoch nicht möglich, Daten in Echtzeit mithilfe eines Pipelineendpunkts zu senden oder zu empfangen.

Veröffentlichte Pipelines sind flexibel und können zum Trainieren oder erneuten Trainieren von Modellen, zum [Ausführen von Batchrückschlüssen](how-to-run-batch-predictions-designer.md), Verarbeiten neuer Daten und vieles mehr verwendet werden. Sie können mehrere Pipelines in einem einzigen Pipelineendpunkt veröffentlichen und angeben, welche Pipelineversion ausgeführt werden soll.

Eine veröffentlichte Pipeline wird auf den Rechenressourcen ausgeführt, die Sie im Pipeline-Entwurf für jede Komponente definieren.

Der Designer erstellt dasselbe [PublishedPipeline](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline)-Objekt wie das SDK.

## <a name="next-steps"></a>Nächste Schritte

* Die Grundlagen von Predictive Analytics und maschinellem Lernen werden hier erläutert: [Tutorial: Prognostizieren von Automobilpreisen mit dem Designer](tutorial-designer-automobile-price-train-score.md).
* Erfahren Sie, wie Sie vorhandene [Designer-Beispiele](samples-designer.md) ändern, um sie an Ihre Anforderungen anzupassen.