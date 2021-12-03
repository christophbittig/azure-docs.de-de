---
title: Schnelleres Beschriften von Bildern mit der intelligenten Bezeichnungserstellung
titleSuffix: Azure Cognitive Services
description: In dieser Anleitung erfahren Sie, wie Sie mit der intelligenten Bezeichnungserstellung vorgeschlagene Tags für Bilder generieren. Auf diese Weise können Sie eine große Anzahl von Bildern schneller beschriften, wenn Sie ein Custom Vision-Modell trainieren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/27/2021
ms.author: pafarley
ms.openlocfilehash: be1e97aead471012e6d0e92931d670306cb1ddf4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458302"
---
# <a name="label-images-faster-with-smart-labeler"></a>Schnelleres Beschriften von Bildern mit der intelligenten Bezeichnungserstellung

In dieser Anleitung erfahren Sie, wie Sie mit der intelligenten Bezeichnungserstellung vorgeschlagene Tags für Bilder generieren. Auf diese Weise können Sie eine große Anzahl von Images schneller beschriften, wenn Sie ein benutzerdefiniertes Vision-Modell trainieren.

Wenn Sie Bilder für ein Custom Vision-Modell kennzeichnen, verwendet der Dienst die neueste trainierte Iteration des Modells, um die Beschriftungen von nicht gekennzeichneten Bildern vorherzusagen. Anschließend werden diese Vorhersagen als vorgeschlagene Tags angezeigt, die auf dem ausgewählten Übereinstimmungsschwellenwert und der Vorhersageunsicherheit basieren. Sie können dann die Vorschläge entweder bestätigen oder ändern, um den Prozess der manuellen Kennzeichnung der Bilder für das Training zu beschleunigen.

## <a name="when-to-use-smart-labeler"></a>Szenarien für die intelligente Bezeichnungserstellung

Beachten Sie die folgenden Einschränkungen:

* Fordern Sie vorgeschlagene Tags möglichst nur für Bilder an, deren Inhalt bereits einmal trainiert wurde. Rufen Sie keine Vorschläge für ein neues Tag ab, das Sie gerade erst zu trainieren beginnen.

> [!IMPORTANT]
> Für das Feature zur intelligenten Bezeichnungserstellung gilt dasselbe [Preismodell](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) wie für normale Vorhersagen. Wenn Sie zum ersten Mal vorgeschlagene Tags für eine Reihe von Bildern auslösen, werden Ihnen dieselben Gebühren wie bei Vorhersageaufrufen berechnet. Danach speichert der Dienst die Ergebnisse für die ausgewählten Bilder 30 Tage lang in einer Datenbank, und Sie können innerhalb dieses Zeitraums jederzeit kostenlos darauf zugreifen. Nach 30 Tagen werden Sie belastet, wenn Sie die vorgeschlagenen Tags erneut anfordern.

## <a name="smart-labeler-workflow"></a>Workflow der intelligenten Bezeichnungserstellung

Die folgenden Schritte veranschaulichen die Verwendung der intelligenten Bezeichnungserstellung:

1. Laden Sie alle Ihre Trainingsbilder in Ihr Custom Vision-Projekt hoch.
1. Beschriften Sie einen Teil Ihres Datasets, und wählen Sie dieselbe Anzahl von Bildern für die einzelnen Tags aus.
    > [!TIP]
    > Achten Sie darauf, dass Sie alle Tags verwenden, für die Sie später Vorschläge erwarten.
1. Starten Sie den Trainingsprozess.
1. Wenn das Training abgeschlossen ist, navigieren Sie zur Ansicht **Nicht gekennzeichnet**, und wählen Sie die Schaltfläche **Vorgeschlagene Tags abrufen** im linken Bereich aus.
    > [!div class="mx-imgBorder"]
    > ![Die Schaltfläche für die vorgeschlagenen Tags wird unter der Registerkarte für nicht gekennzeichnete Bilder angezeigt.](./media/suggested-tags/suggested-tags-button.png)
1. Legen Sie im angezeigten Popupfenster die Anzahl der Bilder fest, für die Sie Vorschläge erhalten möchten. Sie sollten nur für einen Teil der nicht gekennzeichneten Bilder erste Tagvorschläge erhalten. Sie erhalten bessere Tagvorschläge, wenn Sie diesen Prozess durchlaufen.
1. Bestätigen Sie die vorgeschlagenen Tags und korrigieren Sie diese, wenn sie nicht richtig sind.
    > [!TIP]
    > Bilder mit vorgeschlagenen Tags werden nach ihrer Vorhersageunsicherheit sortiert (niedrigere Werte bedeuten eine höhere Zuverlässigkeit). Sie können die Sortierreihenfolge mit der Option zum **Sortieren nach Unsicherheit** ändern. Wenn Sie die Reihenfolge **absteigend** festlegen, können Sie zuerst die Vorhersagen mit hoher Unsicherheit korrigieren und dann schnell die Vorhersagen mit geringer Unsicherheit bestätigen.
    * In Bildklassifizierungsprojekten können Sie Tags stapelweise auswählen und bestätigen. Filtern Sie die Ansicht nach einem bestimmten vorgeschlagenen Tag, deaktivieren Sie die Auswahl von falsch gekennzeichneten Bildern, und bestätigen Sie dann den Rest in einem Stapel.
        > [!div class="mx-imgBorder"]
        > ![Vorgeschlagene Tags werden für IC mit Filtern im Batchmodus angezeigt.](./media/suggested-tags/ic-batch-mode.png)

        Sie können vorgeschlagene Tags auch im Einzelbildmodus verwenden, indem Sie ein Bild aus dem Katalog auswählen.

        ![Vorgeschlagene Tags werden für IC im Einzelbildmodus angezeigt.](./media/suggested-tags/ic-individual-image-mode.png)
    * In Objekterkennungsprojekten werden Batchbestätigungen nicht unterstützt, aber Sie können trotzdem nach vorgeschlagenen Tags filtern und sortieren, um eine besser organisierte Beschriftung zu ermöglichen. Miniaturansichten Ihrer nicht gekennzeichneten Bilder zeigen eine Überlagerung von Begrenzungsrahmen, die die Positionen der vorgeschlagenen Tags anzeigen. Wenn Sie keinen Filter für vorgeschlagene Tags auswählen, werden alle Ihre nicht gekennzeichneten Bilder angezeigt, ohne dass die Begrenzungsrahmen überlagert werden.
        > [!div class="mx-imgBorder"]
        > ![Vorgeschlagene Tags werden für OD mit Filtern im Batchmodus angezeigt.](./media/suggested-tags/od-batch-mode.png)

        Um Tags für die Objekterkennung zu bestätigen, müssen Sie sie auf jedes einzelne Bild im Katalog anwenden.

        ![Vorgeschlagene Tags werden für OD im Einzelbildmodus angezeigt.](./media/suggested-tags/od-individual-image-mode.png)
1. Starten Sie den Trainingsprozess erneut.
1. Wiederholen Sie die vorherigen Schritte, bis Sie mit der Qualität der Vorschläge zufrieden sind.

## <a name="next-steps"></a>Nächste Schritte

Befolgen Sie eine Schnellstartanleitung, um mit der Erstellung und dem Training eines Custom Vision-Projekts zu beginnen.

* [Erstellen einer Klassifizierung](getting-started-build-a-classifier.md)
* [Erstellen einer Objekterkennung](get-started-build-detector.md)