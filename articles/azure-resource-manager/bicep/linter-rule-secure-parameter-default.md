---
title: Linter-Regel - sicherer Parameter-Standard
description: Linter-Regel - sicherer Parameter-Standard
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: 4fa82fbbe74d9bf51d1eb498341b999a89e12978
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700226"
---
# <a name="linter-rule---secure-parameter-default"></a>Linter-Regel - sicherer Parameter-Standard

Der Linter vereinfacht das Erzwingen von Codierungsstandards, indem er während der Entwicklung Leitfäden bereitstellt. Der aktuelle Satz von Linter-Regeln ist minimal und stammt aus den [arm-ttk Testfällen](../templates/template-test-cases.md):

- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

Weitere Informationen finden Sie unter [Bicep-Linter verwenden](./linter.md).

## <a name="code"></a>Code

`secure-parameter-default`

## <a name="description"></a>BESCHREIBUNG

Geben Sie keinen fest kodierten Standardwert für einen [sicheren Parameter](./parameters.md#secure-parameters) in Ihrer Vorlage an, es sei denn, er ist leer oder ein Ausdruck, der einen Aufruf von [newGuid()](./bicep-functions-string.md#newguid) enthält.

Sie verwenden den @secure()-Decorator für Parameter, die sensible Werte wie Passwörter enthalten. Wenn ein Parameter einen sicheren Decorator verwendet, wird der Wert des Parameters nicht protokolliert oder in der Einsatzhistorie gespeichert. Dadurch wird verhindert, dass der sensible Wert von einem böswilligen Benutzer entdeckt wird.

Wenn Sie jedoch einen Standardwert für einen gesicherten Parameter angeben, kann dieser Wert von jedem ermittelt werden, der auf die Vorlage oder den Bereitstellungsverlauf zugreifen kann.

## <a name="examples"></a>Beispiele

Das folgende Beispiel besteht diesen Test nicht:

```bicep
@secure()
param adminPassword string = 'HardcodedPassword'
```

Die folgenden Beispiele bestehen diesen Test:

```bicep
@secure()
param adminPassword string
```

```bicep
@secure()
param adminPassword string = ''
```

```bicep
@secure()
param adminPassword string = newGuid()
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung von Visual Studio Code und der BICEP-Erweiterung finden Sie unter [Schnellstart: Erstellen von BICEP-Dateien mit Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
