---
title: Definieren mehrerer Instanzen einer Eigenschaft in Bicep
description: Verwenden Sie eine Bicep-Eigenschaftenschleife, die beim Erstellen einer Ressourceneigenschaft durchlaufen wird.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: 6e9d41136401b28cf330bc828947d35a67c69a43
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225963"
---
# <a name="property-iteration-in-bicep"></a>Eigenschafteniteration in Bicep

In diesem Artikel erfahren Sie, wie Sie in einer Bicep-Datei mehrere Instanzen einer Eigenschaft erstellen. Sie können dem Abschnitt `properties` einer Ressource eine Schleife hinzufügen und die Anzahl der Elemente für eine Eigenschaft dynamisch festlegen. So vermeiden Sie auch das Wiederholen einer Syntax in Ihrer Bicep-Datei.

Sie können eine Schleife nur mit Ressourcen der obersten Ebene verwenden, auch wenn Sie die Schleife auf eine Eigenschaft anwenden. Weitere Informationen zum Ändern einer untergeordneten Ressource in eine Ressource der obersten Ebene finden Sie unter [Iterationen für eine untergeordnete Ressource](loop-resources.md#iteration-for-a-child-resource).

Eine Schleife kann auch mit [Modulen](loop-modules.md), [Ressourcen](loop-resources.md), [Variablen](loop-variables.md) und [Ausgaben](loop-outputs.md) verwendet werden.

## <a name="syntax"></a>Syntax

Mit Schleifen können mehrere Eigenschaften deklariert werden durch:

- Verwendung eines Schleifenindexes

  ```bicep
  <property-name>: [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

- Durchlaufen eines Arrays

  ```bicep
  <property-name>: [for <item> in <collection>: {
    <properties>
  }]
  ```

  Weitere Informationen finden Sie unter [Schleifenarray](#loop-array).

- Durchlaufen eines Arrays und eines Indexes

  ```bicep
  <property-name>: [for (<item>, <index>) in <collection>: {
    <properties>
  }]
  ```

## <a name="loop-limits"></a>Schleifengrenzwerte

Bicep-Dateien dürfen keine Schleifen mit einer Anzahl von unter 0 oder über 800 Iterationen enthalten. 

## <a name="loop-array"></a>Schleifenarray

In diesem Beispiel wird ein Array für die Eigenschaft `subnets` durchlaufen, um in einem virtuellen Netzwerk zwei Subnetze zu erstellen.

```bicep
param rgLocation string = resourceGroup().location

var subnets = [
  {
    name: 'api'
    subnetPrefix: '10.144.0.0/24'
  }
  {
    name: 'worker'
    subnetPrefix: '10.144.1.0/24'
  }
]

resource vnet 'Microsoft.Network/virtualNetworks@2020-07-01' = {
  name: 'vnet'
  location: rgLocation
  properties: {
    addressSpace: {
      addressPrefixes: [
        '10.144.0.0/20'
      ]
    }
    subnets: [for subnet in subnets: {
      name: subnet.name
      properties: {
        addressPrefix: subnet.subnetPrefix
      }
    }]
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu anderen Verwendungsmöglichkeiten von Schleifen finden Sie unter folgenden Links:
  - [Ressourceniteration in Bicep](loop-resources.md)
  - [Moduliteration in Bicep](loop-modules.md)
  - [Variableniteration in Bicep](loop-variables.md)
  - [Ausgabeiteration in Bicep](loop-outputs.md)
- Informationen zum Festlegen von Abhängigkeiten von Ressourcen, die in einer Schleife erstellt werden, finden Sie unter [Festlegen von Ressourcenabhängigkeiten](./resource-declaration.md#set-resource-dependencies).
