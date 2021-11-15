---
title: Iterative Schleifen in Bicep
description: Verwenden von Schleifen zum Durchlaufen von Sammlungen in Bicep
ms.topic: conceptual
ms.date: 10/19/2021
ms.openlocfilehash: 9c38824fd3fdf10ce807b66aeccda2c0631cbc58
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270441"
---
# <a name="iterative-loops-in-bicep"></a>Iterative Schleifen in Bicep

In diesem Artikel erfahren Sie, wie Sie die `for`-Syntax verwenden, um Elemente in einer Sammlung zu durchlaufen. Sie können Schleifen verwenden, um mehrere Kopien einer Ressource, eines Moduls, einer Variablen, einer Eigenschaft oder einer Ausgabe zu definieren. Verwenden Sie Schleifen, um eine Wiederholungssyntax in Ihrer Bicep-Datei zu vermeiden und die Anzahl der während der Bereitstellung zu erstellenden Kopien dynamisch festzulegen.

### <a name="microsoft-learn"></a>Microsoft Learn

Weitere Informationen und praktische Anleitungen zu Schleifen finden Sie in **Microsoft Learn** unter [Erstellen flexibler Bicep-Vorlagen mithilfe von Bedingungen und Schleifen](/learn/modules/build-flexible-bicep-templates-conditions-loops/).

## <a name="loop-syntax"></a>Schleifensyntax

Schleifen können wie folgt deklariert werden:

- Durch Verwendung eines **ganzzahligen Index**. Diese Option funktioniert, wenn Ihr Szenario lautet: „Ich möchte so und so viele Instanzen erstellen“. Die [range-Funktion](bicep-functions-array.md#range) erstellt ein Array von ganzen Zahlen, das am Startindex beginnt und die Anzahl der angegebenen Elemente enthält. Innerhalb der Schleife können Sie den ganzzahligen Index verwenden, um Werte zu ändern. Weitere Informationen finden Sie unter [Ganzzahliger Index](#integer-index).

  ```bicep
  [for <index> in range(<startIndex>, <numberOfElements>): {
    ...
  }]
  ```

- Verwenden von **Elementen in einem Array**. Diese Option funktioniert wenn Ihr Szenario lautet: „Ich möchte eine Instanz für jedes Element in einem Array erstellen“. Innerhalb der Schleife können Sie den Wert des aktuellen Arrayelements verwenden, um Werte zu ändern. Weitere Informationen finden Sie unter [Arrayelemente](#array-elements).

  ```bicep
  [for <item> in <collection>: {
    ...
  }]
  ```

- Verwenden von **Elementen als Wörterbuchobjekt**. Diese Option funktioniert wenn Ihr Szenario lautet: „Ich möchte eine Instanz für jedes Element in einem Objekt erstellen“. Die [items-Funktion](bicep-functions-array.md#items) konvertiert das Objekt in ein Array. Innerhalb der Schleife können Sie Eigenschaften aus dem Objekt verwenden, um Werte zu erstellen. Weitere Informationen finden Sie unter [Wörterbuchobjekt](#dictionary-object).

  ```bicep
  [for <item> in items(<object>): {
    ...
  }]
  ```

- Verwenden von **ganzzahligen Indizes und Elementen in einem Array**. Diese Option funktioniert, wenn Ihr Szenario lautet: „Ich möchte eine Instanz für jedes Element in einem Array erstellen, aber ich benötige auch den aktuellen Index, um einen anderen Wert zu erstellen.“ Weitere Informationen finden Sie unter [Schleifenarray und -index](#array-and-index).

  ```bicep
  [for (<item>, <index>) in <collection>: {
    ...
  }]
  ```

- Hinzufügen einer **bedingten Bereitstellung**. Diese Option funktioniert, wenn Ihr Szenario lautet: „Ich möchte mehrere Instanzen erstellen, aber ich möchte die einzelnen Instanzen nur bereitstellen, wenn eine Bedingung erfüllt ist“. Weitere Informationen finden Sie unter [Schleife mit Bedingung](#loop-with-condition).

  ```bicep
  [for <item> in <collection>: if(<condition>) {
    ...
  }]
  ```

## <a name="loop-limits"></a>Schleifengrenzwerte

Die Verwendung von Schleifen in Bicep weist folgende Einschränkungen auf:

- Es dürfen nicht weniger als 0 oder mehr als 800 Schleifeniterationen verwendet werden.
- Eine Ressource mit geschachtelten untergeordneten Ressourcen kann in einer Schleife nicht verwendet werden. Ändern Sie die untergeordneten Ressourcen in Ressourcen der obersten Ebene.  Weitere Informationen finden Sie unter [Iteration für eine untergeordnete Ressource](#iteration-for-a-child-resource).
- Eine Schleife kann nicht auf mehreren Ebenen von Eigenschaften verwendet werden.

## <a name="integer-index"></a>Ganzzahliger Index

Erstellen Sie als einfaches Beispiel für die Verwendung eines Index eine **Variable**, die ein Array von Zeichenfolgen enthält.

```bicep
param itemCount int = 5

var stringArray = [for i in range(0, itemCount): 'item${(i + 1)}']

output arrayResult array = stringArray
```

Die Ausgabe gibt ein Array mit den folgenden Elementen zurück:

```json
[
  "item1",
  "item2",
  "item3",
  "item4",
  "item5"
]
```

Im nächsten Beispiel wird die Anzahl von Speicherkonten erstellt, die im Parameter `storageCount` angegeben ist. Es werden drei Eigenschaften für jedes Speicherkonto zurückgegeben.

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

output storageInfo array = [for i in range(0, storageCount): {
  id: storageAcct[i].id
  blobEndpoint: storageAcct[i].properties.primaryEndpoints.blob
  status: storageAcct[i].properties.statusOfPrimary
}]
```

Beachten Sie, dass der Index `i` zum Erstellen des Ressourcennamens für das Speicherkonto verwendet wird.

Im nächsten Beispiel wird ein Modul mehrmals bereitgestellt.

```bicep
param location string
param storageCount int = 2

var baseName = 'store${uniqueString(resourceGroup().id)}'

module stgModule './storageAccount.bicep' = [for i in range(0, storageCount): {
  name: '${i}deploy${baseName}'
  params: {
    storageName: '${i}${baseName}'
    location: location
  }
}]
```

## <a name="array-elements"></a>Arrayelemente

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

Im nächsten Beispiel wird ein Array durchlaufen, um eine Eigenschaft zu definieren. Es werden zwei Subnetze innerhalb eines virtuellen Netzwerks erstellt.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/loops/loopproperty.bicep" highlight="23-28" :::

## <a name="array-and-index"></a>Array und Index

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

Im nächsten Beispiel werden sowohl die Elemente eines Arrays als auch ein Index verwendet, um Informationen zu den neuen Ressourcen auszugeben.

```bicep
param nsgLocation string = resourceGroup().location
param orgNames array = [
  'Contoso'
  'Fabrikam'
  'Coho'
]

resource nsg 'Microsoft.Network/networkSecurityGroups@2020-06-01' = [for name in orgNames: {
  name: 'nsg-${name}'
  location: nsgLocation
}]

output deployedNSGs array = [for (name, i) in orgNames: {
  orgName: name
  nsgName: nsg[i].name
  resourceId: nsg[i].id
}]
```

## <a name="dictionary-object"></a>Wörterbuchobjekt

Verwenden Sie zum Iterieren von Elementen in einem Wörterbuchobjekt die [items-Funktion](bicep-functions-array.md#items), die das Objekt in ein Array konvertiert. Verwenden Sie die `value`-Eigenschaft, um Eigenschaften für die Objekte abzurufen.

```bicep
param nsgValues object = {
  nsg1: {
    name: 'nsg-westus1'
    location: 'westus'
  }
  nsg2: {
    name: 'nsg-east1'
    location: 'eastus'
  }
}

resource nsg 'Microsoft.Network/networkSecurityGroups@2020-06-01' = [for nsg in items(nsgValues): {
  name: nsg.value.name
  location: nsg.value.location
}]
```

## <a name="loop-with-condition"></a>Schleife mit Bedingung

Für **Ressourcen und Module** können Sie einen `if`-Ausdruck mit der Schleifensyntax hinzufügen, um die Sammlung bedingt bereitzustellen.

Das folgende Beispiel zeigt eine Schleife in Kombination mit einer Bedingungsanweisung. In diesem Beispiel wird eine einzelne Bedingung auf alle Instanzen des Moduls angewendet.

```bicep
param location string
param storageCount int = 2
param createNewStorage bool = true

var baseName = 'store${uniqueString(resourceGroup().id)}'

module stgModule './storageAccount.bicep' = [for i in range(0, storageCount): if(createNewStorage) {
  name: '${i}deploy${baseName}'
  params: {
    storageName: '${i}${baseName}'
    location: location
  }
}]
```

Das nächste Beispiel zeigt, wie eine Bedingung angewendet wird, die für das aktuelle Element im Array spezifisch ist.

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

## <a name="deploy-in-batches"></a>In Batches bereitstellen

Standardmäßig werden Azure-Ressourcen parallel bereitgestellt. Wenn Sie eine Schleife verwenden, um mehrere Instanzen eines Ressourcentyps zu erstellen, werden alle diese Instanzen gleichzeitig bereitgestellt. Die Reihenfolge, in der sie erstellt werden, ist nicht garantiert. Es gibt keine Beschränkung für die Anzahl der parallel bereitgestellten Ressourcen, mit Ausnahme der Begrenzung der Gesamtanzahl auf 800 Ressourcen in der Bicep-Datei.

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

Legen Sie für eine sequenzielle Bereitstellung die Batchgröße auf 1 fest.

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

- Informationen zum Festlegen von Abhängigkeiten von Ressourcen, die in einer Schleife erstellt werden, finden Sie unter [Ressourcenabhängigkeiten](./resource-declaration.md#dependencies).
