---
title: Deklarieren von Ressourcen in Bicep
description: Hier wird beschrieben, wie Ressourcen für die Bereitstellung in Bicep deklariert werden.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 10/07/2021
ms.openlocfilehash: 4b3b355016057af00c361a118aed2728948768dd
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129659619"
---
# <a name="resource-declaration-in-bicep"></a>Ressourcendeklaration in Bicep

Um eine Ressource über eine Bicep-Datei bereitzustellen, fügen Sie mithilfe des Schlüsselworts `resource` eine Ressourcendeklaration hinzu.

## <a name="set-resource-type-and-version"></a>Festlegen von Ressourcentyp und Version

Wenn Sie Ihrer Bicep-Datei eine Ressource hinzufügen, legen Sie zunächst den Ressourcentyp und die API-Version fest. Diese Werte bestimmen die anderen Eigenschaften, die für die Ressource verfügbar sind.

Im folgenden Beispiel wird gezeigt, wie der Ressourcentyp und die API-Version für ein Speicherkonto festgelegt werden. Das Beispiel zeigt nicht die vollständige Ressourcendeklaration.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  ...
}
```

Sie legen einen symbolischen Namen für die Ressource fest. Im vorherigen Beispiel lautet der symbolische Name `stg`.  Der symbolische Name ist nicht mit dem Ressourcennamen identisch. Sie verwenden den symbolischen Namen, um in anderen Teilen der Bicep-Datei auf die Ressource zu verweisen. Bei symbolischen Namen wird zwischen Groß- und Kleinschreibung unterschieden.  Sie dürfen Buchstaben, Zahlen und _; enthalten, jedoch nicht mit einer Zahl beginnen.

Das `apiProfile`-Element wird von Bicep nicht unterstützt. Dieses Element ist in [ARM-Vorlagen-JSON-Dateien (Azure Resource Manager)](../templates/syntax.md) verfügbar.

## <a name="set-resource-name"></a>Festlegen des Ressourcennamens

Jede Ressource besitzt einen Namen. Achten Sie beim Festlegen des Ressourcennamens auf die [Regeln und Einschränkungen für Ressourcennamen](../management/resource-name-rules.md).

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'examplestorage'
  ...
}
```

In der Regel legen Sie den Namen auf einen Parameter fest, damit Sie während der Bereitstellung unterschiedliche Werte übergeben können.

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  ...
}
```

## <a name="set-location"></a>Legen Sie einen Speicherort fest

Viele Ressourcen erfordern einen Speicherort. Sie können mittels IntelliSense oder über eine [Vorlagenreferenz](/azure/templates/) ermitteln, ob die Ressource einen Speicherort benötigt. Im folgenden Beispiel wird ein Speicherortparameter hinzugefügt, der für das Speicherkonto verwendet wird.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'examplestorage'
  location: 'eastus'
  ...
}
```

In der Regel legen Sie den Standort auf einen Parameter fest, damit die Bereitstellung an verschiedenen Standorten erfolgen kann.

```bicep
param location string = resourceGroup().location

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'examplestorage'
  location: location
  ...
}
```

An verschiedenen Speicherorten werden unterschiedliche Ressourcentypen unterstützt. Informationen zum Abrufen der unterstützten Standorte für einen Azure-Dienst finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/).  Verwenden Sie Azure PowerShell oder die Azure-Befehlszeilenschnittstelle, um die unterstützten Speicherorte für einen Ressourcentyp abzurufen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes `
  | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
az provider show \
  --namespace Microsoft.Batch \
  --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" \
  --out table
```

---

## <a name="set-tags"></a>Festlegen von Tags

Sie können während der Bereitstellung Tags auf eine Ressource anwenden. Tags helfen Ihnen dabei, Ihre bereitgestellten Ressourcen logisch zu organisieren. Beispiele für die verschiedenen Methoden zum Angeben der Tags finden Sie unter [ARM-Vorlagen-Tags](../management/tag-resources.md#arm-templates).

## <a name="set-managed-identities-for-azure-resources"></a>Festlegen verwalteter Identitäten für Azure-Ressourcen

Einige Ressourcen unterstützen [verwaltete Identitäten für Azure-Ressourcen](../../active-directory/managed-identities-azure-resources/overview.md). Diese Ressourcen verfügen über ein Identitätsobjekt auf der Stammebene der Ressourcendeklaration.

Sie können entweder systemseitig oder benutzerseitig zugewiesene Identitäten verwenden.

Das folgende Beispiel zeigt, wie sie eine systemseitig zugewiesene Identität für einen Azure Kubernetes Service-Cluster konfigurieren.

```bicep
resource aks 'Microsoft.ContainerService/managedClusters@2020-09-01' = {
  name: clusterName
  location: location
  tags: tags
  identity: {
    type: 'SystemAssigned'
  }
```

Im nächsten Beispiel wird gezeigt, wie Sie eine benutzerseitig zugewiesene Identität für einen virtuellen Computer konfigurieren.

```bicep
param userAssignedIdentity string

resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  name: vmName
  location: location
  identity: {
    type: 'UserAssigned'
    userAssignedIdentities: {
      '${userAssignedIdentity}': {}
    }
  }
```

## <a name="set-resource-specific-properties"></a>Festlegen ressourcenspezifischer Eigenschaften

Die vorstehenden Eigenschaften sind für die meisten Ressourcentypen generisch. Nachdem Sie diese Werte festgelegt haben, müssen Sie die Eigenschaften festlegen, die für den Ressourcentyp, den Sie bereitstellen, spezifisch sind.

Verwenden Sie IntelliSense oder eine [Vorlagenreferenz](/azure/templates/), um zu bestimmen, welche Eigenschaften verfügbar sind und welche erforderlich sind. Im folgenden Beispiel werden die restlichen Eigenschaften für ein Speicherkonto festgelegt.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'examplestorage'
  location: 'eastus'
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}
```

## <a name="set-resource-dependencies"></a>Festlegen von Ressourcenabhängigkeiten

Beim Bereitstellen einer Ressource müssen Sie möglicherweise sicherstellen, dass andere Ressourcen bereits vorhanden sind. So benötigen Sie beispielsweise einen logischen SQL-Server, bevor Sie eine Datenbank bereitstellen. Diese Beziehung richten Sie ein, indem Sie eine Ressource als von einer anderen Ressource abhängig kennzeichnen. Es gibt zwei Möglichkeiten, die Reihenfolge der Ressourcenbereitstellung zu beeinflussen: durch die [implizite Abhängigkeit](#implicit-dependency) und die [explizite Abhängigkeit](#explicit-dependency).

Azure Resource Manager wertet die Abhängigkeiten zwischen den Ressourcen aus und stellt sie in der Reihenfolge ihrer Abhängigkeiten bereit. Wenn Ressourcen nicht voneinander abhängig sind, stellt Resource Manager sie parallel bereit. Sie müssen nur Abhängigkeiten für Ressourcen definieren, die in der gleichen Bicep-Datei bereitgestellt werden.

### <a name="implicit-dependency"></a>Implizite Abhängigkeit

Eine implizite Abhängigkeit wird erstellt, wenn eine Ressourcendeklaration auf eine andere Ressource in derselben Bereitstellung verweist. Im folgenden Beispiel wird auf *dnsZone* beispielsweise durch die zweite Ressourcendefinition verwiesen:

```bicep
resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = {
  name: 'myZone'
  location: 'global'
}

resource otherResource 'Microsoft.Example/examples@2020-06-01' = {
  name: 'exampleResource'
  properties: {
    // get read-only DNS zone property
    nameServers: dnsZone.properties.nameServers
  }
}
```

Eine geschachtelte Ressource verfügt ebenfalls über eine implizite Abhängigkeit von ihrer enthaltenden Ressource.

```bicep
resource myParent 'My.Rp/parentType@2020-01-01' = {
  name: 'myParent'
  location: 'West US'

  // depends on 'myParent' implicitly
  resource myChild 'childType' = {
    name: 'myChild'
  }
}
```

Wenn eine implizite Abhängigkeit besteht, **fügen Sie keine explizite Abhängigkeit hinzu**.

Weitere Informationen zu geschachtelten Ressourcen finden Sie unter [Festlegen von Name und Typ für untergeordnete Ressourcen in Bicep](./child-resource-name-type.md).

### <a name="explicit-dependency"></a>Explizite Abhängigkeit

Eine explizite Abhängigkeit wird mit der `dependsOn`-Eigenschaft deklariert. Die Eigenschaft akzeptiert ein Array von Ressourcenbezeichnern, sodass Sie mehr als eine Abhängigkeit angeben können.

Das folgende Beispiel zeigt eine DNS-Zone namens `otherZone`, die von einer DNS-Zone namens `dnsZone` abhängt:

```bicep
resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = {
  name: 'demoeZone1'
  location: 'global'
}

resource otherZone 'Microsoft.Network/dnszones@2018-05-01' = {
  name: 'demoZone2'
  location: 'global'
  dependsOn: [
    dnsZone
  ]
}
```

Sie können Beziehungen zwischen Ihren Ressourcen mithilfe von `dependsOn` zuordnen, dabei sollte Ihnen jedoch bewusst sein, welchen Zweck diese Zuordnung erfüllt. Um beispielsweise zu dokumentieren, wie Ressourcen miteinander verbunden sind, ist `dependsOn` nicht der richtige Ansatz. Nach der Bereitstellung können Sie nicht mehr abfragen, welche Ressourcen im `dependsOn`-Element definiert waren. Das Festlegen unnötiger Abhängigkeiten verlangsamt die Bereitstellung, da Resource Manager diese Ressourcen nicht gleichzeitig bereitstellen kann.

Explizite Abhängigkeiten sind zwar manchmal erforderlich, dies ist aber selten der Fall. In den meisten Fällen können Sie einen symbolischen Verweis verwenden, um die Abhängigkeit zwischen Ressourcen zu implizieren. Wenn Sie selbst explizite Abhängigkeiten festlegen, sollten Sie berücksichtigen, ob es eine Möglichkeit gibt, sie zu entfernen.

### <a name="visualize-dependencies"></a>Visualisieren von Abhängigkeiten

Visual Studio Code stellt ein Tool zum Visualisieren der Abhängigkeiten zur Verfügung. Öffnen Sie eine Bicep-Datei in Visual Studio Code, und wählen Sie dann die Schaltfläche „Schnellansicht“ in der oberen linken Ecke aus.  Der folgende Screenshot zeigt die Abhängigkeiten eines virtuellen Computers.

:::image type="content" source="./media/resource-declaration/bicep-resource-visualizer.png" alt-text="Screenshot: Schnellansicht der Bicep-Ressourcen in Visual Studio Code":::

## <a name="reference-existing-resources"></a>Verweisen auf vorhandene Ressourcen

Um auf eine Ressource zu verweisen, die sich außerhalb der aktuellen Bicep-Datei befindet, verwenden Sie das `existing`-Schlüsselwort in einer Ressourcendeklaration.

Geben Sie bei Verwendung des Schlüsselworts `existing` den `name` der Ressource an. Im folgenden Beispiel wird ein vorhandenes Speicherkonto in derselben Ressourcengruppe wie der der aktuellen Bereitstellung gespeichert.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
  name: 'examplestorage'
}

output blobEndpoint string = stg.properties.primaryEndpoints.blob
```

Optional können Sie die `scope`-Eigenschaft festlegen, um auf eine Ressource in einem anderen Bereich zuzugreifen. Im folgenden Beispiel wird auf ein vorhandenes Speicherkonto in einer anderen Ressourcengruppe verwiesen.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
  name: 'examplestorage'
  scope: resourceGroup(exampleRG)
}

output blobEndpoint string = stg.properties.primaryEndpoints.blob
```

Weitere Informationen zum Festlegen des Bereichs finden Sie unter [Bereichsfunktionen für Bicep](bicep-functions-scope.md).

In den vorherigen Beispielen wird das Speicherkonto nicht bereitgestellt. Stattdessen können Sie mithilfe des symbolischen Namens auf Eigenschaften der vorhandenen Ressource zugreifen.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur bedingten Bereitstellung einer Ressource finden Sie unter [Bedingte Bereitstellung in Bicep](./conditional-resource-deployment.md).
