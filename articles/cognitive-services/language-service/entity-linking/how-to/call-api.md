---
title: Aufrufen der Entitätsverknüpfungs-API
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie Entitäten im Text mit der Entitätsverknüpfungs-API identifizieren und verknüpfen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: article
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-entity-linking, ignite-fall-2021
ms.openlocfilehash: a7edc27898c1af9fcb8f7bc72698d2d6c3a63e68
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021512"
---
# <a name="how-to-use-entity-linking"></a>Verwenden der Entitätsverknüpfung

Das Feature für die Entitätsverknüpfung kann verwendet werden, um die Identität einer im Text gefundenen Entität zu identifizieren und eindeutig zu machen (um beispielsweise zu ermitteln, ob das Wort „*Mars*“ den Planeten oder den römischen Kriegsgott bezeichnet). Die Entitätsverknüpfung gibt die Entitäten im Text mit Links zu [Wikipedia](https://www.wikipedia.org/) als Wissensdatenbank zurück.

> [!TIP]
> Informationen zu den ersten Schritte zur Verwendung dieses Features finden Sie im [Schnellstartartikel](../quickstart.md). Sie können auch Beispielanforderungen in [Language Studio](../../language-studio.md) erstellen, ohne Code schreiben zu müssen.

## <a name="determine-how-to-process-the-data-optional"></a>Festlegen der Art der Datenverarbeitung (optional)

### <a name="specify-the-entity-linking-model"></a>Angeben des Entitätsverknüpfungsmodells

Standardmäßig wendet die Entitätsverknüpfung das neueste verfügbare KI-Modell auf den Text an. Sie können Ihre API-Anforderungen auch für die Verwendung einer bestimmten Modellversion konfigurieren. Das angegebene Modell wird verwendet, um Entitätsverknüpfungsvorgänge durchzuführen.

| Unterstützte Versionen | Aktuelle Version |
|--|--|
| `2019-10-01`, `2020-02-01` | `2020-02-01` |

### <a name="input-languages"></a>Eingabesprachen

Bei der Übermittlung von Dokumenten zur Verarbeitung durch die Entitätsverknüpfung, können Sie angeben, in welcher [unterstützten Sprachen](../language-support.md) die Dokumente geschrieben sind. Wenn Sie keine Sprache angeben, verwendet die Entitätsverknüpfung standardmäßig Englisch. Aufgrund der [Unterstützung von Emojis und mehreren Sprachen](../../concepts/multilingual-emoji-support.md) enthält der Antworttext unter Umständen Textversätze. 

## <a name="submitting-data"></a>Übermitteln der Daten

Die Entitätsverknüpfung liefert bessere Ergebnisse, wenn Sie ihr kleinere Textblöcke zuführen. Bei anderen Feature, z. B. der Schlüsselbegriffserkennung, verhält es sich genau umgekehrt: Sie funktioniert besser, wenn sie für große Textblöcke durchgeführt wird. Um die besten Ergebnisse beider Vorgänge zu erhalten, sollten Sie die Eingaben entsprechend umstrukturieren.

Um eine API-Anforderung zu senden, benötigen Sie Endpunkt und Schlüssel für eine Sprachressource.

> [!NOTE]
> Sie finden Schlüssel und Endpunkt für Ihre Sprachressource im Azure-Portal. Sie befinden sich auf der Seite mit dem **Schlüssel und Endpunkt** der Ressource unter **Ressourcenverwaltung**. 

Die Analyse erfolgt, wenn die Anforderung eingeht. Informationen zur Größe und Anzahl von Anforderungen, die Sie pro Minute und Sekunde senden können, finden Sie in den nachstehend aufgeführten Datengrenzwerten.

Die synchrone Verwendung der Entitätsverknüpfung erfolgt zustandslos. Auf Ihrem Konto werden keine Daten gespeichert, und die Ergebnisse werden sofort in der Antwort zurückgegeben.

Wenn Sie dieses Feature asynchron verwenden, sind die API-Ergebnisse ab der Erfassung der Anforderung wie in der Antwort angegeben 24 Stunden lang verfügbar. Nach diesem Zeitraum werden die Ergebnisse endgültig gelöscht und stehen nicht mehr zum Abruf zur Verfügung.

### <a name="getting-entity-linking-results"></a>Abrufen von Ergebnissen der Entitätsverknüpfung  

Sie können die Ergebnisse an eine Anwendung streamen, oder die Ausgabe in einer Datei im lokalen System speichern.

## <a name="data-limits"></a>Datengrenzwerte

> [!NOTE]
> * Wenn Sie größere Dokumente analysieren müssen, als der Grenzwert zulässt, können Sie den Text in kleinere Textabschnitte aufteilen, bevor Sie ihn an die API senden. 
> * Ein Dokument ist eine einzelne aus Textzeichen bestehende Zeichenfolge.  

| Begrenzung | Wert |
|------------------------|---------------|
| Maximale Größe eines einzelnen Dokuments | 5\.120 Zeichen (gemessen von [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements)). |
| Maximale Zeichenanzahl pro Anforderung (asynchron)  | 125.000 Zeichen für alle übermittelten Dokumente, gemessen mithilfe von [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Maximale Größe der gesamte Anforderung | 1 MB |
| Max. Anzahl von Dokumenten pro Anforderung | 5 |

Wenn bei einem Dokument das Zeichenlimit überschritten wird, verhält sich die API unterschiedlich, je nachdem, ob Sie es synchron oder asynchron verwenden:

* Asynchron: Die API lehnt die gesamte Anforderung ab und gibt einen Fehler vom Typ `400 bad request` zurück, wenn ein enthaltenes Dokument die maximal zulässige Größe überschreitet.
* Synchron: Dokumente, die die maximal zulässige Größe überschreiten, werden von der API nicht verarbeitet, und es wird jeweils ein Fehler mit einem Hinweis auf ein ungültiges Dokument zurückgegeben. Wenn eine API-Anforderung mehrere Dokumente umfasst, fährt die API mit deren Verarbeitung fort, sofern sie sich innerhalb des Zeichenlimits befinden.

### <a name="rate-limits"></a>Ratenbegrenzungen

Die Ratenbegrenzung variiert je nach [Tarif](https://aka.ms/unifiedLanguagePricing).

| Tarif          | Anforderungen pro Sekunde | Anforderungen pro Minute |
|---------------|---------------------|---------------------|
| S/Mehrere Dienste | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |

## <a name="see-also"></a>Siehe auch

* [Übersicht über Entitätsverknüpfung](../overview.md)
