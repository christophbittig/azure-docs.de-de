---
title: Linter-Einstellungen für Bizeps-Konfiguration
description: Beschreibt, wie Sie die Konfigurationswerte für den Bicep-Linter anpassen können.
ms.topic: conceptual
ms.date: 11/16/2021
ms.openlocfilehash: d48ea625d58b35ac18c39bfb3fb60eb4ec3d7edd
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557306"
---
# <a name="add-linter-settings-in-the-bicep-config-file"></a>Hinzufügen von Linter-Einstellungen in der Bicep-Konfigurationsdatei

In einer **bicepconfig.json**-Datei können Sie die Validierungseinstellungen für den [Bicep-Linter](linter.md) anpassen. Der Linter verwendet diese Einstellungen, wenn er Ihre Bicep-Dateien nach den besten Verfahren auswertet.

Dieser Artikel beschreibt die Einstellungen, die für die Arbeit mit dem Bicep-Linter zur Verfügung stehen.

## <a name="customize-linter"></a>Anpassen des Linters

Die Linter-Einstellungen sind unter dem Element `analyzers` verfügbar. Sie können den Linter aktivieren oder deaktivieren, regelspezifische Werte angeben und auch die Ebene von Regeln festlegen.

Das folgende Beispiel zeigt die Regeln, die für die Konfiguration verfügbar sind.

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

Dabei handelt es sich um die folgenden Eigenschaften:

- **enabled**: Geben Sie **true** an, um den Linter zu aktivieren, und **false**, um den Linter zu deaktivieren.
- **verbose**: Geben Sie **true** an, um die von Visual Studio Code verwendete „bicepconfig.json“-Datei anzuzeigen.
- **rules**: Geben Sie regelspezifische Werte an. Jede Regel verfügt über eine Ebene, die bestimmt, wie der Linter reagiert, wenn ein Verstoß gefunden wird.

Die verfügbaren Werte für **level** (Eben) lauten:

| **level**  | **Verhalten zur Buildzeit** | **Editor-Verhalten** |
|--|--|--|
| `Error` | Verstöße werden in der Buildausgabe der Befehlszeile als Fehler angezeigt und bewirken ein Fehlschlagen des Buildvorgangs. | Fehlerhafter Code wird mit einer roten Wellenlinie unterstrichen und auf der Registerkarte mit den Problemen angezeigt. |
| `Warning` | Verstöße werden in der Buildausgabe der Befehlszeile als Warnungen angezeigt, führen aber nicht zum Fehlschlagen des Buildvorgangs. | Fehlerhafter Code wird mit einer gelben Wellenlinie unterstrichen und auf der Registerkarte mit den Problemen angezeigt. |
| `Info` | Verstöße erscheinen in der Befehlszeilenausgabe des Builds nicht. | Fehlerhafter Code wird mit einer blauen Wellenlinie unterstrichen und auf der Registerkarte mit den Problemen angezeigt. |
| `Off` | Vollständig unterdrückt. | Vollständig unterdrückt. |

## <a name="environment-urls"></a>Environment URLs

Für die Regel zu hartcodierten Umgebungs-URLs können Sie anpassen, welche URLs aktiviert sind. Standardmäßig werden die folgenden Einstellungen angewendet:

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

## <a name="next-steps"></a>Nächste Schritte

* [Konfigurieren Sie Ihre Bicep-Umgebung](bicep-config.md)
* [Modul-Einstellungen in der Bicep-Konfiguration hinzufügen](bicep-config-modules.md)
* Erfahren Sie mehr über den [Bizeps-Linter](linter.md)
