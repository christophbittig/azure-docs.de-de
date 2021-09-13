---
title: Alle Dateitestfälle für Azure Resource Manager Testtoolkit
description: Beschreibt die Tests, die vom Azure Resource Manager Vorlagen-Testtoolkit für alle Dateien ausgeführt werden.
ms.topic: conceptual
ms.date: 07/16/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 6af9a7c149aacbd50537086ae70a8d845604d2ef
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114393508"
---
# <a name="test-cases-for-all-files"></a>Testfälle für alle Dateien

In diesem Artikel werden die Tests beschrieben, die mit dem [Vorlagen-Testtoolkit](test-toolkit.md) für alle JSON-Dateien (JavaScript Object Notation) ausgeführt werden. Die Beispiele umfassen die Testnamen und Codebeispiele, die die Tests **bestehen** (pass) oder **nicht bestehen** (fail). Weitere Informationen zur Ausführungsweise von Tests oder eines bestimmten Tests finden Sie unter [Testparameter](test-toolkit.md#test-parameters).

## <a name="use-valid-json-syntax"></a>Verwenden einer gültigen JSON-Syntax

Testname: JSONFiles Should Be Valid

Dieser Test überprüft, ob alle JSON-Dateien eine gültige Syntax enthalten. Klicken Sie in GitHub auf die Datei _azuredeploy.json_, _azuredeploy.parameters.json_ oder _createUiDefinition.json_. Wenn der Test **fehlschlägt**, werden Fehler oder Warnungen für andere Tests oder die JSON-Analyse angezeigt.

### <a name="template-file-example"></a>Beispiel für eine Vorlagendatei

Das folgende Beispiel schlägt fehl, da in _azuredeploy.json_ die führende geschweifte Klammer (`{`) in `parameters`, `comboBox` und `location` fehlt.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters":
    "comboBox":
      "type": "string"
    },
    "location":
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

Das folgende Beispiel **besteht den Test**.

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

### <a name="parameter-file-example"></a>Beispiel für eine Parameterdatei

Das folgende Beispiel **schlägt fehl**, weil _azuredeploy.parameters.json_ einen Parameter ohne `value` verwendet.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "value":
    }
  }
}
```

Das folgende Beispiel **besteht den Test**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "value": "westus"
    }
  }
}
```

### <a name="createuidefintion-example"></a>CreateUiDefintion-Beispiel

Das folgende Beispiel **schlägt fehl**, da in _azuredeploy.json_ die führende geschweifte Klammer (`{`) in Abschnitt `outputs` fehlt.

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
    "outputs":
      "comboBox": "[basics('comboBox')]",
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

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Testtoolkit finden Sie unter [Verwenden des Resource Manager Vorlagen-Testtoolkits](test-toolkit.md).
- Tests für ARM-Vorlagen finden Sie unter [Testfälle für ARM-Vorlagen](template-test-cases.md).
- Informationen zum Testen von Parameterdateien finden Sie unter [Testfälle für Parameterdateien](parameters.md).
- „createUiDefinition“-Tests finden Sie unter [Testfälle für „createUiDefinition.json“](createUiDefinition-test-cases.md).
