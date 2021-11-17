---
title: Verwenden des Bicep-Linters
description: Erfahren Sie, wie Sie den Bicep-Linter verwenden.
ms.topic: conceptual
ms.date: 10/12/2021
ms.openlocfilehash: 5edf5fbb9553233455e7b24dd482a18d3c7e1c42
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548100"
---
# <a name="use-bicep-linter"></a>Verwenden des Bicep-Linters

Der Bicep-Linter überprüft Bicep-Dateien auf Syntaxfehler und Verstöße gegen bewährte Methoden. Der Linter hilft beim Erzwingen von Codierungsstandards, indem er während der Entwicklung Leitfäden bereitstellt. Sie können die bewährten Methoden, die zum Überprüfen der Datei verwendet werden sollen, anpassen.

## <a name="linter-requirements"></a>Linter-Anforderungen

Der Linter ist in die Bicep CLI und die Bicep-Erweiterung für Visual Studio Code integriert. Um ihn zu verwenden, benötigen Sie die Version **0.4 oder höher**.

## <a name="default-rules"></a>Standardregeln

Der Standardsatz von Linterregeln ist minimal und stammt aus den [„arm-ttk“-Testfällen](../templates/template-test-cases.md). Die Erweiterung und die Bicep CLI überprüfen die folgenden Regeln, die auf die Warnstufe festgelegt sind.

- [adminusername-should-not-be-literal](./linter-rule-admin-username-should-not-be-literal.md)
- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

Sie können anpassen, wie die Linterregeln angewendet werden. Um die Standardeinstellungen zu überschreiben, fügen Sie eine **bicepconfig.json**-Datei hinzu, und wenden Sie benutzerdefinierte Einstellungen an. Weitere Informationen zum Anwenden dieser Einstellungen finden Sie unter [Hinzufügen benutzerdefinierter Einstellungen in der Bicep-Konfigurationsdatei](bicep-config-linter.md).

## <a name="use-in-visual-studio-code"></a>Verwenden in Visual Studio Code

Der folgende Screenshot zeigt den Linter in Visual Studio Code:

:::image type="content" source="./media/linter/bicep-linter-show-errors.png" alt-text="Verwendung des Bicep-Linters in Visual Studio Code":::

Im Bereich **PROBLEME** im Screenshot gibt es vier Fehler, eine Warnung und eine Informationsmeldung.  Die Informationsmeldung zeigt die verwendete Bicep-Konfigurationsdatei an. Diese Informationen werden nur angezeigt, wenn Sie **verbose** in der Konfigurationsdatei auf **true** festgelegt haben.

Zeigen Sie mit dem Mauszeiger auf einen der Problembereiche. Der Linter zeigt Details zum Fehler oder zur Warnung an. Wählen Sie den Bereich aus, es wird auch eine blaue Glühbirne angezeigt:

:::image type="content" source="./media/linter/bicep-linter-show-quickfix.png" alt-text="Verwendung des Bicep-Linters in Visual Studio Code: schnelle Problembehebung":::

Wählen Sie die Glühbirne oder den Link **Schnelle Problembehebung** aus, um die Lösung anzuzeigen:

:::image type="content" source="./media/linter/bicep-linter-show-quickfix-solution.png" alt-text="Verwendung des Bicep-Linters in Visual Studio Code: schnelle Problembehebung anzeigen":::

Wählen Sie die Lösung aus, um das Problem automatisch beheben zu lassen.

## <a name="use-in-bicep-cli"></a>Verwenden der Bicep-CLI

Der folgende Screenshot zeigt den Linter in der Befehlszeile. Die Ausgabe des Buildbefehls zeigt alle Regelverletzungen an.

:::image type="content" source="./media/linter/bicep-linter-command-line.png" alt-text="Verwendung des Bicep-Linters an der Befehlszeile":::

Sie können diese Überprüfungen in Ihre CI/CD-Pipelines integrieren. Außerdem können Sie eine GitHub-Aktion verwenden, um einen Bicep-Build zu versuchen. Fehler führen zu einem Abbruch der Pipelines.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Anpassen der Linterregeln finden Sie unter [Hinzufügen benutzerdefinierter Einstellungen in der Bicep-Konfigurationsdatei](bicep-config-linter.md).
* Weitere Informationen zur Verwendung von Visual Studio Code und der BICEP-Erweiterung finden Sie unter [Schnellstart: Erstellen von BICEP-Dateien mit Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
