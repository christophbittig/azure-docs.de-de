---
title: Herstellen einer Verbindung mit einem Back-End v1
description: Erfahren Sie, wie Sie von einer App Service-Umgebung aus eine sichere Verbindung mit Back-End-Ressourcen herstellen. Dieses Dokument wird nur für Kunden bereitgestellt, die die ASE-Legacyumgebung v1 verwenden.
author: madsd
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.topic: article
ms.date: 10/04/2016
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: eb022d72dbbf433c8a91f092cb2337fc7e3a6e55
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130004671"
---
# <a name="connect-securely-to-back-end-resources-from-an-app-service-environment"></a>Sicheres Verbinden mit Back-End-Ressourcen von einer App Service-Umgebung aus
Da eine App Service-Umgebung immer **entweder** in einem virtuellen Netzwerk von Azure Resource Manager **oder** einem [virtuellen Netzwerk][virtualnetwork] des klassischen Bereitstellungsmodells erstellt wird, können aus einer App Service-Umgebung ausgehende Verbindungen zu anderen Back-End-Ressourcen ausschließlich über das virtuelle Netzwerk erfolgen. Seit Juni 2016 können ASEs auch in virtuellen Netzwerken bereitgestellt werden, die entweder öffentliche Adressbereiche oder RFC1918-Adressräume (private Adressen) verwenden.  

Beispielsweise kann ein SQL Server auf einem Cluster virtueller Computer ausgeführt werden, wenn Port 1433 gesperrt ist.  Der Endpunkt kann durch eine ACL geschützt werden, um nur den Zugriff von anderen Ressourcen im selben virtuellen Netzwerk aus zuzulassen.  

Als weiteres Beispiel können vertrauliche Endpunkte lokal ausgeführt werden und mit Azure entweder über [Site-to-Site][SiteToSite]- oder [Azure ExpressRoute][ExpressRoute]-Verbindungen verbunden sein.  Daher können nur Ressourcen in virtuellen Netzwerken, die mit den Site-to-Site- oder ExpressRoute-Tunneln verbunden sind, auf lokale Standorte zugreifen.

In all diesen Szenarien können Apps, die in einer App Service-Umgebung ausgeführt werden, sich sicher mit den verschiedenen Servern und Ressourcen verbinden. Wenn ausgehender Datenverkehr von Apps in einer App Service-Umgebung zu privaten Endpunkten in demselben virtuellen Netzwerk (oder die mit demselben virtuellen Netzwerk verbunden sind) fließt, wird er nur über das virtuelle Netzwerk geleitet.  Ausgehender Datenverkehr an private Endpunkte fließt nicht über das öffentliche Internet.

Ein Problem liegt bei ausgehendem Datenverkehr aus einer App Service-Umgebung zu Endpunkten in einem virtuellen Netzwerk vor. App Service-Umgebungen können Endpunkte von virtuellen Computern nicht erreichen, die sich im **selben** Subnetz wie die App Service-Umgebung befinden. Diese Einschränkung sollte normalerweise kein Problem darstellen, wenn die App Service-Umgebungen in einem Subnetz bereitgestellt werden, das ausschließlich für die Verwendung durch die App Service-Umgebung reserviert ist.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>Ausgehende Verbindungen und DNS-Anforderungen
Damit eine App Service-Umgebung richtig funktioniert, ist ausgehender Zugriff auf verschiedene Endpunkte erforderlich. Eine vollständige Liste externer Endpunkte, die von einer ASE verwendet werden, befindet sich im Artikel [Netzwerkkonfiguration für ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) im Abschnitt „Erforderliche Netzwerkverbindung“.

App Service-Umgebungen erfordern zudem eine gültige DNS-Infrastruktur, die für das virtuelle Netzwerk konfiguriert ist.  Wenn die DNS-Konfiguration nach der Erstellung einer App Service-Umgebung geändert wird, können Entwickler erzwingen, dass eine App Service-Umgebung die neue DNS-Konfiguration übernimmt. Wählen Sie oben auf dem Verwaltungsblatt der App Service-Umgebung im Portal das Symbol **Neu starten** aus, um einen parallelen Neustart der Umgebung auszulösen, durch den die Umgebung die neue DNS-Konfiguration übernimmt.

Es empfiehlt sich auch, benutzerdefinierte DNS-Server im VNet vor dem Erstellen einer App-Service-Umgebung einzurichten.  Wenn die DNS-Konfiguration eines virtuellen Netzwerks geändert wird, während eine App Service-Umgebung erstellt wird, führt dies zum Fehlschlagen der Erstellung der App Service-Umgebung. Wenn ein benutzerdefinierter DNS-Server am anderen Ende eines VPN-Gateways vorhanden ist und der DNS-Server nicht erreichbar oder nicht verfügbar ist, schlägt das Erstellen der App-Service-Umgebung am anderen Ende eines VPN-Gateways ebenfalls fehl.

## <a name="connecting-to-a-sql-server"></a>Verbinden mit einem SQL Server
Eine gängige SQL Server-Konfiguration verfügt über einen Endpunkt, der an Port 1433 lauscht:

![SQL Server-Endpunkt][SqlServerEndpoint]

Es gibt zwei Ansätze zum Einschränken des Datenverkehrs zu diesem Endpunkt:

* [Netzwerk-Zugriffssteuerungslisten][NetworkAccessControlLists] (Netzwerk-ACLs)
* [Netzwerksicherheitsgruppen][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>Einschränken des Zugriffs mit einer Netzwerk-ACL
Port 1433 kann über eine Netzwerk-Zugriffssteuerungsliste geschützt werden.  Im Beispiel unten werden die Clientadressen, die aus einem virtuellen Netzwerk stammen, zu Zuweisungsberechtigungen hinzugefügt, und der Zugriff auf alle anderen Clients wird blockiert.

![Beispiel zu Netzwerk-Zugriffssteuerungslisten][NetworkAccessControlListExample]

Alle Anwendungen, die in der App Service-Umgebung in demselben virtuellen Netzwerk wie SQL Server ausgeführt werden, können mit der SQL Server-Instanz eine Verbindung herstellen. Verwenden Sie die **interne VNet-IP-Adresse** für den virtuellen SQL Server-Computer.  

Die unten aufgeführte Beispiel-Verbindungszeichenfolge verweist auf den SQL Server über dessen private IP-Adresse.

`Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient`

Obwohl der virtuelle Computer auch über einen öffentlichen Endpunkt verfügt, werden Verbindungsversuche anhand der öffentlichen IP-Adresse aufgrund der Netzwerk-ACL zurückgewiesen. 

## <a name="restricting-access-with-a-network-security-group"></a>Einschränken des Zugriffs mit einer Netzwerksicherheitsgruppe
Als alternative Methode zum Sichern des Zugriffs kann eine Netzwerksicherheitsgruppe verwendet werden.  Netzwerksicherheitsgruppen können auf einzelne virtuelle Computer oder auf ein Subnetz mit virtuellen Computern angewendet werden.

Zunächst müssen Sie eine Netzwerksicherheitsgruppe erstellen:

```azurepowershell-interactive
New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"
```

Das Beschränken des Zugriffs auf den VNet-internen Datenverkehr ist mit einer Netzwerksicherheitsgruppe einfach.  Die Standardregeln in einer Netzwerksicherheitsgruppe ermöglichen nur den Zugriff über andere Netzwerkclients in demselben virtuellen Netzwerk.

Daher ist das Sperren des Zugriffs auf SQL Server ebenfalls einfach. Wenden Sie einfach nur eine Netzwerksicherheitsgruppe mit ihren Standardregeln entweder auf die virtuellen Computer mit SQL Server oder auf das Subnetz mit den virtuellen Computern an.

Im folgenden Beispiel wird eine Netzwerksicherheitsgruppe auf das enthaltende Subnetz angewendet:

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'
```

Das Endergebnis ist ein Satz von Sicherheitsregeln, die den externen Zugriff blockieren, während der VNet-interne Zugriff zugelassen wird:

![Standard-Netzwerksicherheitsgruppen][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>Erste Schritte
Informationen zum Einstieg in App Service-Umgebungen finden Sie unter [Einführung in die App Service-Umgebung][IntroToAppServiceEnvironment]

Details zum Steuern des eingehenden Datenverkehrs in Ihre App Service-Umgebung finden Sie unter [Steuern von eingehendem Datenverkehr in eine App Service-Umgebung][ControlInboundASE].

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[ControlInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SiteToSite]: ../../vpn-gateway/vpn-gateway-multi-site.md
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: /previous-versions/azure/virtual-network/virtual-networks-acl
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[ControlInboundASE]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png