---
title: Parameterdateitestfälle für Azure Resource Manager-Testtoolkit
description: Beschreibt die Parameterdateitests, die vom Azure Resource Manager-Vorlagen-Testtoolkit ausgeführt werden.
ms.topic: conceptual
ms.date: 07/16/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 37fefd84a6385e003a4bb501a789003e8c63d461
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114393626"
---
# <a name="test-cases-for-parameter-files"></a>Testfälle für Parameterdateien

In diesem Artikel werden die Tests beschrieben, die mit dem [Vorlagen-Testtoolkit](test-toolkit.md) für [Parameterdateien](parameter-files.md) ausgeführt werden. Beispielsweise eine Datei mit dem Namen _azuredeploy.parameters.js_. Die Beispiele umfassen die Testnamen und Codebeispiele, die die Tests **bestehen** (pass) oder **nicht bestehen** (fail). Weitere Informationen zur Ausführungsweise von Tests oder eines bestimmten Tests finden Sie unter [Testparameter](test-toolkit.md#test-parameters).

Das Toolkit enthält [Testfälle](template-test-cases.md) für Azure Resource Manager-Vorlagen (ARM-Vorlagen) und die Hauptvorlagendateien namens _azuredeploy.json_ oder _maintemplate.json_.

## <a name="use-valid-contentversion"></a>Verwenden einer gültigen contentVersion

Testname: DeploymentParameters Should Have ContentVersion

Die `contentVersion` muss eine Zeichenfolge im Format `1.0.0.0` enthalten und darf nur Zahlen verwenden.

Das folgende Beispiel besteht den Test **nicht**, weil keine `contentVersion` vorhanden ist.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

Das folgende Beispiel **besteht** den Test, weil `contentVersion` keine Zeichenfolge ist.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": {},
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

Das folgende Beispiel **besteht** den Test.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

## <a name="file-must-include-parameters"></a>Datei muss Parameter enthalten

Testname: DeploymentParameters Should Have Parameters

Eine Parameterdatei muss den `parameters`-Abschnitt enthalten.

Das folgende Beispiel besteht den Test **nicht**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
}
```

Das folgende Beispiel **besteht** den Test.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

## <a name="use-valid-schema-version"></a>Verwenden einer gültigen Schemaversion

Testname: DeploymentParameters Should Have Schema

Die Parameterdatei muss eine gültige Schemaversion enthalten.

Es gibt zwei gültige Schemaversionen für Parameterdateien:

- `https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#`
- `https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#`

Das folgende Beispiel besteht den Test **nicht**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2021-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

Das folgende Beispiel **besteht** den Test.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

## <a name="parameters-must-contain-values"></a>Parameter müssen Werte enthalten

Testname: DeploymentParameters Should Have Value

Ein Parameter muss einen `value` oder eine `reference` enthalten. Für Geheimnisse wie z. B. ein Kennwort verwendet ein Schlüsseltresor eine `reference` in der Parameterdatei. Weitere Informationen finden Sie unter [Verwenden von Azure Key Vault zum Übergeben eines sicheren Parameterwerts während der Bereitstellung](key-vault-parameter.md).

Das folgende Beispiel besteht den Test **nicht**, weil ein `stgAcctName` keinen `value` hat.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stgAcctName": {}
  }
}
```

Das folgende Beispiel **besteht** den Test.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Testtoolkit finden Sie unter [Verwenden des Resource Manager-Vorlagen-Testtoolkits](test-toolkit.md).
- Tests für ARM-Vorlagen finden Sie unter [Testfälle für ARM-Vorlagen](template-test-cases.md).
- Unter [Testfälle für „createUiDefinition.json“](createUiDefinition-test-cases.md) finden Sie createUiDefinition-Tests.
- Informationen zu Tests für alle Dateien finden Sie unter [Testfälle für alle Dateien](all-files-test-cases.md).
