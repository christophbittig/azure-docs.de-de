---
title: Verwalten von privaten Verbindungen zur Ressourcenverwaltung
description: Verwenden Sie REST API, um bestehende private Verbindung zur Ressourcenverwaltung zu verwalten
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: 376fa600cce4479a0ed2c04e9d3d7b3fbeb9aeaa
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227344"
---
# <a name="manage-resource-management-private-links-with-rest-api"></a>Verwalten von privaten Verbindungen zur Ressourcenverwaltung mit REST API

Dieser Artikel erklärt, wie Sie mit bestehenden privaten Verbindungen zur Ressourcenverwaltung arbeiten können. Er zeigt die REST-API-Vorgänge zum Abrufen und Löschen vorhandener Ressourcen.

Wenn Sie einen privaten Verbindungen für die Ressourcenverwaltung erstellen müssen, lesen Sie [Verwenden von Portal zum Erstellen einer privaten Verbindung zum Verwalten von Azure-Ressourcen](create-private-link-access-portal.md) oder [Verwenden von REST API zum Erstellen einer privaten Verbindung zum Verwalten von Azure-Ressourcen](create-private-link-access-rest.md).

## <a name="resource-management-private-links"></a>Private Verbindungen der Ressourcenverwaltung

Zum **Erhalt einer bestimmten** Privaten Verbindung der Ressourcenverwaltung senden Sie die folgende Anforderung:

```http
GET
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{rmplName}?api-version=2020-05-01 
```

Die Operation gibt zurück:

```json
{
  "properties": {
    "privateEndpointConnections": []
  },
  "id": {rmplResourceId},
  "name": {rmplName},
  "type": "Microsoft.Authorization/resourceManagementPrivateLinks",
  "location": {region}
}
```

**Zum Erhalt aller** privaten Verbindungen der Ressourcenverwaltung in einem Abonnement, verwenden Sie:

```http
GET
https://management.azure.com/subscriptions/{subscriptionID}/providers/Microsoft.Authorization/resourceManagementPrivateLinks?api-version=2020-05-01
```

Die Operation gibt zurück:

```json
[
  {
    "properties": {
      "privateEndpointConnections": []
    },
    "id": {rmplResourceId},
    "name": {rmplName},
    "type": "Microsoft.Authorization/resourceManagementPrivateLinks",
    "location": {region}
  },
  {
    "properties": {
      "privateEndpointConnections": []
    },
    "id": {rmplResourceId},
    "name": {rmplName},
    "type": "Microsoft.Authorization/resourceManagementPrivateLinks",
    "location": {region}
  }
]
```

Zum **Löschen einer bestimmten** privaten Verbindung der Ressourcenverwaltung verwenden Sie:

```http
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{rmplName}?api-version=2020-05-01
```

Der Vorgang gibt zurück: `Status 200 OK`.

## <a name="private-link-association"></a>Zuordnung einer privaten Verbindung

Zum **Erhalt einer bestimmten** Zuordnung einer privaten Verbindung für eine Verwaltungsgruppe, verwenden Sie:

```http
GET
https://management.azure.com/providers/Microsoft.Management/managementGroups/{managementGroupID}/providers/Microsoft.Authorization/privateLinkAssociations?api-version=2020-05-01 
```

Der Vorgang gibt zurück:

```json
{
  "value": [
    {
      "properties": {
        "privateLink": {rmplResourceID},
        "tenantId": {tenantId},
        "scope": "/providers/Microsoft.Management/managementGroups/{managementGroupId}"
      },
      "id": {plaResourceId},
      "type": "Microsoft.Authorization/privateLinkAssociations",
      "name": {plaName}
    }
  ]
}
```

Zum **Löschen** eine privaten Verbindung verwenden Sie:

```http
DELETE 
https://management.azure.com/providers/Microsoft.Management/managementGroups/{managementGroupID}/providers/Microsoft.Authorization/privateLinkAssociations/{plaID}?api-version=2020-05-01
```

Der Vorgang gibt zurück: `Status 200 OK`.

## <a name="private-endpoints"></a>Private Endpunkte

**Zum Erhalt aller** privaten Endpunkte in einem Abonnement verwenden Sie:

```http
GET 
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Network/privateEndpoints?api-version=2020-04-01
```

Der Vorgang gibt zurück:

```json
{
  "value": [
    {
      "name": {privateEndpointName},
      "id": {privateEndpointResourceId},
      "etag": {etag},
      "type": "Microsoft.Network/privateEndpoints",
      "location": {region},
      "properties": {
        "provisioningState": "Updating",
        "resourceGuid": {GUID},
        "privateLinkServiceConnections": [
          {
            "name": {connectionName},
            "id": {connectionResourceId},
            "etag": {etag},
            "properties": {
              "provisioningState": "Succeeded",
              "privateLinkServiceId": {rmplResourceId},
              "groupIds": [
                "ResourceManagement"
              ],
              "privateLinkServiceConnectionState": {
                "status": "Approved",
                "description": "",
                "actionsRequired": "None"
              }
            },
            "type": "Microsoft.Network/privateEndpoints/privateLinkServiceConnections"
          }
        ],
        "manualPrivateLinkServiceConnections": [],
        "subnet": {
          "id": {subnetResourceId}
        },
        "networkInterfaces": [
          {
            "id": {networkInterfaceResourceId}
          }
        ],
        "customDnsConfigs": [
          {
            "fqdn": "management.azure.com",
            "ipAddresses": [
              "10.0.0.4"
            ]
          }
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Nächste Schritte

* Um mehr über private Verbindungen zu lernen, siehe [Azure Private Link](../../private-link/index.yml).
* Um eine privaten Verbindung der Ressourcenverwaltung zu erstellen, siehe [ Verwenden von Portal zum Erstellen einer privaten Verbindung zum Verwalten von Azure-Ressourcen](create-private-link-access-portal.md) oder [Verwenden von REST API zum Erstellen einer privaten Verbindung zum Verwalten von Azure-Ressourcen](create-private-link-access-rest.md).