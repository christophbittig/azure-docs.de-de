---
title: Mehrstufige Sicherheit v1
description: Informieren Sie sich, wie Sie eine mehrstufige Sicherheitsarchitektur in Ihre App Service-Umgebung implementieren. Dieses Dokument wird nur für Kunden bereitgestellt, die die ASE-Legacyumgebung v1 verwenden.
author: madsd
ms.assetid: 73ce0213-bd3e-4876-b1ed-5ecad4ad5601
ms.topic: article
ms.date: 08/30/2016
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: d2245cc7557b7f5d6c6c392957fe0ef817975c53
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130001673"
---
# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>Implementieren einer mehrstufigen Sicherheitsarchitektur mit App Service-Umgebungen
Da App Service-Umgebungen eine in einem virtuellen Netzwerk bereitgestellte isolierte Laufzeitumgebung bieten, können Entwickler eine mehrstufige Sicherheitsarchitektur erstellen, in der sie abgestuften Netzwerkzugriff für jede physische Anwendungsschicht gewähren können.

Üblicherweise sollten API-Back-Ends nicht dem allgemeinen Internetzugriff preisgegeben werden, und APIs sollten nur von Upstream-Web-Apps aufgerufen werden können.  Um den öffentlichen Zugriff auf API-Anwendungen zu beschränken, können [Netzwerksicherheitsgruppen (Network Security Groups, NSGs)][NetworkSecurityGroups] in Subnetzen mit App Service-Umgebungen verwendet werden.

Das nachfolgende Diagramm zeigt eine Beispielarchitektur mit einer WebAPI-basierten App, die in einer App Service-Umgebung bereitgestellt wurde.  Drei getrennte Web-App-Instanzen, die in drei getrennten App Service-Umgebungen bereitgestellt wurden, führen Back-End-Aufrufe an die gleiche WebAPI-App aus.

![Konzeptionelle Architektur][ConceptualArchitecture] 

Die grünen Pluszeichen weisen darauf hin, dass die Netzwerksicherheitsgruppe im Subnetz, das „apiase“ enthält, eingehende Aufrufe von den Upstream-Web-Apps sowie Aufrufe der App an sich selbst zulässt.  Die gleiche Netzwerksicherheitsgruppe verweigert jedoch explizit den Zugriff durch allgemeinen eingehenden Datenverkehr aus dem Internet. 

Im weiteren Verlauf dieses Artikels werden die Schritte erläutert, die zum Konfigurieren der Netzwerksicherheitsgruppe in dem Subnetz mit „apiase“ erforderlich sind.

## <a name="determining-the-network-behavior"></a>Festlegen des Netzwerkverhaltens
Um zu ermitteln, welche Netzwerksicherheitsregeln erforderlich sind, müssen Sie festlegen, welche Netzwerkclients auf die App Service-Umgebung mit der API-App zugreifen können und welche Clients blockiert werden.

Da [Netzwerksicherheitsgruppen][NetworkSecurityGroups] auf Subnetze angewendet und App Service-Umgebungen in Subnetzen bereitgestellt werden, gelten die Regeln einer Netzwerksicherheitsgruppe für **alle** Apps, die in der jeweiligen App Service-Umgebung ausgeführt werden.  Betrachten Sie die Beispielarchitektur für diesen Artikel: Sobald eine Netzwerksicherheitsgruppe auf das Subnetz angewendet wird, das „apiase“ enthält, werden alle Apps, die in der App Service-Umgebung „apiase“ ausgeführt werden, durch den gleichen Satz Sicherheitsregeln geschützt. 

* **Ermitteln der ausgehenden IP-Adressen von Aufruffunktionen für den Upstream:**  Wie lauten die IP-Adressen der Aufruffunktionen für den Upstream?  Diesen Adressen muss in der Netzwerksicherheitsgruppe explizit Zugriff gewährt werden.  Da Aufrufe zwischen App Service-Umgebungen als „Internetaufrufe“ betrachtet werden, muss der ausgehenden IP-Adresse, die den einzelnen Upstream-Umgebungen von App Service zugewiesen ist, in der Netzwerksicherheitsgruppe Zugriff auf das Subnetz „apiase“ gewährt werden.   Weitere Informationen zum Ermitteln der ausgehenden IP-Adresse für Apps, die in einer App Service-Umgebung ausgeführt werden, finden Sie im Artikel [Übersicht über die Netzwerkarchitektur][NetworkArchitecture].
* **Muss die Back-End-API-App sich selbst aufrufen?**  Ein Aspekt, der nicht selten übersehen wird, ist ein Szenario, in dem die Back-End-Anwendung sich selbst aufrufen muss.  Wenn sich die Back-End-API-Anwendung in einer App Service-Umgebung selbst aufrufen muss, wird auch dies als „Internetaufruf“ behandelt.  In der Beispielarchitektur muss zu diesem Zweck auch der Zugriff von der ausgehenden IP-Adresse der App Service-Umgebung „apiase“ gewährt werden.

## <a name="setting-up-the-network-security-group"></a>Einrichten der Netzwerksicherheitsgruppe
Sobald die ausgehenden IP-Adressen bekannt sind, besteht der nächste Schritt darin, eine Netzwerksicherheitsgruppe zu erstellen und einzurichten.  Netzwerksicherheitsgruppen können sowohl für Resource Manager-basierte virtuelle Netzwerke als auch für klassische virtuelle Netzwerke erstellt werden.  Die folgenden Beispiele veranschaulichen das Erstellen und Konfigurieren einer NSG in einem klassischen virtuellen Netzwerk mit PowerShell.

In der Beispielarchitektur befinden sich die Umgebungen in der Region „USA, Süden-Mitte“, daher wird in dieser Region eine leere Netzwerksicherheitsgruppe erstellt:

```azurepowershell-interactive
New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" 
-Label "Only allow web frontend and loopback traffic"
```

Zuerst wird eine explizite Zulassungsregel für die Azure-Verwaltungsinfrastruktur hinzugefügt, wie im Artikel zu [eingehendem Datenverkehr][InboundTraffic] für App Service-Umgebungen erläutert.

```azurepowershell-interactive
#Open ports for access by Azure management infrastructure
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" 
-Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
```

Als Nächstes werden zwei Regeln hinzugefügt, die HTTP- und HTTPS-Aufrufe aus der ersten App Service-Upstreamumgebung („fe1ase“) zulassen.

```azurepowershell-interactive
#Grant access to requests from the first upstream web front-end
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" 
-Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" 
-Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

Wiederholen Sie diesen Schritt für die zweite und dritte App Service-Upstreamumgebung („fe2ase“ und „fe3ase“).

```azurepowershell-interactive
#Grant access to requests from the second upstream web front-end
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" 
-Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" 
-Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

#Grant access to requests from the third upstream web front-end
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" 
-Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" 
-Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

Zum Schluss wird der ausgehenden IP-Adresse der App Service-Umgebung der Back-End-API Zugriff gewährt, sodass diese API sich selbst aufrufen kann.

```azurepowershell-interactive
#Allow apps on the apiase environment to call back into itself
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" 
-Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" 
-Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

Es sind keine weiteren Netzwerksicherheitsregeln erforderlich, da jede Netzwerksicherheitsgruppe über einen Satz Standardregeln verfügt, die Zugriffsversuche aus dem Internet standardmäßig blockieren.

Unten sehen Sie die vollständige Liste der Regeln in der Netzwerksicherheitsgruppe.  Beachten Sie, dass die letzte, hervorgehobene Regel den Zugriff aller aufrufenden Funktionen blockiert, mit Ausnahme derjenigen, denen explizit Zugriff gewährt wurde.

![Konfiguration von Netzwerksicherheitsgruppen][NSGConfiguration] 

Der letzte Schritt besteht darin, die Netzwerksicherheitsgruppe auf das Subnetz anzuwenden, in dem sich die App Service-Umgebung „apiase“ befindet.

```azurepowershell-interactive
#Apply the NSG to the backend API subnet
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet 
-VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'
```

Nach Anwendung der Netzwerksicherheitsgruppe auf das Subnetz sind Aufrufe an die „apiase“-Umgebung nur noch für die drei App Service-Upstreamumgebungen sowie die App Service-Umgebung, die das API-Back-End umfasst, zulässig.

## <a name="additional-links-and-information"></a>Zusätzliche Links und Informationen
Informationen zu [Netzwerksicherheitsgruppen](../../virtual-network/network-security-groups-overview.md)

Informationen zu [ausgehenden IP-Adressen][NetworkArchitecture] und App Service-Umgebungen.

Von App Service-Umgebungen verwendete [Netzwerkports][InboundTraffic].

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[NetworkArchitecture]:  app-service-app-service-environment-network-architecture-overview.md
[InboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png