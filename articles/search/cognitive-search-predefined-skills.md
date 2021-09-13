---
title: Integrierte Text- und Bildverarbeitung während der Indizierung
titleSuffix: Azure Cognitive Search
description: Durch Skills für Datenextraktion, natürliche Sprache und Bildverarbeitung erhält der Rohdateninhalt in einer Anreicherungspipeline von Azure Cognitive Search Semantik und Struktur.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: ddf7d6bba58d8eb8a7ad8a52a8e9c51098e71dcc
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/16/2021
ms.locfileid: "122350907"
---
# <a name="built-in-skills-for-text-and-image-processing-during-indexing-azure-cognitive-search"></a>Integrierte Skills für die Text- und Bildverarbeitung während der Indizierung (Azure Cognitive Search)

In diesem Artikel erfahren Sie mehr über die Skills, die mit Azure Cognitive Search bereitgestellt werden und die Sie in ein [Skillset](cognitive-search-working-with-skillsets.md) einschließen können, um Inhalt und Struktur aus unstrukturierten Rohdaten von Text- und Bilddateien zu extrahieren. Ein *Skill* ist ein unteilbarer Vorgang, der Inhalte in irgendeiner Weise transformiert. Häufig handelt es sich um einen Vorgang, der Text erkennt oder extrahiert, aber es kann auch ein praktischer Skill sein, der die bereits erstellten Anreicherungen umgestaltet. In der Regel ist die Ausgabe textbasiert, sodass sie in Volltextabfragen verwendet werden kann. 

## <a name="built-in-skills"></a>Integrierte Skills

Integrierte Skills basieren auf vortrainierten Modellen, d. h. Sie können das Modell nicht mit Ihren eigenen Trainingsdaten trainieren. Skills, die die Cognitive Resources-APIs aufrufen, sind von diesen Diensten abhängig und werden nach dem nutzungsbasierten Preis von Cognitive Services berechnet, wenn Sie [eine Ressource anfügen](cognitive-search-attach-cognitive-services.md). Andere Skills werden durch Azure Cognitive Search bewertet, oder es handelt sich um hilfreiche Skills, die kostenlos verfügbar sind.

In der folgenden Tabelle werden die integrierten Skills aufgelistet und beschrieben.

| OData-Typ  | BESCHREIBUNG | Bewertet durch |
|-------|-------------|-------------|
|[Microsoft.Skills.Text.CustomEntityLookupSkill](cognitive-search-skill-custom-entity-lookup.md) | Sucht nach Text aus einer benutzerdefinierten Liste von Wörtern und Ausdrücken.| Azure Cognitive Search ([Preise](https://azure.microsoft.com/pricing/details/search/)) |
| [Microsoft.Skills.Text.KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md) | Dieser Skill verwendet ein vortrainiertes Modell, um wichtige Phrasen zu erkennen, die auf der Platzierung von Begriffen, sprachlichen Regeln, der Nähe zu anderen Begriffen und der Ungewöhnlichkeit des Begriffs innerhalb der Quelldaten basieren. | Cognitive Services ([Preise](https://azure.microsoft.com/pricing/details/cognitive-services/)) | 
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Dieser Skill verwendet ein vortrainiertes Modell, um die verwendete Sprache zu erkennen (eine Sprachen-ID pro Dokument). Wenn mehrere Sprachen in den gleichen Textsegmenten verwendet werden, wird die LCID der vorwiegend verwendeten Sprache ausgegeben. | Cognitive Services ([Preise](https://azure.microsoft.com/pricing/details/cognitive-services/)) | 
| [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md) | Konsolidiert Text aus einer Sammlung von Feldern in einem einzigen Feld.  | Nicht zutreffend |
| [Microsoft.Skills.Text.V3.EntityLinkingSkill](cognitive-search-skill-entity-linking-v3.md) | Dieser Skill verwendet ein vortrainiertes Modell, um in einem gegebenen Text Übereinstimmungen mit verknüpften Entitäten zu ermitteln. | Cognitive Services ([Preise](https://azure.microsoft.com/pricing/details/cognitive-services/)) | 
| [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md) | Dieser Skill verwendet ein vortrainiertes Modell, um Entitäten für eine feste Gruppe von Kategorien zu erstellen: die Felder `"Person"`, `"Location"`, `"Organization"`, `"Quantity"`, `"DateTime"`, `"URL"`, `"Email"`, `"PersonType"`, `"Event"`, `"Product"`, `"Skill"`, `"Address"`, `"Phone Number"` und `"IP Address"`. | Cognitive Services ([Preise](https://azure.microsoft.com/pricing/details/cognitive-services/)) | 
| [Microsoft.Skills.Text.PIIDetectionSkill](cognitive-search-skill-pii-detection.md)  | Dieser Skill verwendet ein vortrainiertes Modell, um persönliche Informationen aus einem Text zu extrahieren. Darüber hinaus bietet der Skill verschiedene Maskierungsoptionen für die im Text erkannten persönlichen Informationsentitäten.  | Cognitive Services ([Preise](https://azure.microsoft.com/pricing/details/cognitive-services/)) | 
| [Microsoft.Skills.Text.V3.SentimentSkill](cognitive-search-skill-sentiment-v3.md)  | Dieser Skill verwendet ein vortrainiertes Modell, um für jeweils einen Datensatz Stimmungsbezeichnungen (z. B. „negativ“, „neutral“ und „positiv“) basierend auf der höchsten Zuverlässigkeitsbewertung anzugeben, die vom Dienst auf Satz- und Dokumentebene gefunden wird. | Cognitive Services ([Preise](https://azure.microsoft.com/pricing/details/cognitive-services/)) | 
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | Teilt Text in Seiten auf, damit Sie Inhalt inkrementell anreichern oder erweitern können. | Nicht zutreffend |
| [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md) | Dieser Skill verwendet ein vortrainiertes Modell, um den Eingabetext zur Normalisierung oder Lokalisierung in verschiedene Sprachen zu übersetzen. | Cognitive Services ([Preise](https://azure.microsoft.com/pricing/details/cognitive-services/)) | 
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Dieser Skill verwendet einen Bilderkennungsalgorithmus, um den Inhalt eines Bildes zu identifizieren und eine Textbeschreibung zu erzeugen. | Cognitive Services ([Preise](https://azure.microsoft.com/pricing/details/cognitive-services/)) | 
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | Optische Zeichenerkennung | Cognitive Services ([Preise](https://azure.microsoft.com/pricing/details/cognitive-services/)) |
| [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md) | Ermöglicht Filterung, Zuweisung eines Standardwerts und Zusammenführung von Daten auf der Grundlage einer Bedingung. | Nicht zutreffend |
| [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) | Extrahiert Inhalt aus einer Datei innerhalb der Anreicherungspipeline. | Azure Cognitive Search ([Preise](https://azure.microsoft.com/pricing/details/search/))
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | Ordnet die Ausgabe einem komplexen Typ zu (ein mehrteiliger Datentyp, der für einen vollständigen Namen, eine mehrzeilige Adresse oder eine Kombination aus Nachname und persönlicher ID verwendet werden kann). | Nicht zutreffend |

## <a name="custom-skills"></a>Benutzerdefinierte Qualifikationen

[Benutzerdefinierte Skills](cognitive-search-custom-skill-web-api.md) sind Module, die Sie entwerfen, entwickeln und im Web bereitstellen. Sie können das Modul dann innerhalb eines Skillsets als benutzerdefinierten Skill aufrufen.

| type  | BESCHREIBUNG | Bewertet durch |
|-------|-------------|-------------|
| [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md) | Ermöglicht die Erweiterbarkeit einer KI-Anreicherungspipeline, indem ein HTTP-Aufruf in einer benutzerdefinierten Web-API ausgeführt wird. | Keine, es sei denn, Ihre Lösung verwendet einen gemessenen Azure-Dienst. |
| [Microsoft.Skills.Custom.AmlSkill](cognitive-search-aml-skill.md) | Ermöglicht die Erweiterbarkeit einer KI-Anreicherungspipeline mit einem Azure Machine Learning-Modell | Keine, es sei denn, Ihre Lösung verwendet einen gemessenen Azure-Dienst. |

Eine Anleitung zum Erstellen eines benutzerdefinierten Skills finden Sie unter [Definieren einer benutzerdefinierten Schnittstelle](cognitive-search-custom-skill-interface.md) und [Beispiel: Erstellen eines benutzerdefinierten Skills für die KI-Anreicherung](cognitive-search-create-custom-skill-example.md).

## <a name="see-also"></a>Weitere Informationen

+ [Definieren eines Skillsets](cognitive-search-defining-skillset.md)
+ [Schnittstellendefinition für benutzerdefinierte Skills](cognitive-search-custom-skill-interface.md)
+ [Tutorial: Angereicherte Indizierung mit künstlicher Intelligenz (KI)](cognitive-search-tutorial-blob.md)
