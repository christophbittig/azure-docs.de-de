---
title: 'Bicep-Funktionen: Bereiche'
description: Hier werden die Funktionen beschrieben, die in einer Bicep-Datei zum Abrufen von Werten zu Bereitstellungsbereichen verwendet werden.
ms.topic: conceptual
ms.date: 10/18/2021
ms.openlocfilehash: 809d3b2097e530f92370374be5f15c39bbd70734
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130178339"
---
# <a name="scope-functions-for-bicep"></a>Bereichsfunktionen für Bicep

Dieser Artikel beschreibt die Bicep-Funktionen zum Abrufen von Scope-Werten.

## <a name="managementgroup"></a>managementGroup

`managementGroup()`

`managementGroup(identifier)`

Gibt ein Objekt zurück, das zum Festlegen des Bereichs in einer Verwaltungsgruppe verwendet wird

oder

Gibt ein Objekt mit Eigenschaften aus der Verwaltungsgruppe in der aktuellen Bereitstellung zurück.

Namespace: [az](bicep-functions.md#namespaces-for-functions).

### <a name="remarks"></a>Hinweise

`managementGroup()` kann nur für [Bereitstellungen von Verwaltungsgruppen](deploy-to-management-group.md) verwendet werden. Gibt die aktuelle Verwaltungsgruppe für den Bereitstellungsvorgang zurück Verwenden, wenn Sie entweder ein Bereichsobjekt abrufen oder Eigenschaften für die aktuelle Verwaltungsgruppe abrufen.

`managementGroup(identifier)` kann für jeden Bereitstellungsbereich verwendet werden, jedoch nur beim Abrufen des Bereichsobjekts. Um die Eigenschaften für eine Verwaltungsgruppe abzurufen, können Sie nicht den Verwaltungsgruppenbezeichner als Eingabe übergeben.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| Bezeichner |Nein |Zeichenfolge |Eindeutiger Bezeichner für die Verwaltungsgruppe zur Bereitstellung Verwenden Sie nicht den Anzeigenamen für die Verwaltungsgruppe. Wenn Sie keinen Wert angeben, wird die aktuelle Verwaltungsgruppe ausgegeben. |

### <a name="return-value"></a>Rückgabewert

Ein Objekt, das zum Festlegen der `scope`-Eigenschaft bei einem [Modul](modules.md#set-module-scope) oder einem [Erweiterungsressourcentyp](scope-extension-resources.md) verwendet wird Oder ein Objekt mit den Eigenschaften für die aktuelle Verwaltungsgruppe.

### <a name="management-group-example"></a>Beispiel für Verwaltungsgruppe

Das folgende Beispiel legt den Bereich für ein Modul für eine Verwaltungsgruppe fest.

```bicep
param managementGroupIdentifier string

module  'module.bicep' = {
  name: 'deployToMG'
  scope: managementGroup(managementGroupIdentifier)
}
```

Im nächsten Beispiel werden Eigenschaften für die aktuelle Verwaltungsgruppe zurückgegeben.

```bicep
targetScope = 'managementGroup'

var mgInfo = managementGroup()

output mgResult object = mgInfo
```

Rückgabe:

```json
"mgResult": {
  "type": "Object",
  "value": {
    "id": "/providers/Microsoft.Management/managementGroups/examplemg1",
    "name": "examplemg1",
    "properties": {
      "details": {
        "parent": {
          "displayName": "Tenant Root Group",
          "id": "/providers/Microsoft.Management/managementGroups/00000000-0000-0000-0000-000000000000",
          "name": "00000000-0000-0000-0000-000000000000"
        },
        "updatedBy": "00000000-0000-0000-0000-000000000000",
        "updatedTime": "2020-07-23T21:05:52.661306Z",
        "version": "1"
      },
      "displayName": "Example MG 1",
      "tenantId": "00000000-0000-0000-0000-000000000000"
    },
    "type": "/providers/Microsoft.Management/managementGroups"
  }
}
```

Im nächsten Beispiel wird eine neue Verwaltungsgruppe erstellt und diese Funktion verwendet, um die übergeordnete Verwaltungsgruppe festzulegen.

```bicep
targetScope = 'managementGroup'

param mgName string = 'mg-${uniqueString(newGuid())}'

resource newMG 'Microsoft.Management/managementGroups@2020-05-01' = {
  scope: tenant()
  name: mgName
  properties: {
    details: {
      parent: {
        id: managementGroup().id
      }
    }
  }
}

output newManagementGroup string = mgName
```

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

`resourceGroup(resourceGroupName)`

`resourceGroup(subscriptionId, resourceGroupName)`

Gibt ein Objekt zurück, das zum Festlegen des Bereichs in einer Ressourcengruppe verwendet wird

oder

Gibt ein Objekt zurück, das die aktuelle Ressourcengruppe darstellt.

Namespace: [az](bicep-functions.md#namespaces-for-functions).

### <a name="remarks"></a>Hinweise

Die Funktion resourceGroup hat zwei spezielle Verwendungszwecke. Ein Zweck ist das Festlegen des Bereichs bei einem [Modul](modules.md#set-module-scope) oder einem [Erweiterungsressourcentyp](scope-extension-resources.md). Der andere Zweck ist das Abrufen von Details zur aktuellen Ressourcengruppe. Die Platzierung der Funktion bestimmt die Verwendung. Wenn die Funktion zum Festlegen der `scope`-Eigenschaft verwendet wird, gibt sie ein Bereichsobjekt zurück.

`resourceGroup()` kann entweder zum Festlegen des Bereichs oder zum Abrufen von Details zur Ressourcengruppe genutzt werden.

`resourceGroup(resourceGroupName)` und `resourceGroup(subscriptionId, resourceGroupName)` können nur zum Festlegen von Bereichen verwendet werden.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| resourceGroupName |Nein |Zeichenfolge | Name der Ressourcengruppe, in der Sie die Bereitstellung durchführen Wenn Sie keinen Wert angeben, wird die aktuelle Ressourcengruppe ausgegeben. |
| subscriptionId |Nein |Zeichenfolge |Eindeutiger Bezeichner für das Abonnement, in dem Sie die Bereitstellung durchführen Wenn Sie keinen Wert angeben, wird das aktuelle Abonnement ausgegeben. |

### <a name="return-value"></a>Rückgabewert

Wenn die Funktion zum Festlegen des Bereichs verwendet wird, gibt sie ein Objekt zurück, das für die `scope`-Eigenschaft bei einem Modul oder einem Erweiterungsressourcentyp gültig ist.

Beim Abrufen von Details zur Ressourcengruppe gibt die Funktion das folgende Format zurück:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "{resourceGroupLocation}",
  "managedBy": "{identifier-of-managing-resource}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

Die Eigenschaft **managedBy** wird nur für Ressourcengruppen zurückgegeben, die von einem anderen Dienst verwaltete Ressourcen enthalten. Bei verwalteten Anwendungen, Databricks und AKS ist der Wert der Eigenschaft die Ressourcen-ID der Verwaltungsressource.

### <a name="resource-group-example"></a>resourceGroup-Beispiel

Im folgenden Beispiel wird ein Modul per Bereich einer Ressourcengruppe zugewiesen.

```bicep
param resourceGroupName string

module exampleModule 'module.bicep' = {
  name: 'exampleModule'
  scope: resourceGroup(resourceGroupName)
}
```

Das nächste Beispiel gibt die Eigenschaften der Ressourcengruppe zurück.

```bicep
output resourceGroupOutput object = resourceGroup()
```

Es gibt ein Objekt im folgenden Format zurück:

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

Die Funktion „resourceGroup“ wird häufig verwendet, um Ressourcen am gleichen Speicherort wie die Ressourcengruppe zu erstellen. Das folgende Beispiel nutzt den Speicherort der Ressourcengruppe als einen Parameterstandardwert.

```bicep
param location string = resourceGroup().location
```

Die Funktion „resourceGroup“ kann auch verwendet werden, um Tags aus einer Ressourcengruppe auf eine Ressource anzuwenden. Weitere Informationen finden Sie unter [Apply tags from resource group](../management/tag-resources.md#apply-tags-from-resource-group) (Anwenden von Tags aus einer Ressourcengruppe).

## <a name="subscription"></a>Abonnement

`subscription()`

`subscription(subscriptionId)`

Gibt ein Objekt zurück, das zum Festlegen des Bereichs in einem Abonnement verwendet wird

oder

Gibt Details zum Abonnement für die aktuelle Bereitstellung zurück.

Namespace: [az](bicep-functions.md#namespaces-for-functions).

### <a name="remarks"></a>Hinweise

Die Funktion subscription hat zwei spezielle Verwendungszwecke. Ein Zweck ist das Festlegen des Bereichs bei einem [Modul](modules.md#set-module-scope) oder einem [Erweiterungsressourcentyp](scope-extension-resources.md). Der andere Zweck ist das Abrufen von Details zum aktuellen Abonnement. Die Platzierung der Funktion bestimmt die Verwendung. Wenn die Funktion zum Festlegen der `scope`-Eigenschaft verwendet wird, gibt sie ein Bereichsobjekt zurück.

`subscription(subscriptionId)` kann nur zum Festlegen von Bereichen verwendet werden.

`subscription()` kann zum Festlegen des Bereichs oder zum Abrufen von Details zum Abonnement genutzt werden.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| subscriptionId |Nein |Zeichenfolge |Eindeutiger Bezeichner für das Abonnement, in dem Sie die Bereitstellung durchführen Wenn Sie keinen Wert angeben, wird das aktuelle Abonnement ausgegeben. |

### <a name="return-value"></a>Rückgabewert

Wenn die Funktion zum Festlegen des Bereichs verwendet wird, gibt sie ein Objekt zurück, das für die `scope`-Eigenschaft bei einem Modul oder einem Erweiterungsressourcentyp gültig ist.

Beim Abrufen von Details zum Abonnement gibt die Funktion das folgende Format zurück:

```json
{
  "id": "/subscriptions/{subscription-id}",
  "subscriptionId": "{subscription-id}",
  "tenantId": "{tenant-id}",
  "displayName": "{name-of-subscription}"
}
```

### <a name="subscription-example"></a>subscription-Beispiel

Im folgenden Beispiel wird ein Modul per Bereich dem Abonnement zugewiesen.

```bicep
module exampleModule 'module.bicep' = {
  name: 'deployToSub'
  scope: subscription()
}
```

Das nächste Beispiel gibt die Details für ein Abonnement zurück.

```bicep
output subscriptionOutput object = subscription()
```

## <a name="tenant"></a>tenant

`tenant()`

Gibt ein Objekt zurück, das zum Festlegen des Bereichs für einen Mandanten verwendet wird

oder

Gibt Eigenschaften zum Mandanten für die aktuelle Bereitstellung zurück.

Namespace: [az](bicep-functions.md#namespaces-for-functions).

### <a name="remarks"></a>Hinweise

`tenant()` kann für beliebige Bereitstellungsbereiche verwendet werden. Sie gibt immer den aktuellen Mandanten zurück. Sie können diese Funktion verwenden, um den Bereich für eine Ressource festzulegen oder Eigenschaften für den aktuellen Mandanten abzurufen.

### <a name="return-value"></a>Rückgabewert

Ein Objekt, das zum Festlegen der `scope`-Eigenschaft bei einem [Modul](modules.md#set-module-scope) oder einem [Erweiterungsressourcentyp](scope-extension-resources.md) verwendet wird Oder ein Objekt mit Eigenschaften über den aktuellen Mandanten.

### <a name="tenant-example"></a>Mandantenbeispiel

Das folgende Beispiel zeigt ein Modul, das im Mandanten bereitgestellt wird.

```bicep
module exampleModule 'module.bicep' = {
  name: 'deployToTenant'
  scope: tenant()
}
```

Das nächste Beispiel gibt die Eigenschaften für einen Mandanten zurück.

```bicep
var tenantInfo = tenant()

output tenantResult object = tenantInfo
```

Rückgabe:

```json
"tenantResult": {
  "type": "Object",
  "value": {
    "countryCode": "US",
    "displayName": "Contoso",
    "id": "/tenants/00000000-0000-0000-0000-000000000000",
    "tenantId": "00000000-0000-0000-0000-000000000000"
  }
}
```

Für einige Ressourcen muss die Mandanten-ID für eine Eigenschaft festgelegt werden. Anstatt die Mandanten-ID als Parameter zu übergeben, können Sie sie mit der Mandantenfunktion abrufen.

```bicep
resource kv 'Microsoft.KeyVault/vaults@2021-06-01-preview' = {
  name: 'examplekeyvault'
  location: 'westus'
  properties: {
    tenantId: tenant().tenantId
    ...
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Bereitstellungsbereichen finden Sie unter:

* [Bereitstellungen von Ressourcengruppen](deploy-to-resource-group.md)
* [Abonnementbereitstellungen](deploy-to-subscription.md)
* [Verwaltungsgruppenbereitstellungen mit Bicep-Dateien](deploy-to-management-group.md)
* [Mandantenbereitstellungen mit Bicep-Datei](deploy-to-tenant.md)
