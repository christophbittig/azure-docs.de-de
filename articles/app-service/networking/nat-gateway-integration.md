---
title: NAT Gateway-Integration – Azure App Service | Microsoft-Dokumentation
description: Hier finden Sie Informationen zur NAT Gateway-Integration in Azure App Service.
services: app-service
author: madsd
ms.assetid: 0a84734e-b5c1-4264-8d1f-77e781b28426
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 08/04/2021
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: e5a6f2fe52a9b74f74047a5a9512e40bb267161f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130245282"
---
# <a name="virtual-network-nat-gateway-integration"></a>Virtual Network NAT Gateway-Integration

Das NAT-Gateway ist ein vollständig verwalteter, äußerst resilienter Dienst, der einem oder mehreren Subnetzen zugeordnet werden kann, und der sicherstellt, dass der gesamte ausgehende Datenverkehr mit Internetanbindung über das Gateway geleitet wird. Bei App Service gibt es zwei wichtige Szenarien, in denen Sie das NAT-Gateway einsetzen können. 

Das NAT-Gateway stellt Ihnen eine statische vorhersagbare öffentliche IP-Adresse für den ausgehenden Datenverkehr mit Internetanbindung zur Verfügung. Außerdem werden die verfügbaren [SNAT-Ports](../troubleshoot-intermittent-outbound-connection-errors.md) in Szenarien, in denen eine hohe Anzahl gleichzeitiger Verbindungen mit derselben Kombination aus öffentlicher Adresse und Port hergestellt werden, erheblich erhöht.

Weitere Informationen und Preise finden Sie in der [NAT-Gateway-Übersicht](../../virtual-network/nat-gateway/nat-overview.md).

:::image type="content" source="./media/nat-gateway-integration/nat-gateway-overview.png" alt-text="Diagramm: Internetdatenverkehr, der in einer Azure Virtual Network-Instanz zu einem NAT-Gateway fließt":::

> [!Note] 
> Die Verwendung des NAT-Gateways mit App Service ist von der regionalen VNET-Integration abhängig. Deshalb ist ein App Service-Plan der Kategorie **Standard**, **Premium**, **PremiumV2** oder **PremiumV3** erforderlich.

## <a name="configuring-nat-gateway-integration"></a>Konfigurieren der NAT-Gatewayintegration

Um die NAT-Gatewayintegration mit App Service konfigurieren zu können, müssen Sie die folgenden Schritte ausführen:

* Konfigurieren Sie die regionale VNET-Integration mit Ihrer App wie unter [Integrieren Ihrer App in ein Azure Virtual Network](../overview-vnet-integration.md) beschrieben.
* Stellen Sie sicher, dass [Route All](../overview-vnet-integration.md#routes) (Gesamten Datenverkehr weiterleiten) für Ihre VNET-Integration aktiviert ist, damit der Internet-Datenverkehr entsprechend der Routen in Ihrem VNet ebenfalls weitergeleitet wird.
* Stellen Sie ein NAT-Gateway mit einer öffentlichen IP-Adresse bereit und ordnen Sie es dem Subnetz der VNET-Integration zu.

Einrichten des NAT-Gateways über das Portal:

1. Navigieren Sie im App Service-Portal zur Benutzeroberfläche **Netzwerk** und wählen Sie im Abschnitt „Ausgehender Datenverkehr“ „VNet-Integration“ aus. Stellen Sie sicher, dass Ihre App in ein Subnetz integriert wurde und dass **Route All** aktiviert ist.
:::image type="content" source="./media/nat-gateway-integration/nat-gateway-route-all-enabled.png" alt-text="Screenshot: „Route All“ aktiviert im Bereich „VNet-Integration“":::
1. Wählen Sie im Menü des Azure-Portals oder auf der **Startseite** die Option **Ressource erstellen** aus. Das Fenster **Neu** wird angezeigt.
1. Suchen Sie nach „NAT-Gateway“ und wählen Sie es in der Ergebnisliste aus.
1. Tragen Sie die Informationen in der Registerkarte **Grundlagen** ein und wählen Sie die Region, in der sich Ihre App befindet.
:::image type="content" source="./media/nat-gateway-integration/nat-gateway-create-basics.png" alt-text="Screenshot der Registerkarte „Grundlagen“ unter „NAT-Gateway erstellen“":::
1. Erstellen Sie in der Registerkarte **Ausgehende IP** eine neue öffentliche IP oder wählen Sie eine bereits vorhandene aus.
:::image type="content" source="./media/nat-gateway-integration/nat-gateway-create-outbound-ip.png" alt-text="Screenshot der Registerkarte „Ausgehende IP“ unter „NAT-Gateway erstellen“":::
1. Wählen Sie in der Registerkarte **Subnetz** das Subnetz für die VNet-Integration.
:::image type="content" source="./media/nat-gateway-integration/nat-gateway-create-subnet.png" alt-text="Screenshot der Registerkarte „Subnetz“ unter „NAT-Gateway erstellen“":::
1. Füllen Sie bei Bedarf die Tags aus und **Erstellen** Sie das NAT-Gateway. Nachdem das NAT-Gateway bereitgestellt wurde, klicken Sie auf **Zur Ressourcengruppe wechseln** und wählen Sie das neue NAT-Gateway aus. Auf dem Blatt „Ausgehende IP“ können Sie nun die öffentliche IP-Adresse sehen, die Ihre App für den ausgehenden Datenverkehr mit Internetanbindung verwendet.
:::image type="content" source="./media/nat-gateway-integration/nat-gateway-public-ip.png" alt-text="Screenshot des Blatts „Ausgehende IP“ im NAT-Gatewayportal"::: 

Wenn Sie ihre Umgebung lieber mithilfe der CLI konfigurieren möchten, sind dies die dafür notwendigen Befehle. Voraussetzung ist, dass Sie im Vorfeld eine Web-App mit konfigurierter VNET-Integration erstellen.

Stellen Sie sicher, dass **Route All** für Ihre VNET-Integration konfiguriert ist (*Hinweis*: Als `az version` ist mindestens Version 2.27 erforderlich):

```azurecli-interactive
az webapp config set --resource-group [myResourceGroup] --name [myWebApp] --vnet-route-all-enabled
```

Erstellen einer öffentlichen IP-Adresse und eines NAT-Gateways:

```azurecli-interactive
az network public-ip create --resource-group [myResourceGroup] --name myPublicIP --sku standard --allocation static

az network nat gateway create --resource-group [myResourceGroup] --name myNATgateway --public-ip-addresses myPublicIP --idle-timeout 10
```

Ordnen Sie das NAT-Gateway dem Subnetz für die VNET-Integration zu:

```azurecli-interactive
az network vnet subnet update --resource-group [myResourceGroup] --vnet-name [myVnet] --name [myIntegrationSubnet] --nat-gateway myNATgateway
```

## <a name="scaling-nat-gateway"></a>Skalieren des NAT-Gateways

Das gleiche NAT-Gateway kann in mehreren Subnetzen derselben Virtual Network-Instanz genutzt werden, d. h. dass ein NAT-Gateway für mehrere Apps und App Service-Pläne verwendet werden kann.

Das NAT-Gateway unterstützt sowohl öffentliche IP-Adressen als auch Präfixe für öffentliche IP-Adressen. Ein NAT-Gateway kann bis zu 16 IP-Adressen über einzelne IP-Adressen und Präfixe hinweg unterstützen. Durch jede IP-Adresse werden 64.000 Ports (SNAT-Ports) zugeordnet, sodass bis zu 1 Mio. Ports verfügbar sind. Weitere Informationen erhalten Sie im [Skalierungsabschnitt](../../virtual-network/nat-gateway/nat-gateway-resource.md#scaling) des NAT-Gateways.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum NAT-Gateway finden Sie in der [NAT-Gatewaydokumentation](../../virtual-network/nat-gateway/nat-overview.md).

Weitere Informationen zur VNET-Integration finden Sie in der [Dokumentation zur VNET-Integration.](../overview-vnet-integration.md)