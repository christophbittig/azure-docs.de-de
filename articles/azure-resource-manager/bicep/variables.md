---
title: Variablen in Bicep
description: Informationen zum Definieren von Variablen in Bicep
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 11/12/2021
ms.openlocfilehash: 35175cff08b1470725da87015a3faef9f5bc3ed4
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494784"
---
# <a name="variables-in-bicep"></a>Variablen in Bicep

Dieser Artikel beschreibt, wie Sie Variablen in Ihrer Bicep-Datei definieren und verwenden. Sie verwenden Variablen, um die Entwicklung Ihrer Bicep-Dateien zu vereinfachen. Anstatt komplizierte Ausdrücke in der Bicep-Datei zu wiederholen, definieren Sie eine Variable, die den komplizierten Ausdruck enthält. Anschließend verwenden Sie diese Variable nach Bedarf in der gesamten Bicep-Datei.

Resource Manager löst Variablen vor Beginn der Bereitstellungsvorgänge auf. Jedes Vorkommen der Variablen in der Bicep-Datei wird von Resource Manager durch den aufgelösten Wert ersetzt.

## <a name="define-variable"></a>Definieren einer Variablen

Die Syntax zum Definieren einer Variablen lautet:

```bicep
var <variable-name> = <variable-value>
```

Eine Variable darf nicht denselben Namen wie ein Parameter, ein Modul oder eine Ressource haben.

Beachten Sie, dass Sie keinen [Datentyp](data-types.md) für die Variable angeben. Der Typ wird aus dem Wert abgeleitet. Im folgenden Beispiel wird eine Variable auf eine Zeichenfolge festgelegt.

```bicep
var stringVar = 'example value'
```

Sie können beim Erstellen der Variablen den Wert eines Parameters oder einer anderen Variablen verwenden.

```bicep
param inputValue string = 'deployment parameter'

var stringVar = 'preset variable'
var concatToVar =  '${stringVar}AddToVar'
var concatToParam = '${inputValue}AddToParam'

output addToVar string = concatToVar
output addToParam string = concatToParam
```

Im vorherigen Beispiel wird Folgendes zurückgegeben:

```json
{
  "addToParam": {
    "type": "String",
    "value": "deployment parameterAddToParam"
  },
  "addToVar": {
    "type": "String",
    "value": "preset variableAddToVar"
  }
}
```

Sie können [Bicep-Funktionen](bicep-functions.md) verwenden, um den Variablenwert zu erstellen. Im folgenden Beispiel werden Bicep-Funktionen verwendet, um einen Zeichenfolgenwert für einen Speicherkontonamen zu erstellen.

```bicep
param storageNamePrefix string = 'stg'
var storageName = '${toLower(storageNamePrefix)}${uniqueString(resourceGroup().id)}'

output uniqueStorageName string = storageName
```

Im folgenden Beispiel wird ein Wert wie der folgende zurückgegeben:

```json
"uniqueStorageName": {
  "type": "String",
  "value": "stghzuunrvapn6sw"
}
```

Sie können iterative Schleifen verwenden, wenn Sie eine Variable definieren. Im folgenden Beispiel wird ein Array von Objekten mit drei Eigenschaften erstellt.

```bicep
param itemCount int = 3

var objectArray = [for i in range(0, itemCount): {
  name: 'myDataDisk${(i + 1)}'
  diskSizeGB: '1'
  diskIndex: i
}]

output arrayResult array = objectArray
```

Die Ausgabe gibt ein Array mit den folgenden Elementen zurück:

```json
[
  {
    "name": "myDataDisk1",
    "diskSizeGB": "1",
    "diskIndex": 0
  },
  {
    "name": "myDataDisk2",
    "diskSizeGB": "1",
    "diskIndex": 1
  },
  {
    "name": "myDataDisk3",
    "diskSizeGB": "1",
    "diskIndex": 2
  }
]
```

Weitere Informationen zu den Schleifentypen, die Sie mit Variablen verwenden können, finden Sie unter [Iterative Schleifen in Bicep](loops.md).

## <a name="use-variable"></a>Verwenden einer Variablen

Im folgenden Beispiel wird gezeigt, wie die Variable für eine Ressourceneigenschaft verwendet wird. Durch Angabe des Variablennamens verweisen Sie auf den Wert der Variable: `storageName`.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/variables/variableswithfunction.bicep" highlight="4,7,15" :::

Da Speicherkontonamen Kleinbuchstaben verwenden müssen, verwendet die Variable `storageName` die Funktion `toLower`, um den Wert `storageNamePrefix` in Kleinbuchstaben zu schreiben. Die Funktion `uniqueString` erstellt einen eindeutigen Wert aus der Ressourcengruppen-ID. Die Werte werden zu einer Zeichenfolge verkettet.

## <a name="configuration-variables"></a>Konfigurationsvariablen

Sie können Variablen definieren, die zugehörige Werte zum Konfigurieren einer Umgebung enthalten. Die Variable wird als ein Objekt mit den Werten definiert. Das folgende Beispiel zeigt ein Objekt, das Werte für zwei Umgebungen enthält: **test** und **prod**. Sie übergeben einen dieser Werte während der Bereitstellung.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/variables/variablesconfigurations.bicep":::

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu den verfügbaren Eigenschaften für Variablen finden Sie unter [Verstehen der Struktur und Syntax von Bicep-Dateien](file.md).
- Informationen zur Verwendung der Schleifensyntax finden Sie unter [Iterative Schleifen in Bicep](loops.md).