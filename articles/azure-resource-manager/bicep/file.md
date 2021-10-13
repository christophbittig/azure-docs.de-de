---
title: BICEP-Dateistruktur und-Syntax
description: Beschreibt die Struktur und die Eigenschaften einer BICEP-Datei mithilfe von deklarativer Syntax.
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: e627821f80f76ff536859fd643bd01c55d50ab7e
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129350410"
---
# <a name="understand-the-structure-and-syntax-of-bicep-files"></a>Verstehen der Struktur und Syntax von ARM-Vorlagen

In diesem Artikel wird die Struktur einer BICEP-Datei beschrieben. Er zeigt die verschiedenen Abschnitte der Datei und die Eigenschaften, die in diesen Abschnitten verfügbar sind.

Dieser Artikel richtet sich an Benutzer, die bereits Vorkenntnisse zu ARM-Vorlagen haben. Er bietet detaillierte Informationen zur Struktur der Bicep-Datei. Ein schrittweises Tutorial über den Erstellungsprozess einer Bicep-Datei finden Sie unter [Schnellstart: Erstellen von Bicep-Dateien mit Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).

## <a name="bicep-format"></a>Bicep-Format

Eine Bicep Datei verfügt über die folgenden Elemente. Bicep ist eine deklarative Sprache, was bedeutet, dass die Elemente in beliebiger Reihenfolge enthalten sein können.  Im Gegensatz zu imperativen Sprachen wirkt sich die Reihenfolge der Elemente nicht darauf aus, wie die Bereitstellung verarbeitet wird.

```bicep
targetScope = '<scope>'

@<decorator>(<argument>)
param <parameter-name> <parameter-data-type> = <default-value>

var <variable-name> = <variable-value>

resource <resource-symbolic-name> '<resource-type>@<api-version>' = {
  <resource-properties>
}

// conditional deployment
resource <resource-symbolic-name> '<resource-type>@<api-version>' = if (<condition-to-deploy>) {
  <resource-properties>
}

// iterative deployment
@<decorator>(<argument>)
resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <item> in <collection>: {
  <resource-properties>
}]

module <module-symbolic-name> '<path-to-file>' = {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}

// conditional deployment
module <module-symbolic-name> '<path-to-file>' = if (<condition-to-deploy>) {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}

// iterative deployment
module <module-symbolic-name> '<path-to-file>' = [for <item> in <collection>: {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}]

// deploy to different scope
module <module-symbolic-name> '<path-to-file>' = {
  name: '<linked-deployment-name>'
  scope: <scope-object>
  params: {
    <parameter-names-and-values>
  }
}

output <output-name> <output-data-type> = <output-value>

// iterative output
output <output-name> array = [for <item> in <collection>: {
  <output-properties>
}]
```

Das folgende Beispiel zeigt eine Implementierung dieser Elemente.

```bicep
@minLength(3)
@maxLength(11)
param storagePrefix string

param storageSKU string = 'Standard_LRS'
param location string = resourceGroup().location

var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'

resource stg 'Microsoft.Storage/storageAccounts@2019-04-01' = {
  name: uniqueStorageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}

module webModule './webApp.bicep' = {
  name: 'webDeploy'
  params: {
    skuName: 'S1'
    location: location
  }
}

output storageEndpoint object = stg.properties.primaryEndpoints
```

## <a name="target-scope"></a>Zielbereich

Standardmäßig ist der Zielbereich auf festgelegt `resourceGroup`. Wenn Sie auf der Ebene der Ressourcengruppe bereitstellen, müssen Sie den Zielbereich in Ihrer Bicep-Datei nicht festlegen.

Zulässige Werte sind:

* **ResourceGroup** : Standardwert, der für [Ressourcengruppen-Bereitstellungen](deploy-to-resource-group.md)verwendet wird.
* **Abonnement** : wird für [Abonnement-Bereitstellungen](deploy-to-subscription.md)verwendet.
* **managementGroup** - wird für [Bereitstellungen der Verwaltungsgruppe](deploy-to-management-group.md) verwendet.
* **Tenant** - Verwendet für [Tenant-Bereitstellungen](deploy-to-tenant.md).

In einem Modul können Sie einen Bereich definieren, der sich vom Bereich für den Rest der Bicep-Datei unterscheiden kann. Weitere Informationen finden Sie unter [Online-Backup konfigurieren](modules.md#configure-module-scopes).

## <a name="parameters"></a>Parameter

Verwenden Sie Parameter für Werte, die für verschiedene Bereitstellungen variieren müssen. Sie können einen Standardwert für den Parameter definieren, der verwendet wird, wenn während der Bereitstellung kein Wert angegeben wird.

Beispielsweise können Sie einen SKU-Parameter hinzufügen, um unterschiedliche Größen für eine Ressource anzugeben. Sie können Bicep-Funktionen verwenden, um den Standardwert zu erstellen, z. B. den Speicherort der Ressourcengruppe.

```bicep
param storageSKU string = 'Standard_LRS'
param location string = resourceGroup().location
```

Informationen zu den verfügbaren Datentypen finden Sie unter [Datentypen in Bicep](data-types.md).

Ein Parameter darf nicht denselben Namen wie eine Variable, ein Modul oder eine Ressource haben.

Weitere Informationen finden Sie unter [Parameter in Bicep](./parameters.md).

## <a name="parameter-decorators"></a>Parameter-Decorator

Sie können für jeden Parameter ein oder mehrere Decorator-Zeichen hinzufügen. Diese Decoratoren definieren die Werte, die für den Parameter zulässig sind. Im folgenden Beispiel werden die SKUs angegeben, die über die BICEP-Datei bereitgestellt werden können.

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
param storageSKU string = 'Standard_LRS'
```

In der folgenden Tabelle werden die verfügbaren Decorator-Elemente und deren Verwendung beschrieben.

| Decorator | Anwenden auf | Argument | BESCHREIBUNG |
| --------- | ---- | ----------- | ------- |
| Zugelassen | all | array | Zulässige Werte für den Parameter. Verwenden Sie diesen Decorator, um sicherzustellen, dass der Benutzer korrekte Werte bereitstellt. |
| description | all | Zeichenfolge | Text, der erklärt, wie der Parameter zu verwenden ist. Die Beschreibung wird den Benutzern über das Portal angezeigt. |
| maxLength | Array, Zeichenfolge | INT | Die maximale Länge für Zeichenfolge- und Array-Parameter. Der Stop-Wert ist inklusiv. |
| maxValue | INT | INT | Der maximale Wert für den ganzzahligen Parameter. Der Stop-Wert ist inklusiv. |
| metadata | all | Objekt | Benutzerdefinierte Eigenschaften, die auf den Parameter angewendet werden sollen. Kann eine Beschreibungseigenschaft enthalten, die dem Description-Decorator entspricht. |
| minLength | Array, Zeichenfolge | INT | Die minimale Länge für Zeichenfolge- und Array-Parameter. Der Stop-Wert ist inklusiv. |
| minValue | INT | INT | Der minimale Wert für den ganzzahligen Parameter. Der Stop-Wert ist inklusiv. |
| secure | String-Objekt | none | Markiert den Parameter als sicher. Der Wert eines sicheren Parameters wird weder im Bereitstellungsverlauf gespeichert noch protokolliert. Weitere Informationen finden Sie unter Sichern von [Zeichenfolgen und Objekten](data-types.md#secure-strings-and-objects). |

Decorators befinden sich im [sys-Namespace](bicep-functions.md#namespaces-for-functions). Wenn Sie diesen Decorator von einem anderen Element gleichen Namens unterscheiden müssen, stellen Sie dem Decorator `sys` voran. Wenn Ihre Bicep-Datei z. B. einen Parameter mit dem Namen `description` enthält, müssen Sie den sys-Namespace hinzufügen, wenn Sie den Decorator **description** verwenden.

```bicep
@sys.description('The name of the instance.')
param name string
@sys.description('The description of the instance to display.')
param description string
```

Weitere Informationen finden Sie unter [Decorators](parameters.md#decorators).

## <a name="variables"></a>Variablen

Verwenden Sie Variablen für komplexe Ausdrücke, die in einer BICEP-Datei wiederholt werden. Beispielsweise können Sie eine Variable für einen Ressourcennamen hinzufügen, der durch Verkettung mehrerer Werte erstellt wird.

```bicep
var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'
```

Sie geben keinen [Datentyp](data-types.md) für eine Variable an. Stattdessen wird der Datentyp vom-Wert abgeleitet.

Eine Variable darf nicht denselben Namen wie ein Parameter, ein Modul oder eine Ressource haben.

Weitere Informationen finden Sie unter [Variablen in Bicep](./variables.md).

## <a name="resource"></a>Resource

Verwenden `resource` Sie das Schlüsselwort, um eine bereitzustellende Ressource zu definieren. Die Ressourcendeklaration enthält einen symbolischen Namen für die Ressource. Sie verwenden diesen symbolischen Namen in anderen Teilen der BICEP-Datei, wenn Sie einen Wert aus der Ressource erhalten müssen. Der symbolische Name kann a-z, A-Z, 0-9, und „_“ enthalten, kann aber nicht mit einer Zahl beginnen.

Die Ressourcendeklaration enthält auch den Ressourcentyp und die API-Version.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: uniqueStorageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}
```

In ihrer Ressourcendeklaration fügen Sie Eigenschaften für den Ressourcentyp ein. Diese Eigenschaften sind für jeden Ressourcentyp eindeutig.

Weitere Informationen finden Sie unter [Ressourcendeklaration in Bicep](resource-declaration.md).

Fügen Sie einen Ausdruck hinzu, um [eine Ressource bedingt](conditional-resource-deployment.md)bereitzustellen`if`.

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
  name: uniqueStorageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}
```

Fügen Sie einen Ausdruck hinzu, um [mehr als eine Instanz](https://github.com/Azure/bicep/blob/main/docs/spec/loops.md) eines Ressourcentyps bereitzustellen`for`. Der Ausdruck kann Elemente eines Arrays durchlaufen.

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  name: storageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}]
```

Eine Ressource darf nicht denselben Namen wie ein Parameter, eine Variable oder ein Modul haben.

## <a name="modules"></a>Module

Verwenden Sie Module zum Verknüpfen mit anderen BICEP-Dateien, die den Code enthalten, den Sie wieder verwenden möchten. Das Modul enthält mindestens eine bereitzustellende Ressource. Diese Ressourcen werden zusammen mit allen anderen Ressourcen in der BICEP-Datei bereitgestellt.

```bicep
module webModule './webApp.bicep' = {
  name: 'webDeploy'
  params: {
    skuName: 'S1'
    location: location
  }
}
```

Mit dem symbolischen Namen können Sie von einer anderen Stelle in der Datei auf das Modul verweisen. Beispielsweise können Sie einen Ausgabewert aus einem Modul mit dem symbolischen Namen und dem Namen des Ausgabewerts erhalten. Der symbolische Name kann a-z, A-Z, 0-9, und „_“ enthalten, kann aber nicht mit einer Zahl beginnen.

Ein Modul darf nicht denselben Namen wie ein Parameter, eine Variable oder eine Ressource haben.

Ebenso wie Ressourcen können Sie ein Modul bedingt oder iterativ bereitstellen. Die Syntax ist für Module wie Ressourcen identisch.

Weitere Informationen finden Sie unter [Was ist Bicep (Vorschau)?](./modules.md).

## <a name="resource-and-module-decorators"></a>Ressourcen-und Modul-Decorator

Sie können einer Ressourcen-oder Modul Definition ein Decorator-Modul hinzufügen. Der einzige unterstützte Decorator ist `batchSize(int)`. Sie können ihn nur auf eine Ressourcen- oder Moduldefinition anwenden, `for`die einen Ausdruck verwendet.

Standardmäßig werden Ressourcen parallel bereitgestellt. Sie wissen nicht, in welcher Reihenfolge Sie abgeschlossen sind. Wenn Sie den `batchSize`Decorator hinzufügen, stellen Sie Instanzen seriell bereit. Verwenden Sie das integere Argument, um die Anzahl von Instanzen anzugeben, die parallel bereitgestellt werden sollen.

```bicep
@batchSize(3)
resource storageAccountResources 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  ...
}]
```

Der `batchSize`-Decorator befindet sich im [sys-Namespace](bicep-functions.md#namespaces-for-functions). Wenn Sie diesen Decorator von einem anderen Element gleichen Namens unterscheiden müssen, stellen Sie dem Decorator **sys** voran: `@sys.batchSize(2)`

Weitere Informationen finden Sie unter [Bereitstellung in Batches](loop-resources.md#deploy-in-batches).

## <a name="outputs"></a>Ausgaben

Verwenden Sie Ausgänge, um Werte aus der Bereitstellung zurückzugeben. In der Regel geben Sie einen Wert aus einer bereitgestellten Ressource zurück, wenn Sie diesen Wert für einen anderen Vorgang wieder verwenden müssen.

```bicep
output storageEndpoint object = stg.properties.primaryEndpoints
```

Geben Sie einen [Datentyp](data-types.md) für den Ausgabewert an.

Eine Ausgabe darf nicht denselben Namen wie ein Parameter, eine Variable, ein Modul oder eine Ressource haben.

Weitere Informationen finden Sie unter [Ausgaben in Bicep](./outputs.md).

## <a name="whitespace"></a>Leerraum

Leerzeichen und Tabstoppzeichen werden beim Erstellen von Bicep-Dateien ignoriert. Neue Zeilen haben aber eine semantische Bedeutung, z. B. bei Deklarationen von [Objekten](./data-types.md#objects) und [Arrays](./data-types.md#arrays).

## <a name="comments"></a>Kommentare

Wird `//` für einzeilige Kommentare oder `/* ... */` für mehrzeilenbasierte Kommentare verwendet.

Das folgende Beispiel zeigt einen einzeiligen Kommentar.

```bicep
// This is your primary NIC.
resource nic1 'Microsoft.Network/networkInterfaces@2020-06-01' = {
   ...
}
```

Das folgende Beispiel zeigt einen mehrzeiligen Kommentar.

```bicep
/*
  This Bicep file assumes the key vault already exists and
  is in same subscription and resource group as the deployment.
*/
param existingKeyVaultName string
```

## <a name="multi-line-strings"></a>Mehrzeilige Zeichenfolgen

Sie können eine Zeichenfolge in mehrere Zeilen unterteilen. Verwenden Sie drei einfache Anführungszeichen `'''`, um die mehrzeilige Zeichenfolge zu starten und zu beenden.

Zeichen innerhalb der mehrzeiligen Zeichenfolge werden unverändert behandelt. Escapezeichen sind nicht erforderlich. Sie können nicht `'''` in die mehrzeilige Zeichenfolge einschließen. Zeichen folgen Interpolationen werden zurzeit nicht unterstützt.

Sie können entweder die Zeichenfolge direkt nach dem Öffnen `'''` oder eine neue Zeile einschließen. In beiden Fällen enthält die resultierende Zeichenfolge keine neue Zeile. Abhängig von den Zeilenenden in der BICEP-Datei werden neue Zeilen als `\r\n` oder `\n`interpretiert.

Das folgende Beispiel zeigt eine mehrzeilige Zeichenkette.

```bicep
var stringVar = '''
this is multi-line
  string with formatting
  preserved.
'''
```

Das vorangegangene Beispiel entspricht dem folgenden JSON.

```json
"variables": {
  "stringVar": "this is multi-line\r\n  string with formatting\r\n  preserved.\r\n"
}
```

## <a name="next-steps"></a>Nächste Schritte

Eine Einführung in BICEP finden Sie unter [Was ist BICEP?](./overview.md). Informationen zu Bicep-Datentypen finden Sie unter [Datentypen](./data-types.md).
