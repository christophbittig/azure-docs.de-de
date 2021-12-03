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
ms.openlocfilehash: 488d72987f97dd01842332f5d1d11739e878115d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131439099"
---
# <a name="frequently-asked-questions-for-custom-named-entity-recognition"></a>Häufig gestellte Fragen zur benutzerdefinierten Erkennung benannter Entitäten

Hier finden Sie Antworten auf häufig gestellte Fragen zu Konzepten und Szenarien im Zusammenhang mit benutzerdefinierten NER in Azure Cognitive Service für Sprache.

## <a name="how-do-i-get-started-with-the-service"></a>Wie steige ich in die Arbeit mit dem Dienst ein?

Informieren Sie sich im [Schnellstart](./quickstart.md), wie Sie schnell Ihr erstes Projekt erstellen. Noch ausführlichere Informationen finden Sie unter [Erstellen von Projekten](how-to/create-project.md).

## <a name="what-are-the-service-limits"></a>Was sind die Dienstgrenzwerte?

Weitere Informationen finden Sie im [Artikel zu den Diensteinschränkungen](service-limits.md).

## <a name="how-many-tagged-files-are-needed"></a>Wie viele markierte Dateien werden benötigt?

Im Allgemeinen führen vielfältige und repräsentativ [markierte Daten](how-to/tag-data.md) zu besseren Ergebnissen, da das Tagging genau, konsistent und vollständig erfolgt. Es gibt keine bestimmte Anzahl markierter Instanzen, mit der jedes Modell gut funktioniert. Die Leistung hängt stark von Ihrem Schema und der Mehrdeutigkeit Ihres Schemas ab. Mehrdeutige Entitätstypen benötigen mehr Tags. Die Leistung hängt auch von der Qualität Ihrer Markierung ab. Die empfohlene Anzahl markierter Instanzen pro Entität beträgt 50.

## <a name="training-is-taking-a-long-time-is-this-expected"></a>Das Training dauert sehr lange, ist das zu erwarten?

Der Trainingsprozess kann sehr lange dauern. Grob geschätzt beträgt die erwartete Trainingszeit für Dateien mit einer kombinierten Länge von 12.800.000 Zeichen etwa 6 Stunden.

## <a name="how-do-i-build-my-custom-model-programmatically"></a>Wie erstelle ich ein benutzerdefiniertes Modell programmgesteuert?

Sie können die [REST-APIs](https://aka.ms/ct-authoring-swagger) verwenden, um Ihre benutzerdefinierten Modelle zu erstellen. Befolgen Sie diesen [Schnellstart](quickstart.md?pivots=rest-api), um mit dem Erstellen eines Projekts und eines Modells mithilfe der APIs zu beginnen. Dort finden Sie auch Beispiele für das Aufrufen der Erstellungs-API.

## <a name="what-is-the-recommended-cicd-process"></a>Welcher CI/CD-Prozess wird empfohlen?

Sie können mehrere Modelle mit demselben Dataset innerhalb desselben Projekts trainieren. Nachdem Sie Ihr Modell erfolgreich trainiert haben, können Sie [dessen Auswertung anzeigen](how-to/view-model-evaluation.md). Sie können Ihr Modell in [Language Studio](https://aka.ms/languageStudio) [bereitstellen und testen](quickstart.md#deploy-your-model). Sie können Ihren Daten Tags hinzufügen oder von diesen entfernen, ein **neues** Modell trainieren und es auch testen. Sehen Sie sich die [Dienstgrenzwerte](service-limits.md) an, um mehr über die maximale Anzahl trainierter Modelle im selben Projekt zu erfahren. Wenn Sie ein neues Modell trainieren, wird Ihr Dataset nach dem Zufallsprinzip in Trainings- und Testsätze [aufgeteilt](how-to/train-model.md#data-split), sodass keine Garantie dafür besteht, dass sich die gezeigte Modellauswertung für den gleichen Testsatz gilt, sodass die Ergebnisse nicht vergleichbar sind. Es wird empfohlen, einen eigenen Testsatz zu entwickeln und damit beide Modelle zu bewerten, damit Sie Verbesserungen messen können.

## <a name="does-a-low-or-high-model-score-guarantee-bad-or-good-performance-in-production"></a>Garantiert ein niedriger oder hoher Modellscore eine schlechte oder gute Leistung in der Produktion?

Die Modellauswertung ist möglicherweise nicht immer umfassend. Dies ist von Folgendem abhängig:
* Wenn der **Testsatz** zu klein ist, sind gute/schlechte Scores nicht repräsentativ für die tatsächliche Leistung des Modells. Wenn ein bestimmter Entitätstyp fehlt oder im Testsatz nicht ausreichend repräsentiert ist, wirkt sich dies auch auf die Modellleistung aus.
* **Datendiversität:** Wenn Ihre Daten nur einige Szenarien/Beispiele für den Text umfassen, den Sie in der Produktion erwarten, wird Ihr Modell nicht für alle möglichen Szenarien trainiert und kann in den Szenarien, in denen es nicht trainiert wurde, eine schlechte Leistung zeigen.
* **Datendarstellung:** Wenn das Dataset, das zum Trainieren des Modells verwendet wird, nicht repräsentativ für die Daten ist, die das Modell in der Produktion enthält, wird die Modellleistung stark beeinträchtigt.

Weitere Informationen finden Sie im Artikel zu [Datenauswahl und Schemaentwurf](how-to/design-schema.md).

## <a name="how-do-i-improve-model-performance"></a>Wie verbessere ich die Modellleistung?

* Sehen Sie sich die [Konfusionsmatrix](how-to/view-model-evaluation.md) des Modells an. Wenn Sie feststellen, dass ein bestimmter Entitätstyp häufig falsch vorhergesagt wird, sollten Sie weitere markierte Instanzen für diese Klasse hinzufügen. Wenn zwei Entitätstypen bei der Vorhersage häufig miteinander verwechselt werden, ist das Schema mehrdeutig. Sie sollten dann beide Entitätstypen zusammenführen, um eine bessere Leistung zu erzielen.

* [Untersuchen Sie die Datenverteilung.](how-to/improve-model.md#examine-data-distribution) Wenn einer der Entitätstypen deutlich mehr markierte Instanzen als die anderen hat, ist Ihr Modell möglicherweise zu stark auf diesen Typ ausgerichtet. Fügen Sie den anderen Entitätstypen weitere Daten hinzu, oder entfernen Sie Beispiele aus dem dominanten Typ.

* Weitere Informationen zu Datenauswahl und Schemaentwurf finden Sie [hier](how-to/design-schema.md).

* [Überprüfen Sie Ihren Testsatz](how-to/improve-model.md), um vorhergesagte und markierte Entitäten nebeneinander zu sehen, damit Sie eine bessere Vorstellung von Ihrer Modellleistung erhalten und entscheiden können, ob Änderungen am Schema oder an den Tags erforderlich sind.

## <a name="why-do-i-get-different-results-when-i-retrain-my-model"></a>Warum erhalte ich beim erneuten Trainieren meines Modells andere Ergebnisse?

* Wenn Sie ein neues Modell trainieren, wird Ihr Dataset nach dem Zufallsprinzip in Trainings- und Testsätze [aufgeteilt](how-to/train-model.md#data-split), sodass keine Garantie dafür besteht, dass sich die gezeigte Modellauswertung für den gleichen Testsatz gilt, sodass die Ergebnisse nicht vergleichbar sind.

* Wenn Sie dasselbe Modell erneut trainieren, ist Ihr Testsatz zwar identisch, aber Sie werden möglicherweise eine geringfügige Abweichung bei den Vorhersagen des Modells feststellen. Dies liegt daran, dass das trainierte Modell nicht stabil genug ist. Ein Faktor dafür ist, wie repräsentativ und eindeutig Ihre Daten sind und welche Qualität Ihre markierten Daten aufweisen.

## <a name="how-do-i-get-predictions-in-different-languages"></a>Wie erhalte ich Vorhersagen in verschiedenen Sprachen?

Zunächst müssen Sie die mehrsprachige Option beim [Erstellen Ihres Projekts](how-to/create-project.md) aktivieren. Sie können die Option aber auch später noch auf der Seite mit den Projekteinstellungen aktivieren. Nachdem Sie Ihr Modell trainiert und bereitgestellt haben, können Sie mit der Abfrage in [mehreren Sprachen](language-support.md#multiple-language-support) beginnen. Sie können für verschiedene Sprachen unterschiedliche Ergebnisse erhalten. Um die Genauigkeit bei einer Sprache zu verbessern, fügen Sie Ihrem Projekt zusätzliche markierte Instanzen in dieser Sprache hinzu, um das trainierte Modell mit mehr Syntax dieser Sprache vertraut zu machen.

## <a name="i-trained-my-model-but-i-cant-test-it"></a>Ich habe mein Modell trainiert, kann es aber nicht testen.

Sie müssen Ihr [Modell bereitstellen,](quickstart.md#deploy-your-model) bevor Sie es testen können. 

## <a name="how-do-i-use-my-trained-model-for-the-prediction-api"></a>Wie verwende ich mein trainiertes Modell für die Vorhersage-API?

Nach der Bereitstellung Ihres Modells [rufen Sie die Vorhersage-API auf](how-to/call-api.md). Weitere Informationen finden Sie in der [Referenz zur Vorhersage-API](https://aka.ms/ct-runtime-swagger).

## <a name="data-privacy-and-security"></a>Datenschutz und Sicherheit

Die Erkennung benannter Entitäten (NER) fällt laut Datenschutz-Grundverordnung (DSGVO) in den Bereich „Datenverarbeitung“. In Übereinstimmung mit den DSGVO-Richtlinien haben Benutzer*innen der Erkennung benannter Entitäten die volle Kontrolle über das Anzeigen, Exportieren oder Löschen von Benutzerinhalten über [Language Studio](https://aka.ms/languageStudio) oder programmgesteuert mithilfe von [REST-APIs](https://aka.ms/ct-authoring-swagger).

Ihre Daten werden nur in Ihrem Azure Storage-Konto gespeichert. Die benutzerdefinierte Erkennung benannter Entitäten hat nur während des Trainings Lesezugriff darauf.

## <a name="how-to-clone-my-project"></a>Wie klone ich mein Projekt?

Um Ihr Projekt zu klonen, müssen Sie die Export-API verwenden, um die Projektressourcen zu exportieren und sie dann in ein neues Projekt zu importieren. Informationen zu beiden Vorgängen finden Sie in der [Referenz zur REST-API](https://aka.ms/ct-authoring-swagger).

## <a name="next-steps"></a>Nächste Schritte

* [Benutzerdefinierte NER-Übersicht](overview.md)
* [Schnellstart](quickstart.md)
