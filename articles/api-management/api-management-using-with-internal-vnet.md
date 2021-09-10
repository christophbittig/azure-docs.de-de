---
title: Herstellen einer Verbindung mit einem internen virtuellen Netzwerk mit Azure API Management
titleSuffix: Azure API Management
description: Hier erfahren Sie, wie Azure API Management für ein internes virtuelles Netzwerk eingerichtet und konfiguriert wird.
services: api-management
documentationcenter: ''
author: vladvino
editor: ''
ms.service: api-management
ms.topic: how-to
ms.date: 06/08/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b0f67862352f2354dbdb5439542229dbee67ae00
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "113107467"
---
# <a name="connect-to-an-internal-virtual-network-using-azure-api-management"></a>Herstellen einer Verbindung mit einem internen virtuellen Netzwerk mit Azure API Management 
Mit Azure Virtual Networks (VNETs) kann Azure API Management APIs verwalten, die nicht über das Internet zugänglich sind, wobei mehrere VPN-Technologien verwendet werden, um die Verbindung herzustellen. Sie können API Management über [externe](./api-management-using-with-vnet.md) oder interne Modi bereitstellen. In diesem Artikel erfahren Sie, wie Sie API Management im internen VNET-Modus bereitstellen.

Bei Bereitstellung von API Management im internen VNET-Modus können Sie nur die folgenden Dienstendpunkte in einem VNET anzeigen, dessen Zugriff Ihrer Kontrolle unterliegt.
* Proxygateway
* Entwicklerportal
* Direkte Verwaltung
* Git 

> [!NOTE]
> Keiner der Dienstendpunkte ist auf dem öffentlichen DNS-Server registriert. Auf die Dienstendpunkte kann erst zugegriffen werden, wenn Sie [DNS für das VNET konfigurieren](#apim-dns-configuration).

Verwenden Sie API Management im internen Modus für folgende Aufgaben:

* APIs, die in Ihrem privaten Rechenzentrum gehostet werden, können externen Dritten über Azure-VPN-Verbindungen oder Azure ExpressRoute sicher zugänglich gemacht werden.
* Das Verfügbarmachen von cloudbasierten und lokalen APIs über ein gemeinsames Gateway ermöglicht Hybrid Cloud-Szenarien.
* Verwalten von APIs, die in mehreren geografischen Standorten gehostet werden, über einen einzelnen Gatewayendpunkt.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Voraussetzungen

+ **Ein aktives Azure-Abonnement.** [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Eine Azure API Management-Instanz (unterstützte SKUs: Developer, Premium und Isolated)** . Weitere Informationen finden Sie unter [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md).

[!INCLUDE [api-management-public-ip-for-vnet](../../includes/api-management-public-ip-for-vnet.md)]

Bei Bereitstellung eines API Management-Diensts in einem VNET wird eine [Liste von Ports](./api-management-using-with-vnet.md#required-ports) verwendet, die geöffnet werden müssen.

## <a name="creating-an-api-management-in-an-internal-vnet"></a><a name="enable-vpn"> </a>Erstellen von API Management in einem internen VNET
Der API Management-Dienst in einem internen virtuellen Netzwerk wird hinter einem internen Lastenausgleich gehostet. Die Lastenausgleich-SKU hängt von der Verwaltungs-API ab, die zum Erstellen des Diensts verwendet wird. Weitere Informationen finden Sie unter [Azure Load Balancer-SKUs](../load-balancer/skus.md).

| API-Version | Gehostet hinter |
| ----------- | ------------- |
| 2020-12-01 | Interner Lastenausgleich in der Basic-SKU |
| 2020-01-01-preview mit einer öffentlichen IP-Adresse aus Ihrem Abonnement | Interner Lastenausgleich in der Standard-SKU |

### <a name="enable-a-vnet-connection-using-the-azure-portal"></a>Aktivieren einer VNET-Verbindung über das Azure-Portal

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrer Azure API Management-Instanz.
1. Wählen Sie **Virtuelles Netzwerk** aus.
1. Konfigurieren Sie den **internen** Zugriffstyp. Ausführliche Schritte finden Sie unter [Aktivieren der VNET-Konnektivität über das Azure-Portal](api-management-using-with-vnet.md#enable-vnet-connectivity-using-the-azure-portal).

    ![Menü zum Einrichten einer Azure API Management-Instanz in einem internen VNET][api-management-using-internal-vnet-menu]

4. Wählen Sie **Speichern** aus.

Nach erfolgreicher Bereitstellung sollten die **private** virtuelle IP-Adresse und die **öffentliche** virtuelle IP-Adresse Ihres API Management-Diensts auf dem Blatt **Übersicht** angezeigt werden. 

| Virtuelle IP-Adresse | BESCHREIBUNG |
| ----- | ----- |
| **Private virtuelle IP-Adresse** | Eine IP-Adresse mit Lastenausgleich aus dem API Management-delegierten Subnetz, über die Sie auf die Endpunkte `gateway`, `portal`, `management` und `scm` zugreifen können. |  
| **Öffentliche virtuelle IP-Adresse** | Wird *nur* für den Datenverkehr auf Steuerungsebene zum `management`-Endpunkt über `port 3443` verwendet. Kann für das [ApiManagement][ServiceTags]-Diensttag gesperrt werden. |  

![API Management-Dashboard mit konfiguriertem internem VNET][api-management-internal-vnet-dashboard]

> [!NOTE]
> Da der Gateway-URI nicht unter dem öffentlichen DNS registriert ist, funktioniert die im Azure-Portal verfügbare Testkonsole bei **intern** bereitgestelltem VNET-Dienst nicht. Verwenden Sie stattdessen die im **Entwicklerportal** bereitgestellte Testkonsole.

### <a name="deploy-api-management-into-vnet"></a><a name="deploy-apim-internal-vnet"> </a>Bereitstellen von API Management im VNET

Sie können die VNET-Konnektivität auch mithilfe der folgenden Methoden aktivieren.


### <a name="api-version-2020-12-01"></a>API-Version 2020-12-01

* [Azure Resource Manager-Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.apimanagement/api-management-create-with-internal-vnet)

     [![Bereitstellung in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.apimanagement%2Fapi-management-create-with-internal-vnet%2Fazuredeploy.json)

* Azure PowerShell-Cmdlets: [Erstellen](/powershell/module/az.apimanagement/new-azapimanagement) oder [Aktualisieren](/powershell/module/az.apimanagement/update-azapimanagementregion) einer API Management-Instanz in einem VNET

## <a name="dns-configuration"></a><a name="apim-dns-configuration"></a>DNS-Konfiguration
Im externen VNET-Modus wird das DNS von Azure verwaltet. Beim internen VNET-Modus müssen Sie die DNS-Verwaltung selbst übernehmen. Es wird Folgendes empfohlen:
1. Konfigurieren einer privaten Azure DNS-Zone.
1. Verknüpfen der privaten Azure DNS-Zone mit dem VNET, in dem Sie Ihren API Management-Dienst bereitgestellt haben. 

Erfahren Sie mehr über das [Einrichten einer privaten Zone in Azure DNS](../dns/private-dns-getstarted-portal.md).

> [!NOTE]
> Der API Management-Dienst lauscht nicht auf von IP-Adressen stammende Anforderungen. Er reagiert nur auf Anforderungen für den Hostnamen, der für seine Dienstendpunkte konfiguriert ist. Zu diesen Endpunkten gehören:
> * Gateway
> * Das Azure-Portal
> * Entwicklerportal
> * Direktverwaltungsendpunkt
> * Git

### <a name="access-on-default-host-names"></a>Zugreifen über Standardhostnamen
Wenn Sie einen API Management-Dienst erstellen (z. B. `contosointernalvnet`), werden standardmäßig die folgenden Dienstendpunkte konfiguriert:

| Endpunkt | Endpunktkonfiguration |
| ----- | ----- |
| Gateway oder Proxy | `contosointernalvnet.azure-api.net` |
| Entwicklerportal | `contosointernalvnet.portal.azure-api.net` |
| Neues Entwicklerportal | `contosointernalvnet.developer.azure-api.net` |
| Direktverwaltungsendpunkt | `contosointernalvnet.management.azure-api.net` |
| Git | `contosointernalvnet.scm.azure-api.net` |

Für den Zugriff auf diese API Management-Dienstendpunkte können Sie einen virtuellen Computer in einem Subnetz erstellen, das mit dem VNET verbunden ist, in dem API Management bereitgestellt wird. Wenn die interne virtuelle IP-Adresse für Ihren Dienst 10.1.0.5 lautet, können Sie die Hostdatei (`%SystemDrive%\drivers\etc\hosts`) wie folgt zuordnen:

| Interne virtuelle IP-Adresse | Endpunktkonfiguration |
| ----- | ----- |
| 10.1.0.5 | `contosointernalvnet.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.portal.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.developer.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.management.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.scm.azure-api.net` |

Anschließend können Sie über den virtuellen Computer, den Sie erstellt haben, auf alle Dienstendpunkte zugreifen.
Wenn Sie einen benutzerdefinierten DNS-Server in einem VNET verwenden, können Sie auch DNS-A-Einträge erstellen und von überall in Ihrem VNET auf diese Endpunkte zugreifen.

### <a name="access-on-custom-domain-names"></a>Zugreifen über benutzerdefinierte Domänennamen

Wenn Sie auf den API Management-Dienst nicht mit den Standardhostnamen zugreifen möchten: 

1. Richten Sie benutzerdefinierte Domänennamen für alle Dienstendpunkte ein, wie unten dargestellt:

   ![Einrichten einer benutzerdefinierten Domäne für API Management][api-management-custom-domain-name]

2. Erstellen Sie Einträge auf Ihrem DNS-Server, um auf die Endpunkte zuzugreifen, die über Ihr VNET zugänglich sind.

## <a name="routing"></a><a name="routing"> </a> Routing

* Eine *private* virtuelle IP-Adresse mit Lastenausgleich aus dem Subnetzbereich wird für den Zugriff auf die API Management-Dienstendpunkte aus dem VNET reserviert. 
    * Suchen Sie diese private IP-Adresse auf dem Blatt „Übersicht“ des Diensts im Azure-Portal. 
    * Registrieren Sie diese Adresse bei den DNS-Servern, die vom VNET verwendet werden.
* Eine *öffentliche* IP-Adresse (VIP) mit Lastenausgleich wird auch reserviert, um den Zugriff auf den Verwaltungsdienst-Endpunkt über `port 3443` zu ermöglichen. 
    * Suchen Sie diese öffentliche IP-Adresse auf dem Blatt „Übersicht“ des Diensts im Azure-Portal. 
    * Verwenden Sie nur die *öffentliche* IP-Adresse für den Datenverkehr auf Steuerungsebene zum `management`-Endpunkt über `port 3443`. 
    * Diese IP-Adresse kann für das [ApiManagement][ServiceTags]-Diensttag gesperrt werden.
* DIP-Adressen werden jeder VM im Dienst zugewiesen und für den Zugriff auf Ressourcen *innerhalb* des VNET verwendet. Eine VIP-Adresse wird für den Zugriff auf Ressourcen *außerhalb* des VNET verwendet. Falls Ressourcen innerhalb des VNET mit IP-Einschränkungslisten geschützt werden, müssen Sie den gesamten Bereich des Subnetzes angeben, in dem der API Management-Dienst bereitgestellt wird, um Zugriff zu gewähren oder zu verweigern.
* Die öffentlichen und privaten IP-Adressen mit Lastenausgleich finden Sie auf dem Blatt „Übersicht“ im Azure-Portal.
* Wenn Sie den Dienst im VNET entfernen oder hinzufügen, können sich die IP-Adressen ändern, die für den öffentlichen und privaten Zugriff zugewiesen sind. Sie müssen möglicherweise die DNS-Registrierungen, Routingregeln und IP-Einschränkungslisten innerhalb des VNET aktualisieren.

## <a name="related-content"></a><a name="related-content"> </a>Verwandte Inhalte
Weitere Informationen erhalten Sie in den folgenden Artikeln:
* [Allgemeine Probleme mit der Netzwerkkonfiguration beim Einrichten von API Management in einem VNET][Common network configuration problems]
* [Häufig gestellte Fragen zum VNET](../virtual-network/virtual-networks-faq.md)
* [Managing DNS Records](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10)) (Verwalten von DNS-Einträgen)

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/updated-api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/updated-api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/updated-api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
