---
title: Bicep-Module
description: Hier wird beschrieben, wie ein Modul definiert und verwendet wird und wie Modulbereiche eingesetzt werden.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 10/05/2021
ms.openlocfilehash: bd5069db6a2ad9cb14f5f0b3bc28612afa519727
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2021
ms.locfileid: "129619593"
---
# <a name="use-bicep-modules"></a>Verwenden von Bicep-Modulen

Mithilfe von Bicep können Sie eine komplexe Lösung in Modul unterteilen. Bei einem Bicep-Modul handelt es sich um eine Bicep-Datei, die von einer anderen Bicep-Datei bereitgestellt wird. Komplexe Details der Ressourcendeklaration können in ein Modul gekapselt werden, um die Lesbarkeit von Dateien, die das Modul verwenden, zu verbessern. Diese Module können wiederverwendet und für andere Benutzer freigegeben werden. Bicep-Module werden in eine einzelne ARM-Vorlage (Azure Resource Manager) mit [geschachtelten Vorlagen](../templates/linked-templates.md#nested-template) für die Bereitstellung konvertiert.

In diesem Artikel erfahren Sie, wie Sie Module definieren und verwenden.

Ein Tutorial finden Sie unter [Erstellen Ihrer ersten Bicep-Vorlage](/learn/modules/deploy-azure-resources-by-using-bicep-templates/).

## <a name="define-modules"></a>Definieren von Modulen

Jede Bicep-Datei kann als Modul verwendet werden. Ein Modul stellt Parameter und Ausgaben nur als Vertrag für andere Bicep-Dateien bereit. Parameter und Ausgaben sind optional.

Die folgende Bicep-Datei kann zum Erstellen eines Speicherkontos bereitgestellt oder als Modul verwendet werden.  Im nächsten Abschnitt erfahren Sie, wie Module verwendet werden:

```bicep
@minLength(3)
@maxLength(11)
param storagePrefix string

@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_RAGRS'
  'Standard_ZRS'
  'Premium_LRS'
  'Premium_ZRS'
  'Standard_GZRS'
  'Standard_RAGZRS'
])
param storageSKU string = 'Standard_LRS'
param location string

var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'

resource stg 'Microsoft.Storage/storageAccounts@2019-04-01' = {
  name: uniqueStorageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}

output storageEndpoint object = stg.properties.primaryEndpoints
```

Die Ausgabe wird verwendet, um Werte an die übergeordneten Bicep-Dateien zu übergeben.

## <a name="consume-modules"></a>Verwenden von Modulen

Verwenden Sie das Schlüsselwort _module_, um ein Modul zu verwenden. Mit der folgenden Bicep-Datei wird die in der referenzierten Moduldatei definierte Ressource bereitgestellt:

```bicep
@minLength(3)
@maxLength(11)
param namePrefix string
param location string = resourceGroup().location

module stgModule 'storageAccount.bicep' = {
  name: 'storageDeploy'
  params: {
    storagePrefix: namePrefix
    location: location
  }
}

output storageEndpoint object = stgModule.outputs.storageEndpoint
```

- **module**: Schlüsselwort.
- **symbolischer Name** (stgModule): Bezeichner für das Modul.
- **Moduldatei**: Auf Moduldateien muss mithilfe relativer Pfade verwiesen werden. Alle Pfade in Bicep müssen mithilfe des eines Schrägstrichs (/) als Verzeichnistrennzeichen angegeben werden, um eine plattformübergreifende konsistente Kompilierung sicherzustellen. Der umgekehrte Schrägstrich von Windows (\\) wird nicht unterstützt. Pfade können Leerzeichen enthalten.
- Die Eigenschaft **_name_** (storageDeploy) ist bei der Verwendung eines Moduls erforderlich. Wenn mit Bicep die Vorlage IL generiert wird, wird dieses Feld als Name der Ressource für geschachtelte Bereitstellung verwendet, die für das Modul generiert wird:

    ```json
    ...
    ...
    "resources": [
      {
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2020-10-01",
        "name": "storageDeploy",
        "properties": {
          ...
        }
      }
    ]
    ...
    ```
- Die **_params_**-Eigenschaft enthält alle Parameter, die an die Moduldatei übergeben werden sollen. Diese Parameter entsprechen den in der Bicep-Datei definierten Parametern.

Module werden wie Ressourcen parallel bereitgestellt, es sei denn, sie hängen von anderen Modulen oder Ressourcenbereitstellungen ab. Weitere Informationen zu Abhängigkeiten finden Sie unter [Festlegen von Ressourcenabhängigkeiten](resource-declaration.md#set-resource-dependencies).

Zum Abrufen eines Ausgabewerts aus einem Modul rufen Sie den Eigenschaftswert mit einer Syntax wie dieser ab: `stgModule.outputs.storageEndpoint`. Dabei ist `stgModule` der Bezeichner des Moduls.

Sie können ein Modul bedingt bereitstellen. Verwenden Sie dieselbe **if**-Syntax, wie Sie sie bei einer [bedingten Bereitstellung einer Ressource](conditional-resource-deployment.md) verwenden würden.

```bicep
param deployZone bool

module dnsZone 'dnszones.bicep' = if (deployZone) {
  name: 'myZoneModule'
}
```

Mithilfe von Schleifen können Sie ein Modul mehrmals bereitstellen. Weitere Informationen finden Sie unter [Moduliteration in Bicep](loop-modules.md).

## <a name="configure-module-scopes"></a>Konfigurieren von Modulbereichen

Beim Deklarieren eines Moduls können Sie für das Modul einen Bereich festlegen, der sich vom Bereich für die enthaltene Bicep-Datei unterscheidet. Legen Sie den Bereich für das Modul mithilfe der Eigenschaft `scope` fest. Wenn die scope-Eigenschaft nicht angegeben wird, wird das Modul im Zielbereich des übergeordneten Moduls bereitgestellt.

Anhand der folgenden Bicep-Datei wird deutlich, wie eine Ressourcengruppe erstellt und ein Modul in der Ressourcengruppe bereitgestellt wird:

```bicep
// set the target scope for this file
targetScope = 'subscription'

@minLength(3)
@maxLength(11)
param namePrefix string

param location string = deployment().location

var resourceGroupName = '${namePrefix}rg'
resource myResourceGroup 'Microsoft.Resources/resourceGroups@2021-04-01' = {
  name: resourceGroupName
  location: location
  scope: subscription()
}

module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  scope: myResourceGroup
  params: {
    storagePrefix: namePrefix
    location: location
  }
}

output storageEndpoint object = stgModule.outputs.storageEndpoint
```

Im folgenden Beispiel wird veranschaulicht, wie Sie die Bereitstellung in vorhandene Ressourcengruppen ausführen:

```bicep
targetScope = 'subscription'

resource firstRG 'Microsoft.Resources/resourceGroups@2021-04-01' existing = {
  name: 'demogroup1'
}

resource secondRG 'Microsoft.Resources/resourceGroups@2021-04-01' existing = {
  name: 'demogroup2'
}

module storage1 'storageAccount.bicep' = {
  name: 'westusdeploy'
  scope: firstRG
  params: {
    storagePrefix: 'stg1'
    location: 'westus'
  }
}

module storage2 'storageAccount.bicep' = {
  name: 'eastusdeploy'
  scope: secondRG
  params: {
    storagePrefix: 'stg2'
    location: 'eastus'
  }
}
```

Die scope-Eigenschaft muss auf ein gültiges Bereichsobjekt festgelegt werden. Wenn Ihre Bicep-Datei eine Ressourcengruppe, ein Abonnement oder eine Verwaltungsgruppe bereitgestellt, können Sie den Bereich für ein Modul auf den symbolischen Namen für diese Ressource festlegen. Sie können auch die Bereichsfunktionen verwenden, um einen gültigen Bereich zu erhalten.

Dabei handelt es sich um die folgenden Funktionen:

- [Ressourcengruppe](bicep-functions-scope.md#resourcegroup)
- [Abonnement](bicep-functions-scope.md#subscription)
- [managementGroup](bicep-functions-scope.md#managementgroup)
- [tenant](bicep-functions-scope.md#tenant)

Im folgenden Beispiel wird der Bereich mithilfe der Funktion `managementGroup` festgelegt:

```bicep
param managementGroupName string

module  'module.bicep' = {
  name: 'deployToMG'
  scope: managementGroup(managementGroupName)
}
```

## <a name="next-steps"></a>Nächste Schritte

- Mithilfe der Funktion [getSecret](bicep-functions-resource.md#getsecret) können Sie einen vertraulichen Wert an ein Modul übergeben.
- Mithilfe von Schleifen können Sie ein Modul mehrmals bereitstellen. Weitere Informationen finden Sie unter [Moduliteration in Bicep](loop-modules.md).
