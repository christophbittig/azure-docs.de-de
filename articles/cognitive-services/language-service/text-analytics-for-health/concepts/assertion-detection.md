---
title: Assertionserkennung in der Textanalyse für Gesundheitsdaten
titleSuffix: Azure Cognitive Services
description: Erfahren Sie mehr über die Assertionserkennung.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-health, ignite-fall-2021
ms.openlocfilehash: 0f9d24e43bd4525a703027cff6a5ee4bec7bd431
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029756"
---
# <a name="assertion-detection"></a>Assertionserkennung

Die Bedeutung medizinischer Inhalte wird in hohem Maße durch Modifizierer beeinflusst, z. B. negative oder bedingte Assertionen, die bei Fehldarstellung kritische Auswirkungen haben können. Text Analytics for Health unterstützt drei Kategorien der Assertionserkennung für Entitäten im Text: 

* Sicherheit
* Bedingt
* Zuordnung

## <a name="assertion-output"></a>Assertionsausgabe

Text Analytics for Health gibt Assertionsmodifizierer zurück. Dies sind Informationsattribute, die medizinischen Konzepten zugewiesen sind und das Verständnis des Kontexts der Konzepte im Text vertiefen. Diese Modifizierer werden in drei Kategorien unterteilt, die jeweils einen anderen Aspekt betreffen und einen Satz von sich gegenseitig ausschließenden Werten enthalten. Jeder Entität wird nur ein Wert pro Kategorie zugewiesen. Der häufigste Wert für jede Kategorie ist der Standardwert. Die vom Dienst ausgegebene Antwort enthält nur Assertionsmodifizierer, die sich vom Standardwert unterscheiden.

**CERTAINTY**: Gibt an, ob das Konzept vorhanden ist. Gibt außerdem den Grad der Gewissheit im Text in Bezug auf das Vorhandensein des Konzepts (eindeutig oder möglich) an.
*   **Positive** [Standard]: Das Konzept ist vorhanden oder ist aufgetreten.
* **Negative**: Das Konzept ist jetzt nicht vorhanden oder ist nie aufgetreten.
* **Positive_Possible**: Das Konzept ist wahrscheinlich vorhanden, aber es gibt einen bestimmten Grad an Ungewissheit.
* **Negative_Possible**: Das Konzept ist wahrscheinlich nicht vorhanden, aber es gibt einen bestimmten Grad an Ungewissheit.
* **Neutral_Possible**: Das Konzept ist vorhanden oder nicht vorhanden, mit dem gleichen Grad an Gewissheit für beide Möglichkeiten.

**CONDITIONALITY**: Gibt an, ob das Vorhandensein eines Konzepts von bestimmten Bedingungen abhängt. 
*   **None** [Standard]: Das Konzept ist ein Fakt und weder hypothetisch noch von bestimmten Bedingungen abhängig.
*   **Hypothetical**: Das Konzept kann in Zukunft entstehen oder auftreten.
*   **Conditional**: Das Konzept ist vorhanden oder tritt nur unter bestimmten Bedingungen auf.

**ASSOCIATION**: Gibt an, ob das Konzept der im Text beschriebenen Person oder einer anderen Person zugeordnet ist.
*   **Subject** [Standard]: Das Konzept ist der im Text beschriebenen Person (in der Regel der Patient) zugeordnet.
*   **Someone_Else**: Das Konzept ist einer anderen als der im Text beschriebenen Person zugeordnet.


Die Assertionserkennung stellt negierte Entitäten als negativen Wert für die Kategorie „certainty“ dar, wie im folgenden Beispiel:

```json
{
    "offset": 381,
    "length": 3,
    "text": "SOB",
    "category": "SymptomOrSign",
    "confidenceScore": 0.98,
    "assertion": {
        "certainty": "negative"
    },
    "name": "Dyspnea",
    "links": [
        {
            "dataSource": "UMLS",
            "id": "C0013404"
        },
        {
            "dataSource": "AOD",
            "id": "0000005442"
        },
    ...
}
```

## <a name="next-steps"></a>Nächste Schritte

[Aufrufen der Textanalyse für Gesundheitsdaten](../how-to/call-api.md)
