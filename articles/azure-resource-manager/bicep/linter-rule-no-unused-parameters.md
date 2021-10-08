---
title: Linterregel – keine nicht verwendeten Parameter
description: Linterregel – keine nicht verwendeten Parameter
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: e8304948c2b373d16a7fa48c38ac4bac9ec806df
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700361"
---
# <a name="linter-rule---no-unused-parameters"></a>Linterregel – keine nicht verwendeten Parameter

Der Linter vereinfacht das Erzwingen von Codierungsstandards, indem er während der Entwicklung Leitfäden bereitstellt. Die aktuellen Linterregeln sind minimal und stammen aus [arm-ttk-Testfällen](../templates/template-test-cases.md):

- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

Weitere Informationen finden Sie unter [Verwenden von Biceps-Linter](./linter.md).

## <a name="code"></a>Code

`no-unused-params`

## <a name="description"></a>BESCHREIBUNG

Löschen Sie alle Parameter, die definiert sind, aber nicht verwendet werden, um Ihre Vorlage übersichtlicher zu machen. Dieser Test ermittelt alle Parameter, die nicht in der Vorlage verwendet werden. Das Löschen nicht verwendeter Parameter vereinfacht zudem die Bereitstellung Ihrer Vorlage, da Sie keine unnötigen Werte angeben müssen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung von Visual Studio Code und der BICEP-Erweiterung finden Sie unter [Schnellstart: Erstellen von BICEP-Dateien mit Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
