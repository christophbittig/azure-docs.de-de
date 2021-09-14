---
title: Definieren mehrerer Instanzen eines Ausgabewerts in Bicep
description: Verwenden Sie eine Bicep-Ausgabeschleife, um Bereitstellungswerte zu durchlaufen und zurückzugeben.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: 67c4a99dbdb370202e4bbf080f32c626b71093b1
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123213835"
---
# <a name="output-iteration-in-bicep"></a>Ausgabeiteration in Bicep

In diesem Artikel wird gezeigt, wie Sie mehr als einen Wert für eine Ausgabe in Ihrer Bicep-Datei erstellen. Sie können dem Abschnitt `output` der Datei eine Schleife hinzufügen und während der Bereitstellung dynamisch mehrere Elemente zurückgeben.

Sie können auch eine Schleife mit [Modulen](loop-modules.md), [Ressourcen](loop-resources.md), [Eigenschaften in einer Ressource](loop-properties.md) und [Variablen](loop-variables.md) verwenden.

## <a name="syntax"></a>Syntax

Mit Schleifen können Sie Elemente während der Bereitstellung zurückgeben durch:

- Verwendung eines Schleifenindexes

  ```bicep
  output <output-name> array = [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

  Weitere Informationen finden Sie unter [Schleifen-Index](#loop-index).

- Durchlaufen eines Arrays

  ```bicep
  output <output-name> array = [for <item> in <collection>: {
    <properties>
  }]

  ```

- Iterieren über ein Array und einen Index.

  ```bicep
  output <output-name> array = [for <item>, <index> in <collection>: {
    <properties>
  }]
  ```

  Weitere Informationen finden Sie unter [Schleifen-Array und -Index](#loop-array-and-index).

## <a name="loop-limits"></a>Schleifengrenzwerte

Bicep-Dateien dürfen keine Schleifen mit einer Anzahl von unter 0 oder über 800 Iterationen enthalten. 

## <a name="loop-index"></a>Schleifen-Index

Im folgenden Beispiel wird eine variable Anzahl von Speicherkonten erstellt und für jedes Speicherkonto ein Endpunkt zurückgegeben:

```bicep
param rgLocation string = resourceGroup().location
param storageCount int = 2

var baseNameVar = 'storage${uniqueString(resourceGroup().id)}'

resource stg 'Microsoft.Storage/storageAccounts@2021-02-01' = [for i in range(0, storageCount): {
  name: '${i}${baseNameVar}'
  location: rgLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]

output storageEndpoints array = [for i in range(0, storageCount): stg[i].properties.primaryEndpoints.blob]
```

Die Ausgabe gibt ein Array mit den folgenden Elementen zurück:

```json
[
  "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
  "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

Im nächsten Beispiel werden drei Eigenschaften aus den neuen Speicherkonten zurückgegeben.

```bicep
param rgLocation string = resourceGroup().location
param storageCount int = 2

var baseNameVar = 'storage${uniqueString(resourceGroup().id)}'

resource stg 'Microsoft.Storage/storageAccounts@2021-02-01' = [for i in range(0, storageCount): {
  name: '${i}${baseNameVar}'
  location: rgLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]

output storageInfo array = [for i in range(0, storageCount): {
  id: stg[i].id
  blobEndpoint: stg[i].properties.primaryEndpoints.blob
  status: stg[i].properties.statusOfPrimary
}]
```

Die Ausgabe gibt ein Array mit den folgenden Werten zurück:

```json
[
  {
    "id": "/subscriptions/{sub-id}/resourceGroups/{rg-name}/providers/Microsoft.Storage/storageAccounts/0storagecfrbqnnmpeudi",
    "blobEndpoint": "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "status": "available"
  },
  {
    "id": "/subscriptions/{sub-id}/resourceGroups/{rg-name}/providers/Microsoft.Storage/storageAccounts/1storagecfrbqnnmpeudi",
    "blobEndpoint": "https://1storagecfrbqnnmpeudi.blob.core.windows.net/",
    "status": "available"
  }
]
```

## <a name="loop-array-and-index"></a>Schleifen-Array und -Index

In diesem Beispiel werden sowohl die Elemente eines Arrays als auch ein Index verwendet.

```bicep
param rgLocation string = resourceGroup().location

var stgNames = [
  'demostg1'
  'demostg2'
  'demostg3'
]

resource stg 'Microsoft.Storage/storageAccounts@2021-02-01' = [for name in stgNames: {
  name: name
  location: rgLocation
  kind: 'Storage'
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
}]

output stgOutput array = [for (name, i) in stgNames: {
  name: stg[i].name
  resourceId: stg[i].id
}]
```

Die Ausgabe gibt ein Array mit den folgenden Elementen zurück:

```json
[
  {
    "name": "demostg1",
    "resourceId": "/subscriptions/{sub-id}/resourceGroups/{rg-name}/providers/Microsoft.Storage/storageAccounts/demostg1"
  },
  {
    "name": "demostg2",
    "resourceId": "/subscriptions/{sub-id}/resourceGroups/{rg-name}/providers/Microsoft.Storage/storageAccounts/demostg2"
  },
  {
    "name": "demostg3",
    "resourceId": "/subscriptions/{sub-id}/resourceGroups/{rg-name}/providers/Microsoft.Storage/storageAccounts/demostg3"
  }
]
```

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu anderen Verwendungsmöglichkeiten von Schleifen finden Sie unter folgenden Links:
  - [Ressourceniteration in Bicep](loop-resources.md)
  - [Moduliteration in Bicep](loop-modules.md)
  - [Eigenschafteniteration in Bicep](loop-properties.md)
  - [Variableniteration in Bicep](loop-variables.md)
- Informationen zum Festlegen von Abhängigkeiten von Ressourcen, die in einer Schleife erstellt werden, finden Sie unter [Festlegen von Ressourcenabhängigkeiten](./resource-declaration.md#set-resource-dependencies).

