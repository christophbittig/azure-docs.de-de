---
title: Trainieren Ihres benutzerdefinierten Klassifizierungsmodells – Azure Cognitive Services
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie Ihr Modell für die benutzerdefinierte Textklassifizierung trainieren können.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 466ddd800c70c27787a0a8ebcd8c14a4392e6c2f
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061937"
---
# <a name="how-to-train-a-text-classification-model"></a>Trainieren eines Textklassifizierungsmodells


Training ist der Prozess, bei dem das Modell anhand Ihrer [mit einem Tag versehenen Daten](tag-data.md) lernt. Nach Abschluss des Trainings können Sie [anhand der Metriken für die Modellauswertung](../how-to/view-model-evaluation.md) ermitteln, ob Sie [Ihr Modell verbessern müssen](../how-to/improve-model.md).

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie Ihr Modell trainieren, benötigen Sie Folgendes:

* [Ein erfolgreich erstelltes Projekt](create-project.md) mit einem konfigurierten Azure Blob Speicherkonto, 
* Textdaten, die in Ihr Speicherkonto [hochgeladen wurden](create-project.md#prepare-training-data).
* [Mit einem Tag versehene Daten](tag-data.md)

Weitere Informationen finden Sie im [Lebenszyklus der Anwendungsentwicklung](../overview.md#project-development-lifecycle).

## <a name="data-splits"></a>Datenaufteilungen

Bevor Sie mit dem Trainingsprozess beginnen, werden Dateien in Ihrem Dataset nach dem Zufallsprinzip in drei Gruppen unterteilt:

* Der **Trainingssatz** enthält 80 % der Dateien in Ihrem Dataset. Dies ist der Hauptdatensatz, der zum Trainieren des Modells verwendet wird.

* Der **Testsatz** enthält 20 % der Dateien, die in Ihrem Dataset verfügbar sind. Dieser Satz wird verwendet, um eine unvoreingenommene [Auswertung](../how-to/view-model-evaluation.md) des Modells vornehmen zu können. Dieser Satz wird dem Modell beim Training nicht zugeführt. Detaillierte Informationen zu richtigen und falschen Vorhersagen für diesen Satz werden nicht angezeigt, sodass Sie Ihre Trainingsdaten nicht neu anpassen und die Ergebnisse ändern.

## <a name="train-model-in-language-studio"></a>Trainieren eines Modells in Language Studio

1. Wechseln Sie in Language Studio zu Ihrer [Projektseite.](https://aka.ms/LanguageStudio)

2. Wählen Sie im Menü links **Trainieren** aus.

3. Wählen Sie zum Trainieren eines neuen Modells **Neues Modell trainieren** aus, und geben Sie den Namen des Modells im Textfeld darunter ein. Sie können **ein vorhandenes Modell überschreiben,** indem Sie diese Option auswählen und das gewünschte Modell in der Dropdownliste unten auswählen.

    :::image type="content" source="../media/train-model.png" alt-text="Erstellen eines neuen Modells" lightbox="../media/train-model.png":::

4. Wählen Sie unten auf der Seite die Schaltfläche **Trainieren** aus.

Die Zeitdauer zum Trainieren eines Modells variiert je nach Dataset und kann bis zu mehreren Stunden in Anspruch nehmen. Sie können nur jeweils ein Modell trainieren, und Sie können keine anderen Modelle erstellen oder trainieren, wenn bereits ein Modell im selben Projekt trainiert wird. 


Beachten Sie nach erfolgreichem Abschluss des Trainings Folgendes:

* [Anzeigen der Auswertungsdetails des Modells](../how-to/view-model-evaluation.md) Nach dem Trainieren des Modells wird die Modellauswertung für den [Testsatz](../how-to/train-model.md#data-splits)durchgeführt, der während des Trainings nicht in das Modell eingeführt wurde. Durch Anzeigen der Auswertung erhalten Sie einen Eindruck davon, wie sich das Modell in realen Szenarien verhält.

* [Untersuchen der Datenverteilung](../how-to/improve-model.md#examine-data-distribution-from-language-studio) Stellen Sie sicher, dass alle Klassen gut dargestellt sind und dass Sie über eine ausgewogene Datenverteilung verfügen, um sicherzustellen, dass alle Klassen angemessen dargestellt werden. Wenn eine bestimmte Klasse viel seltener als die anderen mit einem Tag versehen wird, ist diese Klasse wahrscheinlich unzureichend repräsentiert, und die meisten Vorkommen werden wahrscheinlich nicht ordnungsgemäß vom Modell während der Runtime erkannt. In diesem Fall sollten Sie prüfen, ob Sie weitere Dateien, die zu dieser Klasse gehören, zu Ihrem Datensatz hinzufügen können.

* [Verbessern der Leistung (optional)](../how-to/improve-model.md) Neben der Überarbeitung [von mit Tags versehenen Daten](tag-data.md) basierend auf der Fehleranalyse können Sie die Anzahl der Tags für Entitätstypen mit geringer Leistung erhöhen oder die Vielfalt Ihrer markierten Daten verbessern. Auf diese Weise kann Ihr Modell lernen, korrekte Vorhersagen zu treffen, und zwar unabhängig von möglichen linguistischen Phänomenen, die zu Fehlern führen.

<!-- * Define your own test set: If you are using a random split option and the resulting test set was not comprehensive enough, consider defining your own test to include a variety of data layouts and balanced tagged classes.
 -->


## <a name="next-steps"></a>Nächste Schritte

Nach Abschluss des Trainings können Sie die [Metriken zur Modellauswertung](../how-to/view-model-evaluation.md) verwenden, um optional [Ihr Modell zu verbessern](../how-to/improve-model.md). Sobald Sie mit Ihrem Modell zufrieden sind, können Sie es bereitstellen und für die [Klassifizierung von Text](call-api.md)zur Verfügung stellen.
