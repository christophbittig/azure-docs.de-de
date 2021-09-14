---
title: Bereitstellen mehrerer Instanzen von Modulen in Bicep
description: Verwenden Sie Schleifen und Arrays in einer Bicep-Datei, um mehrere Instanzen von Modulen bereitzustellen.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 08/27/2021
ms.openlocfilehash: 44e5a4356cac0f252b344a7acf8b68a2f51d1ef1
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123215238"
---
# <a name="module-iteration-in-bicep"></a>Moduliteration in Bicep

In diesem Artikel erfahren Sie, wie Sie in Ihrer Bicep-Datei mehr als eine Instanz von einem [Modul](modules.md) bereitstellen können. Sie können einer `module`-Deklaration eine Schleife hinzufügen und dynamisch festlegen, wie oft dieses Modul bereitgestellt werden soll. So vermeiden Sie auch das Wiederholen einer Syntax in Ihrer Bicep-Datei.

Eine Schleife kann auch mit [Ressourcen](loop-resources.md), [Eigenschaften](loop-properties.md), [Variablen](loop-variables.md), and [Ausgaben](loop-outputs.md) verwendet werden.

## <a name="syntax"></a>Syntax

Mit Schleifen können mehrere Module folgendermaßen deklariert werden:

- Verwendung eines Schleifenindexes

  ```bicep
  module <module-symbolic-name> '<module-file>' = [for <index> in range(<start>, <stop>): {
    <module-properties>
  }]
  ```

  Weitere Informationen finden Sie unter [Schleifen-Index](#loop-index).

- Durchlaufen eines Arrays

  ```bicep
  module <module-symbolic-name> '<module-file>' = [for <item> in <collection>: {
    <module-properties>
  }]
  ```

  Weitere Informationen finden Sie unter [Schleifen-Array](#loop-array).

- Iterieren über ein Array und einen Index:

  ```bicep
  module <module-symbolic-name> 'module-file' = [for (<item>, <index>) in <collection>: {
    <module-properties>
  }]
  ```

## <a name="loop-limits"></a>Schleifengrenzwerte

Bicep-Dateien dürfen keine Schleifen mit einer Anzahl von unter 0 oder über 800 Iterationen enthalten.

## <a name="loop-index"></a>Schleifen-Index

Im folgenden Beispiel wird ein Modul so oft bereitgestellt, wie im `storageCount`-Parameter angegeben. Jede Instanz des Moduls erstellt ein Speicherkonto.

```bicep
param location string = resourceGroup().location
param storageCount int = 2

var baseName = 'store${uniqueString(resourceGroup().id)}'

module stgModule './storageAccount.bicep' = [for i in range(0, storageCount): {
  name: '${i}storage${baseName}'
  params: {
    storageName: '${i}${baseName}'
    location: location
  }
}]
```

Beachten Sie, dass der Index `i` zum Erstellen des Ressourcennamens für das Speicherkonto verwendet wird. Das Speicherkonto wird als Parameterwert an das Modul übergeben.

## <a name="loop-array"></a>Schleifen-Array

Im folgenden Beispiel wird ein Speicherkonto für jeden im `storageNames`-Parameter angegebenen Namen erstellt. In diesem Beispiel wird ein Speicherkonto erstellt.

```bicep
param rgLocation string = resourceGroup().location
param storageNames array = [
  'contoso'
  'fabrikam'
  'coho'
]

module stgModule './storageAccount.bicep' = [for name in storageNames: {
  name: '${name}${uniqueString(resourceGroup().id)}'
  params: {
    storageName: name
    location: location
  }
}]

```

Das Verweisen auf eine Modulsammlung wird in Ausgabeschleifen nicht unterstützt. Um Ergebnisse von Modulen in einer Auflistung auszugeben, wenden Sie einen Array-Indexer auf den Ausdruck an. Weitere Informationen finden Sie unter [Ausgabeiteration](loop-outputs.md).

## <a name="module-iteration-with-condition"></a>Moduliteration mit Bedingung

Das folgende Beispiel zeigt eine Bicep-Datei mit einer gefilterten Modulschleife. Filter müssen Ausdrücke sein, die als boolescher Wert ausgewertet werden.

```bicep
param location string = resourceGroup().location
param storageCount int = 2
param createNewStorage bool = true

var baseName = 'store${uniqueString(resourceGroup().id)}'

module stgModule './storageAccount.bicep' = [for i in range(0, storageCount): if(createNewStorage) {
  name: '${i}storage${baseName}'
  params: {
    storageName: '${i}${baseName}'
    location: location
  }
}]
```

Im vorherigen Beispiel wird das Modul nur aufgerufen, wenn der boolesche Wert `true` ist.

Filter werden durch [Ressource-Schleifen](loop-resources.md) unterstützt.

## <a name="deploy-in-batches"></a>In Batches bereitstellen

Resource Manager erstellt die Ressourcen standardmäßig gleichzeitig. Wenn Sie eine Schleife verwenden, um mehrere Instanzen eines Ressourcentyps zu erstellen, werden alle diese Instanzen gleichzeitig bereitgestellt. Die Reihenfolge, in der sie erstellt werden, ist nicht garantiert. Es gibt keine Beschränkung für die Anzahl der parallel bereitgestellten Ressourcen, mit Ausnahme der Begrenzung der Gesamtanzahl auf 800 Ressourcen in der Bicep-Datei.

Möglicherweise möchten Sie nicht alle Instanzen eines Ressourcentyps gleichzeitig aktualisieren. Wenn Sie z.B. eine Produktionsumgebung aktualisieren, möchten Sie die Updates möglicherweise staffeln, sodass nur eine bestimmte Anzahl von Ressourcen gleichzeitig aktualisiert wird. Sie können angeben, dass eine Teilmenge der Instanzen gleichzeitig in einem Batch zusammengefasst und bereitgestellt wird. Die anderen Instanzen warten auf den Abschluss dieses Batches.

Fügen Sie zum seriellen Bereitstellen von Instanzen eines Moduls den [BatchSize-Decorator](./file.md#resource-and-module-decorators) hinzu. Legen Sie dessen Wert auf die Anzahl der Instanzen fest, die gleichzeitig bereitgestellt werden sollen. Eine Abhängigkeit von früheren Instanzen wird in der Schleife erstellt, sodass ein Batch erst dann gestartet wird, wenn der vorherige Batch abgeschlossen wurde.

```bicep
param location string = resourceGroup().location

@batchSize(2)
module stgModule './storageAccount.bicep' = [for i in range(0, 4): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  params: {
    storageName: '${i}${baseName}'
    location: location
  }
}]
```

Legen Sie für eine rein sequenzielle Bereitstellung die Batchgröße auf „1“ fest.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu anderen Verwendungsmöglichkeiten der Schleife finden Sie unter:
  - [Ressourceniteration in Bicep](loop-resources.md)
  - [Eigenschafteniteration in Bicep](loop-properties.md)
  - [Variableniteration in Bicep](loop-variables.md)
  - [Ausgabeiteration in Bicep](loop-outputs.md)
- Weitere Informationen zu Modulen finden Sie unter [Nutzung von Bicep-Modulen](modules.md).
- Informationen zum Festlegen von Abhängigkeiten von Ressourcen, die in einer Schleife erstellt werden, finden Sie unter [Festlegen von Ressourcenabhängigkeiten](./resource-declaration.md#set-resource-dependencies).
