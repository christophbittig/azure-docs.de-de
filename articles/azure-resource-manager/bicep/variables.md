---
title: Variablen in Bicep
description: Informationen zum Definieren von Variablen in Bicep
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: b2f696adbad88cd424f2292b333069a7b80a13b2
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634955"
---
# <a name="variables-in-bicep"></a>Variablen in Bicep

Dieser Artikel beschreibt, wie Sie Variablen in Ihrer Bicep-Datei definieren und verwenden. Sie verwenden Variablen, um die Entwicklung Ihrer Bicep-Dateien zu vereinfachen. Anstatt komplizierte Ausdrücke in der Bicep-Datei zu wiederholen, definieren Sie eine Variable, die den komplizierten Ausdruck enthält. Anschließend verwenden Sie diese Variable nach Bedarf in der gesamten Bicep-Datei.

Resource Manager löst Variablen vor Beginn der Bereitstellungsvorgänge auf. Jedes Vorkommen der Variablen in der Bicep-Datei wird von Resource Manager durch den aufgelösten Wert ersetzt.

## <a name="define-variable"></a>Definieren einer Variablen

Wenn Sie eine Variable definieren, geben Sie keinen [Datentyp](data-types.md) für die Variable an. Sie geben stattdessen einen Wert oder einen Vorlagenausdruck an. Der Variablentyp wird vom aufgelösten Wert abgeleitet. Im folgenden Beispiel wird eine Variable auf eine Zeichenfolge festgelegt.

```bicep
var stringVar = 'example value'
```

Sie können beim Erstellen der Variablen den Wert eines Parameters oder einer anderen Variablen verwenden.

```bicep
param inputValue string = 'deployment Parameter'

var stringVar = 'myVariable'
var concatToVar =  '${stringVar}AddToVar'
var concatToParam = '${inputValue}AddToParam'
```

Sie können [Bicep-Funktionen](bicep-functions.md) verwenden, um den Variablenwert zu erstellen. Die Funktionen [reference](bicep-functions-resource.md#reference) und [list](bicep-functions-resource.md#list) sind beim Deklarieren von Variablen gültig.

Im folgenden Beispiel wird ein Zeichenfolgenwert für ein Speicherkonto erstellt. Es werden mehrere Bicep-Funktionen verwendet, um einen Parameterwert abzurufen und zu einer eindeutigen Zeichenfolge zu verketten.

```bicep
var storageName = '${toLower(storageNamePrefix)}${uniqueString(resourceGroup().id)}'
```

## <a name="use-variable"></a>Verwenden einer Variablen

Im folgenden Beispiel wird gezeigt, wie die Variable für eine Ressourceneigenschaft verwendet wird. Durch Angabe des Variablennamens verweisen Sie auf den Wert der Variable: `storageName`.

```bicep
param rgLocation string = resourceGroup().location
param storageNamePrefix string = 'STG'

var storageName = '${toLower(storageNamePrefix)}${uniqueString(resourceGroup().id)}'

resource demoAccount 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name: storageName
  location: rgLocation
  kind: 'Storage'
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
}

output stgOutput string = storageName
```

Da Speicherkontonamen Kleinbuchstaben verwenden müssen, verwendet die Variable `storageName` die Funktion `toLower`, um den Wert `storageNamePrefix` in Kleinbuchstaben zu schreiben. Die Funktion `uniqueString` erstellt einen eindeutigen Wert aus der Ressourcengruppen-ID. Die Werte werden zu einer Zeichenfolge verkettet.

## <a name="example-template"></a>Beispielvorlage

Von der folgenden Vorlage werden keine Ressourcen bereitgestellt. Sie zeigt einige Möglichkeiten, Variablen unterschiedlicher Typen zu deklarieren.

:::code language="bicep" source="~/azure-docs-bicep-samples/bicep/variables.bicep":::

## <a name="configuration-variables"></a>Konfigurationsvariablen

Sie können Variablen definieren, die zugehörige Werte zum Konfigurieren einer Umgebung enthalten. Die Variable wird als ein Objekt mit den Werten definiert. Das folgende Beispiel zeigt ein Objekt, das Werte für zwei Umgebungen enthält: **test** und **prod**. Sie übergeben einen dieser Werte während der Bereitstellung.

:::code language="bicep" source="~/azure-docs-bicep-samples/bicep/variablesconfigurations.bicep":::

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu den verfügbaren Eigenschaften für Variablen finden Sie unter [Verstehen der Struktur und Syntax von Bicep-Dateien](file.md).
