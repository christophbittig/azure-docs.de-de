---
title: 'Muster: Parameter in einer Richtliniendefinition'
description: Dieses Azure Policy-Muster enthält ein Beispiel für die Verwendung von Zeichenfolgen- und Arrayparametern in einer Richtliniendefinition sowie für das Parametrisieren des Effekts.
ms.date: 08/17/2021
ms.topic: sample
ms.openlocfilehash: d2cddd0d86a5ecc44ef39bec989db2a7700c878a
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324126"
---
# <a name="azure-policy-pattern-parameters"></a>Azure Policy-Muster: Parameter

Eine Richtliniendefinition kann mithilfe von [Parametern](../concepts/definition-structure.md#parameters) dynamisch gemacht werden, damit weniger Richtliniendefinitionen benötigt werden. Der Parameter wird im Rahmen der Richtlinienzuweisung definiert. Parameter verfügen über eine Reihe vordefinierter Eigenschaften, die den Parameter und dessen Verwendung beschreiben.

## <a name="sample-1-string-parameters"></a>Beispiel 1: Zeichenfolgenparameter

In dieser Richtliniendefinition werden zwei Parameter (**tagName** und **tagValue**) verwendet, um festzulegen, wonach die Richtlinienzuweisung in Ressourcen suchen soll. Mit diesem Format kann die Richtliniendefinition für beliebig viele Kombinationen aus Tagname und Tagwert verwendet werden, ohne mehrere Richtliniendefinitionen verwalten zu müssen.

> [!NOTE]
> Ein Tagbeispiel, in dem **mode** auf _All_ festgelegt ist und eine Ressourcengruppe verwendet wird, finden Sie unter [Muster: Tags –Beispiel 1](./pattern-tags.md#sample-1-parameterize-tags).

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json":::

### <a name="sample-1-explanation"></a>Beispiel 1: Erklärung

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="8-13":::

In diesem Teil der Richtliniendefinition wird der Parameter **tagName** als Zeichenfolge (_String_) definiert und eine Beschreibung für dessen Verwendung angegeben.

Der Parameter wird dann im Block **policyRule.if** verwendet, um die Richtlinie dynamisch zu machen. Hier wird damit das auszuwertende Feld (ein Tag mit dem Wert **tagName**) definiert.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="22-27" highlight="3":::

## <a name="sample-2-array-parameters"></a>Beispiel 2: Arrayparameter

In dieser Richtliniendefinition wird ein einzelner Parameter (**listOfBandwidthinMbps**) verwendet, um zu überprüfen, ob die Bandbreiteneinstellung der ExpressRoute-Verbindungsressource mit einem der zulässigen Werte konfiguriert ist. Ist dies nicht der Fall, wird die Erstellung oder Aktualisierung der Ressource [verweigert](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json":::

### <a name="sample-2-explanation"></a>Beispiel 2: Erklärung

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="6-12":::

In diesem Teil der Richtliniendefinition wird der Parameter **listOfBandwidthinMbps** als _Array_ definiert und eine Beschreibung für dessen Verwendung angegeben. Da es sich um ein _Array_ handelt, sind mehrere Werte für den Abgleich vorhanden.

Der Parameter wird dann im Block **policyRule.if** verwendet. Bei einem Parameter vom Typ _Array_ muss die [Bedingung](../concepts/definition-structure.md#conditions) **in** oder **notIn** des _Arrays_
verwendet werden.
In diesem Beispiel wird der Alias **serviceProvider.bandwidthInMbps** als einer der definierten Werte für den Abgleich verwendet.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="21-24" highlight="3":::

## <a name="sample-3-parameterized-effect"></a>Beispiel 3: Parametrisierte Auswirkung

Eine gängige Methode, Richtliniendefinitionen wiederverwendbar zu machen, besteht darin, die Auswirkung selbst zu parametrisieren. In diesem Beispiel wird ein einziger Parameter (**effect**) verwendet. Durch Parametrisieren der Auswirkung kann dieselbe Definition verschiedenen Bereichen mit unterschiedlichen Auswirkungen zugewiesen werden.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-3.json":::

### <a name="sample-3-explanation"></a>Beispiel 3: Erklärung

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-3.json" range="11-25":::

In diesem Teil der Richtliniendefinition wird der Parameter **effect** als Zeichenfolge (_string_) definiert. Die Richtliniendefinition legt den Standardwert für eine Zuweisung auf _Audit_ fest und schränkt die anderen Optionen auf _Disabled_ und _Deny_ ein.

Der Parameter wird dann im Block **policyRule.then** für die Auswirkung (_effect_) verwendet.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-3.json" range="38-40" highlight="2":::

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich andere [Muster und integrierte Definitionen](./index.md) an.
- Lesen Sie die Informationen unter [Struktur von Azure Policy-Definitionen](../concepts/definition-structure.md).
- Lesen Sie [Grundlegendes zu Richtlinienauswirkungen](../concepts/effects.md).
