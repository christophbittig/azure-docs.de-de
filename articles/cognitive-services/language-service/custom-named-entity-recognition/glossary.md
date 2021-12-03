---
title: Definitionen und Benennungen, die für die benutzerdefinierte benannte Entitätserkennung verwendet werden
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie mehr über die Definitionen und Benennungen, die beim Verwenden der benutzerdefinierten benannten Entitätserkennung (NER) auftreten können.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: c57d4efffc207759b8d3da343bda397ee885dc0f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131439080"
---
# <a name="custom-named-entity-recognition-ner-definitions-and-terms"></a>Definitionen und Benennungen zur benutzerdefinierten benannten Entitätserkennung (NER)

In diesem Artikel erfahren Sie mehr über einige Definitionen und Benennungen, die beim Verwenden der benutzerdefinierten NER auftreten können.

## <a name="project"></a>Projekt

Ein Projekt ist ein Arbeitsbereich zum Erstellen Ihrer benutzerdefinierten KI-Modelle auf der Grundlage Ihrer Daten. Auf Ihr Projekt können nur Sie und andere Personen zugreifen, die Mitwirkendenzugriff auf die von Ihnen genutzte Azure-Ressource haben.
Als Voraussetzung für das Erstellen eines benutzerdefinierten Entitätsextraktionsprojekts müssen Sie Ihre Ressource mit Ihrem Dataset mit einem Speicherkonto verbinden, wenn Sie [ein neues Projekt erstellen](how-to/create-project.md). Ihr Projekt enthält automatisch alle Dateien vom Typ `.txt`, die in Ihrem Container verfügbar sind.

In Ihrem Projekt können Sie die folgenden Vorgänge ausführen:

* **Markieren Ihrer Daten**: Der Prozess des Markierens Ihrer Daten, der bewirkt, dass Ihr Modell beim Trainieren lernt, was Sie extrahieren möchten.
* **Erstellen und Trainieren Ihres Modells**: Der wichtigste Schritt Ihres Projekts, bei dem Ihr Modell mit dem Lernen anhand Ihrer markierten Daten beginnt. 
* **Anzeigen der Details zur Modellauswertung**: Überprüfen Sie die Modellleistung, um zu entscheiden, ob noch Verbesserungen möglich sind oder ob Sie mit den Ergebnissen zufrieden sind.
* **Verbessern des Modells**: Wenn Sie wissen, was mit Ihrem Modell schief gelaufen ist und wie Sie die Leistung verbessern können. 
* **Bereitstellen des Modells**: Stellen Sie Ihr Modell zur Nutzung bereit. 
* **Testen Ihres Modells**: Testen Sie Ihr Modell in einem Dataset.

## <a name="model"></a>Modell

Ein Modell ist ein Objekt, das für eine bestimmte Aufgabe trainiert wurde, in diesem Fall die benutzerdefinierte Entitätserkennung.

* Das **Modelltraining** ist der Prozess, bei dem Sie Ihrem Modell beibringen, was basierend auf Ihren markierten Daten extrahiert werden soll.
* Die **Modellauswertung** ist der Prozess, der direkt nach dem Training ausgeführt wird, um zu erfahren, wie gut Ihr Modell funktioniert.
* Die **Modellimplementierung** ist der Prozess, mit dem das Modell zur Verwendung bereitgestellt wird.

## <a name="entity"></a>Entität

Eine Entität ist eine Textspanne, die einen bestimmten Informationstyp angibt. Eine Textspanne besteht aus einem oder mehreren Wörtern (oder Tokens). Bei der benutzerdefinierten benannten Entitätserkennung (NER) stellen Entitäten die Information dar, die Sie aus dem Text extrahieren möchten. 

Beispiel: Im Satz „*John borrowed 25,000 USD from Fred*“ (John hat 25.000 USD von Fred geliehen) können dies die Entitäten sein: 

| Entitätsname/-typ | Entität |
| -- | -- |
| Name des Kreditnehmers | *John* |
| Name des Darlehensgebers | *Fred* |
| Darlehensbetrag | *25.000 USD* |

## <a name="next-steps"></a>Nächste Schritte

* [Daten- und Diensteinschränkungen](service-limits.md)
* [Übersicht über benutzerdefinierte NER](../overview.md)
