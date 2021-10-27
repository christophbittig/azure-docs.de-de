---
title: Linter-Regel - sicherer Parameter-Standard
description: Linter-Regel - sicherer Parameter-Standard
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: 139b19124f22d5cb42be71d6a6042830ad7809f7
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130166496"
---
# <a name="linter-rule---secure-parameter-default"></a>Linter-Regel - sicherer Parameter-Standard

Diese Regel sucht nach fest codierten Standardwerten für sichere Parameter.

## <a name="returned-code"></a>Zurückgegebener Code

`secure-parameter-default`

## <a name="solution"></a>Lösung

Geben Sie keinen fest kodierten Standardwert für einen [sicheren Parameter](./parameters.md#secure-parameters) in Ihrer Bicep-Datei an, es sei denn, er ist eine leere Zeichenfolge oder ein Ausdruck, der die Funktion [newGuid()](./bicep-functions-string.md#newguid) aufruft.

Sie verwenden den `@secure()`-Decorator für Parameter, die sensible Werte wie Passwörter enthalten. Wenn ein Parameter einen sicheren Decorator verwendet, wird der Wert des Parameters nicht protokolliert oder in der Einsatzhistorie gespeichert. Dadurch wird verhindert, dass der sensible Wert von einem böswilligen Benutzer entdeckt wird.

Wenn Sie jedoch einen Standardwert für einen gesicherten Parameter angeben, kann dieser Wert von jedem ermittelt werden, der auf die Vorlage oder den Bereitstellungsverlauf zugreifen kann.

Im folgenden Beispiel schlägt dieser Test fehl, da der Parameter über einen fest codierten Standardwert verfügt.

```bicep
@secure()
param adminPassword string = 'HardcodedPassword'
```

Sie können dies beheben, indem Sie den Standardwert entfernen.

```bicep
@secure()
param adminPassword string
```

Eine andere Möglichkeit ist, eine leere Zeichenfolge für den Standardwert bereitzustellen.

```bicep
@secure()
param adminPassword string = ''
```

Alternativ können Sie `newGuid()` verwenden, um den Standardwert zu generieren.

```bicep
@secure()
param adminPassword string = newGuid()
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Linter finden Sie unter [Bicep-Linter verwenden](./linter.md).
