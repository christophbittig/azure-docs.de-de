---
title: Definieren mehrerer Instanzen einer Variablen in Bicep
description: Verwendung einer Bicep-Variablenschleife zum Iterieren beim Erstellen einer Variablen
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 429a15c222e47bab29b314b0d11f7e077281b635
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/21/2021
ms.locfileid: "122635002"
---
# <a name="variable-iteration-in-bicep"></a>Variableniteration in Bicep

Dieser Artikel erklärt, wie Sie mehr als einen Wert für eine Variable in Ihrer Bicep-Datei erstellen. Sie können dem Abschnitt `variables` eine Schleife hinzufügen und die Anzahl der Elemente für eine Variable während der Bereitstellung dynamisch festlegen. So vermeiden Sie auch das Wiederholen von Syntax in Ihrer Bicep-Datei.

Sie können „copy“ auch mit [Ressourcen](loop-resources.md), [Eigenschaften in einer Ressource](loop-properties.md) und [Ausgaben](loop-outputs.md) verwenden.

## <a name="syntax"></a>Syntax

Mit Schleifen können mehrere Variablen folgendermaßen deklariert werden:

- Durchlaufen eines Arrays

  ```bicep
  var <variable-name> = [for <item> in <collection>: {
    <properties>
  }]

  ```

- Durchlaufen der Elemente eines Arrays

  ```bicep
  var <variable-name> = [for <item>, <index> in <collection>: {
    <properties>
  }]
  ```

- Verwendung eines Schleifenindexes

  ```bicep
  var <variable-name> = [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

## <a name="loop-limits"></a>Schleifengrenzwerte

Bicep-Dateien dürfen keine Schleifen mit einer Anzahl von unter 0 oder über 800 Iterationen enthalten. Installieren Sie zum Bereitstellen von Bicep-Dateien die neueste Version der [Bicep-Tools](install.md).

## <a name="variable-iteration"></a>Variableniteration

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

## <a name="example-templates"></a>Beispielvorlagen

Die folgenden Beispiele zeigen allgemeine Szenarien für das Erstellen mehrerer Werte für eine Variable.

|Vorlage  |BESCHREIBUNG  |
|---------|---------|
|[Schleifenvariablen](https://github.com/Azure/azure-docs-bicep-samples/blob/main/bicep/multiple-instance/loopvariables.bicep) | Veranschaulicht das Iterieren von Variablen. |
|[Mehrere Sicherheitsregeln](https://github.com/Azure/azure-docs-bicep-samples/blob/main/bicep/multiple-instance/multiplesecurityrules.bicep) |Stellt mehrere Sicherheitsregeln in einer Netzwerksicherheitsgruppe bereit. Die Sicherheitsregeln werden aus einem Parameter generiert. Informationen zum Parameter finden Sie im Artikel zur [Datei mit mehreren NSG-Parametern](https://github.com/Azure/azure-docs-bicep-samples/blob/main/bicep/multiple-instance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu anderen Verwendungsmöglichkeiten von Schleifen finden Sie unter folgenden Links:
  - [Ressourceniteration in Bicep-Dateien](loop-resources.md)
  - [Eigenschafteniteration in Bicep-Dateien](loop-properties.md)
  - [Ausgabeiteration in Bicep-Dateien](loop-outputs.md)
- Weitere Informationen zu den Abschnitten in einer Bicep-Datei finden Sie unter [Verstehen der Struktur und Syntax von Bicep-Dateien](file.md).
- Informationen zum Bereitstellen mehrerer Ressourcen finden Sie unter [Verwenden von Bicep-Modulen](modules.md).
- Informationen zum Festlegen von Abhängigkeiten von Ressourcen, die in einer Schleife erstellt werden, finden Sie unter [Festlegen von Ressourcenabhängigkeiten](./resource-declaration.md#set-resource-dependencies).
- Informationen zum Bereitstellen mit PowerShell finden Sie unter [Bereitstellen von Ressourcen mit Bicep und Azure PowerShell](deploy-powershell.md).
- Informationen zum Bereitstellen mit der Azure CLI finden Sie unter [Bereitstellen von Ressourcen mit Bicep und der Azure CLI](deploy-cli.md).
