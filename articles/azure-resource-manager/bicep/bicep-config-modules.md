---
title: Moduleinstellung für die Bicep-Konfiguration
description: Beschreibt, wie Konfigurationswerte für Module in Bicep-Bereitstellungen angepasst werden.
ms.topic: conceptual
ms.date: 11/16/2021
ms.openlocfilehash: e130695532d470a154acc0b35bca864fcdbcba9d
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557271"
---
# <a name="add-module-settings-in-the-bicep-config-file"></a>Hinzufügen von Moduleinstellungen in der Bicep-Konfigurationsdatei

In einer **bicepconfig.json**-Datei können Sie Aliase für Modulpfade erstellen und die Rangfolge der Anmeldeinformationen für die Wiederherstellung eines Moduls konfigurieren.

In diesem Artikel werden die Einstellungen beschrieben, die für die Arbeit mit [Modulen](modules.md) verfügbar sind.

## <a name="aliases-for-modules"></a>Aliase für Module

Um den Pfad zum Verknüpfen mit Modulen zu vereinfachen, erstellen Sie Aliase in der Konfigurationsdatei. Ein Alias verweist entweder auf eine Modulregistrierung oder eine Ressourcengruppe, die Vorlagenspezifikationen enthält.

Die Konfigurationsdatei verfügt über eine Eigenschaft für `moduleAliases`. Diese Eigenschaft enthält alle Aliase, die Sie definieren. Unter dieser Eigenschaft werden die Aliase basierend darauf aufgeteilt, ob sie auf eine Registrierung oder eine Vorlagenspezifikation verweisen.

Um einen Alias für eine **Bicep-Registrierung** zu erstellen, fügen Sie eine `br` Eigenschaft hinzu. Um einen Alias für eine **Vorlagenspezifikation** hinzuzufügen, verwenden Sie die `ts` Eigenschaft.

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

## <a name="next-steps"></a>Nächste Schritte

* [Konfigurieren Ihrer Bicep-Umgebung](bicep-config.md)
* [Hinzufügen von Lintereinstellungen zur Bicep-Konfiguration](bicep-config-linter.md)
* Informationen zu [Modulen](modules.md)
