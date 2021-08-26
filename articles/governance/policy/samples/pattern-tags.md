---
title: 'Muster: Verwenden von Tags in einer Richtliniendefinition'
description: Dieses Azure Policy-Muster bietet Beispiele für das Hinzufügen parametrisierter Tags oder das Erben von Tags einer Ressourcengruppe in einer Richtliniendefinition.
ms.date: 08/17/2021
ms.topic: sample
ms.openlocfilehash: 71f560c9a5eafb477f01e65d493987f8ae67bdaa
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324106"
---
# <a name="azure-policy-pattern-tags"></a>Azure Policy-Muster: Tags

[Tags](../../../azure-resource-manager/management/tag-resources.md) sind ein wichtiger Bestandteil der Verwaltung, Organisation und Steuerung Ihrer Azure-Ressourcen. Azure Policy bietet Ihnen die Möglichkeit, Tags für Ihre neuen und bestehenden Ressourcen mit der Auswirkung [Modify (Ändern)](../concepts/effects.md#modify) und mithilfe von [Wartungstasks](../how-to/remediate-resources.md) im gewünschten Umfang zu konfigurieren.

## <a name="sample-1-parameterize-tags"></a>Beispiel 1: Parametrisieren von Tags

In dieser Richtliniendefinition werden zwei Parameter (**tagName** und **tagValue**) verwendet, um festzulegen, wonach die Richtlinienzuweisung in Ressourcengruppen suchen soll. Mit diesem Format kann die Richtliniendefinition für beliebig viele Kombinationen aus Tagname und Tagwert verwendet werden, ohne mehrere Richtliniendefinitionen verwalten zu müssen.

> [!NOTE]
> Dieses Richtliniendefinitionsmuster ähnelt dem unter [Muster: Parameter – Beispiel 1](./pattern-parameters.md#sample-1-string-parameters) aufgeführten Muster. Aber in diesem Beispiel hier ist der Modus (**mode**) auf _All_ festgelegt, und als Ziel werden Ressourcengruppen verwendet.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json":::

### <a name="sample-1-explanation"></a>Beispiel 1: Erklärung

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="2-8" highlight="3":::

In diesem Beispiel ist **mode** auf _All_ festgelegt, weil eine Ressourcengruppe als Ziel verwendet wird. In den meisten Fällen sollte **mode** beim Arbeiten mit Tags auf _Indexed_ (Indiziert) festgelegt werden. Weitere Informationen finden Sie unter [Modi](../concepts/definition-structure.md#resource-manager-modes).

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="26-36" highlight="7-8":::

In diesem Teil der Richtliniendefinition kombiniert `concat` den parametrisierten Parameter **tagName** und das Format `tags['name']`, um **field** anzuweisen, dieses Tag für den Parameter **tagValue** auszuwerten.
Da **notEquals** verwendet wird, wenn **tags\[tagName\]** nicht **tagValue** entspricht, wird die Auswirkung **modify** ausgelöst.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="43-47" highlight="3-4":::

Hier wird vom Vorgang **addOrReplace** das gleiche Format für die Verwendung der parametrisierten Tagwerte verwendet, um das Tag mit dem gewünschten Wert für die ausgewertete Ressourcengruppe zu erstellen oder zu aktualisieren.

## <a name="sample-2-inherit-tag-value-from-resource-group"></a>Beispiel 2: Erben des Tagwerts der Ressourcengruppe

In dieser Richtliniendefinition wird der Parameter **tagName** verwendet, um zu bestimmen, welcher Tagwert von der übergeordneten Ressourcengruppe geerbt werden soll.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json":::

### <a name="sample-2-explanation"></a>Beispiel 2: Erklärung

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="2-8" highlight="3":::

In diesem Beispiel ist **mode** auf _Indexed_ festgelegt, weil keine Ressourcengruppe oder kein Abonnement als Ziel verwendet wird, obwohl der Wert aus einer Ressourcengruppe abgerufen wird. Weitere Informationen finden Sie unter [Modi](../concepts/definition-structure.md#resource-manager-modes).

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="19-29" highlight="3-4,7-8":::

**policyRule.if** verwendet `concat` wie [Beispiel 1](#sample-1-parameterize-tags), um den Wert von **tagName** auszuwerten. Es wird jedoch die Funktion `resourceGroup()` verwendet, um den Wert mit dem Wert desselben Tags für die übergeordnete Ressourcengruppe zu vergleichen. In der zweiten Klausel wird überprüft, ob das Tag für die Ressourcengruppe einen Wert aufweist und nicht NULL ist.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="36-40" highlight="3-4":::

Hier verwendet der Wert, der dem Tag **tagName** für die Ressource zugewiesen wird, auch die Funktion `resourceGroup()`, um den Wert aus der übergeordneten Ressourcengruppe abzurufen. Auf diese Weise können Tags von übergeordneten Ressourcengruppen geerbt werden. Wenn Sie bereits die Ressource erstellt, aber das Tag nicht hinzugefügt haben, können Sie mit dieser Richtliniendefinition und einem [Wartungstask](../how-to/remediate-resources.md) vorhandene Ressourcen aktualisieren.

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich andere [Muster und integrierte Definitionen](./index.md) an.
- Lesen Sie die Informationen unter [Struktur von Azure Policy-Definitionen](../concepts/definition-structure.md).
- Lesen Sie [Grundlegendes zu Richtlinienauswirkungen](../concepts/effects.md).
