---
title: Häufig gestellte Fragen
titleSuffix: Azure Cognitive Services
description: Verwenden Sie diesen Artikel, um schnell Antworten auf häufig gestellte Fragen zu Conversational Language Understanding zu erhalten.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2e699beb4bae6c31c73e2e5eebc508306deeb262
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131100364"
---
# <a name="frequently-asked-questions-for-conversational-language-understanding"></a>Häufig gestellte Fragen zu Conversational Language Understanding

Verwenden Sie diesen Artikel, um schnell Antworten auf allgemeine Fragen zu Conversational Language Understanding zu erhalten.

## <a name="how-do-i-create-a-project"></a>Wie erstelle ich ein Projekt?

Weitere Informationen finden Sie im [Schnellstart](./quickstart.md) für die schnelle Erstellung Ihres ersten Projekts oder im [Schrittanleitungsartikel](./how-to/create-project.md). 

## <a name="how-do-i-connect-other-service-applications-in-orchestration-workflow-projects"></a>Wie verbinde ich andere Dienstanwendungen in Projekten für Orchestrierungsworkflows?

Im [Artikel zur Schemaerstellung](./how-to/build-schema.md#build-project-schema-for-orchestration-workflow-projects) finden Sie Informationen zum Verbinden eines anderen Projekts als Absicht.

## <a name="which-luis-applications-can-i-connect-to-in-orchestration-workflow-projects"></a>Mit welchen LUIS-Anwendungen kann ich in Projekten für Orchestrierungsworkflows eine Verbindung herstellen?

LUIS-Anwendungen, die die Sprachressource als Erstellungsressource verwenden, sind für Verbindungen verfügbar. Sie können nur mit LUIS-Anwendungen Verbindungen herstellen, die zur gleichen Ressource gehören. Diese Option ist nur für Ressourcen in Westeuropa verfügbar, da dies die einzige gemeinsame Region zwischen LUIS und CLU ist. Weitere Informationen finden Sie unter [Regionale Einschränkungen](./service-limits.md#region-limits). 

## <a name="training-is-taking-a-long-time-is-this-expected"></a>Das Training dauert sehr lange, ist das zu erwarten?

Bei Unterhaltungsprojekten ist mit langen Trainingszeiten zu rechnen. Je nach Anzahl von Beispielen können Ihre Trainingszeiten zwischen fünf Minuten und einer Stunde oder mehr variieren. 

## <a name="can-i-add-entities-to-orchestration-workflow-projects"></a>Kann ich Entitäten zu Projekten für Orchestrierungsworkflows hinzufügen?

Nein. Orchestrierungsprojekte sind nur für Absichten aktiviert, die für das Routing mit anderen Projekten verbunden werden können. 

## <a name="how-do-i-use-entity-components"></a>Wie verwende ich Entitätskomponenten?

Weitere Informationen finden Sie im Artikel [Entitätskomponenten](./concepts/entity-components.md).

## <a name="which-languages-are-supported-in-this-feature"></a>Welche Sprachen werden in diesem Feature unterstützt?

Weitere Informationen finden Sie im Artikel zur [Sprachunterstützung](./language-support.md).

## <a name="how-do-i-get-more-accurate-results-for-my-project"></a>Wie kann ich genauere Ergebnisse für mein Projekt erhalten?

Sehen Sie sich die [empfohlenen Richtlinien](./how-to/build-schema.md#guidelines-and-recommendations) an, um Informationen zur Verbesserung der Genauigkeit zu erhalten.

## <a name="how-do-i-get-predictions-in-different-languages"></a>Wie erhalte ich Vorhersagen in verschiedenen Sprachen?

Wenn Sie ein Unterhaltungsprojekt in einer beliebigen Sprache trainieren und bereitstellen, können Sie sofort versuchen, es in [mehreren Sprachen](./concepts/multiple-languages.md) abzufragen. Sie können für verschiedene Sprachen unterschiedliche Ergebnisse erhalten. Um die Genauigkeit einer Sprache zu verbessern, fügen Sie Ihrem Projekt Äußerungen in dieser Sprache hinzu, um das trainierte Modell mit mehr Syntax dieser Sprache vertraut zu machen.

## <a name="how-many-intents-entities-utterances-can-i-add-to-a-project"></a>Wie viele Absichten, Entitäten und Äußerungen kann ich einem Projekt hinzufügen?

Weitere Informationen finden Sie im Artikel zu [Diensteinschränkungen](./service-limits.md). 

## <a name="can-i-label-the-same-word-as-2-different-entities"></a>Kann ich ein und dasselbe Wort als zwei verschiedene Entitäten bezeichnen?

Im Gegensatz zu LUIS können Sie denselben Text nicht als zwei verschiedene Entitäten bezeichnen. Gelernte Komponenten über verschiedene Entitäten hinweg schließen sich gegenseitig aus, und für jeden Satz von Zeichen wird nur eine gelernte Spanne vorhergesagt.

## <a name="can-i-import-a-luis-json-file-into-conversational-language-understanding"></a>Kann ich eine LUIS JSON-Datei in Conversational Language Understanding importieren?

Ja, Sie können [jede JSON-Datei einer LUIS-Anwendung der neuesten Version in den Dienst importieren](./concepts/backwards-compatibility.md).

## <a name="can-i-import-a-luis-lu-file-into-conversational-language-understanding"></a>Kann ich eine LUIS `.LU`-Datei in Conversational Language Understanding importieren?

Nein, der Dienst unterstützt nur das JSON-Format. Sie können zu LUIS wechseln, die Datei `.LU` importieren und als JSON-Datei exportieren. 

## <a name="is-there-any-sdk-support"></a>Gibt es eine SDK-Unterstützung?

Ja, nur für Vorhersagen, und [Beispiele sind verfügbar](https://aka.ms/cluSampleCode). Für das SDK gibt es derzeit keine Unterstützung für die Erstellung.

## <a name="are-there-apis-for-this-feature"></a>Gibt es APIs für dieses Feature?

Ja, alle APIs [sind verfügbar](https://aka.ms/clu-apis).

## <a name="next-steps"></a>Nächste Schritte

[Übersicht über das Conversational Language Understanding](overview.md)
