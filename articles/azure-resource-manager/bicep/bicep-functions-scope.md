---
title: 'Bicep-Funktionen: Bereiche'
description: Hier werden die Funktionen beschrieben, die in einer Bicep-Datei zum Abrufen von Werten zu Bereitstellungsbereichen verwendet werden.
ms.topic: conceptual
ms.date: 09/30/2021
ms.openlocfilehash: ee7ea8efbb19048ff5aa6b6ead2bf9d2495ad23a
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129360230"
---
# <a name="scope-functions-for-bicep"></a>Bereichsfunktionen für Bicep

Dieser Artikel beschreibt die Bicep-Funktionen zum Abrufen von Scope-Werten.

## <a name="managementgroup"></a>managementGroup

`managementGroup()`

`managementGroup(name)`

Gibt ein Objekt zurück, das zum Festlegen des Bereichs in einer Verwaltungsgruppe verwendet wird

Namespace: [az](bicep-functions.md#namespaces-for-functions).

### <a name="remarks"></a>Hinweise

`managementGroup()` kann nur für [Bereitstellungen von Verwaltungsgruppen](deploy-to-management-group.md) verwendet werden. Gibt die aktuelle Verwaltungsgruppe für den Bereitstellungsvorgang zurück

`managementGroup(name)` kann für beliebige Bereitstellungsbereiche verwendet werden.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| name |Nein |Zeichenfolge |Eindeutiger Bezeichner für die Verwaltungsgruppe zur Bereitstellung Verwenden Sie nicht den Anzeigenamen für die Verwaltungsgruppe. Wenn Sie keinen Wert angeben, wird die aktuelle Verwaltungsgruppe ausgegeben. |

### <a name="return-value"></a>Rückgabewert

Ein Objekt, das zum Festlegen der `scope`-Eigenschaft bei einem [Modul](modules.md#configure-module-scopes) oder einem [Erweiterungsressourcentyp](scope-extension-resources.md) verwendet wird

### <a name="management-group-example"></a>Beispiel für Verwaltungsgruppe

Das folgende Beispiel legt den Bereich für ein Modul für eine Verwaltungsgruppe fest.

```bicep
param managementGroupName string

module  'module.bicep' = {
  name: 'deployToMG'
  scope: managementGroup(managementGroupName)
}
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

Die Funktion resourceGroup hat zwei spezielle Verwendungszwecke. Ein Zweck ist das Festlegen des Bereichs bei einem [Modul](modules.md#configure-module-scopes) oder einem [Erweiterungsressourcentyp](scope-extension-resources.md). Der andere Zweck ist das Abrufen von Details zur aktuellen Ressourcengruppe. Die Platzierung der Funktion bestimmt die Verwendung. Wenn die Funktion zum Festlegen der `scope`-Eigenschaft verwendet wird, gibt sie ein Bereichsobjekt zurück.

`resourceGroup()` kann entweder zum Festlegen des Bereichs oder zum Abrufen von Details zur Ressourcengruppe genutzt werden.

`resourceGroup(resourceGroupName)` und `resourceGroup(subscriptionId, resourceGroupName)` können nur zum Festlegen von Bereichen verwendet werden.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

Die Funktion subscription hat zwei spezielle Verwendungszwecke. Ein Zweck ist das Festlegen des Bereichs bei einem [Modul](modules.md#configure-module-scopes) oder einem [Erweiterungsressourcentyp](scope-extension-resources.md). Der andere Zweck ist das Abrufen von Details zum aktuellen Abonnement. Die Platzierung der Funktion bestimmt die Verwendung. Wenn die Funktion zum Festlegen der `scope`-Eigenschaft verwendet wird, gibt sie ein Bereichsobjekt zurück.

`subscription(subscriptionId)` kann nur zum Festlegen von Bereichen verwendet werden.

`subscription()` kann zum Festlegen des Bereichs oder zum Abrufen von Details zum Abonnement genutzt werden.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

Namespace: [az](bicep-functions.md#namespaces-for-functions).

### <a name="remarks"></a>Hinweise

`tenant()` kann für beliebige Bereitstellungsbereiche verwendet werden. Sie gibt immer den aktuellen Mandanten zurück.

### <a name="return-value"></a>Rückgabewert

Ein Objekt, das zum Festlegen der `scope`-Eigenschaft bei einem [Modul](modules.md#configure-module-scopes) oder einem [Erweiterungsressourcentyp](scope-extension-resources.md) verwendet wird

### <a name="tenant-example"></a>Mandantenbeispiel

Das folgende Beispiel zeigt ein Modul, das im Mandanten bereitgestellt wird.

```bicep
module exampleModule 'module.bicep' = {
  name: 'deployToTenant'
  scope: tenant()
}
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Bereitstellungsbereichen finden Sie unter:

* [Bereitstellungen von Ressourcengruppen](deploy-to-resource-group.md)
* [Abonnementbereitstellungen](deploy-to-subscription.md)
* [Verwaltungsgruppenbereitstellungen mit Bicep-Dateien](deploy-to-management-group.md)
* [Mandantenbereitstellungen mit Bicep-Datei](deploy-to-tenant.md)
