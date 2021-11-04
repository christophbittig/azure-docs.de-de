---
title: Verbessern der Leistung der benutzerdefinierten Erkennung benannter Entitäten (Custom Named Entity Recognition, NER)
titleSuffix: Azure Cognitive Services
description: Erfahren Sie mehr über das Verbessern eines Modells für die benutzerdefinierte Erkennung benannter Entitäten.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 5ae65097fbd4e7bdc9f4e602a64bc392e45ef75e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095117"
---
# <a name="improve-the-performance-of-custom-named-entity-recognition-ner-models"></a>Verbessern der Leistung von NER-Modellen (benutzerdefinierte Erkennung benannter Entitäten) 

Nach dem Trainieren Ihres Modells haben Sie dessen Auswertungsdetails überprüft, und Sie können damit beginnen, die Modellleistung zu verbessern. In diesem Artikel überprüfen Sie Inkonsistenzen zwischen den vorhergesagten Klassen und den vom Modell gekennzeichneten Klassen und untersuchen die Datenverteilung.

## <a name="prerequisites"></a>Voraussetzungen

* Ein erfolgreich [erstelltes Projekt](create-project.md) mit einem konfigurierten Azure Blob Storage-Konto
    * Textdaten, die in Ihr Speicherkonto [hochgeladen wurden](create-project.md#prepare-training-data)
* [Mit einem Tag versehene Daten](tag-data.md)
* Ein [erfolgreich trainiertes Modell](train-model.md)
* Überprüfen Sie die [Modellauswertungsdetails](view-model-evaluation.md), um die Leistung Ihres Modells zu bestimmen.
    * Vertrautheit mit den für die Auswertung verwendeten [Auswertungsmetriken](../concepts/evaluation-metrics.md)

Weitere Informationen finden Sie im [Lebenszyklus der Anwendungsentwicklung](../overview.md#application-development-lifecycle).


## <a name="improve-model"></a>Verbessern des Modells

Nach dem Überprüfen der [Auswertung Ihres Modells](view-model-evaluation.md) haben Sie eine Idee dahingehend entwickelt, was mit der Vorhersage Ihres Modells nicht stimmt. 

> [!NOTE]
> Dieser Leitfaden konzentriert sich auf Daten aus der [Validierungsmenge](train-model.md#data-split), die während des Trainings erstellt wurde.

### <a name="review-validation-set"></a>Überprüfen der Validierungsmenge

Mithilfe von Language Studio können Sie die Leistung Ihres Modells im Vergleich zur erwarteten Leistung überprüfen. Sie können vorhergesagte und gekennzeichnete Klassen für jedes von Ihnen trainierte Modell überprüfen.

1. Wechseln Sie in Language Studio zu Ihrer [Projektseite.](https://aka.ms/languageStudio)
    1. Suchen Sie in Language Studio den Abschnitt **Text klassifizieren**.
    2. Wählen Sie **Benutzerdefinierte Textklassifizierung** aus. 

2. Wählen Sie im Menü auf der linken Seite **Modell verbessern** aus.

3. Wählen Sie **Validierungsmenge überprüfen** aus.

4. Wählen Sie im Dropdownmenü **Modell** Ihr trainiertes Modell aus.

5. Zur einfacheren Analyse können Sie auf **Show incorrect predictions only** (Nur falsche Vorhersagen anzeigen) umschalten, um nur die Fehler anzuzeigen.

Verwenden Sie die folgenden Informationen, um das Modell zu verbessern. 

* Wenn die Entität `X` ständig als Entität `Y` identifiziert wird, bedeutet dies, dass es eine Mehrdeutigkeit zwischen diesen Entitätstypen gibt und Sie Ihr Schema überdenken müssen.

* Wenn eine komplexe Entität wiederholt nicht extrahiert wird, sollten Sie sie in einfachere Entitäten aufschlüsseln, um die Extraktion zu erleichtern.

* Wenn eine Entität vorhergesagt wird, obwohl sie in Ihren Daten nicht markiert wurde, bedeutet dies, dass Sie Ihre Tags überprüfen müssen. Stellen Sie sicher, dass alle Instanzen einer Entität in allen Dateien ordnungsgemäß markiert sind.

### <a name="examine-data-distribution"></a>Untersuchen der Datenverteilung

Indem Sie die Datenverteilung in Ihren Dateien untersuchen, können Sie entscheiden, ob eine Entität unterrepräsentiert ist. Unausgeglichene Daten treten auf, wenn Tags nicht gleichmäßig auf Ihre Entitäten verteilt werden und ein Risiko für die Leistung Ihres Modells darstellen. Wenn z. B. *Entität 1* 50 Tags besitzt, *Entität 2* aber nur 10 Tags, ist dies ein Beispiel für ein unausgeglichene Daten, bei dem *Entität 1* überrepräsentiert und *Entität 2* unterrepräsentiert ist. Das Modell ist auf die Extraktion von *Entität 1* ausgerichtet und kann *Entität 2* übersehen. Komplexere Probleme aufgrund eines Datenungleichgewichts können auftreten, wenn das Schema mehrdeutig ist. Wenn die beiden Entitäten in irgendeiner Weise ähnlich sind und *Entität 2* unterrepräsentiert ist, wird das Modell sie höchstwahrscheinlich als *Entität 1* extrahieren.

Bei der [Modellauswertung](view-model-evaluation.md) haben Entitäten, die überrepräsentiert sind, tendenziell einen höheren Abruf als andere Entitäten, während unterrepräsentierte Entitäten einen niedrigeren Abruf aufweisen.

So untersuchen Sie die Datenverteilung in Ihrem Dataset:

1. Wechseln Sie in Language Studio zu Ihrer [Projektseite.](https://aka.ms/languageStudio)
    1. Suchen Sie in Language Studio nach dem Abschnitt **Informationen extrahieren**.
    2. Wählen Sie **Extraktion benutzerdefinierter benannter Entität** aus. 
2. Wählen Sie im Menü auf der linken Seite **Modell verbessern** aus.

3. Wählen Sie **Datenverteilung untersuchen** aus.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie mit der Leistung Ihres Modells zufrieden sind, können Sie mit dem [Senden von Entitätsextraktionsanforderungen](call-api.md) mithilfe der Runtime-API beginnen.
