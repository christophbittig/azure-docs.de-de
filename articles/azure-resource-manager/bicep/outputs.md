---
title: Ausgaben in Bicep
description: Beschreibt, wie Sie Ausgabevariablen in Bicep definieren.
ms.topic: conceptual
ms.date: 11/12/2021
ms.openlocfilehash: 4ed640cd639b7a1b69cd07ae8ac50a4ddff436fb
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491315"
---
# <a name="outputs-in-bicep"></a>Ausgaben in Bicep

Dieser Artikel beschreibt, wie Sie in einer Bicep-Datei Ausgabewerte definieren. Sie verwenden Ausgaben, wenn Werte von den bereitgestellten Ressourcen zurückgegeben werden müssen.

## <a name="define-output-values"></a>Definieren von Ausgabewerten

Die Syntax zum Definieren eines Ausgabewerts lautet:

```bicep
output <name> <data-type> = <value>
```

Eine Ausgabe darf nicht denselben Namen wie ein Parameter, eine Variable, ein Modul oder eine Ressource haben. Jeder Ausgabewert muss sich zu einem der [Datentypen](data-types.md) auflösen lassen.

Im folgenden Beispiel wird gezeigt, wie Sie eine Eigenschaft von einer bereitgestellten Ressource zurückgeben lassen. Im Beispiel ist `publicIP` der symbolische Name einer öffentlichen IP-Adresse, die in der Bicep-Datei bereitgestellt wird. Der Ausgabewert erhält den vollqualifizierten Domänennamen für die öffentliche IP-Adresse.

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

Im nächsten Beispiel wird veranschaulicht, wie Ausgaben verschiedener Typen zurückgegeben werden.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/outputs/output.bicep":::

Wenn Sie eine Eigenschaft ausgeben müssen, die einen Bindestrich im Namen aufweist, setzen Sie den Namen in eckige Klammern, anstatt die Punktnotation zu verwenden. Verwenden Sie beispielsweise `['property-name']` anstelle von `.property-name`.

```bicep
var user = {
  'user-name': 'Test Person'
}

output stringOutput string = user['user-name']
```

## <a name="conditional-output"></a>Bedingte Ausgabe

Wenn der zurückzugebende Wert von einer Bedingung in der Bereitstellung abhängt, verwenden Sie den `?`-Operator.

```bicep
output <name> <data-type> = <condition> ? <true-value> : <false-value>
```

In der Regel verwenden Sie eine bedingte Ausgabe, wenn Sie eine Ressource [bedingt bereitgestellt](conditional-resource-deployment.md) haben. Das folgende Beispiel zeigt, wie die Ressourcen-ID für eine öffentliche IP-Adresse abhängig davon zurückgegeben wird, ob eine neue Ressourcen-ID bereitgestellt wurde.

Um eine bedingte Ausgabe in Bicep anzugeben, verwenden Sie den `?`-Operator. Im folgenden Beispiel wird entweder eine Endpunkt-URL oder eine leere Zeichenfolge zurückgegeben, abhängig von einer Bedingung.

```bicep
param deployStorage bool = true
param storageName string
param location string = resourceGroup().location

resource myStorageAccount 'Microsoft.Storage/storageAccounts@2019-06-01' = if (deployStorage) {
  name: storageName
  location: location
  kind: 'StorageV2'
  sku:{
    name:'Standard_LRS'
    tier: 'Standard'
  }
  properties: {
    accessTier: 'Hot'
  }
}

output endpoint string = deployStorage ? myStorageAccount.properties.primaryEndpoints.blob : ''
```

## <a name="dynamic-number-of-outputs"></a>Dynamische Anzahl von Ausgaben

In einigen Szenarien wissen Sie nicht, wie viele Instanzen eines Werts beim Erstellen der Vorlage zurückgegeben werden müssen. Sie können eine variable Anzahl von Werten zurückgeben, indem Sie den `for`-Ausdruck verwenden.

```bicep
output <name> <data-type> = [for <item> in <collection>: {
  ...
}]
```

Im folgenden Beispiel wird ein Array durchlaufen.

```bicep
param nsgLocation string = resourceGroup().location
param orgNames array = [
  'Contoso'
  'Fabrikam'
  'Coho'
]

resource nsg 'Microsoft.Network/networkSecurityGroups@2020-06-01' = [for name in orgNames: {
  name: 'nsg-${name}'
  location: nsgLocation
}]

output deployedNSGs array = [for (name, i) in orgNames: {
  orgName: name
  nsgName: nsg[i].name
  resourceId: nsg[i].id
}]
```

Weitere Informationen zu Schleifen finden Sie unter [Iterative Schleifen in Bicep](loops.md).

## <a name="outputs-from-modules"></a>Ausgaben von Modulen

Um von einem Modul einen Ausgabewert zu erhalten, verwenden Sie die folgende Syntax:

```bicep
<module-name>.outputs.<property-name>
```

Das folgende Beispiel zeigt, wie Sie die IP-Adresse für einen Lastenausgleich festlegen, indem Sie einen Wert aus einem Modul abrufen. Der Name des Moduls lautet `publicIP`.

```bicep
publicIPAddress: {
  id: publicIP.outputs.resourceID
}
```

## <a name="get-output-values"></a>Abrufen von Ausgabewerten

Bei erfolgreicher Bereitstellung werden die Ausgabewerte automatisch in den Ergebnissen der Bereitstellung zurückgegeben.

Sie können ein Azure CLI- oder Azure PowerShell-Skript verwenden, um Ausgabewerte aus dem Bereitstellungsverlauf abzurufen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
az deployment group show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu den verfügbaren Eigenschaften für Ausgaben finden Sie unter [Grundlegendes zur Struktur und Syntax von Bicep](./file.md).
