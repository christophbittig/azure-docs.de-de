---
title: Steuern des eingehenden Datenverkehrs v1
description: Erfahren Sie, wie Sie eingehenden Datenverkehr in eine App Service-Umgebung steuern. Dieses Dokument wird nur für Kunden bereitgestellt, die die ASE-Legacyumgebung v1 verwenden.
author: madsd
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.topic: article
ms.date: 01/11/2017
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: 9fc4570d922da43635725f028a31b4458bf03113
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130001711"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Steuern von eingehendem Datenverkehr in eine App Service-Umgebung
## <a name="overview"></a>Übersicht
Eine App Service-Umgebung kann **entweder** in einem virtuellen Netzwerk von Azure Resource Manager **oder** einem [virtuellen Netzwerk][virtualnetwork] des klassischen Bereitstellungsmodells erstellt werden.  Ein neues virtuelles Netzwerk und ein neues Subnetz können bei der Erstellung einer App Service-Umgebung definiert werden. Stattdessen kann eine App Service-Umgebung in einem bereits vorhandenen virtuellen Netzwerk und einem bereits vorhandenen Subnetz erstellt werden.  Seit Juni 2016 können ASEs auch in virtuellen Netzwerken bereitgestellt werden, die entweder öffentliche Adressbereiche oder RFC1918-Adressräume (private Adressen) verwenden.  Weitere Informationen finden Sie unter [Erstellen einer ASEv1 aus einer Vorlage](app-service-app-service-environment-create-ilb-ase-resourcemanager.md).

Erstellen Sie eine App Service-Umgebung immer in einem Subnetz. Ein Subnetz bietet eine Netzwerkgrenze, die zum Sperren von eingehendem Datenverkehr hinter Upstreamgeräten und -diensten verwendet werden kann. Dieses Setup ermöglicht nur bestimmten Upstream-IP-Adressen das Akzeptieren von HTTP- und HTTPS-Datenverkehr.

Der ein- und ausgehende Netzwerkdatenverkehr in einem Subnetz wird anhand einer [Netzwerksicherheitsgruppe][NetworkSecurityGroups] gesteuert. Um eingehenden Datenverkehr zu steuern, erstellen Sie Netzwerksicherheitsregeln in einer Netzwerksicherheitsgruppe. Weisen Sie dann die Netzwerksicherheitsgruppe dem Subnetz zu, das die App Service-Umgebung enthält.

Sobald Sie einem Subnetz eine Netzwerksicherheitsgruppe zugewiesen haben, wird der in den Apps in der App Service-Umgebung eingehende Datenverkehr abhängig von den Zulassungs- und Ablehnungsregeln, die in der Netzwerksicherheitsgruppe definiert sind, zugelassen oder blockiert.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>In einer App Service-Umgebung verwendete eingehende Netzwerkports
Bevor Sie eingehenden Netzwerkdatenverkehr mithilfe einer Netzwerksicherheitsgruppe sperren, sollten Sie die erforderlichen und optionalen Netzwerkports kennen, die von einer App Service-Umgebung verwendet werden.  Das versehentliche Ausschließen von Datenverkehr für bestimmte Ports kann zu einem Funktionalitätsverlust in einer App Service-Umgebung führen.

Die folgende Liste enthält die Ports, die von einer App Service-Umgebung verwendet werden. Alle Ports haben den Typ **TCP**, sofern nichts anderes angegeben ist:

* 454:  **Erforderlicher Port**, der von der Azure-Infrastruktur für die Verwaltung und Wartung von App Service-Umgebungen über TLS verwendet wird.  Der Datenverkehr für diesen Port darf nicht blockiert werden.  Dieser Port ist immer an die öffentliche VIP-Adresse einer ASE gebunden.
* 455:  **Erforderlicher Port**, der von der Azure-Infrastruktur für die Verwaltung und Wartung von App Service-Umgebungen über TLS verwendet wird.  Der Datenverkehr für diesen Port darf nicht blockiert werden.  Dieser Port ist immer an die öffentliche VIP-Adresse einer ASE gebunden.
* 80:  Standardport für eingehenden HTTP-Datenverkehr in Apps, die in App Service-Plänen in einer App Service-Umgebung ausgeführt werden.  Dieser Port ist in einer für den internen Lastenausgleich geeigneten App Service-Umgebung an die Adresse der ASE für den internen Lastenausgleich gebunden.
* 443: Standardport für eingehenden TLS-Datenverkehr in Apps, die in App Service-Plänen in einer App Service-Umgebung ausgeführt werden.  Dieser Port ist in einer für den internen Lastenausgleich geeigneten App Service-Umgebung an die Adresse der ASE für den internen Lastenausgleich gebunden.
* 21:  Steuerungskanal für FTP.  Dieser Port kann sicher blockiert werden, wenn FTP nicht verwendet wird.  Dieser Port kann in einer für den internen Lastenausgleich geeigneten App Service-Umgebung an die Adresse der ASE für den internen Lastenausgleich gebunden werden.
* 990:  Steuerungskanal für FTPS.  Dieser Port kann sicher blockiert werden, wenn FTPS nicht verwendet wird.  Dieser Port kann in einer für den internen Lastenausgleich geeigneten App Service-Umgebung an die Adresse der ASE für den internen Lastenausgleich gebunden werden.
* 10001–10020: Datenkanäle für FTP.  Wie der Steuerungskanal können diese Ports sicher blockiert werden, wenn FTP nicht verwendet wird.  Dieser Port kann in einer für den internen Lastenausgleich geeigneten App Service-Umgebung an die Adresse der ASE für den internen Lastenausgleich gebunden werden.
* 4016: Wird zum Remotedebuggen in Visual Studio 2012 verwendet.  Dieser Port kann sicher blockiert werden, wenn die Funktion nicht verwendet wird.  Dieser Port ist in einer für den internen Lastenausgleich geeigneten App Service-Umgebung an die Adresse der ASE für den internen Lastenausgleich gebunden.
* 4018: Wird zum Remotedebuggen in Visual Studio 2013 verwendet.  Dieser Port kann sicher blockiert werden, wenn die Funktion nicht verwendet wird.  Dieser Port ist in einer für den internen Lastenausgleich geeigneten App Service-Umgebung an die Adresse der ASE für den internen Lastenausgleich gebunden.
* 4020: Wird zum Remotedebuggen in Visual Studio 2015 verwendet.  Dieser Port kann sicher blockiert werden, wenn die Funktion nicht verwendet wird.  Dieser Port ist in einer für den internen Lastenausgleich geeigneten App Service-Umgebung an die Adresse der ASE für den internen Lastenausgleich gebunden.

## <a name="outbound-connectivity-and-dns-requirements"></a>Ausgehende Verbindungen und DNS-Anforderungen
Damit eine App Service-Umgebung richtig funktioniert, ist auch ausgehender Zugriff auf verschiedene Endpunkte erforderlich. Eine vollständige Liste externer Endpunkte, die von einer ASE verwendet werden, befindet sich im Artikel [Netzwerkkonfiguration für ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) im Abschnitt „Erforderliche Netzwerkverbindung“.

App Service-Umgebungen erfordern zudem eine gültige DNS-Infrastruktur, die für das virtuelle Netzwerk konfiguriert ist.  Wenn die DNS-Konfiguration nach der Erstellung einer App Service-Umgebung geändert wird, können Entwickler erzwingen, dass eine App Service-Umgebung die neue DNS-Konfiguration übernimmt.  Wenn Sie einen parallelen Neustart der Umgebung mithilfe des Symbols **Neustart** auslösen, übernimmt die Umgebung die neue DNS-Konfiguration. (Das Symbol **Neustart** befindet sich am oberen Rand des Blatts für die App Service-Umgebungsverwaltung im [Azure-Portal][NewPortal].)

Es empfiehlt sich auch, benutzerdefinierte DNS-Server im VNet vor dem Erstellen einer App-Service-Umgebung einzurichten.  Wenn die DNS-Konfiguration eines virtuellen Netzwerks geändert wird, während eine App Service-Umgebung erstellt wird, schlägt das Erstellen der App Service-Umgebung fehl.  Wenn ein benutzerdefinierter DNS-Server am anderen Ende eines VPN-Gateways vorhanden ist und der DNS-Server nicht erreichbar oder nicht verfügbar ist, kommt es ebenso zu einem Fehler beim Erstellen der App-Service-Umgebung.

## <a name="creating-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe
Einzelheiten zur Funktionsweise von Netzwerksicherheitsgruppen finden Sie in den folgenden [Informationen][NetworkSecurityGroups].  Das Azure-Dienstverwaltungsbeispiel unten berührt die wichtigsten Aspekte von Netzwerksicherheitsgruppen. In dem Beispiel wird eine Netzwerksicherheitsgruppe konfiguriert und auf ein Subnetz angewendet, das eine App Service-Umgebung enthält.

**Hinweis:** Netzwerksicherheitsgruppen können im [Azure-Portal](https://portal.azure.com) oder über Azure PowerShell grafisch konfiguriert werden.

Netzwerksicherheitsgruppen werden zuerst als eigenständige Entität erstellt, die einem Abonnement zugeordnet ist. Da Netzwerksicherheitsgruppen in einer Azure-Region erstellt werden, erstellen Sie die Netzwerksicherheitsgruppe in derselben Region wie die App Service-Umgebung.

Der folgende Befehl veranschaulicht das Erstellen einer Netzwerksicherheitsgruppe:

```azurepowershell-interactive
New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"
```

Sobald eine Netzwerksicherheitsgruppe erstellt wurde, werden ihr eine oder mehrere Netzwerksicherheitsregeln hinzugefügt.  Da der Regelsatz sich mit der Zeit ändern kann, sollten Sie in dem für Regelprioritäten verwendeten Nummerierungsschema Abstände einräumen. Diese Vorgehensweise erleichtert das Einfügen zusätzlicher Regeln im Laufe der Zeit.

In dem folgenden Beispiel gewährt eine Regel explizit Zugriff auf die Verwaltungsports, die von der Azure-Infrastruktur für die Verwaltung und Wartung einer App Service-Umgebung benötigt werden.  Der gesamte Verwaltungsdatenverkehr erfolgt über TLS und wird durch Clientzertifikate gesichert. Selbst wenn die Ports geöffnet werden, sind sie für keine Entität außer für die Azure-Verwaltungsinfrastruktur zugänglich.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
```

Wenn Sie den Zugriff auf Port 80 und 443 sperren, um eine App Service-Umgebung hinter Upstreamgeräten oder -diensten zu „verstecken“, merken Sie sich die Upstream-IP-Adresse.  Wenn Sie beispielsweise eine Web Application Firewall (WAF) verwenden, besitzt die WAF eine eigene IP-Adresse (oder mehrere Adressen). Die WAF verwendet diese beim Leiten von Datenverkehr über einen Proxy an eine Downstream-App Service-Umgebung.  Diese IP-Adresse müssen Sie im Parameter *SourceAddressPrefix* einer Netzwerksicherheitsregel verwenden.

Im folgenden Beispiel wird der eingehende Datenverkehr von einer bestimmten Upstream-IP-Adresse explizit zugelassen.  Die Adresse *1.2.3.4* dient als Platzhalter für die IP-Adresse einer Upstream-WAF.  Ändern Sie den Wert in die Adresse, die von Ihrem Upstreamgerät oder -dienst verwendet wird.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

Wenn FTP-Unterstützung gewünscht wird, verwenden Sie die folgenden Regeln als Vorlage für das Erteilen des Zugriffs auf den FTP-Steuerungsport und die Datenkanalports.  Da es sich bei FTP um ein zustandsbehaftetes Protokoll handelt, können Sie FTP-Datenverkehr möglicherweise nicht über eine herkömmliche HTTP/HTTPS-Firewall oder ein Proxygerät leiten.  In diesem Fall müssen Sie das *SourceAddressPrefix* auf einen anderen Wert festlegen, z. B. den IP-Adressbereich von Entwicklungs- oder Bereitstellungscomputern, auf denen FTP-Clients ausgeführt werden. 

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP
```

(**Hinweis:** Der Datenkanal-Portbereich kann sich während des Vorschauzeitraums unter Umständen ändern.)

Für das Remotedebuggen mit Visual Studio wird das Erteilen des Zugriffs durch die folgenden Regeln veranschaulicht.  Es gibt für jede unterstützte Version von Visual Studio eine separate Regel, da jede Version einen anderen Port für das Remotedebuggen verwendet.  Wie beim FTP-Zugriff fließt der Datenverkehr für das Remotedebuggen möglicherweise nicht ordnungsgemäß durch eine herkömmliche WAF oder ein Proxygerät.  Das *SourceAddressPrefix* kann stattdessen auf den IP-Adressbereich von Entwicklungscomputern mit Visual Studio festgelegt werden.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP
```

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Zuweisen einer Netzwerksicherheitsgruppe zu einem Subnetz
Eine Netzwerksicherheitsgruppe verfügt über eine Standardsicherheitsregel, die den Zugriff auf den gesamten externen Datenverkehr verweigert. Wenn Sie diese Regel mit den voranstehenden Netzwerksicherheitsregeln kombinieren, kann nur Datenverkehr von Quelladressbereichen, die einer *Zulassen*-Aktion zugeordnet sind, Datenverkehr an Apps senden, die in einer App Service-Umgebung ausgeführt werden.

Nachdem eine Netzwerksicherheitsgruppe mit Sicherheitsregeln aufgefüllt wurde, weisen Sie diese dem Subnetz zu, das die App Service-Umgebung enthält.  Der Befehl für die Zuweisung verweist auf zwei Namen: den Namen des virtuellen Netzwerks, in dem sich die App Service-Umgebung befindet, sowie den Namen des Subnetzes, in dem die App Service-Umgebung erstellt wurde.  

Das folgende Beispiel zeigt eine Netzwerksicherheitsgruppe, die einem Subnetz und einem virtuellen Netzwerk zugewiesen wird:

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'
```

Die Zuweisung ist ein Vorgang mit langer Laufzeit, und es kann einige Minuten dauern, bis er abgeschlossen ist. Nach der erfolgreichen Zuweisung der Netzwerksicherheitsgruppe erreicht nur derjenige eingehende Datenverkehr die Apps in der App Service-Umgebung, der den *Zulassen*-Regeln entspricht.

Das folgende Beispiel zeigt der Vollständigkeit halber das Entfernen und Trennen der Netzwerksicherheitsgruppe vom Subnetz:

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'
```

## <a name="special-considerations-for-explicit-ip-ssl"></a>Besonderheiten für explizites IP-SSL
Wenn eine App mit einer expliziten SSL-IP-Adresse (anwendbar *ausschließlich* auf ASEs, die nur eine öffentliche VIP-Adresse besitzen) und nicht mit der Standard-IP-Adresse der App Service-Umgebung konfiguriert wird, fließt sowohl HTTP- als auch HTTPS-Datenverkehr über andere Ports als die Ports 80 und 443 in das Subnetz.

Um die von jeder IP-SSL-Adresse verwendeten einzelnen Portpaare zu finden, wechseln Sie zum Portal, und zeigen Sie das Blatt „Details-UX“ der App Service-Umgebung an.  Wählen Sie **Alle Einstellungen** > **IP-Adressen** aus.  Das Blatt **IP-Adressen** enthält eine Tabelle aller explizit konfigurierten IP-SSL-Adressen für die App Service-Umgebung. Ferner enthält das Blatt das spezifische Portpaar, mit dem HTTP- und HTTPS-Datenverkehr, der jeder IP-SSL-Adresse zugeordnet ist, weitergeleitet wird.  Verwenden Sie dieses Portpaar beim Konfigurieren von Regeln in einer Netzwerksicherheitsgruppe für die DestinationPortRange-Parameter.

Wenn eine App in einer ASE für die IP-SSL-Adresse konfiguriert ist, wird die spezielle Portpaarzuordnung externen Kunden nicht angezeigt, und sie müssen sich darüber keine Gedanken machen.  Der Datenverkehr zu den Apps fließt normal an die konfigurierte IP-SSL-Adresse.  Die Übersetzung in das spezielle Portpaar geschieht automatisch intern während des letzten Abschnitts des Datenverkehrsroutings in das Subnetz, das die ASE enthält. 

## <a name="getting-started"></a>Erste Schritte
Informationen zum Einstieg in App Service-Umgebungen finden Sie unter [Einführung in die App Service-Umgebung][IntroToAppServiceEnvironment].

Weitere Informationen finden Sie unter [Sicheres Verbinden mit Back-End-Ressourcen von einer App Service-Umgebung aus][SecurelyConnecttoBackend].

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->