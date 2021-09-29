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
ms.date: 08/03/2021
ms.custom: designer, FY21Q4-aml-seo-hack, contperf-fy21q4
ms.openlocfilehash: 3a38717cec9aed40e3aff96376a9d956eb82a53d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124792218"
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

1. Platzieren von [Datasets](#datasets) und [Modulen](#module) auf der Canvas per Drag & Drop-Verfahren
1. Verbinden der Module, um einen [Pipelineentwurf](#pipeline-draft) zu erstellen
1. Übermitteln einer [Pipelineausführung](#pipeline-run) mithilfe der Computeressourcen in Ihrem Azure Machine Learning-Arbeitsbereich
1. Konvertieren Ihrer **Trainingspipelines** in die **Rückschlusspipelines**
1. [Veröffentlichen](#publish) Ihrer Pipelines an einem REST-basierten **Pipelineendpunkt**, um eine neue Pipeline zu übermitteln, die mit anderen Parametern und Datasets ausgeführt wird
    + Veröffentlichen einer **Trainingspipeline**, um beim Ändern von Parametern und Datasets eine einzelnen Pipeline zum Trainieren mehrerer Modelle nochmal zu verwenden
    + Veröffentlichen einer **Batchrückschlusspipeline**, um Vorhersage zu neuen Daten zu treffen, indem ein zuvor trainiertes Modell verwendet wird
1. [Bereitstellen](#deploy) einer **Echtzeit-Rückschlusspipeline** für einen Echtzeitendpunkt, um in Echtzeit Vorhersagen zu neuen Daten zu treffen

![Workflowdiagramm für Training, Batchrückschluss und Echtzeitrückschluss im Designer](./media/concept-designer/designer-workflow-diagram.png)

## <a name="pipeline"></a>Pipeline

Eine [Pipeline](concept-azure-machine-learning-architecture.md#ml-pipelines) besteht aus Datasets und algorithmischen Modulen, die Sie verbinden. Pipelines haben viele Verwendungsmöglichkeiten: Sie können eine Pipeline erstellen, die ein einzelnes Modell trainiert, oder eine, die mehrere Modelle trainiert. Sie können eine Pipeline erstellen, die Vorhersagen in Echtzeit oder im Batch erstellt, oder eine, die nur Daten bereinigt. Mit Pipelines können Sie Ihre Arbeit wiederverwenden und Ihre Projekte organisieren.

### <a name="pipeline-draft"></a>Pipelineentwurf

Wenn Sie eine Pipeline im Designer bearbeiten, wird der Fortschritt als **Pipelineentwurf** gespeichert. Sie können einen Pipelineentwurf jederzeit bearbeiten, indem Sie Module hinzufügen oder entfernen, Computeziele konfigurieren, Parameter erstellen usw.

Eine gültige Pipeline weist diese Merkmale auf:

* Datasets können nur mit Modulen eine Verbindung herstellen.
* Module können nur entweder mit Datasets oder mit anderen Modulen eine Verbindung herstellen.
* Alle Eingangsports für Module müssen eine Verbindung zum Datenfluss aufweisen.
* Alle erforderlichen Parameter der einzelnen Module müssen festgelegt sein.

Wenn Sie bereit sind, den Pipelineentwurf auszuführen, übermitteln Sie eine Pipelineausführung.

### <a name="pipeline-run"></a>Ausführen der Pipeline

Jedes Mal, wenn Sie eine Pipeline ausführen, werden die Konfiguration der Pipeline und ihre Ergebnisse in Ihrem Arbeitsbereich als **Pipelineausführung** gespeichert. Sie können zu jeder Pipelineausführung zurückkehren, um sie zur Problembehandlung oder zur Überwachung zu überprüfen. **Klonen** Sie eine Pipelineausführung, um einen neuen Pipelineentwurf zu erstellen, den Sie bearbeiten können.

Pipelineausführungen werden in [Experimenten](concept-azure-machine-learning-architecture.md#experiments) gruppiert, um den Ausführungsverlauf zu organisieren. Sie können das Experiment für jede Pipelineausführung festlegen. 

## <a name="datasets"></a>Datasets

Ein Azure Machine Learning-Dataset erleichtert Ihnen den Zugriff auf Ihre Daten und die Arbeit damit. Im Designer sind mehrere [Beispieldatasets](samples-designer.md#datasets) enthalten, mit denen Sie experimentieren können. Sie können bei Bedarf weitere Datasets [registrieren](how-to-create-register-datasets.md).

## <a name="module"></a>Modul

Ein Modul ist ein Algorithmus, den Sie auf Ihre Daten anwenden können. Der Designer verfügt über eine Reihe von Modulen, die von Funktionen für die Datenerfassung bis zu Trainings-, Bewertungs- und Überprüfungsvorgängen reichen.

Ein Modul kann eine Reihe von Parametern haben, die Sie zum Konfigurieren der internen Algorithmen des Moduls einsetzen können. Wenn Sie ein Modul auf der Canvas auswählen, werden dessen Parameter rechts neben der Canvas im Bereich „Eigenschaften“ angezeigt. Sie können die Parameter in diesem Bereich zur Abstimmung Ihrer Pipeline verändern. Sie können die Computeressourcen für einzelne Module im Designer festlegen. 

:::image type="content" source="./media/concept-designer/properties.png" alt-text="Moduleigenschaften":::


Unterstützung bei der Navigation durch die Bibliothek der verfügbaren Machine Learning-Algorithmen finden Sie unter [Algorithmen und Module: Referenzübersicht](algorithm-module-reference/module-reference.md). Hilfe bei der Auswahl eines Algorithmus finden Sie unter [Azure Machine Learning – Cheat Sheet für Algorithmen](algorithm-cheat-sheet.md).

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

Eine veröffentliche Pipeline wird auf den Computeressourcen ausgeführt, die Sie im Pipelineentwurf für jedes Modul definieren.

Der Designer erstellt dasselbe [PublishedPipeline](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline)-Objekt wie das SDK.

## <a name="next-steps"></a>Nächste Schritte

* Die Grundlagen von Predictive Analytics und maschinellem Lernen werden hier erläutert: [Tutorial: Prognostizieren von Automobilpreisen mit dem Designer](tutorial-designer-automobile-price-train-score.md).
* Erfahren Sie, wie Sie vorhandene [Designer-Beispiele](samples-designer.md) ändern, um sie an Ihre Anforderungen anzupassen.