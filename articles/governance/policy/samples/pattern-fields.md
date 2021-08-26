---
title: 'Muster: Feldeigenschaften in einer Richtliniendefinition'
description: Dieses Azure Policy-Muster enthält ein Beispiel für die Verwendung von Feldeigenschaften in einer Richtliniendefinition.
ms.date: 08/17/2021
ms.topic: sample
ms.openlocfilehash: 732075527d300c8e474536787ec378aaa323241a
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324183"
---
# <a name="azure-policy-pattern-field-properties"></a>Azure Policy-Muster: Feldeigenschaften

Der Operator [field](../concepts/definition-structure.md#fields) wertet die angegebene Eigenschaft oder den angegebenen [Alias](../concepts/definition-structure.md#aliases) für einen bereitgestellten Wert aus, um eine bestimmte [Bedingung](../concepts/definition-structure.md#conditions) zu untersuchen.

## <a name="sample-policy-definition"></a>Beispielrichtliniendefinition

Mit der folgenden Richtliniendefinition können Sie zulässige Regionen definieren, die die Anforderungen Ihrer Organisation im Zusammenhang mit geografischen Standorten erfüllen. Die zulässigen Ressourcen werden im Parameter **listOfAllowedLocations** (_array_) definiert. Ressourcen, die der Definition entsprechen, werden [abgelehnt](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json":::

### <a name="explanation"></a>Erklärung

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json" range="18-36" highlight="3,7,11":::

Der Operator **field** wird dreimal innerhalb des [logischen Operators](../concepts/definition-structure.md#logical-operators) **allOf** verwendet.

- Das erste Vorkommen wertet die Eigenschaft `location` mit der Bedingung **notIn** für den Parameter **listOfAllowedLocations** aus. **notIn** funktioniert, da ein _Array_ erwartet wird und es sich bei dem Parameter um ein _Array_ handelt. Wenn sich der Standort (`location`) der erstellten oder aktualisierten Ressource nicht in der genehmigten Liste befindet, wird dieses Element als „true“ ausgewertet.
- Das zweite Vorkommen wertet ebenfalls die Eigenschaft `location` aus, verwendet jedoch die Bedingung **notEquals**, um zu ermitteln, ob die Ressource _global_ ist. Wenn der Standort (`location`) der erstellten oder aktualisierten Ressource nicht _global_ ist, wird dieses Element als „true“ ausgewertet.
- Das letzte Vorkommen wertet die Eigenschaft `type` aus und verwendet die Bedingung **notEquals**, um sicherzustellen, dass der Ressourcentyp nicht _Microsoft.AzureActiveDirectory/b2cDirectories_ lautet. Ist dies nicht der Fall, wird das Element als „true“ ausgewertet.

Wenn alle drei Bedingungsanweisungen im logischen Operator **allOf** als „true“ ausgewertet werden, wird die Ressourcenerstellung oder -aktualisierung von Azure Policy blockiert.

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich andere [Muster und integrierte Definitionen](./index.md) an.
- Lesen Sie die Informationen unter [Struktur von Azure Policy-Definitionen](../concepts/definition-structure.md).
- Lesen Sie [Grundlegendes zu Richtlinienauswirkungen](../concepts/effects.md).