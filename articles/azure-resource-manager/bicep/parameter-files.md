---
title: Erstellen einer Parameterdatei für Bicep
description: Erstellen einer Parameterdatei zum Übergeben von Werten während der Bereitstellung einer Bicep-Datei
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/16/2021
ms.openlocfilehash: 4628b7d6a04bdec2a7ec4273536bf895dc23a5d9
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2021
ms.locfileid: "112292229"
---
# <a name="create-bicep-parameter-file"></a>Erstellen einer Bicep-Parameterdatei

Anstatt Parameter als Inlinewerte in Ihrem Skript zu übergeben, können Sie eine JSON-Datei verwenden, die die Parameterwerte enthält. In diesem Artikel erfahren Sie, wie Sie eine Parameterdatei erstellen, die Sie mit einer Bicep-Datei verwenden.

## <a name="parameter-file"></a>Parameterdatei

Eine Parameterdatei verwendet das folgende Format:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "value": "<second-value>"
    }
  }
}
```

Beachten Sie, dass die Parameterdatei Parameterwerte als Nur-Text speichert. Diese Vorgehensweise funktioniert bei Werten, die nicht sensibler Art sind, z. B. bei einer SKU-Ressource. Nur-Text ist nicht für sensible Werte, z. B. Kennwörter, geeignet. Wenn Sie einen Parameter übergeben müssen, der einen vertraulichen Wert enthält, speichern Sie den Wert in einem Schlüsseltresor. Anstatt den vertraulichen Wert Ihrer Parameterdatei hinzuzufügen, sollten Sie ihn mit der [Funktion „getSecret“](bicep-functions-resource.md#getsecret) abrufen. Weitere Informationen finden Sie unter [Verwenden von Azure Key Vault zum Übergeben eines sicheren Parameterwerts während der Bicep-Bereitstellung](key-vault-parameter.md).

## <a name="define-parameter-values"></a>Definieren von Parameterwerten

Um zu bestimmen, wie die Parameternamen und -werte definiert werden, öffnen Sie Ihre Bicep-Datei. Sehen Sie sich den Parameterabschnitt der Bicep-Datei an. Die folgenden Beispiele zeigen die Parameter aus einer Bicep-Datei.

```bicep
@maxLength(11)
param storagePrefix string

@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
param storageAccountType string = 'Standard_LRS'
```

In der Parameterdatei ist das erste Detail der Name der einzelnen Parameter. Der Parameternamen in der Parameterdatei müssen mit den Parameternamen in der Bicep-Datei identisch sein.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
    },
    "storageAccountType": {
    }
  }
}
```

Beachten Sie die Parametertypskala. Die Parametertypen in ihrer Parameterdatei müssen die gleichen Typen wie Ihre Bicep-Datei verwenden. In diesem Beispiel sind beide Parametertypen Zeichenfolgen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": ""
    },
    "storageAccountType": {
      "value": ""
    }
  }
}
```

Überprüfen Sie die Bicep-Datei auf Parameter mit einem Standardwert. Wenn ein Parameter über einen Standardwert verfügt, können Sie einen Wert in der Parameterdatei angeben, aber er ist nicht erforderlich. Der Wert der Parameterdatei überschreibt den Standardwert der Bicep-Datei.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "" // This value must be provided.
    },
    "storageAccountType": {
      "value": "" // This value is optional. Bicep will use default value if not provided.
    }
  }
}
```

Überprüfen Sie die zulässigen Werte der Bicep-Datei und alle Einschränkungen, z. B. die maximale Länge. Diese Werte geben den Wertebereich an, den Sie für einen Parameter angeben können. In diesem Beispiel kann`storagePrefix` maximal 11 Zeichen enthalten und `storageAccountType` muss einen zulässigen Wert angeben.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "storage"
    },
    "storageAccountType": {
      "value": "Standard_ZRS"
    }
  }
}
```

> [!NOTE]
> Ihre Parameterdatei kann nur Werte für Parameter enthalten, die in der Bicep-Datei definiert sind. Wenn die Parameterdatei zusätzliche Parameter enthält, die nicht mit den Parametern in der Bicep-Datei identisch sind, erhalten Sie eine Fehlermeldung.

## <a name="parameter-type-formats"></a>Parametertypformate

Das folgende Beispiel zeigt die Formate verschiedener Parametertypen: Zeichenfolge, ganze Zahl, Boolean, Array und Objekt.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "exampleString": {
      "value": "test string"
    },
    "exampleInt": {
      "value": 4
    },
    "exampleBool": {
      "value": true
    },
    "exampleArray": {
      "value": [
        "value 1",
        "value 2"
      ]
    },
    "exampleObject": {
      "value": {
        "property1": "value1",
        "property2": "value2"
      }
    }
  }
}
```

## <a name="deploy-bicep-file-with-parameter-file"></a>Bereitstellen der Bicep-Datei mit der Parameterdatei

Übergeben Sie in der Azure CLI eine lokale Parameterdatei mit `@` und dem Namen der Parameterdatei. Beispiel: `@storage.parameters.json`.

```azurecli
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.bicep \
  --parameters @storage.parameters.json
```

Weitere Informationen finden Sie unter [Bereitstellen von Ressourcen mit Bicep und der Azure CLI](./deploy-cli.md#parameters). Zum Bereitstellen von _.bicep_-Dateien benötigen Sie Version 2.20 oder höher der Azure CLI.

Übergeben Sie in Azure PowerShell eine lokale Parameterdatei, indem Sie den Parameter `TemplateParameterFile` verwenden.

```azurepowershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile C:\MyTemplates\storage.bicep `
  -TemplateParameterFile C:\MyTemplates\storage.parameters.json
```

Weitere Informationen finden Sie unter [Bereitstellen von Ressourcen mit Bicep und Azure PowerShell](./deploy-powershell.md#parameters). Zum Bereitstellen von BICEP-Dateien benötigen Sie _Version 5.6.0 oder höher von Azure PowerShell_.

## <a name="file-name"></a>Dateiname

Die allgemeine Konvention für das Benennen der Parameterdatei ist das Hinzufügen von _Parametern_ zum Namen der Bicep-Datei. Wenn Ihre Bicep-Datei beispielsweise den Namen _azuredeploy.bicep_ hat, ergibt sich für Ihre Parameterdatei der Name _azuredeploy.parameters.json_. Anhand dieser Namenskonvention können Sie die Verbindung zwischen der Bicep-Datei und den Parametern erkennen.

Erstellen Sie mehr als eine Parameterdatei, wenn Sie die Bereitstellung in unterschiedlichen Umgebungen durchführen möchten. Wenn Sie die Parameterdateien benennen, identifizieren Sie deren Verwendung, z. B. Entwicklung und Produktion. Verwenden Sie beispielsweise _azuredeploy.parameters-dev.json_ und _azuredeploy.parameters-prod.json_, um Ressourcen bereitzustellen.

## <a name="parameter-precedence"></a>Parameterrangfolge

Sie können Inlineparameter und eine lokale Parameterdatei im selben Bereitstellungsvorgang verwenden. Sie können beispielsweise einige Werte in der lokalen Parameterdatei angeben und weitere Werte während der Bereitstellung inline hinzufügen. Wenn Sie Werte für einen Parameter sowohl in der lokalen Parameterdatei als auch inline bereitstellen, haben die Inlinewerte Vorrang.

Sie können eine externe Parameterdatei verwenden, indem Sie den URI der Datei bereitstellen. Bei Verwendung einer externen Parameterdatei können Sie keine anderen Werte (weder inline noch aus einer lokalen Datei) übergeben. Alle Inlineparameter werden ignoriert. Stellen Sie alle Parameterwerte in der externen Datei bereit.

## <a name="parameter-name-conflicts"></a>Parameternamenskonflikte

Wenn Ihre Bicep-Datei einen Parameter enthält, der denselben Namen besitzt wie einer der Parameter des PowerShell-Befehls, zeigt PowerShell den Parameter der Bicep-Datei mit dem Postfix `FromTemplate` an. Beispiel: Ein Parameter namens `ResourceGroupName` in Ihrer Bicep-Datei verursacht einen Konflikt mit dem Parameter `ResourceGroupName` im Cmdlet [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Sie werden zur Eingabe eines Werts für `ResourceGroupNameFromTemplate` aufgefordert. Verwenden Sie Parameternamen, die nicht für Bereitstellungsbefehle verwendet werden, um diese Verwechslung zu vermeiden.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Definieren von Parametern in einer Bicep-Datei finden Sie unter [Parameter in Bicep](./parameters.md).
- Informationen zum Abrufen von vertraulichen Werten finden Sie unter [Verwenden von Azure Key Vault zum Übergeben eines sicheren Parameterwerts während der Bereitstellung](./key-vault-parameter.md).
