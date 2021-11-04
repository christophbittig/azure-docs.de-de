---
title: Trainieren und Auswerten von Modellen in Conversational Language Understanding
titleSuffix: Azure Cognitive Services
description: Verwenden Sie diesen Artikel, um ein Modell zu trainieren und dessen Auswertungsdetails anzuzeigen, um Verbesserungen vorzunehmen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: a5f4ccb11b759f735618ef991e7dfd85fd071035
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131100331"
---
# <a name="train-and-evaluate-models"></a>Trainieren und Auswerten eines Modells

Nachdem Sie das [Kennzeichnen Ihrer Äußerungen](./tag-utterances.md)abgeschlossen haben, können Sie Ihr Modell trainieren. Training ist die Konvertierung des aktuellen Zustands der Trainingsdaten Ihres Projekts, um ein Modell zu erstellen, das für Vorhersagen verwendet werden kann. Bei jedem Training müssen Sie Ihre Trainingsinstanz benennen. 

Sie können mehrere Modelle innerhalb desselben Projekts erstellen und trainieren. Wenn Sie jedoch ein bestimmtes Modell erneut trainieren, wird der letzte Zustand überschrieben.

Die Trainingszeiten können zwischen einigen Sekunden bei Projekten mit Orchestrierungsworkflows und bis zu ein paar Stunden reichen, wenn Sie die [maximale Anzahl](../service-limits.md) von Äußerungen erreichen. Vor dem Training haben Sie die Möglichkeit, die Auswertung zu aktivieren, mit der Sie die Leistung Ihres Modells anzeigen können. 

## <a name="train-model"></a>Trainieren des Modells

Geben Sie einen neuen Modellnamen ein, oder wählen Sie ein vorhandenes Modell aus der Dropdownliste für den **Modellnamen** aus. Drücken Sie die EINGABETASTE, nachdem Sie einen Modellnamen hinzugefügt haben. Wählen Sie aus, ob Sie Ihr Modell auswerten möchten, indem Sie den Umschalter **Run evaluation with training** (Auswertung mit Training ausführen) ändern. Falls aktiviert, werden Ihre gekennzeichneten Äußerungen in drei Teile aufgeteilt: 80 % für das Training, 10 % für die Überprüfung und 10 % für den Test. Anschließend können Sie die Auswertungsergebnisse des Modells anzeigen.

:::image type="content" source="../media/train-model.png" alt-text="Screenshot: Seite „Modell trainieren“ für Conversational Language Understanding-Projekte." lightbox="../media/train-model.png":::

Klicken Sie auf die Schaltfläche **Trainieren**, und warten Sie, bis das Training abgeschlossen ist. Der Trainingsstatus Ihres Modells wird auf der Seite mit den Modelldetails angezeigt.

## <a name="evaluate-model"></a>Bewerten eines Modells

Nachdem das Modelltraining abgeschlossen ist, können Sie Ihre Modelldetails anzeigen und sehen, wie gut es mit dem Testsatz abschneidet, wenn Sie die Auswertung im Trainingsschritt aktiviert haben. Das Beobachten der Leistung Ihres Modells wird als Auswertung bezeichnet. Der Testsatz besteht aus 20 % Ihrer Äußerungen, die vor dem Training nach dem Zufallsprinzip aufgeteilt werden. Der Testsatz besteht aus Daten, die während des Trainingsprozesses nicht in das Modell eingeführt wurden. Damit der Auswertungsprozess abgeschlossen werden kann, müssen mindestens 10 Äußerungen in Ihrem Trainingssatz vorhanden sein.

Auf der Seite **Modelldetails anzeigen** können Sie alle Ihre Modelle mit ihrem aktuellen Trainingsstatus und dem Datum des letzten Trainings anzeigen.

:::image type="content" source="../media/model-page-1.png" alt-text="Screenshot: Seite „Modelldetails“ für Conversational Language Understanding-Projekte." lightbox="../media/model-page-1.png":::

* Klicken Sie auf den Modellnamen, um weitere Details anzuzeigen. Auf einen Modellnamen kann nur geklickt werden, wenn Sie die Auswertung vorher aktiviert haben. 
* Im Abschnitt **Übersicht** finden Sie die Makrogenauigkeit, den Abruf und den F1-Score für die kollektiven Absichten oder Entitäten, je nachdem, welche Option Sie ausgewählt haben. 
* Auf den Registerkarten **Absichten** und **Entitäten** finden Sie die Mikrogenauigkeit, den Recall und den F1-Score für jede Absicht oder Entität separat.

> [!NOTE]
> Wenn Sie hier keine der Absichten oder Entitäten sehen, die Sie in Ihrem Modell verwenden, liegt das daran, dass sie in keiner der Äußerungen enthalten waren, die für den Testsatz verwendet wurden.

Sie können die [Konfusionsmatrix](../concepts/evaluation-metrics.md#confusion-matrix) für Absichten und Entitäten anzeigen, indem Sie auf die Registerkarte **Test set confusion matrix** (Konfusionsmatrix für den Testsatz) oben auf dem Bildschirm klicken. 

## <a name="next-steps"></a>Nächste Schritte
* [Metriken für die Modellauswertung](../concepts/evaluation-metrics.md)
* [Bereitstellen und Abfragen des Modells](./deploy-query-model.md)
