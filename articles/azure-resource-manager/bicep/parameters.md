---
title: Parameter in Bicep-Dateien
description: Anleitung zum Definieren von Parametern in Bicep-Dateien.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 11/12/2021
ms.openlocfilehash: 4345269d9c1191545a28998a38aeedb14b37e0bc
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486598"
---
# <a name="parameters-in-bicep"></a>Parameter in Bicep

Dieser Artikel beschreibt, wie Sie in Ihrer Bicep-Datei Parameter definieren und verwenden. Durch Angeben verschiedener Werte für Parameter können Sie eine Bicep-Datei für verschiedene Umgebungen wiederverwenden.

Resource Manager löst Parameterwerte vor Beginn der Bereitstellungsvorgänge auf. Jedes Vorkommen des Parameters wird von Resource Manager durch den aufgelösten Wert ersetzt.

Jeder Parameter muss auf einen der [Datentypen](data-types.md) festgelegt werden.

### <a name="microsoft-learn"></a>Microsoft Learn

Weitere Informationen zu Parametern sowie praktische Anleitungen finden Sie unter [Erstellen wiederverwendbarer Bicep-Vorlagen mithilfe von Parametern](/learn/modules/build-reusable-bicep-templates-parameters) auf **Microsoft Learn**.

## <a name="declaration"></a>Deklaration

Jeder Parameter hat einen Namen und einen [Datentyp](data-types.md). Optional können Sie einen Standardwert für den Parameter angeben.

```bicep
param <parameter-name> <parameter-data-type> = <default-value>
```

Ein Parameter kann nicht den gleichen Namen wie eine Variable, eine Ressource, eine Ausgabe oder ein anderer Parameter im gleichen Geltungsbereich aufweisen.

Das folgende Beispiel zeigt grundlegende Deklarationen von Parametern.

```bicep
param demoString string
param demoInt int
param demoBool bool
param demoObject object
param demoArray array
```

## <a name="default-value"></a>Standardwert

Sie können für einen Parameter keinen Standardwert angeben. Der Standardwert wird verwendet, wenn während der Bereitstellung kein Wert angegeben wird.

```bicep
param demoParam string = 'Contoso'
```

Ausdrücke können mit dem Standardwert verwendet werden. In Ausdrücken dürfen keine anderen Parametereigenschaften verwendet werden. Im Parameterabschnitt kann weder die [reference](bicep-functions-resource.md#reference)-Funktion noch eine der [list](bicep-functions-resource.md#list)-Funktionen verwendet werden. Diese Funktionen rufen den Laufzeitstatus der Ressource ab und können nicht vor der Bereitstellung ausgeführt werden, wenn Parameter aufgelöst werden.

```bicep
param location string = resourceGroup().location
```

Sie können einen anderen Parameterwert verwenden, um einen Standardwert zu erstellen. Mit der folgenden Vorlage wird aus dem Websitenamen ein Name für den Hostplan erstellt.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/parameters/parameterswithfunctions.bicep" highlight="2":::

## <a name="decorators"></a>Decorator-Elemente

Parameter verwenden Decorators für Einschränkungen oder Metadaten. Die Decorators haben das Format `@expression` und werden über der Deklaration des Parameters platziert. Sie können einen Parameter als sicher markieren, zulässige Werte angeben, die minimale und maximale Länge für eine Zeichenkette festlegen, den minimalen und maximalen Wert für eine ganze Zahl festlegen und eine Beschreibung des Parameters angeben.

Das folgende Beispiel zeigt zwei gängige Verwendungen für Dekoratoren.

```bicep
@secure()
param demoPassword string

@description('Must be at least Standard_A3 to support 2 NICs.')
param virtualMachineSize string = 'Standard_DS1_v2'
```

In der folgenden Tabelle werden die verfügbaren Decorator-Elemente und deren Verwendung beschrieben.

| Decorator | Anwenden auf | Argument | Beschreibung |
| --------- | ---- | ----------- | ------- |
| [Zugelassen](#allowed-values) | all | array | Zulässige Werte für den Parameter. Verwenden Sie diesen Decorator, um sicherzustellen, dass der Benutzer korrekte Werte bereitstellt. |
| [description](#description) | all | Zeichenfolge | Text, der erklärt, wie der Parameter zu verwenden ist. Die Beschreibung wird den Benutzern über das Portal angezeigt. |
| [maxLength](#length-constraints) | Array, Zeichenfolge | INT | Die maximale Länge für Zeichenfolge- und Array-Parameter. Der Stop-Wert ist inklusiv. |
| [maxValue](#integer-constraints) | INT | INT | Der maximale Wert für den ganzzahligen Parameter. Der Stop-Wert ist inklusiv. |
| metadata | all | Objekt | Benutzerdefinierte Eigenschaften, die auf den Parameter angewendet werden sollen. Kann eine Beschreibungseigenschaft enthalten, die dem Description-Decorator entspricht. |
| [minLength](#length-constraints) | Array, Zeichenfolge | INT | Die minimale Länge für Zeichenfolge- und Array-Parameter. Der Stop-Wert ist inklusiv. |
| [minValue](#integer-constraints) | INT | INT | Der minimale Wert für den ganzzahligen Parameter. Der Stop-Wert ist inklusiv. |
| [secure](#secure-parameters) | String-Objekt | none | Markiert den Parameter als sicher. Der Wert eines sicheren Parameters wird weder im Bereitstellungsverlauf gespeichert noch protokolliert. Weitere Informationen finden Sie unter Sichern von [Zeichenfolgen und Objekten](data-types.md#secure-strings-and-objects). |

Decorators befinden sich im [sys-Namespace](bicep-functions.md#namespaces-for-functions). Wenn Sie diesen Decorator von einem anderen Element gleichen Namens unterscheiden müssen, stellen Sie dem Decorator `sys` voran. Zum Beispiel, wenn Ihre Bicep Datei einen Parameter mit dem Namen `description` enthält, müssen Sie den sys Namespace hinzufügen, wenn Sie den **description** Dekorator verwenden.

```bicep
@sys.description('The name of the instance.')
param name string
@sys.description('The description of the instance to display.')
param description string
```

Die verfügbaren Dekoratoren werden in den folgenden Abschnitten beschrieben.

### <a name="secure-parameters"></a>Sichere Parameter

Sie können Zeichenfolgen- oder Objektparameter als sicher kennzeichnen. Der Wert eines sicheren Parameters wird weder im Bereitstellungsverlauf gespeichert noch protokolliert.

```bicep
@secure()
param demoPassword string

@secure()
param demoSecretObject object
```

### <a name="allowed-values"></a>Zulässige Werte

Sie können für einen Parameter zulässige Werte definieren. Diese werden in einem Array bereitgestellt. Wenn für den Parameter ein Wert übergeben wird, der nicht zu den zulässigen Werten gehört, schlägt die Bereitstellung während der Überprüfung fehl.

```bicep
@allowed([
  'one'
  'two'
])
param demoEnum string
```

### <a name="length-constraints"></a>Längenbeschränkungen

Sie können die minimale und maximale Länge von Zeichenfolgen- und Arrayparametern angeben. Sie können eine oder beide Einschränkungen festlegen. Bei Zeichenfolgen gibt die Länge die Anzahl der Zeichen an. Bei Arrays gibt die Länge die Anzahl der Elemente im Array an.

Im folgenden Beispiel werden zwei Parameter deklariert. Ein Parameter ist für den Namen eines Speicherkontos, der 3–24 Zeichen enthalten muss. Der andere Parameter ist ein Array, das 1–5 Elemente aufweisen muss.

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string

@minLength(1)
@maxLength(5)
param appNames array
```

### <a name="integer-constraints"></a>Ganzzahlige Einschränkungen

Sie können die minimalen und maximalen Werte für ganzzahlige Parameter festlegen. Sie können eine oder beide Einschränkungen festlegen.

```bicep
@minValue(1)
@maxValue(12)
param month int
```

### <a name="description"></a>BESCHREIBUNG

Fügen Sie dem Parameter eine Beschreibung hinzu, damit Benutzer verstehen, welchen Wert sie angeben sollen. Wenn Sie die Vorlage über das Portal bereitstellen, wird der in der Beschreibung angegebene Text automatisch als Tipp für diesen Parameter verwendet. Beschreibungen sollten nur hinzugefügt werden, wenn der Text mehr Informationen bietet, als aus dem Parameternamen abgeleitet werden können.

```bicep
@description('Must be at least Standard_A3 to support 2 NICs.')
param virtualMachineSize string = 'Standard_DS1_v2'
```

## <a name="use-parameter"></a>Verwenden eines Parameters

Um auf den Wert des Parameters zu verweisen, verwenden Sie den Parameternamen. Im folgenden Beispiel wird ein Parameterwert für den Namen eines Schlüsseltresors verwendet.

```bicep
param vaultName string = 'keyVault${uniqueString(resourceGroup().id)}'

resource keyvault 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: vaultName
  ...
}
```

## <a name="objects-as-parameters"></a>Objekte als Parameter

Es kann einfacher sein, in Beziehung stehende Werte zu organisieren, indem sie als Objekt übergeben werden. Diese Vorgehensweise verringert auch die Anzahl der Parameter in der Vorlage.

Das folgende Beispiel zeigt einen Parameter, bei dem es sich um ein Objekt handelt. Der Standardwert gibt die erwarteten Eigenschaften für das Objekt an. Diese Eigenschaften werden in der Definition der bereitzustellenden Ressource verwendet.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/parameters/parameterobject.bicep":::


## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu den verfügbaren Eigenschaften für Parameter finden Sie unter [Verstehen der Struktur und Syntax von Bicep-Dateien](file.md).
- Weitere Informationen zum Übergeben von Parameterwerten als Datei finden Sie unter [Erstellen einer Bicep-Parameterdatei](parameter-files.md).
