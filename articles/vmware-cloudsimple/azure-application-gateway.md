---
title: Verwenden von Azure Application Gateway mit virtuellen VMware-Computern
description: Beschreibt, wie Sie das Azure Application Gateway verwenden, um eingehenden Webdatenverkehr für Webserver zu verwalten, die in virtuellen VMware-Computern in der privaten CloudSimple-Cloudumgebung ausgeführt werden.
author: suzizuber
ms.author: v-szuber
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 15e10ed71702350aaadbac76ea02f445dd846cc1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132322795"
---
# <a name="use-azure-application-gateway-with-vmware-virtual-machines-in-the-cloudsimple-private-cloud-environment"></a>Verwenden von Azure Application Gateway mit virtuellen VMware-Computern in der privaten CloudSimple-Cloudumgebung

Sie können das Azure Application Gateway verwenden, um eingehenden Webdatenverkehr für Webserver zu verwalten, die in virtuellen VMware-Computern in Ihrer privaten CloudSimple-Cloudumgebung ausgeführt werden.

Durch die Nutzung von Azure Application Gateway in einer öffentlich-privaten Hybridbereitstellung können Sie den Webdatenverkehr für Ihre Anwendungen verwalten, ein sicheres Front-End bereitstellen und die TLS-Verarbeitung für die in der VMware-Umgebung ausgeführten Dienste entlasten. Azure Application Gateway leitet eingehenden Webdatenverkehr an Back-End-Poolinstanzen weiter, die in VMware-Umgebungen gemäß konfigurierten Regeln und Integritätstests ausgeführt werden.

Diese Azure Application Gateway-Lösung erfordert Folgendes:

* Ein Azure-Abonnement
* Erstellen und Konfigurieren eines virtuellen Azure-Netzwerks und eines Subnetzes im virtuellen Netzwerk.
* Erstellen und Konfigurieren von NSG-Regeln und Einrichten des Peerings Ihres VNET mit Ihrer privaten CloudSimple-Cloud mithilfe von ExpressRoute.
* Erstellen und konfigurieren Sie Ihre private Cloud.
* Erstellen und konfigurieren Sie Ihr Azure Application Gateway.

## <a name="azure-application-gateway-deployment-scenario"></a>Bereitstellungsszenario für Azure Application Gateway

In diesem Szenario wird das Azure Application Gateway in Ihrem virtuellen Azure-Netzwerk ausgeführt. Das virtuelle Netzwerk ist über eine ExpressRoute-Leitung mit Ihrer privaten Cloud verbunden. Alle Subnetze in der privaten Cloud sind aus den Subnetzen des virtuellen Netzwerks über IP-Adressen erreichbar.

![Azure-Lastenausgleich im virtuellen Azure-Netzwerk](media/load-balancer-use-case.png)

## <a name="how-to-deploy-the-solution"></a>Bereitstellen der Lösung

Der Bereitstellungsvorgang besteht aus den folgenden Aufgaben:

1. [Sicherstellen, dass alle Voraussetzungen erfüllt sind](#1-verify-prerequisites)
2. [Herstellen einer Verbindung zwischen dem virtuellen Azure-Netzwerk und der privaten Cloud](#2-connect-your-azure-virtual-network-to-your-private-cloud)
3. [Bereitstellen eines Azure Application Gateways](#3-deploy-an-azure-application-gateway)
4. [Erstellen und Konfigurieren eines Webserver-VM-Pools in Ihrer privaten Cloud](#4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud)

## <a name="1-verify-prerequisites"></a>1. Überprüfen der Voraussetzungen

Vergewissern Sie sich, dass die folgenden Voraussetzungen erfüllt sind:

* Ein Azure Resource Manager und ein virtuelles Netzwerk wurden bereits erstellt.
* Ein dediziertes Subnetz (für Application Gateway) in Ihrem virtuellen Azure-Netzwerk wurde bereits erstellt.
* Eine private CloudSimple-Cloud wurde bereits erstellt.
* Zwischen IP-Subnetzen im virtuellen Netzwerk und Subnetzen in der privaten Cloud besteht kein IP-Konflikt.

## <a name="2-connect-your-azure-virtual-network-to-your-private-cloud"></a>2. Herstellen einer Verbindung Ihres virtuellen Azure-Netzwerks mit der privaten Cloud

Um Ihr virtuelles Azure-Netzwerk mit Ihrer privaten Cloud zu verbinden, führen Sie den folgenden Vorgang aus.

1. [Kopieren Sie im CloudSimple-Portal die Informationen zum ExpressRoute-Peering](virtual-network-connection.md).

2. [Konfigurieren Sie ein virtuelles Netzwerkgateway für Ihr virtuelles Azure-Netzwerk](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).

3. [Verbinden Sie Ihr virtuelles Netzwerk mit der CloudSimple ExpressRoute-Leitung](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription).

4. [Verwenden Sie die von Ihnen kopierten Peeringinformationen, um Ihr virtuelles Netzwerk mit der ExpressRoute-Leitung zu verbinden](virtual-network-connection.md).

## <a name="3-deploy-an-azure-application-gateway"></a>3. Bereitstellen eines Azure Anwendungsgateways

Ausführlich Anleitungen dazu finden Sie unter [Erstellen eines Anwendungsgateways mit pfadbasierten Routingregeln mithilfe des Azure-Portals](../application-gateway/create-url-route-portal.md). Dies ist eine Zusammenfassung der erforderlichen Schritte:

1. Erstellen Sie ein virtuelles Netzwerk in Ihrem Abonnement und eine Ressourcengruppe.
2. Erstellen Sie ein Subnetz (das als dediziertes Subnetz verwendet wird) in Ihrem virtuellen Netzwerk.
3. Erstellen Sie ein Standardanwendungsgateway (aktivieren Sie optional WAF): Klicken Sie auf der Homepage im Azure-Portal links oben auf der Seite auf **Ressourcen** > **Netzwerk** > **Application Gateway**. Wählen Sie die Standard-SKU und -Größe aus, und geben Sie das Azure-Abonnement, die Ressourcengruppe und Standortinformationen an. Erstellen Sie bei Bedarf eine neue öffentliche IP-Adresse für dieses Anwendungsgateway, und geben Sie Details zum virtuellen Netzwerk und zum dedizierten Subnetz für das Anwendungsgateway an.
4. Fügen Sie einen Back-End-Pool mit virtuellen Computern hinzu, und fügen Sie diesen Ihrem Anwendungsgateway hinzu.

## <a name="4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud"></a>4. Erstellen und Konfigurieren eines Webserver-VM-Pools in Ihrer privaten Cloud

Erstellen Sie in vCenter VMs mit dem Betriebssystem und Webserver Ihrer Wahl (z.B. Windows/IIS oder Linux/Apache). Wählen Sie ein Subnetz/VLAN aus, das für die Webschicht in Ihrer privaten Cloud reserviert ist. Vergewissern Sie sich, dass sich mindestens ein vNIC der Webserver-VMs im Subnetz der Webschicht befindet.
