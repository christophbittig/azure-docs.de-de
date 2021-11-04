---
title: Durchführen der Erkennung benannter Entitäten (NER)
titleSuffix: Azure Cognitive Services
description: In diesem Artikel erfahren Sie, wie Sie benannte Entitäten aus Texten extrahieren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-ner, ignite-fall-2021
ms.openlocfilehash: cd6901e4ac1650f6739d24697fab5abafe8409a5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095959"
---
# <a name="how-to-use-named-entity-recognitionner"></a>Verwenden des Features zur Erkennung benannter Entitäten (Named Entity Recognition, NER)

Das NER-Feature kann unstrukturierten Text auswerten und benannte Entitäten aus Texten in mehreren vordefinierten Kategorien extrahieren, z. B. Person, Standort, Ereignis, Produkt und Organisation.  

## <a name="determine-how-to-process-the-data-optional"></a>Festlegen der Art der Datenverarbeitung (optional)

### <a name="specify-the-ner-model"></a>Angeben des NER-Modells

Standardmäßig wendet dieses Feature das neueste verfügbare KI-Modell auf Ihren Text an. Sie können Ihre API-Anforderungen auch für die Verwendung einer bestimmten Modellversion konfigurieren. Das Modell, das Sie angeben, wird zum Ausführen von NER-Vorgängen verwendet.

| Unterstützte Versionen | Aktuelle Version |
|--|--|
| `2019-10-01`, `2020-02-01`, `2020-04-01`,`2021-01-15`,`2021-06-01`  | `2021-06-01`   |


### <a name="input-languages"></a>Eingabesprachen

Wenn Sie Dokumente zur Verarbeitung übermitteln, können Sie angeben, in welcher [unterstützten Sprache](language-support.md) sie geschrieben werden. Wenn Sie keine Sprache angeben, wird die Schlüsselbegriffserkennung standardmäßig auf Englisch festgelegt. Die API gibt möglicherweise Offsets in der Antwort zurück, um verschiedene [mehrsprachige und Emoji-Codierungen](../concepts/multilingual-emoji-support.md) zu unterstützen. 

## <a name="submitting-data"></a>Übermitteln der Daten

Die Analyse erfolgt, wenn die Anforderung eingeht. Informationen zur Größe und Anzahl von Anforderungen, die Sie pro Minute und Sekunde senden können, finden Sie in den nachstehend aufgeführten Datengrenzwerten.

Die synchrone Verwendung des NER-Features ist zustandslos. Auf Ihrem Konto werden keine Daten gespeichert, und die Ergebnisse werden sofort in der Antwort zurückgegeben.

Wenn Sie diese Features asynchron verwenden, sind die API-Ergebnisse ab der Erfassung der Anforderung, wie in der Antwort angegeben, 24 Stunden lang verfügbar. Nach diesem Zeitraum werden die Ergebnisse endgültig gelöscht und stehen nicht mehr zum Abruf zur Verfügung.

Die API versucht, die [definierten Entitätskategorien](concepts/named-entity-categories.md) für eine bestimmte Dokumentsprache zu erkennen. 

## <a name="getting-ner-results"></a>Abrufen von NER-Ergebnissen

Wenn Sie Ergebnisse von der NER erhalten, können Sie diese an eine App streamen oder die Ausgabe in einer Datei im lokalen System speichern. Die API-Antwort enthält [erkannte Entitäten](concepts/named-entity-categories.md), einschließlich ihrer Kategorien und Unterkategorien sowie Konfidenzergebnisse. 

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
