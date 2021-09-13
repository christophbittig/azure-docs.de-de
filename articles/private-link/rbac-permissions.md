---
title: Azure RBAC-Berechtigungen für Azure Private Link
description: Sie erhalten Informationen zu den Azure RBAC-Berechtigungen, die zum Bereitstellen eines privaten Endpunkts und eines Private Link-Diensts erforderlich sind.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: conceptual
ms.date: 5/25/2021
ms.custom: template-concept
ms.openlocfilehash: 50b648c5419b995f9c808288895af49b12f29b86
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122350288"
---
# <a name="azure-rbac-permissions-for-azure-private-link"></a>Azure RBAC-Berechtigungen für Azure Private Link

Die Zugriffsverwaltung für Cloudressourcen ist eine wichtige Funktion für jede Organisation. Mit der rollenbasierten Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC) wird der Zugriff auf und Betrieb von Azure-Ressourcen verwaltet.

Zum Bereitstellen eines privaten Endpunkts oder Private Link-Diensts muss einem Benutzer eine integrierte Rolle zugewiesen sein, z. B.: 

* [Besitzer](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fprivate-link%2ftoc.json#owner)
* [Mitwirkender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fprivate-link%2ftoc.json#contributor)
* [Netzwerkmitwirkender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fprivate-link%2ftoc.json#network-contributor)

Sie können einen präziseren Zugriff bereitstellen, indem Sie eine [benutzerdefinierte Rolle](../role-based-access-control/custom-roles.md?toc=%2fazure%2fprivate-link%2ftoc.json) mit den in den folgenden Abschnitten beschriebenen Berechtigungen erstellen.

> [!IMPORTANT]
> In diesem Artikel werden die spezifischen Berechtigungen zum Erstellen eines privaten Endpunkts oder Private Link-Diensts aufgeführt. Stellen Sie sicher, dass Sie die spezifischen Berechtigungen für den Dienst hinzufügen, dem Sie Zugriff über Private Link gewähren möchten, z. B. Microsoft. SQL Rolle „Mitwirkender“ für Azure SQL. Weitere Informationen zu integrierten Rollen finden Sie im Artikel über die [rollenbasierte Zugriffssteuerung in Azure](../role-based-access-control/built-in-roles.md).

Microsoft.Network und der spezifische Ressourcenanbieter, den Sie bereitstellen, z. B. Microsoft.Sql, müssen auf Abonnementebene registriert werden:

![image](https://user-images.githubusercontent.com/20302679/129105527-b946eee9-038a-46ef-b446-be371eb23ca9.png)

## <a name="private-endpoint"></a>Privater Endpunkt

In diesem Abschnitt werden die detaillierten Berechtigungen aufgeführt, die zum Bereitstellen eines privaten Endpunkts erforderlich sind.

| Aktion                                                              | BESCHREIBUNG                                                                      |
| ---------                                                           | -------------                                                                 |
| Microsoft.Resources/deployments/*                                   | Erstellen und Verwalten einer Bereitstellung                                                |
| Microsoft.Resources/subscriptions/resourcegroups/resources/read     | Liest die Ressourcen für die Ressourcengruppe                                     |
| Microsoft.Network/virtualNetworks/read                              | Liest die Definition des virtuellen Netzwerks                                            |
| Microsoft.Network/virtualNetworks/subnets/read                      | Liest eine Subnetzdefinition für virtuelle Netzwerke aus                                      |
| Microsoft.Network/virtualNetworks/subnets/write                     | Erstellt ein Subnetz für virtuelle Netzwerke oder aktualisiert ein vorhandenes Subnetz für virtuelle Netzwerke.|
| Microsoft.Network/virtualNetworks/subnets/join/action               | Verknüpft ein virtuelles Netzwerk.                                                       |
| Microsoft.Network/privateEndpoints/read                             | Liest eine private Endpunktressource                                             |
| Microsoft.Network/privateEndpoints/write                            | Erstellt einen neuen privaten Endpunkt oder aktualisiert einen vorhandenen privaten Endpunkt       |
| Microsoft.Network/locations/availablePrivateEndpointTypes/read      | Liest die verfügbaren privaten Endpunktressourcen                                     |

Hier ist das JSON-Format der oben genannten Berechtigungen angegeben. Geben Sie Ihren eigenen roleName, description und assignableScopes ein:

```JSON
{
 "properties": {
   "roleName": "Role Name",
   "description": "Description",
   "assignableScopes": [
     "/subscriptions/SubscriptionID/resourceGroups/ResourceGroupName"
   ],
   "permissions": [
     {
       "actions": [
         "Microsoft.Resources/deployments/*",
         "Microsoft.Resources/subscriptions/resourceGroups/read",
         "Microsoft.Network/virtualNetworks/read",
         "Microsoft.Network/virtualNetworks/subnets/read",
         "Microsoft.Network/virtualNetworks/subnets/write",
         "Microsoft.Network/virtualNetworks/subnets/join/action",
         "Microsoft.Network/privateEndpoints/read",
         "Microsoft.Network/privateEndpoints/write",
         "Microsoft.Network/locations/availablePrivateEndpointTypes/read"
       ],
       "notActions": [],
       "dataActions": [],
       "notDataActions": []
     }
   ]
 }
}
```

## <a name="private-link-service"></a>Private Link-Dienst

In diesem Abschnitt werden die detaillierten Berechtigungen aufgeführt, die zum Bereitstellen eines Private Link-Diensts erforderlich sind.

| Aktion | BESCHREIBUNG   |
| --------- | ------------- |
| Microsoft.Resources/deployments/*                                   | Erstellen und Verwalten einer Bereitstellung                                                |
| Microsoft.Resources/subscriptions/resourcegroups/resources/read     | Liest die Ressourcen für die Ressourcengruppe                                     |
| Microsoft.Network/virtualNetworks/read                              | Liest die Definition des virtuellen Netzwerks                                            |
| Microsoft.Network/virtualNetworks/subnets/read                      | Liest eine Subnetzdefinition für virtuelle Netzwerke aus                                      |
| Microsoft.Network/virtualNetworks/subnets/write                     | Erstellt ein Subnetz für virtuelle Netzwerke oder aktualisiert ein vorhandenes Subnetz für virtuelle Netzwerke.|
| Microsoft.Network/privateLinkServices/read | Liest eine Private Link-Dienstressource|
| Microsoft.Network/privateLinkServices/write | Erstellt einen neuen Private Link-Dienst oder aktualisiert einen vorhandenen Private Link-Dienst.|
| Microsoft.Network/privateLinkServices/privateEndpointConnections/read | Liest die Definition einer privaten Endpunktverbindung |
| Microsoft.Network/privateLinkServices/privateEndpointConnections/write | Erstellt eine neue private Endpunktverbindung oder aktualisiert eine vorhandene private Endpunktverbindung|
| Microsoft.Network/networkSecurityGroups/join/action | Verknüpft eine Netzwerksicherheitsgruppe. |
| Microsoft.Network/loadBalancers/read | Liest eine Lastenausgleichsdefinition |
| Microsoft.Network/loadBalancers/write | Erstellt einen Lastenausgleich oder aktualisiert einen vorhandenen Lastenausgleich. |

```JSON
{
  "properties": {
    "roleName": "Role Name",
    "description": "Description",
    "assignableScopes": [
      "/subscriptions/SubscriptionID/resourceGroups/ResourceGroupName"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Network/virtualNetworks/read",
          "Microsoft.Network/virtualNetworks/subnets/read",
          "Microsoft.Network/virtualNetworks/subnets/write",
          "Microsoft.Network/virtualNetworks/subnets/join/action",
          "Microsoft.Network/privateLinkServices/read",
          "Microsoft.Network/privateLinkServices/write",
          "Microsoft.Network/privateLinkServices/privateEndpointConnections/read",
          "Microsoft.Network/privateLinkServices/privateEndpointConnections/write",
          "Microsoft.Network/networkSecurityGroups/join/action",
          "Microsoft.Network/loadBalancers/read",
          "Microsoft.Network/loadBalancers/write"
        ],
        "notActions": [],
        "dataActions": [],
        "notDataActions": []
      }
    ]
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu privaten Endpunkten und Private Link-Diensten in Azure Private Link finden Sie unter:

- [Was ist der private Azure-Endpunkt?](private-endpoint-overview.md)
- [Was ist der Azure Private Link-Dienst?](private-link-service-overview.md)
