---
title: 'Linterregel: Interpolation bevorzugen'
description: 'Linterregel: Interpolation bevorzugen'
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: 648b924b545e801f8a97ef5202f8022252ef94c4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598461"
---
# <a name="linter-rule---prefer-interpolation"></a>Linterregel: Interpolation bevorzugen

Der Linter vereinfacht das Erzwingen von Codierungsstandards, indem er während der Entwicklung Leitfäden bereitstellt. Die aktuellen Linterregeln sind minimal und stammen aus [arm-ttk-Testfällen](../templates/template-test-cases.md):

- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

Weitere Informationen finden Sie unter [Verwenden von Biceps-Linter](./linter.md).

## <a name="code"></a>Code

`prefer-interpolation`

## <a name="description"></a>BESCHREIBUNG

Die Zeichenfolgeninterpolation sollte anstelle der concat-Funktion verwendet werden.

## <a name="examples"></a>Beispiele

Das folgende Beispiel besteht diesen Test nicht, da die concat-Funktion verwendet wird.

```bicep
param suffix string = '001'
var vnetName = concat('vnet-', suffix)

resource vnet 'Microsoft.Network/virtualNetworks@2018-10-01' = {
  name: vnetName
  ...
}
```

Im folgenden Beispiel ist der Test erfolgreich.

```bicep
param suffix string = '001'
var vnetName = 'vnet-${suffix}'

resource vnet 'Microsoft.Network/virtualNetworks@2018-10-01' = {
  name: vnetName
  ...
}
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung von Visual Studio Code und der BICEP-Erweiterung finden Sie unter [Schnellstart: Erstellen von BICEP-Dateien mit Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
