---
title: 'Linterregel: Vereinfachen der Interpolation'
description: 'Linterregel: Vereinfachen der Interpolation'
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: e2e0f9eca607ececad22c025e89b7c976cd0d05a
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130165354"
---
# <a name="linter-rule---simplify-interpolation"></a>Linterregel: Vereinfachen der Interpolation

Diese Regel sucht nach Syntax, die die Zeichenfolgeninterpolation verwendet, wenn sie nicht benötigt wird.

## <a name="returned-code"></a>Zurückgegebener Code

`simplify-interpolation`

## <a name="solution"></a>Lösung

Entfernen Sie alle Verwendungen der Zeichenfolgeninterpolation, die nicht Teil eines Ausdrucks ist, um Werte zu kombinieren.

Im folgenden Beispiel schlägt dieser Test fehl, da er nur auf einen Parameter verweist.

```bicep
param AutomationAccountName string

resource AutomationAccount 'Microsoft.Automation/automationAccounts@2020-01-13-preview' = {
  name: '${AutomationAccountName}'
  ...
}
```

Sie können dies beheben, indem Sie die Syntax der Zeichenfolgeninterpolation entfernen.

```bicep
param AutomationAccountName string

resource AutomationAccount 'Microsoft.Automation/automationAccounts@2020-01-13-preview' = {
  name: AutomationAccountName
  ...
}
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Linter finden Sie unter [Verwenden des Bicep-Linters](./linter.md).
