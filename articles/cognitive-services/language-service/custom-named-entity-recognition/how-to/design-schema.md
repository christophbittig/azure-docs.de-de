---
title: Vorbereiten von Daten und Entwerfen eines Schemas für benutzerdefinierte NER
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie Daten auswählen und vorbereiten, um Projekte für benutzerdefinierte NER erfolgreich zu erstellen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 35f57b29f20e4b9ca0105c2395a34376e25a8c8a
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132489112"
---
# <a name="how-to-prepare-data-and-define-a-schema-for-custom-ner"></a>Vorbereiten von Daten und Definieren eines Schemas für benutzerdefinierte NER

Um ein benutzerdefiniertes NER-Modell zu erstellen, benötigen Sie Qualitätsdaten, um es zu trainieren. In diesem Artikel wird beschrieben, wie Sie bei der Auswahl und Vorbereitung Ihrer Daten und beim Definieren eines Schemas vorgehen sollten. Das Schema definiert die Entitätstypen/Kategorien, die Ihr Modell zur Laufzeit aus Text extrahieren soll, und ist der erste Schritt bei der [Entwicklung eines Entitätsextraktionsmodells](../overview.md#application-development-lifecycle).

## <a name="schema-design"></a>Schemaentwurf

Das Schema definiert die Entitätstypen/Kategorien, die Ihr Modell zur Laufzeit aus Text extrahieren soll.

* Überprüfen Sie die Dateien in Ihrem Dataset, um sich mit ihrem Format und ihrer Struktur vertraut zu machen.

* Bestimmen Sie die [Entitäten](../glossary.md#entity), die Sie aus den Daten extrahieren möchten.

    Wenn Sie beispielsweise Entitäten aus Support-E-Mails extrahieren, kann es sinnvoll sein, „Kundenname“, „Produktname“, „Problem des Kunden“, „Anforderungsdatum“ und „Kontaktinformationen“ zu extrahieren.

* Vermeiden Sie Mehrdeutigkeiten bei Entitätstypen.

    **Mehrdeutigkeit** tritt auf, wenn die von Ihnen ausgewählten Typen einander ähneln. Je mehrdeutiger Ihr Schema ist, desto mehr gekennzeichnete Daten benötigen Sie zum Trainieren Ihres Modells.

    Wenn Sie z. B. Daten aus einem Vertrag extrahieren, müssen Sie weitere Beispiele hinzufügen, um „Name der Ersten Partei“ und „Name der zweiten Partei“ zu extrahieren, um Mehrdeutigkeiten zu vermeiden, da die Namen beider Parteien ähnlich aussehen. Das Vermeiden von Mehrdeutigkeit spart Zeit, Aufwand und führt zu besseren Ergebnissen.

* Vermeiden Sie komplexe Entitäten. Komplexe Entitäten können im Text schwierig zu ermitteln sein. Erwägen Sie eine Aufteilung in mehrere Entitäten.

    Beispielsweise hätte das Modell Schwierigkeiten beim Extrahieren von „Adresse“, wenn diese nicht in kleinere Entitäten aufgeschlüsselt würde. Adressen treten in so vielen verschiedenen Varianten auf, dass eine große Anzahl gekennzeichneter Entitäten erforderlich wäre, um dem Modell das Extrahieren einer Adresse im Ganzen beizubringen, ohne sie aufzubrechen. Wenn Sie jedoch „Adresse“ durch „Straßenname“, „Postfach“, „Stadt“, „Bundesland“ und „Postleitzahl“ ersetzen, benötigt das Modell weniger Tags pro Entität.

## <a name="data-selection"></a>Datenauswahl

Die Qualität der Daten, mit denen Sie Ihr Modell trainieren, hat großen Einfluss auf die Leistung des Modells.

* Verwenden Sie echte Daten, die den Problembereich Ihrer Domäne widerspiegeln, um Ihr Modell effektiv zu trainieren. Sie können synthetische Daten verwenden, um den anfänglichen Trainingsprozess von Modellen zu beschleunigen, aber diese unterscheiden sich wahrscheinlich von Ihren realen Daten und machen Ihr Modell bei Verwendung weniger effektiv.

* Gleichen Sie Ihre Datenverteilung so weit wie möglich aus, ohne weit von der realen Verteilung abzuweichen. Wenn Sie Ihr Modell z. B. dafür trainieren, Entitäten aus Rechtsdokumenten zu extrahieren, die in vielen verschiedenen Formaten und Sprachen vorliegen können, sollten Sie Beispiele bereitstellen, um die in der Praxis erwartete Vielfalt beispielhaft wiederzugeben.

* Verwenden Sie nach Möglichkeit unterschiedliche Daten, um eine Überanpassung Ihres Modells zu verhindern. Eine geringere Vielfalt der Trainingsdaten kann dazu führen, dass Ihr Modell zweifelhafte Korrelationen lernt, die in realen Daten möglicherweise nicht vorhanden sind. 
 
* Vermeiden Sie doppelte Dateien in Ihren Daten. Doppelte Daten wirken sich negativ auf den Trainingsprozess, die Modellmetriken und die Modellleistung aus. 

* Berücksichtigen Sie, woher Ihre Daten stammen. Wenn Sie Daten von einer Person, aus einer Abteilung oder aus einem Teil Ihres Szenarios sammeln, fehlt ihnen wahrscheinlich die Diversität, deren Erlernen für Ihr Modell möglicherweise wichtig ist. 

> [!NOTE]
> Wenn Ihre Dateien in mehreren Sprachen vorliegen, wählen Sie während der [Projekterstellung](../quickstart.md) die Option **Mehrere Sprachen** aus, und legen Sie die Option **Sprache** auf die Sprache der Mehrheit Ihrer Dateien fest.

## <a name="data-preparation"></a>Datenvorbereitung

Als Voraussetzung für die Erstellung eines Projekts müssen Ihre Trainingsdaten in einen Blobcontainer in Ihrem Speicherkonto hochgeladen werden. Sie können Trainingsdateien direkt aus Azure oder mithilfe des Tools Azure Storage-Explorer erstellen und hochladen. Mit dem Azure Storage-Explorer-Tool können Sie schneller mehr Daten hochladen.  

* [Erstellen und Hochladen von Dateien aus Azure](/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container)
* [Erstellen und Hochladen von Dateien mit Azure Storage-Explorer](/azure/vs-azure-tools-storage-explorer-blobs)

Sie können nur `.txt`-Dateien verwenden. Wenn Ihre Daten in einem anderen Format vorliegen, können Sie den [Parse-Befehl von CLUtils](https://github.com/microsoft/CognitiveServicesLanguageUtilities/blob/main/CustomTextAnalytics.CLUtils/Solution/CogSLanguageUtilities.ViewLayer.CliCommands/Commands/ParseCommand/README.md) verwenden, um das Dateiformat zu ändern.

 Sie können ein mit Anmerkungen versehenes Dataset oder ein nicht kommentiertes Dataset hochladen und Ihre Daten in Language Studio [kennzeichnen](../how-to/tag-data.md). 
 
## <a name="next-steps"></a>Nächste Schritte

Falls Sie dies noch nicht getan haben, erstellen Sie ein benutzerdefiniertes NER-Projekt. Wenn Sie zum ersten Mal benutzerdefinierte NER verwenden, sollten Sie den [Schnellstart](../quickstart.md) in Betracht ziehen, um ein Beispielprojekt zu erstellen. Weitere Informationen zu den Voraussetzungen zum Erstellen eines Projekts finden Sie außerdem im [Anleitungsartikel](../how-to/create-project.md). 
