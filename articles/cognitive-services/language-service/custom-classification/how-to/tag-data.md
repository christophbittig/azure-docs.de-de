---
title: 'Markieren Ihrer Daten für die benutzerdefinierte Klassifizierung: Azure Cognitive Services'
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie Ihre Daten für die Verwendung mit der benutzerdefinierten Textklassifizierungs-API markieren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 390e5fabe5c23c3b258e898eab0691f88c1828c0
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057174"
---
# <a name="tag-text-data-for-training-your-model"></a>Markieren von Textdaten zum Trainieren Ihres Modells 

Bevor Sie ein benutzerdefiniertes Textklassifizierungsmodell erstellen, müssen Sie zuerst Daten markiert haben. Wenn Sie Ihre Daten noch nicht markiert haben, können Sie sie in Language Studio markieren. Markierte Daten informieren das Modell darüber, wie Text interpretiert werden kann, und werden für Training und Auswertung verwendet.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie Daten markieren können, benötigen Sie Folgendes:
* [Ein erfolgreich erstelltes Projekt](create-project.md) mit einem konfigurierten Azure Blob Speicherkonto, 
* Textdaten, die in Ihr Speicherkonto [hochgeladen wurden](create-project.md#prepare-training-data).

Weitere Informationen finden Sie im [Lebenszyklus der Anwendungsentwicklung](../overview.md#project-development-lifecycle).

<!--Tagging your data will let you [train your model](train-model.md), [evaluate it](train-model.md), and use it to [classify text](call-api.md).-->

## <a name="tag-your-data"></a>Markieren Ihrer Daten

Nachdem Trainingsdaten in Ihr Azure-Speicherkonto hochgeladen wurden, müssen Sie sie markieren, damit Ihr Modell weiß, welche Wörter den benötigten Klassen zugeordnet werden. Wenn Sie Daten in Language Studio markieren (oder Ihre Daten manuell markieren), werden diese Tags in dem [JSON-Format](../concepts/data-formats.md) gespeichert, das Ihr Modell während des Trainings verwendet. 

Beachten Sie beim Markieren Ihrer Daten Folgendes:

* Im Allgemeinen führen mehr markierte Daten zu besseren Ergebnissen, sofern die Daten ordnungsgemäß markiert sind.

* Obwohl wir etwa 50 markierte Dateien pro Klasse empfehlen, gibt es keine feste Anzahl, die garantiert, dass Ihr Modell die beste Leistung erbringt. Denn die Leistung des Modells hängt auch von der möglichen Mehrdeutigkeit in Ihrem [Schema](design-schema.md) und der Qualität Ihrer markierten Daten ab.

Markieren Sie Ihre Daten wie folgt:

1. Wechseln Sie in Language Studio zu Ihrer [Projektseite.](https://aka.ms/custom-classification)

1. Wählen Sie im Menü auf der linken Seite **Daten markieren** aus.

3. Auf der linken Seite finden Sie eine Liste aller TXT-Dateien, die in Ihren Projekten verfügbar sind. Sie können die Datei auswählen, mit der Sie mit dem Markieren beginnen möchten. Oder Sie können zum Navigieren die Schaltflächen „Zurück“ und „Weiter“ unten auf der Seite verwenden.  
    
4.  Sie können entweder alle Dateien oder nur markierte Dateien anzeigen, indem Sie im Dropdownmenü **Anzeigen** die Ansicht ändern. 

    > [!NOTE]
    > Wenn Sie für Ihr Projekt mehrere Sprachen aktiviert haben, finden Sie das zusätzliche Dropdownmenü **Sprache**. Wählen Sie die Sprache der einzelnen Dokumente aus.

5. Bevor Sie mit dem Markieren beginnen, fügen Sie Ihrem Projekt rechts oben Klassen hinzu.


    :::image type="content" source="../media/tag-1.png" alt-text="Screenshot des Bildschirms zum Markieren von Daten" lightbox="../media/tag-1.png":::

6. Beginnen Sie mit dem Markieren Ihrer Dateien.

    * **Klassifizierung mit einzelner Bezeichnung**: Ihre Datei kann nur mit einer Klasse markiert werden. Sie können dazu eines der Optionsfelder neben der Klasse aktivieren, mit der Sie diese Datei markieren möchten.

      :::image type="content" source="../media/tag-single.png" alt-text="Screenshot des Klassifizierungsmenüs für einzelne Bezeichnungen" lightbox="../media/tag-single.png":::

    * **Klassifizierung mit mehreren Bezeichnungen**: Ihre Datei kann mit mehreren Klassen markiert werden. Aktivieren Sie hierzu alle zutreffenden Kontrollkästchen neben den Klassen, mit der Sie diese Datei markieren möchten.

      :::image type="content" source="../media/tag-multi.png" alt-text="Screenshot des Menüs für die Klassifizierung mit mehreren Bezeichnungen" lightbox="../media/tag-multi.png":::

Beim Markieren werden Ihre Änderungen in regelmäßigen Abständen synchronisiert. Wenn sie noch nicht gespeichert wurden, wird oben auf der Seite eine Warnung eingeblendet. Wenn Sie manuell speichern möchten, klicken Sie oben auf der Seite auf die Schaltfläche „Tags speichern“.

## <a name="remove-tags"></a>Entfernen von Tags

Wenn Sie ein Tag entfernen möchten, deaktivieren Sie die Schaltfläche neben der Klasse.

## <a name="delete-or-classes"></a>Löschen von Klassen

So wird eine Klasse gelöscht bzw. umbenannt

1. Wählen Sie im Menü auf der rechten Seite die Klasse aus, die Sie bearbeiten möchten.
2. Klicken Sie auf die drei Punkte, und wählen Sie im Dropdownmenü die gewünschte Option aus.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Daten markiert haben, können Sie mit dem [Trainieren eines Modells](train-model.md) beginnen, das anhand Ihrer Daten lernt.
