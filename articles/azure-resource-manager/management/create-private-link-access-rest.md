---
title: Verwalten von Ressourcen über eine private Verbindung
description: Einschränken des Verwaltungszugriffs für Ressourcen auf private Verbindungen
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: eb7e81ef739fdb94bc91c65b079d5b76b22636c9
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227342"
---
# <a name="use-rest-api-to-create-private-link-for-managing-azure-resources"></a>Verwenden der REST-API zum Erstellen einer privaten Verbindung zum Verwalten von Azure-Ressourcen

In diesem Artikel wird erläutert, wie Sie [Azure Private Link](../../private-link/index.yml) verwenden können, um den Zugriff für die Verwaltung von Ressourcen in Ihren Abonnements einzuschränken.

[!INCLUDE [Create content](../../../includes/resource-manager-create-rmpl.md)]

## <a name="create-resource-management-private-link"></a>Erstellen einer privaten Ressourcenmanagementverbindung

Zum Erstellen einer private Ressourcenmanagementverbindung senden Sie die folgende Anforderung:

```http
PUT
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{rmplName}?api-version=2020-05-01
```

Nehmen Sie in den Anforderungstext den für die Ressource gewünschten Standort auf:

```json
{
  "location":"{region}"
}
```

Der Vorgang gibt Folgendes zurück:

```json
{  
  "id": "/subscriptions/{subID}/resourceGroups/{rgName}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{name}",
  "location": "{region}",
  "name": "{rmplName}",
  "properties": {
    "privateEndpointConnections": []
  },
  "resourceGroup": "{rgName}",
  "type": "Microsoft.Authorization/resourceManagementPrivateLinks"
}
```

Notieren Sie sich die ID, die für die neue private Ressourcenmanagementverbindung zurückgegeben wird. Sie verwenden sie zum Erstellen der Zuordnung der privaten Verbindung.

## <a name="create-private-link-association"></a>Erstellen einer Zuordnung einer privaten Verbindung

Verwenden Sie zum Erstellen einer Zuordnung einer privaten Verbindung:

```http
PUT
https://management.azure.com/providers/Microsoft.Management/managementGroups/{managementGroupId}/providers/Microsoft.Authorization/privateLinkAssociations/{GUID}?api-version=2020-05-01 
```

Fügen Sie Folgendes in den Anforderungstext ein:

```json
{
  "properties": {
    "privateLink": "/subscriptions/{subscription-id}/resourceGroups/{rg-name}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{rmplName}",
    "publicNetworkAccess": "enabled"
  }
}
```

Der Vorgang gibt Folgendes zurück:

```json
{
  "id": {plaResourceId},
  "name": {plaName},
  "properties": {
    "privateLink": {rmplResourceId},
    "publicNetworkAccess": "Enabled",
    "tenantId": "{tenantId}",
    "scope": "/providers/Microsoft.Management/managementGroups/{managementGroupId}"
  },
  "type": "Microsoft.Authorization/privateLinkAssociations"
}
```

## <a name="add-private-endpoint"></a>Privaten Endpunkt hinzufügen

In diesem Artikel wird davon ausgegangen, dass Sie bereits über ein virtuelles Netzwerk verfügen. In dem Subnetz, das für den privaten Endpunkt verwendet wird, müssen Sie Netzwerkrichtlinien für private Endpunkte deaktivieren. Wenn Sie Netzwerkrichtlinien für private Endpunkte nicht deaktiviert haben, finden Sie weitere Informationen unter [Deaktivieren von Netzwerkrichtlinien für private Endpunkte](../../private-link/disable-private-endpoint-network-policy.md).

Verwenden Sie den folgenden Vorgang, um einen privaten Endpunkt zu erstellen:

```http
PUT
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/privateEndpoints/{privateEndpointName}?api-version=2020-11-01
```

Legen Sie im Anforderungstext die `privateServiceLinkId` auf die ID Ihrer privaten Ressourcenmanagementverbindung fest. Die `groupIds` müssen `ResourceManagement` enthalten. Der Standort des privaten Endpunkts muss mit dem Standort des Subnetzes identisch sein.

```json
{
  "location": "westus2",
  "properties": {
    "privateLinkServiceConnections": [
      {
        "name": "{connection-name}",
        "properties": {
           "privateLinkServiceId": "/subscriptions/{subID}/resourceGroups/{rgName}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{name}",
           "groupIds": [
              "ResourceManagement"
           ]
         }
      }
    ],
    "subnet": {
      "id": "/subscriptions/{subID}/resourceGroups/{rgName}/providers/Microsoft.Network/virtualNetworks/{vnet-name}/subnets/{subnet-name}"
    }
  }
}
```

Der nächste Schritt variiert je nachdem, ob Sie automatische oder manuelle Genehmigung verwenden. Weitere Informationen zur Genehmigung finden Sie unter [Zugriff auf eine Private Link-Ressource mithilfe des Genehmigungsworkflows](../../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow).

Die Antwort enthält den Genehmigungszustand.

```json
"privateLinkServiceConnectionState": {
    "actionsRequired": "None",
    "description": "",
    "status": "Approved"
},
```

Wenn Ihre Anforderung automatisch genehmigt wird, können Sie mit dem nächsten Abschnitt fortfahren. Wenn Ihre Anforderung eine manuelle Genehmigung erfordert, warten Sie, bis der Netzwerkadministrator Ihre Verbindung mit dem privaten Endpunkt genehmigt.

## <a name="next-steps"></a>Nächste Schritte

Um mehr über private Verbindungen zu lernen, siehe [Azure Private Link](../../private-link/index.yml).