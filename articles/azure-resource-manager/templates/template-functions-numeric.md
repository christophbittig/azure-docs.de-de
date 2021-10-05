---
title: Vorlagenfunktionen – numerisch
description: Informationen zu den Funktionen, die in einer ARM-Vorlage (Azure Resource Manager) zum Arbeiten mit Zahlen verwendet werden können.
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: ccfed6794b81b7910310cc5a9fd02dcf0cdb0e0f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124820418"
---
# <a name="numeric-functions-for-arm-templates"></a>Numerische Funktionen für ARM-Vorlagen

Resource Manager stellt die folgenden Funktionen für das Arbeiten mit Zahlen in Ihren ARM-Vorlagen (Azure Resource Manager) bereit:

* [add](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [float](#float)
* [int](#int)
* [max](#max)
* [min](#min)
* [mod](#mod)
* [mul](#mul)
* [sub](#sub)

## <a name="add"></a>add

`add(operand1, operand2)`

Gibt die Summe der beiden angegebenen ganzen Zahlen zurück.

Die `add`-Funktion wird in Bicep nicht unterstützt. Verwenden Sie stattdessen den [`+`-Operator](../bicep/operators-numeric.md#add-).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | Beschreibung |
|:--- |:--- |:--- |:--- |
|operand1 |Ja |INT |Erste zu addierende Zahl. |
|operand2 |Ja |INT |Zweite zu addierende Zahl. |

### <a name="return-value"></a>Rückgabewert

Eine ganze Zahl, die die Summe der Parameter enthält.

### <a name="example"></a>Beispiel

Im folgenden Beispiel werden zwei Parameter hinzugefügt.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/add.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| addResult | Int | 8 |

## <a name="copyindex"></a>copyIndex

`copyIndex(loopName, offset)`

Gibt den Index einer Iterationsschleife zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | Beschreibung |
|:--- |:--- |:--- |:--- |
| loopName | Nein  | Zeichenfolge | Der Name der Schleife zum Abrufen der Iteration |
| offset |Nein |INT |Die Zahl, die dem nullbasierten (0) Iterationswert hinzugefügt werden soll. |

### <a name="remarks"></a>Hinweise

Diese Funktion wird immer mit einem **copy** -Objekt verwendet. Wenn kein Wert für **offset** angegeben wird, wird der aktuelle Iterationswert zurückgegeben. Der Iterationswert beginnt bei 0 (null).

Mit der Eigenschaft **loopName** können Sie angeben, ob sich „copyIndex“ auf eine Ressourceniteration oder eine Eigenschafteniteration bezieht. Wenn kein Wert für **loopName** angegeben wird, wird die Iteration des aktuellen Ressourcentyps verwendet. Geben Sie einen Wert für **loopName** an, wenn eine Iteration für eine Eigenschaft ausgeführt wird.

Weitere Informationen zur Verwendung von Kopiervorgängen finden Sie unter:

* [Ressourceniteration in ARM-Vorlagen](copy-resources.md)
* [Eigenschafteniteration in ARM-Vorlagen](copy-properties.md)
* [Variableniteration in ARM-Vorlagen](copy-variables.md)
* [Ausgabeiteration in ARM-Vorlagen](copy-outputs.md)

### <a name="example"></a>Beispiel

Das folgende Beispiel enthält eine Kopierschleife und den Indexwert im Namen.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/copyindex.json":::

### <a name="return-value"></a>Rückgabewert

Eine ganze Zahl, die den aktuellen Index der Iteration darstellt.

## <a name="div"></a>div

`div(operand1, operand2)`

Gibt die Ganzzahldivision der beiden angegebenen ganzen Zahlen zurück.

Die `div`-Funktion wird in Bicep nicht unterstützt. Verwenden Sie stattdessen den [`/`-Operator](../bicep/operators-numeric.md#divide-).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | Beschreibung |
|:--- |:--- |:--- |:--- |
| operand1 |Ja |INT |Die zu teilende Zahl (Dividend). |
| operand2 |Ja |INT |Die Zahl, durch die geteilt wird (Divisor). Darf nicht null (0) sein. |

### <a name="return-value"></a>Rückgabewert

Eine ganze Zahl, die die Division darstellt.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird ein Parameter durch einen anderen Parameter dividiert.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/div.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| divResult | Int | 2 |

## <a name="float"></a>float

`float(arg1)`

Konvertiert den Wert in eine Gleitkommazahl. Diese Funktion wird nur beim Übergeben von benutzerdefinierten Parametern an eine Anwendung (z. B. eine Logik-App) verwendet.

Die `float`-Funktion wird in Bicep nicht unterstützt.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Zeichenfolge oder ganze Zahl |Der in eine Gleitkommazahl zu konvertierende Wert. |

### <a name="return-value"></a>Rückgabewert

Eine Gleitkommazahl.

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt, wie „float“ zum Übergeben von Parametern an eine Logik-App verwendet wird:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/float.json":::

## <a name="int"></a>INT

`int(valueToConvert)`

Konvertiert den angegebenen Wert in eine ganze Zahl (Integer).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| valueToConvert |Ja |Zeichenfolge oder ganze Zahl |Der Wert, der in eine ganze Zahl (Integer) konvertiert werden soll. |

### <a name="return-value"></a>Rückgabewert

Eine ganze Zahl des konvertierten Werts.

### <a name="example"></a>Beispiel

In der folgenden Beispielvorlage wird der vom Benutzer angegebene Parameterwert in eine ganze Zahl konvertiert.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/int.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| intResult | Int | 4 |

## <a name="max"></a>max

`max (arg1)`

Gibt den größten Wert aus einem Array mit ganzen Zahlen oder einer durch Trennzeichen getrennten Liste mit ganzen Zahlen zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array mit ganzen Zahlen oder durch Trennzeichen getrennte Liste mit ganzen Zahlen |Die Auflistung, aus der der größte Wert abgerufen werden soll. |

### <a name="return-value"></a>Rückgabewert

Eine ganze Zahl, die den größten Wert aus der Auflistung darstellt.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird gezeigt, wie „max“ mit einem Array und einer Liste mit ganzen Zahlen verwendet wird.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/max.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

## <a name="min"></a>Min

`min (arg1)`

Gibt den kleinsten Wert aus einem Array mit ganzen Zahlen oder einer durch Trennzeichen getrennten Liste mit ganzen Zahlen zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array mit ganzen Zahlen oder durch Trennzeichen getrennte Liste mit ganzen Zahlen |Die Auflistung, aus der der kleinste Wert abgerufen werden soll. |

### <a name="return-value"></a>Rückgabewert

Eine ganze Zahl, die den kleinsten Wert aus der Auflistung darstellt.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird gezeigt, wie „min“ mit einem Array und einer Liste mit ganzen Zahlen verwendet wird.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/min.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

## <a name="mod"></a>mod

`mod(operand1, operand2)`

Gibt den Rest der Ganzzahldivision mit den beiden angegebenen ganzen Zahlen zurück.

Die `mod`-Funktion wird in Bicep nicht unterstützt. Verwenden Sie stattdessen den Operator [%](../bicep/operators-numeric.md#modulo-).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | Beschreibung |
|:--- |:--- |:--- |:--- |
| operand1 |Ja |INT |Die zu teilende Zahl (Dividend). |
| operand2 |Ja |INT |Die Zahl, durch die dividiert wird (Divisor), darf nicht null (0) sein. |

### <a name="return-value"></a>Rückgabewert

Eine ganze Zahl, die den Rest darstellt.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird der Restbetrag der Division von einem Parameter durch einen anderen Parameter zurückgegeben.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/mod.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| modResult | Int | 1 |

## <a name="mul"></a>mul

`mul(operand1, operand2)`

Gibt die Multiplikation der beiden angegebenen ganzen Zahlen zurück.

Die `mul`-Funktion wird in Bicep nicht unterstützt. Verwenden Sie stattdessen den Operator [*](../bicep/operators-numeric.md#multiply-).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | Beschreibung |
|:--- |:--- |:--- |:--- |
| operand1 |Ja |INT |Erste zu multiplizierende Zahl. |
| operand2 |Ja |INT |Zweite zu multiplizierende Zahl. |

### <a name="return-value"></a>Rückgabewert

Eine ganze Zahl, die die Multiplikation darstellt.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird ein Parameter mit einem anderen Parameter multipliziert.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/mul.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| mulResult | Int | 15 |

## <a name="sub"></a>sub

`sub(operand1, operand2)`

Gibt die Differenz der beiden angegebenen ganzen Zahlen zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | Beschreibung |
|:--- |:--- |:--- |:--- |
| operand1 |Ja |INT |Zahl, von der subtrahiert wird. |
| operand2 |Ja |INT |Zahl, die subtrahiert wird. |

### <a name="return-value"></a>Rückgabewert

Eine ganze Zahl, die die Subtraktion darstellt.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird ein Parameter von einem anderen Parameter subtrahiert.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/sub.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| subResult | Int | 4 |

## <a name="next-steps"></a>Nächste Schritte

* Eine Beschreibung der Abschnitte in einer ARM-Vorlage finden Sie unter [Grundlegendes zur Struktur und Syntax von ARM-Vorlagen](./syntax.md).
* Wenn Sie beim Erstellen eines Ressourcentyps eine angegebene Anzahl von Wiederholungen durchlaufen möchten, finden Sie weitere Informationen unter [Ressourceniteration in ARM-Vorlagen](copy-resources.md).
