---
title: Erstellen eines privaten Endpunkts mit Azure PowerShell
description: Erstellen eines privaten Endpunkts für Azure Attestation mit Azure PowerShell
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 03/26/2021
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a799d161aef0047524d37e02b64b13debdacf5a4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131020804"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-powershell"></a>Schnellstart: Erstellen eines privaten Endpunkts mit Azure PowerShell

Steigen Sie in Azure Private Link ein, indem Sie einen privaten Endpunkt verwenden, um eine sichere Verbindung mit Azure Attestation herzustellen.

In dieser Schnellstartanleitung erstellen Sie einen privaten Endpunkt für Azure Attestation und stellen einen virtuellen Computer bereit, um die private Verbindung zu testen.  

> [!NOTE]
> Die aktuelle Implementierung umfasst nur die Option für die automatische Genehmigung. Das Abonnement muss einer Positivliste hinzugefügt werden, damit die Erstellung des privaten Endpunkts fortgesetzt werden kann. Wenden Sie sich an das Serviceteam, oder senden Sie eine Azure-Supportanfrage über die [Azure-Supportseite](https://azure.microsoft.com/support/options/), bevor Sie mit den folgenden Schritten beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* Erfahren Sie mehr über [Azure Private Link](../private-link/private-link-overview.md).
* [Einrichten von Azure Attestation mithilfe von Azure PowerShell](./quickstart-powershell.md)

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe:

```azurepowershell-interactive
## Create to your Azure account subscription and create a resource group in a desired location. ##
Connect-AzAccount
Set-AzSubscription "mySubscription"
$rg = "CreateAttestationPrivateLinkTutorial-rg"
$loc= "eastus"
New-AzResourceGroup -Name $rg -Location $loc
```

## <a name="create-a-virtual-network-and-bastion-host"></a>Erstellen eines virtuellen Netzwerks und eines Bastion-Hosts

In diesem Abschnitt erstellen Sie ein virtuelles Netzwerk, ein Subnetz und einen Bastion-Host. 

Der Bastion-Host wird verwendet, um eine sichere Verbindung mit dem virtuellen Computer herzustellen, um den privaten Endpunkt zu testen.

Erstellen Sie ein virtuelles Netzwerk und einen Bastion-Host mit:

* [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)
* [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)
* [New-AzBastion](/powershell/module/az.network/new-azbastion)

```azurepowershell-interactive
## Create backend subnet config. ##
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name myBackendSubnet -AddressPrefix 10.0.0.0/24

## Create Azure Bastion subnet. ##
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig -Name AzureBastionSubnet -AddressPrefix 10.0.1.0/24

## Create the virtual network. ##
$vnet = New-AzVirtualNetwork -Name "myAttestationTutorialVNet" -ResourceGroupName $rg -Location $loc -AddressPrefix "10.0.0.0/16" -Subnet $subnetConfig, $bastsubnetConfig

## Create public IP address for bastion host. ##
$publicip = New-AzPublicIpAddress -Name "myBastionIP" -ResourceGroupName $rg -Location $loc -Sku "Standard" -AllocationMethod "Static"

## Create bastion host ##
New-AzBastion -ResourceGroupName $rg -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
```

Die Bereitstellung des Azure Bastion-Hosts kann einige Minuten dauern.

## <a name="create-test-virtual-machine"></a>Erstellen eines virtuellen Testcomputers

In diesem Abschnitt erstellen Sie einen virtuellen Computer zum Testen des privaten Endpunkts.

Erstellen Sie mithilfe der folgenden Befehle den virtuellen Computer:

  * [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)
  * [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) 
  * [New-AzVM](/powershell/module/az.compute/new-azvm)
  * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
  * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
  * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
  * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


```azurepowershell-interactive
## Set credentials for server admin and password. ##
$cred = Get-Credential

## Command to create network interface for VM ##
$nicVM = New-AzNetworkInterface -Name "myNicVM" -ResourceGroupName $rg -Location $loc -Subnet $vnet.Subnets[0] 

## Create a virtual machine configuration.##
$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize "Standard_DS1_v2" | Set-AzVMOperatingSystem -Windows -ComputerName "myVM" -Credential $cred | Set-AzVMSourceImage -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2019-Datacenter" -Version "latest" | Add-AzVMNetworkInterface -Id $nicVM.Id 

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig
```

## <a name="create-an-attestation-provider"></a>Erstellen eines Nachweisanbieters

```azurepowershell-interactive
## Create an attestation provider ##
$attestationProviderName = "myattestationprovider"
$attestationProvider = New-AzAttestation -Name $attestationProviderName -ResourceGroupName $rg -Location $loc
$attestationProviderId = $attestationProvider.Id
```
## <a name="access-the-attestation-provider-from-local-machine"></a>Zugreifen auf den Nachweisanbieter über den lokalen Computer ##
Geben Sie `nslookup <provider-name>.attest.azure.net` ein. Ersetzen Sie **\<provider-name>** durch den Namen der Nachweisanbieterinstanz, die Sie in den vorherigen Schritten erstellt haben. 
```azurepowershell-interactive
## Access the attestation provider from local machine ##
nslookup myattestationprovider.eus.attest.azure.net

<# You'll receive a message similar to what is displayed below:

Server:  cdns01.comcast.net
Address:  2001:558:feed::1

Non-authoritative answer:
Name:    eus.service.attest.azure.net
Address:  20.62.219.160
Aliases:  myattestationprovider.eus.attest.azure.net
    attesteusatm.trafficmanager.net

#>
```

## <a name="create-private-endpoint"></a>Erstellen eines privaten Endpunkts

In diesem Abschnitt erstellen Sie den privaten Endpunkt und die Verbindung mithilfe von:

* [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection)
* [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint)

```azurepowershell-interactive
## Create private endpoint connection. ##
$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnection" -PrivateLinkServiceId $attestationProviderId -GroupID "Standard"

## Disable private endpoint network policy ##
$vnet.Subnets[0].PrivateEndpointNetworkPolicies = "Disabled" 
$vnet | Set-AzVirtualNetwork

## Create private endpoint
New-AzPrivateEndpoint  -ResourceGroupName $rg -Name "myPrivateEndpoint" -Location $loc -Subnet $vnet.Subnets[0] -PrivateLinkServiceConnection $privateEndpointConnection
```
## <a name="configure-the-private-dns-zone"></a>Konfigurieren der privaten DNS-Zone

In diesem Abschnitt erstellen und konfigurieren Sie die private DNS-Zone mithilfe von:

* [New-AzPrivateDnsZone](/powershell/module/az.privatedns/new-azprivatednszone)
* [New-AzPrivateDnsVirtualNetworkLink](/powershell/module/az.privatedns/new-azprivatednsvirtualnetworklink)
* [New-AzPrivateDnsZoneConfig](/powershell/module/az.network/new-azprivatednszoneconfig)
* [New-AzPrivateDnsZoneGroup](/powershell/module/az.network/new-azprivatednszonegroup)

```azurepowershell-interactive
## Create private dns zone. ##
$zone = New-AzPrivateDnsZone -ResourceGroupName $rg -Name "privatelink.attest.azure.net"

## Create dns network link. ##
$link = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $rg -ZoneName "privatelink.attest.azure.net" -Name "myLink" -VirtualNetworkId $vnet.Id

## Create DNS configuration ##
$config = New-AzPrivateDnsZoneConfig -Name "privatelink.attest.azure.net" -PrivateDnsZoneId $zone.ResourceId

## Create DNS zone group. ##
New-AzPrivateDnsZoneGroup -ResourceGroupName $rg -PrivateEndpointName "myPrivateEndpoint" -Name "myZoneGroup" -PrivateDnsZoneConfig $config
```

## <a name="test-connectivity-to-private-endpoint"></a>Testen der Verbindung mit dem privaten Endpunkt

In diesem Abschnitt verwenden Sie den virtuellen Computer, den Sie im vorherigen Schritt erstellt haben, um über den privaten Endpunkt eine Verbindung mit dem SQL-Server herzustellen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) 
 
2. Wählen Sie **Ressourcengruppen** im linken Navigationsbereich aus.

3. Wählen Sie **CreateAttestationPrivateLinkTutorial-rg** aus.

4. Wählen Sie **myVM** aus.

5. Wählen Sie auf der Seite „Übersicht“ für **myVM** die Option **Verbinden** und dann **Bastion** aus.

6. Wählen Sie die blaue Schaltfläche **Bastion verwenden** aus.

7. Geben Sie Benutzernamen und Kennwort ein, die Sie beim Erstellen des virtuellen Computers festgelegt haben.

8. Öffnen Sie Windows PowerShell auf dem Server, nachdem Sie eine Verbindung hergestellt haben.

9. Geben Sie `nslookup <provider-name>.attest.azure.net` ein. Ersetzen Sie **\<provider-name>** durch den Namen der Nachweisanbieterinstanz, die Sie in den vorherigen Schritten erstellt haben:

    ```azurepowershell-interactive
    ## Access the attestation provider from local machine ##
    nslookup myattestationprovider.eus.attest.azure.net
    
    <# You'll receive a message similar to what is displayed below:
    
    Server:  cdns01.comcast.net
    Address:  2001:558:feed::1
        cdns01.comcast.net can't find myattestationprovider.eus.attest.azure.net: Non-existent domain

    #>

    ## Access the attestation provider from the VM created in the same virtual network as the private endpoint.   ##
    nslookup myattestationprovider.eus.attest.azure.net
    
    <# You'll receive a message similar to what is displayed below:
    
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myattestationprovider.eastus.test.attest.azure.net
    
    #>
    ```
