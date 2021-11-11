---
title: Bedingte Bereitstellung mit Bicep
description: Beschreibt, wie eine Ressource in Bicep bedingt bereitgestellt werden kann.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 07/30/2021
ms.openlocfilehash: 0c2f154a0c45dd46a56a4321aa67a75e2263b317
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130219600"
---
# <a name="conditional-deployment-in-bicep"></a>Bedingte Bereitstellung in Bicep

Manchmal muss eine Ressource oder ein Modul optional in Bicep bereitgestellt werden. Verwenden Sie das Schlüsselwort `if`, um anzugeben, ob die Ressource oder das Modul bereitgestellt wird. Der Wert für die Bedingung wird in „true“ oder „false“ aufgelöst. Wenn der Wert TRUE ist, wird die Ressource erstellt. Wenn der Wert FALSE ist, wird die Ressource nicht erstellt. Der Wert kann nur auf die gesamte Ressource bzw. auf das gesamte Modul angewendet werden.

> [!NOTE]
> Die bedingte Bereitstellung wird nicht an [untergeordnete Ressourcen](child-resource-name-type.md) weitergegeben. Wenn Sie eine Ressource und ihre untergeordneten Ressourcen bedingt bereitstellen möchten, müssen Sie dieselbe Bedingung auf jeden Ressourcentyp anwenden.

### <a name="microsoft-learn"></a>Microsoft Learn

Weitere Informationen und praktische Anleitungen zu Bedingungen finden Sie in **Microsoft Learn** unter [Erstellen flexibler Bicep-Vorlagen mithilfe von Bedingungen und Schleifen](/learn/modules/build-flexible-bicep-templates-conditions-loops/).

## <a name="deploy-condition"></a>Bereitstellungsbedingung

Sie können einen Parameterwert übergeben, der angibt, ob eine Ressource bereitgestellt wird. Das folgende Beispiel stellt bedingt eine DNS-Zone bereit:

```bicep
param deployZone bool

resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = if (deployZone) {
  name: 'myZone'
  location: 'global'
}
```

Im nächsten Beispiel wird ein Modul bedingt bereitgestellt.

```bicep
param deployZone bool

module dnsZone 'dnszones.bicep' = if (deployZone) {
  name: 'myZoneModule'
}
```

Bedingungen können mit Abhängigkeitsdeklarationen verwendet werden. Bei [expliziten Abhängigkeiten](resource-declaration.md#dependencies) entfernt Azure Resource Manager sie automatisch aus den erforderlichen Abhängigkeiten, wenn die Ressource nicht bereitgestellt wird. Bei impliziten Abhängigkeiten kann zwar auf eine Eigenschaft einer bedingten Ressource verwiesen werden, dies kann allerdings einen Bereitstellungsfehler zur Folge haben.

## <a name="new-or-existing-resource"></a>Neue oder vorhandene Ressource

Sie können bedingte Bereitstellung verwenden, um eine neue Ressource zu erstellen oder eine vorhandene zu verwenden. Im folgenden Beispiel wird gezeigt, wie ein neues Speicherkonto bereitgestellt oder ein vorhandenes Speicherkonto verwendet wird.

```bicep
param storageAccountName string
param location string = resourceGroup().location

@allowed([
  'new'
  'existing'
])
param newOrExisting string = 'new'

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
  name: storageAccountName
  location: location
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

Wenn der Parameter `newOrExisting` auf **new** festgelegt ist, wird die Bedingung zu „true“ ausgewertet. Das Speicherkonto wird bereitgestellt. Ist `newOrExisting` dagegen auf **existing** festgelegt, wird die Bedingung zu „false“ ausgewertet, und das Speicherkonto wird nicht bereitgestellt.

## <a name="runtime-functions"></a>Laufzeitfunktionen

Bei Verwendung einer Funktion vom Typ [reference](./bicep-functions-resource.md#reference) oder [list](./bicep-functions-resource.md#list) mit einer Ressource, die bedingt bereitgestellt wird, wird die Funktion auch dann ausgewertet, wenn die Ressource nicht bereitgestellt wird. Es wird eine Fehlermeldung angezeigt, wenn die Funktion auf eine nicht vorhandene Ressource verweist.

Verwenden Sie den Operator [Bedingter Ausdruck ?:](./operators-logical.md#conditional-expression--), um sicherzustellen, dass die Funktion nur für Bedingungen ausgewertet wird, wenn die Ressource bereitgestellt wird. In der folgenden Beispielvorlage wird gezeigt, wie Sie diese Funktion mit Ausdrücken verwenden, die nur bedingt gültig sind.

```bicep
param vmName string
param location string
param logAnalytics string = ''

resource vmName_omsOnboarding 'Microsoft.Compute/virtualMachines/extensions@2017-03-30' = if (!empty(logAnalytics)) {
  name: '${vmName}/omsOnboarding'
  location: location
  properties: {
    publisher: 'Microsoft.EnterpriseCloud.Monitoring'
    type: 'MicrosoftMonitoringAgent'
    typeHandlerVersion: '1.0'
    autoUpgradeMinorVersion: true
    settings: {
      workspaceId: ((!empty(logAnalytics)) ? reference(logAnalytics, '2015-11-01-preview').customerId : json('null'))
    }
    protectedSettings: {
      workspaceKey: ((!empty(logAnalytics)) ? listKeys(logAnalytics, '2015-11-01-preview').primarySharedKey : json('null'))
    }
  }
}

output mgmtStatus string = ((!empty(logAnalytics)) ? 'Enabled monitoring for VM!' : 'Nothing to enable')
```

## <a name="next-steps"></a>Nächste Schritte

* Ein Microsoft Learn-Modul zu Bedingungen und Schleifen finden Sie unter [Erstellen flexibler Bicep-Vorlagen mithilfe von Bedingungen und Schleifen](/learn/modules/build-flexible-bicep-templates-conditions-loops/).
* Empfehlungen zum Erstellen von Bicep-Dateien finden Sie unter [Bewährte Methoden für Bicep](best-practices.md).
* Informationen zum Erstellen mehrerer Instanzen einer Ressource finden Sie unter [Iterative Schleifen in Bicep](loops.md).
