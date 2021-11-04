---
title: Benutzerdefinierte Erkennung benannter Entitäten (Custom Erkennung benannter Entitäten, NER) – häufig gestellte Fragen
titleSuffix: Azure Cognitive Services
description: Erfahren Sie mehr über häufig gestellte Fragen zur Verwendung benutzerdefinierter Erkennung benannter Entitäten.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: c413e03e9106a3063e9cfdf26a1ab4b7fd7d5f25
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095926"
---
# <a name="frequently-asked-questions-for-custom-named-entity-recognition"></a>Häufig gestellte Fragen zur benutzerdefinierten Erkennung benannter Entitäten

Hier finden Sie Antworten auf häufig gestellte Fragen zu Konzepten und Szenarien im Zusammenhang mit benutzerdefinierten NER in Azure Cognitive Service für Sprache.

## <a name="how-many-tagged-files-are-needed"></a>Wie viele markierte Dateien werden benötigt?

Im Allgemeinen führen vielfältige und repräsentativ [markierte Daten](how-to/tag-data.md) zu besseren Ergebnissen, da das Tagging genau, konsistent und vollständig erfolgt. Es gibt keine bestimmte Anzahl markierter Entitäten, die dazu führen, dass jedes Modell gut funktioniert. Die Leistung hängt stark von Ihrem Schema und der Mehrdeutigkeit Ihrer Entitäten ab. Mehrdeutige Entitätstypen benötigen mehr Tags. Die Leistung hängt auch von der Qualität Ihrer Markierung ab. Die empfohlene Anzahl markierter Instanzen pro Entität beträgt 200. 

## <a name="what-are-the-service-limits"></a>Was sind die Dienstgrenzwerte?

Weitere Informationen finden Sie im [Artikel zu den Diensteinschränkungen](service-limits.md).

## <a name="what-to-do-if-my-model-scores-poorly"></a>Was tun, wenn mein Modell schlecht abschneidet?

Die Modellauswertung ist möglicherweise nicht immer umfassend, insbesondere wenn eine bestimmte Klasse fehlt oder im Testsatz nicht dargestellt wird. Fügen Sie Ihrem Modell weitere markierte Daten hinzu, um sowohl die Leistung zu verbessern als auch einen repräsentativeren Testsatz zu haben.

## <a name="how-do-i-improve-model-performance"></a>Wie verbessere ich die Modellleistung?

Sehen Sie sich die [Konfusionsmatrix](how-to/view-model-evaluation.md) an, um mehrdeutige Schemata zu identifizieren. [Überprüfen Sie dann Ihren Testsatz](how-to/improve-model.md), um vorhergesagte und markierte Entitäten nebeneinander zu sehen, damit Sie eine bessere Vorstellung von Ihrer Modellleistung erhalten und entscheiden können, ob Änderungen am Schema oder an den Tags erforderlich sind.  

## <a name="i-trained-my-model-but-i-cant-test-it"></a>Ich habe mein Modell trainiert, kann es aber nicht testen.

Sie müssen Ihr [Modell bereitstellen,](quickstart.md#deploy-your-model) bevor Sie es testen können. 

## <a name="how-do-i-use-the-analyze-api"></a>Wie verwende ich die Analyse-API?

Nach der Bereitstellung Ihres Modells [rufen Sie die Laufzeit-API auf](how-to/call-api.md). Weitere Informationen finden Sie in der [Referenz zur API-Analyse](https://aka.ms/ct-runtime-swagger).

## <a name="data-privacy-and-security"></a>Datenschutz und Sicherheit

Ihre Daten werden nur in Ihrem Azure-Speicherkonto gespeichert. Benutzerdefinierte NER hat nur während des Trainings und der Auswertung Zugriff auf Lesezugriff. 

<!-- ## How to clone my project?

To clone your project you need to [export]() project assests and then [import]() them into a new project. -->

## <a name="next-steps"></a>Nächste Schritte

[Benutzerdefinierte NER-Übersicht](overview.md)
