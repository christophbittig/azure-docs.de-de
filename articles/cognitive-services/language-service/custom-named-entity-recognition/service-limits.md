---
title: Dienstgrenzwerte für benutzerdefinierte Erkennung benannter Entitäten (NER)
titleSuffix: Azure Cognitive Services
description: Erfahren Sie mehr über die Daten- und Dienstgrenzwerte bei der Verwendung einer benutzerdefinierten Erkennung benannter Entitäten (NER).
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, references_regions, ignite-fall-2021
ms.openlocfilehash: 255c0f5f85f1f7b385fed82c36a799004f03ed0b
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488214"
---
# <a name="custom-named-entity-recognition-ner-service-limits"></a>Dienstgrenzwerte für benutzerdefinierte Erkennung benannter Entitäten (NER)

In diesem Artikel erfahren Sie mehr über die Grenzwerte für Daten und Dienste beim Verwenden der benutzerdefinierten NER.

## <a name="file-limits"></a>Dateigrenzwerte

* Sie können nur `.txt`- wiedergeben wird. Wenn Ihre Daten in einem anderen Format vorliegen, können Sie den [Parse-Befehl von CLUtils](https://github.com/microsoft/CognitiveServicesLanguageUtilities/blob/main/CustomTextAnalytics.CLUtils/Solution/CogSLanguageUtilities.ViewLayer.CliCommands/Commands/ParseCommand/README.md) verwenden, um das Dokument zu öffnen und den Text zu extrahieren.

* Alle Dateien, die in Ihren Container hochgeladen werden, müssen Daten enthalten. Leere Dateien sind für das Training nicht zulässig.

* Alle Dateien sollten im Stammverzeichnis Ihres Containers verfügbar sein.

* Die maximal zulässige Länge für Ihre Datei beträgt 128.000 Zeichen, was ungefähr 28.000 Wörtern oder 56 Seiten entspricht.

* Ihr [Trainingsdataset](how-to/train-model.md#data-split) sollte mindestens 10 Dateien und nicht mehr als 100.000 Dateien enthalten.


## <a name="apis-limits"></a>APIs-Grenzwerte

* Bei Verwendung der Erstellungs-API gibt es maximal 10 POST-Anforderungen und 100 GET-Anforderungen pro Minute.

* Bei Verwendung der Analyse-API gibt es maximal 20 GET- oder POST-Anforderungen pro Minute.

* Die maximale Dateigröße pro Anforderung beträgt 125.000 Zeichen. Sie können bis zu 25 Dateien senden, solange diese insgesamt nicht 125.000 Zeichen überschreiten.

> [!NOTE]
> Wenn Sie größere Dateien senden müssen, als der Grenzwert zulässt, können Sie den Text in kleinere Textabschnitte aufteilen, bevor Sie ihn an die API senden. Für diesen Vorgang können Sie den [Chunk-Befehl von CLUtils](https://github.com/microsoft/CognitiveServicesLanguageUtilities/blob/main/CustomTextAnalytics.CLUtils/Solution/CogSLanguageUtilities.ViewLayer.CliCommands/Commands/ChunkCommand/README.md) verwenden.

## <a name="azure-resource-limits"></a>Azure-Ressourcengrenzwerte

* Sie können nur ein Speicherkonto pro Ressource verbinden. Dieser Prozess kann nicht rückgängig gemacht werden. Wenn Sie ein Speicherkonto mit Ihrer Ressource verbinden, können Sie es später nicht trennen.

* Sie können bis zu 500 Projekte pro Ressource haben.

* Projektnamen müssen innerhalb derselben Ressource eindeutig sein, sowohl für die benutzerdefinierte NER als auch für die [benutzerdefinierte Textklassifizierung](../custom-classification/overview.md).

## <a name="regional-availability"></a>Regionale Verfügbarkeit 

Die benutzerdefinierte Textklassifizierung ist nur für ausgewählte Azure-Regionen verfügbar. Wenn Sie eine [Azure-Ressource](how-to/create-project.md) erstellen, muss sie in einer der folgenden Regionen bereitgestellt werden:
* **USA, Westen 2**
* **Europa, Westen**
    
## <a name="project-limits"></a>Projekteinschränkungen

* Sie können nur einen Speichercontainer für jedes Projekt verbinden. Dieser Prozess kann nicht rückgängig gemacht werden. Wenn Sie einen Container mit Ihrem Projekt verbinden, können Sie ihn später nicht mehr trennen.

* Pro Projekt kann nur 1 [Tagdatei](how-to/tag-data.md) verwendet werden. Sie können später nicht zu einer anderen Tagdatei wechseln. Sie können nur die Tags in Ihrem Projekt aktualisieren.

* Sie können Ihr Projekt nach der Erstellung nicht umbenennen.

* Ihr Projekt muss mindestens 10 gekennzeichnete Dateien und darf maximal 100.000 Dateien enthalten.

* Sie können bis zu 10 trainierte Modelle pro Projekt verwenden.

* Modellnamen müssen innerhalb desselben Projekts eindeutig sein.

* Sie können ein Modell nach der Erstellung nicht umbenennen.

* Pro Projekt können Sie jeweils nur ein Modell trainieren.

## <a name="entity-limits"></a>Grenzwerte für Entitäten

* Es wird empfohlen, dass Ihre markierte Entität nicht 10 Wörter überschreitet, wobei die maximal zulässige Anzahl 100 Zeichen beträgt.

* Ihr Projekt muss mindestens einen Entitätstyp haben, und der Höchstwert beträgt 200 Entitätstypen.

* Es wird empfohlen, ca. 200 gekennzeichnete Instanzen pro Entität zu verwenden, und Sie benötigen mindestens 10 gekennzeichnete Instanzen pro Entität.

## <a name="naming-limits"></a>Beschränkungen für Benennungen

| attribute | Einschränkungen |
|--|--|
| Projektname |  Sie können nur Buchstaben `(a-z, A-Z)` und Zahlen `(0-9)` ohne Leerzeichen verwenden. |
| Modellname |  Sie können nur Buchstaben `(a-z, A-Z)` und Zahlen `(0-9)` ohne Leerzeichen verwenden. |
| Entitätsnamen| Sie können nur Buchstaben `(a-z, A-Z)` und Zahlen `(0-9)` und Sonderzeichen `@ # _ . , ^ \ [ ]` verwenden. |
| Dateinamen | Sie können nur Buchstaben `(a-z, A-Z)` und Zahlen `(0-9)` ohne Leerzeichen verwenden. |

## <a name="next-steps"></a>Nächste Schritte

[Benutzerdefinierte NER-Übersicht](../overview.md)
