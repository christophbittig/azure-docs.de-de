---
title: Häufig gestellte Fragen zur benutzerdefinierten Textklassifizierung
titleSuffix: Azure Cognitive Services
description: Hier finden Sie häufig gestellte Fragen zur Verwendung der API für die benutzerdefinierte Textklassifizierung.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: a6a4473e6d7dfa55fc75934f3fbdfd44c89ab1c4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095213"
---
# <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

Hier finden Sie Antworten auf häufig gestellte Fragen zu Konzepten sowie Szenarien im Zusammenhang mit der benutzerdefinierten Textklassifizierung in Azure Cognitive Services for Language.

## <a name="how-many-tagged-files-are-needed"></a>Wie viele markierte Dateien werden benötigt?

Im Allgemeinen führen vielfältige und repräsentativ [markierte Daten](how-to/tag-data.md) zu besseren Ergebnissen, da das Tagging genau, konsistent und vollständig erfolgt. Es gibt keine bestimmte Anzahl markierter Klassen, die dazu führen, dass jedes Modell gut funktioniert. Die Leistung hängt stark von Ihrem Schema und der Mehrdeutigkeit Ihres Schemas ab. Mehrdeutige Klassen benötigen mehr Tags. Die Leistung hängt auch von der Qualität Ihrer Markierung ab. Die empfohlene Anzahl markierter Instanzen pro Entität beträgt 50. 

## <a name="what-are-the-service-limits"></a>Was sind die Dienstgrenzwerte?

Weitere Informationen finden Sie im [Artikel zu den Diensteinschränkungen](service-limits.md).

## <a name="what-to-do-if-my-model-scores-poorly"></a>Was tun, wenn mein Modell schlecht abschneidet?

Die Modellauswertung ist möglicherweise nicht immer umfassend, insbesondere wenn eine bestimmte Klasse fehlt oder im Testsatz nicht dargestellt wird. Fügen Sie Ihrem Modell weitere markierte Daten hinzu, um sowohl die Leistung zu verbessern als auch einen repräsentativeren Testsatz zu haben.

## <a name="how-do-i-improve-model-performance"></a>Wie verbessere ich die Modellleistung?

Sehen Sie sich die [Konfusionsmatrix](how-to/view-model-evaluation.md) an, um mehrdeutige Schemata zu identifizieren. [Überprüfen Sie dann Ihren Testsatz](how-to/improve-model.md), um vorhergesagte und markierte Klassen nebeneinander zu sehen, damit Sie eine bessere Vorstellung von Ihrer Modellleistung erhalten und entscheiden können, ob Änderungen am Schema oder an den Tags erforderlich sind.  

## <a name="i-trained-my-model-but-i-cant-test-it"></a>Ich habe mein Modell trainiert, kann es aber nicht testen.

Sie müssen Ihr [Modell bereitstellen,](quickstart.md#deploy-your-model) bevor Sie es testen können. 

## <a name="how-do-i-use-the-analyze-api"></a>Wie verwende ich die Analyse-API?

Nach der Bereitstellung Ihres Modells [rufen Sie die Laufzeit-API auf](how-to/call-api.md). Weitere Informationen finden Sie in der [Referenz zur API-Analyse](https://aka.ms/ct-runtime-swagger).

## <a name="data-privacy-and-security"></a>Datenschutz und Sicherheit

Ihre Daten werden nur in Ihrem Azure-Speicherkonto gespeichert. Die benutzerdefinierte Klassifizierung hat nur während des Trainings und der Auswertung Lesezugriff. 

<!-- ## How to clone my project?

To clone your project you need to [export]() project assests and then [import]() them into a new project. -->

## <a name="next-steps"></a>Nächste Schritte

* [Grenzwerte für die benutzerdefinierte Textklassifizierung – Übersicht](overview.md)
* [quickstart](quickstart.md)
