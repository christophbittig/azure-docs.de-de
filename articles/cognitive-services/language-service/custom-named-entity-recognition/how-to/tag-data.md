---
title: Markieren Ihrer Daten für die benutzerdefinierte Erkennung benannter Entitäten (Custom Named Entity Recognition, NER)
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie Ihre Daten für die benutzerdefinierte Erkennung benannter Entitäten markieren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 2328971a659bd4d4b147f9a708c1b5910e8ce12c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095207"
---
# <a name="tag-your-data-for-custom-named-entity-recognition-ner-in-language-studio"></a>Markieren Ihrer Daten für die benutzerdefinierte Erkennung benannter Entitäten in Language Studio

Bevor Sie Ihre benutzerdefinierten Entitätsextraktionsmodelle erstellen können, müssen Sie Daten mit Tags versehen. Wenn Ihre Daten noch nicht markiert sind, können Sie sie in Language Studio markieren. Um Ihre Daten zu markieren, müssen Sie [ein Projekt erstellen](../quickstart.md).

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie Daten markieren können, benötigen Sie Folgendes:

* Ein erfolgreich [erstelltes Projekt](create-project.md) mit einem konfigurierten Azure Blob Storage-Konto
    * Textdaten, die in Ihr Speicherkonto [hochgeladen wurden](create-project.md#prepare-training-data)

Weitere Informationen finden Sie im [Lebenszyklus der Anwendungsentwicklung](../overview.md#application-development-lifecycle).

## <a name="tag-your-data"></a>Markieren Ihrer Daten

Nachdem Trainingsdaten in Ihr Azure-Speicherkonto hochgeladen wurden, müssen Sie sie markieren, damit Ihr Modell weiß, welche Wörter den benötigten Klassen zugeordnet werden. Wenn Sie Daten in Language Studio markieren (oder Ihre Daten manuell markieren), werden diese Tags in dem [JSON-Format](../concepts/data-formats.md) gespeichert, das Ihr Modell während des Trainings verwendet.  

Beachten Sie beim Markieren Ihrer Daten Folgendes:

* **Präzise Markierung:** Markieren Sie jede Entität immer mit ihrem richtigen Typ. Nehmen Sie nur das auf, was Sie extrahieren möchten, und vermeiden Sie unnötige Daten im Tag.
* **Konsistente Markierung:** Eine Entität muss in allen Dateien über dasselbe Tag verfügen.
* **Vollständige Markierung:** Markieren Sie alle Instanzen der Entität in allen Dateien.

Die Genauigkeit, Konsistenz und Vollständigkeit Ihrer markierten Daten sind wichtige Faktoren für die Bestimmung der Modellleistung. So markieren Sie Ihre Daten:

1. Wechseln Sie in [Language Studio](https://aka.ms/custom-extraction) zur Projektseite, und wählen Sie Ihr Projekt aus.

2. Wählen Sie im Menü auf der linken Seite **Daten markieren** aus.

3. Auf der linken Seite finden Sie eine Liste aller `.txt`-Dateien, die in Ihren Projekten verfügbar sind. Sie können die Datei auswählen, mit der Sie mit dem Markieren beginnen möchten. Oder Sie können zum Navigieren die Schaltflächen **Zurück** und **Weiter** unten auf der Seite verwenden.

4. Klicken Sie in der oberen rechten Ecke auf **Entitäten hinzufügen**, um mit dem Markieren zu beginnen. Sie können entweder alle Dateien oder nur markierte Dateien anzeigen, indem Sie im Dropdownmenü **Anzeigen** die Ansicht ändern.

>[!TIP]
> * Es gibt keine Standardanzahl von Tags, die Sie benötigen. Sie sollten mit 50 Tags pro Entität beginnen. Die Anzahl der Tags, die Sie benötigen, hängt davon ab, wie unterschiedlich Ihre Entitäten sind und wie einfach sie voneinander unterschieden werden können. Dies hängt auch von Ihren Markierungen ab, die konsistent und vollständig sein sollten.

:::image type="content" source="../media/tagging-screen.png" alt-text="Screenshot: Language Studio-Bildschirm zum Markieren von Daten" lightbox="../media/tagging-screen.png":::

Wenn Sie mehrere Sprachen für Ihr Projekt aktiviert haben, können Sie über die Dropdownliste **Sprache** die Sprache der einzelnen Dokumente auswählen.

Beim Markieren werden Ihre Änderungen in regelmäßigen Abständen synchronisiert. Wenn sie noch nicht gespeichert wurden, wird oben auf der Seite eine Warnung eingeblendet. Wenn Sie manuell speichern möchten, klicken Sie oben auf der Seite auf die Schaltfläche **Tags speichern**.

## <a name="tagging-options"></a>Markierungsoptionen

Sie haben zwei Möglichkeiten, Ihr Dokument zu markieren:


|Option |BESCHREIBUNG  |
|---------|---------|
|Markieren mit einem Pinsel     | Wählen Sie das Pinselsymbol neben einer Entität in der oberen rechten Ecke des Bildschirms aus, und markieren Sie dann Wörter im Dokument, die Sie der Entität zuordnen möchten.           |
|Markieren über ein Menü    | Heben Sie das Wort hervor, das Sie als Entität markieren möchten, um ein Menü anzuzeigen. Wählen Sie das Tag aus, das Sie für diese Entität zuweisen möchten.        |

Der folgende Screenshot veranschaulicht das Markieren mit einem Pinsel.

:::image type="content" source="../media/tag-options.png" alt-text="Screenshot: Markierungsoptionen, die bei der benutzerdefinierten Erkennung benannter Entitäten angeboten werden" lightbox="../media/tag-options.png":::

## <a name="remove-tags"></a>Entfernen von Tags

So entfernen Sie ein Tag:

1. Wählen Sie die Entität aus, aus der Sie ein Tag entfernen möchten.
2. Scrollen Sie durch das angezeigte Menü, und wählen Sie **Tag entfernen** aus.

## <a name="delete-or-rename-entities"></a>Löschen oder Umbenennen von Entitäten

So löschen Sie eine Entität oder benennen sie um:

1. Wählen Sie in der oberen rechten Ecke des Menüs die Entität aus, die Sie bearbeiten möchten.
2. Klicken Sie auf die drei Punkte neben der Entität, und wählen Sie im Dropdownmenü die gewünschte Option aus.


## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Daten markiert haben, können Sie mit dem [Trainieren eines Modells](train-model.md) beginnen, das anhand Ihrer Daten lernt.
