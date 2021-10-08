---
title: Linterregel – keine nicht verwendeten Variablen
description: Linterregel – keine nicht verwendeten Variablen
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: 80ace33219024f19792ee909c092e498b645665e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598435"
---
# <a name="linter-rule---no-unused-variables"></a>Linterregel – keine nicht verwendeten Variablen

Der Linter vereinfacht das Erzwingen von Codierungsstandards, indem er während der Entwicklung Leitfäden bereitstellt. Der aktuelle Satz von Linterregeln ist minimal und stammt aus [arm-ttk-Testfällen](../templates/template-test-cases.md):

- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

Weitere Informationen finden Sie unter [Verwenden des Bicep-Linters](./linter.md).

## <a name="code"></a>Code

`no-unused-vars`

## <a name="description"></a>BESCHREIBUNG

Löschen Sie alle Variablen, die definiert sind, aber nicht verwendet werden, um Ihre Vorlage übersichtlicher zu machen. Dieser Test ermittelt alle Variablen, die nicht in der Vorlage verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung von Visual Studio Code und der BICEP-Erweiterung finden Sie unter [Schnellstart: Erstellen von BICEP-Dateien mit Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
