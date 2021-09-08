---
title: Verwenden des Bicep-Linters
description: Erfahren Sie, wie Sie den Bicep-Linter verwenden.
ms.topic: conceptual
ms.date: 07/01/2021
ms.openlocfilehash: 82ab1462ca55a4329370d185aed626ae631e2f2b
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/13/2021
ms.locfileid: "113649436"
---
# <a name="use-bicep-linter"></a>Verwenden des Bicep-Linters

Der Bicep-Linter kann zum Analysieren von Bicep-Dateien verwendet werden. Er prüft auf Syntaxfehler und fängt einen anpassbaren Satz bewährter Methoden für die Erstellung ab, bevor Sie Ihre Bicep-Dateien erstellen oder bereitstellen. Der Linter vereinfacht das Erzwingen von Codierungsstandards, indem er während der Entwicklung Leitfäden bereitstellt.

## <a name="install-linter"></a>Installieren des Linters

Der Linter kann mit Visual Studio Code und der Bicep-CLI verwendet werden. Voraussetzungen:

- Bicep-CLI Version 0.4 oder höher
- Bicep-Erweiterung für Visual Studio Code Version 0.4 oder höher

## <a name="customize-linter"></a>Anpassen des Linters

Wenn Sie „bicepconfig.json“ verwenden, können Sie den Linter aktivieren oder deaktivieren, regelspezifische Werte angeben und auch die Ebene von Regeln festlegen. Im Folgenden finden Sie die Standarddatei „bicepconfig.json“:

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
- **verbose:** Geben Sie **true** ein, um die Datei „bicepconfig.json“ anzuzeigen, die von Visual Studio Code verwendet wird.
- **rules:** Geben Sie regelspezifische Werte ein. Jede Regel verfügt über mindestens eine Eigenschaft und Ebene. Diese Eigenschaft steuert das Verhalten von Bicep, sofern sie in der Bicep-Datei gefunden wird.

Sie können mehrere Werte für die Regelebene verwenden:

| **level**  | **Verhalten zur Buildzeit** | **Editor-Verhalten** |
|--|--|--|
| `Error` | Verstöße werden in der Buildausgabe an der Befehlszeile als Fehler angezeigt und bewirken einen Fehler des Builds. | Fehlerhafter Code wird mit einer roten Wellenlinie unterstrichen und auf der Registerkarte mit den Problemen angezeigt. |
| `Warning` | Verstöße werden als Warnungen in der Buildausgabe der Befehlszeile angezeigt, bewirken aber nicht, dass Builds zu Fehlern führen. | Fehlerhafter Code wird mit einer gelben Wellenlinie unterstrichen und auf der Registerkarte mit den Problemen angezeigt. |
| `Info` | Verstöße werden in der Buildausgabe der Befehlszeile nicht angezeigt. | Fehlerhafter Code wird mit einer blauen Wellenlinie unterstrichen und auf der Registerkarte mit den Problemen angezeigt. |
| `Off` | Vollständig unterdrückt. | Vollständig unterdrückt. |

Der aktuelle Satz von Linterregeln ist minimal und stammt aus [arm-ttk-Testfällen](../templates/template-test-cases.md). Sowohl die Visual Studio Code-Erweiterung als auch die Bicep-CLI überprüfen standardmäßig alle verfügbaren Regeln, und alle Regeln sind auf die Warnstufe festgelegt. Basierend auf der Ebene einer Regel werden im Editor Fehler, Warnungen oder Informationsmeldungen angezeigt.

- [no-hardcoded-env-urls](https://github.com/Azure/bicep/blob/main/docs/linter-rules/no-hardcoded-env-urls.md)
- [no-unused-params](https://github.com/Azure/bicep/blob/main/docs/linter-rules/no-unused-params.md)
- [no-unused-vars](https://github.com/Azure/bicep/blob/main/docs/linter-rules/no-unused-vars.md)
- [prefer-interpolation](https://github.com/Azure/bicep/blob/main/docs/linter-rules/prefer-interpolation.md)
- [secure-parameter-default](https://github.com/Azure/bicep/blob/main/docs/linter-rules/secure-parameter-default.md)
- [simplify-interpolation](https://github.com/Azure/bicep/blob/main/docs/linter-rules/simplify-interpolation.md)

Die Bicep-Erweiterung von Visual Studio Code bietet IntelliSense beim Bearbeiten von Bicep-Konfigurationsdateien:

:::image type="content" source="./media/linter/bicep-linter-configure-intellisense.png" alt-text="IntelliSense-Unterstützung beim Konfigurieren von „bicepconfig.json“":::

## <a name="use-in-visual-studio-code"></a>Verwenden in Visual Studio Code

Installieren Sie die Bicep-Erweiterung 0.4 oder höher, um den Linter zu verwenden.  Der folgende Screenshot zeigt den Linter in Aktion:

:::image type="content" source="./media/linter/bicep-linter-show-errors.png" alt-text="Verwendung des Bicep-Linters in Visual Studio Code":::

Im Bereich **PROBLEME** im Screenshot gibt es vier Fehler, eine Warnung und eine Informationsmeldung.  Die Informationsmeldung zeigt die verwendete Bicep-Konfigurationsdatei an. Diese Informationen werden nur angezeigt, wenn Sie **verbose** in der Konfigurationsdatei auf **true** festgelegt haben.

Zeigen Sie mit dem Mauszeiger auf einen der Problembereiche. Der Linter zeigt Details zum Fehler oder zur Warnung an. Wenn Sie auf den Bereich klicken, wird auch eine blaue Glühbirne angezeigt:

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
