---
title: Bicep-Konfigurationsdatei
description: Beschreibt, wie Konfigurationswerte für Ihre Bicep-Bereitstellungen angepasst werden.
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 7a057d353fd5b25ae122e7856f1ccb560d7fce56
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130071832"
---
# <a name="add-custom-settings-in-the-bicep-config-file"></a>Hinzufügen benutzerdefinierter Einstellungen in der Bicep-Konfigurationsdatei

Um Konfigurationswerte für Ihre Bicep-Bereitstellungen anzupassen, fügen Sie dem Verzeichnis, in dem Sie Bicep-Dateien speichern, eine Datei namens **bicepconfig.json** hinzu. Geben Sie in dieser Datei die zu verwendenden Konfigurationswerte an.

Sie können mehrere „bicepconfig.json“-Dateien hinzufügen. Die nächstgelegene Konfigurationsdatei in der Verzeichnishierarchie wird verwendet.

In diesem Artikel werden die Eigenschaften beschrieben, die in der Konfigurationsdatei verfügbar sind. Sie können diese Eigenschaften jedoch auch über IntelliSense ermitteln, das von der Bicep-Erweiterung für Visual Studio Code bereitgestellt wird.

:::image type="content" source="./media/bicep-config/bicep-linter-configure-intellisense.png" alt-text="IntelliSense-Unterstützung beim Konfigurieren von „bicepconfig.json“":::

## <a name="aliases-for-modules"></a>Aliase für Module

Um den Pfad zum Verknüpfen mit Modulen zu vereinfachen, können Sie Aliase in der Konfigurationsdatei erstellen. Ein Alias kann auf eine Modulregistrierung oder eine Ressourcengruppe verweisen, die Vorlagenspezifikationen enthält. Die Konfigurationsdatei verfügt über eine Eigenschaft für `moduleAliases`. Um einen Alias für eine Bicep-Registrierung zu erstellen, fügen Sie unter der Eigenschaft `moduleAliases` eine Eigenschaft `br` hinzu. Um einen Alias für eine Vorlagenspezifikation hinzuzufügen, verwenden Sie die Eigenschaft `ts`.

```json
{
  "moduleAliases": {
    "br": {
      <add-registry-aliases>
    },
    "ts": {
      <add-template-specs-aliases>
    }
  }
}
```

Fügen Sie innerhalb der Eigenschaft `br` so viele Aliase hinzu, wie benötigt. Geben Sie für jeden Alias einen Namen und die folgenden Eigenschaften ein:

- **registry** (erforderlich): Name des Registrierungsanmeldeservers.
- **modulePath** (optional): Registrierungsrepository, in dem die Module gespeichert werden.

Fügen Sie innerhalb der Eigenschaft `ts` so viele Aliase hinzu, wie benötigt. Geben Sie für jeden Alias einen Namen und die folgenden Eigenschaften ein:

- **subscription** (erforderlich): Die Abonnement-ID, die die Vorlagenspezifikationen hostet.
- **resourceGroup** (erforderlich): Der Name der Ressourcengruppe, die die Vorlagenspezifikationen enthält.

Das folgende Beispiel zeigt eine Konfigurationsdatei, die zwei Aliase für eine Modulregistrierung definiert, und einen Alias für eine Ressourcengruppe, die Vorlagenspezifikationen enthält.

```json
{
  "moduleAliases": {
    "br": {
      "ContosoRegistry": {
        "registry": "contosoregistry.azurecr.io"
      },
      "CoreModules": {
        "registry": "contosoregistry.azurecr.io",
        "modulePath": "bicep/modules/core"
      }
    },
    "ts": {
      "CoreSpecs": {
        "subscription": "00000000-0000-0000-0000-000000000000",
        "resourceGroup": "CoreSpecsRG"
      }
    }
  }
}
```

Wenn Sie einen Alias im Modulverweis verwenden, müssen Sie diese Formate verwenden:

```bicep
br/<alias>:<file>:<tag>
ts/<alias>:<file>:<tag>
```

Definieren Sie Ihre Aliase für den Ordner oder die Ressourcengruppe, der/die Module enthält, nicht für die Datei selbst. Der Dateiname muss im Verweis auf das Modul enthalten sein.

**Ohne die Aliase** würden Sie mit dem vollständigen Pfad eine Verknüpfung mit einem Modul in einer Registrierung herstellen.

```bicep
module stgModule 'br:contosoregistry.azurecr.io/bicep/modules/core/storage:v1' = {
```

**Mit den Aliasen** können Sie den Link vereinfachen, indem Sie den Alias für die Registrierung verwenden.

```bicep
module stgModule 'br/ContosoRegistry:bicep/modules/core/storage:v1' = {
```

Alternativ können Sie den Link vereinfachen, indem Sie den Alias verwenden, der die Registrierung und den Modulpfad angibt.

```bicep
module stgModule  'br/CoreModules:storage:v1' = {
```

Verwenden Sie als Vorlagenspezifikation:

```bicep
module stgModule  'ts/CoreSpecs:storage:v1' = {
```

## <a name="credentials-for-restoring-modules"></a>Anmeldeinformationen zum Wiederherstellen von Modulen

Um externe Module im lokalen Cache [wiederherzustellen](bicep-cli.md#restore), muss das Konto über die richtigen Berechtigungen für den Zugriff auf die Registrierung verfügen. Sie können die Rangfolge der Anmeldeinformationen für die Authentifizierung bei der Registrierung konfigurieren. Standardmäßig verwendet Bicep die Anmeldeinformationen des Benutzers, der in Azure CLI oder Azure PowerShell authentifiziert wurde. Um die Rangfolge der Anmeldeinformationen anzupassen, fügen Sie der Konfigurationsdatei die Elemente `cloud` und `credentialPrecedence` hinzu.

```json
{
    "cloud": {
      "credentialPrecedence": [
        "AzureCLI",
        "AzurePowerShell"
      ]
    }
}
```

Die verfügbaren Anmeldeinformationen sind:

* AzureCLI
* AzurePowerShell
* Environment
* ManagedIdentity
* VisualStudio
* VisualStudioCode

## <a name="customize-linter"></a>Anpassen des Linters

In der Konfigurationsdatei passen Sie die Einstellungen für den [Bicep-Linter](linter.md) an. Sie können den Linter aktivieren oder deaktivieren, regelspezifische Werte angeben und auch die Ebene von Regeln festlegen.

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


