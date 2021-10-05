---
title: Vorlagenfunktionen – Objekte
description: Informationen zu den Funktionen, die in einer Azure Resource Manager-Vorlage (ARM-Vorlage) zum Arbeiten mit Objekten verwendet werden können.
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 3e6069744b1879e97ef3977916acbfc8aa6a9bd2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124820452"
---
# <a name="object-functions-for-arm-templates"></a>Objektfunktionen für ARM-Vorlagen

Resource Manager stellt mehrere Funktionen für das Arbeiten mit Objekten in Ihrer Azure Resource Manager-Vorlage (ARM-Vorlage) bereit:

* [contains](#contains)
* [createObject](#createobject)
* [empty](#empty)
* [intersection](#intersection)
* [json](#json)
* [length](#length)
* [null](#null)
* [union](#union)

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

| Name | Typ | Wert |
| ---- | ---- | ----- |
| stringTrue | Bool | True |
| stringFalse | Bool | False |
| objectTrue | Bool | True |
| objectFalse | Bool | False |
| arrayTrue | Bool | True |
| arrayFalse | Bool | False |

## <a name="createobject"></a>createObject

`createObject(key1, value1, key2, value2, ...)`

Erstellt ein Objekt aus den Schlüsseln und Werten.

Die `createObject`-Funktion wird von Bicep nicht unterstützt.  Erstellen Sie ein-Objekt, indem Sie `{}` verwenden. Weitere Informationen finden Sie unter [Objekte](../bicep/data-types.md#objects).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| key1 |Nein |Zeichenfolge |Der Name des Schlüssels. |
| value1 |Nein |int, boolean, string, object oder array |Der Wert für den Schlüssel. |
| Mehr Schlüssel |Nein |Zeichenfolge |Mehr Namen der Schlüssel. |
| Mehr Werte |Nein |int, boolean, string, object oder array |Mehr Werte für die Schlüssel. |

Die Funktion akzeptiert nur eine gerade Anzahl von Parametern. Jeder Schlüssel muss über einen entsprechenden Wert verfügen.

### <a name="return-value"></a>Rückgabewert

Ein Objekt mit jedem Schlüssel-Wert-Paar.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird ein Objekt aus verschiedenen Werttypen erstellt.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/object/createobject.json":::

Die Ausgabe des vorherigen Beispiels mit den Standardwerten ist ein Objekt namens `newObject` mit folgendem Wert:

```json
{
  "intProp": 1,
  "stringProp": "abc",
  "boolProp": true,
  "arrayProp": ["a", "b", "c"],
  "objectProp": {"key1": "value1"}
}
```

## <a name="empty"></a>empty

`empty(itemToTest)`

Bestimmt, ob ein Array, Objekt oder eine Zeichenfolge leer ist.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| itemToTest |Ja |Array, Objekt oder Zeichenfolge |Der Wert, für den überprüft werden soll, ob er leer ist. |

### <a name="return-value"></a>Rückgabewert

Gibt **True** zurück, wenn der Werte leer ist. Andernfalls wird **False** zurückgegeben.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird überprüft, ob ein Array, Objekt und eine Zeichenfolge leer sind.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/empty.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| arrayEmpty | Bool | True |
| objectEmpty | Bool | True |
| stringEmpty | Bool | True |

## <a name="intersection"></a>Schnittmenge

`intersection(arg1, arg2, arg3, ...)`

Gibt ein einzelnes Array oder ein Objekt mit den gemeinsamen Elementen aus den Parametern zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array oder Objekt |Der erste Wert für die Suche nach gemeinsamen Elementen. |
| arg2 |Ja |Array oder Objekt |Der zweite Wert für die Suche nach gemeinsamen Elementen. |
| Mehr Argumente |Nein |Array oder Objekt |Mehr Werte für die Suche nach gemeinsamen Elementen. |

### <a name="return-value"></a>Rückgabewert

Ein Array oder Objekt mit den gemeinsamen Elementen.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird die Verwendung von „intersection“ mit Arrays und Objekten gezeigt.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/intersection.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "three": "c"} |
| arrayOutput | Array | ["two", "three"] |

<a id="json"></a>

## <a name="json"></a>json

`json(arg1)`

Konvertiert eine gültige JSON-Zeichenfolge in einen JSON-Datentyp.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Zeichenfolge |Der in JSON zu konvertierende Wert. Die Zeichenfolge muss eine ordnungsgemäß formatierte JSON-Zeichenfolge sein. |

### <a name="return-value"></a>Rückgabewert

Der JSON-Datentyp aus der angegebenen Zeichenfolge oder ein leerer Wert, wenn **null** angegeben ist.

### <a name="remarks"></a>Bemerkungen

Wenn Sie einen Parameterwert oder eine Variable in das JSON-Objekt einschließen möchten, verwenden Sie die Funktion [concat](template-functions-string.md#concat), um die Zeichenfolge zu erstellen, die Sie an die Funktion übergeben.

Sie können auch [null()](#null) verwenden, um einen NULL-Wert zu erhalten.

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt die Verwendung der Funktion `json`. Beachten Sie, dass Sie `null` für ein leeres Objekt übergeben können.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/object/json.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Typ | Wert |
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

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array, Zeichenfolge oder Objekt |Das Array, von dem die Anzahl der Elemente ermittelt werden soll, die Zeichenfolge, von der die Anzahl der Zeichen ermittelt werden soll, oder das Objekt, von dem die Anzahl der Eigenschaften auf Stammebene ermittelt werden soll. |

### <a name="return-value"></a>Rückgabewert

Eine ganze Zahl.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird die Verwendung von „length“ mit einem Array und einer Zeichenfolge gezeigt:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/length.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| arraylength | Int | 3 |
| stringLength | Int | 13 |
| objectLength | Int | 4 |

## <a name="null"></a>NULL

`null()`

Gibt NULL zurück.

Die Funktion `null` ist in Bicep nicht verfügbar. Verwenden Sie stattdessen das Schlüsselwort `null`.

### <a name="parameters"></a>Parameter

Die Funktion „null“ akzeptiert keine Parameter.

### <a name="return-value"></a>Rückgabewert

Ein Wert, der immer „NULL“ lautet.

### <a name="example"></a>Beispiel

Das folgende Beispiel verwendet die „null“-Funktion.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/object/null.json":::

Die Ausgabe aus dem vorherigen Beispiel lautet wie folgt:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| emptyOutput | Bool | True |

## <a name="union"></a>union

`union(arg1, arg2, arg3, ...)`

Gibt ein einzelnes Array oder Objekt mit allen Elementen aus den Parametern zurück. Doppelte Werte oder Schlüssel sind nur einmal enthalten.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array oder Objekt |Der erste zum Verknüpfen von Elementen zu verwendende Wert. |
| arg2 |Ja |Array oder Objekt |Der zweite zum Verknüpfen von Elementen zu verwendende Wert. |
| Mehr Argumente |Nein |Array oder Objekt |Mehr zum Verknüpfen von Elementen zu verwendende Werte. |

### <a name="return-value"></a>Rückgabewert

Ein Array oder Objekt.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird die Verwendung von „union“ mit Arrays und Objekten gezeigt:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/union.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "two": "b", "three": "c2", "four": "d", "five": "e"} |
| arrayOutput | Array | ["one", "two", "three", "four"] |

## <a name="next-steps"></a>Nächste Schritte

* Eine Beschreibung der Abschnitte in einer ARM-Vorlage finden Sie unter [Grundlegendes zur Struktur und Syntax von ARM-Vorlagen](./syntax.md).
