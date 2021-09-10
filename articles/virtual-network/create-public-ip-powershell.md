---
title: 'Erstellen einer öffentlichen IP-Adresse: PowerShell'
titleSuffix: Azure Virtual Network
description: Erfahren Sie, wie Sie mit Azure PowerShell eine öffentliche IP-Adresse erstellen.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 05/03/2021
ms.author: allensu
ms.openlocfilehash: dc9a6aa398a8cab2927bc8890ceaeb4d0026714c
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/07/2021
ms.locfileid: "113434805"
---
# <a name="create-a-public-ip-address-using-azure-powershell"></a>Erstellen einer öffentlichen IP-Adresse mit Azure PowerShell

In diesem Artikel wird gezeigt, wie Sie mithilfe von Azure PowerShell eine öffentliche IP-Adressressource erstellen. 

Weitere Informationen zu Ressourcen, die öffentliche IP-Adressen unterstützen, finden Sie unter [Öffentliche IP-Adressen](./public-ip-addresses.md).
## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell (lokal installiert) oder Azure Cloud Shell

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens Version 5.4.1 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe
Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie eine Ressourcengruppe mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) namens **myResourceGroup** am Speicherort **eastus2**.

```azurepowershell-interactive
$rg =@{
    Name = 'myResourceGroup'
    Location = 'eastus2'
}
New-AzResourceGroup @rg
```
## <a name="create-standard-sku-public-ip-with-zones"></a>Erstellen einer öffentlichen IP-Adresse der Standard-SKU mit Zonen

In diesem Abschnitt erstellen Sie eine öffentliche IP-Adresse mit Zonen. Öffentliche IP-Adressen können zonenredundant oder zonengebunden sein.

### <a name="zone-redundant"></a>Zonenredundant

>[!NOTE]
>Der folgende Befehl funktioniert für die Az.Network-Modulversion 4.5.0 oder höher.  Weitere Informationen zu den derzeit verwendeten PowerShell-Modulen finden Sie in der [PowerShellGet-Dokumentation](/powershell/module/powershellget/).

Verwenden Sie [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress), um eine zonenredundante öffentliche IPv4-Adresse der SKU „Standard“ namens **myStandardZRPublicIP** in **myResourceGroup** zu erstellen. 

Ändern Sie zum Erstellen einer IPv6-Adresse den Parameter **version** in **IPv6**.

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardZRPublicIP'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    Zone = 1,2,3
}
New-AzPublicIpAddress @ip
```
> [!IMPORTANT]
> Führen Sie für Az.Network-Module, die älter als 4.5.0 sind, den oben genannten Befehl aus, ohne einen Zonenparameter anzugeben, um eine zonenredundante IP-Adresse zu erstellen. 
>

### <a name="zonal"></a>Zonal

Verwenden Sie den folgenden Befehl, um eine zonenbezogene öffentliche IPv4-Adresse der SKU „Standard“ namens **myStandardZonalPublicIP** in Zone 2 in **myResourceGroup** zu erstellen.

Ändern Sie zum Erstellen einer IPv6-Adresse den Parameter **version** in **IPv6**.

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardZonalPublicIP'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    Zone = 2
}
New-AzPublicIpAddress @ip
```
>[!NOTE]
>Die oben genannten Optionen für Zonen sind nur in Regionen mit [Verfügbarkeitszonen](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) gültig.

## <a name="create-standard-public-ip-without-zones"></a>Erstellen einer öffentlichen IP-Adresse der SKU „Standard“ ohne Zonen

In diesem Abschnitt erstellen Sie eine nicht zonengebundene IP-Adresse.  

>[!NOTE]
>Der folgende Befehl funktioniert für die Az.Network-Modulversion 4.5.0 oder höher.  Weitere Informationen zu den derzeit verwendeten PowerShell-Modulen finden Sie in der [PowerShellGet-Dokumentation](/powershell/module/powershellget/).

Verwenden Sie [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress), um eine öffentliche IPv4-Adresse der SKU „Standard“ als nicht zonenbezogene Ressource namens **myStandardPublicIP** in **myResourceGroup** zu erstellen. 

Ändern Sie zum Erstellen einer IPv6-Adresse den Parameter **version** in **IPv6**.

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
}
New-AzPublicIpAddress @ip
```
Das Entfernen des Parameters **zone** im Befehl gilt für alle Regionen.  

Das Entfernen des Parameters **zone** ist die Standardauswahl für öffentliche IP-Adressen der SKU „Standard“ in Regionen ohne [Verfügbarkeitszonen](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

## <a name="create-a-basic-public-ip"></a>Erstellen einer öffentlichen IP-Adresse der SKU „Basic“

In diesem Abschnitt erstellen Sie eine IP-Adresse der SKU „Basic“. Öffentliche IP-Adressen des Typs „Basic“ unterstützen keine Verfügbarkeitszonen.

Verwenden Sie [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress), um eine statische öffentliche IPv4-Adresse der SKU „Basic“ namens **myBasicPublicIP** in **myResourceGroup** zu erstellen.  

Ändern Sie zum Erstellen einer IPv6-Adresse den Parameter **version** in **IPv6**. 

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Basic'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
}
New-AzPublicIpAddress @ip
```
Wenn es akzeptabel ist, dass sich die IP-Adresse im Laufe der Zeit ändert, kann als IP-Zuweisung **Dynamic** ausgewählt werden, indem „AllocationMethod“ in **Dynamic** geändert wird. 

>[!NOTE]
> Eine IPv6-Adresse der SKU „Basic“ muss immer dynamisch sein.

## <a name="routing-preference-and-tier"></a>Routingpräferenz und -ebene

Statische öffentliche IPv4-Adressen der Standard-SKU unterstützen die Routingpräferenz oder das Feature „Globale Ebene“.

### <a name="routing-preference"></a>Routingpräferenz

Standardmäßig ist die Routingpräferenz für öffentliche IP-Adressen auf „Microsoft-Netzwerk“ festgelegt. Damit wird der Datenverkehr über das globale Microsoft-WAN an den Benutzer übertragen.  

Durch Auswahl von **Internet** wird der Datenverkehr im Microsoft-Netzwerk minimiert, anstatt den Datenverkehr über das Transit-ISP-Netzwerk zu optimierten Kosten zu übertragen.  

Weitere Informationen zu den Routingpräferenzen finden Sie unter [Was ist Routingpräferenz (Vorschau)?](./routing-preference-overview.md)

Der Befehl erstellt eine neue zonenredundante öffentliche IPv4-Adresse der SKU „Standard“ mit einer Routingpräferenz vom Typ **Internet**:

```azurepowershell-interactive
## Create IP tag for Internet and Routing Preference. ##
$tag = ${
    IpTagType = 'RoutingPreference'
    Tag = 'Internet'   
}
$ipTag = New-AzPublicIpTag @tag

## Create IP. ##
$ip = @{
    Name = 'myStandardPublicIP'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    IpTag = $ipTag
    Zone = 1,2,3   
}
New-AzPublicIpAddress @ip
```
### <a name="tier"></a>Tarif

Öffentliche IP-Adressen sind einer einzelnen Region zugeordnet. Bei der Ebene **Global** erstreckt sich eine IP-Adresse über mehrere Regionen. Die Ebene **Global** ist für die Front-Ends des regionsübergreifenden Lastenausgleichs erforderlich.  

Weitere Informationen finden Sie unter [Regionsübergreifender Lastenausgleich](../load-balancer/cross-region-overview.md).

Mit dem folgenden Befehl wird eine globale IPv4-Adresse erstellt. Diese Adresse kann dem Front-End eines regionsübergreifenden Lastenausgleichs zugeordnet werden.

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP-Global'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    Tier = 'Global'
}
New-AzPublicIpAddress @ip
```
>[!NOTE]
>Adressen der globalen Ebene unterstützen keine Verfügbarkeitszonen.

## <a name="additional-information"></a>Zusätzliche Informationen 

Weitere Informationen zu den einzelnen Parametern, die in dieser Anleitung aufgeführt sind, finden Sie unter [Verwalten öffentlicher IP-Adressen](./virtual-network-public-ip-address.md#create-a-public-ip-address).

## <a name="next-steps"></a>Nächste Schritte
- Zuordnen einer [öffentlichen IP-Adresse zu einem virtuellen Computer](./associate-public-ip-address-vm.md#azure-portal)
- Lesen Sie mehr über [öffentliche IP-Adressen](./public-ip-addresses.md#public-ip-addresses) in Azure.
- Erfahren Sie mehr über alle [Einstellungen für öffentliche IP-Adressen](virtual-network-public-ip-address.md#create-a-public-ip-address).
