---
title: Handbuch für die Migration von Version 2 der Textanalyse-API
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie Ihre Anwendungen zur Verwendung von Version 3 der Textanalyse-API migrieren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/06/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 396cac23548eb951e47c4ec3a602aadcdfa304f4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021583"
---
# <a name="migrate-from-version-2-of-the-text-analytics-api"></a>Migrieren von Version 2 der Textanalyse-API

Wenn Ihre Anwendungen Version 2.1 der Textanalyse-API verwenden, hilft Ihnen dieser Artikel, ein Upgrade auf Ihre Anwendungen durchzuführen, sodass sie die neueste Version der Features verwenden, die jetzt Teil von [Azure Cognitive Service für Language](../overview.md) sind.

## <a name="features"></a>Features

Wählen Sie eines der folgenden Features aus, um Informationen anzuzeigen, die Sie zum Aktualisieren Ihrer Anwendung verwenden können.

## <a name="sentiment-analysis"></a>[Standpunktanalyse](#tab/sentiment-analysis)

> [!TIP]
> Möchten Sie die neueste Version der API in Ihrer Anwendung verwenden? Informationen zur aktuellen Version der API finden Sie unter [Vorgehensweise: Stimmungsanalyse und Opinion Mining](../sentiment-opinion-mining/how-to/call-api.md) und [Schnellstart: Verwenden der Textanalyse-Clientbibliothek und der REST-API](../sentiment-opinion-mining/quickstart.md). 

## <a name="feature-changes"></a>Funktionsänderungen 

Die Standpunktanalyse in Version 2.1 gibt Stimmungsbewertungen zwischen 0 (null) und 1 für alle an die API übermittelten Dokumente zurück. Bewertungen, die näher an 1 liegen, geben eine positivere Stimmung an. In der aktuellen Version dieses Features werden hingegen Stimmungsbezeichnungen (z. B. „positiv“ oder „negativ“) sowohl für die Sätze als auch für das gesamte Dokument zurückgegeben. Außerdem werden dazugehörige Zuverlässigkeitsbewertungen angegeben. 

## <a name="steps-to-migrate"></a>Migrationsschritte

### <a name="rest-api"></a>REST-API

Wenn Ihre Anwendung die REST-API nutzt, ändern Sie den Anforderungsendpunkt in den [aktuellen Endpunkt](../sentiment-opinion-mining/quickstart.md?pivots=rest-api) für die Stimmungsanalyse. Beispiel: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/sentiment`. Sie müssen auch die Anwendung aktualisieren, um Stimmungsbezeichnungen zu verwenden, die in der [API-Antwort](../sentiment-opinion-mining/how-to/call-api.md) zurückgegeben werden. 

Beispiele für die JSON-Antwort finden Sie in der Referenzdokumentation.
* [Version 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)
* [Version 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment) 
* [Version 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/Sentiment)

### <a name="client-libraries"></a>Clientbibliotheken

Für die Verwendung der aktuellen Version der Stimmungsanalyse-Clientbibliothek müssen Sie das aktuelle Softwarepaket im Namespace `Azure.AI.TextAnalytics` herunterladen. Im [Schnellstartartikel](../sentiment-opinion-mining/quickstart.md) sind die Befehle, die Sie für die von Ihnen bevorzugte Sprache verwenden können, mit Beispielcode aufgeführt.

## <a name="ner-and-entity-linking"></a>[Erkennung benannter Entitäten und Entitätsverknüpfung](#tab/named-entity-recognition)

> [!TIP]
> Möchten Sie die neueste Version der API in Ihrer Anwendung verwenden? Informationen zur aktuellen Version der APIs finden Sie in den folgenden Artikeln: NER:
> * [Schnellstart](../named-entity-recognition/quickstart.md)
> * [Aufrufen der API](../named-entity-recognition/how-to-call.md) Entitätsverknüpfung
> * [Schnellstart](../entity-linking/quickstart.md)
> * [Aufrufen der API](../entity-linking/how-to/call-api.md)

## <a name="feature-changes"></a>Funktionsänderungen

In Version 2.1 verwendet die Textanalyse-API einen Endpunkt für die Erkennung benannter Entitäten und die Entitätsverknüpfung. Die aktuelle Version dieses Features bietet die erweiterte Erkennung benannter Entitäten. Außerdem werden separate Endpunkte für Anforderungen der Erkennung benannter Entitäten und der Entitätsverknüpfung verwendet. Darüber hinaus können Sie ein anderes Feature verwenden, das im Sprachdienst angeboten wird, mit dem Sie [persönliche Informationen (pii) und Integritätsinformationen (phi)](../personally-identifiable-information/overview.md) erkennen können. 

## <a name="steps-to-migrate"></a>Migrationsschritte

### <a name="rest-api"></a>REST-API

Wenn Ihre Anwendung die REST-API nutzt, ändern Sie den Anforderungsendpunkt in die [aktuellen Endpunkte](../named-entity-recognition/quickstart.md?pivots=rest-api) für die Erkennung benannter Entitäten und/oder die Entitätsverknüpfung. Beispiel:

Entitätsverknüpfung
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/entities/linking`

NER
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/entities/recognition/general`

Sie müssen auch Ihre Anwendung aktualisieren, um die [Entitätskategorien](../named-entity-recognition/concepts/named-entity-categories.md) verwenden zu können, die in der [API-Antwort](../named-entity-recognition/how-to-call.md) zurückgegeben werden.

Beispiele für die JSON-Antwort finden Sie in der Referenzdokumentation.
* [Version 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)
* [Version 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral) 
* [Version 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/EntitiesRecognitionGeneral)

### <a name="client-libraries"></a>Clientbibliotheken

Für die Verwendung der aktuellen Version der Clientbibliotheken der Erkennung benannter Entitäten sowie der Entitätsverknüpfung müssen Sie das aktuelle Softwarepaket im Namespace `Azure.AI.TextAnalytics` herunterladen. Im Schnellstartartikel zur [Erkennung benannter Entitäten](../named-entity-recognition/quickstart.md) und der [Entitätsverknüpfung](../entity-linking/quickstart.md) sind die Befehle aufgelistet (einschließlich Beispielcode), die Sie für Ihre bevorzugte Sprache verwenden können.

#### <a name="version-21-entity-categories"></a>Entitätskategorien für Version 2.1

In der folgenden Tabelle sind die für die NER-Version 2.1 zurückgegebenen Entitätskategorien aufgeführt.

| Category   | BESCHREIBUNG                          |
|------------|--------------------------------------|
| Person   |   Namen von Personen  |
|Standort    | Natürliche und von Menschen hergestellte Wahrzeichen, Gebäude, geografische Merkmale und geopolitische Entitäten |
|Organization | Firmen, politische Gruppen, Musikgruppen, Sportvereine, Regierungsstellen und öffentliche Organisationen Nationalitäten und Religionen werden in diesem Entitätstyp nicht berücksichtigt. |
| PhoneNumber | Telefonnummern (nur US- und EU-Telefonnummern) |
| Email | E-Mail-Adressen |
| URL | URLs zu Websites |
| IP | Netzwerk-IP-Adressen |
| Datetime | Datums- und Uhrzeitangaben| 
| Date | Kalenderdatumsangaben |
| Time | Tageszeiten |
| DateRange | Datumsbereiche |
| TimeRange | Uhrzeitbereiche |
| Duration | Zeitspannen |
| Set | Festgelegte Wiederholungszeiten |
| Menge | Zahlen und numerische Mengen. |
| Number | Zahlen. |
| Prozentwert | Prozentangaben|
| Ordinal | Ordinalzahlen |
| Age | Altersangaben |
| Währung | Währungen |
| Dimension | Abmessungen und Maße |
| Temperatur | Temperaturen |

## <a name="language-detection"></a>[Sprachenerkennung](#tab/language-detection)

> [!TIP]
> Möchten Sie die neueste Version der API in Ihrer Anwendung verwenden? Informationen zur aktuellen Version der API finden Sie unter [Beispiel: Sprachenerkennung mithilfe der Textanalyse](../language-detection/how-to/call-api.md) und [Schnellstart: Verwenden der Textanalyse-Clientbibliothek und der REST-API](../language-detection/quickstart.md). 

## <a name="feature-changes"></a>Funktionsänderungen 

Die Ausgabe des Spracherkennungsfeatures wurde in der aktuellen Version geändert. Die JSON-Antwort enthält `ConfidenceScore` anstelle von `score`. Außerdem wird in der aktuellen Version für jedes Dokument nur eine einzelne Sprache in einem Attribut vom Typ `detectedLanguage` zurückgegeben.

## <a name="steps-to-migrate"></a>Migrationsschritte

### <a name="rest-api"></a>REST-API

Wenn Ihre Anwendung die REST-API nutzt, ändern Sie den Anforderungsendpunkt in den [aktuellen Endpunkt](../language-detection/quickstart.md?pivots=rest-api) für die Sprachenerkennung. Beispiel: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/languages`. Sie müssen auch die Anwendung aktualisieren, um `ConfidenceScore` anstelle von `score` in der [API-Antwort](../language-detection/how-to/call-api.md) zu verwenden. 

Beispiele für die JSON-Antwort finden Sie in der Referenzdokumentation.
* [Version 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)
* [Version 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) 
* [Version 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/Languages)

#### <a name="client-libraries"></a>Clientbibliotheken

Für die Verwendung der aktuellen Version der Stimmungsanalyse-Clientbibliothek müssen Sie das aktuelle Softwarepaket im Namespace `Azure.AI.TextAnalytics` herunterladen. Im [Schnellstartartikel](../language-detection/quickstart.md) sind die Befehle, die Sie für die von Ihnen bevorzugte Sprache verwenden können, mit Beispielcode aufgeführt.

## <a name="key-phrase-extraction"></a>[Schlüsselbegriffserkennung](#tab/key-phrase-extraction)

> [!TIP]
> Möchten Sie die neueste Version der API in Ihrer Anwendung verwenden? Informationen zur aktuellen Version der API finden Sie unter [Beispiel: Erkennen von Schlüsselbegriffen mithilfe der Textanalyse](../key-phrase-extraction/how-to/call-api.md) und [Schnellstart: Verwenden der Textanalyse-Clientbibliothek und der REST-API](../key-phrase-extraction/quickstart.md). 

## <a name="feature-changes"></a>Funktionsänderungen 

Abgesehen von der Endpunktversion wurden derzeit keine Änderungen am Feature für die Schlüsselbegriffserkennung vorgenommen.

## <a name="steps-to-migrate"></a>Migrationsschritte

### <a name="rest-api"></a>REST-API

Wenn Ihre Anwendung die REST-API nutzt, ändern Sie den Anforderungsendpunkt in den [aktuellen Endpunkt](../key-phrase-extraction/quickstart.md?pivots=rest-api) für die Schlüsselbegriffserkennung. Beispiel: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.1/keyPhrases`

Beispiele für die JSON-Antwort finden Sie in der Referenzdokumentation.
* [Version 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)
* [Version 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) 
* [Version 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/KeyPhrases)

### <a name="client-libraries"></a>Clientbibliotheken

Für die Verwendung der aktuellen Version der Stimmungsanalyse-Clientbibliothek müssen Sie das aktuelle Softwarepaket im Namespace `Azure.AI.TextAnalytics` herunterladen. Im [Schnellstartartikel](../key-phrase-extraction/quickstart.md) sind die Befehle, die Sie für die von Ihnen bevorzugte Sprache verwenden können, mit Beispielcode aufgeführt.

---

## <a name="see-also"></a>Siehe auch

* [Was ist Azure Cognitive Service für Language?](../overview.md)
