---
title: Testen und erneutes Trainieren eines Modells – Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie ein Image testen und dann verwenden, um Ihr Modell im Custom Vision-Dienst neu zu trainieren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/27/2021
ms.author: pafarley
ms.openlocfilehash: b326a610d3ac946d8fd610b072e882df3a4b5281
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450455"
---
# <a name="test-and-retrain-a-model-with-custom-vision-service"></a>Testen und erneutes Trainieren eines Modells mit Custom Vision Service

Nachdem Sie Ihr Modell trainiert haben, können Sie es schnell mithilfe eines lokal gespeicherten Bilds oder eines Onlinebilds testen. Im Test wird die zuletzt trainierte Iteration Ihres Modells verwendet.

## <a name="test-your-model"></a>Testen des Modells

1. Wählen Sie auf der [Custom Vision-Webseite](https://customvision.ai) Ihr Projekt aus. Wählen Sie **Quick Test** (Schnelltest) auf der rechten Seite der oberen Menüleiste aus. Diese Aktion öffnet ein Fenster mit der Bezeichnung **Quick Test** (Schnelltest).

    ![Die Schaltfläche „Quick Test“ (Schnelltest) wird in der oberen rechten Ecke des Fensters angezeigt.](./media/test-your-model/quick-test-button.png)

2. Klicken Sie im Fenster **Quick Test** (Schnelltest) auf das Feld **Submit Image** (Bild senden), und geben Sie die URL des Bilds ein, das Sie für den Test verwenden möchten. Wenn Sie stattdessen ein lokal gespeichertes Bild verwenden möchten, klicken Sie auf die Schaltfläche **Browse local files** (Lokale Dateien durchsuchen), und wählen Sie eine lokale Bilddatei aus.

    ![Abbildung der Seite „Submit image“ (Bild senden)](./media/test-your-model/submit-image.png)

Das Bild, das Sie auswählen, wird in der Mitte der Seite angezeigt. Anschließend werden die Ergebnisse unterhalb des Bilds in Form einer Tabelle mit zwei Spalten angezeigt, die die Beschriftungen **Tags** (Kategorien) und **Confidence** (Verlässlichkeit) tragen. Nachdem Sie die Ergebnisse angesehen haben, können Sie das Fenster **Quick Test** (Schnelltest) schließen.

Sie können dieses Testbild jetzt Ihrem Modell hinzufügen und das Modell dann erneut trainieren.

## <a name="use-the-predicted-image-for-training"></a>Verwenden des vorhergesagten Bilds für das Training

Führen Sie die folgenden Schritte aus, um das zuvor für das Training gesendete Bild zu verwenden:

1. Um die an die Klassifizierung gesendeten Bilder anzuzeigen, öffnen Sie die [Custom Vision-Webseite](https://customvision.ai) und wählen die Registerkarte __Predictions__ (Vorhersagen) aus.

    ![Abbildung der Registerkarte „Predictions“ (Vorhersagen)](./media/test-your-model/predictions-tab.png)

    > [!TIP]
    > In der Standardansicht werden Bilder aus der aktuellen Iteration angezeigt. Sie können das Dropdownfeld __Iteration__ verwenden, um Bilder anzuzeigen, die während früherer Iterationen gesendet wurden.

2. Zeigen Sie mit dem Mauszeiger auf ein Bild, um die Kategorien anzuzeigen, die von der Klassifizierung vorhergesagt wurden.

    > [!TIP]
    > Die Bilder sind so nach einer Rangfolge geordnet, dass die Bilder, die für die Klassifizierung die höchste Bewertung erzielen, oben angezeigt werden. Wenn Sie eine andere Sortierung auswählen möchten, können Sie den Abschnitt __Sort__ (Sortieren) verwenden.

    Um Ihren Trainingsdaten ein Bild hinzuzufügen, wählen Sie das Bild, die Kategorie und dann die Option __Save and close__ (Speichern und schließen) aus. Das Bild wird aus dem Bereich __Predictions__ (Vorhersagen) entfernt und den Trainingsbildern hinzugefügt. Sie können es anzeigen, indem Sie die Registerkarte __Training Images__ (Trainingsbilder) auswählen.

    ![Bild der Kategorienseite](./media/test-your-model/tag-image.png)

3. Verwenden Sie die Schaltfläche __Train__ (Trainieren), um die Klassifizierung erneut zu trainieren.

## <a name="next-steps"></a>Nächste Schritte

[Verbessern Ihres Modells](getting-started-improving-your-classifier.md)
