---
title: Azure ExpressRoute CrossConnnections Schaltkreisplatzierung API
description: Dieser Artikel bietet ExpressRoute-Partnern einen detaillierten Überblick über die ExpressRoute CrossConnections API für die Schaltungsplatzierung.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/19/2021
ms.author: mialdrid
ms.openlocfilehash: 3b464e0d9bb5c770109899f7aba8c9e0934d2064
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095956"
---
# <a name="expressroute-circuit-placement-api"></a>ExpressRoute-API für die Schaltungsplatzierung

Die ExpressRoute Partner Circuit Placement API ermöglicht es ExpressRoute-Partnern, Circuit-Konnektivität für ein bestimmtes Portpaar bereitzustellen. Wenn ein ExpressRoute-Partner mehrere Portpaare an einem Peering-Standort verwaltet, kann er diese API verwenden, um auszuwählen, welches Portpaar die ExpressRoute-Schaltung ermöglicht.

Diese API verwendet den Ressourcentyp expressRouteCrossConnection. Weitere Informationen finden Sie unter [ExpressRoute CrossConnection API Entwicklung und Integration](cross-connections-api-development.md).‘

## <a name="workflow"></a>Workflow

1. ExpressRoute-Kunden teilen sich den Serviceschlüssel der ExpressRoute-Zielschaltung.

1. Der ExpressRoute-Partner führt einen GET mit der expressRouteProviderPorts API aus, um die **PortPairDescription** des Zielportpaares zu identifizieren. Der ExpressRoute-Partner kann eine Liste von PortPairDescriptions über alle Portpaare im Abonnement abfragen oder die Abfrage auf einen bestimmten Peering-Standort beschränken.

1. Sobald die Ziel-PortPairDescription identifiziert ist, führt der ExpressRoute-Partner GET/PUT expressRouteCrossConnection aus, um die ExpressRoute-Schaltung auf das Ziel-Port-Paar zu verschieben.

ExpressRoute-Partner verwalten die Layer-2- und Layer-3-Konfiguration, indem sie REST-Vorgänge für die expressRouteCrossConnections-Ressource ausgeben.

## <a name="api-development-and-integration-steps"></a>Schritte der API-Entwicklung und -Integration

### <a name="get-using-the-expressrouteproviderports-api-to-list-port-pairs"></a>GET mithilfe der expressRouteProviderPorts-API zum Auflisten von Portpaaren

Der ExpressRoute-Partner kann alle Portpaare innerhalb des Abonnements des Zielanbieters auflisten, oder er kann die Portpaare innerhalb eines bestimmten Peering-Standorts auflisten,

### <a name="to-get-a-list-of-all-port-pairs-for-a-provider"></a>So erhalten Sie eine Liste aller Portpaare für einen Anbieter

https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Network/expressRouteProviderPorts 

#### <a name="get-operation"></a>Operation erhalten

```rest
{
    "parameters": {
      "api-version": "2020-03-01",
      "subscriptionId": "subid"
    },
    "responses": {
        "200": {
          "body": {
            "value": [
              {
                "portPairDescriptor": "bvtazureixpportpair1",
                "id": "/subscriptions/subid/providers/Microsoft.Network/ExpressRouteProviderPort/bvtazureixpportpair1",
                "type": "Microsoft.Network/expressRouteProviderPort",
                "location": "uswest",
                "etag": "W/\"c0e6477e-8150-4d4f-9bf6-bb10e6acb63a\"",
                "properties": {
                    "portPairDescriptor": "bvtazureixpportpair",
                    "primaryAzurePort": "bvtazureixp01a",
                    "secondaryAzurePort": "bvtazureixp01b",
                    "peeringLocation": "SiliconValley",
                    "overprovisionFactor": 4,
                    "portBandwidthInMbps": 4000,
                    "usedBandwidthInMbps": 2500,
                    "remainingBandwidthInMbps": 1500
                }
              },
              {
                "portPairDescriptor": "bvtazureixpportpair2",
                "id": "/subscriptions/subid/providers/Microsoft.Network/ ExpressRouteProviderPort/bvtazureixpportpair2",
                "type": "Microsoft.Network/expressRouteProviderPort",
                "location": "uswest",
                "etag": "W/\"c0e6477e-8150-4d4f-9bf6-bb10e6acb63a\"",
                "properties": {
                    "portPairDescriptor": "bvtazureixpportpair2",
                    "primaryAzurePort": "bvtazureixp02a",
                    "secondaryAzurePort": "bvtazureixp02b",
                    "peeringLocation": "seattle",
                    "overprovisionFactor": 4,
                    "portBandwidthInMbps": 4000,
                    "usedBandwidthInMbps": 1200,
                    "remainingBandwidthInMbps": 1800
                }
              }
            ]
          }
        }
      }
    }
  }
}
```

**Antwort-Statuscode**

* 200 (OK) Die Anfrage ist erfolgreich. Es wird eine Liste der Ports abgerufen.
* 4XX (Bad Request) Eine der Validierungen ist fehlgeschlagen - zum Beispiel: Die Subid des Anbieters ist ungültig.

### <a name="list-of-all-port-for-a-provider-for-a-particular-peering-location"></a>Liste aller Ports eines Anbieters für einen bestimmten Peering-Standort

#### <a name="get"></a>GET

https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Network/expressRouteProviderPorts?location={locationName}

#### <a name="get-operation"></a>GET-Vorgang

```rest
{
  "parameters": {
    "api-version": "2020-03-01",
    "locationName": "SiliconValley",
    "subscriptionId": "subid"
  },
  "responses": {
    "200": {
      "body": {
        "value": [
          {
            "portPairDescriptor": "bvtazureixpportpair1",
            "id": "/subscriptions/subid/providers/Microsoft.Network/ ExpressRouteProviderPort /bvtazureixpportpair1",
            "type": "Microsoft.Network/expressRouteProviderPort",
            "location": "uswest",
            "etag": "W/\"c0e6477e-8150-4d4f-9bf6-bb10e6acb63a\"",
            "properties": {
              "portPairDescriptor": "bvtazureixpportpair",
              "primaryAzurePort": "bvtazureixp01a",
              "secondaryAzurePort": "bvtazureixp01b",
              "peeringLocation": "SiliconValley",
              "overprovisionFactor": 4,
              "portBandwidthInMbps": 4000,
              "usedBandwidthInMbps": 2500,
              "remainingBandwidthInMbps": 1500
            }
          }
        ]
      }
    }
  }
}
```

**Antwort-Statuscode**

* 200 (OK) Die Anfrage ist erfolgreich. Es wird eine Liste der Ports abgerufen.
* 4XX (Bad Request) Eine der Validierungen ist fehlgeschlagen - zum Beispiel: Die Subid des Anbieters ist ungültig oder der Standort ist ungültig.

Zum Abrufen von Anschlussdetails eines bestimmten Anschlusses unter Verwendung der Anschlusspaar-Deskriptor-ID.

#### <a name="get"></a>GET

https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Network/expressRouteProviderPorts/{portPairDescriptor}

#### <a name="get-operation"></a>GET-Vorgang

```rest
{
  "parameters": {
    "api-version": "2020-03-01",
    "portPairDescriptor": " bvtazureixpportpair1",
    "subscriptionId": "subid"
  },
  "responses": {
    "200": {
      "body": {
        "value": [
          {
            "portPairDescriptor": "bvtazureixpportpair1",
            "id": "/subscriptions/subid/providers/Microsoft.Network/ExpressRouteProviderPort/bvtazureixpportpair1",
            "type": "Microsoft.Network/expressRouteProviderPort",
            "location": "uswest",
            "etag": "W/\"c0e6477e-8150-4d4f-9bf6-bb10e6acb63a\"",
            "properties": {
              "portPairDescriptor": "bvtazureixpportpair",
              "primaryAzurePort": "bvtazureixp01a",
              "secondaryAzurePort": "bvtazureixp01b",
              "peeringLocation": "SiliconValley",
              "overprovisionFactor": 4,
              "portBandwidthInMbps": 4000,
              "usedBandwidthInMbps": 2500,
              "remainingBandwidthInMbps": 15
            }
          }
        ]
      }
    }
  }
}
```

**Statuscode Beschreibung**

* 200 (OK) Die Anfrage ist erfolgreich. Er holt die Details des Anschlusses.
* 204 Das Anschlusspaar mit der genannten Deskriptor-ID ist nicht verfügbar.
* 4XX (Bad Request) Eine der Validierungen ist fehlgeschlagen - Zum Beispiel: Die Subid des Anbieters ist ungültig.

### <a name="put-expressroutecrossconnection-api-to-move-a-circuit-to-a-specific-port-pair"></a>PUT expressRouteCrossConnection API zum Verschieben eines Stromkreises zu einem bestimmten Anschlusspaar

Sobald der portPairDescriptor des Zielportpaares identifiziert ist, kann der ExpressRoute-Partner die [ExpressRouteCrossConnection API](/rest/api/expressroute/express-route-cross-connections/create-or-update) verwenden, um die ExpressRoute-Schaltung zu einem bestimmten Portpaar zu verschieben.

Derzeit wird diese API von Anbietern verwendet, um den Bereitstellungsstatus von Leitungen zu aktualisieren. Dieselbe API wird von den Anbietern verwendet, um das Portpaar der Schaltung zu aktualisieren.

Derzeit sind die Eigenschaften primaryAzurePort und secondaryAzurePort schreibgeschützt. Jetzt haben wir die Nur-Lese-Eigenschaften für diese Ports deaktiviert.

#### <a name="put"></a>PUT

https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/expressRouteCrossConnections/{crossConnectionName}?api-version=2021-02-01

#### <a name="put-operation"></a>PUT-Operation

```rest
{
"parameters": {
    "api-version": "2020-03-01",
    "crossConnectionName": "The name of the cross connection",
    "subscriptionId": "subid"
  }
},
{
Request   "body": {
    " primaryAzurePort ": " bvtazureixp03a"
     "secondaryAzurePort": "bvtazureixp03b",
  }
}
Response:
{
  "name": "<circuitServiceKey>",
  "id": "/subscriptions/subid/resourceGroups/CrossConnectionSiliconValley/providers/Microsoft.Network/expressRouteCrossConnections/<circuitServiceKey>",
  "type": "Microsoft.Network/expressRouteCrossConnections",
  "location": "brazilsouth",
  "properties": {
    "provisioningState": "Enabled",
    "expressRouteCircuit": {
      "id": "/subscriptions/subid/resourceGroups/ertest/providers/Microsoft.Network/expressRouteCircuits/er1"
    },
    "peerings": [],
    "peeringLocation": "SiliconValley",
    "bandwidthInMbps": 1000,
    "primaryAzurePort": "bvtazureixp03a",
    "secondaryAzurePort": "bvtazureixp03b",
    "sTag": 2,
    "serviceProviderProvisioningState": "NotProvisioned"
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu allen ExpressRoute-REST-APIs finden Sie unter [ExpressRoute-REST-APIs](/rest/api/expressroute/).