---
title: 'Bicep-Funktionen: logisch'
description: Hier werden die Funktionen beschrieben, die in einer Bicep-Datei zum Bestimmen von logischen Werten verwendet werden können.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/10/2021
ms.openlocfilehash: e3a811102c31301386135042c79c205eeb9f7f9a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124758425"
---
# <a name="logical-functions-for-bicep"></a>Logische Funktionen für Bicep

Bicep stellt die `bool`-Funktion zum Konvertieren von Werten in einen booleschen Wert bereit.

Die meisten Logikfunktionen in Resource Manager-Vorlagen werden in Bicep durch [logische Operatoren](./operators-logical.md) ersetzt.

## <a name="bool"></a>bool

`bool(arg1)`

Konvertiert den Parameter in einen booleschen Wert.

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

| Name | Type | Wert |
| ---- | ---- | ----- |
| trueString | Bool | True |
| falseString | Bool | False |
| trueInt | Bool | True |
| falseInt | Bool | False |

## <a name="next-steps"></a>Nächste Schritte

* Weitere Aktionen im Zusammenhang mit logischen Werten finden Sie unter [Logische Operatoren](./operators-logical.md).
