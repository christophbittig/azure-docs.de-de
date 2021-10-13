---
title: Verwenden von API Management in einem virtuellen Netzwerk mit Azure Application Gateway
titleSuffix: Azure API Management
description: Hier finden Sie Informationen zum Einrichten und Konfigurieren von Azure API Management in einem internen virtuellen Netzwerk mit Application Gateway (Web Application Firewall) als Front-End.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.author: danlep
ms.date: 06/10/2021
ms.custom: devx-track-azurepowershell,contperf-fy21q4
ms.openlocfilehash: 05767ffb0487964780ab25ec56fd586451066ac3
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2021
ms.locfileid: "129615351"
---
# <a name="integrate-api-management-in-an-internal-virtual-network-with-application-gateway"></a>Integrieren von API Management in ein internes virtuelles Netzwerk mit Application Gateway

Der API Management-Dienst kann in einem [virtuellen Netzwerk im internen Modus](api-management-using-with-internal-vnet.md) konfiguriert werden, sodass nur innerhalb des virtuellen Netzwerks darauf zugegriffen werden kann. [Azure Application Gateway](../application-gateway/overview.md) ist ein PaaS-Dienst, der als Layer 7-Lastenausgleich agiert. Er fungiert als Reverseproxydienst und bietet unter anderem eine Web Application Firewall (WAF).

Die Kombination aus API Management in einem internen virtuellen Netzwerk und Application Gateway-Front-End ermöglicht Folgendes:

* Verwenden Sie dieselbe API Management-Ressource für die Nutzung durch interne und externe Consumer.
* Verwenden Sie nur eine API Management-Ressource, und halten Sie eine Teilmenge der in API Management definierten APIs für externe Consumer bereit.
* Schaffen Sie eine einfache Möglichkeit zum Aktivieren und Deaktivieren des Zugriffs auf API Management über das öffentliche Internet.

> [!NOTE]
> Dieser Artikel wurde für die Verwendung der [WAF_v2-SKU für Application Gateway](../application-gateway/application-gateway-autoscaling-zone-redundant.md) aktualisiert.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zum Ausführen der in diesem Artikel beschriebenen Schritte benötigen Sie Folgendes:

* Ein aktives Azure-Abonnement.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Zertifikate
     - PFX-Dateien für benutzerdefinierte Hostnamen des API Management-Diensts: Gateway, Entwicklerportal und Verwaltungsendpunkt. 
     - Eine CER-Datei für das Stammzertifikat der PFX-Zertifikate. 
     
    Weitere Informationen finden Sie unter [Erstellen von Zertifikaten zum Zulassen des Back-Ends für Azure Application Gateway](../application-gateway/certificates-for-backend-authentication.md). Zu Testzwecken können optional [selbstsignierte Zertifikate](../application-gateway/self-signed-certificates.md) generiert werden.
* Die aktuelle Version von Azure PowerShell. [Installieren Sie Azure PowerShell](/powershell/azure/install-az-ps), sofern noch nicht geschehen.

## <a name="scenario"></a>Szenario

In diesem Artikel erfahren Sie, wie Sie einen einzelnen API Management-Dienst sowohl für interne als auch für externe Consumer verwenden und als einzelnes Front-End für lokale und cloudbasierte APIs einrichten. Außerdem wird erläutert, wie Sie nur eine Teilmenge Ihrer APIs (im Beispiel grün markiert) für die externe Nutzung verfügbar machen, indem Sie die Routingfunktion von Application Gateway verwenden.

Im ersten Setupbeispiel werden alle APIs ausschließlich innerhalb Ihres virtuellen Netzwerks verwaltet. Interne Consumer (orange gekennzeichnet) können auf alle internen und externen APIs zugreifen. Der Datenverkehr gelangt nie ins Internet. Hochleistungskonnektivität kann über ExpressRoute-Verbindungen bereitgestellt werden.

![URL-Route](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

### <a name="what-is-required-to-integrate-api-management-and-application-gateway"></a>Was ist für die Integration von API Management und Application Gateway erforderlich?

* **Back-End-Serverpool:** Dies ist die interne virtuelle IP-Adresse des API Management-Diensts.
* **Einstellungen für den Back-End-Serverpool:** Für jeden Pool sind Einstellungen wie Port, Protokoll und cookiebasierte Affinität festgelegt. Diese Einstellungen werden auf alle Server innerhalb des Pools angewendet.
* **Front-End-Port:** Dies ist der öffentliche Port, der im Anwendungsgateway geöffnet ist. Datenverkehr, der an diesem Port eintrifft, wird an einen der Back-End-Server umgeleitet.
* **Listener:** Der Listener verfügt über einen Front-End-Port, ein Protokoll („Http“ oder „Https“; jeweils unter Beachtung der Groß-/Kleinschreibung) und den Namen des TLS/SSL-Zertifikats (falls die TLS-Auslagerung konfiguriert wird).
* **Regel:** Durch die Regel wird ein Listener an einen Back-End-Serverpool gebunden.
* **Benutzerdefinierter Integritätstest:** Für Application Gateway werden standardmäßig auf IP-Adressen basierende Tests verwendet, um zu ermitteln, welche Server im Back-End-Adresspool aktiv sind. Der API Management-Dienst reagiert nur auf Anforderungen mit den richtigen Hostheadern, sodass bei den Standardtests ein Fehler auftritt. Ein benutzerdefinierter Integritätstest wird definiert, damit vom Anwendungsgateway ermittelt werden kann, ob der Dienst aktiv ist und Anforderungen weitergeleitet werden.
* **Benutzerdefinierte Domänenzertifikate:** Zum Zugreifen auf API Management über das Internet müssen Sie eine CNAME-Zuordnung des Hostnamens zum DNS-Namen des Application Gateway-Front-Ends erstellen. So wird sichergestellt, dass der Hostnamenheader und das Zertifikat, die an Application Gateway gesendet und an API Management weitergeleitet werden, von API Management als gültig erkannt werden. In diesem Beispiel werden drei Zertifikate verwendet: eins für das Gateway des API Management-Diensts (das Back-End), eins für das Entwicklerportal und eins für den Verwaltungsendpunkt.  

## <a name="steps-required-to-integrate-api-management-and-application-gateway"></a>Erforderliche Schritte für die Integration von API Management und Application Gateway

1. Erstellen einer Ressourcengruppe für den Ressourcen-Manager
1. Erstellen eines virtuellen Netzwerks, eines Subnetzes und einer öffentlichen IP-Adresse für Application Gateway Erstellen eines anderen Subnetzes für API Management
1. Erstellen eines API Management-Diensts im VNet-Subnetz aus dem vorherigen Schritt. Achten Sie darauf, dass Sie den internen Modus verwenden.
1. Einrichten benutzerdefinierter Domänennamen im API Management-Dienst
1. Konfigurieren einer privaten DNS-Zone für die DNS-Auflösung im virtuellen Netzwerk
1. Erstellen eines Konfigurationsobjekts für das Application Gateway
1. Erstellen einer Application Gateway-Ressource
1. Erstellen eines CNAME-Eintrags aus dem öffentlichen DNS-Namen des Application Gateway für den API Management-Proxyhostnamen

### <a name="expose-the-developer-portal-and-management-endpoint-externally-through-application-gateway"></a>Externes Verfügbarmachen des Entwicklerportals und des Verwaltungsendpunkts über Application Gateway

In diesem Leitfaden werden das **Entwicklerportal** und der **Verwaltungsendpunkt** über das Anwendungsgateway auch für externe Zielgruppen verfügbar gemacht. Hierzu müssen zusätzliche Schritte ausgeführt werden, um einen Listener, einen Test, Einstellungen und Regeln für die einzelnen Endpunkte zu erstellen. Alle Details finden Sie in den entsprechenden Schritten.

> [!WARNING]
> Wenn Sie Azure AD oder die Authentifizierung eines Drittanbieters verwenden, aktivieren Sie in Application Gateway das Feature [Cookiebasierte Sitzungsaffinität](../application-gateway/features.md#session-affinity).

> [!WARNING]
> Um zu verhindern, dass das Herunterladen von OpenAPI-Spezifikationen im Entwicklerportal durch die Application Gateway-WAF unterbrochen wird, muss die Firewallregel `942200 - "Detects MySQL comment-/space-obfuscated injections and backtick termination"` deaktiviert werden.
> 
> Folgende Application Gateway-WAF-Regeln können die Funktionalität des Portals beeinträchtigen:
> 
> - `920300`, `920330`, `931130`, `942100`, `942110`, `942180`, `942200`, `942260`, `942340`, `942370` für den Verwaltungsmodus
> - `942200`, `942260`, `942370`, `942430`, `942440` für das veröffentlichte Portal

## <a name="create-a-resource-group-for-resource-manager"></a>Erstellen einer Ressourcengruppe für den Resource Manager

### <a name="step-1"></a>Schritt 1

Anmelden an Azure

```powershell
Connect-AzAccount
```

Authentifizieren Sie sich mit Ihren Anmeldeinformationen.

### <a name="step-2"></a>Schritt 2

Wählen Sie das gewünschte Abonnement aus.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000" # GUID of your Azure subscription
Get-AzSubscription -Subscriptionid $subscriptionId | Select-AzSubscription
```

### <a name="step-3"></a>Schritt 3

Erstellen Sie eine Ressourcengruppe. (Überspringen Sie diesen Schritt, wenn Sie eine vorhandene Ressourcengruppe verwenden.)

```powershell
$resGroupName = "apim-appGw-RG" # resource group name
$location = "West US"           # Azure region
New-AzResourceGroup -Name $resGroupName -Location $location
```

Azure Resource Manager erfordert, dass alle Ressourcengruppen einen Speicherort angeben. Dieser wird als Standardspeicherort für Ressourcen in dieser Ressourcengruppe verwendet. Stellen Sie sicher, dass alle Befehle, mit denen ein Anwendungsgateway erstellt wird, die gleiche Ressourcengruppe verwenden.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Erstellen eines virtuelles Netzwerkes und eines Subnetzes für das Application Gateway.

Das folgende Beispiel zeigt, wie Sie mit Resource Manager ein virtuelles Netzwerk erstellen. Das virtuelle Netzwerk in diesem Beispiel besteht aus separaten Subnetzen für Application Gateway und API Management.

### <a name="step-1"></a>Schritt 1

Erstellen Sie Netzwerksicherheitsgruppen und NSG-Regeln für die Subnetze für Application Gateway und API Management.

```powershell
$appGwRule1 = New-AzNetworkSecurityRuleConfig -Name appgw-in -Description "AppGw inbound" `
    -Access Allow -Protocol * -Direction Inbound -Priority 100 -SourceAddressPrefix `
    GatewayManager -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 65200-65535
$appGwRule2 = New-AzNetworkSecurityRuleConfig -Name appgw-in-internet -Description "AppGw inbound Internet" `
    -Access Allow -Protocol "TCP" -Direction Inbound -Priority 110 -SourceAddressPrefix `
    Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 443
$appGwNsg = New-AzNetworkSecurityGroup -ResourceGroupName $resGroupName -Location $location -Name `
    "NSG-APPGW" -SecurityRules $appGwRule1, $appGwRule2

$apimRule1 = New-AzNetworkSecurityRuleConfig -Name apim-in -Description "APIM inbound" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix `
    ApiManagement -SourcePortRange * -DestinationAddressPrefix VirtualNetwork -DestinationPortRange 3443
$apimNsg = New-AzNetworkSecurityGroup -ResourceGroupName $resGroupName -Location $location -Name `
    "NSG-APIM" -SecurityRules $apimRule1
```

### <a name="step-2"></a>Schritt 2

Weisen Sie der für Application Gateway vorgesehenen Subnetzvariablen beim Erstellen eines virtuellen Netzwerks den Adressbereich 10.0.0.0/24 zu.

```powershell
$appGatewaySubnet = New-AzVirtualNetworkSubnetConfig -Name "appGatewaySubnet" -NetworkSecurityGroup $appGwNsg -AddressPrefix "10.0.0.0/24"
```

### <a name="step-3"></a>Schritt 3

Weisen Sie der für API Management vorgesehenen Subnetzvariablen beim Erstellen eines virtuellen Netzwerks den Adressbereich 10.0.1.0/24 zu.

```powershell
$apimSubnet = New-AzVirtualNetworkSubnetConfig -Name "apimSubnet" -NetworkSecurityGroup $apimNsg -AddressPrefix "10.0.1.0/24"
```

### <a name="step-4"></a>Schritt 4

Erstellen Sie ein virtuelles Netzwerk mit dem Namen **appgwvnet** in der Ressourcengruppe **apim-appGw-RG** für die Region „USA, Westen“. Verwenden Sie das Präfix „10.0.0.0/16“ mit den Subnetzen „10.0.0.0/24“ und „10.0.1.0/24“.

```powershell
$vnet = New-AzVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName `
  -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appGatewaySubnet,$apimSubnet
```

### <a name="step-5"></a>Schritt 5

Weisen Sie Subnetzvariablen für die nächsten Schritte zu.

```powershell
$appGatewaySubnetData = $vnet.Subnets[0]
$apimSubnetData = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-virtual-network-configured-in-internal-mode"></a>Erstellen eines API Management-Diensts in einem virtuellen Netzwerk, das im internen Modus konfiguriert ist

Im folgenden Beispiel wird veranschaulicht, wie Sie einen API Management-Dienst in einem virtuellen Netzwerk erstellen, das nur für internen Zugriff konfiguriert ist.

### <a name="step-1"></a>Schritt 1

Erstellen Sie ein Objekt vom Typ „Virtuelles API Management-Netzwerk“ unter Verwendung des weiter oben erstellten Subnetzes `$apimSubnetData`.

```powershell
$apimVirtualNetwork = New-AzApiManagementVirtualNetwork -SubnetResourceId $apimSubnetData.Id
```

### <a name="step-2"></a>Schritt 2

Erstellen Sie innerhalb des virtuellen Netzwerks einen API Management-Dienst. In diesem Beispiel wird der Dienst auf der Dienstebene „Developer“ erstellt. Geben Sie einen eindeutigen Namen für Ihren API Management-Dienst an.

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name, must be globally unique
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

Es dauert in der Regel zwischen 30 und 40 Minuten, einen API Management-Dienst mit diesem Tarif zu erstellen und zu aktivieren. Nachdem der vorherige Befehl erfolgreich ausgeführt wurde, lesen Sie bitte [DNS-Konfiguration für den Zugriff auf den API Management-Dienst des internen virtuellen Netzwerks](api-management-using-with-internal-vnet.md#dns-configuration), um den Zugriff darauf zu bestätigen. 

## <a name="set-up-custom-domain-names-in-api-management"></a>Einrichten benutzerdefinierter Domänennamen in API Management

### <a name="step-1"></a>Schritt 1

Initialisieren Sie die folgenden Variablen mit den Details der Zertifikate mit privaten Schlüsseln für die Domänen und des vertrauenswürdigen Stammzertifikats. In diesem Beispiel werden `api.contoso.net`, `portal.contoso.net` und `management.contoso.net` verwendet.  

```powershell
$gatewayHostname = "api.contoso.net"                 # API gateway host
$portalHostname = "portal.contoso.net"               # API developer portal host
$managementHostname = "management.contoso.net"               # API management endpoint host
$gatewayCertPfxPath = "C:\Users\Contoso\gateway.pfx" # full path to api.contoso.net .pfx file
$portalCertPfxPath = "C:\Users\Contoso\portal.pfx"   # full path to portal.contoso.net .pfx file
$managementCertPfxPath = "C:\Users\Contoso\management.pfx"   # full path to management.contoso.net .pfx file
$gatewayCertPfxPassword = "certificatePassword123"   # password for api.contoso.net pfx certificate
$portalCertPfxPassword = "certificatePassword123"    # password for portal.contoso.net pfx certificate
$managementCertPfxPassword = "certificatePassword123"    # password for management.contoso.net pfx certificate
# Path to trusted root CER file used in Application Gateway HTTP settings
$trustedRootCertCerPath = "C:\Users\Contoso\trustedroot.cer" # full path to contoso.net trusted root .cer file

$certGatewayPwd = ConvertTo-SecureString -String $gatewayCertPfxPassword -AsPlainText -Force
$certPortalPwd = ConvertTo-SecureString -String $portalCertPfxPassword -AsPlainText -Force
$certManagementPwd = ConvertTo-SecureString -String $managementCertPfxPassword -AsPlainText -Force
```

### <a name="step-2"></a>Schritt 2

Erstellen Sie die Hostnamenkonfigurationsobjekte für die API Management-Endpunkte, und legen Sie sie fest.  

```powershell
$gatewayHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $gatewayHostname `
  -HostnameType Proxy -PfxPath $gatewayCertPfxPath -PfxPassword $certGatewayPwd
$portalHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $portalHostname `
  -HostnameType DeveloperPortal -PfxPath $portalCertPfxPath -PfxPassword $certPortalPwd
$managementHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $managementHostname `
  -HostnameType Management -PfxPath $managementCertPfxPath -PfxPassword $certManagementPwd

$apimService.ProxyCustomHostnameConfiguration = $gatewayHostnameConfig
$apimService.PortalCustomHostnameConfiguration = $portalHostnameConfig
$apimService.ManagementCustomHostnameConfiguration = $managementHostnameConfig

Set-AzApiManagement -InputObject $apimService
```

> [!NOTE]
> Zum Konfigurieren der Konnektivität mit dem Legacy-Entwicklerportal muss `-HostnameType DeveloperPortal` durch `-HostnameType Portal` ersetzt werden.

## <a name="configure-a-private-zone-for-dns-resolution-in-the-virtual-network"></a>Konfigurieren einer privaten Zone für die DNS-Auflösung im virtuellen Netzwerk

### <a name="step-1"></a>Schritt 1

Erstellen Sie eine private DNS-Zone, und verknüpfen Sie das virtuelle Netzwerk.

```powershell
$myZone = New-AzPrivateDnsZone -Name "contoso.net" -ResourceGroupName $resGroupName 
$link = New-AzPrivateDnsVirtualNetworkLink -ZoneName contoso.net `
  -ResourceGroupName $resGroupName -Name "mylink" `
  -VirtualNetworkId $vnet.id
```

### <a name="step-2"></a>Schritt 2

Erstellen Sie A-Einträge für die benutzerdefinierten Domänenhostnamen, die der privaten IP-Adresse des API Management-Diensts zugeordnet sind:

```powershell
$apimIP = $apimService.PrivateIPAddresses[0]

New-AzPrivateDnsRecordSet -Name api -RecordType A -ZoneName contoso.net `
  -ResourceGroupName $resGroupName -Ttl 3600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $apimIP)
New-AzPrivateDnsRecordSet -Name portal -RecordType A -ZoneName contoso.net `
  -ResourceGroupName $resGroupName -Ttl 3600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $apimIP)
New-AzPrivateDnsRecordSet -Name management -RecordType A -ZoneName contoso.net `
  -ResourceGroupName $resGroupName -Ttl 3600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $apimIP)
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Erstellen der öffentlichen IP-Adresse für die Front-End-Konfiguration

Erstellen Sie in der Ressourcengruppe eine Ressource vom Typ „Öffentliche IP-Standardadresse“ namens **publicIP01**.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $resGroupName `
  -name "publicIP01" -location $location -AllocationMethod Static -Sku Standard
```

Dem Anwendungsgateway wird beim Starten des Diensts eine IP-Adresse zugewiesen.

## <a name="create-application-gateway-configuration"></a>Erstellen einer Application Gateway-Konfiguration

Sie müssen alle Konfigurationselemente einrichten, bevor Sie das Anwendungsgateway erstellen. Die folgenden Schritten erstellen die Konfigurationselemente, die für eine Application Gateway-Ressource benötigt werden.

### <a name="step-1"></a>Schritt 1

Erstellen Sie für das Anwendungsgateway eine IP-Konfiguration mit dem Namen **gatewayIP01**. Beim Start von Application Gateway wird eine IP-Adresse aus dem konfigurierten Subnetz ausgewählt, und der Netzwerkdatenverkehr wird an die IP-Adressen im Back-End-IP-Pool weitergeleitet. Beachten Sie, dass jede Instanz eine eigene IP-Adresse benötigt.

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appGatewaySubnetData
```

### <a name="step-2"></a>Schritt 2

Konfigurieren Sie den Front-End-IP-Port für den öffentlichen IP-Endpunkt. Dieser Port ist der Port, mit dem Endbenutzer eine Verbindung herstellen.

```powershell
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>Schritt 3

Konfigurieren Sie die Front-End-IP-Adresse mit dem öffentlichen IP-Endpunkt.

```powershell
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Schritt 4

Konfigurieren Sie das Zertifikat für den Application Gateway-Dienst, der zum Entschlüsseln und Verschlüsseln des durchlaufenden Datenverkehrs verwendet wird.

> [!NOTE]
> Application Gateway unterstützt das Definieren benutzerdefinierter TLS-Optionen, das Deaktivieren bestimmter TLS-Protokollversionen sowie das Angeben von Verschlüsselungssammlungen und der bevorzugten Reihenfolge. Weitere Informationen zu konfigurierbaren TLS-Optionen finden Sie in der [Übersicht über TLS-Richtlinien](../application-gateway/application-gateway-ssl-policy-overview.md).

```powershell
$certGateway = New-AzApplicationGatewaySslCertificate -Name "gatewaycert" `
  -CertificateFile $gatewayCertPfxPath -Password $certGatewayPwd
$certPortal = New-AzApplicationGatewaySslCertificate -Name "portalcert" `
  -CertificateFile $portalCertPfxPath -Password $certPortalPwd
$certManagement = New-AzApplicationGatewaySslCertificate -Name "managementcert" `
  -CertificateFile $managementCertPfxPath -Password $certManagementPwd
```

### <a name="step-5"></a>Schritt 5

Erstellen Sie die HTTP-Listener für Application Gateway. Weisen Sie ihnen die Front-End-IP-Konfiguration, den Port und die TLS/SSL-Zertifikate zu.

```powershell
$gatewayListener = New-AzApplicationGatewayHttpListener -Name "gatewaylistener" `
  -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 `
  -SslCertificate $certGateway -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzApplicationGatewayHttpListener -Name "portallistener" `
  -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 `
  -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
$managementListener = New-AzApplicationGatewayHttpListener -Name "managementlistener" `
  -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 `
  -SslCertificate $certManagement -HostName $managementHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>Schritt 6

Erstellen Sie benutzerdefinierte Tests für den Gatewaydomänenendpunkt `ContosoApi` des API Management-Diensts. Der Pfad `/status-0123456789abcdef` ist ein Standard-Integritätsendpunkt, der von allen API Management-Diensten gehostet wird. Legen Sie `api.contoso.net` als benutzerdefinierten Testhostnamen fest, den Sie mit dem TLS/SSL-Zertifikat schützen möchten.

> [!NOTE]
> Der Hostname `contosoapi.azure-api.net` ist der Proxy-Standardhostname, der konfiguriert wird, wenn im öffentlichen Azure-Bereich der Dienst `contosoapi` erstellt wird.
>

```powershell
$apimGatewayProbe = New-AzApplicationGatewayProbeConfig -Name "apimgatewayprobe" `
  -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" `
  -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzApplicationGatewayProbeConfig -Name "apimportalprobe" `
  -Protocol "Https" -HostName $portalHostname -Path "/signin" `
  -Interval 60 -Timeout 300 -UnhealthyThreshold 8
$apimManagementProbe = New-AzApplicationGatewayProbeConfig -Name "apimmanagementprobe" `
  -Protocol "Https" -HostName $managementHostname -Path "/ServiceStatus" `
  -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Schritt 7

Laden Sie das vertrauenswürdige Stammzertifikat hoch, das für die HTTP-Einstellungen konfiguriert werden soll.

```powershell
$trustedRootCert = New-AzApplicationGatewayTrustedRootCertificate -Name "whitelistcert1" -CertificateFile $trustedRootCertCerPath
```

### <a name="step-8"></a>Schritt 8

Konfigurieren Sie HTTP-Back-End-Einstellungen für Application Gateway, und schließen Sie ein Timeout für Back-End-Anforderungen ein, nach dem die Anforderungen abgebrochen werden. Dieser Wert unterscheidet sich vom Testtimeout.

```powershell
$apimPoolGatewaySetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolGatewaySetting" `
  -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimGatewayProbe `
  -TrustedRootCertificate $trustedRootCert -PickHostNameFromBackendAddress -RequestTimeout 180
$apimPoolPortalSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" `
  -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe `
  -TrustedRootCertificate $trustedRootCert -PickHostNameFromBackendAddress -RequestTimeout 180
$apimPoolManagementSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolManagementSetting" `
  -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimManagementProbe `
  -TrustedRootCertificate $trustedRootCert -PickHostNameFromBackendAddress -RequestTimeout 180
```

### <a name="step-9"></a>Schritt 9

Konfigurieren Sie einen Back-End-IP-Adresspool für jeden API Management-Endpunkt unter Verwendung des jeweiligen Domänennamens.

```powershell
$apimGatewayBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "gatewaybackend" `
  -BackendFqdns $gatewayHostname
$apimPortalBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "portalbackend" `
  -BackendFqdns $portalHostname
$apimManagementBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "managementbackend" `
  -BackendFqdns $managementHostname
```

### <a name="step-10"></a>Schritt 10

Erstellen Sie Regeln zur Verwendung von grundlegendem Routing für das Anwendungsgateway.

```powershell
$gatewayRule = New-AzApplicationGatewayRequestRoutingRule -Name "gatewayrule" `
  -RuleType Basic -HttpListener $gatewayListener -BackendAddressPool $apimGatewayBackendPool `
  -BackendHttpSettings $apimPoolGatewaySetting
$portalRule = New-AzApplicationGatewayRequestRoutingRule -Name "portalrule" `
  -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimPortalBackendPool `
  -BackendHttpSettings $apimPoolPortalSetting
$managementRule = New-AzApplicationGatewayRequestRoutingRule -Name "managementrule" `
  -RuleType Basic -HttpListener $managementListener -BackendAddressPool $apimManagementBackendPool `
  -BackendHttpSettings $apimPoolManagementSetting
```

> [!TIP]
> Ändern Sie `-RuleType` und das Routing, um den Zugriff auf bestimmte Seiten im Entwicklerportal zu beschränken.

### <a name="step-11"></a>Schritt 11

Konfigurieren Sie die Anzahl der Instanzen und die Größe für das Application Gateway. In diesem Beispiel wird die [WAF_v2 SKU](../web-application-firewall/ag/ag-overview.md) verwendet, um eine höhere Sicherheit für die API Management-Ressource zu erzielen.

```powershell
$sku = New-AzApplicationGatewaySku -Name "WAF_v2" -Tier "WAF_v2" -Capacity 2
```

### <a name="step-12"></a>Schritt 12

Konfigurieren Sie für die WAF den „Präventionsmodus“.

```powershell
$config = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Erstellen eines Application Gateways

Erstellen Sie ein Application Gateway mit allen Konfigurationsobjekten aus den vorherigen Schritten.

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location `
  -BackendAddressPools $apimGatewayBackendPool,$apimPortalBackendPool,$apimManagementBackendPool `
  -BackendHttpSettingsCollection $apimPoolGatewaySetting, $apimPoolPortalSetting, $apimPoolManagementSetting `
  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 `
  -HttpListeners $gatewayListener,$portalListener,$managementListener `
  -RequestRoutingRules $gatewayRule,$portalRule,$managementRule `
  -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $certGateway,$certPortal,$certManagement `
  -TrustedRootCertificate $trustedRootCert -Probes $apimGatewayProbe,$apimPortalProbe,$apimManagementProbe
```

Bestätigen Sie nach Abschluss der Bereitstellung des Anwendungsgateways den Integritätsstatus der API Management-Back-Ends. Verwenden Sie dazu entweder das Portal, oder führen Sie den folgenden Befehl aus:

```powershell
Get-AzApplicationGatewayBackendHealth -Name $appgwName -ResourceGroupName $resGroupName
```

Vergewissern Sie sich, dass die einzelnen Back-End-Pools jeweils fehlerfrei sind. Informationen zur Problembehandlung für fehlerhafte Back-Ends oder für Back-Ends mit unbekanntem Integritätsstatus finden Sie bei Bedarf unter [Behandeln von Problemen mit der Back-End-Integrität in Application Gateway](../application-gateway/application-gateway-backend-health-troubleshooting.md).

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>Erstellen eines CNAME-Eintrags für den API Management-Proxyhostnamen zum Namen des öffentlichen DNS der Application Gateway-Ressource

Konfigurieren Sie nach dem Erstellen des Gateways das Front-End für die Kommunikation. Bei Verwendung einer öffentlichen IP-Adresse ist für Application Gateway ein dynamisch zugewiesener DNS-Name erforderlich, der ggf. nicht benutzerfreundlich ist.

Verwenden Sie den DNS-Namen von Application Gateway, um einen CNAME-Eintrag zu erstellen, durch den der API Management-Gatewayhostname (`api.contoso.net` in den vorherigen Beispielen) auf diesen DNS-Namen verwiesen wird. Rufen Sie zum Konfigurieren des CNAME-Eintrags für die Front-End-IP-Adresse mithilfe des Elements `PublicIPAddress` Details zu Application Gateway sowie die zugeordnete IP-Adresse/den zugeordneten DNS-Namen ab. Die Verwendung von A-Einträgen wird nicht empfohlen, da sich die VIP möglicherweise ändert, wenn das Gateway neu gestartet wird.

```powershell
Get-AzPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

Zu Testzwecken können Sie die Hostdatei auf Ihrem lokalen Computer mit Einträgen aktualisieren, die die öffentliche IP-Adresse von Application Gateway den einzelnen konfigurierten API Management-Endpunkthostnamen zuordnen (Beispiel: `api.contoso.net`, `portal.contoso.net`, `management.contoso.net`).

## <a name="summary"></a>Zusammenfassung

Wenn Azure API Management in einem virtuellen Netzwerk konfiguriert wird, steht eine einzelne Gatewayschnittstelle für alle konfigurierten APIs zur Verfügung – ganz gleich, ob sie lokal oder in der Cloud gehostet werden. Durch die Integration von Application Gateway in API Management verfügen Sie über die Flexibilität, den Zugriff über das Internet für bestimmte APIs selektiv zu aktivieren, und können eine WAF als Front-End für Ihre API Management-Instanz bereitstellen.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Azure Application Gateway
  * [Übersicht über Application Gateway](../application-gateway/overview.md)
  * [Web Application Firewall für Application Gateway](../web-application-firewall/ag/ag-overview.md)
  * [Anwendungsgateways mit pfadbasiertem Routing](../application-gateway/tutorial-url-route-powershell.md)
* Weitere Informationen zu API Management und virtuellen Netzwerken
  * [Herstellen einer Verbindung mit einem internen virtuellen Netzwerk mit Azure API Management](api-management-using-with-internal-vnet.md)
  * [Herstellen einer Verbindung mit einem virtuellen Netzwerk mit Azure API Management](api-management-using-with-vnet.md)
