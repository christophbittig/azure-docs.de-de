---
title: Bicep-Accessoroperatoren
description: Beschreibt die Bicep-Zugriffsoperatoren für Ressourcen und Eigenschaften.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/10/2021
ms.openlocfilehash: f3ed1ca46c2dfbcab8bfbdc720ff7ab448cbc742
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124744443"
---
# <a name="bicep-accessor-operators"></a>Bicep-Accessoroperatoren

Die Accessoroperatoren werden verwendet, um auf untergeordnete Ressourcen, Eigenschaften von Objekten und Elemente in einem Array zuzugreifen. Sie können den Eigenschaftenaccessor auch verwenden, um einige Funktionen zu verwenden.

| Operator | Name |
| ---- | ---- |
| `[]` | [Indexaccessor](#index-accessor) |
| `.`  | [Funktionsaccessor](#function-accessor) |
| `::` | [Accessor für geschachtelte Ressourcen](#nested-resource-accessor) |
| `.`  | [Eigenschaftenaccessor](#property-accessor) |

## <a name="index-accessor"></a>Indexaccessor

`array[integerIndex]`

`object['stringIndex']`

Verwenden Sie den Indexaccessor, um entweder ein Element aus einem Array oder eine Eigenschaft aus einem Objekt abzurufen.

Für ein **Array** geben Sie den Index als **ganze Zahl** an. Die ganze Zahl entspricht der auf Null basierenden Position des abzurufenden Elements.

Für ein **Objekt** geben Sie den Index als **Zeichenfolge** an. Die Zeichenfolge entspricht dem Namen des abzurufenden Objekts.

Das folgende Beispiel ruft ein Element in einem Array ab.

```bicep
var arrayVar = [
  'Coho'
  'Contoso'
  'Fabrikan'
]

output accessorResult string = arrayVar[1]
``` 

Ausgabe des Beispiels:

| Name | Type | Wert |
| ---- | ---- | ---- |
| accessorResult | Zeichenfolge | „Contoso“ |

Das nächste Beispiel ruft eine Eigenschaft für ein Objekt ab.

```bicep
var environmentSettings = {
  dev: {
    name: 'Development'
  }
  prod: {
    name: 'Production'
  }
}

output accessorResult string = environmentSettings['dev'].name
```

Ausgabe des Beispiels:

| Name | Type | Wert |
| ---- | ---- | ---- |
| accessorResult | Zeichenfolge | „Development“ |

## <a name="function-accessor"></a>Funktionsaccessor

`resourceName.functionName()`

Zwei Funktionen, [getSecret](bicep-functions-resource.md#getsecret) und [list*](bicep-functions-resource.md#list), unterstützen den Accessoroperator zum Aufrufen der Funktion. Diese beiden Funktionen sind die einzigen Funktionen, die den Accessoroperator unterstützen.

### <a name="example"></a>Beispiel

Das folgende Beispiel verweist auf einen vorhandenen Schlüsseltresor und verwendet dann `getSecret`, um ein Geheimnis an ein Modul zu übergeben.

```bicep
resource kv 'Microsoft.KeyVault/vaults@2019-09-01' existing = {
  name: kvName
  scope: resourceGroup(subscriptionId, kvResourceGroup )
}

module sql './sql.bicep' = {
  name: 'deploySQL'
  params: {
    sqlServerName: sqlServerName
    adminLogin: adminLogin
    adminPassword: kv.getSecret('vmAdminPassword')
  }
}
```

## <a name="nested-resource-accessor"></a>Accessor für geschachtelte Ressourcen

`parentResource::nestedResource`

Eine geschachtelte Ressource ist eine Ressource, die in einer anderen Ressource deklariert wird. Verwenden Sie den Accessor für geschachtelte Ressourcen (`::`), um von außerhalb der übergeordneten Ressource auf diese geschachtelten Ressourcen zuzugreifen.

Innerhalb der übergeordneten Ressource verweisen Sie lediglich mit dem symbolischen Namen auf die geschachtelte Ressource. Sie müssen den Accessor für geschachtelte Ressourcen nur verwenden, wenn Sie von außerhalb der übergeordneten Ressource auf die geschachtelte Ressource verweisen.

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt, wie Sie innerhalb der übergeordneten Ressource und von außerhalb der übergeordneten Ressource auf eine geschachtelte Ressource verweisen.

```bicep
resource demoParent 'demo.Rp/parentType@2020-01-01' = {
  name: 'demoParent'
  location: 'West US'

  // Declare a nested resource within 'demoParent'
  resource demoNested 'childType' = {
    name: 'demoNested'
    properties: {
      displayName: 'The nested instance.'
    }
  }

  // Declare another nested resource
  resource demoSibling 'childType' = {
    name: 'demoSibling'
    properties: {
      // Use symbolic name to reference because this line is within demoParent
      displayName: 'Sibling of ${demoNested.properties.displayName}'
    }
  }
}

// Use nested accessor to reference because this line is outside of demoParent
output displayName string = demoParent::demoNested.properties.displayName
```

## <a name="property-accessor"></a>Eigenschaftenaccessor

`objectName.propertyName`

Verwenden Sie Eigenschaftenaccessoren, um auf Eigenschaften eines Objekts zuzugreifen. Eigenschaftenaccessoren können mit jedem Objekt verwendet werden, einschließlich Parametern und Variablen, die Objekte sind. Sie erhalten einen Fehler, wenn Sie den Eigenschaftenaccessor mit einem Ausdruck verwenden, der kein Objekt ist.

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt eine Objektvariable und wie auf die Eigenschaften zugegriffen wird.

```bicep
var x = {
  y: {
    z: 'Hello'
    a: true
  }
  q: 42
}

output outputZ string = x.y.z
output outputQ int = x.q
```

Ausgabe des Beispiels:

| Name | Type | Wert |
| ---- | ---- | ---- |
| `outputZ` | Zeichenfolge | 'Hello' |
| `outputQ` | integer | 42 |

In der Regel verwenden Sie den Eigenschaftenaccessor mit einer in der Bicep-Datei bereitgestellten Ressource. Im folgenden Beispiel wird eine öffentliche IP-Adresse erstellt und Eigenschaftenaccessoren verwendet, um einen Wert aus der bereitgestellten Ressource zurückzugeben.

```bicep
resource publicIp 'Microsoft.Network/publicIPAddresses@2020-06-01' = {
  name: publicIpResourceName
  location: location
  properties: {
    publicIPAllocationMethod: dynamicAllocation ? 'Dynamic' : 'Static'
    dnsSettings: {
      domainNameLabel: publicIpDnsLabel
    }
  }
}

// Use property accessor to get value
output ipFqdn string = publicIp.properties.dnsSettings.fqdn
```

## <a name="next-steps"></a>Nächste Schritte

- Verwenden Sie zum Ausführen der Beispiele die Azure CLI oder Azure PowerShell, um [eine Bicep-Datei bereitzustellen](./quickstart-create-bicep-use-visual-studio-code.md#deploy-the-bicep-file).
- Informationen zum Erstellen einer Bicep-Datei finden Sie unter [Schnellstart: Erstellen von Bicep-Dateien mit Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
- Informationen zum Beheben von Bicep-Typfehlern finden Sie unter [any-Funktion für Bicep](./bicep-functions-any.md).
