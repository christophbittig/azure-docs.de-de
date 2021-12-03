---
title: Grenzwerte für die benutzerdefinierte Textklassifizierung
titleSuffix: Azure Cognitive Services
description: Erfahren Sie mehr über die Grenzwerte für Daten und Raten beim Verwenden der benutzerdefinierten Textklassifizierung.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, references_regions, ignite-fall-2021
ms.openlocfilehash: 06db585531cb3a73b291f2c8d45de18a3d298fb2
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484085"
---
# <a name="custom-text-classification-limits"></a>Grenzwerte für die benutzerdefinierte Textklassifizierung

In diesem Artikel erfahren Sie mehr über die Grenzwerte für Daten und Raten beim Verwenden der benutzerdefinierten Textklassifizierung.

## <a name="file-limits"></a>Dateigrenzwerte

* Sie können nur `.txt`-Dateien für die benutzerdefinierte Textklassifizierung verwenden. Wenn Ihre Daten in einem anderen Format vorliegen, können Sie den [Parse-Befehl von CLUtils](https://aka.ms/CognitiveServicesLanguageUtilities) verwenden, um das Dokument aufzuschlüsseln und den Text zu extrahieren.

* Alle Dateien, die in Ihren Container hochgeladen werden, müssen Daten enthalten, im Training sind keine leeren Dateien zulässig.

* Alle Dateien sollten im Stammverzeichnis Ihres Containers verfügbar sein.

* Ihr [Trainingsdataset](how-to/train-model.md#data-splits) sollte mindestens 10 Dateien und nicht mehr als 1.000.000 Dateien enthalten.

## <a name="api-limits"></a>API-Grenzwerte

**Erstellungs-API**

* Sie können maximal 10 POST-Anforderungen und 100 GET-Anforderungen pro Minute senden.

**Analyse-API**

* Sie können maximal 20 GET- oder POST-Anforderungen pro Minute senden.

* Die maximale Größe von Dateien pro Anforderung beträgt 125.000 Zeichen. Sie können bis zu 25 Dateien senden, solange deren Gesamtgröße 125.000 Zeichen nicht überschreitet.

> [!NOTE]
> Wenn Sie größere Dateien senden müssen, als der Grenzwert zulässt, können Sie den Text in kleinere Textabschnitte aufteilen, bevor Sie ihn an die API senden. Für diesen Vorgang können Sie den [chunk-Befehl von CLUtils](https://github.com/microsoft/CognitiveServicesLanguageUtilities/blob/main/CustomTextAnalytics.CLUtils/Solution/CogSLanguageUtilities.ViewLayer.CliCommands/Commands/ChunkCommand/README.md) verwenden.

## <a name="azure-resource-limits"></a>Azure-Ressourcengrenzwerte

* Sie können nur ein Speicherkonto pro Ressource verbinden. Dieser Prozess kann nicht rückgängig gemacht werden. Wenn Sie ein Speicherkonto mit Ihrer Ressource verbinden, können Sie es später nicht trennen.

* Sie können bis zu 500 Projekte pro Ressource haben.

* Projektnamen müssen innerhalb derselben Ressource eindeutig sein, sowohl für das Feature zur benutzerdefinierten Erkennung benannter Entitäten (NER) als auch für das zur benutzerdefinierten Textklassifizierung.

## <a name="regional-availability"></a>Regionale Verfügbarkeit 

Die benutzerdefinierte Textklassifizierung ist nur für ausgewählte Azure-Regionen verfügbar. Wenn Sie eine [Azure-Ressource](how-to/create-project.md) erstellen, muss sie in einer der folgenden Regionen bereitgestellt werden:
* **USA, Westen 2**
* **Europa, Westen**

## <a name="project-limits"></a>Projekteinschränkungen

* Sie können nur einen Speichercontainer für jedes Projekt verbinden. Dieser Prozess kann nicht rückgängig gemacht werden, wenn Sie einen Container mit Ihrem Projekt verbinden, können Sie ihn später nicht trennen.

* Pro Projekt kann nur 1 Tagdatei verwendet werden. Sie können die Tagdatei später nicht mehr ändern, sondern nur die Tags in Ihrem Projekt aktualisieren.

* Sie können Ihr Projekt nach der Erstellung nicht umbenennen.

* Ihr Projekt muss mindestens 10 Dateien und darf maximal 1.000.000 Dateien enthalten.

* Sie können bis zu 10 trainierte Modelle pro Projekt verwenden.

* Modellnamen müssen innerhalb desselben Projekts eindeutig sein.

* Sie können ein Modell nach der Erstellung nicht umbenennen.

* Pro Projekt können Sie jeweils nur ein Modell trainieren.

## <a name="classes-limits"></a>Klassengrenzwerte

* Ihr Projekt muss mindestens zwei Klassen aufweisen. <!-- The maximum is 200 classes. -->

* Es wird empfohlen, ca. 200 gekennzeichnete Instanzen pro Klasse zu verwenden, und Sie benötigen mindestens 10 gekennzeichnete Instanzen pro Klasse.

## <a name="naming-limits"></a>Beschränkungen für Benennungen

| attribute | Einschränkungen |
|--|--|
| Projektname |  Sie können nur Buchstaben `(a-z, A-Z)` und Zahlen `(0-9)` ohne Leerzeichen verwenden. |
| Modellname |  Sie können nur Buchstaben `(a-z, A-Z)`, Zahlen `(0-9)` und Sonderzeichen `@ # _ . , ^ \ [ ]` verwenden. |
| Entitätsnamen| Sie können nur Buchstaben `(a-z, A-Z)`, Zahlen `(0-9)` und Sonderzeichen `@ # _ . , ^ \ [ ]` verwenden. |
| Dateinamen | Sie können nur Buchstaben `(a-z, A-Z)` und Zahlen `(0-9)` ohne Leerzeichen verwenden. |
