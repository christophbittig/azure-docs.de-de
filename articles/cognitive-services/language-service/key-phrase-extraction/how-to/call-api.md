---
title: Aufrufen der Schlüsselbegriffserkennungs-API
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie Schlüsselbegriffe mithilfe der Schlüsselbegriffserkennungs-API extrahieren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: article
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-key-phrase, ignite-fall-2021
ms.openlocfilehash: 72a7d077b5ee7752f7b91a2f5db5e1b5614c4ddd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095268"
---
# <a name="how-to-use-key-phrase-extraction"></a>Verwenden der Schlüsselbegriffserkennung 

Die Schlüsselbegriffserkennung kann unstrukturierten Text bewerten und gibt für jedes Dokument eine Liste mit Schlüsselbegriffen zurück.

Dieses Feature ist nützlich, wenn Sie die wichtigsten Punkte aus mehreren Dokumenten schnell identifizieren müssen. Wenn der eingegebene Text beispielsweise „*Das Essen war köstlich, und das Personal war vorbildlich*“ lautet, gibt der Dienst die Kernpunkte „*Essen*“ und „*Personal war vorbildlich*“ zurück.

> [!TIP]
> Informationen zu den ersten Schritte zur Verwendung dieses Features finden Sie im [Schnellstartartikel](../quickstart.md). Sie können auch Beispielanforderungen in [Language Studio](../../language-studio.md) erstellen, ohne Code schreiben zu müssen.


## <a name="determine-how-to-process-the-data-optional"></a>Festlegen der Art der Datenverarbeitung (optional)

### <a name="specify-the-key-phrase-extraction-model"></a>Angeben des Schlüsselbegriffserkennungsmodells

Standardmäßig verwendet die Schlüsselbegriffserkennung das neueste verfügbare KI-Modell für Ihren Text. Sie können Ihre API-Anforderungen auch für die Verwendung einer bestimmten Modellversion konfigurieren. Das festgelegte Modell wird verwendet, um Schlüsselbegriffserkennungsvorgänge durchzuführen.

| Unterstützte Versionen | Aktuelle Version |
|--|--|
| `2019-10-01`, `2020-07-01`, `2021-06-01`  | `2021-06-01`   |

### <a name="input-languages"></a>Eingabesprachen

Wenn Sie Dokumente übermitteln, die von der Schlüsselbegriffserkennung verarbeitet werden sollen, können Sie angeben, in welchen [unterstützten Sprachen](../language-support.md) die Dokumente geschrieben sind. Wenn Sie keine Sprache angeben, wird die Schlüsselbegriffserkennung standardmäßig auf Englisch festgelegt. Die API gibt möglicherweise Offsets in der Antwort zurück, um verschiedene [mehrsprachige und Emoji-Codierungen](../../concepts/multilingual-emoji-support.md) zu unterstützen. 

## <a name="submitting-data"></a>Übermitteln der Daten

Die Schlüsselbegriffserkennung funktioniert am besten, wenn Sie ihr größere Texte zur Verarbeitung übergeben. Dies steht im Gegensatz zur Standpunktanalyse, die mit kleineren Texten besser funktioniert. Um für beide Vorgänge optimale Ergebnisse zu erzielen, empfiehlt es sich ggf., die Eingaben entsprechend umzustrukturieren.

Um eine API-Anforderung zu senden, benötigen Sie den Endpunkt und den Schlüssel für Ihre Sprachressource.

> [!NOTE]
> Sie finden Schlüssel und Endpunkt für Ihre Sprachressource im Azure-Portal. Sie befinden sich auf der Seite mit dem **Schlüssel und Endpunkt** der Ressource unter **Ressourcenverwaltung**. 

Die Analyse erfolgt, wenn die Anforderung eingeht. Informationen zur Größe und Anzahl von Anforderungen, die Sie pro Minute und Sekunde senden können, finden Sie in den nachstehend aufgeführten Datengrenzwerten.

Die synchrone Verwendung der Schlüsselbegriffserkennung ist zustandslos. Auf Ihrem Konto werden keine Daten gespeichert, und die Ergebnisse werden sofort in der Antwort zurückgegeben.

Wenn Sie dieses Feature asynchron verwenden, sind die API-Ergebnisse ab der Erfassung der Anforderung wie in der Antwort angegeben 24 Stunden lang verfügbar. Nach diesem Zeitraum werden die Ergebnisse endgültig gelöscht und stehen nicht mehr zum Abruf zur Verfügung.


## <a name="getting-key-phrase-extraction-results"></a>Abrufen der Ergebnisse der Schlüsselbegriffserkennung

Wenn Sie Ergebnisse von der API erhalten, wird die Reihenfolge der zurückgegebenen Schlüsselbegriffe intern durch das Modell bestimmt. Sie können die Ergebnisse an eine Anwendung streamen, oder die Ausgabe in einer Datei im lokalen System speichern.

## <a name="data-limits"></a>Datengrenzwerte

> [!NOTE]
> * Wenn Sie größere Dokumente analysieren müssen, als der Grenzwert zulässt, können Sie den Text in kleinere Textabschnitte aufteilen, bevor Sie ihn an die API senden. 
> * Ein Dokument ist eine einzelne aus Textzeichen bestehende Zeichenfolge.  

| Begrenzung | Wert | 
|------------------------|---------------|
| Maximale Größe eines einzelnen Dokuments (synchron) | 5\.120 Zeichen (gemessen von [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements)). |
| Maximale Zeichenanzahl pro Anforderung (asynchron) | 125.000 Zeichen für alle übermittelten Dokumente, gemessen mithilfe von [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Maximale Größe der gesamte Anforderung | 1 MB.  |
| Max. Anzahl von Dokumenten pro Anforderung |  10 |

Wenn bei einem Dokument das Zeichenlimit überschritten wird, verhält sich die API abhängig vom verwendeten Endpunkt unterschiedlich:

* Asynchron: Die API lehnt die gesamte Anforderung ab und gibt einen Fehler vom Typ `400 bad request` zurück, wenn ein enthaltenes Dokument die maximal zulässige Größe überschreitet.
* Synchron: Dokumente, die die maximal zulässige Größe überschreiten, werden von der API nicht verarbeitet, und es wird jeweils ein Fehler mit einem Hinweis auf ein ungültiges Dokument zurückgegeben. Wenn eine API-Anforderung mehrere Dokumente umfasst, fährt die API mit deren Verarbeitung fort, sofern sie sich innerhalb des Zeichenlimits befinden.

### <a name="rate-limits"></a>Ratenbegrenzungen

Die Ratenbegrenzung variiert je nach [Tarif](https://aka.ms/unifiedLanguagePricing).

| Tarif          | Anforderungen pro Sekunde | Anforderungen pro Minute |
|---------------|---------------------|---------------------|
| S/Mehrere Dienste | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |

## <a name="next-steps"></a>Nächste Schritte

[Übersicht über die Schlüsselbegriffserkennung](../overview.md)
