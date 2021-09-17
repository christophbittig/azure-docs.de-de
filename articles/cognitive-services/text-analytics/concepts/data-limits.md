---
title: Datengrenzwerte für die Textanalyse-API
titleSuffix: Azure Cognitive Services
description: Datenbeschränkungen für die Textanalyse-API von Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 04/07/2021
ms.author: aahi
ms.reviewer: chtufts
ms.openlocfilehash: b583caa4fdb2a1e72833d4e24c317282be041513
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122822133"
---
# <a name="data-and-rate-limits-for-the-text-analytics-api"></a>Datengrenzwerte und Ratenbegrenzungen für die Textanalyse-API
<a name="data-limits"></a>

In diesem Artikel finden Sie die Grenzwerte für die Datengröße und -rate, die beim Senden von Daten an die Textanalyse-API gelten.

## <a name="data-limits"></a>Datengrenzwerte

> [!NOTE]
> * Die Datengrenzwerte oder Ratenbegrenzungen haben keine Auswirkungen auf die Preise. Die Preise basieren auf der Anzahl der Textdatensätze, die Sie an die API senden. Weitere Informationen finden Sie in den [Preisdetails](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) Ihrer Textanalyseressource.
>   * Ein Textdatensatz umfasst 1.000 Zeichen. 
> * Daten- und Ratenbegrenzungen basieren auf der Anzahl von Dokumenten, die Sie an die API senden. Wenn Sie größere Dokumente analysieren müssen, als der Grenzwert zulässt, können Sie den Text in kleinere Textabschnitte aufteilen, bevor Sie ihn an die API senden. 
>   * Ein Dokument ist eine einzelne aus Textzeichen bestehende Zeichenfolge.  

| Begrenzung | Wert |
|------------------------|---------------|
| Maximale Größe eines einzelnen Dokuments | 5\.120 Zeichen (gemessen von [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements)). Gilt auch für die Textanalyse für Gesundheit. |
| Maximale Größe eines einzelnen Dokuments (Endpunkt: `/analyze`)  | 125.000 Zeichen (gemessen durch [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements)). Gilt nicht für die Textanalyse für Gesundheit. |
| Maximale Größe der gesamte Anforderung | 1 MB. Gilt auch für die Textanalyse für Gesundheit. |


Wenn bei einem Dokument das Zeichenlimit überschritten wird, verhält sich die API abhängig vom verwendeten Endpunkt unterschiedlich:

* `/analyze`-Endpunkt:
  * Die API lehnt die gesamte Anforderung ab und gibt einen Fehler vom Typ `400 bad request` zurück, wenn ein enthaltenes Dokument die maximal zulässige Größe überschreitet.
* Alle anderen Endpunkte:  
  * Dokumente, die die maximal zulässige Größe überschreiten, werden von der API nicht verarbeitet, und es wird jeweils ein Fehler mit einem Hinweis auf ein ungültiges Dokument zurückgegeben. Wenn eine API-Anforderung mehrere Dokumente umfasst, fährt die API mit deren Verarbeitung fort, sofern sie sich innerhalb des Zeichenlimits befinden.

Die maximale Anzahl von Dokumenten, die Sie in einer einzelnen Anforderung senden können, hängt von der verwendeten API-Version und dem verwendeten Feature ab, wie in der folgenden Tabelle beschrieben:

#### <a name="version-3"></a>[Version 3](#tab/version-3)

Die folgenden Grenzwerte gelten für die aktuelle Version 3 der API. Wenn Sie die folgenden Grenzwerte überschreiten, wird der HTTP-Fehlercode 400 generiert.


| Funktion | Max. Anzahl von Dokumenten pro Anforderung | 
|----------|-----------|
| Spracherkennung | 1000 |
| Standpunktanalyse | 10 |
| Opinion Mining | 10 |
| Schlüsselwortextraktion | 10 |
| Erkennung benannter Entitäten | 5 |
| Entitätsverknüpfung | 5 |
| Textanalyse für Gesundheit  | Zehn für die webbasierte API, 1.000 für den Container |
| Analyseendpunkt | 25 für alle Vorgänge |

#### <a name="version-2"></a>[Version 2](#tab/version-2)

| Funktion | Max. Anzahl von Dokumenten pro Anforderung | 
|----------|-----------|
| Spracherkennung | 1000 |
| Standpunktanalyse | 1000 |
| Schlüsselwortextraktion | 1000 |
| Erkennung benannter Entitäten | 1000 |
| Entitätsverknüpfung | 1000 |

---

## <a name="rate-limits"></a>Ratenbegrenzungen

Die Ratenbegrenzung variiert je nach [Tarif](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/). Diese Begrenzungen sind bei beiden Versionen der API identisch. Diese Ratenbegrenzungen gelten nicht für die Textanalyse für Integritätscontainer, für die keine Ratenbegrenzung festgelegt ist.

| Tarif          | Anforderungen pro Sekunde | Anforderungen pro Minute |
|---------------|---------------------|---------------------|
| S/Mehrere Dienste | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

Anforderungsraten werden für jedes Textanalysefeature separat gemessen. So können Sie die maximale Anzahl von Anforderungen für Ihren Tarif gleichzeitig an jedes Feature senden. Wenn Sie also beispielsweise den Tarif `S` verwenden und gleichzeitig 1.000 Anforderungen senden, können Sie 59 Sekunden lang keine weitere Anforderung senden.


Die Ebenen S0-S4 sind veraltet, und es wird ein Wechsel zur Ebene S empfohlen.

## <a name="see-also"></a>Weitere Informationen

* [Worum handelt es sich bei der Textanalyse-API?](../overview.md)
* [Preisübersicht](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)
