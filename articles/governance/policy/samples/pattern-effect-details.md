---
title: 'Muster: Auswirkungen einer Richtliniendefinition'
description: Dieses Azure Policy-Muster enthält ein Beispiel für die Verwendung der verschiedenen Auswirkungen einer Richtliniendefinition.
ms.date: 08/17/2021
ms.topic: sample
ms.openlocfilehash: 214cb0fd59eccb4f4080452f30afa44204af2f4b
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324215"
---
# <a name="azure-policy-pattern-effects"></a>Azure Policy-Muster: Auswirkungen

Azure Policy verfügt zahlreiche [Auswirkungen](../concepts/effects.md), die bestimmen, wie der Dienst auf nicht kompatible Ressourcen reagiert. Einige Auswirkungen sind einfach und erfordern keine weiteren Eigenschaften in der Richtliniendefinition. Für andere sind dagegen mehrere Eigenschaften erforderlich.

## <a name="sample-1-simple-effect"></a>Beispiel 1: Einfache Auswirkung

Mit der folgenden Richtliniendefinition wird überprüft, ob das im Parameter **tagName** definierte Tag in der ausgewerteten Ressource vorhanden ist. Ist das Tag noch nicht vorhanden, wird die Auswirkung [modify](../concepts/effects.md#modify) ausgelöst, um das Tag mit dem Wert aus dem Parameter **tagValue** hinzuzufügen.

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json":::

### <a name="sample-1-explanation"></a>Beispiel 1: Erklärung

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json" range="40-50":::

Für die Auswirkung **modify** ist der Block **policyRule.then.details** erforderlich, in dem Rollendefinitions-IDs (**roleDefinitionIds**) und Vorgänge (**operations**) definiert werden. Mithilfe dieser Parameter wird Azure Policy mitgeteilt, welche Rollen erforderlich sind, um das Tag hinzuzufügen und die Ressource zu korrigieren, und welcher Vorgang vom Typ **modify** verwendet werden soll. In diesem Beispiel wird der **Vorgang** _add_ verwendet. Die Parameter dienen zum Festlegen des Tags und des zugehörigen Werts.

## <a name="sample-2-complex-effect"></a>Beispiel 2: Komplexe Auswirkung

Mit der folgenden Richtliniendefinition wird jeder virtuelle Computer daraufhin überprüft, ob eine in den Parametern **publisher** und **type** definierte Erweiterung vorhanden ist. Mithilfe von [auditIfNotExists](../concepts/effects.md#auditifnotexists) wird eine mit dem virtuellen Computer zusammenhängende Ressource überprüft, um zu ermitteln, ob eine den definierten Parametern entsprechende Instanz vorhanden ist. In diesem Beispiel wird der Typ **extensions** überprüft.

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json":::

### <a name="sample-2-explanation"></a>Beispiel 2: Erklärung

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json" range="45-58":::

Für die Auswirkung **auditIfNotExists** ist der Block **policyRule.then.details** erforderlich, um sowohl einen Typ (**type**) als auch die gewünschte Existenzbedingung (**existenceCondition**) zu definieren. Für die Existenzbedingung (**existenceCondition**) werden Elemente der Richtliniensprache wie etwa [logische Operatoren](../concepts/definition-structure.md#logical-operators) verwendet, um zu bestimmen, ob eine passende zugehörige Ressource vorhanden ist. In diesem Beispiel werden die Werte, die für jeden [Alias](../concepts/definition-structure.md#aliases) überprüft werden, in Parametern definiert.

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich andere [Muster und integrierte Definitionen](./index.md) an.
- Lesen Sie die Informationen unter [Struktur von Azure Policy-Definitionen](../concepts/definition-structure.md).
- Lesen Sie [Grundlegendes zu Richtlinienauswirkungen](../concepts/effects.md).
