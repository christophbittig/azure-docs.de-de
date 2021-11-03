---
title: In der benutzerdefinierten Textklassifizierung verwendete Definitionen
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie mehr über die in der benutzerdefinierten Textklassifizierung verwendeten Definitionen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 0699f4c32a62be3b07fd795357302f71ffcce8c0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131100415"
---
# <a name="terms-and-definitions-used-in-custom-text-classification"></a>In der benutzerdefinierten Textklassifizierung verwendete Begriffe und Definitionen 

Hier erfahren Sie mehr über die in der benutzerdefinierten Textklassifizierung verwendeten Definitionen. 

## <a name="project"></a>Projekt

Ein Projekt ist ein Arbeitsbereich zum Erstellen Ihrer benutzerdefinierten KI-Modelle auf der Grundlage Ihrer Daten. Auf Ihr Projekt können nur Sie und andere Personen zugreifen, die Mitwirkendenzugriff auf die verwendete Azure-Ressource haben.
Als Voraussetzung für die Erstellung eines Projekts zur benutzerdefinierten Textklassifizierung müssen Sie [Ihre Ressource mit einem Speicherkonto verbinden](how-to/create-project.md).
Im Rahmen des Projekterstellungsflows müssen Sie sie mit einem Blobcontainer verbinden, in den Sie Ihr Dataset hochgeladen haben. Ihr Projekt enthält automatisch alle Dateien vom Typ `.txt`, die in Ihrem Container verfügbar sind. Sie können mehrere Modelle in Ihrem Projekt verwenden, die alle auf demselben Dataset aufgebaut sind. Weitere Informationen finden Sie im [Artikel zu den Diensteinschränkungen](service-limits.md).

In Ihrem Projekt können Sie die folgenden Vorgänge ausführen:

* [Markieren Ihrer Daten:](./how-to/tag-data.md) Der Prozess, bei dem jede Datei Ihres Datasets mit den entsprechenden Klassen markiert wird, sodass Ihr Modell beim Trainieren lernt, wie es Ihre Dateien klassifizieren soll.
* [Erstellen und Trainieren Ihres Modells:](./how-to/train-model.md) Der wichtigste Schritt Ihres Projekts. In diesem Schritt beginnt Ihr Modell mit dem Lernen auf der Grundlage der markierten Daten. 
* [Anzeigen der Details zur Modellauswertung:](./how-to/view-model-evaluation.md) Überprüfen Sie die Modellleistung, um zu entscheiden, ob noch Verbesserungen möglich sind oder ob Sie mit den Ergebnissen zufrieden sind.
* [Verbessern des Modells (optional):](./how-to/improve-model.md) Bestimmen Sie, welche Probleme bei Ihrem Modell aufgetreten sind, und verbessern Sie die Leistung.
* [Bereitstellen des Modells:](quickstart.md?pivots=language-studio#deploy-your-model) Stellen Sie Ihr Modell zur Nutzung zur Verfügung. 
* [Testen des Modells:](quickstart.md?pivots=language-studio#test-your-model) Testen Sie Ihr Modell, und sehen Sie sich seine Leistung an.

### <a name="project-types"></a>Projekttypen

Benutzerdefinierte Textklassifizierung unterstützt zwei Arten von Projekten:

* **Klassifizierung mit einzelner Bezeichnung:** Sie können für jede Datei Ihres Datasets nur eine einzelne Klasse zuweisen. Handelt es sich beispielsweise um ein Drehbuch, kann für die Datei nur `Action`, `Thriller` oder `Romance` festgelegt werden.

* **Klassifizierung mit mehreren Bezeichnungen:** Sie können für jede Datei Ihres Datasets **mehrere** Klassen zuweisen. Handelt es sich beispielsweise um ein Drehbuch, kann für die Datei `Action` oder `Action` und `Thriller` oder `Romance` festgelegt werden.

## <a name="model"></a>Modell

Ein Modell ist ein Objekt, das für eine bestimmte Aufgabe trainiert wird. In unserem Fall ist das benutzerdefinierte Textklassifizierung. Modelle werden trainiert, indem markierte Daten zum Lernen zur Verfügung gestellt werden, damit die Modelle später zum Klassifizieren von Text verwendet werden können. Wenn Sie mit der Leistung des Modells zufrieden sind, kann es bereitgestellt werden. Dadurch wird es [zur Nutzung zur Verfügung gestellt](https://aka.ms/ct-runtime-swagger).

## <a name="class"></a>Klasse

Eine Klasse ist eine benutzerdefinierte Kategorie, die verwendet wird, um die Gesamtklassifizierung des Texts anzugeben. Sie markieren Ihre Daten mit Ihren zugewiesenen Klassen, bevor Sie sie zum Trainieren an das Modell übergeben. 
