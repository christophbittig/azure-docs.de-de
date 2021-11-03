---
title: Erkennen von personenbezogenen Informationen (PII)
titleSuffix: Azure Cognitive Services
description: In diesem Artikel erfahren Sie, wie Sie PII und Integritätsinformationen (PHI) aus Texten extrahieren und personenbezogene Informationen erkennen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-pii, ignite-fall-2021
ms.openlocfilehash: 93f232233da0310881539441fdb296ac4ae39b62
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095665"
---
# <a name="how-to-detect-and-redact-personally-identifying-information-pii"></a>Erkennen und Redigieren von personenbezogenen Informationen (Personally Identifying Information, PII)

Das PII-Feature kann unstrukturierten Text auswerten, vertrauliche Informationen (PII) und Integritätsinformationen (Health Information, PHI) aus Texten in mehreren vordefinierten Kategorien extrahieren.

## <a name="determine-how-to-process-the-data-optional"></a>Festlegen der Art der Datenverarbeitung (optional)

### <a name="specify-the-pii-detection-model"></a>Angeben des PII-Erkennungsmodells

Standardmäßig wendet dieses Feature das neueste verfügbare KI-Modell auf Ihren Text an. Sie können Ihre API-Anforderungen auch für die Verwendung einer bestimmten Modellversion konfigurieren. Das Modell, das Sie angeben, wird zum Ausführen von NER- und PII-Vorgängen verwendet.

| Unterstützte Versionen | Aktuelle Version |
|--|--|
| `2019-10-01`, `2020-02-01`, `2020-04-01`,`2020-07-01`, `2021-01-15`  | `2021-01-15`   |

### <a name="input-languages"></a>Eingabesprachen

Wenn Sie Dokumente zur Verarbeitung übermitteln, können Sie angeben, in welcher [unterstützten Sprache](language-support.md) sie geschrieben werden. Wenn Sie keine Sprache angeben, wird die Schlüsselbegriffserkennung standardmäßig auf Englisch festgelegt. Die API gibt möglicherweise Offsets in der Antwort zurück, um verschiedene [mehrsprachige und Emoji-Codierungen](../concepts/multilingual-emoji-support.md) zu unterstützen. 

## <a name="submitting-data"></a>Übermitteln der Daten

Die Analyse erfolgt, wenn die Anforderung eingeht. Informationen zur Größe und Anzahl von Anforderungen, die Sie pro Minute und Sekunde senden können, finden Sie in den nachstehend aufgeführten Datengrenzwerten.

Die synchrone Verwendung des PII-Erkennungsfeatures ist zustandslos. Auf Ihrem Konto werden keine Daten gespeichert, und die Ergebnisse werden sofort in der Antwort zurückgegeben.

Wenn Sie diese Features asynchron verwenden, sind die API-Ergebnisse ab der Erfassung der Anforderung, wie in der Antwort angegeben, 48 Stunden lang verfügbar. Nach diesem Zeitraum werden die Ergebnisse endgültig gelöscht und stehen nicht mehr zum Abruf zur Verfügung.

Die API versucht, die [definierten Entitätskategorien](concepts/entity-categories.md) für eine bestimmte Dokumentsprache zu erkennen. Wenn Sie angeben möchten, welche Entitäten erkannt und zurückgegeben werden sollen, verwenden Sie den optionalen Parameter `piiCategories` mit den entsprechenden Entitätskategorien. Mit diesem Parameter lassen sich auch Entitäten erkennen, die für Ihre Dokumentsprache standardmäßig nicht aktiviert sind. Die folgende Beispiel-URL erkennt die Führerscheinnummer eines französischen Führerscheins, die in einem englischen Text vorkommt, zusammen mit den englischen Standardentitäten.

> [!TIP]
> Wenn Sie beim Angeben von Entitätskategorien `default` nicht angeben, gibt die API nur die von Ihnen angegebenen Entitätskategorien zurück.

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.5/entities/recognition/pii?piiCategories=default,FRDriversLicenseNumber`

## <a name="getting-pii-results"></a>Abrufen von PII-Ergebnissen

Wenn Sie Ergebnisse von der PII-Erkennung erhalten, können Sie diese an eine App streamen oder die Ausgabe in einer Datei im lokalen System speichern. Die API-Antwort enthält [erkannte Entitäten](concepts/entity-categories.md), einschließlich ihrer Kategorien und Unterkategorien sowie Konfidenzergebnisse. Die Textzeichenfolge mit den redigierten PII-Entitäten wird ebenfalls zurückgegeben.

## <a name="data-limits"></a>Datengrenzwerte

> [!NOTE]
> * Wenn Sie größere Dokumente analysieren müssen, als der Grenzwert zulässt, können Sie den Text in kleinere Textabschnitte aufteilen, bevor Sie ihn an die API senden. 
> * Ein Dokument ist eine einzelne aus Textzeichen bestehende Zeichenfolge.  

| Begrenzung | Wert |
|------------------------|---------------|
| Maximale Größe eines einzelnen Dokuments (synchron) | 5\.120 Zeichen (gemessen von [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements)).  |
| Maximale Zeichenanzahl pro Anforderung (asynchron)  | 125.000 Zeichen für alle übermittelten Dokumente, gemessen mithilfe von [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements).  |
| Maximale Größe der gesamte Anforderung | 1 MB. |
| Max. Anzahl von Dokumenten pro Anforderung | 5 |

Wenn bei einem Dokument das Zeichenlimit überschritten wird, verhält sich die API abhängig vom verwendeten Feature unterschiedlich:

* Asynchron:
  * Die API lehnt die gesamte Anforderung ab und gibt einen Fehler vom Typ `400 bad request` zurück, wenn ein enthaltenes Dokument die maximal zulässige Größe überschreitet.
* Synchron:  
  * Dokumente, die die maximal zulässige Größe überschreiten, werden von der API nicht verarbeitet, und es wird jeweils ein Fehler mit einem Hinweis auf ein ungültiges Dokument zurückgegeben. Wenn eine API-Anforderung mehrere Dokumente umfasst, fährt die API mit deren Verarbeitung fort, sofern sie sich innerhalb des Zeichenlimits befinden.

Wenn die maximale Anzahl von Dokumenten überschritten wird, die Sie in einer einzelnen Anforderung senden können, wird ein HTTP 400-Fehlercode generiert.

### <a name="rate-limits"></a>Ratenbegrenzungen

Die Ratenbegrenzung variiert je nach [Tarif](https://aka.ms/unifiedLanguagePricing).

| Tarif          | Anforderungen pro Sekunde | Anforderungen pro Minute |
|---------------|---------------------|---------------------|
| S/Mehrere Dienste | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |

## <a name="next-steps"></a>Nächste Schritte

[Erkennung benannter Entitäten (Übersicht)](overview.md)
