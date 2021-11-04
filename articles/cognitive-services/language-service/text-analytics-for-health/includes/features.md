---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: bf17375352b7c5ac4751ec14e75beb4f38b5472d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095075"
---
# <a name="named-entity-recognition"></a>[Erkennung benannter Entitäten](#tab/ner)

Die Erkennung benannter Entitäten erkennt Wörter und Ausdrücke in unstrukturiertem Text, die einem oder mehr semantischen Typen zugeordnet werden können, wie etwa Diagnose, Namen von Medikamenten, Symptom/Auffälligkeit oder Alter.

> [!div class="mx-imgBorder"]
> ![Textanalyse für NER im Gesundheitswesen](../media/call-api/health-named-entity-recognition.png)

# <a name="relation-extraction"></a>[Beziehungsextrahierung](#tab/relation-extraction)

Die Beziehungsextrahierung identifiziert sinnvolle Verbindungen zwischen Begriffen, die im Text erwähnt werden. Beispielsweise wird die Beziehung „Zeit der Erkrankung“ durch Zuordnen eines Erkrankungsnamens zu einer Zeit ermittelt, oder eine Beziehung wird durch Zuordnen einer Abkürzung mit der vollständigen Beschreibung ermittelt.  

> [!div class="mx-imgBorder"]
> ![Textanalyse für die Beziehungsextrahierung von Gesundheitsdaten](../media/call-api/health-relation-extraction.png)


# <a name="entity-linking"></a>[Entitätsverknüpfung](#tab/entity-linking)

Durch Entitätsverknüpfung werden verschiedene Entitäten unterschieden, indem im Text erwähnte benannte Entitäten Konzepten aus einer vordefinierten Konzeptdatenbank, einschließlich des Unified Medical Language System (UMLS), zugeordnet werden. Medizinischen Konzepten wird als zusätzliche Form der Normalisierung auch eine bevorzugte Benennung zugewiesen.

> [!div class="mx-imgBorder"]
> ![Textanalyse für die Entitätsverknüpfung für Gesundheitsdaten](../media/call-api/health-entity-linking.png)

Text Analytics for Health unterstützt die Verknüpfung mit den Vokabularen für Gesundheit und Biomedizin in der Metathesaurus Knowledge Source des Unified Medical Language System ([UMLS](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)).

# <a name="assertion-detection"></a>[Assertionserkennung](#tab/assertion-detection) 

Die Bedeutung medizinischer Inhalte wird in hohem Maße durch Modifizierer beeinflusst, z. B. negative oder bedingte Assertionen, die bei Fehldarstellung kritische Auswirkungen haben können. Text Analytics for Health unterstützt drei Kategorien der Assertionserkennung für Entitäten im Text: 

* Sicherheit
* Bedingt
* Zuordnung

> [!div class="mx-imgBorder"]
> ![Textanalyse für die Negation von Gesundheitsdaten](../media/call-api/assertions.png)

---
