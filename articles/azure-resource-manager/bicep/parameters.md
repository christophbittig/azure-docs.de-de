---
title: Parameter in Bicep-Dateien
description: Anleitung zum Definieren von Parametern in Bicep-Dateien.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 8701d437a34d364ff6f6e2d58cbf84dc28a79798
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634211"
---
# <a name="parameters-in-bicep"></a>Parameter in Bicep

Dieser Artikel beschreibt, wie Sie in Ihrer Bicep-Datei Parameter definieren und verwenden. Durch Angeben verschiedener Werte für Parameter können Sie eine Bicep-Datei für verschiedene Umgebungen wiederverwenden.

Resource Manager löst Parameterwerte vor Beginn der Bereitstellungsvorgänge auf. Jedes Vorkommen des Parameters wird von Resource Manager durch den aufgelösten Wert ersetzt.

Jeder Parameter muss auf einen der [Datentypen](data-types.md) festgelegt werden.

## <a name="minimal-declaration"></a>Minimale Deklaration

Jeder Parameter benötigt einen Namen und einen Typ. Ein Parameter kann nicht den gleichen Namen wie eine Variable, eine Ressource, eine Ausgabe oder ein anderer Parameter im gleichen Geltungsbereich aufweisen.

```bicep
param demoString string
param demoInt int
param demoBool bool
param demoObject object
param demoArray array
```

## <a name="decorators"></a>Decorator-Elemente

Parameter verwenden Decorators für Einschränkungen oder Metadaten. Die Decorators haben das Format `@expression` und werden über der Deklaration des Parameters platziert.

```bicep
@expression
param stgAcctName string
```

Die folgenden Abschnitte dieses Artikels erfahren die Verwendung der in einer Bicep-Datei verfügbaren Decorators.

## <a name="secure-parameters"></a>Sichere Parameter

Sie können Zeichenfolgen- oder Objektparameter als sicher kennzeichnen. Der Wert eines sicheren Parameters wird weder im Bereitstellungsverlauf gespeichert noch protokolliert.

```bicep
@secure()
param demoPassword string

@secure()
param demoSecretObject object
```

## <a name="allowed-values"></a>Zulässige Werte

Sie können für einen Parameter zulässige Werte definieren. Diese werden in einem Array bereitgestellt. Wenn für den Parameter ein Wert übergeben wird, der nicht zu den zulässigen Werten gehört, schlägt die Bereitstellung während der Überprüfung fehl.

```bicep
@allowed([
  'one'
  'two'
])
param demoEnum string
```

## <a name="default-value"></a>Standardwert

Sie können für einen Parameter keinen Standardwert angeben. Der Standardwert wird verwendet, wenn während der Bereitstellung kein Wert angegeben wird.

```bicep
param demoParam string = 'Contoso'
```

Wenn Sie für den Parameter einen Standardwert zusammen mit anderen Eigenschaften angeben möchten, verwenden Sie folgende Syntax.

```bicep
@allowed([
  'Contoso'
  'Fabrikam'
])
param demoParam string = 'Contoso'
```

Ausdrücke können mit dem Standardwert verwendet werden. Im Parameterabschnitt kann weder die [reference](bicep-functions-resource.md#reference)-Funktion noch eine der [list](bicep-functions-resource.md#list)-Funktionen verwendet werden. Diese Funktionen rufen den Laufzeitstatus der Ressource ab und können nicht vor der Bereitstellung ausgeführt werden, wenn Parameter aufgelöst werden.

In Ausdrücken dürfen keine anderen Parametereigenschaften verwendet werden.

```bicep
param location string = resourceGroup().location
```

Sie können einen anderen Parameterwert verwenden, um einen Standardwert zu erstellen. Mit der folgenden Vorlage wird aus dem Websitenamen ein Name für den Hostplan erstellt.

```bicep
param siteName string = 'site${uniqueString(resourceGroup().id)}'
param hostingPlanName string = '${siteName}-plan'
```

## <a name="length-constraints"></a>Längenbeschränkungen

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

## <a name="integer-constraints"></a>Ganzzahlige Einschränkungen

Sie können die minimalen und maximalen Werte für ganzzahlige Parameter festlegen. Sie können eine oder beide Einschränkungen festlegen.

```bicep
@minValue(1)
@maxValue(12)
param month int
```

## <a name="description"></a>BESCHREIBUNG

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

```bicep
param vNetSettings object = {
  name: 'VNet1'
  location: 'eastus'
  addressPrefixes: [
    {
      name: 'firstPrefix'
      addressPrefix: '10.0.0.0/22'
    }
  ]
  subnets: [
    {
      name: 'firstSubnet'
      addressPrefix: '10.0.0.0/24'
    }
    {
      name: 'secondSubnet'
      addressPrefix: '10.0.1.0/24'
    }
  ]
}
resource vnet 'Microsoft.Network/virtualNetworks@2020-06-01' = {
  name: vNetSettings.name
  location: vNetSettings.location
  properties: {
    addressSpace: {
      addressPrefixes: [
        vNetSettings.addressPrefixes[0].addressPrefix
      ]
    }
    subnets: [
      {
        name: vNetSettings.subnets[0].name
        properties: {
          addressPrefix: vNetSettings.subnets[0].addressPrefix
        }
      }
      {
        name: vNetSettings.subnets[1].name
        properties: {
          addressPrefix: vNetSettings.subnets[1].addressPrefix
        }
      }
    ]
  }
}
```

## <a name="example-templates"></a>Beispielvorlagen

In den folgenden Beispielen werden Szenarien für die Verwendung von Parametern veranschaulicht.

|Vorlage  |Beschreibung  |
|---------|---------|
|[Parameter mit Funktionen für Standardwerte](https://github.com/Azure/azure-docs-bicep-samples/blob/main/bicep/parameterswithfunctions.bicep) | Zeigt, wie Bicep-Funktionen verwendet werden, wenn Sie Standardwerte für Parameter definieren. Die Bicep-Datei stellt keine Ressourcen bereit. Sie erstellt Parameterwerte und gibt diese Werte zurück. |
|[Parameterobjekt](https://github.com/Azure/azure-docs-bicep-samples/blob/main/bicep/parameterobject.bicep) | Veranschaulicht die Verwendung eines Objekts für einen Parameter. Die Bicep-Datei stellt keine Ressourcen bereit. Sie erstellt Parameterwerte und gibt diese Werte zurück. |

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu den verfügbaren Eigenschaften für Parameter finden Sie unter [Verstehen der Struktur und Syntax von Bicep-Dateien](file.md).
- Weitere Informationen zum Übergeben von Parameterwerten als Datei finden Sie unter [Erstellen einer Bicep-Parameterdatei](parameter-files.md).
