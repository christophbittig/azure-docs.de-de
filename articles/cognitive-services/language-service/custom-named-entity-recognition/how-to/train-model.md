---
title: Trainieren Ihres Modells zur benutzerdefinierten Erkennung benannter Entitäten (NER)
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie Ihr Modell für die benutzerdefinierte Erkennung benannter Entitäten (NER) trainieren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 6fdf5ad5ed2eb8291b9a43e6004d8b18c5879b93
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095925"
---
# <a name="train-your-custom-named-entity-recognition-ner-model"></a>Trainieren Ihres Modells zur benutzerdefinierten Erkennung benannter Entitäten (NER)

Training ist der Prozess, bei dem das Modell anhand Ihrer [mit einem Tag versehenen Daten](tag-data.md) lernt. Nach Abschluss des Trainings können Sie [anhand der Metriken für die Modellauswertung](../how-to/view-model-evaluation.md) ermitteln, ob Sie [Ihr Modell verbessern müssen](../how-to/improve-model.md).

Die Zeitdauer zum Trainieren eines Modells variiert je nach Dataset und kann bis zu mehreren Stunden in Anspruch nehmen. Sie können nur jeweils ein Modell trainieren, und Sie können keine anderen Modelle erstellen oder trainieren, wenn bereits ein Modell im selben Projekt trainiert wird. 

## <a name="prerequisites"></a>Voraussetzungen

* Ein erfolgreich [erstelltes Projekt](create-project.md) mit einem konfigurierten Azure Blob Storage-Konto
    * Textdaten, die in Ihr Speicherkonto [hochgeladen wurden](create-project.md#prepare-training-data)
* [Mit einem Tag versehene Daten](tag-data.md)

Weitere Informationen finden Sie im [Lebenszyklus der Anwendungsentwicklung](../overview.md#application-development-lifecycle).

## <a name="data-split"></a>Datentrennung

Vor Beginn des Trainingsprozess, werden Dateien in Ihrem Dataset nach dem Zufallsprinzip in zwei Gruppen unterteilt:

* Der **Trainingssatz** enthält 80 % der Dateien in Ihrem Dataset. Dies ist der Hauptdatensatz, der zum Trainieren des Modells verwendet wird.

* Der **Testsatz** enthält 20 % der Dateien, die in Ihrem Dataset verfügbar sind. Dieser Satz wird verwendet, um eine unvoreingenommene [Auswertung](../how-to/view-model-evaluation.md) des Modells vornehmen zu können. Dieser Satz wird dem Modell beim Training nicht zugeführt.

## <a name="train-model-in-language-studio"></a>Trainieren eines Modells in Language Studio

1. Wechseln Sie in Language Studio zu Ihrer [Projektseite.](https://aka.ms/LanguageStudio)

2. Wählen Sie im Menü links **Trainieren** aus.

3. Wählen Sie zum Trainieren eines neuen Modells **Neues Modell trainieren** aus, und geben Sie den Namen des Modells im Textfeld darunter ein. Sie können **ein vorhandenes Modell überschreiben,** indem Sie diese Option auswählen und das gewünschte Modell in der Dropdownliste unten auswählen.

    :::image type="content" source="../media/train-model.png" alt-text="Erstellen eines neuen Modells" lightbox="../media/train-model.png":::

4. Wählen Sie unten auf der Seite die Schaltfläche **Trainieren** aus.

## <a name="next-steps"></a>Nächste Schritte

Nach Abschluss des Trainings können Sie die [Metriken zur Modellauswertung](view-model-evaluation.md) verwenden, um optional [Ihr Modell zu verbessern](improve-model.md). Sobald Sie mit Ihrem Modell zufrieden sind, können Sie es bereitstellen und für die [Extraktion von Entitäten](call-api.md) aus Texten zur Verfügung stellen.
