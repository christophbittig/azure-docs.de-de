---
title: Dokumentationslinks zur KI-Anreicherung
titleSuffix: Azure Cognitive Search
description: Eine kommentierte Liste von Artikeln, Tutorials, Beispielen und Blogbeiträgen zu Workloads der KI-Anreicherung in Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/16/2021
ms.openlocfilehash: acf4d6fa3057dca4d1b7ccf0c08ee8c8ca5ef31a
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131555524"
---
# <a name="documentation-resources-for-ai-enrichment-in-azure-cognitive-search"></a>Dokumentationressourcen für die KI-Anreicherung in Azure Cognitive Search

Die KI-Anreicherung ist ein Add-On für die Indexer-basierte Indizierung, die latente Informationen in Nicht-Textquellen und undifferenziertem Text findet und in durchsuchbare Volltextinhalte in Azure Cognitive Search umwandelt. 

Bei integrierter Verarbeitung werden die vorab trainierten KI-Modelle in Cognitive Services intern aufgerufen, um die Analysen auszuführen. Sie können mithilfe von Azure Machine Learning, Azure Functions oder anderen Ansätzen auch benutzerdefinierte Modelle integrieren.

Im Folgenden finden Sie eine konsolidierte Liste der Dokumentation für die KI-Anreicherung.

## <a name="concepts"></a>Konzepte

+ [KI-Anreicherungen](cognitive-search-concept-intro.md)
+ [Skillsets](cognitive-search-working-with-skillsets.md)
+ [Debugsitzungen](cognitive-search-debug-session.md)
+ [Wissensspeicher](knowledge-store-concept-intro.md)
+ [Projektionen](knowledge-store-projection-overview.md)
+ [Inkrementelle Anreicherung (Wiederverwendung eines zwischengespeicherten angereicherten Dokuments)](cognitive-search-incremental-indexing-conceptual.md)

## <a name="hands-on-walkthroughs"></a>Praktische Anleitungen

+ [Schnellstart: Erstellen eines Skillsets für Textübersetzung und Entität](cognitive-search-quickstart-blob.md)
+ [Schnellstart: Erstellen eines OCR-Image-Skillsets](cognitive-search-quickstart-ocr.md)
+ [Tutorial: Angereicherte Indizierung mit künstlicher Intelligenz (KI)](cognitive-search-tutorial-blob.md)
+ [Tutorial: Diagnostizieren, Reparieren und Committen von Änderungen an Ihrem Skillset](cognitive-search-tutorial-debug-sessions.md)

## <a name="knowledge-stores"></a>Wissensspeicher

+ [Schnellstart: Erstellen eines Azure Cognitive Search-Wissensspeichers im Azure-Portal](knowledge-store-create-portal.md)
+ [Erstellen eines Wissensspeichers mithilfe von REST und Postman](knowledge-store-create-rest.md)
+ [Anzeigen eines Wissensspeichers mit einem Speicherbrowser](knowledge-store-view-storage-explorer.md)
+ [Herstellen einer Verbindung mit einem Wissensspeicher mit Power BI](knowledge-store-connect-power-bi.md)
+ [Definieren von Projektionen in einem Wissensspeicher](knowledge-store-projections-examples.md)

## <a name="custom-skills-advanced"></a>Benutzerdefinierte Qualifikationen (erweitert)

+ [Definieren einer benutzerdefinierten Skillsschnittstelle](cognitive-search-custom-skill-interface.md)
+ [Beispiel: Erstellen einer benutzerdefinierten Qualifikation mit der Bing-Entitätssuche-API](cognitive-search-create-custom-skill-example.md)
+ [Beispiel: Erstellen einer benutzerdefinierten Qualifikation mit Python](cognitive-search-custom-skill-python.md)
+ [Beispiel: Erstellen eines benutzerdefinierten Skills der Formularerkennung](cognitive-search-custom-skill-form.md) 
+ [Beispiel: Erstellen und Bereitstellen eines benutzerdefinierten Skills mit Azure Machine Learning](cognitive-search-tutorial-aml-custom-skill.md) 

## <a name="how-to-guidance"></a>Leitfäden zur Vorgehensweise

+ [Anfügen einer Cognitive Services-Ressource an ein Skillset in der kognitiven Azure-Suche](cognitive-search-attach-cognitive-services.md)
+ [Definieren einer Fähigkeitengruppe](cognitive-search-defining-skillset.md)
+ [Verweisen auf Anmerkungen in einem Skillset der kognitiven Azure-Suche](cognitive-search-concept-annotations-syntax.md)
+ [Zuordnen von mit KI angereicherten Feldern zu einem durchsuchbaren Index](cognitive-search-output-field-mapping.md)
+ [Verarbeiten und Extrahieren von Informationen aus Bildern in KI-Anreicherungsszenarios](cognitive-search-concept-image-scenarios.md)
+ [Konfigurieren der Zwischenspeicherung für die inkrementelle Anreicherung](search-howto-incremental-index.md)
+ [Neuerstellen eines Azure Cognitive Search-Indexes](search-howto-reindex.md)
+ [KI-Anreicherung in Azure Cognitive Search](cognitive-search-concept-troubleshooting.md)
+ [Beheben von häufigen Fehler und Warnungen bei Suchindexern in Azure Cognitive Search](cognitive-search-common-errors-warnings.md)

## <a name="skills-reference"></a>Referenz zu Skills

+ [Integrierte Qualifikationen](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.V3.EntityLinkingSkill](cognitive-search-skill-entity-linking-v3.md)
  + [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.PIIDetectionSkill](cognitive-search-skill-pii-detection.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.V3.SentimentSkill](cognitive-search-skill-sentiment-v3.md)
  + [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ Benutzerdefinierte Qualifikationen
  + [Microsoft.Skills.Custom.AmlSkill](cognitive-search-aml-skill.md)
  + [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [Veraltete Skills](cognitive-search-skill-deprecated.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)
  + [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)

## <a name="apis"></a>APIs

+ [REST-API](/rest/api/searchservice/)
  + [Erstellen eines Skillsets (API-Version 30.06.2020)](/rest/api/searchservice/create-skillset)
  + [Erstellen eines Indexers (API-Version 30.06.2020)](/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Weitere Informationen

+ [REST-API für die kognitive Azure-Suche](/rest/api/searchservice/)
+ [Indexer in der kognitiven Azure-Suche](search-indexer-overview.md)
+ [Was ist Azure Cognitive Search?](search-what-is-azure-search.md)