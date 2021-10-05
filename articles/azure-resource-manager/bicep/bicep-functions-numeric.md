---
title: Bicep-Funktionen – numerisch
description: Hier werden die Funktionen beschrieben, die in einer Bicep-Datei für das Arbeiten mit Zahlen verwendet werden.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/10/2021
ms.openlocfilehash: 8df0aec96001aa5b1a7cdd7342fe663f0bfa2da0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124758482"
---
# <a name="numeric-functions-for-bicep"></a>Numerische Funktionen für Bicep

In diesem Artikel werden die Bicep-Funktionen für die Arbeit mit ganzen Zahlen beschrieben.

Einige der numerischen JSON-Funktionen in Azure Resource Manager werden durch [numerische Bicep-Operatoren](./operators-numeric.md) ersetzt.

## <a name="int"></a>INT

`int(valueToConvert)`

Konvertiert den angegebenen Wert in eine ganze Zahl (Integer).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| valueToConvert |Ja |Zeichenfolge oder ganze Zahl |Der Wert, der in eine ganze Zahl (Integer) konvertiert werden soll. |

### <a name="return-value"></a>Rückgabewert

Eine ganze Zahl des konvertierten Werts.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird der vom Benutzer angegebene Parameterwert in eine ganze Zahl konvertiert.

```bicep
param stringToConvert string = '4'

output inResult int = int(stringToConvert)
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Type | Wert |
| ---- | ---- | ----- |
| intResult | Int | 4 |

## <a name="max"></a>max

`max (arg1)`

Gibt den größten Wert aus einem Array mit ganzen Zahlen oder einer durch Trennzeichen getrennten Liste mit ganzen Zahlen zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array mit ganzen Zahlen oder durch Trennzeichen getrennte Liste mit ganzen Zahlen |Die Auflistung, aus der der größte Wert abgerufen werden soll. |

### <a name="return-value"></a>Rückgabewert

Eine ganze Zahl, die den größten Wert aus der Auflistung darstellt.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird gezeigt, wie „max“ mit einem Array und einer Liste mit ganzen Zahlen verwendet wird:

```bicep
param arrayToTest array = [
  0
  3
  2
  5
  4
]

output arrayOutPut int = max(arrayToTest)
output intOutput int = max(0,3,2,5,4)
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Type | Wert |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

## <a name="min"></a>Min

`min (arg1)`

Gibt den kleinsten Wert aus einem Array mit ganzen Zahlen oder einer durch Trennzeichen getrennten Liste mit ganzen Zahlen zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array mit ganzen Zahlen oder durch Trennzeichen getrennte Liste mit ganzen Zahlen |Die Auflistung, aus der der kleinste Wert abgerufen werden soll. |

### <a name="return-value"></a>Rückgabewert

Eine ganze Zahl, die den kleinsten Wert aus der Auflistung darstellt.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird gezeigt, wie „min“ mit einem Array und einer Liste mit ganzen Zahlen verwendet wird:

```bicep
param arrayToTest array = [
  0
  3
  2
  5
  4
]

output arrayOutPut int = min(arrayToTest)
output intOutput int = min(0,3,2,5,4)
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Type | Wert |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

## <a name="next-steps"></a>Nächste Schritte

* Weitere Aktionen mit Zahlen finden Sie unter [Numerische Bicep-Operatoren](./operators-numeric.md).
