---
title: Vorbereiten von Daten und Definieren eines Schemas
titleSuffix: Azure Cognitive Services
description: Erfahren Sie mehr über die Datenauswahl, die Datenvorbereitung und die Erstellung eines Schemas für benutzerdefinierte Klassifizierungsprojekte.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 243139219259c3b4be95010df988cf30256ca204
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484111"
---
# <a name="how-to-prepare-data-and-define-a-schema"></a>Vorbereiten von Daten und Definieren eines Schemas

Zum Erstellen eines benutzerdefinierten Klassifizierungsmodells benötigen Sie Qualitätsdaten, um es zu trainieren. In diesem Artikel wird beschrieben, wie Sie bei der Auswahl und Vorbereitung Ihrer Daten und beim Definieren eines Schemas vorgehen sollten. Ein Schema definiert die Klassen, in die Ihr Modell Ihren Text zur Laufzeit klassifizieren muss, und ist der erste Schritt bei der [Entwicklung einer benutzerdefinierten Klassifizierungsanwendung](../overview.md#project-development-lifecycle).


## <a name="data-selection"></a>Datenauswahl

Die Qualität der Daten, mit denen Sie Ihr Modell trainieren, hat großen Einfluss auf die Leistung des Modells.

* Verwenden Sie echte Daten, die den Problembereich Ihrer Domäne widerspiegeln, um Ihr Modell effektiv zu trainieren. Sie können synthetische Daten verwenden, um den anfänglichen Trainingsprozess von Modellen zu beschleunigen, aber diese unterscheiden sich wahrscheinlich von Ihren realen Daten und machen Ihr Modell bei Verwendung weniger effektiv.

* Gleichen Sie Ihre Datenverteilung so weit wie möglich aus, ohne weit von der realen Verteilung abzuweichen.

* Verwenden Sie nach Möglichkeit unterschiedliche Daten, um eine Überanpassung Ihres Modells zu verhindern. Eine geringere Vielfalt der Trainingsdaten kann dazu führen, dass Ihr Modell zweifelhafte Korrelationen lernt, die in realen Daten möglicherweise nicht vorhanden sind. 
 
* Vermeiden Sie doppelte Dateien in Ihren Daten. Doppelte Daten wirken sich negativ auf den Trainingsprozess, die Modellmetriken und die Modellleistung aus. 

* Berücksichtigen Sie, woher Ihre Daten stammen. Wenn Sie Daten von einer Person, aus einer Abteilung oder aus einem Teil Ihres Szenarios sammeln, fehlt ihnen wahrscheinlich die Diversität, deren Erlernen für Ihr Modell möglicherweise wichtig ist. 

> [!NOTE]
> Wenn Ihre Dateien in mehreren Sprachen vorliegen, wählen Sie während der [Projekterstellung](../quickstart.md) die Option **Mehrere Sprachen** aus, und legen Sie die Option **Sprache** auf die Sprache der Mehrheit Ihrer Dateien fest.

## <a name="data-preparation"></a>Datenvorbereitung

Als Voraussetzung für die Erstellung eines Projekts zur benutzerdefinierten Textklassifizierung müssen Ihre Trainingsdaten in einen Blobcontainer in Ihrem Speicherkonto hochgeladen werden. Sie können Trainingsdateien direkt aus Azure oder mithilfe des Tools Azure Storage-Explorer erstellen und hochladen. Mit dem Azure Storage-Explorer-Tool können Sie schneller mehr Daten hochladen.  

* [Erstellen und Hochladen von Dateien aus Azure](/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container)
* [Erstellen und Hochladen von Dateien mit Azure Storage-Explorer](/azure/vs-azure-tools-storage-explorer-blobs)

Sie können nur `.txt`- Dateien für benutzerdefinierten Text verwenden. Wenn Ihre Daten in einem anderen Format vorliegen, können Sie den [Parse-Befehl von CLUtils](https://github.com/microsoft/CognitiveServicesLanguageUtilities/blob/main/CustomTextAnalytics.CLUtils/Solution/CogSLanguageUtilities.ViewLayer.CliCommands/Commands/ParseCommand/README.md) verwenden, um das Dateiformat zu ändern.

 Sie können ein mit Anmerkungen versehenes Dataset oder ein nicht kommentiertes Dataset hochladen und Ihre Daten in Language Studio [kennzeichnen](../how-to/tag-data.md). 
 
## <a name="schema-design"></a>Schemaentwurf

Das Schema definiert die Klassen, in die Ihr Modell Ihren Text zur Laufzeit klassifizieren muss.

* **Überprüfen und identifizieren:** Überprüfen Sie die Dateien in Ihrem Dataset, um mit ihrer Struktur und ihrem Inhalt vertraut zu sein, und bestimmen Sie dann, wie Sie Ihre Daten klassifizieren möchten. 

    Wenn Sie beispielsweise Supporttickets klassifizieren, benötigen Sie möglicherweise die folgenden Klassen: *Anmeldeproblem*, *Hardwareproblem*, *Konnektivitätsproblem* und *Anforderung neuer Geräte*.

* **Vermeiden von Mehrdeutigkeit in Klassen**: Mehrdeutigkeit entsteht, wenn die von Ihnen angegebenen Klassen eine ähnliche Bedeutung wie andere haben. Je mehrdeutiger Ihr Schema ist, desto mehr gekennzeichnete Daten benötigen Sie möglicherweise zum Trainieren Ihres Modells.  

    Wenn Sie beispielsweise Kochrezepte klassifizieren, können diese in gewissem Umfang ähnlich sein. Zum Differenzieren zwischen *Dessertrezepten* und *Rezepten für Hauptspeisen* müssen Sie möglicherweise weitere Beispiele kennzeichnen, um Ihr Modell bei der Unterscheidung der beiden Klassen zu unterstützen. Das Vermeiden von Mehrdeutigkeit spart Zeit und führt zu besseren Ergebnissen. 

* **Daten außerhalb des Gültigkeitsbereichs**: Wenn Sie Ihr Modell in der Produktion einsetzen, erwägen Sie, eine Klasse *außerhalb des Gültigkeitsbereichs* hinzufügen, wenn Sie mit Dateien rechnen, die zu keiner Ihrer Klassen gehören. Fügen Sie anschließend Ihrem Dataset einige Dateien hinzu, um sie als *außerhalb des Gültigkeitsbereichs* zu kennzeichnen. Das Modell kann lernen, irrelevante Dateien zu erkennen und deren Tags entsprechend vorherzusagen.

## <a name="next-steps"></a>Nächste Schritte

Falls dies noch nicht geschehen ist, erstellen Sie ein benutzerdefiniertes Klassifizierungsprojekt. Wenn Sie zum ersten Mal benutzerdefinierte Klassifizierung verwenden, sollten Sie den [Schnellstart](../quickstart.md) in Betracht ziehen, um ein Beispielprojekt zu erstellen. Weitere Informationen zu den Voraussetzungen zum Erstellen eines Projekts finden Sie außerdem in den [Projektanforderungen](../how-to/create-project.md). 
