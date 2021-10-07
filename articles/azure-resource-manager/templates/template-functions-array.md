---
title: Vorlagenfunktionen – Arrays
description: Informationen zu den Funktionen, die in einer Azure Resource Manager-Vorlage (ARM-Vorlage) zum Arbeiten mit Arrays verwendet werden können.
ms.topic: conceptual
ms.date: 09/08/2021
ms.openlocfilehash: 25fb249e1840a7538fa1dd14f3b1a930bbefdfee
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124755993"
---
# <a name="array-functions-for-arm-templates"></a>Arrayfunktionen für ARM-Vorlagen

Resource Manager stellt mehrere Funktionen für das Arbeiten mit Arrays in Ihrer Azure Resource Manager-Vorlage (ARM-Vorlage) bereit:

* [array](#array)
* [concat](#concat)
* [contains](#contains)
* [createArray](#createarray)
* [empty](#empty)
* [first](#first)
* [intersection](#intersection)
* [last](#last)
* [length](#length)
* [max](#max)
* [min](#min)
* [range](#range)
* [skip](#skip)
* [take](#take)
* [union](#union)

Informationen zu einem Array mit Zeichenfolgenwerten, die durch einen Wert getrennt sind, finden Sie unter [split](template-functions-string.md#split).

## <a name="array"></a>array

`array(convertToArray)`

Konvertiert den Wert in ein Array.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| convertToArray |Ja |int, string, array oder object |Der Wert, der in ein Array konvertiert werden soll. |

### <a name="return-value"></a>Rückgabewert

Ein -Array.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird die Verwendung der array-Funktion mit unterschiedlichen Typen gezeigt.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/array.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| intOutput | Array | [1] |
| stringOutput | Array | ["efgh"] |
| objectOutput | Array | [{"a": "b", "c": "d"}] |

## <a name="concat"></a>concat

`concat(arg1, arg2, arg3, ...)`

Kombiniert mehrere Arrays und gibt das verkettete Array zurück oder kombiniert mehrere Zeichenfolgenwerte und gibt die verkettete Zeichenfolge zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array oder Zeichenfolge |Das erste Array bzw. die erste Zeichenfolge für die Verkettung. |
| mehr Argumente |Nein  |Array oder Zeichenfolge |Weitere Arrays oder Strings in sequenzieller Reihenfolge für die Verkettung. |

Diese Funktion akzeptiert eine beliebige Anzahl von Argumenten und Zeichenfolgen oder Arrays für die Parameter. Sie können jedoch nicht sowohl Arrays als auch Zeichenfolgen für Parameter angeben. Arrays werden nur mit anderen Arrays verkettet.

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge oder ein Array aus verketteten Werten.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird veranschaulicht, wie zwei Arrays kombiniert werden.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/concat-array.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| return | Array | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

Das folgende Beispiel zeigt, wie zwei Zeichenfolgenwerte kombiniert werden und eine verkettete Zeichenfolge zurückgeben.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/concat-string.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| concatOutput | String | prefix-5yj4yjf5mbg72 |

## <a name="contains"></a>contains

`contains(container, itemToFind)`

Überprüft, ob ein Array einen Wert enthält, ein Objekt einen Schlüssel enthält oder eine Zeichenfolge eine Teilzeichenfolge enthält. Die Groß-/Kleinschreibung wird beim Zeichenfolgenvergleich beachtet. Wenn Sie jedoch testen, ob ein Objekt einen Schlüssel enthält, wird die Groß-/Kleinschreibung beim Vergleich nicht beachtet.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | Beschreibung |
|:--- |:--- |:--- |:--- |
| Container |Ja |Array, Objekt oder Zeichenfolge |Der Wert, der den zu suchenden Wert enthält. |
| itemToFind |Ja |Zeichenfolge oder ganze Zahl |Der zu suchende Wert. |

### <a name="return-value"></a>Rückgabewert

**True**, wenn das Element gefunden wurde; andernfalls **False**.

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt die Verwendung von „contains“ mit unterschiedlichen Typen:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/contains.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Type | Wert |
| ---- | ---- | ----- |
| stringTrue | Bool | True |
| stringFalse | Bool | False |
| objectTrue | Bool | True |
| objectFalse | Bool | False |
| arrayTrue | Bool | True |
| arrayFalse | Bool | False |

## <a name="createarray"></a>createArray

`createArray (arg1, arg2, arg3, ...)`

Erstellt ein Array auf der Grundlage der Parameter.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| args |Nein |Zeichenfolge, ganze Zahl, Array oder Objekt |Die Werte im Array. |

### <a name="return-value"></a>Rückgabewert

Ein -Array. Wenn keine Parameter angegeben werden, wird ein leeres Array zurückgegeben.

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt die Verwendung von „createArray“ mit unterschiedlichen Typen:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/createarray.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Type | Wert |
| ---- | ---- | ----- |
| stringArray | Array | ["a", "b", "c"] |
| intArray | Array | [1, 2, 3] |
| objectArray | Array | [{"one": "a", "two": "b", "three": "c"}] |
| arrayArray | Array | [["one", "two", "three"]] |
| emptyArray | Array | [] |

## <a name="empty"></a>empty

`empty(itemToTest)`

Bestimmt, ob ein Array, Objekt oder eine Zeichenfolge leer ist.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| itemToTest |Ja |Array, Objekt oder Zeichenfolge |Der Wert, für den überprüft werden soll, ob er leer ist. |

### <a name="return-value"></a>Rückgabewert

Gibt **True** zurück, wenn der Werte leer ist. Andernfalls wird **False** zurückgegeben.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird überprüft, ob ein Array, Objekt und eine Zeichenfolge leer sind.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/empty.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Type | Wert |
| ---- | ---- | ----- |
| arrayEmpty | Bool | True |
| objectEmpty | Bool | True |
| stringEmpty | Bool | True |

## <a name="first"></a>first

`first(arg1)`

Gibt das erste Element des Arrays oder das erste Zeichen der Zeichenfolge zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array oder Zeichenfolge |Der Wert, dessen erstes Element oder Zeichen abgerufen wird. |

### <a name="return-value"></a>Rückgabewert

Der Typ (Zeichenfolge, ganze Zahl, Array oder Objekt) des ersten Elements in einem Array oder das erste Zeichen einer Zeichenfolge.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird die Verwendung der first-Funktion mit einem Array und einer Zeichenfolge gezeigt.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/first.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| arrayOutput | String | one |
| stringOutput | String | O |

## <a name="intersection"></a>Schnittmenge

`intersection(arg1, arg2, arg3, ...)`

Gibt ein einzelnes Array oder ein Objekt mit den gemeinsamen Elementen aus den Parametern zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array oder Objekt |Der erste Wert für die Suche nach gemeinsamen Elementen. |
| arg2 |Ja |Array oder Objekt |Der zweite Wert für die Suche nach gemeinsamen Elementen. |
| mehr Argumente |Nein |Array oder Objekt |Weitere Werte für die Suche nach gemeinsamen Elementen. |

### <a name="return-value"></a>Rückgabewert

Ein Array oder Objekt mit den gemeinsamen Elementen.

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt, wie Sie die Schnittmenge mit Arrays und Objekten verwenden können.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/intersection.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "three": "c"} |
| arrayOutput | Array | ["two", "three"] |

## <a name="last"></a>last

`last (arg1)`

Gibt das letzte Element des Arrays bzw. das letzte Zeichen der Zeichenfolge zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array oder Zeichenfolge |Der Wert, dessen letztes Element oder Zeichen abgerufen wird. |

### <a name="return-value"></a>Rückgabewert

Der Typ (Zeichenfolge, ganze Zahl, Array oder Objekt) des letzten Elements in einem Array oder das letzte Zeichen einer Zeichenfolge.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird die Verwendung der last-Funktion mit einem Array und einer Zeichenfolge gezeigt.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/last.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| arrayOutput | String | three |
| stringOutput | String | e |

## <a name="length"></a>length

`length(arg1)`

Gibt die Anzahl von Elementen in einem Array, Zeichen in einer Zeichenfolge oder Eigenschaften auf Stammebene in einem Objekt zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array, Zeichenfolge oder Objekt |Das Array, von dem die Anzahl der Elemente ermittelt werden soll, die Zeichenfolge, von der die Anzahl der Zeichen ermittelt werden soll, oder das Objekt, von dem die Anzahl der Eigenschaften auf Stammebene ermittelt werden soll. |

### <a name="return-value"></a>Rückgabewert

Eine ganze Zahl.

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt, wie man length mit einem Array und einer Zeichenkette verwendet.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/length.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| arraylength | Int | 3 |
| stringLength | Int | 13 |
| objectLength | Int | 4 |

Sie können diese Funktion mit einem Array verwenden, um bei der Erstellung von Ressourcen die Anzahl der Iterationen anzugeben. Im folgenden Beispiel bezieht sich der Parameter **siteNames** auf ein Array von Namen, die bei der Erstellung der Websites verwendet werden.

```json
"copy": {
  "name": "websitescopy",
  "count": "[length(parameters('siteNames'))]"
}
```

Weitere Informationen zum Verwenden dieser Funktion mit einem Array finden Sie unter [Ressourceniteration in ARM-Vorlagen](copy-resources.md).

## <a name="max"></a>max

`max(arg1)`

Gibt den größten Wert aus einem Array mit ganzen Zahlen oder einer durch Trennzeichen getrennten Liste mit ganzen Zahlen zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array mit ganzen Zahlen oder durch Trennzeichen getrennte Liste mit ganzen Zahlen |Die Auflistung, aus der der größte Wert abgerufen werden soll. |

### <a name="return-value"></a>Rückgabewert

Ein Ganzzahlwert, der den größten Wert darstellt.

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt, wie man max mit einem Array und einer Liste von Ganzzahlen verwendet.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/max.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

## <a name="min"></a>Min

`min(arg1)`

Gibt den kleinsten Wert aus einem Array mit ganzen Zahlen oder einer durch Trennzeichen getrennten Liste mit ganzen Zahlen zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array mit ganzen Zahlen oder durch Trennzeichen getrennte Liste mit ganzen Zahlen |Die Auflistung, aus der der kleinste Wert abgerufen werden soll. |

### <a name="return-value"></a>Rückgabewert

Ein Ganzzahlwert, der den kleinsten Wert darstellt.

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt, wie man min mit einem Array und einer Liste von Ganzzahlen verwendet.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/min.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

## <a name="range"></a>range

`range(startIndex, count)`

Erstellt ein Array mit ganzen Zahlen, das mit einer ganzen Zahl beginnt und eine bestimmte Zahl von Elementen enthält.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| startIndex |Ja |INT |Die erste ganze Zahl im Array. Die Summe von „startIndex“ und „count“ darf nicht größer sein als 2147483647. |
| count |Ja |INT |Die Anzahl von ganzen Zahlen im Array. Muss eine positive ganze Zahl bis 10000 sein. |

### <a name="return-value"></a>Rückgabewert

Ein Array mit ganzen Zahlen.

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt, wie Sie die Bereichsfunktion verwenden.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/range.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| rangeOutput | Array | [5, 6, 7] |

## <a name="skip"></a>skip

`skip(originalValue, numberToSkip)`

Gibt ein Array mit allen Elementen gemäß der angegebenen Anzahl im Array bzw. eine Zeichenfolge mit allen Zeichen gemäß der angegebenen Anzahl in der Zeichenfolge zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| originalValue |Ja |Array oder Zeichenfolge |Array oder Zeichenfolge, wo Elemente übersprungen werden sollen. |
| numberToSkip |Ja |INT |Die Anzahl der zu überspringenden Elemente bzw. Zeichen. Wenn dieser Wert 0 (null) oder kleiner ist, werden alle Elemente oder Zeichen in dem Wert zurückgegeben. Ist er größer als die Länge des Arrays bzw. der Zeichenfolge, wird ein leeres Array bzw. eine leere Zeichenfolge zurückgegeben. |

### <a name="return-value"></a>Rückgabewert

Ein Array oder eine Zeichenfolge.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird die angegebene Anzahl von Elementen im Array und Zeichen in der Zeichenfolge übersprungen.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/skip.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Type | Wert |
| ---- | ---- | ----- |
| arrayOutput | Array | ["three"] |
| stringOutput | String | two three |

## <a name="take"></a>take

`take(originalValue, numberToTake)`

Gibt ein Array oder eine Zeichenkette zurück. Ein Array hat die angegebene Anzahl von Elementen ab dem Beginn des Arrays. Eine Zeichenkette hat die angegebene Anzahl von Zeichen vom Anfang der Zeichenkette an.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| originalValue |Ja |Array oder Zeichenfolge |Das Array bzw. die Zeichenfolge, wo die Elemente entnommen werden sollen. |
| numberToTake |Ja |INT |Die Anzahl der zu entnehmenden Elemente bzw. Zeichen. Ist dieser Wert 0 oder kleiner, wird ein leeres Array bzw. eine leere Zeichenfolge zurückgegeben. Ist er größer als die Länge des entsprechenden Arrays bzw. der Zeichenfolge, werden alle Elemente des Arrays bzw. der Zeichenfolge zurückgegeben. |

### <a name="return-value"></a>Rückgabewert

Ein Array oder eine Zeichenfolge.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird die angegebene Anzahl von Elementen aus dem Array und Zeichen aus der Zeichenfolge entnommen.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/take.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Type | Wert |
| ---- | ---- | ----- |
| arrayOutput | Array | ["one", "two"] |
| stringOutput | String | on |

## <a name="union"></a>union

`union(arg1, arg2, arg3, ...)`

Gibt ein einzelnes Array oder Objekt mit allen Elementen aus den Parametern zurück. Doppelte Werte oder Schlüssel sind nur einmal enthalten.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array oder Objekt |Der erste zum Verknüpfen von Elementen zu verwendende Wert. |
| arg2 |Ja |Array oder Objekt |Der zweite zum Verknüpfen von Elementen zu verwendende Wert. |
| mehr Argumente |Nein |Array oder Objekt |Weitere Werte, die für die Verbindung von Elementen zu verwenden sind. |

### <a name="return-value"></a>Rückgabewert

Ein Array oder Objekt.

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt, wie Union mit Arrays und Objekten verwendet werden kann.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/union.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Type | Wert |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "two": "b", "three": "c2", "four": "d", "five": "e"} |
| arrayOutput | Array | ["one", "two", "three", "four"] |

## <a name="next-steps"></a>Nächste Schritte

* Eine Beschreibung der Abschnitte in einer ARM-Vorlage finden Sie unter [Grundlegendes zur Struktur und Syntax von ARM-Vorlagen](./syntax.md).
