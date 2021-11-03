---
title: Anzeigen der Auswertung für ein benutzerdefiniertes Modell für die Erkennung benannter Entitäten (NER)
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie die Auswertung eines benutzerdefinierten Modells für die Erkennung benannter Entitäten (NER) anzeigen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 62724137bd02f8c3ff58665ad92a4346eddc0b53
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095923"
---
# <a name="view-the-models-evaluation-and-details"></a>Anzeigen der Auswertung und Details des Modells

Nachdem das Training des Modells abgeschlossen ist, können Sie die Modelldetails anzeigen und sehen, wie gut es für den Testsatz funktioniert. Dieser enthält 10 % Ihrer Daten nach dem Zufallsprinzip und wird während des [Trainings](train-model.md#data-split) erstellt. Der Testsatz besteht aus Daten, die während des Trainingsprozesses nicht in das Modell eingeführt wurden. Damit der Auswertungsprozess abgeschlossen werden kann, müssen mindestens 10 Dateien in Ihrem Trainingssatz vorhanden sein. Außerdem benötigen Sie ein [benutzerdefiniertes NER-Projekt](../quickstart.md) mit einem [trainierten Modell](train-model.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein erfolgreich [erstelltes Projekt](create-project.md) mit einem konfigurierten Azure Blob Storage-Konto
    * Textdaten, die in Ihr Speicherkonto [hochgeladen wurden](create-project.md#prepare-training-data)
* [Mit einem Tag versehene Daten](tag-data.md)
* Ein [erfolgreich trainiertes Modell](train-model.md)

Weitere Informationen finden Sie im [Lebenszyklus der Anwendungsentwicklung](../overview.md#application-development-lifecycle).

## <a name="view-the-models-evaluation-details"></a>Anzeigen der Auswertungsdetails des Modells

1. Wechseln Sie in Language Studio zu Ihrer [Projektseite.](https://aka.ms/languageStudio)
    1. Suchen Sie in Language Studio nach dem Abschnitt **Informationen extrahieren**.
    2. Wählen Sie **Extraktion benutzerdefinierter benannter Entität** aus.

2. Wählen Sie im Menü auf der linken Seite des Bildschirms die Option **Modelldetails anzeigen** aus.

3. Zeigen Sie den Modelltrainingsstatus in der Spalte **Status** und die F1-Bewertung für das Modell in der **F1-Bewertungsspalte** an. Klicken Sie auf den Modellnamen, um weitere Details anzuzeigen.

4. Sie finden die Auswertungsmetriken **auf Modellebene** unter **Übersicht** und die Auswertungsmetriken **auf Entitätsebene** unter **Entitätsleistungsmetriken**. Die Konfusionsmatrix für das Modell befindet sich unter **Testsatz-Konfusionsmatrix**.
    
    > [!NOTE]
    > Wenn Sie nicht alle hier angezeigten Entitäten finden, liegt dies daran, dass sie nicht in den Dateien im Testsatz enthalten waren.

    :::image type="content" source="../media/model-details.png" alt-text="Screenshot der Modellleistungsmetriken in Language Studio" lightbox="../media/model-details.png":::

## <a name="next-steps"></a>Nächste Schritte

* Nachdem Sie die Modellauswertung überprüft haben, können Sie mit der [Verbesserung Ihres Modells](improve-model.md) beginnen.
* Hier erfahren Sie mehr über die [Metriken, die in der Auswertung verwendet werden](../concepts/evaluation-metrics.md). 
