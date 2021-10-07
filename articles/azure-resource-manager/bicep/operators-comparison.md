---
title: Bicep-Vergleichsoperatoren
description: Beschreibt Bicep-Vergleichsoperatoren, die Werte vergleichen.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/07/2021
ms.openlocfilehash: 1a28da26a3c97982bb0e06deebae6ae68b1eb7d9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124744415"
---
# <a name="bicep-comparison-operators"></a>Bicep-Vergleichsoperatoren

Die Vergleichsoperatoren vergleichen Werte und geben entweder `true` oder `false` zurück. Verwenden Sie zum Ausführen der Beispiele die Azure CLI oder Azure PowerShell, um [eine Bicep-Datei bereitzustellen](./quickstart-create-bicep-use-visual-studio-code.md#deploy-the-bicep-file).

| Operator | Name |
| ---- | ---- |
| `>=` | [Größer oder gleich](#greater-than-or-equal-) |
| `>`  | [Größer als](#greater-than-) |
| `<=` | [Kleiner oder gleich](#less-than-or-equal-) |
| `<`  | [Kleiner als](#less-than-) |
| `==` | [Ist gleich](#equals-) |
| `!=` | [Ungleich](#not-equal-) |
| `=~` | [Gleiche Groß-/Kleinschreibung](#equal-case-insensitive-) |
| `!~` | [Ungleiche Groß-/Kleinschreibung](#not-equal-case-insensitive-) |

## <a name="greater-than-or-equal-"></a>Größer als oder gleich >=

`operand1 >= operand2`

Bewertet, ob der erste Wert größer oder gleich dem zweiten Wert ist.

### <a name="operands"></a>Operanden

| Operand | type | BESCHREIBUNG |
| ---- | ---- | ---- |
| `operand1` | Ganzzahl, Zeichenfolge | Erster Wert im Vergleich. |
| `operand2` | Ganzzahl, Zeichenfolge | Zweiter Wert im Vergleich. |

### <a name="return-value"></a>Rückgabewert

Wenn der erste Wert größer oder gleich dem zweiten Wert ist, wird `true` zurückgegeben. Andernfalls wird `false` zurückgegeben.

### <a name="example"></a>Beispiel

Ein Ganzzahlpaar und ein Zeichenfolgenpaar werden verglichen.

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'A'
param secondString string = 'A'

output intGtE bool = firstInt >= secondInt
output stringGtE bool = firstString >= secondString
```

Ausgabe des Beispiels:

| Name | Type | Wert |
| ---- | ---- | ---- |
| `intGtE` | boolean | true |
| `stringGtE` | boolean | true |

## <a name="greater-than-"></a>Größer als >

`operand1 > operand2`

Bewertet, ob der erste Wert größer als der zweite ist.

### <a name="operands"></a>Operanden

| Operand | type | BESCHREIBUNG |
| ---- | ---- | ---- |
| `operand1` | Ganzzahl, Zeichenfolge | Erster Wert im Vergleich. |
| `operand2` | Ganzzahl, Zeichenfolge | Zweiter Wert im Vergleich. |

### <a name="return-value"></a>Rückgabewert

Wenn der erste Wert größer als der zweite ist, wird `true` zurückgegeben. Andernfalls wird `false` zurückgegeben.

### <a name="example"></a>Beispiel

Ein Ganzzahlpaar und ein Zeichenfolgenpaar werden verglichen.

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'bend'
param secondString string = 'band'

output intGt bool = firstInt > secondInt
output stringGt bool = firstString > secondString
```

Ausgabe des Beispiels:

Durch **e** in **bend** wird die erste Zeichenfolge größer.

| Name | Type | Wert |
| ---- | ---- | ---- |
| `intGt` | boolean | true |
| `stringGt` | boolean | true |

## <a name="less-than-or-equal-"></a>Kleiner als oder gleich <=

`operand1 <= operand2`

Bewertet, ob der erste Wert kleiner oder gleich dem zweiten Wert ist.

### <a name="operands"></a>Operanden

| Operand | type | BESCHREIBUNG |
| ---- | ---- | ---- |
| `operand1` | Ganzzahl, Zeichenfolge | Erster Wert im Vergleich. |
| `operand2` | Ganzzahl, Zeichenfolge | Zweiter Wert im Vergleich. |

### <a name="return-value"></a>Rückgabewert

Wenn der erste Wert kleiner oder gleich dem zweiten Wert ist, wird `true` zurückgegeben. Andernfalls wird `false` zurückgegeben.

### <a name="example"></a>Beispiel

Ein Ganzzahlpaar und ein Zeichenfolgenpaar werden verglichen.

```bicep
param firstInt int = 5
param secondInt int = 10

param firstString string = 'demo'
param secondString string = 'demo'

output intLtE bool = firstInt <= secondInt
output stringLtE bool = firstString <= secondString
```

Ausgabe des Beispiels:

| Name | Type | Wert |
| ---- | ---- | ---- |
| `intLtE` | boolean | true |
| `stringLtE` | boolean | true |

## <a name="less-than-"></a>Kleiner als <

`operand1 < operand2`

Bewertet, ob der erste Wert kleiner als der zweite ist.

### <a name="operands"></a>Operanden

| Operand | type | BESCHREIBUNG |
| ---- | ---- | ---- |
| `operand1` | Ganzzahl, Zeichenfolge | Erster Wert im Vergleich. |
| `operand2` | Ganzzahl, Zeichenfolge | Zweiter Wert im Vergleich. |

### <a name="return-value"></a>Rückgabewert

Wenn der erste Wert kleiner als der zweite ist, wird `true` zurückgegeben. Andernfalls wird `false` zurückgegeben.

### <a name="example"></a>Beispiel

Ein Ganzzahlpaar und ein Zeichenfolgenpaar werden verglichen.

```bicep
param firstInt int = 5
param secondInt int = 10

param firstString string = 'demo'
param secondString string = 'Demo'

output intLt bool = firstInt < secondInt
output stringLt bool = firstString < secondString
```

Ausgabe des Beispiels:

Die Zeichenfolge ist `true`, da Kleinbuchstaben kleiner als Großbuchstaben sind.

| Name | Type | Wert |
| ---- | ---- | ---- |
| `intLt` | boolean | true |
| `stringLt` | boolean | true |

## <a name="equals-"></a>Gleich ==

`operand1 == operand2`

Bewertet, ob die Werte gleich sind.

### <a name="operands"></a>Operanden

| Operand | type | BESCHREIBUNG |
| ---- | ---- | ---- |
| `operand1` | string, integer, boolean, array, object | Erster Wert im Vergleich. |
| `operand2` | string, integer, boolean, array, object | Zweiter Wert im Vergleich. |

### <a name="return-value"></a>Rückgabewert

Wenn die Operanden gleich sind, wird `true` zurückgegeben. Wenn sich die Operanden unterscheiden, wird `false` zurückgegeben.

### <a name="example"></a>Beispiel

Paare aus ganzen Zahlen, Zeichenfolgen und booleschen Werten werden verglichen.

```bicep
param firstInt int = 5
param secondInt int = 5

param firstString string = 'demo'
param secondString string = 'demo'

param firstBool bool = true
param secondBool bool = true

output intEqual bool = firstInt == secondInt
output stringEqual bool = firstString == secondString
output boolEqual bool = firstBool == secondBool
```

Ausgabe des Beispiels:

| Name | Type | Wert |
| ---- | ---- | ---- |
| `intEqual` | boolean | true |
| `stringEqual` | boolean | true |
| `boolEqual` | boolean | true |

Beim Vergleichen von Arrays müssen die beiden Arrays die gleichen Elemente und die gleiche Reihenfolge aufweisen. Die Arrays müssen nicht einander zugewiesen werden.

```bicep
var array1 = [
  1
  2
  3
]

var array2 = [
  1
  2
  3
]

var array3 = array2

var array4 = [
  3
  2
  1
]

output sameElements bool = array1 == array2 // returns true because arrays are defined with same elements
output assignArray bool = array2 == array3 // returns true because one array was defined as equal to the other array
output differentOrder bool = array4 == array1 // returns false because order of elements is different
```

Ausgabe des Beispiels:

| Name | Type | Wert |
| ---- | ---- | ---- |
| sameElements | bool | true |
| assignArray | bool | true |
| differentOrder | bool | false |

Beim Vergleichen von Objekten müssen die Eigenschaftennamen und Werte identisch sein. Die Eigenschaften müssen nicht in der gleichen Reihenfolge definiert werden.

```bicep
var object1 = {
  prop1: 'val1'
  prop2: 'val2'
}

var object2 = {
  prop1: 'val1'
  prop2: 'val2'
}

var object3 = {
  prop2: 'val2'
  prop1: 'val1'
}

var object4 = object3

var object5 = {
  prop1: 'valX'
  prop2: 'valY'
}

output sameObjects bool = object1 == object2 // returns true because both objects defined with same properties
output differentPropertyOrder bool = object3 == object2 // returns true because both objects have same properties even though order is different
output assignObject bool = object4 == object1 // returns true because one object was defined as equal to the other object
output differentValues bool = object5 == object1 // returns false because values are different
```

Ausgabe des Beispiels:

| Name | Type | Wert |
| ---- | ---- | ---- |
| sameObjects | bool | true |
| differentPropertyOrder | bool | true |
| assignObject | bool | true |
| differentValues | bool | false |

## <a name="not-equal-"></a>Ungleich !=

`operand1 != operand2`

Bewertet, ob die beiden Werte **nicht** gleich sind.

### <a name="operands"></a>Operanden

| Operand | type | BESCHREIBUNG |
| ---- | ---- | ---- |
| `operand1` | string, integer, boolean, array, object | Erster Wert im Vergleich. |
| `operand2` | string, integer, boolean, array, object | Zweiter Wert im Vergleich. |

### <a name="return-value"></a>Rückgabewert

Wenn die Operanden **nicht** gleich sind, wird `true` zurückgegeben. Wenn die Operanden gleich sind, wird `false` zurückgegeben.

### <a name="example"></a>Beispiel

Paare aus ganzen Zahlen, Zeichenfolgen und booleschen Werten werden verglichen.

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'demo'
param secondString string = 'test'

param firstBool bool = false
param secondBool bool = true

output intNotEqual bool = firstInt != secondInt
output stringNotEqual bool = firstString != secondString
output boolNotEqual bool = firstBool != secondBool
```

Ausgabe des Beispiels:

| Name | Type | Wert |
| ---- | ---- | ---- |
| `intNotEqual` | boolean | true |
| `stringNotEqual` | boolean | true |
| `boolNotEqual` | boolean | true |

Informationen zu Arrays und Objekten finden Sie in den Beispielen unter [Gleich ==](#equals-).

## <a name="equal-case-insensitive-"></a>Groß-/Kleinschreibung nicht berücksichtigt =~

`operand1 =~ operand2`

Ignoriert Groß- und Kleinschreibung, um festzustellen, ob die beiden Werte gleich sind.

### <a name="operands"></a>Operanden

| Operand | type | Beschreibung |
| ---- | ---- | ---- |
| `operand1`  | Zeichenfolge | Erste Zeichenfolge im Vergleich. |
| `operand2`  | Zeichenfolge | Zweite Zeichenfolge im Vergleich. |

### <a name="return-value"></a>Rückgabewert

Wenn die Zeichenfolgen gleich sind, wird `true` zurückgegeben. Andernfalls wird `false` zurückgegeben.

### <a name="example"></a>Beispiel

Vergleicht Zeichenfolgen, die sowohl Groß- als auch Kleinbuchstaben enthalten.

```bicep
param firstString string = 'demo'
param secondString string = 'DEMO'

param thirdString string = 'demo'
param fourthString string = 'TEST'

output strEqual1 bool = firstString =~ secondString
output strEqual2 bool = thirdString =~ fourthString
```

Ausgabe des Beispiels:

| Name | Type | Wert |
| ---- | ---- | ---- |
| `strEqual1` | boolean | true |
| `strEqual2` | boolean | false |

## <a name="not-equal-case-insensitive-"></a>Ungleiche Groß-/Kleinschreibung wird nicht berücksichtigt !~

`operand1 !~ operand2`

Ignoriert die Groß-/Schreibung, um zu bestimmen, ob die beiden Werte **nicht** gleich sind.

### <a name="operands"></a>Operanden

| Operand | type | Beschreibung |
| ---- | ---- | ---- |
| `operand1` | Zeichenfolge | Erste Zeichenfolge im Vergleich. |
| `operand2` | Zeichenfolge | Zweite Zeichenfolge im Vergleich. |

### <a name="return-value"></a>Rückgabewert

Wenn die Zeichenfolgen **ungleich** sind, wird `true` zurückgegeben. Andernfalls wird `false` zurückgegeben.

### <a name="example"></a>Beispiel

Vergleicht Zeichenfolgen, die sowohl Groß- als auch Kleinbuchstaben enthalten.

```bicep
param firstString string = 'demo'
param secondString string = 'TEST'

param thirdString string = 'demo'
param fourthString string = 'DeMo'

output strNotEqual1 bool = firstString !~ secondString
output strEqual2 bool = thirdString !~ fourthString
```

Ausgabe des Beispiels:

| Name | Type | Wert |
| ---- | ---- | ---- |
| `strNotEqual1` | boolean | true |
| `strNotEqual2` | boolean | false |

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Erstellen einer Bicep-Datei finden Sie unter [Schnellstart: Erstellen von Bicep-Dateien mit Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
- Informationen zum Beheben von Bicep-Typfehlern finden Sie unter [any-Funktion für Bicep](./bicep-functions-any.md).
- Einen Vergleich der Syntax für Bicep und JSON finden Sie unter [Vergleichen von JSON mit Bicep für Vorlagen](./compare-template-syntax.md).
- Beispiele zu Bicep-Funktionen finden Sie unter [Bicep-Funktionen](./bicep-functions.md).
