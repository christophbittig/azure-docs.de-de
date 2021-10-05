---
title: Definieren mehrerer Instanzen einer Variablen in Bicep
description: Verwendung einer Bicep-Variablenschleife zum Iterieren beim Erstellen einer Variablen
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: 14721057379217ae9d14b97c94483435dde08203
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124831992"
---
# <a name="variable-iteration-in-bicep"></a>Variableniteration in Bicep

Dieser Artikel erklärt, wie Sie mehr als einen Wert für eine Variable in Ihrer Bicep-Datei erstellen. Sie können der Deklaration `variables` eine Schleife hinzufügen und die Anzahl der Elemente für eine Variable dynamisch festlegen. So vermeiden Sie auch das Wiederholen einer Syntax in Ihrer Bicep-Datei.

Sie können „copy“ auch mit [Modulen](loop-modules.md), [Ressourcen](loop-resources.md), [Eigenschaften in einer Ressource](loop-properties.md) und [Ausgaben](loop-outputs.md) verwenden.

### <a name="microsoft-learn"></a>Microsoft Learn

Weitere Informationen und praktische Anleitungen zu Schleifen finden Sie in **Microsoft Learn** unter [Erstellen flexibler Bicep-Vorlagen mithilfe von Bedingungen und Schleifen](/learn/modules/build-flexible-bicep-templates-conditions-loops/).

## <a name="syntax"></a>Syntax

Mit Schleifen können mehrere Variablen folgendermaßen deklariert werden:

- Verwendung eines Schleifenindexes

  ```bicep
  var <variable-name> = [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

  Weitere Informationen finden Sie unter [Schleifenindex](#loop-index).

- Durchlaufen eines Arrays

  ```bicep
  var <variable-name> = [for <item> in <collection>: {
    <properties>
  }]

  ```

  Weitere Informationen finden Sie unter [Schleifenarray](#loop-array).

- Durchlaufen eines Arrays und eines Indexes

  ```bicep
  var <variable-name> = [for <item>, <index> in <collection>: {
    <properties>
  }]
  ```

## <a name="loop-limits"></a>Schleifengrenzwerte

Bicep-Dateien dürfen keine Schleifen mit einer Anzahl von unter 0 oder über 800 Iterationen enthalten. 

## <a name="loop-index"></a>Schleifenindex

Im folgenden Beispiel wird veranschaulicht, wie Sie ein Array mit Zeichenfolgenwerten erstellen:

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

Das nächste Beispiel zeigt, wie Sie ein Array von Objekten mit den drei Eigenschaften `name`, `diskSizeGB` und `diskIndex` erstellen.

```bicep
param itemCount int = 5

var objectArray = [for i in range(0, itemCount): {
  name: 'myDataDisk${(i + 1)}'
  diskSizeGB: '1'
  diskIndex: i
}]

output arrayResult array = objectArray
```

Die Ausgabe gibt ein Array mit den folgenden Werten zurück:

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
  },
  {
    "name": "myDataDisk4",
    "diskSizeGB": "1",
    "diskIndex": 3
  },
  {
    "name": "myDataDisk5",
    "diskSizeGB": "1",
    "diskIndex": 4
  }
]
```

## <a name="loop-array"></a>Schleifenarray

Im folgenden Beispiel wird eine Schleife für ein Array ausgeführt, das als Parameter übergeben wird. Die Variable erstellt mithilfe des Parameters Objekte im erforderlichen Format.

```bicep
@description('An array that contains objects with properties for the security rules.')
param securityRules array = [
  {
    name: 'RDPAllow'
    description: 'allow RDP connections'
    direction: 'Inbound'
    priority: 100
    sourceAddressPrefix: '*'
    destinationAddressPrefix: '10.0.0.0/24'
    sourcePortRange: '*'
    destinationPortRange: '3389'
    access: 'Allow'
    protocol: 'Tcp'
  }
  {
    name: 'HTTPAllow'
    description: 'allow HTTP connections'
    direction: 'Inbound'
    priority: 200
    sourceAddressPrefix: '*'
    destinationAddressPrefix: '10.0.1.0/24'
    sourcePortRange: '*'
    destinationPortRange: '80'
    access: 'Allow'
    protocol: 'Tcp'
  }
]


var securityRulesVar = [for rule in securityRules: {
  name: rule.name
  properties: {
    description: rule.description
    priority: rule.priority
    protocol: rule.protocol
    sourcePortRange: rule.sourcePortRange
    destinationPortRange: rule.destinationPortRange
    sourceAddressPrefix: rule.sourceAddressPrefix
    destinationAddressPrefix: rule.destinationAddressPrefix
    access: rule.access
    direction: rule.direction
  }
}]

resource netSG 'Microsoft.Network/networkSecurityGroups@2020-11-01' = {
  name: 'NSG1'
  location: resourceGroup().location
  properties: {
    securityRules: securityRulesVar
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu anderen Verwendungsmöglichkeiten von Schleifen finden Sie unter folgenden Links:
  - [Ressourceniteration in Bicep](loop-resources.md)
  - [Moduliteration in Bicep](loop-modules.md)
  - [Eigenschafteniteration in Bicep](loop-properties.md)
  - [Ausgabeiteration in Bicep](loop-outputs.md)
- Informationen zum Festlegen von Abhängigkeiten von Ressourcen, die in einer Schleife erstellt werden, finden Sie unter [Festlegen von Ressourcenabhängigkeiten](./resource-declaration.md#set-resource-dependencies).
