---
title: 'Bicep-Funktionen: logisch'
description: Hier werden die Funktionen beschrieben, die in einer Bicep-Datei zum Bestimmen von logischen Werten verwendet werden können.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/30/2021
ms.openlocfilehash: 4ab5f0382d320e31c650dcb6ebee68f39d6e43b1
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129362990"
---
# <a name="logical-functions-for-bicep"></a>Logische Funktionen für Bicep

Bicep stellt die `bool`-Funktion zum Konvertieren von Werten in einen booleschen Wert bereit.

Die meisten Logikfunktionen in Resource Manager-Vorlagen werden in Bicep durch [logische Operatoren](./operators-logical.md) ersetzt.

## <a name="bool"></a>bool

`bool(arg1)`

Konvertiert den Parameter in einen booleschen Wert.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Zeichenfolge oder ganze Zahl |Der Wert, der in einen booleschen Wert konvertiert werden soll. |

### <a name="return-value"></a>Rückgabewert

Ein boolescher Wert des konvertierten Werts.

### <a name="examples"></a>Beispiele

Im folgenden Beispiel wird gezeigt, wie „bool“ mit einer Zeichenfolge oder ganzen Zahl verwendet wird.

```bicep
output trueString bool = bool('true')
output falseString bool = bool('false')
output trueInt bool = bool(1)
output falseInt bool = bool(0)
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| trueString | Bool | True |
| falseString | Bool | False |
| trueInt | Bool | True |
| falseInt | Bool | False |

## <a name="next-steps"></a>Nächste Schritte

* Weitere Aktionen im Zusammenhang mit logischen Werten finden Sie unter [Logische Operatoren](./operators-logical.md).
