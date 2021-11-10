---
title: Verbessern der Leistung eines Modells für benutzerdefinierte Textklassifizierung
titleSuffix: Azure Cognitive Services
description: Erfahren Sie mehr über das Verbessern eines Modells für die benutzerdefinierte Textklassifizierung.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 73513e90f37a3d003351604d119612306ba66277
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132062736"
---
# <a name="improve-model-performance"></a>Verbessern der Modellleistung

Nachdem Sie Ihr Modell trainiert und seine Auswertungsdetails überprüft haben, können Sie entscheiden, ob Sie die Leistung Ihres Modells verbessern müssen. In diesem Artikel überprüfen Sie Inkonsistenzen zwischen den vorhergesagten Klassen und den vom Modell gekennzeichneten Klassen und untersuchen die Datenverteilung.

## <a name="prerequisites"></a>Voraussetzungen

Zum optionalen Verbessern eines Modells müssen diese Voraussetzungen gegeben sein:

* [Ein Projekt für die benutzerdefinierte Klassifizierung](create-project.md) mit einem konfigurierten Azure Blob Storage-Konto 
* Textdaten, die in Ihr Speicherkonto [hochgeladen wurden](create-project.md#prepare-training-data).
* [Gekennzeichnete Daten](tag-data.md) zum erfolgreichen [Trainieren eines Modells](train-model.md)
* Überprüfen Sie die [Modellauswertungsdetails](view-model-evaluation.md), um die Leistung Ihres Modells zu bestimmen.
* Vertrautheit mit den für die Auswertung verwendeten [Auswertungsmetriken](../concepts/evaluation.md)

Weitere Informationen finden Sie im [Lebenszyklus der Anwendungsentwicklung](../overview.md#project-development-lifecycle).

## <a name="review-test-set-predictions"></a>Überprüfen von Testmengenvorhersagen

Mithilfe von Language Studio können Sie die Leistung Ihres Modells im Vergleich zur erwarteten Leistung überprüfen. Sie können vorhergesagte und gekennzeichnete Klassen für jedes von Ihnen trainierte Modell nebeneinander überprüfen.

1. Navigieren Sie zu Ihrer Projektseite in [Language Studio](https://aka.ms/languageStudio)).
    1. Suchen Sie in Language Studio den Abschnitt **Text klassifizieren**.
    2. Wählen Sie **Benutzerdefinierte Textklassifizierung** aus. 

2. Wählen Sie im Menü auf der linken Seite **Modell verbessern** aus.

3. Wählen Sie **Review test set** (Testmenge überprüfen) aus.

4. Wählen Sie im Dropdownmenü **Modell** Ihr trainiertes Modell aus.

5. Zur einfacheren Analyse können Sie auf **Show incorrect predictions only** (Nur falsche Vorhersagen anzeigen) umschalten, um nur die Fehler anzuzeigen.

    :::image type="content" source="../media/review-validation-set.png" alt-text="Überprüfen der Validierungsmenge" lightbox="../media/review-validation-set.png":::

6. Wenn eine Datei, die zur Klasse `X` gehören soll, ständig als Klasse `Y` klassifiziert wird, bedeutet dies, dass es Mehrdeutigkeit zwischen diesen Klassen gibt, und Sie ihr Schema überdenken müssen.

## <a name="examine-data-distribution-from-language-studio"></a>Untersuchen der Datenverteilung in Language Studio

Indem Sie die Datenverteilung in Ihren Dateien untersuchen, können Sie entscheiden, ob eine Klasse unterrepräsentiert ist. Ein Datenungleichgewicht tritt auf, wenn die für das Training verwendeten Dateien nicht gleichmäßig auf die Klassen verteilt werden, und stellt eine Gefahr für die Leistung eines Modells dar. Wenn beispielsweise *Klasse 1* 50 gekennzeichnete Dateien aufweist, während *Klasse 2* nur 10 gekennzeichnete Dateien enthält, besteht ein Datenungleichgewicht, bei dem *Klasse 1* überrepräsentiert und *Klasse 2* unterrepräsentiert ist. 

In diesem Fall hat Ihr Modell die Tendenz, Ihre Datei als zu *Klasse 1* zugehörig anzusehen, und übersieht *Klasse 2* möglicherweise. Ein komplexeres Problem aufgrund eines Datenungleichgewichts kann auftreten, wenn das Schema mehrdeutig ist. Wenn zwischen den zwei Klassen keine klare Unterscheidung besteht und *Klasse 2* unterrepräsentiert ist, wird das Modell einen Text mit hoher Wahrscheinlichkeit als zu *Klasse 1* gehörig erkennen.

In den [Auswertungsmetriken](../concepts/evaluation.md) weist eine überrepräsentierte Klasse tendenziell mehr Abrufe auf als andere Klassen, während unterrepräsentierte Klassen weniger Abrufe aufweisen.

So untersuchen Sie die Datenverteilung in Ihrem Dataset:

1. Wechseln Sie in Language Studio zu Ihrer [Projektseite.](https://aka.ms/languageStudio)
    1. Wechseln Sie in Language Studio zum Abschnitt **Text klassifizieren**.
    2. Wählen Sie **Benutzerdefinierte Textklassifizierung** aus. 

2. Wählen Sie im Menü auf der linken Seite **Modell verbessern** aus.

3. Wählen Sie **Datenverteilung untersuchen** aus.

    :::image type="content" source="../media/examine-data-distribution.png" alt-text="Untersuchen der Datenverteilung" lightbox="../media/examine-data-distribution.png":::

4. Wechseln Sie zurück zur Seite **Tag data** (Daten kennzeichnen), und nehmen Sie Anpassungen vor, nachdem Sie eine Vorstellung davon erhalten haben, in welcher Weise Sie Ihre Daten anders kennzeichnen sollten.

## <a name="next-steps"></a>Nächste Schritte

* Wenn Sie mit der Leistung Ihres Modells zufrieden sind, können Sie mit dem [Senden von Textklassifizierungsanforderungen](call-api.md) mithilfe der Runtime-API beginnen.
