---
title: 'Linterregel: Vereinfachen der Interpolation'
description: 'Linterregel: Vereinfachen der Interpolation'
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: aff3df9bd7b357dcfdfd62f87971580399b3f2ce
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700225"
---
# <a name="linter-rule---simplify-interpolation"></a>Linterregel: Vereinfachen der Interpolation

Der Linter vereinfacht das Erzwingen von Codierungsstandards, indem er während der Entwicklung Leitfäden bereitstellt. Die aktuellen Linterregeln sind minimal und stammen aus [arm-ttk-Testfällen](../templates/template-test-cases.md):

- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

Weitere Informationen finden Sie unter [Verwenden von Biceps-Linter](./linter.md).

## <a name="code"></a>Code

`simplify-interpolation`

## <a name="description"></a>BESCHREIBUNG

Es ist nicht erforderlich, die Interpolation zu verwenden, um auf einen Parameter oder eine Variable zu verweisen.

## <a name="examples"></a>Beispiele

Im folgenden Beispiel ist der Test nicht erfolgreich.

```bicep
param AutomationAccountName string

resource AutomationAccount 'Microsoft.Automation/automationAccounts@2020-01-13-preview' = {
  name: '${AutomationAccountName}'
  ...
}
```

Im folgenden Beispiel ist der Test erfolgreich.

```bicep
param AutomationAccountName string

resource AutomationAccount 'Microsoft.Automation/automationAccounts@2020-01-13-preview' = {
  name: AutomationAccountName
  ...
}
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung von Visual Studio Code und der BICEP-Erweiterung finden Sie unter [Schnellstart: Erstellen von BICEP-Dateien mit Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
