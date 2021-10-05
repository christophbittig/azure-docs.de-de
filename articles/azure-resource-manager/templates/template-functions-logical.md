---
title: Vorlagenfunktionen – logisch
description: Informationen zu den Funktionen, die in einer Azure Resource Manager-Vorlage (ARM-Vorlage) zum Bestimmen von logischen Werten verwendet werden können.
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: b94f7aa38c708278f2ccf54a5592016873fcd285
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124744349"
---
# <a name="logical-functions-for-arm-templates"></a>Logikfunktionen für ARM-Vorlagen

Resource Manager stellt mehrere Funktionen zum Durchführen von Vergleichen in Ihren Azure Resource Manager-Vorlagen (ARM-Vorlagen) bereit:

* [and](#and)
* [bool](#bool)
* [false](#false)
* [if](#if)
* [not](#not)
* [or](#or)
* [true](#true)

## <a name="and"></a>und

`and(arg1, arg2, ...)`

Überprüft, ob alle Parameterwerte zutreffen.

Die Funktion `and` wird in Bicep nicht unterstützt. Verwenden Sie stattdessen den [&&-Operator](../bicep/operators-logical.md#and-).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |boolean |Der erste Wert, für den überprüft wird, ob er zutrifft. |
| arg2 |Ja |boolean |Der zweite Wert, für den überprüft wird, ob er zutrifft. |
| Mehr Argumente |Nein |boolean |Mehr Argumente, für die überprüft wird, ob sie zutreffen. |

### <a name="return-value"></a>Rückgabewert

Gibt **True** zurück, wenn alle Werte zutreffen. Andernfalls wird **False** zurückgegeben.

### <a name="examples"></a>Beispiele

Das folgende Beispiel zeigt die Verwendung logischer Funktionen.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/andornot.json":::

Die Ausgabe aus dem vorherigen Beispiel lautet wie folgt:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

## <a name="bool"></a>bool

`bool(arg1)`

Konvertiert den Parameter in einen booleschen Wert.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Zeichenfolge oder ganze Zahl |Der Wert, der in einen booleschen Wert konvertiert werden soll. |

### <a name="return-value"></a>Rückgabewert

Ein boolescher Wert des konvertierten Werts.

### <a name="remarks"></a>Bemerkungen

Sie können auch [true()](#true) und [false()](#false) verwenden, um boolesche Werte zu erhalten.

### <a name="examples"></a>Beispiele

Im folgenden Beispiel wird gezeigt, wie „bool“ mit einer Zeichenfolge oder ganzen Zahl verwendet wird.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/bool.json":::

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| trueString | Bool | True |
| falseString | Bool | False |
| trueInt | Bool | True |
| falseInt | Bool | False |

## <a name="false"></a>false

`false()`

Gibt false zurück.

Die Funktion `false` ist in Bicep nicht verfügbar.  Verwenden Sie stattdessen das Schlüsselwort `false`.

### <a name="parameters"></a>Parameter

Die Funktion „false“ akzeptiert keine Parameter.

### <a name="return-value"></a>Rückgabewert

Ein boolescher Wert, der immer „false“ lautet.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird ein Ausgabewert „false“ zurückgegeben.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/false.json":::

Die Ausgabe aus dem vorherigen Beispiel lautet wie folgt:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| falseOutput | Bool | False |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

Gibt einen Wert abhängig davon zurück, ob eine Bedingung zutrifft oder nicht.

Die `if`-Funktion wird in Bicep nicht unterstützt. Verwenden Sie stattdessen den [?:-Operator](../bicep/operators-logical.md#conditional-expression--).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| condition |Ja |boolean |Der Wert, für den überprüft wird, ob er zutrifft oder nicht. |
| trueValue |Ja | Zeichenfolge, Integer, Objekt oder Array |Der zurückzugebende Wert, wenn die Bedingung zutrifft. |
| falseValue |Ja | Zeichenfolge, Integer, Objekt oder Array |Der zurückzugebende Wert, wenn die Bedingung nicht zutrifft. |

### <a name="return-value"></a>Rückgabewert

Gibt den zweiten Parameter zurück, wenn der erste Parameter **True** ist, andernfalls wird der dritte Parameter zurückgegeben.

### <a name="remarks"></a>Bemerkungen

Wenn die Bedingung zutrifft (**True**), wird nur der Wert „True“ ausgewertet. Wenn die Bedingung nicht zutrifft (**False**), wird nur der Wert „False“ ausgewertet. Mit der `if`-Funktion („Wenn“) können Sie Ausdrücke einschließen, die nur bedingt gültig sind. Beispielsweise können Sie auf eine Ressource verweisen, die unter der einen Bedingung, nicht aber unter der anderen Bedingung vorhanden ist. Ein Beispiel für die bedingte Auswertung von Ausdrücken wird im nächsten Abschnitt gezeigt.

### <a name="examples"></a>Beispiele

Das folgende Beispiel zeigt die Verwendung der Funktion `if`.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/if.json":::

Die Ausgabe aus dem vorherigen Beispiel lautet wie folgt:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| yesOutput | String | ja |
| noOutput | Zeichenfolge | nein |
| objectOutput | Object | { "test": "value1" } |

In der folgenden [Beispielvorlage](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) wird gezeigt, wie Sie diese Funktion mit Ausdrücken verwenden, die nur bedingt gültig sind.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "logAnalytics": {
      "type": "string",
      "defaultValue": ""
    }
  },
  "resources": [
    {
      "condition": "[not(empty(parameters('logAnalytics')))]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "apiVersion": "2017-03-30",
      "name": "[concat(parameters('vmName'),'/omsOnboarding')]",
      "location": "[parameters('location')]",
      "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "workspaceId": "[if(not(empty(parameters('logAnalytics'))), reference(parameters('logAnalytics'), '2015-11-01-preview').customerId, json('null'))]"
        },
        "protectedSettings": {
          "workspaceKey": "[if(not(empty(parameters('logAnalytics'))), listKeys(parameters('logAnalytics'), '2015-11-01-preview').primarySharedKey, json('null'))]"
        }
      }
    }
  ],
  "outputs": {
    "mgmtStatus": {
      "type": "string",
      "value": "[if(not(empty(parameters('logAnalytics'))), 'Enabled monitoring for VM!', 'Nothing to enable')]"
    }
  }
}
```

## <a name="not"></a>not

`not(arg1)`

Konvertiert den booleschen Wert in seinen gegenteiligen Wert.

Die Funktion `not` wird in Bicep nicht unterstützt. Verwenden Sie stattdessen den [!-Operator](../bicep/operators-logical.md#not-).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |boolean |Der zu konvertierende Wert. |

### <a name="return-value"></a>Rückgabewert

Gibt **True** zurück, wenn der Parameter **False** ist. Gibt **False** zurück, wenn der Parameter **True** ist.

### <a name="examples"></a>Beispiele

Das folgende Beispiel zeigt die Verwendung logischer Funktionen.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/andornot.json":::

Die Ausgabe aus dem vorherigen Beispiel lautet wie folgt:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

Im folgenden Beispiel wird `not` mit [equals](template-functions-comparison.md#equals) (gleich) verwendet.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/not-equals.json":::

Die Ausgabe aus dem vorherigen Beispiel lautet wie folgt:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

## <a name="or"></a>oder

`or(arg1, arg2, ...)`

Überprüft, ob einer der Parameterwerte zutrifft.

Die Funktion `or` wird in Bicep nicht unterstützt. Verwenden Sie stattdessen den [||-Operator](../bicep/operators-logical.md#or-).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |boolean |Der erste Wert, für den überprüft wird, ob er zutrifft. |
| arg2 |Ja |boolean |Der zweite Wert, für den überprüft wird, ob er zutrifft. |
| Mehr Argumente |Nein |boolean |Mehr Argumente, für die überprüft wird, ob sie zutreffen. |

### <a name="return-value"></a>Rückgabewert

Gibt **True** zurück, wenn einer der Werte zutrifft. Andernfalls wird **False** zurückgegeben.

### <a name="examples"></a>Beispiele

Das folgende Beispiel zeigt die Verwendung logischer Funktionen.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/andornot.json":::

Die Ausgabe aus dem vorherigen Beispiel lautet wie folgt:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

## <a name="true"></a>true

`true()`

Gibt true zurück.

Die Funktion `true` ist in Bicep nicht verfügbar.  Verwenden Sie stattdessen das Schlüsselwort `true`.

### <a name="parameters"></a>Parameter

Die Funktion „true“ akzeptiert keine Parameter.

### <a name="return-value"></a>Rückgabewert

Ein boolescher Wert, der immer „true“ lautet.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird ein Ausgabewert „true“ zurückgegeben.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/true.json":::

Die Ausgabe aus dem vorherigen Beispiel lautet wie folgt:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| trueOutput | Bool | True |

## <a name="next-steps"></a>Nächste Schritte

* Eine Beschreibung der Abschnitte in einer ARM-Vorlage finden Sie unter [Grundlegendes zur Struktur und Syntax von ARM-Vorlagen](./syntax.md).
