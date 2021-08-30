---
title: „createUiDefinition.json“-Testfälle für das Azure Resource Manager-Testtoolkit
description: Beschreibt die „createUiDefinition.json“-Tests, die vom Azure Resource Manager-Vorlagen-Testtoolkit ausgeführt werden.
ms.topic: conceptual
ms.date: 07/16/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 573f7db7c1f6fd3d45531e8be15db3d8affc8eba
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114392032"
---
# <a name="test-cases-for-createuidefinitionjson"></a>Testfälle für „createUiDefinition.json“

In diesem Artikel werden die Tests beschrieben, die mit dem [Vorlagen-Testtoolkit](test-toolkit.md) für [createUiDefinition.json](../managed-applications/create-uidefinition-overview.md)-Dateien ausgeführt werden. Die Beispiele umfassen die Testnamen und Codebeispiele, die die Tests **bestehen** (pass) oder **nicht bestehen** (fail).

Das Toolkit enthält [Testfälle](template-test-cases.md) für Azure Resource Manager-Vorlagen (ARM-Vorlagen) und die Hauptvorlagendateien namens _azuredeploy.json_ oder _maintemplate.json_. Wenn das Verzeichnis eine _createUiDefinition.json_-Datei enthält, werden spezifische Tests für Benutzeroberflächen-Steuerelemente ausgeführt. Weitere Informationen zur Ausführungsweise von Tests oder eines bestimmten Tests finden Sie unter [Testparameter](test-toolkit.md#test-parameters).

Die _createUiDefinition.json_-Datei erstellt benutzerdefinierte Benutzeroberflächen-Steuerelemente unter Verwendung von [Elementen](../managed-applications/create-uidefinition-elements.md) und [Funktionen](../managed-applications/create-uidefinition-functions.md).

## <a name="verify-template-parameter-allows-values"></a>Verifizieren, dass Vorlagenparameter Werte zulassen

Testname: **Allowed Values Should Actually Be Allowed** (Zulässige Werte sollten tatsächlich zulässig sein)

Dieser Test überprüft, ob Werte für jedes Steuerelement in _createUiDefinition.json_ in den Parametern der Hauptvorlage zulässig sind. Die Parameter werden anhand des Namens zwischen der Hauptvorlage und der _createUiDefinition.json_-Datei zugeordnet.

Die Parameter der Hauptvorlage müssen die Werte aus den `allowedValues` des jeweiligen Steuerelements akzeptieren. Der Test überprüft außerdem, ob im Abschnitt `outputs` der Datei _createUiDefinition.json_ auf das Steuerelement verwiesen wird.

Dieser Test überprüft die Hauptvorlage und die Datei _createUiDefinition.json_. Ein Beispiel für die Datei _createUiDefinition.json_ wird im Anschluss an die Hauptvorlagenbeispiele gezeigt.

Das folgende Beispiel **besteht den Test nicht**, weil der Parametername `combo` der Hauptvorlage nicht mit dem Parameternamen `comboBox` des Steuerelements übereinstimmt.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "combo": {
      "type": "string",
      "defaultValue": "two"
    }
  },
  "resources": [],
  "outputs": {
    "comboBoxOutput": {
      "type": "string",
      "value": "[parameters('combo')]"
    }
  }
}
```

Das folgende Beispiel **besteht den Test nicht**, weil der Parametertyp `int` der Hauptvorlage den `string`-Wert des Steuerelements nicht akzeptiert. Und wenn ein Parameter einer Hauptvorlage einen `defaultValue` definiert, muss er ein gültiger `value` in den `allowedValues` des jeweiligen Steuerelements sein.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "comboBox": {
      "type": "int",
      "defaultValue": 4
    }
  },
  "resources": [],
  "outputs": {
    "comboBoxOutput": {
      "type": "string",
      "value": "[parameters('combo')]"
    }
  }
}
```

Das folgende Beispiel **besteht den Test**, weil der Parametername der Hauptvorlage mit dem Parameternamen des Steuerelements übereinstimmt. Außerdem ist der Parametertyp der Vorlage eine `string` mit einem `defaultValue`, der in den `allowedValues` des Steuerelements angegeben ist.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "comboBox": {
      "type": "string",
      "defaultValue": "two"
    }
  },
  "resources": [],
  "outputs": {
    "comboBoxOutput": {
      "type": "string",
      "value": "[parameters('comboBox')]"
    }
  }
}
```

Die Datei _createUiDefinition.json_ für dieses Beispiel:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "demoComboBox",
        "label": "demoComboBoxLabel",
        "elements": [
          {
            "name": "comboBox",
            "type": "Microsoft.Common.DropDown",
            "label": "Example drop down",
            "defaultValue": "Value two",
            "toolTip": "This is a tool tip",
            "constraints": {
              "allowedValues": [
                {
                  "label": "Value one",
                  "description": "The value to select for option 1.",
                  "value": "one"
                },
                {
                  "label": "Value two",
                  "description": "The value to select for option 2.",
                  "value": "two"
                }
              ],
              "required": true
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "comboBox": "[steps('demoComboBox').comboBox]"
    }
  }
}
```

## <a name="output-controls-must-exist"></a>Es müssen Ausgabesteuerelemente vorhanden sein

Testname: **Controls In Outputs Must Exist** (Steuerelemente in Ausgaben müssen vorhanden sein)

Steuerelemente, die im Abschnitt `outputs` verwendet werden, müssen in einem Element an anderer Stelle in _createUiDefinition.json_ vorhanden sein. Der Name, auf den in `outputs` verwiesen wird, müssen einem in `basics[]` oder `steps[]` verwendeten Namen entsprechen.

Das folgende Beispiel **besteht den Test nicht**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "nameDoesNotMatchOutput",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

Das folgende Beispiel **besteht den Test**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

## <a name="properties-must-include-values"></a>Eigenschaften müssen Werte enthalten

Testname: **CreateUIDefinition Must Not Have Blanks** (CreateUIDefinition darf keine Leerstellen enthalten)

Eigenschaften müssen Werte enthalten. Erforderliche Eigenschaften müssen gültige Werte verwenden. Optionale Eigenschaften, die leer sind, sollten entfernt werden. Der Test lässt leere `"basics": []`, `"steps": []` oder `defaultValue` zu.

Das folgenden Beispiel **besteht den Test nicht**, weil `label`, `placeholder` und `toolTip` leer sind.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "",
        "placeholder": "",
        "defaultValue": "",
        "toolTip": ""
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

Das folgenden Beispiel **besteht den Test**, weil `label` und `toolTip` Werte besitzen und `placeholder` entfernt wurde.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "defaultValue": "",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

## <a name="use-valid-schema-and-version"></a>Verwenden eines gültigen Schemas und einer gültigen Version

Testname: **CreateUIDefinition Should Have Schema** (CreateUIDefinition sollte über ein Schema verfügen)

Die Datei _createUiDefinition.json_ muss eine `$schema`-Eigenschaft enthalten und ein gültiges `$schema` sowie eine gültige `version` verwenden. Die Versionszahlen in `$schema` und `version` müssen übereinstimmen.

Das folgende Beispiel **besteht den Test nicht**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.9.9-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.9.9-preview"
}
```

Das folgenden Beispiel **besteht den Test**, weil es das neueste `$schema` und die aktuellste `version` verwendet.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview"
}
```

## <a name="dont-hide-credential-confirmation"></a>Kein Ausblenden von Anmeldeinformationen

Testname: **Credential Confirmation Should Not Be Hidden** (Bestätigung von Anmeldeinformationen sollte nicht ausgeblendet werden)

Dieser Test überprüft, ob Anmeldeinformationen für [Microsoft.Common.PasswordBox](../managed-applications/microsoft-common-passwordbox.md) oder [Microsoft.Compute.CredentialsCombo](../managed-applications/microsoft-compute-credentialscombo.md) bestätigt werden. Die `hideConfirmation`-Eigenschaft sollte auf `false` festgelegt werden, damit die Bestätigung sichtbar ist.

Das folgende Beispiel **besteht den Test nicht**, weil `hideConfirmation` den Wert `true` hat.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "credentials",
        "type": "Microsoft.Compute.CredentialsCombo",
        "label": {
          "password": "Password",
          "confirmPassword": "Confirm password"
        },
        "toolTip": {
          "password": "Type your credentials"
        },
        "constraints": {
          "required": true,
          "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{12,}$",
          "customValidationMessage": "The password must be alphanumeric, contain at least 12 characters, and have at least 1 letter and 1 number."
        },
        "options": {
          "hideConfirmation": true
        },
        "osPlatform": "Windows",
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "credentials": "[basics('credentials')]"
    }
  }
}
```

Das folgende Beispiel **besteht den Test**, weil `hideConfirmation` den Wert `false` hat.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "credentials",
        "type": "Microsoft.Compute.CredentialsCombo",
        "label": {
          "password": "Password",
          "confirmPassword": "Confirm password"
        },
        "toolTip": {
          "password": "Type your credentials"
        },
        "constraints": {
          "required": true,
          "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{12,}$",
          "customValidationMessage": "The password must be alphanumeric, contain at least 12 characters, and have at least 1 letter and 1 number."
        },
        "options": {
          "hideConfirmation": false
        },
        "osPlatform": "Windows",
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "credentials": "[basics('credentials')]"
    }
  }
}
```

## <a name="use-correct-handler"></a>Verwenden des richtigen Handlers

Testname: **Handler Must Be Correct** (Handler muss korrekt sein)

Verwenden Sie `Microsoft.Azure.CreateUIDef` oder `Microsoft.Compute.MultiVm` in der Datei _createUIDefinition.json_.

Das folgende Beispiel **besteht den Test nicht**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.",
  "version": "0.1.2-preview"
}
```

Das folgende Beispiel **besteht den Test**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview"
}
```

## <a name="dont-hide-existing-resources"></a>Kein Ausblenden vorhandener Ressourcen

Testname: **HideExisting Must Be Correctly Handled** (HideExisting muss korrekt gehandhabt werden)

Wenn `hideExisting`0 auf `false` festgelegt ist oder ausgelassen wird, muss `outputs` `resourceGroup` und `newOrExisting` enthalten. Die Standardeinstellung für `hideExisting` ist `false`.

Beispiele für Steuerelementttypen, die `hideExisting` umfassen, sind [Microsoft.Storage.StorageAccountSelector](../managed-applications/microsoft-storage-storageaccountselector.md), [Microsoft.Network.PublicIpAddressCombo](../managed-applications/microsoft-network-publicipaddresscombo.md) oder [Microsoft.Network.VirtualNetworkCombo](../managed-applications/microsoft-network-virtualnetworkcombo.md).

Das folgende Beispiel **besteht den Test nicht**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "storage",
        "type": "Microsoft.Storage.StorageAccountSelector",
        "label": "Storage account",
        "toolTip": "This is a demo storage account",
        "defaultValue": {
          "name": "storageaccount01",
          "type": "Premium_LRS"
        },
        "options": {
          "hideExisting": false
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]"
    }
  }
}
```

Das folgende Beispiel **besteht den Test**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "storage",
        "type": "Microsoft.Storage.StorageAccountSelector",
        "label": "Storage account",
        "toolTip": "This is a demo storage account",
        "defaultValue": {
          "name": "storageaccount01",
          "type": "Premium_LRS"
        },
        "options": {
          "hideExisting": false
        },
        "visible": false
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "resourceGroup": "[basics('storage').resourceGroup]",
      "newOrExisting": "[basics('storage').newOrExisting]"
    }
  }
}
```

## <a name="use-location-in-outputs"></a>Verwenden von Standorten in Ausgaben

Testname: **Location Should Be In Outputs** (Standort sollte in Ausgaben vorhanden sein)

Der Abschnitt `outputs` sollte einen Standort unter Verwendung der Funktion [location](../managed-applications/create-ui-definition-referencing-functions.md#location) enthalten.

Das folgenden Beispiel **besteht den Test nicht**, weil `outputs` keinen Standort enthält.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

Das folgende Beispiel **besteht den Test**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]",
      "location": "[location()]"
    }
  }
}
```

## <a name="include-control-outputs-in-template-parameters"></a>Einschließen von Steuerelementausgaben in Voralgenparameter

Testname: **Outputs Must Be Present In Template Parameters** (Ausgabe muss in Vorlagenparametern vorhanden sein)

Der Test überprüft, ob _createUiDefinition.json_ einen Abschnitt `outputs` enthält. Der Test überprüft außerdem, ob diese `outputs` im Abschnitt `parameters` der Hauptvorlage definiert sind. Die Namen müssen übereinstimmen, weil Parameter anhand des Namens zwischen der _createUiDefinition.json_-Datei und der Hauptvorlage zugeordnet werden.

Dieser Test überprüft die Hauptvorlage und die Datei _createUiDefinition.json_. Ein Beispiel für die Datei _createUiDefinition.json_ wird im Anschluss an die Hauptvorlagenbeispiele gezeigt.

Das folgende Beispiel **besteht den Test nicht**, weil die Hauptvorlage den Parameter `comboBox` aus dem Abschnitt `outputs` der Datei _createUiDefinition.json_ nicht enthält.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "resources": [],
  "outputs": {
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    }
  }
}
```

Das folgende Beispiel **besteht den Test**, weil die Hauptvorlage den Parameter `comboBox` enthält.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "comboBox": {
      "type": "string",
      "defaultValue": "two"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "resources": [],
  "outputs": {
    "comboBox": {
      "type": "string",
      "value": "[parameters('comboBox')]"
    },
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    }
  }
}
```

Die Datei _createUiDefinition.json_ für dieses Beispiel:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]",
      "location": "[location()]"
    }
  }
}
```

## <a name="parameters-without-default-must-exist-in-outputs"></a>Parameter ohne Standardwert müssen in Ausgaben vorhanden sein

Testname: **Parameters Without Default Must Exist In CreateUIDefinition** (Parameter ohne Standardwert müssen in CreateUIDefinition vorhanden sein)

Parameter in der Hauptvorlage ohne einen Standardwert müssen im Abschnitt `outputs` der Datei _createUiDefinition.json_ vorhanden sein.

Dieser Test überprüft die Hauptvorlage und die Datei _createUiDefinition.json_. Ein Beispiel für die Datei _azuredeploy.json_ wird im Anschluss an das Beispiel für das Steuerelement gezeigt.

Das folgende Beispiel **besteht den Test nicht**, weil der Abschnitt `outputs` der Datei _createUiDefinition.json_ nicht den Parameter `comboBox` der Hauptvorlage enthält.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]"
    }
  }
}
```

Das folgende Beispiel **besteht den Test**, weil _createUiDefinition.json_ den Parameter `comboBox` im Abschnitt `outputs` enthält.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]",
      "location": "[location()]"
    }
  }
}
```

Die Datei _azuredeploy.json_ für dieses Beispiel. Der Parameter `comboBox` hat keinen Standardwert.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "comboBox": {
      "type": "string"
    },
    "location": {
      "type": "string"
    }
  },
  "resources": [],
  "outputs": {
    "comboBox": {
      "type": "string",
      "value": "[parameters('comboBox')]"
    },
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    }
  }
}
```

## <a name="use-secure-parameter-with-password-box"></a>Verwenden eines sicheren Parameters mit Kennwortfeld

Testname: **Password Textboxes Must Be Used For Password Parameters** (Kennworttextfelder müssen für Kennwortparameter verwendet werden)

Dieser Test überprüft, ob ein [Microsoft.Common.PasswordBox](../managed-applications/microsoft-common-passwordbox.md)-Element im Abschnitt `parameters` der Hauptvorlage und im Abschnitt `outputs` der Datei _createUiDefinition.json_ definiert ist. Der Parametertyp der Hauptvorlage für ein Kennwortfeld muss `secureString` oder `secureObject` sein.

Dieser Test überprüft die Hauptvorlage und die Datei _createUiDefinition.json_. Ein Beispiel für die Datei _createUiDefinition.json_ wird im Anschluss an die Hauptvorlagenbeispiele gezeigt.

Das folgende Beispiel **besteht den Test nicht**, weil der Parameter `passwordBox` der Hauptvorlage ein `string` ist.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "passwordBox": {
      "type": "string"
    },
    "location": {
      "type": "string"
    }
  },
  "resources": [],
  "outputs": {
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    }
  }
}
```

Das folgende Beispiel **besteht den Test**, weil der Parameter `passwordBox` der Hauptvorlage ein `secureString` ist.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "passwordBox": {
      "type": "secureString"
    },
    "location": {
      "type": "string"
    }
  },
  "resources": [],
  "outputs": {
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    }
  }
}
```

Die Datei _createUiDefinition.json_ für dieses Beispiel:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "passwordBox",
        "type": "Microsoft.Common.PasswordBox",
        "label": {
          "password": "Password",
          "confirmPassword": "Confirm password"
        },
        "toolTip": "Type a password"
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "passwordBox": "[basics('passwordBox')]"
    }
  }
}
```

## <a name="password-box-requires-minimum-length"></a>Kennwortfeld erfordert Mindestlänge

Testname: **PasswordBoxes Must Have Min Length** (Kennwortfelder müssen eine Mindestlänge besitzen)

Der Test überprüft, ob das [Microsoft.Common.PasswordBox](../managed-applications/microsoft-common-passwordbox.md)-Element `constraints` mit einem `regex` verwendet, der mindestens 12 Zeichen erfordert.

Das folgende Beispiel **besteht den Test nicht**, weil keine `constraints` vorhanden sind.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "passwordBox",
        "type": "Microsoft.Common.PasswordBox",
        "label": {
          "password": "Password",
          "confirmPassword": "Confirm password"
        },
        "toolTip": "Type a password"
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "passwordBox": "[basics('passwordBox')]"
    }
  }
}
```

Das folgende Beispiel **besteht den Test**, weil der `regex` mindestens 12 Zeichen erfordert.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "passwordBox",
        "type": "Microsoft.Common.PasswordBox",
        "label": {
          "password": "Password",
          "confirmPassword": "Confirm password"
        },
        "toolTip": "Type a password",
        "constraints": {
          "required": true,
          "regex": "^[a-zA-Z0-9]{12,}$",
          "validationMessage": "Password must be at least 12 characters long, contain only numbers and letters"
        }
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "passwordBox": "[basics('passwordBox')]"
    }
  }
}
```

## <a name="text-box-must-use-validation"></a>Textfeld muss Validierung verwenden

Testname: **Textboxes Are Well Formed** (Textfelder sind wohlgeformt)

Verwenden Sie eine Validierung mit Textfeldern, um auf `constraints` zu prüfen, die einen `regex` und eine `message` enthalten.

Das folgende Beispiel **besteht den Test nicht**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "textBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Text box",
        "toolTip": "Type 1-30 alphanumeric characters",
        "placeholder": "Type your text here",
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "textBox": "[basics('textBox')]"
    }
  }
}
```

Das folgende Beispiel **besteht den Test**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "textBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Text box",
        "toolTip": "Type 1-30 alphanumeric characters",
        "placeholder": "Type your text here",
        "constraints": {
          "required": true,
          "validations": [
            {
              "regex": "^[a-z0-9A-Z]{1,30}$",
              "message": "Only 1-30 characters alphanumeric characters are allowed."
            }
          ]
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "textBox": "[basics('textBox')]"
    }
  }
}
```

## <a name="tooltip-must-exist-with-a-value"></a>QuickInfo (toolTip) muss mit einem Wert vorhanden sein

Testname: **Tooltips Should Be Present** (QuickInfos sollten vorhanden sein)

Dieser Test überprüft, ob die `toolTip`-Eigenschaft vorhanden ist und einen Wert enthält.

Das folgende Beispiel **besteht den Test nicht**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": ""
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

Das folgende Beispiel **besteht den Test**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

## <a name="dont-set-a-default-user-name"></a>Kein Festsetzen eines Standardbenutzernamens

Testname: **Usernames Should Not Have A Default** (Benutzernamen sollten keinen Standardwert haben)

Der Test überprüft, ob für [Microsoft.Compute.UserNameTextBox](../managed-applications/microsoft-compute-usernametextbox.md) ein `defaultValue` festgelegt ist.

Das folgenden Beispiel **besteht den Test nicht**, weil ein `defaultValue` angegeben ist.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "userNameBox",
        "type": "Microsoft.Compute.UserNameTextBox",
        "label": "User name",
        "defaultValue": "admin",
        "toolTip": "Enter your user name",
        "osPlatform": "Windows"
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "userNameBox": "[basics('userNameBox')]"
    }
  }
}
```

Das folgende Beispiel **besteht den Test**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "userNameBox",
        "type": "Microsoft.Compute.UserNameTextBox",
        "label": "User name",
        "toolTip": "Enter your user name",
        "osPlatform": "Windows"
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "userNameBox": "[basics('userNameBox')]"
    }
  }
}
```

## <a name="use-message-with-validations"></a>Verwenden einer Meldung mit Validierungen

Testname: **Validations Must Have Message** (Validierungen müssen eine Meldung besitzen)

Dieser Test überprüft, ob jegliche `validations` in _createUiDefinition.json_ eine `message` enthalten.

Das folgenden Beispiel **besteht den Test nicht**, weil die Validierung des `regex` keine `message` enthält.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "textBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Text box",
        "toolTip": "Type 1-30 alphanumeric characters",
        "placeholder": "Type your text here",
        "constraints": {
          "required": true,
          "validations": [
            {
              "regex": "^[a-z0-9A-Z]{1,30}$"
            }
          ]
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "textBox": "[basics('textBox')]"
    }
  }
}
```

Das folgende Beispiel **besteht den Test**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "textBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Text box",
        "toolTip": "Type 1-30 alphanumeric characters",
        "placeholder": "Type your text here",
        "constraints": {
          "required": true,
          "validations": [
            {
              "regex": "^[a-z0-9A-Z]{1,30}$",
              "message": "Only 1-30 characters alphanumeric characters are allowed."
            }
          ]
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "textBox": "[basics('textBox')]"
    }
  }
}
```

## <a name="virtual-machine-sizes-must-match"></a>Größen virtueller Computer müssen übereinstimmen

Testname: **VM Sizes Must Match Template** (VM-Größen müssen mit Vorlage übereinstimmen)

Dieser Test überprüft, ob ein [Microsoft.Compute.SizeSelector](../managed-applications/microsoft-compute-sizeselector.md) im Abschnitt `outputs` der Datei _createUiDefinition.json_ und im Abschnitt `parameters` der Hauptvorlage vorhanden ist. Parameter der Hauptvorlage, die einen `defaultValue` angeben, müssen mit einem Wert in `allowedSizes` des Steuerelements übereinstimmen.

Dieser Test überprüft die Hauptvorlage und die Datei _createUiDefinition.json_. Ein Beispiel für die Datei _createUiDefinition.json_ wird im Anschluss an die Hauptvorlagenbeispiele gezeigt.

Das folgende Beispiel **besteht den Test nicht**, weil der `defaultValue` der Hauptvorlage keinem Wert in `allowedSizes` entspricht.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D9"
    }
  },
  "resources": [],
  "outputs": {
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    },
    "vmSize": {
      "type": "string",
      "value": "[parameters('vmSize')]"
    }
  }
}
```

Das folgende Beispiel **besteht den Test**, weil der `defaultValue` der Hauptvorlage einem Wert in `allowedSizes` entspricht.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D3"
    }
  },
  "resources": [],
  "outputs": {
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    },
    "vmSize": {
      "type": "string",
      "value": "[parameters('vmSize')]"
    }
  }
}
```

Die Datei _createUiDefinition.json_ für dieses Beispiel:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "vmSize",
        "type": "Microsoft.Compute.SizeSelector",
        "label": "VM Size",
        "toolTip": "Select a virtual machine size",
        "recommendedSizes": [
          "Standard_D1"
        ],
        "constraints": {
          "allowedSizes": [
            "Standard_D1",
            "Standard_D2",
            "Standard_D3"
          ]
        },
        "osPlatform": "Windows",
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "vmSize": "[basics('vmSize')]"
    }
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Erstellen einer Azure-Portal-Benutzeroberfläche finden Sie unter [Die Datei „CreateUiDefinition.json“ für die Benutzeroberfläche zum Erstellen verwalteter Azure-Anwendungen](../managed-applications/create-uidefinition-overview.md).
- Informationen zum Verwenden der Sandbox zum Erstellen von Benutzeroberflächendefinitionen finden Sie unter [Testen Ihrer Portaloberfläche für Azure Managed Applications](../managed-applications/test-createuidefinition.md).
- Weitere Informationen zu Benutzeroberflächen-Steuerelementen finden Sie unter [CreateUiDefinition-Elemente](../managed-applications/create-uidefinition-elements.md) und [CreateUiDefinition-Funktionen](../managed-applications/create-uidefinition-functions.md).
- Weitere Informationen zu den Tests für ARM-Vorlagen finden Sie unter [Testfälle für ARM-Vorlagen](template-test-cases.md).
