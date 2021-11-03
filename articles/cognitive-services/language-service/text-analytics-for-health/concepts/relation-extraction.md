---
title: Beziehungsextrahierung in der Textanalyse für Gesundheitsdaten
titleSuffix: Azure Cognitive Services
description: Informationen zur Beziehungsextrahierung
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-health, ignite-fall-2021
ms.openlocfilehash: 6fd838471387d7ef1b54beb9ead7b802f6e041e2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029748"
---
# <a name="relation-extraction"></a>Beziehungsextrahierung

Die Beziehungsextrahierung identifiziert sinnvolle Verbindungen zwischen Begriffen, die im Text erwähnt werden. Beispielsweise wird die Beziehung „Zeit der Erkrankung“ durch Zuordnen eines Erkrankungsnamens zu einer Zeit ermittelt, oder eine Beziehung wird durch Zuordnen einer Abkürzung mit der vollständigen Beschreibung ermittelt.  


## <a name="relation-extraction-output"></a>Ausgabe der Beziehungsextraktion

Die Textanalyse für Gesundheitsdaten erkennt die Beziehungen zwischen unterschiedlichen Konzepten, einschließlich der Beziehungen zwischen Attribut und Entität (z. B. Informationen zur Körperstruktur, Medikamentendosierung) und zwischen Entitäten (z. B. Abkürzungserkennung).

> [!NOTE]
> * Beziehungen, die auf „CONDITION“ verweisen, verweisen auf den Entitätstyp „DIAGNOSIS“ oder den Entitätstyp „SYMPTOM_OR_SIGN“.
> * Beziehungen, die auf „MEDICATION“ verweisen, verweisen auf den Entitätstyp „MEDICATION_NAME“ oder den Entitätstyp „SYMPTOM_OR_SIGN“.
> * Beziehungen, die auf „TIME“ verweisen, verweisen auf den Entitätstyp „TIME“ oder den Entitätstyp „DATE“.

Die Ausgabe der Beziehungsextraktion enthält URI-Verweise und zugewiesene Rollen der Entitäten des Beziehungstyps. Beispiel im folgenden JSON-Code:

```json
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/0",
                                "role": "Dosage"
                            },
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            }
                        ]
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/2",
                                "role": "Route"
                            }
                        ]
...
]
```

## <a name="recognized-relations"></a>Erkannte Beziehungen

Die folgenden Beziehungen können von der API zurückgegeben werden. 

**ABBREVIATION**

**BODY_SITE_OF_CONDITION**

**BODY_SITE_OF_TREATMENT**

**COURSE_OF_CONDITION**

**COURSE_OF_EXAMINATION**

**COURSE_OF_MEDICATION**

**COURSE_OF_TREATMENT**

**DIRECTION_OF_BODY_STRUCTURE**

**DIRECTION_OF_CONDITION**

**DIRECTION_OF_EXAMINATION**

**DIRECTION_OF_TREATMENT**

**DOSAGE_OF_MEDICATION**

**EXAMINATION_FINDS_CONDITION**

**EXPRESSION_OF_GENE**

**EXPRESSION_OF_VARIANT**

**FORM_OF_MEDICATION**

**FREQUENCY_OF_CONDITION**

**FREQUENCY_OF_MEDICATION**

**FREQUENCY_OF_TREATMENT**

**MUTATION_TYPE_OF_GENE**

**MUTATION_TYPE_OF_VARIANT**

**QUALIFIER_OF_CONDITION**

**RELATION_OF_EXAMINATION**

**ROUTE_OF_MEDICATION** 

**SCALE_OF_CONDITION**

**TIME_OF_CONDITION**

**TIME_OF_EVENT**

**TIME_OF_EXAMINATION**

**TIME_OF_MEDICATION**

**TIME_OF_TREATMENT**

**UNIT_OF_CONDITION**

**UNIT_OF_EXAMINATION**

**VALUE_OF_CONDITION**  

**VALUE_OF_EXAMINATION**

**VARIANT_OF_GENE**
