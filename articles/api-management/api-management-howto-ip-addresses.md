---
title: IP-Adressen des Azure API Management-Diensts | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie die IP-Adressen eines Azure API Management-Diensts abrufen und wann sie sich ändern.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 04/13/2021
ms.author: danlep
ms.openlocfilehash: d0fe9c305b9592aad7b08437d6e9d14150748ed6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128680735"
---
# <a name="ip-addresses-of-azure-api-management"></a>IP-Adressen von Azure API Management

In diesem Artikel erfahren Sie, wie Sie die IP-Adressen des Azure API Management-Diensts abrufen. IP-Adressen können öffentlich oder privat sein, wenn sich der Dienst in einem virtuellen Netzwerk befindet.

Mithilfe von IP-Adressen können Sie Firewallregeln erstellen, den eingehenden Datenverkehr für die Back-End-Dienste filtern oder den ausgehenden Datenverkehr einschränken.

## <a name="ip-addresses-of-api-management-service"></a>IP-Adressen des API Management-Diensts

Jede API Management-Dienstinstanz im Developer-, Basic-, Standard-oder Premium-Tarif hat öffentliche IP-Adressen, die ausschließlich für diese Dienstinstanz gelten (sie werden nicht mit anderen Ressourcen gemeinsam genutzt). 

Sie können die IP-Adressen aus dem Übersichtsdashboard ihrer Ressource im Azure-Portal abrufen.

![IP-Adresse von API Management](media/api-management-howto-ip-addresses/public-ip.png)

Sie können sie auch programmgesteuert abrufen. Verwenden Sie dazu den folgenden API-Aufruf:

```
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>
```

Die öffentlichen IP-Adressen sind in der Antwort enthalten:

```json
{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.77.143.53"
    ],
    ...
  }
  ...
}
```

Bei [Bereitstellungen in mehreren Regionen](api-management-howto-deploy-multi-region.md) verfügt jede regionale Bereitstellung über eine einzelne öffentliche IP-Adresse.

## <a name="ip-addresses-of-api-management-service-in-vnet"></a>IP-Adressen des API Management-Diensts in einem VNet

Wenn sich Ihr API Management-Dienst innerhalb eines virtuellen Netzwerks befindet, besitzt er zwei Arten von IP-Adressen: öffentliche und private.

Öffentliche IP-Adressen werden für die interne Kommunikation über den Port `3443` verwendet, um die Konfiguration zu verwalten (beispielsweise über Azure Resource Manager). In der externen VNet-Konfiguration werden sie auch für API-Laufzeitdatenverkehr verwendet. Wenn eine Anforderung von API Management an ein öffentliches Back-End (Back-End mit Internetzugriff) gesendet wird, wird als Ursprung der Anforderung eine öffentliche IP-Adresse angezeigt.

Private virtuelle IP-Adressen (VIPs), die **nur** im [Modus „internes VNet“](api-management-using-with-internal-vnet.md) verfügbar sind, werden verwendet, um innerhalb des Netzwerks eine Verbindung mit API Management-Endpunkten (Gateways, das Entwicklerportal und die Verwaltungsebene für direkten API-Zugriff) herzustellen. Sie können verwendet werden, um DNS-Einträge im Netzwerk einzurichten.

Im Azure-Portal und in der Antwort des API-Aufrufs werden Adressen beider Arten angezeigt:

![IP-Adresse von API Management in einem VNet](media/api-management-howto-ip-addresses/vnet-ip.png)


```json
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>

{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.85.20.170"
    ],
    "privateIPAddresses": [
      "192.168.1.5"
    ],
    ...
  },
  ...
}
```

API Management verwendet eine öffentliche IP-Adresse für Verbindungen außerhalb des VNet und eine private IP-Adresse für Verbindungen innerhalb des VNet.

## <a name="ip-addresses-of-consumption-tier-api-management-service"></a>IP-Adressen des API Management-Diensts im Consumption-Tarif

Wenn es sich bei Ihrem API Management-Dienst um einen Dienst im Consumption-Tarif handelt, besitzt er keine dedizierte IP-Adresse. Dienste im Consumption-Tarif werden in einer gemeinsam genutzten Infrastruktur und ohne deterministische IP-Adresse ausgeführt. 

Datenverkehr kann mithilfe des IP-Adressbereichs von Azure-Rechenzentren eingeschränkt werden. Die genauen Schritte werden in [diesem Artikel der Azure Functions-Dokumentation](../azure-functions/ip-addresses.md#data-center-outbound-ip-addresses) beschrieben.

## <a name="changes-to-the-ip-addresses"></a>Änderungen an den IP-Adressen

Im Developer-, Basic-, Standard- und Premium-Tarif von API Management sind die öffentlichen IP-Adressen (VIPs) für die Lebensdauer eines Diensts statisch – mit Ausnahme von folgenden Fällen:

* Der Dienst wird gelöscht und anschließend neu erstellt.
* Das Dienstabonnement wird [gesperrt](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states), oder es wird eine diesbezügliche [Warnung](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) ausgegeben (z.B. aufgrund von nicht geleisteten Zahlungen), und anschließend wird es reaktiviert.
* Azure Virtual Network wird dem Dienst hinzugefügt oder daraus entfernt.
* Der API Management-Dienst wird zwischen dem externen und internen VNet-Bereitstellungsmodus umgeschaltet.

Bei [Bereitstellungen in mehreren Regionen](api-management-howto-deploy-multi-region.md) ändert sich die regionale IP-Adresse, wenn eine Region verlassen und anschließend reaktiviert wird. Die regionale IP-Adresse ändert sich auch, wenn Sie [Verfügbarkeitszonen](zone-redundancy.md) aktivieren, hinzufügen oder entfernen.
