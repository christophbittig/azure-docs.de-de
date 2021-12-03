---
title: Herstellen einer Verbindung mit einem internen virtuellen Netzwerk mit Azure API Management
description: Hier erfahren Sie, wie Azure API Management über den internen Modus in einem virtuellen Netzwerk eingerichtet und konfiguriert wird.
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 08/10/2021
ms.author: danlep
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6dc297736403b6124b27f34462ffaee0bfa4cf4a
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845777"
---
# <a name="connect-to-a-virtual-network-in-internal-mode-using-azure-api-management"></a>Herstellen einer Verbindung mit einem virtuellen Netzwerk im internen Modus mithilfe von Azure API Management 
Über virtuelle Azure-Netzwerke (VNets) kann Azure API Management APIs verwalten, die nicht über das Internet zugänglich sind. Hierbei werden mehrere VPN-Technologien zum Herstellen der Verbindung verwendet. Sie können API Management über [externe](./api-management-using-with-vnet.md) oder interne Modi bereitstellen. Informationen zu VNet-Konnektivitätsoptionen, Anforderungen und Überlegungen finden Sie unter [Verwenden eines virtuellen Netzwerks mit Azure API Management](virtual-network-concepts.md).

In diesem Artikel erfahren Sie, wie Sie API Management im internen VNET-Modus bereitstellen. In diesem Modus können Sie nur die folgenden Dienstendpunkte in einem VNet anzeigen, dessen Zugriff Ihrer Kontrolle unterliegt.
* API-Gateway
* Entwicklerportal
* Direkte Verwaltung
* Git 

> [!NOTE]
> Keiner der Dienstendpunkte ist auf dem öffentlichen DNS-Server registriert. Auf die Dienstendpunkte kann erst zugegriffen werden, wenn Sie [DNS für das VNet konfigurieren](#dns-configuration).

Verwenden Sie API Management im internen Modus für folgende Aufgaben:

* Gewährleisten eines sicheren Zugriffs auf APIs, die in Ihrem privaten Rechenzentrum gehostet werden, für externe Dritte über Azure-VPN-Verbindungen oder Azure ExpressRoute.
* Das Verfügbarmachen von cloudbasierten und lokalen APIs über ein gemeinsames Gateway ermöglicht Hybrid Cloud-Szenarien.
* Verwalten von APIs, die in mehreren geografischen Standorten gehostet werden, über einen einzelnen Gatewayendpunkt.

:::image type="content" source="media/api-management-using-with-internal-vnet/api-management-vnet-internal.png" alt-text="Herstellen einer Verbindung mit dem internen VNet":::

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Voraussetzungen

Einige Voraussetzungen unterscheiden sich abhängig von der Version (`stv2` oder `stv1`) der [Computeplattform](compute-infrastructure.md) für Ihre API Management-Instanz. 

> [!TIP]
> Wenn Sie das Portal verwenden, um die Netzwerkkonfiguration Ihrer API Management-Instanz zu erstellen oder zu aktualisieren, wird die Instanz auf der `stv2`-Computeplattform gehostet.

### <a name="stv2"></a>[stv2](#tab/stv2)

+ **Eine API Management-Instanz.** Weitere Informationen finden Sie unter [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md).

* **Ein virtuelles Netzwerk und ein Subnetz** müssen sich in derselben Region und in demselben Abonnement befinden wie Ihre API Management-Instanz. Das Subnetz kann andere Azure-Ressourcen enthalten.

[!INCLUDE [api-management-public-ip-for-vnet](../../includes/api-management-public-ip-for-vnet.md)]

   > [!NOTE]
   > Wenn Sie einen API Management-Dienst in einem internen VNet auf der `stv2`-Plattform bereitstellen, wird er unter Verwendung der öffentlichen IP-Adressressource hinter einem internen Lastenausgleich in der [Standard-SKU](../load-balancer/skus.md) gehostet.

### <a name="stv1"></a>[stv1](#tab/stv1)

+ **Eine API Management-Instanz.** Weitere Informationen finden Sie unter [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md).

* **Ein virtuelles Netzwerk und ein Subnetz** müssen sich in derselben Region und in demselben Abonnement befinden wie Ihre API Management-Instanz.

    Das Subnetz muss dediziert für API Management-Instanzen verwendet werden. Beim Versuch, eine Azure API Management-Instanz in einem Subnetz eines Ressourcen-Manager-VNet bereitzustellen, das andere Ressourcen enthält, tritt ein Fehler bei der Bereitstellung auf.

   > [!NOTE]
   > Wenn Sie einen API Management-Dienst in einem internen VNet auf der `stv1`-Plattform bereitstellen, wird er hinter einem internen Lastenausgleich in der [Basic-SKU](../load-balancer/skus.md) gehostet.

---

## <a name="enable-vnet-connection"></a>VNet-Verbindung Aktivieren

### <a name="enable-vnet-connectivity-using-the-azure-portal-stv2-platform"></a>Aktivieren von VNet-Konnektivität über das Azure-Portal (`stv2`-Plattform)

1. Wechseln Sie zum [Azure-Portal](https://portal.azure.com), um Ihre API Management-Instanz zu suchen. Suchen Sie die **API Management-Dienste**, und wählen Sie sie aus.
1. Wählen Sie Ihre API Management-Instanz aus.
1. Wählen Sie **Virtuelles Netzwerk** aus.
1. Wählen Sie den Zugriffstyp **Intern** aus.
1. In der Liste der Standorte (Regionen), an denen Ihr API Management-Dienst bereitgestellt wird: 
    1. Wählen Sie einen **Standort** aus.
    1. Wählen Sie **Virtuelles Netzwerk**, **Subnetz** und **IP-Adresse** aus. 
        * Die Liste der VNets enthält Resource Manager-VNets, die in Ihrem Azure-Abonnement zur Verfügung stehen und in der Region eingerichtet sind, die Sie konfigurieren.
1. Wählen Sie **Übernehmen**. Die Seite **Virtuelles Netzwerk** Ihrer API Management-Instanz wird mit Ihren neuen Optionen für das VNet und Subnetz aktualisiert.
   :::image type="content" source="media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png" alt-text="Einrichten eines internen VNet im Azure-Portal":::
1. Fahren Sie mit dem Konfigurieren der VNet-Einstellungen für die restlichen Standorte Ihrer API Management-Instanz fort.
1. Wählen Sie auf der oberen Navigationsleiste **Speichern** und dann **Netzwerkkonfiguration anwenden** aus.

    Es kann zwischen 15 und 45 Minuten dauern, bis die API Management-Instanz aktualisiert wurde.

Nach erfolgreicher Bereitstellung sollten die **private** virtuelle IP-Adresse und die **öffentliche** virtuelle IP-Adresse Ihres API Management-Diensts auf dem Blatt **Übersicht** angezeigt werden. Weitere Informationen zu den IP-Adressen finden Sie in diesem Artikel unter [Routing](#routing).

:::image type="content" source="media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png" alt-text="Öffentliche und private IP-Adresse im Azure-Portal"::: 

> [!NOTE]
> Da die Gateway-URL nicht im öffentlichen DNS registriert ist, funktioniert die im Azure-Portal verfügbare Testkonsole nicht bei einem **internen** im VNet bereitgestellten Dienst. Verwenden Sie stattdessen die im **Entwicklerportal** bereitgestellte Testkonsole.

### <a name="enable-connectivity-using-a-resource-manager-template"></a>Ermöglichen von Konnektivität über eine Resource Manager-Vorlage

#### <a name="api-version-2021-01-01-preview-stv2-platform"></a>API-Version 2021-01-01-preview (`stv2`-Plattform)

* [Azure Resource Manager-Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.apimanagement/api-management-create-with-internal-vnet-publicip)

     [![Bereitstellung in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.apimanagement%2Fapi-management-create-with-internal-vnet-publicip%2Fazuredeploy.json)

#### <a name="api-version-2020-12-01-stv1-platform"></a>API-Version 2020-12-01 (`stv1`-Plattform)

* [Azure Resource Manager-Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.apimanagement/api-management-create-with-internal-vnet)

     [![Bereitstellung in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.apimanagement%2Fapi-management-create-with-internal-vnet%2Fazuredeploy.json)

### <a name="enable-connectivity-using-azure-powershell-cmdlets-stv1-platform"></a>Ermöglichen von Konnektivität über Azure PowerShell-Cmdlets (`stv1`-Plattform)

[Erstellen](/powershell/module/az.apimanagement/new-azapimanagement) oder [aktualisieren](/powershell/module/az.apimanagement/update-azapimanagementregion) Sie eine API Management-Instanz in einem VNet.

## <a name="dns-configuration"></a>DNS-Konfiguration

Im externen VNET-Modus wird das DNS von Azure verwaltet. Beim internen VNET-Modus müssen Sie die DNS-Verwaltung selbst übernehmen. Es wird Folgendes empfohlen:
1. Konfigurieren Sie eine [private Azure DNS-Zone](../dns/private-dns-overview.md).
1. Verknüpfen Sie die private Azure DNS-Zone mit dem VNet, in dem Sie Ihren API Management-Dienst bereitgestellt haben. 

Erfahren Sie mehr über das [Einrichten einer privaten Zone in Azure DNS](../dns/private-dns-getstarted-portal.md).

> [!NOTE]
> Der API Management-Dienst lauscht nicht auf Anforderungen an seinen IP-Adressen. Er reagiert nur auf Anforderungen für den Hostnamen, der für seine Dienstendpunkte konfiguriert ist. Zu diesen Endpunkten gehören:
> * API-Gateway
> * Das Azure-Portal
> * Entwicklerportal
> * Direktverwaltungsendpunkt
> * Git

### <a name="access-on-default-host-names"></a>Zugreifen über Standardhostnamen
Wenn Sie einen API Management-Dienst erstellen (z. B. `contosointernalvnet`), werden standardmäßig die folgenden Dienstendpunkte konfiguriert:

| Endpunkt | Endpunktkonfiguration |
| ----- | ----- |
| API Gateway | `contosointernalvnet.azure-api.net` |
| Entwicklerportal | `contosointernalvnet.portal.azure-api.net` |
| Neues Entwicklerportal | `contosointernalvnet.developer.azure-api.net` |
| Direktverwaltungsendpunkt | `contosointernalvnet.management.azure-api.net` |
| Git | `contosointernalvnet.scm.azure-api.net` |

Für den Zugriff auf diese API Management-Dienstendpunkte können Sie einen virtuellen Computer in einem Subnetz erstellen, das mit dem VNET verbunden ist, in dem API Management bereitgestellt wird. Wenn die interne virtuelle IP-Adresse für Ihren Dienst 10.1.0.5 lautet, können Sie die Hostdatei wie folgt zuordnen. Unter Windows befindet sich diese Datei unter `%SystemDrive%\drivers\etc\hosts`. 

| Interne virtuelle IP-Adresse | Endpunktkonfiguration |
| ----- | ----- |
| 10.1.0.5 | `contosointernalvnet.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.portal.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.developer.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.management.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.scm.azure-api.net` |

Anschließend können Sie über den virtuellen Computer, den Sie erstellt haben, auf alle Dienstendpunkte zugreifen.


### <a name="access-on-custom-domain-names"></a>Zugreifen über benutzerdefinierte Domänennamen

Wenn Sie auf den API Management-Dienst nicht mit den Standardhostnamen zugreifen möchten: 

1. Richten Sie [benutzerdefinierte Domänennamen](configure-custom-domain.md) für alle Dienstendpunkte ein, wie in der folgenden Abbildung dargestellt:

    :::image type="content" source="media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png" alt-text="Einrichten eines benutzerdefinierten Domänennamens":::

2. Erstellen Sie Einträge auf Ihrem DNS-Server, um auf die Endpunkte zuzugreifen, die über Ihr VNET zugänglich sind.

## <a name="routing"></a>Routing

Die folgenden virtuellen IP-Adressen werden für eine API Management-Instanz in einem internen virtuellen Netzwerk konfiguriert. Erfahren Sie mehr über die [IP-Adressen von API Management](api-management-howto-ip-addresses.md).

| Virtuelle IP-Adresse | BESCHREIBUNG |
| ----- | ----- |
| **Private virtuelle IP-Adresse** | Eine IP-Adresse mit Lastenausgleich aus dem Subnetzbereich (DIP) der API Management-Instanz, über die Sie auf das API-Gateway, das Entwicklerportal, die Verwaltung und Git-Endpunkte zugreifen können.<br/><br/>Registrieren Sie diese Adresse bei den DNS-Servern, die vom VNET verwendet werden.     |  
| **Öffentliche virtuelle IP-Adresse** | Wird *nur* für den Datenverkehr auf Steuerungsebene zum Verwaltungsendpunkt über Port 3443 verwendet. Kann für das [ApiManagement][ServiceTags]-Diensttag gesperrt werden. | 

Die öffentlichen und privaten IP-Adressen mit Lastenausgleich finden Sie auf dem Blatt **Übersicht** im Azure-Portal.

> [!NOTE]
> Die VIP-Adressen der API Management-Instanz ändern sich in folgenden Fällen:
> * Das VNet wird aktiviert oder deaktiviert. 
> * API Management wird vom **externen** in den **internen** VNet-Modus oder umgekehrt verschoben.
> * Einstellungen zur [Zonenredundanz](zone-redundancy.md) werden an einem Standort für Ihre Instanz aktiviert, aktualisiert oder deaktiviert (nur Premium-SKU).
>
> Sie müssen möglicherweise die DNS-Registrierungen, Routingregeln und IP-Einschränkungslisten innerhalb des VNET aktualisieren.

### <a name="vip-and-dip-addresses"></a>VIP- und DIP-Adressen

DIP-Adressen werden jeder zugrunde liegenden VM im Dienst zugewiesen und für den Zugriff auf Ressourcen *innerhalb* des VNet verwendet. Eine VIP-Adresse wird für den Zugriff auf Ressourcen *außerhalb* des VNET verwendet. Falls Ressourcen innerhalb des VNET mit IP-Einschränkungslisten geschützt werden, müssen Sie den gesamten Bereich des Subnetzes angeben, in dem der API Management-Dienst bereitgestellt wird, um Zugriff zu gewähren oder zu verweigern.

Erfahren Sie mehr über die [empfohlene Subnetzgröße](virtual-network-concepts.md#subnet-size).

#### <a name="example"></a>Beispiel

Wenn Sie eine [Kapazitätseinheit](api-management-capacity.md) von API Management im Premium-Tarif in einem internen VNet bereitstellen, werden drei IP-Adressen verwendet: eine für die private VIP und jeweils eine für die DIPs für zwei VMs. Wenn Sie auf vier Einheiten aufskalieren, werden mehr IP-Adressen für zusätzliche DIPs aus dem Subnetz verbraucht.  

Wenn für den Zielendpunkt nur eine feste Gruppe von DIPs in der Positivliste aufgeführt ist, kommt es zu Verbindungsfehlern, wenn Sie in Zukunft neue Einheiten hinzufügen. Aus diesem Grund und weil sich das Subnetz vollständig unter Ihrer Kontrolle befindet, wird empfohlen, das gesamte Subnetz in die Positivliste im Back-End aufzunehmen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen:

* [Netzwerkkonfiguration beim Einrichten von Azure API Management in einem VNet][Common network configuration problems]
* [Häufig gestellte Fragen zum VNET](../virtual-network/virtual-networks-faq.md)
* [Managing DNS Records](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10)) (Verwalten von DNS-Einträgen)

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/updated-api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/updated-api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/updated-api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags

