---
title: DenseNet
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mit der DenseNet-Komponente im Azure Machine Learning-Designer ein Bildklassifizierungsmodell mithilfe des DenseNet-Algorithmus erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: 919378cc51128a946d32698a06e2330c57eb1147
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566406"
---
# <a name="densenet"></a>DenseNet

In diesem Artikel wird beschrieben, wie Sie mit der **DenseNet**-Komponente im Azure Machine Learning-Designer ein Bildklassifizierungsmodell mithilfe des DenseNet-Algorithmus erstellen.  

Dieser Klassifizierungsalgorithmus ist eine überwachte Lernmethode und erfordert ein Imageverzeichnis mit Bezeichnungen. 

> [!NOTE]
> Diese Komponente unterstützt keine beschrifteten Datasets, die von *Data Labeling* (Datenbeschriftung) im Studio erzeugt wurden, sondern nur beschriftete Bildverzeichnisse, die von der Komponente [Convert to Image Directory](convert-to-image-directory.md) (in Bildverzeichnis konvertieren) erzeugt wurden. 

Sie können das Modell trainieren, indem Sie das Modell und das Bildverzeichnis mit Bezeichnungen als Eingaben für [Trainieren eines PyTorch-Modells](train-pytorch-model.md) angeben. Mit dem trainierten Modell können Sie anschließend Werte für neue Eingabebeispiele mithilfe von [Bewerten eines Bildmodells](score-image-model.md) vorhersagen.

### <a name="more-about-densenet"></a>Weitere Informationen zu DenseNet

Weitere Informationen zu DenseNet finden Sie in der Forschungsarbeit [Densely Connected Convolutional Networks](https://arxiv.org/abs/1608.06993).

## <a name="how-to-configure-densenet"></a>Konfigurieren von DenseNet

1.  Fügen Sie die Komponente **DenseNet** zu Ihrer Pipeline im Designer hinzu.  

2.  Geben Sie als **Modellnamen** den Namen einer bestimmten DenseNet-Struktur ein. Es stehen verschiedene unterstützte Strukturen zur Wahl, „densenet121“, „densenet161“, „densenet169“, „densenet201“.

3.  Geben Sie unter **Vortrainiert** an, ob ein in ImageNet vortrainiertes Modell verwendet werden soll. Bei Auswahl dieser Option können Sie das Modell basierend auf dem ausgewählten vortrainierten Modell optimieren. Wird die Auswahl aufgehoben, können Sie von Grund auf neu trainieren.

4.  Geben Sie unter **Memory efficient** (arbeitsspeichereffizient) an, ob Prüfpunktausführung verwendet werden soll, die viel arbeitsspeichereffizienter, aber langsamer ist. Weitere Informationen finden Sie in der Forschungsarbeit [Memory-Efficient Implementation of DenseNets](https://arxiv.org/pdf/1707.06990.pdf).

5.  Verbinden Sie die Ausgabe der **DenseNet**-Komponente, der Trainings- und der Validierungs-Bilddataset-Komponente mit dem [Train Pytorch Model](train-pytorch-model.md). 

6. Übermitteln Sie die Pipeline.


## <a name="results"></a>Ergebnisse

Wenn Sie das Modell zur Bewertung verwenden möchten, verbinden Sie nach Abschluss der Pipelineausführung [Train Pytorch Model](train-pytorch-model.md) (Trainieren eines Pytorch-Modells) mit [Score Image Model](score-image-model.md) (Bewerten eines Bildmodells), um Werte für neue Eingabebeispiele vorherzusagen.

## <a name="technical-notes"></a>Technische Hinweise  

###  <a name="component-parameters"></a>Komponentenparameter  

| Name             | Range | type    | Standard     | BESCHREIBUNG                              |
| ---------------- | ----- | ------- | ----------- | ---------------------------------------- |
| Modellname       | Any   | Mode    | densenet201 | Name einer bestimmten DenseNet-Struktur     |
| Vortrainiert       | Any   | Boolean | True        | Gibt an, ob ein in ImageNet vortrainiertes Modell verwendet werden soll |
| Arbeitsspeichereffizient | Any   | Boolean | False       | Ob Prüfpunktausführung verwendet werden soll, die viel arbeitsspeichereffizienter, aber langsamer ist |

###  <a name="output"></a>Output  

| Name            | type                    | BESCHREIBUNG                              |
| --------------- | ----------------------- | ---------------------------------------- |
| Untrainiertes Modell | UntrainedModelDirectory | Ein untrainiertes DenseNet-Modell, das mit „Trainieren eines Pytorch-Modells“ verbunden werden kann. |

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Sammlung der verfügbaren Komponenten](component-reference.md) für Azure Machine Learning an. 
