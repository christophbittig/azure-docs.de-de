---
title: 'Muster: Der Operator „value“ in einer Richtliniendefinition'
description: Dieses Azure Policy-Muster enthält ein Beispiel für die Verwendung des Operators „value“ in einer Richtliniendefinition.
ms.date: 08/17/2021
ms.topic: sample
ms.openlocfilehash: b67f8c71e28218b6da50ddad3c2d0d773a4de8a7
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324139"
---
# <a name="azure-policy-pattern-the-value-operator"></a>Azure Policy-Muster: Der Operator „value“

Der Operator [value](../concepts/definition-structure.md#value) wertet [Parameter](../concepts/definition-structure.md#parameters), [unterstützte Vorlagenfunktionen](../concepts/definition-structure.md#policy-functions) oder Literale für einen bereitgestellten Wert aus, um eine bestimmte [Bedingung](../concepts/definition-structure.md#conditions) zu untersuchen.

> [!WARNING]
> Wenn eine _Vorlagenfunktion_ einen Fehler ergibt, schlägt die Richtlinienauswertung fehl. Eine fehlerhafte Auswertung ist ein implizites **Deny** (Verweigern). Weitere Informationen finden Sie unter [Vermeiden von Vorlagenfehlern](../concepts/definition-structure.md#avoiding-template-failures).

## <a name="sample-policy-definition"></a>Beispielrichtliniendefinition

Mit der folgenden Richtliniendefinition wird das im Parameter **tagName** (_Zeichenfolge_) angegebene Tag für Ressourcen hinzugefügt oder ersetzt, und der Wert für **tagName** wird von der Ressourcengruppe geerbt, in der sich die Ressource befindet. Diese Auswertung erfolgt, wenn die Ressource erstellt oder aktualisiert wird. Da es sich bei der Korrektur um eine Auswirkung vom Typ [modify](../concepts/effects.md#modify) handelt, kann sie für vorhandenen Ressourcen über einen [Wartungstask](../how-to/remediate-resources.md) ausgeführt werden.

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json":::

### <a name="explanation"></a>Erklärung

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json" range="20-30" highlight="7,8":::

Der Operator **value** wird innerhalb des Blocks **policyRule.if** verwendet, der sich wiederum innerhalb von **properties** befindet. In diesem Beispiel wird der [logische Operator](../concepts/definition-structure.md#logical-operators) **allOf** verwendet, um anzugeben, dass beide Bedingungsanweisungen „true“ sein müssen, damit die Auswirkung **modify** ausgelöst wird.

Mit **value** wird das Ergebnis der Vorlagenfunktion [resourceGroup()](../../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup) für die Bedingung **notEquals** eines leeren Werts ausgewertet. Wenn der in **tagName** für die übergeordnete Ressourcengruppe angegebene Tagname vorhanden ist, wird die Bedingung als „true“ ausgewertet.

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich andere [Muster und integrierte Definitionen](./index.md) an.
- Lesen Sie die Informationen unter [Struktur von Azure Policy-Definitionen](../concepts/definition-structure.md).
- Lesen Sie [Grundlegendes zu Richtlinienauswirkungen](../concepts/effects.md).