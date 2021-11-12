---
title: ResNet
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mit dem ResNet-Algorithmus ein Bildklassifizierungsmodell im Azure Machine Learning-Designer erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: c6be11cc4dafb5423d38bc869fc8b915b48814ce
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566401"
---
# <a name="resnet"></a>ResNet

Dieser Artikel beschreibt die Verwendung der Komponente **ResNet** im Azure Machine Learning Designer, um ein Bildklassifizierungsmodell mit dem ResNet-Algorithmus zu erstellen.  

Dieser Klassifizierungsalgorithmus ist eine überwachte Lernmethode und erfordert ein Dataset mit Bezeichnungen. 
> [!NOTE]
> Diese Komponente unterstützt keine beschrifteten Datensätze, die von *Datenbeschriftung* im Studio erzeugt wurden, sondern nur beschriftete Bildverzeichnisse, die von [ In Bildverzeichnis konvertieren](convert-to-image-directory.md) Komponente. 

Sie können das Modell trainieren, indem Sie ein Modell und ein Bildverzeichnis mit Bezeichnungen als Eingaben für [Trainieren eines PyTorch-Modells](train-pytorch-model.md) angeben. Mit dem trainierten Modell können Sie anschließend Werte für neue Eingabebeispiele mithilfe von [Bewerten eines Bildmodells](score-image-model.md) vorhersagen.

### <a name="more-about-resnet"></a>Weitere Informationen zu ResNet

Weitere Einzelheiten zu ResNet finden Sie in [diesem Artikel](https://pytorch.org/vision/stable/models.html#torchvision.models.resnext101_32x8d).

## <a name="how-to-configure-resnet"></a>Konfigurieren von ResNet

1.  Fügen Sie die Komponente **ResNet** zu Ihrer Pipeline im Designer hinzu.  

2.  Geben Sie für **Modellname** den Namen einer bestimmten ResNet-Struktur an, und Sie können unter den unterstützten ResNet-Optionen auswählen: 'resnet18', 'resnet34', 'resnet50', 'resnet101', 'resnet152', 'resnet152', 'resnext50\_32x4d', 'resnext101\_32x8d', 'wide_resnet50\_2', 'wide_resnet101\_2'.

3.  Geben Sie für **Vortrainiert** an, ob ein in ImageNet vortrainiertes Modell verwendet werden soll. Bei Auswahl dieser Option können Sie das Modell basierend auf dem ausgewählten vortrainierten Modell optimieren. Wird die Auswahl aufgehoben, können Sie von Grund auf neu trainieren.

4.  Bestimmen Sie für **Zero init residual**, ob die letzte Stapelnormschicht in jedem Restzweig null-initialisiert werden soll. Wenn diese Option ausgewählt ist, beginnt der Restzweig mit Nullen, und jeder Restblock verhält sich wie eine Identität. Dies kann bei der Konvergenz bei großen Stapelgrößen gemäß https://arxiv.org/abs/1706.02677 helfen.

5.  Verbinden Sie die Ausgabe der **ResNet**-Komponente, der Trainings- und der Validierungs-Bilddatensatz-Komponente mit dem [Train Pytorch Model](train-pytorch-model.md). 

6.  Übermitteln Sie die Pipeline.

## <a name="results"></a>Ergebnisse

Nachdem die Pipeline-Ausführung abgeschlossen ist und um das Modell für die Bewertung zu verwenden, verbinden Sie das [Train PyTorch Model](train-pytorch-model.md) mit dem [Score Image Model](score-image-model.md), um Werte für neue Eingabebeispiele vorherzusagen.

## <a name="technical-notes"></a>Technische Hinweise  

###  <a name="component-parameters"></a>Komponentenparameter  

| Name       | Range | type    | Standard           | BESCHREIBUNG                              |
| ---------- | ----- | ------- | ----------------- | ---------------------------------------- |
| Modellname | Any   | Mode    | resnext101\_32x8d | Name einer bestimmten ResNet-Struktur       |
| Vortrainiert | Any   | Boolean | True              | Gibt an, ob ein in ImageNet vortrainiertes Modell verwendet werden soll |
| Rest-Zero init | Any | Boolean | False | Ob die letzte Stapelnormschicht in jedem Restzweig null-initialisiert werden soll |
|            |       |         |                   |                                          |

###  <a name="output"></a>Output  

| Name            | type                    | BESCHREIBUNG                              |
| --------------- | ----------------------- | ---------------------------------------- |
| Untrainiertes Modell | UntrainedModelDirectory | Ein untrainiertes ResNet-Modell, das mit „Trainieren eines Pytorch-Modells“ verbunden werden kann. |

## <a name="next-steps"></a>Nächste Schritte

Siehe die [Sammlung der für Azure Machine Learning verfügbaren Komponenten](component-reference.md). 