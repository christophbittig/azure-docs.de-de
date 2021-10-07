---
title: Bicep-Operatoren
description: Hier werden die für Azure Resource Manager-Bereitstellungen verfügbaren Bicep-Operatoren beschrieben.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/10/2021
ms.openlocfilehash: 13591112171919d6c58959c40dffa1340f3e8ebd
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124764312"
---
# <a name="bicep-operators"></a>Bicep-Operatoren

In diesem Artikel werden die Bicep-Operatoren beschrieben. Operatoren werden verwendet, um Werte zu berechnen, Werte zu vergleichen oder Bedingungen auszuwerten. Es gibt vier Arten von Bicep-Operatoren:

- [Accessor](#accessor)
- [Vergleich](#comparison)
- [Logisch](#logical)
- [numeric](#numeric)

## <a name="operator-precedence-and-associativity"></a>Operatorrangfolge und Assoziativität

Die nachstehenden Operatoren sind in absteigender Rangfolge aufgeführt (je höher die Position, desto höher der Rang). Operatoren auf derselben Ebene haben den gleichen Rang.

| Symbol | Vorgangstyp | Assoziativität |
|:-|:-|:-|
| `(` `)` `[` `]` `.` `::` | Klammern, Arrayindexer, Eigenschaftenaccessoren und Accessor für geschachtelter Ressourcen  | Von links nach rechts |
| `!` `-` | Unär | Von rechts nach links |
| `%` `*` `/` | Multiplikativ | Von links nach rechts |
| `+` `-` | Additiv | Von links nach rechts |
| `<=` `<` `>` `>=` | Relational | Von links nach rechts |
| `==` `!=` `=~` `!~` | Gleichheit | Von links nach rechts |
| `&&` | Logisches AND | Von links nach rechts |
| `||` | Logisches OR | Von links nach rechts |
| `?` `:` | Bedingter Ausdruck (ternär) | Von rechts nach links
| `??` | Coalesce | Von links nach rechts

## <a name="parentheses"></a>Klammern

Wenn Sie einen Ausdruck in Klammern einschließen, können Sie die Standardpriorität des Bicep-Operators außer Kraft setzen. Der Ausdruck `x + y / z` wertet zum Beispiel zuerst die Division und dann die Addition aus. Der Ausdruck `(x + y) / z` wertet jedoch zuerst die Addition und dann die Division aus.

## <a name="accessor"></a>Accessor

Die Accessoroperatoren werden verwendet, um auf geschachtelte Ressourcen und Eigenschaften von Objekten zuzugreifen.

| Operator | Name | BESCHREIBUNG |
| ---- | ---- | ---- |
| `[]` | [Indexaccessor](./operators-access.md#index-accessor) | Zugriff auf ein Element eines Arrays oder einer Eigenschaft eines Objekts. |
| `.` | [Funktionsaccessor](./operators-access.md#function-accessor) | Aufruf einer Funktion für eine Ressource |
| `::` | [Accessor für geschachtelte Ressourcen](./operators-access.md#nested-resource-accessor) | Zugriff auf eine geschachtelte Ressource von außerhalb der übergeordneten Ressource |
| `.` | [Eigenschaftenaccessor](./operators-access.md#property-accessor) | Zugriff auf Eigenschaften eines Objekts |

## <a name="comparison"></a>Vergleich

Die Vergleichsoperatoren vergleichen Werte und geben entweder `true` oder `false` zurück.

| Operator | Name | Beschreibung |
| ---- | ---- | ---- |
| `>=` | [Größer oder gleich](./operators-comparison.md#greater-than-or-equal-) | Wertet aus, ob der erste Wert größer oder gleich dem zweiten Wert ist. |
| `>`  | [Größer als](./operators-comparison.md#greater-than-) | Wertet aus, ob der erste Wert größer ist als der zweite Wert. |
| `<=` | [Kleiner oder gleich](./operators-comparison.md#less-than-or-equal-) | Wertet aus, ob der erste Wert kleiner oder gleich dem zweiten Wert ist. |
| `<`  | [Kleiner als](./operators-comparison.md#less-than-) | Wertet aus, ob der erste Wert kleiner ist als der zweite Wert. |
| `==` | [Ist gleich](./operators-comparison.md#equals-) | Wertet aus, ob zwei Werte gleich sind. |
| `!=` | [Ungleich](./operators-comparison.md#not-equal-) | Wertet aus, ob zwei Werte **nicht** gleich sind. |
| `=~` | [Gleiche Groß-/Kleinschreibung wird nicht berücksichtigt](./operators-comparison.md#equal-case-insensitive-) | Ignoriert die Groß-/Kleinschreibung, um zu ermitteln, ob zwei Werte gleich sind. |
| `!~` | [Ungleiche Groß-/Kleinschreibung wird nicht berücksichtigt](./operators-comparison.md#not-equal-case-insensitive-) | Ignoriert die Groß-/Kleinschreibung, um zu ermitteln, ob zwei Werte **nicht** gleich sind. |

## <a name="logical"></a>Logisch

Die logischen Operatoren werten boolesche Werte aus, geben Werte zurück, die ungleich NULL sind, oder werten einen bedingten Ausdruck aus.

| Operator | Name | BESCHREIBUNG |
| ---- | ---- | ---- |
| `&&` | [Und](./operators-logical.md#and-) | Gibt `true` zurück, wenn alle Werte WAHR sind. |
| `||`| [Oder](./operators-logical.md#or-) | Gibt `true` zurück, wenn einer der beiden Werte WAHR ist. |
| `!` | [Not](./operators-logical.md#not-) | Negiert einen booleschen Wert. Nimmt einen Operanden. |
| `??` | [Koaleszieren (COALESCE)](./operators-logical.md#coalesce-) | Gibt den ersten Wert zurück, der ungleich NULL ist. |
| `?` `:` | [Bedingter Ausdruck](./operators-logical.md#conditional-expression--) | Wertet eine Bedingung auf WAHR oder FALSCH aus und gibt einen Wert zurück. |

## <a name="numeric"></a>Numeric

Die numerischen Operatoren verwenden zur Ausführung von Berechnungen ganze Zahlen und geben ganzzahlige Werte zurück.

| Operator | Name | BESCHREIBUNG |
| ---- | ---- | ---- |
| `*` | [Multiplizieren](./operators-numeric.md#multiply-) | Multipliziert zwei ganze Zahlen. |
| `/` | [Dividieren](./operators-numeric.md#divide-) | Dividiert eine ganze Zahl durch eine ganze Zahl. |
| `%` | [Modulo](./operators-numeric.md#modulo-) | Dividiert eine ganze Zahl durch eine ganze Zahl und gibt den Rest zurück. |
| `+` | [Add (Hinzufügen)](./operators-numeric.md#add-) | Addiert zwei ganze Zahlen. |
| `-` | [Subtrahieren](./operators-numeric.md#subtract--) | Subtrahiert eine Ganzzahl von einer anderen Ganzzahl. Benötigt zwei Operanden. |
| `-` | [Minus](./operators-numeric.md#minus--) (unär) | Multipliziert eine ganze Zahl mit `-1`. Nimmt einen Operanden. |

> [!NOTE]
> Subtrahieren und Minus verwenden denselben Operator. Die Funktionalität ist unterschiedlich, weil Subtrahieren zwei Operanden und Minus einen Operanden verwendet.


## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Erstellen einer Bicep-Datei finden Sie unter [Schnellstart: Erstellen von Bicep-Dateien mit Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
- Informationen zum Beheben von Bicep-Typfehlern finden Sie unter [any-Funktion für Bicep](./bicep-functions-any.md).
- Einen Vergleich der Syntax für Bicep und JSON finden Sie unter [Vergleichen von JSON mit Bicep für Vorlagen](./compare-template-syntax.md).
- Beispiele zu Bicep-Funktionen finden Sie unter [Bicep-Funktionen](./bicep-functions.md).
