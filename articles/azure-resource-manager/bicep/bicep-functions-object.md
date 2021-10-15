---
title: Bicep-Funktionen – Objekte
description: Hier werden die Funktionen beschrieben, die in Bicep-Dateien zum Arbeiten mit Arrays verwendet werden.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/30/2021
ms.openlocfilehash: 38dfdee2dbe40b40631b45b8d0f9e157f5efc7ae
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129349832"
---
# <a name="object-functions-for-bicep"></a>Objektfunktionen für Bicep

In diesem Artikel werden die Bicep-Funktionen für die Arbeit mit Objekten beschrieben.

## <a name="contains"></a>contains

`contains(container, itemToFind)`

Überprüft, ob ein Array einen Wert enthält, ein Objekt einen Schlüssel enthält oder eine Zeichenfolge eine Teilzeichenfolge enthält. Die Groß-/Kleinschreibung wird beim Zeichenfolgenvergleich beachtet. Wenn Sie jedoch testen, ob ein Objekt einen Schlüssel enthält, wird die Groß-/Kleinschreibung beim Vergleich nicht beachtet.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| Container |Ja |Array, Objekt oder Zeichenfolge |Der Wert, der den zu suchenden Wert enthält. |
| itemToFind |Ja |Zeichenfolge oder ganze Zahl |Der zu suchende Wert. |

### <a name="return-value"></a>Rückgabewert

**True**, wenn das Element gefunden wurde; andernfalls **False**.

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt die Verwendung von „contains“ mit unterschiedlichen Typen:

```bicep
param stringToTest string = 'OneTwoThree'
param objectToTest object = {
  'one': 'a'
  'two': 'b'
  'three': 'c'
}
param arrayToTest array = [
  'one'
  'two'
  'three'
]

output stringTrue bool = contains(stringToTest, 'e')
output stringFalse bool = contains(stringToTest, 'z')
output objectTrue bool = contains(objectToTest, 'one')
output objectFalse bool = contains(objectToTest, 'a')
output arrayTrue bool = contains(arrayToTest, 'three')
output arrayFalse bool = contains(arrayToTest, 'four')
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| stringTrue | Bool | True |
| stringFalse | Bool | False |
| objectTrue | Bool | True |
| objectFalse | Bool | False |
| arrayTrue | Bool | True |
| arrayFalse | Bool | False |

## <a name="empty"></a>empty

`empty(itemToTest)`

Bestimmt, ob ein Array, Objekt oder eine Zeichenfolge leer ist.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| itemToTest |Ja |Array, Objekt oder Zeichenfolge |Der Wert, für den überprüft werden soll, ob er leer ist. |

### <a name="return-value"></a>Rückgabewert

Gibt **True** zurück, wenn der Werte leer ist. Andernfalls wird **False** zurückgegeben.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird überprüft, ob ein Array, Objekt und eine Zeichenfolge leer sind.

```bicep
param testArray array = []
param testObject object = {}
param testString string = ''

output arrayEmpty bool = empty(testArray)
output objectEmpty bool = empty(testObject)
output stringEmpty bool = empty(testString)
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| arrayEmpty | Bool | True |
| objectEmpty | Bool | True |
| stringEmpty | Bool | True |

## <a name="intersection"></a>Schnittmenge

`intersection(arg1, arg2, arg3, ...)`

Gibt ein einzelnes Array oder ein Objekt mit den gemeinsamen Elementen aus den Parametern zurück.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array oder Objekt |Der erste Wert für die Suche nach gemeinsamen Elementen. |
| arg2 |Ja |Array oder Objekt |Der zweite Wert für die Suche nach gemeinsamen Elementen. |
| zusätzliche Argumente |Nein |Array oder Objekt |Weitere Werte für die Suche nach gemeinsamen Elementen. |

### <a name="return-value"></a>Rückgabewert

Ein Array oder Objekt mit den gemeinsamen Elementen.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird die Verwendung von „intersection“ mit Arrays und Objekten gezeigt:

```bicep
param firstObject object = {
  'one': 'a'
  'two': 'b'
  'three': 'c'
}
param secondObject object = {
  'one': 'a'
  'two': 'z'
  'three': 'c'
}
param firstArray array = [
  'one'
  'two'
  'three'
]
param secondArray array = [
  'two'
  'three'
]

output objectOutput object = intersection(firstObject, secondObject)
output arrayOutput array = intersection(firstArray, secondArray)
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "three": "c"} |
| arrayOutput | Array | ["two", "three"] |

<a id="json"></a>

## <a name="json"></a>json

`json(arg1)`

Konvertiert eine gültige JSON-Zeichenfolge in einen JSON-Datentyp.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Zeichenfolge |Der in JSON zu konvertierende Wert. Die Zeichenfolge muss eine ordnungsgemäß formatierte JSON-Zeichenfolge sein. |

### <a name="return-value"></a>Rückgabewert

Der JSON-Datentyp aus der angegebenen Zeichenfolge oder ein leerer Wert, wenn **null** angegeben ist.

### <a name="remarks"></a>Bemerkungen

Wenn Sie einen Parameterwert oder eine Variable in das JSON-Objekt einschließen möchten, verwenden Sie die Funktion [concat](./bicep-functions-string.md#concat), um die Zeichenfolge zu erstellen, die Sie an die Funktion übergeben.

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt die Verwendung der JSON-Funktion. Beachten Sie, dass Sie **NULL** für ein leeres-Objekt übergeben können.

```bicep
param jsonEmptyObject string = 'null'
param jsonObject string = '{\'a\': \'b\'}'
param jsonString string = '\'test\''
param jsonBoolean string = 'true'
param jsonInt string = '3'
param jsonArray string = '[[1,2,3]]'
param concatValue string = 'demo value'

output emptyObjectOutput bool = empty(json(jsonEmptyObject))
output objectOutput object = json(jsonObject)
output stringOutput string =json(jsonString)
output booleanOutput bool = json(jsonBoolean)
output intOutput int = json(jsonInt)
output arrayOutput array = json(jsonArray)
output concatObjectOutput object = json(concat('{"a": "', concatValue, '"}'))
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| emptyObjectOutput | Boolesch | True |
| objectOutput | Object | {"a": "b"} |
| stringOutput | String | test |
| booleanOutput | Boolesch | True |
| intOutput | Integer | 3 |
| arrayOutput | Array | [ 1, 2, 3 ] |
| concatObjectOutput | Object | { "a": "demo value" } |

## <a name="length"></a>length

`length(arg1)`

Gibt die Anzahl von Elementen in einem Array, Zeichen in einer Zeichenfolge oder Eigenschaften auf Stammebene in einem Objekt zurück.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array, Zeichenfolge oder Objekt |Das Array, von dem die Anzahl der Elemente ermittelt werden soll, die Zeichenfolge, von der die Anzahl der Zeichen ermittelt werden soll, oder das Objekt, von dem die Anzahl der Eigenschaften auf Stammebene ermittelt werden soll. |

### <a name="return-value"></a>Rückgabewert

Eine ganze Zahl.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird die Verwendung von „length“ mit einem Array und einer Zeichenfolge gezeigt:

```bicep
param arrayToTest array = [
  'one'
  'two'
  'three'
]
param stringToTest string = 'One Two Three'
param objectToTest object = {
  'propA': 'one'
  'propB': 'two'
  'propC': 'three'
  'propD': {
      'propD-1': 'sub'
      'propD-2': 'sub'
  }
}

output arrayLength int = length(arrayToTest)
output stringLength int = length(stringToTest)
output objectLength int = length(objectToTest)
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| arraylength | Int | 3 |
| stringLength | Int | 13 |
| objectLength | Int | 4 |

## <a name="union"></a>union

`union(arg1, arg2, arg3, ...)`

Gibt ein einzelnes Array oder Objekt mit allen Elementen aus den Parametern zurück. Doppelte Werte oder Schlüssel sind nur einmal enthalten.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array oder Objekt |Der erste zum Verknüpfen von Elementen zu verwendende Wert. |
| arg2 |Ja |Array oder Objekt |Der zweite zum Verknüpfen von Elementen zu verwendende Wert. |
| zusätzliche Argumente |Nein |Array oder Objekt |Weitere zum Verknüpfen von Elementen zu verwendende Werte. |

### <a name="return-value"></a>Rückgabewert

Ein Array oder Objekt.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird die Verwendung von „union“ mit Arrays und Objekten gezeigt:

```bicep
param firstObject object = {
  'one': 'a'
  'two': 'b'
  'three': 'c1'
}

param secondObject object = {
  'three': 'c2'
  'four': 'd'
  'five': 'e'
}

param firstArray array = [
  'one'
  'two'
  'three'
]

param secondArray array = [
  'three'
  'four'
]

output objectOutput object = union(firstObject, secondObject)
output arrayOutput array = union(firstArray, secondArray)
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "two": "b", "three": "c2", "four": "d", "five": "e"} |
| arrayOutput | Array | ["one", "two", "three", "four"] |

## <a name="next-steps"></a>Nächste Schritte

* Eine Beschreibung der Abschnitte in einer Bicep-Datei finden Sie unter [Grundlegendes zur Struktur und Syntax von Bicep-Dateien](./file.md).
