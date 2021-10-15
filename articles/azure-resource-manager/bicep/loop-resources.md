---
title: Bereitstellen mehrerer Instanzen von Ressourcen in Bicep
description: Verwendung von Schleifen und Arrays in einer Bicep-Datei, um mehrere Instanzen von Ressourcen bereitzustellen.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/30/2021
ms.openlocfilehash: fadd3cfac94889a187409e95331190b7d7b98b73
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129363003"
---
# <a name="resource-iteration-in-bicep"></a>Ressourceniteration in Bicep

In diesem Artikel erfahren Sie, wie Sie in einer Bicep-Datei mehrere Instanzen einer Ressource erstellen. Sie können einer `resource`-Deklaration eine Schleife hinzufügen und die Anzahl der bereitzustellenden Ressourcen dynamisch festlegen. So vermeiden Sie auch Syntaxwiederholungen in Ihrer Bicep-Datei.

Sie können eine Schleife auch mit [Modulen](loop-modules.md), [Eigenschaften](loop-properties.md), [Variablen](loop-variables.md) und [Ausgaben](loop-outputs.md) verwenden.

Wenn Sie angeben müssen, ob eine Ressource überhaupt bereitgestellt wird, finden Sie die erforderlichen Informationen unter [Element „condition“](conditional-resource-deployment.md).

### <a name="microsoft-learn"></a>Microsoft Learn

Weitere Informationen und praktische Anleitungen zu Schleifen finden Sie in **Microsoft Learn** unter [Erstellen flexibler Bicep-Vorlagen mithilfe von Bedingungen und Schleifen](/learn/modules/build-flexible-bicep-templates-conditions-loops/).

## <a name="syntax"></a>Syntax

Mit Schleifen können mehrere Ressourcen deklariert werden durch:

- Verwendung eines Schleifenindexes

  ```bicep
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <index> in range(<start>, <stop>): {
    <resource-properties>
  }]
  ```

  Weitere Informationen finden Sie unter [Schleifenindex](#loop-index).

- Durchlaufen eines Arrays

  ```bicep
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <item> in <collection>: {
    <resource-properties>
  }]
  ```

  Weitere Informationen finden Sie unter [Schleifenarray](#loop-array).

- Durchlaufen eines Arrays und eines Indexes

  ```bicep
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for (<item>, <index>) in <collection>: {
    <resource-properties>
  }]
  ```

  Weitere Informationen finden Sie unter [Schleifenarray und -index](#loop-array-and-index).

## <a name="loop-limits"></a>Schleifengrenzwerte

Bicep-Schleifen weisen die folgenden Einschränkungen auf:

- Eine Ressource mit geschachtelten untergeordneten Ressourcen kann in einer Schleife nicht verwendet werden. Sie müssen die untergeordneten Ressourcen in Ressourcen der obersten Ebene ändern.  Weitere Informationen finden Sie unter [Iteration für eine untergeordnete Ressource](#iteration-for-a-child-resource).
- Eine Schleife kann nicht auf mehreren Ebenen von Eigenschaften verwendet werden. Weitere Informationen finden Sie unter [Eigenschafteniteration in Bicep-Dateien](./loop-properties.md).
- Es dürfen nicht weniger als 0 oder mehr als 800 Schleifeniterationen verwendet werden.

## <a name="loop-index"></a>Schleifenindex

Im folgenden Beispiel wird die Anzahl von Speicherkonten erstellt, die im Parameter `storageCount` angegeben ist.

```bicep
param rgLocation string = resourceGroup().location
param storageCount int = 2

resource storageAcct 'Microsoft.Storage/storageAccounts@2021-02-01' = [for i in range(0, storageCount): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: rgLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]
```

Beachten Sie, dass der Index `i` zum Erstellen des Ressourcennamens für das Speicherkonto verwendet wird.

## <a name="loop-array"></a>Schleifenarray

Im folgenden Beispiel wird ein Speicherkonto für jeden im `storageNames`-Parameter angegebenen Namen erstellt.

```bicep
param rgLocation string = resourceGroup().location
param storageNames array = [
  'contoso'
  'fabrikam'
  'coho'
]

resource storageAcct 'Microsoft.Storage/storageAccounts@2021-02-01' = [for name in storageNames: {
  name: '${name}${uniqueString(resourceGroup().id)}'
  location: rgLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]
```

Wenn Sie Werte von den bereitgestellten Ressourcen zurückgeben möchten, können Sie eine Schleife im [Ausgabenabschnitt](loop-outputs.md) verwenden.

## <a name="loop-array-and-index"></a>Schleifenarray und -index

Im folgenden Beispiel werden beim Definieren des Speicherkontos sowohl das Arrayelement als auch der Indexwert verwendet.

```bicep
param storageAccountNamePrefix string

var storageConfigurations = [
  {
    suffix: 'local'
    sku: 'Standard_LRS'
  }
  {
    suffix: 'geo'
    sku: 'Standard_GRS'
  }
]

resource storageAccountResources 'Microsoft.Storage/storageAccounts@2021-02-01' = [for (config, i) in storageConfigurations: {
  name: '${storageAccountNamePrefix}${config.suffix}${i}'
  location: resourceGroup().location
  properties: {
    supportsHttpsTrafficOnly: true
    accessTier: 'Hot'
    encryption: {
      keySource: 'Microsoft.Storage'
      services: {
        blob: {
          enabled: true
        }
        file: {
          enabled: true
        }
      }
    }
  }
  kind: 'StorageV2'
  sku: {
    name: config.sku
  }
}]
```

## <a name="resource-iteration-with-condition"></a>Ressourceniteration mit Bedingung

Das folgende Beispiel zeigt eine geschachtelte Schleife in Kombination mit einer gefilterten Ressourcenschleife. Filter müssen Ausdrücke sein, die als boolescher Wert ausgewertet werden.

```bicep
resource parentResources 'Microsoft.Example/examples@2020-06-06' = [for parent in parents: if(parent.enabled) {
  name: parent.name
  properties: {
    children: [for child in parent.children: {
      name: child.name
      setting: child.settingValue
    }]
  }
}]
```

Filter werden auch mit [Modulschleifen](loop-modules.md) unterstützt.

## <a name="deploy-in-batches"></a>In Batches bereitstellen

Resource Manager erstellt die Ressourcen standardmäßig gleichzeitig. Wenn Sie eine Schleife verwenden, um mehrere Instanzen eines Ressourcentyps zu erstellen, werden alle diese Instanzen gleichzeitig bereitgestellt. Die Reihenfolge, in der sie erstellt werden, ist nicht garantiert. Es gibt keine Beschränkung für die Anzahl der parallel bereitgestellten Ressourcen, mit Ausnahme der Begrenzung der Gesamtanzahl auf 800 Ressourcen in der Bicep-Datei.

Es wird empfohlen, nicht alle Instanzen eines Ressourcentyps gleichzeitig zu aktualisieren. Wenn Sie z.B. eine Produktionsumgebung aktualisieren, möchten Sie die Updates möglicherweise staffeln, sodass nur eine bestimmte Anzahl von Ressourcen gleichzeitig aktualisiert wird. Sie können angeben, dass eine Teilmenge der Instanzen gleichzeitig in einem Batch zusammengefasst und bereitgestellt wird. Die anderen Instanzen warten auf den Abschluss dieses Batches.

Fügen Sie zum seriellen Bereitstellen von Instanzen einer Ressource den [BatchSize-Decorator](./file.md#resource-and-module-decorators) hinzu. Legen Sie dessen Wert auf die Anzahl von Instanzen fest, die gleichzeitig bereitgestellt werden sollen. Eine Abhängigkeit von früheren Instanzen wird in der Schleife erstellt, sodass ein Batch erst dann gestartet wird, wenn der vorherige Batch abgeschlossen wurde.

```bicep
param rgLocation string = resourceGroup().location

@batchSize(2)
resource storageAcct 'Microsoft.Storage/storageAccounts@2021-02-01' = [for i in range(0, 4): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: rgLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]
```

Legen Sie für eine rein sequenzielle Bereitstellung die Batchgröße auf „1“ fest.

Der `batchSize`-Decorator befindet sich im [sys-Namespace](bicep-functions.md#namespaces-for-functions). Wenn Sie diesen Decorator von einem anderen Element gleichen Namens unterscheiden müssen, stellen Sie dem Decorator **sys** voran: `@sys.batchSize(2)`

## <a name="iteration-for-a-child-resource"></a>Iteration für eine untergeordnete Ressource

Für eine geschachtelte untergeordnete Ressource kann keine Schleife verwendet werden. Um mehr als eine Instanz einer untergeordneten Ressource zu erstellen, ändern Sie die untergeordnete Ressource in eine Ressource der obersten Ebene.

Beispiel: Angenommen, Sie definieren in der Regel einen Dateidienst und eine Dateifreigabe als geschachtelte Ressourcen für ein Speicherkonto.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name: 'examplestorage'
  location: resourceGroup().location
  kind: 'StorageV2'
  sku: {
    name: 'Standard_LRS'
  }
  resource service 'fileServices' = {
    name: 'default'
    resource share 'shares' = {
      name: 'exampleshare'
    }
  }
}
```

Wenn Sie mehrere Dateifreigaben erstellen möchten, verschieben Sie sie außerhalb des Speicherkontos. Sie definieren die Beziehung zur übergeordneten Ressource mithilfe der `parent`-Eigenschaft.

Das folgende Beispiel zeigt, wie Sie ein Speicherkonto, einen Dateidienst und mehrere Dateifreigaben erstellen:

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name: 'examplestorage'
  location: resourceGroup().location
  kind: 'StorageV2'
  sku: {
    name: 'Standard_LRS'
  }
}

resource service 'Microsoft.Storage/storageAccounts/fileServices@2021-02-01' = {
  name: 'default'
  parent: stg
}

resource share 'Microsoft.Storage/storageAccounts/fileServices/shares@2021-02-01' = [for i in range(0, 3): {
  name: 'exampleshare${i}'
  parent: service
}]
```

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu anderen Verwendungsmöglichkeiten der Schleife finden Sie unter:
  - [Eigenschafteniteration in Bicep](loop-properties.md)
  - [Variableniteration in Bicep](loop-variables.md)
  - [Ausgabeiteration in Bicep](loop-outputs.md)
- Informationen zum Festlegen von Abhängigkeiten von Ressourcen, die in einer Schleife erstellt werden, finden Sie unter [Festlegen von Ressourcenabhängigkeiten](./resource-declaration.md#set-resource-dependencies).
