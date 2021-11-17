---
title: Bicep-Funktionen – Zeichenfolge
description: Hier werden die Funktionen beschrieben, die in einer Bicep-Datei für das Arbeiten mit Zeichenfolgen verwendet werden.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 10/29/2021
ms.openlocfilehash: a73df839ff7dcaad992b8930c8fb2545e854951c
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132028076"
---
# <a name="string-functions-for-bicep"></a>Zeichenfolgenfunktionen für Bicep

In diesem Artikel werden die Bicep-Funktionen für die Arbeit mit Zeichenfolgen beschrieben.

## <a name="base64"></a>base64

`base64(inputString)`

Rückkehr zur base64-Darstellung der Eingabezeichenfolge.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| inputString |Ja |Zeichenfolge |Der Wert, der als base64-Darstellung zurückgegeben wird. |

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge mit der base64-Darstellung.

### <a name="examples"></a>Beispiele

Das folgende Beispiel zeigt die Funktionsweise der base64-Funktion.

```bicep
param stringData string = 'one, two, three'
param jsonFormattedData string = '{\'one\': \'a\', \'two\': \'b\'}'

var base64String = base64(stringData)
var base64Object = base64(jsonFormattedData)

output base64Output string = base64String
output toStringOutput string = base64ToString(base64String)
output toJsonOutput object = base64ToJson(base64Object)
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="base64tojson"></a>base64ToJson

`base64tojson`

Konvertiert eine base64-Darstellung in ein JSON-Objekt.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| base64Value |Ja |Zeichenfolge |Die in ein JSON-Objekt zu konvertierende base64-Darstellung. |

### <a name="return-value"></a>Rückgabewert

Ein JSON-Objekt.

### <a name="examples"></a>Beispiele

Im folgenden Beispiel wird die base64ToJson-Funktion zum Konvertieren eines base64-Werts verwendet:

```bicep
param stringData string = 'one, two, three'
param jsonFormattedData string = '{\'one\': \'a\', \'two\': \'b\'}'

var base64String = base64(stringData)
var base64Object = base64(jsonFormattedData)

output base64Output string = base64String
output toStringOutput string = base64ToString(base64String)
output toJsonOutput object = base64ToJson(base64Object)

```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="base64tostring"></a>base64ToString

`base64ToString(base64Value)`

Konvertiert eine base64-Darstellung in eine Zeichenfolge.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| base64Value |Ja |Zeichenfolge |Die in eine Zeichenfolge zu konvertierende base64-Darstellung. |

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge des konvertierten base64-Werts.

### <a name="examples"></a>Beispiele

Im folgenden Beispiel wird die base64ToString-Funktion verwendet, um einen base64-Wert zu konvertieren:

```bicep
param stringData string = 'one, two, three'
param jsonFormattedData string = '{\'one\': \'a\', \'two\': \'b\'}'

var base64String = base64(stringData)
var base64Object = base64(jsonFormattedData)

output base64Output string = base64String
output toStringOutput string = base64ToString(base64String)
output toJsonOutput object = base64ToJson(base64Object)
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="concat"></a>concat

Verwenden Sie anstelle der concat-Funktion die Zeichenfolgeninterpolation.

```bicep
param prefix string = 'prefix'

output concatOutput string = '${prefix}And${uniqueString(resourceGroup().id)}'
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| concatOutput | String | prefixAnd5yj4yjf5mbg72 |

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

## <a name="contains"></a>contains

`contains (container, itemToFind)`

Überprüft, ob ein Array einen Wert enthält, ein Objekt einen Schlüssel enthält oder eine Zeichenfolge eine Teilzeichenfolge enthält. Die Groß-/Kleinschreibung wird beim Zeichenfolgenvergleich beachtet. Wenn Sie jedoch testen, ob ein Objekt einen Schlüssel enthält, wird die Groß-/Kleinschreibung beim Vergleich nicht beachtet.

Namespace: [sys](bicep-functions.md#namespaces-for-functions)

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| Container |Ja |Array, Objekt oder Zeichenfolge |Der Wert, der den zu suchenden Wert enthält. |
| itemToFind |Ja |Zeichenfolge oder ganze Zahl |Der zu suchende Wert. |

### <a name="return-value"></a>Rückgabewert

**True**, wenn das Element gefunden wurde; andernfalls **False**.

### <a name="examples"></a>Beispiele

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

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| stringTrue | Bool | True |
| stringFalse | Bool | False |
| objectTrue | Bool | True |
| objectFalse | Bool | False |
| arrayTrue | Bool | True |
| arrayFalse | Bool | False |

## <a name="datauri"></a>dataUri

`dataUri(stringToConvert)`

Konvertiert einen Wert in einen Daten-URI.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| stringToConvert |Ja |Zeichenfolge |Der Wert, der in einen Daten-URI konvertiert werden soll. |

### <a name="return-value"></a>Rückgabewert

Eine als Daten-URI formatierte Zeichenfolge.

### <a name="examples"></a>Beispiele

Im folgenden Beispiel wird ein Wert in einen Daten-URI und ein Daten-URI in eine Zeichenfolge konvertiert:

```bicep
param stringToTest string = 'Hello'
param dataFormattedString string = 'data:;base64,SGVsbG8sIFdvcmxkIQ=='

output dataUriOutput string = dataUri(stringToTest)
output toStringOutput string = dataUriToString(dataFormattedString)
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| dataUriOutput | String | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | String | Hello, World! |

## <a name="datauritostring"></a>dataUriToString

`dataUriToString(dataUriToConvert)`

Konvertiert einen als Daten-URI formatierten Wert in eine Zeichenfolge.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |Ja |Zeichenfolge |Der zu konvertierende Daten-URI-Wert. |

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge, die den konvertierten Wert enthält.

### <a name="examples"></a>Beispiele

Im folgenden Beispiel wird ein Wert in einen Daten-URI und ein Daten-URI in eine Zeichenfolge konvertiert:

```bicep
param stringToTest string = 'Hello'
param dataFormattedString string = 'data:;base64,SGVsbG8sIFdvcmxkIQ=='

output dataUriOutput string = dataUri(stringToTest)
output toStringOutput string = dataUriToString(dataFormattedString)
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| dataUriOutput | String | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | String | Hello, World! |

## <a name="empty"></a>empty

`empty(itemToTest)`

Bestimmt, ob ein Array, Objekt oder eine Zeichenfolge leer ist.

Namespace: [sys](bicep-functions.md#namespaces-for-functions)

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| itemToTest |Ja |Array, Objekt oder Zeichenfolge |Der Wert, für den überprüft werden soll, ob er leer ist. |

### <a name="return-value"></a>Rückgabewert

Gibt **True** zurück, wenn der Werte leer ist. Andernfalls wird **False** zurückgegeben.

### <a name="examples"></a>Beispiele

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

## <a name="endswith"></a>endsWith

`endsWith(stringToSearch, stringToFind)`

Bestimmt, ob eine Zeichenfolge mit einem Wert endet. Bei dem Vergleich wird Groß- und Kleinschreibung nicht unterschieden.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ja |Zeichenfolge |Der Wert, der das zu suchende Element enthält. |
| stringToFind |Ja |Zeichenfolge |Der zu suchende Wert. |

### <a name="return-value"></a>Rückgabewert

**True**, wenn das letzte Zeichen oder die letzten Zeichen der Zeichenfolge mit dem Wert übereinstimmen, andernfalls **False**.

### <a name="examples"></a>Beispiele

Das folgende Beispiel zeigt die Verwendung der Funktionen „startsWith“ und „endsWith“:

```bicep
output startsTrue bool = startsWith('abcdef', 'ab')
output startsCapTrue bool = startsWith('abcdef', 'A')
output startsFalse bool = startsWith('abcdef', 'e')
output endsTrue bool = endsWith('abcdef', 'ef')
output endsCapTrue bool = endsWith('abcdef', 'F')
output endsFalse bool = endsWith('abcdef', 'e')
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| startsTrue | Bool | True |
| startsCapTrue | Bool | True |
| startsFalse | Bool | False |
| endsTrue | Bool | True |
| endsCapTrue | Bool | True |
| endsFalse | Bool | False |

## <a name="first"></a>first

`first(arg1)`

Gibt das erste Zeichen der Zeichenfolge oder das erste Element des Arrays zurück.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array oder Zeichenfolge |Der Wert, dessen erstes Element oder Zeichen abgerufen wird. |

### <a name="return-value"></a>Rückgabewert

Ein Zeichenfolgenwert, der dem letzten Zeichen entspricht, bzw. der Typ (Zeichenfolge, ganze Zahl, Array oder Objekt) des ersten Elements in einem Array.

### <a name="examples"></a>Beispiele

Im folgenden Beispiel wird die Verwendung der first-Funktion mit einem Array und einer Zeichenfolge gezeigt.

```bicep
param arrayToTest array = [
  'one'
  'two'
  'three'
]

output arrayOutput string = first(arrayToTest)
output stringOutput string = first('One Two Three')
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| arrayOutput | String | one |
| stringOutput | String | O |

## <a name="format"></a>format

`format(formatString, arg1, arg2, ...)`

Erstellt eine formatierte Zeichenfolge aus Eingabewerten.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| formatString | Ja | Zeichenfolge | Die zusammengesetzte Formatzeichenfolge. |
| arg1 | Ja | Zeichenfolge, Integer oder boolescher Wert | Der Wert, der in die formatierte Zeichenfolge aufgenommen werden soll. |
| zusätzliche Argumente | Nein | Zeichenfolge, Integer oder boolescher Wert | Weitere Werte, die in die formatierte Zeichenfolge aufgenommen werden sollen. |

### <a name="remarks"></a>Bemerkungen

Verwenden Sie diese Funktion zum Formatieren einer Zeichenfolge in Ihrer Bicep-Datei. Sie verwendet die gleichen Formatierungsoptionen wie die [System.String.Format](/dotnet/api/system.string.format)-Methode in .NET.

### <a name="examples"></a>Beispiele

Das folgende Beispiel zeigt die Verwendung der format-Funktion.

```bicep
param greeting string = 'Hello'
param name string = 'User'
param numberToFormat int = 8175133

output formatTest string = format('{0}, {1}. Formatted number: {2:N0}', greeting, name, numberToFormat)
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| formatTest | String | Hallo, Benutzer. Formatierte Zahl: 8.175.133 |

## <a name="guid"></a>guid

`guid(baseString, ...)`

Erstellt einen Wert im Format eines Globally Unique Identifiers basierend auf den als Parameter angegebenen Werten.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| baseString |Ja |Zeichenfolge |Der in der Hashfunktion für die Erstellung des GUID verwendete Wert. |
| Zusätzliche Parameter nach Bedarf. |Nein |Zeichenfolge |Sie können beliebig viele Zeichenfolgen hinzufügen, ganz wie sie zum Erstellen des Werts benötigt werden, der die Ebene der Eindeutigkeit angibt. |

### <a name="remarks"></a>Bemerkungen

Diese Funktion ist hilfreich, wenn Sie einen Wert im Format eines Globally Unique Identifiers erstellen müssen. Sie geben Parameterwerte an, die den Eindeutigkeitsbereich für das Ergebnis einschränken. Sie können angeben, ob der Name bis hinunter zum Abonnement, zur Ressourcengruppe oder zur Bereitstellung eindeutig ist.

Der zurückgegebene Wert ist keine zufällige Zeichenfolge, sondern das Ergebnis einer Hashfunktion, die für die Parameter ausgeführt wurde. Der zurückgegebene Wert ist 36 Zeichen lang. Er ist nicht global eindeutig. Verwenden Sie die [newGuid](#newguid)-Funktion, um eine neue GUID zu erstellen, die nicht auf diesem Hashwert der Parameter basiert.

Die folgenden Beispiele zeigen, wie Sie mithilfe des GUID einen eindeutigen Wert für häufig verwendete Ebenen erstellen können.

Eindeutige Zuordnung zum Abonnement

```bicep
guid(subscription().subscriptionId)
```

Eindeutige Zuordnung zur Ressourcengruppe

```bicep
guid(resourceGroup().id)
```

Eindeutige Zuordnung zur Bereitstellung für eine Ressourcengruppe

```bicep
guid(resourceGroup().id, deployment().name)
```

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge mit 36 Zeichen im Format eines Globally Unique Identifiers.

> [!NOTE]
> Bedeutung der Reihenfolge: Es handelt sich nicht nur um dieselben Parameter, sie müssen auch in derselben Reihenfolge angegeben werden. Beispiel: `guid('hello', 'world') != guid('world', 'hello')`

### <a name="examples"></a>Beispiele

Das folgende Beispiel gibt Ergebnisse aus GUID zurück:

```bicep
output guidPerSubscription string = guid(subscription().subscriptionId)
output guidPerResourceGroup string = guid(resourceGroup().id)
output guidPerDeployment string = guid(resourceGroup().id, deployment().name)
```

## <a name="indexof"></a>indexOf

`indexOf(stringToSearch, stringToFind)`

Gibt die erste Position eines Werts innerhalb einer Zeichenfolge zurück. Bei dem Vergleich wird Groß- und Kleinschreibung nicht unterschieden.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ja |Zeichenfolge |Der Wert, der das zu suchende Element enthält. |
| stringToFind |Ja |Zeichenfolge |Der zu suchende Wert. |

### <a name="return-value"></a>Rückgabewert

Eine ganze Zahl, die die Position des zu suchenden Elements darstellt. Der Wert ist nullbasiert. Wenn das Element nicht gefunden wird, wird -1 zurückgegeben.

### <a name="examples"></a>Beispiele

Das folgende Beispiel zeigt die Verwendung der Funktionen „indexOf“ und „lastIndexOf“:

```bicep
output firstT int = indexOf('test', 't')
output lastT int = lastIndexOf('test', 't')
output firstString int = indexOf('abcdef', 'CD')
output lastString int = lastIndexOf('abcdef', 'AB')
output notFound int = indexOf('abcdef', 'z')
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| NotFound | Int | -1 |

<a id="json"></a>

## <a name="json"></a>json

`json(arg1)`

Konvertiert eine gültige JSON-Zeichenfolge in einen JSON-Datentyp. Weitere Informationen finden Sie unter [json-Funktion](./bicep-functions-object.md#json).

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

## <a name="last"></a>last

`last (arg1)`

Gibt das letzte Zeichen der Zeichenfolge bzw. das letzte Element des Arrays zurück.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array oder Zeichenfolge |Der Wert, dessen letztes Element oder Zeichen abgerufen wird. |

### <a name="return-value"></a>Rückgabewert

Ein Zeichenfolgenwert, der dem letzten Zeichen entspricht, bzw. der Typ (Zeichenfolge, ganze Zahl, Array oder Objekt) des letzten Elements in einem Array.

### <a name="examples"></a>Beispiele

Im folgenden Beispiel wird die Verwendung der last-Funktion mit einem Array und einer Zeichenfolge gezeigt.

```bicep
param arrayToTest array = [
  'one'
  'two'
  'three'
]

output arrayOutput string = last(arrayToTest)
output stringOutput string = last('One Two Three')
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| arrayOutput | String | three |
| stringOutput | String | e |

## <a name="lastindexof"></a>lastIndexOf

`lastIndexOf(stringToSearch, stringToFind)`

Gibt die letzte Position eines Werts innerhalb einer Zeichenfolge zurück. Bei dem Vergleich wird Groß- und Kleinschreibung nicht unterschieden.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ja |Zeichenfolge |Der Wert, der das zu suchende Element enthält. |
| stringToFind |Ja |Zeichenfolge |Der zu suchende Wert. |

### <a name="return-value"></a>Rückgabewert

Eine ganze Zahl, die die letzte Position des zu suchenden Elements darstellt. Der Wert ist nullbasiert. Wenn das Element nicht gefunden wird, wird -1 zurückgegeben.

### <a name="examples"></a>Beispiele

Das folgende Beispiel zeigt die Verwendung der Funktionen „indexOf“ und „lastIndexOf“:

```bicep
output firstT int = indexOf('test', 't')
output lastT int = lastIndexOf('test', 't')
output firstString int = indexOf('abcdef', 'CD')
output lastString int = lastIndexOf('abcdef', 'AB')
output notFound int = indexOf('abcdef', 'z')
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| NotFound | Int | -1 |

## <a name="length"></a>length

`length(string)`

Gibt die Anzahl von Zeichen in einer Zeichenfolge, Elementen in einem Array oder Eigenschaften auf Stammebene in einem Objekt zurück.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array, Zeichenfolge oder Objekt |Das Array, von dem die Anzahl der Elemente ermittelt werden soll, die Zeichenfolge, von der die Anzahl der Zeichen ermittelt werden soll, oder das Objekt, von dem die Anzahl der Eigenschaften auf Stammebene ermittelt werden soll. |

### <a name="return-value"></a>Rückgabewert

Eine ganze Zahl.

### <a name="examples"></a>Beispiele

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

## <a name="newguid"></a>newGuid

`newGuid()`

Gibt einen Wert im Format einer GUID (eindeutiger Bezeichner) zurück. **Diese Funktion kann nur für den Standardwert eines Parameters verwendet werden.**

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="remarks"></a>Bemerkungen

Sie können diese Funktion nur in einem Ausdruck für den Standardwert eines Parameters verwenden. Wenn diese Funktion an einer anderen Stelle in einer Bicep-Datei verwendet wird, wird ein Fehler zurückgegeben. Die Funktion ist in anderen Teilen der Bicep-Datei nicht zulässig, da bei jedem Aufruf ein anderer Wert zurückgegeben wird. Das Bereitstellen derselben Bicep-Datei mit denselben Parametern würde nicht zuverlässig zu denselben Ergebnissen führen.

Im Gegensatz zur [guid](#guid)-Funktion verwendet die newGuid-Funktion keine Parameter. Wenn Sie die Funktion „guid“ mit denselben Parametern aufrufen, wird jedes Mal der gleiche Bezeichner zurückgegeben. Verwenden Sie die Funktion „guid“, wenn Sie die gleiche GUID für eine spezifische Umgebung zuverlässig generieren müssen. Verwenden Sie die Funktion „newGuid“, wenn Sie jedes Mal einen anderen Bezeichner benötigen, z. B. beim Bereitstellen von Ressourcen für eine Testumgebung.

Die newGuid-Funktion verwendet die [Guid-Struktur](/dotnet/api/system.guid) im .NET Framework, um den global eindeutigen Bezeichner (GUID) zu generieren.

Wenn Sie die [Option zum erneuten Bereitstellen einer vorherigen erfolgreichen Bereitstellung](../templates/rollback-on-error.md) verwenden und die vorherige Bereitstellung einen Parameter enthält, der die Funktion „newGuid“ nutzt, wird dieser Parameter nicht erneut ausgewertet. Stattdessen wird der Parameterwert der vorherigen Bereitstellung in der Rollbackbereitstellung automatisch wiederverwendet.

In einer Testumgebung müssen Sie Ressourcen, die nur kurz gespeichert werden, möglicherweise wiederholt bereitstellen. Anstatt eindeutige Namen zu erstellen, können Sie die Funktion „newGuid“ mit [uniqueString](#uniquestring) verwenden, um eindeutige Namen zu erstellen.

Seien Sie vorsichtig, wenn Sie eine Bicep-Datei erneut bereitstellen, die die newGuid-Funktion für einen Standardwert nutzt. Wenn Sie die erneute Bereitstellung durchführen und keinen Wert für den Parameter bereitstellen, wird die Funktion erneut ausgewertet. Wenn Sie eine vorhandene Ressource aktualisieren möchten, anstatt eine neue zu erstellen, übergeben Sie den Parameterwert aus der früheren Bereitstellung.

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge mit 36 Zeichen im Format eines Globally Unique Identifiers.

### <a name="examples"></a>Beispiele

Im folgenden Beispiel wird ein Parameter mit einem neuen Bezeichner veranschaulicht.

```bicep
param guidValue string = newGuid()

output guidOutput string = guidValue
```

---

Die Ausgabe des vorherigen Beispiels variiert bei jeder Bereitstellung. Sie sollte jedoch folgender ähneln:

| Name | type | Wert |
| ---- | ---- | ----- |
| guidOutput | Zeichenfolge | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

Im folgenden Beispiel wird die newGuid-Funktion verwendet, um einen eindeutigen Namen für ein Speicherkonto zu erstellen. Diese Bicep-Datei funktioniert möglicherweise in Testumgebungen, in denen das Speicherkonto nur für kurze Zeit vorhanden ist und nicht erneut bereitgestellt wird.

```bicep
param guidValue string = newGuid()

var storageName = 'storage${uniqueString(guidValue)}'

resource myStorage 'Microsoft.Storage/storageAccounts@2018-07-01' = {
  name: storageName
  location: 'West US'
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'StorageV2'
  properties: {}
}

output nameOutput string = storageName
```

Die Ausgabe des vorherigen Beispiels variiert bei jeder Bereitstellung. Sie sollte jedoch folgender ähneln:

| Name | type | Wert |
| ---- | ---- | ----- |
| nameOutput | Zeichenfolge | storagenziwvyru7uxie |

## <a name="padleft"></a>padLeft

`padLeft(valueToPad, totalLength, paddingCharacter)`

Gibt eine rechtsbündig ausgerichtete Zeichenfolge zurück, indem links Zeichen hinzugefügt werden, bis die angegebene Gesamtlänge erreicht ist.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| valueToPad |Ja |Zeichenfolge oder ganze Zahl |Der Wert, der rechtsbündig ausgerichtet werden soll. |
| totalLength |Ja |INT |Die Gesamtzahl der Zeichen in der zurückgegebenen Zeichenfolge. |
| paddingCharacter |Nein |einzelnes Zeichen |Das Zeichen, das für das Auffüllen auf der linken Seite verwendet werden soll, bis die Gesamtlänge erreicht ist. Der Standardwert ist ein Leerzeichen. |

Wenn die Länge der ursprünglichen Zeichenfolge die Anzahl der aufzufüllenden Zeichen überschreitet, werden keine Zeichen hinzugefügt.

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge mit mindestens der angegebenen Zeichenanzahl.

### <a name="examples"></a>Beispiele

Das folgende Beispiel zeigt, wie Sie den vom Benutzer angegebenen Parameterwert auffüllen, indem Sie das Nullzeichen hinzufügen, bis er die Gesamtzahl der Zeichen erreicht.

```bicep
param testString string = '123'

output stringOutput string = padLeft(testString, 10, '0')
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| stringOutput | String | 0000000123 |

## <a name="replace"></a>replace

`replace(originalString, oldString, newString)`

Gibt eine neue Zeichenfolge zurück, in der alle Instanzen einer Zeichenfolge durch eine andere Zeichenfolge ersetzt wurden.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| originalString |Ja |Zeichenfolge |Der Wert, für den alle Instanzen einer Zeichenfolge durch eine andere Zeichenfolge ersetzt wurden. |
| oldString |Ja |Zeichenfolge |Die Zeichenfolge, die aus der ursprünglichen Zeichenfolge entfernt werden soll. |
| newString |Ja |Zeichenfolge |Die Zeichenfolge, die anstelle der entfernten Zeichenfolge eingefügt werden soll. |

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge mit den ersetzten Zeichen.

### <a name="examples"></a>Beispiele

Das folgende Beispiel zeigt, wie Sie aus einer von einem Benutzer bereitgestellten Zeichenfolge alle Gedankenstriche entfernen und einen Teil der Zeichenfolge durch eine andere Zeichenfolge ersetzen.

```bicep
param testString string = '123-123-1234'

output firstOutput string = replace(testString, '-', '')
output secondOutput string = replace(testString, '1234', 'xxxx')
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| firstOutput | String | 1231231234 |
| secondOutput | String | 123-123-xxxx |

## <a name="skip"></a>skip

`skip(originalValue, numberToSkip)`

Gibt eine Zeichenfolge mit allen Zeichen gemäß der angegebenen Anzahl von Zeichen oder ein Array mit allen Elementen gemäß der angegebenen Anzahl von Elementen zurück.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| originalValue |Ja |Array oder Zeichenfolge |Array oder Zeichenfolge, wo Elemente übersprungen werden sollen. |
| numberToSkip |Ja |INT |Die Anzahl der zu überspringenden Elemente bzw. Zeichen. Wenn dieser Wert 0 (null) oder kleiner ist, werden alle Elemente oder Zeichen in dem Wert zurückgegeben. Ist er größer als die Länge des Arrays bzw. der Zeichenfolge, wird ein leeres Array bzw. eine leere Zeichenfolge zurückgegeben. |

### <a name="return-value"></a>Rückgabewert

Ein Array oder eine Zeichenfolge.

### <a name="examples"></a>Beispiele

Im folgenden Beispiel wird die angegebene Anzahl von Elementen im Array und Zeichen in der Zeichenfolge übersprungen.

```bicep
param testArray array = [
  'one'
  'two'
  'three'
]
param elementsToSkip int = 2
param testString string = 'one two three'
param charactersToSkip int = 4

output arrayOutput array = skip(testArray, elementsToSkip)
output stringOutput string = skip(testString, charactersToSkip)
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| arrayOutput | Array | ["three"] |
| stringOutput | String | two three |

## <a name="split"></a>split

`split(inputString, delimiter)`

Gibt ein Array mit Zeichenfolgen zurück, das die Teilzeichenfolgen der Eingabezeichenfolge getrennt durch die angegebenen Trennzeichen enthält.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| inputString |Ja |Zeichenfolge |Die zu teilende Zeichenfolge. |
| Trennzeichen |Ja |Zeichenfolge oder Array von Zeichenfolgen |Das Trennzeichen, das zum Teilen der Zeichenfolge verwendet werden soll. |

### <a name="return-value"></a>Rückgabewert

Ein Array der Zeichenfolgen.

### <a name="examples"></a>Beispiele

Im nächsten Beispiel wird die Eingabezeichenfolge mit einem Komma sowie entweder mit einem Komma oder Semikolon getrennt.

```bicep
param firstString string = 'one,two,three'
param secondString string = 'one;two,three'

var delimiters = [
  ','
  ';'
]

output firstOutput array = split(firstString, ',')
output secondOutput array = split(secondString, delimiters)
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| firstOutput | Array | ["one", "two", "three"] |
| secondOutput | Array | ["one", "two", "three"] |

## <a name="startswith"></a>startsWith

`startsWith(stringToSearch, stringToFind)`

Stellt fest, ob eine Zeichenfolge mit einem bestimmten Wert beginnt. Bei dem Vergleich wird Groß- und Kleinschreibung nicht unterschieden.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ja |Zeichenfolge |Der Wert, der das zu suchende Element enthält. |
| stringToFind |Ja |Zeichenfolge |Der zu suchende Wert. |

### <a name="return-value"></a>Rückgabewert

**True**, wenn das Zeichen oder die ersten Zeichen der Zeichenfolge mit dem Wert übereinstimmen, andernfalls **False**.

### <a name="examples"></a>Beispiele

Das folgende Beispiel zeigt die Verwendung der Funktionen „startsWith“ und „endsWith“:

```bicep
output startsTrue bool = startsWith('abcdef', 'ab')
output startsCapTrue bool = startsWith('abcdef', 'A')
output startsFalse bool = startsWith('abcdef', 'e')
output endsTrue bool = endsWith('abcdef', 'ef')
output endsCapTrue bool = endsWith('abcdef', 'F')
output endsFalse bool = endsWith('abcdef', 'e')
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| startsTrue | Bool | True |
| startsCapTrue | Bool | True |
| startsFalse | Bool | False |
| endsTrue | Bool | True |
| endsCapTrue | Bool | True |
| endsFalse | Bool | False |

## <a name="string"></a>Zeichenfolge

`string(valueToConvert)`

Konvertiert den angegebenen Wert in eine Zeichenfolge.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| valueToConvert |Ja | Any |Der Wert, der in eine Zeichenfolge konvertiert werden soll. Werte aller Typen können konvertiert werden, auch Objekte und Arrays. |

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge des konvertierten Werts.

### <a name="examples"></a>Beispiele

Das folgende Beispiel zeigt das Konvertieren verschiedener Typen von Werten in Zeichenfolgen:

```bicep
param testObject object = {
  'valueA': 10
  'valueB': 'Example Text'
}
param testArray array = [
  'a'
  'b'
  'c'
]
param testInt int = 5

output objectOutput string = string(testObject)
output arrayOutput string = string(testArray)
output intOutput string = string(testInt)
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| objectOutput | String | {"valueA":10,"valueB":"Example Text"} |
| arrayOutput | String | ["a","b","c"] |
| intOutput | String | 5 |

## <a name="substring"></a>substring

`substring(stringToParse, startIndex, length)`

Gibt eine Teilzeichenfolge zurück, die an der angegebenen Zeichenposition beginnt und die angegebene Anzahl von Zeichen enthält.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| stringToParse |Ja |Zeichenfolge |Die ursprüngliche Zeichenfolge, aus der die Teilzeichenfolge extrahiert wird. |
| startIndex |Nein |INT |Die nullbasierte Anfangsposition für die Teilzeichenfolge. |
| length |Nein |INT |Die Anzahl der Zeichen der Teilzeichenfolge. Muss auf eine Position innerhalb der Zeichenfolge verweisen. Muss Null (0) oder größer sein. Wenn dieser Wert nicht angegeben wird, wird der Rest der Zeichenfolge von der Startposition zurückgegeben.|

### <a name="return-value"></a>Rückgabewert

Die Teilzeichenfolge. Oder eine leere Zeichenfolge, wenn die Länge Null (0) entspricht.

### <a name="remarks"></a>Bemerkungen

Die Funktion schlägt fehl, wenn sich die Teilzeichenfolge über das Ende der Zeichenfolge erstreckt, oder wenn die Länge kleiner als Null (0) ist. Das folgende Beispiel führt zu dem Fehler „Die Parameter "index" und "length" müssen auf eine Position innerhalb der Zeichenfolge verweisen. Indexparameter: '0', Längenparameter: '11', Länge des Zeichenfolgenparameters: '10'.".

```bicep
param inputString string = '1234567890'

var prefix = substring(inputString, 0, 11)
```

### <a name="examples"></a>Beispiele

Im folgenden Beispiel wird eine Teilzeichenfolge aus einem Parameter extrahiert.

```bicep
param testString string = 'one two three'
output substringOutput string = substring(testString, 4, 3)
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| substringOutput | String | two |

## <a name="take"></a>take

`take(originalValue, numberToTake)`

Gibt eine Zeichenfolge mit der angegebenen Anzahl von Zeichen ab dem Anfang der Zeichenfolge bzw. ein Array mit der angegebenen Anzahl von Elementen ab dem Anfang des Arrays zurück.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| originalValue |Ja |Array oder Zeichenfolge |Das Array bzw. die Zeichenfolge, wo die Elemente entnommen werden sollen. |
| numberToTake |Ja |INT |Die Anzahl der zu entnehmenden Elemente bzw. Zeichen. Ist dieser Wert 0 oder kleiner, wird ein leeres Array bzw. eine leere Zeichenfolge zurückgegeben. Ist er größer als die Länge des entsprechenden Arrays bzw. der Zeichenfolge, werden alle Elemente des Arrays bzw. der Zeichenfolge zurückgegeben. |

### <a name="return-value"></a>Rückgabewert

Ein Array oder eine Zeichenfolge.

### <a name="examples"></a>Beispiele

Im folgenden Beispiel wird die angegebene Anzahl von Elementen aus dem Array und Zeichen aus der Zeichenfolge entnommen.

```bicep
param testArray array = [
  'one'
  'two'
  'three'
]
param elementsToTake int = 2
param testString string = 'one two three'
param charactersToTake int = 2

output arrayOutput array = take(testArray, elementsToTake)
output stringOutput string = take(testString, charactersToTake)
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| arrayOutput | Array | ["one", "two"] |
| stringOutput | String | on |

## <a name="tolower"></a>toLower

`toLower(stringToChange)`

Konvertiert die angegebene Zeichenfolge in Kleinbuchstaben.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| stringToChange |Ja |Zeichenfolge |Der Wert, der in Kleinbuchstaben konvertiert werden soll. |

### <a name="return-value"></a>Rückgabewert

Die in Kleinbuchstaben konvertierte Zeichenfolge.

### <a name="examples"></a>Beispiele

Im folgenden Beispiel wird ein Parameterwert in Klein- und Großbuchstaben konvertiert.

```bicep
param testString string = 'One Two Three'

output toLowerOutput string = toLower(testString)
output toUpperOutput string = toUpper(testString)
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| toLowerOutput | String | one two three |
| toUpperOutput | String | ONE TWO THREE |

## <a name="toupper"></a>toUpper

`toUpper(stringToChange)`

Konvertiert die angegebene Zeichenfolge in Großbuchstaben.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| stringToChange |Ja |Zeichenfolge |Der Wert, der in Großbuchstaben konvertiert werden soll. |

### <a name="return-value"></a>Rückgabewert

Die in Großbuchstaben konvertierte Zeichenfolge.

### <a name="examples"></a>Beispiele

Im folgenden Beispiel wird ein Parameterwert in Klein- und Großbuchstaben konvertiert.

```bicep
param testString string = 'One Two Three'

output toLowerOutput string = toLower(testString)
output toUpperOutput string = toUpper(testString)
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| toLowerOutput | String | one two three |
| toUpperOutput | String | ONE TWO THREE |

## <a name="trim"></a>trim

`trim (stringToTrim)`

Entfernt alle führenden und nachgestellten Leerzeichen aus der angegebenen Zeichenfolge.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| stringToTrim |Ja |Zeichenfolge |Der zu kürzende Wert. |

### <a name="return-value"></a>Rückgabewert

Die Zeichenfolge ohne vorangestellte oder nachstehende Leerzeichen.

### <a name="examples"></a>Beispiele

Im folgenden Beispiel werden die Leerzeichen aus dem Parameter entfernt.

```bicep
param testString string = '    one two three   '

output return string = trim(testString)
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| return | String | one two three |

## <a name="uniquestring"></a>uniqueString

`uniqueString (baseString, ...)`

Erstellt auf der Grundlage der als Parameter angegebenen Werte eine deterministische Hashzeichenfolge.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| baseString |Ja |Zeichenfolge |Der Wert, der in der Hashfunktion verwendet wird, um eine eindeutige Zeichenfolge zu erstellen. |
| Zusätzliche Parameter nach Bedarf. |Nein |Zeichenfolge |Sie können beliebig viele Zeichenfolgen hinzufügen, ganz wie sie zum Erstellen des Werts benötigt werden, der die Ebene der Eindeutigkeit angibt. |

### <a name="remarks"></a>Bemerkungen

Diese Funktion ist hilfreich, wenn Sie einen eindeutigen Namen für eine Ressource erstellen müssen. Sie geben Parameterwerte an, die den Eindeutigkeitsbereich für das Ergebnis einschränken. Sie können angeben, ob der Name bis hinunter zum Abonnement, zur Ressourcengruppe oder zur Bereitstellung eindeutig ist.

Der zurückgegebene Wert ist keine zufällige Zeichenfolge, sondern das Ergebnis einer Hashfunktion. Der zurückgegebene Wert ist 13 Zeichen lang. Er ist nicht global eindeutig. Es empfiehlt sich, den Wert mit einem Präfix aus Ihrer Benennungskonvention zu kombinieren, um einen aussagekräftigen Namen zu erstellen. Im folgenden Beispiel wird das Format des zurückgegebenen Werts veranschaulicht. Der tatsächliche Wert variiert je nach den angegebenen Parametern.

`tcvhiyu5h2o5o`

Die folgenden Beispiele zeigen, wie Sie mithilfe von uniqueString einen eindeutigen Wert für häufig verwendete Ebenen erstellen können.

Eindeutige Zuordnung zum Abonnement

```bicep
uniqueString(subscription().subscriptionId)
```

Eindeutige Zuordnung zur Ressourcengruppe

```bicep
uniqueString(resourceGroup().id)
```

Eindeutige Zuordnung zur Bereitstellung für eine Ressourcengruppe

```bicep
uniqueString(resourceGroup().id, deployment().name)
```

Das folgende Beispiel zeigt, wie Sie einen eindeutigen Namen für ein Speicherkonto auf Grundlage seiner Ressourcengruppe erstellen. Innerhalb der Ressourcengruppe ist der Name nicht eindeutig, wenn er auf die gleiche Weise erstellt wird.

```bicep
resource mystorage 'Microsoft.Storage/storageAccounts@2018-07-01' = {
  name: 'storage${uniqueString(resourceGroup().id)}'
  ...
}
```

Wenn Sie bei jeder Bereitstellung einer Bicep-Datei einen neuen eindeutigen Namen erstellen müssen und die Ressource nicht aktualisieren möchten, können Sie die [utcNow](./bicep-functions-date.md#utcnow)-Funktion mit uniqueString verwenden. Diesen Ansatz können Sie in einer Testumgebung verwenden. Ein Beispiel finden Sie unter [utcNow](./bicep-functions-date.md#utcnow). Beachten Sie, dass Sie die utcNow-Funktion nur in einem Ausdruck für den Standardwert eines Parameters verwendet werden können.

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge mit 13 Zeichen.

### <a name="examples"></a>Beispiele

Im folgenden Beispiel werden Ergebnisse von Uniquestring zurückgegeben:

```bicep
output uniqueRG string = uniqueString(resourceGroup().id)
output uniqueDeploy string = uniqueString(resourceGroup().id, deployment().name)
```

## <a name="uri"></a>uri

`uri (baseUri, relativeUri)`

Erstellt einen absoluten URI durch Kombinieren der baseUri- und der relativeUri-Zeichenfolge.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| baseUri |Ja |Zeichenfolge |Die Zeichenfolge mit dem Basis-URI. Achten Sie darauf, das Verhalten bezüglich der Behandlung des nachgestellten Schrägstrichs („/“) zu beobachten, wie im Anschluss auf die folgende Tabelle beschrieben.  |
| relativeUri |Ja |Zeichenfolge |Der Zeichenfolge mit dem relativen URI, die der Zeichenfolge mit dem Basis-URI hinzugefügt werden soll. |

* Wenn **BaseUri** mit einem nachgestellten Schrägstrich endet, ist das Ergebnis einfach **BaseUri**, gefolgt von **relativeUri**.

* Wenn **baseUri** nicht mit einem nachgestellten Schrägstrich endet, gibt es zwei Möglichkeiten.

   * Wenn **baseUri** gar keine Schrägstriche aufweist (abgesehen von „//“ in der Nähe des Anfangs), ist das Ergebnis einfach **baseUri**, gefolgt von **relativeUri**.

   * Wenn **baseUri** einige Schrägstriche aufweist, aber nicht mit einem Schrägstrich endet, wird alles ab dem letzten Schrägstrich aus **baseUri** entfernt, und das Ergebnis ist **baseUri**, gefolgt von **relativeUri**.

Im Folgenden finden Sie einige Beispiele:

```
uri('http://contoso.org/firstpath', 'myscript.sh') -> http://contoso.org/myscript.sh
uri('http://contoso.org/firstpath/', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json/', 'myscript.sh') -> http://contoso.org/firstpath/azuredeploy.json/myscript.sh
```

Vollständige Details: Die Parameter **baseUri** und **relativeUri** werden wie in [RFC 3986, Abschnitt 5](https://tools.ietf.org/html/rfc3986#section-5) angegeben aufgelöst.

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge, die den absoluten URI für den Basis- und relativen URI-Wert darstellt.

### <a name="examples"></a>Beispiele

Im folgenden Beispiel wird die Verwendung von „uri“, „uriComponent“ und „uriComponentToString“ gezeigt:

```bicep
var uriFormat = uri('http://contoso.com/resources/', 'nested/azuredeploy.json')
var uriEncoded = uriComponent(uriFormat)

output uriOutput string = uriFormat
output componentOutput string = uriEncoded
output toStringOutput string = uriComponentToString(uriEncoded)
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| uriOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | String | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponent"></a>uriComponent

`uricomponent(stringToEncode)`

Codiert einen URI.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| stringToEncode |Ja |Zeichenfolge |Der zu codierende Wert. |

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge des als URI codierten Werts.

### <a name="examples"></a>Beispiele

Im folgenden Beispiel wird die Verwendung von „uri“, „uriComponent“ und „uriComponentToString“ gezeigt:

```bicep
var uriFormat = uri('http://contoso.com/resources/', 'nested/azuredeploy.json')
var uriEncoded = uriComponent(uriFormat)

output uriOutput string = uriFormat
output componentOutput string = uriEncoded
output toStringOutput string = uriComponentToString(uriEncoded)
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| uriOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | String | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponenttostring"></a>uriComponentToString

`uriComponentToString(uriEncodedString)`

Gibt eine Zeichenfolge des als URI codierten Werts zurück.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| uriEncodedString |Ja |Zeichenfolge |Der als URI codierte Wert, der in eine Zeichenfolge konvertiert werden soll. |

### <a name="return-value"></a>Rückgabewert

Eine decodierte Zeichenfolge des als URI codierten Werts.

### <a name="examples"></a>Beispiele

Im folgenden Beispiel wird die Verwendung von „uri“, „uriComponent“ und „uriComponentToString“ gezeigt:

```bicep
var uriFormat = uri('http://contoso.com/resources/', 'nested/azuredeploy.json')
var uriEncoded = uriComponent(uriFormat)

output uriOutput string = uriFormat
output componentOutput string = uriEncoded
output toStringOutput string = uriComponentToString(uriEncoded)
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| uriOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | String | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="next-steps"></a>Nächste Schritte

* Eine Beschreibung der Abschnitte in einer Bicep-Datei finden Sie unter [Grundlegendes zur Struktur und Syntax von Bicep-Dateien](./file.md).
* Wenn Sie beim Erstellen eines Ressourcentyps eine angegebene Anzahl von Wiederholungen durchlaufen möchten, finden Sie weitere Informationen unter [Iterative Schleifen in Bicep](loops.md).
* Informationen zum Bereitstellen der von Ihnen erstellten Bicep-Datei finden Sie unter [Bereitstellen von Ressourcen mit Bicep und Azure PowerShell](./deploy-powershell.md).
