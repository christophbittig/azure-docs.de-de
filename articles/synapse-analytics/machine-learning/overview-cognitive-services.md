---
title: Cognitive Services in Azure Synapse Analytics
description: Reichern Sie Ihre Daten in Azure Synapse Analytics mithilfe von vortrainierten Modellen aus Azure Cognitive Services mit künstlicher Intelligenz (KI) an.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: overview
ms.reviewer: jrasnick, garye, negust, ruxu
ms.date: 06/30/2021
author: garyericson
ms.author: garye
ms.custom: ignite-fall-2021
ms.openlocfilehash: e69730f486303fa508fed4cd53f8df3ee76433ba
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132318463"
---
# <a name="cognitive-services-in-azure-synapse-analytics"></a>Cognitive Services in Azure Synapse Analytics

Mit vortrainierten Modellen aus Azure Cognitive Services können Sie Ihre Daten mit künstlicher Intelligenz (KI) in Azure Synapse Analytics anreichern.

[Azure Cognitive Services](../../cognitive-services/what-are-cognitive-services.md) sind cloudbasierte Dienste, die Ihre Daten um kognitive Intelligenz erweitern, auch wenn Sie nicht über KI- oder Data Science-Kenntnisse verfügen. Es gibt verschiedene Möglichkeiten, wie Sie diese Dienste mit Ihren Daten in Synapse Analytics verwenden können:

- Der Cognitive Services-Assistent in Synapse Analytics generiert PySpark-Code in einem Synapse-Notebook, das mithilfe von Daten in einer Spark-Tabelle eine Verbindung mit einem Cognitive Services-Dienst herstellt. Mithilfe vorab trainierter Machine Learning-Modelle fügt der Dienst Ihren Daten dann KI hinzu. Weitere Informationen finden Sie unter [Stimmungsanalyse-Assistent](tutorial-cognitive-services-sentiment.md) und [Anomalieerkennungs-Assistent](tutorial-cognitive-services-anomaly.md).

- Synapse Machine Learning ([SynapseML](https://github.com/microsoft/SynapseML)) ermöglicht die Erstellung leistungsstarker und hochgradig skalierbarer Vorhersage- und Analysemodelle auf der Grundlage verschiedener Spark-Datenquellen. Synapse Spark bietet integrierte SynapseML-Bibliotheken, einschließlich [Cognitive Services für Spark](https://github.com/microsoft/SynapseML/blob/master/docs/cogsvc.md). Weitere Informationen finden Sie in den [Tutorials](#tutorials).

- Sie können ausgehend von dem vom Assistenten generierten PySpark-Code oder dem im Tutorial bereitgestellten SynapseML-Beispielcode eigenen Code schreiben, um andere Dienste von Cognitive Services mit Ihren Daten zu verwenden. Weitere Informationen zu den verfügbaren Diensten finden Sie unter [Was sind Azure Cognitive Services?](../../cognitive-services/what-are-cognitive-services.md).




## <a name="get-started"></a>Erste Schritte

Das Tutorial [Voraussetzungen für die Verwendung von Cognitive Services in Azure Synapse Analytics](tutorial-configure-cognitive-services-synapse.md) führt Sie durch einige Schritte, die Sie ausführen müssen, bevor Sie Cognitive Services in Synapse Analytics verwenden können.

## <a name="tutorials"></a>Tutorials

Die folgenden Tutorials enthalten vollständige Beispiele für die Verwendung von Cognitive Services in Synapse Analytics.

- [Stimmungsanalyse mit Cognitive Services:](tutorial-cognitive-services-sentiment.md) Sie erstellen mithilfe eines Beispieldatasets von Kundenkommentaren eine Spark-Tabelle mit einer Spalte, die die Stimmung der Kommentare in jeder Zeile angibt.

- [Anomalieerkennung mit Cognitive Services:](tutorial-cognitive-services-anomaly.md) Sie erstellen mithilfe eines Beispieldatasets von Zeitreihendaten eine Spark-Tabelle mit einer Spalte, die angibt, ob die Daten in den einzelnen Zeilen Anomalien sind.

- [Erstellen von Machine Learning-Anwendungen mithilfe von Microsoft Machine Learning für Apache Spark](tutorial-build-applications-use-mmlspark.md): In diesem Tutorial wird veranschaulicht, wie Sie SynapseML verwenden, um über Cognitive Services auf verschiedene Modelle zuzugreifen.

- [Formularerkennung mit Applied KI Service](tutorial-form-recognizer-use-mmlspark.md) demonstriert, wie Sie in Azure Synapse Analytics mit dem Feature [Formularerkennung](../../applied-ai-services/form-recognizer/index.yml) Formulare und Dokumente analysieren, um Text und Daten zu extrahieren. 

- [Textanalyse mit Cognitive Services](tutorial-text-analytics-use-mmlspark.md) veranschaulicht, wie Sie in Azure Synapse Analytics mithilfe der [Textanalyse](../../cognitive-services/text-analytics/index.yml) unstrukturierten Text analysieren.

- In [Textübersetzung mit Cognitive Services](tutorial-translator-use-mmlspark.md) wird gezeigt, wie Sie in Azure Synapse Analytics mit dem Feature [Textübersetzung](../../cognitive-services/Translator/index.yml) intelligente, mehrsprachige Lösungen erstellen.

- [Maschinelles Sehen mit Cognitive Services](tutorial-computer-vision-use-mmlspark.md) veranschaulicht, wie Sie in Azure Synapse Analytics Bilder mit dem Feature [Maschinelles Sehen](../../cognitive-services/computer-vision/index.yml) analysieren.

## <a name="available-cognitive-services-apis"></a>Verfügbare Cognitive Services-APIs
### <a name="bing-image-search"></a>Bing-Bildersuche

| API-Typ                                   | SynapseML-APIs                  | Cognitive Services-APIs (Versionen)                                                                                               | DEP-VNet-Unterstützung |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
|Bing-Bildersuche|BingImageSearch|Bilder: Visuelle Suche, Version 7.0| Nicht unterstützt|

### <a name="anomaly-detector"></a>Anomalieerkennung

| API-Typ                                   | SynapseML-APIs                  | Cognitive Services-APIs (Versionen)                                                                                               | DEP-VNet-Unterstützung |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| Erkennen der letzten Anomalie                        | DetectLastAnomaly              | Erkennen des letzten Punkts, Version 1.0                                                                                                          | Unterstützt        |
| Erkennen von Anomalien                           | DetectAnomalies                | Erkennen der gesamten Serie, Version 1.0                                                                                                       | Unterstützt        |
| Einfaches DetectAnomalies                     | SimpleDetectAnomalies          | Erkennen der gesamten Serie, Version 1.0                                                                                                       | Unterstützt        |

### <a name="computer-vision"></a>Maschinelles Sehen

| API-Typ                                   | SynapseML-APIs                  | Cognitive Services-APIs (Versionen)                                                                                               | DEP-VNet-Unterstützung |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| OCR                                        | OCR                            | Erkennen von gedrucktem Text, Version 2.0                                                                                                     | Unterstützt        |
| Texterkennung                             | RecognizeText                  | Texterkennung, Version 2.0                                                                                                             | Unterstützt        |
| Bild lesen                                 | ReadImage                      | Lesen, Version 3.1                                                                                                                       | Unterstützt        |
| Generieren von Miniaturansichten                        | GenerateThumbnails             | Generieren einer Miniaturansicht, Version 2.0                                                                                                         | Unterstützt        |
| Bild analysieren                              | AnalyzeImage                   | Bild analysieren, Version 2.0                                                                                                              | Unterstützt        |
| Erkennen domänenspezifischer Inhalte          | RecognizeDomainSpecificContent | Analysieren eines Bilds nach Domäne, Version 2.0                                                                                                    | Unterstützt        |
| Bild markieren                                  | TagImage                       | Bild markieren, Version 2.0                                                                                                                  | Unterstützt        |
| Bild beschreiben                             | DescribeImage                  | Bild beschreiben, Version 2.0                                                                                                             | Unterstützt        |


### <a name="translator"></a>Übersetzer

| API-Typ                                   | SynapseML-APIs                  | Cognitive Services-APIs (Versionen)                                                                                               | DEP-VNet-Unterstützung |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| Übersetzen von Text                             | Translate                      | Übersetzen, Version 3.0                                                                                                                  | Nicht unterstützt    |
| Transliteration von Text                         | Transliterate                  | Transliterieren, Version 3.0                                                                                                              | Nicht unterstützt    |
| Erkennen von Sprache                            | Detect                         | Erkennen, Version 3.0                                                                                                                     | Nicht unterstützt    |
| Satz umbrechen                             | BreakSentence                  | Satz umbrechen, Version 3.0                                                                                                             | Nicht unterstützt    |
| Wörterbuchsuche (Alternative Übersetzungen) | DictionaryLookup               | Wörterbuchsuche, Version 3.0                                                                                                          | Nicht unterstützt    |
| Dokumentübersetzung                       | DocumentTranslator             | Dokumentübersetzung, Version 1.0                                                                                                       | Nicht unterstützt    |


### <a name="face"></a>Gesicht

| API-Typ                                   | SynapseML-APIs                  | Cognitive Services-APIs (Versionen)                                                                                               | DEP-VNet-Unterstützung |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| Gesichtserkennung                                | DetectFace                     | Erkennen mit URL, Version 1.0                                                                                                            | Unterstützt        |
| Suchen ähnlicher Gesichter                          | FindSimilarFace                | Suchen von Ähnlichem, Version 1.0                                                                                                               | Unterstützt        |
| Gruppieren von Gesichtern                                | GroupFaces                     | Gruppieren, Version 1.0                                                                                                                      | Unterstützt        |
| Identifizieren von Gesichtern                             | IdentifyFaces                  | Identifizieren, Version 1.0                                                                                                                   | Unterstützt        |
| Überprüfen von Gesichtern                               | VerifyFaces                    | Überprüfen von Gesichtern, Version 1.0                                                                                                        | Unterstützt        |

### <a name="form-recognizer"></a>Formularerkennung
| API-Typ                                   | SynapseML-APIs                  | Cognitive Services-APIs (Versionen)                                                                                               | DEP-VNet-Unterstützung |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| Analysieren des Layouts                             | AnalyzeLayout                  | Analysieren des Layouts (asynchron), Version 2.1                                                                                                       | Unterstützt        |
| Analysieren von Belegen                           | AnalyzeReceipts                | Analysieren von Belegen (asynchron), Version 2.1                                                                                                      | Unterstützt        |
| Analysieren von Visitenkarten                     | AnalyzeBusinessCards           | Analysieren von Visitenkarten (asynchron), Version 2.1                                                                                                | Unterstützt        |
| Analysieren von Rechnungen                           | AnalyzeInvoices                | Analysieren von Rechnungen (asynchron), Version 2.1                                                                                                      | Unterstützt        |
| Analysieren von Ausweisdokumenten                       | AnalyzeIDDocuments             | Dokumentmodell für Identifikation (ID), Version 2.1                                                                                         | Unterstützt        |
| Auflisten benutzerdefinierter Modelle                         | ListCustomModels               | Auflisten benutzerdefinierter Modelle, Version 2.1                                                                                                         | Unterstützt        |
| Abrufen eines benutzerdefinierten Modells                           | GetCustomModel                 | Abrufen benutzerdefinierter Modelle, Version 2.1                                                                                                          | Unterstützt        |
| Analysieren eines benutzerdefinierten Modells                       | AnalyzeCustomModel             | Analysieren mit dem benutzerdefinierten Modell, Version 2.1                                                                                                  | Unterstützt        |

### <a name="speech-to-text"></a>Spracherkennung
| API-Typ                                   | SynapseML-APIs                  | Cognitive Services-APIs (Versionen)                                                                                               | DEP-VNet-Unterstützung |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| Spracherkennung                             | SpeechToText                   | SpeechToText, Version 1.0 |  Nicht unterstützt    |
| Spracherkennung (SDK)                         | SpeechToTextSDK                | Verwenden des Speech SDK, Version 1.14.0                                                                                                 | Nicht unterstützt    |


### <a name="text-analytics"></a>Textanalyse

| API-Typ                                   | SynapseML-APIs                  | Cognitive Services-APIs (Versionen)                                                                                               | DEP-VNet-Unterstützung |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| Stimmung in Text, Version 2                          | TextSentimentV2                | Stimmung, Version 2.0                                                                                                                  | Unterstützt        |
| Spracherkennungsmodul, Version 2                       | LanguageDetectorV2             | Sprachen, Version 2.0                                                                                                                  | Unterstützt        |
| Entitätserkennung, Version 2                         | EntityDetectorV2               | Entitätsverknüpfung, Version 2.0                                                                                                           | Unterstützt        |
| NER, Version 2                                     | NERV2                          | Entitätserkennung (allgemein), Version 2.0                                                                                               | Unterstützt        |
| Schlüsselbegriffserkennung, Version 2.0                    | KeyPhraseExtractorV2           | Schlüsselbegriffe, Version 2.0                                                                                                                | Unterstützt        |
| Stimmung in Text                             | TextSentiment                  | Stimmung, Version 3.1                                                                                                                  | Unterstützt        |
| Schlüsselbegriffserkennung                       | KeyPhraseExtractor             | Schlüsselbegriffe, Version 3.1                                                                                                                | Unterstützt        |
| PII                                        | PII                            | Entitätserkennung (personenbezogene Daten), Version 3.1                                                                                                   | Unterstützt        |
| NER                                        | NER                            | Entitätserkennung (allgemein), Version 3.1                                                                                               | Unterstützt        |
| Spracherkennung                          | LanguageDetector               | Sprachen, Version 3.1                                                                                                                  | Unterstützt        |
| Entitätserkennung                            | EntityDetector                 | Entitätsverknüpfung, Version 3.1                                                                                                           | Unterstützt        |


## <a name="next-steps"></a>Nächste Schritte

- [Machine Learning-Funktionen in Azure Synapse Analytics](what-is-machine-learning.md)
- [Was ist Cognitive Services?](../../cognitive-services/what-are-cognitive-services.md)
- [Verwenden eines Beispielnotebooks aus dem Synapse Analytics-Katalog](quickstart-gallery-sample-notebook.md)
