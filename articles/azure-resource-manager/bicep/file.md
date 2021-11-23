---
title: BICEP-Dateistruktur und-Syntax
description: Beschreibt die Struktur und die Eigenschaften einer BICEP-Datei mithilfe von deklarativer Syntax.
ms.topic: conceptual
ms.date: 11/12/2021
ms.openlocfilehash: 352ff708b9b36eff06be8f3a3dda10b28b02e37b
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493981"
---
# <a name="understand-the-structure-and-syntax-of-bicep-files"></a>Verstehen der Struktur und Syntax von ARM-Vorlagen

In diesem Artikel wird die Struktur und Syntax einer Bicep-Datei beschrieben. Er zeigt die verschiedenen Abschnitte der Datei und die Eigenschaften, die in diesen Abschnitten verfügbar sind.

Ein schrittweises Tutorial über den Erstellungsprozess einer Bicep-Datei finden Sie unter [Schnellstart: Erstellen von Bicep-Dateien mit Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).

## <a name="bicep-format"></a>Bicep-Format

Bicep ist eine deklarative Sprache, was bedeutet, dass die Elemente in beliebiger Reihenfolge enthalten sein können. Im Gegensatz zu imperativen Sprachen wirkt sich die Reihenfolge der Elemente nicht darauf aus, wie die Bereitstellung verarbeitet wird.

Eine Bicep Datei verfügt über die folgenden Elemente.

```bicep
targetScope = '<scope>'

@<decorator>(<argument>)
param <parameter-name> <parameter-data-type> = <default-value>

var <variable-name> = <variable-value>

resource <resource-symbolic-name> '<resource-type>@<api-version>' = {
  <resource-properties>
}

module <module-symbolic-name> '<path-to-file>' = {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}

output <output-name> <output-data-type> = <output-value>
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

In einem Modul können Sie einen Bereich definieren, der sich vom Bereich für den Rest der Bicep-Datei unterscheiden kann. Weitere Informationen finden Sie unter [Online-Backup konfigurieren](modules.md#set-module-scope).

## <a name="parameters"></a>Parameter

Verwenden Sie Parameter für Werte, die für verschiedene Bereitstellungen variieren müssen. Sie können einen Standardwert für den Parameter definieren, der verwendet wird, wenn während der Bereitstellung kein Wert angegeben wird.

Beispielsweise können Sie einen SKU-Parameter hinzufügen, um unterschiedliche Größen für eine Ressource anzugeben. Sie können unterschiedliche Werte übergeben, je nachdem, ob Sie die Bereitstellung in einer Test- oder Produktionsumgebungen durchführen.

```bicep
param storageSKU string = 'Standard_LRS'
```

Der Parameter kann in Ihrer Bicep-Datei verwendet werden.

```bicep
sku: {
  name: storageSKU
}
```

Weitere Informationen finden Sie unter [Parameter in Bicep](./parameters.md).

## <a name="parameter-decorators"></a>Parameter-Decorator

Sie können für jeden Parameter ein oder mehrere Decorator-Zeichen hinzufügen. Diese Decorator-Elemente beschreiben den Parameter und definieren Einschränkungen für die übergebenen Werte. Das folgende Beispiel zeigt einen Decorator, es sind aber noch viele weitere verfügbar.

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
param storageSKU string = 'Standard_LRS'
```

Weitere Informationen, einschließlich Beschreibungen aller verfügbaren Decorator-Elemente, finden Sie unter [Decorators](parameters.md#decorators).

## <a name="variables"></a>Variablen

Sie können die Lesbarkeit Ihrer Bicep-Datei verbessern, indem Sie komplexe Ausdrücke in einer Variable kapseln. Beispielsweise können Sie eine Variable für einen Ressourcennamen hinzufügen, der durch Verkettung mehrerer Werte erstellt wird.

```bicep
var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'
```

Wenden Sie diese Variable überall an, wo Sie den komplexen Ausdruck benötigen.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-04-01' = {
  name: uniqueStorageName
```

Weitere Informationen finden Sie unter [Variablen in Bicep](./variables.md).

## <a name="resource"></a>Resource

Verwenden `resource` Sie das Schlüsselwort, um eine bereitzustellende Ressource zu definieren. Die Ressourcendeklaration enthält einen symbolischen Namen für die Ressource. Sie verwenden diesen symbolischen Namen in anderen Teilen der Bicep-Datei, um einen Wert aus der Ressource abzurufen.

Die Ressourcendeklaration enthält den Ressourcentyp und die API-Version. Schließen Sie im Text der Ressourcendeklaration Eigenschaften ein, die für den Ressourcentyp spezifisch sind.

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

Weitere Informationen finden Sie unter [Ressourcendeklaration in Bicep](resource-declaration.md).

## <a name="modules"></a>Module

Mit Modulen können Sie Code aus einer Bicep-Datei in anderen Bicep-Dateien wiederverwenden. Sie geben in der Moduldeklaration einen Link zu der Datei an, die Sie wiederverwenden möchten. Wenn Sie die Bicep-Datei bereitstellen, werden auch die Ressourcen im Modul bereitgestellt.

```bicep
module webModule './webApp.bicep' = {
  name: 'webDeploy'
  params: {
    skuName: 'S1'
    location: location
  }
}
```

Mit dem symbolischen Namen können Sie von einer anderen Stelle in der Datei auf das Modul verweisen. Beispielsweise können Sie einen Ausgabewert aus einem Modul mit dem symbolischen Namen und dem Namen des Ausgabewerts erhalten.

Weitere Informationen finden Sie unter [Was ist Bicep (Vorschau)?](./modules.md).

## <a name="resource-and-module-decorators"></a>Ressourcen-und Modul-Decorator

Sie können einer Ressourcen-oder Modul Definition ein Decorator-Modul hinzufügen. Der einzige unterstützte Decorator ist `batchSize(int)`. Sie können ihn nur auf eine Ressourcen- oder Moduldefinition anwenden, `for`die einen Ausdruck verwendet.

Standardmäßig werden Ressourcen parallel bereitgestellt. Wenn Sie den `batchSize`Decorator hinzufügen, stellen Sie Instanzen seriell bereit.

```bicep
@batchSize(3)
resource storageAccountResources 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  ...
}]
```

Weitere Informationen finden Sie unter [Bereitstellung in Batches](loops.md#deploy-in-batches).

## <a name="outputs"></a>Ausgaben

Verwenden Sie Ausgänge, um Werte aus der Bereitstellung zurückzugeben. In der Regel geben Sie einen Wert aus einer bereitgestellten Ressource zurück, wenn Sie diesen Wert für einen anderen Vorgang wieder verwenden müssen.

```bicep
output storageEndpoint object = stg.properties.primaryEndpoints
```

Weitere Informationen finden Sie unter [Ausgaben in Bicep](./outputs.md).

## <a name="loops"></a>Schleifen

Sie können Ihrer Bicep-Datei iterative Schleifen hinzufügen, um mehrere Kopien von folgenden Elementen zu definieren:

* resource
* module
* -Variable
* property
* output

Verwenden Sie den `for`-Ausdruck, um eine Schleife zu definieren.

```bicep
param moduleCount int = 2

module stgModule './example.bicep' = [for i in range(0, moduleCount): {
  name: '${i}deployModule'
  params: {
  }
}]
```

Sie können ein Array, ein Objekt oder einen ganzzahligen Index durchlaufen.

Weitere Informationen finden Sie unter [Iterative Schleifen in Bicep](loops.md).

## <a name="conditional-deployment"></a>Bedingte Bereitstellung

Sie können Ihrer Bicep-Datei eine Ressource oder ein Modul hinzufügen, die bzw. das bedingt bereitgestellt wird. Während der Bereitstellung wird die Bedingung ausgewertet. Das Ergebnis bestimmt dann, ob die Ressource oder das Modul bereitgestellt wird. Verwenden Sie den `if`-Ausdruck, um eine bedingte Bereitstellung zu definieren.

```bicep
param deployZone bool

resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = if (deployZone) {
  name: 'myZone'
  location: 'global'
}
```

Für weitere Informationen siehe [Bedingter Einsatz in Bicep](conditional-resource-deployment.md).

## <a name="whitespace"></a>Leerraum

Leerzeichen und Tabstoppzeichen werden beim Erstellen von Bicep-Dateien ignoriert.

Bicep reagiert empfindlich auf Zeilenvorschübe. Zum Beispiel:

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
  ...
}
```

Kann nicht wie folgt geschrieben werden:

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' =
    if (newOrExisting == 'new') {
      ...
    }
```

Definieren Sie [Objekte](./data-types.md#objects) und [Arrays](./data-types.md#arrays) auf mehreren Zeilen.

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

## <a name="known-limitations"></a>Bekannte Einschränkungen

- Keine Unterstützung für das „apiProfile“-Konzept, das für die Zuordnung eines einzelnen „apiProfile“ zu einer festgelegten „apiVersion“ für jeden Ressourcentyp verwendet wird.
- Keine Unterstützung für benutzerdefinierte Funktionen.

## <a name="next-steps"></a>Nächste Schritte

Eine Einführung in BICEP finden Sie unter [Was ist BICEP?](./overview.md). Informationen zu Bicep-Datentypen finden Sie unter [Datentypen](./data-types.md).
