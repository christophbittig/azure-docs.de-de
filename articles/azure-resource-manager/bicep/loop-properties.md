---
title: Definieren mehrerer Instanzen einer Eigenschaft in Bicep
description: Verwenden Sie eine Bicep-Eigenschaftenschleife, die beim Erstellen einer Ressourceneigenschaft durchlaufen wird.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 59ca13e4f6ab29a71c543fcbe779ef616beba43b
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026142"
---
# <a name="property-iteration-in-bicep"></a>Eigenschafteniteration in Bicep

In diesem Artikel erfahren Sie, wie Sie in einer Bicep-Datei mehrere Instanzen einer Eigenschaft erstellen. Sie können dem Abschnitt `properties` einer Ressource eine Schleife hinzufügen und die Anzahl der Elemente für eine Eigenschaft während der Bereitstellung dynamisch festlegen. So vermeiden Sie auch das Wiederholen von Syntax in Ihrer Bicep-Datei.

Sie können eine Schleife nur mit Ressourcen der obersten Ebene verwenden, auch wenn Sie die Schleife auf eine Eigenschaft anwenden. Weitere Informationen zum Ändern einer untergeordneten Ressource in eine Ressource der obersten Ebene finden Sie unter [Iterationen für eine untergeordnete Ressource](loop-resources.md#iteration-for-a-child-resource).

Eine Schleife kann auch mit [Ressourcen](loop-resources.md), [Variablen](loop-variables.md) und [Ausgaben](loop-outputs.md) verwendet werden.

## <a name="syntax"></a>Syntax

Mit Schleifen können mehrere Eigenschaften deklariert werden durch:

- Durchlaufen eines Arrays

  ```bicep
  <property-name>: [for <item> in <collection>: {
    <properties>
  }]
  ```

- Durchlaufen der Elemente eines Arrays

  ```bicep
  <property-name>: [for (<item>, <index>) in <collection>: {
    <properties>
  }]
  ```

- Verwendung eines Schleifenindexes

  ```bicep
  <property-name>: [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

## <a name="loop-limits"></a>Schleifengrenzwerte

Bicep-Dateien dürfen keine Schleifen mit einer Anzahl von unter 0 oder über 800 Iterationen enthalten. Installieren Sie zum Bereitstellen von Bicep-Dateien die neueste Version der [Bicep-Tools](install.md).

## <a name="property-iteration"></a>Iteration von Eigenschaften

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
  - [Ressourceniteration in Bicep-Dateien](loop-resources.md)
  - [Variableniteration in Bicep-Dateien](loop-variables.md)
  - [Ausgabeiteration in Bicep-Dateien](loop-outputs.md)
- Weitere Informationen zu den Abschnitten in einer Bicep-Datei finden Sie unter [Verstehen der Struktur und Syntax von Bicep-Dateien](file.md).
- Informationen zum Bereitstellen mehrerer Ressourcen finden Sie unter [Verwenden von Bicep-Modulen](modules.md).
- Informationen zum Festlegen von Abhängigkeiten von Ressourcen, die in einer Schleife erstellt werden, finden Sie unter [Festlegen von Ressourcenabhängigkeiten](./resource-declaration.md#set-resource-dependencies).
- Informationen zum Bereitstellen mit PowerShell finden Sie unter [Bereitstellen von Ressourcen mit Bicep und Azure PowerShell](deploy-powershell.md).
- Informationen zum Bereitstellen mit der Azure CLI finden Sie unter [Bereitstellen von Ressourcen mit Bicep und der Azure CLI](deploy-cli.md).
