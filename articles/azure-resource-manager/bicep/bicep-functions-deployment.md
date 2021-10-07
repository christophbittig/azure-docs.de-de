---
title: 'Bicep-Funktionen: Bereitstellung'
description: Hier werden die Funktionen beschrieben, die in einer Bicep-Datei zum Abrufen von Bereitstellungsinformationen verwendet werden sollen.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/10/2021
ms.openlocfilehash: e909ea77da32675beec6d5117430717acf274ab1
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124731097"
---
# <a name="deployment-functions-for-bicep"></a>Bereitstellungsfunktionen für Bicep

In diesem Artikel werden die Bicep-Funktionen zum Abrufen von Werten im Zusammenhang mit der aktuellen Bereitstellung beschrieben.

## <a name="deployment"></a>deployment

`deployment()`

Gibt Informationen zum aktuellen Bereitstellungsvorgang zurück.

### <a name="return-value"></a>Rückgabewert

Diese Funktion gibt das Objekt zurück, das während der Bereitstellung übergeben wird. Die Eigenschaften in dem zurückgegebenen Objekt unterscheiden sich abhängig davon, wo Sie sich befinden:

* Bereitstellen einer lokalen Bicep-Datei
* Bereitstellen in einer Ressourcengruppe oder Bereitstellen in einem der anderen Bereiche ([Azure-Abonnement](deploy-to-subscription.md), [Verwaltungsgruppe](deploy-to-management-group.md) oder [Mandant](deploy-to-tenant.md)).

Beim Bereitstellen einer lokalen Bicep-Datei in einer Ressourcengruppe gibt die Funktion das folgende Format zurück:

```json
{
  "name": "",
  "properties": {
    "template": {
      "$schema": "",
      "contentVersion": "",
      "parameters": {},
      "variables": {},
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

Wenn Sie in einem Azure-Abonnement, einer Verwaltungsgruppe oder einem Mandanten bereitstellen, enthält das Rückgabeobjekt eine `location`-Eigenschaft. Die location-Eigenschaft ist beim Bereitstellen einer lokalen Bicep-Datei enthalten. Das Format lautet:

```json
{
  "name": "",
  "location": "",
  "properties": {
    "template": {
      "$schema": "",
      "contentVersion": "",
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird das Bereitstellungsobjekt zurückgegeben:

```bicep
output deploymentOutput object = deployment()
```

Im vorherigen Beispiel wird das folgende Objekt zurückgegeben:

```json
{
  "name": "deployment",
  "properties": {
    "template": {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "deploymentOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "templateHash": "13135986259522608210",
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

## <a name="environment"></a>Environment

`environment()`

Gibt Informationen zur für die Bereitstellung verwendeten Azure-Umgebung zurück.

### <a name="return-value"></a>Rückgabewert

Diese Funktion gibt Eigenschaften für die aktuelle Azure-Umgebung zurück. Das folgende Beispiel zeigt die Eigenschaften für globales Azure. Sovereign Clouds können etwas andere Eigenschaften zurückgeben.

```json
{
  "name": "",
  "gallery": "",
  "graph": "",
  "portal": "",
  "graphAudience": "",
  "activeDirectoryDataLake": "",
  "batch": "",
  "media": "",
  "sqlManagement": "",
  "vmImageAliasDoc": "",
  "resourceManager": "",
  "authentication": {
    "loginEndpoint": "",
    "audiences": [
      "",
      ""
    ],
    "tenant": "",
    "identityProvider": ""
  },
  "suffixes": {
    "acrLoginServer": "",
    "azureDatalakeAnalyticsCatalogAndJob": "",
    "azureDatalakeStoreFileSystem": "",
    "azureFrontDoorEndpointSuffix": "",
    "keyvaultDns": "",
    "sqlServerHostname": "",
    "storage": ""
  }
}
```

### <a name="example"></a>Beispiel

Die folgende Bicep-Beispieldatei gibt das Umgebungsobjekt zurück.

```bicep
output environmentOutput object = environment()
```

Im vorherigen Beispiel wird bei Bereitstellung in globalem Azure das folgende Objekt zurückgegeben:

```json
{
  "name": "AzureCloud",
  "gallery": "https://gallery.azure.com/",
  "graph": "https://graph.windows.net/",
  "portal": "https://portal.azure.com",
  "graphAudience": "https://graph.windows.net/",
  "activeDirectoryDataLake": "https://datalake.azure.net/",
  "batch": "https://batch.core.windows.net/",
  "media": "https://rest.media.azure.net",
  "sqlManagement": "https://management.core.windows.net:8443/",
  "vmImageAliasDoc": "https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json",
  "resourceManager": "https://management.azure.com/",
  "authentication": {
    "loginEndpoint": "https://login.windows.net/",
    "audiences": [
      "https://management.core.windows.net/",
      "https://management.azure.com/"
    ],
    "tenant": "common",
    "identityProvider": "AAD"
  },
  "suffixes": {
    "acrLoginServer": ".azurecr.io",
    "azureDatalakeAnalyticsCatalogAndJob": "azuredatalakeanalytics.net",
    "azureDatalakeStoreFileSystem": "azuredatalakestore.net",
    "azureFrontDoorEndpointSuffix": "azurefd.net",
    "keyvaultDns": ".vault.azure.net",
    "sqlServerHostname": ".database.windows.net",
    "storage": "core.windows.net"
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Abrufen von Werten aus Ressourcen, Ressourcengruppen oder Abonnements finden Sie unter [Ressourcenfunktionen](./bicep-functions-resource.md).
