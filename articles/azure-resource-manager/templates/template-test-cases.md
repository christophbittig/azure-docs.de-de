---
title: Vorlagentestfälle für das Testtoolkit
description: Beschreibt die Vorlagentests, die vom Azure Resource Manager-Vorlagen-Testtoolkit ausgeführt werden.
ms.topic: conceptual
ms.date: 07/30/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 961a62ae45b423e2e4c2abf8a4e7d771c2775591
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122347001"
---
# <a name="test-cases-for-arm-templates"></a>Testfälle für ARM-Vorlagen

In diesem Artikel werden die Tests beschrieben, die mit dem [Vorlagen-Testtoolkit](test-toolkit.md) für Azure Resource Manager-Vorlagen (ARM-Vorlagen) ausgeführt werden. Er enthält Beispiele, bei denen der Test **bestanden** oder **nicht bestanden** wird, und umfasst den Namen des jeweiligen Tests. Weitere Informationen zur Ausführungsweise von Tests oder eines bestimmten Tests finden Sie unter [Testparameter](test-toolkit.md#test-parameters).

## <a name="use-correct-schema"></a>Verwendung des richtigen Schemas

Testname: **DeploymentTemplate Schema Is Correct**

Sie müssen in Ihrer Vorlage einen gültigen Schemawert angeben.

Das folgende Beispiel **besteht den Test nicht**, weil das Schema ungültig ist.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-01-01/deploymentTemplate.json#",
}
```

Im folgenden Beispiel wird eine **Warnung** angezeigt, da die Schemaversion `2015-01-01` veraltet ist und nicht gepflegt wird.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
}
```

Das folgende Beispiel **besteht den Test**, indem es ein gültiges Schema verwendet.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
}
```

Die `schema`-Eigenschaft der Vorlage muss auf eins der folgenden Schemas festgelegt sein:

- `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`
- `https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`
- `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`
- `https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#`
- `https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json`

## <a name="declared-parameters-must-be-used"></a>Verwendung deklarierter Parameter

Testname: **Parameters Must Be Referenced**

Dieser Test sucht nach Parametern, die nicht in der Vorlage verwendet werden, oder nach Parametern, die nicht in einem gültigen Ausdruck verwendet werden.

Löschen Sie alle Parameter, die definiert sind, aber nicht verwendet werden, um Ihre Vorlage übersichtlicher zu machen. Das Beseitigen nicht verwendeter Parameter vereinfacht die Bereitstellung von Vorlagen, da Sie keine unnötigen Werte angeben müssen.

Das folgende Beispiel **besteht den Test nicht**, weil in dem Ausdruck, der auf einen Parameter verweist, die führende eckige Klammer (`[`) fehlt.

```json
"resources": [
  {
    "location": " parameters('location')]"
  }
]
```

Das folgende Beispiel **besteht den Test**, weil der Ausdruck gültig ist.

```json
"resources": [
  {
    "location": "[parameters('location')]"
  }
]
```

## <a name="secure-parameters-cant-have-hard-coded-default"></a>Keine hartcodierten Standardwerte für sichere Parameter

Testname: **Secure String Parameters Cannot Have Default**

Stellen Sie keinen hartcodierten Standardwert für einen sicheren Parameter in Ihrer Vorlage bereit. Ein sicherer Parameter kann eine leere Zeichenfolge als Standardwert aufweisen oder die [newGuid](template-functions-string.md#newguid)-Funktion in einem Ausdruck verwenden.

Für Parameter, die vertrauliche Werte enthalten (z. B. Kennwörter), verwenden Sie die Typen `secureString` oder `secureObject`. Der Wert von Parametern mit einem sicheren Typ wird nicht protokolliert oder im Bereitstellungsverlauf gespeichert. Dadurch wird verhindert, dass der sensible Wert von einem böswilligen Benutzer entdeckt wird.

Wenn Sie einen Standardwert angeben, ist dieser Wert für jeden Benutzer sichtbar, der Zugriff auf die Vorlage oder den Bereitstellungsverlauf hat.

Das folgende Beispiel **besteht den Test nicht**.

```json
"parameters": {
  "adminPassword": {
    "defaultValue": "HardcodedPassword",
    "type": "secureString"
  }
}
```

Das nächste Beispiel **besteht den Test**.

```json
"parameters": {
  "adminPassword": {
    "type": "secureString"
  }
}
```

Das folgende Beispiel **besteht den Test**, weil die `newGuid`-Funktion verwendet wird.

```json
"parameters": {
  "secureParameter": {
    "type": "secureString",
    "defaultValue": "[newGuid()]"
  }
}
```

## <a name="environment-urls-cant-be-hard-coded"></a>Umgebungs-URLs können nicht hartcodiert werden

Testname: **DeploymentTemplate Must Not Contain Hardcoded Uri**

Die Umgebungs-URLs in Ihrer Vorlage dürfen nicht hartcodiert werden. Verwenden Sie stattdessen die [Umgebungsfunktion](template-functions-deployment.md#environment), um diese URLs während der Bereitstellung dynamisch abzurufen. Eine Liste der blockierten URL-Hosts finden Sie im Artikel zum [Testfall](https://github.com/Azure/arm-ttk/blob/master/arm-ttk/testcases/deploymentTemplate/DeploymentTemplate-Must-Not-Contain-Hardcoded-Uri.test.ps1).

Das folgende Beispiel **besteht den Test nicht**, weil die URL hartcodiert ist.

```json
"variables":{
  "AzureURL":"https://management.azure.com"
}
```

Der Test ist auch bei Verwendung von [concat](template-functions-string.md#concat) oder [uri](template-functions-string.md#uri) **nicht erfolgreich**.

```json
"variables":{
  "AzureSchemaURL1": "[concat('https://','gallery.azure.com')]",
  "AzureSchemaURL2": "[uri('gallery.azure.com','test')]"
}
```

Das folgende Beispiel **besteht den Test**.

```json
"variables": {
  "AzureSchemaURL": "[environment().gallery]"
}
```

## <a name="location-uses-parameter"></a>Verwendung eines Parameters für den Speicherort

Testname: **Location Should Not Be Hardcoded**

Um den Standort einer Ressource festzulegen, sollten Ihre Vorlagen über einen Parameter mit dem Namen `location` verfügen, dessen Typ auf `string` festgelegt ist. In der Hauptvorlage _azuredeploy.json_ oder _mainTemplate.json_ kann dieser Parameter standardmäßig auf den Standort der Ressourcengruppe festgelegt werden. In verknüpften oder geschachtelten Vorlagen sollte der location-Parameter keinen Standardstandort aufweisen.

Vorlagenbenutzer haben möglicherweise eingeschränkten Zugriff auf Regionen, in denen sie Ressourcen erstellen können. Ein hartcodierter Ressourcenstandort hindert Benutzer möglicherweise daran, eine Ressource zu erstellen. Der Ausdruck `"[resourceGroup().location]"` könnte Benutzer blockieren, wenn die Ressourcengruppe in einer Region erstellt wurde, auf die der Benutzer nicht zugreifen kann. Blockierte Benutzer können die Vorlage nicht verwenden.

Wenn Sie einen `location`-Parameter angeben, der als Standardwert den Standort der Ressourcengruppe aufweist, können die Benutzer den Standardwert verwenden oder einen anderen Standort angeben.

Das folgende Beispiel **besteht den Test nicht**, weil der `location` der Ressource auf `resourceGroup().location` festgelegt ist.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "[resourceGroup().location]",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ]
}
```

Im nächsten Beispiel wird ein `location`-Parameter verwendet, aber **der Test wird nicht bestanden**, da der Standardwert des Parameters ein hartcodierter Standort ist.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "westus"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "[parameters('location')]",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ],
  "outputs": {}
}
```

Das folgende Beispiel **besteht den Test**, wenn die Vorlage als Hauptvorlage verwendet wird. Erstellen Sie einen Parameter, dessen Standardwert der Ressourcengruppenstandort ist, Benutzern jedoch die Angabe eines anderen Werts ermöglicht.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for the resources."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "[parameters('location')]",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ],
  "outputs": {}
}
```

> [!NOTE]
> Wenn das vorherige Beispiel als verknüpfte Vorlage verwendet wird, wird der Test **nicht bestanden**. Wenn Sie die Verwendung als verknüpfte Vorlage planen, entfernen Sie den Standardwert.

## <a name="resources-should-have-location"></a>Verwendung eines Speicherorts für Ressourcen

Testname: **Resources Should Have Location**

Der Speicherort für eine Ressource sollte auf einen [Vorlagenausdruck](template-expressions.md) oder `global` festgelegt werden. Der Vorlagenausdruck verwendet in der Regel den unter [Standort verwendet Parameter](#location-uses-parameter) beschriebenen `location`-Parameter.

Das folgende Beispiel **besteht den Test nicht**, weil der `location` kein Ausdruck oder `global` ist.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "functions": [],
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "westus",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ],
  "outputs": {}
}
```

Das folgende Beispiel **besteht den Test**, weil der `location` der Ressource auf `global` festgelegt ist.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "functions": [],
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "global",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ],
  "outputs": {}
}

```

Das nächste Beispiel **besteht den Test** ebenfalls, weil der `location`-Parameter einen Ausdruck verwendet. Die Ressource `location` verwendet den Wert des Ausdrucks.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for the resources."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "[parameters('location')]",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ],
  "outputs": {}
}
```

## <a name="vm-size-uses-parameter"></a>Verwendung eines Parameters für die VM-Größe

Testname: **VM Size Should Be A Parameter**

Die `vmSize` des `hardwareProfile` des Objekts darf nicht hartcodiert werden. Der Test wird nicht bestanden, wenn das `hardwareProfile` ausgelassen wird oder einen hartcodierten Wert enthält. Stellen Sie einen Parameter bereit, damit Benutzer Ihrer Vorlage die Größe der bereitgestellten VM ändern können. Weitere Informationen finden Sie unter [Microsoft.Compute virtualMachines](/azure/templates/microsoft.compute/virtualmachines).

Das folgende Beispiel **besteht den Test nicht**, weil die `vmSize` des `hardwareProfile` des Objekts ein hartcodierter Wert ist.

```json
"resources": [
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2020-12-01",
    "name": "demoVM",
    "location": "[parameters('location')]",
    "properties": {
      "hardwareProfile": {
        "vmSize": "Standard_D2_v3"
      }
    }
  }
]
```

Das Beispiel  **besteht den Test**, wenn ein Parameter einen Wert für `vmSize` angibt:

```json
"parameters": {
  "vmSizeParameter": {
    "type": "string",
    "defaultValue": "Standard_D2_v3",
    "metadata": {
      "description": "Size for the virtual machine."
    }
  }
}
```

Dann verwendet `hardwareProfile` einen Ausdruck für `vmSize`, um auf den Wert des Parameters zu verweisen:

```json
"resources": [
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2020-12-01",
    "name": "demoVM",
    "location": "[parameters('location')]",
    "properties": {
      "hardwareProfile": {
        "vmSize": "[parameters('vmSizeParameter')]"
      }
    }
  }
]
```

## <a name="min-and-max-values-are-numbers"></a>Angabe von Mindest- und Maximalwerten als Zahlen

Testname: **Min And Max Value Are Numbers**

Wenn Sie einen Parameter mit `minValue` und `maxValue` definieren, geben Sie diese als Zahlen an. Sie müssen `minValue` und `maxValue` als Paar verwenden, sonst wird der Test nicht bestanden.

Das folgenden Beispiel **besteht den Test nicht**, weil `minValue` und `maxValue` Zeichenfolgen sind.

```json
"exampleParameter": {
  "type": "int",
  "minValue": "0",
  "maxValue": "10"
}
```

Das folgende Beispiel **besteht den Test nicht**, weil nur `minValue` verwendet wird.

```json
"exampleParameter": {
  "type": "int",
  "minValue": 0
}
```

Das folgenden Beispiel **besteht den Test**, weil `minValue` und `maxValue` Zahlen sind.

```json
"exampleParameter": {
  "type": "int",
  "minValue": 0,
  "maxValue": 10
}
```

## <a name="artifacts-parameter-defined-correctly"></a>Korrekte Definition von Artefaktparametern

Testname: **artifacts parameter**

Wenn Sie Parameter für `_artifactsLocation` und `_artifactsLocationSasToken` hinzufügen, müssen Sie die richtigen Standardwerte und Typen verwenden. Die folgenden Bedingungen müssen erfüllt sein, damit dieser Test erfolgreich ist:

- Wenn Sie einen Parameter bereitstellen, müssen Sie auch den anderen Parameter angeben.
- `_artifactsLocation` muss vom Typ `string` sein.
- `_artifactsLocation` muss in der Hauptvorlage über einen Standardwert verfügen.
- `_artifactsLocation` darf in einer geschachtelten Vorlage keinen Standardwert haben.
- `_artifactsLocation` muss entweder `"[deployment().properties.templateLink.uri]"` oder die Basis-URL des Repositorys als Standardwert aufweisen.
- `_artifactsLocationSasToken` muss vom Typ `secureString` sein.
- `_artifactsLocationSasToken` darf nur eine leere Zeichenfolge als Standardwert aufweisen.
- `_artifactsLocationSasToken` darf in einer geschachtelten Vorlage keinen Standardwert haben.

## <a name="declared-variables-must-be-used"></a>Verwendung deklarierter Variablen

Testname: **Variables Must Be Referenced**

Dieser Test sucht nach Variablen, die nicht in der Vorlage oder nicht in einem gültigen Ausdruck verwendet werden. Löschen Sie alle Variablen, die definiert sind, aber nicht verwendet werden, um Ihre Vorlage übersichtlicher zu machen.

Auf Variablen, die das `copy`-Element zum Iterieren von Werten verwenden, muss verwiesen werden. Weitere Informationen finden Sie unter [Variableniteration in ARM-Vorlagen](copy-variables.md).

Das folgende Beispiel **besteht den Test nicht**, weil auf die Variable, die das `copy`-Element verwendet, nicht verwiesen wird.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "itemCount": {
      "type": "int",
      "defaultValue": 5
    }
  },
  "variables": {
    "copy": [
      {
        "name": "stringArray",
        "count": "[parameters('itemCount')]",
        "input": "[concat('item', copyIndex('stringArray', 1))]"
      }
    ]
  },
  "resources": [],
  "outputs": {}
}
```

Das folgende Beispiel **besteht den Test nicht**, weil in dem Ausdruck, der auf eine Variable verweist, die führende eckige Klammer (`[`) fehlt.

```json
"outputs": {
  "outputVariable": {
    "type": "string",
    "value": " variables('varExample')]"
  }
}
```

Das folgende Beispiel **besteht den Test**, weil in `outputs` auf die Variable verwiesen wird.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "itemCount": {
      "type": "int",
      "defaultValue": 5
    }
  },
  "variables": {
    "copy": [
      {
        "name": "stringArray",
        "count": "[parameters('itemCount')]",
        "input": "[concat('item', copyIndex('stringArray', 1))]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "arrayResult": {
      "type": "array",
      "value": "[variables('stringArray')]"
    }
  }
}
```

Das folgende Beispiel **besteht den Test**, weil der Ausdruck gültig ist.

```json
"outputs": {
  "outputVariable": {
    "type": "string",
    "value": "[variables('varExample')]"
  }
}
```

## <a name="dynamic-variable-should-not-use-concat"></a>Keine Verwendung der concat-Funktion für dynamische Variablen

Testname: **Dynamic Variable References Should Not Use Concat**

Manchmal müssen Sie eine Variable dynamisch auf Grundlage des Werts einer anderen Variablen oder eines anderen Parameters erstellen. Verwenden Sie beim Festlegen des Werts nicht die [concat](template-functions-string.md#concat)-Funktion. Verwenden Sie stattdessen ein Objekt, das die verfügbaren Optionen enthält, und rufen Sie während der Bereitstellung dynamisch eine der Eigenschaften aus dem Objekt ab.

Das folgende Beispiel **besteht den Test**. Die Variable `currentImage` wird während der Bereitstellung dynamisch festgelegt.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "osType": {
      "type": "string",
      "allowedValues": [
        "Windows",
        "Linux"
      ]
    }
  },
  "variables": {
    "imageOS": {
      "Windows": {
        "image": "Windows Image"
      },
      "Linux": {
        "image": "Linux Image"
      }
    },
    "currentImage": "[variables('imageOS')[parameters('osType')].image]"
  },
  "resources": [],
  "outputs": {
    "result": {
      "type": "string",
      "value": "[variables('currentImage')]"
    }
  }
}
```

## <a name="use-recent-api-version"></a>Verwendung einer aktuellen API-Version

Testname: **apiVersions Should Be Recent**

Für jede Ressource muss eine aktuelle API-Version verwendet werden, die als Zeichenfolge hartcodiert ist. Der Test wertet die API-Version in Ihrer Vorlage anhand der Versionen des Ressourcenanbieters im Cache des Toolkits aus. Eine API-Version, die ab dem Datum der Testausführung weniger als zwei Jahre alt ist, gilt als aktuell. Verwenden Sie keine Vorschauversion, wenn eine neuere Version verfügbar ist.

Eine Warnung, dass keine API-Version gefunden wurde, zeigt nur an, dass die Version nicht im Cache des Toolkits enthalten ist. Wenn Sie die neueste Version einer API verwenden, was empfohlen wird, kann dies eine Warnung generieren.

Weitere Informationen zum [Toolkitcache](https://github.com/Azure/arm-ttk/tree/master/arm-ttk/cache).

Das folgende Beispiel **besteht den Test nicht**, weil die API-Version älter als zwei Jahre ist.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "storageaccount1",
    "location": "[parameters('location')]"
  }
]
```

Das folgende Beispiel **besteht den Test nicht**, weil eine Vorschauversion verwendet wird, obwohl eine neuere Version verfügbar ist.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2020-08-01-preview",
    "name": "storageaccount1",
    "location": "[parameters('location')]"
  }
]
```

Das folgende Beispiel **besteht den Test**, weil es sich um eine neue Version handelt, die keine Vorschauversion ist.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2021-02-01",
    "name": "storageaccount1",
    "location": "[parameters('location')]"
  }
]
```

## <a name="use-hard-coded-api-version"></a>Verwenden einer hartcodierten API-Version

Testname: **Providers apiVersions Is Not Permitted**

Die API-Version für einen Ressourcentyp bestimmt, welche Eigenschaften verfügbar sind. Stellen Sie in Ihrer Vorlage eine hartcodierte API-Version bereit. Rufen Sie keine API-Version ab, die während der Bereitstellung bestimmt wird, da Sie dann nicht wissen, welche Eigenschaften verfügbar sind.

Das folgende Beispiel **besteht den Test nicht**.

```json
"resources": [
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "[providers('Microsoft.Compute', 'virtualMachines').apiVersions[0]]",
    ...
  }
]
```

Das folgende Beispiel **besteht den Test**.

```json
"resources": [
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2020-12-01",
    ...
  }
]
```

## <a name="properties-cant-be-empty"></a>Keine leeren Eigenschaften

Testname: **Template Should Not Contain Blanks**

Eigenschaften dürfen nicht auf einen leeren Wert hartcodiert werden. Leere Werte sind NULL sowie leere Zeichenfolgen, Objekte oder Arrays. Wenn eine Eigenschaft auf einen leeren Wert festgelegt wird, entfernen Sie diese Eigenschaft aus der Vorlage. Sie können eine Eigenschaft während der Bereitstellung auf einen leeren Wert festlegen, z. B. über einen Parameter.

Die `template`-Eigenschaft in einer [geschachtelten Vorlage](linked-templates.md#nested-template) kann leere Eigenschaften enthalten. Weitere Informationen zu geschachtelten Vorlagen finden Sie unter [Microsoft.Resources deployments](/azure/templates/microsoft.resources/deployments).

Das folgende Beispiel **besteht den Test nicht**, weil leere Eigenschaften vorhanden sind.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2021-01-01",
    "name": "storageaccount1",
    "location": "[parameters('location')]",
    "sku": {},
    "kind": ""
  }
]
```

Das folgende Beispiel **besteht den Test**, weil die Eigenschaften Werte umfassen.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2021-01-01",
    "name": "storageaccount1",
    "location": "[parameters('location')]",
    "sku": {
      "name": "Standard_LRS",
      "tier": "Standard"
    },
    "kind": "Storage"
  }
]
```

## <a name="use-resource-id-functions"></a>Verwendung von Ressourcen-ID-Funktionen

Testname: **IDs Should Be Derived From ResourceIDs**

Verwenden Sie eine der Ressourcen-ID-Funktionen, wenn Sie eine Ressourcen-ID angeben. Folgende Funktionen sind zulässig:

- [Ressourcen-ID](template-functions-resource.md#resourceid)
- [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
- [tenantResourceId](template-functions-resource.md#tenantresourceid)
- [extensionResourceId](template-functions-resource.md#extensionresourceid)

Verwenden Sie nicht die concat-Funktion, um eine Ressourcen-ID zu erstellen. Das folgende Beispiel **besteht den Test nicht**.

```json
"networkSecurityGroup": {
    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroupName'))]"
}
```

Das nächste Beispiel **besteht den Test**.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

## <a name="resourceid-function-has-correct-parameters"></a>Korrekte Parameter für die resourceId-Funktion

Testname: **ResourceIds should not contain**

Verwenden Sie keine unnötigen Funktionen für optionale Parameter, wenn Sie Ressourcen-IDs generieren. Die [resourceId](template-functions-resource.md#resourceid)-Funktion verwendet standardmäßig das aktuelle Abonnement und die aktuelle Ressourcengruppe. Sie müssen diese Werte nicht angeben.

Im folgenden Beispiel **wird der Test nicht bestanden**, weil Sie die aktuelle Abonnement-ID und den aktuellen Ressourcengruppennamen nicht angeben müssen.

```json
"networkSecurityGroup": {
    "id": "[resourceId(subscription().subscriptionId, resourceGroup().name, 'Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

Das nächste Beispiel **besteht den Test**.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

Dieser Test gilt für Folgendes:

- [Ressourcen-ID](template-functions-resource.md#resourceid)
- [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
- [tenantResourceId](template-functions-resource.md#tenantresourceid)
- [extensionResourceId](template-functions-resource.md#extensionresourceid)
- [Referenz](template-functions-resource.md#reference)
- [list*](template-functions-resource.md#list)

Bei `reference` und `list*` ist der Test **nicht erfolgreich**, wenn Sie `concat` zum Erstellen der Ressourcen-ID verwenden.

## <a name="dependson-best-practices"></a>dependsOn best practices

Testname: **DependsOn Best Practices**

Verwenden Sie beim Festlegen der Bereitstellungsabhängigkeiten nicht die [if](template-functions-logical.md#if)-Funktion, um eine Bedingung zu testen. Wenn eine Ressource von einer Ressource abhängig ist, die [bedingt bereitgestellt](conditional-resource-deployment.md) wird, legen Sie die Abhängigkeit wie bei jeder beliebigen Ressource fest. Wenn eine bedingte Ressource nicht bereitgestellt wurde, entfernt Azure Resource Manager sie automatisch aus den erforderlichen Abhängigkeiten.

Das `dependsOn`-Element kann nicht mit einer [concat](template-functions-array.md#concat)-Funktion beginnen.

Das folgende Beispiel **besteht den Test nicht**, weil es eine `if`-Funktion enthält.

```json
"dependsOn": [
  "[if(equals(parameters('newOrExisting'),'new'), variables('storageAccountName'), '')]"
]
```

Das folgende Beispiel **besteht den Test nicht**, weil es mit `concat` beginnt.

```json
"dependsOn": [
  "[concat(variables('storageAccountName'))]"
]
```

Das folgende Beispiel **besteht den Test**.

```json
"dependsOn": [
  "[variables('storageAccountName')]"
]
```

## <a name="nested-or-linked-deployments-cant-use-debug"></a>Keine Verwendung von „debug“ für geschachtelte oder verknüpfte Bereitstellungen

Testname: **Deployment Resources Must Not Be Debug**

Wenn Sie eine [geschachtelte oder verknüpfte Vorlage](linked-templates.md) mit dem Ressourcentyp `Microsoft.Resources/deployments` definieren, können Sie das [Debuggen](/azure/templates/microsoft.resources/deployments#debugsetting-object) aktivieren. Debuggen wird verwendet, wenn Sie eine Vorlage testen müssen, aber vertrauliche Informationen verfügbar machen können. Bevor die Vorlage in der Produktion verwendet wird, deaktivieren Sie das Debuggen. Sie können das `debugSetting`-Objekt entfernen oder die `detailLevel`-Eigenschaft auf `none` ändern.

Das folgende Beispiel **besteht den Test nicht**.

```json
"debugSetting": {
  "detailLevel": "requestContent"
}
```

Das folgende Beispiel **besteht den Test**.

```json
"debugSetting": {
  "detailLevel": "none"
}
```

## <a name="admin-user-names-cant-be-literal-value"></a>Keine Literalwerte für Administratorbenutzernamen

Testname: **adminUsername Should Not Be A Literal**

Verwenden Sie beim Festlegen eines `adminUserName` keinen Literalwert. Erstellen Sie einen Parameter für den Benutzernamen, und verwenden Sie einen Ausdruck, um auf den Wert des Parameters zu verweisen.

Das folgende Beispiel **besteht den Test nicht** wegen eines Literalwerts.

```json
"osProfile":  {
  "adminUserName": "myAdmin"
}
```

Das folgende Beispiel **besteht den Test** wegen eines Ausdrucks.

```json
"osProfile": {
  "adminUsername": "[parameters('adminUsername')]"
}
```

## <a name="use-latest-vm-image"></a>Verwendung des aktuellen VM-Images

Testname: **VM Images Should Use Latest Version**

Dieser Test ist deaktiviert, aber die Ausgabe zeigt, dass er bestanden wurde. Die bewährte Methode besteht darin, Ihre Vorlage auf die folgenden Kriterien zu überprüfen:

Wenn Ihre Vorlage eine VM mit einem Image enthält, müssen Sie sicherstellen, dass es sich um die aktuelle Imageversion handelt.

## <a name="use-stable-vm-images"></a>Verwendung stabiler VM-Images

Testname: **Virtual Machines Should Not Be Preview**

Für VMs sollten keine Vorschauimages verwendet werden. Der Test Überprüft das `storageProfile`, um zu verifizieren, dass die `imageReference` keine Zeichenfolge verwendet, die _preview_ (Vorschau) enthält. Und dass _preview_ (Vorschau) nicht in den `imageReference`-Eigenschaften `offer`, `sku` oder `version` verwendet wird.

Weitere Informationen zur `imageReference`-Eigenschaft finden Sie unter [Microsoft.Compute virtualMachines](/azure/templates/microsoft.compute/virtualmachines#imagereference-object) und [Microsoft.Compute virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets#imagereference-object).

Das folgende Beispiel **besteht den Test nicht**, weil `imageReference` eine Zeichenfolge ist, die _preview_ enthält.

```json
"properties": {
  "storageProfile": {
    "imageReference": "latest-preview"
  }
}
```

Das folgende Beispiel **besteht den Test nicht**, wenn _preview_ in `offer`, `sku` oder `version` verwendet wird.

```json
"properties": {
  "storageProfile": {
    "imageReference": {
      "publisher": "Canonical",
      "offer": "UbuntuServer_preview",
      "sku": "16.04-LTS-preview",
      "version": "preview"
    }
  }
}
```

Das folgende Beispiel **besteht den Test**.

```json
"storageProfile": {
  "imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest"
  }
}
```

## <a name="dont-use-managedidentity-extension"></a>Keine Verwendung der ManagedIdentity-Erweiterung

Testname: **ManagedIdentityExtension must not be used**

Wenden Sie die `ManagedIdentity`-Erweiterung nicht auf eine VM an. Die Erweiterung wurde 2019 eingestellt und sollte nicht mehr verwendet werden.

## <a name="outputs-cant-include-secrets"></a>Keine Geheimnisse in Ausgaben

Testname: **Outputs Must Not Contain Secrets**

Fügen Sie dem Abschnitt `outputs` keine Werte hinzu, durch die möglicherweise Geheimnisse offengelegt werden. Zum Beispiel sichere Parameter vom Typ `secureString` oder `secureObject` bzw. [list*](template-functions-resource.md#list)-Funktionen wie `listKeys`.

Die Ausgabe einer Vorlage wird im Bereitstellungsverlauf gespeichert. Die Informationen können daher von einem böswilligen Benutzer gefunden werden.

Im folgenden Beispiel **wir der Test nicht bestanden**, weil ein Ausgabewert einen sicheren Parameter enthält.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "secureParam": {
      "type": "secureString"
    }
  },
  "functions": [],
  "variables": {},
  "resources": [],
  "outputs": {
    "badResult": {
      "type": "string",
      "value": "[concat('this is the value ', parameters('secureParam'))]"
    }
  }
}
```

Im folgenden Beispiel ist der Test **nicht erfolgreich**, weil eine [list*](template-functions-resource.md#list)-Funktion in den Ausgaben verwendet wird.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
      "type": "string"
    }
  },
  "functions": [],
  "variables": {},
  "resources": [],
  "outputs": {
    "badResult": {
      "type": "object",
      "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2021-02-01')]"
    }
  }
}
```

## <a name="use-protectedsettings-for-commandtoexecute-secrets"></a>Verwenden von „protectedSettings“ für „commandToExecute“-Geheimnisse

Testname: **„CommandToExecute“ muss „ProtectedSettings“ für Geheimnisse verwenden**

Verwenden Sie für Ressourcen vom Typ `CustomScript` die verschlüsselten `protectedSettings`, wenn `commandToExecute` Geheimnisdaten wie z. B. ein Kennwort enthält. Beispielsweise können Geheimnisdaten in sicheren Parametern des Typs `secureString` oder `secureObject`, in [list*](template-functions-resource.md#list)-Funktionen wie `listKeys` oder in benutzerdefinierten Skripts verwendet werden.

Verwenden Sie keine Geheimnisdaten im `settings`-Objekt, da dieses Klartext verwendet. Weitere Informationen finden Sie unter [Microsoft.Compute virtualMachines/extensions](/azure/templates/microsoft.compute/virtualmachines/extensions), [Windows](
/azure/virtual-machines/extensions/custom-script-windows) oder [Linux](../../virtual-machines/extensions/custom-script-linux.md).

Das folgende Beispiel **besteht den Test nicht**, weil der `settings` in einem sicheren Parameter `commandToExecute` verwendet.

```json
"parameters": {
  "adminPassword": {
    "type": "secureString"
  }
}
...
"properties": {
  "type": "CustomScript",
  "settings": {
    "commandToExecute": "[parameters('adminPassword')]"
  }
}
```

Das folgende Beispiel **besteht den Test nicht**, weil `settings` `commandToExecute` mit einer `listKeys`-Funktion verwendet.

```json
"properties": {
  "type": "CustomScript",
  "settings": {
    "commandToExecute": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2021-02-01')]"
  }
}
```

Das folgende Beispiel **besteht den Test**, weil `protectedSettings` `commandToExecute` mit einem sicheren Parameter verwendet.

```json
"parameters": {
  "adminPassword": {
    "type": "secureString"
  }
}
...
"properties": {
  "type": "CustomScript",
  "protectedSettings": {
    "commandToExecute": "[parameters('adminPassword')]"
  }
}
```

Das folgende Beispiel **besteht den Test**, weil `protectedSettings` `commandToExecute` mit einer `listKeys`-Funktion verwendet.

```json
"properties": {
  "type": "CustomScript",
  "protectedSettings": {
    "commandToExecute": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2021-02-01')]"
  }
}
```

## <a name="use-recent-api-versions-in-reference-functions"></a>Verwenden aktueller API-Versionen in Referenzfunktionen

Testname: **apiVersions Should Be Recent in Reference Functions** (apiVersions sollten aktuell in Referenzfunktionen sein)

Die API-Version, die in einer [reference](template-functions-resource.md#reference)-Funktion verwendet wird, muss aktuell sein und darf keine Vorschauversion sein. Der Test wertet die API-Version in Ihrer Vorlage anhand der Versionen des Ressourcenanbieters im Cache des Toolkits aus. Eine API-Version, die ab dem Datum der Testausführung weniger als zwei Jahre alt ist, gilt als aktuell.

Eine Warnung, dass keine API-Version gefunden wurde, zeigt nur an, dass die Version nicht im Cache des Toolkits enthalten ist. Wenn Sie die neueste Version einer API verwenden, was empfohlen wird, kann dies eine Warnung generieren.

Weitere Informationen zum [Toolkitcache](https://github.com/Azure/arm-ttk/tree/master/arm-ttk/cache).

Das folgende Beispiel **besteht den Test nicht**, weil die API-Version älter als zwei Jahre ist.

```json
"outputs": {
  "stgAcct": {
    "type": "string",
    "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2019-06-01')]"
  }
}
```

Das folgende Beispiel **besteht den Test nicht**, weil die API-Version eine Vorschauversion ist.

```json
"outputs": {
  "stgAcct": {
    "type": "string",
    "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2020-08-01-preview')]"
  }
}
```

Das folgende Beispiel **besteht den Test**, weil die API-Version weniger als zwei Jahre alt und keine Vorschauversion ist.

```json
"outputs": {
  "stgAcct": {
    "type": "string",
    "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2021-02-01')]"
  }
}
```

## <a name="use-type-and-name-in-resourceid-functions"></a>Verwenden von Typ und Name in resourceId-Funktionen

Testname: **Resources Should Not Be Ambiguous** (Ressourcen sollten nicht mehrdeutig sein)

Dieser Test ist deaktiviert, aber die Ausgabe zeigt, dass er bestanden wurde. Die bewährte Methode besteht darin, Ihre Vorlage auf die folgenden Kriterien zu überprüfen:

Eine [resourceId](template-functions-resource.md#resourceid) muss einen Ressourcentyp und einen Ressourcennamen enthalten. Dieser Test sucht alle `resourceId`-Funktionen der Vorlage und überprüft, ob die Ressource in der Vorlage mit der richtigen Syntax verwendet wird. Andernfalls gilt die Funktion als mehrdeutig.

Beispielsweise gilt eine `resourceId`-Funktion als mehrdeutig:

- Wenn keine Ressource in der Vorlage gefunden wird und keine Ressourcengruppe angegeben ist.
- Wenn eine Ressource eine Bedingung enthält und keine Ressourcengruppe angegeben ist.
- Wenn eine verwandte Ressource einige, aber nicht alle Namenssegmente enthält. Eine untergeordnete Ressource enthält beispielsweise mehr als ein Namenssegment. Weitere Informationen finden Sie unter [resourceId Hinweise](template-functions-resource.md#remarks-3).

## <a name="use-inner-scope-for-nested-deployment-secure-parameters"></a>Verwenden des inneren Bereichs für sichere Parameter bei geschachtelter Bereitstellung

Testname: **Secure Params In Nested Deployments** (Sichere Parameter in geschachtelten Bereitstellungen)

Verwenden Sie das Objekt `expressionEvaluationOptions` der geschachtelten Vorlage mit dem Bereich `inner`, um Ausdrücke auszuwerten, die sichere Parameter vom Typ `secureString` oder `secureObject` bzw. [list*](template-functions-resource.md#list)-Funktionen wie `listKeys` enthalten. Wenn der Bereich `outer` verwendet wird, werden Ausdrücke im Bereich der übergeordneten Vorlage als Klartext ausgewertet. Der sichere Wert ist dann für alle Benutzer sichtbar, die Zugriff auf den Bereitstellungsverlauf haben. Der Standardwert von `expressionEvaluationOptions` ist `outer`.

Weitere Informationen zu geschachtelten Vorlagen finden Sie unter [Microsoft.Resources deployments](/azure/templates/microsoft.resources/deployments) und [Auswertungsbereich von Ausdrücken in geschachtelten Vorlagen](linked-templates.md#expression-evaluation-scope-in-nested-templates).

Das folgende Beispiel **besteht den Test nicht**, weil `expressionEvaluationOptions` den Bereich `outer` zum Auswerten sicherer Parameter oder `list*`-Funktionen verwendet.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2021-04-01",
    "name": "nestedTemplate",
    "properties": {
      "expressionEvaluationOptions": {
        "scope": "outer"
      }
    }
  }
]
```

Das folgende Beispiel **besteht den Test**, weil `expressionEvaluationOptions` den Bereich `inner` zum Auswerten sicherer Parameter oder von `list*`-Funktionen verwendet.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2021-04-01",
    "name": "nestedTemplate",
    "properties": {
      "expressionEvaluationOptions": {
        "scope": "inner"
      }
    }
  }
]
```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Ausführen des Testtoolkits finden Sie unter [Verwenden des Resource Manager-Vorlagen-Testtoolkits](test-toolkit.md).
- Ein Microsoft Learn-Modul, das die Verwendung des Testtoolkits behandelt, finden Sie unter [Anzeigen einer Vorschau von Änderungen und Überprüfen von Azure-Ressourcen mithilfe von „Was-wäre-wenn“ und dem ARM-Vorlagen-Testtoolkit](/learn/modules/arm-template-test/).
- Informationen zum Testen von Parameterdateien finden Sie unter [Testfälle für Parameterdateien](parameters.md).
- „createUiDefinition“-Tests finden Sie unter [Testfälle für „createUiDefinition.json“](createUiDefinition-test-cases.md).
- Informationen zu Tests für alle Dateien finden Sie unter [Testfälle für alle Dateien](all-files-test-cases.md).
