---
title: Verwenden des Bicep-Linters
description: Erfahren Sie, wie Sie den Bicep-Linter verwenden.
ms.topic: conceptual
ms.date: 09/10/2021
ms.openlocfilehash: cef701d9a9f64990c0afbe265c3355f9c1a850ce
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631170"
---
# <a name="use-bicep-linter"></a>Verwenden des Bicep-Linters

Der Bicep-Linter kann zum Analysieren von Bicep-Dateien verwendet werden. Es aktiviert die Suche nach Syntaxfehlern und Verstößen gegen bewährte Verfahren, bevor Sie Ihre Bicep-Datei erstellen oder bereitstellen. Sie können die Dokumenterstellung zu Best Practices zum überprüfen der Datei anpassen. Der Linter vereinfacht das Erzwingen von Codierungsstandards, indem er während der Entwicklung Leitfäden bereitstellt.

## <a name="linter-requirements"></a>Linter-Anforderungen

Der Linter ist in die Bicep-CLI und die VS-Code-Erweiterung integriert. Um ihn zu verwenden, benötigen Sie die Version 0.4 oder höher.

## <a name="customize-linter"></a>Anpassen des Linters

Wenn Sie „bicepconfig.json“ verwenden, können Sie den Linter aktivieren oder deaktivieren, regelspezifische Werte angeben und auch die Ebene von Regeln festlegen. Das folgende Beispiel zeigt die standardmäßige bicepconfig.json:

```json
{
  "analyzers": {
    "core": {
      "verbose": false,
      "enabled": true,
      "rules": {
        "no-hardcoded-env-urls": {
          "level": "warning",
          "disallowedhosts": [
            "management.core.windows.net",
            "gallery.azure.com",
            "management.core.windows.net",
            "management.azure.com",
            "database.windows.net",
            "core.windows.net",
            "login.microsoftonline.com",
            "graph.windows.net",
            "trafficmanager.net",
            "vault.azure.net",
            "datalake.azure.net",
            "azuredatalakestore.net",
            "azuredatalakeanalytics.net",
            "vault.azure.net",
            "api.loganalytics.io",
            "api.loganalytics.iov1",
            "asazure.windows.net",
            "region.asazure.windows.net",
            "api.loganalytics.iov1",
            "api.loganalytics.io",
            "asazure.windows.net",
            "region.asazure.windows.net",
            "batch.core.windows.net"
          ],
          "excludedhosts": [
            "schema.management.azure.com"
          ]
        }
      }
    }
  }
}
```

Sie können eine benutzerdefinierte Datei „bicepconfig.json“ zusammen mit Ihren Vorlagen im selben Verzeichnis platzieren. In diesem Fall wird die in der Ordnerstruktur nächstgelegene Konfigurationsdatei verwendet.

Der folgende JSON-Code ist ein Beispiel für „bicepconfig.json“:

```json
{
  "analyzers": {
    "core": {
      "enabled": true,
      "verbose": true,
      "rules": {
        "no-hardcoded-env-urls": {
          "level": "warning"
        },
        "no-unused-params": {
          "level": "error"
        },
        "no-unused-vars": {
          "level": "error"
        },
        "prefer-interpolation": {
          "level": "warning"
        },
        "secure-parameter-default": {
          "level": "error"
        },
        "simplify-interpolation": {
          "level": "warning"
        }
      }
    }
  }
}
```

- **enabled:** Geben Sie **true** ein, um den Linter zu aktivieren, und **false**, um den Linter zu deaktivieren.
- **Hverbose**: Geben Sie **true** ein, um die von Visual Studio Code verwendete bicepconfig.json-Datei anzuzeigen.
- **rules:** Geben Sie regelspezifische Werte ein. Jede Regel verfügt über mindestens eine Eigenschaft und Ebene. Diese Eigenschaft steuert das Verhalten von Bicep, sofern sie in der Bicep-Datei gefunden wird.

Sie können mehrere Werte für die Regelebene verwenden:

| **level**  | **Verhalten zur Buildzeit** | **Editor-Verhalten** |
|--|--|--|
| `Error` | Verstöße werden in der Buildausgabe an der Befehlszeile als Fehler angezeigt und bewirken einen Fehler des Builds. | Fehlerhafter Code wird mit einer roten Wellenlinie unterstrichen und auf der Registerkarte mit den Problemen angezeigt. |
| `Warning` | Verstöße werden als Warnungen in der Buildausgabe der Befehlszeile angezeigt, bewirken aber nicht, dass Builds zu Fehlern führen. | Fehlerhafter Code wird mit einer gelben Wellenlinie unterstrichen und auf der Registerkarte mit den Problemen angezeigt. |
| `Info` | Verstöße erscheinen in der Befehlszeilenausgabe des Builds nicht. | Fehlerhafter Code wird mit einer blauen Wellenlinie unterstrichen und auf der Registerkarte mit den Problemen angezeigt. |
| `Off` | Vollständig unterdrückt. | Vollständig unterdrückt. |

Der aktuelle Satz von Linterregeln ist minimal und stammt aus [arm-ttk-Testfällen](../templates/template-test-cases.md). Sowohl die Visual Studio Code-Erweiterung als auch die Bicep-CLI überprüfen standardmäßig alle verfügbaren Regeln, und alle Regeln sind auf die Warnstufe festgelegt. Basierend auf der Ebene einer Regel werden im Editor Fehler, Warnungen oder Informationsmeldungen angezeigt.

- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

Die Bicep-Erweiterung von Visual Studio Code bietet IntelliSense beim Bearbeiten von Bicep-Konfigurationsdateien:

:::image type="content" source="./media/linter/bicep-linter-configure-intellisense.png" alt-text="IntelliSense-Unterstützung beim Konfigurieren von „bicepconfig.json“":::

## <a name="use-in-visual-studio-code"></a>Verwenden in Visual Studio Code

Installieren Sie die Bicep-Erweiterung 0.4 oder höher, um den Linter zu verwenden.  Der folgende Screenshot zeigt den Linter in Aktion:

:::image type="content" source="./media/linter/bicep-linter-show-errors.png" alt-text="Verwendung des Bicep-Linters in Visual Studio Code":::

Im Bereich **PROBLEME** im Screenshot gibt es vier Fehler, eine Warnung und eine Informationsmeldung.  Die Informationsmeldung zeigt die verwendete Bicep-Konfigurationsdatei an. Diese Informationen werden nur angezeigt, wenn Sie **verbose** in der Konfigurationsdatei auf **true** festgelegt haben.

Zeigen Sie mit dem Mauszeiger auf einen der Problembereiche. Der Linter zeigt Details zum Fehler oder zur Warnung an. Wählen Sie den Bereich aus, es wird auch eine blaue Glühbirne angezeigt:

:::image type="content" source="./media/linter/bicep-linter-show-quickfix.png" alt-text="Verwendung des Bicep-Linters in Visual Studio Code: schnelle Problembehebung":::

Wählen Sie die Glühbirne oder den Link **Schnelle Problembehebung** aus, um die Lösung anzuzeigen:

:::image type="content" source="./media/linter/bicep-linter-show-quickfix-solution.png" alt-text="Verwendung des Bicep-Linters in Visual Studio Code: schnelle Problembehebung anzeigen":::

Wählen Sie die Lösung aus, um das Problem automatisch beheben zu lassen.

## <a name="use-in-bicep-cli"></a>Verwenden der Bicep-CLI

Installieren Sie die Bicep-CLI 0.4 oder höher, um den Linter zu verwenden.  Der folgende Screenshot zeigt den Linter in Aktion. Die Bicep-Datei ist dieselbe wie in [Verwenden in Visual Studio Code](#use-in-visual-studio-code).

:::image type="content" source="./media/linter/bicep-linter-command-line.png" alt-text="Verwendung des Bicep-Linters an der Befehlszeile":::

Sie können diese Überprüfungen in Ihre CI/CD-Pipelines integrieren. Außerdem können Sie eine GitHub-Aktion verwenden, um einen Bicep-Build zu versuchen. Fehler führen zu einem Abbruch der Pipelines.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung von Visual Studio Code und der BICEP-Erweiterung finden Sie unter [Schnellstart: Erstellen von BICEP-Dateien mit Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
