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
ms.openlocfilehash: e10c1f8622e1bbb60a3fbb4f5d6fd5bd3da46255
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/09/2021
ms.locfileid: "113550442"
---
# <a name="migrate-to-version-3x-of-the-text-analytics-api"></a>Migration zu Version 3 der Textanalyse-API

Wenn Sie die Version 2.1 der Textanalyse-API verwenden, unterstützt dieser Artikel Sie dabei, ein Upgrade zur Verwendung von Version 3.x für Ihre Anwendung durchzuführen. Die Versionen 3.1 und 3.0 sind allgemein verfügbar und führen neue Features wie die erweiterte [Erkennung benannter Entitäten](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-features-and-versions) und die [Versionsverwaltung für Modelle](concepts/model-versioning.md) ein. Version 3.1 ist ebenfalls verfügbar, in der Features wie [Opinion Mining](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) und Erkennung [personenbezogener Informationen](how-tos/text-analytics-how-to-entity-linking.md?tabs=version-3-1#personally-identifiable-information-pii) hinzugefügt wurden. Für die Modelle in Version 2 oder 3.1-preview.x werden keine weiteren Updates veröffentlicht. 

## <a name="sentiment-analysis"></a>[Standpunktanalyse](#tab/sentiment-analysis)

> [!TIP]
> Möchten Sie die neueste Version der API in Ihrer Anwendung verwenden? Informationen zur aktuellen Version der API finden Sie unter [Vorgehensweise: Stimmungsanalyse und Opinion Mining](how-tos/text-analytics-how-to-sentiment-analysis.md) und [Schnellstart: Verwenden der Textanalyse-Clientbibliothek und der REST-API](quickstarts/client-libraries-rest-api.md). 

### <a name="feature-changes"></a>Funktionsänderungen 

Die Standpunktanalyse in Version 2.1 gibt Stimmungsbewertungen zwischen 0 (null) und 1 für alle an die API übermittelten Dokumente zurück. Bewertungen, die näher an 1 liegen, geben eine positivere Stimmung an. In Version 3 werden hingegen Stimmungsbezeichnungen (z. B. „positiv“ oder „negativ“) sowohl für die Sätze als auch für das gesamte Dokument zurückgegeben. Außerdem werden dazugehörige Zuverlässigkeitsbewertungen angegeben. 

### <a name="steps-to-migrate"></a>Migrationsschritte

#### <a name="rest-api"></a>REST-API

Wenn Ihre Anwendung die REST-API nutzt, ändern Sie den Anforderungsendpunkt in den Endpunkt der Version 3 für die Stimmungsanalyse. Beispiel: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/sentiment`. Sie müssen auch die Anwendung aktualisieren, um Stimmungsbezeichnungen zu verwenden, die in der [API-Antwort](how-tos/text-analytics-how-to-sentiment-analysis.md#view-the-results) zurückgegeben werden. 

Beispiele für die JSON-Antwort finden Sie in der Referenzdokumentation.
* [Version 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)
* [Version 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment) 
* [Version 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/Sentiment)

#### <a name="client-libraries"></a>Clientbibliotheken

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

## <a name="ner-and-entity-linking"></a>[Erkennung benannter Entitäten und Entitätsverknüpfung](#tab/named-entity-recognition)

> [!TIP]
> Möchten Sie die neueste Version der API in Ihrer Anwendung verwenden? Informationen zur aktuellen Version der API finden Sie unter [Verwenden der Erkennung benannter Entitäten in der Textanalyse](how-tos/text-analytics-how-to-entity-linking.md) und [Schnellstart: Verwenden der Textanalyse-Clientbibliothek und der REST-API](quickstarts/client-libraries-rest-api.md). 

### <a name="feature-changes"></a>Funktionsänderungen

In Version 2.1 verwendet die Textanalyse-API einen Endpunkt für die Erkennung benannter Entitäten und die Entitätsverknüpfung. In Version 3 wird die erweiterte Erkennung benannter Entitäten bereitgestellt. Außerdem werden separate Endpunkte für Anforderungen der Erkennung benannter Entitäten und der Entitätsverknüpfung verwendet. In v3.1 kann die Erkennung benannter Entitäten auch persönliche Informationen (`pii`) und Gesundheitsinformationen (`phi`) ermitteln. 

### <a name="steps-to-migrate"></a>Migrationsschritte

#### <a name="rest-api"></a>REST-API

Wenn Ihre Anwendung die REST-API nutzt, ändern Sie den Anforderungsendpunkt in die Endpunkte der Version 3 für die Erkennung benannter Entitäten und/oder die Entitätsverknüpfung.

Entitätsverknüpfung
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/entities/linking`

NER
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/entities/recognition/general`

Sie müssen auch Ihre Anwendung aktualisieren, um die [Entitätskategorien](named-entity-types.md) verwenden zu können, die in der [API-Antwort](how-tos/text-analytics-how-to-entity-linking.md#view-results) zurückgegeben werden.

Beispiele für die JSON-Antwort finden Sie in der Referenzdokumentation.
* [Version 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)
* [Version 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral) 
* [Version 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/EntitiesRecognitionGeneral)

#### <a name="client-libraries"></a>Clientbibliotheken

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

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
> Möchten Sie die neueste Version der API in Ihrer Anwendung verwenden? Informationen zur aktuellen Version der API finden Sie unter [Beispiel: Sprachenerkennung mithilfe der Textanalyse](how-tos/text-analytics-how-to-language-detection.md) und [Schnellstart: Verwenden der Textanalyse-Clientbibliothek und der REST-API](quickstarts/client-libraries-rest-api.md). 

### <a name="feature-changes"></a>Funktionsänderungen 

Die Ausgabe des Spracherkennungsfeatures hat sich in v3 geändert. Die JSON-Antwort enthält `ConfidenceScore` anstelle von `score`. Außerdem wird in v3 für jedes Dokument nur eine einzelne Sprache in einem Attribut vom Typ `detectedLanguage` zurückgegeben.

### <a name="steps-to-migrate"></a>Migrationsschritte

#### <a name="rest-api"></a>REST-API

Wenn Ihre Anwendung die REST-API nutzt, ändern Sie den Anforderungsendpunkt in den Endpunkt der Version 3 für die Sprachenerkennung. Beispiel: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/languages`. Sie müssen auch die Anwendung aktualisieren, um `ConfidenceScore` anstelle von `score` in der [API-Antwort](how-tos/text-analytics-how-to-language-detection.md#step-3-view-the-results) zu verwenden. 

Beispiele für die JSON-Antwort finden Sie in der Referenzdokumentation.
* [Version 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)
* [Version 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) 
* [Version 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/Languages)

#### <a name="client-libraries"></a>Clientbibliotheken

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

## <a name="key-phrase-extraction"></a>[Schlüsselbegriffserkennung](#tab/key-phrase-extraction)

> [!TIP]
> Möchten Sie die neueste Version der API in Ihrer Anwendung verwenden? Informationen zur aktuellen Version der API finden Sie unter [Beispiel: Erkennen von Schlüsselbegriffen mithilfe der Textanalyse](how-tos/text-analytics-how-to-keyword-extraction.md) und [Schnellstart: Verwenden der Textanalyse-Clientbibliothek und der REST-API](quickstarts/client-libraries-rest-api.md). 

### <a name="feature-changes"></a>Funktionsänderungen 

Abgesehen von der Endpunktversion wurden in Version 3 keine Änderungen am Feature für die Schlüsselbegriffserkennung vorgenommen.

### <a name="steps-to-migrate"></a>Migrationsschritte

#### <a name="rest-api"></a>REST-API

Wenn Ihre Anwendung die REST-API nutzt, ändern Sie den Anforderungsendpunkt in den Endpunkt der Version 3 für die Schlüsselbegriffserkennung. Beispiel: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`

Beispiele für die JSON-Antwort finden Sie in der Referenzdokumentation.
* [Version 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)
* [Version 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) 
* [Version 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/KeyPhrases)

#### <a name="client-libraries"></a>Clientbibliotheken

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

---

## <a name="see-also"></a>Weitere Informationen

* [Worum handelt es sich bei der Textanalyse-API?](overview.md)
* [Sprachunterstützung](language-support.md)
* [Versionsverwaltung für Modelle](concepts/model-versioning.md)
