---
title: Vorlagenfunktionen – Vergleich
description: Informationen zu den Funktionen, die in einer Azure Resource Manager-Vorlage (ARM-Vorlage) zum Vergleichen von Werten verwendet werden können.
ms.topic: conceptual
ms.date: 09/08/2021
ms.openlocfilehash: 0f9243cdc61ad5e7710663328eb4f85c9471fda5
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124812343"
---
# <a name="comparison-functions-for-arm-templates"></a>Vergleichsfunktionen für ARM-Vorlagen

Resource Manager stellt mehrere Funktionen zum Durchführen von Vergleichen in Ihren Azure Resource Manager-Vorlagen (ARM-Vorlagen) bereit:

* [coalesce](#coalesce)
* [equals](#equals)
* [greater](#greater)
* [greaterOrEquals](#greaterorequals)
* [less](#less)
* [lessOrEquals](#lessorequals)

## <a name="coalesce"></a>coalesce

`coalesce(arg1, arg2, arg3, ...)`

Gibt den ersten Wert aus den Parametern zurück, der nicht NULL ist. Leere Zeichenfolgen, leere Arrays und leere Objekte sind nicht NULL.

In Bicep verwenden Sie stattdessen den Operator `??`. Siehe [Coalesce ??](../bicep/operators-logical.md#coalesce-).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |int, string, array oder object |Der erste Wert, der auf NULL getestet werden soll. |
| weitere Argumente |Nein  |int, string, array oder object | Weitere Werte zur Prüfung auf Null. |

### <a name="return-value"></a>Rückgabewert

Der Wert des ersten Parameters ungleich NULL, der Zeichenfolge, ganze Zahl, Array oder Objekt sein kann. NULL, wenn alle Parameter NULL sind.

### <a name="example"></a>Beispiel

Die folgende Beispielvorlage zeigt die Ausgabe bei unterschiedlichen Verwendungen von „coalesce“:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/comparison/coalesce.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| stringOutput | String | default |
| intOutput | Int | 1 |
| objectOutput | Object | {"first": "default"} |
| arrayOutput | Array | [1] |
| emptyOutput | Bool | True |

## <a name="equals"></a>equals

`equals(arg1, arg2)`

Überprüft, ob zwei Werte einander entsprechen.

In Bicep verwenden Sie stattdessen den Operator `==`. Siehe [Equals ==](../bicep/operators-comparison.md#equals-).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |int, string, array oder object |Der erste auf Gleichheit zu überprüfende Wert. |
| arg2 |Ja |int, string, array oder object |Der zweite auf Gleichheit zu überprüfende Wert. |

### <a name="return-value"></a>Rückgabewert

Gibt **True** zurück, wenn die Werte gleich sind. Andernfalls wird **False** zurückgegeben.

### <a name="remarks"></a>Hinweise

Die Funktion „equals“ wird häufig mit dem Element `condition` verwendet, um zu prüfen, ob eine Ressource bereitgestellt wurde.

```json
{
  "condition": "[equals(parameters('newOrExisting'),'new')]",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[variables('storageAccountName')]",
  "apiVersion": "2017-06-01",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "[variables('storageAccountType')]"
  },
  "kind": "Storage",
  "properties": {}
}
```

### <a name="example"></a>Beispiel

Im folgenden Beispiel werden verschiedene Arten von Werten auf Gleichheit geprüft. Alle Standardwerte geben „True“ zurück.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/comparison/equals.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | True |
| checkArrays | Bool | True |
| checkObjects | Bool | True |

In der folgenden Beispielvorlage wird [not](template-functions-logical.md#not) mit „**equals**“ verwendet.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/not-equals.json":::

Die Ausgabe aus dem vorherigen Beispiel lautet wie folgt:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

## <a name="greater"></a>greater

`greater(arg1, arg2)`

Überprüft, ob der erste Wert größer als der zweite Wert ist.

In Bicep verwenden Sie stattdessen den Operator `>`. Siehe [Größer als >](../bicep/operators-comparison.md#greater-than-).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Ganz Zahl oder Zeichenfolge |Der erste Wert für den Vergleich vom Typ „Größer als“. |
| arg2 |Ja |Ganz Zahl oder Zeichenfolge |Der zweite Wert für den Vergleich vom Typ „Größer als“. |

### <a name="return-value"></a>Rückgabewert

Gibt **True** zurück, wenn der erste Wert größer als der zweite Wert ist. Andernfalls wird **False** zurückgegeben.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird geprüft, ob der eine Wert größer als der andere ist.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/comparison/greater.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| checkInts | Bool | False |
| checkStrings | Bool | True |

## <a name="greaterorequals"></a>greaterOrEquals

`greaterOrEquals(arg1, arg2)`

Überprüft, ob der erste Wert größer als oder gleich Wert 2 ist.

In Bicep verwenden Sie stattdessen den Operator `>=`. Siehe [Größer als oder gleich >=](../bicep/operators-comparison.md#greater-than-or-equal-).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Ganz Zahl oder Zeichenfolge |Der erste Wert für den Vergleich vom Typ „Größer als oder gleich“. |
| arg2 |Ja |Ganz Zahl oder Zeichenfolge |Der zweite Wert für den Vergleich vom Typ „Größer als oder gleich“. |

### <a name="return-value"></a>Rückgabewert

Gibt **True** zurück, wenn der erste Wert größer als oder gleich Wert 2 ist. Andernfalls wird **False** zurückgegeben.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird geprüft, ob der eine Wert größer oder gleich dem anderen ist.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/comparison/greaterorequals.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| checkInts | Bool | False |
| checkStrings | Bool | True |

## <a name="less"></a>less

`less(arg1, arg2)`

Überprüft, ob der erste Wert kleiner als der zweite Wert ist.

In Bicep verwenden Sie stattdessen den Operator `<`. Siehe [Kleiner als <](../bicep/operators-comparison.md#less-than-).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Ganz Zahl oder Zeichenfolge |Der erste Wert für den Vergleich vom Typ „Kleiner als“. |
| arg2 |Ja |Ganz Zahl oder Zeichenfolge |Der zweite Wert für den Vergleich vom Typ „Kleiner als“. |

### <a name="return-value"></a>Rückgabewert

Gibt **True** zurück, wenn der erste Wert kleiner als der zweite Wert ist. Andernfalls wird **False** zurückgegeben.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird geprüft, ob der eine Wert kleiner als der andere ist.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/comparison/less.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | False |

## <a name="lessorequals"></a>lessOrEquals

`lessOrEquals(arg1, arg2)`

Überprüft, ob der erste Wert kleiner als oder gleich Wert 2 ist.

In Bicep verwenden Sie stattdessen den Operator `<=`. Siehe [Kleiner als oder gleich <=](../bicep/operators-comparison.md#less-than-or-equal-).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Ganz Zahl oder Zeichenfolge |Der erste Wert für den Vergleich vom Typ „Kleiner als oder gleich“. |
| arg2 |Ja |Ganz Zahl oder Zeichenfolge |Der zweite Wert für den Vergleich vom Typ „Kleiner als oder gleich“. |

### <a name="return-value"></a>Rückgabewert

Gibt **True** zurück, wenn der erste Wert kleiner als oder gleich Wert 2 ist. Andernfalls wird **False** zurückgegeben.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird geprüft, ob der eine Wert kleiner oder gleich dem anderen ist.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/comparison/lessorequals.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | False |

## <a name="next-steps"></a>Nächste Schritte

* Eine Beschreibung der Abschnitte in einer ARM-Vorlage finden Sie unter [Grundlegendes zur Struktur und Syntax von ARM-Vorlagen](./syntax.md).
